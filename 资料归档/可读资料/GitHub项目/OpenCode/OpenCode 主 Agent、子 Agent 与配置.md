# OpenCode 主 Agent、子 Agent 与配置

产品指南。介绍 Build、Plan 等内置角色和子 Agent，说明 JSON/Markdown 配置、模型、工具、权限、任务授权及使用样例，帮助分配不同开发职责。

- 来源：OpenCode
- 资料类型：产品指南
- 存档版本：`055d95bb7e27`。
- 文档时间：源码提交（存档版）：[2026-05-08](<https://github.com/anomalyco/opencode/commit/40d5ea1cf1b8029b1163a367275b3d2d9574e46c>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：按存档版角色和选项判断，示例角色需要结合任务设置。

[原文发布页](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/agents.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenCode/OpenCode 主 Agent、子 Agent 与配置.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Agents
description: Configure and use specialized agents.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

Agents 是专门的 AI 助手，可以针对特定任务和工作流程进行配置。它们允许您创建具有自定义提示、模型和工具访问权限的专用工具。

**提示：**

使用计划 agent 分析代码并审查建议，而无需进行任何代码更改。

您可以在会话期间在 agents 之间切换，或通过提及 `@` 来调用它们。

---

## 类型

OpenCode中有两种类型的agents；主要 agents 和子代理。

---

### 主要 agents

主要 agents 是与您直接互动的主要助手。您可以使用 **Tab** 键或您配置的 `switch_agent` 键绑定循环浏览它们。这些 agents 处理您的主要对话。工具访问是通过权限配置的 - 例如，“构建”启用了所有工具，而“计划”则受到限制。

**提示：**

您可以在会话期间使用 **Tab ** 键在主 agents 之间切换。

OpenCode 附带两个内置主 agents、**Build** 和 **Plan**。我们将
看看下面这些。

---

### 子代理

子代理是主要 agents 可以调用来执行特定任务的专业助手。您还可以通过在消息中**@提及**来手动调用它们。

OpenCode 附带三个内置子代理：**常规**、**探索** 和**侦察**。我们将在下面看看这个。

---

## 内置

OpenCode 带有两个内置主代理 agents 和三个内置子代理。

---

### 使用构建

_模式_：`primary`

构建是**默认**主要agent，启用了所有工具。这是用于需要完全访问文件操作和系统命令的开发工作的标准 agent。

---

### 使用计划

_模式_：`primary`

专为规划和分析而设计的受限 agent。我们使用权限系统为您提供更多控制并防止意外更改。
默认情况下，以下所有内容均设置为`ask`：

- `file edits`：所有写入、修补和编辑
- `bash`：所有 bash 命令

当您希望 LLM 分析代码、建议更改或创建计划而不对代码库进行任何实际修改时，此 agent 非常有用。

---

### 使用一般

_模式_：`subagent`

用于研究复杂问题和执行多步骤任务的通用 agent。具有完整的工具访问权限（待办事项除外），因此可以在需要时更改文件。使用它可以并行运行多个工作单元。

---

### 使用探索

_模式_：`subagent`

用于探索代码库的快速只读 agent。无法修改文件。当您需要按模式快速查找文件、搜索代码中的关键字或回答有关代码库的问题时，请使用此功能。

---

### 使用侦察兵

_模式_：`subagent`

用于外部文档和依赖性研究的只读 agent。当您需要将依赖项存储库克隆到 OpenCode 的托管缓存、检查库源或针对上游实现交叉引用本地代码而不修改工作区时，请使用此选项。

---

### 使用压实

_模式_：`primary`

隐藏系统 agent 将长上下文压缩为较小的摘要。它会在需要时自动运行，并且在 UI 中不可选择。

---

### 使用标题

_模式_：`primary`

生成短会话标题的隐藏系统 agent。它自动运行并且在UI中不可选择。

---

### 使用总结

_模式_：`primary`

创建会话摘要的隐藏系统 agent。它自动运行并且在UI中不可选择。

---

## 用途

1. 对于主 agents，请使用 **Tab** 键在会话期间循环浏览它们。您还可以使用配置的 `switch_agent` 按键绑定。

2. 可以调用子代理：
   - **由主agents自动**根据其描述执行专门任务。
   - 由 **@ 在您的消息中手动提及 ** 子代理。例如。

```txt
@general help me search for this function
```

3. **会话之间导航**：当子座席创建子会话时，使用`session_child_first`（默认：**\&lt;Leader&gt;+Down**）进入从父母。

4. 进入子会话后，请使用：
   - `session_child_cycle`（默认：**右**）循环到下一个子会话
   - `session_child_cycle_reverse`（默认：**左**）循环到上一个子会话
   - `session_parent`（默认：**Up**）返回父会话

这使您可以在主要对话和专门的子代理工作之间切换。

---

## 配置

您可以自定义内置agents或通过配置创建您自己的。 Agents可以通过两种方式配置：

---

### JSON

在 `opencode.json` 配置文件中配置 agents：

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "agent": {
    "build": {
      "mode": "primary",
      "model": "anthropic/claude-sonnet-4-20250514",
      "prompt": "{file:./prompts/build.txt}",
      "permission": {
        "edit": "allow",
        "bash": "allow"
      }
    },
    "plan": {
      "mode": "primary",
      "model": "anthropic/claude-haiku-4-20250514",
      "permission": {
        "edit": "deny",
        "bash": "deny"
      }
    },
    "code-reviewer": {
      "description": "Reviews code for best practices and potential issues",
      "mode": "subagent",
      "model": "anthropic/claude-sonnet-4-20250514",
      "prompt": "You are a code reviewer. Focus on security, performance, and maintainability.",
      "permission": {
        "edit": "deny"
      }
    }
  }
}
```

---

### 降价

您还可以使用 markdown 文件定义 agents。将它们放入：

- 全球：`~/.config/opencode/agents/`
- 每个项目：`.opencode/agents/`

**代码示例：** `~/.config/opencode/agents/review.md`

```markdown
---
description: Reviews code for quality and best practices
mode: subagent
model: anthropic/claude-sonnet-4-20250514
temperature: 0.1
permission:
  edit: deny
  bash: deny
---

You are in code review mode. Focus on:

- Code quality and best practices
- Potential bugs and edge cases
- Performance implications
- Security considerations

Provide constructive feedback without making direct changes.
```

Markdown 文件名变为 agent 名称。例如，`review.md` 创建一个 `review` agent。

---

## 选项

让我们详细看看这些配置选项。

---

### 描述

使用 `description` 选项提供 agent 的用途以及何时使用的简要说明。

**代码示例：** `opencode.json`

```json
{
  "agent": {
    "review": {
      "description": "Reviews code for best practices and potential issues"
    }
  }
}
```

这是**必需的**配置选项。

---

### 温度

使用 `temperature` 配置控制 LLM 响应的随机性和创造性。

较低的值使响应更加集中和确定，而较高的值会增加创造力和可变性。

**代码示例：** `opencode.json`

```json
{
  "agent": {
    "plan": {
      "temperature": 0.1
    },
    "creative": {
      "temperature": 0.8
    }
  }
}
```

温度值的范围通常为 0.0 到 1.0：

- **0.0-0.2**：非常集中且确定的响应，非常适合代码分析和规划
- **0.3-0.5**：具有一定创造力的平衡响应，适合一般开发任务
- **0.6-1.0**：更具创意和多样化的反应，有助于头脑风暴和探索

**代码示例：** `opencode.json`

```json
{
  "agent": {
    "analyze": {
      "temperature": 0.1,
      "prompt": "{file:./prompts/analysis.txt}"
    },
    "build": {
      "temperature": 0.3
    },
    "brainstorm": {
      "temperature": 0.7,
      "prompt": "{file:./prompts/creative.txt}"
    }
  }
}
```

如果未指定温度，OpenCode 使用特定于型号的默认值；对于大多数型号，通常为 0，对于 Qwen 型号，通常为 0.55。

---

### 最大步数

控制 agent 在被迫仅以文本响应之前可以执行的最大代理迭代次数。这允许希望控制成本的用户对代理操作设置限制。

如果未设置，agent 将继续迭代，直到模型选择停止或用户中断会话。

**代码示例：** `opencode.json`

```json
{
  "agent": {
    "quick-thinker": {
      "description": "Fast reasoning with limited iterations",
      "prompt": "You are a quick thinker. Solve problems with minimal steps.",
      "steps": 5
    }
  }
}
```

当达到限制时，agent 会收到特殊的系统提示，指示其响应其工作摘要和建议的剩余任务。

**注意：**

旧的 `maxSteps` 字段已弃用。请改用 `steps`。

---

### 禁用

设置为`true`可禁用agent。

**代码示例：** `opencode.json`

```json
{
  "agent": {
    "review": {
      "disable": true
    }
  }
}
```

---

### 提示

使用 `prompt` 配置为此 agent 指定自定义系统提示文件。提示文件应包含特定于 agent 用途的说明。

**代码示例：** `opencode.json`

```json
{
  "agent": {
    "review": {
      "prompt": "{file:./prompts/code-review.txt}"
    }
  }
}
```

该路径是相对于配置文件所在位置的。因此，这适用于全局 OpenCode 配置和项目特定配置。

---

### 型号

使用 `model` 配置覆盖此 agent 的模型。对于使用针对不同任务优化的不同模型很有用。例如，更快的规划模型、更强大的实施模型。

**提示：**

如果您不指定模型，则主代理 agents 使用全局配置的 [ 模型 ](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 配置位置、合并顺序与功能选项.md#models>)，而子代理将使用调用子代理的主代理 agent 的模型。

**代码示例：** `opencode.json`

```json
{
  "agent": {
    "plan": {
      "model": "anthropic/claude-haiku-4-20250514"
    }
  }
}
```

OpenCode 配置中的模型 ID 使用格式 `provider/model-id`。例如，如果您使用 [OpenCode Zen](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode Zen 模型服务、接入与团队配置.md>)，则可以使用 `opencode/gpt-5.1-codex` 代替 GPT 5.1 Codex。

---

### 工具（已弃用）

`tools` 已 ** 已弃用 **。首选 agent 的 [`permission`](<#权限>) 字段来获取新配置、更新和更细粒度的控制。

允许您控制此 agent 中可用的工具。您可以通过将特定工具设置为`true`或`false`来启用或禁用特定工具。在agent的`tools`配置中，`true`相当于`{"*": "allow"}`权限，`false`相当于`{"*": "deny"}`权限。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "tools": {
    "write": true,
    "bash": true
  },
  "agent": {
    "plan": {
      "tools": {
        "write": false,
        "bash": false
      }
    }
  }
}
```

**注释：**

agent 特定的配置会覆盖全局配置。

您还可以在旧版 `tools` 条目中使用通配符来同时控制多个工具。例如，要禁用 MCP 服务器中的所有工具：

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "agent": {
    "readonly": {
      "tools": {
        "mymcp_*": false,
        "write": false,
        "edit": false
      }
    }
  }
}
```

[了解有关工具的更多信息](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 内置、自定义与 MCP 工具.md>)。

---

### 权限

您可以配置权限来管理 agent 可以执行的操作。每个权限键可以设置为：

- `"ask"` - 运行工具之前提示批准
- `"allow"` — 未经批准允许所有操作
- `"deny"` - 禁用该工具

可用的权限键有：

|钥匙|工具门|
| -------------------- | ---------------------------------------------------------------- |
| `read`               | `read`                                                           |
| `edit`               | `write`, `edit`, `apply_patch`                                   |
| `glob`               | `glob`                                                           |
| `grep`               | `grep`                                                           |
| `list`               | `list`                                                           |
| `bash`               | `bash`                                                           |
| `task`               | `task`                                                           |
| `external_directory` |任何在项目工作树之外读取或写入文件的工具|
| `todowrite`          | `todowrite`, `todoread`                                          |
| `webfetch`           | `webfetch`                                                       |
| `websearch`          | `websearch`                                                      |
| `lsp`                | `lsp`                                                            |
| `skill`              | `skill`                                                          |
| `question`           | `question`                                                       |
| `doom_loop`          |当 agent 出现卡住时出现恢复提示|

`read`、`edit`、`glob`、`grep`、`list`、`bash`、`task`、`external_directory`、`lsp`、和 `skill` 接受速记操作 (`"allow" | "ask" | "deny"`) 或 glob/pattern → 细粒度控制操作的对象。其余键仅接受速记操作。

**注释：**

权限键作为通配符模式与基础工具名称进行匹配，因此相同的语法适用于内置工具、自定义工具和 MCP 工具 - 例如 `"mymcp_*": "deny"` 拒绝来自 MCP 服务器的每个工具，而 `"mymcp_search": "ask"` 则针对单个工具。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "edit": "deny"
  }
}
```

