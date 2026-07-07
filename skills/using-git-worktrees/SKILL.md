---
name: using-git-worktrees
description: 当开始需要与当前工作空间隔离的功能开发时，或在执行实现计划之前使用 - 通过原生工具或 git worktree 回退确保存在隔离的工作空间
---

# 使用 Git Worktrees

## 概述

确保工作在隔离的工作空间中进行。优先使用平台的原生 worktree 工具。仅在无原生工具可用时回退到手动 git worktree。

**核心原则：** 先检测现有隔离。然后使用原生工具。再回退到 git。绝不与 harness 对抗。

**开场声明：** "我正在使用 using-git-worktrees skill 来设置隔离的工作空间。"

## 第零步：检测现有隔离

**在创建任何内容之前，检查你是否已经处于隔离的工作空间中。**

```bash
GIT_DIR=$(cd "$(git rev-parse --git-dir)" 2>/dev/null && pwd -P)
GIT_COMMON=$(cd "$(git rev-parse --git-common-dir)" 2>/dev/null && pwd -P)
BRANCH=$(git branch --show-current)
```

**子模块防护：** `GIT_DIR != GIT_COMMON` 在 git 子模块内部也为 true。在得出"已在 worktree 中"的结论之前，先验证你不处于子模块中：

```bash
# If this returns a path, you're in a submodule, not a worktree — treat as normal repo
git rev-parse --show-superproject-working-tree 2>/dev/null
```

**如果 `GIT_DIR != GIT_COMMON`（且不是子模块）：** 你已经处于链接的 worktree 中。跳转到第二步（项目设置）。不要创建另一个 worktree。

报告分支状态：
- 在分支上："已处于隔离的工作空间 <path>，位于分支 <name>。"
- Detached HEAD："已处于隔离的工作空间 <path>（detached HEAD，外部管理）。完成时需创建分支。"

**如果 `GIT_DIR == GIT_COMMON`（或处于子模块中）：** 你处于普通仓库检出状态。

用户是否已在你的指令中表达了他们的 worktree 偏好？如果没有，在创建 worktree 之前请求同意：

> "你是否希望我设置一个隔离的 worktree？它可以保护你当前的分支不受更改影响。"

尊重任何已声明的现有偏好，无需询问。如果用户拒绝同意，则在原地工作并跳转到第二步。

## 第一步：创建隔离的工作空间

**你有两种机制。按此顺序尝试。**

### 1a. 原生 Worktree 工具（首选）

用户已请求隔离的工作空间（第零步的同意）。你是否已经有创建 worktree 的方法？它可能是一个名为 `EnterWorktree`、`WorktreeCreate` 的工具，或 `/worktree` 命令，或 `--worktree` 标志。如果有，请使用它并跳转到第二步。

原生工具会自动处理目录位置、分支创建和清理。当你拥有原生工具时使用 `git worktree add` 会产生你的 harness 无法看到或管理的幻影状态。

仅当你没有可用的原生 worktree 工具时，才继续进入第一步b。

### 1b. Git Worktree 回退

**仅当第一步a不适用时使用此方法** — 你没有可用的原生 worktree 工具。使用 git 手动创建 worktree。

#### 目录选择

遵循以下优先级顺序。用户明确表达的偏好始终优先于观察到的文件系统状态。

1. **检查你的指令中是否有已声明的 worktree 目录偏好。** 如果用户已指定，则直接使用，无需询问。

2. **检查是否已存在项目本地的 worktree 目录：**
   ```bash
   ls -d .worktrees 2>/dev/null     # Preferred (hidden)
   ls -d worktrees 2>/dev/null      # Alternative
   ```
   如果找到，则使用。如果两者都存在，`.worktrees` 优先。

3. **如果没有其他可用指引**，默认为项目根目录下的 `.worktrees/`。

#### 安全检查（仅限项目本地目录）

**在创建 worktree 之前必须验证目录已被忽略：**

