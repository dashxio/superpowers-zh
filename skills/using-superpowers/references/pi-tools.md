# Pi 工具映射

Skills（技能）用动作来表述（"派遣 subagent（子代理）"、"创建 todo（待办事项）"、"读取文件"）。在 Pi 上，这些动作会解析为以下工具。

| 技能请求的动作 | Pi 对应工具 |
| --- | --- |
| 派遣 subagent（`Subagent (general-purpose):` 模板） | 如果可用，使用已安装的 subagent 工具，例如来自 `pi-subagents` 的 `subagent` 工具 |
| 任务跟踪（"创建 todo"、"标记完成"） | 如果可用，使用已安装的 todo/任务工具；否则在 plan 或 `TODO.md` 中跟踪任务 |

## Subagents

Pi 核心未附带标准的 subagent 工具。`pi-subagents` 包是一个强大的可选伴侣，提供了 `subagent` 工具，支持 single-agent（单代理）、chain、parallel、async、forked-context 以及 resume/status 工作流。如果没有可用的 subagent 工具，不要虚构 `Task` 调用；在当前会话中顺序执行，或说明可选 subagent 功能未安装。

## 任务列表

Pi 核心未附带标准的任务列表工具。如果已安装 todo/任务扩展，请使用其文档化的工具。否则，使用 Superpowers 计划文件、Markdown 格式的检查表或仓库本地的 `TODO.md` 进行任务跟踪。较旧的 Superpowers 文档可能引用 `TodoWrite`；请将其视为上述的任务跟踪动作。
