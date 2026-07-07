# Antigravity CLI（`agy`）工具映射

Skills（技能）用行动说话（"dispatch a subagent（子代理）"、"create a todo（待办事项）"、"read a file（读取文件）"）。在 Antigravity CLI（`agy`）上，这些操作对应以下工具。

| Skills 请求的操作 | Antigravity CLI 对应工具 |
|----------------------|----------------------|
| 派遣 subagent（`Subagent (general-purpose):` 模板） | 调用 `invoke_subagent`，使用内置 `TypeName`——`self` 用于全功能工作，`research` 用于只读（参见 [Subagent support](#subagent-support)） |
| 任务追踪（"create a todo"、"mark complete"） | 一个 **task artifact（任务工件）**——使用 `write_to_file`，带有 `IsArtifact: true` 和 `ArtifactType: "task"`（参见 [Task tracking](#task-tracking)）。**不是** `manage_task`，后者管理后台进程。 |

## 任务追踪

Antigravity **没有 todo 工具**（`manage_task` 管理后台进程——`list`/`kill`/`status`/`send_input`——它*不是*清单）。当 skill 要求创建 todo 列表或追踪任务时，维护一个 **task artifact**：一个 markdown 清单，使用 `write_to_file`（`IsArtifact: true`、`ArtifactMetadata.ArtifactType: "task"`）保存，并在过程中使用 `replace_file_content` / `multi_replace_file_content` 进行编辑。

在开始任何多步骤任务时，创建 task artifact，列出计划的每一步。每完成一步，编辑 artifact 将其标记为已完成（`- [x]`）。如果计划有变，更新清单。保持清单最新——它是剩余任务的权威依据；当对话变长时，在开始每一步之前重新阅读它。
