# OpenSpec 继续生成下一工件 Skill 原件

定义检查现有变更状态、根据工件依赖创建下一份工件，并报告进度与后续步骤的代理指令。

- 来源：OpenSpec
- 资料类型：SKILL.md：继续工件生成代理指令原件
- 存档版本：`9111a7654d78`。
- 文档时间：发表／更新：未知；抓取核验：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/skills/openspec-continue-change/SKILL.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenSpec/skills/openspec-continue-change/SKILL.md>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: openspec-continue-change
description: Continue working on an OpenSpec change by creating the next artifact. Use when the user wants to progress their change, create the next artifact, or continue their workflow. Also use when the user says "openspec continue" or "opsx continue".
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

Continue 通过创建下一个工件来进行更改。

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
   - 如果不明确，请运行 `openspec list --json` 以获取按最近修改排序的可用更改，并要求用户选择一项

出现提示时，将最近修改的最前面的 3-4 作为选项显示，显示：
   - 更改姓名
   - 状态（例如“0/5任务”、“完成”、“无任务”）
   - 最近修改的时间（来自 `lastModified` 字段）

将最近修改的更改标记为“（推荐）”，因为它可能是用户想要继续的。

始终宣布：“使用更改：<name>”以及如何覆盖（例如，`/openspec-continue-change <other>`）。

2. **检查当前状态**
   ```bash
   openspec status --change "<name>" --json
   ```
解析 JSON 以了解当前状态。响应内容包括：
   - `schemaName`：正在使用的工作流程模式（例如“规范驱动”）
   - `artifacts`：工件及其状态的数组（“完成”、“跳过”、“就绪”、“阻止”）
   - `isPlanningComplete`：指示所有规划工件是否完成的布尔值。较旧的 CLI 版本公开与 `isComplete` 相同的值。
   - `planningHome`、`changeRoot`、`artifactPaths` 和 `actionContext`：路径和范围上下文。使用这些而不是假设存储库本地路径。

3. **根据状态采取行动**：

   ---

**首先，完成一个部分编写的工件**：
   - 输出为 glob 模式的工件（例如 `specs/**/*.md`）一旦存在一个匹配文件就会读取 `done`，因此在写入其文件时中断的运行会使其 `done` 中仍然缺少一些文件
   - 对于每个这样的 `done` 工件，首先获取其说明并读取确定预期文件所需的任何完整依赖项。然后将这些文件与 `artifactPaths.<id>.existingOutputPaths` 进行比较。对于规范驱动的 `specs`，请阅读提案并检查其列出的每个功能是否有一个 `specs/<capability-path>/spec.md`；在比较路径标识之前，根据 `changeRoot` 解析每个预期路径
   - 如果缺少任何文件，则仅写入缺少的文件和 STOP - 算作此调用的 ONE 工件。如果您无法判断某个文件是否是故意遗漏的，请询问用户

   ---

**如果所有规划工件均已完成（`isPlanningComplete: true` 或旧版 `isComplete: true`）**：
   - 恭喜用户
   - 显示最终状态，包括所使用的架构
   - 建议：“规划已完成！您现在可以实施此更改。实施和所有跟踪工作完成后，将其存档。”
   - STOP

   ---

