# Spec Kit 缺陷修复命令模板

根据已完成的缺陷评估执行修复、运行前后检查和 Hook，并记录修改结果；属于工作流命令模板。

- 来源：Spec Kit
- 资料类型：存档文档
- 存档版本：`0443760542e7`。
- 文档时间：源码提交（存档版）：[2026-10-08](<https://github.com/github/spec-kit/commit/f545e4c93e230a17093015863fc0af53f9a314ee>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/spec-kit/blob/main/extensions/bug/commands/speckit.bug.fix.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Spec Kit/Spec Kit 缺陷修复命令模板.md>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
description: "Apply the remediation from a bug assessment and record what was changed"
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 修复错误

应用 `__SPECKIT_COMMAND_BUG_ASSESS__` 提出的修复措施，并将更改记录在 `.specify/bugs/<slug>/fix.md` 的修复报告中。此命令**仅**在给定段的评估存在后有效。

## 用户输入

```text
$ARGUMENTS
```

用户输入应该识别要修复的错误。接受以下任何一项：

- `slug=<bug-slug>` 或 `--slug <bug-slug>` 或只是一个类似 slug 的令牌。
- 包含 slug 的路径（例如 `.specify/bugs/login-timeout/`）。
- **Nothing** — 回到上下文（见下文）。

## 段头分辨率

按此顺序解析 `BUG_SLUG`，在第一个匹配处停止：

1. ** 显式用户输入 ** — 在 `$ARGUMENTS` 中传递的 slug（上述任何形式）。
2. **对话上下文** — 如果当前会话刚刚运行`__SPECKIT_COMMAND_BUG_ASSESS__`，则它报告的 slug 是工作 slug。重复使用它而无需重新提示。通过检查`.specify/bugs/<slug>/assessment.md`是否存在来确认；如果没有，就会失败。
3. **磁盘上的单个候选者** — 列表`.specify/bugs/*/assessment.md`。如果恰好找到一个匹配的 `assessment.md`，则使用其父目录中的 slug。
4. **消除歧义**：
   - **交互模式**：询问用户要修复哪个bug并列出候选者。
   - **自动模式**：停止并列出候选者错误。不要猜测。

解决后，设置 `BUG_SLUG` 和 `BUG_DIR = .specify/bugs/<BUG_SLUG>`，并在回复中简要说明使用了哪个解决路径（明确/来自上下文/单个候选人/询问）。

## 先决条件

- `BUG_DIR/assessment.md` MUST 存在。如果没有，请停止并指示用户先运行`__SPECKIT_COMMAND_BUG_ASSESS__`。
- 如果`BUG_DIR/fix.md`已存在，则询问用户是否覆盖它，然后继续（交互模式）或拒绝（自动模式）。
- 完整阅读 `BUG_DIR/assessment.md`。对待其**建议的补救措施**、**可能更改的文件**、**添加或更新的测试**和**风险和注意事项** 部分作为此命令的契约。

## 执行前检查

至此，`BUG_SLUG` 和 `BUG_DIR` 已得到解析，因此在此会话中运行的挂钩命令可以从会话中重用它们；没有任何内容会自动传递给它。

**检查扩展钩子（修复错误之前）**：
- 检查项目根目录中是否存在`.specify/extensions.yml`。
- 如果存在，则读取它并查找 `hooks.before_bug_fix` 键下的条目
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

    Wait for the result of the hook command before proceeding to Execution.
    ```
在发出上面的块之后，您 MUST 实际上调用了钩子并等待它完成然后再继续。运行它的方式与您自己在此 agent/session 中运行命令的方式相同（调用可能与上面显示的文字 `{command}` id 不同，例如技能模式 agent 将其运行为 `/skill:speckit-...` 或 `$speckit-...`）。单独发出块不会运行钩子。
- 如果没有注册钩子或者`.specify/extensions.yml`不存在，则静默跳过

## 执行

1. **确认计划**
   - 根据评估，在 3–6 项目符号中重申您将要更改的内容以及更改的位置。
   - 如果评估的结论是 `invalid`，请停止 — 没有什么需要修复的。告诉用户并退出。
   - 如果结论是 `likely valid, needs reproduction` 并且存在未解决的 `[NEEDS CLARIFICATION]` 项目，请标记它们并询问用户是否以交互模式继续，或以自动模式停止。

2. **应用修复措施**
   - 进行首选补救措施描述的代码更改。保留在评估列出的文件内，除非新发现的证据需要扩大范围（在这种情况下，请在报告中明确记录扩展）。
   - 添加或更新评估中调用的测试，以便错误不会悄无声息地回归。
   - 保持最小的变化——不要重构不相关的代码，不要引入评估不需要的依赖项。
   - 如果您发现评估错误（建议的修复不起作用，根本原因在其他地方），STOP修改代码，在**评估偏差**下的修复报告中记录新发现，并建议重新运行`__SPECKIT_COMMAND_BUG_ASSESS__`。

3. **运行本地检查**
   - 如果项目具有明显的测试命令（例如，`pytest`、`npm test`、`cargo test`），请运行测试来执行更改的路径。捕获pass/fail和按键输出。
   - 未经用户同意，请勿运行破坏性或依赖网络的套件。

4. **编写修复报告**

使用以下结构写入 `BUG_DIR/fix.md`：

   ```markdown
   # Bug Fix: <short title>

   - **Slug**: <BUG_SLUG>
   - **Fixed**: <ISO 8601 date>
   - **Assessment**: ./assessment.md
   - **Status**: applied | partial | not-applied

   ## Summary

   <One or two sentences describing what was changed and why.>

   ## Changes

   | File | Change | Notes |
   |------|--------|-------|
   | `path/to/file.py` | <added / modified / removed> | <short note> |
   | `path/to/test_file.py` | added test | <short note> |

   ## Diff Highlights (optional)

   <Short, illustrative snippets of the most important hunks — not a full diff dump.>

   ## Tests Added or Updated

   - `path/to/test_file.py::test_name` — <what it pins down>

   ## Local Verification

   - Commands run: `<command>` → <result, brief>
   - Manual checks: <what was verified by hand, if anything>

   ## Deviations from Assessment

   <Empty if none. Otherwise, list any places where the actual fix departed from the proposed remediation and why.>

   ## Follow-ups

   - <suggested cleanup, monitoring, doc update, etc.>
   ```

## 强制执行后挂钩

**您MUST在向用户报告完成情况之前完成此部分。**

至此，`BUG_SLUG` 和 `BUG_DIR` 已得到解析，并且书面报告可用，因此在此会话中运行的挂钩命令可以从对话中重用它们；没有任何内容会自动传递给它。

检查项目根目录中是否存在`.specify/extensions.yml`。
- 如果不存在，或者`hooks.after_bug_fix`下没有注册任何钩子，则跳至完成报告。
- 如果存在，请读取它并查找 `hooks.after_bug_fix` 键下的条目。
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

**返回报告**：

- slug 和 `BUG_DIR/fix.md` 路径。
- 状态（`applied`、`partial`、`not-applied`）。
- 建议的下一个步骤：`__SPECKIT_COMMAND_BUG_TEST__ slug=<BUG_SLUG>`。

## 护栏

- 切勿修改项目工作区之外的文件。
- 切勿编辑 `assessment.md` — 这是您正在执行的合同。在**评估偏差**下的`fix.md`中记录分歧。
- 除非评估明确要求，否则切勿删除文件。
- 未经确认，切勿覆盖现有的 `fix.md`。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Fix Bug

Apply the remediation that was proposed by `__SPECKIT_COMMAND_BUG_ASSESS__` and record the changes in a fix report at `.specify/bugs/<slug>/fix.md`. This command is **only** valid after an assessment exists for the given slug.

## User Input

```text
$ARGUMENTS
```

The user input should identify the bug to fix. Accept any of:

- `slug=<bug-slug>` or `--slug <bug-slug>` or just a bare slug-like token.
- A path that contains the slug (e.g. `.specify/bugs/login-timeout/`).
- **Nothing** — fall back to context (see below).

## Slug Resolution

Resolve `BUG_SLUG` in this order, stopping at the first match:

1. **Explicit user input** — a slug passed in `$ARGUMENTS` (any of the forms above).
2. **Conversation context** — if the current session has just run `__SPECKIT_COMMAND_BUG_ASSESS__`, the slug it reported is the working slug. Reuse it without re-prompting. Confirm it by checking that `.specify/bugs/<slug>/assessment.md` exists; if it does not, fall through.
3. **Single candidate on disk** — list `.specify/bugs/*/assessment.md`. If exactly one matching `assessment.md` is found, use the slug from its parent directory.
4. **Disambiguate**:
   - **Interactive mode**: ask the user which bug to fix and list the candidates.
   - **Automated mode**: stop with an error listing the candidates. Do not guess.

Once resolved, set `BUG_SLUG` and `BUG_DIR = .specify/bugs/<BUG_SLUG>`, and briefly state in your reply which resolution path was used (explicit / from context / single candidate / asked).

## Prerequisites

- `BUG_DIR/assessment.md` MUST exist. If it does not, stop and instruct the user to run `__SPECKIT_COMMAND_BUG_ASSESS__` first.
- If `BUG_DIR/fix.md` already exists, ask the user whether to overwrite it before continuing (interactive mode) or refuse (automated mode).
- Read `BUG_DIR/assessment.md` in full. Treat its **Proposed Remediation**, **Files likely to change**, **Tests to add or update**, and **Risks & Considerations** sections as the contract for this command.

## Pre-Execution Checks

By this point `BUG_SLUG` and `BUG_DIR` have been resolved, so a hook command run in this session can reuse them from the conversation; nothing is passed to it automatically.

**Check for extension hooks (before bug fixing)**:
- Check if `.specify/extensions.yml` exists in the project root.
- If it exists, read it and look for entries under the `hooks.before_bug_fix` key
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

    Wait for the result of the hook command before proceeding to Execution.
    ```
    After emitting the block above you MUST actually invoke the hook and wait for it to finish before continuing. Run it the same way you would run the command yourself in this agent/session (the invocation may differ from the literal `{command}` id shown above, e.g. a skills-mode agent runs it as `/skill:speckit-...` or `$speckit-...`). Emitting the block alone does not run the hook.
- If no hooks are registered or `.specify/extensions.yml` does not exist, skip silently

## Execution

1. **Confirm the plan**
   - Restate, in 3–6 bullets, what you are about to change and where, based on the assessment.
   - If the assessment's verdict is `invalid`, stop — there is nothing to fix. Tell the user and exit.
   - If the verdict is `likely valid, needs reproduction` and there are unresolved `[NEEDS CLARIFICATION]` items, flag them and ask the user whether to proceed in interactive mode, or stop in automated mode.

2. **Apply the remediation**
   - Make the code changes described by the preferred remediation. Stay within the files listed by the assessment unless newly discovered evidence requires expanding scope (in which case, log the expansion explicitly in the report).
   - Add or update the tests called out in the assessment so the bug cannot regress silently.
   - Keep the change minimal — do not refactor unrelated code, do not introduce dependencies that the assessment did not call for.
   - If you discover the assessment was wrong (the proposed fix does not work, the root cause is elsewhere), STOP modifying code, document the new finding in the fix report under **Deviations from Assessment**, and recommend re-running `__SPECKIT_COMMAND_BUG_ASSESS__`.

3. **Run local checks**
   - If the project has obvious test commands (e.g., `pytest`, `npm test`, `cargo test`), run the tests that exercise the changed paths. Capture pass/fail and key output.
   - Do not run destructive or network-dependent suites without the user's consent.

4. **Write the fix report**

   Write to `BUG_DIR/fix.md` using this structure:

   ```markdown
   # Bug Fix: <short title>

   - **Slug**: <BUG_SLUG>
   - **Fixed**: <ISO 8601 date>
   - **Assessment**: ./assessment.md
   - **Status**: applied | partial | not-applied

   ## Summary

   <One or two sentences describing what was changed and why.>

   ## Changes

   | File | Change | Notes |
   |------|--------|-------|
   | `path/to/file.py` | <added / modified / removed> | <short note> |
   | `path/to/test_file.py` | added test | <short note> |

   ## Diff Highlights (optional)

   <Short, illustrative snippets of the most important hunks — not a full diff dump.>

   ## Tests Added or Updated

   - `path/to/test_file.py::test_name` — <what it pins down>

   ## Local Verification

   - Commands run: `<command>` → <result, brief>
   - Manual checks: <what was verified by hand, if anything>

   ## Deviations from Assessment

   <Empty if none. Otherwise, list any places where the actual fix departed from the proposed remediation and why.>

   ## Follow-ups

   - <suggested cleanup, monitoring, doc update, etc.>
   ```

## Mandatory Post-Execution Hooks

**You MUST complete this section before reporting completion to the user.**

By this point `BUG_SLUG` and `BUG_DIR` have been resolved and the written report is available, so a hook command run in this session can reuse them from the conversation; nothing is passed to it automatically.

Check if `.specify/extensions.yml` exists in the project root.
- If it does not exist, or no hooks are registered under `hooks.after_bug_fix`, skip to the Completion Report.
- If it exists, read it and look for entries under the `hooks.after_bug_fix` key.
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

**Report back** with:

- The slug and `BUG_DIR/fix.md` path.
- The status (`applied`, `partial`, `not-applied`).
- The next suggested step: `__SPECKIT_COMMAND_BUG_TEST__ slug=<BUG_SLUG>`.

## Guardrails

- Never modify files outside the project workspace.
- Never edit `assessment.md` — it is the contract you are working against. Record disagreements in `fix.md` under **Deviations from Assessment**.
- Never delete files unless the assessment explicitly required it.
- Never overwrite an existing `fix.md` without confirmation.


</details>
