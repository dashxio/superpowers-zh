# Superpowers-zh 中文化实施计划

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 将 Superpowers 插件 (v6.1.1) 完整中文化，创建 superpowers-zh 独立插件

**Architecture:** 从原版 Superpowers 完整复制目录结构，按 P0→P1→P2→P3 优先级逐文件中文化。保留文件名、YAML `name` 字段、代码块不变，正文内容中文化。

**Tech Stack:** 纯 Markdown 文本翻译，无代码变更

## Global Constraints

- YAML `name:` 字段保留英文原文
- 技能目录名和文件名保留英文原文
- 代码块、shell 命令、文件路径保留原文
- dot 流程图节点保留英文原文
- 关键术语首次出现时保留英文 + 中文注释（如 `The Iron Law（铁律）`），后续直接用原文
- 语言风格：保持原版强硬直接的语气，中文表达自然通顺
- `plugin.json` 中 `name` 改为 `superpowers-zh`，`description` 中英双语

---

## Phase 1: 项目初始化

### Task 1: 复制原版 Superpowers 到项目目录

**Files:**
- 从 `C:\Users\26024\.claude\plugins\cache\claude-plugins-official\superpowers\6.1.1\` 复制全部内容到 `d:\doing\Project_superpowers-zh\`

**说明:** 完整复制原版插件目录结构，排除 `.git/`、`docs/plans/`、`docs/specs/`、`tests/`。

- [ ] **Step 1: 复制技能目录**

```powershell
Copy-Item -Recurse -Path "C:\Users\26024\.claude\plugins\cache\claude-plugins-official\superpowers\6.1.1\skills\" -Destination "d:\doing\Project_superpowers-zh\skills\"
```

- [ ] **Step 2: 复制插件配置和 hooks**

```powershell
Copy-Item -Recurse -Path "C:\Users\26024\.claude\plugins\cache\claude-plugins-official\superpowers\6.1.1\.claude-plugin\" -Destination "d:\doing\Project_superpowers-zh\.claude-plugin\"
Copy-Item -Recurse -Path "C:\Users\26024\.claude\plugins\cache\claude-plugins-official\superpowers\6.1.1\hooks\" -Destination "d:\doing\Project_superpowers-zh\hooks\"
```

- [ ] **Step 3: 复制顶层文档和配置文件**

```powershell
Copy-Item -Path "C:\Users\26024\.claude\plugins\cache\claude-plugins-official\superpowers\6.1.1\*.md" -Destination "d:\doing\Project_superpowers-zh\"
Copy-Item -Path "C:\Users\26024\.claude\plugins\cache\claude-plugins-official\superpowers\6.1.1\*.json" -Destination "d:\doing\Project_superpowers-zh\"
Copy-Item -Path "C:\Users\26024\.claude\plugins\cache\claude-plugins-official\superpowers\6.1.1\LICENSE" -Destination "d:\doing\Project_superpowers-zh\LICENSE"
Copy-Item -Path "C:\Users\26024\.claude\plugins\cache\claude-plugins-official\superpowers\6.1.1\.gitignore" -Destination "d:\doing\Project_superpowers-zh\.gitignore"
Copy-Item -Path "C:\Users\26024\.claude\plugins\cache\claude-plugins-official\superpowers\6.1.1\.gitattributes" -Destination "d:\doing\Project_superpowers-zh\.gitattributes"
```

- [ ] **Step 4: 复制平台适配文件**

```powershell
Copy-Item -Recurse -Path "C:\Users\26024\.claude\plugins\cache\claude-plugins-official\superpowers\6.1.1\.codex-plugin\" -Destination "d:\doing\Project_superpowers-zh\.codex-plugin\"
Copy-Item -Recurse -Path "C:\Users\26024\.claude\plugins\cache\claude-plugins-official\superpowers\6.1.1\.cursor-plugin\" -Destination "d:\doing\Project_superpowers-zh\.cursor-plugin\"
Copy-Item -Recurse -Path "C:\Users\26024\.claude\plugins\cache\claude-plugins-official\superpowers\6.1.1\.kimi-plugin\" -Destination "d:\doing\Project_superpowers-zh\.kimi-plugin\"
Copy-Item -Recurse -Path "C:\Users\26024\.claude\plugins\cache\claude-plugins-official\superpowers\6.1.1\.pi\" -Destination "d:\doing\Project_superpowers-zh\.pi\"
Copy-Item -Recurse -Path "C:\Users\26024\.claude\plugins\cache\claude-plugins-official\superpowers\6.1.1\.opencode\" -Destination "d:\doing\Project_superpowers-zh\.opencode\"
Copy-Item -Recurse -Path "C:\Users\26024\.claude\plugins\cache\claude-plugins-official\superpowers\6.1.1\assets\" -Destination "d:\doing\Project_superpowers-zh\assets\"
Copy-Item -Recurse -Path "C:\Users\26024\.claude\plugins\cache\claude-plugins-official\superpowers\6.1.1\scripts\" -Destination "d:\doing\Project_superpowers-zh\scripts\"
```

- [ ] **Step 5: 验证文件复制完整性**

```powershell
Get-ChildItem -Recurse "d:\doing\Project_superpowers-zh\skills\" | Measure-Object | ForEach-Object { Write-Host "Skills files: $($_.Count)" }
Write-Host "---"
Get-ChildItem "d:\doing\Project_superpowers-zh\*.md" | ForEach-Object { Write-Host $_.Name }
```

---

## Phase 2: P0 - 插件元数据（2 个文件）

### Task 2: 中文化 plugin.json

**Files:**
- Modify: `.claude-plugin/plugin.json`

**原则:** `name` → `superpowers-zh`，`description` → 中英双语，保留 `version`、`author`、`license` 等元数据。

- [ ] **Step 1: 修改 plugin.json**

将 `name` 改为 `superpowers-zh`，`description` 改为中文描述（附英文原文），`keywords` 添加中文关键词。其余字段保留。

- [ ] **Step 2: 验证 JSON 格式有效**

```powershell
Get-Content "d:\doing\Project_superpowers-zh\.claude-plugin\plugin.json" | ConvertFrom-Json | Out-Null; Write-Host "Valid JSON"
```

### Task 3: 中文化 marketplace.json

**Files:**
- Modify: `.claude-plugin/marketplace.json`

- [ ] **Step 1: 中文化 marketplace.json 中的描述字段**

阅读原文件，将描述内容中文化。

- [ ] **Step 2: 验证 JSON 格式有效**

```powershell
Get-Content "d:\doing\Project_superpowers-zh\.claude-plugin\marketplace.json" | ConvertFrom-Json | Out-Null; Write-Host "Valid JSON"
```

---

## Phase 3: P1 - 核心技能 SKILL.md（14 个文件）

> 每个技能独立为一个 Task。中文化原则：YAML `name` 保留英文，`description` 中文化；标题和正文中文化；代码块/命令/路径保留；关键术语首次出现加中文注释。

### Task 4: 中文化 using-superpowers/SKILL.md

**Files:**
- Modify: `skills/using-superpowers/SKILL.md`

**说明:** 这是插件的引导技能，所有会话启动时加载。翻译所有正文内容，保留 `<EXTREMELY-IMPORTANT>` 等 XML 标签，保留代码块和路径。

- [ ] **Step 1: 阅读原文件**

阅读 `skills/using-superpowers/SKILL.md` 完整内容。

- [ ] **Step 2: 中文化正文内容**

逐段翻译。特别关注 Red Flags 表格（保留表格结构，中文化表格内容）和平台适配部分。

- [ ] **Step 3: 验证**

确认 `<EXTREMELY-IMPORTANT>` 标签完整保留，YAML `name` 保持 `using-superpowers`，`description` 已中文化，代码块/路径未变。

### Task 5: 中文化 brainstorming/SKILL.md

**Files:**
- Modify: `skills/brainstorming/SKILL.md`

**说明:** 最大的技能文件之一。包含流程图（dot 格式，节点文本保留英文）、检查清单、反模式警告等。

- [ ] **Step 1: 阅读原文件**
- [ ] **Step 2: 中文化正文内容**
- [ ] **Step 3: 验证**

确认 YAML `name` 保持 `brainstorming`，dot 流程图节点文本保留英文，`HARD-GATE` 标签保留，`docs/superpowers/specs/` 路径引用保留。

### Task 6: 中文化 writing-plans/SKILL.md

**Files:**
- Modify: `skills/writing-plans/SKILL.md`

- [ ] **Step 1: 阅读原文件**
- [ ] **Step 2: 中文化正文内容**

注意保留任务结构模板中的代码块。保留 `## No Placeholders` 等特定术语首次出现时的英文+中文注释。

