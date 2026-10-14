# FAQ

## Why did my job stop?

Run:

```bash
sacct -j <jobid> --format=JobID,State,Elapsed,MaxRSS,ReqTRES
```

- `TIMEOUT`: the job reached `--time`.
- `OUT_OF_MEMORY`: it exceeded requested memory.
- `FAILED`: the program exited with an error; inspect its output log.
- `CANCELLED`: the user or an administrator cancelled it.
- `PREEMPTED` or a requeued `scavenger` job: best-effort work yielded to higher-priority work.

## Why is my job pending?

Pending is normal. Run `squeue --me` and inspect the reason. `Resources` means the requested shape is busy; `Priority` means other work is ahead; `AssocGrpGRES` commonly means an account or course-holder GPU cap is in use. `squeue --me --start` is an estimate, not a guarantee.

Sprocket’s [Hub](/hub) adds current pressure, history, and evidence-qualified wait ranges.

## Can I SSH directly to a compute node?

Only while you hold an allocation on that node. Start with `salloc`, find the assigned node using `squeue --me`, and then SSH to it. Without an allocation, connect to `sprlab005` and submit work through Slurm.

## I need only CPUs. Do I still use Slurm?

Research accounts do:

```bash
salloc --cpus-per-task=8 --mem=32G --time=1:00:00
```

Course compute is GPU-budgeted and accepts CPU-only jobs, each billed as one GPU whatever CPU and memory it requests. A CPU-only course job runs outside the course GPU reservation and receives four CPUs if it asks for none. Substantial CPU-only work spends the assignment budget at the same rate as GPU work, so course staff should size the budget for the workflow they intend.

## Why is interactive research limited to an hour?

Interactive research runs on the high-turnover `debug` partition. Use it to develop and diagnose, then move unattended or longer work to `sbatch` on `main`. A course assignment has its own wall-time ceiling and budget; always request only the interactive time needed.

## What is my account type?

Sprocket’s Me and Course pages show what your account may do and which courses it belongs to. Sprocket records research access, course access, or both against one identity, so enrolling in a course adds course access to the account you already have. Graduate and undergraduate research accounts use research QoS, a course-only account uses assignment budgets, and an account holding both stays on research policy until it names a course assignment account. See [Accounts and access](accounts.md).

## What course account should I use?

Copy `--account=<assignment>` from Sprocket’s Budget page or run `mybudget`. When multiple assignments are open, the account is required. Do not guess a Slurm leaf account or copy another team’s value.

## How is course budget charged?

Requested GPUs × requested wall minutes is held when the job is submitted. After completion, actual elapsed GPU-minutes become consumed and unused held time becomes free again. Pending and running team jobs share the same holder pool. See [Course workflow](coursework.md).

## Why does my course budget look unavailable?

When queue or accounting evidence is missing or stale, Sprocket refuses the submission rather than running it without a limit. Wait briefly and refresh. If it persists, send Support the assignment, command, approximate time, and the source health it displays. Do not try to route around the course account.

## Can course assignments overlap?

Yes. Each open assignment has a separate budget and account. Select which one pays for the job. The two-GPU holder/user ceiling spans assignments, so overlapping windows do not multiply concurrency.

## My team changed. What happens to jobs, budget, and submissions?

Course staff decide it and will tell you what it means for your team. Minutes you have already used, time held by running jobs, and closed history stay with the holder they belong to. An open confirmation may need redoing by the team as it then stands.

## How do we submit course work?

Declare a full GitHub commit SHA, or a path inside your assigned course workspace, on the Submissions page. Course staff assign the repository; you supply one exact commit, and a branch or tag name is not accepted because what it points at can change. Sprocket verifies the commit you name, and copies a path into its own storage so later changes do not affect it. Path submissions receive an immediate structural result and, when staff set them up, submission check results. A revision the checks did not accept cannot be confirmed and does not replace an earlier accepted one; see [Course workflow](coursework.md#submission-checks). Review the exact revision; every required team member confirms it. Confirmation waits for a trusted count of zero active jobs, closes the assignment window, and discards remaining GPU-minutes once active jobs clear.

## How do I request a course extension or budget exception?

Ask course staff directly. They decide it and will tell you the outcome. There is no student-facing form for it, and Support cannot grant one. Do not send supporting documents through Support.

## Is my budget or the queue limiting me?

Compare two figures that `mybudget` and the **Budget** page both print: your free GPU-minutes, and your equal share of what the class can use before your deadline. If your free budget is the smaller number, your budget is the limit and a quieter hour will not give you more. If your equal share is the smaller number, the queue is the limit. Both surfaces report the capacity in force now and the next scheduled change, so submit into a window that has room.

## Where do files live?

Research homes are under `/spr-storage/home/<username>`, which is the same filesystem as `/home`. Course personal work is under `/course/<NetID>`, and team workspaces under `/course/groups/<course>/<team>`. Course workspace sizes come from the course’s published assignments, so they change when staff publish or amend one, and they never shrink below what the workspace already holds. `mybudget` prints the current sizes and your usage. See [Storage](storage.md) for the complete map.

## Can Slurm email me?

Yes:

```bash
sbatch --mail-type=BEGIN,END,FAIL,TIME_LIMIT --mail-user=<netid> train.sh
```

A bare NetID goes to `<netid>@illinois.edu`. The sending address is not monitored for replies.

## Can I use more GPUs?

Research limits depend on QoS: 4 for `normal` or `expedite`, 2 for `undergrad`, and up to 8 for requeueable `scavenger`. Each node has two GPUs, so requests above two span nodes and need suitable distributed code.

Course jobs use one node and at most two GPUs. The assignment may allow only one, and the requested cost must fit its budget.

## Can I use VS Code?

Yes. Attach VS Code Remote-SSH to `sprlab005`, edit persistent files there, and submit jobs from its terminal. Do not depend on an editor attached to a temporary compute allocation. See [VS Code](vscode.md).

## A login-host command feels throttled

Ordinary users are capped at about eight CPUs on `sprlab005` so one local process cannot starve everybody’s shell. Move heavy compilation, preprocessing, extraction, or training into a Slurm job.

## Where do I ask for help?

Use [Sprocket Support](/support) for technical failures and include exact commands, job IDs, times, and errors. Use [Resource requests](/requests) for reviewed research resources. Course assignment rules, grading, teams, and academic exceptions belong with course staff; ask them directly, and keep medical or other private details out of Support (see [extensions and exceptions](#how-do-i-request-a-course-extension-or-budget-exception)).
