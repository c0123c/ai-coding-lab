# OpenSpec 快速生成全部工件 Skill 原件

定义建立变更、读取工件依赖顺序，并一次生成进入实施阶段所需全部规划工件的代理指令。

- 来源：OpenSpec
- 资料类型：SKILL.md：快速工件生成代理指令原件
- 存档版本：`9111a7654d78`。
- 文档时间：发表／更新：未知；抓取核验：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/skills/openspec-ff-change/SKILL.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenSpec/skills/openspec-ff-change/SKILL.md>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: openspec-ff-change
description: Fast-forward through OpenSpec artifact creation. Use when the user wants to quickly create all artifacts needed for implementation without stepping through each one individually. Also use when the user says "openspec ff" or "opsx ff".
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

通过工件创建快进 - 一次性生成开始实施所需的一切。

**商店选择：** 如果用户命名商店（商店是在本机上注册的独立 OpenSpec 存储库）或工作位于其中，请运行 `openspec store list --json` 以发现注册的商店 ID，然后在读取或写入规格和更改的命令上传递 `--store <id>` (`new change`、`status`、`instructions`、`list`、`show`、`validate`、`archive`、`doctor`、`context`、 `schemas`、`view`）。选择后，将 `--store <id>` 视为工作流程其余部分的粘性。下面这些命令的每个未指定范围的示例都是简写形式：在运行之前，附加标志。例如，运行 `openspec status --change "<name>" --json --store "<id>"`，而不是下面显示的无作用域形式。其他命令不采用该标志。命令打印的提示已经带有该标志；继续跟进。如果没有存储，命令将作用于最近的本地 `openspec/` 根。

**项目检查：** 这些步骤需要已使用 OpenSpec 的项目。在写入任何内容（`new change`、`archive`、`sync specs`或创作工件文件）的第一步之前，请确认项目具有根：运行`openspec list --json`（选择商店时使用`--store <id>`，因为该商店是根）并读取`root`。根对象意味着项目已设置。 `"root": null` 意味着它不是 - 这里没有 `openspec/` 目录，并且诸如 `openspec new change` 之类的写入会创建一个作为副作用。该命令也以非零值退出，这是答案而不是损坏的 CLI，因此请阅读 JSON 而不是重试或解决它。

其中一个 `"root": null` 与设置无关：当 `status` 错误消息以 `Declared in` 或 `Invalid store declaration in` 开头并命名该项目的 `openspec/config.yaml`（或 `config.yml`）时，该项目确实通过存储使用 OpenSpec声明，本机无法解析（商店未注册，或 `store:` 行格式错误）。不要将其视为未初始化并跳过以下分支：在写入之前停止并向用户显示错误为 `message` 和 `fix`。

否则，在没有 root 的情况下，接下来发生的情况取决于如何实现此工作流程：

- **自动选择**：您自己选择此工作流程，无需用户命名OpenSpec、命名此技能或运行其斜线命令。停止使用 OpenSpec 并正常应答请求，就像未安装 OpenSpec 时一样。不要要求他们设置任何内容，也不要提及 OpenSpec 设置。
- **显式OpenSpec请求**：名为OpenSpec的用户，命名了该技能，或运行了其斜线命令。在写作之前停下来，询问如何继续：设置此项目 (`openspec init`)，定位他们已有的商店 (`--store <id>`)，或者在没有 OpenSpec 的情况下继续此请求。等待他们的答复。

在这两个分支中，永远不要创建根作为副作用：在用户要求之前不要运行 `openspec init`，不要手动创建 `openspec/` 文件，并且不要让命令创建它。

**输入**：用户的请求应包含更改名称（短横线大小写）OR他们想要构建的内容的描述。

**步骤**

1. **如果没有提供明确的输入，询问他们想要构建什么**

询问用户（开放式，无预设选项）：
   > “您想要进行哪些更改？描述您想要构建或修复什么。”

从他们的描述中，得出一个短横线命名（例如，“添加用户身份验证”→ `add-user-auth`）。

**IMPORTANT**：NOT在不了解用户想要构建什么的情况下继续进行。

2. **创建更改目录**
   ```bash
   openspec new change "<name>"
   ```
这在规划住宅中产生了由CLI解决的脚手架变化。

3. **获取工件构建顺序**
   ```bash
   openspec status --change "<name>" --json
   ```
解析JSON得到：
   - `applyRequires`：实施前所需的工件 ID 数组（例如，`["tasks"]`）
   - `artifacts`：所有工件的列表，每个工件都有其 `status` 及其 `requires` 边缘（它直接依赖的工件 ID）
   - `planningHome`、`changeRoot`、`artifactPaths` 和 `actionContext`：路径和范围上下文。使用这些而不是假设存储库本地路径。

4. **创建所需集合中的每个工件**

使用待办事项列表来跟踪工件的进度。

按依赖关系顺序循环遍历工件（首先是没有挂起的依赖关系的工件）：

a. **对于每个 `ready` 工件（满足依赖关系）**：
      - 获取说明：
        ```bash
        openspec instructions <artifact-id> --change "<name>" --json
        ```
      - 指令JSON包括：
        - `context`：项目背景（对您的限制 - 执行 NOT 包含在输出中）
        - `rules`：特定于工件的规则（对您的约束 - 执行 NOT 包含在输出中）
        - `template`：输出文件使用的结构
        - `instruction`：此工件类型的特定于架构的指南
        - `skipped`/`warning`：当更改声明 skip_specs 并且必须创建此工件 NOT 时出现 - 停止并选择另一个工件
        - `resolvedOutputPath`：写入工件的解析路径或模式
        - `dependencies`：已完成的工件以供阅读上下文
      - 读取任何已完成的依赖文件以获取上下文 - 始终从磁盘重新读取它们，即使您在对话的早期看到它们（用户可能已经编辑过它们）
      - **起草前检查相关项目**：先阅读`context`和`rules`，然后检查`openspec/`之外的相关实现、附近测试、配置和文档。保持检查只读并与变化成比例；将结果重复用于以后的工件，并仅根据需要进行更多检查。
        - 从请求和项目背景中识别目标项目；规划住宅可能与代码分开。如果目标不明确，请询问。对于绿地或非代码更改，请检查可用的结构和相关文档。如果来源不可用，请说明限制并询问它何时对计划产生重大影响。
        - 根据您发现的内容确定范围、方法和任务。将观察到的行为与假设和建议的添加区分开来；表面上与现有规范相冲突，而不是默默地决定哪个是正确的。
        - 现在就进行此发现，而不是将通用的“探索代码库”或“制定计划”任务留给实施。针对未解决的问题进行任何必要的后续调查。
      - 如果 `instruction` 字段将创建委托给特定技能或命令，则调用它来生成工件而不是自己编写文件，然后验证工件文件是否存在于 `resolvedOutputPath`
      - 否则，使用 `template` 作为结构创建工件文件并将其写入 `resolvedOutputPath`。如果`resolvedOutputPath`是glob，则按照`instruction`选择具体文件路径
      - 应用 `context` 和 `rules` 作为约束 - 但将 NOT 将它们复制到文件中
      - 显示简要进度：“✓ 创建 <artifact-id>”

b. **Continue 直到所需集合中的每个工件都存在（不仅仅是 `apply.requires`）**
      - 创建每个工件后，重新运行 `openspec status --change "<name>" --json`
      - 所需的集合是 `applyRequires` 加上通过遵循 `status --json` 中的 `requires` 边缘可到达的每个工件 - 传递它们（规范驱动关闭提案、规范、设计、任务）。将文物留在单独设置的外面
      - `status` 仅是文件存在，因此读取 `done` 的 `applyRequires` 工件确实 NOT 意味着其依赖项存在 - 写入 `tasks.md` 早期标记 `tasks` 已完成，而 `specs` 从未写入。使用每个工件的 `requires` 边缘，而不是其 `status` 来构建所需的集合：`done` 工件仍然列出它所依赖的内容
      - 已读取 `status: "skipped"` 的工件已满足：更改在 `.openspec.yaml` 中声明 `skip_specs`，因此其文件必须 NOT 存在。永远不要尝试创建一个
      - 创建所需集合中缺少的每个工件，然后重新检查 - 创建一个可以解锁其他工件
      - 仅当 `status` 已报告它 `skipped` 或当其自己的 `instruction` 表示它是有条件时才跳过一个：运行 `openspec instructions <artifact-id> --change "<name>" --json` 并仅在其 `instruction` 字段将其标记为可选时跳过（例如“仅在...时创建”）。规范驱动的 `design.md` 符合资格； `specs` 仅通过上述 `skipped` 状态才符合资格，而不是由您自己判断。告诉用户，不要重新考虑
      - 依赖项是推动者，而不是门：如果仅因为您跳过了条件依赖项而所需的工件仍然是 `blocked`，则无论如何都要写它
      - 当所需集中的每个工件为 `done`、`skipped` 或被故意跳过时停止

c. **如果工件需要用户输入**（上下文非常不清楚）：
      - 请用户澄清
      - 然后继续创作

5. **显示最终状态**
   ```bash
   openspec status --change "<name>"
   ```

**输出**

完成所有工件后，总结一下：
- 更改名称和位置
- 创建的工件列表，包含简要说明，以及您跳过的任何条件工件及其原因
- 已准备就绪：“实施所需的所有工件均已准备就绪。”
- 提示：“运行 `/openspec-apply-change` 或要求我实施以开始处理任务。”

**神器创建指南**

- 对于每种工件类型，请遵循 `openspec instructions` 中的 `instruction` 字段 - 这是权威指南，即使对于熟悉的工件名称也是如此
- 如果 `instruction` 字段指示您使用特定技能或命令来创建工件，请调用它而不是直接写入工件
- 该模式定义了每个工件应包含的内容 - 遵循它
- 在创建新项目之前，请阅读依赖项的上下文
- 使用 `template` 作为输出文件的结构 - 填写其部分
- **IMPORTANT**：`context`和`rules`是YOU的约束，而不是文件的内容
  - 是否将 NOT 将 `<context>`、`<rules>`、`<project_context>` 块复制到工件中
  - 这些指导您编写的内容，但不应出现在输出中

**护栏**
- 创建应用阶段传递依赖的每个工件，而不仅仅是 `apply.requires` 中列出的 id
- 在创建新的依赖项之前始终读取依赖项 - 从磁盘重新读取，而不是从会话内存中读取（自上次查看文件以来，文件可能已更改）
- 如果上下文非常不清楚，请询问用户 - 但更愿意做出合理的决定以保持动力
- 如果具有该名称的更改已存在，建议继续该更改
- 写入后验证每个工件文件是否存在，然后再继续下一步


## 原文对照

<details>
<summary>展开完整原文</summary>

Fast-forward through artifact creation - generate everything needed to start implementation in one go.

**Store selection:** If the user names a store (a store is a standalone OpenSpec repo registered on this machine) or the work lives in one, run `openspec store list --json` to discover registered store ids, then pass `--store <id>` on the commands that read or write specs and changes (`new change`, `status`, `instructions`, `list`, `show`, `validate`, `archive`, `doctor`, `context`, `schemas`, `view`). Once selected, treat `--store <id>` as sticky for the rest of the workflow. Every unscoped example of those commands below is shorthand: before running it, append the flag. For example, run `openspec status --change "<name>" --json --store "<id>"`, not the unscoped form shown below. Other commands do not take the flag. Hints printed by commands already carry the flag; keep it on follow-ups. Without a store, commands act on the nearest local `openspec/` root.

**Project check:** These steps expect a project that already uses OpenSpec. Before the first step that writes anything (`new change`, `archive`, `sync specs`, or authoring an artifact file), confirm the project has a root: run `openspec list --json` (with `--store <id>` when a store is selected, since the store is then the root) and read `root`. A root object means the project is set up. `"root": null` means it is not - there is no `openspec/` directory here, and a write such as `openspec new change` would create one as a side effect. The command also exits non-zero, which is that answer rather than a broken CLI, so read the JSON instead of retrying or working around it.

One `"root": null` is not about setup: when a `status` error message starts with `Declared in` or `Invalid store declaration in` and names this project's `openspec/config.yaml` (or `config.yml`), the project does use OpenSpec through a store it declares, which this machine cannot resolve (the store is not registered, or the `store:` line is malformed). Do not treat it as uninitialized and skip the branches below: stop before writing and show the user that error's `message` and `fix`.

Otherwise, with no root, what happens next depends on how this workflow was reached:

- **Auto-selected**: you chose this workflow yourself, without the user naming OpenSpec, naming this skill, or running its slash command. Stop using OpenSpec and answer the request normally, as you would with no OpenSpec installed. Do not ask them to set anything up and do not mention OpenSpec setup.
- **Explicit OpenSpec request**: the user named OpenSpec, named this skill, or ran its slash command. Stop before writing and ask how to proceed: set this project up (`openspec init`), target a store they already have (`--store <id>`), or continue without OpenSpec for this request. Wait for their answer.

In both branches, never create the root as a side effect: do not run `openspec init` until the user asks for it, do not hand-create `openspec/` files, and do not let a command create it.

**Input**: The user's request should include a change name (kebab-case) OR a description of what they want to build.

**Steps**

1. **If no clear input provided, ask what they want to build**

   Ask the user (open-ended, no preset options):
   > "What change do you want to work on? Describe what you want to build or fix."

   From their description, derive a kebab-case name (e.g., "add user authentication" → `add-user-auth`).

   **IMPORTANT**: Do NOT proceed without understanding what the user wants to build.

2. **Create the change directory**
   ```bash
   openspec new change "<name>"
   ```
   This creates a scaffolded change in the planning home resolved by the CLI.

3. **Get the artifact build order**
   ```bash
   openspec status --change "<name>" --json
   ```
   Parse the JSON to get:
   - `applyRequires`: array of artifact IDs needed before implementation (e.g., `["tasks"]`)
   - `artifacts`: list of all artifacts, each with its `status` and its `requires` edges (the artifact IDs it directly depends on)
   - `planningHome`, `changeRoot`, `artifactPaths`, and `actionContext`: path and scope context. Use these instead of assuming repo-local paths.

4. **Create every artifact in the required set**

   Use a todo list to track progress through the artifacts.

   Loop through artifacts in dependency order (artifacts with no pending dependencies first):

   a. **For each artifact that is `ready` (dependencies satisfied)**:
      - Get instructions:
        ```bash
        openspec instructions <artifact-id> --change "<name>" --json
        ```
      - The instructions JSON includes:
        - `context`: Project background (constraints for you - do NOT include in output)
        - `rules`: Artifact-specific rules (constraints for you - do NOT include in output)
        - `template`: The structure to use for your output file
        - `instruction`: Schema-specific guidance for this artifact type
        - `skipped`/`warning`: present when the change declares skip_specs and this artifact must NOT be created - stop and pick another artifact
        - `resolvedOutputPath`: Resolved path or pattern to write the artifact
        - `dependencies`: Completed artifacts to read for context
      - Read any completed dependency files for context - always re-read them from disk, even if you saw them earlier in the conversation (the user may have edited them)
      - **Inspect the relevant project before drafting**: Read `context` and `rules` first, then inspect relevant implementation, nearby tests, configuration, and documentation outside `openspec/`. Keep inspection read-only and proportional to the change; reuse findings for later artifacts and inspect more only as needed.
        - Identify the target project from the request and project context; the planning home may be separate from the code. If the target is unclear, ask. For greenfield or non-code changes, inspect the available structure and relevant documents. If source is unavailable, state the limitation and ask when it materially affects the plan.
        - Ground scope, approach, and tasks in what you find. Distinguish observed behavior from assumptions and proposed additions; surface conflicts with existing specs instead of silently deciding which is correct.
        - Do this discovery now, rather than leaving generic "explore the codebase" or "make a plan" tasks for implementation. Keep any necessary follow-up investigation specific to an unresolved question.
      - If the `instruction` field delegates creation to a specific skill or command, invoke it to produce the artifact instead of writing the file yourself, then verify the artifact file exists at `resolvedOutputPath`
      - Otherwise create the artifact file using `template` as the structure and write it to `resolvedOutputPath`. If `resolvedOutputPath` is a glob, follow `instruction` to choose the concrete file path
      - Apply `context` and `rules` as constraints - but do NOT copy them into the file
      - Show brief progress: "✓ Created <artifact-id>"

   b. **Continue until every artifact in the required set exists (not just `apply.requires`)**
      - After creating each artifact, re-run `openspec status --change "<name>" --json`
      - The required set is `applyRequires` plus every artifact reachable from those by following the `requires` edges in `status --json` - walk them transitively (spec-driven closes over proposal, specs, design, tasks). Leave artifacts outside that set alone
      - `status` is file-existence only, so an `applyRequires` artifact reading `done` does NOT mean its dependencies exist - writing `tasks.md` early marks `tasks` done while `specs` was never written. Use each artifact's `requires` edges, not its `status`, to build the required set: a `done` artifact still lists what it depends on
      - An artifact already reading `status: "skipped"` is satisfied: the change declares `skip_specs` in `.openspec.yaml`, so its files must NOT exist. Never try to create one
      - Create every artifact in the required set that is missing, then re-check - creating one can unblock others
      - Skip one only when `status` already reports it `skipped`, or when its own `instruction` says it is conditional: run `openspec instructions <artifact-id> --change "<name>" --json` and skip only if its `instruction` field marks it optional (e.g. "create only if..."). Spec-driven's `design.md` qualifies; `specs` qualifies only via the `skipped` status above, never by your own judgment. Tell the user, and do not reconsider it
      - Dependencies are enablers, not gates: if a required artifact is still `blocked` only because you skipped a conditional dependency, write it anyway
      - Stop when every artifact in the required set is `done`, `skipped`, or was deliberately skipped

   c. **If an artifact requires user input** (critically unclear context):
      - Ask the user to clarify
      - Then continue with creation

5. **Show final status**
   ```bash
   openspec status --change "<name>"
   ```

**Output**

After completing all artifacts, summarize:
- Change name and location
- List of artifacts created with brief descriptions, plus any conditional artifact you skipped and why
- What's ready: "All artifacts needed for implementation are ready."
- Prompt: "Run `/openspec-apply-change` or ask me to implement to start working on the tasks."

**Artifact Creation Guidelines**

- Follow the `instruction` field from `openspec instructions` for each artifact type - it is the authoritative guidance, even for familiar artifact names
- If the `instruction` field directs you to use a specific skill or command to create the artifact, invoke it instead of writing the artifact directly
- The schema defines what each artifact should contain - follow it
- Read dependency artifacts for context before creating new ones
- Use `template` as the structure for your output file - fill in its sections
- **IMPORTANT**: `context` and `rules` are constraints for YOU, not content for the file
  - Do NOT copy `<context>`, `<rules>`, `<project_context>` blocks into the artifact
  - These guide what you write, but should never appear in the output

**Guardrails**
- Create every artifact the apply phase transitively depends on, not just the ids listed in `apply.requires`
- Always read dependency artifacts before creating a new one - re-read from disk, not from conversation memory (files may have changed since you last saw them)
- If context is critically unclear, ask the user - but prefer making reasonable decisions to keep momentum
- If a change with that name already exists, suggest continuing that change instead
- Verify each artifact file exists after writing before proceeding to next


</details>
