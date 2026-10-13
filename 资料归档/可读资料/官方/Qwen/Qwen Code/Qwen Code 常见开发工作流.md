# Qwen Code 常见开发工作流

覆盖理解仓库、修复重构、测试、文档、PR、恢复会话及并行工作树，并说明如何将 CLI 纳入验证流程。

- 来源：Qwen／Qwen Code
- 资料类型：存档文档
- 存档版本：`7e3de59d3aba`。
- 文档时间：源码提交（存档版）：[2026-08-29](<https://github.com/QwenLM/qwen-code/commit/7d9ec83cb983705a184a25a745afdc5ae715c0d6>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/QwenLM/qwen-code/blob/main/docs/users/common-workflow.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Qwen/Qwen Code/docs/users/common-workflow.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 常见工作流程

> 通过 Qwen Code 了解常见工作流程。

本文档中的每项任务都包含清晰的说明、示例命令和最佳实践，可帮助您充分利用 Qwen Code。

## 了解新的代码库

### 快速了解代码库概述

假设您刚刚加入一个新项目，需要快速了解其结构。

**1。导航到项目根目录**

```bash
cd /path/to/project
```

**2。开始Qwen Code**

```bash
qwen
```

**3。请求高级概述**

```
give me an overview of this codebase
```

**4。深入研究特定组件**

```
explain the main architecture patterns used here
```

```
what are the key data models?
```

```
how is authentication handled?
```

> [！提示]
>
> - 从广泛的问题开始，然后缩小到特定领域
> - 询问项目中使用的编码约定和模式
> - 索取项目特定术语表

### 查找相关代码

假设您需要查找与特定特性或功能相关的代码。

**1。请Qwen Code查找相关文件**

```
find the files that handle user authentication
```

**2。获取有关组件如何交互的上下文**

```
how do these authentication files work together?
```

**3。了解执行流程**

```
trace the login process from front-end to database
```

> [！提示]
>
> - 具体说明您要寻找的内容
> - 使用项目中的领域语言

## 高效修复错误

假设您遇到错误消息并需要查找并修复其来源。

**1。与 Qwen Code** 分享错误

```
I'm seeing an error when I run npm test
```

**2。寻求修复建议**

```
suggest a few ways to fix the @ts-ignore in user.ts
```

**3。应用修复**

```
update user.tsto add the null check you suggested
```

> [！提示]
>
> - 告诉 Qwen Code 命令来重现问题并获取堆栈跟踪
> - 提及重现错误的任何步骤
> - 让 Qwen Code 知道错误是间歇性的还是持续的

## 重构代码

假设您需要更新旧代码以使用现代模式和实践。

**1。识别遗留代码以进行重构**

```
find deprecated API usage in our codebase
```

**2。获取重构建议**

```
suggest how to refactor utils.js to use modern JavaScript features
```

**3。安全地应用更改**

```
refactor utils.js to use ES 2024 features while maintaining the same behavior
```

**4。验证重构**

```
run tests for the refactored code
```

> [！提示]
>
> - 请 Qwen Code 解释现代方法的好处
> - 要求更改在需要时保持向后兼容性
> - 以小的、可测试的增量进行重构

## 使用专门的子代理

假设您想要使用专门的 AI 子代理来更有效地处理特定任务。

**1。查看可用的子代理**

```
/agents
```

这会显示所有可用的子代理并允许您创建新的子代理。

**2。自动使用子代理**

Qwen Code 自动将适当的任务委托给专门的子代理：

```
review my recent code changes for security issues
```

```
run all tests and fix any failures
```

**3。显式请求特定子代理**

```
use the code-reviewer subagent to check the auth module
```

```
have the debugger subagent investigate why users can't log in
```

**4。为您的工作流程创建自定义子代理**

```
/agents
```

然后选择“创建”并按照提示进行定义：

- 描述子代理用途的唯一标识符（例如，`code-reviewer`、`api-designer`）。
- 当Qwen Code应该使用这个agent时
- 它可以访问哪些工具
- 描述 agent 角色和行为的系统提示

> [！提示]
>
> - 在 `.qwen/agents/` 中创建项目特定的子代理以供团队共享
> - 使用描述性 `description` 字段启用自动委派
> - 将工具访问限制为每个子代理实际需要的内容
> - 了解更多[子Agents](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/sub-agents>)
> - 了解更多[审批模式](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/approval-mode>)

## 处理测试

假设您需要为未覆盖的代码添加测试。

**1。识别未经测试的代码**

```
find functions in NotificationsService.swift that are not covered by tests
```

**2。生成测试脚手架**

```
add tests for the notification service
```

**3。添加有意义的测试用例**

```
add test cases for edge conditions in the notification service
```

**4。运行并验证测试**

```
run the new tests and fix any failures
```

Qwen Code 可以生成遵循项目现有模式和约定的测试。当要求进行测试时，请具体说明您想要验证的行为。 Qwen Code 检查现有的测试文件以匹配已在使用的样式、框架和断言模式。

如需全面覆盖，请询问 Qwen Code 以确定您可能错过的边缘情况。 Qwen Code 可以分析您的代码路径，并建议对错误条件、边界值和容易忽视的意外输入进行测试。

## 创建拉取请求

假设您需要为您的更改创建一个记录良好的拉取请求。

**1。总结您的更改**

```
summarize the changes I've made to the authentication module
```

**2。使用 Qwen Code** 生成拉取请求

```
create a pr
```

**3。审查和完善**

```
enhance the PR description with more context about the security improvements
```

**4。添加测试详细信息**

```
add information about how these changes were tested
```

> [！提示]
>
> - 直接询问Qwen Code为您制作PR
> - 提交前查看 Qwen Code 生成的 PR
> - 请 Qwen Code 强调潜在风险或注意事项

## 处理文档

假设您需要添加或更新代码的文档。

**1。识别未记录的代码**

```
find functions without proper JSDoc comments in the auth module
```

**2。生成文档**

```
add JSDoc comments to the undocumented functions in auth.js
```

**3。回顾并加强**

```
improve the generated documentation with more context and examples
```

**4。验证文档**

```
check if the documentation follows our project standards
```

> [！提示]
>
> - 指定您想要的文档样式（JSDoc、文档字符串等）
> - 询问文档中的示例
> - 请求公共 API、接口和复杂逻辑的文档

## 参考文件和目录

使用`@`快速包含文件或目录，而无需等待Qwen Code读取它们。

**1。引用单个文件**

```
Explain the logic in @src/utils/auth.js
```

这包括对话中文件的完整内容。

**2。参考目录**

```
What's the structure of @src/components?
```

这提供了包含文件信息的目录列表。

**3。参考MCP资源**

```
Show me the data from @github: repos/owner/repo/issues
```

这使用@server:资源格式从连接的MCP服务器获取数据。详情请参见[MCP](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/mcp>)。

> [！提示]
>
> - 文件路径可以是相对路径或绝对路径
> - @文件引用将文件目录和父目录中的`QWEN.md`添加到上下文中
> - 目录引用显示文件列表，而不是内容
> - 您可以在一条消息中引用多个文件（例如，“`@file 1.js` 和 `@file 2.js`”）

## 恢复之前的对话

假设您一直在使用 Qwen Code 处理一项任务，并且需要在稍后的会话中从上次中断的地方继续。

Qwen Code提供了两种恢复之前对话的选项：

- `--continue`自动继续最近的对话
- `--resume` 显示对话选择器

**1。 Continue 最近的对话**

```bash
qwen --continue
```

这会立即恢复您最近的对话，而不会出现任何提示。

**2。 Continue 非交互模式下**

```bash
qwen --continue -p "Continue with my task"
```

将 `-p`（或 `--prompt`）与 `--continue` 结合使用可在非交互模式下恢复最近的对话，非常适合脚本或自动化。

**3。显示对话选择器**

```bash
qwen --resume
```

这将显示一个交互式对话选择器，其中包含一个干净的列表视图，其中显示：

- 会话摘要（或初始提示）
- 元数据：经过的时间、消息计数和 git 分支

使用箭头键导航并按 Enter 键选择对话。按 Esc 退出。

> [！提示]
>
> - 对话历史记录存储在您的本地计算机上
> - 使用 `--continue` 快速访问您最近的对话
> - 当您需要选择特定的过去对话时，请使用 `--resume`
> - 恢复时，您将在继续之前看到整个对话历史记录
> - 恢复的对话以与原始对话相同的型号和配置开始
>
> **工作原理**:
>
> 1. **对话存储**：所有对话及其完整消息历史记录都会自动保存在本地
> 2. **消息反序列化**：恢复时，恢复整个消息历史记录以维护上下文
> 3. **工具状态**：保留上次对话的工具使用情况和结果
> 4. **上下文恢复**：对话恢复，所有先前的上下文都完好无损
>
> **示例**:
>
> ```bash
> # Continue 最近的对话
> q文 --continue
>
> # Continue 最近的对话，带有特定提示
> qwen --continue -p "显示我们的进度"
>
> # 显示对话选择器
> q文 --resume
>
> # Continue 非交互模式下的最近对话
> qwen --continue -p“再次运行测试”
> ```

## 使用 Git 工作树运行并行 Qwen Code 会话

假设您需要同时处理多个任务，并在 Qwen Code 实例之间实现完全代码隔离。

**1。了解 Git 工作树**

Git 工作树允许您将同一存储库中的多个分支检出到不同的目录中。每个工作树都有自己的工作目录，其中包含独立的文件，同时共享相同的 Git 历史记录。请参阅 [ 官方 Git 工作树文档 ](<https://git-scm.com/docs/git-worktree>) 了解更多信息。

**2。创建一个新的工作树**

```bash
# Create a new worktree with a new branch
git worktree add ../project-feature-a -b feature-a

# Or create a worktree with an existing branch
git worktree add ../project-bugfix bugfix-123
```

这将创建一个新目录，其中包含存储库的单独工作副本。

**3。在每个工作树**中运行Qwen Code

```bash
# Navigate to your worktree
cd ../project-feature-a

# Run Qwen Code in this isolated environment
qwen
```

**4。在另一个工作树**中运行Qwen Code

```bash
cd ../project-bugfix
qwen
```

**5。管理您的工作树**

```bash
# List all worktrees
git worktree list

# Remove a worktree when done
git worktree remove ../project-feature-a
```

> [！提示]
>
> - 每个工作树都有自己独立的文件状态，非常适合并行 Qwen Code 会话
> - 在一个工作树中所做的更改不会影响其他工作树，从而防止 Qwen Code 实例相互干扰
> - 所有工作树共享相同的 Git 历史记录和远程连接
> - 对于长时间运行的任务，您可以让 Qwen Code 在一个工作树中工作，同时在另一个工作树中继续开发
> - 使用描述性目录名称可以轻松识别每个工作树的任务
> - 请记住根据项目的设置在每个新工作树中初始化您的开发环境。根据您的堆栈，这可能包括：
>   - JavaScript 项目：运行依赖项安装（`npm install`、`yarn`）
>   - Python 项目：设置虚拟环境或使用包管理器安装
>   - 其他语言：遵循项目的标准设置流程

## 使用Qwen Code作为unix风格的实用程序

### 将 Qwen Code 添加到您的验证流程中

假设您想使用 Qwen Code 作为 linter 或代码审查器。

**将 Qwen Code 添加到您的构建脚本中：**

```json
// package.json
{
    ...
    "scripts": {
        ...
        "lint:Qwen Code": "qwen -p 'you are a linter. please look at the changes vs. main and report any issues related to typos. report the filename and line number on one line, and a description of the issue on the second line. do not return any other text.'"
    }
}
```

> [！提示]
>
> - 使用 Qwen Code 在 CI/CD 管道中进行自动代码审查
> - 自定义提示以检查与您的项目相关的特定问题
> - 考虑为不同类型的验证创建多个脚本

### 管道输入、管道输出

假设您要将数据通过管道传输到 Qwen Code，并以结构化格式取回数据。

**通过Qwen Code传输数据：**

```bash
cat build-error.txt | qwen -p 'concisely explain the root cause of this build error' > output.txt
```

> [！提示]
>
> - 使用管道将Qwen-代码集成到现有的shell脚本中
> - 与其他 Unix 工具结合使用，实现强大的工作流程
> - 考虑使用 --output-format 进行结构化输出

### 控制输出格式

假设您需要 Qwen Code 以特定格式输出，特别是在将 Qwen Code 集成到脚本或其他工具中时。

**1。使用文本格式（默认）**

```bash
cat data.txt | qwen -p 'summarize this data' --output-format text > summary.txt
```

这仅输出 Qwen Code 的纯文本响应（默认行为）。

**2。使用JSON格式**

```bash
cat code.py | qwen -p 'analyze this code for bugs' --output-format json > analysis.json
```

这会输出 JSON 消息数组，其中包含成本和持续时间等元数据。

**3。使用流式传输JSON格式**

```bash
cat log.txt | qwen -p 'parse this log file for errors' --output-format stream-json
```

当 Qwen Code 处理请求时，这会实时输出一系列 JSON 对象。每条消息都是有效的 JSON 对象，但如果串联，整个输出则无效 JSON。

> [！提示]
>
> - 使用 `--output-format text` 进行简单集成，您只需要 Qwen Code 的响应
> - 当您需要完整的对话日志时，请使用 `--output-format json`
> - 使用【`--output-format stream-json`】实时输出每轮对话

## 向 Qwen Code 询问其功能

Qwen Code 具有对其文档的内置访问权限，并且可以回答有关其自身功能和限制的问题。

### 示例问题

```
can Qwen Code create pull requests?
```

```
how does Qwen Code handle permissions?
```

```
what slash commands are available?
```

```
how do I use MCP with Qwen Code?
```

```
how do I configure Qwen Code for Amazon Bedrock?
```

```
what are the limitations of Qwen Code?
```

> [!note]
>
> Qwen Code 为这些问题提供了基于文档的答案。有关可执行示例和动手演示，请参阅上面的特定工作流程部分。

> [！提示]
>
> - 无论您使用的是什么版本，Qwen Code 始终可以访问最新的 Qwen Code 文档
> - 提出具体问题以获得详细答案
> - Qwen Code 可以解释复杂的功能，例如 MCP 集成、企业配置和高级工作流程

## 执行基于证据的结论

当 Qwen Code 从事高风险工作时——代码审查、事件故障排除、审计或调查生产数据——一个自信但错误的结论比没有结论的代价更高。您可以在 QWEN.md 中对团队的验证策略进行编码（请参阅 [内存](<Qwen Code 项目指令与记忆.md>)），以便它适用于每个会话，而无需更改任何人的默认值。

以下规则改编自多 agent 部署的生产回顾，其中自信但错误的结论反复追溯到相同的故障模式：从代码推断状态而不是查询它，将一个满足的条件视为证据，并将先前的陈述视为事实重复。

**1。向您的项目添加验证策略 QWEN.md**

将此模板放入存储库根目录的 `QWEN.md` 中并提交，以便该策略适用于整个团队：

```markdown
# Verification policy

Treat these rules as mandatory in investigations, troubleshooting, reviews, and any task that draws conclusions:

1. Verify before concluding. Back claims about data or system state with evidence from the authoritative source (database, API, logs, command output). Conclusions drawn from reading code alone must be labeled "unverified inference".
2. Verify the full chain. Enumerate the causal chain and check every link; one satisfied necessary condition does not prove a conclusion.
3. Treat conversation history as leads, not facts. Statements in earlier transcripts — including your own prior assertions — are leads. Re-verify them at the source before repeating them as facts.
4. Troubleshoot in order: your own recent changes first, then docs and known issues, and only then external dependencies.
5. 100% evidence rule in reviews. In review and audit tasks, report no conclusion without evidence; attach verifiable evidence (file:line, query result, log excerpt) to every claim.
```

**2。选择合适的范围**

请参阅 [在哪里创建 QWEN.md](<Qwen Code 项目指令与记忆.md#在哪里创建qwenmd>) 哪个文件适用于谁 — 提交项目根 `QWEN.md` 副本，以便策略覆盖整个团队。

> [！提示]
>
> - 保持规则具体且可操作：“用查询结果支持每个声明”比“小心”更有效。
> - 保持策略简短——QWEN.md 简洁时更可靠（参见 [Memory](<Qwen Code 项目指令与记忆.md>)）。
> - 使规则适应您的堆栈：命名您的团队视为权威来源的实际查询工具、仪表板或日志命令。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Common workflows

> Learn about common workflows with Qwen Code.

Each task in this document includes clear instructions, example commands, and best practices to help you get the most from Qwen Code.

## Understand new codebases

### Get a quick codebase overview

Suppose you've just joined a new project and need to understand its structure quickly.

**1. Navigate to the project root directory**

```bash
cd /path/to/project
```

**2. Start Qwen Code**

```bash
qwen
```

**3. Ask for a high-level overview**

```
give me an overview of this codebase
```

**4. Dive deeper into specific components**

```
explain the main architecture patterns used here
```

```
what are the key data models?
```

```
how is authentication handled?
```

> [!tip]
>
> - Start with broad questions, then narrow down to specific areas
> - Ask about coding conventions and patterns used in the project
> - Request a glossary of project-specific terms

### Find relevant code

Suppose you need to locate code related to a specific feature or functionality.

**1. Ask Qwen Code to find relevant files**

```
find the files that handle user authentication
```

**2. Get context on how components interact**

```
how do these authentication files work together?
```

**3. Understand the execution flow**

```
trace the login process from front-end to database
```

> [!tip]
>
> - Be specific about what you're looking for
> - Use domain language from the project

## Fix bugs efficiently

Suppose you've encountered an error message and need to find and fix its source.

**1. Share the error with Qwen Code**

```
I'm seeing an error when I run npm test
```

**2. Ask for fix recommendations**

```
suggest a few ways to fix the @ts-ignore in user.ts
```

**3. Apply the fix**

```
update user.tsto add the null check you suggested
```

> [!tip]
>
> - Tell Qwen Code the command to reproduce the issue and get a stack trace
> - Mention any steps to reproduce the error
> - Let Qwen Code know if the error is intermittent or consistent

## Refactor code

Suppose you need to update old code to use modern patterns and practices.

**1. Identify legacy code for refactoring**

```
find deprecated API usage in our codebase
```

**2. Get refactoring recommendations**

```
suggest how to refactor utils.js to use modern JavaScript features
```

**3. Apply the changes safely**

```
refactor utils.js to use ES 2024 features while maintaining the same behavior
```

**4. Verify the refactoring**

```
run tests for the refactored code
```

> [!tip]
>
> - Ask Qwen Code to explain the benefits of the modern approach
> - Request that changes maintain backward compatibility when needed
> - Do refactoring in small, testable increments

## Use specialized subagents

Suppose you want to use specialized AI subagents to handle specific tasks more effectively.

**1. View available subagents**

```
/agents
```

This shows all available subagents and lets you create new ones.

**2. Use subagents automatically**

Qwen Code automatically delegates appropriate tasks to specialized subagents:

```
review my recent code changes for security issues
```

```
run all tests and fix any failures
```

**3. Explicitly request specific subagents**

```
use the code-reviewer subagent to check the auth module
```

```
have the debugger subagent investigate why users can't log in
```

**4. Create custom subagents for your workflow**

```
/agents
```

Then select "create" and follow the prompts to define:

- A unique identifier that describes the subagent's purpose (for example, `code-reviewer`, `api-designer`).
- When Qwen Code should use this agent
- Which tools it can access
- A system prompt describing the agent's role and behavior

> [!tip]
>
> - Create project-specific subagents in `.qwen/agents/` for team sharing
> - Use descriptive `description` fields to enable automatic delegation
> - Limit tool access to what each subagent actually needs
> - Know more about [Sub Agents](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/sub-agents>)
> - Know more about [Approval Mode](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/approval-mode>)

## Work with tests

Suppose you need to add tests for uncovered code.

**1. Identify untested code**

```
find functions in NotificationsService.swift that are not covered by tests
```

**2. Generate test scaffolding**

```
add tests for the notification service
```

**3. Add meaningful test cases**

```
add test cases for edge conditions in the notification service
```

**4. Run and verify tests**

```
run the new tests and fix any failures
```

Qwen Code can generate tests that follow your project's existing patterns and conventions. When asking for tests, be specific about what behavior you want to verify. Qwen Code examines your existing test files to match the style, frameworks, and assertion patterns already in use.

For comprehensive coverage, ask Qwen Code to identify edge cases you might have missed. Qwen Code can analyze your code paths and suggest tests for error conditions, boundary values, and unexpected inputs that are easy to overlook.

## Create pull requests

Suppose you need to create a well-documented pull request for your changes.

**1. Summarize your changes**

```
summarize the changes I've made to the authentication module
```

**2. Generate a pull request with Qwen Code**

```
create a pr
```

**3. Review and refine**

```
enhance the PR description with more context about the security improvements
```

**4. Add testing details**

```
add information about how these changes were tested
```

> [!tip]
>
> - Ask Qwen Code directly to make a PR for you
> - Review Qwen Code's generated PR before submitting
> - Ask Qwen Code to highlight potential risks or considerations

## Handle documentation

Suppose you need to add or update documentation for your code.

**1. Identify undocumented code**

```
find functions without proper JSDoc comments in the auth module
```

**2. Generate documentation**

```
add JSDoc comments to the undocumented functions in auth.js
```

**3. Review and enhance**

```
improve the generated documentation with more context and examples
```

**4. Verify documentation**

```
check if the documentation follows our project standards
```

> [!tip]
>
> - Specify the documentation style you want (JSDoc, docstrings, etc.)
> - Ask for examples in the documentation
> - Request documentation for public APIs, interfaces, and complex logic

## Reference files and directories

Use `@` to quickly include files or directories without waiting for Qwen Code to read them.

**1. Reference a single file**

```
Explain the logic in @src/utils/auth.js
```

This includes the full content of the file in the conversation.

**2. Reference a directory**

```
What's the structure of @src/components?
```

This provides a directory listing with file information.

**3. Reference MCP resources**

```
Show me the data from @github: repos/owner/repo/issues
```

This fetches data from connected MCP servers using the format @server: resource. See [MCP](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/mcp>) for details.

> [!tip]
>
> - File paths can be relative or absolute
> - @ file references add `QWEN.md` in the file's directory and parent directories to context
> - Directory references show file listings, not contents
> - You can reference multiple files in a single message (for example, "`@file 1.js` and `@file 2.js`")

## Resume previous conversations

Suppose you've been working on a task with Qwen Code and need to continue where you left off in a later session.

Qwen Code provides two options for resuming previous conversations:

- `--continue` to automatically continue the most recent conversation
- `--resume` to display a conversation picker

**1. Continue the most recent conversation**

```bash
qwen --continue
```

This immediately resumes your most recent conversation without any prompts.

**2. Continue in non-interactive mode**

```bash
qwen --continue -p "Continue with my task"
```

Use `-p` (or `--prompt`) with `--continue` to resume the most recent conversation in non-interactive mode, perfect for scripts or automation.

**3. Show conversation picker**

```bash
qwen --resume
```

This displays an interactive conversation selector with a clean list view showing:

- Session summary (or initial prompt)
- Metadata: time elapsed, message count, and git branch

Use arrow keys to navigate and press Enter to select a conversation. Press Esc to exit.

> [!tip]
>
> - Conversation history is stored locally on your machine
> - Use `--continue` for quick access to your most recent conversation
> - Use `--resume` when you need to select a specific past conversation
> - When resuming, you'll see the entire conversation history before continuing
> - The resumed conversation starts with the same model and configuration as the original
>
> **How it works**:
>
> 1. **Conversation Storage**: All conversations are automatically saved locally with their full message history
> 2. **Message Deserialization**: When resuming, the entire message history is restored to maintain context
> 3. **Tool State**: Tool usage and results from the previous conversation are preserved
> 4. **Context Restoration**: The conversation resumes with all previous context intact
>
> **Examples**:
>
> ```bash
> # Continue most recent conversation
> qwen --continue
>
> # Continue most recent conversation with a specific prompt
> qwen --continue -p "Show me our progress"
>
> # Show conversation picker
> qwen --resume
>
> # Continue most recent conversation in non-interactive mode
> qwen --continue -p "Run the tests again"
> ```

## Run parallel Qwen Code sessions with Git worktrees

Suppose you need to work on multiple tasks simultaneously with complete code isolation between Qwen Code instances.

**1. Understand Git worktrees**

Git worktrees allow you to check out multiple branches from the same repository into separate directories. Each worktree has its own working directory with isolated files, while sharing the same Git history. Learn more in the [official Git worktree documentation](<https://git-scm.com/docs/git-worktree>).

**2. Create a new worktree**

```bash
# Create a new worktree with a new branch
git worktree add ../project-feature-a -b feature-a

# Or create a worktree with an existing branch
git worktree add ../project-bugfix bugfix-123
```

This creates a new directory with a separate working copy of your repository.

**3. Run Qwen Code in each worktree**

```bash
# Navigate to your worktree
cd ../project-feature-a

# Run Qwen Code in this isolated environment
qwen
```

**4. Run Qwen Code in another worktree**

```bash
cd ../project-bugfix
qwen
```

**5. Manage your worktrees**

```bash
# List all worktrees
git worktree list

# Remove a worktree when done
git worktree remove ../project-feature-a
```

> [!tip]
>
> - Each worktree has its own independent file state, making it perfect for parallel Qwen Code sessions
> - Changes made in one worktree won't affect others, preventing Qwen Code instances from interfering with each other
> - All worktrees share the same Git history and remote connections
> - For long-running tasks, you can have Qwen Code working in one worktree while you continue development in another
> - Use descriptive directory names to easily identify which task each worktree is for
> - Remember to initialize your development environment in each new worktree according to your project's setup. Depending on your stack, this might include:
>   - JavaScript projects: Running dependency installation (`npm install`, `yarn`)
>   - Python projects: Setting up virtual environments or installing with package managers
>   - Other languages: Following your project's standard setup process

## Use Qwen Code as a unix-style utility

### Add Qwen Code to your verification process

Suppose you want to use Qwen Code as a linter or code reviewer.

**Add Qwen Code to your build script:**

```json
// package.json
{
    ...
    "scripts": {
        ...
        "lint:Qwen Code": "qwen -p 'you are a linter. please look at the changes vs. main and report any issues related to typos. report the filename and line number on one line, and a description of the issue on the second line. do not return any other text.'"
    }
}
```

> [!tip]
>
> - Use Qwen Code for automated code review in your CI/CD pipeline
> - Customize the prompt to check for specific issues relevant to your project
> - Consider creating multiple scripts for different types of verification

### Pipe in, pipe out

Suppose you want to pipe data into Qwen Code, and get back data in a structured format.

**Pipe data through Qwen Code:**

```bash
cat build-error.txt | qwen -p 'concisely explain the root cause of this build error' > output.txt
```

> [!tip]
>
> - Use pipes to integrate Qwen-Code into existing shell scripts
> - Combine with other Unix tools for powerful workflows
> - Consider using --output-format for structured output

### Control output format

Suppose you need Qwen Code's output in a specific format, especially when integrating Qwen Code into scripts or other tools.

**1. Use text format (default)**

```bash
cat data.txt | qwen -p 'summarize this data' --output-format text > summary.txt
```

This outputs just Qwen Code's plain text response (default behavior).

**2. Use JSON format**

```bash
cat code.py | qwen -p 'analyze this code for bugs' --output-format json > analysis.json
```

This outputs a JSON array of messages with metadata including cost and duration.

**3. Use streaming JSON format**

```bash
cat log.txt | qwen -p 'parse this log file for errors' --output-format stream-json
```

This outputs a series of JSON objects in real-time as Qwen Code processes the request. Each message is a valid JSON object, but the entire output is not valid JSON if concatenated.

> [!tip]
>
> - Use `--output-format text` for simple integrations where you just need Qwen Code's response
> - Use `--output-format json` when you need the full conversation log
> - Use `--output-format stream-json` for real-time output of each conversation turn

## Ask Qwen Code about its capabilities

Qwen Code has built-in access to its documentation and can answer questions about its own features and limitations.

### Example questions

```
can Qwen Code create pull requests?
```

```
how does Qwen Code handle permissions?
```

```
what slash commands are available?
```

```
how do I use MCP with Qwen Code?
```

```
how do I configure Qwen Code for Amazon Bedrock?
```

```
what are the limitations of Qwen Code?
```

> [!note]
>
> Qwen Code provides documentation-based answers to these questions. For executable examples and hands-on demonstrations, refer to the specific workflow sections above.

> [!tip]
>
> - Qwen Code always has access to the latest Qwen Code documentation, regardless of the version you're using
> - Ask specific questions to get detailed answers
> - Qwen Code can explain complex features like MCP integration, enterprise configurations, and advanced workflows

## Enforce evidence-based conclusions

When Qwen Code does high-stakes work — code review, incident troubleshooting, audits, or investigating production data — a confident but wrong conclusion costs more than no conclusion at all. You can encode your team's verification policy in QWEN.md (see [Memory](<Qwen Code 项目指令与记忆.md>)) so it applies to every session, without changing anyone's defaults.

The rules below are adapted from a production retrospective of a multi-agent deployment, where confident-but-wrong conclusions repeatedly traced back to the same failure modes: inferring state from code instead of querying it, treating one satisfied condition as proof, and repeating earlier statements as facts.

**1. Add a verification policy to your project QWEN.md**

Put this template in `QWEN.md` at your repository root and commit it, so the policy applies to the whole team:

```markdown
# Verification policy

Treat these rules as mandatory in investigations, troubleshooting, reviews, and any task that draws conclusions:

1. Verify before concluding. Back claims about data or system state with evidence from the authoritative source (database, API, logs, command output). Conclusions drawn from reading code alone must be labeled "unverified inference".
2. Verify the full chain. Enumerate the causal chain and check every link; one satisfied necessary condition does not prove a conclusion.
3. Treat conversation history as leads, not facts. Statements in earlier transcripts — including your own prior assertions — are leads. Re-verify them at the source before repeating them as facts.
4. Troubleshoot in order: your own recent changes first, then docs and known issues, and only then external dependencies.
5. 100% evidence rule in reviews. In review and audit tasks, report no conclusion without evidence; attach verifiable evidence (file:line, query result, log excerpt) to every claim.
```

**2. Choose the right scope**

See [Where to create QWEN.md](<Qwen Code 项目指令与记忆.md#where-to-create-qwenmd>) for which file applies to whom — commit the project-root `QWEN.md` copy so the policy covers the whole team.

> [!tip]
>
> - Keep the rules specific and actionable: "back every claim with a query result" works better than "be careful".
> - Keep the policy short — QWEN.md is followed more reliably when it is concise (see [Memory](<Qwen Code 项目指令与记忆.md>)).
> - Adapt the rules to your stack: name the actual query tools, dashboards, or log commands your team treats as authoritative sources.


</details>
