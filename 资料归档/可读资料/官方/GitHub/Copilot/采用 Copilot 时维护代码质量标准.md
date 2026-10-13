# 采用 Copilot 时维护代码质量标准

围绕强制 PR 与审查、测试、安全扫描、项目指导、失败应对和质量检查，讨论团队应保留的验证环节。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-09-15](<https://github.com/github/docs/commit/1413b140620171c5e4494c52e28a736e4b68b36e>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/tutorials/roll-out-at-scale/govern-at-scale/maintain-codebase-standards.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/采用 Copilot 时维护代码质量标准.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Maintaining codebase standards in a GitHub Copilot rollout
intro: Stay in control of your enterprise's code with rulesets, security features, and effective training.
permissions: Enterprise owners
versions:
  feature: copilot
shortTitle: Maintain codebase standards
category:
  - Roll Copilot out at scale
  - Manage Copilot for a team
contentType: tutorials
redirect_from:
  - /copilot/tutorials/roll-out-at-scale/maintain-codebase-standards
docsTeamMetrics:
  - ai-governance
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

大多数企业都意识到 AI 编码工具可以带来的生产力优势。然而，许多人担心公司中的不当使用，例如恶意提示或开发人员未经审查就接受AI建议，将导致其代码库的标准受到损害。

您可以通过设置 {% data variables.product.github %} 环境和工作文化以进行有效治理来减轻这些风险。 {% data variables.product.prodname_copilot %} 的一个主要好处是它内置于 {% data variables.product.github %} 平台中，该平台已经包含一系列企业级代码治理功能。

## 1。需要拉取请求和评论

开发人员和不良行为者永远不应该能够单方面将未经审查的 AI 建议或 agent 直接应用于敏感代码库。在用户将代码合并到生产代码库和其他重要分支之前，您应该需要 ** 批准的拉取请求 **。

为此，请创建一个规则集：

1. 识别包含您要保护的代码库的组织或存储库，并**对其应用自定义属性**。这将使您能够轻松地在规则集中定位这些资源。请参阅[AUTOTITLE](<https://github.com/organizations/managing-organization-settings/managing-custom-properties-for-repositories-in-your-organization>)或[AUTOTITLE](<https://github.com/enterprise-cloud@latest/admin/managing-accounts-and-repositories/managing-organizations-in-your-enterprise/managing-custom-properties-for-organizations>)。

或者，您可以手动将这些受保护的资源添加到规则集，或根据命名约定来定位它们。

1. 为您的企业创建分支规则集。参见[AUTOTITLE](<https://github.com/enterprise-cloud@latest/admin/enforcing-policies/enforcing-policies-for-your-enterprise/enforcing-policies-for-code-governance#creating-a-branch-or-tag-ruleset>)。

   * 至少启用 **在合并之前需要拉取请求 ** 和 **阻止强制推送 ** 规则。在“需要拉取请求”规则下，确保您至少需要一项批准。
   * 如果需要，启用其他规则。例如，如果您担心坏人劫持拉取请求，请确保在推送新提交时**驳回过时的拉取请求批准**。

1. 鼓励存储库管理员为存储库中的特定文件设置 CODEOWNERS 文件。当这些文件被修改时，这将自动请求代码所有者进行审查。

然后，您可以返回规则集并启用 ** 需要代码所有者审核 ** 规则。

1. 鼓励组织所有者和存储库管理员创建更具体的规则集，因为他们可能更了解自己的代码的要求。

这些规则集将添加到您在企业级别定义的基线，但永远不会覆盖它。

## 2。测试你的代码

良好的 DevOps 实践可确保您的代码在合并和部署之前自动进行测试，从而最大限度地降低进入默认分支并在生产环境中出现错误的风险。

1. 启用{% data variables.product.prodname_actions %}或另一个CI/CD系统。
1. 鼓励开发人员为所有功能编写测试并将测试集成到 {% data variables.product.prodname_actions %} 工作流程中。
1. 鼓励组织所有者或存储库所有者创建规则集并将重要工作流添加到 **要求工作流在合并之前通过 ** 规则。

## 3。扫描代码是否存在漏洞

{% data variables.product.prodname_copilot_short %} 旨在避免将漏洞引入您的代码库。例如，由 {% data variables.copilot.copilot_cloud_agent %} 创建的代码会自动扫描是否存在易受攻击的模式和秘密，例如 API 密钥。

但是，定期扫描所有代码是否存在漏洞和机密，并从一开始就防止开发人员引入漏洞是一种很好的做法。

1. 作为起点，在您的组织上应用并强制执行基本的 ** 安全配置 **。这是安全功能启用设置的集合。我们建议包括 {% data variables.product.prodname_code_scanning %}、{% data variables.product.prodname_secret_scanning %} 和机密推送保护。请参阅[AUTOTITLE](<https://github.com/code-security/how-tos/secure-at-scale/configure-organization-security/establish-complete-coverage/create-custom-configuration#creating-a-secret-protection-and-code-security-configuration>)。
1. 当您更多地了解自己的需求时，请创建其他自定义配置或在存储库级别应用精细设置。
1. 要对拉取请求强制执行 {% data variables.product.prodname_code_scanning %}，请返回到您的规则集并启用 **需要 {% data variables.product.prodname_code_scanning %} 结果 ** 规则。

## 4。为 {% data variables.product.prodname_copilot_short %} 创建指南

为了首先提高 {% data variables.product.prodname_copilot_short %} 建议的质量，您应该创建自定义说明。这些说明为所有提示添加了上下文，告诉 {% data variables.product.prodname_copilot_short %} 遵循贵公司的编码标准。

1. 要建立良好的基线，请在组织级别创建**自定义指令**。这些可以是可能适用于任何存储库的高级标准。但请注意，这些说明仅适用于 {% data variables.product.github %} 网站。参见[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-organization-instructions>)。
1. 为了获得更完整的覆盖范围，鼓励开发人员和存储库管理员**为特定存储库编写自定义指令**。这些适用于比组织指令更多的地方，并且可以更详细地了解每个项目及其要求。请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions>)。

## 5。鼓励最佳实践

有了强大的护栏，开发人员应该已经能够有效地使用 AI。然而，提供有关 AI 工具的培训并创建一种鼓励而不是强制执行最佳实践的文化非常重要。

1. 传达您的治理设置以及您的公司对开发人员应如何使用 {% data variables.product.prodname_copilot_short %} 的期望。例如，如果所有 agent 工作都应进行彻底审查，请确保建立并传达此流程。
1. 鼓励开发人员在打开拉取请求或请求审查之前在 {% data variables.copilot.copilot_cli_short %} 中运行 `/security-review`。将此视为轻量级检查，然后继续执行标准拉取请求审核流程。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/cli-best-practices#code-review-assistance>)。
1. 创建入职资源，例如内部文档或视频。首先，共享现有资源，例如 [AUTOTITLE](<https://github.com/copilot/get-started/best-practices>) 和 [AUTOTITLE](<https://github.com/copilot/tutorials/copilot-cookbook>)。
1. 提供持续的培训和支持，例如研讨会。在成功的推广中，许多公司都确定了可以培训其他人如何有效使用 {% data variables.product.prodname_copilot_short %} 的“冠军”。

## 6。做最坏的打算

即使采取了最严格的防护措施，无论您的开发人员是否使用 AI 工具，仍然有可能合并易受攻击或容易出错的代码。

为了准备这些场景，您应该计划如何解决问题并与开发人员沟通该计划。例如：

1. 恢复错误的拉取请求并回滚部署。
1. 创建一个讨论帖子，分析出了什么问题以及将来如何避免它。
1. 检查审核日志中是否存在规则集绕过、不正确的权限或治理设置更改等情况。

## 7。检查代码质量

如果您对自己的治理模型充满信心，但仍然担心 {% data variables.product.prodname_copilot_short %} 会随着时间的推移降低代码库的质量，那么您可以在部署过程中对此进行衡量。如果启用，{% data variables.product.prodname_code_quality %} 会提供有关存储库代码运行状况的指标。请参阅[AUTOTITLE](<https://github.com/code-security/concepts/code-quality/code-quality>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

Most enterprises are aware of the productivity benefits that AI coding tools can bring. However, many worry that improper usage in their company, such as malicious prompts or developers accepting AI suggestions without review, will lead to their codebase's standards being compromised.

You can mitigate these risks by setting up your {% data variables.product.github %} environment and work culture for effective governance. A major benefit of {% data variables.product.prodname_copilot %} is that it is built into the {% data variables.product.github %} platform, which already contains a range of features for enterprise-grade code governance.

## 1. Require pull requests and reviews

Developers and bad actors should never be able to unilaterally apply unvetted AI suggestions or agent work directly to sensitive codebases. You should require an **approved pull request** before users can merge code into production codebases and other important branches.

To do this, create a ruleset:

1. Identify organizations or repositories that contain the codebases you want to protect, and **apply a custom property** to them. This will allow you to easily target those resources in a ruleset. See [AUTOTITLE](<https://github.com/organizations/managing-organization-settings/managing-custom-properties-for-repositories-in-your-organization>) or [AUTOTITLE](<https://github.com/enterprise-cloud@latest/admin/managing-accounts-and-repositories/managing-organizations-in-your-enterprise/managing-custom-properties-for-organizations>).

   Alternatively, you can add these protected resources to a ruleset manually, or target them based on a naming convention.

1. Create a branch ruleset for your enterprise. See [AUTOTITLE](<https://github.com/enterprise-cloud@latest/admin/enforcing-policies/enforcing-policies-for-your-enterprise/enforcing-policies-for-code-governance#creating-a-branch-or-tag-ruleset>).

   * Enable at least the **Require a pull request before merging** and **Block force pushes** rules. Under the "Require a pull request" rule, ensure you require at least one approval.
   * Enable other rules if required. For example, if you're concerned about bad actors hijacking pull requests, make sure you **dismiss stale pull request approvals** when new commits are pushed.

1. Encourage repository administrators to set up CODEOWNERS files for specific files in repositories. This will automatically request a review from the code owners when those files are modified.

   Then, you can go back to your ruleset and enable the **Require review from Code Owners** rule.

1. Encourage organization owners and repository administrators to create more specific rulesets, as they're likely to have more awareness of requirements for their own code.

   These rulesets will add to the baseline that you define at the enterprise level, but will never override it.

## 2. Test your code

Good DevOps practices ensure that your code is automatically tested before being merged and deployed, minimizing the risks of errors entering your default branches and surfacing in production environments.

1. Enable {% data variables.product.prodname_actions %} or another CI/CD system.
1. Encourage developers to write tests for all features and integrate tests into {% data variables.product.prodname_actions %} workflows.
1. Encourage organization owners or repository owners to create rulesets and add important workflows to the **Require workflows to pass before merging** rule.

## 3. Scan code for vulnerabilities

{% data variables.product.prodname_copilot_short %} is already designed to avoid introducing vulnerabilities into your codebase. For example, code created by {% data variables.copilot.copilot_cloud_agent %} is automatically scanned for vulnerable patterns and secrets such as API keys.

However, it is good practice to regularly scan all code for vulnerabilities and secrets, and to prevent developers from introducing vulnerabilities in the first place.

1. As a starting point, apply and enforce a basic **security configuration** on your organizations. This is a collection of enablement settings for security features. We recommend including {% data variables.product.prodname_code_scanning %}, {% data variables.product.prodname_secret_scanning %}, and secrets push protection. See [AUTOTITLE](<https://github.com/code-security/how-tos/secure-at-scale/configure-organization-security/establish-complete-coverage/create-custom-configuration#creating-a-secret-protection-and-code-security-configuration>).
1. As you learn more about your needs, create additional custom configurations or apply granular settings at the repository level.
1. To enforce {% data variables.product.prodname_code_scanning %} on pull requests, go back to your ruleset and enable the **Require {% data variables.product.prodname_code_scanning %} results** rule.

## 4. Create guidelines for {% data variables.product.prodname_copilot_short %}

To improve the quality of {% data variables.product.prodname_copilot_short %}'s suggestions in the first place, you should create custom instructions. These instructions add context to all prompts that tells {% data variables.product.prodname_copilot_short %} to follow your company's coding standards.

1. To establish a good baseline, create **custom instructions at the organization level**. These can be high-level standards that are likely to apply to any repository. However, note that these instructions only apply on the {% data variables.product.github %} website. See [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-organization-instructions>).
1. For more complete coverage, encourage developers and repository administrators to **write custom instructions for specific repositories**. These apply in more places than organization instructions, and can go into more detail about each project and its requirements. See [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions>).

## 5. Encourage best practices

With strong guardrails in place, developers should already be enabled to use AI effectively. However, it is important to provide training on AI tools and create a culture where best practices are encouraged, rather than just enforced.

1. Communicate your governance settings and your company's expectations for how developers should use {% data variables.product.prodname_copilot_short %}. For example, if all agent work should be thoroughly reviewed, ensure this process is established and communicated.
1. Encourage developers to run `/security-review` in {% data variables.copilot.copilot_cli_short %} before opening a pull request or requesting review. Treat this as a lightweight check, then continue with your standard pull request review process. For more information, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/cli-best-practices#code-review-assistance>).
1. Create onboarding resources such as internal documentation or videos. For a starting point, share existing resources like [AUTOTITLE](<https://github.com/copilot/get-started/best-practices>) and [AUTOTITLE](<https://github.com/copilot/tutorials/copilot-cookbook>).
1. Offer ongoing training and support, such as workshops. In successful rollouts, many companies identify "champions" who can train others on how to use {% data variables.product.prodname_copilot_short %} effectively.

## 6. Plan for the worst

Even with the strictest guardrails in place, it is always possible that vulnerable or error-prone code will be merged, regardless of whether your developers are using AI tools.

To prepare for these scenarios, you should plan for how you will address problems and communicate this plan with developers. For example:

1. Revert a bad pull request and roll back a deployment.
1. Create a discussion post analyzing what went wrong and how to avoid it in the future.
1. Check audit logs for things like ruleset bypasses, incorrect permissions, or changes to governance settings.

## 7. Check code quality

If you're confident in your governance model but still concerned that {% data variables.product.prodname_copilot_short %} will reduce the quality of your codebase over time, you can measure this over the course of a rollout. If enabled, {% data variables.product.prodname_code_quality %} provides metrics on the code health of your repositories. See [AUTOTITLE](<https://github.com/code-security/concepts/code-quality/code-quality>).


</details>
