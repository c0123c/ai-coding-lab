# 通过 Copilot SDK 创建云端会话

说明远端执行会话的创建、首次提示、仓库关联、恢复、权限与故障排查，并提供多语言调用示例。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-08-31](<https://github.com/github/docs/commit/b64e844d57a55484b670b1407122423f76b54720>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/copilot-sdk/features/cloud-sessions.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/通过 Copilot SDK 创建云端会话.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Cloud sessions
shortTitle: Cloud Sessions
intro: >-
  Cloud sessions run {% data variables.product.prodname_copilot_short %} work on {% data variables.product.github %}-hosted compute and appear in the
  agents panel on {% data variables.product.github %}. Use them when your app
  should create a session that executes remotely instead of starting a local
  {% data variables.copilot.copilot_cli %} session on the user's machine or your server.
versions:
  fpt: '*'
  ghec: '*'
contentType: how-tos
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

<!-- markdownlint-disable GHD046 GHD005 -->
<!-- Suppressed: GHD046 (outdated release terminology), GHD005 (hardcoded data variable) -->

## 先决条件

在创建云会话之前，请确保：

* 用户具有云 agent 权限的 Copilot 访问权限。
* 会话可以使用用户令牌或登录的 Copilot CLI 身份对 GitHub 进行身份验证。
* 您可以将会话与 GitHub 存储库关联。这在 SDK 类型中是可选的，但建议这样做，以便云 agent 具有正确的存储库上下文。
* 组织策略允许从云表面远程控制和查看会话。

## 创建云会话

设置 create-session `cloud` 选项以创建云会话。您可以包含存储库元数据以将云会话与 GitHub 存储库关联。

<!-- tabs:start -->

### TypeScript

```typescript
import { CopilotClient } from "@github/copilot-sdk";

const client = new CopilotClient();
await client.start();

const session = await client.createSession({
  onPermissionRequest: async () => ({ kind: "approve-once" }),
  cloud: {
    repository: {
      owner: "github",
      name: "copilot-sdk",
      branch: "main",
    },
  },
});
```

### Python

```python
from copilot import (
    CloudSessionOptions,
    CloudSessionRepository,
    CopilotClient,
    PermissionHandler,
)

client = CopilotClient()
await client.start()

session = await client.create_session(
    on_permission_request=PermissionHandler.approve_all,
    cloud=CloudSessionOptions(
        repository=CloudSessionRepository(
            owner="github",
            name="copilot-sdk",
            branch="main",
        )
    ),
)
```

### Go

```golang
client := copilot.NewClient(nil)
if err := client.Start(ctx); err != nil {
    return err
}

session, err := client.CreateSession(ctx, &copilot.SessionConfig{
    Cloud: &copilot.CloudSessionOptions{
        Repository: &copilot.CloudSessionRepository{
            Owner:  "github",
            Name:   "copilot-sdk",
            Branch: "main",
        },
    },
    OnPermissionRequest: func(req copilot.PermissionRequest, inv copilot.PermissionInvocation) (rpc.PermissionDecision, error) {
        return &rpc.PermissionDecisionApproveOnce{}, nil
    },
})
_ = session
```

### .NET

```csharp
await using var client = new CopilotClient();

var session = await client.CreateSessionAsync(new SessionConfig
{
    Cloud = new CloudSessionOptions
    {
        Repository = new CloudSessionRepository
        {
            Owner = "github",
            Name = "copilot-sdk",
            Branch = "main",
        },
    },
    OnPermissionRequest = (req, inv) =>
        Task.FromResult(PermissionDecision.ApproveOnce()),
});
```

### Java

```java
import com.github.copilot.CopilotClient;
import com.github.copilot.rpc.*;

try (var client = new CopilotClient()) {
    client.start().get();

    var session = client.createSession(
        new SessionConfig()
            .setCloud(new CloudSessionOptions()
                .setRepository(new CloudSessionRepository()
                    .setOwner("github")
                    .setName("copilot-sdk")
                    .setBranch("main")))
            .setOnPermissionRequest(PermissionHandler.APPROVE_ALL)
    ).get();
}
```

### Rust

```rust
use std::sync::Arc;
use github_copilot_sdk::{CloudSessionOptions, CloudSessionRepository, SessionConfig};
use github_copilot_sdk::handler::ApproveAllHandler;

let session = client.create_session(
    SessionConfig::default()
        .with_cloud(CloudSessionOptions::with_repository(
            CloudSessionRepository::new("github", "copilot-sdk").with_branch("main"),
        ))
        .with_permission_handler(Arc::new(ApproveAllHandler)),
).await?;
```

<!-- tabs:end -->

## 发送第一个提示

云会话分两个阶段初始化：agent 保留任务后，`createSession` 就会解析，但远程 `copilot-agent` 工作线程还需要一两秒的时间来连接并发出 `session.start`。如果您在此之前调用 `session.send`，则运行时的 `RemoteSession.send` 会抛出 `"Remote session is still starting"`，但架构包装器是即发即弃的，并且 ** 会默默地吞下错误 **，同时仍将新的 `messageId` 返回到您的代码。提示被丢弃在服务器上并且永远不会到达工作人员。

为了可靠地发送，请在**发送之前订阅事件**，并等待第一个`producer`为`"copilot-agent"`的`session.start`事件：

<!-- docs-validate: skip -->

```typescript
import { CopilotClient, type CopilotSession } from "@github/copilot-sdk";

const client = new CopilotClient();
await client.start();

const session: CopilotSession = await client.createSession({
  streaming: true, // required for assistant.message_delta to fire
  cloud: { repository: { owner: "github", name: "copilot-sdk" } },
  onPermissionRequest: async () => ({ kind: "approve-once" }),
});

// Subscribe BEFORE sending so you don't miss the start event.
const ready = new Promise<void>((resolve) => {
  const off = session.on("session.start", (event) => {
    if (event.data?.producer === "copilot-agent") {
      off();
      resolve();
    }
  });
});

await ready;
await session.send({ prompt: "Summarize the README" });
```

一些注意事项：

* 在 `createSession` 上设置 `streaming: true`，以便运行时发出 `assistant.message_delta` 事件。如果没有它，您获得的唯一辅助信号是最终的 `assistant.message` - 适合批量使用，但如果您渲染实时 UI，聊天会看起来冻结。参见[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/features/streaming-events>)。
* 只有**第一个**`session.send`对这个种族敏感。同一会话上的后续发送正常工作，因为运行时在会话生命周期内保持 `hasSessionStarted` 设置。
* 在 `ready` 承诺周围应用超时（例如 60 s），这样卡住的会话就不会永远挂起您的应用程序。
* 相同的模式适用于每种 SDK 语言 — 订阅 `session.start`，检查 `producer === "copilot-agent"`，然后调用 `send`。

## 访问 agent 会话 URL

云会话本质上是远程的：一旦工作线程连接，会话就会发布到 `https://github.com/copilot/tasks/{sessionId}`，并且运行时会发出带有 URL 的 `session.info` 事件。您**不需要**调用`remote.enable()`——API仅用于将本地会话同步到{% data variables.product.github %}。

通过订阅 `session.info` 并按 `infoType: "remote"` 过滤来捕获 URL：

<!-- docs-validate: skip -->

```typescript
session.on("session.info", (event) => {
  if (event.data?.infoType === "remote" && event.data.url) {
    console.log("Open from web or mobile:", event.data.url);
    // For example, surface in your UI as a shareable link or QR code.
  }
});
```

该事件在 `session.start` 之后不久触发。如果您的渲染器在事件已经触发后安装，请将 URL 与应用程序状态中的会话记录一起保留，并在重新安装时重新水化 - 运行时不会自行重新发出 `session.info`。

对于通过 `remote: true` 提升的本地会话上的相同接线，请参阅 [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/features/remote-sessions>)。

## 存储库关联

`cloud.repository` 对象将云会话与 GitHub 存储库关联：

|领域|必填|描述|
|-------|----------|-------------|
| `owner` |是的|存储库所有者或组织。|
| `name` |是的|存储库名称。|
| `branch` |否|用于存储库上下文的分支。省略它可以让运行时选择默认分支或当前存储库上下文。|

存储库关联在 SDK 类型中是可选的，但只要您的应用程序知道目标存储库，就包含它。它有助于会话在 agents 面板中显示正确的存储库上下文，并为云 agent 提供更清晰的起点。

当工作应从特定分支开始时，请使用 `branch`。如果您的应用程序从拉取请求、问题分类流或部署工作流创建会话，请传递与用户可见任务匹配的分支。

## 恢复云会话

`cloud` 选项仅在创建新会话时适用。要恢复现有云会话，请使用 SDK 语言的标准简历 API：

```typescript
const session = await client.resumeSession("session-id", {
  onPermissionRequest: async () => ({ kind: "approve-once" }),
});
```

简历中请勿再次通过`cloud`。保存的会话元数据确定该会话是云支持的，并且恢复遵循正常的会话恢复路径。

## 组织政策和权利

当用户或组织无权执行云agent 或组织级策略阻止流时，云会话创建可能会失败。特别是云沙箱的策略可以阻止客户端创建云任务。

发生这种情况时，运行时会报告云任务创建失败原因 `"policy_blocked"`。将此视为授权或策略结果，而不是暂时的基础设施故障。

在TypeScript中，重试前检查原因：

```typescript
try {
  await client.createSession({ cloud: { repository } });
} catch (error) {
  if ((error as { reason?: string }).reason === "policy_blocked") {
    // Show an admin-facing message or link to org policy settings.
  }
  throw error;
}
```

在 SDK 错误以不同方式表示的语言中，检查出现的错误原因或代码并显式处理 `"policy_blocked"`。在不改变策略的情况下重试预计不会成功。

## 集成ID和路由

云会话标有从 `GITHUB_COPILOT_INTEGRATION_ID` 环境变量派生的 `Copilot-Integration-Id` 标头。此集成 ID 用于路由、归因和特定于集成的行为。

有关多用户服务器指南和完整集成 ID 详细信息，请参阅 [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/setup/multi-tenancy>)。

SDK- 创建的云会话将路由到 `copilot-developer-sandbox` agent slug。该名称是云 agent 的内部路由 slug，并不意味着会话使用本地 Windows 沙箱。

## 高级：`COPILOT_MC_BASE_URL`

默认情况下，运行时从配置的 {% data variables.product.prodname_copilot_short %} API URL 派生 agent 会话基 URL。仅当您需要覆盖该会话端点时才设置 `COPILOT_MC_BASE_URL`。

GitHub Enterprise Server 部署可能需要这样做。在生产中依赖它之前，请与您的 GitHub 代表确认正确的值和支持状态。

```shell
COPILOT_MC_BASE_URL="https://example.com/agents"
```

## 云会话与远程会话

|能力|远程会话|云会议|
|------------|-----------------|----------------|
|执行地点|本地计算机或您的服务器|GitHub 托管计算|
|会话角色|将本地会话共享到 GitHub web/mobile|创建并路由托管会话|
|SDK 选项|`remote: true` 在客户端或会话上|`cloud: { ... }` 创建会话|
|恢复路径|标准简历|标准简历|
|Windows沙箱关系|无关的|无关的|

当会话应在 SDK 运行时已运行的情况下执行，但也可以从 {% data variables.product.github %} 上的 agents 面板访问时，请使用远程会话。当会话应在 GitHub 托管计算上执行时，请使用云会话。

## 故障排除

|症状|可能的原因|要检查什么|
|---------|--------------|---------------|
|云会话创建返回 `"policy_blocked"`|组织策略阻止从云流进行远程控制或查看|检查组织 Copilot 政策和用户权利|
|会话在没有存储库上下文的情况下创建|`cloud.repository` 被省略|通过 `owner`、`name` 和可选的 `branch`|
|恢复忽略新的 `cloud` 选项|`cloud`仅适用于新会话|正常恢复现有会话|
|与沙箱设置混淆|Windows 沙箱和云会话是分开的|请勿使用 `SANDBOX=true` 进行云执行|
|`session.send` 解析为 `messageId`，但没有触发 `assistant.*` 事件，并且会话日志中没有出现任何提示|session.send 领先于远程工作人员的 `session.start`；运行时吞没了提示|在发送之前等待第一个带有 `producer === "copilot-agent"` 的 `session.start` 事件。请参阅[发送第一个提示](<#发送第一个提示>)|
|即使云工作人员正在处理，实时 UI 也不会更新|`streaming` 未在 `createSession` 上设置，因此仅发出最终的 `assistant.message`|在`createSession`上设置`streaming: true`并重新启动|
|云会话有效，但您的 UI 中没有出现可共享的 URL|应用程序从未订阅过 URL 的 `session.info`|订阅`session.info`并过滤`infoType === "remote"`。请参阅[访问agent会话URL](<#访问-agent-会话-url>)|

## 另请参阅

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/features/remote-sessions>)：将本地托管的会话共享到 {% data variables.product.github %} 上的 agents 面板
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/features/streaming-events>)：订阅`assistant.*`实时UI渲染的增量
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/setup/multi-tenancy>)：集成 ID 和服务器部署模式
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/auth>)：为SDK会话配置GitHub身份验证


