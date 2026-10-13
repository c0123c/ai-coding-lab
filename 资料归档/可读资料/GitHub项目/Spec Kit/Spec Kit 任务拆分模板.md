# Spec Kit 任务拆分模板

根据设计资料生成带依赖顺序的 tasks.md，按阶段组织可执行事项并列出完成条件；属于任务生成模板。

- 来源：Spec Kit
- 资料类型：存档文档
- 存档版本：`0443760542e7`。
- 文档时间：源码提交（存档版）：[2026-09-09](<https://github.com/github/spec-kit/commit/b60057692cd726ea56331ec47f9ebc3e8877d8f7>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/spec-kit/blob/main/templates/commands/tasks.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Spec Kit/Spec Kit 任务拆分模板.md>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
description: Generate an actionable, dependency-ordered tasks.md for the feature based on available design artifacts.
handoffs:
  - label: Analyze For Consistency
    agent: speckit.analyze
    prompt: Run a project analysis for consistency
    send: true
  - label: Implement Project
    agent: speckit.implement
    prompt: Start the implementation in phases
    send: true
scripts:
  sh: scripts/bash/setup-tasks.sh --json
  ps: scripts/powershell/setup-tasks.ps1 -Json
  py: scripts/python/setup_tasks.py --json
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

**检查扩展钩子（在任务生成之前）**：
- 检查项目根目录中是否存在`.specify/extensions.yml`。
- 如果存在，则读取它并查找 `hooks.before_tasks` 键下的条目
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

1. **安装**：从存储库根运行`{SCRIPT}`并解析FEATURE_DIR、TASKS_TEMPLATE_CONTENT、TASKS_TEMPLATE和AVAILABLE_DOCS列表。 `FEATURE_DIR` 和 `TASKS_TEMPLATE` 提供时必须是绝对路径。 `AVAILABLE_DOCS` 是 `FEATURE_DIR` 下可用的文档 names/relative 路径列表（例如 `research.md` 或 `contracts/`）。对于像“I'm Groot”这样的参数中的单引号，请使用转义语法：例如 'I'\''m Groot' （或者如果可能的话使用双引号：“I'm Groot”）。

2. **加载设计文档**：读取FEATURE_DIR：
   - **必需**：plan.md（技术堆栈、库、结构）、spec.md（具有优先级的用户故事）
   - **可选**：data-model.md（实体），contracts/（接口合约），research.md（决策），quickstart.md（测试场景）
   - **IF EXISTS**：加载`/memory/constitution.md`以了解项目原则和治理约束
   - 注意：并非所有项目都有所有文档。根据可用内容生成任务。

3. **执行任务生成工作流程**：
   - 加载 plan.md 并提取技术堆栈、库、项目结构
   - 加载 spec.md 并提取用户故事及其优先级（P1、P2、P3 等）
   - 如果 data-model.md 存在：提取实体并映射到用户故事
   - 如果契约/存在：将接口契约映射到用户故事
   - 如果 research.md 存在：提取设置任务的决策
   - 生成按用户故事组织的任务（请参阅下面的任务生成规则）
   - 生成显示用户故事完成顺序的依赖图
   - 每个用户故事创建并行执行示例
   - 验证任务完整性（每个用户故事都有所有需要的任务，可独立测试）

4. **生成tasks.md**：使用TASKS_TEMPLATE_CONTENT（来自上面的JSON输出）作为结构。为了与省略 TASKS_TEMPLATE_CONTENT 的旧安装脚本兼容，请改为阅读 TASKS_TEMPLATE。填写：
   - 来自 plan.md 的正确功能名称
   - 阶段1：设置任务（项目初始化）
   - 阶段 2：基础任务（阻止所有用户故事的先决条件）
   - 阶段 3+：每个用户故事一个阶段（按照 spec.md 的优先顺序）
   - 每个阶段包括：故事目标、独立测试标准、测试（如果需要）、实施任务
   - 最后阶段：完善和横切关注点
   - 所有任务必须遵循严格的清单格式（请参阅下面的任务生成规则）
   - 清除每个任务的文件路径
   - 显示故事完成顺序的依赖关系部分
   - 每个故事的并行执行示例
   - 实施策略部分（【MVP】第一、增量交付）

## 强制执行后挂钩

**您MUST在向用户报告完成情况之前完成此部分。**

检查项目根目录中是否存在`.specify/extensions.yml`。
- 如果不存在，或者`hooks.after_tasks`下没有注册任何钩子，则跳至完成报告。
- 如果存在，请读取它并查找 `hooks.after_tasks` 键下的条目。
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

生成的tasks.md的输出路径和摘要：
- 任务总数
- 每个用户故事的任务计数
- 确定的并行机会
- 每个故事的独立测试标准
- 建议的 MVP 范围（通常只是用户故事 1）
- 格式验证：确认ALL任务遵循清单格式（复选框、ID、标签、文件路径）

任务生成的上下文：{ARGS}

tasks.md 应该可以立即执行 - 每个任务必须足够具体，以便 LLM 可以在没有额外上下文的情况下完成它。

## 任务生成规则

**CRITICAL**：任务MUST按用户故事组织，以实现独立实施和测试。

**测试是OPTIONAL**：仅在功能规范中明确要求或用户请求TDD方法时才生成测试任务。

### 检查表格式（REQUIRED）

每个任务MUST严格遵循以下格式：

```text
- [ ] [TaskID] [P?] [Story?] Description with file path
```

**格式组件**：

1. **复选框**：ALWAYS以`- [ ]`开头（降价复选框）
2. **任务ID**：执行顺序的序号（T001、T002、T003...）
3. **[P] 标记**：如果任务可并行，则包含 ONLY（不同文件，不依赖于不完整的任务）
4. **[故事]标签**：REQUIRED仅适用于用户故事阶段任务
   - 格式：[US1]、[US2]、[US3] 等（映射到 spec.md 中的用户故事）
   - 设置阶段：NO 故事标签
   - 基础阶段：NO故事标签
   - 用户故事阶段：MUST 有故事标签
   - 打磨阶段：【NO】故事标签
5. **说明**：具有确切文件路径的清除操作

**示例**：

- ✅ CORRECT: `- [ ] T001 Create project structure per implementation plan`
- ✅ CORRECT: `- [ ] T005 [P] Implement authentication middleware in src/middleware/auth.py`
- ✅ CORRECT: `- [ ] T012 [P] [US1] Create User model in src/models/user.py`
- ✅ CORRECT: `- [ ] T014 [US1] Implement UserService in src/services/user_service.py`
- ❌ WRONG：`- [ ] Create User model`（缺少ID和故事标签）
- ❌ WRONG：`T001 [US1] Create model`（缺少复选框）
- ❌ WRONG：`- [ ] [US1] Create User model`（缺少任务ID）
- ❌ WRONG：`- [ ] T001 [US1] Create model`（缺少文件路径）

### 任务组织

1. **来自用户故事 (spec.md)** - PRIMARY ORGANIZATION：
   - 每个用户故事（P1、P2、P3...）都有自己的阶段
   - 将所有相关组件映射到他们的故事：
     - 该故事所需的模型
     - 该故事所需的服务
     - 该故事需要 Interfaces/UI
     - 如果需要测试：特定于该故事的测试
   - 标记故事依赖性（大多数故事应该是独立的）

2. **来自合同**：
   - 将每个界面契约→映射到它所服务的用户故事
   - 如果需要测试：每个接口合约 → 合约测试任务 [P] 在该故事阶段实施之前

3. **来自数据模型**：
   - 将每个实体映射到需要它的用户故事
   - 如果实体服务于多个故事：放入最早的故事或设置阶段
   - 关系→适当故事阶段的服务层任务
   - 对于 data-model.md 中具有约束的每个字段（最大长度、nullable/required、枚举值、验证规则），请在任务描述中逐字引用约束，这样就不会由执行时自行决定

4. **来自Setup/Infrastructure**：
   - 共享基础设施→设置阶段（阶段1）
   - Foundational/blocking 任务 → 基础阶段（阶段 2）
   - 特定于故事的设置 → 在该故事的阶段内

### 相结构

- **阶段1**：设置（项目初始化）
- **阶段 2**：基础（阻止先决条件 - MUST 在用户故事之前完成）
- **阶段 3+**：按优先级顺序排列的用户故事（P1、P2、P3...）
  - 在每个故事中：测试（如果需要）→模型→服务→端点→集成
  - 每个阶段都应该是一个完整的、可独立测试的增量
- **最后阶段**：完善和交叉关注点

## 完成时间

- 使用所有阶段、任务 ID 和文件路径生成的 [ ] tasks.md
- [ ] 根据上面强制执行后挂钩中的规则调度或跳过扩展挂钩
- [ ] 向用户报告完成情况，包括任务计数、故事细分和 MVP 范围


## 原文对照

<details>
<summary>展开完整原文</summary>

## User Input

```text
$ARGUMENTS
```

You **MUST** consider the user input before proceeding (if not empty).

## Pre-Execution Checks

**Check for extension hooks (before tasks generation)**:
- Check if `.specify/extensions.yml` exists in the project root.
- If it exists, read it and look for entries under the `hooks.before_tasks` key
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

1. **Setup**: Run `{SCRIPT}` from repo root and parse FEATURE_DIR, TASKS_TEMPLATE_CONTENT, TASKS_TEMPLATE, and AVAILABLE_DOCS list. `FEATURE_DIR` and `TASKS_TEMPLATE` must be absolute paths when provided. `AVAILABLE_DOCS` is a list of document names/relative paths available under `FEATURE_DIR` (for example `research.md` or `contracts/`). For single quotes in args like "I'm Groot", use escape syntax: e.g 'I'\''m Groot' (or double-quote if possible: "I'm Groot").

2. **Load design documents**: Read from FEATURE_DIR:
   - **Required**: plan.md (tech stack, libraries, structure), spec.md (user stories with priorities)
   - **Optional**: data-model.md (entities), contracts/ (interface contracts), research.md (decisions), quickstart.md (test scenarios)
   - **IF EXISTS**: Load `/memory/constitution.md` for project principles and governance constraints
   - Note: Not all projects have all documents. Generate tasks based on what's available.

3. **Execute task generation workflow**:
   - Load plan.md and extract tech stack, libraries, project structure
   - Load spec.md and extract user stories with their priorities (P1, P2, P3, etc.)
   - If data-model.md exists: Extract entities and map to user stories
   - If contracts/ exists: Map interface contracts to user stories
   - If research.md exists: Extract decisions for setup tasks
   - Generate tasks organized by user story (see Task Generation Rules below)
   - Generate dependency graph showing user story completion order
   - Create parallel execution examples per user story
   - Validate task completeness (each user story has all needed tasks, independently testable)

4. **Generate tasks.md**: Use TASKS_TEMPLATE_CONTENT (from the JSON output above) as the structure. For compatibility with older setup scripts that omit TASKS_TEMPLATE_CONTENT, read TASKS_TEMPLATE instead. Fill with:
   - Correct feature name from plan.md
   - Phase 1: Setup tasks (project initialization)
   - Phase 2: Foundational tasks (blocking prerequisites for all user stories)
   - Phase 3+: One phase per user story (in priority order from spec.md)
   - Each phase includes: story goal, independent test criteria, tests (if requested), implementation tasks
   - Final Phase: Polish & cross-cutting concerns
   - All tasks must follow the strict checklist format (see Task Generation Rules below)
   - Clear file paths for each task
   - Dependencies section showing story completion order
   - Parallel execution examples per story
   - Implementation strategy section (MVP first, incremental delivery)

## Mandatory Post-Execution Hooks

**You MUST complete this section before reporting completion to the user.**

Check if `.specify/extensions.yml` exists in the project root.
- If it does not exist, or no hooks are registered under `hooks.after_tasks`, skip to the Completion Report.
- If it exists, read it and look for entries under the `hooks.after_tasks` key.
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

Output path to generated tasks.md and summary:
- Total task count
- Task count per user story
- Parallel opportunities identified
- Independent test criteria for each story
- Suggested MVP scope (typically just User Story 1)
- Format validation: Confirm ALL tasks follow the checklist format (checkbox, ID, labels, file paths)

Context for task generation: {ARGS}

The tasks.md should be immediately executable - each task must be specific enough that an LLM can complete it without additional context.

## Task Generation Rules

**CRITICAL**: Tasks MUST be organized by user story to enable independent implementation and testing.

**Tests are OPTIONAL**: Only generate test tasks if explicitly requested in the feature specification or if user requests TDD approach.

### Checklist Format (REQUIRED)

Every task MUST strictly follow this format:

```text
- [ ] [TaskID] [P?] [Story?] Description with file path
```

**Format Components**:

1. **Checkbox**: ALWAYS start with `- [ ]` (markdown checkbox)
2. **Task ID**: Sequential number (T001, T002, T003...) in execution order
3. **[P] marker**: Include ONLY if task is parallelizable (different files, no dependencies on incomplete tasks)
4. **[Story] label**: REQUIRED for user story phase tasks only
   - Format: [US1], [US2], [US3], etc. (maps to user stories from spec.md)
   - Setup phase: NO story label
   - Foundational phase: NO story label
   - User Story phases: MUST have story label
   - Polish phase: NO story label
5. **Description**: Clear action with exact file path

**Examples**:

- ✅ CORRECT: `- [ ] T001 Create project structure per implementation plan`
- ✅ CORRECT: `- [ ] T005 [P] Implement authentication middleware in src/middleware/auth.py`
- ✅ CORRECT: `- [ ] T012 [P] [US1] Create User model in src/models/user.py`
- ✅ CORRECT: `- [ ] T014 [US1] Implement UserService in src/services/user_service.py`
- ❌ WRONG: `- [ ] Create User model` (missing ID and Story label)
- ❌ WRONG: `T001 [US1] Create model` (missing checkbox)
- ❌ WRONG: `- [ ] [US1] Create User model` (missing Task ID)
- ❌ WRONG: `- [ ] T001 [US1] Create model` (missing file path)

### Task Organization

1. **From User Stories (spec.md)** - PRIMARY ORGANIZATION:
   - Each user story (P1, P2, P3...) gets its own phase
   - Map all related components to their story:
     - Models needed for that story
     - Services needed for that story
     - Interfaces/UI needed for that story
     - If tests requested: Tests specific to that story
   - Mark story dependencies (most stories should be independent)

2. **From Contracts**:
   - Map each interface contract → to the user story it serves
   - If tests requested: Each interface contract → contract test task [P] before implementation in that story's phase

3. **From Data Model**:
   - Map each entity to the user story(ies) that need it
   - If entity serves multiple stories: Put in earliest story or Setup phase
   - Relationships → service layer tasks in appropriate story phase
   - For each field with constraints in data-model.md (max length, nullable/required, enum values, validation rules), quote the constraint verbatim in the task description so it is not left to implementation-time discretion

4. **From Setup/Infrastructure**:
   - Shared infrastructure → Setup phase (Phase 1)
   - Foundational/blocking tasks → Foundational phase (Phase 2)
   - Story-specific setup → within that story's phase

### Phase Structure

- **Phase 1**: Setup (project initialization)
- **Phase 2**: Foundational (blocking prerequisites - MUST complete before user stories)
- **Phase 3+**: User Stories in priority order (P1, P2, P3...)
  - Within each story: Tests (if requested) → Models → Services → Endpoints → Integration
  - Each phase should be a complete, independently testable increment
- **Final Phase**: Polish & Cross-Cutting Concerns

## Done When

- [ ] tasks.md generated with all phases, task IDs, and file paths
- [ ] Extension hooks dispatched or skipped according to the rules in Mandatory Post-Execution Hooks above
- [ ] Completion reported to user with task count, story breakdown, and MVP scope


</details>
