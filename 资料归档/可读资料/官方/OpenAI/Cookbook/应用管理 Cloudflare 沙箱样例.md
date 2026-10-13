# 应用管理 Cloudflare 沙箱样例

通过 Worker 启动执行环境、运行任务和取回计划文件，介绍部署、调用及沙箱清理。

- 来源：OpenAI／Cookbook
- 资料类型：存档文档
- 存档版本：`5bd6cb40b6b8`。
- 文档时间：源码提交（存档版）：[2026-09-26](<https://github.com/openai/openai-cookbook/commit/6dc6324fb9ed780b32b787f23fad336e9f1eff15>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/sandboxes/cloudflare/application_managed/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Cookbook/examples/agents_api/sandboxes/cloudflare/application_managed/README.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 应用程序管理的 Cloudflare 沙箱

Python 应用程序创建 Agents API 会话并调用您的 Cloudflare
Worker 来启动沙箱。 Worker运行`codex exec-server`并返回
agent生成的`plan.md`。然后应用程序会销毁沙箱并
删除会话。配置由应用程序驱动，而不是 Webhook。

Cloudflare 的沙箱 SDK 在 Worker 内部运行。本示例中的小型 Worker 
 为 Python 客户端提供经过身份验证的启动、文件读取和销毁路由。

## 部署工作人员

完成 [ 提供商设置 ](<Cloudflare 沙箱集成入口.md#先决条件>)。在`application_managed/wrangler.jsonc`中设置唯一的Worker
名称。从 Cookbook 存储库 
root 开始，切换到 Cloudflare 提供程序目录并部署：

```bash
cd examples/agents_api/sandboxes/cloudflare
npm run deploy:application
npx wrangler secret put OPENAI_EXECUTOR_API_KEY --config application_managed/wrangler.jsonc
npx wrangler secret put SANDBOX_CONTROL_TOKEN --config application_managed/wrangler.jsonc
```

使用受限执行者密钥和单独的随机控制令牌。将 
 应用程序的 `OPENAI_API_KEY` 保留在 Worker 和沙箱之外。 OpenAI 密钥
 必须具有相同的所有者、组织和项目。

## 运行应用程序

将 `OPENAI_API_KEY`、`SANDBOX_CONTROL_TOKEN`（与 
Worker 中存储的值相同）和 `CLOUDFLARE_SANDBOX_WORKER_URL` 设置为已部署的 Worker URL。
从 Cookbook 存储库根目录：

```bash
uv run examples/agents_api/sandboxes/cloudflare/application_managed/main.py
```

Python 依赖项被声明为内联。该脚本允许 
 设置和执行 6 分钟，并在失败时尝试执行清理操作。 Worker
还设置了十分钟的空闲超时。如果应用程序中断，请向 Worker 发送 
 经过身份验证的 `DELETE /sandboxes/<printed session ID>`，并删除 
 Agents API 会话。

对于后续轮次，保留这两个资源，直到应用程序完成。
不要将配置 Webhook 处理程序附加到这些会话。完成此部署后 
，从提供程序目录运行 `npx wrangler delete --config application_managed/wrangler.jsonc`
。

## 参考文献

- [Cloudflare 沙盒文档](<https://developers.cloudflare.com/sandbox/>)
- [后台进程](<https://developers.cloudflare.com/sandbox/guides/background-processes/>)
- [Webhook 管理的示例](<Webhook 管理 Cloudflare 沙箱样例.md>)
- [Webhook 设置和生命周期](<Webhook 管理沙箱的总入口.md#设置一次>)


## 原文对照

<details>
<summary>展开完整原文</summary>

# Application-managed Cloudflare sandbox

The Python application creates an Agents API session and calls your Cloudflare
Worker to start a sandbox. The Worker runs `codex exec-server` and returns the
agent's generated `plan.md`. The application then destroys the sandbox and
deletes the session. Provisioning is driven by the application, not webhooks.

Cloudflare's sandbox SDK runs inside a Worker. The small Worker in this example
provides authenticated start, file-read, and destroy routes for the Python client.

## Deploy the Worker

Complete the [provider setup](<Cloudflare 沙箱集成入口.md#prerequisites>). Set a unique Worker
name in `application_managed/wrangler.jsonc`. Starting at the Cookbook repository
root, change into the Cloudflare provider directory and deploy:

```bash
cd examples/agents_api/sandboxes/cloudflare
npm run deploy:application
npx wrangler secret put OPENAI_EXECUTOR_API_KEY --config application_managed/wrangler.jsonc
npx wrangler secret put SANDBOX_CONTROL_TOKEN --config application_managed/wrangler.jsonc
```

Use a restricted executor key and a separate random control token. Keep the
application's `OPENAI_API_KEY` outside the Worker and sandbox. The OpenAI keys
must have the same owner, organization, and project.

## Run the application

Set `OPENAI_API_KEY`, `SANDBOX_CONTROL_TOKEN` (the same value stored in the
Worker), and `CLOUDFLARE_SANDBOX_WORKER_URL` to the deployed Worker URL.
From the Cookbook repository root:

```bash
uv run examples/agents_api/sandboxes/cloudflare/application_managed/main.py
```

Python dependencies are declared inline. The script allows six minutes for
setup and execution and attempts both cleanup operations on failure. The Worker
also sets a ten-minute idle timeout. If the application is interrupted, send an
authenticated `DELETE /sandboxes/<printed session ID>` to the Worker and delete
the Agents API session.

For follow-up turns, keep both resources until the application is finished.
Do not attach a provisioning webhook handler to these sessions. When finished
with this deployment, run `npx wrangler delete --config application_managed/wrangler.jsonc`
from the provider directory.

## References

- [Cloudflare Sandbox documentation](<https://developers.cloudflare.com/sandbox/>)
- [Background processes](<https://developers.cloudflare.com/sandbox/guides/background-processes/>)
- [Webhook-managed example](<Webhook 管理 Cloudflare 沙箱样例.md>)
- [Webhook setup and lifecycle](<Webhook 管理沙箱的总入口.md#set-up-once>)


</details>
