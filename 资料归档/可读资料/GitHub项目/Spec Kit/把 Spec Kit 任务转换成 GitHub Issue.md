# 把 Spec Kit 任务转换成 GitHub Issue

根据设计产物将任务转成有依赖顺序的 Issue，并执行前后检查；属于任务交付命令模板。

- 来源：Spec Kit
- 资料类型：存档文档
- 存档版本：`0443760542e7`。
- 文档时间：源码提交（存档版）：[2026-09-29](<https://github.com/github/spec-kit/commit/987c9b8b18a31779b6f709fa6b276d3bc4f630d0>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/spec-kit/blob/main/extensions/github/commands/speckit.github.taskstoissues.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Spec Kit/把 Spec Kit 任务转换成 GitHub Issue.md>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
description: Convert existing tasks into actionable, dependency-ordered GitHub issues for the feature based on available design artifacts.
tools: ['github/github-mcp-server/list_issues', 'github/github-mcp-server/issue_write']
scripts:
  sh: scripts/bash/resolve-tasks.sh --json
  ps: scripts/powershell/resolve-tasks.ps1 -Json
  py: scripts/python/resolve_tasks.py --json
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

**检查扩展挂钩（在任务到问题转换之前）**：
- 检查项目根目录中是否存在`.specify/extensions.yml`。
- 如果存在，则读取它并查找 `hooks.before_taskstoissues` 键下的条目
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

1. 从存储库根运行 `{SCRIPT}` 并解析 FEATURE_DIR、TASKS 和 AVAILABLE_DOCS 列表。所有路径都必须是绝对路径。对于像“I'm Groot”这样的参数中的单引号，请使用转义语法：例如 'I'\''m Groot' （或者如果可能的话使用双引号：“I'm Groot”）。
1. **IF EXISTS**：加载 `/memory/constitution.md` 以了解项目原则和治理约束。
1. 从执行的脚本中，提取 **tasks** 的路径（`TASKS` 值）。
1. 通过运行以下命令获取 Git 远程：

```bash
git config --get remote.origin.url
```

> [!CAUTION]
> ONLY PROCEED TO NEXT STEPS IF THE REMOTE IS A GITHUB URL

1. **获取重复数据删除的现有问题**：在创建任何内容之前，从 `tasks.md` 构建您要处理的任务 ID 集（每个 ID 都是 `T` 后跟 ** 至少 ** 三位数，例如`T001` — `__SPECKIT_COMMAND_CONVERGE__` 使用 `T{M+1:03d}` 分配新 ID，这是一个下限而不是上限，因此一旦文件具有超过 999 任务，ID 就会是四位数或更长）。然后使用 GitHub MCP 服务器的 `list_issues` 工具查找已涵盖这些 ID 的问题。不要传递 `state` 值，因为省略它会使工具返回打开和关闭的问题。请求 `perPage: 100` 以减少调用次数，并且由于该工具使用基于 cursor 的分页，因此请使用 `after` 参数请求页面（使用之前响应中的 `endCursor`）。对于每个问题标题，将其与任务 ID 模式 `\bT\d{3,}\b` 进行匹配（`{3,}` 接受四位数和更长的 ID — 使用 `\d{3}`，包含 `T1000` 的标题根本不匹配，因为结尾的 `\b` 不能落在两位数字之间，因此任务不会默默地进行重复数据删除或创建；单词边界仍然会阻止像 `ST001` 这样的标记匹配，并强制消耗整个数字运行，因此 `T100` 永远不会在 `T1000` 内匹配；这也可以识别写为 `T001 ...`、`T001: ...` 的标题`[T001] ...`），并且当它与您的任务 ID 之一匹配时，将 ID 标记为已存在问题。一旦每个任务 ID 匹配，或者没有更多页面，就停止分页，这样一旦所有任务 ID 都被考虑在内，您就不会继续获取整个存储库的问题历史记录。这限制了对具有大量问题历史记录的存储库的调用数量，并且在重新生成 `tasks.md` 或重新调用技能后重新运行命令时仍然可以防止重复。
1. 对于列表中的每个任务，使用 GitHub MCP 服务器在代表 Git 远程的存储库中创建一个新问题。 `tasks.md` 中的任务行以 markdown 复选框开头，因此首先删除前导 `- [ ]`（以及任何 `[P]` / `[US#]` 标记）以恢复任务 ID 及其描述。使用 `T001: <description>` 形式的单个规范标题创建问题，其中 ID 写入一次，后跟任务描述（例如，行 `- [ ] T001 Create project structure` 变为标题 `T001: Create project structure`）。
   - **跳过**其ID已存在于上一步中的现有问题集中的任何任务，并报告它（例如，`T001 already has an issue, skipping`）。
   - 仅为尚无匹配问题的任务创建问题。

> [!CAUTION]
> UNDER NO CIRCUMSTANCES EVER CREATE ISSUES IN REPOSITORIES THAT DO NOT MATCH THE REMOTE URL

## 执行后检查

**检查扩展钩子（任务到问题转换后）**：
检查项目根目录中是否存在`.specify/extensions.yml`。
- 如果存在，则读取它并查找 `hooks.after_taskstoissues` 键下的条目
- 如果 YAML 无法解析或无效，请不要静默跳过：告诉用户无法读取 `.specify/extensions.yml`（包括解析器错误）并且没有检查钩子，包括在那里注册的任何强制 (`optional: false`) 钩子，然后正常继续
- 过滤掉 `enabled` 明确为 `false` 的钩子。默认情况下，将没有 `enabled` 字段的挂钩视为启用。
- 对于每个剩余的钩子，**不**尝试解释或评估钩子`condition`表达式：
  - 如果钩子没有 `condition` 字段，或者是 null/empty，则将钩子视为可执行文件
  - 如果挂钩定义非空 `condition`，则跳过挂钩并将条件评估留给 HookExecutor 实现
- 对于每个可执行挂钩，根据其 `optional` 标志输出以下内容：
  - **可选挂钩**（`optional: true`）：
    ```
    ## Extension Hooks

    **Optional Hook**: {extension}
    Command: `/{command}`
    Description: {description}

    Prompt: {prompt}
    To execute: `/{command}`
    ```
  - **强制挂钩**（`optional: false`）：
    ```
    ## Extension Hooks

    **Automatic Hook**: {extension}
    Executing: `/{command}`
    EXECUTE_COMMAND: {command}
    ```
在发出上面的块之后，您 MUST 实际上调用了钩子并等待它完成然后再继续。运行它的方式与您自己在此 agent/session 中运行命令的方式相同（调用可能与上面显示的文字 `{command}` id 不同，例如技能模式 agent 将其运行为 `/skill:speckit-...` 或 `$speckit-...`）。单独发出块不会运行钩子。
- 如果没有注册钩子或者`.specify/extensions.yml`不存在，则静默跳过


## 原文对照

<details>
<summary>展开完整原文</summary>

## User Input

```text
$ARGUMENTS
```

You **MUST** consider the user input before proceeding (if not empty).

## Pre-Execution Checks

**Check for extension hooks (before tasks-to-issues conversion)**:
- Check if `.specify/extensions.yml` exists in the project root.
- If it exists, read it and look for entries under the `hooks.before_taskstoissues` key
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

1. Run `{SCRIPT}` from repo root and parse FEATURE_DIR, TASKS and the AVAILABLE_DOCS list. All paths must be absolute. For single quotes in args like "I'm Groot", use escape syntax: e.g 'I'\''m Groot' (or double-quote if possible: "I'm Groot").
1. **IF EXISTS**: Load `/memory/constitution.md` for project principles and governance constraints.
1. From the executed script, extract the path to **tasks** (the `TASKS` value).
1. Get the Git remote by running:

```bash
git config --get remote.origin.url
```

> [!CAUTION]
> ONLY PROCEED TO NEXT STEPS IF THE REMOTE IS A GITHUB URL

1. **Fetch existing issues for deduplication**: Before creating anything, build the set of task IDs you are about to process from `tasks.md` (each is a `T` followed by **at least** three digits, e.g. `T001` — `__SPECKIT_COMMAND_CONVERGE__` assigns new IDs with `T{M+1:03d}`, which is a floor rather than a cap, so once a file has more than 999 tasks the IDs are four digits or longer). Then use the GitHub MCP server's `list_issues` tool to look for issues that already cover those IDs. Do not pass a `state` value, since omitting it makes the tool return both open and closed issues. Request `perPage: 100` to keep the number of calls down, and since the tool uses cursor-based pagination, request pages with the `after` parameter (using the `endCursor` from the previous response). For each issue title, match it against the task ID pattern `\bT\d{3,}\b` (the `{3,}` accepts four-digit and longer IDs — with `\d{3}` a title containing `T1000` would not match at all, because the trailing `\b` cannot fall between two digits, so that task would be silently neither deduplicated nor created; word boundaries still stop a token like `ST001` from matching, and force the whole digit run to be consumed so `T100` can never match inside `T1000`; this also recognises titles written as `T001 ...`, `T001: ...` or `[T001] ...`) and, when it matches one of your task IDs, mark that ID as already having an issue. Stop paginating as soon as every task ID has been matched, or when there are no more pages, so you do not keep fetching the whole repository's issue history once all task IDs are accounted for. This bounds the number of calls on repos with large issue histories and still prevents duplicates when the command is re-run after `tasks.md` is regenerated or the skill is re-invoked.
1. For each task in the list, use the GitHub MCP server to create a new issue in the repository that is representative of the Git remote. Task lines in `tasks.md` start with a markdown checkbox, so first strip the leading `- [ ]` (and any `[P]` / `[US#]` markers) to recover the task ID and its description. Create the issue with a single canonical title of the form `T001: <description>`, with the ID written once followed by the task description (for example, the line `- [ ] T001 Create project structure` becomes the title `T001: Create project structure`).
   - **Skip** any task whose ID is already present in the set of existing issues from the previous step, and report it (for example, `T001 already has an issue, skipping`).
   - Only create issues for tasks that do not yet have a matching issue.

> [!CAUTION]
> UNDER NO CIRCUMSTANCES EVER CREATE ISSUES IN REPOSITORIES THAT DO NOT MATCH THE REMOTE URL

## Post-Execution Checks

**Check for extension hooks (after tasks-to-issues conversion)**:
Check if `.specify/extensions.yml` exists in the project root.
- If it exists, read it and look for entries under the `hooks.after_taskstoissues` key
- If the YAML cannot be parsed or is invalid, do not skip silently: tell the user that `.specify/extensions.yml` could not be read (include the parser error) and that no hooks were checked, including any mandatory (`optional: false`) hooks registered there, then continue normally
- Filter out hooks where `enabled` is explicitly `false`. Treat hooks without an `enabled` field as enabled by default.
- For each remaining hook, do **not** attempt to interpret or evaluate hook `condition` expressions:
  - If the hook has no `condition` field, or it is null/empty, treat the hook as executable
  - If the hook defines a non-empty `condition`, skip the hook and leave condition evaluation to the HookExecutor implementation
- For each executable hook, output the following based on its `optional` flag:
  - **Optional hook** (`optional: true`):
    ```
    ## Extension Hooks

    **Optional Hook**: {extension}
    Command: `/{command}`
    Description: {description}

    Prompt: {prompt}
    To execute: `/{command}`
    ```
  - **Mandatory hook** (`optional: false`):
    ```
    ## Extension Hooks

    **Automatic Hook**: {extension}
    Executing: `/{command}`
    EXECUTE_COMMAND: {command}
    ```
    After emitting the block above you MUST actually invoke the hook and wait for it to finish before continuing. Run it the same way you would run the command yourself in this agent/session (the invocation may differ from the literal `{command}` id shown above, e.g. a skills-mode agent runs it as `/skill:speckit-...` or `$speckit-...`). Emitting the block alone does not run the hook.
- If no hooks are registered or `.specify/extensions.yml` does not exist, skip silently


</details>
