# Using the Sprocket portal

Sprocket is the portal for the cluster, and it serves this guide under **Wiki**. Public operational information is available without an account. Personal, course, lab, and administrative views appear after sign-in according to role.

## Cluster information

- **Home** summarizes GPU capacity, running and pending work, active users, recent utilization, and the overall contention index.
- **Queue** shows current jobs and the CPU, memory, and GPU each one holds. It explains whether GPU utilization is unavailable because a job is pending, CPU-only, or missing telemetry. The list can be filtered by whose job it is, whether it is coursework or research, or whether it holds a GPU, and sorted by job, user, class, state, CPU, memory, GPU, elapsed time, or time limit.
- **Usage** combines current pressure with seven-day activity and evidence-qualified wait ranges for comparable one- and two-GPU jobs. Forecasts are estimates, not reservations or guarantees.
- **Monitor** gives signed-in lab users detailed node, GPU, process, storage, and telemetry-source health. It shows what each node currently holds in CPU, memory, and GPU, and how much of that is coursework versus research, both per node and across the cluster.
- **Efficiency** ranks lab users once enough job and GPU evidence exists. It combines GPU activity, CPU and memory fit, and storage headroom. Use it to find waste in your own jobs; it is not a performance grade.

Across all of these, a figure Sprocket could not measure is shown as unavailable rather than as zero, and a chart draws an unmeasured interval as a gap. A stale reading is labelled stale.

## Your account

The **Me** page shows your identity and access, SSH keys when portal management is enabled, personal job and usage history, and storage usage with threshold guidance and links back into this guide. A value marked unavailable means Sprocket has no fresh evidence for it.

## Course workspace

Students receive a three-part course workspace:

- **Getting started** covers access, storage, jobs, and support.
- **Submissions** lists assignments and deadlines, the assigned GitHub repository, declared revisions, and final confirmation.
- **Budget** shows what is left on the open assignment, what the class can use before the deadline, and the next scheduled capacity change.

Deadlines on these pages are shown in the course timezone.

TAs and instructors also receive the Staff console for rosters, teams, assignment publication and amendment, enrolment changes, exceptions, and the list of holders with nothing submitted before a deadline.

See [Course workflow](coursework.md) before submitting course jobs.

## Operational communication

- **Service status** is the current health and maintenance view.
- **News** carries announcements.
- **Changes** records user-visible fixes and deployments.

When current behavior conflicts with an older message, check the latest service status and change entry, then report the discrepancy.

## Get help

- **Support** is for failures, questions, and course or infrastructure problems. Include the command, job ID, approximate time, and exact error when applicable.
- **Resource requests** is for research storage, project space, or another lasting operational resource that needs review.
- **Work requests** is for a temporary change to how your work is scheduled, such as a time-limited priority grant for a deadline. These expire on their own.
- Course assignment policy, grading, teams, and academic exceptions go to course staff directly. Sprocket has no student-facing form for them.

Do not attach medical or similarly sensitive evidence to a support report or a resource request. Give it to course staff by whatever route they specify.
