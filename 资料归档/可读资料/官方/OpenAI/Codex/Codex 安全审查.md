# Codex 安全审查

介绍准备威胁模型上下文、配置报告阈值并请求安全审查的方法，用于核查变更中的安全问题。

- 来源：OpenAI／Codex
- 资料类型：存档文档
- 文档时间：官方未公开发表/更新时间（[已复查](<https://learn.chatgpt.com/docs/security/security-review>)）；本地抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://learn.chatgpt.com/docs/security/security-review.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Codex/Codex 安全审查.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 安全审查

> 完整的文档索引，请参见[llms.txt](<https://learn.chatgpt.com/llms.txt>)。通过将 `.md` 附加到页面 URL 可以获得文档页面的 Markdown 版本。

Codex 安全审查适用于 ChatGPT Enterprise、Business、Edu 和 
Pro 客户； Plus 上不提供此功能。安全审查消耗包括
Codex津贴或ChatGPT积分。

Codex 安全审查是针对希望 
 特别关注拉取请求中的安全问题的客户的额外审查。

Codex 安全审查比 [代码
评论](<https://learn.chatgpt.com/docs/third-party/github>) 通过分析特定安全风险
拉取请求差异、支持存储库上下文和配置的威胁模型
或安全指导。代码审查还可以识别与安全相关的问题：
其总体审查的一部分，因此您可能会发现调查结果之间偶尔有重叠。

## 开始之前

要配置自动 Codex 安全审查，您需要：

- Codex 您工作区的安全审查访问权限
- [Codex 云](<https://learn.chatgpt.com/docs/cloud>) 设置已连接的 GitHub 存储库
- GitHub 存储库设置的推送或管理员权限

现有的 Codex 安全扫描是可选的。

<a id="configure-security-review"></a>

## 配置Codex安全审查

1. Go 到 [Codex 设置 ](<https://app.chatgpt.com/settings/code-review>) 并选择存储库。
2. 在**检查安全漏洞**下，开启**自动安全检查**。
3. 使用 ** 审核 ** 选择哪些拉取请求获得 Codex 安全审核：
   - **遵循个人喜好**让每个贡献者选择加入他们的个人喜好
Codex 安全审查设置。
   - **所有 PR ** 适用于存储库中的每个拉取请求。
   - **团队 PR **（如果可用）适用于由
您的 ChatGPT 工作区的成员，而不是 GitHub 团队的成员。
4. 使用**触发器**选择何时运行Codex安全审查：
   - **On PR open** 在拉取请求打开时独立运行。
   - **每次推送时**在推送新提交后独立运行。
   - **每当代码审查运行时** 都需要代码审查并运行 Codex 安全性
一起回顾一下。

## 添加威胁模型上下文

您可以配置威胁模型，以提供有关应用程序的 
 资产、信任边界、安全假设和存储库特定风险的 Codex 上下文。
如果存储库具有现有的 Codex 安全扫描配置，您可以使用 
 其威胁模型。否则，请提供已检查到存储库的
威胁模型文件的路径。如果您不指定来源，Codex 将为每次审核重新生成 
 威胁模型。

## 设置报告阈值

默认情况下，自动 Codex 安全审核报告 ** 高 ** 和 ** 严重 **
 结果，而手动请求的审核报告 ** 中 **， **高**和
**严重**发现。您可以独立更改
自动和手动审核的最低严重性，并添加基于路径的覆盖。

发布到拉取请求的结果继承该拉取请求的 GitHub
 可见性。任何可以查看拉取请求的人都可以查看这些发现，
包括公共存储库上的结果或来自工作区外部
贡献者的拉取请求。为 
 拉取请求评论可能广泛可见的存储库仔细选择报告阈值。举报阈值控制
Codex发布到GitHub的内容；完整的 Codex 安全审查报告保留在 
Codex 中。

<a id="request-a-security-review"></a>

## 请求 Codex 安全审查

要手动请求 Codex 安全审查，请将此评论添加到拉取请求中：

`@codex security review`

Codex 在审核运行时做出反应，然后直接在拉取请求上发布符合您的 
 手动报告阈值的结果。打开关联的 
Codex 任务并选择 ** 安全报告 ** 选项卡以查看完整报告，
 包括严重性、攻击路径、支持证据、验证和 
 修复指南。如果没有问题满足报告阈值，Codex 不会
将调查结果发布到拉取请求。

## 相关文档

- [使用 Codex](<https://learn.chatgpt.com/docs/third-party/github>) 审查 GitHub 拉取请求解释了代码审查和 GitHub 集成。
- [Codex 安全性](<https://learn.chatgpt.com/docs/security>) 提供产品概述。
- [Codex 安全云设置](<https://learn.chatgpt.com/docs/security/setup>) 解释存储库扫描和结果审查。
- [改进威胁模型](<https://learn.chatgpt.com/docs/security/threat-model>)解释了如何调整存储库上下文。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Security Review

> For the complete documentation index, see [llms.txt](<https://learn.chatgpt.com/llms.txt>). Markdown versions of documentation pages are available by appending `.md` to the page URL.

Codex Security Review is available to ChatGPT Enterprise, Business, Edu, and
Pro customers; it is not available on Plus. Security reviews consume included
Codex allowance or ChatGPT credits.

Codex Security Review is an additional review for customers that want to
pay particular attention to security issues in pull requests.

Codex Security Review goes deeper than [Code
Review](<https://learn.chatgpt.com/docs/third-party/github>) on security-specific risks by analyzing the
pull request diff, supporting repository context, and configured threat models
or security guidance. Code Review can also identify security-related issues as
part of its general review, so you may see occasional overlap between findings.

## Before you start

To configure automatic Codex Security Review, you need:

- Codex Security Review access for your workspace
- [Codex cloud](<https://learn.chatgpt.com/docs/cloud>) set up with a connected GitHub repository
- GitHub push or admin permission for the repository settings

An existing Codex Security scan is optional.

<a id="configure-security-review"></a>

## Configure Codex Security Review

1. Go to [Codex settings](<https://app.chatgpt.com/settings/code-review>) and choose a repository.
2. Under **Review security vulnerabilities**, turn on **Auto security review**.
3. Use **Review** to choose which pull requests get Codex Security Review:
   - **Follow personal preferences** lets each contributor opt in with their personal
     Codex Security Review settings.
   - **All PRs** applies to every pull request in the repository.
   - **Team PRs**, when available, applies to pull requests opened by
     members of your ChatGPT workspace, not members of a GitHub team.
4. Use **Trigger** to choose when Codex Security Review runs:
   - **On PR open** runs independently when a pull request is opened.
   - **On every push** runs independently after new commits are pushed.
   - **Whenever code review runs** requires Code Review and runs Codex Security
     Review alongside it.

## Add threat-model context

You can configure a threat model to give Codex context about your application's
assets, trust boundaries, security assumptions, and repository-specific risks.
If the repository has an existing Codex Security scan configuration, you can use
its threat model. Otherwise, provide the path to a threat model file checked
into the repository. If you do not specify a source, Codex regenerates the
threat model for every review.

## Set reporting thresholds

By default, automatic Codex Security Reviews report **High** and **Critical**
findings, while manually requested reviews report **Medium**, **High**, and
**Critical** findings. You can change the minimum severity independently for
automatic and manual reviews, and add path-based overrides.

Findings posted to a pull request inherit that pull request's GitHub
visibility. Anyone who can view the pull request can view those findings,
including on public repositories or pull requests from contributors outside
your workspace. Choose reporting thresholds carefully for repositories where
pull request comments may be broadly visible. The reporting threshold controls
what Codex posts to GitHub; the full Codex Security Review report remains in
Codex.

<a id="request-a-security-review"></a>

## Request a Codex Security Review

To request a Codex Security Review manually, add this comment to a pull request:

`@codex security review`

Codex reacts while the review is running, then posts findings that meet your
manual reporting threshold directly on the pull request. Open the associated
Codex task and select the **Security Report** tab to view the full report,
including severity, attack path, supporting evidence, validation, and
remediation guidance. If no issues meet the reporting threshold, Codex does not
post findings to the pull request.

## Related docs

- [Review GitHub pull requests with Codex](<https://learn.chatgpt.com/docs/third-party/github>) explains Code Review and the GitHub integration.
- [Codex Security](<https://learn.chatgpt.com/docs/security>) gives the product overview.
- [Codex Security cloud setup](<https://learn.chatgpt.com/docs/security/setup>) explains repository scans and findings review.
- [Improving the threat model](<https://learn.chatgpt.com/docs/security/threat-model>) explains how to tune repository context.


</details>
