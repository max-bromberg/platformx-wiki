# Systems Platform and Intelligence Lab

This guide covers the `sprc` GPU cluster for two supported audiences: research-lab members and students using the cluster through a supported course. Both use the same login host, compute nodes, Slurm scheduler, and Sprocket portal, but their accounts are governed differently.

## Where to start

- **Research account:** start with [Accounts and access](accounts.md), then [Running jobs](running-jobs.md). Your default research tier, recent usage, and optional deadline or best-effort tiers determine scheduling.
- **Course-only account:** start with [Course workflow](coursework.md). Your open assignment, GPU-minute budget, holder, deadline, and the course capacity schedule determine what you can submit.
- **Researcher enrolled in a course:** you keep the research workflow by default. Add the assignment account shown in Sprocket only when the job is course work. [Accounts and access](accounts.md#researchers-who-are-also-course-students) explains the boundary.

## The cluster

The cluster has four compute nodes, `sprc00` through `sprc03`. Each has two NVIDIA H100 NVL GPUs, 128 schedulable CPUs, and about 1.5 TB of memory. The shared total is eight GPUs.

Connect first to the login host:

```bash
ssh <username>@sprlab005.csl.illinois.edu
```

The login host is for editing, source control, file management, and submitting jobs. GPU work and heavy CPU work belong in Slurm jobs on the compute nodes.

```bash
salloc --gres=gpu:1 --time=1:00:00     # short interactive research session
sbatch --gres=gpu:1 --time=12:00:00 train.sh
squeue --me
```

Course students use the same commands plus the assignment account shown on Sprocket’s Course pages. Course-only accounts stay inside course policy automatically.

## What Sprocket provides

[Sprocket](/) is the cluster portal, and it serves this guide at [/wiki](/wiki). It provides live capacity and contention, queue and usage views, service status, news and change records, account and storage information, support and resource requests, and the course pages for assignments, budgets, and submissions. See [Using the Sprocket portal](portal.md) for the complete map.

## Shared expectations

- Request only the GPUs, CPUs, memory, and wall time the job needs.
- Use short interactive sessions to develop and debug; use `sbatch` for unattended work.
- Keep heavy work off the login host.
- Cancel work you no longer need and clean temporary data from shared fast storage.
- Treat displayed start times as estimates, not guarantees.
- Use Sprocket Support when something is broken. Course-policy and assignment questions go to course staff; research coordination can also use the lab’s `#server` channel.

## Find a topic

- [Accounts and access](accounts.md)
- [Running jobs](running-jobs.md)
- [Priority, capacity, and fair sharing](priority.md)
- [Course workflow](coursework.md)
- [Command reference](cheatsheet.md)
- [Storage](storage.md)
- [Workflows and file management](workflows.md)
- [VS Code](vscode.md)
- [Agentic coding helper](agentic-helper.md)
- [FAQ](faq.md)
