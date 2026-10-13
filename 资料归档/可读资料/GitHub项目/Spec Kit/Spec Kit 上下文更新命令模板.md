# Spec Kit 上下文更新命令模板

说明刷新编码 Agent 指令文件中由 Spec Kit 管理的区段，供项目上下文维护时参考。

- 来源：Spec Kit
- 资料类型：存档文档
- 存档版本：`0443760542e7`。
- 文档时间：源码提交（存档版）：[2026-07-10](<https://github.com/github/spec-kit/commit/126e56882b0484d13f0a12e62d6d871cd5f3957a>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/spec-kit/blob/main/extensions/agent-context/commands/speckit.agent-context.update.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Spec Kit/Spec Kit 上下文更新命令模板.md>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
description: "Refresh the managed Spec Kit section in coding agent context file(s)"
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 更新编码 Agent 上下文

刷新活动编码 agent 的 context/instruction 文件内的托管规格套件部分（例如 `CLAUDE.md`、`.github/copilot-instructions.md`、`AGENTS.md`）。

## 行为

该脚本读取 
`.specify/extensions/agent-context/agent-context-config.yml` 处的 agent 上下文扩展配置以发现：

- `context_file` — 要管理的编码 agent 上下文文件的路径。
- `context_files` — 用于多个编码 agent 上下文文件的可选项目相对路径。当非空时，脚本会更新每个列出的文件，并且该列表优先于 `context_file`。
- `context_markers.start` / `.end` — 托管部分周围的分隔符。当字段缺失时，默认为 `<!-- SPECKIT START -->` 和 `<!-- SPECKIT END -->`。

然后，它创建、替换或附加托管块，以便该部分在可以发现时指向最新的计划路径（`specs/` 下的任何 `plan.md`，包括嵌套范围布局，例如 `specs/<scope>/<feature>/plan.md`）。

如果`context_files`和`context_file`为空，则该命令报告无任何操作并成功退出。上下文文件路径必须保持与项目相关；绝对路径、Windows 驱动器路径、反斜杠分隔符和 `..` 路径段将被拒绝。

## 执行

- **重击**：`.specify/extensions/agent-context/scripts/bash/update-agent-context.sh [plan_path]`
- **PowerShell**: `.specify/extensions/agent-context/scripts/powershell/update-agent-context.ps1 [plan_path]`

当省略 `plan_path` 时，脚本会自动检测最近修改的 `specs/**/plan.md`（递归搜索，因此会发现嵌套的作用域布局）。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Update Coding Agent Context

Refresh the managed Spec Kit section inside the active coding agent's context/instruction file (e.g. `CLAUDE.md`, `.github/copilot-instructions.md`, `AGENTS.md`).

## Behavior

The script reads the agent-context extension config at
`.specify/extensions/agent-context/agent-context-config.yml` to discover:

- `context_file` — the path of the coding agent context file to manage.
- `context_files` — optional project-relative paths for multiple coding agent context files. When non-empty, the script updates each listed file and the list takes precedence over `context_file`.
- `context_markers.start` / `.end` — the delimiters surrounding the managed section. Defaults to `<!-- SPECKIT START -->` and `<!-- SPECKIT END -->` when the field is missing.

It then creates, replaces, or appends the managed block so that the section points at the most recent plan path when one can be discovered (any `plan.md` under `specs/`, including nested scoped layouts such as `specs/<scope>/<feature>/plan.md`).

If `context_files` and `context_file` are empty, the command reports nothing to do and exits successfully. Context file paths must stay project-relative; absolute paths, Windows drive paths, backslash separators, and `..` path segments are rejected.

## Execution

- **Bash**: `.specify/extensions/agent-context/scripts/bash/update-agent-context.sh [plan_path]`
- **PowerShell**: `.specify/extensions/agent-context/scripts/powershell/update-agent-context.ps1 [plan_path]`

When `plan_path` is omitted, the script auto-detects the most recently modified `specs/**/plan.md` (searched recursively, so nested scoped layouts are discovered).


</details>
