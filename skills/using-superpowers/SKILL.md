---
name: using-superpowers
description: 在任何对话开始时使用 — 建立如何查找和使用技能的规则，要求在任何回复（包括澄清性问题）之前必须先调用技能
---

<SUBAGENT-STOP>
如果你被派发为 subagent 来执行特定任务，请忽略此技能。
</SUBAGENT-STOP>

<EXTREMELY-IMPORTANT>
如果你认为某个技能有哪怕 1% 的可能性适用于你正在做的事情，你绝对必须调用该技能。

如果某个技能适用于你的任务，你没有选择。你必须使用它。

这不是可以讨价还价的。你不能找理由绕过去。
</EXTREMELY-IMPORTANT>

## The Rule（核心规则）

**在任何回复或行动之前调用相关或被请求的技能** — 包括澄清性问题、探索代码库或检查文件。如果结果证明它不适用于当前情况，你可以不用它。

**进入计划模式之前：** 如果你还没有进行头脑风暴，先调用 brainstorming 技能。

然后宣布「正在使用 [技能名称] 来 [目的]」并严格遵循该技能。如果技能有检查清单，为每一项创建一个 todo。

## Skill Priority（技能优先级）

当多个技能同时适用时，流程类技能优先 — 它们确定方法，然后实施类技能（如 frontend-design 等）来执行。Brainstorming 和 systematic-debugging 是 Superpowers 最常用的流程技能，但这一规则适用于所有技能。

- 「我们来构建 X」→ 先用 superpowers:brainstorming，然后用实施类技能。
- 「修复这个 bug」→ 先用 superpowers:systematic-debugging，然后用领域技能。

## Red Flags（危险信号）

以下想法意味着 STOP — 你在找借口：

| 想法 | 现实 |
|---------|---------|
| 「这只是一个简单的问题」 | 问题是任务。检查技能。 |
| 「我需要先了解更多上下文」 | 技能检查在澄清性问题之前。 |
| 「让我先探索一下代码库」 | 技能告诉你如何探索。先检查。 |
| 「我可以快速检查一下 git/文件」 | 文件缺乏对话上下文。检查技能。 |
| 「让我先收集信息」 | 技能告诉你如何收集信息。 |
| 「这不需要正式的技能」 | 如果有技能存在，就使用它。 |
| 「我记得这个技能」 | 技能会演进。阅读当前版本。 |
| 「这不算一个任务」 | 行动 = 任务。检查技能。 |
| 「这个技能太小题大做了」 | 简单的事情会变复杂。使用它。 |
| 「我先做这一件事」 | 在做任何事之前先检查。 |
| 「这感觉很有成效」 | 无纪律的行动浪费时间。技能可以防止这种情况。 |
| 「我知道那是什么意思」 | 知道概念 ≠ 使用技能。调用它。 |

## Platform Adaptation（平台适配）

如果你的 harness 出现在这里，请阅读其参考文件以获取特殊说明：

- Codex: `references/codex-tools.md`
- Pi: `references/pi-tools.md`
- Antigravity: `references/antigravity-tools.md`

## User Instructions（用户指令）

用户指令（CLAUDE.md、AGENTS.md、GEMINI.md 等，以及直接请求）优先于技能，技能优先于默认行为。只有在你的 human partner（人类搭档）明确告知的情况下，才可以跳过技能的工作流程或指令。
