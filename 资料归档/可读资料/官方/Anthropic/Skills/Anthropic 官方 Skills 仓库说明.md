# Anthropic 官方 Skills 仓库说明

介绍技能目录、脚本与资源的组织方式，以及在 Claude Code、Claude.ai 和 API 中的使用入口；区分仓库实现与 Agent Skills 标准。

- 来源：Anthropic／Skills
- 资料类型：存档文档
- 存档版本：`dbd4588f9e10`。
- 文档时间：源码提交（存档版）：[2026-05-09](<https://github.com/anthropics/skills/commit/f458cee31a7577a47ba0c9a101976fa599385174>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/anthropics/skills/blob/main/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Skills/Anthropic 官方 Skills 仓库说明.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

> **注意：**此存储库包含Anthropic对Claude的技能实现。有关 Agent Skills 标准的信息，请参阅 [agentskills.io](<http://agentskills.io>)。

[![skills.sh](<https://skills.sh/b/anthropics/skills>)](<https://skills.sh/anthropics/skills>)

# 技能
技能是 Claude 动态加载的指令、脚本和资源的文件夹，以提高专门任务的性能。技能教授 Claude 如何以可重复的方式完成特定任务，无论是使用公司的品牌指南创建文档、使用组织的特定工作流程分析数据，还是自动化个人任务。

欲了解更多信息，请查看：
- [什么是技能？](<https://support.claude.com/en/articles/12512176-what-are-skills>)
- [使用Claude](<https://support.claude.com/en/articles/12512180-using-skills-in-claude>)中的技能
- [如何创建自定义技能](<https://support.claude.com/en/articles/12512198-creating-custom-skills>)
- [为现实世界配备agentsAgent Skills](<https://anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills>)

# 关于此存储库

该存储库包含的技能展示了 Claude 技能系统的可能性。这些技能范围从创意应用程序（艺术、音乐、设计）到技术任务（测试 Web 应用程序、MCP 服务器生成）到企业工作流程（通信、品牌推广等）。

每个技能都独立包含在自己的文件夹中，其中包含一个 `SKILL.md` 文件，其中包含 Claude 使用的说明和元数据。浏览这些技能，以获得您自己技能的灵感或了解不同的模式和方法。

此存储库中的许多技能都是开源的（Apache 2.0）。我们还提供了文档创建和编辑技能 [Claude的文档功能](<https://www.anthropic.com/news/create-files>) 在引擎盖下 [`skills/docx`](<https://github.com/anthropics/skills/blob/dbd4588f9e1033efb41dad4bef2f7947c8993d44/skills/docx>), [`skills/pdf`](<https://github.com/anthropics/skills/blob/dbd4588f9e1033efb41dad4bef2f7947c8993d44/skills/pdf>), [`skills/pptx`](<https://github.com/anthropics/skills/blob/dbd4588f9e1033efb41dad4bef2f7947c8993d44/skills/pptx>), 和 [`skills/xlsx`](<https://github.com/anthropics/skills/blob/dbd4588f9e1033efb41dad4bef2f7947c8993d44/skills/xlsx>) 子文件夹。这些是可用的源代码，不是开源的，但我们希望与开发人员分享这些内容，作为在生产中积极使用的更复杂技能的参考 AI 应用程序。

## 免责声明

**这些技能仅用于演示和教育目的。**虽然Claude中可能提供其中一些功能，但您从Claude获得的实现和行为可能与这些技能中显示的不同。这些技能旨在说明模式和可能性。在依靠技能执行关键任务之前，请务必在自己的环境中彻底测试技能。

# 技能组合
- [./skills](<https://github.com/anthropics/skills/blob/dbd4588f9e1033efb41dad4bef2f7947c8993d44/skills>)：创意与设计、开发与技术、企业与沟通以及文档技能的技能示例
- [./spec](<https://github.com/anthropics/skills/blob/dbd4588f9e1033efb41dad4bef2f7947c8993d44/spec>)：Agent Skills规格
- [./template](<https://github.com/anthropics/skills/blob/dbd4588f9e1033efb41dad4bef2f7947c8993d44/template>)：技能模板

# 尝试使用 Claude Code、Claude.ai 和 API

## Claude Code
您可以通过在 Claude Code 中运行以下命令来将此存储库注册为 Claude Code 插件市场：
```
/plugin marketplace add anthropics/skills
```

然后，安装一组特定的技能：
1. 选择`Browse and install plugins`
2. 选择`anthropic-agent-skills`
3. 选择`document-skills`或`example-skills`
4. 选择`Install now`

或者，通过以下方式直接安装任一插件：
```
/plugin install document-skills@anthropic-agent-skills
/plugin install example-skills@anthropic-agent-skills
```

安装插件后，只需提及即可使用该技能。例如，如果您从市场安装 `document-skills` 插件，您可以要求 Claude Code 执行以下操作：“使用 PDF 技能从 `path/to/some-file.pdf` 中提取表单字段”

## Claude.ai

这些示例技能均已可用于 Claude.ai 中的付费计划。

要使用此存储库中的任何技能或上传自定义技能，请按照[使用Claude](<https://support.claude.com/en/articles/12512180-using-skills-in-claude#h_a4222fa77b>)中的技能中的说明进行操作。

## Claude API

您可以使用Anthropic的预建技能，并通过ClaudeAPI上传自定义技能。有关更多信息，请参阅[技能API快速入门](<https://docs.claude.com/en/api/skills-guide#creating-a-skill>)。

# 创造基本技能

技能的创建很简单 - 只需一个包含 `SKILL.md` 文件的文件夹，其中包含 YAML 前言和说明。您可以使用此存储库中的 **template-skill** 作为起点：

```markdown
---
name: my-skill-name
description: A clear description of what this skill does and when to use it
---

# My Skill Name

[Add your instructions here that Claude will follow when this skill is active]

## Examples
- Example usage 1
- Example usage 2

## Guidelines
- Guideline 1
- Guideline 2
```

frontmatter 仅需要两个字段：
- `name` - 你的技能的唯一标识符（小写，空格连字符）
- `description` - 该技能的作用以及何时使用的完整描述

下面的 Markdown 内容包含 Claude 将遵循的说明、示例和指南。更多详情请参见[如何创建自定义技能](<https://support.claude.com/en/articles/12512198-creating-custom-skills>)。

# 合作伙伴技能

技能是教导 Claude 如何更好地使用特定软件的好方法。当我们看到合作伙伴提供的出色技能示例时，我们可能会在这里重点介绍其中的一些技能：

- **Notion** - [Notion Claude](<https://www.notion.so/notiondevs/Notion-Skills-for-Claude-28da4445d27180c7af1df7d8615723d0>) 的技能


## 原文对照

<details>
<summary>展开完整原文</summary>

> **Note:** This repository contains Anthropic's implementation of skills for Claude. For information about the Agent Skills standard, see [agentskills.io](<http://agentskills.io>).

[![skills.sh](<https://skills.sh/b/anthropics/skills>)](<https://skills.sh/anthropics/skills>)

# Skills
Skills are folders of instructions, scripts, and resources that Claude loads dynamically to improve performance on specialized tasks. Skills teach Claude how to complete specific tasks in a repeatable way, whether that's creating documents with your company's brand guidelines, analyzing data using your organization's specific workflows, or automating personal tasks.

For more information, check out:
- [What are skills?](<https://support.claude.com/en/articles/12512176-what-are-skills>)
- [Using skills in Claude](<https://support.claude.com/en/articles/12512180-using-skills-in-claude>)
- [How to create custom skills](<https://support.claude.com/en/articles/12512198-creating-custom-skills>)
- [Equipping agents for the real world with Agent Skills](<https://anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills>)

# About This Repository

This repository contains skills that demonstrate what's possible with Claude's skills system. These skills range from creative applications (art, music, design) to technical tasks (testing web apps, MCP server generation) to enterprise workflows (communications, branding, etc.).

Each skill is self-contained in its own folder with a `SKILL.md` file containing the instructions and metadata that Claude uses. Browse through these skills to get inspiration for your own skills or to understand different patterns and approaches.

Many skills in this repo are open source (Apache 2.0). We've also included the document creation & editing skills that power [Claude's document capabilities](<https://www.anthropic.com/news/create-files>) under the hood in the [`skills/docx`](<https://github.com/anthropics/skills/blob/dbd4588f9e1033efb41dad4bef2f7947c8993d44/skills/docx>), [`skills/pdf`](<https://github.com/anthropics/skills/blob/dbd4588f9e1033efb41dad4bef2f7947c8993d44/skills/pdf>), [`skills/pptx`](<https://github.com/anthropics/skills/blob/dbd4588f9e1033efb41dad4bef2f7947c8993d44/skills/pptx>), and [`skills/xlsx`](<https://github.com/anthropics/skills/blob/dbd4588f9e1033efb41dad4bef2f7947c8993d44/skills/xlsx>) subfolders. These are source-available, not open source, but we wanted to share these with developers as a reference for more complex skills that are actively used in a production AI application.

## Disclaimer

**These skills are provided for demonstration and educational purposes only.** While some of these capabilities may be available in Claude, the implementations and behaviors you receive from Claude may differ from what is shown in these skills. These skills are meant to illustrate patterns and possibilities. Always test skills thoroughly in your own environment before relying on them for critical tasks.

# Skill Sets
- [./skills](<https://github.com/anthropics/skills/blob/dbd4588f9e1033efb41dad4bef2f7947c8993d44/skills>): Skill examples for Creative & Design, Development & Technical, Enterprise & Communication, and Document Skills
- [./spec](<https://github.com/anthropics/skills/blob/dbd4588f9e1033efb41dad4bef2f7947c8993d44/spec>): The Agent Skills specification
- [./template](<https://github.com/anthropics/skills/blob/dbd4588f9e1033efb41dad4bef2f7947c8993d44/template>): Skill template

# Try in Claude Code, Claude.ai, and the API

## Claude Code
You can register this repository as a Claude Code Plugin marketplace by running the following command in Claude Code:
```
/plugin marketplace add anthropics/skills
```

Then, to install a specific set of skills:
1. Select `Browse and install plugins`
2. Select `anthropic-agent-skills`
3. Select `document-skills` or `example-skills`
4. Select `Install now`

Alternatively, directly install either Plugin via:
```
/plugin install document-skills@anthropic-agent-skills
/plugin install example-skills@anthropic-agent-skills
```

After installing the plugin, you can use the skill by just mentioning it. For instance, if you install the `document-skills` plugin from the marketplace, you can ask Claude Code to do something like: "Use the PDF skill to extract the form fields from `path/to/some-file.pdf`"

## Claude.ai

These example skills are all already available to paid plans in Claude.ai. 

To use any skill from this repository or upload custom skills, follow the instructions in [Using skills in Claude](<https://support.claude.com/en/articles/12512180-using-skills-in-claude#h_a4222fa77b>).

## Claude API

You can use Anthropic's pre-built skills, and upload custom skills, via the Claude API. See the [Skills API Quickstart](<https://docs.claude.com/en/api/skills-guide#creating-a-skill>) for more.

# Creating a Basic Skill

Skills are simple to create - just a folder with a `SKILL.md` file containing YAML frontmatter and instructions. You can use the **template-skill** in this repository as a starting point:

```markdown
---
name: my-skill-name
description: A clear description of what this skill does and when to use it
---

# My Skill Name

[Add your instructions here that Claude will follow when this skill is active]

## Examples
- Example usage 1
- Example usage 2

## Guidelines
- Guideline 1
- Guideline 2
```

The frontmatter requires only two fields:
- `name` - A unique identifier for your skill (lowercase, hyphens for spaces)
- `description` - A complete description of what the skill does and when to use it

The markdown content below contains the instructions, examples, and guidelines that Claude will follow. For more details, see [How to create custom skills](<https://support.claude.com/en/articles/12512198-creating-custom-skills>).

# Partner Skills

Skills are a great way to teach Claude how to get better at using specific pieces of software. As we see awesome example skills from partners, we may highlight some of them here:

- **Notion** - [Notion Skills for Claude](<https://www.notion.so/notiondevs/Notion-Skills-for-Claude-28da4445d27180c7af1df7d8615723d0>)


</details>
