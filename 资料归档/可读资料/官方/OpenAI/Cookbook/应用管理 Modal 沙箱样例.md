# 应用管理 Modal 沙箱样例

在远端构建镜像、连接执行服务和处理文件，说明 API 与执行凭据配置及任务结束后的资源清理。

- 来源：OpenAI／Cookbook
- 资料类型：存档文档
- 存档版本：`5bd6cb40b6b8`。
- 文档时间：源码提交（存档版）：[2026-09-26](<https://github.com/openai/openai-cookbook/commit/6dc6324fb9ed780b32b787f23fad336e9f1eff15>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/sandboxes/modal/application_managed/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Cookbook/examples/agents_api/sandboxes/modal/application_managed/README.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 应用程序管理的 Modal 沙箱

该应用程序远程构建镜像，启动 Modal 沙箱，连接 
 执行器，针对 `sample_report.txt` 运行 agent，并清理这两个资源。

## 运行

设置`OPENAI_API_KEY`和单独的受限`OPENAI_EXECUTOR_API_KEY`。使用具有相同所有者、组织和项目的密钥
。从 Cookbook 存储库根目录：

```bash
uv tool run --from 'modal[api-proxy-support]>=1.3.4,<2' modal setup
uv run examples/agents_api/sandboxes/modal/application_managed/main.py
```

模态 SDK 使用 `~/.modal.toml` 中的活动配置文件； `MODAL_TOKEN_ID` 和 
`MODAL_TOKEN_SECRET` 设置后会覆盖它。沙箱的最大生命周期为 15 分钟 
，并且没有入站端口：`codex exec-server` 将出站连接到 OpenAI。

对于从 OpenAI webhook 提供计算的 Modal 托管处理程序，请使用 
[webhook 管理的 Modal 示例 ](<Webhook 管理 Modal 沙箱样例.md>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Application-managed Modal sandbox

The application builds an image remotely, starts a Modal sandbox, connects the
executor, runs the agent against `sample_report.txt`, and cleans up both resources.

## Run

Set `OPENAI_API_KEY` and a separate restricted `OPENAI_EXECUTOR_API_KEY`. Use keys
with the same owner, organization, and project. From the Cookbook repository root:

```bash
uv tool run --from 'modal[api-proxy-support]>=1.3.4,<2' modal setup
uv run examples/agents_api/sandboxes/modal/application_managed/main.py
```

The Modal SDK uses the active profile in `~/.modal.toml`; `MODAL_TOKEN_ID` and
`MODAL_TOKEN_SECRET` override it when set. The sandbox has a 15-minute maximum
lifetime and no inbound port: `codex exec-server` connects outbound to OpenAI.

For a Modal-hosted handler that provisions compute from OpenAI webhooks, use the
[webhook-managed Modal example](<Webhook 管理 Modal 沙箱样例.md>).


</details>
