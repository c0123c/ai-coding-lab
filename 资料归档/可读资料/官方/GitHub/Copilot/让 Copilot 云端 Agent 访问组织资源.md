# 让 Copilot 云端 Agent 访问组织资源

以私有包和 MCP 为例说明保存密钥、设置 Agent 与安装依赖，并解释谁可管理这些配置。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-09-15](<https://github.com/github/docs/commit/1413b140620171c5e4494c52e28a736e4b68b36e>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/tutorials/cloud-agent/give-access-to-resources.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/让 Copilot 云端 Agent 访问组织资源.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: 'Giving GitHub Copilot cloud agent access to resources in your organization'
shortTitle: 'Give access to resources'
intro: 'Get more out of {% data variables.product.prodname_copilot_short %} by giving it access to approved MCP servers and internal packages.'
versions:
  feature: copilot
contentType: tutorials
category:
  - Manage Copilot for a team
  - Roll Copilot out at scale
docsTeamMetrics:
  - ai-governance
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

{% data variables.copilot.copilot_cloud_agent %} 可以连接到 MCP 服务器、使用私有包以及访问外部服务，但前提是您组织的存储库配置为允许这样做。

尽管下面的大部分配置是在存储库级别完成的，但组织所有者可以控制哪些资源在范围内以及谁可以配置对它们的访问权限。

## 示例场景

您的组织使用 Sentry 来跟踪 Node 应用程序中的错误。新的异常作为 {% data variables.product.github %} 上的问题提出，并且您的开发人员希望将这些问题分配给 {% data variables.product.prodname_copilot_short %}。

您希望 {% data variables.product.prodname_copilot_short %} ：

* 连接到 Sentry MCP 服务器，以便它可以访问 Sentry 实例的详细信息
* 安装依赖项，包括 {% data variables.product.github %} 上托管的私有包，以构建您的应用程序并运行测试
* 遵循组织的错误处理约定

## 安全地存储秘密

默认情况下，{% data variables.product.prodname_copilot_short %} 的身份验证令牌的范围仅限于其运行的存储库。这意味着 {% data variables.product.prodname_copilot_short %} 将无法对外部系统进行身份验证或访问私有的组织范围的包。

要向 {% data variables.product.prodname_copilot_short %} 提供对机密的访问权限，存储库管理员可以配置 Agents 机密。组织所有者还可以在组织级别添加 Agents 机密，以跨多个存储库共享配置。 {% data variables.product.prodname_copilot_short %} 可以在其设置和任务执行期间访问此数据。它将无法访问 {% data variables.product.prodname_actions %}、{% data variables.product.prodname_codespaces %} 或 {% data variables.product.prodname_dependabot %} 机密和变量。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/configure-secrets-and-variables>)。

### 示例：保存秘密

存储库管理员保存组织的 Sentry 实例的身份验证令牌。

1. Go 到存储库设置的 **秘密和变量 ** > **Agents** 部分。
1. 将 Sentry 实例的访问令牌保存在名为 `COPILOT_MCP_SENTRY_ACCESS_TOKEN` 的密钥中。

> [!TIP] 我们不需要为我们的私有 {% data variables.product.prodname_registry %} 注册表保存令牌，我们将使用标准 {% data variables.product.prodname_actions %} `GITHUB_TOKEN` 访问该注册表。但是，如果您使用外部包注册表，则需要保存身份验证令牌。

## 配置对 MCP 服务器的访问

组织和企业所有者可以设置策略以允许用户配置对 MCP 服务器的访问。如果启用此策略，用户可以在存储库设置或自定义 agent 配置文件中为 {% data variables.copilot.copilot_cloud_agent %} 配置 MCP 服务器。为了实现组织范围内的一致性，我们建议在组织或企业级别创建 ** 自定义 agent 配置文件 **。

使用自定义 agent 的会话可以访问 ** 中配置的 MCP 服务器，包括 ** 存储库设置和 agent 配置文件。但是，您使用组织范围的自定义 agents 覆盖的用例越多，用户需要在存储库设置中配置对 MCP 服务器的临时访问权限的用户就越少。

