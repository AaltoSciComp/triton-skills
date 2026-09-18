# Triton job monitoring — concepts

Source of truth: [monitoring tutorial](https://scicomp.aalto.fi/triton/tut/monitoring/),
[Slurm ref](https://scicomp.aalto.fi/triton/ref/slurm.html).

## States

- **PENDING** — resources busy or request too large (mem/time/rare GPU). Check
  reason in `slurm q`. `BadConstraints` can appear when some partitions cannot
  satisfy the request even though others can — see FAQ if needed.
- **RUNNING** — optional mid-run checks; `seff` is most useful after completion
  (or after meaningful runtime).
- **COMPLETED** — always check `seff` before the next big submit.
- **FAILED / OOM / TIMEOUT** — raise mem/time modestly; fix code if OOM is far
  above request (leak / wrong data path).
- **CANCELLED** — user/admin stop; check logs.
- **launch failed requeued held** — external failure; `scontrol release JOBID`
  only if the user wants it released.

## Efficiency playbook

| Observation | Suggestion |
| --- | --- |
| Mem efficiency ≪ 50% | Lower `--mem` / `--mem-per-cpu` |
| Mem ~100% or OOM | Raise with headroom; check leaks |
| Low CPU efficiency, many CPUs | Fewer `--cpus-per-task` unless parallel |
| Hit walltime | Raise `--time`, or checkpoint / split |
| Low GPU util, high CPU | Maybe more CPUs for data loading (don’t oversubscribe) |
| Low GPU + low CPU | Not using GPU, bad batch size, or I/O bound |
| Many tiny array jobs | Coarser array / combine work |

`--mem-per-cpu` × `--ntasks` × `--cpus-per-task` can be huge — spell that out
when both mem styles are set (**never** combine `--mem` and `--mem-per-cpu`).

## GPU efficiency

- `module load seff-gpu` then `seff JOBID`, and/or
  `sacct -j JOBID -o TRESUsageInAve -p` (Triton-specific TRES fields).
- Live GPU inventory / naming: [GPU ref](https://scicomp.aalto.fi/triton/ref/gpu.html)
  — do not invent types.
