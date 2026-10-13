# 为仓库配置 MCP 服务

说明配置 JSON、变量与机密引用，并用 Sentry、Notion、Azure 等示例扩展云端 Agent 和代码审查的工具能力。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 文档时间：官方文件提交（截至抓取）：[2026-09-30](<https://github.com/github/docs/commit/d9fd377ea956f59385ac8cb2034ba5c613b6fd85>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://raw.githubusercontent.com/github/docs/main/content/copilot/how-tos/copilot-on-github/customize-copilot/configure-mcp-servers.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/为仓库配置 MCP 服务.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Configure MCP servers for your repository
shortTitle: Configure MCP servers
intro: 'Configure Model Context Protocol (MCP) servers for your repository to give {% data variables.copilot.copilot_cloud_agent %} and {% data variables.copilot.copilot_code-review_short %} access to external tools and data sources.'
versions:
  feature: copilot
redirect_from:
  - /copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/extend-cloud-agent-with-mcp
  - /copilot/how-tos/use-copilot-agents/coding-agent/extend-coding-agent-with-mcp
  - /copilot/customizing-copilot/using-model-context-protocol/extending-copilot-coding-agent-with-mcp
  - /copilot/customizing-copilot/extending-copilot-coding-agent-with-mcp
  - /early-access/copilot/coding-agent/extending-copilot-coding-agent-with-model-context-protocol
  - /copilot/using-github-copilot/coding-agent/extending-copilot-coding-agent-with-mcp
  - /copilot/how-tos/agents/copilot-coding-agent/extending-copilot-coding-agent-with-mcp
  - /copilot/how-tos/agents/copilot-coding-agent/extend-coding-agent-with-mcp
  - /copilot/how-tos/agents/coding-agent/extend-coding-agent-with-mcp
  - /copilot/how-tos/use-copilot-agents/cloud-agent/extend-cloud-agent-with-mcp
contentType: how-tos
category:
  - Integrate Copilot with your tools
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

## 先决条件

在为存储库设置 MCP 服务器之前，请阅读 [AUTOTITLE](<https://raw.githubusercontent.com/copilot/concepts/context/mcp>) 和 [AUTOTITLE](<https://raw.githubusercontent.com/copilot/concepts/agents/cloud-agent/mcp-and-cloud-agent>)。

## 简介

作为存储库管理员，您可以配置 MCP 服务器以在存储库中使用。您可以使用 JSON- 格式的配置来执行此操作，该配置指定要使用的 MCP 服务器的详细信息。您可以将 JSON 配置直接输入到 {% data variables.product.prodname_dotcom_the_website %} 上存储库的设置中。

此存储库级 MCP 配置由 {% data variables.copilot.copilot_cloud_agent %} 和 {% data variables.copilot.copilot_code-review_short %} 共享。有关禁用 {% data variables.copilot.copilot_code-review_short %} 使用 MCP 服务器的信息，请参阅 [禁用 {% data variables.copilot.copilot_code-review_short %}](<#禁用-mcp-工具进行代码审查>) 的 MCP 工具。

> [!NOTE]
> 以前在 {% data variables.copilot.copilot_cloud_agent %} 设置下管理的现有存储库 MCP 配置已自动移至新的共享 MCP 设置页面。无需执行迁移操作。

默认情况下启用 {% data variables.product.github %} MCP 服务器和 Playwright MCP 服务器。您可以在这些默认服务器旁边添加您自己的 MCP 服务器。

组织和企业管理员还可以使用 YAML frontmatter 将 MCP 服务器配置为 {% data variables.copilot.custom_agents_short %} 的一部分。欲了解更多信息，请参阅[AUTOTITLE](<https://raw.githubusercontent.com/copilot/reference/custom-agents-configuration#mcp-server-configuration-details>)。

> [！WARNING]
> 一旦您配置了MCP服务器，{% data variables.product.prodname_copilot_short %}将能够自主使用服务器提供的工具，并且在使用前不会征求您的同意。

{% data reusables.copilot.mcp.repo-mcp-limitations %}

## 将 MCP 配置添加到您的存储库

存储库管理员可以按照以下步骤配置 MCP 服务器：

{% data reusables.repositories.navigate-to-repo %}
{% data reusables.repositories.sidebar-settings %}
1. {% data reusables.user-settings.code-planning-automation %} 单击 **{% data variables.product.prodname_copilot_short %}**，然后单击 **MCP 服务器 **。
1. 在“Model Context Protocol (MCP)”页面上，在“MCP 配置”部分添加您的配置。

本文的以下部分解释了如何编写您需要在此处输入的 JSON 配置。

1. 单击**保存MCP配置**。

您的配置将经过验证以确保语法正确。

1. 如果您的 MCP 服务器需要变量、密钥或密钥，请添加名称以 `COPILOT_MCP_` 为前缀的 Agents 密钥或变量。只有 Agents 机密和名称前缀为 `COPILOT_MCP_` 的变量可用于您的 MCP 配置。参见[AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/configure-secrets-and-variables>)。

## 为 MCP 服务器编写 JSON 配置

您可以使用特殊的 JSON 格式配置 MCP 服务器。 JSON 必须包含 `mcpServers` 对象，其中键是 MCP 服务器的名称（例如 `sentry`），值是具有该 MCP 服务器配置的对象。

```json copy
{
  "mcpServers": {
    "MCP SERVER 1": {
      "command": "VALUE",
      "args": [ VALUES ],
      ...
    },
    "MCP SERVER 2": {
      "command": "VALUE",
      "args": [ VALUES ],
      ...
    },
    ...
  }
}
```

配置对象可以包含以下键：

**本地和远程MCP服务器所需的密钥**
* `tools` (`string[]`)：从MCP服务器启用的工具。您也许可以在服务器的文档或其代码中找到工具列表。我们强烈建议您将特定的只读工具列入白名单，因为 agent 将能够自主使用这些工具，并且不会先请求您批准。您还可以通过在数组中包含 `*` 来启用所有工具。
* `type` (`string`)：{% data variables.copilot.copilot_cloud_agent %} 接受 `"local"`、`"stdio"`、`"http"` 或 `"sse"`。

> [!NOTE]
> 对于{% data variables.copilot.copilot_code-review_short %}，MCP服务器的`tools/list`响应中的每个工具必须将`annotations.readOnlyHint`设置为`true`以允许{% data variables.product.prodname_copilot_short %}使用它。当此注释缺失或设置为 `false` 时，{% data variables.copilot.copilot_code-review_short %} 会排除工具。更多信息请参见[MCP规范](<https://modelcontextprotocol.io/specification/2025-11-25/schema#toolannotations-readonlyhint>)中的工具注释。

**本地MCP特定键**
* `command` (`string`)：必填。运行以启动 MCP 服务器的命令。
* `args` (`string[]`)：必填。要传递给 `command` 的参数。
* `env` (`object`)：可选。要传递到服务器的环境变量。该对象应将应公开给您的 MCP 服务器的环境变量的名称映射到以下之一：
  * 对 {% data variables.product.prodname_copilot_short %} 环境中的机密或变量的替换引用，例如 `$COPILOT_MCP_API_KEY` 或 `${COPILOT_MCP_API_KEY}`。引用的名称必须以 `COPILOT_MCP_` 开头。
  * 文字字符串值。

**远程MCP特定按键**
* `url` (`string`)：必填。 MCP 服务器的 URL。
* `headers` (`object`)：可选。附加到服务器请求的标头。该对象应将标头键的名称映射到以下之一：
  * 对 {% data variables.product.prodname_copilot_short %} 环境中的机密或变量的替换引用，例如 `$COPILOT_MCP_API_KEY` 或 `${COPILOT_MCP_API_KEY}`。引用的名称必须以 `COPILOT_MCP_` 开头。
  * 文字字符串值。

请注意，除 `tools` 和 `type` 之外的所有 `string` 和 `string[]` 字段都支持替换为您在 {% data variables.product.prodname_copilot_short %} 环境中配置的变量或密钥。

### 变量替换

支持以下语法模式来引用 {% data variables.product.prodname_copilot_short %} 环境中配置的环境变量：

|语法|示例|
| ----------- | ------------            |
| `$VAR`      | `$COPILOT_MCP_API_KEY`  |
| `${VAR}`    | `${COPILOT_MCP_API_KEY}`|
| `${VAR:-default}` | `${COPILOT_MCP_API_KEY:-fallback_value}` |

## 配置示例

以下示例显示了不同提供商的 MCP 服务器配置。
* [哨兵](<#示例哨兵>)
* [Notion](<#示例notion>)
* [天蓝色](<#示例天蓝色>)
* [Cloudflare](<#示例cloudflare>)
* [天蓝色 DevOps](<#示例azure-devops>)
* [阿特拉斯](<#示例atlassian>)

### 示例：哨兵

[Sentry MCP 服务器 ](<https://github.com/getsentry/sentry-mcp>) 为 {% data variables.product.prodname_copilot_short %} 提供对 [Sentry](<https://sentry.io>) 中记录的异常的经过身份验证的访问。

```javascript copy
// If you copy and paste this example, you will need to remove the comments prefixed with `//`, which are not valid JSON.
{
  "mcpServers": {
    "sentry": {
      "type": "local",
      "command": "npx",
      // We can use the $SENTRY_HOST environment variable which is passed to
      // the server because of the `env` value below.
      "args": ["@sentry/mcp-server@latest", "--host=$SENTRY_HOST"],
      "env": {
        // We can specify an environment variable value as a string...
        "SENTRY_HOST": "https://contoso.sentry.io",
        // or refer to a variable or secret in your {% data variables.product.prodname_copilot_short %} environment
        // with a name starting with `COPILOT_MCP_`
        "SENTRY_ACCESS_TOKEN": "$COPILOT_MCP_SENTRY_ACCESS_TOKEN"
      }
    }
  }
}
```

### 示例：Notion

[Notion MCP 服务器 ](<https://github.com/makenotion/notion-mcp-server>) 为 {% data variables.product.prodname_copilot_short %} 提供对 [Notion](<https://notion.so>) 中的笔记和其他内容的经过身份验证的访问。

```javascript copy
// If you copy and paste this example, you will need to remove the comments prefixed with `//`, which are not valid JSON.
{
  "mcpServers": {
    "notionApi": {
      "type": "local",
      "command": "docker",
      "args": [
        "run",
        "--rm",
        "-i",
        "-e",
        // We can use the $NOTION_API_KEY environment variable which is passed to
        // the server because of the `env` value below.
        "OPENAPI_MCP_HEADERS={\"Authorization\": \"Bearer $NOTION_API_KEY\", \"Notion-Version\": \"2022-06-28\"}",
       "mcp/notion"
      ],
      "env": {
        // The value of the `COPILOT_MCP_NOTION_API_KEY` secret will be passed to the
        // server command as an environment variable called `NOTION_API_KEY`
        "NOTION_API_KEY": "$COPILOT_MCP_NOTION_API_KEY"
      },
      "tools": ["*"]
    }
  }
}
```

### 示例：天蓝色

[Microsoft MCP 存储库 ](<https://github.com/microsoft/mcp>) 包括 Azure MCP 服务器，它允许 {% data variables.product.prodname_copilot_short %} 在进行代码更改时了解订阅中的 Azure 特定文件和 Azure 资源。

要使用 `copilot-setup-steps.yml` 文件自动配置存储库以通过 Azure 进行身份验证以及用于身份验证的机密，请在本地克隆存储库，然后在存储库的根目录中运行 [Azure 开发人员 CLI](<https://learn.microsoft.com/en-us/azure/developer/azure-developer-cli/?ref_product=copilot&ref_type=engagement&ref_style=button>) 的 `azd cloud-agent config` 命令。

运行命令并合并创建的拉取请求后，您可以将 MCP 配置添加到存储库。

  ```json copy
   {
     "mcpServers": {
       "Azure": {
        "type": "local",
        "command": "npx",
        "args": [
          "-y",
          "@azure/mcp@latest",
          "server",
          "start"
         ],
        "tools": ["*"]
       }
     }
   }
   ```

### 示例：Cloudflare

[Cloudflare MCP 服务器 ](<https://github.com/cloudflare/mcp-server-cloudflare>) 在您的 Cloudflare 服务之间创建连接，包括处理文档和数据分析。

```json copy
{
  "mcpServers": {
    "cloudflare": {
      "type": "sse",
      "url": "https://docs.mcp.cloudflare.com/sse",
      "tools": ["*"]
    }
  }
}
```

### 示例：Azure DevOps

[Azure DevOps MCP 服务器 ](<https://github.com/microsoft/azure-devops-mcp>) 在 {% data variables.product.prodname_copilot_short %} 和 Azure DevOps 服务之间创建无缝连接，包括工作项、管道或文档。

要将 Azure DevOps MCP 服务器与 {% data variables.copilot.copilot_cloud_agent %} 结合使用，必须更新存储库的 copilot-setup-steps.yml 文件以包含 Azure 登录工作流步骤。

1. 在 Microsoft Entra 应用程序中配置 OIDC，信任 {% data variables.product.github %}。请参阅 [通过 OpenID 连接 ](<https://learn.microsoft.com/en-us/azure/developer/github/connect-from-azure-openid-connect>) 使用 Azure 登录操作。
1. 设置应用程序标识对 Azure DevOps 组织和项目的访问权限。请参阅[添加组织用户并管理访问权限](<https://learn.microsoft.com/en-us/azure/devops/organizations/accounts/add-organization-users>)。
1. 如果您还没有 `.github/workflows/copilot-setup-steps.yml` Actions 工作流程文件，请在您的存储库中添加该文件。
1. 将 Azure 登录步骤添加到 `copilot-setup-steps` 工作流作业。

   ```yaml copy
   {% data reusables.actions.actions-not-certified-by-github-comment %}
   on:
     workflow_dispatch:
   permissions:
     id-token: write
     contents: read
   jobs:
     copilot-setup-steps:
       runs-on: ubuntu-latest
       permissions:
         id-token: write
         contents: read
       environment: copilot
       steps:
         - name: Azure login
           uses: azure/login@a457da9ea143d694b1b9c7c869ebb04ebe844ef5
           with:
             client-id: {% raw %}${{ secrets.AZURE_CLIENT_ID }}{% endraw %}
             tenant-id: {% raw %}${{ secrets.AZURE_TENANT_ID }}{% endraw %}
             allow-no-subscriptions: true
   ```

此配置可确保 {% data variables.copilot.copilot_cloud_agent %} 运行时执行 `azure/login` 操作。
1. 在组织或存储库级别将 `AZURE_CLIENT_ID` 和 `AZURE_TENANT_ID` 的机密配置为 Agents 机密。欲了解更多信息，请参阅[AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/configure-secrets-and-variables>)。
1. 通过使用您希望 {% data variables.product.prodname_copilot_short %} 使用的工具将 `ado` 对象添加到 MCP 配置中来配置 Azure DevOps MCP 服务器。

  ```json copy
  {
    "mcpServers": {
      "ado": {
        "type": "local",
        "command": "npx",
        "args": ["-y", "@azure-devops/mcp", "<your-azure-devops-organization>", "-a", "azcli"],
        "tools": ["wit_get_work_item", "wit_get_work_items_batch_by_ids", ...]
      }
    }
  }
   ```

### 示例：Atlassian

[Atlassian MCP 服务器 ](<https://github.com/atlassian/atlassian-mcp-server>) 提供 {% data variables.product.prodname_copilot_short %} 对 Atlassian 应用程序（包括 Jira、Compass 和 Confluence）的经过身份验证的访问。

有关使用 API 密钥向 Atlassian MCP 服务器进行身份验证的更多信息，请参阅 Atlassian 文档中的 [通过 API 令牌配置身份验证 ](<https://support.atlassian.com/atlassian-rovo-mcp-server/docs/configuring-authentication-via-api-token/>)。

```javascript copy
// If you copy and paste this example, you will need to remove the comments prefixed with `//`, which are not valid JSON.
{
  "mcpServers": {
    "atlassian-rovo-mcp": {
      "command": "npx",
      "type": "local",
      "tools": ["*"],
      "args": [
        "mcp-remote@latest",
        "https://mcp.atlassian.com/v1/mcp",
        // We can use the $ATLASSIAN_API_KEY environment variable which is passed
        // to the server because of the `env` value below.
        "--header",
        "Authorization: Basic $ATLASSIAN_API_KEY"
      ],
      "env": {
        // The value of the `COPILOT_MCP_ATLASSIAN_API_KEY` secret will be passed
        // to the server command as an environment variable
        // called `ATLASSIAN_API_KEY`.
        "ATLASSIAN_API_KEY": "$COPILOT_MCP_ATLASSIAN_API_KEY"
      }
    }
  }
}
```

## 重复使用 {% data variables.product.prodname_vscode %} 中的 MCP 配置

如果您已经在 {% data variables.product.prodname_vscode_shortname %} 中配置了 MCP 服务器，则可以在 {% data variables.product.github %} 上为存储库 MCP 设置使用类似的配置。

根据 {% data variables.product.prodname_vscode_shortname %} 的配置方式，您也许能够在存储库的 `.vscode/mcp.json` 文件或计算机的私有 `settings.json` 文件中找到 MCP 设置。

要调整 {% data variables.product.github %} 上存储库 MCP 设置的配置，您将需要：

1. 为每个 MCP 服务器添加一个 `tools` 密钥，指定 {% data variables.product.prodname_copilot_short %} 可以使用哪些工具。
1. 如果您已经配置了`inputs`，请直接切换为使用`env`。
1. 如果您配置了`envFile`，请直接切换为使用`env`。
1. 更新 `args` 配置中对 `inputs` 的任何引用，以引用 `env` 中的环境变量。

有关 {% data variables.product.prodname_vscode_shortname %} 中的 MCP 的更多信息，请参阅 [{% data variables.product.prodname_vscode_shortname %} 文档 ](<https://code.visualstudio.com/docs/copilot/chat/mcp-servers>)。

## 验证您的 MCP 配置

设置 MCP 配置后，您应该对其进行测试以确保其设置正确。

### 使用 {% data variables.copilot.copilot_cloud_agent %} 进行验证

1. 在存储库中创建一个问题，然后将其分配给 {% data variables.product.prodname_copilot_short %}。
1. 等待几秒钟，{% data variables.product.prodname_copilot_short %} 将会对该问题留下 👀 反应。
1. 再等几秒钟，{% data variables.product.prodname_copilot_short %} 将创建一个拉取请求，该请求将出现在问题的时间线中。
1. 单击时间线中创建的拉取请求，然后等待“Copilot开始工作”时间线事件出现。
1. 单击**查看会话**以打开{% data variables.copilot.copilot_cloud_agent %}日志。
1. 单击日志查看器右上角的省略号按钮 (**...**)，然后单击边栏中的 **{% data variables.product.prodname_copilot_short %}**。
1. 单击 **启动 MCP 服务器 ** 步骤以展开日志。
1. 如果您的 MCP 服务器已成功启动，您将看到日志底部列出的工具。

如果您的 MCP 服务器需要默认情况下未安装在 {% data variables.product.prodname_actions %} 运行器上的任何依赖项（例如 `uv` 和 `pipx`），或者需要特殊的设置步骤，则您可能需要创建 `copilot-setup-steps.yml` Actions 工作流程文件来安装它们。欲了解更多信息，请参阅[AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment>)。

### 使用 {% data variables.copilot.copilot_code-review_short %} 进行验证

1. 在存储库中打开拉取请求。
1. 请求 {% data variables.product.prodname_copilot_short %} 进行审核。
1. 单击 ** 查看会话 **，从拉取请求时间线打开链接的审阅会话。在会话日志中，查看“设置环境”部分以查看启动和调用了哪些 MCP 服务器和工具。

## 自定义内置{% data variables.product.github %} MCP服务器

{% data variables.product.github %} MCP 服务器默认启用，并使用特殊范围的令牌连接到 {% data variables.product.github %}，该令牌仅对当前存储库具有只读访问权限。

如果您想允许{% data variables.product.prodname_copilot_short %}访问当前存储库之外的数据，您可以配置具有更广泛访问权限的{% data variables.product.pat_generic %}或{% data variables.product.prodname_github_app %}。

{% data reusables.repositories.navigate-to-repo %}
{% data reusables.repositories.sidebar-settings %}
1. {% data reusables.user-settings.code-planning-automation %} 单击 **{% data variables.product.prodname_copilot_short %}**，然后单击 **MCP 服务器 **。
1. 在“MCP配置”部分添加您的配置。

对于{% data variables.product.pat_generic %}，您可以添加以下示例：

   ```javascript copy
    // If you copy and paste this example, you will need to remove the comments prefixed with `//`, which are not valid JSON.
    {
      "mcpServers": {
        "github-mcp-server": {
          "type": "http",
          // Remove "/readonly" to enable wider access to all tools.
          // Then, use the "X-MCP-Toolsets" header to specify which toolsets you'd like to include.
          // Use the "tools" field to select individual tools from the toolsets.
          "url": "https://api.githubcopilot.com/mcp/readonly",
          "tools": ["*"],
          "headers": {
            "X-MCP-Toolsets": "repos,issues,users,pull_requests,code_security,secret_protection,actions,web_search"
          }
        }
      }
    }
   ```

对于{% data variables.product.prodname_github_app %}，您可以添加以下示例：

   ```javascript copy
    {
      "mcpServers": {
        "github-wider-access": {
          "type": "stdio",
          "command": "docker",
          "args": [
            "run", "--rm", "-i",
            "-e", "GITHUB_APP_ID",
            "-e", "GITHUB_APP_INSTALLATION_ID",
            "-e", "GITHUB_APP_PRIVATE_KEY",
            "-e", "GITHUB_READ_ONLY",
            "-e", "GITHUB_TOOLSETS",
            "ghcr.io/github/github-mcp-server"
          ],
          "env": {
            "GITHUB_APP_ID": "$COPILOT_MCP_GITHUB_APP_ID",
            "GITHUB_APP_INSTALLATION_ID": "$COPILOT_MCP_GITHUB_APP_INSTALLATION_ID",
            "GITHUB_APP_PRIVATE_KEY": "$COPILOT_MCP_GITHUB_APP_PRIVATE_KEY",
            "GITHUB_READ_ONLY": "true",
            "GITHUB_TOOLSETS": "repos,pull_requests"
          },
          "tools": ["*"]
        }
      }
    }
   ```

有关带有{% data variables.product.prodname_github_app %}的{% data variables.product.github %} MCP服务器的更多信息，请参阅[GitHub应用程序身份验证](<https://github.com/github/github-mcp-server/blob/main/docs/github-app-auth.md>)。

有关工具集的更多信息，请参阅 {% data variables.product.github %} 远程 MCP 服务器文档中的 [README](<https://github.com/github/github-mcp-server?tab=readme-ov-file#available-toolsets>)。

1. 单击**保存MCP配置**。
1. 添加您的 Agents 秘密和变量。您可以在组织或存储库级别配置这些。欲了解更多信息，请参阅[AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/configure-secrets-and-variables>)。

对于 {% data variables.product.pat_generic %}，添加一个名为 `COPILOT_MCP_GITHUB_PERSONAL_ACCESS_TOKEN` 的机密，并将您的 {% data variables.product.pat_generic %} 作为值。我们建议使用 {% data variables.product.pat_v2 %}，以便您可以限制令牌对特定存储库的只读权限。有关 {% data variables.product.pat_generic_plural %} 的更多信息，请参阅 [AUTOTITLE](<https://raw.githubusercontent.com/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens>)。

   对于 {% data variables.product.prodname_github_app %}，你的 Agents 秘密和变量必须以前缀 `COPILOT_MCP_`。例如，添加一个名为 `COPILOT_MCP_GITHUB_APP_ID` 与你的 {% data variables.product.prodname_github_app %} ID 作为值，一个名为 `COPILOT_MCP_GITHUB_APP_INSTALLATION_ID` 与你的 {% data variables.product.prodname_github_app %} 安装 ID 作为值，还有一个秘密称为 `COPILOT_MCP_GITHUB_APP_PRIVATE_KEY` 与你的 {% data variables.product.prodname_github_app %} 私钥作为值。

1. 如果您在使用 {% data variables.product.github %} MCP 服务器时看到 `api.github.com` 的防火墙警告，请将 `api.github.com` 添加到自定义许可名单中。欲了解更多信息，请参阅[AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-the-firewall>)。

有关在其他环境中使用 {% data variables.product.github %} MCP 服务器的信息，请参阅 [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-in-your-ide/copilot-for-common-tasks/use-the-github-mcp-server>)。

## 禁用 MCP 工具进行代码审查

在存储库设置中，默认情况下启用{% data variables.copilot.copilot_code-review_short %}使用MCP工具。

如果您希望配置的 MCP 服务器仅可用于 {% data variables.copilot.copilot_cloud_agent %}，而不可用于 {% data variables.copilot.copilot_code-review_short %}，请禁用此设置。

如果禁用此设置，{% data variables.copilot.copilot_code-review_short %} 将不会调用 MCP 工具来审查该存储库中的拉取请求，包括来自默认 {% data variables.product.github %} 和 Playwright MCP 服务器的工具。

{% data reusables.repositories.navigate-to-repo %}
{% data reusables.repositories.sidebar-settings %}
1. {% data reusables.user-settings.code-planning-automation %} 单击 **{% data variables.product.prodname_copilot_short %}**，然后单击 ** 代码审查 **。
1. 单击 ** 允许 Copilot 在审查拉取请求时使用 MCP 工具 ** 切换以禁用该设置。

## 后续步骤

* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-cli/customize-copilot/add-mcp-servers>)
* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/use-copilot-agents/request-a-code-review/use-code-review>)
* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/create-custom-agents>)
* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment>)
* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-in-your-ide/customize-copilot/extend-copilot-with-tools-and-context/extend-copilot-chat-with-mcp>)


## 原文对照

<details>
<summary>展开完整原文</summary>

## Prerequisite

Before setting up MCP servers for your repository, read [AUTOTITLE](<https://raw.githubusercontent.com/copilot/concepts/context/mcp>) and [AUTOTITLE](<https://raw.githubusercontent.com/copilot/concepts/agents/cloud-agent/mcp-and-cloud-agent>).

## Introduction

As a repository administrator, you can configure MCP servers for use within your repository. You do this using a JSON-formatted configuration that specifies the details of the MCP servers you want to use. You enter the JSON configuration directly into the settings for the repository on {% data variables.product.prodname_dotcom_the_website %}.

This repository-level MCP configuration is shared by {% data variables.copilot.copilot_cloud_agent %} and {% data variables.copilot.copilot_code-review_short %}. For information on disabling use of MCP servers by {% data variables.copilot.copilot_code-review_short %}, see [Disabling MCP tools for {% data variables.copilot.copilot_code-review_short %}](<#disabling-mcp-tools-for-code-review>).

> [!NOTE]
> Existing repository MCP configurations that were previously managed under {% data variables.copilot.copilot_cloud_agent %} settings were automatically moved to the new shared MCP settings page. No migration action is required.

The {% data variables.product.github %} MCP server and Playwright MCP server are enabled by default. You can add your own MCP servers alongside these defaults.

Organization and enterprise administrators can also configure MCP servers as part of {% data variables.copilot.custom_agents_short %} using the YAML frontmatter. For more information, see [AUTOTITLE](<https://raw.githubusercontent.com/copilot/reference/custom-agents-configuration#mcp-server-configuration-details>).

> [!WARNING]
> Once you've configured an MCP server, {% data variables.product.prodname_copilot_short %} will be able to use the tools provided by the server autonomously, and will not ask for your approval before using them.

{% data reusables.copilot.mcp.repo-mcp-limitations %}

## Adding an MCP configuration to your repository

Repository administrators can configure MCP servers by following these steps:

{% data reusables.repositories.navigate-to-repo %}
{% data reusables.repositories.sidebar-settings %}
1. {% data reusables.user-settings.code-planning-automation %} click **{% data variables.product.prodname_copilot_short %}** then **MCP servers**.
1. On the "Model Context Protocol (MCP)" page, add your configuration in the "MCP configuration" section.

   The following sections in this article explain how to write the JSON configuration that you need to enter here.

1. Click **Save MCP configuration**.

   Your configuration will be validated to ensure proper syntax.

1. If your MCP server requires a variable, key, or secret, add an Agents secret or variable with a name prefixed with `COPILOT_MCP_`. Only Agents secrets and variables with names prefixed with `COPILOT_MCP_` will be available to your MCP configuration. See [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/configure-secrets-and-variables>).

## Writing a JSON configuration for MCP servers

You configure MCP servers using a special JSON format. The JSON must contain an `mcpServers` object, where the key is the name of the MCP server (for example, `sentry`), and the value is an object with the configuration for that MCP server.

```json copy
{
  "mcpServers": {
    "MCP SERVER 1": {
      "command": "VALUE",
      "args": [ VALUES ],
      ...
    },
    "MCP SERVER 2": {
      "command": "VALUE",
      "args": [ VALUES ],
      ...
    },
    ...
  }
}
```

The configuration object can contain the following keys:

**Required keys for local and remote MCP servers**
* `tools` (`string[]`): The tools from the MCP server to enable. You may be able to find a list of tools in the server's documentation, or in its code. We strongly recommend that you allowlist specific read-only tools, since the agent will be able to use these tools autonomously and will not ask you for approval first. You can also enable all tools by including `*` in the array.
* `type` (`string`): {% data variables.copilot.copilot_cloud_agent %} accepts `"local"`, `"stdio"`, `"http"`, or `"sse"`.

> [!NOTE]
> For {% data variables.copilot.copilot_code-review_short %}, each tool in the MCP server's `tools/list` response must set `annotations.readOnlyHint` to `true` to allow {% data variables.product.prodname_copilot_short %} to use it. {% data variables.copilot.copilot_code-review_short %} excludes tools when this annotation is missing or set to `false`. For more information, see [Tool annotations in the MCP specification](<https://modelcontextprotocol.io/specification/2025-11-25/schema#toolannotations-readonlyhint>).

**Local MCP specific keys**
* `command` (`string`): Required. The command to run to start the MCP server.
* `args` (`string[]`): Required. The arguments to pass to the `command`.
* `env` (`object`): Optional. The environment variables to pass to the server. This object should map the name of the environment variable that should be exposed to your MCP server to one of the following:
  * A substitution reference to a secret or variable in your {% data variables.product.prodname_copilot_short %} environment, such as `$COPILOT_MCP_API_KEY` or `${COPILOT_MCP_API_KEY}`. Referenced names must start with `COPILOT_MCP_`.
  * A literal string value.

**Remote MCP specific keys**
* `url` (`string`): Required. The MCP server's URL.
* `headers` (`object`): Optional. The headers to attach to requests to the server. This object should map the name of header keys to one of the following:
  * A substitution reference to a secret or variable in your {% data variables.product.prodname_copilot_short %} environment, such as `$COPILOT_MCP_API_KEY` or `${COPILOT_MCP_API_KEY}`. Referenced names must start with `COPILOT_MCP_`.
  * A literal string value.

Note that all `string` and `string[]` fields besides `tools` and `type` support substitution with a variable or secret you have configured in your {% data variables.product.prodname_copilot_short %} environment.

### Variable substitution

The following syntax patterns are supported for referencing environment variables configured in your {% data variables.product.prodname_copilot_short %} environment:

| Syntax      | Example                 |
| ----------- | ------------            |
| `$VAR`      | `$COPILOT_MCP_API_KEY`  |
| `${VAR}`    | `${COPILOT_MCP_API_KEY}`|
| `${VAR:-default}` | `${COPILOT_MCP_API_KEY:-fallback_value}` |

## Example configurations

The examples below show MCP server configurations for different providers.
* [Sentry](<#example-sentry>)
* [Notion](<#example-notion>)
* [Azure](<#example-azure>)
* [Cloudflare](<#example-cloudflare>)
* [Azure DevOps](<#example-azure-devops>)
* [Atlassian](<#example-atlassian>)

### Example: Sentry

The [Sentry MCP server](<https://github.com/getsentry/sentry-mcp>) gives {% data variables.product.prodname_copilot_short %} authenticated access to exceptions recorded in [Sentry](<https://sentry.io>).

```javascript copy
// If you copy and paste this example, you will need to remove the comments prefixed with `//`, which are not valid JSON.
{
  "mcpServers": {
    "sentry": {
      "type": "local",
      "command": "npx",
      // We can use the $SENTRY_HOST environment variable which is passed to
      // the server because of the `env` value below.
      "args": ["@sentry/mcp-server@latest", "--host=$SENTRY_HOST"],
      "env": {
        // We can specify an environment variable value as a string...
        "SENTRY_HOST": "https://contoso.sentry.io",
        // or refer to a variable or secret in your {% data variables.product.prodname_copilot_short %} environment
        // with a name starting with `COPILOT_MCP_`
        "SENTRY_ACCESS_TOKEN": "$COPILOT_MCP_SENTRY_ACCESS_TOKEN"
      }
    }
  }
}
```

### Example: Notion

The [Notion MCP server](<https://github.com/makenotion/notion-mcp-server>) gives {% data variables.product.prodname_copilot_short %} authenticated access to notes and other content from [Notion](<https://notion.so>).

```javascript copy
// If you copy and paste this example, you will need to remove the comments prefixed with `//`, which are not valid JSON.
{
  "mcpServers": {
    "notionApi": {
      "type": "local",
      "command": "docker",
      "args": [
        "run",
        "--rm",
        "-i",
        "-e",
        // We can use the $NOTION_API_KEY environment variable which is passed to
        // the server because of the `env` value below.
        "OPENAPI_MCP_HEADERS={\"Authorization\": \"Bearer $NOTION_API_KEY\", \"Notion-Version\": \"2022-06-28\"}",
       "mcp/notion"
      ],
      "env": {
        // The value of the `COPILOT_MCP_NOTION_API_KEY` secret will be passed to the
        // server command as an environment variable called `NOTION_API_KEY`
        "NOTION_API_KEY": "$COPILOT_MCP_NOTION_API_KEY"
      },
      "tools": ["*"]
    }
  }
}
```

### Example: Azure

The [Microsoft MCP repository](<https://github.com/microsoft/mcp>) includes the Azure MCP server, which allows {% data variables.product.prodname_copilot_short %} to understand your Azure-specific files and Azure resources within your subscription when making code changes.

To automatically configure your repository with a `copilot-setup-steps.yml` file to authenticate with Azure, plus secrets for authentication, clone the repository locally then run the [Azure Developer CLI](<https://learn.microsoft.com/en-us/azure/developer/azure-developer-cli/?ref_product=copilot&ref_type=engagement&ref_style=button>)'s `azd cloud-agent config` command in the root of the repository.

Once you've run the command and merged the created pull request, you can add the MCP configuration to your repository.

  ```json copy
   {
     "mcpServers": {
       "Azure": {
        "type": "local",
        "command": "npx",
        "args": [
          "-y",
          "@azure/mcp@latest",
          "server",
          "start"
         ],
        "tools": ["*"]
       }
     }
   }
   ```

### Example: Cloudflare

The [Cloudflare MCP server](<https://github.com/cloudflare/mcp-server-cloudflare>) creates connections between your Cloudflare services, including processing documentation and data analysis.

```json copy
{
  "mcpServers": {
    "cloudflare": {
      "type": "sse",
      "url": "https://docs.mcp.cloudflare.com/sse",
      "tools": ["*"]
    }
  }
}
```

### Example: Azure DevOps

The [Azure DevOps MCP server](<https://github.com/microsoft/azure-devops-mcp>) creates a seamless connection between {% data variables.product.prodname_copilot_short %} and your Azure DevOps services, including work items, pipelines or documentation.

To use the Azure DevOps MCP server with {% data variables.copilot.copilot_cloud_agent %}, you must update the repository's copilot-setup-steps.yml file to include an Azure login workflow step.

1. Configure OIDC in a Microsoft Entra application, trusting {% data variables.product.github %}. See [Use the Azure Login action with OpenID Connect](<https://learn.microsoft.com/en-us/azure/developer/github/connect-from-azure-openid-connect>).
1. Setup access to Azure DevOps organization and projects for the application identity. See [Add organization users and manage access](<https://learn.microsoft.com/en-us/azure/devops/organizations/accounts/add-organization-users>).
1. Add a `.github/workflows/copilot-setup-steps.yml` Actions workflow file in your repository if you do not already have one.
1. Add an Azure login step to the `copilot-setup-steps` workflow job.

   ```yaml copy
   {% data reusables.actions.actions-not-certified-by-github-comment %}
   on:
     workflow_dispatch:
   permissions:
     id-token: write
     contents: read
   jobs:
     copilot-setup-steps:
       runs-on: ubuntu-latest
       permissions:
         id-token: write
         contents: read
       environment: copilot
       steps:
         - name: Azure login
           uses: azure/login@a457da9ea143d694b1b9c7c869ebb04ebe844ef5
           with:
             client-id: {% raw %}${{ secrets.AZURE_CLIENT_ID }}{% endraw %}
             tenant-id: {% raw %}${{ secrets.AZURE_TENANT_ID }}{% endraw %}
             allow-no-subscriptions: true
   ```

   This configuration ensures the `azure/login` action is executed when {% data variables.copilot.copilot_cloud_agent %} runs.
1. Configure secrets for your `AZURE_CLIENT_ID` and `AZURE_TENANT_ID` as Agents secrets at either the organization or repository level. For more information, see [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/configure-secrets-and-variables>).
1. Configure the Azure DevOps MCP server by adding an `ado` object to your MCP configuration with the tools you want {% data variables.product.prodname_copilot_short %} to use.

  ```json copy
  {
    "mcpServers": {
      "ado": {
        "type": "local",
        "command": "npx",
        "args": ["-y", "@azure-devops/mcp", "<your-azure-devops-organization>", "-a", "azcli"],
        "tools": ["wit_get_work_item", "wit_get_work_items_batch_by_ids", ...]
      }
    }
  }
   ```

### Example: Atlassian

The [Atlassian MCP server](<https://github.com/atlassian/atlassian-mcp-server>) gives {% data variables.product.prodname_copilot_short %} authenticated access to your Atlassian apps, including Jira, Compass, and Confluence.

For more information about authenticating to the Atlassian MCP server using an API key, see [Configuring authentication via API token](<https://support.atlassian.com/atlassian-rovo-mcp-server/docs/configuring-authentication-via-api-token/>) in the Atlassian documentation.

```javascript copy
// If you copy and paste this example, you will need to remove the comments prefixed with `//`, which are not valid JSON.
{
  "mcpServers": {
    "atlassian-rovo-mcp": {
      "command": "npx",
      "type": "local",
      "tools": ["*"],
      "args": [
        "mcp-remote@latest",
        "https://mcp.atlassian.com/v1/mcp",
        // We can use the $ATLASSIAN_API_KEY environment variable which is passed
        // to the server because of the `env` value below.
        "--header",
        "Authorization: Basic $ATLASSIAN_API_KEY"
      ],
      "env": {
        // The value of the `COPILOT_MCP_ATLASSIAN_API_KEY` secret will be passed
        // to the server command as an environment variable
        // called `ATLASSIAN_API_KEY`.
        "ATLASSIAN_API_KEY": "$COPILOT_MCP_ATLASSIAN_API_KEY"
      }
    }
  }
}
```

## Reusing your MCP configuration from {% data variables.product.prodname_vscode %}

If you have already configured MCP servers in {% data variables.product.prodname_vscode_shortname %}, you can leverage a similar configuration for your repository MCP settings on {% data variables.product.github %}.

Depending on how {% data variables.product.prodname_vscode_shortname %} is configured, you may be able to find your MCP settings in your repository's `.vscode/mcp.json` file, or in your machine's private `settings.json` file.

To adapt the configuration for repository MCP settings on {% data variables.product.github %}, you will need to:

1. Add a `tools` key for each MCP server, specifying which tools will be available to {% data variables.product.prodname_copilot_short %}.
1. If you've configured `inputs`, switch to using `env` directly.
1. If you've configured an `envFile`, switch to using `env` directly.
1. Update any references to `inputs` in your `args` configuration to refer to environment variables from `env` instead.

For more information on MCP in {% data variables.product.prodname_vscode_shortname %}, see the [{% data variables.product.prodname_vscode_shortname %} docs](<https://code.visualstudio.com/docs/copilot/chat/mcp-servers>).

## Validating your MCP configuration

Once you've set up your MCP configuration, you should test it to make sure it is set up correctly.

### Validate with {% data variables.copilot.copilot_cloud_agent %}

1. Create an issue in the repository, then assign it to {% data variables.product.prodname_copilot_short %}.
1. Wait a few seconds, and {% data variables.product.prodname_copilot_short %} will leave an 👀 reaction on the issue.
1. Wait a few more seconds, and {% data variables.product.prodname_copilot_short %} will create a pull request, which will appear in the issue's timeline.
1. Click the created pull request in the timeline, and wait until a "Copilot started work" timeline event appears.
1. Click **View session** to open the {% data variables.copilot.copilot_cloud_agent %} logs.
1. Click the ellipsis button (**...**) at the top right of the log viewer, then click **{% data variables.product.prodname_copilot_short %}** in the sidebar.
1. Click the **Start MCP Servers** step to expand the logs.
1. If your MCP servers have been started successfully, you will see their tools listed at the bottom of the logs.

If your MCP servers require any dependencies that are not installed on the {% data variables.product.prodname_actions %} runner by default, such as `uv` and `pipx`, or that need special setup steps, you may need to create a `copilot-setup-steps.yml` Actions workflow file to install them. For more information, see [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment>).

### Validate with {% data variables.copilot.copilot_code-review_short %}

1. Open a pull request in the repository.
1. Request a review from {% data variables.product.prodname_copilot_short %}.
1. Open the linked review session from the pull request timeline by clicking **View session**. In the session logs, review the "Setting up environment" section to see which MCP servers and tools were started and called.

## Customizing the built-in {% data variables.product.github %} MCP server

The {% data variables.product.github %} MCP server is enabled by default and connects to {% data variables.product.github %} with a specially scoped token that only has read-only access to the current repository.

If you want to allow {% data variables.product.prodname_copilot_short %} to access data outside the current repository, you can configure a {% data variables.product.pat_generic %} with wider access or a {% data variables.product.prodname_github_app %}.

{% data reusables.repositories.navigate-to-repo %}
{% data reusables.repositories.sidebar-settings %}
1. {% data reusables.user-settings.code-planning-automation %} click **{% data variables.product.prodname_copilot_short %}** then **MCP servers**.
1. Add your configuration in the "MCP configuration" section. 

   For {% data variables.product.pat_generic %}, you can add the following example:

   ```javascript copy
    // If you copy and paste this example, you will need to remove the comments prefixed with `//`, which are not valid JSON.
    {
      "mcpServers": {
        "github-mcp-server": {
          "type": "http",
          // Remove "/readonly" to enable wider access to all tools.
          // Then, use the "X-MCP-Toolsets" header to specify which toolsets you'd like to include.
          // Use the "tools" field to select individual tools from the toolsets.
          "url": "https://api.githubcopilot.com/mcp/readonly",
          "tools": ["*"],
          "headers": {
            "X-MCP-Toolsets": "repos,issues,users,pull_requests,code_security,secret_protection,actions,web_search"
          }
        }
      }
    }
   ```

   For {% data variables.product.prodname_github_app %}, you can add the following example:

   ```javascript copy
    {
      "mcpServers": {
        "github-wider-access": {
          "type": "stdio",
          "command": "docker",
          "args": [
            "run", "--rm", "-i",
            "-e", "GITHUB_APP_ID",
            "-e", "GITHUB_APP_INSTALLATION_ID",
            "-e", "GITHUB_APP_PRIVATE_KEY",
            "-e", "GITHUB_READ_ONLY",
            "-e", "GITHUB_TOOLSETS",
            "ghcr.io/github/github-mcp-server"
          ],
          "env": {
            "GITHUB_APP_ID": "$COPILOT_MCP_GITHUB_APP_ID",
            "GITHUB_APP_INSTALLATION_ID": "$COPILOT_MCP_GITHUB_APP_INSTALLATION_ID",
            "GITHUB_APP_PRIVATE_KEY": "$COPILOT_MCP_GITHUB_APP_PRIVATE_KEY",
            "GITHUB_READ_ONLY": "true",
            "GITHUB_TOOLSETS": "repos,pull_requests"
          },
          "tools": ["*"]
        }
      }
    }
   ```

   For more information on the {% data variables.product.github %} MCP server with {% data variables.product.prodname_github_app %}, refer to [GitHub App authentication](<https://github.com/github/github-mcp-server/blob/main/docs/github-app-auth.md>).

   For more information on toolsets, refer to the [README](<https://github.com/github/github-mcp-server?tab=readme-ov-file#available-toolsets>) in the {% data variables.product.github %} Remote MCP Server documentation.

1. Click **Save MCP configuration**.
1. Add your Agents secrets and variables. You can configure these at either the organization or repository level. For more information, see [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/configure-secrets-and-variables>).

   For {% data variables.product.pat_generic %}, add a secret called `COPILOT_MCP_GITHUB_PERSONAL_ACCESS_TOKEN` with your {% data variables.product.pat_generic %} as the value. We recommend using a {% data variables.product.pat_v2 %} so you can limit the token's access to read-only permissions on specific repositories. For more information on {% data variables.product.pat_generic_plural %}, see [AUTOTITLE](<https://raw.githubusercontent.com/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens>).

   For {% data variables.product.prodname_github_app %}, your Agents secrets and variables must be prefixed with `COPILOT_MCP_`. For example, add a variable called `COPILOT_MCP_GITHUB_APP_ID` with your {% data variables.product.prodname_github_app %} ID as the value, a variable called `COPILOT_MCP_GITHUB_APP_INSTALLATION_ID` with your {% data variables.product.prodname_github_app %} installation ID as the value, and a secret called `COPILOT_MCP_GITHUB_APP_PRIVATE_KEY` with your {% data variables.product.prodname_github_app %} private key as the value.

1. If you see a firewall warning for `api.github.com` when using the {% data variables.product.github %} MCP server, add `api.github.com` to the custom allowlist. For more information, see [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-the-firewall>).

For information on using the {% data variables.product.github %} MCP server in other environments, see [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-in-your-ide/copilot-for-common-tasks/use-the-github-mcp-server>).

## Disabling MCP tools for code review

In repository settings, use of MCP tools by {% data variables.copilot.copilot_code-review_short %} is enabled by default.

Disable this setting if you want the configured MCP servers available only for {% data variables.copilot.copilot_cloud_agent %}, and not for {% data variables.copilot.copilot_code-review_short %}.

If you disable this setting, {% data variables.copilot.copilot_code-review_short %} won't call MCP tools for pull request reviews in that repository, including tools from the default {% data variables.product.github %} and Playwright MCP servers.

{% data reusables.repositories.navigate-to-repo %}
{% data reusables.repositories.sidebar-settings %}
1. {% data reusables.user-settings.code-planning-automation %} click **{% data variables.product.prodname_copilot_short %}** then **Code review**.
1. Click the **Allow Copilot to use MCP tools when reviewing pull requests** toggle to disable the setting.

## Next steps

* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-cli/customize-copilot/add-mcp-servers>)
* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/use-copilot-agents/request-a-code-review/use-code-review>)
* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/create-custom-agents>)
* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment>)
* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-in-your-ide/customize-copilot/extend-copilot-with-tools-and-context/extend-copilot-chat-with-mcp>)


</details>
