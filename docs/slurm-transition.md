# About the transition

This page explains why the cluster is moving to SLURM, what changes for you, and when each stage takes effect.

!!! danger "First, migrate your data by Tuesday, June 30"
    Before anything else, the per-node home directories and the `/mnt/data1` data drive on the compute nodes are erased after June 30. Move your work to the shared storage now. See [Migrate your data](storage-migration.md).

## Why the cluster is changing

The sprc cluster has four machines and eight high-end GPUs shared across the whole lab. Until now, nothing managed who used what, which led to three recurring problems.

1.  **GPUs held but not used.** Someone starts a process, then leaves, and the GPU stays reserved and idle while others wait for it.
2.  **No fairness.** Someone who has already used the cluster heavily this week has the same chance at the next free GPU as someone who has not touched it.
3.  **No visibility.** Because nothing recorded usage, the lab could not answer basic questions such as how busy the cluster really is, or whether it needs more hardware.

SLURM addresses all three. It is the scheduler used by most university HPC clusters, so it is well understood and well supported.

## What changes for you

The one real change is that you request the resources you need before you use a compute node:

```bash
salloc --gres=gpu:1 --time=4:00:00              # a GPU
salloc --cpus-per-task=8 --mem=32G --time=4:00:00   # CPU cores and memory, no GPU
```

Everything else stays the same. You keep your login and password, your home directory and files (once they are on the shared storage), your code, conda environments, and containers, and your editors and tools, including VS Code Remote-SSH and tmux.

Two habits are new. The first is time limits: a four-hour request ends after four hours, so for longer work you ask for more time up front and hold the allocation in `tmux` so it survives a disconnect. The second is that resources return to the pool when your time is up, which is what makes a free GPU available when you need one.

This applies to all compute-node work. CPU-only jobs go through SLURM the same way GPU jobs do, with the same allocations, time limits, and fair-share scheduling. The only GPU-specific piece is GPU isolation, which simply has nothing to apply to a job that requested no GPU.

## The timeline

The dates are fixed through soft enforcement. Hard enforcement follows later, on a date announced ahead of time.

| Stage | When | What you notice |
|---|---|---|
| Optional period and data migration | now to June 30 | SLURM is live to try with `salloc`, and your direct-SSH workflow still works. Move your data to the shared storage before June 30. See [Migrate your data](storage-migration.md). |
| Data wipe | after June 30 | The per-node home directories and `/mnt/data1` drives are erased. |
| Soft enforcement | July 1 | Work inside an allocation gains time limits, fair-share scheduling, and GPU isolation, and `sbatch` becomes available. Direct SSH still works, so try the new workflow and report any issues. |
| Hard enforcement | to be announced | An allocation is required to use a compute node. Direct SSH without one is declined, with sysadmin accounts excepted. |

## What soft enforcement means

From July 1, work you run inside an allocation is bounded. If you use `salloc` or `sbatch`, the job receives time limits and fair-share scheduling, where queue order reflects recent usage, and a GPU job also receives GPU isolation, so it sees only the GPUs it reserved. CPU-only jobs receive the time limits and fair-share scheduling as well.

Batch submission with `sbatch` becomes available on July 1, once every home directory is on the shared storage.

Direct SSH still works during this period. You can connect to a compute node as before, with or without an allocation, since nothing is blocked yet. Use the optional period to try the allocation-based workflow and report any issues so the sysadmin team can resolve them before hard enforcement.

## Deadlines are protected

Fair scheduling does not override real deadlines. If you have a paper or demo, ask in `#server` and the sysadmin team can raise your queue priority for a set window so your jobs start sooner. There is a supported priority path that the sysadmins control.

## Try it now

The best way to be ready is to use SLURM during the optional period, while your old workflow is still available:

```bash
salloc --gres=gpu:1 --time=4:00:00     # request a GPU, then ssh to your node
squeue --me                            # your jobs
sinfo                                  # what is free
```

See the [Command reference](cheatsheet.md) for the full set of commands, and the [FAQ](faq.md) for common questions.

## Questions

Ask in `#server`. If you are unsure how to move your specific workflow onto SLURM, describe it in the channel and the sysadmin team will help.
