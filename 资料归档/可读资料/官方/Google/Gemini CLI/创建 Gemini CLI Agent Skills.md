# 创建 Gemini CLI Agent Skills

从目录和 SKILL.md 定义到脚本逻辑、测试与分享介绍技能编写流程，说明技能的构成和发现机制。

- 来源：Google／Gemini CLI
- 资料类型：存档文档
- 文档时间：更新：[2026-04-30](<https://geminicli.com/docs/cli/creating-skills/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://geminicli.com/docs/cli/creating-skills/>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Gemini CLI/创建 Gemini CLI Agent Skills.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 创建 Agent Skills

Agent Skills 让您可以利用专业知识、程序性 
 工作流程和特定于任务的资源来扩展 Gemini CLI。本指南将引导您通过
自动和手动方法来创建和组织您的技能。

## 快速入门：通过提示创建技能

[标题为“快速入门：使用提示创建技能”的部分](<#快速入门通过提示创建技能>)

创建新技能的最快方法是使用内置的 `skill-creator`。
此元技能将指导您完成设计、搭建和验证您的 
 专业知识。

只需要求 Gemini CLI 为您创建一项技能：

> “创建一项名为‘代码审查器’的新技能，用于分析本地文件以查找 
>  常见错误和样式违规。”

Gemini 将：

1. 为您的技能生成一个新目录（例如，`my-new-skill/`）。
2. 创建一个 `SKILL.md` 文件，其中包含必要的 YAML frontmatter（`name` 和
   `description`).
3. 创建标准资源目录：`scripts/`、`references/`和
   `assets/`.

创建后，您可以在`.gemini/skills/code-reviewer/`中找到您的新技能。

## 手动创建

[标题为“手动创建”的部分](<#手动创建>)

1. **为您的技能创建一个目录**（例如，`my-new-skill/`）。
2. **在新目录中创建一个`SKILL.md`文件**。

### 1。创建目录结构

[标题为“1。创建目录结构”的部分](<#1创建目录结构>)

第一步是为您的技能及其 
 脚本创建必要的文件夹。

**macOS/Linux**

终端窗口

```
mkdir -p .gemini/skills/code-reviewer/scripts
```

**Windows (PowerShell)**

终端窗口

```
New-Item -ItemType Directory -Force -Path ".gemini\skills\code-reviewer\scripts"
```

### 2。定义技能 (`SKILL.md`)

[标题为“2.定义技能(SKILL.md)”的部分](<#2定义技能-skillmd>)

`SKILL.md` 文件定义了 agent 的技能用途和说明。
在 `.gemini/skills/code-reviewer/SKILL.md` 创建文件。

```
---


name: code-reviewer


description:


Expertise in reviewing code changes for correctness, security, and style. Use


when the user asks to "review" their code or a PR.


---


# Code Reviewer Instructions


You act as a senior software engineer specialized in code quality. When this


skill is active, you MUST:


1.  **Analyze**: Review the provided code for logical errors, security


vulnerabilities, and style violations.


2.  **Review**: Use the bundled `scripts/review.js` utility to perform an


automated check.


3.  **Feedback**: Provide constructive feedback, clearly distinguishing between


critical issues and minor improvements.
```

### 3。添加工具逻辑

[标题为“3。添加工具逻辑”的部分](<#3添加工具逻辑>)

技能可以捆绑脚本等资源来执行确定性任务。在 `.gemini/skills/code-reviewer/scripts/review.js` 处创建 
 文件。

.gemini/skills/code-reviewer/scripts/review.js

```
const file = process.argv[2];


if (!file) {


console.error('Usage: node review.js <file>');


process.exit(1);


}


console.log(`Reviewing ${file}...`);


// Simple mock review logic


setTimeout(() => {


console.log(`Result: Success (No major issues found in ${file})`);


}, 500);
```

### 4。测试技能

[标题为“4。测试技能”的部分](<#4测试技能>)

Gemini CLI自动发现`.gemini/skills`目录中的技能。

1. 开始一个新会话并提出一个触发该技能的问题
描述：“你能评论一下index.js”
2. Gemini 标识请求与 `code-reviewer` 描述匹配，并且
请求许可来激活它。
3. 一旦您批准，Gemini 就会执行捆绑的脚本：
   `node .gemini/skills/code-reviewer/scripts/review.js index.js`

要确定您的技能是否已正确加载，请运行以下命令：

终端窗口

```
/skills
```

### 5。可选：分享您的技能

[标题为“5。可选：分享您的技能”的部分](<#5可选分享您的技能>)

您可以根据目标受众以多种方式分享您的技能。

* **工作区技能**：将您的技能提交到以下位置的`.gemini/skills/`目录
您的项目存储库。
* **扩展**：将你的技能捆绑在一个
[Gemini CLI扩展名](<https://geminicli.com/docs/extensions/writing-extensions>)。
* **Git 存储库**：将技能目录共享为独立的 Git 存储库并
使用`gemini skills install <url>`安装它。

---

## 核心理念

[标题为“核心概念”的部分](<#核心理念>)

现在您已经掌握了第一项技能，让我们探索核心组件和 
 工作流程，以开发更复杂的专业知识。

### 技能结构

[标题为“技能结构”的部分](<#技能结构>)

虽然 `SKILL.md` 文件是唯一必需的组件，但我们建议使用 
 以下结构来组织您的技能资源。

```
my-skill/


├── SKILL.md       (Required) Instructions and metadata


├── scripts/       (Optional) Executable scripts


├── references/    (Optional) Static documentation


└── assets/        (Optional) Templates and other resources
```

激活技能后，模型将被授予访问整个目录的权限。
您可以指示模型使用这些
文件夹中找到的工具和文件。

### 元数据和触发器

[标题为“元数据和触发器”的部分](<#元数据和触发器>)

`SKILL.md` 文件使用 YAML frontmatter 作为元数据。

* **`name`**：技能的唯一标识符。这应该与目录匹配
名字。
* **`description`**: **CRITICAL.** 这就是 Gemini 决定何时使用
技能。具体说明它处理的任务以及应该
触发它的关键字。

### 发现层

[标题为“发现层”的部分](<#发现层>)

Gemini CLI 从多个位置发现技能，遵循特定的优先顺序 
（从最低到最高）：

1. **内置技能**：包含在Gemini CLI中（预先批准）。
2. **扩展技能**：捆绑在[扩展](<https://geminicli.com/docs/extensions>)中。
3. **用户技能**：`~/.gemini/skills/`或`~/.agents/skills/`别名。
4. **工作空间技能**：`.gemini/skills/`或`.agents/skills/`别名。

### 发现别名

[标题为“发现别名”的部分](<#发现别名>)

您可以使用 `.agents/skills` 作为 `.gemini/skills` 的替代。此别名 
 与遵循 
[Agent Skills](<https://agentskills.io>) 标准的其他 AI agent 工具兼容。

## 高级开发

[标题为“高级开发”的部分](<#高级开发>)

一旦您掌握了基本技能，您就可以使用专门的脚本和工作流程
来简化您的开发流程。

### 创建脚本

[标题为“创建脚本”的部分](<#创建脚本>)

如果您正在开发一项技能并希望使用内置
工具所使用的相同脚本，您可以在核心包中找到它们。这些脚本有助于自动化
技能的初始化、验证和打包。

* **初始化**：`node scripts/init_skill.cjs <name> --path <dir>`
* **验证**：`node scripts/validate_skill.cjs <path/to/skill>`
* **包**：`node scripts/package_skill.cjs <path/to/skill>`（创建一个
`.skill` zip 文件）

### 链接本地发展

[标题为“链接本地发展”的部分](<#链接本地发展>)

如果您正在单独的目录中开发技能，您可以将其链接到您的
用户技能目录进行测试：

终端窗口

```
gemini skills link .
```

## 后续步骤

[标题为“后续步骤”的部分](<#后续步骤>)

* [技能最佳实践](<https://geminicli.com/docs/cli/skills-best-practices>)：学习策略
培养可靠、有效的技能。
* [Agent Skills 概述](<https://geminicli.com/docs/cli/skills>)：深入探讨发现层和
技能生命周期。
* [开始使用Agent Skills](<https://geminicli.com/docs/cli/tutorials/skills-getting-started>)：A
触发和使用技能的快速演练。

最后更新：四月 30、2026


## 原文对照

<details>
<summary>展开完整原文</summary>

# Creating Agent Skills

Agent Skills let you extend Gemini CLI with specialized expertise, procedural
workflows, and task-specific resources. This guide walks you through both
automated and manual methods for creating and organizing your skills.

## Quickstart: Create a skill with a prompt

[Section titled “Quickstart: Create a skill with a prompt”](<#quickstart-create-a-skill-with-a-prompt>)

The fastest way to create a new skill is to use the built-in `skill-creator`.
This meta-skill guides you through designing, scaffolding, and validating your
expertise.

Simply ask Gemini CLI to create a skill for you:

> “Create a new skill called ‘code-reviewer’ that analyzes local files for
> common errors and style violations.”

Gemini will then:

1. Generate a new directory for your skill (for example, `my-new-skill/`).
2. Create a `SKILL.md` file with the necessary YAML frontmatter (`name` and
   `description`).
3. Create the standard resource directories: `scripts/`, `references/`, and
   `assets/`.

Once created, you can find your new skill in `.gemini/skills/code-reviewer/`.

## Manual creation

[Section titled “Manual creation”](<#manual-creation>)

1. **Create a directory** for your skill (for example, `my-new-skill/`).
2. **Create a `SKILL.md` file** inside the new directory.

### 1. Create the directory structure

[Section titled “1. Create the directory structure”](<#1-create-the-directory-structure>)

The first step is to create the necessary folders for your skill and its
scripts.

**macOS/Linux**

Terminal window

```
mkdir -p .gemini/skills/code-reviewer/scripts
```

**Windows (PowerShell)**

Terminal window

```
New-Item -ItemType Directory -Force -Path ".gemini\skills\code-reviewer\scripts"
```

### 2. Define the skill (`SKILL.md`)

[Section titled “2. Define the skill (SKILL.md)”](<#2-define-the-skill-skillmd>)

The `SKILL.md` file defines the skill’s purpose and instructions for the agent.
Create a file at `.gemini/skills/code-reviewer/SKILL.md`.

```
---


name: code-reviewer


description:


Expertise in reviewing code changes for correctness, security, and style. Use


when the user asks to "review" their code or a PR.


---


# Code Reviewer Instructions


You act as a senior software engineer specialized in code quality. When this


skill is active, you MUST:


1.  **Analyze**: Review the provided code for logical errors, security


vulnerabilities, and style violations.


2.  **Review**: Use the bundled `scripts/review.js` utility to perform an


automated check.


3.  **Feedback**: Provide constructive feedback, clearly distinguishing between


critical issues and minor improvements.
```

### 3. Add the tool logic

[Section titled “3. Add the tool logic”](<#3-add-the-tool-logic>)

Skills can bundle resources like scripts to perform deterministic tasks. Create
a file at `.gemini/skills/code-reviewer/scripts/review.js`.

.gemini/skills/code-reviewer/scripts/review.js

```
const file = process.argv[2];


if (!file) {


console.error('Usage: node review.js <file>');


process.exit(1);


}


console.log(`Reviewing ${file}...`);


// Simple mock review logic


setTimeout(() => {


console.log(`Result: Success (No major issues found in ${file})`);


}, 500);
```

### 4. Test the skill

[Section titled “4. Test the skill”](<#4-test-the-skill>)

Gemini CLI automatically discovers skills in the `.gemini/skills` directory.

1. Start a new session and ask a question that triggers the skill’s
   description: “Can you review index.js”
2. Gemini identifies the request matches the `code-reviewer` description and
   asks for permission to activate it.
3. Once you approve, Gemini executes the bundled script:
   `node .gemini/skills/code-reviewer/scripts/review.js index.js`

To determine whether your skill has been correctly loaded, run the command:

Terminal window

```
/skills
```

### 5. Optional: Share your skill

[Section titled “5. Optional: Share your skill”](<#5-optional-share-your-skill>)

You can share your skills in several ways depending on your target audience.

* **Workspace skills**: Commit your skill to a `.gemini/skills/` directory in
  your project repository.
* **Extensions**: Bundle your skill within a
  [Gemini CLI extension](<https://geminicli.com/docs/extensions/writing-extensions>).
* **Git repositories**: Share the skill directory as a standalone Git repo and
  install it using `gemini skills install <url>`.

---

## Core concepts

[Section titled “Core concepts”](<#core-concepts>)

Now that you’ve built your first skill, let’s explore the core components and
workflows for developing more complex expertise.

### Skill structure

[Section titled “Skill structure”](<#skill-structure>)

While a `SKILL.md` file is the only required component, we recommend the
following structure for organizing your skill’s resources.

```
my-skill/


├── SKILL.md       (Required) Instructions and metadata


├── scripts/       (Optional) Executable scripts


├── references/    (Optional) Static documentation


└── assets/        (Optional) Templates and other resources
```

When a skill is activated, the model is granted access to this entire directory.
You can instruct the model to use the tools and files found within these
folders.

### Metadata and triggers

[Section titled “Metadata and triggers”](<#metadata-and-triggers>)

The `SKILL.md` file uses YAML frontmatter for metadata.

* **`name`**: A unique identifier for the skill. This should match the directory
  name.
* **`description`**: **CRITICAL.** This is how Gemini decides when to use the
  skill. Be specific about the tasks it handles and the keywords that should
  trigger it.

### Discovery tiers

[Section titled “Discovery tiers”](<#discovery-tiers>)

Gemini CLI discovers skills from several locations, following a specific order
of precedence (lowest to highest):

1. **Built-in Skills**: Included with Gemini CLI (pre-approved).
2. **Extension Skills**: Bundled within [extensions](<https://geminicli.com/docs/extensions>).
3. **User Skills**: `~/.gemini/skills/` or the `~/.agents/skills/` alias.
4. **Workspace Skills**: `.gemini/skills/` or the `.agents/skills/` alias.

### Discovery aliases

[Section titled “Discovery aliases”](<#discovery-aliases>)

You can use `.agents/skills` as an alternative to `.gemini/skills`. This alias
is compatible with other AI agent tools following the
[Agent Skills](<https://agentskills.io>) standard.

## Advanced development

[Section titled “Advanced development”](<#advanced-development>)

Once you’ve built a basic skill, you can use specialized scripts and workflows
to streamline your development process.

### Creation scripts

[Section titled “Creation scripts”](<#creation-scripts>)

If you are developing a skill and want to use the same scripts the built-in
tools use, you can find them in the core package. These scripts help automate
the initialization, validation, and packaging of skills.

* **Initialize**: `node scripts/init_skill.cjs <name> --path <dir>`
* **Validate**: `node scripts/validate_skill.cjs <path/to/skill>`
* **Package**: `node scripts/package_skill.cjs <path/to/skill>` (Creates a
  `.skill` zip file)

### Linking for local development

[Section titled “Linking for local development”](<#linking-for-local-development>)

If you are developing a skill in a separate directory, you can link it to your
user skills directory for testing:

Terminal window

```
gemini skills link .
```

## Next steps

[Section titled “Next steps”](<#next-steps>)

* [Skill best practices](<https://geminicli.com/docs/cli/skills-best-practices>): Learn strategies for
  building reliable and effective skills.
* [Agent Skills overview](<https://geminicli.com/docs/cli/skills>): Deep dive into discovery tiers and the
  skill lifecycle.
* [Get started with Agent Skills](<https://geminicli.com/docs/cli/tutorials/skills-getting-started>): A
  quick walkthrough of triggering and using skills.

Last updated: Apr 30, 2026


</details>
