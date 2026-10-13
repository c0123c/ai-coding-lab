# 应用管理 Docker 沙箱样例

构建本地镜像、启动执行服务、处理样例文件并清理容器与 API 会话，说明分开的执行凭据配置。

- 来源：OpenAI／Cookbook
- 资料类型：存档文档
- 存档版本：`5bd6cb40b6b8`。
- 文档时间：源码提交（存档版）：[2026-09-26](<https://github.com/openai/openai-cookbook/commit/6dc6324fb9ed780b32b787f23fad336e9f1eff15>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/sandboxes/docker/application_managed/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Cookbook/examples/agents_api/sandboxes/docker/application_managed/README.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 应用程序管理的 Docker 沙箱

应用程序构建本地映像，启动执行程序，针对 
`sample_report.txt` 运行 agent，并清理容器和 API 会话。

## 运行

启动 Docker 并设置 `OPENAI_API_KEY` 和单独的受限 
`OPENAI_EXECUTOR_API_KEY`。使用具有相同所有者、组织和项目的密钥。
来自 Cookbook 存储库根：

```bash
uv run examples/agents_api/sandboxes/docker/application_managed/main.py
```

[Dockerfile](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/docker/application_managed/Dockerfile>)安装Codex并将示例复制到`/workspace`中。
这是一个本地开发示例，而不是强化的多租户沙箱。

要通过 OpenAI webhook 管理云计算，请使用 
[webhook 管理的示例 ](<Webhook 管理沙箱的总入口.md>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Application-managed Docker sandbox

The application builds a local image, starts the executor, runs the agent against
`sample_report.txt`, and cleans up the container and API session.

## Run

Start Docker and set `OPENAI_API_KEY` and a separate restricted
`OPENAI_EXECUTOR_API_KEY`. Use keys with the same owner, organization, and project.
From the Cookbook repository root:

```bash
uv run examples/agents_api/sandboxes/docker/application_managed/main.py
```

The [Dockerfile](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/docker/application_managed/Dockerfile>) installs Codex and copies the sample into `/workspace`.
This is a local development example, not a hardened multi-tenant sandbox.

To manage cloud compute through OpenAI webhooks, use the
[webhook-managed examples](<Webhook 管理沙箱的总入口.md>).


</details>
