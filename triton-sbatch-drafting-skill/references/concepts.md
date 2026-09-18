# Triton sbatch — concepts

Source of truth: [Slurm ref](https://scicomp.aalto.fi/triton/ref/slurm.html),
[GPU ref](https://scicomp.aalto.fi/triton/ref/gpu.html). Hardware and module
names change — prefer live docs, `slurm features`, or Docs MCP over copying
full inventory tables into answers.

## Common `#SBATCH` options

| Option | Meaning |
| --- | --- |
| `--time=HH:MM:SS` / `DD-HH` | Walltime |
| `--mem=N` / `--mem-per-cpu=N` | Memory (e.g. `4G`); **never both** |
| `--cpus-per-task=N` / `-c` | Shared-memory cores |
| `--ntasks=N` / `-n` | MPI / multi-process |
| `--nodes=N-M` / `-N` | Node count range |
| `--gpus=N` or `TYPE:N` | GPU count / type |
| `--gres=min-vram:NNg` / `min-cuda-cc:NN` | VRAM / compute capability (one `--gres`, comma-separated) |
| `--partition=NAME` | Usually omit; auto from resources |
| `--job-name` / `--output` / `--error` | `%j` `%x` `%A` `%a` |
| `--array=…` | e.g. `0-9`, `1-10,15`, `0-99%50` |
| `--constraint=` / `--tmp=nnnG` / `--exclusive` | Feature pin / local `/tmp` / whole nodes |
| `--mail-type=` + `--mail-user=` | Aalto email only |

**Vague defaults:** serial Python `--time=01:00:00 --mem=4G`; one GPU unless
the app needs more.

## GPU request patterns

| Need | Example |
| --- | --- |
| Any single GPU | `--gpus=1` |
| Named type | `--gpus=h200:1` (confirm available types live) |
| Min VRAM | `--gpus=1` + `--gres=min-vram:80g` |
| Min CUDA CC | `--gpus=1` + `--gres=min-cuda-cc:80` |
| VRAM + CC | `--gpus=1` + `--gres=min-vram:40g,min-cuda-cc:80` |
| Debug ≤30 min | `--partition=gpu-debug` + `--gpus=1` |
| AMD | `--gpus=1` + `-p gpu-amd` (confirm partition live) |

Triton-specific rules:

- Prefer capability GRES (`min-vram` / `min-cuda-cc`) over pinning a model
  name when "any GPU with enough memory/CC" is enough.
- `scicomp-python-env` PyTorch → older GPUs (V100-era).
- Newer GPUs (H100/H200/B300-class): load the current
  `scicomp-pytorch-env/…` from `module spider` / [PyTorch](https://scicomp.aalto.fi/triton/apps/pytorch/)
  and pair with the CC GRES the docs require (often `min-cuda-cc:80`).
- Grace-H200 is **ARM** — x86 binaries and x86 containers will not run.
  Partition name and GPU-hour caps: [Grace Hopper](https://scicomp.aalto.fi/triton/usage/gracehopper/).
- **Do not paste memorized VRAM tables.** Look up live inventory:
  `slurm features` / [available GPUs](https://scicomp.aalto.fi/triton/ref/gpu.html).

## Software & paths

Put activation **inside** the script after `#SBATCH`:

| Choice | In the script |
| --- | --- |
| Own conda/mamba env | `module load mamba` then `source activate ENV` |
| Central Python | `module load scicomp-python-env` |
| Central PyTorch on newer GPUs | `module load scicomp-pytorch-env/…` (spider) + matching CC GRES |

Do not recreate or silently modify the user’s env unless they ask. Data under
`$WRKDIR` (`/scratch/work/$USER`) or `/scratch/DEPT/PROJECT/`, not `$HOME`.

- Envs: [conda/mamba](https://scicomp.aalto.fi/triton/apps/conda/)
- Storage: [data storage](https://scicomp.aalto.fi/triton/tut/storage/)

## Parallelism checks

- **Array:** map `$SLURM_ARRAY_TASK_ID`; throttle with `%N`; avoid thousands
  of tiny Lustre-thrashing jobs.
- **Shared memory:** `export OMP_NUM_THREADS=$SLURM_CPUS_PER_TASK` (or
  equivalent for the runtime).
- **GPU:** always `--gpus`; no CPU-only work on GPU nodes; match PyTorch /
  CUDA build to GPU generation / CC.
- **MPI:** start workers with `srun` as required by the stack; don’t assume
  generic `mpirun` defaults.

## Tutorials

- [Serial](https://scicomp.aalto.fi/triton/tut/serial/)
- [Arrays](https://scicomp.aalto.fi/triton/tut/array/)
- [GPUs](https://scicomp.aalto.fi/triton/tut/gpu/)
- [Parallel](https://scicomp.aalto.fi/triton/tut/parallel/)
- [Monitoring](https://scicomp.aalto.fi/triton/tut/monitoring/) (`seff`)
