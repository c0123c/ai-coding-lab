# 评测 Agent Skills 的输出质量

介绍设计测试、比较有无技能、记录时长、断言评分、汇总模式和人工反馈，形成技能改进的迭代流程。

- 来源：通用规范／Agent Skills
- 资料类型：存档文档
- 文档时间：官网现页更新：[2026-03-16](<https://agentskills.io/skill-creation/evaluating-skills>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://agentskills.io/skill-creation/evaluating-skills.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/通用规范/Agent Skills/评测 Agent Skills 的输出质量.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

> ## 文档索引
> 在以下位置获取完整的文档索引： https://agentskills.io/llms.txt
> 在进一步探索之前使用此文件来发现所有可用页面。

# 评估技能输出质量

> 如何使用评估驱动的迭代来测试您的技能是否产生良好的输出。

您编写了一项技能，在提示下尝试了它，它似乎有效。但它是否可靠地工作——在不同的提示下，在极端情况下，比完全没有技能要好？运行结构化评估 (evals) 可以回答这些问题，并为您提供系统性提高技能的反馈循环。

## 设计测试用例

一个测试用例由三部分组成：

* **提示**：一条真实的用户消息——某人实际会输入的那种内容。
* **预期输出**：人类可读的成功描述。
* **输入文件**（可选）：技能需要使用的文件。

将测试用例存储在技能目录中的 `evals/evals.json` 中：

```json evals/evals.json theme={null}
{
  "skill_name": "csv-analyzer",
  "evals": [
    {
      "id": 1,
      "prompt": "I have a CSV of monthly sales data in data/sales_2025.csv. Can you find the top 3 months by revenue and make a bar chart?",
      "expected_output": "A bar chart image showing the top 3 months by revenue, with labeled axes and values.",
      "files": ["evals/files/sales_2025.csv"]
    },
    {
      "id": 2,
      "prompt": "there's a csv in my downloads called customers.csv, some rows have missing emails — can you clean it up and tell me how many were missing?",
      "expected_output": "A cleaned CSV with missing emails handled, plus a count of how many were missing.",
      "files": ["evals/files/customers.csv"]
    }
  ]
}
```

**编写良好测试提示的技巧：**

* **从2-3测试用例开始。**在看到第一轮结果之前不要过度投资。您可以稍后扩展该集合。
* **改变提示。**使用不同的措辞、详细程度和形式。有些提示应该是随意的（“嘿，你能清理这个 csv 吗”），其他提示应该是精确的（“在 data/input.csv 处解析 CSV，删除 B 列为空的行，并将结果写入 data/output.csv”）。
* **涵盖边缘情况。**至少包含一个测试边界条件的提示 - 格式错误的输入、异常请求或技能指令可能不明确的情况。
* **使用真实的上下文。**真实的用户提及文件路径、列名和个人上下文。像“处理此数据”这样的提示太模糊，无法测试任何有用的东西。

不用担心定义特定的 pass/fail 检查 - 只需提示和预期输出。在看到第一次运行产生的结果后，您将添加详细的检查（称为断言）。

## 运行评估

核心模式是运行每个测试用例两次：一次**使用技能**，一次**不使用技能**（或使用以前的版本）。这为您提供了比较的基准。

### 工作区结构

将评估结果与技能目录一起组织在工作区目录中。每次通过完整的 eval 循环都会获得自己的 `iteration-N/` 目录。其中，每个测试用例都会获得一个带有 `with_skill/` 和 `without_skill/` 子目录的 eval 目录：

```
csv-analyzer/
├── SKILL.md
└── evals/
    └── evals.json
csv-analyzer-workspace/
└── iteration-1/
    ├── eval-top-months-chart/
    │   ├── with_skill/
    │   │   ├── outputs/       # Files produced by the run
    │   │   ├── timing.json    # Tokens and duration
    │   │   └── grading.json   # Assertion results
    │   └── without_skill/
    │       ├── outputs/
    │       ├── timing.json
    │       └── grading.json
    ├── eval-clean-missing-emails/
    │   ├── with_skill/
    │   │   ├── outputs/
    │   │   ├── timing.json
    │   │   └── grading.json
    │   └── without_skill/
    │       ├── outputs/
    │       ├── timing.json
    │       └── grading.json
    └── benchmark.json         # Aggregated statistics
```

您手工编写的主文件是`evals/evals.json`。其他 JSON 文件（`grading.json`、`timing.json`、`benchmark.json`）是在评估过程中生成的 — 由 agent、脚本或您生成。

### 产卵奔跑

每次评估运行都应该从一个干净的上下文开始——没有先前运行或技能开发过程中留下的状态。这确保了 agent 只遵循 `SKILL.md` 告诉它的内容。在支持子代理的环境中（例如，Claude Code），这种隔离是自然而然的：每个子任务都是全新开始的。如果没有子代理，则每次运行都使用单独的会话。

对于每次运行，请提供：

* 技能路径（或基线无技能）
* 测试提示
* 任何输入文件
* 输出目录

以下是您为单次技能运行向 agent 发出的指令示例：

```
Execute this task:
- Skill path: /path/to/csv-analyzer
- Task: I have a CSV of monthly sales data in data/sales_2025.csv.
  Can you find the top 3 months by revenue and make a bar chart?
- Input files: evals/files/sales_2025.csv
- Save outputs to: csv-analyzer-workspace/iteration-1/eval-top-months-chart/with_skill/outputs/
```

对于基线，使用相同的提示但没有技能路径，保存到 `without_skill/outputs/`。

提高现有技能时，请使用以前的版本作为基准。在编辑之前拍摄快照 (`cp -r <skill-path> <workspace>/skill-snapshot/`)，将基线运行指向快照，然后保存到 `old_skill/outputs/` 而不是 `without_skill/`。

### 捕获计时数据

计时数据可让您比较该技能相对于基线所花费的时间和代币数量——一项可以显着提高输出质量但代币使用量增加三倍的技能与更好且更便宜的技能是不同的权衡。每次运行完成后，记录令牌计数和持续时间：

```json timing.json theme={null}
{
  "total_tokens": 84852,
  "duration_ms": 23332
}
```



**提示：**

在Claude Code中，当子代理任务完成时，[任务完成通知](<https://platform.claude.com/docs/en/agent-sdk/typescript#sdk-task-notification-message>)包括`total_tokens`和`duration_ms`。立即保存这些值——它们不会保留在其他地方。


## 编写断言

断言是关于输出应包含或实现的内容的可验证陈述。在看到第一轮输出后添加它们 - 在技能运行之前，您通常不知道“好”是什么样子。

好的断言：

* `"The output file is valid JSON"` — 可通过编程验证。
* `"The bar chart has labeled axes"` — 具体且可观察。
* `"The report includes at least 3 recommendations"` — 可数。

弱断言：

* `"The output is good"` — 太模糊，无法评分。
* `"The output uses exactly the phrase 'Total Revenue: $X'"` — 太脆；使用不同措辞的正确输出将失败。

并非所有事情都需要断言。有些品质——写作风格、视觉设计、输出是否“感觉正确”——很难分解为 pass/fail 检查。这些可以在[人工审核](<#与人一起审查结果>)期间更好地捕获。为可以客观检查的事物保留断言。

将断言添加到`evals/evals.json`中的每个测试用例：

```json evals/evals.json highlight={9-14} theme={null}
{
  "skill_name": "csv-analyzer",
  "evals": [
    {
      "id": 1,
      "prompt": "I have a CSV of monthly sales data in data/sales_2025.csv. Can you find the top 3 months by revenue and make a bar chart?",
      "expected_output": "A bar chart image showing the top 3 months by revenue, with labeled axes and values.",
      "files": ["evals/files/sales_2025.csv"],
      "assertions": [
        "The output includes a bar chart image file",
        "The chart shows exactly 3 months",
        "Both axes are labeled",
        "The chart title or caption mentions revenue"
      ]
    }
  ]
}
```

## 对输出进行分级

分级意味着根据实际输出评估每个断言，并用具体证据记录**PASS**或**FAIL**。证据应该引用或引用输出，而不仅仅是陈述意见。

最简单的方法是将输出和断言提供给 LLM 并要求它评估每一个。对于可以通过代码检查的断言（有效的 JSON、正确的行数、文件存在且具有预期的尺寸），请使用验证脚本 — 脚本比机械检查的 LLM 判断更可靠，并且可在迭代中重复使用。

```json grading.json theme={null}
{
  "assertion_results": [
    {
      "text": "The output includes a bar chart image file",
      "passed": true,
      "evidence": "Found chart.png (45KB) in outputs directory"
    },
    {
      "text": "The chart shows exactly 3 months",
      "passed": true,
      "evidence": "Chart displays bars for March, July, and November"
    },
    {
      "text": "Both axes are labeled",
      "passed": false,
      "evidence": "Y-axis is labeled 'Revenue ($)' but X-axis has no label"
    },
    {
      "text": "The chart title or caption mentions revenue",
      "passed": true,
      "evidence": "Chart title reads 'Top 3 Months by Revenue'"
    }
  ],
  "summary": {
    "passed": 3,
    "failed": 1,
    "total": 4,
    "pass_rate": 0.75
  }
}
```

### 评分原则

* **需要PASS的具体证据。**不要轻易相信。如果一个断言说“包括摘要”，并且输出有一个标题为“摘要”的部分，其中有一个模糊的句子，那么这是一个 FAIL - 标签在那里，但实质内容不在那里。
* **审查断言本身，而不仅仅是结果。**在评分时，请注意断言何时太简单（无论技能质量如何总是通过）、太难（即使输出良好也总是失败）或无法验证（无法仅从输出中检查）。为下一次迭代修复这些问题。



**提示：**

要比较两个技能版本，请尝试**盲比较**：将两个输出呈现给LLM法官，而不透露哪个来自哪个版本。评委根据自己的评分标准对整体质量——组织、格式、可用性、润色——进行评分，不存在对哪个版本“应该”更好的偏见。这补充了断言分级：两个输出可能都通过所有断言，但总体质量存在显着差异。


## 汇总结果

对迭代中的每次运行进行评分后，计算每个配置的摘要统计信息并将其保存到 `benchmark.json` 以及 eval 目录（例如，`csv-analyzer-workspace/iteration-1/benchmark.json`）：

```json benchmark.json theme={null}
{
  "run_summary": {
    "with_skill": {
      "pass_rate": { "mean": 0.83, "stddev": 0.06 },
      "time_seconds": { "mean": 45.0, "stddev": 12.0 },
      "tokens": { "mean": 3800, "stddev": 400 }
    },
    "without_skill": {
      "pass_rate": { "mean": 0.33, "stddev": 0.10 },
      "time_seconds": { "mean": 32.0, "stddev": 8.0 },
      "tokens": { "mean": 2100, "stddev": 300 }
    },
    "delta": {
      "pass_rate": 0.50,
      "time_seconds": 13.0,
      "tokens": 1700
    }
  }
}
```

`delta` 告诉您该技能的成本（更多时间，更多代币）以及它购买什么（更高的通过率）。增加 13 秒但通过率提高 50 个百分点的技能可能是值得的。使令牌使用量加倍而获得 2 点改进的技能可能不会。



**注释：**

标准差 (`stddev`) 仅在每次评估多次运行时才有意义。在仅使用 2-3 测试用例和单次运行的早期迭代中，重点关注原始通过计数和增量 - 当您扩展测试集并多次运行每个评估时，统计度量变得有用。


## 分析模式

聚合统计数据可以隐藏重要的模式。计算基准后：

* **删除或替换始终在两种配置中传递的断言。**这些不会告诉您任何有用的信息 - 模型无需技巧即可很好地处理它们。他们夸大了技能通过率，而没有反映实际技能价值。
* **调查在两种配置中总是失败的断言。**要么断言被破坏（要求模型不能做的事情），测试用例太难，要么断言正在检查错误的事情。在下一次迭代之前修复这些问题。
* **研究那些通过该技能而失败的断言。**这就是该技能明显增加价值的地方。了解 * 为什么 * — 哪些指令或脚本产生了差异？
* **当结果在运行中不一致时收紧指令。**如果相同的评估有时通过而其他则失败（在基准测试中反映为高`stddev`），则评估可能会不稳定（对模型随机性敏感），或者技能的指令可能足够模糊，以至于模型每次都会以不同的方式解释它们。添加示例或更具体的指导以减少歧义。
* **检查时间和令牌异常值。**如果一个评估花费的时间3x比其他评估更长，请读取其执行记录（模型在运行期间执行的完整日志）以查找瓶颈。

## 与人一起审查结果

断言分级和模式分析很吸引人，但它们只检查您认为要编写断言的内容。人工审阅者带来了全新的视角 - 发现您没有预料到的问题，注意到输出在技术上正确但没有抓住要点，或者发现难以用 pass/fail 检查表达的问题。对于每个测试用例，请检查实际输出和成绩。

记录每个测试用例的具体反馈并将其保存在工作区中（例如，作为 `feedback.json` 与 eval 目录一起）：

```json feedback.json theme={null}
{
  "eval-top-months-chart": "The chart is missing axis labels and the months are in alphabetical order instead of chronological.",
  "eval-clean-missing-emails": ""
}
```

“图表缺少轴标签”是可操作的； “看起来很糟糕”不是。空反馈意味着输出看起来不错——该测试用例通过了您的审查。在 [ 迭代步骤 ](<#迭代技能>) 期间，将改进重点放在您有特定投诉的测试用例上。

## 迭代技能

经过评分和审核后，您将获得三个信号源：

* **失败的断言**指向特定的差距——缺少的步骤、不明确的指令或技能无法处理的情况。
* **人类反馈**指出了更广泛的质量问题——方法错误，输出结构不良，或者技能产生了技术上正确但无用的结果。
* **执行记录**揭示*为什么*出了问题。如果agent忽略了一条指令，则该指令可能不明确。如果 agent 将时间花在无成效的步骤上，则可能需要简化或删除这些说明。

将这些信号转化为技能改进的最有效方法是将所有三个信号以及当前的 `SKILL.md` 提供给 LLM 并要求其提出更改建议。 LLM 可以综合失败的断言、审阅者投诉和转录行为的模式，而手动连接这些模式会很繁琐。当提示 LLM 时，请包含以下准则：

* **从反馈中归纳。**该技能将在许多不同的提示中使用，而不仅仅是测试用例。修复应该广泛地解决根本问题，而不是针对特定示例添加狭窄的补丁。
* **保持技能精益。**更少、更好的指令通常胜过详尽的规则。如果成绩单显示浪费的工作（不必要的验证、不需要的中间输出），请删除这些说明。如果尽管添加了更多规则，但通过率仍保持稳定，则该技能可能受到过度限制 - 尝试删除说明，看看结果是否保持不变或有所改善。
* **解释原因。** 基于推理的指令（“做 X，因为 Y 往往会导致 Z”）比严格的指令（“ALWAYS 做 X，NEVER 做 Y”）效果更好。当模型理解目的时，他们会更可靠地遵循指令。
* **捆绑重复的工作。**如果每个测试运行都独立编写一个类似的帮助程序脚本（图表构建器、数据解析器），则这是将脚本捆绑到技能的`scripts/`目录中的信号。请参阅[使用脚本](<https://agentskills.io/skill-creation/using-scripts>)了解如何执行此操作。

### 循环

1. 将评估信号和电流 `SKILL.md` 提供给 LLM 并要求其提出改进建议。
2. 检查并应用更改。
3. 在新的 `iteration-<N+1>/` 目录中重新运行所有测试用例。
4. 对新结果进行评分和汇总。
5. 与人一起审查。重复。

当您对结果感到满意、反馈始终为空或者您在迭代之间不再看到有意义的改进时，请停止。



**提示：**

[`skill-creator`](<https://github.com/anthropics/skills/tree/main/skills/skill-creator>) 技能可自动执行大部分工作流程 — 运行评估、对断言进行分级、聚合基准测试以及呈现结果以供人工审核。


## 原文对照

<details>
<summary>展开完整原文</summary>

> ## Documentation Index
> Fetch the complete documentation index at: https://agentskills.io/llms.txt
> Use this file to discover all available pages before exploring further.

# Evaluating skill output quality

> How to test whether your skill produces good outputs using eval-driven iteration.

You wrote a skill, tried it on a prompt, and it seemed to work. But does it work reliably — across varied prompts, in edge cases, better than no skill at all? Running structured evaluations (evals) answers these questions and gives you a feedback loop for improving the skill systematically.

## Designing test cases

A test case has three parts:

* **Prompt**: a realistic user message — the kind of thing someone would actually type.
* **Expected output**: a human-readable description of what success looks like.
* **Input files** (optional): files the skill needs to work with.

Store test cases in `evals/evals.json` inside your skill directory:

```json evals/evals.json theme={null}
{
  "skill_name": "csv-analyzer",
  "evals": [
    {
      "id": 1,
      "prompt": "I have a CSV of monthly sales data in data/sales_2025.csv. Can you find the top 3 months by revenue and make a bar chart?",
      "expected_output": "A bar chart image showing the top 3 months by revenue, with labeled axes and values.",
      "files": ["evals/files/sales_2025.csv"]
    },
    {
      "id": 2,
      "prompt": "there's a csv in my downloads called customers.csv, some rows have missing emails — can you clean it up and tell me how many were missing?",
      "expected_output": "A cleaned CSV with missing emails handled, plus a count of how many were missing.",
      "files": ["evals/files/customers.csv"]
    }
  ]
}
```

**Tips for writing good test prompts:**

* **Start with 2-3 test cases.** Don't over-invest before you've seen your first round of results. You can expand the set later.
* **Vary the prompts.** Use different phrasings, levels of detail, and formality. Some prompts should be casual ("hey can you clean up this csv"), others precise ("Parse the CSV at data/input.csv, drop rows where column B is null, and write the result to data/output.csv").
* **Cover edge cases.** Include at least one prompt that tests a boundary condition — a malformed input, an unusual request, or a case where the skill's instructions might be ambiguous.
* **Use realistic context.** Real users mention file paths, column names, and personal context. Prompts like "process this data" are too vague to test anything useful.

Don't worry about defining specific pass/fail checks yet — just the prompts and expected outputs. You'll add detailed checks (called assertions) after you see what the first run produces.

## Running evals

The core pattern is to run each test case twice: once **with the skill** and once **without it** (or with a previous version). This gives you a baseline to compare against.

### Workspace structure

Organize eval results in a workspace directory alongside your skill directory. Each pass through the full eval loop gets its own `iteration-N/` directory. Within that, each test case gets an eval directory with `with_skill/` and `without_skill/` subdirectories:

```
csv-analyzer/
├── SKILL.md
└── evals/
    └── evals.json
csv-analyzer-workspace/
└── iteration-1/
    ├── eval-top-months-chart/
    │   ├── with_skill/
    │   │   ├── outputs/       # Files produced by the run
    │   │   ├── timing.json    # Tokens and duration
    │   │   └── grading.json   # Assertion results
    │   └── without_skill/
    │       ├── outputs/
    │       ├── timing.json
    │       └── grading.json
    ├── eval-clean-missing-emails/
    │   ├── with_skill/
    │   │   ├── outputs/
    │   │   ├── timing.json
    │   │   └── grading.json
    │   └── without_skill/
    │       ├── outputs/
    │       ├── timing.json
    │       └── grading.json
    └── benchmark.json         # Aggregated statistics
```

The main file you author by hand is `evals/evals.json`. The other JSON files (`grading.json`, `timing.json`, `benchmark.json`) are produced during the eval process — by the agent, by scripts, or by you.

### Spawning runs

Each eval run should start with a clean context — no leftover state from previous runs or from the skill development process. This ensures the agent follows only what the `SKILL.md` tells it. In environments that support subagents (Claude Code, for example), this isolation comes naturally: each child task starts fresh. Without subagents, use a separate session for each run.

For each run, provide:

* The skill path (or no skill for the baseline)
* The test prompt
* Any input files
* The output directory

Here's an example of the instructions you'd give the agent for a single with-skill run:

```
Execute this task:
- Skill path: /path/to/csv-analyzer
- Task: I have a CSV of monthly sales data in data/sales_2025.csv.
  Can you find the top 3 months by revenue and make a bar chart?
- Input files: evals/files/sales_2025.csv
- Save outputs to: csv-analyzer-workspace/iteration-1/eval-top-months-chart/with_skill/outputs/
```

For the baseline, use the same prompt but without the skill path, saving to `without_skill/outputs/`.

When improving an existing skill, use the previous version as your baseline. Snapshot it before editing (`cp -r <skill-path> <workspace>/skill-snapshot/`), point the baseline run at the snapshot, and save to `old_skill/outputs/` instead of `without_skill/`.

### Capturing timing data

Timing data lets you compare how much time and tokens the skill costs relative to the baseline — a skill that dramatically improves output quality but triples token usage is a different trade-off than one that's both better and cheaper. When each run completes, record the token count and duration:

```json timing.json theme={null}
{
  "total_tokens": 84852,
  "duration_ms": 23332
}
```



**提示：**

  In Claude Code, when a subagent task finishes, the [task completion notification](<https://platform.claude.com/docs/en/agent-sdk/typescript#sdk-task-notification-message>) includes `total_tokens` and `duration_ms`. Save these values immediately — they aren't persisted anywhere else.


## Writing assertions

Assertions are verifiable statements about what the output should contain or achieve. Add them after you see your first round of outputs — you often don't know what "good" looks like until the skill has run.

Good assertions:

* `"The output file is valid JSON"` — programmatically verifiable.
* `"The bar chart has labeled axes"` — specific and observable.
* `"The report includes at least 3 recommendations"` — countable.

Weak assertions:

* `"The output is good"` — too vague to grade.
* `"The output uses exactly the phrase 'Total Revenue: $X'"` — too brittle; correct output with different wording would fail.

Not everything needs an assertion. Some qualities — writing style, visual design, whether the output "feels right" — are hard to decompose into pass/fail checks. These are better caught during [human review](<#reviewing-results-with-a-human>). Reserve assertions for things that can be checked objectively.

Add assertions to each test case in `evals/evals.json`:

```json evals/evals.json highlight={9-14} theme={null}
{
  "skill_name": "csv-analyzer",
  "evals": [
    {
      "id": 1,
      "prompt": "I have a CSV of monthly sales data in data/sales_2025.csv. Can you find the top 3 months by revenue and make a bar chart?",
      "expected_output": "A bar chart image showing the top 3 months by revenue, with labeled axes and values.",
      "files": ["evals/files/sales_2025.csv"],
      "assertions": [
        "The output includes a bar chart image file",
        "The chart shows exactly 3 months",
        "Both axes are labeled",
        "The chart title or caption mentions revenue"
      ]
    }
  ]
}
```

## Grading outputs

Grading means evaluating each assertion against the actual outputs and recording **PASS** or **FAIL** with specific evidence. The evidence should quote or reference the output, not just state an opinion.

The simplest approach is to give the outputs and assertions to an LLM and ask it to evaluate each one. For assertions that can be checked by code (valid JSON, correct row count, file exists with expected dimensions), use a verification script — scripts are more reliable than LLM judgment for mechanical checks and reusable across iterations.

```json grading.json theme={null}
{
  "assertion_results": [
    {
      "text": "The output includes a bar chart image file",
      "passed": true,
      "evidence": "Found chart.png (45KB) in outputs directory"
    },
    {
      "text": "The chart shows exactly 3 months",
      "passed": true,
      "evidence": "Chart displays bars for March, July, and November"
    },
    {
      "text": "Both axes are labeled",
      "passed": false,
      "evidence": "Y-axis is labeled 'Revenue ($)' but X-axis has no label"
    },
    {
      "text": "The chart title or caption mentions revenue",
      "passed": true,
      "evidence": "Chart title reads 'Top 3 Months by Revenue'"
    }
  ],
  "summary": {
    "passed": 3,
    "failed": 1,
    "total": 4,
    "pass_rate": 0.75
  }
}
```

### Grading principles

* **Require concrete evidence for a PASS.** Don't give the benefit of the doubt. If an assertion says "includes a summary" and the output has a section titled "Summary" with one vague sentence, that's a FAIL — the label is there but the substance isn't.
* **Review the assertions themselves, not just the results.** While grading, notice when assertions are too easy (always pass regardless of skill quality), too hard (always fail even when the output is good), or unverifiable (can't be checked from the output alone). Fix these for the next iteration.



**提示：**

  For comparing two skill versions, try **blind comparison**: present both outputs to an LLM judge without revealing which came from which version. The judge scores holistic qualities — organization, formatting, usability, polish — on its own rubric, free from bias about which version "should" be better. This complements assertion grading: two outputs might both pass all assertions but differ significantly in overall quality.


## Aggregating results

Once every run in the iteration is graded, compute summary statistics per configuration and save them to `benchmark.json` alongside the eval directories (e.g., `csv-analyzer-workspace/iteration-1/benchmark.json`):

```json benchmark.json theme={null}
{
  "run_summary": {
    "with_skill": {
      "pass_rate": { "mean": 0.83, "stddev": 0.06 },
      "time_seconds": { "mean": 45.0, "stddev": 12.0 },
      "tokens": { "mean": 3800, "stddev": 400 }
    },
    "without_skill": {
      "pass_rate": { "mean": 0.33, "stddev": 0.10 },
      "time_seconds": { "mean": 32.0, "stddev": 8.0 },
      "tokens": { "mean": 2100, "stddev": 300 }
    },
    "delta": {
      "pass_rate": 0.50,
      "time_seconds": 13.0,
      "tokens": 1700
    }
  }
}
```

The `delta` tells you what the skill costs (more time, more tokens) and what it buys (higher pass rate). A skill that adds 13 seconds but improves pass rate by 50 percentage points is probably worth it. A skill that doubles token usage for a 2-point improvement might not be.



**注释：**

  Standard deviation (`stddev`) is only meaningful with multiple runs per eval. In early iterations with just 2-3 test cases and single runs, focus on the raw pass counts and the delta — the statistical measures become useful as you expand the test set and run each eval multiple times.


## Analyzing patterns

Aggregate statistics can hide important patterns. After computing the benchmarks:

* **Remove or replace assertions that always pass in both configurations.** These don't tell you anything useful — the model handles them fine without the skill. They inflate the with-skill pass rate without reflecting actual skill value.
* **Investigate assertions that always fail in both configurations.** Either the assertion is broken (asking for something the model can't do), the test case is too hard, or the assertion is checking for the wrong thing. Fix these before the next iteration.
* **Study assertions that pass with the skill but fail without.** This is where the skill is clearly adding value. Understand *why* — which instructions or scripts made the difference?
* **Tighten instructions when results are inconsistent across runs.** If the same eval passes sometimes and fails others (reflected as high `stddev` in the benchmark), the eval may be flaky (sensitive to model randomness), or the skill's instructions may be ambiguous enough that the model interprets them differently each time. Add examples or more specific guidance to reduce ambiguity.
* **Check time and token outliers.** If one eval takes 3x longer than the others, read its execution transcript (the full log of what the model did during the run) to find the bottleneck.

## Reviewing results with a human

Assertion grading and pattern analysis catch a lot, but they only check what you thought to write assertions for. A human reviewer brings a fresh perspective — catching issues you didn't anticipate, noticing when the output is technically correct but misses the point, or spotting problems that are hard to express as pass/fail checks. For each test case, review the actual outputs alongside the grades.

Record specific feedback for each test case and save it in the workspace (e.g., as a `feedback.json` alongside the eval directories):

```json feedback.json theme={null}
{
  "eval-top-months-chart": "The chart is missing axis labels and the months are in alphabetical order instead of chronological.",
  "eval-clean-missing-emails": ""
}
```

"The chart is missing axis labels" is actionable; "looks bad" is not. Empty feedback means the output looked fine — that test case passed your review. During the [iteration step](<#iterating-on-the-skill>), focus your improvements on the test cases where you had specific complaints.

## Iterating on the skill

After grading and reviewing, you have three sources of signal:

* **Failed assertions** point to specific gaps — a missing step, an unclear instruction, or a case the skill doesn't handle.
* **Human feedback** points to broader quality issues — the approach was wrong, the output was poorly structured, or the skill produced a technically correct but unhelpful result.
* **Execution transcripts** reveal *why* things went wrong. If the agent ignored an instruction, the instruction may be ambiguous. If the agent spent time on unproductive steps, those instructions may need to be simplified or removed.

The most effective way to turn these signals into skill improvements is to give all three — along with the current `SKILL.md` — to an LLM and ask it to propose changes. The LLM can synthesize patterns across failed assertions, reviewer complaints, and transcript behavior that would be tedious to connect manually. When prompting the LLM, include these guidelines:

* **Generalize from feedback.** The skill will be used across many different prompts, not just the test cases. Fixes should address underlying issues broadly rather than adding narrow patches for specific examples.
* **Keep the skill lean.** Fewer, better instructions often outperform exhaustive rules. If transcripts show wasted work (unnecessary validation, unneeded intermediate outputs), remove those instructions. If pass rates plateau despite adding more rules, the skill may be over-constrained — try removing instructions and see if results hold or improve.
* **Explain the why.** Reasoning-based instructions ("Do X because Y tends to cause Z") work better than rigid directives ("ALWAYS do X, NEVER do Y"). Models follow instructions more reliably when they understand the purpose.
* **Bundle repeated work.** If every test run independently wrote a similar helper script (a chart builder, a data parser), that's a signal to bundle the script into the skill's `scripts/` directory. See [Using scripts](<https://agentskills.io/skill-creation/using-scripts>) for how to do this.

### The loop

1. Give the eval signals and current `SKILL.md` to an LLM and ask it to propose improvements.
2. Review and apply the changes.
3. Rerun all test cases in a new `iteration-<N+1>/` directory.
4. Grade and aggregate the new results.
5. Review with a human. Repeat.

Stop when you're satisfied with the results, feedback is consistently empty, or you're no longer seeing meaningful improvement between iterations.



**提示：**

  The [`skill-creator`](<https://github.com/anthropics/skills/tree/main/skills/skill-creator>) Skill automates much of this workflow — running evals, grading assertions, aggregating benchmarks, and presenting results for human review.


</details>
