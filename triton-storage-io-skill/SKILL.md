---
name: triton-storage-io-skill
description: >
  Guides Triton data placement and I/O (HOME vs WRKDIR vs project scratch,
  quotas, dust, local /tmp, Lustre-friendly patterns). Use when choosing paths,
  transferring data, hitting quota, designing array I/O, or cleaning disk on
  Aalto Triton. Read-only quota/listing OK; deletes and mass moves are
  avoid-zone — show commands and wait for confirmation.
---

# Triton storage & I/O

Prefer live docs over memorized quotas:
[storage](https://scicomp.aalto.fi/triton/tut/storage/),
[quotas](https://scicomp.aalto.fi/triton/usage/quotas/),
[local storage](https://scicomp.aalto.fi/triton/usage/localstorage/),
[remote data](https://scicomp.aalto.fi/triton/tut/remotedata/),
[small files](https://scicomp.aalto.fi/triton/usage/smallfiles/).

## Operating rules (read first)

1. **Safety tiers**
   - **Read-only** — `quota`, `dust`, `ls`/`du` on request (no tight loops).
   - **Create** — propose path layout, staging/`rsync` recipes, `--tmp` usage;
     show commands before running transfers.
   - **Avoid-zone** — `rm -r`, mass moves, rewriting shared project trees,
     multi-TB syncs. List exact targets; run only after explicit confirmation.
     Scratch / `$WRKDIR` / project scratch are **not** backed up.

2. **Default placement.** Compute data under `$WRKDIR` or
   `/scratch/DEPT/PROJECT/` — not `$HOME` (≈10GB, configs only).

3. **No job I/O on `$HOME`.** Arrays and many tiny files thrash Lustre — fewer,
   larger outputs; stage heavy small-file work on node-local `/tmp`.

## Quick start: common requests

- **"Where should I put this?"** Classify → path/var → backup yes/no. Details:
  `references/concepts.md`.
- **"I'm over quota / disk full."** `quota`; then `dust` on the hot tree.
  Prefer [project storage](https://scicomp.aalto.fi/data/requesting/) over
  growing personal quota. Cleaning: list targets, wait for confirmation.
- **"Array jobs / many workers."** Avoid N workers hammering one conda env or
  writing tiny files to Lustre; recipes in `references/code-patterns.md`.
- **"Faster I/O / unpacking."** `--tmp=nnnG` + `/tmp/$SLURM_JOB_ID/`; copy
  results back before the job ends.
- **"Copy data to/from Triton."** `rsync`/`sftp` to `triton.aalto.fi` (SMB:
  `data.triton.aalto.fi`); no multi-TB syncs without approval.

## Advice format

Name directory class → literal path/var → backup yes/no → if cleaning, list
targets and wait for confirmation.

## Reference material

- `references/concepts.md` — storage map, quotas, Lustre / small-file rules,
  local `/tmp` and ramfs.
- `references/code-patterns.md` — `quota`/`dust`, staging, `--tmp`, transfers.

Related: `triton-modules-envs-skill`, `triton-sbatch-drafting-skill`,
`triton-agent-hygiene-skill`. Help:
[SciComp garage](https://scicomp.aalto.fi/help/garage/).
