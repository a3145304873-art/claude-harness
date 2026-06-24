# Feature Structure (功能目录结构)

## Purpose

This rule defines how features are organized. Each feature is a self-contained unit with its code, tests, and documentation in the same folder.

## Core Principle

**Code and docs for the same feature live together.**

## Directory Structure

```
project/
├── features/                          # 所有功能
│   ├── chapter-sorting/               # 功能 1：章节排序
│   │   ├── README.md                  # 功能说明（用户可见）
│   │   ├── design.md                  # 设计文档（技术细节）
│   │   ├── plan.md                    # 实施计划
│   │   ├── chapter_sorter.py          # 实现代码
│   │   ├── test_chapter_sorter.py     # 测试
│   │   └── CHANGELOG.md              # 功能变更记录
│   │
│   ├── knowledge-graph/               # 功能 2：知识图谱
│   │   ├── README.md
│   │   ├── design.md
│   │   ├── plan.md
│   │   ├── graph.py
│   │   ├── test_graph.py
│   │   └── CHANGELOG.md
│   │
│   └── ...                            # 更多功能
│
├── core/                              # 核心模块（非功能）
│   ├── agent_loop.py
│   ├── tools.py
│   └── ...
│
├── CLAUDE.md                          # 项目全局文档
└── README.md                          # 项目说明
```

## Feature Folder Contents

Each feature folder MUST contain:

| File | Purpose | Required |
|------|---------|----------|
| `README.md` | 功能说明（用户可见） | ✅ Yes |
| `design.md` | 设计文档（技术细节） | ✅ Yes |
| `plan.md` | 实施计划 | ✅ Yes |
| `*.py` | 实现代码 | ✅ Yes |
| `test_*.py` | 测试 | ✅ Yes |
| `CHANGELOG.md` | 功能变更记录 | ⚠️ Recommended |

## README.md Template

```markdown
# [Feature Name]

## What is this?

[1-2 sentences describing what this feature does]

## Why?

[What problem does this solve?]

## How to use

[User-facing usage instructions]

## Examples

[Concrete examples with code/output]

## Related

- [Design Document](design.md)
- [Implementation Plan](plan.md)
- [Changelog](CHANGELOG.md)
```

## design.md Template

```markdown
# [Feature Name] Design

## Overview

[Brief description of the design]

## Architecture

[How the feature fits into the system]

## Components

### [Component 1]
- **Purpose:** [what it does]
- **Interface:** [how to use it]
- **Dependencies:** [what it depends on]

### [Component 2]
...

## Data Flow

[How data moves through the feature]

## Error Handling

[What errors can occur and how they're handled]

## Testing Strategy

[How this feature will be tested]

## Alternatives Considered

[Other approaches that were considered and why they were rejected]
```

## plan.md Template

```markdown
# [Feature Name] Implementation Plan

**Goal:** [One sentence describing what this builds]

**Architecture:** [2-3 sentences about approach]

## Tasks

### Task 1: [Component Name]

**Files:**
- Create: `features/feature-name/file.py`
- Test: `features/feature-name/test_file.py`

- [ ] Step 1: Write failing test
- [ ] Step 2: Run test to verify failure
- [ ] Step 3: Write minimal implementation
- [ ] Step 4: Run test to verify pass
- [ ] Step 5: Commit

### Task 2: ...
```

## Feature Naming Convention

```
features/
├── chapter-sorting/       # kebab-case
├── knowledge-graph/
├── style-distiller/
├── golden-finger/
└── detailed-outline/
```

Rules:
- Use **kebab-case** (lowercase with hyphens)
- Be descriptive but concise
- Match the feature's primary noun/verb

## When to Create a New Feature Folder

Create a new feature folder when:

1. **New user-facing functionality** — something users will interact with
2. **Significant scope** — more than 3 files, more than 500 lines total
3. **Independent lifecycle** — can be developed, tested, and released independently

Do NOT create a feature folder for:

1. **Bug fixes** — fix in the existing feature's folder
2. **Small enhancements** — add to the existing feature's folder
3. **Infrastructure changes** — put in `core/` or relevant module

## Integration with Feature Gate

When the feature-gate.md process completes:

1. **Phase 3 (Document):** Save design doc to `features/<feature-name>/design.md`
2. **Phase 4 (Plan):** Save plan to `features/<feature-name>/plan.md`
3. **Phase 5 (Implement):** Write code to `features/<feature-name>/`
4. **Phase 5 (Document):** Write README to `features/<feature-name>/README.md`

## Migration from Current Structure

For existing projects (like Hermes), features are currently in flat files. To migrate:

1. **Identify feature boundaries** — group related files
2. **Create feature folders** — move files into `features/<name>/`
3. **Add documentation** — write README.md, design.md for each
4. **Update imports** — fix all import paths
5. **Update CLAUDE.md** — reflect new structure

## Quick Reference

**Creating a new feature:**
```bash
mkdir -p features/my-feature
# Then follow feature-gate.md process:
# 1. design.md → features/my-feature/design.md
# 2. plan.md → features/my-feature/plan.md
# 3. code → features/my-feature/*.py
# 4. tests → features/my-feature/test_*.py
# 5. README.md → features/my-feature/README.md
```

**Finding a feature's code:**
```bash
# All code for chapter-sorting is in:
features/chapter-sorting/
```

**Finding a feature's docs:**
```bash
# All docs for chapter-sorting are in:
features/chapter-sorting/README.md    # User docs
features/chapter-sorting/design.md    # Technical design
features/chapter-sorting/plan.md      # Implementation plan
```
