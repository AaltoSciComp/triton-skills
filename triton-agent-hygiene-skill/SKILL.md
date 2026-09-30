---
name: triton-agent-hygiene-skill
description: >
  Enforces responsible AI-agent use on Aalto Triton HPC (code.triton login,
  login-node limits, job storms, secrets/LLM exfil, scratch safety). Use when
  working on Triton via an agent, SSH/Remote-SSH, submitting many jobs,
  installing packages, deleting files, or when the user mentions AI agents on
  HPC / cluster etiquette. Does NOT cancel jobs or delete data without explicit
  confirmation.
---

# Triton AI agent hygiene

Policy: [AI Agents on HPC](https://scicomp.aalto.fi/triton/usage/ai-agents/).
Also: [VS Code on Triton](https://scicomp.aalto.fi/triton/apps/vscode.html).
Ask before destructive or cluster-wide actions.

## Operating rules (read first)

1. **Safety tiers**
   - **Read-only** — `slurm q` / `seff` / `module spider` / `quota` on request
     (no tight poll loops / unattended `watch`).
   - **Create** — draft scripts and commands; `sbatch` / installs only when
     explicitly asked; show the artifact first.
   - **Avoid-zone** — `scancel` of others’/shared jobs, `rm -r` on scratch,
     mass moves, rewriting shared trees, bulk env rebuilds. Write a reviewable
     list; run only after clear confirmation. Scratch is **not** backed up.

2. **Connect coding tools to `code.triton.aalto.fi`**, not `triton.aalto.fi`
   (VS Code Remote-SSH, Claude Code, Codex, PyCharm, …). Processes on the
   general login host may be killed.

3. **Login nodes are shared.** No heavy compute, training, or large data work.
   Use Slurm (`sbatch` / `sinteractive` / `gpu-debug`).

4. **Do not storm the scheduler.** Prefer one `--array` (with `%N`) over floods
   of tiny `sbatch` calls. Do not poll `squeue`/`sacct`/`slurm q` in tight loops.

5. **Do not invent Triton Slurm** — verify partitions, GRES, modules against
   [quick reference](https://scicomp.aalto.fi/triton/ref/) / live docs / Docs MCP.

6. **Secrets & sensitive data.** Code and files the agent reads may be sent to
   an external LLM. No API keys, passwords, personal/GDPR, or confidential
   research data in agent context; keep credentials out of opened trees.

7. **IDE hygiene.** Open a specific project directory — not all of `$HOME`,
   `$WRKDIR`, or `/scratch` (file-index CPU storms).

8. **Install deliberately.** Prefer modules / existing envs. No unsupervised
   pip/npm/conda from the open internet.

9. **Git / credentials.** Propose git commands; do not run ones that need
   interactive credentials or secrets unless explicitly asked and safe.

10. **Persistence.** Prefer one focused agent session; write durable files
    often (admins may kill disruptive login-node processes).

11. **Outputs.** Do not present guessed or unverified numbers/results as fact;
    mark uncertainty. Treat drafted commands as requiring confirmation before
    avoid-zone execution.

## Quick start: common requests

| Situation | Do |
| --- | --- |
| Check jobs | `slurm q` / `slurm qq` once |
| Submit | Show script; submit only if asked |
| Many params | Design `--array`, not a submit loop |
| GPU debug | `sinteractive` / `gpu-debug` / short `sbatch` |
| Install | `module spider` / documented envs; list pkgs; wait for confirmation |
| Delete / clean | Explain paths; require confirmation |
| Cancel job | Show `scancel …`; require confirmation |

Workflow variants (local agent vs remote-on-login): `references/concepts.md`.

## Reference material

- `references/concepts.md` — where the agent runs, policy checklist.
  (No `code-patterns.md` — this skill is operating rules.)
