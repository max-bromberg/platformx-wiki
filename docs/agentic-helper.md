# Agentic coding helper

The `sprc-slurm` plugin teaches supported coding agents how to stage, submit, monitor, and debug work on this cluster. It is useful for translating a plain-language request into `salloc`, `sbatch`, `squeue`, and `sacct` operations.

## Install

The plugin ships from the [`sprc-plugins`](https://github.com/platformx-students/sprc-plugins) marketplace. That repository is private, and read access to the `platformx-students` GitHub organization is the whole access control. Every install path authenticates as you, so check that git can reach the repository first:

```bash
gh auth login
git ls-remote git@github.com:platformx-students/sprc-plugins >/dev/null && echo "git auth OK"
```

A failure there is this machine’s git credentials. Ask the lab admins if you need access to the organization itself.

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

## Coursework

The plugin is built for research work. Course staff may use it on assignment work, and it no longer refuses to run for a course account.

That means nothing about it decides what help is allowed on your assignment. Your course’s academic integrity policy does, and it is the only authority on the question. Ask course staff before using an agent on graded work.

## Responsibility

The plugin is a convenience layer over the same commands you would type. Every command runs with your account and your permissions. Review destructive commands, requested resources, output paths, and repository changes exactly as you would if you had typed them. Never ask an agent to work around an account, QoS, reservation, or budget rejection.

Use [Sprocket Support](/support) for plugin or cluster problems and include the generated command and error.
