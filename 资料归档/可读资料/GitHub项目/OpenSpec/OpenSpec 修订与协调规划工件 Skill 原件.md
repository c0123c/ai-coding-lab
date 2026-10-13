# OpenSpec 修订与协调规划工件 Skill 原件

定义读取现有变更工件、理解修订要求、逐份协调并更新规划内容，以及完成后提供下一步指引的流程。

- 来源：OpenSpec
- 资料类型：SKILL.md：规划工件修订代理指令原件
- 存档版本：`9111a7654d78`。
- 文档时间：发表／更新：未知；抓取核验：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/skills/openspec-update-change/SKILL.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenSpec/skills/openspec-update-change/SKILL.md>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: openspec-update-change
description: Update an OpenSpec change by revising its existing planning artifacts and keeping them coherent with one another. Use when the user wants to revise a change's plan, fold new decisions into it, or reconcile its artifacts after an edit. Also use when the user says "openspec update change" or "opsx update". If the user means the openspec update CLI command, which refreshes generated files, run that command instead. Never edits code.
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

修改变更的现有计划工件并保持它们的连贯性。切勿编辑代码。

**商店选择：** 如果用户命名商店（商店是在本机上注册的独立 OpenSpec 存储库）或工作位于其中，请运行 `openspec store list --json` 以发现注册的商店 ID，然后在读取或写入规格和更改的命令上传递 `--store <id>` (`new change`、`status`、`instructions`、`list`、`show`、`validate`、`archive`、`doctor`、`context`、 `schemas`、`view`）。选择后，将 `--store <id>` 视为工作流程其余部分的粘性。下面这些命令的每个未指定范围的示例都是简写形式：在运行之前，附加标志。例如，运行 `openspec status --change "<name>" --json --store "<id>"`，而不是下面显示的无作用域形式。其他命令不采用该标志。命令打印的提示已经带有该标志；继续跟进。如果没有存储，命令将作用于最近的本地 `openspec/` 根。

**项目检查：** 这些步骤需要已使用 OpenSpec 的项目。在写入任何内容（`new change`、`archive`、`sync specs`或创作工件文件）的第一步之前，请确认项目具有根：运行`openspec list --json`（选择商店时使用`--store <id>`，因为该商店是根）并读取`root`。根对象意味着项目已设置。 `"root": null` 意味着它不是 - 这里没有 `openspec/` 目录，并且诸如 `openspec new change` 之类的写入会创建一个作为副作用。该命令也以非零值退出，这是答案而不是损坏的 CLI，因此请阅读 JSON 而不是重试或解决它。

其中一个 `"root": null` 与设置无关：当 `status` 错误消息以 `Declared in` 或 `Invalid store declaration in` 开头并命名该项目的 `openspec/config.yaml`（或 `config.yml`）时，该项目确实通过存储使用 OpenSpec声明，本机无法解析（商店未注册，或 `store:` 行格式错误）。不要将其视为未初始化并跳过以下分支：在写入之前停止并向用户显示错误为 `message` 和 `fix`。

否则，在没有 root 的情况下，接下来发生的情况取决于如何实现此工作流程：

- **自动选择**：您自己选择此工作流程，无需用户命名OpenSpec、命名此技能或运行其斜线命令。停止使用 OpenSpec 并正常应答请求，就像未安装 OpenSpec 时一样。不要要求他们设置任何内容，也不要提及 OpenSpec 设置。
- **显式OpenSpec请求**：名为OpenSpec的用户，命名了该技能，或运行了其斜线命令。在写作之前停下来，询问如何继续：设置此项目 (`openspec init`)，定位他们已有的商店 (`--store <id>`)，或者在没有 OpenSpec 的情况下继续此请求。等待他们的答复。

在这两个分支中，永远不要创建根作为副作用：在用户要求之前不要运行 `openspec init`，不要手动创建 `openspec/` 文件，并且不要让命令创建它。

**输入**：可以选择指定更改名称。如果省略，请检查是否可以从对话上下文中推断出来。如果含糊或不明确，您 MUST 会提示可用的更改。

该工作流程修改了已经存在的工件； `/openspec-continue-change` 是创造那些不存在的东西的原因。

**步骤**

1. **选择更改**

如果提供了名称，请使用它。否则：
   - 从对话上下文中推断用户是否提到了更改
   - 如果仅存在一项活动更改，则自动选择
   - 如果不明确，请运行 `openspec list --json` 以获取按最近修改排序的可用更改，并要求用户选择一项

出现提示时，将最近修改的最前面的 3-4 作为选项显示，显示：
   - 更改姓名
   - 状态（例如“0/5任务”、“完成”、“无任务”）
   - 最近修改的时间（来自 `lastModified` 字段）

将最近修改的更改标记为“（推荐）”，因为它可能是用户想要更新的内容。

始终宣布：“使用更改：<name>”以及如何覆盖（例如，`/openspec-update-change <other>`）。

2. **获取变更的工件**
   ```bash
   openspec status --change "<name>" --json
   ```
解析 JSON 以了解当前状态。响应内容包括：
   - `schemaName`：正在使用的工作流程模式（例如“规范驱动”）
   - `artifacts`：工件及其状态的数组（“完成”、“跳过”、“就绪”、“阻止”）
   - `isPlanningComplete`：指示所有规划工件是否完成的布尔值。较旧的 CLI 版本公开与 `isComplete` 相同的值。
   - `planningHome`、`changeRoot`、`artifactPaths` 和 `actionContext`：路径和范围上下文。使用这些而不是假设存储库本地路径。

工件 ID 和路径来自活动模式 - NOT 假定它们，并对硬编码工件名称执行 NOT 分支。自定义架构必须保持不变。

要编辑的文件是 `artifactPaths.<id>.existingOutputPaths` - 磁盘上存在的具体文件，已经针对 glob 工件进行了 glob 扩展（例如 `specs/**/*.md`）。执行 NOT 写入 `resolvedOutputPath`：对于 glob 工件，它仍然是 glob 模式，而不是真正的文件。

3. **了解请求**
   - 如果用户要求特定的修订（“设计现在使用 X”），那就是开始编辑。
   - 如果他们只说“更新”/“使其连贯”，请将其视为连贯性审查：阅读现有工件并相互检查它们是否存在矛盾、差距和重复。

4. **读取并协调**
   - 读取请求涉及的工件以及更改的其他现有工件。
   - 在对话中而不是在文件中起草请求的编辑。准确地弄清楚它改变了什么；步骤 5 拥有每次写入。然后根据起草的编辑检查所有其他现有工件 - 在 ANY 方向：对较晚工件的编辑可能需要修改较早的工件，而不仅仅是相反。构建顺序是一个有用的阅读顺序，而不是对可以修改的工件的限制。
   - 注意现在不一致、缺失或矛盾的所有内容。
   - 对已存在的文件提出修订建议 (`existingOutputPaths`)。如果工件没有现有的输出文件和状态 `ready` 或 `blocked`，请记下它并引导用户到 `/openspec-continue-change` 来创建它们。保持`skipped`文物不变；不要将它们视为缺失或将它们推迟到继续工作流程。
   - 在至少一个文件匹配后，全局工件（例如 `specs/**/*.md`）会被标记为 `done`，并且继续工作流程仅处理 `ready` 工件。当协调发现 `existingOutputPaths` 非空的 glob 工件丢失文件时：
     1. 运行`openspec instructions "<artifact-id>" --change "<name>" --json`并使用其`instruction`和`template`。将`context`和`rules`视为约束；不要将它们复制到文件中。如果说明报告 `skipped: true`，请不要创建该文件。从磁盘读取当前依赖文件；如果缺少必需的不可跳过的依赖项，请停止并要求用户先恢复它。
     2. 选择 `changeRoot` 内与 `artifactPaths.<id>.outputPath` 匹配且尚不存在的具体路径。解析任何符号链接的父目录后，验证它是否保留在 `changeRoot` 内。 glob `resolvedOutputPath` 不是有效目标。
     3. 将新文件包含在步骤 5 的建议修订中，并仅在用户确认后创建。
     4. 确认后，在创建之前，刷新状态和说明。验证工件仍在范围内、未跳过且已部分填充；重复上面的具体路径检查。
     5. 如果目标已存在，则使用创建操作会失败。如果`instruction`将创建委托给另一个技能或命令，则仅当它可以遵循已确认的路径和这些护栏时才调用它；否则停止。如果任何检查失败或确认的草稿不再有效，请停止并与用户协调，而不是替换现有内容或选择不同的路径。
   - 如果变化已经是连贯的，请说出来并且不提出任何修改。

5. **确认并应用，一次一件神器**
   - 此步骤执行此工作流程中的每个工件写入；没有早期步骤会编辑工件。
   - 显示每个提议的修订及其原因 - 包括步骤 4 中起草的请求编辑。待用户确认后才写入。
   - 如果用户拒绝修订，则不要编写它 - 保持该工件不变。
   - 当需要大量重写时，首先获取该工件的规则和模板：
     ```bash
     openspec instructions "<artifact-id>" --change "<name>" --json
     ```

6. **指向下一步（仅供参考 - NEVER 执行操作）**
   - 具有空 `existingOutputPaths` 和状态 `ready` 或 `blocked` 的工件 -> 建议 `/openspec-continue-change` 创建它们。
   - 更改已实施（任务已检查/已应用）-> 代码可能不再与修订后的计划匹配；建议 `/openspec-apply-change` 将增量带入代码中。
   - 一切都已完成并已实施 -> 建议 `/openspec-archive-change`。

**输出**

每次调用后，显示：
- 哪些工件被修订（以及哪些提议的修订被拒绝）
- 在已部分填充的 glob 工件下创建的任何文件
- 任何推迟到 `/openspec-continue-change` 的内容（尚无文件且状态为 `ready` 或 `blocked` 的工件，绝不是 `skipped` 工件）
- 更改的位置以及推荐的下一个命令

**护栏**
- 仅规划工件 - NEVER 编辑实现代码。如果修改后的计划意味着代码更改，请停止并指向`/openspec-apply-change`。
- 使用`openspec status`报告的工件ID和路径；永远不要在硬编码的工件名称上分支。
- 只编辑`existingOutputPaths`中的具体文件；切勿写入 glob `resolvedOutputPath`。
- 不要推进构建边界：如果工件具有空的 `existingOutputPaths` 和状态 `ready` 或 `blocked`，那就是 `/openspec-continue-change` 的工作。保持 `skipped` 工件不变。唯一的新文件范围是 `existingOutputPaths` 非空的 glob 工件下已确认的具体路径。
- 在编写之前与用户确认每次编辑。
- 如果请求更改了更改的 * 意图 * 而不是对其进行改进，建议使用 `/openspec-new-change` 重新开始（“更新与重新开始”启发式）。


## 原文对照

<details>
<summary>展开完整原文</summary>

Revise a change's existing planning artifacts and keep them coherent. Never edit code.

**Store selection:** If the user names a store (a store is a standalone OpenSpec repo registered on this machine) or the work lives in one, run `openspec store list --json` to discover registered store ids, then pass `--store <id>` on the commands that read or write specs and changes (`new change`, `status`, `instructions`, `list`, `show`, `validate`, `archive`, `doctor`, `context`, `schemas`, `view`). Once selected, treat `--store <id>` as sticky for the rest of the workflow. Every unscoped example of those commands below is shorthand: before running it, append the flag. For example, run `openspec status --change "<name>" --json --store "<id>"`, not the unscoped form shown below. Other commands do not take the flag. Hints printed by commands already carry the flag; keep it on follow-ups. Without a store, commands act on the nearest local `openspec/` root.

**Project check:** These steps expect a project that already uses OpenSpec. Before the first step that writes anything (`new change`, `archive`, `sync specs`, or authoring an artifact file), confirm the project has a root: run `openspec list --json` (with `--store <id>` when a store is selected, since the store is then the root) and read `root`. A root object means the project is set up. `"root": null` means it is not - there is no `openspec/` directory here, and a write such as `openspec new change` would create one as a side effect. The command also exits non-zero, which is that answer rather than a broken CLI, so read the JSON instead of retrying or working around it.

One `"root": null` is not about setup: when a `status` error message starts with `Declared in` or `Invalid store declaration in` and names this project's `openspec/config.yaml` (or `config.yml`), the project does use OpenSpec through a store it declares, which this machine cannot resolve (the store is not registered, or the `store:` line is malformed). Do not treat it as uninitialized and skip the branches below: stop before writing and show the user that error's `message` and `fix`.

Otherwise, with no root, what happens next depends on how this workflow was reached:

- **Auto-selected**: you chose this workflow yourself, without the user naming OpenSpec, naming this skill, or running its slash command. Stop using OpenSpec and answer the request normally, as you would with no OpenSpec installed. Do not ask them to set anything up and do not mention OpenSpec setup.
- **Explicit OpenSpec request**: the user named OpenSpec, named this skill, or ran its slash command. Stop before writing and ask how to proceed: set this project up (`openspec init`), target a store they already have (`--store <id>`), or continue without OpenSpec for this request. Wait for their answer.

In both branches, never create the root as a side effect: do not run `openspec init` until the user asks for it, do not hand-create `openspec/` files, and do not let a command create it.

**Input**: Optionally specify a change name. If omitted, check if it can be inferred from conversation context. If vague or ambiguous you MUST prompt for available changes.

This workflow revises artifacts that already exist; `/openspec-continue-change` is what creates the ones that do not.

**Steps**

1. **Select the change**

   If a name is provided, use it. Otherwise:
   - Infer from conversation context if the user mentioned a change
   - Auto-select if only one active change exists
   - If ambiguous, run `openspec list --json` to get available changes sorted by most recently modified, and ask the user to select one

   When prompting, present the top 3-4 most recently modified changes as options, showing:
   - Change name
   - Status (e.g., "0/5 tasks", "complete", "no tasks")
   - How recently it was modified (from `lastModified` field)

   Mark the most recently modified change as "(Recommended)" since it's likely what the user wants to update.

   Always announce: "Using change: <name>" and how to override (e.g., `/openspec-update-change <other>`).

2. **Get the change's artifacts**
   ```bash
   openspec status --change "<name>" --json
   ```
   Parse the JSON to understand current state. The response includes:
   - `schemaName`: The workflow schema being used (e.g., "spec-driven")
   - `artifacts`: Array of artifacts with their status ("done", "skipped", "ready", "blocked")
   - `isPlanningComplete`: Boolean indicating if all planning artifacts are complete. Older CLI versions expose the same value as `isComplete`.
   - `planningHome`, `changeRoot`, `artifactPaths`, and `actionContext`: path and scope context. Use these instead of assuming repo-local paths.

   The artifact ids and paths come from the active schema - do NOT assume them, and do NOT branch on hardcoded artifact names. Custom schemas must work unchanged.

   The files to edit are `artifactPaths.<id>.existingOutputPaths` - the concrete files that exist on disk, already glob-expanded for glob artifacts (e.g. `specs/**/*.md`). Do NOT write to `resolvedOutputPath`: for a glob artifact it is still the glob pattern, not a real file.

3. **Understand the request**
   - If the user asked for a specific revision ("the design now uses X"), that is the starting edit.
   - If they only said "update" / "make this coherent", treat it as a coherence review: read the existing artifacts and check them against each other for contradictions, gaps, and duplication.

4. **Read and reconcile**
   - Read the artifact(s) the request touches and the change's other existing artifacts.
   - Draft the requested edit in the conversation, not in files. Work out exactly what it changes; step 5 owns every write. Then check every other existing artifact against the drafted edit - in ANY direction: an edit to a later artifact may require revising an earlier one, not only the other way around. Build order is a useful reading order, not a constraint on which artifacts may be revised.
   - Note everything that is now inconsistent, missing, or contradictory.
   - Propose revisions to files that already exist (`existingOutputPaths`). If an artifact has no existing output files and status `ready` or `blocked`, note it and point the user to `/openspec-continue-change` to create them. Leave `skipped` artifacts untouched; do not treat them as missing or defer them to the continue workflow.
   - A glob artifact (e.g. `specs/**/*.md`) is marked `done` after at least one file matches, and the continue workflow only handles `ready` artifacts. When reconciliation identifies a missing file for a glob artifact whose `existingOutputPaths` is non-empty:
     1. Run `openspec instructions "<artifact-id>" --change "<name>" --json` and use its `instruction` and `template`. Treat `context` and `rules` as constraints; do not copy them into the file. If instructions report `skipped: true`, do not create the file. Read current dependency files from disk; if a required non-skipped dependency is missing, stop and ask the user to restore it first.
     2. Choose a concrete path inside `changeRoot` that matches `artifactPaths.<id>.outputPath` and does not already exist. Verify it remains inside `changeRoot` after resolving any symlinked parent directories. The glob `resolvedOutputPath` is not a valid target.
     3. Include the new file in step 5's proposed revisions and create it only after the user confirms.
     4. After confirmation, immediately before creation, refresh status and instructions. Verify the artifact is still in scope, not skipped, and partially populated; repeat the concrete-path checks above.
     5. Use a create operation that fails if the target already exists. If `instruction` delegates creation to another skill or command, invoke it only if it can honor the confirmed path and these guardrails; otherwise stop. If any check fails or the confirmed draft is no longer valid, stop and reconcile with the user rather than replacing existing content or choosing a different path.
   - If the change is already coherent, say so and propose no revisions.

5. **Confirm and apply, one artifact at a time**
   - This step performs every artifact write in this workflow; no earlier step edits an artifact.
   - Show each proposed revision and why - including the requested edit drafted in step 4. Write only after the user confirms.
   - If the user rejects a revision, do not write it - leave that artifact unchanged.
   - When a substantial rewrite is needed, get that artifact's rules and template first:
     ```bash
     openspec instructions "<artifact-id>" --change "<name>" --json
     ```

6. **Point to the next step (guidance only - NEVER act on it)**
   - Artifacts with empty `existingOutputPaths` and status `ready` or `blocked` -> suggest `/openspec-continue-change` to create them.
   - Change already implemented (tasks checked off / already applied) -> the code may no longer match the revised plan; suggest `/openspec-apply-change` to carry the delta into code.
   - Everything done and implemented -> suggest `/openspec-archive-change`.

**Output**

After each invocation, show:
- Which artifacts were revised (and which proposed revisions were rejected)
- Any file created under a glob artifact that was already partially populated
- Anything deferred to `/openspec-continue-change` (artifacts with no files yet and status `ready` or `blocked`, never `skipped` artifacts)
- Where the change stands and the recommended next command

**Guardrails**
- Planning artifacts only - NEVER edit implementation code. If the revised plan implies code changes, stop and point to `/openspec-apply-change`.
- Use the artifact ids and paths reported by `openspec status`; never branch on hardcoded artifact names.
- Edit only the concrete files in `existingOutputPaths`; never write to a glob `resolvedOutputPath`.
- Do not advance the build frontier: if an artifact has empty `existingOutputPaths` and status `ready` or `blocked`, that is `/openspec-continue-change`'s job. Leave `skipped` artifacts untouched. The only new-file scope is a confirmed concrete path under a glob artifact whose `existingOutputPaths` is non-empty.
- Confirm every edit with the user before writing.
- If the request changes the change's *intent* rather than refining it, recommend starting fresh with `/openspec-new-change` (the "Update vs. Start Fresh" heuristic).


</details>
