# Copilot SDK 自定义 Agent 与子 Agent 编排

说明角色提示、工具范围、专属 Skills、事件监听和委派机制，并讨论研究与编辑协作及失败处理。

- 来源：GitHub／Copilot
- 资料类型：存档文档
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-08-20](<https://github.com/github/docs/commit/cf960fc42d0894ea600cb10548e681ab141acaf4>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/copilot-sdk/features/custom-agents.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/Copilot SDK 自定义 Agent 与子 Agent 编排.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Custom agents and sub-agent orchestration
shortTitle: Custom Agents
intro: >-
  Define specialized agents with scoped tools and prompts, then let Copilot
  orchestrate them as sub-agents within a single session. For dispatching
  multiple sub-agents in parallel, see [AUTOTITLE](/copilot/how-tos/copilot-sdk/features/fleet-mode).
versions:
  fpt: '*'
  ghec: '*'
redirect_from:
  - /copilot/how-tos/copilot-sdk/use-copilot-sdk/custom-agents
contentType: how-tos
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

<!-- markdownlint-disable GHD046 GHD005 -->
<!-- Suppressed: GHD046 (outdated release terminology), GHD005 (hardcoded data variable) -->

## 概述

定制 agents 重量轻 agent 您附加到会话的定义。每个 agent 有自己的系统提示、工具限制、可选 MCP 服务器。当用户的请求与 agent的专业知识， Copilot 运行时自动委托给 agent 作为 **子agent**- 在隔离的上下文中运行它，同时将生命周期事件流回父会话。

![图表：显示所描述过程的流程图。](<https://github.com/assets/images/help/copilot/copilot-sdk/features-custom-agents-diagram-0.png>)

|概念|描述|
|---------|-------------|
|**定制 agent**|一个名为 agent 的配置，带有自己的提示符和工具集|
|**子agent**|由运行时调用的自定义 agent 来处理部分任务|
|**推论**|运行时能够根据用户的意图自动选择 agent|
|**家长会**|产生子agent的会话；接收所有生命周期事件|

## 定义自定义 agents

创建会话时传递`customAgents`。每个agent至少需要一个`name`和`prompt`。

{% codetabs %}
{% codetab typescript %}

```typescript
import { CopilotClient } from "@github/copilot-sdk";

const client = new CopilotClient();
await client.start();

const session = await client.createSession({
    model: "gpt-5.4",
    customAgents: [
        {
            name: "researcher",
            displayName: "Research Agent",
            description: "Explores codebases and answers questions using read-only tools",
            tools: ["grep", "glob", "view"],
            prompt: "You are a research assistant. Analyze code and answer questions. Do not modify any files.",
        },
        {
            name: "editor",
            displayName: "Editor Agent",
            description: "Makes targeted code changes",
            tools: ["view", "edit", "bash"],
            prompt: "You are a code editor. Make minimal, surgical changes to files as requested.",
        },
    ],
    onPermissionRequest: async () => ({ kind: "approve-once" }),
});
```

{% endcodetab %}
{% codetab python %}

```python
from copilot import CopilotClient, PermissionDecisionApproveOnce

client = CopilotClient()
await client.start()

session = await client.create_session(
    on_permission_request=lambda req, inv: PermissionDecisionApproveOnce(),
    model="gpt-5.4",
    custom_agents=[
        {
            "name": "researcher",
            "display_name": "Research Agent",
            "description": "Explores codebases and answers questions using read-only tools",
            "tools": ["grep", "glob", "view"],
            "prompt": "You are a research assistant. Analyze code and answer questions. Do not modify any files.",
        },
        {
            "name": "editor",
            "display_name": "Editor Agent",
            "description": "Makes targeted code changes",
            "tools": ["view", "edit", "bash"],
            "prompt": "You are a code editor. Make minimal, surgical changes to files as requested.",
        },
    ],
)
```

{% endcodetab %}
{% codetab go %}

```golang
ctx := context.Background()
client := copilot.NewClient(nil)
client.Start(ctx)

session, _ := client.CreateSession(ctx, &copilot.SessionConfig{
    Model: "gpt-5.4",
    CustomAgents: []copilot.CustomAgentConfig{
        {
            Name:        "researcher",
            DisplayName: "Research Agent",
            Description: "Explores codebases and answers questions using read-only tools",
            Tools:       []string{"grep", "glob", "view"},
            Prompt:      "You are a research assistant. Analyze code and answer questions. Do not modify any files.",
        },
        {
            Name:        "editor",
            DisplayName: "Editor Agent",
            Description: "Makes targeted code changes",
            Tools:       []string{"view", "edit", "bash"},
            Prompt:      "You are a code editor. Make minimal, surgical changes to files as requested.",
        },
    },
    OnPermissionRequest: func(req copilot.PermissionRequest, inv copilot.PermissionInvocation) (rpc.PermissionDecision, error) {
        return &rpc.PermissionDecisionApproveOnce{}, nil
    },
})
```

{% endcodetab %}
{% codetab dotnet %}

```csharp
using GitHub.Copilot;
using GitHub.Copilot.Rpc;

await using var client = new CopilotClient();
await using var session = await client.CreateSessionAsync(new SessionConfig
{
    Model = "gpt-5.4",
    CustomAgents = new List<CustomAgentConfig>
    {
        new()
        {
            Name = "researcher",
            DisplayName = "Research Agent",
            Description = "Explores codebases and answers questions using read-only tools",
            Tools = new List<string> { "grep", "glob", "view" },
            Prompt = "You are a research assistant. Analyze code and answer questions. Do not modify any files.",
        },
        new()
        {
            Name = "editor",
            DisplayName = "Editor Agent",
            Description = "Makes targeted code changes",
            Tools = new List<string> { "view", "edit", "bash" },
            Prompt = "You are a code editor. Make minimal, surgical changes to files as requested.",
        },
    },
    OnPermissionRequest = (req, inv) =>
        Task.FromResult(PermissionDecision.ApproveOnce()),
});
```

{% endcodetab %}
{% codetab java %}

```java
import com.github.copilot.CopilotClient;
import com.github.copilot.rpc.*;
import java.util.List;

try (var client = new CopilotClient()) {
    client.start().get();

    var session = client.createSession(
        new SessionConfig()
            .setModel("gpt-5.4")
            .setCustomAgents(List.of(
                new CustomAgentConfig()
                    .setName("researcher")
                    .setDisplayName("Research Agent")
                    .setDescription("Explores codebases and answers questions using read-only tools")
                    .setTools(List.of("grep", "glob", "view"))
                    .setPrompt("You are a research assistant. Analyze code and answer questions. Do not modify any files."),
                new CustomAgentConfig()
                    .setName("editor")
                    .setDisplayName("Editor Agent")
                    .setDescription("Makes targeted code changes")
                    .setTools(List.of("view", "edit", "bash"))
                    .setPrompt("You are a code editor. Make minimal, surgical changes to files as requested.")
            ))
            .setOnPermissionRequest(PermissionHandler.APPROVE_ALL)
    ).get();
}
```

{% endcodetab %}
{% endcodetabs %}

## 配置参考

|财产|类型|必填|描述|
|----------|------|----------|-------------|
| `name` | `string` | ✅ |agent 的唯一标识符|
| `displayName` | `string` | |事件中显示的人类可读名称|
| `description` | `string` | |agent 的作用 - 帮助运行时选择它|
| `tools` |`string[]` 或 `null`| |agent 可以使用的工具名称。 `null` 或省略 = 所有工具|
| `prompt` | `string` | ✅ |系统提示agent|
| `mcpServers` | `object` | |MCP 特定于此 agent 的服务器配置|
| `infer` | `boolean` | |运行时是否可以自动选择此agent（默认：`true`）|
| `skills` | `string[]` | |启动时预加载到 agent 上下文中的技能名称|
| `model` | `string` | |agent 运行时使用的型号标识符|
| `reasoningEffort` | `string` | |在此 agent 运行时使用推理工作。省略时，SDK 不会发送每个 agent 覆盖，并且运行时会解决该问题（请参阅下面的注释）|

> [！TIP]
> 一个好的`description`可以帮助运行时将用户意图匹配到正确的agent。具体说明 agent 的专业知识和能力。

设置 `model` 和 `reasoningEffort` 以在自定义 agent 运行时覆盖父会话的模型设置。当省略 `reasoningEffort` 时，SDK 不会发送每个 agent 覆盖，并且运行时会根据自己的优先级解析工作：每个调用客户端选项、已解析模型的默认值或 agent 定义均优先；否则，仅当子代理运行与父级相同的模型时，运行时才会继承父级会话的工作。当子代理解析为不同的模型时，它会回退到该模型的默认值，而不是继承父代理的工作。 Python 使用 `reasoning_effort`，.NET 使用 `ReasoningEffort`，Go 使用 `ReasoningEffort`，Java 使用 `setReasoningEffort`，并且Rust 使用`with_reasoning_effort`。

除了上面的每个 agent 配置之外，您还可以在 ** 会话配置 ** 本身上设置 `agent`，以在会话启动时预先选择哪个自定义 agent 处于活动状态。请参阅下面的 [在会话创建 ](<#在会话创建时选择-agent>) 中选择 Agent。

|会话配置属性|类型|描述|
|-------------------------|------|-------------|
| `agent` | `string` |在会话创建时预先选择的自定义名称 agent。必须匹配 `customAgents` 中的 `name`。|

## 每-agent skills

您可以使用 `skills` 属性将技能预加载到 agent 的上下文中。指定后，每个列出的技能的 ** 完整内容 ** 会在启动时立即注入到 agent 的上下文中 - agent 不需要调用技能工具；说明已经存在。技能是**选择加入**：agents默认不接收技能，子agents不会从父级继承技能。技能名称是从会话级别 `skillDirectories` 解析的。

```typescript
const session = await client.createSession({
    skillDirectories: ["./skills"],
    customAgents: [
        {
            name: "security-auditor",
            description: "Security-focused code reviewer",
            prompt: "Focus on OWASP Top 10 vulnerabilities",
            skills: ["security-scan", "dependency-check"],
        },
        {
            name: "docs-writer",
            description: "Technical documentation writer",
            prompt: "Write clear, concise documentation",
            skills: ["markdown-lint"],
        },
    ],
    onPermissionRequest: async () => ({ kind: "approve-once" }),
});
```

在此示例中，`security-auditor` 以已注入其上下文的 `security-scan` 和 `dependency-check` 开头，而 `docs-writer` 以 `markdown-lint` 开头。没有 `skills` 字段的 agent 不会接收技能内容。

## 在会话创建时选择 agent

您可以在会话配置中传递 `agent` 来预先选择会话启动时应激活的自定义 agent。该值必须与 `customAgents` 中定义的 agents 之一的 `name` 匹配。

这相当于在创建后调用 `session.rpc.agent.select()`，但避免了额外的 API 调用，并确保 agent 从第一个提示开始就处于活动状态。

{% codetabs %}
{% codetab typescript %}

<!-- docs-validate: skip -->

```typescript
const session = await client.createSession({
    customAgents: [
        {
            name: "researcher",
            prompt: "You are a research assistant. Analyze code and answer questions.",
        },
        {
            name: "editor",
            prompt: "You are a code editor. Make minimal, surgical changes.",
        },
    ],
    agent: "researcher", // Pre-select the researcher agent
});
```

{% endcodetab %}
{% codetab python %}

<!-- docs-validate: skip -->

```python
session = await client.create_session(
    on_permission_request=PermissionHandler.approve_all,
    custom_agents=[
        {
            "name": "researcher",
            "prompt": "You are a research assistant. Analyze code and answer questions.",
        },
        {
            "name": "editor",
            "prompt": "You are a code editor. Make minimal, surgical changes.",
        },
    ],
    agent="researcher",  # Pre-select the researcher agent
)
```

{% endcodetab %}
{% codetab go %}

<!-- docs-validate: skip -->

```golang
session, _ := client.CreateSession(ctx, &copilot.SessionConfig{
    CustomAgents: []copilot.CustomAgentConfig{
        {
            Name:   "researcher",
            Prompt: "You are a research assistant. Analyze code and answer questions.",
        },
        {
            Name:   "editor",
            Prompt: "You are a code editor. Make minimal, surgical changes.",
        },
    },
    Agent: "researcher", // Pre-select the researcher agent
})
```

{% endcodetab %}
{% codetab dotnet %}

<!-- docs-validate: skip -->

```csharp
var session = await client.CreateSessionAsync(new SessionConfig
{
    CustomAgents = new List<CustomAgentConfig>
    {
        new() { Name = "researcher", Prompt = "You are a research assistant. Analyze code and answer questions." },
        new() { Name = "editor", Prompt = "You are a code editor. Make minimal, surgical changes." },
    },
    Agent = "researcher", // Pre-select the researcher agent
});
```

{% endcodetab %}
{% codetab java %}

<!-- docs-validate: skip -->

```java
import com.github.copilot.rpc.*;
import java.util.List;

var session = client.createSession(
    new SessionConfig()
        .setCustomAgents(List.of(
            new CustomAgentConfig()
                .setName("researcher")
                .setPrompt("You are a research assistant. Analyze code and answer questions."),
            new CustomAgentConfig()
                .setName("editor")
                .setPrompt("You are a code editor. Make minimal, surgical changes.")
        ))
        .setAgent("researcher") // Pre-select the researcher agent
        .setOnPermissionRequest(PermissionHandler.APPROVE_ALL)
).get();
```

{% endcodetab %}
{% endcodetabs %}

## 子agent委托如何运作

当您使用自定义 agents 向会话发送提示时，运行时会评估是否委托给子 agent：

1. **意图匹配** - 运行时根据每个agent的`name`和`description`分析用户的提示
1. **Agent 选择 ** - 如果找到匹配且 `infer` 不是 `false`，则运行时选择 agent
1. **隔离执行**—子agent使用自己的提示符和受限工具集运行
1. **事件流** - 生命周期事件（`subagent.started`、`subagent.completed` 等）流回父会话
1. **结果集成** - 子agent的输出合并到父agent的响应中

### 控制推理

默认情况下，所有自定义 agents 均可用于自动选择 (`infer: true`)。设置 `infer: false` 以防止运行时自动选择 agent — 对于您只想通过显式用户请求调用的 agents 很有用：

```typescript
{
    name: "dangerous-cleanup",
    description: "Deletes unused files and dead code",
    tools: ["bash", "edit", "view"],
    prompt: "You clean up codebases by removing dead code and unused files.",
    infer: false, // Only invoked when user explicitly asks for this agent
}
```

## 监听子agent事件

当子agent运行时，父会话会发出生命周期事件。订阅这些事件以构建可视化 agent 活动的 UI。

子 agent 发起的会话事件共享父会话流并包括信封级别 `agentId`。 Root/main agent 事件和会话级事件省略 `agentId`，因此渲染器可以通过检查事件信封将父响应与子 agent 跟踪分开。

### 事件类型

|活动|发出时间|数据|
|-------|-------------|------|
| `subagent.selected` |运行时为任务选择 agent| `agentName`, `agentDisplayName`, `tools` |
| `subagent.started` |子agent开始执行| `toolCallId`, `agentName`, `agentDisplayName`, `agentDescription`, `model?` |
| `subagent.completed` |子agent成功完成| `toolCallId`, `agentName`, `agentDisplayName`, `model?`, `durationMs?`, `totalTokens?`, `totalToolCalls?` |
| `subagent.failed` |子agent遇到错误| `toolCallId`, `agentName`, `agentDisplayName`, `error`, `model?`, `durationMs?`, `totalTokens?`, `totalToolCalls?` |
| `subagent.deselected` |运行时切换远离子agent|—|

### 订阅活动

{% codetabs %}
{% codetab typescript %}

```typescript
session.on((event) => {
    switch (event.type) {
        case "subagent.started":
            console.log(`▶ Sub-agent started: ${event.data.agentDisplayName}`);
            console.log(`  Description: ${event.data.agentDescription}`);
            console.log(`  Tool call ID: ${event.data.toolCallId}`);
            break;

        case "subagent.completed":
            console.log(`✅ Sub-agent completed: ${event.data.agentDisplayName}`);
            if (event.data.durationMs !== undefined) console.log(`  Duration: ${event.data.durationMs}ms`);
            if (event.data.totalTokens !== undefined) console.log(`  Tokens: ${event.data.totalTokens}`);
            if (event.data.totalToolCalls !== undefined) console.log(`  Tool calls: ${event.data.totalToolCalls}`);
            break;

        case "subagent.failed":
            console.log(`❌ Sub-agent failed: ${event.data.agentDisplayName}`);
            console.log(`  Error: ${event.data.error}`);
            if (event.data.durationMs !== undefined) console.log(`  Duration: ${event.data.durationMs}ms`);
            break;

        case "subagent.selected":
            console.log(`🎯 Agent selected: ${event.data.agentDisplayName}`);
            console.log(`  Tools: ${event.data.tools?.join(", ") ?? "all"}`);
            break;

        case "subagent.deselected":
            console.log("↩ Agent deselected, returning to parent");
            break;
    }
});

const response = await session.sendAndWait({
    prompt: "Research how authentication works in this codebase",
});
```

{% endcodetab %}
{% codetab python %}

```python
def handle_event(event):
    if event.type == "subagent.started":
        print(f"▶ Sub-agent started: {event.data.agent_display_name}")
        print(f"  Description: {event.data.agent_description}")
    elif event.type == "subagent.completed":
        print(f"✅ Sub-agent completed: {event.data.agent_display_name}")
    elif event.type == "subagent.failed":
        print(f"❌ Sub-agent failed: {event.data.agent_display_name}")
        print(f"  Error: {event.data.error}")
    elif event.type == "subagent.selected":
        tools = event.data.tools or "all"
        print(f"🎯 Agent selected: {event.data.agent_display_name} (tools: {tools})")

unsubscribe = session.on(handle_event)

response = await session.send_and_wait("Research how authentication works in this codebase")
```

{% endcodetab %}
{% codetab go %}

```golang
session.On(func(event copilot.SessionEvent) {
    switch d := event.Data.(type) {
    case *copilot.SubagentStartedData:
        fmt.Printf("▶ Sub-agent started: %s\n", d.AgentDisplayName)
        fmt.Printf("  Description: %s\n", d.AgentDescription)
        fmt.Printf("  Tool call ID: %s\n", d.ToolCallID)
    case *copilot.SubagentCompletedData:
        fmt.Printf("✅ Sub-agent completed: %s\n", d.AgentDisplayName)
    case *copilot.SubagentFailedData:
        fmt.Printf("❌ Sub-agent failed: %s — %v\n", d.AgentDisplayName, d.Error)
    case *copilot.SubagentSelectedData:
        fmt.Printf("🎯 Agent selected: %s\n", d.AgentDisplayName)
    }
})

_, err := session.SendAndWait(ctx, copilot.MessageOptions{
    Prompt: "Research how authentication works in this codebase",
})
```

{% endcodetab %}
{% codetab dotnet %}

```csharp
using var subscription = session.On<SessionEvent>(evt =>
{
    switch (evt)
    {
        case SubagentStartedEvent started:
            Console.WriteLine($"▶ Sub-agent started: {started.Data.AgentDisplayName}");
            Console.WriteLine($"  Description: {started.Data.AgentDescription}");
            Console.WriteLine($"  Tool call ID: {started.Data.ToolCallId}");
            break;
        case SubagentCompletedEvent completed:
            Console.WriteLine($"✅ Sub-agent completed: {completed.Data.AgentDisplayName}");
            break;
        case SubagentFailedEvent failed:
            Console.WriteLine($"❌ Sub-agent failed: {failed.Data.AgentDisplayName} — {failed.Data.Error}");
            break;
        case SubagentSelectedEvent selected:
            Console.WriteLine($"🎯 Agent selected: {selected.Data.AgentDisplayName}");
            break;
    }
});

await session.SendAndWaitAsync(new MessageOptions
{
    Prompt = "Research how authentication works in this codebase"
});
```

{% endcodetab %}
{% codetab java %}

<!-- docs-validate: skip -->

```java
session.on(event -> {
    if (event instanceof SubagentStartedEvent e) {
        System.out.println("▶ Sub-agent started: " + e.getData().agentDisplayName());
        System.out.println("  Description: " + e.getData().agentDescription());
        System.out.println("  Tool call ID: " + e.getData().toolCallId());
    } else if (event instanceof SubagentCompletedEvent e) {
        System.out.println("✅ Sub-agent completed: " + e.getData().agentName());
    } else if (event instanceof SubagentFailedEvent e) {
        System.out.println("❌ Sub-agent failed: " + e.getData().agentName());
        System.out.println("  Error: " + e.getData().error());
    } else if (event instanceof SubagentSelectedEvent e) {
        System.out.println("🎯 Agent selected: " + e.getData().agentDisplayName());
    } else if (event instanceof SubagentDeselectedEvent e) {
        System.out.println("↩ Agent deselected, returning to parent");
    }
});

var response = session.sendAndWait(
    new MessageOptions().setPrompt("Research how authentication works in this codebase")
).get();
```

{% endcodetab %}
{% endcodetabs %}

## 构建一棵 agent 树 UI

子 agent 事件包括 `toolCallId` 字段，可让您重建执行树。以下是跟踪 agent 活动的模式：

```typescript
interface AgentNode {
    toolCallId: string;
    name: string;
    displayName: string;
    status: "running" | "completed" | "failed";
    error?: string;
    startedAt: Date;
    completedAt?: Date;
}

const agentTree = new Map<string, AgentNode>();

session.on((event) => {
    if (event.type === "subagent.started") {
        agentTree.set(event.data.toolCallId, {
            toolCallId: event.data.toolCallId,
            name: event.data.agentName,
            displayName: event.data.agentDisplayName,
            status: "running",
            startedAt: new Date(event.timestamp),
        });
    }

    if (event.type === "subagent.completed") {
        const node = agentTree.get(event.data.toolCallId);
        if (node) {
            node.status = "completed";
            node.completedAt = new Date(event.timestamp);
        }
    }

    if (event.type === "subagent.failed") {
        const node = agentTree.get(event.data.toolCallId);
        if (node) {
            node.status = "failed";
            node.error = event.data.error;
            node.completedAt = new Date(event.timestamp);
        }
    }

    // Render your UI with the updated tree
    renderAgentTree(agentTree);
});
```

## 根据 agent 确定范围的工具

使用 `tools` 属性来限制 agent 可以访问哪些工具。这对于安全和保持 agents 的关注至关重要：

```typescript
const session = await client.createSession({
    customAgents: [
        {
            name: "reader",
            description: "Read-only exploration of the codebase",
            tools: ["grep", "glob", "view"],  // No write access
            prompt: "You explore and analyze code. Never suggest modifications directly.",
        },
        {
            name: "writer",
            description: "Makes code changes",
            tools: ["view", "edit", "bash"],   // Write access
            prompt: "You make precise code changes as instructed.",
        },
        {
            name: "unrestricted",
            description: "Full access agent for complex tasks",
            tools: null,                        // All tools available
            prompt: "You handle complex multi-step tasks using any available tools.",
        },
    ],
});
```

> [!NOTE]
> 当 `tools` 为 `null` 或省略时，agent 继承对会话上配置的所有工具的访问权限。使用显式工具列表来强制执行最小权限原则。

## Agent-专属工具

使用会话配置上的 `defaultAgent` 属性可隐藏默认 agent 中的特定工具（在未选择自定义 agent 时处理轮流的内置 agent）。当需要这些工具的功能时，这会迫使主 agent 委托给子 agents，从而保持主 agent 的上下文干净。

这在以下情况下很有用：
* 某些工具会生成大量上下文，从而压倒主要的内容 agent
* 您希望主 agent 充当协调者，将繁重的工作委派给专门的子 agents
* 您需要严格区分编排和执行

{% codetabs %}
{% codetab typescript %}

```typescript
import { CopilotClient, defineTool, approveAll } from "@github/copilot-sdk";
import { z } from "zod";

const heavyContextTool = defineTool("analyze-codebase", {
    description: "Performs deep analysis of the codebase, generating extensive context",
    parameters: z.object({ query: z.string() }),
    handler: async ({ query }) => {
        // ... expensive analysis that returns lots of data
        return { analysis: "..." };
    },
});

const session = await client.createSession({
    tools: [heavyContextTool],
    defaultAgent: {
        excludedTools: ["analyze-codebase"],
    },
    customAgents: [
        {
            name: "researcher",
            description: "Deep codebase analysis agent with access to heavy-context tools",
            tools: ["analyze-codebase"],
            prompt: "You perform thorough codebase analysis using the analyze-codebase tool.",
        },
    ],
});
```

{% endcodetab %}
{% codetab python %}

```python
from copilot import CopilotClient
from copilot.tools import Tool

heavy_tool = Tool(
    name="analyze-codebase",
    description="Performs deep analysis of the codebase",
    handler=analyze_handler,
    parameters={"type": "object", "properties": {"query": {"type": "string"}}},
)

session = await client.create_session(
    tools=[heavy_tool],
    default_agent={"excluded_tools": ["analyze-codebase"]},
    custom_agents=[
        {
            "name": "researcher",
            "description": "Deep codebase analysis agent",
            "tools": ["analyze-codebase"],
            "prompt": "You perform thorough codebase analysis.",
        },
    ],
    on_permission_request=approve_all,
)
```

{% endcodetab %}
{% codetab go %}

<!-- docs-validate: skip -->

```golang
session, err := client.CreateSession(ctx, &copilot.SessionConfig{
    Tools: []copilot.Tool{heavyTool},
    DefaultAgent: &copilot.DefaultAgentConfig{
        ExcludedTools: []string{"analyze-codebase"},
    },
    CustomAgents: []copilot.CustomAgentConfig{
        {
            Name:        "researcher",
            Description: "Deep codebase analysis agent",
            Tools:       []string{"analyze-codebase"},
            Prompt:      "You perform thorough codebase analysis.",
        },
    },
})
```

{% endcodetab %}
{% codetab csharp %}

<!-- docs-validate: skip -->

```csharp
var session = await client.CreateSessionAsync(new SessionConfig
{
    Tools = [analyzeCodebaseTool],
    DefaultAgent = new DefaultAgentConfig
    {
        ExcludedTools = ["analyze-codebase"],
    },
    CustomAgents =
    [
        new CustomAgentConfig
        {
            Name = "researcher",
            Description = "Deep codebase analysis agent",
            Tools = ["analyze-codebase"],
            Prompt = "You perform thorough codebase analysis.",
        },
    ],
});
```

{% endcodetab %}
{% endcodetabs %}

### 它是如何运作的

`defaultAgent.excludedTools`中列出的工具：

1. **已注册** - 它们的处理程序可供执行
1. **从主agent的工具列表中隐藏** - LLM不会直接看到或调用它们
1. **对任何将其包含在其 `tools` 数组中的自定义子 agent 保持可用 **

### 与其他工具过滤器的交互

`defaultAgent.excludedTools` 与会话级 `availableTools` 和 `excludedTools` 正交：

|过滤器|适用范围|效果|
|--------|-------|--------|
| `availableTools` |全会话范围|许可名单——只有这些工具适用于任何人|
| `excludedTools` |全会话范围|阻止列表——这些工具对所有人都被阻止|
| `defaultAgent.excludedTools` |仅主agent|这些工具对主 agent 隐藏，但可用于子 agents|

优先级：
1. 首先应用会话级 `availableTools`/`excludedTools`（全局）
1. `defaultAgent.excludedTools` 应用在顶部，进一步限制仅主 agent

> [!NOTE]
> 如果某个工具同时位于 `excludedTools`（会话级）和 `defaultAgent.excludedTools` 中，则会话级排除优先 — 该工具不适用于所有人。

## 将 MCP 服务器连接到 agents

每个自定义 agent 都可以拥有自己的 MCP (Model Context Protocol) 服务器，使其能够访问专门的数据源：

```typescript
const session = await client.createSession({
    customAgents: [
        {
            name: "db-analyst",
            description: "Analyzes database schemas and queries",
            prompt: "You are a database expert. Use the database MCP server to analyze schemas.",
            mcpServers: {
                "database": {
                    command: "npx",
                    args: ["-y", "@modelcontextprotocol/server-postgres", "postgresql://localhost/mydb"],
                },
            },
        },
    ],
});
```

## 模式和最佳实践

### 将研究员与编辑配对

常见的模式是定义一个只读研究人员 agent 和一个可写编辑器 agent。运行时将探索任务委托给研究人员，将修改任务委托给编辑人员：

```typescript
customAgents: [
    {
        name: "researcher",
        description: "Analyzes code structure, finds patterns, and answers questions",
        tools: ["grep", "glob", "view"],
        prompt: "You are a code analyst. Thoroughly explore the codebase to answer questions.",
    },
    {
        name: "implementer",
        description: "Implements code changes based on analysis",
        tools: ["view", "edit", "bash"],
        prompt: "You make minimal, targeted code changes. Always verify changes compile.",
    },
]
```

### 保持 agent 描述具体

运行时使用 `description` 来匹配用户意图。模糊的描述会导致糟糕的授权：

```typescript
// ❌ Too vague — runtime can't distinguish from other agents
{ description: "Helps with code" }

// ✅ Specific — runtime knows when to delegate
{ description: "Analyzes Python test coverage and identifies untested code paths" }
```

### 优雅地处理失败

子agents可能会失败。始终侦听 `subagent.failed` 事件并在应用程序中处理它们：

```typescript
session.on((event) => {
    if (event.type === "subagent.failed") {
        logger.error(`Agent ${event.data.agentName} failed: ${event.data.error}`);
        // Show error in UI, retry, or fall back to parent agent
    }
});
```


## 原文对照

<details>
<summary>展开完整原文</summary>

<!-- markdownlint-disable GHD046 GHD005 -->
<!-- Suppressed: GHD046 (outdated release terminology), GHD005 (hardcoded data variable) -->

## Overview

Custom agents are lightweight agent definitions you attach to a session. Each agent has its own system prompt, tool restrictions, and optional MCP servers. When a user's request matches an agent's expertise, the Copilot runtime automatically delegates to that agent as a **sub-agent**—running it in an isolated context while streaming lifecycle events back to the parent session.

![Diagram: Flowchart showing the described process.](<https://github.com/assets/images/help/copilot/copilot-sdk/features-custom-agents-diagram-0.png>)

| Concept | Description |
|---------|-------------|
| **Custom agent** | A named agent config with its own prompt and tool set |
| **Sub-agent** | A custom agent invoked by the runtime to handle part of a task |
| **Inference** | The runtime's ability to auto-select an agent based on the user's intent |
| **Parent session** | The session that spawned the sub-agent; receives all lifecycle events |

## Defining custom agents

Pass `customAgents` when creating a session. Each agent needs at minimum a `name` and `prompt`.

{% codetabs %}
{% codetab typescript %}

```typescript
import { CopilotClient } from "@github/copilot-sdk";

const client = new CopilotClient();
await client.start();

const session = await client.createSession({
    model: "gpt-5.4",
    customAgents: [
        {
            name: "researcher",
            displayName: "Research Agent",
            description: "Explores codebases and answers questions using read-only tools",
            tools: ["grep", "glob", "view"],
            prompt: "You are a research assistant. Analyze code and answer questions. Do not modify any files.",
        },
        {
            name: "editor",
            displayName: "Editor Agent",
            description: "Makes targeted code changes",
            tools: ["view", "edit", "bash"],
            prompt: "You are a code editor. Make minimal, surgical changes to files as requested.",
        },
    ],
    onPermissionRequest: async () => ({ kind: "approve-once" }),
});
```

{% endcodetab %}
{% codetab python %}

```python
from copilot import CopilotClient, PermissionDecisionApproveOnce

client = CopilotClient()
await client.start()

session = await client.create_session(
    on_permission_request=lambda req, inv: PermissionDecisionApproveOnce(),
    model="gpt-5.4",
    custom_agents=[
        {
            "name": "researcher",
            "display_name": "Research Agent",
            "description": "Explores codebases and answers questions using read-only tools",
            "tools": ["grep", "glob", "view"],
            "prompt": "You are a research assistant. Analyze code and answer questions. Do not modify any files.",
        },
        {
            "name": "editor",
            "display_name": "Editor Agent",
            "description": "Makes targeted code changes",
            "tools": ["view", "edit", "bash"],
            "prompt": "You are a code editor. Make minimal, surgical changes to files as requested.",
        },
    ],
)
```

{% endcodetab %}
{% codetab go %}

```golang
ctx := context.Background()
client := copilot.NewClient(nil)
client.Start(ctx)

session, _ := client.CreateSession(ctx, &copilot.SessionConfig{
    Model: "gpt-5.4",
    CustomAgents: []copilot.CustomAgentConfig{
        {
            Name:        "researcher",
            DisplayName: "Research Agent",
            Description: "Explores codebases and answers questions using read-only tools",
            Tools:       []string{"grep", "glob", "view"},
            Prompt:      "You are a research assistant. Analyze code and answer questions. Do not modify any files.",
        },
        {
            Name:        "editor",
            DisplayName: "Editor Agent",
            Description: "Makes targeted code changes",
            Tools:       []string{"view", "edit", "bash"},
            Prompt:      "You are a code editor. Make minimal, surgical changes to files as requested.",
        },
    },
    OnPermissionRequest: func(req copilot.PermissionRequest, inv copilot.PermissionInvocation) (rpc.PermissionDecision, error) {
        return &rpc.PermissionDecisionApproveOnce{}, nil
    },
})
```

{% endcodetab %}
{% codetab dotnet %}

```csharp
using GitHub.Copilot;
using GitHub.Copilot.Rpc;

await using var client = new CopilotClient();
await using var session = await client.CreateSessionAsync(new SessionConfig
{
    Model = "gpt-5.4",
    CustomAgents = new List<CustomAgentConfig>
    {
        new()
        {
            Name = "researcher",
            DisplayName = "Research Agent",
            Description = "Explores codebases and answers questions using read-only tools",
            Tools = new List<string> { "grep", "glob", "view" },
            Prompt = "You are a research assistant. Analyze code and answer questions. Do not modify any files.",
        },
        new()
        {
            Name = "editor",
            DisplayName = "Editor Agent",
            Description = "Makes targeted code changes",
            Tools = new List<string> { "view", "edit", "bash" },
            Prompt = "You are a code editor. Make minimal, surgical changes to files as requested.",
        },
    },
    OnPermissionRequest = (req, inv) =>
        Task.FromResult(PermissionDecision.ApproveOnce()),
});
```

{% endcodetab %}
{% codetab java %}

```java
import com.github.copilot.CopilotClient;
import com.github.copilot.rpc.*;
import java.util.List;

try (var client = new CopilotClient()) {
    client.start().get();

    var session = client.createSession(
        new SessionConfig()
            .setModel("gpt-5.4")
            .setCustomAgents(List.of(
                new CustomAgentConfig()
                    .setName("researcher")
                    .setDisplayName("Research Agent")
                    .setDescription("Explores codebases and answers questions using read-only tools")
                    .setTools(List.of("grep", "glob", "view"))
                    .setPrompt("You are a research assistant. Analyze code and answer questions. Do not modify any files."),
                new CustomAgentConfig()
                    .setName("editor")
                    .setDisplayName("Editor Agent")
                    .setDescription("Makes targeted code changes")
                    .setTools(List.of("view", "edit", "bash"))
                    .setPrompt("You are a code editor. Make minimal, surgical changes to files as requested.")
            ))
            .setOnPermissionRequest(PermissionHandler.APPROVE_ALL)
    ).get();
}
```

{% endcodetab %}
{% endcodetabs %}

## Configuration reference

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `name` | `string` | ✅ | Unique identifier for the agent |
| `displayName` | `string` | | Human-readable name shown in events |
| `description` | `string` | | What the agent does—helps the runtime select it |
| `tools` | `string[]` or `null` | | Tool names the agent can use. `null` or omitted = all tools |
| `prompt` | `string` | ✅ | System prompt for the agent |
| `mcpServers` | `object` | | MCP server configurations specific to this agent |
| `infer` | `boolean` | | Whether the runtime can auto-select this agent (default: `true`) |
| `skills` | `string[]` | | Skill names to preload into the agent's context at startup |
| `model` | `string` | | Model identifier to use while this agent runs |
| `reasoningEffort` | `string` | | Reasoning effort to use while this agent runs. When omitted, the SDK sends no per-agent override and the runtime resolves the effort (see note below) |

> [!TIP]
> A good `description` helps the runtime match user intent to the right agent. Be specific about the agent's expertise and capabilities.

Set `model` and `reasoningEffort` to override the parent session's model settings while a custom agent runs. When `reasoningEffort` is omitted, the SDK sends no per-agent override and the runtime resolves the effort from its own precedence: a per-call client option, the resolved model's default, or the agent definition all take priority; otherwise the runtime inherits the parent session's effort only when the subagent runs the same model as the parent. When the subagent resolves to a different model, it falls back to that model's default instead of inheriting the parent's effort. Python uses `reasoning_effort`, .NET uses `ReasoningEffort`, Go uses `ReasoningEffort`, Java uses `setReasoningEffort`, and Rust uses `with_reasoning_effort`.

In addition to per-agent configuration above, you can set `agent` on the **session config** itself to pre-select which custom agent is active when the session starts. See [Selecting an Agent at Session Creation](<#selecting-an-agent-at-session-creation>) below.

| Session Config Property | Type | Description |
|-------------------------|------|-------------|
| `agent` | `string` | Name of the custom agent to pre-select at session creation. Must match a `name` in `customAgents`. |

## Per-agent skills

You can preload skills into an agent's context using the `skills` property. When specified, the **full content** of each listed skill is eagerly injected into the agent's context at startup—the agent doesn't need to invoke a skill tool; the instructions are already present. Skills are **opt-in**: agents receive no skills by default, and sub-agents do not inherit skills from the parent. Skill names are resolved from the session-level `skillDirectories`.

```typescript
const session = await client.createSession({
    skillDirectories: ["./skills"],
    customAgents: [
        {
            name: "security-auditor",
            description: "Security-focused code reviewer",
            prompt: "Focus on OWASP Top 10 vulnerabilities",
            skills: ["security-scan", "dependency-check"],
        },
        {
            name: "docs-writer",
            description: "Technical documentation writer",
            prompt: "Write clear, concise documentation",
            skills: ["markdown-lint"],
        },
    ],
    onPermissionRequest: async () => ({ kind: "approve-once" }),
});
```

In this example, `security-auditor` starts with `security-scan` and `dependency-check` already injected into its context, while `docs-writer` starts with `markdown-lint`. An agent without a `skills` field receives no skill content.

## Selecting an agent at session creation

You can pass `agent` in the session config to pre-select which custom agent should be active when the session starts. The value must match the `name` of one of the agents defined in `customAgents`.

This is equivalent to calling `session.rpc.agent.select()` after creation, but avoids the extra API call and ensures the agent is active from the very first prompt.

{% codetabs %}
{% codetab typescript %}

<!-- docs-validate: skip -->

```typescript
const session = await client.createSession({
    customAgents: [
        {
            name: "researcher",
            prompt: "You are a research assistant. Analyze code and answer questions.",
        },
        {
            name: "editor",
            prompt: "You are a code editor. Make minimal, surgical changes.",
        },
    ],
    agent: "researcher", // Pre-select the researcher agent
});
```

{% endcodetab %}
{% codetab python %}

<!-- docs-validate: skip -->

```python
session = await client.create_session(
    on_permission_request=PermissionHandler.approve_all,
    custom_agents=[
        {
            "name": "researcher",
            "prompt": "You are a research assistant. Analyze code and answer questions.",
        },
        {
            "name": "editor",
            "prompt": "You are a code editor. Make minimal, surgical changes.",
        },
    ],
    agent="researcher",  # Pre-select the researcher agent
)
```

{% endcodetab %}
{% codetab go %}

<!-- docs-validate: skip -->

```golang
session, _ := client.CreateSession(ctx, &copilot.SessionConfig{
    CustomAgents: []copilot.CustomAgentConfig{
        {
            Name:   "researcher",
            Prompt: "You are a research assistant. Analyze code and answer questions.",
        },
        {
            Name:   "editor",
            Prompt: "You are a code editor. Make minimal, surgical changes.",
        },
    },
    Agent: "researcher", // Pre-select the researcher agent
})
```

{% endcodetab %}
{% codetab dotnet %}

<!-- docs-validate: skip -->

```csharp
var session = await client.CreateSessionAsync(new SessionConfig
{
    CustomAgents = new List<CustomAgentConfig>
    {
        new() { Name = "researcher", Prompt = "You are a research assistant. Analyze code and answer questions." },
        new() { Name = "editor", Prompt = "You are a code editor. Make minimal, surgical changes." },
    },
    Agent = "researcher", // Pre-select the researcher agent
});
```

{% endcodetab %}
{% codetab java %}

<!-- docs-validate: skip -->

```java
import com.github.copilot.rpc.*;
import java.util.List;

var session = client.createSession(
    new SessionConfig()
        .setCustomAgents(List.of(
            new CustomAgentConfig()
                .setName("researcher")
                .setPrompt("You are a research assistant. Analyze code and answer questions."),
            new CustomAgentConfig()
                .setName("editor")
                .setPrompt("You are a code editor. Make minimal, surgical changes.")
        ))
        .setAgent("researcher") // Pre-select the researcher agent
        .setOnPermissionRequest(PermissionHandler.APPROVE_ALL)
).get();
```

{% endcodetab %}
{% endcodetabs %}

## How sub-agent delegation works

When you send a prompt to a session with custom agents, the runtime evaluates whether to delegate to a sub-agent:

1. **Intent matching**—The runtime analyzes the user's prompt against each agent's `name` and `description`
1. **Agent selection**—If a match is found and `infer` is not `false`, the runtime selects the agent
1. **Isolated execution**—The sub-agent runs with its own prompt and restricted tool set
1. **Event streaming**—Lifecycle events (`subagent.started`, `subagent.completed`, etc.) stream back to the parent session
1. **Result integration**—The sub-agent's output is incorporated into the parent agent's response

### Controlling inference

By default, all custom agents are available for automatic selection (`infer: true`). Set `infer: false` to prevent the runtime from auto-selecting an agent—useful for agents you only want invoked through explicit user requests:

```typescript
{
    name: "dangerous-cleanup",
    description: "Deletes unused files and dead code",
    tools: ["bash", "edit", "view"],
    prompt: "You clean up codebases by removing dead code and unused files.",
    infer: false, // Only invoked when user explicitly asks for this agent
}
```

## Listening to sub-agent events

When a sub-agent runs, the parent session emits lifecycle events. Subscribe to these events to build UIs that visualize agent activity.

Sub-agent-originated session events share the parent session stream and include envelope-level `agentId`. Root/main agent events and session-level events omit `agentId`, so renderers can keep the parent response separate from sub-agent traces by checking the event envelope.

### Event types

| Event | Emitted when | Data |
|-------|-------------|------|
| `subagent.selected` | Runtime selects an agent for the task | `agentName`, `agentDisplayName`, `tools` |
| `subagent.started` | Sub-agent begins execution | `toolCallId`, `agentName`, `agentDisplayName`, `agentDescription`, `model?` |
| `subagent.completed` | Sub-agent finishes successfully | `toolCallId`, `agentName`, `agentDisplayName`, `model?`, `durationMs?`, `totalTokens?`, `totalToolCalls?` |
| `subagent.failed` | Sub-agent encounters an error | `toolCallId`, `agentName`, `agentDisplayName`, `error`, `model?`, `durationMs?`, `totalTokens?`, `totalToolCalls?` |
| `subagent.deselected` | Runtime switches away from the sub-agent |—|

### Subscribing to events

{% codetabs %}
{% codetab typescript %}

```typescript
session.on((event) => {
    switch (event.type) {
        case "subagent.started":
            console.log(`▶ Sub-agent started: ${event.data.agentDisplayName}`);
            console.log(`  Description: ${event.data.agentDescription}`);
            console.log(`  Tool call ID: ${event.data.toolCallId}`);
            break;

        case "subagent.completed":
            console.log(`✅ Sub-agent completed: ${event.data.agentDisplayName}`);
            if (event.data.durationMs !== undefined) console.log(`  Duration: ${event.data.durationMs}ms`);
            if (event.data.totalTokens !== undefined) console.log(`  Tokens: ${event.data.totalTokens}`);
            if (event.data.totalToolCalls !== undefined) console.log(`  Tool calls: ${event.data.totalToolCalls}`);
            break;

        case "subagent.failed":
            console.log(`❌ Sub-agent failed: ${event.data.agentDisplayName}`);
            console.log(`  Error: ${event.data.error}`);
            if (event.data.durationMs !== undefined) console.log(`  Duration: ${event.data.durationMs}ms`);
            break;

        case "subagent.selected":
            console.log(`🎯 Agent selected: ${event.data.agentDisplayName}`);
            console.log(`  Tools: ${event.data.tools?.join(", ") ?? "all"}`);
            break;

        case "subagent.deselected":
            console.log("↩ Agent deselected, returning to parent");
            break;
    }
});

const response = await session.sendAndWait({
    prompt: "Research how authentication works in this codebase",
});
```

{% endcodetab %}
{% codetab python %}

```python
def handle_event(event):
    if event.type == "subagent.started":
        print(f"▶ Sub-agent started: {event.data.agent_display_name}")
        print(f"  Description: {event.data.agent_description}")
    elif event.type == "subagent.completed":
        print(f"✅ Sub-agent completed: {event.data.agent_display_name}")
    elif event.type == "subagent.failed":
        print(f"❌ Sub-agent failed: {event.data.agent_display_name}")
        print(f"  Error: {event.data.error}")
    elif event.type == "subagent.selected":
        tools = event.data.tools or "all"
        print(f"🎯 Agent selected: {event.data.agent_display_name} (tools: {tools})")

unsubscribe = session.on(handle_event)

response = await session.send_and_wait("Research how authentication works in this codebase")
```

{% endcodetab %}
{% codetab go %}

```golang
session.On(func(event copilot.SessionEvent) {
    switch d := event.Data.(type) {
    case *copilot.SubagentStartedData:
        fmt.Printf("▶ Sub-agent started: %s\n", d.AgentDisplayName)
        fmt.Printf("  Description: %s\n", d.AgentDescription)
        fmt.Printf("  Tool call ID: %s\n", d.ToolCallID)
    case *copilot.SubagentCompletedData:
        fmt.Printf("✅ Sub-agent completed: %s\n", d.AgentDisplayName)
    case *copilot.SubagentFailedData:
        fmt.Printf("❌ Sub-agent failed: %s — %v\n", d.AgentDisplayName, d.Error)
    case *copilot.SubagentSelectedData:
        fmt.Printf("🎯 Agent selected: %s\n", d.AgentDisplayName)
    }
})

_, err := session.SendAndWait(ctx, copilot.MessageOptions{
    Prompt: "Research how authentication works in this codebase",
})
```

{% endcodetab %}
{% codetab dotnet %}

```csharp
using var subscription = session.On<SessionEvent>(evt =>
{
    switch (evt)
    {
        case SubagentStartedEvent started:
            Console.WriteLine($"▶ Sub-agent started: {started.Data.AgentDisplayName}");
            Console.WriteLine($"  Description: {started.Data.AgentDescription}");
            Console.WriteLine($"  Tool call ID: {started.Data.ToolCallId}");
            break;
        case SubagentCompletedEvent completed:
            Console.WriteLine($"✅ Sub-agent completed: {completed.Data.AgentDisplayName}");
            break;
        case SubagentFailedEvent failed:
            Console.WriteLine($"❌ Sub-agent failed: {failed.Data.AgentDisplayName} — {failed.Data.Error}");
            break;
        case SubagentSelectedEvent selected:
            Console.WriteLine($"🎯 Agent selected: {selected.Data.AgentDisplayName}");
            break;
    }
});

await session.SendAndWaitAsync(new MessageOptions
{
    Prompt = "Research how authentication works in this codebase"
});
```

{% endcodetab %}
{% codetab java %}

<!-- docs-validate: skip -->

```java
session.on(event -> {
    if (event instanceof SubagentStartedEvent e) {
        System.out.println("▶ Sub-agent started: " + e.getData().agentDisplayName());
        System.out.println("  Description: " + e.getData().agentDescription());
        System.out.println("  Tool call ID: " + e.getData().toolCallId());
    } else if (event instanceof SubagentCompletedEvent e) {
        System.out.println("✅ Sub-agent completed: " + e.getData().agentName());
    } else if (event instanceof SubagentFailedEvent e) {
        System.out.println("❌ Sub-agent failed: " + e.getData().agentName());
        System.out.println("  Error: " + e.getData().error());
    } else if (event instanceof SubagentSelectedEvent e) {
        System.out.println("🎯 Agent selected: " + e.getData().agentDisplayName());
    } else if (event instanceof SubagentDeselectedEvent e) {
        System.out.println("↩ Agent deselected, returning to parent");
    }
});

var response = session.sendAndWait(
    new MessageOptions().setPrompt("Research how authentication works in this codebase")
).get();
```

{% endcodetab %}
{% endcodetabs %}

## Building an agent tree UI

Sub-agent events include `toolCallId` fields that let you reconstruct the execution tree. Here's a pattern for tracking agent activity:

```typescript
interface AgentNode {
    toolCallId: string;
    name: string;
    displayName: string;
    status: "running" | "completed" | "failed";
    error?: string;
    startedAt: Date;
    completedAt?: Date;
}

const agentTree = new Map<string, AgentNode>();

session.on((event) => {
    if (event.type === "subagent.started") {
        agentTree.set(event.data.toolCallId, {
            toolCallId: event.data.toolCallId,
            name: event.data.agentName,
            displayName: event.data.agentDisplayName,
            status: "running",
            startedAt: new Date(event.timestamp),
        });
    }

    if (event.type === "subagent.completed") {
        const node = agentTree.get(event.data.toolCallId);
        if (node) {
            node.status = "completed";
            node.completedAt = new Date(event.timestamp);
        }
    }

    if (event.type === "subagent.failed") {
        const node = agentTree.get(event.data.toolCallId);
        if (node) {
            node.status = "failed";
            node.error = event.data.error;
            node.completedAt = new Date(event.timestamp);
        }
    }

    // Render your UI with the updated tree
    renderAgentTree(agentTree);
});
```

## Scoping tools per agent

Use the `tools` property to restrict which tools an agent can access. This is essential for security and for keeping agents focused:

```typescript
const session = await client.createSession({
    customAgents: [
        {
            name: "reader",
            description: "Read-only exploration of the codebase",
            tools: ["grep", "glob", "view"],  // No write access
            prompt: "You explore and analyze code. Never suggest modifications directly.",
        },
        {
            name: "writer",
            description: "Makes code changes",
            tools: ["view", "edit", "bash"],   // Write access
            prompt: "You make precise code changes as instructed.",
        },
        {
            name: "unrestricted",
            description: "Full access agent for complex tasks",
            tools: null,                        // All tools available
            prompt: "You handle complex multi-step tasks using any available tools.",
        },
    ],
});
```

> [!NOTE]
> When `tools` is `null` or omitted, the agent inherits access to all tools configured on the session. Use explicit tool lists to enforce the principle of least privilege.

## Agent-exclusive tools

Use the `defaultAgent` property on the session configuration to hide specific tools from the default agent (the built-in agent that handles turns when no custom agent is selected). This forces the main agent to delegate to sub-agents when those tools' capabilities are needed, keeping the main agent's context clean.

This is useful when:
* Certain tools generate large amounts of context that would overwhelm the main agent
* You want the main agent to act as an orchestrator, delegating heavy work to specialized sub-agents
* You need strict separation between orchestration and execution

{% codetabs %}
{% codetab typescript %}

```typescript
import { CopilotClient, defineTool, approveAll } from "@github/copilot-sdk";
import { z } from "zod";

const heavyContextTool = defineTool("analyze-codebase", {
    description: "Performs deep analysis of the codebase, generating extensive context",
    parameters: z.object({ query: z.string() }),
    handler: async ({ query }) => {
        // ... expensive analysis that returns lots of data
        return { analysis: "..." };
    },
});

const session = await client.createSession({
    tools: [heavyContextTool],
    defaultAgent: {
        excludedTools: ["analyze-codebase"],
    },
    customAgents: [
        {
            name: "researcher",
            description: "Deep codebase analysis agent with access to heavy-context tools",
            tools: ["analyze-codebase"],
            prompt: "You perform thorough codebase analysis using the analyze-codebase tool.",
        },
    ],
});
```

{% endcodetab %}
{% codetab python %}

```python
from copilot import CopilotClient
from copilot.tools import Tool

heavy_tool = Tool(
    name="analyze-codebase",
    description="Performs deep analysis of the codebase",
    handler=analyze_handler,
    parameters={"type": "object", "properties": {"query": {"type": "string"}}},
)

session = await client.create_session(
    tools=[heavy_tool],
    default_agent={"excluded_tools": ["analyze-codebase"]},
    custom_agents=[
        {
            "name": "researcher",
            "description": "Deep codebase analysis agent",
            "tools": ["analyze-codebase"],
            "prompt": "You perform thorough codebase analysis.",
        },
    ],
    on_permission_request=approve_all,
)
```

{% endcodetab %}
{% codetab go %}

<!-- docs-validate: skip -->

```golang
session, err := client.CreateSession(ctx, &copilot.SessionConfig{
    Tools: []copilot.Tool{heavyTool},
    DefaultAgent: &copilot.DefaultAgentConfig{
        ExcludedTools: []string{"analyze-codebase"},
    },
    CustomAgents: []copilot.CustomAgentConfig{
        {
            Name:        "researcher",
            Description: "Deep codebase analysis agent",
            Tools:       []string{"analyze-codebase"},
            Prompt:      "You perform thorough codebase analysis.",
        },
    },
})
```

{% endcodetab %}
{% codetab csharp %}

<!-- docs-validate: skip -->

```csharp
var session = await client.CreateSessionAsync(new SessionConfig
{
    Tools = [analyzeCodebaseTool],
    DefaultAgent = new DefaultAgentConfig
    {
        ExcludedTools = ["analyze-codebase"],
    },
    CustomAgents =
    [
        new CustomAgentConfig
        {
            Name = "researcher",
            Description = "Deep codebase analysis agent",
            Tools = ["analyze-codebase"],
            Prompt = "You perform thorough codebase analysis.",
        },
    ],
});
```

{% endcodetab %}
{% endcodetabs %}

### How it works

Tools listed in `defaultAgent.excludedTools`:

1. **Are registered**—their handlers are available for execution
1. **Are hidden** from the main agent's tool list—the LLM won't see or call them directly
1. **Remain available** to any custom sub-agent that includes them in its `tools` array

### Interaction with other tool filters

`defaultAgent.excludedTools` is orthogonal to the session-level `availableTools` and `excludedTools`:

| Filter | Scope | Effect |
|--------|-------|--------|
| `availableTools` | Session-wide | Allowlist—only these tools exist for anyone |
| `excludedTools` | Session-wide | Blocklist—these tools are blocked for everyone |
| `defaultAgent.excludedTools` | Main agent only | These tools are hidden from the main agent but available to sub-agents |

Precedence:
1. Session-level `availableTools`/`excludedTools` are applied first (globally)
1. `defaultAgent.excludedTools` is applied on top, further restricting the main agent only

> [!NOTE]
> If a tool is in both `excludedTools` (session-level) and `defaultAgent.excludedTools`, the session-level exclusion takes precedence—the tool is unavailable to everyone.

## Attaching MCP servers to agents

Each custom agent can have its own MCP (Model Context Protocol) servers, giving it access to specialized data sources:

```typescript
const session = await client.createSession({
    customAgents: [
        {
            name: "db-analyst",
            description: "Analyzes database schemas and queries",
            prompt: "You are a database expert. Use the database MCP server to analyze schemas.",
            mcpServers: {
                "database": {
                    command: "npx",
                    args: ["-y", "@modelcontextprotocol/server-postgres", "postgresql://localhost/mydb"],
                },
            },
        },
    ],
});
```

## Patterns and best practices

### Pair a researcher with an editor

A common pattern is to define a read-only researcher agent and a write-capable editor agent. The runtime delegates exploration tasks to the researcher and modification tasks to the editor:

```typescript
customAgents: [
    {
        name: "researcher",
        description: "Analyzes code structure, finds patterns, and answers questions",
        tools: ["grep", "glob", "view"],
        prompt: "You are a code analyst. Thoroughly explore the codebase to answer questions.",
    },
    {
        name: "implementer",
        description: "Implements code changes based on analysis",
        tools: ["view", "edit", "bash"],
        prompt: "You make minimal, targeted code changes. Always verify changes compile.",
    },
]
```

### Keep agent descriptions specific

The runtime uses the `description` to match user intent. Vague descriptions lead to poor delegation:

```typescript
// ❌ Too vague — runtime can't distinguish from other agents
{ description: "Helps with code" }

// ✅ Specific — runtime knows when to delegate
{ description: "Analyzes Python test coverage and identifies untested code paths" }
```

### Handle failures gracefully

Sub-agents can fail. Always listen for `subagent.failed` events and handle them in your application:

```typescript
session.on((event) => {
    if (event.type === "subagent.failed") {
        logger.error(`Agent ${event.data.agentName} failed: ${event.data.error}`);
        // Show error in UI, retry, or fall back to parent agent
    }
});
```


</details>
