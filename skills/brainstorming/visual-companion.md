# Visual Companion Guide（视觉伴侣指南）

基于浏览器的视觉头脑风暴（brainstorming）伴侣，用于展示原型图（mockups）、图表和选项。

## 何时使用

根据每个问题决定，而不是每个会话。判断标准：**用户通过看到它比阅读它更好理解吗？**

当内容本身是视觉性的时，**使用浏览器（browser）**：

- **UI原型（UI mockups）** —— 线框图（wireframes）、布局、导航结构、组件设计
- **架构图（Architecture diagrams）** —— 系统组件、数据流、关系图
- **并排视觉比较（Side-by-side visual comparisons）** —— 比较两种布局、两种配色方案、两种设计方向
- **设计打磨（Design polish）** —— 当问题关于外观和感觉、间距、视觉层次时
- **空间关系（Spatial relationships）** —— 状态机、流程图、实体关系图

当内容是文本或表格时，**使用终端（terminal）**：

- **需求和范围问题** —— "X是什么意思？"，"哪些功能在范围内？"
- **概念性的A/B/C选择** —— 在用文字描述的方案之间做选择
- **权衡列表** —— 优缺点、对比表
- **技术决策** —— API设计、数据建模、架构方案选择
- **澄清问题** —— 任何答案是用文字而非视觉偏好的问题

一个关于UI主题的问题不自动成为视觉问题。"你想要什么样的向导？"是概念性的——使用终端。"这些向导布局中哪个感觉对？"是视觉性的——使用浏览器。

## 工作原理

服务器（Server）监视一个目录中的HTML文件，并将最新的文件提供给浏览器。你将HTML内容写入 `screen_dir`，用户在浏览器中看到它并可以点击选择选项。选择结果被记录到 `state_dir/events`，你可以在下一轮对话中读取。

**内容片段（Content fragments）vs 完整文档：** 如果你的HTML文件以 `<!DOCTYPE` 或 `<html` 开头，服务器按原样提供（仅注入辅助脚本）。否则，服务器会自动将你的内容包裹在框架模板（frame template）中——添加页头、CSS主题、连接状态和所有交互基础设施。**默认编写内容片段。** 仅当你需要完全控制页面时才编写完整文档。

## 启动会话

```bash
# Start AFTER the user approves the companion. --open auto-opens their browser on
# the first screen; --project-dir persists mockups and enables same-port restart.
scripts/start-server.sh --project-dir /path/to/project --open

# Returns: {"type":"server-started","port":52341,
#           "url":"http://localhost:52341/?key=ab12…",
#           "screen_dir":"/path/to/project/.superpowers/brainstorm/12345-1706000000/content",
#           "state_dir":"/path/to/project/.superpowers/brainstorm/12345-1706000000/state"}
```

保存响应中的 `screen_dir` 和 `state_dir`。使用 `--open` 时，浏览器会在你推送第一个屏幕时自动打开——你不需要让用户手动打开，但仍要分享URL作为备用（无头/远程设置无法自动打开）。

**URL包含一个会话密钥（session key）（`?key=…`）。** 服务器拒绝任何没有密钥的请求，所以始终给用户来自 `url` 字段的**完整**URL——永远不要去掉查询字符串，也永远不要只给一个裸的 `http://host:port`。该密钥控制HTTP和WebSocket访问，因此其他浏览器标签页或网络上的其他机器无法读取屏幕或注入事件。首次加载后，浏览器通过cookie记住密钥，因此刷新和 `/files/*` 资源无需重复输入。

**查找连接信息：** 服务器将其启动JSON写入 `$STATE_DIR/server-info`。如果你在后台启动服务器且没有捕获标准输出，读取该文件即可获取URL和端口。使用 `--project-dir` 时，检查 `<project>/.superpowers/brainstorm/` 下的会话目录。

**注意：** 将项目根目录作为 `--project-dir` 传入，以便原型图持久保存在 `.superpowers/brainstorm/` 中并在服务器重启后保留。没有该参数，文件会进入 `/tmp` 并被清理。提醒用户如果 `.superpowers/` 尚未在 `.gitignore` 中则添加它。

**按平台启动服务器：**

**Claude Code：**
```bash
# Default mode works — the script backgrounds the server itself.
scripts/start-server.sh --project-dir /path/to/project --open
```

