# Vercel 沙箱集成入口

概述临时沙箱的应用管理与 Webhook 管理，帮助查找执行服务启动、结果读取及清理样例。

- 来源：OpenAI／Cookbook
- 资料类型：存档文档
- 存档版本：`5bd6cb40b6b8`。
- 文档时间：源码提交（存档版）：[2026-09-26](<https://github.com/openai/openai-cookbook/commit/6dc6324fb9ed780b32b787f23fad336e9f1eff15>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/sandboxes/vercel/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Cookbook/examples/agents_api/sandboxes/vercel/README.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Vercel 沙箱

选择谁启动并重新连接执行器：

- [应用程序管理的](<应用管理 Vercel 沙箱样例.md>)：Python应用程序
创建一个非持久性沙箱，读取输出并清理这两个资源。
- [Webhook 管理的](<Webhook 管理 Vercel 沙箱样例.md>)：TypeScript 功能验证
webhooks 并使用 Vercel 队列来配置命名沙箱。

这些模式使用不同的提供商 SDK 并保持其实现独立。
两者都运行 `codex exec-server`，但 `OPENAI_EXECUTOR_API_KEY` 受到限制； 
application/session-read 密钥位于沙箱之外。两个 OpenAI 密钥必须 
 具有相同的所有者、组织和项目。

使用具有沙箱访问权限的专用 Vercel 项目。 webhook模式还需要
队列访问。测试部署应使用单独的项目，而不是覆盖
现有应用程序。每种模式的 README 涵盖凭证、执行限制、
 和清理。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Vercel sandboxes

Choose who starts and reconnects the executor:

- [Application-managed](<应用管理 Vercel 沙箱样例.md>): a Python application
  creates a non-persistent sandbox, reads the output, and cleans up both resources.
- [Webhook-managed](<Webhook 管理 Vercel 沙箱样例.md>): TypeScript functions verify
  webhooks and use Vercel Queues to provision named sandboxes.

The modes use different provider SDKs and keep their implementations separate.
Both run `codex exec-server` with a restricted `OPENAI_EXECUTOR_API_KEY`; the
application/session-read key stays outside the sandbox. The two OpenAI keys must
have the same owner, organization, and project.

Use a dedicated Vercel project with Sandbox access. The webhook mode also needs
Queues access. Test deployments should use a separate project, not overwrite an
existing application. Each mode's README covers credentials, execution limits,
and cleanup.


</details>
