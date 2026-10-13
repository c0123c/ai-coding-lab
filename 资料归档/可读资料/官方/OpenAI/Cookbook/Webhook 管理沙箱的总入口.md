# Webhook 管理沙箱的总入口

介绍应用创建会话、事件处理器启动重连沙箱、凭据、多个提供方及资源回收，帮助选择管理模式。

- 来源：OpenAI／Cookbook
- 资料类型：存档文档
- 存档版本：`5bd6cb40b6b8`。
- 文档时间：源码提交（存档版）：[2026-09-26](<https://github.com/openai/openai-cookbook/commit/6dc6324fb9ed780b32b787f23fad336e9f1eff15>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/sandboxes/webhook_managed.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Cookbook/examples/agents_api/sandboxes/webhook_managed.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Webhook 管理的沙箱

通过 Agents API 运行 agent 并让沙箱 
 提供商帐户中的 Webhook 处理程序启动并重新连接其沙箱。

## 它是如何运作的

- **应用程序：**每个提供商的`webhook_managed/client.py`创建会话，
发送输入并流式传输结果。它不导入任何提供者 SDK。
- ** 提供程序处理程序：** 部署一次，它接收 OpenAI webhook 并启动或
当需要环境连接时重新连接沙箱执行器。

## 选择处理程序

|提供者|处理程序代码|设置|
| --- | --- | --- |
|莫代尔| [handler.py](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/modal/webhook_managed/handler.py>) |[部署](<Webhook 管理 Modal 沙箱样例.md>)|
|韦尔塞尔|[api/webhook.ts](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/vercel/webhook_managed/api/webhook.ts>) 和 [api/provision.ts](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/vercel/webhook_managed/api/provision.ts>)|[部署](<Webhook 管理 Vercel 沙箱样例.md>)|
|云耀| [src/index.ts](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/cloudflare/webhook_managed/src/index.ts>) |[部署](<Webhook 管理 Cloudflare 沙箱样例.md>)|
|布莱克塞尔| [handler.py](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/blaxel/webhook_managed/handler.py>) |[部署](<Webhook 管理 Blaxel 沙箱样例.md>)|
|代托纳| [handler.py](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/daytona/webhook_managed/handler.py>) |[部署](<Webhook 管理 Daytona 沙箱样例.md>)|
| E2B | [handler.py](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/e2b/webhook_managed/handler.py>) |[部署](<Webhook 管理 E2B 沙箱样例.md>)|
|运行循环| [handler.py](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/runloop/webhook_managed/handler.py>) |[部署](<Webhook 管理 Runloop 沙箱样例.md>)|
| DigitalOcean | [handler.py](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/digitalocean/webhook_managed/handler.py>) |[部署](<Webhook 管理 DigitalOcean 沙箱样例.md>)|

完成以下设置，部署一个提供商的处理程序，然后运行其 
 客户端。要从应用程序直接配置，请使用
[应用程序管理的沙箱](<自托管 Agent 沙箱样例总入口.md>)。

## 设置一次

1. 安装【[uv](<https://docs.astral.sh/uv/getting-started/installation/>)】并选择
您的提供商文件夹。例如，从 Cookbook 存储库根目录：

   ```bash
   cd examples/agents_api/sandboxes/modal/webhook_managed
   ```

从该文件夹运行下面的客户端命令。每个 Python 脚本都内联声明 
 其依赖项； `uv run` 会自动安装它们。
2. 设置应用程序的 `OPENAI_API_KEY`，然后创建一个 agent：

   ```bash
   uv run client.py --create-agent sandbox-demo
   ```

输出示例：

   ```json
   {"agent_id": "agent_..."}
   ```

将 `OPENAI_AGENT_ID` 设置为返回的 `agent_id`，替换下面的占位符。
 如果您已有 agent，请改用其 ID。

   ```bash
   export OPENAI_AGENT_ID="agent_..."
   ```

在您的应用程序和处理程序中使用相同的 ID。 `OPENAI_AGENT_ID` 是特定于这些示例的过滤器 
：处理程序忽略其他 agents 的会话。
 默认模型是 `gpt-5.6-sol`；使用 
 不同的受支持模型创建 agent 时，请使用 `--model`。
3. 按照某一提供商的 README 配置凭据、部署其处理程序，以及
注册网络钩子。启用 `agent.session.action_required` 和 
 `agent.session.failed`，然后在运行会话之前安装签名密钥。

### 凭证

|变量|使用地点|
| --- | --- |
| `OPENAI_API_KEY` |应用程序和控制器。应用程序创建会话；控制器读取它们的当前状态。|
| `OPENAI_EXECUTOR_API_KEY` |[环境密钥](<https://developers.openai.com/api/docs/guides/agents-api/environments/self-hosted#authentication>)，作为`CODEX_API_KEY`传递给每个工作人员。|
| `OPENAI_WEBHOOK_SECRET` |仅控制器。验证您的 OpenAI 项目的交付。|

两个 API 密钥必须具有相同的组织、项目和用户或服务帐户 
 所有者。您的网络策略必须允许来自沙箱提供商的 API 请求。
将应用程序密钥和签名机密保留在工作沙箱之外。按照 README 中的描述配置 
 提供商凭据。

更改 API 密钥时，请更新提供程序的存储凭据并重新部署 
 处理程序。对现有的 Webhook 签名保密。来自同一项目
但不同用户或服务帐户的密钥无法注册会话的执行者。

## 运行并重新连接

```bash
uv run client.py \
  --agent-id "$OPENAI_AGENT_ID" \
  --input "Use the shell to write hello to /workspace/hello.txt, then read it."
```

客户端打印会话ID；将其另存为 `SESSION_ID` 以便后续输入。
要在停止或暂停沙箱后重新连接，请保留 API 会话并运行：

```bash
uv run client.py \
  --session-id "$SESSION_ID" \
  --input "Run a shell command to print hello again."
```

如果执行器离线，新输入会触发需要连接的 Webhook。
处理程序恢复或替换沙箱，具体取决于提供程序。输入
请求等待执行器；等待期间请勿再次提交。
OpenAI 最多等待五分钟；该客户端使用十分钟的 HTTP 超时。

## 使用多个提供商

每个处理程序管理一个 `OPENAI_AGENT_ID` 的会话。如果您在同一 OpenAI 项目中部署多个 
 个提供程序，请为每个提供程序分配不同的 agent。不要
让多个处理程序配置同一会话。

## 生命周期和清理

- `action_required` 与 `environment_connection`：检索当前状态，然后
仅当该操作仍然存在时才启动或重新连接。
- `failed`：仅当当前会话仍然失败时才清理沙箱。
- 其他事件：忽略。这些示例不会在 `idle` 上关闭。

默认情况下，工作人员的生命周期为 30 分钟或超时。这可以中断
活动回合；将限制设置为高于预期任务持续时间。 E2B在重新连接时刷新其
运行超时； Daytona 的 TTL 从创作开始算起。请参阅每个 
 提供商的 README 以了解持久性行为。替换沙箱不会恢复
其文件或重播中断的命令。

Blaxel、Daytona 和 E2B 控制器也有时间限制。在会话需要时保持控制器
可用。对于长时间运行的部署，提供持久的 
 控制器托管和队列存储、监视配置失败并协调耗尽重试的 
 作业。

完成后，stop/delete 提供者计算 ** 和 ** 删除 API 会话：

```bash
uv run client.py --session-id "$SESSION_ID" --delete
```

清理之前停止新输入。没有会话删除 Webhook，因此删除 
API 会话不会释放提供程序资源。停用控制器时，
首先删除其OpenAI webhook。处理应用程序中的输入超时； 
唤醒 Webhook 不保证在 API 重新启动后恢复待处理的输入。

有关共享协议和恢复限制，请参阅[管理沙箱生命周期](<https://developers.openai.com/api/docs/guides/agents-api/environments/lifecycle>)
。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Webhook-managed sandboxes

Run the agent through the Agents API and let a webhook handler in your sandbox
provider account start and reconnect its sandbox.

## How it works

- **Application:** each provider's `webhook_managed/client.py` creates sessions,
  sends input, and streams results. It imports no provider SDK.
- **Provider handler:** deployed once, it receives OpenAI webhooks and starts or
  reconnects the sandbox executor when an environment connection is required.

## Choose a handler

| Provider | Handler code | Setup |
| --- | --- | --- |
| Modal | [handler.py](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/modal/webhook_managed/handler.py>) | [Deploy](<Webhook 管理 Modal 沙箱样例.md>) |
| Vercel | [api/webhook.ts](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/vercel/webhook_managed/api/webhook.ts>) and [api/provision.ts](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/vercel/webhook_managed/api/provision.ts>) | [Deploy](<Webhook 管理 Vercel 沙箱样例.md>) |
| Cloudflare | [src/index.ts](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/cloudflare/webhook_managed/src/index.ts>) | [Deploy](<Webhook 管理 Cloudflare 沙箱样例.md>) |
| Blaxel | [handler.py](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/blaxel/webhook_managed/handler.py>) | [Deploy](<Webhook 管理 Blaxel 沙箱样例.md>) |
| Daytona | [handler.py](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/daytona/webhook_managed/handler.py>) | [Deploy](<Webhook 管理 Daytona 沙箱样例.md>) |
| E2B | [handler.py](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/e2b/webhook_managed/handler.py>) | [Deploy](<Webhook 管理 E2B 沙箱样例.md>) |
| Runloop | [handler.py](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/runloop/webhook_managed/handler.py>) | [Deploy](<Webhook 管理 Runloop 沙箱样例.md>) |
| DigitalOcean | [handler.py](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/digitalocean/webhook_managed/handler.py>) | [Deploy](<Webhook 管理 DigitalOcean 沙箱样例.md>) |

Complete the setup below, deploy one provider's handler, then run its
client. For direct provisioning from your application, use
[application-managed sandboxes](<自托管 Agent 沙箱样例总入口.md>).

## Set up once

1. Install [uv](<https://docs.astral.sh/uv/getting-started/installation/>) and choose
   your provider folder. For example, from the Cookbook repository root:

   ```bash
   cd examples/agents_api/sandboxes/modal/webhook_managed
   ```

   Run the client commands below from that folder. Each Python script declares
   its dependencies inline; `uv run` installs them automatically.
2. Set your application's `OPENAI_API_KEY`, then create an agent:

   ```bash
   uv run client.py --create-agent sandbox-demo
   ```

   Example output:

   ```json
   {"agent_id": "agent_..."}
   ```

   Set `OPENAI_AGENT_ID` to the returned `agent_id`, replacing the placeholder below.
   If you already have an agent, use its ID instead.

   ```bash
   export OPENAI_AGENT_ID="agent_..."
   ```

   Use the same ID in your application and handler. `OPENAI_AGENT_ID` is a filter
   specific to these examples: the handler ignores sessions for other agents.
   The default model is `gpt-5.6-sol`; use `--model` when creating an agent with a
   different supported model.
3. Follow one provider's README to configure credentials, deploy its handler, and
   register the webhook. Enable `agent.session.action_required` and
   `agent.session.failed`, then install the signing secret before running a session.

### Credentials

| Variable | Where it is used |
| --- | --- |
| `OPENAI_API_KEY` | Application and controller. The application creates sessions; the controller reads their current state. |
| `OPENAI_EXECUTOR_API_KEY` | An [environment key](<https://developers.openai.com/api/docs/guides/agents-api/environments/self-hosted#authentication>), passed to each worker as `CODEX_API_KEY`. |
| `OPENAI_WEBHOOK_SECRET` | Controller only. Verifies deliveries from your OpenAI project. |

The two API keys must have the same organization, project, and user or service-account
owner. Your network policies must allow API requests from the sandbox provider.
Keep the application key and signing secret out of worker sandboxes. Configure
provider credentials as described in its README.

When changing either API key, update the provider's stored credentials and redeploy
the handler. Keep the existing webhook signing secret. A key from the same project
but a different user or service account cannot register the session's executor.

## Run and reconnect

```bash
uv run client.py \
  --agent-id "$OPENAI_AGENT_ID" \
  --input "Use the shell to write hello to /workspace/hello.txt, then read it."
```

The client prints the session ID; save it as `SESSION_ID` for follow-up input.
To reconnect after stopping or pausing the sandbox, keep the API session and run:

```bash
uv run client.py \
  --session-id "$SESSION_ID" \
  --input "Run a shell command to print hello again."
```

If the executor is offline, the new input triggers a connection-required webhook.
The handler resumes or replaces the sandbox, depending on the provider. The input
request waits for the executor; do not submit it again while it is waiting.
OpenAI waits up to five minutes; this client uses a ten-minute HTTP timeout.

## Use multiple providers

Each handler manages sessions for one `OPENAI_AGENT_ID`. If you deploy more than
one provider in the same OpenAI project, assign a different agent to each. Do not
let multiple handlers provision the same session.

## Lifecycle and cleanup

- `action_required` with `environment_connection`: retrieve current state, then
  start or reconnect only if that action still exists.
- `failed`: clean up the sandbox only if the current session is still failed.
- Other events: ignored. These examples do not shut down on `idle`.

Workers have a 30-minute lifetime or timeout by default. This can interrupt an
active turn; set limits above your expected task duration. E2B refreshes its
running timeout on reconnect; Daytona's TTL counts from creation. See each
provider's README for persistence behavior. Replacing a sandbox does not restore
its files or replay interrupted commands.

The Blaxel, Daytona, and E2B controllers also have time limits. Keep the controller
available while sessions need it. For a long-running deployment, provide persistent
controller hosting and queue storage, monitor provisioning failures, and reconcile
jobs that exhaust their retries.

When finished, stop/delete provider compute **and** delete the API session:

```bash
uv run client.py --session-id "$SESSION_ID" --delete
```

Stop new input before cleanup. There is no session-deletion webhook, so removing
the API session does not release provider resources. When retiring a controller,
remove its OpenAI webhook first. Handle input timeouts in your application; the
wakeup webhook does not guarantee recovery of a pending input after an API restart.

See [Manage Sandbox Lifecycle](<https://developers.openai.com/api/docs/guides/agents-api/environments/lifecycle>)
for the shared protocol and recovery limits.


</details>
