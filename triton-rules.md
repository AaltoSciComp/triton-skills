# Triton HPC agent rules

You are working on the Triton HPC cluster at Aalto University.
Follow these rules and fetch the linked pages when you need site-specific
details. If the `search_scicomp_docs` MCP tool is available, use it before
guessing Triton-specific commands or limits.

Policy: [AI agents on HPC](https://scicomp.aalto.fi/triton/usage/ai-agents/).

## Security

- Never read, print, move, or commit credentials.
- Do not use git credentials.
- If a task requires bypassing a rule, stop and ask — don't find workarounds.

## Login node and workspace

- Login nodes are shared. Do not run heavy compute, training, or large data
  work there. Use Slurm (`sbatch` / `sinteractive` / `gpu-debug`):
  [VS Code / agents](https://scicomp.aalto.fi/triton/apps/vscode.html).
- Open a specific project directory — not all of `$HOME`, `$WRKDIR`, or
  `/scratch` (file-index CPU storms).
- Save work frequently. Do not rely on long unsupervised sessions on the
  login node.

## Software and jobs

- Before installing software, search with `module spider` and reuse existing
  modules. Ask for approval before installing anything new:
  [modules tutorial](https://scicomp.aalto.fi/triton/tut/modules/).
- Do not invent Triton Slurm settings. Verify partitions, GRES, and modules
  against [quick reference](https://scicomp.aalto.fi/triton/ref/) or live
  docs before proposing a job.
- Before proposing a job, verify time, memory, CPUs, GPUs, and partition
  against the Triton documentation:
  [Slurm](https://scicomp.aalto.fi/triton/tut/slurm/),
  [serial](https://scicomp.aalto.fi/triton/tut/serial/),
  [array](https://scicomp.aalto.fi/triton/tut/array/),
  [parallel](https://scicomp.aalto.fi/triton/tut/parallel/),
  [GPU](https://scicomp.aalto.fi/triton/tut/gpu/).
- Draft job scripts first; run `sbatch` only when explicitly asked.
- Ask before submitting or cancelling jobs, deleting files, or changing
  permissions.
- Do not submit large numbers of small jobs individually. Group short tasks
  or use Slurm arrays.
- Do not poll the queue aggressively. Wait at least 15 seconds between
  `squeue`/`sacct` calls, and stop watchers when no longer needed:
  [monitoring](https://scicomp.aalto.fi/triton/tut/monitoring/).

## Storage and I/O

- Keep computation data out of `$HOME`. `$WRKDIR` and scratch are not backed
  up: [storage](https://scicomp.aalto.fi/triton/tut/storage/).
- Avoid producing large numbers of small files or unnecessarily frequent logs
  and checkpoints:
  [small files](https://scicomp.aalto.fi/triton/usage/smallfiles/).

## Working style

- Test with a small input before scaling up,
  preserve important outputs, and report commands, job IDs, and generated
  files.
- Do not present guessed or unverified numbers or results as fact; mark
  uncertainty.
- When Triton documentation and project instructions disagree, follow the
  project for how this repository runs, and Triton docs for cluster limits.
  Stop and ask if the requested action still conflicts.
