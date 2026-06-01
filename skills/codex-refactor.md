# codex-refactor

Refactor code with a safety net. Run existing tests before touching anything. Refactor. Verify tests still pass.

## Prompt

```
Refactor the target below. Follow this sequence:

STEP 1: BASELINE
Run the full test suite. Record pass/fail counts.
If tests fail before you touch anything, stop and report the failures.
Do not refactor code that already has failing tests.

STEP 2: PLAN
Read the target file(s). Identify:
- What to change and why
- Which functions/classes are affected
- Whether tests cover the affected code paths

State your plan in 3 sentences or fewer.

STEP 3: REFACTOR
Apply the change. Rules:
- Do not change behavior. Refactoring means same input, same output.
- Do not add features.
- Do not change function signatures unless you update all call sites.
- Do not rename public APIs.
- Keep the diff small. If you changed more than 200 lines, split it.

STEP 4: VERIFY
Run the full test suite again. Compare to baseline.
- Same pass count: good.
- More passes: you fixed something, note it.
- Fewer passes: you broke something. Revert and try again.

STEP 5: COMMIT
If tests pass, commit. Message format: "refactor: <what changed>"
If tests fail, revert with git checkout and report what broke.
```

## When to Use

- User says "refactor this module" or "clean up this code"
- Code works but is hard to read or maintain
- Before adding features to tangled code

## When Not to Use

- Code is broken (use quick-fix)
- You want to change behavior (that's a feature, not a refactor)
- No tests exist (write tests first with test-writer, then refactor)

## What Codex Does Wrong Without This

Without guardrails, Codex breaks tests and then "fixes" them to match the new behavior. It renames variables nobody asked about. It extracts functions that add no value. It changes the public API. This skill runs tests before and after, compares the results, and reverts on any regression.

## Example

```bash
codex exec "
Refactor the target below. Follow this sequence:

STEP 1: BASELINE
Run the full test suite. Record pass/fail counts.
If tests fail before you touch anything, stop and report the failures.
Do not refactor code that already has failing tests.

STEP 2: PLAN
Read the target file(s). Identify:
- What to change and why
- Which functions/classes are affected
- Whether tests cover the affected code paths

State your plan in 3 sentences or fewer.

STEP 3: REFACTOR
Apply the change. Rules:
- Do not change behavior. Refactoring means same input, same output.
- Do not add features.
- Do not change function signatures unless you update all call sites.
- Do not rename public APIs.
- Keep the diff small. If you changed more than 200 lines, split it.

STEP 4: VERIFY
Run the full test suite again. Compare to baseline.
- Same pass count: good.
- More passes: you fixed something, note it.
- Fewer passes: you broke something. Revert and try again.

STEP 5: COMMIT
If tests pass, commit. Message format: 'refactor: <what changed>'
If tests fail, revert with git checkout and report what broke.

Target: src/services/payment.py
"
```
