---
name: triton-job-monitoring-skill
description: >
  Interprets Triton Slurm job status and efficiency (slurm q, seff, GPU util)
  and suggests resource retunes. Use when jobs are Pending/Failed, the user
  asks about queue status, seff, wasted memory/time/GPUs, or how to right-size
  a Triton job. Status/seff are read-only; scancel and resubmits need explicit
  confirmation / request.
---

# Triton job monitoring

Observe and right-size jobs. Docs:
[Monitoring](https://scicomp.aalto.fi/triton/tut/monitoring/),
[quick reference](https://scicomp.aalto.fi/triton/ref/).

## Operating rules (read first)

1. **Safety tiers**
   - **Read-only** — `slurm q` / `qq` / `history` / `j`, `seff`, `sacct` on
     request. A few commands — **no** `watch` / tight poll loops unless the
     user asks to watch interactively.
   - **Create** — propose `#SBATCH` retunes and a short test script; submit
     follow-ups **only** if explicitly asked.
   - **Avoid-zone** — `scancel` (especially others’ / shared jobs), mass
     cancels. Show the exact `scancel` line; run only after confirmation.

2. Prefer Triton’s `slurm` helper over raw `squeue` when available.
3. After large retunes, suggest a short test job before a long production run.

## Quick start: common requests

- **"Is my job running / why pending?"** `slurm q` — read STATE and REASON.
  States: `references/concepts.md`.
- **"Was this efficient?"** `seff JOBID` (GPU: `module load seff-gpu` then
  `seff`, or `sacct` TRES). Playbook: `references/concepts.md`.
- **"Cancel this job."** Show `scancel JOBID`; wait for confirmation.
- **"Fix my resources."** Propose concrete `#SBATCH` diffs from `seff`
  numbers; do not `sbatch` unless asked.

## Reference material

- `references/concepts.md` — states, efficiency playbook, mem gotchas.
- `references/code-patterns.md` — command recipes.

Related: `triton-sbatch-drafting-skill`, `triton-agent-hygiene-skill`. Help:
[SciComp garage](https://scicomp.aalto.fi/help/garage/).
