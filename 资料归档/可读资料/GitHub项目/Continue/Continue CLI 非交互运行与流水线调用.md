# Continue CLI 非交互运行与流水线调用

CLI 指南。说明 cn -p、输入输出管道、JSON 输出、续接会话及 Git Hook、CI 样例；非交互模式会排除需要询问的工具，写文件需显式允许。

- 来源：Continue
- 资料类型：产品指南
- 存档版本：`5522c6f44ca0`。
- 文档时间：源码提交（存档版）：[2026-03-24](<https://github.com/continuedev/continue/commit/f7f3ab7440341f052642b7d0fab72d40fd8e7ad0>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：非交互模式的审批行为与交互模式不同。

[原文发布页](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/cli/headless-mode.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Continue/Continue CLI 非交互运行与流水线调用.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: "Headless Mode"
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

运行 `cn -p "your prompt"` 以执行单个任务，无需交互式会话。 agent 运行完成并将其响应打印到标准输出。

```bash
cn -p "Generate a conventional commit message for the staged changes"
```

## 管道

将数据通过管道传输到 `cn` 以将其包含为上下文：

```bash
git diff --staged | cn -p "Write a commit message for this diff"
cat error.log | cn -p "Explain what went wrong"
curl -s https://api.example.com/health | cn -p "Is this healthy?"
```

将输出通过管道传输到其他工具中：

```bash
cn -p "Generate a commit message" | git commit -F -
cn -p "List all TODO comments in src/" --silent > todos.txt
```

## 输出选项

|旗帜|效果|
|------|--------|
| `--silent` |从输出中去除 `<think>` 标签和多余的空格|
| `--format json` |输出结构化JSON|

`--silent` 将输出传送到另一个程序时很有用。 `--format json` 对于以编程方式解析结果很有用。

## 工具权限

在无头模式下，通常会提示批准（`ask` 权限）的工具会被自动排除——没有人批准它们。要启用写入操作，请显式允许它们：

```bash
# Allow the agent to write files
cn -p "Fix the type errors in src/" --allow Write --allow Edit

# Allow everything
cn -p "Set up the project" --allow "*"
```

有关完整的策略系统，请参阅[工具权限](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/cli/Continue_Continue CLI 工具权限、模式与优先级.md>)。

## 以无头模式恢复

在没有交互式输入的情况下恢复先前的会话：

```bash
cn -p --resume
```

这会重播上一个会话的历史记录，这对于检查自动化中过去的结果非常有用。

## 示例

```bash
# Git hooks
cn -p "Review staged changes for obvious bugs" --silent

# CI pipeline — allow file writes for auto-fix
cn -p "Fix all ESLint errors in src/" --allow Write --allow Edit --allow Bash

# Generate docs from code
cn -p "@src/api/ Generate OpenAPI documentation for these endpoints" --silent > api-docs.yaml

# Use a specific assistant
cn -p "Triage this error log" --config my-org/error-triage-assistant
```


## 原文对照

<details>
<summary>展开完整原文</summary>

Run `cn -p "your prompt"` to execute a single task without an interactive session. The agent runs to completion and prints its response to stdout.

```bash
cn -p "Generate a conventional commit message for the staged changes"
```

## Piping

Pipe data into `cn` to include it as context:

```bash
git diff --staged | cn -p "Write a commit message for this diff"
cat error.log | cn -p "Explain what went wrong"
curl -s https://api.example.com/health | cn -p "Is this healthy?"
```

Pipe the output into other tools:

```bash
cn -p "Generate a commit message" | git commit -F -
cn -p "List all TODO comments in src/" --silent > todos.txt
```

## Output options

| Flag | Effect |
|------|--------|
| `--silent` | Strip `<think>` tags and excess whitespace from output |
| `--format json` | Output structured JSON |

`--silent` is useful when piping output into another program. `--format json` is useful for parsing results programmatically.

## Tool permissions

In headless mode, tools that would normally prompt for approval (`ask` permission) are automatically excluded — there's no one to approve them. To enable write operations, explicitly allow them:

```bash
# Allow the agent to write files
cn -p "Fix the type errors in src/" --allow Write --allow Edit

# Allow everything
cn -p "Set up the project" --allow "*"
```

See [tool permissions](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/cli/Continue_Continue CLI 工具权限、模式与优先级.md>) for the full policy system.

## Resume in headless mode

Resume a previous session without interactive input:

```bash
cn -p --resume
```

This replays the previous session's history, which can be useful for inspecting past results in automation.

## Examples

```bash
# Git hooks
cn -p "Review staged changes for obvious bugs" --silent

# CI pipeline — allow file writes for auto-fix
cn -p "Fix all ESLint errors in src/" --allow Write --allow Edit --allow Bash

# Generate docs from code
cn -p "@src/api/ Generate OpenAPI documentation for these endpoints" --silent > api-docs.yaml

# Use a specific assistant
cn -p "Triage this error log" --config my-org/error-triage-assistant
```


</details>
