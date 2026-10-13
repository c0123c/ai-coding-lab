# 为 Copilot CLI 接入外部模型

说明自备 API 密钥接入兼容端点、Azure OpenAI 和 Anthropic 的前提与配置，并介绍离线运行；适用能力以保存版本为准。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-10-06](<https://github.com/github/docs/commit/a7c2de673ea756adf470049988144bfe6191c452>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/copilot-cli/customize-copilot/use-byok-models.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/为 Copilot CLI 接入外部模型.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Adding LLM models to GitHub Copilot CLI
shortTitle: Add LLM models
intro: 'Use a model from an external provider of your choice in {% data variables.product.prodname_copilot_short %} by supplying your own API key.'
allowTitleToDifferFromFilename: true
versions:
  fpt: '*'
  ghec: '*'
contentType: how-tos
category:
  - Configure Copilot
  - Configure Copilot CLI
docsTeamMetrics:
  - copilot-cli
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

除了 {% data variables.product.github %} 托管模型之外，您还可以使用 BYOK（自带密钥）将 {% data variables.copilot.copilot_cli_short %} 配置为包括来自您选择的 LLM 提供商的模型。这使您可以连接到 OpenAI 兼容端点、Azure OpenAI 或 Anthropic，包括本地运行的模型，例如 Ollama。

> [！NOTE]
> 本文适用于想要在本地计算机上配置自己的 LLM 提供程序 API 密钥的用户。要为企业中的用户设置自定义模型，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/administer-copilot/manage-for-enterprise/enable-custom-models>)。

## 先决条件

* {% data variables.copilot.copilot_cli_short %} 已安装。参见[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/set-up-copilot-cli/install-copilot-cli>)。
* 您拥有来自受支持的 LLM 提供商的 API 密钥，或者您正在运行本地模型（例如 Ollama）。

## 支持的提供商

{% data variables.copilot.copilot_cli_short %}支持三种提供商类型：

|提供商类型|兼容服务|
|---|---|
| `openai` |OpenAI、Ollama、vLLM、Foundry Local 和任何其他 OpenAI 聊天完成 API- 兼容端点。这是默认的提供者类型。|
| `azure` |Azure OpenAI 服务。|
| `anthropic` |Anthropic（Claude 型号）。|

有关其他示例，请在终端中运行 `copilot help providers`。

## 型号要求

模型必须支持**工具调用**（也称为函数调用）和**流媒体**。如果模型不支持任一功能，{% data variables.copilot.copilot_cli_short %} 将返回错误。为了获得最佳结果，请使用上下文窗口至少包含 128k 个标记的模型。

## 配置您的提供商

您可以在启动 {% data variables.copilot.copilot_cli_short %} 之前通过设置环境变量来配置模型提供程序。

|环境变量|必填|描述|
|---|---|---|
| `COPILOT_PROVIDER_BASE_URL` |是的|模型提供者的 API 端点的基础 URL。|
| `COPILOT_PROVIDER_TYPE` |否|提供程序类型：`openai`（默认）、`azure` 或 `anthropic`。|
| `COPILOT_PROVIDER_API_KEY` |否|您的提供商的 API 密钥。对于不使用身份验证的提供程序（例如本地 Ollama 实例）不需要。|
| `COPILOT_PROVIDER_BEARER_TOKEN` |否|当不使用 API- 密钥身份验证时，用于提供者身份验证的承载令牌。|
| `COPILOT_PROVIDER_WIRE_API` |否|指定与提供商通信时使用的 API 协议。|
| `COPILOT_PROVIDER_AZURE_API_VERSION` |仅限天蓝色|用于请求的 Azure OpenAI API 版本。|
| `COPILOT_PROVIDER_MODEL_ID` |否|用于识别模型功能和令牌限制的众所周知的模型名称。|
| `COPILOT_PROVIDER_WIRE_MODEL` |否|发送给提供者 API 进行推断的模型名称。对于 Azure OpenAI，请使用部署名称。|
| `COPILOT_PROVIDER_MAX_PROMPT_TOKENS` |否|请求中允许的最大提示令牌数。|
| `COPILOT_PROVIDER_MAX_OUTPUT_TOKENS` |否|响应中生成的最大令牌数。|
| `COPILOT_MODEL` |是的|要使用的型号标识符。您还可以使用 `--model` 命令行标志进行设置。|

