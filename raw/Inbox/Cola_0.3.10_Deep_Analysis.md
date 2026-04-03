# Cola 桌面 AI Agent 深度逆向分析报告

> **版本:** 0.3.10 (build 16) | **Bundle ID:** `ai.marswave.cola` | **开发者:** MarsWave (Hong Kong) Limited
> **平台:** macOS 12.0+ (仅 arm64) | **框架:** Electron 40.7.0 | **分析日期:** 2026-04-03

---

## 目录

1. [产品概览](#1-产品概览)
2. [架构概览](#2-架构概览)
3. [Electron 主进程](#3-electron-主进程)
4. [前端 / 渲染进程](#4-前端--渲染进程)
5. [内置服务端](#5-内置服务端)
6. [LLM 多供应商集成](#6-llm-多供应商集成)
7. [Agent 运行时与 MOD 系统](#7-agent-运行时与-mod-系统)
8. [MCP 协议集成](#8-mcp-协议集成)
9. [浏览器自动化](#9-浏览器自动化)
10. [端侧语音模型](#10-端侧语音模型)
11. [技能系统 (35 个 Skills)](#11-技能系统-35-个-skills)
12. [CLI 命令行工具](#12-cli-命令行工具)
13. [原生模块与框架](#13-原生模块与框架)
14. [依赖分析](#14-依赖分析)
15. [安全与代码签名](#15-安全与代码签名)
16. [云端基础设施](#16-云端基础设施)
17. [文件系统布局](#17-文件系统布局)

---

## 1. 产品概览

Cola 是一款**具备语音和视觉能力的桌面 AI Agent**，由 ColaOS (MarsWave) 出品。它是一个功能完整的桌面 AI 助手，集成了：

- **语音优先交互** -- 端侧语音识别，支持 5 种语言
- **多 LLM 网关** -- 支持 25+ 供应商、150+ 模型
- **Agent 运行时** -- 工具调用、浏览器自动化、MCP 协议支持
- **Office 文档处理** -- Word、Excel、PowerPoint、PDF
- **Google Workspace 集成** -- Gmail、Calendar、Drive、Docs、Sheets
- **Apple 生态集成** -- 日历、备忘录、提醒事项
- **Slack 机器人集成**
- **云端同步** -- 对话历史双向同步
- **插件/MOD 系统** -- 可定制的 Agent 人格
- **CLI 命令行** -- 终端交互方式

底层构建于 **pi**（一个由 Mario Zechner / @mariozechner 开发的编程 Agent 框架），本质上是一个类似 Claude Code 的 Agent SDK。

**关键 URL：**
- 产品: `colaos.ai`
- API: `api.colaos.ai`
- LLM 网关: `zhuoma.colaos.ai`
- CDN: `cdn.colaos.ai`
- URL Scheme: `colaos://`

---

## 2. 架构概览

Cola 采用**分离进程架构**：

```
+------------------+     WebSocket RPC      +------------------+
|  Electron 主进程  | <-------------------> |   Cola Server     |
|  (桥接/外壳)      |    ws://127.0.0.1:     |  (AI 运行时)       |
|                  |        19532           |                  |
+--------+---------+                       +--------+---------+
         |                                          |
    IPC 桥接                                 LLM API 调用
    (contextBridge)                         (25+ 供应商)
         |                                          |
+--------+---------+                       +--------+---------+
|    渲染进程       |                       |  外部 LLM 服务    |
|  (React 19 应用)  |                       | OpenAI/Claude/   |
|                  |                       | Gemini/Mistral.. |
+------------------+                       +------------------+
```

1. **Electron 主进程** -- 窗口管理、IPC 通信、SQLite、与 Cola Server 的桥接
2. **Cola Server** -- 独立的 Node.js 进程 (`cola-server.cjs`)，真正的 AI Agent 运行时
3. **渲染进程** -- React 19 应用，仅通过 `contextBridge` 暴露的 API 通信
4. **内置 Node.js** -- 独立的 Node.js v24.14.0 二进制文件 (114MB, arm64)

主进程**不直接运行** AI Agent 逻辑。它充当渲染进程与 Cola Server 之间的 **WebSocket 桥接器**。

---

## 3. Electron 主进程

### 3.1 窗口管理

**主窗口：**
- 尺寸：1200x720，居中，可调整大小，最小 400x500
- 样式：`titleBarStyle: "hiddenInset"`，红绿灯位于 (12,12)，透明背景，毛玻璃效果 `"under-window"`
- 关闭行为：隐藏而非关闭（Dock 常驻）
- 安全：`sandbox: true`，`contextIsolation: true`，`webSecurity: true`
- 覆盖模式：全屏置顶，覆盖所有工作区

**迷你窗口（托盘弹出窗）：**
- 尺寸：440x220，无边框，不可调整大小，不显示在任务栏
- 样式：透明，毛玻璃效果 `"sidebar"`，在 Mission Control 中隐藏
- 行为：显示在托盘图标下方，失焦即隐藏（除非已固定）
- 置顶层级：`"floating"`

### 3.2 所有 IPC 通道

#### ipcMain.handle（请求/响应）-- 60+ 方法

| 分类 | 通道 |
|------|------|
| **Agent** | `agent:prompt`、`agent:abort`、`agent:testConnection` |
| **设置** | `settings:get`、`settings:save`、`settings:update` |
| **认证** | `auth:refresh`、`auth:setTokens`、`auth:logout`、`auth:get`、`auth:openBrowser`、`auth:getAppVersion`、`auth:startCallbackServer`、`auth:stopCallbackServer`、`auth:fetch` |
| **TTS** | `tts:get-speakers` |
| **更新** | `updater:check`、`updater:install`、`updater:status` |
| **网关** | `gateway:token` |
| **记忆** | `memory:read`、`memory:write`、`memory:openDir` |
| **剪贴板** | `clipboard:read`、`clipboard:write` |
| **主题** | `themes:load`、`themes:save` |
| **MOD** | `mod:list`、`mod:switch`、`mod:current` |
| **队列** | `queue:list` |
| **麦克风** | `mic:getStatus`、`mic:request` |
| **语音** | `speech:init`、`speech:recognize`、`speech:isReady` |
| **文件系统** | `fs:listOutputFiles`、`fs:readFile`、`fs:readFileDataUrl` |
| **文件** | `file:get-download-url` |
| **对话框** | `dialog:pickImages`、`dialog:pickFiles` |
| **Shell** | `shell:openPath`、`shell:showItemInFolder` |
| **聊天** | `chat:history`、`chat:clear`、`chat:delete-messages`、`chat:set-account-key`、`chat:get-account-key` |
| **任务** | `work:list`、`work:get` |
| **同步** | `sync:status`、`sync:run-now`、`sync:retry-outbound`、`sync:migration-status` |
| **通知** | `notify:task-complete` |
| **反馈** | `feedback:packageLogs`、`feedback:pickImages`、`feedback:uploadFile`、`feedback:cleanupFile` |

#### ipcMain.on（单向触发）

| 通道 | 功能 |
|------|------|
| `win:invoke` | 无边框窗口控制（显示/非激活显示/最小化/最大化/关闭） |
| `mini:open-main` | 显示主窗口，隐藏迷你窗 |
| `mini:hide` | 隐藏迷你窗 |
| `mini:drag-active` | 设置迷你窗拖拽状态 |
| `mini:set-pinned` | 固定/取消固定迷你窗 |
| `window:close/minimize/hide` | 窗口管理 |
| `window:traffic-lights` | 显示/隐藏 macOS 红绿灯按钮 |
| `window:set-fullscreen` | 切换全屏 |
| `window:overlay` | 进入/退出覆盖模式 |
| `sound:play-system` | 通过 `afplay` 播放系统音效 |
| `log:info/error/warn` | 渲染进程日志 |

#### 主进程 -> 渲染进程事件

| 通道 | 用途 |
|------|------|
| `agent:text-delta` | 流式文本增量 |
| `agent:blocks-snapshot` | 更新后的 blocks 数组 |
| `agent:message-complete` | Agent 响应完成 |
| `agent:message-error` | Agent 错误 |
| `agent:user-message` | 用户消息回显 |
| `agent:delete-messages` | 消息删除 |
| `agent:response-started` | 新响应开始 |
| `agent:set-theme` / `agent:create-theme` | 主题变更 |
| `agent:compaction` | 上下文压缩事件 |
| `agent:agent-start` / `agent:agent-end` | 子 Agent 生命周期 |
| `agent:child-event` | 子会话事件 |
| `auth:grant-token` / `auth:expired` | 认证事件 |
| `mod:switch-start/success/error/reload` | MOD 切换生命周期 |
| `work:changed` / `work:refreshed` | 任务项更新 |
| `tts:audio-chunk` / `tts:stop` | TTS 流式音频 |
| `updater:status` | 更新状态变更 |
| `sync:messages-upserted/deleted` | 同步事件 |
| `system:input-source-changed` | macOS 输入法切换 |

### 3.3 系统集成

| 功能 | 实现方式 |
|------|----------|
| **托盘** | 模板图标，左键切换主窗/迷你窗，右键菜单（中文界面） |
| **全局快捷键** | `Alt+C` 切换迷你窗口 |
| **深度链接** | `colaos://` 协议，解析 `grant_token` 用于 OAuth |
| **OAuth 回调** | 本地 HTTP 服务器（随机端口），中文 HTML 响应"登录成功" |
| **通知** | 窗口非聚焦时显示系统通知 |
| **电源监控** | 休眠/唤醒触发同步重连 |
| **输入源** | 监控 macOS 输入法切换 |
| **音效** | `afplay` 播放系统音效（路径限制在 `/System/Library/`） |

### 3.4 本地数据库 (SQLite)

**文件：** `~/.cola/chat-history.db`（better-sqlite3，WAL 模式）

**`messages` 表：**
- `id`、`account_key`、`conversation_id`、`client_message_id`
- `role`、`content`、`blocks`（JSON）、`error`
- `prompt_id`、`seq`、`created_at`、`synced_at`、`deleted_at`

**`sync_state` 表：**
- `account_key`、`scope_key`、`conversation_id`、`sync_cursor`、`latest_seq`、`last_sync_at`

账户密钥：`user:{sub}`（来自 JWT）或 `device:local` 回退。

### 3.5 WebSocket 桥接协议

请求格式：
```json
{"type": "request", "id": "req_1_1712345678", "method": "agent.prompt", "params": {...}}
```

响应格式：
```json
{"type": "response", "id": "req_1_1712345678", "ok": true, "result": {...}}
```

事件格式：
```json
{"type": "event", "event": "agent:text-delta", "data": {...}}
```

**转发到服务端的 RPC 方法：** `agent.prompt`、`agent.abort`、`agent.testConnection`、`settings.*`、`memory.*`、`themes.*`、`mod.*`、`auth.*`、`sync.*`、`queue.list`、`file.getDownloadUrl`

请求超时：300 秒。重连退避：1s、2s、4s、8s、15s、30s（指数退避）。

### 3.6 自动更新

- 使用 `electron-updater`
- `autoDownload: true`，`autoInstallOnAppQuit: true`
- 首次检查：启动后 10 秒
- 定期检查：每 4 小时
- 更新地址：`https://cdn.colaos.ai/cola-public-file/release/update`

### 3.7 连通性监控

- 每 15 秒轮询 `https://api.colaos.ai/v1/health`
- 每次请求 5 秒超时
- 恢复连接时：触发同步重连

---

## 4. 前端 / 渲染进程

### 4.1 技术栈

| 层级 | 技术 | 版本 |
|------|------|------|
| UI 框架 | React | 19.2.4 |
| 状态管理 | Zustand | 单一全局 store |
| 动画 | Framer Motion | 12.35.0 |
| CSS 框架 | Tailwind CSS | v4.x |
| Toast 通知 | Sonner | -- |
| 图标 | Lucide React | 0.577.0 |
| Markdown 渲染 | react-markdown + remark-gfm | -- |
| 国际化 | i18next / react-i18next | -- |
| UI 原语 | shadcn/ui 模式（Radix UI + cva） | -- |
| 构建工具 | Vite | -- |
| 包管理器 | pnpm | -- |

### 4.2 应用外壳

- **`index.html`** -- 主窗口，加载 `index-D3lN4nM9.js`（763 KB）+ 共享 chunk（83 KB）+ CSS（62 KB）
- **`mini.html`** -- 迷你窗，透明背景，加载 `mini-C3lMPgx-.js`（36 KB）

### 4.3 状态管理 (Zustand Store)

```
核心状态：
  appState: 'idle' | 'processing'
  settings: AppSettings
  isConfigured, isLoggedIn, userInfo

流式/响应状态：
  isAgentRunning, currentPromptId, streamStartedAt
  responseBlocks: ResponseBlock[], streamingText, error

聊天历史：
  messages: ChatMessage[]（使用类 CRDT 的 upsert 进行同步合并）

欢迎/引导：
  welcomePhase: 'dialogue' | 'login' | 'server-wait' | 'complete' | null
  welcomeDialogue, welcomeLang: 'en' | 'zh'

队列: queuedPrompts, queueDroppedCount
MOD: currentMod（默认 'default'）, availableMods
同步: connectivity, lastSyncAt, pendingSyncCount
更新: updateStatus, updateDismissed, dismissedVersion
```

**默认模型：** `claude-opus-4-6`，通过 Anthropic 供应商

### 4.4 导航（状态机驱动，无路由）

```
authReady=false              --> null（空白）
welcomePhase='login'         --> 登录页
welcomePhase='dialogue'      --> 欢迎对话页
welcomePhase='server-wait'   --> 主页（加载中）
onboardingCompleted          --> 主页
!isLoggedIn && !isConfigured --> 登录页
```

### 4.5 组件层级

```
App
  MotionConfig
    TooltipProvider
      TitleBar（标题栏）
      LoginPage | WelcomeDialoguePage | MainPage
        MainPage
          PlanetVisual（动态星球/头像，随语音波形变化）
          VoiceButton（按住说话/录音）
          ChatPanel（右侧面板，可切换）
            标签页: 聊天 | 文件 | 任务
            MessageList（消息列表）
              UserBubble / AssistantBubble
                ResponseTimeline
                  MarkdownContent
                  FileBlock / ThinkingBlock / ChatLoading
                CopyButton（复制按钮）
            InputBar（输入栏：文本+附件+语音+@提及）
            FilesPanel（输出文件画廊）
            TasksPanel（任务项/清单）
      SettingsPanel（设置面板，Dialog 覆盖层）
      UpdateBanner（更新横幅）
      ApiErrorDialog（API 错误弹窗）
      Toaster（Toast 通知）
```

### 4.6 功能清单

| 功能 | 详情 |
|------|------|
| **语音输入** | 按住说话、实时波形可视化、端侧语音识别 |
| **聊天** | 流式文本、响应块（文本/文件/思考）、Markdown（GFM）、文件附件、图片 @提及、自动滚动 |
| **输入** | 多行输入、拖拽上传、文件选择器、@提及系统、语音录制、Enter 发送 |
| **任务面板** | 任务项状态跟踪、检查清单、制品、过滤器 |
| **文件面板** | 输出文件画廊、图片缩略图（懒加载）、音频预览、在 Finder 中打开 |
| **设置** | 账户、语言（6种）、外观（亮/暗/跟随系统）、云同步、TTS 语音、开发者选项、反馈 |
| **欢迎流程** | 电影式多步对话、双语（中/英）、语音音频、背景音乐、闪光效果 |
| **认证** | Google OAuth、魔法链接、OTP 验证码、激活码、JWT 带刷新令牌轮换 |
| **迷你窗口** | 独立的原生 TS 实现、独立 API 桥接、聊天、语音、固定/拖拽 |

### 4.7 事件轮询架构

通过 `window.agentEvents.poll()` 自适应频率轮询事件：
- 标签页隐藏时：1000ms
- 空闲时：500ms
- 流式输出时：50ms（20fps）
- 刚提交后：requestAnimationFrame（~16ms，持续 2 秒）

### 4.8 主题系统

| 主题 | 空闲色相 | 背景色 | 强调色 |
|------|----------|--------|--------|
| 默认 | 252（紫色） | #151210 | 17（橙色） |
| 海洋 | 195（青色） | #020c14 | 195 |
| 火焰 | 25（橙色） | #130800 | 30 |
| 森林 | 145（绿色） | #050f07 | 140 |
| 樱花 | 340（粉色） | #12050a | 340 |

每个主题包含：`idleHue/Sat/Lit`、`listenHue/Sat/Lit`、`processHue/Sat/Lit`、`bgColor`、`accentHue`、`avatarType`（球体/SVG/图片）。Agent 可动态创建自定义主题。

### 4.9 设计系统

**亮色模式：** `--col-bg: #FFFBF5`（暖奶油色），强调色 `#f1752d`（暖橙色）
**暗色模式：** `--col-bg: #151210`（深暖黑色），强调色 `#df7436`
**字体：** `-apple-system, BlinkMacSystemFont, Segoe UI, sans-serif` / `ui-monospace, SFMono-Regular, Menlo`

### 4.10 音频文件

| 分类 | 文件数 | 用途 |
|------|--------|------|
| 背景音乐 | 1 个（1.9MB） | 引导流程背景音乐 |
| 过渡音乐 | 2 个（中/英） | 过渡音效 |
| 音效 | 2 个 | 唤醒 + 确认音效 |
| 语音旁白 | 28 个（双语） | 引导步骤的语音旁白 |

---

## 5. 内置服务端

**文件：** `Resources/server/cola-server.cjs`（~11MB，3,277 行压缩代码）
**框架：** Express.js
**默认绑定：** `127.0.0.1:19532`

### 5.1 内部 WebSocket RPC 事件

| 事件 | 字符串值 |
|------|----------|
| `AGENT_PROMPT` | `agent.prompt` |
| `AGENT_ABORT` | `agent.abort` |
| `AGENT_TEST_CONNECTION` | `agent.testConnection` |
| `MEMORY_READ/WRITE` | `memory.read/write` |
| `SETTINGS_GET/SAVE/UPDATE` | `settings.get/save/update` |
| `AUTH_GET/SET_TOKENS/LOGOUT/REFRESH` | 认证相关操作 |
| `SERVER_HEALTH/STATUS` | 服务器健康/状态 |
| `SESSION_LIST/SHOW/DELETE/CLEAN` | 会话管理 |
| `MOD_LIST/CURRENT/SWITCH` | MOD 管理 |
| `LOG_LIST/READ` | 日志操作 |
| `CRON_LIST/DELETE` | 定时任务管理 |
| `CHANNEL_LIST/SAVE` | 频道管理 |
| `QUEUE_LIST` | 队列列表 |
| `FILE_GET_DOWNLOAD_URL` | 文件下载 URL |
| `THEMES_LOAD` | 主题加载 |
| `AWARENESS_CRON_TRIGGER` | 意识 cron 触发 |
| `MEMORY_CRON_TRIGGER` | 记忆 cron 触发 |
| `SYNC_*` | 同步操作（状态、立即执行、推拉历史、重试、迁移） |

### 5.2 REST API 接口（兼容 OpenAI/Anthropic）

#### 聊天与补全
| 方法 | 路径 | 说明 |
|------|------|------|
| POST | `/v1/chat/completions` | 兼容 OpenAI 的聊天补全 |
| POST | `/v1/messages` | 兼容 Anthropic 的消息接口 |
| POST | `/v1/complete` | 旧版补全 |
| POST | `/v1/fim/completions` | 中间填充（FIM）代码补全 |
| POST | `/v1/chat/classifications` | 聊天分类 |
| POST | `/v1/chat/moderations` | 聊天审核 |

#### Agent 管理
| 方法 | 路径 | 说明 |
|------|------|------|
| POST | `/v1/agents` | 创建 Agent |
| GET | `/v1/agents` | 列出 Agent |
| GET | `/v1/agents/{id}` | 获取 Agent |
| PUT | `/v1/agents/{id}` | 更新 Agent |
| DELETE | `/v1/agents/{id}` | 删除 Agent |
| GET | `/v1/agents/{id}/versions` | 列出版本 |
| POST | `/v1/agents/completions` | Agent 补全 |

#### 对话管理
| 方法 | 路径 | 说明 |
|------|------|------|
| POST | `/v1/conversations` | 发起对话 |
| GET | `/v1/conversations` | 列出对话 |
| GET | `/v1/conversations/{id}` | 获取对话 |
| POST | `/v1/conversations/{id}/messages` | 追加消息 |
| GET | `/v1/conversations/{id}/history` | 获取历史 |
| POST | `/v1/conversations/{id}/restart` | 重启对话 |

#### 文件与文档库（RAG）
| 方法 | 路径 | 说明 |
|------|------|------|
| POST | `/v1/files` | 上传文件 |
| GET | `/v1/files` | 列出文件 |
| GET | `/v1/files/{id}/content` | 下载文件 |
| POST | `/v1/libraries` | 创建文档库 |
| GET | `/v1/libraries` | 列出文档库 |
| POST | `/v1/libraries/{id}/documents` | 上传文档 |
| GET | `/v1/libraries/{id}/documents` | 列出文档 |

#### 音频、嵌入、模型等
| 方法 | 路径 | 说明 |
|------|------|------|
| POST | `/v1/audio/transcriptions` | 音频转写 |
| POST | `/v1/embeddings` | 向量嵌入 |
| GET | `/v1/models` | 列出模型 |
| POST | `/v1/moderations` | 内容审核 |
| POST | `/v1/ocr` | OCR 识别 |
| POST | `/v1/url/scrape` | URL 抓取 |
| GET | `/v1/me` | 当前用户信息 |
| POST | `/v1/auth/refresh` | 令牌刷新 |
| GET/POST | `/v1/skills` | 技能管理 |
| POST | `/v1/messages/count_tokens` | Token 计数 |

#### 微调与批处理
| 方法 | 路径 | 说明 |
|------|------|------|
| POST | `/v1/fine_tuning/jobs` | 创建微调任务 |
| POST | `/v1/batch/jobs` | 创建批处理任务 |

#### 浏览器控制（独立 Express 子应用）
| 方法 | 路径 | 说明 |
|------|------|------|
| GET | `/` | 浏览器配置文件状态 |
| GET | `/profiles` | 列出配置文件 |
| POST | `/profiles/create` | 创建配置文件 |
| POST | `/start` / `/stop` | 启动/停止浏览器 |
| GET | `/tabs` | 列出标签页 |
| POST | `/tabs/open` / `/tabs/focus` | 标签页管理 |
| POST | `/navigate` | 导航到 URL |
| POST | `/screenshot` | 截屏 |
| GET | `/snapshot` | 页面无障碍快照 |
| POST | `/act` | 浏览器操作（点击、输入、滚动） |
| POST | `/pdf` | 生成 PDF |

### 5.3 WebSocket 速率限制

- 严格模式：10 个请求 / 10-60 秒（敏感操作）
- 通用模式：200 个请求 / 200-60 秒

### 5.4 流式事件

| 事件 | 说明 |
|------|------|
| `agent:agent-start` | Agent 回合开始 |
| `agent:text-delta` | 增量文本 Token |
| `agent:thinking` | 思考/推理块 |
| `agent:tool-start` / `agent:tool-end` | 工具调用生命周期 |
| `agent:complete` | 回合完成 |
| `agent:agent-end` | Agent 会话结束 |
| `agent:turn-settled` | 回合完全结算 |
| `agent:error` | 发生错误 |
| `agent:compaction` | 上下文压缩事件 |
| `agent:file` | 文件操作 |
| `agent:session-spawned` | 子 Agent 会话创建 |
| `agent:set-theme` / `agent:create-theme` | 主题事件 |

### 5.5 自主记忆系统

Cola 具有**定时自省反思机制**：
- **日记 cron：** `0 21 * * *`（每天晚上 9 点）-- 写入记忆日记
- **自省 cron：** `30 21 * * *`（每天晚上 9:30）-- 运行意识反思
- 互斥锁保护，基于日期的去重防止重复执行

### 5.6 Slack 集成

通过 `@slack/bolt` 实现完整 Slack 机器人：
- Socket 模式 + HTTP 接收器
- 事件处理：`message`、`app_mention`
- 操作：`postMessage`、`delete`、`filesUploadV2`
- OAuth 安装流程

---

## 6. LLM 多供应商集成

### 6.1 已注册的供应商（25+）

| 供应商 | API 协议 | 备注 |
|--------|----------|------|
| `anthropic` | anthropic-messages | 主要供应商 |
| `openai` | openai-completions | 标准 |
| `openai`（responses） | openai-responses | 新版 API |
| `openai-codex` | openai-codex-responses | 代码专用 |
| `azure-openai-responses` | azure-openai-responses | Azure 变体 |
| `google` | google-generative-ai | Gemini API |
| `google-vertex` | google-vertex | Vertex AI |
| `google-gemini-cli` | google-gemini-cli | CLI 变体 |
| `google-antigravity` | google 变体 | -- |
| `mistral` | mistral-conversations | Mistral API |
| `amazon-bedrock` | bedrock-converse-stream | AWS Bedrock |
| `xai` | openai-compat | Grok |
| `groq` | openai-compat | 快速推理 |
| `github-copilot` | openai-compat | Copilot |
| `openrouter` | openai-compat | 246 个模型映射 |
| `vercel-ai-gateway` | openai-compat | Vercel |
| `huggingface` | -- | HF 推理 |
| `cerebras` | -- | 快速推理 |
| `minimax` / `minimax-cn` | -- | MiniMax |
| `kimi-coding` | -- | Moonshot |
| `zai` | -- | -- |
| `opencode` / `opencode-go` | -- | -- |

### 6.2 模型目录（150+ 个模型）

**Claude 系列：** claude-1.3 到 claude-opus-4-6（含 `-thinking` 变体）

**GPT 系列：** gpt-4 到 gpt-5.4-pro，gpt-oss-20b/120b

**Gemini 系列：** gemini-1.5-flash/pro 到 gemini-3.1-flash/pro

**Grok 系列：** grok-2 到 grok-4-1-fast

**Mistral 系列：** mistral-large、medium、small、nemo、saba

**其他：** DeepSeek-R1/V3、Llama-3.x 等

### 6.3 供应商配置

- 自定义模型配置文件：`~/.pi/agent/models.json`
- 运行时供应商注册：`baseUrl`、`apiKey`/`oauth`、`models[]`、`streamSimple`
- 模型覆盖：`baseUrl`、`headers`、`compat`、`cost`、`contextWindow`、`maxTokens`

### 6.4 流式协议

每个供应商有 `stream()`（完整结构化事件）和 `streamSimple()`（纯文本）两种：
- **Anthropic：** SSE，事件类型 `message_start`、`content_block_start/delta/stop`、`message_delta/stop`
- **OpenAI：** SSE，`choices[].delta` 格式，`data: [DONE]` 终止
- **Google：** SSE，通过 `/v1internal:streamGenerateContent?alt=sse`
- **Bedrock：** Converse stream API

### 6.5 重试与降级

- OpenAI Codex：遇到 429、500、502、503、504 时重试
- Google：多端点降级，指数退避
- OpenRouter：246 个模型路由作为降级层

### 6.6 环境变量密钥

```
ANTHROPIC_API_KEY, ANTHROPIC_AUTH_TOKEN, ANTHROPIC_OAUTH_TOKEN
OPENAI_API_KEY
AZURE_OPENAI_API_KEY, AZURE_OPENAI_ENDPOINT, AZURE_OPENAI_RESOURCE_NAME
AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY, AWS_SESSION_TOKEN, AWS_BEARER_TOKEN_BEDROCK
GOOGLE_APPLICATION_CREDENTIALS, GOOGLE_CLOUD_API_KEY
GH_TOKEN, GITHUB_TOKEN, COPILOT_GITHUB_TOKEN
```

---

## 7. Agent 运行时与 MOD 系统

### 7.1 MOD 系统

MOD 是存储在 `~/.cola/mods/` 中的命名式 Agent 配置：

```
~/.cola/mods/
  default/
    AGENT.md          -- 系统提示词
    skills/           -- 模组专属技能
    extensions/       -- 模组专属扩展
  custom-mod/
    AGENT.md
    ...
```

- 默认 MOD 自动创建，附带 "Solar System AI Assistant" 提示词
- 文件监控，300ms 防抖，支持热重载
- 全局资源位于 `~/.cola/resources/`，含 `skills/`、`prompts/`、`extensions/`
- MOD 本地技能与全局技能冲突检测

### 7.2 系统提示词组装

由多个组件拼装而成：
1. Agent 内容（来自 AGENT.md）
2. 当前 MOD 上下文 + 可用 MOD 列表
3. 频道上下文
4. 全局记忆
5. Cola 目录上下文
6. 运行时感知
7. 已激活的工具名称
8. 可用 Agent 列表

### 7.3 上下文压缩

- 通过专用分词器进行 Token 计数
- 可配置阈值的上下文溢出检测（默认 100,000 tokens）
- 超限时压缩/摘要历史消息

### 7.4 会话管理

- 会话存储在 `~/.cola/sessions/`
- 按 `{来源}:{名称}` 分作用域（如 `desktop:local`、`cli:benzema`）
- 支持对话回放、历史追踪
- 支持按时间清理旧会话

---

## 8. MCP 协议集成

### 8.1 Chrome DevTools MCP

Cola 使用 Chrome DevTools MCP 服务器：
```
npx -y chrome-devtools-mcp@latest --autoConnect --experimentalStructuredContent --experimental-page-id-routing
```

传输层：StdioClientTransport（stdin/stdout JSON-RPC）

**MCP 工具：**
- `list_pages`、`new_page`、`select_page`、`close_page`
- `navigate_page`、`take_snapshot`、`take_screenshot`
- `click`、`fill`、`fill_form`、`hover`、`drag`、`press_key`
- `resize_page`、`handle_dialog`、`evaluate_script`

### 8.2 连接管理

- 连接池（Map），按配置键 `[profileName, userDataDir]` 索引
- 待连接队列（Map），防止重复连接
- 传输进程 PID 死亡时自动清理
- 优雅关闭：SIGTERM -> 2 秒 -> SIGKILL

### 8.3 工具调用

支持跨多 API 的工具调用：
- **Anthropic：** `tool_use`、`server_tool_use`、`mcp_tool_use` 内容块
- **OpenAI：** `function` 类型工具调用，带 `tool_call_id`
- 工具类型：`DocumentLibraryTool`、`ChunkTool`、自定义扩展工具

---

## 9. 浏览器自动化

### 9.1 双模式架构

**模式一：本地托管 (`local-managed`)：**
- Cola 通过 Playwright 启动自有 Chromium 浏览器
- 直接 CDP（Chrome DevTools 协议）访问
- 浏览器自动检测：Chrome、Chrome Beta、Chrome Canary、Chromium、Brave、Edge
- 支持无头模式（`--headless=new`）
- 启动参数：`--no-first-run`、`--no-default-browser-check`、`--disable-sync`、`--window-size=1440,960`

**模式二：现有会话 (`local-existing-session`)：**
- 通过 Chrome DevTools MCP 附加到运行中的 Chrome
- 功能受限（无 PDF、无选择器点击、无模拟输入）
- 自动连接流程

### 9.2 配置文件管理

- 多配置文件，独立配置
- 每个配置文件含：名称、颜色、CDP 端口/URL、用户数据目录、驱动类型
- 内置 `cola` 和 `user` 配置文件不可删除
- CDP 端口自动分配
- 10 色调色板用于配置文件标识

### 9.3 截屏管线

1. 通过 MCP 或 Playwright 截屏
2. 保存到临时文件（`os.tmpdir()` 中的 UUID）
3. 压缩/缩放：最大边 1440px，可配置最大字节数
4. 保存到 `~/.cola/outputs/browser-{timestamp}.{ext}`
5. 返回元数据

### 9.4 页面快照

- AI 格式：语义化的文本表示
- Aria 格式：带引用 ID 的可交互元素
- 引用到 UID 的映射，用于元素定位
- 可配置限制（默认 40，最大 500 节点）

---

## 10. 端侧语音模型

**模型总重量：~327 MB** -- 全部 ONNX 格式，INT8 量化，完全离线运行。

### 10.1 Silero VAD（语音活动检测）
- **文件：** `silero_vad.onnx`（629 KB）
- **来源：** PyTorch 2.2.2
- **用途：** 检测语音与静音，用于音频分段

### 10.2 SenseVoice（多语言语音识别）
- **目录：** `sherpa-onnx-sense-voice-zh-en-ja-ko-yue-int8-2024-07-17/`
- **模型：** `model.int8.onnx`（228 MB，INT8 量化）
- **词汇表：** 25,055 个 Token（SentencePiece BPE）
- **支持语言：** 中文、英文、日文、韩文、粤语
- **来源：** 阿里巴巴
- **特殊能力：**
  - ASR（语音识别）：`<|ASR|>`
  - AED（音频事件检测）：`<|AED|>` -- 语音、背景音乐、笑声、掌声、哭声、喷嚏、呼吸、咳嗽、歌声
  - SER（语音情感识别）：`<|SER|>` -- 开心、悲伤、愤怒、中性、恐惧、厌恶、惊讶

### 10.3 Whisper Tiny 英文版
- **目录：** `sherpa-onnx-whisper-tiny.en/`
- **编码器：** `tiny.en-encoder.int8.onnx`（12 MB）
- **解码器：** `tiny.en-decoder.int8.onnx`（86 MB）
- **词汇表：** 50,256 个 Token（OpenAI BPE）
- **用途：** 轻量级英文专用备选方案

### 10.4 推理管线

```
音频输入 -> Silero VAD（分割语音段） -> SenseVoice（多语言）或 Whisper Tiny（英文备选）
```

运行时：**sherpa-onnx**（k2-fsa），C++/ONNX，通过 `sherpa-onnx-node` 原生插件调用。
硬件加速：通过 ONNX Runtime 1.23.2 支持 Apple CoreML + Metal GPU。

---

## 11. 技能系统 (35 个 Skills)

### 11.1 技能架构

每个技能是一个目录，包含 `SKILL.md`（YAML frontmatter + Markdown 主体）：

```yaml
---
name: my-skill-name           # kebab-case，最多 64 字符
description: >                # 必填 -- 触发机制
  功能描述。当 [场景] 时使用。
version: 1.0.0
tags: [tag1, tag2]
metadata:
  openclaw:
    emoji: "..."
    os: ["darwin"]
    requires:
      bins: ["tool"]           # 必需的 CLI 工具
      env: ["API_KEY"]         # 必需的环境变量
    install: [...]
---
```

**加载优先级：** MOD 技能 > 全局技能 > 内置技能

### 11.2 元/系统技能 (2 个)

| 技能 | 说明 |
|------|------|
| **cola-skill-creator** | 创建新 Cola 技能的指南。6 步流程：意图 > 规划 > Frontmatter > 主体 > 资源 > 验证。附带 `skill-spec.md` 和 `writing-guide.md` 参考文档。 |
| **self-improving-agent** | 持续改进框架。记录到 `.learnings/`（LEARNINGS.md、ERRORS.md、FEATURE_REQUESTS.md）。晋升管线：学习 -> 项目记忆 -> 工作区指南。重复模式检测，出现次数 >= 3 时自动晋升。成熟的学习可提取为新技能。 |

### 11.3 Office 文档技能 (4 个)

| 技能 | 说明 | 依赖 |
|------|------|------|
| **docx** | 创建/读取/编辑 Word 文档。三种工作流：读取（pandoc）、创建（docx-js）、编辑（解包 XML > 编辑 > 打包）。支持修订追踪+批注。14 个脚本 + ECMA-376 XSD 架构。 | pandoc、LibreOffice、Poppler |
| **xlsx** | 创建/读取/编辑 Excel 电子表格。工具：pandas（数据）、openpyxl（公式）。强制通过 LibreOffice 重新计算公式。财务模型颜色编码标准。 | pandas、openpyxl、LibreOffice |
| **pptx** | 创建/读取/编辑 PowerPoint。三种工作流：读取（markitdown）、编辑（XML）、创建（PptxGenJS）。10 种设计主题，排版配对。强制两步 QA。 | markitdown、Pillow、pptxgenjs、LibreOffice |
| **pdf** | 完整 PDF 处理：读取、合并、拆分、旋转、水印、创建、填表、加密/解密、OCR。可填写/不可填写表单检测管线。8 个脚本。 | pypdf、pdfplumber、reportlab、pytesseract、poppler-utils |

### 11.4 Google Workspace 技能 (18 个)

所有技能需要 `gws` CLI 工具。通过 `gws-shared` 共享认证。

| 技能 | 服务 | 能力 |
|------|------|------|
| **gws-shared** | -- | 认证模式、全局参数、安全规则 |
| **gws-calendar** | 日历 | 完整 ACL、日历列表、事件、忙闲、设置 |
| **gws-calendar-agenda** | 日历 | 显示即将到来的事件（只读） |
| **gws-calendar-insert** | 日历 | 创建带参会者的事件 |
| **gws-docs** | 文档 | 通过 batchUpdate 读写 |
| **gws-docs-write** | 文档 | 向文档追加文本 |
| **gws-drive** | 云盘 | 文件、文件夹、共享盘、权限 |
| **gws-drive-upload** | 云盘 | 上传并自动检测元数据 |
| **gws-events** | 事件 | Workspace 事件订阅（Pub/Sub） |
| **gws-events-subscribe** | 事件 | 订阅并以 NDJSON 流式输出 |
| **gws-events-renew** | 事件 | 续期过期订阅 |
| **gws-gmail** | 邮件 | 完整发送、阅读、草稿、标签、线程 |
| **gws-gmail-send** | 邮件 | 发送邮件（收件人、主题、正文、抄送、密送） |
| **gws-gmail-triage** | 邮件 | 未读收件箱摘要（只读） |
| **gws-gmail-reply** | 邮件 | 自动线程化的回复 |
| **gws-gmail-reply-all** | 邮件 | 自动线程化的全部回复 |
| **gws-gmail-forward** | 邮件 | 转发给新收件人 |
| **gws-gmail-watch** | 邮件 | 监听新邮件并以 NDJSON 流式输出 |
| **gws-sheets** | 表格 | 完整电子表格 CRUD、开发者元数据 |
| **gws-sheets-read** | 表格 | 读取范围内的值 |
| **gws-sheets-append** | 表格 | 追加行 |

### 11.5 Apple 生态技能 (3 个)

| 技能 | 实现方式 | 依赖 |
|------|----------|------|
| **apple-calendar** | AppleScript（7 个 shell 脚本） | 仅 macOS |
| **apple-notes** | `memo` CLI | `brew install antoniorodr/memo/memo` |
| **apple-reminders** | `remindctl` CLI | `brew install steipete/tap/remindctl` |

### 11.6 生产力/集成技能 (5 个)

| 技能 | 说明 | 依赖 |
|------|------|------|
| **gh-cli** | 全面的 GitHub CLI 参考（v2.85.0）。认证、仓库、Issue、PR、Actions、项目、发布、Gist、Codespace、组织。400+ 行。 | `gh` CLI |
| **notion** | Notion API（2025-09-03）。页面、数据库（"数据源"）、块。 | `NOTION_API_KEY` |
| **obsidian** | Obsidian 知识库管理。搜索、创建、移动（带 wikilink 重构）、删除。 | `obsidian-cli` |
| **coli** | 语音识别（ASR）。`coli asr <音频文件>`，支持 sensevoice/whisper 引擎。 | `@marswave/coli` |
| **spotify-player** | 终端 Spotify 播放/搜索，通过 `spogo` 或 `spotify_player`。 | Spotify Premium |

---

## 12. CLI 命令行工具

**文件：** `Resources/cli/cola-cli.cjs`（399 KB，单文件 CJS 打包）
**版本：** CLI 0.1.1，应用 0.3.10
**运行时：** Node.js v24.14.0（内置），最低 v20
**框架：** Commander.js v14.0.3

### 12.1 命令列表

#### `cola message <message>`
向 Cola Agent 发送消息。

| 选项 | 说明 | 默认值 |
|------|------|--------|
| `--session <name>` | 会话作用域 | `cli:$USER` |
| `-j, --json` | JSON 格式输出 | false |
| `-v, --verbose` | 显示工具执行详情 | false |
| `--debug` | 显示 WebSocket 帧 | false |
| `--server <url>` | 服务器 URL | `localhost:19532` |
| `--attachments <json>` | JSON 格式的文件路径数组 | -- |

#### `cola status`
| 选项 | 默认值 |
|------|--------|
| `-j, --json` | false |
| `--server <url>` | `localhost:19532` |

#### `cola config list|get|set`
| 子命令 | 选项 |
|--------|------|
| `list` | `--json`、`--show-secrets` |
| `get <key>` | `--json`、`--show-secrets` |
| `set <key> <value>` | 自动解析布尔值、数字、JSON |

#### `cola server start|stop`
| 子命令 | 选项 |
|--------|------|
| `start` | `--host`、`--port`（从应用内运行时提示由桌面端管理） |
| `stop` | 读取 PID，SIGTERM -> 5 秒 -> SIGKILL |

#### `cola session list|show|delete|clean`
| 子命令 | 选项 |
|--------|------|
| `list` | `--json` |
| `show <scope>` | `-n <count>`（默认 20）、`--json`、`--verbose` |
| `delete <scope>` | -- |
| `clean` | `--older-than <duration>`（默认 7d） |

#### `cola logs`
| 选项 | 默认值 |
|------|--------|
| `-n, --lines <count>` | 50 |
| `-f, --follow` | false |

### 12.2 WebSocket 协议

**连接：** `ws://{host}:{port}?token={gateway_token}`（5 秒超时）

**请求：**
```json
{"type": "request", "id": "1", "method": "agent.prompt", "params": {...}}
```

**响应：**
```json
{"type": "response", "id": "1", "ok": true, "result": {...}}
```

**事件：**
```json
{"type": "event", "event": "agent:text-delta", "data": {"delta": "...", "promptId": "..."}}
```

### 12.3 `cola message` 端到端流程

1. 解析选项，确定会话密钥（默认 `cli:$USER`）
2. 从 `~/.cola/gateway-token` 读取网关令牌
3. 打开 WebSocket（5 秒超时）
4. 发送 `agent.prompt`，携带 `{message, scopeKey, attachments?}`
5. 接收 `{promptId, model, provider}`
6. 流式事件：`text-delta` -> stdout，`tool-start/end` -> stderr（verbose 模式）
7. 跟踪子 Agent 会话（`session-spawned`、`turn-settled`）
8. 父级 `complete` + 所有子级结算后结束
9. 超时：`COLA_CLI_TIMEOUT_MS`（默认 10 分钟），退出码 2
10. JSON 模式：结构化输出，包含 steps[]、sessions[]、response

### 12.4 环境变量

| 变量 | 用途 | 默认值 |
|------|------|--------|
| `COLA_DATA_DIR` | 数据目录 | `~/.cola` |
| `COLA_HOST` | 服务器地址 | `127.0.0.1` |
| `COLA_PORT` | 服务器端口 | `19532` |
| `COLA_CLI_TIMEOUT_MS` | 消息超时时间 | `600000` |

---

## 13. 原生模块与框架

### 13.1 Electron 框架

| 框架 | 版本 | 用途 |
|------|------|------|
| Electron Framework | 40.7.0 | 核心运行时（Chromium，arm64） |
| Squirrel.framework | 1.0 | 自动更新（GitHub Squirrel） |
| Mantle.framework | 0.0.0 | Squirrel 的 ObjC 模型层 |
| ReactiveObjC.framework | 3.1.0 | Squirrel 的响应式编程 |

**辅助应用：** Cola Helper、Cola Helper (GPU)、Cola Helper (Renderer)、Cola Helper (Plugin) -- 全部 arm64，版本 0.3.10.16。

### 13.2 原生模块 (app.asar.unpacked)

| 模块 | 版本 | 大小 | 用途 |
|------|------|------|------|
| sherpa-onnx-darwin-arm64 | 1.12.29 | 72 MB | 语音 ONNX 运行时（CoreML + Metal） |
| koffi | 2.15.1 | 83 MB | 通用 C FFI（21 个平台） |
| @mariozechner/clipboard-darwin-arm64 | 0.3.2 | 2 MB | 原生剪贴板访问 |
| @mariozechner/clipboard-darwin-universal | 0.3.2 | 4.2 MB | 通用剪贴板二进制 |
| better-sqlite3 | 12.6.2 | -- | SQLite 原生插件 |
| @img/sharp-darwin-arm64 | 0.34.5 | 274 KB | Sharp 原生插件 |
| @img/sharp-libvips-darwin-arm64 | 1.2.4 | 15 MB | libvips 图像处理 |
| jszip | 3.10.1 | -- | ZIP 处理 |

### 13.3 sherpa-onnx 原生栈

| 二进制文件 | 大小 | 类型 |
|-----------|------|------|
| `sherpa-onnx.node` | 570 KB | Node.js N-API 插件 |
| `libsherpa-onnx-c-api.dylib` | 4.6 MB | C API 层 |
| `libsherpa-onnx-cxx-api.dylib` | 141 KB | C++ API 层 |
| `libonnxruntime.1.23.2.dylib` | 33 MB | ONNX 运行时（CoreML + Metal） |

---

## 14. 依赖分析

### 14.1 概览

- **node_modules 总大小：** 479 MB
- **包总数：** 755
- **构建工具：** pnpm

### 14.2 Cola 自有包

#### @mariozechner 作用域（pi 框架，v0.62.0）

| 包 | 说明 |
|---|------|
| **pi-agent-core** | 通用 Agent，带传输抽象、状态管理、附件支持 |
| **pi-ai** | 统一 LLM API，支持 7 个供应商（Anthropic、OpenAI x3、Google x2、Mistral、Azure、Bedrock）+ OAuth |
| **pi-coding-agent** | 编程 Agent SDK，带 read/bash/edit/write 工具、会话管理、扩展、技能。24 份文档。 |
| **pi-tui** | 终端 UI，差分渲染 |
| **clipboard** + **clipboard-darwin-*** | 原生剪贴板访问 |
| **jiti** | jiti 的 fork，支持 virtualModules |

**关键发现：** `pi-*` 系列包是一个完整的**类 Claude Code 编程 Agent 框架**。`pi-coding-agent` 有完整 SDK：`createAgentSession()`、事件订阅、程序化提示。

#### @marswave 作用域

| 包 | 版本 | 说明 |
|---|------|------|
| **coli** | 0.0.13 | CLI 工具，含 ASR + TTS，通过 sherpa-onnx 和 mac-say |

### 14.3 LLM 供应商 SDK

| 包 | 版本 |
|---|------|
| @anthropic-ai/sdk | 0.73.0 |
| openai | 6.26.0 |
| @mistralai/mistralai | 1.14.1 |
| @google/genai | 通过 pi-ai |
| @aws-sdk/client-bedrock-runtime | 通过 pi-ai（27 + 44 个子包） |

### 14.4 主要依赖分类

| 分类 | 包数量 | 备注 |
|------|--------|------|
| **OpenTelemetry** | 58 个包 | 追踪、指标、日志 + gRPC、HTTP、Prometheus、Zipkin、Jaeger 导出器 |
| **Sentry** | 10 个包 | @sentry/electron 7.10.0 |
| **Radix UI** | 31 个包 | 无头 UI 原语 |
| **AWS SDK** | 71 个包 | Bedrock 运行时 |
| **Slack** | 6 个包 | @slack/bolt 4.6.0 |
| **语音** | sherpa-onnx + koffi | 155 MB（占 node_modules 的 32%） |
| **3D** | three 0.183.2 | 15 MB -- 视觉效果/UI |
| **校验** | zod 4.3.6 | 最新 Zod 4 |

### 14.5 命名谱系

内部代号：**pi**（`pi-agent-core`、`pi-coding-agent`、`pi-ai`、`pi-tui`）
产品品牌：**Cola**（`@marswave/coli`、`ai.marswave.cola`）
平台：**ColaOS**（`colaos.ai`）
技能系统：**OpenClaw**（元数据格式引用）

---

## 15. 安全与代码签名

### 15.1 签名身份

- **签名者：** Developer ID Application: MarsWave (Hong Kong) Limited (CJSWWQ2B9V)
- **签名时间：** 2026 年 3 月 26 日
- **公证：** 已通过 Apple 公证（Gatekeeper 批准）
- **强化运行时：** v26.1.0
- **ASAR 完整性：** SHA256 哈希在 Info.plist 中验证

### 15.2 配置文件

- **名称：** `cola_pro`
- **类型：** DIRECT（Developer ID 分发）
- **创建时间：** 2026-03-04
- **过期时间：** 2044-02-28（18 年有效期）
- **APS 环境：** production（推送通知已启用）

### 15.3 授权声明（极其广泛）

**硬件：**
- `device.audio-input`（麦克风）
- `device.camera`（摄像头）
- `device.bluetooth`（蓝牙）
- `device.serial`（串口设备）
- `device.usb`（USB 设备）

**文件系统：**
- `files.all`（完全磁盘访问）
- `files.downloads.read-write`
- `files.user-selected.read-write`
- `files.bookmarks.app-scope` / `document-scope`

**个人信息：**
- `personal-information.addressbook`（通讯录）
- `personal-information.calendars`（日历）
- `personal-information.location`（位置）
- `personal-information.photos-library`（照片）

**网络：**
- `network.client`（出站）
- `network.server`（入站）

**代码执行（所有保护均已禁用）：**
- `cs.allow-jit`（JIT 编译）
- `cs.allow-unsigned-executable-memory`（允许未签名可执行内存）
- `cs.disable-executable-page-protection`（禁用 W^X 保护）
- `cs.disable-library-validation`（加载未签名 dylib）
- `cs.allow-dyld-environment-variables`（允许 DYLD 注入）

**自动化：**
- `automation.apple-events`（Apple Events）
- `temporary-exception.apple-events` 针对系统事件和 Finder

### 15.4 CSP（内容安全策略）

```
default-src 'self';
script-src 'self' 'unsafe-inline';
style-src 'self' 'unsafe-inline';
img-src 'self' data: blob:;
media-src 'self' data: blob:;
connect-src 'self' ws://localhost:* http://localhost:*;
```

### 15.5 Electron 安全设置

- `contextIsolation: true`（两个窗口均启用）
- `sandbox: true`（两个窗口均启用）
- `webSecurity: true`（主窗口）
- `nodeIntegration: false`（默认）
- 外部 URL 通过 `shell.openExternal()` 打开
- 禁止读取的文件扩展名：`.zip`、`.rar`、`.exe`、`.dmg`、`.app`、`.msi`、`.sh`、`.bash`、`.command`、`.ps1`、`.deb`、`.rpm`
- 文件读取限制：20MB

### 15.6 认证安全

- 认证令牌存储在 `~/.pi/agent/auth.json`，权限 `chmod 0600`
- 通过 `proper-lockfile` 实现文件锁（10 次重试，20ms 退避）
- 网关令牌存储在 `~/.cola/gateway-token`
- 跨供应商 OAuth 令牌刷新带文件锁

---

## 16. 云端基础设施

### 16.1 端点

| 服务 | URL |
|------|-----|
| 生产环境 API | `https://api.colaos.ai` |
| 预发布环境 API | `https://api.staging.colaos.ai` |
| LLM 网关 | `https://zhuoma.colaos.ai` |
| 预发布网关 | `https://zhuoma.staging.colaos.ai` |
| CDN / 更新 | `https://cdn.colaos.ai` |
| TTS API | `https://api.marswave.ai/openapi/v1` |
| 健康检查 | `https://api.colaos.ai/v1/health` |

### 16.2 硬编码值

**客户端 ID：**
```
PJBkELS1o_q9nJ~NzF2_Fmr21TNX&~eoJR49FFdFhD3U
```

**Sentry DSN：**
```
https://aeaadab83096a22f0fa39a0719b7cf7c@o4508880592502784.ingest.us.sentry.io/4511103547277312
```
- 组织 ID：`o4508880592502784`
- 项目 ID：`4511103547277312`
- Release 格式：`cola@{version}`

### 16.3 云端同步

- 基于变更日志的双向同步
- 拉取：基于游标，每批 200 条
- 推送：每批 50 条
- 定期同步：每 5 分钟
- 类 CRDT 合并：通过 `client_message_id` + `seq`

### 16.4 TTS 默认语音

| 语言 | 说话人 |
|------|--------|
| 中文 | `wanjin-971d09f0` |
| 英文 | `minight-kate-d4b925d0` |

---

## 17. 文件系统布局

### 17.1 应用包结构

```
Cola.app/Contents/
  Info.plist                    -- 应用元数据
  PkgInfo                      -- APPL
  CodeResources                 -- 代码签名资源
  embedded.provisionprofile     -- 配置文件
  _CodeSignature/               -- 代码签名
  MacOS/
    Cola                        -- 主二进制（56KB，Electron 外壳）
  Frameworks/
    Electron Framework.framework/  -- Electron 40.7.0
    Squirrel.framework/            -- 自动更新
    Mantle.framework/              -- ObjC 模型层
    ReactiveObjC.framework/        -- 响应式编程
    Cola Helper.app/               -- 通用辅助进程
    Cola Helper (GPU).app/         -- GPU 进程
    Cola Helper (Renderer).app/    -- 渲染进程
    Cola Helper (Plugin).app/      -- 插件进程
  Resources/
    app.asar                    -- Electron 应用包（解包后 489 MB）
    app.asar.unpacked/          -- 原生模块
    app-update.yml              -- 更新配置
    icon.icns                   -- 应用图标
    tray-iconTemplate@2x.png    -- 托盘图标
    cli/
      cola-cli.cjs              -- CLI 工具（399 KB）
    runtime/
      node                      -- Node.js v24.14.0（114 MB）
    server/
      cola-server.cjs           -- 服务端（11 MB）
      node_modules/             -- 服务端原生依赖（26 MB）
    models/
      silero_vad.onnx           -- VAD 模型（629 KB）
      sherpa-onnx-sense-voice-*/-- SenseVoice ASR（228 MB）
      sherpa-onnx-whisper-*/    -- Whisper ASR（98 MB）
    skills/                     -- 35 个技能目录
    *.lproj/                    -- 本地化（30+ 种语言）
```

### 17.2 用户数据结构 (`~/.cola/`)

```
~/.cola/
  settings.json                 -- 用户设置
  gateway-token                 -- WebSocket 认证令牌
  cola-server.pid               -- 服务器 PID
  chat-history.db               -- SQLite 聊天数据库
  sessions/                     -- 对话会话
  outputs/                      -- 生成的文件（截图、PDF）
  work-items/                   -- 任务队列
  memory/                       -- 持久化 Agent 记忆
  awareness/                    -- 自省反思数据
  logs/                         -- 服务器日志（每日轮换，保留 7 天）
  mods/
    default/
      AGENT.md                  -- 系统提示词
      skills/                   -- MOD 专属技能
      extensions/               -- MOD 专属扩展
  resources/
    skills/                     -- 全局技能
    prompts/                    -- 全局提示词
    extensions/                 -- 全局扩展
  browser/{profile}/user-data/  -- 浏览器配置数据
  attachments/                  -- 文件附件
  themes.json                   -- 自定义主题
  channels.json                 -- 频道配置
  crons.json                    -- 定时任务
  cron-stats.json               -- 定时任务统计
  runtime/node                  -- 部署的 Node.js 二进制

~/.pi/agent/
  auth.json                     -- 供应商 API 密钥（chmod 0600）
  models.json                   -- 自定义模型配置
```

---

## 附录 A：所有环境变量

### Cola 专属
| 变量 | 用途 | 默认值 |
|------|------|--------|
| `COLA_HOST` | 服务器绑定地址 | `127.0.0.1` |
| `COLA_PORT` | 服务器绑定端口 | `19532` |
| `COLA_DATA_DIR` | 数据目录覆盖 | `~/.cola` |
| `COLA_BROWSER_EXECUTABLE` | 自定义浏览器路径 | -- |
| `COLA_EVAL_GEMINI_SEARCH` | Gemini 搜索评估 | -- |
| `COLA_CLI_TIMEOUT_MS` | CLI 消息超时 | `600000` |

### PI 框架
| 变量 | 用途 |
|------|------|
| `PI_PACKAGE_DIR` | 包目录 |
| `PI_CACHE_RETENTION` | 缓存策略（`long`/`short`） |
| `PI_OFFLINE` | 离线模式 |
| `PI_BASE_URL` | 基础 URL 覆盖 |
| `PI_KEY` / `PI_KEY_AUTH` / `PI_TOKEN` | 认证密钥 |

### 遥测
| 变量 | 用途 |
|------|------|
| `SENTRY_DSN` | Sentry DSN |
| `SENTRY_ENVIRONMENT` | 环境标识 |
| `SENTRY_DEBUG` | 调试模式 |
| `SENTRY_TRACES_SAMPLE_RATE` | 追踪采样率 |
| `OTEL_EXPORTER_OTLP_ENDPOINT` | OpenTelemetry 端点 |
| `OTEL_EXPORTER_OTLP_HEADERS` | OTLP 头部 |
| `OTEL_EXPORTER_PROMETHEUS_HOST/PORT` | Prometheus |

---

## 附录 B：错误处理

### 自定义错误类
- `MT` -- 浏览器错误（配置文件未找到、CDP 端口被占用）
- `Cc` -- 能力错误（不支持的功能）
- `tw` -- 标签页未找到
- `OV` -- 对象未找到
- `jr` -- API/SDK 错误（Anthropic）
- `IX` -- 内容过滤错误

### 错误处理流程
1. 所有路由处理器包裹 try/catch
2. 错误映射器转换为 HTTP 状态码
3. 结构化 `{ error: "message" }` JSON 响应
4. 通过 `captureException` / `captureMessage` 上报 Sentry
5. 三级日志：info/warn/error（前缀 `[cola-server]`）
6. 追踪日志：`~/.cola/logs/trace-*.jsonl`，带轮换
7. stdout/stderr EPIPE 静默处理

---

## 附录 C：关键技术观察

1. **语音优先的设计哲学：** 主页面中央是一个动态星球和语音按钮；文本聊天只是次要功能。这本质上是一个语音优先的 AI Agent。

2. **基于 pi 框架构建：** Cola 构建于 `@mariozechner/pi-*`，一个完整的类 Claude Code 编程 Agent SDK。"pi" 的血统贯穿整个代码库（pi-agent-core、pi-coding-agent、pi-ai、pi-tui）。作者：Mario Zechner (badlogic)。

3. **通用 LLM 网关：** 25+ 供应商、150+ 模型、统一流式输出。充当本地 API 网关，标准化所有主流 LLM 供应商的接口。

4. **自主记忆：** 每日定时自省 cron（晚 9 点写日记，晚 9:30 运行意识反思），使 Cola 成为一个能持续学习的自主 Agent。

5. **离线能力：** 327 MB 的语音模型实现完全离线的语音交互，无需网络即可完成语音转写。

6. **极其广泛的权限：** 完全磁盘访问、所有个人数据、所有代码执行保护均已禁用。这是 macOS 应用所能请求的最大权限集。

7. **OpenClaw 血统：** 技能系统的元数据格式引用了 "OpenClaw"，表明 Cola 继承自或关联于 OpenClaw 项目。

8. **硬件加速推理：** ONNX Runtime 链接到 CoreML + Metal，意味着语音模型在 Apple Neural Engine 和 GPU 上运行，而非仅使用 CPU。

9. **多 Agent 编排：** CLI 和服务端支持生成子 Agent，并带有生命周期跟踪（`session-spawned`、`turn-settled`）。

10. **自适应事件轮询：** 渲染进程不使用 WebSocket 推送，而是以智能频率轮询事件（流式输出时 50ms，隐藏时 1000ms）。这是 Electron 架构下的务实选择。

---

*本报告通过深度分析 Cola 0.3.10 (build 16) macOS arm64 版本生成。*
*解包自：`/Users/benzema/Downloads/Cola_0.3.10_16_arm64.dmg`*
