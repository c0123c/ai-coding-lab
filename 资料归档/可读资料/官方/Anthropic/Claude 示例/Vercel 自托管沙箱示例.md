# Vercel 自托管沙箱示例

展示签名验证、队列和会话执行器的配置与部署，说明测试和平台限制；属于工程集成样例。

- 来源：Anthropic／Claude 示例
- 资料类型：存档文档
- 存档版本：`d7265d6ae994`。
- 文档时间：源码提交（存档版）：[2026-05-19](<https://github.com/anthropics/claude-cookbooks/commit/01b041a64c08190ef0f5359270d4966605831144>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/anthropics/claude-cookbooks/blob/main/managed_agents/self_hosted_sandboxes/vercel/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude 示例/Vercel 自托管沙箱示例.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Vercel 演示 — 自托管沙箱

与 Modal、Daytona、
 和 Cloudflare 演示相同的 webhook → 排水队列 → 每会话运行程序形状，但运行程序是一个 **Vercel 沙盒，运行 TS
`EnvironmentWorker.handleItem()`** 和 SDK 的 `betaAgentToolset20260401()` 
沙箱的真实文件系统（`bash` / `read` / `write` / `edit` / `glob` / `grep`）。

webhook 函数 (`api/webhook.ts`) 只是一个唤醒信号。它使用 `client.beta.webhooks.unwrap()` 验证 
 标准 Webhooks 签名，排出 
`work.poll()` 直至为空（上限为 25），并且每个项目：acks，创建一个 Vercel
 沙箱，上传 `runner/runner.mjs`，然后启动它超然的。不良工作项被 
 记录并跳过 — 它保持未确认状态并在下一个 Webhook 上回收，因此它 
 无法插入队列的其余部分。

空闲策略是SDK默认值：调度程序在
`session.status_idle`和`stop_reason: end_turn`后60秒退出；任何其他事件（包括 
`requires_action` 空闲，其中 agent 在沙箱上被阻止）都会重置 
 时钟。

任何地方都没有组织 API 密钥：webhook 使用环境密钥进行轮询，并且 
 运行程序使用相同的环境密钥进行身份验证 - 
 控制平面和每个会话调用的单个凭据。

## 文件

- `api/webhook.ts` — Vercel 功能：验证 sig → 排出 → 每个项目生成沙箱。
- `runner/runner.mjs` — 在沙箱内运行：`EnvironmentWorker.handleItem()` —
构建每个会话 `AgentToolContext`，下载 agent 的技能，在心跳时运行 
 `SessionToolRunner`，在退出时强制停止工作项。

## 先决条件

- `npm`、[Vercel CLI](<https://vercel.com/docs/cli>) (`npm i -g vercel`)
- 链接的 Vercel 项目 (`vercel link`)
- 已注册的自托管环境+其环境密钥

## 配置

```sh
npm install
vercel link
vercel env add ANTHROPIC_WEBHOOK_SECRET   # placeholder for now
vercel env add ANTHROPIC_ENVIRONMENT_ID
vercel env add ANTHROPIC_ENVIRONMENT_KEY
```

## 部署

```sh
vercel deploy --prod
```

这将打印 `https://<project>.vercel.app` URL。在控制台中（或通过API）将
`https://<project>.vercel.app/api/webhook`注册为
`session.status_run_started`的webhook，复制发布的
秘密，然后更新：

```sh
vercel env rm ANTHROPIC_WEBHOOK_SECRET production
vercel env add ANTHROPIC_WEBHOOK_SECRET production
vercel deploy --prod
```

## 测试

创建一个指向您的环境 ID 的会话并向其发送消息：

```py
session = client.beta.sessions.create(agent=agent_id, environment_id=ENVIRONMENT_ID)
client.beta.sessions.events.send(session.id, events=[{"type": "user.message", "content": "ls -la"}])
```

您应该按顺序看到：

```
vercel logs <project>.vercel.app
# [webhook] event=session.status_run_started session_id=...
# [webhook] acked work=... session=... sandbox=sbx_... (created)
```

沙盒 stdout/stderr（`[runner]` 行）显示在 Vercel 仪表板中项目的 
**可观察性 → 沙盒 ** 选项卡下。

## 注释

- `Sandbox.create()` + `writeFiles()` + `npm install` 是 ~15–25 秒之前
功能响应； `vercel.json` 设置 `maxDuration: 60`。跑步者本身是
 `detached`。该函数还将运行器的前 30 秒输出镜像到 
 函数日志中，因此可以从 `vercel logs` 进行调试，而无需使用沙箱 
 可观察性选项卡。
- **沙箱重用** - Vercel Sandbox没有原生tag/label/get-by-name API，所以
重用需要外部状态。如果附加了 [Vercel KV](<https://vercel.com/storage/kv>)
 存储（设置了 `KV_REST_API_URL`/`KV_REST_API_TOKEN`），则 webhook 
 会记录 `session_id → sandbox_id` 并在下一个 
 上重用正在运行的沙箱`run_started`，调用 `extendTimeout()`，以便活动会话在轮流中保持其 VM
 处于活动状态。如果没有 KV，每次交付都会创建一个新的沙箱 - 
 `SessionToolRunner` 通过 `seen`/`answered` 进行重复数据删除，因此重复的运行程序是 
 浪费，而不是错误。
- **工作目录** — webhook 在以下位置创建 `/mnt/session` 和 `/workspace`
沙箱启动并使用 `/workspace` 作为调度工作目录，匹配 
 Modal/CF 演示（技能下载到 `/workspace/skills/<name>/`）。这些是 
 普通临时目录，* 不是 * 持久卷 — Vercel Sandbox 没有 
 卷 API。当沙箱停止时，agent 的工作树就会消失。
- `npm install` 在生成时在沙箱内运行，因此该演示没有构建
步骤。要缩短冷启动约 10 秒的时间，请将 `runner/runner.mjs` 与 
 esbuild 预捆绑，然后上传该捆绑包。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Vercel demo — Self-Hosted Sandboxes

Same webhook → drain-queue → per-session runner shape as the Modal, Daytona,
and Cloudflare demos, but the runner is a **Vercel Sandbox running the TS
`EnvironmentWorker.handleItem()`** with the SDK's `betaAgentToolset20260401()` against the
sandbox's real filesystem (`bash` / `read` / `write` / `edit` / `glob` / `grep`).

The webhook function (`api/webhook.ts`) is a wake-up signal only. It verifies
the Standard Webhooks signature with `client.beta.webhooks.unwrap()`, drains
`work.poll()` until empty (capped at 25), and per item: acks, creates a Vercel
Sandbox, uploads `runner/runner.mjs`, and starts it detached. A bad work item is
logged and skipped — it stays un-acked and reclaims on the next webhook, so it
can't wedge the rest of the queue.

Idle policy is the SDK default: the dispatcher exits 60s after
`session.status_idle` with `stop_reason: end_turn`; any other event — including
`requires_action` idle, where the agent is blocked on the sandbox — resets the
clock.

No org API key anywhere: the webhook polls with the environment key, and the
runner authenticates with that same environment key — the single credential for
both the control plane and the per-session calls.

## Files

- `api/webhook.ts` — Vercel Function: verify sig → drain → spawn sandbox per item.
- `runner/runner.mjs` — runs inside the sandbox: `EnvironmentWorker.handleItem()` —
  builds the per-session `AgentToolContext`, downloads the agent's skills, runs a
  `SessionToolRunner` while heartbeating, force-stops the work item on exit.

## Prerequisites

- `npm`, the [Vercel CLI](<https://vercel.com/docs/cli>) (`npm i -g vercel`)
- A Vercel project linked (`vercel link`)
- A registered self-hosted environment + its environment key

## Configure

```sh
npm install
vercel link
vercel env add ANTHROPIC_WEBHOOK_SECRET   # placeholder for now
vercel env add ANTHROPIC_ENVIRONMENT_ID
vercel env add ANTHROPIC_ENVIRONMENT_KEY
```

## Deploy

```sh
vercel deploy --prod
```

This prints a `https://<project>.vercel.app` URL. Register
`https://<project>.vercel.app/api/webhook` as a webhook for
`session.status_run_started` in Console (or via the API), copy the issued
secret, then update:

```sh
vercel env rm ANTHROPIC_WEBHOOK_SECRET production
vercel env add ANTHROPIC_WEBHOOK_SECRET production
vercel deploy --prod
```

## Test

Create a session pointing at your environment id and send it a message:

```py
session = client.beta.sessions.create(agent=agent_id, environment_id=ENVIRONMENT_ID)
client.beta.sessions.events.send(session.id, events=[{"type": "user.message", "content": "ls -la"}])
```

You should see, in order:

```
vercel logs <project>.vercel.app
# [webhook] event=session.status_run_started session_id=...
# [webhook] acked work=... session=... sandbox=sbx_... (created)
```

Sandbox stdout/stderr (the `[runner]` lines) shows under the project's
**Observability → Sandboxes** tab in the Vercel dashboard.

## Notes

- `Sandbox.create()` + `writeFiles()` + `npm install` is ~15–25s before the
  function responds; `vercel.json` sets `maxDuration: 60`. The runner itself is
  `detached`. The function also mirrors the runner's first ~30s of output into
  the function log so it's debuggable from `vercel logs` without the Sandboxes
  observability tab.
- **Sandbox reuse** — Vercel Sandbox has no native tag/label/get-by-name API, so
  reuse needs external state. If a [Vercel KV](<https://vercel.com/storage/kv>)
  store is attached (`KV_REST_API_URL`/`KV_REST_API_TOKEN` are set), the webhook
  records `session_id → sandbox_id` and reuses a running sandbox on the next
  `run_started`, calling `extendTimeout()` so an active session keeps its VM
  alive across turns. Without KV, every delivery creates a fresh sandbox —
  `SessionToolRunner` dedups via `seen`/`answered` so a duplicate runner is
  wasteful, not wrong.
- **Working dirs** — the webhook creates `/mnt/session` and `/workspace` at
  sandbox start and uses `/workspace` as the dispatch workdir, matching the
  Modal/CF demos (skills download to `/workspace/skills/<name>/`). These are
  plain ephemeral directories, *not* persistent volumes — Vercel Sandbox has no
  volume API. The agent's working tree is gone when the sandbox stops.
- `npm install` runs inside the sandbox at spawn time so this demo has no build
  step. To shave ~10s off cold start, pre-bundle `runner/runner.mjs` with
  esbuild and upload the bundle instead.


</details>