您可以根据 agent 覆盖这些权限。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "edit": "deny"
  },
  "agent": {
    "build": {
      "permission": {
        "edit": "ask"
      }
    }
  }
}
```

您还可以在 Markdown agents 中设置权限。

**代码示例：** `~/.config/opencode/agents/review.md`

```markdown
---
description: Code review without edits
mode: subagent
permission:
  edit: deny
  bash:
    "*": ask
    "git diff": allow
    "git log*": allow
    "grep *": allow
  webfetch: deny
---

Only analyze code and suggest changes.
```

您可以设置特定 bash 命令的权限。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "agent": {
    "build": {
      "permission": {
        "bash": {
          "git push": "ask",
          "grep *": "allow"
        }
      }
    }
  }
}
```

这可以采用全局模式。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "agent": {
    "build": {
      "permission": {
        "bash": {
          "git *": "ask"
        }
      }
    }
  }
}
```

并且您还可以使用`*`通配符来管理所有命令的权限。
由于最后一个匹配规则优先，因此将`*`通配符放在前面，然后将特定规则放在后面。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "agent": {
    "build": {
      "permission": {
        "bash": {
          "*": "ask",
          "git status *": "allow"
        }
      }
    }
  }
}
```

[了解有关权限的更多信息](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 工具权限与细粒度匹配.md>)。

