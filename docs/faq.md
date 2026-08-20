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

Sprocket’s [Queue](/queue) and [Usage](/usage) pages add current pressure, history, and evidence-qualified wait ranges.

## Can I SSH directly to a compute node?

Only while you hold an allocation on that node. Start with `salloc`, find the assigned node using `squeue --me`, and then SSH to it. Without an allocation, connect to `sprlab005` and submit work through Slurm.

## I need only CPUs. Do I still use Slurm?

Research accounts do:

```bash
salloc --cpus-per-task=8 --mem=32G --time=1:00:00
```

Course-only compute is GPU-budgeted and rejects CPU-only jobs. If a course assignment needs substantial CPU-only work, course staff must provide the intended workflow.

## Why is interactive research limited to an hour?

Interactive research runs on the high-turnover `debug` partition. Use it to develop and diagnose, then move unattended or longer work to `sbatch` on `main`. A course assignment has its own wall-time ceiling and budget; always request only the interactive time needed.

## What is my account type?

Sprocket’s Me and Course pages show the available role and course membership. Graduate and undergraduate research accounts use research QoS. A course-only account uses assignment budgets. A researcher enrolled in a course remains research by default and selects a course assignment account only for course work. See [Accounts and access](accounts.md).

## What course account should I use?

Copy `--account=<assignment>` from Sprocket’s Checkpoints page or run `mybudget`. When multiple assignments are open, the account is required. Do not guess a Slurm leaf account or copy another team’s value.

## How is course budget charged?

Requested GPUs × requested wall minutes is held when the job is submitted. After completion, actual elapsed GPU-minutes become consumed and unused held time becomes free again. Pending and running team jobs share the same holder pool. See [Course workflow](coursework.md).

## Why does my course budget look unavailable?

Sprocket intentionally fails closed when queue or accounting evidence is missing or stale. Wait briefly and refresh. If it persists, send Support the assignment, command, approximate time, and displayed source health. Do not attempt to route around the course account.

## Can course assignments overlap?

Yes. Each open assignment has a separate budget and account. Select which one pays for the job. The two-GPU holder/user ceiling spans assignments, so overlapping windows do not multiply concurrency.

## My team changed. What happens to jobs, budget, and submissions?

An instructor previews and applies the exception in Sprocket. Consumed minutes, existing job holds, closed history, and artifacts remain with their original holder. Staff explicitly choose what happens to free budget. Open confirmations affected by membership may be invalidated so the resulting team confirms a coherent revision.

## How do we submit course work?

Declare an allowed GitHub revision or a path inside your assigned course workspace in Checkpoints. Sprocket resolves GitHub input to an immutable commit and snapshots path input as an immutable ZFS revision. Path submissions receive an immediate structural result and, when configured by staff, an instructor verifier result. Review the exact revision; every required team member confirms it. Confirmation waits for trusted zero active jobs and closes the assignment window when final.

## How do I request a course extension or budget exception?

Use **Request an adjustment** on the relevant checkpoint. Attach the required private evidence there, not to ordinary Support. Course staff decide the academic exception and Sprocket records the resulting window and budget change.

## Where do files live?

Research homes are under `/spr-storage/home/<username>` with the account-type quota shown in Sprocket. Course-only personal work is under `/course/<NetID>` with a standard 10 GiB baseline; team workspaces normally provide 50 GiB under `/course/groups/<course>/<team>`. Published checkpoint policy can grow course allocations, and they do not shrink automatically. See [Storage](storage.md) for the complete map.

## Can Slurm email me?

Yes:

```bash
sbatch --mail-type=BEGIN,END,FAIL,TIME_LIMIT --mail-user=<netid> train.sh
```

A bare NetID goes to `<netid>@illinois.edu`. The sending address is not monitored for replies.

## Can I use more GPUs?

Research limits depend on QoS: 4 for `normal` or `expedite`, 2 for `undergrad`, and up to 8 for requeueable `scavenger`. Each node has two GPUs, so requests above two span nodes and need suitable distributed code.

Course jobs use one node and at most two GPUs. The checkpoint may allow only one, and the requested cost must fit the assignment budget.

## Can I use VS Code?

Yes. Attach VS Code Remote-SSH to `sprlab005`, edit persistent files there, and submit jobs from its terminal. Do not depend on an editor attached to a temporary compute allocation. See [VS Code](vscode.md).

## A login-host command feels throttled

Ordinary users are capped at about eight CPUs on `sprlab005` so one local process cannot starve everybody’s shell. Move heavy compilation, preprocessing, extraction, or training into a Slurm job.

## Where do I ask for help?

Use [Sprocket Support](/support) for technical failures and include exact commands, job IDs, times, and errors. Use [Resource requests](/requests) for reviewed research resources. Course assignment rules, grading, teams, and academic exceptions belong with course staff and the course adjustment flow.
