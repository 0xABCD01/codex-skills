# codex-git-hygiene

Clean up a branch before opening a PR. Squash fixup commits, rebase on main, fix commit messages.

## Prompt

```
Clean up this branch for PR. Follow these steps:

STEP 1: SHOW CURRENT STATE
Run: git log --oneline main..HEAD
Count the commits. If 1 or fewer, stop and say "nothing to clean up."

STEP 2: IDENTIFY FIXUP COMMITS
Look for commits with these prefixes:
- fixup!
- squash!
- wip
- tmp
- fix
- oops
- typo
- forgot

Also look for commits that modify the same file as the previous commit
(these are likely fixups that should be squashed).

STEP 3: INTERACTIVE REBASE
Run: git rebase -i main
Squash fixup commits into their parent. Keep feature commits separate.

Rules for the rebase:
- "First commit" and "second commit" on the same feature → squash
- "WIP: add auth" + "fix auth" + "actually fix auth" → squash into one
- "Add user model" + "Add task model" → keep separate
- Rename vague messages ("fix stuff", "updates") to describe the change

STEP 4: VERIFY
Run: git log --oneline main..HEAD
Confirm the commit history reads as a clean sequence of logical changes.
Run: git diff main...HEAD --stat
Confirm the total changes haven't changed (rebase didn't lose anything).

STEP 5: REBASE ON LATEST MAIN
Run: git fetch origin main
Run: git rebase origin/main
If conflicts, stop and report them. Do not force-resolve.

STEP 6: REPORT
Show the final commit list. Each message should:
- Start with a verb (add, remove, fix, refactor, update)
- Name the thing changed
- Stay under 72 characters
```

## When to Use

- Before opening a PR
- After a long coding session with many small commits
- When commit history has "fix fix fix" chains

## When Not to Use

- Branch has 1 clean commit
- Branch is already rebased and clean
- Other people's commits are on this branch (rebase rewrites history)

## What Codex Does Wrong Without This

Left to its own devices, Codex squashes the wrong commits during rebase. It force-pushes without asking. When conflicts appear, it picks a side at random. This skill makes it show the current state first, squash by rules (not guesswork), and stop when conflicts appear instead of resolving them blind.

## Example

```bash
codex exec "
Clean up this branch for PR. Follow these steps:

STEP 1: SHOW CURRENT STATE
Run: git log --oneline main..HEAD
Count the commits. If 1 or fewer, stop and say 'nothing to clean up.'

STEP 2: IDENTIFY FIXUP COMMITS
Look for commits with these prefixes:
- fixup!, squash!, wip, tmp, fix, oops, typo, forgot

Also look for commits that modify the same file as the previous commit.

STEP 3: INTERACTIVE REBASE
Run: git rebase -i main
Squash fixup commits into their parent. Keep feature commits separate.

Rules for the rebase:
- Same feature with multiple attempts → squash
- Separate features → keep separate
- Rename vague messages to describe the change

STEP 4: VERIFY
Run: git log --oneline main..HEAD
Confirm the history is clean.
Run: git diff main...HEAD --stat
Confirm no changes were lost.

STEP 5: REBASE ON LATEST MAIN
Run: git fetch origin main
Run: git rebase origin/main
If conflicts, stop and report them. Do not force-resolve.

STEP 6: REPORT
Show the final commit list.
"
```
