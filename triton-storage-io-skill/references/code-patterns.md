# Triton storage — code patterns

## Quota and hot directories

```bash
quota
module load triton/2024.1-gcc dust   # stack/version: confirm with module spider
dust $WRKDIR
dust /scratch/DEPT/PROJECT
```

`dust` module name can drift — if load fails, `module spider dust`.

## Job-local staging (`/tmp`)

```bash
#!/bin/bash -l
#SBATCH --time=02:00:00
#SBATCH --mem=16G
#SBATCH --cpus-per-task=4
#SBATCH --tmp=100G
#SBATCH --output=logs/%x-%j.out

set -euo pipefail
TMP=/tmp/${SLURM_JOB_ID}
mkdir -p "$TMP"
trap 'rm -rf "$TMP"' EXIT

cp -a "$WRKDIR/input_dataset" "$TMP/"
cd "$TMP"
srun ./my_application -i input_dataset -o out/
mkdir -p "$WRKDIR/results"
cp -a out/ "$WRKDIR/results/"
```

## Fewer, larger array outputs

```bash
# Prefer one output per task (or shard), not thousands of tiny files:
#SBATCH --array=0-99%20
srun python run_one.py --shard "$SLURM_ARRAY_TASK_ID" \
  --out "$WRKDIR/results/shard_${SLURM_ARRAY_TASK_ID}.npz"
```

## Transfer sketches (show; confirm large syncs)

```bash
# From laptop → Triton work dir
rsync -avP ./local_data/ USER@triton.aalto.fi:scratch/work/USER/project/data/

# Pull results back
rsync -avP USER@triton.aalto.fi:scratch/work/USER/project/results/ ./results/
```

## Cleaning (avoid-zone — list paths, wait for confirmation)

```bash
# Example review list only — do not run until the user confirms:
# du -sh $WRKDIR/old_run_* $WRKDIR/apptainer_cache
# rm -rf $WRKDIR/old_run_2024_06 $WRKDIR/apptainer_cache
```
