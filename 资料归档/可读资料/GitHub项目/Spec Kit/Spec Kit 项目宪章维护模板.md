# Spec Kit 项目宪章维护模板

根据用户原则创建或更新项目宪章，并核对相关模板及版本关系；属于项目治理命令模板。

- 来源：Spec Kit
- 资料类型：存档文档
- 存档版本：`0443760542e7`。
- 文档时间：源码提交（存档版）：[2026-09-09](<https://github.com/github/spec-kit/commit/b60057692cd726ea56331ec47f9ebc3e8877d8f7>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/spec-kit/blob/main/templates/commands/constitution.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Spec Kit/Spec Kit 项目宪章维护模板.md>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
description: Create or update the project constitution from interactive or provided principle inputs.
handoffs:
  - label: Build Specification
    agent: speckit.specify
    prompt: Implement the feature specification based on the updated constitution. I want to build...
scripts:
  sh: scripts/bash/resolve-template.sh constitution-template --json
  ps: scripts/powershell/resolve-template.ps1 constitution-template -Json
  py: scripts/python/resolve_template.py constitution-template --json
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

## 瞄准镜守卫

该命令本身的工作仅限于更新项目章程本身。依赖模板
和命令在运行时读取构成，此处不进行修改。

- 将用户输入的每个部分分类为构成内容或单独的、
非治理意图。
- 如果输入包括功能实现、代码生成、重构、构建或
部署请求，您 **MUST NOT** 执行它们。相反，将它们提取为延迟意图。
- 您**MUSTNOT**创建、修改或删除应用程序源文件、功能路线、
组件、测试、部署文件或与构成
工作流程无关的其他工件。
- 如果不清楚指令是否属于宪法内容，请先要求澄清
做出改变。
- 完成宪法更新后，为每个延期包含一个 `Next Actions` 部分
意图。列出原始意图并建议适当的后续 Spec Kit 命令，例如 
 为 `__SPECKIT_COMMAND_SPECIFY__`，而不调用它。
- 如果没有非治理意图，请省略 `Next Actions` 部分。

## 执行前检查

**检查扩展钩子（在宪法更新之前）**：
- 检查项目根目录中是否存在`.specify/extensions.yml`。
- 如果存在，则读取它并查找 `hooks.before_constitution` 键下的条目
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

    Wait for the result of the hook command before proceeding to the Outline.
    ```
在发出上面的块之后，您 MUST 实际上调用了钩子并等待它完成然后再继续。运行它的方式与您自己在此 agent/session 中运行命令的方式相同（调用可能与上面显示的文字 `{command}` id 不同，例如技能模式 agent 将其运行为 `/skill:speckit-...` 或 `$speckit-...`）。单独发出块不会运行钩子。
- 如果没有注册钩子或者`.specify/extensions.yml`不存在，则静默跳过

## 概要

您正在`.specify/memory/constitution.md`更新项目章程。活动的 
 结构支架在命令时通过规格套件 
preset/template 解析堆栈从 `constitution-template` 解析。

按照这个执行流程：

1. 从存储库根运行 `{SCRIPT}` 并将 `TEMPLATE_CONTENT` 解析为活动模板。
   - 共享解析器应用项目覆盖、组合预设图层和扩展图层
在核心模板回退之前。在继续之前它MUST成功。
   - 如果失败，则停止并报告解析错误；不要只继续一个人的贡献
模板层。
   - 如果`.specify/memory/constitution.md`存在，则将其加载为当前项目特定的源
值和修正。保留在应用新的
解析的脚手架时仍然适用的信息。
   - 如果不存在，则使用解析后的模板作为初始文档。
   - 不要写回任何版本化模板层。
   - 识别 `[ALL_CAPS_IDENTIFIER]` 形式的每个占位符标记。
**IMPORTANT**：用户可能需要比模板中使用的原则更少或更多的原则。如果指定了数字，请遵守该数字 - 遵循通用模板。您将相应地更新文档。

2. 占位符的 Collect/derive 值：
   - 如果用户输入（对话）提供了一个值，请使用它。
   - 否则从现有的存储库上下文中推断（README、文档、先前的宪法版本（如果嵌入））。
   - 对于治理日期：`RATIFICATION_DATE` 是原始采用日期（如果未知询问或标记 TODO），如果进行更改，`LAST_AMENDED_DATE` 是今天，否则保留之前的日期。
   - `CONSTITUTION_VERSION` 必须根据语义版本控制规则递增：
     - MAJOR：向后不兼容的governance/principle删除或重新定义。
     - MINOR：新的 principle/section 添加或实质性扩展了指导。
     - PATCH：澄清、措辞、拼写错误修复、非语义改进。
   - 如果版本碰撞类型不明确，请在最终确定之前提出推理。

3. 使用解析后的模板作为所需结构起草更新的章程内容：
   - 用具体文本替换每个占位符（除了项目已选择不定义的有意保留的模板槽之外，没有留下任何带括号的标记 - 明确证明任何剩余的都是合理的）。
   - 保留标题层次结构和注释一旦替换即可删除，除非它们仍然添加澄清指导。
   - 确保每个原则部分：简洁的名称、段落（或项目符号列表）捕获不可协商的规则、明确的理由（如果不明显）。
   - 确保治理部分列出了修改程序、版本控制政策和合规性审查期望。

4. 更新后，在宪法文件顶部生成同步影响报告作为 HTML 注释。
本报告为临时草稿材料，供人工审核修订，并非治理
内容；预计在提交修订后的宪法文件之前将其删除。
   - 版本变更：旧→新
   - 修改原则列表（旧标题 → 如果重命名则为新标题）
   - 添加的部分
   - 删除的部分
   - 如果有任何占位符故意推迟，则跟进 TODO。

5. 最终输出之前的验证：
   - 没有剩余的无法解释的括号标记。
   - 版本行与报告匹配。
   - 日期 ISO 格式 YYYY-MM-DD。
   - 原则是声明性的、可测试的，并且没有模糊的语言（“应该”→ 在适当的情况下替换为 MUST/SHOULD 基本原理）。

6. 将完成的构成写回`.specify/memory/constitution.md`（覆盖）。

7. 向用户输出最终摘要：
   - 新版本和碰撞原理。
   - 任何需要手动跟进的 TODO 占位符或延迟项目。
   - 建议的提交消息（例如，`docs: amend constitution to vX.Y.Z (principle additions + governance update)`）。
   - `Next Actions` 部分用于任何延迟的非治理意图。

格式和风格要求：

- 完全按照模板中的方式使用 Markdown 标题（不要 demote/promote 级别）。
- 将长的基本原理行换行以保持可读性（理想情况下为 <100 字符），但不要用尴尬的中断来强制执行。
- 各部分之间保留一个空行。
- 避免尾随空格。

如果用户提供部分更新（例如，仅一项原则修订），仍然执行验证和版本决策步骤。

如果缺少关键信息（例如，批准日期确实未知），请插入 `TODO(<FIELD_NAME>): explanation` 并包含在同步影响报告中的延期项目下。

只写`.specify/memory/constitution.md`；不要创建或修改模板源文件。

## 执行后检查

**检查扩展钩子（构成更新后）**：
检查项目根目录中是否存在`.specify/extensions.yml`。
- 如果存在，则读取它并查找 `hooks.after_constitution` 键下的条目
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


## 原文对照

<details>
<summary>展开完整原文</summary>

## User Input

```text
$ARGUMENTS
```

You **MUST** consider the user input before proceeding (if not empty).

## Scope Guard

This command's own work is limited to updating the project constitution itself. Dependent templates
and commands read the constitution at runtime and are not modified here.

- Classify every part of the user input as either constitution content or a separate,
  non-governance intent.
- If the input includes feature implementation, code generation, refactoring, building, or
  deployment requests, you **MUST NOT** execute them. Extract them as deferred intents instead.
- You **MUST NOT** create, modify, or delete application source files, feature routes,
  components, tests, deployment files, or other artifacts unrelated to the constitution
  workflow.
- If it is unclear whether an instruction is constitution content, ask for clarification before
  making changes.
- After completing the constitution update, include a `Next Actions` section for each deferred
  intent. List the original intent and suggest the appropriate follow-up Spec Kit command, such
  as `__SPECKIT_COMMAND_SPECIFY__`, without invoking it.
- If there are no non-governance intents, omit the `Next Actions` section.

## Pre-Execution Checks

**Check for extension hooks (before constitution update)**:
- Check if `.specify/extensions.yml` exists in the project root.
- If it exists, read it and look for entries under the `hooks.before_constitution` key
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

    Wait for the result of the hook command before proceeding to the Outline.
    ```
    After emitting the block above you MUST actually invoke the hook and wait for it to finish before continuing. Run it the same way you would run the command yourself in this agent/session (the invocation may differ from the literal `{command}` id shown above, e.g. a skills-mode agent runs it as `/skill:speckit-...` or `$speckit-...`). Emitting the block alone does not run the hook.
- If no hooks are registered or `.specify/extensions.yml` does not exist, skip silently

## Outline

You are updating the project constitution at `.specify/memory/constitution.md`. The active
constitution scaffold is resolved at command time from `constitution-template` through the Spec Kit
preset/template resolution stack.

Follow this execution flow:

1. Run `{SCRIPT}` from the repository root and parse `TEMPLATE_CONTENT` as the active template.
   - The shared resolver applies project overrides, composing preset layers, and extension layers
     before the core template fallback. It MUST succeed before continuing.
   - If it fails, stop and report the resolution error; do not continue with only one contributing
     template layer.
   - If `.specify/memory/constitution.md` exists, load it as the source of current project-specific
     values and amendments. Preserve information that is still applicable when applying the newly
     resolved scaffold.
   - If it does not exist, use the resolved template as the initial document.
   - Do not write back to any versioned template layer.
   - Identify every placeholder token of the form `[ALL_CAPS_IDENTIFIER]`.
   **IMPORTANT**: The user might require less or more principles than the ones used in the template. If a number is specified, respect that - follow the general template. You will update the doc accordingly.

2. Collect/derive values for placeholders:
   - If user input (conversation) supplies a value, use it.
   - Otherwise infer from existing repo context (README, docs, prior constitution versions if embedded).
   - For governance dates: `RATIFICATION_DATE` is the original adoption date (if unknown ask or mark TODO), `LAST_AMENDED_DATE` is today if changes are made, otherwise keep previous.
   - `CONSTITUTION_VERSION` must increment according to semantic versioning rules:
     - MAJOR: Backward incompatible governance/principle removals or redefinitions.
     - MINOR: New principle/section added or materially expanded guidance.
     - PATCH: Clarifications, wording, typo fixes, non-semantic refinements.
   - If version bump type ambiguous, propose reasoning before finalizing.

3. Draft the updated constitution content using the resolved template as the required structure:
   - Replace every placeholder with concrete text (no bracketed tokens left except intentionally retained template slots that the project has chosen not to define yet—explicitly justify any left).
   - Preserve heading hierarchy and comments can be removed once replaced unless they still add clarifying guidance.
   - Ensure each Principle section: succinct name line, paragraph (or bullet list) capturing non‑negotiable rules, explicit rationale if not obvious.
   - Ensure Governance section lists amendment procedure, versioning policy, and compliance review expectations.

4. Produce a Sync Impact Report as an HTML comment at the top of the constitution file after update.
   This report is temporary scratch material for human review of the amendment, not governance
   content; it is expected to be removed before the amended constitution file is committed.
   - Version change: old → new
   - List of modified principles (old title → new title if renamed)
   - Added sections
   - Removed sections
   - Follow-up TODOs if any placeholders intentionally deferred.

5. Validation before final output:
   - No remaining unexplained bracket tokens.
   - Version line matches report.
   - Dates ISO format YYYY-MM-DD.
   - Principles are declarative, testable, and free of vague language ("should" → replace with MUST/SHOULD rationale where appropriate).

6. Write the completed constitution back to `.specify/memory/constitution.md` (overwrite).

7. Output a final summary to the user with:
   - New version and bump rationale.
   - Any TODO placeholders or deferred items requiring manual follow-up.
   - Suggested commit message (e.g., `docs: amend constitution to vX.Y.Z (principle additions + governance update)`).
   - A `Next Actions` section for any deferred non-governance intents.

Formatting & Style Requirements:

- Use Markdown headings exactly as in the template (do not demote/promote levels).
- Wrap long rationale lines to keep readability (<100 chars ideally) but do not hard enforce with awkward breaks.
- Keep a single blank line between sections.
- Avoid trailing whitespace.

If the user supplies partial updates (e.g., only one principle revision), still perform validation and version decision steps.

If critical info missing (e.g., ratification date truly unknown), insert `TODO(<FIELD_NAME>): explanation` and include in the Sync Impact Report under deferred items.

Write only `.specify/memory/constitution.md`; do not create or modify template source files.

## Post-Execution Checks

**Check for extension hooks (after constitution update)**:
Check if `.specify/extensions.yml` exists in the project root.
- If it exists, read it and look for entries under the `hooks.after_constitution` key
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


</details>
