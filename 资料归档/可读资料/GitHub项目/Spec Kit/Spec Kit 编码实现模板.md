# Spec Kit 编码实现模板

按 tasks.md 推进实现，处理依赖、执行检查和 Hook 并汇报结果；属于工作流执行命令模板。

- 来源：Spec Kit
- 资料类型：存档文档
- 存档版本：`0443760542e7`。
- 文档时间：源码提交（存档版）：[2026-09-09](<https://github.com/github/spec-kit/commit/b60057692cd726ea56331ec47f9ebc3e8877d8f7>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/spec-kit/blob/main/templates/commands/implement.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Spec Kit/Spec Kit 编码实现模板.md>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
description: Execute the implementation plan by processing and executing all tasks defined in tasks.md
scripts:
  sh: scripts/bash/check-prerequisites.sh --json --require-tasks --include-tasks
  ps: scripts/powershell/check-prerequisites.ps1 -Json -RequireTasks -IncludeTasks
  py: scripts/python/check_prerequisites.py --json --require-tasks --include-tasks
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

**检查扩展钩子（实现之前）**：
- 检查项目根目录中是否存在`.specify/extensions.yml`。
- 如果存在，则读取它并查找 `hooks.before_implement` 键下的条目
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

1. 从存储库根运行 `{SCRIPT}` 并解析 FEATURE_DIR 和 AVAILABLE_DOCS 列表。所有路径都必须是绝对路径。对于像“I'm Groot”这样的参数中的单引号，请使用转义语法：例如 'I'\''m Groot' （或者如果可能的话使用双引号：“I'm Groot”）。

2. **检查清单状态**（如果FEATURE_DIR/checklists/存在）：
   - 将清单标记视为只读门：扫描复选框状态、报告状态，并在需要时在继续之前询问； NOT 修改清单文件或标记
   - `checklists/requirements.md`是`__SPECKIT_COMMAND_SPECIFY__`和`__SPECKIT_COMMAND_CLARIFY__`维护的内置规格质量检查表； `__SPECKIT_COMMAND_CHECKLIST__` 生成的自定义清单是审阅者拥有的需求质量审阅工件
   - 对于自定义检查表，`[x]` 表示审核者确定满足需求质量标准；它确实 NOT 意味着实施工作已完成
   - 扫描 checklists/ 目录中的所有清单文件
   - 对于每个清单，计算：
     - 总项目：所有匹配 `- [ ]` 或 `- [X]` 或 `- [x]` 的行
     - 检查的项目：匹配`- [X]`或`- [x]`的行
     - 未选中的项目：匹配 `- [ ]` 的行
   - 创建状态表：

     ```text
     | Checklist | Total | Checked | Unchecked | Status |
     |-----------|-------|---------|-----------|--------|
     | ux.md     | 12    | 12      | 0         | ✓ PASS |
     | test.md   | 8     | 5       | 3         | ✗ FAIL |
     | security.md | 6   | 6       | 0         | ✓ PASS |
     ```

   - 计算总体状态：
     - **PASS**：所有清单都有0未选中的项目
     - **FAIL**：一个或多个清单有未选中的项目

   - **如果任何清单中有未勾选的项目**：
     - 显示包含未检查项目计数的表
     - **STOP** 并询问：“某些清单有未检查的项目。您是否仍要继续实施？(yes/no)”
     - 等待用户响应后再继续
     - 如果用户说“不”或“等待”或“停止”，则停止执行
     - 如果用户说“是”或“继续”或“继续”，则继续执行步骤 3

   - **如果检查了所有清单**：
     - 显示表格，显示所有已通过的清单
     - 自动进行步骤3

3. 加载并分析实现上下文：
   - **REQUIRED**：阅读tasks.md获取完整的任务列表和执行计划
   - **REQUIRED**：阅读 plan.md 了解技术堆栈、架构和文件结构
   - **IF EXISTS**：读取 data-model.md 以了解实体和关系
   - **IF EXISTS**：阅读合同/了解 API 规格和测试要求
   - **IF EXISTS**：阅读 research.md 了解技术决策和限制
   - **IF EXISTS**：阅读/memory/constitution.md以了解治理约束
   - **IF EXISTS**：阅读 quickstart.md 了解集成场景

4. **项目设置验证**：
   - **REQUIRED**：Create/verify根据实际项目设置忽略文件：

**检测与创建逻辑**：
   - 检查以下命令是否成功确定存储库是否为 git 存储库（如果是，则为 create/verify .gitignore）：

     ```sh
     git rev-parse --git-dir 2>/dev/null
     ```

   - 检查 Dockerfile* 是否存在或 Docker 位于 plan.md → create/verify .dockerignore
   - 检查 .eslintrc* 是否存在 → create/verify .eslintignore
   - 检查 eslint.config.* 是否存在 → 确保配置的 `ignores` 条目涵盖所需的模式
   - 检查 .prettierrc* 是否存在 → create/verify .prettierignore
   - 检查 .npmrc 或 package.json 是否存在 → create/verify .npmignore （如果发布）
   - 检查 terraform 文件 (*.tf) 是否存在 → create/verify .terraformignore
   - 检查是否需要 .helmignore（存在 helm 图表）→ create/verify .helmignore

**如果忽略文件已存在**：验证其包含基本模式，仅附加缺少的关键模式
 **如果忽略文件丢失**：使用检测到的技术的完整模式集创建

**按技术分类的常见模式**（来自plan.md技术堆栈）：
   - **Node.js/JavaScript/TypeScript**: `node_modules/`, `dist/`, `build/`, `*.log`, `.env*`
   - **Python**: `__pycache__/`, `*.pyc`, `.venv/`, `venv/`, `dist/`, `*.egg-info/`
   - **Java**: `target/`, `*.class`, `*.jar`, `.gradle/`, `build/`
   - **C#/.NET**: `bin/`, `obj/`, `*.user`, `*.suo`, `packages/`
   - **Go**: `*.exe`, `*.test`, `vendor/`, `*.out`
   - **Ruby**: `.bundle/`, `log/`, `tmp/`, `*.gem`, `vendor/bundle/`
   - **PHP**: `vendor/`, `*.log`, `*.cache`, `*.env`
   - **Rust**: `target/`, `debug/`, `release/`, `*.rs.bk`, `*.rlib`, `*.prof*`, `.idea/`, `*.log`, `.env*`
   - **Kotlin**: `build/`, `out/`, `.gradle/`, `.idea/`, `*.class`, `*.jar`, `*.iml`, `*.log`, `.env*`
   - **C++**: `build/`, `bin/`, `obj/`, `out/`, `*.o`, `*.so`, `*.a`, `*.exe`, `*.dll`, `.idea/`, `*.log`, `.env*`
   - **C**: `build/`, `bin/`, `obj/`, `out/`, `*.o`, `*.a`, `*.so`, `*.exe`, `*.dll`, `autom4te.cache/`, `config.status`, `config.log`, `.idea/`, `*.log`, `.env*`
   - **斯威夫特**：`.build/`，`DerivedData/`，`*.swiftpm/`，`Packages/`
   - **R**: `.Rproj.user/`, `.Rhistory`, `.RData`, `.Ruserdata`, `*.Rproj`, `packrat/`, `renv/`
   - **通用**：`.DS_Store`、`Thumbs.db`、`*.tmp`、`*.swp`、`.vscode/`、`.idea/`

**工具特定模式**：
   - **Docker**：`node_modules/`、`.git/`、`Dockerfile*`、`.dockerignore`、`*.log*`、`.env*`、`coverage/`
   - **ESLint**：`node_modules/`、`dist/`、`build/`、`coverage/`、`*.min.js`
   - **更漂亮**：`node_modules/`、`dist/`、`build/`、`coverage/`、`package-lock.json`、`yarn.lock`、 `pnpm-lock.yaml`
   - **Terraform**：`.terraform/`、`*.tfstate*`、`*.tfvars`、`.terraform.lock.hcl`
   - **Kubernetes/k8s**: `*.secret.yaml`, `secrets/`, `.kube/`, `kubeconfig*`, `*.key`, `*.crt`

5. 解析tasks.md结构并提取：
   - **任务阶段**：设置、测试、核心、集成、完善
   - **任务依赖关系**：顺序与并行执行规则
   - **任务详细信息**：ID、描述、文件路径、并行标记[P]
   - **执行流程**：顺序和依赖关系要求

6. 按照任务计划执行实施：
   - **分阶段执行**：完成每个阶段，然后再进入下一阶段
   - **尊重依赖关系**：按顺序运行顺序任务，并行任务[P]可以一起运行
   - **遵循TDD方法**：在相应的执行任务之前执行测试任务
   - **基于文件的协调**：影响相同文件的任务必须按顺序运行
   - **验证检查点**：在继续之前验证每个阶段的完成情况

7. 实施执行规则：
   - **首先设置**：初始化项目结构、依赖项、配置
   - **代码之前的测试**：如果您需要为合约、实体和集成场景编写测试
   - **核心开发**：实现模型、服务、CLI命令、端点
   - **集成工作**：数据库连接、中间件、日志记录、外部服务
   - **完善和验证**：单元测试、性能优化、文档

8. 进度跟踪和错误处理：
   - 每完成一项任务后报告进度
   - 如果任何非并行任务失败则停止执行
   - 对于并行任务 [P]，继续执行成功的任务，报告失败的任务
   - 提供清晰的错误消息以及调试上下文
   - 如果实施无法继续，建议后续步骤
   - **IMPORTANT** 对于已完成的任务，请确保在任务文件中将任务标记为 [X]。

9. 完成验证：
   - 验证所有必需的任务均已完成
   - 检查实现的功能是否符合原始规范
   - 验证测试是否通过且覆盖范围满足要求
   - 确认实施遵循技术计划

注意：此命令假定 tasks.md 中存在完整的任务细分。如果任务不完整或丢失，建议先运行`__SPECKIT_COMMAND_TASKS__`重新生成任务列表。

## 强制执行后挂钩

**您MUST在向用户报告完成情况之前完成此部分。**

检查项目根目录中是否存在`.specify/extensions.yml`。
- 如果不存在，或者`hooks.after_implement`下没有注册任何钩子，则跳至完成报告。
- 如果存在，请读取它并查找 `hooks.after_implement` 键下的条目。
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

报告最终状态以及已完成工作的摘要。

## 完成时间

- [ ] tasks.md 中的所有任务已完成并标记为 `[X]`
- [ ] 根据规范、计划和测试覆盖率验证实施
- [ ] 根据上面强制执行后挂钩中的规则调度或跳过扩展挂钩
- [ ] 向用户报告完成情况以及已完成工作的摘要


## 原文对照

<details>
<summary>展开完整原文</summary>

## User Input

```text
$ARGUMENTS
```

You **MUST** consider the user input before proceeding (if not empty).

## Pre-Execution Checks

**Check for extension hooks (before implementation)**:
- Check if `.specify/extensions.yml` exists in the project root.
- If it exists, read it and look for entries under the `hooks.before_implement` key
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

1. Run `{SCRIPT}` from repo root and parse FEATURE_DIR and AVAILABLE_DOCS list. All paths must be absolute. For single quotes in args like "I'm Groot", use escape syntax: e.g 'I'\''m Groot' (or double-quote if possible: "I'm Groot").

2. **Check checklists status** (if FEATURE_DIR/checklists/ exists):
   - Treat checklist markers as a read-only gate: scan checkbox state, report status, and ask before proceeding when needed; do NOT modify checklist files or markers
   - `checklists/requirements.md` is the built-in spec-quality checklist maintained by `__SPECKIT_COMMAND_SPECIFY__` and `__SPECKIT_COMMAND_CLARIFY__`; custom checklists generated by `__SPECKIT_COMMAND_CHECKLIST__` are reviewer-owned requirements-quality review artifacts
   - For custom checklists, `[x]` means the reviewer determined the requirements-quality criterion is satisfied; it does NOT mean implementation work is complete
   - Scan all checklist files in the checklists/ directory
   - For each checklist, count:
     - Total items: All lines matching `- [ ]` or `- [X]` or `- [x]`
     - Checked items: Lines matching `- [X]` or `- [x]`
     - Unchecked items: Lines matching `- [ ]`
   - Create a status table:

     ```text
     | Checklist | Total | Checked | Unchecked | Status |
     |-----------|-------|---------|-----------|--------|
     | ux.md     | 12    | 12      | 0         | ✓ PASS |
     | test.md   | 8     | 5       | 3         | ✗ FAIL |
     | security.md | 6   | 6       | 0         | ✓ PASS |
     ```

   - Calculate overall status:
     - **PASS**: All checklists have 0 unchecked items
     - **FAIL**: One or more checklists have unchecked items

   - **If any checklist has unchecked items**:
     - Display the table with unchecked item counts
     - **STOP** and ask: "Some checklists have unchecked items. Do you want to proceed with implementation anyway? (yes/no)"
     - Wait for user response before continuing
     - If user says "no" or "wait" or "stop", halt execution
     - If user says "yes" or "proceed" or "continue", proceed to step 3

   - **If all checklists are checked**:
     - Display the table showing all checklists passed
     - Automatically proceed to step 3

3. Load and analyze the implementation context:
   - **REQUIRED**: Read tasks.md for the complete task list and execution plan
   - **REQUIRED**: Read plan.md for tech stack, architecture, and file structure
   - **IF EXISTS**: Read data-model.md for entities and relationships
   - **IF EXISTS**: Read contracts/ for API specifications and test requirements
   - **IF EXISTS**: Read research.md for technical decisions and constraints
   - **IF EXISTS**: Read /memory/constitution.md for governance constraints
   - **IF EXISTS**: Read quickstart.md for integration scenarios

4. **Project Setup Verification**:
   - **REQUIRED**: Create/verify ignore files based on actual project setup:

   **Detection & Creation Logic**:
   - Check if the following command succeeds to determine if the repository is a git repo (create/verify .gitignore if so):

     ```sh
     git rev-parse --git-dir 2>/dev/null
     ```

   - Check if Dockerfile* exists or Docker in plan.md → create/verify .dockerignore
   - Check if .eslintrc* exists → create/verify .eslintignore
   - Check if eslint.config.* exists → ensure the config's `ignores` entries cover required patterns
   - Check if .prettierrc* exists → create/verify .prettierignore
   - Check if .npmrc or package.json exists → create/verify .npmignore (if publishing)
   - Check if terraform files (*.tf) exist → create/verify .terraformignore
   - Check if .helmignore needed (helm charts present) → create/verify .helmignore

   **If ignore file already exists**: Verify it contains essential patterns, append missing critical patterns only
   **If ignore file missing**: Create with full pattern set for detected technology

   **Common Patterns by Technology** (from plan.md tech stack):
   - **Node.js/JavaScript/TypeScript**: `node_modules/`, `dist/`, `build/`, `*.log`, `.env*`
   - **Python**: `__pycache__/`, `*.pyc`, `.venv/`, `venv/`, `dist/`, `*.egg-info/`
   - **Java**: `target/`, `*.class`, `*.jar`, `.gradle/`, `build/`
   - **C#/.NET**: `bin/`, `obj/`, `*.user`, `*.suo`, `packages/`
   - **Go**: `*.exe`, `*.test`, `vendor/`, `*.out`
   - **Ruby**: `.bundle/`, `log/`, `tmp/`, `*.gem`, `vendor/bundle/`
   - **PHP**: `vendor/`, `*.log`, `*.cache`, `*.env`
   - **Rust**: `target/`, `debug/`, `release/`, `*.rs.bk`, `*.rlib`, `*.prof*`, `.idea/`, `*.log`, `.env*`
   - **Kotlin**: `build/`, `out/`, `.gradle/`, `.idea/`, `*.class`, `*.jar`, `*.iml`, `*.log`, `.env*`
   - **C++**: `build/`, `bin/`, `obj/`, `out/`, `*.o`, `*.so`, `*.a`, `*.exe`, `*.dll`, `.idea/`, `*.log`, `.env*`
   - **C**: `build/`, `bin/`, `obj/`, `out/`, `*.o`, `*.a`, `*.so`, `*.exe`, `*.dll`, `autom4te.cache/`, `config.status`, `config.log`, `.idea/`, `*.log`, `.env*`
   - **Swift**: `.build/`, `DerivedData/`, `*.swiftpm/`, `Packages/`
   - **R**: `.Rproj.user/`, `.Rhistory`, `.RData`, `.Ruserdata`, `*.Rproj`, `packrat/`, `renv/`
   - **Universal**: `.DS_Store`, `Thumbs.db`, `*.tmp`, `*.swp`, `.vscode/`, `.idea/`

   **Tool-Specific Patterns**:
   - **Docker**: `node_modules/`, `.git/`, `Dockerfile*`, `.dockerignore`, `*.log*`, `.env*`, `coverage/`
   - **ESLint**: `node_modules/`, `dist/`, `build/`, `coverage/`, `*.min.js`
   - **Prettier**: `node_modules/`, `dist/`, `build/`, `coverage/`, `package-lock.json`, `yarn.lock`, `pnpm-lock.yaml`
   - **Terraform**: `.terraform/`, `*.tfstate*`, `*.tfvars`, `.terraform.lock.hcl`
   - **Kubernetes/k8s**: `*.secret.yaml`, `secrets/`, `.kube/`, `kubeconfig*`, `*.key`, `*.crt`

5. Parse tasks.md structure and extract:
   - **Task phases**: Setup, Tests, Core, Integration, Polish
   - **Task dependencies**: Sequential vs parallel execution rules
   - **Task details**: ID, description, file paths, parallel markers [P]
   - **Execution flow**: Order and dependency requirements

6. Execute implementation following the task plan:
   - **Phase-by-phase execution**: Complete each phase before moving to the next
   - **Respect dependencies**: Run sequential tasks in order, parallel tasks [P] can run together
   - **Follow TDD approach**: Execute test tasks before their corresponding implementation tasks
   - **File-based coordination**: Tasks affecting the same files must run sequentially
   - **Validation checkpoints**: Verify each phase completion before proceeding

7. Implementation execution rules:
   - **Setup first**: Initialize project structure, dependencies, configuration
   - **Tests before code**: If you need to write tests for contracts, entities, and integration scenarios
   - **Core development**: Implement models, services, CLI commands, endpoints
   - **Integration work**: Database connections, middleware, logging, external services
   - **Polish and validation**: Unit tests, performance optimization, documentation

8. Progress tracking and error handling:
   - Report progress after each completed task
   - Halt execution if any non-parallel task fails
   - For parallel tasks [P], continue with successful tasks, report failed ones
   - Provide clear error messages with context for debugging
   - Suggest next steps if implementation cannot proceed
   - **IMPORTANT** For completed tasks, make sure to mark the task off as [X] in the tasks file.

9. Completion validation:
   - Verify all required tasks are completed
   - Check that implemented features match the original specification
   - Validate that tests pass and coverage meets requirements
   - Confirm the implementation follows the technical plan

Note: This command assumes a complete task breakdown exists in tasks.md. If tasks are incomplete or missing, suggest running `__SPECKIT_COMMAND_TASKS__` first to regenerate the task list.

## Mandatory Post-Execution Hooks

**You MUST complete this section before reporting completion to the user.**

Check if `.specify/extensions.yml` exists in the project root.
- If it does not exist, or no hooks are registered under `hooks.after_implement`, skip to the Completion Report.
- If it exists, read it and look for entries under the `hooks.after_implement` key.
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

Report final status with summary of completed work.

## Done When

- [ ] All tasks in tasks.md completed and marked `[X]`
- [ ] Implementation validated against specification, plan, and test coverage
- [ ] Extension hooks dispatched or skipped according to the rules in Mandatory Post-Execution Hooks above
- [ ] Completion reported to user with summary of completed work


</details>
