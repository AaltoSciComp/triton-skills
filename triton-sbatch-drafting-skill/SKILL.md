---
name: triton-sbatch-drafting-skill
description: >
  Draft Slurm sbatch scripts for Aalto Triton HPC correctly and safely. Invoke
  when the user wants a batch job, sbatch/srun script, GPU or array job,
  interactive debug job, or help choosing time/mem/CPUs/GPUs/GRES (min-vram,
  min-cuda-cc), modules, or conda/mamba envs on Triton. Generates reviewable
  job scripts; submits with sbatch only when explicitly asked; does NOT cancel
  jobs or delete data on the user's behalf.
---

# Triton sbatch drafting

Draft batch scripts for [Triton](https://scicomp.aalto.fi/triton/). Prefer the
[Slurm quick reference](https://scicomp.aalto.fi/triton/ref/slurm.html) and
live docs / Docs MCP over generic Slurm lore or memorized GPU inventories.

## Operating rules (read first)

1. **Safety tiers**
   - **Read-only** — fine on request: `slurm q` / `slurm qq`, `seff`,
     `scontrol show job`, `module spider` (no `watch` / tight poll loops).
   - **Create** — writing a script is always OK. Running `sbatch` /
     `sinteractive` only when the user explicitly asks to submit or get a
     shell; show the script (or equivalent flags) first.
   - **Avoid-zone** — `scancel`, `rm`/mass cleanup on `$WRKDIR` or project
     scratch, rewriting shared trees. Write a reviewable command list; run
     only after clear confirmation. Scratch is **not** backed up.

   "Write me a job script" means write the script — **not** submit it.

2. **Never run the batch work on a login node.** Deliver `#SBATCH` scripts
   for `sbatch`, or `sinteractive` / `gpu-debug` for short interactive tests.
   Prefer agents on **`code.triton.aalto.fi`**. Policy:
   [AI Agents on HPC](https://scicomp.aalto.fi/triton/usage/ai-agents/).

3. **Do not invent Triton Slurm.** Verify partitions, GRES, GPU names, and
   modules against live docs (`slurm features`,
   [GPU ref](https://scicomp.aalto.fi/triton/ref/gpu.html)) when unsure.
   Omit `--partition` unless required — Triton usually auto-detects.

4. **Anti-priors (common model mistakes)**
   - Submit with **`sbatch script.sh`**, not `bash script.sh` (`#SBATCH`
     ignored → work may hit the login node).
   - Own envs: `module load mamba` then **`source activate ENV`** — not
     `conda activate`.
   - GPU jobs need **`--gpus=…`**; CPU-only work on GPU nodes is forbidden.
   - One `--gres=…` only — combine with commas:
     `--gres=min-vram:40g,min-cuda-cc:80`.
   - Create `logs/` **before** `sbatch` if using `--output`/`--error` under
     it (Slurm opens those paths before the script body runs).

5. **Right-size.** Conservative first script; refine with `seff` after a
   test. Prefer one `--array` (with `%N` throttle) over floods of tiny jobs.

## Quick start: common requests

- **"Write me a job script."** Classify parallelism (serial / array /
  shared-memory / MPI / GPU). Ask only for unknowns that change the script
  (walltime, mem, GPU/VRAM, software env, data paths). Adapt a template from
  `references/code-patterns.md`.
- **"GPU / PyTorch job."** `--gpus=1` (or type); prefer capability GRES over
  pinning a name; `gpu-debug` + `--gpus=1` for ≤30 min tests. Newer-GPU
  PyTorch: confirm `scicomp-pytorch-env/…` with `module spider` and pair with
  `min-cuda-cc` as docs require. Grace-H200 is **ARM** — x86 binaries will
  not run. Details: `references/concepts.md`.
- **"Many parameter combinations."** One `--array` + `$SLURM_ARRAY_TASK_ID`,
  cap concurrency with `%N`; avoid thousands of tiny Lustre-thrashing tasks.
- **"Interactive / debug now."** `sinteractive` or short `sbatch` on
  `gpu-debug` — not the login node.
- **"Use my conda env."** Activation **inside** the script after `#SBATCH`;
  do not recreate or silently mutate the env unless asked. Data under
  `$WRKDIR` or project scratch — not `$HOME`.

## Output format

1. Brief rationale (job type + key resources)
2. Full script
3. Submit / check lines: `sbatch …`, `slurm q` (after a run: `seff JOBID`)
4. One open question if a critical resource was guessed

## Reference material

Load when you need detail:

- `references/concepts.md` — `#SBATCH` / GRES cheat sheet, GPU request
  patterns, software/env choices, parallelism checks, doc links.
- `references/code-patterns.md` — ready-to-adapt scripts (serial, conda,
  OpenMP, GPU, array, CUDA module).

When advising on mechanics, prefer these notes and live docs over memory; if
silent on a detail, say so or look it up rather than inventing Triton
behaviour. Related skills: `triton-agent-hygiene-skill`,
`triton-job-monitoring-skill`, `triton-modules-envs-skill`,
`triton-storage-io-skill`, `triton-containers-skill`. Help:
[SciComp garage](https://scicomp.aalto.fi/help/garage/).
