# 管理 Copilot Chat 上下文与使用量

说明何时延续会话、何时换新会话，以及上下文长度如何影响使用量，帮助保持讨论聚焦并控制消耗。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-08-07](<https://github.com/github/docs/commit/7140cc68b4d9a967fa37863243865be094ddcdf0>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/tutorials/optimize-chat-usage.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/管理 Copilot Chat 上下文与使用量.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Optimize GitHub Copilot Chat context to reduce AI credit usage
shortTitle: Optimize Chat usage
intro: Keep {% data variables.copilot.copilot_chat_short %} responses focused and reduce {% data variables.product.prodname_ai_credits_short %} usage by knowing when to continue a conversation and when to start a new one.
versions:
  feature: copilot
contentType: tutorials
category:
  - Author and optimize with Copilot
redirect_from:
  - /copilot/how-tos/chat-with-copilot/manage-long-conversations
  - /copilot/tutorials/manage-long-conversations
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

## Continue 上下文仍然相关的对话

Continue 当您的下一个提示取决于之前的消息时，相同的对话。例如：

* 您正在完善 {% data variables.product.prodname_copilot_short %} 已经生成的代码。
* 您正在跨多个提示调试相同的错误。
* 您正在迭代一种设计、测试计划或实施方法。

将相关提示放在一起有助于 {% data variables.product.prodname_copilot_short %} 保持正确的上下文并减少重复。

## 切换任务时开始新对话

当您的下一个提示是关于不同的问题时，开始新的对话。例如：

* 您完成了一项功能并正在开始另一项功能。
* 您将从编码工作转向文档或发布工作。
* 现有线程包含不再适用的上下文。

重新开始有助于{% data variables.product.prodname_copilot_short %}专注于您的新目标。

## 了解对话长度如何影响 {% data variables.product.prodname_ai_credits_short %} 使用情况

{% data variables.copilot.copilot_chat_short %} 中的每个提示都会使用您的新消息和相关上下文进行处理，例如对话历史记录、选定的文件和工具结果。

较长的线程可能需要为每个新提示处理更多上下文。更多处理的上下文可以增加令牌的使用，从而增加 {% data variables.product.prodname_ai_credits_short %} 的使用。


## 进一步阅读

* [AUTOTITLE](<https://github.com/copilot/tutorials/optimize-ai-usage>)
* [AUTOTITLE](<https://github.com/copilot/get-started/best-practices>)


## 原文对照

<details>
<summary>展开完整原文</summary>

## Continue a conversation when the context is still relevant

Continue the same conversation when your next prompt depends on earlier messages. For example:

* You're refining code that {% data variables.product.prodname_copilot_short %} already generated.
* You're debugging the same error across multiple prompts.
* You're iterating on one design, test plan, or implementation approach.

Keeping related prompts together helps {% data variables.product.prodname_copilot_short %} keep the right context and reduces repetition.

## Start a new conversation when you switch tasks

Start a new conversation when your next prompt is about a different problem. For example:

* You finished one feature and are starting another.
* You're moving from coding work to documentation or release work.
* The existing thread contains context that no longer applies.

Starting fresh helps {% data variables.product.prodname_copilot_short %} focus on your new goal.

## Understand how conversation length affects {% data variables.product.prodname_ai_credits_short %} usage

Each prompt in {% data variables.copilot.copilot_chat_short %} is processed with your new message and relevant context, such as conversation history, selected files, and tool results.

Longer threads can require more context to be processed for each new prompt. More processed context can increase token usage, which can increase {% data variables.product.prodname_ai_credits_short %} usage.


## Further reading

* [AUTOTITLE](<https://github.com/copilot/tutorials/optimize-ai-usage>)
* [AUTOTITLE](<https://github.com/copilot/get-started/best-practices>)


</details>
