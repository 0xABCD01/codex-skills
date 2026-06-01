# codex-ci-fixer

Read CI failure logs, find the root cause, fix the code, push, verify the next run passes.

## Prompt

```
CI is failing. Fix it. Follow these steps:

STEP 1 — GET THE FAILURE LOG
Run: gh run list --limit 5
Find the most recent failed run. Get its ID.
Run: gh run view [RUN_ID] --log-failed

If gh is not available, ask the user to paste the failure log.

STEP 2 — IDENTIFY THE ROOT CAUSE
Read the failure log. Find the FIRST error (not warnings, not info).
Categorize it:
- Test failure: a specific test assertion failed
- Lint error: code style violation
- Type error: type checker found an issue
- Build error: compilation or bundling failed
- Missing dep: a package isn't installed
- Config error: CI config has a problem

Name the file, line number, and exact error message.

STEP 3 — READ THE FAILING CODE
Read the file that caused the failure. Read 30 lines of context.
Determine: did your recent change cause this, or was it pre-existing?

Run: git log --oneline -5
Check if the failure relates to the latest commits.

STEP 4 — FIX
Apply the smallest fix that resolves the error.

Rules:
- For test failures: fix the code, not the test (unless the test is wrong)
- For lint errors: run the linter's auto-fix (ruff --fix, eslint --fix, etc.)
- For type errors: add the missing type annotation or fix the type
- For missing deps: add the dependency to the package file
- For config errors: fix the CI config file

Do not change code unrelated to the failure.

STEP 5 — VERIFY LOCALLY
Run the same check that CI runs:
- If pytest failed: run pytest
- If lint failed: run the linter
- If type check failed: run mypy/tsc
- If build failed: run the build command

Confirm it passes locally before pushing.

STEP 6 — PUSH AND VERIFY
Run: git add -A && git commit -m 'fix ci: <what you fixed>'
Run: git push

Wait for CI to start:
Run: gh run list --limit 1
If still running, wait and check again.

Report the final status: pass or fail.
If it fails again, repeat from Step 1 with the new log.
```

## When to Use

- CI is red and you need to fix it
- A PR's checks are failing
- After merging and the main branch CI breaks

## When Not to Use

- CI failure is from an external service outage
- Failure is in a deployment step (not a code issue)

## What Codex Does Wrong Without This

Codex reads the last line of the log (usually a summary) and guesses. It changes the test to pass instead of fixing the code. It pushes without running the check locally first, so CI fails again. This skill forces Codex to read the actual error, fix locally, verify, then push.

## Example

```bash
codex exec "
CI is failing. Fix it. Follow these steps:

STEP 1 — GET THE FAILURE LOG
Run: gh run list --limit 5
Find the most recent failed run. Get its ID.
Run: gh run view [RUN_ID] --log-failed

STEP 2 — IDENTIFY THE ROOT CAUSE
Read the failure log. Find the FIRST error.
Categorize it: test failure, lint error, type error, build error, missing dep, config error.
Name the file, line number, and exact error message.

STEP 3 — READ THE FAILING CODE
Read the file that caused the failure with 30 lines of context.
Determine if your recent change caused this.

STEP 4 — FIX
Apply the smallest fix. Rules:
- Fix the code, not the test (unless the test is wrong)
- For lint: run auto-fix
- Do not change unrelated code

STEP 5 — VERIFY LOCALLY
Run the same check CI runs. Confirm it passes.

STEP 6 — PUSH AND VERIFY
Commit and push. Check CI status.
If it fails again, repeat from Step 1.
"
```