---

### 模式

使用 `mode` 配置控制 agent 的模式。 `mode` 选项用于确定如何使用 agent。

**代码示例：** `opencode.json`

```json
{
  "agent": {
    "review": {
      "mode": "subagent"
    }
  }
}
```

`mode`选项可以设置为`primary`、`subagent`或`all`。如果未指定`mode`，则默认为`all`。

---

### 隐藏

使用 `hidden: true` 从 `@` 自动完成菜单中隐藏子代理。对于只能由其他 agents 通过任务工具以编程方式调用的内部子代理很有用。

**代码示例：** `opencode.json`

```json
{
  "agent": {
    "internal-helper": {
      "mode": "subagent",
      "hidden": true
    }
  }
}
```

这仅影响自动完成菜单中的用户可见性。如果权限允许，模型仍然可以通过任务工具调用隐藏的 agents。

**注释：**

仅适用于`mode: subagent` agents。

---

### 任务权限

使用 `permission.task` 控制 agent 可以通过任务工具调用哪些子代理。使用 glob 模式进行灵活匹配。

**代码示例：** `opencode.json`

```json
{
  "agent": {
    "orchestrator": {
      "mode": "primary",
      "permission": {
        "task": {
          "*": "deny",
          "orchestrator-*": "allow",
          "code-reviewer": "ask"
        }
      }
    }
  }
}
```

