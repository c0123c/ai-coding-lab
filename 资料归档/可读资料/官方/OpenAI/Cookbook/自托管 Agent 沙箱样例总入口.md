# 自托管 Agent 沙箱样例总入口

比较应用与 Webhook 配置方式以及不同提供方，说明执行服务连接和计算资源清理责任。

- 来源：OpenAI／Cookbook
- 资料类型：存档文档
- 存档版本：`5bd6cb40b6b8`。
- 文档时间：源码提交（存档版）：[2026-09-29](<https://github.com/openai/openai-cookbook/commit/86ff2cc0c4b2417feb36bc76ff0f24c5c4e785be>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/sandboxes/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Cookbook/examples/agents_api/sandboxes/README.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 自托管沙箱示例

选择您的应用程序启动沙箱计算的方式。两种模式都运行
OpenAI云端中的agent，并通过`codex exec-server`连接沙箱。

|模式|您的申请|沙盒配置|
| --- | --- | --- |
|应用程序管理|运行提供商的`application_managed/main.py`。|您的应用程序直接启动和停止计算；没有 webhook 处理程序。|
|[Webhook 管理](<Webhook 管理沙箱的总入口.md>)|运行提供程序的 webhook 管理示例以发送输入并跟随轮流。|当 OpenAI 发送 Webhook 时，单独部署的处理程序会启动或重新连接计算。|

每个会话选择一种配置模式。删除 API 会话不会停止 
 提供商计算；每个示例都记录了两者的清理。

## 选择提供商

|提供者|应用程序管理|Webhook 管理|
| --- | --- | --- |
|[布拉塞尔](<Blaxel 沙箱集成入口.md>)|[运行](<应用管理 Blaxel 沙箱样例.md>)|[部署](<Webhook 管理 Blaxel 沙箱样例.md>)|
|[Cloudflare](<Cloudflare 沙箱集成入口.md>)|[运行](<应用管理 Cloudflare 沙箱样例.md>)|[部署](<Webhook 管理 Cloudflare 沙箱样例.md>)|
|[代托纳](<Daytona 沙箱集成入口.md>)|[运行](<应用管理 Daytona 沙箱样例.md>)|[部署](<Webhook 管理 Daytona 沙箱样例.md>)|
| [DigitalOcean](<DigitalOcean 沙箱集成入口.md>) |[运行](<应用管理 DigitalOcean 沙箱样例.md>)|[部署](<Webhook 管理 DigitalOcean 沙箱样例.md>)|
|[Docker](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/docker/README.md>)|[运行](<应用管理 Docker 沙箱样例.md>)|不包括在内|
| [E2B](<E2B 沙箱集成入口.md>) |[运行](<应用管理 E2B 沙箱样例.md>)|[部署](<Webhook 管理 E2B 沙箱样例.md>)|
|[莫代尔](<Modal 沙箱集成入口.md>)|[运行](<应用管理 Modal 沙箱样例.md>)|[部署](<Webhook 管理 Modal 沙箱样例.md>)|
|[AWS 拉姆达 MicroVMs](<用 AWS Lambda MicroVM 运行 Agent 工具.md>)|[运行](<应用管理 AWS Lambda MicroVM 样例.md>)|[部署](<Webhook 管理 AWS Lambda MicroVM 样例.md>)|
| [OCI](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/oci/README.md>) |[运行](<应用管理 OCI GenAI 沙箱样例.md>)|不包括在内|
|[运行循环](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/runloop/README.md>)|[运行](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/runloop/application_managed/README.md>)|[部署](<Webhook 管理 Runloop 沙箱样例.md>)|
|[维塞尔](<Vercel 沙箱集成入口.md>)|[运行](<应用管理 Vercel 沙箱样例.md>)|[部署](<Webhook 管理 Vercel 沙箱样例.md>)|

每个提供程序文件夹都包含其示例和共享沙箱设置。 Webhook
示例包括他们自己的客户端；他们不会从其他提供商导入代码。
从 Cookbook 存储库根运行命令，除非提供商 README 另有说明。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Self-hosted sandbox examples

Choose how your application starts sandbox compute. Both modes run the agent in
OpenAI's cloud and connect the sandbox through `codex exec-server`.

| Mode | Your application | Sandbox provisioning |
| --- | --- | --- |
| Application-managed | Run the provider's `application_managed/main.py`. | Your application starts and stops compute directly; no webhook handler. |
| [Webhook-managed](<Webhook 管理沙箱的总入口.md>) | Run the provider's webhook-managed example to send input and follow the turn. | A separately deployed handler starts or reconnects compute when OpenAI sends a webhook. |

Choose one provisioning mode per session. Deleting an API session does not stop
provider compute; each example documents cleanup of both.

## Choose a provider

| Provider | Application-managed | Webhook-managed |
| --- | --- | --- |
| [Blaxel](<Blaxel 沙箱集成入口.md>) | [Run](<应用管理 Blaxel 沙箱样例.md>) | [Deploy](<Webhook 管理 Blaxel 沙箱样例.md>) |
| [Cloudflare](<Cloudflare 沙箱集成入口.md>) | [Run](<应用管理 Cloudflare 沙箱样例.md>) | [Deploy](<Webhook 管理 Cloudflare 沙箱样例.md>) |
| [Daytona](<Daytona 沙箱集成入口.md>) | [Run](<应用管理 Daytona 沙箱样例.md>) | [Deploy](<Webhook 管理 Daytona 沙箱样例.md>) |
| [DigitalOcean](<DigitalOcean 沙箱集成入口.md>) | [Run](<应用管理 DigitalOcean 沙箱样例.md>) | [Deploy](<Webhook 管理 DigitalOcean 沙箱样例.md>) |
| [Docker](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/docker/README.md>) | [Run](<应用管理 Docker 沙箱样例.md>) | Not included |
| [E2B](<E2B 沙箱集成入口.md>) | [Run](<应用管理 E2B 沙箱样例.md>) | [Deploy](<Webhook 管理 E2B 沙箱样例.md>) |
| [Modal](<Modal 沙箱集成入口.md>) | [Run](<应用管理 Modal 沙箱样例.md>) | [Deploy](<Webhook 管理 Modal 沙箱样例.md>) |
| [AWS Lambda MicroVMs](<用 AWS Lambda MicroVM 运行 Agent 工具.md>) | [Run](<应用管理 AWS Lambda MicroVM 样例.md>) | [Deploy](<Webhook 管理 AWS Lambda MicroVM 样例.md>) |
| [OCI](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/oci/README.md>) | [Run](<应用管理 OCI GenAI 沙箱样例.md>) | Not included |
| [Runloop](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/runloop/README.md>) | [Run](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/runloop/application_managed/README.md>) | [Deploy](<Webhook 管理 Runloop 沙箱样例.md>) |
| [Vercel](<Vercel 沙箱集成入口.md>) | [Run](<应用管理 Vercel 沙箱样例.md>) | [Deploy](<Webhook 管理 Vercel 沙箱样例.md>) |

Each provider folder contains its examples and shared sandbox setup. Webhook
examples include their own client; they do not import code from another provider.
Run commands from the Cookbook repository root unless the provider README says otherwise.


</details>
