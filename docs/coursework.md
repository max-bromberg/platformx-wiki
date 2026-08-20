# Course workflow

Course compute is organized around open assignments in Sprocket. Each assignment has its own Slurm account, availability window, GPU-minute budget, GPU cap, wall-time ceiling, storage allowance, and submission rules. Instructors configure those values in the portal.

## 1. Open the course workspace

Sign in to Sprocket and open **Course → Getting started**, then **Course → Checkpoints**. The workspace shows:

- every published checkpoint and deadline;
- every assignment currently open to you;
- the exact `--account=<assignment>` value to use;
- total, consumed, held, and free GPU-minutes;
- whether the holder is you or a team;
- budget freshness and the effective close time; and
- declared and confirmed submission revisions.

If more than one assignment is open, choose one explicitly with `--account`. Even with only one open assignment, copying the displayed account makes scripts unambiguous and reproducible.

## 2. Understand the budget

One GPU for one minute costs one GPU-minute. Two GPUs for one minute cost two GPU-minutes.

```text
job reservation = requested wall minutes × requested GPUs
free budget     = total budget − consumed minutes − held minutes
```

Pending and running jobs hold their full requested cost so several submissions cannot overspend the same budget. When a job completes, accounting replaces that hold with its actual elapsed GPU-time. Ask for a realistic wall time: an unnecessarily large request can consume all free headroom while the job is queued or running.

Budgets belong to the assignment holder. An individual assignment has a personal pool; a team assignment has one pool shared by every member. Team members’ jobs all draw from that same pool.

Budgets do not roll into another assignment. Overlapping assignments remain separate pools, while the two-GPU holder ceiling spans them so opening a second assignment does not double concurrency.

Check from either surface:

```bash
mybudget                       # shell view
```

or use the Checkpoints page. If accounting is missing or stale, course submission fails closed rather than running without a limit.

## 3. Run course work

Course jobs require at least one GPU, run on `main`, use one compute node, and are attached to the active course capacity reservation. Do not request `debug`, `normal`, `undergrad`, `expedite`, or `scavenger` for course work.

```bash
# short interactive test
salloc --account=<assignment> --gres=gpu:1 --time=0:30:00

# unattended job
sbatch --account=<assignment> --gres=gpu:1 --time=1:00:00 train.sh
```

The cluster-wide course ceiling is two GPUs per holder and two GPUs per course user. A checkpoint may set a lower GPU or wall-time limit. Each job is limited to one node, 64 CPUs, and 512 GB of memory. Job arrays are not accepted because every array element would need an independent budget reservation.

After submission, resource-changing edits are rejected because they would invalidate the reserved cost. Cancel and resubmit to change the account, QoS, partition, reservation, GPUs, CPUs, memory, nodes, or wall time. Naming, holding, and releasing a job remain available.

## 4. Work with a team

When a checkpoint uses team holders, every team member shares its compute budget and team workspace. Coordinate before launching large jobs: another member’s pending job can hold budget even before it starts.

Instructors can correct team membership for drops, reassignments, or other exceptions. A reviewed change can keep, reset, explicitly set, remove, or transfer free budget. Consumed time, time held by existing jobs, closed history, and existing artifacts never move silently. An open submission that no longer matches the team may require a new declaration and confirmation.

## 5. Store course files

- Course-only students receive a private `/course/<NetID>` workspace with a 10 GB quota.
- Team checkpoints can provide a shared 50 GB workspace under `/course/groups/<course>/<team>`.
- A researcher enrolled in a course keeps the existing research UID and passwd home. Sprocket also
  provides an isolated `/course/<NetID>` personal coursework workspace so individual path revisions
  can be snapshotted without touching the research home; the team workspace is added for team work.

Exact checkpoint storage allowances may be higher and never reduce a live dataset below its existing usage. Immutable path revisions count against the same storage boundary; obsolete draft revisions are pruned while open and final revisions are retained. Keep another durable copy of important work rather than relying on temporary working storage.

## 6. Declare and confirm a submission

On Checkpoints, choose a source allowed by the instructor:

- For **Course workspace path**, enter an absolute path inside the workspace shown by Sprocket. The portal snapshots that path, rejects links, special files, empty or oversized trees, and immediately shows whether the immutable revision is valid. A checkpoint may also run an instructor-provided Python verifier in a short-lived, read-only, no-network sandbox.
- For **GitHub**, enter an `owner/repository` and a branch, tag, or commit. Sprocket resolves it server-side to an immutable commit.

An invalid declaration never replaces the current valid attempt. Correct the path or repository and create another revision. Review the selected source, validity result, and immutable revision before confirming.

For team submissions, every required team member confirms the same revision. Final confirmation is blocked while jobs are active or job evidence is stale. Once confirmation succeeds, the holder’s assignment window closes immediately and that revision becomes the final submission. If nobody confirms early, the deadline worker selects the latest eligible declaration or records no submission when the effective close time passes.

Confirmation is consequential: stop or finish relevant jobs, verify outputs are pushed, confirm the immutable revision, and only then acknowledge the finalization warning.

## 7. Extensions and exceptions

Use **Request an adjustment** on the checkpoint when an exception needs additional GPU-minutes and a later expiry. The form requires an explanation and supporting PDF, JPEG, or PNG no larger than 5 MiB. Evidence is private to the requester and authorized course staff, and downloads are audited.

Course staff may approve or deny the request and may correct a holder’s open or close time. Reopening a finalized checkpoint preserves the earlier outcome and grading record as superseded history; it does not erase them.

For a technical failure, use [Sprocket Support](/support). Assignment rules, grading, teams, and deadline decisions belong with course staff.
