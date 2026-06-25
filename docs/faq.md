# FAQ

Short answers to common questions. If you are still stuck, ask in `#server`.

### My job was killed. Why?

Run `sacct -j <jobid>` and read the `State` column.

-   **`TIMEOUT`** means you reached the `--time` limit. Request more time on the next run, up to three days in `main`.
-   **`OUT_OF_MEMORY`** means the job used more memory than it requested. Add `--mem=64G`, or whatever you need, or reduce the batch size.
-   **`FAILED`** means your program exited with an error. Check its output log.
-   **`CANCELLED`** means someone ran `scancel`. If it was not you, an admin may have cancelled it, which is rare and always explained.

### Do I still have to do this? I used to just SSH in.

SLURM is optional until June 30, and direct SSH continues to work through soft enforcement on July 1, so you can keep your current workflow for now. What begins on July 1 is that work you run inside an allocation gains time limits, fair-share scheduling, and, for GPU jobs, GPU isolation. At hard enforcement, on a date announced ahead of time, an allocation becomes required to use a compute node. Trying the new workflow now, while both still work, means no surprises later. See [About the transition](slurm-transition.md).

### I only need CPU, not a GPU. Does this apply to me?

Yes. SLURM schedules the whole compute node, not only its GPUs, so CPU-only work follows the same path. Leave off `--gres` and request cores and memory instead:

```bash
salloc --cpus-per-task=8 --mem=32G --time=4:00:00
```

You still receive a time limit and the same fair-share scheduling, and at hard enforcement you still need an allocation to use a node. The only GPU-specific piece is GPU isolation, which has nothing to apply to a job that requested no GPU.

### What is the June 30 deadline about? Do I need to move my data?

Yes, by Tuesday, June 30. The per-node home directories and the `/mnt/data1` data drive on the compute nodes are erased after June 30, ahead of soft enforcement on July 1. Copy anything you want to keep to the shared storage, then mark your name in the migration sheet. The full steps are on the [Migrate your data](storage-migration.md) page. Shared storage has a 50 GB quota per user; if you need more, ask in `#server`.

### How do I ask for more than one GPU?

`--gres=gpu:2` gives you both GPUs on a node. Each node has two GPUs, so two is the maximum on a single node. Multi-node GPU jobs are possible but uncommon here, so ask if you need one.

### How do I know what is free before I ask?

```bash
sinfo            # idle is free, mix is partly used, alloc is full
squeue           # what is running and queued
```

The [live cluster status dashboard](/dashboard/) gives the same view of free GPUs, queue depth, and contention.

### My job is stuck in `PD` (pending). What does that mean?

Pending is normal. Run `squeue --me` and read the reason in parentheses.

-   **`Resources`** means the GPUs you asked for are busy, and you are next in line when they free up.
-   **`Priority`** means other jobs are ahead of you in the queue.
-   **`QOSMaxWallDurationPerJobLimit`** means you asked for more time than allowed. Resubmit with a shorter `--time`.

`squeue --me --start` shows an estimated start time.

### Can I submit a job and come back later?

From July 1, yes. Batch submission with `sbatch` needs every home directory on the shared storage, so it switches on when soft enforcement begins, once the June 30 migration is complete. Until then, use `salloc` for everything. For a long run, hold the allocation in `tmux` so it survives a sleeping laptop or a dropped connection: run `salloc`, then `ssh <node>` from inside the tmux session, detach with `Ctrl-b d`, and reattach later with `tmux attach`.

### Can I still use VS Code Remote-SSH?

Yes. Allocate a node with `salloc`, then point VS Code Remote-SSH at that node. See the [VS Code](vscode.md) page.

### My time limit is too short for my training run.

You have three options.

1.  Ask for more time up front, for example `--time=24:00:00`, up to three days in `main`.
2.  Submit the run as a batch job with `sbatch` so it runs unattended.
3.  Add checkpointing to your code so a job can resume where it left off. The sysadmin team is happy to help you set this up.

### I have a paper or demo deadline. Can I get priority?

Yes. Ask in `#server` and the sysadmin team can raise your queue priority for a set window so your jobs start sooner. This is a normal, supported part of how the cluster runs.

### Does any of this change my login, files, or environment?

No. You keep the same username and password, the same conda environments, containers, and tools, and the same home directory path and contents. Your home now lives on shared storage, which is why you need to [migrate your data](storage-migration.md) off the old per-node disks by June 30. The only new step is requesting resources before you use a node, whether a GPU or just cores and memory.

### Where did my GPU go when my allocation ended?

It returned to the shared pool for the next person. Returning resources when your time is up is what keeps a free GPU available when you need one, rather than reserved and idle by someone who has walked away.

### Still stuck?

Ask in `#server`. No question is too basic.
