# OpenSpec 归档单个变更 Skill 原件

定义工件与任务完成检查、增量规格同步状态评估、执行归档和输出归档摘要的代理指令。

- 来源：OpenSpec
- 资料类型：SKILL.md：单个变更归档代理指令原件
- 存档版本：`9111a7654d78`。
- 文档时间：发表／更新：未知；抓取核验：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/skills/openspec-archive-change/SKILL.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenSpec/skills/openspec-archive-change/SKILL.md>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: openspec-archive-change
description: Archive a completed OpenSpec change in the experimental workflow. Use when the user wants to finalize and archive a change after implementation is complete. Also use when the user says "openspec archive" or "opsx archive".
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

归档实验工作流程中已完成的更改。

**商店选择：** 如果用户命名商店（商店是在本机上注册的独立 OpenSpec 存储库）或工作位于其中，请运行 `openspec store list --json` 以发现注册的商店 ID，然后在读取或写入规格和更改的命令上传递 `--store <id>` (`new change`、`status`、`instructions`、`list`、`show`、`validate`、`archive`、`doctor`、`context`、 `schemas`、`view`）。选择后，将 `--store <id>` 视为工作流程其余部分的粘性。下面这些命令的每个未指定范围的示例都是简写形式：在运行之前，附加标志。例如，运行 `openspec status --change "<name>" --json --store "<id>"`，而不是下面显示的无作用域形式。其他命令不采用该标志。命令打印的提示已经带有该标志；继续跟进。如果没有存储，命令将作用于最近的本地 `openspec/` 根。

**项目检查：** 这些步骤需要已使用 OpenSpec 的项目。在写入任何内容（`new change`、`archive`、`sync specs`或创作工件文件）的第一步之前，请确认项目具有根：运行`openspec list --json`（选择商店时使用`--store <id>`，因为该商店是根）并读取`root`。根对象意味着项目已设置。 `"root": null` 意味着它不是 - 这里没有 `openspec/` 目录，并且诸如 `openspec new change` 之类的写入会创建一个作为副作用。该命令也以非零值退出，这是答案而不是损坏的 CLI，因此请阅读 JSON 而不是重试或解决它。

其中一个 `"root": null` 与设置无关：当 `status` 错误消息以 `Declared in` 或 `Invalid store declaration in` 开头并命名该项目的 `openspec/config.yaml`（或 `config.yml`）时，该项目确实通过存储使用 OpenSpec声明，本机无法解析（商店未注册，或 `store:` 行格式错误）。不要将其视为未初始化并跳过以下分支：在写入之前停止并向用户显示错误为 `message` 和 `fix`。

否则，在没有 root 的情况下，接下来发生的情况取决于如何实现此工作流程：

- **自动选择**：您自己选择此工作流程，无需用户命名OpenSpec、命名此技能或运行其斜线命令。停止使用 OpenSpec 并正常应答请求，就像未安装 OpenSpec 时一样。不要要求他们设置任何内容，也不要提及 OpenSpec 设置。
- **显式OpenSpec请求**：名为OpenSpec的用户，命名了该技能，或运行了其斜线命令。在写作之前停下来，询问如何继续：设置此项目 (`openspec init`)，定位他们已有的商店 (`--store <id>`)，或者在没有 OpenSpec 的情况下继续此请求。等待他们的答复。

在这两个分支中，永远不要创建根作为副作用：在用户要求之前不要运行 `openspec init`，不要手动创建 `openspec/` 文件，并且不要让命令创建它。

`<capability-path>` 是相对于 `specs/` 的规范目录（例如，`user-auth` 或 `identity/user-auth`）。在解析其主要规范时，保留每个增量规范的完整路径。

**输入**：可以选择指定更改名称。如果省略，请检查是否可以从对话上下文中推断出来。如果含糊或不明确，您 MUST 会提示可用的更改。

**步骤**

1. **选择更改**

如果提供了名称，请使用它。否则：
   - 从对话上下文中推断用户是否提到了更改
   - 如果仅存在一项活动更改，则自动选择
   - 如果不明确，请运行 `openspec list --json` 以获取可用更改并要求用户选择一项

出现提示时，仅显示活动更改（尚未存档）。
 包括用于每个更改的架构（如果有）。

始终宣布：“使用更改：<name>”以及如何覆盖（例如，`/openspec-archive-change <other>`）。

**在现有存档检查之前加载当前存档输入：**

解决选定的更改并规划根后，运行：
   ```bash
   openspec instructions archive --change "<name>" --json
   ```
在此命令上保留相同的选定根标志。此查找是建议性的，并且 
 是可选的：它只提供额外的提示输入，因此绝不能阻止归档。
 如果它以非零值退出或返回无效的 JSON — 例如在较旧的 CLI 上，
 尚不支持此命令 — 继续归档工作流程无
上下文，无操作指导。不要报告错误，也不要停止。

成功的响应可能会省略两个可选字段。将 `context` 视为 
 所需的提示级输入：阅读并考虑它，并应用相关项目 
 事实、约定和约束。将 `operationGuidance` 视为可选 
 附加建议：阅读并考虑每个条目，并遵循 
 适用且与内置存档工作流程兼容的条目。

将这两个字段与内置步骤、显式用户选择、已解析的 
 路径、CLI 检查和命令契约分开。如果上下文与 
 控制输入之一冲突，请报告冲突并保留控制值。如果
指南不适用或与控制输入冲突，请勿遵循它
并解释原因。不要从 
 任一字段推断替换路径、跳过的提示或标志，并且不要将其文本逐字复制到规范中、更改工件、
 或存档摘要，除非用户单独要求。这些是
提示级行为契约，而不是可执行的检查。

2. **检查工件完成状态**

运行 `openspec status --change "<name>" --json` 以检查工件完成情况。

解析JSON即可理解：
   - `schemaName`：正在使用的工作流程
   - `planningHome`、`changeRoot`、`artifactPaths` 和 `actionContext`：路径和范围上下文
   - `artifacts`：工件列表及其状态（`done`、`skipped` 或其他）

**如果任何工件既不是 `done` 也不是 `skipped`**（跳过的工件满足要求 - 更改声明 skip_specs）：
   - 显示警告列出不完整的工件
   - 询问用户确认他们想要继续
   - 如果用户确认则继续

3. **检查任务完成状态**

使用相同的选定根标志运行 `openspec list --json`，并在 `changes` 中找到 `name` 与所选更改完全匹配的 
 条目。
 需要恰好一个匹配且非负整数 `totalTasks` 和 
 `completedTasks`，与`completedTasks <= totalTasks`。 CLI 解析
 架构的跟踪任务文件，包括自定义工件名称、输出
 路径和glob。
 不完整的任务 = `totalTasks - completedTasks`。

不要根据工件状态或缺少 
 顶级 `tasks.md` 来推断任务完成情况。如果查找失败、返回无效 JSON、忽略或 
 重复所选更改，或返回无效计数，请报告问题 
 并在同步或存档之前停止。
 仅 CLI 计数`x`/`X` 复选框标记完整；
 其他标记（包括不熟悉的标记）仍然不完整。

**如果发现未完成的任务：**
   - 显示警告，显示未完成任务的计数
   - 询问用户确认他们想要继续
   - 如果用户确认则继续

**如果 `totalTasks` 为零：** 继续，不会出现与任务相关的警告。

4. **评估增量规格同步状态**

使用状态 JSON 中的 `artifactPaths.specs.existingOutputPaths` 作为唯一的 
 增量规格源。如果 `specs` 条目丢失或 
 `existingOutputPaths` 为空，请在没有同步提示的情况下继续，并且不要从其他工件推断 
 增量规格。

**如果存在增量规格：**
   - 将每个增量规格与其在 `<planningHome.root>/openspec/specs/<capability-path>/spec.md` 处的相应主规格进行比较（使用步骤 2 中的存储感知 `planningHome.root`，而不是硬编码的存储库路径）
   - 缺少的主要规格是**不会自动**“已同步”。对于新功能，主要规格是同步的 * 输出 *，而不是输入：
     - 如果增量具有 MODIFIED 或 RENAMED 要求，请报告只有 ADDED 要求才能创建新的主要规范，并将该功能标记为同步阻止。永远不要发明没有当前版本的需求。
     - 否则，如果增量只有 REMOVED 要求，并且更改的 `.openspec.yaml` 声明 `retire_capabilities: true`，则该功能已停用：将其计为已同步，警告没有任何内容需要删除，并且不要重新创建主规范。现在以及验证同步完成时均应用此规则。
     - 否则，如果增量没有 ADDED 要求，则报告不可能进行同步，并将该功能标记为同步阻止。对于仅 REMOVED- 的增量，警告没有可从中删除的主要规格，并保持主要规格树不变。 `openspec archive`拒绝未标记的REMOVED-仅限带有`Spec must have at least one requirement`的情况。
     - 否则，将该功能视为需要同步，并在摘要中对其进行命名 (`<capability-path>: new main spec will be created`)。如果增量也有 REMOVED 要求，请警告它们将被忽略，因为没有可从中删除的主要规范。同步仅根据增量的 ADDED 要求创建主要规范，与 `openspec archive` 完全相同。
   - 确定将应用哪些更改（添加、修改、删除、重命名）
   - Continue 评估剩余功能，即使其中一个功能被同步阻止。在提示之前显示综合摘要。

**提示选项：**
   - 如果任何功能被同步阻止：解释原因并仅提供“存档而不同步”、“取消”
   - 否则，如果需要更改：“立即同步（推荐）”、“存档而不同步”
   - 否则，如果已经同步：“立即存档”、“仍然同步”、“取消”

答案路线：
   - “取消”——停止，不归档
   - “存档而不同步”或“立即存档”- 继续存档
   - “立即同步”或“无论如何同步”——同步，然后验证（如下）。当功能被同步阻止时，不要启动任何同步；解释阻挡者并重复可用的选择。
   - 还有什么问题——再问一次而不是存档

在选定的同步写入任何主要规范之前，请使用相同的 
 选定根标志运行 
 `openspec instructions specs --change "<name>" --json` 一次。需要零退出状态和有效的工件指令
 JSON。如果查找失败或返回无效的 JSON，请在编写任何主要规范或移动更改之前报告错误并停止 
。省略 
 `rules` 的有效响应是无规则情况。仅将返回的`rules`应用于本次合并产生的主要规格的内容和
形式；不要将它们用作存档指导，
 更改 CLI 行为，或将规则文本复制到任何输出文件中。

然后运行 `openspec-sync-specs` 内联工作流程（agent 驱动的智能合并）以进行更改“<name>”，传递增量规格分析和从上面获取的规格规则快照，并等待其完成。内联同步必须重用该快照，而无需再次获取 `specs` 指令。不要将其委托给后台任务 - 步骤 5 会将 `changeRoot` 从仍在读取它的同步下移出，使更改存档并且主要规格从未更新。如果你的agent只能委托运行，则同步委托并等待结果。

如果同步报告任何停止或阻塞情况，则将同步视为失败。
 立即停止存档。不执行同步后内容比较，也不移动其 `changeRoot`.
 没有任何内容移动，因此用户可以修复阻止条件或重新运行同步。

同步写入每个主要规范后，根据规范同步合约验证其结构：
   - 新的主要规格以 `# <capability> Specification` 标题开头。现有的主要规范保持其标题不变。
   - 保留现有的 `## Purpose` 部分，对于既定的主要规格完全不受影响。
   - 对于新的主要规格，请逐字复制增量 `## Purpose`。仅当目的文本短于标准验证预期时发出警告。不要重新生成或重写现有的创作目的。如果未提供可用的 `## Purpose`，请使用现有的 TBD 目的行为和警告。
   - 验证主规范中是否保留有增量样式节标题（`## ADDED Requirements`、`## MODIFIED Requirements`、`## REMOVED Requirements`、`## RENAMED Requirements`），严格遵守同步工作流程格式化规则。
   - 要求阻止同步写入或更改使用 `### Requirement:` 标题，并且其场景使用规范的 `## Requirements` 部分下的 `#### Scenario:` 标题。保留三角洲未完全提及的内容。

然后从这一步的顶部重新运行比较，包括显式退役、缺少规格的情况，针对 `artifactPaths.specs.existingOutputPaths` 中具有增量规格的每个功能 - 不仅仅是同步报告它触及的功能。成功的同步不会留下任何可应用的内容，因此每个功能现在必须读取为已同步：
   - ADDED 要求存在
   - MODIFIED 需求包含增量中指定的场景和描述更改，其他场景保持不变
   - REMOVED 要求消失了——并且此同步取消了一项功能（删除了最后一个要求，将 `## Requirements` 留空），其主要规范被删除而不是留空。
   - RENAMED 要求在新名称下存在，在旧名称下不存在

如果同步失败或任何功能不匹配，请报告差异并停止 - 不存档。没有任何内容发生移动，并且 `changeRoot` 完好无损，因此用户可以修复不匹配或重新运行同步并再次启动存档。

5. **执行存档**

   创建一个 `archive` 目录下 `planningHome.changesDir` 如果不存在：
   ```bash
   mkdir -p "<planningHome.changesDir>/archive"
   ```

生成目标名称：当名称已以 `YYYY-MM-DD-` 前缀开头时，按原样使用更改名称；否则将当前日期添加为 `YYYY-MM-DD-<change-name>`。切勿叠加第二个日期（与 `openspec archive` 相同的规则）。

**检查目标是否已存在：**
   - 如果是：失败并出现错误，建议重命名现有存档或使用不同的日期
   - 如果没有：将`changeRoot`移动到存档目录

   ```bash
   mv "<changeRoot>" "<planningHome.changesDir>/archive/<target-name>"
   ```

6. **显示摘要**

显示存档完成摘要，包括：
   - 更改姓名
   - 使用的架构
   - 存档位置
   - 规格是否同步（如果适用）
   - 请注意任何警告（不完整 artifacts/tasks）

**成功时输出**

```markdown
## Archive Complete

**Change:** <change-name>
**Schema:** <schema-name>
**Archived to:** the archive path derived from `planningHome.changesDir`/<target-name>/
**Specs:** <"✓ Synced to main specs" only if the step 4 verification passed; otherwise "No delta specs" or "Sync skipped">

<"All artifacts complete. All tasks complete." — or, if archived with warnings, list them instead (e.g. "Archived with 2 incomplete tasks")>
```

**护栏**
- 宣布选定的变更；当不明确时提示选择
- 使用工件图（openspec状态--json）进行完成检查
- 不要因警告而阻止存档 - 只需通知并确认
- 移动到存档时保留 .openspec.yaml（它随目录一起移动）
- 显示所发生事件的清晰摘要
- 如果请求同步，请内联运行 `openspec-sync-specs` 工作流程（agent 驱动）
- 当规格同步仍在进行中时切勿存档 - 内联运行同步并在移动之前验证主要规格 `changeRoot`
- 如果存在差异规格，请始终运行同步评估并在提示之前显示组合摘要
- 应用相关的运行时上下文并报告冲突；操作指引仍为建议性
- 考虑每个指南条目并解释任何不适用或相互冲突的建议
- 现有的 CLI 检查、解析路径、提示和命令契约保持不变
- 工件规则仅约束正在编写的规范，而不是操作指南
- 切勿将运行时上下文、操作指南或工件规则文本逐字复制到输出文件中


## 原文对照

<details>
<summary>展开完整原文</summary>

Archive a completed change in the experimental workflow.

**Store selection:** If the user names a store (a store is a standalone OpenSpec repo registered on this machine) or the work lives in one, run `openspec store list --json` to discover registered store ids, then pass `--store <id>` on the commands that read or write specs and changes (`new change`, `status`, `instructions`, `list`, `show`, `validate`, `archive`, `doctor`, `context`, `schemas`, `view`). Once selected, treat `--store <id>` as sticky for the rest of the workflow. Every unscoped example of those commands below is shorthand: before running it, append the flag. For example, run `openspec status --change "<name>" --json --store "<id>"`, not the unscoped form shown below. Other commands do not take the flag. Hints printed by commands already carry the flag; keep it on follow-ups. Without a store, commands act on the nearest local `openspec/` root.

**Project check:** These steps expect a project that already uses OpenSpec. Before the first step that writes anything (`new change`, `archive`, `sync specs`, or authoring an artifact file), confirm the project has a root: run `openspec list --json` (with `--store <id>` when a store is selected, since the store is then the root) and read `root`. A root object means the project is set up. `"root": null` means it is not - there is no `openspec/` directory here, and a write such as `openspec new change` would create one as a side effect. The command also exits non-zero, which is that answer rather than a broken CLI, so read the JSON instead of retrying or working around it.

One `"root": null` is not about setup: when a `status` error message starts with `Declared in` or `Invalid store declaration in` and names this project's `openspec/config.yaml` (or `config.yml`), the project does use OpenSpec through a store it declares, which this machine cannot resolve (the store is not registered, or the `store:` line is malformed). Do not treat it as uninitialized and skip the branches below: stop before writing and show the user that error's `message` and `fix`.

Otherwise, with no root, what happens next depends on how this workflow was reached:

- **Auto-selected**: you chose this workflow yourself, without the user naming OpenSpec, naming this skill, or running its slash command. Stop using OpenSpec and answer the request normally, as you would with no OpenSpec installed. Do not ask them to set anything up and do not mention OpenSpec setup.
- **Explicit OpenSpec request**: the user named OpenSpec, named this skill, or ran its slash command. Stop before writing and ask how to proceed: set this project up (`openspec init`), target a store they already have (`--store <id>`), or continue without OpenSpec for this request. Wait for their answer.

In both branches, never create the root as a side effect: do not run `openspec init` until the user asks for it, do not hand-create `openspec/` files, and do not let a command create it.

`<capability-path>` is the spec directory relative to `specs/` (for example, `user-auth` or `identity/user-auth`). Preserve the full path from each delta spec when resolving its main spec.

**Input**: Optionally specify a change name. If omitted, check if it can be inferred from conversation context. If vague or ambiguous you MUST prompt for available changes.

**Steps**

1. **Select the change**

   If a name is provided, use it. Otherwise:
   - Infer from conversation context if the user mentioned a change
   - Auto-select if only one active change exists
   - If ambiguous, run `openspec list --json` to get available changes and ask the user to select one

   When prompting, show only active changes (not already archived).
   Include the schema used for each change if available.

   Always announce: "Using change: <name>" and how to override (e.g., `/openspec-archive-change <other>`).

   **Load current archive inputs before the existing archive checks:**

   After resolving the selected change and planning root, run:
   ```bash
   openspec instructions archive --change "<name>" --json
   ```
   Keep the same selected-root flags on this command. This lookup is advisory and
   optional: it only supplies extra prompt inputs, so it must never block archiving.
   If it exits non-zero or returns invalid JSON — for example on an older CLI that
   does not support this command yet — continue the archive workflow with no
   context and no operation guidance. Do not report an error and do not stop.

   A successful response may omit both optional fields. Treat `context` as a
   required prompt-level input: read and consider it, and apply relevant project
   facts, conventions, and constraints. Treat `operationGuidance` as optional
   additive advice: read and consider every entry, and follow entries that are
   applicable and compatible with the built-in archive workflow.

   Keep both fields separate from built-in steps, explicit user choices, resolved
   paths, CLI checks, and command contracts. If context conflicts with one of those
   controlling inputs, report the conflict and preserve the controlling value. If
   guidance is inapplicable or conflicts with a controlling input, do not follow it
   and explain why. Do not infer replacement paths, skipped prompts, or flags from
   either field, and do not copy their text verbatim into specs, change artifacts,
   or archive summaries unless the user separately asks for it. These are
   prompt-level behavior contracts, not enforceable checks.

2. **Check artifact completion status**

   Run `openspec status --change "<name>" --json` to check artifact completion.

   Parse the JSON to understand:
   - `schemaName`: The workflow being used
   - `planningHome`, `changeRoot`, `artifactPaths`, and `actionContext`: path and scope context
   - `artifacts`: List of artifacts with their status (`done`, `skipped`, or other)

   **If any artifacts are neither `done` nor `skipped`** (skipped artifacts satisfy the requirement - the change declares skip_specs):
   - Display warning listing incomplete artifacts
   - Ask the user to confirm they want to proceed
   - Proceed if user confirms

3. **Check task completion status**

   Run `openspec list --json` with the same selected-root flags and find the
   entry in `changes` whose `name` exactly matches the selected change.
   Require exactly one match and nonnegative integer `totalTasks` and
   `completedTasks`, with `completedTasks <= totalTasks`. The CLI resolves
   the schema's tracked task files, including custom artifact names, output
   paths, and globs.
   Incomplete tasks = `totalTasks - completedTasks`.

   Do not infer task completion from artifact status or the absence of a
   top-level `tasks.md`. If the lookup fails, returns invalid JSON, omits or
   duplicates the selected change, or returns invalid counts, report the problem
   and stop before syncing or archiving.
   The CLI counts only `x`/`X` checkbox markers as complete;
   other markers, including unfamiliar ones, remain incomplete.

   **If incomplete tasks found:**
   - Display warning showing count of incomplete tasks
   - Ask the user to confirm they want to proceed
   - Proceed if user confirms

   **If `totalTasks` is zero:** Proceed without a task-related warning.

4. **Assess delta spec sync state**

   Use `artifactPaths.specs.existingOutputPaths` from status JSON as the only
   delta-spec source. If the `specs` entry is missing or
   `existingOutputPaths` is empty, proceed without a sync prompt and do not infer
   delta specs from other artifacts.

   **If delta specs exist:**
   - Compare each delta spec with its corresponding main spec at `<planningHome.root>/openspec/specs/<capability-path>/spec.md` (use the store-aware `planningHome.root` from step 2, not a hardcoded repo path)
   - A missing main spec is **not automatically** "already synced". For a new capability, the main spec is an *output* of the sync, not an input:
     - If the delta has MODIFIED or RENAMED requirements, report that only ADDED requirements can create a new main spec and mark that capability as sync-blocked. Never invent a requirement that has no current version.
     - Otherwise, if the delta has only REMOVED requirements and the change's `.openspec.yaml` declares `retire_capabilities: true`, the capability is already retired: count it as already synced, warn that there is nothing left to remove, and do not recreate the main spec. Apply this rule both now and when verifying a completed sync.
     - Otherwise, if the delta has no ADDED requirements, report that no sync is possible and mark that capability as sync-blocked. For a REMOVED-only delta, warn that there is no main spec to remove from and leave the main-spec tree unchanged. `openspec archive` refuses the unmarked REMOVED-only case with `Spec must have at least one requirement`.
     - Otherwise, count the capability as needing sync and name it in the summary (`<capability-path>: new main spec will be created`). If the delta also has REMOVED requirements, warn that they will be ignored because there is no main spec to remove from. The sync creates the main spec from only the delta's ADDED requirements, exactly as `openspec archive` does.
   - Determine what changes would be applied (adds, modifications, removals, renames)
   - Continue assessing the remaining capabilities even when one is sync-blocked. Show a combined summary before prompting.

   **Prompt options:**
   - If any capability is sync-blocked: explain why and offer only "Archive without syncing", "Cancel"
   - Otherwise, if changes needed: "Sync now (recommended)", "Archive without syncing"
   - Otherwise, if already synced: "Archive now", "Sync anyway", "Cancel"

   Route on the answer:
   - "Cancel" — stop, do not archive
   - "Archive without syncing" or "Archive now" — proceed to archive
   - "Sync now" or "Sync anyway" — sync, then verify (below). Do not start any sync while a capability is sync-blocked; explain the blocker and repeat the available choices.
   - Anything else — ask again rather than archiving

   Before a selected sync writes any main spec, run
   `openspec instructions specs --change "<name>" --json` once with the same
   selected-root flags. Require a zero exit status and valid artifact-instruction
   JSON. If the lookup fails or returns invalid JSON, report the error and stop
   before writing any main spec or moving the change. A valid response with omitted
   `rules` is the no-rules case. Apply returned `rules` only to the content and
   form of main specs produced by this merge; do not use them as archive guidance,
   change CLI behavior, or copy the rule text into any output file.

   Then run the `openspec-sync-specs` workflow inline (agent-driven intelligent merge) for change '<name>', passing the delta spec analysis and the fetched specs-rule snapshot from above, and wait for it to finish. The inline sync must reuse that snapshot without fetching `specs` instructions again. Do not delegate it to a background task — step 5 would move `changeRoot` out from under a sync that is still reading it, leaving the change archived and the main specs never updated. If your agent can only run it by delegation, delegate synchronously and wait for the result.

   If the sync reports any stop or blocking condition, treat the sync as failed.
   Stop the archive immediately. Do not perform the post-sync content comparison and do not move its `changeRoot`.
   Nothing has moved, so the user can fix the blocking condition or re-run the sync.

   After the sync writes each main spec, verify its structure against the canonical sync contract:
   - A new main spec starts with a `# <capability> Specification` title. An existing main spec keeps its title exactly as it is.
   - Preserve existing `## Purpose` sections completely untouched for established main specs.
   - For a new main spec, copy the delta `## Purpose` verbatim. Warn only if the purpose text is shorter than standard validation expects. Do not regenerate or rewrite existing authored purpose. If no usable `## Purpose` is provided, use the existing TBD Purpose behavior and warning.
   - Verify that no delta-style section headers (`## ADDED Requirements`, `## MODIFIED Requirements`, `## REMOVED Requirements`, `## RENAMED Requirements`) remain in the main spec, adhering strictly to the sync workflow formatting rules.
   - Requirement blocks the sync wrote or changed use `### Requirement:` headings, and their scenarios use `#### Scenario:` headings, under the spec's `## Requirements` section. Leave content the delta does not mention exactly as it is.

   Then re-run the comparison from the top of this step, including the explicitly retired, missing-spec case, against every capability that has a delta spec in `artifactPaths.specs.existingOutputPaths` — not only the ones the sync reports it touched. A successful sync leaves nothing left to apply, so each capability must now read as already synced:
   - ADDED requirements present
   - MODIFIED requirements carrying the scenario and description changes named in the delta, with their other scenarios intact
   - REMOVED requirements gone — and where this sync retired a capability (removed its last requirement, leaving `## Requirements` empty), its main spec deleted rather than left empty.
   - RENAMED requirements present under the new name and absent under the old one

   If the sync failed, or any capability does not match, report what differs and stop — do not archive. Nothing has moved and `changeRoot` is intact, so the user can fix the mismatch or re-run the sync and start the archive again.

5. **Perform the archive**

   Create an `archive` directory under `planningHome.changesDir` if it doesn't exist:
   ```bash
   mkdir -p "<planningHome.changesDir>/archive"
   ```

   Generate the target name: use the change name as-is when it already starts with a `YYYY-MM-DD-` prefix; otherwise prepend the current date as `YYYY-MM-DD-<change-name>`. Never stack a second date (same rule as `openspec archive`).

   **Check if target already exists:**
   - If yes: Fail with error, suggest renaming existing archive or using different date
   - If no: Move `changeRoot` to the archive directory

   ```bash
   mv "<changeRoot>" "<planningHome.changesDir>/archive/<target-name>"
   ```

6. **Display summary**

   Show archive completion summary including:
   - Change name
   - Schema that was used
   - Archive location
   - Whether specs were synced (if applicable)
   - Note about any warnings (incomplete artifacts/tasks)

**Output On Success**

```markdown
## Archive Complete

**Change:** <change-name>
**Schema:** <schema-name>
**Archived to:** the archive path derived from `planningHome.changesDir`/<target-name>/
**Specs:** <"✓ Synced to main specs" only if the step 4 verification passed; otherwise "No delta specs" or "Sync skipped">

<"All artifacts complete. All tasks complete." — or, if archived with warnings, list them instead (e.g. "Archived with 2 incomplete tasks")>
```

**Guardrails**
- Announce the selected change; prompt for selection when it is ambiguous
- Use artifact graph (openspec status --json) for completion checking
- Don't block archive on warnings - just inform and confirm
- Preserve .openspec.yaml when moving to archive (it moves with the directory)
- Show clear summary of what happened
- If sync is requested, run the `openspec-sync-specs` workflow inline (agent-driven)
- Never archive while a spec sync is still in flight — run the sync inline and verify the main specs before moving `changeRoot`
- If delta specs exist, always run the sync assessment and show the combined summary before prompting
- Apply relevant runtime context and report conflicts; operation guidance remains advisory
- Consider every guidance entry and explain any inapplicable or conflicting advice
- Existing CLI checks, resolved paths, prompts, and command contracts are unchanged
- Artifact rules constrain only the specs being written and are never operation guidance
- Never copy runtime context, operation guidance, or artifact-rule text verbatim into output files


</details>
