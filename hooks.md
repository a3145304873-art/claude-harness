# Hooks System

## Hook Types

- **PreToolUse**: Before tool execution (validation, parameter modification)
- **PostToolUse**: After tool execution (auto-format, checks)
- **Stop**: When session ends (final verification)

## Auto-Accept Permissions

Use with caution:
- Enable for trusted, well-defined plans
- Disable for exploratory work
- Never use dangerously-skip-permissions flag
- Configure `allowedTools` in `~/.claude.json` instead

## TodoWrite Best Practices

Use TodoWrite tool to:
- Track progress on multi-step tasks
- Verify understanding of instructions
- Enable real-time steering
- Show granular implementation steps

Todo list reveals:
- Out of order steps
- Missing items
- Extra unnecessary items
- Wrong granularity
- Misinterpreted requirements

## Git Hooks (Pre-commit)

**MANDATORY for all projects**: Before any commit, ensure pre-commit hooks are installed and passing.

### Setup Requirements

Every project MUST have:

1. **`.pre-commit-config.yaml`** with at minimum:
   - `ruff-check` (Python linting)
   - `ruff-format` (Python formatting)
   - `check-added-large-files` (prevent oversized commits)
   - `no-commit-to-branch` (prevent direct commits to main)

2. **`.ruff.toml`** or `[tool.ruff]` in `pyproject.toml` with:
   - `line-length = 88`
   - `target-version = "py310"` (or appropriate)
   - Sensible rule selections

### Pre-commit Workflow

```
1. User asks to commit code
2. Claude checks if .pre-commit-config.yaml exists
3. If not → Create it from template (see ~/.claude/templates/)
4. Run `pre-commit run --all-files`
5. If failures → Fix issues before committing
6. If passes → Proceed with commit
```

### Claude Code Integration

When using Claude Code to commit:

1. **Before commit**: Always run `ruff check --fix .` and `ruff format .`
2. **During commit**: Pre-commit hooks run automatically
3. **After commit**: Verify hooks passed

## Architecture Hooks

### Import Boundary Check

Before merging code that changes imports:

1. Check if `import-linter` is configured
2. If yes → Run `lint-imports` to verify boundaries
3. If no → Warn user to set up boundary enforcement

### File Size Check

Before creating or modifying files:

1. Check file line count
2. If > 500 lines → Warn and suggest splitting
3. If > 800 lines → **BLOCK** and require splitting

## Code Quality Hooks

### Ruff Integration

```bash
# Always run before committing
ruff check --fix .
ruff format .
```

### Type Checking (if configured)

```bash
# Run if pyright/mypy is configured
pyright .  # or mypy .
```

### Test Gate

```bash
# Run tests before merge
pytest --cov=. --cov-fail-under=80
```

## Hook Enforcement Levels

| Level | Description | When to Use |
|-------|-------------|-------------|
| **WARN** | Show warning, allow proceed | Style issues, suggestions |
| **ERROR** | Show error, require fix | Lint failures, test failures |
| **BLOCK** | Prevent action entirely | Security issues, architecture violations |

## Integration with Other Rules

This rule works with:
- [feature-discipline.md](./feature-discipline.md) - Feature creep prevention
- [architecture-guard.md](./architecture-guard.md) - Module boundary enforcement
- [testing.md](./testing.md) - Test coverage requirements
- [security.md](./security.md) - Security checks
