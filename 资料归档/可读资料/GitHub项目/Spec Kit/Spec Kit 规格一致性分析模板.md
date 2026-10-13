# Spec Kit 规格一致性分析模板

对照 spec.md、plan.md 和 tasks.md 检查重复、歧义、遗漏与覆盖关系，形成问题报告；属于只读分析命令模板。

- 来源：Spec Kit
- 资料类型：存档文档
- 存档版本：`0443760542e7`。
- 文档时间：源码提交（存档版）：[2026-09-09](<https://github.com/github/spec-kit/commit/b60057692cd726ea56331ec47f9ebc3e8877d8f7>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/spec-kit/blob/main/templates/commands/analyze.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Spec Kit/Spec Kit 规格一致性分析模板.md>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
description: Perform a non-destructive cross-artifact consistency and quality analysis across spec.md, plan.md, and tasks.md after task generation.
scripts:
  sh: scripts/bash/check-prerequisites.sh --json --require-spec --require-tasks --include-tasks
  ps: scripts/powershell/check-prerequisites.ps1 -Json -RequireSpec -RequireTasks -IncludeTasks
  py: scripts/python/check_prerequisites.py --json --require-spec --require-tasks --include-tasks
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

## 用户输入

```text
$ARGUMENTS
```

您**MUST**在继续之前考虑用户输入（如果不为空）。

## 执行前检查

**检查延长钩（分析前）**：
- 检查项目根目录中是否存在`.specify/extensions.yml`。
- 如果存在，则读取它并查找 `hooks.before_analyze` 键下的条目
- 如果 YAML 无法解析或无效，请不要静默跳过：告诉用户无法读取 `.specify/extensions.yml`（包括解析器错误）并且没有检查钩子，包括在那里注册的任何强制 (`optional: false`) 钩子，然后正常继续
- 过滤掉 `enabled` 明确为 `false` 的钩子。默认情况下，将没有 `enabled` 字段的挂钩视为启用。
- 对于每个剩余的钩子，**不**尝试解释或评估钩子`condition`表达式：
  - 如果钩子没有 `condition` 字段，或者是 null/empty，则将钩子视为可执行文件
  - 如果挂钩定义非空 `condition`，则跳过挂钩并将条件评估留给 HookExecutor 实现
- 对于每个可执行挂钩，根据其 `optional` 标志输出以下内容：
  - **可选挂钩**（`optional: true`）：
    ```
    ## Extension Hooks

    **Optional Pre-Hook**: {extension}
    Command: `/{command}`
    Description: {description}

    Prompt: {prompt}
    To execute: `/{command}`
    ```
  - **强制挂钩**（`optional: false`）：
    ```
    ## Extension Hooks

    **Automatic Pre-Hook**: {extension}
    Executing: `/{command}`
    EXECUTE_COMMAND: {command}

    Wait for the result of the hook command before proceeding to the Goal.
    ```
在发出上面的块之后，您 MUST 实际上调用了钩子并等待它完成然后再继续。运行它的方式与您自己在此 agent/session 中运行命令的方式相同（调用可能与上面显示的文字 `{command}` id 不同，例如技能模式 agent 将其运行为 `/skill:speckit-...` 或 `$speckit-...`）。单独发出块不会运行钩子。
- 如果没有注册钩子或者`.specify/extensions.yml`不存在，则静默跳过

## 目标

在实施之前识别三个核心工件（`spec.md`、`plan.md`、`tasks.md`）中的不一致、重复、歧义和未指定的项目。此命令 MUST 仅在 `__SPECKIT_COMMAND_TASKS__` 成功生成完整的 `tasks.md` 后运行。

## 操作限制

**STRICTLY READ-ONLY**：不要****修改任何文件。输出结构化分析报告。提供可选的修复计划（用户必须明确批准才能手动调用任何后续编辑命令）。

**宪法权威**：项目宪法（`/memory/constitution.md`）在本分析范围内是**不可协商的**。宪法冲突会自动发生CRITICAL，并需要调整规范、计划或任务，而不是淡化、重新解释或沉默地忽视原则。如果原则本身需要改变，则必须在 `__SPECKIT_COMMAND_ANALYZE__` 之外进行单独的、明确的宪法更新。

## 执行步骤

### 1。初始化分析上下文

从存储库根运行 `{SCRIPT}` 一次，并解析 JSON 以获得 FEATURE_DIR 和 AVAILABLE_DOCS。导出绝对路径：

- SPEC = FEATURE_DIR/spec.md
- PLAN = FEATURE_DIR/plan.md
- TASKS = FEATURE_DIR/tasks.md

如果缺少任何必需的文件，则中止并显示错误消息（指示用户运行缺少的先决条件命令）。
对于参数中的单引号，例如“I'm Groot”，请使用转义语法：例如'I'\''m Groot'（如果可能，则使用双引号：“I'm Groot”）。

### 2。加载工件（渐进式披露）

仅从每个工件加载最少的必要上下文：

**来自spec.md：**

- Overview/Context
- 功能要求
- 成功标准（可衡量的结果——例如性能、安全性、可用性、用户成功、业务影响）
- 用户故事
- 边缘情况（如果存在）

**来自plan.md：**

- Architecture/stack选择
- 数据模型参考
- 阶段
- 技术限制

**来自tasks.md：**

- 任务 ID
- 描述
- 相分组
- 平行标记 [P]
- 引用的文件路径

**由构成：**

- 加载`/memory/constitution.md`进行原理验证

### 3。构建语义模型

创建内部表示（不包括输出中的原始工件）：

- **需求清单**：对于每个功能需求（FR-###）和成功标准（SC-###），记录一个稳定的密钥。使用显式的 FR-/SC- 标识符作为主键（如果存在），并且还可以选择派生命令式短语以提高可读性（例如，“用户可以上传文件”→ `user-can-upload-file`）。仅包括需要可构建工作的成功标准项目（例如，负载测试基础设施、安全审核工具），并排除启动后结果指标和业务 KPI（例如，“将支持票数减少 50%”）。
- **用户story/action库存**：具有接受标准的离散用户操作
- **任务覆盖范围映射**：将每项任务映射到一个或多个需求或故事（通过关键字/显式参考模式（如 ID 或关键短语）进行推断）
- **宪法规则集**：提取原则名称和MUST/SHOULD规范性陈述

### 4。检测通过（令牌有效分析）

关注高信号发现。限制为 50 调查结果总数；溢出摘要中的聚合余数。

#### A. 重复检测

- 识别几乎重复的需求
- 标记较低质量的措辞以进行合并

#### B. 歧义检测

- 标记缺乏可衡量标准的模糊形容词（快速、可扩展、安全、直观、稳健）
- 标记未解析的占位符（TODO、TKTK、???、`<placeholder>` 等）

#### C. 规格不足

- 要求有动词但缺少宾语或可衡量的结果
- 用户故事缺少验收标准一致性
- 引用 spec/plan 中未定义的文件或组件的任务

#### D. 宪法调整

- 与 MUST 原则相冲突的任何要求或计划要素
- 宪法中缺少规定的部分或质量关卡

#### E. 覆盖范围差距

- 零关联任务的需求
- 没有映射的任务 requirement/story
- 需要可构建工作的成功标准（性能、安全性、可用性）未反映在任务中

#### F. 不一致

- 术语漂移（同一概念在不同文件中的命名不同）
- 计划中引用但规范中不存在的数据实体（反之亦然）
- 任务顺序矛盾（例如，集成任务在基础设置任务之前，没有依赖项注释）
- 冲突的要求（例如，一个要求 Next.js，而另一个指定 Vue）

### 5。严重性分配

使用此启发式对发现进行优先排序：

- **CRITICAL**：违反宪法 MUST、缺少核心规范工件或零覆盖率要求阻碍基线功能
- **HIGH**：重复或冲突的要求，不明确的security/performance属性，无法测试的验收标准
- **MEDIUM**：术语漂移、缺少非功能性任务覆盖、未指定边缘情况
- **LOW**：Style/wording改进，轻微冗余不影响执行顺序

### 6。生成简洁的分析报告

输出一个 Markdown 报告（无文件写入），结构如下：

## 规格分析报告

| ID |类别|严重性|地点|总结|推荐|
|----|----------|----------|-------------|---------|----------------|
| A1 |重复| HIGH | spec.md:L120-134 |两个相似的需求...|合并措辞；保持更清晰的版本|

（每个发现添加一行；生成以类别首字母为前缀的稳定 ID。）

**覆盖范围汇总表：**

|要求要点|有任务吗？|任务 ID|注释|
|-----------------|-----------|----------|-------|

**宪法对齐问题：**（如果有）

**未映射的任务：**（如果有）

**指标：**

- 总要求
- 任务总数
- 覆盖率 %（>=1 任务的要求）
- 歧义计数
- 重复计数
- 关键问题很重要

### 7。提供下一步行动

在报告末尾，输出一个简洁的下一步操作块：

- 如果存在 CRITICAL 问题：建议在 `__SPECKIT_COMMAND_IMPLEMENT__` 之前解决
- 如果只有LOW/MEDIUM：用户可以继续，但提供改进建议
- 提供明确的命令建议：例如，“运行 __SPECKIT_COMMAND_SPECIFY__ 并进行细化”、“运行__SPECKIT_COMMAND_PLAN__ 调整架构”，“手动编辑 tasks.md 以添加“性能指标”的覆盖范围”

### 8。提供补救措施

询问用户：“您希望我针对前 N 个问题提出具体的修复编辑建议吗？” （NOT 自动应用它们。）

### 9。检查是否有延长钩

报告后，检查项目根目录中是否存在`.specify/extensions.yml`。
- 如果存在，则读取它并查找 `hooks.after_analyze` 键下的条目
- 如果 YAML 无法解析或无效，请不要静默跳过：告诉用户无法读取 `.specify/extensions.yml`（包括解析器错误）并且没有检查钩子，包括在那里注册的任何强制 (`optional: false`) 钩子，然后正常继续
- 过滤掉 `enabled` 明确为 `false` 的钩子。默认情况下，将没有 `enabled` 字段的挂钩视为启用。
- 对于每个剩余的钩子，**不**尝试解释或评估钩子`condition`表达式：
  - 如果钩子没有 `condition` 字段，或者是 null/empty，则将钩子视为可执行文件
  - 如果挂钩定义非空 `condition`，则跳过挂钩并将条件评估留给 HookExecutor 实现
- 对于每个可执行挂钩，根据其 `optional` 标志输出以下内容：
  - **可选挂钩**（`optional: true`）：
    ```
    ## Extension Hooks

    **Optional Hook**: {extension}
    Command: `/{command}`
    Description: {description}

    Prompt: {prompt}
    To execute: `/{command}`
    ```
  - **强制挂钩**（`optional: false`）：
    ```
    ## Extension Hooks

    **Automatic Hook**: {extension}
    Executing: `/{command}`
    EXECUTE_COMMAND: {command}
    ```
在发出上面的块之后，您 MUST 实际上调用了钩子并等待它完成然后再继续。运行它的方式与您自己在此 agent/session 中运行命令的方式相同（调用可能与上面显示的文字 `{command}` id 不同，例如技能模式 agent 将其运行为 `/skill:speckit-...` 或 `$speckit-...`）。单独发出块不会运行钩子。
- 如果没有注册钩子或者`.specify/extensions.yml`不存在，则静默跳过

## 工作原理

### 情境效率

- **最小高信号令牌**：关注可操作的发现，而不是详尽的文档
- **渐进式披露**：增量加载工件；不要将所有内容转储到分析中
- **令牌高效输出**：将结果表限制为50行；总结溢出
- **确定性结果**：不进行任何更改地重新运行应该会产生一致的 ID 和计数

### 分析指南

- **NEVER修改文件**（这是只读分析）
- **NEVER 幻觉缺失的部分**（如果缺失，请准确报告）
- **优先考虑违反宪法的行为**（这些始终是CRITICAL）
- **使用示例而不是详尽的规则**（引用特定实例，而不是通用模式）
- **优雅地报告零问题**（发出包含覆盖率统计数据的成功报告）

## 背景

{ARGS}


## 原文对照

<details>
<summary>展开完整原文</summary>

## User Input

```text
$ARGUMENTS
```

You **MUST** consider the user input before proceeding (if not empty).

## Pre-Execution Checks

**Check for extension hooks (before analysis)**:
- Check if `.specify/extensions.yml` exists in the project root.
- If it exists, read it and look for entries under the `hooks.before_analyze` key
- If the YAML cannot be parsed or is invalid, do not skip silently: tell the user that `.specify/extensions.yml` could not be read (include the parser error) and that no hooks were checked, including any mandatory (`optional: false`) hooks registered there, then continue normally
- Filter out hooks where `enabled` is explicitly `false`. Treat hooks without an `enabled` field as enabled by default.
- For each remaining hook, do **not** attempt to interpret or evaluate hook `condition` expressions:
  - If the hook has no `condition` field, or it is null/empty, treat the hook as executable
  - If the hook defines a non-empty `condition`, skip the hook and leave condition evaluation to the HookExecutor implementation
- For each executable hook, output the following based on its `optional` flag:
  - **Optional hook** (`optional: true`):
    ```
    ## Extension Hooks

    **Optional Pre-Hook**: {extension}
    Command: `/{command}`
    Description: {description}

    Prompt: {prompt}
    To execute: `/{command}`
    ```
  - **Mandatory hook** (`optional: false`):
    ```
    ## Extension Hooks

    **Automatic Pre-Hook**: {extension}
    Executing: `/{command}`
    EXECUTE_COMMAND: {command}

    Wait for the result of the hook command before proceeding to the Goal.
    ```
    After emitting the block above you MUST actually invoke the hook and wait for it to finish before continuing. Run it the same way you would run the command yourself in this agent/session (the invocation may differ from the literal `{command}` id shown above, e.g. a skills-mode agent runs it as `/skill:speckit-...` or `$speckit-...`). Emitting the block alone does not run the hook.
- If no hooks are registered or `.specify/extensions.yml` does not exist, skip silently

## Goal

Identify inconsistencies, duplications, ambiguities, and underspecified items across the three core artifacts (`spec.md`, `plan.md`, `tasks.md`) before implementation. This command MUST run only after `__SPECKIT_COMMAND_TASKS__` has successfully produced a complete `tasks.md`.

## Operating Constraints

**STRICTLY READ-ONLY**: Do **not** modify any files. Output a structured analysis report. Offer an optional remediation plan (user must explicitly approve before any follow-up editing commands would be invoked manually).

**Constitution Authority**: The project constitution (`/memory/constitution.md`) is **non-negotiable** within this analysis scope. Constitution conflicts are automatically CRITICAL and require adjustment of the spec, plan, or tasks—not dilution, reinterpretation, or silent ignoring of the principle. If a principle itself needs to change, that must occur in a separate, explicit constitution update outside `__SPECKIT_COMMAND_ANALYZE__`.

## Execution Steps

### 1. Initialize Analysis Context

Run `{SCRIPT}` once from repo root and parse JSON for FEATURE_DIR and AVAILABLE_DOCS. Derive absolute paths:

- SPEC = FEATURE_DIR/spec.md
- PLAN = FEATURE_DIR/plan.md
- TASKS = FEATURE_DIR/tasks.md

Abort with an error message if any required file is missing (instruct the user to run missing prerequisite command).
For single quotes in args like "I'm Groot", use escape syntax: e.g 'I'\''m Groot' (or double-quote if possible: "I'm Groot").

### 2. Load Artifacts (Progressive Disclosure)

Load only the minimal necessary context from each artifact:

**From spec.md:**

- Overview/Context
- Functional Requirements
- Success Criteria (measurable outcomes — e.g., performance, security, availability, user success, business impact)
- User Stories
- Edge Cases (if present)

**From plan.md:**

- Architecture/stack choices
- Data Model references
- Phases
- Technical constraints

**From tasks.md:**

- Task IDs
- Descriptions
- Phase grouping
- Parallel markers [P]
- Referenced file paths

**From constitution:**

- Load `/memory/constitution.md` for principle validation

### 3. Build Semantic Models

Create internal representations (do not include raw artifacts in output):

- **Requirements inventory**: For each Functional Requirement (FR-###) and Success Criterion (SC-###), record a stable key. Use the explicit FR-/SC- identifier as the primary key when present, and optionally also derive an imperative-phrase slug for readability (e.g., "User can upload file" → `user-can-upload-file`). Include only Success Criteria items that require buildable work (e.g., load-testing infrastructure, security audit tooling), and exclude post-launch outcome metrics and business KPIs (e.g., "Reduce support tickets by 50%").
- **User story/action inventory**: Discrete user actions with acceptance criteria
- **Task coverage mapping**: Map each task to one or more requirements or stories (inference by keyword / explicit reference patterns like IDs or key phrases)
- **Constitution rule set**: Extract principle names and MUST/SHOULD normative statements

### 4. Detection Passes (Token-Efficient Analysis)

Focus on high-signal findings. Limit to 50 findings total; aggregate remainder in overflow summary.

#### A. Duplication Detection

- Identify near-duplicate requirements
- Mark lower-quality phrasing for consolidation

#### B. Ambiguity Detection

- Flag vague adjectives (fast, scalable, secure, intuitive, robust) lacking measurable criteria
- Flag unresolved placeholders (TODO, TKTK, ???, `<placeholder>`, etc.)

#### C. Underspecification

- Requirements with verbs but missing object or measurable outcome
- User stories missing acceptance criteria alignment
- Tasks referencing files or components not defined in spec/plan

#### D. Constitution Alignment

- Any requirement or plan element conflicting with a MUST principle
- Missing mandated sections or quality gates from constitution

#### E. Coverage Gaps

- Requirements with zero associated tasks
- Tasks with no mapped requirement/story
- Success Criteria requiring buildable work (performance, security, availability) not reflected in tasks

#### F. Inconsistency

- Terminology drift (same concept named differently across files)
- Data entities referenced in plan but absent in spec (or vice versa)
- Task ordering contradictions (e.g., integration tasks before foundational setup tasks without dependency note)
- Conflicting requirements (e.g., one requires Next.js while other specifies Vue)

### 5. Severity Assignment

Use this heuristic to prioritize findings:

- **CRITICAL**: Violates constitution MUST, missing core spec artifact, or requirement with zero coverage that blocks baseline functionality
- **HIGH**: Duplicate or conflicting requirement, ambiguous security/performance attribute, untestable acceptance criterion
- **MEDIUM**: Terminology drift, missing non-functional task coverage, underspecified edge case
- **LOW**: Style/wording improvements, minor redundancy not affecting execution order

### 6. Produce Compact Analysis Report

Output a Markdown report (no file writes) with the following structure:

## Specification Analysis Report

| ID | Category | Severity | Location(s) | Summary | Recommendation |
|----|----------|----------|-------------|---------|----------------|
| A1 | Duplication | HIGH | spec.md:L120-134 | Two similar requirements ... | Merge phrasing; keep clearer version |

(Add one row per finding; generate stable IDs prefixed by category initial.)

**Coverage Summary Table:**

| Requirement Key | Has Task? | Task IDs | Notes |
|-----------------|-----------|----------|-------|

**Constitution Alignment Issues:** (if any)

**Unmapped Tasks:** (if any)

**Metrics:**

- Total Requirements
- Total Tasks
- Coverage % (requirements with >=1 task)
- Ambiguity Count
- Duplication Count
- Critical Issues Count

### 7. Provide Next Actions

At end of report, output a concise Next Actions block:

- If CRITICAL issues exist: Recommend resolving before `__SPECKIT_COMMAND_IMPLEMENT__`
- If only LOW/MEDIUM: User may proceed, but provide improvement suggestions
- Provide explicit command suggestions: e.g., "Run __SPECKIT_COMMAND_SPECIFY__ with refinement", "Run __SPECKIT_COMMAND_PLAN__ to adjust architecture", "Manually edit tasks.md to add coverage for 'performance-metrics'"

### 8. Offer Remediation

Ask the user: "Would you like me to suggest concrete remediation edits for the top N issues?" (Do NOT apply them automatically.)

### 9. Check for extension hooks

After reporting, check if `.specify/extensions.yml` exists in the project root.
- If it exists, read it and look for entries under the `hooks.after_analyze` key
- If the YAML cannot be parsed or is invalid, do not skip silently: tell the user that `.specify/extensions.yml` could not be read (include the parser error) and that no hooks were checked, including any mandatory (`optional: false`) hooks registered there, then continue normally
- Filter out hooks where `enabled` is explicitly `false`. Treat hooks without an `enabled` field as enabled by default.
- For each remaining hook, do **not** attempt to interpret or evaluate hook `condition` expressions:
  - If the hook has no `condition` field, or it is null/empty, treat the hook as executable
  - If the hook defines a non-empty `condition`, skip the hook and leave condition evaluation to the HookExecutor implementation
- For each executable hook, output the following based on its `optional` flag:
  - **Optional hook** (`optional: true`):
    ```
    ## Extension Hooks

    **Optional Hook**: {extension}
    Command: `/{command}`
    Description: {description}

    Prompt: {prompt}
    To execute: `/{command}`
    ```
  - **Mandatory hook** (`optional: false`):
    ```
    ## Extension Hooks

    **Automatic Hook**: {extension}
    Executing: `/{command}`
    EXECUTE_COMMAND: {command}
    ```
    After emitting the block above you MUST actually invoke the hook and wait for it to finish before continuing. Run it the same way you would run the command yourself in this agent/session (the invocation may differ from the literal `{command}` id shown above, e.g. a skills-mode agent runs it as `/skill:speckit-...` or `$speckit-...`). Emitting the block alone does not run the hook.
- If no hooks are registered or `.specify/extensions.yml` does not exist, skip silently

## Operating Principles

### Context Efficiency

- **Minimal high-signal tokens**: Focus on actionable findings, not exhaustive documentation
- **Progressive disclosure**: Load artifacts incrementally; don't dump all content into analysis
- **Token-efficient output**: Limit findings table to 50 rows; summarize overflow
- **Deterministic results**: Rerunning without changes should produce consistent IDs and counts

### Analysis Guidelines

- **NEVER modify files** (this is read-only analysis)
- **NEVER hallucinate missing sections** (if absent, report them accurately)
- **Prioritize constitution violations** (these are always CRITICAL)
- **Use examples over exhaustive rules** (cite specific instances, not generic patterns)
- **Report zero issues gracefully** (emit success report with coverage statistics)

## Context

{ARGS}


</details>
