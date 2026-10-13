# 用 Agent 应用衔接软件交付流程

介绍需求范围、安全、发布与交付等阶段的 Agent 应用，展示怎样在 GitHub 中组织跨环节工作。

- 来源：GitHub／工程文章
- 资料类型：存档文档
- 文档时间：发表：[2026-08-14](<https://github.blog/ai-and-ml/github-copilot/how-to-bring-your-software-delivery-workflow-into-github-with-agent-apps/>)；更新：[2026-08-14](<https://github.blog/ai-and-ml/github-copilot/how-to-bring-your-software-delivery-workflow-into-github-with-agent-apps/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.blog/ai-and-ml/github-copilot/how-to-bring-your-software-delivery-workflow-into-github-with-agent-apps/>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/工程文章/用 Agent 应用衔接软件交付流程.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 如何使用 agent 应用程序将您的软件交付工作流程引入 GitHub

您在拉取请求旁边打开了多少个选项卡？

想象一下，您的产品的免费试用入门流程中出现了一个新问题：将“邀请您的队友”步骤设为可选。随着注册人数的增加，支持人员不断将该步骤标记为摩擦点。快赢吧？

从范围界定到部署，您需要回答以下四个问题：

* 这是正确的改变吗？
* 我所接触的依赖关系是否干净？
* 我如何安全地推出它？
* 现在部署安全吗？

每个答案都存在于不同的工具中，因此完成拉取请求意味着在四个地方携带相同的上下文。

[GitHub agent 应用程序 ](<https://docs.github.com/copilot/concepts/agents/agent-apps>) 将回答这些问题所需的工具带到您已经工作的地方，由与我们自己的 [Copilot 云 agent](<https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent>) 相同的平台和工具提供支持。下面的说明性演练展示了如何使用您已经依赖的服务（例如 Amplitude、Endor Labs、LaunchDarkly 和 PagerDuty）来回答这些问题并完成此请求，而无需离开 GitHub。

## 1。在构建之前

支持人员表示，“邀请你的队友”步骤对于正在使用你的产品的客户来说很烦人，但他们没有说明谁提出了投诉，或者这些投诉是否会导致客户流失。你的怀疑是正确的。因此，您无需打开 Amplitude 并构建查询来确认您的预感，而是直接从 **Agents** 选项卡询问 [Amplitude](<https://github.com/apps/amplitude>) agent：

[![](<https://github.blog/wp-content/uploads/2026/08/Screenshot-2026-08-12-at-4.03.38-PM.png>)](<https://github.blog/wp-content/uploads/2026/08/Amp.mp4>)

```
@amplitude[agent] is completing the team invite step correlated with success later in the funnel? Break it down by segments we're measuring.
```

分裂结果很明显：完成该步骤的团队用户更有可能在以后保留，而单独用户则没有这种相关性。现在重新调整范围是合理的：推迟单独注册的步骤并保留团队注册的步骤。

现在可以在 GitHub 中访问产品见解，从而可以在编写任何代码之前进行路线修正。

## 2。当你构建它时

Copilot 打开更改的草稿拉取请求。该实现还更新了入门流程使用的依赖项。您不必等待 CI 扫描稍后失败，而是在评论中询问 [Endor Labs](<https://github.com/marketplace/endor-labs-agenthq-plugin>) agent：

[![](<https://github.blog/wp-content/uploads/2026/08/Screenshot-2026-08-12-at-4.03.21-PM.png>)](<https://github.blog/wp-content/uploads/2026/08/endor.mp4>)

```
@endor-labs-github-agenthq[agent] is there anything I need to watch out for in the dependencies being touched by this pull request?
```

agent 识别已更改的依赖项，检查它们是否存在已知漏洞和更广泛的包风险，然后在拉取请求中报告。这一次，一切看起来都很干净。没有什么可以补救的。

当更改仍在您面前时，依赖性审查将成为主动检查。比 CI 扫描失败后进行修复要好得多。

## 3。推出

之前的发现现在已付诸实施：单独注册的人可以获得可选路径，而团队则保留现有路径。由于这些细分是在注册时设置的，因此功能标志可以直接针对它们。请 [LaunchDarkly](<https://github.com/marketplace/launchdarkly-agent>) agent 为您设置，就像您询问团队成员一样：

[![](<https://github.blog/wp-content/uploads/2026/08/Screenshot-2026-08-12-at-4.03.15-PM.png>)](<https://github.blog/wp-content/uploads/2026/08/darkly.mp4>)

```
@launchdarkly-agent[agent] please create a feature flag for this pull request and wire it into the code. 
   - key: defer-team-invite 
   - type: boolean 
   - default: false 
   - target: solo-intent signups 
   - rollout: internal > 5% > 25% > 100%
```

agent 在 LaunchDarkly 中创建标志，并将代码实现添加为您审核的提交。如果目标环境需要批准，它会创建批准请求，而不是直接应用定位更改。是否继续推出仍由人类决定。

标志设置从第二个工具、手动代码切换和 Slack 协调到一个拉取请求评论和您审查的提交。

## 4。发货前

审查会告诉您代码是正确的，但服务是否处于良好的部署状态则是另一个问题。合并之前，您询问[PagerDuty](<https://github.com/apps/pagerduty-agent-app>) agent：

[![](<https://github.blog/wp-content/uploads/2026/08/Screenshot-2026-08-12-at-4.03.02-PM.png>)](<https://github.blog/wp-content/uploads/2026/08/pagerduty.mp4>)

```
@pagerduty-agent-app[agent] assess the deployment risk for this pull request against the onboarding service. Check active incidents and recent incident history, then recommend whether to proceed.
```

agent 将存储库映射到其 PagerDuty 服务，检查活动事件，查看之前的 90 天，并将拉取请求中的文件与过去事件涉及的区域进行比较。

这次的风险很低。没有活跃的事件，并且与当前的变化没有有意义的相关性。建议继续进行。

没有什么戏剧性的事情发生，但这就是重点。检查部署风险成为拉取请求的例行步骤，而不是仅在发布已经感觉危险时才执行的操作。

## 有什么变化

您仍然使用 Amplitude、LaunchDarkly、Endor Labs 和 PagerDuty。但现在，您不再需要在它们之间传递上下文，它们都将直接在您的 GitHub 工作流程中工作。

随着工作从想法转向生产，当每个服务的上下文或功能很重要时，开发人员可以将其引入 GitHub。通过 agent 应用程序，GitHub 成为开发人员和 agents 协调接下来发生的事情的地方，而无需开发人员切换上下文。

## 尝试一下

[Agent 应用程序 ](<https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/use-agent-apps>) 可从 [GitHub 市场 ](<https://github.com/marketplace?type=apps&category=agent-apps>) 获取。安装一个，为您的组织启用它，然后试用一下：

* 将其分配给一个问题以启动任务。
* 在拉取请求评论中@提及它以进行分析或采取行动。
* 从存储库的 **Agents** 选项卡中选择它。

你的工具仍然是你的工具。现在，它们会显示在您已经工作的位置：GitHub。探索其他首个 agent 应用程序并开始将您的堆栈直接引入您的工作流程：

* [Packfiles](<https://github.com/marketplace/packfiles-agent>) 的 agent 读取您的积压工作并构建迁移策略。读取您的待办事项并制定迁移策略。
* [Miro](<https://github.com/marketplace/miro-agent-app>) 的 agent 将视觉协作与代码工作流程连接起来。
* [Bright Security](<https://github.com/marketplace/bright-security-agent>)的agent在GitHub内部自主处理端到端动态安全测试。
* [SonarQube](<https://github.com/marketplace/sonarqube-agent>) 的 agent 将分析、质量门控和修复引入 GitHub agent 会话中。
* [Octopus Deploy](<https://github.com/marketplace/octopus-deploy-intelligence-agent>)的agent可以识别、诊断和解决部署失败。

[在 GitHub Marketplace >](<https://github.com/marketplace?type=apps&category=agent-apps>) 中发现 agent 应用

---

## 标签：

* [Agent 应用程序](<https://github.blog/tag/agent-apps/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [GitHub 市场](<https://github.blog/tag/github-marketplace/>)

## 撰写者

![张三](<https://avatars.githubusercontent.com/u/38818012?v=4&s=200>)

### [张三](<https://github.blog/author/imsamz/>)

[@im-samz](<https://github.com/im-samz>)

产品经理，GitHub Copilot


## 原文对照

<details>
<summary>展开完整原文</summary>

# How to bring your software delivery workflow into GitHub with agent apps

How many tabs do you have open alongside your pull request?

Imagine picking up a new issue in your product’s free-trial onboarding flow: make the “invite your teammates” step optional. Support keeps flagging the step as a friction point as signups increase. Quick win, right?

From scoping to deployment, you need answers to these four questions:

* Is this even the right change?
* Are the dependencies I’m touching clean?
* How do I roll it out safely?
* Is it safe to deploy right now?

Each answer lives in a different tool, so working through the pull request means carrying the same context across four places.

[GitHub agent apps](<https://docs.github.com/copilot/concepts/agents/agent-apps>) bring the tools you need to answer those questions to where you’re already working, powered by the same platform and harness as our own [Copilot cloud agent](<https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent>). The illustrative walkthrough below shows how you can use services you already depend on, such as Amplitude, Endor Labs, LaunchDarkly, and PagerDuty to answer these questions and complete this request, without ever leaving GitHub.

## 1. Before you build it

Support says the “invite your teammates” step is annoying for customers who are onboarding with your product, but they haven’t given an indication of who has complained or whether those complaints lead to churn. You’d be right to be skeptical. So instead of opening Amplitude and building a query to confirm your hunch, you ask the [Amplitude](<https://github.com/apps/amplitude>) agent right from the **Agents** tab:

[![](<https://github.blog/wp-content/uploads/2026/08/Screenshot-2026-08-12-at-4.03.38-PM.png>)](<https://github.blog/wp-content/uploads/2026/08/Amp.mp4>)

```
@amplitude[agent] is completing the team invite step correlated with success later in the funnel? Break it down by segments we're measuring.
```

The split comes back clear: team users who finish the step are more likely to retain later, while solo users don’t have that correlation. A rescope is now justified: defer the step for solo signups and keep it for teams.

Access to product insights is now within GitHub, enabling course correction before any code is written.

## 2. As you build it

Copilot opens a draft pull request for the change. The implementation also updates dependencies used by the onboarding flow. Instead of waiting for a CI scan to fail later, you ask the [Endor Labs](<https://github.com/marketplace/endor-labs-agenthq-plugin>) agent in a comment:

[![](<https://github.blog/wp-content/uploads/2026/08/Screenshot-2026-08-12-at-4.03.21-PM.png>)](<https://github.blog/wp-content/uploads/2026/08/endor.mp4>)

```
@endor-labs-github-agenthq[agent] is there anything I need to watch out for in the dependencies being touched by this pull request?
```

The agent identifies the changed dependencies, checks them for known vulnerabilities and broader package risk, then reports back in the pull request. This time, everything looks clean. Nothing to remediate.

Dependency review becomes a proactive check while the change is still in front of you. Much better than remediating a CI scan after it fails.

## 3. Rolling it out

The previous finding now gets carried through to implementation: solo signups get the optional path, while teams keep the existing one. Because these segments are set at signup, a feature flag can target them directly. Ask the [LaunchDarkly](<https://github.com/marketplace/launchdarkly-agent>) agent to set it up for you, the same way you’d ask a team member:

[![](<https://github.blog/wp-content/uploads/2026/08/Screenshot-2026-08-12-at-4.03.15-PM.png>)](<https://github.blog/wp-content/uploads/2026/08/darkly.mp4>)

```
@launchdarkly-agent[agent] please create a feature flag for this pull request and wire it into the code. 
   - key: defer-team-invite 
   - type: boolean 
   - default: false 
   - target: solo-intent signups 
   - rollout: internal > 5% > 25% > 100%
```

The agent creates the flag in LaunchDarkly and adds the code implementation as a commit you review. If the target environment requires approval, it creates an approval request instead of applying the targeting change directly. A human still decides whether the rollout moves forward.

Flag setup goes from a second tool, a manual code handoff, and Slack coordination to one pull request comment and a commit you review.

## 4. Before you ship

Review tells you the code is correct, but whether the service is in a good state for a deployment is a different question. Before merging, you ask the [PagerDuty](<https://github.com/apps/pagerduty-agent-app>) agent:

[![](<https://github.blog/wp-content/uploads/2026/08/Screenshot-2026-08-12-at-4.03.02-PM.png>)](<https://github.blog/wp-content/uploads/2026/08/pagerduty.mp4>)

```
@pagerduty-agent-app[agent] assess the deployment risk for this pull request against the onboarding service. Check active incidents and recent incident history, then recommend whether to proceed.
```

The agent maps the repository to its PagerDuty service, checks for active incidents, reviews the previous 90 days, and compares the files in the pull request with areas involved in past incidents.

This time, the risk is low. There are no active incidents and no meaningful correlation with the current changes. The recommendation is to proceed.

Nothing dramatic happens, but that’s the point. Checking deploy risk becomes a routine step for your pull requests instead of something you do only when a release already feels dangerous.

## What changes

You still use Amplitude, LaunchDarkly, Endor Labs, and PagerDuty. But now, you no longer need to carry the context between them, and they’ll all work directly in your GitHub workflows.

As work moves from idea to production, developers can bring each service into GitHub when its context or capabilities matter. With agent apps, GitHub becomes the place where developers and agents coordinate what happens next, without developers switching contexts.

## Try it

[Agent apps](<https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/use-agent-apps>) are available from the [GitHub Marketplace](<https://github.com/marketplace?type=apps&category=agent-apps>). Install one, enable it for your organization, and take it for a spin:

* Assign it to an issue to kick off a task.
* @mention it in a pull request comment for analysis or action.
* Select it from the **Agents** tab in your repository.

Your tools are still your tools. Now, they show up where you are already working: on GitHub. Explore the other inaugural agent apps and start bringing your stack directly into your workflow:

* [Packfiles](<https://github.com/marketplace/packfiles-agent>)’s agent reads your backlog and builds a migration strategy. reads your backlog and builds a migration strategy.
* [Miro](<https://github.com/marketplace/miro-agent-app>)‘s agent connects visual collaboration with code workflows.
* [Bright Security](<https://github.com/marketplace/bright-security-agent>)‘s agent autonomously handles end-to-end dynamic security testing inside GitHub.
* [SonarQube](<https://github.com/marketplace/sonarqube-agent>)‘s agent brings analysis, quality gates, and remediation into GitHub agent sessions.
* [Octopus Deploy](<https://github.com/marketplace/octopus-deploy-intelligence-agent>)‘s agent can identify, diagnose, and resolve deployment failures.

[Discover agent apps in the GitHub Marketplace >](<https://github.com/marketplace?type=apps&category=agent-apps>)

---

## Tags:

* [Agent Apps](<https://github.blog/tag/agent-apps/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [GitHub Marketplace](<https://github.blog/tag/github-marketplace/>)

## Written by

![Sam Zhang](<https://avatars.githubusercontent.com/u/38818012?v=4&s=200>)

### [Sam Zhang](<https://github.blog/author/imsamz/>)

[@im-samz](<https://github.com/im-samz>)

Product Manager, GitHub Copilot


</details>
