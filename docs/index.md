# Systems Platform and Intelligence Lab

This site documents the sprc compute cluster: how to log in, how to run jobs, what is changing during the move to SLURM, and where to get help.

## What is changing

In June 2026 the cluster is moving to SLURM, the scheduler used by most university HPC systems. The change rolls out in stages so that you can adapt gradually.

SLURM is optional until June 30, which means your current direct-SSH workflow keeps working while you try the new commands. Soft enforcement begins on July 1: from that date, work you run inside an allocation gains time limits, fair-share scheduling, and GPU isolation, and batch submission with `sbatch` becomes available. A later date, announced ahead of time, brings hard enforcement, after which an allocation is required to use a compute node. The [About the transition](slurm-transition.md) page explains the reasoning and the full timeline.

!!! danger "Migrate your data by Tuesday, June 30"
    The per-node home directories and the `/mnt/data1` data drive on the compute nodes (sprc00 through sprc03) will be erased after June 30. Copy anything you want to keep to the shared storage before then, and mark your name in the migration sheet posted in `#server`. The [Migrate your data](storage-migration.md) page lists the steps.

## The one workflow change

Before you use a compute node, you request the resources you need with a single command. After that, you work the same way you do today, with the same code, conda environments, containers, VS Code Remote-SSH, and tmux.

```bash
salloc --gres=gpu:1 --time=4:00:00     # request one GPU for four hours
squeue --me                            # see which node you were assigned
ssh <node>                             # connect and work as usual
```

Work that does not need a GPU follows the same path. You request CPU cores and memory and leave off `--gres`:

```bash
salloc --cpus-per-task=8 --mem=32G --time=4:00:00
```

The [Running jobs](running-jobs.md) page covers allocations, long runs, batch jobs, and deadlines in full.

## Find your way around

<div class="grid cards" markdown>

-   **[Migrate your data](storage-migration.md)**

    Move your files to shared storage before the June 30 wipe.

-   **[Running jobs](running-jobs.md)**

    Request GPUs and CPUs, hold long runs, submit batch jobs, and handle deadlines.

-   **[Command reference](cheatsheet.md)**

    `salloc`, `sbatch`, `squeue`, `scancel`, and `sacct` at a glance.

-   **[VS Code](vscode.md)**

    Remote-SSH development on an allocated node.

-   **[FAQ](faq.md)**

    Killed jobs, time limits, pending jobs, and other common questions.

-   **[About the transition](slurm-transition.md)**

    Why the cluster is changing and when each stage takes effect.

</div>

## See what is free

The [live cluster status dashboard](/dashboard/) shows free GPUs, queue depth, and current contention. From a shell, three commands give you the same picture:

```bash
sinfo            # node and partition state: idle, mixed, or fully allocated
squeue           # everything queued and running across the cluster
squeue --me      # only your jobs
```

## The cluster at a glance

The cluster has four compute nodes, sprc00 through sprc03, each with two NVIDIA H100 NVL GPUs, for eight GPUs in total. You log in to a single login node at `sprlab005.csl.illinois.edu`, where your files live and from which you request compute.

Two partitions share that hardware. The default partition, `main`, accepts jobs of up to three days and handles all normal work. The `debug` partition is for short tests of up to one hour and gives quick turnaround. Select a partition with `--partition` (for example, `--partition=debug`). If you leave it off, your job runs in `main`.

## Get help

Ask in `#server`. If you are unsure how to move your current workflow onto SLURM, describe it in the channel and the sysadmin team will help you adapt it.

*Maintained by the sysadmin team. Last updated 2026-06-25.*
