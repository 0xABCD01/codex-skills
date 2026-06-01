# codex-test-writer

Generate tests for a source file. Detect the test framework. Match existing patterns. Cover edge cases.

## Prompt

```
Write tests for the file(s) listed below. Follow these steps:

STEP 1: DETECT FRAMEWORK
Check for these files in the project root:
- package.json → use the test script, or jest/vitest if configured
- pyproject.toml or setup.cfg → use pytest
- go.mod → use go test
- Cargo.toml → use cargo test
If no framework found, stop and report which file you checked.

STEP 2: FIND EXISTING TESTS
Search for existing test files (test_*, *_test.*, *.test.*, *_spec.*).
Read one example test file to learn the project's patterns:
- Import style
- Naming conventions
- Assertion style
- Fixture/setup patterns

STEP 3: READ THE SOURCE
Read the target file fully. Identify:
- Every public function/method
- Each branch (if/else, switch, early return)
- Error paths (throws, error returns, None/null checks)
- Edge cases (empty input, zero, negative, very large, None/null)

STEP 4: WRITE TESTS
For each public function, write:
- Happy path test (normal input, expected output)
- Edge case test (boundary values)
- Error test (invalid input raises/returns error)

Follow the existing test patterns from Step 2. Use the same import style,
assertion style, and naming conventions.

STEP 5: RUN TESTS
Run the test suite. If any new tests fail, fix them.
Report: X tests written, Y passed, Z failed.
```

## When to Use

- User says "write tests for this file"
- After building a feature with no tests
- Before a refactor (tests provide safety net)

## When Not to Use

- File already has good test coverage
- User wants to skip tests ("ship it")

## What Codex Does Wrong Without This

Codex picks pytest when the project uses unittest. It invents fixture patterns instead of copying what exists. It covers the happy path and skips error cases. Names end up as `test_function_1` instead of `test_returns_error_when_input_is_empty`. This skill makes Codex read an existing test file first and match its style.

## Example

```bash
codex exec "
Write tests for the file(s) listed below. Follow these steps:

STEP 1: DETECT FRAMEWORK
Check for these files in the project root:
- package.json → use the test script, or jest/vitest if configured
- pyproject.toml or setup.cfg → use pytest
- go.mod → use go test
- Cargo.toml → use cargo test
If no framework found, stop and report which file you checked.

STEP 2: FIND EXISTING TESTS
Search for existing test files (test_*, *_test.*, *.test.*, *_spec.*).
Read one example test file to learn the project's patterns:
- Import style
- Naming conventions
- Assertion style
- Fixture/setup patterns

STEP 3: READ THE SOURCE
Read the target file fully. Identify:
- Every public function/method
- Each branch (if/else, switch, early return)
- Error paths (throws, error returns, None/null checks)
- Edge cases (empty input, zero, negative, very large, None/null)

STEP 4: WRITE TESTS
For each public function, write:
- Happy path test (normal input, expected output)
- Edge case test (boundary values)
- Error test (invalid input raises/returns error)

Follow the existing test patterns from Step 2. Use the same import style,
assertion style, and naming conventions.

STEP 5: RUN TESTS
Run the test suite. If any new tests fail, fix them.
Report: X tests written, Y passed, Z failed.

File: src/utils/parser.py
"
```
