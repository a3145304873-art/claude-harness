# Feature Discipline (功能蔓延防护)

## Purpose

This rule prevents feature creep by enforcing disciplined feature development. Every new feature must justify its existence before implementation.

## The Problem

Without discipline, projects accumulate features like:
- "顺便加个..." (adding features "while we're at it")
- "以后可能会用到" (YAGNI violations)
- "这个也不错" (scope creep during implementation)
- Features that serve no current user story

## Feature Gate

**BEFORE implementing ANY new feature, tool, or module, answer these 4 questions:**

1. **Is there a spec/issue/story for this?**
   - If NO → Write one first. Describe the user story, acceptance criteria, and scope.
   - If YES → Proceed to next question.

2. **Does this serve the CURRENT release goal?**
   - If NO → Add to backlog, do NOT implement now.
   - If YES → Proceed to next question.

3. **Can this be implemented in < 300 lines?**
   - If NO → Split into phases. Phase 1 must be < 300 lines.
   - If YES → Proceed to next question.

4. **Does this require modifying > 3 existing files?**
   - If YES → Pause. This is a high-impact change. Get review first.
   - If NO → Proceed with implementation.

## Anti-Patterns to REJECT

When you see these patterns in code or conversation, STOP and push back:

| Pattern | Example | Response |
|---------|---------|----------|
| 顺便加 | "顺便加个日志功能" | "不顺。写 spec 先。" |
| 以后可能 | "以后可能需要支持多语言" | "YAGNI。现在不需要就不加。" |
| 也不错 | "加个缓存也不错" | "有 spec 吗？没有就不加。" |
| 快速加 | "很快就能加完" | "快也要有 spec。" |
| 已经在改了 | "反正已经在改这个文件了" | "那也写 spec。" |

## Feature Consolidation Rule

When adding a new tool/feature, ask:
- Can this be merged with an existing tool/feature?
- Can an existing tool be extended instead of creating a new one?

**Goal**: Reduce tool count, not increase it. Every new tool is a maintenance burden.

## Commit Size Limits

| Commit Type | Max Lines Changed | Max Files |
|-------------|-------------------|-----------|
| feat | 500 | 10 |
| fix | 300 | 8 |
| refactor | 800 | 20 |
| initial commit | No limit (only for project setup) | - |

**If your change exceeds these limits, split it into multiple commits.**

## Enforcement

- **In conversation**: Claude should push back when user asks to add features without spec.
- **In code review**: Flag any commit that exceeds size limits.
- **In pre-commit hooks**: `check-added-large-files` prevents oversized commits.

## Integration with Other Rules

This rule works with:
- [development-workflow.md](./development-workflow.md) - Plan before coding
- [testing.md](./testing.md) - Tests for new features
- [code-review.md](./code-review.md) - Review before merge
