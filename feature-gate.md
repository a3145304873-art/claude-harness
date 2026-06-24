# Feature Gate (功能门控 — 先问清楚再动手)

> Inspired by [obra/Superpowers](https://github.com/obra/Superpowers) brainstorming + writing-plans skills.

## Purpose

This rule ensures Claude NEVER jumps straight into coding. Every feature goes through a structured conversation first: understand → design → plan → implement → verify.

## HARD-GATE (硬门控)

```
Do NOT write any code, create any file, or take any implementation action
until you have:
1. Asked clarifying questions
2. Presented a design
3. Got user approval
4. Written an implementation plan

This applies to EVERY feature regardless of perceived simplicity.
"Simple" projects are where unexamined assumptions cause the most wasted work.
```

## The Complete Flow

```
User: "帮我加个 XXX 功能"
    │
    ▼
┌─────────────────────────────────────────────────────────────────┐
│  Phase 1: 理解 (Understand)                                      │
│                                                                   │
│  1. 探索项目上下文                                                │
│     • 检查相关文件和目录                                          │
│     • 查看最近提交                                                │
│     • 阅读相关文档                                                │
│                                                                   │
│  2. 逐个提问（一次只问一个问题）                                  │
│     • 优先用选择题，开放题也可以                                  │
│     • 聚焦：目的、约束、成功标准                                  │
│     • 评估范围：如果太大，先拆分子项目                            │
│                                                                   │
│  示例问题：                                                       │
│  "这个功能主要解决什么问题？"                                     │
│  "你希望用户怎么触发它？"                                         │
│  "有没有参考的实现？"                                             │
└─────────────────────────────────────────────────────────────────┘
    │
    ▼
┌─────────────────────────────────────────────────────────────────┐
│  Phase 2: 设计 (Design)                                          │
│                                                                   │
│  3. 提出 2-3 种方案                                               │
│     • 每种方案说明优缺点                                          │
│     • 给出推荐方案和理由                                          │
│     • 让用户选择                                                  │
│                                                                   │
│  4. 分段展示设计                                                  │
│     • 架构、组件、数据流、错误处理、测试                          │
│     • 每段展示后问"这部分看起来对吗？"                            │
│     • 准备好回头澄清                                              │
│                                                                   │
│  设计原则：                                                       │
│  • 每个单元有单一职责                                             │
│  • 通过明确接口通信                                               │
│  • 可独立理解和测试                                               │
│  • 文件增长到 500 行是信号——它做太多了                             │
└─────────────────────────────────────────────────────────────────┘
    │ 用户确认设计
    ▼
┌─────────────────────────────────────────────────────────────────┐
│  Phase 3: 文档 (Document)                                        │
│                                                                   │
│  5. 写设计文档                                                    │
│     • 保存到 features/<feature-name>/design.md                   │
│     • 提交到 git                                                  │
│                                                                   │
│  6. 设计自查                                                      │
│     • 占位符扫描：有 TBD/TODO/模糊需求吗？修复。                  │
│     • 内部一致性：各部分有矛盾吗？                                │
│     • 范围检查：能在一个计划内完成吗？                            │
│     • 歧义检查：有需求可以有两种解读吗？明确化。                  │
│                                                                   │
│  7. 用户审核设计文档                                              │
│     • "设计文档已保存到 <path>，请审核后我们再写实施计划。"       │
│     • 等待用户确认                                                │
└─────────────────────────────────────────────────────────────────┘
    │ 用户确认文档
    ▼
┌─────────────────────────────────────────────────────────────────┐
│  Phase 4: 计划 (Plan)                                            │
│                                                                   │
│  8. 写实施计划                                                    │
│     • 保存到 features/<feature-name>/plan.md                     │
│     • 每个任务 2-5 分钟可完成                                     │
│     • 每步包含完整代码（无占位符）                                │
│     • 精确文件路径                                                │
│     • 精确命令 + 预期输出                                         │
│                                                                   │
│  计划结构：                                                       │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │ # [功能名] 实施计划                                          │ │
│  │                                                               │ │
│  │ **目标：**[一句话描述]                                        │ │
│  │ **架构：**[2-3 句话描述方案]                                  │ │
│  │ **技术栈：**[关键技术/库]                                     │ │
│  │                                                               │ │
│  │ ### Task 1: [组件名]                                          │ │
│  │ **文件：**                                                    │ │
│  │ - Create: `exact/path/to/file.py`                             │ │
│  │ - Modify: `exact/path/to/existing.py:123-145`                 │ │
│  │ - Test: `tests/exact/path/to/test.py`                         │ │
│  │                                                               │ │
│  │ - [ ] Step 1: 写失败的测试                                    │ │
│  │ - [ ] Step 2: 运行测试确认失败                                │ │
│  │ - [ ] Step 3: 写最小实现                                      │ │
│  │ - [ ] Step 4: 运行测试确认通过                                │ │
│  │ - [ ] Step 5: 提交                                            │ │
│  └─────────────────────────────────────────────────────────────┘ │
│                                                                   │
│  9. 计划自查                                                      │
│     • Spec 覆盖：每个需求都有对应任务吗？                         │
│     • 占位符扫描：有 TBD/TODO 吗？                                │
│     • 类型一致性：函数名/签名在各任务间一致吗？                   │
└─────────────────────────────────────────────────────────────────┘
    │
    ▼
┌─────────────────────────────────────────────────────────────────┐
│  Phase 5: 实施 (Implement)                                       │
│                                                                   │
│  10. TDD 实施                                                     │
│      • 先写测试（RED）                                            │
│      • 写最小实现（GREEN）                                        │
│      • 重构（IMPROVE）                                            │
│      • 每步提交                                                    │
│                                                                   │
│  11. 文档同步                                                     │
│      • 写 docstring（跟代码一起）                                 │
│      • 更新 CLAUDE.md（如果文件结构变了）                         │
│      • 更新 CHANGELOG（如果有用户可见变更）                       │
└─────────────────────────────────────────────────────────────────┘
    │
    ▼
┌─────────────────────────────────────────────────────────────────┐
│  Phase 6: 验证 (Verify) — 铁律                                    │
│                                                                   │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │                    铁律：证据先于声明                         │ │
│  │                                                               │ │
│  │  没有运行验证命令，就不能说"完成了"                           │ │
│  │  没有看到输出，就不能说"通过了"                               │ │
│  │                                                               │ │
│  │  ❌ "应该没问题了"                                            │ │
│  │  ❌ "看起来正确"                                              │ │
│  │  ❌ "我很有信心"                                              │ │
│  │                                                               │ │
│  │  ✅ 运行命令 → 读输出 → 确认通过 → 带证据声明                 │ │
│  └─────────────────────────────────────────────────────────────┘ │
│                                                                   │
│  验证检查表：                                                     │
│  • [ ] 运行测试命令，看到 0 failures                              │
│  • [ ] 运行 linter，看到 0 errors                                │
│  • [ ] 运行构建，看到 exit 0                                     │
│  • [ ] 回归测试：先确认失败，再确认修复                          │
│  • [ ] 逐条核对需求：每个都实现了吗？                            │
└─────────────────────────────────────────────────────────────────┘
    │ 全部通过
    ▼
  ✅ 功能完成（带证据）
```

## Quick Reference

**When user asks to add a feature:**

1. **DON'T** jump into code
2. **DO** ask: "这个功能主要解决什么问题？"
3. **DO** ask: "你希望它怎么工作？"
4. **DO** propose 2-3 approaches
5. **DO** present design section by section
6. **DO** write design doc → user review
7. **DO** write implementation plan
8. **DO** implement with TDD
9. **DO** verify with evidence before claiming done

## Anti-Patterns to BLOCK

| Pattern | Example | Response |
|---------|---------|----------|
| 直接写代码 | "好的，我来实现..." | "先让我了解一下需求。这个功能主要解决什么问题？" |
| 跳过设计 | "这个很简单，直接写" | "简单也需要设计。让我先确认几个问题。" |
| 跳过计划 | "我知道怎么做了" | "让我先写个实施计划，确认一下细节。" |
| 声明完成无证据 | "完成了！" | "让我先运行测试确认。" |
| 应该没问题 | "应该可以了" | "让我实际运行一下确认。" |

## Integration with Other Rules

This rule is the **entry point** for all feature work:
- [feature-discipline.md](./feature-discipline.md) - Scope control
- [documentation.md](./documentation.md) - Doc as deliverable
- [architecture-guard.md](./architecture-guard.md) - Boundary enforcement
- [harness.md](./harness.md) - Overall harness flow
