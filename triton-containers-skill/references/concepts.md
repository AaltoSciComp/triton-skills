# Triton containers — concepts

Source of truth: [Singularity/Apptainer](https://scicomp.aalto.fi/triton/usage/singularity/),
[NVIDIA containers](https://scicomp.aalto.fi/triton/apps/nvidiacontainers/),
[Grace Hopper](https://scicomp.aalto.fi/triton/usage/gracehopper/).
Image tags, partition names, and GPU-hour caps drift — prefer live pages.

## Runtime

- Apptainer ≈ Singularity; `singularity` is an alias on Triton.
- No Docker daemon; `docker://…` pulls/converts via Apptainer.
- Writes through bind mounts are real filesystem writes.

## Binds

| Path | Default |
| --- | --- |
| `$HOME` | Bound automatically |
| `/scratch`, `/m`, `/l` | **Not** automatic — add `--bind` |
| `$PWD` | Bind if the job cwd must appear inside |

Missing binds look like “file not found” inside the container.

## Prebuilt image modules

- Newer style: `module load apptainer-NAME/VERSION` sets `$IMAGE_PATH`.
- `family("apptainer")` → only one image module at a time.
- Older style: `apptainer-wrapper` / `apptainer_wrapper` with auto binds —
  check `module show`.
- List candidates: `module --terse avail 2>&1 | grep apptainer` (or spider).

## Cache & Lustre

- Default cache `~/.apptainer` fills `$HOME` quota fast.
- `export APPTAINER_CACHEDIR=$WRKDIR/apptainer_cache` before build/pull.
- Prefer a single `.sif` over `--sandbox`.
- Reclaim: `apptainer cache clean`; check `quota`. Confirm before large deletes.

## GPU & NVIDIA images

- Slurm `--gpus` + Apptainer `--nv`.
- Documented `nvidia-pytorch` / `nvidia-tensorflow` modules are gone — use
  `docker://nvcr.io/nvidia/...` (or equivalent) directly; confirm tags on NGC /
  live docs.

## ARM / Grace-Hopper

- Partition example (verify live): `--partition=gpu-grace-h200-141g`
  (`gpuarm[1-2]`).
- Intended for testing/dev; GPU-hour caps apply — see Grace Hopper page.
- x86 images do not run; build on an ARM allocation from an ARM base (e.g. NGC
  PyTorch ARM tags).
