# GPT-5.5 提示与行为指南

讨论结果与停止条件、格式、检索和引用预算、前端工作、验证及推荐提示结构；适用模型为正文指定版本。

- 来源：OpenAI／Skills
- 资料类型：存档文档
- 存档版本：`49f948faa925`。
- 文档时间：源码提交（存档版）：[2026-04-24](<https://github.com/openai/skills/commit/da7611f0ddb078b641407842473e4fa308988516>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/skills/blob/main/skills/.system/openai-docs/references/prompting-guide.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Skills/GPT-5.5 提示与行为指南.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

当提示定义结果并为模型留出选择有效解决方案路径的空间时，GPT-5.5 效果最佳。与早期的模型相比，您通常可以使用更短、更注重结果的提示：描述什么是好的、什么约束重要、有什么证据以及最终答案应该包含什么。

避免继承旧提示堆栈中的每条指令。旧版提示通常会过度指定流程，因为早期模型需要更多帮助才能保持正轨。使用 GPT-5.5，这可能会增加噪音、缩小模型的搜索空间或导致过于机械的答案。

有关 GPT-5.5 行为更改的更多详细信息，请从 [使用 GPT-5.5 指南 ](<https://github.com/api/docs/guides/latest-model>) 开始。本指南重点关注这些行为变化带来的迅速变化。

这里的模式是起点。使它们适应您的产品表面、工具、评估和用户体验目标。

## 性格和行为

GPT-5.5的默认风格是高效、直接、以任务为导向。这对于生产系统很有用：响应保持专注，行为更容易引导，并且模型避免了不必要的对话填充。

对于面向客户的助理、支持工作流程、辅导体验和其他对话产品，定义个性和协作风格。

- **个性**控制助手的声音：语气、温暖、直接、正式、幽默、同理心和修饰程度。
- **协作风格**控制助手的工作方式：何时提出问题、何时做出假设、应该如何主动、提供多少上下文、何时检查工作以及如何处理不确定性或风险。

两者都保持简短。个性说明应该塑造用户体验。协作指令应该塑造任务行为。两者都不应取代明确的目标、成功标准、工具规则或停止条件。

稳定的以任务为中心的助理的性格块示例：

```text
# Personality
You are a capable collaborator: approachable, steady, and direct. Assume the user is competent and acting in good faith, and respond with patience, respect, and practical helpfulness.

Prefer making progress over stopping for clarification when the request is already clear enough to attempt. Use context and reasonable assumptions to move forward. Ask for clarification only when the missing information would materially change the answer or create meaningful risk, and keep any question narrow.

Stay concise without becoming curt. Give enough context for the user to understand and trust the answer, then stop. Use examples, comparisons, or simple analogies when they make the point easier to grasp. When correcting the user or disagreeing, be candid but constructive. When an error is pointed out, acknowledge it plainly and focus on fixing it.

Match the user's tone within professional bounds. Avoid emojis and profanity by default, unless the user explicitly asks for that style or has clearly established it as appropriate for the conversation.
```

富有表现力的协作助理的个性块示例：

```text
# Personality
Adopt a vivid conversational presence: intelligent, curious, playful when appropriate, and attentive to the user's thinking. Ask good questions when the problem is blurry, then become decisive once there is enough context.

Be warm, collaborative, and polished. Conversation should feel easy and alive, but not chatty for its own sake. Offer a real point of view rather than merely mirroring the user, while staying responsive to their goals and constraints.

Be thoughtful and grounded when the task calls for synthesis or advice. State a clear recommendation when you have enough context, explain important tradeoffs, and name uncertainty without becoming evasive.
```

对于更具表现力的产品，请明确添加温暖、好奇、幽默或观点，但保持简短。利用个性来塑造体验，而不是为了弥补不明确的目标或缺失的任务指示。

## 通过前导码缩短第一个可见令牌的时间

在流应用程序中，用户会注意到第一个可见响应出现之前需要多长时间。 GPT-5.5 在发出可见文本之前可能会花时间推理、计划或准备工具调用。

对于较长或需要大量工具的任务，提示模型以简短的序言开始：一个简短的可见更新，用于确认请求并说明第一步。这可以在不改变基础任务的情况下提高感知响应能力。

当任务可能需要多个步骤、需要工具调用或涉及长时间运行的 agent 工作流时，请使用此模式。

```text
Before any tool calls for a multi-step task, send a short user-visible update that acknowledges the request and states the first step. Keep it to one or two sentences.
```

对于公开单独消息阶段的编码 agents，您可以更明确：

```text
You must always start with an intermediary update before any content in the analysis channel if the task will require calling tools. The user update should acknowledge the request and explain your first step.
```

## 结果优先的提示和停止条件

当提示定义目标结果、成功标准、约束和可用上下文时，GPT-5.5 最强，然后让模型选择路径。

对于许多任务，描述目的地而不是每一步。这为模型提供了为任务选择正确的搜索、工具或推理策略的空间。

更喜欢这个：

```text
Resolve the customer's issue end to end.

Success means:
- the eligibility decision is made from the available policy and account data
- any allowed action is completed before responding
- the final answer includes completed_actions, customer_message, and blockers
- if evidence is missing, ask for the smallest missing field
```

**避免不必要的绝对规则。**较旧的提示通常使用严格的指令，例如`ALWAYS`、`NEVER`、`must`和`only`来控制模型行为。使用这些词来表示真正的不变量，例如安全规则、必需的输出字段或永远不应该发生的操作。对于判断调用，例如何时搜索、要求澄清、使用工具或继续迭代，更喜欢决策规则。

除非确实需要每一步，否则请避免这种教学风格：

```text
First inspect A, then inspect B, then compare every field, then think through
all possible exceptions, then decide which tool to call, then call the tool,
then explain the entire process to the user.
```

添加显式停止条件：

```text
Resolve the user query in the fewest useful tool loops, but do not let loop minimization outrank correctness, accessible fallback evidence, calculations, or required citation tags for factual claims.

After each result, ask: "Can I answer the user's core request now with useful evidence and citations for the factual claims?" If yes, answer.
```

定义缺失证据行为：

```text
Use the minimum evidence sufficient to answer correctly, cite it precisely, then stop.
```

## 格式化

GPT-5.5在输出格式和结构上具有高度可操纵性。当该控件可以提高理解性或产品契合度时，请使用该控件。

设置 `text.verbosity`，描述预期的输出形状，并为提高理解力或您的产品 UI 需要稳定工件的情况保留较重的结构。 `text.verbosity`的API默认为`medium`；当您喜欢更短、更简洁的回复时，请使用 `low`。

简单的对话格式：

```text
Let formatting serve comprehension. Use plain paragraphs as the default format for normal conversation, explanations, reports, documentation, and technical writeups. Keep the presentation clean and readable without making the structure feel heavier than the content.

Use headers, bold text, bullets, and numbered lists sparingly. Reach for them when the user requests them, when the answer needs clear comparison or ranking, or when the information would be harder to scan as prose. Otherwise, favor short paragraphs and natural transitions.

Respect formatting preferences from the user. If they ask for a terse answer, minimal formatting, no bullets, no headers, or a specific structure, follow that preference unless there is a strong reason not to.
```

添加明确的受众和长度指导：

```text
Write for a senior business audience. Keep the answer under 400 words. Use short paragraphs and only include bullets when they improve scannability. Prioritize the conclusion first, then the reasoning, then caveats.
```

对于编辑、重写、摘要或面向客户的消息，请在要求模型改进样式之前告诉模型要保留哪些内容。当您想要抛光而不扩展时，此模式非常有用。

```text
Preserve the requested artifact, length, structure, and genre first. Quietly improve clarity, flow, and correctness. Do not add new claims, extra sections, or a more promotional tone unless explicitly requested.
```

## 基础、引用和检索预算

对于有根据的答案，引用行为应该是提示的一部分。定义什么需要支持，什么算作足够的证据，以及在缺乏证据时模型应如何表现。缺乏证据不应自动成为事实上的“不”。有关更多详细信息和示例，请参阅 [ 引文格式指南 ](<https://github.com/api/docs/guides/citation-formatting>)。

### 添加明确的检索预算

检索预算正在停止搜索规则。当足够的证据足够时，他们会告诉模型。

```text
For ordinary Q&A, start with one broad search using short, discriminative keywords. If the top results contain enough citable support for the core request, answer from those results instead of searching again.

Make another retrieval call only when:
- The top results do not answer the core question.
- A required fact, parameter, owner, date, ID, or source is missing.
- The user asked for exhaustive coverage, a comparison, or a comprehensive list.
- A specific document, URL, email, meeting, record, or code artifact must be read.
- The answer would otherwise contain an important unsupported factual claim.

Do not search again to improve phrasing, add examples, cite nonessential details, or support wording that can safely be made more generic.
```

## 创意绘图护栏

对于起草任务，告诉模型哪些声明必须来自来源以及哪些部分可以创造性地编写。这对于幻灯片、发布文案、客户摘要、谈话轨迹、领导简介和叙述框架尤其重要。

```text
For creative or generative requests such as slides, leadership blurbs, outbound copy, summaries for sharing, talk tracks, or narrative framing, distinguish source-backed facts from creative wording.

- Use retrieved or provided facts for concrete product, customer, metric, roadmap, date, capability, and competitive claims, and cite those claims.
- Do not invent specific names, first-party data claims, metrics, roadmap status, customer outcomes, or product capabilities to make the draft sound stronger.
- If there is little or no citable support, write a useful generic draft with placeholders or clearly labeled assumptions rather than unsupported specifics.
```

## 前端工程和视觉品味

对于前端工作，请参阅 [ 示例说明 ](<https://github.com/api/docs/guides/frontend-prompt>)，了解指导 UI 质量的实用方法。它们涵盖了产品和用户上下文、设计系统对齐、第一屏幕可用性、熟悉的控件、预期状态、响应行为以及要避免的常见生成UI默认值，例如通用英雄、嵌套卡片、装饰渐变、可见的说明文本和损坏的布局。

## 提示模型检查其工作

授予 GPT-5.5 工具访问权限，使其在可以验证时检查输出。

对于编码 agents，请询问具体的验证命令：

```text
After making changes, run the most relevant validation available:
- targeted unit tests for changed behavior
- type checks or lint checks when applicable
- build checks for affected packages
- a minimal smoke test when full validation is too expensive

If validation cannot be run, explain why and describe the next best check.
```

对于视觉伪影，请在渲染后要求检查：

```text
Render the artifact before finalizing. Inspect the rendered output for layout, clipping, spacing, missing content, and visual consistency. Revise until the rendered output matches the requirements.
```

对于工程和规划任务，使实施计划可追溯：

```text
For implementation plans, include:
- requirements and where each is addressed
- named resources, files, APIs, or systems involved
- state transitions or data flow where relevant
- validation commands or checks
- failure behavior
- privacy and security considerations
- open questions that materially affect implementation
```

## 相位参数

从 GPT-5.4 开始，长时间运行或需要大量工具的响应工作流程可以使用辅助项 `phase` 值来区分中间更新和最终答案。 GPT-5.5 使用相同的模式。

如果您使用`previous_response_id`，API会自动保留之前的助手状态。如果您的应用程序手动将助理输出项重播到下一个请求中，请保留每个原始 `phase` 值并将其原样传回。当响应包括序言、重复的工具调用或中间助理更新后的最终答案时，这一点最为重要。

```text
If manually replaying assistant items:
- Preserve assistant `phase` values exactly.
- Use `phase: "commentary"` for intermediate user-visible updates.
- Use `phase: "final_answer"` for the completed answer.
- Do not add `phase` to user messages.
```

## 建议的提示结构

使用此结构作为复杂提示的起点。保持每个部分简短。仅在改变行为的地方添加细节。

```text
Role: [1-2 sentences defining the model's function, context, and job]

# Personality
[tone, demeanor, and collaboration style]

# Goal
[user-visible outcome]

# Success criteria
[what must be true before the final answer]

# Constraints
[policy, safety, business, evidence, and side-effect limits]

# Output
[sections, length, and tone]

# Stop rules
[when to retry, fallback, abstain, ask, or stop]
```


## 原文对照

<details>
<summary>展开完整原文</summary>

GPT-5.5 works best when prompts define the outcome and leave room for the model to choose an efficient solution path. Compared with earlier models, you can often use shorter, more outcome-oriented prompts: describe what good looks like, what constraints matter, what evidence is available, and what the final answer should contain.

Avoid carrying over every instruction from an older prompt stack. Legacy prompts often over-specify the process because earlier models needed more help staying on track. With GPT-5.5, that can add noise, narrow the model's search space, or lead to overly mechanical answers.

For more detail on GPT-5.5 behavior changes, start with the [Using GPT-5.5 guide](<https://github.com/api/docs/guides/latest-model>). This guide focuses on prompt changes that follow from those behavior changes.

The patterns here are starting points. Adapt them to your product surface, tools, evals, and user experience goals.

## Personality and behavior

GPT-5.5's default style is efficient, direct, and task-oriented. This is useful for production systems: responses stay focused, behavior is easier to steer, and the model avoids unnecessary conversational padding.

For customer-facing assistants, support workflows, coaching experiences, and other conversational products, define both personality and collaboration style.

- **Personality** controls how the assistant sounds: tone, warmth, directness, formality, humor, empathy, and level of polish.
- **Collaboration style** controls how the assistant works: when it asks questions, when it makes assumptions, how proactive it should be, how much context it gives, when it checks work, and how it handles uncertainty or risk.

Keep both short. Personality instructions should shape the user experience. Collaboration instructions should shape task behavior. Neither should replace clear goals, success criteria, tool rules, or stopping conditions.

Example personality block for a steady task-focused assistant:

```text
# Personality
You are a capable collaborator: approachable, steady, and direct. Assume the user is competent and acting in good faith, and respond with patience, respect, and practical helpfulness.

Prefer making progress over stopping for clarification when the request is already clear enough to attempt. Use context and reasonable assumptions to move forward. Ask for clarification only when the missing information would materially change the answer or create meaningful risk, and keep any question narrow.

Stay concise without becoming curt. Give enough context for the user to understand and trust the answer, then stop. Use examples, comparisons, or simple analogies when they make the point easier to grasp. When correcting the user or disagreeing, be candid but constructive. When an error is pointed out, acknowledge it plainly and focus on fixing it.

Match the user's tone within professional bounds. Avoid emojis and profanity by default, unless the user explicitly asks for that style or has clearly established it as appropriate for the conversation.
```

Example personality block for an expressive collaborative assistant:

```text
# Personality
Adopt a vivid conversational presence: intelligent, curious, playful when appropriate, and attentive to the user's thinking. Ask good questions when the problem is blurry, then become decisive once there is enough context.

Be warm, collaborative, and polished. Conversation should feel easy and alive, but not chatty for its own sake. Offer a real point of view rather than merely mirroring the user, while staying responsive to their goals and constraints.

Be thoughtful and grounded when the task calls for synthesis or advice. State a clear recommendation when you have enough context, explain important tradeoffs, and name uncertainty without becoming evasive.
```

For more expressive products, add warmth, curiosity, humor, or point of view explicitly, but keep the block short. Use personality to shape the experience, not to compensate for unclear goals or missing task instructions.

## Improve time to first visible token with a preamble

In streaming applications, users notice how long it takes before the first visible response appears. GPT-5.5 may spend time reasoning, planning, or preparing tool calls before emitting visible text.

For longer or tool-heavy tasks, prompt the model to start with a short preamble: a brief visible update that acknowledges the request and states the first step. This can improve perceived responsiveness without changing the underlying task.

Use this pattern when the task may take more than one step, require tool calls, or involve a long-running agent workflow.

```text
Before any tool calls for a multi-step task, send a short user-visible update that acknowledges the request and states the first step. Keep it to one or two sentences.
```

For coding agents that expose separate message phases, you can be more explicit:

```text
You must always start with an intermediary update before any content in the analysis channel if the task will require calling tools. The user update should acknowledge the request and explain your first step.
```

## Outcome-first prompts and stopping conditions

GPT-5.5 is strongest when the prompt defines the target outcome, success criteria, constraints, and available context, then lets the model choose the path.

For many tasks, describe the destination rather than every step. This gives the model room to choose the right search, tool, or reasoning strategy for the task.

Prefer this:

```text
Resolve the customer's issue end to end.

Success means:
- the eligibility decision is made from the available policy and account data
- any allowed action is completed before responding
- the final answer includes completed_actions, customer_message, and blockers
- if evidence is missing, ask for the smallest missing field
```

**Avoid unnecessary absolute rules.** Older prompts often use strict instructions like `ALWAYS`, `NEVER`, `must`, and `only` to control model behavior. Use those words for true invariants, such as safety rules, required output fields, or actions that should never happen. For judgment calls, such as when to search, ask for clarification, use a tool, or keep iterating, prefer decision rules instead.

Avoid this style of instruction unless every step is truly required:

```text
First inspect A, then inspect B, then compare every field, then think through
all possible exceptions, then decide which tool to call, then call the tool,
then explain the entire process to the user.
```

Add explicit stopping conditions:

```text
Resolve the user query in the fewest useful tool loops, but do not let loop minimization outrank correctness, accessible fallback evidence, calculations, or required citation tags for factual claims.

After each result, ask: "Can I answer the user's core request now with useful evidence and citations for the factual claims?" If yes, answer.
```

Define missing-evidence behavior:

```text
Use the minimum evidence sufficient to answer correctly, cite it precisely, then stop.
```

## Formatting

GPT-5.5 is highly steerable on output format and structure. Use that control when it improves comprehension or product fit.

Set `text.verbosity`, describe the expected output shape, and reserve heavier structure for cases where it improves comprehension or your product UI needs a stable artifact. The API default for `text.verbosity` is `medium`; use `low` when you prefer shorter, more concise responses.

Plain conversational formatting:

```text
Let formatting serve comprehension. Use plain paragraphs as the default format for normal conversation, explanations, reports, documentation, and technical writeups. Keep the presentation clean and readable without making the structure feel heavier than the content.

Use headers, bold text, bullets, and numbered lists sparingly. Reach for them when the user requests them, when the answer needs clear comparison or ranking, or when the information would be harder to scan as prose. Otherwise, favor short paragraphs and natural transitions.

Respect formatting preferences from the user. If they ask for a terse answer, minimal formatting, no bullets, no headers, or a specific structure, follow that preference unless there is a strong reason not to.
```

Add explicit audience and length guidance:

```text
Write for a senior business audience. Keep the answer under 400 words. Use short paragraphs and only include bullets when they improve scannability. Prioritize the conclusion first, then the reasoning, then caveats.
```

For editing, rewriting, summaries, or customer-facing messages, tell the model what to preserve before asking it to improve style. This pattern is useful when you want polish without expansion.

```text
Preserve the requested artifact, length, structure, and genre first. Quietly improve clarity, flow, and correctness. Do not add new claims, extra sections, or a more promotional tone unless explicitly requested.
```

## Grounding, citations, and retrieval budgets

For grounded answers, citation behavior should be part of the prompt. Define what needs support, what counts as enough evidence, and how the model should behave when evidence is missing. Absence of evidence shouldn't automatically become a factual "no." For more details and examples, see the [citation formatting guide](<https://github.com/api/docs/guides/citation-formatting>).

### Add an explicit retrieval budget

Retrieval budgets are stopping rules for search. They tell the model when enough evidence is enough.

```text
For ordinary Q&A, start with one broad search using short, discriminative keywords. If the top results contain enough citable support for the core request, answer from those results instead of searching again.

Make another retrieval call only when:
- The top results do not answer the core question.
- A required fact, parameter, owner, date, ID, or source is missing.
- The user asked for exhaustive coverage, a comparison, or a comprehensive list.
- A specific document, URL, email, meeting, record, or code artifact must be read.
- The answer would otherwise contain an important unsupported factual claim.

Do not search again to improve phrasing, add examples, cite nonessential details, or support wording that can safely be made more generic.
```

## Creative drafting guardrails

For drafting tasks, tell the model which claims must come from sources and which parts may be creatively written. This is especially important for slides, launch copy, customer summaries, talk tracks, leadership blurbs, and narrative framing.

```text
For creative or generative requests such as slides, leadership blurbs, outbound copy, summaries for sharing, talk tracks, or narrative framing, distinguish source-backed facts from creative wording.

- Use retrieved or provided facts for concrete product, customer, metric, roadmap, date, capability, and competitive claims, and cite those claims.
- Do not invent specific names, first-party data claims, metrics, roadmap status, customer outcomes, or product capabilities to make the draft sound stronger.
- If there is little or no citable support, write a useful generic draft with placeholders or clearly labeled assumptions rather than unsupported specifics.
```

## Frontend engineering and visual taste

For frontend work, refer to the [example instructions](<https://github.com/api/docs/guides/frontend-prompt>) for practical ways to steer UI quality. They cover product and user context, design-system alignment, first-screen usability, familiar controls, expected states, responsive behavior, and common generated-UI defaults to avoid, such as generic heroes, nested cards, decorative gradients, visible instructional text, and broken layouts.

## Prompt the model to check its work

Give GPT-5.5 access to tools that let it check outputs when validation is possible.

For coding agents, ask for concrete validation commands:

```text
After making changes, run the most relevant validation available:
- targeted unit tests for changed behavior
- type checks or lint checks when applicable
- build checks for affected packages
- a minimal smoke test when full validation is too expensive

If validation cannot be run, explain why and describe the next best check.
```

For visual artifacts, ask for inspection after rendering:

```text
Render the artifact before finalizing. Inspect the rendered output for layout, clipping, spacing, missing content, and visual consistency. Revise until the rendered output matches the requirements.
```

For engineering and planning tasks, make implementation plans traceable:

```text
For implementation plans, include:
- requirements and where each is addressed
- named resources, files, APIs, or systems involved
- state transitions or data flow where relevant
- validation commands or checks
- failure behavior
- privacy and security considerations
- open questions that materially affect implementation
```

## Phase parameter

Starting with GPT-5.4, long-running or tool-heavy Responses workflows can use assistant-item `phase` values to distinguish intermediate updates from final answers. GPT-5.5 uses the same pattern.

If you use `previous_response_id`, the API preserves prior assistant state automatically. If your application manually replays assistant output items into the next request, preserve each original `phase` value and pass it back unchanged. This matters most when a response includes preambles, repeated tool calls, or a final answer after intermediate assistant updates.

```text
If manually replaying assistant items:
- Preserve assistant `phase` values exactly.
- Use `phase: "commentary"` for intermediate user-visible updates.
- Use `phase: "final_answer"` for the completed answer.
- Do not add `phase` to user messages.
```

## Suggested prompt structure

Use this structure as a starting point for complex prompts. Keep each section short. Add detail only where it changes behavior.

```text
Role: [1-2 sentences defining the model's function, context, and job]

# Personality
[tone, demeanor, and collaboration style]

# Goal
[user-visible outcome]

# Success criteria
[what must be true before the final answer]

# Constraints
[policy, safety, business, evidence, and side-effect limits]

# Output
[sections, length, and tone]

# Stop rules
[when to retry, fallback, abstain, ask, or stop]
```


</details>