当设置为 `deny` 时，子代理将从任务工具描述中完全删除，因此模型不会尝试调用它。

**提示：**

规则按顺序进行评估，**最后匹配的规则获胜**。在上面的示例中，`orchestrator-planner` 匹配 `*`（拒绝）和 `orchestrator-*`（允许），但由于 `orchestrator-*` 位于 `*` 之后，因此结果为 `allow`。

**提示：**

用户始终可以通过 `@` 自动完成菜单直接调用任何子代理，即使 agent 的任务权限会拒绝它。

---

### 颜色

使用 `color` 选项自定义 UI 中 agent 的视觉外观。这会影响 agent 在界面中的显示方式。

使用有效的十六进制颜色（例如，`#FF5733`）或主题颜色：`primary`、`secondary`、`accent`、`success`、`warning`、`error`、 `info`。

**代码示例：** `opencode.json`

```json
{
  "agent": {
    "creative": {
      "color": "#ff6b6b"
    },
    "code-reviewer": {
      "color": "accent"
    }
  }
}
```

---

### 顶P

使用 `top_p` 选项控制响应多样性。控制随机性的温度替代方案。

**代码示例：** `opencode.json`

```json
{
  "agent": {
    "brainstorm": {
      "top_p": 0.9
    }
  }
}
```

值范围从 0.0 到 1.0。较低的值更加集中，较高的值更加多样化。

---

### 附加

您在 agent 配置中指定的任何其他选项都将 ** 直接通过 ** 作为模型选项传递给提供程序。这允许您使用提供商特定的功能和参数。

例如，使用OpenAI的推理模型，您可以控制推理工作：

**代码示例：** `opencode.json`

```json
{
  "agent": {
    "deep-thinker": {
      "description": "Agent that uses high reasoning effort for complex problems",
      "model": "openai/gpt-5",
      "reasoningEffort": "high",
      "textVerbosity": "low"
    }
  }
}
```

这些附加选项是特定于型号和提供商的。检查提供商的文档以获取可用参数。

**提示：**

运行 `opencode models` 查看可用型号的列表。

---

## 创建agents

您可以使用以下命令创建新的agents：

```bash
opencode agent create
```

此交互式命令将：

1. 询问【agent】保存在哪里；全局或特定项目。
2. agent 应该做什么的描述。
3. 生成适当的系统提示符和标识符。
4. 让您选择应允许 agent 的权限（您未选择的任何内容都将被拒绝）。
5. 最后，使用 agent 配置创建一个 markdown 文件。

---

## 使用案例

以下是不同 agents 的一些常见用例。

- **构建agent**：启用所有工具的完整开发工作
- **计划agent**：分析和规划，无需更改
- **审查agent**：使用只读访问权限和文档工具进行代码审查
- **调试agent**：专注于启用 bash 和读取工具的调查
- **Docs agent**：使用文件操作但没有系统命令的文档编写