## 原文对照

<details>
<summary>展开完整原文</summary>

<!-- markdownlint-disable GHD046 GHD005 -->
<!-- Suppressed: GHD046 (outdated release terminology), GHD005 (hardcoded data variable) -->

## Prerequisites

Before creating a cloud session, make sure:

* The user has Copilot access with cloud-agent entitlement.
* The session can authenticate to GitHub, either with a user token or a logged-in Copilot CLI identity.
* You can associate the session with a GitHub repository. This is optional in the SDK type, but recommended so the cloud agent has the correct repository context.
* Organization policies allow remote control and viewing sessions from cloud surfaces.

## Creating a cloud session

Set the create-session `cloud` option to create a cloud session. You can include repository metadata to associate the cloud session with a GitHub repository.

<!-- tabs:start -->

### TypeScript

```typescript
import { CopilotClient } from "@github/copilot-sdk";

const client = new CopilotClient();
await client.start();

const session = await client.createSession({
  onPermissionRequest: async () => ({ kind: "approve-once" }),
  cloud: {
    repository: {
      owner: "github",
      name: "copilot-sdk",
      branch: "main",
    },
  },
});
```

### Python

```python
from copilot import (
    CloudSessionOptions,
    CloudSessionRepository,
    CopilotClient,
    PermissionHandler,
)

client = CopilotClient()
await client.start()

session = await client.create_session(
    on_permission_request=PermissionHandler.approve_all,
    cloud=CloudSessionOptions(
        repository=CloudSessionRepository(
            owner="github",
            name="copilot-sdk",
            branch="main",
        )
    ),
)
```

