# Development Harness (开发防护体系)

## Purpose

This rule defines the global development harness that automatically enforces code quality, architecture boundaries, and feature discipline across ALL projects.

## How It Works

The harness is a **6-layer defense system** that runs automatically:

```
User Input
    │
    ▼
┌─────────────────────────────────────────────────────────────────┐
│                    Layer 0: Feature Gate (HARD-GATE)              │
│                    (feature-gate.md)                              │
│                                                                   │
│  Before ANY implementation:                                       │
│  1. 探索项目上下文                                                │
│  2. 逐个提问（一次一个问题）                                     │
│  3. 提出 2-3 种方案                                               │
│  4. 分段展示设计 → 用户确认                                       │
│  5. 写设计文档 → 自查 → 用户审核                                 │
│  6. 写实施计划（每步 2-5 分钟，含完整代码）                       │
│  7. TDD 实施                                                      │
│  8. 验证（证据先于声明）                                          │
│                                                                   │
│  ❌ 不问清楚不写代码                                              │
│  ❌ 不确认设计不动手                                              │
│  ❌ 没有证据不说"完成"                                            │
└─────────────────────────────────────────────────────────────────┘
    │
    ▼
┌─────────────────────────────────────────────────────────────────┐
│                    Layer 1: Feature Discipline                    │
│                    (feature-discipline.md)                        │
│                                                                   │
│  Before ANY new feature/tool/module:                              │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │ Q1: Is there a spec/issue?                                  │ │
│  │     NO → Write spec first                                   │ │
│  │ Q2: Does it serve current goal?                             │ │
│  │     NO → Add to backlog, DON'T implement                    │ │
│  │ Q3: Can it be done in < 300 lines?                          │ │
│  │     NO → Split into phases                                  │ │
│  │ Q4: Does it modify > 3 files?                               │ │
│  │     YES → Get review first                                  │ │
│  └─────────────────────────────────────────────────────────────┘ │
│                                                                   │
│  Anti-patterns BLOCKED:                                           │
│  • "顺便加个..." → REJECT                                        │
│  • "以后可能用到" → REJECT (YAGNI)                               │
│  • "也不错" → REJECT (no spec)                                   │
└─────────────────────────────────────────────────────────────────┘
    │ PASS
    ▼
┌─────────────────────────────────────────────────────────────────┐
│                    Layer 2: Code Generation                       │
│                    (coding-style.md)                              │
│                                                                   │
│  During code writing:                                             │
│  • Immutable patterns (new objects, never mutate)                 │
│  • KISS / DRY / YAGNI                                            │
│  • Functions < 50 lines                                          │
│  • Files < 500 lines (800 max)                                   │
│  • No deep nesting (> 4 levels)                                  │
│  • Explicit error handling                                       │
│  • Input validation at boundaries                                │
└─────────────────────────────────────────────────────────────────┘
    │
    ▼
┌─────────────────────────────────────────────────────────────────┐
│                    Layer 3: Quality Gate                          │
│                    (architecture-guard.md + testing.md)           │
│                                                                   │
│  Before commit:                                                   │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │ 1. ruff check --fix .       (lint + auto-fix)               │ │
│  │ 2. ruff format .            (format)                        │ │
│  │ 3. import-linter check      (architecture boundaries)       │ │
│  │ 4. pytest --cov             (test coverage >= 80%)          │ │
│  └─────────────────────────────────────────────────────────────┘ │
│                                                                   │
│  File size violations:                                            │
│  • > 500 lines → WARN + suggest split                            │
│  • > 800 lines → BLOCK + require split                           │
│  • Function > 50 lines → WARN + suggest extract                  │
└─────────────────────────────────────────────────────────────────┘
    │ PASS
    ▼
┌─────────────────────────────────────────────────────────────────┐
│                    Layer 4: Commit Gate                           │
│                    (hooks.md + .pre-commit-config.yaml)           │
│                                                                   │
│  Pre-commit hooks (automatic):                                    │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │ • ruff-check            Python lint                         │ │
│  │ • ruff-format           Python format                       │ │
│  │ • check-added-large     No files > 500KB                    │ │
│  │ • no-commit-to-branch   Protect main/master                 │ │
│  │ • check-merge-conflict  No conflict markers                 │ │
│  │ • debug-statements      No pdb/breakpoint                   │ │
│  │ • trailing-whitespace   Clean whitespace                    │ │
│  │ • end-of-file-fixer     Proper file endings                 │ │
│  └─────────────────────────────────────────────────────────────┘ │
│                                                                   │
│  Commit size limits:                                              │
│  • feat: max 500 lines changed, 10 files                         │
│  • fix: max 300 lines changed, 8 files                           │
│  • refactor: max 800 lines changed, 20 files                     │
└─────────────────────────────────────────────────────────────────┘
    │ PASS
    ▼
┌─────────────────────────────────────────────────────────────────┐
│                    Layer 5: Documentation Sync                    │
│                    (documentation.md)                             │
│                                                                   │
│  After commit, auto-update docs:                                  │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │ • New module → Update CLAUDE.md file structure              │ │
│  │ • New function → Add docstring with Args/Returns/Raises     │ │
│  │ • Refactor → Update affected docstrings                     │ │
│  │ • Bug fix → Add CHANGELOG entry                             │ │
│  │ • API change → Update docs/api.md                           │ │
│  └─────────────────────────────────────────────────────────────┘ │
│                                                                   │
│  Documentation verification:                                      │
│  • Check for missing docstrings (ruff select D)                  │
│  • Verify CLAUDE.md matches current file structure               │
│  • Verify CHANGELOG has entry for this change                    │
└─────────────────────────────────────────────────────────────────┘
    │ PASS
    ▼
  Commit + Docs Synced ✓
```