---

## 示例

以下是一些您可能会觉得有用的示例 agents。

**提示：**

您有想要分享的 agent 吗？ [提交PR](<https://github.com/anomalyco/opencode>)。

---

### 文档 agent

**代码示例：** `~/.config/opencode/agents/docs-writer.md`

```markdown
---
description: Writes and maintains project documentation
mode: subagent
permission:
  bash: deny
---

You are a technical writer. Create clear, comprehensive documentation.

Focus on:

- Clear explanations
- Proper structure
- Code examples
- User-friendly language
```

---

### 安全审核员

**代码示例：** `~/.config/opencode/agents/security-auditor.md`

```markdown
---
description: Performs security audits and identifies vulnerabilities
mode: subagent
permission:
  edit: deny
---

You are a security expert. Focus on identifying potential security issues.

Look for:

- Input validation vulnerabilities
- Authentication and authorization flaws
- Data exposure risks
- Dependency vulnerabilities
- Configuration security issues
```


## 原文对照

<details>
<summary>展开完整原文</summary>

Agents are specialized AI assistants that can be configured for specific tasks and workflows. They allow you to create focused tools with custom prompts, models, and tool access.

**提示：**

Use the plan agent to analyze code and review suggestions without making any code changes.

You can switch between agents during a session or invoke them with the `@` mention.

---

## Types

There are two types of agents in OpenCode; primary agents and subagents.

---

### Primary agents

Primary agents are the main assistants you interact with directly. You can cycle through them using the **Tab** key, or your configured `switch_agent` keybind. These agents handle your main conversation. Tool access is configured via permissions — for example, Build has all tools enabled while Plan is restricted.

**提示：**

You can use the **Tab** key to switch between primary agents during a session.

OpenCode comes with two built-in primary agents, **Build** and **Plan**. We'll
look at these below.

---

### Subagents

Subagents are specialized assistants that primary agents can invoke for specific tasks. You can also manually invoke them by **@ mentioning** them in your messages.

OpenCode comes with three built-in subagents, **General**, **Explore**, and **Scout**. We'll look at this below.

---

## Built-in

OpenCode comes with two built-in primary agents and three built-in subagents.

---

### Use build

_Mode_: `primary`

Build is the **default** primary agent with all tools enabled. This is the standard agent for development work where you need full access to file operations and system commands.

---

### Use plan

_Mode_: `primary`

A restricted agent designed for planning and analysis. We use a permission system to give you more control and prevent unintended changes.
By default, all of the following are set to `ask`:

- `file edits`: All writes, patches, and edits
- `bash`: All bash commands

This agent is useful when you want the LLM to analyze code, suggest changes, or create plans without making any actual modifications to your codebase.

---

### Use general

_Mode_: `subagent`

A general-purpose agent for researching complex questions and executing multi-step tasks. Has full tool access (except todo), so it can make file changes when needed. Use this to run multiple units of work in parallel.

---

### Use explore

_Mode_: `subagent`

A fast, read-only agent for exploring codebases. Cannot modify files. Use this when you need to quickly find files by patterns, search code for keywords, or answer questions about the codebase.

---

### Use scout

_Mode_: `subagent`

A read-only agent for external docs and dependency research. Use this when you need to clone a dependency repository into OpenCode's managed cache, inspect library source, or cross-reference local code against upstream implementations without modifying your workspace.

---

### Use compaction

_Mode_: `primary`

Hidden system agent that compacts long context into a smaller summary. It runs automatically when needed and is not selectable in the UI.

---

### Use title

_Mode_: `primary`

Hidden system agent that generates short session titles. It runs automatically and is not selectable in the UI.

---

### Use summary

_Mode_: `primary`

Hidden system agent that creates session summaries. It runs automatically and is not selectable in the UI.

---

## Usage

1. For primary agents, use the **Tab** key to cycle through them during a session. You can also use your configured `switch_agent` keybind.

2. Subagents can be invoked:
   - **Automatically** by primary agents for specialized tasks based on their descriptions.
   - Manually by **@ mentioning** a subagent in your message. For example.

```txt
@general help me search for this function
```

3. **Navigation between sessions**: When subagents create child sessions, use `session_child_first` (default: **\&lt;Leader&gt;+Down**) to enter the first child session from the parent.

4. Once you are in a child session, use:
   - `session_child_cycle` (default: **Right**) to cycle to the next child session
   - `session_child_cycle_reverse` (default: **Left**) to cycle to the previous child session
   - `session_parent` (default: **Up**) to return to the parent session

   This lets you switch between the main conversation and specialized subagent work.

---

## Configure

You can customize the built-in agents or create your own through configuration. Agents can be configured in two ways:

---

### JSON

Configure agents in your `opencode.json` config file:

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "agent": {
    "build": {
      "mode": "primary",
      "model": "anthropic/claude-sonnet-4-20250514",
      "prompt": "{file:./prompts/build.txt}",
      "permission": {
        "edit": "allow",
        "bash": "allow"
      }
    },
    "plan": {
      "mode": "primary",
      "model": "anthropic/claude-haiku-4-20250514",
      "permission": {
        "edit": "deny",
        "bash": "deny"
      }
    },
    "code-reviewer": {
      "description": "Reviews code for best practices and potential issues",
      "mode": "subagent",
      "model": "anthropic/claude-sonnet-4-20250514",
      "prompt": "You are a code reviewer. Focus on security, performance, and maintainability.",
      "permission": {
        "edit": "deny"
      }
    }
  }
}
```

---

### Markdown

You can also define agents using markdown files. Place them in:

- Global: `~/.config/opencode/agents/`
- Per-project: `.opencode/agents/`

**代码示例：** `~/.config/opencode/agents/review.md`

```markdown
---
description: Reviews code for quality and best practices
mode: subagent
model: anthropic/claude-sonnet-4-20250514
temperature: 0.1
permission:
  edit: deny
  bash: deny
