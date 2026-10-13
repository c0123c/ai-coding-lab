# Gemini CLI 文件管理

说明如何读取文件和目录提供上下文，搜索相关实现、修改代码并管理多文件任务。

- 来源：Google／Gemini CLI
- 资料类型：存档文档
- 文档时间：更新：[2026-03-19](<https://geminicli.com/docs/cli/tutorials/file-management/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://geminicli.com/docs/cli/tutorials/file-management/>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Gemini CLI/Gemini CLI 文件管理.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 使用Gemini CLI进行文件管理

使用 Gemini CLI 探索、分析和修改您的代码库。在本指南中，
您将学习如何向Gemini CLI提供文件和目录，修改和
创建文件，以及控制Gemini CLI可以看到的内容。

## 先决条件

- Gemini CLI已安装并经过身份验证。

- 要使用的项目目录（例如，git 存储库）。

## 通过读取文件提供上下文

Gemini CLI通常会尝试读取相关文件，有时会提示您
进行访问（取决于您的设置）。为了确保Gemini CLI使用文件，
您也可以直接包含它。

### 直接文件包含 (@)

如果您知道要处理的文件的路径，请使用@符号。此
强制CLI立即读取文件并将其内容插入到您的
提示符中。

```
`\n@src/components/UserProfile.tsx\n Explain how this component handles user data.`
```

### 处理多个文件

复杂的功能通常跨越多个文件。您可以链接@引用来为
和agent提供依赖关系的完整图片。

```
`\n@src/components/UserProfile.tsx\n @src/types/User.ts Refactor the component to use the updated User interface.`
```

### 包括整个目录

对于广泛的问题或重构，您可以包含整个目录。 
小心大型文件夹，因为这会消耗更多令牌。

```
`\n@src/utils/\n Check these utility functions for any deprecated API usage.`
```

## 如何查找文件（探索）

如果您不知道确切的文件路径，可以要求Gemini CLI为
您查找。这在浏览新代码库或查找特定的 
 逻辑时非常有用。

### 场景：查找组件定义

您知道有一个 UserProfile 组件，但您不知道它位于哪里。

```
`Find the file that defines the UserProfile component.`
```

Gemini 使用 glob 或 list_directory 工具来搜索您的项目 
 结构。它将返回特定路径（例如， src/components/UserProfile.tsx ），然后您可以在下一个 
 回合中将其与 @ 一起使用。

## 如何修改代码

一旦 Gemini CLI 有了上下文，您就可以指示它进行特定编辑。 agent
能够进行复杂的重构，而不仅仅是简单的文本替换。

```
`Update @src/components/UserProfile.tsx to show a loading spinner if the user data is null.`
```

Gemini CLI使用替换工具提出有针对性的代码更改。

### 创建新文件

您还可以要求 agent 创建全新的文件或文件夹结构。

```
`Create a new file @src/components/LoadingSpinner.tsx with a simple Tailwind CSS spinner.`
```

Gemini CLI使用write_file工具从头开始生成新文件。

## 检查并确认更改

Gemini CLI 安全优先。在修改任何文件之前，它会显示建议更改的
统一差异。

```
if (!user) return null;\nif (!user) return <LoadingSpinner />;
```

- 红线 (-)：将被删除的代码。

- 绿线 (+)：将添加的代码。

按 y 确认并将更改应用到本地文件系统。如果 
 差异看起来不正确，请按 n 取消并优化提示。

## 验证结果

编辑完成后，验证修复情况。您可以简单地再次读取该文件
，或者更好的是，运行项目的测试。

```
`Run the tests for the UserProfile component.`
```

Gemini CLI 使用 run_shell_command 工具来执行测试运行程序（对于 
 示例、 npm test 或 jest ）。这确保了更改不会破坏现有的
功能。

## 高级：控制 Gemini 看到的内容

默认情况下，Gemini CLI 尊重您的 .gitignore 文件。它不会通过 node_modules 读取或搜索 
、构建工件或其他被忽略的路径。

如果您有敏感文件（如 .env ）或大型资产，您希望将 
 隐藏在 AI 中而不在 Git 中忽略它们，您可以在项目根目录中创建一个 .geminiignore 文件。

示例 .geminiignore ：

```
.env\nlocal-db-dump.sql\nprivate-notes.md
```

## 后续步骤

- 了解如何管理上下文和记忆以保持您的
agent 长时间会话更智能。

- 有关运行测试的更多信息，请参阅执行 shell 命令和
构建。

- 探索技术文件系统参考
高级工具参数。


## 原文对照

<details>
<summary>展开完整原文</summary>

# File management with Gemini CLI

Explore, analyze, and modify your codebase using Gemini CLI. In this guide,
you’ll learn how to provide Gemini CLI with files and directories, modify and
create files, and control what Gemini CLI can see.

## Prerequisites

- Gemini CLI installed and authenticated.

- A project directory to work with (for example, a git repository).

## Providing context by reading files

Gemini CLI will generally try to read relevant files, sometimes prompting you
for access (depending on your settings). To ensure that Gemini CLI uses a file,
you can also include it directly.

### Direct file inclusion ( @ )

If you know the path to the file you want to work on, use the @ symbol. This
forces the CLI to read the file immediately and inject its content into your
prompt.

```
`\n@src/components/UserProfile.tsx\n Explain how this component handles user data.`
```

### Working with multiple files

Complex features often span multiple files. You can chain @ references to give
the agent a complete picture of the dependencies.

```
`\n@src/components/UserProfile.tsx\n @src/types/User.ts Refactor the component to use the updated User interface.`
```

### Including entire directories

For broad questions or refactoring, you can include an entire directory. Be
careful with large folders, as this consumes more tokens.

```
`\n@src/utils/\n Check these utility functions for any deprecated API usage.`
```

## How to find files (Exploration)

If you don’t know the exact file path, you can ask Gemini CLI to find it for
you. This is useful when navigating a new codebase or looking for specific
logic.

### Scenario: Find a component definition

You know there’s a UserProfile component, but you don’t know where it lives.

```
`Find the file that defines the UserProfile component.`
```

Gemini uses the glob or list_directory tools to search your project
structure. It will return the specific path (for example, src/components/UserProfile.tsx ), which you can then use with @ in your next
turn.

## How to modify code

Once Gemini CLI has context, you can direct it to make specific edits. The agent
is capable of complex refactoring, not just simple text replacement.

```
`Update @src/components/UserProfile.tsx to show a loading spinner if the user data is null.`
```

Gemini CLI uses the replace tool to propose a targeted code change.

### Creating new files

You can also ask the agent to create entirely new files or folder structures.

```
`Create a new file @src/components/LoadingSpinner.tsx with a simple Tailwind CSS spinner.`
```

Gemini CLI uses the write_file tool to generate the new file from scratch.

## Review and confirm changes

Gemini CLI prioritizes safety. Before any file is modified, it presents a
unified diff of the proposed changes.

```
if (!user) return null;\nif (!user) return <LoadingSpinner />;
```

- Red lines (-): Code that will be removed.

- Green lines (+): Code that will be added.

Press y to confirm and apply the change to your local file system. If the
diff doesn’t look right, press n to cancel and refine your prompt.

## Verify the result

After the edit is complete, verify the fix. You can simply read the file again
or, better yet, run your project’s tests.

```
`Run the tests for the UserProfile component.`
```

Gemini CLI uses the run_shell_command tool to execute your test runner (for
example, npm test or jest ). This ensures the changes didn’t break existing
functionality.

## Advanced: Controlling what Gemini sees

By default, Gemini CLI respects your .gitignore file. It won’t read or search
through node_modules , build artifacts, or other ignored paths.

If you have sensitive files (like .env ) or large assets that you want to keep
hidden from the AI without ignoring them in Git, you can create a .geminiignore file in your project root.

Example .geminiignore :

```
.env\nlocal-db-dump.sql\nprivate-notes.md
```

## Next steps

- Learn how to Manage context and memory to keep your
agent smarter over long sessions.

- See Execute shell commands for more on running tests and
builds.

- Explore the technical File system reference for
advanced tool parameters.


</details>
