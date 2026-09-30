# Triton LLMs — code patterns

Adapt resources; confirm modules with `module spider`. Create `logs/` before
`sbatch` if outputs live there. Submit only if asked.

## List shared HF models

```bash
ls /scratch/shareddata/dldata/huggingface-hub-cache/hub
```

## GPU job: shared cache + scicomp-llm-env

```bash
#!/bin/bash -l
#SBATCH --job-name=hf-llm
#SBATCH --time=00:30:00
#SBATCH --cpus-per-task=4
#SBATCH --mem=16G
#SBATCH --gpus=1
#SBATCH --output=logs/%x-%j.out
#SBATCH --error=logs/%x-%j.err

module load model-huggingface   # shared hub cache under /scratch/shareddata/dldata
module load scicomp-llm-env
srun python huggingface_example.py
```

`--mem` is host RAM, not GPU VRAM. For a VRAM floor, add one `--gres=…`
([GPU ref](https://scicomp.aalto.fi/triton/ref/gpu.html)).


## Minimal Transformers skeleton

```python
from transformers import pipeline
import torch

pipe = pipeline(
    "text-generation",
    model="mistralai/Mistral-7B-Instruct-v0.3",  # prefer a model already in shared cache
    torch_dtype=torch.bfloat16 if torch.cuda.is_available() else torch.float32,
    device_map="auto",
)
print(pipe("Explain Slurm in one sentence.", max_new_tokens=64))
```

Pin model IDs the user asked for; if not cached, say so and prefer
requesting Triton admins / Aalto RSEs add it to the shared cache — or, after
explicit confirmation, a deliberate `$WRKDIR` download. Never silently fill
`$HOME`.

## Personal HF cache under $WRKDIR (only if needed)

```bash
export HF_HOME="$WRKDIR/hf-home"
mkdir -p "$HF_HOME"
# still prefer module load model-huggingface when the shared cache has the model
```

## After run

```bash
slurm q
seff JOBID    # GPU: module load seff-gpu && seff JOBID
```
