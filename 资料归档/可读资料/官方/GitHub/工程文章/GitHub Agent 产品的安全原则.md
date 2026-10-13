# GitHub Agent 产品的安全原则

分析自主工具操作的安全问题，介绍构建 Agent 产品时的权限与执行原则，并说明如何应用到自己的系统。

- 来源：GitHub／工程文章
- 资料类型：存档文档
- 文档时间：发表：[2025-11-25](<https://github.blog/ai-and-ml/github-copilot/how-githubs-agentic-security-principles-make-our-ai-agents-as-secure-as-possible/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.blog/ai-and-ml/github-copilot/how-githubs-agentic-security-principles-make-our-ai-agents-as-secure-as-possible/>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/工程文章/GitHub Agent 产品的安全原则.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# GitHub 的代理安全原则如何使我们的 AI agents 尽可能安全

在过去的几个月里，我们一直在努力为开发人员构建最可用和最有趣的 AI agents。为了在可用性和安全性之间取得适当的平衡，我们制定了一套准则，以确保我们设计的所有内容始终包含人机交互元素。

AI 产品越“代理”，它实际上能做的事情就越多，从而实现更丰富的工作流程，但代价是风险更大。随着功能的增加，AI 脱离护栏、失去对齐、甚至被坏人操纵的可能性更大，影响也更大。其中任何一个都可能给我们的客户带来安全事件。

为了使这些 agents 尽可能安全，我们构建了所有托管的 agents，以最大限度地提高可解释性、最大限度地减少自主性并减少异常行为。让我们深入研究我们的托管代理产品的威胁模型，特别是 Copilot 编码 agent。我们还将研究如何构建安全控制来减轻这些威胁，也许您将能够将这些原则应用到您自己的agents中。

## 安全问题

在开发代理功能时，我们主要关注三类风险：

1. **数据泄露**

当 agent 具有 Internet 访问权限时，它可能会将数据从上下文泄漏到非预期目的地。 agent 可能会被欺骗，无意或恶意地将数据从当前存储库发送到非预期网站。根据数据的敏感性，这可能会导致严重的安全事件，例如，如果 agent 将写访问权限 GitHub 令牌泄漏到恶意端点。

2. **冒充和正确行为归因**

当agent采取行动时，可能不清楚它应该拥有什么权限或应该在谁的指导下操作。当有人将 Copilot 编码 agent 分配给某个问题时，谁发布了该指令 - 提交该问题的人还是将其分配给 Copilot 的人？如果确实因 agent 的行为而发生了事件，我们如何确保 agent 所采取的行动具有适当的问责制和可追溯性？

3. **立即注射**

Agents 代表发起用户进行操作，因此确保发起用户知道 agent 将要做什么非常重要。 Agents 是从 GitHub 问题、存储库中的文件和许多其他地方提示的，因此确保发起者清楚了解 * 所有 * 指导它的信息非常重要。如果没有，恶意用户可能会隐藏指令并欺骗存储库维护人员使用错误指令运行 agents。

## 代理产品规则

为了帮助防止上述风险，我们为所有托管代理产品创建了一套规则，以使它们对用户来说更加一致和安全。

1. **确保所有上下文都可见**

允许不可见上下文可能会允许恶意用户隐藏维护人员可能无法看到的指令。例如，在Copilot编码agent中，恶意用户可能会创建一个GitHub问题，其中包含带有提示注入指令的不可见Unicode。如果维护者将 Copilot 分配给此问题，则可能会导致安全事件，因为维护者不会意识到这些隐形指令。

为了防止这种情况，我们显示生成上下文的文件，并尝试通过 Unicode 或 HTML 标签删除任何不可见或屏蔽的信息，然后再将其传递给 agent。这确保只有维护人员清晰可见的信息才会传递到 agent。

2. **防火墙 agent**

如前所述，不受限制地访问外部资源可能会导致 agent 泄露敏感信息或被外部资源即时注入并失去一致性。

我们对 Copilot 编码 agent 应用防火墙，以限制其访问潜在有害外部资源的能力。这允许用户配置 agent 的网络访问并阻止任何不需要的连接。为了平衡安全性和可用性，我们自动允许 MCP 交互绕过防火墙。

在我们的其他代理体验中，例如 Copilot 聊天，我们不会自动执行代码。例如，生成 HTML 时，输出最初显示为预览代码。用户必须手动启用丰富的预览界面，该界面执行 HTML。

3. **限制对敏感信息的访问**

防止 agent 泄露敏感数据的最简单方法是……首先不授予对其的访问权限！

我们仅提供 Copilot 其正常运行所必需的信息。这意味着诸如 CI 机密和当前存储库之外的文件之类的内容不会自动传递到 agents。一旦 agent 完成其会话，特定敏感内容（例如 Copilot 编码 agent 的 GitHub 令牌）将被撤销。

4. **防止不可逆的状态更改**

AI 可能而且将会犯错误。为了防止这些错误产生无法修复的下游影响，我们确保我们的 agents 在没有人参与的情况下无法启动任何不可逆的状态更改。

例如，Copilot编码agent只能创建拉取请求；它无法直接提交到默认分支。 Copilot 创建的拉取请求不会自动运行 CI；人类用户必须验证代码并手动运行 GitHub 操作。在我们的 Copilot 聊天功能中，MCP 交互在进行任何工具调用之前需要获得批准。

5. **始终将操作归因于发起者和 agent**

用户发起的任何代理交互都明确归因于该用户，并且 agent 采取的任何操作都明确归因于 agent。这确保了任何行动都有明确的责任链。

例如，由 Copilot 编码 agent 创建的拉取请求由发起操作的用户共同提交。拉取请求是使用 Copilot 身份生成的，以明确它们是 AI- 生成的。

6. **仅从授权用户收集上下文**

我们确保 agents 仅从授权用户收集上下文。这意味着 agents 必须始终在发起交互的用户授予的权限和上下文下运行。

Copilot 编码 agent 只能由对底层存储库具有写访问权限的用户分配给问题。另外，作为额外的安全控制，特别是对于公共存储库，它仅读取对底层存储库具有写访问权限的用户的问题评论。

## 立即尝试一下

我们建立了适用于任何新的 AI 产品的代理安全原则；它们被设计用于从代码生成 agents 到聊天功能的所有功能。虽然这些设计决策旨在对最终用户来说是不可见和直观的，但我们希望这使我们的产品决策更加清晰，以便您可以继续放心地使用 GitHub Copilot。有关这些安全功能的更多信息，请查看 [ 的 Copilot 编码 agent](<https://docs.github.com/en/copilot/concepts/agents/coding-agent/about-coding-agent>) 的公共文档。

尝试我们的新代理产品 [GitHub Copilot](<https://github.com/features/copilot>) >

---

## 标签：

* [代理 AI](<https://github.blog/tag/agentic-ai/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [安全](<https://github.blog/tag/security/>)

## 撰写者

![拉胡尔·扎德](<https://avatars.githubusercontent.com/u/21364815?v=4&s=200>)

### [拉胡尔·扎德](<https://github.blog/author/rzhade3/>)

[@rzade3](<https://github.com/rzhade3>)


## 原文对照

<details>
<summary>展开完整原文</summary>

# How GitHub’s agentic security principles make our AI agents as secure as possible

We’ve been hard at work over the past few months to build the most usable and enjoyable AI agents for developers. To strike the right balance between usability and security, we’ve put together a set of guidelines to make sure that there’s always a human-in-the-loop element to everything we design.

The more “agentic” an AI product is, the more it can actually do, enabling much richer workflows, but at the cost of a greater risk. With added functionality, there’s a greater chance and a much greater impact of the AI going off its guardrails, losing alignment, or even getting manipulated by a bad actor. Any of these could cause security incidents for our customers.

To make these agents as secure as possible, we’ve built all of our hosted agents to maximize interpretability, minimize autonomy, and reduce anomalous behavior. Let’s dive into our threat model for our hosted agentic products, specifically Copilot coding agent. We’ll also examine how we’ve built security controls to mitigate these threats, and perhaps you’ll be able to apply these principles to your own agents.

## Security concerns

When developing agentic features, we are primarily concerned with three classes of risks:

1. **Data exfiltration**

When an agent has Internet access, it could leak data from the context to unintended destinations. The agent may be tricked into sending data from the current repository to an unintended website, either inadvertently or maliciously. Depending on the sensitivity of data, this could result in a severe security incident, such as if an agent leaks a write access GitHub token to a malicious endpoint.

2. **Impersonation and proper action attribution**

When an agent undertakes an action, it may not be clear what permissions it should have or under whose direction it should operate. When someone assigns the Copilot coding agent to an issue, who issued the directive—the person who filed the issue or the person who assigned it to Copilot? And if an incident does occur as a result of something an agent did, how can we ensure proper accountability and traceability for the actions taken by the agent?

3. **Prompt injection**

Agents operate on behalf of the initiating user, so it’s very important to ensure that the initiating user knows what the agent is going to do. Agents are prompted from GitHub Issues, files within a repository, and many other places, so it’s important to ensure that the initiator has a clear picture of *all* the information guiding it. If not, malicious users could hide directives and trick repository maintainers into running agents with bad directives.

## Rules for agentic products

To help prevent the above risks, we have created a set of rules for all of our hosted agentic products to make them more consistent and secure for our users.

1. **Ensuring all context is visible**

Allowing invisible context can allow malicious users to hide directives that maintainers may not be able to see. For example, in the Copilot coding agent, a malicious user may create a GitHub Issue that contains invisible Unicode with prompt injection instructions. If a maintainer assigns Copilot to this issue, this could result in a security incident as the maintainer would not have been aware of these invisible directives.

To prevent this, we display the files from which context is generated and attempt to remove any invisible or masked information via Unicode or HTML tags before passing it to the agent. This ensures that only information that is clearly visible to maintainers is passed to the agent.

2. **Firewalling the agent**

As mentioned previously, having unfettered access to external resources can allow the agent to exfiltrate sensitive information or be prompt-injected by the external resource and lose alignment.

We apply a firewall to the Copilot coding agent to limit its ability to access potentially harmful external resources. This allows users to configure the agent’s network access and block any unwanted connections. To balance security and usability, we automatically allow MCP interactions to bypass the firewall..

In our other agentic experiences like Copilot Chat, we do not automatically execute code. For example, when generating HTML, the output is initially presented as code for preview. A user must manually enable the rich previewing interface, which executes the HTML.

3. **Limiting access to sensitive information**

The easiest way to prevent an agent from exfiltrating sensitive data is… to not give access to it in the first place!

We only give Copilot information that is absolutely necessary for it to function. This means that things like CI secrets and files outside the current repository are not automatically passed to agents. Specific sensitive content, such as the GitHub token for the Copilot coding agent, is revoked once the agent has completed its session.

4. **Preventing irreversible state changes**

AI can and will make mistakes. To prevent these mistakes from having downstream effects that cannot be fixed, we make sure that our agents are not able to initiate any irreversible state changes without a human in the loop.

For example, the Copilot coding agent is only able to create pull requests; it is not able to commit directly to a default branch. Pull requests created by Copilot do not run CI automatically; a human user must validate the code and manually run GitHub Actions. In our Copilot Chat feature, MCP interactions ask for approval before undertaking any tool calls.

5. **Consistently attributing actions to both initiator and agent**

Any agentic interaction initiated by a user is clearly attributed to that user, and any action taken by the agent is clearly attributed to the agent. This ensures a clear chain of responsibility for any actions.

For example, pull requests created by the Copilot coding agent are co-committed by the user who initiated the action. Pull requests are generated using the Copilot identity to make it clear that they were AI-generated.

6. **Only gathering context from authorized users**

We ensure that agents gather context only from authorized users. This means that agents must always operate under the permissions and context granted by the user who initiated the interaction.

The Copilot coding agent can only be assigned to issues by users who have write access to the underlying repository. Plus, as an additional security control, especially for public repositories, it only reads issue comments from users who have write access to the underlying repository.

## Try it out now

We built our agentic security principles to be applicable for any new AI products; they’re designed to work with everything from code generation agents to chat functionality. While these design decisions are intended to be invisible and intuitive to end users, we hope this makes our product decisions clearer so you can continue to use GitHub Copilot with confidence. For more information on these security features, check out [public documentation for Copilot coding agent](<https://docs.github.com/en/copilot/concepts/agents/coding-agent/about-coding-agent>).

Try out our new agentic products with [GitHub Copilot](<https://github.com/features/copilot>) >

---

## Tags:

* [agentic AI](<https://github.blog/tag/agentic-ai/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [Security](<https://github.blog/tag/security/>)

## Written by

![Rahul Zhade](<https://avatars.githubusercontent.com/u/21364815?v=4&s=200>)

### [Rahul Zhade](<https://github.blog/author/rzhade3/>)

[@rzhade3](<https://github.com/rzhade3>)


</details>
