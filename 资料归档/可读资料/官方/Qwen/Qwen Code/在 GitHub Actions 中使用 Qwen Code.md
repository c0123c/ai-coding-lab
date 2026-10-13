# 在 GitHub Actions 中使用 Qwen Code

介绍 API 密钥、工作流、Issue 分流、PR 审查和交互分派，列出配置、输出、变量与认证要求。

- 来源：Qwen／Qwen Code
- 资料类型：存档文档
- 存档版本：`7e3de59d3aba`。
- 文档时间：源码提交（存档版）：[2026-09-28](<https://github.com/QwenLM/qwen-code/commit/fc4e01b9fc381952c15592ead9e0cf170754dd8d>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/QwenLM/qwen-code/blob/main/docs/users/integration-github-action.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Qwen/Qwen Code/在 GitHub Actions 中使用 Qwen Code.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# GitHub 动作：qwen-code-action

## 概述

`qwen-code-action` 是一个 GitHub 整合行动 [Qwen Code] 通过以下方式进入您的开发工作流程 [Qwen Code CLI]。它既作为一个自主的 agent 用于关键的日常编码任务，以及您可以快速将工作委派给的按需协作者。

使用它直接在 GitHub 存储库中以对话方式（例如，`@qwencoder fix this issue`）使用 [Qwen Code] 执行 GitHub 拉取请求审查、分类问题、执行代码分析和修改等。

## 特点

- **自动化**：根据事件（例如问题打开）或时间表（例如每晚）触发工作流程。
- **按需协作**：触发问题和拉取请求中的工作流程
通过提及 [Qwen Code CLI](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/commands>)（例如，`@qwencoder /review`）来发表评论。
- **可通过工具扩展**：利用[Qwen Code](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/developers/tools/introduction.md>)模型的工具调用功能与[GitHubCLI](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/`gh`>)等其他CLI进行交互。
- **可自定义**：使用存储库中的`QWEN.md`文件来提供
项目特定说明和上下文 [Qwen Code CLI](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/commands>)。

## 快速入门

只需几分钟即可开始使用存储库中的 Qwen Code CLI：

### 1。获取 Qwen API 钥匙

从【[DashScope](<https://help.aliyun.com/zh/model-studio/qwen-code>)】（阿里云【AI】平台）获取您的【API】密钥

### 2。将其添加为 GitHub 秘密

将您的 API 密钥作为名为 `QWEN_API_KEY` 的机密存储在存储库中：

- Go 到存储库的 **设置 > 秘密和变量 > 操作**
- 单击**新建存储库秘密**
- 名称：`QWEN_API_KEY`，值：您的API密钥

### 3。更新你的 .gitignore

将以下条目添加到您的 `.gitignore` 文件中：

```gitignore
# qwen-code-cli settings
.qwen/

# GitHub App credentials
gha-creds-*.json
```

### 4。选择工作流程

您有两个选项来设置工作流程：

**选项 A：使用设置命令（推荐）**

1. 在终端中启动 Qwen Code CLI：

   ```shell
   qwen
   ```

2. 在终端的 Qwen Code CLI 中，输入：

   ```
   /setup-github
   ```

**选项 B：手动复制工作流程**

1. 将预构建的工作流程从 [`examples/workflows`](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/common-workflow>) 目录复制到存储库的 `.github/workflows` 目录。注意：还必须复制 `qwen-dispatch.yml` 工作流程，这会触发工作流程运行。

### 5。尝试一下

**拉取请求审核：**

- 在存储库中打开拉取请求并等待自动审核
- 对现有拉取请求发表评论 `@qwencoder /review` 以手动触发审核

**问题分类：**

- 打开问题并等待自动分类
- 对现有问题发表评论 `@qwencoder /triage` 以手动触发分类

**一般AI协助：**

- 在任何问题或拉取请求中，请在您的请求后提及 `@qwencoder`
- 示例：
  - `@qwencoder explain this code change`
  - `@qwencoder suggest improvements for this function`
  - `@qwencoder help me debug this error`
  - `@qwencoder write unit tests for this component`

## 工作流程

此操作为不同的用例提供了多个预构建的工作流程。每个工作流程都设计为复制到存储库的 `.github/workflows` 目录中并根据需要进行自定义。

### Qwen Code 调度

此工作流充当 Qwen Code CLI 的中央调度程序，根据触发事件和注释中提供的命令将请求路由到适当的工作流。有关如何设置调度工作流程的详细指南，请访问 [Qwen Code 调度工作流程文档 ](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/common-workflow>)。

### 问题分类

此操作可用于自动或按需分类 GitHub 问题。有关有效的问题分类设置，请参阅 [Qwen 分类工作流程 ](<https://github.com/QwenLM/qwen-code/blob/main/.github/workflows/qwen-triage.yml>)。

### 拉取请求审查

此操作可用于在拉取请求打开时自动审核它们。有关如何设置拉取请求审核系统的详细指南，请转到 [GitHub PR 审核工作流程文档 ](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/common-workflow>)。

### Qwen Code CLI 助理

这种类型的操作可用于在拉取请求和问题中调用通用的会话式 Qwen Code AI 助手来执行各种任务。有关如何设置通用 Qwen Code CLI 工作流程的详细指南，请转到 [Qwen Code 助理工作流程文档 ](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/common-workflow>)。

## 配置

### 输入

<!-- BEGIN_AUTOGEN_INPUTS -->

- <a name="__input_qwen_api_key"></a><a href="#user-content-__input_qwen_api_key"><code>qwen*api_key</code></a>：*（可选）\_ Qwen API 的 API 键。

- <a name="__input_qwen_cli_version"></a><a href="#user-content-__input_qwen_cli_version"><code>qwen*cli_version</code></a>：*（可选，默认：`latest`）\_ 要安装的Qwen Code CLI的版本。可以是“最新”、“预览”、“每晚”、特定版本号或 git 分支、标签或提交。有关更多信息，请参阅 [Qwen Code CLI 版本 ](<https://github.com/QwenLM/qwen-code/releases>)。

- <a name="__input_qwen_debug"></a><a href="#user-content-__input_qwen_debug"><code>qwen*debug</code></a>：*（可选）\_ 启用调试日志记录和输出流。

- <a name="__input_qwen_model"></a><a href="#user-content-__input_qwen_model"><code>qwen*model</code></a>: *（可选）\_ 与 Qwen Code 一起使用的型号。

- <a name="__input_prompt"></a><a href="#user-content-__input_prompt"><code>prompt</code></a>：_（可选，默认：`You are a helpful assistant.`）_ 传递给Qwen Code CLI的[`--prompt`参数](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/configuration/settings.md#command-line-arguments-table>)的字符串。

- <a name="__input_settings"></a><a href="#user-content-__input_settings"><code>settings</code></a>：_（可选）_ 写入`.qwen/settings.json`的JSON字符串，用于配置CLI的_项目_设置。
有关更多详细信息，请参阅 [ 设置文件 ](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/configuration/settings.md#settings-files>) 的文档。

- <a name="__input_use_qwen_code_assist"></a><a href="#user-content-__input_use_qwen_code_assist"><code>use*qwen_code_assist</code></a>：*（可选，默认：`false`）\_是否使用代码辅助进行Qwen Code模型访问，而不是默认的Qwen CodeAPI键。
有关更多信息，请参阅 [Qwen Code CLI 文档 ](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/configuration/auth.md>)。

- <a name="__input_use_vertex_ai"></a><a href="#user-content-__input_use_vertex_ai"><code>use*vertex_ai</code></a>：*（可选，默认：`false`）\_是否使用Vertex AI进行Qwen Code模型访问而不是默认的Qwen Code API关键。
有关更多信息，请参阅 [Qwen Code CLI 文档 ](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/configuration/auth.md>)。

- <a name="__input_extensions"></a><a href="#user-content-__input_extensions"><code>extensions</code></a>：_（可选）_ 要安装的 Qwen Code CLI 扩展列表。

- <a name="__input_upload_artifacts"></a><a href="#user-content-__input_upload_artifacts"><code>upload*artifacts</code></a>：*（可选，默认：`false`）\_是否将工件上传到github操作。

- <a name="__input_use_pnpm"></a><a href="#user-content-__input_use_pnpm"><code>use*pnpm</code></a>：*（可选，默认：`false`）\_是否使用pnpm而不是npm来安装qwen-code-cli

- <a name="__input_workflow_name"></a><a href="#user-content-__input_workflow_name"><code>workflow*name</code></a>：*（可选，默认：`${{ github.workflow }}`）\_ GitHub工作流程名称，用于遥测目的。

<!-- END_AUTOGEN_INPUTS -->

### 输出

<!-- BEGIN_AUTOGEN_OUTPUTS -->

- <a name="__output_summary"></a><a href="#user-content-__output_summary"><code>summary</code></a>：Qwen Code CLI 执行的汇总输出。

- <a name="__output_error"></a><a href="#user-content-__output_error"><code>error</code></a>：Qwen Code CLI 执行的错误输出（如果有）。

<!-- END_AUTOGEN_OUTPUTS -->

### 存储库变量

我们建议将以下值设置为存储库变量，以便可以在所有工作流程中重复使用它们。或者，您可以将它们内联设置为单个工作流程中的操作输入或覆盖存储库级别的值。

|名称|描述|类型|必填|需要时|
| ------------------ | --------------------------------------------------------- | -------- | -------- | ------------------------- |
| `DEBUG`            |启用 Qwen Code CLI 的调试日志记录。|变量|否|从来没有|
| `QWEN_CLI_VERSION` |控制安装 Qwen Code CLI 的版本。|变量|否|固定 CLI 版本|
| `APP_ID`           |GitHub 用于自定义身份验证的应用程序 ID。|变量|否|使用自定义 GitHub 应用程序|

添加存储库变量：

1. Go 到存储库的 ** 设置 > 秘密和变量 > 操作 > 新变量 **。
2. 输入变量名称和值。
3. 保存。

有关存储库变量的详细信息，请参阅 [GitHub documentation on variables][变量].

### 秘密

您可以在存储库中设置以下机密：

|名称|描述|必填|需要时|
| ----------------- | --------------------------------------------- | -------- | ------------------------------------------ |
| `QWEN_API_KEY`    |来自 DashScope 的 Qwen API 密钥。|是的|对于调用 Qwen 的所有工作流程都是必需的。|
| `APP_PRIVATE_KEY` |您的 GitHub 应用程序的私钥（PEM 格式）。|否|使用自定义 GitHub 应用程序。|

添加秘密：

1. Go 到存储库的 ** 设置 > 秘密和变量 > 操作 > 新存储库秘密 **。
2. 输入秘密名称和值。
3. 保存。

欲了解更多信息，请参阅 [官方 GitHub 有关创建和使用加密机密的文档][秘密].

## 认证

此操作需要对 GitHub API 进行身份验证，也可以选择对 Qwen Code 服务进行身份验证。

### GitHub 认证

您可以通过两种方式向GitHub进行身份验证：

1. **默认 `GITHUB_TOKEN`:** 对于更简单的用例，该操作可以使用
工作流程提供的默认 `GITHUB_TOKEN`。
2. **自定义GitHub应用程序（推荐）：**最安全、最灵活
身份验证，我们建议创建自定义 GitHub 应用程序。

有关 Qwen 和 GitHub 身份验证的详细设置说明，请转至 
[** 身份验证文档 **](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/configuration/auth>)。

## 扩展

Qwen Code CLI 可以通过扩展来扩展附加功能。
这些扩展是从其 GitHub 存储库的源代码安装的。

有关如何设置和配置扩展的详细说明，请访问
[扩展文档](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/extension/introduction.md>)。

## 最佳实践

为了确保自动化工作流程的安全性、可靠性和效率，我们强烈建议遵循我们的最佳实践。这些指南涵盖了存储库安全、工作流程配置和监控等关键领域。

主要建议包括：

- **保护您的存储库：**实施分支和标签保护，并限制拉取请求审批者。
- **监控和审核：**定期检查操作日志并启用OpenTelemetry以更深入地了解性能和行为。

有关保护存储库和工作流程的综合指南，请参阅我们的 [** 最佳实践文档 **](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/common-workflow>)。

## 定制化

创建一个 QWEN存储库根目录中的 .md 文件以提供
项目特定的背景和说明 [Qwen Code CLI](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/common-workflow>)。这对于定义很有用
编码约定、架构模式或模型应遵循的其他准则
遵循给定的存储库。

## 贡献

欢迎贡献！查看 Qwen Code CLI **贡献指南 ** 了解有关如何开始的更多详细信息。

[secrets]: <https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions>
[Qwen Code]: <https://github.com/QwenLM/qwen-code>
[DashScope]: <https://dashscope.console.aliyun.com/apiKey>
[Qwen Code CLI]: <https://github.com/QwenLM/qwen-code-action/>
[variables]: <https://docs.github.com/en/actions/how-tos/write-workflows/choose-what-workflows-do/use-variables#creating-configuration-variables-for-a-repository>
[GitHub CLI]: <https://docs.github.com/en/github-cli/github-cli>
[QWEN.md]: <https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/configuration/settings.md#context-files-hierarchical-instructional-context>


## 原文对照

<details>
<summary>展开完整原文</summary>

# GitHub Actions：qwen-code-action

## Overview

`qwen-code-action` is a GitHub Action that integrates [Qwen Code] into your development workflow via the [Qwen Code CLI]. It acts both as an autonomous agent for critical routine coding tasks, and an on-demand collaborator you can quickly delegate work to.

Use it to perform GitHub pull request reviews, triage issues, perform code analysis and modification, and more using [Qwen Code] conversationally (e.g., `@qwencoder fix this issue`) directly inside your GitHub repositories.

## Features

- **Automation**: Trigger workflows based on events (e.g. issue opening) or schedules (e.g. nightly).
- **On-demand Collaboration**: Trigger workflows in issue and pull request
  comments by mentioning the [Qwen Code CLI](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/commands>) (e.g., `@qwencoder /review`).
- **Extensible with Tools**: Leverage [Qwen Code](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/developers/tools/introduction.md>) models' tool-calling capabilities to interact with other CLIs like the [GitHub CLI] (`gh`).
- **Customizable**: Use a `QWEN.md` file in your repository to provide
  project-specific instructions and context to [Qwen Code CLI](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/commands>).

## Quick Start

Get started with Qwen Code CLI in your repository in just a few minutes:

### 1. Get a Qwen API Key

Obtain your API key from [DashScope](<https://help.aliyun.com/zh/model-studio/qwen-code>) (Alibaba Cloud's AI platform)

### 2. Add it as a GitHub Secret

Store your API key as a secret named `QWEN_API_KEY` in your repository:

- Go to your repository's **Settings > Secrets and variables > Actions**
- Click **New repository secret**
- Name: `QWEN_API_KEY`, Value: your API key

### 3. Update your .gitignore

Add the following entries to your `.gitignore` file:

```gitignore
# qwen-code-cli settings
.qwen/

# GitHub App credentials
gha-creds-*.json
```

### 4. Choose a Workflow

You have two options to set up a workflow:

**Option A: Use setup command (Recommended)**

1. Start the Qwen Code CLI in your terminal:

   ```shell
   qwen
   ```

2. In Qwen Code CLI in your terminal, type:

   ```
   /setup-github
   ```

**Option B: Manually copy workflows**

1. Copy the pre-built workflows from the [`examples/workflows`](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/common-workflow>) directory to your repository's `.github/workflows` directory. Note: the `qwen-dispatch.yml` workflow must also be copied, which triggers the workflows to run.

### 5. Try it out

**Pull Request Review:**

- Open a pull request in your repository and wait for automatic review
- Comment `@qwencoder /review` on an existing pull request to manually trigger a review

**Issue Triage:**

- Open an issue and wait for automatic triage
- Comment `@qwencoder /triage` on existing issues to manually trigger triaging

**General AI Assistance:**

- In any issue or pull request, mention `@qwencoder` followed by your request
- Examples:
  - `@qwencoder explain this code change`
  - `@qwencoder suggest improvements for this function`
  - `@qwencoder help me debug this error`
  - `@qwencoder write unit tests for this component`

## Workflows

This action provides several pre-built workflows for different use cases. Each workflow is designed to be copied into your repository's `.github/workflows` directory and customized as needed.

### Qwen Code Dispatch

This workflow acts as a central dispatcher for Qwen Code CLI, routing requests to the appropriate workflow based on the triggering event and the command provided in the comment. For a detailed guide on how to set up the dispatch workflow, go to the [Qwen Code Dispatch workflow documentation](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/common-workflow>).

### Issue Triage

This action can be used to triage GitHub Issues automatically or on demand. For a working issue triage setup, see the [Qwen triage workflow](<https://github.com/QwenLM/qwen-code/blob/main/.github/workflows/qwen-triage.yml>).

### Pull Request Review

This action can be used to automatically review pull requests when they are opened. For a detailed guide on how to set up the pull request review system, go to the [GitHub PR Review workflow documentation](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/common-workflow>).

### Qwen Code CLI Assistant

This type of action can be used to invoke a general-purpose, conversational Qwen Code AI assistant within the pull requests and issues to perform a wide range of tasks. For a detailed guide on how to set up the general-purpose Qwen Code CLI workflow, go to the [Qwen Code Assistant workflow documentation](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/common-workflow>).

## Configuration

### Inputs

<!-- BEGIN_AUTOGEN_INPUTS -->

- <a name="__input_qwen_api_key"></a><a href="#user-content-__input_qwen_api_key"><code>qwen*api_key</code></a>: *(Optional)\_ The API key for the Qwen API.

- <a name="__input_qwen_cli_version"></a><a href="#user-content-__input_qwen_cli_version"><code>qwen*cli_version</code></a>: *(Optional, default: `latest`)\_ The version of the Qwen Code CLI to install. Can be "latest", "preview", "nightly", a specific version number, or a git branch, tag, or commit. For more information, see [Qwen Code CLI releases](<https://github.com/QwenLM/qwen-code/releases>).

- <a name="__input_qwen_debug"></a><a href="#user-content-__input_qwen_debug"><code>qwen*debug</code></a>: *(Optional)\_ Enable debug logging and output streaming.

- <a name="__input_qwen_model"></a><a href="#user-content-__input_qwen_model"><code>qwen*model</code></a>: *(Optional)\_ The model to use with Qwen Code.

- <a name="__input_prompt"></a><a href="#user-content-__input_prompt"><code>prompt</code></a>: _(Optional, default: `You are a helpful assistant.`)_ A string passed to the Qwen Code CLI's [`--prompt` argument](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/configuration/settings.md#command-line-arguments-table>).

- <a name="__input_settings"></a><a href="#user-content-__input_settings"><code>settings</code></a>: _(Optional)_ A JSON string written to `.qwen/settings.json` to configure the CLI's _project_ settings.
  For more details, see the documentation on [settings files](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/configuration/settings.md#settings-files>).

- <a name="__input_use_qwen_code_assist"></a><a href="#user-content-__input_use_qwen_code_assist"><code>use*qwen_code_assist</code></a>: *(Optional, default: `false`)\_ Whether to use Code Assist for Qwen Code model access instead of the default Qwen Code API key.
  For more information, see the [Qwen Code CLI documentation](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/configuration/auth.md>).

- <a name="__input_use_vertex_ai"></a><a href="#user-content-__input_use_vertex_ai"><code>use*vertex_ai</code></a>: *(Optional, default: `false`)\_ Whether to use Vertex AI for Qwen Code model access instead of the default Qwen Code API key.
  For more information, see the [Qwen Code CLI documentation](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/configuration/auth.md>).

- <a name="__input_extensions"></a><a href="#user-content-__input_extensions"><code>extensions</code></a>: _(Optional)_ A list of Qwen Code CLI extensions to install.

- <a name="__input_upload_artifacts"></a><a href="#user-content-__input_upload_artifacts"><code>upload*artifacts</code></a>: *(Optional, default: `false`)\_ Whether to upload artifacts to the github action.

- <a name="__input_use_pnpm"></a><a href="#user-content-__input_use_pnpm"><code>use*pnpm</code></a>: *(Optional, default: `false`)\_ Whether or not to use pnpm instead of npm to install qwen-code-cli

- <a name="__input_workflow_name"></a><a href="#user-content-__input_workflow_name"><code>workflow*name</code></a>: *(Optional, default: `${{ github.workflow }}`)\_ The GitHub workflow name, used for telemetry purposes.

<!-- END_AUTOGEN_INPUTS -->

### Outputs

<!-- BEGIN_AUTOGEN_OUTPUTS -->

- <a name="__output_summary"></a><a href="#user-content-__output_summary"><code>summary</code></a>: The summarized output from the Qwen Code CLI execution.

- <a name="__output_error"></a><a href="#user-content-__output_error"><code>error</code></a>: The error output from the Qwen Code CLI execution, if any.

<!-- END_AUTOGEN_OUTPUTS -->

### Repository Variables

We recommend setting the following values as repository variables so they can be reused across all workflows. Alternatively, you can set them inline as action inputs in individual workflows or to override repository-level values.

| Name               | Description                                               | Type     | Required | When Required             |
| ------------------ | --------------------------------------------------------- | -------- | -------- | ------------------------- |
| `DEBUG`            | Enables debug logging for the Qwen Code CLI.              | Variable | No       | Never                     |
| `QWEN_CLI_VERSION` | Controls which version of the Qwen Code CLI is installed. | Variable | No       | Pinning the CLI version   |
| `APP_ID`           | GitHub App ID for custom authentication.                  | Variable | No       | Using a custom GitHub App |

To add a repository variable:

1. Go to your repository's **Settings > Secrets and variables > Actions > New variable**.
2. Enter the variable name and value.
3. Save.

For details about repository variables, refer to the [GitHub documentation on variables][variables].

### Secrets

You can set the following secrets in your repository:

| Name              | Description                                   | Required | When Required                              |
| ----------------- | --------------------------------------------- | -------- | ------------------------------------------ |
| `QWEN_API_KEY`    | Your Qwen API key from DashScope.             | Yes      | Required for all workflows that call Qwen. |
| `APP_PRIVATE_KEY` | Private key for your GitHub App (PEM format). | No       | Using a custom GitHub App.                 |

To add a secret:

1. Go to your repository's **Settings > Secrets and variables >Actions > New repository secret**.
2. Enter the secret name and value.
3. Save.

For more information, refer to the [official GitHub documentation on creating and using encrypted secrets][secrets].

## Authentication

This action requires authentication to the GitHub API and optionally to Qwen Code services.

### GitHub Authentication

You can authenticate with GitHub in two ways:

1. **Default `GITHUB_TOKEN`:** For simpler use cases, the action can use the
   default `GITHUB_TOKEN` provided by the workflow.
2. **Custom GitHub App (Recommended):** For the most secure and flexible
   authentication, we recommend creating a custom GitHub App.

For detailed setup instructions for both Qwen and GitHub authentication, go to the
[**Authentication documentation**](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/configuration/auth>).

## Extensions

The Qwen Code CLI can be extended with additional functionality through extensions.
These extensions are installed from source from their GitHub repositories.

For detailed instructions on how to set up and configure extensions, go to the
[Extensions documentation](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/extension/introduction.md>).

## Best Practices

To ensure the security, reliability, and efficiency of your automated workflows, we strongly recommend following our best practices. These guidelines cover key areas such as repository security, workflow configuration, and monitoring.

Key recommendations include:

- **Securing Your Repository:** Implementing branch and tag protection, and restricting pull request approvers.
- **Monitoring and Auditing:** Regularly reviewing action logs and enabling OpenTelemetry for deeper insights into performance and behavior.

For a comprehensive guide on securing your repository and workflows, please refer to our [**Best Practices documentation**](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/common-workflow>).

## Customization

Create a QWEN.md file in the root of your repository to provide
project-specific context and instructions to [Qwen Code CLI](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/common-workflow>). This is useful for defining
coding conventions, architectural patterns, or other guidelines the model should
follow for a given repository.

## Contributing

Contributions are welcome! Check out the Qwen Code CLI **Contributing Guide** for more details on how to get started.

[secrets]: <https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions>
[Qwen Code]: <https://github.com/QwenLM/qwen-code>
[DashScope]: <https://dashscope.console.aliyun.com/apiKey>
[Qwen Code CLI]: <https://github.com/QwenLM/qwen-code-action/>
[variables]: <https://docs.github.com/en/actions/how-tos/write-workflows/choose-what-workflows-do/use-variables#creating-configuration-variables-for-a-repository>
[GitHub CLI]: <https://docs.github.com/en/github-cli/github-cli>
[QWEN.md]: <https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/configuration/settings.md#context-files-hierarchical-instructional-context>


</details>
