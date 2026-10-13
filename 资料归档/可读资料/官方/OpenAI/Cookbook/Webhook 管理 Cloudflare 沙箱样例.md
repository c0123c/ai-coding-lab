# Webhook 管理 Cloudflare 沙箱样例

介绍签名校验、Durable Object 保存唤醒状态、重试和防止重复执行，展示持久化调度实现。

- 来源：OpenAI／Cookbook
- 资料类型：存档文档
- 存档版本：`5bd6cb40b6b8`。
- 文档时间：源码提交（存档版）：[2026-09-26](<https://github.com/openai/openai-cookbook/commit/6dc6324fb9ed780b32b787f23fad336e9f1eff15>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/sandboxes/cloudflare/webhook_managed/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Cookbook/examples/agents_api/sandboxes/cloudflare/webhook_managed/README.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Cloudflare webhook 管理的沙箱

Worker 验证签名并在每个会话的 Durable
 对象中保留唤醒。它的警报启动沙箱执行器。持久存储处理重试；
会话ID标识沙箱，OS锁可防止重复的执行程序。

## 部署

完成 [ 提供商设置 ](<Cloudflare 沙箱集成入口.md#先决条件>)。
使用 [ 共享设置 ](<Webhook 管理沙箱的总入口.md#设置一次>)
 以及此模式的 `client.py` 创建 agent。从 Cookbook 存储库根目录开始：

```bash
cd examples/agents_api/sandboxes/cloudflare
uv run webhook_managed/client.py --create-agent cloudflare-webhook-example
```

在 `webhook_managed/wrangler.jsonc` 中设置唯一的 Worker 名称和 `OPENAI_AGENT_ID`。
保留在 Cloudflare 提供程序目录中以获取其余命令。部署
并使用CLI的提示添加机密：

```bash
npm run deploy:webhook
npx wrangler secret put OPENAI_API_KEY --config webhook_managed/wrangler.jsonc
npx wrangler secret put OPENAI_EXECUTOR_API_KEY --config webhook_managed/wrangler.jsonc
npx wrangler secret put OPENAI_WEBHOOK_SECRET --config webhook_managed/wrangler.jsonc
npx wrangler secret put SANDBOX_CONTROL_TOKEN --config webhook_managed/wrangler.jsonc
```

对清理端点使用单独的随机控制令牌。仅对于第一个
部署，签名值可能是`pending-webhook-registration`； 
接收器拒绝它。 SDK 和 Dockerfile 使用匹配的沙箱版本。

将 `https://YOUR_WORKER.workers.dev/webhook` 注册为 OpenAI。使用 `npx wrangler secret put OPENAI_WEBHOOK_SECRET --config webhook_managed/wrangler.jsonc` 设置真实签名 
 秘密。
运行 [ 客户端并使用提供商本地客户端重新连接流程 ](<Webhook 管理沙箱的总入口.md#运行并重新连接>)
：

```bash
uv run webhook_managed/client.py --agent-id "$OPENAI_AGENT_ID"
uv run webhook_managed/client.py --session-id "$SESSION_ID" --input "Run another shell command."
```

重新部署时保持`OPENAI_AGENT_ID`设置；其他 agents 的会话将被忽略。

## 停止或清理

回合结束后调用经过身份验证的清理端点：

```python
import os
import httpx

response = httpx.delete(
    f"{os.environ['WORKER_URL']}/sandboxes/{os.environ['SESSION_ID']}",
    headers={"Authorization": f"Bearer {os.environ['SANDBOX_CONTROL_TOKEN']}"},
    timeout=60,
)
response.raise_for_status()
```

以下输入会触发新连接。最终清理还需要删除
API会话。控制器安排最大生命周期清理警报，因为
执行器使容器保持活动状态。替换容器具有新的文件系统。
在删除 Worker 及其容器之前删除 OpenAI webhook：

```bash
npx wrangler delete --config webhook_managed/wrangler.jsonc
```

请参阅[应用程序管理模式](<应用管理 Cloudflare 沙箱样例.md>)以进行直接配置。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Cloudflare webhook-managed sandbox

The Worker verifies the signature and persists a wakeup in a per-session Durable
Object. Its alarm starts the Sandbox executor. Durable storage handles retries;
the session ID identifies the sandbox, and an OS lock prevents duplicate executors.

## Deploy

Complete the [provider setup](<Cloudflare 沙箱集成入口.md#prerequisites>).
Create the agent using the [shared setup](<Webhook 管理沙箱的总入口.md#set-up-once>)
with this mode's `client.py`. Starting at the Cookbook repository root:

```bash
cd examples/agents_api/sandboxes/cloudflare
uv run webhook_managed/client.py --create-agent cloudflare-webhook-example
```

Set a unique Worker name and `OPENAI_AGENT_ID` in `webhook_managed/wrangler.jsonc`.
Stay in the Cloudflare provider directory for the remaining commands. Deploy
and add secrets using the CLI's prompts:

```bash
npm run deploy:webhook
npx wrangler secret put OPENAI_API_KEY --config webhook_managed/wrangler.jsonc
npx wrangler secret put OPENAI_EXECUTOR_API_KEY --config webhook_managed/wrangler.jsonc
npx wrangler secret put OPENAI_WEBHOOK_SECRET --config webhook_managed/wrangler.jsonc
npx wrangler secret put SANDBOX_CONTROL_TOKEN --config webhook_managed/wrangler.jsonc
```

Use a separate random control token for the cleanup endpoint. For the first
deployment only, the signing value may be `pending-webhook-registration`; the
receiver rejects it. The SDK and Dockerfile use matching Sandbox versions.

Register `https://YOUR_WORKER.workers.dev/webhook` with OpenAI. Set the real signing
secret using `npx wrangler secret put OPENAI_WEBHOOK_SECRET --config webhook_managed/wrangler.jsonc`.
Run the [client and reconnect flow](<Webhook 管理沙箱的总入口.md#run-and-reconnect>)
with the provider-local client:

```bash
uv run webhook_managed/client.py --agent-id "$OPENAI_AGENT_ID"
uv run webhook_managed/client.py --session-id "$SESSION_ID" --input "Run another shell command."
```

Keep `OPENAI_AGENT_ID` set when redeploying; sessions for other agents are ignored.

## Stop or clean up

Call the authenticated cleanup endpoint after a turn finishes:

```python
import os
import httpx

response = httpx.delete(
    f"{os.environ['WORKER_URL']}/sandboxes/{os.environ['SESSION_ID']}",
    headers={"Authorization": f"Bearer {os.environ['SANDBOX_CONTROL_TOKEN']}"},
    timeout=60,
)
response.raise_for_status()
```

A following input triggers a new connection. Final cleanup also requires deleting
the API session. The controller schedules a maximum-lifetime cleanup alarm because
the executor keeps the container alive. Replacement containers have a fresh filesystem.
Remove the OpenAI webhook before deleting the Worker and its containers:

```bash
npx wrangler delete --config webhook_managed/wrangler.jsonc
```

See the [application-managed mode](<应用管理 Cloudflare 沙箱样例.md>) for direct provisioning.


</details>