**如果工件已准备好创建**（状态显示带有`status: "ready"`的工件）：
   - 从状态输出中选择带有 `status: "ready"` 的 FIRST 工件
   - 获取其说明：
     ```bash
     openspec instructions <artifact-id> --change "<name>" --json
     ```
   - 解析JSON。关键字段是：
     - `context`：项目背景（对您的限制 - 执行 NOT 包含在输出中）
     - `rules`：特定于工件的规则（对您的约束 - 执行 NOT 包含在输出中）
     - `template`：输出文件使用的结构
     - `instruction`：特定于架构的指导
     - `resolvedOutputPath`：写入工件的解析路径或模式
     - `dependencies`：要读取上下文的已完成工件（带有 `skipped: true` 的条目没有文件 - 不要查找它们）
     - `skipped`/`warning`：当更改声明 skip_specs 时出现，并且必须创建此工件 NOT - 选择另一个工件
   - **创建工件文件**：
     - 读取任何已完成的依赖文件以获取上下文 - 始终从磁盘重新读取它们，即使您在对话的早期看到它们（用户可能已经编辑过它们）
     - 如果 `instruction` 字段将创建委托给特定技能或命令，则调用它来生成工件而不是自己编写文件，然后验证工件文件是否存在于 `resolvedOutputPath`
     - 否则使用 `template` 作为结构 - 填写其部分
     - 写入时应用 `context` 和 `rules` 作为约束 - 但将 NOT 将它们复制到文件中
     - 写入指令中指定的`resolvedOutputPath`。如果是 glob 模式，请使用模式指令和更改的上下文选择具体文件路径
   - 显示已创建的内容和现已解锁的内容
   - 创建 ONE 神器后的 STOP

   ---

**如果没有工件准备好（全部被阻止）**：
   - 使用有效的架构不应发生这种情况
   - 显示状态并建议检查问题

4. **创建工件后，显示进度**
   ```bash
   openspec status --change "<name>"
   ```

**输出**

每次调用后，显示：
- 创建了哪个工件
- 正在使用的架构工作流程
- 当前进度（N/M完成）
- 现在解锁了哪些神器
- 提示：“想继续吗？请让我继续或告诉我下一步该怎么做。”

**神器创建指南**

工件类型及其用途取决于架构。指令输出中的 `instruction` 字段是每个工件的权威指南 - 即使工件具有熟悉的名称（proposal.md、tasks.md 等），也要遵循它，因为自定义模式可能会为相同的文件名定义不同的内容或不同的过程。

如果 `instruction` 字段指示您使用特定技能或命令来创建工件，请调用它而不是直接写入工件。

**护栏**
- 每次调用创建 ONE 工件
- 在创建新的依赖项之前始终读取依赖项 - 从磁盘重新读取，而不是从会话内存中读取（自上次查看文件以来，文件可能已更改）
- 切勿跳过工件或无序创建
- 如果上下文不清楚，请在创建之前询问用户
- 在标记进度之前验证写入后工件文件是否存在
- 使用模式的工件序列，不要假设特定的工件名称
- **IMPORTANT**：`context`和`rules`是YOU的约束，而不是文件的内容
  - 是否将 NOT 将 `<context>`、`<rules>`、`<project_context>` 块复制到工件中
  - 这些指导您编写的内容，但不应出现在输出中


## 原文对照

<details>
<summary>展开完整原文</summary>

Continue working on a change by creating the next artifact.

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
   - If ambiguous, run `openspec list --json` to get available changes sorted by most recently modified, and ask the user to select one

   When prompting, present the top 3-4 most recently modified changes as options, showing:
   - Change name
   - Status (e.g., "0/5 tasks", "complete", "no tasks")
   - How recently it was modified (from `lastModified` field)

   Mark the most recently modified change as "(Recommended)" since it's likely what the user wants to continue.

   Always announce: "Using change: <name>" and how to override (e.g., `/openspec-continue-change <other>`).

2. **Check current status**
   ```bash
   openspec status --change "<name>" --json
   ```
   Parse the JSON to understand current state. The response includes:
   - `schemaName`: The workflow schema being used (e.g., "spec-driven")
   - `artifacts`: Array of artifacts with their status ("done", "skipped", "ready", "blocked")
   - `isPlanningComplete`: Boolean indicating if all planning artifacts are complete. Older CLI versions expose the same value as `isComplete`.
   - `planningHome`, `changeRoot`, `artifactPaths`, and `actionContext`: path and scope context. Use these instead of assuming repo-local paths.

