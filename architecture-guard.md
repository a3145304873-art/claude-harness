# Architecture Guard (架构边界强制)

## Purpose

This rule enforces module boundaries and prevents architecture decay. It ensures that code structure remains clean as the project grows.

## Core Principle

**Modules have boundaries. Boundaries are enforced, not suggested.**

## Layer Architecture

Every project should define its layers. Higher layers MAY import lower layers. Lower layers MUST NOT import higher layers.

### Default Layer Model

```
┌─────────────────────────────────────┐
│  Presentation (UI, CLI, API)        │  Highest
├─────────────────────────────────────┤
│  Application (Orchestration)        │
├─────────────────────────────────────┤
│  Domain (Business Logic)            │
├─────────────────────────────────────┤
│  Infrastructure (Storage, External) │  Lowest
└─────────────────────────────────────┘
```

### Import Rules

| Rule | Description |
|------|-------------|
| **Downward only** | Higher layers import lower layers, never the reverse |
| **Same layer OK** | Modules in the same layer can import each other |
| **Skip layers OK** | Presentation can import Infrastructure directly (for now) |
| **No cycles** | Module A imports B, B imports C, C must NOT import A |

## File Size Limits

| File Type | Max Lines | Action if exceeded |
|-----------|-----------|-------------------|
| Single module | 500 | Split into package |
| Single function | 50 | Extract helper functions |
| Single class | 200 | Extract methods or split responsibilities |
| Test file | 400 | Split by feature/layer |

## Module Boundary Enforcement

### Python Projects

Use `import-linter` to enforce boundaries:

```toml
# pyproject.toml
[tool.importlinter]
root_package = "your_package"

[[tool.importlinter.contracts]]
name = "Layer isolation"
type = "layers"
layers = [
    "presentation",
    "application",
    "domain",
    "infrastructure",
]

[[tool.importlinter.contracts]]
name = "No circular imports"
type = "independence"
modules = [
    "presentation",
    "application",
    "domain",
    "infrastructure",
]
```

### Frontend Projects (React/TS)

Use `dependency-cruiser` to enforce boundaries:

```javascript
// .dependency-cruiser.js
module.exports = {
  forbidden: [
    {
      name: "no-circular",
      from: {},
      to: { circular: true },
    },
    {
      name: "presentation-no-import-infrastructure",
      from: { path: "^src/pages" },
      to: { path: "^src/lib/api" },
    },
  ],
};
```

## Architecture Review Checklist

Before merging any PR that changes module structure:

- [ ] No new circular dependencies introduced
- [ ] Layer boundaries respected
- [ ] File size limits not exceeded
- [ ] New modules follow naming conventions
- [ ] Import paths are explicit (no relative imports beyond `./`)

## Refactoring Triggers

When you see these patterns, trigger a refactoring discussion:

| Pattern | Signal | Action |
|---------|--------|--------|
| File > 500 lines | Module is too large | Split into package |
| Function > 50 lines | Function does too much | Extract helpers |
| Circular import | Architecture violation | Restructure dependencies |
| God module | Too many responsibilities | Split by concern |
| Deep nesting (> 4 levels) | Complex logic | Extract sub-functions |

## Integration with Other Rules

This rule works with:
- [coding-style.md](./coding-style.md) - Code quality standards
- [code-review.md](./code-review.md) - Review checklist
- [testing.md](./testing.md) - Test coverage requirements
