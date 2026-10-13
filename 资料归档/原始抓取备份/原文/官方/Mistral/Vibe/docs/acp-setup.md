---
vendor: "Mistral"
repository: "mistralai/mistral-vibe"
path: "docs/acp-setup.md"
source_url: "https://github.com/mistralai/mistral-vibe/blob/main/docs/acp-setup.md"
raw_url: "https://raw.githubusercontent.com/mistralai/mistral-vibe/main/docs/acp-setup.md"
resolved_url: "https://raw.githubusercontent.com/mistralai/mistral-vibe/main/docs/acp-setup.md"
tree_snapshot_sha: "4ae5c5955da8e761318916bdb1a7f46f0b50c7d0"
sha256_body: "bbccdb191c603791bcac4a0cba87071ecb736c700453161131afe3e37a3d86a2"
archive: "01_资料来源/官方资料/审计记录/官方GitHub相关正文_2026-10-10/official-github-pages-0007.jsonl.gz:47"
---

# ACP Setup

Mistral Vibe can be used in text editors and IDEs that support [Agent Client Protocol](https://agentclientprotocol.com/overview/clients). Mistral Vibe includes the `vibe-acp` tool.
Once you have set up `vibe` with the API keys, you are ready to use `vibe-acp` in your editor. Below are the setup instructions for some editors that support ACP.

## Zed

For usage in Zed, we recommend using the [Mistral Vibe Zed ACP agent](https://zed.dev/acp/agent/mistral-vibe). Alternatively, you can set up a local install as follows:

1. Go to `~/.config/zed/settings.json` and, under the `agent_servers` JSON object, add the following key-value pair to invoke the `vibe-acp` command. Here is the snippet:

```json
{
   "agent_servers": {
      "Mistral Vibe": {
         "type": "custom",
         "command": "vibe-acp",
         "args": [],
         "env": {}
      }
   }
}
```

2. In the `Agent Panel` view, select the `Mistral Vibe` agent and start the conversation.

## JetBrains IDEs

For using Mistral Vibe in JetBrains IDEs, you'll need to have the [Jetbrains AI Assistant extension](https://plugins.jetbrains.com/plugin/22282-jetbrains-ai-assistant) installed

### Version 2025.3 or later

1. Open settings, then go to `Tools > AI Assistant > Agents`. Search for `Mistral Vibe`, click install

2. Open AI Assistant. You should be able to select Mistral Vibe from the agent selector (if you're not authenticated yet, you will be prompted to do so).

### Legacy method

1. Add the following snippet to your JetBrains IDE acp.json ([documentation](https://www.jetbrains.com/help/ai-assistant/acp.html)):

```json
{
  "agent_servers": {
    "Mistral Vibe": {
      "command": "vibe-acp",
    }
  }
}
```

1. In the AI Chat agent selector, select the new Mistral Vibe agent and start the conversation.

## Xcode

1. Find the absolute path to the `vibe-acp` executable:

```shell
command -v vibe-acp
```

2. In Xcode, open `Xcode > Settings > Intelligence`.

3. Under `Agents`, click `Add an Agent`.

4. Set the name to `Mistral Vibe` and the executable to the absolute path from step 1. Leave the interpreter, arguments, and environment variables empty.

5. Click `Add`, then select Mistral Vibe in the coding assistant's agent picker and start a conversation.

See [Apple's Xcode documentation](https://developer.apple.com/documentation/xcode/setting-up-coding-intelligence#Enable-agents) for more information about adding ACP agents.

## Neovim (using avante.nvim)

Add Mistral Vibe in the acp_providers section of your configuration

```lua
{
  acp_providers = {
    ["mistral-vibe"] = {
      command = "vibe-acp",
      env = {
         MISTRAL_API_KEY = os.getenv("MISTRAL_API_KEY"), -- necessary if you setup Mistral Vibe manually
      },
    }
  }
}
```
