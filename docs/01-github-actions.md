# GitHub Actions

## Anatomy of a workflow

Files live in `.github/workflows/*.yml`. Each file is one workflow.

```yaml
name: CI                      # shown in the Actions tab
on:                           # what triggers it
  pull_request:
    branches: [main]
permissions:                  # least privilege for GITHUB_TOKEN
  contents: read
jobs:
  test:                       # job id
    runs-on: ubuntu-latest    # runner
    steps:
      - uses: actions/checkout@v7      # reusable action
      - run: pytest                    # shell command
```

- A **workflow** contains **jobs**; jobs run in parallel unless one has `needs: other-job`.
- A **job** contains **steps**; steps run in order on one runner.
- `uses:` runs a published action, `run:` runs a shell command.

## Common triggers

| Trigger | Use for |
| --- | --- |
| `pull_request` | CI checks before merge |
| `push` with `branches: [main]` | release / deploy after merge |
| `workflow_dispatch` | manual "Run workflow" button |
| `schedule: - cron: "0 3 * * 1"` | nightly or weekly jobs |

Avoid `on: [push, pull_request]` for CI: a push to a PR branch runs everything twice.

## Matrix builds

```yaml
strategy:
  fail-fast: false            # keep other combos running if one fails
  matrix:
    os: [ubuntu-latest, macos-latest, windows-latest]
    python-version: ["3.10", "3.11", "3.12", "3.13"]
runs-on: ${{ matrix.os }}
```

Always **quote** Python versions. `3.10` unquoted is the float `3.1`.

## Pin versions of actions

- First-party actions: major tag is fine (`actions/checkout@v7`).
- Third-party: pin an exact tag (`commitizen-action@0.27.1`) or a commit SHA. Never `@master`.
- Check the latest tag on each action's Releases page every few months.

## Secrets and tokens

- `${{ secrets.GITHUB_TOKEN }}` is created per run. Scope it with `permissions:`.
- Use a PAT only when the default token cannot do the job (e.g. pushing to a protected branch, or needing the push to trigger other workflows; pushes made with `GITHUB_TOKEN` do not trigger new workflow runs).

## Security gotcha: injection

Do not put untrusted values (PR title, branch name, commit message) inside `run:` with `${{ }}`. Pass them through `env:` and use the shell variable:

```yaml
env:
  BASE_REF: ${{ github.base_ref }}
run: echo "$BASE_REF"
```

## Preventing a bump loop

A workflow that pushes a commit to `main` can trigger itself. `bump-version.yml` avoids that with:

```yaml
if: ${{ !startsWith(github.event.head_commit.message, 'bump:') }}
```

## Debugging

- Actions tab → run → re-run with **Enable debug logging**.
- Test the YAML locally with [`act`](https://github.com/nektos/act) or lint with `actionlint`.
