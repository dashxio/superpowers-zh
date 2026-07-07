---
name: finishing-a-development-branch
description: 当实现完成、所有测试通过，且需要决定如何整合工作时使用 - 通过呈现针对合并、PR 或清理的结构化选项来引导开发工作的完成
---

# 完成开发分支

## 概述

通过呈现清晰选项并处理所选工作流，来引导开发工作的完成。

**核心原则：** 验证测试 → 检测环境 → 呈现选项 → 执行选择 → 清理

**开场声明：** "我正在使用 finishing-a-development-branch skill 来完成这项工作。"

## 流程

### 第一步：验证测试

**在呈现选项之前，先验证测试是否通过：**

```bash
# Run project's test suite
npm test / cargo test / pytest / go test ./...
```

**如果测试失败：**
```
Tests failing (<N> failures). Must fix before completing:

[Show failures]

Cannot proceed with merge/PR until tests pass.
```

停止。不要进入第二步。

**如果测试通过：** 继续进入第二步。

### 第二步：检测环境

**在呈现选项之前，确定工作空间状态：**

```bash
GIT_DIR=$(cd "$(git rev-parse --git-dir)" 2>/dev/null && pwd -P)
GIT_COMMON=$(cd "$(git rev-parse --git-common-dir)" 2>/dev/null && pwd -P)
```

这决定了显示哪个菜单以及如何进行清理：

| 状态 | 菜单 | 清理 |
|-------|------|---------|
| `GIT_DIR == GIT_COMMON`（普通仓库） | 标准4选项 | 无需清理 worktree |
| `GIT_DIR != GIT_COMMON`，命名分支 | 标准4选项 | 基于来源清理（见第六步） |
| `GIT_DIR != GIT_COMMON`，detached HEAD | 简化3选项（无合并） | 无需清理（外部管理） |

### 第三步：确定基础分支

```bash
# Try common base branches
git merge-base HEAD main 2>/dev/null || git merge-base HEAD master 2>/dev/null
```

或询问："此分支从 main 分出，是否确认？"

### 第四步：呈现选项

**普通仓库和命名分支 worktree — 准确呈现以下4个选项：**

```
Implementation complete. What would you like to do?

1. Merge back to <base-branch> locally
2. Push and create a Pull Request
3. Keep the branch as-is (I'll handle it later)
4. Discard this work

Which option?
```

**Detached HEAD — 准确呈现以下3个选项：**

```
Implementation complete. You're on a detached HEAD (externally managed workspace).

1. Push as new branch and create a Pull Request
2. Keep as-is (I'll handle it later)
3. Discard this work

Which option?
```

**不要添加解释** — 保持选项简洁。

### 第五步：执行选择

#### 选项1：本地合并

```bash
# Get main repo root for CWD safety
MAIN_ROOT=$(git -C "$(git rev-parse --git-common-dir)/.." rev-parse --show-toplevel)
cd "$MAIN_ROOT"

# Merge first — verify success before removing anything
git checkout <base-branch>
git pull
git merge <feature-branch>

# Verify tests on merged result
<test command>

# Only after merge succeeds: cleanup worktree (Step 6), then delete branch
```

然后：清理 worktree（第六步），再删除分支：

```bash
git branch -d <feature-branch>
```

#### 选项2：推送并创建PR

```bash
# Push branch
git push -u origin <feature-branch>
```

**不要清理 worktree** — 用户需要它来迭代处理 PR 反馈。

#### 选项3：保持现状

报告："正在保留分支 <name>。Worktree 保留在 <path>。"

**不要清理 worktree。**

#### 选项4：丢弃

**先确认：**
```
This will permanently delete:
- Branch <name>
- All commits: <commit-list>
- Worktree at <path>

Type 'discard' to confirm.
```

等待准确的确认输入。

如果已确认：
```bash
MAIN_ROOT=$(git -C "$(git rev-parse --git-common-dir)/.." rev-parse --show-toplevel)
cd "$MAIN_ROOT"
```

然后：清理 worktree（第六步），再强制删除分支：
```bash
git branch -D <feature-branch>
```

### 第六步：清理工作空间

**仅对选项1和4执行。** 选项2和3始终保留 worktree。

```bash
GIT_DIR=$(cd "$(git rev-parse --git-dir)" 2>/dev/null && pwd -P)
GIT_COMMON=$(cd "$(git rev-parse --git-common-dir)" 2>/dev/null && pwd -P)
WORKTREE_PATH=$(git rev-parse --show-toplevel)
```

**如果 `GIT_DIR == GIT_COMMON`：** 普通仓库，无需清理 worktree。完成。

**如果 worktree 路径位于 `.worktrees/` 或 `worktrees/` 下：** Superpowers 创建了此 worktree — 由我们负责清理。

```bash
MAIN_ROOT=$(git -C "$(git rev-parse --git-common-dir)/.." rev-parse --show-toplevel)
cd "$MAIN_ROOT"
git worktree remove "$WORKTREE_PATH"
git worktree prune  # Self-healing: clean up any stale registrations
```

**否则：** 宿主环境（harness）拥有此工作空间。不要删除它。如果你的平台提供了退出工作空间的工具，请使用它。否则，保持工作空间不变。

## 快速参考

| 选项 | 合并 | 推送 | 保留 Worktree | 清理分支 |
|--------|-------|------|---------------|----------------|
| 1. 本地合并 | 是 | - | - | 是 |
| 2. 创建 PR | - | 是 | 是 | - |
| 3. 保持现状 | - | - | 是 | - |
| 4. 丢弃 | - | - | - | 是（强制） |

## 常见错误

**跳过测试验证**
- **问题：** 合入有问题的代码，创建失败的 PR
- **解决：** 始终在提供选项前验证测试

**开放式问题**
- **问题：** "接下来我该做什么？" 过于模糊
- **解决：** 准确呈现4个结构化选项（detached HEAD 为3个）

**为选项2清理 worktree**
- **问题：** 移除了用户进行 PR 迭代所需的工作空间
- **解决：** 仅对选项1和4进行清理

**在移除 worktree 之前删除分支**
- **问题：** `git branch -d` 失败，因为 worktree 仍引用该分支
- **解决：** 先合并，移除 worktree，再删除分支

**从 worktree 内部运行 git worktree remove**
- **问题：** 当当前工作目录位于要移除的 worktree 内部时，命令静默失败
- **解决：** 在 `git worktree remove` 之前始终先 `cd` 到主仓库根目录

**清理 harness 拥有的 worktree**
- **问题：** 移除 harness 创建的 worktree 会导致幻影状态
- **解决：** 仅清理 `.worktrees/` 或 `worktrees/` 下的 worktree

**丢弃操作未确认**
- **问题：** 意外删除工作成果
- **解决：** 要求输入 "discard" 进行确认

## 红线警示

**绝不：**
- 在测试失败时继续推进
- 不验证结果测试就进行合并
- 未确认就删除工作
- 未经明确请求就强制推送
- 在确认合并成功前移除 worktree
- 清理不是你创建的 worktree（来源检查）
- 从 worktree 内部运行 `git worktree remove`

**始终：**
- 在提供选项前验证测试
- 在呈现菜单前检测环境
- 准确呈现4个选项（detached HEAD 为3个）
- 对选项4获取键入确认
- 仅对选项1和4清理 worktree
- 在移除 worktree 前 `cd` 到主仓库根目录
- 移除后运行 `git worktree prune`
