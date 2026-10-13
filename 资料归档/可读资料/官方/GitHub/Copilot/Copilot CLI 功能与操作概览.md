# Copilot CLI 功能与操作概览

介绍交互和程序调用模式、本地或云端沙箱、上下文管理、定制、工具权限和常见终端开发任务。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-10-07](<https://github.com/github/docs/commit/03b432e6d532db63f40312771e3ba8212d1995c0>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/concepts/copilot-surfaces/copilot-cli.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/Copilot CLI 功能与操作概览.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: GitHub Copilot CLI
shortTitle: Copilot CLI
allowTitleToDifferFromFilename: true
intro: 'Find out about using {% data variables.product.prodname_copilot_short %} from the command line.'
product: '{% data reusables.gated-features.copilot-cli %}'
versions:
  feature: copilot
contentType: concepts
category:
  - Learn about Copilot # Copilot discovery page
  - Learn about Copilot CLI # Copilot CLI bespoke page
redirect_from:
  - /copilot/concepts/agents/about-copilot-cli
  - /copilot/concepts/agents/copilot-cli/about-copilot-cli
  - /copilot/concepts/copilot-surfaces/about-copilot-cli
docsTeamMetrics:
  - copilot-cli
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

## 简介

{% data variables.product.prodname_copilot %} 的命令行界面 (CLI) 允许您直接从终端使用 {% data variables.product.prodname_copilot_short %}。您可以用它来回答问题、编写和调试代码以及​​与 {% data variables.product.prodname_dotcom_the_website %} 交互。例如，您可以要求 {% data variables.product.prodname_copilot_short %} 对项目进行一些更改并创建拉取请求。

{% data variables.copilot.copilot_cli %}让您无需离开终端即可快速访问强大的AI agent。它可以代表您工作，帮助您更快地完成任务，并且您可以与 {% data variables.copilot.copilot_cli %} 迭代工作来构建您需要的代码。

## 支持的操作系统

* Linux
* macOS
* Powershell 中的 Windows 和 [适用于 Linux 的 Windows 子系统 (WSL)](<https://learn.microsoft.com/en-us/windows/wsl/about>)

有关安装说明，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/set-up-copilot-cli/install-copilot-cli>)。

## 使用方式

{% data variables.copilot.copilot_cli %} 有两个用户界面：交互式和编程式。

### 交互界面

要启动交互式会话，请输入 `copilot`。在交互式会话中，您可以与{% data variables.product.prodname_copilot_short %}进行对话。您可以提示{% data variables.product.prodname_copilot_short %}执行一项或多项任务，并且可以为其提供反馈并引导工作方向。

![{% data variables.product.prodname_copilot_short %}交互界面欢迎信息截图。](<https://github.com/assets/images/help/copilot/copilot-cli-welcome.png>)

交互界面有两种模式。除了默认的ask/execute模式之外，还有一种**计划模式**，其中{% data variables.product.prodname_copilot_short %}将为您想要完成的任务构建结构化的实施计划。

按 <kbd>Shift</kbd>+<kbd>Tab</kbd> 在模式之间循环。在计划模式下，{% data variables.product.prodname_copilot_short %} 分析您的请求，提出澄清问题以了解范围和要求，并在编写任何代码之前制定计划。这可以帮助您在编写任何代码之前发现误解，并保持对复杂的多步骤任务的控制。

### 编程接口

您还可以直接在命令行上传递 CLI 单个提示符。 CLI完成任务然后退出。

要以编程方式使用 CLI，请在命令中包含 `-p` 或 `--prompt` 命令行选项。要允许 {% data variables.product.prodname_copilot_short %} 修改和执行文件，您还应该使用本文后面描述的批准选项之一 - 请参阅 [允许在没有手动批准的情况下使用工具 ](<#允许无需手动批准即可使用工具>) ）。例如：

  ```bash copy
  copilot -p "Show me this week's commits and summarize them" --allow-tool='shell(git)'
  ```

或者，您可以使用脚本输出命令行选项并将其通过管道传输到 `copilot`。例如：

  ```bash copy
  ./script-outputting-options.sh | copilot
  ```

> [!CAUTION]
> 如果您使用自动批准选项，例如 `--allow-all-tools`，则 {% data variables.product.prodname_copilot_short %} 与您对计算机上的文件具有相同的访问权限，并且可以运行您可以运行的任何 shell 命令，而无需事先获得您的批准。请参阅本文后面的 [安全注意事项 ](<#安全考虑>)。

## 在沙箱中运行 {% data variables.copilot.copilot_cli_short %} 命令

为了防止 {% data variables.product.prodname_copilot_short %} 修改您不希望它触及的文件，您可以强制 agents 在本地沙盒环境中运行命令。或者，您可以在云中的隔离环境中运行整个 CLI 会话。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/concepts/about-cloud-and-local-sandboxes>)。

### 本地沙箱

您可以在 {% data variables.copilot.copilot_cli_short %} 会话内启用本地沙箱，以限制 {% data variables.product.prodname_copilot_short %} 对您的文件系统、网络和系统功能的访问。要启用它，请在会话中运行 `/sandbox enable`。

### 云沙箱

> [!NOTE]
> {% data reusables.cli.public-preview-cloud-sandbox %}

您可以在具有云沙箱的隔离的云托管环境中启动 {% data variables.copilot.copilot_cli_short %} 会话。当您想要运行代码而不影响本地计算机、在使用之间保持会话状态、从不同计算机继续会话或并行运行多个任务时，这非常有用。云沙箱策略继承自 {% data variables.copilot.copilot_cloud_agent %} 策略，因此防火墙规则等现有安全控制可扩展到云沙箱，​​无需额外设置。

要启动云支持的会话，请运行：

```bash copy
copilot --cloud
```

## {% data variables.copilot.copilot_cli %} 的用例

以下部分提供了您可以使用 {% data variables.copilot.copilot_cli %} 完成的任务示例。

### 本地任务

* 在项目目录中，您可以要求 {% data variables.product.prodname_copilot_short %} 更改项目中的代码。例如：

  `Change the background-color of H1 headings to dark blue`

{% data variables.product.prodname_copilot_short %} 查找定义了 H1 标题的 CSS 文件并更改颜色值。

* 请 {% data variables.product.prodname_copilot_short %} 告诉您有关文件的更改：

  `Show me the last 5 changes made to the CHANGELOG.md file. Who changed the file, when, and give a brief summary of the changes they made`

* 使用 {% data variables.product.prodname_copilot_short %} 帮助您改进项目中的代码或文档。

  * `Suggest improvements to content.js`

  * `Rewrite the readme in this project to make it more accessible to newcomers`

* 使用{% data variables.product.prodname_copilot_short %}帮助您执行Git操作。

  * `Commit the changes to this repo`

  * `Revert the last commit, leaving the changes unstaged`

* 要求 {% data variables.product.prodname_copilot_short %} 从头开始创建一个应用程序 - 例如，作为概念证明。

  `Use the create-next-app kit and tailwind CSS to create a next.js app. The app should be a dashboard built with data from the GitHub API. It should track this project's build success rate, average build duration, number of failed builds, and automated test pass rate. After creating the app, give me easy to follow instructions on how to build, run, and view the app in my browser.`

* 要求 {% data variables.product.prodname_copilot_short %} 解释为什么它所做的更改没有按预期工作，或者告诉 {% data variables.product.prodname_copilot_short %} 修复上次所做更改的问题。例如：

  `You said: "The application is now running on http://localhost:3002 and is fully functional!" but when I browse to that URL I get "This site can't be reached"`

### 电脑使用

在 macOS 和 Windows 上的本地会话中，您可以启用计算机使用，让 {% data variables.product.prodname_copilot_short %} 与桌面应用程序交互。此功能对于不提供 API、命令行界面或 MCP 集成的旧版软件和仅 GUI- 的软件中的工作流程非常有用。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/concepts/agents/computer-use>)。

### 涉及{% data variables.product.prodname_dotcom_the_website %}的任务

* 从 {% data variables.product.prodname_dotcom_the_website %} 获取并显示有关您的作品的详细信息。

  * `List my open PRs`

这列出了来自 {% data variables.product.github %} 上任何存储库的开放拉取请求。要获得更具体的结果，请在提示中包含存储库名称：

  * `List all open issues assigned to me in OWNER/REPO`

* 请求 {% data variables.product.prodname_copilot_short %} 解决问题：

  `I've been assigned this issue: https://github.com/octo-org/octo-repo/issues/1234. Start working on this for me in a suitably named branch.`

* 要求 {% data variables.product.prodname_copilot_short %} 进行文件更改并对 {% data variables.product.prodname_dotcom_the_website %} 提出拉取请求。

  * `In the root of this repo, add a Node script called user-info.js that outputs information about the user who ran the script. Create a pull request to add this file to the repo on {% data variables.product.github %}.`

  * `Create a PR that updates the README at https://github.com/octo-org/octo-repo, changing the subheading "How to run" to "Example usage"`

Copilot 代表您在 {% data variables.product.prodname_dotcom_the_website %} 上创建拉取请求。您被标记为拉取请求作者。

* 请 {% data variables.product.prodname_copilot_short %} 在 {% data variables.product.prodname_dotcom_the_website %} 上为您创建问题。

  ``Raise an improvement issue in octo-org/octo-repo. In src/someapp/somefile.py the `file = open('data.txt', 'r')` block opens a file but never closes it.``

* 请求 {% data variables.product.prodname_copilot_short %} 检查拉取请求中的代码更改。

  `Check the changes made in PR https://github.com/octo-org/octo-repo/pull/57575. Report any serious errors you find in these changes.`

Copilot 在 CLI 中回复并总结了它发现的任何问题。

* 管理来自 {% data variables.copilot.copilot_cli %} 的拉取请求。

  * `Merge all of the open PRs that I've created in octo-org/octo-repo`

  * `Close PR #11 on octo-org/octo-repo`

* 查找特定类型的问题。

  `Use the {% data variables.product.github %} MCP server to find good first issues for a new team member to work on from octo-org/octo-repo`

  > [！NOTE]
  > 如果您知道特定的 MCP 服务器可以实现特定任务，那么在提示中指定它可以帮助 {% data variables.product.prodname_copilot_short %} 提供您想要的结果。

* 查找特定的 {% data variables.product.prodname_actions %} 工作流程。

  `List any Actions workflows in this repo that add comments to PRs`

* 创建 {% data variables.product.prodname_actions %} 工作流程。

  `Branch off from main and create a {% data variables.product.prodname_actions %} workflow that will run on pull requests, or can be run manually. The workflow should run eslint to check for problems in the changes made in the PR. If warnings or errors are found these should be shown as messages in the diff view of the PR. I want to prevent code with errors from being merged into main so, if any errors are found, the workflow should cause the PR check to fail. Push the new branch and create a pull request.`

## 引导对话

当 {% data variables.product.prodname_copilot_short %} 思考引导对话时，你可以与它互动：

* **将附加消息排队**：发送后续消息以将对话引向不同的方向，或将附加指令排队以供{% data variables.product.prodname_copilot_short %}在完成当前响应后进行处理。这会让对话感觉更加自然，让您掌控一切。
* **关于拒绝的内联反馈**：当您拒绝工具权限请求时，您可以向{% data variables.product.prodname_copilot_short %}提供有关拒绝的内联反馈，以便它可以调整其方法而无需完全停止。当您想要引导 {% data variables.product.prodname_copilot_short %} 远离某些操作时，这会使对话更加自然。

## 自动上下文管理

{% data variables.copilot.copilot_cli %} 自动管理您的对话上下文：

* **自动压缩**：当您的对话接近令牌限制95%时，{% data variables.product.prodname_copilot_short %}会自动在后台压缩您的历史记录，而不会中断您的工作流程。这使得几乎无限的会话成为可能。
* **手动控制**：使用`/compact`随时手动压缩上下文。如果您改变主意，请按 <kbd>Esc</kbd> 取消。
* **可视化使用情况**：`/context`命令显示详细的令牌使用情况细分，以便您可以了解上下文窗口的使用方式。

## 定制{% data variables.copilot.copilot_cli %}

您可以通过多种方式自定义 {% data variables.copilot.copilot_cli %}：

* **自定义说明**：自定义说明允许您为项目提供{% data variables.product.prodname_copilot_short %}附加上下文以及如何构建、测试和验证其更改。现在，所有自定义指令文件都会合并，而不是使用基于优先级的后备。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/customize-copilot/add-custom-instructions>)。
* **Model Context Protocol (MCP) 服务器**：MCP 服务器允许您向 {% data variables.product.prodname_copilot_short %} 授予对不同数据源和工具的访问权限。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/use-copilot-cli/overview#add-an-mcp-server>)。
* **{% data variables.copilot.custom_agents_caps_short %}**：{% data variables.copilot.custom_agents_caps_short %}允许您为不同的任务创建不同的专用版本的{% data variables.product.prodname_copilot_short %}。例如，您可以按照团队的指南将 {% data variables.product.prodname_copilot_short %} 定制为专家前端工程师。 {% data variables.copilot.copilot_cli %} 包括专门的 {% data variables.copilot.custom_agents_short %}，它会自动将常见任务委托给它。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/use-copilot-cli/overview#use-custom-agents>)。
* **Hooks**：Hooks 允许您在 agent 执行期间的关键点执行自定义 shell 命令，使您能够添加验证、日志记录、安全扫描或工作流程自动化。参见[AUTOTITLE](<https://github.com/copilot/concepts/agents/hooks>)。
* **技能**：技能可以让你增强{% data variables.product.prodname_copilot_short %}使用指令、脚本和资源执行专门任务的能力。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/concepts/agents/about-agent-skills>)。
* **{% data variables.copilot.copilot_memory %}**：{% data variables.copilot.copilot_memory %} 允许 {% data variables.product.prodname_copilot_short %} 通过存储“记忆”来建立对存储库的持久理解，这些“记忆”是 {% data variables.product.prodname_copilot_short %} 在工作时推断出的有关编码约定、模式和偏好的信息。这减少了在提示中重复解释上下文的需要，并使未来的会话更加高效。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/concepts/agents/copilot-memory>)。

