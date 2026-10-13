# Copilot 插件概览

介绍插件如何打包 Agent、Skills、Hooks 和集成，比较插件格式、目录结构和分发市场，用于复用与共享定制能力。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-09-10](<https://github.com/github/docs/commit/593c0f547c94823862ebc2c46fc55fc22d1e438c>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/concepts/agents/about-plugins.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/Copilot 插件概览（存档be38ec5d-471）.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: About {% data variables.product.prodname_copilot %} plugins
shortTitle: Plugins
allowTitleToDifferFromFilename: true
intro: 'Plugins are installable packages that extend {% data variables.product.prodname_copilot_short %} with reusable agents, skills, hooks, and integrations.'
product: '{% data reusables.gated-features.copilot-cli %}<br><br>{% data reusables.gated-features.copilot-cloud-agent %}<br><br>{% data reusables.gated-features.github-app %}<br><a href="https://github.com/features/copilot/plans?ref_product=copilot&ref_type=purchase&ref_style=button" target="_blank" class="btn btn-primary mt-3 mr-3 no-underline"><span>Sign up for {% data variables.product.prodname_copilot_short %}</span> {% octicon "link-external" height:16 %}</a>'
versions:
  feature: copilot
contentType: concepts
category:
  - Learn about Copilot # Copilot discovery page
  - Learn about Copilot CLI # Copilot CLI bespoke page
docsTeamMetrics:
  - copilot-cli
redirect_from:
  - /copilot/concepts/agents/copilot-cli/about-cli-plugins
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

插件提供了一种分发自定义 {% data variables.product.prodname_copilot_short %} 功能的方法。您可以使用插件向 {% data variables.product.prodname_copilot_short %} 添加一组预配置的功能，包括 {% data variables.copilot.copilot_cli_short %}、{% data variables.copilot.copilot_cloud_agent %} 和 {% data variables.copilot.github_copilot_app %}。

## 什么是插件？

* 扩展 {% data variables.product.prodname_copilot_short %} 功能的可分发包。
* 单个可安装单元中的一组组件。

## 插件包含哪些内容

插件可以包含以下部分或全部组件。这些组件的位置取决于插件格式：

* **自定义 agents** — 专业 AI 助手（`agents/` 中的 `*.agent.md` 文件）
* **Skills** - 离散可调用功能（`skills/` 中的技能子目录，包含 `SKILL.md` 文件）
* **Hooks** - 拦截 agent 行为的事件处理程序（插件根目录中的 `hooks.json` 文件，或 `hooks/` 中）
* **MCP 服务器配置** — Model Context Protocol 集成
* **LSP 服务器配置** - 语言服务器协议集成（插件根目录中的 `lsp.json` 文件或 `.github/` 中）

## 插件格式

{% data variables.product.prodname_copilot_short %}支持两种插件格式：

* **Agent 插件 1.0** 是一种可移植格式，用于跨兼容客户端共享技能和 MCP 服务器配置。要使用此格式，请将 `plugin.json` 中的 `$schema` 设置为 `https://agent-plugins.org/schemas/1.0.0/plugin.schema.json`。技能是从`skills/`中发现的，MCP服务器配置是从插件根目录的`mcp.json`中发现的。无法在清单中配置这些位置。当您想让技能和MCP服务器可移植时，请选择Agent插件1.0。
* **旧版 {% data variables.product.prodname_copilot_short %} 插件 ** 未声明 Agent 插件 `$schema`。它们使用现有的清单字段和组件发现行为，包括 `.mcp.json`、`.github/mcp.json` 或 `mcpServers` 清单字段中的可配置组件路径和 MCP 配置。当您需要可配置的组件路径或维护现有的 {% data variables.product.prodname_copilot_short %} 特定插件时，请选择旧格式。

对于新插件，请使用 Agent 插件 1.0 除非您需要可配置的组件路径。主要将旧格式用于现有的旧插件。

两种格式均受支持。添加 `$schema` 会更改 {% data variables.product.prodname_copilot_short %} 解释清单和发现组件的方式。没有 `$schema` 的插件继续作为旧插件加载。

## 插件的结构如何

插件是一个具有特定结构的目录和一个 `plugin.json` 清单文件。 Agent 插件 1.0 需要插件根目录下的清单。旧版插件支持其他清单位置。清单为插件提供了名称和元数据。根据格式，它还可以指向组件。除了清单之外，该目录还可以包含 agents、技能、挂钩、MCP 服务器配置和 LSP 服务器配置。

### Agent 插件 1.0 结构

Agent 插件 1.0 将技能和 MCP 服务器存储在标准位置，以便兼容的客户端可以发现它们。其他组件，包括 agents、挂钩、命令和 LSP 服务器，是特定于客户端的。 {% data variables.product.prodname_copilot_short %} 从 `com.github.copilot` 目录中读取这些组件。其他客户端会忽略此目录，因此同一插件可以将共享技能和 MCP 服务器与 {% data variables.product.prodname_copilot_short %} 特定组件结合起来。

Agent Plugins 1.0 目录可能如下所示：

```text
my-plugin/
├── plugin.json               # Required manifest
├── skills/                   # Skills (optional)
│   └── deploy/
│       └── SKILL.md
├── mcp.json                  # MCP server config (optional)
└── com.github.copilot/       # Copilot components (optional)
    ├── agents/
    │   └── helper.agent.md
    ├── commands/
    ├── rules/
    ├── hooks/
    │   └── hooks.json
    └── lsp.json
```

清单必须包含 Agent 插件 1.0 `$schema`。有关支持的顶级清单字段、名称要求和组件位置，请参阅 [Agent 插件 1.0 清单字段 ](<https://github.com/copilot/reference/copilot-cli-reference/cli-plugin-reference#agent-plugins-10-manifest-fields>)。

### 遗留插件结构

旧版 {% data variables.product.prodname_copilot_short %} 插件目录可能如下所示：

```text
my-plugin/
├── plugin.json           # Required manifest
├── agents/               # Custom agents (optional)
│   └── helper.agent.md
├── skills/               # Skills (optional)
│   └── deploy/
│       └── SKILL.md
├── hooks.json            # Hook configuration (optional)
├── .mcp.json             # MCP server config (optional)
└── lsp.json              # LSP server config (optional)
```

有关可以包含在清单中的完整字段集，请参阅 [AUTOTITLE](<https://github.com/copilot/reference/copilot-cli-reference/cli-plugin-reference>)。有关编写插件的分步指南，请参阅 [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/customize-copilot/plugins-creating>)。

## 为什么要使用插件？

插件具有以下优点：

* 跨项目的可重用性
* 团队标准化【{% data variables.product.prodname_copilot_short %}】配置
* 分享领域专业知识（例如，通过提供 Rails 专家或 Kubernetes 专家的技能）
* 封装复杂的 MCP 服务器设置

## 我在哪里可以获得插件？

您可以从以下位置安装插件：

* 一个市场
* 一个存储库
* 本地路径

市场是开发人员可以发布、发现、安装和管理插件的位置。它有点像应用程序商店，但针对的是插件。

市场的示例包括：

* [copilot-插件](<https://github.com/github/copilot-plugins>)（默认添加）
* [awesome-copilot](<https://github.com/github/awesome-copilot>)（默认添加）
* [克劳德代码插件](<https://github.com/anthropics/claude-code>)
* [claudeforge-市场](<https://github.com/claudeforge/marketplace>)

如何安装插件取决于您使用的客户端：

* 在 {% data variables.copilot.copilot_cli_short %} 中，您可以使用 `copilot plugin install` 命令或 `/plugin install` 斜杠命令强制安装插件，或者通过将插件添加到用户级 `~/.copilot/settings.json` 文件或存储库级 `.github/copilot/settings.json` 文件的 `enabledPlugins` 字段来声明性安装插件。参见[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/customize-copilot/plugins-finding-installing>)。
* 在 {% data variables.copilot.copilot_cloud_agent %} 中，您可以通过将插件添加到存储库的 `.github/copilot/settings.json` 文件的 `enabledPlugins` 字段来以声明方式安装插件。要从默认情况下未注册的市场安装插件，您还可以将市场添加到同一文件中的 `extraKnownMarketplaces` 字段。
* 在{% data variables.copilot.github_copilot_app %}中，点击**自定义**，然后点击**插件**浏览市场并安装插件。

企业管理员可以定义适用于企业 {% data variables.product.prodname_copilot_short %} 计划的用户的插件标准，包括指定自动安装的其他市场和插件。参见[AUTOTITLE](<https://github.com/copilot/concepts/enterprise/plugin-standards>)。

## 插件市场如何运作

插件市场是一个插件注册表，您可以从中浏览和安装。市场可以托管在 {% data variables.product.prodname_dotcom_the_website %} 上的存储库中、任何其他在线 Git 托管服务中、或者本地或共享文件系统上。

市场由 `marketplace.json` 文件定义，该文件提供有关市场的元数据并列出其提供的插件。市场的 `plugins` 数组中的每个条目都描述一个插件，包括其名称、描述、版本和插件目录的路径。

由于市场中的插件是有版本的，因此市场可以轻松发现、安装和更新插件，并在团队中共享它们。有关创建市场的分步指南，请参阅 [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/customize-copilot/plugins-marketplace>)。

## 插件与手动配置对比

您可以通过插件添加的任何功能，也可以通过手动配置 {% data variables.product.prodname_copilot_short %} 来添加，例如，通过添加自定义 agent 配置文件或 MCP 服务器。但是，与手动配置相比，插件具有以下几个优点：

|特点|在存储库中手动配置|插件|
|------------|-----------------------------|-----------------|
|适用范围|单一存储库|任何项目|
|分享|说明书【copy/paste】|安装命令或 `enabledPlugins` 条目|
|版本控制|Git 历史记录|市场版本|
|发现|搜索存储库|市场浏览|

## 进一步阅读

* [AUTOTITLE](<https://github.com/copilot/concepts/agents/about-agent-skills>)
* [AUTOTITLE](<https://github.com/copilot/concepts/agents/hooks>)
* [AUTOTITLE](<https://github.com/copilot/concepts/agents/copilot-cli/about-custom-agents>)


## 原文对照

<details>
<summary>展开完整原文</summary>

Plugins provide a way to distribute custom {% data variables.product.prodname_copilot_short %} functionality. You can use a plugin to add a preconfigured set of capabilities to {% data variables.product.prodname_copilot_short %}, including {% data variables.copilot.copilot_cli_short %}, {% data variables.copilot.copilot_cloud_agent %}, and the {% data variables.copilot.github_copilot_app %}.

## What is a plugin?

* A distributable package that extends {% data variables.product.prodname_copilot_short %}'s functionality.
* A bundle of components in a single installable unit.

## What plugins contain

A plugin can contain some or all of the following components. The locations of these components depend on the plugin format:

* **Custom agents** — Specialized AI assistants (`*.agent.md` files in `agents/`)
* **Skills** — Discrete callable capabilities (skills subdirectories in `skills/`, containing a `SKILL.md` file)
* **Hooks** — Event handlers that intercept agent behavior (a `hooks.json` file in the plugin root, or in `hooks/`)
* **MCP server configurations** — Model Context Protocol integrations
* **LSP server configurations** — Language Server Protocol integrations (an `lsp.json` file in the plugin root, or in `.github/`)

## Plugin formats

{% data variables.product.prodname_copilot_short %} supports two plugin formats:

* **Agent Plugins 1.0** is a portable format for sharing skills and MCP server configurations across compatible clients. To use this format, set `$schema` in `plugin.json` to `https://agent-plugins.org/schemas/1.0.0/plugin.schema.json`. Skills are discovered from `skills/`, and MCP server configuration is discovered from `mcp.json` at the plugin root. These locations cannot be configured in the manifest. Choose Agent Plugins 1.0 when you want to make skills and MCP servers portable.
* **Legacy {% data variables.product.prodname_copilot_short %} plugins** do not declare the Agent Plugins `$schema`. They use the existing manifest fields and component discovery behavior, including configurable component paths and MCP configuration in `.mcp.json`, `.github/mcp.json`, or the `mcpServers` manifest field. Choose the legacy format when you need configurable component paths or are maintaining an existing {% data variables.product.prodname_copilot_short %}-specific plugin.

For a new plugin, use Agent Plugins 1.0 unless you require configurable component paths. Use the legacy format primarily for existing legacy plugins.

Both formats are supported. Adding `$schema` changes how {% data variables.product.prodname_copilot_short %} interprets the manifest and discovers components. Plugins without `$schema` continue to load as legacy plugins.

## How plugins are structured

A plugin is a directory with a specific structure and a `plugin.json` manifest file. Agent Plugins 1.0 requires the manifest at the plugin root. Legacy plugins support additional manifest locations. The manifest gives the plugin a name and metadata. Depending on the format, it can also point to components. Alongside the manifest, the directory can contain agents, skills, hooks, MCP server configurations, and LSP server configurations.

### Agent Plugins 1.0 structure

Agent Plugins 1.0 stores skills and MCP servers in standard locations so compatible clients can discover them. Other components, including agents, hooks, commands, and LSP servers, are client-specific. {% data variables.product.prodname_copilot_short %} reads these components from the `com.github.copilot` directory. Other clients ignore this directory, so the same plugin can combine shared skills and MCP servers with {% data variables.product.prodname_copilot_short %}-specific components.

An Agent Plugins 1.0 directory can look like this:

```text
my-plugin/
├── plugin.json               # Required manifest
├── skills/                   # Skills (optional)
│   └── deploy/
│       └── SKILL.md
├── mcp.json                  # MCP server config (optional)
└── com.github.copilot/       # Copilot components (optional)
    ├── agents/
    │   └── helper.agent.md
    ├── commands/
    ├── rules/
    ├── hooks/
    │   └── hooks.json
    └── lsp.json
```

The manifest must include the Agent Plugins 1.0 `$schema`. For supported top-level manifest fields, name requirements, and component locations, see [Agent Plugins 1.0 manifest fields](<https://github.com/copilot/reference/copilot-cli-reference/cli-plugin-reference#agent-plugins-10-manifest-fields>).

### Legacy plugin structure

A legacy {% data variables.product.prodname_copilot_short %} plugin directory can look like this:

```text
my-plugin/
├── plugin.json           # Required manifest
├── agents/               # Custom agents (optional)
│   └── helper.agent.md
├── skills/               # Skills (optional)
│   └── deploy/
│       └── SKILL.md
├── hooks.json            # Hook configuration (optional)
├── .mcp.json             # MCP server config (optional)
└── lsp.json              # LSP server config (optional)
```

For the full set of fields you can include in the manifest, see [AUTOTITLE](<https://github.com/copilot/reference/copilot-cli-reference/cli-plugin-reference>). For step-by-step guidance on authoring a plugin, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/customize-copilot/plugins-creating>).

## Why use plugins?

Plugins provide the following benefits:

* Reusability across projects
* Team standardization of {% data variables.product.prodname_copilot_short %} configuration
* Share domain expertise (for example, by providing the skills of a Rails expert, or a Kubernetes expert)
* Encapsulate complex MCP server setups

## Where can I get plugins?

You can install plugins from:

* A marketplace
* A repository
* A local path

A marketplace is a location where developers can publish, discover, install, and manage plugins. It's a bit like an app store—but for plugins.

Examples of marketplaces include:

* [copilot-plugins](<https://github.com/github/copilot-plugins>) (added by default)
* [awesome-copilot](<https://github.com/github/awesome-copilot>) (added by default)
* [claude-code-plugins](<https://github.com/anthropics/claude-code>)
* [claudeforge-marketplace](<https://github.com/claudeforge/marketplace>)

How you install a plugin depends on which client you're using:

* In {% data variables.copilot.copilot_cli_short %}, you can install plugins imperatively using the `copilot plugin install` command or the `/plugin install` slash command, or declaratively by adding the plugin to the `enabledPlugins` field of either a user-level `~/.copilot/settings.json` file or a repository-level `.github/copilot/settings.json` file. See [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/customize-copilot/plugins-finding-installing>).
* In {% data variables.copilot.copilot_cloud_agent %}, you install plugins declaratively by adding them to the `enabledPlugins` field of the repository's `.github/copilot/settings.json` file. To install plugins from a marketplace that isn't registered by default, you can also add the marketplace to the `extraKnownMarketplaces` field in the same file.
* In the {% data variables.copilot.github_copilot_app %}, click **Customize**, then click **Plugins** to browse marketplaces and install plugins.

Enterprise administrators can define plugin standards that apply to users on the enterprise's {% data variables.product.prodname_copilot_short %} plan, including specifying additional marketplaces and plugins that are automatically installed. See [AUTOTITLE](<https://github.com/copilot/concepts/enterprise/plugin-standards>).

## How plugin marketplaces work

A plugin marketplace is a registry of plugins that you can browse and install from. A marketplace can be hosted in a repository on {% data variables.product.prodname_dotcom_the_website %}, in any other online Git hosting service, or on your local or shared file system.

A marketplace is defined by a `marketplace.json` file, which provides metadata about the marketplace and lists the plugins it makes available. Each entry in the marketplace's `plugins` array describes a plugin—including its name, description, version, and the path to the plugin's directory.

Because plugins in a marketplace are versioned, marketplaces make it easy to discover, install, and update plugins, and to share them across a team. For step-by-step guidance on creating a marketplace, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/customize-copilot/plugins-marketplace>).

## Plugins compared with manual configuration

Any functionality that you could add with a plugin, you could also add by configuring {% data variables.product.prodname_copilot_short %} manually—for example, by adding custom agent profiles or MCP servers. However, plugins provide several advantages over manual configuration:

| Feature    | Manual configuration in a repository | Plugin |
|------------|-----------------------------|-----------------|
| Scope      | Single repository           | Any project |
| Sharing    | Manual copy/paste           | Install command or `enabledPlugins` entry |
| Versioning | Git history                 | Marketplace versions |
| Discovery  | Searching repositories      | Marketplace browsing |

## Further reading

* [AUTOTITLE](<https://github.com/copilot/concepts/agents/about-agent-skills>)
* [AUTOTITLE](<https://github.com/copilot/concepts/agents/hooks>)
* [AUTOTITLE](<https://github.com/copilot/concepts/agents/copilot-cli/about-custom-agents>)


</details>
