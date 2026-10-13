# Webhook 管理 Modal 沙箱样例

说明事件签名验证、排队、串行配置和命名沙箱复用，并提供部署与清理操作。

- 来源：OpenAI／Cookbook
- 资料类型：存档文档
- 存档版本：`5bd6cb40b6b8`。
- 文档时间：源码提交（存档版）：[2026-09-26](<https://github.com/openai/openai-cookbook/commit/6dc6324fb9ed780b32b787f23fad336e9f1eff15>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/sandboxes/modal/webhook_managed/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Cookbook/examples/agents_api/sandboxes/modal/webhook_managed/README.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 模态 Webhook 管理的沙箱

端点验证 OpenAI 签名并为 Modal 函数排队。
一个配置工作线程按顺序处理请求并重用命名沙箱。

## 部署

从 Cookbook 存储库根目录：

```bash
uvx modal setup
```

使用 [ 共享设置 ](<Webhook 管理沙箱的总入口.md#设置一次>)
 与此提供商的 `webhook_managed/client.py` 创建 agent，然后在 Modal 的仪表板中创建 
 这些机密：

|秘密名字|变量|
| --- | --- |
| `agents-api-webhook-modal-controller` |共享设置中的 `OPENAI_API_KEY`、`OPENAI_AGENT_ID` — agent ID|
| `agents-api-webhook-modal-executor` |`CODEX_API_KEY` — 受限执行者密钥|
| `agents-api-webhook-modal-signing` | `OPENAI_WEBHOOK_SECRET` |

仅在获取 
 端点 URL 时使用 `pending-webhook-registration` 作为签名值。处理程序明确拒绝此占位符。切勿将真实的 
 凭据放入源文件中。

将 [modal_executor.py](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/modal/modal_executor.py>) 保留在模式目录旁边； 
控制器映像包含此用于远程导入的模块。

```bash
uv run examples/agents_api/sandboxes/modal/webhook_managed/handler.py
```

将打印的 URL 注册到 OpenAI，存储生成的签名密钥，然后再次运行 
 部署命令。然后按照[运行并重新连接](<Webhook 管理沙箱的总入口.md#运行并重新连接>)：

```bash
uv run examples/agents_api/sandboxes/modal/webhook_managed/client.py --agent-id "$OPENAI_AGENT_ID"
uv run examples/agents_api/sandboxes/modal/webhook_managed/client.py --session-id "$SESSION_ID" --input "Run another shell command."
```

轮换密钥时，编辑现有机密并重新部署。对于CLI，使用
`uvx modal secret create --force`来替换值； Python API 的 
`allow_existing=True` 不会更新现有密钥。

## 停止或清理

沙箱名称是 API 会话 ID。在 Modal 仪表板中终止它，或者
使用可用的 Modal 凭据运行此命令：

```python
import asyncio
import os
import modal


async def stop():
    try:
        sandbox = await modal.Sandbox.from_name.aio(
            "agents-api-webhook-modal", os.environ["SESSION_ID"]
        )
    except modal.exception.NotFoundError:
        return
    await sandbox.terminate.aio()


asyncio.run(stop())
```

对于最终清理，还要删除 API 会话。在 
 停止已部署的 Modal 应用程序之前删除 OpenAI webhook。替换沙箱从新的文件系统开始；
该示例不附加持久卷。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Modal webhook-managed sandbox

The endpoint verifies OpenAI signatures and queues work for a Modal function.
One provisioning worker processes requests in order and reuses named sandboxes.

## Deploy

From the Cookbook repository root:

```bash
uvx modal setup
```

Create the agent using the [shared setup](<Webhook 管理沙箱的总入口.md#set-up-once>)
with this provider's `webhook_managed/client.py`, then create
these secrets in Modal's dashboard:

| Secret name | Variables |
| --- | --- |
| `agents-api-webhook-modal-controller` | `OPENAI_API_KEY`, `OPENAI_AGENT_ID` — agent ID from the shared setup |
| `agents-api-webhook-modal-executor` | `CODEX_API_KEY` — the restricted executor key |
| `agents-api-webhook-modal-signing` | `OPENAI_WEBHOOK_SECRET` |

Use `pending-webhook-registration` for the signing value only while obtaining the
endpoint URL. The handler explicitly rejects this placeholder. Never put real
credentials in source files.

Keep [modal_executor.py](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/modal/modal_executor.py>) beside the mode directories; the
controller image includes this module for remote imports.

```bash
uv run examples/agents_api/sandboxes/modal/webhook_managed/handler.py
```

Register the printed URL with OpenAI, store the generated signing secret, and run
the deployment command again. Then follow [run and reconnect](<Webhook 管理沙箱的总入口.md#run-and-reconnect>):

```bash
uv run examples/agents_api/sandboxes/modal/webhook_managed/client.py --agent-id "$OPENAI_AGENT_ID"
uv run examples/agents_api/sandboxes/modal/webhook_managed/client.py --session-id "$SESSION_ID" --input "Run another shell command."
```

When rotating keys, edit the existing secrets and redeploy. With the CLI, use
`uvx modal secret create --force` to replace values; the Python API's
`allow_existing=True` does not update an existing secret.

## Stop or clean up

The sandbox name is the API session ID. Terminate it in the Modal dashboard, or
run this with Modal credentials available:

```python
import asyncio
import os
import modal


async def stop():
    try:
        sandbox = await modal.Sandbox.from_name.aio(
            "agents-api-webhook-modal", os.environ["SESSION_ID"]
        )
    except modal.exception.NotFoundError:
        return
    await sandbox.terminate.aio()


asyncio.run(stop())
```

For final cleanup, also delete the API session. Remove the OpenAI webhook before
stopping the deployed Modal app. Replacement sandboxes start with a fresh filesystem;
the example does not attach a persistent volume.


</details>
