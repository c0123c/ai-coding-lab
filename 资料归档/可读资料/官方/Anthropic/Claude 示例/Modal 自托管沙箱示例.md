# Modal 自托管沙箱示例

介绍 Webhook 接收、队列和执行器文件，涵盖配置、部署、测试及迭代；属于沙箱后端参考实现。

- 来源：Anthropic／Claude 示例
- 资料类型：存档文档
- 存档版本：`d7265d6ae994`。
- 文档时间：源码提交（存档版）：[2026-05-19](<https://github.com/anthropics/claude-cookbooks/commit/01b041a64c08190ef0f5359270d4966605831144>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/anthropics/claude-cookbooks/blob/main/managed_agents/self_hosted_sandboxes/modal/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude 示例/Modal 自托管沙箱示例.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 模态演示——自托管沙箱

[使用指南](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/self_hosted_sandboxes/docs/usage-guide.md>)在[Modal](<https://modal.com>)上的webhook流程的参考实现。两个文件：

- `modal_sandbox_webhook.py` - 模态应用程序接收 `session.status_run_started` webhook，使用 `client.beta.webhooks.unwrap()` 验证它，使用 `client.beta.environments.work.poller(drain=True, auto_stop=False)` 排空环境工作队列，并为每个项目启动每个会话模态沙箱。每个会话 `modal.Volume` 安装在 `/workspace` 上，因此 agent 的工作树和下载的技能在同一会话的沙箱重新启动后仍然存在。沙箱环境变量使用与 `ant beta:worker poll --on-work` 相同的 `ANTHROPIC_*` 合约。
- `sandbox_runner.py` — 在沙箱内运行：`client.beta.environments.work.worker(environment_key=..., workdir="/workspace", unrestricted_paths=True).handle_item()`。它读取 `ANTHROPIC_*` 环境变量，构建每个会话 `AgentToolContext` 并将 agent 的技能下载到 `/workspace/skills/<name>/`，运行 `SessionToolRunner`（心跳 + 协调 + 事件流 + `bash`/`read`/`write`/`edit`/`glob`/`grep`调度+结果发布），并在退出时强制停止工作项。空闲策略是SDK默认：`session.status_idle`和`stop_reason: end_turn`后60秒退出；任何其他事件都会重置时钟。

任何地方都没有组织 API 密钥：webhook 使用环境密钥进行轮询，运行程序使用相同的环境密钥进行身份验证 - 控制平面和每个会话调用的单一凭证。

## 先决条件

```shell
pip install modal
modal setup   # auth to your Modal workspace
```

## 配置

```shell
modal secret create cma-self-hosted-sandboxes-secrets \
    ANTHROPIC_WEBHOOK_SECRET=placeholder \
    ANTHROPIC_ENVIRONMENT_ID='env_...' \
    ANTHROPIC_ENVIRONMENT_KEY='sk-ant-oat...'
```

## 部署

```shell
modal deploy modal_sandbox_webhook.py
```

这将打印 `*.modal.run` URL。在控制台中（或通过 API）将 URL 注册为 `session.status_run_started` 的 Webhook，复制发布的密钥，然后更新：

```shell
modal secret create cma-self-hosted-sandboxes-secrets \
    ANTHROPIC_WEBHOOK_SECRET='whsec_...' \
    ANTHROPIC_ENVIRONMENT_ID='env_...' \
    ANTHROPIC_ENVIRONMENT_KEY='sk-ant-oat...' \
    --force
```

（无需重新部署 - 在容器启动时读取机密。）

## 测试

创建一个指向您的环境 ID 的会话并向其发送消息：

```py
session = client.beta.sessions.create(agent=agent_id, environment_id=ENVIRONMENT_ID)
client.beta.sessions.events.send(session.id, events=[{"type": "user.message", "content": "ls -la"}])
```

您应该按顺序看到：

```shell
modal app logs cma-self-hosted-sandboxes
# [webhook] event=session.status_run_started session_id=...
# [webhook] acked work=... session=... sandbox=sb-... (created)
```

沙箱标准输出（`[runner]` 行）显示在模态仪表板中的 
** 应用程序 → cma-self-hosted-sandboxes → 沙箱 ** 下。

## 迭代

编辑 Python 文件需要重新部署 (`modal deploy ...`)。仅编辑秘密则不然。要在迭代时强制重新开始，请在重新部署之前 `modal app stop cma-self-hosted-sandboxes`。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Modal demo — Self-Hosted Sandboxes

Reference implementation of the [usage guide](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/self_hosted_sandboxes/docs/usage-guide.md>)'s webhook flow on [Modal](<https://modal.com>). Two files:

- `modal_sandbox_webhook.py` — Modal app that receives the `session.status_run_started` webhook, verifies it with `client.beta.webhooks.unwrap()`, drains the environment work queue with `client.beta.environments.work.poller(drain=True, auto_stop=False)`, and spins up a per-session Modal Sandbox per item. A per-session `modal.Volume` is mounted at `/workspace` so the agent's working tree and downloaded skills persist across sandbox restarts for the same session. The sandbox env vars use the same `ANTHROPIC_*` contract as `ant beta:worker poll --on-work`.
- `sandbox_runner.py` — runs inside that Sandbox: `client.beta.environments.work.worker(environment_key=..., workdir="/workspace", unrestricted_paths=True).handle_item()`. It reads the `ANTHROPIC_*` env vars, builds the per-session `AgentToolContext` and downloads the agent's skills into `/workspace/skills/<name>/`, runs a `SessionToolRunner` (heartbeat + reconcile + event stream + `bash`/`read`/`write`/`edit`/`glob`/`grep` dispatch + result posting), and force-stops the work item on exit. Idle policy is the SDK default: it exits 60s after `session.status_idle` with `stop_reason: end_turn`; any other event resets the clock.

No org API key anywhere: the webhook polls with the environment key, and the runner authenticates with that same environment key — the single credential for both the control plane and the per-session calls.

## Prerequisites

```shell
pip install modal
modal setup   # auth to your Modal workspace
```

## Configure

```shell
modal secret create cma-self-hosted-sandboxes-secrets \
    ANTHROPIC_WEBHOOK_SECRET=placeholder \
    ANTHROPIC_ENVIRONMENT_ID='env_...' \
    ANTHROPIC_ENVIRONMENT_KEY='sk-ant-oat...'
```

## Deploy

```shell
modal deploy modal_sandbox_webhook.py
```

This prints a `*.modal.run` URL. Register that URL as a webhook for `session.status_run_started` in Console (or via the API), copy the issued secret, then update:

```shell
modal secret create cma-self-hosted-sandboxes-secrets \
    ANTHROPIC_WEBHOOK_SECRET='whsec_...' \
    ANTHROPIC_ENVIRONMENT_ID='env_...' \
    ANTHROPIC_ENVIRONMENT_KEY='sk-ant-oat...' \
    --force
```

(no redeploy needed — secrets are read at container start.)

## Test

Create a session pointing at your environment id and send it a message:

```py
session = client.beta.sessions.create(agent=agent_id, environment_id=ENVIRONMENT_ID)
client.beta.sessions.events.send(session.id, events=[{"type": "user.message", "content": "ls -la"}])
```

You should see, in order:

```shell
modal app logs cma-self-hosted-sandboxes
# [webhook] event=session.status_run_started session_id=...
# [webhook] acked work=... session=... sandbox=sb-... (created)
```

Sandbox stdout (the `[runner]` lines) shows in the Modal dashboard under
**Apps → cma-self-hosted-sandboxes → Sandboxes**.

## Iterating

Editing either Python file requires a redeploy (`modal deploy ...`). Editing only secrets does not. To force a clean slate while iterating, `modal app stop cma-self-hosted-sandboxes` before redeploying.


</details>