在Windows上，脚本会自动检测并切换到前台模式（这会阻塞工具调用）。在Bash工具调用中使用 `run_in_background: true`，使服务器能在跨对话轮次中存活，然后在下一轮读取 `$STATE_DIR/server-info` 以获取URL和端口。

**Codex：**
```bash
# Codex reaps background processes. The script auto-detects CODEX_CI and
# switches to foreground mode. Run it normally — no extra flags needed.
scripts/start-server.sh --project-dir /path/to/project --open
```

**Copilot CLI：**
```bash
# Use --foreground and start the server via the bash tool with mode: "async"
# so the process survives across turns. Capture the returned shellId for
# read_bash / stop_bash if you need to interact with it later.
scripts/start-server.sh --project-dir /path/to/project --open --foreground
```

**其他环境：** 服务器必须在跨对话轮次的后台保持运行。如果你的环境收割已分离的进程，请使用 `--foreground` 并通过平台的背景执行机制启动命令。

如果从浏览器无法访问URL（常见于远程/容器化环境），绑定一个非回环主机：

```bash
scripts/start-server.sh \
  --project-dir /path/to/project \
  --host 0.0.0.0 \
  --url-host localhost
```

使用 `--url-host` 控制返回的URL JSON中打印的主机名。

## 工作循环

1. **确认服务器在运行**，然后在 `screen_dir` 中写入一个新的HTML文件：
   - **必需：在引用URL或推送屏幕之前确认服务器仍在运行。** 检查 `$STATE_DIR/server-info` 存在且 `$STATE_DIR/server-stopped` 不存在。如果服务器已关闭，使用相同的 `--project-dir` 重新运行 `start-server.sh`——它会复用同一端口，因此用户打开的标签页会自动重新连接（服务器关闭时它会显示一个"已暂停"的覆盖层），你无需发送新的URL。服务器在空闲4小时后自动退出（可通过 `--idle-timeout-minutes` 配置）。
   - 使用语义化的文件名：`platform.html`, `visual-style.html`, `layout.html`
   - **永远不要重复使用文件名**——每个屏幕使用新文件
   - 使用你的文件创建工具——**永远不要使用 cat/heredoc**（会在终端中产生噪声）
   - 服务器自动提供最新的文件

2. **告诉用户期望什么并结束你的轮次：**
   - 提醒他们URL（每一步都要，不仅仅是第一次）
   - 简要文字说明屏幕上显示的内容（例如："显示主页的3种布局选项"）
   - 让他们在终端中回应："看看然后告诉我你的想法。如果愿意，可以点击选择选项。"

3. **在下一轮对话中**——用户通过终端回应后：
   - 如果存在，读取 `$STATE_DIR/events` —— 其中包含用户的浏览器交互（点击、选择）作为JSON行
   - 与用户的终端文本合并以获取全貌
   - 终端消息是主要反馈；`state_dir/events` 提供结构化的交互数据

4. **迭代或推进**——如果反馈改变了当前屏幕，写入一个新文件（例如 `layout-v2.html`）。只有当前步骤验证通过后才进入下一个问题。

5. **返回终端时卸载（Unload）**——当下一步不需要浏览器时（例如澄清问题、权衡讨论），推送一个等待屏幕以清除过时内容：

   ```html
   <!-- filename: waiting.html (or waiting-2.html, etc.) -->
   <div style="display:flex;align-items:center;justify-content:center;min-height:60vh">
     <p class="subtitle">Continuing in terminal...</p>
   </div>
   ```

   这可以防止用户在对话已经推进时还盯着一个已解决的选择。当下一个视觉问题出现时，像往常一样推送新的内容文件。

6. 重复直到完成。

## 编写内容片段

只需编写页面内部的内容。服务器会自动将其包裹在框架模板中（页头、主题CSS、连接状态和所有交互基础设施）。

**最小示例：**

```html
<h2>Which layout works better?</h2>
<p class="subtitle">Consider readability and visual hierarchy</p>

<div class="options">
  <div class="option" data-choice="a" onclick="toggleSelect(this)">
    <div class="letter">A</div>
    <div class="content">
      <h3>Single Column</h3>
      <p>Clean, focused reading experience</p>
    </div>
  </div>
  <div class="option" data-choice="b" onclick="toggleSelect(this)">
    <div class="letter">B</div>
    <div class="content">
      <h3>Two Column</h3>
      <p>Sidebar navigation with main content</p>
    </div>
  </div>
</div>
```

