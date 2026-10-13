# 编写 Agent Skills 的最佳实践

强调以真实经验和项目产物形成指令，合理控制上下文、拆分职责、逐步加载，并用执行反馈调整细节。

- 来源：通用规范／Agent Skills
- 资料类型：存档文档
- 文档时间：官网现页更新：[2026-04-22](<https://agentskills.io/skill-creation/best-practices>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://agentskills.io/skill-creation/best-practices.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/通用规范/Agent Skills/编写 Agent Skills 的最佳实践.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

> ## 文档索引
> 在以下位置获取完整的文档索引： https://agentskills.io/llms.txt
> 在进一步探索之前使用此文件来发现所有可用页面。

# 技能创造者的最佳实践

> 如何编写范围明确并针对任务进行校准的技能。

## 从真正的专业知识开始

技能创建中的一个常见陷阱是要求 LLM 生成技能而不提供特定领域的上下文 - 仅依赖于 LLM 的一般培训知识。结果是模糊的、通用的过程（“适当处理错误”、“遵循身份验证的最佳实践”），而不是使技能有价值的特定 API 模式、边缘情况和项目约定。

有效的技能植根于真正的专业知识。关键是将特定领域的上下文输入到创建过程中。

### 摘自实践任务

与 agent 对话完成一项真实任务，并在此过程中提供上下文、更正和偏好。然后将可重用的模式提取为技能。注意：

* **有效的步骤** — 导致成功的一系列行动
* **您所做的更正** - 您引导agent方法的地方（例如，“使用库X而不是Y”，“检查边缘情况Z”）
* **Input/output 格式** — 数据输入和输出的样子
* **您提供的上下文** — agent 尚不知道的特定于项目的事实、约定或约束

### 从现有项目工件综合

当您拥有大量现有知识时，您可以将其输入LLM并要求其合成技能。根据团队的实际事件报告和操作手册综合的数据管道技能将胜过根据通用的“数据工程最佳实践”文章综合的数据管道技能，因为它捕获了*您的*架构、故障模式和恢复过程。关键是项目特定的材料，而不是通用的参考资料。

好的来源材料包括：

* 内部文档、操作手册和风格指南
* API 规范、架构和配置文件
* 代码审查意见和问题跟踪器（捕获反复出现的问题和审查者的期望）
* 版本控制历史记录，尤其是补丁和修复（通过实际更改揭示模式）
* 现实世界的失败案例及其解决方案

## 通过实际执行精益求精

技能的初稿通常需要完善。针对实际任务运行该技能，然后将结果（所有结果，而不仅仅是失败）反馈到创建过程中。问：什么引发了误报？错过了什么？可以砍什么？

即使单次执行然后修改也能显着提高质量，而复杂的领域通常会从多次执行中受益。



**提示：**

读取 agent 执行跟踪，而不仅仅是最终输出。如果 agent 在非生产性步骤上浪费时间，常见原因包括指令过于模糊（agent 在找到一种有效的方法之前尝试了多种方法）、不适用于当前任务的指令（agent 无论如何都会遵循它们），或者提供了太多没有明确默认值的选项。


有关更结构化的迭代方法（包括测试用例、断言和评分），请参阅[评估技能输出质量](<https://agentskills.io/skill-creation/evaluating-skills>)。

## 明智地利用环境

一旦技能激活，其完整的 `SKILL.md` 主体就会与对话历史记录、系统上下文和其他活动技能一起加载到 agent 的上下文窗口中。你技能中的每个标记都会与该窗口中的其他所有标记争夺 agent 的注意力。

### 添加agent缺少的内容，省略它知道的内容

重点关注 agent * 在没有您的技能的情况下 * 不会知道的内容：特定于项目的约定、特定于领域的过程、不明显的边缘情况以及要使用的特定工具或 API。您无需解释 PDF 是什么、HTTP 如何工作或数据库迁移的作用。

````markdown theme={null}
<!-- Too verbose — the agent already knows what PDFs are -->
## Extract PDF text

PDF (Portable Document Format) files are a common file format that contains
text, images, and other content. To extract text from a PDF, you'll need to
use a library. pdfplumber is recommended because it handles most cases well.

<!-- Better — jumps straight to what the agent wouldn't know on its own -->
## Extract PDF text

Use pdfplumber for text extraction. For scanned documents, fall back to
pdf2image with pytesseract.

```python
import pdfplumber

with pdfplumber.open("file.pdf") as pdf:
    text = pdf.pages[0].extract_text()
```
````

针对每一条内容询问自己：“如果没有这条指令，agent 会出错吗？”如果答案是否定的，那就剪掉它。如果您不确定，请测试一下。如果 agent 已经在没有该技能的情况下很好地处理了整个任务，则该技能可能不会增加价值。请参阅[评估技能输出质量](<https://agentskills.io/skill-creation/evaluating-skills>)了解如何系统地测试这一点。

### 设计连贯的单元

决定一项技能应该涵盖什么就像决定一个函数应该做什么：你希望它封装一个与其他技能很好地组合的连贯的工作单元。技能范围过于狭窄，迫使为一项任务加载多种技能，从而带来开销和指令冲突的风险。技能范围太广，很难精确激活。查询数据库和格式化结果的技能可能是一个连贯的单元，而还涵盖数据库管理的技能可能试图做太多事情。

### 追求适度的细节

过于全面的技能弊大于利——agent 很难提取相关内容，并且可能会追求由不适用于当前任务的指令触发的非生产性路径。带有工作示例的简洁、逐步指导往往胜过详尽的文档。当您发现自己涵盖了所有边缘情况时，请考虑 agent 自己的判断是否可以更好地处理大多数情况。

### 通过渐进式披露构建大型技能

[规范](<https://agentskills.io/specification#progressive-disclosure>)建议将`SKILL.md`保留在500行和5,000令牌下——这只是agent每次运行所需的核心指令。当某项技能确实需要更多内容时，请将详细的参考资料移至 `references/` 或类似目录中的单独文件中。

关键是告诉agent*何时*加载每个文件。 “如果 API 返回非 200 状态代码，则读取 `references/api-errors.md`”比一般的“请参阅参考资料/了解详细信息”更有用。这让 agent 按需加载上下文，而不是预先加载上下文，这就是 [ 渐进式披露 ](<https://agentskills.io/specification#progressive-disclosure>) 的设计原理。

## 校准控制

并非技能的每个部分都需要相同程度的规范性。将指令的具体性与任务的脆弱性相匹配。

### 将特异性与脆弱性相匹配

**当多种方法有效并且任务可以容忍变化时，给予agent自由**。对于灵活的指令，解释*为什么*比严格的指令更有效——理解指令背后目的的agent可以做出更好的依赖于上下文的决策。代码审查技能可以描述要寻找的内容，而无需规定确切的步骤：

```markdown theme={null}
## Code review process

1. Check all database queries for SQL injection (use parameterized queries)
2. Verify authentication checks on every endpoint
3. Look for race conditions in concurrent code paths
4. Confirm error messages don't leak internal details
```

**当操作脆弱、一致性很重要或必须遵循特定顺序时，必须是规范性的**：

````markdown theme={null}
## Database migration

Run exactly this sequence:

```bash
python scripts/migrate.py --verify --backup
```

Do not modify the command or add additional flags.
````

大多数技能都是混合的。独立校准每个部件。

### 提供默认值，而不是菜单

当多种工具或方法可以工作时，选择默认值并简要提及替代方案，而不是将它们作为相同的选项呈现。

````markdown theme={null}
<!-- Too many options -->
You can use pypdf, pdfplumber, PyMuPDF, or pdf2image...

<!-- Clear default with escape hatch -->
Use pdfplumber for text extraction:

```python
import pdfplumber
```

For scanned PDFs requiring OCR, use pdf2image with pytesseract instead.
````

### 优先采用程序而非声明

技能应该教agent *如何解决*一类问题，而不是*针对特定实例生成什么*。比较：

```markdown theme={null}
<!-- Specific answer — only useful for this exact task -->
Join the `orders` table to `customers` on `customer_id`, filter where
`region = 'EMEA'`, and sum the `amount` column.

<!-- Reusable method — works for any analytical query -->
1. Read the schema from `references/schema.yaml` to find relevant tables
2. Join tables using the `_id` foreign key convention
3. Apply any filters from the user's request as WHERE clauses
4. Aggregate numeric columns as needed and format as a markdown table
```

这并不意味着技能不能包含特定的细节 - 输出格式模板（请参阅[输出格式模板](<#输出格式模板>)）、“从不输出PII”之类的约束以及特定于工具的指令都是有价值的。要点是，即使个别细节是具体的，*方法*也应该进行概括。

## 有效指示的模式

这些是用于构建技能内容的可重用技术。并不是每项技能都需要全部——使用适合您任务的技能。

### 陷阱部分

许多技能中最有价值的内容是一系列陷阱——特定于环境的事实，违背了合理的假设。这些不是一般性的建议（“适当地处理错误”），而是对 agent 在没有被告知的情况下所犯的错误的具体更正：

```markdown theme={null}
## Gotchas

- The `users` table uses soft deletes. Queries must include
  `WHERE deleted_at IS NULL` or results will include deactivated accounts.
- The user ID is `user_id` in the database, `uid` in the auth service,
  and `accountId` in the billing API. All three refer to the same value.
- The `/health` endpoint returns 200 as long as the web server is running,
  even if the database connection is down. Use `/ready` to check full
  service health.
```

将陷阱保留在 `SKILL.md` 中，agent 在遇到这种情况之前会读取它们。如果您告诉 agent 何时加载，单独的参考文件就可以工作，但对于不明显的问题，agent 可能无法识别触发器。



**提示：**

当 agent 犯了一个你必须纠正的错误时，请将更正添加到陷阱部分。这是迭代提高技能的最直接方法之一（参见[通过实际执行进行精炼](<#通过实际执行精益求精>)）。


### 输出格式模板

当您需要 agent 生成特定格式的输出时，请提供模板。这比用散文描述格式更可靠，因为 agents 模式与具体结构匹配得很好。短模板可以内嵌在`SKILL.md`中；对于较长的模板或仅在某些情况下需要的模板，请将它们存储在 `assets/` 中并从 `SKILL.md` 引用它们，以便它们仅在需要时加载。

````markdown theme={null}
## Report structure

Use this template, adapting sections as needed for the specific analysis:

```markdown
# [Analysis Title]

## Executive summary
[One-paragraph overview of key findings]

## Key findings
- Finding 1 with supporting data
- Finding 2 with supporting data

## Recommendations
1. Specific actionable recommendation
2. Specific actionable recommendation
```
````

### 多步骤工作流程清单

明确的清单有助于 agent 跟踪进度并避免跳过步骤，特别是当步骤具有依赖性或验证门时。

```markdown theme={null}
## Form processing workflow

Progress:
- [ ] Step 1: Analyze the form (run `scripts/analyze_form.py`)
- [ ] Step 2: Create field mapping (edit `fields.json`)
- [ ] Step 3: Validate mapping (run `scripts/validate_fields.py`)
- [ ] Step 4: Fill the form (run `scripts/fill_form.py`)
- [ ] Step 5: Verify output (run `scripts/verify_output.py`)
```

### 验证循环

指示 agent 在继续之前验证其自己的工作。模式是：完成工作，运行验证器（脚本、参考清单或自检），修复任何问题，然后重复直到验证通过。

```markdown theme={null}
## Editing workflow

1. Make your edits
2. Run validation: `python scripts/validate.py output/`
3. If validation fails:
   - Review the error message
   - Fix the issues
   - Run validation again
4. Only proceed when validation passes
```

参考文档还可以充当“验证器”——指示 agent 在最终确定之前根据参考文件检查其工作。

### 计划-验证-执行

对于批量或破坏性操作，让 agent 以结构化格式创建中间计划，根据事实来源对其进行验证，然后才执行。

```markdown theme={null}
## PDF form filling

1. Extract form fields: `python scripts/analyze_form.py input.pdf` → `form_fields.json`
   (lists every field name, type, and whether it's required)
2. Create `field_values.json` mapping each field name to its intended value
3. Validate: `python scripts/validate_fields.py form_fields.json field_values.json`
   (checks that every field name exists in the form, types are compatible, and
   required fields aren't missing)
4. If validation fails, revise `field_values.json` and re-validate
5. Fill the form: `python scripts/fill_form.py input.pdf field_values.json output.pdf`
```

关键成分是步骤 3：一个验证脚本，用于根据事实来源 (`form_fields.json`) 检查计划 (`field_values.json`)。诸如“未找到字段‘签名\_日期’ - 可用字段：客户\_名称、订单\_总计、签名\_日期\_签名”之类的错误]为agent提供了足够的信息来进行自我更正。

### 捆绑可重用脚本

当[迭代技能](<https://agentskills.io/skill-creation/evaluating-skills>)时，比较agent跨测试用例的执行跟踪。如果您注意到 agent 每次运行都会独立地重新发明相同的逻辑 - 构建图表、解析特定格式、验证输出 - 这是一个信号，需要编写一次经过测试的脚本并将其捆绑在 `scripts/` 中。

有关设计和捆绑脚本的更多信息，请参阅[在技能中使用脚本](<https://agentskills.io/skill-creation/using-scripts>)。

## 后续步骤

一旦您掌握了工作技能，两个指南可以帮助您进一步完善它：

* **[评估技能输出质量](<https://agentskills.io/skill-creation/evaluating-skills>)** — 设置测试用例、对结果进行评分并系统地迭代。
* **[优化技能描述](<https://agentskills.io/skill-creation/optimizing-descriptions>)** - 测试并改进你的技能的`description`字段，以便它在正确的提示上触发。


## 原文对照

<details>
<summary>展开完整原文</summary>

> ## Documentation Index
> Fetch the complete documentation index at: https://agentskills.io/llms.txt
> Use this file to discover all available pages before exploring further.

# Best practices for skill creators

> How to write skills that are well-scoped and calibrated to the task.

## Start from real expertise

A common pitfall in skill creation is asking an LLM to generate a skill without providing domain-specific context — relying solely on the LLM's general training knowledge. The result is vague, generic procedures ("handle errors appropriately," "follow best practices for authentication") rather than the specific API patterns, edge cases, and project conventions that make a skill valuable.

Effective skills are grounded in real expertise. The key is feeding domain-specific context into the creation process.

### Extract from a hands-on task

Complete a real task in conversation with an agent, providing context, corrections, and preferences along the way. Then extract the reusable pattern into a skill. Pay attention to:

* **Steps that worked** — the sequence of actions that led to success
* **Corrections you made** — places where you steered the agent's approach (e.g., "use library X instead of Y," "check for edge case Z")
* **Input/output formats** — what the data looked like going in and coming out
* **Context you provided** — project-specific facts, conventions, or constraints the agent didn't already know

### Synthesize from existing project artifacts

When you have a body of existing knowledge, you can feed it into an LLM and ask it to synthesize a skill. A data-pipeline skill synthesized from your team's actual incident reports and runbooks will outperform one synthesized from a generic "data engineering best practices" article, because it captures *your* schemas, failure modes, and recovery procedures. The key is project-specific material, not generic references.

Good source material includes:

* Internal documentation, runbooks, and style guides
* API specifications, schemas, and configuration files
* Code review comments and issue trackers (captures recurring concerns and reviewer expectations)
* Version control history, especially patches and fixes (reveals patterns through what actually changed)
* Real-world failure cases and their resolutions

## Refine with real execution

The first draft of a skill usually needs refinement. Run the skill against real tasks, then feed the results — all of them, not just failures — back into the creation process. Ask: what triggered false positives? What was missed? What could be cut?

Even a single pass of execute-then-revise noticeably improves quality, and complex domains often benefit from several.



**提示：**

  Read agent execution traces, not just final outputs. If the agent wastes time on unproductive steps, common causes include instructions that are too vague (the agent tries several approaches before finding one that works), instructions that don't apply to the current task (the agent follows them anyway), or too many options presented without a clear default.


For a more structured approach to iteration, including test cases, assertions, and grading, see [Evaluating skill output quality](<https://agentskills.io/skill-creation/evaluating-skills>).

## Spending context wisely

Once a skill activates, its full `SKILL.md` body loads into the agent's context window alongside conversation history, system context, and other active skills. Every token in your skill competes for the agent's attention with everything else in that window.

### Add what the agent lacks, omit what it knows

Focus on what the agent *wouldn't* know without your skill: project-specific conventions, domain-specific procedures, non-obvious edge cases, and the particular tools or APIs to use. You don't need to explain what a PDF is, how HTTP works, or what a database migration does.

````markdown theme={null}
<!-- Too verbose — the agent already knows what PDFs are -->
## Extract PDF text

PDF (Portable Document Format) files are a common file format that contains
text, images, and other content. To extract text from a PDF, you'll need to
use a library. pdfplumber is recommended because it handles most cases well.

<!-- Better — jumps straight to what the agent wouldn't know on its own -->
## Extract PDF text

Use pdfplumber for text extraction. For scanned documents, fall back to
pdf2image with pytesseract.

```python
import pdfplumber

with pdfplumber.open("file.pdf") as pdf:
    text = pdf.pages[0].extract_text()
```
````

Ask yourself about each piece of content: "Would the agent get this wrong without this instruction?" If the answer is no, cut it. If you're unsure, test it. And if the agent already handles the entire task well without the skill, the skill may not be adding value. See [Evaluating skill output quality](<https://agentskills.io/skill-creation/evaluating-skills>) for how to test this systematically.

### Design coherent units

Deciding what a skill should cover is like deciding what a function should do: you want it to encapsulate a coherent unit of work that composes well with other skills. Skills scoped too narrowly force multiple skills to load for a single task, risking overhead and conflicting instructions. Skills scoped too broadly become hard to activate precisely. A skill for querying a database and formatting the results may be one coherent unit, while a skill that also covers database administration is probably trying to do too much.

### Aim for moderate detail

Overly comprehensive skills can hurt more than they help — the agent struggles to extract what's relevant and may pursue unproductive paths triggered by instructions that don't apply to the current task. Concise, stepwise guidance with a working example tends to outperform exhaustive documentation. When you find yourself covering every edge case, consider whether most are better handled by the agent's own judgment.

### Structure large skills with progressive disclosure

The [specification](<https://agentskills.io/specification#progressive-disclosure>) recommends keeping `SKILL.md` under 500 lines and 5,000 tokens — just the core instructions the agent needs on every run. When a skill legitimately needs more content, move detailed reference material to separate files in `references/` or similar directories.

The key is telling the agent *when* to load each file. "Read `references/api-errors.md` if the API returns a non-200 status code" is more useful than a generic "see references/ for details." This lets the agent load context on demand rather than up front, which is how [progressive disclosure](<https://agentskills.io/specification#progressive-disclosure>) is designed to work.

## Calibrating control

Not every part of a skill needs the same level of prescriptiveness. Match the specificity of your instructions to the fragility of the task.

### Match specificity to fragility

**Give the agent freedom** when multiple approaches are valid and the task tolerates variation. For flexible instructions, explaining *why* can be more effective than rigid directives — an agent that understands the purpose behind an instruction makes better context-dependent decisions. A code review skill can describe what to look for without prescribing exact steps:

```markdown theme={null}
## Code review process

1. Check all database queries for SQL injection (use parameterized queries)
2. Verify authentication checks on every endpoint
3. Look for race conditions in concurrent code paths
4. Confirm error messages don't leak internal details
```

**Be prescriptive** when operations are fragile, consistency matters, or a specific sequence must be followed:

````markdown theme={null}
## Database migration

Run exactly this sequence:

```bash
python scripts/migrate.py --verify --backup
```

Do not modify the command or add additional flags.
````

Most skills have a mix. Calibrate each part independently.

### Provide defaults, not menus

When multiple tools or approaches could work, pick a default and mention alternatives briefly rather than presenting them as equal options.

````markdown theme={null}
<!-- Too many options -->
You can use pypdf, pdfplumber, PyMuPDF, or pdf2image...

<!-- Clear default with escape hatch -->
Use pdfplumber for text extraction:

```python
import pdfplumber
```

For scanned PDFs requiring OCR, use pdf2image with pytesseract instead.
````

### Favor procedures over declarations

A skill should teach the agent *how to approach* a class of problems, not *what to produce* for a specific instance. Compare:

```markdown theme={null}
<!-- Specific answer — only useful for this exact task -->
Join the `orders` table to `customers` on `customer_id`, filter where
`region = 'EMEA'`, and sum the `amount` column.

<!-- Reusable method — works for any analytical query -->
1. Read the schema from `references/schema.yaml` to find relevant tables
2. Join tables using the `_id` foreign key convention
3. Apply any filters from the user's request as WHERE clauses
4. Aggregate numeric columns as needed and format as a markdown table
```

This doesn't mean skills can't include specific details — output format templates (see [Templates for output format](<#templates-for-output-format>)), constraints like "never output PII," and tool-specific instructions are all valuable. The point is that the *approach* should generalize even when individual details are specific.

## Patterns for effective instructions

These are reusable techniques for structuring skill content. Not every skill needs all of them — use the ones that fit your task.

### Gotchas sections

The highest-value content in many skills is a list of gotchas — environment-specific facts that defy reasonable assumptions. These aren't general advice ("handle errors appropriately") but concrete corrections to mistakes the agent will make without being told otherwise:

```markdown theme={null}
## Gotchas

- The `users` table uses soft deletes. Queries must include
  `WHERE deleted_at IS NULL` or results will include deactivated accounts.
- The user ID is `user_id` in the database, `uid` in the auth service,
  and `accountId` in the billing API. All three refer to the same value.
- The `/health` endpoint returns 200 as long as the web server is running,
  even if the database connection is down. Use `/ready` to check full
  service health.
```

Keep gotchas in `SKILL.md` where the agent reads them before encountering the situation. A separate reference file works if you tell the agent when to load it, but for non-obvious issues, the agent may not recognize the trigger.



**提示：**

  When an agent makes a mistake you have to correct, add the correction to the gotchas section. This is one of the most direct ways to improve a skill iteratively (see [Refine with real execution](<#refine-with-real-execution>)).


### Templates for output format

When you need the agent to produce output in a specific format, provide a template. This is more reliable than describing the format in prose, because agents pattern-match well against concrete structures. Short templates can live inline in `SKILL.md`; for longer templates, or templates only needed in certain cases, store them in `assets/` and reference them from `SKILL.md` so they only load when needed.

````markdown theme={null}
## Report structure

Use this template, adapting sections as needed for the specific analysis:

```markdown
# [Analysis Title]

## Executive summary
[One-paragraph overview of key findings]

## Key findings
- Finding 1 with supporting data
- Finding 2 with supporting data

## Recommendations
1. Specific actionable recommendation
2. Specific actionable recommendation
```
````

### Checklists for multi-step workflows

An explicit checklist helps the agent track progress and avoid skipping steps, especially when steps have dependencies or validation gates.

```markdown theme={null}
## Form processing workflow

Progress:
- [ ] Step 1: Analyze the form (run `scripts/analyze_form.py`)
- [ ] Step 2: Create field mapping (edit `fields.json`)
- [ ] Step 3: Validate mapping (run `scripts/validate_fields.py`)
- [ ] Step 4: Fill the form (run `scripts/fill_form.py`)
- [ ] Step 5: Verify output (run `scripts/verify_output.py`)
```

### Validation loops

Instruct the agent to validate its own work before moving on. The pattern is: do the work, run a validator (a script, a reference checklist, or a self-check), fix any issues, and repeat until validation passes.

```markdown theme={null}
## Editing workflow

1. Make your edits
2. Run validation: `python scripts/validate.py output/`
3. If validation fails:
   - Review the error message
   - Fix the issues
   - Run validation again
4. Only proceed when validation passes
```

A reference document can also serve as the "validator" — instruct the agent to check its work against the reference before finalizing.

### Plan-validate-execute

For batch or destructive operations, have the agent create an intermediate plan in a structured format, validate it against a source of truth, and only then execute.

```markdown theme={null}
## PDF form filling

1. Extract form fields: `python scripts/analyze_form.py input.pdf` → `form_fields.json`
   (lists every field name, type, and whether it's required)
2. Create `field_values.json` mapping each field name to its intended value
3. Validate: `python scripts/validate_fields.py form_fields.json field_values.json`
   (checks that every field name exists in the form, types are compatible, and
   required fields aren't missing)
4. If validation fails, revise `field_values.json` and re-validate
5. Fill the form: `python scripts/fill_form.py input.pdf field_values.json output.pdf`
```

The key ingredient is step 3: a validation script that checks the plan (`field_values.json`) against the source of truth (`form_fields.json`). Errors like "Field 'signature\_date' not found — available fields: customer\_name, order\_total, signature\_date\_signed" give the agent enough information to self-correct.

### Bundling reusable scripts

When [iterating on a skill](<https://agentskills.io/skill-creation/evaluating-skills>), compare the agent's execution traces across test cases. If you notice the agent independently reinventing the same logic each run — building charts, parsing a specific format, validating output — that's a signal to write a tested script once and bundle it in `scripts/`.

For more on designing and bundling scripts, see [Using scripts in skills](<https://agentskills.io/skill-creation/using-scripts>).

## Next steps

Once you have a working skill, two guides can help you refine it further:

* **[Evaluating skill output quality](<https://agentskills.io/skill-creation/evaluating-skills>)** — Set up test cases, grade results, and iterate systematically.
* **[Optimizing skill descriptions](<https://agentskills.io/skill-creation/optimizing-descriptions>)** — Test and improve your skill's `description` field so it triggers on the right prompts.


</details>
