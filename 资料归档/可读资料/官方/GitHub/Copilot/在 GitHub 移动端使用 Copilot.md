# 在 GitHub 移动端使用 Copilot

提供询问软件问题、探索仓库、分析代码及讨论 PR 和 Issue 的操作，并说明移动端限制。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-09-09](<https://github.com/github/docs/commit/df9bd944684a61fc90f8cfda11cf031984998e04>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/copilot-on-github/chat-with-copilot/chat-in-mobile.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/在 GitHub 移动端使用 Copilot.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Asking GitHub Copilot questions in GitHub Mobile
intro: 'Ask coding questions, explore repositories, and get help with pull requests in {% data variables.product.prodname_mobile %}.'
versions:
  feature: copilot-chat-for-mobile
shortTitle: Chat in Mobile
redirect_from:
  - /copilot/github-copilot-chat/using-github-copilot-chat-in-github-mobile
  - /copilot/github-copilot-chat/copilot-chat-in-github-mobile/using-github-copilot-chat-in-github-mobile
  - /copilot/github-copilot-chat/copilot-chat-in-github-mobile
  - /copilot/using-github-copilot/asking-github-copilot-questions-in-github-mobile
  - /copilot/using-github-copilot/copilot-chat/asking-github-copilot-questions-in-github-mobile
  - /copilot/how-tos/chat/asking-github-copilot-questions-in-github-mobile
  - /copilot/how-tos/chat/use-chat-in-mobile
  - /copilot/how-tos/use-chat/use-chat-in-mobile
  - /copilot/how-tos/chat-with-copilot/use-chat-in-mobile
  - /copilot/how-tos/chat-with-copilot/chat-in-mobile
contentType: how-tos
category: 
  - Author and optimize with Copilot
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

## 局限性

以下限制适用于 {% data variables.copilot.copilot_mobile_short %}：

* 如果您是组织或企业的成员，您对 {% data variables.copilot.copilot_mobile_short %} 的访问可能取决于管理员设置的策略。参见[AUTOTITLE](<https://github.com/copilot/concepts/enterprise/policies>)。
* 如果您通过 iOS 或 Android 上的应用内购买购买了 {% data variables.copilot.copilot_pro_short %}、{% data variables.copilot.copilot_pro_plus_short %} 或 {% data variables.copilot.copilot_max_short %}，则无法启用额外的 {% data variables.product.prodname_ai_credits %}。为此，请取消您的移动订阅并通过网络浏览器在 {% data variables.product.prodname_dotcom_the_website %} 上重新订阅。

## 询问有关软件开发的一般问题

{% data reusables.copilot.chat-mobile-start-chat %}
1. 在页面底部的“询问 {% data variables.product.prodname_copilot_short %}”框中，键入问题并发送消息。

您可以提出的一些常见问题的示例包括：
   * `What are the advantages of the Go programming language?`
   * `What is Agile software development?`
   * `What is the most popular JavaScript framework?`
   * `Give me some examples of regular expressions.`
   * `Write a bash script to output today's date.`

1. 在对话线程中，您可以提出后续问题。 {% data variables.product.prodname_copilot_short %} 将在对话的上下文中回答。

## 询问有关存储库的探索性问题

您可以询问有关特定存储库的问题，以获得理解代码的帮助，或者获得有关您正在处理的特定任务的帮助。

1. 在 {% data variables.product.prodname_mobile %} 中，导航到存储库，然后点击屏幕右下角的 **{% octicon "copilot" aria-hidden="true" aria-label="Copilot" %}** 图标。
1. 在页面底部，使用“询问 {% data variables.product.prodname_copilot_short %}”框，输入问题并发送消息。

例如，如果您选择正在使用的存储库作为上下文，您可以询问：

   * `What is the main purpose of this repo? What problem does it solve or what functionality does it provide?`
   * `What web frameworks are used in this project?`
   * `Where is rate limiting implemented in our API?`
   * `How is the code organized? Explain the project architecture.`
   * `Are there any specific environment requirements for working on this project?`

## 询问有关特定代码片段的问题

您可以与 {% data variables.product.prodname_copilot_short %} 讨论存储库中的文件或文件中的特定代码行。

1. 在 {% data variables.product.prodname_mobile %} 中，导航到存储库并打开文件。
1. 执行以下操作之一：
   * 要询问有关整个文件的问题，请点击文件视图右下角的 {% data variables.product.prodname_copilot_short %} 图标 ({% octicon "copilot" aria-hidden="true" aria-label="copilot" %})。
   * 要询问有关文件中特定行的问题，请选择并复制您想要询问的行。然后点击 {% data variables.product.prodname_copilot_short %} 图标 ({% octicon "copilot" aria-hidden="true" aria-label="copilot" %}) 并将复制的行粘贴到 {% data variables.copilot.copilot_chat_short %} 输入字段中。

1. 在聊天面板底部的“提问 {% data variables.product.prodname_copilot_short %}”框中输入问题并发送消息。

{% ifversion ghec %}

## 询问有关拉取请求、问题和讨论的问题

导航到 {% data variables.product.prodname_mobile %} 中的任何拉取请求、问题或讨论，然后点击 **{% octicon "copilot" aria-hidden="true" aria-label="Copilot" %}** 图标向 {% data variables.product.prodname_copilot_short %} 询问相关信息。

{% endif %}

## 在 {% data variables.product.prodname_mobile %} 中扩展 {% data variables.copilot.copilot_chat_short %}

{% data reusables.copilot.copilot-extensions.extending-copilot-chat %}


## 原文对照

<details>
<summary>展开完整原文</summary>

## Limitations

The following limitations apply to {% data variables.copilot.copilot_mobile_short %}:

* If you're a member of an organization or enterprise, your access to {% data variables.copilot.copilot_mobile_short %} may depend on policies set by your administrator. See [AUTOTITLE](<https://github.com/copilot/concepts/enterprise/policies>).
* If you purchased {% data variables.copilot.copilot_pro_short %}, {% data variables.copilot.copilot_pro_plus_short %}, or {% data variables.copilot.copilot_max_short %} through an in-app purchase on iOS or Android, you cannot enable additional {% data variables.product.prodname_ai_credits %}. To do so, cancel your mobile subscription and re-subscribe on {% data variables.product.prodname_dotcom_the_website %} through a web browser.

## Asking a general question about software development

{% data reusables.copilot.chat-mobile-start-chat %}
1. At the bottom of the page, in the "Ask {% data variables.product.prodname_copilot_short %}" box, type a question and send the message.

   Some examples of general questions you could ask are:
   * `What are the advantages of the Go programming language?`
   * `What is Agile software development?`
   * `What is the most popular JavaScript framework?`
   * `Give me some examples of regular expressions.`
   * `Write a bash script to output today's date.`

1. Within a conversation thread, you can ask follow-up questions. {% data variables.product.prodname_copilot_short %} will answer within the context of the conversation.

## Asking exploratory questions about a repository

You can ask questions about a specific repository, to get help with understanding the code, or to get help with a specific task you're working on.

1. In {% data variables.product.prodname_mobile %}, navigate to a repository, and tap the **{% octicon "copilot" aria-hidden="true" aria-label="Copilot" %}** icon in the bottom right corner of the screen.
1. At the bottom of the page, use the "Ask {% data variables.product.prodname_copilot_short %}" box, type a question and send the message.

   For example, if you chose the repository you are working in as the context, you could ask:

   * `What is the main purpose of this repo? What problem does it solve or what functionality does it provide?`
   * `What web frameworks are used in this project?`
   * `Where is rate limiting implemented in our API?`
   * `How is the code organized? Explain the project architecture.`
   * `Are there any specific environment requirements for working on this project?`

## Asking questions about specific pieces of code

You can chat with {% data variables.product.prodname_copilot_short %} about a file in your repository, or about specific lines of code within a file.

1. In {% data variables.product.prodname_mobile %}, navigate to a repository and open a file.
1. Do one of the following:
   * To ask a question about the entire file, tap the {% data variables.product.prodname_copilot_short %} icon ({% octicon "copilot" aria-hidden="true" aria-label="copilot" %}) in the bottom right corner of the file view.
   * To ask a question about specific lines within the file, select and copy the lines you want to ask about. Then tap the {% data variables.product.prodname_copilot_short %} icon ({% octicon "copilot" aria-hidden="true" aria-label="copilot" %}) and paste the copied lines in the {% data variables.copilot.copilot_chat_short %} input field.

1. Type a question in the "Ask {% data variables.product.prodname_copilot_short %}" box at the bottom of the chat panel and send the message.

{% ifversion ghec %}

## Asking questions about pull requests, issues, and discussions

Navigate to any pull request, issue, or discussion in {% data variables.product.prodname_mobile %} and tap the **{% octicon "copilot" aria-hidden="true" aria-label="Copilot" %}** icon to ask {% data variables.product.prodname_copilot_short %} about it.

{% endif %}

## Extending {% data variables.copilot.copilot_chat_short %} in {% data variables.product.prodname_mobile %}

{% data reusables.copilot.copilot-extensions.extending-copilot-chat %}


</details>
