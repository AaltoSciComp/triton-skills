# Authoring Triton skills

Notes for producing or updating skills here. Consumer install: `README.md`.
Inspired by [CSCfi/csc-skills](https://github.com/CSCfi/csc-skills); adapted for
Aalto Triton / SciComp docs.

## What belongs in a skill

Only **Triton-specific** behaviour a capable model does not already know
(hostnames, modules, GRES, path conventions, policy) and places a default is
**wrong or expensive** here (`source activate` not `conda activate`; `sbatch`
not `bash script.sh`; inventing partitions; login-node compute; job storms;
deletes on scratch).

Cut generic Slurm/Python/Docker explainers, unprompted boilerplate, filler, and
restating the same fact twice in one file. Test: if deleting a line would not
change model behaviour, delete it.

Skills are **for agents to execute**, not end-user tutorials. Every instruction
must be an agent action (draft, verify, confirm, refuse, look up). Drop
human-only steps (visit garage, disclose AI use, open a browser yourself,
create accounts). Pointing at a URL the operator may open is fine; “go chat
with SciComp” is not.

`SKILL.md` is a short router; detail loads from `references/`. Live docs /
Docs MCP (`search_scicomp_docs`) win over memorized partition/GPU/module lists —
link [triton/ref](https://scicomp.aalto.fi/triton/ref/) instead of pasting
inventory tables.

| Mechanism | Use for |
| --- | --- |
| **Skill** | Stable workflows + Triton quirks + safety posture |
| **Docs MCP** / live URLs | Anything that drifts (partitions, GPUs, module versions) |
| **Agent rules** | Short always-on constraints — not full task workflows |

## Layout

Template: `triton-sbatch-drafting-skill/`. Directory name = YAML `name`.
`triton-agent-hygiene-skill` may omit `code-patterns.md`.

```
triton-<name>-skill/
├── SKILL.md                 # router (always loaded when skill matches)
├── sources.tsv              # provenance URLs (preferred)
└── references/
    ├── concepts.md          # mechanics + anti-priors ("advise")
    └── code-patterns.md     # copy-adapt snippets ("generate")
```

**`SKILL.md`:** frontmatter `name` + `description` (trigger — how users ask;
nouns/verbs + one-line safety; ≤1024 chars; rules in the body). Body:
Operating rules → Quick start → Reference material. Keep only always-needed
material inline.

**`concepts.md`:** site mechanics, tables, policy pointers; flag anti-priors.
**`code-patterns.md`:** Triton wiring only — no long standard-Slurm tutorials.

## Shared safety tiers

Same model in every skill (tune examples to the domain):

| Tier | Agent may | Examples |
| --- | --- | --- |
| **Read-only** | Run on request | `slurm q`, `seff`, `module spider`, `quota` (no `watch` / tight loops) |
| **Create** | After disclosure; prefer showing first | Draft `sbatch`; submit **only** if asked |
| **Avoid-zone** | Reviewable script; run after confirmation | `scancel` of others’/shared jobs, `rm -r` on scratch, mass moves, bulk env rebuilds |

"Write me a job script" → write it, do **not** `sbatch`. Scratch / project
scratch are **not** backed up. Policy:
[AI Agents on HPC](https://scicomp.aalto.fi/triton/usage/ai-agents/).

## Always-on agent rule

Required for consumers. Short etiquette that applies even when no skill
matched: [`triton-rules.md`](triton-rules.md). On Triton the published copy
is `/scratch/shareddata/triton-skills/triton-rules.md` — users add a line in
`AGENTS.md` / `CLAUDE.md` / agent rule config pointing at that path (see
`README.md`). This git repo is development only. Do not duplicate full
workflows in the rules file — skills own those.

## Current skills

`triton-agent-hygiene-skill`, `triton-sbatch-drafting-skill`,
`triton-job-monitoring-skill`, `triton-modules-envs-skill`,
`triton-llms-skill`, `triton-storage-io-skill`,
`triton-containers-skill`.

## Editing & review

1. Check claims against live docs / MCP; emit a short drift list before editing.
2. Sync every mirrored place: `description`, quick start, `concepts.md`,
   `code-patterns.md`, README blurb.
3. Prefer placeholders + `module spider` over pinning versions that will drift
   (`scicomp-pytorch-env/<ver>`, CUDA stacks, Apptainer tags, NGC tags).

Deferred packaging: `.agents/skills/` symlinks; Claude Code marketplace
manifests. Install via `ln -s` — see `README.md`.
