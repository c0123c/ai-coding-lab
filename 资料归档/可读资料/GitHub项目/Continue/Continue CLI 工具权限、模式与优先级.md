# Continue CLI 工具权限、模式与优先级

CLI 指南。解释 allow、ask、exclude，命令行匹配模式和 permissions.yaml，说明参数优先级以及 Plan、Auto 模式的覆盖行为，帮助限制编辑与终端工具。

- 来源：Continue
- 资料类型：产品指南
- 存档版本：`5522c6f44ca0`。
- 文档时间：源码提交（存档版）：[2026-03-19](<https://github.com/continuedev/continue/commit/5d67e484d724cefd3a362949523f1adf97a91198>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：模式可能覆盖单项工具策略，应按存档正文核对实际配置。

[原文发布页](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/cli/tool-permissions.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Continue/Continue CLI 工具权限、模式与优先级.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: "Tool Permissions"
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

每个工具都具有以下三个权限级别之一：

|许可|行为|
|------------|----------|
| `allow` |自动运行，无提示|
| `ask` |运行前提示批准（仅限TUI）|
| `exclude` |完全对 agent 隐藏|

## 默认值

只读工具（`Read`、`List`、`Search`、`Fetch`、`Diff`、`AskQuestion`、`Checklist`、`Status`、 `CheckBackgroundJob`、`ReportFailure`、`UploadArtifact`）默认为`allow`。 `AskQuestion` 是一个内置只读工具，可让 agent 暂停并在继续之前要求澄清。写入工具（`Edit`、`MultiEdit`、`Write`）和`Bash`默认为`ask`。在[无头模式](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/cli/Continue_Continue CLI 非交互运行与流水线调用.md>)中，`ask`工具被排除在外，因为没有人批准它们。

## 用标志覆盖

使用 `--allow`、`--ask` 和 `--exclude` 覆盖启动时的默认值：

```bash
# Allow file writes without prompting
cn --allow Write --allow Edit

# Exclude terminal commands
cn --exclude Bash

# Allow everything (headless automation)
cn -p "Set up the project" --allow "*"
```

标志优先于所有其他权限源。

## 工具匹配模式

标志接受工具匹配模式：

- `Write` — 匹配对 `Write` 工具的任何调用
- `Write(*)` — 同上
- `Write(**/*.ts)` — 匹配 `Write` 调用，其中主参数与 glob `**/*.ts` 匹配

```bash
# Allow writing only to TypeScript files
cn --allow "Write(**/*.ts)"

# Allow bash but not for install commands
cn --allow Bash --exclude "Bash(npm install*)"
```

## `permissions.yaml`

持久权限存储在`~/.continue/permissions.yaml`中。当您在 TUI 批准提示中选择“Continue + 不再询问”时，此文件会更新。

```yaml
# ~/.continue/permissions.yaml
allow:
  - Read(*)
  - Write(**/*.ts)

ask:
  - Bash

exclude: []
```

您可以直接编辑该文件，但它主要由TUI管理。更改将在下一个会话中生效。

## 优先级

当多个来源为同一工具定义权限时，优先级最高的来源获胜：

1. **模式策略** - `--auto`和`--readonly`覆盖所有内容（见下文）
2. **CLI 标志 ** — `--allow`、`--ask`、`--exclude`
3. **`permissions.yaml`** — 持久的个人设置
4. **默认值** — 内置策略

## 模式

模式是通用权限集的简写。在 TUI 会话期间使用 `Shift+Tab` 切换模式，或在启动时设置它们：

```bash
cn --auto       # Allow all tools without prompting
cn --readonly   # Plan mode — read-only tools only, no file writes
```

|模式|效果|
|------|--------|
|**正常**（默认）|使用配置的权限|
|**计划**（`--readonly`）|排除所有写入工具，允许读取和 `Bash`|
|**自动** (`--auto`)|允许一切 — `*: allow`|

计划和自动模式是绝对优先的。他们完全忽略 `--allow`、`--exclude` 和 `permissions.yaml`。


## 原文对照

<details>
<summary>展开完整原文</summary>

Every tool has one of three permission levels:

| Permission | Behavior |
|------------|----------|
| `allow` | Runs automatically, no prompt |
| `ask` | Prompts for approval before running (TUI only) |
| `exclude` | Hidden from the agent entirely |

## Defaults

Read-only tools (`Read`, `List`, `Search`, `Fetch`, `Diff`, `AskQuestion`, `Checklist`, `Status`, `CheckBackgroundJob`, `ReportFailure`, `UploadArtifact`) default to `allow`. `AskQuestion` is a built-in read-only tool that lets the agent pause and ask for clarification before continuing. Write tools (`Edit`, `MultiEdit`, `Write`) and `Bash` default to `ask`. In [headless mode](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/cli/Continue_Continue CLI 非交互运行与流水线调用.md>), `ask` tools are excluded since there's no one to approve them.

## Overriding with flags

Use `--allow`, `--ask`, and `--exclude` to override defaults at launch:

```bash
# Allow file writes without prompting
cn --allow Write --allow Edit

# Exclude terminal commands
cn --exclude Bash

# Allow everything (headless automation)
cn -p "Set up the project" --allow "*"
```

Flags take precedence over all other permission sources.

## Tool matching patterns

Flags accept tool matching patterns:

- `Write` — matches any call to the `Write` tool
- `Write(*)` — same as above
- `Write(**/*.ts)` — matches `Write` calls where the primary argument matches the glob `**/*.ts`

```bash
# Allow writing only to TypeScript files
cn --allow "Write(**/*.ts)"

# Allow bash but not for install commands
cn --allow Bash --exclude "Bash(npm install*)"
```

## `permissions.yaml`

Persistent permissions are stored in `~/.continue/permissions.yaml`. This file is updated when you choose "Continue + don't ask again" in the TUI approval prompt.

```yaml
# ~/.continue/permissions.yaml
allow:
  - Read(*)
  - Write(**/*.ts)

ask:
  - Bash

exclude: []
```

You can edit this file directly, but it's primarily managed by the TUI. Changes take effect on the next session.

## Precedence

When multiple sources define a permission for the same tool, the highest-priority source wins:

1. **Mode policies** — `--auto` and `--readonly` override everything (see below)
2. **CLI flags** — `--allow`, `--ask`, `--exclude`
3. **`permissions.yaml`** — persistent personal settings
4. **Defaults** — built-in policies

## Modes

Modes are a shorthand for common permission sets. Switch modes with `Shift+Tab` during a TUI session, or set them at launch:

```bash
cn --auto       # Allow all tools without prompting
cn --readonly   # Plan mode — read-only tools only, no file writes
```

| Mode | Effect |
|------|--------|
| **normal** (default) | Uses configured permissions |
| **plan** (`--readonly`) | Excludes all write tools, allows reads and `Bash` |
| **auto** (`--auto`) | Allows everything — `*: allow` |

Plan and auto modes are absolute overrides. They ignore `--allow`, `--exclude`, and `permissions.yaml` entirely.


</details>