## Auto-Setup Protocol (MANDATORY)

**At the start of EVERY session**, Claude MUST run this check:

### Step 1: Verify Harness Configuration

```bash
# Check if harness is configured
check_harness() {
    local missing=()

    # Check for pre-commit config
    if [ ! -f ".pre-commit-config.yaml" ]; then
        missing+=(".pre-commit-config.yaml")
    fi

    # Check for ruff config
    if [ ! -f ".ruff.toml" ] && ! grep -q "\[tool.ruff\]" pyproject.toml 2>/dev/null; then
        missing+=(".ruff.toml")
    fi

    # Check for import-linter (optional but recommended)
    if ! grep -q "importlinter" pyproject.toml 2>/dev/null; then
        echo "⚠️  import-linter not configured (recommended)"
    fi

    # Report
    if [ ${#missing[@]} -eq 0 ]; then
        echo "✅ Harness configured"
    else
        echo "❌ Missing: ${missing[*]}"
        echo "Run: ~/.claude/templates/init-project.sh"
    fi
}
```

### Step 2: Auto-Setup if Missing

If harness is NOT configured, Claude MUST:

1. **Inform user**: "Harness not detected. Setting up automatically..."
2. **Copy templates**:
   ```bash
   cp ~/.claude/templates/.pre-commit-config.yaml ./
   cp ~/.claude/templates/.ruff.toml ./
   ```
3. **Customize**: Update `.ruff.toml` with correct `known-first-party`
4. **Install hooks**:
   ```bash
   pip install pre-commit ruff
   pre-commit install
   ```
5. **Initial run**:
   ```bash
   pre-commit run --all-files
   ```
6. **Report results** to user

### Step 3: Verify Setup

After setup, verify:
```bash
# Verify pre-commit hooks installed
ls .git/hooks/pre-commit

# Verify ruff works
ruff --version

# Verify config is valid
ruff check . --statistics
```

## Auto-Setup Triggers

Claude MUST auto-check harness when:

| Trigger | Action |
|---------|--------|
| Session start (first message) | Run harness check |
| User says "commit" | Verify pre-commit passes |
| User says "new project" | Run full setup |
| User says "init harness" | Run full setup |
| File > 500 lines detected | Warn + suggest split |
| New Python file created | Verify ruff config exists |

## Harness Status Command

Users can ask "harness status" to get:
```
✅ Pre-commit hooks: installed
✅ Ruff config: configured
⚠️  Import-linter: not configured (recommended)
✅ Test coverage: 82%
✅ Last commit: passed all hooks
```

## Enforcement Points

| Action | Check | Enforcement |
|--------|-------|-------------|
| Add feature | Feature discipline 4 questions | BLOCK if no spec |
| Write code | Coding style rules | WARN on violations |
| Create file | File size check | BLOCK if > 800 lines |
| Add import | Architecture boundaries | BLOCK if violates layers |
| Commit | Pre-commit hooks | BLOCK if hooks fail |
| Merge | Test coverage | BLOCK if < 80% |

## Integration with Other Rules

This rule is the **master orchestrator** that references:
- [feature-gate.md](./feature-gate.md) - Layer 0 (HARD-GATE, entry point)
- [feature-structure.md](./feature-structure.md) - Feature folder organization
- [feature-discipline.md](./feature-discipline.md) - Layer 1
- [coding-style.md](./coding-style.md) - Layer 2
- [architecture-guard.md](./architecture-guard.md) - Layer 3
- [testing.md](./testing.md) - Layer 3
- [hooks.md](./hooks.md) - Layer 4
- [code-review.md](./code-review.md) - Post-merge review
- [documentation.md](./documentation.md) - Auto doc maintenance

## Quick Reference

**When user asks to add a feature:**
1. Ask: "Is there a spec for this?"
2. Ask: "Does this serve the current goal?"
3. If both YES → Proceed with implementation
4. If any NO → Push back, suggest writing spec first

**When user asks to commit:**
1. Run `ruff check --fix .`
2. Run `ruff format .`
3. Check file sizes
4. Run `pre-commit run --all-files`
5. If all pass → Commit
6. If failures → Fix and retry

**When creating a new file:**
1. Check line count during creation
2. If approaching 500 lines → Warn
3. If exceeding 500 lines → Suggest splitting
4. If exceeding 800 lines → Block and require split
