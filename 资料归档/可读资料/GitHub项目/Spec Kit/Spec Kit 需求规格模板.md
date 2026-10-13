# Spec Kit 需求规格模板

把自然语言需求转成结构化规格，整理必要场景、要求和可验证成功条件，并交接澄清或技术规划。

- 来源：Spec Kit
- 资料类型：存档文档
- 存档版本：`0443760542e7`。
- 文档时间：源码提交（存档版）：[2026-09-09](<https://github.com/github/spec-kit/commit/b60057692cd726ea56331ec47f9ebc3e8877d8f7>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/spec-kit/blob/main/templates/commands/specify.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Spec Kit/Spec Kit 需求规格模板.md>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
description: Create or update the feature specification from a natural language feature description.
handoffs:
  - label: Build Technical Plan
    agent: speckit.plan
    prompt: Create a plan for the spec. I am building with...
  - label: Clarify Spec Requirements
    agent: speckit.clarify
    prompt: Clarify specification requirements
    send: true
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

**检查延长钩（指定前）**：
- 检查项目根目录中是否存在`.specify/extensions.yml`。
- 如果存在，则读取它并查找 `hooks.before_specify` 键下的条目
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

用户在触发消息**中的`__SPECKIT_COMMAND_SPECIFY__`之后输入的文本是**功能描述。假设您在此对话中始终可以使用它，即使 `{ARGS}` 字面意思出现在下面。不要要求用户重复它，除非他们提供了空命令。

鉴于该功能描述，请执行以下操作：

1. **为该特征生成一个简洁的短名称**（2-4单词）：
   - 分析特征描述并提取最有意义的关键词
   - 创建一个能够抓住该功能本质的 2-4 单词短名称
   - 尽可能使用操作名词格式（例如“add-user-auth”、“fix- payment-bug”）
   - 保留技术术语和首字母缩略词（OAuth2、API、JWT 等）
   - 保持简洁但具有足够的描述性，以便一目了然地了解该功能
   - 示例：
     - “我想添加用户身份验证”→“用户身份验证”
     - “为 API 实施 OAuth2 集成”→“oauth2-api-integration”
     - “创建分析仪表板”→“分析仪表板”
     - “修复支付处理超时错误”→“修复支付超时”

2. **分支创建**（可选，通过钩子）：

如果 `before_specify` 钩子在上面的预执行检查中成功运行，它将有 created/switched 到 git 分支并输出包含 `BRANCH_NAME` 和 `FEATURE_NUM` 的 JSON。请注意这些值以供参考，但分支名称 ** 而不是 ** 决定了规范目录名称。

如果用户明确提供了 `GIT_BRANCH_NAME`，请将其传递给挂钩，以便分支脚本使用确切的值作为分支名称（绕过所有 prefix/suffix 生成）。

3. **创建spec功能目录**：

规格位于默认的 `specs/` 目录下，除非用户明确提供 `SPECIFY_FEATURE_DIRECTORY`。

**`SPECIFY_FEATURE_DIRECTORY`**的解析顺序：
   1. 如果用户明确提供了 `SPECIFY_FEATURE_DIRECTORY`（例如，通过环境变量、参数或配置），请按原样使用
   2. 否则，在`specs/`下自动生成它：
      - 检查 `.specify/init-options.json` 中的 `feature_numbering`（首选）或 `branch_numbering`（已弃用，仅迁移 - 将在未来版本中删除）
      - 如果`"timestamp"`：前缀是`YYYYMMDD-HHMMSS`（当前时间戳）
      - 如果 `"sequential"` 或不存在：前缀为 `NNN`（扫描 `specs/` 中的现有目录后下一个可用的 3 位数）
      - 构建目录名称：`<prefix>-<short-name>`（例如`003-user-auth`或`20260319-143022-user-auth`）
      - 将 `SPECIFY_FEATURE_DIRECTORY` 设置为 `specs/<directory-name>`
      - 如果使用了 `branch_numbering`（且 `feature_numbering` 不存在），则发出一行警告：“⚠️ init-options.json 中的 `branch_numbering` 已弃用。重命名为 `feature_numbering`。”

**创建目录和规范文件**：
   - `mkdir -p SPECIFY_FEATURE_DIRECTORY`
   - 通过规格套件 preset/template 解析堆栈解析活动 `spec-template`（相当于 `specify preset resolve spec-template`）
   - 将解析后的`spec-template`文件复制到`SPECIFY_FEATURE_DIRECTORY/spec.md`作为起点
   - 将 `SPEC_FILE` 设置为 `SPECIFY_FEATURE_DIRECTORY/spec.md`
   - 保留 `.specify/feature.json` 的解析路径：
     ```json
     {
       "feature_directory": "<resolved feature dir>"
     }
     ```
写入实际解析的目录路径值（例如，`specs/003-user-auth`），而不是文字字符串 `SPECIFY_FEATURE_DIRECTORY`。
 这允许下游命令（`__SPECKIT_COMMAND_PLAN__`、`__SPECKIT_COMMAND_TASKS__` 等）定位功能目录，而无需依赖 git 分支名称约定。

   **IMPORTANT**:
   - 每次 `__SPECKIT_COMMAND_SPECIFY__` 调用只能创建一项功能
   - Spec 目录名称和 git 分支名称是独立的 - 它们可能相同，但这是用户的选择
   - spec 目录和文件始终由该命令创建，而不是由钩子创建

4. 加载已解析的活动 `spec-template` 文件以了解所需的部分。

5. **IF EXISTS**：加载 `/memory/constitution.md` 以了解项目原则和治理约束。

6. 按照这个执行流程：
    1. 从参数中解析用户描述
如果为空：ERROR“未提供功能描述”
    2. 从描述中提取关键概念
识别：参与者、行动、数据、约束
    3. 对于不清楚的方面：
       - 根据背景和行业标准做出明智的猜测
       - 仅标记 [NEEDS CLARIFICATION：具体问题 ] 如果：
         - 该选择会显着影响功能范围或用户体验
         - 存在多种合理的解释，具有不同的含义
         - 不存在合理的违约
       - **LIMIT：最大 3 [NEEDS CLARIFICATION] 标记总数 **
       - 按影响优先澄清：范围 > security/privacy > 用户体验 > 技术细节
    4. 填写用户场景和测试部分
如果没有明确的用户流程：ERROR“无法确定用户场景”
    5. 生成功能需求
每个要求都必须是可测试的
 对于未指定的细节使用合理的默认值（假设部分中的文档假设）
    6. 定义成功标准
创建可衡量的、与技术无关的成果
 包括定量指标（时间、性能、数量）和定性指标（用户满意度、任务完成情况）
 每个标准都必须可验证，无需实施细节
    7. 识别关键实体（如果涉及数据）
    8. 返回：SUCCESS（规格已准备好规划）

7. 使用模板结构将规范写入 SPEC_FILE，用从功能描述（参数）派生的具体细节替换占位符，同时保留部分顺序和标题。

8. **规范质量验证**：编写初始规范后，根据质量标准对其进行验证：

a. **创建规格质量检查表**：使用具有以下验证项目的检查表模板结构在`SPECIFY_FEATURE_DIRECTORY/checklists/requirements.md`生成检查表文件：

      ```markdown
      # Specification Quality Checklist: [FEATURE NAME]

      **Purpose**: Validate specification completeness and quality before proceeding to planning
      **Created**: [DATE]
      **Feature**: [Link to spec.md]

      ## Content Quality

      - [ ] No implementation details (languages, frameworks, APIs)
      - [ ] Focused on user value and business needs
      - [ ] Written for non-technical stakeholders
      - [ ] All mandatory sections completed

      ## Requirement Completeness

      - [ ] No [NEEDS CLARIFICATION] markers remain
      - [ ] Requirements are testable and unambiguous
      - [ ] Success criteria are measurable
      - [ ] Success criteria are technology-agnostic (no implementation details)
      - [ ] All acceptance scenarios are defined
      - [ ] Edge cases are identified
      - [ ] Scope is clearly bounded
      - [ ] Dependencies and assumptions identified

      ## Feature Readiness

      - [ ] All functional requirements have clear acceptance criteria
      - [ ] User scenarios cover primary flows
      - [ ] Feature meets measurable outcomes defined in Success Criteria
      - [ ] No implementation details leak into specification

      ## Notes

      - Items marked incomplete require spec updates before `__SPECKIT_COMMAND_CLARIFY__` or `__SPECKIT_COMMAND_PLAN__`
      ```

b. **运行验证检查**：根据每个清单项目检查规范：
      - 对于每个项目，确定它是通过还是失败
      - 记录发现的具体问题（引用相关规范部分）

c. **处理验证结果**：

      - **如果所有项目都通过**：将清单标记为完成并继续执行强制执行后挂钩部分

      - **如果项目失败（不包括[NEEDS CLARIFICATION]）**：
        1. 列出失败的项目和具体问题
        2. 更新规范以解决每个问题
        3. 重新运行验证，直到所有项目都通过（最大 3 迭代）
        4. 如果在 3 迭代后仍然失败，请在清单注释中记录剩余问题并警告用户

      - **如果[NEEDS CLARIFICATION]标记仍然存在**：
        1. 从规范中提取所有 [NEEDS CLARIFICATION: ...] 标记
        2. **LIMIT CHECK**：如果存在超过 3 个标记，则仅保留最关键的 3（受 scope/security/UX 影响）并对其余标记进行明智的猜测
        3. 对于所需的每项说明（最大 3），以以下格式向用户提供选项：

           ```markdown
           ## Question [N]: [Topic]

           **Context**: [Quote relevant spec section]

           **What we need to know**: [Specific question from NEEDS CLARIFICATION marker]

           **Suggested Answers**:

           | Option | Answer | Implications |
           |--------|--------|--------------|
           | A      | [First suggested answer] | [What this means for the feature] |
           | B      | [Second suggested answer] | [What this means for the feature] |
           | C      | [Third suggested answer] | [What this means for the feature] |
           | Custom | Provide your own answer | [Explain how to provide custom input] |

           **Your choice**: _[Wait for user response]_
           ```

        4. **CRITICAL - 表格格式**：确保降价表格格式正确：
           - 使用一致的间距并对齐管道
           - 每个单元格内容周围应有空格：`| Content |` 而不是 `|Content|`
           - 标头分隔符必须至少包含 3 破折号：`|--------|`
           - 测试表格在 Markdown 预览中是否正确呈现
        5. 按顺序对问题编号（Q1、Q2、Q3 - 总计最多 3）
        6. 在等待答复之前将所有问题一起提出
        7. 等待用户回答所有问题的选择（例如，“Q1：A，Q2：自定义 - [详细信息]，Q3：B”）
        8. 通过将每个 [NEEDS CLARIFICATION] 标记替换为用户选择或提供的答案来更新规范
        9. 解决所有问题后重新运行验证

d. **更新清单**：每次验证迭代后，使用当前的pass/fail状态更新清单文件

## 强制执行后挂钩

**您MUST在向用户报告完成情况之前完成此部分。**

检查项目根目录中是否存在`.specify/extensions.yml`。
- 如果不存在，或者`hooks.after_specify`下没有注册任何钩子，则跳至完成报告。
- 如果存在，请读取它并查找 `hooks.after_specify` 键下的条目。
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

向用户报告完成情况：
- `SPECIFY_FEATURE_DIRECTORY` — 功能目录路径
- `SPEC_FILE` — 规格文件路径
- 检查表结果摘要
- 下一阶段的准备情况（`__SPECKIT_COMMAND_CLARIFY__` 或 `__SPECKIT_COMMAND_PLAN__`）

**NOTE:** 分支创建由 `before_specify` 挂钩（git 扩展）处理。 Spec 目录和文件创建始终由该核心命令处理。

## 快速指南

- 关注**WHAT**用户需求和**WHY**。
- 避免 HOW 实现（无技术堆栈、API、代码结构）。
- 为业务利益相关者而不是开发人员编写。
- DO NOT 创建嵌入在规范中的任何清单。这将是一个单独的命令。

### 部分要求

- **必填部分**：每个功能都必须填写
- **可选部分**：仅在与功能相关时包含
- 当某个部分不适用时，将其完全删除（不要保留为“N/A”）

### 对于AI一代

从用户提示创建此规范时：

1. **做出明智的猜测**：使用上下文、行业标准和常见模式来填补空白
2. **文档假设**：在假设部分记录合理的默认值
3. **限制说明**：最大3 [NEEDS CLARIFICATION]标记 - 仅用于以下关键决策：
   - 显着影响功能范围或用户体验
   - 有多种合理的解释，具有不同的含义
   - 缺乏任何合理的违约
4. **优先澄清**：范围 > security/privacy > 用户体验 > 技术细节
5. **像测试人员一样思考**：每个模糊的要求都应该失败“可测试且明确”清单项
6. **需要澄清的常见区域**（仅当不存在合理默认值时）：
   - 功能范围和边界（include/exclude特定用例）
   - 用户类型和权限（如果可能有多种相互冲突的解释）
   - Security/compliance 要求（当 legally/financially 重要时）

**合理默认值的示例**（不要问这些）：

- 数据保留：该领域的行业标准实践
- 性能目标：标准 web/mobile 应用程序期望（除非另有说明）
- 错误处理：具有适当后备功能的用户友好消息
- 身份验证方法：基于标准会话或适用于 Web 应用程序的 OAuth2
- 集成模式：使用适合项目的模式（REST/GraphQL 用于 Web 服务、函数调用用于库、CLI 参数用于工具等）

### 成功标准指南

成功的标准必须是：

1. **可衡量**：包括具体指标（时间、百分比、计数、比率）
2. **与技术无关**：没有提及框架、语言、数据库或工具
3. **以用户为中心**：从user/business角度描述结果，而不是系统内部
4. **可验证**：可以在不知道实现细节的情况下tested/validated

**很好的例子**：

- “用户可以在 3 分钟内完成结账”
- “系统支持10,000并发用户”
- “95% 次搜索在 1 秒内返回结果”
- “任务完成率提高了 40%”

**不好的例子**（以实现为重点）：

- “API 响应时间低于 200 毫秒”（技术性太强，使用“用户立即看到结果”）
- “数据库可以处理1000 TPS”（实现细节，使用面向用户的指标）
- “React 组件高效渲染”（特定于框架）
- “Redis 缓存命中率高于 80%”（特定于技术）

## 完成时间

- [ ] 写入 `SPEC_FILE` 的规范并根据质量检查表进行验证
- [ ] 根据上面强制执行后挂钩中的规则调度或跳过扩展挂钩
- [ ] 向用户报告完成情况，包括功能目录、规范文件路径和清单结果


## 原文对照

<details>
<summary>展开完整原文</summary>

## User Input

```text
$ARGUMENTS
```

You **MUST** consider the user input before proceeding (if not empty).

## Pre-Execution Checks

**Check for extension hooks (before specification)**:
- Check if `.specify/extensions.yml` exists in the project root.
- If it exists, read it and look for entries under the `hooks.before_specify` key
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

The text the user typed after `__SPECKIT_COMMAND_SPECIFY__` in the triggering message **is** the feature description. Assume you always have it available in this conversation even if `{ARGS}` appears literally below. Do not ask the user to repeat it unless they provided an empty command.

Given that feature description, do this:

1. **Generate a concise short name** (2-4 words) for the feature:
   - Analyze the feature description and extract the most meaningful keywords
   - Create a 2-4 word short name that captures the essence of the feature
   - Use action-noun format when possible (e.g., "add-user-auth", "fix-payment-bug")
   - Preserve technical terms and acronyms (OAuth2, API, JWT, etc.)
   - Keep it concise but descriptive enough to understand the feature at a glance
   - Examples:
     - "I want to add user authentication" → "user-auth"
     - "Implement OAuth2 integration for the API" → "oauth2-api-integration"
     - "Create a dashboard for analytics" → "analytics-dashboard"
     - "Fix payment processing timeout bug" → "fix-payment-timeout"

2. **Branch creation** (optional, via hook):

   If a `before_specify` hook ran successfully in the Pre-Execution Checks above, it will have created/switched to a git branch and output JSON containing `BRANCH_NAME` and `FEATURE_NUM`. Note these values for reference, but the branch name does **not** dictate the spec directory name.

   If the user explicitly provided `GIT_BRANCH_NAME`, pass it through to the hook so the branch script uses the exact value as the branch name (bypassing all prefix/suffix generation).

3. **Create the spec feature directory**:

   Specs live under the default `specs/` directory unless the user explicitly provides `SPECIFY_FEATURE_DIRECTORY`.

   **Resolution order for `SPECIFY_FEATURE_DIRECTORY`**:
   1. If the user explicitly provided `SPECIFY_FEATURE_DIRECTORY` (e.g., via environment variable, argument, or configuration), use it as-is
   2. Otherwise, auto-generate it under `specs/`:
      - Check `.specify/init-options.json` for `feature_numbering` (preferred) or `branch_numbering` (deprecated, migration only — will be removed in a future release)
      - If `"timestamp"`: prefix is `YYYYMMDD-HHMMSS` (current timestamp)
      - If `"sequential"` or absent: prefix is `NNN` (next available 3-digit number after scanning existing directories in `specs/`)
      - Construct the directory name: `<prefix>-<short-name>` (e.g., `003-user-auth` or `20260319-143022-user-auth`)
      - Set `SPECIFY_FEATURE_DIRECTORY` to `specs/<directory-name>`
      - If `branch_numbering` was used (and `feature_numbering` was absent), emit a one-line warning: "⚠️ `branch_numbering` in init-options.json is deprecated. Rename to `feature_numbering`."

   **Create the directory and spec file**:
   - `mkdir -p SPECIFY_FEATURE_DIRECTORY`
   - Resolve the active `spec-template` through the Spec Kit preset/template resolution stack (equivalent to `specify preset resolve spec-template`)
   - Copy the resolved `spec-template` file to `SPECIFY_FEATURE_DIRECTORY/spec.md` as the starting point
   - Set `SPEC_FILE` to `SPECIFY_FEATURE_DIRECTORY/spec.md`
   - Persist the resolved path to `.specify/feature.json`:
     ```json
     {
       "feature_directory": "<resolved feature dir>"
     }
     ```
     Write the actual resolved directory path value (for example, `specs/003-user-auth`), not the literal string `SPECIFY_FEATURE_DIRECTORY`.
     This allows downstream commands (`__SPECKIT_COMMAND_PLAN__`, `__SPECKIT_COMMAND_TASKS__`, etc.) to locate the feature directory without relying on git branch name conventions.

   **IMPORTANT**:
   - You must only create one feature per `__SPECKIT_COMMAND_SPECIFY__` invocation
   - The spec directory name and the git branch name are independent — they may be the same but that is the user's choice
   - The spec directory and file are always created by this command, never by the hook

4. Load the resolved active `spec-template` file to understand required sections.

5. **IF EXISTS**: Load `/memory/constitution.md` for project principles and governance constraints.

6. Follow this execution flow:
    1. Parse user description from arguments
       If empty: ERROR "No feature description provided"
    2. Extract key concepts from description
       Identify: actors, actions, data, constraints
    3. For unclear aspects:
       - Make informed guesses based on context and industry standards
       - Only mark with [NEEDS CLARIFICATION: specific question] if:
         - The choice significantly impacts feature scope or user experience
         - Multiple reasonable interpretations exist with different implications
         - No reasonable default exists
       - **LIMIT: Maximum 3 [NEEDS CLARIFICATION] markers total**
       - Prioritize clarifications by impact: scope > security/privacy > user experience > technical details
    4. Fill User Scenarios & Testing section
       If no clear user flow: ERROR "Cannot determine user scenarios"
    5. Generate Functional Requirements
       Each requirement must be testable
       Use reasonable defaults for unspecified details (document assumptions in Assumptions section)
    6. Define Success Criteria
       Create measurable, technology-agnostic outcomes
       Include both quantitative metrics (time, performance, volume) and qualitative measures (user satisfaction, task completion)
       Each criterion must be verifiable without implementation details
    7. Identify Key Entities (if data involved)
    8. Return: SUCCESS (spec ready for planning)

7. Write the specification to SPEC_FILE using the template structure, replacing placeholders with concrete details derived from the feature description (arguments) while preserving section order and headings.

8. **Specification Quality Validation**: After writing the initial spec, validate it against quality criteria:

   a. **Create Spec Quality Checklist**: Generate a checklist file at `SPECIFY_FEATURE_DIRECTORY/checklists/requirements.md` using the checklist template structure with these validation items:

      ```markdown
      # Specification Quality Checklist: [FEATURE NAME]

      **Purpose**: Validate specification completeness and quality before proceeding to planning
      **Created**: [DATE]
      **Feature**: [Link to spec.md]

      ## Content Quality

      - [ ] No implementation details (languages, frameworks, APIs)
      - [ ] Focused on user value and business needs
      - [ ] Written for non-technical stakeholders
      - [ ] All mandatory sections completed

      ## Requirement Completeness

      - [ ] No [NEEDS CLARIFICATION] markers remain
      - [ ] Requirements are testable and unambiguous
      - [ ] Success criteria are measurable
      - [ ] Success criteria are technology-agnostic (no implementation details)
      - [ ] All acceptance scenarios are defined
      - [ ] Edge cases are identified
      - [ ] Scope is clearly bounded
      - [ ] Dependencies and assumptions identified

      ## Feature Readiness

      - [ ] All functional requirements have clear acceptance criteria
      - [ ] User scenarios cover primary flows
      - [ ] Feature meets measurable outcomes defined in Success Criteria
      - [ ] No implementation details leak into specification

      ## Notes

      - Items marked incomplete require spec updates before `__SPECKIT_COMMAND_CLARIFY__` or `__SPECKIT_COMMAND_PLAN__`
      ```

   b. **Run Validation Check**: Review the spec against each checklist item:
      - For each item, determine if it passes or fails
      - Document specific issues found (quote relevant spec sections)

   c. **Handle Validation Results**:

      - **If all items pass**: Mark checklist complete and proceed to the Mandatory Post-Execution Hooks section

      - **If items fail (excluding [NEEDS CLARIFICATION])**:
        1. List the failing items and specific issues
        2. Update the spec to address each issue
        3. Re-run validation until all items pass (max 3 iterations)
        4. If still failing after 3 iterations, document remaining issues in checklist notes and warn user

      - **If [NEEDS CLARIFICATION] markers remain**:
        1. Extract all [NEEDS CLARIFICATION: ...] markers from the spec
        2. **LIMIT CHECK**: If more than 3 markers exist, keep only the 3 most critical (by scope/security/UX impact) and make informed guesses for the rest
        3. For each clarification needed (max 3), present options to user in this format:

           ```markdown
           ## Question [N]: [Topic]

           **Context**: [Quote relevant spec section]

           **What we need to know**: [Specific question from NEEDS CLARIFICATION marker]

           **Suggested Answers**:

           | Option | Answer | Implications |
           |--------|--------|--------------|
           | A      | [First suggested answer] | [What this means for the feature] |
           | B      | [Second suggested answer] | [What this means for the feature] |
           | C      | [Third suggested answer] | [What this means for the feature] |
           | Custom | Provide your own answer | [Explain how to provide custom input] |

           **Your choice**: _[Wait for user response]_
           ```

        4. **CRITICAL - Table Formatting**: Ensure markdown tables are properly formatted:
           - Use consistent spacing with pipes aligned
           - Each cell should have spaces around content: `| Content |` not `|Content|`
           - Header separator must have at least 3 dashes: `|--------|`
           - Test that the table renders correctly in markdown preview
        5. Number questions sequentially (Q1, Q2, Q3 - max 3 total)
        6. Present all questions together before waiting for responses
        7. Wait for user to respond with their choices for all questions (e.g., "Q1: A, Q2: Custom - [details], Q3: B")
        8. Update the spec by replacing each [NEEDS CLARIFICATION] marker with the user's selected or provided answer
        9. Re-run validation after all clarifications are resolved

   d. **Update Checklist**: After each validation iteration, update the checklist file with current pass/fail status

## Mandatory Post-Execution Hooks

**You MUST complete this section before reporting completion to the user.**

Check if `.specify/extensions.yml` exists in the project root.
- If it does not exist, or no hooks are registered under `hooks.after_specify`, skip to the Completion Report.
- If it exists, read it and look for entries under the `hooks.after_specify` key.
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

Report completion to the user with:
- `SPECIFY_FEATURE_DIRECTORY` — the feature directory path
- `SPEC_FILE` — the spec file path
- Checklist results summary
- Readiness for the next phase (`__SPECKIT_COMMAND_CLARIFY__` or `__SPECKIT_COMMAND_PLAN__`)

**NOTE:** Branch creation is handled by the `before_specify` hook (git extension). Spec directory and file creation are always handled by this core command.

## Quick Guidelines

- Focus on **WHAT** users need and **WHY**.
- Avoid HOW to implement (no tech stack, APIs, code structure).
- Written for business stakeholders, not developers.
- DO NOT create any checklists that are embedded in the spec. That will be a separate command.

### Section Requirements

- **Mandatory sections**: Must be completed for every feature
- **Optional sections**: Include only when relevant to the feature
- When a section doesn't apply, remove it entirely (don't leave as "N/A")

