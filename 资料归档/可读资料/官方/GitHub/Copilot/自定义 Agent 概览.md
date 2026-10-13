# 自定义 Agent 概览

介绍通过 Markdown 配置专门角色、指令、工具和 MCP 服务，将团队约定固化为可复用 Agent；说明配置格式、位置与支持界面。

- 来源：GitHub／Copilot
- 资料类型：存档文档
- 文档时间：官方文件提交（截至抓取）：[2026-09-04](<https://github.com/github/docs/commit/31dde23b61c15aa120f1cd4747d0a777f423b118>)；发表/更新：官方未公开；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://docs.github.com/en/copilot/concepts/agents/cloud-agent/about-custom-agents>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/自定义 Agent 概览.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 关于定制【agents】

自定义 agents 通过根据您的需求量身定制的帮助来增强 Copilot。

## 关于定制【agents】

自定义 agents 是 Copilot agent 的专门版本，您可以根据自己独特的工作流程、编码约定和用例进行定制。他们就像量身定制的队友一样，遵循您的标准、使用正确的工具并实施特定于团队的实践。您定义这些 agents 一次，而不是重复提供相同的指令和上下文。

您可以使用名为 agent 配置文件的 Markdown 文件定义自定义 agents。这些文件指定提示、工具和 MCP 服务器。这允许您将约定、框架和期望的结果直接编码到 Copilot 中。

agent 配置文件定义自定义 agent 的行为。当您将 agent 分配给任务或问题时，它会实例化自定义 agent。

## Agent配置文件格式

Agent 配置文件是带有 YAML frontmatter 的 Markdown 文件。最简单的形式包括：

* **名称**（可选）：自定义agent的显示名称。如果省略，agent 的文件名将用作其标识符和默认显示名称。
* **描述**：解释agent的用途和功能。
* **提示**：定义agent的行为和专业知识的自定义指令。
* **工具**（可选）：agent可以访问的特定工具。默认情况下，agents可以访问所有可用的工具，包括内置工具和MCP服务器工具。

Agent 配置文件还可以使用 `mcp-servers` 属性包含 MCP 服务器配置。

### 示例 agent 配置文件

此示例是一个基本的 agent 配置文件，其中配置了名称、描述和提示。

```text
---
name: readme-creator
description: Agent specializing in creating and improving README files
---

You are a documentation specialist focused on README files. Your scope is limited to README files or other related documentation files only - do not modify or analyze code files.

Focus on the following instructions:
- Create and update README.md files with clear project descriptions
- Structure README sections logically: overview, installation, usage, contributing
- Write scannable content with proper headings and formatting
- Add appropriate badges, links, and navigation elements
- Use relative links (e.g., `docs/CONTRIBUTING.md`) instead of absolute URLs for files within the repository
- Make links descriptive and add alt text to images
```

## 您可以在其中配置自定义agents

您可以在不同级别定义 agent 配置文件：

* **存储库级别**：在存储库中为特定于项目的agents创建`.github/agents/CUSTOM-AGENT-NAME.md`。
* **组织级别**：在组织的`.github`或`.github-private`存储库中创建`/agents/CUSTOM-AGENT-NAME.md`，以在组织内获得更广泛的可用性。
* **企业级**：在企业所有者在企业设置中指定的组织的`.github-private`存储库中创建`/agents/CUSTOM-AGENT-NAME.md`，以便在企业中的所有存储库中可用。

有关详细信息，请参阅[准备在您的组织中使用自定义 agents](<https://docs.github.com/en/copilot/how-tos/administer-copilot/manage-for-organization/prepare-for-custom-agents>) 和 [准备在您的企业中使用自定义 agents](<https://docs.github.com/en/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/prepare-for-custom-agents>)。

## 哪里可以使用自定义agents

> \[！NOTE]
> 自定义 agents 正在为 JetBrains IDE、Eclipse 和 Xcode 提供公共预览版，并且可能会发生变化。

创建自定义 agents 后，它们即可用于：

* **Copilot 云 agent，位于 GitHub.com**：agents 选项卡和面板、问题分配和拉取请求
* IDE 中的 **Copilot 云 agent **：Visual Studio Code、JetBrains IDE、Eclipse 和 Xcode
* **GitHub Copilot 应用程序**
* **GitHub Copilot CLI**

您可以直接在 Visual Studio Code、JetBrains IDE、Eclipse 和 Xcode 中使用 agent 配置文件。某些属性在环境之间可能会有所不同或被忽略。

有关使用自定义的更多信息 agents 在 Visual Studio Code，参见 [定制 agents 在 VS Code](<https://code.visualstudio.com/docs/copilot/customization/custom-agents>).

## 后续步骤

要创建您自己的自定义 agents，请参阅：

* [为 Copilot 云 agent](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/create-custom-agents>) 创建自定义 agents
* [为 GitHub Copilot CLI](<https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/create-custom-agents-for-cli>) 创建和使用自定义 agents
* [Copilot 定制备忘单](<https://docs.github.com/en/copilot/reference/customization-cheat-sheet>)


## 原文对照

<details>
<summary>展开完整原文</summary>

# About custom agents

Custom agents enhance Copilot with assistance tailored to your needs.

## About custom agents

Custom agents are specialized versions of the Copilot agent that you can tailor to your unique workflows, coding conventions, and use cases. They act like tailored teammates that follow your standards, use the right tools, and implement team-specific practices. You define these agents once instead of repeatedly providing the same instructions and context.

You define custom agents using Markdown files called agent profiles. These files specify prompts, tools, and MCP servers. This allows you to encode your conventions, frameworks, and desired outcomes directly into Copilot.

The agent profile defines the custom agent's behavior. When you assign the agent to a task or issue, it instantiates the custom agent.

## Agent profile format

Agent profiles are Markdown files with YAML frontmatter. In their simplest form, they include:

* **Name** (optional): A display name for the custom agent. If omitted, the agent's filename is used as its identifier and default display name.
* **Description**: Explains the agent's purpose and capabilities.
* **Prompt**: Custom instructions that define the agent's behavior and expertise.
* **Tools** (optional): Specific tools the agent can access. By default, agents can access all available tools, including built-in tools, and MCP server tools.

Agent profiles can also include MCP server configurations using the `mcp-servers` property.

### Example agent profile

This example is a basic agent profile with name, description, and prompt configured.

```text
---
name: readme-creator
description: Agent specializing in creating and improving README files
---

You are a documentation specialist focused on README files. Your scope is limited to README files or other related documentation files only - do not modify or analyze code files.

Focus on the following instructions:
- Create and update README.md files with clear project descriptions
- Structure README sections logically: overview, installation, usage, contributing
- Write scannable content with proper headings and formatting
- Add appropriate badges, links, and navigation elements
- Use relative links (e.g., `docs/CONTRIBUTING.md`) instead of absolute URLs for files within the repository
- Make links descriptive and add alt text to images
```

## Where you can configure custom agents

You can define agent profiles at different levels:

* **Repository level**: Create `.github/agents/CUSTOM-AGENT-NAME.md` in your repository for project-specific agents.
* **Organization level**: Create `/agents/CUSTOM-AGENT-NAME.md` in the organization's `.github` or `.github-private` repository for broader availability within the organization.
* **Enterprise level**: Create `/agents/CUSTOM-AGENT-NAME.md` in the `.github-private` repository of an organization that an enterprise owner has designated in enterprise settings for availability across all repositories in the enterprise.

For more information, see [Preparing to use custom agents in your organization](<https://docs.github.com/en/copilot/how-tos/administer-copilot/manage-for-organization/prepare-for-custom-agents>) and [Preparing to use custom agents in your enterprise](<https://docs.github.com/en/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/prepare-for-custom-agents>).

## Where you can use custom agents

> \[!NOTE]
> Custom agents are in public preview for JetBrains IDEs, Eclipse, and Xcode, and subject to change.

Once you create custom agents, they become available to:

* **Copilot cloud agent on GitHub.com**: The agents tab and panel, issue assignment, and pull requests
* **Copilot cloud agent in IDEs**: Visual Studio Code, JetBrains IDEs, Eclipse, and Xcode
* **GitHub Copilot app**
* **GitHub Copilot CLI**

You can use agent profiles directly in Visual Studio Code, JetBrains IDEs, Eclipse, and Xcode. Some properties may function differently or be ignored between environments.

For more information on using custom agents in Visual Studio Code, see [Custom agents in VS Code](<https://code.visualstudio.com/docs/copilot/customization/custom-agents>).

## Next steps

To create your own custom agents, see:

* [Creating custom agents for Copilot cloud agent](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/create-custom-agents>)
* [Creating and using custom agents for GitHub Copilot CLI](<https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/create-custom-agents-for-cli>)
* [Copilot customization cheat sheet](<https://docs.github.com/en/copilot/reference/customization-cheat-sheet>)


</details>
