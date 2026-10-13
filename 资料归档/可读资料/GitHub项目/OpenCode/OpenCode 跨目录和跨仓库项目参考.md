# OpenCode 跨目录和跨仓库项目参考

产品指南。通过别名配置本地目录或 Git 仓库，说明用途描述、补全显示、引用和字段，使 Agent 使用外部文档、共享库或例子，同时保留工具权限约束。

- 来源：OpenCode
- 资料类型：产品指南
- 存档版本：`055d95bb7e27`。
- 文档时间：源码提交（存档版）：[2026-06-10](<https://github.com/anomalyco/opencode/commit/8a2cfc00c93afc32a79979b7a928bc55d6483934>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：增加参考目录不会自动赋予编辑权限。

[原文发布页](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/references.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenCode/OpenCode 跨目录和跨仓库项目参考.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: References
description: Add local directories and Git repositories as project references.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

引用使 OpenCode 能够访问当前项目之外的目录。使用它们可以在您工作时提供文档、共享库、示例或其他存储库。

引用由`opencode.json`或`opencode.jsonc`中的别名配置。

**代码示例：** `opencode.jsonc`

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "references": {
    "docs": {
      "path": "../product-docs",
      "description": "Use for product behavior and documentation conventions",
    },
    "sdk": {
      "repository": "anomalyco/opencode-sdk-js",
      "branch": "main",
      "description": "Use for JavaScript SDK implementation details",
    },
  },
}
```

---

## 本地目录

使用 `path` 引用本地目录。

**代码示例：** `opencode.jsonc`

```jsonc
{
  "references": {
    "docs": {
      "path": "../docs",
    },
  },
}
```

路径可以是：

- 相对于定义引用的配置文件
- 绝对值，如`/home/user/docs`
- 相对于您的主目录，例如 `~/docs`

您还可以使用字符串简写：

**代码示例：** `opencode.jsonc`

```jsonc
{
  "references": {
    "docs": "../docs",
  },
}
```

---

## Git 存储库

使用 `repository` 引用 Git 存储库。 OpenCode 在其本地存储库缓存中具体化存储库，并使签出的源可用作参考目录。

**代码示例：** `opencode.jsonc`

```jsonc
{
  "references": {
    "effect": {
      "repository": "Effect-TS/effect",
      "branch": "main",
    },
  },
}
```

`repository` 接受 Git URL、host/path 引用和 GitHub `owner/repo` 简写。可选的 `branch` 字段选择分支或参考。如果没有 `branch`，OpenCode 将使用存储库的默认分支。

当不需要分支、描述或其他选项时，可以使用字符串简写：

**代码示例：** `opencode.jsonc`

```jsonc
{
  "references": {
    "effect": "Effect-TS/effect",
  },
}
```

**注释：**

Git 引用是异步刷新的。新配置的存储库可能需要一些时间才能完成克隆或更新。

---

## 描述用法

添加 `description` 来解释 agent 何时应使用引用。

**代码示例：** `opencode.jsonc`

```jsonc
{
  "references": {
    "design-system": {
      "path": "../design-system",
      "description": "Use when implementing UI components or design tokens",
    },
  },
}
```

OpenCode 包括agent 上下文中的参考文献和描述。描述应该足够简短和具体，以区分具有相似内容的参考文献。没有描述的参考文献仍然可以通过自动完成和直接使用获得，但不会公布到 agents。

---

## 隐藏自动完成条目

将 `hidden` 设置为 `true` 以忽略 TUI 中 `@` 自动完成的引用。

**代码示例：** `opencode.jsonc`

```jsonc
{
  "references": {
    "internal": {
      "path": "../internal",
      "description": "Use for internal implementation details",
      "hidden": true,
    },
  },
}
```

`hidden` 仅影响自动完成。带有描述的隐藏参考仍然包含在 agent 上下文中。

---

## 使用参考文献

配置的参考显示在 TUI `@` 自动完成中。输入 `@alias` 以附加参考根目录，或输入 `@alias/` 以搜索其中的文件。

```text
Compare this implementation with @sdk/src/client.ts
```

Agents 还接收已配置引用的解析路径和描述，这些引用在其系统上下文中具有描述，因此他们可以在相关时检查引用，而无需您手动附加它。

OpenCode 自动允许通过其外部目录权限边界引用目录。普通工具权限仍然适用；例如，无法编辑文件的 agent 不会获得编辑访问权限，因为目录被配置为引用。

---

## 配置字段

|领域|本地|git|描述|
| ------------- | ----- | --- | ------------------------------------------------ |
| `path`        |是的|否|本地参考目录|
| `repository`  |否|是的|Git URL、host/path 或 GitHub `owner/repo` 值|
| `branch`      |否|是的|可选的 Git 分支或参考|
| `description` |是的|是的|描述何时使用参考的指南|
| `hidden`      |是的|是的|隐藏 TUI `@` 自动完成的引用|

引用别名不能为空或包含 `/`、空格、反引号或逗号。


## 原文对照

<details>
<summary>展开完整原文</summary>

References give OpenCode access to directories outside the current project. Use them to make documentation, shared libraries, examples, or another repository available while you work.

References are configured by alias in `opencode.json` or `opencode.jsonc`.

**代码示例：** `opencode.jsonc`

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "references": {
    "docs": {
      "path": "../product-docs",
      "description": "Use for product behavior and documentation conventions",
    },
    "sdk": {
      "repository": "anomalyco/opencode-sdk-js",
      "branch": "main",
      "description": "Use for JavaScript SDK implementation details",
    },
  },
}
```

---

## Local directories

Use `path` to reference a local directory.

**代码示例：** `opencode.jsonc`

```jsonc
{
  "references": {
    "docs": {
      "path": "../docs",
    },
  },
}
```

Paths can be:

- Relative to the config file that defines the reference
- Absolute, such as `/home/user/docs`
- Relative to your home directory, such as `~/docs`

You can also use a string shorthand:

**代码示例：** `opencode.jsonc`

```jsonc
{
  "references": {
    "docs": "../docs",
  },
}
```

---

## Git repositories

Use `repository` to reference a Git repository. OpenCode materializes the repository in its local repository cache and makes the checked-out source available as a reference directory.

**代码示例：** `opencode.jsonc`

```jsonc
{
  "references": {
    "effect": {
      "repository": "Effect-TS/effect",
      "branch": "main",
    },
  },
}
```

`repository` accepts Git URLs, host/path references, and GitHub `owner/repo` shorthand. The optional `branch` field selects a branch or ref. Without `branch`, OpenCode uses the repository's default branch.

You can use string shorthand when you do not need a branch, description, or other options:

**代码示例：** `opencode.jsonc`

```jsonc
{
  "references": {
    "effect": "Effect-TS/effect",
  },
}
```

**注释：**

Git references are refreshed asynchronously. A newly configured repository may take a moment to finish cloning or updating.

---

## Describe usage

Add `description` to explain when an agent should use a reference.

**代码示例：** `opencode.jsonc`

```jsonc
{
  "references": {
    "design-system": {
      "path": "../design-system",
      "description": "Use when implementing UI components or design tokens",
    },
  },
}
```

OpenCode includes references with descriptions in agent context. Descriptions should be short and specific enough to distinguish references with similar content. References without descriptions remain available through autocomplete and direct use, but are not advertised to agents.

---

## Hide autocomplete entries

Set `hidden` to `true` to omit a reference from `@` autocomplete in the TUI.

**代码示例：** `opencode.jsonc`

```jsonc
{
  "references": {
    "internal": {
      "path": "../internal",
      "description": "Use for internal implementation details",
      "hidden": true,
    },
  },
}
```

`hidden` only affects autocomplete. A hidden reference with a description remains included in agent context.

---

## Use references

Configured references appear in TUI `@` autocomplete. Type `@alias` to attach the reference root, or `@alias/` to search for files inside it.

```text
Compare this implementation with @sdk/src/client.ts
```

Agents also receive the resolved paths and descriptions of configured references that have descriptions in their system context, so they can inspect a reference when it is relevant without you attaching it manually.

OpenCode automatically allows reference directories through its external-directory permission boundary. Normal tool permissions still apply; for example, an agent that cannot edit files does not gain edit access because a directory is configured as a reference.

---

## Configure fields

| Field         | Local | Git | Description                                      |
| ------------- | ----- | --- | ------------------------------------------------ |
| `path`        | Yes   | No  | Local reference directory                        |
| `repository`  | No    | Yes | Git URL, host/path, or GitHub `owner/repo` value |
| `branch`      | No    | Yes | Optional Git branch or ref                       |
| `description` | Yes   | Yes | Guidance describing when to use the reference    |
| `hidden`      | Yes   | Yes | Hide the reference from TUI `@` autocomplete     |

Reference aliases cannot be empty or contain `/`, whitespace, backticks, or commas.


</details>
