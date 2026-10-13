# Antigravity 项目规则

说明持久指令的位置、目录作用范围、激活方式和引用文件的方法，用于约束编码规范与架构选择。

- 来源：Google／Antigravity
- 资料类型：存档文档
- 文档时间：官方未公开发表/更新时间（[已复查](<https://antigravity.google/docs/rules/>)）；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://antigravity.google/docs/rules/>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Antigravity/Antigravity 项目规则.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 规则

规则是持久性指令、编码标准和架构
约束，它们决定了反重力在代码库中的行为方式。与适用于单个对话的聊天
提示不同，反重力会自动
从您的文件系统中发现规则并将其注入agent的上下文中。

## 规则存储位置

反重力发现跨多个范围的规则，下面按 
 优先级顺序列出（从高到低）。规则是累积的，而不是基于
替换：反重力将全局、
工作空间和目录范围中所有发现的规则合并到提示中。当指令冲突时，
更具体的目录规则优先。

### 目录范围的规则

您可以将 AGENTS.md 或 GEMINI.md 文件（或 .agents/rules/ 目录）放置在项目的任何子目录中。每当Antigravity读取或
编辑文件时，它都会从该文件的文件夹沿着目录树向上走到
工作空间根目录，在每个级别加载规则：

- <dir>/AGENTS.md 或 <dir>/GEMINI.md

- <dir>/.agents/AGENTS.md 或 <dir>/.agents/GEMINI.md

- <dir>/.agents/rules/*.md（以及旧版 <dir>/.agent/rules/*.md）

### 全球规则

全局规则适用于您工作站上的所有项目：

- 独立全局文件（不需要 frontmatter，始终处于活动状态）： ~/.gemini/AGENTS.md 、 ~/.gemini/GEMINI.md 、 ~/.gemini/config/AGENTS.md 或 ~/.gemini/config/GEMINI.md

- 模块化全局规则（需要 YAML frontmatter）：~/.gemini/config/rules/*.md

## YAML frontmatter 和激活模式

Frontmatter 要求取决于文件类型：

- AGENTS.md 和 GEMINI.md 不使用 frontmatter。反重力治疗
其全部内容为纯 Markdown，并在其目录范围内保持持续活动
(always_on)。

- rules/ 内的每个 .md 文件都必须以 YAML frontmatter 开头，声明有效的触发器。

### 支持的 frontmatter 键

.agents/rules/ 中的规则支持以下 YAML frontmatter 键：

```
---\n
\ntrigger\n: \nmodel_decision\n       # Required: always_on | model_decision | glob | manual\n
\ndescription\n: \n"..."\n            # Required for model_decision; recommended for all rules\n
\nglobs\n: \n"*.ts, *.tsx"\n          # Required for glob (singular `glob:` is also accepted)\n
\n---
```

字段 类型 必需 描述 触发器字符串 是 控制规则激活的方式和时间： model_decision 、 always_on 、 glob 或 manual 。描述字符串 model_decision 所需 规则适用时间的简明摘要。 Antigravity 将此摘要注入到 model_decision 规则的提示索引中，并在 always_on 规则超出令牌预算时将其用作后备上下文。 glob （或 glob ） string glob 必需的 以逗号分隔的文件 glob 模式（例如， "*.py, *_test.py" ）。将以 * 开头的模式括在引号中，以便 YAML 不会将 * 视为别名锚点。

### 激活模式

配置触发键以控制如何以及何时将规则加载到
上下文中：

- model_decision（最适合详细的领域指南）：反重力
仅预先注入规则的路径和描述（渐进式
披露）。当您的任务与 
 描述匹配时，agent 会按需读取完整规则。 --- 触发器：model_decision 描述：“每当编写或审查数据库迁移或 SQL 架构更改时，请应用这些准则。” --- # 数据库迁移规则1。切勿在单个部署中删除或重命名列；使用扩展和收缩迁移。 2。在 PostgreSQL 中的现有表上创建索引时，始终添加 `CONCURRENTLY`。

model_decision（最适合详细的领域指南）：反重力
仅预先注入规则的路径和描述（渐进式
披露）。当您的任务与 
 描述匹配时，agent 会按需读取完整规则。

```
---\n
\ntrigger\n: \nmodel_decision\n
\ndescription\n: \n"Apply these guidelines whenever writing or reviewing database migrations or SQL schema changes."\n
\n---\n
\n
\n# Database Migration Rules\n
\n
\n1.\n Never drop or rename a column in a single deployment; use an expand-and-contract migration.\n
\n2.\n Always add \n`CONCURRENTLY`\n when creating indexes on existing tables in PostgreSQL.
```

- always_on（始终在范围内有效）：反重力注入全部
每次都会将内容输入到系统提示中。当您不需要 frontmatter 时，最好将 always_on 规则直接放在 AGENTS.md 中。

always_on（在范围内始终处于活动状态）：反重力每次都会将完整的
内容注入系统提示符中。当您不需要 frontmatter 时，最好将 always_on 规则直接放在 AGENTS.md 中。

- glob（文件模式范围）：当 agent 时自动激活
与匹配 glob 中任何逗号分隔模式的文件进行交互： --- 触发器 : glob glob : "*.proto, **/*.pb.go" 描述 : "Protobuf 架构设计和向后兼容性规则。" --- # Protobuf 约定 * 切勿重复使用已删除的字段编号；始终将字段编号和名称标记为 `reserved` 。

glob（文件模式范围）：当 agent
 与匹配 glob 中任何逗号分隔模式的文件交互时自动激活：

```
---\n
\ntrigger\n: \nglob\n
\nglobs\n: \n"*.proto, **/*.pb.go"\n
\ndescription\n: \n"Protobuf schema design and backward-compatibility rules."\n
\n---\n
\n
\n# Protobuf Conventions\n
\n
\n*\n Never reuse a deleted field number; always mark both the field number and name as \n`reserved`\n.
```

- 手册（仅明确@提及）：反重力永远不会加载这些
自动规则。仅当您在聊天中明确 @ 提及它时，agent 才会注入规则（非常适合发布清单或审核规则）。

手册（仅明确@提及）：反重力永远不会自动加载这些
规则。仅当您在聊天中明确 @ 提及它时，agent 才会注入规则（非常适合发布清单或审核规则）。

## 高级功能和限制

### 文件包括

在任何规则文件（ AGENTS.md 、 GEMINI.md 或 .agents/rules/*.md ）内，
Antigravity 支持两种不同的 @ 语法来引用文件：

- @[label](<https://antigravity.google/docs/rules/path>) ：内联目标文件的内容
在及时评估和大小限制检查之前直接进入规则。路径解析：反重力解析包含规则文件的
目录的相对路径。以 ~/ 开头的路径扩展到您的 
 用户主目录。 Frontmatter 剥离：如果包含的文件包含 YAML frontmatter，
Antigravity 会剥离 frontmatter，并仅在 Markdown 正文中进行拼接。

- 路径解析：反重力解析相对路径
包含规则文件的目录。以 ~/ 开头的路径扩展到您的 
 用户主目录。

- Frontmatter 剥离：如果包含的文件包含 YAML frontmatter，
Antigravity 剥离了 frontmatter 并仅在 Markdown 正文中进行拼接。

- @filename ：不内联文件内容。
相反，它解析路径（相对于规则文件，或作为 
workspace-relative/absolute 路径）并将其重写为规范的绝对 
 工作空间路径 ( @/workspace/path )，因此 agent 具有明确的文件 
 引用。

```
Follow our shared API design standards:\n
\n@[\nAPI Design Standards\n](\n../docs/api-standards.md\n)\n
\n
\nWhen updating database models, check @src/db/schema.ts first.
```

### 跨项目共享规则

要共享存储在 .agents/rules/ 外部的规则或包含规则的嵌套 
 子目录，同时保留其 YAML frontmatter 和触发器，
create .agents/rules.json ：

```
{\n
\n  "inherits"\n: [\n
\n    { \n"path"\n: \n"../shared-config/.agents/rules.json"\n }\n
\n  ],\n
\n  "entries"\n: [\n
\n    { \n"path"\n: \n"../shared-rules"\n, \n"exclude"\n: [\n"deprecated_rules.md"\n, \n"legacy/"\n] },\n
\n    { \n"path"\n: \n"rules"\n, \n"include_only"\n: [\n"frontend/react.md"\n] }\n
\n  ]\n
\n}
```

### 规模限制和代币预算

为了防止规则耗尽上下文窗口，Antigravity 强制执行两个 
 限制：

- 24 KB（24,000 字节）每个文件限制：反重力截断任何单个
超过 24,000 字节的规则文件（扩展 @[label](<https://antigravity.google/docs/rules/path>) 后包含）。

- 20,000-令牌聚合规则预算：所有活动的全局和always_on规则共享与自定义预算分开的20,000-令牌预算
（技能和MCP等插件）。如果总数超过 20,000 令牌，
反重力会自动将最大的规则文件从内联文本降级为 
轻量级指针（ - <path>: <description> ），从而允许 agent按需阅读
。

## 按表面配置

在下面选择您的表面以配置全局或特定于工作区的规则：

- 反重力2.0

- 反重力CLI

- 反重力 IDE 和扩展

### 反重力2.0

#### 管理反重力规则 2.0

请按照以下步骤在 Antigravity 2.0 中创建或管理规则：

- 从应用程序菜单或项目设置中打开“自定义”面板。

- 选择规则选项卡。

- 单击“+ 全局”创建全局规则，或单击“+ 工作区”创建规则
范围仅限于活动项目。

#### 反重力 2.0 文件位置

反重力 2.0 从以下位置加载规则：

- 工作区规则： AGENTS.md 、 GEMINI.md 或 .agents/rules/*.md 在您的
工作区根目录或任何项目子目录。

- 全局规则： ~/.gemini/AGENTS.md 、 ~/.gemini/GEMINI.md 或模块化
规则在 ~/.gemini/config/rules/*.md 中。

### 反重力CLI

#### 管理反重力规则 CLI

反重力 CLI 在提示扩展期间评估工作空间、目录范围、全局和插件 
 规则：

- 工作空间和目录规则：存储库根目录或子目录中的 AGENTS.md 、 GEMINI.md 或 .agents/rules/*.md 。

- 全局规则： ~/.gemini/AGENTS.md 、 ~/.gemini/GEMINI.md 、 ~/.gemini/config/rules/*.md 或 ~/.gemini/antigravity-cli/rules/*.md 。

- 插件规则：反重力自动激活里面打包的规则
在 ~/.gemini/antigravity-cli/plugins/<plugin_name>/rules/ 下安装了插件。

### 反重力 IDE 和扩展

#### 管理反重力 IDE 和扩展中的规则

请按照以下步骤在 Antigravity IDE 和扩展中创建或管理规则：

- 单击 agent 侧面板顶部的 ... 菜单。

- 选择“自定义”，然后导航至“规则”选项卡。

- 单击“+ 全局”来创作工作站范围的规则，或单击“+ 工作区”来创建特定于项目的规则。

#### 反重力 IDE 和扩展文件位置

反重力 IDE 和扩展从以下位置加载规则：

- 工作区规则： AGENTS.md 、 GEMINI.md 或 .agents/rules/*.md 内
您的项目或子目录。

- 全局规则： ~/.gemini/AGENTS.md 、 ~/.gemini/GEMINI.md 或 ~/.gemini/config/rules/*.md 。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Rules

Rules are persistent instructions, coding standards, and architectural
constraints that shape how Antigravity behaves in your codebase. Unlike chat
prompts that apply to a single conversation, Antigravity automatically
discovers rules from your filesystem and injects them into the agent’s context.

## Where rules are stored

Antigravity discovers rules across multiple scopes, listed below in order of
precedence (highest to lowest). Rules are cumulative rather than
replacement-based: Antigravity combines all discovered rules across global,
workspace, and directory scopes into the prompt. When instructions conflict,
more specific directory rules take priority.

### Directory-scoped rules

You can place an AGENTS.md or GEMINI.md file—or a .agents/rules/ directory—in any subdirectory of your project. Whenever Antigravity reads or
edits a file, it walks up the directory tree from that file’s folder to the
workspace root, loading rules at each level:

- <dir>/AGENTS.md or <dir>/GEMINI.md

- <dir>/.agents/AGENTS.md or <dir>/.agents/GEMINI.md

- <dir>/.agents/rules/*.md (and legacy <dir>/.agent/rules/*.md )

### Global rules

Global rules apply across all projects on your workstation:

- Standalone global files (no frontmatter needed, always active): ~/.gemini/AGENTS.md , ~/.gemini/GEMINI.md , ~/.gemini/config/AGENTS.md , or ~/.gemini/config/GEMINI.md

- Modular global rules (YAML frontmatter required): ~/.gemini/config/rules/*.md

## YAML frontmatter and activation modes

Frontmatter requirements depend on the file type:

- AGENTS.md and GEMINI.md do not use frontmatter. Antigravity treats
its entire content as plain Markdown and keeps it continuously active
( always_on ) for its directory scope.

- Every .md file inside rules/ must start with YAML frontmatter declaring a valid trigger .

### Supported frontmatter keys

Rules in .agents/rules/ support the following YAML frontmatter keys:

```
---\n
\ntrigger\n: \nmodel_decision\n       # Required: always_on | model_decision | glob | manual\n
\ndescription\n: \n"..."\n            # Required for model_decision; recommended for all rules\n
\nglobs\n: \n"*.ts, *.tsx"\n          # Required for glob (singular `glob:` is also accepted)\n
\n---
```

Field Type Required Description trigger string Yes Controls how and when the rule activates: model_decision , always_on , glob , or manual . description string Required for model_decision Concise summary of when the rule applies. Antigravity injects this summary into the prompt index for model_decision rules, and uses it as fallback context if an always_on rule exceeds the token budget. globs (or glob ) string Required for glob Comma-separated file glob patterns (for example, "*.py, *_test.py" ). Wrap patterns starting with * in quotes so YAML does not treat * as an alias anchor.

### Activation modes

Configure the trigger key to control how and when a rule is loaded into
context:

- model_decision (best for detailed domain guides) : Antigravity
injects only the rule’s path and description upfront (progressive
disclosure). The agent reads the full rule on demand when your task matches
the description. --- trigger : model_decision description : "Apply these guidelines whenever writing or reviewing database migrations or SQL schema changes." --- # Database Migration Rules 1. Never drop or rename a column in a single deployment; use an expand-and-contract migration. 2. Always add `CONCURRENTLY` when creating indexes on existing tables in PostgreSQL.

model_decision (best for detailed domain guides) : Antigravity
injects only the rule’s path and description upfront (progressive
disclosure). The agent reads the full rule on demand when your task matches
the description.

```
---\n
\ntrigger\n: \nmodel_decision\n
\ndescription\n: \n"Apply these guidelines whenever writing or reviewing database migrations or SQL schema changes."\n
\n---\n
\n
\n# Database Migration Rules\n
\n
\n1.\n Never drop or rename a column in a single deployment; use an expand-and-contract migration.\n
\n2.\n Always add \n`CONCURRENTLY`\n when creating indexes on existing tables in PostgreSQL.
```

- always_on (always active in scope) : Antigravity injects the full
content into the system prompt on every turn. Prefer placing always_on rules directly in AGENTS.md when you do not need frontmatter.

always_on (always active in scope) : Antigravity injects the full
content into the system prompt on every turn. Prefer placing always_on rules directly in AGENTS.md when you do not need frontmatter.

- glob (file-pattern scoped) : Automatically activates when the agent
interacts with files matching any comma-separated pattern in globs : --- trigger : glob globs : "*.proto, **/*.pb.go" description : "Protobuf schema design and backward-compatibility rules." --- # Protobuf Conventions * Never reuse a deleted field number; always mark both the field number and name as `reserved` .

glob (file-pattern scoped) : Automatically activates when the agent
interacts with files matching any comma-separated pattern in globs :

```
---\n
\ntrigger\n: \nglob\n
\nglobs\n: \n"*.proto, **/*.pb.go"\n
\ndescription\n: \n"Protobuf schema design and backward-compatibility rules."\n
\n---\n
\n
\n# Protobuf Conventions\n
\n
\n*\n Never reuse a deleted field number; always mark both the field number and name as \n`reserved`\n.
```

- manual (explicit @ mention only) : Antigravity never loads these
rules automatically. The agent injects the rule only when you explicitly @ -mention it in chat (ideal for release checklists or audit rubrics).

manual (explicit @ mention only) : Antigravity never loads these
rules automatically. The agent injects the rule only when you explicitly @ -mention it in chat (ideal for release checklists or audit rubrics).

## Advanced features and limits

### File includes

Inside any rule file ( AGENTS.md , GEMINI.md , or .agents/rules/*.md ),
Antigravity supports two distinct @ syntaxes for referencing files:

- @[label](<https://antigravity.google/docs/rules/path>) : Inlines the target file’s contents
directly into the rule before prompt evaluation and size-limit checks. Path resolution : Antigravity resolves relative paths from the
directory containing the rule file. Paths starting with ~/ expand to your
user home directory. Frontmatter stripping : If an included file contains YAML frontmatter,
Antigravity strips the frontmatter and splices in only the Markdown body.

- Path resolution : Antigravity resolves relative paths from the
directory containing the rule file. Paths starting with ~/ expand to your
user home directory.

- Frontmatter stripping : If an included file contains YAML frontmatter,
Antigravity strips the frontmatter and splices in only the Markdown body.

- @filename : Does not inline file contents.
Instead, it resolves the path (relative to the rule file, or as a
workspace-relative/absolute path) and rewrites it to a canonical absolute
workspace path ( @/workspace/path ) so the agent has an unambiguous file
reference.

```
Follow our shared API design standards:\n
\n@[\nAPI Design Standards\n](\n../docs/api-standards.md\n)\n
\n
\nWhen updating database models, check @src/db/schema.ts first.
```

### Sharing rules across projects

To share rules stored outside .agents/rules/ or to include nested
subdirectories of rules while preserving their YAML frontmatter and triggers,
create .agents/rules.json :

```
{\n
\n  "inherits"\n: [\n
\n    { \n"path"\n: \n"../shared-config/.agents/rules.json"\n }\n
\n  ],\n
\n  "entries"\n: [\n
\n    { \n"path"\n: \n"../shared-rules"\n, \n"exclude"\n: [\n"deprecated_rules.md"\n, \n"legacy/"\n] },\n
\n    { \n"path"\n: \n"rules"\n, \n"include_only"\n: [\n"frontend/react.md"\n] }\n
\n  ]\n
\n}
```

### Size limits and token budgets

To prevent rules from exhausting the context window, Antigravity enforces two
limits:

- 24 KB ( 24,000 bytes) per-file limit : Antigravity truncates any single
rule file that exceeds 24,000 bytes (after expanding @[label](<https://antigravity.google/docs/rules/path>) includes).

- 20,000-token aggregate rules budget : All active global and always_on rules share a 20,000-token budget separate from the customization budget
(plugins such as skills and MCP). If the total exceeds 20,000 tokens,
Antigravity automatically demotes the largest rule files from inline text to
lightweight pointers ( - <path>: <description> ), allowing the agent to read
them on demand.

## Configuration by surface

Select your surface below to configure global or workspace-specific rules:

- Antigravity 2.0

- Antigravity CLI

- Antigravity IDE & Extensions

### Antigravity 2.0

#### Managing rules in Antigravity 2.0

Follow these steps to create or manage rules in Antigravity 2.0:

- Open the Customizations panel from the application menu or project settings.

- Select the Rules tab.

- Click + Global to create global rules, or + Workspace to create rules
scoped to the active project.

#### Antigravity 2.0 file locations

Antigravity 2.0 loads rules from the following locations:

- Workspace rules : AGENTS.md , GEMINI.md , or .agents/rules/*.md in your
workspace root or any project subdirectory.

- Global rules : ~/.gemini/AGENTS.md , ~/.gemini/GEMINI.md , or modular
rules in ~/.gemini/config/rules/*.md .

### Antigravity CLI

#### Managing rules in Antigravity CLI

The Antigravity CLI evaluates workspace, directory-scoped, global, and plugin
rules during prompt expansion:

- Workspace and directory rules : AGENTS.md , GEMINI.md , or .agents/rules/*.md at the repository root or in subdirectories.

- Global rules : ~/.gemini/AGENTS.md , ~/.gemini/GEMINI.md , ~/.gemini/config/rules/*.md , or ~/.gemini/antigravity-cli/rules/*.md .

- Plugin rules : Antigravity automatically activates rules packaged inside
installed plugins under ~/.gemini/antigravity-cli/plugins/<plugin_name>/rules/ .

### Antigravity IDE & Extensions

#### Managing rules in Antigravity IDE and extensions

Follow these steps to create or manage rules in Antigravity IDE and extensions:

- Click the … menu at the top of the agent side panel.

- Select Customizations , then navigate to the Rules tab.

- Click + Global to author workstation-wide rules, or + Workspace to create project-specific rules.

#### Antigravity IDE and extensions file locations

Antigravity IDE and extensions load rules from the following locations:

- Workspace rules : AGENTS.md , GEMINI.md , or .agents/rules/*.md within
your project or subdirectories.

- Global rules : ~/.gemini/AGENTS.md , ~/.gemini/GEMINI.md , or ~/.gemini/config/rules/*.md .


</details>
