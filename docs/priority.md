# Priority, capacity, and fair sharing

The cluster serves research and supported courses on the same eight GPUs. Research is governed primarily by QoS and recent use. Course work is governed by assignment budgets plus scheduled course capacity. Neither policy automatically kills ordinary running research or course jobs; only best-effort `scavenger` work is automatically requeued.

## Research tiers

| Tier | Who | Queue priority | Batch wall time | GPUs per user |
| --- | --- | ---: | ---: | ---: |
| `expedite` | Researcher with an active deadline grant | 1000 | 24 hours | 4 |
| `normal` | Graduate researcher, default | 500 | 3 days | 4 |
| `undergrad` | Undergraduate researcher, default | 400 | 1 day | 2 |
| `scavenger` | Any researcher, explicit best effort | 1 | 1 day | 8 |

Research users do not choose `normal` or `undergrad`; the account supplies the correct default. `expedite` is selectable only during an approved grant. `scavenger` is opt-in and batch-only.

Interactive research sessions are routed to `debug`, which has a one-hour cap and schedules ahead of pending batch work without preempting a running job. An interactive `expedite` session is the deadline exception and runs on `main`; interactive `scavenger` is rejected.

## Research fair sharing

Within a research tier, recent GPU-weighted usage affects queue order. The decay half-life is three days: usage matters most immediately and fades continuously rather than resetting on a particular day. A person who has used less recently generally moves ahead of a peer in the same tier.

QoS priority dominates fair share and age, so tiers do not invert. A `normal` job remains ahead of `undergrad`, and both remain ahead of `scavenger`, regardless of recent usage.

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

## Deadline research: `expedite`

For a genuine paper or demo deadline, submit a Work request in Sprocket or use the lab’s established coordination channel. A grant is normally time-limited and automatically expires. Once approved:

```bash
sbatch --qos=expedite --gres=gpu:1 --time=24:00:00 train.sh
```

Expedite raises queue priority; it does not evict ordinary running work. It is not a course-budget extension.

## Best effort: `scavenger`

Restartable research work can use otherwise idle GPUs:

```bash
sbatch --qos=scavenger --gres=gpu:4 sweep.sh
```

Scavenger has the lowest priority and may be requeued when higher-priority work needs its resources. The job restarts from the beginning unless the program writes and restores checkpoints. It cannot run interactively and is not available to course jobs.

## Inspect queue position

```bash
squeue --me --start
sprio -l
sshare -l
```

Start times are estimates. Reservations, requested shape, higher-priority arrivals, and jobs ending earlier or later than requested can move them.
