# Claude Code 最佳实践

围绕探索、规划、编码和验证给出操作建议，涵盖清晰任务、项目规则、工具、权限、会话管理与并行协作，并说明常见失败模式和修正方法。

- 来源：Anthropic／Claude Code
- 资料类型：存档文档
- 文档时间：官网现页更新：[2026-10-06](<https://code.claude.com/docs/en/best-practices>)；本地抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://code.claude.com/docs/en/best-practices.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude Code/Claude Code 最佳实践.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

> ## 文档索引
> 在以下位置获取完整的文档索引： https://code.claude.com/docs/llms.txt
> 在进一步探索之前使用此文件来发现所有可用页面。

# Claude Code 的最佳实践

> 充分利用 Claude Code 的提示和模式，从配置环境到跨并行会话进行扩展。

Claude Code 是一个代理编码环境。与回答问题和等待的聊天机器人不同，Claude Code 可以在您观看、重定向或完全离开时读取您的文件、运行命令、进行更改并自主解决问题。

这会改变你的工作方式。您不必自己编写代码并要求 Claude 审查它，而是描述您想要的内容，然后 Claude 弄清楚如何构建它。 Claude 探索、计划和实施。

但这种自主权仍然有一个学习曲线。 Claude 在您需要了解的某些限制下工作。

本指南涵盖了已在 Anthropic 内部团队以及跨各种代码库、语言和环境使用 Claude Code 的工程师证明有效的模式。有关代理循环的工作原理，请参阅[Claude Code如何工作](<https://code.claude.com/docs/en/how-claude-code-works>)。

***

大多数最佳实践都基于一个约束：Claude 的上下文窗口很快就会填满，并且性能会随着填满而降低。

Claude 的上下文窗口保存您的整个对话，包括每条消息、Claude 读取的每个文件以及每个命令输出。然而，这很快就会被填满。单个调试会话或代码库探索可能会生成并消耗数万个令牌。

这很重要，因为 LLM 性能会随着上下文填充而降低。当上下文窗口变满时，Claude可能会开始“忘记”之前的指令或犯更多错误。上下文窗口是最重要的管理资源。要了解会话在实践中如何填满，[观看交互式演练](<https://code.claude.com/docs/en/context-window>)，了解启动时加载的内容以及每个文件读取的成本。使用 [ 自定义状态行 ](<https://code.claude.com/docs/en/statusline>) 持续跟踪上下文使用情况，并参阅 [ 减少令牌使用 ](<https://code.claude.com/docs/en/costs#reduce-token-usage>) 了解减少令牌使用的策略。

***

## 给Claude一种验证其工作的方法



**提示：**

给Claude一个它可以运行的检查：测试、构建、要比较的屏幕截图。这就是您观看的一场会议和您离开的一场会议之间的区别。


当工作看起来完成时，Claude 停止。如果没有检查，它就可以运行，“看起来完成”是唯一可用的信号，并且您成为验证循环：每个错误都等待您注意到它。给Claude一些产生通过或失败的东西，循环会自行关闭。 Claude 执行工作、运行检查、读取结果并迭代，直到检查通过。

检查是返回信号 Claude 可以在对话中读取的任何内容：测试套件、构建退出代码、linter、根据固定装置比较输出的脚本，或与设计相比的 [ 浏览器屏幕截图 ](<https://code.claude.com/docs/en/chrome>)。在 Claude 的检查通过后自行运行 [`/verify`](<https://code.claude.com/docs/en/skills#run-and-verify-your-app>) 以确认对正在运行的应用程序的更改。

|策略|之前|之后|
| - | - | - |
|**提供验证标准**|*“实现验证电子邮件地址的功能”*|*“编写一个validateEmail函数。示例测试用例：[user@example.com](<mailto:user@example.com>)为真，无效为假，[user@.com](<mailto:user@.com>)为假。之后运行测试实施“*|
|**验证UI视觉变化**|*“让仪表盘看起来更好”*|*“\[粘贴屏幕截图]实现此设计。对结果进行屏幕截图并将其与原始结果进行比较。列出差异并修复它们”*|
|**解决根本原因，而不是症状**|*“构建失败”*|*“构建失败并出现此错误：\[粘贴错误]。修复它并验证构建是否成功。解决根本原因，不要抑制错误”*|

一旦支票存在，决定止损的难度：

* **在一个提示中**：要求Claude在同一消息中运行检查并迭代，如上表所示。
* **跨会话**：将检查设置为[`/goal`条件](<https://code.claude.com/docs/en/goal>)。每个回合后都有一个单独的评估员重新检查，并且 Claude 继续工作直到目标解决。如果 Claude 停止，Claude Code 最终会停止运行，目标仍然设定 — 请参阅 [ /goal 评估如何工作 ](<https://code.claude.com/docs/en/goal#how-evaluation-works>)。
* **作为确定性门**：[停止钩子](<https://code.claude.com/docs/en/hooks#stop>)将你的检查作为脚本运行，并阻止回合结束，直到它通过。 [停止输入](<https://code.claude.com/docs/en/hooks#stop-input>)覆盖连续块上的上限。
* **第二意见**：一个[验证子代理](<https://code.claude.com/docs/en/sub-agents>)或一个[检查其自己的发现的动态工作流程](<https://code.claude.com/docs/en/workflows>)有一个新的模型试图反驳结果，所以agent做作业并不是给作业打分的。

每一步都以设置换取注意力。提示版本适用于当今的任何任务。 `/goal` 和 Stop 挂钩版本可以让无人值守的运行在没有您的情况下正确完成。

让 Claude 显示证据而不是断言成功：测试输出、运行的命令及其返回的内容或结果的屏幕截图。查看证据比您自己重新运行验证更快，并且它适用于您没有观看的会议。

***

## 先探索，然后计划，最后编码



**提示：**

将研究和规划与实施分开，以避免解决错误的问题。


让 Claude 直接跳转到编码可能会生成解决错误问题的代码。使用[计划模式](<https://code.claude.com/docs/en/permission-modes#analyze-before-you-edit-with-plan-mode>)将探索与执行分开。

推荐的工作流程分为四个阶段：



  

#### 探索

按 `Shift+Tab` 进入计划模式，直到状态栏显示 `⏸ plan mode on`，或使用 `claude --permission-mode plan` 启动会话。 Claude 读取文件并回答问题而不进行更改。

    ```txt title="claude (plan mode)" wrap theme={null}
    read /src/auth and understand how we handle sessions and login.
    also look at how we manage environment variables for secrets.
    ```
  


  

#### 计划

请Claude制定详细的实施计划。

    ```txt title="claude (plan mode)" wrap theme={null}
    I want to add Google OAuth. What files need to change?
    What's the session flow? Create a plan.
    ```

在 Claude 继续之前，按 `Ctrl+G` 在文本编辑器中打开计划以进行直接编辑。
  


  

#### 实施

通过批准计划或按 `Shift+Tab` 退出计划模式，然后按 Claude code，验证其计划。

    ```txt title="claude" wrap theme={null}
    implement the OAuth flow from your plan. write tests for the
    callback handler, run the test suite and fix any failures.
    ```
  


  

#### 提交

要求 Claude 提交一条描述性消息并创建一个 PR。

    ```txt title="claude" wrap theme={null}
    commit with a descriptive message and open a PR
    ```
  




**说明：**

计划模式很有用，但也会增加开销。

对于范围明确且修复量较小的任务（例如修复拼写错误、添加日志行或重命名变量），请直接要求 Claude 执行。

当您不确定方法、更改修改多个文件或不熟悉所修改的代码时，规划最有用。如果您可以用一句话描述差异，请跳过该计划。


***

## 在提示中提供具体上下文



**提示：**

您的指示越精确，您需要的纠正就越少。


Claude可以推断意图，但它无法读懂你的想法。参考特定文件、提及约束并指出示例模式。

|策略|之前|之后|
| - | - | - |
|**确定任务范围。**指定哪个文件、什么场景以及测试首选项。|*“添加foo.py测试”*|*“为foo.py编写一个测试，涵盖用户注销的边缘情况。避免模拟。”*|
|**指向来源。**直接Claude指向可以回答问题的来源。|*“为什么ExecutionFactory有这么奇怪的API？”*|*“浏览 ExecutionFactory 的 git 历史记录并总结其 api 是如何形成的”*|
|**引用现有模式。**将Claude指向代码库中的模式。|*“添加日历小部件”*|*“看看现有的小部件是如何在主页上实现的，以了解这些模式。HotDogWidget.php 是一个很好的例子。遵循该模式来实现一个新的日历小部件，让用户选择月份并分页 forwards/backwards 以选择年份。从头开始构建，除了代码库中已使用的库之外，无需其他库。”*|
|**描述症状。**提供症状、可能的位置以及“固定”的样子。|*“修复登录错误”*|*“用户报告会话超时后登录失败。检查src/auth/中的身份验证流程，尤其是令牌刷新。编写重现该问题的失败测试，然后修复它”*|

当您正在探索并且有能力纠正路线时，模糊的提示可能会很有用。像 `"what would you improve in this file?"` 这样的提示可以显示您不会想到询问的事情。

### 提供丰富的内容



**提示：**

使用`@`引用文件、粘贴screenshots/images或直接管道数据。


您可以通过多种方式向Claude提供丰富的数据：

* **使用`@`**引用文件，而不是描述代码所在的位置。 Claude 在响应之前读取文件。
* **直接粘贴图像**。 Copy/paste 或将图像拖放到提示中。
* **提供文档和 API 参考资料的 URL **。使用 `/permissions` 将常用域列入白名单。
* **通过运行`cat error.log | claude -p "explain this error"`传入数据**，直接发送文件内容。
* **让Claude获取它需要的东西**。告诉 Claude 使用 Bash 命令、MCP 工具或通过读取文件来提取上下文本身。

***

## 配置您的环境

几个设置步骤可以使 Claude Code 在您的所有会话中显着更加有效。有关扩展功能以及何时使用每一项功能的完整概述，请参阅 [扩展 Claude Code](<https://code.claude.com/docs/en/features-overview>)。

### 写一个有效的CLAUDE.md



**提示：**

运行 `/init` 以根据您当前的项目结构生成入门 CLAUDE.md 文件，然后随着时间的推移进行完善。


CLAUDE.md 是 Claude 在每次对话开始时读取的特殊文件。包括 Bash 命令、代码风格和工作流程规则。这提供了 Claude 持久上下文，它无法单独从代码推断。

CLAUDE.md 文件没有必需的格式，但请保持简短且易于阅读。例如：

```markdown CLAUDE.md theme={null}
# Code style
- Use ES modules (import/export) syntax, not CommonJS (require)
- Destructure imports when possible (eg. import { foo } from 'bar')

# Workflow
- Be sure to typecheck when you're done making a series of code changes
- Prefer running single tests, and not the whole test suite, for performance
```

运行`/context`以确认Claude已加载文件。 CLAUDE.md 每次会话都会加载，因此仅包含广泛适用的内容。对于仅有时相关的领域知识或工作流程，请改用 [ 技能 ](<https://code.claude.com/docs/en/skills>)。 Claude 按需加载它们，不会让每个对话都变得臃肿。

保持简洁。对于每一行，询问：*“删除此行会导致Claude出错吗？”*如果不是，则将其删除。臃肿的 CLAUDE.md 文件导致 Claude 忽略您的实际指令！

|✅ 包括|❌排除|
| - | - |
|Bash 命令 Claude 无法猜测|任何事情Claude都可以通过阅读代码弄清楚|
|与默认值不同的代码样式规则|标准语言约定 Claude 已经知道|
|测试说明和首选测试运行者|详细的 API 文档（改为链接到文档）|
|存储库礼仪（分支命名，PR 约定）|经常变化的信息|
|针对您的项目的架构决策|长解释或教程|
|开发人员环境怪癖（必需的环境变量）|代码库的逐文件描述|
|常见的陷阱或不言而喻的行为|不言而喻的做法，例如“编写干净的代码”|

如果 Claude 尽管有规则反对，但仍继续做您不想要的事情，则文件可能太长并且规则丢失了。如果 Claude 询问您的问题在 CLAUDE.md 中得到了回答，则措辞可能不明确。像对待代码一样对待 CLAUDE.md：当出现问题时对其进行审查，定期修剪它，并通过观察 Claude 的行为是否真正发生变化来测试更改。对于签入的 CLAUDE.md，运行 [`/doctor`](<https://code.claude.com/docs/en/commands#all-commands>) 和 Claude 建议削减它可以从代码库派生的内容。

如果 Claude 不断跳过一条指令，请仅在该行添加强调，例如“IMPORTANT”。如果你强调很多行，那么没有任何一行是突出的。将 CLAUDE.md 检查到 git 中，以便您的团队可以做出贡献。随着时间的推移，该文件的价值会不断增加。

CLAUDE.md 文件可以使用 `@path/to/import` 语法导入其他文件。有关导入规则以及 CLAUDE.md 文件的存放位置，请参阅 [CLAUDE.md 文件 ](<https://code.claude.com/docs/en/memory#claude-md-files>)。

### 配置权限



**提示：**

要在不放弃控制的情况下获得更少的提示，请使用 `/permissions` 预先批准您信任的工具，并让沙盒命令运行而无需使用 `/sandbox` 进行询问。当您想要自己批准编辑和命令时，请切换到手动模式。


对于 Claude Code v2.1.283 或更高版本，自动模式是交互式终端和 VS Code 会话的 [ 内置启动权限模式 ](<https://code.claude.com/docs/en/permission-modes#eliminate-prompts-with-auto-mode>)：单独的分类器模型会代替您审查大多数操作，并仅阻止看起来有风险的操作，例如范围升级、未知基础设施或敌对内容驱动行动。在早期版本中，自动模式是仅在 Pro、Max 和 Team 计划中内置的启动权限模式。

在手动模式下，Claude Code 在可能修改系统的操作之前询问：文件写入、Bash 命令、MCP 工具。这很安全，但很乏味。第十次批准后，您将点击而不是审核。有两个工具可以在手动模式下消除这些干扰，并也适用于自动模式：

* **权限白名单**：允许您知道安全的特定工具，例如`npm run lint`或`git commit`
* **沙盒**：启用限制文件系统和网络访问的OS-级别隔离，允许Claude在定义的边界内更自由地工作

详细了解 [ 权限模式 ](<https://code.claude.com/docs/en/permission-modes>)、[ 权限规则 ](<https://code.claude.com/docs/en/permissions>) 和 [ 沙盒 ](<https://code.claude.com/docs/en/sandboxing>)。

### 使用【CLI】工具



**提示：**

告诉Claude Code在与外部服务交互时使用CLI工具，例如`gh`、`aws`、`gcloud`和`sentry-cli`。


CLI 工具是与外部服务交互的最上下文有效的方式。如果您使用GitHub，请安装`gh` CLI。 Claude 知道如何使用它来创建问题、打开拉取请求和阅读评论。如果没有 `gh`，Claude 仍然可以使用 GitHub API，但未经身份验证的请求通常会达到速率限制。

Claude 在学习它还不知道的 CLI 工具方面也很有效。尝试输入类似 `Use 'foo-cli-tool --help' to learn about foo tool, then use it to solve A, B, C.` 的提示

### 连接MCP服务器



**提示：**

使用服务器名称和 URL 运行 `claude mcp add` 或命令来连接外部工具，例如 Notion、Figma 或您的数据库。例如：`claude mcp add --transport http notion https://mcp.notion.com/mcp`。


借助 [MCP 服务器 ](<https://code.claude.com/docs/en/mcp>)，您可以要求 Claude 实现问题跟踪器的功能、查询数据库、分析监控数据、集成 Figma 的设计以及自动化工作流程。

### 设置挂钩



**提示：**

对每次都必须发生且零异常的操作使用钩子。


[Hooks](<https://code.claude.com/docs/en/hooks-guide>) 在 Claude 工作流程中的特定点自动运行脚本。与建议性的 CLAUDE.md 指令不同，挂钩是确定性的并保证操作发生。

Claude可以为您编写钩子。尝试像 *“编写一个在每次文件编辑后运行 eslint 的钩子”* 或 *“编写一个阻止写入迁移文件夹的钩子。”* 直接编辑 `.claude/settings.json` 以手动配置钩子，然后运行`/hooks` 浏览配置内容。

### 创造技能



**提示：**

在 `.claude/skills/` 中创建 `SKILL.md` 文件，以提供 Claude 领域知识和可重用的工作流程。


[技能](<https://code.claude.com/docs/en/skills>)通过特定于您的项目、团队或领域的信息扩展Claude的知识。 Claude 在相关时自动应用它们，或者您可以使用 `/skill-name` 直接调用它们。

通过将带有 `SKILL.md` 的目录添加到 `.claude/skills/` 来创建技能：

```markdown .claude/skills/api-conventions/SKILL.md theme={null}
---
name: api-conventions
description: REST API design conventions for our services
---
# API Conventions
- Use kebab-case for URL paths
- Use camelCase for JSON properties
- Always include pagination for list endpoints
- Version APIs in the URL path (/v1/, /v2/)
```

技能还可以定义您直接调用的可重复工作流程：

```markdown .claude/skills/fix-issue/SKILL.md theme={null}
---
name: fix-issue
description: Fix a GitHub issue
disable-model-invocation: true
---
Analyze and fix the GitHub issue: $ARGUMENTS.

1. Use `gh issue view` to get the issue details
2. Understand the problem described in the issue
3. Search the codebase for relevant files
4. Implement the necessary changes to fix the issue
5. Write and run tests to verify the fix
6. Ensure code passes linting and type checking
7. Create a descriptive commit message
8. Push and create a PR
```

运行`/fix-issue 1234`来调用它。对于您想要手动触发的具有副作用的工作流程，请使用 `disable-model-invocation: true`。

### 创建自定义子代理



**提示：**

在`.claude/agents/`中定义Claude可以委派独立任务的专业助理。


[子代理](<https://code.claude.com/docs/en/sub-agents>)在自己的上下文中使用自己的一组允许的工具运行。它们对于读取许多文件或需要专门关注而又不会扰乱您的主要对话的任务非常有用。

```markdown .claude/agents/security-reviewer.md theme={null}
---
name: security-reviewer
description: Reviews code for security vulnerabilities
tools: Read, Grep, Glob, Bash
model: opus
---
You are a senior security engineer. Review code for:
- Injection vulnerabilities (SQL, XSS, command injection)
- Authentication and authorization flaws
- Secrets or credentials in code
- Insecure data handling

Provide specific line references and suggested fixes.
```

告诉 Claude 显式使用子代理：*“使用子代理检查此代码是否存在安全问题。”*

### 安装插件



**提示：**

运行`/plugin`浏览市场。插件无需配置即可添加技能、工具和集成。


[插件](<https://code.claude.com/docs/en/plugins/overview>)将技能、挂钩、子代理和MCP服务器捆绑到来自社区和Anthropic的单个可安装单元中。如果您使用类型语言，请安装 [ 代码智能插件 ](<https://code.claude.com/docs/en/plugins/code-intelligence>)，以在编辑后提供 Claude 精确的符号导航和自动错误检测。

有关在技能、子代理、挂钩和 MCP 之间进行选择的指南，请参阅 [扩展 Claude Code](<https://code.claude.com/docs/en/features-overview#match-features-to-your-goal>)。

***

## 有效沟通

问 Claude 您会问其他工程师的问题，对于较大的功能，请 Claude 采访您并在开始实施之前编写规范。

### 询问代码库问题



**提示：**

问 Claude 你会问高级工程师的问题。


加入新的代码库时，请使用 Claude Code 进行学习和探索。您可以向 Claude 提出与其他工程师相同的问题：

* 日志记录如何工作？
* 如何创建新的 API 端点？
* `async move { ... }`在`foo.rs`的134线上做什么？
* `CustomerOnboardingFlowImpl` 处理哪些边缘情况？
* 为什么此代码在 333 行上调用 `foo()` 而不是 `bar()`？

以这种方式使用 Claude Code 是一种有效的入职工作流程，可以缩短启动时间并减少其他工程师的负担。无需特殊提示：直接提问。

### 让Claude采访你



**提示：**

对于较大的功能，请先让 Claude 采访您。从最简单的提示开始，然后要求 Claude 使用 `AskUserQuestion` 工具采访您。


Claude 询问您可能尚未考虑的事情，包括技术实现、UI/UX、边缘情况和权衡。在发送提示之前，将 `[brief description]` 替换为您的功能。

```text wrap theme={null}
I want to build [brief description]. Interview me in detail using the AskUserQuestion tool.

Ask about technical implementation, UI/UX, edge cases, concerns, and tradeoffs. Don't ask obvious questions, dig into the hard parts I might not have considered.

Keep interviewing until we've covered everything, then write a complete spec to SPEC.md.
```

规范完成后，启动一个新会话来执行它。新的会议有干净的背景，完全专注于实施，并且您有一份书面规范可供参考。

最有用的规范是独立的：它们命名所涉及的文件和接口，说明超出范围的内容，并以证明该功能有效的端到端验证步骤结束。花在使规范精确化上的时间比花在观察实施上的时间更有价值。

***

## 管理您的会话

对话是持续且可逆的。利用这一点来发挥你的优势！

### 尽早且经常纠正航向



**提示：**

一旦发现 Claude 偏离轨道，请立即纠正。


最好的结果来自紧密的反馈循环。虽然 Claude 有时会在第一次尝试时完美解决问题，但快速纠正通常会更快地产生更好的解决方案。

* **`Esc`**：使用`Esc`键停止Claude动作。上下文被保留，因此您可以重定向。
* **`Esc + Esc`或`/rewind`**：按两次`Esc`或运行`/rewind`打开快退菜单并恢复之前的对话和代码状态，或从所选消息中进行摘要。
* **`"Undo that"`**：让Claude恢复其更改。
* **`/clear`**：重置不相关任务之间的上下文。具有不相关上下文的长时间会话会降低性能。

如果您在一次会话中就同一问题纠正 Claude 两次以上，则上下文会因失败的方法而变得混乱。运行 `/clear` 并根据包含您学到的内容的更具体的提示重新开始。带有更好提示的干净会话几乎总是胜过带有累积修正的长时间会话。

### 积极管理环境



**提示：**

在不相关的任务之间运行 `/clear` 以重置上下文。


Claude Code 当您接近上下文限制时，会自动压缩对话历史记录，从而在释放空间的同时保留重要的代码和决策。

在长时间会话期间，Claude 的上下文窗口可能会充满不相关的对话、文件内容和命令。这会降低性能，有时还会分散注意力 Claude。

* 在任务之间频繁使用 `/clear` 来完全重置上下文窗口
* 当自动压缩触发时，Claude 总结最重要的内容，包括代码模式、文件状态和关键决策
* 要获得更多控制，请运行 `/compact <instructions>`，例如 `/compact Focus on the API changes`
* 要仅压缩对话的一部分，请使用 `Esc + Esc` 或 `/rewind`，选择消息检查点，然后选择 **从此处汇总** 或 **从此处汇总**。第一个压缩了从该点开始的信息，同时保持了先前的上下文完整；第二个压缩了早期的信息，同时完整地保留了最近的信息。请参阅[快退菜单的摘要选项](<https://code.claude.com/docs/en/checkpointing#rewind-and-summarize>)。
* 使用 `"When compacting, always preserve the full list of modified files and any test commands"` 等指令自定义 CLAUDE.md 中的压缩行为，以确保关键上下文在摘要中幸存下来
* 对于不需要停留在上下文中的问题，请使用 [`/btw`](<https://code.claude.com/docs/en/interactive-mode#side-questions-with-%2Fbtw>)。答案永远不会进入对话历史记录，因此您可以在不了解上下文的情况下检查详细信息。

### 使用子代理进行调查



**提示：**

与 `"use subagents to investigate X"` 委托研究。他们在单独的上下文中进行探索，使您的主要对话保持清晰以便于实施。


由于上下文是您的基本约束，因此请使用子代理来阻止研究。当 Claude 研究代码库时，它会读取大量文件，所有这些文件都会消耗您的上下文。子代理在单独的上下文窗口中运行并报告摘要：

```text wrap theme={null}
Use subagents to investigate how our authentication system handles token
refresh, and whether we have any existing OAuth utilities I should reuse.
```

您还可以在Claude实现某些功能后使用子代理进行验证。请参阅[添加对抗性审查步骤](<#添加对抗性审查步骤>)。

### 通过检查点倒回



**提示：**

您发送的每个开始回合的提示都会创建一个检查点。您可以将对话、代码或两者恢复到任何先前的检查点。


Claude 在每次更改之前自动创建文件快照，以便检查点可以恢复它们。双击`Escape`或运行`/rewind`打开快退菜单。您可以仅恢复对话、仅恢复代码、恢复两者或从选定的消息中进行摘要。有关详细信息，请参阅[检查点](<https://code.claude.com/docs/en/checkpointing>)。

您可以告诉Claude尝试一些有风险的事情，而不是仔细计划每一步。如果不起作用，请倒带并尝试其他方法。检查点与对话一起保存，因此您可以关闭终端，稍后恢复会话，并且仍然可以倒带。



**注意：**

检查点仅跟踪通过Claude的文件编辑工具所做的更改。不会捕获通过 Bash 命令或外部进程所做的更改。这不是 git 的替代品。


### 恢复对话



**提示：**

使用 `/rename` 命名会话并将它们视为分支：每个工作流都有自己的持久上下文。


Claude Code 在本地保存对话，因此当任务跨越多个会议时，您无需重新解释上下文。运行 [`claude --continue`](<https://code.claude.com/docs/en/sessions#resume-a-session>) 从上次停下的地方继续，或者运行 `claude --resume` 从列表中进行选择。为会话提供描述性名称，例如 `oauth-migration`，以便您稍后可以找到它们。有关完整的恢复、分支和命名控件集，请参阅[管理会话](<https://code.claude.com/docs/en/sessions>)。

***

## 自动化和规模化

一旦您有效地使用了一个 Claude，就可以通过并行会话、非交互模式和扇出模式来增加您的输出。

### 运行非交互模式



**提示：**

在 CI、预提交挂钩或脚本中使用 `claude -p "prompt"`。添加 `--output-format stream-json --verbose` 用于流式传输 JSON 输出。


使用`claude -p "your prompt"`，您可以非交互方式运行Claude，无需交互提示。除非您通过 `--no-session-persistence`，否则运行仍会创建可恢复会话。 [非交互模式](<https://code.claude.com/docs/en/headless>)是您将Claude集成到CI管道、预提交挂钩或任何自动化工作流程中的方式。输出格式允许您以编程方式解析结果：纯文本、JSON 或流式传输 JSON。

```bash theme={null}
# One-off queries
claude -p "Explain what this project does"

# Structured output for scripts
claude -p "List all API endpoints" --output-format json

# Streaming for real-time processing
claude -p "Analyze this log file" --output-format stream-json --verbose
```

第一个命令打印纯文本。 `json` 格式返回带有 `result` 字段的单个 JSON 对象。 `stream-json` 格式每行打印一个 JSON 对象，从 init 事件开始。

### 运行多个 Claude 会话



**提示：**

并行运行多个 Claude 会话以加快开发速度、运行独立实验或启动复杂的工作流程。


选择适合您想要自己进行协调程度的并行方法，并在会话需要在会话之间传递结果时添加消息传递：

* [Worktrees](<https://code.claude.com/docs/en/worktrees>)：在隔离的 git 签出中运行单独的 CLI 会话，以便编辑不会发生冲突
* [跨会话消息传递](<https://code.claude.com/docs/en/cross-session-messaging>)：让您自己运行的会话相互传递发现结果
* [桌面应用程序](<https://code.claude.com/docs/en/desktop#work-in-parallel-with-sessions>)：直观地管理多个本地会话，可以选择每个会话都在自己的工作树中
* [在云中使用 Claude Code](<https://code.claude.com/docs/en/claude-code-on-the-web>)：默认在 Anthropic 托管基础架构上运行会话
* [Agent视图](<https://code.claude.com/docs/en/agent-view>)：研究预览。运行 `claude agents` 调度在后台持续运行的会话并从一个屏幕观看它们
* [Agent 团队](<https://code.claude.com/docs/en/agent-teams>)：实验性且默认禁用。通过共享任务、消息传递和团队领导自动协调多个会话

除了并行工作之外，多个会话还可以实现以质量为中心的工作流程。新的上下文可以改善代码审查，因为 Claude 不会偏向于它刚刚编写的代码。

例如，使用 Writer/Reviewer 模式：

|会议A（作家）|会议B（审稿人）|
| - | - |
| `Implement a rate limiter for our API endpoints` | |
| | `Review the rate limiter implementation in @src/middleware/rateLimiter.ts. Look for edge cases, race conditions, and consistency with our existing middleware patterns.` |
| `Here's the review feedback: [Session B output]. Address these issues.` | |

您可以对测试执行类似的操作：让一个 Claude 编写测试，然后另一个编写代码来通过测试。

### 跨文件扇出



**提示：**

循环调用每个任务 `claude -p`。使用`--allowedTools`预先批准批量操作的工具。


对于大型迁移或分析，您可以在许多并行 Claude 调用之间分配工作。运行 [`/batch <instruction>`](<https://code.claude.com/docs/en/commands#all-commands>) 以使 Claude 将更改拆分到 5 到 30 子代理。每个子代理在其自己的工作树中工作。要从您自己的脚本驱动扇出，请循环 `claude -p`：



  

#### 生成任务列表

让Claude将需要迁移的文件列表写入到一个文件中，以便下一步的循环可以读取它，并有类似`list all 2,000 Python files that need migrating and save the list to files.txt`的提示
  


  

#### 编写一个脚本来循环遍历列表

    ```bash theme={null}
    for file in $(cat files.txt); do
      claude -p "Migrate $file from Python 2 to Python 3. Return OK or FAIL." \
        --allowedTools "Edit,Bash(git commit *)" \
        --permission-mode dontAsk
    done
    ```
  


  

#### 对几个文件进行测试，然后对所有文件运行

根据第一个 2-3 文件的问题来优化提示，然后运行全套文件。 `--allowedTools` 标志预先批准迁移所需的工具，而 [`--permission-mode dontAsk`](<https://code.claude.com/docs/en/permission-modes#allow-only-pre-approved-tools-with-dontask-mode>) 拒绝任何其他需要批准的工具，这在您无人值守的情况下运行时很重要。
  


您还可以将 Claude 集成到现有的 data/processing 管道中：

```bash theme={null}
claude -p "<your prompt>" --output-format json | your_command
```

### 以自动模式自主运行

要通过后台安全检查不间断执行，请使用[自动模式](<https://code.claude.com/docs/en/permission-modes#eliminate-prompts-with-auto-mode>)。分类器模型会在命令运行前对其进行审查，阻止范围升级、未知基础设施和敌对内容驱动的操作，同时让日常工作在没有提示的情况下继续进行。

```bash theme={null}
claude --permission-mode auto -p "fix all lint errors"
```

当分类器使用 `-p` 标志在非交互式运行中重复阻止操作时，Claude Code 不会停止运行。请参阅 [ 当自动模式回退时 ](<https://code.claude.com/docs/en/permission-modes#when-auto-mode-falls-back>) 了解会发生什么以及阈值。

### 添加对抗性审查步骤



**提示：**

在将任务视为已完成之前，让子代理在新的上下文中检查差异并报告差距。


Claude 无人值守工作的时间越长，在将工作计算为已完成之前进行独立检查就越重要。在新的 [ 子代理 ](<https://code.claude.com/docs/en/sub-agents>) 上下文中运行的审阅者只能看到差异和您给出的标准，而不是产生更改的推理，因此它根据自己的条件评估结果。

要进行正确性检查，请运行捆绑的 [`/code-review` 技能 ](<https://code.claude.com/docs/en/commands>)，该技能会检查新子代理中当前差异的错误并将结果返回到会话。要检查与您的计划的差异，请自己编写审核提示。列出要检查的工作、检查计划以及什么算作发现：

```text wrap theme={null}
Use a subagent to review the rate limiter diff against PLAN.md. Check that
every requirement is implemented, the listed edge cases have tests, and
nothing outside the task's scope changed. Report gaps, not style preferences.
```

由于审阅者作为子代理运行，因此实施会话会直接接收差距，并可以修复它们并重新审阅，而无需在窗口之间复制结果。



**说明：**

被提示寻找差距的审稿人通常会报告一些差距，即使工作是合理的，因为这就是要求做的。追逐每一个发现会导致过度设计：额外的抽象层、防御性代码以及对不可能发生的情况的测试。告诉审阅者仅标记影响正确性或规定要求的差距，并将其余部分视为可选。


***

## 避免常见的故障模式

这些都是常见的错误。尽早认识它们可以节省时间：

* **厨房水槽会议。**您从一个任务开始，然后问Claude一些不相关的事情，然后返回第一个任务。上下文充满了不相关的信息。
  > **修复**：不相关任务之间的`/clear`。
* **反复改正。**Claude做错了，你改正了，还是错，你再改正。上下文被失败的方法污染了。
  > **修复**：在两次失败的更正后，`/clear`并结合您所学到的内容编写一个更好的初始提示。
* **过度指定的CLAUDE.md。**如果你的CLAUDE.md太长，Claude会忽略一半，因为重要的规则会在噪音中丢失。
  > **修复**：无情地修剪。如果 Claude 在没有指令的情况下已经正确执行某些操作，请将其删除或将其转换为挂钩。
* **信任然后验证的差距。** Claude 产生一个看似合理的实现，但不处理边缘情况。
  > **修复**：始终提供验证（测试、脚本、屏幕截图）。如果无法验证，请勿发货。
* **无限的探索。**你要求Claude“调查”某件事而不界定它的范围。 Claude 读取数百个文件，填充上下文。
  > **修复**：缩小调查范围或使用子代理，以便探索不会消耗您的主要上下文。

***

## 培养你的直觉

本指南中的模式并不是一成不变的。它们是总体上行之有效的起点，但可能并不适合所有情况。

有时你*应该*让背景积累起来，因为你深陷一个复杂的问题而历史很有价值。有时你应该跳过计划并让Claude弄清楚，因为任务是探索性的。有时，模糊的提示是完全正确的，因为您想在限制问题之前看看 Claude 如何解释问题。

注意什么有效。当 Claude 产生出色的输出时，请注意您所做的事情：提示结构、您提供的上下文、您所处的模式。当 Claude 遇到困难时，问为什么。周围环境是否太嘈杂？提示太模糊？任务太大，无法完成一次？

随着时间的推移，您将发展出任何指南都无法捕捉到的直觉。你会知道什么时候要具体，什么时候要开放，什么时候要计划，什么时候要探索，什么时候要理清背景，什么时候要让它积累。

## 相关资源

* [Claude Code如何工作](<https://code.claude.com/docs/en/how-claude-code-works>)：代理循环、工具和上下文管理
* [扩展Claude Code](<https://code.claude.com/docs/en/features-overview>)：技能、挂钩、MCP、子代理和插件
* [常见工作流程](<https://code.claude.com/docs/en/common-workflows>)：调试、测试、PR 等的分步秘诀
* [CLAUDE.md](<https://code.claude.com/docs/en/memory>)：存储项目约定和持久上下文


## 原文对照

<details>
<summary>展开完整原文</summary>

> ## Documentation Index
> Fetch the complete documentation index at: https://code.claude.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Best practices for Claude Code

> Tips and patterns for getting the most out of Claude Code, from configuring your environment to scaling across parallel sessions.

Claude Code is an agentic coding environment. Unlike a chatbot that answers questions and waits, Claude Code can read your files, run commands, make changes, and autonomously work through problems while you watch, redirect, or step away entirely.

This changes how you work. Instead of writing code yourself and asking Claude to review it, you describe what you want and Claude figures out how to build it. Claude explores, plans, and implements.

But this autonomy still comes with a learning curve. Claude works within certain constraints you need to understand.

This guide covers patterns that have proven effective across Anthropic's internal teams and for engineers using Claude Code across various codebases, languages, and environments. For how the agentic loop works, see [How Claude Code works](<https://code.claude.com/docs/en/how-claude-code-works>).

***

Most best practices are based on one constraint: Claude's context window fills up fast, and performance degrades as it fills.

Claude's context window holds your entire conversation, including every message, every file Claude reads, and every command output. However, this can fill up fast. A single debugging session or codebase exploration might generate and consume tens of thousands of tokens.

This matters since LLM performance degrades as context fills. When the context window is getting full, Claude may start "forgetting" earlier instructions or making more mistakes. The context window is the most important resource to manage. To see how a session fills up in practice, [watch an interactive walkthrough](<https://code.claude.com/docs/en/context-window>) of what loads at startup and what each file read costs. Track context usage continuously with a [custom status line](<https://code.claude.com/docs/en/statusline>), and see [Reduce token usage](<https://code.claude.com/docs/en/costs#reduce-token-usage>) for strategies on reducing token usage.

***

## Give Claude a way to verify its work



**提示：**

  Give Claude a check it can run: tests, a build, a screenshot to compare. It's the difference between a session you watch and one you walk away from.


Claude stops when the work looks done. Without a check it can run, "looks done" is the only signal available, and you become the verification loop: every mistake waits for you to notice it. Give Claude something that produces a pass or fail, and the loop closes on its own. Claude does the work, runs the check, reads the result, and iterates until the check passes.

The check is anything that returns a signal Claude can read in the conversation: a test suite, a build exit code, a linter, a script that diffs output against a fixture, or a [browser screenshot](<https://code.claude.com/docs/en/chrome>) compared against a design. Run [`/verify`](<https://code.claude.com/docs/en/skills#run-and-verify-your-app>) yourself after Claude's check passes to confirm the change against the running app.

| Strategy | Before | After |
| - | - | - |
| **Provide verification criteria** | *"implement a function that validates email addresses"* | *"write a validateEmail function. example test cases: [user@example.com](<mailto:user@example.com>) is true, invalid is false, [user@.com](<mailto:user@.com>) is false. run the tests after implementing"* |
| **Verify UI changes visually** | *"make the dashboard look better"* | *"\[paste screenshot] implement this design. take a screenshot of the result and compare it to the original. list differences and fix them"* |
| **Address root causes, not symptoms** | *"the build is failing"* | *"the build fails with this error: \[paste error]. fix it and verify the build succeeds. address the root cause, don't suppress the error"* |

Once the check exists, decide how hard it gates the stop:

* **In one prompt**: ask Claude to run the check and iterate in the same message, as in the table above.
* **Across a session**: set the check as a [`/goal` condition](<https://code.claude.com/docs/en/goal>). A separate evaluator re-checks it after every turn and Claude keeps working until the goal resolves. If Claude stalls, Claude Code eventually stops the run with the goal still set — see [how /goal evaluation works](<https://code.claude.com/docs/en/goal#how-evaluation-works>).
* **As a deterministic gate**: a [Stop hook](<https://code.claude.com/docs/en/hooks#stop>) runs your check as a script and blocks the turn from ending until it passes. [Stop input](<https://code.claude.com/docs/en/hooks#stop-input>) covers the cap on consecutive blocks.
* **By a second opinion**: a [verification subagent](<https://code.claude.com/docs/en/sub-agents>) or a [dynamic workflow](<https://code.claude.com/docs/en/workflows>) that checks its own findings has a fresh model try to refute the result, so the agent doing the work isn't the one grading it.

Each step trades setup for attention. The prompt version works on any task today. The `/goal` and Stop hook versions are what let an unattended run finish correctly without you.

Have Claude show evidence rather than asserting success: the test output, the command it ran and what it returned, or a screenshot of the result. Reviewing evidence is faster than re-running the verification yourself, and it works for sessions you weren't watching.

***

## Explore first, then plan, then code



**提示：**

  Separate research and planning from implementation to avoid solving the wrong problem.


Letting Claude jump straight to coding can produce code that solves the wrong problem. Use [plan mode](<https://code.claude.com/docs/en/permission-modes#analyze-before-you-edit-with-plan-mode>) to separate exploration from execution.

The recommended workflow has four phases:



  

#### Explore

    Enter plan mode by pressing `Shift+Tab` until the status bar shows `⏸ plan mode on`, or start the session with `claude --permission-mode plan`. Claude reads files and answers questions without making changes.

    ```txt title="claude (plan mode)" wrap theme={null}
    read /src/auth and understand how we handle sessions and login.
    also look at how we manage environment variables for secrets.
    ```
  


  

#### Plan

    Ask Claude to create a detailed implementation plan.

    ```txt title="claude (plan mode)" wrap theme={null}
    I want to add Google OAuth. What files need to change?
    What's the session flow? Create a plan.
    ```

    Press `Ctrl+G` to open the plan in your text editor for direct editing before Claude proceeds.
  


  

#### Implement

    Switch out of plan mode by approving the plan or pressing `Shift+Tab`, then let Claude code, verifying against its plan.

    ```txt title="claude" wrap theme={null}
    implement the OAuth flow from your plan. write tests for the
    callback handler, run the test suite and fix any failures.
    ```
  


  

#### Commit

    Ask Claude to commit with a descriptive message and create a PR.

    ```txt title="claude" wrap theme={null}
    commit with a descriptive message and open a PR
    ```
  




**说明：**

  Plan mode is useful, but also adds overhead.

  For tasks where the scope is clear and the fix is small (like fixing a typo, adding a log line, or renaming a variable) ask Claude to do it directly.

  Planning is most useful when you're uncertain about the approach, when the change modifies multiple files, or when you're unfamiliar with the code being modified. If you could describe the diff in one sentence, skip the plan.


***

## Provide specific context in your prompts



**提示：**

  The more precise your instructions, the fewer corrections you'll need.


Claude can infer intent, but it can't read your mind. Reference specific files, mention constraints, and point to example patterns.

| Strategy | Before | After |
| - | - | - |
| **Scope the task.** Specify which file, what scenario, and testing preferences. | *"add tests for foo.py"* | *"write a test for foo.py covering the edge case where the user is logged out. avoid mocks."* |
| **Point to sources.** Direct Claude to the source that can answer a question. | *"why does ExecutionFactory have such a weird api?"* | *"look through ExecutionFactory's git history and summarize how its api came to be"* |
| **Reference existing patterns.** Point Claude to patterns in your codebase. | *"add a calendar widget"* | *"look at how existing widgets are implemented on the home page to understand the patterns. HotDogWidget.php is a good example. follow the pattern to implement a new calendar widget that lets the user select a month and paginate forwards/backwards to pick a year. build from scratch without libraries other than the ones already used in the codebase."* |
| **Describe the symptom.** Provide the symptom, the likely location, and what "fixed" looks like. | *"fix the login bug"* | *"users report that login fails after session timeout. check the auth flow in src/auth/, especially token refresh. write a failing test that reproduces the issue, then fix it"* |

Vague prompts can be useful when you're exploring and can afford to course-correct. A prompt like `"what would you improve in this file?"` can surface things you wouldn't have thought to ask about.

### Provide rich content



**提示：**

  Use `@` to reference files, paste screenshots/images, or pipe data directly.


You can provide rich data to Claude in several ways:

* **Reference files with `@`** instead of describing where code lives. Claude reads the file before responding.
* **Paste images directly**. Copy/paste or drag and drop images into the prompt.
* **Give URLs** for documentation and API references. Use `/permissions` to allowlist frequently used domains.
* **Pipe in data** by running `cat error.log | claude -p "explain this error"` to send file contents directly.
* **Let Claude fetch what it needs**. Tell Claude to pull context itself using Bash commands, MCP tools, or by reading files.

***

## Configure your environment

A few setup steps make Claude Code significantly more effective across all your sessions. For a full overview of extension features and when to use each one, see [Extend Claude Code](<https://code.claude.com/docs/en/features-overview>).

### Write an effective CLAUDE.md



**提示：**

  Run `/init` to generate a starter CLAUDE.md file based on your current project structure, then refine over time.


CLAUDE.md is a special file that Claude reads at the start of every conversation. Include Bash commands, code style, and workflow rules. This gives Claude persistent context it can't infer from code alone.

There's no required format for CLAUDE.md files, but keep it short and human-readable. For example:

```markdown CLAUDE.md theme={null}
# Code style
- Use ES modules (import/export) syntax, not CommonJS (require)
- Destructure imports when possible (eg. import { foo } from 'bar')

# Workflow
- Be sure to typecheck when you're done making a series of code changes
- Prefer running single tests, and not the whole test suite, for performance
```

Run `/context` to confirm Claude loaded the file. CLAUDE.md is loaded every session, so only include things that apply broadly. For domain knowledge or workflows that are only relevant sometimes, use [skills](<https://code.claude.com/docs/en/skills>) instead. Claude loads them on demand without bloating every conversation.

Keep it concise. For each line, ask: *"Would removing this cause Claude to make mistakes?"* If not, cut it. Bloated CLAUDE.md files cause Claude to ignore your actual instructions!

| ✅ Include | ❌ Exclude |
| - | - |
| Bash commands Claude can't guess | Anything Claude can figure out by reading code |
| Code style rules that differ from defaults | Standard language conventions Claude already knows |
| Testing instructions and preferred test runners | Detailed API documentation (link to docs instead) |
| Repository etiquette (branch naming, PR conventions) | Information that changes frequently |
| Architectural decisions specific to your project | Long explanations or tutorials |
| Developer environment quirks (required env vars) | File-by-file descriptions of the codebase |
| Common gotchas or behaviors that aren't self-evident | Self-evident practices like "write clean code" |

If Claude keeps doing something you don't want despite having a rule against it, the file is probably too long and the rule is getting lost. If Claude asks you questions that are answered in CLAUDE.md, the phrasing might be ambiguous. Treat CLAUDE.md like code: review it when things go wrong, prune it regularly, and test changes by observing whether Claude's behavior actually shifts. For a checked-in CLAUDE.md, run [`/doctor`](<https://code.claude.com/docs/en/commands#all-commands>) and Claude proposes cuts for content it can derive from the codebase.

If Claude keeps skipping one instruction, add emphasis such as "IMPORTANT" to that line alone. If you emphasize many lines, none of them stands out. Check CLAUDE.md into git so your team can contribute. The file compounds in value over time.

CLAUDE.md files can import additional files using `@path/to/import` syntax. For import rules and where CLAUDE.md files can live, see [CLAUDE.md files](<https://code.claude.com/docs/en/memory#claude-md-files>).

### Configure permissions



**提示：**

  To get fewer prompts without giving up control, pre-approve the tools you trust with `/permissions` and let sandboxed commands run without asking with `/sandbox`. Switch to Manual mode when you want to approve edits and commands yourself.


With Claude Code v2.1.283 or later, auto mode is the [built-in starting permission mode](<https://code.claude.com/docs/en/permission-modes#eliminate-prompts-with-auto-mode>) for interactive terminal and VS Code sessions: a separate classifier model reviews most actions instead of you and blocks only what looks risky, such as scope escalation, unknown infrastructure, or hostile-content-driven actions. On earlier versions, auto mode is the built-in starting permission mode only on Pro, Max, and Team plans.

In Manual mode, Claude Code asks before actions that might modify your system: file writes, Bash commands, MCP tools. That's safe but tedious. After the tenth approval you're clicking through rather than reviewing. Two tools cut those interruptions in Manual mode and apply in auto mode as well:

* **Permission allowlists**: permit specific tools you know are safe, like `npm run lint` or `git commit`
* **Sandboxing**: enable OS-level isolation that restricts filesystem and network access, allowing Claude to work more freely within defined boundaries

Read more about [permission modes](<https://code.claude.com/docs/en/permission-modes>), [permission rules](<https://code.claude.com/docs/en/permissions>), and [sandboxing](<https://code.claude.com/docs/en/sandboxing>).

### Use CLI tools



**提示：**

  Tell Claude Code to use CLI tools like `gh`, `aws`, `gcloud`, and `sentry-cli` when interacting with external services.


CLI tools are the most context-efficient way to interact with external services. If you use GitHub, install the `gh` CLI. Claude knows how to use it for creating issues, opening pull requests, and reading comments. Without `gh`, Claude can still use the GitHub API, but unauthenticated requests often hit rate limits.

Claude is also effective at learning CLI tools it doesn't already know. Try prompts like `Use 'foo-cli-tool --help' to learn about foo tool, then use it to solve A, B, C.`

### Connect MCP servers



**提示：**

  Run `claude mcp add` with a server name and URL or command to connect external tools like Notion, Figma, or your database. For example: `claude mcp add --transport http notion https://mcp.notion.com/mcp`.


With [MCP servers](<https://code.claude.com/docs/en/mcp>), you can ask Claude to implement features from issue trackers, query databases, analyze monitoring data, integrate designs from Figma, and automate workflows.

### Set up hooks



**提示：**

  Use hooks for actions that must happen every time with zero exceptions.


[Hooks](<https://code.claude.com/docs/en/hooks-guide>) run scripts automatically at specific points in Claude's workflow. Unlike CLAUDE.md instructions which are advisory, hooks are deterministic and guarantee the action happens.

Claude can write hooks for you. Try prompts like *"Write a hook that runs eslint after every file edit"* or *"Write a hook that blocks writes to the migrations folder."* Edit `.claude/settings.json` directly to configure hooks by hand, and run `/hooks` to browse what's configured.

### Create skills



**提示：**

  Create `SKILL.md` files in `.claude/skills/` to give Claude domain knowledge and reusable workflows.


[Skills](<https://code.claude.com/docs/en/skills>) extend Claude's knowledge with information specific to your project, team, or domain. Claude applies them automatically when relevant, or you can invoke them directly with `/skill-name`.

Create a skill by adding a directory with a `SKILL.md` to `.claude/skills/`:

```markdown .claude/skills/api-conventions/SKILL.md theme={null}
---
name: api-conventions
description: REST API design conventions for our services
---
# API Conventions
- Use kebab-case for URL paths
- Use camelCase for JSON properties
- Always include pagination for list endpoints
- Version APIs in the URL path (/v1/, /v2/)
```

Skills can also define repeatable workflows you invoke directly:

```markdown .claude/skills/fix-issue/SKILL.md theme={null}
---
name: fix-issue
description: Fix a GitHub issue
disable-model-invocation: true
---
Analyze and fix the GitHub issue: $ARGUMENTS.

1. Use `gh issue view` to get the issue details
2. Understand the problem described in the issue
3. Search the codebase for relevant files
4. Implement the necessary changes to fix the issue
5. Write and run tests to verify the fix
6. Ensure code passes linting and type checking
7. Create a descriptive commit message
8. Push and create a PR
```

Run `/fix-issue 1234` to invoke it. Use `disable-model-invocation: true` for workflows with side effects that you want to trigger manually.

### Create custom subagents



**提示：**

  Define specialized assistants in `.claude/agents/` that Claude can delegate to for isolated tasks.


[Subagents](<https://code.claude.com/docs/en/sub-agents>) run in their own context with their own set of allowed tools. They're useful for tasks that read many files or need specialized focus without cluttering your main conversation.

```markdown .claude/agents/security-reviewer.md theme={null}
---
name: security-reviewer
description: Reviews code for security vulnerabilities
tools: Read, Grep, Glob, Bash
model: opus
---
You are a senior security engineer. Review code for:
- Injection vulnerabilities (SQL, XSS, command injection)
- Authentication and authorization flaws
- Secrets or credentials in code
- Insecure data handling

Provide specific line references and suggested fixes.
```

Tell Claude to use subagents explicitly: *"Use a subagent to review this code for security issues."*

### Install plugins



**提示：**

  Run `/plugin` to browse the marketplace. Plugins add skills, tools, and integrations without configuration.


[Plugins](<https://code.claude.com/docs/en/plugins/overview>) bundle skills, hooks, subagents, and MCP servers into a single installable unit from the community and Anthropic. If you work with a typed language, install a [code intelligence plugin](<https://code.claude.com/docs/en/plugins/code-intelligence>) to give Claude precise symbol navigation and automatic error detection after edits.

For guidance on choosing between skills, subagents, hooks, and MCP, see [Extend Claude Code](<https://code.claude.com/docs/en/features-overview#match-features-to-your-goal>).

***

## Communicate effectively

Ask Claude the questions you'd ask another engineer, and for larger features have Claude interview you and write a spec before you start implementing.

### Ask codebase questions



**提示：**

  Ask Claude questions you'd ask a senior engineer.


When onboarding to a new codebase, use Claude Code for learning and exploration. You can ask Claude the same sorts of questions you would ask another engineer:

* How does logging work?
* How do I make a new API endpoint?
* What does `async move { ... }` do on line 134 of `foo.rs`?
* What edge cases does `CustomerOnboardingFlowImpl` handle?
* Why does this code call `foo()` instead of `bar()` on line 333?

Using Claude Code this way is an effective onboarding workflow, improving ramp-up time and reducing load on other engineers. No special prompting required: ask questions directly.

### Let Claude interview you



**提示：**

  For larger features, have Claude interview you first. Start with a minimal prompt and ask Claude to interview you using the `AskUserQuestion` tool.


Claude asks about things you might not have considered yet, including technical implementation, UI/UX, edge cases, and tradeoffs. Replace `[brief description]` with your feature before sending the prompt.

```text wrap theme={null}
I want to build [brief description]. Interview me in detail using the AskUserQuestion tool.

Ask about technical implementation, UI/UX, edge cases, concerns, and tradeoffs. Don't ask obvious questions, dig into the hard parts I might not have considered.

Keep interviewing until we've covered everything, then write a complete spec to SPEC.md.
```

Once the spec is complete, start a fresh session to execute it. The new session has clean context focused entirely on implementation, and you have a written spec to reference.

The most useful specs are self-contained: they name the files and interfaces involved, state what is out of scope, and end with an end-to-end verification step that proves the feature works. Time spent making the spec precise pays off more than time spent watching the implementation.

***

## Manage your session

Conversations are persistent and reversible. Use this to your advantage!

### Course-correct early and often



**提示：**

  Correct Claude as soon as you notice it going off track.


The best results come from tight feedback loops. Though Claude occasionally solves problems perfectly on the first attempt, correcting it quickly generally produces better solutions faster.

* **`Esc`**: stop Claude mid-action with the `Esc` key. Context is preserved, so you can redirect.
* **`Esc + Esc` or `/rewind`**: press `Esc` twice or run `/rewind` to open the rewind menu and restore previous conversation and code state, or summarize from a selected message.
* **`"Undo that"`**: have Claude revert its changes.
* **`/clear`**: reset context between unrelated tasks. Long sessions with irrelevant context can reduce performance.

If you've corrected Claude more than twice on the same issue in one session, the context is cluttered with failed approaches. Run `/clear` and start fresh with a more specific prompt that incorporates what you learned. A clean session with a better prompt almost always outperforms a long session with accumulated corrections.

### Manage context aggressively



**提示：**

  Run `/clear` between unrelated tasks to reset context.


Claude Code automatically compacts conversation history when you approach context limits, which preserves important code and decisions while freeing space.

During long sessions, Claude's context window can fill with irrelevant conversation, file contents, and commands. This can reduce performance and sometimes distract Claude.

* Use `/clear` frequently between tasks to reset the context window entirely
* When auto compaction triggers, Claude summarizes what matters most, including code patterns, file states, and key decisions
* For more control, run `/compact <instructions>`, like `/compact Focus on the API changes`
* To compact only part of the conversation, use `Esc + Esc` or `/rewind`, select a message checkpoint, and choose **Summarize from here** or **Summarize up to here**. The first condenses messages from that point forward while keeping earlier context intact; the second condenses earlier messages while keeping recent ones in full. See [the rewind menu's summarize options](<https://code.claude.com/docs/en/checkpointing#rewind-and-summarize>).
* Customize compaction behavior in CLAUDE.md with instructions like `"When compacting, always preserve the full list of modified files and any test commands"` to ensure critical context survives summarization
* For questions that don't need to stay in context, use [`/btw`](<https://code.claude.com/docs/en/interactive-mode#side-questions-with-%2Fbtw>). The answer never enters conversation history, so you can check a detail without growing context.

### Use subagents for investigation



**提示：**

  Delegate research with `"use subagents to investigate X"`. They explore in a separate context, keeping your main conversation clean for implementation.


Since context is your fundamental constraint, use subagents to keep research out of it. When Claude researches a codebase it reads lots of files, all of which consume your context. Subagents run in separate context windows and report back summaries:

```text wrap theme={null}
Use subagents to investigate how our authentication system handles token
refresh, and whether we have any existing OAuth utilities I should reuse.
```

You can also use subagents for verification after Claude implements something. See [Add an adversarial review step](<#add-an-adversarial-review-step>).

### Rewind with checkpoints



**提示：**

  Every prompt you send that starts a turn creates a checkpoint. You can restore conversation, code, or both to any previous checkpoint.


Claude automatically snapshots files before each change so a checkpoint can restore them. Double-tap `Escape` or run `/rewind` to open the rewind menu. You can restore conversation only, restore code only, restore both, or summarize from a selected message. See [Checkpointing](<https://code.claude.com/docs/en/checkpointing>) for details.

Instead of carefully planning every move, you can tell Claude to try something risky. If it doesn't work, rewind and try a different approach. Checkpoints are saved with the conversation, so you can close your terminal, resume the session later, and still rewind.



**注意：**

  Checkpoints only track changes made through Claude's file editing tools. Changes made through Bash commands or external processes are not captured. This isn't a replacement for git.


### Resume conversations



**提示：**

  Name sessions with `/rename` and treat them like branches: each workstream gets its own persistent context.


Claude Code saves conversations locally, so when a task spans multiple sittings you don't have to re-explain the context. Run [`claude --continue`](<https://code.claude.com/docs/en/sessions#resume-a-session>) to pick up where you left off, or `claude --resume` to choose from a list. Give sessions descriptive names like `oauth-migration` so you can find them later. See [Manage sessions](<https://code.claude.com/docs/en/sessions>) for the full set of resume, branch, and naming controls.

***

## Automate and scale

Once you're effective with one Claude, multiply your output with parallel sessions, non-interactive mode, and fan-out patterns.

### Run non-interactive mode



**提示：**

  Use `claude -p "prompt"` in CI, pre-commit hooks, or scripts. Add `--output-format stream-json --verbose` for streaming JSON output.


With `claude -p "your prompt"`, you can run Claude non-interactively, without an interactive prompt. The run still creates a resumable session unless you pass `--no-session-persistence`. [Non-interactive mode](<https://code.claude.com/docs/en/headless>) is how you integrate Claude into CI pipelines, pre-commit hooks, or any automated workflow. The output formats let you parse results programmatically: plain text, JSON, or streaming JSON.

```bash theme={null}
# One-off queries
claude -p "Explain what this project does"

# Structured output for scripts
claude -p "List all API endpoints" --output-format json

# Streaming for real-time processing
claude -p "Analyze this log file" --output-format stream-json --verbose
```

The first command prints plain text. The `json` format returns a single JSON object with a `result` field. The `stream-json` format prints one JSON object per line, starting with an init event.

### Run multiple Claude sessions



**提示：**

  Run multiple Claude sessions in parallel to speed up development, run isolated experiments, or start complex workflows.


Pick the parallel approach that fits how much coordination you want to do yourself, and add messaging when the sessions need to pass findings between them:

* [Worktrees](<https://code.claude.com/docs/en/worktrees>): run separate CLI sessions in isolated git checkouts so edits don't collide
* [Cross-session messaging](<https://code.claude.com/docs/en/cross-session-messaging>): let the sessions you run yourself pass findings to each other
* [Desktop app](<https://code.claude.com/docs/en/desktop#work-in-parallel-with-sessions>): manage multiple local sessions visually, optionally each in its own worktree
* [Use Claude Code in the cloud](<https://code.claude.com/docs/en/claude-code-on-the-web>): run sessions on Anthropic-managed infrastructure by default
* [Agent view](<https://code.claude.com/docs/en/agent-view>): research preview. Run `claude agents` to dispatch sessions that keep running in the background and watch them from one screen
* [Agent teams](<https://code.claude.com/docs/en/agent-teams>): experimental and disabled by default. Automated coordination of multiple sessions with shared tasks, messaging, and a team lead

Beyond parallelizing work, multiple sessions enable quality-focused workflows. A fresh context improves code review since Claude won't be biased toward code it just wrote.

For example, use a Writer/Reviewer pattern:

| Session A (Writer) | Session B (Reviewer) |
| - | - |
| `Implement a rate limiter for our API endpoints` | |
| | `Review the rate limiter implementation in @src/middleware/rateLimiter.ts. Look for edge cases, race conditions, and consistency with our existing middleware patterns.` |
| `Here's the review feedback: [Session B output]. Address these issues.` | |

You can do something similar with tests: have one Claude write tests, then another write code to pass them.

### Fan out across files



**提示：**

  Loop through tasks calling `claude -p` for each. Use `--allowedTools` to pre-approve tools for batch operations.


For large migrations or analyses, you can distribute work across many parallel Claude invocations. Run [`/batch <instruction>`](<https://code.claude.com/docs/en/commands#all-commands>) to have Claude split the change across 5 to 30 subagents. Each subagent works in its own worktree. To drive the fan-out from your own script instead, loop over `claude -p`:



  

#### Generate a task list

    Have Claude write the list of files that need migrating to a file, so the loop in the next step can read it, with a prompt like `list all 2,000 Python files that need migrating and save the list to files.txt`
  


  

#### Write a script to loop through the list

    ```bash theme={null}
    for file in $(cat files.txt); do
      claude -p "Migrate $file from Python 2 to Python 3. Return OK or FAIL." \
        --allowedTools "Edit,Bash(git commit *)" \
        --permission-mode dontAsk
    done
    ```
  


  

#### Test on a few files, then run on all of them

    Refine your prompt based on what goes wrong with the first 2-3 files, then run on the full set. The `--allowedTools` flag pre-approves the tools the migration needs, and [`--permission-mode dontAsk`](<https://code.claude.com/docs/en/permission-modes#allow-only-pre-approved-tools-with-dontask-mode>) denies anything else that would need approval, which matters when you're running unattended.
  


You can also integrate Claude into existing data/processing pipelines:

```bash theme={null}
claude -p "<your prompt>" --output-format json | your_command
```

### Run autonomously with auto mode

For uninterrupted execution with background safety checks, use [auto mode](<https://code.claude.com/docs/en/permission-modes#eliminate-prompts-with-auto-mode>). A classifier model reviews commands before they run, blocking scope escalation, unknown infrastructure, and hostile-content-driven actions while letting routine work proceed without prompts.

```bash theme={null}
claude --permission-mode auto -p "fix all lint errors"
```

When the classifier repeatedly blocks actions in a non-interactive run with the `-p` flag, Claude Code doesn't stop the run. See [when auto mode falls back](<https://code.claude.com/docs/en/permission-modes#when-auto-mode-falls-back>) for what happens instead and for the thresholds.

### Add an adversarial review step



**提示：**

  Before treating a task as done, have a subagent review the diff in a fresh context and report gaps.


The longer Claude works unattended, the more an independent check matters before you count the work as done. A reviewer running in a fresh [subagent](<https://code.claude.com/docs/en/sub-agents>) context sees only the diff and the criteria you give it, not the reasoning that produced the change, so it evaluates the result on its own terms.

For a correctness check, run the bundled [`/code-review` skill](<https://code.claude.com/docs/en/commands>), which reviews the current diff for bugs in a fresh subagent and returns findings to the session. To check the diff against your plan instead, write the review prompt yourself. Name the work to check, the plan to check it against, and what counts as a finding:

```text wrap theme={null}
Use a subagent to review the rate limiter diff against PLAN.md. Check that
every requirement is implemented, the listed edge cases have tests, and
nothing outside the task's scope changed. Report gaps, not style preferences.
```

Because the reviewer runs as a subagent, the implementing session receives the gaps directly and can fix them and re-review without you copying findings between windows.



**说明：**

  A reviewer prompted to find gaps will usually report some, even when the work is sound, because that is what it was asked to do. Chasing every finding leads to over-engineering: extra abstraction layers, defensive code, and tests for cases that can't happen. Tell the reviewer to flag only gaps that affect correctness or the stated requirements, and treat the rest as optional.


***

## Avoid common failure patterns

These are common mistakes. Recognizing them early saves time:

* **The kitchen sink session.** You start with one task, then ask Claude something unrelated, then go back to the first task. Context is full of irrelevant information.
  > **Fix**: `/clear` between unrelated tasks.
* **Correcting over and over.** Claude does something wrong, you correct it, it's still wrong, you correct again. Context is polluted with failed approaches.
  > **Fix**: After two failed corrections, `/clear` and write a better initial prompt incorporating what you learned.
* **The over-specified CLAUDE.md.** If your CLAUDE.md is too long, Claude ignores half of it because important rules get lost in the noise.
  > **Fix**: Ruthlessly prune. If Claude already does something correctly without the instruction, delete it or convert it to a hook.
* **The trust-then-verify gap.** Claude produces a plausible-looking implementation that doesn't handle edge cases.
  > **Fix**: Always provide verification (tests, scripts, screenshots). If you can't verify it, don't ship it.
* **The infinite exploration.** You ask Claude to "investigate" something without scoping it. Claude reads hundreds of files, filling the context.
  > **Fix**: Scope investigations narrowly or use subagents so the exploration doesn't consume your main context.

***

## Develop your intuition

The patterns in this guide aren't set in stone. They're starting points that work well in general, but might not be optimal for every situation.

Sometimes you *should* let context accumulate because you're deep in one complex problem and the history is valuable. Sometimes you should skip planning and let Claude figure it out because the task is exploratory. Sometimes a vague prompt is exactly right because you want to see how Claude interprets the problem before constraining it.

Pay attention to what works. When Claude produces great output, notice what you did: the prompt structure, the context you provided, the mode you were in. When Claude struggles, ask why. Was the context too noisy? The prompt too vague? The task too big for one pass?

Over time, you'll develop intuition that no guide can capture. You'll know when to be specific and when to be open-ended, when to plan and when to explore, when to clear context and when to let it accumulate.

## Related resources

* [How Claude Code works](<https://code.claude.com/docs/en/how-claude-code-works>): the agentic loop, tools, and context management
* [Extend Claude Code](<https://code.claude.com/docs/en/features-overview>): skills, hooks, MCP, subagents, and plugins
* [Common workflows](<https://code.claude.com/docs/en/common-workflows>): step-by-step recipes for debugging, testing, PRs, and more
* [CLAUDE.md](<https://code.claude.com/docs/en/memory>): store project conventions and persistent context


</details>
