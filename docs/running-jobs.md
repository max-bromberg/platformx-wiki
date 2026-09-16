# Running jobs

Connect to `sprlab005.csl.illinois.edu`, request resources through Slurm, and let Slurm place the work on a compute node. The login host is not a compute node.

!!! note "Research and course jobs route differently"
    The research examples below rely on the account’s default research policy. Course students must also name the assignment account shown in Sprocket; an account without research access cannot submit unbudgeted research work. See [Course workflow](coursework.md).

    If your account holds both research and course access, a job submitted without `--account` runs under research policy while an assignment is open, and Slurm prints one reminder naming the course account it was not charged to.

## Interactive or batch

- **Interactive (`salloc` or standalone `srun`)** is for development, debugging, and short tests where you need a shell.
- **Batch (`sbatch`)** is for unattended training, sweeps, data preparation, and anything you want to leave running.

For research accounts, interactive work is routed to `debug` and capped at one hour. Batch work runs on `main` and follows the selected research tier’s wall-time limit. Course work always runs through the course gate on `main`; its assignment supplies the wall-time ceiling and budget.

## Short research session

```bash
salloc --gres=gpu:1 --time=1:00:00
squeue --me
ssh <assigned-node>
```

Each node has two GPUs, so a single-node request can use one or two. An interactive research request above one hour is trimmed to one hour. An explicit research interactive request for `main` is rejected; submit a batch job instead.

For CPU-only research work, leave off the GPU request:

```bash
salloc --cpus-per-task=8 --mem=32G --time=1:00:00
```

Course allocations need an assignment account. A job may run with no GPU. It is billed as one GPU and receives four CPUs if it requests none:

```bash
salloc --account=<assignment> --gres=gpu:1 --time=0:30:00
```

## Submit a batch job

Create a script with resource requests at the top:

```bash
#!/bin/bash
#SBATCH --gres=gpu:1
#SBATCH --time=12:00:00
#SBATCH --job-name=train
#SBATCH --mail-type=END,FAIL
#SBATCH --mail-user=<netid>

srun python train.py
```

Then submit and monitor it:

```bash
sbatch train.sh
squeue --me
```

For course work, add the assignment account to the script or command:

```bash
#SBATCH --account=<assignment>
```

Use the exact value from Sprocket. When several assignments are open, omitting it is rejected because Slurm cannot know which budget to charge.

## Resource defaults

If a GPU job does not specify CPU or memory, each GPU receives 32 Slurm CPUs (16 physical cores) and about 384 GB of memory. Two GPUs receive 64 Slurm CPUs and about 768 GB. A CPU-only research job with no CPU request receives 4 Slurm CPUs and about 48 GB.

Request explicit values only when the job needs something different:

```bash
sbatch --gres=gpu:1 --cpus-per-task=16 --mem=128G --time=6:00:00 train.sh
```

Over-requesting reduces placement options and, for course work, holds more assignment budget.

## Connect to an allocated node

After `salloc` starts, `squeue --me` shows the assigned node. SSH to that node:

```bash
ssh sprc01
nvidia-smi
```

The session joins the allocation and inherits its GPU, CPU, memory, and time limits. SSH to a compute node without an allocation is declined. Start `tmux` on the login host before `salloc` if a brief network interruption would otherwise lose an interactive shell.

## Watch and diagnose

```bash
squeue --me
squeue --me --start
scontrol show job <jobid>
sacct -j <jobid> --format=JobID,State,Elapsed,MaxRSS,ReqTRES
```

Pending is normal. The reason field explains whether the job is waiting for resources, priority, a reservation, or an account/QoS limit. Start times are estimates and become less reliable farther into the future.

Use [Sprocket Queue](/queue) and [Usage](/usage) for the same information in one view.

## Stop or change work

```bash
scancel <jobid>
scancel --me
```

Cancel work you no longer need. For course jobs, changes that affect cost or placement require cancel-and-resubmit so the budget reservation stays correct. Harmless metadata and hold/release operations remain available.

## Email notifications

Use `--mail-type=BEGIN,END,FAIL,TIME_LIMIT` and `--mail-user=<netid>`. A bare NetID is delivered to `<netid>@illinois.edu`. Messages come from “SPRC Cluster”; replies are not monitored.

## Research deadline and best-effort tiers

Research users can request a temporary `expedite` grant for a genuine paper or demo deadline, or submit checkpointable batch work with `--qos=scavenger` to use idle capacity at the risk of requeue. These options do not apply to course jobs. See [Priority, capacity, and fair sharing](priority.md).
