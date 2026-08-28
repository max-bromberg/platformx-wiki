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

## Not for coursework

This plugin is for research work only. It stops as soon as it detects a course account, so follow the course’s own guidance for assignment work.

## Responsibility

The plugin is a convenience, not a separate execution system. Every command runs with the user’s account and permissions. Review destructive commands, requested resources, output paths, and repository changes exactly as you would if typing them yourself. Never ask an agent to work around an account, QoS, reservation, or budget rejection.

Use [Sprocket Support](/support) for plugin or cluster problems and include the generated command and error.
