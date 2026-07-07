---
name: executing-plans
description: 当你有一份书面的实施计划要在独立会话中执行并带有审核检查点时使用
---

# 执行计划

## 概述

加载计划，批判性审核，执行所有任务，完成后报告。

**开始时宣布：**「正在使用 executing-plans 技能来实施此计划。」

**注意：** 告诉你的 human partner（人类搭档），Superpowers 在使用 subagent 时效果更好。如果在支持 subagent 的平台上运行（Claude Code、Codex CLI、Codex App 和 Copilot CLI 均符合条件；参见 `../using-superpowers/references/` 中的各平台工具参考），工作质量会显著提高。如果 subagent 可用，请使用 superpowers:subagent-driven-development 而非此技能。

## 流程

### 第1步：加载并审核计划
1. 阅读计划文件
2. 批判性审核——识别关于计划的任何问题或疑虑
3. 如果有疑虑：在开始前向你的 human partner 提出
4. 如果没有疑虑：为计划项创建 todos 并继续

### 第2步：执行任务

对于每个任务：
1. 标记为 in_progress
2. 严格按照每个步骤执行（计划已将步骤细化为小块）
3. 按指定运行验证
4. 标记为 completed

### 第3步：完成开发

所有任务完成并验证后：
- 宣布：「正在使用 finishing-a-development-branch 技能完成此工作。」
- **必需的子技能：** 使用 superpowers:finishing-a-development-branch
- 遵循该技能来验证测试、呈现选项、执行选择

## 何时停止并寻求帮助

**在以下情况立即停止执行：**
- 遇到阻塞（缺少依赖、测试失败、指令不清）
- 计划有严重缺陷导致无法开始
- 你不理解某个指令
- 验证反复失败

**寻求澄清，而非猜测。**

## 何时重新审视之前的步骤

**在以下情况返回到审核（第1步）：**
- 搭档根据你的反馈更新了计划
- 基本方法需要重新思考

**不要强行通过阻塞** — 停下来询问。

## 记住
- 首先批判性审核计划
- 严格按照计划步骤执行
- 不要跳过验证
- 当计划指示时引用相应技能
- 遇到阻塞时停止，不要猜测
- 未经用户明确同意，绝不在 main/master 分支上开始实施

## 集成

**必需的工作流技能：**
- **superpowers:using-git-worktrees** — 确保隔离的工作空间（创建或验证已有的）
- **superpowers:writing-plans** — 创建此技能执行的计划
- **superpowers:finishing-a-development-branch** — 所有任务完成后完成开发
