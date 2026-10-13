# 为 Gemini CLI 配置 MCP 服务

说明准备认证信息、写配置、验证连接并调用工具，以 Issue 和拉取请求操作为例展示接入流程。

- 来源：Google／Gemini CLI
- 资料类型：存档文档
- 文档时间：更新：[2026-04-10](<https://geminicli.com/docs/cli/tutorials/mcp-setup/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://geminicli.com/docs/cli/tutorials/mcp-setup/>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Gemini CLI/为 Gemini CLI 配置 MCP 服务.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 设置 MCP 服务器

将 Gemini CLI 连接到您的外部数据库和服务。在本指南中，
您将了解如何通过安装 GitHub
MCP 服务器并使用它来管理您的存储库来扩展 Gemini CLI 的功能。

## 先决条件

[标题为“先决条件”的部分](<#先决条件>)

* Gemini CLI已安装。
* **Docker:** 此特定示例所需（许多 MCP 服务器作为 Docker 运行
容器）。
* **GitHub 令牌：** 具有存储库权限的个人访问令牌 (PAT)。

## 如何准备您的凭据

[标题为“如何准备您的凭据”的部分](<#如何准备您的凭据>)

大多数 MCP 服务器需要身份验证。对于GitHub，您需要一个PAT。

1. 创建[细粒度PAT](<https://github.com/settings/tokens?type=beta>)。
2. 授予其对 **元数据 ** 和 ** 内容 ** 的 ** 读取 ** 访问权限，并且
**Read/Write** 访问 ** 问题 ** 和 ** 拉取请求 **。
3. 将其存储在您的环境中：

**macOS/Linux**

终端窗口

```
export GITHUB_PERSONAL_ACCESS_TOKEN="github_pat_..."
```

**Windows (PowerShell)**

终端窗口

```
$env:GITHUB_PERSONAL_ACCESS_TOKEN="github_pat_..."
```

## 如何配置Gemini CLI

[标题为“如何配置Gemini CLI”的部分](<#如何配置gemini-cli>)

您通过编辑 `settings.json` 告诉 Gemini 有关新服务器的信息。

1. 打开`~/.gemini/settings.json`（或项目特定的
   `.gemini/settings.json`).
2. 添加 `mcpServers` 块。这告诉 Gemini：“运行这个 docker 容器
并与它交谈。”

```
{


"mcpServers": {


"github": {


"command": "docker",


"args": [


"run",


"-i",


"--rm",


"-e",


"GITHUB_PERSONAL_ACCESS_TOKEN",


"ghcr.io/github/github-mcp-server:latest"


],


"env": {


"GITHUB_PERSONAL_ACCESS_TOKEN": "${GITHUB_PERSONAL_ACCESS_TOKEN}"


}


}


}


}
```

## 如何验证连接

[标题为“如何验证连接”的部分](<#如何验证连接>)

重新启动Gemini CLI。它将自动尝试启动定义的服务器。

**命令：** `/mcp list`

您应该看到：`✓ github: docker ... - Connected`

如果您看到 `Disconnected` 或错误，请检查 Docker 是否正在运行并且您的 API
 令牌是否有效。

## 如何使用新工具

[标题为“如何使用新工具”的部分](<#如何使用新工具>)

现在服务器正在运行，agent 具有新功能（“工具”）。你
不需要学习特殊命令；只需用自然语言询问即可。

### 场景：列出拉取请求

[标题为“场景：列出拉取请求”的部分](<#场景列出拉取请求>)

**提示：** `List the open PRs in the google/gemini-cli repository.`

agent 将：

1. 识别请求与 GitHub 工具匹配。
2. 拨打`mcp_github_list_pull_requests`。
3. 将数据呈现给您。

### 场景：创建问题

[标题为“场景：创建问题”的部分](<#场景创建问题>)

**提示：**
`Create an issue in my repo titled "Bug: Login fails" with the description "See logs".`

## 故障排除

[标题为“故障排除”的部分](<#故障排除>)

* **服务器无法启动？**尝试在您的计算机中手动运行docker命令
终端以查看是否打印错误（例如，“未找到图像”）。
* **找不到工具？**运行`/mcp reload`强制CLI重新查询服务器
因为它的能力。

## 后续步骤

[标题为“后续步骤”的部分](<#后续步骤>)

* 探索 [MCP 服务器参考 ](<https://geminicli.com/docs/tools/mcp-server>) 以了解
用于远程服务器的 SSE 和 HTTP 传输。
* 浏览
[官方MCP服务器列表](<https://github.com/modelcontextprotocol/servers>)到
查找Slack、Postgres、Google驱动器等的连接器。

最后更新：四月 10、2026


## 原文对照

<details>
<summary>展开完整原文</summary>

# Set up an MCP server

Connect Gemini CLI to your external databases and services. In this guide,
you’ll learn how to extend Gemini CLI’s capabilities by installing the GitHub
MCP server and using it to manage your repositories.

## Prerequisites

[Section titled “Prerequisites”](<#prerequisites>)

* Gemini CLI installed.
* **Docker:** Required for this specific example (many MCP servers run as Docker
  containers).
* **GitHub token:** A Personal Access Token (PAT) with repo permissions.

## How to prepare your credentials

[Section titled “How to prepare your credentials”](<#how-to-prepare-your-credentials>)

Most MCP servers require authentication. For GitHub, you need a PAT.

1. Create a [fine-grained PAT](<https://github.com/settings/tokens?type=beta>).
2. Grant it **Read** access to **Metadata** and **Contents**, and
   **Read/Write** access to **Issues** and **Pull Requests**.
3. Store it in your environment:

**macOS/Linux**

Terminal window

```
export GITHUB_PERSONAL_ACCESS_TOKEN="github_pat_..."
```

**Windows (PowerShell)**

Terminal window

```
$env:GITHUB_PERSONAL_ACCESS_TOKEN="github_pat_..."
```

## How to configure Gemini CLI

[Section titled “How to configure Gemini CLI”](<#how-to-configure-gemini-cli>)

You tell Gemini about new servers by editing your `settings.json`.

1. Open `~/.gemini/settings.json` (or the project-specific
   `.gemini/settings.json`).
2. Add the `mcpServers` block. This tells Gemini: “Run this docker container
   and talk to it.”

```
{


"mcpServers": {


"github": {


"command": "docker",


"args": [


"run",


"-i",


"--rm",


"-e",


"GITHUB_PERSONAL_ACCESS_TOKEN",


"ghcr.io/github/github-mcp-server:latest"


],


"env": {


"GITHUB_PERSONAL_ACCESS_TOKEN": "${GITHUB_PERSONAL_ACCESS_TOKEN}"


}


}


}


}
```

## How to verify the connection

[Section titled “How to verify the connection”](<#how-to-verify-the-connection>)

Restart Gemini CLI. It will automatically try to start the defined servers.

**Command:** `/mcp list`

You should see: `✓ github: docker ... - Connected`

If you see `Disconnected` or an error, check that Docker is running and your API
token is valid.

## How to use the new tools

[Section titled “How to use the new tools”](<#how-to-use-the-new-tools>)

Now that the server is running, the agent has new capabilities (“tools”). You
don’t need to learn special commands; just ask in natural language.

### Scenario: Listing pull requests

[Section titled “Scenario: Listing pull requests”](<#scenario-listing-pull-requests>)

**Prompt:** `List the open PRs in the google/gemini-cli repository.`

The agent will:

1. Recognize the request matches a GitHub tool.
2. Call `mcp_github_list_pull_requests`.
3. Present the data to you.

### Scenario: Creating an issue

[Section titled “Scenario: Creating an issue”](<#scenario-creating-an-issue>)

**Prompt:**
`Create an issue in my repo titled "Bug: Login fails" with the description "See logs".`

## Troubleshooting

[Section titled “Troubleshooting”](<#troubleshooting>)

* **Server won’t start?** Try running the docker command manually in your
  terminal to see if it prints an error (for example, “image not found”).
* **Tools not found?** Run `/mcp reload` to force the CLI to re-query the server
  for its capabilities.

## Next steps

[Section titled “Next steps”](<#next-steps>)

* Explore the [MCP servers reference](<https://geminicli.com/docs/tools/mcp-server>) to learn about
  SSE and HTTP transports for remote servers.
* Browse the
  [official MCP server list](<https://github.com/modelcontextprotocol/servers>) to
  find connectors for Slack, Postgres, Google Drive, and more.

Last updated: Apr 10, 2026


</details>
