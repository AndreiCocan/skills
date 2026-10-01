---
name: conventional-commits
description: >
  Writes git commit messages that follow the Conventional Commits 1.0.0 spec:
  type, optional scope, imperative description, body explaining why, and
  footers including BREAKING CHANGE. Covers picking the right type, deriving
  the message from the staged diff rather than from the task prompt, matching
  a repository's existing convention, and splitting a commit that does two
  things. Use before writing any git commit message, when amending or
  rewording a commit, when asked to fix or review commit messages, and
  whenever the user asks for conventional commits by name.
---

# Conventional Commits

A commit message describes what the diff does, for someone reading `git log`
in a year with no memory of the task. Derive it from the diff, never from the
prompt that started the work.

## Read before writing

1. `git status` and `git diff --staged`. The message describes what is staged, nothing else.
2. `git log --oneline -20`. Match the repository's existing convention.
3. If the repository does not use Conventional Commits, follow what it does use. A lone `feat(api):` in a log of plain sentences is noise, not an improvement. Say so, and offer to convert the repo separately.

The diff staged is the diff described. If it does two unrelated things, that is
two commits, not one message with "and" in it.

## Format

```
<type>[(<scope>)][!]: <description>

[body]

[footers]
```

- **type**: from the list below, lowercase.
- **scope**: optional noun in parentheses naming the affected area (`api`, `parser`, `deps`). Take it from scopes the repo already uses; do not invent a parallel vocabulary. Never use an issue identifier as a scope.
- **!**: marks a breaking change, before the colon.
- **description**: mandatory. Imperative, present tense, first letter not capitalized, no trailing period.
- **body**: optional, blank line before it, wrapped at 72 columns.
- **footers**: optional, blank line before them, git trailer format (`Token: value`). Mandatory for a breaking change the description does not explain.

Three commits do not follow this format:

| Commit | Message |
|---|---|
| Initial commit | `chore: init` |
| Merge commit | git's default: `Merge branch '<branch name>'` |
| Revert commit | git's default: `Revert "<reverted commit subject line>"` |

## Types

Changes relevant to the API or UI:

| Type | Use for |
|---|---|
| `feat` | add, adjust, or remove a feature of the API or UI |
| `fix` | fix an API or UI bug of a preceding `feat` commit |

Other changes:

| Type | Use for |
|---|---|
| `refactor` | rewrite or restructure code without changing API or UI behavior |
| `perf` | a `refactor` that improves performance |
| `style` | code style only (white space, formatting, missing semicolons), no behavior change |
| `test` | add missing tests or correct existing ones |
| `docs` | documentation only |
| `build` | build tools, dependencies, project version |
| `ci` | CI/CD pipeline configuration |
| `ops` | infrastructure (IaC), deployment scripts, backups, monitoring, recovery procedures |
| `chore` | tasks such as the initial commit or editing `.gitignore` |

Picking between them: did the API or UI change? Yes → `feat`, or `fix` when
it repairs an earlier `feat`. Removing a feature is `feat` too. No, only the
shape of the code → `refactor`, or `perf` when that makes it faster. How the
software is built → `build`. How the pipeline runs → `ci`. How the software
is deployed, run, monitored, backed up, or recovered → `ops`. `chore` is the
last resort, not the default.

## Versioning

The commits of a release choose its version:

- A breaking API or UI change (`!`) increments the major version.
- Otherwise, a `feat` increments the minor version.
- Otherwise, increment the patch version.

## The description

It completes the sentence "If applied, this commit will ___".

- `add retry to the upload client`, not `added retry` or `adds retry`.
- Aim for a subject line under 50 characters, hard limit 72.
- Name the thing that changed, not the activity. `fix(auth): reject expired tokens` beats `fix(auth): fix bug in auth`.
- No task or ticket noise in the subject. Footers hold that.

## The body

Include one when the subject cannot carry the reason. State why the change was
needed and contrast it with the previous behavior, not a line-by-line account
of the diff: the diff is already in the commit. Use the imperative, present
tense, as in the description.

Worth a body: a non-obvious root cause, a rejected alternative, a constraint
that forced the approach, a behavior change a reader would not expect from the
subject.

## Language

ASD-STE100 simplified technical English, in the subject and the body alike: one
idea per sentence, present tense, active voice, one term per concept. A commit
message is read by people who did not write it, often in a second language,
often years after the diff stopped being obvious.

- NEVER use an em dash. Use a period, a comma, or parentheses.
- Be as short as the change allows and no shorter. Concision cuts words, never facts: a reason the reader needs stays, however long that makes the body.
- State facts, do not hedge. "Serves a stale entry when the TTL expires mid-read", not "may sometimes return stale data".
- One term per concept across the whole log. Do not call it a cache in one commit and a store in the next.
- Name symbols, flags, and files exactly as the code names them, case for case.

## Breaking changes

Both markers, together, when a public contract changes:

```
feat(api)!: return an error when the key is absent

BREAKING CHANGE: Lookup previously returned a zero value for a missing key
and now returns ErrNotFound. Callers relying on the zero value must handle
the error.
```

The `BREAKING CHANGE:` footer states what broke and what callers do about it.
`!` alone tells a reader something broke without telling them what. The
footer is mandatory when the description does not say what broke.

The footer starts with the exact words `BREAKING CHANGE:`. A one-line
explanation follows after a space. A multi-line explanation starts after two
newlines:

```
BREAKING CHANGE:

Lookup previously returned a zero value for a missing key.
It now returns ErrNotFound.
```

## Footers

- Issue references: `Refs: #123`, `Closes #123`, `Fixes JIRA-456`.
- `BREAKING CHANGE:` as above.

## Never

- Describe the prompt instead of the diff. "implement user request" documents nothing.
- Claim a verification that did not run. If the tests were not run, the message does not say tests pass.
- Bundle unrelated changes and paper over it with "and" or a bullet list of three unrelated subjects.
- Commit generated files, secrets, or unrelated stray files picked up by a blind `git add -A`. Check `git status` first.
- Use `chore` for something a user can observe.
- Write a subject that repeats the type: `fix: fix`, `docs: update docs`.

## Examples

```
feat(parser): accept ISO 8601 durations

fix(cache): evict entries whose TTL expired during a read

The read path checked expiry before taking the lock, so an entry expiring
between the check and the read was served stale. Move the check inside the
critical section.

Fixes #482

refactor(store): extract the retry loop into withRetry

docs(readme): document the timeout flag default

ops(helm): add a PodDisruptionBudget to the api deployment

build: update dependencies

feat(api)!: remove the ticket list endpoint

BREAKING CHANGE: the ticket endpoints no longer list all entities.

Revert "feat(parser): accept ISO 8601 durations"

This reverts commit 4f2a1c9.
```
