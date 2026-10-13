# Spec Kit 编码 Agent 上下文扩展

说明管理 CLAUDE.md、AGENTS.md 等文件中特定区段的生命周期、安装与配置，保留区段之外的项目内容。

- 来源：Spec Kit
- 资料类型：存档文档
- 存档版本：`0443760542e7`。
- 文档时间：源码提交（存档版）：[2026-07-23](<https://github.com/github/spec-kit/commit/a62fb1f034d59470c28fa18f22ef2c9c3e2b802a>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/spec-kit/blob/main/extensions/agent-context/README.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Spec Kit/Spec Kit 编码 Agent 上下文扩展.md>) · [返回资料总览](<../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 编码 Agent 上下文扩展

此捆绑扩展管理 ** 编码 agent context/instruction 文件 **（例如 `CLAUDE.md`、`.github/copilot-instructions.md`、`AGENTS.md`、`GEMINI.md`，...）积极整合。

它拥有由可配置的 start/end 标记分隔的托管部分的生命周期（默认值：`<!-- SPECKIT START -->` / `<!-- SPECKIT END -->`）。对于 `.mdc` 文件，它还确保 YAML frontmatter（文件顶部的元数据块）包含 `alwaysApply: true`。否则，托管部分之外的所有内容都不会受到影响。

> NOTE：规格套件本身永远不会触及您的 agent 上下文文件。这个扩展是唯一可以做的事情，并且它是可选的：如果您希望块保持同步，则安装它；如果您想自己管理该文件，则跳过它。

## 为什么要延长？

并非每个规格套件用户都希望规格套件写入编码 agent 的上下文文件中。将此行为保留在专用的 ** 选择加入 ** 扩展中可以让用户：

- **选择是否安装它** - `specify init`**不**安装它。当您希望 Spec Kit 管理 agent 上下文文件时，明确添加它；当它不存在时，文件永远不会被修改，当它被禁用时，它的自动挂钩不会运行。
- **自定义标记** 通过编辑 `.specify/extensions/agent-context/agent-context-config.yml` ([agent-context-config.yml](<https://github.com/github/spec-kit/blob/0443760542e7e5269724744a2ac55c4bd386d095/extensions/agent-context/agent-context-config.yml>) 在此存储库中） - 捆绑的脚本遵循 `context_markers` 值。
- **当项目有意使用多个编码 agent 上下文文件（例如 `AGENTS.md` 和 `CLAUDE.md`）时，通过设置 `context_files` 同步多个 agent 锚点 **。
- **通过在 agent 中运行 `speckit.agent-context.update` 命令按需刷新 **，或者通过 [extension.yml](<https://github.com/github/spec-kit/blob/0443760542e7e5269724744a2ac55c4bd386d095/extensions/agent-context/extension.yml>)（`after_specify`、`after_plan`）中声明的挂钩自动刷新。

## 安装

要安装扩展，请从初始化的 Spec Kit 项目的根目录运行：

```bash
specify extension add agent-context
```

## 禁用

```bash
specify extension disable agent-context

# Re-enable it
specify extension enable agent-context
```

当此扩展被禁用（或未安装）时，规范套件中的任何内容都不会创建、更新或删除托管块 - 任何模板中的 `__CONTEXT_FILE__` 占位符都保持原样，并且永远不会读取扩展自己的配置。

## 命令

|命令|描述|
| ------------------------------ | --------------------------------------------------------------------------------- |
| `speckit.agent-context.update` |使用当前计划路径刷新 agent 上下文文件中的托管部分。|

> NOTE：上面的命令ID是规范的。使用集成语法调用它：`/speckit.agent-context.update` 用于点命令集成； `/speckit-agent-context-update` 用于 hyphen/skills 集成（包括 Forge 和 Cline）； `$speckit-agent-context-update`为Codex或技能模式下的ZCode；或 `/skill:speckit-agent-context-update` 为 Kimi。

## 配置

所有配置都通过位于 `.specify/extensions/agent-context/agent-context-config.yml` 的扩展自己的配置文件（存储库中的 [agent-context-config.yml](<https://github.com/github/spec-kit/blob/0443760542e7e5269724744a2ac55c4bd386d095/extensions/agent-context/agent-context-config.yml>)）。

## 要求

捆绑的更新脚本需要 **Python 3** 和 **PyYAML** 来进行 YAML/upsert 处理（PowerShell 也可以使用 `ConvertFrom-Yaml`（如果可用））。

PyYAML 随 `specify` CLI 一起提供，通常可通过相同的 `python3` 解释器获得。如果挂钩报告 _“需要 PyYAML……在当前 Python 环境中不可用”_，则意味着系统 `python3` 与用于安装 Spec Kit 的系统不同。要解决此问题，请运行：

```bash
pip install pyyaml
# or target the specific interpreter Spec Kit uses:
/path/to/speckit-python -m pip install pyyaml
```

## 问题

对于任何其他问题，请在 [ 官方 GitHub 存储库 ](<https://github.com/github/spec-kit/issues>) 中创建问题。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Coding Agent Context Extension

This bundled extension manages the **coding agent context/instruction file** (e.g. `CLAUDE.md`, `.github/copilot-instructions.md`, `AGENTS.md`, `GEMINI.md`, …) for the active integration.

It owns the lifecycle of the managed section delimited by the configurable start/end markers (defaults: `<!-- SPECKIT START -->` / `<!-- SPECKIT END -->`). For `.mdc` files, it also ensures the YAML frontmatter (the metadata block at the top of the file) contains `alwaysApply: true`. Otherwise, everything outside the managed section is untouched.

> NOTE: Spec Kit itself never touches your agent context file. This extension is the only thing that does, and it's opt-in: install it if you want the block kept in sync, skip it if you'd rather manage that file yourself.

## Why an extension?

Not every Spec Kit user wants Spec Kit to write into the coding agent's context file. Keeping this behavior in a dedicated, **opt-in** extension lets users:

- **Choose whether to install it at all** - `specify init` does **not** install it. Add it explicitly when you want Spec Kit to manage the agent context file; when it is absent, the file is never modified, and when it is disabled, its automatic hooks do not run.
- **Customize the markers** by editing `.specify/extensions/agent-context/agent-context-config.yml` ([agent-context-config.yml](<https://github.com/github/spec-kit/blob/0443760542e7e5269724744a2ac55c4bd386d095/extensions/agent-context/agent-context-config.yml>) in this repo) - the bundled scripts honor the `context_markers` value.
- **Synchronize multiple agent anchors** by setting `context_files` when a project intentionally uses more than one coding agent context file, such as `AGENTS.md` and `CLAUDE.md`.
- **Refresh on demand** by running the `speckit.agent-context.update` command in your agent, or automatically through the hooks declared in [extension.yml](<https://github.com/github/spec-kit/blob/0443760542e7e5269724744a2ac55c4bd386d095/extensions/agent-context/extension.yml>) (`after_specify`, `after_plan`).

## Installation

To install the extension, from the root of an initialized Spec Kit project, run:

```bash
specify extension add agent-context
```

## Disabling

```bash
specify extension disable agent-context

# Re-enable it
specify extension enable agent-context
```

While this extension is disabled (or not installed), nothing in Spec Kit creates, updates, or removes the managed block - the `__CONTEXT_FILE__` placeholder in any template is left as-is, and the extension's own config is never read.

## Commands

| Command                        | Description                                                                       |
| ------------------------------ | --------------------------------------------------------------------------------- |
| `speckit.agent-context.update` | Refresh the managed section in the agent context file with the current plan path. |

> NOTE: The command ID above is canonical. Invoke it using the syntax for your integration: `/speckit.agent-context.update` for dot-command integrations; `/speckit-agent-context-update` for hyphen/skills integrations (including Forge and Cline); `$speckit-agent-context-update` for Codex or ZCode in skills mode; or `/skill:speckit-agent-context-update` for Kimi.

## Configuration

All configuration flows through the extension's own config file at `.specify/extensions/agent-context/agent-context-config.yml` ([agent-context-config.yml](<https://github.com/github/spec-kit/blob/0443760542e7e5269724744a2ac55c4bd386d095/extensions/agent-context/agent-context-config.yml>) in the repo).

## Requirements

The bundled update scripts require **Python 3** with **PyYAML** for YAML/upsert processing (PowerShell can also use `ConvertFrom-Yaml` when available).

PyYAML ships with the `specify` CLI and is normally available via the same `python3` interpreter. If a hook reports _"PyYAML is required … not available in the current Python environment"_, it means the system `python3` differs from the one used to install Spec Kit. To resolve, run:

```bash
pip install pyyaml
# or target the specific interpreter Spec Kit uses:
/path/to/speckit-python -m pip install pyyaml
```

## Issues

For any other issues, please create an issue in the [official GitHub repo](<https://github.com/github/spec-kit/issues>).


</details>