## 连接到 OpenAI 兼容端点

如果您要连接到 OpenAI、Ollama、vLLM、Foundry Local 或与 OpenAI 聊天完成 API 兼容的任何其他端点，请使用以下步骤。

1. 为您的提供商设置环境变量。例如，对于本地 Ollama 实例：

   ```shell
   export COPILOT_PROVIDER_BASE_URL=http://localhost:11434
   export COPILOT_MODEL=YOUR-MODEL-NAME
   ```

将 `YOUR-MODEL-NAME` 替换为您在 Ollama 中拉取的模型的名称（例如，`llama3.2`）。

1. 对于远程 OpenAI 端点，还要设置您的 API 密钥。

   ```shell
   export COPILOT_PROVIDER_BASE_URL=https://api.openai.com/v1
   export COPILOT_PROVIDER_API_KEY=YOUR-OPENAI-API-KEY
   export COPILOT_MODEL=YOUR-MODEL-NAME
   ```

将 `YOUR-OPENAI-API-KEY` 替换为您的 OpenAI API 密钥，并将 `YOUR-MODEL-NAME` 替换为您要使用的型号（例如，`gpt-4o`）。

{% data reusables.copilot.copilot-cli.start-cli %}

## 连接到 Azure OpenAI

1. 设置 Azure OpenAI 的环境变量。

   ```shell
   export COPILOT_PROVIDER_BASE_URL=https://YOUR-RESOURCE-NAME.openai.azure.com
   export COPILOT_PROVIDER_TYPE=azure
   export COPILOT_PROVIDER_API_KEY=YOUR-AZURE-API-KEY
   export COPILOT_PROVIDER_AZURE_API_VERSION=YOUR-AZURE-API-VERSION
   export COPILOT_PROVIDER_MODEL_ID=YOUR-MODEL-NAME
   export COPILOT_PROVIDER_WIRE_MODEL=YOUR-DEPLOYMENT-NAME
   export COPILOT_MODEL=YOUR-DEPLOYMENT-NAME
   ```

替换以下占位符：

    * `YOUR-RESOURCE-NAME`：您的 Azure OpenAI 资源名称
    * `YOUR-DEPLOYMENT-NAME`：接收请求的部署名称
    * `YOUR-MODEL-NAME`：与部署关联的底层模型（例如，`gpt-4o`）
    * `YOUR-AZURE-API-VERSION`：Azure OpenAI API 版本
    * `YOUR-AZURE-API-KEY`：您的 Azure OpenAI API 密钥

### Azure 特定的环境变量

|变量|描述|
|---|---|
| `COPILOT_PROVIDER_MODEL_ID` |众所周知的型号名称。 {% data variables.copilot.copilot_cli_short %} 使用此值来识别模型功能和令牌限制。|
| `COPILOT_PROVIDER_WIRE_MODEL` |Azure OpenAI 部署名称。 Azure OpenAI 通过部署而不是直接通过模型名称路由请求。|

{% data reusables.copilot.copilot-cli.start-cli %}

## 连接到Anthropic

1. 设置Anthropic的环境变量：

   ```shell
   export COPILOT_PROVIDER_TYPE=anthropic
   export COPILOT_PROVIDER_BASE_URL=https://api.anthropic.com
   export COPILOT_PROVIDER_API_KEY=YOUR-ANTHROPIC-API-KEY
   export COPILOT_MODEL=YOUR-MODEL-NAME
   ```

将 `YOUR-ANTHROPIC-API-KEY` 替换为您的 Anthropic API 密钥，将 YOUR-MODEL-NAME 替换为您要使用的 Claude 型号（例如，`claude-opus-4-5`）。

{% data reusables.copilot.copilot-cli.start-cli %}

## 离线模式下运行

