# codex-quick-fix

Fix a single bug. Make the smallest change that works. Run the test. Commit.

## Prompt

```
Fix the bug described below. Follow these rules:

1. Read the error message or description.
2. Find the file and line. Read 20 lines of context around it.
3. Make the smallest possible change. Do not refactor. Do not "improve" nearby code.
4. Run the relevant test. If no test exists for this code path, write one.
5. Run the full test suite if one exists.
6. Commit with a message that names the bug and the fix.

Do not touch files unrelated to the bug.
Do not add comments explaining what you changed.
Do not rename variables or reorganize imports.

Bug description: [PASTE ERROR OR DESCRIPTION HERE]
```

## When to Use

- User pastes a traceback or error message
- A test fails and you know which file
- A single behavior is broken

## When Not to Use

- Multiple bugs at once (use multiple quick-fix calls)
- Architecture problems (use refactor)
- You don't know which file has the bug (investigate first)

## What Codex Does Wrong Without This

Codex rewrites three files to fix a typo in one. It renames variables while fixing a null check. It adds defensive code everywhere instead of fixing the one broken path. This skill stops all of that.

## Example

```bash
codex exec "
Fix the bug described below. Follow these rules:

1. Read the error message or description.
2. Find the file and line. Read 20 lines of context around it.
3. Make the smallest possible change. Do not refactor. Do not 'improve' nearby code.
4. Run the relevant test. If no test exists for this code path, write one.
5. Run the full test suite if one exists.
6. Commit with a message that names the bug and the fix.

Do not touch files unrelated to the bug.
Do not add comments explaining what you changed.
Do not rename variables or reorganize imports.

Bug description: TypeError: Cannot read property 'id' of undefined at src/api/users.js:42
"
```
