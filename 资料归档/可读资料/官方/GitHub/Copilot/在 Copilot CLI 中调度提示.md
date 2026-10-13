# 在 Copilot CLI 中调度提示

讲解 /every 定期执行与 /after 延迟执行、时间语法、技能调度、管理及会话重开后的行为。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-09-17](<https://github.com/github/docs/commit/f256799b2b9c6c031d661e10c07dfa05c5869616>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/copilot-cli/automate-copilot-cli/schedule-prompts.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/在 Copilot CLI 中调度提示.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Scheduling prompts in {% data variables.copilot.copilot_cli %}
shortTitle: Schedule prompts
allowTitleToDifferFromFilename: true
intro: 'Use the `/every` and `/after` slash commands to submit a prompt to {% data variables.product.prodname_copilot_short %} on a recurring schedule, or after a specified delay.'
versions:
  feature: copilot
contentType: how-tos
docsTeamMetrics:
  - copilot-cli
category:
  - Author and optimize with Copilot # Copilot discovery page
  - Build with Copilot CLI # Copilot CLI bespoke page
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

> [!NOTE]
> `/every` 和 `/after` 命令目前是实验性功能，仅当您使用 `/experimental on` 斜杠命令或 `‑‑experimental` 命令行选项时才可用。

在交互式 {% data variables.copilot.copilot_cli_short %} 会话中，您可以安排自动提交提示。当您希望 {% data variables.product.prodname_copilot_short %} 以常规节奏重复任务或在延迟后执行一次性任务而无需记住手动提交提示时，这非常有用。

为此有两个斜杠命令：

* `/every` — 安排提示以固定间隔或以 {% data variables.product.prodname_copilot_short %} 确定的节奏重复发送 ****。
* `/after` — 安排在指定时间段后发送 ** 一次 ** 提示。

这两个命令仅在交互式 {% data variables.copilot.copilot_cli_short %} 会话中可用：计划仅在创建它们的会话正在运行时触发。有关在没有打开会话时按计划运行 {% data variables.copilot.copilot_cli_short %} 的方法，请参阅本文末尾的 [从外部计划程序运行提示 ](<#从外部调度程序运行提示>)。

## 使用 `/every` 安排重复提示

在交互式 {% data variables.copilot.copilot_cli_short %} 会话中，键入 `/every`，然后键入要提交的提示。要使用固定间隔，请在提示前包含间隔。

```copilot
/every [INTERVAL] PROMPT
```

如果指定时间间隔，则将在时间间隔过后提交提示，然后以相同的节奏再次提交，直到您删除计划条目或结束交互式 CLI 会话。

### 示例

```copilot
/every 1h run the test suite and summarize any new failures
```

```copilot
/every 30m check for new comments on my open pull requests
```

> [!NOTE]
> `/loop` 是 `/every` 的别名。因此，如果您愿意，可以输入 `/loop` 而不是 `/every`，效果是相同的。

### 自定进度安排

如果您使用带提示但没有间隔或时间的 `/every`（例如，`/every keep checking the deployment and let me know once it's healthy`）-{% data variables.product.prodname_copilot_short %} 将创建一个自定进度的计划而不是错误。每次运行后，{% data variables.product.prodname_copilot_short %} 根据任务决定何时再次运行提示，而不是遵循固定的时间间隔，并在确定没有任何内容可观看时自行停止。由于模型会选择节奏，因此自定进度计划的运行频率可能比固定间隔的运行频率更高或更低，因此请密切关注它，如果它运行的次数超出您的预期，请将其删除（如 [ 管理计划提示 ](<#管理预定提示>) 中所述）。

## 使用 `/after` 安排一次提示

输入 `/after`，然后输入延迟和您要提交的提示：

```copilot
/after DELAY PROMPT
```

延迟过后，提示会触发一次，然后从计划列表中删除。

> [！NOTE]
> `/after`不支持自定进度调度。如果您将提示传递给 `/after`，则必须提供延迟或时间。

### 示例

```copilot
/after 30m Give me details of changes to README.md made in the last 30 minutes
```

```copilot
/after 10m Check that the address finder is visible on example.com/register
```

## 安排技能

您可以使用`/every`和`/after`来安排技能。为此，您可以使用斜杠命令显式引用该技能，也可以使用自然语言告诉 {% data variables.product.prodname_copilot_short %} 运行该技能。

> [!NOTE]
> 只能安排用户可调用的技能和内置斜杠命令的子集。启动独立工作的命令（例如 `/plan`、`/review`、`/research` 或 `/security-review`）是可调度的。更改会话或配置（例如，`/model`、`/clear`、`/compact`、`/permissions` 或 `/sandbox`）、仅显示信息（例如 `/usage` 或 `/context`）或管理计划的命令本身（`/every` 和 `/after`）无法安排，并且 {% data variables.product.prodname_copilot_short %} 在您尝试时会拒绝它们。

### 示例

```copilot
/after 2h Use the docx skill to create a new file summarizing recent changes to this repo
```

```copilot
/every 1d /refactor-plan Adjust the architecture of this project to improve the responsiveness of the client UI
```

## 间隔和延迟语法

|后缀|单位|示例|
| ------ | ------- | ------- |
| `s`    |秒| `30s`   |
| `m`    |分钟| `5m`    |
| `h`    |小时| `2h`    |
| `d`    |天| `1d`    |

当您指定数字持续时间时，请始终包含后缀。裸数字（例如 `/every 30 remind me to check for Slack messages`）不会被识别为间隔。

对于固定间隔，最小值为 **10 秒**，最大值为 **1 天**（24 小时）。

您还可以用简单语言描述时间，而不是使用持续时间，例如 `/after at 3pm push the release` 或 `/every day at 9am post the standup`。 {% data variables.product.prodname_copilot_short %} 使用模型来解释该短语，然后从中创建时间表。

## 识别会话中预定的提示

触发计划提示时，{% data variables.product.prodname_copilot_short %} 会在提示前添加 `[Scheduled prompt #4]` 等文本，以将其与您自己键入的提示区分开来。

您可以使用ID取消预约。例如，输入 `stop prompt 4`。

## 管理预定提示

要列出当前会话的活动计划，请键入不带参数的 `/every` 或 `/after`。

要删除计划，请使用键盘上的箭头键在列表中移动并选择要删除的计划，然后按 <kbd>x</kbd>。

按<kbd>Esc</kbd>退出日程列表。

## 关闭并重新打开会话时会发生什么

计划提示的范围仅限于创建它们的会话，并且仅在该会话运行时触发。

当您重新打开会话时（使用 `--continue` 或 `--resume` 命令行选项），计划将恢复。对于以固定间隔创建的循环计划，下一次运行之前的等待时间从您重新打开会话的那一刻开始计算。

在您关闭会话之前尚未触发的 `/after` 计划将保留其原始目标时间，而不是重新启动延迟。如果在会话关闭时过了该时间，则在您重新打开会话时会立即提交提示。

## 从外部调度程序运行提示

`/every` 和 `/after` 命令仅在交互式 {% data variables.copilot.copilot_cli_short %} 会话打开时运行。如果您希望即使在没有活动会话的情况下也按计划运行提示，您可以从外部计划程序运行 {% data variables.copilot.copilot_cli_short %}，例如 macOS 或 Linux 上的 **cron**，或 Windows 上的 **任务计划程序 **。

要以编程方式运行 {% data variables.copilot.copilot_cli_short %}，请使用语法 `copilot -p "YOUR PROMPT"`。 CLI 处理您的提示而不显示交互界面，然后退出。

这对于以下任务很有用：

* **夜间维护** - 例如，每晚针对分支上的最新更改运行测试套件，并将摘要发布到跟踪问题。
* **定期依赖项检查** - 例如，打开每周拉取请求来更新依赖项并根据结果运行测试套件。
* **预定报告** - 例如，生成分配给您的新问题或拉取请求的每日摘要，并通过电子邮件发送或发布到聊天频道。

欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/automate-copilot-cli/run-cli-programmatically>)。

## 进一步阅读

* [AUTOTITLE](<https://github.com/copilot/reference/copilot-cli-reference/cli-command-reference#slash-commands-in-the-interactive-interface>)


## 原文对照

<details>
<summary>展开完整原文</summary>

> [!NOTE]
> The `/every` and `/after` commands are currently experimental features and are only available if you have used the `/experimental on` slash command, or the `‑‑experimental` command line option.

In an interactive {% data variables.copilot.copilot_cli_short %} session you can schedule a prompt to be submitted automatically. This is useful when you want {% data variables.product.prodname_copilot_short %} to repeat a task at a regular cadence or to perform a one-off task after a delay, without you having to remember to submit the prompt manually.

There are two slash commands for this:

* `/every` — schedule a prompt to be sent **repeatedly**, either at a fixed interval or at a cadence determined by {% data variables.product.prodname_copilot_short %}.
* `/after` — schedule a prompt to be sent **once**, after a specified period of time.

Both commands are only available inside an interactive {% data variables.copilot.copilot_cli_short %} session: the schedules fire only while the session in which they were created is running. For ways to run {% data variables.copilot.copilot_cli_short %} on a schedule when no session is open, see [Running a prompt from an external scheduler](<#running-a-prompt-from-an-external-scheduler>) at the end of this article.

## Scheduling a recurring prompt with `/every`

In an interactive {% data variables.copilot.copilot_cli_short %} session, type `/every` followed by the prompt you want to be submitted. To use a fixed interval, include the interval before the prompt.

```copilot
/every [INTERVAL] PROMPT
```

If you specify an interval, the prompt will be submitted after the interval has elapsed and then again on the same cadence until you delete the schedule entry or you end the interactive CLI session.

### Examples

```copilot
/every 1h run the test suite and summarize any new failures
```

```copilot
/every 30m check for new comments on my open pull requests
```

> [!NOTE]
> `/loop` is an alias of `/every`. So you can enter `/loop` instead of `/every`, if you prefer, and the effect is the same.

### Self-paced scheduling

If you use `/every` with a prompt but no interval or time—for example, `/every keep checking the deployment and let me know once it's healthy`—{% data variables.product.prodname_copilot_short %} creates a self-paced schedule instead of an error. After each run, {% data variables.product.prodname_copilot_short %} decides when to run the prompt again based on the task, rather than following a fixed interval, and stops on its own once it decides there's nothing left to watch. Because the model chooses the cadence, a self-paced schedule can run more or less often than a fixed interval would, so keep an eye on it and delete it (as described in [Managing scheduled prompts](<#managing-scheduled-prompts>)) if it runs more than you expect.

## Scheduling a once-only prompt with `/after`

Type `/after` followed by a delay and the prompt you want to submit:

```copilot
/after DELAY PROMPT
```

The prompt fires once, after the delay has elapsed, and is then removed from the schedule list.

> [!NOTE]
> `/after` does not support self-paced scheduling. If you pass a prompt to `/after`, you must supply a delay or time.

### Examples

```copilot
/after 30m Give me details of changes to README.md made in the last 30 minutes
```

```copilot
/after 10m Check that the address finder is visible on example.com/register
```

## Scheduling a skill

You can use `/every` and `/after` to schedule a skill. To do this, you can reference the skill explicitly by using its slash command, or you can use natural language to tell {% data variables.product.prodname_copilot_short %} to run the skill.

> [!NOTE]
> Only user-invocable skills and a subset of built-in slash commands can be scheduled. Commands that start a self-contained piece of work—such as `/plan`, `/review`, `/research`, or `/security-review`—are schedulable. Commands that change your session or configuration (for example, `/model`, `/clear`, `/compact`, `/permissions`, or `/sandbox`), that only display information (such as `/usage` or `/context`), or that manage scheduling itself (`/every` and `/after`) can't be scheduled, and {% data variables.product.prodname_copilot_short %} rejects them when you try.

### Examples

```copilot
/after 2h Use the docx skill to create a new file summarizing recent changes to this repo
```

```copilot
/every 1d /refactor-plan Adjust the architecture of this project to improve the responsiveness of the client UI
```

## Interval and delay syntax

| Suffix | Unit    | Example |
| ------ | ------- | ------- |
| `s`    | seconds | `30s`   |
| `m`    | minutes | `5m`    |
| `h`    | hours   | `2h`    |
| `d`    | days    | `1d`    |

When you specify a numeric duration, always include the suffix. A bare number—for example, `/every 30 remind me to check for Slack messages`—is not recognized as an interval.

For a fixed interval, the minimum is **10 seconds** and the maximum is **1 day** (24 hours).

You can also describe the timing in plain language instead of using a duration—for example, `/after at 3pm push the release`, or `/every day at 9am post the standup`. {% data variables.product.prodname_copilot_short %} uses a model to interpret the phrase, then creates the schedule from it.

## Identifying scheduled prompts in the session

When a scheduled prompt is triggered, {% data variables.product.prodname_copilot_short %} precedes the prompt with text such as `[Scheduled prompt #4]` to distinguish it from a prompt you typed yourself.

You can use the ID to cancel the schedule. For example, by entering `stop prompt 4`.

## Managing scheduled prompts

To list the active schedules for the current session, type `/every` or `/after` with no arguments.

To delete a schedule, use the arrow keys on your keyboard to move through the list and select the schedule you want to delete, then press <kbd>x</kbd>.

Press <kbd>Esc</kbd> to exit the schedule list.

## What happens when you close and reopen a session

Scheduled prompts are scoped to the session they were created in, and they are only triggered while that session is running.

When you reopen the session (using the `--continue` or `--resume` command line options) the schedules are restored. For a recurring schedule created with a fixed interval, the wait before the next run is measured from the moment you reopen the session.

An `/after` schedule that had not been triggered before you closed the session keeps its original target time, rather than restarting the delay. If that time passed while the session was closed, the prompt is submitted as soon as you reopen the session.

## Running a prompt from an external scheduler

The `/every` and `/after` commands only run while an interactive {% data variables.copilot.copilot_cli_short %} session is open. If you want a prompt to run on a schedule even when no session is active, you can run {% data variables.copilot.copilot_cli_short %} from an external scheduler such as **cron** on macOS or Linux, or **Task Scheduler** on Windows.

To run {% data variables.copilot.copilot_cli_short %} programmatically, use the syntax `copilot -p "YOUR PROMPT"`. The CLI processes your prompt without displaying the interactive interface and then exits.

This is useful for tasks such as:

* **Nightly maintenance** — for example, running your test suite against the latest changes on a branch every night and posting a summary to a tracking issue.
* **Periodic dependency checks** — for example, opening a weekly pull request that updates dependencies and runs the test suite against the result.
* **Scheduled reports** — for example, generating a daily summary of new issues or pull requests assigned to you and emailing or posting it to a chat channel.

For more information, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/automate-copilot-cli/run-cli-programmatically>).

## Further reading

* [AUTOTITLE](<https://github.com/copilot/reference/copilot-cli-reference/cli-command-reference#slash-commands-in-the-interactive-interface>)


</details>
