# Gemini CLI 入门

串联安装、认证、配置与首次使用，以理解仓库和运行单元测试等任务说明终端工作流，并介绍查询配额。

- 来源：Google／Gemini CLI
- 资料类型：存档文档
- 存档版本：`9b6e0265d16b`。
- 文档时间：源码提交（存档版）：[2026-04-17](<https://github.com/google-gemini/gemini-cli/commit/220888ac2dfd40733bc51ea6b8550c3a0777673c>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/google-gemini/gemini-cli/blob/main/docs/get-started/index.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Gemini CLI/docs/get-started/index.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 开始使用 Gemini CLI

欢迎来到【Gemini CLI】！本指南将帮助您使用 Gemini CLI 安装、配置和启动 
，以直接从终端增强您的工作流程。

## 快速入门：安装、身份验证、配置和使用 Gemini CLI

Gemini CLI 将高级语言模型的强大功能直接带入您的命令
线路界面。作为 AI- 的强力助手，Gemini CLI 可以帮助您完成 
 各种任务，从理解和生成代码到审查和 
 编辑文档。

## 安装

安装和运行Gemini CLI的标准方法使用`npm`：

```bash
npm install -g @google/gemini-cli
```

安装 Gemini CLI 后，从命令行运行 Gemini CLI：

```bash
gemini
```

更多安装选项请参见
[Gemini CLI安装](<Gemini CLI 安装、运行与发布渠道.md>)。

## 认证

要开始使用 Gemini CLI，您必须通过 Google 服务进行身份验证。在大多数
情况下，您可以使用现有的Google帐户登录：

1. 安装后运行Gemini CLI：

   ```bash
   gemini
   ```

2. 当被问及“您想如何对该项目进行身份验证？”时选择**1。
使用 Google** 登录。

3. 选择您的Google帐户。

4. 点击**登录**。

某些帐户类型可能需要您配置 Google 云项目。有关
更多信息，包括其他身份验证方法，请参阅
[Gemini CLI身份验证设置](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/get-started/authentication.mdx>)。

## 配置

Gemini CLI 提供了多种配置其行为的方法，包括环境
变量、命令行参数和设置文件。

要探索您的配置选项，请参阅
[Gemini CLI配置](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/reference/configuration.md>)。

## 使用

安装并通过身份验证后，您可以通过在终端中发出 
 命令和提示来开始使用 Gemini CLI。要求它生成代码、解释文件、
等等。

<!-- prettier-ignore -->
> [！NOTE]
> 这些示例展示了潜在的功能。您的实际
> 结果可能会根据所使用的模型和您的项目环境而有所不同。

### 根据内容重命名您的照片

您可以使用 Gemini CLI 自动执行需要可视化 
 分析的文件管理任务。在此示例中，Gemini CLI 根据图像的实际 
 主题重命名图像。

场景：您有一个包含以下文件的文件夹：

```bash
photos/photo1.png
photos/photo2.png
photos/photo3.png
```

给Gemini以下提示：

```cli
Rename the photos in my "photos" directory based on their contents.
```

结果：Gemini 请求重命名您的文件的权限。

选择**允许一次**，您的文件将被重命名：

```bash
photos/yellow_flowers.png
photos/antique_dresser.png
photos/green_android_robot.png
```

### 通过阅读代码来解释存储库

Gemini CLI 对于快速代码库探索非常有效。以下示例
展示了如何要求Gemini CLI获取、分析和汇总远程项目。

场景：您想通过检查
代码来了解流行的开源实用程序的工作原理，而不仅仅是检查其README。

给Gemini CLI以下提示：

```cli
Clone the 'chalk' repository from https://github.com/chalk/chalk, read its key source files, and explain how it works.
```

结果：Gemini 执行一系列操作来回答您的请求。

1.  首先，它请求运行 `git clone` 的权限来下载存储库。
2.  接下来，它会找到重要的源文件并请求读取权限
他们。
3.  最后对代码进行分析后，进行总结。

Gemini CLI根据实际源代码返回解释：

```markdown
The `chalk` library is a popular npm package for styling terminal output with
colors. After analyzing the source code, here's how it works:

- **Core functionality:** The main file sets up a chainable API. Each color or
  modifier (like `bold` or `italic`) is a getter that appends the corresponding
  ANSI escape code to an internal stack.

- **Chainable API:** When you call `chalk.red.bold('Hello')`, you are accessing
  getters. The `red` getter adds the red color code, and the `bold` getter adds
  the bold code.

- **Output generation:** When the chain is treated as a string (for example, in
  `console.log`), a final `toString()` method is called. This method joins all
  the stored ANSI codes, wraps them around the input string ('Hello'), and adds
  a reset code at the end. This produces the final, styled string that the
  terminal can render.
```

### 将两个电子表格合并为一个电子表格

Gemini CLI 可以跨多个文件处理和转换数据。使用此 
 功能可以合并报告或重新格式化数据集，而无需手动复制。

场景：您有两个 .csv 文件：`Revenue - 2023.csv` 和 
`Revenue - 2024.csv`。每个文件都包含每月收入数据。

给Gemini CLI以下提示：

```cli
Combine the two .csv files into a single .csv file, with each year a different column.
```

结果：Gemini CLI 读取每个文件，然后请求写入新的 
 文件的权限。提供您的许可，Gemini CLI 提供合并数据：

```csv
Month,2023,2024
January,0,1000
February,0,1200
March,0,2400
April,900,500
May,1000,800
June,1000,900
July,1200,1000
August,1800,400
September,2000,2000
October,2400,3400
November,3400,1800
December,2100,9000
```

### 运行单元测试

Gemini CLI 可以根据您现有的 
 实现生成样板代码和测试。此示例演示如何请求 
JavaScript 组件的代码覆盖率。

场景：您已经编写了一个简单的登录页面。您希望编写单元测试来
确保您的登录页面具有代码覆盖率。

给Gemini CLI以下提示：

```cli
Write unit tests for Login.js.
```

结果：Gemini CLI 请求写入新文件的权限，并为您的登录页面创建一个测试 
。

## 检查使用情况和配额

您可以使用
`/stats model`命令查看当前的令牌使用情况和配额信息。此命令提供当前 
 会话的令牌使用情况的快照，以及受支持的 
 模型的总体配额和使用情况。

有关`/stats`命令及其子命令的详细信息，请参见
[命令参考](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/reference/commands.md#stats>)。

## 后续步骤

- 按照[文件管理](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/tutorials/file-management.md>)指南
开始使用您的代码库。
- 请参阅[Shell命令](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/tutorials/shell-commands.md>)了解
终端集成。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Get started with Gemini CLI

Welcome to Gemini CLI! This guide will help you install, configure, and start
using Gemini CLI to enhance your workflow right from your terminal.

## Quickstart: Install, authenticate, configure, and use Gemini CLI

Gemini CLI brings the power of advanced language models directly to your command
line interface. As an AI-powered assistant, Gemini CLI can help you with a
variety of tasks, from understanding and generating code to reviewing and
editing documents.

## Install

The standard method to install and run Gemini CLI uses `npm`:

```bash
npm install -g @google/gemini-cli
```

Once Gemini CLI is installed, run Gemini CLI from your command line:

```bash
gemini
```

For more installation options, see
[Gemini CLI Installation](<Gemini CLI 安装、运行与发布渠道.md>).

## Authenticate

To begin using Gemini CLI, you must authenticate with a Google service. In most
cases, you can log in with your existing Google account:

1. Run Gemini CLI after installation:

   ```bash
   gemini
   ```

2. When asked "How would you like to authenticate for this project?" select **1.
   Sign in with Google**.

3. Select your Google account.

4. Click on **Sign in**.

Certain account types may require you to configure a Google Cloud project. For
more information, including other authentication methods, see
[Gemini CLI Authentication Setup](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/get-started/authentication.mdx>).

## Configure

Gemini CLI offers several ways to configure its behavior, including environment
variables, command-line arguments, and settings files.

To explore your configuration options, see
[Gemini CLI Configuration](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/reference/configuration.md>).

## Use

Once installed and authenticated, you can start using Gemini CLI by issuing
commands and prompts in your terminal. Ask it to generate code, explain files,
and more.

<!-- prettier-ignore -->
> [!NOTE]
> These examples demonstrate potential capabilities. Your actual
> results can vary based on the model used and your project environment.

### Rename your photographs based on content

You can use Gemini CLI to automate file management tasks that require visual
analysis. In this example, Gemini CLI renames images based on their actual
subject matter.

Scenario: You have a folder containing the following files:

```bash
photos/photo1.png
photos/photo2.png
photos/photo3.png
```

Give Gemini the following prompt:

```cli
Rename the photos in my "photos" directory based on their contents.
```

Result: Gemini asks for permission to rename your files.

Select **Allow once** and your files are renamed:

```bash
photos/yellow_flowers.png
photos/antique_dresser.png
photos/green_android_robot.png
```

### Explain a repository by reading its code

Gemini CLI is effective for rapid codebase exploration. The following example
shows how to ask Gemini CLI to fetch, analyze, and summarize a remote project.

Scenario: You want to understand how a popular open-source utility works by
inspecting its code, not just its README.

Give Gemini CLI the following prompt:

```cli
Clone the 'chalk' repository from https://github.com/chalk/chalk, read its key source files, and explain how it works.
```

Result: Gemini performs a sequence of actions to answer your request.

1.  First, it asks for permission to run `git clone` to download the repository.
2.  Next, it finds the important source files and asks for permission to read
    them.
3.  Finally, after analyzing the code, it provides a summary.

Gemini CLI returns an explanation based on the actual source code:

```markdown
The `chalk` library is a popular npm package for styling terminal output with
colors. After analyzing the source code, here's how it works:

- **Core functionality:** The main file sets up a chainable API. Each color or
  modifier (like `bold` or `italic`) is a getter that appends the corresponding
  ANSI escape code to an internal stack.

- **Chainable API:** When you call `chalk.red.bold('Hello')`, you are accessing
  getters. The `red` getter adds the red color code, and the `bold` getter adds
  the bold code.

- **Output generation:** When the chain is treated as a string (for example, in
  `console.log`), a final `toString()` method is called. This method joins all
  the stored ANSI codes, wraps them around the input string ('Hello'), and adds
  a reset code at the end. This produces the final, styled string that the
  terminal can render.
```

### Combine two spreadsheets into one spreadsheet

Gemini CLI can process and transform data across multiple files. Use this
capability to merge reports or reformat data sets without manual copying.

Scenario: You have two .csv files: `Revenue - 2023.csv` and
`Revenue - 2024.csv`. Each file contains monthly revenue figures.

Give Gemini CLI the following prompt:

```cli
Combine the two .csv files into a single .csv file, with each year a different column.
```

Result: Gemini CLI reads each file and then asks for permission to write a new
file. Provide your permission and Gemini CLI provides the combined data:

```csv
Month,2023,2024
January,0,1000
February,0,1200
March,0,2400
April,900,500
May,1000,800
June,1000,900
July,1200,1000
August,1800,400
September,2000,2000
October,2400,3400
November,3400,1800
December,2100,9000
```

### Run unit tests

Gemini CLI can generate boilerplate code and tests based on your existing
implementation. This example demonstrates how to request code coverage for a
JavaScript component.

Scenario: You've written a simple login page. You wish to write unit tests to
ensure that your login page has code coverage.

Give Gemini CLI the following prompt:

```cli
Write unit tests for Login.js.
```

Result: Gemini CLI asks for permission to write a new file and creates a test
for your login page.

## Check usage and quota

You can check your current token usage and quota information using the
`/stats model` command. This command provides a snapshot of your current
session's token usage, as well as your overall quota and usage for the supported
models.

For more information on the `/stats` command and its subcommands, see the
[Command Reference](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/reference/commands.md#stats>).

## Next steps

- Follow the [File management](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/tutorials/file-management.md>) guide to
  start working with your codebase.
- See [Shell commands](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/tutorials/shell-commands.md>) to learn about
  terminal integration.


</details>
