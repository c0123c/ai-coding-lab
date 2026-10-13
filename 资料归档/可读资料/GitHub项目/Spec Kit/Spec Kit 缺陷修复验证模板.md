# Spec Kit 缺陷修复验证模板

指导检查已修复的问题、执行验证及 Hook、保存验证报告；属于命令实现资料。

- 来源：Spec Kit
- 资料类型：存档文档
- 存档版本：`0443760542e7`。
- 文档时间：源码提交（存档版）：[2026-10-08](<https://github.com/github/spec-kit/commit/f545e4c93e230a17093015863fc0af53f9a314ee>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/spec-kit/blob/main/extensions/bug/commands/speckit.bug.test.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Spec Kit/Spec Kit 缺陷修复验证模板.md>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
description: "Validate that a previously fixed bug is resolved and record the verification report"
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 测试错误修复

验证 `__SPECKIT_COMMAND_BUG_FIX__` 记录的修复确实解决了 `__SPECKIT_COMMAND_BUG_ASSESS__` 描述的错误。输出是位于 `.specify/bugs/<slug>/test.md` 的验证报告。

## 用户输入

```text
$ARGUMENTS
```

用户输入应识别要验证的错误。接受以下任何一项：

- `slug=<bug-slug>` 或 `--slug <bug-slug>` 或裸露的 slug 状令牌。
- 包含 slug 的路径（例如 `.specify/bugs/login-timeout/`）。
- **Nothing** — 回到上下文（见下文）。

## 段头分辨率

按此顺序解析 `BUG_SLUG`，在第一个匹配处停止：

1. ** 显式用户输入 ** — 在 `$ARGUMENTS` 中传递的 slug（上述任何形式）。
2. **对话上下文** - 如果当前会话刚刚运行`__SPECKIT_COMMAND_BUG_ASSESS__`或`__SPECKIT_COMMAND_BUG_FIX__`，则它报告的slug是工作slug。重复使用它而无需重新提示。通过检查`.specify/bugs/<slug>/fix.md`是否存在来确认；如果没有，就会失败。
3. **磁盘上的单个候选者** — 列表`.specify/bugs/*/fix.md`。如果恰好有一个错误具有 `fix.md`，请使用它。
4. **消除歧义**：
   - **交互模式**：询问用户要验证哪个错误并列出候选者。
   - **自动模式**：停止并列出候选者错误。不要猜测。

解决后，设置 `BUG_SLUG` 和 `BUG_DIR = .specify/bugs/<BUG_SLUG>`，并在回复中简要说明使用了哪个解决路径（明确/来自上下文/单个候选人/询问）。

## 先决条件

- `BUG_DIR/assessment.md` MUST 存在。
- `BUG_DIR/fix.md` MUST 存在。如果没有，请停止并指示用户先运行`__SPECKIT_COMMAND_BUG_FIX__`。
- 如果`BUG_DIR/test.md`已存在，则询问用户是否覆盖它（交互模式）或拒绝（自动模式）。
- 完整阅读 `assessment.md` 和 `fix.md`，以便您了解：
  - 原始症状和重现步骤（来自`assessment.md`）。
  - 实际的代码更改和添加的测试（来自`fix.md`）。

## 执行前检查

至此，`BUG_SLUG` 和 `BUG_DIR` 已得到解析，因此在此会话中运行的挂钩命令可以从会话中重用它们；没有任何内容会自动传递给它。

**检查扩展钩子（在错误验证之前）**：
- 检查项目根目录中是否存在`.specify/extensions.yml`。
- 如果存在，则读取它并查找 `hooks.before_bug_test` 键下的条目
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

1. **计划验证**
   - 决定哪些检查证明错误已消失：
     - 重新运行评估中的再现步骤（或其自动等效步骤）。
     - 运行修复中添加或更新的测试。
     - 运行涉及更改文件的任何更广泛的回归套件。
   - 确定哪些检查证明没有任何损坏：
     - 已更改模块的现有测试套件。
     - Lint / 类型检查项目是否使用它们。

2. **运行检查**
   - 执行每项计划检查。捕获命令、退出状态和相关输出的简短摘录（最后几行或失败的断言）。
   - 如果检查是破坏性的、依赖于网络的或昂贵的，则跳过它并将其记录为`skipped`并给出原因；未经用户明确同意，请勿运行它。
   - 如果您根本无法运行检查（缺少工具、未配置测试框架），请将其记录为 `not-run` 并给出原因，而不是捏造结果。

3. **判断胜负**
   - 将修复标记为：
     - **已验证** — 所有关键检查均通过，原始症状不再重现。
     - **部分** — 原始症状消失，但出现不相关的回归，或者某些检查没有结论。
     - **失败** - 症状仍然重现或回归套件被修复破坏。
   - 不要过度主张。如果实际并未执行复制（例如，该错误需要生产环境），请明确说明。

4. **撰写验证报告**

使用以下结构写入 `BUG_DIR/test.md`：

   ```markdown
   # Bug Verification: <short title>

   - **Slug**: <BUG_SLUG>
   - **Tested**: <ISO 8601 date>
   - **Assessment**: ./assessment.md
   - **Fix**: ./fix.md
   - **Result**: verified | partial | failed

   ## Summary

   <One or two sentences: does the bug reproduce, did the fix hold, were any regressions found.>

   ## Checks Performed

   | Check | Command / Action | Result | Notes |
   |-------|------------------|--------|-------|
   | Reproduction (post-fix) | <command or manual steps> | pass / fail / skipped / not-run | <short note> |
   | New / updated tests | `<command>` | pass / fail | <short note> |
   | Regression suite | `<command>` | pass / fail / skipped | <short note> |
   | Lint / type-check | `<command>` | pass / fail / skipped | <short note> |

   ## Output Excerpts

   <Short snippets of relevant output (e.g., final summary line of a test run, the failing assertion). Keep it tight — no full logs.>

   ## Residual Risks

   - <known limitation, environment not covered, etc.>

   ## Recommendation

   <One paragraph. Examples:>
   - "Close the bug — verified end-to-end."
   - "Hold — reproduction inconclusive; needs verification in staging."
   - "Reopen — symptom still reproduces; rerun `__SPECKIT_COMMAND_BUG_ASSESS__`."
   ```

## 强制执行后挂钩

**您MUST在向用户报告完成情况之前完成此部分。**

至此，`BUG_SLUG` 和 `BUG_DIR` 已得到解析，并且书面报告可用，因此在此会话中运行的挂钩命令可以从对话中重用它们；没有任何内容会自动传递给它。

检查项目根目录中是否存在`.specify/extensions.yml`。
- 如果不存在，或者`hooks.after_bug_test`下没有注册任何钩子，则跳至完成报告。
- 如果存在，请读取它并查找 `hooks.after_bug_test` 键下的条目。
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

- slug 和 `BUG_DIR/test.md` 路径。
- 结果（`verified`、`partial`、`failed`）。
- 如果结果是 `failed`，建议使用 `test.md` 中捕获的新证据重新运行 `__SPECKIT_COMMAND_BUG_ASSESS__`。

## 护栏

- 该命令MUSTNOT修改源代码。它仅在 `.specify/bugs/<slug>/` 内运行检查和写入。
- 未经确认，切勿覆盖现有的 `test.md`。
- 如果原始评估列出了您并未实际执行的复制品，切勿仅根据测试将修复标记为 `verified` — 降级为 `partial` 并如此说。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Test Bug Fix

Validate that the fix recorded by `__SPECKIT_COMMAND_BUG_FIX__` actually resolves the bug described by `__SPECKIT_COMMAND_BUG_ASSESS__`. The output is a verification report at `.specify/bugs/<slug>/test.md`.

## User Input

```text
$ARGUMENTS
```

The user input should identify the bug to validate. Accept any of:

- `slug=<bug-slug>` or `--slug <bug-slug>` or a bare slug-like token.
- A path that contains the slug (e.g. `.specify/bugs/login-timeout/`).
- **Nothing** — fall back to context (see below).

## Slug Resolution

Resolve `BUG_SLUG` in this order, stopping at the first match:

1. **Explicit user input** — a slug passed in `$ARGUMENTS` (any of the forms above).
2. **Conversation context** — if the current session has just run `__SPECKIT_COMMAND_BUG_ASSESS__` or `__SPECKIT_COMMAND_BUG_FIX__`, the slug it reported is the working slug. Reuse it without re-prompting. Confirm it by checking that `.specify/bugs/<slug>/fix.md` exists; if it does not, fall through.
3. **Single candidate on disk** — list `.specify/bugs/*/fix.md`. If exactly one bug has a `fix.md`, use it.
4. **Disambiguate**:
   - **Interactive mode**: ask the user which bug to validate and list the candidates.
   - **Automated mode**: stop with an error listing the candidates. Do not guess.

Once resolved, set `BUG_SLUG` and `BUG_DIR = .specify/bugs/<BUG_SLUG>`, and briefly state in your reply which resolution path was used (explicit / from context / single candidate / asked).

## Prerequisites

- `BUG_DIR/assessment.md` MUST exist.
- `BUG_DIR/fix.md` MUST exist. If not, stop and instruct the user to run `__SPECKIT_COMMAND_BUG_FIX__` first.
- If `BUG_DIR/test.md` already exists, ask the user whether to overwrite it (interactive mode) or refuse (automated mode).
- Read both `assessment.md` and `fix.md` in full so you know:
  - The original symptom and reproduction steps (from `assessment.md`).
  - The actual code changes and tests added (from `fix.md`).

## Pre-Execution Checks

By this point `BUG_SLUG` and `BUG_DIR` have been resolved, so a hook command run in this session can reuse them from the conversation; nothing is passed to it automatically.

**Check for extension hooks (before bug verification)**:
- Check if `.specify/extensions.yml` exists in the project root.
- If it exists, read it and look for entries under the `hooks.before_bug_test` key
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

1. **Plan the validation**
   - Decide which checks prove the bug is gone:
     - Re-run the reproduction steps from the assessment (or their automated equivalent).
     - Run the tests added or updated in the fix.
     - Run any broader regression suite that touches the changed files.
   - Decide which checks prove nothing was broken:
     - Existing test suites for the changed modules.
     - Lint / type-check if the project uses them.

2. **Run the checks**
   - Execute each planned check. Capture command, exit status, and a short excerpt of relevant output (last few lines, or the failing assertion).
   - If a check is destructive, network-dependent, or expensive, skip it and record it as `skipped` with a reason; do not run it without explicit user consent.
   - If you cannot run a check at all (missing tooling, no test framework configured), record it as `not-run` with a reason instead of fabricating a result.

3. **Judge the outcome**
   - Mark the fix as:
     - **verified** — all critical checks pass and the original symptom no longer reproduces.
     - **partial** — the original symptom is gone but unrelated regressions appeared, or some checks are inconclusive.
     - **failed** — the symptom still reproduces or the regression suite is broken by the fix.
   - Do not over-claim. If reproduction was not actually performed (e.g., the bug required a production environment), say so explicitly.

4. **Write the verification report**

   Write to `BUG_DIR/test.md` using this structure:

   ```markdown
   # Bug Verification: <short title>

   - **Slug**: <BUG_SLUG>
   - **Tested**: <ISO 8601 date>
   - **Assessment**: ./assessment.md
   - **Fix**: ./fix.md
   - **Result**: verified | partial | failed

   ## Summary

   <One or two sentences: does the bug reproduce, did the fix hold, were any regressions found.>

   ## Checks Performed

   | Check | Command / Action | Result | Notes |
   |-------|------------------|--------|-------|
   | Reproduction (post-fix) | <command or manual steps> | pass / fail / skipped / not-run | <short note> |
   | New / updated tests | `<command>` | pass / fail | <short note> |
   | Regression suite | `<command>` | pass / fail / skipped | <short note> |
   | Lint / type-check | `<command>` | pass / fail / skipped | <short note> |

   ## Output Excerpts

   <Short snippets of relevant output (e.g., final summary line of a test run, the failing assertion). Keep it tight — no full logs.>

   ## Residual Risks

   - <known limitation, environment not covered, etc.>

   ## Recommendation

   <One paragraph. Examples:>
   - "Close the bug — verified end-to-end."
   - "Hold — reproduction inconclusive; needs verification in staging."
   - "Reopen — symptom still reproduces; rerun `__SPECKIT_COMMAND_BUG_ASSESS__`."
   ```

## Mandatory Post-Execution Hooks

**You MUST complete this section before reporting completion to the user.**

By this point `BUG_SLUG` and `BUG_DIR` have been resolved and the written report is available, so a hook command run in this session can reuse them from the conversation; nothing is passed to it automatically.

Check if `.specify/extensions.yml` exists in the project root.
- If it does not exist, or no hooks are registered under `hooks.after_bug_test`, skip to the Completion Report.
- If it exists, read it and look for entries under the `hooks.after_bug_test` key.
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

- The slug and `BUG_DIR/test.md` path.
- The result (`verified`, `partial`, `failed`).
- If the result is `failed`, recommend re-running `__SPECKIT_COMMAND_BUG_ASSESS__` with the new evidence captured in `test.md`.

## Guardrails

- This command MUST NOT modify source code. It only runs checks and writes inside `.specify/bugs/<slug>/`.
- Never overwrite an existing `test.md` without confirmation.
- Never mark a fix as `verified` based on tests alone if the original assessment listed a reproduction that you did not actually exercise — downgrade to `partial` and say so.


</details>
