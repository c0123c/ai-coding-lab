# Spec Kit 技术规划模板

根据规格完成调研、设计和接口产物，检查项目约束，再交接任务拆分或清单生成；属于规划命令模板。

- 来源：Spec Kit
- 资料类型：存档文档
- 存档版本：`0443760542e7`。
- 文档时间：源码提交（存档版）：[2026-09-09](<https://github.com/github/spec-kit/commit/b60057692cd726ea56331ec47f9ebc3e8877d8f7>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/spec-kit/blob/main/templates/commands/plan.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Spec Kit/Spec Kit 技术规划模板.md>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
description: Execute the implementation planning workflow using the plan template to generate design artifacts.
handoffs:
  - label: Create Tasks
    agent: speckit.tasks
    prompt: Break the plan into tasks
    send: true
  - label: Create Checklist
    agent: speckit.checklist
    prompt: Create a checklist for the following domain...
scripts:
  sh: scripts/bash/setup-plan.sh --json
  ps: scripts/powershell/setup-plan.ps1 -Json
  py: scripts/python/setup_plan.py --json
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

**检查延长钩（规划前）**：
- 检查项目根目录中是否存在`.specify/extensions.yml`。
- 如果存在，则读取它并查找 `hooks.before_plan` 键下的条目
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

1. **安装**：从存储库根运行`{SCRIPT}`并解析JSON为FEATURE_SPEC、IMPL_PLAN、FEATURE_DIR、BRANCH。对于像“I'm Groot”这样的参数中的单引号，请使用转义语法：例如 'I'\''m Groot' （或者如果可能的话使用双引号：“I'm Groot”）。

2. **加载上下文**：读取FEATURE_SPEC和`/memory/constitution.md`。加载IMPL_PLAN模板（已复制）。

3. **执行计划工作流程**：按照IMPL_PLAN模板中的结构进行：
   - 填写技术背景（将未知标记为“NEEDS CLARIFICATION”）
   - 填写宪法中的宪法检查部分
   - 评估门（ERROR 如果违规不合理）
   - 阶段0：生成research.md（解析所有NEEDSCLARIFICATION）
   - 阶段1：生成data-model.md，contracts/，quickstart.md
   - 设计后重新评估宪法检查

## 强制执行后挂钩

**您MUST在向用户报告完成情况之前完成此部分。**

检查项目根目录中是否存在`.specify/extensions.yml`。
- 如果不存在，或者`hooks.after_plan`下没有注册任何钩子，则跳至完成报告。
- 如果存在，请读取它并查找 `hooks.after_plan` 键下的条目。
- 如果 YAML 无法解析或无效，请不要默默地跳过：告诉用户无法读取 `.specify/extensions.yml`（包括解析器错误）并且未检查任何钩子，包括在那里注册的任何强制 (`optional: false`) 钩子，然后继续完成报告。
- 过滤掉 `enabled` 明确为 `false` 的钩子。默认情况下，将没有 `enabled` 字段的挂钩视为启用。
- 对于每个剩余的钩子，**不**尝试解释或评估钩子`condition`表达式：
  - 如果钩子没有 `condition` 字段，或者是 null/empty，则将钩子视为可执行文件
  - 如果挂钩定义非空 `condition`，则跳过挂钩并将条件评估留给 HookExecutor 实现
- 对于每个可执行挂钩，根据其 `optional` 标志输出以下内容：
  - **强制挂钩** (`optional: false`) — **您MUST为每个强制挂钩**发出`EXECUTE_COMMAND:`：
    ```
    ## Extension Hooks

    **Automatic Hook**: {extension}
    Executing: `/{command}`
    EXECUTE_COMMAND: {command}
    ```
在发出上面的块之后，您 MUST 实际上调用了钩子并等待它完成然后再继续。运行它的方式与您自己在此 agent/session 中运行命令的方式相同（调用可能与上面显示的文字 `{command}` id 不同，例如技能模式 agent 将其运行为 `/skill:speckit-...` 或 `$speckit-...`）。单独发出块不会运行钩子。
  - **可选挂钩**（`optional: true`）：
    ```
    ## Extension Hooks

    **Optional Hook**: {extension}
    Command: `/{command}`
    Description: {description}

    Prompt: {prompt}
    To execute: `/{command}`
    ```

## 竣工报告

命令在阶段1设计后结束。报告分支、IMPL_PLAN 路径和生成的工件。

## 阶段

### 阶段0：概要与研究

1. **从上面的技术上下文**中提取未知数：
   - 对于每个 NEEDS CLARIFICATION → 研究任务
   - 对于每个依赖项 → 最佳实践任务
   - 对于每个集成 → 模式任务

2. **生成和调度研究agents**：

   ```text
   For each unknown in Technical Context:
     Task: "Research {unknown} for {feature context}"
   For each technology choice:
     Task: "Find best practices for {tech} in {domain}"
   ```

3. **使用格式将结果**合并到`research.md`中：
   - 决策：[选择了什么]
   - 理由：[为什么选择]
   - 考虑的替代方案：[还评估了哪些内容]

**输出**：research.md，所有NEEDS CLARIFICATION已解决

### 阶段 1：设计与合同

**先决条件：** `research.md` 完成

1. **从特征规范中提取实体**→`data-model.md`：
   - 实体名称、字段、关系
   - 来自需求的验证规则
   - 状态转换（如果适用）

2. **定义接口合约**（如果项目有外部接口）→ `/contracts/`：
   - 确定项目向用户或其他系统公开哪些接口
   - 记录适合项目类型的合同格式
   - 示例：库的公共 API、CLI 工具的命令模式、Web 服务的端点、解析器的语法、应用程序的 UI 合约
   - 如果项目是纯粹内部的（构建脚本、一次性工具等），则跳过

3. **创建快速入门验证指南** → `quickstart.md`：
   - 记录可运行的验证场景，证明该功能端到端有效
   - 包括先决条件、设置命令、test/run 命令和预期结果
   - 使用合同和数据模型详细信息的链接或引用，而不是重复它们
   - 不包括完整的实现代码、model/service/controller主体、迁移或完整的测试套件
   - 保留此神器作为 validation/run 指南；实施细节属于`tasks.md`和实施阶段

**输出**：data-model.md、/contracts/*、quickstart.md

## 关键规则

- 使用绝对路径进行文件系统操作；使用项目相对路径作为文档中的参考
- ERROR 关于门故障或未解决的澄清

## 完成时间

- [ ] 执行计划工作流程并生成设计工件
- [ ] 根据上面强制执行后挂钩中的规则调度或跳过扩展挂钩
- [ ] 向用户报告完成情况，包括分支、计划路径和生成的工件


## 原文对照

<details>
<summary>展开完整原文</summary>

## User Input

```text
$ARGUMENTS
```

You **MUST** consider the user input before proceeding (if not empty).

## Pre-Execution Checks

**Check for extension hooks (before planning)**:
- Check if `.specify/extensions.yml` exists in the project root.
- If it exists, read it and look for entries under the `hooks.before_plan` key
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

1. **Setup**: Run `{SCRIPT}` from repo root and parse JSON for FEATURE_SPEC, IMPL_PLAN, FEATURE_DIR, BRANCH. For single quotes in args like "I'm Groot", use escape syntax: e.g 'I'\''m Groot' (or double-quote if possible: "I'm Groot").

2. **Load context**: Read FEATURE_SPEC and `/memory/constitution.md`. Load IMPL_PLAN template (already copied).

3. **Execute plan workflow**: Follow the structure in IMPL_PLAN template to:
   - Fill Technical Context (mark unknowns as "NEEDS CLARIFICATION")
   - Fill Constitution Check section from constitution
   - Evaluate gates (ERROR if violations unjustified)
   - Phase 0: Generate research.md (resolve all NEEDS CLARIFICATION)
   - Phase 1: Generate data-model.md, contracts/, quickstart.md
   - Re-evaluate Constitution Check post-design

## Mandatory Post-Execution Hooks

**You MUST complete this section before reporting completion to the user.**

Check if `.specify/extensions.yml` exists in the project root.
- If it does not exist, or no hooks are registered under `hooks.after_plan`, skip to the Completion Report.
- If it exists, read it and look for entries under the `hooks.after_plan` key.
- If the YAML cannot be parsed or is invalid, do not skip silently: tell the user that `.specify/extensions.yml` could not be read (include the parser error) and that no hooks were checked, including any mandatory (`optional: false`) hooks registered there, then continue to the Completion Report.
- Filter out hooks where `enabled` is explicitly `false`. Treat hooks without an `enabled` field as enabled by default.
- For each remaining hook, do **not** attempt to interpret or evaluate hook `condition` expressions:
  - If the hook has no `condition` field, or it is null/empty, treat the hook as executable
  - If the hook defines a non-empty `condition`, skip the hook and leave condition evaluation to the HookExecutor implementation
- For each executable hook, output the following based on its `optional` flag:
  - **Mandatory hook** (`optional: false`) — **You MUST emit `EXECUTE_COMMAND:` for each mandatory hook**:
    ```
    ## Extension Hooks

    **Automatic Hook**: {extension}
    Executing: `/{command}`
    EXECUTE_COMMAND: {command}
    ```
    After emitting the block above you MUST actually invoke the hook and wait for it to finish before continuing. Run it the same way you would run the command yourself in this agent/session (the invocation may differ from the literal `{command}` id shown above, e.g. a skills-mode agent runs it as `/skill:speckit-...` or `$speckit-...`). Emitting the block alone does not run the hook.
  - **Optional hook** (`optional: true`):
    ```
    ## Extension Hooks

    **Optional Hook**: {extension}
    Command: `/{command}`
    Description: {description}

    Prompt: {prompt}
    To execute: `/{command}`
    ```

## Completion Report

Command ends after Phase 1 design. Report branch, IMPL_PLAN path, and generated artifacts.

## Phases

### Phase 0: Outline & Research

1. **Extract unknowns from Technical Context** above:
   - For each NEEDS CLARIFICATION → research task
   - For each dependency → best practices task
   - For each integration → patterns task

2. **Generate and dispatch research agents**:

   ```text
   For each unknown in Technical Context:
     Task: "Research {unknown} for {feature context}"
   For each technology choice:
     Task: "Find best practices for {tech} in {domain}"
   ```

3. **Consolidate findings** in `research.md` using format:
   - Decision: [what was chosen]
   - Rationale: [why chosen]
   - Alternatives considered: [what else evaluated]

**Output**: research.md with all NEEDS CLARIFICATION resolved

### Phase 1: Design & Contracts

**Prerequisites:** `research.md` complete

1. **Extract entities from feature spec** → `data-model.md`:
   - Entity name, fields, relationships
   - Validation rules from requirements
   - State transitions if applicable

2. **Define interface contracts** (if project has external interfaces) → `/contracts/`:
   - Identify what interfaces the project exposes to users or other systems
   - Document the contract format appropriate for the project type
   - Examples: public APIs for libraries, command schemas for CLI tools, endpoints for web services, grammars for parsers, UI contracts for applications
   - Skip if project is purely internal (build scripts, one-off tools, etc.)

3. **Create quickstart validation guide** → `quickstart.md`:
   - Document runnable validation scenarios that prove the feature works end-to-end
   - Include prerequisites, setup commands, test/run commands, and expected outcomes
   - Use links or references to contracts and data model details instead of duplicating them
   - Do not include full implementation code, model/service/controller bodies, migrations, or complete test suites
   - Keep this artifact as a validation/run guide; implementation details belong in `tasks.md` and the implementation phase

**Output**: data-model.md, /contracts/*, quickstart.md

## Key rules

- Use absolute paths for filesystem operations; use project-relative paths for references in documentation
- ERROR on gate failures or unresolved clarifications

## Done When

- [ ] Plan workflow executed and design artifacts generated
- [ ] Extension hooks dispatched or skipped according to the rules in Mandatory Post-Execution Hooks above
- [ ] Completion reported to user with branch, plan path, and generated artifacts


</details>
