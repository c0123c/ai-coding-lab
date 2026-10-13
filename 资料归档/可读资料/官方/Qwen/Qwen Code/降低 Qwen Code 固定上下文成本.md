# 降低 Qwen Code 固定上下文成本

解释系统提示、工具定义、项目指令和技能目录的重复消耗，介绍测量、缩减工具、迁移场景说明及验证节省效果。

- 来源：Qwen／Qwen Code
- 资料类型：存档文档
- 存档版本：`7e3de59d3aba`。
- 文档时间：源码提交（存档版）：[2026-10-06](<https://github.com/QwenLM/qwen-code/commit/8e3f3923b8c85d9c59e755b1846b4d87949d00dd>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/QwenLM/qwen-code/blob/main/docs/users/features/context-cost.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Qwen/Qwen Code/docs/users/features/降低 Qwen Code 固定上下文成本.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 居民环境成本

会话发送的每个请求在您的任何对话之前都带有相同的前缀：系统提示、每个声明的工具的架构、您的上下文 (`QWEN.md`) 文件和技能列表。您在每个回合 ** 上为 ** 支付该前缀费用，包括仅回答问题的回合。本页是关于测量和切割它的。

[令牌缓存](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/token-caching.md>)减少了前缀的_价格_。本页减少了_前缀_。它们组成——较小的前缀缓存起来也更便宜。

## 查看您所支付的费用

```
/context detail
```

`/context`按类别打印明细； `detail` 添加每项行 - 每个内置工具、每个 MCP 工具、每个上下文文件、每个列出的技能 - 这样您就可以看到哪个条目是昂贵的。在会话的 **第一轮 ** 上阅读它，其中对话仍然是空的，你看到的所有内容都是前缀。

这些类别是`/context`报告的内容加上两个簿记行：`startupContext`（第一个用户轮流发送的环境块）以及类别未归因的任何显式残差，因此这些部分总是加起来等于总数。

## 使用闲置成本，而不是窗口百分比

上下文窗口的百分比不是您可以持有的目标，因为分母是任意的。相同的配置在 1M 上下文模型上读取为 6.5%，在 128k 模型上读取为 37% — 相同的文本、相同的成本、截然不同的数字。改用：

> **空闲成本** — 提出一个问题且不调用任何工具的会话的输入令牌。

它独立于模型和窗口，并且您无法通过将文本从工具架构移动到上下文文件来使其看起来更好。第二个有用的读物​​是**对话需要多少轮才能超过前缀**：需要20轮次摊销的前缀在5轮次会话中永远不会摊销。

## 杠杆，按返回顺序

### 1。关闭您不使用的功能

向模型声明的每个工具都会将其架构添加到每个请求中。因此，使用大型常驻工具关闭可选功能可以节省请求令牌。相反，延迟工具会贡献较短的目录条目以及稍后发现或调用的成本。禁用某个功能还会从子代理中删除其工具，而下一个控制杆并不总是能做到这一点。

### 2。保持急切的工具表面符合您实际使用的内容

`tools.eager` 是内置工具的白名单，其架构保留在初始请求中。其他一切都变成 ** 延迟 **：仍然注册，仍然在 `/tools` 中列出，仍然可调用 - 当模型需要它时，通过 `tool_search` → `tool_call` 桥到达它。

根据正确的基线衡量许可名单。 _ 自己的 _ 默认所需的工具在第一个请求中已经不存在，并且自从 `tools.toolSearch.threshold` 开始默认为 `0` 后，没有任何东西会预加载它们，因此允许列表保存的只是它保留的默认急切工具的模式，而不是整个内置表面。在将数字添加到您的列表之前，重新阅读一下您实际运行的版本 `/context`。

```jsonc
{
  "tools": {
    "eager": [
      "read_file",
      "write_file",
      "edit",
      "glob",
      "grep_search",
      "run_shell_command",
      "skill",
    ],
  },
}
```

使用前需要了解四件事：

- **这不是禁用。**降级的工具仍然可以访问。如果您打算删除工具，请使用整体工具 `permissions.deny` 规则或 `tools.disabled`。
- **某些工具是豁免的**，并保持其正常的加载行为，无论列表如何显示：`tool_search`和`tool_call`（到达保留工具的桥的两半），`structured_output`，计划模式生命周期工具（`enter_plan_mode`， `exit_plan_mode`、`ask_user_question`）、`task_stop`、MCP 工具（`mcp__*`）和计算机使用工具（`computer_use__*`）。 `task_stop` 和计算机使用系列无论如何都是默认按需使用的，因此对它们进行门禁不会节省任何内容； MCP 工具由 `tools.toolSearch.*` 和每服务器 `includeTools` / `excludeTools` 过滤器管理； `tools.eager` 中的任何内容都不能删除其余部分之一：需要整个工具 `permissions.deny` 规则、`tools.disabled` 条目或 `--exclude-tools` — 对于桥接对，`tools.toolSearch.enabled: false` 会立即删除两半。删除半个桥并不是摆脱允许列表的中立方式：每个普通的延迟工具（所有 MCP 工具，延迟的计算机使用系列）都会被强制声明到每个请求中，而保留的工具不会提供给模型，并且无法通过桥加载回 - 它保持注册状态，因此按名称直接调用仍然会经过正常批准。
- **`permissions.allow` 不保存任何内容。** 这是纯粹的自动批准：它从不降级、隐藏或删除工具。审批模式也不行。
- **需要桥的两半。**通过`tool_search`审核保留工具并通过`tool_call`调用； `tool_search` 单独可以读取它无法调用的模式。如果其中一个未注册 — `tools.toolSearch.enabled: false` 均否认； `tool_search`/`tool_call` 拒绝规则、`--exclude-tools` 条目或 `tools.disabled` 条目会删除一个 — 允许列表仍然保留架构，没有任何内容可以加载它们，并且不会向该会话的模型提供降级工具（记录警告）；他们保持注册状态，因此直接点名仍需经过正常批准。在这种情况下，普通的延迟工具会回退到急切地声明；您使用 `tools.eager` 降级的工具则不会。

`tools.visible` 是您想要预先声明的一种工具的逃生口，即使它在默认情况下被推迟。

默认情况下，Agent和目标协调（`agent`、`list_agents`、`get_goal`、`update_goal`和`propose_goal`）被推迟；不需要`tools.eager`配置。该模型看到的是简短的发现条目而不是完整的模式。它们的首次使用需要通过桥来发现，因此请比较整个任务成本和成功 delegation/Goal 完成以及第一个请求。这些是普通的延迟工具：`tools.visible`、预加载和上述的不完整桥急切回退仍然适用。预加载对于整个延迟候选池来说是全有或全无的，并且这五个声明很大，因此过去在会话开始时显示每个延迟工具的 `tools.toolSearch.threshold` 现在无法显示其中任何一个。更改任一值后重新读取 `/context` 读数。

### 3。将情景指导从上下文文件移至技能中

上下文文件被连接到它所应用的每个会话的每个请求中，没有相关性门控。一个 [技能](<在 Qwen Code 中创建与管理 Skills.md>) 仅按名称和描述列出 - 在一个测量样本中， 84 技能平均约为 55 每个令牌 - 并在调用时加载其主体和技能 [门控 `paths:`](<在 Qwen Code 中创建与管理 Skills.md#可选在文件路径上设置技能-paths>) 在触及匹配文件之前甚至不会列出。

仅在上下文文件中保留始终正确的内容——身份、词汇、硬约束——并将“当做 X 时，做 Y”放入技能或 [`paths:` 门控规则 ](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/rules.md>) 中。 `/context detail` 命名每个上下文文件，对于 [ 扩展的 ](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/extension/getting-started-extensions.md>) 文件，它命名拥有它的扩展。

### 4。系统提示，最后

基本提示已经是最小的常驻类别，其中大约三分之一是不得编辑的安全和许可文本。其门控工具指导遵循声明的集合，但桥可到达的除外 Agent；这些条目命名的其他工具仍然需要声明。因此，修剪工具表面也可以缩小该指导。将其批量替换为 `--system-prompt` 是可能的，并且是此页面上风险最高的更改；如果这样做，请在每次升级时比较上游提示。

## 陷阱

- **子代理也获取延迟工具。** 未声明显式工具列表的子代理会接收每个已注册工具的架构（包括延迟工具），并且不会经过 ToolSearch。 `tools.eager` 和 `permissions.deny` 是唯一可以到达它的旋钮；预载阈值则不然。
- **后台内存agents需要完整的工具列表，并且两个列表不同。**项目Dreamworker需要六个工具（`read_file`，`grep_search`，`glob`，`run_shell_command`， `write_file`、`edit`);自动提取需要五个——同一组没有`run_shell_command`，它在设计上拒绝了这一点。拒绝一个工具 agent 实际上会默默地降低它的性能，而不是出错。所以不要在全球范围内否认`run_shell_command`只是为了放弃它的宣言：梦想仍然需要它。
- **令牌可以移动而不是消失。**拿走`grep_search`和`glob`，模型可以通过shell到达`grep`和`find`，其输出落在对话中。新输出在首次发送时添加输入令牌；包含它的未更改历史记录可能会在以后的请求中命中提供者的前缀缓存。通过每个任务的总输入令牌、提供者报告的缓存和未缓存的输入以及实际账单来判断更改，而不是仅通过前缀。
- **恢复的会话重新发送他们需要的内容。**出现在恢复的会话历史记录中的降级工具会自动恢复其架构；被拒绝的工具则不会。
- **到达被保留的工具不再重写前缀 - 但它曾经这样做，并且旧的建议假设它会这样做。**发现通过`tool_search`→`tool_call`桥，这使得声明的工具列表字节稳定，因此提示缓存前缀在会话中期发现中幸存；成本是扣留工具第一次使用之前的一次额外往返。这就是为什么 `tools.toolSearch.threshold` 现在默认为 `0`：每回合携带延迟集不再是交易中更便宜的一面。提高阈值只是为了在肯定需要它们的会话中回购 _ 普通 _ 延迟工具（MCP 工具和按需内置工具）的往返 — 它永远不会预加载您使用 `tools.eager` 降级的工具；那些人在任何门槛处都留在桥后面。预加载也在会话启动时运行，并且 MCP 服务器通常在其之后连接，因此无论阈值如何，新的启动都会声明没有 MCP 工具；它们从下一个会话开始时达到预加载（在 CLI 中，在 `/clear` 之后），或立即在 `QWEN_CODE_LEGACY_MCP_BLOCKING=1` 下。
- **用于反转此交易的前缀缓存模型；对于延迟，他们不再这样做。** 对于折扣取决于未更改前缀的模型，保持声明列表相同比保持较小更重要 - 这就是为什么一些部署手动禁用 ToolSearch。现在，到达保留工具会使该列表保持字节稳定，因此该特定原因已过期 - 但有一个例外，在 `tool_search` 描述本身中说明：当实时历史记录包含对保留工具的直接调用时，工具集刷新仍可能重新声明保留工具，并且这确实会移动前缀。前缀稳定性仍然反对任何其他在会话中重写前缀的方法，例如在轮次之间编辑上下文文件。
- **范围泄漏。**设置适用于读取它们的每个客户端（CLI、Web Shell、服务），因此每个部署工具表面需要自己的设置范围。

## 验证保存情况

1. 请注意更改前的闲置成本：新的会话，一个简单的问题，第一回合的`/context`。
2. 一次应用一个控制杆并重复，重新启动会话 - 大多数设置都会在启动时读取。
3. 根据您自己的任务集确认该功能仍然存在：工具调用成功率、必须调用 `tool_search` 的频率以及任务结果。模型从未想过要寻找的降级工具不会严重失败；它只是停止使用。
4. 检查账单，而不仅仅是前缀——查看有关令牌进入对话的陷阱。

## 另请参阅

- [令牌缓存](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/token-caching.md>) — 缓存对剩余价格的影响。
- [规则](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/rules.md>) - `paths:`-条件上下文，包括扩展可能贡献的内容。
- [技能](<在 Qwen Code 中创建与管理 Skills.md>) - 渐进式披露，以及`paths:`门控。
- [设置参考](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/configuration/settings.md>) — `tools.eager`、`tools.visible`、`tools.disabled`、`tools.toolSearch.*`、`permissions.deny`的确切语义。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Resident Context Cost

Every request a session sends carries the same prefix before any of your conversation: the system prompt, the schema of every declared tool, your context (`QWEN.md`) files, and the skill listing. You pay for that prefix on **every turn**, including the turns that just answer a question. This page is about measuring it and cutting it.

[Token caching](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/token-caching.md>) reduces the _price_ of the prefix. This page reduces the _prefix_. They compose — a smaller prefix is cheaper cached too.

## See what you are paying for

```
/context detail
```

`/context` prints the breakdown by category; `detail` adds per-item rows — each built-in tool, each MCP tool, each context file, each listed skill — so you can see which single entry is the expensive one. Read it on the **first turn** of a session, where the conversation is still empty and everything you see is prefix.

The categories are what `/context` reports plus two bookkeeping rows: `startupContext` (the environment block sent as the first user turn) and an explicit residual for whatever the categories do not attribute, so the parts always add up to the total.

## Use idle cost, not percent of window

A percentage of the context window is not a target you can hold, because the denominator is arbitrary. The same configuration reads as 6.5% on a 1M-context model and 37% on a 128k one — identical text, identical cost, wildly different number. Use instead:

> **Idle cost** — the input tokens of a session that asks one question and calls no tool.

It is independent of the model and the window, and you cannot make it look better by moving text from a tool schema into a context file. A second useful reading is **how many turns it takes for the conversation to outgrow the prefix**: a prefix that takes 20 turns to amortise is never amortised in a 5-turn session.

## The levers, in order of return

### 1. Turn off features you do not use

Each tool declared to the model adds its schema to every request. Switching off optional features with large resident tools can therefore save request tokens. Deferred tools instead contribute short catalog entries and the cost of later discovery or invocation. Disabling a feature also removes its tools from subagents, which the next lever does not always do.

### 2. Keep the eager tool surface to what you actually use

`tools.eager` is an allowlist of built-in tools whose schemas stay in the initial request. Everything else becomes **deferred**: still registered, still listed in `/tools`, still callable — the model reaches it through the `tool_search` → `tool_call` bridge when it turns out to need it.

Measure the allowlist against the right baseline. Tools that are on demand by _their own_ default are already absent from the first request, and since `tools.toolSearch.threshold` began defaulting to `0` nothing preloads them back, so an allowlist's saving is only the schemas of the eager-by-default tools it withholds — not the whole built-in surface. Take a fresh `/context` reading on the version you actually run before attributing a number to your list.

```jsonc
{
  "tools": {
    "eager": [
      "read_file",
      "write_file",
      "edit",
      "glob",
      "grep_search",
      "run_shell_command",
      "skill",
    ],
  },
}
```

Four things to know before you use it:

- **It is not a disable.** A demoted tool stays reachable. If you meant to remove a tool, use a whole-tool `permissions.deny` rule or `tools.disabled`.
- **Some tools are exempt** and keep their normal loading behaviour whatever the list says: `tool_search` and `tool_call` (the two halves of the bridge that reaches a withheld tool), `structured_output`, the plan-mode lifecycle tools (`enter_plan_mode`, `exit_plan_mode`, `ask_user_question`), `task_stop`, MCP tools (`mcp__*`), and Computer Use tools (`computer_use__*`). `task_stop` and the Computer Use family are on demand by default anyway, so gating them would save nothing; MCP tools are governed by `tools.toolSearch.*` and the per-server `includeTools` / `excludeTools` filters; and nothing in `tools.eager` can drop one of the rest: that takes a whole-tool `permissions.deny` rule, a `tools.disabled` entry, or `--exclude-tools` — and for the bridge pair, `tools.toolSearch.enabled: false` removes both halves at once. Removing a bridge half is not a neutral way out of the allowlist: every ordinary deferred tool (all MCP tools, the deferred Computer Use family) is then force-declared into every request, while a withheld tool is not offered to the model and cannot be loaded back through the bridge — it stays registered, so a direct call by name still goes through normal approval.
- **`permissions.allow` saves nothing.** It is pure auto-approval: it never demotes, hides, or removes a tool. Neither do approval modes.
- **It needs both halves of the bridge.** A withheld tool is reviewed with `tool_search` and invoked through `tool_call`; `tool_search` alone can read a schema it cannot call. If either is unregistered — `tools.toolSearch.enabled: false` denies both; a `tool_search`/`tool_call` deny rule, an `--exclude-tools` entry, or a `tools.disabled` entry removes one — the allowlist still withholds the schemas, nothing can load them back, and the demoted tools are not offered to the model for that session (a warning is logged); they stay registered, so a direct call by name still goes through normal approval. Ordinary deferred tools fall back to being declared eagerly in that case; tools you demoted with `tools.eager` do not.

`tools.visible` is the escape hatch for one tool you want declared up front even though it is deferred by default.

Agent and Goal coordination (`agent`, `list_agents`, `get_goal`, `update_goal`, and `propose_goal`) is deferred by default; no `tools.eager` configuration is needed. The model sees short discovery entries instead of the full schemas. Their first use needs discovery through the bridge, so compare whole-task cost and successful delegation/Goal completion as well as the first request. These are ordinary deferred tools: `tools.visible`, preloading, and the incomplete-bridge eager fallback described above still apply. Preloading is all-or-nothing over the whole deferred candidate pool, and these five declarations are large, so a `tools.toolSearch.threshold` that used to reveal every deferred tool at session start can now reveal none of them. Re-take a `/context` reading after changing either one.

### 3. Move scenario guidance out of context files into skills

A context file is concatenated into every request of every session it applies to, with no relevance gating. A [skill](<在 Qwen Code 中创建与管理 Skills.md>) is listed by its name and description only — in one measured sample, 84 skills averaged about 55 tokens each — and loads its body when invoked, and a skill [gated on `paths:`](<在 Qwen Code 中创建与管理 Skills.md#optional-gate-a-skill-on-file-paths-paths>) is not even listed until a matching file is touched.

Keep in a context file only what is always true — identity, vocabulary, a hard constraint — and put "when doing X, do Y" in a skill or a [`paths:`-gated rule](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/rules.md>). `/context detail` names each context file, and for an [extension's](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/extension/getting-started-extensions.md>) file it names the extension that owns it.

### 4. The system prompt, last

The base prompt is already the smallest of the resident categories, and roughly a third of it is safety and permission text that must not be edited. Its gated tool guidance follows the declared set, with an exception for bridge-reachable Agent; other tools named by those entries still need declarations. Trimming your tool surface can therefore shrink that guidance as well. Replacing it wholesale with `--system-prompt` is possible and is the highest-risk change on this page; if you do, diff the upstream prompt on every upgrade.

## Traps

- **Subagents get the deferred tools too.** A subagent that does not declare an explicit tool list receives every registered tool's schema, deferred ones included, and does not go through ToolSearch. `tools.eager` and `permissions.deny` are the only knobs that reach it; the preload threshold does not.
- **The background memory agents need their full tool lists, and the two lists differ.** The project Dream worker needs six tools (`read_file`, `grep_search`, `glob`, `run_shell_command`, `write_file`, `edit`); automatic extraction needs five — the same set without `run_shell_command`, which it denies by design. Denying a tool either agent actually holds degrades it silently rather than erroring. So do not deny `run_shell_command` globally just to drop its declaration: Dream still needs it.
- **Tokens can move rather than disappear.** Take away `grep_search` and `glob` and the model may reach for `grep` and `find` through the shell, whose output lands in the conversation. New output adds input tokens when first sent; unchanged history containing it may hit the provider's prefix cache on later requests. Judge a change by total input tokens per task, provider-reported cached and uncached input, and the actual bill, not by the prefix alone.
- **Resumed sessions re-send what they need.** A demoted tool that appears in a resumed session's history gets its schema back automatically; a denied tool does not.
- **Reaching a withheld tool no longer rewrites the prefix — but it used to, and old advice assumes it does.** Discovery goes through the `tool_search` → `tool_call` bridge, which leaves the declared tool list byte-stable, so the prompt-cache prefix survives a mid-session discovery; the cost is one extra round trip before a withheld tool's first use. That is why `tools.toolSearch.threshold` now defaults to `0`: carrying the deferred set every turn is no longer the cheaper side of the trade. Raise the threshold only to buy back that round trip for _ordinary_ deferred tools (MCP tools and the on-demand built-ins) in a session that will certainly need them — it never preloads a tool you demoted with `tools.eager`; those stay behind the bridge at any threshold. The preload also runs at session start, and MCP servers usually connect after it, so a fresh launch declares no MCP tools whatever the threshold; they reach the preload from the next session start (in the CLI, after `/clear`), or immediately under `QWEN_CODE_LEGACY_MCP_BLOCKING=1`.
- **Prefix-caching models used to invert this trade; for deferral they no longer do.** For a model whose discount depends on an unchanged prefix, keeping the declaration list identical once mattered more than keeping it small — which is why some deployments disabled ToolSearch by hand. Reaching a withheld tool now leaves that list byte-stable, so that particular reason has expired — with one exception, stated in the `tool_search` description itself: a tool-set refresh may still re-declare a withheld tool when the live history contains a direct call to it, and that does move the prefix. Prefix stability still argues against anything else that rewrites the prefix mid-session, such as editing a context file between turns.
- **Scope leaks.** Settings apply to every client that reads them (CLI, Web Shell, serve), so a per-deployment tool surface needs its own settings scope.

## Verify the saving

1. Note idle cost before the change: a fresh session, one trivial question, `/context` on the first turn.
2. Apply one lever at a time and repeat, restarting the session — most of these settings are read at startup.
3. Confirm that capability survived, on your own task set: tool-call success rate, how often `tool_search` has to be called, and task outcomes. A demoted tool that the model never thinks to look for does not fail loudly; it just stops being used.
4. Check the bill, not only the prefix — see the trap about tokens moving into the conversation.

## See also

- [Token Caching](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/token-caching.md>) — what caching does to the price of what remains.
- [Rules](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/rules.md>) — `paths:`-conditional context, including what an extension may contribute.
- [Skills](<在 Qwen Code 中创建与管理 Skills.md>) — progressive disclosure, and `paths:` gating.
- [Settings reference](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/configuration/settings.md>) — the exact semantics of `tools.eager`, `tools.visible`, `tools.disabled`, `tools.toolSearch.*`, `permissions.deny`.


</details>
