# 用 Copilot 加快团队 PR 流程

从流程阻碍、功能选择和实施入手，讨论 PR 摘要、审查、修改建议及衡量指标，供团队评估采用方式。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-09-09](<https://github.com/github/docs/commit/df9bd944684a61fc90f8cfda11cf031984998e04>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/tutorials/roll-out-at-scale/drive-downstream-impact/accelerate-pull-requests.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/用 Copilot 加快团队 PR 流程.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Accelerating pull requests in your company with GitHub Copilot
shortTitle: Accelerate pull requests
intro: 'Understand features, enable developers, and measure Copilot''s impact.'
versions:
  feature: copilot
product: '{% data variables.copilot.copilot_for_business %} or {% data variables.copilot.copilot_enterprise %}'
allowTitleToDifferFromFilename: true
redirect_from:
  - /copilot/tutorials/rolling-out-github-copilot-at-scale/driving-downstream-impact/accelerate-pull-requests
  - /copilot/tutorials/rolling-out-github-copilot-at-scale/drive-downstream-impact/accelerate-pull-requests
contentType: tutorials
category:
  - Accelerate PR velocity
  - Manage Copilot for a team
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

{% data reusables.copilot.essp-intro %}

## 1。识别成功的障碍

{% data reusables.copilot.identify-barriers-intro %}

由于审查周期过长，团队在合并拉取请求时经常会遇到延迟。这些延误通常源于：

* 复杂的代码更改难以理解
* 不一致的代码格式使审核变得困难
* 普遍缺乏变更所提供的背景信息
* 导致审核缓慢或难以解决的社会因素

审阅者还很容易错过可能导致生产问题的小错误。

这会导致开发过程中出现瓶颈，并减慢功能的整体交付和质量。

## 2。评估您的选择

{% data reusables.copilot.evaluate-options-intro %}

<a href="https://github.com/github-copilot/purchase?ref_product=copilot&ref_type=trial&ref_style=button" target="_blank" class="btn btn-primary mt-3 mr-3 no-underline"><span>注册{% data variables.product.prodname_copilot_short %}</span>{% octicon "link-external" height:16 aria-label="link-external" %}</a>

### {% data variables.product.prodname_copilot_short %} 如何提供帮助

{% data variables.product.prodname_copilot %} 提供了一套功能，旨在加速拉取请求审查流程、提高代码质量和改善协作，最终缩短合并时间。

通过利用 {% data variables.product.prodname_copilot_short %} 的功能，团队可以简化工作流程、减少摩擦并确保一致的高质量代码。

#### 生成完整且有用的 PR 摘要

{% data variables.product.prodname_copilot_short %}可以自动生成清晰简洁的PR摘要，节省开发人员的时间，并确保审阅者容易理解PR的目的和变化。这减少了误解的可能性并加快了审核过程。

#### 在审稿过程中协助审稿人

{% data variables.product.prodname_copilot %}可以用作强大的PR复习伴侣。

* {% data variables.product.prodname_copilot_short %} 可以帮助解释复杂的代码更改，以便审阅者更快地了解 PR 所做的贡献。
* {% data variables.product.prodname_copilot_short %} 可以直接在 {% data variables.product.github %} 上的拉取请求审核界面中提供存储库范围内的上下文感知建议和潜在的代码改进，帮助审核者捕获潜在问题并更有效地提供建设性反馈。
* {% data variables.product.prodname_copilot_short %}可以帮助审稿人起草和撰写清晰、一致、有效的审稿意见。

#### 根据组织指南进行评论

* {% data variables.product.prodname_copilot_short %} 可以在打开拉取请求之前查看 IDE 中的代码更改，或者被指定为拉取请求的审阅者。
* 通过规则集，您可以设置 {% data variables.product.prodname_copilot_short %} 以根据自定义标准系统地审查拉取请求。
* 通过自定义审查指令，{% data variables.product.prodname_copilot_short %} 可以强制执行组织编码标准和最佳实践，自动标记潜在违规行为并提出修复建议。

这些功能可确保整个代码库的一致性，并帮助您在开发过程的早期发现错误，减少手动代码审查的需要，并为开发人员和审查人员节省时间。

#### 建议代码修复

根据拉取请求审核评论，{% data variables.product.prodname_copilot_short %} 可以帮助拉取请求作者快速实施所需的代码更改以解决审核问题。

### 文化考虑

{% data reusables.copilot.cultural-factors-intro %}

* 团队可能 ** 等待太长时间才能发布 **，同时部署大量代码。这可能是由于担心频繁发布造成不稳定、缺乏 CI/CD 管道成熟度或严格的合规性要求造成的。
* 开发人员可能**花费太长时间完善代码**或添加不必要的功能。这可能是由于完美主义文化或缺乏有效的优先顺序造成的。
* 开发人员可能**为简单问题构建过于复杂的解决方案**。这可能是由于不必要地面向未来的愿望，或者通过复杂性增加价值的压力造成的。

## 3。实施变革

{% data reusables.copilot.implement-changes-intro %}

* [创建有用的拉取请求摘要](<#创建有用的拉取请求摘要>)
* [使用{% data variables.product.prodname_copilot_short %}作为审阅助手](<#use-copilot-as-a-review-assistant>)
* [添加{% data variables.product.prodname_copilot_short %}为审阅者](<#add-copilot-as-a-reviewer>)
* [获取实施审核意见的帮助](<#获得实施审核意见的帮助>)
* [开发者最佳实践](<#开发人员的最佳实践>)
* [资源](<#资源>)

### 创建有用的拉取请求摘要

1. 创建拉取请求时，单击“添加描述”字段中的 {% data variables.product.prodname_copilot_short %} 图标，然后单击 **摘要**。
1. {% data variables.product.prodname_copilot_short %} 将扫描拉取请求并提供散文中所做更改的概述，以及受其影响的文件的更改的项目符号列表。
1. 检查您对 {% data variables.product.prodname_copilot_short %} 的描述是否满意。
1. 当审阅者收到您的拉取请求时，他们将获得留下审阅所需的所有上下文。

### 使用{% data variables.product.prodname_copilot_short %}作为审稿助手

当以审阅者身份跳转到拉取请求时，您可以使用 {% data variables.product.prodname_copilot_short %} 来加快审阅速度。

1. 使用{% data variables.product.prodname_copilot_short %}来**了解拉取请求**中的变化。

   * 要求 {% data variables.product.prodname_copilot_short %} 总结对文件所做的更改，这对于较长的差异特别有帮助。您可以通过单击文件右上角的 {% octicon "kebab-horizontal" aria-label="Show options" %} 从差异中选择特定文件。

![拉取请求“文件已更改”选项卡的屏幕截图。 “询问 {% data variables.product.prodname_copilot_short %} 有关此差异的信息”选项以红色突出显示。](<https://github.com/assets/images/help/copilot/ask-to-explain.png>)

   * 对于特定行的更改，请突出显示您想要更好理解的行，并要求 {% data variables.product.prodname_copilot_short %} 向您解释更改。您可以通过首先单击最上面的行号，按住 SHIFT 键，然后单击差异的最下面的行来突出显示一组行。

![拉取请求“文件已更改”选项卡的屏幕截图。所选行会突出显示，并且下拉列表中会显示“解释”选项。](<https://github.com/assets/images/help/copilot/highlight-lines.png>)

1. **与 {% data variables.product.prodname_copilot_short %} 协作进行 PR 评论 **。在提示 {% data variables.product.prodname_copilot_short %} 之前，不要忘记将特定文件差异附加到对话中。

   * 您可以通过询问：`Provide your judgement as a PR Reviewer, both for functional and non-functional aspects that these changes bring`来询问{% data variables.product.prodname_copilot_short %}对PR更改的看法。请注意此提示如何要求 {% data variables.product.prodname_copilot_short %} 考虑代码的功能和非功能方面。

   * 对于您自己的PR评论意见，请向{% data variables.product.prodname_copilot_short %}询问第二意见：`As my peer reviewer on this pull request, give me your feedback on my own review: YOUR-REVIEW-COMMENT. Do you think it's pertinent? Am I missing something?`

1. 与{% data variables.product.prodname_copilot_short %}协作**起草并完善您的审阅意见**。

   * 在与{% data variables.product.prodname_copilot_short %}计划审核后，您可以要求列出您应该提供的评论：`Make a list of review comments to add to the PR and tell me exactly in which file diff and lines each comment should be added`。
   * 您还可以要求 {% data variables.product.prodname_copilot_short %} 创建您想要的审阅评论的初稿，或在发布评论之前对其进行优化：`Help me draft review comments as discussed` 或 `Refine this review comment to make it clear, concise, and actionable`。

### 添加 {% data variables.product.prodname_copilot_short %} 为审阅者

为了减少审查时间并更快地合并拉取请求，请系统地使用 {% data variables.product.prodname_copilot_short %} 代码审查：首先在打开拉取请求之前的 IDE 中，然后在 {% data variables.product.github %} 中的 PR 中。

使用 {% data variables.product.prodname_copilot_short %} 代码审查并不能取代人工代码审查的需要。然而，遵循上述步骤可以帮助人们更快地完成评论。

* **开发人员**应该在打开拉取请求之前使用{% data variables.product.prodname_copilot_short %}代码审查来请求审查他们的所有更改。
* **管理员**应设置存储库或组织规则集，以自动将{% data variables.product.prodname_copilot_short %}添加为任何针对受保护分支的拉取请求的审阅者。
* **团队领导**应捕获其团队的标准风格和规则，并将其设置为组织的自定义指令，以便{% data variables.product.prodname_copilot_short %}可以在审核中利用它们。
  * 确保您的自定义指令捕获一组最小的样式建议，使代码更具可读性，这将在拉取请求审核过程中有所帮助。
  * 要减少由于样式问题而产生的 PR 审阅意见数量，请在存储库和组织级别的 {% data variables.product.prodname_copilot_short %} 说明中设置相同的建议。这样，{% data variables.product.prodname_copilot_short %}生成的代码将符合这些准则。

### 获得实施审核意见的帮助

拉取请求作者可以在 {% data variables.product.prodname_copilot_short %} 的帮助下快速实施修复，从而加快 PR 审阅评论的解决速度。

* 对于 {% data variables.product.prodname_copilot_short %} 本身留下的任何审核意见，可以直接提交建议的修复，或者在提交之前在 Copilot 工作区中对其进行编辑。
* 对于同行留下的任何审阅评论，请导航到与 PR 审阅评论相关的文件差异，并将该差异附加到 {% data variables.copilot.copilot_chat_short %} 对话中。然后，复制粘贴评论评论，提示如下：`Suggest a fix for this review comment:`
* 如果您使用的是VS Code，请在agent模式下询问{% data variables.product.prodname_copilot %}以实施审阅评论中所需的更改。

### 开发人员的最佳实践

开发者**应该**：

* 在推动尽早发现并解决问题之前，请在您的 IDE 中请求 {% data variables.product.prodname_copilot_short %} 的审核。
* 使用 {% data variables.product.prodname_copilot_short %} 规划和完善您自己的 PR 审阅意见，以帮助 PR 作者理解和解决问题。
* 将相关的差异上下文（包括特定的代码行）附加到您与 {% data variables.product.prodname_copilot_short %} 的对话中。

开发者**不应该**：

* 应用{% data variables.product.prodname_copilot_short %}的建议而不进行测试。
* 仅依赖 {% data variables.product.prodname_copilot_short %} 进行评论。
* 忽略代码的可读性。

### 资源

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/copilot-for-github-tasks/create-a-pr-summary>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/use-copilot-agents/request-a-code-review/use-code-review>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/set-up-copilot/configure-code-review>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-organization-instructions>)

## 值得关注的指标

{% data reusables.copilot.measure-changes-intro %}

* **开发人员满意度**：使用开发人员调查来衡量对工程工具的满意度。
* **每个开发人员合并的拉取请求**：您可以使用`pull request` webhook，确保`action`是`closed`并且`pull request`对象内的`merged`属性是`true`。
* **拉取请求提前时间**：测量PR创建和合并之间的平均时间长度。
* **拉取请求缺陷逃逸率**：衡量由于审查不力的 PR 导致的部署问题的比率。
* **拉取请求评论评论类型**：下载PR评论评论，使用基于AI-的主题分类进行分类，并跟踪人工评论者对设计、可扩展性和策略的评论。


## 原文对照

<details>
<summary>展开完整原文</summary>

{% data reusables.copilot.essp-intro %}

## 1. Identify barriers to success

{% data reusables.copilot.identify-barriers-intro %}

Teams often experience delays in merging pull requests due to lengthy review cycles. These delays often stem from:

* Complex code changes that are difficult to understand
* Inconsistent code formatting that makes reviews challenging
* A general lack of context provided with the changes
* Social factors that contribute to slow or hard-to-address reviews

Reviewers can also easily miss small errors that may lead to production issues.

This leads to bottlenecks in the development process and slows down the overall delivery and quality of features.

## 2. Evaluate your options

{% data reusables.copilot.evaluate-options-intro %}

<a href="https://github.com/github-copilot/purchase?ref_product=copilot&ref_type=trial&ref_style=button" target="_blank" class="btn btn-primary mt-3 mr-3 no-underline"><span>Sign up for {% data variables.product.prodname_copilot_short %}</span>{% octicon "link-external" height:16 aria-label="link-external" %}</a>

### How {% data variables.product.prodname_copilot_short %} can help

{% data variables.product.prodname_copilot %} offers a suite of features designed to accelerate the pull request review process, enhance code quality, and improve collaboration, ultimately leading to faster merge times.

By leveraging {% data variables.product.prodname_copilot_short %}'s capabilities, teams can streamline their workflows, reduce friction, and ensure consistent, high-quality code.

#### Generates complete and helpful PR summaries

{% data variables.product.prodname_copilot_short %} can automatically generate clear and concise PR summaries, saving developers time and ensuring that the purpose and changes of the PR are easily understood by reviewers. This reduces the likelihood of misunderstandings and speeds up the review process.

#### Assists reviewers during their review process

{% data variables.product.prodname_copilot %} can be used as a powerful PR review companion.

* {% data variables.product.prodname_copilot_short %} can help explain complex code changes so that reviewers more quickly understand what the PR is contributing.
* {% data variables.product.prodname_copilot_short %} can provide repository-wide, context-aware suggestions and potential code improvements directly within the pull request review interface on {% data variables.product.github %}, helping reviewers catch potential issues and offer constructive feedback more efficiently.
* {% data variables.product.prodname_copilot_short %} can help reviewers draft and write clear, consistent, and effective review comments.

#### Reviews based on organization guidelines

* {% data variables.product.prodname_copilot_short %} can review code changes in your IDE before opening a pull request, or be assigned as reviewer to a pull request.
* With rulesets, you can set up {% data variables.product.prodname_copilot_short %} to systematically review pull requests based on custom criteria.
* With custom instructions for reviews, {% data variables.product.prodname_copilot_short %} can enforce organizational coding standards and best practices, automatically flagging potential violations and suggesting fixes.

These features ensure consistency across the codebase and help you catch errors early in the development process, reducing the need for manual code reviews and saving time for developers and reviewers.

#### Suggests code fixes

Based on a pull request review comment, {% data variables.product.prodname_copilot_short %} can help pull request authors quickly implement the required code changes to resolve the review.

### Cultural considerations

{% data reusables.copilot.cultural-factors-intro %}

* Teams might **wait too long to release**, deploying large batches of code at once. This could be caused by a fear of destabilization with frequent releases, a lack of CI/CD pipeline maturity, or strict compliance requirements.
* Developers might **spend too long perfecting code** or adding unnecessary features. This could be caused by a culture of perfectionism or a lack of effective prioritization.
* Developers might **build overly complex solutions** for simple problems. This could be caused by a desire to future-proof unnecessarily, or pressure to add value through complexity.

## 3. Implement changes

{% data reusables.copilot.implement-changes-intro %}

* [Create helpful pull request summaries](<#create-helpful-pull-request-summaries>)
* [Use {% data variables.product.prodname_copilot_short %} as a review assistant](<#use-copilot-as-a-review-assistant>)
* [Add {% data variables.product.prodname_copilot_short %} as a reviewer](<#add-copilot-as-a-reviewer>)
* [Get help implementing review comments](<#get-help-implementing-review-comments>)
* [Best practices for developers](<#best-practices-for-developers>)
* [Resources](<#resources>)

### Create helpful pull request summaries

1. When creating a pull request, click the {% data variables.product.prodname_copilot_short %} icon in the "Add a description" field, then click **Summary**.
1. {% data variables.product.prodname_copilot_short %} will scan through the pull request and provide an overview of the changes made in prose, as well as a bulleted list of changes with the files that they impact.
1. Check you're happy with {% data variables.product.prodname_copilot_short %}'s description.
1. When reviewers come to your pull request, they'll have all the context they need to leave a review.

### Use {% data variables.product.prodname_copilot_short %} as a review assistant

When jumping into a pull request as a reviewer, you can use {% data variables.product.prodname_copilot_short %} to speed up your review.

1. Use {% data variables.product.prodname_copilot_short %} to **understand the changes in the pull request**.

   * Ask {% data variables.product.prodname_copilot_short %} to summarize the changes made to a file, particularly helpful for longer diffs. You can pick a specific file from the diff by clicking {% octicon "kebab-horizontal" aria-label="Show options" %} on the top-right corner of the file.

     ![Screenshot of a pull request "files changed" tab. The "Ask {% data variables.product.prodname_copilot_short %} about this diff" option is highlighted in red.](<https://github.com/assets/images/help/copilot/ask-to-explain.png>)

   * For changes in specific lines, highlight the lines you want to better understand and ask {% data variables.product.prodname_copilot_short %} to explain the changes to you. You can highlight a set of lines by clicking on the uppermost line number first, holding your SHIFT key, and then clicking on the lowermost line of the diff.

     ![Screenshot of a pull request "files changed" tab. A selection of lines is highlighted and an "Explain" option is displayed in a dropdown.](<https://github.com/assets/images/help/copilot/highlight-lines.png>)

1. **Collaborate on your PR review** with {% data variables.product.prodname_copilot_short %}. Don't forget to attach the specific file diffs to the conversation before prompting {% data variables.product.prodname_copilot_short %}.

   * You can ask {% data variables.product.prodname_copilot_short %} for its own opinion on the PR changes by asking: `Provide your judgement as a PR Reviewer, both for functional and non-functional aspects that these changes bring`. Note how this prompt asks {% data variables.product.prodname_copilot_short %} to consider both functional and non-functional aspects of the code.

   * For your own PR review comments, ask {% data variables.product.prodname_copilot_short %} for a second opinion: `As my peer reviewer on this pull request, give me your feedback on my own review: YOUR-REVIEW-COMMENT. Do you think it's pertinent? Am I missing something?`

1. Collaborate with {% data variables.product.prodname_copilot_short %} to **draft and refine your review comments**.

   * After planning the review with {% data variables.product.prodname_copilot_short %}, you can ask to list the comments that you should provide: `Make a list of review comments to add to the PR and tell me exactly in which file diff and lines each comment should be added`.
   * You can also ask {% data variables.product.prodname_copilot_short %} to create a first draft of a review comment you have in mind or refine a comment before you post it: `Help me draft review comments as discussed` or `Refine this review comment to make it clear, concise, and actionable`.

### Add {% data variables.product.prodname_copilot_short %} as a reviewer

To reduce review times and merge pull requests faster, use {% data variables.product.prodname_copilot_short %} code reviews systematically: first in the IDE before opening the pull request, then on the PR in {% data variables.product.github %}.

Using {% data variables.product.prodname_copilot_short %} code review does not replace the need for human code review. However, following the steps above can help humans complete their reviews faster.

* **Developers** should request a review of all their changes using {% data variables.product.prodname_copilot_short %} code review before opening a pull request.
* **Administrators** should set up repository or organization rulesets to automatically add {% data variables.product.prodname_copilot_short %} as a reviewer on any pull request targeting protected branches.
* **Team leads** should capture their team's standard style and rules and set them as custom instructions for the organization so that {% data variables.product.prodname_copilot_short %} can leverage them in reviews.
  * Ensure your custom instructions capture a minimum set of styling recommendations that make code more readable, which will help during the pull request review process.
  * To reduce the amount of PR review comments due to styling issues, set the same recommendations in {% data variables.product.prodname_copilot_short %} instructions at the repository and organization level. This way, the code generated by {% data variables.product.prodname_copilot_short %} will conform to these guidelines.

### Get help implementing review comments

Pull request authors can speed up resolution of PR review comments by quickly implementing fixes with {% data variables.product.prodname_copilot_short %}'s assistance.

* For any review comments left by {% data variables.product.prodname_copilot_short %} itself, either commit the proposed fix directly, or edit it in Copilot Workspace before committing.
* For any review comments left by peers, navigate to the file diff related to the PR review comment and attach the diff to a {% data variables.copilot.copilot_chat_short %} conversation. Then, copy paste the review comment with a prompt like this: `Suggest a fix for this review comment:`
* If you are using VS Code, ask {% data variables.product.prodname_copilot %} in agent mode to implement the required changes from the review comment.

### Best practices for developers

Developers **should**:

* Request {% data variables.product.prodname_copilot_short %}'s review in your IDE before pushing to catch and resolve issues early.
* Use {% data variables.product.prodname_copilot_short %} to plan and refine your own PR review comments to help PR authors understand and resolve issues.
* Attach relevant diff context, including specific lines of code, to your conversations with {% data variables.product.prodname_copilot_short %}.

Developers **should not**:

* Apply {% data variables.product.prodname_copilot_short %}'s suggestions without testing.
* Rely solely on {% data variables.product.prodname_copilot_short %} for reviews.
* Neglect code readability.

### Resources

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/copilot-for-github-tasks/create-a-pr-summary>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/use-copilot-agents/request-a-code-review/use-code-review>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/set-up-copilot/configure-code-review>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-organization-instructions>)

## Metrics to watch

{% data reusables.copilot.measure-changes-intro %}

* **Developer satisfaction**: Use developer surveys to measure satisfaction with engineering tooling.
* **Pull requests merged per developer**: You can use the `pull request` webhook, ensuring `action` is `closed` and the `merged` property inside `pull request` object is `true`.
* **Pull requests lead time**: Measure the average length of time between PR creation and merge.
* **Pull request defect escape rate**: Measure the rate of deployment problems caused by poorly reviewed PRs.
* **Pull request review comment type**: Download PR review comments, categorize using AI-based topic classification, and track the comments made by human reviewers on design, scalability, and strategy.


</details>
