# Claude Harness

全局开发规范体系，为 Claude Code 提供统一的开发防护、代码质量标准和工作流程。

## 什么是 Harness？

Harness 是一套**6 层防御系统**，自动强制执行代码质量、架构边界和功能纪律：

```
Layer 0: Feature Gate (功能门控)
Layer 1: Feature Discipline (功能纪律)
Layer 2: Code Generation (代码生成)
Layer 3: Quality Gate (质量门控)
Layer 4: Commit Gate (提交门控)
Layer 5: Documentation Sync (文档同步)
```

## 文件结构

```
claude-harness/
├── harness.md                 # 主文档：6 层防御体系总览
├── feature-gate.md            # Layer 0：功能门控（硬门控）
├── feature-discipline.md      # Layer 1：功能蔓延防护
├── coding-style.md            # Layer 2：编码风格（KISS/DRY/YAGNI）
├── architecture-guard.md      # Layer 3：架构边界强制
├── testing.md                 # Layer 3：测试要求（80% 覆盖率）
├── hooks.md                   # Layer 4：Hooks 系统
├── documentation.md           # Layer 5：文档维护
├── agents.md                  # Agent 编排指南
├── code-review.md             # 代码审查标准
├── development-workflow.md    # 开发工作流
├── feature-structure.md       # 功能目录结构
├── git-workflow.md            # Git 工作流
├── patterns.md                # 常用设计模式
├── performance.md             # 性能优化指南
├── security.md                # 安全规范
└── README.md                  # 本文件
```

## 快速开始

### 1. 克隆仓库

```bash
git clone https://github.com/a3145304873-art/claude-harness.git
```

### 2. 复制到全局目录

```bash
# macOS / Linux
cp claude-harness/* ~/.claude/rules/common/

# Windows (PowerShell)
Copy-Item claude-harness\* ~\.claude\rules\common\ -Recurse
```

### 3. 验证安装

```bash
ls ~/.claude/rules/common/
# 应该看到 17 个 .md 文件
```

## 核心规范

### Layer 0: Feature Gate（硬门控）

**在写任何代码之前，必须：**

1. 提出澄清问题
2. 展示设计方案（2-3 种方案）
3. 获得用户批准
4. 编写实施计划

```bash
# 示例对话
User: "帮我加个 XXX 功能"
Claude: "这个功能主要解决什么问题？"
Claude: "你希望用户怎么触发它？"
Claude: "这里有两个方案，你选哪个？"
```

### Layer 1: Feature Discipline（功能蔓延防护）

**实现任何新功能前，回答 4 个问题：**

| 问题 | 否 → 操作 |
|------|----------|
| 有 spec/issue 吗？ | 写一个先 |
| 服务于当前目标吗？ | 加入 backlog |
| 能在 300 行内完成吗？ | 分阶段 |
| 需要修改 > 3 个文件吗？ | 先获取审查 |

**禁止的反模式：**
- ❌ "顺便加个..."
- ❌ "以后可能用到"
- ❌ "也不错"

### Layer 2: Code Generation（代码生成）

**编码原则：**
- **不可变性**：创建新对象，不修改现有对象
- **KISS**：保持简单
- **DRY**：不重复
- **YAGNI**：不提前构建

**文件大小限制：**
- 单个文件 < 500 行（800 行上限）
- 单个函数 < 50 行
- 单个类 < 200 行

### Layer 3: Quality Gate（质量门控）

**提交前检查：**

```bash
# 1. Lint + 自动修复
ruff check --fix .

# 2. 格式化
ruff format .

# 3. 架构边界检查
import-linter check

# 4. 测试覆盖率 >= 80%
pytest --cov=. --cov-fail-under=80
```

### Layer 4: Commit Gate（提交门控）

**Pre-commit Hooks：**
- ruff-check: Python lint
- ruff-format: Python 格式
- check-added-large-files: 禁止 > 500KB
- no-commit-to-branch: 保护 main/master

**提交大小限制：**
| 类型 | 最大行数 | 最大文件数 |
|------|---------|-----------|
| feat | 500 | 10 |
| fix | 300 | 8 |
| refactor | 800 | 20 |

### Layer 5: Documentation Sync（文档同步）

**每次变更后自动更新：**
- 新模块 → 更新 CLAUDE.md
- 新函数 → 添加 docstring
- 重构 → 更新受影响的文档
- Bug fix → 添加 CHANGELOG 条目

## Agent 编排

### 可用 Agents

| Agent | 用途 | 使用场景 |
|-------|------|---------|
| planner | 实现规划 | 复杂功能、重构 |
| architect | 系统设计 | 架构决策 |
| tdd-guide | 测试驱动开发 | 新功能、bug 修复 |
| code-reviewer | 代码审查 | 写完代码后 |
| security-reviewer | 安全分析 | 提交前 |

### 立即使用（无需用户提示）

1. 复杂功能请求 → 使用 **planner** agent
2. 代码刚写完 → 使用 **code-reviewer** agent
3. Bug 修复或新功能 → 使用 **tdd-guide** agent
4. 架构决策 → 使用 **architect** agent

## 安全规范

**每次提交前检查：**
- [ ] 无硬编码密钥
- [ ] 所有用户输入已验证
- [ ] SQL 注入防护
- [ ] XSS 防护
- [ ] CSRF 保护
- [ ] 错误信息不泄露敏感数据

## 测试要求

**最低覆盖率：80%**

**测试类型（全部需要）：**
1. 单元测试 - 独立函数、工具、组件
2. 集成测试 - API 端点、数据库操作
3. E2E 测试 - 关键用户流程

**TDD 工作流（强制）：**
```bash
# 1. 先写测试（RED）
# 2. 运行测试 - 应该失败
# 3. 写最小实现（GREEN）
# 4. 运行测试 - 应该通过
# 5. 重构（IMPROVE）
# 6. 验证覆盖率 >= 80%
```

## 集成方式

### 项目级集成

在项目根目录创建 `.claude/rules/` 目录，复制需要的规范文件。

### 全局集成

将所有文件复制到 `~/.claude/rules/common/`。

### 自定义扩展

基于现有规范创建项目特定的规则：

```bash
# 示例：为 Python 项目添加额外规则
cp coding-style.md ~/.claude/rules/common/
# 然后编辑添加 Python 特定规范
```

## 常见问题

### Q: 这些规则会自动执行吗？

A: 是的，Claude Code 会在每次会话开始时加载这些规范，并自动遵守。

### Q: 可以禁用某些规则吗？

A: 可以删除或重命名对应的 .md 文件。

### Q: 如何更新规范？

A: 直接编辑 `.claude/rules/common/` 中的文件，或重新克隆本仓库。

### Q: 这些规则适用于哪些语言？

A: 主要针对 Python 项目，但核心原则（KISS/DRY/YAGNI）适用于所有语言。

## 相关资源

- [Claude Code 官方文档](https://docs.anthropic.com/claude-code)
- [Claude Code GitHub](https://github.com/anthropics/claude-code)

## 许可证

MIT License

## 贡献

欢迎提交 Issue 和 Pull Request 来改进这套规范体系。
