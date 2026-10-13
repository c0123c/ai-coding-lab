# Daytona 自托管沙箱示例

展示 Webhook、队列和会话执行器在 Daytona 上的实现，说明环境准备与运行方式；属于平台集成样例。

- 来源：Anthropic／Claude 示例
- 资料类型：存档文档
- 存档版本：`d7265d6ae994`。
- 文档时间：源码提交（存档版）：[2026-05-19](<https://github.com/anthropics/claude-cookbooks/commit/01b041a64c08190ef0f5359270d4966605831144>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/anthropics/claude-cookbooks/blob/main/managed_agents/self_hosted_sandboxes/daytona/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude 示例/Daytona 自托管沙箱示例.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Daytona 演示 — 自托管沙盒

参考实现 [使用指南](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/self_hosted_sandboxes/docs/usage-guide.md>) 上 [代托纳](<https://www.daytona.io/>). `daytona_webhook.py` 是一个 FastAPI 处理的应用程序 `session.status_run_started` webhook（已验证 `client.beta.webhooks.unwrap()`), **排空环境工作队列** 与 `client.beta.environments.work.poller(drain=True, auto_stop=False)` 因此，任何单次交付都会恢复之前错过的交付，并且每个项目都会创建一个 Daytona 沙箱，上传 **相同的提供商不可知论 `sandbox_runner.py`** Modal 演示使用并启动它。 Daytona 沙盒是完整的 Linux 容器，所以 `beta_agent_toolset_20260401` (bash/read/write/edit/glob/grep）按原样工作。

没有组织 API 密钥到达运行程序：webhook 使用环境密钥进行轮询，每个沙箱都使用相同的环境密钥进行身份验证 - 控制平面和每个会话调用的单一凭据。

```sh
# standardwebhooks backs `client.beta.webhooks.unwrap()` — only the orchestrator
# host needs it; the inner Daytona sandbox never sees raw webhook deliveries.
pip install fastapi uvicorn daytona-sdk standardwebhooks anthropic

export DAYTONA_API_KEY=... DAYTONA_API_URL=...
export ANTHROPIC_WEBHOOK_SECRET=... \
       ANTHROPIC_ENVIRONMENT_ID=env_... ANTHROPIC_ENVIRONMENT_KEY=sk-ant-oat...

uvicorn daytona_webhook:app --host 0.0.0.0 --port 8080
```

在可以服务 HTTP 并到达 Daytona API 的任何地方部署 FastAPI 应用程序（飞行、渲染、隧道后面的 VM 等），然后将其 URL 注册为 webhook 端点。

> **冷启动注意事项：** `_spawn()` 在每个新的 Daytona 沙箱中运行 `pip install anthropic`，这会在跑步者开始之前增加 ~10–15 秒。对于生产，将 SDK 预烘焙到自定义 Daytona 图像中并删除 `pip install` 行。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Daytona demo — Self-Hosted Sandboxes

Reference implementation of the [usage guide](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/self_hosted_sandboxes/docs/usage-guide.md>) on [Daytona](<https://www.daytona.io/>). `daytona_webhook.py` is a FastAPI app that handles the `session.status_run_started` webhook (verified with `client.beta.webhooks.unwrap()`), **drains the environment work queue** with `client.beta.environments.work.poller(drain=True, auto_stop=False)` so any single delivery recovers earlier missed ones, and per item creates a Daytona sandbox, uploads the **same provider-agnostic `sandbox_runner.py`** the Modal demo uses, and starts it. Daytona sandboxes are full Linux containers, so `beta_agent_toolset_20260401` (bash/read/write/edit/glob/grep) works as-is.

No org API key reaches the runner: the webhook polls with the environment key, and each sandbox authenticates with that same environment key — the single credential for both the control plane and the per-session calls.

```sh
# standardwebhooks backs `client.beta.webhooks.unwrap()` — only the orchestrator
# host needs it; the inner Daytona sandbox never sees raw webhook deliveries.
pip install fastapi uvicorn daytona-sdk standardwebhooks anthropic

export DAYTONA_API_KEY=... DAYTONA_API_URL=...
export ANTHROPIC_WEBHOOK_SECRET=... \
       ANTHROPIC_ENVIRONMENT_ID=env_... ANTHROPIC_ENVIRONMENT_KEY=sk-ant-oat...

uvicorn daytona_webhook:app --host 0.0.0.0 --port 8080
```

Deploy the FastAPI app anywhere that can serve HTTP and reach the Daytona API (Fly, Render, a VM behind a tunnel, etc.), then register its URL as the webhook endpoint.

> **Cold-start note:** `_spawn()` runs `pip install anthropic` inside each fresh Daytona sandbox, which adds ~10–15s before the runner starts. For production, pre-bake the SDK into a custom Daytona image and drop the `pip install` line.


</details>
