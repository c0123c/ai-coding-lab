# Copilot 云端 Agent 的风险与防护

讨论未验证代码、推送权限、敏感信息、提示注入、工作可见性和自动运行风险，说明产品防护及使用边界。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-09-23](<https://github.com/github/docs/commit/fc2c908a7831570a31dd61732aa3ba4645559b1a>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/concepts/security-governance-and-network-settings/risks-and-mitigations.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/Copilot 云端 Agent 的风险与防护.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Risks and mitigations for GitHub Copilot cloud agent
shortTitle: Risks and mitigations
intro: How do {% data variables.copilot.copilot_cloud_agent %}'s built-in security protections mitigate known risks?
versions:
  feature: copilot
contentType: concepts
category:
  - Learn about Copilot
  - Manage Copilot for a team
  - Roll Copilot out at scale
redirect_from:
  - /copilot/concepts/agents/coding-agent/risks-and-mitigations
  - /copilot/concepts/agents/cloud-agent/risks-and-mitigations
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

{% data variables.copilot.copilot_cloud_agent %} 是一个自治的 agent，它可以访问您的代码并可以将更改推送到您的存储库。这会带来一定的风险。

在可能的情况下，{% data variables.product.github %} 已应用适当的缓解措施。这为 {% data variables.copilot.copilot_cloud_agent %} 提供了内置安全保护的强大基础，您可以通过遵循最佳实践指南来补充。

## 未经验证的代码可能会引入漏洞

{% data reusables.copilot.cloud-agent-validation-tools-intro %} {% data variables.copilot.copilot_cloud_agent %} 的安全验证 ** 不需要 ** {% data variables.product.prodname_GHAS_cs_or_sp %} 许可证。

* **{% data variables.product.prodname_codeql %}**用于识别代码安全问题。
* 新引入的依赖项将根据 **{% data variables.product.prodname_advisory_database %}** 检查恶意软件建议以及任何 CVSS- 评级的高或严重漏洞。
* **{% data variables.product.prodname_secret_scanning_caps %}** 用于检测敏感信息，例如 API 密钥、令牌和其他秘密。
* 有关 {% data variables.copilot.copilot_cloud_agent %} 执行的分析和采取的操作的详细信息可以在会话日志中查看。参见[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/use-copilot-agents/manage-and-track-agents>)。

（可选）您可以禁用 {% data variables.copilot.copilot_cloud_agent %} 使用的一种或多种代码质量和安全验证工具。参见[AUTOTITLE](<https://github.com/copilot/how-tos/use-copilot-agents/cloud-agent/configuring-agent-settings>)。

## {% data variables.copilot.copilot_cloud_agent %} 可以将代码更改推送到您的存储库

为了减轻这种风险，{% data variables.product.github %}：

* **限制谁可以触发agent。**只有对存储库具有写入权限的用户才能触发{% data variables.copilot.copilot_cloud_agent %}工作。没有写入权限的用户的评论永远不会提交给 agent。
* **限制agent可以推送到的分支。** {% data variables.copilot.copilot_cloud_agent %}只能推送到单个分支。当通过在现有拉取请求中提及 `@copilot` 来触发 agent 时，{% data variables.product.prodname_copilot_short %} 具有对该拉取请求分支的写访问权限。在其他情况下，为{% data variables.product.prodname_copilot_short %}创建一个新的`copilot/`分支，并且agent只能推送到该分支。 agent 还受到任何分支保护和工作存储库所需的检查。
* **限制agent的凭据。** {% data variables.copilot.copilot_cloud_agent %}只能执行简单的推送操作。它不能直接运行`git push`或其他Git命令。
* **合并前需要人工审核。**{% data variables.copilot.copilot_cloud_agent %} 创建的草稿拉取请求必须由人工审核和合并。 {% data variables.copilot.copilot_cloud_agent %} 无法将其拉取请求标记为“准备审核”，并且无法批准或合并拉取请求。
* **限制 {% data variables.product.prodname_actions %} 工作流运行。** 默认情况下，只有在审核 {% data variables.copilot.copilot_cloud_agent %} 的代码并且对存储库具有写访问权限的用户单击 **批准并运行工作流 ** 按钮时，才会触发工作流。或者，您可以配置 {% data variables.product.prodname_copilot_short %} 以允许工作流程自动运行。请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/use-copilot-agents/review-copilot-output#manage-github-actions-workflow-runs>)。
* ** 阻止要求 {% data variables.copilot.copilot_cloud_agent %} 创建拉取请求的用户批准它。** 这将维护“所需批准”规则和分支保护中的预期控制。参见[AUTOTITLE](<https://github.com/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/available-rules-for-rulesets>)。
* **当拉取请求不属于某个人时，需要额外的批准。**当{% data variables.copilot.copilot_cloud_agent %}以其自己的应用程序身份打开拉取请求时，只要存储库已经需要至少一项批准，则在合并之前还需要一项批准。默认情况下，此功能在规则集中启用，管理员可以将其关闭，并且始终适用于分支保护规则。参见[AUTOTITLE](<https://github.com/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/available-rules-for-rulesets#additional-approval-for-unattributed-copilot-pull-requests>)。

## {% data variables.copilot.copilot_cloud_agent %} 有权访问敏感信息

{% data variables.copilot.copilot_cloud_agent %} 可以访问代码和其他敏感信息，并且可能会意外或由于恶意用户输入而泄露这些信息。

为了减轻这种风险，{% data variables.product.github %} **限制{% data variables.copilot.copilot_cloud_agent %}访问互联网**。请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-the-firewall>)。

## AI 提示可能容易受到注入攻击

用户可以在分配给{% data variables.copilot.copilot_cloud_agent %}的问题中包含隐藏消息或为{% data variables.copilot.copilot_cloud_agent %}留下的评论，以[提示注入](<https://genai.owasp.org/llmrisk/llm01-prompt-injection/>)的形式。

为了减轻这种风险，{% data variables.product.github %} ** 在将用户输入传递到 {% data variables.copilot.copilot_cloud_agent %}** 之前过滤隐藏字符：例如，在问题或拉取请求评论中作为 HTML 评论输入的文本不会传递到 {% data variables.copilot.copilot_cloud_agent %}。

## 管理员可能会忽视 agents 的工作

为了减轻这种风险，{% data variables.copilot.copilot_cloud_agent %} 被设计为可审计和可追踪的。

* {% data variables.copilot.copilot_cloud_agent %} 的提交由 {% data variables.product.prodname_copilot_short %} 编写，分配问题或请求更改拉取请求的开发人员标记为共同作者。这样可以更轻松地识别 {% data variables.copilot.copilot_cloud_agent %} 生成的代码以及谁启动了该任务。
* {% data variables.copilot.copilot_cloud_agent %} 的提交已签名，因此它们在 {% data variables.product.github %} 上显示为“已验证”。这让我们确信这些提交是由 {% data variables.copilot.copilot_cloud_agent %} 做出的并且未被更改。
* 管理员可以使用会话日志和审核日志事件。
* 每个 agent 创作的提交的提交消息都包含指向 agent 会话日志的链接，用于代码审查和审计。请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/use-copilot-agents/manage-and-track-agents>)。

## 自动化运行无需人员启动每项任务

{% data variables.copilot.copilot_automations_cap %} 按计划或响应事件自动运行 {% data variables.copilot.copilot_cloud_agent %}，无需人工启动每项任务。请参阅[AUTOTITLE](<https://github.com/copilot/concepts/agents/cloud-agent/about-automations>)。这引入了一些额外的风险，{% data variables.product.github %} 缓解如下。

* ** 工作归属于创建自动化的人员。** 由 {% data variables.copilot.copilot_automation %} 打开的拉取请求和推送的代码归属于创建 {% data variables.copilot.copilot_automation %} 的用户。当该用户自己创建拉取请求时，他们无法批准它，这保留了预期的“必需批准”控制。
* **您控制自动化可以使用哪些工具。**当事件触发{% data variables.copilot.copilot_automation %}时，来自不受信任用户的输入可能会成为提示的一部分。为了限制提示注入的影响，您可以准确选择 {% data variables.copilot.copilot_automation %} 可以使用哪些工具，因此它只能执行任务所需的操作。
* **默认情况下会忽略来自不受信任用户的事件。** {% data variables.copilot.copilot_automations_cap %}默认情况下会忽略对存储库没有写访问权限的用户触发的事件，并设置选择加入。
* **工作流程仍然需要人工批准。** {% data variables.copilot.copilot_automation %} 提出的问题或拉取请求可能会触发另一个 {% data variables.copilot.copilot_automation %}。与所有 {% data variables.copilot.copilot_cloud_agent %} 工作一样，{% data variables.product.prodname_actions %} 工作流不会在拉取请求上运行，直到具有写入权限的用户批准它们，这会阻止工作流作为此类链的一部分自动运行。


## 原文对照

<details>
<summary>展开完整原文</summary>

{% data variables.copilot.copilot_cloud_agent %} is an autonomous agent that has access to your code and can push changes to your repository. This entails certain risks.

Where possible, {% data variables.product.github %} has applied appropriate mitigations. This gives {% data variables.copilot.copilot_cloud_agent %} a strong base of built-in security protections that you can supplement by following best practice guidance.

## Unvalidated code can introduce vulnerabilities

{% data reusables.copilot.cloud-agent-validation-tools-intro %} {% data variables.copilot.copilot_cloud_agent %}'s security validation **does not require** a {% data variables.product.prodname_GHAS_cs_or_sp %} license.

* **{% data variables.product.prodname_codeql %}** is used to identify code security issues.
* Newly introduced dependencies are checked against the **{% data variables.product.prodname_advisory_database %}** for malware advisories, and for any CVSS-rated High or Critical vulnerabilities.
* **{% data variables.product.prodname_secret_scanning_caps %}** is used to detect sensitive information such as API keys, tokens, and other secrets.
* Details about the analysis performed and the actions taken by {% data variables.copilot.copilot_cloud_agent %} can be reviewed in the session log. See [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/use-copilot-agents/manage-and-track-agents>).

Optionally, you can disable one or more of the code quality and security validation tools used by {% data variables.copilot.copilot_cloud_agent %}. See [AUTOTITLE](<https://github.com/copilot/how-tos/use-copilot-agents/cloud-agent/configuring-agent-settings>).

## {% data variables.copilot.copilot_cloud_agent %} can push code changes to your repository

To mitigate this risk, {% data variables.product.github %}:

* **Limits who can trigger the agent.** Only users with write access to the repository can trigger {% data variables.copilot.copilot_cloud_agent %} to work. Comments from users without write access are never presented to the agent.
* **Limits the branch the agent can push to.** {% data variables.copilot.copilot_cloud_agent %} only has the ability to push to a single branch. When the agent is triggered by mentioning `@copilot` on an existing pull request, {% data variables.product.prodname_copilot_short %} has write access to the pull request's branch. In other cases, a new `copilot/` branch is created for {% data variables.product.prodname_copilot_short %}, and the agent can only push to that branch. The agent is also subject to any branch protections and required checks for the working repository.
* **Limits the agent's credentials.** {% data variables.copilot.copilot_cloud_agent %} can only perform simple push operations. It cannot directly run `git push` or other Git commands.
* **Requires human review before merging.** Draft pull requests created by {% data variables.copilot.copilot_cloud_agent %} must be reviewed and merged by a human. {% data variables.copilot.copilot_cloud_agent %} cannot mark its pull requests as "Ready for review" and cannot approve or merge a pull request.
* **Restricts {% data variables.product.prodname_actions %} workflow runs.** By default, workflows are not triggered until {% data variables.copilot.copilot_cloud_agent %}'s code is reviewed and a user with write access to the repository clicks the **Approve and run workflows** button. Optionally, you can configure {% data variables.product.prodname_copilot_short %} to allow workflows to run automatically. See [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/use-copilot-agents/review-copilot-output#manage-github-actions-workflow-runs>).
* **Prevents the user who asked {% data variables.copilot.copilot_cloud_agent %} to create a pull request from approving it.** This maintains the expected controls in the "Required approvals" rule and branch protection. See [AUTOTITLE](<https://github.com/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/available-rules-for-rulesets>).
* **Requires an additional approval when a pull request isn't attributed to a person.** When {% data variables.copilot.copilot_cloud_agent %} opens a pull request under its own app identity, one more approval is required before it can be merged, as long as the repository already requires at least one approval. This is enabled by default in rulesets, where administrators can turn it off, and always applies to branch protection rules. See [AUTOTITLE](<https://github.com/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/available-rules-for-rulesets#additional-approval-for-unattributed-copilot-pull-requests>).

## {% data variables.copilot.copilot_cloud_agent %} has access to sensitive information

{% data variables.copilot.copilot_cloud_agent %} has access to code and other sensitive information, and could leak it, either accidentally or due to malicious user input.

To mitigate this risk, {% data variables.product.github %} **restricts {% data variables.copilot.copilot_cloud_agent %}'s access to the internet**. See [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-the-firewall>).

## AI prompts can be vulnerable to injection

Users can include hidden messages in issues assigned to {% data variables.copilot.copilot_cloud_agent %} or comments left for {% data variables.copilot.copilot_cloud_agent %} as a form of [prompt injection](<https://genai.owasp.org/llmrisk/llm01-prompt-injection/>).

To mitigate this risk, {% data variables.product.github %} **filters hidden characters before passing user input to {% data variables.copilot.copilot_cloud_agent %}**: For example, text entered as an HTML comment in an issue or pull request comment is not passed to {% data variables.copilot.copilot_cloud_agent %}.

## Administrators can lose sight of agents' work

To mitigate this risk, {% data variables.copilot.copilot_cloud_agent %} is designed to be auditable and traceable.

* {% data variables.copilot.copilot_cloud_agent %}'s commits are authored by {% data variables.product.prodname_copilot_short %}, with the developer who assigned the issue or requested the change to the pull request marked as the co-author. This makes it easier to identify code generated by {% data variables.copilot.copilot_cloud_agent %} and who started the task.
* {% data variables.copilot.copilot_cloud_agent %}'s commits are signed, so they appear as "Verified" on {% data variables.product.github %}. This provides confidence that the commits were made by {% data variables.copilot.copilot_cloud_agent %} and have not been altered.
* Session logs and audit log events are available to administrators.
* The commit message for each agent-authored commit includes a link to the agent session logs, for code review and auditing. See [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/use-copilot-agents/manage-and-track-agents>).

## Automations run without a person initiating each task

{% data variables.copilot.copilot_automations_cap %} run {% data variables.copilot.copilot_cloud_agent %} automatically, on a schedule or in response to events, without a person initiating each task. See [AUTOTITLE](<https://github.com/copilot/concepts/agents/cloud-agent/about-automations>). This introduces some additional risks, which {% data variables.product.github %} mitigates as follows.

* **Work is attributed to the person who created the automation.** Pull requests opened and code pushed by an {% data variables.copilot.copilot_automation %} are attributed to the user who created the {% data variables.copilot.copilot_automation %}. As when that user creates a pull request themselves, they can't approve it, which preserves the expected "Required approvals" controls.
* **You control which tools an automation can use.** When an {% data variables.copilot.copilot_automation %} is triggered by an event, input from untrusted users could become part of the prompt. To limit the impact of prompt injection, you choose exactly which tools an {% data variables.copilot.copilot_automation %} can use, so it can only take the actions the task requires.
* **Events from untrusted users are ignored by default.** {% data variables.copilot.copilot_automations_cap %} ignore events triggered by users without write access to the repository by default, with a setting to opt in.
* **Workflows still require human approval.** An issue or pull request opened by an {% data variables.copilot.copilot_automation %} could trigger another {% data variables.copilot.copilot_automation %}. As with all {% data variables.copilot.copilot_cloud_agent %} work, {% data variables.product.prodname_actions %} workflows don't run on a pull request until a user with write access approves them, which prevents workflows from running automatically as part of such a chain.


</details>