就是这样。不需要 `<html>`、CSS或`<script>`标签。服务器会提供所有这些。

## 可用的CSS类（CSS Classes）

框架模板为你的内容提供了以下CSS类：

### 选项（A/B/C选择）

```html
<div class="options">
  <div class="option" data-choice="a" onclick="toggleSelect(this)">
    <div class="letter">A</div>
    <div class="content">
      <h3>Title</h3>
      <p>Description</p>
    </div>
  </div>
</div>
```

**多选（Multi-select）：** 在容器上添加 `data-multiselect` 以允许用户选择多个选项。每次点击切换项目的选中样式。

```html
<div class="options" data-multiselect>
  <!-- same option markup — users can select/deselect multiple -->
</div>
```

### 卡片（视觉设计）

```html
<div class="cards">
  <div class="card" data-choice="design1" onclick="toggleSelect(this)">
    <div class="card-image"><!-- mockup content --></div>
    <div class="card-body">
      <h3>Name</h3>
      <p>Description</p>
    </div>
  </div>
</div>
```

### 原型容器

```html
<div class="mockup">
  <div class="mockup-header">Preview: Dashboard Layout</div>
  <div class="mockup-body"><!-- your mockup HTML --></div>
</div>
```

### 分屏视图（并排）

```html
<div class="split">
  <div class="mockup"><!-- left --></div>
  <div class="mockup"><!-- right --></div>
</div>
```

### 优缺点（Pros/Cons）

```html
<div class="pros-cons">
  <div class="pros"><h4>Pros</h4><ul><li>Benefit</li></ul></div>
  <div class="cons"><h4>Cons</h4><ul><li>Drawback</li></ul></div>
</div>
```

### 原型元素（线框构建块）

```html
<div class="mock-nav">Logo | Home | About | Contact</div>
<div style="display: flex;">
  <div class="mock-sidebar">Navigation</div>
  <div class="mock-content">Main content area</div>
</div>
<button class="mock-button">Action Button</button>
<input class="mock-input" placeholder="Input field">
<div class="placeholder">Placeholder area</div>
```

### 排版和章节

- `h2` —— 页面标题
- `h3` —— 章节标题
- `.subtitle` —— 标题下方的次要文本
- `.section` —— 带底部间距的内容块
- `.label` —— 小号大写标签文本

## 浏览器事件格式

当用户在浏览器中点击选项时，他们的交互被记录到 `$STATE_DIR/events`（每行一个JSON对象）。当你推送新屏幕时，该文件会自动清空。

```jsonl
{"type":"click","choice":"a","text":"Option A - Simple Layout","timestamp":1706000101}
{"type":"click","choice":"c","text":"Option C - Complex Grid","timestamp":1706000108}
{"type":"click","choice":"b","text":"Option B - Hybrid","timestamp":1706000115}
```

完整的事件流显示用户的探索路径——他们可能在确定之前点击多个选项。最后一个 `choice` 事件通常是最终选择，但点击的模式可能揭示出犹豫或值得追问的偏好。

如果 `$STATE_DIR/events` 不存在，说明用户没有与浏览器交互——仅使用他们的终端文本。

## 设计技巧

- **根据问题调整保真度** —— 布局用线框图，打磨问题用精美设计
- **在每个页面上解释问题** —— "哪个布局看起来更专业？"而不是仅仅"选一个"
- **先迭代再推进** —— 如果反馈改变了当前屏幕，写一个新版本
- **每个屏幕最多2-4个选项**
- **在必要时使用真实内容** —— 对于摄影作品集，使用真实图片（Unsplash）。占位内容会掩盖设计问题。
- **保持原型简洁** —— 专注于布局和结构，而不是像素完美的设计

## 文件命名

- 使用语义化的名称：`platform.html`, `visual-style.html`, `layout.html`
- 永远不要重复使用文件名——每个屏幕必须是新文件
- 迭代时：附加版本后缀如 `layout-v2.html`, `layout-v3.html`
- 服务器按修改时间提供最新的文件

## 清理

```bash
scripts/stop-server.sh $SESSION_DIR
```

如果会话使用了 `--project-dir`，原型图文件会保留在 `.superpowers/brainstorm/` 中以供后续参考。只有 `/tmp` 中的会话会在停止时被删除。

## 参考

- 框架模板（CSS参考）：`scripts/frame-template.html`
- 辅助脚本（客户端）：`scripts/helper.js`
