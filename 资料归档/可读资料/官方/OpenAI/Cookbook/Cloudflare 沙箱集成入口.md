# Cloudflare 沙箱集成入口

比较应用直接管理与 Webhook 唤醒方式，列出部署前提，帮助定位执行服务接入样例。

- 来源：OpenAI／Cookbook
- 资料类型：存档文档
- 存档版本：`5bd6cb40b6b8`。
- 文档时间：源码提交（存档版）：[2026-09-26](<https://github.com/openai/openai-cookbook/commit/6dc6324fb9ed780b32b787f23fad336e9f1eff15>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/sandboxes/cloudflare/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Cookbook/examples/agents_api/sandboxes/cloudflare/README.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Cloudflare 沙箱

选择谁启动并重新连接执行器：

- [应用程序管理的](<应用管理 Cloudflare 沙箱样例.md>)：Python应用程序调用
经过身份验证的 Worker 路由来启动沙箱、读取其输出并销毁它。
- [Webhook 管理的 ](<Webhook 管理 Cloudflare 沙箱样例.md>)：签名的 OpenAI webhook 唤醒
提供并重新连接沙箱的持久对象控制器。

两种模式共享 `Dockerfile`、npm 依赖项、TypeScript 配置、
 和 `executor.ts`。由于身份验证和生命周期管理不同，每种模式都保留自己的 Worker 入口点和 Wrangler
 配置。

## 先决条件

使用启用了 Workers Paid 和容器的 Cloudflare 帐户、Node.js 和 
Docker 来运行映像构建。两种模式使用单独的 Worker 名称，并使用 
 进行测试；不要部署在现有的应用程序上。

安装并检查此目录中的两个 Workers：

```bash
npm ci
npm run check
npx wrangler login
```

固定的沙箱 SDK 版本与 Docker 映像匹配。升级时，
两者一起更新。为
沙箱设置单独的受限`OPENAI_EXECUTOR_API_KEY`。将 application/session-read 钥匙放在容器外面。

按照模式的 README 进行配置、部署和清理命令。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Cloudflare sandboxes

Choose who starts and reconnects the executor:

- [Application-managed](<应用管理 Cloudflare 沙箱样例.md>): a Python application calls
  authenticated Worker routes to start a sandbox, read its output, and destroy it.
- [Webhook-managed](<Webhook 管理 Cloudflare 沙箱样例.md>): a signed OpenAI webhook wakes a
  Durable Object controller that provisions and reconnects the sandbox.

Both modes share the `Dockerfile`, npm dependencies, TypeScript configuration,
and `executor.ts`. Each mode keeps its own Worker entrypoint and Wrangler
configuration because authentication and lifecycle management differ.

## Prerequisites

Use a Cloudflare account with Workers Paid and Containers enabled, Node.js, and
Docker running for image builds. Use separate Worker names for the two modes and
for tests; do not deploy over an existing application.

Install and check both Workers from this directory:

```bash
npm ci
npm run check
npx wrangler login
```

The pinned Sandbox SDK version matches the Docker image. When upgrading it,
update both together. Set a separate restricted `OPENAI_EXECUTOR_API_KEY` for
the sandbox. Keep the application/session-read key out of the container.

Follow a mode's README for its configuration, deployment, and cleanup commands.


</details>
