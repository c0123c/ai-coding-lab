# Skills 与插件的选择

比较可复用开发流程和工具集成的组织方式，说明何时建立技能、何时通过插件共享能力。

- 来源：OpenAI／Codex
- 资料类型：存档文档
- 文档时间：官方未公开发表/更新时间（[已复查](<https://learn.chatgpt.com/docs/skills-and-plugins>)）；本地抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://learn.chatgpt.com/docs/skills-and-plugins.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Codex/Skills 与插件的选择.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 技能和插件

> 完整的文档索引，请参见[llms.txt](<https://learn.chatgpt.com/llms.txt>)。通过将 `.md` 附加到页面 URL 可以获得文档页面的 Markdown 版本。

技能和插件可帮助 ChatGPT 和 Codex 使用 
 正确的说明、资源和工具完成可重复的工作。它们减少了将
相同的提示、模板、要求或流程粘贴到每个聊天中的需要。



[观看：ChatGPT](<https://www.youtube.com/watch?v=pKwRNdDtai0>)中的插件

- **技能**打包了针对特定目标的说明和支持资源
任务或工作流程。
- **插件**是一个可安装的捆绑包，可以包含技能和模型上下文
协议（MCP）服务器。 MCP 服务器公开工具，并且可以选择包含 
 自定义 ChatGPT UI。插件还可以包含 Codex 运行时的 [ 生命周期挂钩 ](<https://learn.chatgpt.com/docs/hooks>)
，包括 ChatGPT Work 和 Codex。

## 使用技能进行可重复的工作

技能是一个可重用的工作流程，可提供 ChatGPT 或 Codex 特定于任务的 
 指导。它可以捕获您已经执行重复性工作的方式，因此无论何时出现该任务，
产品都会遵循相同的流程。

一项技能可以结合：

- 帮助 ChatGPT 和 Codex 识别该技能的名称和描述
适用。
- 定义过程和预期结果的工作流程指令。
- 支持资源，例如模板、示例、品牌指南、架构、
或连接的工具。

当良好的结果取决于可重复的方法时，技能最有用。对于
例如，一项技能可以准备每日简报、审查文档、创建
演示，应用团队写作标准，或从
每周使用相同的连接工具。

使用技能来提高一致性，在 
 工作流程中提供团队最佳实践，并共享标准流程，而不是依赖未记录的 
 知识。

当您的请求与其目的相符时，ChatGPT和Codex可以选择技能。您
也可以明确选择一个。 ChatGPT 支持 `@` 提及，而 Codex
 支持 `$` 技能提及。

## 培养技能

您可以首先将已经重复的任务转变为 
ChatGPT 和 Codex 的重点剧本。良好的首要技能包括每周更新、活动简介、
会议后续行动，或步骤和格式应保持
一致的任何任务。

培养有用的技能：

1. **选择一项重点任务。**记下您通常从什么开始，例如
文件、链接或注释，以及最终结果应该是什么样子。
2. **描述工作流程。**在ChatGPT中，从`@skill-creator`开始；在Codex中，
使用`$skill-creator`。解释目标、要遵循的步骤、预期的 
 格式以及该技能应始终包含或避免的任何内容。添加模板 
 或一个好的示例（如果有）。
3. **审阅并尝试草稿。**检查说明，测试技能
现实的要求，如果结果错过了一个步骤或偏离了
您想要的格式，请对其进行改进。
4. **安装并重复使用。**技能启用后，ChatGPT或Codex可以使用
它用于相关请求，或者您可以明确选择它。当您的工作区设置允许时，您还可以
与队友共享。

有关培养技能的更多详细信息，请参阅下面的专用指南。

[培养技能



      <Tools />
    

使用 ChatGPT 和 Codex 创建、测试和共享可重用技能。](https://learn.chatgpt.com/docs/build-skills)

## 使用工具插件和共享工作流程

插件使可重用功能更易于安装和共享。插件可以
将技能与MCP服务器相结合，以获取来自
GitHub、Google Drive或Slack等服务的工具和上下文。

ChatGPT和Codex共享一个通用插件目录。当您想要
添加现有工作流程而不是自己构建工作流程时，请浏览它。安装 
 插件后，直接描述任务或使用适合您表面的调用语法显式选择插件或捆绑的 
 技能。

[了解如何安装和使用插件](<https://learn.chatgpt.com/docs/plugins>)。

## 在技能和插件之间进行选择

当您需要可重复使用的指令来完成重点任务时，请使用技能。当您需要一个可将指令与 
 连接的服务或其他工具结合起来的可安装包时，请使用 
 插件。

要通过ChatGPT中的对话构建插件，请参阅
[构建插件](<https://learn.chatgpt.com/docs/build-plugins>)。

移动现有的自定义 GPT 工作流程？请参阅 [将自定义 GPT 迁移到插件 ](<https://learn.chatgpt.com/docs/migrate-custom-gpts>) 以了解工作区准备、迁移、测试和共享。

您还可以使用
[录制和重播](<https://learn.chatgpt.com/docs/extend/record-and-replay>)演示工作流程，这会将录制变成
可重复使用的技能。要打包和分发您自己的捆绑包，请参阅
[构建插件](<https://developers.openai.com/plugins/build/plugins>)。

如果您的插件需要连接到服务或公开 MCP 工具，请参阅
[构建 MCP 服务器](<https://developers.openai.com/plugins/build/mcp-server>)。当您的插件准备好接受公众审核时，
请参阅[提交插件](<https://developers.openai.com/plugins/deploy/submission>)。

有关可重用工作流程的更多示例，请参阅[使用OpenAI
学院](<https://openai.com/academy/skills/>)中的技能。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Skills & Plugins

> For the complete documentation index, see [llms.txt](<https://learn.chatgpt.com/llms.txt>). Markdown versions of documentation pages are available by appending `.md` to the page URL.

Skills and plugins help ChatGPT and Codex complete repeatable work with the
right instructions, resources, and tools. They reduce the need to paste the
same prompt, template, requirements, or process into every chat.



[Watch: Plugins in ChatGPT](<https://www.youtube.com/watch?v=pKwRNdDtai0>)

- A **skill** packages instructions and supporting resources for a specific
  task or workflow.
- A **plugin** is an installable bundle that can include skills and Model Context
  Protocol (MCP) servers. MCP servers expose tools and can optionally include
  custom ChatGPT UI. Plugins can also include [lifecycle hooks](<https://learn.chatgpt.com/docs/hooks>)
  for the Codex runtime, including ChatGPT Work and Codex.

## Use skills for repeatable work

A skill is a reusable workflow that gives ChatGPT or Codex task-specific
guidance. It can capture the way you already perform recurring work so either
product follows the same process whenever that task comes up.

A skill can combine:

- A name and description that help ChatGPT and Codex recognize when the skill
  applies.
- Workflow instructions that define the process and expected result.
- Supporting resources such as templates, examples, brand guidance, schemas,
  or connected tools.

Skills are most useful when good results depend on a repeatable approach. For
example, a skill can prepare a daily brief, review documentation, create a
presentation, apply a team writing standard, or gather information from the
same connected tools each week.

Use skills to improve consistency, make team best practices available in the
workflow, and share a standard process instead of relying on undocumented
knowledge.

ChatGPT and Codex can choose a skill when your request matches its purpose. You
can also select one explicitly. ChatGPT supports `@` mentions, while Codex
supports `$` mentions for skills.

## Build skills

You can start by turning a task you already repeat into a focused playbook for
ChatGPT and Codex. Good first skills include a weekly update, a campaign brief,
a meeting follow-up, or any task where the steps and format should stay
consistent.

To build a useful skill:

1. **Choose one focused task.** Note what you normally start with, such as
   files, links, or notes, and what a finished result should look like.
2. **Describe the workflow.** In ChatGPT, start with `@skill-creator`; in Codex,
   use `$skill-creator`. Explain the goal, the steps to follow, the expected
   format, and anything the skill should always include or avoid. Add a template
   or a good example when you have one.
3. **Review and try the draft.** Check the instructions, test the skill with a
   realistic request, and refine it if the result misses a step or drifts from
   the format you want.
4. **Install and reuse it.** Once the skill is enabled, ChatGPT or Codex can use
   it for relevant requests, or you can select it explicitly. You can also
   share it with teammates when your workspace settings allow it.

For more details on building skills, see our dedicated guide below.

[Build skills



      <Tools />
    

    Create, test, and share reusable skills with ChatGPT and Codex.](https://learn.chatgpt.com/docs/build-skills)

## Use plugins for tools and shared workflows

Plugins make reusable capabilities easier to install and share. A plugin can
combine skills with MCP servers for tools and context from services such as
GitHub, Google Drive, or Slack.

ChatGPT and Codex share one universal plugin directory. Browse it when you want
to add an existing workflow instead of building one yourself. After installing
a plugin, describe the task directly or explicitly choose a plugin or bundled
skill using the invocation syntax for your surface.

[Learn how to install and use plugins](<https://learn.chatgpt.com/docs/plugins>).

## Choose between a skill and a plugin

Use a skill when you need reusable instructions for a focused task. Use a
plugin when you want an installable package that can combine instructions with
connected services or other tools.

To build a plugin through a conversation in ChatGPT, see
[Build plugins](<https://learn.chatgpt.com/docs/build-plugins>).

Moving an existing custom GPT workflow? See [Migrate custom GPTs to plugins](<https://learn.chatgpt.com/docs/migrate-custom-gpts>) for workspace preparation, migration, testing, and sharing.

You can also demonstrate a workflow with
[Record & Replay](<https://learn.chatgpt.com/docs/extend/record-and-replay>), which turns the recording into a
reusable skill. To package and distribute your own bundle, see
[Build plugins](<https://developers.openai.com/plugins/build/plugins>).

If your plugin needs to connect to a service or expose MCP tools, see
[Build an MCP server](<https://developers.openai.com/plugins/build/mcp-server>). When your plugin is ready for public review,
see [Submit plugins](<https://developers.openai.com/plugins/deploy/submission>).

For more examples of reusable workflows, see [Using skills in OpenAI
Academy](<https://openai.com/academy/skills/>).


</details>
