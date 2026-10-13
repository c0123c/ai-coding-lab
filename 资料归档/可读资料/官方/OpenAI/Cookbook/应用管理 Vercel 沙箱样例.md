# 应用管理 Vercel 沙箱样例

展示 API 会话、执行服务和计划文件生成，说明如何取回结果并销毁临时执行环境。

- 来源：OpenAI／Cookbook
- 资料类型：存档文档
- 存档版本：`5bd6cb40b6b8`。
- 文档时间：源码提交（存档版）：[2026-09-26](<https://github.com/openai/openai-cookbook/commit/6dc6324fb9ed780b32b787f23fad336e9f1eff15>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/sandboxes/vercel/application_managed/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Cookbook/examples/agents_api/sandboxes/vercel/application_managed/README.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 应用程序管理的 Vercel 沙箱

该应用程序创建一个 Agents API 会话和一个 Vercel 沙箱，启动 
`codex exec-server`，并要求 agent 将 `brief.txt` 转换为 `plan.md`。
它打印计划，销毁计划沙箱，并删除会话。

## 运行

使用 [ 提供程序先决条件 ](<Vercel 沙箱集成入口.md>) 和专用 Vercel 项目。
将 `VERCEL_TOKEN`、`VERCEL_TEAM_ID`、`VERCEL_PROJECT_ID`、`OPENAI_API_KEY` 和 
 设置为单独的受限`OPENAI_EXECUTOR_API_KEY`。 OpenAI 密钥必须具有 
 相同的所有者、组织和项目。只有执行者密钥才能进入沙箱。

从 Cookbook 存储库根目录：

```bash
uv run examples/agents_api/sandboxes/vercel/application_managed/main.py
```

依赖项是内联声明的。该脚本允许六分钟的时间进行设置和
执行，并在失败时尝试清理操作。它创建一个具有十分钟执行限制的
非持久沙箱。如果创建超时，
在重试之前检查 Vercel 中的 `agents-api-<last 12 characters of the printed session ID>`
。

对于后续轮次，保留这两个资源，直到应用程序完成。
不要将配置 Webhook 处理程序附加到这些会话。

## 参考文献

- [Vercel 沙盒文档](<https://vercel.com/docs/sandbox>)
- [Vercel 沙盒 SDK 参考](<https://vercel.com/docs/sandbox/sdk-reference>)
- [Webhook 管理的示例](<Webhook 管理 Vercel 沙箱样例.md>)
- [Webhook 设置和生命周期](<Webhook 管理沙箱的总入口.md#设置一次>)


## 原文对照

<details>
<summary>展开完整原文</summary>

# Application-managed Vercel sandbox

The application creates an Agents API session and a Vercel sandbox, starts
`codex exec-server`, and asks the agent to turn `brief.txt` into `plan.md`.
It prints the plan, destroys the sandbox, and deletes the session.

## Run

Use the [provider prerequisites](<Vercel 沙箱集成入口.md>) and a dedicated Vercel project.
Set `VERCEL_TOKEN`, `VERCEL_TEAM_ID`, `VERCEL_PROJECT_ID`, `OPENAI_API_KEY`, and
a separate restricted `OPENAI_EXECUTOR_API_KEY`. The OpenAI keys must have the
same owner, organization, and project. Only the executor key enters the sandbox.

From the Cookbook repository root:

```bash
uv run examples/agents_api/sandboxes/vercel/application_managed/main.py
```

Dependencies are declared inline. The script allows six minutes for setup and
execution and attempts both cleanup operations on failure. It creates a
non-persistent sandbox with a ten-minute execution limit. If creation times out,
check Vercel for `agents-api-<last 12 characters of the printed session ID>`
before retrying.

For follow-up turns, keep both resources until the application is finished.
Do not attach a provisioning webhook handler to these sessions.

## References

- [Vercel Sandbox documentation](<https://vercel.com/docs/sandbox>)
- [Vercel Sandbox SDK reference](<https://vercel.com/docs/sandbox/sdk-reference>)
- [Webhook-managed example](<Webhook 管理 Vercel 沙箱样例.md>)
- [Webhook setup and lifecycle](<Webhook 管理沙箱的总入口.md#set-up-once>)


</details>
