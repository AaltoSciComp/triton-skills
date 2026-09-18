# Triton containers — code patterns

## Cache before any pull/build

```bash
mkdir -p "$WRKDIR/apptainer_cache"
export APPTAINER_CACHEDIR="$WRKDIR/apptainer_cache"
```

## Prebuilt module

```bash
module --terse avail 2>&1 | grep apptainer
module load apptainer-fmriprep/25.2.3   # example — confirm version with spider
apptainer run-help "$IMAGE_PATH"
apptainer exec "$IMAGE_PATH" fmriprep --version
```

## Interactive / one-shot

```bash
apptainer shell IMAGE.sif
apptainer exec --bind /scratch,/m,/l IMAGE.sif COMMAND
apptainer run IMAGE.sif
# GPU:
apptainer exec --nv --bind /scratch IMAGE.sif nvidia-smi
```

## Batch + GPU

```bash
#!/bin/bash -l
#SBATCH --time=01:00:00
#SBATCH --mem=10G
#SBATCH --cpus-per-task=4
#SBATCH --gpus=1
#SBATCH --output=logs/%x-%j.out

srun apptainer exec --nv --bind /scratch \
  train.sif python /opt/train.py "$WRKDIR/input"
```

## Build from Docker / definition

```bash
cd "$WRKDIR"
export APPTAINER_CACHEDIR="$WRKDIR/apptainer_cache"
mkdir -p "$APPTAINER_CACHEDIR"
apptainer build image.sif docker://ORG/NAME:VERSION
apptainer build image.sif recipe.def
```

Example definition (pin tags; NGC tags change monthly):

```
Bootstrap: docker
From: nvcr.io/nvidia/pytorch:26.02-py3

%post
  pip install transformers==4.57.6
```

Keep `.def` in git. Heavy / ARM builds: submit an `sbatch` on the right
partition rather than building on the login node.

## Verify

```bash
apptainer exec --nv IMAGE.sif nvidia-smi
apptainer exec --bind /scratch IMAGE.sif ls /scratch
apptainer exec --nv IMAGE.sif python -c "import torch; print(torch.cuda.is_available())"
```
