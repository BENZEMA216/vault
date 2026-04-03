# Cola Desktop AI Agent -- Deep Analysis Report

> **Version:** 0.3.10 (build 16) | **Bundle ID:** `ai.marswave.cola` | **Developer:** MarsWave (Hong Kong) Limited
> **Platform:** macOS 12.0+ (arm64-only) | **Framework:** Electron 40.7.0 | **Analysis Date:** 2026-04-03

---

## Table of Contents

1. [Product Overview](#1-product-overview)
2. [Architecture Overview](#2-architecture-overview)
3. [Electron Main Process](#3-electron-main-process)
4. [Frontend / Renderer](#4-frontend--renderer)
5. [Built-in Server](#5-built-in-server)
6. [LLM Provider Integration](#6-llm-provider-integration)
7. [Agent Runtime & MOD System](#7-agent-runtime--mod-system)
8. [MCP Integration](#8-mcp-integration)
9. [Browser Automation](#9-browser-automation)
10. [On-Device Speech Models](#10-on-device-speech-models)
11. [Skills System (35 Skills)](#11-skills-system-35-skills)
12. [CLI Tool](#12-cli-tool)
13. [Native Modules & Frameworks](#13-native-modules--frameworks)
14. [Dependency Analysis](#14-dependency-analysis)
15. [Security & Code Signing](#15-security--code-signing)
16. [Cloud Infrastructure](#16-cloud-infrastructure)
17. [File System Layout](#17-file-system-layout)

---

## 1. Product Overview

Cola is a **Desktop AI Agent with voice & vision**, branded as "Cola" by ColaOS (MarsWave). It is a full-featured desktop AI assistant that combines:

- **Voice-first interaction** with on-device speech recognition (5 languages)
- **Multi-LLM gateway** supporting 25+ providers and 150+ models
- **Agent runtime** with tool use, browser automation, and MCP protocol support
- **Office document processing** (Word, Excel, PowerPoint, PDF)
- **Google Workspace integration** (Gmail, Calendar, Drive, Docs, Sheets)
- **Apple ecosystem integration** (Calendar, Notes, Reminders)
- **Slack bot integration**
- **Cloud sync** with conversation history
- **Plugin/MOD system** for customizable agent personalities
- **CLI companion** for terminal-based interaction

The app is built on top of **pi** (a coding agent framework by Mario Zechner / @mariozechner), which is essentially a Claude Code-like agent SDK.

**Key URLs:**
- Product: `colaos.ai`
- API: `api.colaos.ai`
- LLM Gateway: `zhuoma.colaos.ai`
- CDN: `cdn.colaos.ai`
- URL Scheme: `colaos://`

---

## 2. Architecture Overview

Cola uses a **split-process architecture**:

```
+------------------+     WebSocket RPC      +------------------+
|  Electron Main   | <-------------------> |   Cola Server     |
|  (Bridge/Shell)  |    ws://127.0.0.1:     |  (AI Runtime)     |
|                  |        19532           |                  |
+--------+---------+                       +--------+---------+
         |                                          |
    IPC Bridge                              LLM API Calls
    (contextBridge)                         (25+ providers)
         |                                          |
+--------+---------+                       +--------+---------+
|    Renderer      |                       |  External LLMs   |
|  (React 19 App)  |                       | OpenAI/Claude/   |
|                  |                       | Gemini/Mistral.. |
+------------------+                       +------------------+
```

1. **Electron Main Process** -- Window management, IPC, SQLite, bridge to Cola Server
2. **Cola Server** -- Separate Node.js process (`cola-server.cjs`), the actual AI agent runtime
3. **Renderer** -- React 19 app communicating only via `contextBridge`-exposed APIs
4. **Bundled Node.js** -- Standalone Node.js v24.14.0 binary (114MB, arm64)

The main process does NOT run AI agent logic directly. It acts as a **WebSocket bridge** between the renderer and the Cola Server.

---

## 3. Electron Main Process

### 3.1 Window Management

**Main Window:**
- Size: 1200x720, center, resizable, min 400x500
- Style: `titleBarStyle: "hiddenInset"`, traffic lights at (12,12), transparent, vibrancy `"under-window"`
- Close behavior: Hides instead of closing (dock-persistent)
- Security: `sandbox: true`, `contextIsolation: true`, `webSecurity: true`
- Overlay mode: Full-screen always-on-top across all workspaces

**Mini Window (Tray popup):**
- Size: 440x220, frameless, not resizable, skip taskbar
- Style: Transparent, vibrancy `"sidebar"`, hidden in Mission Control
- Behavior: Shows below tray icon, hides on blur (unless pinned)
- Always-on-top `"floating"` level

### 3.2 All IPC Channels

#### ipcMain.handle (request/response) -- 60+ methods

| Category | Channels |
|----------|----------|
| **Agent** | `agent:prompt`, `agent:abort`, `agent:testConnection` |
| **Settings** | `settings:get`, `settings:save`, `settings:update` |
| **Auth** | `auth:refresh`, `auth:setTokens`, `auth:logout`, `auth:get`, `auth:openBrowser`, `auth:getAppVersion`, `auth:startCallbackServer`, `auth:stopCallbackServer`, `auth:fetch` |
| **TTS** | `tts:get-speakers` |
| **Updater** | `updater:check`, `updater:install`, `updater:status` |
| **Gateway** | `gateway:token` |
| **Memory** | `memory:read`, `memory:write`, `memory:openDir` |
| **Clipboard** | `clipboard:read`, `clipboard:write` |
| **Themes** | `themes:load`, `themes:save` |
| **MODs** | `mod:list`, `mod:switch`, `mod:current` |
| **Queue** | `queue:list` |
| **Microphone** | `mic:getStatus`, `mic:request` |
| **Speech** | `speech:init`, `speech:recognize`, `speech:isReady` |
| **File System** | `fs:listOutputFiles`, `fs:readFile`, `fs:readFileDataUrl` |
| **File** | `file:get-download-url` |
| **Dialog** | `dialog:pickImages`, `dialog:pickFiles` |
| **Shell** | `shell:openPath`, `shell:showItemInFolder` |
| **Chat** | `chat:history`, `chat:clear`, `chat:delete-messages`, `chat:set-account-key`, `chat:get-account-key` |
| **Work** | `work:list`, `work:get` |
| **Sync** | `sync:status`, `sync:run-now`, `sync:retry-outbound`, `sync:migration-status` |
| **Notify** | `notify:task-complete` |
| **Feedback** | `feedback:packageLogs`, `feedback:pickImages`, `feedback:uploadFile`, `feedback:cleanupFile` |

#### ipcMain.on (fire-and-forget)

| Channel | Function |
|---------|----------|
| `win:invoke` | Frameless window controls (show/showInactive/min/max/close) |
| `mini:open-main` | Show main window, hide mini |
| `mini:hide` | Hide mini window |
| `mini:drag-active` | Set drag state for mini window |
| `mini:set-pinned` | Pin/unpin mini window |
| `window:close/minimize/hide` | Window management |
| `window:traffic-lights` | Show/hide macOS traffic lights |
| `window:set-fullscreen` | Toggle simple fullscreen |
| `window:overlay` | Enter/exit overlay mode |
| `sound:play-system` | Play system sound via `afplay` |
| `log:info/error/warn` | Logging from renderer |

#### Events sent TO renderer (main -> renderer)

| Channel | Purpose |
|---------|---------|
| `agent:text-delta` | Streaming text delta |
| `agent:blocks-snapshot` | Updated blocks array |
| `agent:message-complete` | Agent response finished |
| `agent:message-error` | Agent error |
| `agent:user-message` | User message echoed back |
| `agent:delete-messages` | Messages deleted |
| `agent:response-started` | New response begun |
| `agent:set-theme` / `agent:create-theme` | Theme changes |
| `agent:compaction` | Context compaction event |
| `agent:agent-start` / `agent:agent-end` | Sub-agent lifecycle |
| `agent:child-event` | Child session events |
| `auth:grant-token` / `auth:expired` | Auth events |
| `mod:switch-start/success/error/reload` | MOD switching lifecycle |
| `work:changed` / `work:refreshed` | Work item updates |
| `tts:audio-chunk` / `tts:stop` | TTS streaming |
| `updater:status` | Update state changes |
| `sync:messages-upserted/deleted` | Sync events |
| `system:input-source-changed` | macOS IME change |

### 3.3 System Integration

| Feature | Implementation |
|---------|---------------|
| **Tray** | Template icon, left-click toggles main/mini, right-click menu (Chinese UI) |
| **Global Shortcut** | `Alt+C` toggles mini window |
| **Deep Links** | `colaos://` protocol, parses `grant_token` for OAuth |
| **OAuth Callback** | Local HTTP server on random port, Chinese HTML response |
| **Notifications** | System notification for task completion when unfocused |
| **Power Monitor** | Suspend/resume triggers sync reconnect |
| **Input Source** | Monitors macOS IME changes |
| **Sound** | `afplay` for system sounds (path restricted to `/System/Library/`) |

### 3.4 Local Database (SQLite)

**File:** `~/.cola/chat-history.db` (better-sqlite3, WAL mode)

**`messages` table:**
- `id`, `account_key`, `conversation_id`, `client_message_id`
- `role`, `content`, `blocks` (JSON), `error`
- `prompt_id`, `seq`, `created_at`, `synced_at`, `deleted_at`

**`sync_state` table:**
- `account_key`, `scope_key`, `conversation_id`, `sync_cursor`, `latest_seq`, `last_sync_at`

Account key: `user:{sub}` (from JWT) or `device:local` fallback.

### 3.5 WebSocket Bridge Protocol

Request format:
```json
{"type": "request", "id": "req_1_1712345678", "method": "agent.prompt", "params": {...}}
```

Response format:
```json
{"type": "response", "id": "req_1_1712345678", "ok": true, "result": {...}}
```

Event format:
```json
{"type": "event", "event": "agent:text-delta", "data": {...}}
```

**RPC methods forwarded to server:** `agent.prompt`, `agent.abort`, `agent.testConnection`, `settings.*`, `memory.*`, `themes.*`, `mod.*`, `auth.*`, `sync.*`, `queue.list`, `file.getDownloadUrl`

Request timeout: 300 seconds. Reconnect backoff: 1s, 2s, 4s, 8s, 15s, 30s (exponential).

### 3.6 Auto-Update

- Uses `electron-updater`
- `autoDownload: true`, `autoInstallOnAppQuit: true`
- First check: 10 seconds after launch
- Recurring check: every 4 hours
- Update URL: `https://cdn.colaos.ai/cola-public-file/release/update`

### 3.7 Connectivity Monitor

- Polls `https://api.colaos.ai/v1/health` every 15 seconds
- 5-second timeout per request
- On recovery: triggers sync reconnect

---

## 4. Frontend / Renderer

### 4.1 Technology Stack

| Layer | Technology | Version |
|-------|-----------|---------|
| UI Framework | React | 19.2.4 |
| State Management | Zustand | Single global store |
| Animation | Framer Motion | 12.35.0 |
| CSS Framework | Tailwind CSS | v4.x |
| Toast | Sonner | -- |
| Icons | Lucide React | 0.577.0 |
| Markdown | react-markdown + remark-gfm | -- |
| i18n | i18next / react-i18next | -- |
| UI Primitives | shadcn/ui pattern (Radix UI + cva) | -- |
| Build Tool | Vite | -- |
| Package Manager | pnpm | -- |

### 4.2 App Shell

- **`index.html`** -- Main window, loads `index-D3lN4nM9.js` (763 KB) + shared chunk (83 KB) + CSS (62 KB)
- **`mini.html`** -- Mini window, transparent background, loads `mini-C3lMPgx-.js` (36 KB)

### 4.3 State Management (Zustand Store)

```
Core State:
  appState: 'idle' | 'processing'
  settings: AppSettings
  isConfigured, isLoggedIn, userInfo

Streaming/Response:
  isAgentRunning, currentPromptId, streamStartedAt
  responseBlocks: ResponseBlock[], streamingText, error

Chat History:
  messages: ChatMessage[] (with CRDT-like upsert for sync)

Welcome/Onboarding:
  welcomePhase: 'dialogue' | 'login' | 'server-wait' | 'complete' | null
  welcomeDialogue, welcomeLang: 'en' | 'zh'

Queue: queuedPrompts, queueDroppedCount
MOD: currentMod (default: 'default'), availableMods
Sync: connectivity, lastSyncAt, pendingSyncCount
Update: updateStatus, updateDismissed, dismissedVersion
```

**Default model:** `claude-opus-4-6` via Anthropic provider

### 4.4 Navigation (State-Machine, No Router)

```
authReady=false              --> null (blank)
welcomePhase='login'         --> LoginPage
welcomePhase='dialogue'      --> WelcomeDialoguePage
welcomePhase='server-wait'   --> MainPage (loading)
onboardingCompleted          --> MainPage
!isLoggedIn && !isConfigured --> LoginPage
```

### 4.5 Component Hierarchy

```
App
  MotionConfig
    TooltipProvider
      TitleBar
      LoginPage | WelcomeDialoguePage | MainPage
        MainPage
          PlanetVisual (animated orb/avatar, reacts to waveform)
          VoiceButton (push-to-talk / recording)
          ChatPanel (right column, toggleable)
            Tabs: chat | files | tasks
            MessageList
              UserBubble / AssistantBubble
                ResponseTimeline
                  MarkdownContent
                  FileBlock / ThinkingBlock / ChatLoading
                CopyButton
            InputBar (text + attachments + voice + mentions)
            FilesPanel (output file gallery)
            TasksPanel (work items / checklists)
      SettingsPanel (Dialog overlay)
      UpdateBanner
      ApiErrorDialog
      Toaster
```

### 4.6 Feature Inventory

| Feature | Details |
|---------|---------|
| **Voice Input** | Push-to-talk, real-time waveform, on-device speech recognition |
| **Chat** | Streaming text, response blocks (text/file/thinking), markdown (GFM), file attachments, image mentions, auto-scroll |
| **Input** | Multi-line, drag-and-drop, file picker, mention system, voice recording, Enter to send |
| **Tasks Panel** | Work items with status tracking, checklists, artifacts, filters |
| **Files Panel** | Output gallery, image thumbnails (lazy loading), audio preview, open in Finder |
| **Settings** | Account, language (6), appearance (light/dark/system), cloud sync, TTS voice, developer options, feedback |
| **Welcome** | Cinematic multi-step dialogue, bilingual (zh/en), voice audio, BGM, flash effects |
| **Auth** | Google OAuth, magic link, OTP, activation code, JWT with refresh rotation |
| **Mini Window** | Independent vanilla TS implementation, own API bridge, chat, voice, pin/drag |

### 4.7 Event Polling Architecture

Events polled via `window.agentEvents.poll()` with adaptive frequency:
- Hidden tab: 1000ms
- Idle: 500ms
- Streaming: 50ms (20fps)
- Just-submitted: RAF (~16ms for 2 seconds)

### 4.8 Theme System

| Theme | Idle Hue | BG Color | Accent |
|-------|----------|----------|--------|
| Default | 252 (purple) | #151210 | 17 (orange) |
| Ocean | 195 (cyan) | #020c14 | 195 |
| Fire | 25 (orange) | #130800 | 30 |
| Forest | 145 (green) | #050f07 | 140 |
| Sakura | 340 (pink) | #12050a | 340 |

Each theme: `idleHue/Sat/Lit`, `listenHue/Sat/Lit`, `processHue/Sat/Lit`, `bgColor`, `accentHue`, `avatarType` (sphere/svg/image). Custom themes can be created by the agent.

### 4.9 Design System

**Light Mode:** `--col-bg: #FFFBF5` (warm cream), accent `#f1752d` (warm orange)
**Dark Mode:** `--col-bg: #151210` (deep warm black), accent `#df7436`
**Fonts:** `-apple-system, BlinkMacSystemFont, Segoe UI, sans-serif` / `ui-monospace, SFMono-Regular, Menlo`

### 4.10 Audio Files

| Category | Files | Purpose |
|----------|-------|---------|
| BGM | 1 file (1.9MB) | Background music during onboarding |
| Transition | 2 files (zh/en) | Transition music |
| SFX | 2 files | Awaken + confirmation sounds |
| Voice Lines | 28 files (bilingual) | Narration for onboarding steps |

---

## 5. Built-in Server

**File:** `Resources/server/cola-server.cjs` (~11MB, 3,277 lines minified)
**Framework:** Express.js
**Default bind:** `127.0.0.1:19532`

### 5.1 Internal WebSocket RPC Events

| Event | String Value |
|-------|-------------|
| `AGENT_PROMPT` | `agent.prompt` |
| `AGENT_ABORT` | `agent.abort` |
| `AGENT_TEST_CONNECTION` | `agent.testConnection` |
| `MEMORY_READ/WRITE` | `memory.read/write` |
| `SETTINGS_GET/SAVE/UPDATE` | `settings.get/save/update` |
| `AUTH_GET/SET_TOKENS/LOGOUT/REFRESH` | auth operations |
| `SERVER_HEALTH/STATUS` | server health/status |
| `SESSION_LIST/SHOW/DELETE/CLEAN` | session management |
| `MOD_LIST/CURRENT/SWITCH` | mod management |
| `LOG_LIST/READ` | log operations |
| `CRON_LIST/DELETE` | cron management |
| `CHANNEL_LIST/SAVE` | channel management |
| `QUEUE_LIST` | queue listing |
| `FILE_GET_DOWNLOAD_URL` | file download URLs |
| `THEMES_LOAD` | theme loading |
| `AWARENESS_CRON_TRIGGER` | awareness cron |
| `MEMORY_CRON_TRIGGER` | memory cron |
| `SYNC_*` | sync operations (status, run-now, push/pull history, retry, migration) |

### 5.2 REST API Surface (OpenAI/Anthropic-compatible)

#### Chat & Completions
| Method | Path | Description |
|--------|------|-------------|
| POST | `/v1/chat/completions` | OpenAI-compatible chat completions |
| POST | `/v1/messages` | Anthropic-compatible messages |
| POST | `/v1/complete` | Legacy completions |
| POST | `/v1/fim/completions` | Fill-in-middle code completion |
| POST | `/v1/chat/classifications` | Chat classifications |
| POST | `/v1/chat/moderations` | Chat moderations |

#### Agents
| Method | Path | Description |
|--------|------|-------------|
| POST | `/v1/agents` | Create agent |
| GET | `/v1/agents` | List agents |
| GET | `/v1/agents/{id}` | Get agent |
| PUT | `/v1/agents/{id}` | Update agent |
| DELETE | `/v1/agents/{id}` | Delete agent |
| GET | `/v1/agents/{id}/versions` | List versions |
| POST | `/v1/agents/completions` | Agent completions |

#### Conversations
| Method | Path | Description |
|--------|------|-------------|
| POST | `/v1/conversations` | Start conversation |
| GET | `/v1/conversations` | List conversations |
| GET | `/v1/conversations/{id}` | Get conversation |
| POST | `/v1/conversations/{id}/messages` | Append messages |
| GET | `/v1/conversations/{id}/history` | Get history |
| POST | `/v1/conversations/{id}/restart` | Restart conversation |

#### Files & Libraries (RAG)
| Method | Path | Description |
|--------|------|-------------|
| POST | `/v1/files` | Upload file |
| GET | `/v1/files` | List files |
| GET | `/v1/files/{id}/content` | Download file |
| POST | `/v1/libraries` | Create library |
| GET | `/v1/libraries` | List libraries |
| POST | `/v1/libraries/{id}/documents` | Upload document |
| GET | `/v1/libraries/{id}/documents` | List documents |

#### Audio, Embeddings, Models, Other
| Method | Path | Description |
|--------|------|-------------|
| POST | `/v1/audio/transcriptions` | Audio transcription |
| POST | `/v1/embeddings` | Embeddings |
| GET | `/v1/models` | List models |
| POST | `/v1/moderations` | Moderations |
| POST | `/v1/ocr` | OCR |
| POST | `/v1/url/scrape` | URL scraping |
| GET | `/v1/me` | Current user |
| POST | `/v1/auth/refresh` | Token refresh |
| GET/POST | `/v1/skills` | Skills management |
| POST | `/v1/messages/count_tokens` | Token counting |

#### Fine-tuning & Batch
| Method | Path | Description |
|--------|------|-------------|
| POST | `/v1/fine_tuning/jobs` | Create fine-tuning job |
| POST | `/v1/batch/jobs` | Create batch job |

#### Browser Control (separate Express sub-app)
| Method | Path | Description |
|--------|------|-------------|
| GET | `/` | Browser profile status |
| GET | `/profiles` | List profiles |
| POST | `/profiles/create` | Create profile |
| POST | `/start` / `/stop` | Launch/stop browser |
| GET | `/tabs` | List open tabs |
| POST | `/tabs/open` / `/tabs/focus` | Tab management |
| POST | `/navigate` | Navigate to URL |
| POST | `/screenshot` | Take screenshot |
| GET | `/snapshot` | Page accessibility snapshot |
| POST | `/act` | Browser actions (click, type, scroll) |
| POST | `/pdf` | Generate PDF |

### 5.3 WebSocket Rate Limiting

- Strict: 10 requests per 10/60 seconds (sensitive ops)
- General: 200 requests per 200/60 seconds

### 5.4 Streaming Events

| Event | Description |
|-------|-------------|
| `agent:agent-start` | Agent turn begins |
| `agent:text-delta` | Incremental text token |
| `agent:thinking` | Thinking/reasoning block |
| `agent:tool-start` / `agent:tool-end` | Tool call lifecycle |
| `agent:complete` | Turn completed |
| `agent:agent-end` | Agent session ends |
| `agent:turn-settled` | Turn fully settled |
| `agent:error` | Error occurred |
| `agent:compaction` | Context compaction event |
| `agent:file` | File operation |
| `agent:session-spawned` | Sub-agent session created |
| `agent:set-theme` / `agent:create-theme` | Theme events |

### 5.5 Autonomous Memory System

Cola has **cron-scheduled self-reflection:**
- **Diary cron:** `0 21 * * *` (daily 9 PM) -- writes memory diary entries
- **Self-reflection cron:** `30 21 * * *` (daily 9:30 PM) -- runs awareness reflection
- Mutex-protected, date-based dedup prevents double-runs

### 5.6 Slack Integration

Full Slack Bot via `@slack/bolt`:
- Socket Mode + HTTP Receiver
- Event handlers: `message`, `app_mention`
- Actions: `postMessage`, `delete`, `filesUploadV2`
- OAuth installation flow

---

## 6. LLM Provider Integration

### 6.1 Registered Providers (25+)

| Provider | API Protocol | Notes |
|----------|-------------|-------|
| `anthropic` | anthropic-messages | Primary |
| `openai` | openai-completions | Standard |
| `openai` (responses) | openai-responses | Newer API |
| `openai-codex` | openai-codex-responses | Code-specific |
| `azure-openai-responses` | azure-openai-responses | Azure variant |
| `google` | google-generative-ai | Gemini API |
| `google-vertex` | google-vertex | Vertex AI |
| `google-gemini-cli` | google-gemini-cli | CLI variant |
| `google-antigravity` | google variant | -- |
| `mistral` | mistral-conversations | Mistral API |
| `amazon-bedrock` | bedrock-converse-stream | AWS Bedrock |
| `xai` | openai-compat | Grok |
| `groq` | openai-compat | Fast inference |
| `github-copilot` | openai-compat | Copilot |
| `openrouter` | openai-compat | 246 model mappings |
| `vercel-ai-gateway` | openai-compat | Vercel |
| `huggingface` | -- | HF Inference |
| `cerebras` | -- | Fast inference |
| `minimax` / `minimax-cn` | -- | MiniMax |
| `kimi-coding` | -- | Moonshot |
| `zai` | -- | -- |
| `opencode` / `opencode-go` | -- | -- |

### 6.2 Model Catalog (150+ models)

**Claude family:** claude-1.3 through claude-opus-4-6 (incl. `-thinking` variants)

**GPT family:** gpt-4 through gpt-5.4-pro, gpt-oss-20b/120b

**Gemini family:** gemini-1.5-flash/pro through gemini-3.1-flash/pro

**Grok family:** grok-2 through grok-4-1-fast

**Mistral family:** mistral-large, medium, small, nemo, saba

**Others:** DeepSeek-R1/V3, Llama-3.x, etc.

### 6.3 Provider Configuration

- Custom model configs at `~/.pi/agent/models.json`
- Runtime provider registration with `baseUrl`, `apiKey`/`oauth`, `models[]`, `streamSimple`
- Model overrides: `baseUrl`, `headers`, `compat`, `cost`, `contextWindow`, `maxTokens`

### 6.4 Streaming Protocols

Each provider has `stream()` (full structured events) and `streamSimple()` (text-only):
- **Anthropic:** SSE with `message_start`, `content_block_start/delta/stop`, `message_delta/stop`
- **OpenAI:** SSE with `choices[].delta`, `data: [DONE]`
- **Google:** SSE via `/v1internal:streamGenerateContent?alt=sse`
- **Bedrock:** Converse stream API

### 6.5 Retry & Fallback

- OpenAI Codex: retry on 429, 500, 502, 503, 504
- Google: multi-endpoint failover, exponential backoff
- OpenRouter: 246-model routing as fallback layer

### 6.6 Environment Variable Keys

```
ANTHROPIC_API_KEY, ANTHROPIC_AUTH_TOKEN, ANTHROPIC_OAUTH_TOKEN
OPENAI_API_KEY
AZURE_OPENAI_API_KEY, AZURE_OPENAI_ENDPOINT, AZURE_OPENAI_RESOURCE_NAME
AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY, AWS_SESSION_TOKEN, AWS_BEARER_TOKEN_BEDROCK
GOOGLE_APPLICATION_CREDENTIALS, GOOGLE_CLOUD_API_KEY
GH_TOKEN, GITHUB_TOKEN, COPILOT_GITHUB_TOKEN
```

---

## 7. Agent Runtime & MOD System

### 7.1 MOD System

MODs are named agent configurations stored in `~/.cola/mods/`:

```
~/.cola/mods/
  default/
    AGENT.md          -- system prompt
    skills/           -- per-mod skills
    extensions/        -- per-mod extensions
  custom-mod/
    AGENT.md
    ...
```

- Default mod auto-creates with "Solar System AI Assistant" prompt
- File watching with 300ms debounce for hot-reload
- Global resources at `~/.cola/resources/` with `skills/`, `prompts/`, `extensions/`
- Conflict detection between mod-local and global skills

### 7.2 System Prompt Assembly

Composed from multiple components:
1. Agent content (from AGENT.md)
2. Active mod context + available mods list
3. Channel context
4. Global memory
5. Cola directory context
6. Runtime awareness
7. Active tool names
8. Available agents list

### 7.3 Context Compaction

- Token counting via dedicated tokenizer
- Context overflow detection at configurable threshold (default 100,000 tokens)
- Historical messages compressed/summarized when limits exceeded

### 7.4 Session Management

- Sessions stored in `~/.cola/sessions/`
- Scoped by `{source}:{name}` (e.g., `desktop:local`, `cli:benzema`)
- Conversation replay, history tracking
- Clean-up support with age-based filtering

---

## 8. MCP Integration

### 8.1 Chrome DevTools MCP

Cola uses the Chrome DevTools MCP server:
```
npx -y chrome-devtools-mcp@latest --autoConnect --experimentalStructuredContent --experimental-page-id-routing
```

Transport: StdioClientTransport (stdin/stdout JSON-RPC)

**MCP tools:**
- `list_pages`, `new_page`, `select_page`, `close_page`
- `navigate_page`, `take_snapshot`, `take_screenshot`
- `click`, `fill`, `fill_form`, `hover`, `drag`, `press_key`
- `resize_page`, `handle_dialog`, `evaluate_script`

### 8.2 Connection Management

- Connection pool (Map) by profile key `[profileName, userDataDir]`
- Pending connections (Map) prevents duplicates
- Auto-cleanup on transport PID death
- Graceful shutdown: SIGTERM -> 2s -> SIGKILL

### 8.3 Tool Calling

Supports tool calling across multiple APIs:
- **Anthropic:** `tool_use`, `server_tool_use`, `mcp_tool_use` content blocks
- **OpenAI:** `function` type tool calls with `tool_call_id`
- Tool types: `DocumentLibraryTool`, `ChunkTool`, custom extension tools

---

## 9. Browser Automation

### 9.1 Dual-Mode Architecture

**Mode 1: Local Managed (`local-managed`):**
- Cola launches its own Chromium browser via Playwright
- Direct CDP (Chrome DevTools Protocol) access
- Browser auto-detection: Chrome, Chrome Beta, Chrome Canary, Chromium, Brave, Edge
- Headless mode support (`--headless=new`)
- Launch flags: `--no-first-run`, `--no-default-browser-check`, `--disable-sync`, `--window-size=1440,960`

**Mode 2: Existing Session (`local-existing-session`):**
- Attaches to running Chrome via Chrome DevTools MCP
- Limited feature set (no PDF, no selector clicks, no typed input)
- Auto-connect flow

### 9.2 Profile Management

- Multiple profiles with independent configs
- Each profile: name, color, CDP port/URL, user data dir, driver type
- Built-in `cola` and `user` profiles protected from deletion
- CDP port auto-allocation
- 10-color palette for profile identification

### 9.3 Screenshot Pipeline

1. Take screenshot via MCP or Playwright
2. Save to temp (UUID in `os.tmpdir()`)
3. Compress/resize: max 1440px side, configurable max bytes
4. Save to `~/.cola/outputs/browser-{timestamp}.{ext}`
5. Return metadata

### 9.4 Page Snapshot

- AI format: semantic text representation
- Aria format: interactive elements with ref IDs
- Ref-to-UID mapping for element targeting
- Configurable limit (default 40, max 500 nodes)

---

## 10. On-Device Speech Models

**Total model weight: ~327 MB** -- all ONNX format, INT8 quantized, fully offline.

### 10.1 Silero VAD (Voice Activity Detection)
- **File:** `silero_vad.onnx` (629 KB)
- **Producer:** PyTorch 2.2.2
- **Purpose:** Detects speech vs silence for audio segmentation

### 10.2 SenseVoice (Multilingual ASR)
- **Directory:** `sherpa-onnx-sense-voice-zh-en-ja-ko-yue-int8-2024-07-17/`
- **Model:** `model.int8.onnx` (228 MB, INT8 quantized)
- **Vocabulary:** 25,055 tokens (SentencePiece BPE)
- **Languages:** Chinese, English, Japanese, Korean, Cantonese
- **Source:** Alibaba
- **Special capabilities:**
  - ASR (speech recognition): `<|ASR|>`
  - AED (audio event detection): `<|AED|>` -- Speech, BGM, Laughter, Applause, Cry, Sneeze, Breath, Cough, Sing
  - SER (speech emotion recognition): `<|SER|>` -- HAPPY, SAD, ANGRY, NEUTRAL, FEARFUL, DISGUSTED, SURPRISED

### 10.3 Whisper Tiny English
- **Directory:** `sherpa-onnx-whisper-tiny.en/`
- **Encoder:** `tiny.en-encoder.int8.onnx` (12 MB)
- **Decoder:** `tiny.en-decoder.int8.onnx` (86 MB)
- **Vocabulary:** 50,256 tokens (OpenAI BPE)
- **Purpose:** Lightweight English-only fallback

### 10.4 Inference Pipeline

```
Audio Input -> Silero VAD (segment speech) -> SenseVoice (multilingual) or Whisper Tiny (English fallback)
```

Runtime: **sherpa-onnx** (k2-fsa), C++/ONNX via `sherpa-onnx-node` native addon.
Hardware acceleration: Apple CoreML + Metal GPU via ONNX Runtime 1.23.2.

---

## 11. Skills System (35 Skills)

### 11.1 Skill Architecture

Each skill is a directory with `SKILL.md` (YAML frontmatter + Markdown body):

```yaml
---
name: my-skill-name           # kebab-case, max 64 chars
description: >                # REQUIRED -- trigger mechanism
  What it does. Use when [scenarios].
version: 1.0.0
tags: [tag1, tag2]
metadata:
  openclaw:
    emoji: "..."
    os: ["darwin"]
    requires:
      bins: ["tool"]           # required CLI binaries
      env: ["API_KEY"]         # required env vars
    install: [...]
---
```

**Loading priority:** MOD skills > Global skills > Bundled skills

### 11.2 Meta / System Skills (2)

| Skill | Description |
|-------|-------------|
| **cola-skill-creator** | Guide for creating new Cola skills. 6-step workflow: Intent > Plan > Frontmatter > Body > Resources > Validate. Includes `skill-spec.md` and `writing-guide.md` references. |
| **self-improving-agent** | Continuous improvement framework. Logs to `.learnings/` (LEARNINGS.md, ERRORS.md, FEATURE_REQUESTS.md). Promotion pipeline: learning -> project memory -> workspace guidance. Recurring pattern detection with auto-promotion at count >= 3. Skill extraction for mature learnings. |

### 11.3 Office Document Skills (4)

| Skill | Description | Dependencies |
|-------|-------------|-------------|
| **docx** | Create/read/edit Word documents. Three workflows: Read (pandoc), Create (docx-js), Edit (unpack XML > edit > pack). Tracked changes + comments support. 14 bundled scripts + ECMA-376 XSD schemas. | pandoc, LibreOffice, Poppler |
| **xlsx** | Create/read/edit Excel spreadsheets. Tools: pandas (data), openpyxl (formulas). Mandatory formula recalculation via LibreOffice. Financial model color coding standards. | pandas, openpyxl, LibreOffice |
| **pptx** | Create/read/edit PowerPoint. Three workflows: Read (markitdown), Edit (XML), Create (PptxGenJS). 10 design themes, typography pairings. Mandatory 2-step QA. | markitdown, Pillow, pptxgenjs, LibreOffice |
| **pdf** | Full PDF processing: read, merge, split, rotate, watermark, create, fill forms, encrypt/decrypt, OCR. Form filling pipeline with fillable/non-fillable detection. 8 bundled scripts. | pypdf, pdfplumber, reportlab, pytesseract, poppler-utils |

### 11.4 Google Workspace Skills (18)

All require `gws` CLI binary. Shared auth via `gws-shared`.

| Skill | Service | Capabilities |
|-------|---------|-------------|
| **gws-shared** | -- | Auth patterns, global flags, security rules |
| **gws-calendar** | Calendar | Full ACL, calendar lists, events, freebusy, settings |
| **gws-calendar-agenda** | Calendar | Show upcoming events (read-only) |
| **gws-calendar-insert** | Calendar | Create events with attendees |
| **gws-docs** | Docs | Read/write via batchUpdate |
| **gws-docs-write** | Docs | Append text to a doc |
| **gws-drive** | Drive | Files, folders, shared drives, permissions |
| **gws-drive-upload** | Drive | Upload with auto metadata |
| **gws-events** | Events | Workspace event subscriptions (Pub/Sub) |
| **gws-events-subscribe** | Events | Subscribe + stream as NDJSON |
| **gws-events-renew** | Events | Renew expiring subscriptions |
| **gws-gmail** | Gmail | Full send, read, drafts, labels, threads |
| **gws-gmail-send** | Gmail | Send email (to, subject, body, cc, bcc) |
| **gws-gmail-triage** | Gmail | Unread inbox summary (read-only) |
| **gws-gmail-reply** | Gmail | Reply with auto-threading |
| **gws-gmail-reply-all** | Gmail | Reply-all with auto-threading |
| **gws-gmail-forward** | Gmail | Forward to new recipients |
| **gws-gmail-watch** | Gmail | Watch + stream new emails (NDJSON) |
| **gws-sheets** | Sheets | Full spreadsheet CRUD, developer metadata |
| **gws-sheets-read** | Sheets | Read values from range |
| **gws-sheets-append** | Sheets | Append rows |

### 11.5 Apple Ecosystem Skills (3)

| Skill | Method | Dependencies |
|-------|--------|-------------|
| **apple-calendar** | AppleScript (7 shell scripts) | macOS only |
| **apple-notes** | `memo` CLI | `brew install antoniorodr/memo/memo` |
| **apple-reminders** | `remindctl` CLI | `brew install steipete/tap/remindctl` |

### 11.6 Productivity / Integration Skills (5)

| Skill | Description | Dependencies |
|-------|-------------|-------------|
| **gh-cli** | Comprehensive GitHub CLI reference (v2.85.0). Auth, repos, issues, PRs, Actions, projects, releases, gists, codespaces, orgs. 400+ lines. | `gh` CLI |
| **notion** | Notion API (2025-09-03). Pages, databases ("Data Sources"), blocks. | `NOTION_API_KEY` |
| **obsidian** | Obsidian vault management. Search, create, move (with wikilink refactoring), delete. | `obsidian-cli` |
| **coli** | Speech recognition (ASR). `coli asr <audio-file>` with sensevoice/whisper engine. | `@marswave/coli` |
| **spotify-player** | Terminal Spotify playback/search via `spogo` or `spotify_player`. | Spotify Premium |

---

## 12. CLI Tool

**File:** `Resources/cli/cola-cli.cjs` (399 KB, single-file CJS bundle)
**Version:** CLI 0.1.1, App 0.3.10
**Runtime:** Node.js v24.14.0 (bundled), min v20
**Framework:** Commander.js v14.0.3

### 12.1 Commands

#### `cola message <message>`
Send a message to Cola Agent.

| Option | Description | Default |
|--------|-------------|---------|
| `--session <name>` | Session scope key | `cli:$USER` |
| `-j, --json` | Output as JSON | false |
| `-v, --verbose` | Show tool execution details | false |
| `--debug` | Show WebSocket frames | false |
| `--server <url>` | Server URL | `localhost:19532` |
| `--attachments <json>` | JSON array of file paths | -- |

#### `cola status`
| Option | Default |
|--------|---------|
| `-j, --json` | false |
| `--server <url>` | `localhost:19532` |

#### `cola config list|get|set`
| Subcommand | Options |
|------------|---------|
| `list` | `--json`, `--show-secrets` |
| `get <key>` | `--json`, `--show-secrets` |
| `set <key> <value>` | Auto-parses booleans, numbers, JSON |

#### `cola server start|stop`
| Subcommand | Options |
|------------|---------|
| `start` | `--host`, `--port` (from app: managed by desktop) |
| `stop` | Reads PID, SIGTERM -> 5s -> SIGKILL |

#### `cola session list|show|delete|clean`
| Subcommand | Options |
|------------|---------|
| `list` | `--json` |
| `show <scope>` | `-n <count>` (default 20), `--json`, `--verbose` |
| `delete <scope>` | -- |
| `clean` | `--older-than <duration>` (default 7d) |

#### `cola logs`
| Option | Default |
|--------|---------|
| `-n, --lines <count>` | 50 |
| `-f, --follow` | false |

### 12.2 WebSocket Protocol

**Connection:** `ws://{host}:{port}?token={gateway_token}` (5s timeout)

**Request:**
```json
{"type": "request", "id": "1", "method": "agent.prompt", "params": {...}}
```

**Response:**
```json
{"type": "response", "id": "1", "ok": true, "result": {...}}
```

**Event:**
```json
{"type": "event", "event": "agent:text-delta", "data": {"delta": "...", "promptId": "..."}}
```

### 12.3 `cola message` End-to-End Flow

1. Parse options, resolve session key (`cli:$USER` default)
2. Read gateway token from `~/.cola/gateway-token`
3. Open WebSocket (5s timeout)
4. Send `agent.prompt` with `{message, scopeKey, attachments?}`
5. Receive `{promptId, model, provider}`
6. Stream events: `text-delta` -> stdout, `tool-start/end` -> stderr (verbose)
7. Track sub-agent sessions (`session-spawned`, `turn-settled`)
8. Resolve on parent `complete` + all children settled
9. Timeout: `COLA_CLI_TIMEOUT_MS` (default 10 min), exit code 2
10. JSON mode: structured output with steps[], sessions[], response

### 12.4 Environment Variables

| Variable | Purpose | Default |
|----------|---------|---------|
| `COLA_DATA_DIR` | Data directory | `~/.cola` |
| `COLA_HOST` | Server host | `127.0.0.1` |
| `COLA_PORT` | Server port | `19532` |
| `COLA_CLI_TIMEOUT_MS` | Message timeout | `600000` |

---

## 13. Native Modules & Frameworks

### 13.1 Electron Frameworks

| Framework | Version | Purpose |
|-----------|---------|---------|
| Electron Framework | 40.7.0 | Core runtime (Chromium, arm64) |
| Squirrel.framework | 1.0 | Auto-update (GitHub Squirrel) |
| Mantle.framework | 0.0.0 | ObjC model layer for Squirrel |
| ReactiveObjC.framework | 3.1.0 | Reactive programming for Squirrel |

**Helper Apps:** Cola Helper, Cola Helper (GPU), Cola Helper (Renderer), Cola Helper (Plugin) -- all arm64, version 0.3.10.16.

### 13.2 Native Modules (app.asar.unpacked)

| Module | Version | Size | Purpose |
|--------|---------|------|---------|
| sherpa-onnx-darwin-arm64 | 1.12.29 | 72 MB | ONNX runtime for speech (CoreML + Metal) |
| koffi | 2.15.1 | 83 MB | Universal C FFI (21 platforms) |
| @mariozechner/clipboard-darwin-arm64 | 0.3.2 | 2 MB | Native clipboard access |
| @mariozechner/clipboard-darwin-universal | 0.3.2 | 4.2 MB | Universal clipboard binary |
| better-sqlite3 | 12.6.2 | -- | SQLite native addon |
| @img/sharp-darwin-arm64 | 0.34.5 | 274 KB | Sharp native addon |
| @img/sharp-libvips-darwin-arm64 | 1.2.4 | 15 MB | libvips image processing |
| jszip | 3.10.1 | -- | ZIP handling |

### 13.3 sherpa-onnx Native Stack

| Binary | Size | Type |
|--------|------|------|
| `sherpa-onnx.node` | 570 KB | Node.js N-API addon |
| `libsherpa-onnx-c-api.dylib` | 4.6 MB | C API layer |
| `libsherpa-onnx-cxx-api.dylib` | 141 KB | C++ API layer |
| `libonnxruntime.1.23.2.dylib` | 33 MB | ONNX Runtime (CoreML + Metal) |

---

## 14. Dependency Analysis

### 14.1 Overview

- **Total node_modules size:** 479 MB
- **Total packages:** 755
- **Build tool:** pnpm

### 14.2 Cola's Own Packages

#### @mariozechner scope (pi framework, v0.62.0)

| Package | Description |
|---------|-------------|
| **pi-agent-core** | General-purpose agent with transport abstraction, state management, attachment support |
| **pi-ai** | Unified LLM API with 7 providers (Anthropic, OpenAI x3, Google x2, Mistral, Azure, Bedrock) + OAuth |
| **pi-coding-agent** | Coding agent SDK with read/bash/edit/write tools, session management, extensions, skills. 24 doc files. |
| **pi-tui** | Terminal UI with differential rendering |
| **clipboard** + **clipboard-darwin-*** | Native clipboard access |
| **jiti** | Fork of jiti with virtualModules support |

**Key finding:** The `pi-*` packages are a full **Claude Code-like coding agent framework**. `pi-coding-agent` has an SDK: `createAgentSession()`, event subscriptions, programmatic prompting.

#### @marswave scope

| Package | Version | Description |
|---------|---------|-------------|
| **coli** | 0.0.13 | CLI with ASR + TTS via sherpa-onnx and mac-say |

### 14.3 LLM Provider SDKs

| Package | Version |
|---------|---------|
| @anthropic-ai/sdk | 0.73.0 |
| openai | 6.26.0 |
| @mistralai/mistralai | 1.14.1 |
| @google/genai | via pi-ai |
| @aws-sdk/client-bedrock-runtime | via pi-ai (27 + 44 sub-packages) |

### 14.4 Major Dependencies by Category

| Category | Packages | Notable |
|----------|----------|---------|
| **OpenTelemetry** | 58 packages | Traces, metrics, logs + exporters for gRPC, HTTP, Prometheus, Zipkin, Jaeger |
| **Sentry** | 10 packages | @sentry/electron 7.10.0 |
| **Radix UI** | 31 packages | Headless UI primitives |
| **AWS SDK** | 71 packages | Bedrock runtime |
| **Slack** | 6 packages | @slack/bolt 4.6.0 |
| **Speech** | sherpa-onnx + koffi | 155 MB total (32% of node_modules) |
| **3D** | three 0.183.2 | 15 MB -- visual effects/UI |
| **Schema** | zod 4.3.6 | Bleeding-edge Zod 4 |

### 14.5 Naming Lineage

Internal: **pi** (`pi-agent-core`, `pi-coding-agent`, `pi-ai`, `pi-tui`)
Product: **Cola** (`@marswave/coli`, `ai.marswave.cola`)
Platform: **ColaOS** (`colaos.ai`)
Skill system: **OpenClaw** (metadata format references)

---

## 15. Security & Code Signing

### 15.1 Signing Identity

- **Authority:** Developer ID Application: MarsWave (Hong Kong) Limited (CJSWWQ2B9V)
- **Signed:** Mar 26, 2026
- **Notarization:** Apple-notarized (Gatekeeper approved)
- **Hardened Runtime:** v26.1.0
- **ASAR Integrity:** SHA256 verified in Info.plist

### 15.2 Provisioning Profile

- **Name:** `cola_pro`
- **Type:** DIRECT (Developer ID distribution)
- **Created:** 2026-03-04
- **Expires:** 2044-02-28 (18-year lifetime)
- **APS Environment:** production (push notifications)

### 15.3 Entitlements (Extremely Broad)

**Hardware:**
- `device.audio-input` (Microphone)
- `device.camera` (Camera)
- `device.bluetooth` (Bluetooth)
- `device.serial` (Serial devices)
- `device.usb` (USB devices)

**File System:**
- `files.all` (Full disk access)
- `files.downloads.read-write`
- `files.user-selected.read-write`
- `files.bookmarks.app-scope` / `document-scope`

**Personal Information:**
- `personal-information.addressbook` (Contacts)
- `personal-information.calendars` (Calendar)
- `personal-information.location` (Location)
- `personal-information.photos-library` (Photos)

**Network:**
- `network.client` (Outbound)
- `network.server` (Inbound)

**Code Execution (all protections disabled):**
- `cs.allow-jit` (JIT compilation)
- `cs.allow-unsigned-executable-memory`
- `cs.disable-executable-page-protection` (No W^X)
- `cs.disable-library-validation` (Load unsigned dylibs)
- `cs.allow-dyld-environment-variables` (DYLD injection)

**Automation:**
- `automation.apple-events`
- `temporary-exception.apple-events` for System Events and Finder

### 15.4 CSP (Content-Security-Policy)

```
default-src 'self';
script-src 'self' 'unsafe-inline';
style-src 'self' 'unsafe-inline';
img-src 'self' data: blob:;
media-src 'self' data: blob:;
connect-src 'self' ws://localhost:* http://localhost:*;
```

### 15.5 Electron Security

- `contextIsolation: true` (both windows)
- `sandbox: true` (both windows)
- `webSecurity: true` (main window)
- `nodeIntegration: false` (default)
- External URLs via `shell.openExternal()`
- File read blocked extensions: `.zip`, `.rar`, `.exe`, `.dmg`, `.app`, `.msi`, `.sh`, `.bash`, `.command`, `.ps1`, `.deb`, `.rpm`
- File read limit: 20MB

### 15.6 Auth Security

- Auth tokens at `~/.pi/agent/auth.json` with `chmod 0600`
- File locking via `proper-lockfile` (10 retries, 20ms backoff)
- Gateway token at `~/.cola/gateway-token`
- OAuth token refresh with cross-provider lock

---

## 16. Cloud Infrastructure

### 16.1 Endpoints

| Service | URL |
|---------|-----|
| Production API | `https://api.colaos.ai` |
| Staging API | `https://api.staging.colaos.ai` |
| LLM Gateway | `https://zhuoma.colaos.ai` |
| Staging Gateway | `https://zhuoma.staging.colaos.ai` |
| CDN / Updates | `https://cdn.colaos.ai` |
| TTS API | `https://api.marswave.ai/openapi/v1` |
| Health Check | `https://api.colaos.ai/v1/health` |

### 16.2 Hardcoded Values

**Client ID:**
```
PJBkELS1o_q9nJ~NzF2_Fmr21TNX&~eoJR49FFdFhD3U
```

**Sentry DSN:**
```
https://aeaadab83096a22f0fa39a0719b7cf7c@o4508880592502784.ingest.us.sentry.io/4511103547277312
```
- Organization: `o4508880592502784`
- Project: `4511103547277312`
- Release: `cola@{version}`

### 16.3 Cloud Sync

- Bi-directional changelog-based sync
- Pull: cursor-based, batches of 200
- Push: batches of 50
- Periodic sync every 5 minutes
- CRDT-like merge via `client_message_id` + `seq`

### 16.4 TTS Defaults

| Language | Speaker |
|----------|---------|
| Chinese | `wanjin-971d09f0` |
| English | `minight-kate-d4b925d0` |

---

## 17. File System Layout

### 17.1 App Bundle Structure

```
Cola.app/Contents/
  Info.plist                    -- App metadata
  PkgInfo                      -- APPL
  CodeResources                 -- Code signing resources
  embedded.provisionprofile     -- Provisioning profile
  _CodeSignature/               -- Code signature
  MacOS/
    Cola                        -- Main binary (56KB, Electron shell)
  Frameworks/
    Electron Framework.framework/  -- Electron 40.7.0
    Squirrel.framework/            -- Auto-update
    Mantle.framework/              -- ObjC models
    ReactiveObjC.framework/        -- Reactive programming
    Cola Helper.app/               -- General helper
    Cola Helper (GPU).app/         -- GPU process
    Cola Helper (Renderer).app/    -- Renderer process
    Cola Helper (Plugin).app/      -- Plugin process
  Resources/
    app.asar                    -- Electron app bundle (489 MB unpacked)
    app.asar.unpacked/          -- Native modules
    app-update.yml              -- Update config
    icon.icns                   -- App icon
    tray-iconTemplate@2x.png    -- Tray icon
    cli/
      cola-cli.cjs              -- CLI tool (399 KB)
    runtime/
      node                      -- Node.js v24.14.0 (114 MB)
    server/
      cola-server.cjs           -- Server (11 MB)
      node_modules/             -- Server native deps (26 MB)
    models/
      silero_vad.onnx           -- VAD model (629 KB)
      sherpa-onnx-sense-voice-*/-- SenseVoice ASR (228 MB)
      sherpa-onnx-whisper-*/    -- Whisper ASR (98 MB)
    skills/                     -- 35 skill directories
    *.lproj/                    -- Localization (30+ languages)
```

### 17.2 User Data Structure (`~/.cola/`)

```
~/.cola/
  settings.json                 -- User settings
  gateway-token                 -- WebSocket auth token
  cola-server.pid               -- Server PID
  chat-history.db               -- SQLite chat database
  sessions/                     -- Conversation sessions
  outputs/                      -- Generated files (screenshots, PDFs)
  work-items/                   -- Work item queue
  memory/                       -- Persistent agent memory
  awareness/                    -- Self-reflection data
  logs/                         -- Server logs (daily rotation, 7 days)
  mods/
    default/
      AGENT.md                  -- System prompt
      skills/                   -- Per-mod skills
      extensions/               -- Per-mod extensions
  resources/
    skills/                     -- Global skills
    prompts/                    -- Global prompts
    extensions/                 -- Global extensions
  browser/{profile}/user-data/  -- Browser profile data
  attachments/                  -- File attachments
  themes.json                   -- Custom themes
  channels.json                 -- Channel configs
  crons.json                    -- Scheduled tasks
  cron-stats.json               -- Cron statistics
  runtime/node                  -- Deployed Node.js binary

~/.pi/agent/
  auth.json                     -- Provider API keys (chmod 0600)
  models.json                   -- Custom model configs
```

---

## Appendix A: All Environment Variables

### Cola-Specific
| Variable | Purpose | Default |
|----------|---------|---------|
| `COLA_HOST` | Server bind host | `127.0.0.1` |
| `COLA_PORT` | Server bind port | `19532` |
| `COLA_DATA_DIR` | Data directory override | `~/.cola` |
| `COLA_BROWSER_EXECUTABLE` | Custom browser path | -- |
| `COLA_EVAL_GEMINI_SEARCH` | Gemini search eval | -- |
| `COLA_CLI_TIMEOUT_MS` | CLI message timeout | `600000` |

### PI Framework
| Variable | Purpose |
|----------|---------|
| `PI_PACKAGE_DIR` | Package directory |
| `PI_CACHE_RETENTION` | Cache policy (`long`/`short`) |
| `PI_OFFLINE` | Offline mode |
| `PI_BASE_URL` | Base URL override |
| `PI_KEY` / `PI_KEY_AUTH` / `PI_TOKEN` | Auth keys |

### Telemetry
| Variable | Purpose |
|----------|---------|
| `SENTRY_DSN` | Sentry DSN |
| `SENTRY_ENVIRONMENT` | Environment |
| `SENTRY_DEBUG` | Debug mode |
| `SENTRY_TRACES_SAMPLE_RATE` | Trace sampling |
| `OTEL_EXPORTER_OTLP_ENDPOINT` | OpenTelemetry endpoint |
| `OTEL_EXPORTER_OTLP_HEADERS` | OTLP headers |
| `OTEL_EXPORTER_PROMETHEUS_HOST/PORT` | Prometheus |

---

## Appendix B: Error Handling

### Custom Error Classes
- `MT` -- Browser errors (profile not found, CDP port busy)
- `Cc` -- Capability errors (unsupported feature)
- `tw` -- Tab not found
- `OV` -- Object not found
- `jr` -- API/SDK errors (Anthropic)
- `IX` -- Content filter error

### Error Flow
1. Try/catch wrappers on all route handlers
2. Error mappers convert to HTTP status codes
3. Structured `{ error: "message" }` JSON responses
4. Sentry reporting via `captureException` / `captureMessage`
5. Three-level logging: info/warn/error (prefix `[cola-server]`)
6. Trace logs: `~/.cola/logs/trace-*.jsonl` with rotation
7. EPIPE silencing on stdout/stderr

---

## Appendix C: Key Technical Observations

1. **Voice-first design philosophy:** The main page centers an animated orb with voice button; text chat is secondary. This is fundamentally a voice-first AI agent.

2. **Built on pi framework:** Cola is built on `@mariozechner/pi-*`, a full Claude Code-like coding agent SDK. The "pi" lineage is visible throughout the codebase (pi-agent-core, pi-coding-agent, pi-ai, pi-tui). Author: Mario Zechner (badlogic).

3. **Universal LLM gateway:** 25+ providers, 150+ models, unified streaming. Acts as a local API gateway normalizing across all major LLM providers.

4. **Autonomous memory:** Daily self-reflection crons (9 PM diary, 9:30 PM awareness) make Cola an autonomous agent that learns over time.

5. **Offline-capable:** 327 MB of speech models enable fully offline voice interaction. No network needed for transcription.

6. **Extremely broad permissions:** Full disk access, all personal data, all code execution protections disabled. This is the most permissive entitlement set possible for a macOS app.

7. **OpenClaw lineage:** Skill system metadata format references "OpenClaw", suggesting Cola inherits from or is related to the OpenClaw project.

8. **Hardware-accelerated inference:** ONNX Runtime links to CoreML + Metal, meaning speech models run on Apple Neural Engine and GPU, not just CPU.

9. **Multi-agent orchestration:** The CLI and server support spawning sub-agents with lifecycle tracking (`session-spawned`, `turn-settled`).

10. **Adaptive event polling:** Instead of WebSocket push to renderer, events are polled with smart frequency (50ms during streaming, 1000ms when hidden). Pragmatic choice for Electron.

---

*Report generated by deep analysis of Cola 0.3.10 (build 16) for macOS arm64.*
*Unpacked from: `/Users/benzema/Downloads/Cola_0.3.10_16_arm64.dmg`*