## 安全考虑

当您使用{% data variables.copilot.copilot_cli_short %}时，{% data variables.product.prodname_copilot_short %}可以代表您执行任务，例如执行或修改文件，或者运行shell命令。

因此，在使用 {% data variables.copilot.copilot_cli_short %} 时，您应该始终牢记安全注意事项，就像您自己直接处理文件或直接在终端中运行命令一样。当 {% data variables.copilot.copilot_cli_short %} 请求您批准时，您应该始终仔细查看建议的命令。

### 内容排除

对于 {% data variables.copilot.copilot_business_short %} 和 {% data variables.copilot.copilot_enterprise_short %} 用户，{% data variables.copilot.copilot_cli_short %} 遵循在企业、组织和存储库级别配置的内容排除策略。排除的文件不用作上下文。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/concepts/context/content-exclusion>)。

### 可信目录

受信任的目录控制 {% data variables.copilot.copilot_cli_short %} 可以读取、修改和执行文件的位置。

您应该只从您信任的目录启动 {% data variables.copilot.copilot_cli_short %}。您不应在可能包含您不确定信任的可执行文件的目录中使用 {% data variables.copilot.copilot_cli_short %}。同样，如果您从包含敏感或机密数据或您不希望更改的文件的目录启动 CLI，则可能会无意中使这些文件面临风险。通常，您不应从主目录启动 {% data variables.copilot.copilot_cli_short %}。

