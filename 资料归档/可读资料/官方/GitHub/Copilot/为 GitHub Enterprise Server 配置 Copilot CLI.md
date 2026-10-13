# 为 GitHub Enterprise Server 配置 Copilot CLI

说明服务端模型提供方、客户端连接、用户令牌及支持能力；适用于自托管企业实例。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-09-22](<https://github.com/github/docs/commit/87251ef3fe9f0f373ceeb9a826b7dfe4bdb2500a>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/copilot-on-ghes/set-up-copilot-cli.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/为 GitHub Enterprise Server 配置 Copilot CLI.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Set up GitHub Copilot CLI for GitHub Enterprise Server
shortTitle: Set up {% data variables.copilot.copilot_cli_short %}
intro: Configure a model provider for your {% data variables.product.prodname_ghe_server %} instance, then connect {% data variables.copilot.copilot_cli_short %} clients to the instance.
allowTitleToDifferFromFilename: true
versions:
  feature: copilot-cli-ghes
contentType: how-tos
docsTeamMetrics:
  - copilot-cli
redirect_from:
  - /admin/github-copilot/copilot-cli/set-up-and-use-copilot-cli
category:
  - Manage Copilot for a team
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

> [！NOTE]
> 此功能位于 {% data variables.release-phases.technical_preview %} 中，可能会发生变化。

您可以将 {% data variables.copilot.copilot_cli_short %} 配置为在断开连接或气隙环境中与 {% data variables.product.prodname_ghe_server %} 配合使用，而无需连接到 {% data variables.product.github %} 云。管理员为实例配置模型提供程序，用户使用其 {% data variables.product.prodname_ghe_server %} 凭据连接 {% data variables.copilot.copilot_cli_short %}。

设置此功能涉及两个角色：

* **管理员**：使用`ghe-config`在{% data variables.product.prodname_ghe_server %}实例上配置模型提供程序。这是一项一次性设置，需要管理 SSH 访问权限。
* **最终用户**：在本地计算机上设置环境变量以将{% data variables.copilot.copilot_cli_short %}连接到实例。

## 先决条件

* 您拥有 {% data variables.product.prodname_ghe_server %} 实例的管理 SSH 访问权限。
* 您拥有来自受支持的 LLM 提供商的 API 密钥。
* {% data variables.copilot.copilot_cli_short %} 安装在客户端计算机上。参见[AUTOTITLE](<https://github.com/enterprise-cloud@latest/copilot/how-tos/copilot-cli/set-up-copilot-cli/install-copilot-cli>)。
* {% data variables.product.prodname_cli %} (`gh`) 安装在客户端计算机上。请参阅{% data variables.product.prodname_cli %}文档中的[{% data variables.product.prodname_cli %}手册](<https://cli.github.com/manual/>)。

有关支持的提供程序和型号要求的信息，请参阅 [AUTOTITLE](<https://github.com/enterprise-cloud@latest/copilot/how-tos/copilot-cli/customize-copilot/use-byok-models>)。

## 配置您的 {% data variables.product.prodname_ghe_server %} 实例

此步骤适用于{% data variables.product.prodname_ghe_server %}实例的操作员或管理员。

通过对 {% data variables.product.prodname_ghe_server %} 实例的管理 SSH 访问权限，使用以下 `ghe-config` 值配置模型提供程序。配置后，运行`ghe-config-apply`以应用更改。

|变量名|必填|选项|描述|
|---|---|---|---|
| `app.copilot-proxy.enabled` |是的| `true`, `false` |启用或禁用该功能。|
| `app.copilot-proxy.endpoint-url` |是的| URI |完整的上游基础 URL 包括任何版本前缀（例如 `https://api.openai.com/v1`）。|
| `secrets.copilot-proxy.endpoint-key` |是的|字符串|上游提供商的 API 密钥。|
| `app.copilot-proxy.provider-model-id` |是的|字符串|{% data variables.copilot.copilot_cli_short %} 用于在内部查找模型的提供者模型 ID。|
| `app.copilot-proxy.provider-type` |是的| `openai`, `azure`, `anthropic` |提供者类型。 OpenAI 包括 OpenAI、Ollama、vLLM、Foundry Local 和任何其他 OpenAI 聊天完成 API- 兼容端点。|
| `app.copilot-proxy.upstream-timeout` |否|整数，以秒为单位|Read/send 上游请求超时（以秒为单位）。如果未设置，则回退到默认超时。|
| `app.copilot-proxy.provider-wire-api` |否| `completions`, `responses` |提供商的电汇 API 格式。|
| `app.copilot-proxy.provider-wire-model` |否|字符串|如果发送到上游提供者的模型标识符与内部模型 ID 不同，则覆盖该模型标识符。|
| `app.copilot-proxy.enable-upstream-probe` |否| `true`, `false` |启用或禁用启动上游探测。默认为启用。禁用后，将跳过启动探测。|

例如，以下命令配置 OpenAI 提供程序。

```shell
ghe-config app.copilot-proxy.enabled true
ghe-config app.copilot-proxy.endpoint-url 'https://api.openai.com/v1'
ghe-config secrets.copilot-proxy.endpoint-key 'YOUR-API-KEY'
ghe-config app.copilot-proxy.provider-model-id 'gpt-5.5'
ghe-config app.copilot-proxy.provider-wire-model 'gpt-5.5'
ghe-config app.copilot-proxy.provider-type openai
ghe-config app.copilot-proxy.upstream-timeout 300
ghe-config app.copilot-proxy.enable-upstream-probe false
ghe-config-apply
```

在应用配置之前，将 `YOUR-API-KEY` 替换为真实的 API 密钥。

## 配置您的 {% data variables.copilot.copilot_cli_short %} 客户端（最终用户）

在启动 {% data variables.copilot.copilot_cli_short %} 之前，通过设置以下环境变量来配置 {% data variables.copilot.copilot_cli_short %} 连接到您的 {% data variables.product.prodname_ghe_server %} 实例。

|环境变量|必填|描述|
|---|---|---|
| `COPILOT_PROVIDER_GHES_HOST` |是的|您的 {% data variables.product.prodname_ghe_server %} 实例的主机名。|
| `COPILOT_PROVIDER_GHES_TOKEN` |是的|{% data variables.product.prodname_ghe_server %} 实例的 {% data variables.product.pat_generic %}。此令牌验证对实例的请求。|
| `COPILOT_OFFLINE` |是的|启用离线模式。 {% data variables.product.prodname_ghe_server %} 提供程序仅在启用离线模式时才处于活动状态。|

## 了解客户端（最终用户）令牌

{% data variables.copilot.copilot_cli_short %} 需要访问 LLM 推理，因此始终需要 `COPILOT_PROVIDER_GHES_TOKEN`。您也很可能希望 {% data variables.copilot.copilot_cli_short %} 执行 {% data variables.product.github %} 操作，例如创建问题、拉取请求和搜索存储库。此类操作可以通过{% data variables.product.prodname_cli %}完成。

建议并首选您运行 `gh auth login --hostname YOUR-GHES-HOSTNAME`。成功后，下一步是将`COPILOT_PROVIDER_GHES_TOKEN`设置为`gh auth login --hostname YOUR-GHES-HOSTNAME`中生成的令牌。动态检索令牌比从 `~/.config/gh/hosts.yml` 复制令牌更安全。您可以使用 `COPILOT_PROVIDER_GHES_TOKEN="$(gh auth token --hostname YOUR-GHES-HOSTNAME)"` 来执行此操作。

或者，您可以在 {% data variables.product.prodname_ghe_server %} 实例上生成 {% data variables.product.pat_generic %}，将该令牌设置为 `COPILOT_PROVIDER_GHES_TOKEN`，并在运行 `gh auth login --hostname YOUR-GHES-HOSTNAME` 时使用相同的令牌。

当您交互使用 {% data variables.copilot.copilot_cli_short %} 时，上述方法有效。对于自动化，您需要做一些不同的事情：
* 将`GH_ENTERPRISE_TOKEN`（或`GITHUB_ENTERPRISE_TOKEN`）设置为{% data variables.product.pat_generic %}。
* 将 `GH_HOST` 设置为服务器的主机名。
* 当同一主机同时存在 `GH_ENTERPRISE_TOKEN` 和 `gh auth login` 凭据时，环境变量优先。

## 推荐的最终用户设置

1. 验证{% data variables.product.prodname_cli %}。

   ```shell
   gh auth login --hostname YOUR-GHES-HOSTNAME
   ```

1. 设置{% data variables.copilot.copilot_cli_short %}所需的环境变量。

   ```shell
   export COPILOT_PROVIDER_GHES_HOST=YOUR-GHES-HOSTNAME
   export COPILOT_PROVIDER_GHES_TOKEN="$(gh auth token --hostname YOUR-GHES-HOSTNAME)"
   export COPILOT_OFFLINE=true
   ```

如果您使用`gh auth login`对多个帐户进行身份验证，则可以将`GH_HOST`设置为服务器的主机名，并将`GH_ENTERPRISE_TOKEN`（或`GITHUB_ENTERPRISE_TOKEN`）设置为`"$(gh auth token --hostname YOUR-GHES-HOSTNAME)"`。这可确保 {% data variables.product.prodname_cli %} 定位您的 {% data variables.product.prodname_ghe_server %} 实例。
   
   ```shell
   export GH_HOST=YOUR-GHES-HOSTNAME
   export GH_ENTERPRISE_TOKEN="$(gh auth token --hostname YOUR-GHES-HOSTNAME)"
   ```

{% data reusables.copilot.copilot-cli.start-cli %}

您可以将整个设置作为脚本运行。

## 示例

如果 {% data variables.product.prodname_ghe_server %} 和您的 {% data variables.copilot.copilot_cli_short %} 配置均正确，那么您应该在 {% data variables.copilot.copilot_cli_short %} 会话中看到类似以下的响应。

```shell
     • fabric-core-mcp — disabled
     • powerbi-mcp — disabled
     • slack — connected

 ● Current model: gpt-5.5

 ❯ Hello                                                                   13:31

 ● Hello!

 ❯ what is going on in github/codeql-action repo?                          13:33

 ● I’ll check recent repository activity on the GHES host: repo metadata, open 
   PRs/issues, and latest commits.

 $ Shell Fetch repo metadata 2 lines…                                         5s
   gh api --hostname "$GH_HOST" repos/github/codeql-action --jq '{name_with_own…
```

## {% data variables.product.prodname_ghe_server %} 支持的功能

有关 {% data variables.copilot.copilot_cli_short %} 功能的最新信息，请参阅 [AUTOTITLE](<https://github.com/enterprise-cloud@latest/copilot/how-tos/copilot-cli>) 作为主要事实来源。一般来说，任何依赖于 {% data variables.product.github %} 云服务连接的功能在 {% data variables.product.prodname_ghe_server %} 离线配置中不可用。

下表提供了 {% data variables.product.prodname_ghe_server %} 产品中可用内容的方向性概述。

|能力| {% data variables.product.prodname_dotcom %} / {% data variables.product.prodname_ghe_cloud %} | {% data variables.product.prodname_ghe_server %} |
|:---|:---:|:---:|
|AI-辅助编码（提示、代码生成、调试）| {% octicon "check" aria-label="Available" %} | {% octicon "check" aria-label="Available" %} |
|Shell命令和文件操作| {% octicon "check" aria-label="Available" %} | {% octicon "check" aria-label="Available" %} |
|{% data variables.product.github %} 通过 `gh` CLI 进行的操作（问题、PR、回购）| {% octicon "check" aria-label="Available" %} |{% octicon "check" aria-label="Available" %}（需要 `gh` CLI 通过实例身份验证）|
|{% data variables.product.github %} MCP 服务器工具| {% octicon "check" aria-label="Available" %} | {% octicon "x" aria-label="Not available" %} |
|网络搜索和网络获取| {% octicon "check" aria-label="Available" %} | {% octicon "x" aria-label="Not available" %} |
|{% data variables.product.prodname_copilot_short %}型号选择（{% data variables.product.github %}托管型号）| {% octicon "check" aria-label="Available" %} | {% octicon "x" aria-label="Not available" %} |
|遥测和使用报告| {% octicon "check" aria-label="Available" %} | {% octicon "x" aria-label="Not available" %} |
|自动更新| {% octicon "check" aria-label="Available" %} | {% octicon "x" aria-label="Not available" %} |


## 原文对照

<details>
<summary>展开完整原文</summary>

> [!NOTE]
> This feature is in {% data variables.release-phases.technical_preview %} and subject to change.

You can configure {% data variables.copilot.copilot_cli_short %} to work with {% data variables.product.prodname_ghe_server %} in disconnected or air-gapped environments without connectivity to {% data variables.product.github %} Cloud. An administrator configures a model provider for the instance, and users connect {% data variables.copilot.copilot_cli_short %} with their {% data variables.product.prodname_ghe_server %} credentials.

Setting up this feature involves two roles:

* **Administrator**: Configures the model provider on the {% data variables.product.prodname_ghe_server %} instance using `ghe-config`. This is a one-time setup that requires administrative SSH access.
* **End user**: Sets environment variables on a local machine to connect {% data variables.copilot.copilot_cli_short %} to the instance.

## Prerequisites

* You have administrative SSH access to the {% data variables.product.prodname_ghe_server %} instance.
* You have an API key from a supported LLM provider.
* {% data variables.copilot.copilot_cli_short %} is installed on client machines. See [AUTOTITLE](<https://github.com/enterprise-cloud@latest/copilot/how-tos/copilot-cli/set-up-copilot-cli/install-copilot-cli>).
* {% data variables.product.prodname_cli %} (`gh`) is installed on client machines. See [{% data variables.product.prodname_cli %} manual](<https://cli.github.com/manual/>) in the {% data variables.product.prodname_cli %} documentation.

For information about supported providers and model requirements, see [AUTOTITLE](<https://github.com/enterprise-cloud@latest/copilot/how-tos/copilot-cli/customize-copilot/use-byok-models>).

## Configuring your {% data variables.product.prodname_ghe_server %} instance

This step is for the operator or administrator of the {% data variables.product.prodname_ghe_server %} instance.

With administrative SSH access to the {% data variables.product.prodname_ghe_server %} instance, configure the model provider using the following `ghe-config` values. After configuring, run `ghe-config-apply` to apply the changes.

| Variable name | Required | Options | Description |
|---|---|---|---|
| `app.copilot-proxy.enabled` | Yes | `true`, `false` | Enables or disables the feature. |
| `app.copilot-proxy.endpoint-url` | Yes | URI | The full upstream base URL including any version prefix (for example, `https://api.openai.com/v1`). |
| `secrets.copilot-proxy.endpoint-key` | Yes | String | The API key for the upstream provider. |
| `app.copilot-proxy.provider-model-id` | Yes | String | The provider model ID that {% data variables.copilot.copilot_cli_short %} uses to look up the model internally. |
| `app.copilot-proxy.provider-type` | Yes | `openai`, `azure`, `anthropic` | The provider type. OpenAI includes OpenAI, Ollama, vLLM, Foundry Local, and any other OpenAI Chat Completions API-compatible endpoint. |
| `app.copilot-proxy.upstream-timeout` | No | Integer, in seconds | Read/send timeout in seconds for upstream requests. If not set, falls back to the default timeout. |
| `app.copilot-proxy.provider-wire-api` | No | `completions`, `responses` | The wire API format for the provider. |
| `app.copilot-proxy.provider-wire-model` | No | String | Overrides the model identifier sent to the upstream provider if it differs from the internal model ID. |
| `app.copilot-proxy.enable-upstream-probe` | No | `true`, `false` | Enables or disables the startup upstream probe. Defaults to enabled. When disabled, the startup probe is skipped. |

For example, the following commands configure an OpenAI provider.

```shell
ghe-config app.copilot-proxy.enabled true
ghe-config app.copilot-proxy.endpoint-url 'https://api.openai.com/v1'
ghe-config secrets.copilot-proxy.endpoint-key 'YOUR-API-KEY'
ghe-config app.copilot-proxy.provider-model-id 'gpt-5.5'
ghe-config app.copilot-proxy.provider-wire-model 'gpt-5.5'
ghe-config app.copilot-proxy.provider-type openai
ghe-config app.copilot-proxy.upstream-timeout 300
ghe-config app.copilot-proxy.enable-upstream-probe false
ghe-config-apply
```

Replace `YOUR-API-KEY` with the real API key before applying the configuration.

## Configuring your {% data variables.copilot.copilot_cli_short %} client (end user)

Configure {% data variables.copilot.copilot_cli_short %} to connect to your {% data variables.product.prodname_ghe_server %} instance by setting the following environment variables before starting {% data variables.copilot.copilot_cli_short %}.

| Environment variable | Required | Description |
|---|---|---|
| `COPILOT_PROVIDER_GHES_HOST` | Yes | The hostname of your {% data variables.product.prodname_ghe_server %} instance. |
| `COPILOT_PROVIDER_GHES_TOKEN` | Yes | A {% data variables.product.pat_generic %} for the {% data variables.product.prodname_ghe_server %} instance. This token authenticates requests to the instance. |
| `COPILOT_OFFLINE` | Yes | Enables offline mode. The {% data variables.product.prodname_ghe_server %} provider is only active when offline mode is enabled. |

## Understanding client (end user) tokens

{% data variables.copilot.copilot_cli_short %} needs access to LLM inference, so `COPILOT_PROVIDER_GHES_TOKEN` is always required. You will also very likely want {% data variables.copilot.copilot_cli_short %} to perform {% data variables.product.github %} operations such as create issues, pull requests, and search repositories. Such operations can be done via the {% data variables.product.prodname_cli %}.

It is recommended and preferred that you run `gh auth login --hostname YOUR-GHES-HOSTNAME`. After it succeeds, next step is to set `COPILOT_PROVIDER_GHES_TOKEN` to the token generated in `gh auth login --hostname YOUR-GHES-HOSTNAME`. It is more secure to retrieve the token dynamically rather than copying it from `~/.config/gh/hosts.yml`. You can do so by using `COPILOT_PROVIDER_GHES_TOKEN="$(gh auth token --hostname YOUR-GHES-HOSTNAME)"`. 

Alternatively, you can generate a {% data variables.product.pat_generic %} on your {% data variables.product.prodname_ghe_server %} instance, set that token as `COPILOT_PROVIDER_GHES_TOKEN`, and use the same token when running `gh auth login --hostname YOUR-GHES-HOSTNAME`.

The above approach works when you are using {% data variables.copilot.copilot_cli_short %} interactively. For automation, you need to do a few things differently:
* Set `GH_ENTERPRISE_TOKEN` (or `GITHUB_ENTERPRISE_TOKEN`) to the {% data variables.product.pat_generic %}.
* Set `GH_HOST` to your server's hostname.
* When both `GH_ENTERPRISE_TOKEN` and `gh auth login` credentials exist for the same host, the environment variable takes precedence.

## Recommended end user setup

1. Authenticate {% data variables.product.prodname_cli %}.

   ```shell
   gh auth login --hostname YOUR-GHES-HOSTNAME
   ```

1. Set the environment variables required by {% data variables.copilot.copilot_cli_short %}.

   ```shell
   export COPILOT_PROVIDER_GHES_HOST=YOUR-GHES-HOSTNAME
   export COPILOT_PROVIDER_GHES_TOKEN="$(gh auth token --hostname YOUR-GHES-HOSTNAME)"
   export COPILOT_OFFLINE=true
   ```

   If you are authenticated with `gh auth login` to multiple accounts, you can set `GH_HOST` to your server's hostname and set `GH_ENTERPRISE_TOKEN` (or `GITHUB_ENTERPRISE_TOKEN`) to `"$(gh auth token --hostname YOUR-GHES-HOSTNAME)"`. This ensures {% data variables.product.prodname_cli %} targets your {% data variables.product.prodname_ghe_server %} instance.
   
   ```shell
   export GH_HOST=YOUR-GHES-HOSTNAME
   export GH_ENTERPRISE_TOKEN="$(gh auth token --hostname YOUR-GHES-HOSTNAME)"
   ```

{% data reusables.copilot.copilot-cli.start-cli %}

You can run this entire set-up as a script.

## Examples

If both {% data variables.product.prodname_ghe_server %} and your {% data variables.copilot.copilot_cli_short %} configurations are correct, then you should see responses like the following in your {% data variables.copilot.copilot_cli_short %} session.

```shell
     • fabric-core-mcp — disabled
     • powerbi-mcp — disabled
     • slack — connected

 ● Current model: gpt-5.5

 ❯ Hello                                                                   13:31

 ● Hello!

 ❯ what is going on in github/codeql-action repo?                          13:33

 ● I’ll check recent repository activity on the GHES host: repo metadata, open 
   PRs/issues, and latest commits.

 $ Shell Fetch repo metadata 2 lines…                                         5s
   gh api --hostname "$GH_HOST" repos/github/codeql-action --jq '{name_with_own…
```

## Supported capabilities on {% data variables.product.prodname_ghe_server %}

For the most up-to-date information on {% data variables.copilot.copilot_cli_short %} features, refer to [AUTOTITLE](<https://github.com/enterprise-cloud@latest/copilot/how-tos/copilot-cli>) as the primary source of truth. In general, any capability that relies on connectivity to {% data variables.product.github %} cloud services is not available in the {% data variables.product.prodname_ghe_server %} offline configuration.

The following table provides a directional overview of what is available in {% data variables.product.prodname_ghe_server %} offering.

| Capability | {% data variables.product.prodname_dotcom %} / {% data variables.product.prodname_ghe_cloud %} | {% data variables.product.prodname_ghe_server %} |
|:---|:---:|:---:|
| AI-assisted coding (prompts, code generation, debugging) | {% octicon "check" aria-label="Available" %} | {% octicon "check" aria-label="Available" %} |
| Shell commands and file operations | {% octicon "check" aria-label="Available" %} | {% octicon "check" aria-label="Available" %} |
| {% data variables.product.github %} operations (issues, PRs, repos) via `gh` CLI | {% octicon "check" aria-label="Available" %} | {% octicon "check" aria-label="Available" %} (requires `gh` CLI authenticated to the instance) |
| {% data variables.product.github %} MCP server tools | {% octicon "check" aria-label="Available" %} | {% octicon "x" aria-label="Not available" %} |
| Web search and web fetch | {% octicon "check" aria-label="Available" %} | {% octicon "x" aria-label="Not available" %} |
| {% data variables.product.prodname_copilot_short %} model selection ({% data variables.product.github %}-hosted models) | {% octicon "check" aria-label="Available" %} | {% octicon "x" aria-label="Not available" %} |
| Telemetry and usage reporting | {% octicon "check" aria-label="Available" %} | {% octicon "x" aria-label="Not available" %} |
| Auto-update | {% octicon "check" aria-label="Available" %} | {% octicon "x" aria-label="Not available" %} |


</details>
