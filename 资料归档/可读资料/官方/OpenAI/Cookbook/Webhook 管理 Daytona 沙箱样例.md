# Webhook 管理 Daytona 沙箱样例

用控制器接收事件并在 SQLite 排队，为各 API 会话启动或重连独立工作沙箱，说明部署与清理。

- 来源：OpenAI／Cookbook
- 资料类型：存档文档
- 存档版本：`5bd6cb40b6b8`。
- 文档时间：源码提交（存档版）：[2026-09-26](<https://github.com/openai/openai-cookbook/commit/6dc6324fb9ed780b32b787f23fad336e9f1eff15>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/sandboxes/daytona/webhook_managed/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Cookbook/examples/agents_api/sandboxes/daytona/webhook_managed/README.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Daytona webhook 管理的沙箱

专用 Daytona 沙箱中的控制器接收 OpenAI webhook 并在 SQLite 中队列 
 工作。它为每个 API
 会话启动或重新连接一个单独的工作沙箱。 Agent 命令仅在工作沙箱中运行。

工作人员使用 Daytona 的默认 JavaScript 快照。安装程序为 `daytona` 用户的 
 创建 `/workspace`，并在该用户的 `.local` 目录下安装 Codex alpha。

## 部署

使用 [ 共享设置 ](<Webhook 管理沙箱的总入口.md#设置一次>) 创建 agent。从 Cookbook 存储库根目录运行下面的 
 部署命令。

套装 `OPENAI_AGENT_ID` 到 agent ID 从共享设置。旁边提供
`DAYTONA_API_KEY`, `OPENAI_API_KEY`, 和 `OPENAI_EXECUTOR_API_KEY` 通过你的秘密
经理。两人 OpenAI 密钥必须与会话所有者的组织、项目、
以及用户或服务帐户。可选 `DAYTONA_API_URL` 和 `DAYTONA_TARGET` 是
传递给控制器。

```bash
uv run examples/agents_api/sandboxes/daytona/webhook_managed/deploy.py
```

在**OpenAI项目设置→Webhooks**中注册打印的URL，订阅
`agent.session.action_required`和`agent.session.failed`。将生成的
`OPENAI_WEBHOOK_SECRET`设置在本地，然后再次运行部署命令。使用
[运行和重新连接指南](<Webhook 管理沙箱的总入口.md#运行并重新连接>)与此
提供商的客户端创建会话并提交工作：

```bash
uv run examples/agents_api/sandboxes/daytona/webhook_managed/client.py --agent-id "$OPENAI_AGENT_ID"
```

不要在 Daytona 的 Webhooks 页面中注册此 URL：该页面向外发送 Daytona
 生命周期通知，而不是接收 OpenAI 事件。

公共端点拒绝请求，直到安装签名密钥并
检查每个交付的签名。仅控制器接收应用程序和
Daytona 凭证；会话沙箱仅接收受限制的执行程序密钥。
运行一个控制器进程。它的队列在进程重新启动后仍然存在，而不是控制器
删除。失败的配置最多可尝试五次。

## 生命周期

|目前状态|处理程序动作|
| --- | --- |
|需要连接，无需工作人员|创建一个工作程序并启动执行程序。|
|需要连接，已停止工作程序|启动同一个工作程序并重新启动执行程序。|
|会话失败|删除其工人。|
|其他 agent、已解决的操作或空闲事件|不要配置或停止计算。|

## 停止或清理

控制器记录每个会话的沙箱 ID。要在回合之间释放计算，
停止代托纳中的沙箱。下一个输入通过 
webhook 将其唤醒。停止会保留其文件系统，但会结束正在运行的进程。删除它
反而会导致下一个输入创建一个没有以前文件的新沙箱。

对于最终清理，请删除 API 会话及其 Daytona 沙箱分别。
删除 OpenAI 删除之前的 webhook `agents-api-webhook-daytona`
控制器。工人沙箱有一个 30分钟挂钟 TTL;控制器有
两个小时 TTL，因重新部署而延长。过期会破坏沙箱并且
它的本地队列和文件。对于长期运行的服务，提供持久托管
和控制器的队列存储。
如果控制器过期，请重新部署并更新 OpenAI webhook 与新的 URL.
参见 [代托纳生命周期设置](<https://www.daytona.io/docs/sandboxes#wall-clock-ttl>).

控制器日志位于`/app/controller.log`；执行器日志位于每个工作人员内的
`/tmp/codex-executor.log`中。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Daytona webhook-managed sandbox

A controller in a dedicated Daytona sandbox receives OpenAI webhooks and queues
work in SQLite. It starts or reconnects a separate worker sandbox for each API
session. Agent commands run only in the worker sandbox.

Workers use Daytona's default JavaScript snapshot. Setup creates `/workspace` for
the `daytona` user and installs the Codex alpha under that user's `.local` directory.

## Deploy

Create an agent using the [shared setup](<Webhook 管理沙箱的总入口.md#set-up-once>). Run the
deployment command below from the Cookbook repository root.

Set `OPENAI_AGENT_ID` to the agent ID from the shared setup. Provide it alongside
`DAYTONA_API_KEY`, `OPENAI_API_KEY`, and `OPENAI_EXECUTOR_API_KEY` through your secret
manager. The two OpenAI keys must match the session owner's organization, project,
and user or service account. Optional `DAYTONA_API_URL` and `DAYTONA_TARGET` are
passed to the controller.

```bash
uv run examples/agents_api/sandboxes/daytona/webhook_managed/deploy.py
```

Register the printed URL in **OpenAI project settings → Webhooks**, subscribing to
`agent.session.action_required` and `agent.session.failed`. Set the generated
`OPENAI_WEBHOOK_SECRET` locally and run the deployment command again. Use the
[run and reconnect guide](<Webhook 管理沙箱的总入口.md#run-and-reconnect>) with this
provider's client to create sessions and submit work:

```bash
uv run examples/agents_api/sandboxes/daytona/webhook_managed/client.py --agent-id "$OPENAI_AGENT_ID"
```

Do not register this URL in Daytona's Webhooks page: that page sends Daytona
lifecycle notifications outward, rather than receiving OpenAI events.

The public endpoint rejects requests until the signing secret is installed and
checks every delivery's signature. Only the controller receives application and
Daytona credentials; session sandboxes receive only the restricted executor key.
Run one controller process. Its queue survives process restarts, not controller
deletion. Failed provisioning gets up to five attempts.

## Lifecycle

| Current state | Handler action |
| --- | --- |
| Connection required, no worker | Create a worker and launch the executor. |
| Connection required, stopped worker | Start the same worker and relaunch the executor. |
| Session failed | Delete its worker. |
| Other agent, resolved action, or idle event | Do not provision or stop compute. |

## Stop or clean up

The controller logs each session's sandbox ID. To release compute between turns,
stop that sandbox in Daytona. The next input wakes it through the
webhook. Stopping retains its filesystem but ends running processes. Deleting it
instead causes the next input to create a fresh sandbox without previous files.

For final cleanup, delete the API session and its Daytona sandbox separately.
Remove the OpenAI webhook before deleting the `agents-api-webhook-daytona`
controller. Worker sandboxes have a 30-minute wall-clock TTL; the controller has
a two-hour TTL, extended on redeployment. Expiration destroys the sandbox and
its local queue and files. For a long-running service, provide persistent hosting
and queue storage for the controller.
If the controller expires, redeploy it and update the OpenAI webhook with the new URL.
See [Daytona lifecycle settings](<https://www.daytona.io/docs/sandboxes#wall-clock-ttl>).

Controller logs are in `/app/controller.log`; executor logs are in
`/tmp/codex-executor.log` inside each worker.


</details>
