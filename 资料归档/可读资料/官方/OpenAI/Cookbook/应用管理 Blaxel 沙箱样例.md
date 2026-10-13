# 应用管理 Blaxel 沙箱样例

创建 API 会话和沙箱、启动执行服务，将需求文件生成计划，再读取产物并清理资源；属于后端集成样例。

- 来源：OpenAI／Cookbook
- 资料类型：存档文档
- 存档版本：`5bd6cb40b6b8`。
- 文档时间：源码提交（存档版）：[2026-09-26](<https://github.com/openai/openai-cookbook/commit/6dc6324fb9ed780b32b787f23fad336e9f1eff15>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/sandboxes/blaxel/application_managed/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Cookbook/examples/agents_api/sandboxes/blaxel/application_managed/README.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 应用程序管理的 Blaxel 沙箱

该应用程序创建一个 Agents API 会话和一个 Blaxel 沙箱，启动 
`codex exec-server`，并要求 agent 将 `brief.txt` 转换为 `plan.md`。
它打印计划，删除沙箱，并删除会话。

## 运行

设置 `BL_API_KEY`、`BL_WORKSPACE`、`OPENAI_API_KEY` 和单独的受限 
`OPENAI_EXECUTOR_API_KEY`。 OpenAI 密钥必须具有相同的所有者、
 组织和项目。只有执行者密钥才能进入沙箱。
设置`BL_REGION`选择区域；默认为 `us-pdx-1`。

从 Cookbook 存储库根目录：

```bash
uv run examples/agents_api/sandboxes/blaxel/application_managed/main.py
```

依赖项是内联声明的。该脚本允许六分钟的时间进行设置和
执行，并在失败时尝试清理操作。沙盒有
十分钟TTL。如果在返回沙箱之前创建超时，请在重试之前检查 Blaxel
 中的 `agents-api-<last 12 characters of the printed session ID>`。

对于后续轮次，保留这两个资源，直到应用程序完成。
不要将配置 Webhook 处理程序附加到这些会话。

## 参考文献

- [Blaxel 沙盒文档](<https://docs.blaxel.ai/Sandboxes/Overview>)
- [布拉塞尔 Python SDK](<https://github.com/blaxel-ai/sdk-python>)
- [Webhook 管理的示例](<Webhook 管理 Blaxel 沙箱样例.md>)


## 原文对照

<details>
<summary>展开完整原文</summary>

# Application-managed Blaxel sandbox

The application creates an Agents API session and a Blaxel sandbox, starts
`codex exec-server`, and asks the agent to turn `brief.txt` into `plan.md`.
It prints the plan, deletes the sandbox, and deletes the session.

## Run

Set `BL_API_KEY`, `BL_WORKSPACE`, `OPENAI_API_KEY`, and a separate restricted
`OPENAI_EXECUTOR_API_KEY`. The OpenAI keys must have the same owner,
organization, and project. Only the executor key enters the sandbox.
Set `BL_REGION` to choose a region; the default is `us-pdx-1`.

From the Cookbook repository root:

```bash
uv run examples/agents_api/sandboxes/blaxel/application_managed/main.py
```

Dependencies are declared inline. The script allows six minutes for setup and
execution and attempts both cleanup operations on failure. The sandbox has a
ten-minute TTL. If creation times out before returning a sandbox, check Blaxel
for `agents-api-<last 12 characters of the printed session ID>` before retrying.

For follow-up turns, keep both resources until the application is finished.
Do not attach a provisioning webhook handler to these sessions.

## References

- [Blaxel sandbox documentation](<https://docs.blaxel.ai/Sandboxes/Overview>)
- [Blaxel Python SDK](<https://github.com/blaxel-ai/sdk-python>)
- [Webhook-managed example](<Webhook 管理 Blaxel 沙箱样例.md>)


</details>
