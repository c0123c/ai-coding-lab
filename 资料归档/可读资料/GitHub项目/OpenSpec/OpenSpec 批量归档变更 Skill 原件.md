# OpenSpec 批量归档变更 Skill 原件

定义多变更选择、批量状态验证、规格冲突检测与处理、确认批量操作及逐项归档的代理指令。

- 来源：OpenSpec
- 资料类型：SKILL.md：批量归档代理指令原件
- 存档版本：`9111a7654d78`。
- 文档时间：发表／更新：未知；抓取核验：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/skills/openspec-bulk-archive-change/SKILL.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenSpec/skills/openspec-bulk-archive-change/SKILL.md>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: openspec-bulk-archive-change
description: Archive multiple completed OpenSpec changes at once. Use when archiving several parallel changes. Also use for a plural archive request - "openspec bulk-archive", "opsx bulk-archive", "openspec archive all", or "openspec archive these changes".
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

在单个操作中归档多个已完成的更改。

这项技能允许您批量归档更改，通过检查代码库以确定实际实现的内容来智能地处理规范冲突。

**商店选择：** 如果用户命名商店（商店是在本机上注册的独立 OpenSpec 存储库）或工作位于其中，请运行 `openspec store list --json` 以发现注册的商店 ID，然后在读取或写入规格和更改的命令上传递 `--store <id>` (`new change`、`status`、`instructions`、`list`、`show`、`validate`、`archive`、`doctor`、`context`、 `schemas`、`view`）。选择后，将 `--store <id>` 视为工作流程其余部分的粘性。下面这些命令的每个未指定范围的示例都是简写形式：在运行之前，附加标志。例如，运行 `openspec status --change "<name>" --json --store "<id>"`，而不是下面显示的无作用域形式。其他命令不采用该标志。命令打印的提示已经带有该标志；继续跟进。如果没有存储，命令将作用于最近的本地 `openspec/` 根。

**项目检查：** 这些步骤需要已使用 OpenSpec 的项目。在写入任何内容（`new change`、`archive`、`sync specs`或创作工件文件）的第一步之前，请确认项目具有根：运行`openspec list --json`（选择商店时使用`--store <id>`，因为该商店是根）并读取`root`。根对象意味着项目已设置。 `"root": null` 意味着它不是 - 这里没有 `openspec/` 目录，并且诸如 `openspec new change` 之类的写入会创建一个作为副作用。该命令也以非零值退出，这是答案而不是损坏的 CLI，因此请阅读 JSON 而不是重试或解决它。

其中一个 `"root": null` 与设置无关：当 `status` 错误消息以 `Declared in` 或 `Invalid store declaration in` 开头并命名该项目的 `openspec/config.yaml`（或 `config.yml`）时，该项目确实通过存储使用 OpenSpec声明，本机无法解析（商店未注册，或 `store:` 行格式错误）。不要将其视为未初始化并跳过以下分支：在写入之前停止并向用户显示错误为 `message` 和 `fix`。

否则，在没有 root 的情况下，接下来发生的情况取决于如何实现此工作流程：

- **自动选择**：您自己选择此工作流程，无需用户命名OpenSpec、命名此技能或运行其斜线命令。停止使用 OpenSpec 并正常应答请求，就像未安装 OpenSpec 时一样。不要要求他们设置任何内容，也不要提及 OpenSpec 设置。
- **显式OpenSpec请求**：名为OpenSpec的用户，命名了该技能，或运行了其斜线命令。在写作之前停下来，询问如何继续：设置此项目 (`openspec init`)，定位他们已有的商店 (`--store <id>`)，或者在没有 OpenSpec 的情况下继续此请求。等待他们的答复。

在这两个分支中，永远不要创建根作为副作用：在用户要求之前不要运行 `openspec init`，不要手动创建 `openspec/` 文件，并且不要让命令创建它。

`<capability-path>` 是相对于 `specs/` 的规范目录（例如，`user-auth` 或 `identity/user-auth`）。在解析其主要规范时，保留每个增量规范的完整路径。

**输入**：不需要（提示选择）

**步骤**

1. **获取活动更改**

运行 `openspec list --json` 以获取所有活动更改。

如果不存在主动更改，则通知用户并停止。

2. **提示更改选择**

要求用户选择更改（多选）：
   - 显示列表输出中的每个更改名称和任务状态
   - 包括“所有更改”选项
   - 允许任意数量的选择（1+ 有效，2+ 是典型用例）

**IMPORTANT**：自动选择NOT。始终让用户选择。

**在批量验证之前为选定的根加载当前存档输入一次：**

从此根中选择一项选定的更改，并使用 
 相同的选定根标志运行 
 `openspec instructions archive --change "<selected-change>" --json`。此查找是建议性和可选的：它仅提供
额外的提示输入，因此它绝不能阻止批处理。如果失败或返回
无效JSON - 例如在不支持此命令
的旧版CLI上 - 在没有上下文和操作指导的情况下继续批处理。不要
报告错误并且不要停止。

有效的响应可以省略 `context` 和 `operationGuidance`。将 
 `context` 视为整个批次中必需的提示级输入：阅读并考虑 
 它，并应用相关的项目事实、约定和约束。将 
 `operationGuidance` 视为可选的附加建议：阅读并考虑每个 
 条目，并遵循适用且与内置 
 批处理工作流程兼容的条目。

将这两个字段与冲突分析、显式用户选择、
 解析路径、CLI 检查和命令契约分开。如果上下文与控制输入中的一个 
 冲突，请报告冲突并保留控制 
 值。如果指导不适用或与控制输入冲突，请勿
遵循它并解释原因。不要从任一字段推断跳过的提示、替换路径或 
 标志，也不要将其文本逐字复制到规范、更改、
 或摘要中。这些是提示级行为契约，而不是可执行的检查。

3. **批量验证 - 收集所有选定更改的状态**

使用任务 
 进度的相同选定根标志运行 `openspec list --json` 一次。如果查找失败，返回无效的 JSON，或忽略任何选定的 
 更改，包含重复的选定更改，或返回无效计数，
 会报告问题并在同步或存档批次之前停止。

对于每个选定的更改，收集：

a. **工件状态** - 运行`openspec status --change "<name>" --json`
      - 解析 `schemaName`、`artifacts`、`planningHome`、`changeRoot`、`artifactPaths` 和 `actionContext`
      - 注意哪些工件是 `done` 与其他状态

b. **任务完成** - 从列表响应中查找 `name` 与此更改完全匹配的 `changes` 条目
      - 需要非负整数 `totalTasks` 和 `completedTasks`，其中 `completedTasks <= totalTasks`
      - 未完成的任务 = `totalTasks - completedTasks`
      - CLI 解析架构的跟踪任务文件，包括自定义工件名称、输出路径和全局变量
      - 不要根据工件状态、工件 ID `tasks` 或缺少顶级 `tasks.md` 来推断任务完成情况
      - CLI 仅将 `x`/`X` 复选框标记计为完整；其他标记仍然不完整
      - 如果 `totalTasks` 为零，请注明“无任务”

c. **Delta 规格** - 从状态 JSON 检查 `artifactPaths.specs.existingOutputPaths`
      - 列出现有的功能规范
      - 对于每个，提取需求名称（匹配 `### Requirement: <name>` 的行）
      - 将此列表视为唯一的增量规格来源。如果 `specs` 条目是
丢失或列表为空，不对该更改执行规范同步或规范指令
查找；不要从不相关的工件推断增量。
      - 针对每个更改（包括混合模式）独立评估这一点
某些模式没有 `specs` 工件的批次。

d. **存档目标** - 计算每个更改的目标名称一次并将其记录为该更改的 `<target-name>`
      - 当更改名称已以 `YYYY-MM-DD-` 前缀开头时，按原样使用更改名称；否则将当前日期添加为 `YYYY-MM-DD-<name>`（与 `openspec archive` 相同的规则）
      - 检查`<planningHome.changesDir>/archive/<target-name>`是否已存在
      - 如果存在，或者另一个选定的更改解析为相同的目标名称，请使用 `Archive directory already exists` 标记每个此类更改 `Blocked`
      - 被阻止的更改永远不会同步或移动：在步骤 6 表中将其显示为 `Blocked`，将其排除在冲突解决之外（仅使用其他更改解决其冲突），并在步骤 8d 中将其记录为失败
      - 在写入任何主要规范之前检查此处，匹配 `openspec archive`：同步后发现的冲突将使主要规范重写为从未发生过的存档

4. **检测规格冲突**

构建一个由 `<capability-path>` 键控的地图，相对于 `specs/` 的确切路径：

   ```text
   identity/user-auth -> [change-a, change-b]  <- CONFLICT (2+ changes)
   billing/user-auth  -> [change-c]            <- OK (different full path)
   ```

当 2+ 选定的更改具有完全相同的 `<capability-path>` 的增量规格时，存在冲突。

5. **代理解决冲突**

**对于每个冲突**，调查代码库：

a. **阅读每个冲突更改的增量规格**，以了解每个更改的声明内容add/modify

b. **搜索代码库**以获取实现证据：
      - 从每个增量规范中查找实现要求的代码
      - 检查相关文件、函数或测试

c. **确定分辨率**：
      - 如果实际只实施一项更改 -> 同步该规范
      - 如果两者都实现 -> 按时间顺序应用（较旧的优先，较新的覆盖）
      - 如果两者均未实现 -> 跳过规范同步，警告用户

d. **每个冲突的记录分辨率**：
      - 每个增量规格的包含或排除决定，以更改和 `<capability-path>` 为关键
      - 其中包括要应用的增量规格以及应用顺序
      - 由于缺少实现，需要从同步中排除哪些增量规范
      - 基本原理（在代码库中找到的内容）

6. **显示综合状态表**

显示总结所有更改的表格：

   ```markdown
   | Change              | Artifacts | Tasks | Specs   | Conflicts | Status |
   |---------------------|-----------|-------|---------|-----------|--------|
   | schema-management   | Done      | 5/5   | 2 delta | None      | Ready  |
   | project-config      | Done      | 3/3   | 1 delta | None      | Ready  |
   | add-oauth           | Done      | 4/4   | 1 delta | identity/user-auth (!) | Ready* |
   | add-verify-skill    | 1 left    | 2/5   | None    | None      | Warn   |
   ```

对于冲突，请显示解决方案：
   ```text
   * Conflict resolution:
     - identity/user-auth spec: Will apply add-oauth then add-jwt (both implemented, chronological order)
   ```

对于不完整的更改，显示警告：
   ```text
   Warnings:
   - add-verify-skill: 1 incomplete artifact, 3 incomplete tasks
   ```

7. **确认批量操作**

向用户询问一个确认问题：

   - “存档 N 次更改？”具有基于状态的选项
   - 选项可能包括：
     - “存档所有 N 个更改”
     - “仅存档 N 个准备好的更改（跳过不完整）”
     - “取消”

如果有不完整的更改，请明确说明它们将被存档并带有警告。

按意图路由答案，而不是按确切的标签 - 您编写了这些标签，
 因此匹配用户选择的内容而不是上面的措辞：
   - “取消”——停止，不归档。报告没有存档任何内容并跳过其余步骤。
   - 存档所有内容选项 — 继续执行除 `Blocked` 之外的每个选定更改
   - 只读选项 — 仅继续步骤 6 表标记为 `Ready` 或 `Ready*` 的更改，并将其余部分记录为步骤 8d 中的已跳过，但 `Blocked` 更改除外，这些更改将保持为“失败”，并显示 `Archive directory already exists`。如果跳过 `Ready*` 更改的冲突伙伴，则仅使用正在存档的更改重新导出该冲突的解决方案。
   - 还有什么问题——再问一次而不是存档

在步骤 8 写入第一个主要规格或移动任何更改之前，请获取已确认批次的每个 
 所需的规格规则快照。对于将
同步具体`artifactPaths.specs.existingOutputPaths`的每个更改，使用
相同的选定根标志运行
 `openspec instructions specs --change "<name>" --json`一次。在第一次写入或移动之前获取所有快照。
如果任何查找退出非零或返回无效的工件指令JSON，
识别受影响的更改，报告错误，并在
任何主规范写入或更改移动之前停止整个批次。不要将查找失败视为省略
规则。没有 `rules` 的有效响应是无规则情况。

8. **对每个确认的更改执行存档**

在处理之前，将步骤 5 中记录的决策（在任何步骤 7 重新推导之后）携带到两个 per-delta 集合中：
   - `includedDeltas`：来自已确认更改的所有非冲突增量规范以及选择同步的冲突增量
   - `excludedDeltas`：已排除已确认更改的冲突增量，因为缺少实现
   - 单个更改可以同时包含和排除增量规格。保留每个增量的决定；不要将其折叠为每次更改同步标志。

按确定的顺序处理更改（尊重冲突解决）：

a. **同步包含增量规格**：
      - 仅针对 `includedDeltas` 中条目的更改运行内联 `openspec-sync-specs` 工作流程（agent 驱动的智能合并），仅传递包含的增量路径并明确指示它忽略该更改的 `excludedDeltas`。等待它完成。
      - 如果同步报告任何停止或阻塞情况，则将同步视为失败。立即停止处理该更改。在继续下一个更改之前，请在批处理结果中将此更改的结果记录为“失败”，包括同步 blocking/error 条件。
      - 不进行同步后内容比较，不移动其`changeRoot`；保持更改不变。
      - 对于冲突，请按已解决的顺序申请。
      - 将更改获取的规范规则快照传递到内联同步中；内联
同步必须重用它而无需再次获取指令
      - 仅将工件规则应用于该更改产生的主要规格。他们确实
不更改冲突解决、存档行为或 CLI 合约，并且 
 它们的文本不会复制到输出文件中
      - 不要委托给后台任务 - 步骤 8c 会将 `changeRoot` 从仍在读取的同步下移出。
      - 如果更改不包含增量规格，则不要为其运行同步工作流程。

b. **移动前验证包含的增量规格changeRoot**：
      - 仅针对 `includedDeltas` 中的增量规格与 `<planningHome.root>/openspec/specs/<capability-path>/spec.md` 中的主要规格重新运行比较（使用步骤 3 状态 JSON 中的存储感知 `planningHome.root`，而不是硬编码的存储库路径）。
      - 验证主要规格是否已更新：
        - ADDED 要求存在
        - MODIFIED 需求包含增量中指定的场景和描述更改，其他场景完好无损
        - REMOVED 要求消失了——并且此同步取消了一项功能（删除了最后一个要求，将 `## Requirements` 留空），其主要规范被删除而不是留空。
        - RENAMED 要求在新名称下存在，在旧名称下不存在
      - 不验证`excludedDeltas`中的增量规格；它们故意保持不同步。
      - 如果同步失败或任何功能与验证不匹配，请报告差异并 fail/skip 移动该更改的 `changeRoot` — 不存档该更改。 `changeRoot` 保持不变。

c. **执行存档**：

目标名称：使用步骤3d中为此更改记录的`<target-name>`，保持不变。切勿在此处重新计算：午夜过后运行的批处理将检查步骤 3 中的一个日期并移至另一个日期。

**检查目标是否已存在：**
      - 即使已检查步骤 3，在移动前再次检查：目标可能出现在批次中
      - 如果是：将此更改记录为失败，并显示 `Archive directory already exists`，将 `changeRoot` 保留在原处，报告已为其同步的所有主要规格步骤 8a，然后继续进行其余更改
      - 如果没有：将`changeRoot`移动到存档目录

      ```bash
      mkdir -p "<planningHome.changesDir>/archive"
      mv "<changeRoot>" "<planningHome.changesDir>/archive/<target-name>"
      ```

**确认移动没有嵌套：** `mv`即使在检查后出现目标时也会退出0，将变化*移动到*内。如果 `<planningHome.changesDir>/archive/<target-name>/<change-directory-name>` 现在存在（`changeRoot` 的最后一个路径段），则将该目录移回 `changeRoot` 并将此更改记录为“失败，并显示 `Archive directory already exists`”。切勿将其报告为已存档。

d. **跟踪每次更改的结果**：
      - 成功：归档成功
      - 失败：存档或规格验证期间出错（记录错误）
      - 已跳过：用户选择不存档（如果适用）
      - 同步跳过：对于 `excludedDeltas` 中的每个增量，报告 `sync skipped` 以及更改、`<capability-path>` 和记录的原因。这与跳过存档不同。

9. **显示摘要**

显示最终结果：

   ```markdown
   ## Bulk Archive Complete

   Archived 3 changes:
   - schema-management-cli -> archive/2026-01-19-schema-management-cli/
   - project-config -> archive/2026-01-19-project-config/
   - add-oauth -> archive/2026-01-19-add-oauth/

   Skipped 1 change:
   - add-verify-skill (user chose not to archive incomplete)

   Spec sync summary:
   - 4 delta specs synced to main specs
   - 1 delta spec sync skipped (add-jwt, identity/user-auth: implementation not found)
   - 1 conflict resolved (identity/user-auth: synced add-oauth, skipped add-jwt)
   ```

如果有任何失败：
   ```text
   Failed 1 change:
   - some-change: Archive directory already exists
   ```

**冲突解决示例**

示例1：仅实现了一个
```text
Conflict: <planningHome.root>/openspec/specs/auth/spec.md touched by [add-oauth, add-jwt]

Checking add-oauth:
- Delta adds "OAuth Provider Integration" requirement
- Searching codebase... found src/auth/oauth.ts implementing OAuth flow

Checking add-jwt:
- Delta adds "JWT Token Handling" requirement
- Searching codebase... no JWT implementation found

Resolution: Only add-oauth is implemented. Will sync add-oauth specs only.
```

示例2：两者均已实现
```text
Conflict: <planningHome.root>/openspec/specs/api/spec.md touched by [add-rest-api, add-graphql]

Checking add-rest-api (created 2026-01-10):
- Delta adds "REST Endpoints" requirement
- Searching codebase... found src/api/rest.ts

Checking add-graphql (created 2026-01-15):
- Delta adds "GraphQL Schema" requirement
- Searching codebase... found src/api/graphql.ts

Resolution: Both implemented. Will apply add-rest-api specs first,
then add-graphql specs (chronological order, newer takes precedence).
```

**成功时输出**

```markdown
## Bulk Archive Complete

Archived N changes:
- <change-1> -> archive/<target-name-1>/
- <change-2> -> archive/<target-name-2>/

Spec sync summary:
- N delta specs synced to main specs
- No conflicts (or: M conflicts resolved)
```

**部分成功的输出**

```markdown
## Bulk Archive Complete (partial)

Archived N changes:
- <change-1> -> archive/<target-name-1>/

Skipped M changes:
- <change-2> (user chose not to archive incomplete)

Failed K changes:
- <change-3>: Archive directory already exists
```

**无变化时的输出**

```markdown
## No Changes to Archive

No active changes found. Create a new change to get started.
```

**护栏**
- 允许任意数量的更改（1+ 很好，2+ 是典型用例）
- 始终提示选择，从不自动选择
- 及早检测规范冲突并通过检查代码库解决
- 实施这两项更改后，按时间顺序应用规范
- 仅当缺少实现时才跳过规范同步（警告用户）
- 在确认之前显示清晰的每次更改状态
- 对整个批次使用单一确认
- 用户取消确认后切勿存档 - 取消的批次不会存档任何内容
- 跟踪并报告所有结果 (success/skip/fail)
- 移动到存档时保留 .openspec.yaml
- 存档目录目标使用当前日期，在步骤 3d 中计算一次并在移动时重用：YYYY-MM-DD-<name>；已以 `YYYY-MM-DD-` 前缀开头的名称按原样使用（切勿堆叠第二个日期）
- 如果存档目标存在，则该更改失败，但继续其他更改
- 在第一个主规范写入之前，检查步骤 3 中的每个存档目标；目标存在的更改永远不会同步或移动
- 如果请求同步，请针对包含增量规格的每个更改运行内联 `openspec-sync-specs` 工作流程（agent 驱动）
- 将每个增量 `includedDeltas` 和 `excludedDeltas` 决策付诸执行；仅同步和验证包含的增量
- 将每个排除的增量报告为 `sync skipped`，而不将存档本身视为已跳过
- 当规格同步仍在进行时，切勿归档更改 - 在移动 `changeRoot` 之前，运行内联同步并验证 `<planningHome.root>/openspec/specs/<capability-path>/spec.md` 处的主要规格
- 在规范检查或移动之前，为每个选定的根获取一次存档输入
- 在批次第一次主规范写入或移动之前获取所有必需的规范规则快照
- 失败的归档输入查找永远不会阻塞批处理；它在没有背景或指导的情况下进行
- 失败的规格指令查找自动停止整个批次
- 没有具体 `artifactPaths.specs.existingOutputPaths` 的更改在没有规范同步的情况下继续
- 在批处理中应用相关的运行时上下文并报告冲突
- 操作指引仍为建议性；考虑每一个条目并解释被拒绝的建议
- 将运行时输入、冲突分析、CLI-派生值和工件规则分开
- 工件规则仅约束书面规范
- 切勿将运行时输入或工件规则文本逐字复制到输出文件中


## 原文对照

<details>
<summary>展开完整原文</summary>

Archive multiple completed changes in a single operation.

This skill allows you to batch-archive changes, handling spec conflicts intelligently by checking the codebase to determine what's actually implemented.

**Store selection:** If the user names a store (a store is a standalone OpenSpec repo registered on this machine) or the work lives in one, run `openspec store list --json` to discover registered store ids, then pass `--store <id>` on the commands that read or write specs and changes (`new change`, `status`, `instructions`, `list`, `show`, `validate`, `archive`, `doctor`, `context`, `schemas`, `view`). Once selected, treat `--store <id>` as sticky for the rest of the workflow. Every unscoped example of those commands below is shorthand: before running it, append the flag. For example, run `openspec status --change "<name>" --json --store "<id>"`, not the unscoped form shown below. Other commands do not take the flag. Hints printed by commands already carry the flag; keep it on follow-ups. Without a store, commands act on the nearest local `openspec/` root.

**Project check:** These steps expect a project that already uses OpenSpec. Before the first step that writes anything (`new change`, `archive`, `sync specs`, or authoring an artifact file), confirm the project has a root: run `openspec list --json` (with `--store <id>` when a store is selected, since the store is then the root) and read `root`. A root object means the project is set up. `"root": null` means it is not - there is no `openspec/` directory here, and a write such as `openspec new change` would create one as a side effect. The command also exits non-zero, which is that answer rather than a broken CLI, so read the JSON instead of retrying or working around it.

One `"root": null` is not about setup: when a `status` error message starts with `Declared in` or `Invalid store declaration in` and names this project's `openspec/config.yaml` (or `config.yml`), the project does use OpenSpec through a store it declares, which this machine cannot resolve (the store is not registered, or the `store:` line is malformed). Do not treat it as uninitialized and skip the branches below: stop before writing and show the user that error's `message` and `fix`.

Otherwise, with no root, what happens next depends on how this workflow was reached:

- **Auto-selected**: you chose this workflow yourself, without the user naming OpenSpec, naming this skill, or running its slash command. Stop using OpenSpec and answer the request normally, as you would with no OpenSpec installed. Do not ask them to set anything up and do not mention OpenSpec setup.
- **Explicit OpenSpec request**: the user named OpenSpec, named this skill, or ran its slash command. Stop before writing and ask how to proceed: set this project up (`openspec init`), target a store they already have (`--store <id>`), or continue without OpenSpec for this request. Wait for their answer.

In both branches, never create the root as a side effect: do not run `openspec init` until the user asks for it, do not hand-create `openspec/` files, and do not let a command create it.

`<capability-path>` is the spec directory relative to `specs/` (for example, `user-auth` or `identity/user-auth`). Preserve the full path from each delta spec when resolving its main spec.

**Input**: None required (prompts for selection)

**Steps**

1. **Get active changes**

   Run `openspec list --json` to get all active changes.

   If no active changes exist, inform user and stop.

2. **Prompt for change selection**

   Ask the user to choose changes (multi-select):
   - Show each change name and task status from the list output
   - Include an option for "All changes"
   - Allow any number of selections (1+ works, 2+ is the typical use case)

   **IMPORTANT**: Do NOT auto-select. Always let the user choose.

   **Load current archive inputs once for the selected root before batch validation:**

   Choose one selected change from this root and run
   `openspec instructions archive --change "<selected-change>" --json` with the
   same selected-root flags. This lookup is advisory and optional: it only supplies
   extra prompt inputs, so it must never block the batch. If it fails or returns
   invalid JSON — for example on an older CLI that does not support this command
   yet — continue the batch with no context and no operation guidance. Do not
   report an error and do not stop.

   A valid response may omit `context` and `operationGuidance`. Treat
   `context` as a required prompt-level input across the batch: read and consider
   it, and apply relevant project facts, conventions, and constraints. Treat
   `operationGuidance` as optional additive advice: read and consider every
   entry, and follow entries that are applicable and compatible with the built-in
   batch workflow.

   Keep both fields separate from conflict analysis, explicit user choices,
   resolved paths, CLI checks, and command contracts. If context conflicts with one
   of those controlling inputs, report the conflict and preserve the controlling
   value. If guidance is inapplicable or conflicts with a controlling input, do not
   follow it and explain why. Do not infer skipped prompts, replacement paths, or
   flags from either field, and do not copy their text verbatim into specs, changes,
   or summaries. These are prompt-level behavior contracts, not enforceable checks.

3. **Batch validation - gather status for all selected changes**

   Run `openspec list --json` once with the same selected-root flags for task
   progress. If the lookup fails, returns invalid JSON, or omits any selected
   change, contains a duplicate selected change, or returns invalid counts,
   report the problem and stop before syncing or archiving the batch.

   For each selected change, collect:

   a. **Artifact status** - Run `openspec status --change "<name>" --json`
      - Parse `schemaName`, `artifacts`, `planningHome`, `changeRoot`, `artifactPaths`, and `actionContext`
      - Note which artifacts are `done` vs other states

   b. **Task completion** - Find the `changes` entry from the list response whose `name` exactly matches this change
      - Require nonnegative integer `totalTasks` and `completedTasks`, with `completedTasks <= totalTasks`
      - Incomplete tasks = `totalTasks - completedTasks`
      - The CLI resolves the schema's tracked task files, including custom artifact names, output paths, and globs
      - Do not infer task completion from artifact status, an artifact id of `tasks`, or the absence of a top-level `tasks.md`
      - The CLI counts only `x`/`X` checkbox markers as complete; other markers remain incomplete
      - If `totalTasks` is zero, note as "No tasks"

   c. **Delta specs** - Check `artifactPaths.specs.existingOutputPaths` from status JSON
      - List which capability specs exist
      - For each, extract requirement names (lines matching `### Requirement: <name>`)
      - Treat this list as the only delta-spec source. If the `specs` entry is
        missing or the list is empty, perform no spec sync or specs-instruction
        lookup for that change; do not infer deltas from unrelated artifacts.
      - Evaluate this independently for every change, including mixed-schema
        batches where some schemas have no `specs` artifact.

   d. **Archive target** - Compute each change's target name once and record it as that change's `<target-name>`
      - Use the change name as-is when it already starts with a `YYYY-MM-DD-` prefix; otherwise prepend the current date as `YYYY-MM-DD-<name>` (same rule as `openspec archive`)
      - Check whether `<planningHome.changesDir>/archive/<target-name>` already exists
      - If it exists, or another selected change resolves to the same target name, mark every such change `Blocked` with `Archive directory already exists`
      - A blocked change is never synced or moved: show it as `Blocked` in the step 6 table, leave it out of conflict resolution (resolve its conflicts using only the other changes), and record it as Failed in step 8d
      - Checking here, before any main spec is written, matches `openspec archive`: a collision found after sync would leave main specs rewritten for an archive that never happened

4. **Detect spec conflicts**

   Build a map keyed by `<capability-path>`, the exact path relative to `specs/`:

   ```text
   identity/user-auth -> [change-a, change-b]  <- CONFLICT (2+ changes)
   billing/user-auth  -> [change-c]            <- OK (different full path)
   ```

   A conflict exists when 2+ selected changes have delta specs for the exact same `<capability-path>`.

5. **Resolve conflicts agentically**

   **For each conflict**, investigate the codebase:

   a. **Read the delta specs** from each conflicting change to understand what each claims to add/modify

   b. **Search the codebase** for implementation evidence:
      - Look for code implementing requirements from each delta spec
      - Check for related files, functions, or tests

   c. **Determine resolution**:
      - If only one change is actually implemented -> sync that one's specs
      - If both implemented -> apply in chronological order (older first, newer overwrites)
      - If neither implemented -> skip spec sync, warn user

   d. **Record resolution** for each conflict:
      - An inclusion or exclusion decision for every delta spec, keyed by change and `<capability-path>`
      - Which included delta specs to apply and in what order
      - Which delta specs to exclude from sync because their implementation is missing
      - Rationale (what was found in codebase)

6. **Show consolidated status table**

   Display a table summarizing all changes:

   ```markdown
   | Change              | Artifacts | Tasks | Specs   | Conflicts | Status |
   |---------------------|-----------|-------|---------|-----------|--------|
   | schema-management   | Done      | 5/5   | 2 delta | None      | Ready  |
   | project-config      | Done      | 3/3   | 1 delta | None      | Ready  |
   | add-oauth           | Done      | 4/4   | 1 delta | identity/user-auth (!) | Ready* |
   | add-verify-skill    | 1 left    | 2/5   | None    | None      | Warn   |
   ```

   For conflicts, show the resolution:
   ```text
   * Conflict resolution:
     - identity/user-auth spec: Will apply add-oauth then add-jwt (both implemented, chronological order)
   ```

   For incomplete changes, show warnings:
   ```text
   Warnings:
   - add-verify-skill: 1 incomplete artifact, 3 incomplete tasks
   ```

7. **Confirm batch operation**

   Ask the user a single confirmation question:

   - "Archive N changes?" with options based on status
   - Options might include:
     - "Archive all N changes"
     - "Archive only N ready changes (skip incomplete)"
     - "Cancel"

   If there are incomplete changes, make clear they'll be archived with warnings.

   Route on the answer by intent, not by exact label — you wrote these labels,
   so match what the user picked rather than the wording above:
   - "Cancel" — stop, do not archive. Report that nothing was archived and skip the remaining steps.
   - The archive-everything option — proceed with every selected change that is not `Blocked`
   - The ready-only option — proceed with only the changes the step 6 table marks `Ready` or `Ready*`, and record the rest as Skipped in step 8d, except `Blocked` changes, which stay Failed with `Archive directory already exists`. If a `Ready*` change's conflict partner is skipped, re-derive that conflict's resolution using only the changes being archived.
   - Anything else — ask again rather than archiving

   Before step 8 writes the first main spec or moves any change, fetch every
   required specs-rule snapshot for the confirmed batch. For each change that will
   sync concrete `artifactPaths.specs.existingOutputPaths`, run
   `openspec instructions specs --change "<name>" --json` exactly once with the
   same selected-root flags. Obtain all snapshots before the first write or move.
   If any lookup exits non-zero or returns invalid artifact-instruction JSON,
   identify the affected change, report the error, and stop the whole batch before
   any main-spec write or change move. Do not treat lookup failure as omitted
   rules. A valid response without `rules` is the no-rules case.

8. **Execute archive for each confirmed change**

   Before processing, carry the recorded decisions from step 5 (after any step 7 re-derivation) into two per-delta sets:
   - `includedDeltas`: all non-conflicting delta specs from confirmed changes plus conflict deltas selected for sync
   - `excludedDeltas`: conflict deltas from confirmed changes excluded because their implementation is missing
   - A single change can have both included and excluded delta specs. Keep the decision per delta; do not collapse it into a per-change sync flag.

   Process changes in the determined order (respecting conflict resolution):

   a. **Sync included delta specs**:
      - Run the `openspec-sync-specs` workflow inline (agent-driven intelligent merge) only for changes with entries in `includedDeltas`, passing only the included delta paths and explicitly instructing it to ignore that change's `excludedDeltas`. Wait for it to finish.
      - If the sync reports any stop or blocking condition, treat the sync as failed. Stop processing that change immediately. Before continuing to the next change, record this change's outcome as Failed in the batch results, including the sync blocking/error condition.
      - Do not perform the post-sync content comparison and do not move its `changeRoot`; leave the change intact.
      - For conflicts, apply in resolved order.
      - Pass that change's fetched specs-rule snapshot into inline sync; inline
        sync must reuse it without fetching instructions again
      - Apply artifact rules only to main specs produced by that change. They do
        not change conflict resolution, archive behavior, or CLI contracts, and
        their text is not copied into an output file
      - Do not delegate to a background task — step 8c would move `changeRoot` out from under a sync that is still reading it.
      - If a change has no included delta specs, do not run the sync workflow for it.

   b. **Verify included delta specs before moving changeRoot**:
      - Re-run the comparison only for delta specs in `includedDeltas` against main spec at `<planningHome.root>/openspec/specs/<capability-path>/spec.md` (use the store-aware `planningHome.root` from step 3 status JSON, not a hardcoded repo path).
      - Verify that main specs are updated:
        - ADDED requirements present
        - MODIFIED requirements carrying scenario and description changes named in the delta, with their other scenarios intact
        - REMOVED requirements gone — and where this sync retired a capability (removed its last requirement, leaving `## Requirements` empty), its main spec deleted rather than left empty.
        - RENAMED requirements present under the new name and absent under the old one
      - Do not verify delta specs in `excludedDeltas`; they are intentionally left unsynced.
      - If sync failed or any capability does not match verification, report what differs and fail/skip moving that change's `changeRoot` — do not archive that change. `changeRoot` remains intact.

   c. **Perform the archive**:

      Target name: use the `<target-name>` recorded for this change in step 3d, unchanged. Never recompute it here: a batch that runs past midnight would check one date in step 3 and move to another.

      **Check if target already exists:**
      - Check again immediately before the move, even though step 3 already checked: the target can appear mid-batch
      - If yes: record this change as Failed with `Archive directory already exists`, leave `changeRoot` where it is, report any main specs step 8a already synced for it, and continue with the remaining changes
      - If no: move `changeRoot` to the archive directory

      ```bash
      mkdir -p "<planningHome.changesDir>/archive"
      mv "<changeRoot>" "<planningHome.changesDir>/archive/<target-name>"
      ```

      **Confirm the move did not nest:** `mv` exits 0 even when the target appeared after the check, moving the change *inside* it. If `<planningHome.changesDir>/archive/<target-name>/<change-directory-name>` now exists (the last path segment of `changeRoot`), move that directory back to `changeRoot` and record this change as Failed with `Archive directory already exists`. Never report it as archived.

   d. **Track outcome** for each change:
      - Success: archived successfully
      - Failed: error during archive or spec verification (record error)
      - Skipped: user chose not to archive (if applicable)
      - Sync skipped: for every delta in `excludedDeltas`, report `sync skipped` with the change, `<capability-path>`, and recorded reason. This is distinct from skipping the archive.

9. **Display summary**

   Show final results:

   ```markdown
   ## Bulk Archive Complete

   Archived 3 changes:
   - schema-management-cli -> archive/2026-01-19-schema-management-cli/
   - project-config -> archive/2026-01-19-project-config/
   - add-oauth -> archive/2026-01-19-add-oauth/

   Skipped 1 change:
   - add-verify-skill (user chose not to archive incomplete)

   Spec sync summary:
   - 4 delta specs synced to main specs
   - 1 delta spec sync skipped (add-jwt, identity/user-auth: implementation not found)
   - 1 conflict resolved (identity/user-auth: synced add-oauth, skipped add-jwt)
   ```

   If any failures:
   ```text
   Failed 1 change:
   - some-change: Archive directory already exists
   ```

**Conflict Resolution Examples**

Example 1: Only one implemented
```text
Conflict: <planningHome.root>/openspec/specs/auth/spec.md touched by [add-oauth, add-jwt]

Checking add-oauth:
- Delta adds "OAuth Provider Integration" requirement
- Searching codebase... found src/auth/oauth.ts implementing OAuth flow

Checking add-jwt:
- Delta adds "JWT Token Handling" requirement
- Searching codebase... no JWT implementation found

Resolution: Only add-oauth is implemented. Will sync add-oauth specs only.
```

Example 2: Both implemented
```text
Conflict: <planningHome.root>/openspec/specs/api/spec.md touched by [add-rest-api, add-graphql]

Checking add-rest-api (created 2026-01-10):
- Delta adds "REST Endpoints" requirement
- Searching codebase... found src/api/rest.ts

Checking add-graphql (created 2026-01-15):
- Delta adds "GraphQL Schema" requirement
- Searching codebase... found src/api/graphql.ts

Resolution: Both implemented. Will apply add-rest-api specs first,
then add-graphql specs (chronological order, newer takes precedence).
```

**Output On Success**

```markdown
## Bulk Archive Complete

Archived N changes:
- <change-1> -> archive/<target-name-1>/
- <change-2> -> archive/<target-name-2>/

Spec sync summary:
- N delta specs synced to main specs
- No conflicts (or: M conflicts resolved)
```

**Output On Partial Success**

```markdown
## Bulk Archive Complete (partial)

Archived N changes:
- <change-1> -> archive/<target-name-1>/

Skipped M changes:
- <change-2> (user chose not to archive incomplete)

Failed K changes:
- <change-3>: Archive directory already exists
```

**Output When No Changes**

```markdown
## No Changes to Archive

No active changes found. Create a new change to get started.
```

**Guardrails**
- Allow any number of changes (1+ is fine, 2+ is the typical use case)
- Always prompt for selection, never auto-select
- Detect spec conflicts early and resolve by checking codebase
- When both changes are implemented, apply specs in chronological order
- Skip spec sync only when implementation is missing (warn user)
- Show clear per-change status before confirming
- Use single confirmation for entire batch
- Never archive after the user cancels the confirmation — a cancelled batch archives nothing
- Track and report all outcomes (success/skip/fail)
- Preserve .openspec.yaml when moving to archive
- Archive directory target uses the current date, computed once in step 3d and reused at the move: YYYY-MM-DD-<name>; a name that already starts with a `YYYY-MM-DD-` prefix is used as-is (never stack a second date)
- If archive target exists, fail that change but continue with others
- Check every archive target in step 3, before the first main-spec write; a change whose target exists is never synced or moved
- If sync is requested, run the `openspec-sync-specs` workflow inline (agent-driven) for each change with included delta specs
- Carry the per-delta `includedDeltas` and `excludedDeltas` decisions into execution; sync and verify only included deltas
- Report every excluded delta as `sync skipped` without treating the archive itself as skipped
- Never archive a change while a spec sync is still in flight — run the sync inline and verify main specs at `<planningHome.root>/openspec/specs/<capability-path>/spec.md` before moving `changeRoot`
- Fetch archive inputs once per selected root before spec inspection or moves
- Fetch all required specs-rule snapshots before the batch's first main-spec write or move
- A failed archive-inputs lookup never blocks the batch; it proceeds with no context or guidance
- A failed specs instruction lookup stops the whole batch atomically
- Changes without concrete `artifactPaths.specs.existingOutputPaths` continue without spec sync
- Apply relevant runtime context across the batch and report conflicts
- Operation guidance remains advisory; consider every entry and explain rejected advice
- Keep runtime inputs, conflict analysis, CLI-derived values, and artifact rules separate
- Artifact rules constrain only written specs
- Never copy runtime input or artifact-rule text verbatim into output files


</details>
