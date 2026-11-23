# Release actions

A release action is a manual step a PR needs when its code reaches an environment: a data script, a backfill, a recalculation. Each one is a file in this folder. This README is not an action.

## If your PR needs one

Add a file here in the same PR as the code, named after your branch without the type prefix (`feat/482-recalculate-totals` → `482-recalculate-totals.md`). If the pipeline can do the step instead, automate it and add no file.

Write only:

- **Environments**: a line such as `Environments: dev, prod`.
- **What to do**: the exact commands, dry run first, then the real run.
- **When**: what must happen first, e.g. "after the other repo has published config version 12 there".

Example:

```
Environments: dev, prod

What to do: run `python scripts/recalculate_totals.py` (dry run), check the count, then rerun with `--apply`.
When: after this PR is deployed there and the other repo has published config version 12.
```

To change a step that already ran somewhere, add a new file instead of editing the old one.

## If you merge

Do not run release actions yourself unless the person merging asks you to.

1. Before merging, show the person merging the files the PR adds to this folder: what to do, when, and in which environments. Also flag it if the PR seems to need a step it does not declare, or a file lacks what, when or where.
2. The person runs the steps for that environment, or tells you to run them.
3. When the person says a step is done or must wait, comment on the PR that added the file: `Release action done on <env>` or `Release action pending on <env>: <reason>`.

A release PR from `develop` into `main` works the same way, even when it carries many PRs: every file it adds to this folder is a step for prod. List them all, and comment on each file's own PR, not on the release PR.
