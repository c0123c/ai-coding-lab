# Webhook 管理 E2B 沙箱样例

介绍专用控制器接收事件、SQLite 队列及独立工作沙箱的启动重连，说明生命周期与清理。

- 来源：OpenAI／Cookbook
- 资料类型：存档文档
- 存档版本：`5bd6cb40b6b8`。
- 文档时间：源码提交（存档版）：[2026-09-26](<https://github.com/openai/openai-cookbook/commit/6dc6324fb9ed780b32b787f23fad336e9f1eff15>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/sandboxes/e2b/webhook_managed/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Cookbook/examples/agents_api/sandboxes/e2b/webhook_managed/README.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# E2B webhook 管理的沙箱

专用 E2B 沙箱中的控制器接收 OpenAI webhooks 并在 SQLite 中队列工作 
。它为每个 API 会话启动或重新连接单独的工作沙箱。
Agent 命令仅在工作沙箱中运行。

## 部署

使用 [ 共享设置 ](<Webhook 管理沙箱的总入口.md#设置一次>) 创建 agent。从 Cookbook 存储库根目录运行下面的 
 部署命令。

套装 `OPENAI_AGENT_ID` 到 agent ID 从共享设置。旁边提供
`E2B_API_KEY`, `OPENAI_API_KEY`, 和 `OPENAI_EXECUTOR_API_KEY` 通过你的秘密
经理。两人 OpenAI 密钥必须与会话所有者的组织、项目、
以及用户或服务帐户。

```bash
uv run examples/agents_api/sandboxes/e2b/webhook_managed/deploy.py
```

部署将控制器 ID 保存在 
`deploy.py` 旁边被忽略的 `.controller.json` 中。保留该文件以重新部署同一控制器并保留其队列。
如果控制器过期，请在创建替换之前删除该文件；它的 
URL 发生了变化，因此也更新 OpenAI webhook。

在**OpenAI项目设置→Webhooks**中注册打印的URL，订阅
`agent.session.action_required`和`agent.session.failed`。在本地设置生成的
`OPENAI_WEBHOOK_SECRET`并重新部署。然后使用
[运行并重新连接指南](<Webhook 管理沙箱的总入口.md#运行并重新连接>)与此
提供商的客户端：

```bash
uv run examples/agents_api/sandboxes/e2b/webhook_managed/client.py --agent-id "$OPENAI_AGENT_ID"
```

E2B 的 Webhooks 页面用于传出沙箱生命周期通知；它不是您注册此 OpenAI 接收器的 
。

公共 Webhook 端点验证每个交付的签名。 Worker
沙箱禁用公共入站流量并仅接收受限执行器
密钥。应用程序密钥、E2B 密钥和签名密钥保留在控制器中。
运行一个控制器进程。它的队列在进程重新启动后仍然存在，而不是控制器
删除。失败的配置最多可尝试五次。

## 生命周期

|目前状态|处理程序动作|
| --- | --- |
|需要连接，无需工作人员|创建一个工作程序并启动执行程序。|
|需要连接，已暂停工作线程|恢复同一个工作线程并确保其执行程序正在运行。|
|会话失败|杀死它的工人，包括一个暂停的工人。|
|其他 agent、已解决的操作或空闲事件|不要配置或停止计算。|

## 停止或清理

控制器记录每个会话的沙箱 ID。要在回合之间释放计算，
通过E2B暂停沙箱；下一个输入使处理程序恢复
。相反，杀死它会导致下一个输入创建一个新的沙箱，而没有
以前的文件。处理程序通过 `agents-session-id` 元数据查找工作人员。

对于最终清理，请删除 API 会话并单独终止其 E2B 沙箱，
 包括暂停的工作线程。在杀死控制器之前删除 OpenAI webhook。
工作线程沙箱有一个 30 分钟的运行超时，重新连接时刷新； 
 控制器有一小时超时，重新部署后会延长。该示例不会
自动暂停或自动恢复控制器。它的 SQLite 队列在 
 进程重新启动后仍然存在，而不是沙箱删除。对于长时间运行的服务，请为控制器提供
持久托管和队列存储。

控制器日志位于`/app/controller.log`；执行器日志位于每个工作人员内的
`/tmp/codex-executor.log`中。


## 原文对照

<details>
<summary>展开完整原文</summary>

# E2B webhook-managed sandbox

A controller in a dedicated E2B sandbox receives OpenAI webhooks and queues work
in SQLite. It starts or reconnects a separate worker sandbox for each API session.
Agent commands run only in the worker sandbox.

## Deploy

Create an agent using the [shared setup](<Webhook 管理沙箱的总入口.md#set-up-once>). Run the
deployment command below from the Cookbook repository root.

Set `OPENAI_AGENT_ID` to the agent ID from the shared setup. Provide it alongside
`E2B_API_KEY`, `OPENAI_API_KEY`, and `OPENAI_EXECUTOR_API_KEY` through your secret
manager. The two OpenAI keys must match the session owner's organization, project,
and user or service account.

```bash
uv run examples/agents_api/sandboxes/e2b/webhook_managed/deploy.py
```

The deployment saves the controller ID in the ignored `.controller.json` beside
`deploy.py`. Keep that file to redeploy the same controller and retain its queue.
If the controller expires, remove this file before creating a replacement; its
URL changes, so update the OpenAI webhook too.

Register the printed URL in **OpenAI project settings → Webhooks**, subscribing to
`agent.session.action_required` and `agent.session.failed`. Set the generated
`OPENAI_WEBHOOK_SECRET` locally and redeploy. Then use the
[run and reconnect guide](<Webhook 管理沙箱的总入口.md#run-and-reconnect>) with this
provider's client:

```bash
uv run examples/agents_api/sandboxes/e2b/webhook_managed/client.py --agent-id "$OPENAI_AGENT_ID"
```

E2B's Webhooks page is for outgoing sandbox lifecycle notifications; it is not
where you register this OpenAI receiver.

The public webhook endpoint verifies each delivery's signature. Worker
sandboxes disable public inbound traffic and receive only the restricted executor
key. The application key, E2B key, and signing secret stay in the controller.
Run one controller process. Its queue survives process restarts, not controller
deletion. Failed provisioning gets up to five attempts.

## Lifecycle

| Current state | Handler action |
| --- | --- |
| Connection required, no worker | Create a worker and launch the executor. |
| Connection required, paused worker | Resume the same worker and ensure its executor is running. |
| Session failed | Kill its worker, including a paused worker. |
| Other agent, resolved action, or idle event | Do not provision or stop compute. |

## Stop or clean up

The controller logs each session's sandbox ID. To release compute between turns,
pause the sandbox through E2B; the next input makes the handler resume
it. Killing it instead causes the next input to create a fresh sandbox, without
previous files. The handler finds workers by their `agents-session-id` metadata.

For final cleanup, delete the API session and kill its E2B sandbox separately,
including paused workers. Remove the OpenAI webhook before killing the controller.
Worker sandboxes have a 30-minute running timeout, refreshed on reconnect; the
controller has a one-hour timeout, extended on redeployment. The example does not
automatically pause or auto-resume the controller. Its SQLite queue survives a
process restart, not sandbox deletion. For a long-running service, provide
persistent hosting and queue storage for the controller.

Controller logs are in `/app/controller.log`; executor logs are in
`/tmp/codex-executor.log` inside each worker.


</details>