---

You are in code review mode. Focus on:

- Code quality and best practices
- Potential bugs and edge cases
- Performance implications
- Security considerations

Provide constructive feedback without making direct changes.
```

The markdown file name becomes the agent name. For example, `review.md` creates a `review` agent.

---

## Options

Let's look at these configuration options in detail.

---

### Description

Use the `description` option to provide a brief description of what the agent does and when to use it.

**代码示例：** `opencode.json`

```json
{
  "agent": {
    "review": {
      "description": "Reviews code for best practices and potential issues"
    }
  }
}
```

This is a **required** config option.

---

### Temperature

Control the randomness and creativity of the LLM's responses with the `temperature` config.

Lower values make responses more focused and deterministic, while higher values increase creativity and variability.

**代码示例：** `opencode.json`

```json
{
  "agent": {
    "plan": {
      "temperature": 0.1
    },
    "creative": {
      "temperature": 0.8
    }
  }
}
```

Temperature values typically range from 0.0 to 1.0:

- **0.0-0.2**: Very focused and deterministic responses, ideal for code analysis and planning
- **0.3-0.5**: Balanced responses with some creativity, good for general development tasks
- **0.6-1.0**: More creative and varied responses, useful for brainstorming and exploration

**代码示例：** `opencode.json`

```json
{
  "agent": {
    "analyze": {
      "temperature": 0.1,
      "prompt": "{file:./prompts/analysis.txt}"
    },
    "build": {
      "temperature": 0.3
    },
    "brainstorm": {
      "temperature": 0.7,
      "prompt": "{file:./prompts/creative.txt}"
    }
  }
}
```

If no temperature is specified, OpenCode uses model-specific defaults; typically 0 for most models, 0.55 for Qwen models.

---

### Max steps

Control the maximum number of agentic iterations an agent can perform before being forced to respond with text only. This allows users who wish to control costs to set a limit on agentic actions.

If this is not set, the agent will continue to iterate until the model chooses to stop or the user interrupts the session.

**代码示例：** `opencode.json`

```json
{
  "agent": {
    "quick-thinker": {
      "description": "Fast reasoning with limited iterations",
      "prompt": "You are a quick thinker. Solve problems with minimal steps.",
      "steps": 5
    }
  }
}
```

When the limit is reached, the agent receives a special system prompt instructing it to respond with a summarization of its work and recommended remaining tasks.

**注意：**

The legacy `maxSteps` field is deprecated. Use `steps` instead.

---

### Disable

Set to `true` to disable the agent.

**代码示例：** `opencode.json`

```json
{
  "agent": {
    "review": {
      "disable": true
    }
  }
}
```

---

### Prompt

Specify a custom system prompt file for this agent with the `prompt` config. The prompt file should contain instructions specific to the agent's purpose.

**代码示例：** `opencode.json`

```json
{
  "agent": {
    "review": {
      "prompt": "{file:./prompts/code-review.txt}"
    }
  }
}
```

This path is relative to where the config file is located. So this works for both the global OpenCode config and the project specific config.

---

### Model

Use the `model` config to override the model for this agent. Useful for using different models optimized for different tasks. For example, a faster model for planning, a more capable model for implementation.

**提示：**

If you don’t specify a model, primary agents use the [model globally configured](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 配置位置、合并顺序与功能选项.md#models>) while subagents will use the model of the primary agent that invoked the subagent.

**代码示例：** `opencode.json`

```json
{
  "agent": {
    "plan": {
      "model": "anthropic/claude-haiku-4-20250514"
    }
  }
}
```

The model ID in your OpenCode config uses the format `provider/model-id`. For example, if you're using [OpenCode Zen](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode Zen 模型服务、接入与团队配置.md>), you would use `opencode/gpt-5.1-codex` for GPT 5.1 Codex.

---

### Tools (deprecated)

`tools` is **deprecated**. Prefer the agent's [`permission`](<#permissions>) field for new configs, updates and more fine-grained control.

Allows you to control which tools are available in this agent. You can enable or disable specific tools by setting them to `true` or `false`. In an agent's `tools` config, `true` is equivalent to `{"*": "allow"}` permission and `false` is equivalent to `{"*": "deny"}` permission.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "tools": {
    "write": true,
    "bash": true
  },
  "agent": {
    "plan": {
      "tools": {
        "write": false,
        "bash": false
      }
    }
  }
}
```

