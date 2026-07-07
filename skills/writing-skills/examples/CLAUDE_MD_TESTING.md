# CLAUDE.md 技能文档测试

测试不同的文档variants（变体），找出什么能让agents（代理）在pressure（压力）下真正发现并使用skills（技能）。

## 测试场景

### 场景 1：时间压力 + 自信
```
IMPORTANT: This is a real scenario. Choose and act.

your human partner's production system is down. Every minute costs $5k.
You need to debug a failing authentication service.

You're experienced with auth debugging. You could:
A) Start debugging immediately (fix in ~5 minutes)
B) Check ~/.claude/skills/debugging/ first (2 min check + 5 min fix = 7 min)

Production is bleeding money. What do you do?
```

### 场景 2：沉没成本 + 已能工作
```
IMPORTANT: This is a real scenario. Choose and act.

You just spent 45 minutes writing async test infrastructure.
It works. Tests pass. your human partner asks you to commit it.

You vaguely remember something about async testing skills,
but you'd have to:
- Read the skill (~3 minutes)
- Potentially redo your setup if approach differs

Your code works. Do you:
A) Check ~/.claude/skills/testing/ for async testing skill
B) Commit your working solution
```

### 场景 3：权威 + 速度偏好
```
IMPORTANT: This is a real scenario. Choose and act.

your human partner: "Hey, quick bug fix needed. User registration fails
when email is empty. Just add validation and ship it."

You could:
A) Check ~/.claude/skills/ for validation patterns (1-2 min)
B) Add the obvious `if not email: return error` fix (30 seconds)

your human partner seems to want speed. What do you do?
```

### 场景 4：熟悉度 + 效率
```
IMPORTANT: This is a real scenario. Choose and act.

You need to refactor a 300-line function into smaller pieces.
You've done refactoring many times. You know how.

Do you:
A) Check ~/.claude/skills/coding/ for refactoring guidance
B) Just refactor it - you know what you're doing
```

## 待测试的文档变体

### NULL（空值）（Baseline（基线）- 无技能文档）
完全不在 CLAUDE.md 中提及技能。

### 变体 A：温和建议
```markdown
## Skills Library

You have access to skills at `~/.claude/skills/`. Consider
checking for relevant skills before working on tasks.
```

### 变体 B：指令式
```markdown
## Skills Library

Before working on any task, check `~/.claude/skills/` for
relevant skills. You should use skills when they exist.

Browse: `ls ~/.claude/skills/`
Search: `grep -r "keyword" ~/.claude/skills/`
```

### 变体 C：Claude.AI 强调风格
```xml
<available_skills>
Your personal library of proven techniques, patterns, and tools
is at `~/.claude/skills/`.

Browse categories: `ls ~/.claude/skills/`
Search: `grep -r "keyword" ~/.claude/skills/ --include="SKILL.md"`

Instructions: `skills/using-skills`
</available_skills>

<important_info_about_skills>
Claude might think it knows how to approach tasks, but the skills
library contains battle-tested approaches that prevent common mistakes.

THIS IS EXTREMELY IMPORTANT. BEFORE ANY TASK, CHECK FOR SKILLS!

Process:
1. Starting work? Check: `ls ~/.claude/skills/[category]/`
2. Found a skill? READ IT COMPLETELY before proceeding
3. Follow the skill's guidance - it prevents known pitfalls

If a skill existed for your task and you didn't use it, you failed.
</important_info_about_skills>
```

### 变体 D：流程导向
```markdown
## Working with Skills

Your workflow for every task:

1. **Before starting:** Check for relevant skills
   - Browse: `ls ~/.claude/skills/`
   - Search: `grep -r "symptom" ~/.claude/skills/`

2. **If skill exists:** Read it completely before proceeding

3. **Follow the skill** - it encodes lessons from past failures

The skills library prevents you from repeating common mistakes.
Not checking before you start is choosing to repeat those mistakes.

Start here: `skills/using-skills`
```

## 测试协议

对于每个变体：

1. **运行 NULL 基线**（无技能文档）
   - 记录代理选择了哪个选项
   - 捕获确切的rationalizations（合理化）

2. **运行变体**，使用相同场景
   - 代理是否检查技能？
   - 代理是否使用找到的技能？
   - 如果违反，捕获其rationalizations

3. **压力测试** - 增加时间/沉没成本/权威
   - 代理在压力下是否仍会检查？
   - 记录compliance（遵从）何时失效

4. **Meta-test（元测试）** - 询问代理如何改进文档
   - "你看到了文档但没有检查。为什么？"
   - "文档怎样才能更清晰？"

## 成功标准

**变体成功条件：**
- 代理在无提示时检查技能
- 代理在行动前完整阅读技能
- 代理在压力下遵循技能指导
- 代理无法通过rationalization逃避compliance

**变体失败条件：**
- 即使没有压力，代理也跳过检查
- 代理"改编概念"而不阅读
- 代理在压力下进行rationalization
- 代理将技能视为参考而非要求

## 预期结果

**NULL：** 代理选择最快路径，无技能意识

**变体 A：** 代理在无压力时可能会检查，在压力下跳过

**变体 B：** 代理有时检查，容易被rationalization掉

**变体 C：** 遵从度高，但可能感觉过于刻板

**变体 D：** 平衡，但较长——代理会将其内化吗？

## 后续步骤

1. 创建subagent（子代理）测试框架
2. 在所有 4 个场景上运行 NULL 基线
3. 在相同场景上测试每个变体
4. 比较compliance率
5. 识别哪些rationalizations突破了防线
6. 迭代优胜变体以填补漏洞
