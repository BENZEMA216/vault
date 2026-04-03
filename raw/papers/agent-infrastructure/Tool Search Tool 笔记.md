# Tool Search Tool 笔记

> 来源：https://platform.claude.com/docs/en/agents-and-tools/tool-use/tool-search-tool
> 阅读日期：2025-01-23

---

## 一、是什么

Tool Search Tool 是 Claude API 的一个功能，用于解决**工具太多**时的问题：

| 问题 | 说明 |
|------|------|
| **上下文爆炸** | 50 个工具定义 ≈ 10-20K tokens，占用大量上下文窗口 |
| **选择准确率下降** | 超过 30-50 个工具后，Claude 选错工具的概率大增 |

**解决方案**：不一次性加载所有工具，而是让 Claude 按需搜索和加载。

---

## 二、工作流程

```
传统方式：
一次性加载所有 100 个工具定义 → 上下文爆炸 + 选择困难

Tool Search 方式：
1. 只加载 "工具搜索工具" + 3-5 个常用工具
2. 其他工具标记为 defer_loading: true（延迟加载）
3. Claude 需要时，先搜索工具目录
4. API 返回 3-5 个最相关的工具定义
5. Claude 从中选择并调用
```

### 具体示例

```
用户: "旧金山天气怎么样？"
     ↓
Claude: 调用 tool_search(query="weather")
     ↓
API: 返回 [get_weather, weather_forecast] ← 只加载相关的
     ↓
Claude: 调用 get_weather(location="San Francisco")
     ↓
返回结果
```

---

## 三、两种搜索方式

| 类型 | 查询方式 | 示例 |
|------|----------|------|
| **Regex** (`tool_search_tool_regex_20251119`) | Python 正则表达式 | `"get_.*_data"` 匹配 `get_user_data` |
| **BM25** (`tool_search_tool_bm25_20251119`) | 自然语言 | "查询天气的工具" |

### Regex 常用模式

```python
"weather"                    # 包含 weather
"get_.*_data"                # get_user_data, get_weather_data
"database.*query|query.*database"  # OR 模式
"(?i)slack"                  # 大小写不敏感
```

---

## 四、API 使用

### 基本结构

```python
tools = [
    # 工具搜索器（必须立即加载，不能 defer）
    {
        "type": "tool_search_tool_regex_20251119",
        "name": "tool_search_tool_regex"
    },

    # 延迟加载的工具
    {
        "name": "get_weather",
        "description": "Get weather for a location",
        "input_schema": {...},
        "defer_loading": True  # 关键：延迟加载
    },
    {
        "name": "send_email",
        "description": "Send an email",
        "input_schema": {...},
        "defer_loading": True
    },
    # ... 可以有成百上千个工具
]
```

### 响应格式

```json
{
  "content": [
    {
      "type": "server_tool_use",
      "name": "tool_search_tool_regex",
      "input": {"query": "weather"}
    },
    {
      "type": "tool_search_tool_result",
      "content": {
        "tool_references": [
          {"type": "tool_reference", "tool_name": "get_weather"}
        ]
      }
    },
    {
      "type": "tool_use",
      "name": "get_weather",
      "input": {"location": "San Francisco"}
    }
  ]
}
```

---

## 五、限制和最佳实践

### 限制

- **最大工具数**：10,000 个
- **搜索结果**：每次返回 3-5 个最相关的工具
- **正则长度**：最大 200 字符
- **支持模型**：Sonnet 4.0+, Opus 4.0+（不支持 Haiku）

### 最佳实践

- 保留 3-5 个最常用工具为**非延迟加载**
- 写清晰的工具名和描述（搜索会匹配这些字段）
- 在 system prompt 中描述可用工具类别
- 监控哪些工具被发现，优化描述

### 适用场景

| 适用 | 不适用 |
|------|--------|
| 10+ 个工具 | 少于 10 个工具 |
| 工具定义占用 >10K tokens | 工具定义很小 |
| 工具库持续增长 | 每次都用所有工具 |
| MCP 多服务器场景（200+ 工具） | — |

---

## 六、核心思想：选择性暴露信息

### 同一抽象的不同实例

```
                    ┌─────────────────────────────┐
                    │   核心抽象：选择性暴露信息    │
                    │   "不给全部，只给需要的"     │
                    └──────────────┬──────────────┘
                                   │
          ┌────────────────────────┼────────────────────────┐
          │                        │                        │
          ▼                        ▼                        ▼
   ┌──────────────┐       ┌──────────────┐        ┌──────────────┐
   │ 渐进式披露    │       │  RAG / 按需检索 │        │  懒加载      │
   │ (面向人类)    │       │  (面向 LLM)    │        │  (面向系统)   │
   └──────────────┘       └──────────────┘        └──────────────┘
```

### 不同领域的术语

| 领域 | 术语 |
|------|------|
| UX 设计 | **渐进式披露** (Progressive Disclosure) |
| 信息检索 | **按需检索** (On-demand Retrieval) |
| 软件工程 | **懒加载** (Lazy Loading) |
| 操作系统 | **按需分页** (Demand Paging) |
| LLM 领域 | **动态上下文管理** / **RAG** |

---

## 七、类似项目/技术

| 项目/技术 | 应用方式 |
|-----------|----------|
| **RAG** | 不把所有文档塞进 prompt，按需检索相关段落 |
| **LLM-in-Sandbox** | 长文本放文件系统，AGENT 用 grep/cat 按需读取 |
| **MemGPT / Letta** | 虚拟内存思想，上下文分层，按需调入调出 |
| **LangChain Tool Retriever** | 用 embedding 检索相关工具 |

---

## 八、与 Creative CoWork 的关联

### Skills 系统可采用两层架构

```
┌─────────────────────────────────────────────┐
│  Layer 1: 常驻 Skills (3-5个最常用)         │
│  - 文件管理、基础生成、项目设置              │
├─────────────────────────────────────────────┤
│  Layer 2: 按需加载 Skills (通过检索)         │
│  - 视频剪辑、音频处理、特效、调色...         │
│  - 用户自定义的各种 workflow                │
└─────────────────────────────────────────────┘
```

### 检索方式选择

| 方式 | 适用场景 |
|------|----------|
| Regex/关键词 | 工具名明确、简单场景 |
| BM25 | 用户描述模糊 |
| Embedding 向量检索 | 大规模工具库、语义理解 |

### 产品表述

> "Skills 的加载采用**渐进式披露**思路——AGENT 不一次性看到所有能力，而是根据任务按需检索加载，降低决策负担。"

---

## 九、参考资料

- 官方文档：https://platform.claude.com/docs/en/agents-and-tools/tool-use/tool-search-tool
- 相关：[[LLM-in-Sandbox 论文笔记]]（按需检索的另一个应用）

---

*笔记整理于 2025-01-23*
