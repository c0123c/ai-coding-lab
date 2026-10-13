# 为 Copilot 云端 Agent 配置密钥与变量

说明仓库及组织级配置、使用方式与命名限制，帮助 Agent 访问私有依赖和配置 MCP 服务。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-07-09](<https://github.com/github/docs/commit/3af7cb3042ac2f16645785e9f6dc89922d5e5588>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/configure-secrets-and-variables.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/为 Copilot 云端 Agent 配置密钥与变量.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Configure secrets and variables for Copilot cloud agent
shortTitle: Configure secrets and variables
intro: 'Securely pass secrets and variables to {% data variables.copilot.copilot_cloud_agent %} so it can access private resources and configure MCP servers.'
versions:
  feature: copilot
contentType: how-tos
category:
  - Configure Copilot
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

## 关于 {% data variables.copilot.copilot_cloud_agent %} 的秘密和变量

当您将任务委托给 {% data variables.copilot.copilot_cloud_agent %} 时，它会在由 {% data variables.product.prodname_actions %} 提供支持的自己的临时开发环境中运行。您可能希望将机密和变量传递给 agent：

* 给予 {% data variables.product.prodname_copilot_short %} 在构建、测试或验证您的代码时访问私有资源，例如内部包注册表 agent的环境。
* 通过将 API 密钥、令牌或其他配置传递到服务器来配置 MCP 服务器。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/configure-mcp-servers>)。
* 设置 {% data variables.product.prodname_copilot_short %} 在其环境（包括 `copilot-setup-steps.yml`）中运行的脚本和工具可用的环境变量。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment>)。

{% data variables.copilot.copilot_cloud_agent %} 有自己专用的 **Agents** 秘密和变量，以及现有的 ** 操作 **、** 代码空间 ** 和**Dependabot** 类型。您可以在以下位置配置 Agents 机密和变量：

* **组织级别**，因此单个机密或变量可以在组织中的任何或所有存储库之间共享。
* **存储库级别**，用于仅适用于单个存储库的配置。

您配置的变量和机密将作为环境变量公开给 {% data variables.product.prodname_copilot_short %}，但前缀为 `COPILOT_MCP_` 的机密和变量除外，它们仅适用于 MCP 服务器。

> [!NOTE]
> 如果您之前在存储库的 {% data variables.product.prodname_actions %} 设置中的 `copilot` 环境中配置了机密或变量，则这些机密和变量已自动迁移到新的存储库级别 **Agents** 类型。您无需采取任何操作，并且可以从新位置继续管理它们。

## 配置存储库级机密和变量

您必须是存储库管理员才能为存储库配置 Agents 机密和变量。

{% data reusables.repositories.navigate-to-repo %}
{% data reusables.repositories.sidebar-settings %}
1. 在边栏的“安全”部分中，单击 ** 机密和变量 **，然后单击 **Agents**。
1. 要添加密钥，请单击 **密钥 ** 选项卡，然后单击 **新存储库密钥 **。要添加变量，请单击 **变量 ** 选项卡，然后单击 **新存储库变量 **。
1. 填写“名称”和“值”（或“秘密”）字段，然后单击**添加秘密**或**添加变量**。

## 配置组织级机密和变量

您必须是组织所有者才能为组织配置 Agents 机密和变量。

{% data reusables.organizations.navigate-to-org %}
{% data reusables.organizations.org_settings %}
1. 在边栏的“安全”部分中，单击 ** 机密和变量 **，然后单击 **Agents**。
1. 要添加机密，请单击 ** 机密 ** 选项卡，然后单击 ** 新组织机密 **。要添加变量，请单击**变量**选项卡，然后单击**新建组织变量**。
1. 填写“名称”和“值”（或“秘密”）字段。
1. 在“存储库访问”下，选择组织中的哪些存储库可以访问机密或变量：

   * **所有存储库**：组织中的任何存储库都可以访问机密或变量。
   * **私有存储库**：组织中的任何私有或内部存储库都可以访问机密或变量。
   * **选定的存储库**：只有您指定的存储库才能访问机密或变量。

1. 单击**添加秘密**或**添加变量**。

## 使用秘密和变量

配置完成后， Agents 秘密和变量自动可用 {% data variables.copilot.copilot_cloud_agent %} 当它处理存储库中的任务时。他们暴露于 agent 作为其开发环境中的环境变量，因此它们可以被脚本和工具使用 {% data variables.product.prodname_copilot_short %} 运行，包括您的 `copilot-setup-steps.yml` 工作流程。

秘密值在 {% data variables.copilot.copilot_cloud_agent %} 会话日志中被屏蔽。

> [!NOTE]
> {% data variables.copilot.copilot_cloud_agent %} 无权访问 {% data variables.product.prodname_actions %}、{% data variables.product.prodname_codespaces %} 或 {% data variables.product.prodname_dependabot %} 机密和变量。仅 Agents 秘密和变量被传递到 agent。

## 秘密和变量的命名要求

姓名必须：

* 仅包含字母数字字符（`[A-Z]`、`[0-9]`）或下划线（`_`）。不允许有空格。
* 不以 `GITHUB_` 前缀开头。
* 不以数字开头。

