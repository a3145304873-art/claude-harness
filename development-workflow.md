# Development Workflow

> This file extends [common/git-workflow.md](./git-workflow.md) with the full feature development process that happens before git operations.

The Feature Implementation Workflow describes the development pipeline: research, planning, TDD, code review, and then committing to git.

## Harness Setup (One-time per project)

Before starting development, ensure the project has proper harness configuration:

1. **Check for pre-commit hooks**: If `.pre-commit-config.yaml` doesn't exist, create it from `~/.claude/templates/`
2. **Check for ruff config**: If `.ruff.toml` doesn't exist, create it from `~/.claude/templates/`
3. **Check for import-linter**: If not configured in `pyproject.toml`, add it from `~/.claude/templates/`
4. **Install hooks**: Run `pre-commit install` if not already done

See [hooks.md](./hooks.md) for pre-commit integration details.

## Feature Implementation Workflow

0. **Research & Reuse** _(mandatory before any new implementation)_
   - **GitHub code search first:** Run `gh search repos` and `gh search code` to find existing implementations, templates, and patterns before writing anything new.
   - **Library docs second:** Use Context7 or primary vendor docs to confirm API behavior, package usage, and version-specific details before implementing.
   - **Exa only when the first two are insufficient:** Use Exa for broader web research or discovery after GitHub search and primary docs.
   - **Check package registries:** Search npm, PyPI, crates.io, and other registries before writing utility code. Prefer battle-tested libraries over hand-rolled solutions.
   - **Search for adaptable implementations:** Look for open-source projects that solve 80%+ of the problem and can be forked, ported, or wrapped.
   - Prefer adopting or porting a proven approach over writing net-new code when it meets the requirement.

1. **Plan First**
   - Use **planner** agent to create implementation plan
   - Generate planning docs before coding: PRD, architecture, system_design, tech_doc, task_list
   - Identify dependencies and risks
   - Break down into phases

2. **TDD Approach**
   - Use **tdd-guide** agent
   - Write tests first (RED)
   - Implement to pass tests (GREEN)
   - Refactor (IMPROVE)
   - Verify 80%+ coverage

3. **Code Review**
   - Use **code-reviewer** agent immediately after writing code
   - Address CRITICAL and HIGH issues
   - Fix MEDIUM issues when possible

4. **Documentation** (MANDATORY - part of feature delivery)
   - Write docstrings for every new function/class/module
   - Update CLAUDE.md if file structure changed
   - Add CHANGELOG entry for user-facing changes
   - Update README if setup/usage changed
   - See [documentation.md](./documentation.md) for full requirements

5. **Commit & Push**
   - Run `ruff check --fix .` and `ruff format .` before committing
   - Pre-commit hooks run automatically (lint, format, size checks)
   - Detailed commit messages
   - Follow conventional commits format
   - See [git-workflow.md](./git-workflow.md) for commit message format and PR process
   - See [feature-discipline.md](./feature-discipline.md) for commit size limits

6. **Pre-Review Checks**
   - Verify all automated checks (CI/CD) are passing
   - Resolve any merge conflicts
   - Ensure branch is up to date with target branch
   - Only request review after these checks pass
