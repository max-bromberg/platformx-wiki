# Workflows and file management

Keep durable files on the storage assigned to your account, stage only the data that benefits from faster I/O, and make every batch job reproducible from a repository plus documented inputs.

## A reliable run

1. **Keep source and durable inputs in persistent storage.** Research code belongs in `/home` or `/projects`; large research artifacts belong in `/data`. Course work belongs in the personal or team path assigned under `/course`.
2. **Use the shared model cache.** Hugging Face tools use `/huggingface` automatically. Course staff may publish a course-specific cache convention.
3. **Stage a hot working set only when useful.** Research jobs can use `/fast-data` for repeated high-I/O access. Course students use only locations authorized by course staff.
4. **Run from a script.** Record dependencies, resource requests, input paths, output paths, random seeds, and the immutable code revision.
5. **Copy results back and clean scratch.** Temporary fast storage is not a durable artifact store.

Small jobs that read data once may gain nothing from staging. Avoid copying data simply because a faster tier exists.

## Research pattern

```bash
# prepare on the login host
git -C /projects/<project> rev-parse HEAD

# submit reproducibly
sbatch --gres=gpu:1 --time=12:00:00 train.sh
```

For benchmarking, put timed, repeatedly accessed data on `/fast-data`, repeat the measurement, record cluster conditions, and avoid interpreting a single shared-system run as a stable hardware limit.

Checkpoint long or restartable work. This makes failures cheaper and lets suitable research jobs use `scavenger` without restarting from the beginning after requeue.

## Course pattern

```bash
mybudget
sbatch --account=<assignment> --gres=gpu:1 --time=1:00:00 train.sh
```

Before launching, coordinate with teammates because pending and running jobs share the holder budget. Commit and push outputs needed for grading, then declare the repository reference in Sprocket. Sprocket resolves it to an immutable revision; every required team member reviews and confirms that exact revision.

Do not use a mutable branch name alone as your record of what ran. Save the commit hash, job ID, Slurm account, parameters, and relevant artifact paths.

## Keep the login host responsive

Use `sprlab005` for editors, source control, modest compilation, file organization, and submission. Each ordinary user is capped at about eight CPUs there. Training, large extraction, preprocessing, and heavy builds belong in Slurm jobs.

## File ownership and collaboration

- Use group-owned research project directories for lab collaboration.
- Use the assigned course team directory for course collaboration.
- Set reasonable group permissions; do not solve access problems with world-writable directories.
- Never place credentials, private adjustment evidence, or secrets in a shared repository or team directory.

See [Storage](storage.md) for paths and quotas. Use [Sprocket Support](/support) for a broken mount or permission boundary and [Resource requests](/requests) for reviewed research capacity changes.
