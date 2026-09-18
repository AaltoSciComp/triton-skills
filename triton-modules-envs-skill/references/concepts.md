# Triton modules & envs — concepts

Source of truth: [modules](https://scicomp.aalto.fi/triton/tut/modules/),
[Python](https://scicomp.aalto.fi/triton/apps/python/),
[PyTorch](https://scicomp.aalto.fi/triton/apps/pytorch/),
[conda/mamba](https://scicomp.aalto.fi/triton/apps/conda/),
[software ref](https://scicomp.aalto.fi/triton/ref/software.html).
Exact module versions change — verify with `module spider` before pinning.

## Lmod workflow

1. Ask language/framework and (for ML) GPU generation if known.
2. `module spider PATTERN` — read prerequisites (often a `triton/…` stack).
3. Prefer central `scicomp-*-env` over a new conda env when enough.
4. Load **inside** the job script or interactive allocation, not only on the
   login shell you used to draft the script.
5. Pin versions in scripts for reproducibility when spider shows multiple.

## Common loads (examples — confirm live)

| Need | Load (verify with spider) |
| --- | --- |
| Scientific Python | `scicomp-python-env` |
| PyTorch on newer GPUs (e.g. B300) | `scicomp-pytorch-env/2026.1` |
| R | `r` or `scicomp-r-env` |
| Matlab | `matlab` (pin version if needed) |
| Own conda/mamba | `mamba` then `source activate ENV` |
| CUDA toolkit | stack + `cuda/…` per spider |

- `scicomp-python-env` PyTorch → older GPUs (V100-era).
- Newest GPUs → dedicated PyTorch module + matching CUDA CC GRES in the job
  (see sbatch skill / [GPU ref](https://scicomp.aalto.fi/triton/ref/gpu.html)).
- Activate with **`source activate` / `source deactivate`** (not
  `conda activate`) on Triton.

## Own environments

- First-time: redirect pkgs/envs to `$WRKDIR` —
  [conda first-time setup](https://scicomp.aalto.fi/triton/apps/conda/#conda-first-time-setup).
- Prefer `environment.yml` + `mamba env create --file …`.
- Create/update only with user approval.
- CUDA builds may need `CONDA_OVERRIDE_CUDA=…`; do heavy work in a job, not on
  the login node.
- `mamba clean` reclaims caches (does not delete envs).

## Anti-patterns

- Unplanned `pip install` into global/user paths
- Installing “latest” in an unsupervised agent session
- Assuming laptop conda ≡ Triton without modules
- Heavy CUDA compile/test on the **login** node
- Putting envs/caches in `$HOME` (quota)
