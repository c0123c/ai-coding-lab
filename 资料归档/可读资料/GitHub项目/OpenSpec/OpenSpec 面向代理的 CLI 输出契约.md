# OpenSpec 面向代理的 CLI 输出契约

逐项说明 CLI 的 JSON 输出、诊断封装、根目录选择与命令数据结构，供编码代理按机器可读契约调用 OpenSpec。

- 来源：OpenSpec
- 资料类型：代理接口与机器可读输出规范
- 存档版本：`9111a7654d78`。
- 文档时间：发表／更新：未知；抓取核验：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/docs/agent-contract.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenSpec/docs/agent-contract.md>) · [返回资料总览](<../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# OpenSpec Agent 合约

`openspec` CLI 的机器可读表面，根据 `src/` 进行验证（顶点审核，2026-06-11）。下面的每个形状都是从发射代码中记录的。

## 1。一般约定

- **每次调用一个 JSON 文档。** 在 `--json` 模式下，stdout 恰好携带一个 JSON 文档（2-空格漂亮打印）。人类散文、旋转器和商店横幅都会进入 stderr。
- **商店横幅。** 在人类模式下，商店选择的根将 `Using OpenSpec root: <id> (<path>)` 打印到 stderr。从未以 JSON 模式打印。
- **按键外壳取决于表面**（参见已知的不一致）：store/doctor/context有效负载使用`snake_case`；工作流负载（`status`、`instructions`、`new change`、`validate`、`list`）使用 `camelCase`，但嵌入的 `root` 对象除外，该对象始终使用 `store_id`。
- **在大多数有效负载中（例如`root.store_id`、`member.path`），可选密钥被省略，不为空**。使用显式 `null` 的异常按形状调用（存储医生 `git.*`，故障有效负载）。

## 2。诊断信封

每个机器可读诊断共享一个包络形状 (`StoreDiagnostic`)：

```json
{
  "severity": "error" | "warning" | "info",
  "code": "snake_case_string",
  "message": "human sentence",
  "target": "dotted.surface (optional)",
  "fix": "one actionable sentence/command (optional)"
}
```

诊断显示在两个位置：**状态数组**（`status: StoreDiagnostic[]`位于顶层或每个条目）用于健康检查结果，以及**抛出错误**在命令失败时转换为单元素`status`数组。

## 3。根选择和`RootOutput`

所有根解析命令（`list`、`show`、`validate`、`status`、`instructions`、`instructions apply`、`instructions archive`、`new change`、 `archive`、`doctor`、`context`、`schemas`）以一优先级解析一个 OpenSpec 根：

1. `--store <id>` → 注册商店的根目录(`source: "store"`)。
2. 否则，具有 `openspec/` 的最近祖先：规划形状 → `source: "nearest"`（`store:` 指针将被忽略，并出现 stderr 警告）；具有有效 `store:` 指针的仅配置目录 → 该存储，`source: "declared"`。
3. 无最近根+全局`defaultStore`集合(`openspec config set defaultStore <id>`)→存储，`source: "global_default"`；过时的 ID 失败，并出现底层存储错误和 `fix` 命名 `openspec config unset defaultStore`。
4. 没有最近的根，不存在默认+注册存储→错误`no_root_with_registered_stores`。
5. 无 root、无默认值、无存储：命令可能将 cwd 视为 `source: "implicit"`； `doctor`、`context`、`list` 和批量 `validate` 会失败并显示 `no_openspec_root`。 `list` 保留了 `openspec/project.md` 遗留项目的隐式回退。

成功的JSON有效负载通常会嵌入根；成功的`schemas --json`
故意保留§4.13中记录的兼容性裸阵列：

```json
"root": { "path": "/abs/path", "source": "store" | "declared" | "global_default" | "nearest" | "implicit", "store_id": "id (only when store-selected)" }
```

**根故障合约**：在JSON模式下，解析失败在标准输出上打印`{ ...commandNullShape, "status": [diagnostic] }`并退出1。

## 4。命令 JSON 形状

### 4.1 `list --json`
`{ "changes": [ { "name", "completedTasks", "totalTasks", "lastModified", "status": "no-tasks"|"complete"|"in-progress", "nested"?: ["<area>/<name>", ...] } ], "warnings"?: [ { "code", "name", "nested", "message" } ], "root": RootOutput }` — 请注意，每次更改的 `status` 是一个字符串枚举。 `--specs`：`{ "specs": [ { "id", "requirementCount" } ], "root" }`。

`warnings`（空时省略）报告`changes/`下未更改的目录。今天唯一的代码是 `nested_change_directory`：一个包装更改目录的命名空间文件夹，OpenSpec 无法解决该问题，因为更改始终是直接位于 `changes/` 下的目录。同一条目在列出的更改上带有`nested`，其`status`则毫无意义。不要将此类条目视为更改；报告消息并保留目录。

### 4.2 `show <item> --json`
更改：`{ "id", "title", "deltaCount", "deltas": [...], "root" }`。规格：`{ "id", "title", "overview", "requirementCount", "requirements": [...], "metadata": { "version", "format", "sourcePath"? }, "root" }`。规范或变更增量的 `requirement`/`requirements` 中的要求是 `{ "name", "text", "scenarios": [ { "name", "rawText" } ] }`。要求 `name` 的标头没有 `Requirement:` 并且没有结束 `#` 运行，确切的名称存档与 MODIFIED/REMOVED/RENAMED 条目相匹配。场景 `name` 是其级别 4 标头，没有 `Scenario:` 且没有结束 `#` 运行，MODIFIED 场景丢失检查的名称进行比较。

### 4.3 `validate --json`
`{ "items": [ { "id", "type": "change"|"spec", "valid", "issues": [ { "level", "path", "message", "line"?, "column"? } ], "durationMs" } ], "summary": { "totals": {items,passed,failed}, "byType": {...} }, "version": "1.0", "root" }`。当任何项目失败时退出1。

### 4.4 `status --json`
`{ "changeName", "schemaName", "planningHome"?: { "kind", "root", "changesDir", "defaultSchema" }, "changeRoot", "artifactPaths": { "<id>": {outputPath, resolvedOutputPath, existingOutputPaths} }, "nextSteps": ["..."], "actionContext": { "mode": "repo-local", "sourceOfTruth": "repo", "planningArtifacts", "linkedContext", "allowedEditRoots", "requiresAffectedAreaSelection", "constraints" }, "isPlanningComplete", "isComplete", "applyRequires", "artifacts": [ {id, outputPath, status: "done"|"skipped"|"ready"|"blocked", requires, missingDeps?} ], "root" }`。对于存储选择的根，当当前路径上最近的项目是仅配置根且其 `store:` 指针命名该存储时，`allowedEditRoots` 为 `[<declaring project>, <store>]`，否则为 `[<store>]`（包括全局 `defaultStore`），并有一个约束告诉 agent 询问要编辑哪个存储库。 OpenSpec 不会将商店的任务路由到存储库，因此声明的项目是当前项目，而不是更改涉及的每个存储库。 `isPlanningComplete`表示每个不可跳过的规划工件都存在；跳过的工件算作已满足但未创建。这并不意味着实施任务已经完成。 `isComplete` 保留为具有相同值的兼容性别名。每个工件的 `requires` 是其直接依赖 ID（每个状态都存在，因此即使工件是 `done`，传递所需的集合也是可计算的）； `missingDeps`仅在`blocked`时出现。 `artifacts` 数组按依赖顺序排列，模式的 `artifacts:` 声明顺序打破了同时准备就绪的工件之间的联系（从不按字母顺序排列），因此第一个 `ready` 条目是接下来要写入的工件； `missingDeps` 使用相同的顺序。 `"skipped"` 标记 `generates` 路径在 `.openspec.yaml` 声明 `skip_specs: true` 的更改中的 `specs/` 下的工件；它满足依赖关系，但不得创建。无活动更改：`{ "changes": [], "message", "root" }`，退出 0。

`--all`（批处理，与 `--change` 互斥 - 组合它们是 `{ "changes": [], "root": null, "status": [d] }` 空形状的错误）：`{ "changes": [ <per-change status object, no per-change root>, ... ], "root" }`，按更改名称排序。无法加载的更改将 `{ "changeName", "status": [d] }` 贡献到位；扫描继续，保留完整的包络，并在文本和 JSON 模式下退出 1。即使不存在任何更改，无效的 `--schema` 也会导致整个调用失败并出现空形状。

### 4.5 `instructions <artifact> --json`
`{ "changeName", "artifactId", "schemaName", "changeDir", "planningHome"?, "outputPath", "resolvedOutputPath", "existingOutputPaths", "description", "instruction"?, "context"?, "rules"?, "references"?: ReferenceIndexEntry[], "skipped"?, "warning"?, "template", "dependencies": [{id,done,path,description,skipped?}], "unlocks", "root" }`。 `unlocks` 按照模式的声明顺序列出了此准备好的工件（与 `status` 推荐的顺序相同）。当更改声明 `skip_specs: true` 时，会出现 `"skipped": true`（带有 `"warning"`），并且会跳过此工件 — 不创建其文件。具有 `skipped: true` 的依赖项条目无需文件即可满足 - 不要尝试读取其路径。

`ReferenceIndexEntry`: `{ "store_id", "root"?, "specs"?: [{id,summary}], "fetch"?, "status": [] }` — 已解析条目携带 root/specs/fetch；未解决的携带store_id+警告状态。索引上限为 50KB (`reference_index_truncated`)。

### 4.6 `instructions apply --json`
`{ "changeName", "changeDir", "schemaName", "contextFiles": { "<artifactId>": ["/abs", ...] }, "progress": {total,complete,remaining}, "tasks": [{id,description,done}], "state": "blocked"|"all_done"|"ready", "missingArtifacts"?, "missingPrerequisites"?, "warnings"?, "instruction", "references"?, "context"?, "operationGuidance"?, "root" }`。 `missingArtifacts` 是应用块的内容（模式的 `apply.requires`）； `missingPrerequisites` 是在 apply 运行之前仍需要构建的所有内容，按照构建顺序 - 这些需求的传递闭包，因此它可能是更长的列表。 `warnings` 列出了变更本身的非阻塞问题 - 今天，准备在没有增量规范和没有 `skip_specs: true` 的情况下实施的变更，状态 `openspec validate` 拒绝。每次调用时都会从选定的根中读取两个可选根字段（`context`、`operationGuidance`）。 `context` 是必需的提示级输入，必须应用其相关项目事实、约定和约束； `operationGuidance` 是建议性输入，仅在适用且与内置工作流程兼容时才会遵循其条目。两者都与状态、任务、进度、上下文文件和内置指令分开。

### 4.7 `instructions archive --json`
`{ "changeName", "context"?, "operationGuidance"?, "root" }`。需要解析的 repo/store 根中的有效 `--change` 并使用与 apply 相同的 required-context/advisory-guidance 语义。这是一个只读的运行时输入表面：它不返回静态存档工作流程、检查或合并增量规范、写入主要规范或移动更改。

### 4.8 `new change <name> --json`
成功：`{ "change": { "id", "path", "metadataPath", "schema" }, "root" }`。失败：`{ "change": null, "status": [d] }`，退出1。

### 4.9 `archive <name> --json`
成功：`{ "archive": { "change", "archivedAs": "YYYY-MM-DD-name", "path", "specsUpdated", "totals"?, "warnings"? }, "root" }`。失败：`{ "archive": null, "root"?, "status": [d] }`，退出1。 `specsUpdated` 仅当至少一个规范文件被写入或停用时才为 true（一项功能，其最后一个要求被删除的更改已删除其规范，这需要更改的 `.openspec.yaml` 中的 `retire_capabilities: true`；每个停用都在 `warnings` 中命名，仅当规范存在于调用者的结帐中时，才使用可粘贴的 Git 恢复命令）；已同步的变更存档，其总数和跳过列于 `warnings` 中。 JSON模式是严格非交互的：每个提示点都变成一个`archive_*`代码。

- **`archive: null`**：命令失败。这并不能保证文件不变。
- **`archive_retirement_cleanup_failed`**：更改已存档，但停用备份验证或清理失败。列出的备份路径可能已更改或消失。该消息还可以报告失败的后备副本清理留下的暂存源。在清理之前检查当前存档和所有报告的恢复路径。保留任何需要的内容。重试存档不会清除这些路径。
- **`archive_error`**：后备诊断无法识别文件是否已更改。在重试之前检查更改、存档和受影响的规范。

### 4.10 `doctor --json`
`{ "root": { "path", "source", "store_id"?, "healthy", "status": [] }, "store": { "id", "metadata": {present,valid,remote?}, "origin_url"?, "drift"?: {ahead,behind}, "status": [] } | null, "references": [...], "status": [] }`。 `drift`（仅适用于具有上游跟踪引用的 git 支持的商店结帐）是 ahead/behind 针对上次获取的上游而不是实时远程进行计数。任何严重程度的健康检查结果都会退出 0。失败负载：`{ "root": null, "store": null, "references": [], "status": [d] }`，退出1。

### 4.11 `context --json`
`{ "root": { "path", "source", "store_id"?, "role": "openspec_root" }, "members": [ { "role": "referenced_store", "id", "path"?, "remote"?, "fetch"?, "status": [] } ], "status": [] }`。 AVAILABLE = 路径存在 AND 状态为空。 `--code-workspace <path>` 写入 `{folders:[{name,path}]}`（仅可用引用存储，`ref:` 前缀）；在 JSON 模式下，写入在打印之前运行，因此即使写入失败，标准输出也只保留一个文档。失败：`{ "root": null, "members": [], "status": [d] }`，退出1。

### 4.12 `store ... --json`
setup/register：`{ "store": {id, root, metadata_path?}, "registry": {path, registered, already_registered}, "git": {is_repository, initialized, committed}, "created_files": [], "status": [] }`。 unregister/remove：`{ "store", "registry": {path, removed}, "files": {deleted, deleted_path, left_on_disk}, "status": [] }`。列表：`{ "stores": [{id, root}], "status": [] }`。医生：`{ "stores": [ { id, root, metadata_path?, openspec_root: {...healthy, status}, metadata: {present, valid, id?, remote}, git: {is_repository, has_commits, has_uncommitted_changes, has_remote, origin_url}, status } ], "status": [] }`（`null` = unknown/not 已探测）。健康结果退出0；失败退出1并具有匹配的空形状。提示取消退出130。

### 4.13 `schemas --json` / `templates --json`
`schemas`：成功仍然是一个裸数组`[ {name, description, artifacts, source} ]`；它解析规范的根选择优先级并接受 `--store <id>`。选根失败：`{ "schemas": [], "root": null, "status": [d] }`，退出1。 `templates`：带键对象`{ "<artifactId>": {path, source} }`，仍然基于cwd，没有root/status键。

## 5。退出代码合约

|情况|退出|标准输出|
|---|---|---|
|成功，包括。健康检查结果（doctor/context/store医生）| 0 |有效负载|
|`--json` 模式下命令失败| 1 |一份包含 `status: [d]` 的 JSON 文档以及命令的空形状|
|`validate` 包含失败的项目| 1 |完整报告|
|提示取消（`store`群，人性模式）| 130 |仅标准错误|

## 6。诊断代码目录

### 分辨率
`no_openspec_root`、`no_root_with_registered_stores`、`no_registered_stores`、`unknown_store`、`store_identity_mismatch`、`unhealthy_store_root`、`store_path_not_supported`、`invalid_store_pointer`、`initiative_option_removed`、 `areas_option_removed`；直通：`invalid_store_id`、`invalid_store_registry`、`invalid_store_metadata`。

### OpenSpec-根健康状况（错误，无法修复）
`openspec_store_root_missing`、`openspec_store_root_not_directory`、`openspec_root_missing`、`openspec_root_not_directory`、`openspec_config_missing`、`openspec_config_not_file`、`openspec_specs_not_directory`、`openspec_changes_not_directory`、`openspec_archive_not_directory`。在存储测试期间，健康根中可能不存在 `openspec/specs/`、`openspec/changes/` 和 `openspec/changes/archive/`；它们仅在存在时才是健康错误，而不是目录。

### 商店 registry/identity/state
`invalid_store_id`、`invalid_store_registry`、`invalid_store_metadata`、`store_registry_busy`、`store_not_found`、`no_store_registry`、`store_registry_changed`、`store_metadata_missing`、`store_metadata_id_mismatch`、 `store_metadata_invalid`、`store_id_conflict`、`store_path_conflict`、`store_already_registered`（信息）。

### 商店 setup/register/remove
`store_setup_id_required`、`store_setup_path_required`、`store_setup_path_not_directory`、`store_setup_inside_git_repo`、`store_setup_non_empty_directory`、`store_setup_cancelled`、`store_path_required`、`store_path_missing`、`store_path_not_directory`、 `store_root_pointer_declared`、`store_register_root_unhealthy`、`store_register_identity_confirmation_required`、`store_register_cancelled`、`store_remote_empty`、`store_remote_requires_hand_edit`、`store_remove_confirmation_required`、`store_remove_cancelled`、`store_remove_path_not_directory`、 `store_remove_metadata_missing`、`store_remove_contains_registered_store`、`store_root_missing`（删除时警告，医生错误）、`store_root_not_directory`。

### 存储git
`store_git_init_failed`、`store_git_identity_missing`、`store_git_commit_failed`、`store_git_no_commits`（警告）、`store_clone_fragile_directories`（警告）、`store_remote_divergence`（信息，医生）、`store_checkout_drift`（信息，医生）。

### 参考文献（警告）
`reference_invalid_id`, `reference_registry_unreadable`, `reference_unresolved`, `reference_root_unhealthy`, `reference_index_truncated`.

### 关系（警告；医生；上下文仅保留注册表之一）
`relationship_registry_unreadable`, `root_pointer_ignored`, `root_pointer_invalid`, `pointer_declarations_inert`.

### 存档（JSON模式）
`archive_change_name_required`, `archive_change_not_found`, `archive_change_symlink`, `archive_validation_failed`, `archive_confirmation_required`, `archive_tasks_incomplete`, `archive_spec_update_failed`, `archive_spec_validation_failed`, `archive_target_exists`, `archive_retirement_cleanup_failed`, `archive_error`.

### 上下文写入
`context_file_exists`, `context_output_dir_missing`.

### 后备措施
`doctor_failed`, `context_failed`, `store_error`, `change_error`, `archive_error`.

## 已知的不一致之处

由顶点审计记录；发布的密钥重命名是在此版本之后推迟的产品决策：

1. ~~在`--json`模式下，几个失败路径仅在没有JSON文档的情况下打印stderr。~~在顶点挑战回合中修复：`show`/`validate`发出未知且不明确的项目`{status:[{code: unknown_item | ambiguous_item, ...}]}`；通过 JSON- 感知故障助手在 `status`/`instructions`/`list`/`show`/`validate` 路由中抛出错误（命令的空形状 + `status`）； `store <unknown subcommand> --json` 发出 `{status:[{code: unknown_store_subcommand}]}`； `list` 在解析失败时携带其 `{changes|specs: [], root: null}` 空形状。
2. `store_root_missing` 发出两种严重性（删除时发出警告，存储医生中出现错误）——取决于上下文，如上所述。
3. snake_case（商店系列）与camelCase（工作流程系列）钥匙壳； `root.store_id` 到处都是 snake_case。
4. src 中存在四个并行的信封类型声明；存档诊断从不携带 `target`。
5. `list --json` 在每次更改时重用 `status` 键作为字符串枚举。
6. 仅 `validate` 输出带有 `version` 字段。
7. `templates` 忽略根选择（基于 cwd，无 `--store`）。
8. 已弃用的名词形式（`change`/`spec` 子命令）会发出不带 `root`/`status` 的未封装有效负载。


## 原文对照

<details>
<summary>展开完整原文</summary>

# OpenSpec Agent Contract

Machine-readable surfaces of the `openspec` CLI, verified against `src/` (capstone audit, 2026-06-11). Every shape below is documented from the emitting code.

## 1. General conventions

- **One JSON document per invocation.** In `--json` mode, stdout carries exactly one JSON document (2-space pretty-printed). Human prose, spinners, and the store banner go to stderr.
- **Store banner.** In human mode, a store-selected root prints `Using OpenSpec root: <id> (<path>)` to stderr. Never printed in JSON mode.
- **Key casing is surface-dependent** (see Known inconsistencies): store/doctor/context payloads use `snake_case`; workflow payloads (`status`, `instructions`, `new change`, `validate`, `list`) use `camelCase`, except the embedded `root` object, which always uses `store_id`.
- **Optional keys are omitted, not null**, in most payloads (e.g. `root.store_id`, `member.path`). Exceptions that use explicit `null` are called out per shape (store doctor `git.*`, failure payloads).

## 2. The diagnostic envelope

One envelope shape is shared by every machine-readable diagnostic (`StoreDiagnostic`):

```json
{
  "severity": "error" | "warning" | "info",
  "code": "snake_case_string",
  "message": "human sentence",
  "target": "dotted.surface (optional)",
  "fix": "one actionable sentence/command (optional)"
}
```

Diagnostics appear in two positions: **status arrays** (`status: StoreDiagnostic[]` at top level or per entry) for health findings, and **thrown errors** converted to a single-element `status` array on command failure.

## 3. Root selection and `RootOutput`

All root-resolving commands (`list`, `show`, `validate`, `status`, `instructions`, `instructions apply`, `instructions archive`, `new change`, `archive`, `doctor`, `context`, `schemas`) resolve one OpenSpec root with one precedence:

1. `--store <id>` → the registered store's root (`source: "store"`).
2. Otherwise, nearest ancestor with `openspec/`: planning shape → `source: "nearest"` (a `store:` pointer is ignored with a stderr warning); config-only dir with a valid `store:` pointer → that store, `source: "declared"`.
3. No nearest root + global `defaultStore` set (`openspec config set defaultStore <id>`) → that store, `source: "global_default"`; a stale id fails with the underlying store error and a `fix` naming `openspec config unset defaultStore`.
4. No nearest root, no default + registered stores exist → error `no_root_with_registered_stores`.
5. No root, no default, no stores: commands may treat the cwd as `source: "implicit"`; `doctor`, `context`, `list`, and bulk `validate` instead fail with `no_openspec_root`. `list` preserves the implicit fallback for legacy projects with `openspec/project.md`.

Successful JSON payloads normally embed the root; successful `schemas --json`
deliberately remains the compatibility bare array documented in §4.13:

```json
"root": { "path": "/abs/path", "source": "store" | "declared" | "global_default" | "nearest" | "implicit", "store_id": "id (only when store-selected)" }
```

**Root-failure contract**: in JSON mode a resolution failure prints `{ ...commandNullShape, "status": [diagnostic] }` on stdout and exits 1.

## 4. Command JSON shapes

### 4.1 `list --json`
`{ "changes": [ { "name", "completedTasks", "totalTasks", "lastModified", "status": "no-tasks"|"complete"|"in-progress", "nested"?: ["<area>/<name>", ...] } ], "warnings"?: [ { "code", "name", "nested", "message" } ], "root": RootOutput }` — note the per-change `status` is a string enum here. `--specs`: `{ "specs": [ { "id", "requirementCount" } ], "root" }`.

`warnings` (omitted when empty) reports directories under `changes/` that are not changes. Today the only code is `nested_change_directory`: a namespace folder wrapping change directories, which OpenSpec cannot address because a change is always a directory directly under `changes/`. The same entry carries `nested` on the listed change, whose `status` is then meaningless. Do not treat such an entry as a change; report the message and leave the directories alone.

### 4.2 `show <item> --json`
Change: `{ "id", "title", "deltaCount", "deltas": [...], "root" }`. Spec: `{ "id", "title", "overview", "requirementCount", "requirements": [...], "metadata": { "version", "format", "sourcePath"? }, "root" }`. A requirement, in a spec or in a change delta's `requirement`/`requirements`, is `{ "name", "text", "scenarios": [ { "name", "rawText" } ] }`. A requirement `name` is its header without `Requirement:` and without a closing `#` run, the exact name archive matches MODIFIED/REMOVED/RENAMED entries against. A scenario `name` is its level-4 header without `Scenario:` and without a closing `#` run, the name the MODIFIED scenario-loss check compares.

### 4.3 `validate --json`
`{ "items": [ { "id", "type": "change"|"spec", "valid", "issues": [ { "level", "path", "message", "line"?, "column"? } ], "durationMs" } ], "summary": { "totals": {items,passed,failed}, "byType": {...} }, "version": "1.0", "root" }`. Exit 1 when any item fails.

### 4.4 `status --json`
`{ "changeName", "schemaName", "planningHome"?: { "kind", "root", "changesDir", "defaultSchema" }, "changeRoot", "artifactPaths": { "<id>": {outputPath, resolvedOutputPath, existingOutputPaths} }, "nextSteps": ["..."], "actionContext": { "mode": "repo-local", "sourceOfTruth": "repo", "planningArtifacts", "linkedContext", "allowedEditRoots", "requiresAffectedAreaSelection", "constraints" }, "isPlanningComplete", "isComplete", "applyRequires", "artifacts": [ {id, outputPath, status: "done"|"skipped"|"ready"|"blocked", requires, missingDeps?} ], "root" }`. For a store-selected root, `allowedEditRoots` is `[<declaring project>, <store>]` when the nearest project on the current path is a config-only root whose `store:` pointer names that store, and `[<store>]` otherwise (including a global `defaultStore`), with a constraint telling the agent to ask which repository to edit. OpenSpec does not route a store's tasks to repos, so the declaring project is the current one, not every repo the change touches. `isPlanningComplete` means every non-skipped planning artifact exists; skipped artifacts count as satisfied without being created. It does not mean implementation tasks are complete. `isComplete` is retained as a compatibility alias with the same value. Each artifact's `requires` is its direct dependency ids (present for every status, so the transitive required set is computable even when the artifact is `done`); `missingDeps` appears only when `blocked`. The `artifacts` array is in dependency order, with the schema's `artifacts:` declaration order breaking ties between artifacts that become ready at the same time (never alphabetical), so the first `ready` entry is the artifact to write next; `missingDeps` uses that same order. `"skipped"` marks an artifact whose `generates` path is under `specs/` in a change whose `.openspec.yaml` declares `skip_specs: true`; it satisfies dependencies but must not be created. No active changes: `{ "changes": [], "message", "root" }`, exit 0.

`--all` (batch, mutually exclusive with `--change` — combining them is an error with the `{ "changes": [], "root": null, "status": [d] }` null-shape): `{ "changes": [ <per-change status object, no per-change root>, ... ], "root" }`, sorted by change name. A change that fails to load contributes `{ "changeName", "status": [d] }` in place; the sweep continues, preserves the complete envelope, and exits 1 in both text and JSON modes. An invalid `--schema` fails the whole invocation with the null-shape, even when no changes exist.

### 4.5 `instructions <artifact> --json`
`{ "changeName", "artifactId", "schemaName", "changeDir", "planningHome"?, "outputPath", "resolvedOutputPath", "existingOutputPaths", "description", "instruction"?, "context"?, "rules"?, "references"?: ReferenceIndexEntry[], "skipped"?, "warning"?, "template", "dependencies": [{id,done,path,description,skipped?}], "unlocks", "root" }`. `unlocks` lists the artifacts this one makes ready, in the schema's declaration order (the same order `status` recommends them). `"skipped": true` (with `"warning"`) appears when the change declares `skip_specs: true` and this artifact is skipped — do not create its files. A dependency entry with `skipped: true` is satisfied without files — do not try to read its paths.

`ReferenceIndexEntry`: `{ "store_id", "root"?, "specs"?: [{id,summary}], "fetch"?, "status": [] }` — resolved entries carry root/specs/fetch; unresolved carry store_id + warning status. Index capped at 50KB (`reference_index_truncated`).

### 4.6 `instructions apply --json`
`{ "changeName", "changeDir", "schemaName", "contextFiles": { "<artifactId>": ["/abs", ...] }, "progress": {total,complete,remaining}, "tasks": [{id,description,done}], "state": "blocked"|"all_done"|"ready", "missingArtifacts"?, "missingPrerequisites"?, "warnings"?, "instruction", "references"?, "context"?, "operationGuidance"?, "root" }`. `missingArtifacts` is what apply blocks on (the schema's `apply.requires`); `missingPrerequisites` is everything still to build before apply can run, in build order - the transitive closure of those requires, so it can be the longer list. `warnings` lists non-blocking problems with the change itself - today, a change that is ready to implement with no delta specs and no `skip_specs: true`, the state `openspec validate` rejects. Both optional root fields (`context`, `operationGuidance`) are read from the selected root on every invocation. `context` is a required prompt-level input whose relevant project facts, conventions, and constraints must be applied; `operationGuidance` is advisory input whose entries are followed only when applicable and compatible with the built-in workflow. Both remain separate from state, tasks, progress, context files, and the built-in instruction.

### 4.7 `instructions archive --json`
`{ "changeName", "context"?, "operationGuidance"?, "root" }`. Requires a valid `--change` in the resolved repo/store root and uses the same required-context/advisory-guidance semantics as apply. This is a read-only runtime-input surface: it does not return the static archive workflow, inspect or merge delta specs, write main specs, or move the change.

### 4.8 `new change <name> --json`
Success: `{ "change": { "id", "path", "metadataPath", "schema" }, "root" }`. Failure: `{ "change": null, "status": [d] }`, exit 1.

### 4.9 `archive <name> --json`
Success: `{ "archive": { "change", "archivedAs": "YYYY-MM-DD-name", "path", "specsUpdated", "totals"?, "warnings"? }, "root" }`. Failure: `{ "archive": null, "root"?, "status": [d] }`, exit 1. `specsUpdated` is true only when at least one spec file was written or retired (a capability whose last requirement the change removed has its spec deleted, which requires `retire_capabilities: true` in the change's `.openspec.yaml`; every retirement is named in `warnings`, with a pasteable Git recovery command only when the spec lived in the caller's checkout); an already-synced change archives with all-zero totals and the skips listed in `warnings`. JSON mode is strictly non-interactive: every prompt point becomes an `archive_*` code.

- **`archive: null`**: the command failed. This is not a guarantee that files are unchanged.
- **`archive_retirement_cleanup_failed`**: the change was archived, but retirement backup verification or cleanup failed. A listed backup path may have changed or disappeared. The message can also report a staged source left by a failed fallback-copy cleanup. Inspect the current archive and all reported recovery paths before cleanup. Preserve any needed content. Retrying archive does not clean up these paths.
- **`archive_error`**: the fallback diagnostic does not identify whether files changed. Inspect the change, archive, and affected specs before retrying.

### 4.10 `doctor --json`
`{ "root": { "path", "source", "store_id"?, "healthy", "status": [] }, "store": { "id", "metadata": {present,valid,remote?}, "origin_url"?, "drift"?: {ahead,behind}, "status": [] } | null, "references": [...], "status": [] }`. `drift` (present only for a git-backed store checkout that has an upstream tracking ref) is ahead/behind counts against the last-fetched upstream, not the live remote. Health findings of any severity exit 0. Failure payload: `{ "root": null, "store": null, "references": [], "status": [d] }`, exit 1.

### 4.11 `context --json`
`{ "root": { "path", "source", "store_id"?, "role": "openspec_root" }, "members": [ { "role": "referenced_store", "id", "path"?, "remote"?, "fetch"?, "status": [] } ], "status": [] }`. AVAILABLE = path present AND status empty. `--code-workspace <path>` writes `{folders:[{name,path}]}` (available referenced stores only, `ref:` prefixes); in JSON mode the write runs before printing so stdout holds exactly one document even on write failure. Failure: `{ "root": null, "members": [], "status": [d] }`, exit 1.

### 4.12 `store ... --json`
setup/register: `{ "store": {id, root, metadata_path?}, "registry": {path, registered, already_registered}, "git": {is_repository, initialized, committed}, "created_files": [], "status": [] }`. unregister/remove: `{ "store", "registry": {path, removed}, "files": {deleted, deleted_path, left_on_disk}, "status": [] }`. list: `{ "stores": [{id, root}], "status": [] }`. doctor: `{ "stores": [ { id, root, metadata_path?, openspec_root: {...healthy, status}, metadata: {present, valid, id?, remote}, git: {is_repository, has_commits, has_uncommitted_changes, has_remote, origin_url}, status } ], "status": [] }` (`null` = unknown/not probed). Health findings exit 0; failures exit 1 with the matching null-shape. Prompt cancellation exits 130.

### 4.13 `schemas --json` / `templates --json`
`schemas`: success remains a bare array `[ {name, description, artifacts, source} ]`; it resolves the canonical root-selection precedence and accepts `--store <id>`. Root-selection failure: `{ "schemas": [], "root": null, "status": [d] }`, exit 1. `templates`: keyed object `{ "<artifactId>": {path, source} }`, still cwd-based with no root/status keys.

## 5. Exit-code contract

| Situation | Exit | Stdout |
|---|---|---|
| Success, incl. health findings (doctor/context/store doctor) | 0 | the payload |
| Command failure in `--json` mode | 1 | one JSON document with `status: [d]` and the command's null-shape |
| `validate` with failing items | 1 | full report |
| Prompt cancellation (`store` group, human mode) | 130 | stderr only |

## 6. Diagnostic code catalog

### Resolution
`no_openspec_root`, `no_root_with_registered_stores`, `no_registered_stores`, `unknown_store`, `store_identity_mismatch`, `unhealthy_store_root`, `store_path_not_supported`, `invalid_store_pointer`, `initiative_option_removed`, `areas_option_removed`; pass-through: `invalid_store_id`, `invalid_store_registry`, `invalid_store_metadata`.

### OpenSpec-root health (error, no fix)
`openspec_store_root_missing`, `openspec_store_root_not_directory`, `openspec_root_missing`, `openspec_root_not_directory`, `openspec_config_missing`, `openspec_config_not_file`, `openspec_specs_not_directory`, `openspec_changes_not_directory`, `openspec_archive_not_directory`. During the stores beta, `openspec/specs/`, `openspec/changes/`, and `openspec/changes/archive/` may be absent in a healthy root; they are only health errors when present but not directories.

### Store registry/identity/state
`invalid_store_id`, `invalid_store_registry`, `invalid_store_metadata`, `store_registry_busy`, `store_not_found`, `no_store_registry`, `store_registry_changed`, `store_metadata_missing`, `store_metadata_id_mismatch`, `store_metadata_invalid`, `store_id_conflict`, `store_path_conflict`, `store_already_registered` (info).

### Store setup/register/remove
`store_setup_id_required`, `store_setup_path_required`, `store_setup_path_not_directory`, `store_setup_inside_git_repo`, `store_setup_non_empty_directory`, `store_setup_cancelled`, `store_path_required`, `store_path_missing`, `store_path_not_directory`, `store_root_pointer_declared`, `store_register_root_unhealthy`, `store_register_identity_confirmation_required`, `store_register_cancelled`, `store_remote_empty`, `store_remote_requires_hand_edit`, `store_remove_confirmation_required`, `store_remove_cancelled`, `store_remove_path_not_directory`, `store_remove_metadata_missing`, `store_remove_contains_registered_store`, `store_root_missing` (warning in remove, error in doctor), `store_root_not_directory`.

### Store git
`store_git_init_failed`, `store_git_identity_missing`, `store_git_commit_failed`, `store_git_no_commits` (warning), `store_clone_fragile_directories` (warning), `store_remote_divergence` (info, doctor), `store_checkout_drift` (info, doctor).

### References (warning)
`reference_invalid_id`, `reference_registry_unreadable`, `reference_unresolved`, `reference_root_unhealthy`, `reference_index_truncated`.

### Relationships (warning; doctor; context keeps only the registry one)
`relationship_registry_unreadable`, `root_pointer_ignored`, `root_pointer_invalid`, `pointer_declarations_inert`.

### Archive (JSON mode)
`archive_change_name_required`, `archive_change_not_found`, `archive_change_symlink`, `archive_validation_failed`, `archive_confirmation_required`, `archive_tasks_incomplete`, `archive_spec_update_failed`, `archive_spec_validation_failed`, `archive_target_exists`, `archive_retirement_cleanup_failed`, `archive_error`.

### Context writes
`context_file_exists`, `context_output_dir_missing`.

### Fallbacks
`doctor_failed`, `context_failed`, `store_error`, `change_error`, `archive_error`.

## Known inconsistencies

Recorded by the capstone audit; published-key renames are product decisions deferred past this release:

1. ~~In `--json` mode, several failure paths printed stderr only with no JSON document.~~ Fixed in the capstone gauntlet round: `show`/`validate` unknown and ambiguous items emit `{status:[{code: unknown_item | ambiguous_item, ...}]}`; thrown errors in `status`/`instructions`/`list`/`show`/`validate` route through the JSON-aware failure helper (the command's null-shape + `status`); `store <unknown subcommand> --json` emits `{status:[{code: unknown_store_subcommand}]}`; `list` carries its `{changes|specs: [], root: null}` null-shape on resolution failures.
2. `store_root_missing` is emitted with two severities (warning in remove, error in store doctor) — context-dependent, documented above.
3. snake_case (store family) vs camelCase (workflow family) key casing; `root.store_id` is snake_case everywhere.
4. Four parallel envelope type declarations exist in src; archive diagnostics never carry `target`.
5. `list --json` reuses the `status` key as a string enum per change.
6. Only `validate` output carries a `version` field.
7. `templates` ignores root selection (cwd-based, no `--store`).
8. Deprecated noun forms (`change`/`spec` subcommands) emit unenveloped payloads without `root`/`status`.


</details>
