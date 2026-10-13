# DigitalOcean 沙箱集成入口

概述托管 Agent 的公开预览与运行时依赖，指向不同管理方式的示例；属于版本相关入口。

- 来源：OpenAI／Cookbook
- 资料类型：存档文档
- 存档版本：`5bd6cb40b6b8`。
- 文档时间：源码提交（存档版）：[2026-09-26](<https://github.com/openai/openai-cookbook/commit/6dc6324fb9ed780b32b787f23fad336e9f1eff15>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/sandboxes/digitalocean/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Cookbook/examples/agents_api/sandboxes/digitalocean/README.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# DigitalOcean 沙箱

[DigitalOcean 托管 Agents](<https://docs.digitalocean.com/products/managed-agents/>)
 处于公共预览版。这些示例使用其 Harness Runtime 并需要 PyDo
0.41.0 或更高版本的异步支持：

- [应用程序管理](<应用管理 DigitalOcean 沙箱样例.md>)：创建沙箱，运行
任务，下载其输出，然后删除沙箱和 Agents API 会话。
- [Webhook 管理的](<Webhook 管理 DigitalOcean 沙箱样例.md>)：运行签名的 Webhook 控制器
它提供沙箱并在重新连接时恢复它们。

两种模式均使用 [environment.yaml](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/digitalocean/environment.yaml>)。 `codex-agentapi`
模板启动执行程序，并且仅向沙箱提供受限制的执行程序密钥
。
Webhook 控制器与沙箱计算分离，可以在本地运行或在应用程序平台上运行
。


## 原文对照

<details>
<summary>展开完整原文</summary>

# DigitalOcean sandboxes

[DigitalOcean Managed Agents](<https://docs.digitalocean.com/products/managed-agents/>)
is in public preview. These examples use its Harness Runtime and require PyDo
0.41.0 or later with async support:

- [Application-managed](<应用管理 DigitalOcean 沙箱样例.md>): create a sandbox, run a
  task, download its output, and delete both the sandbox and Agents API session.
- [Webhook-managed](<Webhook 管理 DigitalOcean 沙箱样例.md>): run a signed webhook controller
  that provisions sandboxes and resumes them on reconnect.

Both modes use [environment.yaml](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/digitalocean/environment.yaml>). The `codex-agentapi`
template starts the executor, and only the restricted executor key is supplied
to the sandbox.
The webhook controller is separate from sandbox compute and can run locally or
on App Platform.


</details>
