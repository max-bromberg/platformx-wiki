# Priority, capacity, and fair sharing

The cluster serves research and supported courses on the same eight GPUs. Research is governed primarily by QoS and recent use. Course work is governed by assignment budgets plus scheduled course capacity. Neither policy automatically kills ordinary running research or course jobs; only best-effort `scavenger` work is automatically requeued.

## Research tiers

| Tier | Who | Queue priority | Batch wall time | GPUs per user | CPUs per user |
| --- | --- | ---: | ---: | ---: | ---: |
| `expedite` | Researcher with an active deadline grant | 1000 | 24 hours | 4 | 128 |
| `deadline6` | Researcher with an active deadline tier grant | 500 | 24 hours | 6 | 192 |
| `deadline8` | Researcher with an active deadline tier grant | 500 | 24 hours | 8 | 256 |
| `normal` | Graduate researcher, default | 500 | 3 days | 4 | 128 |
| `undergrad` | Undergraduate researcher, default | 400 | 1 day | 2 | 64 |
| `scavenger` | Any researcher, explicit best effort | 1 | 1 day | 8 | No limit |

Research users do not choose `normal` or `undergrad`; the account supplies the correct default. `expedite`, `deadline6`, and `deadline8` are selectable only during an approved grant. `scavenger` is opt-in and batch-only.

Interactive research sessions are routed to `debug`, which has a one-hour cap and schedules ahead of pending batch work without preempting a running job. An interactive `expedite` session is the deadline exception and runs on `main`; interactive `scavenger` is rejected.

## CPUs per user

Each tier except `scavenger` limits how many CPUs one person holds at once, across all of their jobs in that tier. CPUs inside GPU jobs count, and so do interactive sessions. The limit is 32 CPUs for each GPU the tier allows, which is what a GPU job gets by default, so jobs with default CPUs reach the GPU limit and the CPU limit together. Slurm counts logical CPUs: 128 CPUs is 64 physical cores.

A job that would take you past the limit waits in the queue with the reason `QOSMaxCpuPerUserLimit` and starts when enough of your jobs end. A single job that asks for more CPUs than the limit could never start, and in most cases it is rejected when you submit it. To use more CPUs than your tier allows, submit the extra work with `--qos=scavenger` (see [Best effort](#best-effort-scavenger) below). Scavenger jobs do not count toward your tier's limits.

An `salloc` or `srun` session cannot use `scavenger`, so if your batch jobs hold your whole limit, a new session waits until one of them ends. Leave room for interactive work, for example by limiting how many array tasks run at once with `--array=0-99%6`. A job submitted with `--exclusive` holds every CPU on its nodes (128 per node), so in `normal` one exclusive node uses your whole limit.

## Research fair sharing

Within a research tier, recent GPU-weighted usage affects queue order. The decay half-life is three days: usage matters most immediately and fades continuously rather than resetting on a particular day. A person who has used less recently generally moves ahead of a peer in the same tier.

Within a tier, a job also gets a small boost for each GPU it requests. Without it, a job that needs two GPUs on one node can keep being passed by single-GPU jobs that fit into whatever frees up first. The boost is small, so it settles near-ties between people with similar recent use rather than overriding fair share.

QoS priority dominates fair share, age, and job size, so tiers do not invert. A `normal` job remains ahead of `undergrad`, and both remain ahead of `scavenger`, regardless of recent usage.

Reserved GPUs count even when utilization is low. This is deliberate: a GPU held idle is unavailable to everybody else. Request accurate resources and cancel abandoned jobs.

## Course tier

Course jobs use the shared `course` QoS at priority 200. They do not compete through the research `normal`, `undergrad`, `expedite`, or `scavenger` controls. Instead, three limits work together:

1. **Assignment budget:** requested GPUs multiplied by wall time must fit the holder’s free GPU-minutes.
2. **Holder and user cap:** no holder or course user can occupy more than two GPUs across overlapping assignments.
3. **Scheduled class capacity:** a course job that requests a GPU attaches to a Sprocket-owned reservation that bounds the class’s total use for the current time window. A course job with no GPU runs outside the reservation and is still billed as one GPU.

The schedule holds separate capacities for weekday working hours and for weekday nights, early mornings, and weekends. It bounds the GPUs, CPUs, and memory the class can hold at once. Administrators adjust the window boundaries and the caps through the term as course and research demand changes, so this page does not print the current figures. `mybudget` and the portal's **Budget** page report the cap in force now and the next scheduled change. Running jobs may cross a schedule boundary; a new cap controls additional starts rather than stopping work already running.

Instructors set each assignment’s budget, GPU cap up to the two-GPU holder ceiling, wall-time ceiling, dates, and storage allowance, and they can amend a published assignment. Those values appear on the student’s Course pages and are more specific than this cluster-wide overview.

## Why reservations are used

The schedule separates research and course demand in advance, so the two groups do not have to coordinate with each other job by job. Most GPUs stay outside course capacity so that research work has somewhere to run. Administrators retune the window boundaries and the caps during the semester from observed contention, so check `mybudget` or the **Budget** page for the capacity in force now.

Course jobs never use `debug`, and an account without research access cannot reach research policy. An account that holds both research and course access stays in research policy unless it names a course assignment account.

## Deadline research: `expedite`, `deadline6`, and `deadline8`

For a genuine paper or demo deadline, submit a Work request in Sprocket or use the lab’s established coordination channel. A grant is time-limited and expires automatically. Sprocket emails you when the grant starts and when it ends, and the Work requests page shows the option to use while it is active.

A grant adds the QoS to your account. It does not change the QoS of jobs you submit without it, so name it on each job you submit during the grant:

```bash
sbatch --qos=expedite --gres=gpu:1 --time=24:00:00 train.sh
```

Expedite raises queue priority. The deadline tiers keep `normal` priority and raise the number of GPUs and CPUs you can hold at once: 6 GPUs and 192 CPUs with `deadline6`, 8 GPUs and 256 CPUs with `deadline8`. Each node has two GPUs, so a job that uses more than two spans nodes:

```bash
sbatch --qos=deadline6 --nodes=3 --gres=gpu:2 --time=24:00:00 train.sh
```

None of these evict ordinary running work, and none of them extend a course budget.

When the grant ends, running jobs continue. Jobs still waiting in the queue with the QoS will not start. Move each one to your default QoS with `scontrol update job=<jobid> qos=normal` (`undergrad` for an undergraduate account), or cancel and resubmit it.

## Best effort: `scavenger`

Restartable research work can use otherwise idle GPUs and CPUs beyond your tier's limits. Scavenger allows 8 GPUs per person and has no CPU limit:

```bash
sbatch --qos=scavenger --gres=gpu:4 sweep.sh
sbatch --qos=scavenger --cpus-per-task=64 --time=12:00:00 preprocess.sh
```

For a job array that needs more CPUs than your tier allows, keep the tasks that fit under your limit in your default tier and submit the rest as a second array with `--qos=scavenger`.

Scavenger has the lowest priority and may be requeued when higher-priority work needs its resources. The job restarts from the beginning unless the program writes and restores checkpoints. It cannot run interactively and is not available to course jobs.

## Inspect queue position

```bash
squeue --me --start
sprio -l
sshare -l
```

Start times are estimates. Reservations, requested shape, higher-priority arrivals, and jobs ending earlier or later than requested can move them.
