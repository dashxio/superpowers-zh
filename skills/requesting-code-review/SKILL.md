---
name: requesting-code-review
description: 在完成任务、实施主要功能或合并前使用，以验证工作是否满足需求
---

# 请求代码审查

派发一个代码审查 subagent 在问题蔓延之前将其捕获。审查者获得精确构建的评估上下文 — 永远不是你会话的历史。这使审查者专注于工作成果而非你的思考过程，同时也为你保留了继续工作的上下文。

**核心原则：** 尽早审查，经常审查。

## 何时请求审查

**强制性：**
- subagent-driven development 中每个任务之后
- 完成主要功能后
- 合并到 main 之前

**可选但有价值：**
- 卡住时（新的视角）
- 重构前（基线检查）
- 修复复杂 bug 后

## 如何请求

**1. 获取 git SHA：**
```bash
BASE_SHA=$(git rev-parse HEAD~1)  # or origin/main
HEAD_SHA=$(git rev-parse HEAD)
```

**2. 派发代码审查 subagent：**

派发一个 `general-purpose` subagent，填写 [code-reviewer.md](code-reviewer.md) 中的模板

**占位符：**
- `{DESCRIPTION}` — 你构建的内容的简要总结
- `{PLAN_OR_REQUIREMENTS}` — 它应该做什么
- `{BASE_SHA}` — 起始提交
- `{HEAD_SHA}` — 结束提交

**3. 根据反馈采取行动：**
- 立即修复 Critical 问题
- 在继续之前修复 Important 问题
- 记录 Minor 问题以供后续处理
- 如果审查者有误，提出反驳（附带理由）

## 示例

```
[Just completed Task 2: Add verification function]

You: Let me request code review before proceeding.

BASE_SHA=$(git log --oneline | grep "Task 1" | head -1 | awk '{print $1}')
HEAD_SHA=$(git rev-parse HEAD)

[Dispatch code reviewer subagent]
  DESCRIPTION: Added verifyIndex() and repairIndex() with 4 issue types
  PLAN_OR_REQUIREMENTS: Task 2 from docs/superpowers/plans/deployment-plan.md
  BASE_SHA: a7981ec
  HEAD_SHA: 3df7661

[Subagent returns]:
  Strengths: Clean architecture, real tests
  Issues:
    Important: Missing progress indicators
    Minor: Magic number (100) for reporting interval
  Assessment: Ready to proceed

You: [Fix progress indicators]
[Continue to Task 3]
```

## 与工作流的集成

**Subagent-Driven Development（Subagent 驱动开发）：**
- 每个任务之后审查
- 在问题累积之前捕获
- 在进入下一个任务之前修复

**Executing Plans（执行计划）：**
- 每个任务后或在自然检查点审查
- 获取反馈，应用，继续

**Ad-Hoc Development（临时开发）：**
- 合并前审查
- 卡住时审查

## Red Flags（危险信号）

**绝不：**
- 因为"太简单"而跳过审查
- 忽略 Critical 问题
- 在未修复 Important 问题的情况下继续
- 与有效的技术反馈争论

**如果审查者有误：**
- 用技术理由反驳
- 展示证明其可行的代码/测试
- 请求澄清

模板参见：[code-reviewer.md](code-reviewer.md)
