# Superpowers-zh 中文化设计文档

## 概述

将 Superpowers 插件（v6.1.1）完整中文化，创建 `superpowers-zh` 独立插件，供中文用户使用。保持原插件的功能、架构和行为约束力不变，仅做语言层面的本地化。

## 范围

**包含：**
- 14 个核心技能的 SKILL.md 及其辅助文件（references、prompts 等）
- 顶层用户文档（README.md、CLAUDE.md、AGENTS.md、GEMINI.md、CODE_OF_CONDUCT.md）
- 插件元数据（plugin.json、marketplace.json）

**不包含：**
- `docs/plans/` 和 `docs/specs/` 下的历史设计文档
- `tests/` 测试文件
- `hooks/` 脚本文件
- `LICENSE` 法律文件
- `.github/` 模板文件

## 项目结构

完整镜像原版目录结构（`skills/` 目录名、文件名、YAML `name` 字段均不变），仅替换文件内的正文内容为中文化版本。

## 中文化原则

### 1. 保留不译（技术兼容）

| 项目 | 原因 |
|------|------|
| YAML `name:` 字段 | agent 调度依赖此字段 |
| 技能目录名和文件名 | 路径兼容 |
| 代码块、shell 命令、文件路径 | 技术内容 |
| dot 流程图节点 | 流程图可读性 |
| 文档路径引用 | 实际路径指向 |

### 2. 保留原文 + 首次中文注释（关键术语）

关键术语保留英文，首次出现时附带中文注释。后续出现直接用原文。例如：

- `The Iron Law（铁律）`
- `HARD-GATE（硬性门槛）`
- `RED-GREEN-REFACTOR（红-绿-重构）`
- `human partner（人类搭档）`

### 3. 完全中文化（正文内容）

- 所有说明文字、步骤描述、段落正文
- 标题（如 `# Brainstorming Ideas Into Designs` → `# 通过头脑风暴将想法转化为设计`）
- 反模式警告、检查清单、提示
- 列表项说明文字
- YAML `description:` 字段

### 4. 语言风格

保持原版强硬、直接、有"人格"的语气，同时确保中文表达自然通顺。例如：

- `Delete means delete.` → `删掉就是删掉。`
- `NO PRODUCTION CODE WITHOUT A FAILING TEST FIRST` → `没有失败的测试，不准写生产代码`

### 5. 插件元数据

- `plugin.json`: `name` → `superpowers-zh`，`description` → 中英双语
- `marketplace.json`: 描述字段中文化

## 实施文件清单

### P0 - 插件元数据（2 个文件）
- `.claude-plugin/plugin.json`
- `.claude-plugin/marketplace.json`

### P1 - 核心技能 SKILL.md（14 个文件）
- `skills/brainstorming/SKILL.md`
- `skills/dispatching-parallel-agents/SKILL.md`
- `skills/executing-plans/SKILL.md`
- `skills/finishing-a-development-branch/SKILL.md`
- `skills/receiving-code-review/SKILL.md`
- `skills/requesting-code-review/SKILL.md`
- `skills/subagent-driven-development/SKILL.md`
- `skills/systematic-debugging/SKILL.md`
- `skills/test-driven-development/SKILL.md`
- `skills/using-git-worktrees/SKILL.md`
- `skills/using-superpowers/SKILL.md`
- `skills/verification-before-completion/SKILL.md`
- `skills/writing-plans/SKILL.md`
- `skills/writing-skills/SKILL.md`

### P2 - 技能辅助文件（约 15 个文件）
- `skills/brainstorming/visual-companion.md`
- `skills/brainstorming/spec-document-reviewer-prompt.md`
- `skills/requesting-code-review/code-reviewer.md`
- `skills/subagent-driven-development/implementer-prompt.md`
- `skills/subagent-driven-development/task-reviewer-prompt.md`
- `skills/systematic-debugging/condition-based-waiting.md`
- `skills/systematic-debugging/defense-in-depth.md`
- `skills/systematic-debugging/root-cause-tracing.md`
- `skills/test-driven-development/testing-anti-patterns.md`
- `skills/using-superpowers/references/codex-tools.md`
- `skills/using-superpowers/references/pi-tools.md`
- `skills/using-superpowers/references/antigravity-tools.md`
- `skills/writing-plans/plan-document-reviewer-prompt.md`
- `skills/writing-skills/anthropic-best-practices.md`
- `skills/writing-skills/persuasion-principles.md`
- `skills/writing-skills/testing-skills-with-subagents.md`
- `skills/writing-skills/examples/CLAUDE_MD_TESTING.md`

### P3 - 顶层文档（5 个文件）
- `README.md`
- `CLAUDE.md`
- `AGENTS.md`
- `GEMINI.md`
- `CODE_OF_CONDUCT.md`

## 质量检查

- 每个文件中文化后，对照原文检查是否有遗漏段落
- 确保代码块、命令、路径引用未被意外修改
- 确保 YAML 前言中的 `name` 字段保持原文
- 确保关键术语翻译在全文一致
