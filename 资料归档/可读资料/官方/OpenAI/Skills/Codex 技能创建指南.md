# Codex 技能创建指南

介绍职责与自由度、SKILL.md、元数据和资源组织，以及从需求到初始化、编写、验证的流程。

- 来源：OpenAI／Skills
- 资料类型：存档文档
- 存档版本：`49f948faa925`。
- 文档时间：源码提交（存档版）：[2026-02-09](<https://github.com/openai/skills/commit/4ab6e0fd99c6667163bc34173e3ed3a3fed75ebc>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/skills/blob/main/skills/.system/skill-creator/SKILL.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Skills/Codex 技能创建指南.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: skill-creator
description: Guide for creating effective skills. This skill should be used when users want to create a new skill (or update an existing skill) that extends Codex's capabilities with specialized knowledge, workflows, or tool integrations.
metadata:
  short-description: Create or update a skill
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 技能创造者

该技能为创建有效技能提供了指导。

## 关于技能

技能是模块化、独立的文件夹，通过提供 
 专业知识、工作流程和工具来扩展 Codex 的功能。将它们视为特定 
 域或任务的“入门指南”——它们将 Codex 从通用 agent 转换为专门的 agent
，配备了任何模型都无法完全拥有的程序知识。

### 提供什么技能

1. 专业工作流程 - 针对特定领域的多步骤程序
2. 工具集成 - 使用特定文件格式或 API 的说明
3. 领域专业知识 - 公司特定的知识、模式、业务逻辑
4. 捆绑资源 - 用于复杂和重复任务的脚本、参考和资产

## 核心原则

### 简洁是关键

上下文窗口是一种公共物品。技能与其他 Codex 需要的内容共享上下文窗口：系统提示、对话历史记录、其他技能的元数据以及实际的用户请求。

**默认假设：Codex已经非常聪明。**只添加上下文Codex还没有。挑战每一条信息：“Codex真的需要这个解释吗？”和“这一段是否证明其代币成本合理？”

比起冗长的解释，更喜欢简洁的例子。

### 设置适当的自由度

将特异性水平与任务的脆弱性和可变性相匹配：

**高自由度（基于文本的指令）**：当多种方法有效、决策取决于上下文或启发式指导方法时使用。

**中等自由度（带有参数的伪代码或脚本）**：当存在首选模式、某些变化可接受或配置影响行为时使用。

**自由度低（特定脚本，参数少）**：当操作脆弱且容易出错、一致性至关重要或必须遵循特定顺序时使用。

将Codex视为探索一条路径：一座带有悬崖的窄桥需要特定的护栏（低自由度），而开阔的场地允许许多路线（高自由度）。

### 技能剖析

每个技能都包含必需的 SKILL.md 文件和可选的捆绑资源：

```
skill-name/
├── SKILL.md (required)
│   ├── YAML frontmatter metadata (required)
│   │   ├── name: (required)
│   │   └── description: (required)
│   └── Markdown instructions (required)
├── agents/ (recommended)
│   └── openai.yaml - UI metadata for skill lists and chips
└── Bundled Resources (optional)
    ├── scripts/          - Executable code (Python/Bash/etc.)
    ├── references/       - Documentation intended to be loaded into context as needed
    └── assets/           - Files used in output (templates, icons, fonts, etc.)
```

#### SKILL.md（必填）

每个 SKILL.md 包含：

- **Frontmatter** (YAML)：包含 `name` 和 `description` 字段。这些是 Codex 读取以确定何时使用该技能的唯一字段，因此清楚而全面地描述该技能是什么以及何时应该使用它非常重要。
- **正文**（Markdown）：使用该技能的说明和指导。仅加载AFTER技能才会触发（如果有的话）。

#### Agents 元数据（推荐）

- UI-面向技能列表和筹码的元数据
- 在生成值之前阅读references/openai_yaml.md并遵循其描述和约束
- 通过阅读技能创建：面向人的`display_name`、`short_description`和`default_prompt`
- 通过将值作为 `--interface key=value` 传递到 `scripts/generate_openai_yaml.py` 或 `scripts/init_skill.py` 来确定性生成
- 更新时：验证 `agents/openai.yaml` 仍然匹配 SKILL.md；如果陈旧则重新生成
- 仅包含明确提供的其他可选界面字段（图标、品牌颜色）
- 有关字段定义和示例，请参阅 references/openai_yaml.md

#### 捆绑资源（可选）

##### 脚本（`scripts/`）

用于需要确定性可靠性或重复重写的任务的可执行代码（Python/Bash/etc.）。

- **何时包含**：当重复重写相同的代码或需要确定性可靠性时
- **示例**：`scripts/rotate_pdf.py`用于PDF轮换任务
- **优点**：令牌高效，确定性，可以在不加载到上下文的情况下执行
- **注意**：脚本可能仍需要由Codex读取以进行修补或特定于环境的调整

##### 参考文献 (`references/`)

旨在根据需要加载到上下文中的文档和参考材料，以告知 Codex 的流程和思维。

- **何时包含**：用于Codex在工作时应参考的文档
- **示例**：`references/finance.md` 表示财务架构，`references/mnda.md` 表示公司 NDA 模板，`references/policies.md` 表示公司政策，`references/api_docs.md` 表示 API 规范
- **用例**：数据库架构、API文档、领域知识、公司政策、详细的工作流程指南
- **优点**：保持SKILL.md精简，仅在Codex确定需要时加载
- **最佳实践**：如果文件很大（> 10k 字），请在 SKILL.md 中包含 grep 搜索模式
- **避免重复**：信息应该存在于SKILL.md或引用文件中，而不是同时存在。优先选择参考文件来获取详细信息，除非它确实是技能的核心，这可以保持 SKILL.md 的精简，同时使信息可在不占用上下文窗口的情况下被发现。仅在 SKILL.md 中保留必要的程序说明和工作流程指南；将详细的参考材料、架构和示例移至参考文件。

##### 资产（`assets/`）

文件不打算加载到上下文中，而是在 Codex 生成的输出中使用。

- **何时包含**：当技能需要在最终输出中使用的文件时
- **示例**：`assets/logo.png`用于品牌资产，`assets/slides.pptx`用于PowerPoint模板，`assets/frontend-template/`用于HTML/React样板，`assets/font.ttf`用于排版
- **用例**：复制或修改的模板、图像、图标、样板代码、字体、示例文档
- **优点**：将输出资源与文档分开，使Codex能够使用文件而不将它们加载到上下文中

#### 技能中不应该包含哪些内容

技能应该只包含直接支持其功能的基本文件。是否NOT创建无关的文档或辅助文件，包括：

- README.md
- INSTALLATION_GUIDE.md
- QUICK_REFERENCE.md
- CHANGELOG.md
- 等等

该技能应仅包含 AI agent 完成手头工作所需的信息。它不应该包含有关创建它的过程、设置和测试过程、面向用户的文档等的辅助上下文。创建额外的文档文件只会增加混乱和混乱。

### 渐进式披露设计原则

技能使用三级加载系统来有效管理上下文：

1. **元数据（名称 + 描述）** - 始终在上下文中（~100 个单词）
2. **SKILL.md body** - 技能触发时（<5k 字）
3. **捆绑资源** - 根据Codex的需要（无限制，因为可以在不读入上下文窗口的情况下执行脚本）

#### 渐进式披露模式

将 SKILL.md 正文保留在 500 行下方，以尽量减少上下文膨胀。当接近此限制时，将内容拆分为单独的文件。将内容拆分到其他文件时，从 SKILL.md 中引用它们并清楚地描述何时阅读它们非常重要，以确保技能的读者知道它们存在以及何时使用它们。

**关键原则：**当一项技能支持多种变体、框架或选项时，仅在SKILL.md中保留核心工作流程和选择指导。将特定于变体的详细信息（模式、示例、配置）移至单独的参考文件中。

**模式1：带参考的高级指南**

```markdown
# PDF Processing

## Quick start

Extract text with pdfplumber:
[code example]

## Advanced features

- **Form filling**: See [FORMS.md](FORMS.md) for complete guide
- **API reference**: See [REFERENCE.md](REFERENCE.md) for all methods
- **Examples**: See [EXAMPLES.md](EXAMPLES.md) for common patterns
```

Codex仅在需要时加载FORMS.md、REFERENCE.md或EXAMPLES.md。

**模式2：特定于域的组织**

对于具有多个领域的技能，请按领域组织内容以避免加载不相关的上下文：

```
bigquery-skill/
├── SKILL.md (overview and navigation)
└── reference/
    ├── finance.md (revenue, billing metrics)
    ├── sales.md (opportunities, pipeline)
    ├── product.md (API usage, features)
    └── marketing.md (campaigns, attribution)
```

当用户询问销售指标时，Codex 只会读取 sales.md。

同样，对于支持多个框架或变体的技能，请按变体进行组织：

```
cloud-deploy/
├── SKILL.md (workflow + provider selection)
└── references/
    ├── aws.md (AWS deployment patterns)
    ├── gcp.md (GCP deployment patterns)
    └── azure.md (Azure deployment patterns)
```

当用户选择AWS时，Codex仅读取aws.md。

**模式3：条件详细信息**

显示基本内容，高级内容链接：

```markdown
# DOCX Processing

## Creating documents

Use docx-js for new documents. See [DOCX-JS.md](DOCX-JS.md).

## Editing documents

For simple edits, modify the XML directly.

**For tracked changes**: See [REDLINING.md](REDLINING.md)
**For OOXML details**: See [OOXML.md](OOXML.md)
```

仅当用户需要这些功能时，Codex 才会读取 REDLINING.md 或 OOXML.md。

**重要指南：**

- **避免深层嵌套引用** - 将引用保持在SKILL.md 的深一层。所有参考文件应直接从 SKILL.md 链接。
- **构建较长的参考文件** - 对于长度超过 100 行的文件，请在顶部包含一个目录，以便 Codex 在预览时可以看到完整范围。

## 技能创造过程

技能创建涉及以下步骤：

1. 通过具体例子理解技巧
2. 规划可重用的技能内容（脚本、参考文献、资产）
3. 初始化技能（运行init_skill.py）
4. 编辑技能（实现资源并写入SKILL.md）
5. 验证技能（运行quick_validate.py）
6. 根据实际使用情况进行迭代

按顺序执行这些步骤，仅当有明确原因不适用时才跳过。

### 技能命名

- 仅使用小写字母、数字和连字符；将用户提供的标题标准化为连字符大小写（例如，“计划模式”-> `plan-mode`）。
- 生成名称时，在 64 个字符（字母、数字、连字符）下生成名称。
- 更喜欢用简短的、以动词为主导的短语来描述动作。
- 当工具提高清晰度或触发时的命名空间（例如，`gh-address-comments`、`linear-address-issue`）。
- 按照技能名称准确命名技能文件夹。

### 步骤1：通过具体例子理解技能

仅当已经清楚地了解技能的使用模式时才跳过此步骤。即使使用现有技能，它仍然很有价值。

要创建有效的技能，请清楚地了解如何使用该技能的具体示例。这种理解可以来自直接的用户示例，也可以来自通过用户反馈验证的生成示例。

例如，在培养图像编辑技能时，相关问题包括：

- “图像编辑器技能应该支持哪些功能？编辑、旋转还是其他功能？”
- “你能举例说明如何使用这项技能吗？”
- “我可以想象用户会要求‘消除此图像的红眼’或‘旋转此图像’之类的内容。您认为这项技能还有其他使用方式吗？”
- “用户会说什么来触发这个技能？”

为了避免让用户感到不知所措，请避免在一条消息中提出太多问题。从最重要的问题开始，并根据需要进行跟进，以提高效率。

当清楚地了解技能应支持的功能时，结束此步骤。

### 步骤2：规划可重复使用的技能内容

要将具体示例转化为有效技能，请通过以下方式分析每个示例：

1. 考虑如何从头开始执行示例
2. 确定哪些脚本、引用和资产在重复执行这些工作流程时会有所帮助

示例：当构建 `pdf-editor` 技能来处理“帮我旋转这个 PDF”之类的查询时，分析显示：

1. 旋转PDF每次都需要重新编写相同的代码
2. `scripts/rotate_pdf.py` 脚本有助于存储在技能中

示例：当为“为我构建一个待办事项应用程序”或“为我构建一个仪表板来跟踪我的步骤”等查询设计 `frontend-webapp-builder` 技能时，分析显示：

1. 每次编写前端 Web 应用程序都需要相同的样板 HTML/React
2. 包含样板 HTML/React 项目文件的 `assets/hello-world/` 模板将有助于存储在技能中

示例：构建 `big-query` 技能来处理“今天有多少用户登录？”之类的查询时分析表明：

1. 查询BigQuery每次都需要重新发现表模式和关系
2. 记录表模式的 `references/schema.md` 文件有助于存储在技能中

要确定技能的内容，请分析每个具体示例以创建可重用资源的列表，其中包括：脚本、引用和资产。

### 步骤3：初始化技能

此时，是时候实际创建该技能了。

仅当正在开发的技能已存在时才跳过此步骤。在这种情况下，请继续执行下一步。

从头开始创建新技能时，请始终运行 `init_skill.py` 脚本。该脚本可以方便地生成一个新的模板技能目录，该目录自动包含技能所需的所有内容，从而使技能创建过程更加高效和可靠。

用途：

```bash
scripts/init_skill.py <skill-name> --path <output-directory> [--resources scripts,references,assets] [--examples]
```

示例：

```bash
scripts/init_skill.py my-skill --path skills/public
scripts/init_skill.py my-skill --path skills/public --resources scripts,references
scripts/init_skill.py my-skill --path skills/public --resources scripts --examples
```

脚本：

- 在指定路径创建技能目录
- 生成具有适当 frontmatter 和 TODO 占位符的 SKILL.md 模板
- 使用 agent 生成的 `display_name`、`short_description` 和通过 `--interface key=value` 传递的 `default_prompt` 创建 `agents/openai.yaml`
- 可以选择根据 `--resources` 创建资源目录
- 设置 `--examples` 时可选择添加示例文件

初始化完成后，自定义SKILL.md，根据需要添加资源。如果您使用`--examples`，请替换或删除占位符文件。

通过读取技能生成 `display_name`、`short_description` 和 `default_prompt`，然后将它们作为 `--interface key=value` 传递给 `init_skill.py` 或使用以下命令重新生成：

```bash
scripts/generate_openai_yaml.py <path/to/skill-folder> --interface key=value
```

仅当用户明确提供其他可选接口字段时才包含它们。有关完整字段描述和示例，请参阅 references/openai_yaml.md.

### 步骤4：编辑技能

编辑（新生成的或现有的）技能时，请记住该技能是为 Codex 的另一个实例创建的以供使用。包括对 Codex 有益且非显而易见的信息。考虑哪些程序知识、特定领域的细节或可重用资产可以帮助另一个 Codex 实例更有效地执行这些任务。

#### 从可重复使用的技能内容开始

要开始实施，请从上面标识的可重用资源开始：`scripts/`、`references/` 和 `assets/` 文件。请注意，此步骤可能需要用户输入。例如，在实现 `brand-guidelines` 技能时，用户可能需要提供要存储在 `assets/` 中的品牌资产或模板，或要存储在 `references/` 中的文档。

添加的脚本必须通过实际运行来进行测试，以确保没有错误并且输出符合预期。如果有许多类似的脚本，则只需测试代表性示例即可确保它们都能正常工作，同时平衡完成时间。

如果您使用了 `--examples`，请删除该技能不需要的所有占位符文件。只创建实际需要的资源目录。

#### 更新SKILL.md

**写作指南：**始终使用imperative/infinitive形式。

##### 前题

用 `name` 和 `description` 编写 YAML 的 frontmatter：

- `name`：技能名称
- `description`：这是你技能的主要触发机制，有助于Codex了解何时使用该技能。
  - 包括该技能的用途以及何时使用该技能的具体 triggers/contexts。
  - 此处包含所有“何时使用”信息 - 不在正文中。正文只有在触发后才会加载，因此正文中的“何时使用此技能”部分对Codex没有帮助。
  - `docx` 技能的示例描述：“全面的文档创建、编辑和分析，支持跟踪更改、注释、格式保存和文本提取。当 Codex 需要处理专业文档（.docx 文件）时使用，用于：(1) 创建新文档，(2) 修改或编辑内容， (3) 使用跟踪的更改，(4) 添加注释或任何其他文档任务”

请勿在 YAML frontmatter 中包含任何其他字段。

##### 身体

编写使用该技能及其捆绑资源的说明。

### 步骤5：验证技能

技能开发完成后，验证技能文件夹以尽早发现基本问题：

```bash
scripts/quick_validate.py <path/to/skill-folder>
```

验证脚本检查 YAML frontmatter 格式、必填字段和命名规则。如果验证失败，请修复报告的问题并再次运行命令。

### 步骤 6：迭代

测试技能后，用户可以请求改进。通常，这种情况会在使用该技能后立即发生，并提供该技能如何执行的新背景。

**迭代工作流程：**

1. 在实际任务中使用该技能
2. 注意困难或低效率
3. 确定如何更新 SKILL.md 或捆绑资源
4. 实施更改并再次测试


## 原文对照

<details>
<summary>展开完整原文</summary>

# Skill Creator

This skill provides guidance for creating effective skills.

## About Skills

Skills are modular, self-contained folders that extend Codex's capabilities by providing
specialized knowledge, workflows, and tools. Think of them as "onboarding guides" for specific
domains or tasks—they transform Codex from a general-purpose agent into a specialized agent
equipped with procedural knowledge that no model can fully possess.

### What Skills Provide

1. Specialized workflows - Multi-step procedures for specific domains
2. Tool integrations - Instructions for working with specific file formats or APIs
3. Domain expertise - Company-specific knowledge, schemas, business logic
4. Bundled resources - Scripts, references, and assets for complex and repetitive tasks

## Core Principles

### Concise is Key

The context window is a public good. Skills share the context window with everything else Codex needs: system prompt, conversation history, other Skills' metadata, and the actual user request.

**Default assumption: Codex is already very smart.** Only add context Codex doesn't already have. Challenge each piece of information: "Does Codex really need this explanation?" and "Does this paragraph justify its token cost?"

Prefer concise examples over verbose explanations.

### Set Appropriate Degrees of Freedom

Match the level of specificity to the task's fragility and variability:

**High freedom (text-based instructions)**: Use when multiple approaches are valid, decisions depend on context, or heuristics guide the approach.

**Medium freedom (pseudocode or scripts with parameters)**: Use when a preferred pattern exists, some variation is acceptable, or configuration affects behavior.

**Low freedom (specific scripts, few parameters)**: Use when operations are fragile and error-prone, consistency is critical, or a specific sequence must be followed.

Think of Codex as exploring a path: a narrow bridge with cliffs needs specific guardrails (low freedom), while an open field allows many routes (high freedom).

### Anatomy of a Skill

Every skill consists of a required SKILL.md file and optional bundled resources:

```
skill-name/
├── SKILL.md (required)
│   ├── YAML frontmatter metadata (required)
│   │   ├── name: (required)
│   │   └── description: (required)
│   └── Markdown instructions (required)
├── agents/ (recommended)
│   └── openai.yaml - UI metadata for skill lists and chips
└── Bundled Resources (optional)
    ├── scripts/          - Executable code (Python/Bash/etc.)
    ├── references/       - Documentation intended to be loaded into context as needed
    └── assets/           - Files used in output (templates, icons, fonts, etc.)
```

#### SKILL.md (required)

Every SKILL.md consists of:

- **Frontmatter** (YAML): Contains `name` and `description` fields. These are the only fields that Codex reads to determine when the skill gets used, thus it is very important to be clear and comprehensive in describing what the skill is, and when it should be used.
- **Body** (Markdown): Instructions and guidance for using the skill. Only loaded AFTER the skill triggers (if at all).

#### Agents metadata (recommended)

- UI-facing metadata for skill lists and chips
- Read references/openai_yaml.md before generating values and follow its descriptions and constraints
- Create: human-facing `display_name`, `short_description`, and `default_prompt` by reading the skill
- Generate deterministically by passing the values as `--interface key=value` to `scripts/generate_openai_yaml.py` or `scripts/init_skill.py`
- On updates: validate `agents/openai.yaml` still matches SKILL.md; regenerate if stale
- Only include other optional interface fields (icons, brand color) if explicitly provided
- See references/openai_yaml.md for field definitions and examples

#### Bundled Resources (optional)

##### Scripts (`scripts/`)

Executable code (Python/Bash/etc.) for tasks that require deterministic reliability or are repeatedly rewritten.

- **When to include**: When the same code is being rewritten repeatedly or deterministic reliability is needed
- **Example**: `scripts/rotate_pdf.py` for PDF rotation tasks
- **Benefits**: Token efficient, deterministic, may be executed without loading into context
- **Note**: Scripts may still need to be read by Codex for patching or environment-specific adjustments

##### References (`references/`)

Documentation and reference material intended to be loaded as needed into context to inform Codex's process and thinking.

- **When to include**: For documentation that Codex should reference while working
- **Examples**: `references/finance.md` for financial schemas, `references/mnda.md` for company NDA template, `references/policies.md` for company policies, `references/api_docs.md` for API specifications
- **Use cases**: Database schemas, API documentation, domain knowledge, company policies, detailed workflow guides
- **Benefits**: Keeps SKILL.md lean, loaded only when Codex determines it's needed
- **Best practice**: If files are large (>10k words), include grep search patterns in SKILL.md
- **Avoid duplication**: Information should live in either SKILL.md or references files, not both. Prefer references files for detailed information unless it's truly core to the skill—this keeps SKILL.md lean while making information discoverable without hogging the context window. Keep only essential procedural instructions and workflow guidance in SKILL.md; move detailed reference material, schemas, and examples to references files.

##### Assets (`assets/`)

Files not intended to be loaded into context, but rather used within the output Codex produces.

- **When to include**: When the skill needs files that will be used in the final output
- **Examples**: `assets/logo.png` for brand assets, `assets/slides.pptx` for PowerPoint templates, `assets/frontend-template/` for HTML/React boilerplate, `assets/font.ttf` for typography
- **Use cases**: Templates, images, icons, boilerplate code, fonts, sample documents that get copied or modified
- **Benefits**: Separates output resources from documentation, enables Codex to use files without loading them into context

#### What to Not Include in a Skill

A skill should only contain essential files that directly support its functionality. Do NOT create extraneous documentation or auxiliary files, including:

- README.md
- INSTALLATION_GUIDE.md
- QUICK_REFERENCE.md
- CHANGELOG.md
- etc.

The skill should only contain the information needed for an AI agent to do the job at hand. It should not contain auxiliary context about the process that went into creating it, setup and testing procedures, user-facing documentation, etc. Creating additional documentation files just adds clutter and confusion.

### Progressive Disclosure Design Principle

Skills use a three-level loading system to manage context efficiently:

1. **Metadata (name + description)** - Always in context (~100 words)
2. **SKILL.md body** - When skill triggers (<5k words)
3. **Bundled resources** - As needed by Codex (Unlimited because scripts can be executed without reading into context window)

#### Progressive Disclosure Patterns

Keep SKILL.md body to the essentials and under 500 lines to minimize context bloat. Split content into separate files when approaching this limit. When splitting out content into other files, it is very important to reference them from SKILL.md and describe clearly when to read them, to ensure the reader of the skill knows they exist and when to use them.

**Key principle:** When a skill supports multiple variations, frameworks, or options, keep only the core workflow and selection guidance in SKILL.md. Move variant-specific details (patterns, examples, configuration) into separate reference files.

**Pattern 1: High-level guide with references**

```markdown
# PDF Processing

## Quick start

Extract text with pdfplumber:
[code example]

## Advanced features

- **Form filling**: See [FORMS.md](FORMS.md) for complete guide
- **API reference**: See [REFERENCE.md](REFERENCE.md) for all methods
- **Examples**: See [EXAMPLES.md](EXAMPLES.md) for common patterns
```

Codex loads FORMS.md, REFERENCE.md, or EXAMPLES.md only when needed.

**Pattern 2: Domain-specific organization**

For Skills with multiple domains, organize content by domain to avoid loading irrelevant context:

```
bigquery-skill/
├── SKILL.md (overview and navigation)
└── reference/
    ├── finance.md (revenue, billing metrics)
    ├── sales.md (opportunities, pipeline)
    ├── product.md (API usage, features)
    └── marketing.md (campaigns, attribution)
```

When a user asks about sales metrics, Codex only reads sales.md.

Similarly, for skills supporting multiple frameworks or variants, organize by variant:

```
cloud-deploy/
├── SKILL.md (workflow + provider selection)
└── references/
    ├── aws.md (AWS deployment patterns)
    ├── gcp.md (GCP deployment patterns)
    └── azure.md (Azure deployment patterns)
```

When the user chooses AWS, Codex only reads aws.md.

**Pattern 3: Conditional details**

Show basic content, link to advanced content:

```markdown
# DOCX Processing

## Creating documents

Use docx-js for new documents. See [DOCX-JS.md](DOCX-JS.md).

## Editing documents

For simple edits, modify the XML directly.

**For tracked changes**: See [REDLINING.md](REDLINING.md)
**For OOXML details**: See [OOXML.md](OOXML.md)
```

Codex reads REDLINING.md or OOXML.md only when the user needs those features.

**Important guidelines:**

- **Avoid deeply nested references** - Keep references one level deep from SKILL.md. All reference files should link directly from SKILL.md.
- **Structure longer reference files** - For files longer than 100 lines, include a table of contents at the top so Codex can see the full scope when previewing.

## Skill Creation Process

Skill creation involves these steps:

1. Understand the skill with concrete examples
2. Plan reusable skill contents (scripts, references, assets)
3. Initialize the skill (run init_skill.py)
4. Edit the skill (implement resources and write SKILL.md)
5. Validate the skill (run quick_validate.py)
6. Iterate based on real usage

Follow these steps in order, skipping only if there is a clear reason why they are not applicable.

### Skill Naming

- Use lowercase letters, digits, and hyphens only; normalize user-provided titles to hyphen-case (e.g., "Plan Mode" -> `plan-mode`).
- When generating names, generate a name under 64 characters (letters, digits, hyphens).
- Prefer short, verb-led phrases that describe the action.
- Namespace by tool when it improves clarity or triggering (e.g., `gh-address-comments`, `linear-address-issue`).
- Name the skill folder exactly after the skill name.

### Step 1: Understanding the Skill with Concrete Examples

Skip this step only when the skill's usage patterns are already clearly understood. It remains valuable even when working with an existing skill.

To create an effective skill, clearly understand concrete examples of how the skill will be used. This understanding can come from either direct user examples or generated examples that are validated with user feedback.

For example, when building an image-editor skill, relevant questions include:

- "What functionality should the image-editor skill support? Editing, rotating, anything else?"
- "Can you give some examples of how this skill would be used?"
- "I can imagine users asking for things like 'Remove the red-eye from this image' or 'Rotate this image'. Are there other ways you imagine this skill being used?"
- "What would a user say that should trigger this skill?"

To avoid overwhelming users, avoid asking too many questions in a single message. Start with the most important questions and follow up as needed for better effectiveness.

Conclude this step when there is a clear sense of the functionality the skill should support.

### Step 2: Planning the Reusable Skill Contents

To turn concrete examples into an effective skill, analyze each example by:

1. Considering how to execute on the example from scratch
2. Identifying what scripts, references, and assets would be helpful when executing these workflows repeatedly

Example: When building a `pdf-editor` skill to handle queries like "Help me rotate this PDF," the analysis shows:

1. Rotating a PDF requires re-writing the same code each time
2. A `scripts/rotate_pdf.py` script would be helpful to store in the skill

Example: When designing a `frontend-webapp-builder` skill for queries like "Build me a todo app" or "Build me a dashboard to track my steps," the analysis shows:

1. Writing a frontend webapp requires the same boilerplate HTML/React each time
2. An `assets/hello-world/` template containing the boilerplate HTML/React project files would be helpful to store in the skill

Example: When building a `big-query` skill to handle queries like "How many users have logged in today?" the analysis shows:

1. Querying BigQuery requires re-discovering the table schemas and relationships each time
2. A `references/schema.md` file documenting the table schemas would be helpful to store in the skill

To establish the skill's contents, analyze each concrete example to create a list of the reusable resources to include: scripts, references, and assets.

### Step 3: Initializing the Skill

At this point, it is time to actually create the skill.

Skip this step only if the skill being developed already exists. In this case, continue to the next step.

When creating a new skill from scratch, always run the `init_skill.py` script. The script conveniently generates a new template skill directory that automatically includes everything a skill requires, making the skill creation process much more efficient and reliable.

Usage:

```bash
scripts/init_skill.py <skill-name> --path <output-directory> [--resources scripts,references,assets] [--examples]
```

Examples:

```bash
scripts/init_skill.py my-skill --path skills/public
scripts/init_skill.py my-skill --path skills/public --resources scripts,references
scripts/init_skill.py my-skill --path skills/public --resources scripts --examples
```

The script:

- Creates the skill directory at the specified path
- Generates a SKILL.md template with proper frontmatter and TODO placeholders
- Creates `agents/openai.yaml` using agent-generated `display_name`, `short_description`, and `default_prompt` passed via `--interface key=value`
- Optionally creates resource directories based on `--resources`
- Optionally adds example files when `--examples` is set

After initialization, customize the SKILL.md and add resources as needed. If you used `--examples`, replace or delete placeholder files.

Generate `display_name`, `short_description`, and `default_prompt` by reading the skill, then pass them as `--interface key=value` to `init_skill.py` or regenerate with:

```bash
scripts/generate_openai_yaml.py <path/to/skill-folder> --interface key=value
```

Only include other optional interface fields when the user explicitly provides them. For full field descriptions and examples, see references/openai_yaml.md.

### Step 4: Edit the Skill

When editing the (newly-generated or existing) skill, remember that the skill is being created for another instance of Codex to use. Include information that would be beneficial and non-obvious to Codex. Consider what procedural knowledge, domain-specific details, or reusable assets would help another Codex instance execute these tasks more effectively.

#### Start with Reusable Skill Contents

To begin implementation, start with the reusable resources identified above: `scripts/`, `references/`, and `assets/` files. Note that this step may require user input. For example, when implementing a `brand-guidelines` skill, the user may need to provide brand assets or templates to store in `assets/`, or documentation to store in `references/`.

Added scripts must be tested by actually running them to ensure there are no bugs and that the output matches what is expected. If there are many similar scripts, only a representative sample needs to be tested to ensure confidence that they all work while balancing time to completion.

If you used `--examples`, delete any placeholder files that are not needed for the skill. Only create resource directories that are actually required.

#### Update SKILL.md

**Writing Guidelines:** Always use imperative/infinitive form.

##### Frontmatter

Write the YAML frontmatter with `name` and `description`:

- `name`: The skill name
- `description`: This is the primary triggering mechanism for your skill, and helps Codex understand when to use the skill.
  - Include both what the Skill does and specific triggers/contexts for when to use it.
  - Include all "when to use" information here - Not in the body. The body is only loaded after triggering, so "When to Use This Skill" sections in the body are not helpful to Codex.
  - Example description for a `docx` skill: "Comprehensive document creation, editing, and analysis with support for tracked changes, comments, formatting preservation, and text extraction. Use when Codex needs to work with professional documents (.docx files) for: (1) Creating new documents, (2) Modifying or editing content, (3) Working with tracked changes, (4) Adding comments, or any other document tasks"

Do not include any other fields in YAML frontmatter.

##### Body

Write instructions for using the skill and its bundled resources.

### Step 5: Validate the Skill

Once development of the skill is complete, validate the skill folder to catch basic issues early:

```bash
scripts/quick_validate.py <path/to/skill-folder>
```

The validation script checks YAML frontmatter format, required fields, and naming rules. If validation fails, fix the reported issues and run the command again.

### Step 6: Iterate

After testing the skill, users may request improvements. Often this happens right after using the skill, with fresh context of how the skill performed.

**Iteration workflow:**

1. Use the skill on real tasks
2. Notice struggles or inefficiencies
3. Identify how SKILL.md or bundled resources should be updated
4. Implement changes and test again


</details>
