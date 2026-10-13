# 在 Copilot Chat 中使用 GitHub MCP

介绍无需单独配置即可执行的 GitHub 操作，例如搜索、创建分支和处理拉取请求，同时列出支持范围与限制。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-09-30](<https://github.com/github/docs/commit/d9fd377ea956f59385ac8cb2034ba5c613b6fd85>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/copilot-on-github/copilot-for-github-tasks/using-the-github-mcp-server-from-copilot-chat.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/在 Copilot Chat 中使用 GitHub MCP（存档be38ec5d-545）.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Using the GitHub MCP Server from Copilot Chat
shortTitle: Use the {% data variables.product.github %} MCP Server from {% data variables.copilot.copilot_chat_short %}
intro: Perform {% data variables.product.github %} tasks—like creating branches, merging pull requests, and searching for users—directly from {% data variables.copilot.copilot_chat_dotcom_short %}, with no setup required.
versions:
  feature: copilot
contentType: how-tos
category:
  - Integrate Copilot with your tools
redirect_from:
  - /copilot/how-tos/copilot-on-github/copilot-for-github-tasks/use-the-github-mcp-server
  - /copilot/how-tos/copilot-on-github/copilot-for-github-tasks/using-the-github-mcp-server-on-github
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

{% data variables.product.github %} MCP 服务器已在 {% data variables.copilot.copilot_chat_dotcom_short %} 中预先配置了有限的技能，因此您可以立即使用它。指示 {% data variables.copilot.copilot_chat_short %} 代表您执行创建分支或合并拉取请求等任务。

有关可用技能的完整列表，请参阅 [AUTOTITLE](<https://github.com/copilot/reference/chat-cheat-sheet#mcp-skills>)。有关MCP的更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/concepts/context/mcp>)。

## 使用{% data variables.product.github %}MCP服务器

{% data reusables.copilot.access-chat-instructions %}
1. 输入请求并按**输入**。例如：

    {% prompt %}创建一个名为的新分支 [BRANCH-NAME] 在存储库中 [OWNER/REPO-NAME].{% endprompt %}

{% prompt %}将拉取请求[PR-NUMBER]合并到存储库[OWNER/REPO-NAME]中。{% endprompt %}

1. 单击**允许**确认操作。
1. {% data variables.copilot.copilot_chat_short %} 执行操作并显示结果。

## 局限性

{% data variables.copilot.copilot_chat_dotcom_short %} 中的 {% data variables.product.github %} MCP 服务器支持一组有限的技能。如果不支持请求的操作，{% data variables.copilot.copilot_chat_short %} 会提供指导，但无法直接执行。

要访问全套工具，请在 IDE 中设置 {% data variables.product.github %} MCP 服务器。参见[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/copilot-for-common-tasks/use-the-github-mcp-server>)。

您还可以将 {% data variables.product.github %} MCP 服务器与 {% data variables.copilot.copilot_cloud_agent %} 一起使用。默认情况下启用只读访问，并且可以自定义以实现更广泛的访问。参见[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/configure-mcp-servers#customizing-the-built-in-github-mcp-server>)。
## 进一步阅读

* [AUTOTITLE](<https://github.com/copilot/tutorials/enhance-agent-mode-with-mcp>)


## 原文对照

<details>
<summary>展开完整原文</summary>

The {% data variables.product.github %} MCP server is pre-configured in {% data variables.copilot.copilot_chat_dotcom_short %} with a limited set of skills, so you can use it immediately. Instruct {% data variables.copilot.copilot_chat_short %} to perform tasks like creating branches or merging pull requests on your behalf.

For a full list of available skills, see [AUTOTITLE](<https://github.com/copilot/reference/chat-cheat-sheet#mcp-skills>). For more about MCP, see [AUTOTITLE](<https://github.com/copilot/concepts/context/mcp>).

## Use the {% data variables.product.github %} MCP server

{% data reusables.copilot.access-chat-instructions %}
1. Type a request and press **Enter**. For example:

    {% prompt %}Create a new branch called [BRANCH-NAME] in the repository [OWNER/REPO-NAME].{% endprompt %}

    {% prompt %}Merge the pull request [PR-NUMBER] in the repository [OWNER/REPO-NAME].{% endprompt %}

1. Click **Allow** to confirm the action.
1. {% data variables.copilot.copilot_chat_short %} performs the action and shows the result.

## Limitations

The {% data variables.product.github %} MCP server in {% data variables.copilot.copilot_chat_dotcom_short %} supports a limited set of skills. If a requested action is not supported, {% data variables.copilot.copilot_chat_short %} provides guidance but cannot perform it directly.

To access the full set of tools, set up the {% data variables.product.github %} MCP server in your IDE. See [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/copilot-for-common-tasks/use-the-github-mcp-server>).

You can also use the {% data variables.product.github %} MCP server with {% data variables.copilot.copilot_cloud_agent %}. It is enabled with read-only access by default and can be customized for wider access. See [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/configure-mcp-servers#customizing-the-built-in-github-mcp-server>).
## Further reading

* [AUTOTITLE](<https://github.com/copilot/tutorials/enhance-agent-mode-with-mcp>)


</details>