```bash
git check-ignore -q .worktrees 2>/dev/null || git check-ignore -q worktrees 2>/dev/null
```

**如果未被忽略：** 添加到 .gitignore，提交更改，然后继续。

**为何关键：** 防止意外将 worktree 内容提交到仓库。

#### 创建 Worktree

```bash
# Determine path based on chosen location
path="$LOCATION/$BRANCH_NAME"

git worktree add "$path" -b "$BRANCH_NAME"
cd "$path"
```

**沙箱回退：** 如果 `git worktree add` 因权限错误（沙箱拒绝）而失败，告知用户沙箱阻止了 worktree 创建，你将在当前目录中工作。然后在原地运行设置和基线测试。

## 第二步：项目设置

自动检测并运行相应的设置：

```bash
# Node.js
if [ -f package.json ]; then npm install; fi

# Rust
if [ -f Cargo.toml ]; then cargo build; fi

# Python
if [ -f requirements.txt ]; then pip install -r requirements.txt; fi
if [ -f pyproject.toml ]; then poetry install; fi

# Go
if [ -f go.mod ]; then go mod download; fi
```

## 第三步：验证干净基线

运行测试以确保工作空间从干净状态开始：

```bash
# Use project-appropriate command
npm test / cargo test / pytest / go test ./...
```

**如果测试失败：** 报告失败情况，询问是否继续或进行调查。

**如果测试通过：** 报告就绪。

### 报告

```
Worktree ready at <full-path>
Tests passing (<N> tests, 0 failures)
Ready to implement <feature-name>
```

## 快速参考

| 情况 | 操作 |
|-----------|--------|
| 已处于链接的 worktree 中 | 跳过创建（第零步） |
| 处于子模块中 | 视为普通仓库（第零步防护） |
| 有原生 worktree 工具可用 | 使用它（第一步a） |
| 无原生工具 | Git worktree 回退（第一步b） |
| 存在 `.worktrees/` | 使用它（验证已忽略） |
| 存在 `worktrees/` | 使用它（验证已忽略） |
| 两者都存在 | 使用 `.worktrees/` |
| 两者都不存在 | 检查指令文件，然后默认 `.worktrees/` |
| 目录未被忽略 | 添加到 .gitignore + 提交 |
| 创建时权限错误 | 沙箱回退，原地工作 |
| 基线测试失败 | 报告失败 + 询问 |
| 没有 package.json/Cargo.toml | 跳过依赖安装 |

## 常见错误

### 与 harness 对抗

- **问题：** 当平台已提供隔离时使用 `git worktree add`
- **解决：** 第零步检测现有隔离。第一步a交由原生工具处理。

### 跳过检测

- **问题：** 在现有 worktree 内部创建嵌套的 worktree
- **解决：** 在创建任何内容之前始终运行第零步

### 跳过忽略验证

- **问题：** Worktree 内容被追踪，污染 git status
- **解决：** 在创建项目本地 worktree 之前始终使用 `git check-ignore`

### 假设目录位置

- **问题：** 造成不一致，违反项目约定
- **解决：** 遵循优先级：明确指令 > 现有项目本地目录 > 默认

### 在测试失败时继续

- **问题：** 无法区分新 bug 与已有问题
- **解决：** 报告失败，获得明确许可后再继续

## 红线警示

**绝不：**
- 在第零步检测到现有隔离时创建 worktree
- 当你拥有原生 worktree 工具（例如 `EnterWorktree`）时使用 `git worktree add`。这是第一大错误 — 如果你有此工具，请使用它。
- 跳过第一步a直接跳到第一步b的 git 命令
- 未验证目录被忽略就创建 worktree（项目本地）
- 跳过基线测试验证
- 在测试失败时未经询问就继续

**始终：**
- 首先运行第零步检测
- 优先使用原生工具而非 git 回退
- 遵循目录优先级：明确指令 > 现有项目本地目录 > 默认
- 验证项目本地目录已被忽略
- 自动检测并运行项目设置
- 验证干净的测试基线
