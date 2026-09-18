# Triton modules & envs — code patterns

## Discover and load

```bash
module spider pytorch
module spider scicomp-python-env
module load scicomp-python-env
module list
# module purge   # only when intentionally resetting
```

## Central env inside sbatch

```bash
#!/bin/bash -l
#SBATCH --time=01:00:00
#SBATCH --mem=4G
#SBATCH --output=logs/%x-%j.out

module load scicomp-python-env
srun python script.py
```

## Newer-GPU PyTorch (confirm module version with spider)

```bash
#!/bin/bash -l
#SBATCH --time=04:00:00
#SBATCH --mem=32G
#SBATCH --cpus-per-task=4
#SBATCH --gpus=1
#SBATCH --gres=min-cuda-cc:80
#SBATCH --output=logs/%x-%j.out

module load scicomp-pytorch-env/2026.1
srun python train.py
```

## Own mamba env — activate (Triton anti-prior)

```bash
module load mamba
source activate myenv          # not: conda activate myenv
# ... run ...
source deactivate
```

## First-time mamba paths (off `$HOME`)

```bash
module load mamba
mkdir -p "$WRKDIR/.conda_pkgs" "$WRKDIR/.conda_envs"
conda config --append pkgs_dirs ~/.conda/pkgs
conda config --append envs_dirs ~/.conda/envs
conda config --prepend pkgs_dirs "$WRKDIR/.conda_pkgs"
conda config --prepend envs_dirs "$WRKDIR/.conda_envs"
```

Official steps:
https://scicomp.aalto.fi/triton/apps/conda/#conda-first-time-setup

## Create from file (needs user approval)

```bash
module load mamba
mamba env create --file environment.yml
# updates: mamba env update --file environment.yml --prune
```

## Quota pressure

```bash
mamba clean -a          # caches only
quota
```
