# Migrate your data

!!! danger "Migrate your data by Tuesday, June 30"
    Your sprc home directories and the `/mnt/data1` data drive on the compute nodes (sprc00 through sprc03) are erased after June 30, ahead of soft enforcement on July 1. Copy anything you want to keep to the shared storage before then, and mark your name in the migration sheet linked in the `#server` announcement.

## What is happening

The cluster now has shared storage that every node sees at the same place. Your home directory on the login node, `sprlab005`, already lives there. What is going away is the per-node local storage on the compute nodes:

-   the local home directories on sprc00 through sprc03, and
-   the `/mnt/data1` data drive on those nodes.

These are erased after June 30 so the cluster can run on a single shared home for soft enforcement. Anything you have only on a compute node's local disk is lost unless you copy it to the shared storage first.

## What you need to do

**1. Find your shared home directory.** It is the same directory everywhere, mounted at two paths:

-   On the compute nodes (sprc00 through sprc03): `/mnt/spr_storage/home/<your-username>`
-   On the login node (`sprlab005`): `/home/<your-username>`, which is already shared storage, so anything already here is safe.

**2. Copy your work into it.** Log in to each compute node where you have data, and copy your local home and your `/mnt/data1` work onto the shared home. For example, on a compute node, with `<you>` replaced by your username:

```bash
# local home  ->  shared home
rsync -aAXH --info=progress2 /home/<you>/      /mnt/spr_storage/home/<you>/

# data drive  ->  a folder in your shared home
rsync -aAXH --info=progress2 /mnt/data1/<you>/ /mnt/spr_storage/home/<you>/data1/
```

Adjust the source paths to wherever your data actually lives. `rsync` is restartable, so if it is interrupted, run the same command again and it continues where it left off.

**3. Check that it landed:**

```bash
ls -la /mnt/spr_storage/home/<you>/
du -sh /mnt/spr_storage/home/<you>/
```

**4. Mark your name** in the migration sheet linked in the `#server` announcement, so the sysadmin team can confirm your data has moved.

## Quota

Shared storage has a 50 GB quota per user. Check your usage with `du -sh /mnt/spr_storage/home/<you>/`. If your data is larger than 50 GB, post in `#server` before you copy and the sysadmin team will raise your quota, otherwise the copy stops when you reach the limit.

## After June 30

-   The per-node home directories and `/mnt/data1` drives are erased.
-   Soft enforcement begins on July 1. Work you run inside an allocation gains time limits and fair-share scheduling, GPU jobs gain GPU isolation, and `sbatch` becomes available. Direct SSH still works. See [About the transition](slurm-transition.md).

## Help

If you are unsure what to keep, or you are concerned about the quota, ask in `#server` before the deadline and the sysadmin team will help you sort it out.
