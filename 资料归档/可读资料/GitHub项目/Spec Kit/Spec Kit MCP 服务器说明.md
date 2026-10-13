# Spec Kit MCP 服务器说明

介绍实验性 MCP 工具入口、版本返回值和错误格式；正文标明接口契约仍可能变化。

- 来源：Spec Kit
- 资料类型：存档文档
- 存档版本：`0443760542e7`。
- 文档时间：源码提交（存档版）：[2026-10-02](<https://github.com/github/spec-kit/commit/e1fa857a7f536b22760d48c1aa9ace41df0fd1dc>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/spec-kit/blob/main/docs/reference/mcp.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Spec Kit/Spec Kit MCP 服务器说明.md>) · [返回资料总览](<../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# MCP 服务器

> [！WARNING]
> `specify mcp` 是实验性的。在MCP表面宣布稳定之前，其命令库存和工具合同可能
> 发生变化。

```bash
specify mcp
```

仅通过 **stdio** 启动 Model Context Protocol 服务器。该命令
不提供HTTP、SSE、daemon/service管理或传输选择
选项。配置 MCP 主机以将 `specify mcp` 作为本地子进程启动。

由于 stdout 携带 MCP 协议帧，因此服务器不会打印 Spec
Kit 横幅、丰富输出、启动消息、警告或日志。

## 工具

服务器公开了三个通用工具：

|工具|目的|
| --- | --- |
| `specify_list_commands` |通过MCP列出当前支持的CLI命令|
| `specify_describe_command` |描述一个支持的点分CLI命令|
| `specify_run_command` |通过真实指定运行一个支持的命令 CLI|

第一个实验版本仅支持点命令 `version`。
所有其他命令名称都会被拒绝，并出现 `unavailable_command` 工具错误。
服务器不会公开项目发现、工件、突变、
安装或更新操作、工作流程、确认或访问层。

## 版本结果和错误

`specify_run_command` 使用与运行 CLI 相同的 Python 环境在隔离子 
 进程中调用以下规范命令：

```bash
specify version --json
```

成功后，MCP 工具将返回该命令的直接 JSON 对象，其中包含 
`cli_version`、`runtime`、`system` 和 `features`。它不会将结果 
 包装在通用 `schema_version`、`ok` 或 `result` 字段中。

在 CLI 失败时，适配器会消耗来自 stderr 的结构化 JSON 错误，并且 
 返回包含 `error.code`、`error.message` 和 
`error.details` 的 MCP 工具错误。空、格式错误、混合或非 JSON 子进程输出被 
 报告为已清理的 `adapter_internal_error`；服务器永远不会替换
成功形状的回退或公开回溯。


## 原文对照

<details>
<summary>展开完整原文</summary>

# MCP Server

> [!WARNING]
> `specify mcp` is experimental. Its command inventory and tool contracts may
> change before the MCP surface is declared stable.

```bash
specify mcp
```

Starts a Model Context Protocol server over **stdio only**. The command does
not provide HTTP, SSE, daemon/service management, or transport-selection
options. Configure an MCP host to launch `specify mcp` as a local subprocess.

Because stdout carries MCP protocol frames, the server does not print the Spec
Kit banner, Rich output, startup messages, warnings, or logs there.

## Tools

The server exposes three generic tools:

| Tool | Purpose |
| --- | --- |
| `specify_list_commands` | List CLI commands currently supported through MCP |
| `specify_describe_command` | Describe one supported dotted CLI command |
| `specify_run_command` | Run one supported command through the real Specify CLI |

This first experimental release supports only the dotted command `version`.
Every other command name is rejected with an `unavailable_command` tool error.
The server does not expose project discovery, artifacts, mutations,
installation or update operations, workflows, confirmations, or access tiers.

## Version result and errors

`specify_run_command` invokes the canonical command below in an isolated child
process using the same Python environment as the running CLI:

```bash
specify version --json
```

On success, the MCP tool returns that command's direct JSON object with
`cli_version`, `runtime`, `system`, and `features`. It does not wrap the result
in universal `schema_version`, `ok`, or `result` fields.

On CLI failure, the adapter consumes the structured JSON error from stderr and
returns an MCP tool error containing `error.code`, `error.message`, and
`error.details`. Empty, malformed, mixed, or non-JSON child-process output is
reported as a sanitized `adapter_internal_error`; the server never substitutes
a success-shaped fallback or exposes a traceback.


</details>
