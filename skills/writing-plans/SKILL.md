---
name: writing-plans
description: 当你有一个多步骤任务的规格说明或需求时，在接触代码之前使用此技能
---

# 编写计划（Writing Plans）

## 概述

编写全面的实施计划，假设工程师对我们的代码库零背景了解且品味存疑。记录他们需要知道的一切：每个任务要触及哪些文件、代码、测试、可能需要查看的文档，以及如何测试。以一口大小的任务形式提供整个计划。DRY。YAGNI。TDD。频繁提交。

假设他们是熟练的开发者，但对我们的工具集或问题领域几乎一无所知。假设他们不太擅长良好的测试设计。

**开始时声明：** "我正在使用 writing-plans 技能来创建实施计划。"

**上下文：** 如果在隔离的工作树中工作，应该在执行时通过 `superpowers:using-git-worktrees` 技能创建该工作树。

**保存计划到：** `docs/superpowers/plans/YYYY-MM-DD-<feature-name>.md`
- （用户对计划位置的偏好会覆盖此默认值）

## 范围检查

如果规格说明涵盖多个独立子系统，应该在头脑风暴期间已将其分解为子项目规格。如果没有，建议将此分解为单独的计划——每个子系统一个。每个计划应能独立产生可工作、可测试的软件。

## 文件结构

在定义任务之前，规划哪些文件将被创建或修改，以及每个文件的职责。这是分解决策被确定下来的地方。

- 设计具有清晰边界和定义良好的接口的单元。每个文件应有单一明确职责。
- 你在推理能够同时放入上下文的代码时表现最佳，当文件聚焦时你的编辑也更可靠。优先选择更小、更聚焦的文件，而不是做太多事的大型文件。
- 一起变更的文件应放在一起。按职责拆分，而不是按技术层拆分。
- 在现有代码库中，遵循已建立的模式。如果代码库使用大型文件，不要单方面进行重组——但如果你正在修改的文件变得庞大臃肿，在计划中包含拆分是合理的。

这个结构指导任务的分解。每个任务应产生独立有意义的自包含变更。

## 任务合理规模

任务是最小单元，携带自己的测试周期并值得一次新的审查关口。在划定任务边界时：将设置、配置、脚手架和文档步骤折叠到需要它们的交付物所属的任务中；只在审查者可能有意义地拒绝一个任务而批准其相邻任务的地方进行拆分。每个任务以一个可独立测试的交付物结束。

## 一口大小的任务粒度

**每一步是一个操作（2-5分钟）：**
- "编写失败测试" - 步骤
- "运行测试确认它失败" - 步骤
- "实现使测试通过的最小代码" - 步骤
- "运行测试并确认它们通过" - 步骤
- "提交" - 步骤

## 计划文档头部

**每个计划必须以这个头部开头：**

```markdown
# [Feature Name] Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** [One sentence describing what this builds]

**Architecture:** [2-3 sentences about approach]

**Tech Stack:** [Key technologies/libraries]

## Global Constraints

[The spec's project-wide requirements — version floors, dependency limits,
naming and copy rules, platform requirements — one line each, with exact
values copied verbatim from the spec. Every task's requirements implicitly
include this section.]

---
```

## 任务结构

````markdown
### Task N: [Component Name]

**Files:**
- Create: `exact/path/to/file.py`
- Modify: `exact/path/to/existing.py:123-145`
- Test: `tests/exact/path/to/test.py`

**Interfaces:**
- Consumes: [what this task uses from earlier tasks — exact signatures]
- Produces: [what later tasks rely on — exact function names, parameter
  and return types. A task's implementer sees only their own task; this
  block is how they learn the names and types neighboring tasks use.]

- [ ] **Step 1: Write the failing test**

```python
def test_specific_behavior():
    result = function(input)
    assert result == expected
```

- [ ] **Step 2: Run test to verify it fails**

Run: `pytest tests/path/test.py::test_name -v`
Expected: FAIL with "function not defined"

- [ ] **Step 3: Write minimal implementation**

```python
def function(input):
    return expected
```

- [ ] **Step 4: Run test to verify it passes**

Run: `pytest tests/path/test.py::test_name -v`
Expected: PASS

- [ ] **Step 5: Commit**

```bash
git add tests/path/test.py src/path/file.py
git commit -m "feat: add specific feature"
```
````

## 禁止占位符

每个步骤必须包含工程师所需的实际内容。以下是**计划失败**——永远不要编写它们：
- "待定"、"待办"、"稍后实现"、"补充细节"
- "添加适当的错误处理"/"添加验证"/"处理边界情况"
- "为上述内容编写测试"（没有实际的测试代码）
- "类似于任务N"（重复代码——工程师可能按乱序阅读任务）
- 描述做什么但不展示如何做的步骤（代码步骤需要代码块）
- 引用任何任务中未定义的类型、函数或方法

## 记住

- 始终使用精确文件路径
- 每个步骤中的完整代码——如果步骤更改了代码，展示代码
- 精确命令及预期输出
- DRY、YAGNI、TDD、频繁提交

## 自审

编写完整计划后，以全新视角审视规格说明，对照它检查计划。这是你自己运行的检查清单——不是子代理调度。

**1. 规格覆盖率：** 浏览规格说明中的每个章节/需求。你能指出一个实现它的任务吗？列出任何差距。

**2. 占位符扫描：** 搜索计划中的红旗——上文"禁止占位符"部分中的任何模式。修复它们。

**3. 类型一致性：** 你在后面任务中使用的类型、方法签名和属性名称是否与你在前面任务中定义的匹配？在任务3中叫 `clearLayers()` 但在任务7中叫 `clearFullLayers()` 的函数是一个错误。

如果发现问题，内联修复它们。无需重新审查——只需修复并继续。如果发现规格需求没有对应任务，添加该任务。

## 执行交接

保存计划后，提供执行选择：

**"计划已完成并保存到 `docs/superpowers/plans/<filename>.md`。两种执行选项：**

**1. 子代理驱动（推荐）** - 每个任务分派一个新的子代理，任务间进行审查，快速迭代

**2. 内联执行** - 在当前会话中使用 executing-plans 执行任务，带检查点的批量执行

**选择哪种方式？"**

**如果选择子代理驱动：**
- **必需子技能：** 使用 superpowers:subagent-driven-development
- 每个任务一个新鲜子代理 + 两阶段审查

**如果选择内联执行：**
- **必需子技能：** 使用 superpowers:executing-plans
- 带审查检查点的批量执行
