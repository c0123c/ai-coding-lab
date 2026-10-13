# Webhook 管理 Blaxel 沙箱样例

介绍签名验证、SQLite 排队、后台启动会话沙箱和控制器部署，说明队列持久化及资源删除的边界。

- 来源：OpenAI／Cookbook
- 资料类型：存档文档
- 存档版本：`5bd6cb40b6b8`。
- 文档时间：源码提交（存档版）：[2026-09-26](<https://github.com/openai/openai-cookbook/commit/6dc6324fb9ed780b32b787f23fad336e9f1eff15>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/sandboxes/blaxel/webhook_managed/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Cookbook/examples/agents_api/sandboxes/blaxel/webhook_managed/README.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Blaxel webhook 管理的沙箱

控制器沙箱公开了经过签名验证的 FastAPI 端点。它在返回 `200` 之前将工作 
 保存到 SQLite；一名后台工作人员启动会话沙箱。
队列在控制器进程重新启动后仍然存在，但在删除控制器后仍然存在。

## 部署

使用 [ 共享设置 ](<Webhook 管理沙箱的总入口.md#设置一次>) 创建 agent。从 Cookbook 存储库根目录运行下面的 
 部署命令。

通过您的秘密管理器提供这些环境变量：

- `BL_API_KEY`、`BL_WORKSPACE`：Blaxel 帐户凭据。
- `BL_REGION`：可选区域；控制器和工作人员默认为 `us-pdx-1`。
- `OPENAI_API_KEY`：controller/session-read 凭证。
- `OPENAI_AGENT_ID`：共享设置中的agent ID。
- `OPENAI_EXECUTOR_API_KEY`：受限工人凭证。
- `OPENAI_WEBHOOK_SECRET`：注册 Webhook 后签名秘密。

```bash
uv run examples/agents_api/sandboxes/blaxel/webhook_managed/deploy.py
```

该部署创建一个控制器并打印其公共 Webhook URL。在配置 
 签名密钥之前，端点将返回 `503`。将 URL 注册到 
OpenAI，设置真正的签名密钥，然后重新运行部署。然后使用
[运行并重新连接指南](<Webhook 管理沙箱的总入口.md#运行并重新连接>)与此
提供商的客户端：

```bash
uv run examples/agents_api/sandboxes/blaxel/webhook_managed/client.py --agent-id "$OPENAI_AGENT_ID"
```

更改凭据后重新运行部署；保留相同的签名秘密，除非
您在OpenAI中轮换它。

只有控制器接收应用程序和 Blaxel 凭证。会话
工作人员仅收到受限的执行者密钥。失败的配置最多可尝试
五次。控制器在两小时后失效。对于长时间运行的服务，
为控制器提供持久托管和队列存储。

## 停止或清理

处理程序记录每个会话的沙箱名称。在 Blaxel
 仪表板中删除该工作人员以在回合后释放计算。替换从新的文件系统开始。
要进行最终清理，请同时删除 API 会话。

完成后，删除 OpenAI webhook，然后删除 
`agents-api-webhook-controller` 沙箱以及本示例创建的任何剩余 
 工作线程。不要删除其他工作区沙箱。工作人员 
 还标有 Agents API 会话 ID，并具有 30 分钟 TTL。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Blaxel webhook-managed sandbox

A controller sandbox exposes a signature-verified FastAPI endpoint. It saves work
to SQLite before returning `200`; one background worker starts session sandboxes.
The queue survives controller process restarts, but not deletion of the controller.

## Deploy

Create an agent using the [shared setup](<Webhook 管理沙箱的总入口.md#set-up-once>). Run the
deployment command below from the Cookbook repository root.

Provide these environment variables through your secret manager:

- `BL_API_KEY`, `BL_WORKSPACE`: Blaxel account credentials.
- `BL_REGION`: optional region; defaults to `us-pdx-1` for controller and workers.
- `OPENAI_API_KEY`: controller/session-read credential.
- `OPENAI_AGENT_ID`: agent ID from the shared setup.
- `OPENAI_EXECUTOR_API_KEY`: restricted worker credential.
- `OPENAI_WEBHOOK_SECRET`: signing secret, once the webhook has been registered.

```bash
uv run examples/agents_api/sandboxes/blaxel/webhook_managed/deploy.py
```

The deployment creates a controller and prints its public webhook URL. Until the
signing secret is configured, the endpoint returns `503`. Register the URL with
OpenAI, set the real signing secret, and rerun deployment. Then use the
[run and reconnect guide](<Webhook 管理沙箱的总入口.md#run-and-reconnect>) with this
provider's client:

```bash
uv run examples/agents_api/sandboxes/blaxel/webhook_managed/client.py --agent-id "$OPENAI_AGENT_ID"
```

Rerun deployment after changing credentials; keep the same signing secret unless
you rotate it in OpenAI.

Only the controller receives the application and Blaxel credentials. Session
workers receive only the restricted executor key. Failed provisioning gets up to
five attempts. The controller expires after two hours. For a long-running service,
provide persistent hosting and queue storage for the controller.

## Stop or clean up

The handler logs each session's sandbox name. Delete that worker in the Blaxel
dashboard to release compute after a turn. A replacement starts with a fresh filesystem.
For final cleanup, delete the API session too.

When finished, remove the OpenAI webhook, then delete the
`agents-api-webhook-controller` sandbox and any remaining
workers created by this example. Do not delete other workspace sandboxes. Workers
are also labeled with their Agents API session ID and have a 30-minute TTL.


</details>
