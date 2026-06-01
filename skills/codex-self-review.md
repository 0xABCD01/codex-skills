# codex-self-review

Review code changes before pushing. Security scan, correctness check, test run. Three passes, no shortcuts.

## Prompt

```
Review all uncommitted changes. Follow these steps:

STEP 1 — SCOPE
Run: git diff --stat HEAD
Run: git diff HEAD --name-only
List every changed file.

STEP 2 — SECURITY SCAN
Grep the diff for these patterns. Run each command:

Hardcoded secrets:
git diff HEAD | grep -iE 'api_key|secret|password|token|private_key' | grep -v test | grep -v example

Shell injection:
git diff HEAD | grep -E 'os\.system\(|subprocess.*shell=True|exec\(|eval\('

SQL injection:
git diff HEAD | grep -E 'execute\(f\"|\.format\(.*SELECT|\.format\(.*INSERT'

Unsafe deserialization:
git diff HEAD | grep -E 'pickle\.loads?\(|yaml\.load\([^)]*$'

Path traversal:
git diff HEAD | grep -E 'open\(.*\+|os\.path\.join.*request'

For each match, explain: is this a real issue or a false positive?

STEP 3 — CORRECTNESS REVIEW
Read the full diff: git diff HEAD

For each file, check:
- Error paths handled (try/except, error returns, null checks)
- Edge cases covered (empty input, None, large data)
- No off-by-one errors
- External calls (API, DB, file I/O) wrapped in error handling
- No debug prints, console.logs, or TODO comments left behind
- No unused imports or dead code

STEP 4 — TEST VERIFICATION
Detect and run the test framework:
- package.json → npm test
- pyproject.toml → pytest
- go.mod → go test ./...
- Cargo.toml → cargo test

Report pass/fail counts.

STEP 5 — VERDICT
Produce this report:

REVIEW VERDICT: [PASS / FAIL / PASS_WITH_NOTES]

CRITICAL (must fix before push):
- [file:line] description

WARNINGS (should fix):
- [file:line] description

SUGGESTIONS (nice to have):
- [file:line] description

TESTS: [X passed, Y failed / no test framework found]
SECURITY: [CLEAN / issues found]
```

## When to Use

- After building a feature, before pushing
- User says "review your changes" or "check before push"
- Any session that touched 2 or more files

## When Not to Use

- One-line config change
- User says "just push it"

## What Codex Does Wrong Without This

Codex says "looks good" without checking. It skips the security grep. It runs tests but ignores failures. This skill forces three separate checks and a structured report.

## Example

```bash
codex exec "
Review all uncommitted changes. Follow these steps:

STEP 1 — SCOPE
Run: git diff --stat HEAD
List every changed file.

STEP 2 — SECURITY SCAN
Grep the diff for:
- Hardcoded secrets (api_key, secret, password, token)
- Shell injection (os.system, subprocess shell=True)
- SQL injection (f-strings in execute())
- Unsafe deserialization (pickle.loads, yaml.load)
- Path traversal (unsanitized file paths)

For each match, explain if it's real or false positive.

STEP 3 — CORRECTNESS REVIEW
Read the full diff. For each file check:
- Error handling, edge cases, off-by-one, debug statements, unused imports.

STEP 4 — TEST VERIFICATION
Detect and run tests. Report pass/fail.

STEP 5 — VERDICT
Report: CRITICAL / WARNINGS / SUGGESTIONS / TESTS / SECURITY / SUMMARY
"
```