我们建议浏览 [{% data variables.product.github %} MCP 注册表 ](<https://github.com/mcp>) 来查找值得信赖、评价较高的选项。

### 示例：创建自定义 agent

组织所有者为 Sentry agent 创建自定义 agent 配置文件。它可以访问 Sentry MCP 服务器和组织错误处理约定的自定义指令。

1. 在您的组织中创建一个名为 `.github-private` 的存储库。或者，企业所有者可以将此存储库设置为企业中所有自定义 agents 的源。
1. 在存储库中，添加具有如下配置文件的 `agent.md` 文件。这包括 MCP 服务器的配置，它引用我们保存的秘密。

   ``` text
   ---
   name: sentry-error-fixer
   description: Proposed fixes for exception issues raised from Sentry
   mcp-servers:
     sentry:
       type: 'local'
       command: 'npx'
       args: ['@sentry/mcp-server@latest']
       env:
         SENTRY_ACCESS_TOKEN: {% raw %}${{ secrets.COPILOT_MCP_SENTRY_ACCESS_TOKEN }}{% endraw %}
   ---

   You are an error resolution specialist. When you're assigned an issue created by our Sentry integration, check for error details and stack traces using the MCP server, then propose a fix.

   Make sure you check that your proposed fix works by building the site with `npm run build` and running the test suite in `npm test`.
   ```

1. 当开发人员将问题分配给 {% data variables.product.prodname_copilot_short %} 时，他们可以从下拉列表中选择自定义 agent。

## 安装私有包

授予 {% data variables.product.prodname_copilot_short %} 访问项目依赖项的最佳方法是将它们安装在 `copilot-setup-steps.yml` 工作流程文件中。该文件定义了 {% data variables.product.prodname_copilot_short %} 开始工作之前如何设置环境。

为了允许工作流程提取您的私有、组织范围内的包，您将更新包设置以确保存储库的 `GITHUB_TOKEN` 有权访问该包。这比使用具有组织权限的长期 {% data variables.product.pat_generic %} 更安全。

### 示例：安装节点依赖项

开发人员创建一个工作流程来安装存储库的 `package-lock.json` 文件中定义的节点依赖项。这包括托管在 {% data variables.product.github %} 上的私有、组织范围的包。

1. 开发人员在存储库中创建一个 `copilot-setup-steps.yml` 文件。
1. 他们添加了安装项目依赖项的步骤。例如：

   {% data reusables.copilot.cloud-agent.install-dependencies %}

1. 组织管理员通过在每个包的设置中授予对存储库的访问权限，确保存储库能够访问组织的私有包。参见[AUTOTITLE](<https://github.com/packages/learn-github-packages/configuring-a-packages-access-control-and-visibility#github-actions-access-for-packages-scoped-to-organizations>)。

>[!TIP] 如果您需要访问公司网络内部托管的软件包，您可能需要在自托管 {% data variables.product.prodname_actions %} 运行器上运行 {% data variables.copilot.copilot_cloud_agent %}。

## 控制谁可以配置这些设置

现在您已经了解了如何在存储库和组织级别控制对资源的访问，请考虑您希望为用户提供多少范围来管理这些设置。

1. **选择哪些存储库有权访问**到{% data variables.copilot.copilot_cloud_agent %}。如果您担心特定存储库，可以为所有用户阻止它。
1. **考虑谁获得这些存储库的管理员访问权限**。您可以通过创建具有 **所有存储库管理员 ** 自定义角色的团队来在组织级别对此进行控制。这些用户将能够在每个存储库中管理配置_设置_，例如MCP配置和Agents秘密和变量。
1. **使用规则集和CODEOWNERS文件**来控制配置_文件_的编辑，例如`copilot-setup-steps.yml`，默认情况下任何具有写入权限的人都可以编辑。
1. **查看默认防火墙**。防火墙不会影响与 MCP 服务器的连接或 `copilot-setup-steps.yml` 中的设置步骤，但会限制 {% data variables.product.prodname_copilot_short %} 在任务执行期间对 Internet 的访问。参见[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-the-firewall>)。
1. **定义插件标准**。插件是可安装的软件包，通过可重用的 agents、技能、挂钩和集成来扩展 {% data variables.product.prodname_copilot_short %}。您可以控制企业的 `{% data variables.copilot.managed_setting_file %}` 文件中允许哪些插件和市场。参见[AUTOTITLE](<https://github.com/copilot/concepts/enterprise/plugin-standards>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

{% data variables.copilot.copilot_cloud_agent %} can connect to MCP servers, use private packages, and access external services, but only if your organization's repositories are configured to allow it.

Although much of the configuration below is done at the repository level, organization owners have control over which resources are in scope and who can configure access to them.

## Example scenario

Your organization uses Sentry to track bugs in your Node app. New exceptions are raised as issues on {% data variables.product.github %}, and your developers want to assign these issues to {% data variables.product.prodname_copilot_short %}.

You want {% data variables.product.prodname_copilot_short %} to:

* Connect to the Sentry MCP server so it can access details on your Sentry instance
* Install dependencies, including private packages hosted on {% data variables.product.github %}, to build your app and run tests
* Follow your organization's conventions for error-handling

## Storing secrets securely

By default, the scope of {% data variables.product.prodname_copilot_short %}'s authentication token is limited to the repository where it's running. This means that {% data variables.product.prodname_copilot_short %} won't be able to authenticate to external systems or access private, organization-scoped packages.

To provide {% data variables.product.prodname_copilot_short %} with access to secrets, repository administrators can configure Agents secrets. Organization owners can also add Agents secrets at the organization level to share configuration across multiple repositories. {% data variables.product.prodname_copilot_short %} can access this data during its setup and task execution. It won't be able to access {% data variables.product.prodname_actions %}, {% data variables.product.prodname_codespaces %}, or {% data variables.product.prodname_dependabot %} secrets and variables. For more information, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/configure-secrets-and-variables>).

### Example: Save a secret

A repository administrator saves an authentication token for the organization's Sentry instance.

1. Go to the **Secrets and variables** > **Agents** section of the repository settings.
1. Save an access token for your Sentry instance in a secret called `COPILOT_MCP_SENTRY_ACCESS_TOKEN`.

> [!TIP] We don't need to save a token for our private {% data variables.product.prodname_registry %} registry, which we'll access using the standard {% data variables.product.prodname_actions %} `GITHUB_TOKEN`. However, you would want to save an authentication token if you were using an external package registry.

## Configuring access to MCP servers

Organization and enterprise owners can set a policy to allow users to configure access to MCP servers. If this policy is enabled, users can configure MCP servers for {% data variables.copilot.copilot_cloud_agent %} in repository settings or in custom agent profiles. For organization-wide consistency, we recommend creating **custom agent profiles** at the organization or enterprise level.

A session using a custom agent has access to MCP servers configured in **both** the repository settings and the agent profile. However, the more use cases you cover with organization-wide custom agents, the less users will need to configure ad hoc access to MCP servers in repository settings.

We recommend browsing the [{% data variables.product.github %} MCP Registry](<https://github.com/mcp>) to find trusted, highly rated options.

### Example: Create a custom agent

An organization owner creates a custom agent profile for the Sentry agent. It has access to the Sentry MCP server and custom instructions for the organization's error-handling conventions.

1. Create a repository called `.github-private` in your organization. Optionally, an enterprise owner can set this repository as the source for all custom agents in the enterprise.
1. In the repository, add an `agent.md` file with a profile like the following. This includes configuration for the MCP server, which references the secret we saved.

   ``` text
   ---
   name: sentry-error-fixer
   description: Proposed fixes for exception issues raised from Sentry
   mcp-servers:
     sentry:
       type: 'local'
       command: 'npx'
       args: ['@sentry/mcp-server@latest']
       env:
         SENTRY_ACCESS_TOKEN: {% raw %}${{ secrets.COPILOT_MCP_SENTRY_ACCESS_TOKEN }}{% endraw %}
   ---

   You are an error resolution specialist. When you're assigned an issue created by our Sentry integration, check for error details and stack traces using the MCP server, then propose a fix.

   Make sure you check that your proposed fix works by building the site with `npm run build` and running the test suite in `npm test`.
   ```

1. When developers assign an issue to {% data variables.product.prodname_copilot_short %}, they can select the custom agent from a dropdown.

## Installing private packages

The best way to give {% data variables.product.prodname_copilot_short %} access to a project's dependencies is to install them in a `copilot-setup-steps.yml` workflow file. This file defines how the environment is set up before {% data variables.product.prodname_copilot_short %} starts working.

To allow the workflow to pull your private, organization-scoped packages, you will update the package settings to make sure that the repository's `GITHUB_TOKEN` has access to the package. This is more secure than using a long-lived {% data variables.product.pat_generic %} with organization permissions.

### Example: Install Node dependencies

A developer creates a workflow to install the Node dependencies defined in a repository's `package-lock.json` file. This includes private, organization-scoped packages hosted on {% data variables.product.github %}.

1. The developer creates a `copilot-setup-steps.yml` file in the repository.
1. They add steps for installing the project's dependencies. For example:

   {% data reusables.copilot.cloud-agent.install-dependencies %}

1. An organization administrator ensures that the repository has access to the organization's private packages by granting access to the repository in each package's settings. See [AUTOTITLE](<https://github.com/packages/learn-github-packages/configuring-a-packages-access-control-and-visibility#github-actions-access-for-packages-scoped-to-organizations>).

>[!TIP] If you need to access packages that are hosted internally within your corporate network, you may need to run {% data variables.copilot.copilot_cloud_agent %} on self-hosted {% data variables.product.prodname_actions %} runners.

## Controlling who can configure these settings

Now you have seen how access to resources is controlled at the repository and organization levels, consider how much scope you want to give users to manage these settings.

1. **Choose which repositories have access** to {% data variables.copilot.copilot_cloud_agent %}. If you're concerned about a specific repository, you can block it for all users.
1. **Consider who gets admin access** to these repositories. You can control this at the organization level by creating a team with the **All-repository admin** custom role. These users will be able to manage configuration _settings_, such as MCP configuration and Agents secrets and variables, in every repository.
1. **Use rulesets and CODEOWNERS files** to control edits of configuration _files_, such as `copilot-setup-steps.yml`, which anyone with write access can edit by default.
1. **Review the default firewall**. The firewall doesn't affect connections to MCP servers or setup steps in `copilot-setup-steps.yml`, but it does limit {% data variables.product.prodname_copilot_short %}'s access to the Internet during task execution. See [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-the-firewall>).
1. **Define plugin standards**. Plugins are installable packages that extend {% data variables.product.prodname_copilot_short %} with reusable agents, skills, hooks, and integrations. You can control which plugins and marketplaces are permitted in an enterprise's `{% data variables.copilot.managed_setting_file %}` file. See [AUTOTITLE](<https://github.com/copilot/concepts/enterprise/plugin-standards>).


</details>
