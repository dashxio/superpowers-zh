# 技能编写最佳实践

> 学习如何编写代理（agent）能够发现并成功使用的有效技能（skill）。

优秀的技能（skill）应当简洁、结构清晰，并经过真实使用场景的测试。本指南提供实用的编写决策，帮助你编写代理（agent）能够发现并有效使用的技能（skill）。

关于技能（skill）如何工作的概念背景，请参阅 [技能概述](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview)。

## 核心原则

### 简洁是关键

[上下文窗口（context window）](https://platform.claude.com/docs/en/build-with-claude/context-windows) 是公共资源。你的技能（skill）与代理（agent）需要知道的所有其他内容共享上下文窗口（context window），包括：

* 系统提示词（system prompt）
* 对话历史
* 其他技能（skill）的元数据
* 你的实际请求

并非技能（skill）中的每个 token 都有即时成本。启动时，只有所有技能（skill）的元数据（名称和描述）会预先加载。代理（agent）只会在技能（skill）变得相关时读取 SKILL.md，并且只在需要时读取其他文件。然而，在 SKILL.md 中保持简洁仍然很重要：一旦代理（agent）加载了它，每个 token 都会与对话历史和其他上下文（context）竞争。

**默认假设**：代理（agent）已经非常智能

只添加代理（agent）尚不具备的上下文（context）。质疑每一条信息：

* "代理真的需要这个解释吗？"
* "我能假设代理已经知道这个吗？"
* "这段文字值得它的 token 成本吗？"

**好例子：简洁**（约 50 tokens）：

````markdown  theme={null}
## Extract PDF text

Use pdfplumber for text extraction:

```python
import pdfplumber

with pdfplumber.open("file.pdf") as pdf:
    text = pdf.pages[0].extract_text()
```
````

**坏例子：过于冗长**（约 150 tokens）：

```markdown  theme={null}
## Extract PDF text

PDF (Portable Document Format) files are a common file format that contains
text, images, and other content. To extract text from a PDF, you'll need to
use a library. There are many libraries available for PDF processing, but we
recommend pdfplumber because it's easy to use and handles most cases well.
First, you'll need to install it using pip. Then you can use the code below...
```

简洁的版本假设代理（agent）知道什么是 PDF 以及库（library）如何工作。

### 设置适当的自由度

将具体程度与任务的脆弱性和可变性相匹配。

**高自由度**（基于文本的指令）：

在以下情况下使用：

* 多种方法都是有效的
* 决策取决于上下文（context）
* 启发式方法指导方向

示例：

```markdown  theme={null}
## Code review process

1. Analyze the code structure and organization
2. Check for potential bugs or edge cases
3. Suggest improvements for readability and maintainability
4. Verify adherence to project conventions
```

**中自由度**（伪代码或带参数的脚本）：

在以下情况下使用：

* 存在偏好的模式
* 允许一定的变化
* 配置影响行为

示例：

````markdown  theme={null}
## Generate report

Use this template and customize as needed:

```python
def generate_report(data, format="markdown", include_charts=True):
    # Process data
    # Generate output in specified format
    # Optionally include visualizations
```
````

**低自由度**（特定脚本，很少或没有参数）：

在以下情况下使用：

* 操作脆弱且容易出错
* 一致性至关重要
* 必须遵循特定顺序

示例：

````markdown  theme={null}
## Database migration

Run exactly this script:

```bash
python scripts/migrate.py --verify --backup
```

Do not modify the command or add additional flags.
````

**类比**：把代理（agent）想象成一个探索路径的机器人：

* **两侧悬崖间的窄桥**：只有一条安全的前进道路。提供具体的护栏和精确的指令（低自由度）。示例：必须按精确顺序运行的数据库迁移。
* **没有障碍的开阔地**：多条路径都能通向成功。给出大致方向，信任代理（agent）找到最佳路线（高自由度）。示例：代码审查，由上下文（context）决定最佳方法。

### 在所有计划使用的模型上进行测试

技能（skill）作为模型的附加内容，其有效性取决于底层模型。在所有计划使用的模型上测试你的技能（skill）。

**各模型的测试考虑**：

* **Claude Haiku**（快速、经济）：技能（skill）是否提供了足够的指导？
* **Claude Sonnet**（均衡）：技能（skill）是否清晰高效？
* **Claude Opus**（强大推理能力）：技能（skill）是否避免了过度解释？

在 Opus 上完美运行的内容可能需要在 Haiku 上添加更多细节。如果你计划在多个模型上使用技能（skill），请确保指令在所有模型上都能良好工作。

## 技能结构

<Note>
  **YAML 前置元数据（YAML Frontmatter）**：SKILL.md 的前置元数据需要两个字段：

  * `name` - 技能（skill）的人类可读名称（最多 64 个字符）
  * `description` - 一行描述，说明技能（skill）的作用及何时使用（最多 1024 个字符）

  关于完整的技能（skill）结构详情，请参阅 [技能概述](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview#skill-structure)。
</Note>

### 命名约定

使用一致的命名模式，使技能（skill）更容易被引用和讨论。我们建议使用 **动名词形式（动词 + -ing）** 作为技能（skill）名称，这能清晰地描述技能（skill）提供的活动或能力。

**好的命名示例（动名词形式）**：

* "Processing PDFs"
* "Analyzing spreadsheets"
* "Managing databases"
* "Testing code"
* "Writing documentation"

**可接受的替代方案**：

* 名词短语："PDF Processing", "Spreadsheet Analysis"
* 面向行动："Process PDFs", "Analyze Spreadsheets"

**避免**：

* 模糊的名称："Helper", "Utils", "Tools"
* 过于泛化："Documents", "Data", "Files"
* 技能（skill）集合中不一致的模式

一致的命名使得以下操作更容易：

* 在文档和对话中引用技能（skill）
* 一眼了解技能（skill）的功能
* 组织和搜索多个技能（skill）
* 维护专业、一致的技能（skill）库

### 编写有效的描述

`description` 字段用于技能（skill）的发现，应同时包含技能（skill）的功能和何时使用。

<Warning>
  **始终使用第三人称编写**。描述会被注入到系统提示词（system prompt）中，不一致的人称视角可能导致发现（discovery）问题。

  * **好：** "Processes Excel files and generates reports"
  * **避免：** "I can help you process Excel files"
  * **避免：** "You can use this to process Excel files"
</Warning>

**要具体并包含关键术语**。既要包含技能（skill）的功能，也要包含何时使用的具体触发条件/上下文（context）。

每个技能（skill）只有一个描述字段。描述对于技能（skill）选择至关重要：代理（agent）使用它从潜在 100+ 个可用技能（skill）中选择正确的技能（skill）。你的描述必须提供足够的细节，让代理（agent）知道何时选择这个技能（skill），而 SKILL.md 的其余部分则提供实现细节。

有效的示例：

**PDF 处理技能（PDF Processing skill）：**

```yaml  theme={null}
description: Extract text and tables from PDF files, fill forms, merge documents. Use when working with PDF files or when the user mentions PDFs, forms, or document extraction.
```

**Excel 分析技能（Excel Analysis skill）：**

```yaml  theme={null}
description: Analyze Excel spreadsheets, create pivot tables, generate charts. Use when analyzing Excel files, spreadsheets, tabular data, or .xlsx files.
```

**Git 提交助手技能（Git Commit Helper skill）：**

```yaml  theme={null}
description: Generate descriptive commit messages by analyzing git diffs. Use when the user asks for help writing commit messages or reviewing staged changes.
```

避免像这样的模糊描述：

```yaml  theme={null}
description: Helps with documents
```

```yaml  theme={null}
description: Processes data
```

```yaml  theme={null}
description: Does stuff with files
```

### 渐进式披露模式

SKILL.md 作为概述，根据需要为代理（agent）指向详细材料，就像入职指南中的目录一样。关于渐进式披露（progressive disclosure）如何工作的解释，请参阅概述中的 [技能如何工作](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview#how-skills-work)。

**实践指导：**

* 保持 SKILL.md 正文在 500 行以内以获得最佳性能
* 接近此限制时，将内容拆分到单独的文件中
* 使用以下模式有效组织指令、代码和资源

#### 视觉概览：从简单到复杂

一个基本的技能（skill）从仅包含元数据和指令的 SKILL.md 文件开始：

<img src="https://mintcdn.com/anthropic-claude-docs/4Bny2bjzuGBK7o00/images/agent-skills-simple-file.png?fit=max&auto=format&n=4Bny2bjzuGBK7o00&q=85&s=87782ff239b297d9a9e8e1b72ed72db9" alt="Simple SKILL.md file showing YAML frontmatter and markdown body" data-og-width="2048" width="2048" data-og-height="1153" height="1153" data-path="images/agent-skills-simple-file.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/anthropic-claude-docs/4Bny2bjzuGBK7o00/images/agent-skills-simple-file.png?w=280&fit=max&auto=format&n=4Bny2bjzuGBK7o00&q=85&s=c61cc33b6f5855809907f7fda94cd80e 280w, https://mintcdn.com/anthropic-claude-docs/4Bny2bjzuGBK7o00/images/agent-skills-simple-file.png?w=560&fit=max&auto=format&n=4Bny2bjzuGBK7o00&q=85&s=90d2c0c1c76b36e8d485f49e0810dbfd 560w, https://mintcdn.com/anthropic-claude-docs/4Bny2bjzuGBK7o00/images/agent-skills-simple-file.png?w=840&fit=max&auto=format&n=4Bny2bjzuGBK7o00&q=85&s=ad17d231ac7b0bea7e5b4d58fb4aeabb 840w, https://mintcdn.com/anthropic-claude-docs/4Bny2bjzuGBK7o00/images/agent-skills-simple-file.png?w=1100&fit=max&auto=format&n=4Bny2bjzuGBK7o00&q=85&s=f5d0a7a3c668435bb0aee9a3a8f8c329 1100w, https://mintcdn.com/anthropic-claude-docs/4Bny2bjzuGBK7o00/images/agent-skills-simple-file.png?w=1650&fit=max&auto=format&n=4Bny2bjzuGBK7o00&q=85&s=0e927c1af9de5799cfe557d12249f6e6 1650w, https://mintcdn.com/anthropic-claude-docs/4Bny2bjzuGBK7o00/images/agent-skills-simple-file.png?w=2500&fit=max&auto=format&n=4Bny2bjzuGBK7o00&q=85&s=46bbb1a51dd4c8202a470ac8c80a893d 2500w" />

随着技能（skill）的增长，你可以捆绑代理（agent）仅在需要时才加载的附加内容：

<img src="https://mintcdn.com/anthropic-claude-docs/4Bny2bjzuGBK7o00/images/agent-skills-bundling-content.png?fit=max&auto=format&n=4Bny2bjzuGBK7o00&q=85&s=a5e0aa41e3d53985a7e3e43668a33ea3" alt="Bundling additional reference files like reference.md and forms.md." data-og-width="2048" width="2048" data-og-height="1327" height="1327" data-path="images/agent-skills-bundling-content.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/anthropic-claude-docs/4Bny2bjzuGBK7o00/images/agent-skills-bundling-content.png?w=280&fit=max&auto=format&n=4Bny2bjzuGBK7o00&q=85&s=f8a0e73783e99b4a643d79eac86b70a2 280w, https://mintcdn.com/anthropic-claude-docs/4Bny2bjzuGBK7o00/images/agent-skills-bundling-content.png?w=560&fit=max&auto=format&n=4Bny2bjzuGBK7o00&q=85&s=dc510a2a9d3f14359416b706f067904a 560w, https://mintcdn.com/anthropic-claude-docs/4Bny2bjzuGBK7o00/images/agent-skills-bundling-content.png?w=840&fit=max&auto=format&n=4Bny2bjzuGBK7o00&q=85&s=82cd6286c966303f7dd914c28170e385 840w, https://mintcdn.com/anthropic-claude-docs/4Bny2bjzuGBK7o00/images/agent-skills-bundling-content.png?w=1100&fit=max&auto=format&n=4Bny2bjzuGBK7o00&q=85&s=56f3be36c77e4fe4b523df209a6824c6 1100w, https://mintcdn.com/anthropic-claude-docs/4Bny2bjzuGBK7o00/images/agent-skills-bundling-content.png?w=1650&fit=max&auto=format&n=4Bny2bjzuGBK7o00&q=85&s=d22b5161b2075656417d56f41a74f3dd 1650w, https://mintcdn.com/anthropic-claude-docs/4Bny2bjzuGBK7o00/images/agent-skills-bundling-content.png?w=2500&fit=max&auto=format&n=4Bny2bjzuGBK7o00&q=85&s=3dd4bdd6850ffcc96c6c45fcb0acd6eb 2500w" />

完整的技能（skill）目录结构可能如下所示：

```
pdf/
├── SKILL.md              # Main instructions (loaded when triggered)
├── FORMS.md              # Form-filling guide (loaded as needed)
├── reference.md          # API reference (loaded as needed)
├── examples.md           # Usage examples (loaded as needed)
└── scripts/
    ├── analyze_form.py   # Utility script (executed, not loaded)
    ├── fill_form.py      # Form filling script
    └── validate.py       # Validation script
```

#### 模式 1：带参考的高级指南

````markdown  theme={null}
---
name: PDF Processing
description: Extracts text and tables from PDF files, fills forms, and merges documents. Use when working with PDF files or when the user mentions PDFs, forms, or document extraction.
---

# PDF Processing

## Quick start

Extract text with pdfplumber:
```python
import pdfplumber
with pdfplumber.open("file.pdf") as pdf:
    text = pdf.pages[0].extract_text()
```

## Advanced features

**Form filling**: See [FORMS.md](FORMS.md) for complete guide
**API reference**: See [REFERENCE.md](REFERENCE.md) for all methods
**Examples**: See [EXAMPLES.md](EXAMPLES.md) for common patterns
````

代理（agent）仅在需要时加载 FORMS.md、REFERENCE.md 或 EXAMPLES.md。

#### 模式 2：领域特定组织

对于涉及多个领域的技能（skill），按领域组织内容以避免加载无关的上下文（context）。当用户询问销售指标时，代理（agent）只需要读取与销售相关的模式（schema），而不是财务或营销数据。这样可以保持 token 使用量低，上下文（context）集中。

```
bigquery-skill/
├── SKILL.md (overview and navigation)
└── reference/
    ├── finance.md (revenue, billing metrics)
    ├── sales.md (opportunities, pipeline)
    ├── product.md (API usage, features)
    └── marketing.md (campaigns, attribution)
```

````markdown SKILL.md theme={null}
# BigQuery Data Analysis

## Available datasets

**Finance**: Revenue, ARR, billing → See [reference/finance.md](reference/finance.md)
**Sales**: Opportunities, pipeline, accounts → See [reference/sales.md](reference/sales.md)
**Product**: API usage, features, adoption → See [reference/product.md](reference/product.md)
**Marketing**: Campaigns, attribution, email → See [reference/marketing.md](reference/marketing.md)

## Quick search

Find specific metrics using grep:

```bash
grep -i "revenue" reference/finance.md
grep -i "pipeline" reference/sales.md
grep -i "api usage" reference/product.md
```
````

#### 模式 3：条件性详情

展示基本内容，链接到高级内容：

```markdown  theme={null}
# DOCX Processing

## Creating documents

Use docx-js for new documents. See [DOCX-JS.md](DOCX-JS.md).

## Editing documents

For simple edits, modify the XML directly.

**For tracked changes**: See [REDLINING.md](REDLINING.md)
**For OOXML details**: See [OOXML.md](OOXML.md)
```

代理（agent）仅在用户需要这些功能时读取 REDLINING.md 或 OOXML.md。

### 避免深度嵌套的引用

当文件被其他引用文件引用时，代理（agent）可能会部分读取文件。遇到嵌套引用时，代理（agent）可能会使用 `head -100` 等命令预览内容而不是读取整个文件，导致信息不完整。

**保持引用距离 SKILL.md 一层深度**。所有引用文件都应从 SKILL.md 直接链接，以确保代理（agent）在需要时能读取完整文件。

**坏例子：太深**：

```markdown  theme={null}
# SKILL.md
See [advanced.md](advanced.md)...

# advanced.md
See [details.md](details.md)...

# details.md
Here's the actual information...
```

**好例子：一层深度**：

```markdown  theme={null}
# SKILL.md

**Basic usage**: [instructions in SKILL.md]
**Advanced features**: See [advanced.md](advanced.md)
**API reference**: See [reference.md](reference.md)
**Examples**: See [examples.md](examples.md)
```

### 为较长的引用文件添加目录结构

对于超过 100 行的引用文件，在顶部包含一个目录。这确保了即使代理（agent）通过部分读取预览内容时，也能看到可用信息的完整范围。

**示例**：

```markdown  theme={null}
# API Reference

## Contents
- Authentication and setup
- Core methods (create, read, update, delete)
- Advanced features (batch operations, webhooks)
- Error handling patterns
- Code examples

## Authentication and setup
...

## Core methods
...
```

代理（agent）可以随后读取完整文件或根据需要跳转到特定章节。

关于这种基于文件系统的架构如何实现渐进式披露（progressive disclosure）的详细信息，请参阅下方高级部分中的[运行时环境](#runtime-environment)章节。

## 工作流与反馈循环

### 为复杂任务使用工作流

将复杂的操作分解为清晰、顺序的步骤。对于特别复杂的工作流（workflow），提供一个检查清单（checklist），代理（agent）可以将其复制到响应中并随着进展勾选。

**示例 1：研究综合工作流**（适用于无代码的技能）：

````markdown  theme={null}
## Research synthesis workflow

Copy this checklist and track your progress:

```
Research Progress:
- [ ] Step 1: Read all source documents
- [ ] Step 2: Identify key themes
- [ ] Step 3: Cross-reference claims
- [ ] Step 4: Create structured summary
- [ ] Step 5: Verify citations
```

**Step 1: Read all source documents**

Review each document in the `sources/` directory. Note the main arguments and supporting evidence.

**Step 2: Identify key themes**

Look for patterns across sources. What themes appear repeatedly? Where do sources agree or disagree?

**Step 3: Cross-reference claims**

For each major claim, verify it appears in the source material. Note which source supports each point.

**Step 4: Create structured summary**

Organize findings by theme. Include:
- Main claim
- Supporting evidence from sources
- Conflicting viewpoints (if any)

**Step 5: Verify citations**

Check that every claim references the correct source document. If citations are incomplete, return to Step 3.
````

这个例子展示了工作流（workflow）如何应用于不需要代码的分析任务。检查清单（checklist）模式适用于任何复杂的多步骤过程。

**示例 2：PDF 表单填写工作流**（适用于有代码的技能）：

````markdown  theme={null}
## PDF form filling workflow

Copy this checklist and check off items as you complete them:

```
Task Progress:
- [ ] Step 1: Analyze the form (run analyze_form.py)
- [ ] Step 2: Create field mapping (edit fields.json)
- [ ] Step 3: Validate mapping (run validate_fields.py)
- [ ] Step 4: Fill the form (run fill_form.py)
- [ ] Step 5: Verify output (run verify_output.py)
```

**Step 1: Analyze the form**

Run: `python scripts/analyze_form.py input.pdf`

This extracts form fields and their locations, saving to `fields.json`.

**Step 2: Create field mapping**

Edit `fields.json` to add values for each field.

**Step 3: Validate mapping**

Run: `python scripts/validate_fields.py fields.json`

Fix any validation errors before continuing.

**Step 4: Fill the form**

Run: `python scripts/fill_form.py input.pdf fields.json output.pdf`

**Step 5: Verify output**

Run: `python scripts/verify_output.py output.pdf`

If verification fails, return to Step 2.
````

清晰的步骤可以防止代理（agent）跳过关键的验证。检查清单（checklist）帮助你与代理（agent）跟踪多步骤工作流（workflow）的进度。

### 实现反馈循环

**常见模式**：运行验证器 → 修复错误 → 重复

这种模式能大幅提高输出质量。

**示例 1：样式指南合规**（适用于无代码的技能）：

```markdown  theme={null}
## Content review process

1. Draft your content following the guidelines in STYLE_GUIDE.md
2. Review against the checklist:
   - Check terminology consistency
   - Verify examples follow the standard format
   - Confirm all required sections are present
3. If issues found:
   - Note each issue with specific section reference
   - Revise the content
   - Review the checklist again
4. Only proceed when all requirements are met
5. Finalize and save the document
```

这展示了使用参考文档代替脚本的验证循环模式。这里的"验证器"是 STYLE\_GUIDE.md，代理（agent）通过阅读和比较来执行检查。

**示例 2：文档编辑过程**（适用于有代码的技能）：

```markdown  theme={null}
## Document editing process

1. Make your edits to `word/document.xml`
2. **Validate immediately**: `python ooxml/scripts/validate.py unpacked_dir/`
3. If validation fails:
   - Review the error message carefully
   - Fix the issues in the XML
   - Run validation again
4. **Only proceed when validation passes**
5. Rebuild: `python ooxml/scripts/pack.py unpacked_dir/ output.docx`
6. Test the output document
```

验证循环（feedback loop）能及早发现错误。

## 内容指南

### 避免时效性信息

不要包含会过时的信息：

**坏例子：有时效性**（会变得不正确）：

```markdown  theme={null}
If you're doing this before August 2025, use the old API.
After August 2025, use the new API.
```

**好例子**（使用"旧模式"章节）：

```markdown  theme={null}
## Current method

Use the v2 API endpoint: `api.example.com/v2/messages`

## Old patterns

<details>
<summary>Legacy v1 API (deprecated 2025-08)</summary>

The v1 API used: `api.example.com/v1/messages`

This endpoint is no longer supported.
</details>
```

旧模式章节提供了历史背景，而不会让主要内容变得杂乱。

### 使用一致的术语

选择一个术语并在整个技能（skill）中始终如一地使用：

**好 - 一致**：

* 始终使用 "API endpoint"
* 始终使用 "field"
* 始终使用 "extract"

**坏 - 不一致**：

* 混用 "API endpoint"、"URL"、"API route"、"path"
* 混用 "field"、"box"、"element"、"control"
* 混用 "extract"、"pull"、"get"、"retrieve"

一致性有助于代理（agent）理解和遵循指令。

## 常见模式

### 模板模式

为输出格式提供模板。根据需求匹配严格程度。

**对于严格的要求**（如 API 响应或数据格式）：

````markdown  theme={null}
## Report structure

ALWAYS use this exact template structure:

```markdown
# [Analysis Title]

## Executive summary
[One-paragraph overview of key findings]

## Key findings
- Finding 1 with supporting data
- Finding 2 with supporting data
- Finding 3 with supporting data

## Recommendations
1. Specific actionable recommendation
2. Specific actionable recommendation
```
````

**对于灵活的指导**（当适应性调整有帮助时）：

````markdown  theme={null}
## Report structure

Here is a sensible default format, but use your best judgment based on the analysis:

```markdown
# [Analysis Title]

## Executive summary
[Overview]

## Key findings
[Adapt sections based on what you discover]

## Recommendations
[Tailor to the specific context]
```

Adjust sections as needed for the specific analysis type.
````

### 示例模式

对于输出质量取决于示例的技能（skill），像常规提示（prompting）一样提供输入/输出对：

````markdown  theme={null}
## Commit message format

Generate commit messages following these examples:

**Example 1:**
Input: Added user authentication with JWT tokens
Output:
```
feat(auth): implement JWT-based authentication

Add login endpoint and token validation middleware
```

**Example 2:**
Input: Fixed bug where dates displayed incorrectly in reports
Output:
```
fix(reports): correct date formatting in timezone conversion

Use UTC timestamps consistently across report generation
```

**Example 3:**
Input: Updated dependencies and refactored error handling
Output:
```
chore: update dependencies and refactor error handling

- Upgrade lodash to 4.17.21
- Standardize error response format across endpoints
```

Follow this style: type(scope): brief description, then detailed explanation.
````

示例帮助代理（agent）比纯文字描述更清晰地理解期望的风格和详细程度。

### 条件工作流模式

引导代理（agent）通过决策点：

```markdown  theme={null}
## Document modification workflow

1. Determine the modification type:

   **Creating new content?** → Follow "Creation workflow" below
   **Editing existing content?** → Follow "Editing workflow" below

2. Creation workflow:
   - Use docx-js library
   - Build document from scratch
   - Export to .docx format

3. Editing workflow:
   - Unpack existing document
   - Modify XML directly
   - Validate after each change
   - Repack when complete
```

<Tip>
  如果工作流（workflow）变得庞大或复杂，包含许多步骤，考虑将它们推送到单独的文件中，并告诉代理（agent）根据手头的任务读取相应的文件。
</Tip>

## 评估与迭代

### 先构建评估

**在编写大量文档之前先创建评估（evaluation）**。这确保你的技能（skill）解决的是真实问题，而不是记录想象中的需求。

**评估驱动开发（Evaluation-driven development）：**

1. **识别差距**：在没有技能（skill）的情况下，让代理（agent）执行代表性任务。记录具体的失败或缺失的上下文（context）
2. **创建评估（evaluation）**：构建三个测试这些差距的场景
3. **建立基准**：在没有技能（skill）的情况下衡量代理（agent）的性能
4. **编写最简指令**：创建刚好足够的内容来填补差距并通过评估（evaluation）
5. **迭代**：执行评估（evaluation），与基准比较，并进行优化

这种方法确保你在解决实际问题，而不是预测可能永远不会出现的要求。

**评估（evaluation）结构**：

```json  theme={null}
{
  "skills": ["pdf-processing"],
  "query": "Extract all text from this PDF file and save it to output.txt",
  "files": ["test-files/document.pdf"],
  "expected_behavior": [
    "Successfully reads the PDF file using an appropriate PDF processing library or command-line tool",
    "Extracts text content from all pages in the document without missing any pages",
    "Saves the extracted text to a file named output.txt in a clear, readable format"
  ]
}
```

<Note>
  这个例子展示了一个数据驱动的评估（evaluation）和简单的测试评分标准。我们目前不提供内置的运行这些评估（evaluation）的方法。用户可以创建自己的评估（evaluation）系统。评估（evaluation）是你衡量技能（skill）有效性的真理之源。
</Note>

### 与代理迭代开发技能

最有效的技能（skill）开发过程涉及代理本身。与一个实例（称之为"代理 A"）一起创建将由其他实例（称之为"代理 B"）使用的技能（skill）。代理 A 帮助你设计和优化指令，而代理 B 在真实任务中测试它们。这种方法之所以有效，是因为底层模型既理解如何编写有效的代理指令，也理解代理需要哪些信息。

**创建新技能（skill）：**

1. **在没有技能（skill）的情况下完成任务**：使用常规提示（prompting）与代理 A 一起解决问题。在工作的过程中，你会自然地提供上下文（context）、解释偏好并分享过程性知识。注意你反复提供的信息。

2. **识别可复用的模式**：完成任务后，确定你提供的哪些上下文（context）对将来类似的任务会有用。

   **示例**：如果你完成了一个 BigQuery 分析，你可能提供了表名、字段定义、过滤规则（如"始终排除测试账户"）和常见查询模式。

3. **请代理 A 创建一个技能（skill）**："创建一个技能，捕获我们刚才使用的这个 BigQuery 分析模式。包括表模式、命名约定和关于排除测试账户的规则。"

   <Tip>
     现代代理（agent）原生理解技能（skill）的格式和结构。你不需要特殊的系统提示词或"编写技能"技能来获得创建技能的帮助。只需让代理（agent）创建一个技能（skill），它就会生成结构正确的 SKILL.md 内容，包含适当的前置元数据和正文内容。
   </Tip>

4. **检查简洁性**：检查代理 A 是否添加了不必要的解释。要求："删除关于胜率含义的解释——代理已经知道这个了。"

5. **改进信息架构**：请代理 A 更有效地组织内容。例如："组织一下，让表模式放在单独的引用文件中。我们以后可能会添加更多表。"

6. **在类似任务上测试**：在相关的用例中使用技能（skill）与代理 B（一个加载了该技能的新实例）进行测试。观察代理 B 是否能找到正确的信息、正确应用规则并成功处理任务。

7. **基于观察迭代**：如果代理 B 遇到困难或遗漏了某些内容，带着具体细节回到代理 A："代理使用这个技能时，它忘记了按日期筛选 Q4 的数据。我们应该添加一个关于日期过滤模式的部分吗？"

**迭代现有技能（skill）：**

同样的分层模式在改进技能（skill）时继续适用。你在以下角色之间交替：

* **与代理 A 一起工作**（帮助优化技能的专业者）
* **与代理 B 一起测试**（使用技能执行实际工作的代理）
* **观察代理 B 的行为**并将见解带回给代理 A

1. **在实际工作流中使用技能**：给代理 B（已加载技能）实际任务，而不是测试场景

2. **观察代理 B 的行为**：记录它在哪些方面遇到困难、成功或做出意外选择

   **示例观察**："当我要求代理 B 提供区域销售报告时，它编写了查询但忘记了过滤测试账户，尽管技能（skill）中提到了这个规则。"

3. **回到代理 A 寻求改进**：分享当前的 SKILL.md 并描述你观察到的内容。询问："我注意到代理 B 在我要求区域报告时忘记了过滤测试账户。技能（skill）提到了过滤，但也许不够突出？"

4. **审查代理 A 的建议**：代理 A 可能建议重新组织以使规则更突出，使用更强硬的语言如"必须过滤"而不是"始终过滤"，或者重构工作流（workflow）部分。

5. **应用并测试更改**：用代理 A 的优化更新技能（skill），然后在类似的请求上再次用代理 B 测试

6. **根据使用情况重复**：在遇到新场景时继续这个观察-优化-测试的循环。每次迭代都基于真实的代理行为而不是假设来改进技能（skill）。

**收集团队反馈：**

1. 与团队成员分享技能（skill）并观察他们的使用情况
2. 询问：技能（skill）是否在预期时被激活？指令是否清晰？缺少什么？
3. 整合反馈以解决自身使用模式中的盲点

**为什么这种方法有效**：代理 A 理解代理需求，你提供领域专业知识，代理 B 通过真实使用揭示差距，迭代优化基于观察到的行为而非假设来改进技能（skill）。

### 观察代理如何导航技能

在迭代技能（skill）时，注意代理（agent）在实践中如何实际使用它们。关注：

* **意外的探索路径**：代理（agent）是否以你未曾预料到的顺序读取文件？这可能表明你的结构并不像你想象的那么直观
* **遗漏的连接**：代理（agent）是否未能跟随对重要文件的引用？你的链接可能需要更明确或更突出
* **过度依赖某些部分**：如果代理（agent）反复读取同一个文件，考虑该内容是否应该放在主 SKILL.md 中
* **被忽略的内容**：如果代理（agent）从未访问某个捆绑文件，它可能是不必要的，或者在主指令中标记得不够好

基于这些观察而非假设进行迭代。技能（skill）元数据中的 'name' 和 'description' 特别关键。代理（agent）在决定是否针对当前任务触发技能（skill）时使用这些信息。确保它们清楚地描述技能（skill）的功能以及何时使用。

## 要避免的反模式

### 避免 Windows 风格路径

始终在文件路径中使用正斜杠，即使在 Windows 上也是如此：

* ✓ **好**：`scripts/helper.py`，`reference/guide.md`
* ✗ **避免**：`scripts\helper.py`，`reference\guide.md`

Unix 风格路径在所有平台上都有效，而 Windows 风格路径在 Unix 系统上会导致错误。

### 避免提供过多选项

除非必要，不要提供多种方法：

````markdown  theme={null}
**Bad example: Too many choices** (confusing):
"You can use pypdf, or pdfplumber, or PyMuPDF, or pdf2image, or..."

**Good example: Provide a default** (with escape hatch):
"Use pdfplumber for text extraction:
```python
import pdfplumber
```

For scanned PDFs requiring OCR, use pdf2image with pytesseract instead."
````

## 高级：带可执行代码的技能

以下部分聚焦于包含可执行脚本的技能（skill）。如果你的技能（skill）只使用 markdown 指令，请跳至[有效技能检查清单](#checklist-for-effective-skills)。

### 解决问题，而非推卸责任

在编写技能（skill）脚本时，处理错误条件而不是将问题推给代理（agent）。

**好例子：显式处理错误**：

```python  theme={null}
def process_file(path):
    """Process a file, creating it if it doesn't exist."""
    try:
        with open(path) as f:
            return f.read()
    except FileNotFoundError:
        # Create file with default content instead of failing
        print(f"File {path} not found, creating default")
        with open(path, 'w') as f:
            f.write('')
        return ''
    except PermissionError:
        # Provide alternative instead of failing
        print(f"Cannot access {path}, using default")
        return ''
```

**坏例子：推给代理**：

```python  theme={null}
def process_file(path):
    # Just fail and let the agent figure it out
    return open(path).read()
```

配置参数也应该有合理的理由和文档说明，以避免"魔数常数"（Ousterhout 法则）。如果你不知道正确的值是什么，代理（agent）又如何确定呢？

**好例子：自文档化**：

```python  theme={null}
# HTTP requests typically complete within 30 seconds
# Longer timeout accounts for slow connections
REQUEST_TIMEOUT = 30

# Three retries balances reliability vs speed
# Most intermittent failures resolve by the second retry
MAX_RETRIES = 3
```

**坏例子：魔法数字**：

```python  theme={null}
TIMEOUT = 47  # Why 47?
RETRIES = 5   # Why 5?
```

### 提供实用脚本

即使你的代理（agent）可以编写脚本，预制的脚本也有优势：

**实用脚本的好处**：

* 比生成的代码更可靠
* 节省 token（无需在上下文（context）中包含代码）
* 节省时间（无需生成代码）
* 确保跨使用的一致性

<img src="https://mintcdn.com/anthropic-claude-docs/4Bny2bjzuGBK7o00/images/agent-skills-executable-scripts.png?fit=max&auto=format&n=4Bny2bjzuGBK7o00&q=85&s=4bbc45f2c2e0bee9f2f0d5da669bad00" alt="Bundling executable scripts alongside instruction files" data-og-width="2048" width="2048" data-og-height="1154" height="1154" data-path="images/agent-skills-executable-scripts.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/anthropic-claude-docs/4Bny2bjzuGBK7o00/images/agent-skills-executable-scripts.png?w=280&fit=max&auto=format&n=4Bny2bjzuGBK7o00&q=85&s=9a04e6535a8467bfeea492e517de389f 280w, https://mintcdn.com/anthropic-claude-docs/4Bny2bjzuGBK7o00/images/agent-skills-executable-scripts.png?w=560&fit=max&auto=format&n=4Bny2bjzuGBK7o00&q=85&s=e49333ad90141af17c0d7651cca7216b 560w, https://mintcdn.com/anthropic-claude-docs/4Bny2bjzuGBK7o00/images/agent-skills-executable-scripts.png?w=840&fit=max&auto=format&n=4Bny2bjzuGBK7o00&q=85&s=954265a5df52223d6572b6214168c428 840w, https://mintcdn.com/anthropic-claude-docs/4Bny2bjzuGBK7o00/images/agent-skills-executable-scripts.png?w=1100&fit=max&auto=format&n=4Bny2bjzuGBK7o00&q=85&s=2ff7a2d8f2a83ee8af132b29f10150fd 1100w, https://mintcdn.com/anthropic-claude-docs/4Bny2bjzuGBK7o00/images/agent-skills-executable-scripts.png?w=1650&fit=max&auto=format&n=4Bny2bjzuGBK7o00&q=85&s=48ab96245e04077f4d15e9170e081cfb 1650w, https://mintcdn.com/anthropic-claude-docs/4Bny2bjzuGBK7o00/images/agent-skills-executable-scripts.png?w=2500&fit=max&auto=format&n=4Bny2bjzuGBK7o00&q=85&s=0301a6c8b3ee879497cc5b5483177c90 2500w" />

上图展示了可执行脚本如何与指令文件一起工作。指令文件（forms.md）引用脚本，代理（agent）可以执行它而无需将其内容加载到上下文（context）中。

**重要区别**：在你的指令中明确说明代理（agent）应该：

* **执行脚本**（最常见）："Run `analyze_form.py` to extract fields"
* **作为参考阅读**（对于复杂逻辑）："See `analyze_form.py` for the field extraction algorithm"

对于大多数实用脚本，执行是首选，因为它更可靠和高效。有关脚本执行如何工作的详细信息，请参阅下面的[运行时环境](#runtime-environment)章节。

**示例**：

````markdown  theme={null}
## Utility scripts

**analyze_form.py**: Extract all form fields from PDF

```bash
python scripts/analyze_form.py input.pdf > fields.json
```

Output format:
```json
{
  "field_name": {"type": "text", "x": 100, "y": 200},
  "signature": {"type": "sig", "x": 150, "y": 500}
}
```

**validate_boxes.py**: Check for overlapping bounding boxes

```bash
python scripts/validate_boxes.py fields.json
# Returns: "OK" or lists conflicts
```

**fill_form.py**: Apply field values to PDF

```bash
python scripts/fill_form.py input.pdf fields.json output.pdf
```
````

### 使用视觉分析

当输入可以渲染为图像时，让代理（agent）分析它们：

````markdown  theme={null}
## Form layout analysis

1. Convert PDF to images:
   ```bash
   python scripts/pdf_to_images.py form.pdf
   ```

2. Analyze each page image to identify form fields
3. The agent can see field locations and types visually
````

<Note>
  在这个示例中，你需要编写 `pdf_to_images.py` 脚本。
</Note>

代理（agent）的视觉能力有助于理解布局和结构。

### 创建可验证的中间输出

当代理（agent）执行复杂的、开放式任务时，它们可能会犯错。"计划-验证-执行"模式能及早发现错误：代理（agent）首先以结构化格式创建计划，然后用脚本验证该计划，最后再执行。

**示例**：想象一下让代理（agent）根据电子表格更新 PDF 中的 50 个表单字段。没有验证，它可能会引用不存在的字段、创建冲突的值、遗漏必填字段或错误地应用更新。

**解决方案**：使用上面展示的工作流（workflow）模式（PDF 表单填写），但添加一个中间 `changes.json` 文件，在应用更改之前对其进行验证。工作流（workflow）变为：分析 → **创建计划文件** → **验证计划** → 执行 → 验证。

**为什么这种模式有效：**

* **及早发现错误**：验证在更改应用之前发现问题
* **机器可验证**：脚本提供客观验证
* **可逆的计划**：代理可以在不触及原始文件的情况下迭代计划
* **清晰的调试**：错误信息指向具体问题

**何时使用**：批量操作、破坏性更改、复杂的验证规则、高风险的业务操作。

**实现建议**：让验证脚本用具体的错误信息进行详细输出，如"字段 'signature\_date' 未找到。可用字段：customer\_name, order\_total, signature\_date\_signed"，以帮助代理（agent）修复问题。

### 打包依赖

技能（skill）在代码执行环境中运行，受限于特定平台：

* **claude.ai**：可以从 npm 和 PyPI 安装包，并从 GitHub 仓库拉取
* **Anthropic API**：没有网络访问权限，也没有运行时包安装

在 SKILL.md 中列出所需的包，并验证它们在[代码执行工具文档](https://platform.claude.com/docs/en/agents-and-tools/tool-use/code-execution-tool)中可用。

### 运行时环境

技能（skill）在具有文件系统访问权限、bash 命令和代码执行能力的代码执行环境中运行。有关此架构的概念性解释，请参阅概述中的 [技能架构](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview#the-skills-architecture)。

**这对你的编写工作的影响：**

**代理如何访问技能：**

1. **元数据预加载**：启动时，所有技能（skill）的 YAML 前置元数据（YAML frontmatter）中的名称和描述被加载到系统提示词（system prompt）中
2. **按需读取文件**：代理（agent）使用其文件读取工具在需要时从文件系统访问 SKILL.md 和其他文件
3. **脚本高效执行**：实用脚本可以通过 bash 执行，无需将其完整内容加载到上下文（context）中。只有脚本的输出消耗 token
4. **大文件无上下文惩罚**：引用文件、数据或文档在未被实际读取之前不消耗上下文（context）token

* **文件路径很重要**：代理（agent）像文件系统一样导航你的技能（skill）目录。使用正斜杠（`reference/guide.md`），而不是反斜杠
* **描述性地命名文件**：使用能指示内容的名称：`form_validation_rules.md`，而不是 `doc2.md`
* **按发现组织**：按领域或功能组织目录
  * 好：`reference/finance.md`，`reference/sales.md`
  * 坏：`docs/file1.md`，`docs/file2.md`
* **打包全面的资源**：包含完整的 API 文档、丰富的示例、大型数据集；在访问之前没有上下文（context）惩罚
* **确定性操作优先使用脚本**：编写 `validate_form.py` 而不是要求代理（agent）生成验证代码
* **明确执行意图**：
  * "Run `analyze_form.py` to extract fields"（执行）
  * "See `analyze_form.py` for the extraction algorithm"（作为参考阅读）
* **测试文件访问模式**：通过真实请求验证代理（agent）能否导航你的目录结构

**示例：**

```
bigquery-skill/
├── SKILL.md (overview, points to reference files)
└── reference/
    ├── finance.md (revenue metrics)
    ├── sales.md (pipeline data)
    └── product.md (usage analytics)
```

当用户询问收入时，代理（agent）读取 SKILL.md，看到对 `reference/finance.md` 的引用，并调用 bash 仅读取该文件。sales.md 和 product.md 文件保留在文件系统上，在需要之前消耗零个上下文（context）token。这种基于文件系统的模型正是实现渐进式披露（progressive disclosure）的原因。代理（agent）可以导航并有选择地加载每个任务所需的内容。

有关技术架构的完整详情，请参阅技能概述中的 [技能如何工作](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview#how-skills-work)。

### MCP 工具引用

如果你的技能（skill）使用 MCP（模型上下文协议）（MCP (Model Context Protocol)）工具，始终使用完全限定的工具名称以避免"工具未找到"错误。

**格式**：`ServerName:tool_name`

**示例**：

```markdown  theme={null}
Use the BigQuery:bigquery_schema tool to retrieve table schemas.
Use the GitHub:create_issue tool to create issues.
```

其中：

* `BigQuery` 和 `GitHub` 是 MCP 服务器名称
* `bigquery_schema` 和 `create_issue` 是这些服务器内的工具名称

没有服务器前缀，代理（agent）可能无法定位工具，尤其是在多个 MCP 服务器可用时。

### 避免假设工具已安装

不要假设包已经可用：

````markdown  theme={null}
**Bad example: Assumes installation**:
"Use the pdf library to process the file."

**Good example: Explicit about dependencies**:
"Install required package: `pip install pypdf`

Then use it:
```python
from pypdf import PdfReader
reader = PdfReader("file.pdf")
```"
````

## 技术说明

### YAML 前置元数据要求

SKILL.md 的前置元数据需要 `name`（最多 64 个字符）和 `description`（最多 1024 个字符）字段。有关完整结构详情，请参阅 [技能概述](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview#skill-structure)。

### Token 预算

保持 SKILL.md 正文在 500 行以内以获得最佳性能。如果你的内容超过此限制，使用前面描述的渐进式披露（progressive disclosure）模式将其拆分到多个文件中。有关架构详情，请参阅 [技能概述](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview#how-skills-work)。

## 有效技能检查清单

在分享技能（skill）之前，请验证：

### 核心质量

* [ ] 描述具体且包含关键术语
* [ ] 描述既包含技能（skill）的功能也包含何时使用
* [ ] SKILL.md 正文在 500 行以内
* [ ] 额外详情在单独的文件中（如果需要）
* [ ] 无时效性信息（或在"旧模式"部分）
* [ ] 全篇术语一致
* [ ] 示例具体而非抽象
* [ ] 文件引用只有一层深度
* [ ] 适当使用了渐进式披露（progressive disclosure）
* [ ] 工作流（workflow）有清晰的步骤

### 代码和脚本

* [ ] 脚本解决问题而非推给代理（agent）
* [ ] 错误处理明确且有用
* [ ] 没有"魔数常数"（所有值都有合理理由）
* [ ] 所需包已在指令中列出并已验证可用
* [ ] 脚本有清晰的文档
* [ ] 没有 Windows 风格路径（全部使用正斜杠）
* [ ] 关键操作有验证/核实步骤
* [ ] 对质量关键任务包含反馈循环（feedback loop）

### 测试

* [ ] 至少创建了三个评估（evaluation）
* [ ] 在 Haiku、Sonnet 和 Opus 上测试过
* [ ] 使用真实使用场景测试过
* [ ] 整合了团队反馈（如适用）

## 下一步

<CardGroup cols={2}>
  <Card title="Get started with Agent Skills" icon="rocket" href="https://platform.claude.com/docs/en/agents-and-tools/agent-skills/quickstart">
    Create your first Skill
  </Card>

  <Card title="Use Skills in Claude Code" icon="terminal" href="https://code.claude.com/docs/en/skills">
    Create and manage Skills in Claude Code
  </Card>

  <Card title="Use Skills with the API" icon="code" href="https://platform.claude.com/docs/en/build-with-claude/skills-guide">
    Upload and use Skills programmatically
  </Card>
</CardGroup>
