# 用 Codex 审查 GitHub 拉取请求

介绍连接和启用审查、请求或自动触发审查、定制项目规则，以及安全审查的设置和使用条件。

- 来源：OpenAI／Codex
- 资料类型：存档文档
- 文档时间：官方未公开发表/更新时间（[已复查](<https://learn.chatgpt.com/docs/third-party/github>)）；本地抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://learn.chatgpt.com/docs/third-party/github.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Codex/用 Codex 审查 GitHub 拉取请求.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 使用 Codex 审核 GitHub 拉取请求

> 完整的文档索引，请参见[llms.txt](<https://learn.chatgpt.com/llms.txt>)。通过将 `.md` 附加到页面 URL 可以获得文档页面的 Markdown 版本。

使用 Codex 代码审查来获得 GitHub 拉取 
 请求的另一个高信号审查通过。 Codex 审查拉取请求差异，遵循您的存储库指南，
 并发布针对严重问题的标准 GitHub 代码审查。安全性
审查（在研究预览版中提供）提供了对拉取请求中
潜在安全问题的更深入审查。



[观看：Codex代码审查演练](<https://www.youtube.com/watch?v=HwbSWVg5Ln4>)




## 开始之前

确保您有：

- 连接到 Codex 的 GitHub 存储库。有关旧版集成设置，请参阅 [Codex 云（旧版）](<https://learn.chatgpt.com/docs/environments/cloud-environment>)。
- 访问[Codex代码审查设置](<https://app.chatgpt.com/settings/code-review>)。
- 如果您希望 Codex 遵循存储库特定的审核指南，则需要 `AGENTS.md` 文件。

## 设置 Codex 代码审查

要配置自动审核，您需要连接的 GitHub 存储库和 
GitHub 推送或管理员权限对其设置。

1. 将您的 GitHub 存储库连接到 Codex。
2. Go至[Codex设置](<https://app.chatgpt.com/settings/code-review>)。
3. 选择您的存储库，然后打开 ** 审核代码 ** 下的 ** 自动审核 **。



  
    

> 插图：显示存储库和个人自动审核切换的Codex设置


  





## 请求 Codex 审核

1. 在拉取请求评论中，提及 `@codex review`。
2. 等待Codex反应(👀)并发表评论。



  
    

> 插图：带有 @codex 审核的拉取请求评论


  





Codex 发布了对拉取请求的评论，就像队友一样。在
GitHub中，Codex仅标记P0和P1问题，因此审核评论将重点关注
高优先级风险。



  
    

> 插图：示例 Codex 对拉取请求的代码审查


  





## 启用自动评论

要为您的拉取请求启用自动审查，请打开 **自动审核**
下 **个人喜好** 在 [Codex 设置](<https://app.chatgpt.com/settings/code-review>).
选择何时运行评论 **审查触发器**。这适用于存储库
启用代码审查并且不需要 `@codex review` 评论。

## 自定义 Codex 评论内容

Codex 在您的存储库中搜索 `AGENTS.md` 文件并遵循适用的 
 代码审查规则。将 `## Code Review Rules` 部分添加到最接近规则管辖的代码 
 的文件中。当 
 有帮助时，使用 `###` 标题对相关检查进行分组。

例如，实验报告服务可以防止暴露后行为 
 改变比较队列：

```md
## Code Review Rules

### Experiment cohorts

- Do not filter treatment comparisons on post-exposure behavior, including conversion or retention.
  Safe path: build cohorts from assignment or exposure; report conversion as an outcome.
```

将存储库范围的规则放在根 `AGENTS.md` 中，将特定于服务的规则 
 放在嵌套文件中，例如 `services/experiment_reporting/AGENTS.md`。 Codex
应用涵盖每个更改文件的根和更具体的指南，因此
不相关的更改不必携带特定于服务的上下文。

从两到三个简明规则开始，这些规则对审阅者经常解释的检查进行编码。有用的规则：

- **关注相应的、特定于存储库的行为。**描述
兼容性约束、数据边界或标记的不安全副作用以及
为什么它很重要。
- **说明安全路径或异常。**提供Codex足够的上下文来区分
预期行为的真正问题。
- **保持规则的范围和持久性。**更喜欢结果而不是函数名称
可以更改，并将指导放置在其管辖的代码附近。
- **在 CI 中保留机械检查。** 保留格式、lint 和其他内容
审查规则的确定性检查。

打开代表性拉取请求并请求 `@codex review` 进行审核。
根据您看到的发现和反馈完善规则，并缩小或
删除产生噪音的指导。

代码审查规则指南Codex；它们不会取代测试、分支保护或
所需的批准。

对于一次性焦点，请将其添加到您的拉取请求评论中：

`@codex review for issues in the database migration`

## 安全审查

安全审查是针对希望
特别注意拉取请求中的安全问题的客户的额外审查。通过分析拉取请求差异、
支持存储库上下文以及配置的威胁模型或安全
指南，它比针对特定安全风险的代码审查更深入
。

代码审查还可以识别与安全相关的问题，作为其一般
审查的一部分，因此您可能会发现代码审查和安全
审查结果之间偶尔有重叠。

### 设置安全审查

有关更详细的设置说明和配置选项，请参阅[安全性
审查](<https://learn.chatgpt.com/docs/security/security-review>)。

1. 将您的 GitHub 存储库连接到 Codex。
2. Go至[Codex设置](<https://app.chatgpt.com/settings/code-review>)。
3. 选择您的存储库。在**检查安全漏洞**下，打开
**自动安全审查**，并选择其拉取请求接受
 **审查**的审查。将 ** 触发器 ** 设置为 ** 每当代码审查运行时 ** 以与代码审查一起运行它 
。

### 请求安全审查

要手动请求安全审查，请将此评论添加到拉取请求中：

`@codex security review`

Codex 在审查运行时做出反应，然后直接在拉取请求上发布安全结果
。打开关联的 Codex 任务并选择 **安全
报告** 选项卡以查看完整报告。

## 根据审查结果采取行动

Codex 发表评论后，您可以通过留下另一条评论来要求它修复同一拉取 
 请求中的问题：

```md
@codex fix the P1 issue
```

Codex 以拉取请求作为上下文启动 [ 旧版云聊天 ](<https://learn.chatgpt.com/docs/environments/cloud-environment>)，并且可以在有权限时将修复 
 推送回分支。

## 给予Codex其他任务

如果你提到 `@codex` 在评论中除了 `review`, Codex 开始一个 [旧版云聊天](<https://learn.chatgpt.com/docs/environments/cloud-environment>) 使用您的拉取请求作为上下文。

```md
@codex fix the CI failures
```

## 解决代码审查问题

如果 Codex 没有反应或发表评论：

- 确认您在 [Codex 设置 ](<https://app.chatgpt.com/settings/code-review>) 中打开了存储库的代码审查。
- 确认拉取请求属于连接到 Codex 的 GitHub 存储库。
- 在拉取请求注释中使用确切的触发器 `@codex review`。
- 自动审核请查看仓库下的【**】自动审核【**】
  设置和 **个人喜好** 在 [Codex 设置](<https://app.chatgpt.com/settings/code-review>)。确认拉取请求事件与您的审核触发器设置匹配。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Review GitHub pull requests with Codex

> For the complete documentation index, see [llms.txt](<https://learn.chatgpt.com/llms.txt>). Markdown versions of documentation pages are available by appending `.md` to the page URL.

Use Codex code review to get another high-signal review pass on GitHub pull
requests. Codex reviews the pull request diff, follows your repository guidance,
and posts a standard GitHub code review focused on serious issues. Security
Review, available in research preview, provides a more in-depth review of
potential security issues in a pull request.



[Watch: Codex code review walkthrough](<https://www.youtube.com/watch?v=HwbSWVg5Ln4>)




## Before you start

Make sure you have:

- A GitHub repository connected to Codex. For legacy integration setup, see [Codex Cloud (Legacy)](<https://learn.chatgpt.com/docs/environments/cloud-environment>).
- Access to [Codex code review settings](<https://app.chatgpt.com/settings/code-review>).
- An `AGENTS.md` file if you want Codex to follow repository-specific review guidance.

## Set up Codex code review

To configure automatic reviews, you need a connected GitHub repository and
GitHub push or admin permission for its settings.

1. Connect your GitHub repository to Codex.
2. Go to [Codex settings](<https://app.chatgpt.com/settings/code-review>).
3. Choose your repository, then turn on **Automatic review** under **Review code**.



  
    

> Illustration: Codex settings showing repositories and the personal Automatic review toggle


  





## Request a Codex review

1. In a pull request comment, mention `@codex review`.
2. Wait for Codex to react (👀) and post a review.



  
    

> Illustration: A pull request comment with @codex review


  





Codex posts a review on the pull request, just like a teammate would. In
GitHub, Codex flags only P0 and P1 issues so review comments stay focused on
high-priority risks.



  
    

> Illustration: Example Codex code review on a pull request


  





## Enable automatic reviews

To enable automatic reviews for your pull requests, turn on **Automatic review**
under **Personal preferences** in [Codex settings](<https://app.chatgpt.com/settings/code-review>).
Choose when reviews run with **Review trigger**. This applies to repositories
with code review enabled and doesn't require an `@codex review` comment.

## Customize what Codex reviews

Codex searches your repository for `AGENTS.md` files and follows the applicable
code review rules. Add a `## Code Review Rules` section to the file closest to
the code the rules govern. Use `###` headings to group related checks when
helpful.

For example, an experiment-reporting service can keep post-exposure behavior
from changing a comparison cohort:

```md
## Code Review Rules

### Experiment cohorts

- Do not filter treatment comparisons on post-exposure behavior, including conversion or retention.
  Safe path: build cohorts from assignment or exposure; report conversion as an outcome.
```

Put repository-wide rules in the root `AGENTS.md` and service-specific rules
in a nested file, such as `services/experiment_reporting/AGENTS.md`. Codex
applies the root and more-specific guidance that covers each changed file, so
unrelated changes don't have to carry service-specific context.

Start with two or three concise rules that encode checks reviewers often explain. Useful rules:

- **Focus on consequential, repository-specific behavior.** Describe the
  compatibility constraint, data boundary, or unsafe side effect to flag and
  why it matters.
- **State the safe path or exception.** Give Codex enough context to distinguish
  a real issue from expected behavior.
- **Keep rules scoped and durable.** Prefer outcomes over function names that
  can change, and place guidance near the code it governs.
- **Leave mechanical checks in CI.** Keep formatting, lint, and other
  deterministic checks out of review rules.

Open a representative pull request and request a review with `@codex review`.
Refine the rules based on the findings and feedback you see, and narrow or
remove guidance that produces noise.

Code review rules guide Codex; they don't replace tests, branch protections, or
required approvals.

For a one-off focus, add it to your pull request comment:

`@codex review for issues in the database migration`

## Security Review

Security Review is an additional review for customers that want to
pay particular attention to security issues in pull requests. It goes deeper
than Code Review on security-specific risks by analyzing the pull request diff,
supporting repository context, and configured threat models or security
guidance.

Code Review can also identify security-related issues as part of its general
review, so you may see occasional overlap between Code Review and Security
Review findings.

### Set up Security Review

For more detailed setup instructions and configuration options, see [Security
Review](<https://learn.chatgpt.com/docs/security/security-review>).

1. Connect your GitHub repository to Codex.
2. Go to [Codex settings](<https://app.chatgpt.com/settings/code-review>).
3. Choose your repository. Under **Review security vulnerabilities**, turn on
   **Auto security review** and choose whose pull requests receive reviews with
   **Review**. Set **Trigger** to **Whenever code review runs** to run it
   alongside Code Review.

### Request a Security Review

To request a Security Review manually, add this comment to a pull request:

`@codex security review`

Codex reacts while the review is running, then posts security findings directly
on the pull request. Open the associated Codex task and select the **Security
Report** tab to view the full report.

## Act on review findings

After Codex posts a review, you can ask it to fix issues in the same pull
request by leaving another comment:

```md
@codex fix the P1 issue
```

Codex starts a [legacy cloud chat](<https://learn.chatgpt.com/docs/environments/cloud-environment>) with the pull request as context and can push a fix
back to the branch when it has permission to do so.

## Give Codex other tasks

If you mention `@codex` in a comment with anything other than `review`, Codex starts a [legacy cloud chat](<https://learn.chatgpt.com/docs/environments/cloud-environment>) using your pull request as context.

```md
@codex fix the CI failures
```

## Troubleshoot code review

If Codex doesn't react or post a review:

- Confirm you turned on code review for the repository in [Codex settings](<https://app.chatgpt.com/settings/code-review>).
- Confirm the pull request belongs to a GitHub repository connected to Codex.
- Use the exact trigger `@codex review` in a pull request comment.
- For automatic reviews, check **Automatic review** under the repository
  settings and **Personal preferences** in [Codex settings](<https://app.chatgpt.com/settings/code-review>). Confirm the pull request event matches your review trigger settings.


</details>
