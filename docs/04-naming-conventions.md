# Naming conventions

## Branches

`<type>/<short-kebab-description>`, using the same types as commits.

```
feat/add-subtract
fix/race-condition
docs/readme-cleanup
chore/restructure-reference
ci/pin-action-versions
```

Optionally prefix with an issue number: `feat/123-add-subtract`.

Long-lived branches: `main` (releases). Keep everything else short-lived and delete after merge.

## Commits

See [02-conventional-commits.md](02-conventional-commits.md). Format: `type(scope): summary`.

## Pull requests

- Title follows the commit format (matters for squash merges): `feat: add subtract() function`.
- Body: what changed, why, how it was tested, `Closes #N`.

## Tags and releases

- Created by `cz bump`, never by hand.
- Semantic version, bare (`2.0.1`) in this repo; `v2.0.1` is the other common style. Pick one and set `tag_format`.
- GitHub release title = the tag.

## Files and folders

| Thing | Convention | Example |
| --- | --- | --- |
| Workflow files | kebab-case, named for the job | `ci.yml`, `bump-version.yml`, `release.yml` |
| Workflow `name:` | short Title Case, shown in checks | `CI`, `Bump version` |
| Job ids | kebab-case | `test`, `commit-lint` |
| Step names | verb phrase | `Install dependencies` |
| Python modules | snake_case | `calculator.py` |
| Test files | `test_<module>.py` in `tests/` | `test_calculator.py` |
| Docs | numbered, kebab-case | `01-github-actions.md` |
| Requirements | `requirements.txt`, `requirements-dev.txt` | |
| Secrets / env vars | UPPER_SNAKE_CASE | `PERSONAL_ACCESS_TOKEN` |

Avoid names like `workflow-template.yaml` for a live workflow: GitHub only reads `.github/workflows/*.yml|yaml`, and `.yml` is the convention.