名称不区分大小写。小写字母将转换为大写字母。名称在创建的级别上必须是唯一的。

如果多个级别存在同名的变量或密钥，则最低级别的值优先。例如，存储库级机密将覆盖同名的组织级机密。

对于要传递到 MCP 服务器的机密和变量，名称必须以前缀 `COPILOT_MCP_` 开头。只有具有此前缀的 Agents 机密和变量可用于您的 MCP 配置。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/configure-mcp-servers>)。

## 进一步阅读

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/configure-mcp-servers>)


## 原文对照

<details>
<summary>展开完整原文</summary>

## About secrets and variables for {% data variables.copilot.copilot_cloud_agent %}

When you delegate a task to {% data variables.copilot.copilot_cloud_agent %}, it works in its own ephemeral development environment, powered by {% data variables.product.prodname_actions %}. You may want to pass secrets and variables to the agent to:

* Give {% data variables.product.prodname_copilot_short %} access to private resources, such as internal package registries, when it builds, tests, or validates your code in the agent's environment.
* Configure MCP servers, by passing API keys, tokens, or other configuration to the servers. For more information, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/configure-mcp-servers>).
* Set environment variables that are available to scripts and tools that {% data variables.product.prodname_copilot_short %} runs in its environment, including in `copilot-setup-steps.yml`. For more information, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment>).

{% data variables.copilot.copilot_cloud_agent %} has its own dedicated **Agents** secrets and variables, alongside the existing **Actions**, **Codespaces**, and **Dependabot** types. You can configure Agents secrets and variables at:

* The **organization level**, so a single secret or variable can be shared across any or all repositories in your organization.
* The **repository level**, for configuration that only applies to a single repository.

Variables and secrets that you configure are exposed to {% data variables.product.prodname_copilot_short %} as environment variables, except secrets and variables prefixed with `COPILOT_MCP_`, which are only available to MCP servers.

> [!NOTE]
> If you previously configured secrets or variables in the `copilot` environment in a repository's {% data variables.product.prodname_actions %} settings, those secrets and variables have been automatically migrated to the new repository-level **Agents** type. You don't need to take any action, and you can manage them from the new location going forward.

## Configuring repository-level secrets and variables

You must be a repository administrator to configure Agents secrets and variables for a repository.

{% data reusables.repositories.navigate-to-repo %}
{% data reusables.repositories.sidebar-settings %}
1. In the "Security" section of the sidebar, click **Secrets and variables**, then click **Agents**.
1. To add a secret, click the **Secrets** tab, then click **New repository secret**. To add a variable, click the **Variables** tab, then click **New repository variable**.
1. Fill in the "Name" and "Value" (or "Secret") fields, and then click **Add secret** or **Add variable**.

## Configuring organization-level secrets and variables

You must be an organization owner to configure Agents secrets and variables for an organization.

{% data reusables.organizations.navigate-to-org %}
{% data reusables.organizations.org_settings %}
1. In the "Security" section of the sidebar, click **Secrets and variables**, then click **Agents**.
1. To add a secret, click the **Secrets** tab, then click **New organization secret**. To add a variable, click the **Variables** tab, then click **New organization variable**.
1. Fill in the "Name" and "Value" (or "Secret") fields.
1. Under "Repository access", choose which repositories in your organization can access the secret or variable:

   * **All repositories**: any repository in the organization can access the secret or variable.
   * **Private repositories**: any private or internal repository in the organization can access the secret or variable.
   * **Selected repositories**: only the repositories you specify can access the secret or variable.

1. Click **Add secret** or **Add variable**.

## Using secrets and variables

Once configured, Agents secrets and variables are automatically available to {% data variables.copilot.copilot_cloud_agent %} when it works on a task in the repository. They are exposed to the agent as environment variables in its development environment, so they can be used by scripts and tools that {% data variables.product.prodname_copilot_short %} runs, including by your `copilot-setup-steps.yml` workflow.

Secret values are masked in {% data variables.copilot.copilot_cloud_agent %} session logs.

> [!NOTE]
> {% data variables.copilot.copilot_cloud_agent %} does not have access to {% data variables.product.prodname_actions %}, {% data variables.product.prodname_codespaces %}, or {% data variables.product.prodname_dependabot %} secrets and variables. Only Agents secrets and variables are passed to the agent.

## Naming requirements for secrets and variables

Names must:

* Only contain alphanumeric characters (`[A-Z]`, `[0-9]`) or underscores (`_`). Spaces are not allowed.
* Not start with the `GITHUB_` prefix.
* Not start with a number.

Names are case-insensitive. Lowercase letters are converted to uppercase. Names must be unique at the level at which they are created.

If a variable or secret with the same name exists at multiple levels, the value at the lowest level takes precedence. For example, a repository-level secret will override an organization-level secret with the same name.

For secrets and variables that you want to pass to MCP servers, the name must begin with the prefix `COPILOT_MCP_`. Only Agents secrets and variables with this prefix are available to your MCP configuration. For more information, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/configure-mcp-servers>).

## Further reading

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/configure-mcp-servers>)


</details>
