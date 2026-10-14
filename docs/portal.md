# Using the Sprocket portal

Sprocket is the portal for the cluster, and it serves this guide under **Wiki**. Public operational information is available without an account. Personal, course, lab, and administrative views appear after sign-in according to role.

## Cluster information

- **Home** shows News and Changes and one card. The card is Cluster at a glance by default: GPU capacity, running and pending work, the node list, recent utilization, and the sources behind them. A signed-in user can choose **Change card** to put any Hub widget they can see there instead. Visitors always see Cluster at a glance.
- **Hub** puts the queue, nodes, usage, and storage on one page. Each panel is a widget you can move, resize, add, or remove, and your layout is saved to your account, so it is the same in any browser. Templates are ready-made layouts: Default, Queue watcher, and Course for every signed-in user, GPU monitor and Storage for lab users, and Cluster operator for administrators. You can also save your own layout and share it by link. Clicking a node, user, pending reason, job class, or dataset filters every widget, and the filters go into the address bar so a filtered view can be shared. Opening a job, node, user, or dataset shows its details in a drawer beside the Hub, and **Pop out** moves a widget, or the whole Hub, into its own window. The Hub has a short tour, which you can take again from the Templates menu.
- The widgets you can add depend on how you are signed in. Course and lab users get the queue tiles, the job table, why jobs are waiting, who is in the queue, wait forecasts for comparable one- and two-GPU jobs, seven-day activity, contention, GPU occupancy, when the cluster has been free, node allocation split into coursework and research, GPU slots per node, source freshness, storage capacity, dataset I/O, I/O pressure, their own jobs, and, for course work, their team budget. Lab users also get per-GPU and per-node detail and storage throughput, latency, and cache and log devices. Administrators also get Node health. Wait forecasts are estimates, not reservations or guarantees.
- Visitors see the Default layout with the cluster-wide counts filled in: running and pending work, users in the queue, and GPUs allocated. The other widgets ask them to sign in, and the layout cannot be changed.
- The old **Queue**, **Usage**, and **Monitor** addresses open the Hub. Sprocket can also be installed from the browser as an app, and the installed app opens on the Hub.
- **Efficiency** ranks research members once enough job and GPU evidence from their research jobs exists. Students do not appear, and course jobs never count, including for a researcher who also takes or staffs a course. It combines GPU activity, CPU and memory fit, and storage headroom. Use it to find waste in your own jobs; it is not a performance grade.

Across all of these pages, a figure Sprocket could not measure is shown as unavailable rather than as zero, and a chart draws an unmeasured interval as a gap. A stale reading is labelled stale.

## Your account

The **Me** page shows your identity and access, SSH keys when portal management is enabled, personal job and usage history, and storage usage with threshold guidance and links back into this guide. A value marked unavailable means Sprocket has no fresh evidence for it.

## Course workspace

Students receive a three-part course workspace:

- **Getting started** covers access, storage, jobs, and support.
- **Submissions** lists assignments and deadlines, the assigned GitHub repository, declared revisions, and final confirmation.
- **Budget** shows what is left on the open assignment, what the class can use before the deadline, and the next scheduled capacity change.

Deadlines on these pages are shown in the course timezone.

TAs and instructors also receive the Staff console for rosters, teams, assignment publication and amendment, enrolment changes, exceptions, and the list of holders with nothing submitted before a deadline. Its **Submissions** tab downloads an assignment's submissions as one zip for grading. Under **Assignments**, each path assignment has a **Submission checks** panel where staff set up, test, and review the checks Sprocket runs on each path revision. TAs see both; setting up a check stays with the instructor.

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