3. **Act based on status**:

   ---

   **Before anything else, finish a partly written artifact**:
   - An artifact whose output is a glob pattern (e.g. `specs/**/*.md`) reads `done` as soon as one matching file exists, so a run interrupted while writing its files leaves it `done` with some still missing
   - For each such `done` artifact, first get its instructions and read any completed dependencies needed to determine the expected files. Then compare those files with `artifactPaths.<id>.existingOutputPaths`. For spec-driven's `specs`, read the proposal and check for one `specs/<capability-path>/spec.md` per capability it lists; resolve each expected path against `changeRoot` before comparing path identity
   - If any are missing, write only the missing files, and STOP - that counts as this invocation's ONE artifact. If you cannot tell whether a file was left out on purpose, ask the user

   ---

   **If all planning artifacts are complete (`isPlanningComplete: true`, or legacy `isComplete: true`)**:
   - Congratulate the user
   - Show final status including the schema used
   - Suggest: "Planning is complete! You can now implement this change. Once implementation and any tracked work are complete, archive it."
   - STOP

   ---

   **If artifacts are ready to create** (status shows artifacts with `status: "ready"`):
   - Pick the FIRST artifact with `status: "ready"` from the status output
   - Get its instructions:
     ```bash
     openspec instructions <artifact-id> --change "<name>" --json
     ```
   - Parse the JSON. The key fields are:
     - `context`: Project background (constraints for you - do NOT include in output)
     - `rules`: Artifact-specific rules (constraints for you - do NOT include in output)
     - `template`: The structure to use for your output file
     - `instruction`: Schema-specific guidance
     - `resolvedOutputPath`: Resolved path or pattern to write the artifact
     - `dependencies`: Completed artifacts to read for context (entries with `skipped: true` have no files - do not look for them)
     - `skipped`/`warning`: present when the change declares skip_specs and this artifact must NOT be created - pick another artifact
   - **Create the artifact file**:
     - Read any completed dependency files for context - always re-read them from disk, even if you saw them earlier in the conversation (the user may have edited them)
     - If the `instruction` field delegates creation to a specific skill or command, invoke it to produce the artifact instead of writing the file yourself, then verify the artifact file exists at `resolvedOutputPath`
     - Otherwise use `template` as the structure - fill in its sections
     - Apply `context` and `rules` as constraints when writing - but do NOT copy them into the file
     - Write to the `resolvedOutputPath` specified in instructions. If it is a glob pattern, choose the concrete file path using the schema instruction and the change's context
   - Show what was created and what's now unlocked
   - STOP after creating ONE artifact

   ---

   **If no artifacts are ready (all blocked)**:
   - This shouldn't happen with a valid schema
   - Show status and suggest checking for issues

4. **After creating an artifact, show progress**
   ```bash
   openspec status --change "<name>"
   ```

**Output**

After each invocation, show:
- Which artifact was created
- Schema workflow being used
- Current progress (N/M complete)
- What artifacts are now unlocked
- Prompt: "Want to continue? Just ask me to continue or tell me what to do next."

**Artifact Creation Guidelines**

The artifact types and their purpose depend on the schema. The `instruction` field from the instructions output is the authoritative guidance for each artifact - follow it even when the artifact has a familiar name (proposal.md, tasks.md, etc.), since custom schemas may define different content or a different process for the same file names.

If the `instruction` field directs you to use a specific skill or command to create the artifact, invoke it instead of writing the artifact directly.

**Guardrails**
- Create ONE artifact per invocation
- Always read dependency artifacts before creating a new one - re-read from disk, not from conversation memory (files may have changed since you last saw them)
- Never skip artifacts or create out of order
- If context is unclear, ask the user before creating
- Verify the artifact file exists after writing before marking progress
- Use the schema's artifact sequence, don't assume specific artifact names
- **IMPORTANT**: `context` and `rules` are constraints for YOU, not content for the file
  - Do NOT copy `<context>`, `<rules>`, `<project_context>` blocks into the artifact
  - These guide what you write, but should never appear in the output


</details>
