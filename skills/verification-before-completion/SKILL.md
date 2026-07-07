---
name: verification-before-completion
description: 当即将声称工作已完成、已修复或通过时，在提交或创建 PR 之前使用 - 要求在做出任何成功声明之前运行验证命令并确认输出；始终先有证据，再下断言
---

# 完成前的验证

## 概述

未经验证就声称工作已完成，这不是效率，而是不诚实。

**核心原则：** 先有证据，再下断言，始终如此。

**违反此规则的文字即违反此规则的精神。**

## 铁律

```
NO COMPLETION CLAIMS WITHOUT FRESH VERIFICATION EVIDENCE
```

如果你在本轮对话中尚未运行验证命令，你就不能声称它通过。

## 门控函数

```
BEFORE claiming any status or expressing satisfaction:

1. IDENTIFY: What command proves this claim?
2. RUN: Execute the FULL command (fresh, complete)
3. READ: Full output, check exit code, count failures
4. VERIFY: Does output confirm the claim?
   - If NO: State actual status with evidence
   - If YES: State claim WITH evidence
5. ONLY THEN: Make the claim

Skip any step = lying, not verifying
```

## 常见失败

| 声明 | 要求 | 不足以证明 |
|-------|----------|----------------|
| 测试通过 | 测试命令输出：0 失败 | 之前的运行，"应该能过" |
| Linter 干净 | Linter 输出：0 错误 | 部分检查，推断 |
| 构建成功 | 构建命令：退出码 0 | Linter 通过，日志看起来没问题 |
| Bug 已修复 | 测试原始症状：通过 | 代码已修改，假设已修复 |
| 回归测试可用 | 红绿循环已验证 | 测试通过一次 |
| Agent 已完成 | VCS diff 显示变更 | Agent 报告"成功" |
| 需求已满足 | 逐行检查清单 | 测试通过 |

## 红线警示 - 立即停止

- 使用"应该"、"可能"、"似乎"
- 在验证前表达满意（"太好了！"、"完美！"、"完成了！"等）
- 即将在未验证的情况下提交/推送/创建 PR
- 信任 agent 的成功报告
- 依赖部分验证
- 想着"就这一次"
- 感到疲惫，想赶紧结束工作
- **任何暗示成功但尚未运行验证的措辞**

## 合理化预防

| 借口 | 现实 |
|--------|---------|
| "现在应该能行了" | 运行验证 |
| "我很确信" | 确信 ≠ 证据 |
| "就这一次" | 没有例外 |
| "Linter 通过了" | Linter ≠ 编译器 |
| "Agent 说成功了" | 独立验证 |
| "我累了" | 疲惫 ≠ 借口 |
| "部分检查就够了" | 部分证明不了什么 |
| "换了个说法所以规则不适用" | 精神重于文字 |

## 关键模式

**测试：**
```
✅ [Run test command] [See: 34/34 pass] "All tests pass"
❌ "Should pass now" / "Looks correct"
```

**回归测试（TDD 红-绿-红）：**
```
✅ Write → Run (pass) → Revert fix → Run (MUST FAIL) → Restore → Run (pass)
❌ "I've written a regression test" (without red-green verification)
```

**构建：**
```
✅ [Run build] [See: exit 0] "Build passes"
❌ "Linter passed" (linter doesn't check compilation)
```

**需求：**
```
✅ Re-read plan → Create checklist → Verify each → Report gaps or completion
❌ "Tests pass, phase complete"
```

**Agent 委派：**
```
✅ Agent reports success → Check VCS diff → Verify changes → Report actual state
❌ Trust agent report
```

## 为何重要

来自24次失败记忆：
- 你的 human partner（人类搭档）说"我不相信你" — 信任被打破
- 未定义的函数被发布 — 会导致崩溃
- 缺失的需求被发布 — 功能不完整
- 时间浪费在虚假完成上 → 重定向 → 返工
- 违反："诚实是核心价值观。如果你撒谎，你会被替换。"

## 何时应用

**始终在以下情况之前：**
- 任何形式的成功/完成声明
- 任何满意表达
- 任何关于工作状态的正面陈述
- 提交、创建 PR、任务完成
- 进入下一个任务
- 委派给 agent

**规则适用于：**
- 确切的用语
- 转述和同义词
- 暗示成功
- 任何暗示完成或正确的沟通

## 底线

**验证没有捷径。**

运行命令。读取输出。然后才声明结果。

这是不可协商的。
