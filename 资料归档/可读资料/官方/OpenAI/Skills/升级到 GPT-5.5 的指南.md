# 升级到 GPT-5.5 的指南

说明先核实目标版本，再检查接口兼容、模型名、提示修改和验证；适用于指定版本迁移。

- 来源：OpenAI／Skills
- 资料类型：存档文档
- 存档版本：`49f948faa925`。
- 文档时间：源码提交（存档版）：[2026-04-24](<https://github.com/openai/skills/commit/724cd511c96593f642bddf13187217aa155d2554>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/skills/blob/main/skills/.system/openai-docs/references/upgrade-guide.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Skills/升级到 GPT-5.5 的指南.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 升级至GPT-5.5

当用户明确要求将现有集成升级到 GPT-5.5 时，请使用本指南。将其与当前的 OpenAI 文档查找配对。默认目标字符串是`gpt-5.5`。

## 新鲜度检查

在应用此捆绑指南进行 latest/current/default 模型升级之前，请从 OpenAI 文档技能目录运行 `node scripts/resolve-latest-model-info.js`。

- 如果命令返回 `modelSlug: "gpt-5p5"`，请继续使用本捆绑指南，并在需要提示更新时使用 `references/prompting-guide.md`。
- 如果该命令返回不同的 `modelSlug`，则获取返回的 `migrationGuideUrl` 和 `promptingGuideUrl` 并将它们用作当前的事实来源，而不是捆绑的引用。
- 如果命令失败、元数据丢失或无法获取任一远程指南，请继续使用捆绑后备引用并说明远程新鲜度检查不可用。
- 如果用户明确命名了目标模型，请保留该目标并仅使用当前文档来检查兼容性或警告。

## 升级姿势

使用最窄的安全更改集进行升级：

- 首先替换模型字符串
- 仅更新与该模型使用直接相关的提示
- 不会自动升级可能有意固定的较旧或不明确的模型用法，例如历史文档、示例、测试、评估基线、比较代码或低成本 fallback/routing 路径。除非用户明确要求升级所有模型使用情况，否则保持这些站点不变并将它们列为需要确认的
- 如果可能，更喜欢仅提示升级
- 如果升级需要 API- 表面更改、参数重写、工具重新布线、提供程序迁移或更广泛的代码编辑，请将其标记为已阻止，而不是扩大范围

## 升级工作流程

1. 盘点当前模型的使用情况。
   - 搜索模型字符串、客户端调用和提示文件。
   - 包括内联提示、提示模板、YAML 或 JSON 配置、Markdown 文档以及已保存的提示（当它们明显与模型使用站点相关时）。
2. 将每个模型的用法与其提示界面配对。
   - 首先首选最接近的提示表面：内联系统或开发人员文本，然后是相邻的提示文件，然后是共享模板。
   - 如果您无法自信地将提示与模型用法联系起来，请直接说出来，而不是猜测。
3. 对源模型系列进行分类。
   - 常见桶：GPT-5.4、GPT-5.3-Codex或GPT-5.2-Codex、早期的GPT-5.x、GPT-4o或GPT-4.1、推理模型如o1或o3或o4-mini，第三方模型，或混杂不清。
4. 决定升级等级。
   - `model string only`
   - `model string + light prompt rewrite`
   - `blocked without code changes`
5. 运行兼容性门。
   - 检查当前集成是否可以接受 `gpt-5.5`，而无需 API- 表面更改或实现更改。
   - 检查结构化输出、工具模式、函数名称和下游解析器是否可以保持不变。
   - 对于长时间运行的响应或需要大量工具的 agents，请检查当主机重播辅助项或使用前导码时 `phase` 是否已保留或往返。
   - 如果兼容性取决于代码更改，则返回 `blocked`。
   - 如果兼容性不清楚，请返回`unknown`而不是即兴发挥。
6. 在范围内应用升级。
   - 默认替换字符串：`gpt-5.5`。
   - 保持小干预并保留行为。
   - 从当前的推理工作可见时开始，除非有明确的理由要改变它。
   - 对于范围内的更改，请更新模型字符串和直接相关的提示。
   - 对于被阻止或未知的更改，请勿编辑；报告阻碍因素或不确定性。
7. 总结一下结果。
   - `Current model usage`
   - `Model-string updates`
   - `Reasoning-effort handling`
   - `Prompt updates`
   - `Structured output and formatting assessment`
   - `Tool-use assessment` 当流程使用工具、检索或终端操作时
   - `Phase assessment` 当流程长时间运行、重放或需要大量工具时
   - `Compatibility check`
   - `Validation performed`

输出规则：

- 对于每个使用站点，说明起始推理努力建议。
- 如果存储库公开了当前的推理设置，建议首先保留它，除非当前的 OpenAI 文档另有说明。
- 如果存储库未公开当前设置，建议不要添加设置，除非当前的 OpenAI 文档需要它。

## 升级成果

### `model string only`

在以下情况下选择此选项：

- 源模型是GPT-5.4
- 现有的提示已经简短、明确且受任务限制
- 工作流程不依赖于严格的输出格式、工具调用行为、批处理完整性或应在升级后验证的长期执行
- 没有明显的兼容性障碍

默认操作：

- 将型号字符串替换为 `gpt-5.5`
- 保留当前的推理工作
- 保持提示不变
- 使用现有测试、实际抽查或现有评估套件（当已有可用时）来验证行为

### `model string + light prompt rewrite`

在以下情况下选择此选项：

- 该任务需要更强的完整性、引用规则、验证或依赖性处理
- 升级后的模型变得过于冗长、过于密集或难以扫描，除非格式受到限制
- 工作流程具有严格的输出形状要求，并且缺乏明确的格式约定、模式或解析器验证
- 工作流程研究繁重，需要对稀疏或空的检索结果进行更强的处理
- 工作流程是面向编码、基于终端、重工具或多agent，但现有的API曲面和工具定义可以保持不变

默认操作：

- 将型号字符串替换为 `gpt-5.5`
- 保留第一遍当前的推理工作
- 仅针对观察到的工作流程风险进行所需的最小提示编辑
- 阅读 [GPT-5.5 提示指南 ](<https://github.com/api/docs/guides/prompt-guidance?model=gpt-5.5>) 选择恢复或改善行为的最小提示更改
- 避免与升级无关的广泛提示清理
- 对于研究工作流程，添加引用规则、检索预算、缺失证据行为以及提示指南中的验证指南
- 对于依赖性感知或工具密集型工作流程，添加先决条件检查、缺失上下文处理、明确的工具预算、停止条件和验证指南
- 对于编码或终端工作流程，添加特定于存储库的约束、验收标准和具体验证命令
- 对于多 agent 支持或分类工作流程，添加任务所有权、移交、完整性和停止条件
- 对于带有前导码或多个辅助消息的长时间运行的响应 agents，明确检查 `phase` 是否已被处理；如果添加或保留 `phase` 需要编辑代码，请将路径标记为 `blocked`
- 不要仅仅因为可见片段最少而将编码或使用工具的响应工作流程分类为 `blocked`；更喜欢 `model string + light prompt rewrite` 除非存储库明确表明安全的 GPT-5.5 路径需要主机端代码更改

### `blocked`

在以下情况下选择此选项：

- 升级似乎需要 API- 表面更改
- 升级似乎需要参数重写或推理设置更改，这些更改不会暴露在实现代码之外
- 升级需要更改工具定义、工具处理程序连接或模式合同
- 用户要求工具、IDE、插件、shell 或环境迁移，而不是模型和提示迁移
- 集成取决于特定于提供者的 API，这些 API 在没有实施工作的情况下不会映射到当前的 OpenAI API 表面
- 您无法自信地识别与模型使用相关的提示界面

默认操作：

- 不要即兴进行更广泛的升级
- 报告阻止程序并解释该修复超出了本指南的范围
- 如果有用，请描述能够解除迁移障碍的最小后续实施任务

## 兼容性检查表

在应用或推荐仅模型和提示升级之前，请检查：

1. 当前主机是否可以接受 `gpt-5.5` 型号字符串而不更改客户端代码或 API 表面？
2. 相关提示是否可识别、可编辑？
3. 主机是否依赖于可能需要API-表面更改、参数重写、提供程序迁移或工具重新布线的行为？
4. 可能的修复是否只是提示，或者是否需要实施更改？
5. 提示界面是否足够接近模型的使用情况，以便您可以进行有针对性的更改而不是广泛的清理？
6. 严格的结构化输出、模式或下游解析器是否仍然有明确的契约？
7. 对于长时间运行的响应或需要大量工具的 agents，如果主机依赖于前导码、重播的辅助项或多条辅助消息，`phase` 是否已保留？
8. 延迟、代币或价格假设是否通过测试、实际抽查或现有评估套件进行验证，而不是从一般模型定位推断？

如果项目 1 为 no，项目 3 到 4 指向实现工作，或者项目 7 为 no 并且修复需要代码更改，则返回 `blocked`。

如果2项为no，则返回`unknown`，除非用户可以指向提示位置。

重要：

- 工具、agents 或多个使用站点的现有使用本身并不是一个拦截器。
- 如果当前主机可以保留相同的 API 曲面和相同的工具定义，则优先选择 `model string + light prompt rewrite` 而不是 `blocked`。
- 为真正需要实施更改的情况保留`blocked`，而不是只需要更强的提示指导的情况。
- 未经任务级验证，请勿主张令牌节省。

## 范围边界

本指南可能：

- 更新或推荐更新的模型字符串
- 更新或推荐更新提示
- 检查代码和提示文件以了解这些更改所属的位置
- 检查现有响应流是否已保留 `phase`
- 标记兼容性阻止程序
- 建议使用现有测试、实际抽查或现有评估套件进行验证

本指南可能不会：

- 将聊天完成代码移至响应
- 将响应代码移动到另一个 API 表面
- 迁移 SDK、API、IDE 配置、shell 挂钩、插件或特定于提供商的工具
- 重写参数形状
- 更改工具定义或工具调用处理
- 更改结构化输出接线
- 在实现代码中添加或改进 `phase` 处理
- 编辑业务逻辑、编排逻辑、SDK 用法、IDE 配置、shell 挂钩或插件集成行为，模型字符串替换和直接相关提示编辑除外

如果安全 GPT-5.5 升级需要任何这些更改，请将路径标记为已阻止且超出范围。

## 验证计划

- 使用现有测试、实际抽查或现有评估套件（如果已有评估套件可用）来验证每个升级的使用站点。
- 与当前 GPT-5.4 基线（如果可用）进行比较。
- 检查任务成功、重试计数、工具调用计数、总令牌、延迟、输出形状和用户可见的质量。
- 对于专门的工作流程，验证最重要的合同，而不是仅判断一般输出质量。
- 如果添加了提示编辑，请确认每个块正在做实际工作而不是添加噪音。
- 如果工作流程对下游有影响，请在最终确定之前添加轻量级验证过程。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Upgrading to GPT-5.5

Use this guide when the user explicitly asks to upgrade an existing integration to GPT-5.5. Pair it with current OpenAI docs lookups. The default target string is `gpt-5.5`.

## Freshness check

Before applying this bundled guide for a latest/current/default model upgrade, run `node scripts/resolve-latest-model-info.js` from the OpenAI Docs skill directory.

- If the command returns `modelSlug: "gpt-5p5"`, continue with this bundled guide and use `references/prompting-guide.md` when prompt updates are needed.
- If the command returns a different `modelSlug`, fetch both the returned `migrationGuideUrl` and `promptingGuideUrl` and use them as the current source of truth instead of the bundled references.
- If the command fails, metadata is missing, or either remote guide cannot be fetched, continue with bundled fallback references and say the remote freshness check was unavailable.
- If the user explicitly named a target model, preserve that target and use current docs only to check compatibility or caveats.

## Upgrade posture

Upgrade with the narrowest safe change set:

- replace the model string first
- update only the prompts that are directly tied to that model usage
- do not automatically upgrade older or ambiguous model usages that may be intentionally pinned, such as historical docs, examples, tests, eval baselines, comparison code, or low-cost fallback/routing paths. Unless the user explicitly asks to upgrade all model usage, leave those sites unchanged and list them as confirmation-needed
- prefer prompt-only upgrades when possible
- if the upgrade would require API-surface changes, parameter rewrites, tool rewiring, provider migration, or broader code edits, mark it as blocked instead of stretching the scope

## Upgrade workflow

1. Inventory current model usage.
   - Search for model strings, client calls, and prompt-bearing files.
   - Include inline prompts, prompt templates, YAML or JSON configs, Markdown docs, and saved prompts when they are clearly tied to a model usage site.
2. Pair each model usage with its prompt surface.
   - Prefer the closest prompt surface first: inline system or developer text, then adjacent prompt files, then shared templates.
   - If you cannot confidently tie a prompt to the model usage, say so instead of guessing.
3. Classify the source model family.
   - Common buckets: GPT-5.4, GPT-5.3-Codex or GPT-5.2-Codex, earlier GPT-5.x, GPT-4o or GPT-4.1, reasoning models such as o1 or o3 or o4-mini, third-party model, or mixed and unclear.
4. Decide the upgrade class.
   - `model string only`
   - `model string + light prompt rewrite`
   - `blocked without code changes`
5. Run the compatibility gate.
   - Check whether the current integration can accept `gpt-5.5` without API-surface changes or implementation changes.
   - Check whether structured outputs, tool schemas, function names, and downstream parsers can remain unchanged.
   - For long-running Responses or tool-heavy agents, check whether `phase` is already preserved or round-tripped when the host replays assistant items or uses preambles.
   - If compatibility depends on code changes, return `blocked`.
   - If compatibility is unclear, return `unknown` rather than improvising.
6. Apply the upgrade when it is in scope.
   - Default replacement string: `gpt-5.5`.
   - Keep the intervention small and behavior-preserving.
   - Start from the current reasoning effort when it is visible unless there is a measured reason to change it.
   - For in-scope changes, update the model string and directly related prompts.
   - For blocked or unknown changes, do not edit; report the blocker or uncertainty.
7. Summarize the result.
   - `Current model usage`
   - `Model-string updates`
   - `Reasoning-effort handling`
   - `Prompt updates`
   - `Structured output and formatting assessment`
   - `Tool-use assessment` when the flow uses tools, retrieval, or terminal actions
   - `Phase assessment` when the flow is long-running, replayed, or tool-heavy
   - `Compatibility check`
   - `Validation performed`

Output rule:

- For each usage site, state the starting reasoning-effort recommendation.
- If the repo exposes the current reasoning setting, recommend preserving it first unless current OpenAI docs say otherwise.
- If the repo does not expose the current setting, recommend not adding one unless current OpenAI docs require it.

## Upgrade outcomes

### `model string only`

Choose this when:

- the source model is GPT-5.4
- the existing prompts are already short, explicit, and task-bounded
- the workflow does not rely on strict output formats, tool-call behavior, batch completeness, or long-horizon execution that should be validated after the upgrade
- there are no obvious compatibility blockers

Default action:

- replace the model string with `gpt-5.5`
- preserve the current reasoning effort
- keep prompts unchanged
- validate behavior with existing tests, realistic spot checks, or an existing eval suite when one is already available

### `model string + light prompt rewrite`

Choose this when:

- the task needs stronger completeness, citation discipline, verification, or dependency handling
- the upgraded model becomes too verbose, too dense, or hard to scan unless formatting is constrained
- the workflow has strict output shape requirements and lacks an explicit format contract, schema, or parser validation
- the workflow is research-heavy and needs stronger handling of sparse or empty retrieval results
- the workflow is coding-oriented, terminal-based, tool-heavy, or multi-agent, but the existing API surface and tool definitions can remain unchanged

Default action:

- replace the model string with `gpt-5.5`
- preserve the current reasoning effort for the first pass
- make only the smallest prompt edits needed for the observed workflow risk
- read the [GPT-5.5 prompting guide](<https://github.com/api/docs/guides/prompt-guidance?model=gpt-5.5>) to choose the smallest prompt changes that recover or improve behavior
- avoid broad prompt cleanup unrelated to the upgrade
- for research workflows, add citation rules, retrieval budgets, missing-evidence behavior, and validation guidance from the prompting guide
- for dependency-aware or tool-heavy workflows, add prerequisite checks, missing-context handling, explicit tool budgets, stop conditions, and validation guidance
- for coding or terminal workflows, add repo-specific constraints, acceptance criteria, and concrete validation commands
- for multi-agent support or triage workflows, add task ownership, handoff, completeness, and stopping criteria
- for long-running Responses agents with preambles or multiple assistant messages, explicitly review whether `phase` is already handled; if adding or preserving `phase` would require code edits, mark the path as `blocked`
- do not classify a coding or tool-using Responses workflow as `blocked` just because the visible snippet is minimal; prefer `model string + light prompt rewrite` unless the repo clearly shows that a safe GPT-5.5 path would require host-side code changes

### `blocked`

Choose this when:

- the upgrade appears to require API-surface changes
- the upgrade appears to require parameter rewrites or reasoning-setting changes that are not exposed outside implementation code
- the upgrade would require changing tool definitions, tool handler wiring, or schema contracts
- the user is asking for a tooling, IDE, plugin, shell, or environment migration rather than a model and prompt migration
- the integration depends on provider-specific APIs that do not map to the current OpenAI API surface without implementation work
- you cannot confidently identify the prompt surface tied to the model usage

Default action:

- do not improvise a broader upgrade
- report the blocker and explain that the fix is out of scope for this guide
- if useful, describe the smallest follow-up implementation task that would unblock the migration

## Compatibility checklist

Before applying or recommending a model-and-prompt-only upgrade, check:

1. Can the current host accept the `gpt-5.5` model string without changing client code or API surface?
2. Are the related prompts identifiable and editable?
3. Does the host depend on behavior that likely needs API-surface changes, parameter rewrites, provider migration, or tool rewiring?
4. Would the likely fix be prompt-only, or would it need implementation changes?
5. Is the prompt surface close enough to the model usage that you can make a targeted change instead of a broad cleanup?
6. Do strict structured outputs, schemas, or downstream parsers still have an explicit contract?
7. For long-running Responses or tool-heavy agents, is `phase` already preserved if the host relies on preambles, replayed assistant items, or multiple assistant messages?
8. Are latency, token, or price assumptions validated by tests, realistic spot checks, or an existing eval suite rather than inferred from general model positioning?

If item 1 is no, items 3 through 4 point to implementation work, or item 7 is no and the fix needs code changes, return `blocked`.

If item 2 is no, return `unknown` unless the user can point to the prompt location.

Important:

- Existing use of tools, agents, or multiple usage sites is not by itself a blocker.
- If the current host can keep the same API surface and the same tool definitions, prefer `model string + light prompt rewrite` over `blocked`.
- Reserve `blocked` for cases that truly require implementation changes, not cases that only need stronger prompt steering.
- Do not claim token savings without task-level validation.

## Scope boundaries

This guide may:

- update or recommend updated model strings
- update or recommend updated prompts
- inspect code and prompt files to understand where those changes belong
- inspect whether existing Responses flows already preserve `phase`
- flag compatibility blockers
- propose validation with existing tests, realistic spot checks, or existing eval suites

This guide may not:

- move Chat Completions code to Responses
- move Responses code to another API surface
- migrate SDKs, APIs, IDE configuration, shell hooks, plugins, or provider-specific tooling
- rewrite parameter shapes
- change tool definitions or tool-call handling
- change structured-output wiring
- add or retrofit `phase` handling in implementation code
- edit business logic, orchestration logic, SDK usage, IDE configuration, shell hooks, or plugin integration behavior except for model-string replacements and directly related prompt edits

If a safe GPT-5.5 upgrade requires any of those changes, mark the path as blocked and out of scope.

## Validation plan

- Validate each upgraded usage site with existing tests, realistic spot checks, or an existing eval suite when one is already available.
- Compare against the current GPT-5.4 baseline when available.
- Check task success, retry count, tool-call count, total tokens, latency, output shape, and user-visible quality.
- For specialized workflows, validate the contract that matters most instead of judging only general output quality.
- If prompt edits were added, confirm each block is doing real work instead of adding noise.
- If the workflow has downstream impact, add a lightweight verification pass before finalization.


</details>
