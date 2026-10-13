# Cline Skills 的结构、触发与资源组织

产品指南。讲解按需加载、斜杠命令、SKILL.md 描述与命名、技能目录和附带文档、模板、脚本，帮助组织重复开发任务；数据分析段落是格式样例。

- 来源：Cline
- 资料类型：产品指南
- 存档版本：`f87705844954`。
- 文档时间：源码提交（存档版）：[2026-10-07](<https://github.com/cline/cline/commit/6354d209c8e54274e78732b4923162b494d97b81>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：技能示例须按实际开发任务调整，不能据此自动安装或执行。

[原文发布页](<https://github.com/cline/cline/blob/f87705844954a86ac993bfc4e2c86402e86d1d60/docs/customization/skills.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Cline/Cline Skills 的结构、触发与资源组织.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: "Skills"
sidebarTitle: "Skills"
description: "Modular instruction sets that extend Cline's capabilities for specific tasks."
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

技能是模块化指令集，可扩展 Cline 执行特定任务的能力。每个技能都包含详细的指导、流程和可选资源，Cline 仅在与您的请求相关时加载。

安装多个技能，Cline只加载它需要的内容。在您询问部署之前，部署技能将保持休眠状态。与 [ 规则 ](<https://docs.cline.bot/customization/cline-rules>)（始终处于活动状态）不同，技能会按需加载，因此当您处理不相关的事情时它们不会消耗上下文。

**注释：**

从“技能”菜单管理技能：单击模型选择器左侧 Cline 面板底部的比例图标，然后切换到 ** 技能 ** 选项卡。

## 技能如何发挥作用

技能使用渐进式加载来最大限度地提高效率：

|级别|加载时|代币成本|内容|
|-------|-------------|------------|---------|
|元数据|始终（启动时）|~100 每个技能的代币|`name` 和 `description` 来自 YAML frontmatter|
|使用说明|技能触发时|5k 代币以下|SKILL.md 正文，含说明和指导|
|资源|根据需要|有效无限制|通过 `read_files` 访问的捆绑文件或执行的脚本|

当您发送消息时，Cline 会看到可用技能及其描述的列表。如果您的请求与技能描述匹配，Cline 会使用 `skills` 工具激活该技能，该工具会加载 SKILL.md 中的完整说明。

## 使用斜线命令触发技能

您还可以使用斜杠命令从聊天输入中显式调用启用的技能。

1. 在聊天中输入 `/` 以打开命令建议。
2. 选择要运行的技能命令（例如，`/aws-deploy`）。
3. Cline触发该技能并加载其`SKILL.md`指令。

当您想要立即强制使用特定技能而不是等待基于描述的自动匹配时，这非常有用。

## 技能结构

每个技能都是一个包含 `SKILL.md` 文件和 YAML frontmatter 的目录。

**代码示例：** `Skill directory structure`

```text
my-skill/
├── SKILL.md          # Required: main instructions
├── docs/             # Optional: additional documentation
│   └── advanced.md
└── scripts/          # Optional: utility scripts
    └── helper.sh
```

`SKILL.md` 文件有两部分：元数据和指令。

**代码示例：** `SKILL.md`

```markdown
---
name: my-skill
description: Brief description of what this skill does and when to use it.
---

# My Skill

Detailed instructions for Cline to follow when this skill is activated.

## Steps
1. First, do this
2. Then do that
3. For advanced usage, see [advanced.md](docs/advanced.md)
```

必填字段：
- `name` 必须与目录名完全匹配
- `description`告诉Cline何时使用该技能（最多1024个字符）

## 创造技能

#### 打开技能菜单

单击模型选择器左侧 Cline 面板底部的比例图标。切换到技能选项卡。

#### 创造新技能

单击“新技能...”并输入技能名称（例如，`aws-deploy`）。 Cline 使用模板 `SKILL.md` 文件创建技能目录。

#### 写下你的技能说明

编辑`SKILL.md`文件：
- 更新 `description` 字段以指定该技能何时触发
- 在正文中添加详细说明
- （可选）在 `docs/`、`templates/` 或 `scripts/` 子目录中添加支持文件

您还可以通过在文件系统中创建目录结构来手动创建技能。将技能目录放在`.cline/skills/`（工作空间）或`~/.cline/skills/`（全局）中，Cline将自动检测它们。

将重要信息放在 SKILL.md 中。 Cline 按顺序读取文件，因此预先加载常见情况。使用清晰的节标题，例如“##错误处理”或“##配置”，以便Cline可以扫描相关节。

### 切换技能

每个技能都有一个开关来启用或禁用它。这使您可以控制哪些技能处于活动状态，而无需删除技能目录。发现技能后默认启用。

例如，您可以在进行本地开发时禁用 CI/CD 技能，或者仅在处理客户的项目时启用特定于客户的技能。

## 写你的 SKILL.md

### 命名约定

技能名称显示在 `name` 字段中，并且必须与目录名称完全匹配。使用带连字符的小写字母（kebab-case）并描述该技能的用途。

好名字：
- `aws-cdk-deploy`
- `pr-review-checklist`
- `database-migration`
- `api-client-generator`

避免：
- `aws`（太模糊）
- `my_skill`（下划线，非描述性）
- `DeployToAWS`（使用 kebab-case，而不是 PascalCase）
- `misc-helpers`（太通用）

### 撰写有效的描述

描述决定了Cline何时激活技能。模糊的描述意味着该技能不会在您期望的时候触发。

好的描述是具体且可操作的：

```yaml
description: Deploy applications to AWS using CDK. Use when deploying, updating infrastructure, or managing AWS resources.

description: Generate release notes from git commits. Use when preparing releases, writing changelogs, or summarizing recent changes.

description: Analyze CSV and Excel data files. Use when exploring datasets, generating statistics, or creating visualizations from tabular data.
```

薄弱的描述留下了太多的歧义：

```yaml
description: Helps with AWS stuff.

description: Data analysis helper.

description: Useful for releases.
```

从技能的作用（动作动词）开始，包括用户可能说的触发短语，并提及特定的文件类型、工具或域。通过尝试不同的请求措辞来测试您的描述，看看技能是否会触发。

### 保持技能专注

将 SKILL.md 保持在 5k 代币以下。如果您的技能需要更多内容，请将其拆分为 `docs/` 目录中的单独文件，并从主要说明中引用它们。 Cline 仅在需要时加载引用的文件。

包括真实的例子。显示要运行的命令、预期的输出以及结果应该是什么样子。抽象说明比具体示例更难遵循。

## 技能所在

技能可以存储在全局或项目工作区中。请参阅 [什么去哪里？](<https://docs.cline.bot/getting-started/config#what-goes-where>) 了解何时使用每种内容的指南。

项目技能：
- `.cline/skills/`（推荐）
- `.clinerules/skills/`
- `.claude/skills/`

全球技能：
- `~/.cline/skills/` (macOS/Linux)
- `C:\Users\USERNAME\.cline\skills\`（Windows）

当全局技能和项目技能同名时，全局技能优先。这使您可以在全局范围内保留通用技能，同时使用 `.cline/skills/` 中的项目特定技能，以便整个团队都可以使用它们。

通过提交 `.cline/skills/` 对您的项目技能进行版本控制。您的团队可以共同分享、审查和改进它们。

## 捆绑支持文件

技能可以包括 Cline 仅在需要时访问的其他文件。

**代码示例：** `Directory structure`

```text
complex-skill/
├── SKILL.md
├── docs/
│   ├── setup.md
│   └── troubleshooting.md
├── templates/
│   └── config.yaml
└── scripts/
    └── validate.py
```

### 文档/

使用文档获取对于 SKILL.md 来说过于详细或仅在特定情况下相关的信息：
- 高级配置选项
- 边缘情况的故障排除指南
- 参考资料（API 模式、数据库模式）
- 平台特定说明

部署技能可能具有 `docs/aws.md`、`docs/gcp.md` 和 `docs/azure.md`。 Cline仅根据您的请求加载相关平台指南。

### 模板/

当您的技能创建配置文件、样板代码或结构化文档时，请使用模板：
- 配置文件（Terraform、Docker Compose、CI/CD 管道）
- 代码脚手架（组件模板、测试装置）
- 文档模板（README、API 文档）

项目设置技能可以包括 Cline 为每个新项目自定义的 `templates/dockerfile`、`templates/docker-compose.yml` 和 `templates/.env.example`。

### 脚本/

如果您需要一致的行为，请使用脚本进行确定性操作：
- 验证（linting 配置、检查先决条件）
- 数据处理（解析、格式化、转换）
- 复杂的计算（成本估算、资源规模）
- API 交互（获取数据、运行运行状况检查）

脚本具有令牌效率，因为只有它们的输出进入上下文，而不是代码本身。 500 行验证脚本会生成简单的“通过”或详细的错误消息，而不消耗脚本逻辑的任何上下文。

### 引用捆绑文件

在 SKILL.md 说明中引用这些文件：

**代码示例：** `SKILL.md (referencing bundled files)`

```markdown
For initial setup, follow [setup.md](docs/setup.md).
Use the config template at `templates/config.yaml` as a starting point.
Run the validation script to check your configuration:

python scripts/validate.py
```

当指令引用文档文件时，Cline 使用 `read_file` 读取文档文件。脚本可以直接执行，并且只有脚本的输出进入上下文窗口。

|使用脚本用于|使用说明用于|
|-----------------|---------------------|
|确定性操作（验证、格式化）|适应环境的灵活指导|
|复杂的计算|决策过程|
|需要可靠性的操作|步骤可能因情况而异|
|任何你不想消耗代币的东西解释|最佳实践和模式|

## 示例：数据分析技能

这是数据分析任务的实用技能。使用此 `SKILL.md` 创建一个名为 `data-analysis/` 的目录：

**代码示例：** `data-analysis/SKILL.md`

```markdown
---
name: data-analysis
description: Analyze data files and generate insights. Use when working with CSV, Excel, or JSON data files that need exploration, cleaning, or visualization.
---

# Data Analysis

When analyzing data files, follow this process:

## 1. Understand the Data
- Read a sample of the file to understand its structure
- Identify column types and data quality issues
- Note any missing values or anomalies

## 2. Ask Clarifying Questions
Before diving in, ask the user:
- What specific insights are they looking for?
- Are there any known data quality issues?
- What format do they want for the output?

## 3. Perform Analysis
Use pandas for data manipulation:

import pandas as pd

# Load and explore
df = pd.read_csv("data.csv")
print(df.head())
print(df.describe())
print(df.info())

For visualization, prefer matplotlib or seaborn depending on complexity.
```

技能将 Cline 从通用助理转变为了解您领域的专家。从您经常重复的任务的一项技能开始，对其进行测试，并迭代描述，直到它可靠地触发。


## 原文对照

<details>
<summary>展开完整原文</summary>

Skills are modular instruction sets that extend Cline's capabilities for specific tasks. Each skill packages detailed guidance, processes, and optional resources that Cline loads only when relevant to your request.

Install multiple skills and Cline only loads what it needs. A deployment skill stays dormant until you ask about deploying. Unlike [rules](<https://docs.cline.bot/customization/cline-rules>) (which are always active), skills load on-demand so they don't consume context when you're working on something unrelated.

**注释：**

Manage skills from the Skills menu: click the scale icon at the bottom of the Cline panel, to the left of the model selector, then switch to the **Skills** tab.

## How Skills Work

Skills use progressive loading to maximize efficiency:

| Level | When Loaded | Token Cost | Content |
|-------|-------------|------------|---------|
| Metadata | Always (at startup) | ~100 tokens per skill | `name` and `description` from YAML frontmatter |
| Instructions | When skill is triggered | Under 5k tokens | SKILL.md body with instructions and guidance |
| Resources | As needed | Effectively unlimited | Bundled files accessed via `read_files` or executed scripts |

When you send a message, Cline sees a list of available skills with their descriptions. If your request matches a skill's description, Cline activates it using the `skills` tool, which loads the full instructions from SKILL.md.

## Triggering Skills with Slash Commands

You can also invoke enabled skills explicitly from the chat input using slash commands.

1. Type `/` in chat to open command suggestions.
2. Select the skill command you want to run (for example, `/aws-deploy`).
3. Cline triggers that skill and loads its `SKILL.md` instructions.

This is useful when you want to force a specific skill immediately instead of waiting for auto-matching based on description.

## Skill Structure

Every skill is a directory containing a `SKILL.md` file with YAML frontmatter.

**代码示例：** `Skill directory structure`

```text
my-skill/
├── SKILL.md          # Required: main instructions
├── docs/             # Optional: additional documentation
│   └── advanced.md
└── scripts/          # Optional: utility scripts
    └── helper.sh
```

The `SKILL.md` file has two parts: metadata and instructions.

**代码示例：** `SKILL.md`

```markdown
---
name: my-skill
description: Brief description of what this skill does and when to use it.
---

# My Skill

Detailed instructions for Cline to follow when this skill is activated.

## Steps
1. First, do this
2. Then do that
3. For advanced usage, see [advanced.md](docs/advanced.md)
```

Required fields:
- `name` must exactly match the directory name
- `description` tells Cline when to use this skill (max 1024 characters)

## Creating a Skill

#### Open the Skills menu

Click the scale icon at the bottom of the Cline panel, to the left of the model selector. Switch to the Skills tab.

#### Create a new skill

Click "New skill..." and enter a name for your skill (e.g., `aws-deploy`). Cline creates a skill directory with a template `SKILL.md` file.

#### Write your skill instructions

Edit the `SKILL.md` file:
- Update the `description` field to specify when this skill should trigger
- Add detailed instructions in the body
- Optionally add supporting files in `docs/`, `templates/`, or `scripts/` subdirectories

You can also create skills manually by creating the directory structure in your file system. Place skill directories in `.cline/skills/` (workspace) or `~/.cline/skills/` (global) and Cline will detect them automatically.

Put the important information first in your SKILL.md. Cline reads the file sequentially, so front-load the common cases. Use clear section headers like "## Error Handling" or "## Configuration" so Cline can scan for relevant sections.

### Toggling Skills

Every skill has a toggle to enable or disable it. This lets you control which skills are active without deleting the skill directory. Skills are enabled by default when discovered.

For example, you might disable a CI/CD skill when working on local development, or enable a client-specific skill only when working on that client's project.

## Writing Your SKILL.md

### Naming Conventions

The skill name appears in the `name` field and must match the directory name exactly. Use lowercase with hyphens (kebab-case) and be descriptive about what the skill does.

Good names:
- `aws-cdk-deploy`
- `pr-review-checklist`
- `database-migration`
- `api-client-generator`

Avoid:
- `aws` (too vague)
- `my_skill` (underscores, not descriptive)
- `DeployToAWS` (use kebab-case, not PascalCase)
- `misc-helpers` (too generic)

### Writing Effective Descriptions

The description determines when Cline activates the skill. A vague description means the skill won't trigger when you expect it to.

Good descriptions are specific and actionable:

```yaml
description: Deploy applications to AWS using CDK. Use when deploying, updating infrastructure, or managing AWS resources.

description: Generate release notes from git commits. Use when preparing releases, writing changelogs, or summarizing recent changes.

description: Analyze CSV and Excel data files. Use when exploring datasets, generating statistics, or creating visualizations from tabular data.
```

Weak descriptions leave too much ambiguity:

```yaml
description: Helps with AWS stuff.

description: Data analysis helper.

description: Useful for releases.
```

Start with what the skill does (action verbs), include trigger phrases users might say, and mention specific file types, tools, or domains. Test your descriptions by trying different phrasings of requests to see if the skill triggers.

### Keeping Skills Focused

Keep SKILL.md under 5k tokens. If your skill needs more content, split it into separate files in a `docs/` directory and reference them from the main instructions. Cline loads referenced files only when needed.

Include real examples. Show what commands to run, what output to expect, and what the result should look like. Abstract instructions are harder to follow than concrete examples.

## Where Skills Live

Skills can be stored globally or in a project workspace. See [What Goes Where?](<https://docs.cline.bot/getting-started/config#what-goes-where>) for guidance on when to use each.

Project skills:
- `.cline/skills/` (recommended)
- `.clinerules/skills/`
- `.claude/skills/`

Global skills:
- `~/.cline/skills/` (macOS/Linux)
- `C:\Users\USERNAME\.cline\skills\` (Windows)

When a global skill and project skill have the same name, the global skill takes precedence. This lets you keep general-purpose skills globally while using project-specific skills in `.cline/skills/` so the whole team can use them.

Version control your project skills by committing `.cline/skills/`. Your team can share, review, and improve them together.

## Bundling Supporting Files

Skills can include additional files that Cline accesses only when needed.

**代码示例：** `Directory structure`

```text
complex-skill/
├── SKILL.md
├── docs/
│   ├── setup.md
│   └── troubleshooting.md
├── templates/
│   └── config.yaml
└── scripts/
    └── validate.py
```

### docs/

Use docs for information that's too detailed for SKILL.md or only relevant in specific situations:
- Advanced configuration options
- Troubleshooting guides for edge cases
- Reference material (API schemas, database schemas)
- Platform-specific instructions

A deployment skill might have `docs/aws.md`, `docs/gcp.md`, and `docs/azure.md`. Cline loads only the relevant platform guide based on your request.

### templates/

Use templates when your skill creates configuration files, boilerplate code, or structured documents:
- Config files (Terraform, Docker Compose, CI/CD pipelines)
- Code scaffolding (component templates, test fixtures)
- Documentation templates (README, API docs)

A project setup skill could include `templates/dockerfile`, `templates/docker-compose.yml`, and `templates/.env.example` that Cline customizes for each new project.

### scripts/

Use scripts for deterministic operations where you want consistent behavior:
- Validation (linting configs, checking prerequisites)
- Data processing (parsing, formatting, transforming)
- Complex calculations (cost estimation, resource sizing)
- API interactions (fetching data, running health checks)

Scripts are token-efficient because only their output enters context, not the code itself. A 500-line validation script produces a simple "Passed" or detailed error messages without consuming any context for the script logic.

### Referencing Bundled Files

Reference these files in your SKILL.md instructions:

**代码示例：** `SKILL.md (referencing bundled files)`

```markdown
For initial setup, follow [setup.md](docs/setup.md).
Use the config template at `templates/config.yaml` as a starting point.
Run the validation script to check your configuration:

python scripts/validate.py
```

Cline reads documentation files using `read_file` when the instructions reference them. Scripts can be executed directly, and only the script's output enters the context window.

| Use Scripts For | Use Instructions For |
|-----------------|---------------------|
| Deterministic operations (validation, formatting) | Flexible guidance that adapts to context |
| Complex computations | Decision-making processes |
| Operations that need reliability | Steps that might vary by situation |
| Anything you'd rather not consume tokens explaining | Best practices and patterns |

## Example: Data Analysis Skill

Here's a practical skill for data analysis tasks. Create a directory called `data-analysis/` with this `SKILL.md`:

**代码示例：** `data-analysis/SKILL.md`

```markdown
---
name: data-analysis
description: Analyze data files and generate insights. Use when working with CSV, Excel, or JSON data files that need exploration, cleaning, or visualization.
---

# Data Analysis

When analyzing data files, follow this process:

## 1. Understand the Data
- Read a sample of the file to understand its structure
- Identify column types and data quality issues
- Note any missing values or anomalies

## 2. Ask Clarifying Questions
Before diving in, ask the user:
- What specific insights are they looking for?
- Are there any known data quality issues?
- What format do they want for the output?

## 3. Perform Analysis
Use pandas for data manipulation:

import pandas as pd

# Load and explore
df = pd.read_csv("data.csv")
print(df.head())
print(df.describe())
print(df.info())

For visualization, prefer matplotlib or seaborn depending on complexity.
```

Skills transform Cline from a general-purpose assistant into a specialist that knows your domain. Start with one skill for a task you repeat often, test it, and iterate on the description until it triggers reliably.


</details>
