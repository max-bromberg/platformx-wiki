# Agentic coding helper

The `sprc-slurm` plugin teaches supported coding agents how to stage, submit, monitor, and debug work on this cluster. It is useful for translating a plain-language request into `salloc`, `sbatch`, `squeue`, and `sacct` operations.

## Install

The plugin ships from the [`sprc-plugins`](https://github.com/platformx-students/sprc-plugins) marketplace.

In Claude Code:

```text
/plugin marketplace add platformx-students/sprc-plugins
/plugin install sprc-slurm@sprc-plugins
/reload-plugins
```

For Codex, ohmypi, or another skills-aware harness, follow the repository’s installation instructions.

## Useful requests

- “Run `train.py` on one GPU for six hours and email me when it finishes.”
- “Why is job 4213 pending?”
- “Package this as a checkpointing batch job suitable for scavenger.”
- “Submit this against the course assignment account shown by `mybudget` for 30 minutes.”

## Course safety

An agent does not create budget or choose which overlapping assignment should pay. For course work:

1. inspect Sprocket Checkpoints or run `mybudget`;
2. name the intended assignment explicitly;
3. verify GPUs × requested minutes fits the free budget; and
4. review the generated command before submission.

The Slurm course gate remains authoritative and fails closed if accounting is unavailable. Never ask an agent to work around an account, QoS, reservation, or budget rejection.

## Responsibility

The plugin is a convenience, not a separate execution system. Every command runs with the user’s account and permissions. Review destructive commands, requested resources, output paths, and repository changes exactly as you would if typing them yourself.

Use [Sprocket Support](/support) for plugin or cluster problems and include the generated command and error.
