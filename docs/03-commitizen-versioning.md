# Commitizen and version bumping

## Install and configure

```bash
pip install commitizen
cz init          # interactive, writes [tool.commitizen] to pyproject.toml
```

Config used here (`pyproject.toml`):

```toml
[tool.commitizen]
name = "cz_conventional_commits"
version = "2.0.1"
tag_format = "$version"
update_changelog_on_bump = true
```

`version` is the source of truth. `cz bump` rewrites it. To also update other files (e.g. `package.json`, `__init__.py`), list them in `version_files`.

## Semantic Versioning: MAJOR.MINOR.PATCH

| Change | Example |
| --- | --- |
| Breaking change | 1.4.2 → **2**.0.0 |
| New feature (`feat`) | 1.4.2 → 1.**5**.0 |
| Bug fix (`fix`) | 1.4.2 → 1.4.**3** |

The highest-impact commit since the last tag decides the bump. Pre-releases: `cz bump --prerelease alpha` → `1.5.0a0`.

## Manual flow (no CI)

```bash
cz bump --dry-run        # see what would happen
cz bump                  # updates version, CHANGELOG.md, commits "bump: version X → Y", tags
git push --follow-tags
```

## Automated flow (this repo)

`.github/workflows/bump-version.yml` runs on every push to `main`:

1. Checks out with `fetch-depth: 0` so tags are available.
2. `commitizen-action` runs `cz bump --changelog`, commits `bump: version A → B` and pushes the commit and tag.
3. The `if: !startsWith(... 'bump:')` guard stops the bump commit from re-triggering itself.

No `feat`/`fix` commits since the last tag → no bump (the action exits with "no commits to bump", which you can ignore, or pass `no_raise: "21"` to the action to keep the job green).

## Tag format

Existing tags in this repo are bare (`2.0.1`), matching `tag_format = "$version"`. To switch to `v2.0.1`, set `tag_format = "v$version"` **before** the next bump, otherwise commitizen will not find the previous tag.

## Starting on an existing repo

```bash
git tag 1.0.0                 # or whatever the current version is
# set version = "1.0.0" in pyproject.toml
cz bump --dry-run
```

## pre-commit hooks

```bash
pre-commit install --hook-type commit-msg --hook-type pre-push
pre-commit run --all-files --hook-stage commit-msg --commit-msg-filename .git/COMMIT_EDITMSG
```

Update the pinned hook version with `pre-commit autoupdate`.

## Troubleshooting

| Symptom | Cause / fix |
| --- | --- |
| `[NO_COMMITS_TO_BUMP]` (exit 21) | no `feat`/`fix` since last tag, expected |
| `Tag ... does not exist` / wrong version | `tag_format` does not match existing tags |
| Action cannot push | workflow permissions are read-only, or `main` is protected |
| Bump workflow runs forever | missing `bump:` guard |
| `cz check` fails on old commits | limit the range: `--rev-range <last-tag>..HEAD` |
