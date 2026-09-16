# Course workflow

Course compute is organized around open assignments in Sprocket. Each assignment has its own Slurm account, availability window, GPU-minute budget, GPU cap, wall-time ceiling, storage allowance, submission rules, and grading arrangement. Instructors configure those values in the portal.

## 1. Open the course workspace

Sign in to Sprocket and open **Course → Getting started**. The course workspace has three sections:

- **Getting started** covers access, storage, jobs, and support.
- **Submissions** lists published assignments and deadlines, your assigned GitHub repository, declared revisions, and final confirmation.
- **Budget** shows what is left on the open assignment and what the class can use before your deadline.

Between them they give you every published assignment and deadline, every assignment currently open to you, the exact `--account=<assignment>` value to use, the granted, used, held, and free GPU-minutes, whether the holder is you or a team, how fresh the budget reading is, and the effective close time.

Deadlines are shown in the course timezone, labelled as such, rather than in your device timezone. A deadline extended for you individually is shown as your own deadline.

If more than one assignment is open, choose one explicitly with `--account`. Even with only one open assignment, copying the displayed account makes scripts unambiguous and reproducible.

## 2. Understand the budget

One GPU for one minute costs one GPU-minute. Two GPUs for one minute cost two GPU-minutes.

```text
job reservation = requested wall minutes * requested GPUs
free budget     = total budget - consumed minutes - held minutes
```

Pending and running jobs hold their full requested cost so several submissions cannot overspend the same budget. When a job completes, accounting replaces that hold with its actual elapsed GPU-time. Ask for a realistic wall time: an unnecessarily large request can consume all free headroom while the job is queued or running.

Budgets belong to the assignment holder. An individual assignment has a personal pool; a team assignment has one pool shared by every member. Team members’ jobs all draw from that same pool.

Budgets do not roll into another assignment. Overlapping assignments remain separate pools, while the two-GPU holder ceiling spans them so opening a second assignment does not double concurrency.

Check from either place:

```bash
mybudget                       # shell view
```

or the portal's **Budget** page. Both report the same figures for the open assignment: the granted total, what you have already used, what your queued and running jobs are holding, and what is free to submit right now.

Both also report the class capacity between now and your deadline. That is the number of GPU-minutes the whole class can use in that window under the current capacity schedule, the number of holders who could compete for them, and your equal share if all of them do. The share assumes every holder competes evenly. Sprocket does not predict what other holders will actually do.

Read the two together. If your free budget is below your equal share, your own budget is the tighter limit, and waiting for a quieter hour does not give you more of it. If your equal share is below your free budget, the queue is the tighter limit, and submitting into the larger capacity windows is how you spend what you hold.

`mybudget` also reports your workspace and team disk use, how many course GPUs are busy now, and the next scheduled capacity change. When Sprocket cannot be reached, it falls back to a local cache and labels the figures as cached.

If accounting is missing or stale, course submission is refused rather than run without a limit. Both report the reading as unavailable instead of showing zero.

## 3. Run course work

Course jobs run on `main` and use one compute node. A job that requests a GPU attaches to the active course capacity reservation. A job with no GPU runs outside that reservation and is billed as one GPU whatever CPUs and memory it requests. Do not request `debug`, `normal`, `undergrad`, `expedite`, or `scavenger` for course work.

```bash
# short interactive test
salloc --account=<assignment> --gres=gpu:1 --time=0:30:00

# unattended job
sbatch --account=<assignment> --gres=gpu:1 --time=1:00:00 train.sh
```

The cluster-wide course ceiling is two GPUs per holder and two GPUs per course user. An assignment may set a lower GPU or wall-time limit. Each job is limited to one node, 64 CPUs, and 512 GB of memory. Job arrays are not accepted because every array element would need an independent budget reservation.

Instructors can amend a published assignment. Its name, start, deadline, GPU-minute total, GPUs per job, wall-time ceiling, storage size, grader, and allowed submission sources can all be corrected after publication, and the portal shows staff what an amendment takes away from students already working. The assignment key, its sequence, whether it is held by students or teams, and its path verifier are fixed once published.

