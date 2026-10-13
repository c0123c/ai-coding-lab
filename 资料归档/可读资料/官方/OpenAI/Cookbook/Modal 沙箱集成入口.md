# Modal 沙箱集成入口

比较应用管理和 Webhook 管理的资源启动与清理责任，指向相应实现样例。

- 来源：OpenAI／Cookbook
- 资料类型：存档文档
- 存档版本：`5bd6cb40b6b8`。
- 文档时间：源码提交（存档版）：[2026-09-26](<https://github.com/openai/openai-cookbook/commit/6dc6324fb9ed780b32b787f23fad336e9f1eff15>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/sandboxes/modal/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Cookbook/examples/agents_api/sandboxes/modal/README.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 模态沙箱

选择由谁配置和清理沙箱：

- [应用程序管理](<应用管理 Modal 沙箱样例.md>)：使用
示例文件，然后终止沙箱并删除 Agents API 会话。
- [Webhook 管理的](<Webhook 管理 Modal 沙箱样例.md>)：部署签名的 Webhook 接收器
并通过命名的 Modal 沙箱重新连接会话。

两种模式都使用 [modal_executor.py](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/modal/modal_executor.py>) 作为基础映像和 
 执行器命令。应用程序在顶部添加其报告工具和示例文件；
webhook 控制器将共享的 Python 源打包到其部署中。
只有受限制的执行程序密钥到达沙箱。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Modal sandboxes

Choose who provisions and cleans up the sandbox:

- [Application-managed](<应用管理 Modal 沙箱样例.md>): run a report task with a
  sample file, then terminate the sandbox and delete the Agents API session.
- [Webhook-managed](<Webhook 管理 Modal 沙箱样例.md>): deploy a signed webhook receiver
  and reconnect sessions through named Modal sandboxes.

Both modes use [modal_executor.py](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/modal/modal_executor.py>) for the base image and
executor command. The application adds its report tools and sample file on top;
the webhook controller packages the shared Python source in its deployment.
Only the restricted executor key reaches the sandbox.


</details>
