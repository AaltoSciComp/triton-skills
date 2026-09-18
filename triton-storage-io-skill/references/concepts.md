# Triton storage — concepts

Source of truth: [storage tutorial](https://scicomp.aalto.fi/triton/tut/storage/),
[storage ref table](https://scicomp.aalto.fi/triton/ref/storage.html),
[quotas](https://scicomp.aalto.fi/triton/usage/quotas/),
[local storage](https://scicomp.aalto.fi/triton/usage/localstorage/),
[small files](https://scicomp.aalto.fi/triton/usage/smallfiles/).
Quota numbers and disk sizes drift — prefer live pages over memorizing limits.

## Where things go

| Location | Path | Backup | Use for |
| --- | --- | --- | --- |
| Home | `$HOME` (`/home/$USER`) | Yes (~10GB hard) | Dotfiles / tiny configs — **not** data or envs |
| Work | `$WRKDIR` (`/scratch/work/$USER`) | **No** | Personal compute data, checkouts |
| Project | `/scratch/DEPT/PROJECT/` | **No** | Shared group data (preferred for teams) |
| Node local | `/tmp` via `--tmp=nnnG` | No; cleared after jobs leave the node | Heavy per-node scratch / many small files |
| RAM disk | `/dev/shm` (and `/tmp` on diskless nodes) | No | Small ultra-fast temp; counts against `--mem` |

Default: work under `$WRKDIR` or project scratch, not `$HOME`.

## Quotas & inodes

- `quota` — usage vs limits (bytes **and** file count).
- Large trees: load `dust` (see `code-patterns.md`) then `dust $WRKDIR`.
- Inodes matter — conda envs, checkpoints, and many tiny files burn file quota
  before byte quota.
- Prefer [requesting project storage](https://scicomp.aalto.fi/data/requesting/)
  over growing personal `$WRKDIR` quota.

## Lustre / small files

Scratch is Lustre: great for large sequential I/O, poor for many tiny files.
Anti-priors:

- Do not point thousands of array tasks at one shared conda prefix for cold
  starts, or write one tiny file per task to scratch.
- Unpack / random-access on node-local `/tmp`, then copy durable outputs back
  to `$WRKDIR` or project scratch.
- Details: [small files](https://scicomp.aalto.fi/triton/usage/smallfiles/).

## Local `/tmp` and ramfs

- Request space with `--tmp=nnnG` (not exclusively reserved — handle ENOSPC).
- Optional: `--constraint=localdisk` when any local disk is enough.
- Use `/tmp/$SLURM_JOB_ID/` (per-user `/tmp` is shared if two of your jobs land
  on the same node).
- Copy needed inputs in at start; copy keepers back to scratch before exit;
  remove the job temp dir.
- `/dev/shm` is memory — increase `--mem` accordingly.
- Not for multi-node shared I/O (use Scratch).

## Transfers & secrets

- Interactive transfer hosts: `rsync` / `sftp` → `triton.aalto.fi`; SMB →
  `data.triton.aalto.fi`. See [remote data](https://scicomp.aalto.fi/triton/tut/remotedata/).
- Multi-TB syncs need human approval / planning.
- Keep tokens and credentials out of agent-readable trees (agents may send
  file contents to external LLMs — [AI Agents on HPC](https://scicomp.aalto.fi/triton/usage/ai-agents/)).
