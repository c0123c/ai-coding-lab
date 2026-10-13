# GitHub 网页中的 Copilot 提问入门

围绕仓库、代码、PR、调试、安全告警及提交提供提示示例，帮助把开发问题表达成具体请求。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-07-09](<https://github.com/github/docs/commit/8477db5b2441da9798de92279b98622b87fb5d39>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/copilot-on-github/chat-with-copilot/get-started-with-chat.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/GitHub 网页中的 Copilot 提问入门.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Getting started with prompts for {% data variables.copilot.copilot_chat_short %} on {% data variables.product.github %}
intro: 'Explore example prompts to ask {% data variables.copilot.copilot_chat_short %} about code, repositories, pull requests, and more.'
versions:
  feature: copilot
shortTitle: Get started with chat
contentType: how-tos
category:
  - Author and optimize with Copilot
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

<a href="https://github.com/copilot?ref_product=copilot&ref_type=engagement&ref_style=button" target="_blank" class="btn btn-primary mt-3 mr-3 mb-3 no-underline">
 <span>在 {% data variables.product.github %}</span> {% octicon "link-external" height:16 aria-label:"link-external" %}
</a> 上与 {% data variables.product.prodname_copilot_short %} 聊天

{% data variables.copilot.copilot_chat_short %} 根据您在 {% data variables.product.github %} 上的位置而做出不同的响应。以下示例显示了您在每种情况下可以提出的问题类型。

## 一般软件问题

* {% prompt %}告诉我有关nodejs Web服务器框架的信息{% endprompt %}
* {% prompt %}如何创建 Express 应用程序{% endprompt %}
* {% prompt %}更新 npm 软件包的最佳方式是什么{% endprompt %}

## 存储库问题

导航到存储库以询问其用途、结构、代码或历史记录。 {% data variables.copilot.copilot_chat_short %} 将其答案建立在存储库中的实际文件和符号中，因此您可以获得高级概述，然后在相同的上下文中提出后续问题。

* {% prompt %}这个repo的主要目的是什么？{% endprompt %}
* {% prompt %}请概述一下该项目的结构。{% endprompt %}
* {% prompt %}如何在本地设置并运行该项目？{% endprompt %}
* {% prompt %}我们的API中的速率限制在哪里实现？{% endprompt %}
* {% prompt %}USERNAME{% endprompt %}最后合并的PR是什么

## 文件和代码问题

打开文件或选择特定行，然后询问它们。

* {% prompt %}解释一下这个文件。{% endprompt %}
* {% prompt %}我该如何改进这段代码？{% endprompt %}
* {% prompt %}为此方法编写一个单元测试。{% endprompt %}

## 拉取请求问题

导航到拉取请求以询问更改、审核或失败的工作流程。

* {% prompt %}总结一下这个PR的变化。{% endprompt %}
* {% prompt %}这个文件的用途是什么？{% endprompt %}
* {% prompt %}告诉我为什么这个作业失败了{% endprompt %}
* {% prompt %}{% data variables.product.prodname_copilot_short %}在此PR中发生了什么变化，为什么？{% endprompt %}
* {% prompt %}打开此PR之前{% data variables.product.prodname_copilot_short %}验证了什么？{% endprompt %}

## 调试问题

导航到失败的工作流运行、检查失败的拉取请求，或粘贴堆栈跟踪以获取诊断和修复问题的帮助。

* {% prompt %}为什么此工作流运行失败？{% endprompt %}
* {% prompt %}解释此堆栈跟踪并提出修复建议。{% endprompt %}
* {% prompt %}此 PR 中的哪些更改最有可能导致测试失败？{% endprompt %}

## 安全警报问题

导航至 {% data variables.product.prodname_code_scanning %}、{% data variables.product.prodname_secret_scanning %} 或 {% data variables.product.prodname_dependabot %} 警报以询问相关情况。

* {% prompt %}如何修复此警报？{% endprompt %}
* {% prompt %}此警报引用了哪一行代码？{% endprompt %}

## 问题、讨论和提交问题

导航到任何问题、讨论或提交并询问 {% data variables.copilot.copilot_chat_short %}。

* {% prompt %}这个问题的目的是什么？{% endprompt %}
* {% prompt %}此提交的预期输出是什么？{% endprompt %}


## 原文对照

<details>
<summary>展开完整原文</summary>

<a href="https://github.com/copilot?ref_product=copilot&ref_type=engagement&ref_style=button" target="_blank" class="btn btn-primary mt-3 mr-3 mb-3 no-underline">
    <span>Chat with {% data variables.product.prodname_copilot_short %} on {% data variables.product.github %}</span> {% octicon "link-external" height:16 aria-label:"link-external" %}
</a>

{% data variables.copilot.copilot_chat_short %} responds differently depending on where you are on {% data variables.product.github %}. The following examples show the kinds of questions you can ask in each context.

## General software questions

* {% prompt %}tell me about nodejs web server frameworks{% endprompt %}
* {% prompt %}how can I create an Express app{% endprompt %}
* {% prompt %}what is the best way to update an npm package{% endprompt %}

## Repository questions

Navigate to a repository to ask about its purpose, structure, code, or history. {% data variables.copilot.copilot_chat_short %} grounds its answers in the actual files and symbols in the repository, so you can get a high-level overview and then ask follow-up questions within the same context.

* {% prompt %}What is the main purpose of this repo?{% endprompt %}
* {% prompt %}Give me an overview of how this project is structured.{% endprompt %}
* {% prompt %}How do I set up and run this project locally?{% endprompt %}
* {% prompt %}Where is rate limiting implemented in our API?{% endprompt %}
* {% prompt %}What was the last merged PR by USERNAME{% endprompt %}

## File and code questions

Open a file or select specific lines, then ask about them.

* {% prompt %}Explain this file.{% endprompt %}
* {% prompt %}How could I improve this code?{% endprompt %}
* {% prompt %}Write a unit test for this method.{% endprompt %}

## Pull request questions

Navigate to a pull request to ask about changes, reviews, or failing workflows.

* {% prompt %}Summarize the changes in this PR.{% endprompt %}
* {% prompt %}What's the purpose of this file?{% endprompt %}
* {% prompt %}Tell me why this job failed{% endprompt %}
* {% prompt %}What did {% data variables.product.prodname_copilot_short %} change in this PR and why?{% endprompt %}
* {% prompt %}What did {% data variables.product.prodname_copilot_short %} validate before opening this PR?{% endprompt %}

## Debugging questions

Navigate to a failed workflow run, a pull request with a failing check, or paste a stack trace to get help diagnosing and fixing the problem.

* {% prompt %}Why did this workflow run fail?{% endprompt %}
* {% prompt %}Explain this stack trace and suggest a fix.{% endprompt %}
* {% prompt %}Which change in this PR most likely caused the failing test?{% endprompt %}

## Security alert questions

Navigate to a {% data variables.product.prodname_code_scanning %}, {% data variables.product.prodname_secret_scanning %}, or {% data variables.product.prodname_dependabot %} alert to ask about it.

* {% prompt %}How would I fix this alert?{% endprompt %}
* {% prompt %}Which line of code is this alert referencing?{% endprompt %}

## Issue, discussion, and commit questions

Navigate to any issue, discussion, or commit and ask {% data variables.copilot.copilot_chat_short %} about it.

* {% prompt %}What is the purpose of this issue?{% endprompt %}
* {% prompt %}What is the expected output of this commit?{% endprompt %}


</details>
