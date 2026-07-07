# Superpowers-zh（中文版）

> **这是 [Superpowers](https://github.com/obra/superpowers) 的中文翻译版本，由 [Jesse Vincent](https://blog.fsck.com) 和 [Prime Radiant](https://primeradiant.com) 团队创建。翻译工作由社区完成，功能与原版保持一致，仅做语言层面的本地化。原始项目及所有荣誉归属于原作者。**

Superpowers 是一套完整的软件开发方法论，专为你的编码代理（coding agent）而设计，构建于一组可组合的技能（skills）和初始指令之上，确保你的代理（agent）能够正确使用它们。

## 工作原理

从你启动编码代理的那一刻起，它*不会*直接跳入写代码。相反，它会退一步，询问你真正想要做什么。

一旦它从对话中梳理出需求规格，它会以足够短的段落展示给你，让你能够真正阅读和理解。

当你确认设计后，你的 agent 会制定一份实现计划，这份计划清晰到足以让一位热情但品味欠佳、缺乏判断力、没有项目背景且厌恶测试的初级工程师也能遵循。该计划强调真正的 红/绿 TDD（Test-Driven Development）、YAGNI（You Aren't Gonna Need It）和 DRY（Don't Repeat Yourself）。

接下来，一旦你说"开始"，它会启动一个 *subagent-driven-development*（子代理驱动开发）流程，让 agent 逐个完成每个工程任务，检查并审查它们的工作，然后继续推进。你的 agent 通常可以自主工作数小时而不会偏离你共同制定的计划。

还有更多功能，但以上是系统的核心。由于技能会自动触发，你无需做任何特殊操作。你的编码代理就这样拥有了 Superpowers。

## 安装

### Claude Code

```bash
# 添加 superpowers-zh 市场
/plugin marketplace add dashxio/superpowers-zh

# 安装插件
/plugin install superpowers-zh@superpowers-zh
```

安装后，Superpowers 会在每次会话启动时自动激活，所有技能按需触发。

> 其他 harness（Antigravity、Codex、Cursor、Pi 等）用户请参阅[原版 Superpowers 安装指南](https://github.com/obra/superpowers#installation)。

## 基本工作流

1. **brainstorming**（头脑风暴） - 在编写代码前激活。通过提问优化粗略想法，探索替代方案，分章节展示设计以供验证。保存设计文档。

2. **using-git-worktrees**（使用 Git 工作树） - 设计批准后激活。在新分支上创建隔离工作空间，运行项目设置，验证干净的测试基线。

3. **writing-plans**（编写计划） - 设计批准后激活。将工作分解为小块任务（每个 2-5 分钟）。每个任务包含精确的文件路径、完整代码和验证步骤。

4. **subagent-driven-development**（子代理驱动开发）或 **executing-plans**（执行计划） - 计划就绪后激活。为每个任务分派全新的子代理（subagent），进行两阶段审查（先检查规格符合性，再检查代码质量），或分批执行并设置人工检查点（checkpoint）。

5. **test-driven-development**（测试驱动开发） - 在实现过程中激活。强制遵循 RED-GREEN-REFACTOR（红-绿-重构）循环：编写会失败的测试，观察其失败，编写最简代码，观察其通过，提交（commit）。删除在测试之前编写的代码。

6. **requesting-code-review**（请求代码审查） - 在任务之间激活。对照计划审查代码，按严重程度报告问题。关键问题会阻止进度。

7. **finishing-a-development-branch**（完成开发分支） - 在任务完成后激活。验证测试，提供选项（合并/PR/保留/丢弃），清理工作树（worktree）。

**agent 在执行任何任务前都会检查相关技能。** 这些是强制性工作流，而非建议。

## 内部构成

### 技能库

**测试**
- **test-driven-development** - RED-GREEN-REFACTOR 循环（包含测试反模式参考）

**调试**
- **systematic-debugging**（系统化调试） - 四阶段根因分析流程（包含根因追踪、纵深防御、基于条件的等待等技术）
- **verification-before-completion**（完成前验证） - 确保问题真正被修复

**协作**
- **brainstorming** - 苏格拉底式设计优化
- **writing-plans** - 详细实现计划
- **executing-plans** - 带检查点的批量执行
- **dispatching-parallel-agents**（调度并行代理） - 并发子代理工作流
- **requesting-code-review** - 预审查清单
- **receiving-code-review**（接收代码审查） - 响应反馈
- **using-git-worktrees** - 并行开发分支
- **finishing-a-development-branch** - 合并/PR 决策工作流
- **subagent-driven-development** - 带两阶段审查（规格符合性，然后代码质量）的快速迭代

**元**
- **writing-skills**（编写技能） - 遵循最佳实践创建新技能（包含测试方法论）
- **using-superpowers**（使用 Superpowers） - 技能系统入门

## 理念

- **测试驱动开发** - 永远先写测试
- **系统化优于临时方案** - 流程优于猜测
- **降低复杂度** - 简单性是首要目标
- **证据胜于声明** - 在宣布成功前先验证

阅读[原始发布公告](https://blog.fsck.com/2025/10/09/superpowers/)。

## 贡献指南

以下是 Superpowers 的一般贡献流程。请注意，我们通常不接受新技能的贡献，且对技能的任何更新必须在我们支持的所有编码代理上都能正常工作。

1. Fork 本仓库
2. 切换到 `dev` 分支
3. 为你的工作创建一个分支
4. 遵循 `writing-skills` 技能来创建和测试新技能或修改现有技能
5. 提交 PR，务必填写 pull request 模板。

技能行为测试使用 [superpowers-evals](https://github.com/prime-radiant-inc/superpowers-evals/) 中的 drill 评估框架（eval harness），克隆至 `evals/` 目录——参见 `evals/README.md` 了解设置方式。插件基础设施测试位于 `tests/` 目录，通过相应的 `run-*.sh` 或 `npm test` 运行。

完整指南请参见 `skills/writing-skills/SKILL.md`。

## 更新

Superpowers 的更新方式因编码代理而异，但通常是自动完成的。

## 许可协议

MIT 许可证 - 详见 LICENSE 文件

## 社区

Superpowers 由 [Jesse Vincent](https://blog.fsck.com) 和 [Prime Radiant](https://primeradiant.com) 的团队成员共同构建。

- **Discord**：[加入](https://discord.gg/35wsABTejz)原版社区
- **问题追踪**：[原版仓库 Issues](https://github.com/obra/superpowers/issues)（中文翻译相关问题请提交至[本仓库](https://github.com/dashxio/superpowers-zh/issues)）
- **发布公告**：[注册](https://primeradiant.com/superpowers/)获取原版更新通知
