# Codex Skills

Skills for [OpenAI Codex CLI](https://github.com/openai/codex). Each skill constrains Codex to follow a proven workflow instead of improvising.

## Install

Copy any `.md` file from `skills/` into your Codex skills directory:

```bash
cp skills/*.md ~/.codex/skills/
```

Or copy one at a time:

```bash
cp skills/codex-quick-fix.md ~/.codex/skills/
```

## Skills

| Skill | Purpose |
|-------|---------|
| [quick-fix](skills/codex-quick-fix.md) | Fix a single bug. Minimal change, run test, commit. |
| [test-writer](skills/codex-test-writer.md) | Generate tests for a file. Detect framework, match patterns, cover edges. |
| [refactor](skills/codex-refactor.md) | Refactor with a safety net. Baseline tests first, then refactor, then verify. |
| [api-scaffolder](skills/codex-api-scaffolder.md) | Scaffold a REST API from a description or OpenAPI spec. |
| [git-hygiene](skills/codex-git-hygiene.md) | Clean up a branch before PR: squash, rebase, fix messages. |
| [dep-audit](skills/codex-dep-audit.md) | Scan deps for vulns, outdated packages, unused imports. |
| [migration](skills/codex-migration.md) | Upgrade a dependency to a new major version. |
| [db-migration](skills/codex-db-migration.md) | Generate ORM migration files from a schema change. |
| [ci-fixer](skills/codex-ci-fixer.md) | Read CI failure logs, fix the code, push, verify green. |
| [self-review](skills/codex-self-review.md) | Review changes before push: security scan, correctness check, test run. |

## How Skills Work

Each file contains a prompt template and a checklist. When you invoke a skill, Codex follows the steps in order instead of guessing. The prompts use exact commands (grep patterns, test runners, git operations) so Codex can't skip steps.

## License

MIT
