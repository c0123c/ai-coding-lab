# Gemini CLI 内部文档查询工具

说明工具用途、参数和调用行为，让 Agent 可查询自身技术文档以核实功能和使用方法。

- 来源：Google／Gemini CLI
- 资料类型：存档文档
- 存档版本：`9b6e0265d16b`。
- 文档时间：源码提交（存档版）：[2026-02-19](<https://github.com/google-gemini/gemini-cli/commit/d25c469f77237f55c50fb531f6bed06c9a8d4cc9>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/google-gemini/gemini-cli/blob/main/docs/tools/internal-docs.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Gemini CLI/docs/tools/internal-docs.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 内部文档工具（`get_internal_docs`）

`get_internal_docs` 工具允许 Gemini CLI 访问其自己的技术 
 文档，以提供有关其功能和使用情况的更准确答案。

## 描述

当Gemini CLI需要验证有关Gemini
CLI的内部功能、内置命令或配置选项的具体细节时，使用此工具。它
提供对`docs/`目录中Markdown文件的直接访问。

### 论点

`get_internal_docs` 采用一个可选参数：

- `path`（字符串，可选）：特定文档文件的相对路径
（例如，`reference/commands.md`）。如果省略，该工具将返回 
 所有可用文档路径的列表。

## 用途

`get_internal_docs`工具由Gemini CLI专用。您无法
手动调用此工具。

当Gemini CLI使用此工具时，它会检索所请求的
文档文件的内容并对其进行处理以回答您的问题。这确保了 
 AI 提供的信息以最新项目 
 文档为基础。

## 行为

Gemini CLI使用该工具来确保技术准确性：

- **能力发现：**如果Gemini CLI不确定某个功能如何工作，它可以
查找相应的文档。
- **参考查找：** Gemini CLI可以验证斜杠命令子命令或
具体设置名称。
- **自行更正：**Gemini CLI可以使用文档来更正其
了解Gemini CLI的系统逻辑。

## 后续步骤

- 探索[命令参考](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/reference/commands.md>)以获取详细指南
斜线命令。
- 设置请参见[配置指南](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/reference/configuration.md>)
参考。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Internal documentation tool (`get_internal_docs`)

The `get_internal_docs` tool lets Gemini CLI access its own technical
documentation to provide more accurate answers about its capabilities and usage.

## Description

This tool is used when Gemini CLI needs to verify specific details about Gemini
CLI's internal features, built-in commands, or configuration options. It
provides direct access to the Markdown files in the `docs/` directory.

### Arguments

`get_internal_docs` takes one optional argument:

- `path` (string, optional): The relative path to a specific documentation file
  (for example, `reference/commands.md`). If omitted, the tool returns a list of
  all available documentation paths.

## Usage

The `get_internal_docs` tool is used exclusively by Gemini CLI. You cannot
invoke this tool manually.

When Gemini CLI uses this tool, it retrieves the content of the requested
documentation file and processes it to answer your question. This ensures that
the information provided by the AI is grounded in the latest project
documentation.

## Behavior

Gemini CLI uses this tool to ensure technical accuracy:

- **Capability discovery:** If Gemini CLI is unsure how a feature works, it can
  lookup the corresponding documentation.
- **Reference lookup:** Gemini CLI can verify slash command sub-commands or
  specific setting names.
- **Self-correction:** Gemini CLI can use the documentation to correct its
  understanding of Gemini CLI's system logic.

## Next steps

- Explore the [Command reference](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/reference/commands.md>) for a detailed guide
  to slash commands.
- See the [Configuration guide](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/reference/configuration.md>) for settings
  reference.


</details>
