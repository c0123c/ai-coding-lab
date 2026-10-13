---
vendor: "GitHub"
repository: "github/docs"
path: "content/copilot/how-tos/copilot-on-github/copilot-for-github-tasks/using-the-github-mcp-server-from-copilot-chat.md"
source_url: "https://github.com/github/docs/blob/main/content/copilot/how-tos/copilot-on-github/copilot-for-github-tasks/using-the-github-mcp-server-from-copilot-chat.md"
raw_url: "https://raw.githubusercontent.com/github/docs/main/content/copilot/how-tos/copilot-on-github/copilot-for-github-tasks/using-the-github-mcp-server-from-copilot-chat.md"
resolved_url: "https://raw.githubusercontent.com/github/docs/main/content/copilot/how-tos/copilot-on-github/copilot-for-github-tasks/using-the-github-mcp-server-from-copilot-chat.md"
tree_snapshot_sha: "be38ec5d78e24172587e61b3c6ff40ace1865c71"
sha256_body: "5e8869cd07a4a51bf281b8370a6e87421e97abac41aa514120ebd43804229be2"
archive: "01_资料来源/官方资料/审计记录/官方GitHub相关正文_2026-10-10/official-github-pages-0005.jsonl.gz:15"
---

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

The {% data variables.product.github %} MCP server is pre-configured in {% data variables.copilot.copilot_chat_dotcom_short %} with a limited set of skills, so you can use it immediately. Instruct {% data variables.copilot.copilot_chat_short %} to perform tasks like creating branches or merging pull requests on your behalf.

For a full list of available skills, see [AUTOTITLE](/copilot/reference/chat-cheat-sheet#mcp-skills). For more about MCP, see [AUTOTITLE](/copilot/concepts/context/mcp).

## Use the {% data variables.product.github %} MCP server

{% data reusables.copilot.access-chat-instructions %}
1. Type a request and press **Enter**. For example:

    {% prompt %}Create a new branch called [BRANCH-NAME] in the repository [OWNER/REPO-NAME].{% endprompt %}

    {% prompt %}Merge the pull request [PR-NUMBER] in the repository [OWNER/REPO-NAME].{% endprompt %}

1. Click **Allow** to confirm the action.
1. {% data variables.copilot.copilot_chat_short %} performs the action and shows the result.

## Limitations

The {% data variables.product.github %} MCP server in {% data variables.copilot.copilot_chat_dotcom_short %} supports a limited set of skills. If a requested action is not supported, {% data variables.copilot.copilot_chat_short %} provides guidance but cannot perform it directly.

To access the full set of tools, set up the {% data variables.product.github %} MCP server in your IDE. See [AUTOTITLE](/copilot/how-tos/copilot-in-your-ide/copilot-for-common-tasks/use-the-github-mcp-server).

You can also use the {% data variables.product.github %} MCP server with {% data variables.copilot.copilot_cloud_agent %}. It is enabled with read-only access by default and can be customized for wider access. See [AUTOTITLE](/copilot/how-tos/copilot-on-github/customize-copilot/configure-mcp-servers#customizing-the-built-in-github-mcp-server).
## Further reading

* [AUTOTITLE](/copilot/tutorials/enhance-agent-mode-with-mcp)
