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
3. **Scheduled class capacity:** course jobs attach to a Sprocket-owned reservation that bounds the class’s total GPU use for the current time window.

The initial capacity posture reserves up to two GPUs on weekdays from 07:00–18:00 Central and up to six GPUs on weekday nights and weekends. Administrators can adjust the schedule in Sprocket as course and research demand changes. Running jobs may cross a schedule boundary; the new cap controls additional starts rather than killing work already running.

Instructors set each checkpoint’s budget, GPU cap up to the two-GPU holder ceiling, wall-time ceiling, dates, and storage allowance. Those values appear on the student’s Checkpoints page and are more specific than this cluster-wide overview.

## Why reservations are used

The schedule preserves an intentional balance without making research and course users micromanage one another. During research-heavy daytime hours, most GPUs remain outside course capacity. Nights and weekends provide more course capacity. The posture can be tuned during the semester using observed contention.

Course jobs never use `debug`, and course-only students cannot escape into research policy. A dual-role researcher stays in research policy unless they explicitly select a course assignment account.

## Deadline research: `expedite`

For a genuine paper or demo deadline, submit a Resource request in Sprocket or use the lab’s established coordination channel. A grant is normally time-limited and automatically expires. Once approved:

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
