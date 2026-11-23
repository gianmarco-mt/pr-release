# pr-release

Keep track of the manual steps a pull request needs when its code reaches an environment, such as a data script, a backfill or a recalculation, so they are not forgotten at merge time.

It is two short text files for your repo. Any coding agent, from any provider, can follow them.

## The problem

Sometimes a PR needs a script to run on the database, first on dev, then on prod. If that step is written only in the PR description, nobody sees it at merge time. Days later, when the same code reaches prod, nobody remembers it either.

## How it works

1. **The developer's agent declares the step.** If a PR needs a manual step, its agent adds a small file to `release-actions/` in the same PR. The file says in which environments the step runs, what to do and when.
2. **The merger's agent flags it.** Before merging, the agent of whoever merges shows them the steps the PR brings. It also flags a file that is incomplete, or a step the PR needs but does not declare. The agent does not run anything on its own.
3. **The person runs the steps,** or asks their agent to run them, after the deploy.
4. **The step is ticked off with a comment** on the PR that added the file:
   `Release action done on dev`, or `Release action pending on prod: <reason>`.

Promoting `develop` to `main` works the same way, even when it carries many PRs. Every action file that `develop` has and `main` does not is a step for prod.

Nothing in the repo changes after the merge. There are no checkboxes to tick and no commits on protected branches. Each PR keeps the history of its step across environments in its comments.

## What goes in your repo

```
your-repo/
├── AGENTS.md                      + the "Release actions" section
└── release-actions/
    ├── README.md                  the instructions for both roles
    └── 482-recalculate-totals.md  an action, added by a PR when needed
```

An action file looks like this:

```
Environments: dev, prod

What to do: run `python scripts/recalculate_totals.py` (dry run), check the count, then rerun with `--apply`.
When: after this PR is deployed there and the other repo has published config version 12.
```

## Install

Copy the contents of `template/` into the root of your repo:

1. `template/release-actions/README.md` goes to `release-actions/README.md`.
2. `template/AGENTS.md` holds a "Release actions" section. If your repo already has an `AGENTS.md`, append the section to it instead of replacing the file.
3. If you use a Claude Code version that reads only `CLAUDE.md`, add a `CLAUDE.md` containing `@AGENTS.md`.

## Files

| Path | What it is |
| --- | --- |
| `README.md` | This page: what the setup is, how it works, how to install it. |
| `how-it-works.html` | A one-page explainer in Italian, with a step-by-step example. Download it and open it in a browser. |
| `template/` | The only files you copy, laid out exactly as they go in your repo. |
| `template/AGENTS.md` | The section to add to your `AGENTS.md`. It points agents to the instructions. |
| `template/release-actions/README.md` | The instructions for developers and mergers. |