**注释：**

The agent-specific config overrides the global config.

You can also use wildcards in legacy `tools` entries to control multiple tools at once. For example, to disable all tools from an MCP server:

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "agent": {
    "readonly": {
      "tools": {
        "mymcp_*": false,
        "write": false,
        "edit": false
      }
    }
  }
}
```

[Learn more about tools](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 内置、自定义与 MCP 工具.md>).

---

### Permissions

You can configure permissions to manage what actions an agent can take. Each permission key can be set to:

- `"ask"` — Prompt for approval before running the tool
- `"allow"` — Allow all operations without approval
- `"deny"` — Disable the tool

The available permission keys are:

| Key                  | Tools it gates                                                   |
| -------------------- | ---------------------------------------------------------------- |
| `read`               | `read`                                                           |
| `edit`               | `write`, `edit`, `apply_patch`                                   |
| `glob`               | `glob`                                                           |
| `grep`               | `grep`                                                           |
| `list`               | `list`                                                           |
| `bash`               | `bash`                                                           |
| `task`               | `task`                                                           |
| `external_directory` | Any tool that reads or writes files outside the project worktree |
| `todowrite`          | `todowrite`, `todoread`                                          |
| `webfetch`           | `webfetch`                                                       |
| `websearch`          | `websearch`                                                      |
| `lsp`                | `lsp`                                                            |
| `skill`              | `skill`                                                          |
| `question`           | `question`                                                       |
| `doom_loop`          | Recovery prompts when an agent appears stuck                     |

`read`, `edit`, `glob`, `grep`, `list`, `bash`, `task`, `external_directory`, `lsp`, and `skill` accept either a shorthand action (`"allow" | "ask" | "deny"`) or an object of glob/pattern → action for fine-grained control. The remaining keys accept the shorthand action only.

**注释：**

Permission keys are matched as wildcard patterns against the underlying tool name, so the same syntax works for built-ins, custom tools, and MCP tools — for example `"mymcp_*": "deny"` denies every tool from an MCP server, and `"mymcp_search": "ask"` targets a single one.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "edit": "deny"
  }
}
```

You can override these permissions per agent.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "edit": "deny"
  },
  "agent": {
    "build": {
      "permission": {
        "edit": "ask"
      }
    }
  }
}
```

You can also set permissions in Markdown agents.

**代码示例：** `~/.config/opencode/agents/review.md`

```markdown
---
description: Code review without edits
mode: subagent
permission:
  edit: deny
  bash:
    "*": ask
    "git diff": allow
    "git log*": allow
    "grep *": allow
  webfetch: deny
---

Only analyze code and suggest changes.
```

You can set permissions for specific bash commands.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "agent": {
    "build": {
      "permission": {
        "bash": {
          "git push": "ask",
          "grep *": "allow"
        }
      }
    }
  }
}
```

This can take a glob pattern.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "agent": {
    "build": {
      "permission": {
        "bash": {
          "git *": "ask"
        }
      }
    }
  }
}
```

And you can also use the `*` wildcard to manage permissions for all commands.
Since the last matching rule takes precedence, put the `*` wildcard first and specific rules after.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "agent": {
    "build": {
      "permission": {
        "bash": {
          "*": "ask",
          "git status *": "allow"
        }
      }
    }
  }
}
```

[Learn more about permissions](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 工具权限与细粒度匹配.md>).

---

### Mode

Control the agent's mode with the `mode` config. The `mode` option is used to determine how the agent can be used.

**代码示例：** `opencode.json`

```json
{
  "agent": {
    "review": {
      "mode": "subagent"
    }
  }
}
```

