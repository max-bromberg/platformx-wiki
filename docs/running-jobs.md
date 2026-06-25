# Running jobs

You work from the login node, which you reach with `ssh <you>@sprlab005.csl.illinois.edu`. From there you request an allocation from SLURM and then connect to the compute node it assigns you. This page covers the common cases, and the [Command reference](cheatsheet.md) lists every command in brief.

## Request a GPU

`salloc` reserves resources and gives you an interactive allocation. Ask for the number of GPUs and the wall-clock time you need:

```bash
salloc --gres=gpu:1 --time=4:00:00     # one GPU for four hours
salloc --gres=gpu:2 --time=2:00:00     # both GPUs on a node, for two hours
```

Each node has two GPUs, so two is the most you can request on a single node. After the allocation is granted, `squeue --me` shows which node you received.

## Request CPUs only

Work that does not use a GPU goes through SLURM in the same way. Request cores and memory and leave off `--gres`:

```bash
salloc --cpus-per-task=8 --mem=32G --time=4:00:00
```

A job that requests a GPU receives a generous default share of cores and memory automatically, so you only need `--cpus-per-task` and `--mem` when you want something different from the default.

## Connect to your node

Once you hold an allocation, connect to the assigned node and work normally:

```bash
ssh <node>        # for example, sprc01
nvidia-smi        # the GPUs available to you
```

Your SSH session joins the allocation and is bound by the same limits, so your editors, conda environments, and containers all behave as they do today.

## Time limits

Every allocation has a wall-clock limit set by `--time` in `HH:MM:SS` form. When the time runs out, the allocation ends and the resources return to the pool. The `main` partition allows up to three days for a single job. Set `--time` to what you realistically need, and request more on your next run if you come up short. If a job ends before you expected, run `sacct -j <jobid>` and read the `State` column; `TIMEOUT` means the job reached its limit.

## Long runs

For a long interactive session, start `tmux` on the login node before you allocate, so the session survives a dropped connection or a sleeping laptop:

```bash
tmux                                    # start tmux on the login node
salloc --gres=gpu:1 --time=24:00:00     # hold the allocation
ssh <node>                              # work on the node
```

Detach from tmux with `Ctrl-b` then `d`, and reattach later with `tmux attach`.

## Batch jobs

From July 1, `sbatch` lets you submit a job and walk away. SLURM runs it when resources are free and writes its output to a file. Put your resource requests at the top of the script as `#SBATCH` lines:

```bash
#!/bin/bash
#SBATCH --gres=gpu:1
#SBATCH --time=12:00:00
#SBATCH --job-name=train
srun python train.py
```

Submit it with `sbatch train.sh` and follow its progress with `squeue --me`. Until soft enforcement begins on July 1, use `salloc` inside `tmux` for long runs.

## Deadlines

When you have a paper or demo deadline, the sysadmin team can raise your queue priority for a set window. Ask in `#server`. Once your account holds the `expedite` priority, add `--qos=expedite` to your request:

```bash
salloc --gres=gpu:1 --time=8:00:00 --qos=expedite
```

## During the optional period

Until soft enforcement begins on July 1, GPU isolation is not yet active, so `nvidia-smi` on a node lists all of its GPUs rather than only the ones you reserved. Please use only the GPUs your allocation granted you. Check the [dashboard](/dashboard/) or `squeue` to see what is already running on a node so that you do not overlap with someone else. From July 1 onward, an allocation sees only the GPUs it reserved.
