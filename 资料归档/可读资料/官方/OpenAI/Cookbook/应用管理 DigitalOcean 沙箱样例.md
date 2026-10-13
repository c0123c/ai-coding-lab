# 应用管理 DigitalOcean 沙箱样例

使用预设模板启动执行环境、运行任务并下载产物，说明应用如何管理会话与计算资源。

- 来源：OpenAI／Cookbook
- 资料类型：存档文档
- 存档版本：`5bd6cb40b6b8`。
- 文档时间：源码提交（存档版）：[2026-09-26](<https://github.com/openai/openai-cookbook/commit/6dc6324fb9ed780b32b787f23fad336e9f1eff15>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/sandboxes/digitalocean/application_managed/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Cookbook/examples/agents_api/sandboxes/digitalocean/application_managed/README.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 应用程序管理的 DigitalOcean 沙箱

该应用程序创建一个 Agents API 会话，并使用 `codex-agentapi` 模板启动 DigitalOcean 沙箱 
。模板运行`codex exec-server`；
OpenAI运行agent。应用程序下载生成的`plan.md`，
销毁沙箱，并删除会话。

## 运行

您需要一个启用沙箱的 DigitalOcean 帐户，并有权访问 
`codex-agentapi` 模板。 [DigitalOcean 托管 Agents](<https://docs.digitalocean.com/products/managed-agents/>)
 处于公共预览版。使用具有异步支持 (`pydo[aio]`) 的 [PyDo 0.41.0 或更高版本 ](<https://github.com/digitalocean/pydo/releases>)
。
此流程不需要 DigitalOcean CLI。

设置 `DIGITALOCEAN_TOKEN`、`OPENAI_API_KEY` 和单独的受限 
`OPENAI_EXECUTOR_API_KEY`。 OpenAI 密钥必须具有相同的所有者、
 组织和项目。仅执行者密钥被传递到DigitalOcean。

从 Cookbook 存储库根目录：

```bash
uv run examples/agents_api/sandboxes/digitalocean/application_managed/main.py
```

依赖项是内联声明的。该脚本读取共享的 [environment.yaml](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/digitalocean/environment.yaml>)
 清单并在运行时提供环境 ID 和执行程序密钥。不要
记录或保存已解析的清单：它包含执行程序密钥。

该脚本允许十分钟的设置和执行时间，然后尝试
清理操作。如果在返回 ID 之前创建超时，请在重试之前检查 
DigitalOcean 中的 `agents-api-<last 12 characters of the printed session ID>`
。

对于后续轮次，保留这两个资源，直到应用程序完成。
不要将配置 Webhook 处理程序附加到这些会话。

## 参考文献

- [DigitalOcean 沙箱设置示例](<https://github.com/digitalocean/pydo/tree/main/examples/agents/doc_python_sdk>)
- [DigitalOcean Python SDK](<https://github.com/digitalocean/pydo>)
- [Webhook 管理的示例](<Webhook 管理 DigitalOcean 沙箱样例.md>)


## 原文对照

<details>
<summary>展开完整原文</summary>

# Application-managed DigitalOcean sandbox

The application creates an Agents API session and starts a DigitalOcean sandbox
using the `codex-agentapi` template. The template runs `codex exec-server`;
OpenAI runs the agent. The application downloads the generated `plan.md`,
destroys the sandbox, and deletes the session.

## Run

You need a sandbox-enabled DigitalOcean account with access to the
`codex-agentapi` template. [DigitalOcean Managed Agents](<https://docs.digitalocean.com/products/managed-agents/>)
is in public preview. Use [PyDo 0.41.0 or later](<https://github.com/digitalocean/pydo/releases>)
with async support (`pydo[aio]`).
The DigitalOcean CLI is not required for this flow.

Set `DIGITALOCEAN_TOKEN`, `OPENAI_API_KEY`, and a separate restricted
`OPENAI_EXECUTOR_API_KEY`. The OpenAI keys must have the same owner,
organization, and project. Only the executor key is passed to DigitalOcean.

From the Cookbook repository root:

```bash
uv run examples/agents_api/sandboxes/digitalocean/application_managed/main.py
```

Dependencies are declared inline. The script reads the shared [environment.yaml](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/digitalocean/environment.yaml>)
manifest and supplies the environment ID and executor key at runtime. Do not
log or save the resolved manifest: it contains the executor key.

The script allows ten minutes for setup and execution, then attempts both
cleanup operations. If creation times out before returning an ID, check
DigitalOcean for `agents-api-<last 12 characters of the printed session ID>`
before retrying.

For follow-up turns, keep both resources until the application is finished.
Do not attach a provisioning webhook handler to these sessions.

## References

- [DigitalOcean sandbox setup example](<https://github.com/digitalocean/pydo/tree/main/examples/agents/doc_python_sdk>)
- [DigitalOcean Python SDK](<https://github.com/digitalocean/pydo>)
- [Webhook-managed example](<Webhook 管理 DigitalOcean 沙箱样例.md>)


</details>
