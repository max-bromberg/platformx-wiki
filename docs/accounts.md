# Accounts and access

What your account may do decides which Slurm policy applies. It does not change the hardware you use or the login host you connect to.

Sprocket records access as capabilities held by one cluster identity: research access at a tier, course access, or both. One person is one identity. Enrolling a researcher in a course adds course access to the account they already have, and it does not create a second account or a second home directory.

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
| Researcher enrolled in a course | Research and course work | Research by default; course only when an assignment account is selected | Existing research home, a separate personal coursework workspace, and assigned team storage |

An undergraduate research account is not the same thing as a course-only student account. The former participates in research fair sharing under `undergrad`; the latter can run only work authorized by an open course assignment.

## Research accounts

Research jobs use the account and default QoS installed for the researcher. Graduate researchers default to `normal`; undergraduate researchers default to `undergrad`. Do not pass `--account` for ordinary research work.

Research accounts may opt into `scavenger` for restartable batch work. A time-limited `expedite` grant is available for genuine research deadlines. [Priority, capacity, and fair sharing](priority.md) has the exact limits.

## Course-only students

An account with course access and no research access always enters the course gate. It cannot fall through to research QoS, the research `debug` partition, or unbudgeted compute of any kind. A course job with no GPU is admitted and billed as one GPU.

Selecting a course during roster invitation creates the student membership and individual holder together with the invite. During acceptance, a new course-only student must add an SSH public key; the account has no usable password. Sprocket then creates the locked Unix identity, private `/course/<NetID>` home, compute-node identities, SSH key, and Slurm holder before enabling portal access. A failure pauses the durable setup job for an administrator to retry instead of reporting a partially built account as ready.

Course staff can end an enrolment. The membership is archived with a recorded reason, the team seat ends, and the course gate stops accepting new course jobs at its next refresh. Submitted revisions, recorded outcomes, and coursework storage are kept. Re-enrolling is a cluster administrator’s action.

Course compute requires all of the following:

1. an active course membership;
2. at least one open assignment for the student or team;
3. a fresh budget reading;
4. enough free GPU-minutes for requested GPUs multiplied by requested wall time; and
5. a request within the assignment’s GPU and wall-time ceilings.

If no assignment is open or no budget remains, the account stays in place and no course job can be submitted. A later assignment restores access through its own budget.

## Researchers who are also course students

An account that holds both research and course access runs research policy unless it names a course account. That keeps an ordinary research command out of course reservations and budgets:

```bash
sbatch --gres=gpu:1 --time=4:00:00 research.sh
```

The account, its home directory, and its research tier stay as they are. Course access adds a
separate `/course/<NetID>` coursework workspace for individual path submissions. It does not replace
`$HOME` and it does not create a second account.

For course work, use the assignment account displayed on Sprocket:

```bash
sbatch --account=<assignment> --gres=gpu:1 --time=1:00:00 course.sh
```

Naming a course account applies every course rule at once: the assignment budget, course QoS, the course capacity reservation when the class has one, the one-node limit, and the holder GPU cap. An account without research access cannot reach research policy by naming a research account.

When such an account submits without `--account` while a course assignment is open, the job runs under research policy and Slurm prints one reminder naming the assignment account it was not charged to. The job is already submitted; nothing is rejected.

## If access does not work

- **Portal sign-in or SSH problem:** use [Sprocket Support](/support).
- **No course or assignment appears:** contact course staff. Membership and publication are instructor-controlled.
- **Invalid account or account/partition combination:** include the exact command and error in a support report.
- **Resource or storage request:** use [Resource requests](/requests). Course budget and deadline exceptions go to course staff instead; Sprocket has no student-facing form for them.
