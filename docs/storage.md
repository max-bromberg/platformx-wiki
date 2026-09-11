# Storage

Storage depends on what your account is for. Research users have a shared home and several research data tiers. Course students receive a bounded course workspace, and a team assignment can add a shared team workspace.

Course workspaces carry an enforced filesystem quota, so a write that would exceed it fails. Research storage is shared, and an allowance agreed through [Resource requests](/requests) is what you are expected to keep to. Sprocket’s **Me** page reports your observed home usage, with a usage band where a quota is set. This page does not print sizes, because they are set per account and per assignment and would go stale here.

## Research home: `/spr-storage/home`

A research home is mounted at `/spr-storage/home/<username>` on the login host and every compute node. `/home` is the same filesystem, so either path reaches the same files. It is the right place for code, configuration, conda environments, and results you need to keep.

Check usage in Sprocket’s **Me** page and from the shell:

```bash
du -sh /spr-storage/home/<username>
```

Large datasets, shared project trees, model weights, and temporary high-I/O working sets belong on the tiers below.

## Course storage: `/course`

A course-only student receives a normal Unix home backed by its own ZFS dataset at `/course/<NetID>`. It is mode `0700` and stays personal after teams form. Both the dataset quota and the referenced-data quota are set to the same boundary, so retained immutable submission snapshots cannot grow outside the approved allocation.

A team assignment can create a shared workspace at:

```text
/course/groups/<course>/<team>
```

Workspace sizes come from the course’s published assignments, so they change when an instructor publishes or amends one, and reconciliation never lowers a quota below what the workspace already holds. `mybudget` prints your current workspace and team sizes alongside your usage. Only current team members receive the team group identity. Team changes preserve existing bytes and history rather than moving or deleting data.

A researcher who is also enrolled in a course keeps their existing account and research home. The
research home remains `$HOME`. Sprocket adds a separate `/course/<NetID>` coursework workspace for
individual path submissions, plus the team workspace a team assignment needs. The course workspace
does not create a second account and does not replace the research home.

## Projects: `/projects`

`/projects` is research group storage for shared code, source trees, package installations, and datasets containing many small files. Directories are group-owned and carry an aggregate project quota rather than a per-person quota. Request a project directory or membership through [Resource requests](/requests).

Course students should not assume they can use `/projects`; use the course workspace unless course staff explicitly provide another location.

## Large files: `/data`

`/data` is for large single files such as model checkpoints, archives, and compressed shards. Research users can organize their own directory, commonly `/data/<username>`. Ask for an allowance through [Resource requests](/requests) before storing a large working set here.

Course-only accounts should use assigned course storage unless course staff explicitly publish a course data location.

## Shared model cache: `/huggingface`

The SSD-backed `/huggingface` cache provides one warm copy of commonly used Hugging Face models. `HF_HOME` and `HF_HUB_CACHE` are configured cluster-wide, so ordinary `transformers`, `datasets`, and `huggingface_hub` use it automatically.

The cache is shared. Do not delete or rewrite another user’s cached material, and do not create duplicate model caches in personal home unless a reproducibility requirement genuinely needs one.

Course staff may publish a more specific course model-cache convention; follow that instruction when present.

## Fast working data: `/fast-data`

`/fast-data` is SSD-backed shared scratch for the hot working set of a running research job. Stage frequently read or written data there, run against the staged copy, copy durable results back, and clean up.

Treat it as working space and keep a durable copy of anything you need elsewhere. It is shared across nodes, so its speed varies under contention.

Course-only students should use it only when course staff explicitly authorize a workflow and location; course storage limits remain the default boundary.

## Quick placement guide

| Material | Research account | Course-only account |
| --- | --- | --- |
| Personal code and durable results | `/spr-storage/home/<username>` | `/course/<NetID>` |
| Team code and shared results | `/projects/<project>` | `/course/groups/<course>/<team>` |
| Large model checkpoints or archives | `/data/<username>` | Assigned course or team workspace |
| Shared Hugging Face models | `/huggingface` | Course-published cache convention |
| Temporary hot working set | `/fast-data` | Only when course staff authorize it |

## Quotas and requests

Sprocket’s Me page reports observed home usage when fresh telemetry is available, with a usage band where a quota is set. A reading marked unavailable means Sprocket has no fresh measurement. It does not mean the space is unlimited. Sprocket measures per-user usage only for the research home; check the other tiers with `du`.

Research users request additional storage through [Resource requests](/requests). Course workspace and team sizes are instructor-configured. Ask course staff, or Sprocket Support if the assigned space is missing or wrong.
