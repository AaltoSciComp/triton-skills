# Triton LLMs — concepts

Source of truth: [LLMs](https://scicomp.aalto.fi/triton/apps/llms.html),
[ML](https://scicomp.aalto.fi/triton/discipline/machinelearning.html).
Cache contents and module versions change — verify live.

## Shared data

| Path / module | Role |
| --- | --- |
| `/scratch/shareddata/dldata/` | Shared datasets and model weights (quota-friendly) |
| `…/huggingface-hub-cache/hub` | Pre-downloaded HF models — `ls` before pulling |
| `module load model-huggingface` | Points HF Hub cache at the shared location |

Do not invent extra shared paths. If a model is missing, do not fill `$HOME`
or silently duplicate multi-GB trees under `$WRKDIR` — report the gap,
prefer asking Triton admins / Aalto RSEs to download into the shared cache
([LLMs](https://scicomp.aalto.fi/triton/apps/llms.html)), and wait for
explicit confirmation before any personal `$WRKDIR` pull.

## Software

| Need | Load (confirm with spider) |
| --- | --- |
| HF Transformers / vLLM / langchain stack | `scicomp-llm-env` |
| Shared HF cache wiring | `model-huggingface` (before running Python) |
| General scientific Python / older-GPU PyTorch | `scicomp-python-env` |
| Newer-GPU PyTorch | `scicomp-pytorch-env/…` per [PyTorch](https://scicomp.aalto.fi/triton/apps/pytorch/) |

Load modules **inside** the job. Own envs: only after explicit confirmation;
keep caches under `$WRKDIR`.


## Anti-patterns

- Default Hub download into `~/.cache/huggingface` on the 10GB `$HOME`
- Unsupervised `pip install transformers torch …` on the login node
- Assuming laptop CUDA ≡ Triton GPU generation / CC
- Leaving interactive GPU Jupyter idle for production training (use batch)
- Treating shared `/scratch/shareddata` as writable personal storage

## Related docs

- GPU requests: [GPU tutorial](https://scicomp.aalto.fi/triton/tut/gpu.html)
- Storage: [storage](https://scicomp.aalto.fi/triton/tut/storage.html)