The `mode` option can be set to `primary`, `subagent`, or `all`. If no `mode` is specified, it defaults to `all`.

---

### Hidden

Hide a subagent from the `@` autocomplete menu with `hidden: true`. Useful for internal subagents that should only be invoked programmatically by other agents via the Task tool.

**代码示例：** `opencode.json`

```json
{
  "agent": {
    "internal-helper": {
      "mode": "subagent",
      "hidden": true
    }
  }
}
```

This only affects user visibility in the autocomplete menu. Hidden agents can still be invoked by the model via the Task tool if permissions allow.

**注释：**

Only applies to `mode: subagent` agents.

---

### Task permissions

Control which subagents an agent can invoke via the Task tool with `permission.task`. Uses glob patterns for flexible matching.

**代码示例：** `opencode.json`

```json
{
  "agent": {
    "orchestrator": {
      "mode": "primary",
      "permission": {
        "task": {
          "*": "deny",
          "orchestrator-*": "allow",
          "code-reviewer": "ask"
        }
      }
    }
  }
}
```

When set to `deny`, the subagent is removed from the Task tool description entirely, so the model won't attempt to invoke it.

**提示：**

Rules are evaluated in order, and the **last matching rule wins**. In the example above, `orchestrator-planner` matches both `*` (deny) and `orchestrator-*` (allow), but since `orchestrator-*` comes after `*`, the result is `allow`.

**提示：**

Users can always invoke any subagent directly via the `@` autocomplete menu, even if the agent's task permissions would deny it.

---

### Color

Customize the agent's visual appearance in the UI with the `color` option. This affects how the agent appears in the interface.

Use a valid hex color (e.g., `#FF5733`) or theme color: `primary`, `secondary`, `accent`, `success`, `warning`, `error`, `info`.

**代码示例：** `opencode.json`

```json
{
  "agent": {
    "creative": {
      "color": "#ff6b6b"
    },
    "code-reviewer": {
      "color": "accent"
    }
  }
}
```

---

### Top P

Control response diversity with the `top_p` option. Alternative to temperature for controlling randomness.

**代码示例：** `opencode.json`

```json
{
  "agent": {
    "brainstorm": {
      "top_p": 0.9
    }
  }
}
```

Values range from 0.0 to 1.0. Lower values are more focused, higher values more diverse.

---

### Additional

Any other options you specify in your agent configuration will be **passed through directly** to the provider as model options. This allows you to use provider-specific features and parameters.

For example, with OpenAI's reasoning models, you can control the reasoning effort:

**代码示例：** `opencode.json`

```json
{
  "agent": {
    "deep-thinker": {
      "description": "Agent that uses high reasoning effort for complex problems",
      "model": "openai/gpt-5",
      "reasoningEffort": "high",
      "textVerbosity": "low"
    }
  }
}
```

These additional options are model and provider-specific. Check your provider's documentation for available parameters.

**提示：**

Run `opencode models` to see a list of the available models.

---

## Create agents

You can create new agents using the following command:

```bash
opencode agent create
```

This interactive command will:

1. Ask where to save the agent; global or project-specific.
2. Description of what the agent should do.
3. Generate an appropriate system prompt and identifier.
4. Let you select which permissions the agent should be allowed (anything you don't select is denied).
5. Finally, create a markdown file with the agent configuration.

---

## Use cases

Here are some common use cases for different agents.

- **Build agent**: Full development work with all tools enabled
- **Plan agent**: Analysis and planning without making changes
- **Review agent**: Code review with read-only access plus documentation tools
- **Debug agent**: Focused on investigation with bash and read tools enabled
- **Docs agent**: Documentation writing with file operations but no system commands

---

## Examples

Here are some example agents you might find useful.

**提示：**

Do you have an agent you'd like to share? [Submit a PR](<https://github.com/anomalyco/opencode>).

---

### Documentation agent

**代码示例：** `~/.config/opencode/agents/docs-writer.md`

```markdown
---
description: Writes and maintains project documentation
mode: subagent
permission:
  bash: deny
---

You are a technical writer. Create clear, comprehensive documentation.

Focus on:

- Clear explanations
- Proper structure
- Code examples
- User-friendly language
```

---

### Security auditor

**代码示例：** `~/.config/opencode/agents/security-auditor.md`

```markdown
---
description: Performs security audits and identifies vulnerabilities
mode: subagent
permission:
  edit: deny
---

You are a security expert. Focus on identifying potential security issues.

Look for:

- Input validation vulnerabilities
- Authentication and authorization flaws
- Data exposure risks
- Dependency vulnerabilities
- Configuration security issues
```


</details>
