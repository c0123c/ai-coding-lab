# Copilot 编码 Agent 的能力更新

介绍模型选择、自我审查、安全扫描、自定义 Agent 和本地云端交接等更新；反映文章发布时的产品能力。

- 来源：GitHub／工程文章
- 资料类型：存档文档
- 文档时间：发表：[2026-02-26](<https://github.blog/ai-and-ml/github-copilot/whats-new-with-github-copilot-coding-agent/>)；更新：[2026-02-26](<https://github.blog/ai-and-ml/github-copilot/whats-new-with-github-copilot-coding-agent/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.blog/ai-and-ml/github-copilot/whats-new-with-github-copilot-coding-agent/>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/工程文章/Copilot 编码 Agent 的能力更新.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# GitHub Copilot 编码 agent 的新增功能

您在午餐前打开一个问题。当你回来时，有一个拉取请求正在等待。

这就是 [GitHub Copilot 编码 agent](<https://docs.github.com/en/copilot/concepts/agents/coding-agent/about-coding-agent>) 的用途。它在后台工作，修复错误，添加测试，清理债务，并在完成后返回拉取请求。当您使用 Copilot 在编辑器中实时编写代码时，编码 agent 正在处理您委托的工作。

最近的一些更新使这种切换更加有用。以下是已发货的内容以及如何开始使用它。

*视觉学习者？观看上面的视频！ ☝️*

## 为每项任务选择正确的模型

Agents 面板现在包含一个模型选择器。

以前，每个后台任务都在单个默认模型上运行。您无法购买更强大的模型来完成更艰巨的工作或优先考虑日常任务的速度。

现在你可以了。使用更快的模型进行简单的工作，例如添加单元测试。升级您的模型以进行粗糙的重构或与真实边缘情况的集成测试。如果您不想考虑它，请将其保留为自动。

**开始使用：**

* 打开 Agents 面板（GitHub 的右上角），选择您的存储库，然后选择一个模型。
* 写下清晰的提示并开始任务。
* 如果您想让 GitHub 选择，请将模型保留为自动。

Copilot Pro 和 Pro+ 用户现在可以选择型号，并且即将推出对商业和企业的支持。

[通过Copilot编码agent了解有关型号选择的更多信息。 👉](<https://docs.github.com/copilot/how-tos/use-copilot-agents/coding-agent/changing-the-ai-model>)

## 拉取请求以更好的状态到达

检查 agent 输出的痛苦部分始终是清理。你打开差异，它就在那里：技术上可行的逻辑，但没有人会那样写。

Copilot 编码 agent 现在在打开拉取请求之前使用 Copilot 代码审查来审查自己的更改。它获取反馈、迭代并改进补丁。当您被标记为进行审核时，已经有人审核过了。

[在一次会话](<https://youtu.be/S1ch_6fjp5M?t=212&si=wVXe0uClUTohv1qW>)中，agent发现自己的字符串连接过于复杂，并在拉取请求落地之前修复了它。这种事情曾经是你的问题。

**开始使用：**

* 将问题分配给 Copilot 或从 Agents 面板创建任务。
* 单击进入任务即可查看日志。
* 查看 agent 运行 Copilot 代码审查和应用反馈的时刻。

出现提示时查看拉取请求。 Copilot 仅在迭代后才请求您进行审核。

[详细了解Copilot代码审查+Copilot编码agent。 👉](<https://docs.github.com/en/copilot/how-tos/use-copilot-agents/request-a-code-review/use-code-review>)

## agent 工作时运行的安全检查

就像人类生成的代码一样，AI-生成的代码可能会带来真正的风险：易受攻击的模式、意外提交的秘密、与已知 CVE 的依赖关系。不同之处在于它做得更快。你真的不想在评论中发现这一点。

Copilot 编码 agent 现在直接在其工作流程内运行代码扫描、秘密扫描和依赖性漏洞检查。如果依赖项存在已知问题，或者某些内容看起来像已提交的 API 密钥，则会在拉取请求打开之前对其进行标记。

代码扫描通常是 GitHub 高级安全性的一部分。使用Copilot编码agent，您可以免费获得。

**开始使用：**

* 通过Agents面板运行任何任务。
* 在运行时检查会话日志。当 agent 工作时，您将看到扫描条目。
* 查看拉取请求。它已经通过了安全过滤器。

[在Copilot编码agent中了解有关安全扫描的更多信息。 👉](<https://github.blog/changelog/2025-10-28-copilot-coding-agent-now-automatically-validates-code-security-and-quality/>)

## 遵循您团队流程的自定义 agents

简短的提示留下了很多判断的余地。而且这种判断并不总是与你的团队的实际工作方式一致。

自定义 agents 让您可以对其进行编码。在`.github/agents/`下创建一个文件并定义具体方法。例如，性能优化器 agent 可以首先连接到基准测试，进行更改，然后在打开拉取请求之前测量差异。

在 [ 最近的 GitHub 结账演示 ](<https://youtu.be/S1ch_6fjp5M?t=400&si=xvLSQSMWzdapU5Hf>) 中，这正是发生的情况。 agent 对查找进行了基准测试，进行了有针对性的修复，并在该功能上进行了 99% 改进。小范围，真实数据，无需猜测。

您也可以在您的组织或企业中共享自定义 agents，因此相同的流程适用于使用编码 agent 的所有团队。

**开始使用：**

* 在您的存储库中的 `.github/agents/` 下创建一个 agent 文件。
* 打开Agents面板并开始一个新任务。
* 从选项中选择您的自定义 agent。
* 编写一个针对 agent 的作用的提示。

[了解有关创建自定义 agents 的更多信息。 👉](<https://docs.github.com/en/copilot/how-tos/use-copilot-agents/coding-agent/create-custom-agents?utm_source=chatgpt.com>)

## 在云和本地之间移动而不会丢失上下文

有时您在云中开始某些事情并希望在本地完成它。有时，您深陷终端，想要在不失去流畅性的情况下交出一些东西。无论哪种方式，切换上下文都意味着重新开始对话。

现在不行了。将云会话拉入您的终端，您将获得分支、日志和完整上下文。或者按CLI中的`&`将工作推回云端并继续在您这边进行。

**开始使用：**

* 使用 Copilot 编码 agent 启动任务并等待会话出现。
* 单击“CopilotCLI中的Continue”并复制命令。
* 将其粘贴到终端中以在本地加载会话，并且分支、日志和上下文完好无损。
* 按 CLI 中的 & 符号 (`&`) 将工作委派回云端并继续在本地进行。

[详细了解 Copilot 编码 agent + CLI 切换。 👉](<https://docs.github.com/en/copilot/how-tos/use-copilot-agents/manage-agents>)

## 这加起来意味着什么

Copilot 编码 agent 已经取得了长足的进步。型号选择、自我审查、安全扫描、定制 agents、CLI 交接——这就是最近发布的功能。该团队正在积极致力于私有模式，在编码之前进行规划，并使用 agent 来完成甚至不需要拉取请求的事情，例如总结问题或生成报告。还有更多即将推出。敬请关注。

在 [GitHub 社区讨论 ](<https://github.com/orgs/community/discussions/categories/copilot-conversations>) 中分享有关下一步发布内容的反馈。

开始使用 [GitHub Copilot 编码 agent >](<https://docs.github.com/en/copilot/concepts/agents/coding-agent/about-coding-agent>)

---

## 标签：

* [GitHub 结帐](<https://github.blog/tag/github-checkout/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [GitHub Copilot编码agent](<https://github.blog/tag/github-copilot-coding-agent/>)

## 撰写者

![安德里亚·格里菲斯](<https://github.blog/wp-content/uploads/2025/08/Andrea-Griffiths_avatar_1755783168-200x200.jpeg>)

### [安德里亚·格里菲斯](<https://github.blog/author/andreagriffiths11/>)

[@AndreaGriffiths11](<https://github.com/andreagriffiths11>)

Andrea 是 GitHub 的高级开发人员倡导者，在开发人员工具方面拥有十多年的经验。她将技术深度与让先进技术更容易获得的使命结合起来。从陆军服役和施工管理转向软件开发后，她以独特的视角将复杂的工程概念与实际实施联系起来。她与威尔士伴侣、两个儿子和两只狗住在佛罗里达州，在那里她继续通过 GitHub 的全球计划推动创新并支持开源。在线查找她@acolombiadev。


## 原文对照

<details>
<summary>展开完整原文</summary>

# What’s new with GitHub Copilot coding agent

You open an issue before lunch. By the time you’re back, there’s a pull request waiting.

That’s what [GitHub Copilot coding agent](<https://docs.github.com/en/copilot/concepts/agents/coding-agent/about-coding-agent>) is built for. It works in the background, fixing bugs, adding tests, cleaning up debt, and comes back with a pull request when it’s done. While you’re writing code in your editor with Copilot in real time, the coding agent is handling the work you’ve delegated.

A few recent updates make that handoff more useful. Here’s what shipped and how to start using it.

*Visual learner? Watch the video above! ☝️*

## Choose the right model for each task

The Agents panel now includes a model picker.

Before, every background task ran on a single default model. You couldn’t pay for a more robust model to complete harder work or prioritize speed on routine tasks.

Now you can. Use a faster model for straightforward work like adding unit tests. Upgrade your model for a gnarly refactor or integration tests with real edge cases. If you’d rather not think about it, leave it on auto.

**To get started:**

* Open the Agents panel (top-right in GitHub), select your repo, and pick a model.
* Write a clear prompt and kick off the task.
* Leave the model on auto if you’d rather let GitHub choose.

Model selection is available for Copilot Pro and Pro+ users now, with support for Business and Enterprise coming soon.

[Learn more about model selection with Copilot coding agent. 👉](<https://docs.github.com/copilot/how-tos/use-copilot-agents/coding-agent/changing-the-ai-model>)

## Pull requests that arrive in better shape

The painful part of reviewing agent output has always been the cleanup. You open the diff and there it is: logic that technically works, but nobody would write it that way.

Copilot coding agent now reviews its own changes using Copilot code review before it opens the pull request. It gets feedback, iterates, and improves the patch. By the time you’re tagged for review, someone already went through it.

[In one session](<https://youtu.be/S1ch_6fjp5M?t=212&si=wVXe0uClUTohv1qW>), the agent caught that its own string concatenation was overly complex and fixed it before the pull request landed. That kind of thing used to be your problem.

**To get started:**

* Assign an issue to Copilot or create a task from the Agents panel.
* Click into the task to view the logs.
* See the moments where the agent ran Copilot code review and applied feedback.

Review the pull request when prompted. Copilot requests your review only after it has iterated.

[Learn more about Copilot code review + Copilot coding agent. 👉](<https://docs.github.com/en/copilot/how-tos/use-copilot-agents/request-a-code-review/use-code-review>)

## Security checks that run while the agent works

Just like with human-generated code, AI-generated code can introduce real risks: vulnerable patterns, secrets accidentally committed, dependencies with known CVEs. The difference is it does it faster. And you really don’t want to find that in review.

Copilot coding agent now runs code scanning, secret scanning, and dependency vulnerability checks directly inside its workflow. If a dependency has a known issue, or something looks like a committed API key, it gets flagged before the pull request opens.

Code scanning is normally part of GitHub Advanced Security. With Copilot coding agent, you get it for free.

**To get started:**

* Run any task through the Agents panel.
* Check the session logs as it runs. You’ll see scanning entries as the agent works.
* Review the pull request. It’s already been through the security filter.

[Learn more about security scanning in Copilot coding agent. 👉](<https://github.blog/changelog/2025-10-28-copilot-coding-agent-now-automatically-validates-code-security-and-quality/>)

## Custom agents that follow your team’s process

A short prompt leaves a lot to judgment. And that judgment isn’t always consistent with how your team actually works.

Custom agents let you codify it. Create a file under `.github/agents/` and define a specific approach. A performance optimizer agent, for example, can be wired to benchmark first, make the change, then measure the difference before opening a pull request.

In a [recent GitHub Checkout demo](<https://youtu.be/S1ch_6fjp5M?t=400&si=xvLSQSMWzdapU5Hf>), that’s exactly what happened. The agent benchmarked a lookup, made a targeted fix, and came back with a 99% improvement on that one function. Small scope, real data, no guessing.

You can share custom agents across your org or enterprise too, so the same process applies everywhere teams are using the coding agent.

**To get started:**

* Create an agent file under `.github/agents/` in your repo.
* Open the Agents panel and start a new task.
* Select your custom agent from the options.
* Write a prompt scoped to what that agent does.

[Learn more about creating custom agents. 👉](<https://docs.github.com/en/copilot/how-tos/use-copilot-agents/coding-agent/create-custom-agents?utm_source=chatgpt.com>)

## Move between cloud and local without losing context

Sometimes you start something in the cloud and want to finish it locally. Sometimes you’re deep in your terminal and want to hand something off without losing your flow. Either way, switching contexts used to mean starting the conversation over.

Now it doesn’t. Pull a cloud session into your terminal and you get the branch, the logs, and the full context. Or press `&` in the CLI to push work back to the cloud and keep going on your end.

**To get started:**

* Start a task with Copilot coding agent and wait for the session to appear.
* Click “Continue in Copilot CLI” and copy the command.
* Paste it in your terminal to load the session locally with branch, logs, and context intact.
* Press the ampersand symbol (`&`) in the CLI to delegate work back to the cloud and keep going locally.

[Learn more about Copilot coding agent + CLI handoff. 👉](<https://docs.github.com/en/copilot/how-tos/use-copilot-agents/manage-agents>)

## What this adds up to

Copilot coding agent has come a long way. Model selection, self-review, security scanning, custom agents, CLI handoff—and that’s just what shipped recently. The team is actively working on private mode, planning before coding, and using the agent for things that don’t even need a pull request, like summarizing issues or generating reports. There’s a lot more coming. Stay tuned.

Share feedback on what ships next in [GitHub Community discussions](<https://github.com/orgs/community/discussions/categories/copilot-conversations>).

Get started with [GitHub Copilot coding agent >](<https://docs.github.com/en/copilot/concepts/agents/coding-agent/about-coding-agent>)

---

## Tags:

* [GitHub Checkout](<https://github.blog/tag/github-checkout/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [GitHub Copilot coding agent](<https://github.blog/tag/github-copilot-coding-agent/>)

## Written by

![Andrea Griffiths](<https://github.blog/wp-content/uploads/2025/08/Andrea-Griffiths_avatar_1755783168-200x200.jpeg>)

### [Andrea Griffiths](<https://github.blog/author/andreagriffiths11/>)

[@AndreaGriffiths11](<https://github.com/andreagriffiths11>)

Andrea is a Senior Developer Advocate at GitHub with over a decade of experience in developer tools. She combines technical depth with a mission to make advanced technologies more accessible. After transitioning from Army service and construction management to software development, she brings a unique perspective to bridging complex engineering concepts with practical implementation. She lives in Florida with her Welsh partner, two sons, and two dogs, where she continues to drive innovation and support open source through GitHub's global initiatives. Find her online @acolombiadev.


</details>
