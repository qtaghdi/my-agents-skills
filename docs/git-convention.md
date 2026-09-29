# Git Convention

This document defines the default Git and pull request conventions for this repository.

Repository-specific workflow requirements may extend these rules, but should not silently weaken history safety or verification requirements.

## 1. General Principles

Git history should make the reason for a change easy to understand.

Prefer:

- focused branches
- focused commits
- independently reviewable changes
- explicit staging
- predictable naming
- verifiable pull request scope

Avoid:

- unrelated changes in one commit
- drive-by cleanup
- ambiguous commit messages
- accidental generated files
- mixing formatting changes with behavior changes
- rewriting shared history without a concrete reason

Before changing Git state, inspect the current repository state instead of assuming it.

## 2. Branches

Use lowercase `kebab-case`.

Default format:

```text
<type>/<short-description>
```

Examples:

```text
feat/project-search
fix/session-expiry
refactor/auth-boundary
docs/api-guide
chore/project-template
```

Allowed common types:

```text
feat
fix
docs
refactor
test
build
ci
chore
perf
revert
security
```

Choose a short description that communicates the branch's actual purpose.

Avoid:

```text
feature/new-feature
fix/bug
update/stuff
work/temp
sangmin-test
```

Do not create a branch from another unmerged feature branch unless a stacked change is intentional.

When creating a normal branch:

1. identify the repository's actual default integration branch
2. update the relevant remote references
3. create the branch from the intended base
4. verify that unrelated commits are not already included

Do not assume every repository uses `main` or `develop`.

## 3. Commit Messages

Use Conventional Commits.

Format:

```text
<type>(<optional-scope>): <description>
```

Examples:

```text
feat(auth): add passkey sign-in
fix(editor): preserve selection after refresh
docs(api): document webhook validation
refactor(issue): simplify grouping flow
test(auth): cover expired session handling
chore(template): add reusable project setup
```

### Language

Write commit subjects in English.

### Subject

The description should be:

- concise
- imperative
- lowercase
- specific to the actual change

Do not end the subject with a period.

Prefer:

```text
fix(auth): reject expired sessions
```

instead of:

```text
fix: fixed some auth stuff
```

### Scope

Use a scope when it makes the affected area clearer.

Examples:

```text
auth
web
api
db
editor
ui
docs
deps
build
```

Do not invent a scope merely to fill the field.

### Type Selection

Use:

- `feat`: new product behavior
- `fix`: bug fix
- `docs`: documentation-only change
- `refactor`: code restructuring without intended behavior change
- `test`: test-only change
- `build`: build system or packaging
- `ci`: CI/CD configuration
- `chore`: repository maintenance or setup
- `perf`: performance improvement
- `security`: security-specific improvement
- `revert`: revert of an earlier change

Do not use `feat` for every meaningful change.

## 4. Commit Scope

One commit should represent one coherent reason for change.

A reviewer should be able to answer:

> Why does this commit exist?

with one clear answer.

Prefer separating changes when they represent independently understandable concerns.

For example:

```text
feat(auth): add session validation
test(auth): cover expired sessions
docs(auth): document session lifecycle
```

may be appropriate when those changes are independently meaningful.

However, do not split changes mechanically.

A tiny implementation and its directly required test may belong in the same commit when separating them would make history less useful.

The goal is coherent history, not maximum commit count.

## 5. Staging

Stage explicit files or paths whenever practical.

Before committing, inspect:

```bash
git status
git diff
git diff --cached
```

Do not blindly stage the entire repository when unrelated work may be present.

Avoid relying on:

```bash
git add .
```

when the working tree contains changes outside the current task.

Preserve user-owned and unrelated modifications.

## 6. Existing Changes

Never discard, overwrite, reset, stash, or rewrite changes that were not created as part of the current task unless explicitly authorized.

When the working tree is already dirty:

1. identify existing changes
2. determine which files belong to the current task
3. keep unrelated work untouched
4. stage only the intended paths

Do not assume an unfamiliar change is disposable.

## 7. History Rewrites

Do not rewrite shared branches.

Never force-push repository default branches such as:

```text
main
master
develop
```

For an unmerged personal or task branch, history may be rewritten only when there is a clear reason and the user has authorized the Git mutation.

When force-pushing is genuinely required, prefer:

```bash
git push --force-with-lease
```

Never use plain `--force` when `--force-with-lease` can safely perform the operation.

Before rewriting:

1. inspect the current remote branch
2. preserve recoverability when appropriate
3. understand which commits will disappear

After rewriting, verify the remote history again.

## 8. Pull Requests

A pull request should have one primary purpose.

