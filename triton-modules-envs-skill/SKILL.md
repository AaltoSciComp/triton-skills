---
name: triton-modules-envs-skill
description: >
  Helps choose and load Triton software (Lmod modules, scicomp Python/PyTorch
  envs, mamba/conda). Use when the user needs Python/R/Matlab/CUDA on Triton,
  module load/spider, conda environments, or "package not found" on the
  cluster. module spider is read-only; creating/updating envs needs approval;
  bulk env rebuilds are avoid-zone.
---

# Triton modules & environments

Prefer modules over ad-hoc installs. Live docs:
[modules](https://scicomp.aalto.fi/triton/tut/modules/),
[Python](https://scicomp.aalto.fi/triton/apps/python/),
[PyTorch](https://scicomp.aalto.fi/triton/apps/pytorch/),
[conda/mamba](https://scicomp.aalto.fi/triton/apps/conda/).
Module versions drift — `module spider` / Docs MCP over memory.

## Operating rules (read first)

1. **Safety tiers**
   - **Read-only** — `module spider` / `avail` / `list` / `show` on request.
   - **Create** — propose `module load` lines and `environment.yml`; create or
     update conda/mamba envs only with user approval; show the plan first.
   - **Avoid-zone** — silent `pip`/`conda`/`mamba` into shared or global paths,
     bulk env rebuilds, wiping conda config. Write a reviewable command list;
     run only after confirmation.

2. **Anti-priors**
   - Own envs: `module load mamba` then **`source activate ENV`** — not
     `conda activate`.
   - Put `module load …` **inside** the sbatch script / interactive session.
   - Keep conda caches/envs off tiny `$HOME` (mamba first-time setup →
     `$WRKDIR`).
   - Prefer central `scicomp-*-env` when it covers the need.

3. **No heavy CUDA builds/tests on the login node** — use Slurm
   (`sinteractive` / short `sbatch`). Agents: **`code.triton.aalto.fi`**.

## Quick start: common requests

- **"What module for X?"** `module spider PATTERN` before inventing names.
  Common loads: `references/concepts.md`.
- **"Python / PyTorch on GPU."** Older GPUs (V100-era): `scicomp-python-env`.
  Newer GPUs (e.g. B300): `scicomp-pytorch-env/2026.1` + GRES
  `min-cuda-cc:80` — confirm versions with spider / [PyTorch app page](https://scicomp.aalto.fi/triton/apps/pytorch/).
- **"Use / create my conda env."** Activation lines and first-time setup:
  `references/code-patterns.md`. Do not recreate silently.
- **"Package not found."** Spider → central env → documented app page → only
  then a user env with approval.

## Reference material

- `references/concepts.md` — Lmod workflow, common loads, anti-patterns.
- `references/code-patterns.md` — activate lines, mamba first-time, env create.

Related: `triton-sbatch-drafting-skill`, `triton-storage-io-skill`,
`triton-agent-hygiene-skill`. Help:
[SciComp garage](https://scicomp.aalto.fi/help/garage/).