After submission, resource-changing edits are rejected because they would invalidate the reserved cost. Cancel and resubmit to change the account, QoS, partition, reservation, GPUs, CPUs, memory, nodes, or wall time. Naming, holding, and releasing a job remain available.

## 4. Work with a team

When an assignment uses team holders, every team member shares its compute budget and team workspace. Coordinate before launching large jobs: another member’s pending job can hold budget even before it starts.

Course staff can form teams for part of a roster and add the rest later, so your team may appear before every classmate has been placed. A team formed after an assignment is published joins that assignment straight away.

Instructors can correct team membership for drops, reassignments, or other exceptions. A reviewed change can keep, reset, explicitly set, remove, or transfer free budget. Consumed time, time held by existing jobs, closed history, and existing artifacts never move silently. An open submission that no longer matches the team may require a new declaration and confirmation.

## 5. Store course files

- Course-only students receive a private `/course/<NetID>` workspace.
- Team assignments can provide a shared workspace under `/course/groups/<course>/<team>`.
- A researcher enrolled in a course keeps their existing account and research home. Sprocket also
  provides a separate `/course/<NetID>` coursework workspace so individual path revisions can be
  snapshotted without touching the research home, plus the team workspace for team work.

Each workspace carries an enforced quota. Its size comes from the course's published assignments, so it changes when instructors publish or amend one, and reconciliation never lowers a quota below what the workspace already holds. `mybudget` and Sprocket report the current size and your usage; this page does not print a figure that would go stale. Immutable path revisions count against the same boundary. Obsolete draft revisions are pruned while the assignment is open and final revisions are retained. Keep another durable copy of important work rather than relying on temporary working storage.

## 6. Declare and confirm a submission

On Submissions, choose a source allowed by the instructor:

- For **Course workspace path**, enter an absolute path inside the workspace shown by Sprocket. The portal snapshots that path, rejects links, special files, and empty or oversized trees, and immediately shows whether the immutable revision is valid. An assignment may also run an instructor-provided Python verifier over the snapshot. That verifier runs for at most ten seconds, with no network access, a 256 MiB memory cap, and no captured output. It runs under your own cluster identity, with the access you have, and it can read every file in the snapshot of your workspace, including files outside the path you selected.
- For **GitHub**, course staff assign one private organization repository per holder. Enter one full commit SHA. Sprocket verifies the repository and confirms that exact commit. A branch or tag name is not accepted, because the commit it points at can change after you declare it.

An invalid declaration never replaces the current valid attempt. Correct the path or the commit and create another revision. Review the selected source, validity result, and immutable revision before confirming.

For team submissions, every required team member confirms the same revision. Final confirmation is blocked while jobs are active or job evidence is stale. Once confirmation succeeds, the holder’s assignment window closes immediately and that revision becomes the final submission. If nobody confirms early, the deadline worker selects the latest eligible declaration or records no submission when the effective close time passes. A declaration is eligible on the time you submitted it, not the time validation finished. If a revision is still being validated when the close time passes, Sprocket waits for it before deciding, so a slow validation does not cost you the submission.

Confirmation is consequential. Stop or finish relevant jobs, verify outputs are pushed, confirm the immutable revision, and only then acknowledge the finalization warning. Remaining GPU-minutes are discarded once active jobs clear.

An assignment is either graded by hand or linked to a grader. Where one is linked, Sprocket queues a grading run as soon as the submission is finalized, whether that happens through your confirmation or at the deadline. The Submissions page shows the recorded outcome when one exists. Grading runs use their own Slurm account and limits, so they do not spend your assignment budget.

## 7. Extensions and exceptions

Ask course staff when an exception needs additional GPU-minutes or a later deadline. They collect whatever supporting documentation they require and decide the exception. Sprocket has no student-facing form for it, so do not send supporting documents through Support.

Course staff then record the decision in Sprocket by correcting the holder’s open or close time, or the holder’s budget. Reopening a finalized assignment preserves the earlier outcome and grading record as superseded history rather than erasing them. While the reopened window is open you declare and confirm again as normal, and your earlier submission stays in the record.

For a technical failure, use [Sprocket Support](/support). Assignment rules, grading, teams, and deadline decisions belong with course staff.