### Go

```golang
client := copilot.NewClient(nil)
if err := client.Start(ctx); err != nil {
    return err
}

session, err := client.CreateSession(ctx, &copilot.SessionConfig{
    Cloud: &copilot.CloudSessionOptions{
        Repository: &copilot.CloudSessionRepository{
            Owner:  "github",
            Name:   "copilot-sdk",
            Branch: "main",
        },
    },
    OnPermissionRequest: func(req copilot.PermissionRequest, inv copilot.PermissionInvocation) (rpc.PermissionDecision, error) {
        return &rpc.PermissionDecisionApproveOnce{}, nil
    },
})
_ = session
```

### .NET

```csharp
await using var client = new CopilotClient();

var session = await client.CreateSessionAsync(new SessionConfig
{
    Cloud = new CloudSessionOptions
    {
        Repository = new CloudSessionRepository
        {
            Owner = "github",
            Name = "copilot-sdk",
            Branch = "main",
        },
    },
    OnPermissionRequest = (req, inv) =>
        Task.FromResult(PermissionDecision.ApproveOnce()),
});
```

### Java

```java
import com.github.copilot.CopilotClient;
import com.github.copilot.rpc.*;

try (var client = new CopilotClient()) {
    client.start().get();

    var session = client.createSession(
        new SessionConfig()
            .setCloud(new CloudSessionOptions()
                .setRepository(new CloudSessionRepository()
                    .setOwner("github")
                    .setName("copilot-sdk")
                    .setBranch("main")))
            .setOnPermissionRequest(PermissionHandler.APPROVE_ALL)
    ).get();
}
```