- [ ] **Step 3: 验证**

### Task 7: 中文化 subagent-driven-development/SKILL.md

**Files:**
- Modify: `skills/subagent-driven-development/SKILL.md`

- [ ] **Step 1: 阅读原文件**
- [ ] **Step 2: 中文化正文内容**
- [ ] **Step 3: 验证**

### Task 8: 中文化 executing-plans/SKILL.md

**Files:**
- Modify: `skills/executing-plans/SKILL.md`

- [ ] **Step 1: 阅读原文件**
- [ ] **Step 2: 中文化正文内容**
- [ ] **Step 3: 验证**

### Task 9: 中文化 test-driven-development/SKILL.md

**Files:**
- Modify: `skills/test-driven-development/SKILL.md`

**说明:** 包含 `The Iron Law`、`RED-GREEN-REFACTOR` 等标志性术语。dot 流程图节点文本保留英文。

- [ ] **Step 1: 阅读原文件**
- [ ] **Step 2: 中文化正文内容**

关键术语首次出现格式：`The Iron Law（铁律）`、`RED-GREEN-REFACTOR（红-绿-重构）`。

- [ ] **Step 3: 验证**

### Task 10: 中文化 systematic-debugging/SKILL.md

**Files:**
- Modify: `skills/systematic-debugging/SKILL.md`