您可以在离线模式下运行{% data variables.copilot.copilot_cli_short %}，以防止其联系{% data variables.product.github %}的服务器。这是专为隔离环境而设计的，其中 CLI 应仅与您的本地或本地模型提供商进行通信。

> [!IMPORTANT]
> 如果您的提供商也在本地或同一隔离环境中，离线模式只能保证完全的网络隔离。如果 `COPILOT_PROVIDER_BASE_URL` 指向远程端点，您的提示和代码上下文仍会通过网络发送到该提供商。

1. 配置您的提供程序环境变量，如配置您的提供程序中所述。

1. 设置离线模式环境变量：

   ```shell
   export COPILOT_OFFLINE=true
   ```

1. {% data reusables.copilot.copilot-cli.start-cli %}


## 原文对照

<details>
<summary>展开完整原文</summary>

You can configure {% data variables.copilot.copilot_cli_short %} to include models from an LLM provider of your choice—using BYOK (Bring Your Own Key)—in addition to the {% data variables.product.github %}-hosted models. This lets you connect to OpenAI-compatible endpoints, Azure OpenAI, or Anthropic, including locally running models such as Ollama.

> [!NOTE]
> This article is for users who want to configure their own LLM provider API key on their local machine. To set up custom models for users in an enterprise, see [AUTOTITLE](<https://github.com/copilot/how-tos/administer-copilot/manage-for-enterprise/enable-custom-models>).

## Prerequisites

* {% data variables.copilot.copilot_cli_short %} is installed. See [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/set-up-copilot-cli/install-copilot-cli>).
* You have an API key from a supported LLM provider, or you have a local model running (such as Ollama).

## Supported providers

{% data variables.copilot.copilot_cli_short %} supports three provider types:

| Provider type | Compatible services |
|---|---|
| `openai` | OpenAI, Ollama, vLLM, Foundry Local, and any other OpenAI Chat Completions API-compatible endpoint. This is the default provider type. |
| `azure` | Azure OpenAI Service. |
| `anthropic` | Anthropic (Claude models). |

For additional examples, run `copilot help providers` in your terminal.

## Model requirements

Models must support **tool calling** (also called function calling) and **streaming**. If a model does not support either capability, {% data variables.copilot.copilot_cli_short %} returns an error. For best results, use a model with a context window of at least 128k tokens.

## Configuring your provider

You configure your model provider by setting environment variables before starting {% data variables.copilot.copilot_cli_short %}.

| Environment variable | Required | Description |
|---|---|---|
| `COPILOT_PROVIDER_BASE_URL` | Yes | The base URL of your model provider's API endpoint. |
| `COPILOT_PROVIDER_TYPE` | No | The provider type: `openai` (default), `azure`, or `anthropic`. |
| `COPILOT_PROVIDER_API_KEY` | No | Your API key for the provider. Not required for providers that do not use authentication, such as a local Ollama instance. |
| `COPILOT_PROVIDER_BEARER_TOKEN` | No | Bearer token used for provider authentication when API-key authentication is not used. |
| `COPILOT_PROVIDER_WIRE_API` | No | Specifies the API protocol used when communicating with the provider. |
| `COPILOT_PROVIDER_AZURE_API_VERSION` | Azure only | The Azure OpenAI API version used for requests. |
| `COPILOT_PROVIDER_MODEL_ID` | No | The well-known model name used to identify model capabilities and token limits. |
| `COPILOT_PROVIDER_WIRE_MODEL` | No | The model name sent to the provider API for inference. For Azure OpenAI, use the deployment name. |
| `COPILOT_PROVIDER_MAX_PROMPT_TOKENS` | No | Maximum number of prompt tokens allowed in a request. |
| `COPILOT_PROVIDER_MAX_OUTPUT_TOKENS` | No | Maximum number of tokens generated in a response. |
| `COPILOT_MODEL` | Yes | The model identifier to use. You can also set this with the `--model` command-line flag. |

## Connecting to an OpenAI-compatible endpoint

Use the following steps if you are connecting to OpenAI, Ollama, vLLM, Foundry Local, or any other endpoint that is compatible with the OpenAI Chat Completions API.

1. Set environment variables for your provider. For example, for a local Ollama instance:

   ```shell
   export COPILOT_PROVIDER_BASE_URL=http://localhost:11434
   export COPILOT_MODEL=YOUR-MODEL-NAME
   ```

   Replace `YOUR-MODEL-NAME` with the name of the model you have pulled in Ollama (for example, `llama3.2`).

1. For a remote OpenAI endpoint, also set your API key.

   ```shell
   export COPILOT_PROVIDER_BASE_URL=https://api.openai.com/v1
   export COPILOT_PROVIDER_API_KEY=YOUR-OPENAI-API-KEY
   export COPILOT_MODEL=YOUR-MODEL-NAME
   ```

   Replace `YOUR-OPENAI-API-KEY` with your OpenAI API key and `YOUR-MODEL-NAME` with the model you want to use (for example, `gpt-4o`).

{% data reusables.copilot.copilot-cli.start-cli %}

## Connecting to Azure OpenAI

1. Set the environment variables for Azure OpenAI.

   ```shell
   export COPILOT_PROVIDER_BASE_URL=https://YOUR-RESOURCE-NAME.openai.azure.com
   export COPILOT_PROVIDER_TYPE=azure
   export COPILOT_PROVIDER_API_KEY=YOUR-AZURE-API-KEY
   export COPILOT_PROVIDER_AZURE_API_VERSION=YOUR-AZURE-API-VERSION
   export COPILOT_PROVIDER_MODEL_ID=YOUR-MODEL-NAME
   export COPILOT_PROVIDER_WIRE_MODEL=YOUR-DEPLOYMENT-NAME
   export COPILOT_MODEL=YOUR-DEPLOYMENT-NAME
   ```

   Replace the following placeholders:

    * `YOUR-RESOURCE-NAME`: your Azure OpenAI resource name
    * `YOUR-DEPLOYMENT-NAME`: the deployment name that receives requests
    * `YOUR-MODEL-NAME`: the underlying model associated with the deployment (for example, `gpt-4o`)
    * `YOUR-AZURE-API-VERSION`: the Azure OpenAI API version
    * `YOUR-AZURE-API-KEY`: your Azure OpenAI API key

### Azure-specific environment variables

| Variable | Description |
|---|---|
| `COPILOT_PROVIDER_MODEL_ID` | The well-known model name. {% data variables.copilot.copilot_cli_short %} uses this value to identify model capabilities and token limits. |
| `COPILOT_PROVIDER_WIRE_MODEL` | The Azure OpenAI deployment name. Azure OpenAI routes requests through deployments rather than directly through model names. |

{% data reusables.copilot.copilot-cli.start-cli %}

## Connecting to Anthropic

1. Set the environment variables for Anthropic:

   ```shell
   export COPILOT_PROVIDER_TYPE=anthropic
   export COPILOT_PROVIDER_BASE_URL=https://api.anthropic.com
   export COPILOT_PROVIDER_API_KEY=YOUR-ANTHROPIC-API-KEY
   export COPILOT_MODEL=YOUR-MODEL-NAME
   ```

   Replace `YOUR-ANTHROPIC-API-KEY` with your Anthropic API key and YOUR-MODEL-NAME with the Claude model you want to use (for example, `claude-opus-4-5`).

{% data reusables.copilot.copilot-cli.start-cli %}

## Running in offline mode

You can run {% data variables.copilot.copilot_cli_short %} in offline mode to prevent it from contacting {% data variables.product.github %}'s servers. This is designed for isolated environments where the CLI should communicate only with your local or on-premises model provider.

> [!IMPORTANT]
> Offline mode only guarantees full network isolation if your provider is also local or within the same isolated environment. If `COPILOT_PROVIDER_BASE_URL` points to a remote endpoint, your prompts and code context are still sent over the network to that provider.

1. Configure your provider environment variables as described in Configuring your provider.

1. Set the offline mode environment variable:

   ```shell
   export COPILOT_OFFLINE=true
   ```

1. {% data reusables.copilot.copilot-cli.start-cli %}


</details>
