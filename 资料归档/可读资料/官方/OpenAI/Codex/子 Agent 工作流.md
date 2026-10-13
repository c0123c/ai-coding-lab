# 子 Agent 工作流

介绍委派角色、调用条件、模型与推理级别选择及上下文组织，说明哪些任务适合并行或分工。

- 来源：OpenAI／Codex
- 资料类型：存档文档
- 文档时间：官方未公开发表/更新时间（[已复查](<https://learn.chatgpt.com/docs/agent-configuration/subagents>)）；本地抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://learn.chatgpt.com/docs/agent-configuration/subagents.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Codex/子 Agent 工作流.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 子代理

> 完整的文档索引，请参见[llms.txt](<https://learn.chatgpt.com/llms.txt>)。通过将 `.md` 附加到页面 URL 可以获得文档页面的 Markdown 版本。

ChatGPT 工作和 Codex 可以通过生成专门的子代理工作流程来运行子代理工作流程
agents 并行，然后将结果收集到一个响应中。这个可以
对于高度并行的复杂任务特别有帮助，例如
代码库探索或实施多步骤功能计划。

在本地Codex客户端中，您还可以为不同的任务定义具有不同模型
配置和指令的自定义agents。

## 可用性

<ContentModeSwitch group="codex-surface" id="web">

ChatGPT Work 向符合条件的账户公开子代理工作流程和活动。

</ContentModeSwitch>

<a id="custom-agents"></a>

<ContentModeSwitch group="codex-surface" ids="app,cli,ide">

当前的 Codex 版本默认启用子代理工作流程。子代理活动 
 显示在 ChatGPT 桌面应用程序、Codex CLI 和 IDE 扩展中。

</ContentModeSwitch>

由于每个子代理执行自己的模型和工具工作，因此子代理工作流 
 比同类的单一 agent 运行消耗更多的令牌。

<ContentModeSwitch group="codex-surface" id="web">

在ChatGPT工作中，要求ChatGPT将独立工作委派给子代理。 
agents 在 ChatGPT 的托管环境中运行，聊天显示他们的 
 活动和结果。在大多数情报级别，明确要求授权
。借助 Ultra，当并行 
agents 能够显着提高速度或质量时，ChatGPT 可以主动委派工作。

</ContentModeSwitch>

<ContentModeSwitch group="codex-surface" id="app">

在应用程序聊天中请求 Codex 将工作的独立部分委托给 
 子代理。当您直接询问或当
适用`AGENTS.md`或技能指令要求时，当前本地Codex会释放委托。该应用程序显示每个
子代理线程，以便您可以检查其工作以及返回到主
聊天的摘要。

</ContentModeSwitch>

<ContentModeSwitch group="codex-surface" id="cli">

在交互式 CLI 会话中请求 Codex 使用子代理。 Codex也可以按照
适用的`AGENTS.md`或请求委托的技能说明。使用 
`/agent` 在 agent 线程运行时检查并在它们之间切换。主
线程将子代理结果收集到其最终响应中。

</ContentModeSwitch>

<ContentModeSwitch group="codex-surface" id="ide">

在 IDE 聊天中要求 Codex 将工作的独立部分委派给子代理。
Codex 还可以遵循适用的 `AGENTS.md` 或请求 
 委派的技能说明。当背景 agent UI 可用时，活动子代理会出现在作曲家上方 
。展开面板以查看其状态，停止所有活动的
子代理，或打开单个子代理线程。

</ContentModeSwitch>

## 为什么子代理工作流程有帮助

即使有很大的上下文窗口，模型也有局限性。如果您用嘈杂的中间输出（例如探索笔记、测试日志、堆栈跟踪和命令输出）淹没主聊天（在其中定义需求、约束和决策），随着时间的推移，会话的可靠性可能会降低。

这通常被描述为：

- **上下文污染**：有用的信息被淹没在嘈杂的中间输出下。
- **上下文腐烂**：当聊天中充满了不太相关的细节时，性能会下降。

有关背景信息，请参阅 [context rot](<https://research.trychroma.com/context-rot>) 上的 Chroma 文章。

子代理工作流程有助于将嘈杂的工作移出主线程：

- 让**主要agent**专注于需求、决策和最终输出。
- 并行运行专门的 ** 子代理 ** 以进行探索、测试或日志分析。
- 从子代理返回 ** 摘要 **，而不是原始中间输出。

当工作可以独立并行运行时，它们还可以节省时间，并且
它们通过将较大形状的任务分成有界的
块，使它们变得更容易处理。例如，Codex 可以将数百万个令牌的 
 文档的分析拆分为更小的问题，并将提炼的内容返回到主 
 线程。

作为起点，使用并行 agents 执行读取繁重的任务，例如 
 探索、测试、分类和汇总。对并行
写入量大的工作流程要更加小心，因为agents立即编辑代码可能会产生
冲突并增加协调开销。

## 核心术语

Codex 在子代理工作流程中使用了一些相关术语：

- **子代理工作流程**：Codex并行运行agents并组合其结果的工作流程。
- **子代理**：Codex开始处理特定任务的委托agent。
- **Agent 线程**：子代理执行其工作的线程。支持的客户端允许您打开这些线程来检查进度或结果。

## 触发子代理工作流程

<ContentModeSwitch group="codex-surface" id="web">

在大多数智力水平上，直接请求子代理或并行agent工作
。 Ultra 支持主动委派，因此 ChatGPT 可以委派合适的 
 独立工作，而无需单独请求。

</ContentModeSwitch>

<ContentModeSwitch group="codex-surface" ids="app,cli,ide">

直接要求子代理或并行agent工作。当
适用项目或技能指令要求时，Codex也可以委派。

</ContentModeSwitch>

实际上，手动触发意味着使用直接指令，例如
“生成两个agents”、“并行委托此工作”或“每个
点使用一个agent”。子代理工作流程比同类的单 agent 运行 
 消耗更多的令牌，因为每个子代理执行自己的模型和工具工作。

一个好的子代理提示应该解释如何划分工作，Codex
是否应该等待所有agents再继续，以及
返回什么摘要或输出。

```text
Review this branch with parallel subagents. Spawn one subagent for security risks, one for test gaps, and one for maintainability. Wait for all three, then summarize the findings by category with file references.
```

## 选择模型和推理

不同的agents需要不同的模型和推理设置。

<ContentModeSwitch group="codex-surface" id="web">

在ChatGPT作品中，从作曲家处选择模型和智力级别。
可用的智力级别可以包括**轻度**、**中度**、 **高**、
**超高**和**最大**，具体取决于所选型号。 **Ultra** 
仅适用于符合条件的帐户和支持的型号。它使用最大
推理，并让ChatGPT主动将合适的工作委派给子代理。

在其他情报级别，当您想要并行委派工作
时，请明确请求子代理。

</ContentModeSwitch>

<ContentModeSwitch group="codex-surface" ids="app,cli,ide">

如果您不配置子代理模型或 `model_reasoning_effort`，
 子代理将继承父级 agent 的模型和推理工作。如果显式 
 生成请求或 `[agents]` 默认选择没有 
 显式或配置推理工作的模型，则子代理将使用该模型的默认 
 推理工作。为了平衡每项任务的智能、速度和价格，
在提示中请求特定模型或推理工作，
在`config.toml`中配置`[agents]`默认值，或直接在自定义中设置`model`和
`model_reasoning_effort` agent 文件。
例如，使用 `gpt-6-luna` 进行快速扫描，或使用更高效的 `gpt-6.1-sol` 配置进行更苛刻的推理。

对于大多数任务 Codex, 开始于 
  `gpt-6.1-sol` 当你的
  登录的帐户或工作区有 [访问](<https://learn.chatgpt.com/docs/models#gpt-6.1-sol>).
  否则，请选择您可用的型号。使用 `gpt-6-luna` 当你想要一个
  更快、更低成本的选项，可实现更轻松的子代理工作。

### 型号选择

- **`gpt-6.1-sol`**：从这里开始满足要求agents。将其用于需要在更大的环境中进行规划、工具使用、验证和跟进的模糊、多步骤工作。
- **`gpt-6-luna`**：用于快速、范围狭窄的 agents 处理清晰、可重复或大批量的工作。

### 推理努力（`model_reasoning_effort`）

对于 GPT-6.1 Sol，请使用您的客户支持的推理工作并选择 
 模型。对于显式模型设置，从 GPT-6 Luna 的 `high` 或 GPT-6 Astra 的 `low`
 开始。使用所选模型支持的级别调整任务。

- **`ultra`**：当所选模型支持时用于最深入的推理
它。
- **`max`** 和 **`xhigh`**：用于特别要求的推理，当
所选模型支持这些级别。
- **`high`**：当 agent 需要跟踪复杂逻辑、检查假设或处理边缘情况（例如，审阅者或关注安全的 agents）时使用。
- **`medium`**：平衡速度和深度。
- **`low`**：当任务简单并且速度最重要时使用。

更高的推理工作会增加响应时间和令牌使用，但它可以提高复杂工作的质量。详细信息请参见【[】型号【](<https://learn.chatgpt.com/docs/models>)】、【[】配置基础【](<https://learn.chatgpt.com/docs/config-file/config-basic>)】、【[】配置参考【](<https://learn.chatgpt.com/docs/config-file/config-reference>)】。

</ContentModeSwitch>

## 编排和线程控制

ChatGPT 或 Codex 处理 agents 之间的编排，包括生成新的 
 子代理、路由后续指令、等待结果以及关闭 
agent 线程。

当许多 agents 正在运行时，Codex 会等待，直到所有请求的结果都 
 可用，然后返回合并的响应。

<ContentModeSwitch group="codex-surface" id="web">

在大多数智力水平下，ChatGPT 在直接请求后会生成 agents。借助 
Ultra，ChatGPT 还可以在并行工作有用时主动委派。

</ContentModeSwitch>

<ContentModeSwitch group="codex-surface" ids="app,cli,ide">

当前本地 Codex 在直接请求或适用的 
 项目或技能指令后发布了生成 agents。

</ContentModeSwitch>

要查看它的实际效果，请在您的项目中尝试以下提示：

```text
I would like to review the following points on the current PR (this branch vs main). Spawn one agent per point, wait for all of them, and summarize the result for each point.
1. Security issue
2. Code quality
3. Bugs
4. Race
5. Test flakiness
6. Maintainability of the code
```

## 管理子代理

<ContentModeSwitch group="codex-surface" id="web">

打开 **Subagents** 以查看只读 **Active** 和 **Done** 列表。选择
已完成的子代理以检查其详细信息和结果。网络侧边栏报告
子代理活动；它不提供控制来停止或引导单个
子代理。

</ContentModeSwitch>

<ContentModeSwitch group="codex-surface" id="app">

- 从主线程中显示的活动打开子代理线程进行检查
它的工作。
- 直接请求 Codex 引导正在运行的子代理、停止它或关闭已完成
子代理线程。



> 插图：Codex 桌面聊天显示两个并行工作的子代理。





> 插图：Codex 桌面子代理面板，没有活动子代理且已完成三个审核。



</ContentModeSwitch>

<ContentModeSwitch group="codex-surface" id="cli">

- 使用 CLI 中的 `/agent` 在活动的 agent 线程之间切换并检查正在进行的线程。
- 直接要求 Codex 引导正在运行的子代理、停止它或关闭已完成的 agent 线程。

</ContentModeSwitch>

<ContentModeSwitch group="codex-surface" id="ide">

- 当背景-agent面板可用时，将其展开以检查状态，
停止活动的子代理，或打开子代理线程。
- 直接请求 Codex 引导正在运行的子代理、停止它或关闭已完成
子代理线程。

</ContentModeSwitch>

## 批准和沙箱控制

<ContentModeSwitch group="codex-surface" ids="app,cli,ide">

子代理继承您当前的沙箱策略。

</ContentModeSwitch>

<ContentModeSwitch group="codex-surface" id="web">

ChatGPT Work 在其托管环境中运行子代理，并且不会公开 
 本地 Codex 沙箱或审批模式控件。子代理使用父聊天可用的工具
。网站和连接器权限仍然是
工具特定的。

</ContentModeSwitch>

<ContentModeSwitch group="codex-surface" id="app">

子代理继承在编写器下选择的权限模式。在要求Codex委派工作之前，请选择父轮的
权限模式。

</ContentModeSwitch>

<ContentModeSwitch group="codex-surface" id="cli">

在交互式 CLI 会话中，即使您正在查看主线程，审批请求也可能会从不活动的 agent
 线程中出现。批准叠加层 
 显示源线程标签，您可以在 
 批准、拒绝或答复请求之前按 `o` 打开该线程。

在非交互式流程中，或者只要运行无法显示新的批准，需要新批准的 
 操作就会失败，并且 Codex 将错误显示回 
 父工作流。

Codex 在生成 
 子代时也会重新应用父回合的实时运行时覆盖。这包括您在 
 会话期间以交互方式设置的沙箱和批准选项，例如 `/permissions` 更改或 `--yolo`，即使所选的 
 自定义 agent 文件设置不同的默认值也是如此。

</ContentModeSwitch>

<ContentModeSwitch group="codex-surface" id="ide">

子代理继承在编写器下选择的权限模式。在要求Codex委派工作之前，选择
父轮的权限模式。

</ContentModeSwitch>

<ContentModeSwitch group="codex-surface" ids="app,cli,ide">

您还可以覆盖单个 [ 自定义 agents](<#定制agents>) 的沙箱配置，例如显式标记其在只读模式下工作。

## 定制【agents】

Codex 附带内置 agents：

- `default`：通用回退agent。
- `worker`：以执行为中心的agent，用于实施和修复。
- `explorer`：大量阅读的代码库探索agent。

定义您自己的定制 agents,添加独立的 TOML 文件下
`~/.codex/agents/` 对于个人 agents 或 `.codex/agents/` 对于项目范围的
agents.

每个文件定义一个自定义 agent。 Codex 将这些文件加载​​为生成会话的配置 
 层，因此自定义 agents 可以覆盖与 
 正常 Codex 会话配置相同的设置。这可能比专用的 agent
 清单更重，并且该格式可能会随着创作和共享的成熟而发展。

每个独立的自定义 agent 文件必须定义：

- `name`
- `description`
- `developer_instructions`

如果自定义 agent 文件设置 `model` 或 `model_reasoning_effort`，则 
 文件中的值优先。在应用文件之前，Codex 从显式生成值解析每个设置 
，然后解析相应的 `[agents]` 默认值，然后解析 
 父级的值。如果显式生成请求或 `[agents]` 默认 
 选择模型并且两者都不提供推理工作，则 Codex 使用 
 该模型的默认工作。仅设置 `model`
 的自定义 agent 文件保留了先前解决的工作。如果所选模型不支持该工作或者您想要 
 不同的模型，请在 
 文件中设置 `model_reasoning_effort`。当自定义 agent 文件省略 
 时，其他会话设置（例如 `sandbox_mode`、`mcp_servers`、
 和 `skills.config`）将从父级继承。

### 全局设置

全局子代理设置仍然位于 [ 配置 ](<https://learn.chatgpt.com/docs/config-file/config-basic#configuration-precedence>) 中的 `[agents]` 下。

|领域|类型|必填|目的|
| ------------------------------------------- | ------- | :------: | ------------------------------------------------------------------- |
| `agents.enabled`                            |布尔值|否|启用或禁用多agent工具。|
| `agents.max_concurrent_threads_per_session` |数量|否|限制同时打开衍生的 agent 线程，不包括主线程。|
| `agents.default_subagent_model`             |字符串|否|设置生成的默认模型 agents。|
| `agents.default_subagent_reasoning_effort`  |字符串|否|设置生成的 agents 的默认推理工作。|
| `agents.interrupt_message`                  |布尔值|否|当 agent 转弯中断时记录模型可见的消息。|

**注释：**

- `agents.enabled` 默认为 `true`。将其设置为`false`以禁用多agent工具。
- 当您未设置 `agents.max_concurrent_threads_per_session` 时，Codex 将选择默认值。现有配置可以继续使用 `agents.max_threads` 作为旧别名。
- 显式生成值覆盖 `agents.default_subagent_model` 和 `agents.default_subagent_reasoning_effort`。
- `agents.interrupt_message` 默认为 `true`。将其设置为 `false` 以忽略 agent 上下文中的模型可见中断消息。
- 如果自定义 agent 名称与内置 agent 匹配，例如 `explorer`，则您的自定义 agent 优先。

### 自定义 agent 文件架构

|领域|类型|必填|目的|
| ------------------------ | ------ | :------: | --------------------------------------------------------------- |
| `name`                   |字符串|是的|Agent 名称 Codex 在生成或引用此 agent 时使用。|
| `description`            |字符串|是的|关于何时 Codex 应使用此 agent 的人性化指导。|
| `developer_instructions` |字符串|是的|定义 agent 行为的核心指令。|

您还可以在自定义 agent 文件中包含其他受支持的 `config.toml` 密钥，例如 `model`、`model_reasoning_effort`、`sandbox_mode`、`mcp_servers` 和 `skills.config`。

Codex 通过其 `name` 字段标识自定义 agent。将文件名与 
 和 agent 名称相匹配是最简单的约定，但 `name` 字段才是事实的来源 
。

### 自定义示例 agents

最好的定制agents是狭隘的和固执己见的。为每一项明确的工作提供一个与该工作相匹配的
工具表面，以及防止其
漂移到相邻工作中的说明。

这些示例使用 [GPT-6.1 Sol](<https://learn.chatgpt.com/docs/models#gpt-6.1-sol>)，您的登录 
 帐户或工作区具有访问权限。如果不可用，请选择您可以
使用的型号。

#### 示例 1：PR 评论

此模式将审核分为三个重点自定义 agents：

- `pr_explorer` 映射代码库并收集证据。
- `reviewer` 寻找正确性、安全性和测试风险。
- `docs_researcher`通过专用的MCP服务器检查框架或API文档。

项目配置（`.codex/config.toml`）：

```toml
[agents]
max_concurrent_threads_per_session = 8
```

`.codex/agents/pr-explorer.toml`:

```toml
name = "pr_explorer"
description = "Read-only codebase explorer for gathering evidence before changes are proposed."
model = "gpt-6-luna"
model_reasoning_effort = "high"
sandbox_mode = "read-only"
developer_instructions = """
Stay in exploration mode.
Trace the real execution path, cite files and symbols, and avoid proposing fixes unless the parent agent asks for them.
Prefer fast search and targeted file reads over broad scans.
"""
```

`.codex/agents/reviewer.toml`:

```toml
name = "reviewer"
description = "PR reviewer focused on correctness, security, and missing tests."
model = "gpt-6.1-sol"
model_reasoning_effort = "medium"
sandbox_mode = "read-only"
developer_instructions = """
Review code like an owner.
Prioritize correctness, security, behavior regressions, and missing test coverage.
Lead with concrete findings, include reproduction steps when possible, and avoid style-only comments unless they hide a real bug.
"""
```


`.codex/agents/docs-researcher.toml`:

```toml
name = "docs_researcher"
description = "Documentation specialist that uses the docs MCP server to verify APIs and framework behavior."
model = "gpt-6-luna"
model_reasoning_effort = "high"
sandbox_mode = "read-only"
developer_instructions = """
Use the docs MCP server to confirm APIs, options, and version-specific behavior.
Return concise answers with links or exact references when available.
Do not make code changes.
"""

[mcp_servers.openaiDeveloperDocs]
url = "https://developers.openai.com/mcp"
```

此设置适用于以下提示：

```text
Review this branch against main. Have pr_explorer map the affected code paths, reviewer find real risks, and docs_researcher verify the framework APIs that the patch relies on.
```

#### 示例2：前端集成调试

此模式对于 UI 回归、不稳定的浏览器流程或跨应用程序代码和正在运行的产品的集成错误很有用。

项目配置（`.codex/config.toml`）：

```toml
[agents]
max_concurrent_threads_per_session = 6
```

`.codex/agents/code-mapper.toml`:

```toml
name = "code_mapper"
description = "Read-only codebase explorer for locating the relevant frontend and backend code paths."
model = "gpt-6-luna"
model_reasoning_effort = "high"
sandbox_mode = "read-only"
developer_instructions = """
Map the code that owns the failing UI flow.
Identify entry points, state transitions, and likely files before the worker starts editing.
"""
```

`.codex/agents/browser-debugger.toml`:

```toml
name = "browser_debugger"
description = "UI debugger that uses browser tooling to reproduce issues and capture evidence."
model = "gpt-6.1-sol"
model_reasoning_effort = "medium"
sandbox_mode = "workspace-write"
developer_instructions = """
Reproduce the issue in the browser, capture exact steps, and report what the UI actually does.
Use browser tooling for screenshots, console output, and network evidence.
Do not edit application code.
"""
[mcp_servers.chrome_devtools]
url = "http://localhost:3000/mcp"
startup_timeout_sec = 20
```


`.codex/agents/ui-fixer.toml`:

```toml
name = "ui_fixer"
description = "Implementation-focused agent for small, targeted fixes after the issue is understood."
model = "gpt-6-luna"
model_reasoning_effort = "high"
developer_instructions = """
Own the fix once the issue is reproduced.
Make the smallest defensible change, keep unrelated files untouched, and validate only the behavior you changed.
"""

[[skills.config]]
path = "/Users/me/.agents/skills/docs-editor/SKILL.md"
enabled = false
```

此设置适用于以下提示：

```text
Investigate why the settings modal fails to save. Have browser_debugger reproduce it, code_mapper trace the responsible code path, and ui_fixer implement the smallest fix once the failure mode is clear.
```

</ContentModeSwitch>


## 原文对照

<details>
<summary>展开完整原文</summary>

# Subagents

> For the complete documentation index, see [llms.txt](<https://learn.chatgpt.com/llms.txt>). Markdown versions of documentation pages are available by appending `.md` to the page URL.

ChatGPT Work and Codex can run subagent workflows by spawning specialized
agents in parallel and then collecting their results in one response. This can
be particularly helpful for complex tasks that are highly parallel, such as
codebase exploration or implementing a multi-step feature plan.

In local Codex clients, you can also define custom agents with different model
configurations and instructions for different tasks.

## Availability

<ContentModeSwitch group="codex-surface" id="web">

ChatGPT Work exposes subagent workflows and activity to eligible accounts.

</ContentModeSwitch>

<a id="custom-agents"></a>

<ContentModeSwitch group="codex-surface" ids="app,cli,ide">

Current Codex releases enable subagent workflows by default. Subagent activity
appears in the ChatGPT desktop app, Codex CLI, and the IDE extension.

</ContentModeSwitch>

Because each subagent does its own model and tool work, subagent workflows
consume more tokens than comparable single-agent runs.

<ContentModeSwitch group="codex-surface" id="web">

In ChatGPT Work, ask ChatGPT to delegate independent work to subagents. The
agents run in ChatGPT's hosted environment, and the chat shows their
activity and results. At most intelligence levels, ask for delegation
explicitly. With Ultra, ChatGPT can proactively delegate work when parallel
agents would materially improve speed or quality.

</ContentModeSwitch>

<ContentModeSwitch group="codex-surface" id="app">

Ask Codex in an app chat to delegate independent parts of the work to
subagents. Current local Codex releases delegate when you ask directly or when
applicable `AGENTS.md` or skill instructions request it. The app surfaces each
subagent thread so you can inspect its work and the summary returned to the main
chat.

</ContentModeSwitch>

<ContentModeSwitch group="codex-surface" id="cli">

Ask Codex in an interactive CLI session to use subagents. Codex can also follow
applicable `AGENTS.md` or skill instructions that request delegation. Use
`/agent` to inspect and switch between agent threads while they run. The main
thread collects the subagent results into its final response.

</ContentModeSwitch>

<ContentModeSwitch group="codex-surface" id="ide">

Ask Codex in an IDE chat to delegate independent parts of the work to subagents.
Codex can also follow applicable `AGENTS.md` or skill instructions that request
delegation. When the background-agent UI is available, active subagents appear
above the composer. Expand the panel to see their status, stop all active
subagents, or open an individual subagent thread.

</ContentModeSwitch>

## Why subagent workflows help

Even with large context windows, models have limits. If you flood the main chat (where you're defining requirements, constraints, and decisions) with noisy intermediate output such as exploration notes, test logs, stack traces, and command output, the session can become less reliable over time.

This is often described as:

- **Context pollution**: useful information gets buried under noisy intermediate output.
- **Context rot**: performance degrades as the chat fills up with less relevant details.

For background, see the Chroma writeup on [context rot](<https://research.trychroma.com/context-rot>).

Subagent workflows help by moving noisy work off the main thread:

- Keep the **main agent** focused on requirements, decisions, and final outputs.
- Run specialized **subagents** in parallel for exploration, tests, or log analysis.
- Return **summaries** from subagents instead of raw intermediate output.

They can also save time when the work can run independently in parallel, and
they make larger-shaped tasks more tractable by breaking them into bounded
pieces. For example, Codex can split analysis of a multi-million-token
document into smaller problems and return distilled takeaways to the main
thread.

As a starting point, use parallel agents for read-heavy tasks such as
exploration, tests, triage, and summarization. Be more careful with parallel
write-heavy workflows, because agents editing code at once can create
conflicts and increase coordination overhead.

## Core terms

Codex uses a few related terms in subagent workflows:

- **Subagent workflow**: A workflow where Codex runs parallel agents and combines their results.
- **Subagent**: A delegated agent that Codex starts to handle a specific task.
- **Agent thread**: The thread where a subagent does its work. Supported clients let you open these threads to inspect progress or results.

## Triggering subagent workflows

<ContentModeSwitch group="codex-surface" id="web">

At most intelligence levels, ask for subagents or parallel agent work
directly. Ultra enables proactive delegation, so ChatGPT can delegate suitable
independent work without a separate request.

</ContentModeSwitch>

<ContentModeSwitch group="codex-surface" ids="app,cli,ide">

Ask for subagents or parallel agent work directly. Codex can also delegate when
applicable project or skill instructions request it.

</ContentModeSwitch>

In practice, manual triggering means using direct instructions such as
"spawn two agents," "delegate this work in parallel," or "use one agent per
point." Subagent workflows consume more tokens than comparable single-agent runs
because each subagent does its own model and tool work.

A good subagent prompt should explain how to divide the work, whether Codex
should wait for all agents before continuing, and what summary or output to
return.

```text
Review this branch with parallel subagents. Spawn one subagent for security risks, one for test gaps, and one for maintainability. Wait for all three, then summarize the findings by category with file references.
```

## Choosing models and reasoning

Different agents need different model and reasoning settings.

<ContentModeSwitch group="codex-surface" id="web">

In ChatGPT Work, choose a model and an intelligence level from the composer.
Available intelligence levels can include **Light**, **Medium**, **High**,
**Extra High**, and **Max**, depending on the selected model. **Ultra** is
available only to eligible accounts and supported models. It uses maximum
reasoning and lets ChatGPT proactively delegate suitable work to subagents.

At other intelligence levels, ask for subagents explicitly when you want work
delegated in parallel.

</ContentModeSwitch>

<ContentModeSwitch group="codex-surface" ids="app,cli,ide">

If you don't configure a subagent model or `model_reasoning_effort`, the
subagent inherits the parent agent's model and reasoning effort. If an explicit
spawn request or an `[agents]` default selects a model without an
explicit or configured reasoning effort, the subagent uses that model's default
reasoning effort. To balance intelligence, speed, and price for each task,
request a specific model or reasoning effort in your prompt,
configure `[agents]` defaults in `config.toml`, or set `model` and
`model_reasoning_effort` directly in the custom agent file.
For example, use `gpt-6-luna` for fast scans or a higher-effort `gpt-6.1-sol` configuration for more demanding reasoning.

For most tasks in Codex, start with 
  `gpt-6.1-sol` when your
  signed-in account or workspace has [access](<https://learn.chatgpt.com/docs/models#gpt-6.1-sol>).
  Otherwise, choose a model available to you. Use `gpt-6-luna` when you want a
  faster, lower-cost option for lighter subagent work.

### Model choice

- **`gpt-6.1-sol`**: Start here for demanding agents. Use it for ambiguous, multi-step work that needs planning, tool use, validation, and follow-through across a larger context.
- **`gpt-6-luna`**: Use for fast, narrowly scoped agents handling clear, repeatable, or high-volume work.

### Reasoning effort (`model_reasoning_effort`)

For GPT-6.1 Sol, use a reasoning effort supported by your client and selected
model. For explicit model settings, start with `high` for GPT-6 Luna or `low`
for GPT-6 Astra. Adjust for the task using a level the selected model supports.

- **`ultra`**: Use for the deepest reasoning when the selected model supports
  it.
- **`max`** and **`xhigh`**: Use for especially demanding reasoning when the
  selected model supports these levels.
- **`high`**: Use when an agent needs to trace complex logic, check assumptions, or work through edge cases (for example, reviewer or security-focused agents).
- **`medium`**: Balances speed and depth.
- **`low`**: Use when the task is straightforward and speed matters most.

Higher reasoning effort increases response time and token usage, but it can improve quality for complex work. For details, see [Models](<https://learn.chatgpt.com/docs/models>), [Config basics](<https://learn.chatgpt.com/docs/config-file/config-basic>), and [Configuration Reference](<https://learn.chatgpt.com/docs/config-file/config-reference>).

</ContentModeSwitch>

## Orchestration and thread controls

ChatGPT or Codex handles orchestration across agents, including spawning new
subagents, routing follow-up instructions, waiting for results, and closing
agent threads.

When many agents are running, Codex waits until all requested results are
available, then returns a consolidated response.

<ContentModeSwitch group="codex-surface" id="web">

At most intelligence levels, ChatGPT spawns agents after a direct request. With
Ultra, ChatGPT can also delegate proactively when parallel work is useful.

</ContentModeSwitch>

<ContentModeSwitch group="codex-surface" ids="app,cli,ide">

Current local Codex releases spawn agents after a direct request or applicable
project or skill instruction.

</ContentModeSwitch>

To see it in action, try the following prompt on your project:

```text
I would like to review the following points on the current PR (this branch vs main). Spawn one agent per point, wait for all of them, and summarize the result for each point.
1. Security issue
2. Code quality
3. Bugs
4. Race
5. Test flakiness
6. Maintainability of the code
```

## Managing subagents

<ContentModeSwitch group="codex-surface" id="web">

Open **Subagents** to see read-only **Active** and **Done** lists. Select a
completed subagent to inspect its details and result. The web sidebar reports
subagent activity; it doesn't provide controls to stop or steer an individual
subagent.

</ContentModeSwitch>

<ContentModeSwitch group="codex-surface" id="app">

- Open a subagent thread from the activity shown in the main thread to inspect
  its work.
- Ask Codex directly to steer a running subagent, stop it, or close completed
  subagent threads.



> Illustration: Codex desktop chat showing two subagents working in parallel.





> Illustration: Codex desktop Subagents panel with no active subagents and three completed audits.



</ContentModeSwitch>

<ContentModeSwitch group="codex-surface" id="cli">

- Use `/agent` in the CLI to switch between active agent threads and inspect the ongoing thread.
- Ask Codex directly to steer a running subagent, stop it, or close completed agent threads.

</ContentModeSwitch>

<ContentModeSwitch group="codex-surface" id="ide">

- When the background-agent panel is available, expand it to inspect status,
  stop active subagents, or open a subagent thread.
- Ask Codex directly to steer a running subagent, stop it, or close completed
  subagent threads.

</ContentModeSwitch>

## Approvals and sandbox controls

<ContentModeSwitch group="codex-surface" ids="app,cli,ide">

Subagents inherit your current sandbox policy.

</ContentModeSwitch>

<ContentModeSwitch group="codex-surface" id="web">

ChatGPT Work runs subagents in its hosted environment and doesn't expose a
local Codex sandbox or approval-mode control. Subagents use the tools available
to the parent chat. Website and connector permissions remain
tool-specific.

</ContentModeSwitch>

<ContentModeSwitch group="codex-surface" id="app">

Subagents inherit the permission mode selected beneath the composer. Choose the
permission mode for the parent turn before you ask Codex to delegate work.

</ContentModeSwitch>

<ContentModeSwitch group="codex-surface" id="cli">

In interactive CLI sessions, approval requests can surface from inactive agent
threads even while you are looking at the main thread. The approval overlay
shows the source thread label, and you can press `o` to open that thread before
you approve, reject, or answer the request.

In non-interactive flows, or whenever a run can't surface a fresh approval, an
action that needs new approval fails and Codex surfaces the error back to the
parent workflow.

Codex also reapplies the parent turn's live runtime overrides when it spawns a
child. That includes sandbox and approval choices you set interactively during
the session, such as `/permissions` changes or `--yolo`, even if the selected
custom agent file sets different defaults.

</ContentModeSwitch>

<ContentModeSwitch group="codex-surface" id="ide">

Subagents inherit the permission mode selected beneath the composer. Choose
the permission mode for the parent turn before you ask Codex to delegate work.

</ContentModeSwitch>

<ContentModeSwitch group="codex-surface" ids="app,cli,ide">

You can also override the sandbox configuration for individual [custom agents](<#custom-agents>), such as explicitly marking one to work in read-only mode.

## Custom agents

Codex ships with built-in agents:

- `default`: general-purpose fallback agent.
- `worker`: execution-focused agent for implementation and fixes.
- `explorer`: read-heavy codebase exploration agent.

To define your own custom agents, add standalone TOML files under
`~/.codex/agents/` for personal agents or `.codex/agents/` for project-scoped
agents.

Each file defines one custom agent. Codex loads these files as configuration
layers for spawned sessions, so custom agents can override the same settings as
a normal Codex session config. That can feel heavier than a dedicated agent
manifest, and the format may evolve as authoring and sharing mature.

Every standalone custom agent file must define:

- `name`
- `description`
- `developer_instructions`

If a custom agent file sets `model` or `model_reasoning_effort`, the value in
the file takes precedence. Before applying the file, Codex resolves each setting
from an explicit spawn value, then the corresponding `[agents]` default, then
the parent's value. If an explicit spawn request or an `[agents]` default
selects a model and neither supplies a reasoning effort, Codex uses
that model's default effort. A custom agent file that sets only `model`
preserves this previously resolved effort. Set `model_reasoning_effort` in the
file too if the selected model doesn't support that effort or you want a
different one. Other session settings, such as `sandbox_mode`, `mcp_servers`,
and `skills.config`, inherit from the parent when the custom agent file omits
them.

### Global settings

Global subagent settings still live under `[agents]` in your [configuration](<https://learn.chatgpt.com/docs/config-file/config-basic#configuration-precedence>).

| Field                                       | Type    | Required | Purpose                                                             |
| ------------------------------------------- | ------- | :------: | ------------------------------------------------------------------- |
| `agents.enabled`                            | boolean |    No    | Enable or disable multi-agent tools.                                |
| `agents.max_concurrent_threads_per_session` | number  |    No    | Cap concurrently open spawned-agent threads, excluding the primary. |
| `agents.default_subagent_model`             | string  |    No    | Set the default model for spawned agents.                           |
| `agents.default_subagent_reasoning_effort`  | string  |    No    | Set the default reasoning effort for spawned agents.                |
| `agents.interrupt_message`                  | boolean |    No    | Record a model-visible message when an agent turn is interrupted.   |

**Notes:**

- `agents.enabled` defaults to `true`. Set it to `false` to disable multi-agent tools.
- When you leave `agents.max_concurrent_threads_per_session` unset, Codex chooses the default. Existing configurations can keep using `agents.max_threads` as a legacy alias.
- Explicit spawn values override `agents.default_subagent_model` and `agents.default_subagent_reasoning_effort`.
- `agents.interrupt_message` defaults to `true`. Set it to `false` to omit the model-visible interruption message from the agent's context.
- If a custom agent name matches a built-in agent such as `explorer`, your custom agent takes precedence.

### Custom agent file schema

| Field                    | Type   | Required | Purpose                                                         |
| ------------------------ | ------ | :------: | --------------------------------------------------------------- |
| `name`                   | string |   Yes    | Agent name Codex uses when spawning or referring to this agent. |
| `description`            | string |   Yes    | Human-facing guidance for when Codex should use this agent.     |
| `developer_instructions` | string |   Yes    | Core instructions that define the agent's behavior.             |

You can also include other supported `config.toml` keys in a custom agent file, such as `model`, `model_reasoning_effort`, `sandbox_mode`, `mcp_servers`, and `skills.config`.

Codex identifies the custom agent by its `name` field. Matching the filename to
the agent name is the simplest convention, but the `name` field is the source
of truth.

### Example custom agents

The best custom agents are narrow and opinionated. Give each one clear job, a
tool surface that matches that job, and instructions that keep it from
drifting into adjacent work.

The examples use [GPT-6.1 Sol](<https://learn.chatgpt.com/docs/models#gpt-6.1-sol>) where your signed-in
account or workspace has access. If it isn't available, choose a model you can
use.

#### Example 1: PR review

This pattern splits review across three focused custom agents:

- `pr_explorer` maps the codebase and gathers evidence.
- `reviewer` looks for correctness, security, and test risks.
- `docs_researcher` checks framework or API documentation through a dedicated MCP server.

Project config (`.codex/config.toml`):

```toml
[agents]
max_concurrent_threads_per_session = 8
```

`.codex/agents/pr-explorer.toml`:

```toml
name = "pr_explorer"
description = "Read-only codebase explorer for gathering evidence before changes are proposed."
model = "gpt-6-luna"
model_reasoning_effort = "high"
sandbox_mode = "read-only"
developer_instructions = """
Stay in exploration mode.
Trace the real execution path, cite files and symbols, and avoid proposing fixes unless the parent agent asks for them.
Prefer fast search and targeted file reads over broad scans.
"""
```

`.codex/agents/reviewer.toml`:

```toml
name = "reviewer"
description = "PR reviewer focused on correctness, security, and missing tests."
model = "gpt-6.1-sol"
model_reasoning_effort = "medium"
sandbox_mode = "read-only"
developer_instructions = """
Review code like an owner.
Prioritize correctness, security, behavior regressions, and missing test coverage.
Lead with concrete findings, include reproduction steps when possible, and avoid style-only comments unless they hide a real bug.
"""
```


`.codex/agents/docs-researcher.toml`:

```toml
name = "docs_researcher"
description = "Documentation specialist that uses the docs MCP server to verify APIs and framework behavior."
model = "gpt-6-luna"
model_reasoning_effort = "high"
sandbox_mode = "read-only"
developer_instructions = """
Use the docs MCP server to confirm APIs, options, and version-specific behavior.
Return concise answers with links or exact references when available.
Do not make code changes.
"""

[mcp_servers.openaiDeveloperDocs]
url = "https://developers.openai.com/mcp"
```

This setup works well for prompts like:

```text
Review this branch against main. Have pr_explorer map the affected code paths, reviewer find real risks, and docs_researcher verify the framework APIs that the patch relies on.
```

#### Example 2: Frontend integration debugging

This pattern is useful for UI regressions, flaky browser flows, or integration bugs that cross application code and the running product.

Project config (`.codex/config.toml`):

```toml
[agents]
max_concurrent_threads_per_session = 6
```

`.codex/agents/code-mapper.toml`:

```toml
name = "code_mapper"
description = "Read-only codebase explorer for locating the relevant frontend and backend code paths."
model = "gpt-6-luna"
model_reasoning_effort = "high"
sandbox_mode = "read-only"
developer_instructions = """
Map the code that owns the failing UI flow.
Identify entry points, state transitions, and likely files before the worker starts editing.
"""
```

`.codex/agents/browser-debugger.toml`:

```toml
name = "browser_debugger"
description = "UI debugger that uses browser tooling to reproduce issues and capture evidence."
model = "gpt-6.1-sol"
model_reasoning_effort = "medium"
sandbox_mode = "workspace-write"
developer_instructions = """
Reproduce the issue in the browser, capture exact steps, and report what the UI actually does.
Use browser tooling for screenshots, console output, and network evidence.
Do not edit application code.
"""
[mcp_servers.chrome_devtools]
url = "http://localhost:3000/mcp"
startup_timeout_sec = 20
```


`.codex/agents/ui-fixer.toml`:

```toml
name = "ui_fixer"
description = "Implementation-focused agent for small, targeted fixes after the issue is understood."
model = "gpt-6-luna"
model_reasoning_effort = "high"
developer_instructions = """
Own the fix once the issue is reproduced.
Make the smallest defensible change, keep unrelated files untouched, and validate only the behavior you changed.
"""

[[skills.config]]
path = "/Users/me/.agents/skills/docs-editor/SKILL.md"
enabled = false
```

This setup works well for prompts like:

```text
Investigate why the settings modal fails to save. Have browser_debugger reproduce it, code_mapper trace the responsible code path, and ui_fixer implement the smallest fix once the failure mode is clear.
```

</ContentModeSwitch>


</details>
