---
name: triton-containers-skill
description: >
  Runs and builds Apptainer/Singularity containers on Aalto Triton, including
  Docker image conversion, bind mounts, GPU passthrough and ARM/Grace-Hopper
  images. Use when the user mentions apptainer, singularity, a .sif file, a
  docker:// image, containerised software on the cluster, --nv, --bind,
  IMAGE_PATH, or prebuilt images (fmriprep/freesurfer/gatk-style). Drafts
  run/build commands; large pulls, cache wipes, and deletes need confirmation.
---

# Containers on Triton

**Apptainer** is the runtime (`singularity` is the same binary). Docker is not
available as a daemon — convert/pull with Apptainer. System binary in
`/usr/bin` (no module required for the runtime). Live docs:
[Singularity/Apptainer](https://scicomp.aalto.fi/triton/usage/singularity/),
[NVIDIA containers](https://scicomp.aalto.fi/triton/apps/nvidiacontainers/),
[Grace Hopper](https://scicomp.aalto.fi/triton/usage/gracehopper/).

## Operating rules (read first)

1. **Safety tiers**
   - **Read-only** — `apptainer run-help`, `module show`, inspect `.def` /
     existing `.sif` on request.
   - **Create** — draft `exec`/`sbatch` lines and definition files; show before
     long `apptainer build` / pulls.
   - **Avoid-zone** — wiping caches, deleting `.sif` trees, mass pulls into
     `$HOME`. Confirm paths; scratch is **not** backed up.

2. **Anti-priors**
   - Set `APPTAINER_CACHEDIR` under `$WRKDIR` before build/pull (default cache
     is under `$HOME` and fills the 10GB quota).
   - GPU: Slurm `--gpus=…` **and** Apptainer `--nv`.
   - Bind `/scratch` (and `/m`,`/l` if needed) — `$HOME` is auto-bound;
     scratch is not.
   - Grace-H200 is **ARM** — x86 images/binaries will not run; build on an ARM
     node from an ARM base. Partition/limits: verify
     [Grace Hopper](https://scicomp.aalto.fi/triton/usage/gracehopper/).
   - Prefer one `.sif` over `--sandbox` (many small files hurt Lustre).

3. Containers isolate processes, **not** data confidentiality — still keep
   secrets out of agent-readable trees.

## Quick start: common requests

- **"Run this container on my data."** `apptainer exec` + binds; GPU → `--nv`.
  Patterns: `references/code-patterns.md`.
- **"Prebuilt SciComp image."** `module avail` / spider for `apptainer-*`;
  `$IMAGE_PATH` or `apptainer_wrapper` — check `module show`.
- **"Build from Docker/NGC."** Cache under `$WRKDIR`; build in a job if heavy.
- **"Grace Hopper / ARM."** Correct partition + ARM base image; see concepts.

## Reference material

- `references/concepts.md` — binds, modules, ARM, cache, NVIDIA modules gone.
- `references/code-patterns.md` — exec/sbatch, build, verify.

Related: `triton-sbatch-drafting-skill`, `triton-storage-io-skill`,
`triton-agent-hygiene-skill`. Help:
[SciComp garage](https://scicomp.aalto.fi/help/garage/).
