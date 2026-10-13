# 自动分流 Dependabot 拉取请求

通过创建自动化任务、描述需求、选择仓库和审查结果，展示怎样处理重复的依赖更新任务并继续后续开发。

- 来源：GitHub／工程文章
- 资料类型：存档文档
- 文档时间：发表：[2026-08-26](<https://github.blog/ai-and-ml/github-copilot/github-copilot-app-for-beginners-automate-dependabot-pull-request-triage/>)；更新：[2026-08-26](<https://github.blog/ai-and-ml/github-copilot/github-copilot-app-for-beginners-automate-dependabot-pull-request-triage/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.blog/ai-and-ml/github-copilot/github-copilot-app-for-beginners-automate-dependabot-pull-request-triage/>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/工程文章/自动分流 Dependabot 拉取请求.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# GitHub Copilot 适合初学者的应用程序：自动化 Dependabot 拉取请求分类

我可能有偏见，但我认为 Dependabot 非常棒。它有助于使我的项目保持最新状态，确保我始终使用安全的库。但由于经常出现新漏洞，因此 Dependabot 经常会发出新的拉取请求。

有时这是一个小版本的提升。有时这是一个主要版本升级。有时一切都会很好。有时……好吧，每个开发人员都遇到过重大变化。

我们如何才能最好地对这些拉取请求进行分类？这项工作本身并不是特别困难，但确实是重复性的。

这是卸载到 Copilot 的完美任务！通过 [GitHub Copilot 应用程序自动化 ](<https://docs.github.com/en/copilot/concepts/agents/cloud-agent/about-automations>)，您可以完成第一轮审核。您无需手动检查每个 Dependabot 拉取请求，而是可以创建一个自动化程序来审查开放的拉取请求、按风险对其进行分组、验证 CI 状态并在一天开始之前提供摘要。

请按照以下步骤构建每日 Dependabot 分类自动化。

## 步骤 1：创建新的自动化

从 GitHub Copilot 应用程序中，[ 创建一个新的自动化 ](<https://docs.github.com/en/copilot/how-tos/github-copilot-app/using-automations>)。

您首先需要配置两件事：

* **名称：** 为自动化指定一个描述性名称，例如 **Daily Dependabot Triage**。
* **触发器：** 决定何时运行。

可用的触发选项包括：

* 手册
* 每小时
* 每日
* 每周
* 创建问题时

对于像[Dependabot评论](<https://docs.github.com/en/code-security/tutorials/secure-your-dependencies/dependabot-quickstart>)这样的重复维护任务，每日计划通常是一个不错的选择。例如，您可以将其安排在工作日开始之前运行，以便在您登录时等待结果。

您还可以选择自动化是在云端运行还是在本地计算机上运行。

## 步骤2：用自然语言描述任务

接下来，告诉Copilot你想要它做什么。

例如：

```
Review the open Dependabot pull requests, group them by risk, identify the safe patch and minor version updates, verify that CI is passing for each pull request, and provide a short summary of the recommended next steps.
```

由于提示使用自然语言，因此您可以对其进行自定义以匹配您团队的工作流程。

## 步骤3：选择存储库

选择自动化应分析的存储库或项目。

选择存储库后，创建自动化。

如果您想立即测试它而不是等待计划的运行，请选择**创建并运行**。

## 步骤 4：查看结果

自动化完成后，Copilot 返回摘要而不是单个拉取请求的列表。

例如，它可能：

* 将安全补丁更新分组在一起
* 单独的次要版本和主要版本升级
* 确定哪些拉取请求已通过 CI
* 突出显示需要额外调查的依赖项

您可以快速确定哪些更新已准备好合并以及哪些更新值得密切关注，而不是用数十个小决定来打断您的早晨。

## 步骤 5：Continue Copilot 会话中的工作

如果其中一项更新需要额外的工作，您可以直接从自动化结果中继续。

例如，如果摘要确定了主要框架升级，您可以从结果中启动新的 Copilot 会话，并要求 Copilot 帮助完成迁移。

由于会话从自动化上下文开始，因此您不必再次收集信息。

## 查看以前的自动化运行

每次自动化运行都会被保存，以便于查看：

* 当它运行时
* 它执行了哪些操作
* 它产生了什么结果

拥有每次运行的历史记录使自动化变得透明。您可以随时回顾发生的事情，而不是将它们视为黑匣子。

## 将重复性工作变成后台工作

Dependabot 分类是非常适合自动化的重复任务的一个很好的例子。您描述一次工作流程，选择何时运行，然后让 Copilot 自动执行重复步骤。

如果您刚刚开始使用自动化，请从您已经在自动驾驶仪上执行的任务开始。让 Copilot 处理日常工作，以便您可以将时间花在需要您专业知识的决策上。

**准备好自动化您的下一个重复任务了吗？** [在 GitHub Copilot 应用程序中创建您的第一个自动化 >](<https://github.com/features/ai/github-app>)

---

## 标签：

* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [GitHub Copilot 应用程序](<https://github.blog/tag/github-copilot-app/>)
* [GitHub Copilot 适合初学者的应用程序](<https://github.blog/tag/github-copilot-app-for-beginners/>)

## 撰写者

![克里斯托弗·哈里森](<https://avatars.githubusercontent.com/u/6109729?v=4&s=200>)

### [克里斯托弗·哈里森](<https://github.blog/author/geektrainer/>)

[@geektrainer](<https://github.com/geektrainer>)

高级开发者倡导者，GitHub


## 原文对照

<details>
<summary>展开完整原文</summary>

# GitHub Copilot app for Beginners: Automate Dependabot pull request triage

I might be biased, but I think Dependabot is pretty amazing. It helps keep my projects up to date, ensuring I’m always using secure libraries. But because there’re frequently new vulnerabilities, there’re frequently new pull requests from Dependabot.

Sometimes it’s a minor version bump. Sometimes it’s a major version upgrade. Sometimes everything will work just fine. And sometimes… well, every single developer has been caught by a breaking change.

How can we best triage these pull requests? The work isn’t particularly difficult per se, but it certainly is repetitive.

It’s the perfect task to offload to Copilot! With [GitHub Copilot app automations](<https://docs.github.com/en/copilot/concepts/agents/cloud-agent/about-automations>), you can hand off that first round of review. Instead of manually inspecting every Dependabot pull request, you can create an automation that reviews open pull requests, groups them by risk, verifies CI status, and delivers a summary before your day begins.

Follow the steps below to build a daily Dependabot triage automation.

## Step 1: Create a new automation

From the GitHub Copilot app, [create a new automation](<https://docs.github.com/en/copilot/how-tos/github-copilot-app/using-automations>).

You’ll configure two things first:

* **Name:** Give the automation a descriptive name, such as **Daily Dependabot Triage**.
* **Trigger:** Decide when it should run.

Available trigger options include:

* Manual
* Hourly
* Daily
* Weekly
* When an issue is created

For recurring maintenance tasks like [Dependabot reviews](<https://docs.github.com/en/code-security/tutorials/secure-your-dependencies/dependabot-quickstart>), a daily schedule is often a good choice. For example, you might schedule it to run before your workday begins so the results are waiting when you log in.

You can also choose whether the automation runs in the cloud or on your local machine.

## Step 2: Describe the task in natural language

Next, tell Copilot what you want it to do.

For example:

```
Review the open Dependabot pull requests, group them by risk, identify the safe patch and minor version updates, verify that CI is passing for each pull request, and provide a short summary of the recommended next steps.
```

Because the prompt uses natural language, you can customize it to match your team’s workflow.

## Step 3: Select the repository

Choose the repository or project the automation should analyze.

Once you’ve selected the repository, create the automation.

If you want to test it immediately instead of waiting for the scheduled run, choose **Create and Run**.

## Step 4: Review the results

When the automation finishes, Copilot returns a summary instead of a list of individual pull requests.

For example, it might:

* Group safe patch updates together
* Separate minor and major version upgrades
* Identify which pull requests have passing CI
* Highlight dependencies that require additional investigation

Rather than interrupting your morning with dozens of small decisions, you can quickly identify which updates are ready to merge and which deserve closer attention.

## Step 5: Continue the work in a Copilot session

If one of the updates requires additional work, you can continue directly from the automation results.

For example, if the summary identifies a major framework upgrade, you can start a new Copilot session from the results and ask Copilot to help complete the migration.

Because the session starts with the automation’s context, you don’t have to gather the information again.

## Review previous automation runs

Every automation run is saved, making it easy to see:

* When it ran
* What actions it performed
* What results it produced

Having a history of each run makes automations transparent. You can always review what happened instead of treating them as a black box.

## Turn repetitive work into background work

Dependabot triage is a good example of the kind of recurring task that’s well suited for automation. You describe the workflow once, choose when it should run, and let Copilot perform the repetitive steps automatically.

If you’re just getting started with automations, begin with a task you already perform on autopilot. Let Copilot handle the routine work so you can spend your time on the decisions that require your expertise.

**Ready to automate your next recurring task?** [Create your first automation in the GitHub Copilot app >](<https://github.com/features/ai/github-app>)

---

## Tags:

* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [GitHub Copilot app](<https://github.blog/tag/github-copilot-app/>)
* [GitHub Copilot app for Beginners](<https://github.blog/tag/github-copilot-app-for-beginners/>)

## Written by

![Christopher Harrison](<https://avatars.githubusercontent.com/u/6109729?v=4&s=200>)

### [Christopher Harrison](<https://github.blog/author/geektrainer/>)

[@geektrainer](<https://github.com/geektrainer>)

Senior Developer Advocate, GitHub


</details>
