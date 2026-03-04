# 0410 -【学习】AGENT2AGENT 协议

- 介绍文章：https://developers.googleblog.com/en/a2a-a-new-era-of-agent-interoperability/
- Github 目录：https://github.com/google/A2A
- IO 文档：https://google.github.io/A2A/#/documentation

看懂了什么？
- MCP 和 A2A 的关系和差异我看懂了
- A2A 的具体通信过程和流程，懂了一些
没看懂什么？
- A2A 真的能解决问题吗？ 不知道，我的思考是：
  - 不能解决能力/用户的问题，Google 的 showcase 跟开玩笑一样，看不到潜力；
  - 也许能解决 AGENT 商业化的问题
  - 也许能解决数据隐私的问题
- 为什么这么设计，有哪些精妙&可以学习之处，还没理解到
https://www.zhihu.com/question/1893443983843255220
- A2A 这个协议现在很难大放光彩啊，可能也需要一个类似 Manus 的产品来激活潜力，最终的结果可能是一个 AGENT 协作体系？



## 五个设计原则？
- **Embrace agentic capabilities**: A2A focuses on enabling agents to collaborate in their natural, ***unstructured modalities***, even when ***they don't share memory, tools and context***. We are enabling true multi-agent scenarios without limiting an agent to a "tool."
  - 非结构化的数据方式
  - 可以不 share 记忆、工具和上下文
- **Build on existing standards:** The protocol is built on top of existing, popular standards including HTTP, SSE, JSON-RPC, which means it's easier to integrate with existing IT stacks businesses already use daily.
  - 这不废话嘛...训练数据就是基于原本的协议的，新造一个协议很容易崩的
- **Secure by default**: A2A is designed to support enterprise-grade authentication and authorization, with parity to OpenAPI's authentication schemes at launch.

- **Support for long-running tasks:** We designed A2A to be flexible and support scenarios where it excels at completing everything from quick tasks to deep research that may take hours and or even days when humans are in the loop. Throughout this process, A2A can provide real-time feedback, notifications, and state updates to its users.

- **Modality agnostic:** The agentic world isn't limited to just text, which is why we've designed A2A to support various modalities, including audio and video streaming.

## 工作原理

A2A 让 client Agent 和 remote Agent可以通信，Client 负责制定任务和传递任务，remote agent 来执行。有几个关键能力如下：
- **Capability discovery:** Agents can advertise their capabilities **using an "Agent Card" in JSON format**, allowing the client agent to identify the best agent that can perform a task and leverage A2A to communicate with the remote agent.
- **Task management:** The communication between a client and remote agent is oriented towards task completion, in which agents work to fulfill end-user requests. **This "task" object is defined by the protocol and has a lifecycle.** It can be completed immediately or, for long-running tasks, each of the agents can communicate to stay in sync with each other on the latest status of completing a task. The output of a task is known as an "artifact."
- **Collaboration:** Agents can send each other messages to communicate context, replies, artifacts, or user instructions.
- **User experience negotiation:** Each message includes "parts," which is a fully formed piece of content, like a generated image. Each part has a specified content type, allowing client and remote agents to negotiate the correct format needed and explicitly include negotiations of the user's UI capabilities–e.g., iframes, video, web forms, and more.

# 一些关键细节

## 关键原则
Using A2A, agents accomplish tasks for end-users without sharing memory, thoughts, or tools. Instead the agents exchange context, status, instructions, and data in their native modalities.使用 A2A，代理无需共享内存、想法或工具即可为最终用户完成任务。相反，代理以其本机模式交换上下文、状态、指令和数据。
- Simple: Reuse existing standards 简单：重用现有标准
- Enterprise Ready: Auth, Security, Privacy, Tracing, MonitoringEnterprise Ready：身份验证、安全性、隐私、跟踪、监控
- Async First: (Very) Long running-tasks and human-in-the-loop异步优先：（非常）长时间运行的任务和人机协同
- Modality Agnostic: text, audio/video, forms, iframe, etc.模态不可知：文本、音频/视频、表单、iframe 等。
- Opaque Execution Agents do not have to share thoughts, plans, or tools.不透明执行代理不必分享想法、计划或工具。
## 角色卡
协议的复杂度好高啊，不愧是用来通信的。
```plaintext
// An AgentCard conveys key information:
// - Overall details (version, name, description, uses)
// - Skills: A set of capabilities the agent can perform
// - Default modalities/content types supported by the agent.
// - Authentication requirements
interface AgentCard {
  name: string;
  description: string;
  url: string;
  provider?: {
    organization: string;
    url: string;
  };
  version: string;
  documentationUrl?: string;
  capabilities: {
    streaming?: boolean;
    pushNotifications?: boolean;
    stateTransitionHistory?: boolean;
  };
  authentication: {
    schemes: string[];
    credentials?: string;
  };
  defaultInputModes: string[];
  defaultOutputModes: string[];
  skills: {
    id: string;
    name: string;
    description: string;
    tags: string[];
    examples?: string[];
    inputModes?: string[];
    outputModes?: string[];
  }[];
}
```


## A2A 和 MCP

- MCP 是 AGENT 与结构化工具链接的协议
- A2A 是 Agents 之间链接的协议
