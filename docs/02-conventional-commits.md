# Conventional Commits

Format:

```
<type>(<optional scope>)<!>: <short summary>

<optional body: what and why>

<optional footer: BREAKING CHANGE: ..., Closes #12>
```

## Types and their effect on the version

| Type | Meaning | Version bump |
| --- | --- | --- |
| `feat` | new feature | **MINOR** (1.2.0 → 1.3.0) |
| `fix` | bug fix | **PATCH** (1.2.0 → 1.2.1) |
| `perf` | performance improvement | PATCH |
| `refactor` | code change, no behaviour change | none by default |
| `docs` | documentation only | none |
| `style` | formatting, whitespace | none |
| `test` | add or fix tests | none |
| `build` | build system, dependencies | none |
| `ci` | CI config, workflows | none |
| `chore` | maintenance, tooling | none |
| `revert` | revert a commit | none |
| any type + `!` or `BREAKING CHANGE:` footer | breaking API change | **MAJOR** (1.2.0 → 2.0.0) |

Only `feat`, `fix`, `perf` and breaking changes appear in the generated changelog and trigger a bump. A push with only `docs:`/`ci:`/`chore:` commits creates no release.

## Rules of thumb

- Imperative, present tense: "add", not "added" or "adds".
- Lowercase summary, no trailing period, 72 characters or less.
- One logical change per commit.
- Scope is optional and names the area: `fix(parser): handle empty input`.

## Examples

```
feat: add subtract() function
fix(api): prevent racing of requests
docs: explain the bump workflow in README
ci: pin commitizen-action to 0.27.1
build: add pandas to requirements
refactor!: rename add() to sum_two()

BREAKING CHANGE: callers must use sum_two()
```

Bad:

```
update stuff
Fixed bug.
WIP
added new file
```

## Tooling

- `cz commit` asks the questions and builds a valid message.
- `cz check --message "feat: x"` validates a string.
- The `commitizen` pre-commit hook rejects invalid messages locally; the `commit-lint` job in `ci.yml` rejects them on the PR.
- Merge commits and `bump:` commits are ignored by the checker.

If you squash-merge, the **PR title** becomes the commit message on `main`, so write it in this format too.
