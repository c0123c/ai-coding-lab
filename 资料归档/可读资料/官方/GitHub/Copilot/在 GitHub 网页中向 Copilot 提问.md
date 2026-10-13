# 在 GitHub 网页中向 Copilot 提问

讲解代码和仓库问答、生成文件、模型比较、会话分支、图片输入及历史管理，帮助在网页开发流程中组织讨论。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-07-09](<https://github.com/github/docs/commit/3af7cb3042ac2f16645785e9f6dc89922d5e5588>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/copilot-on-github/chat-with-copilot/chat-in-github.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/在 GitHub 网页中向 Copilot 提问.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Asking GitHub Copilot questions in GitHub
shortTitle: Chat in GitHub
intro: 'Get instant answers about your code, repositories, and development questions — right from {% data variables.product.github %}.'
versions:
  feature: copilot
redirect_from:
  - /copilot/github-copilot-enterprise/copilot-chat-in-github/using-github-copilot-chat-in-githubcom
  - /copilot/github-copilot-enterprise/copilot-chat-in-github/about-github-copilot-chat-in-githubcom
  - /copilot/github-copilot-chat/copilot-chat-in-github/using-github-copilot-chat-in-githubcom
  - /copilot/github-copilot-chat/copilot-chat-in-github/about-github-copilot-chat-in-githubcom
  - /copilot/github-copilot-chat/copilot-chat-in-github
  - /copilot/using-github-copilot/asking-github-copilot-questions-in-githubcom
  - /copilot/using-github-copilot/asking-github-copilot-questions-in-github
  - /copilot/using-github-copilot/copilot-chat/asking-github-copilot-questions-in-github
  - /copilot/how-tos/chat/asking-github-copilot-questions-in-github
  - /copilot/how-tos/chat/use-chat-in-github
  - /copilot/how-tos/use-chat/use-chat-in-github
  - /copilot/how-tos/chat-with-copilot/use-chat-in-github
  - /copilot/how-tos/chat-with-copilot/chat-in-github
contentType: how-tos
category:
  - Author and optimize with Copilot
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

## 向 {% data variables.copilot.copilot_chat_short %} 提交问题

{% data variables.copilot.copilot_chat_short %} 可从 {% data variables.product.github %} 上的任何页面获取。有些问题在特定上下文中效果最好，例如存储库、问题或拉取请求。

{% data reusables.copilot.access-chat-instructions %}
1. 在提示框中输入问题，然后按<kbd>输入</kbd>。

您可以提出的一些常见问题的示例包括：

   * {% prompt %}Go编程语言有什么优点？{% endprompt %}
   * {% prompt %}什么是敏捷软件开发？{% endprompt %}
   * {% prompt %}最流行的JavaScript框架是什么？{% endprompt %}
   * {% prompt %}给我一些正则表达式的例子。{% endprompt %}
   * {% prompt %}编写一个bash脚本来输出今天的日期。{% endprompt %}

{% data reusables.copilot.stop-response-generation %}
1. 在对话线程中，您可以提出后续问题。 {% data variables.product.prodname_copilot_short %} 将在对话的上下文中回答。

### 查看编辑生成的文件

> [！NOTE]
> 此功能当前位于 {% data variables.release-phases.public_preview %} 中，可能会发生变化。

{% data variables.product.prodname_copilot_short %} 可能会生成文件作为其响应的一部分，您可以从侧面板查看、编辑和下载这些文件。

### 更改和比较 AI 型号

您可以从一系列 AI 型号中进行选择，每个型号都有不同的优点。不同的模型根据其代币定价以不同的速率消耗 {% data variables.product.prodname_ai_credits_short %}。详情请参见[AUTOTITLE](<https://github.com/copilot/reference/copilot-billing/models-and-pricing>)。

{% data reusables.copilot.model-picker-enable-alternative-models %}

1. 在{% data variables.copilot.copilot_chat_short %}底部，选择**CURRENT-MODEL**{% octicon "chevron-down" aria-hidden="true" aria-label="chevron-down" %}下拉菜单，然后单击您选择的AI型号。

提交提示后，您还可以通过单击响应下方的重试图标 ({% octicon "sync" aria-label="The re-run icon" %}) 使用不同的模型重新生成响应。您可以在响应之间切换以比较结果。

### 在对话中使用子线程

子线程是对话的分支，可让您在同一线程中探索主题或新主题的各个方面。

要创建子线程，请将鼠标悬停在之前的问题之一上，然后单击 {% octicon "pencil" aria-label="Edit message" %} 按钮。编辑问题，然后点击**发送**。您无法编辑任何附件。

对您编辑的问题的答复显示在新的子线程中。问题下方会出现一个编辑计数器。将鼠标悬停在计数器上，然后单击 {% octicon "chevron-left" aria-label="Previous response" %} 或 {% octicon "chevron-right" aria-label="Next response" %} 在子线程之间导航。

### 使用{% data variables.copilot.copilot_chat_short %}中的图像

{% data reusables.copilot.using-images-in-chat %}

如需附加文件，请将其拖放到提示框中，或单击{% octicon "plus" aria-label="Add attachment" %}并选择**{% octicon "file-code" aria-hidden="true" %}从计算机上传**。从模型选择器中选择支持图像输入的模型。

### 在 agent 会话中继续对话

当您从 {% data variables.copilot.copilot_chat_short %} 启动 {% data variables.copilot.copilot_cloud_agent %} 任务时（例如，通过要求 {% data variables.product.prodname_copilot_short %} 创建拉取请求或研究存储库），您可以在 {% data variables.copilot.copilot_cloud_agent_short %} 会话运行时继续聊天。 {% data variables.copilot.copilot_chat_short %} 反映了正在进行的会话的状态并利用其上下文，因此您可以询问有关 agent 正在做什么的后续问题。会话完成后，您可以询问有关工作的进一步问题或从 {% data variables.copilot.copilot_chat_short %} 开始另一个会话。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/use-copilot-agents/manage-and-track-agents>)。

### 对话历史记录和保留

{% data variables.copilot.copilot_chat_short %} 最多可存储 100 条您最近的对话。每个对话中的消息都会保留 28 天，然后才会被永久删除。一旦对话没有留下任何消息，它就会自动从您的历史记录中删除。

## 进一步阅读

* [AUTOTITLE](<https://github.com/copilot/tutorials/explore-a-codebase>)


## 原文对照

<details>
<summary>展开完整原文</summary>

## Submitting a question to {% data variables.copilot.copilot_chat_short %}

{% data variables.copilot.copilot_chat_short %} is available from any page on {% data variables.product.github %}. Some questions work best in a specific context, such as a repository, issue, or pull request.

{% data reusables.copilot.access-chat-instructions %}
1. In the prompt box, type a question and press <kbd>Enter</kbd>.

   Some examples of general questions you could ask are:

   * {% prompt %}What are the advantages of the Go programming language?{% endprompt %}
   * {% prompt %}What is Agile software development?{% endprompt %}
   * {% prompt %}What is the most popular JavaScript framework?{% endprompt %}
   * {% prompt %}Give me some examples of regular expressions.{% endprompt %}
   * {% prompt %}Write a bash script to output today's date.{% endprompt %}

{% data reusables.copilot.stop-response-generation %}
1. Within a conversation thread, you can ask follow-up questions. {% data variables.product.prodname_copilot_short %} will answer within the context of the conversation.

### Viewing an editing generated files

> [!NOTE]
> This feature is currently in {% data variables.release-phases.public_preview %} and subject to change.

{% data variables.product.prodname_copilot_short %} may generate files as part of its response, which you can view, edit, and download from the side panel.

### Changing and comparing AI models

You can choose from a selection of AI models, each with different strengths. Different models consume {% data variables.product.prodname_ai_credits_short %} at different rates based on their token pricing. For details, see [AUTOTITLE](<https://github.com/copilot/reference/copilot-billing/models-and-pricing>).

{% data reusables.copilot.model-picker-enable-alternative-models %}

1. At the bottom of {% data variables.copilot.copilot_chat_short %}, select the **CURRENT-MODEL** {% octicon "chevron-down" aria-hidden="true" aria-label="chevron-down" %} dropdown menu, then click the AI model of your choice.

After submitting a prompt, you can also regenerate a response using a different model by clicking the retry icon ({% octicon "sync" aria-label="The re-run icon" %}) below the response. You can switch between responses to compare results.

### Using subthreads in a conversation

Subthreads are branches of a conversation that let you explore aspects of a topic, or new topics, all within the same thread.

To create a subthread, hover over one of your previous questions and click the {% octicon "pencil" aria-label="Edit message" %} button. Edit the question, then click **Send**. You cannot edit any attachments.

The response to your edited question is displayed in a new subthread. An edit counter appears below the question. Hover over the counter, then click {% octicon "chevron-left" aria-label="Previous response" %} or {% octicon "chevron-right" aria-label="Next response" %} to navigate between subthreads.

### Using images in {% data variables.copilot.copilot_chat_short %}

{% data reusables.copilot.using-images-in-chat %}

To attach a file, drag and drop it into the prompt box, or click {% octicon "plus" aria-label="Add attachment" %} and select **{% octicon "file-code" aria-hidden="true" %} Upload from computer**. Select a model that supports image input from the model picker.

### Continuing a conversation alongside an agent session

When you start a {% data variables.copilot.copilot_cloud_agent %} task from {% data variables.copilot.copilot_chat_short %}—for example, by asking {% data variables.product.prodname_copilot_short %} to create a pull request or research a repository—you can keep chatting while the {% data variables.copilot.copilot_cloud_agent_short %} session runs. {% data variables.copilot.copilot_chat_short %} reflects the status of the in-progress session and draws on its context, so you can ask follow-up questions about what the agent is doing. When the session is complete, you can ask further questions about the work or start another session from {% data variables.copilot.copilot_chat_short %}. For more information, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/use-copilot-agents/manage-and-track-agents>).

### Conversation history and retention

{% data variables.copilot.copilot_chat_short %} stores up to 100 of your most recent conversations. Messages within each conversation are kept for 28 days before being permanently deleted. Once a conversation has no messages left, it's automatically removed from your history.

## Further reading

* [AUTOTITLE](<https://github.com/copilot/tutorials/explore-a-codebase>)


</details>
