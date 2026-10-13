# Copilot 如何理解 VS Code 工作区

解释搜索、阅读和语义索引的范围、排除设置与未索引时的行为，帮助为仓库问答提供有效上下文。

- 来源：Microsoft／VS Code
- 资料类型：文档源码（含未展开模板）
- 存档版本：`7dde53519982`。
- 文档时间：官方审核：[2026-10-07](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/reference/workspace-context.md>)；源码提交（存档版）：[2026-10-06](<https://github.com/microsoft/vscode-docs/commit/4fe6b03408632f2e9338a970d26afa480e4a022d>)；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/reference/workspace-context.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Microsoft/VS Code/Copilot 如何理解 VS Code 工作区.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
ContentId: c77dcce9-4ba9-40ac-8ae5-2df855088090
DateApproved: 10/7/2026
MetaDescription: Learn how Copilot agents understand your codebase with semantic search, text search, grep, and other tools to gather context for accurate answers.
MetaSocialImage: ../images/shared/github-copilot-social.png
Keywords:
- workspace context
- semantic search
- cross-file reasoning
- codebase understanding
- large codebase
- monorepo
- indexing
- language intelligence
- LSP
- GitHub code search
- grep
- text search
- agentic search
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# Copilot 如何了解您的工作空间

Copilot agents 搜索整个代码库以了解组件如何连接并提供基于实际代码的答案。您可以使用广泛的提示，例如“身份验证在哪里处理？”或“为列表端点添加测试”并根据您的代码库获得准确的答案和编辑。

本文介绍了 Copilot 如何理解您的代码库，包括它用于搜索代码的不同工具以及它为实现快速搜索而创建的索引。这些相同的通用方法适用于所有代码库，从具有五个文件的代码库到具有 500,000 文件的代码库。

## 搜索和阅读工具

当您发送提示时，Copilot 会分析它需要哪些信息，并自动选择正确的搜索工具组合来回答您的问题或开始生成代码编辑。 Copilot 为此运行多个工具，查看结果，并自动执行后续搜索，直到充分理解问题。

例如，当被要求“向支付服务添加错误处理”时，agent 可能：

1. 使用**语义搜索**在整个项目中查找与支付相关的代码。
1. 使用 **grep** 查找代码库中现有的错误处理模式。
1. 使用**usages**跟踪支付函数的调用方式。
1. 使用**文件搜索**找到相关的配置和测试文件。
1. 阅读相关文件并进行协调更改。

这种迭代方法意味着 agent 以与开发人员相同的方式收集上下文：从多个角度探索代码库，直到获得完整的图片。

Agents 可以访问以下内置搜索工具。您还可以通过键入 `#` 后跟工具名称来在提示中显式引用这些工具。

|工具|描述|
|------|-------------|
|**语义搜索** (`#codebase`)|查找与您的问题含义匹配的代码，而不仅仅是确切的关键字。需要 [ 工作空间索引 ](<#语义索引来源>)。|
|**文字搜索**|搜索文件内容中的文本匹配项，例如特定关键字。|
|**Grep**|跨文件搜索精确的文本或正则表达式模式。无需索引即可工作。|
|**文件搜索**|按名称或全局模式查找文件。|
|**用法**|将“查找所有引用”、“查找实现”和“Go”与“定义”结合起来，以跟踪符号在文件中的使用方式。|
|**列表目录**|列出目录的内容以探索项目结构。|
|**读取文件**|读取特定文件的内容以详细检查其代码。|

这些工具适用于任何大小的工作空间。对于小型项目，整个工作区可以直接读入 agent 的上下文中。对于较大的项目，agent 根据您的项目规模和可用资源选择最有效的搜索策略。

### 搜索工作区之外的 GitHub 存储库

除了搜索当前工作空间之外，agents还可以搜索其他GitHub存储库中的代码。当 agent 需要了解本地未打开的代码库时，这非常有用，例如在上游库中查找 API 或比较跨存储库的实现。

|工具|描述|
|------|-------------|
|**语义搜索 GitHub 存储库 ** (`#githubRepo`)|在 GitHub 存储库中语义搜索相关源代码片段。使用 `owner/repo` 指定存储库。|
|**GitHub 文本搜索** (`#githubTextSearch`)|文本搜索 GitHub 存储库或组织中包含特定关键字或代码模式的文件。|

对于更高级的 GitHub 工作流程，例如搜索和管理问题或拉取请求，请使用 [GitHub MCP 服务器 ](<https://github.com/github/github-mcp-server>)。

### agent 可以访问什么

Agents 搜索开发人员在浏览代码库时使用的相同来源：

* 工作区中的所有 [ 可索引文件 ](<#语义索引包含哪些内容>)，被 `.gitignore` 文件忽略的文件除外
* 具有嵌套文件夹和文件名的目录结构
* 代码符号和定义（类、函数、变量）
* 当前选定的文本或活动编辑器中的可见文本
* 对话历史记录和之前的工具结果

如果您打开了文件或在忽略的文件中选择了文本，则 > [!IMPORTANT]
> `.gitignore` 将被绕过。

### 使用排除设置改进 agent 搜索

当 agent 使用文本搜索或 grep 搜索您的工作区时，返回的每个匹配项都会成为对话上下文的一部分，即使 agent 从未打开该文件。生成的文件、构建工件、日志和大型数据集可能会产生许多不相关的匹配，从而使上下文窗口充满噪音。

配置排除设置以使 agent 搜索集中于您关心的源代码：

* **`.gitignore`**：从文本搜索、grep 和 [ 语义索引 ](<#语义索引包含哪些内容>) 中排除文件。
* **`setting(files.exclude)`**：对资源管理器隐藏文件，并将其从文本搜索、grep 和语义索引中排除。
* **`setting(search.exclude)`**：从文本搜索和 grep 中排除文件，同时保持它们在资源管理器中可见。

严格排除可以提高搜索相关性，加快大型工作区的搜索速度，并减少搜索结果消耗的令牌。这也有助于[管理AI信用使用情况](<https://github.com/docs/agents/guides/optimize-usage.md#exclude-files-from-copilot-context>)。

将排除模式添加到您的 [ 工作区设置 ](<https://github.com/docs/configure/settings.md>)。模式使用 [glob 语法 ](<https://github.com/docs/editor/glob-patterns.md>)。

> [!TIP]
> 当文本搜索未返回结果时，agent 会收到一条可能排除该模式的提示，例如在调查 `node_modules` 中的依赖项时。

## 语义搜索

语义搜索工具（`#codebase`）通过含义而不是确切的关键字来查找代码。

语义搜索需要构建和维护索引，以便即使在非常大的代码库上也可以快速运行。 Copilot自动为您维护该索引。在幕后，索引的部分内容可能存储在您的计算机上，部分内容可能来自远程源，但您不需要管理这种区别。

您可以在{% data variables.product.prodname_vscode_shortname %}状态栏的Copilot状态仪表板中查看索引状态。

![显示 Copilot 状态菜单中工作区索引状态的屏幕截图。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/workspace-context/workspace-index-status.png>)

### 语义索引来源

Copilot 自动为任何工作区构建和维护语义索引。索引源决定索引首次可用的速度：

* **GitHub 存储库**：GitHub 索引工作区中的 GitHub 存储库。使用您的 GitHub 帐户登录即可使用它们。每个存储库只需构建该索引一次，这意味着它通常可以立即可用。 GitHub 在需要时构建并更新此索引。这对于中小型项目来说速度很快，但如果您的存储库包含数十万个文件，则可能需要一些时间。远程索引适用于 GitHub.com 或 GitHub 企业云上托管的存储库。 GitHub 企业服务器不支持它。

* **Azure DevOps 存储库**：自动构建和维护索引。使用您在{% data variables.product.prodname_vscode_shortname %}中的Microsoft帐户登录Copilot即可开始使用索引。检查Copilot状态栏项目以了解当前索引状态。

* **其他工作区**：对于任何其他工作区，包括不受 GitHub 或 Azure DevOps 存储库支持的本地文件夹，Copilot 会为你构建语义索引。初始构建可能需要几分钟，之后索引将在后台保持最新状态。目前，此功能对个人帐户启用，但对组织和企业用户默认关闭。有关如何使用策略启用此功能的更多详细信息，请参阅 [GitHub 文档 ](<https://docs.github.com/en/copilot/concepts/context/repository-indexing#semantic-indexing-for-non-github-repositories>)。

要按需构建或重建当前工作区的索引，请从命令面板运行 **构建代码库语义索引** 命令。

### 语义索引包含哪些内容

{% data variables.product.prodname_vscode_shortname %} 索引属于当前项目的相关文本文件。这不限于特定的文件类型或编程语言。 {% data variables.product.prodname_vscode_shortname %} 自动跳过一些通常不相关的常见文件类型，例如 `.tmp` 或 `.out` 文件。

工作区索引还排除通过 `setting(files.exclude)` 设置或 `.gitignore` 文件从 {% data variables.product.prodname_vscode_shortname %} 中排除的文件。

二进制文件（例如图像或 PDF）不会被索引。

## 获得更好结果的技巧

您的提示措辞方式会影响 agent 使用的工具以及结果的质量。

* **具体**：避免使用模糊的术语，例如“这是做什么的”，其中“这个”可能意味着最后一个答案、当前文件或整个项目。
* **使用代码术语**：使用代码中出现的函数名称、类名称和概念，以便agent可以找到精确匹配。
* **手动添加上下文**：选择代码、参考文件或[#-提及上下文项](<https://github.com/docs/chat/copilot-chat-context.md>)，例如调试上下文、终端输出等。
* **范围您的请求**：响应可以从多个引用中获取，例如“在没有catch块的情况下查找异常”。但不要指望完整的代码库分析，例如“这个函数被调用了多少次？”。
* **使用外部工具解决非代码问题**：有关“谁贡献了此文件？”之类的信息，请配置相关的[工具或MCP服务器](<https://github.com/docs/agents/run/tools.md>)。

## 私有存储库

要对私有存储库使用语义搜索，Copilot 可能需要额外的权限。如果尚未授予这些权限，{% data variables.product.prodname_vscode_shortname %} 会在启动时请求它们。一旦授予，会话将被安全存储以供将来使用。

![要求对私有存储库进行额外身份验证的模态窗口。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/workspace-context/authentication.png>)

在 [GitHub Copilot 信任中心 ](<https://resources.github.com/copilot-trust-center/>) 了解有关安全、隐私和透明度的更多信息。

## 常见问题

### 我需要在提示中使用 `#codebase` 吗？

不会。Agents 在有意义时自动使用语义搜索。您不需要将 `#codebase` 添加到提示中。

`#codebase` 工具始终是语义的并提供一致的结果。如果您想强制对特定提示进行语义搜索，您仍然可以添加 `#codebase` 作为 [ 上下文项 ](<https://github.com/docs/chat/copilot-chat-context.md>)。

### 如果我的工作区未按语义索引，会发生什么情况？

大多数工作区都会自动建立索引，但如果索引尚不可用（例如，当新的本地工作区仍在构建其初始索引时），agents 仍然可以使用文本搜索、grep、文件搜索和语言智能来有效搜索代码。工作区索引支持语义搜索，可通过含义而不是关键字查找代码。如果没有它，agents 依赖其他搜索工具，仍然可以为大多数提示提供准确的结果。我们发现这些其他工具仍然可以提供很好的结果。


## 原文对照

<details>
<summary>展开完整原文</summary>

# How Copilot understands your workspace

Copilot agents search your entire codebase to understand how components connect and provide answers grounded in your actual code. You can use broad prompts like "where is authentication handled?" or "add tests for the list endpoint" and get accurate answers and edits based on your codebase.

This article explains how Copilot understands your codebase, including the different tools it uses for searching through your code and the indexes it creates to enable quick searches. These same general approaches are used on all codebases, from those with five files to those with 500,000 files.

## Search and read tools

When you send a prompt, Copilot analyzes what information it needs and automatically selects the right combination of search tools to either answer your question or start generating code edits. Copilot runs multiple tools for this, reviews the results, and automatically performs follow-up searches until it has a good understanding of the problem.

For example, when asked to "add error handling to the payment service", the agent might:

1. Use **semantic search** to find payment-related code across the project.
1. Use **grep** to find existing error handling patterns in the codebase.
1. Use **usages** to trace how the payment functions are called.
1. Use **file search** to locate related configuration and test files.
1. Read the relevant files and make coordinated changes.

This iterative approach means the agent gathers context the same way a developer would: by exploring the codebase from multiple angles until it has a complete picture.

Agents have access to the following built-in search tools. You can also explicitly reference these tools in your prompts by typing `#` followed by the tool name.

| Tool | Description |
|------|-------------|
| **Semantic search** (`#codebase`) | Finds code that matches the meaning of your question, not just exact keywords. Requires a [workspace index](<#semantic-index-sources>). |
| **Text search** | Searches file content for text matches, such as specific keywords. |
| **Grep** | Searches for exact text or regex patterns across files. Works without an index. |
| **File search** | Finds files by name or glob pattern. |
| **Usages** | Combines Find All References, Find Implementation, and Go to Definition to trace how symbols are used across files. |
| **List directory** | Lists the contents of a directory to explore the project structure. |
| **Read file** | Reads the content of a specific file to examine its code in detail. |

These tools work for any workspace size. For small projects, the entire workspace can be read directly into the agent's context. For larger projects, the agent selects the most efficient search strategy based on your project size and available resources.

### Search GitHub repositories beyond your workspace

In addition to searching the current workspace, agents can search code in other GitHub repositories. This is useful when the agent needs to learn about a codebase that is not open locally, for example to look up an API in an upstream library or compare an implementation across repositories.

| Tool | Description |
|------|-------------|
| **Semantic Search GitHub Repository** (`#githubRepo`) | Semantic search a GitHub repository for relevant source code snippets. Specify a repository using `owner/repo`. |
| **GitHub Text Search** (`#githubTextSearch`) | Text search a GitHub repository or organization for files containing specific keywords or code patterns. |

For more advanced GitHub workflows, such as searching and managing issues or pull requests, use the [GitHub MCP server](<https://github.com/github/github-mcp-server>).

### What the agent has access to

Agents search through the same sources a developer would use when navigating a codebase:

* All [indexable files](<#what-content-is-included-in-the-semantic-index>) in the workspace, except those ignored by a `.gitignore` file
* Directory structure with nested folders and file names
* Code symbols and definitions (classes, functions, variables)
* Currently selected text or visible text in the active editor
* Conversation history and previous tool results

> [!IMPORTANT]
> `.gitignore` is bypassed if you have a file open or have text selected within an ignored file.

### Improve agent search with exclusion settings

When an agent searches your workspace with text search or grep, every match returned becomes part of the conversation context, even if the agent never opens the file. Generated files, build artifacts, logs, and large datasets can produce many irrelevant matches that fill the context window with noise.

Configure exclusion settings to keep agent searches focused on source code you care about:

* **`.gitignore`**: excludes files from text search, grep, and the [semantic index](<#what-content-is-included-in-the-semantic-index>).
* **`setting(files.exclude)`**: hides files from the Explorer and excludes them from text search, grep, and the semantic index.
* **`setting(search.exclude)`**: excludes files from text search and grep while keeping them visible in the Explorer.

Strict exclusions improve search relevance, speed up searches over large workspaces, and reduce the tokens consumed by search results. This also helps [manage AI credit usage](<https://github.com/docs/agents/guides/optimize-usage.md#exclude-files-from-copilot-context>).

Add exclusion patterns to your [workspace settings](<https://github.com/docs/configure/settings.md>). Patterns use [glob syntax](<https://github.com/docs/editor/glob-patterns.md>).

> [!TIP]
> When a text search returns no results, the agent receives a hint that the pattern may be excluded, for example when investigating a dependency in `node_modules`.

## Semantic search

The semantic search tool (`#codebase`) finds code by meaning rather than exact keywords.

Semantic search requires building and maintaining an index so that it can run quickly, even on very large codebases. Copilot maintains this index for you automatically. Behind the scenes, parts of the index might be stored on your machine and parts might come from remote sources, but you don't need to manage this distinction.

You can view the indexing status in the Copilot status dashboard in the {% data variables.product.prodname_vscode_shortname %} Status Bar.

![Screenshot showing the workspace index status in the Copilot status menu.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/workspace-context/workspace-index-status.png>)

### Semantic index sources

Copilot builds and maintains a semantic index for any workspace automatically. The index source determines how quickly the index becomes available for the first time:

* **GitHub repositories**: GitHub indexes the GitHub repositories in your workspace. Sign in with your GitHub account to use them. This index only needs to be built once per repository, which means it is often instantly available. GitHub builds and updates this index when needed. This is fast for small and medium sized projects, but might take some time if your repository contains hundreds of thousands of files. Remote indexing works for repositories hosted on GitHub.com or GitHub Enterprise Cloud. It is not supported for GitHub Enterprise Server.

* **Azure DevOps repositories**: Indexes are automatically built and maintained. Sign in with your Microsoft account in {% data variables.product.prodname_vscode_shortname %} for Copilot to start using the index. Check the Copilot Status Bar item for the current index status.

* **Other workspaces**: For any other workspace, including local folders not backed by a GitHub or Azure DevOps repository, Copilot builds the semantic index for you. The initial build can take a few minutes, after which the index is kept up to date in the background. This feature is currently enabled for personal accounts but off by default for organization and enterprise users. See the [GitHub docs](<https://docs.github.com/en/copilot/concepts/context/repository-indexing#semantic-indexing-for-non-github-repositories>) for more details about how to enable this feature using a policy.

To build or rebuild the index for the current workspace on demand, run the **Build Codebase semantic index** command from the Command Palette.

### What content is included in the semantic index

{% data variables.product.prodname_vscode_shortname %} indexes relevant text files that are part of your current project. This is not limited to specific file types or programming languages. {% data variables.product.prodname_vscode_shortname %} automatically skips some common file types that are typically not relevant, such as `.tmp` or `.out` files.

The workspace index also excludes files that are excluded from {% data variables.product.prodname_vscode_shortname %} by the `setting(files.exclude)` setting or by a `.gitignore` file.

Binary files, such as images or PDFs, are not indexed.

## Tips for better results

The way you phrase your prompt influences which tools the agent uses and the quality of the results.

* **Be specific**: avoid vague terms like "what does this do", where "this" could mean the last answer, current file, or whole project.
* **Use code terms**: use function names, class names, and concepts that appear in your code so the agent can find exact matches.
* **Add context manually**: select code, reference files, or [#-mention context items](<https://github.com/docs/chat/copilot-chat-context.md>) such as debug context, terminal output, and more.
* **Scope your request**: responses can draw from multiple references, such as "find exceptions without a catch block". But don't expect a full codebase analysis, such as "how many times is this function invoked?".
* **Use external tools for non-code questions**: for information like "who contributed to this file?", configure the relevant [tools or MCP servers](<https://github.com/docs/agents/run/tools.md>).

## Private repositories

To use semantic search for private repositories, Copilot may need additional permission. If these permissions are not already granted, {% data variables.product.prodname_vscode_shortname %} asks for them at startup. Once granted, the session is securely stored for the future.

![Modal window asking for additional authentication for a private repository.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/workspace-context/authentication.png>)

Learn more about security, privacy, and transparency in the [GitHub Copilot Trust Center](<https://resources.github.com/copilot-trust-center/>).

## Frequently asked questions

### Do I need to use `#codebase` in my prompts?

No. Agents automatically use semantic search when it makes sense. You don't need to add `#codebase` to your prompt.

The `#codebase` tool is always semantic and provides consistent results. If you want to force a semantic search for a specific prompt, you can still add `#codebase` as a [context item](<https://github.com/docs/chat/copilot-chat-context.md>).

### What happens if my workspace is not semantically indexed?

Most workspaces are indexed automatically, but if the index isn't available yet (for example, while a new local workspace is still building its initial index), agents still search your code effectively by using text search, grep, file search, and language intelligence. The workspace index enables semantic search, which finds code by meaning rather than keywords. Without it, agents rely on the other search tools and can still provide accurate results for most prompts. We've found that these other tools still provide great results.


</details>