### Rust

```rust
use std::sync::Arc;
use github_copilot_sdk::{CloudSessionOptions, CloudSessionRepository, SessionConfig};
use github_copilot_sdk::handler::ApproveAllHandler;

let session = client.create_session(
    SessionConfig::default()
        .with_cloud(CloudSessionOptions::with_repository(
            CloudSessionRepository::new("github", "copilot-sdk").with_branch("main"),
        ))
        .with_permission_handler(Arc::new(ApproveAllHandler)),
).await?;
```

<!-- tabs:end -->

## Sending the first prompt

Cloud sessions initialize in two phases: `createSession` resolves as soon as the agent has reserved the task, but the remote `copilot-agent` worker takes another second or two to connect and emit `session.start`. If you call `session.send` before that, the runtime's `RemoteSession.send` throws `"Remote session is still starting"`, but the schema wrapper is fire-and-forget and **silently swallows the error** while still returning a fresh `messageId` to your code. The prompt is dropped on the server and never reaches the worker.

To send reliably, subscribe to events **before** sending and await the first `session.start` event whose `producer` is `"copilot-agent"`:

<!-- docs-validate: skip -->

```typescript
import { CopilotClient, type CopilotSession } from "@github/copilot-sdk";

const client = new CopilotClient();
await client.start();

const session: CopilotSession = await client.createSession({
  streaming: true, // required for assistant.message_delta to fire
  cloud: { repository: { owner: "github", name: "copilot-sdk" } },
  onPermissionRequest: async () => ({ kind: "approve-once" }),
});

// Subscribe BEFORE sending so you don't miss the start event.
const ready = new Promise<void>((resolve) => {
  const off = session.on("session.start", (event) => {
    if (event.data?.producer === "copilot-agent") {
      off();
      resolve();
    }
  });
});

await ready;
await session.send({ prompt: "Summarize the README" });
```

