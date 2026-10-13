# Blaxel 沙箱集成入口

概述应用管理和 Webhook 管理的执行路径，链接到相应运行与生命周期样例。

- 来源：OpenAI／Cookbook
- 资料类型：存档文档
- 存档版本：`5bd6cb40b6b8`。
- 文档时间：源码提交（存档版）：[2026-09-26](<https://github.com/openai/openai-cookbook/commit/6dc6324fb9ed780b32b787f23fad336e9f1eff15>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/sandboxes/blaxel/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Cookbook/examples/agents_api/sandboxes/blaxel/README.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Blaxel 沙箱

在 Blaxel 沙箱中使用 `codex exec-server` 运行 Agents API 任务：

- [应用程序管理](<应用管理 Blaxel 沙箱样例.md>)：创建沙箱，运行任务，
读取其输出，并清理沙箱和会话。
- [Webhook 管理的](<Webhook 管理 Blaxel 沙箱样例.md>)：部署一个控制器，提供
当会话需要环境连接时。

两种模式都使用[sandbox.py](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/blaxel/sandbox.py>)进行执行器安装和启动。
工作人员仅收到受限的执行器密钥；应用程序凭据保留在应用程序或 Webhook 控制器中的 
。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Blaxel sandboxes

Run Agents API tasks with `codex exec-server` in Blaxel sandboxes:

- [Application-managed](<应用管理 Blaxel 沙箱样例.md>): create a sandbox, run a task,
  read its output, and clean up the sandbox and session.
- [Webhook-managed](<Webhook 管理 Blaxel 沙箱样例.md>): deploy a controller that provisions
  workers when a session needs an environment connection.

Both modes use [sandbox.py](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/blaxel/sandbox.py>) for executor installation and launch.
Workers receive only the restricted executor key; application credentials stay
in the application or webhook controller.


</details>
