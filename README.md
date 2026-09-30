# Triton skills

Agent skills for [Aalto Triton](https://scicomp.aalto.fi/triton/) HPC.
They teach coding agents **Triton-specific** workflows and safety posture —
not generic Slurm/Python tutorials.

**On Triton (consumers):** use the fixed shared tree
`/scratch/shareddata/triton-skills/`.
**This git repo** is for development; changes are published to that path.

Live cluster facts (partitions, GPUs, module versions) change. Prefer
[SciComp docs](https://scicomp.aalto.fi/triton/ref/) or the Docs MCP tool
`search_scicomp_docs` over memorized inventories. Policy:
[AI Agents on HPC](https://scicomp.aalto.fi/triton/usage/ai-agents/).

Authoring notes: [`AGENTS.md`](AGENTS.md).

## Always-on agent rule

Required. Put this in `AGENTS.md`, `CLAUDE.md`, Cursor rules, or whatever
rule/config file your agent loads — so it applies even when no skill matched:

```text
When working on HPC, read /scratch/shareddata/triton-skills/triton-rules.md and follow the policies there.
```

## Skills

| Skill | Use when |
| --- | --- |
| `triton-agent-hygiene-skill` | Agent etiquette, `code.triton`, secrets, job storms |
| `triton-sbatch-drafting-skill` | Draft `#SBATCH` scripts (serial/array/GPU/MPI) |
| `triton-job-monitoring-skill` | `slurm q`, `seff`, right-sizing |
| `triton-modules-envs-skill` | Lmod, conda/mamba, central envs |
| `triton-llms-skill` | HF / LLMs, shared model cache, `scicomp-llm-env` |
| `triton-storage-io-skill` | `$HOME` / `$WRKDIR` / project scratch, quotas, I/O |
| `triton-containers-skill` | Apptainer/Singularity, binds, GPU/`--nv`, ARM |

**Recommended set:** always-on rule + hygiene + the task skills you need.

## Install skills (symlink)

Point your agent’s skills directory at the shared tree on Triton:

```bash
REPO=/scratch/shareddata/triton-skills
```

**Cursor** (personal):

```bash
mkdir -p ~/.cursor/skills
for d in "$REPO"/triton-*-skill; do
  ln -sfn "$d" ~/.cursor/skills/"$(basename "$d")"
done
```

**Cursor** (project-local):

```bash
mkdir -p .cursor/skills
for d in "$REPO"/triton-*-skill; do
  ln -sfn "$d" .cursor/skills/"$(basename "$d")"
done
```

**Claude Code** (typical paths — confirm for your install):

```bash
mkdir -p ~/.claude/skills   # or .claude/skills in a project
for d in "$REPO"/triton-*-skill; do
  ln -sfn "$d" ~/.claude/skills/"$(basename "$d")"
done
```

Shared-tree updates apply through the symlinks.


## Help

[SciComp garage](https://scicomp.aalto.fi/help/garage/) ·
[Zulip](https://scicomp.zulip.cs.aalto.fi/)