A few notes:

* Set `streaming: true` on `createSession` so the runtime emits `assistant.message_delta` events. Without it, the only assistant signal you get is the final `assistant.message` — fine for batch use, but the chat will look frozen if you're rendering a live UI. See [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/features/streaming-events>).
* Only the **first** `session.send` is sensitive to this race. Subsequent sends on the same session work normally because the runtime keeps `hasSessionStarted` set for the life of the session.
* Apply a timeout (e.g. 60 s) around the `ready` promise so a stuck session doesn't hang your app forever.
* The same pattern works in every SDK language — subscribe to `session.start`, check `producer === "copilot-agent"`, then call `send`.

## Accessing the agent session URL

Cloud sessions are inherently remote: once the worker connects, the session is published to `https://github.com/copilot/tasks/{sessionId}` and the runtime emits a `session.info` event with the URL. You do **not** need to call `remote.enable()`— that API is only for syncing a local session to {% data variables.product.github %}.

Capture the URL by subscribing to `session.info` and filtering by `infoType: "remote"`:

<!-- docs-validate: skip -->

```typescript
session.on("session.info", (event) => {
  if (event.data?.infoType === "remote" && event.data.url) {
    console.log("Open from web or mobile:", event.data.url);
    // For example, surface in your UI as a shareable link or QR code.
  }
});
```

The event fires shortly after `session.start`. If your renderer mounts after the event has already fired, persist the URL alongside the session record in your app's state and rehydrate on remount — the runtime does not re-emit `session.info` on its own.

For the same wiring on local sessions promoted via `remote: true`, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/features/remote-sessions>).

## Repository association

The `cloud.repository` object associates the cloud session with a GitHub repository:

| Field | Required | Description |
|-------|----------|-------------|
| `owner` | Yes | Repository owner or organization. |
| `name` | Yes | Repository name. |
| `branch` | No | Branch to use for repository context. Omit it to let the runtime choose the default branch or current repository context. |

Repository association is optional in the SDK type, but include it whenever your app knows the target repository. It helps the session appear with the right repository context in the agents panel and gives the cloud agent a clearer starting point.

Use `branch` when the work should start from a specific branch. If your app is creating sessions from pull requests, issue triage flows, or deployment workflows, pass the branch that matches the user-visible task.

## Resuming a cloud session

The `cloud` option only applies when creating a new session. To resume an existing cloud session, use the standard resume API for the SDK language:

```typescript
const session = await client.resumeSession("session-id", {
  onPermissionRequest: async () => ({ kind: "approve-once" }),
});
```

