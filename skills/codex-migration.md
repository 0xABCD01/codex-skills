# codex-migration

Upgrade a dependency to a new major version. Read the changelog, find breaking changes in your code, apply fixes, run tests.

## Prompt

```
Migrate [PACKAGE] from [CURRENT_VERSION] to [TARGET_VERSION]. Follow these steps:

STEP 1 — READ THE CHANGELOG
Find the changelog or migration guide:
- Check: CHANGELOG.md, UPGRADING.md, MIGRATION.md in the package directory
- Check: node_modules/[package]/CHANGELOG.md
- Check: the package's GitHub releases page
- If none found, run: npm info [package] changelog (or pip show [package])

List every breaking change between current and target version.

STEP 2 — FIND AFFECTED CODE
For each breaking change, search your codebase:
- Changed function signatures: grep for the old signature
- Removed functions: grep for the function name
- Changed defaults: grep for code that relies on the old default
- Renamed config options: grep for the old option name

List each file and line that needs to change.

STEP 3 — APPLY FIXES
For each affected file:
- Update the import if the module path changed
- Update function calls if signatures changed
- Update config if option names changed
- Add the new required options that didn't exist before

Do not change code that is unrelated to this dependency.

STEP 4 — UPDATE THE VERSION
- Node: update package.json, run npm install
- Python: update requirements.txt or pyproject.toml, run pip install
- Go: run go get [package]@[version]
- Rust: update Cargo.toml, run cargo update

STEP 5 — VERIFY
Run the full test suite. If tests fail:
- Check if the failure is from the migration (fix it)
- Check if the failure is pre-existing (note it, don't fix it)

Run the linter if one exists. Fix any new warnings.

STEP 6 — COMMIT
Commit with message: "migrate: [package] [old] → [new]"
If the migration required many changes across files, commit per logical group.

Package: [PACKAGE NAME]
Current version: [CURRENT]
Target version: [TARGET]
```

## When to Use

- User says "upgrade X to version Y"
- A dependency has a security patch in a new major version
- Annual dependency refresh

## When Not to Use

- Minor/patch update (just run the update command)
- No breaking changes between versions

## What Codex Does Wrong Without This

Codex bumps the version number and hopes for the best. It doesn't read the changelog. It runs tests once, sees failures, and starts changing random code. This skill makes Codex read the actual breaking changes first and fix only what's affected.

## Example

```bash
codex exec "
Migrate react from version 17 to 18. Follow these steps:

STEP 1 — READ THE CHANGELOG
Find the changelog or migration guide.
List every breaking change between current and target version.

STEP 2 — FIND AFFECTED CODE
For each breaking change, search your codebase.
List each file and line that needs to change.

STEP 3 — APPLY FIXES
For each affected file, update imports, calls, and config.
Do not change code unrelated to this dependency.

STEP 4 — UPDATE THE VERSION
Update package.json, run npm install.

STEP 5 — VERIFY
Run the full test suite. Fix migration-related failures.
Run the linter. Fix new warnings.

STEP 6 — COMMIT
Commit with message: 'migrate: react 17 → 18'

Package: react
Current version: 17.0.2
Target version: 18.2.0
"
```
