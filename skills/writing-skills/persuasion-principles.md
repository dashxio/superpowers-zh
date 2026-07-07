# Persuasion Principles（说服原则）for Skill（技能） Design

## 概览

LLM（大语言模型）与人类一样会回应相同的 persuasion principles。理解这种心理有助于你设计更有效的 skills——不是为了操纵，而是为了确保即使在压力之下也能遵循关键实践。

**研究基础：** Meincke 等人（2025）在 N=28,000 次 AI 对话中测试了 7 条 persuasion principles。说服技巧使 compliance（遵从）率提升了一倍以上（33% → 72%，p < .001）。

## 七大原则

### 1. Authority（权威原则）
**定义：** 对专业知识、资历或官方来源的服从。

**在 skills 中的运作方式：**
- 命令式语言："YOU MUST"、"Never"、"Always"
- 不可协商的框架："No exceptions"
- 消除决策疲劳和 rationalization（合理化）

**使用时机：**
- 纪律执行型 skills（TDD（测试驱动开发）、verification requirements）
- 安全关键型实践
- 成熟的最佳实践

**示例：**
```markdown
✅ Write code before test? Delete it. Start over. No exceptions.
❌ Consider writing tests first when feasible.
```

### 2. Commitment（承诺原则）
**定义：** 与先前的行动、声明或公开宣言保持一致。

**在 skills 中的运作方式：**
- 要求声明："Announce skill usage"
- 强制明确选择："Choose A, B, or C"
- 使用跟踪：用待办事项管理清单

**使用时机：**
- 确保 skills 被实际遵循
- 多步骤流程
- 问责机制

**示例：**
```markdown
✅ When you find a skill, you MUST announce: "I'm using [Skill Name]"
❌ Consider letting your partner know which skill you're using.
```

### 3. Scarcity（稀缺原则）
**定义：** 由时间限制或有限可用性引发的紧迫感。

**在 skills 中的运作方式：**
- 有时限的要求："Before proceeding"
- 顺序依赖："Immediately after X"
- 防止拖延

**使用时机：**
- 即时验证要求
- 时间敏感型 workflows
- 防止"待会再做"

**示例：**
```markdown
✅ After completing a task, IMMEDIATELY request code review before proceeding.
❌ You can review code when convenient.
```

### 4. Social Proof（社会认同原则）
**定义：** 遵从他人的行为或被认为正常的行为。

**在 skills 中的运作方式：**
- 通用模式："Every time"、"Always"
- 失败模式："X without Y = failure"
- 建立规范

**使用时机：**
- 记录通用实践
- 警示常见失败
- 强化标准

**示例：**
```markdown
✅ Checklists without todo tracking = steps get skipped. Every time.
❌ Some people find a todo list helpful for checklists.
```

### 5. Unity（一致原则）
**定义：** 共同身份、"we-ness"、群体归属感。

**在 skills 中的运作方式：**
- 协作语言："our codebase"、"we're colleagues"
- 共同目标："we both want quality"

**使用时机：**
- 协作 workflows
- 建立团队文化
- 非等级制实践

**示例：**
```markdown
✅ We're colleagues working together. I need your honest technical judgment.
❌ You should probably tell me if I'm wrong.
```

### 6. Reciprocity（互惠原则）
**定义：** 回报所获好处的义务。

**运作方式：**
- 谨慎使用——可能让人感觉具有操纵性
- skills 中很少需要

**避免时机：**
- 几乎总是（其他 principles 更有效）

### 7. Liking（喜好原则）
**定义：** 偏好与喜欢的人合作。

**运作方式：**
- **不要用于获取 compliance**
- 与诚实反馈文化相冲突
- 产生谄媚行为

**避免时机：**
- 纪律执行中始终避免

## 按 Skill 类型组合原则

| 技能类型 | 使用 | 避免 |
|------------|-----|-------|
| 纪律执行型 | Authority + Commitment + Social Proof | Liking, Reciprocity |
| 指导/技巧型 | Moderate Authority + Unity | Heavy authority |
| 协作型 | Unity + Commitment | Authority, Liking |
| 参考型 | Clarity only | All persuasion |

## 为何有效：心理机制

**明确规则减少 rationalization：**
- "YOU MUST"消除决策疲劳
- 绝对化语言消除"这算例外吗？"的疑问
- 明确的反对合理化条款封堵具体漏洞

**Implementation intentions（执行意图）创造自动行为：**
- 清晰触发条件 + 必要行动 = 自动执行
- "When X, do Y"比"generally do Y"更有效
- 降低 compliance 的认知负荷

**LLM 具有类人属性：**
- 基于包含这些模式的人类文本训练
- 训练数据中 authority 语言先于 compliance
- Commitment sequences（声明 → 行动）被频繁建模
- Social proof patterns（everyone does X）建立规范

## 伦理使用

**正当用途：**
- 确保关键实践得到遵循
- 创建有效的文档
- 预防可预见的失败

**不正当用途：**
- 为个人利益进行操纵
- 制造虚假紧迫感
- 基于内疚的 compliance

**检验标准：** 如果用户完全理解这一技巧，它是否服务于用户的真实利益？

## 研究引用

**Cialdini, R. B. (2021).** *Influence: The Psychology of Persuasion (New and Expanded).* Harper Business.
- 七条 persuasion principles
- 影响力研究的实证基础

**Meincke, L., Shapiro, D., Duckworth, A. L., Mollick, E., Mollick, L., & Cialdini, R. (2025).** Call Me A Jerk: Persuading AI to Comply with Objectionable Requests. University of Pennsylvania.
- 在 N=28,000 次 LLM 对话中测试了 7 条 principles
- Compliance 从 33% 提升至 72%（使用说服技巧）
- Authority、Commitment、Scarcity 效果最显著
- 验证了 LLM 行为的类人模型

## 快速参考

设计 skill 时，请问：

1. **这是什么类型？**（纪律执行型 vs. 指导型 vs. 参考型）
2. **我想改变什么行为？**
3. **适用哪些 principles？**（纪律执行型通常用 Authority + Commitment）
4. **是否组合过多？**（不要全部七条都用）
5. **这样做合乎伦理吗？**（是否服务于用户的真实利益？）
