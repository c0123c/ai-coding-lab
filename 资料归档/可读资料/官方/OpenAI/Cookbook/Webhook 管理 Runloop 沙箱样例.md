# Webhook 管理 Runloop 沙箱样例

通过控制器验证事件并排队，按会话启动或恢复独立工作环境，介绍生命周期和停止清理。

- 来源：OpenAI／Cookbook
- 资料类型：存档文档
- 存档版本：`5bd6cb40b6b8`。
- 文档时间：源码提交（存档版）：[2026-09-26](<https://github.com/openai/openai-cookbook/commit/6dc6324fb9ed780b32b787f23fad336e9f1eff15>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/sandboxes/runloop/webhook_managed/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Cookbook/examples/agents_api/sandboxes/runloop/webhook_managed/README.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Runloop webhook 管理的沙箱

专用 Runloop devbox 中的控制器接收签名的 OpenAI webhooks 和 
 队列在 SQLite 中工作。它为每个 
API 会话启动或恢复单独的工作开发盒。 Agent命令仅在工作线程中运行。

## 部署

使用 [ 共享设置 ](<Webhook 管理沙箱的总入口.md#设置一次>)
 和此文件夹的 `client.py` 创建 agent。在本地设置
`OPENAI_AGENT_ID`、`OPENAI_API_KEY`、`OPENAI_EXECUTOR_API_KEY`和`RUNLOOP_API_KEY`
。对 `OPENAI_EXECUTOR_API_KEY` 使用 
[ 环境密钥 ](<https://developers.openai.com/api/docs/guides/agents-api/environments/self-hosted#authentication>)
。

从 Cookbook 存储库根运行：

```bash
uv run examples/agents_api/sandboxes/runloop/webhook_managed/deploy.py
```

在**OpenAI项目设置→Webhooks**中注册打印的URL，订阅
`agent.session.action_required`和`agent.session.failed`。将生成的
`OPENAI_WEBHOOK_SECRET`设置在本地并再次部署。 **将网络钩子URL更新为
新打印的地址**，保持其签名秘密。然后使用 
[ 运行并重新连接步骤 ](<Webhook 管理沙箱的总入口.md#运行并重新连接>) 与 
 提供商本地客户端：

```bash
uv run examples/agents_api/sandboxes/runloop/webhook_managed/client.py \
  --agent-id "$OPENAI_AGENT_ID"
```

部署将凭证存储在带有 `agents_api_webhook_`
 前缀的 Runloop 机密中。控制器通过Runloop网关访问【OpenAI】；工作人员仅接收
作为`CODEX_API_KEY`的环境密钥，并直接连接到OpenAI。
端点拒绝交付，直到配置并验证每个签名。

控制器ID保存在`.controller.json`中。更改处理程序、共享 
`sandbox.py`、agent ID、Runloop 密钥或签名密钥将替换控制器 
 并更改其 URL。否则部署会重用它。部署脚本上传 
 两个 Python 文件，并保留其布局。

## 生命周期

|目前状态|处理程序动作|
| --- | --- |
|需要连接，无需工作人员|创建一个工作程序并启动执行程序。|
|需要连接，已暂停工作人员|恢复并重新启动执行器。|
|会话失败|关闭其工作人员。|
|其他 agent、已解决的操作或空闲事件|不要配置或停止计算。|

在完成排队作业之前，处理程序最多等待 60 秒的连接。
启动失败最多可尝试五次。运行一个控制器进程；其队列
在进程重新启动和suspend/resume中仍然存在，但在控制器更换后仍然存在。

## 停止或清理

在轮流之间暂停工作人员以保留其文件。下一个输入通过 webhook 将其唤醒 
。 30 分钟后，工作人员关闭。控制器在十分钟空闲后暂停
； HTTP请求唤醒它，第一个请求返回
`503`以供OpenAI重试。

要进行最终清理，请删除 API 会话并单独关闭其工作线程。
在关闭控制器之前删除 OpenAI webhook。当不再使用时，删除 
`agents-api-webhook-openai-controller` 网关和示例的 Runloop 机密 
。

在 Runloop devbox 日志中查找控制器日志，在每个工作线程内的 
`/tmp/codex-executor.log` 中查找执行器日志。

## 参考文献

- [Runloop 文档](<https://docs.runloop.ai/>)
- [运行循环 Python SDK](<https://runloopai.github.io/api-client-python/>)


## 原文对照

<details>
<summary>展开完整原文</summary>

# Runloop webhook-managed sandbox

A controller in a dedicated Runloop devbox receives signed OpenAI webhooks and
queues work in SQLite. It starts or resumes a separate worker devbox for each
API session. Agent commands run only in the worker.

## Deploy

Create an agent using the [shared setup](<Webhook 管理沙箱的总入口.md#set-up-once>)
and this folder's `client.py`. Set
`OPENAI_AGENT_ID`, `OPENAI_API_KEY`, `OPENAI_EXECUTOR_API_KEY`, and `RUNLOOP_API_KEY`
locally. Use an
[environment key](<https://developers.openai.com/api/docs/guides/agents-api/environments/self-hosted#authentication>)
for `OPENAI_EXECUTOR_API_KEY`.

Run from the Cookbook repository root:

```bash
uv run examples/agents_api/sandboxes/runloop/webhook_managed/deploy.py
```

Register the printed URL in **OpenAI project settings → Webhooks**, subscribing to
`agent.session.action_required` and `agent.session.failed`. Set the generated
`OPENAI_WEBHOOK_SECRET` locally and deploy again. **Update the webhook URL to the
newly printed address**, keeping its signing secret. Then use the
[run-and-reconnect steps](<Webhook 管理沙箱的总入口.md#run-and-reconnect>) with the
provider-local client:

```bash
uv run examples/agents_api/sandboxes/runloop/webhook_managed/client.py \
  --agent-id "$OPENAI_AGENT_ID"
```

Deployment stores credentials in Runloop secrets with the `agents_api_webhook_`
prefix. The controller accesses OpenAI through a Runloop gateway; workers receive
only the environment key as `CODEX_API_KEY` and connect directly to OpenAI.
The endpoint rejects deliveries until configured and verifies every signature.

The controller ID is saved in `.controller.json`. Changing the handler, shared
`sandbox.py`, agent ID, Runloop key, or signing secret replaces the controller
and changes its URL. Otherwise deployment reuses it. The deploy script uploads
both Python files, preserving their layout.

## Lifecycle

| Current state | Handler action |
| --- | --- |
| Connection required, no worker | Create a worker and launch the executor. |
| Connection required, suspended worker | Resume it and relaunch the executor. |
| Session failed | Shut down its worker. |
| Other agent, resolved action, or idle event | Do not provision or stop compute. |

The handler waits up to 60 seconds for connection before completing a queued job.
Failed startup gets up to five attempts. Run one controller process; its queue
survives process restarts and suspend/resume, but not controller replacement.

## Stop or clean up

Suspend a worker between turns to retain its files. The next input wakes it
through a webhook. Workers shut down after 30 minutes. The controller suspends
after ten idle minutes; HTTP requests wake it, with the first request returning
`503` for OpenAI to retry.

For final cleanup, delete the API session and shut down its worker separately.
Remove the OpenAI webhook before shutting down the controller. Delete the
`agents-api-webhook-openai-controller` gateway and the example's Runloop secrets
when no longer used.

Find controller logs in the Runloop devbox logs and executor logs in
`/tmp/codex-executor.log` inside each worker.

## References

- [Runloop documentation](<https://docs.runloop.ai/>)
- [Runloop Python SDK](<https://runloopai.github.io/api-client-python/>)


</details>
