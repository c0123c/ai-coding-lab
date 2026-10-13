---
vendor: "Qwen"
repository: "QwenLM/qwen-code"
path: "docs/users/integration-zed.md"
source_url: "https://github.com/QwenLM/qwen-code/blob/main/docs/users/integration-zed.md"
raw_url: "https://raw.githubusercontent.com/QwenLM/qwen-code/main/docs/users/integration-zed.md"
resolved_url: "https://raw.githubusercontent.com/QwenLM/qwen-code/main/docs/users/integration-zed.md"
tree_snapshot_sha: "7e3de59d3aba5cab80767ac5b121c0b3b4e35267"
sha256_body: "83adc0daec7dbb0aec246a4e6bc8d9d2b0edde826a77a1a77f05e98350b4ed50"
archive: "01_资料来源/官方资料/审计记录/官方GitHub相关正文_2026-10-10/official-github-pages-0001.jsonl.gz:39"
---

# Zed Editor

> Zed Editor provides native support for AI coding assistants through the Agent Client Protocol (ACP). This integration allows you to use Qwen Code directly within Zed's interface with real-time code suggestions.

![Zed Editor Overview](https://img.alicdn.com/imgextra/i1/O1CN01aAhU311GwEoNh27FP_!!6000000000686-2-tps-3024-1898.png)

### Features

- **Native agent experience**: Integrated AI assistant panel within Zed's interface
- **Agent Client Protocol**: Full support for ACP enabling advanced IDE interactions
- **File management**: @-mention files to add them to the conversation context
- **Conversation history**: Access to past conversations within Zed

### Requirements

- Zed Editor (latest version recommended)
- Qwen Code CLI installed

### Installation

#### Install from ACP Registry (Recommend)

1. Install Qwen Code CLI:

```bash
npm install -g @qwen-code/qwen-code
```

2. Download and install [Zed Editor](https://zed.dev/)

3. In Zed, click the **settings button** in the top right corner, select **"Add agent"**, choose **"Install from Registry"**, find **Qwen Code**, then click **Install**.

   ![ACP Registry](https://img.alicdn.com/imgextra/i4/O1CN0186ybL61EeG35fHFjy_!!6000000000376-2-tps-3056-1705.png)

   ![Qwen Code ACP Installed](https://img.alicdn.com/imgextra/i1/O1CN01OXHhoR1J8irAvjs8F_!!6000000000984-2-tps-1247-703.png)

#### Manual Install

1. Install Qwen Code CLI:

```bash
npm install -g @qwen-code/qwen-code
```

2. Download and install [Zed Editor](https://zed.dev/)

3. In Zed, click the **settings button** in the top right corner, select **"Add agent"**, choose **"Create a custom agent"**, and add the following configuration:

```json
"Qwen Code": {
  "type": "custom",
  "command": "qwen",
  "args": ["--acp"],
  "env": {}
}
```

![Qwen Code Integration](https://img.alicdn.com/imgextra/i1/O1CN013s61L91dSE1J7MTgO_!!6000000003734-2-tps-2592-1234.png)

## Troubleshooting

### Agent not appearing

- Run `qwen --version` in terminal to verify installation
- Check that the JSON configuration is valid
- Restart Zed Editor

### Qwen Code not responding

- Check your internet connection
- Verify CLI works by running `qwen` in terminal
- [File an issue on GitHub](https://github.com/qwenlm/qwen-code/issues) if the problem persists
