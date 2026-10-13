# Spec Kit 需求澄清模板

识别规格中的不明确内容，通过有针对性的问题补齐并回写答案，包含前后检查与完成条件。

- 来源：Spec Kit
- 资料类型：存档文档
- 存档版本：`0443760542e7`。
- 文档时间：源码提交（存档版）：[2026-09-15](<https://github.com/github/spec-kit/commit/98eb2fb4fc9f1ef995f03f233e52c1a6e0e11113>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/spec-kit/blob/main/templates/commands/clarify.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Spec Kit/Spec Kit 需求澄清模板.md>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
description: Identify underspecified areas in the current feature spec by asking up to 5 highly targeted clarification questions and encoding answers back into the spec.
handoffs:
  - label: Build Technical Plan
    agent: speckit.plan
    prompt: Create a plan for the spec. I am building with...
scripts:
   sh: scripts/bash/check-prerequisites.sh --json --paths-only
   ps: scripts/powershell/check-prerequisites.ps1 -Json -PathsOnly
   py: scripts/python/check_prerequisites.py --json --paths-only
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

**检查延长钩（澄清之前）**：
- 检查项目根目录中是否存在`.specify/extensions.yml`。
- 如果存在，则读取它并查找 `hooks.before_clarify` 键下的条目
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

目标：检测并减少活动功能规范中的歧义或缺失决策点，并将说明直接记录在规范文件中。

注意：此澄清工作流程预计将调用 `__SPECKIT_COMMAND_PLAN__` 来运行（并完成）BEFORE。如果用户明确声明他们正在跳过澄清（例如，探索性峰值），您可以继续，但必须警告下游返工风险增加。

执行步骤：

1. 从存储库根 ** 运行 `{SCRIPT}` 一次 **（组合 `--json --paths-only` 模式/`-Json -PathsOnly`）。解析最小的 JSON 有效负载字段：
   - `FEATURE_DIR`
   - `FEATURE_SPEC`
   - （可以选择捕获 `IMPL_PLAN`、`TASKS` 以用于将来的链式流。）
   - 如果JSON解析失败，则中止并指示用户重新运行`__SPECKIT_COMMAND_SPECIFY__`或验证功能分支环境。
   - 对于像“I'm Groot”这样的参数中的单引号，请使用转义语法：例如 'I'\''m Groot' （或者如果可能的话使用双引号：“I'm Groot”）。

2. **IF EXISTS**：加载 `/memory/constitution.md` 以了解项目原则和治理约束。

3. 加载当前规格文件。使用此分类法执行结构化歧义和覆盖范围扫描。对于每个类别，标记状态：清除/部分/缺失。生成用于确定优先级的内部覆盖图（除非不会提出问题，否则不要输出原始图）。

功能范围和行为：
   - 核心用户目标和成功标准
   - 明确的超出范围的声明
   - 用户角色/角色区分

领域和数据模型：
   - 实体、属性、关系
   - 身份和唯一性规则
   - Lifecycle/state 转换
   - 数据量/规模假设

互动&UX流程：
   - 关键用户旅程/序列
   - Error/empty/loading 状态
   - 辅助功能或本地化说明

非功能性质量属性：
   - 性能（延迟、吞吐量目标）
   - 可扩展性（horizontal/vertical，限制）
   - 可靠性和可用性（正常运行时间、恢复预期）
   - 可观察性（日志记录、指标、跟踪信号）
   - 安全和隐私（authN/Z、数据保护、威胁假设）
   - 合规/监管限制（如果有）

集成和外部依赖项：
   - 外部 services/APIs 和故障模式
   - 数据import/export格式
   - Protocol/versioning 假设

边缘情况和故障处理：
   - 负面情景
   - 速率限制/节流
   - 冲突解决（例如并发编辑）

限制和权衡：
   - 技术限制（语言、存储、托管）
   - 明确的权衡或拒绝的替代方案

术语和一致性：
   - 规范术语
   - 避免使用的同义词/不推荐使用的术语

完成信号：
   - 验收标准可测试性
   - 完成风格指标的可衡量定义

杂项/占位符：
   - TODO 标记/未解决的决定
   - 缺乏量化的模糊形容词（“稳健”、“直观”）

对于每个状态为“部分”或“缺失”的类别，添加候选问题机会，除非：
   - 澄清不会实质性改变实施或验证策略
   - 该项目专门涉及实现方法、技术堆栈比较或任务分解（内部注释）

4. （内部）生成候选人澄清问题的优先队列（最大 5）。 NOT一次全部输出吗？应用这些约束：
    - 整个会话的总问题数最多为 5 个。
    - 每个问题必须用 EITHER 回答：
       - 简短的多项选择（2–5 不同的、互斥的选项），OR
       - 一个单词/简短短语的答案（明确限制：“用 <=5 个单词回答”）。
    - 仅包括其答案对架构、数据建模、任务分解、测试设计、UX 行为、操作准备情况或合规性验证产生重大影响的问题。
    - 确保类别覆盖范围平衡：首先尝试覆盖影响最大的未解决类别；当一个高影响领域（例如安全态势）未解决时，避免提出两个低影响问题。
    - 排除已经回答的问题、琐碎的风格偏好或计划级执行细节（除非阻碍正确性）。
    - 支持减少下游返工风险或防止验收测试不一致的澄清。
    - 如果超过 5 个类别仍未解决，请通过（影响 * 不确定性）启发式选择顶部 5。

5. 顺序提问循环（交互式）：
    - 一次提出 EXACTLY ONE 问题。
    - **问题写作质量（适用于每个问题，MC或简答）：**
       - 以 `**Question:**` 开头，后跟一个以 `?` 结尾的完整疑问句。 `?` 之前的问题文本本身必须有意义。
       - NEVER 使用主题标签、章节标题或要求 ID 作为问题本身。例如，`Acceptance device/runtime matrix (FR-023)` 是 INVALID——它是一个标签，而不是一个问题。
       - 在 `?` 之后，唯一允许的后缀是可选的带括号的 requirement/question id。确切格式：`**Question:** <interrogative>?` 或 `**Question:** <interrogative>? (FR-023)`。切勿将 id 放在 `?` 之前，并且切勿使用 id（单独或与主题标签一起）作为整个提示。
       - 紧接着问题行之后，在 recommendation/options. 之前添加一个简单语言的“为什么重要”句子（接受或运输的股份）
       - 使用日常用语；仅在同一句子中定义时才引入术语。自查：不了解规格套件的读者必须能够仅从问题行中进行回答。简洁就好；神秘标签则不然。
    - 对于多项选择题：
       - **分析所有选项**并根据以下条件确定**最合适的选项**：
          - 项目类型的最佳实践
          - 类似实现中的常见模式
          - 降低风险（安全性、性能、可维护性）
          - 与规范中可见的任何明确的项目目标或约束保持一致
       - 在顶部突出显示您的 ** 推荐选项 **，并提供清晰的推理（1-2 句子解释为什么这是最佳选择）。
       - 格式为：`**Recommended:** Option [X] - <reasoning>`
       - 然后将所有选项渲染为 Markdown 表格：

       |选项|描述|
       |--------|-------------|
       | A | <Option A description> |
       | B | <Option B description> |
       | C |<Option C description>（根据需要添加D/E直至5）|
       |短|提供不同的简短答案（<=5 个单词）（仅在适合自由格式替代方案时才包含）|

       - 在表后添加：`You can reply with the option letter (e.g., "A"), accept the recommendation by saying "yes" or "recommended", or provide your own short answer.`
    - 对于简答风格（没有有意义的离散选项）：
       - 根据最佳实践和上下文提供您的 ** 建议答案 **。
       - 格式为：`**Suggested:** <your proposed answer> - <brief reasoning>`
       - 然后输出：`Format: Short answer (<=5 words). You can accept the suggestion by saying "yes" or "suggested", or provide your own answer.`
    - 用户回答后：
       - 如果用户回答“是”、“推荐”或“建议”，请使用您之前提到的 recommendation/suggestion 作为答案。
       - 否则，验证答案映射到一个选项或符合 <=5 单词约束。
       - 如果有歧义，请要求快速消除歧义（计数仍然属于同一问题；不要前进）。
       - 一旦满意，将其记录在工作内存中（尚未写入磁盘）并移至下一个排队的问题。
    - 在以下情况下停止询问进一步的问题：
       - 所有关键的歧义都已提前解决（剩余的排队项目变得不必要），OR
       - 用户信号完成（“完成”、“好”、“不再”），OR
       - 您到达5提出问题。
    - 切勿提前透露未来排队的问题。
    - 如果开始时不存在有效问题，请立即报告没有严重歧义。

6. EACH接受答案后的集成（增量更新方法）：
    - 维护规范的内存表示（在启动时加载一次）以及原始文件内容。
    - 对于本次会议的第一个综合答案：
       - 确保存在 `## Clarifications` 部分（如果缺少，请在每个规范模板的最高级别 contextual/overview 部分之后创建）。
       - 在其下，创建（如果不存在）今天的 `### Session YYYY-MM-DD` 副标题。
    - 接受后立即添加项目符号行：`- Q: <question> → A: <final answer>`。
    - 然后立即将澄清应用到最合适的部分：
       - 功能歧义 → 在功能需求中更新或添加项目符号。
       - 用户交互/参与者区别 → 更新用户故事或参与者小节（如果存在），明确角色、约束或场景。
       - 数据形状/实体→更新数据模型（添加字段、类型、关系）保留顺序；简洁地注意添加的约束。
       - 非功能性约束→成功标准>可衡量结果中的Add/modify可衡量标准（将模糊形容词转换为度量或明确目标）。
       - 边缘情况/负流 → 在边缘情况/错误处理下添加新项目符号（或者如果模板为其提供占位符，则创建此类小节）。
       - 术语冲突→规范跨规范的术语；仅在必要时添加 `(formerly referred to as "X")` 一次来保留原始内容。
    - 如果澄清使之前的含糊不清的陈述无效，则替换该陈述而不是重复；不留下任何过时的矛盾文字。
    - 每次集成时保存规格文件 AFTER，以尽量减少上下文丢失（原子覆盖）的风险。
    - 保留格式：不要对不相关的部分重新排序；保持标题层次结构完整。
    - 保持每个插入的澄清最少且可测试（避免叙述漂移）。

7. 验证（在 EACH 写入加上最终通过后执行）：
   - 澄清会议仅包含每个接受的答案的一个项目符号（无重复）。
   - 提出（接受）的问题总数 ≤ 5。
   - 更新的部分不包含新答案要解决的挥之不去的模糊占位符。
   - 不存在任何矛盾的早期陈述（扫描现已删除的无效替代选项）。
   - Markdown 结构有效；只允许新标题：`## Clarifications`、`### Session YYYY-MM-DD`。
   - 术语一致性：所有更新部分使用相同的规范术语。

8. 将更新后的规格写回`FEATURE_SPEC`。

9. **重新验证规格质量检查表**（如果存在）：
   - 检查`FEATURE_DIR/checklists/requirements.md`是否存在。
   - 如果NOT存在，则直接跳过此步骤。
   - 如果存在：
     1. 阅读清单文件。
     2. 识别代码围栏之外的所有 GitHub 任务列表复选框行 - 与 `- [ ]`、`- [x]` 或 `- [X]`（不区分大小写，允许嵌套项目的前导空格）匹配的行。忽略所有其他内容（标题、注释、非复选框项目符号、元数据）。
     3. 对于每个复选框行，将其当前标记状态（选中或未选中）和项目文本记录到快照前列表中。
     4. 根据 ** 更新的 ** 规范（刚刚在步骤 7 中保存的版本）重新评估每个复选框项目。
     5. 对于每个复选框项，仅在 checked/unchecked 状态实际更改时更新：
        - 如果该项目现在通过并且未选中：将 `[ ]` 更改为 `[x]`。
        - 如果该项目现在失败并已被选中：将 `[x]`/`[X]` 更改为 `[ ]`。
        - 如果状态未更改：按原样保留标记（保留现有情况以避免外观差异）。
     6. 保存更新的清单文件。 **仅切换状态已更改的复选框行的`[ ]`/`[x]`标记部分。**所有其他文件内容 - 标题、元数据、注释、行排序、空格 - 必须保持不变，以避免嘈杂的差异。
     7. 将之前的快照与当前状态进行比较，以计算完成报告的三个列表：
        - **新通过**：从未选中更改为选中的项目。
        - **回归**：从选中更改为未选中的项目。
        - **仍未选中**：仍未选中的项目。
     8. 将 before/after 通过计数记录为 checked/total 复选框项目（例如，“12/16 → 15/16 项目通过”）。

行为规则：

- 如果没有发现有意义的歧义（或者所有潜在问题的影响都较小），请回答：“没有发现值得正式澄清的关键歧义。”并建议继续进行。
- 如果规范文件丢失，请指示用户首先运行`__SPECKIT_COMMAND_SPECIFY__`（不要在此处创建新规范）。
- 提出的问题总数切勿超过 5（单个问题的澄清重试不算作新问题）。
- 避免推测性的技术堆栈问题，除非不存在会妨碍功能清晰度。
- 尊重用户提前终止信号（“停止”、“完成”、“继续”）。
- 如果由于全面覆盖而没有提出任何问题，请输出紧凑的覆盖范围摘要（所有类别均清晰），然后建议前进。
- 如果已达到配额但仍有未解决的高影响类别，请明确将其标记为“推迟”并说明理由。

优先级上下文：{ARGS}

## 强制执行后挂钩

**您MUST在向用户报告完成情况之前完成此部分。**

检查项目根目录中是否存在`.specify/extensions.yml`。
- 如果不存在，或者`hooks.after_clarify`下没有注册任何钩子，则跳至完成报告。
- 如果存在，请读取它并查找 `hooks.after_clarify` 键下的条目。
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

报告完成（提问循环结束或提前终止后）：
- 提出和回答的问题数量。
- 更新规范的路径。
- 触及的部分（列出名称）。
- 规格质量检查表状态（如果 `FEATURE_DIR/checklists/requirements.md` 已重新验证）：显示 before/after 通过计数（例如，“规格质量检查表：12/16 → 15/16 项目通过”）并列出所有更改状态的项目 —包括新检查的（未检查→检查）和任何回归（检查→未检查）。如果有任何项目仍未选中，请将其列为需要注意的区域。
- 覆盖范围汇总表列出了每个分类类别的状态：已解决（已 Partial/Missing 并已解决）、已推迟（超出问题配额，或剩余项目具体为实施方法、技术堆栈比较或任务分解）、清除（已足够）、未完成（仍为 Partial/Missing，但影响较低）。
- 如果仍有任何未完成或延期的，建议是否继续执行 `__SPECKIT_COMMAND_PLAN__` 或稍后在计划后再次运行 `__SPECKIT_COMMAND_CLARIFY__`。
- 建议的下一个命令。

## 完成时间

- [ ] 已识别规范歧义并将澄清集成到规范文件中
- [ ] 根据更新的规格重新验证规格质量检查表（如果 `FEATURE_DIR/checklists/requirements.md` 存在）
- [ ] 根据上面强制执行后挂钩中的规则调度或跳过扩展挂钩
- [ ] 向用户报告完成情况，包括回答的问题、涉及的部分、清单状态和覆盖范围摘要


## 原文对照

<details>
<summary>展开完整原文</summary>

## User Input

```text
$ARGUMENTS
```

You **MUST** consider the user input before proceeding (if not empty).

## Pre-Execution Checks

**Check for extension hooks (before clarification)**:
- Check if `.specify/extensions.yml` exists in the project root.
- If it exists, read it and look for entries under the `hooks.before_clarify` key
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

Goal: Detect and reduce ambiguity or missing decision points in the active feature specification and record the clarifications directly in the spec file.

Note: This clarification workflow is expected to run (and be completed) BEFORE invoking `__SPECKIT_COMMAND_PLAN__`. If the user explicitly states they are skipping clarification (e.g., exploratory spike), you may proceed, but must warn that downstream rework risk increases.

Execution steps:

1. Run `{SCRIPT}` from repo root **once** (combined `--json --paths-only` mode / `-Json -PathsOnly`). Parse minimal JSON payload fields:
   - `FEATURE_DIR`
   - `FEATURE_SPEC`
   - (Optionally capture `IMPL_PLAN`, `TASKS` for future chained flows.)
   - If JSON parsing fails, abort and instruct user to re-run `__SPECKIT_COMMAND_SPECIFY__` or verify feature branch environment.
   - For single quotes in args like "I'm Groot", use escape syntax: e.g 'I'\''m Groot' (or double-quote if possible: "I'm Groot").

2. **IF EXISTS**: Load `/memory/constitution.md` for project principles and governance constraints.

3. Load the current spec file. Perform a structured ambiguity & coverage scan using this taxonomy. For each category, mark status: Clear / Partial / Missing. Produce an internal coverage map used for prioritization (do not output raw map unless no questions will be asked).

   Functional Scope & Behavior:
   - Core user goals & success criteria
   - Explicit out-of-scope declarations
   - User roles / personas differentiation

   Domain & Data Model:
   - Entities, attributes, relationships
   - Identity & uniqueness rules
   - Lifecycle/state transitions
   - Data volume / scale assumptions

   Interaction & UX Flow:
   - Critical user journeys / sequences
   - Error/empty/loading states
   - Accessibility or localization notes

   Non-Functional Quality Attributes:
   - Performance (latency, throughput targets)
   - Scalability (horizontal/vertical, limits)
   - Reliability & availability (uptime, recovery expectations)
   - Observability (logging, metrics, tracing signals)
   - Security & privacy (authN/Z, data protection, threat assumptions)
   - Compliance / regulatory constraints (if any)

   Integration & External Dependencies:
   - External services/APIs and failure modes
   - Data import/export formats
   - Protocol/versioning assumptions

   Edge Cases & Failure Handling:
   - Negative scenarios
   - Rate limiting / throttling
   - Conflict resolution (e.g., concurrent edits)

   Constraints & Tradeoffs:
   - Technical constraints (language, storage, hosting)
   - Explicit tradeoffs or rejected alternatives

   Terminology & Consistency:
   - Canonical glossary terms
   - Avoided synonyms / deprecated terms

   Completion Signals:
   - Acceptance criteria testability
   - Measurable Definition of Done style indicators

   Misc / Placeholders:
   - TODO markers / unresolved decisions
   - Ambiguous adjectives ("robust", "intuitive") lacking quantification

   For each category with Partial or Missing status, add a candidate question opportunity unless:
   - Clarification would not materially change implementation or validation strategy
   - The item is specifically about implementation method, tech-stack comparison, or task breakdown (note internally)

4. Generate (internally) a prioritized queue of candidate clarification questions (maximum 5). Do NOT output them all at once. Apply these constraints:
    - Maximum of 5 total questions across the whole session.
    - Each question must be answerable with EITHER:
       - A short multiple‑choice selection (2–5 distinct, mutually exclusive options), OR
       - A one-word / short‑phrase answer (explicitly constrain: "Answer in <=5 words").
    - Only include questions whose answers materially impact architecture, data modeling, task decomposition, test design, UX behavior, operational readiness, or compliance validation.
    - Ensure category coverage balance: attempt to cover the highest impact unresolved categories first; avoid asking two low-impact questions when a single high-impact area (e.g., security posture) is unresolved.
    - Exclude questions already answered, trivial stylistic preferences, or plan-level execution details (unless blocking correctness).
    - Favor clarifications that reduce downstream rework risk or prevent misaligned acceptance tests.
    - If more than 5 categories remain unresolved, select the top 5 by (Impact * Uncertainty) heuristic.

5. Sequential questioning loop (interactive):
    - Present EXACTLY ONE question at a time.
    - **Question writing quality (applies to every question, MC or short-answer):**
       - Lead with `**Question:**` followed by a full interrogative that ends with `?`. The question text before the `?` must make sense on its own.
       - NEVER use a topic label, section heading, or requirement id as the question itself. For example, `Acceptance device/runtime matrix (FR-023)` is INVALID — it is a label, not a question.
       - After the `?`, the only permitted suffix is an optional parenthesized requirement/question id. Exact format: `**Question:** <interrogative>?` or `**Question:** <interrogative>? (FR-023)`. Never put the id before the `?`, and never use the id (alone or with a topic label) as the whole prompt.
       - Immediately after the question line, add one plain-language "Why it matters" sentence (the stake for acceptance or shipping) before the recommendation/options.
       - Use everyday wording; introduce jargon only if defined in the same sentence. Self-check: a reader who does not know Spec Kit must be able to answer from the Question line alone. Terse is fine; cryptic labels are not.
    - For multiple‑choice questions:
       - **Analyze all options** and determine the **most suitable option** based on:
          - Best practices for the project type
          - Common patterns in similar implementations
          - Risk reduction (security, performance, maintainability)
          - Alignment with any explicit project goals or constraints visible in the spec
       - Present your **recommended option prominently** at the top with clear reasoning (1-2 sentences explaining why this is the best choice).
       - Format as: `**Recommended:** Option [X] - <reasoning>`
       - Then render all options as a Markdown table:

       | Option | Description |
       |--------|-------------|
       | A | <Option A description> |
       | B | <Option B description> |
       | C | <Option C description> (add D/E as needed up to 5) |
       | Short | Provide a different short answer (<=5 words) (Include only if free-form alternative is appropriate) |

       - After the table, add: `You can reply with the option letter (e.g., "A"), accept the recommendation by saying "yes" or "recommended", or provide your own short answer.`
    - For short‑answer style (no meaningful discrete options):
       - Provide your **suggested answer** based on best practices and context.
       - Format as: `**Suggested:** <your proposed answer> - <brief reasoning>`
       - Then output: `Format: Short answer (<=5 words). You can accept the suggestion by saying "yes" or "suggested", or provide your own answer.`
    - After the user answers:
       - If the user replies with "yes", "recommended", or "suggested", use your previously stated recommendation/suggestion as the answer.
       - Otherwise, validate the answer maps to one option or fits the <=5 word constraint.
       - If ambiguous, ask for a quick disambiguation (count still belongs to same question; do not advance).
       - Once satisfactory, record it in working memory (do not yet write to disk) and move to the next queued question.
    - Stop asking further questions when:
       - All critical ambiguities resolved early (remaining queued items become unnecessary), OR
       - User signals completion ("done", "good", "no more"), OR
       - You reach 5 asked questions.
    - Never reveal future queued questions in advance.
    - If no valid questions exist at start, immediately report no critical ambiguities.

6. Integration after EACH accepted answer (incremental update approach):
    - Maintain in-memory representation of the spec (loaded once at start) plus the raw file contents.
    - For the first integrated answer in this session:
       - Ensure a `## Clarifications` section exists (create it just after the highest-level contextual/overview section per the spec template if missing).
       - Under it, create (if not present) a `### Session YYYY-MM-DD` subheading for today.
    - Append a bullet line immediately after acceptance: `- Q: <question> → A: <final answer>`.
    - Then immediately apply the clarification to the most appropriate section(s):
       - Functional ambiguity → Update or add a bullet in Functional Requirements.
       - User interaction / actor distinction → Update User Stories or Actors subsection (if present) with clarified role, constraint, or scenario.
       - Data shape / entities → Update Data Model (add fields, types, relationships) preserving ordering; note added constraints succinctly.
       - Non-functional constraint → Add/modify measurable criteria in Success Criteria > Measurable Outcomes (convert vague adjective to metric or explicit target).
       - Edge case / negative flow → Add a new bullet under Edge Cases / Error Handling (or create such subsection if template provides placeholder for it).
       - Terminology conflict → Normalize term across spec; retain original only if necessary by adding `(formerly referred to as "X")` once.
    - If the clarification invalidates an earlier ambiguous statement, replace that statement instead of duplicating; leave no obsolete contradictory text.
    - Save the spec file AFTER each integration to minimize risk of context loss (atomic overwrite).
    - Preserve formatting: do not reorder unrelated sections; keep heading hierarchy intact.
    - Keep each inserted clarification minimal and testable (avoid narrative drift).

7. Validation (performed after EACH write plus final pass):
   - Clarifications session contains exactly one bullet per accepted answer (no duplicates).
   - Total asked (accepted) questions ≤ 5.
   - Updated sections contain no lingering vague placeholders the new answer was meant to resolve.
   - No contradictory earlier statement remains (scan for now-invalid alternative choices removed).
   - Markdown structure valid; only allowed new headings: `## Clarifications`, `### Session YYYY-MM-DD`.
   - Terminology consistency: same canonical term used across all updated sections.

8. Write the updated spec back to `FEATURE_SPEC`.

9. **Re-validate Spec Quality Checklist** (if it exists):
   - Check if `FEATURE_DIR/checklists/requirements.md` exists.
   - If it does NOT exist, skip this step silently.
   - If it exists:
     1. Read the checklist file.
     2. Identify all GitHub task-list checkbox lines — lines matching `- [ ]`, `- [x]`, or `- [X]` (case-insensitive, tolerant of leading whitespace for nested items) outside of code fences. Ignore all other content (headings, notes, non-checkbox bullets, metadata).
     3. For each checkbox line, record its current marker state (checked or unchecked) and item text into a before-snapshot list.
     4. Re-evaluate each checkbox item against the **updated** spec (the version just saved in step 7).
     5. For each checkbox item, update only if the checked/unchecked state actually changes:
        - If the item now passes and was unchecked: change `[ ]` to `[x]`.
        - If the item now fails and was checked: change `[x]`/`[X]` to `[ ]`.
        - If the state is unchanged: leave the marker as-is (preserve existing case to avoid cosmetic diffs).
     6. Save the updated checklist file. **Only toggle the `[ ]`/`[x]` marker portion of checkbox lines whose state changed.** All other file content — headings, metadata, notes, line ordering, whitespace — must remain unchanged to avoid noisy diffs.
     7. Compare the before-snapshot with the current state to compute three lists for the Completion Report:
        - **Newly passing**: items that changed from unchecked to checked.
        - **Regressions**: items that changed from checked to unchecked.
        - **Still unchecked**: items that remain unchecked.
     8. Record the before/after pass counts as checked/total checkbox items (e.g., "12/16 → 15/16 items passing").

Behavior rules:

- If no meaningful ambiguities found (or all potential questions would be low-impact), respond: "No critical ambiguities detected worth formal clarification." and suggest proceeding.
- If spec file missing, instruct user to run `__SPECKIT_COMMAND_SPECIFY__` first (do not create a new spec here).
- Never exceed 5 total asked questions (clarification retries for a single question do not count as new questions).
- Avoid speculative tech stack questions unless the absence blocks functional clarity.
- Respect user early termination signals ("stop", "done", "proceed").
- If no questions asked due to full coverage, output a compact coverage summary (all categories Clear) then suggest advancing.
- If quota reached with unresolved high-impact categories remaining, explicitly flag them under Deferred with rationale.

Context for prioritization: {ARGS}

## Mandatory Post-Execution Hooks

**You MUST complete this section before reporting completion to the user.**

Check if `.specify/extensions.yml` exists in the project root.
- If it does not exist, or no hooks are registered under `hooks.after_clarify`, skip to the Completion Report.
- If it exists, read it and look for entries under the `hooks.after_clarify` key.
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

Report completion (after questioning loop ends or early termination):
- Number of questions asked & answered.
- Path to updated spec.
- Sections touched (list names).
- Spec quality checklist status (if `FEATURE_DIR/checklists/requirements.md` was re-validated): show before/after pass counts (e.g., "Spec Quality Checklist: 12/16 → 15/16 items passing") and list any items that changed state — both newly checked (unchecked → checked) and any regressions (checked → unchecked). If any items remain unchecked, list them as areas needing attention.
- Coverage summary table listing each taxonomy category with Status: Resolved (was Partial/Missing and addressed), Deferred (exceeds question quota, or remaining item is specifically implementation method, tech-stack comparison, or task breakdown), Clear (already sufficient), Outstanding (still Partial/Missing but low impact).
- If any Outstanding or Deferred remain, recommend whether to proceed to `__SPECKIT_COMMAND_PLAN__` or run `__SPECKIT_COMMAND_CLARIFY__` again later post-plan.
- Suggested next command.

## Done When

- [ ] Spec ambiguities identified and clarifications integrated into spec file
- [ ] Spec quality checklist re-validated against updated spec (if `FEATURE_DIR/checklists/requirements.md` exists)
- [ ] Extension hooks dispatched or skipped according to the rules in Mandatory Post-Execution Hooks above
- [ ] Completion reported to user with questions answered, sections touched, checklist status, and coverage summary


</details>
