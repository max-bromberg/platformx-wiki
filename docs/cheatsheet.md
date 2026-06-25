# Command reference

A brief lookup for day-to-day use. The [Running jobs](running-jobs.md) page explains these in context.

!!! warning "Migrate your data by June 30"
    The per-node home directories and `/mnt/data1` are erased after June 30. [Move your data to shared storage first](storage-migration.md).

## Request a node

Request a GPU and a time limit, then connect to the node you are given:

```bash
salloc --gres=gpu:1 --time=4:00:00            # 1 GPU for 4 hours, default partition (main)
salloc --gres=gpu:2 --time=2:00:00            # both GPUs on a node
salloc --gres=gpu:1 -p debug --time=0:30:00   # quick test on the debug partition
```

For CPU-only work, request cores and memory and leave off `--gres`:

```bash
salloc --cpus-per-task=8 --mem=32G --time=4:00:00
```

After `salloc` returns, connect to the assigned node and work normally:

```bash
ssh <node>        # for example, sprc01
nvidia-smi        # the GPUs available to you
```

## Long runs

Keep an interactive long run alive in `tmux` so it survives a dropped connection:

```bash
tmux                                   # start tmux on the login node
salloc --gres=gpu:1 --time=24:00:00    # hold the allocation
ssh <node>                             # detach with Ctrl-b d, reattach with tmux attach
```

## Submit a batch job

Batch submission becomes available at soft enforcement on July 1, once the June 30 storage migration is complete. Until then, use `salloc` in `tmux` for long runs.

```bash
sbatch --gres=gpu:1 --time=12:00:00 train.sh   # queue a job, then walk away
squeue --me                                    # watch it
```

A batch script sets its own options with `#SBATCH` lines:

```bash
#!/bin/bash
#SBATCH --gres=gpu:1
#SBATCH --time=12:00:00
#SBATCH --job-name=train
srun python train.py
```

## Watch your jobs

```bash
squeue --me                 # your queued and running jobs
squeue                      # everyone's jobs
squeue --me --start         # estimated start time for pending jobs
scontrol show job <jobid>   # full detail on one job
```

In the `ST` column, `R` means running and `PD` means pending. For a pending job, the `NODELIST(REASON)` column gives the reason: `Resources` means the GPUs you asked for are busy, and `Priority` means other jobs are ahead of you.

## See what is free

```bash
sinfo                       # partitions and node states
sinfo -N -l                 # one line per node, with detail
```

A node state of `idle` is free, `mix` is partly used, and `alloc` is fully allocated.

## Stop a job

```bash
scancel <jobid>             # cancel one job
scancel --me                # cancel all of your jobs
```

## After a job ends

```bash
sacct -j <jobid>                                   # what happened to a job
sacct -u <you> --starttime 2026-06-01              # your job history
sacct -j <jobid> --format=JobID,State,Elapsed,MaxRSS,ReqTRES
```

Values in the `State` column worth knowing:

| State | Meaning |
|---|---|
| `COMPLETED` | Finished cleanly (exit 0). |
| `TIMEOUT` | Reached the `--time` limit. Request more time on the next run. |
| `FAILED` | The program exited with a non-zero status. |
| `OUT_OF_MEMORY` | Used more memory than requested. Raise `--mem` or use less. |
| `CANCELLED` | Stopped by `scancel`, by you or by an admin. |

## Common options

| Option | Purpose |
|---|---|
| `--gres=gpu:1` | Request 1 GPU (use `:2` for both on a node). |
| `--time=HH:MM:SS` | Wall-clock limit. |
| `--partition=<name>` (`-p`) | Choose a partition. The default is `main`. |
| `--cpus-per-task=N` (`-c`) | Request N CPU cores. |
| `--mem=32G` | Request memory. |
| `--qos=expedite` | Use a granted deadline-priority window (ask in `#server`). |
| `--job-name=<name>` (`-J`) | Label your job. |

## Partitions

| Partition | Maximum time | Use it for |
|---|---|---|
| `main` (default) | 3 days | All normal work. |
| `debug` | 1 hour | Short tests with quick turnaround. |

## Need help?

Ask in `#server`. If you are on a deadline, ask there about a priority window.
