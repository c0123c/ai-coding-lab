# 在 Modal 托管 SDK Agent

介绍构建镜像、部署、访问、持久化、存活检查和清理，展示一个可运行的 Agent 服务环境。

- 来源：Anthropic／Claude 示例
- 资料类型：存档文档
- 存档版本：`d7265d6ae994`。
- 文档时间：源码提交（存档版）：[2026-05-28](<https://github.com/anthropics/claude-cookbooks/commit/2ec3c494e3ebc2ebcde5f9f243fbffd71f33b199>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/anthropics/claude-cookbooks/blob/main/claude_agent_sdk/hosting/modal/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude 示例/在 Modal 托管 SDK Agent.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 层 2 — 莫代尔

运行 **一样** `hosting/Dockerfile` 图像上 [莫代尔](<https://modal.com>) 通过
`modal.Sandbox`。你得到一个公众 HTTPS URL，缩放至零，并且没有
要管理的基础设施。

## 先决条件

```bash
pip install modal
modal setup            # opens a browser to authenticate
modal secret create anthropic ANTHROPIC_API_KEY="$ANTHROPIC_API_KEY"
```

## 部署

```bash
cd claude_agent_sdk/
python hosting/modal/modal_app.py
```

这将在 Modal 上远程构建 Dockerfile（构建上下文 = `claude_agent_sdk/`，
与本地相同），启动运行 `serve` 的沙箱，并打印隧道 URL。

## 与它交谈

部署脚本打印 `url:` 和 `token:` 行。隧道 URL 是公共的，
 因此服务器需要消息端点上的承载令牌（仅 
`/health` 是开放的）：

```bash
curl -N -X POST "$MODAL_URL/sessions/demo-1/messages" \
  -H "Authorization: Bearer $MODAL_TOKEN" \
  -H 'Content-Type: application/json' \
  -d '{"prompt":"What are the latest AI agent trends?"}'
```

## 坚持

默认情况下，`modal_app.py` 在 `/data` 处安装 `modal.Volume` — 与层 1 相同的 
`CLAUDE_CONFIG_DIR` 技巧。会话记录在沙箱
重新启动后仍然存在。

> **注意：** `modal.Volume` 具有显式提交语义。如果您的工作负载有
> 许多沙箱同时写入多个会话，并且您看到写入丢失，
> 切换到由模态`Dict`或外部存储支持的[`SessionStore`](<https://code.claude.com/docs/en/agent-sdk/session-storage>)
>  \- 这也是层3使用的。

## 活力

Modal 保持沙箱运行，直到传递给 `Sandbox.create`
 的 `timeout=` 过期；它不探测`GET /health`。如果内部服务器挂掉，沙箱
将保持运行状态，请求将失败，直到超时为止。对于任何比演示寿命更长的
，请将您自己的监控指向`/health`，并在沙箱停止应答时重新创建沙箱
。

## 拆解

```bash
python hosting/modal/teardown.py
```

停止沙箱并删除卷，这样您就无需为空闲资源付费。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Tier 2 — Modal

Runs the **same** `hosting/Dockerfile` image on [Modal](<https://modal.com>) via
`modal.Sandbox`. You get a public HTTPS URL, scale-to-zero, and no
infrastructure to manage.

## Prerequisites

```bash
pip install modal
modal setup            # opens a browser to authenticate
modal secret create anthropic ANTHROPIC_API_KEY="$ANTHROPIC_API_KEY"
```

## Deploy

```bash
cd claude_agent_sdk/
python hosting/modal/modal_app.py
```

This builds the Dockerfile remotely on Modal (build context = `claude_agent_sdk/`,
same as local), starts a sandbox running `serve`, and prints a tunnel URL.

## Talk to it

The deploy script prints a `url:` and a `token:` line. The tunnel URL is public,
so the server requires the bearer token on the messages endpoint (only
`/health` is open):

```bash
curl -N -X POST "$MODAL_URL/sessions/demo-1/messages" \
  -H "Authorization: Bearer $MODAL_TOKEN" \
  -H 'Content-Type: application/json' \
  -d '{"prompt":"What are the latest AI agent trends?"}'
```

## Persistence

By default `modal_app.py` mounts a `modal.Volume` at `/data` — the same
`CLAUDE_CONFIG_DIR` trick as tier 1. Session transcripts survive sandbox
restarts.

> **Note:** `modal.Volume` has explicit commit semantics. If your workload has
> many sandboxes writing many sessions concurrently and you see lost writes,
> switch to a [`SessionStore`](<https://code.claude.com/docs/en/agent-sdk/session-storage>)
> backed by Modal `Dict` or an external store — that's also what tier 3 uses.

## Liveness

Modal keeps the sandbox running until the `timeout=` passed to `Sandbox.create`
expires; it does not probe `GET /health`. If the server inside dies, the sandbox
stays up and requests fail until the timeout reaps it. For anything longer-lived
than a demo, point your own monitoring at `/health` and recreate the sandbox
when it stops answering.

## Teardown

```bash
python hosting/modal/teardown.py
```

Stops sandboxes and removes the volume so you're not billed for idle resources.


</details>