权限范围是启发式的，{% data variables.product.company_short %} 不保证受信任目录之外的所有文件都将受到保护。请参阅[风险缓解](<#风险缓解>)。

当您启动 {% data variables.copilot.copilot_cli %} 会话时，系统会要求您确认您信任启动 CLI 的目录中及其下面的文件。请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/set-up-copilot-cli/configure-copilot-cli#setting-trusted-directories>)。

### 允许使用的工具

当{% data variables.product.prodname_copilot_short %}第一次需要使用可用于修改或执行文件的工具时——{% data reusables.cli.tools-needing-approval %}——它会询问您是否允许它使用该工具。

通常，您可以从三个选项中进行选择：

```text
1. Yes
2. Yes, and approve TOOL for the rest of the running session
3. No, and tell Copilot what to do differently (Esc)
```

**选项1**允许{% data variables.product.prodname_copilot_short %}运行此特定命令，仅限这一次。下次需要使用此工具时，它会再次询问您。

**选项2**允许{% data variables.product.prodname_copilot_short %}在当前运行的会话期间再次使用此工具，而无需征求您的许可。它会在新的会话中再次请求您的批准，或者您将来是否恢复当前会话。如果您选择此选项，则表示您允许 {% data variables.product.prodname_copilot_short %} 以它认为合适的任何方式使用此工具。例如，如果 {% data variables.product.prodname_copilot_short %} 要求您允许它运行命令 `rm ./this-file.txt`，并且您选择选项 2，则 {% data variables.product.prodname_copilot_short %} 可以在此会话的当前运行期间运行任何 `rm` 命令（例如 `rm -rf ./*`），而无需请求您的批准。

**选项3**取消建议的命令并允许您告诉{% data variables.product.prodname_copilot_short %}尝试不同的方法。

#### 允许无需手动批准即可使用工具

您可以在交互式或编程会话中使用三个命令行选项来确定 {% data variables.product.prodname_copilot_short %} 可以使用的工具，而无需征求您的批准：

* **`--allow-all-tools`**

允许{% data variables.product.prodname_copilot_short %}使用任何工具而无需征得您的批准。

例如，您可以将此选项与 CLI 的编程调用一起使用，以允许 {% data variables.product.prodname_copilot_short %} 运行任何命令。例如：

  ```shell
  copilot -p "Revert the last commit" --allow-all-tools
  ```

* **`--deny-tool`**

防止{% data variables.product.prodname_copilot_short %}使用特定工具。

该选项优先于 `--allow-all-tools` 和 `--allow-tool` 选项。

* **`--allow-tool`**

允许{% data variables.product.prodname_copilot_short %}使用特定工具而无需征求您的批准。

#### 使用批准选项

`--deny-tool` 和 `--allow-tool` 选项需要以下参数之一：

* `'shell(COMMAND)'`

例如，`copilot --deny-tool='shell(rm)'` 会阻止 {% data variables.product.prodname_copilot_short %} 使用任何 `rm` 命令。

对于`git`和`gh`命令，您可以指定要允许或拒绝的特定第一级子命令。例如：

  ```shell
  copilot --deny-tool='shell(git push)'
  ```

工具规格是可选的。例如，`copilot --allow-tool='shell'`允许{% data variables.product.prodname_copilot_short %}使用任何shell命令，无需单独批准。

* `'write'`

此参数允许或拒绝工具（shell 命令除外）修改文件的权限。

例如，`copilot --allow-tool='write'` 允许{% data variables.product.prodname_copilot_short %} 在未经您个人批准的情况下编辑文件。

* `'MCP_SERVER_NAME'`

此参数允许或拒绝来自指定 MCP 服务器的工具，其中 `MCP_SERVER_NAME` 是您已配置的 MCP 服务器的名称。来自服务器的工具在括号中指定，使用在MCP服务器中注册的工具名称。使用服务器名称而不指定工具将允许或拒绝来自该服务器的所有工具。

例如，`copilot --deny-tool='My-MCP-Server(tool_name)'` 阻止 {% data variables.product.prodname_copilot_short %} 使用名为 `My-MCP-Server` 的 MCP 服务器中名为 `tool_name` 的工具。

您可以通过在CLI的交互界面中输入`/mcp`，然后从显示的列表中选择服务器来查找MCP服务器的名称。

#### 合并审批选项

您可以使用批准选项的组合来准确确定 {% data variables.product.prodname_copilot_short %} 可以使用哪些工具，而无需征求您的批准。

例如，要阻止 {% data variables.product.prodname_copilot_short %} 使用 `rm` 和 `git push` 命令，但自动允许所有其他工具，请使用：

```shell
copilot --allow-all-tools --deny-tool='shell(rm)' --deny-tool='shell(git push)'
```

要防止 {% data variables.product.prodname_copilot_short %} 使用名为 `My-MCP-Server` 的 MCP 服务器中的工具 `tool_name`，但允许在未经个人批准的情况下使用该服务器中的所有其他工具，请使用：

```shell
copilot --allow-tool='My-MCP-Server' --deny-tool='My-MCP-Server(tool_name)'
```

#### 自动工具批准的安全影响

了解使用批准命令行选项的安全影响非常重要。这些选项允许 {% data variables.product.prodname_copilot_short %} 执行完成您的请求所需的命令，而无需让您有机会在运行之前审查和批准这些命令。虽然这简化了工作流程，并允许 CLI 进行无头操作，但它增加了采取意外操作的风险，可能导致数据丢失或损坏或其他安全问题。

您可以通过以下方式控制 {% data variables.copilot.copilot_cli_short %} 可以使用哪些工具：在 {% data variables.product.prodname_copilot_short %} 尝试使用某个工具时响应审批提示，使用命令行标志指定权限，或者（在交互式会话中）使用斜杠命令（例如 `/allow-all` 和 `/yolo`。请参阅 [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/set-up-copilot-cli/configure-copilot-cli#setting-allowed-tools>)。

### 风险缓解

您可以通过在沙盒环境中运行 {% data variables.copilot.copilot_cli_short %} 来降低与使用自动批准选项相关的风险。 {% data variables.product.github %} 为此提供了第一方沙箱，通过本地沙箱来限制对您的计算机的访问，通过云沙箱来实现完全隔离的执行。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/concepts/about-cloud-and-local-sandboxes>)。

或者，您可以在虚拟机、容器或专用系统中运行 {% data variables.copilot.copilot_cli_short %}，并严格控制权限和网络访问。

### 已知的 MCP 服务器策略限制

{% data variables.copilot.copilot_cli_short %} 目前无法支持以下组织级 MCP 服务器策略：

* {% data variables.product.prodname_copilot_short %}**中的**MCP服务器，控制MCP服务器是否可以被{% data variables.product.prodname_copilot_short %}使用。
* **MCP 注册表 URL**，控制哪个 MCP 注册表 {% data variables.product.prodname_copilot_short %} 将允许使用 MCP 服务器。

有关这些策略的更多信息，请参阅 [AUTOTITLE](<https://github.com/copilot/concepts/enterprise/mcp-management#mcp-allowlists>)。

## 型号用途

您可以使用 `/model` 斜线命令或 `--model` 命令行选项来更改 {% data variables.copilot.copilot_cli %} 使用的模型。输入此命令，然后从列表中选择一个模型。

每次您在 {% data variables.copilot.copilot_cli_short %} 的交互界面中与 {% data variables.product.prodname_copilot_short %} 交互，或以编程方式使用 {% data variables.copilot.copilot_cli_short %} 时，都会根据处理的令牌数量消耗 {% data variables.product.prodname_ai_credits_short %}。每次交互消耗的量根据所使用的模型而变化。请参阅[AUTOTITLE](<https://github.com/copilot/reference/copilot-billing/models-and-pricing>)。

### 具有扩展功能的型号

最新型号支持 1 百万个令牌上下文窗口，因此您可以跨更大的代码库、更长的文档和复杂的多文件项目工作，而不会丢失上下文。选择支持的模型后，系统将提示您在默认上下文大小或扩展（1 百万令牌）上下文之间进行选择。

此外，这些模型还支持可配置的推理级别，它在生成响应之前控制模型推理过程的深度。

选择更大的上下文窗口或更高的推理将影响{% data variables.product.prodname_ai_credits_short %}消耗；将消耗更多的代币，因此将使用更多的积分。因此，我们建议您默认使用常规上下文窗口和常规推理，仅针对更复杂的任务选择更大的上下文窗口和更高的推理。

有关支持这些功能的型号列表，请参阅[具有扩展功能的型号](<https://github.com/copilot/reference/ai-models/supported-models#models-with-extended-capabilities>)。

### 使用您自己的模型提供商

您可以将 {% data variables.copilot.copilot_cli_short %} 配置为使用您自己的模型提供程序，而不是 {% data variables.product.github %} 托管的模型。这使您可以连接到 OpenAI 兼容端点、Azure OpenAI 或 Anthropic，包括本地运行的模型，例如 Ollama。您可以使用环境变量配置模型提供程序。

|环境变量|描述|
|---|---|
| `COPILOT_PROVIDER_BASE_URL` |模型提供者的 API 端点的基础 URL。|
| `COPILOT_PROVIDER_TYPE` |提供程序类型：`openai`（默认）、`azure` 或 `anthropic`。 `openai` 类型适用于任何 OpenAI 兼容端点，包括 Ollama 和 vLLM。|
| `COPILOT_PROVIDER_API_KEY` |您用于向提供商进行身份验证的 API 密钥。对于不使用身份验证的提供程序（例如本地 Ollama 实例）不需要。|
| `COPILOT_MODEL` |要使用的模型（使用自定义提供程序时需要）。您还可以使用 `--model` 命令行选项进行设置。|

与{% data variables.copilot.copilot_cli_short %}一起使用的型号必须支持**工具调用**（函数调用）和**流媒体**。如果模型不支持这些功能，{% data variables.copilot.copilot_cli_short %}将返回错误。为了获得最佳结果，模型应具有至少 128k 个标记的上下文窗口。

有关如何配置模型提供程序的详细信息，请在终端中运行 `copilot help providers`。

## 通过 ACP 使用 {% data variables.copilot.copilot_cli_short %}

ACP（Agent客户端协议）是与AI agents交互的开放标准。它允许您在支持此协议的任何第三方工具、IDE 或自动化系统中将 {% data variables.copilot.copilot_cli_short %} 用作 agent。

欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/reference/copilot-cli-reference/acp-server>)。

{% data reusables.cli.feedback %}

## 进一步阅读

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/set-up-copilot-cli/install-copilot-cli>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/use-copilot-cli/overview>)
* [AUTOTITLE](<https://github.com/enterprise-cloud@latest/copilot/responsible-use/agents>)


## 原文对照

<details>
<summary>展开完整原文</summary>

## Introduction

The command-line interface (CLI) for {% data variables.product.prodname_copilot %} allows you to use {% data variables.product.prodname_copilot_short %} directly from your terminal. You can use it to answer questions, write and debug code, and interact with {% data variables.product.prodname_dotcom_the_website %}. For example, you can ask {% data variables.product.prodname_copilot_short %} to make some changes to a project and create a pull request.

{% data variables.copilot.copilot_cli %} gives you quick access to a powerful AI agent, without having to leave your terminal. It can help you complete tasks more quickly by working on your behalf, and you can work iteratively with {% data variables.copilot.copilot_cli %} to build the code you need.

## Supported operating systems

* Linux
* macOS
* Windows from within Powershell and [Windows Subsystem for Linux (WSL)](<https://learn.microsoft.com/en-us/windows/wsl/about>)

For installation instructions, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/set-up-copilot-cli/install-copilot-cli>).

## Modes of use

{% data variables.copilot.copilot_cli %} has two user interfaces: interactive and programmatic.

### Interactive interface

To start an interactive session, enter `copilot`. Within an interactive session, you can have a conversation with {% data variables.product.prodname_copilot_short %}. You can prompt {% data variables.product.prodname_copilot_short %} to perform one or more tasks, and you can give it feedback and steer the direction of the work.

  ![Screenshot of the Welcome message in the interactive interface of {% data variables.product.prodname_copilot_short %}.](<https://github.com/assets/images/help/copilot/copilot-cli-welcome.png>)

  The interactive interface has two modes. In addition to the default ask/execute mode there is also a **plan mode** in which {% data variables.product.prodname_copilot_short %} will build a structured implementation plan for a task you want to complete.

  Press <kbd>Shift</kbd>+<kbd>Tab</kbd> to cycle between modes. In plan mode, {% data variables.product.prodname_copilot_short %} analyzes your request, asks clarifying questions to understand scope and requirements, and builds a plan before writing any code. This helps you catch misunderstandings before any code is written, and stay in control of complex, multi-step tasks.

### Programmatic interface

You can also pass the CLI a single prompt directly on the command line. The CLI completes the task and then exits.

To use the CLI programmatically, include the `-p` or `--prompt` command-line option in your command. To allow {% data variables.product.prodname_copilot_short %} to modify and execute files you should also use one of the approval options described later in this article—see [Allowing tools to be used without manual approval](<#allowing-tools-to-be-used-without-manual-approval>) ). For example:

  ```bash copy
  copilot -p "Show me this week's commits and summarize them" --allow-tool='shell(git)'
  ```

  Alternatively, you can use a script to output command-line options and pipe this to `copilot`. For example:

  ```bash copy
  ./script-outputting-options.sh | copilot
  ```

> [!CAUTION]
> If you use an automatic approval option such as `--allow-all-tools`, {% data variables.product.prodname_copilot_short %} has the same access as you do to files on your computer, and can run any shell commands that you can run, without getting your prior approval. See [Security considerations](<#security-considerations>), later in this article.

## Running {% data variables.copilot.copilot_cli_short %} commands in a sandbox

To prevent {% data variables.product.prodname_copilot_short %} from modifying files you don't want it to touch, you can force agents to run commands within a local sandboxed environment. Alternatively, you can run an entire CLI session in an isolated environment in the cloud. For more information, see [AUTOTITLE](<https://github.com/copilot/concepts/about-cloud-and-local-sandboxes>).

### Local sandboxing

You can enable local sandboxing inside a {% data variables.copilot.copilot_cli_short %} session to restrict {% data variables.product.prodname_copilot_short %}'s access to your filesystem, network, and system capabilities. To enable it, run `/sandbox enable` inside a session.

### Cloud sandboxing

> [!NOTE]
> {% data reusables.cli.public-preview-cloud-sandbox %}

You can start a {% data variables.copilot.copilot_cli_short %} session inside an isolated, cloud-hosted environment with cloud sandboxes. This is useful when you want to run code without affecting your local machine, keep a session's state between uses, continue a session from a different machine, or run multiple tasks in parallel. Cloud sandbox policies inherit from {% data variables.copilot.copilot_cloud_agent %} policies, so existing security controls like firewall rules extend to cloud sandboxes without additional setup.

To start a cloud-backed session, run:

```bash copy
copilot --cloud
```

## Use cases for {% data variables.copilot.copilot_cli %}

The following sections provide examples of tasks you can complete with {% data variables.copilot.copilot_cli %}.

### Local tasks

* From within a project directory you can ask {% data variables.product.prodname_copilot_short %} to make a change to the code in the project. For example:

  `Change the background-color of H1 headings to dark blue`

  {% data variables.product.prodname_copilot_short %} finds the CSS file where H1 headings are defined and changes the color value.

* Ask {% data variables.product.prodname_copilot_short %} to tell you about changes to a file:

  `Show me the last 5 changes made to the CHANGELOG.md file. Who changed the file, when, and give a brief summary of the changes they made`

* Use {% data variables.product.prodname_copilot_short %} to help you improve the code, or documentation, in your project.

  * `Suggest improvements to content.js`

  * `Rewrite the readme in this project to make it more accessible to newcomers`

* Use {% data variables.product.prodname_copilot_short %} to help you perform Git operations.

  * `Commit the changes to this repo`

  * `Revert the last commit, leaving the changes unstaged`

* Ask {% data variables.product.prodname_copilot_short %} to create an application from scratch—for example, as a proof of concept.

  `Use the create-next-app kit and tailwind CSS to create a next.js app. The app should be a dashboard built with data from the GitHub API. It should track this project's build success rate, average build duration, number of failed builds, and automated test pass rate. After creating the app, give me easy to follow instructions on how to build, run, and view the app in my browser.`

* Ask {% data variables.product.prodname_copilot_short %} to explain why a change it made is not working as expected, or tell {% data variables.product.prodname_copilot_short %} to fix a problem with the last change it made. For example:

  `You said: "The application is now running on http://localhost:3002 and is fully functional!" but when I browse to that URL I get "This site can't be reached"`

### Computer use

In local sessions on macOS and Windows, you can enable computer use to let {% data variables.product.prodname_copilot_short %} interact with desktop applications. This capability is useful for workflows in legacy and GUI-only software that do not provide an API, command-line interface, or MCP integration. For more information, see [AUTOTITLE](<https://github.com/copilot/concepts/agents/computer-use>).

### Tasks involving {% data variables.product.prodname_dotcom_the_website %}

* Fetch and display details about your work from {% data variables.product.prodname_dotcom_the_website %}.

  * `List my open PRs`

    This lists your open pull requests from any repository on {% data variables.product.github %}. For more specific results, include the repository name in your prompt:

  * `List all open issues assigned to me in OWNER/REPO`

* Ask {% data variables.product.prodname_copilot_short %} to work on an issue:

  `I've been assigned this issue: https://github.com/octo-org/octo-repo/issues/1234. Start working on this for me in a suitably named branch.`

* Ask {% data variables.product.prodname_copilot_short %} to make file changes and raise a pull request on {% data variables.product.prodname_dotcom_the_website %}.

  * `In the root of this repo, add a Node script called user-info.js that outputs information about the user who ran the script. Create a pull request to add this file to the repo on {% data variables.product.github %}.`

  * `Create a PR that updates the README at https://github.com/octo-org/octo-repo, changing the subheading "How to run" to "Example usage"`

  Copilot creates a pull request on {% data variables.product.prodname_dotcom_the_website %}, on your behalf. You are marked as the pull request author.

* Ask {% data variables.product.prodname_copilot_short %} to create an issue for you on {% data variables.product.prodname_dotcom_the_website %}.

  ``Raise an improvement issue in octo-org/octo-repo. In src/someapp/somefile.py the `file = open('data.txt', 'r')` block opens a file but never closes it.``

* Ask {% data variables.product.prodname_copilot_short %} to check the code changes in a pull request.

  `Check the changes made in PR https://github.com/octo-org/octo-repo/pull/57575. Report any serious errors you find in these changes.`

   Copilot responds in the CLI with a summary of any problems it finds.

* Manage pull requests from {% data variables.copilot.copilot_cli %}.

  * `Merge all of the open PRs that I've created in octo-org/octo-repo`

  * `Close PR #11 on octo-org/octo-repo`

* Find specific types of issues.

  `Use the {% data variables.product.github %} MCP server to find good first issues for a new team member to work on from octo-org/octo-repo`

  > [!NOTE]
  > If you know that a specific MCP server can achieve a particular task, then specifying it in your prompt can help {% data variables.product.prodname_copilot_short %} to deliver the results you want.

* Find specific {% data variables.product.prodname_actions %} workflows.

  `List any Actions workflows in this repo that add comments to PRs`

* Create a {% data variables.product.prodname_actions %} workflow.

  `Branch off from main and create a {% data variables.product.prodname_actions %} workflow that will run on pull requests, or can be run manually. The workflow should run eslint to check for problems in the changes made in the PR. If warnings or errors are found these should be shown as messages in the diff view of the PR. I want to prevent code with errors from being merged into main so, if any errors are found, the workflow should cause the PR check to fail. Push the new branch and create a pull request.`

## Steering the conversation

You can interact with {% data variables.product.prodname_copilot_short %} while it's thinking to steer the conversation:

* **Enqueue additional messages**: Send follow-up messages to steer the conversation in a different direction, or queue additional instructions for {% data variables.product.prodname_copilot_short %} to process after it finishes its current response. This makes conversations feel more natural and keeps you in control.
* **Inline feedback on rejection**: When you reject a tool permission request, you can give {% data variables.product.prodname_copilot_short %} inline feedback about the rejection so it can adapt its approach without stopping entirely. This makes the conversation flow more naturally when you want to guide {% data variables.product.prodname_copilot_short %} away from certain actions.

## Automatic context management

{% data variables.copilot.copilot_cli %} automatically manages your conversation context:

* **Auto-compaction**: When your conversation approaches 95% of the token limit, {% data variables.product.prodname_copilot_short %} automatically compresses your history in the background without interrupting your workflow. This enables virtually infinite sessions.
* **Manual control**: Use `/compact` to manually compress context anytime. Press <kbd>Esc</kbd> to cancel if you change your mind.
* **Visualize usage**: The `/context` command shows a detailed token usage breakdown so you can understand how your context window is being used.

## Customizing {% data variables.copilot.copilot_cli %}

You can customize {% data variables.copilot.copilot_cli %} in a number of ways:

* **Custom instructions**: Custom instructions allow you to give {% data variables.product.prodname_copilot_short %} additional context on your project and how to build, test and validate its changes. All custom instruction files now combine instead of using priority-based fallbacks. For more information, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/customize-copilot/add-custom-instructions>).
* **Model Context Protocol (MCP) servers**: MCP servers allow you to give {% data variables.product.prodname_copilot_short %} access to different data sources and tools. For more information, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/use-copilot-cli/overview#add-an-mcp-server>).
* **{% data variables.copilot.custom_agents_caps_short %}**: {% data variables.copilot.custom_agents_caps_short %} allow you to create different specialized versions of {% data variables.product.prodname_copilot_short %} for different tasks. For example, you could customize {% data variables.product.prodname_copilot_short %} to be an expert frontend engineer following your team's guidelines. {% data variables.copilot.copilot_cli %} includes specialized {% data variables.copilot.custom_agents_short %} that it automatically delegates common tasks to. For more information, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/use-copilot-cli/overview#use-custom-agents>).
* **Hooks**: Hooks allow you to execute custom shell commands at key points during agent execution, enabling you to add validation, logging, security scanning, or workflow automation. See [AUTOTITLE](<https://github.com/copilot/concepts/agents/hooks>).
* **Skills**: Skills allow you to enhance the ability of {% data variables.product.prodname_copilot_short %} to perform specialized tasks with instructions, scripts, and resources. For more information, see [AUTOTITLE](<https://github.com/copilot/concepts/agents/about-agent-skills>).
* **{% data variables.copilot.copilot_memory %}**: {% data variables.copilot.copilot_memory %} allows {% data variables.product.prodname_copilot_short %} to build a persistent understanding of your repository by storing "memories", which are pieces of information about coding conventions, patterns, and preferences that {% data variables.product.prodname_copilot_short %} deduces as it works. This reduces the need to repeatedly explain context in your prompts and makes future sessions more productive. For more information, see [AUTOTITLE](<https://github.com/copilot/concepts/agents/copilot-memory>).

## Security considerations

When you use {% data variables.copilot.copilot_cli_short %}, {% data variables.product.prodname_copilot_short %} can perform tasks on your behalf, such as executing or modifying files, or running shell commands.

You should therefore always keep security considerations in mind when using {% data variables.copilot.copilot_cli_short %}, just as you would when working directly with files yourself, or running commands directly in your terminal. You should always review suggested commands carefully when {% data variables.copilot.copilot_cli_short %} requests your approval.

### Content exclusion

For {% data variables.copilot.copilot_business_short %} and {% data variables.copilot.copilot_enterprise_short %} users, {% data variables.copilot.copilot_cli_short %} respects content exclusion policies configured at the enterprise, organization, and repository levels. Excluded files are not used as context. For more information, see [AUTOTITLE](<https://github.com/copilot/concepts/context/content-exclusion>).

### Trusted directories

Trusted directories control where {% data variables.copilot.copilot_cli_short %} can read, modify, and execute files.

You should only launch {% data variables.copilot.copilot_cli_short %} from directories that you trust. You should not use {% data variables.copilot.copilot_cli_short %} in directories that may contain executable files you can't be sure you trust. Similarly, if you launch the CLI from a directory that contains sensitive or confidential data, or files that you don't want to be changed, you could inadvertently expose those files to risk. Typically, you should not launch {% data variables.copilot.copilot_cli_short %} from your home directory.

Scoping of permissions is heuristic and {% data variables.product.company_short %} does not guarantee that all files outside trusted directories will be protected. See [Risk mitigation](<#risk-mitigation>).

When you start a {% data variables.copilot.copilot_cli %} session, you'll be asked to confirm that you trust the files in, and below, the directory from which you launched the CLI. See [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/set-up-copilot-cli/configure-copilot-cli#setting-trusted-directories>).

### Allowed tools

The first time that {% data variables.product.prodname_copilot_short %} needs to use a tool that could be used to modify or execute a file—{% data reusables.cli.tools-needing-approval %}—it will ask you whether you want to allow it to use that tool.

Typically, you can choose from three options:

```text
1. Yes
2. Yes, and approve TOOL for the rest of the running session
3. No, and tell Copilot what to do differently (Esc)
```

**Option 1** allows {% data variables.product.prodname_copilot_short %} to run this particular command, this time only. The next time it needs to use this tool, it will ask you again.

**Option 2** allows {% data variables.product.prodname_copilot_short %} to use this tool again, without asking you for permission, for the duration of the currently running session. It will ask for your approval again in new sessions, or if you resume the current session in the future. If you choose this option, you are allowing {% data variables.product.prodname_copilot_short %} to use this tool in any way it thinks is appropriate. For example, if {% data variables.product.prodname_copilot_short %} asks you to allow it to run the command `rm ./this-file.txt`, and you choose option 2, then {% data variables.product.prodname_copilot_short %} can run any `rm` command (for example, `rm -rf ./*`) during the current run of this session, without asking for your approval.

**Option 3** cancels the proposed command and allows you to tell {% data variables.product.prodname_copilot_short %} to try a different approach.

#### Allowing tools to be used without manual approval

There are three command-line options that you can use, in either interactive or programmatic sessions, to determine tools that {% data variables.product.prodname_copilot_short %} can use without asking for your approval:

* **`--allow-all-tools`**

  Allows {% data variables.product.prodname_copilot_short %} to use any tool without asking for your approval.

  For example, you can use this option with a programmatic invocation of the CLI to allow {% data variables.product.prodname_copilot_short %} to run any command. For example:

  ```shell
  copilot -p "Revert the last commit" --allow-all-tools
  ```

* **`--deny-tool`**

  Prevents {% data variables.product.prodname_copilot_short %} from using a specific tool.

  This option takes precedence over the `--allow-all-tools` and `--allow-tool` options.

* **`--allow-tool`**

  Allows {% data variables.product.prodname_copilot_short %} to use a specific tool without asking for your approval.

#### Using the approval options

The `--deny-tool` and `--allow-tool` options require one of the following arguments:

* `'shell(COMMAND)'`

  For example, `copilot --deny-tool='shell(rm)'` prevents {% data variables.product.prodname_copilot_short %} from using any `rm` command.

  For `git` and `gh` commands, you can specify a particular first-level subcommand to allow or deny. For example:

  ```shell
  copilot --deny-tool='shell(git push)'
  ```

  The tool specification is optional. For example, `copilot --allow-tool='shell'` allows {% data variables.product.prodname_copilot_short %} to use any shell command without individual approval.

* `'write'`

  This argument allows or denies tools—other than shell commands—permission to modify files.

  For example, `copilot --allow-tool='write'` allows {% data variables.product.prodname_copilot_short %} to edit files without your individual approval.

* `'MCP_SERVER_NAME'`

  This argument allows or denies tools from the specified MCP server, where `MCP_SERVER_NAME` is the name of an MCP server that you have configured. Tools from the server are specified in parentheses, using the tool name that is registered with the MCP server. Using the server name without specifying a tool allows or denies all tools from that server.

  For example, `copilot --deny-tool='My-MCP-Server(tool_name)'` prevents {% data variables.product.prodname_copilot_short %} from using the tool called `tool_name` from the MCP server called `My-MCP-Server`.

  You can find an MCP server's name by entering `/mcp` in the CLI's interactive interface, then selecting the server from the list that's displayed.

#### Combining approval options

You can use a combination of approval options to determine exactly which tools {% data variables.product.prodname_copilot_short %} can use without asking for your approval.

For example, to prevent {% data variables.product.prodname_copilot_short %} from using the `rm` and `git push` commands, but automatically allow all other tools, use:

```shell
copilot --allow-all-tools --deny-tool='shell(rm)' --deny-tool='shell(git push)'
```

To prevent {% data variables.product.prodname_copilot_short %} from using the tool `tool_name` from the MCP server named `My-MCP-Server`, but allow all other tools from that server to be used without individual approval, use:

```shell
copilot --allow-tool='My-MCP-Server' --deny-tool='My-MCP-Server(tool_name)'
```

#### Security implications of automatic tool approval

It's important to be aware of the security implications of using the approval command-line options. These options allow {% data variables.product.prodname_copilot_short %} to execute commands needed to complete your request, without giving you the opportunity to review and approve those commands before they are run. While this streamlines workflows, and allows headless operation of the CLI, it increases the risk of unintended actions being taken that might result in data loss or corruption, or other security issues.

You can control which tools {% data variables.copilot.copilot_cli_short %} can use by responding to approval prompts when {% data variables.product.prodname_copilot_short %} attempts to use a tool, by specifying permissions with command-line flags, or (in an interactive session) by using slash commands (such as `/allow-all` and `/yolo`. See [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/set-up-copilot-cli/configure-copilot-cli#setting-allowed-tools>).

### Risk mitigation

You can mitigate the risks associated with using the automatic approval options by running {% data variables.copilot.copilot_cli_short %} in a sandboxed environment. {% data variables.product.github %} provides first-party sandboxing for this, with local sandboxing to restrict access on your machine and cloud sandboxing for fully isolated execution. For more information, see [AUTOTITLE](<https://github.com/copilot/concepts/about-cloud-and-local-sandboxes>).

Alternatively, you can run {% data variables.copilot.copilot_cli_short %} in a virtual machine, container, or dedicated system with tightly controlled permissions and network access.

### Known MCP server policy limitations

{% data variables.copilot.copilot_cli_short %} can't currently support the following organization-level MCP server policies:

* **MCP servers in {% data variables.product.prodname_copilot_short %}**, which controls whether MCP servers can be used at all by {% data variables.product.prodname_copilot_short %}.
* **MCP Registry URL**, which controls which MCP registry {% data variables.product.prodname_copilot_short %} will allow MCP servers to be used from.

For more information about these policies, see [AUTOTITLE](<https://github.com/copilot/concepts/enterprise/mcp-management#mcp-allowlists>).

## Model usage

You can change the model used by {% data variables.copilot.copilot_cli %} by using the `/model` slash command or the `--model` command-line option. Enter this command, then select a model from the list.

Each time you interact with {% data variables.product.prodname_copilot_short %} in {% data variables.copilot.copilot_cli_short %}'s interactive interface, or use {% data variables.copilot.copilot_cli_short %} programmatically, {% data variables.product.prodname_ai_credits_short %} are consumed based on the number of tokens processed. The amount consumed per interaction varies depending on the model used. See [AUTOTITLE](<https://github.com/copilot/reference/copilot-billing/models-and-pricing>).

### Models with extended capabilities

The latest models support a 1 million token context window, so you can work across larger codebases, longer documents, and complex multi-file projects without losing context. After you select a supported model, you will be prompted to choose between the default context size or an extended (1 million token) context.

In addition, these models also support configurable reasoning levels, which control the depth of the model's reasoning process before it generates a response.

Choosing a larger context window or higher reasoning will impact {% data variables.product.prodname_ai_credits_short %} consumption; more tokens will be consumed, so more credits will be used. For this reason, we recommend that you use the regular context window and regular reasoning by default, selecting the larger context window and higher reasoning for more complex tasks only.

For a list of models that support these capabilities, see [Models with extended capabilities](<https://github.com/copilot/reference/ai-models/supported-models#models-with-extended-capabilities>).

### Using your own model provider

You can configure {% data variables.copilot.copilot_cli_short %} to use your own model provider instead of {% data variables.product.github %}-hosted models. This lets you connect to an OpenAI-compatible endpoint, Azure OpenAI, or Anthropic, including locally running models such as Ollama. You configure your model provider using environment variables.

| Environment variable | Description |
|---|---|
| `COPILOT_PROVIDER_BASE_URL` | The base URL of your model provider's API endpoint. |
| `COPILOT_PROVIDER_TYPE` | The provider type: `openai` (default), `azure`, or `anthropic`. The `openai` type works with any OpenAI-compatible endpoint, including Ollama and vLLM. |
| `COPILOT_PROVIDER_API_KEY` | Your API key for authenticating with the provider. Not required for providers that don't use authentication, such as a local Ollama instance. |
| `COPILOT_MODEL` | The model to use (required when using a custom provider). You can also set this with the `--model` command-line option. |

Models used with {% data variables.copilot.copilot_cli_short %} must support **tool calling** (function calling) and **streaming**. If the model does not support these capabilities, {% data variables.copilot.copilot_cli_short %} will return an error. For best results, the model should have a context window of at least 128k tokens.

For details on how to configure your model provider, run `copilot help providers` in your terminal.

## Use {% data variables.copilot.copilot_cli_short %} via ACP

ACP (the Agent Client Protocol) is an open standard for interacting with AI agents. It allows you to use {% data variables.copilot.copilot_cli_short %} as an agent in any third-party tools, IDEs, or automation systems that support this protocol.

For more information, see [AUTOTITLE](<https://github.com/copilot/reference/copilot-cli-reference/acp-server>).

{% data reusables.cli.feedback %}

## Further reading

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/set-up-copilot-cli/install-copilot-cli>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/use-copilot-cli/overview>)
* [AUTOTITLE](<https://github.com/enterprise-cloud@latest/copilot/responsible-use/agents>)


</details>
