# OpenCode 工具权限与细粒度匹配

产品指南。解释 allow、ask、deny、自动模式、路径与通配符、外部目录及 Agent 权限；正文标明 v1.1.1 起旧 tools 布尔配置并入 permission。

- 来源：OpenCode
- 资料类型：产品指南
- 存档版本：`055d95bb7e27`。
- 文档时间：源码提交（存档版）：[2026-06-30](<https://github.com/anomalyco/opencode/commit/0a5bed2bc2549d988ba969765ec6722615c56e01>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：v1.1.1 起的配置迁移说明；默认权限不能推断成自己的安全边界。

[原文发布页](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/permissions.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenCode/OpenCode 工具权限与细粒度匹配.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Permissions
description: Control which actions require approval to run.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

OpenCode 使用 `permission` 配置来决定给定的操作是否应该自动运行、提示您或被阻止。

从 `v1.1.1` 开始，旧的 `tools` 布尔配置已被弃用，并已合并到 `permission` 中。仍支持旧的 `tools` 配置以实现向后兼容性。

---

## 行动

每个权限规则解析为以下之一：

- `"allow"` — 未经批准运行
- `"ask"` — 提示批准
- `"deny"` — 阻止该操作

---

## 自动模式

以 `--auto` 开头的 OpenCode 可自动批准未明确拒绝的权限请求。

```bash
opencode --auto
```

您还可以通过 [`opencode run`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode CLI 命令、参数与环境变量.md#run>) 使用自动模式。

```bash
opencode run --auto "Refactor this module"
```

仍强制执行显式 `"deny"` 规则。自动模式仅更改需要批准的请求。

在TUI中，打开命令面板并选择**启用自动批准权限**或**禁用自动批准权限**以更改模式。当自动模式处于活动状态时，提示会在当前 agent 旁边显示静音的 `auto` 指示符。

---

## 配置

您可以全局设置权限（使用`*`），并覆盖特定工具。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "*": "ask",
    "bash": "allow",
    "edit": "deny"
  }
}
```

您还可以一次设置所有权限：

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": "allow"
}
```

---

## 粒度规则（对象语法）

对于大多数权限，您可以使用对象来根据工具输入应用不同的操作。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "bash": {
      "*": "ask",
      "git *": "allow",
      "npm *": "allow",
      "rm *": "deny",
      "grep *": "allow"
    },
    "edit": {
      "*": "deny",
      "packages/web/src/content/docs/*.mdx": "allow"
    }
  }
}
```

规则通过模式匹配进行评估，**最后匹配的规则获胜**。常见的模式是将包罗万象的 `"*"` 规则放在前面，然后再放置更具体的规则。

### 通配符

权限模式使用简单的通配符匹配：

- `*` 匹配零个或多个任意字符
- `?` 恰好匹配一个字符
- 所有其他字符均按字面意思匹配

### 主目录扩展

您可以在模式开头使用 `~` 或 `$HOME` 来引用您的主目录。这对于 [`external_directory`](<#外部目录>) 规则特别有用。

- `~/projects/*` -> `/Users/username/projects/*`
- `$HOME/projects/*` -> `/Users/username/projects/*`
- `~` -> `/Users/username`

### 外部目录

使用 `external_directory` 允许工具调用触及启动 OpenCode 的工作目录之外的路径。这适用于任何采用路径作为输入的工具（例如 `read`、`edit`、`glob`、`grep` 和许多 `bash` 命令）。

主扩展（如 `~/...`）仅影响模式的写入方式。它不会使外部路径成为当前工作空间的一部分，因此仍必须通过 `external_directory` 允许工作目录之外的路径。

例如，这允许访问 `~/projects/personal/` 下的所有内容：

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "external_directory": {
      "~/projects/personal/**": "allow"
    }
  }
}
```

此处允许的任何目录都会继承与当前工作空间相同的默认值。由于 [`read` 默认为 `allow`](<#默认值>)，因此也允许读取 `external_directory` 下的条目，除非被覆盖。当工具应限制在这些路径中时添加显式规则，例如在保留读取的同时阻止编辑：

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "external_directory": {
      "~/projects/personal/**": "allow"
    },
    "edit": {
      "~/projects/personal/**": "deny"
    }
  }
}
```

将列表重点放在受信任的路径上，并根据其他工具的需要分层额外的允许或拒绝规则（例如 `bash`）。

---

## 可用权限

OpenCode 权限由工具名称键入，加上一些安全防护措施：

- `read` — 读取文件（与文件路径匹配）
- `edit` — 所有文件修改（涵盖 `edit`、`write`、`patch`）
- `glob` — 文件通配（匹配通配模式）
- `grep` — 内容搜索（匹配正则表达式模式）
- `bash` — 运行 shell 命令（匹配已解析的命令，如 `git status --porcelain`）
- `task` — 启动子代理（与子代理类型匹配）
- `skill` — 加载技能（与技能名称匹配）
- `lsp` — 运行 LSP 查询（当前非粒度）
- `question` — 在执行过程中询问用户问题
- `webfetch` — 获取 URL（与 URL 匹配）
- `websearch` — 网络搜索（与查询匹配）
- `external_directory` — 当工具触及项目工作目录之外的路径时触发
- `doom_loop` — 当相同的工具调用使用相同的输入重复 3 次时触发

---

## 默认值

如果您不指定任何内容，OpenCode将从许可默认值开始：

- 大多数权限默认为`"allow"`。
- `doom_loop`和`external_directory`默认为`"ask"`。
- `read` 是 `"allow"`，但默认情况下会拒绝 `.env` 文件：

**代码示例：** `opencode.json`

```json
{
  "permission": {
    "read": {
      "*": "allow",
      "*.env": "deny",
      "*.env.*": "deny",
      "*.env.example": "allow"
    }
  }
}
```

---

## “问”的作用是什么

当OpenCode提示批准时，UI提供三种结果：

- `once` — 仅批准此请求
- `always` — 批准与建议模式匹配的未来请求（对于当前 OpenCode 会话的其余部分）
- `reject` — 拒绝请求

`always` 将批准的模式集由该工具提供（例如，bash 批准通常将安全命令前缀（如 `git status*`）列入白名单）。

---

## Agents

您可以根据 agent 覆盖权限。 Agent 权限与全局配置合并，并且 agent 规则优先。 [详细了解](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 主 Agent、子 Agent 与配置.md#permissions>)有关agent权限的信息。

**注释：**

有关更详细的模式匹配示例，请参阅上面的 [粒度规则（对象语法）](<#粒度规则对象语法>) 部分。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "bash": {
      "*": "ask",
      "git *": "allow",
      "git commit *": "deny",
      "git push *": "deny",
      "grep *": "allow"
    }
  },
  "agent": {
    "build": {
      "permission": {
        "bash": {
          "*": "ask",
          "git *": "allow",
          "git commit *": "ask",
          "git push *": "deny",
          "grep *": "allow"
        }
      }
    }
  }
}
```

您还可以在 Markdown 中配置 agent 权限：

**代码示例：** `~/.config/opencode/agents/review.md`

```markdown
---
description: Code review without edits
mode: subagent
permission:
  edit: deny
  bash: ask
  webfetch: deny
---

Only analyze code and suggest changes.
```

**提示：**

对带有参数的命令使用模式匹配。 `"grep *"` 允许 `grep pattern file.txt`，而 `"grep"` 单独会阻止它。像 `git status` 这样的命令适用于默认行为，但在传递参数时需要显式许可（如 `"git status *"`）。


## 原文对照

<details>
<summary>展开完整原文</summary>

OpenCode uses the `permission` config to decide whether a given action should run automatically, prompt you, or be blocked.

As of `v1.1.1`, the legacy `tools` boolean config is deprecated and has been merged into `permission`. The old `tools` config is still supported for backwards compatibility.

---

## Actions

Each permission rule resolves to one of:

- `"allow"` — run without approval
- `"ask"` — prompt for approval
- `"deny"` — block the action

---

## Auto mode

Start OpenCode with `--auto` to automatically approve permission requests that are not explicitly denied.

```bash
opencode --auto
```

You can also use auto mode with [`opencode run`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode CLI 命令、参数与环境变量.md#run>).

```bash
opencode run --auto "Refactor this module"
```

Explicit `"deny"` rules are still enforced. Auto mode only changes requests that would otherwise ask for approval.

In the TUI, open the command palette and select **Enable auto-approve permissions** or **Disable auto-approve permissions** to change modes. When auto mode is active, the prompt displays a muted `auto` indicator next to the current agent.

---

## Configuration

You can set permissions globally (with `*`), and override specific tools.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "*": "ask",
    "bash": "allow",
    "edit": "deny"
  }
}
```

You can also set all permissions at once:

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": "allow"
}
```

---

## Granular Rules (Object Syntax)

For most permissions, you can use an object to apply different actions based on the tool input.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "bash": {
      "*": "ask",
      "git *": "allow",
      "npm *": "allow",
      "rm *": "deny",
      "grep *": "allow"
    },
    "edit": {
      "*": "deny",
      "packages/web/src/content/docs/*.mdx": "allow"
    }
  }
}
```

Rules are evaluated by pattern match, with the **last matching rule winning**. A common pattern is to put the catch-all `"*"` rule first, and more specific rules after it.

### Wildcards

Permission patterns use simple wildcard matching:

- `*` matches zero or more of any character
- `?` matches exactly one character
- All other characters match literally

### Home Directory Expansion

You can use `~` or `$HOME` at the start of a pattern to reference your home directory. This is particularly useful for [`external_directory`](<#external-directories>) rules.

- `~/projects/*` -> `/Users/username/projects/*`
- `$HOME/projects/*` -> `/Users/username/projects/*`
- `~` -> `/Users/username`

### External Directories

Use `external_directory` to allow tool calls that touch paths outside the working directory where OpenCode was started. This applies to any tool that takes a path as input (for example `read`, `edit`, `glob`, `grep`, and many `bash` commands).

Home expansion (like `~/...`) only affects how a pattern is written. It does not make an external path part of the current workspace, so paths outside the working directory must still be allowed via `external_directory`.

For example, this allows access to everything under `~/projects/personal/`:

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "external_directory": {
      "~/projects/personal/**": "allow"
    }
  }
}
```

Any directory allowed here inherits the same defaults as the current workspace. Since [`read` defaults to `allow`](<#defaults>), reads are also allowed for entries under `external_directory` unless overridden. Add explicit rules when a tool should be restricted in these paths, such as blocking edits while keeping reads:

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "external_directory": {
      "~/projects/personal/**": "allow"
    },
    "edit": {
      "~/projects/personal/**": "deny"
    }
  }
}
```

Keep the list focused on trusted paths, and layer extra allow or deny rules as needed for other tools (for example `bash`).

---

## Available Permissions

OpenCode permissions are keyed by tool name, plus a couple of safety guards:

- `read` — reading a file (matches the file path)
- `edit` — all file modifications (covers `edit`, `write`, `patch`)
- `glob` — file globbing (matches the glob pattern)
- `grep` — content search (matches the regex pattern)
- `bash` — running shell commands (matches parsed commands like `git status --porcelain`)
- `task` — launching subagents (matches the subagent type)
- `skill` — loading a skill (matches the skill name)
- `lsp` — running LSP queries (currently non-granular)
- `question` — asking the user questions during execution
- `webfetch` — fetching a URL (matches the URL)
- `websearch` — web search (matches the query)
- `external_directory` — triggered when a tool touches paths outside the project working directory
- `doom_loop` — triggered when the same tool call repeats 3 times with identical input

---

## Defaults

If you don’t specify anything, OpenCode starts from permissive defaults:

- Most permissions default to `"allow"`.
- `doom_loop` and `external_directory` default to `"ask"`.
- `read` is `"allow"`, but `.env` files are denied by default:

**代码示例：** `opencode.json`

```json
{
  "permission": {
    "read": {
      "*": "allow",
      "*.env": "deny",
      "*.env.*": "deny",
      "*.env.example": "allow"
    }
  }
}
```

---

## What “Ask” Does

When OpenCode prompts for approval, the UI offers three outcomes:

- `once` — approve just this request
- `always` — approve future requests matching the suggested patterns (for the rest of the current OpenCode session)
- `reject` — deny the request

The set of patterns that `always` would approve is provided by the tool (for example, bash approvals typically whitelist a safe command prefix like `git status*`).

---

## Agents

You can override permissions per agent. Agent permissions are merged with the global config, and agent rules take precedence. [Learn more](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 主 Agent、子 Agent 与配置.md#permissions>) about agent permissions.

**注释：**

Refer to the [Granular Rules (Object Syntax)](<#granular-rules-object-syntax>) section above for more detailed pattern matching examples.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "bash": {
      "*": "ask",
      "git *": "allow",
      "git commit *": "deny",
      "git push *": "deny",
      "grep *": "allow"
    }
  },
  "agent": {
    "build": {
      "permission": {
        "bash": {
          "*": "ask",
          "git *": "allow",
          "git commit *": "ask",
          "git push *": "deny",
          "grep *": "allow"
        }
      }
    }
  }
}
```

You can also configure agent permissions in Markdown:

**代码示例：** `~/.config/opencode/agents/review.md`

```markdown
---
description: Code review without edits
mode: subagent
permission:
  edit: deny
  bash: ask
  webfetch: deny
---

Only analyze code and suggest changes.
```

**提示：**

Use pattern matching for commands with arguments. `"grep *"` allows `grep pattern file.txt`, while `"grep"` alone would block it. Commands like `git status` work for default behavior but require explicit permission (like `"git status *"`) when arguments are passed.


</details>
