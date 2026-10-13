# 用 Copilot 减少团队安全欠账

讨论开发阶段漏洞分析、存量告警修复、团队实施和效果指标，供安排安全维护流程时参考。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-09-03](<https://github.com/github/docs/commit/6820b60091a0121ed813dae4408aa33934abd272>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/tutorials/roll-out-at-scale/drive-downstream-impact/reduce-security-debt.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/用 Copilot 减少团队安全欠账.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Reducing security debt in your company with GitHub Copilot
shortTitle: Reduce security debt
intro: 'Understand features, enable developers, and measure {% data variables.product.prodname_copilot_short %}''s impact.'
versions:
  feature: copilot
product: '{% data variables.copilot.copilot_for_business %} or {% data variables.copilot.copilot_enterprise %}'
allowTitleToDifferFromFilename: true
redirect_from:
  - /copilot/tutorials/rolling-out-github-copilot-at-scale/driving-downstream-impact/reduce-security-debt
  - /copilot/tutorials/rolling-out-github-copilot-at-scale/drive-downstream-impact/reduce-security-debt
contentType: tutorials
category:
  - Roll Copilot out at scale
  - Manage Copilot for a team
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

{% data reusables.copilot.essp-intro %}

## 1。识别成功的障碍

{% data reusables.copilot.identify-barriers-intro %}

开发团队通常关注速度和功能以提供新功能并保持应用程序平稳运行。随着时间的推移，小问题可能会累积，例如：

* 尚未修复的已知安全漏洞
* 依赖具有潜在缺陷的旧软件组件
* 延迟解决已发现的问题

这会产生**担保债务**，大量积压的问题。

担保债务带来真正的风险。它得不到解决的时间越长，规模就越大，成本也越高。巨额安全债务使系统容易受到攻击、暴露敏感数据并削弱客户信任。

挑战在于平衡快速开发与维护安全稳定的软件环境。

## 2。评估您的选择

{% data reusables.copilot.evaluate-options-intro %}

<a href="https://github.com/github-copilot/purchase?ref_product=copilot&ref_type=trial&ref_style=button" target="_blank" class="btn btn-primary mt-3 mr-3 no-underline"><span>注册{% data variables.product.prodname_copilot_short %}</span>{% octicon "link-external" height:16 aria-label="link-external" %}</a>

### {% data variables.product.prodname_copilot_short %} 如何提供帮助

{% data variables.product.prodname_copilot_short %} 将安全考虑因素直接集成到开发生命周期中。这有助于开发人员主动识别和解决潜在的漏洞，同时保持项目最新。

{% data variables.product.prodname_copilot_short %} 减少整个软件开发生命周期的安全漏洞。

#### 开发期间

{% data variables.product.prodname_copilot_short %} 在编写代码时对其进行审查。它利用对常见安全缺陷的理解来标记可能容易受到利用的区域。这种实时分析揭示了标准开发或初始安全审查期间可能会遗漏的隐藏漏洞。

当 {% data variables.product.prodname_copilot_short %} 发现问题时，它会建议更改代码以修复漏洞。这使您能够及早解决弱点并防止担保债务累积。

#### 持续维护

{% data variables.product.prodname_copilot_short %} 与 {% data variables.product.github %} 的代码扫描功能集成，以确保您现有代码库的安全。当代码扫描发现安全警报时，{% data variables.copilot.copilot_autofix_short %} 会对其进行分析并提供有针对性的解决建议。

这些建议的修复措施可以减少您花在研究漏洞和确定如何解决这些漏洞上的时间。这可以帮助您更有效地解决安全警报并防止持续的安全债务。

### 文化考虑

{% data reusables.copilot.cultural-factors-intro %}

* 团队可能**忽略或推迟担保债务**。这使得低效且脆弱的系统持续存在。这可能是由于对功能的最后期限驱动的关注或缺乏对安全债务的长期影响的教育造成的。
* 团队可能会**为简单的问题构建过于复杂的解决方案**。这使得代码更难维护，安全问题更难检测。这可能是由于不必要地面向未来的愿望或通过复杂性增加价值的压力造成的。

## 3。实施变革

{% data reusables.copilot.implement-changes-intro %}

* [分析您的代码是否存在安全漏洞](<#分析您的代码是否存在安全漏洞>)
* [使用 {% data variables.copilot.copilot_autofix_short %} 进行 {% data variables.product.prodname_code_scanning %} 警报](<#use-copilot-autofix-for-code-scanning-alerts>)
* [开发者最佳实践](<#开发人员的最佳实践>)
* [开发者资源](<#开发人员资源>)

### 分析您的代码是否存在安全漏洞

根据代码库的大小，{% data variables.product.prodname_copilot_short %} 在您编写代码时可能无法分析整个项目。这是由于上下文的限制。但是，您可以要求它分析特定文件是否存在不安全的代码实践。

1. 打开{% data variables.product.prodname_vscode %}中的文件进行分析。
1. 在{% data variables.copilot.copilot_chat_short %}中，询问：`Analyze this code for potential security vulnerabilities and suggest fixes`

使用 `#file` 聊天变量在提示中专门包含文件的内容。您还可以使用提示文件和自定义说明来指导 {% data variables.product.prodname_copilot_short %} 的响应。

1. {% data variables.copilot.copilot_chat_short %} 分析代码、识别安全漏洞并提出修复建议。
1. 查看建议的更改并酌情应用它们。

其他提示示例：
* `Are there any security vulnerabilities in my code? If so, can you explain them and suggest fixes?`
* `Does this code follow secure code best practices? If not, what specific improvements can I make?`
* `What are the potential security risks in this code if it were deployed to production? How can I mitigate them?`

### 使用 {% data variables.copilot.copilot_autofix_short %} 作为 {% data variables.product.prodname_code_scanning %} 警报

{% data variables.copilot.copilot_autofix_short %} 是 {% data variables.product.prodname_GH_code_security %} 的一部分，建议对 {% data variables.product.prodname_code_scanning %} 警报进行潜在修复。它可以在公共存储库和具有 {% data variables.product.prodname_GH_code_security %} 许可证的存储库中使用。

当您在存储库上运行代码扫描时，潜在问题会以 {% data variables.product.prodname_code_scanning %} 警报的形式出现。请按照以下步骤解决警报：

1. 在 GitHub 上打开警报。
1. 单击**生成修复**。当 Copilot 可以解决警报时显示此信息。
1. {% data variables.copilot.copilot_autofix_short %} 生成一个潜在的修复程序并向您显示警报中的代码更改。您可以将此代码更改提交到新分支或现有分支。
1. 测试代码。然后打开拉取请求以将更改移至主分支。
1. 将更改移至主分支并且 {% data variables.product.prodname_code_scanning %} 验证修复后，警报将自动关闭。

### 开发人员的最佳实践

开发者**应该**：

* **定期使用{% data variables.copilot.copilot_chat_short %}分析代码片段是否存在漏洞**。养成在提交更改之前检查代码是否存在安全问题的习惯。
* **使用{% data variables.copilot.copilot_autofix_short %}作为{% data variables.product.prodname_code_scanning %}警报**。当出现警报时，首先使用 {% data variables.copilot.copilot_autofix_short %} 来快速解决它们。
* **向{% data variables.copilot.copilot_chat_short %}**提供清晰具体的提示。您的请求越详细，{% data variables.product.prodname_copilot_short %} 就能更好地分析代码并提出相关修复建议。例如，包括编程语言和特定的关注领域。
* **将{% data variables.product.prodname_copilot_short %}与现有安全工具**相结合。使用 {% data variables.product.prodname_copilot_short %} 作为安全分析的附加层，而不是作为专用安全扫描仪和实践的替代品。

开发者**不应该**：

* **自动接受{% data variables.product.prodname_copilot_short %}的安全建议**。始终审查和测试建议的代码更改，以确保它们适当且有效。
* **仅依靠{% data variables.product.prodname_copilot_short %}进行全面的安全审核**。 {% data variables.product.prodname_copilot_short %} 是一个有用的工具，但它不应取代彻底的安全审查和渗透测试。
* **忽略{% data variables.product.prodname_code_scanning %}警报**。及时处理所有警报，即使它们看起来很小，以防止安全债务的积累。
* **使用{% data variables.product.prodname_copilot_short %}作为借口来避免学习安全编码实践**。 Continue 让您自己和您的团队了解安全最佳实践。
* **假设{% data variables.product.prodname_copilot_short %}将捕获每个漏洞**。安全是一个持续的过程，始终需要保持警惕。
* **使用{% data variables.product.prodname_copilot_short %}绕过安全策略**。遵守组织的安全协议。使用 {% data variables.product.prodname_copilot_short %} 作为增强它们的工具，而不是规避它们。

### 开发人员资源

* [{% data variables.copilot.copilot_chat_dotcom_short %}](<https://github.com/copilot/how-tos/copilot-on-github/chat-with-copilot/chat-in-github>)
* [AUTOTITLE](<https://github.com/copilot/tutorials/copilot-cookbook/analyze-security/find-vulnerabilities>)
* [{% data variables.product.prodname_learning %} - 开始使用 {% data variables.product.prodname_copilot %}](<https://github.com/skills/getting-started-with-github-copilot>)

## 值得关注的指标

{% data reusables.copilot.measure-changes-intro %}

* **担保债务比率**。使用安全概览来查看警报数量是否随着时间的推移而下降。
* **是时候修复安全问题了**。使用安全概述来查看修复安全问题的时间是否会随着时间的推移而缩短。

参见[AUTOTITLE](<https://github.com/code-security/how-tos/view-and-interpret-data/analyze-organization-data/assessing-code-security-risk>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

{% data reusables.copilot.essp-intro %}

## 1. Identify barriers to success

{% data reusables.copilot.identify-barriers-intro %}

Development teams often focus on speed and functionality to deliver new features and keep applications running smoothly. Over time, small issues can accumulate, such as:

* Known security weaknesses that remain unfixed
* Reliance on older software components with potential flaws
* Delays in addressing discovered problems

This creates a **security debt**, a significant backlog of issues.

Security debt carries real risks. The longer it goes unaddressed, the larger and more costly it becomes. Large security debt leaves systems vulnerable to attacks, exposes sensitive data, and erodes customer trust.

The challenge is balancing rapid development with maintaining a secure and stable software environment.

## 2. Evaluate your options

{% data reusables.copilot.evaluate-options-intro %}

<a href="https://github.com/github-copilot/purchase?ref_product=copilot&ref_type=trial&ref_style=button" target="_blank" class="btn btn-primary mt-3 mr-3 no-underline"><span>Sign up for {% data variables.product.prodname_copilot_short %}</span>{% octicon "link-external" height:16 aria-label="link-external" %}</a>

### How {% data variables.product.prodname_copilot_short %} can help

{% data variables.product.prodname_copilot_short %} integrates security considerations directly into the development lifecycle. This helps developers proactively identify and address potential vulnerabilities while keeping projects up-to-date.

{% data variables.product.prodname_copilot_short %} reduces security vulnerabilities throughout the software development lifecycle.

#### During development

{% data variables.product.prodname_copilot_short %} reviews code as you write it. It uses its understanding of common security flaws to flag areas that might be vulnerable to exploitation. This real-time analysis surfaces hidden vulnerabilities that might be missed during standard development or initial security reviews.

When {% data variables.product.prodname_copilot_short %} identifies issues, it suggests code changes to fix vulnerabilities. This empowers you to address weaknesses early and prevent security debt from accumulating.

#### Ongoing maintenance

{% data variables.product.prodname_copilot_short %} integrates with {% data variables.product.github %}'s code scanning capabilities to keep your existing codebase secure. When code scanning identifies a security alert, {% data variables.copilot.copilot_autofix_short %} analyzes it and provides targeted recommendations to resolve it.

These suggested fixes reduce the time you spend researching vulnerabilities and determining how to address them. This helps you resolve security alerts more efficiently and prevents ongoing security debt.

### Cultural considerations

{% data reusables.copilot.cultural-factors-intro %}

* Teams might **ignore or defer security debt**. This allows inefficient and vulnerable systems to persist. This could be caused by a deadline-driven focus on features or a lack of education about the long-term impact of security debt.
* Teams might **build overly complex solutions** for simple problems. This makes code harder to maintain and security issues harder to detect. This could be caused by a desire to future-proof unnecessarily or by pressure to add value through complexity.

## 3. Implement changes

{% data reusables.copilot.implement-changes-intro %}

* [Analyze your code for security vulnerabilities](<#analyze-your-code-for-security-vulnerabilities>)
* [Use {% data variables.copilot.copilot_autofix_short %} for {% data variables.product.prodname_code_scanning %} alerts](<#use-copilot-autofix-for-code-scanning-alerts>)
* [Best practices for developers](<#best-practices-for-developers>)
* [Resources for developers](<#resources-for-developers>)

### Analyze your code for security vulnerabilities

Depending on the size of your codebase, {% data variables.product.prodname_copilot_short %} may not be able to analyze your entire project while you are writing code. This is due to context constraints. However, you can ask it to analyze specific files for insecure code practices.

1. Open the files to analyze in {% data variables.product.prodname_vscode %}.
1. In {% data variables.copilot.copilot_chat_short %}, ask: `Analyze this code for potential security vulnerabilities and suggest fixes`

   Use the `#file` chat variable to specifically include a file's content in the prompt. You can also use prompt files and custom instructions to guide {% data variables.product.prodname_copilot_short %}'s responses.

1. {% data variables.copilot.copilot_chat_short %} analyzes the code, identifies security vulnerabilities, and suggests fixes.
1. Review the suggested changes and apply them as appropriate.

Other examples of prompts:
* `Are there any security vulnerabilities in my code? If so, can you explain them and suggest fixes?`
* `Does this code follow secure code best practices? If not, what specific improvements can I make?`
* `What are the potential security risks in this code if it were deployed to production? How can I mitigate them?`

### Use {% data variables.copilot.copilot_autofix_short %} for {% data variables.product.prodname_code_scanning %} alerts

{% data variables.copilot.copilot_autofix_short %} is a part of {% data variables.product.prodname_GH_code_security %} that suggests potential fixes to {% data variables.product.prodname_code_scanning %} alerts. It is available in public repositories and repositories with a license for {% data variables.product.prodname_GH_code_security %}.

When you run a code scan on a repository, potential issues are raised as {% data variables.product.prodname_code_scanning %} alerts. Resolve the alerts by following these steps:

1. Open an alert on GitHub.
1. Click **Generate fix**. This displays when Copilot can resolve the alert.
1. {% data variables.copilot.copilot_autofix_short %} generates a potential fix and shows you the code changes in the alert. You can commit this code change to a new branch or an existing branch.
1. Test the code. Then open a pull request to move the changes to the main branch.
1. After you move the changes to the main branch and {% data variables.product.prodname_code_scanning %} verifies the fix, the alert closes automatically.

### Best practices for developers

Developers **should**:

* **Use {% data variables.copilot.copilot_chat_short %} regularly to analyze code snippets for vulnerabilities**. Make it a habit to check code for security issues before committing changes.
* **Use {% data variables.copilot.copilot_autofix_short %} for {% data variables.product.prodname_code_scanning %} alerts**. When alerts appear, use {% data variables.copilot.copilot_autofix_short %} as a first step to quickly address them.
* **Provide clear and specific prompts to {% data variables.copilot.copilot_chat_short %}**. The more detailed your request, the better {% data variables.product.prodname_copilot_short %} can analyze the code and suggest relevant fixes. For example, include the programming language and specific areas of concern.
* **Combine {% data variables.product.prodname_copilot_short %} with existing security tools**. Use {% data variables.product.prodname_copilot_short %} as an additional layer of security analysis, not as a replacement for dedicated security scanners and practices.

Developers **should not**:

* **Automatically accept {% data variables.product.prodname_copilot_short %}'s security suggestions**. Always review and test the suggested code changes to ensure they are appropriate and effective.
* **Rely solely on {% data variables.product.prodname_copilot_short %} for comprehensive security audits**. {% data variables.product.prodname_copilot_short %} is a helpful tool, but it should not replace thorough security reviews and penetration testing.
* **Ignore {% data variables.product.prodname_code_scanning %} alerts**. Address all alerts promptly, even if they seem minor, to prevent the accumulation of security debt.
* **Use {% data variables.product.prodname_copilot_short %} as an excuse to avoid learning secure coding practices**. Continue to educate yourself and your team on security best practices.
* **Assume {% data variables.product.prodname_copilot_short %} will catch every vulnerability**. Security is an ongoing process, and vigilance is always necessary.
* **Use {% data variables.product.prodname_copilot_short %} to bypass security policies**. Adhere to your organization's security protocols. Use {% data variables.product.prodname_copilot_short %} as a tool to enhance them, not circumvent them.

### Resources for developers

* [{% data variables.copilot.copilot_chat_dotcom_short %}](<https://github.com/copilot/how-tos/copilot-on-github/chat-with-copilot/chat-in-github>)
* [AUTOTITLE](<https://github.com/copilot/tutorials/copilot-cookbook/analyze-security/find-vulnerabilities>)
* [{% data variables.product.prodname_learning %} - Getting Started with {% data variables.product.prodname_copilot %}](<https://github.com/skills/getting-started-with-github-copilot>)

## Metrics to watch

{% data reusables.copilot.measure-changes-intro %}

* **Security debt ratio**. Use security overview to see if the number of alerts falls over time.
* **Time to remediate security issues**. Use security overview to see if the time to remediate security issues falls over time.

See [AUTOTITLE](<https://github.com/code-security/how-tos/view-and-interpret-data/analyze-organization-data/assessing-code-security-risk>).


</details>
