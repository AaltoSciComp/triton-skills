# Triton job monitoring — code patterns

## Queue and history (few shots — no agent poll loops)

```bash
slurm q              # or: slurm qq
slurm history
slurm history 1day
slurm j JOBID
```

User-driven watch (only if they ask): `slurm w q` — they should Ctrl-C when
done; agents must not leave watchers running unattended.

## Efficiency

```bash
seff JOBID
module load seff-gpu
seff JOBID
sacct -j JOBID -o TRESUsageInAve -p
```

## Cancel (avoid-zone — confirm first)

```bash
# Show this to the user; run only after they confirm:
scancel JOBID
# scancel -u $USER    # extreme — never without explicit request
```

## After a retune

```bash
# Propose an edited script, then only if asked:
sbatch script.sh
slurm q
# later:
seff JOBID
```