Do not pass `cloud` again on resume. The saved session metadata determines that the session is cloud-backed, and resume follows the normal session resume path.

## Org policies and entitlements

Cloud session creation can fail when the user or organization is not entitled to cloud-agent execution or when organization-level policies block the flow. In particular, policies for cloud sandbox can prevent clients from creating the cloud task.

When this happens, the runtime reports a `"policy_blocked"` failure reason for cloud task creation. Treat this as an authorization or policy outcome, not as a transient infrastructure failure.

In TypeScript, check for the reason before retrying:

```typescript
try {
  await client.createSession({ cloud: { repository } });
} catch (error) {
  if ((error as { reason?: string }).reason === "policy_blocked") {
    // Show an admin-facing message or link to org policy settings.
  }
  throw error;
}
```

In languages where SDK errors are represented differently, inspect the surfaced error reason or code and handle `"policy_blocked"` explicitly. Retrying without a policy change is not expected to succeed.

## Integration ID and routing

Cloud sessions are stamped with a `Copilot-Integration-Id` header derived from the `GITHUB_COPILOT_INTEGRATION_ID` environment variable. This integration ID is used for routing, attribution, and integration-specific behavior.

For multi-user server guidance and full integration ID details, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/setup/multi-tenancy>).

SDK-created cloud sessions are routed to the `copilot-developer-sandbox` agent slug. The name is an internal routing slug for the cloud agent and does not mean the session uses the local Windows sandbox.

## Advanced: `COPILOT_MC_BASE_URL`

By default, the runtime derives the agent session base URL from the configured {% data variables.product.prodname_copilot_short %} API URL. Set `COPILOT_MC_BASE_URL` only when you need to override that session endpoint.

This may be required for GitHub Enterprise Server deployments. Confirm the correct value and support status with your GitHub representative before relying on it in production.

```shell
COPILOT_MC_BASE_URL="https://example.com/agents"
```

## Cloud sessions vs. remote sessions

| Capability | Remote sessions | Cloud sessions |
|------------|-----------------|----------------|
| Execution location | Local machine or your server | GitHub-hosted compute |
| Session role | Shares a local session to GitHub web/mobile | Creates and routes the hosted session |
| SDK option | `remote: true` on the client or session | `cloud: { ... }` on create session |
| Resume path | Standard resume | Standard resume |
| Windows sandbox relation | Unrelated | Unrelated |

Use remote sessions when the session should execute where the SDK runtime is already running, but also be accessible from the agents panel on {% data variables.product.github %}. Use cloud sessions when the session should execute on GitHub-hosted compute.

## Troubleshooting

| Symptom | Likely cause | What to check |
|---------|--------------|---------------|
| Cloud session creation returns `"policy_blocked"` | Organization policy blocks remote control or view from cloud flows | Check org Copilot policies and user entitlement |
| Session creates without repository context | `cloud.repository` was omitted | Pass `owner`, `name`, and optionally `branch` |
| Resume ignores a new `cloud` option | `cloud` only applies to new sessions | Resume the existing session normally |
| Confusion with sandbox settings | Windows sandbox and cloud sessions are separate | Do not use `SANDBOX=true` for cloud execution |
| `session.send` resolves with a `messageId` but no `assistant.*` events fire and no prompt appears in the session log | The session.send raced ahead of `session.start` from the remote worker; the runtime swallowed the prompt | Await the first `session.start` event with `producer === "copilot-agent"` before sending. See [Sending the first prompt](<#sending-the-first-prompt>) |
| Live UI never updates even though the cloud worker is processing | `streaming` was not set on `createSession`, so only the final `assistant.message` is emitted | Set `streaming: true` on `createSession` and re-launch |
| Cloud session works but no shareable URL appears in your UI | App never subscribed to `session.info` for the URL | Subscribe to `session.info` and filter `infoType === "remote"`. See [Accessing the agent session URL](<#accessing-the-agent-session-url>) |

## See also

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/features/remote-sessions>): share locally hosted sessions to the agents panel on {% data variables.product.github %}
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/features/streaming-events>): subscribe to `assistant.*` deltas for live UI rendering
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/setup/multi-tenancy>): integration IDs and server deployment patterns
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/auth>): configure GitHub authentication for SDK sessions


</details>
