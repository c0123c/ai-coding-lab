# 迁移项目配置到 Codex 的技能

说明将已有指令、Skills、角色和 MCP 配置按顺序迁入项目或全局文件，并进行修复与验证。

- 来源：OpenAI／Skills
- 资料类型：存档文档
- 存档版本：`49f948faa925`。
- 文档时间：源码提交（存档版）：[2026-05-01](<https://github.com/openai/skills/commit/45d05d75363abf13f99d09e899d61e07b8010685>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/skills/blob/main/skills/.curated/migrate-to-codex/SKILL.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Skills/迁移项目配置到 Codex 的技能.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: migrate-to-codex
description: Migrate supported instruction files, skills, agents, and MCP config into Codex project and global files.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 迁移到 Codex

## 自主性

继续进行，直到选定的迁移完全完成：运行迁移器，检查报告，修复迁移的 Codex instructions/skills/agents/MCP 配置，然后重新运行检查，而无需停下来要求确认下一步。如果用户已选择目标，则在创建、编辑、替换或删除该目标（`AGENTS.md`、`.codex/`、`.agents/` 或 `~/.codex/`）中生成的 Codex 工件之前不要询问。保留`.codex/config.toml`或`~/.codex/config.toml`中不相关的现有Codex配置条目，例如`notify`、`projects`、`marketplaces`或不相关的MCP服务器；不要询问它们，除非它们未通过验证或与迁移直接冲突。请勿编辑源 Claude Code 文件（`.claude/`、`~/.claude/`、`.mcp.json` 或 `.claude.json`）、不相关的项目代码、机密或其他存储库。

## 迁移令

对于每个选定的全局或项目源，按以下顺序运行迁移：

1. 首先使用Codex的内置TODO/task列表工具。除非用户明确要求，否则不要创建 `MIGRATION_TODOS.md` 或任何 TODO 文件。 TODO 列表输入有一个 `plan` 数组，其中每个项目都有 `step` 和 `status`；使用状态 `pending`、`in_progress` 和 `completed`。使 TODO 特定于选定的工件。完成之前，更新 TODO 列表，以便每个已完成的步骤都标记为 `completed`，并且没有任何步骤保留为 `in_progress`。使用文字源 → Codex 目标标签，例如：
   - 检查 `.claude/commands` → Codex skills/prompts
   - 检查`.claude/agents`→`.codex/agents`
   - 检查 `.mcp.json` → `.codex/config.toml` MCP 服务器
   - 检查 `.claude/settings.json` 挂钩 → `.codex/hooks.json`
   - 迁移安全选定的工件 → Codex 文件
   - 验证生成的`.codex/config.toml`
   - 验证生成的`.codex/agents`
   - 报告迁移的工件和手动审核项目

2. 阅读 `references/differences.md`（如果 `Docs last checked` 日期较旧，则刷新 Codex 文档）。

3. 书写前扫描并检查：
   - `--scan-only` 列出了活动和非活动源表面。
   - `--plan` 打印暂存的 Codex 工件路径和报告行。
   - `--doctor` 总结了准备情况、手动审核工作和验证风险。

4. 按照 CLI 使用的相同顺序转换曲面：
   - 指令：`CLAUDE.md` / `AGENTS.md` 至 `AGENTS.md`
   - 插件：将 Claude 插件树和市场报告为手动迁移工作
   - hooks：将支持的 Claude 钩子重写为 `.codex/hooks.json` 并启用 `[features].codex_hooks = true`
   - 技能和命令：在`.agents/skills/`下写上Codex技能
   - 配置：从Claude model/sandbox设置和MCP服务器写入`.codex/config.toml`，生成配置时包括`personality = "friendly"`
   - 子代理：在 `.codex/agents/` 下写入 Codex 自定义 agents

5. 试运行，然后写入选定的目标。仅当孤立生成的技能或应删除agents时才使用`--replace`。

6. 实际运行后检查终端输出和`.codex/migrate-to-codex-report.txt`。

7. 按以下顺序查看生成的工件：`AGENTS.md`、`.agents/skills/`、`.codex/config.toml`、`.codex/hooks.json`、`.codex/agents/`，然后仅报告插件项目。

8. 编辑后针对每个目标运行 `--validate-target`。

9. 编辑后重新运行检查和 `--dry-run`。

10. 将最终迁移报告作为每个包含行的范围的一个降价表返回。这些表格仅涵盖您执行的非本机后续迁移工作，例如从斜杠命令、子代理、MCP 服务器、挂钩、unsupported/local 插件注释和手动审核警告创建的技能。仅当您在此后续运行中亲自迁移了配置、说明、技能或支持的插件时，才包含配置、说明、技能或支持的插件的编程本机导入行。

如果只有一个范围有行，则仅呈现没有标题的表。如果多个范围有行，则在每个表之前呈现一个标题。对用户范围行使用 `**User Config**`。对于项目范围行，使用实际项目文件夹名称作为标题，例如 `**northstar-support-portal**`；请勿使用 `Current Project` 作为标题。不要在表格输出之前或之后添加散文。

准确使用这些列：

**northstar-支持门户**

    |状态|项目|注释|
    | --- | --- | --- |
    | `Added` |`Slash command`预审|转换为Codex技能|
    | `Added` |`Subagent` 释放铅|添加为 Codex 子代理|
    | `Check before using` | `Hook` PreToolUse |已转换，但某些 Claude 挂钩行为与 Codex 不同|
    | `Not Added` |`Hook`通知|Codex 没有等效的通知挂钩|
    | `Not Added` |`Plugin` 团队宏|插件需要手动设置|

`Status` 必须是 `Added`、`Check before using` 或 `Not Added`。当创建或更改面向 Codex 的工件并且不需要特殊审核时，请使用 `Added`。当创建或更改了面向 Codex 的工件，但迁移更改了语义、推断的行为、保留工具规则作为指导或删除了不受支持的行为时，请使用 `Check before using`。当检测到源工件但未创建面向 Codex 的工件时，请使用 `Not Added`。 `Item` 将工件类型和具体项目名称组合在一个单元格中。工件类型必须是单数：`Skill`、`Slash command`、`Subagent`、`MCP`、`Hook` 或 `Plugin`。将工件类型包装在内联代码中；在其后以纯文本形式写入项目名称。 `Notes` 始终为必填项；切勿将其留空。保持笔记简短、简单、字面意思。避免内部实现术语，例如运行时扩展。首选 `Converted into a Codex skill`、`Added as a Codex subagent`、`Added to Codex config`、`Converted into a Codex hook`、`Converted, but some Claude hook behavior differs in Codex`、`Codex does not have an equivalent notification hook`、`Plugin needs manual setup` 或 `Plugin marketplace needs manual setup` 等短语。

## 自愈循环

继续循环，直到选定的迁移完成：

1. 运行`--plan`或`--doctor`。
2. 使用 `--dry-run` 运行迁移。
3. 真正运行迁移。
4. 修复每个生成的 `## MANUAL MIGRATION REQUIRED` 块以及可以在 Codex 工件内解析的每个 `manual_fix_required` 或 `skipped` 报告行。
5. 运行`--validate-target`。
6. 重新运行迁移器和验证器，直到报告和验证器没有留下可操作的生成工件修复。

在此循环期间，请勿编辑源 Claude Code 文件、不相关的项目代码、机密或其他存储库。如果报告行需要源提供商更改或产品判断，请为生成的 Codex 工件保留清晰的手动指导，而不是更改源。

## 命令

选择迁移器命令。

   ```bash
   MIGRATE_TO_CODEX='python3 .codex/skills/migrate-to-codex/scripts/migrate-to-codex.py'
   ```

写入之前检查迁移。

   ```bash
   $MIGRATE_TO_CODEX --source ~/.claude/ --scan-only
   $MIGRATE_TO_CODEX --source ~/.claude/ --target ~/.codex/ --plan
   $MIGRATE_TO_CODEX --source ~/.claude/ --target ~/.codex/ --doctor
   ```

对于全局和项目，试运行，然后在没有 `--dry-run` 的情况下运行。

   ```bash
   $MIGRATE_TO_CODEX --source ~/.claude/ --target ~/.codex/ --dry-run
   $MIGRATE_TO_CODEX --source ~/.claude/ --target ~/.codex/
   $MIGRATE_TO_CODEX --source ./.claude/ --target ./.codex/ --dry-run
   $MIGRATE_TO_CODEX --source ./.claude/ --target ./.codex/
   ```

编辑后针对每个目标运行迁移后验证器。

   ```bash
   $MIGRATE_TO_CODEX --validate-target ~/.codex/
   $MIGRATE_TO_CODEX --validate-target ./.codex/
   ```

运行 `$MIGRATE_TO_CODEX --help` 以获取标志（`--scan-only`、`--plan`、`--doctor`、`--validate-target`、默认值等）。深表和更多链接在`references/differences.md`中。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Migrate to Codex

## Autonomy

Keep going until the selected migration is completely done: run the migrator, inspect the report, fix migrated Codex instructions/skills/agents/MCP config, and re-run checks without stopping to ask for confirmation of the next step. If the user has selected a target, do not ask before creating, editing, replacing, or deleting generated Codex artifacts in that target (`AGENTS.md`, `.codex/`, `.agents/`, or `~/.codex/`). Preserve unrelated existing Codex config entries in `.codex/config.toml` or `~/.codex/config.toml`, such as `notify`, `projects`, `marketplaces`, or unrelated MCP servers; do not ask about them unless they fail validation or directly conflict with the migration. Do not edit source Claude Code files (`.claude/`, `~/.claude/`, `.mcp.json`, or `.claude.json`), unrelated project code, secrets, or another repository.

## Migration Order

Run the migration in this order for each selected global or project source:

1. Start by using Codex's built-in TODO/task list tool. Do not create `MIGRATION_TODOS.md` or any TODO file unless the user explicitly asks. The TODO list input has a `plan` array whose items each have `step` and `status`; use statuses `pending`, `in_progress`, and `completed`. Make the TODOs specific to the selected artifacts. Before finishing, update the TODO list so every finished step is marked `completed` and no step remains `in_progress`. Use literal source → Codex target labels, for example:
   - Inspect `.claude/commands` → Codex skills/prompts
   - Inspect `.claude/agents` → `.codex/agents`
   - Inspect `.mcp.json` → `.codex/config.toml` MCP servers
   - Inspect `.claude/settings.json` hooks → `.codex/hooks.json`
   - Migrate safe selected artifacts → Codex files
   - Validate generated `.codex/config.toml`
   - Validate generated `.codex/agents`
   - Report migrated artifacts and manual-review items

2. Read `references/differences.md` (and refresh Codex docs if its `Docs last checked` date is old).

3. Scan and inspect before writing:
   - `--scan-only` lists active and inactive source surfaces.
   - `--plan` prints staged Codex artifact paths and report rows.
   - `--doctor` summarizes readiness, manual-review work, and validation risks.

4. Convert surfaces in the same order the CLI uses:
   - instructions: `CLAUDE.md` / `AGENTS.md` to `AGENTS.md`
   - plugins: report Claude plugin trees and marketplaces as manual migration work
   - hooks: rewrite supported Claude hooks into `.codex/hooks.json` and enable `[features].codex_hooks = true`
   - skills and commands: write Codex skills under `.agents/skills/`
   - config: write `.codex/config.toml` from Claude model/sandbox settings and MCP servers, including `personality = "friendly"` when config is generated
   - subagents: write Codex custom agents under `.codex/agents/`

5. Dry-run, then write the selected target. Use `--replace` only when orphan generated skills or agents should be deleted.

6. Inspect the terminal output and `.codex/migrate-to-codex-report.txt` after real runs.

7. Review generated artifacts in this order: `AGENTS.md`, `.agents/skills/`, `.codex/config.toml`, `.codex/hooks.json`, `.codex/agents/`, then report-only plugin items.

8. Run `--validate-target` against each target after edits.

9. Re-run checks and `--dry-run` after edits.

10. Return the final migration report as one markdown table per scope that has rows. The tables cover only the non-native follow-up migration work you performed, such as skills created from slash commands, subagents, MCP servers, hooks, unsupported/local plugin notes, and manual-review caveats. Include programmatic native import rows for config, instructions, skills, or supported plugins only if you personally migrated them in this follow-up run.

    If only one scope has rows, render only the table with no heading. If multiple scopes have rows, render one heading before each table. Use `**User Config**` for user-scope rows. For project-scope rows, use the actual project folder name as the heading, for example `**northstar-support-portal**`; do not use `Current Project` as the heading. Do not add prose before or after the table output.

    Use exactly these columns:

    **northstar-support-portal**

    | Status | Item | Notes |
    | --- | --- | --- |
    | `Added` | `Slash command` pr-review | Converted into a Codex skill |
    | `Added` | `Subagent` release-lead | Added as a Codex subagent |
    | `Check before using` | `Hook` PreToolUse | Converted, but some Claude hook behavior differs in Codex |
    | `Not Added` | `Hook` Notification | Codex does not have an equivalent notification hook |
    | `Not Added` | `Plugin` team-macros | Plugin needs manual setup |

    `Status` must be `Added`, `Check before using`, or `Not Added`. Use `Added` when a Codex-facing artifact was created or changed and needs no special review. Use `Check before using` when a Codex-facing artifact was created or changed but the migration changed semantics, inferred behavior, preserved tool rules as guidance, or dropped unsupported behavior. Use `Not Added` when a source artifact was detected but no Codex-facing artifact was created. `Item` combines the artifact type and concrete item name in one cell. Artifact type must be singular: `Skill`, `Slash command`, `Subagent`, `MCP`, `Hook`, or `Plugin`. Wrap the artifact type in inline code; write the item name as plain text after it. `Notes` is always required; never leave it empty. Keep notes short, plain, and literal. Avoid internal implementation terms such as runtime expansion. Prefer phrases like `Converted into a Codex skill`, `Added as a Codex subagent`, `Added to Codex config`, `Converted into a Codex hook`, `Converted, but some Claude hook behavior differs in Codex`, `Codex does not have an equivalent notification hook`, `Plugin needs manual setup`, or `Plugin marketplace needs manual setup`.

## Self-Healing Loop

Keep looping until the selected migration is complete:

1. Run `--plan` or `--doctor`.
2. Run the migration with `--dry-run`.
3. Run the migration for real.
4. Fix every generated `## MANUAL MIGRATION REQUIRED` block and every `manual_fix_required` or `skipped` report row that can be resolved inside Codex artifacts.
5. Run `--validate-target`.
6. Re-run the migrator and validator until the report and validator have no actionable generated-artifact fixes left.

Do not edit source Claude Code files, unrelated project code, secrets, or another repository during this loop. If a report row requires source-provider changes or product judgment, leave the generated Codex artifact with clear manual guidance instead of changing the source.

## Commands

Choose the migrator command.

   ```bash
   MIGRATE_TO_CODEX='python3 .codex/skills/migrate-to-codex/scripts/migrate-to-codex.py'
   ```

Inspect the migration before writing.

   ```bash
   $MIGRATE_TO_CODEX --source ~/.claude/ --scan-only
   $MIGRATE_TO_CODEX --source ~/.claude/ --target ~/.codex/ --plan
   $MIGRATE_TO_CODEX --source ~/.claude/ --target ~/.codex/ --doctor
   ```

Dry-run, then run without `--dry-run`, for global and project.

   ```bash
   $MIGRATE_TO_CODEX --source ~/.claude/ --target ~/.codex/ --dry-run
   $MIGRATE_TO_CODEX --source ~/.claude/ --target ~/.codex/
   $MIGRATE_TO_CODEX --source ./.claude/ --target ./.codex/ --dry-run
   $MIGRATE_TO_CODEX --source ./.claude/ --target ./.codex/
   ```

Run the post-migration validator against each target after edits.

   ```bash
   $MIGRATE_TO_CODEX --validate-target ~/.codex/
   $MIGRATE_TO_CODEX --validate-target ./.codex/
   ```

Run `$MIGRATE_TO_CODEX --help` for flags (`--scan-only`, `--plan`, `--doctor`, `--validate-target`, defaults, and so on). Deep tables and more links are in `references/differences.md`.


</details>
