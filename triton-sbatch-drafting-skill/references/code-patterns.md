# Triton sbatch — code patterns

Adapt these; fill in job name, paths, and resources. Create `logs/` before
`sbatch` when outputs live there. Submit with `sbatch script.sh`.

## Skeleton

```bash
#!/bin/bash -l
#SBATCH --job-name=JOBNAME
#SBATCH --time=HH:MM:SS
#SBATCH --mem=Ng                 # OR --mem-per-cpu=Ng — never both
#SBATCH --output=logs/%x-%j.out
#SBATCH --error=logs/%x-%j.err
# Optional: --cpus-per-task=N | --gpus=1 | --gres=min-vram:40g,min-cuda-cc:80
# Optional: --array=0-9%50 | --mail-type=END,FAIL | --mail-user=first.last@aalto.fi

set -euo pipefail

# Pick ONE software block (see examples below)

srun python my_script.py
```

## Serial Python (central module)

```bash
#!/bin/bash -l
#SBATCH --job-name=serial-py
#SBATCH --time=04:00:00
#SBATCH --mem=2G
#SBATCH --output=logs/%x-%j.out

module load scicomp-python-env
srun python /path/to/script.py
```

## Serial Python (own conda/mamba env)

```bash
#!/bin/bash -l
#SBATCH --job-name=serial-conda
#SBATCH --time=04:00:00
#SBATCH --mem=2G
#SBATCH --output=logs/%x-%j.out

module load mamba
source activate myenv
srun python /path/to/script.py
```

Use `source activate` / `source deactivate` on Triton — not `conda activate`.

## Shared-memory (OpenMP-style)

```bash
#!/bin/bash -l
#SBATCH --job-name=omp-py
#SBATCH --time=02:00:00
#SBATCH --mem=16G
#SBATCH --cpus-per-task=8
#SBATCH --output=logs/%x-%j.out

module load scicomp-python-env
export OMP_NUM_THREADS=$SLURM_CPUS_PER_TASK
srun python train.py
```

## GPU + VRAM / CC floor (newer GPUs / PyTorch)

```bash
#!/bin/bash -l
#SBATCH --job-name=gpu-train
#SBATCH --time=12:00:00
#SBATCH --mem=32G
#SBATCH --cpus-per-task=4
#SBATCH --gpus=1
#SBATCH --gres=min-vram:40g,min-cuda-cc:80
#SBATCH --output=logs/%x-%j.out

module load scicomp-pytorch-env/2026.1   # confirm version: module spider
srun python train_gpu.py
```

For V100-era PyTorch, drop `min-cuda-cc` and use
`module load scicomp-python-env`. Short GPU smoke test: add
`#SBATCH --partition=gpu-debug` and keep `--time` ≤ 30 minutes.

## Array

```bash
#!/bin/bash -l
#SBATCH --job-name=array-run
#SBATCH --time=01:00:00
#SBATCH --mem=1G
#SBATCH --array=0-29%10
#SBATCH --output=logs/%A_%a.out

srun ./my_application -input input_data_${SLURM_ARRAY_TASK_ID}
```

Rerun failures: `sbatch --array=2,5 script.sh`.

## CUDA binary (module toolchain)

```bash
#!/bin/bash -l
#SBATCH --job-name=pi-gpu
#SBATCH --time=00:10:00
#SBATCH --mem=500M
#SBATCH --gpus=1
#SBATCH --output=logs/%x-%j.out

module load triton/2024.1-gcc cuda/12.2.1
srun ./pi-gpu 1000000
```

Module versions drift — confirm with `module spider` before copying.

## After submit

```bash
sbatch script.sh
slurm q          # once; do not poll in a tight loop
seff JOBID       # after the job finishes — right-size next run
```
