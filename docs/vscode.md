# VS Code

Connect VS Code Remote-SSH to the login host, edit there, and submit compute through Slurm from the integrated terminal. This keeps the editor stable while compute allocations start and end independently.

## Connect

Use **Remote-SSH: Connect to Host** and select:

```text
<username>@sprlab005.csl.illinois.edu
```

Open the persistent research or course workspace assigned to the account. Off campus, connect to the university VPN first.

## Run research compute

From the integrated terminal:

```bash
sbatch train.sh
squeue --me
```

The job runs on a compute node while logs and outputs appear in shared storage that VS Code can read from the login host.

## Run course compute

Copy the assignment account from Sprocket’s Course pages:

```bash
mybudget
sbatch --account=<assignment> train.sh
```

Keep the account, GPU request, and realistic time limit in the batch script so teammates and course staff can reproduce the submission.

## Why not attach the editor to a compute node?

A compute-node SSH session requires an active allocation and is bound to that job’s limits. Research interactive allocations normally end within an hour, and course allocations consume assignment budget while held. An editor attached to the login host does not require a compute allocation and survives between jobs.

For a short terminal debugging session, use `salloc`, then SSH to the assigned node. Keep the VS Code window on the login host unless there is a specific reason to attach an editor to the temporary allocation.

## Limits to keep in mind

- The login host has no GPU. `nvidia-smi` belongs inside an allocation.
- Keep training, heavy preprocessing, and large builds off the login host.
- A language server or editor task that becomes resource-heavy is still subject to the login-host CPU cap.
- Manage SSH keys from Sprocket’s Me page when that feature is available for the account.

Use [Sprocket Support](/support) for connection or key problems and include the Remote-SSH error log when possible.
