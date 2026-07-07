# 纵深防御验证（Defense-in-Depth Validation）

## 概述

当你修复一个由无效数据引起的错误时，在一个地方添加验证似乎就够了。但是，单一的检查可能会被不同的代码路径、重构或模拟（mocks）绕过。

**核心原则：** 在数据经过的每一层都进行验证。让错误在结构上变得不可能。

## 为什么需要多层

单一验证："我们修复了错误"
多层验证："我们让错误变得不可能"

不同层级捕获不同的情况：
- 入口验证（Entry validation）捕获大多数错误
- 业务逻辑（Business logic）捕获边界情况
- 环境守卫（Environment guards）防止特定上下文中的危险
- 调试日志（Debug logging）在其他层失效时提供帮助

## 四层架构

### 第一层：入口验证（Entry Point Validation）
**目的：** 在 API 边界拒绝明显无效的输入

```typescript
function createProject(name: string, workingDirectory: string) {
  if (!workingDirectory || workingDirectory.trim() === '') {
    throw new Error('workingDirectory cannot be empty');
  }
  if (!existsSync(workingDirectory)) {
    throw new Error(`workingDirectory does not exist: ${workingDirectory}`);
  }
  if (!statSync(workingDirectory).isDirectory()) {
    throw new Error(`workingDirectory is not a directory: ${workingDirectory}`);
  }
  // ... 继续执行
}
```

### 第二层：业务逻辑验证（Business Logic Validation）
**目的：** 确保数据对此操作有意义

```typescript
function initializeWorkspace(projectDir: string, sessionId: string) {
  if (!projectDir) {
    throw new Error('projectDir required for workspace initialization');
  }
  // ... 继续执行
}
```

### 第三层：环境守卫（Environment Guards）
**目的：** 防止特定上下文中的危险操作

```typescript
async function gitInit(directory: string) {
  // 在测试中，拒绝在临时目录之外执行 git init
  if (process.env.NODE_ENV === 'test') {
    const normalized = normalize(resolve(directory));
    const tmpDir = normalize(resolve(tmpdir()));

    if (!normalized.startsWith(tmpDir)) {
      throw new Error(
        `Refusing git init outside temp dir during tests: ${directory}`
      );
    }
  }
  // ... 继续执行
}
```

### 第四层：调试仪表（Debug Instrumentation）
**目的：** 捕获上下文用于事后分析

```typescript
async function gitInit(directory: string) {
  const stack = new Error().stack;
  logger.debug('About to git init', {
    directory,
    cwd: process.cwd(),
    stack,
  });
  // ... 继续执行
}
```

## 应用模式

当你发现一个错误时：

1. **追溯数据流** - 无效值从哪里来？在哪里被使用？
2. **映射所有检查点** - 列出数据经过的每一个点
3. **在每一层添加验证** - 入口、业务、环境、调试
4. **测试每一层** - 尝试绕过第一层，验证第二层能捕获它

## 会话示例

错误：空的 `projectDir` 导致在源代码中执行 `git init`

**数据流：**
1. 测试设置 → 空字符串
2. `Project.create(name, '')`
3. `WorkspaceManager.createWorkspace('')`
4. `git init` 在 `process.cwd()` 中运行

**添加的四层防御：**
- 第一层：`Project.create()` 验证不为空/存在/可写
- 第二层：`WorkspaceManager` 验证 projectDir 不为空
- 第三层：`WorktreeManager` 在测试中拒绝在 tmpdir 之外执行 git init
- 第四层：在 git init 之前记录堆栈追踪

**结果：** 全部 1847 个测试通过，错误无法复现

## 关键洞察

所有四层都是必要的。在测试过程中，每一层都捕获了其他层遗漏的错误：
- 不同的代码路径绕过了入口验证
- 模拟绕过了业务逻辑检查
- 不同平台上的边界情况需要环境守卫
- 调试日志识别出结构性的误用

**不要止步于一个验证点。** 在每一层都添加检查。