**说明:** 包含四阶段调试流程，大量步骤说明。

- [ ] **Step 1: 阅读原文件**
- [ ] **Step 2: 中文化正文内容**
- [ ] **Step 3: 验证**

### Task 11: 中文化 requesting-code-review/SKILL.md

**Files:**
- Modify: `skills/requesting-code-review/SKILL.md`

- [ ] **Step 1: 阅读原文件**
- [ ] **Step 2: 中文化正文内容**
- [ ] **Step 3: 验证**

### Task 12: 中文化 receiving-code-review/SKILL.md

**Files:**
- Modify: `skills/receiving-code-review/SKILL.md`

- [ ] **Step 1: 阅读原文件**
- [ ] **Step 2: 中文化正文内容**
- [ ] **Step 3: 验证**

### Task 13: 中文化 verification-before-completion/SKILL.md

**Files:**
- Modify: `skills/verification-before-completion/SKILL.md`

- [ ] **Step 1: 阅读原文件**
- [ ] **Step 2: 中文化正文内容**
- [ ] **Step 3: 验证**

### Task 14: 中文化 finishing-a-development-branch/SKILL.md

**Files:**
- Modify: `skills/finishing-a-development-branch/SKILL.md`

- [ ] **Step 1: 阅读原文件**
- [ ] **Step 2: 中文化正文内容**
- [ ] **Step 3: 验证**

### Task 15: 中文化 using-git-worktrees/SKILL.md

**Files:**
- Modify: `skills/using-git-worktrees/SKILL.md`

- [ ] **Step 1: 阅读原文件**
- [ ] **Step 2: 中文化正文内容**
- [ ] **Step 3: 验证**

### Task 16: 中文化 dispatching-parallel-agents/SKILL.md

**Files:**
- Modify: `skills/dispatching-parallel-agents/SKILL.md`

- [ ] **Step 1: 阅读原文件**
- [ ] **Step 2: 中文化正文内容**
- [ ] **Step 3: 验证**

### Task 17: 中文化 writing-skills/SKILL.md

**Files:**
- Modify: `skills/writing-skills/SKILL.md`

**说明:** 关于如何编写技能的元技能，包含测试方法论。

- [ ] **Step 1: 阅读原文件**
- [ ] **Step 2: 中文化正文内容**
- [ ] **Step 3: 验证**

---

## Phase 4: P2 - 技能辅助文件（17 个文件）

> 按技能目录分组，每个目录一个 Task。

### Task 18: 中文化 brainstorming/ 辅助文件

**Files:**
- Modify: `skills/brainstorming/visual-companion.md`
- Modify: `skills/brainstorming/spec-document-reviewer-prompt.md`

- [ ] **Step 1: 中文化 visual-companion.md**
- [ ] **Step 2: 中文化 spec-document-reviewer-prompt.md**
- [ ] **Step 3: 验证两个文件代码块/路径完整性**

### Task 19: 中文化 requesting-code-review/ 辅助文件

**Files:**
- Modify: `skills/requesting-code-review/code-reviewer.md`

- [ ] **Step 1: 中文化 code-reviewer.md**
- [ ] **Step 2: 验证**

### Task 20: 中文化 subagent-driven-development/ 辅助文件

**Files:**
- Modify: `skills/subagent-driven-development/implementer-prompt.md`
- Modify: `skills/subagent-driven-development/task-reviewer-prompt.md`

- [ ] **Step 1: 中文化 implementer-prompt.md**
- [ ] **Step 2: 中文化 task-reviewer-prompt.md**
- [ ] **Step 3: 验证**

### Task 21: 中文化 systematic-debugging/ 辅助文件

**Files:**
- Modify: `skills/systematic-debugging/condition-based-waiting.md`
- Modify: `skills/systematic-debugging/defense-in-depth.md`
- Modify: `skills/systematic-debugging/root-cause-tracing.md`

- [ ] **Step 1: 中文化 condition-based-waiting.md**
- [ ] **Step 2: 中文化 defense-in-depth.md**
- [ ] **Step 3: 中文化 root-cause-tracing.md**
- [ ] **Step 4: 验证**

