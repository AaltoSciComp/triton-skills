# Triton agent hygiene — concepts

Source of truth: [AI Agents on HPC](https://scicomp.aalto.fi/triton/usage/ai-agents.html),
[VS Code](https://scicomp.aalto.fi/triton/apps/vscode.html),
[connecting](https://scicomp.aalto.fi/triton/tut/connecting.html).

## Where is the agent running?

| Setup | Runs on Triton? | Cluster notes |
| --- | --- | --- |
| Editor/CLI agent only on laptop | No | Still risks LLM exfil of local copies of data |
| VS Code Remote-SSH / CLI agent on cluster | Yes (login node) | Use **`code.triton.aalto.fi`**; agent can read anything in its tree |

If unsure, use garage — do not guess hostnames.

## Policy checklist (agent session)

- [ ] SSH / Remote-SSH target is `code.triton.aalto.fi`
- [ ] Opened folder is a **project**, not all of home/scratch
- [ ] No secrets or restricted data in the workspace the agent can read
- [ ] Heavy work goes through Slurm, not the login CPU
- [ ] No job-submit or queue-poll storms
- [ ] Destructive commands shown and confirmed
- [ ] User reviews outputs before trusting results

## Sibling skills (load when the task matches)

| Task | Skill |
| --- | --- |
| Draft `sbatch` | `triton-sbatch-drafting-skill` |
| `seff` / queue / right-size | `triton-job-monitoring-skill` |
| Modules / conda | `triton-modules-envs-skill` |
| Paths / quota / I/O | `triton-storage-io-skill` |
| Apptainer | `triton-containers-skill` |
