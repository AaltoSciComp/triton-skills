---
name: triton-llms-skill
description: >
  Runs LLMs and HuggingFace models on Aalto Triton using shared caches and
  central envs (scicomp-llm-env, model-huggingface, /scratch/shareddata/dldata).
  Use when the user mentions HuggingFace, transformers, vLLM, langchain,
  downloading models, HF Hub cache, or LLM inference/fine-tune on Triton.
  Drafts jobs; does not download multi-GB models into $HOME or wipe caches
  without confirmation.
---

# LLMs on Triton

Prefer shared models and central envs over Hub downloads into `$HOME`.
Live docs: [LLMs](https://scicomp.aalto.fi/triton/apps/llms.html),
[ML discipline](https://scicomp.aalto.fi/triton/discipline/machinelearning.html),
[PyTorch](https://scicomp.aalto.fi/triton/apps/pytorch/).
Module versions and cache contents drift — `module spider` / Docs MCP /
`ls` the shared cache over memory.

## Operating rules (read first)

1. **Safety tiers**
   - **Read-only** — `module spider`, list shared cache, `quota` on request.
   - **Create** — draft `sbatch` / Python; show before long downloads or
     env creates; submit only if asked.
   - **Avoid-zone** — wiping HF caches, multi-GB Hub pulls into `$HOME`. Confirm paths; scratch is
     **not** backed up.

2. **Anti-priors**
   - Load **`model-huggingface`** so Transformers uses the shared hub cache
     under `/scratch/shareddata/dldata/…` — do not default to
     `~/.cache/huggingface` on `$HOME`.
   - Prefer **`scicomp-llm-env`** (Transformers, vLLM, langchain, …) over an
     unsupervised pip stack.
   - List what is already cached before request downloading:
     `ls /scratch/shareddata/dldata/huggingface-hub-cache/hub`.
   - Missing models: do **not** silently pull huge weights; say the model is
     absent from the shared cache. Prefer asking Triton admins / Aalto RSEs
     to add it to the shared cache ([LLMs](https://scicomp.aalto.fi/triton/apps/llms.html));
     only with explicit confirmation, a deliberate `$WRKDIR` download.
   - GPU jobs need `--gpus=…`; pair software with GPU generation (newer GPUs
     may need `scicomp-pytorch-env/…` + CC GRES —
     [GPU ref](https://scicomp.aalto.fi/triton/ref/gpu.html) /
     [PyTorch](https://scicomp.aalto.fi/triton/apps/pytorch/)).
   - No login-node inference/training — `sbatch` / `sinteractive` /
     `gpu-debug`. Agents: **`code.triton.aalto.fi`**.


3. **Secrets.** HF tokens and API keys stay out of agent-readable trees.

## Quick start: common requests

- **"Run this HF model on Triton."** Check shared cache →
  `module load model-huggingface` + `scicomp-llm-env` → GPU sbatch.
  Patterns: `references/code-patterns.md`.
- **"Download model X."** Prefer shared cache. If absent, suggest requesting
  Triton admins / Aalto RSEs add it to the shared cache; do not pull by
  default — with explicit confirmation, put `HF_HOME` under `$WRKDIR`.
- **"vLLM / langchain / fine-tune."** Start from `scicomp-llm-env`; confirm
  packages with the env; right-size GPUs via live
  [GPU ref](https://scicomp.aalto.fi/triton/ref/gpu.html).


## Reference material

- `references/concepts.md` — cache layout, modules, anti-patterns.
- `references/code-patterns.md` — sbatch + minimal Transformers skeleton.
