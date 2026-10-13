# OpenCode 自定义命令和提示模板

产品指南。说明用 Markdown 或 JSON 定义命令，传入参数、Shell 输出和文件引用，并配置 Agent、子任务及模型，帮助封装重复开发操作。

- 来源：OpenCode
- 资料类型：产品指南
- 存档版本：`055d95bb7e27`。
- 文档时间：源码提交（存档版）：[2026-01-17](<https://github.com/anomalyco/opencode/commit/58f7da6e9f8fb08a9f42a0d2cc34a18b2475ddea>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：自定义命令中的 Shell 与工具调用仍受实际执行权限约束。

[原文发布页](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/commands.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenCode/OpenCode 自定义命令和提示模板.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Commands
description: Create custom commands for repetitive tasks.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

自定义命令允许您指定在 TUI 中执行该命令时要运行的提示。

```bash
/my-command
```

自定义命令是除 `/init`、`/undo`、`/redo`、`/share`、`/help` 等内置命令之外的命令。 [了解更多](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 终端界面的上下文、命令与撤销.md#commands>)。

---

## 创建命令文件

在 `commands/` 目录中创建 markdown 文件来定义自定义命令。

创建`.opencode/commands/test.md`：

**代码示例：** `.opencode/commands/test.md`

```md
---
description: Run tests with coverage
agent: build
model: anthropic/claude-3-5-sonnet-20241022
---

Run the full test suite with coverage report and show any failures.
Focus on the failing tests and suggest fixes.
```

frontmatter 定义命令属性。内容成为模板。

通过键入 `/` 后跟命令名称来使用该命令。

```bash
"/test"
```

---

## 配置

您可以通过 OpenCode 配置或通过在 `commands/` 目录中创建 markdown 文件来添加自定义命令。

---

### JSON

使用 OpenCode [config](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 配置位置、合并顺序与功能选项.md>) 中的 `command` 选项：

**代码示例：** `opencode.jsonc`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "command": {
    // This becomes the name of the command
    "test": {
      // This is the prompt that will be sent to the LLM
      "template": "Run the full test suite with coverage report and show any failures.\nFocus on the failing tests and suggest fixes.",
      // This is shown as the description in the TUI
      "description": "Run tests with coverage",
      "agent": "build",
      "model": "anthropic/claude-3-5-sonnet-20241022"
    }
  }
}
```

现在您可以在TUI中运行此命令：

```bash
/test
```

---

### 降价

您还可以使用 Markdown 文件定义命令。将它们放入：

- 全球：`~/.config/opencode/commands/`
- 每个项目：`.opencode/commands/`

**代码示例：** `~/.config/opencode/commands/test.md`

```markdown
---
description: Run tests with coverage
agent: build
model: anthropic/claude-3-5-sonnet-20241022
---

Run the full test suite with coverage report and show any failures.
Focus on the failing tests and suggest fixes.
```

Markdown 文件名成为命令名。例如，`test.md` 让
您运行：

```bash
/test
```

---

## 提示配置

自定义命令的提示支持几个特殊的占位符和语法。

---

### 论点

使用 `$ARGUMENTS` 占位符将参数传递给命令。

**代码示例：** `.opencode/commands/component.md`

```md
---
description: Create a new component
---

Create a new React component named $ARGUMENTS with TypeScript support.
Include proper typing and basic structure.
```

使用参数运行命令：

```bash
/component Button
```

并且 `$ARGUMENTS` 将替换为 `Button`。

您还可以使用位置参数访问各个参数：

- `$1` - 第一个参数
- `$2` - 第二个参数
- `$3` - 第三个参数
- 等等...

例如：

**代码示例：** `.opencode/commands/create-file.md`

```md
---
description: Create a new file with content
---

Create a file named $1 in the directory $2
with the following content: $3
```

运行命令：

```bash
/create-file config.json src "{ \"key\": \"value\" }"
```

这取代了：

- `$1` 与 `config.json`
- `$2` 与 `src`
- `$3` 与 `{ "key": "value" }`

---

### 外壳输出

使用 _!`command`_ 将 [bash 命令 ](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 终端界面的上下文、命令与撤销.md#bash-commands>) 输出注入到提示符中。

例如，要创建分析测试覆盖率的自定义命令：

**代码示例：** `.opencode/commands/analyze-coverage.md`

```md
---
description: Analyze test coverage
---

Here are the current test results:
!`npm test`

Based on these results, suggest improvements to increase coverage.
```

或者查看最近的更改：

**代码示例：** `.opencode/commands/review-changes.md`

```md
---
description: Review recent changes
---

Recent git commits:
!`git log --oneline -10`

Review these changes and suggest any improvements.
```

命令在项目的根目录中运行，其输出成为提示的一部分。

---

### 文件参考

使用 `@` 后跟文件名将文件包含在命令中。

**代码示例：** `.opencode/commands/review-component.md`

```md
---
description: Review component
---

Review the component in @src/components/Button.tsx.
Check for performance issues and suggest improvements.
```

文件内容会自动包含在提示中。

---

## 选项

让我们详细看看配置选项。

---

### 模板

`template` 选项定义执行命令时将发送到 LLM 的提示。

**代码示例：** `opencode.json`

```json
{
  "command": {
    "test": {
      "template": "Run the full test suite with coverage report and show any failures.\nFocus on the failing tests and suggest fixes."
    }
  }
}
```

这是**必需的**配置选项。

---

### 描述

使用 `description` 选项提供命令功能的简要描述。

**代码示例：** `opencode.json`

```json
{
  "command": {
    "test": {
      "description": "Run tests with coverage"
    }
  }
}
```

当您输入命令时，这将显示为TUI中的描述。

---

### Agent

使用 `agent` 配置可选择指定哪个 [agent](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 主 Agent、子 Agent 与配置.md>) 应执行此命令。
如果这是一个 [ 子代理](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 主 Agent、子 Agent 与配置.md#subagents>) 该命令将默认触发子代理调用。
要禁用此行为，请设置`subtask` 至 `false`。

**代码示例：** `opencode.json`

```json
{
  "command": {
    "review": {
      "agent": "plan"
    }
  }
}
```

这是一个**可选**配置选项。如果未指定，则默认为您当前的 agent。

---

### 子任务

使用 `subtask` 布尔值强制命令触发 [ 子代理 ](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 主 Agent、子 Agent 与配置.md#subagents>) 调用。
如果您希望命令不污染您的主上下文并且将 ** 强制 ** agent 充当子代理，
即使 `mode` 在 [agent](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 主 Agent、子 Agent 与配置.md>) 配置上设置为 `primary`。

**代码示例：** `opencode.json`

```json
{
  "command": {
    "analyze": {
      "subtask": true
    }
  }
}
```

这是一个**可选**配置选项。

---

### 型号

使用 `model` 配置覆盖此命令的默认模型。

**代码示例：** `opencode.json`

```json
{
  "command": {
    "analyze": {
      "model": "anthropic/claude-3-5-sonnet-20241022"
    }
  }
}
```

这是一个**可选**配置选项。

---

## 内置

opencode包括`/init`、`/undo`、`/redo`、`/share`、`/help`等多个内置命令； [了解更多](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 终端界面的上下文、命令与撤销.md#commands>)。

**注释：**

自定义命令可以覆盖内置命令。

如果您定义具有相同名称的自定义命令，它将覆盖内置命令。


## 原文对照

<details>
<summary>展开完整原文</summary>

Custom commands let you specify a prompt you want to run when that command is executed in the TUI.

```bash
/my-command
```

Custom commands are in addition to the built-in commands like `/init`, `/undo`, `/redo`, `/share`, `/help`. [Learn more](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 终端界面的上下文、命令与撤销.md#commands>).

---

## Create command files

Create markdown files in the `commands/` directory to define custom commands.

Create `.opencode/commands/test.md`:

**代码示例：** `.opencode/commands/test.md`

```md
---
description: Run tests with coverage
agent: build
model: anthropic/claude-3-5-sonnet-20241022
---

Run the full test suite with coverage report and show any failures.
Focus on the failing tests and suggest fixes.
```

The frontmatter defines command properties. The content becomes the template.

Use the command by typing `/` followed by the command name.

```bash
"/test"
```

---

## Configure

You can add custom commands through the OpenCode config or by creating markdown files in the `commands/` directory.

---

### JSON

Use the `command` option in your OpenCode [config](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 配置位置、合并顺序与功能选项.md>):

**代码示例：** `opencode.jsonc`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "command": {
    // This becomes the name of the command
    "test": {
      // This is the prompt that will be sent to the LLM
      "template": "Run the full test suite with coverage report and show any failures.\nFocus on the failing tests and suggest fixes.",
      // This is shown as the description in the TUI
      "description": "Run tests with coverage",
      "agent": "build",
      "model": "anthropic/claude-3-5-sonnet-20241022"
    }
  }
}
```

Now you can run this command in the TUI:

```bash
/test
```

---

### Markdown

You can also define commands using markdown files. Place them in:

- Global: `~/.config/opencode/commands/`
- Per-project: `.opencode/commands/`

**代码示例：** `~/.config/opencode/commands/test.md`

```markdown
---
description: Run tests with coverage
agent: build
model: anthropic/claude-3-5-sonnet-20241022
---

Run the full test suite with coverage report and show any failures.
Focus on the failing tests and suggest fixes.
```

The markdown file name becomes the command name. For example, `test.md` lets
you run:

```bash
/test
```

---

## Prompt config

The prompts for the custom commands support several special placeholders and syntax.

---

### Arguments

Pass arguments to commands using the `$ARGUMENTS` placeholder.

**代码示例：** `.opencode/commands/component.md`

```md
---
description: Create a new component
---

Create a new React component named $ARGUMENTS with TypeScript support.
Include proper typing and basic structure.
```

Run the command with arguments:

```bash
/component Button
```

And `$ARGUMENTS` will be replaced with `Button`.

You can also access individual arguments using positional parameters:

- `$1` - First argument
- `$2` - Second argument
- `$3` - Third argument
- And so on...

For example:

**代码示例：** `.opencode/commands/create-file.md`

```md
---
description: Create a new file with content
---

Create a file named $1 in the directory $2
with the following content: $3
```

Run the command:

```bash
/create-file config.json src "{ \"key\": \"value\" }"
```

This replaces:

- `$1` with `config.json`
- `$2` with `src`
- `$3` with `{ "key": "value" }`

---

### Shell output

Use _!`command`_ to inject [bash command](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 终端界面的上下文、命令与撤销.md#bash-commands>) output into your prompt.

For example, to create a custom command that analyzes test coverage:

**代码示例：** `.opencode/commands/analyze-coverage.md`

```md
---
description: Analyze test coverage
---

Here are the current test results:
!`npm test`

Based on these results, suggest improvements to increase coverage.
```

Or to review recent changes:

**代码示例：** `.opencode/commands/review-changes.md`

```md
---
description: Review recent changes
---

Recent git commits:
!`git log --oneline -10`

Review these changes and suggest any improvements.
```

Commands run in your project's root directory and their output becomes part of the prompt.

---

### File references

Include files in your command using `@` followed by the filename.

**代码示例：** `.opencode/commands/review-component.md`

```md
---
description: Review component
---

Review the component in @src/components/Button.tsx.
Check for performance issues and suggest improvements.
```

The file content gets included in the prompt automatically.

---

## Options

Let's look at the configuration options in detail.

---

### Template

The `template` option defines the prompt that will be sent to the LLM when the command is executed.

**代码示例：** `opencode.json`

```json
{
  "command": {
    "test": {
      "template": "Run the full test suite with coverage report and show any failures.\nFocus on the failing tests and suggest fixes."
    }
  }
}
```

This is a **required** config option.

---

### Description

Use the `description` option to provide a brief description of what the command does.

**代码示例：** `opencode.json`

```json
{
  "command": {
    "test": {
      "description": "Run tests with coverage"
    }
  }
}
```

This is shown as the description in the TUI when you type in the command.

---

### Agent

Use the `agent` config to optionally specify which [agent](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 主 Agent、子 Agent 与配置.md>) should execute this command.
If this is a [subagent](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 主 Agent、子 Agent 与配置.md#subagents>) the command will trigger a subagent invocation by default.
To disable this behavior, set `subtask` to `false`.

**代码示例：** `opencode.json`

```json
{
  "command": {
    "review": {
      "agent": "plan"
    }
  }
}
```

This is an **optional** config option. If not specified, defaults to your current agent.

---

### Subtask

Use the `subtask` boolean to force the command to trigger a [subagent](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 主 Agent、子 Agent 与配置.md#subagents>) invocation.
This is useful if you want the command to not pollute your primary context and will **force** the agent to act as a subagent,
even if `mode` is set to `primary` on the [agent](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 主 Agent、子 Agent 与配置.md>) configuration.

**代码示例：** `opencode.json`

```json
{
  "command": {
    "analyze": {
      "subtask": true
    }
  }
}
```

This is an **optional** config option.

---

### Model

Use the `model` config to override the default model for this command.

**代码示例：** `opencode.json`

```json
{
  "command": {
    "analyze": {
      "model": "anthropic/claude-3-5-sonnet-20241022"
    }
  }
}
```

This is an **optional** config option.

---

## Built-in

opencode includes several built-in commands like `/init`, `/undo`, `/redo`, `/share`, `/help`; [learn more](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 终端界面的上下文、命令与撤销.md#commands>).

**注释：**

Custom commands can override built-in commands.

If you define a custom command with the same name, it will override the built-in command.


</details>