### For AI Generation

When creating this spec from a user prompt:

1. **Make informed guesses**: Use context, industry standards, and common patterns to fill gaps
2. **Document assumptions**: Record reasonable defaults in the Assumptions section
3. **Limit clarifications**: Maximum 3 [NEEDS CLARIFICATION] markers - use only for critical decisions that:
   - Significantly impact feature scope or user experience
   - Have multiple reasonable interpretations with different implications
   - Lack any reasonable default
4. **Prioritize clarifications**: scope > security/privacy > user experience > technical details
5. **Think like a tester**: Every vague requirement should fail the "testable and unambiguous" checklist item
6. **Common areas needing clarification** (only if no reasonable default exists):
   - Feature scope and boundaries (include/exclude specific use cases)
   - User types and permissions (if multiple conflicting interpretations possible)
   - Security/compliance requirements (when legally/financially significant)

**Examples of reasonable defaults** (don't ask about these):

- Data retention: Industry-standard practices for the domain
- Performance targets: Standard web/mobile app expectations unless specified
- Error handling: User-friendly messages with appropriate fallbacks
- Authentication method: Standard session-based or OAuth2 for web apps
- Integration patterns: Use project-appropriate patterns (REST/GraphQL for web services, function calls for libraries, CLI args for tools, etc.)

### Success Criteria Guidelines

Success criteria must be:

1. **Measurable**: Include specific metrics (time, percentage, count, rate)
2. **Technology-agnostic**: No mention of frameworks, languages, databases, or tools
3. **User-focused**: Describe outcomes from user/business perspective, not system internals
4. **Verifiable**: Can be tested/validated without knowing implementation details

**Good examples**:

- "Users can complete checkout in under 3 minutes"
- "System supports 10,000 concurrent users"
- "95% of searches return results in under 1 second"
- "Task completion rate improves by 40%"

**Bad examples** (implementation-focused):

- "API response time is under 200ms" (too technical, use "Users see results instantly")
- "Database can handle 1000 TPS" (implementation detail, use user-facing metric)
- "React components render efficiently" (framework-specific)
- "Redis cache hit rate above 80%" (technology-specific)

## Done When

- [ ] Specification written to `SPEC_FILE` and validated against quality checklist
- [ ] Extension hooks dispatched or skipped according to the rules in Mandatory Post-Execution Hooks above
- [ ] Completion reported to user with feature directory, spec file path, and checklist results


</details>
