# Documentation Maintenance (文档维护)

## Purpose

This rule ensures documentation is written **as part of feature development**, not after. Documentation is a deliverable, not an afterthought.

## Core Principle

**Documentation is part of the feature. No docs = feature incomplete.**

## When Claude Implements a Feature

When user asks Claude to implement a feature, Claude MUST deliver ALL of these:

```
Feature Implementation Deliverables:
├── Code (the implementation)
├── Tests (unit + integration)
├── Documentation ← THIS IS MANDATORY
│   ├── Docstrings (for every new function/class/module)
│   ├── CLAUDE.md update (if file structure changed)
│   ├── CHANGELOG entry (for every user-facing change)
│   └── README update (if setup/usage changed)
└── Commit (with all above included)
```

## Auto-Documentation Rules

### Rule 1: Write Docstrings WITH Code

When writing a new function, Claude MUST write the docstring **in the same response**, not as a follow-up.

```python
# ❌ WRONG: Write code first, add docstring later
def process_chapter(chapter_id, content):
    # ... implementation ...
    return result

# ✅ CORRECT: Write code and docstring together
def process_chapter(chapter_id: str, content: str) -> dict:
    """
    Process a chapter's content and update related systems.
    
    This function handles the complete chapter processing pipeline:
    1. Validates the content
    2. Saves to storage
    3. Updates knowledge graph
    4. Triggers memory sedimentation
    
    Args:
        chapter_id: Unique identifier for the chapter
        content: Raw markdown content of the chapter
    
    Returns:
        dict with keys:
            - success (bool): Whether processing succeeded
            - word_count (int): Number of words in chapter
            - entities_found (list): Extracted entities
    
    Raises:
        ValueError: If chapter_id is empty
        StorageError: If save fails
    
    Example:
        >>> result = process_chapter("ch-001", "# Chapter 1\\nContent...")
        >>> print(result['success'])
        True
    """
    # ... implementation ...
```

### Rule 2: Update CLAUDE.md WITH Structure Changes

When creating a new file or module, Claude MUST update CLAUDE.md **in the same response**.

```markdown
# ❌ WRONG: Create file, forget to update CLAUDE.md

# ✅ CORRECT: Create file AND update CLAUDE.md together

## File Structure (in CLAUDE.md)
```
project/
├── features/
│   ├── new_feature/       # NEW: Brief description
│   │   ├── README.md      # Feature documentation
│   │   ├── design.md      # Technical design
│   │   ├── plan.md        # Implementation plan
│   │   ├── processor.py   # Implementation
│   │   └── test_processor.py  # Tests
│   └── existing_feature/
│       └── ...
├── core/
│   └── ...
```
```

See [feature-structure.md](./feature-structure.md) for the complete feature folder structure.

### Rule 3: Add CHANGELOG Entry WITH Fix/Feature

When fixing a bug or adding a feature, Claude MUST add a CHANGELOG entry **in the same response**.

```markdown
# ❌ WRONG: Fix bug, no changelog entry

# ✅ CORRECT: Fix bug AND add changelog entry

## [Unreleased]

### Fixed
- Chapter save now correctly updates version snapshot (issue #123)

### Added
- New `process_chapter` function for centralized chapter handling
```

## Documentation Types

| Type | Location | When to Write | Auto? |
|------|----------|---------------|-------|
| **Docstrings** | In code | With every function/class | ✅ Yes |
| **CLAUDE.md** | Project root | With every structure change | ✅ Yes |
| **CHANGELOG** | `CHANGELOG.md` | With every fix/feature | ✅ Yes |
| **README** | `README.md` | With setup/usage changes | ✅ Yes |
| **Architecture** | `docs/architecture.md` | With structural changes | ⚠️ Manual |
| **API docs** | Auto-generated | With endpoint changes | ⚠️ Manual |

## Documentation Standards

### Module Docstring

Every Python module MUST have a docstring:

```python
"""
Module Name

Brief description of what this module does.

Key Classes/Functions:
    - ClassName: Brief description
    - function_name: Brief description

Usage:
    from module import ClassName
    
    instance = ClassName()
    result = instance.method()
"""
```

### Function Docstring

Every public function MUST have a docstring:

```python
def function_name(param1: str, param2: int) -> bool:
    """
    Brief description of what this function does.
    
    Args:
        param1: Description of param1
        param2: Description of param2
    
    Returns:
        Description of return value
    
    Raises:
        ValueError: When param1 is empty
        TypeError: When param2 is not positive
    
    Example:
        >>> function_name("test", 42)
        True
    """
```

### CLAUDE.md Updates

When module structure changes, update CLAUDE.md:

```markdown
## File Structure

```
project/
├── module_a/          # NEW: Description
│   ├── __init__.py
│   ├── core.py        # Core functionality
│   └── utils.py       # Utilities
├── module_b/          # Description
│   └── ...
└── ...
```
```

## Documentation Verification

Before committing, verify:

```bash
# Check for missing docstrings
ruff select D --statistics

# Verify CLAUDE.md matches current file structure
# (manual check - compare file tree with CLAUDE.md section)
```

## Documentation as Code

- Documentation lives in the same repository as code
- Documentation is versioned with code
- Documentation is reviewed in PRs
- Documentation is tested (link checking, example verification)

## Integration with Other Rules

This rule works with:
- [coding-style.md](./coding-style.md) - Code quality includes docs
- [code-review.md](./code-review.md) - Review includes doc updates
- [git-workflow.md](./git-workflow.md) - Commit messages as docs

## Quick Reference

**When you add a new file:**
1. Add module docstring
2. Update CLAUDE.md file structure
3. Add CHANGELOG entry

**When you add a new function:**
1. Add function docstring with Args/Returns/Raises
2. Add type hints
3. Add example if public API

**When you refactor:**
1. Update all affected docstrings
2. Update CLAUDE.md if structure changed
3. Update architecture docs if needed

**When you commit:**
1. Verify docstrings present
2. Verify CLAUDE.md current
3. Verify CHANGELOG updated
