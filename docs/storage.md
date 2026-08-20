# Storage

Storage depends on account type and purpose. Research users have shared home and research data tiers. Course-only students receive bounded course storage, and team checkpoints can add a shared team workspace.

## Research home — `/spr-storage/home`

A research home is mounted at `/spr-storage/home/<username>` on the login host and every compute node. It has an account-type quota and is appropriate for code, configuration, conda environments, and results you need to keep.

Check usage in Sprocket’s **Me** page and from the shell:

```bash
du -sh /spr-storage/home/<username>
```

Large datasets, shared project trees, model weights, and temporary high-I/O working sets belong on the tiers below.

## Course storage — `/course`

A course-only student receives a normal Unix home backed by its own ZFS dataset at `/course/<NetID>`, rather than a research home under `/spr-storage/home`. It starts with a 10 GiB allocation, mode `0700`, and remains personal even after teams form. Both the dataset quota and referenced-data quota are set to the same boundary, so retained immutable submission snapshots cannot grow outside the approved allocation.

A team checkpoint can create a shared workspace at:

```text
/course/groups/<course>/<team>
```

The standard team quota is 50 GB. Checkpoint policy can request more space, and reconciliation never lowers a live quota below current usage. Only current team members receive the team group identity. Team changes preserve existing bytes and history rather than silently moving or deleting data.

A researcher who is also enrolled in a course keeps the existing research UID and passwd home. The
research home remains `$HOME`; Sprocket also provides an isolated `/course/<NetID>` personal
coursework workspace for individual path submissions, plus the team workspace needed for team
checkpoints. The course workspace does not create a second Unix identity or replace the research
home.

## Projects — `/projects`

`/projects` is research group storage for shared code, source trees, package installations, and datasets containing many small files. Directories are group-owned and carry an aggregate project quota rather than a per-person quota. Request a project directory or membership through [Resource requests](/requests).

Course students should not assume they can use `/projects`; use the course workspace unless course staff explicitly provide another location.

## Large files — `/data`

`/data` is for large single files such as checkpoints, archives, and compressed shards. Research users can organize their own directory, commonly `/data/<username>`, with a standard 50 GB per-user quota across files they own.

Course-only accounts should use assigned course storage unless course staff explicitly publish a course data location.

## Shared model cache — `/huggingface`

The SSD-backed `/huggingface` cache provides one warm copy of commonly used Hugging Face models. `HF_HOME` and `HF_HUB_CACHE` are configured cluster-wide, so ordinary `transformers`, `datasets`, and `huggingface_hub` use it automatically.

The cache is shared. Do not delete or rewrite another user’s cached material, and do not create duplicate model caches in personal home unless a reproducibility requirement genuinely needs one.

Course staff may publish a more specific course model-cache convention; follow that instruction when present.

## Fast working data — `/fast-data`

`/fast-data` is SSD-backed shared scratch for the hot working set of a running research job. It has a standard 100 GB per-user quota. Stage frequently read or written data there, run against the staged copy, copy durable results back, and clean up.

It is working space, not the only copy of an artifact. It is also shared across nodes, so performance can still vary under contention.

Course-only students should use it only when course staff explicitly authorize a workflow and location; course storage limits remain the default boundary.

## Quick placement guide

| Material | Research account | Course-only account |
| --- | --- | --- |
| Personal code and durable results | `/spr-storage/home/<username>` | `/course/<NetID>` |
| Team code and shared results | `/projects/<project>` | `/course/groups/<course>/<team>` |
| Large checkpoints or archives | `/data/<username>` | Assigned course or team workspace |
| Shared Hugging Face models | `/huggingface` | Course-published cache convention |
| Temporary hot working set | `/fast-data` | Only when course staff authorize it |

## Quotas and requests

Sprocket’s Me page reports observed home usage and quota when fresh telemetry is available. An unavailable reading is not proof of unlimited space.

Research users request additional storage through [Resource requests](/requests). Course checkpoint storage and team workspaces are instructor-configured; use course staff or Sprocket Support when the assigned space is missing or incorrect.
