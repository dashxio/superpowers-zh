## subagent（子代理）分发需要 multi-agent（多代理）支持

在你的 Codex 配置文件（`~/.codex/config.toml`）中添加：

```toml
[features]
multi_agent = true
```

这将为 `dispatching-parallel-agents` 和 `subagent-driven-development` 等 skills（技能）启用 `spawn_agent`、`wait_agent` 和 `close_agent`。使用 subagent-driven-development 时，你应在实现者（implementer）和审查者（reviewer）subagent 完成所有工作后关闭它们。

## 环境检测

创建 worktree 或完成 branch（分支）的 skill 应在执行前通过只读 git 命令检测其环境：

```bash
GIT_DIR=$(cd "$(git rev-parse --git-dir)" 2>/dev/null && pwd -P)
GIT_COMMON=$(cd "$(git rev-parse --git-common-dir)" 2>/dev/null && pwd -P)
BRANCH=$(git branch --show-current)
```

- `GIT_DIR != GIT_COMMON` → 已处于链接 worktree 中（跳过创建）
- `BRANCH` 为空 → 处于分离 HEAD 状态（无法从 sandbox（沙箱）创建 branch/推送/发起 PR）

请参阅 `using-git-worktrees` 第 0 步和 `finishing-a-development-branch` 第 1 步，了解每个 skill 如何使用这些信号。

## Codex App 收尾

当 sandbox 阻止 branch/推送操作（在外部管理的 worktree 中处于分离 HEAD 状态）时，agent（代理）应提交所有工作并告知用户使用应用的本地控件：

- **"Create branch"** — 命名 branch，然后通过应用界面提交/推送/发起 PR
- **"Hand off to local"** — 将工作移交到用户的本地仓库

agent 仍可运行测试、暂存文件，并输出建议的 branch 名称、提交信息和 PR 描述供用户复制。
