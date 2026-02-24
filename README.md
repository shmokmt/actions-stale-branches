# actions-stale-branches

Delete stale branches in a repository by branch glob and inactivity days.

This is a third-party composite action built on top of `actions/github-script`.

## Motivation

In the official [actions/stale](https://github.com/actions/stale) options, branch deletion is described as "Delete branch after closing a stale PR".
That behavior is PR-focused and does not delete remote branches that are not tied to a pull request.
I created this action to clean up those stale remote branches (for example, branches pushed by Devin).

If you want to clean up branches tied to open pull requests, [actions/stale](https://github.com/actions/stale) is a good fit.

## What it does

- Lists all branches.
- Selects branches matching `include_branches`.
- Excludes default/protected branches and `exclude_branches`.
- Checks latest commit time per branch.
- Deletes branches older than `inactive_days` (or only reports with `dry_run: true`).

## Inputs

- `github_token`: Token with `contents: write` permission. Default is `${{ github.token }}`.
- `include_branches` (required): Include patterns, newline or comma separated.
- `inactive_days` (required): Threshold in days.
- `exclude_branches`: Exclude patterns, newline or comma separated.
- `dry_run`: `true` or `false` (default: `true`).

## Outputs

- `summary`: JSON summary string.
- `deleted_count`: Number of deleted branches.
- `candidate_count`: Number of stale branches matching policy.

## Usage

```yaml
name: Cleanup stale branches

on:
  schedule:
    - cron: "0 2 * * *"
  workflow_dispatch:

permissions:
  contents: write

jobs:
  cleanup:
    runs-on: ubuntu-latest
    steps:
      - name: Delete stale feature branches
        uses: shmokmt/actions-stale-branches@v0
        with:
          include_branches: |
            feature/**
            bugfix/**
          exclude_branches: |
            feature/keep-*
          inactive_days: "30"
          dry_run: "true"
```

## Notes

- Branch activity is evaluated by the latest commit date on each branch.
- Default branch and protected branches are always skipped.
- Start with `dry_run: "true"` and inspect `summary` before enabling deletion.
