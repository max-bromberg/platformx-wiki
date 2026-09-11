# Command reference

Use these commands from `sprlab005`. See [Running jobs](running-jobs.md) for the workflow and [Course workflow](coursework.md) for assignment budgets.

## Interactive work

Research account:

```bash
salloc --gres=gpu:1 --time=1:00:00
salloc --cpus-per-task=8 --mem=32G --time=1:00:00
```

Course work:

```bash
salloc --account=<assignment> --gres=gpu:1 --time=0:30:00
mybudget
```

Research interactive work routes to `debug` and is capped at one hour. Course work routes through its assignment on `main`; the assignment controls the maximum, and a job with no GPU is billed as one GPU.

After the allocation starts:

```bash
squeue --me
ssh <assigned-node>
nvidia-smi
```

## Batch work

```bash
sbatch --gres=gpu:1 --time=12:00:00 train.sh
sbatch --account=<assignment> --gres=gpu:1 --time=1:00:00 course.sh
```

A batch script can carry the options itself:

```bash
#!/bin/bash
#SBATCH --account=<assignment>     # course only; omit for research
#SBATCH --gres=gpu:1
#SBATCH --time=1:00:00
#SBATCH --job-name=train
#SBATCH --mail-type=END,FAIL
#SBATCH --mail-user=<netid>
srun python train.py
```

## Watch the cluster

```bash
squeue --me
squeue
squeue --me --start
sinfo
sinfo -N -l
scontrol show job <jobid>
```

Common pending reasons:

| Reason | Meaning |
| --- | --- |
| `Resources` | The requested shape is not free. |
| `Priority` | Other pending work is ahead. |
| `AssocGrpGRES` | An account or holder GPU cap is already in use. For a course job this is your holder cap or the whole class’s cap. |
| `QOSMaxWallDurationPerJobLimit` | The requested time exceeds the selected tier. |
| `Reservation` or `ReqNodeNotAvail` | The job is waiting on an applicable capacity window or node. |

Sprocket’s [Queue](/queue) and [Usage](/usage) pages show the same queue with the context that explains it.

## Stop jobs

```bash
scancel <jobid>
scancel --me
```

## Inspect completed work

```bash
sacct -j <jobid>
sacct -u <username> --starttime 2026-08-01
sacct -j <jobid> --format=JobID,State,Elapsed,MaxRSS,ReqTRES,Account
```

| State | Meaning |
| --- | --- |
| `COMPLETED` | Program exited successfully. |
| `TIMEOUT` | Job reached its requested wall time. |
| `FAILED` | Program exited non-zero. |
| `OUT_OF_MEMORY` | Job exceeded requested memory. |
| `CANCELLED` | User or an administrator cancelled the job. |
| `PREEMPTED` | Best-effort work yielded to higher-priority work. |

## Common options

| Option | Purpose |
| --- | --- |
| `--gres=gpu:N` | Request whole GPUs. Each node has two. |
| `--time=HH:MM:SS` | Maximum wall time. Request what the job realistically needs. |
| `--cpus-per-task=N` or `-c N` | Request Slurm CPUs. |
| `--mem=32G` | Request job memory. |
| `--account=<assignment>` | Charge course work to the assignment shown in Sprocket. Do not use for ordinary research. |
| `--qos=scavenger` | Restartable, lowest-priority research batch work. |
| `--qos=expedite` | Research deadline work during an approved grant. |
| `--mail-type=END,FAIL` | Select email events. Also accepts `BEGIN`, `TIME_LIMIT`, and `ALL`. |
| `--mail-user=<netid>` | Deliver to the university address for a bare NetID. |
| `--job-name=<name>` or `-J <name>` | Give the job a readable name. |

Do not normally set a partition. Slurm routes research interactive work to `debug`, research batch to `main`, and course work to the appropriate course reservation on `main`.

## Course-specific constraints

- Use the assignment account shown on Sprocket’s Course pages; it is required when assignments overlap.
- GPU-minute cost is requested GPUs × requested minutes. A job with no GPU is billed as one GPU, whatever CPU and memory it asks for.
- Job arrays are rejected. A CPU-only job is accepted, gets four CPUs if it asks for none, and runs outside the course GPU reservation.
- Course jobs use one node and at most two GPUs, 64 CPUs, and 512 GB; an assignment may be stricter.
- Cancel and resubmit to change resource or placement fields on a queued course job.

## Need help?

Use [Sprocket Support](/support) and include the command, job ID, time, and exact error. Research resource requests go through [Resource requests](/requests). Course budget and deadline exceptions go to course staff.
