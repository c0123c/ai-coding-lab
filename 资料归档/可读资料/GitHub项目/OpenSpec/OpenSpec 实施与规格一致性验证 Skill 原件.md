# OpenSpec 实施与规格一致性验证 Skill 原件

定义读取规划工件，从完整性、正确性和一致性检查实施，并输出分级问题与验证报告的代理指令。

- 来源：OpenSpec
- 资料类型：SKILL.md：实施验证代理指令原件
- 存档版本：`9111a7654d78`。
- 文档时间：发表／更新：未知；抓取核验：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/skills/openspec-verify-change/SKILL.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenSpec/skills/openspec-verify-change/SKILL.md>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: openspec-verify-change
description: Verify implementation matches OpenSpec change artifacts. Use when the user wants to validate that implementation is complete, correct, and coherent before archiving. Also use when the user says "openspec verify" or "opsx verify".
allowed-tools: Bash(openspec:*)
license: MIT
compatibility: Requires openspec CLI.
metadata:
  author: openspec
  version: "1.0"
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

验证实施是否与变更工件（规格、任务、设计）相匹配。

**商店选择：** 如果用户命名商店（商店是在本机上注册的独立 OpenSpec 存储库）或工作位于其中，请运行 `openspec store list --json` 以发现注册的商店 ID，然后在读取或写入规格和更改的命令上传递 `--store <id>` (`new change`、`status`、`instructions`、`list`、`show`、`validate`、`archive`、`doctor`、`context`、 `schemas`、`view`）。选择后，将 `--store <id>` 视为工作流程其余部分的粘性。下面这些命令的每个未指定范围的示例都是简写形式：在运行之前，附加标志。例如，运行 `openspec status --change "<name>" --json --store "<id>"`，而不是下面显示的无作用域形式。其他命令不采用该标志。命令打印的提示已经带有该标志；继续跟进。如果没有存储，命令将作用于最近的本地 `openspec/` 根。

**项目检查：** 这些步骤需要已使用 OpenSpec 的项目。在写入任何内容（`new change`、`archive`、`sync specs`或创作工件文件）的第一步之前，请确认项目具有根：运行`openspec list --json`（选择商店时使用`--store <id>`，因为该商店是根）并读取`root`。根对象意味着项目已设置。 `"root": null` 意味着它不是 - 这里没有 `openspec/` 目录，并且诸如 `openspec new change` 之类的写入会创建一个作为副作用。该命令也以非零值退出，这是答案而不是损坏的 CLI，因此请阅读 JSON 而不是重试或解决它。

其中一个 `"root": null` 与设置无关：当 `status` 错误消息以 `Declared in` 或 `Invalid store declaration in` 开头并命名该项目的 `openspec/config.yaml`（或 `config.yml`）时，该项目确实通过存储使用 OpenSpec声明，本机无法解析（商店未注册，或 `store:` 行格式错误）。不要将其视为未初始化并跳过以下分支：在写入之前停止并向用户显示错误为 `message` 和 `fix`。

否则，在没有 root 的情况下，接下来发生的情况取决于如何实现此工作流程：

- **自动选择**：您自己选择此工作流程，无需用户命名OpenSpec、命名此技能或运行其斜线命令。停止使用 OpenSpec 并正常应答请求，就像未安装 OpenSpec 时一样。不要要求他们设置任何内容，也不要提及 OpenSpec 设置。
- **显式OpenSpec请求**：名为OpenSpec的用户，命名了该技能，或运行了其斜线命令。在写作之前停下来，询问如何继续：设置此项目 (`openspec init`)，定位他们已有的商店 (`--store <id>`)，或者在没有 OpenSpec 的情况下继续此请求。等待他们的答复。

在这两个分支中，永远不要创建根作为副作用：在用户要求之前不要运行 `openspec init`，不要手动创建 `openspec/` 文件，并且不要让命令创建它。

**输入**：可以选择指定更改名称。如果省略，请检查是否可以从对话上下文中推断出来。如果含糊或不明确，您 MUST 会提示可用的更改。

**步骤**

1. **选择更改**

如果提供了名称，请使用它。否则：
   - 从对话上下文中推断用户是否提到了更改
   - 如果仅存在一项活动更改，则自动选择
   - 如果不明确，请运行 `openspec list --json` 以获取可用更改并要求用户选择一项

出现提示时，显示列表返回的所有活动更改，包括 `status: "no-tasks"` 的更改。
 包括用于每个更改的架构（如果可用）。
 将未完成任务的更改标记为“（正在进行）”。

始终宣布：“使用更改：<name>”以及如何覆盖（例如，`/openspec-verify-change <other>`）。

2. **检查状态以了解架构**
   ```bash
   openspec status --change "<name>" --json
   ```
解析JSON即可理解：
   - `schemaName`：正在使用的工作流程（例如“规范驱动”）
   - `planningHome`、`changeRoot`、`artifactPaths` 和 `actionContext`：路径和范围上下文
   - 此更改存在哪些工件

3. **获取规划上下文并加载工件**

   ```bash
   openspec instructions apply --change "<name>" --json
   ```

这将返回更改目录、`contextFiles`（工件 ID -> 具体文件路径数组）、`taskTrackingConfigured` 以及顶级 `tasks` 和 `progress`，这些文件是从与可读取的模式的 `apply.tracks` 配置匹配的每个具体文件聚合的。读取 `contextFiles` 中的所有可用工件。

将 apply `state` 和 `instruction` 视为上下文，而不是验证结论。验证期间不要执行任务或归档更改。

4. **初始化验证报告结构**

创建具有三个维度的报告结构：
   - **完整性**：跟踪任务和规范覆盖范围
   - **正确性**：跟踪需求实现和场景覆盖
   - **连贯性**：轨道设计的一致性和图案的一致性

每个维度都可能存在 CRITICAL、WARNING 或 SUGGESTION 问题。

验证是建议性的。尊重故意遗漏，例如 `skip_specs: true`、可选设计文档和没有任务跟踪的模式。不要要求或发明可选的或故意省略的工件来获得干净的报告。 `Not verified` 描述了此报告的限制，而不是新的存档先决条件。存档保留其自己的检查和用户确认行为。

将检查架构未定义或将状态报告标记为有意跳过，如 ** 不适用 **。其可读增量规格包含 REMOVED 或 RENAMED 要求但没有 ADDED 或 MODIFIED 要求的变更的正确性检查也是 ** 不适用 **（请参阅步骤 6）。将它们排除在跳过检查计数和存档准备情况评估之外。为证据缺失或无法使用的适用检查保留**未验证**。

如果只有任务证据可用于适用的检查，则仅验证任务完成情况，并将其余适用的检查（包括 ** 代码模式一致性 **）标记为未验证，原因是“仅任务证据可用”。

如果工件无法读取或不包含可用的需求、场景或设计决策，请将受影响的检查标记为未验证并注明具体原因。 Continue 检查得到剩余证据的支持，但部分检查的输入集不是完全验证的检查。缺失的需求会影响规范覆盖范围和需求实施映射；缺少场景影响场景覆盖率；缺少设计决策会影响设计依从性。

5. **验证完整性**

**任务完成**：
   - 如果 `taskTrackingConfigured` 为 false，则将 ** 任务完成 ** 报告为不适用。不要将空的 `tasks` 视为缺失的证据。
   - 否则，使用顶级 `tasks` 和 `progress` 字段。他们已经聚合了与 `apply.tracks` 匹配的每个可读具体文件，无论跟踪工件的 ID 是什么；不要从 `contextFiles` 键推断跟踪。
   - 如果 `unavailableTrackingFiles` 非空，则将 ** 任务完成 ** 标记为未验证，并包含每个不可用的路径和原因。 Continue 使用任何可读的任务证据，但不从部分 `tasks` 和 `progress` 字段推断完成。
   - 如果`taskTrackingConfigured`为真且`tasks`为空，则将**任务完成**标记为未验证，并记录应用`state`和`instruction`的原因。仅非零总数并不能建立可评估的任务描述。
   - 报告 `progress` 中的完成任务与总任务。
   - 如果 `progress.remaining` 大于 0：
     - 为每个列出的未完成任务添加 CRITICAL 问题。如果剩余计数超过列出的未完成任务，还报告不带说明的未完成复选框，并建议添加说明并完成它们。不要仅根据列出的任务来推断完成情况。
     - 建议：“完成任务：<description>”或“如果已实施，则标记为完成”

**规格覆盖范围**：
   - 如果状态标记 `skip_specs: true` 跳过的规范工件，或者模式未定义规范工件（`artifactPaths.<id>.outputPath` 不位于 `specs/` 下的工件），则将依赖于规范的检查报告为不适用。
   - 否则，`contextFiles` 由工件 id 作为键控，并且工件 id 来自活动模式，因此不要假设诸如 `specs` 之类的 id。规格工件是那些 `artifactPaths.<id>.outputPath` 位于 `specs/` 之下的工件；从 `contextFiles.<id>` 读取他们的文件。如果这些规范文件不存在或为空，请将 **规范覆盖范围**、**需求实现映射**和**场景覆盖范围**标记为未验证；不要将它们中的任何一个视为干净的。
   - 如果这些规格文件中存在增量规格：
     - 提取所有要求（标有“### 要求：”，或在 `## RENAMED Requirements` 下列为 `FROM:`/`TO:` 对）并记下每个要求所在的增量部分：`## ADDED`、`## MODIFIED`、`## REMOVED`，或`## RENAMED Requirements`。该部分决定检查的内容。
     - 对于每个 ADDED 或 MODIFIED 要求（对于 MODIFIED，检查增量中的文本，而不是旧的措辞）：
       - 在代码库中搜索与需求相关的关键字
       - 评估是否可能实施
     - 如果 ADDED 或 MODIFIED 要求未实现：
       - 添加 CRITICAL 问题：“未找到要求：<requirement name>”
       - 建议：“实施要求 X：<description>”
     - 对于每个 REMOVED 要求，更改要求行为消失，因此反转检查：
       - 在代码库中搜索已删除的行为。 `openspec/` 工件或文档中的匹配，或者仅服务于迁移说明或 ADDED 要求的代码中的匹配本身并不是证据。报告仍提供已删除行为的任何代码路径，包括与 ADDED 要求共享的代码路径。
       - 没有实现是预期的结果。切勿将 REMOVED 要求报告为“未找到要求”或建议实施它。
       - 如果该行为仍然存在：
         - 添加 CRITICAL 问题：“已删除的要求仍已实现：<requirement name>”
         - 建议：“删除 <file>:<lines> 处的剩余实现，遵循要求的迁移说明（如果有）”
     - 对于每个 RENAMED 条目 (`FROM:`/`TO:`)，名称发生更改，但行为保持不变，因此请检查 TO 要求以了解未更改的行为：
       - 不要将 FROM 名称报告为缺失，并且不需要重命名代码符号、标识符或文件名。
       - 如果 TO 名称也出现在 MODIFIED 下，则会根据 MODIFIED 文本检查其行为；这里跳过它。
       - 否则，请使用与增量规范相同的功能路径，读取 `<planningHome.root>/openspec/specs/<capability-path>/spec.md` 处主规范中的基线要求：FROM 名称下的要求，或仅当 FROM 名称不存在时，才在 TO 名称下，因为主规范已同步。它的主体和场景是 TO 要求保留的行为的证据。
       - 在代码库中搜索该行为并评估它是否仍在实施。
       - 如果它看起来未实现：
         - 添加 CRITICAL 问题：“未找到重命名的要求：<TO name>”
         - 建议：“恢复 <TO name>（从 <FROM name> 重命名）的行为；重命名不得改变行为”
       - 如果无法找到或读取基线要求，请将 ** 规范覆盖范围 ** 标记为未验证该条目并注明原因。切勿将未经检查的重命名视为通过。

6. **验证正确性**

如果增量规范可读并且包含至少一项 REMOVED 或 RENAMED 要求，但没有 ADDED 或 MODIFIED 要求（更改仅删除或重命名要求），则报告 **要求实现映射 ** 和**场景覆盖范围**为**不适用**。规格覆盖范围下的 REMOVED 和 RENAMED 检查是此类更改的证据（每个 RENAMED 条目都根据其基线行为进行检查），因此不要将这两个检查标记为未验证。根本没有可解析要求的增量规范是无用的证据，而不是仅删除的更改：将这些检查标记为未验证。

**需求实现映射**：
   - 对于增量规格中的每个 ADDED 或 MODIFIED 要求（REMOVED 条目和没有 MODIFIED 块的 RENAMED 条目，在规格覆盖范围下解决）：
     - 搜索代码库以获取实施证据
     - 如果找到，请记下文件路径和行范围
     - 评估实施是否符合需求意图
     - 如果检测到分歧：
       - 添加 WARNING：“实现可能与规范有所不同：

"
       - 建议：“根据要求 X 审核 <file>:<lines>”

**场景覆盖范围**：
   - 对于增量规格中 ADDED 或 MODIFIED 要求下的每个场景（标有“#### 场景：”）：
     - 检查代码中是否处理了条件
     - 检查是否存在覆盖该场景的测试
     - 如果场景未发现：
       - 添加WARNING：“未涵盖的场景：<scenario name>”
       - 建议：“为场景添加测试或实现：<description>”
   - 跳过 REMOVED 要求下的场景；这种行为应该消失。

7. **验证一致性**

**设计遵循**：
   - 如果模式没有定义设计工件（没有带有 id 的工件） `design`，并且没有一个 `artifactPaths.<id>.outputPath` 是或结束于 `design.md`），报告 **设计遵循** 为不适用。
   - 如果设计工件的 `contextFiles.<id>` 文件存在：
     - 提取关键决策（查找“决策：”、“方法：”、“架构：”等部分）
     - 验证实施遵循这些决定
     - 如果发现矛盾：
       - 添加 WARNING：“未遵循设计决策：<decision>”
       - 建议：“更新实现或修改 design.md 以符合现实”
   - 否则，如果设计工件的 `contextFiles.<id>` 文件不存在或为空：将 **Design Adherence** 标记为未验证。有了其他支持工件，**代码模式一致性**仍然可以运行；仅任务情况仍然仅限于任务完成。

**代码模式一致性**：
   - 如果无法识别实现更改，请将 ** 代码模式一致性 ** 标记为未验证，并解释缺少的证据。
   - 否则，检查新代码是否与项目模式保持一致
   - 检查文件命名、目录结构、编码风格
   - 如果发现重大偏差：
     - 添加SUGGESTION：“码型偏差：

"
     - 建议：“考虑以下项目模式：<example>”

8. **生成验证报告**

**总结记分卡**：
   ```markdown
   ## Verification Report: <change-name>

   ### Summary
   | Dimension    | Status           |
   |--------------|------------------|
   | Completeness | X/Y tasks, N reqs|
   | Correctness  | M/N reqs covered |
   | Coherence    | Followed/Issues  |
   ```

在每个“状态”单元格中，报告已运行的检查的结果以及每个跳过的检查的 `Not verified (<reason>)`。如果跳过维度中的所有检查，则以 `Not verified` 开头单元格。切勿将跳过的检查视为通过。将每项未验证或部分验证的检查视为在最终评估中跳过。仅计算 N 中的 ADDED 和 MODIFIED 要求，并分别报告 REMOVED 和 RENAMED 要求（例如，“1 删除已确认，1 重命名已验证”）。对于仅删除或重命名需求的更改，正确性单元格显示为 `Not applicable (no ADDED or MODIFIED requirements)`。

**按优先级排列的问题**：

   1. **CRITICAL**（必须在存档前修复）：
      - 未完成的任务
      - 缺少需求实现
      - 删除的要求仍然实施
      - 已重命名的需求，其行为不再实现
      - 每个都有具体的、可操作的建议

   2. **WARNING**（应该修复）：
      - Spec/design 分歧
      - 缺少场景覆盖
      - 每一个都有具体的推荐

   3. **SUGGESTION**（很好修复）：
      - 模式不一致
      - 小改进
      - 每一个都有具体的推荐

**最终评估**：
   - 如果 CRITICAL 问题：“发现 X 个关键问题。在存档之前修复。”如果跳过任何检查，还请列出每个跳过的检查及其原因。
   - 如果没有 CRITICAL 问题，则出现一个或多个警告，并且没有跳过任何检查：“没有严重问题。要考虑 Y 警告。准备存档（有明显改进）。”
   - 如果只跳过建议而不跳过任何检查：“没有严重问题或警告。需要考虑 Z 建议。准备存档（有明显的改进）。”
   - 如果没有问题且没有跳过任何检查：“所有检查均已通过。准备存档。”
   - 如果跳过任何检查并且不存在 CRITICAL 问题：请勿声称已准备就绪。说“在运行的检查中未发现严重问题。<check(s)> 未验证：<reason>。”当非零时，包括警告计数。
   - 在每次最终评估中包括非零的建议计数。

**启发式验证**

- **完整性**：关注客观清单项目（复选框、要求列表）
- **正确性**：使用关键词搜索、文件路径分析、合理推断——不要求完美的确定性
- **连贯性**：寻找明显的不一致之处，不要挑剔风格
- **误报**：当不确定时，优先选择SUGGESTION而不是WARNING，优先选择WARNING而不是CRITICAL
- **可操作性**：每个问题都必须有具体建议，并在适用的情况下提供file/line参考文献

**输出格式**

使用清晰的降价：
- 汇总记分卡表
- 问题分组列表 (CRITICAL/WARNING/SUGGESTION)
- 代码参考格式：`file.ts:123`
- 具体的、可操作的建议
- 没有诸如“考虑审查”之类的模糊建议


## 原文对照

<details>
<summary>展开完整原文</summary>

Verify that an implementation matches the change artifacts (specs, tasks, design).

**Store selection:** If the user names a store (a store is a standalone OpenSpec repo registered on this machine) or the work lives in one, run `openspec store list --json` to discover registered store ids, then pass `--store <id>` on the commands that read or write specs and changes (`new change`, `status`, `instructions`, `list`, `show`, `validate`, `archive`, `doctor`, `context`, `schemas`, `view`). Once selected, treat `--store <id>` as sticky for the rest of the workflow. Every unscoped example of those commands below is shorthand: before running it, append the flag. For example, run `openspec status --change "<name>" --json --store "<id>"`, not the unscoped form shown below. Other commands do not take the flag. Hints printed by commands already carry the flag; keep it on follow-ups. Without a store, commands act on the nearest local `openspec/` root.

**Project check:** These steps expect a project that already uses OpenSpec. Before the first step that writes anything (`new change`, `archive`, `sync specs`, or authoring an artifact file), confirm the project has a root: run `openspec list --json` (with `--store <id>` when a store is selected, since the store is then the root) and read `root`. A root object means the project is set up. `"root": null` means it is not - there is no `openspec/` directory here, and a write such as `openspec new change` would create one as a side effect. The command also exits non-zero, which is that answer rather than a broken CLI, so read the JSON instead of retrying or working around it.

One `"root": null` is not about setup: when a `status` error message starts with `Declared in` or `Invalid store declaration in` and names this project's `openspec/config.yaml` (or `config.yml`), the project does use OpenSpec through a store it declares, which this machine cannot resolve (the store is not registered, or the `store:` line is malformed). Do not treat it as uninitialized and skip the branches below: stop before writing and show the user that error's `message` and `fix`.

Otherwise, with no root, what happens next depends on how this workflow was reached:

- **Auto-selected**: you chose this workflow yourself, without the user naming OpenSpec, naming this skill, or running its slash command. Stop using OpenSpec and answer the request normally, as you would with no OpenSpec installed. Do not ask them to set anything up and do not mention OpenSpec setup.
- **Explicit OpenSpec request**: the user named OpenSpec, named this skill, or ran its slash command. Stop before writing and ask how to proceed: set this project up (`openspec init`), target a store they already have (`--store <id>`), or continue without OpenSpec for this request. Wait for their answer.

In both branches, never create the root as a side effect: do not run `openspec init` until the user asks for it, do not hand-create `openspec/` files, and do not let a command create it.

**Input**: Optionally specify a change name. If omitted, check if it can be inferred from conversation context. If vague or ambiguous you MUST prompt for available changes.

**Steps**

1. **Select the change**

   If a name is provided, use it. Otherwise:
   - Infer from conversation context if the user mentioned a change
   - Auto-select if only one active change exists
   - If ambiguous, run `openspec list --json` to get available changes and ask the user to select one

   When prompting, show all active changes returned by the list, including changes with `status: "no-tasks"`.
   Include the schema used for each change if available.
   Mark changes with incomplete tasks as "(In Progress)".

   Always announce: "Using change: <name>" and how to override (e.g., `/openspec-verify-change <other>`).

2. **Check status to understand the schema**
   ```bash
   openspec status --change "<name>" --json
   ```
   Parse the JSON to understand:
   - `schemaName`: The workflow being used (e.g., "spec-driven")
   - `planningHome`, `changeRoot`, `artifactPaths`, and `actionContext`: path and scope context
   - Which artifacts exist for this change

3. **Get planning context and load artifacts**

   ```bash
   openspec instructions apply --change "<name>" --json
   ```

   This returns the change directory, `contextFiles` (artifact ID -> array of concrete file paths), `taskTrackingConfigured`, and top-level `tasks` and `progress` aggregated from every concrete file matched by the schema's `apply.tracks` configuration that could be read. Read all available artifacts from `contextFiles`.

   Treat apply `state` and `instruction` as context, not a verification verdict. Do not implement tasks or archive the change during verification.

4. **Initialize verification report structure**

   Create a report structure with three dimensions:
   - **Completeness**: Track tasks and spec coverage
   - **Correctness**: Track requirement implementation and scenario coverage
   - **Coherence**: Track design adherence and pattern consistency

   Each dimension can have CRITICAL, WARNING, or SUGGESTION issues.

   Verification is advisory. Respect intentional omissions such as `skip_specs: true`, optional design documents, and schemas without task tracking. Do not require or invent optional or intentionally omitted artifacts to obtain a clean report. `Not verified` describes a limit of this report, not a new archive prerequisite. Archive retains its own checks and user-confirmation behavior.

   Mark checks the schema does not define, or artifacts the status reports as intentionally skipped, as **Not applicable**. The correctness checks of a change whose readable delta specs contain REMOVED or RENAMED requirements but no ADDED or MODIFIED requirements are also **Not applicable** (see step 6). Exclude them from skipped-check counts and the archive-readiness assessment. Reserve **Not verified** for applicable checks whose evidence is missing or unusable.

   If only task evidence is available for applicable checks, verify task completion only and mark the remaining applicable checks, including **Code Pattern Consistency**, as not verified with the reason "Only task evidence available".

   If artifacts cannot be read or contain no usable requirements, scenarios, or design decisions, mark the affected checks as not verified with the specific reason. Continue checks supported by the remaining evidence, but a partially checked input set is not a fully verified check. Missing requirements affect Spec Coverage and Requirement Implementation Mapping; missing scenarios affect Scenario Coverage; missing design decisions affect Design Adherence.

5. **Verify Completeness**

   **Task Completion**:
   - If `taskTrackingConfigured` is false, report **Task Completion** as not applicable. Do not treat empty `tasks` as missing evidence.
   - Otherwise, use the top-level `tasks` and `progress` fields. They already aggregate every readable concrete file matched by `apply.tracks`, regardless of the tracked artifact's ID; do not infer tracking from a `contextFiles` key.
   - If `unavailableTrackingFiles` is nonempty, mark **Task Completion** as not verified and include every unavailable path and reason. Continue using any readable task evidence, but do not infer completion from the partial `tasks` and `progress` fields.
   - If `taskTrackingConfigured` is true and `tasks` is empty, mark **Task Completion** as not verified and record the reason from apply `state` and `instruction`. Nonzero totals alone do not establish evaluable task descriptions.
   - Report complete vs total tasks from `progress`.
   - If `progress.remaining` is greater than 0:
     - Add CRITICAL issue for each listed incomplete task. If the remaining count exceeds the listed incomplete tasks, also report the incomplete checkboxes without descriptions and recommend adding descriptions and completing them. Do not infer completion from the listed tasks alone.
     - Recommendation: "Complete task: <description>" or "Mark as done if already implemented"

   **Spec Coverage**:
   - If status marks the spec artifact skipped by `skip_specs: true`, or the schema defines no spec artifact (no artifact whose `artifactPaths.<id>.outputPath` is under `specs/`), report the spec-dependent checks as not applicable.
   - Otherwise, `contextFiles` is keyed by artifact id, and artifact ids come from the active schema, so do not assume an id such as `specs`. The spec artifacts are those whose `artifactPaths.<id>.outputPath` is under `specs/`; read their files from `contextFiles.<id>`. If those spec files are absent or empty, mark **Spec Coverage**, **Requirement Implementation Mapping**, and **Scenario Coverage** as not verified; do not treat any of them as clean.
   - If delta specs exist in those spec files:
     - Extract all requirements (marked with "### Requirement:", or listed as `FROM:`/`TO:` pairs under `## RENAMED Requirements`) and note the delta section each one sits under: `## ADDED`, `## MODIFIED`, `## REMOVED`, or `## RENAMED Requirements`. The section decides what the check looks for.
     - For each ADDED or MODIFIED requirement (for MODIFIED, check the text in the delta, not the old wording):
       - Search codebase for keywords related to the requirement
       - Assess if implementation likely exists
     - If ADDED or MODIFIED requirements appear unimplemented:
       - Add CRITICAL issue: "Requirement not found: <requirement name>"
       - Recommendation: "Implement requirement X: <description>"
     - For each REMOVED requirement, the change asks for the behavior to be gone, so invert the check:
       - Search codebase for the removed behavior. Matches in `openspec/` artifacts or docs, or in code that serves only the Migration note or an ADDED requirement, are not evidence by themselves. Report any code path that still delivers the removed behavior, including one shared with an ADDED requirement.
       - Finding no implementation is the expected result. Never report a REMOVED requirement as "Requirement not found" or recommend implementing it.
       - If the behavior is still present:
         - Add CRITICAL issue: "Removed requirement still implemented: <requirement name>"
         - Recommendation: "Remove the remaining implementation at <file>:<lines>, following the requirement's Migration note if it has one"
     - For each RENAMED entry (`FROM:`/`TO:`), the name changes but the behavior stays, so check the TO requirement for that unchanged behavior:
       - Do not report the FROM name as missing, and do not require code symbols, identifiers, or file names to be renamed.
       - If the TO name also appears under MODIFIED, its behavior is checked there against the MODIFIED text; skip it here.
       - Otherwise, read the baseline requirement in the main spec at `<planningHome.root>/openspec/specs/<capability-path>/spec.md`, using the same capability path as the delta spec: the requirement under the FROM name, or under the TO name only when the FROM name is absent because the main spec is already synced. Its body and scenarios are the evidence for the behavior the TO requirement keeps.
       - Search codebase for that behavior and assess if it is still implemented.
       - If it appears unimplemented:
         - Add CRITICAL issue: "Renamed requirement not found: <TO name>"
         - Recommendation: "Restore the behavior of <TO name> (renamed from <FROM name>); a rename must not change behavior"
       - If the baseline requirement cannot be found or read, mark **Spec Coverage** as not verified for that entry with the reason. Never count an unchecked rename as passing.

6. **Verify Correctness**

   If the delta specs are readable and contain at least one REMOVED or RENAMED requirement but no ADDED or MODIFIED requirements (the change only removes or renames requirements), report **Requirement Implementation Mapping** and **Scenario Coverage** as **Not applicable**. The REMOVED and RENAMED checks under Spec Coverage are the evidence for such a change (each RENAMED entry is checked there against its baseline behavior), so do not mark these two checks as not verified. A delta spec with no parseable requirements at all is unusable evidence, not a removal-only change: mark these checks as not verified.

   **Requirement Implementation Mapping**:
   - For each ADDED or MODIFIED requirement from delta specs (REMOVED entries, and RENAMED entries without a MODIFIED block, were settled under Spec Coverage):
     - Search codebase for implementation evidence
     - If found, note file paths and line ranges
     - Assess if implementation matches requirement intent
     - If divergence detected:
       - Add WARNING: "Implementation may diverge from spec: 

"
       - Recommendation: "Review <file>:<lines> against requirement X"

   **Scenario Coverage**:
   - For each scenario under an ADDED or MODIFIED requirement in delta specs (marked with "#### Scenario:"):
     - Check if conditions are handled in code
     - Check if tests exist covering the scenario
     - If scenario appears uncovered:
       - Add WARNING: "Scenario not covered: <scenario name>"
       - Recommendation: "Add test or implementation for scenario: <description>"
   - Skip scenarios under a REMOVED requirement; that behavior is meant to be gone.

7. **Verify Coherence**

   **Design Adherence**:
   - If the schema defines no design artifact (no artifact with id `design`, and none whose `artifactPaths.<id>.outputPath` is or ends in `design.md`), report **Design Adherence** as not applicable.
   - If the design artifact's `contextFiles.<id>` file exists:
     - Extract key decisions (look for sections like "Decision:", "Approach:", "Architecture:")
     - Verify implementation follows those decisions
     - If contradiction detected:
       - Add WARNING: "Design decision not followed: <decision>"
       - Recommendation: "Update implementation or revise design.md to match reality"
   - Otherwise, if the design artifact's `contextFiles.<id>` file is absent or empty: mark **Design Adherence** as not verified. With other supporting artifacts, **Code Pattern Consistency** still runs; the task-only case remains limited to task completion.

   **Code Pattern Consistency**:
   - If implementation changes cannot be identified, mark **Code Pattern Consistency** as not verified and explain the missing evidence.
   - Otherwise, review new code for consistency with project patterns
   - Check file naming, directory structure, coding style
   - If significant deviations found:
     - Add SUGGESTION: "Code pattern deviation: 

"
     - Recommendation: "Consider following project pattern: <example>"

8. **Generate Verification Report**

   **Summary Scorecard**:
   ```markdown
   ## Verification Report: <change-name>

   ### Summary
   | Dimension    | Status           |
   |--------------|------------------|
   | Completeness | X/Y tasks, N reqs|
   | Correctness  | M/N reqs covered |
   | Coherence    | Followed/Issues  |
   ```

   In each Status cell, report the results of checks that ran and `Not verified (<reason>)` for every skipped check. If all checks in a dimension were skipped, start the cell with `Not verified`. Never score a skipped check as passing. Treat every not verified or partially verified check as skipped in the final assessment. Count only ADDED and MODIFIED requirements in N, and report REMOVED and RENAMED requirements separately (for example, "1 removal confirmed, 1 rename verified"). For a change that only removes or renames requirements, the Correctness cell reads `Not applicable (no ADDED or MODIFIED requirements)`.

   **Issues by Priority**:

   1. **CRITICAL** (Must fix before archive):
      - Incomplete tasks
      - Missing requirement implementations
      - Removed requirements still implemented
      - Renamed requirements whose behavior is no longer implemented
      - Each with specific, actionable recommendation

   2. **WARNING** (Should fix):
      - Spec/design divergences
      - Missing scenario coverage
      - Each with specific recommendation

   3. **SUGGESTION** (Nice to fix):
      - Pattern inconsistencies
      - Minor improvements
      - Each with specific recommendation

   **Final Assessment**:
   - If CRITICAL issues: "X critical issue(s) found. Fix before archiving." If any check was skipped, also name every skipped check and its reason.
   - If no CRITICAL issues, one or more warnings, and no checks were skipped: "No critical issues. Y warning(s) to consider. Ready for archive (with noted improvements)."
   - If only suggestions and no checks were skipped: "No critical issues or warnings. Z suggestion(s) to consider. Ready for archive (with noted improvements)."
   - If no issues and no checks were skipped: "All checks passed. Ready for archive."
   - If any check was skipped and there are no CRITICAL issues: do not claim readiness. Say "No critical issues found in the checks that ran. <check(s)> not verified: <reason>." Include the warning count when nonzero.
   - Include the suggestion count when nonzero in every final assessment.

**Verification Heuristics**

- **Completeness**: Focus on objective checklist items (checkboxes, requirements list)
- **Correctness**: Use keyword search, file path analysis, reasonable inference - don't require perfect certainty
- **Coherence**: Look for glaring inconsistencies, don't nitpick style
- **False Positives**: When uncertain, prefer SUGGESTION over WARNING, WARNING over CRITICAL
- **Actionability**: Every issue must have a specific recommendation with file/line references where applicable

**Output Format**

Use clear markdown with:
- Table for summary scorecard
- Grouped lists for issues (CRITICAL/WARNING/SUGGESTION)
- Code references in format: `file.ts:123`
- Specific, actionable recommendations
- No vague suggestions like "consider reviewing"


</details>
