# 在 Antigravity 中接入 MCP

说明 MCP 如何提供外部上下文与工具，按 IDE、桌面和 CLI 展示配置入口、服务作用范围及管理方法。

- 来源：Google／Antigravity
- 资料类型：存档文档
- 文档时间：官方未公开发表/更新时间（[已复查](<https://antigravity.google/docs/mcp/>)）；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://antigravity.google/docs/mcp>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Antigravity/在 Antigravity 中接入 MCP.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

[查看降价](<#>)

可用于：

反重力 2.0反重力 CLI 反重力 IDE

# Model Context Protocol (MCP)

[标题为“Model Context Protocol (MCP)”的部分](<#model-context-protocol-mcp>)

Antigravity 支持 [Model Context Protocol (MCP)](<https://modelcontextprotocol.io>)，这是一种开放标准，可让 AI agents 和编辑器安全地连接到本地开发人员工具、数据库、文件解析器和外部远程 API。这种集成为 AI 模型提供了超出您直接工作空间的实时上下文和执行功能。

在本指南中，您将了解如何跨 Antigravity 产品连接和配置 MCP 服务器。

## MCP是什么？

[标题为“什么是MCP？”的部分](<#mcp是什么>)

MCP 充当反重力和更广泛的开发环境之间的通用桥梁。而不是手动复制和粘贴数据库架构、日志或 API 将规范添加到提示或聊天面板中， MCP 让 Antigravity 直接获取结构化上下文或在需要时代表您执行安全操作。

### 添加上下文

[标题为“添加上下文”的部分](<#添加上下文>)

通过 MCP，Antigravity 可以使用来自连接的 MCP 服务器的实时数据来告知其推理和建议：

* 编写 SQL 查询时，Antigravity 可以检查实时 Neon、Supabase 或 AlloyDB 架构，以建议正确的表和列名称。
* 在调试部署失败时，Antigravity 可以直接从 Netlify 或 Heroku 提取最新的构建日志。

### 添加自定义工具

[标题为“添加自定义工具”的部分](<#添加自定义工具>)

通过 MCP，Antigravity 可以执行由您连接的服务器定义的特定、安全的操作：

* 为 `TODO` 项目创建线性问题。
* 搜索 Notion 或 GitHub 以获取身份验证模式。

## 从表面开始

[标题为“表面入门”的部分](<#从表面开始>)

* [反重力 2.0](<#tab-panel-61>)
* [反重力 CLI](<#tab-panel-62>)
* [反重力 IDE](<#tab-panel-63>)

### 反重力2.0

[标题为“反重力2.0”的部分](<#sr-tab-1>)

在反重力2.0中，您可以通过**设置**的**已安装MCP服务器**部分管理您的MCP服务器。

要查看并更新您的 MCP 服务器：

1. 单击屏幕左下角的**设置**按钮。
2. 选择**自定义**并查看**已安装的MCP服务器**部分。

要从 **已安装的 MCP 服务器 ** 部分安装 MCP 服务器：

1. 单击**添加MCP**。这会将您连接到 MCP 商店，这是可用 MCP 服务器的可搜索列表。
2. 搜索或向下滚动到您想要安装的 MCP 服务器。
3. 单击**添加**。

要从此屏幕管理您的 MCP 服务器：

* **卸载**：单击列表中MCP服务器旁边的垃圾桶图标。
* **禁用或启用**：单击列表中MCP服务器旁边的切换开关。
* **刷新**：单击刷新按钮。

### 反重力CLI

[标题为“反重力CLI”的部分](<#sr-tab-2>)

反重力 CLI 支持本地 `stdio` 进程和远程主机 MCP 服务器配置。在反重力 CLI 上安装 MCP 服务器的最简单方法是使用 **交互式 MCP 管理器 **。您还可以手动编辑全局服务器设置或工作区级别 `mcp_config.json`。

#### 互动【MCP】经理

[标题为“交互式 MCP 管理器”的部分](<#互动mcp经理>)

在提示面板中键入 `/mcp`，然后按 `Enter` 打开交互式 **MCP 管理器覆盖图 **。该面板允许您执行以下操作：

* 查看活动、断开连接或正在加载的服务器的实时状态环。
* 手动重新加载服务器配置或检查实时连接日志。

#### 全局和工作区服务器配置

[标题为“全局和工作区服务器配置”的部分](<#全局和工作区服务器配置>)

与传统设置不同，反重力 CLI 将 MCP 定义分离为专用的稀疏配置：

* **全局服务器设置**：在`~/.gemini/config/mcp_config.json`中配置。
* **工作区本地设置**：在活动项目中的`.agents/mcp_config.json`下配置。

您可以使用自定义的[MCP服务器配置](<https://antigravity.google/docs/mcp#mcp-configuration-structure>)直接修改这些文件。

### 反重力IDE

[标题为“反重力IDE”的部分](<#sr-tab-3>)

在反重力IDE中，管理MCP服务器的最简单方法是通过内置的MCP商店。在 MCP 商店中，您可以浏览、发现和安装受支持的 MCP 服务器。您还可以通过更新 `mcp_config.json` 来安装自定义服务器。

使用MCP商店：

1. 单击编辑器agent侧面板顶部的**…**，然后选择**MCP服务器**。
2. 将鼠标悬停在任何支持的服务器上，然后单击 **安装**（或单击服务器以查看详细信息，然后单击 **安装**）。
3. 按照屏幕上的提示进行操作。

安装后，编辑器将自动使用服务器中的资源和工具。

要连接到商店中未列出的自定义 MCP 服务器：

1. 单击编辑器agent侧面板顶部的**…**，然后选择**MCP服务器**。
2. 单击**管理MCP服务器**。
3. 单击**查看原始配置**。
4. 使用您的自定义 [MCP 服务器配置 ](<https://antigravity.google/docs/mcp#mcp-configuration-structure>) 修改 `mcp_config.json` 文件。

配置文件全局位于 `~/.gemini/config/mcp_config.json`（或本地工作区中的 `.agents/mcp_config.json`）。

## MCP配置结构

[标题为“MCP配置结构”的部分](<#mcp配置结构>)

无论是为反重力 2.0、反重力 IDE 还是反重力 CLI 配置自定义服务器，配置文件都遵循标准化格式。该文件包含一个 `mcpServers` 对象，您可以在其中定义要连接的每个服务器：

```
{
  "mcpServers": {
    "sqlite-explorer": {
      "command": "node",
      "args": ["/usr/local/bin/sqlite-mcp-server.js"],
      "env": {
        "SQLITE_DB_PATH": "/var/data/app.db"
      }
    },
    "my-remote-server": {
      "serverUrl": "https://api.example.com/mcp/",
      "headers": {
        "Authorization": "Bearer YOUR_API_TOKEN"
      }
    }
  }
}
```

### MCP配置属性

[标题为“MCP配置属性”的部分](<#mcp配置属性>)

`mcpServers` 下的每个服务器条目都支持以下属性。

需要以下传输属性之一：

* **`command`**（字符串）：`stdio` 传输的可执行文件的路径。
* **`serverUrl`**（字符串）：URL 用于远程 `Streamable HTTP` 或 `SSE` 服务器。

配置以下任意可选属性：

* **`args`**（字符串数组）：`stdio` 传输的命令行参数。
* **`env`**（对象）：`stdio`服务器进程的环境变量。
* **`cwd`**（字符串）：`stdio` 服务器的工作目录。
* **`headers`**（对象）：远程服务器的自定义 HTTP 标头。
* **`authProviderType`**（字符串）：身份验证提供程序。支持 Google 应用程序默认凭据 (ADC) 的 `"google_credentials"`。
* **`oauth`**（对象）：OAuth 客户端凭据（`clientId`、`clientSecret`）。
* **`disabled`**（布尔值）：暂时禁用服务器而不删除其配置。
* **`disabledTools`**（字符串数组）：要从模型中保留的工具名称。

## MCP 认证

[标题为“MCP身份验证”的部分](<#mcp-认证>)

连接的 MCP 服务器可以使用内置 Google 凭据、自动 OAuth 流或自定义 HTTP 标头对外部服务进行安全身份验证。

### Google 凭证

[标题为“Google凭证”的部分](<#google-凭证>)

将 `authProviderType` 设置为 `"google_credentials"` 以使用 Google 应用程序默认凭据 (ADC)：

```
{
  "mcpServers": {
    "my-gcp-service": {
      "serverUrl": "https://example.googleapis.com/mcp/",
      "authProviderType": "google_credentials"
    }
  }
}
```

这需要在本地配置应用程序默认凭据。要设置它们，请运行：

```
gcloud auth application-default login
```

如果您之前登录过，请确保通过运行以下命令设置您的配额项目：

```
gcloud auth application-default set-quota-project {QUOTA_PROJECT}
```

### 开放认证

[标题为“OAuth”的部分](<#开放认证>)

Antigravity 自动处理支持动态客户端注册 (DCR) 的服务器的 OAuth。对于这些服务器，不需要额外的配置：

```
{
  "mcpServers": {
    "oauth-server": {
      "serverUrl": "https://api.example.com/mcp/"
    }
  }
}
```

如果服务器不支持动态客户端注册，您可以手动提供客户端凭据：

```
{
  "mcpServers": {
    "oauth-server": {
      "serverUrl": "https://api.example.com/mcp/",
      "oauth": {
        "clientId": "your-client-id",
        "clientSecret": "your-client-secret"
      }
    }
  }
}
```

如果您手动提供客户端凭据，请确保以下 URL 在 OAuth 提供商中注册为重定向 URI：

```
https://antigravity.google/oauth-callback
```

要连接到启用 OAuth 的服务器：

1. 使用 `Cmd` + `,` (macOS) 或 `Ctrl` + `,` 打开 [**Agent 设置 **](<https://antigravity.google/docs/settings>)（Windows 和 Linux）。
2. 导航到 ** 自定义 ** 选项卡，然后单击服务器旁边的 ** 身份验证 **。

![点击验证](<https://antigravity.google/assets/image/docs/tools/mcp-oauth-authenticate.png>)
3. 在浏览器中完成身份验证并复制授权码。

![复制授权码](<https://antigravity.google/assets/image/docs/tools/mcp-oauth-copy-code.png>)
4. 将代码粘贴回设置面板，然后单击**提交**。

![粘贴授权码](<https://antigravity.google/assets/image/docs/tools/mcp-oauth-paste-code.png>)

一旦通过身份验证，服务器会自动重新连接。

![经过身份验证的服务器](<https://antigravity.google/assets/image/docs/tools/mcp-oauth-authenticated.png>)

访问令牌存储在`~/.gemini/antigravity/mcp_oauth_tokens.json`中。过期的令牌会自动刷新，无效的令牌会被删除。

### 自定义标头

[标题为“自定义标头”的部分](<#自定义标头>)

对于需要自定义 HTTP 标头（例如 API 密钥或不记名令牌）的远程服务器，请将它们添加到 `headers` 对象。例如：

```
{
  "mcpServers": {
    "my-remote-server": {
      "serverUrl": "https://api.example.com/mcp/",
      "headers": {
        "Authorization": "Bearer YOUR_API_TOKEN"
      }
    }
  }
}
```

## MCP权限和访问控制

[标题为“MCP权限和访问控制”的部分](<#mcp权限和访问控制>)

对 Model Context Protocol 工具和资源的访问受 Antigravity 的 [ 权限系统 ](<https://antigravity.google/docs/permissions>) 管理。默认情况下，未配置的MCP工具以**询问**模式运行，需要您的批准才能执行。您可以在策略配置中允许特定工具或整个服务器：

* **`mcp(server/tool)`**：匹配特定服务器上的特定工具。
* **`mcp(server/*)`**：匹配指定服务器上的所有工具。
* **`mcp(*)`**：与所有连接的服务器上的任何 MCP 工具匹配的全局通配符。

## 支持的 MCP 服务器

[标题为“支持的 MCP 服务器”的部分](<#支持的-mcp-服务器>)

MCP 商店可直接集成各种开发者平台、数据库和生产力服务：

数据库和存储（15 服务器）

* [AlloyDB 为 PostgreSQL](<https://cloud.google.com/alloydb/docs/ai/use-alloydb-mcp>)
* [BigQuery](<https://cloud.google.com/bigquery/docs/use-bigquery-mcp>)
* [Bigtable 管理员远程 MCP](<https://docs.cloud.google.com/bigtable/docs/use-bigtable-mcp>)
* [ClickHouse](<https://clickhouse.com/docs/use-cases/AI/MCP>)
* [云 SQL（MySQL、PostgreSQL、SQL 服务器，托管）](<https://docs.cloud.google.com/sql/docs/mysql/use-cloudsql-mcp>)
* [数据中心](<https://docs.cloud.google.com/dataplex/docs/use-remote-mcp>)
* [MCP 数据库工具箱](<https://mcp-toolbox.dev/documentation/introduction/>)
* [MongoDB](<https://github.com/mongodb-js/mongodb-mcp-server>)
* [Neo4j](<https://neo4j.com/docs/mcp/current/>)
* [霓虹灯](<https://github.com/neondatabase-labs/mcp-server-neon>)
* [松果](<https://github.com/pinecone-io/pinecone-mcp>)
* [棱镜](<https://github.com/prisma/prisma?tab=readme-ov-file#mcp-server>)
* [Redis](<https://github.com/redis/mcp-redis>)
* [扳手](<https://docs.cloud.google.com/spanner/docs/use-spanner-mcp>)
* [Supabase](<https://github.com/supabase-community/supabase-mcp>)

开发者工具和CI/CD（17服务器）

* [Apigee MCP](<https://docs.cloud.google.com/apigee/docs/reference/apis/apihub/mcp>)
* [阿特拉斯](<https://support.atlassian.com/atlassian-rovo-mcp-server/docs/getting-started-with-the-atlassian-remote-mcp-server/>)
* [云CLI执行](<https://docs.cloud.google.com/sdk/use-gcloud-mcp>)
* [锻造](<https://app.softwareforge.ai/docs/introduction>)
* [GitHub](<https://github.com/github/github-mcp-server>)
* [GitLab 轨道](<https://docs.gitlab.com/orbit/remote/access/mcp/>)
* [GKE OneMCP](<https://docs.cloud.google.com/kubernetes-engine/docs/how-to/use-gke-mcp>)
* [安全带](<https://github.com/harness/mcp-server>)
* [Heroku](<https://github.com/heroku/heroku-mcp-server>)
* [家庭开发商 MCP](<https://developers.home.google.com/mcp/developer>)
* [线性](<https://linear.app/changelog/2025-05-01-mcp>)
* [mabl](<https://help.mabl.com/hc/en-us/articles/47299375773844-mabl-MCP-overview>)
* [Netlify](<https://github.com/netlify/netlify-mcp>)
* [Opsera AI Agents](<https://docs.agents.opsera.ai/>)
* [邮递员](<https://github.com/postmanlabs/postman-mcp-server>)
* [SonarQube](<https://github.com/SonarSource/sonarqube-mcp-server>)
* [维塞尔](<https://vercel.com/docs/agent-resources/vercel-mcp>)

前端和设计（8服务器）

* [帆布](<https://www.canva.dev/docs/mcp/>)
* [镀铬 DevTools](<https://github.com/ChromeDevTools/chrome-devtools-mcp>)
* [飞镖](<https://dart.dev/tools/mcp-server>)
* [Figma 开发模式 MCP](<https://help.figma.com/hc/en-us/articles/32132100833559-Guide-to-the-Dev-Mode-MCP-Server>)
* [乐客飞](<https://www.locofy.ai/docs/export-and-deployment/locofy-mcp/>)
* [可爱 MCP](<https://docs.lovable.dev/integrations/lovable-mcp-server>)
* [米罗](<https://developers.miro.com/docs/miro-mcp>)
* [摩宾 MCP](<https://mobbin.com/mcp>)

安全性（4 服务器）

* [CrowdStrike](<https://github.com/CrowdStrike/falcon-mcp>)
* [Endor实验室](<https://docs.endorlabs.com/setup-deployment/mcp/antigravity>)
* [Splunk](<https://splunkbase.splunk.com/app/7931>)
* [维兹](<https://www.wiz.io/blog/introducing-wiz-mcp>)

分析、AI 和云（32 服务器）

* [爱维维](<https://github.com/airweave-ai/airweave>)
* [反金属](<https://docs.antimetal.com/connect>)
* [阿里兹](<https://github.com/Arize-ai/arize-tracing-assistant>)
* [云审计管理器](<https://docs.cloud.google.com/audit-manager/docs/reference/auditmanager/mcp>)
* [法庭5](<https://courtroom5.com/>)
* [达洛帕](<https://docs.daloopa.com/docs/mcp>)
* [文档签名](<https://developers.docusign.com/platform/mcp-server/>)
* [Exa](<https://docs.exa.ai/reference/exa-mcp>)
* [FactSet AI-就绪数据](<https://developer.factset.com/mcp/factset-ai-ready-data-mcp>)
* [Finnhub](<https://finnhub.io/mcp>)
* [Firebase](<https://firebase.google.com/docs/ai-assistance/mcp-server>)
* [Fiscal.ai](<https://docs.fiscal.ai/docs/guides/mcp-integration>)
* [Google 云配额](<https://cloud.google.com/docs/quotas/overview>)
* [Grafana云](<https://github.com/grafana/ai-marketplace/tree/main/plugins/grafana-cloud-mcp>)
* [指南](<https://github.com/guidepointglobal/mcp-skills-plugins>)
* [哈维](<https://developers.harvey.ai/guides/harvey_mcp>)
* [iManage](<https://docs.imanage.com/ai/mcpserver-user-help/en-US/iManage_MCP_Server_Help.html>)
* [LegalZoom](<https://www.legalzoom.com/ai>)
* [Looker](<https://mcp-toolbox.dev/documentation/connect-to/ides/looker_mcp/>)
* [LSEG](<https://developers.lseg.com/>)
* [NetDocuments](<https://docs.cloud.google.com/gemini/enterprise/docs/connectors/netdocuments>)
* [Notion](<https://github.com/makenotion/notion-mcp-server>)
* [并行搜索](<https://docs.parallel.ai/search/search-mcp>)
* [PayPal](<https://developer.paypal.com/tools/mcp-server/>)
* [困惑问](<https://github.com/ppl-ai/modelcontextprotocol>)
* [PostHog](<https://posthog.com/mcp>)
* [相对论](<https://docs.cloud.google.com/gemini/enterprise/docs/connectors/relativity>)
* [标普环球](<https://github.com/kensho-technologies/kfinance>)
* [Salesforce](<https://developer.salesforce.com/docs/platform/hosted-mcp-servers/guide/sobject-all.html>)
* [顺序思维](<https://github.com/modelcontextprotocol/servers/tree/main/src/sequentialthinking>)
* [条纹](<https://github.com/stripe/agent-toolkit/tree/main/modelcontextprotocol>)
* [温莎 AI](<https://windsor.ai/documentation/windsor-mcp/>)


## 原文对照

<details>
<summary>展开完整原文</summary>

[View Markdown](<#>)

Available on:

Antigravity 2.0Antigravity CLIAntigravity IDE

# Model Context Protocol (MCP)

[Section titled “Model Context Protocol (MCP)”](<#model-context-protocol-mcp>)

Antigravity supports the [Model Context Protocol (MCP)](<https://modelcontextprotocol.io>), an open standard that lets AI agents and editors securely connect to local developer tools, databases, file parsers, and external remote APIs. This integration provides the AI model with real-time context and execution capabilities beyond your immediate workspace.

In this guide, you’ll learn how to connect and configure MCP servers across Antigravity products.

## What is MCP?

[Section titled “What is MCP?”](<#what-is-mcp>)

MCP acts as a universal bridge between Antigravity and your broader development environment. Instead of manually copying and pasting database schemas, logs, or API specifications into prompts or chat panels, MCP lets Antigravity fetch structured context directly or execute safe actions on your behalf when needed.

### Add context

[Section titled “Add context”](<#add-context>)

With MCP, Antigravity can use live data from connected MCP servers to inform its reasoning and suggestions:

* When writing a SQL query, Antigravity can inspect your live Neon, Supabase, or AlloyDB schema to suggest correct table and column names.
* When debugging deployment failures, Antigravity can pull recent build logs directly from Netlify or Heroku.

### Add custom tools

[Section titled “Add custom tools”](<#add-custom-tools>)

With MCP, Antigravity can execute specific, safe actions defined by your connected servers:

* Create a Linear issue for a `TODO` item.
* Search Notion or GitHub for authentication patterns.

## Getting started by surface

[Section titled “Getting started by surface”](<#getting-started-by-surface>)

* [Antigravity 2.0](<#tab-panel-61>)
* [Antigravity CLI](<#tab-panel-62>)
* [Antigravity IDE](<#tab-panel-63>)

### Antigravity 2.0

[Section titled “Antigravity 2.0”](<#sr-tab-1>)

In Antigravity 2.0, you can manage your MCP servers through the **Installed MCP Servers** section of your **Settings**.

To view and update your MCP servers:

1. Click the **Settings** button on the bottom left of your screen.
2. Select **Customizations** and review the **Installed MCP Servers** section.

To install an MCP server from the **Installed MCP Servers** section:

1. Click **Add MCP**. This connects you to the MCP Store, a searchable list of available MCP servers.
2. Search or scroll down to an MCP server you’d like to install.
3. Click **Add**.

To manage your MCP servers from this screen:

* **Uninstall**: Click the trash can icon next to the MCP server in the list.
* **Disable or enable**: Click the toggle switch next to the MCP server in the list.
* **Refresh**: Click the refresh button.

### Antigravity CLI

[Section titled “Antigravity CLI”](<#sr-tab-2>)

Antigravity CLI supports both local `stdio` processes and remote host MCP server configurations. The simplest path to installing an MCP server on Antigravity CLI is by using the **Interactive MCP Manager**. You can also manually edit your global server setup or workspace-level `mcp_config.json`.

#### Interactive MCP manager

[Section titled “Interactive MCP manager”](<#interactive-mcp-manager>)

Type `/mcp` inside the prompt panel and press `Enter` to open the interactive **MCP Manager Overlay**. This panel lets you perform the following actions:

* View live status rings for active, disconnected, or loading servers.
* Manually reload server configurations or inspect real-time connection logs.

#### Global and workspace server configs

[Section titled “Global and workspace server configs”](<#global-and-workspace-server-configs>)

Unlike legacy setups, Antigravity CLI separates MCP definitions into dedicated, sparse configurations:

* **Global server setups**: Configured in `~/.gemini/config/mcp_config.json`.
* **Workspace local setups**: Configured in your active project under `.agents/mcp_config.json`.

You can modify these files directly with your custom [MCP server configuration](<https://antigravity.google/docs/mcp#mcp-configuration-structure>).

### Antigravity IDE

[Section titled “Antigravity IDE”](<#sr-tab-3>)

In Antigravity IDE, the easiest way to manage MCP servers is through the built-in MCP Store. In the MCP Store, you can browse, discover, and install supported MCP servers. You can also install custom servers by updating your `mcp_config.json`.

To use the MCP Store:

1. Click **…** at the top of the editor’s agent side panel and select **MCP Servers**.
2. Hover over any supported server and click **Install** (or click a server to view details and then click **Install**).
3. Follow any on-screen prompts.

Once installed, resources and tools from the server are automatically available to the editor.

To connect to a custom MCP server not listed in the store:

1. Click **…** at the top of the editor’s agent side panel and select **MCP Servers**.
2. Click **Manage MCP Servers**.
3. Click **View raw config**.
4. Modify the `mcp_config.json` file with your custom [MCP server configuration](<https://antigravity.google/docs/mcp#mcp-configuration-structure>).

The configuration file is located globally at `~/.gemini/config/mcp_config.json` (or locally in your workspace under `.agents/mcp_config.json`).

## MCP configuration structure

[Section titled “MCP configuration structure”](<#mcp-configuration-structure>)

Whether configuring custom servers for Antigravity 2.0, Antigravity IDE, or Antigravity CLI, the configuration file follows a standardized format. The file contains a single `mcpServers` object where you define each server you want to connect to:

```
{
  "mcpServers": {
    "sqlite-explorer": {
      "command": "node",
      "args": ["/usr/local/bin/sqlite-mcp-server.js"],
      "env": {
        "SQLITE_DB_PATH": "/var/data/app.db"
      }
    },
    "my-remote-server": {
      "serverUrl": "https://api.example.com/mcp/",
      "headers": {
        "Authorization": "Bearer YOUR_API_TOKEN"
      }
    }
  }
}
```

### MCP configuration properties

[Section titled “MCP configuration properties”](<#mcp-configuration-properties>)

Each server entry under `mcpServers` supports the following properties.

Require one of the following transport properties:

* **`command`** (string): Path to the executable for `stdio` transport.
* **`serverUrl`** (string): URL for remote `Streamable HTTP` or `SSE` servers.

Configure any of the following optional properties:

* **`args`** (array of strings): Command-line arguments for `stdio` transport.
* **`env`** (object): Environment variables for the `stdio` server process.
* **`cwd`** (string): Working directory for `stdio` servers.
* **`headers`** (object): Custom HTTP headers for remote servers.
* **`authProviderType`** (string): Authentication provider. Supports `"google_credentials"` for Google Application Default Credentials (ADC).
* **`oauth`** (object): OAuth client credentials (`clientId`, `clientSecret`).
* **`disabled`** (boolean): Temporarily disable a server without removing its configuration.
* **`disabledTools`** (array of strings): Tool names to withhold from the model.

## MCP authentication

[Section titled “MCP authentication”](<#mcp-authentication>)

Connected MCP servers can securely authenticate against external services using built-in Google credentials, automatic OAuth flows, or custom HTTP headers.

### Google credentials

[Section titled “Google credentials”](<#google-credentials>)

Set `authProviderType` to `"google_credentials"` to use Google Application Default Credentials (ADC):

```
{
  "mcpServers": {
    "my-gcp-service": {
      "serverUrl": "https://example.googleapis.com/mcp/",
      "authProviderType": "google_credentials"
    }
  }
}
```

This requires Application Default Credentials to be configured locally. To set them up, run:

```
gcloud auth application-default login
```

If you previously logged in, ensure your quota project is set by running:

```
gcloud auth application-default set-quota-project {QUOTA_PROJECT}
```

### OAuth

[Section titled “OAuth”](<#oauth>)

Antigravity automatically handles OAuth for servers that support dynamic client registration (DCR). For these servers, no additional configuration is needed:

```
{
  "mcpServers": {
    "oauth-server": {
      "serverUrl": "https://api.example.com/mcp/"
    }
  }
}
```

If the server doesn’t support dynamic client registration, you can provide your client credentials manually:

```
{
  "mcpServers": {
    "oauth-server": {
      "serverUrl": "https://api.example.com/mcp/",
      "oauth": {
        "clientId": "your-client-id",
        "clientSecret": "your-client-secret"
      }
    }
  }
}
```

If you provide client credentials manually, ensure the following URL is registered as a redirect URI in your OAuth provider:

```
https://antigravity.google/oauth-callback
```

To connect to an OAuth-enabled server:

1. Open [**Agent settings**](<https://antigravity.google/docs/settings>) with `Cmd` + `,` (macOS) or `Ctrl` + `,` (Windows and Linux).
2. Navigate to the **Customizations** tab and click **Authenticate** next to the server.

   ![Click Authenticate](<https://antigravity.google/assets/image/docs/tools/mcp-oauth-authenticate.png>)
3. Complete authentication in your browser and copy the authorization code.

   ![Copy authorization code](<https://antigravity.google/assets/image/docs/tools/mcp-oauth-copy-code.png>)
4. Paste the code back into the settings panel and click **Submit**.

   ![Paste auth code](<https://antigravity.google/assets/image/docs/tools/mcp-oauth-paste-code.png>)

Once authenticated, the server reconnects automatically.

![Authenticated server](<https://antigravity.google/assets/image/docs/tools/mcp-oauth-authenticated.png>)

Access tokens are stored in `~/.gemini/antigravity/mcp_oauth_tokens.json`. Expired tokens are refreshed automatically, and invalid tokens are removed.

### Custom headers

[Section titled “Custom headers”](<#custom-headers>)

For remote servers that require custom HTTP headers (such as API keys or bearer tokens), add them to the `headers` object. For example:

```
{
  "mcpServers": {
    "my-remote-server": {
      "serverUrl": "https://api.example.com/mcp/",
      "headers": {
        "Authorization": "Bearer YOUR_API_TOKEN"
      }
    }
  }
}
```

## MCP permissions and access control

[Section titled “MCP permissions and access control”](<#mcp-permissions-and-access-control>)

Access to Model Context Protocol tools and resources is governed by Antigravity’s [permissions system](<https://antigravity.google/docs/permissions>). By default, unconfigured MCP tools run in **Ask** mode, requiring your approval before execution. You can allow specific tools or entire servers in your policy configuration:

* **`mcp(server/tool)`**: Matches a specific tool on a specific server.
* **`mcp(server/*)`**: Matches all tools on a specified server.
* **`mcp(*)`**: Global wildcard matching any MCP tool across all connected servers.

## Supported MCP servers

[Section titled “Supported MCP servers”](<#supported-mcp-servers>)

The MCP Store features direct integrations for a wide variety of developer platforms, databases, and productivity services:

Databases and storage (15 servers)

* [AlloyDB for PostgreSQL](<https://cloud.google.com/alloydb/docs/ai/use-alloydb-mcp>)
* [BigQuery](<https://cloud.google.com/bigquery/docs/use-bigquery-mcp>)
* [Bigtable Admin remote MCP](<https://docs.cloud.google.com/bigtable/docs/use-bigtable-mcp>)
* [ClickHouse](<https://clickhouse.com/docs/use-cases/AI/MCP>)
* [Cloud SQL (MySQL, PostgreSQL, SQL Server, Managed)](<https://docs.cloud.google.com/sql/docs/mysql/use-cloudsql-mcp>)
* [Dataplex](<https://docs.cloud.google.com/dataplex/docs/use-remote-mcp>)
* [MCP Toolbox for Databases](<https://mcp-toolbox.dev/documentation/introduction/>)
* [MongoDB](<https://github.com/mongodb-js/mongodb-mcp-server>)
* [Neo4j](<https://neo4j.com/docs/mcp/current/>)
* [Neon](<https://github.com/neondatabase-labs/mcp-server-neon>)
* [Pinecone](<https://github.com/pinecone-io/pinecone-mcp>)
* [Prisma](<https://github.com/prisma/prisma?tab=readme-ov-file#mcp-server>)
* [Redis](<https://github.com/redis/mcp-redis>)
* [Spanner](<https://docs.cloud.google.com/spanner/docs/use-spanner-mcp>)
* [Supabase](<https://github.com/supabase-community/supabase-mcp>)

Developer tools and CI/CD (17 servers)

* [Apigee MCP](<https://docs.cloud.google.com/apigee/docs/reference/apis/apihub/mcp>)
* [Atlassian](<https://support.atlassian.com/atlassian-rovo-mcp-server/docs/getting-started-with-the-atlassian-remote-mcp-server/>)
* [Cloud CLI Execution](<https://docs.cloud.google.com/sdk/use-gcloud-mcp>)
* [Forge](<https://app.softwareforge.ai/docs/introduction>)
* [GitHub](<https://github.com/github/github-mcp-server>)
* [GitLab Orbit](<https://docs.gitlab.com/orbit/remote/access/mcp/>)
* [GKE OneMCP](<https://docs.cloud.google.com/kubernetes-engine/docs/how-to/use-gke-mcp>)
* [Harness](<https://github.com/harness/mcp-server>)
* [Heroku](<https://github.com/heroku/heroku-mcp-server>)
* [Home Developer MCP](<https://developers.home.google.com/mcp/developer>)
* [Linear](<https://linear.app/changelog/2025-05-01-mcp>)
* [mabl](<https://help.mabl.com/hc/en-us/articles/47299375773844-mabl-MCP-overview>)
* [Netlify](<https://github.com/netlify/netlify-mcp>)
* [Opsera AI Agents](<https://docs.agents.opsera.ai/>)
* [Postman](<https://github.com/postmanlabs/postman-mcp-server>)
* [SonarQube](<https://github.com/SonarSource/sonarqube-mcp-server>)
* [Vercel](<https://vercel.com/docs/agent-resources/vercel-mcp>)

Frontend and design (8 servers)

* [Canva](<https://www.canva.dev/docs/mcp/>)
* [Chrome DevTools](<https://github.com/ChromeDevTools/chrome-devtools-mcp>)
* [Dart](<https://dart.dev/tools/mcp-server>)
* [Figma Dev Mode MCP](<https://help.figma.com/hc/en-us/articles/32132100833559-Guide-to-the-Dev-Mode-MCP-Server>)
* [Locofy](<https://www.locofy.ai/docs/export-and-deployment/locofy-mcp/>)
* [Lovable MCP](<https://docs.lovable.dev/integrations/lovable-mcp-server>)
* [Miro](<https://developers.miro.com/docs/miro-mcp>)
* [Mobbin MCP](<https://mobbin.com/mcp>)

Security (4 servers)

* [CrowdStrike](<https://github.com/CrowdStrike/falcon-mcp>)
* [Endor Labs](<https://docs.endorlabs.com/setup-deployment/mcp/antigravity>)
* [Splunk](<https://splunkbase.splunk.com/app/7931>)
* [Wiz](<https://www.wiz.io/blog/introducing-wiz-mcp>)

Analytics, AI, and cloud (32 servers)

* [Airweave](<https://github.com/airweave-ai/airweave>)
* [Antimetal](<https://docs.antimetal.com/connect>)
* [Arize](<https://github.com/Arize-ai/arize-tracing-assistant>)
* [Cloud Audit Manager](<https://docs.cloud.google.com/audit-manager/docs/reference/auditmanager/mcp>)
* [Courtroom5](<https://courtroom5.com/>)
* [Daloopa](<https://docs.daloopa.com/docs/mcp>)
* [Docusign](<https://developers.docusign.com/platform/mcp-server/>)
* [Exa](<https://docs.exa.ai/reference/exa-mcp>)
* [FactSet AI-Ready Data](<https://developer.factset.com/mcp/factset-ai-ready-data-mcp>)
* [Finnhub](<https://finnhub.io/mcp>)
* [Firebase](<https://firebase.google.com/docs/ai-assistance/mcp-server>)
* [Fiscal.ai](<https://docs.fiscal.ai/docs/guides/mcp-integration>)
* [Google Cloud Quotas](<https://cloud.google.com/docs/quotas/overview>)
* [Grafana Cloud](<https://github.com/grafana/ai-marketplace/tree/main/plugins/grafana-cloud-mcp>)
* [Guidepoint](<https://github.com/guidepointglobal/mcp-skills-plugins>)
* [Harvey](<https://developers.harvey.ai/guides/harvey_mcp>)
* [iManage](<https://docs.imanage.com/ai/mcpserver-user-help/en-US/iManage_MCP_Server_Help.html>)
* [LegalZoom](<https://www.legalzoom.com/ai>)
* [Looker](<https://mcp-toolbox.dev/documentation/connect-to/ides/looker_mcp/>)
* [LSEG](<https://developers.lseg.com/>)
* [NetDocuments](<https://docs.cloud.google.com/gemini/enterprise/docs/connectors/netdocuments>)
* [Notion](<https://github.com/makenotion/notion-mcp-server>)
* [Parallel Search](<https://docs.parallel.ai/search/search-mcp>)
* [PayPal](<https://developer.paypal.com/tools/mcp-server/>)
* [Perplexity Ask](<https://github.com/ppl-ai/modelcontextprotocol>)
* [PostHog](<https://posthog.com/mcp>)
* [Relativity](<https://docs.cloud.google.com/gemini/enterprise/docs/connectors/relativity>)
* [S&P Global](<https://github.com/kensho-technologies/kfinance>)
* [Salesforce](<https://developer.salesforce.com/docs/platform/hosted-mcp-servers/guide/sobject-all.html>)
* [Sequential Thinking](<https://github.com/modelcontextprotocol/servers/tree/main/src/sequentialthinking>)
* [Stripe](<https://github.com/stripe/agent-toolkit/tree/main/modelcontextprotocol>)
* [Windsor AI](<https://windsor.ai/documentation/windsor-mcp/>)


</details>
