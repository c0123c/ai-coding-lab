# 优化 Skills 触发描述

讲解正反触发查询、重复测试、训练验证拆分和描述改进，减少该触发时漏用或无关任务误用技能。

- 来源：通用规范／Agent Skills
- 资料类型：存档文档
- 文档时间：官网现页更新：[2026-04-22](<https://agentskills.io/skill-creation/optimizing-descriptions>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://agentskills.io/skill-creation/optimizing-descriptions.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/通用规范/Agent Skills/优化 Skills 触发描述.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

> ## 文档索引
> 在以下位置获取完整的文档索引： https://agentskills.io/llms.txt
> 在进一步探索之前使用此文件来发现所有可用页面。

# 优化技能描述

> 如何改进技能描述，使其在相关提示下可靠触发。

技能只有被激活才有帮助。 `SKILL.md` frontmatter 中的 `description` 字段是 agents 用于决定是否为给定任务加载技能的主要机制。描述不明确意味着该技能不会在应该触发的时候触发；过于宽泛的描述意味着它会在不应该触发的时候触发。

本指南介绍了如何系统地测试和改进技能描述以提高触发准确性。

## 技能触发原理

Agents使用[渐进式披露](<https://agentskills.io/specification#progressive-disclosure>)来管理上下文。启动时，他们仅加载每个可用技能的 `name` 和 `description` - 足以决定技能何时可能相关。当用户的任务与描述匹配时，agent 会将完整的 `SKILL.md` 读取到上下文中并遵循其说明。

这意味着描述承担了触发的全部负担。如果描述没有传达该技能何时有用，那么 agent 将不知道要伸手去拿它。

一个重要的细微差别：agents 通常仅针对需要超出其单独处理能力的知识或能力的任务咨询技能。即使描述完全匹配，像“阅读此PDF”这样的简单的一步请求也可能不会触发PDF技能，因为agent可以使用基本工具来处理它。涉及专业知识的任务——不熟悉的 API、特定领域的工作流程或不常见的格式——是精心编写的描述可以发挥作用的地方。

## 撰写有效的描述

在测试之前，了解一个好的描述是什么样的会很有帮助。几个原则：

* **使用命令式措辞。**将描述框架为对agent的指令：“在……时使用此技能”而不是“此技能会……”agent正在决定是否采取行动，所以告诉它何时采取行动。
* **关注用户意图，而不是实现。**描述用户想要实现的目标，而不是技能的内部机制。 agent 与用户请求的内容相匹配。
* **宁可咄咄逼人。** 明确列出该技能适用的上下文，包括用户不直接命名域的情况：“即使他们没有明确提及‘CSV’或‘分析’。”
* **保持简洁。**短段落中的几句话通常是正确的 - 足够长以涵盖技能的范围，足够短以使其不会使 agent 的上下文在许多技能中膨胀。 [规范](<https://agentskills.io/specification#description-field>)强制执行1024字符的硬限制。

## 设计触发器评估查询

要测试触发，您需要一组评估查询 - 真实的用户提示，标明它们是否应该触发您的技能。

```json eval_queries.json theme={null}
[
  { "query": "I've got a spreadsheet in ~/data/q4_results.xlsx with revenue in col C and expenses in col D — can you add a profit margin column and highlight anything under 10%?", "should_trigger": true },
  { "query": "whats the quickest way to convert this json file to yaml", "should_trigger": false }
]
```

针对 20 查询：应触发的 8-10 和不应触发的 8-10。

### 应触发查询

这些测试描述是否涵盖了技能的范围。沿着几个轴改变它们：

* **措辞**：有些正式，有些随意，有些有拼写错误或缩写。
* **显性**：有些直接命名技能的领域（“分析这个CSV”），其他人描述需求而不命名（“我的老板想要来自这个数据文件的图表”）。
* **详细信息**：将简洁的提示与上下文丰富的提示混合在一起 - 简短的“分析我的销售 CSV 并制作图表”以及包含文件路径、列名称和背景故事的较长消息。
* **复杂性**：改变步骤数和决策点。包括单步骤任务和多步骤工作流程，以测试 agent 当它处理的任务被埋在更大的链中时是否可以辨别技能是否相关。

最有用的“应该触发”查询是那些技能会有所帮助但仅从查询来看联系并不明显的查询。在这些情况下，描述措辞会产生影响——如果查询已经询问技能的具体用途，则任何合理的描述都会触发。

### 不应该触发的查询

最有价值的负面测试用例是**未遂事件** - 与您的技能共享关键字或概念但实际上需要不同内容的查询。这些测试测试描述是否准确，而不仅仅是宽泛。

对于 CSV 分析技能，弱负面示例将是：

* `"Write a fibonacci function"` — 显然不相关，没有测试。
* `"What's the weather today?"` — 没有关键字重叠，太简单了。

强烈的负面例子：

* `"I need to update the formulas in my Excel budget spreadsheet"` — 共享“电子表格”和“数据”概念，但需要 Excel 编辑，而不是 CSV 分析。
* `"can you write a python script that reads a csv and uploads each row to our postgres database"` — 涉及CSV，但任务是数据库ETL，而不是分析。

### 现实主义的技巧

真实的用户提示包含通用测试查询所缺乏的上下文。包括：

* 文件路径 (`~/Downloads/report_final_v2.xlsx`)
* 个人背景（`"my manager asked me to..."`）
* 具体细节（列名称、公司名称、数据值）
* 随意的语言、缩写和偶尔出现的拼写错误

## 测试描述是否触发

基本方法：通过安装了技能的agent运行每个查询，并观察agent是否调用它。确保该技能已注册并可由您的 agent 发现 - 其工作原理因客户端而异（例如，技能目录、配置文件或 CLI 标志）。

大多数 agent 客户端提供某种形式的可观察性 - 执行日志、工具调用历史或详细输出 - 让您了解在运行期间参考了哪些技能。有关详细信息，请检查客户的文档。 agent加载你技能的`SKILL.md`时触发该技能；如果 agent 在没有咨询它的情况下继续进行，它不会触发。

如果满足以下条件，则查询“通过”：

* `should_trigger` 是 `true` 并且调用了该技能，或者
* `should_trigger`为`false`且未调用技能。

### 运行多次

模型行为是不确定的 - 相同的查询可能会在一次运行中触发技能，但不会在下一次运行中触发。多次运行每个查询（3 是一个合理的起点）并计算 ** 触发率 **：调用技能的运行分数。

如果触发率高于阈值（0.5 是合理的默认值），则应触发查询将通过。如果不应该触发的查询的触发率低于该阈值，则该查询将通过。

对于 3 处的 20 查询，每个查询都会运行，这就是 60 调用。您需要编写此脚本。这是一般结构 - 将 `check_triggered` 中的 `claude` 调用和检测逻辑替换为 agent 客户端提供的任何内容：

```bash theme={null}
#!/bin/bash
QUERIES_FILE="${1:?Usage: $0 <queries.json>}"
SKILL_NAME="my-skill"
RUNS=3

# This example uses Claude Code's JSON output to check for Skill tool calls.
# Replace this function with detection logic for your agent client.
# Should return 0 (success) if the skill was invoked, 1 otherwise.
check_triggered() {
  local query="$1"
  claude -p "$query" --output-format json 2>/dev/null \
    | jq -e --arg skill "$SKILL_NAME" \
      'any(.messages[].content[]; .type == "tool_use" and .name == "Skill" and .input.skill == $skill)' \
      > /dev/null 2>&1
}

count=$(jq length "$QUERIES_FILE")
for i in $(seq 0 $((count - 1))); do
  query=$(jq -r ".[$i].query" "$QUERIES_FILE")
  should_trigger=$(jq -r ".[$i].should_trigger" "$QUERIES_FILE")
  triggers=0

  for run in $(seq 1 $RUNS); do
    check_triggered "$query" && triggers=$((triggers + 1))
  done

  jq -n \
    --arg query "$query" \
    --argjson should_trigger "$should_trigger" \
    --argjson triggers "$triggers" \
    --argjson runs "$RUNS" \
    '{query: $query, should_trigger: $should_trigger, triggers: $triggers, runs: $runs, trigger_rate: ($triggers / $runs)}'
done | jq -s '.'
```



**提示：**

如果您的 agent 客户端支持它，一旦结果明确，您就可以提前停止运行 - agent 要么咨询该技能，要么在没有该技能的情况下开始工作。这可以显着减少运行完整评估集的时间和成本。


## 通过 train/validation 分割避免过度拟合

如果针对所有查询优化描述，则可能会面临过度拟合的风险 - 精心设计的描述适用于这些特定短语，但不适用于新短语。

解决方案是拆分查询集：

* **训练集 (\~60%)**：用于识别故障并指导改进的查询。
* **验证集（\~40%）**：您预留的查询，仅用于检查改进是否通用。

确保两组都包含按比例混合的应该触发和不应该触发的查询——不要意外地将所有积极因素放在一组中。随机洗牌并在迭代中保持固定的分割，以便您可以进行同类比较。

如果您使用的脚本类似于上面的 [](<#运行多次>)，则可以将查询拆分为两个文件 — `train_queries.json` 和 `validation_queries.json` — 并分别针对每个文件运行该脚本。

## 优化循环

1. **评估***训练集和验证集*的当前描述。火车结果指导你的改变；验证结果会告诉您这些更改是否具有普遍性。
2. **识别故障** 在 *火车组*：哪些应该触发的查询没有触发？哪些不应触发查询？
   * 仅使用训练集失败来指导您的更改 - 无论您是自己修改描述还是提示 LLM，都应将验证集结果排除在流程之外。
3. **修改描述。**重点概括：
   * 如果“应该触发”查询失败，则说明可能过于狭窄。扩大范围或添加有关该技能何时有用的背景。
   * 如果不应该触发的查询是错误触发的，则说明可能过于宽泛。添加关于该技能*不*做什么的具体信息，或澄清该技能与相邻功能之间的界限。
   * 避免添加失败查询中的特定关键字——这会导致过度拟合。相反，找到这些查询所代表的一般类别或概念并解决它。
   * 如果您在几次迭代后陷入困境，请尝试采用结构上不同的描述方法，而不是增量调整。不同的框架或句子结构可能会突破细化无法突破的地方。
   * 检查描述是否保持在 1024 字符限制之下 - 描述在优化过程中往往会增长。
4. **重复**步骤1-3直到所有*训练集*查询通过或者您不再看到有意义的改进。
5. **根据验证通过率选择最佳迭代**——*验证集*中通过的查询的比例。请注意，最好的描述可能不是您最后给出的描述；较早的迭代可能比后来过度拟合训练集的迭代具有更高的验证通过率。

五次迭代通常就足够了。如果性能没有提高，问题可能出在查询（太简单、太难或标记不当）而不是说明。



**提示：**

[`skill-creator`](<https://github.com/anthropics/skills/tree/main/skills/skill-creator>) 技能使该循环端到端自动化：它分割评估集，并行评估触发率，使用 Claude 提出描述改进，并生成实时 HTML 报告，您可以在其运行时观看。


## 应用结果

选择最佳描述后：

1. 更新 `SKILL.md` frontmatter 中的 `description` 字段。
2. 验证描述是否低于 [1024 字符限制 ](<https://agentskills.io/specification#description-field>)。
3. 验证描述是否按预期触发。手动尝试一些提示作为快速健全性检查。为了进行更严格的测试，请编写 5-10 新查询（应该触发和不应该触发的混合）并通过 eval 脚本运行它们 - 因为这些查询从来不是优化过程的一部分，它们可以让您诚实地检查描述是否概括。

之前和之后：

```yaml theme={null}
# Before
description: Process CSV files.

# After
description: >
  Analyze CSV and tabular data files — compute summary statistics,
  add derived columns, generate charts, and clean messy data. Use this
  skill when the user has a CSV, TSV, or Excel file and wants to
  explore, transform, or visualize the data, even if they don't
  explicitly mention "CSV" or "analysis."
```

改进后的描述更具体地说明了技能的用途（摘要统计、派生列、图表、清理），并更广泛地说明了技能的应用时间（CSV、TSV、Excel；即使没有明确的关键字）。

## 后续步骤

一旦你的技能可靠触发，你就会想要评估它是否产生良好的输出。请参阅 [评估技能输出质量 ](<https://agentskills.io/skill-creation/evaluating-skills>) 了解如何设置测试用例、对结果进行评分和迭代。


## 原文对照

<details>
<summary>展开完整原文</summary>

> ## Documentation Index
> Fetch the complete documentation index at: https://agentskills.io/llms.txt
> Use this file to discover all available pages before exploring further.

# Optimizing skill descriptions

> How to improve your skill's description so it triggers reliably on relevant prompts.

A skill only helps if it gets activated. The `description` field in your `SKILL.md` frontmatter is the primary mechanism agents use to decide whether to load a skill for a given task. An under-specified description means the skill won't trigger when it should; an over-broad description means it triggers when it shouldn't.

This guide covers how to systematically test and improve your skill's description for triggering accuracy.

## How skill triggering works

Agents use [progressive disclosure](<https://agentskills.io/specification#progressive-disclosure>) to manage context. At startup, they load only the `name` and `description` of each available skill — just enough to decide when a skill might be relevant. When a user's task matches a description, the agent reads the full `SKILL.md` into context and follows its instructions.

This means the description carries the entire burden of triggering. If the description doesn't convey when the skill is useful, the agent won't know to reach for it.

One important nuance: agents typically only consult skills for tasks that require knowledge or capabilities beyond what they can handle alone. A simple, one-step request like "read this PDF" may not trigger a PDF skill even if the description matches perfectly, because the agent can handle it with basic tools. Tasks that involve specialized knowledge — an unfamiliar API, a domain-specific workflow, or an uncommon format — are where a well-written description can make the difference.

## Writing effective descriptions

Before testing, it helps to know what a good description looks like. A few principles:

* **Use imperative phrasing.** Frame the description as an instruction to the agent: "Use this skill when..." rather than "This skill does..." The agent is deciding whether to act, so tell it when to act.
* **Focus on user intent, not implementation.** Describe what the user is trying to achieve, not the skill's internal mechanics. The agent matches against what the user asked for.
* **Err on the side of being pushy.** Explicitly list contexts where the skill applies, including cases where the user doesn't name the domain directly: "even if they don't explicitly mention 'CSV' or 'analysis.'"
* **Keep it concise.** A few sentences to a short paragraph is usually right — long enough to cover the skill's scope, short enough that it doesn't bloat the agent's context across many skills. The [specification](<https://agentskills.io/specification#description-field>) enforces a hard limit of 1024 characters.

## Designing trigger eval queries

To test triggering, you need a set of eval queries — realistic user prompts labeled with whether they should or shouldn't trigger your skill.

```json eval_queries.json theme={null}
[
  { "query": "I've got a spreadsheet in ~/data/q4_results.xlsx with revenue in col C and expenses in col D — can you add a profit margin column and highlight anything under 10%?", "should_trigger": true },
  { "query": "whats the quickest way to convert this json file to yaml", "should_trigger": false }
]
```

Aim for about 20 queries: 8-10 that should trigger and 8-10 that shouldn't.

### Should-trigger queries

These test whether the description captures the skill's scope. Vary them along several axes:

* **Phrasing**: some formal, some casual, some with typos or abbreviations.
* **Explicitness**: some name the skill's domain directly ("analyze this CSV"), others describe the need without naming it ("my boss wants a chart from this data file").
* **Detail**: mix terse prompts with context-heavy ones — a short "analyze my sales CSV and make a chart" alongside a longer message with file paths, column names, and backstory.
* **Complexity**: vary the number of steps and decision points. Include single-step tasks alongside multi-step workflows to test whether the agent can discern the skill is relevant when the task it addresses is buried in a larger chain.

The most useful should-trigger queries are ones where the skill would help but the connection isn't obvious from the query alone. These are the cases where description wording makes the difference — if the query already asks for exactly what the skill does, any reasonable description would trigger.

### Should-not-trigger queries

The most valuable negative test cases are **near-misses** — queries that share keywords or concepts with your skill but actually need something different. These test whether the description is precise, not just broad.

For a CSV analysis skill, weak negative examples would be:

* `"Write a fibonacci function"` — obviously irrelevant, tests nothing.
* `"What's the weather today?"` — no keyword overlap, too easy.

Strong negative examples:

* `"I need to update the formulas in my Excel budget spreadsheet"` — shares "spreadsheet" and "data" concepts, but needs Excel editing, not CSV analysis.
* `"can you write a python script that reads a csv and uploads each row to our postgres database"` — involves CSV, but the task is database ETL, not analysis.

### Tips for realism

Real user prompts contain context that generic test queries lack. Include:

* File paths (`~/Downloads/report_final_v2.xlsx`)
* Personal context (`"my manager asked me to..."`)
* Specific details (column names, company names, data values)
* Casual language, abbreviations, and occasional typos

## Testing whether a description triggers

The basic approach: run each query through your agent with the skill installed and observe whether the agent invokes it. Make sure the skill is registered and discoverable by your agent — how this works varies by client (e.g., a skills directory, a configuration file, or a CLI flag).

Most agent clients provide some form of observability — execution logs, tool call histories, or verbose output — that lets you see which skills were consulted during a run. Check your client's documentation for details. The skill triggered if the agent loaded your skill's `SKILL.md`; it didn't trigger if the agent proceeded without consulting it.

A query "passes" if:

* `should_trigger` is `true` and the skill was invoked, or
* `should_trigger` is `false` and the skill was not invoked.

### Running multiple times

Model behavior is nondeterministic — the same query might trigger the skill on one run but not the next. Run each query multiple times (3 is a reasonable starting point) and compute a **trigger rate**: the fraction of runs where the skill was invoked.

A should-trigger query passes if its trigger rate is above a threshold (0.5 is a reasonable default). A should-not-trigger query passes if its trigger rate is below that threshold.

With 20 queries at 3 runs each, that's 60 invocations. You'll want to script this. Here's the general structure — replace the `claude` invocation and detection logic in `check_triggered` with whatever your agent client provides:

```bash theme={null}
#!/bin/bash
QUERIES_FILE="${1:?Usage: $0 <queries.json>}"
SKILL_NAME="my-skill"
RUNS=3

# This example uses Claude Code's JSON output to check for Skill tool calls.
# Replace this function with detection logic for your agent client.
# Should return 0 (success) if the skill was invoked, 1 otherwise.
check_triggered() {
  local query="$1"
  claude -p "$query" --output-format json 2>/dev/null \
    | jq -e --arg skill "$SKILL_NAME" \
      'any(.messages[].content[]; .type == "tool_use" and .name == "Skill" and .input.skill == $skill)' \
      > /dev/null 2>&1
}

count=$(jq length "$QUERIES_FILE")
for i in $(seq 0 $((count - 1))); do
  query=$(jq -r ".[$i].query" "$QUERIES_FILE")
  should_trigger=$(jq -r ".[$i].should_trigger" "$QUERIES_FILE")
  triggers=0

  for run in $(seq 1 $RUNS); do
    check_triggered "$query" && triggers=$((triggers + 1))
  done

  jq -n \
    --arg query "$query" \
    --argjson should_trigger "$should_trigger" \
    --argjson triggers "$triggers" \
    --argjson runs "$RUNS" \
    '{query: $query, should_trigger: $should_trigger, triggers: $triggers, runs: $runs, trigger_rate: ($triggers / $runs)}'
done | jq -s '.'
```



**提示：**

  If your agent client supports it, you can stop a run early once the outcome is clear — the agent either consulted the skill or started working without it. This can significantly reduce the time and cost of running the full eval set.


## Avoiding overfitting with train/validation splits

If you optimize the description against all your queries, you risk overfitting — crafting a description that works for these specific phrasings but fails on new ones.

The solution is to split your query set:

* **Train set (\~60%)**: the queries you use to identify failures and guide improvements.
* **Validation set (\~40%)**: queries you set aside and only use to check whether improvements generalize.

Make sure both sets contain a proportional mix of should-trigger and should-not-trigger queries — don't accidentally put all the positives in one set. Shuffle randomly and keep the split fixed across iterations so you're comparing apples to apples.

If you're using a script like the one [above](<#running-multiple-times>), you can split your queries into two files — `train_queries.json` and `validation_queries.json` — and run the script against each one separately.

## The optimization loop

1. **Evaluate** the current description on both *train and validation sets*. The train results guide your changes; the validation results tell you whether those changes are generalizing.
2. **Identify failures** in the *train set*: which should-trigger queries didn't trigger? Which should-not-trigger queries did?
   * Only use train set failures to guide your changes — whether you're revising the description yourself or prompting an LLM, keep validation set results out of the process.
3. **Revise the description.** Focus on generalizing:
   * If should-trigger queries are failing, the description may be too narrow. Broaden the scope or add context about when the skill is useful.
   * If should-not-trigger queries are false-triggering, the description may be too broad. Add specificity about what the skill does *not* do, or clarify the boundary between this skill and adjacent capabilities.
   * Avoid adding specific keywords from failed queries — that's overfitting. Instead, find the general category or concept those queries represent and address that.
   * If you're stuck after several iterations, try a structurally different approach to the description rather than incremental tweaks. A different framing or sentence structure may break through where refinement can't.
   * Check that the description stays under the 1024-character limit — descriptions tend to grow during optimization.
4. **Repeat** steps 1-3 until all *train set* queries pass or you stop seeing meaningful improvement.
5. **Select the best iteration** by its validation pass rate — the fraction of queries in the *validation set* that passed. Note that the best description may not be the last one you produced; an earlier iteration might have a higher validation pass rate than later ones that overfit to the train set.

Five iterations is usually enough. If performance isn't improving, the issue may be with the queries (too easy, too hard, or poorly labeled) rather than the description.



**提示：**

  The [`skill-creator`](<https://github.com/anthropics/skills/tree/main/skills/skill-creator>) Skill automates this loop end-to-end: it splits the eval set, evaluates trigger rates in parallel, proposes description improvements using Claude, and generates a live HTML report you can watch as it runs.


## Applying the result

Once you've selected the best description:

1. Update the `description` field in your `SKILL.md` frontmatter.
2. Verify the description is under the [1024-character limit](<https://agentskills.io/specification#description-field>).
3. Verify the description triggers as expected. Try a few prompts manually as a quick sanity check. For a more rigorous test, write 5-10 fresh queries (a mix of should-trigger and should-not-trigger) and run them through the eval script — since these queries were never part of the optimization process, they give you an honest check on whether the description generalizes.

Before and after:

```yaml theme={null}
# Before
description: Process CSV files.

# After
description: >
  Analyze CSV and tabular data files — compute summary statistics,
  add derived columns, generate charts, and clean messy data. Use this
  skill when the user has a CSV, TSV, or Excel file and wants to
  explore, transform, or visualize the data, even if they don't
  explicitly mention "CSV" or "analysis."
```

The improved description is more specific about what the skill does (summary stats, derived columns, charts, cleaning) and broader about when it applies (CSV, TSV, Excel; even without explicit keywords).

## Next steps

Once your skill triggers reliably, you'll want to evaluate whether it produces good outputs. See [Evaluating skill output quality](<https://agentskills.io/skill-creation/evaluating-skills>) for how to set up test cases, grade results, and iterate.


</details>
