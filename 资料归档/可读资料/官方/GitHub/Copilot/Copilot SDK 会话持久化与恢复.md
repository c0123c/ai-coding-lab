# Copilot SDK 会话持久化与恢复

讲解会话标识、创建与恢复、保存内容、模型和认证变更及生产管理，并提供多语言示例。

- 来源：GitHub／Copilot
- 资料类型：存档文档
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-09-24](<https://github.com/github/docs/commit/d00eabd5ae6575005437fbc848705f0d0415d8d4>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/copilot-sdk/features/session-persistence.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/Copilot SDK 会话持久化与恢复.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Session resume and persistence
shortTitle: Session Persistence
intro: >-
  This guide walks you through the SDK's session persistence capabilities—how to
  pause work, resume it later, and manage sessions in production environments.
versions:
  fpt: '*'
  ghec: '*'
redirect_from:
  - /copilot/how-tos/copilot-sdk/use-copilot-sdk/session-persistence
contentType: how-tos
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

<!-- markdownlint-disable GHD046 GHD005 -->
<!-- Suppressed: GHD046 (outdated release terminology), GHD005 (hardcoded data variable) -->

## 会议如何运作

创建会话时，Copilot CLI 会维护对话历史记录、工具状态和计划上下文。默认情况下，此状态存在于内存中，并在会话结束时消失。启用持久性后，您可以在重新启动、容器迁移甚至不同客户端实例之间恢复会话。

![图表：显示所描述过程的流程图。](<https://github.com/assets/images/help/copilot/copilot-sdk/features-session-persistence-diagram-0.png>)

|状态|会发生什么|
|-------|--------------|
|**创建**|`session_id` 已分配|
|**活动**|发送提示、工具调用、响应|
|**已暂停**|状态保存到磁盘|
|**简历**|从磁盘加载的状态|

## 快速入门：创建可恢复会话

可恢复会话的关键是提供您自己的 `session_id`。如果没有，SDK 会生成随机的 ID，并且稍后无法恢复会话。

### TypeScript

```typescript
import { CopilotClient } from "@github/copilot-sdk";

const client = new CopilotClient();

// Create a session with a meaningful ID
const session = await client.createSession({
  sessionId: "user-123-task-456",
  model: "gpt-5.2-codex",
});

// Do some work...
await session.sendAndWait({ prompt: "Analyze my codebase" });

// Session state is automatically persisted
// You can safely close the client
```

### Python

```python
from copilot import CopilotClient
from copilot.session import PermissionHandler

client = CopilotClient()
await client.start()

# Create a session with a meaningful ID
session = await client.create_session(on_permission_request=PermissionHandler.approve_all, model="gpt-5.2-codex", session_id="user-123-task-456")

# Do some work...
await session.send_and_wait("Analyze my codebase")

# Session state is automatically persisted
```

### Go

```golang
ctx := context.Background()
client := copilot.NewClient(nil)

// Create a session with a meaningful ID
session, _ := client.CreateSession(ctx, &copilot.SessionConfig{
    SessionID: "user-123-task-456",
    Model:     "gpt-5.2-codex",
})

// Do some work...
session.SendAndWait(ctx, copilot.MessageOptions{Prompt: "Analyze my codebase"})

// Session state is automatically persisted
```

### C# (.NET)

```csharp
using GitHub.Copilot;

var client = new CopilotClient();

// Create a session with a meaningful ID
var session = await client.CreateSessionAsync(new SessionConfig
{
    SessionId = "user-123-task-456",
    Model = "gpt-5.2-codex",
});

// Do some work...
await session.SendAndWaitAsync(new MessageOptions { Prompt = "Analyze my codebase" });

// Session state is automatically persisted
```

## 恢复会话

稍后（几分钟、几小时甚至几天），您可以从上次中断的地方恢复会话。

![图表：显示所描述过程的流程图。](<https://github.com/assets/images/help/copilot/copilot-sdk/features-session-persistence-diagram-1.png>)

### TypeScript

```typescript
// Resume from a different client instance (or after restart)
const session = await client.resumeSession("user-123-task-456");

// Continue where you left off
await session.sendAndWait({ prompt: "What did we discuss earlier?" });
```

### Python

```python
# Resume from a different client instance (or after restart)
session = await client.resume_session("user-123-task-456", on_permission_request=PermissionHandler.approve_all)

# Continue where you left off
await session.send_and_wait("What did we discuss earlier?")
```

### Go

```golang
ctx := context.Background()

// Resume from a different client instance (or after restart)
session, _ := client.ResumeSession(ctx, "user-123-task-456", nil)

// Continue where you left off
session.SendAndWait(ctx, copilot.MessageOptions{Prompt: "What did we discuss earlier?"})
```

### C# (.NET)

```csharp
// Resume from a different client instance (or after restart)
var session = await client.ResumeSessionAsync("user-123-task-456");

// Continue where you left off
await session.SendAndWaitAsync(new MessageOptions { Prompt = "What did we discuss earlier?" });
```

## 恢复选项

恢复会话时，您可以选择重新配置许多设置。当您需要更改模型、更新工具配置或修改行为时，这非常有用。

|选项|描述|
|--------|-------------|
| `model` |更改恢复会话的模型|
| `systemMessage` |覆盖或扩展系统提示|
| `availableTools` |限制可用的工具|
| `excludedTools` |禁用特定工具|
| `provider` |重新提供 BYOK 凭据（BYOK 会话所需）|
| `capi.autoTier` |覆盖保留的自动路由首选项|
| `capi.enableWebSocketResponses` |选择恢复会话的响应 API 传输|
| `reasoningEffort` |调整推理努力水平|
| `streaming` |Enable/disable 流响应|
| `workingDirectory` |更改工作目录|
| `configDir` |覆盖配置目录|
| `mcpServers` |配置MCP服务器|
| `customAgents` |配置自定义agents|
| `agent` |按名称预先选择自定义 agent|
| `skillDirectories` |从中加载技能的目录|
| `disabledSkills` |禁用技能|
| `infiniteSessions` |配置无限会话行为|

### 自动分层持久化

对于 `model: "auto"`，可选的 `capi.autoTier` 设置选择自动路由首选项：`efficiency`、`balance`、`intelligence` 或 `fast`。在Python中，使用`capi={"auto_tier": "balance"}`。该设置适用于V2自动路由； V1 自动请求未更改。

`fast` 是仅限集成商的延迟预设，而不是第一方 GitHub Copilot 产品首选项。 SDK 不会决定快速资格，检查客户端身份，选择它作为默认值，或者在运行时不支持它时回退到另一层 - 较旧的运行时返回其本机错误不变。

运行时会保留所选层，因此应用程序无需在每次恢复时重新发送它：

* 创建会话时省略层将使用运行时的默认路由行为。
* 冷恢复恢复持久层。提供显式层会覆盖新激活的恢复值。
* 当恢复已驻留在运行时中的会话时，省略层会保留当前选择，并且提供相同的层是无操作。提供不同的层会请求运行时在恢复成功后应用的安全切换；它不能改变已经在飞行中的转弯。
* 没有持久层的旧会话保留默认路由行为。

层选择不是实时模型切换操作。 SDK转发偏好；运行时拥有持久性和验证。

`session.start` 和 `session.resume` 事件在其可选的 `data.autoTier` 字段（Python 中的 `data.auto_tier`）中公开所选层。当未选择任何层时，该字段将被省略。

### 在会话期间更改自动层

调用 `setAutoTier` 更改实时会话的路由首选项，而不更改所选模型。通过 `null` (Python `None`, Go `nil`) 返回提供商默认的自动路由。

```typescript
const result = await session.setAutoTier("intelligence");
if (result.status === "pending") {
  // Accepted, but not yet in effect.
}
```

运行时不会立即应用该首选项。它记录请求并仅当后来的用户使用 `auto` 模型成功从提供者获取可用模型时才提交请求。因此，`pending` 状态确认请求已被接受，而不是请求已生效。只有最近的请求会幸存：新的请求会替换尚未轮到的任何较早的请求。

通过这些事件观察结果：

* `session.model_change` 当首选项提交时。
* `session.auto_tier_switch_failed` 当它没有时。此事件是短暂的，因此运行时不会持续存在或在恢复时重播它。其 `reason` 字段是 `policy_rejected`、`request_failed`、`setup_failed` 或 `unsupported` 之一，并且先前有效的首选项保持活动状态。

您还可以随时通过会话的 `model.getCurrent` RPC 方法读取权威状态，该方法报告已提交的 `autoTier`、任何未认领的 `pendingAutoTier` 以及当前由正在进行的激活认领的 `activatingAutoTier`。

| SDK |更改等级|返回提供商默认路由|
|-----|-----------------|------------------------------------|
| Node.js | `session.setAutoTier("balance")` | `session.setAutoTier(null)` |
| Python | `session.set_auto_tier("balance")` | `session.set_auto_tier(None)` |
| Go | `session.SetAutoTier(ctx, &tier)` | `session.SetAutoTier(ctx, nil)` |
| .NET | `session.SetAutoTierAsync(AutoTier.Balance)` | `session.SetAutoTierAsync(null)` |
| Rust | `session.set_auto_tier(Some(AutoTier::Balance))` | `session.set_auto_tier(None)` |
| Java | `session.setAutoTier(AutoTier.BALANCE)` | `session.setAutoTier(null)` |

要在一次调用中选择 `auto` 模型及其路由首选项，请将层暂存到模型交换机上。当模型不是 `auto` 时，运行时会拒绝此选项。

| SDK |使用开关暂存一层|重置为提供商默认路由|
|-----|------------------------------|-----------------------------------|
| Node.js | `setModel("auto", { autoTier: "balance" })` | `setModel("auto", { autoTier: null })` |
| Python | `set_model("auto", auto_tier="balance")` | `set_model("auto", auto_tier=None)` |
| Go | `SetModelOptions{AutoTier: &tier}` | `SetModelOptions{ResetAutoTier: true}` |
| .NET | `new SetModelOptions { AutoTier = AutoTier.Balance }` | `new SetModelOptions { ResetAutoTier = true }` |
| Rust | `SetModelOptions::default().with_auto_tier(AutoTier::Balance)` | `SetModelOptions::default().with_reset_auto_tier()` |
| Java | `new SetModelOptions().setModel("auto").setAutoTier(AutoTier.BALANCE)` | `new SetModelOptions().setModel("auto").setResetAutoTier(true)` |

Node.js、Python 和 Rust 在单个值中表示所有三种状态：Node.js 和 Python 因为 `null`/`None` 可以与省略的参数区分开来，并且Rust 因为 `AutoTierPreference::Reset` 是同一选项的不同变体。 Go、.NET 和 Java 无法在一个值中区分“重置”和“未设置”，因此它们带有单独的重置标志。省略两者始终意味着“保留当前偏好”。

### 简历上的回复传输

可选的 `capi.enableWebSocketResponses` 设置选择 CAPI 响应 API 的传输。它默认为 `true`，因此只要所选模型通告 `ws:/responses` 端点，就会使用 WebSocket 传输。将其设置为 `false` 会回退到 HTTP 传输。在Python中，使用`capi={"enable_web_socket_responses": False}`。

当您需要时，在简历通话中提供它。当 WebSocket 连接在代理后面失败时，以及当恢复的会话报告 `400 input item ID does not belong to this connection`（特定于 WebSocket 传输）时，值得进行设置。

```typescript
const session = await client.resumeSession("user-123-task-456", {
  capi: { enableWebSocketResponses: false },
});
```

将其设置为`false`相当于`COPILOT_CLI_DISABLE_WEBSOCKET_RESPONSES`环境变量，其极性相反。

### 示例：更改简历中的型号

```typescript
// Resume with a different model
const session = await client.resumeSession("user-123-task-456", {
  model: "claude-sonnet-4",  // Switch to a different model
  reasoningEffort: "high",   // Increase reasoning effort
});
```

## 在恢复会话中使用 BYOK（自带密钥）

当使用您自己的 API 密钥时，您必须在恢复时重新提供提供程序配置。出于安全原因，API 密钥永远不会保留到磁盘上。

```typescript
// Original session with BYOK
const session = await client.createSession({
  sessionId: "user-123-task-456",
  model: "gpt-5.2-codex",
  provider: {
    type: "azure",
    endpoint: "https://my-resource.openai.azure.com",
    apiKey: process.env.AZURE_OPENAI_KEY,
    deploymentId: "my-gpt-deployment",
  },
});

// When resuming, you MUST re-provide the provider config
const resumed = await client.resumeSession("user-123-task-456", {
  provider: {
    type: "azure",
    endpoint: "https://my-resource.openai.azure.com",
    apiKey: process.env.AZURE_OPENAI_KEY,  // Required again
    deploymentId: "my-gpt-deployment",
  },
});
```

## 什么得到坚持？

会话状态保存到`~/.copilot/session-state/{sessionId}/`：

```text
~/.copilot/session-state/
└── user-123-task-456/
    ├── checkpoints/           # Conversation history snapshots
    │   ├── 001.json          # Initial state
    │   ├── 002.json          # After first interaction
    │   └── ...               # Incremental checkpoints
    ├── plan.md               # Agent's planning state (if any)
    └── files/                # Session artifacts
        ├── analysis.md       # Files the agent created
        └── notes.txt         # Working documents
```

|数据|坚持了吗？|注释|
|------|------------|-------|
|对话历史记录|✅ 是的|完整的消息线索|
|工具调用结果|✅ 是的|缓存上下文|
|Agent规划状态|✅ 是的|`plan.md` 文件|
|会话工件|✅ 是的|在`files/`目录中|
|Provider/API 键|❌ 没有|安全性：必须重新提供|
|内存中工具状态|❌ 没有|工具应该是无状态的|

## 会议 ID 最佳实践

选择编码所有权和目的的会话 ID。这使得审核和清理变得更加容易。

|图案|示例|使用案例|
|---------|---------|----------|
| ❌ `abc123` |随机 ID|难以审计，没有所有权信息|
| ✅ `user-{userId}-{taskId}` | `user-alice-pr-review-42` |多用户应用程序|
| ✅ `tenant-{tenantId}-{workflow}` | `tenant-acme-onboarding` |多租户SaaS|
| ✅ `{userId}-{taskId}-{timestamp}` | `alice-deploy-1706932800` |基于时间的清理|

**结构化ID的好处：**
* 易于审核：“显示用户 alice 的所有会话”
* 易于清理：“删除所有早于 X 的会话”
* 自然访问控制：从会话 ID 中解析用户 ID

### 示例：生成会话 ID

```typescript
function createSessionId(userId: string, taskType: string): string {
  const timestamp = Date.now();
  return `${userId}-${taskType}-${timestamp}`;
}

const sessionId = createSessionId("alice", "code-review");
// → "alice-code-review-1706932800000"
```

```python
import time

def create_session_id(user_id: str, task_type: str) -> str:
    timestamp = int(time.time())
    return f"{user_id}-{task_type}-{timestamp}"

session_id = create_session_id("alice", "code-review")
# → "alice-code-review-1706932800"
```

## 管理会话生命周期

### 列出活动会话

```typescript
// List all sessions
const sessions = await client.listSessions();
console.log(`Found ${sessions.length} sessions`);

for (const session of sessions) {
  console.log(`- ${session.sessionId} (created: ${session.createdAt})`);
}

// Filter sessions by repository
const repoSessions = await client.listSessions({ repository: "owner/repo" });
```

### 清理旧会话

```typescript
async function cleanupExpiredSessions(maxAgeMs: number) {
  const sessions = await client.listSessions();
  const now = Date.now();
  
  for (const session of sessions) {
    const age = now - new Date(session.createdAt).getTime();
    if (age > maxAgeMs) {
      await client.deleteSession(session.sessionId);
      console.log(`Deleted expired session: ${session.sessionId}`);
    }
  }
}

// Clean up sessions older than 24 hours
await cleanupExpiredSessions(24 * 60 * 60 * 1000);
```

### 与会话断开连接 (`disconnect`)

当任务完成时，显式断开与会话的连接，而不是等待超时。这会释放内存资源，但 ** 会在磁盘 ** 上保留会话数据，因此会话仍可以稍后恢复：

```typescript
try {
  // Do work...
  await session.sendAndWait({ prompt: "Complete the task" });
  
  // Task complete — release in-memory resources (session can be resumed later)
  await session.disconnect();
} catch (error) {
  // Clean up even on error
  await session.disconnect();
  throw error;
}
```

每个 SDK 还提供惯用的自动清理模式：

|语言|图案|示例|
|----------|---------|---------|
| **TypeScript** | `Symbol.asyncDispose` | `await using session = await client.createSession(config);` |
| **Python** |`async with` 上下文管理器| `async with await client.create_session(on_permission_request=handler) as session:` |
| **C#** | `IAsyncDisposable` | `await using var session = await client.CreateSessionAsync(config);` |
| **Go** | `defer` | `defer session.Disconnect()` |

> [!NOTE]
> `destroy()` 已弃用，取而代之的是 `disconnect()`。使用 `destroy()` 的现有代码将继续工作，但应迁移。

### 永久删除会话 (`deleteSession`)

要从磁盘中永久删除会话及其所有数据（对话历史记录、计划状态、工件），请使用 `deleteSession`。这是不可逆的 — 删除后会话 ** 无法恢复 **：

```typescript
// Permanently remove session data
await client.deleteSession("user-123-task-456");
```

> **`disconnect()` 与 `deleteSession()`:** `disconnect()` 释放内存资源，但将会话数据保留在磁盘上以供以后恢复。 `deleteSession()` 永久删除所有内容，包括磁盘上的文件。

## 自动清理：空闲超时

默认情况下，会话**没有空闲超时**并且无限期地存在，直到明确断开连接或删除。您可以选择通过 `CopilotClientOptions.sessionIdleTimeoutSeconds` 配置服务器范围的空闲超时：

```typescript
const client = new CopilotClient({
  sessionIdleTimeoutSeconds: 30 * 60, // 30 minutes
});
```

配置超时后，在该时间内没有活动的会话将被自动清除。设置为 `0` 或省略以禁用。

> [!NOTE]
> 此选项仅在 SDK 生成运行时进程时适用。当通过`cliUrl`连接到现有服务器时，服务器自己的超时配置适用。

![图表：显示所描述过程的流程图。](<https://github.com/assets/images/help/copilot/copilot-sdk/features-session-persistence-diagram-2.png>)

无论超时设置如何，具有活动工作的会话（运行命令、后台 agents）始终受到保护，免遭空闲清理。

监听空闲事件以对会话不活动做出反应：

```typescript
session.on("session.idle", (event) => {
  console.log(`Session idle for ${event.idleDurationMs}ms`);
});
```

## 部署模式

### 模式 1：每个用户一台 CLI 服务器（推荐）

最适合：强隔离、多租户环境、Azure 动态会话。

![图表：显示所描述过程的流程图。](<https://github.com/assets/images/help/copilot/copilot-sdk/features-session-persistence-diagram-3.png>)

**优点：** ✅ 完全隔离 | ✅ 简单的安全性 | ✅ 轻松扩展

### 模式 2：共享 CLI 服务器（资源高效）

最适合：内部工具、可信环境、资源受限的设置。

![图表：显示所描述过程的流程图。](<https://github.com/assets/images/help/copilot/copilot-sdk/features-session-persistence-diagram-4.png>)

**要求：**
* ⚠️ 每个用户的唯一会话 ID
* ⚠️ 应用级访问控制
* ⚠️ 操作前会话 ID 验证

```typescript
// Application-level access control for shared CLI
async function resumeSessionWithAuth(
  client: CopilotClient,
  sessionId: string,
  currentUserId: string
): Promise<Session> {
  // Parse user from session ID
  const [sessionUserId] = sessionId.split("-");
  
  if (sessionUserId !== currentUserId) {
    throw new Error("Access denied: session belongs to another user");
  }
  
  return client.resumeSession(sessionId);
}
```

## Azure 动态会话

对于容器可以重新启动或迁移的 serverless/container 部署：

### 挂载持久存储

会话状态目录必须安装到持久存储：

```yaml
# Azure Container Instance example
containers:
  - name: copilot-agent
    image: my-agent:latest
    volumeMounts:
      - name: session-storage
        mountPath: /home/app/.copilot/session-state

volumes:
  - name: session-storage
    azureFile:
      shareName: copilot-sessions
      storageAccountName: myaccount
```

![图表：显示所描述过程的流程图。](<https://github.com/assets/images/help/copilot/copilot-sdk/features-session-persistence-diagram-5.png>)

**会话在容器重新启动后仍然存在！**

## 适用于长时间运行的工作流程的无限会话

对于可能超出上下文限制的工作流程，通过自动压缩启用无限会话：

```typescript
const session = await client.createSession({
  sessionId: "long-workflow-123",
  infiniteSessions: {
    enabled: true,
    backgroundCompactionThreshold: 0.80,  // Start compaction at 80% context
    bufferExhaustionThreshold: 0.95,      // Block at 95% if needed
  },
});
```

> [!NOTE]
> 阈值是上下文利用率(0.0-1.0)，而不是绝对令牌计数。详情请参见[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/troubleshooting/compatibility>)。

## 限制和注意事项

|局限性|描述|缓解措施|
|------------|-------------|------------|
|**BYOK重新认证**|API 密钥未保留|将密钥存储在您的秘密管理器中；简历上提供|
|**可写存储**|`~/.copilot/session-state/` 必须可写|在容器中挂载持久卷|
|**无会话锁定**|对同一会话的并发访问未定义|实施应用程序级锁定或队列|
|**工具状态未保留**|内存中工具状态丢失|将工具设计为无状态或保留自己的状态|

### 处理并发访问

SDK 不提供内置会话锁定。如果多个客户端可能访问同一个会话：

```typescript
// Option 1: Application-level locking with Redis
import Redis from "ioredis";

const redis = new Redis();

async function withSessionLock<T>(
  sessionId: string,
  fn: () => Promise<T>
): Promise<T> {
  const lockKey = `session-lock:${sessionId}`;
  const acquired = await redis.set(lockKey, "locked", "NX", "EX", 300);
  
  if (!acquired) {
    throw new Error("Session is in use by another client");
  }
  
  try {
    return await fn();
  } finally {
    await redis.del(lockKey);
  }
}

// Usage
await withSessionLock("user-123-task-456", async () => {
  const session = await client.resumeSession("user-123-task-456");
  await session.sendAndWait({ prompt: "Continue the task" });
});
```

## 总结

|特点|如何使用|
|---------|------------|
|**创建可恢复会话**|提供您自己的`sessionId`|
|**恢复会话**| `client.resumeSession(sessionId)` |
|**BYOK 简历**|重新提供`provider`配置|
|**列出会话**| `client.listSessions(filter?)` |
|**从活动会话断开连接**|`session.disconnect()`——释放内存资源；保留磁盘上的会话数据以供恢复|
|**永久删除会话**|`client.deleteSession(sessionId)`—从磁盘中永久删除所有会话数据；无法恢复|
|**容器化部署**|将 `~/.copilot/session-state/` 安装到持久存储|

## 后续步骤

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/hooks/hooks-overview>) - 使用钩子自定义会话行为
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/troubleshooting/compatibility>) - SDK 与 CLI 功能比较
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/troubleshooting/debugging>) - 解决会话问题


## 原文对照

<details>
<summary>展开完整原文</summary>

<!-- markdownlint-disable GHD046 GHD005 -->
<!-- Suppressed: GHD046 (outdated release terminology), GHD005 (hardcoded data variable) -->

## How sessions work

When you create a session, the Copilot CLI maintains conversation history, tool state, and planning context. By default, this state lives in memory and disappears when the session ends. With persistence enabled, you can resume sessions across restarts, container migrations, or even different client instances.

![Diagram: Flowchart showing the described process.](<https://github.com/assets/images/help/copilot/copilot-sdk/features-session-persistence-diagram-0.png>)

| State | What happens |
|-------|--------------|
| **Create** | `session_id` assigned |
| **Active** | Send prompts, tool calls, responses |
| **Paused** | State saved to disk |
| **Resume** | State loaded from disk |

## Quick start: creating a resumable session

The key to resumable sessions is providing your own `session_id`. Without one, the SDK generates a random ID and the session can't be resumed later.

### TypeScript

```typescript
import { CopilotClient } from "@github/copilot-sdk";

const client = new CopilotClient();

// Create a session with a meaningful ID
const session = await client.createSession({
  sessionId: "user-123-task-456",
  model: "gpt-5.2-codex",
});

// Do some work...
await session.sendAndWait({ prompt: "Analyze my codebase" });

// Session state is automatically persisted
// You can safely close the client
```

### Python

```python
from copilot import CopilotClient
from copilot.session import PermissionHandler

client = CopilotClient()
await client.start()

# Create a session with a meaningful ID
session = await client.create_session(on_permission_request=PermissionHandler.approve_all, model="gpt-5.2-codex", session_id="user-123-task-456")

# Do some work...
await session.send_and_wait("Analyze my codebase")

# Session state is automatically persisted
```

### Go

```golang
ctx := context.Background()
client := copilot.NewClient(nil)

// Create a session with a meaningful ID
session, _ := client.CreateSession(ctx, &copilot.SessionConfig{
    SessionID: "user-123-task-456",
    Model:     "gpt-5.2-codex",
})

// Do some work...
session.SendAndWait(ctx, copilot.MessageOptions{Prompt: "Analyze my codebase"})

// Session state is automatically persisted
```

### C# (.NET)

```csharp
using GitHub.Copilot;

var client = new CopilotClient();

// Create a session with a meaningful ID
var session = await client.CreateSessionAsync(new SessionConfig
{
    SessionId = "user-123-task-456",
    Model = "gpt-5.2-codex",
});

// Do some work...
await session.SendAndWaitAsync(new MessageOptions { Prompt = "Analyze my codebase" });

// Session state is automatically persisted
```

## Resuming a session

Later—minutes, hours, or even days—you can resume the session from where you left off.

![Diagram: Flowchart showing the described process.](<https://github.com/assets/images/help/copilot/copilot-sdk/features-session-persistence-diagram-1.png>)

### TypeScript

```typescript
// Resume from a different client instance (or after restart)
const session = await client.resumeSession("user-123-task-456");

// Continue where you left off
await session.sendAndWait({ prompt: "What did we discuss earlier?" });
```

### Python

```python
# Resume from a different client instance (or after restart)
session = await client.resume_session("user-123-task-456", on_permission_request=PermissionHandler.approve_all)

# Continue where you left off
await session.send_and_wait("What did we discuss earlier?")
```

### Go

```golang
ctx := context.Background()

// Resume from a different client instance (or after restart)
session, _ := client.ResumeSession(ctx, "user-123-task-456", nil)

// Continue where you left off
session.SendAndWait(ctx, copilot.MessageOptions{Prompt: "What did we discuss earlier?"})
```

### C# (.NET)

```csharp
// Resume from a different client instance (or after restart)
var session = await client.ResumeSessionAsync("user-123-task-456");

// Continue where you left off
await session.SendAndWaitAsync(new MessageOptions { Prompt = "What did we discuss earlier?" });
```

## Resume options

When resuming a session, you can optionally reconfigure many settings. This is useful when you need to change the model, update tool configurations, or modify behavior.

| Option | Description |
|--------|-------------|
| `model` | Change the model for the resumed session |
| `systemMessage` | Override or extend the system prompt |
| `availableTools` | Restrict which tools are available |
| `excludedTools` | Disable specific tools |
| `provider` | Re-provide BYOK credentials (required for BYOK sessions) |
| `capi.autoTier` | Override the persisted Auto routing preference |
| `capi.enableWebSocketResponses` | Choose the Responses API transport for the resumed session |
| `reasoningEffort` | Adjust reasoning effort level |
| `streaming` | Enable/disable streaming responses |
| `workingDirectory` | Change the working directory |
| `configDir` | Override configuration directory |
| `mcpServers` | Configure MCP servers |
| `customAgents` | Configure custom agents |
| `agent` | Pre-select a custom agent by name |
| `skillDirectories` | Directories to load skills from |
| `disabledSkills` | Skills to disable |
| `infiniteSessions` | Configure infinite session behavior |

### Auto tier persistence

With `model: "auto"`, the optional `capi.autoTier` setting selects an Auto routing preference: `efficiency`, `balance`, `intelligence`, or `fast`. In Python, use `capi={"auto_tier": "balance"}`. This setting applies to V2 Auto routing; V1 Auto requests are unchanged.

`fast` is an integrator-only latency preset, not a first-party GitHub Copilot product preference. The SDK does not decide Fast eligibility, inspect client identity, choose it as a default, or fall back to another tier when a runtime does not support it—an older runtime returns its native error unchanged.

The runtime persists the selected tier, so applications do not need to resend it on every resume:

* Omitting the tier when creating a session uses the runtime's default routing behavior.
* A cold resume restores the persisted tier. Supplying an explicit tier overrides the restored value for the new activation.
* When resuming a session already resident in the runtime, omitting the tier preserves the current selection and supplying the same tier is a no-op. Supplying a different tier requests a safe switch that the runtime applies after the resume succeeds; it cannot change a turn that is already in flight.
* Older sessions without a persisted tier retain default routing behavior.

Tier selection is not a live model-switch operation. The SDK forwards the preference; the runtime owns persistence and validation.

The `session.start` and `session.resume` events expose the selected tier in their optional `data.autoTier` field (`data.auto_tier` in Python). When no tier is selected, the field is omitted.

### Changing the Auto tier during a session

Call `setAutoTier` to change the routing preference on a live session without changing the selected model. Pass `null` (Python `None`, Go `nil`) to return to the provider's default Auto routing.

```typescript
const result = await session.setAutoTier("intelligence");
if (result.status === "pending") {
  // Accepted, but not yet in effect.
}
```

The runtime does not apply the preference immediately. It records the request and commits it only when a later user turn using the `auto` model successfully obtains a usable model from the provider. A `pending` status therefore confirms that the request was accepted, not that it took effect. Only the most recent request survives: a new request replaces any earlier one that no turn has claimed yet.

Watch for the outcome through these events:

* `session.model_change` when the preference commits.
* `session.auto_tier_switch_failed` when it does not. This event is ephemeral, so the runtime never persists or replays it on resume. Its `reason` field is one of `policy_rejected`, `request_failed`, `setup_failed`, or `unsupported`, and the previously effective preference stays active.

You can also read the authoritative state at any time through the session's `model.getCurrent` RPC method, which reports the committed `autoTier`, any unclaimed `pendingAutoTier`, and the `activatingAutoTier` currently claimed by an in-progress activation.

| SDK | Change the tier | Return to provider-default routing |
|-----|-----------------|------------------------------------|
| Node.js | `session.setAutoTier("balance")` | `session.setAutoTier(null)` |
| Python | `session.set_auto_tier("balance")` | `session.set_auto_tier(None)` |
| Go | `session.SetAutoTier(ctx, &tier)` | `session.SetAutoTier(ctx, nil)` |
| .NET | `session.SetAutoTierAsync(AutoTier.Balance)` | `session.SetAutoTierAsync(null)` |
| Rust | `session.set_auto_tier(Some(AutoTier::Balance))` | `session.set_auto_tier(None)` |
| Java | `session.setAutoTier(AutoTier.BALANCE)` | `session.setAutoTier(null)` |

To select the `auto` model and its routing preference in a single call, stage the tier on the model switch instead. The runtime rejects this option when the model is anything other than `auto`.

| SDK | Stage a tier with the switch | Reset to provider-default routing |
|-----|------------------------------|-----------------------------------|
| Node.js | `setModel("auto", { autoTier: "balance" })` | `setModel("auto", { autoTier: null })` |
| Python | `set_model("auto", auto_tier="balance")` | `set_model("auto", auto_tier=None)` |
| Go | `SetModelOptions{AutoTier: &tier}` | `SetModelOptions{ResetAutoTier: true}` |
| .NET | `new SetModelOptions { AutoTier = AutoTier.Balance }` | `new SetModelOptions { ResetAutoTier = true }` |
| Rust | `SetModelOptions::default().with_auto_tier(AutoTier::Balance)` | `SetModelOptions::default().with_reset_auto_tier()` |
| Java | `new SetModelOptions().setModel("auto").setAutoTier(AutoTier.BALANCE)` | `new SetModelOptions().setModel("auto").setResetAutoTier(true)` |

Node.js, Python, and Rust express all three states in a single value: Node.js and Python because `null`/`None` is distinguishable from an omitted argument, and Rust because `AutoTierPreference::Reset` is a distinct variant of the same option. Go, .NET, and Java have no way to distinguish "reset" from "unset" in one value, so they carry a separate reset flag. Omitting both always means "leave the current preference alone."

### Responses transport on resume

The optional `capi.enableWebSocketResponses` setting chooses the transport for the CAPI Responses API. It defaults to `true`, so the WebSocket transport is used whenever the selected model advertises the `ws:/responses` endpoint. Setting it to `false` falls back to the HTTP transport. In Python, use `capi={"enable_web_socket_responses": False}`.

Supply it on the resume call when you need it. It is worth setting when WebSocket connections fail behind a proxy, and when a resumed session reports `400 input item ID does not belong to this connection`, which is specific to the WebSocket transport.

```typescript
const session = await client.resumeSession("user-123-task-456", {
  capi: { enableWebSocketResponses: false },
});
```

Setting this to `false` is equivalent to the `COPILOT_CLI_DISABLE_WEBSOCKET_RESPONSES` environment variable, which has the opposite polarity.

### Example: changing model on resume

```typescript
// Resume with a different model
const session = await client.resumeSession("user-123-task-456", {
  model: "claude-sonnet-4",  // Switch to a different model
  reasoningEffort: "high",   // Increase reasoning effort
});
```

## Using BYOK (bring your own key) with resumed sessions

When using your own API keys, you must re-provide the provider configuration when resuming. API keys are never persisted to disk for security reasons.

```typescript
// Original session with BYOK
const session = await client.createSession({
  sessionId: "user-123-task-456",
  model: "gpt-5.2-codex",
  provider: {
    type: "azure",
    endpoint: "https://my-resource.openai.azure.com",
    apiKey: process.env.AZURE_OPENAI_KEY,
    deploymentId: "my-gpt-deployment",
  },
});

// When resuming, you MUST re-provide the provider config
const resumed = await client.resumeSession("user-123-task-456", {
  provider: {
    type: "azure",
    endpoint: "https://my-resource.openai.azure.com",
    apiKey: process.env.AZURE_OPENAI_KEY,  // Required again
    deploymentId: "my-gpt-deployment",
  },
});
```

## What gets persisted?

Session state is saved to `~/.copilot/session-state/{sessionId}/`:

```text
~/.copilot/session-state/
└── user-123-task-456/
    ├── checkpoints/           # Conversation history snapshots
    │   ├── 001.json          # Initial state
    │   ├── 002.json          # After first interaction
    │   └── ...               # Incremental checkpoints
    ├── plan.md               # Agent's planning state (if any)
    └── files/                # Session artifacts
        ├── analysis.md       # Files the agent created
        └── notes.txt         # Working documents
```

| Data | Persisted? | Notes |
|------|------------|-------|
| Conversation history | ✅ Yes | Full message thread |
| Tool call results | ✅ Yes | Cached for context |
| Agent planning state | ✅ Yes | `plan.md` file |
| Session artifacts | ✅ Yes | In `files/` directory |
| Provider/API keys | ❌ No | Security: must re-provide |
| In-memory tool state | ❌ No | Tools should be stateless |

## Session ID best practices

Choose session IDs that encode ownership and purpose. This makes auditing and cleanup much easier.

| Pattern | Example | Use Case |
|---------|---------|----------|
| ❌ `abc123` | Random IDs | Hard to audit, no ownership info |
| ✅ `user-{userId}-{taskId}` | `user-alice-pr-review-42` | Multi-user apps |
| ✅ `tenant-{tenantId}-{workflow}` | `tenant-acme-onboarding` | Multi-tenant SaaS |
| ✅ `{userId}-{taskId}-{timestamp}` | `alice-deploy-1706932800` | Time-based cleanup |

**Benefits of structured IDs:**
* Easy to audit: "Show all sessions for user alice"
* Easy to clean up: "Delete all sessions older than X"
* Natural access control: Parse user ID from session ID

### Example: generating session IDs

```typescript
function createSessionId(userId: string, taskType: string): string {
  const timestamp = Date.now();
  return `${userId}-${taskType}-${timestamp}`;
}

const sessionId = createSessionId("alice", "code-review");
// → "alice-code-review-1706932800000"
```

```python
import time

def create_session_id(user_id: str, task_type: str) -> str:
    timestamp = int(time.time())
    return f"{user_id}-{task_type}-{timestamp}"

session_id = create_session_id("alice", "code-review")
# → "alice-code-review-1706932800"
```

## Managing session lifecycle

### Listing active sessions

```typescript
// List all sessions
const sessions = await client.listSessions();
console.log(`Found ${sessions.length} sessions`);

for (const session of sessions) {
  console.log(`- ${session.sessionId} (created: ${session.createdAt})`);
}

// Filter sessions by repository
const repoSessions = await client.listSessions({ repository: "owner/repo" });
```

### Cleaning up old sessions

```typescript
async function cleanupExpiredSessions(maxAgeMs: number) {
  const sessions = await client.listSessions();
  const now = Date.now();
  
  for (const session of sessions) {
    const age = now - new Date(session.createdAt).getTime();
    if (age > maxAgeMs) {
      await client.deleteSession(session.sessionId);
      console.log(`Deleted expired session: ${session.sessionId}`);
    }
  }
}

// Clean up sessions older than 24 hours
await cleanupExpiredSessions(24 * 60 * 60 * 1000);
```

### Disconnecting from a session (`disconnect`)

When a task completes, disconnect from the session explicitly rather than waiting for timeouts. This releases in-memory resources but **preserves session data on disk**, so the session can still be resumed later:

```typescript
try {
  // Do work...
  await session.sendAndWait({ prompt: "Complete the task" });
  
  // Task complete — release in-memory resources (session can be resumed later)
  await session.disconnect();
} catch (error) {
  // Clean up even on error
  await session.disconnect();
  throw error;
}
```

Each SDK also provides idiomatic automatic cleanup patterns:

| Language | Pattern | Example |
|----------|---------|---------|
| **TypeScript** | `Symbol.asyncDispose` | `await using session = await client.createSession(config);` |
| **Python** | `async with` context manager | `async with await client.create_session(on_permission_request=handler) as session:` |
| **C#** | `IAsyncDisposable` | `await using var session = await client.CreateSessionAsync(config);` |
| **Go** | `defer` | `defer session.Disconnect()` |

> [!NOTE]
> `destroy()` is deprecated in favor of `disconnect()`. Existing code using `destroy()` will continue to work but should be migrated.

### Permanently deleting a session (`deleteSession`)

To permanently remove a session and all its data from disk (conversation history, planning state, artifacts), use `deleteSession`. This is irreversible—the session **cannot** be resumed after deletion:

```typescript
// Permanently remove session data
await client.deleteSession("user-123-task-456");
```

> **`disconnect()` vs `deleteSession()`:** `disconnect()` releases in-memory resources but keeps session data on disk for later resumption. `deleteSession()` permanently removes everything, including files on disk.

## Automatic cleanup: idle timeout

By default, sessions have **no idle timeout** and live indefinitely until explicitly disconnected or deleted. You can optionally configure a server-wide idle timeout via `CopilotClientOptions.sessionIdleTimeoutSeconds`:

```typescript
const client = new CopilotClient({
  sessionIdleTimeoutSeconds: 30 * 60, // 30 minutes
});
```

When a timeout is configured, sessions without activity for that duration are automatically cleaned up. Set to `0` or omit to disable.

> [!NOTE]
> This option only applies when the SDK spawns the runtime process. When connecting to an existing server via `cliUrl`, the server's own timeout configuration applies.

![Diagram: Flowchart showing the described process.](<https://github.com/assets/images/help/copilot/copilot-sdk/features-session-persistence-diagram-2.png>)

Sessions with active work (running commands, background agents) are always protected from idle cleanup, regardless of the timeout setting.

Listen for idle events to react to session inactivity:

```typescript
session.on("session.idle", (event) => {
  console.log(`Session idle for ${event.idleDurationMs}ms`);
});
```

## Deployment patterns

### Pattern 1: one CLI server per user (recommended)

Best for: Strong isolation, multi-tenant environments, Azure Dynamic Sessions.

![Diagram: Flowchart showing the described process.](<https://github.com/assets/images/help/copilot/copilot-sdk/features-session-persistence-diagram-3.png>)

**Benefits:** ✅ Complete isolation | ✅ Simple security | ✅ Easy scaling

### Pattern 2: shared CLI server (resource efficient)

Best for: Internal tools, trusted environments, resource-constrained setups.

![Diagram: Flowchart showing the described process.](<https://github.com/assets/images/help/copilot/copilot-sdk/features-session-persistence-diagram-4.png>)

**Requirements:**
* ⚠️ Unique session IDs per user
* ⚠️ Application-level access control
* ⚠️ Session ID validation before operations

```typescript
// Application-level access control for shared CLI
async function resumeSessionWithAuth(
  client: CopilotClient,
  sessionId: string,
  currentUserId: string
): Promise<Session> {
  // Parse user from session ID
  const [sessionUserId] = sessionId.split("-");
  
  if (sessionUserId !== currentUserId) {
    throw new Error("Access denied: session belongs to another user");
  }
  
  return client.resumeSession(sessionId);
}
```

## Azure dynamic sessions

For serverless/container deployments where containers can restart or migrate:

### Mount persistent storage

The session state directory must be mounted to persistent storage:

```yaml
# Azure Container Instance example
containers:
  - name: copilot-agent
    image: my-agent:latest
    volumeMounts:
      - name: session-storage
        mountPath: /home/app/.copilot/session-state

volumes:
  - name: session-storage
    azureFile:
      shareName: copilot-sessions
      storageAccountName: myaccount
```

![Diagram: Flowchart showing the described process.](<https://github.com/assets/images/help/copilot/copilot-sdk/features-session-persistence-diagram-5.png>)

**Session survives container restarts!**

## Infinite sessions for long-running workflows

For workflows that might exceed context limits, enable infinite sessions with automatic compaction:

```typescript
const session = await client.createSession({
  sessionId: "long-workflow-123",
  infiniteSessions: {
    enabled: true,
    backgroundCompactionThreshold: 0.80,  // Start compaction at 80% context
    bufferExhaustionThreshold: 0.95,      // Block at 95% if needed
  },
});
```

> [!NOTE]
> Thresholds are context utilization ratios (0.0-1.0), not absolute token counts. See the [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/troubleshooting/compatibility>) for details.

## Limitations and considerations

| Limitation | Description | Mitigation |
|------------|-------------|------------|
| **BYOK re-authentication** | API keys aren't persisted | Store keys in your secret manager; provide on resume |
| **Writable storage** | `~/.copilot/session-state/` must be writable | Mount persistent volume in containers |
| **No session locking** | Concurrent access to same session is undefined | Implement application-level locking or queue |
| **Tool state not persisted** | In-memory tool state is lost | Design tools to be stateless or persist their own state |

### Handling concurrent access

The SDK doesn't provide built-in session locking. If multiple clients might access the same session:

```typescript
// Option 1: Application-level locking with Redis
import Redis from "ioredis";

const redis = new Redis();

async function withSessionLock<T>(
  sessionId: string,
  fn: () => Promise<T>
): Promise<T> {
  const lockKey = `session-lock:${sessionId}`;
  const acquired = await redis.set(lockKey, "locked", "NX", "EX", 300);
  
  if (!acquired) {
    throw new Error("Session is in use by another client");
  }
  
  try {
    return await fn();
  } finally {
    await redis.del(lockKey);
  }
}

// Usage
await withSessionLock("user-123-task-456", async () => {
  const session = await client.resumeSession("user-123-task-456");
  await session.sendAndWait({ prompt: "Continue the task" });
});
```

## Summary

| Feature | How to Use |
|---------|------------|
| **Create resumable session** | Provide your own `sessionId` |
| **Resume session** | `client.resumeSession(sessionId)` |
| **BYOK resume** | Re-provide `provider` config |
| **List sessions** | `client.listSessions(filter?)` |
| **Disconnect from active session** | `session.disconnect()`—releases in-memory resources; session data on disk is preserved for resumption |
| **Delete session permanently** | `client.deleteSession(sessionId)`—permanently removes all session data from disk; cannot be resumed |
| **Containerized deployment** | Mount `~/.copilot/session-state/` to persistent storage |

## Next steps

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/hooks/hooks-overview>) - Customize session behavior with hooks
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/troubleshooting/compatibility>) - SDK vs CLI feature comparison
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/troubleshooting/debugging>) - Troubleshoot session issues


</details>
