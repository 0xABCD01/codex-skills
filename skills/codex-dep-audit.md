# codex-dep-audit

Scan dependencies for vulnerabilities, outdated packages, and unused imports. Works for Python, Node, Go, Rust.

## Prompt

```
Audit the dependencies in this project. Follow these steps:

STEP 1 — DETECT LANGUAGE AND PACKAGE MANAGER
Check for: package.json, requirements.txt, pyproject.toml, go.mod, Cargo.toml.
Report which one you found.

STEP 2 — VULNERABILITY SCAN
Run the appropriate scanner:
- Node: npm audit (or yarn audit / pnpm audit)
- Python: pip-audit (install if missing: pip install pip-audit)
- Go: govulncheck ./... (install if missing: go install golang.org/x/vuln/cmd/govulncheck@latest)
- Rust: cargo audit (install if missing: cargo install cargo-audit)

Report each vulnerability:
- Package name and installed version
- Severity (critical/high/medium/low)
- CVE number if available
- Fixed version (what to upgrade to)

STEP 3 — OUTDATED PACKAGES
Check for outdated packages:
- Node: npm outdated
- Python: pip list --outdated
- Go: go list -m -u all | grep '\['
- Rust: cargo outdated (install if needed)

Report: current version → latest version for each outdated package.
Flag breaking changes (major version bumps) separately from safe updates.

STEP 4 — UNUSED DEPENDENCIES
Check for imported-but-not-installed and installed-but-not-imported packages:
- Node: scan source files for require() and import statements, compare to package.json
- Python: scan for import statements, compare to requirements.txt/pyproject.toml
- Go: run go mod tidy and check what changed

Report unused deps and missing deps separately.

STEP 5 — SUMMARY
Produce a report:

VULNERABILITIES: X found (Y critical, Z high, M medium, N low)
OUTDATED: X packages (Y major, Z minor)
UNUSED: X packages can be removed
MISSING: X packages are imported but not declared

For each critical/high vulnerability, suggest the upgrade command.
```

## When to Use

- Before deploying to production
- After adding new dependencies
- Monthly maintenance check
- When a CVE is announced for a package you use

## When Not to Use

- Project has no dependencies
- User only wants to check one specific package

## What Codex Does Wrong Without this

Codex runs `npm audit` and dumps raw JSON. It doesn't distinguish major from minor updates. It suggests upgrading everything at once, which breaks things. This skill separates safe updates from breaking ones and gives specific commands.

## Example

```bash
codex exec "
Audit the dependencies in this project. Follow these steps:

STEP 1 — DETECT LANGUAGE AND PACKAGE MANAGER
Check for: package.json, requirements.txt, pyproject.toml, go.mod, Cargo.toml.
Report which one you found.

STEP 2 — VULNERABILITY SCAN
Run the appropriate scanner:
- Node: npm audit (or yarn audit / pnpm audit)
- Python: pip-audit (install if missing: pip install pip-audit)
- Go: govulncheck ./... (install if missing: go install golang.org/x/vuln/cmd/govulncheck@latest)
- Rust: cargo audit (install if missing: cargo install cargo-audit)

Report each vulnerability:
- Package name and installed version
- Severity (critical/high/medium/low)
- CVE number if available
- Fixed version (what to upgrade to)

STEP 3 — OUTDATED PACKAGES
Check for outdated packages:
- Node: npm outdated
- Python: pip list --outdated
- Go: go list -m -u all | grep \\[\\
- Rust: cargo outdated (install if needed)

Report: current version → latest version for each outdated package.
Flag breaking changes (major version bumps) separately from safe updates.

STEP 4 — UNUSED DEPENDENCIES
Check for imported-but-not-installed and installed-but-not-imported packages.
Report unused deps and missing deps separately.

STEP 5 — SUMMARY
Produce a report:

VULNERABILITIES: X found (Y critical, Z high, M medium, N low)
OUTDATED: X packages (Y major, Z minor)
UNUSED: X packages can be removed
MISSING: X packages are imported but not declared

For each critical/high vulnerability, suggest the upgrade command.
"
```
