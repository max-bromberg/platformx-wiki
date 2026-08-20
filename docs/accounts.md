# Accounts and access

Your account type decides which Slurm policy applies. It does not change the hardware you use or the login host you connect to.

## Before connecting

Sprocket and the login host accept campus traffic. When off campus, connect to the university VPN before opening the portal or using SSH.

```bash
ssh <username>@sprlab005.csl.illinois.edu
```

For most people the cluster username is their NetID. The welcome message is authoritative if a different username was assigned. Use Sprocket’s **Me** page to review the account and manage SSH keys when that option is available.

## Account types

| Account | Intended use | Default scheduling behavior | Storage |
| --- | --- | --- | --- |
| Graduate researcher | Lab research | `normal` research tier | Shared `/spr-storage/home` plus research storage tiers |
| Undergraduate researcher | Lab research | `undergrad` research tier | Shared `/spr-storage/home` plus research storage tiers |
| Course-only student | Work for an enrolled course | `course` policy and an open assignment budget | Personal `/course/<NetID>` and, when assigned, a team workspace |
| Researcher enrolled in a course | Research and course work | Research by default; course only when an assignment account is selected | Existing research home, isolated personal coursework storage, and assigned team storage |

An undergraduate research account is not the same thing as a course-only student account. The former participates in research fair sharing under `undergrad`; the latter can run only work authorized by an open course assignment.

## Research accounts

Research jobs use the account and default QoS installed for the researcher. Graduate researchers default to `normal`; undergraduate researchers default to `undergrad`. Do not pass `--account` for ordinary research work.

Research accounts may opt into `scavenger` for restartable batch work. A time-limited `expedite` grant is available for genuine research deadlines. [Priority, capacity, and fair sharing](priority.md) has the exact limits.

## Course-only students

A course-only UID always enters the course gate. It cannot fall through to research QoS, the research `debug` partition, or unbudgeted CPU-only compute.

Selecting a course during roster invitation creates the student membership and individual holder together with the invite. During acceptance, a new course-only student must add an SSH public key; the account has no usable password. Sprocket then creates the locked Unix identity, private `/course/<NetID>` home, compute-node identities, SSH key, and Slurm holder before enabling portal access. A failure pauses the durable setup job for an administrator to retry instead of reporting a partially built account as ready.

Course compute requires all of the following:

1. an active course membership;
2. at least one open assignment for the student or team;
3. a fresh budget reading;
4. enough free GPU-minutes for requested GPUs multiplied by requested wall time; and
5. a request within the assignment’s GPU and wall-time ceilings.

If no assignment is open or no budget remains, compute access takes a no-budget posture: the account remains present, but no job can be submitted. A later assignment restores access through its own budget.

## Researchers who are also course students

Dual-role users remain research users unless they explicitly select course work. This keeps an ordinary research command out of course reservations and budgets:

Their existing UID and research passwd home remain authoritative. Course convergence adds a
separate `/course/<NetID>` coursework dataset for individual path snapshots; it does not replace
`$HOME` or create a second identity.

```bash
sbatch --gres=gpu:1 --time=4:00:00 research.sh
```

For course work, use the assignment account displayed on Sprocket:

```bash
sbatch --account=<assignment> --gres=gpu:1 --time=1:00:00 course.sh
```

Selecting a course account applies the full course posture: assignment budget, course QoS, course capacity reservation, one-node limit, and holder GPU cap. Selecting a research account from a course-only UID does not bypass the gate.

## If access does not work

- **Portal sign-in or SSH problem:** use [Sprocket Support](/support).
- **No course or checkpoint appears:** contact course staff; membership and publication are instructor-controlled.
- **Invalid account or account/partition combination:** include the exact command and error in a support report.
- **Resource or storage request:** use [Resource requests](/requests). Course budget exceptions follow the adjustment workflow on the Checkpoints page instead.
