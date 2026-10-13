# 让 Claude 持续推进到目标完成

介绍用目标完成条件驱动持续工作，涵盖设置、查看、清除和恢复目标、非交互运行，并比较循环机制和结束条件。

- 来源：Anthropic／Claude Code
- 资料类型：存档文档
- 文档时间：官网现页更新：[2026-10-09](<https://code.claude.com/docs/en/goal>)；本地抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://code.claude.com/docs/en/goal.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude Code/让 Claude 持续推进到目标完成.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

> ## 文档索引
> 在以下位置获取完整的文档索引： https://code.claude.com/docs/llms.txt
> 在进一步探索之前使用此文件来发现所有可用页面。

# 继续 Claude 朝着目标努力

> 使用/goal设置完成条件，并且Claude继续工作，直到满足条件、模型判断它不可能，或者您必须修复的错误才能清除目标。

`/goal` 命令设置完成条件，并且 Claude 会继续朝着该条件努力，而无需您提示每一步。每转一圈后，模型都会检查条件是否成立。如果模型判断尚未满足，Claude将开始另一个回合，而不是将控制权返回给你。一旦满足条件，如果模型判断条件不可能满足，或者如果转弯失败[，则目标会自动清除，您必须修复](<#你必须纠正的错误明确目标>)。

使用具有可验证最终状态的实质性工作目标：

* 将模块迁移到新的 API，直到每个调用站点都编译并测试通过
* 实施设计文档，直到满足所有验收标准
* 将大文件拆分为重点模块，直到每个模块都低于大小预算
* 处理标记的问题积压，直到队列为空

## 比较保持会话运行的方法

三种方法可以使当前会话在提示之间保持运行。根据下一轮应该开始的内容进行选择：

|方法|下一回合开始时间|停止时|
| :- | :- | :- |
| `/goal` |上一轮结束，或者在交互式会话中，[空闲签到](<#背景工作推迟评估>)或[自动重试](<#其他错误重试或暂停目标>)到期|模型确认满足条件或判断不可能，或者转弯失败[，您必须修复错误](<#你必须纠正的错误明确目标>)，或者运行[`/goal clear`](<#明确一个目标>)|
| [`/loop`](<https://code.claude.com/docs/en/scheduled-tasks#run-a-prompt-repeatedly-with-%2Floop>) |时间间隔过去|你停止它，或者Claude决定工作完成|
|[止动钩](<https://code.claude.com/docs/en/hooks-guide#prompt-based-hooks>)|上一回合结束|您自己的脚本或提示决定|

`/goal` 和 Stop 钩子在每回合后都会触发。 `/goal` 是会话范围的快捷方式：您键入一个条件，它仅对当前会话有效。 Stop 挂钩存在于您的设置文件中，适用于其范围内的每个会话，并且可以运行脚本进行确定性检查或提示模型评估的检查。

[自动模式](<https://code.claude.com/docs/en/auto-mode-config>)本身批准单轮内的工具调用，但不会启动新的工具调用。 Claude判断完成工作时停止。 `/goal` 添加了一个单独的评估器，该评估器会在每回合后检查您的状况，因此完成情况是由新模型而不是完成工作的模型决定的。两者是互补的：自动模式删除每工具提示，而 `/goal` 删除每回合提示。



**提示：**

上述方法使当前会话保持运行。您还可以安排独立于任何开放会话运行的工作，例如夜间测试或早晨分类。有关云例程和桌面计划任务，请参阅[计划选项](<https://code.claude.com/docs/en/scheduled-tasks#compare-scheduling-options>)。


## 使用`/goal`

每个会话可以激活一个目标。相同的命令根据参数设置、检查和清除它。

### 设定目标

运行 `/goal`，然后运行您想要满足的条件。如果某个目标已处于活动状态，则新目标将取代它。

```text theme={null}
/goal all tests in test/auth pass and the lint step is clean
```

设定目标立即开始回合，以条件本身作为指令。您不需要发送单独的提示。当目标处于活动状态时，`◎ /goal active` 指示器会显示目标已运行的时间。

目标不会改变您的许可模式。要让目标轮流在无人值守的情况下运行，请在 [ 自动模式 ](<https://code.claude.com/docs/en/auto-mode-config>) 下运行 `/goal`。在 [ 手动模式 ](<https://code.claude.com/docs/en/permission-modes>) 中，Claude 仍然会在您的设置不允许的工具调用之前询问，例如上面的测试命令。

当目标处于活动状态时，记录会显示评估器返回的每个结论，您可以按 Ctrl+O 查看其背后的原因。状态视图还显示最近的原因，因此您可以看到 Claude 下一步正在做什么。

### 写出有效条件

[评估者](<#评估如何运作>)根据Claude在对话中出现的内容来判断你的状况。它不会独立运行命令或读取文件，因此请按照 Claude 自己的输出可以演示的方式编写条件。 “`test/auth` 中的所有测试都通过”之所以有效，是因为 Claude 运行测试并且结果出现在记录中供评估者阅读。

一个在很多回合中都成立的条件通常有：

* **一个可测量的最终状态**：测试结果、构建退出代码、文件计数、空队列
* **规定的检查**：Claude应如何证明它，例如“`npm test`退出0”或“`git status`是干净的”
* **重要的约束**：途中不得更改的任何内容，例如“没有修改其他测试文件”

条件最多可为 4,000 个字符。

要限制目标运行的时间，请在条件中包含回合或时间子句，例如 `or stop after 20 turns`。 Claude 每回合都会报告该条款的进度，评估者会根据对话进行判断。

### 检查状态

不带参数运行 `/goal` 以查看当前状态。

```text theme={null}
/goal
```

如果目标处于活动状态，则状态显示：

* 条件
* 已经运行了多长时间
* 已评估了多少转
* 当前代币支出
* 评估人最近的理由

第一次评估运行后，会出现轮次计数和最近的原因。

如果没有活动目标，但在会话早期已实现目标，则状态会显示已实现的条件及其持续时间、回合数和代币支出。

### 明确一个目标

运行 `/goal clear` 以在活动目标解决之前将其删除。

```text theme={null}
/goal clear
```

Claude 打印 `Goal cleared:`，后跟确认条件，如果没有任何活动，则打印 `No goal set`。

`stop`、`off`、`reset`、`none` 和 `cancel` 被接受作为 `clear` 的别名。运行 `/clear` 开始新对话也会删除任何活动目标。

### 带着积极的目标继续前进

当您恢复会话时，Claude Code 会恢复会话结束时仍处于活动状态的目标。 Claude Code 在每个恢复路由上恢复它：`--continue`、`--resume` 以及会话 ID、名称或 [ 转录文件路径 ](<https://code.claude.com/docs/en/sessions#resume-a-session>) 和 [ 会话选择器](<https://code.claude.com/docs/en/sessions#use-the-session-picker>)。在v2.1.239之前，Claude Code在除`claude --resume`选择器之外的所有路线上恢复了目标。

Claude Code 延续该条件，但重置回合计数、计时器和令牌支出基线。它不会恢复已经实现或清除的目标。

### 非交互运行

`/goal` 在 [ 非交互模式 ](<https://code.claude.com/docs/en/headless>) 中、在 [ 桌面应用程序 ](<https://code.claude.com/docs/en/desktop>) 中以及通过 [ 远程控制 ](<https://code.claude.com/docs/en/remote-control>) 工作。使用 `-p` 设置目标，在一次调用中运行循环直至完成：

```bash theme={null}
claude -p "/goal CHANGELOG.md has an entry for every PR merged this week"
```

使用默认文本输出，Claude 的最终响应会在循环结束时打印，因此运行多次的目标可能会被卡住。添加 `--output-format stream-json --verbose` 以在循环运行时发出每条消息。

使用 Ctrl+C 中断进程，以在非交互式目标解决之前停止它。

## 评估如何运作

`/goal` 是会话范围的 [ 基于提示的 Stop 挂钩 ](<https://code.claude.com/docs/en/hooks#prompt-based-hooks>) 的包装器。每次 Claude 完成一轮时，Claude Code 都会将迄今为止的条件和对话发送到您配置的 [ 小型快速模型 ](<https://code.claude.com/docs/en/model-config>)。该模型返回三个判决之一，每个判决都有一个简短的原因：

* **尚未满足**：Claude继续工作，并将原因作为下一轮的指导。
* **Met**：Claude Code清除目标并在成绩单中记录已实现的条目。
* **不可能**：评估者判断条件永远无法满足。 Claude Code 清除目标并在记录中记录失败条目及其原因。您不需要自己清除它。

如果 Claude 一直回答评估器而没有取得进展（连续几圈没有使用工具），Claude Code 会停止循环，打印警告，并将控制权返回给您，目标仍然设定。评估将在您下一次提示后恢复。 [hooks 指南](<https://code.claude.com/docs/en/hooks-guide#stop-hook-hits-the-block-cap>) 解释了底层机制。

### 当转弯失败时

当回合失败时，如果您必须修复错误，Claude Code 会清除目标。在发生任何其他错误之后，目标保持不变。

#### 你必须纠正的错误明确目标

如果转弯因错误而失败，并且在修复之前不会清除该错误，Claude Code 会清除目标并打印一条警告，指出原因。警告以 `Goal cleared after an unrecoverable error` 开头，以 `Run /goal again to continue` 结束。解决原因，然后[用`/goal <condition>`再次设定目标](<#设定目标>)。明确目标的四种失败：

* 当 Claude Code 管理自己的凭据时，身份验证失败。当主机为您管理它们时，例如桌面应用程序或 [ 云会话 ](<https://code.claude.com/docs/en/claude-code-on-the-web>)，Claude Code 会使目标保持活动状态，因为主机会自行恢复访问。
* 信用余额耗尽
* [自动压缩](<https://code.claude.com/docs/en/model-config#set-the-auto-compact-window>)无法清除的上下文溢出
* 不可用的型号

#### 其他错误重试或暂停目标

在任何其他失败之后，目标仍保持不变。在 Claude Code v2.1.269 或更高版本上的交互式会话中，Claude Code 还会打印一行，指出原因，并自行重试或等待您：

* **重试**：在发生倾向于自行清除的故障（例如服务器过载或连接断开）后，以`Goal still active`开头的通知显示下一次尝试之前的等待时间。三次自动重试后，目标会暂停。
* **暂停**：在重试只会重复的失败之后，例如API速率限制，claude.ai [使用限制](<https://code.claude.com/docs/en/errors#youve-hit-your-session-limit>)，或结束回合的挂钩，以`Goal paused`开头的通知命名原因。如果会话[在使用限制重置](<https://code.claude.com/docs/en/interactive-mode#wait-for-a-usage-limit-to-reset>)时等待自动继续，则Claude将恢复实现目标。

随时发送消息即可立即开始下一回合。要关闭自动重试，请将 [`CLAUDE_CODE_GOAL_CHECKIN_MINUTES`](<https://code.claude.com/docs/en/env-vars>) 设置为 `0`，这也会关闭 [ 签入 ](<#背景工作推迟评估>)。

### 背景工作推迟评估

如果回合结束时子代理或后台 shell 命令仍在运行，则 Claude Code 会跳过该回合的评估。它在下一轮结束时进行评估，该轮结束时没有后台工作运行。当后台工作完成后，【Claude Code】将结果下发给【Claude】作为新一轮，因此您无需提示。

一旦后台工作让目标等待 30 分钟，就该签到了。在签入过程中，Claude Code 列出正在运行的任务，并要求 Claude 读取其输出，如果它们正在进行，则继续等待，并修复或停止任何卡住的任务。首次签到后，Claude Code 在以后每次签到之前等待的时间是第一次签到的两倍，最长为第一次间隔的四倍：默认情况下，首次签到后 1 小时，然后每隔 2 小时等待。 Claude Code 通过以下两种方式之一提供到期签到（包括第一个签到）：

* **当一个回合结束时**：Claude Code在下一个回合结束时交付签到，该回合结束时工作仍在运行。在非交互式会话中，例如以 `-p` 开始的会话，这是 Claude Code 提供签到的唯一方式。
* **当会话空闲时**：在交互式会话中，Claude Code也会自行启动轮次以交付签到，而不是等待您的下一个提示。如果后台工作已停止且未报告结果，Claude Code会要求Claude继续实现目标。 Claude Code 在提示之间每个目标最多启动 3 次空闲签到。在第三次空闲签到中，Claude Code 表示空闲签到将暂停，直到您发送另一个提示。在v2.1.246之前，空闲签到没有上限。空闲签到需要 Claude Code v2.1.236 或更高版本。

在v2.1.239之前，只有空闲签到才会以这种方式退出；在第一个时间间隔重复在轮次结束时交付的签到。

要更改第一个间隔，请设置 [`CLAUDE_CODE_GOAL_CHECKIN_MINUTES`](<https://code.claude.com/docs/en/env-vars>)。 Claude Code 使用您的值代替 30 分钟间隔，并用它来缩放后面的间隔。将其设置为`0`以关闭签入和[自动重试](<#其他错误重试或暂停目标>)。

签到需要 Claude Code v2.1.234 或更高版本。

### 评估模型和成本

要在不同模型上进行评估，请设置 [`ANTHROPIC_DEFAULT_HAIKU_MODEL`](<https://code.claude.com/docs/en/model-config#environment-variables>)。



**注意：**

Claude Code 在使用小型快速模型的任何地方都显示为 `ANTHROPIC_DEFAULT_HAIKU_MODEL`，不仅用于 `/goal` 评估。当您设置它时，Claude Code 还会将 [`haiku` 别名 ](<https://code.claude.com/docs/en/model-config#model-aliases>) 解析为该模型，并在其上运行 [ 后台功能 ](<https://code.claude.com/docs/en/costs#background-token-usage>)，例如对话摘要。


评估器在您的会话配置的任何提供商上运行。它不调用工具，因此只能判断对话中已经出现的内容Claude。



**注释：**

评估代币按为您的提供商配置的小型快速模型计费，与主轮支出相比通常可以忽略不计。


## 要求

Claude Code 使 `/goal` 可以在同一个条件下使用 [工作区信任规则作为设置文件中的挂钩](<https://code.claude.com/docs/en/permissions#what-runs-before-you-trust-a-folder>)，因为评估器是钩子系统的一部分。 `/goal` 也无法使用时 [`disableAllHooks`](<https://code.claude.com/docs/en/hooks#disable-or-remove-hooks>) 是 `true` 设置优先级之后，或者当 [`allowManagedHooksOnly`](<https://code.claude.com/docs/en/settings-reference#allowmanagedhooksonly>) 在托管设置中设置。在每种情况下，该命令都会告诉您原因，而不是默默地什么也不做。

## 另请参阅

* [使用 `/loop`](<https://code.claude.com/docs/en/scheduled-tasks#run-a-prompt-repeatedly-with-%2Floop>) 重复运行提示：按时间间隔重新运行，而不是针对条件重新运行
* [基于提示的钩子](<https://code.claude.com/docs/en/hooks-guide#prompt-based-hooks>)：当您需要自定义评估逻辑时编写自己的停止钩子
* [自动模式](<https://code.claude.com/docs/en/auto-mode-config>)：自动批准工具调用，以便每个目标回合在无人值守的情况下运行
* [计划比较](<https://code.claude.com/docs/en/scheduled-tasks#compare-scheduling-options>)：按独立于任何打开会话的计划运行工作


## 原文对照

<details>
<summary>展开完整原文</summary>

> ## Documentation Index
> Fetch the complete documentation index at: https://code.claude.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Keep Claude working toward a goal

> Set a completion condition with /goal and Claude keeps working until it's met, a model judges it impossible, or an error you have to fix clears the goal.

The `/goal` command sets a completion condition and Claude keeps working toward it without you prompting each step. After each turn, a model checks whether the condition holds. If the model judges it not yet met, Claude starts another turn instead of returning control to you. The goal clears automatically once the condition is met, if the model judges the condition impossible to satisfy, or if a turn fails on [an error you have to fix](<#errors-you-have-to-fix-clear-the-goal>).

Use a goal for substantial work with a verifiable end state:

* Migrating a module to a new API until every call site compiles and tests pass
* Implementing a design doc until all acceptance criteria hold
* Splitting a large file into focused modules until each is under a size budget
* Working through a labeled issue backlog until the queue is empty

## Compare ways to keep a session running

Three approaches keep the current session running between prompts. Pick based on what should start the next turn:

| Approach | Next turn starts when | Stops when |
| :- | :- | :- |
| `/goal` | The previous turn finishes, or, in an interactive session, an [idle check-in](<#background-work-defers-evaluation>) or an [automatic retry](<#other-errors-retry-or-pause-the-goal>) comes due | A model confirms the condition is met or judges it impossible, or a turn fails on [an error you have to fix](<#errors-you-have-to-fix-clear-the-goal>), or you run [`/goal clear`](<#clear-a-goal>) |
| [`/loop`](<https://code.claude.com/docs/en/scheduled-tasks#run-a-prompt-repeatedly-with-%2Floop>) | A time interval elapses | You stop it, or Claude decides the work is done |
| [Stop hook](<https://code.claude.com/docs/en/hooks-guide#prompt-based-hooks>) | The previous turn finishes | Your own script or prompt decides |

`/goal` and a Stop hook both fire after every turn. `/goal` is a session-scoped shortcut: you type a condition and it's active for the current session only. A Stop hook lives in your settings file, applies to every session in its scope, and can run a script for deterministic checks or a prompt for model-evaluated ones.

[Auto mode](<https://code.claude.com/docs/en/auto-mode-config>) on its own approves tool calls within a single turn but doesn't start a new one. Claude stops when it judges the work done. `/goal` adds a separate evaluator that checks your condition after every turn, so completion is decided by a fresh model rather than the one doing the work. The two are complementary: auto mode removes per-tool prompts, and `/goal` removes per-turn prompts.



**提示：**

  The approaches above keep the current session running. You can also schedule work that runs independent of any open session, such as nightly tests or morning triage. See [scheduling options](<https://code.claude.com/docs/en/scheduled-tasks#compare-scheduling-options>) for cloud routines and desktop scheduled tasks.


## Use `/goal`

One goal can be active per session. The same command sets, checks, and clears it depending on the argument.

### Set a goal

Run `/goal` followed by the condition you want satisfied. If a goal is already active, the new one replaces it.

```text theme={null}
/goal all tests in test/auth pass and the lint step is clean
```

Setting a goal starts a turn immediately, with the condition itself as the directive. You don't need to send a separate prompt. While the goal is active, a `◎ /goal active` indicator shows how long the goal has been running.

A goal doesn't change your permission mode. To let goal turns run unattended, run `/goal` in [auto mode](<https://code.claude.com/docs/en/auto-mode-config>). In [Manual mode](<https://code.claude.com/docs/en/permission-modes>), Claude still asks before tool calls that your settings don't already allow, such as the test command above.

While the goal is active, the transcript shows each verdict the evaluator returns, and you can press Ctrl+O to see the reason behind it. The status view also shows the most recent reason, so you can see what Claude is working toward next.

### Write an effective condition

The [evaluator](<#how-evaluation-works>) judges your condition against what Claude has surfaced in the conversation. It doesn't run commands or read files independently, so write the condition as something Claude's own output can demonstrate. "All tests in `test/auth` pass" works because Claude runs the tests and the result appears in the transcript for the evaluator to read.

A condition that holds up across many turns usually has:

* **One measurable end state**: a test result, a build exit code, a file count, an empty queue
* **A stated check**: how Claude should prove it, such as "`npm test` exits 0" or "`git status` is clean"
* **Constraints that matter**: anything that must not change on the way there, such as "no other test file is modified"

The condition can be up to 4,000 characters.

To bound how long a goal runs, include a turn or time clause in the condition, such as `or stop after 20 turns`. Claude reports progress against that clause each turn and the evaluator judges it from the conversation.

### Check status

Run `/goal` with no arguments to see the current state.

```text theme={null}
/goal
```

If a goal is active, the status shows:

* The condition
* How long it has been running
* How many turns have been evaluated
* The current token spend
* The evaluator's most recent reason

The turn count and the most recent reason appear after the first evaluation has run.

If no goal is active but one was achieved earlier in the session, the status shows the achieved condition along with its duration, turn count, and token spend.

### Clear a goal

Run `/goal clear` to remove an active goal before it resolves.

```text theme={null}
/goal clear
```

Claude prints `Goal cleared:` followed by the condition to confirm, or `No goal set` if nothing was active.

`stop`, `off`, `reset`, `none`, and `cancel` are accepted as aliases for `clear`. Running `/clear` to start a new conversation also removes any active goal.

### Resume with an active goal

When you resume a session, Claude Code restores a goal that was still active when the session ended. Claude Code restores it on every resume route: `--continue`, `--resume` with a session ID, name, or [transcript file path](<https://code.claude.com/docs/en/sessions#resume-a-session>), and the [session picker](<https://code.claude.com/docs/en/sessions#use-the-session-picker>). Before v2.1.239, Claude Code restored the goal on every route except the `claude --resume` picker.

Claude Code carries the condition over but resets the turn count, timer, and token-spend baseline. It doesn't restore a goal that was already achieved or cleared.

### Run non-interactively

`/goal` works in [non-interactive mode](<https://code.claude.com/docs/en/headless>), in the [desktop app](<https://code.claude.com/docs/en/desktop>), and through [Remote Control](<https://code.claude.com/docs/en/remote-control>). Setting a goal with `-p` runs the loop to completion in a single invocation:

```bash theme={null}
claude -p "/goal CHANGELOG.md has an entry for every PR merged this week"
```

With the default text output, Claude's final response prints when the loop ends, so a goal that runs many turns can look stuck. Add `--output-format stream-json --verbose` to emit each message as the loop runs.

Interrupt the process with Ctrl+C to stop a non-interactive goal before it resolves.

## How evaluation works

`/goal` is a wrapper around a session-scoped [prompt-based Stop hook](<https://code.claude.com/docs/en/hooks#prompt-based-hooks>). Each time Claude finishes a turn, Claude Code sends the condition and the conversation so far to your configured [small fast model](<https://code.claude.com/docs/en/model-config>). The model returns one of three verdicts, each with a short reason:

* **Not yet met**: Claude keeps working and takes the reason as guidance for the next turn.
* **Met**: Claude Code clears the goal and records an achieved entry in the transcript.
* **Impossible**: the evaluator judged that the condition can never be satisfied. Claude Code clears the goal and records a failed entry in the transcript along with the reason. You don't need to clear it yourself.

If Claude keeps answering the evaluator without making progress (no tool use for several turns in a row), Claude Code stops the loop, prints a warning, and returns control to you with the goal still set. Evaluation resumes after your next prompt. The [hooks guide](<https://code.claude.com/docs/en/hooks-guide#stop-hook-hits-the-block-cap>) explains the underlying mechanism.

### When a turn fails

When a turn fails, Claude Code clears the goal if the error is one you have to fix. After any other error the goal stays set.

#### Errors you have to fix clear the goal

If a turn fails on an error that won't clear until you fix it, Claude Code clears the goal and prints a warning naming the cause. The warning starts with `Goal cleared after an unrecoverable error` and ends with `Run /goal again to continue`. Fix the cause, then [set the goal again](<#set-a-goal>) with `/goal <condition>`. Four kinds of failure clear the goal:

* An authentication failure, when Claude Code manages its own credentials. When a host manages them for you, such as the desktop app or a [cloud session](<https://code.claude.com/docs/en/claude-code-on-the-web>), Claude Code leaves the goal active because the host restores access on its own.
* An exhausted credit balance
* A context overflow that [auto-compaction](<https://code.claude.com/docs/en/model-config#set-the-auto-compact-window>) couldn't clear
* A model that isn't available

#### Other errors retry or pause the goal

After any other failure the goal stays set. In an interactive session on Claude Code v2.1.269 or later, Claude Code also prints a line naming the cause and either retries on its own or waits for you:

* **Retry**: after a failure that tends to clear on its own, such as an overloaded server or a dropped connection, a notice starting with `Goal still active` shows the wait before the next attempt. After three automatic retries, the goal pauses instead.
* **Pause**: after a failure that a retry would only repeat, such as an API rate limit, a claude.ai [usage limit](<https://code.claude.com/docs/en/errors#youve-hit-your-session-limit>), or a hook that ended the turn, a notice starting with `Goal paused` names the cause. If the session is [waiting to continue automatically when a usage limit resets](<https://code.claude.com/docs/en/interactive-mode#wait-for-a-usage-limit-to-reset>), Claude resumes work toward the goal then.

Send a message at any time to start the next turn immediately. To turn automatic retries off, set [`CLAUDE_CODE_GOAL_CHECKIN_MINUTES`](<https://code.claude.com/docs/en/env-vars>) to `0`, which also turns [check-ins](<#background-work-defers-evaluation>) off.

### Background work defers evaluation

If a subagent or a background shell command is still running when a turn ends, Claude Code skips the evaluation for that turn. It evaluates at the end of the next turn that finishes with no background work running. When the background work finishes, Claude Code delivers the result to Claude as a new turn, so you don't have to prompt.

Once background work has kept the goal waiting for 30 minutes, a check-in is due. In the check-in, Claude Code lists the running tasks and asks Claude to read their output, keep waiting if they're progressing, and fix or stop any that are stuck. After the first check-in, Claude Code waits twice as long before each later check-in, up to four times the first interval: with the default, 1 hour after the first check-in, then every 2 hours. Claude Code delivers a due check-in, the first one included, in one of two ways:

* **When a turn ends**: Claude Code delivers the check-in at the end of the next turn that finishes with the work still running. In a non-interactive session, such as one started with `-p`, this is the only way Claude Code delivers check-ins.
* **While the session is idle**: in an interactive session, Claude Code also starts a turn on its own to deliver the check-in instead of waiting for your next prompt. If the background work has stopped without reporting a result, Claude Code asks Claude to continue toward the goal. Claude Code starts at most three idle check-ins per goal between your prompts. In the third idle check-in, Claude Code says that idle check-ins are paused until you send another prompt. Before v2.1.246, idle check-ins were uncapped. Idle check-ins require Claude Code v2.1.236 or later.

Before v2.1.239, only idle check-ins backed off this way; a check-in delivered at a turn end recurred at the first interval.

To change the first interval, set [`CLAUDE_CODE_GOAL_CHECKIN_MINUTES`](<https://code.claude.com/docs/en/env-vars>). Claude Code uses your value in place of the 30-minute interval and scales the later intervals with it. Set it to `0` to turn off check-ins and [automatic retries](<#other-errors-retry-or-pause-the-goal>).

Check-ins require Claude Code v2.1.234 or later.

### Evaluation model and cost

To evaluate on a different model, set [`ANTHROPIC_DEFAULT_HAIKU_MODEL`](<https://code.claude.com/docs/en/model-config#environment-variables>).



**注意：**

  Claude Code reads `ANTHROPIC_DEFAULT_HAIKU_MODEL` everywhere it uses the small fast model, not only for `/goal` evaluation. When you set it, Claude Code also resolves the [`haiku` alias](<https://code.claude.com/docs/en/model-config#model-aliases>) to that model and runs [background functionality](<https://code.claude.com/docs/en/costs#background-token-usage>), such as conversation summarization, on it.


The evaluator runs on whichever provider your session is configured for. It does not call tools, so it can only judge what Claude has already surfaced in the conversation.



**注释：**

  Evaluation tokens are billed on the small fast model configured for your provider and are typically negligible compared to main-turn spend.


## Requirements

Claude Code makes `/goal` available under the same [workspace trust rule as hooks in settings files](<https://code.claude.com/docs/en/permissions#what-runs-before-you-trust-a-folder>), because the evaluator is part of the hooks system. `/goal` is also unavailable when [`disableAllHooks`](<https://code.claude.com/docs/en/hooks#disable-or-remove-hooks>) is `true` after settings precedence applies, or when [`allowManagedHooksOnly`](<https://code.claude.com/docs/en/settings-reference#allowmanagedhooksonly>) is set in managed settings. In each case, the command tells you why instead of silently doing nothing.

## See also

* [Run a prompt repeatedly with `/loop`](<https://code.claude.com/docs/en/scheduled-tasks#run-a-prompt-repeatedly-with-%2Floop>): re-run on a time interval instead of toward a condition
* [Prompt-based hooks](<https://code.claude.com/docs/en/hooks-guide#prompt-based-hooks>): write your own Stop hook when you need custom evaluation logic
* [Auto mode](<https://code.claude.com/docs/en/auto-mode-config>): approve tool calls automatically so each goal turn runs unattended
* [Scheduling comparison](<https://code.claude.com/docs/en/scheduled-tasks#compare-scheduling-options>): run work on a schedule independent of any open session


</details>