Prefer separate pull requests when changes:

- can ship independently
- can roll back independently
- have substantially different risk profiles
- require different reviewers
- introduce infrastructure separately from product behavior
- would otherwise force a reviewer to understand multiple unrelated systems

Keep changes together when splitting them would produce incomplete, misleading, or non-functional pull requests.

Do not split work simply to create more PRs.

### PR Title

Use the same Conventional Commit style as commit messages where practical.

Example:

```text
feat(auth): add passkey sign-in
```

### PR Description

A useful pull request description should normally contain:

```md
## Summary

What changed and why.

## Scope

What is included.

## Non-Goals

What is intentionally not included, when useful.

## Verification

What was actually executed or manually verified.

## Impact

Migration, deployment, security, compatibility, or operational impact when applicable.
```

Do not fill sections with meaningless text solely to satisfy a template.

## 9. Before Commit

Before creating a commit:

1. inspect `git status`
2. inspect the intended diff
3. remove debug or temporary artifacts
4. confirm no secrets are included
5. confirm unrelated files are not staged
6. run appropriate verification for the change
7. update applicable documentation
8. update `CHANGELOG.md` when required

Do not commit known broken code unless the workflow explicitly requires an intermediate commit.

## 10. Before Push

Before pushing:

1. inspect commits that are not yet on the intended base
2. inspect the branch diff against its intended base
3. confirm the branch contains only intended work
4. confirm relevant verification has passed
5. confirm no accidental generated files are included

Do not interpret a successful push as successful deployment.

## 11. Before Pull Request

Before opening a pull request:

1. confirm the intended base branch
2. confirm the intended head branch
3. inspect the complete diff
4. inspect included commits
5. confirm the PR represents one coherent scope
6. confirm verification results are accurately described
7. document known limitations instead of hiding them

Create pull requests as ready for review by default.

Use Draft only when:

- the user explicitly requests it
- the repository workflow requires it
- the change intentionally needs collaboration before it can be reviewed as complete

## 12. After Pull Request Creation

When tools allow it, verify the created pull request rather than assuming the mutation succeeded correctly.

Check applicable:

- base branch
- head branch
- title
- body
- changed files
- commits
- draft state
- CI status

Do not report a pull request as correctly created solely because the create command returned successfully.

## 13. Review Fixes

Treat review feedback as a finding to verify, not an unquestionable instruction.

For each meaningful finding:

1. inspect the relevant current code
2. reproduce or confirm the problem
3. determine whether it is valid
4. fix the underlying issue
5. add regression coverage when appropriate
6. rerun relevant verification

Do not change correct behavior merely to satisfy an incorrect automated review.

Do not resolve a review concern solely because some code around it changed.

## 14. CHANGELOG and Documentation

Material behavior changes should update `CHANGELOG.md` in the same change set.

Documentation must remain synchronized with the resulting implementation.

Do not create a later generic commit such as:

```text
docs: update docs
```

when the documentation logically belongs to the change that introduced the behavior.

Prefer keeping related documentation with the implementation when that produces clearer history.

## 15. GitHub Mutations

Git and GitHub mutations require explicit user intent.

Do not independently:

- create commits
- push branches
- create issues
- edit issues
- open pull requests
- edit pull requests
- merge pull requests
- close issues
- create releases
- create tags
- force-push

unless the user asked for that action.

Permission to perform one mutation does not automatically authorize unrelated mutations.

For example, permission to create a commit does not automatically mean permission to push it.

## 16. Reporting State

Report repository state precisely.

Distinguish:

```text
implemented locally
verified locally
committed
pushed
pull request opened
CI passed
Preview deployed
Preview verified
Production deployed
Production verified
```

Do not use an earlier state as evidence for a later state.

Examples:

- a commit does not mean it was pushed
- a push does not mean CI passed
- green CI does not mean Preview behavior works
- Preview success does not mean Production is updated

Avoid vague completion statements when exact state matters.

## 17. Safety

When unsure whether a Git operation could destroy or hide existing work, stop the mutation and inspect the state first.

Prefer a reversible operation over an irreversible one.

Do not use destructive commands merely because they are faster.

Be especially careful with:

```text
git reset --hard
git clean
git checkout -- <file>
git restore
git rebase
git push --force
```

These operations require understanding what existing work they affect.

## 18. Final Check

A Git change is ready for handoff when:

- the branch scope is coherent
- commits are logically scoped
- commit messages follow the convention
- unrelated user work is preserved
- intended verification has been performed
- documentation is current
- `CHANGELOG.md` is current when applicable
- the final diff contains no accidental files
- remote state is reported accurately