### Task 22: 中文化 test-driven-development/ 辅助文件

**Files:**
- Modify: `skills/test-driven-development/testing-anti-patterns.md`

- [ ] **Step 1: 中文化 testing-anti-patterns.md**
- [ ] **Step 2: 验证**

### Task 23: 中文化 using-superpowers/references/ 辅助文件

**Files:**
- Modify: `skills/using-superpowers/references/codex-tools.md`
- Modify: `skills/using-superpowers/references/pi-tools.md`
- Modify: `skills/using-superpowers/references/antigravity-tools.md`

- [ ] **Step 1: 中文化 codex-tools.md**
- [ ] **Step 2: 中文化 pi-tools.md**
- [ ] **Step 3: 中文化 antigravity-tools.md**
- [ ] **Step 4: 验证**

### Task 24: 中文化 writing-plans/ 辅助文件

**Files:**
- Modify: `skills/writing-plans/plan-document-reviewer-prompt.md`

- [ ] **Step 1: 中文化 plan-document-reviewer-prompt.md**
- [ ] **Step 2: 验证**

### Task 25: 中文化 writing-skills/ 辅助文件

**Files:**
- Modify: `skills/writing-skills/anthropic-best-practices.md`
- Modify: `skills/writing-skills/persuasion-principles.md`
- Modify: `skills/writing-skills/testing-skills-with-subagents.md`
- Modify: `skills/writing-skills/examples/CLAUDE_MD_TESTING.md`

- [ ] **Step 1: 中文化 anthropic-best-practices.md**
- [ ] **Step 2: 中文化 persuasion-principles.md**
- [ ] **Step 3: 中文化 testing-skills-with-subagents.md**
- [ ] **Step 4: 中文化 CLAUDE_MD_TESTING.md**
- [ ] **Step 5: 验证**

---

## Phase 5: P3 - 顶层文档（5 个文件）

### Task 26: 中文化 README.md

**Files:**
- Modify: `README.md`

**说明:** 最大的用户文档，包含安装说明、快速入门、技能介绍、哲学理念等。

- [ ] **Step 1: 阅读原文件**
- [ ] **Step 2: 中文化正文内容**

保留代码块中的命令、bash 代码块、URL 链接。各 harness 安装说明的标题保留平台名称。

- [ ] **Step 3: 验证**

确认所有链接完整、代码块未被修改。

### Task 27: 中文化 CLAUDE.md

**Files:**
- Modify: `CLAUDE.md`

**说明:** 贡献者指南，包含 PR 要求和反模式。语气强硬，需要保留。

- [ ] **Step 1: 阅读原文件**
- [ ] **Step 2: 中文化正文内容**
- [ ] **Step 3: 验证**

### Task 28: 中文化 AGENTS.md

**Files:**
- Modify: `AGENTS.md`

- [ ] **Step 1: 阅读原文件**
- [ ] **Step 2: 中文化正文内容**
- [ ] **Step 3: 验证**

### Task 29: 中文化 GEMINI.md

**Files:**
- Modify: `GEMINI.md`

- [ ] **Step 1: 阅读原文件**
- [ ] **Step 2: 中文化正文内容**
- [ ] **Step 3: 验证**

### Task 30: 中文化 CODE_OF_CONDUCT.md

**Files:**
- Modify: `CODE_OF_CONDUCT.md`

- [ ] **Step 1: 阅读原文件**
- [ ] **Step 2: 中文化正文内容**
- [ ] **Step 3: 验证**

---

## Phase 6: 最终验证

### Task 31: 完整性核查

- [ ] **Step 1: 确认所有 36 个目标文件均已中文化**

```powershell
# 检查所有 SKILL.md 是否存在且已修改
Get-ChildItem -Recurse "d:\doing\Project_superpowers-zh\skills\" -Filter "SKILL.md" | ForEach-Object { Write-Host "$($_.FullName) - $($_.LastWriteTime)" }
```

- [ ] **Step 2: 抽查 3 个技能文件，对照原文检查**

随机选取 3 个已翻译的 SKILL.md，逐段对照原版确认：无遗漏段落、代码块完整、YAML `name` 保留英文、关键术语翻译一致。

- [ ] **Step 3: 确认排除项未被改动**

```powershell
# 验证 LICENSE 未被修改（应为纯英文）
Get-Content "d:\doing\Project_superpowers-zh\LICENSE" -Head 3
# 验证 hooks/session-start 脚本未被修改
Get-Content "d:\doing\Project_superpowers-zh\hooks\session-start"
```

- [ ] **Step 4: 确认没有遗漏的英文待翻译内容（仅限于目标文件）**

对已翻译的文件做快速扫描，确认没有整段英文遗留。
