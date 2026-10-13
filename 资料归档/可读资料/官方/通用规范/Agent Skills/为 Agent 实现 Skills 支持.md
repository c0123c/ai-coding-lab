# 为 Agent 实现 Skills 支持

覆盖扫描发现、解析字段、目录呈现、按需激活、上下文维护及信任处理，并讨论不同架构的实现方式。

- 来源：通用规范／Agent Skills
- 资料类型：存档文档
- 文档时间：官网现页更新：[2026-03-10](<https://agentskills.io/client-implementation/adding-skills-support>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://agentskills.io/client-implementation/adding-skills-support.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/通用规范/Agent Skills/为 Agent 实现 Skills 支持.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

> ## 文档索引
> 在以下位置获取完整的文档索引： https://agentskills.io/llms.txt
> 在进一步探索之前使用此文件来发现所有可用页面。

# 如何为您的agent添加技能支持

> 向 AI agent 或开发工具添加 Agent Skills 支持的指南。

本指南逐步介绍如何向 AI agent 或开发工具添加 Agent Skills 支持。它涵盖了整个生命周期：发现技能、向模型讲述这些技能、将其内容加载到上下文中，以及保持该内容随着时间的推移有效。

无论您的 agent 的架构如何，核心集成都是相同的。实施细节因两个因素而异：

* **技能在哪里？**本地运行的agent可以扫描用户的文件系统以查找技能目录。云托管或沙盒 agent 将需要替代发现机制 - API、远程注册表或捆绑资产。
* **模型如何访问技能内容？**如果模型具有文件读取功能，则可以直接读取`SKILL.md`文件。否则，您将提供专用工具或以编程方式将技能内容注入提示中。

该指南指出了这些差异的重要之处。您不需要支持每个场景 - 遵循适合您的 agent 的路径。

**先决条件**：熟悉[Agent Skills规范](<https://agentskills.io/specification>)，它定义了`SKILL.md`文件格式、frontmatter字段和目录约定。

## 核心原则：渐进式披露

每个技能兼容的agent都遵循相同的三层加载策略：

|等级|加载了什么|当|代币成本|
| - | - | - | - |
|1。目录|名称+描述|会话开始|\~50-100 每个技能的代币|
|2。使用说明|全【`SKILL.md`】本体|技能发动时|\<5000 代币（推荐）|
|3。资源|脚本、参考文献、资源|当说明引用它们时|各不相同|

该模型从一开始就看到目录，因此它知道哪些技能可用。当它确定某项技能相关时，它会加载完整的说明。如果这些指令引用支持文件，模型将根据需要单独加载它们。

这使得基础上下文保持较小，同时使模型能够按需访问专业知识。具有 20 安装技能的 agent 不会预先支付 20 完整指令集的令牌成本 - 仅支付给定对话中实际使用的指令集。

## 步骤1：发现技能

在会话启动时，查找所有可用的技能并加载其元数据。

### 在哪里扫描

您扫描哪些目录取决于您的agent的环境。大多数本地运行的 agents 至少扫描两个范围：

* **项目级别**（相对于工作目录）：特定于项目或存储库的技能。
* **用户级别**（相对于主目录）：给定用户在所有项目中可用的技能。

其他范围也是可能的 - 例如，由管理员部署的组织范围技能，或与 agent 本身捆绑的技能。正确的范围集取决于您的 agent 的部署模型。

在每个范围内，考虑扫描 ** 客户端特定目录 ** 和 **`.agents/skills/` 约定 **：

|适用范围|路径|目的|
| - | - | - |
|项目| `<project>/.<your-client>/skills/` |您客户的出生地|
|项目| `<project>/.agents/skills/` |跨客户端互操作性|
|用户| `~/.<your-client>/skills/` |您客户的出生地|
|用户| `~/.agents/skills/` |跨客户端互操作性|

`.agents/skills/` 路径已成为广泛采用的跨客户端技能共享约定。虽然 Agent Skills 规范不强制要求技能目录所在的位置（它仅定义其中的内容），但扫描 `.agents/skills/` 意味着其他合规客户端安装的技能对您的客户端自动可见，反之亦然。



**注释：**

一些实现还会扫描 `.claude/skills/`（项目级和用户级）以实现实用兼容性，因为许多现有技能都安装在那里。其他附加位置包括直到 git root 的祖先目录（对于 monorepos 有用）、[XDG](<https://specifications.freedesktop.org/basedir-spec/latest/>) 配置目录和用户配置的路径。


### 扫描什么

在每个技能目录中，查找包含名为 `SKILL.md`** 的文件的 ** 子目录：

```
~/.agents/skills/
├── pdf-processing/
│   ├── SKILL.md          ← discovered
│   └── scripts/
│       └── extract.py
├── data-analysis/
│   └── SKILL.md          ← discovered
└── README.md             ← ignored (not a skill directory)
```

实用扫描规则：

* 跳过不包含技能的目录，例如 `.git/` 和 `node_modules/`
* （可选）尊重 `.gitignore` 以避免扫描构建工件
* 设置合理的边界（例如，4-6级别的最大深度、最大2000目录）以防止大型目录树中的失控扫描

### 处理名称冲突

当两个技能共享相同的 `name` 时，应用确定性优先规则。

现有实现的通用约定：**项目级技能覆盖用户级技能。**

在同一范围内（例如，在 `<project>/.agents/skills/` 和 `<project>/.<your-client>/skills/` 下都找到了两个名为 `code-review` 的技能），第一个找到的或最后找到的都是可以接受的 - 选择一个并保持一致。发生碰撞时记录警告，以便用户知道技能被隐藏。

### 信任考虑

项目级技能来自正在处理的存储库，该存储库可能不受信任（例如，新克隆的开源项目）。考虑在信任检查上控制项目级技能加载 - 仅当用户将项目文件夹标记为可信时才加载它们。这可以防止不受信任的存储库悄悄地将指令注入 agent 的上下文中。

### 云托管和沙盒 agents

如果您的 agent 在容器中或远程服务器上运行，它将无法访问用户的本地文件系统。发现需要根据技能范围以不同的方式工作：

* **项目级技能**通常是最简单的情况。如果 agent 在克隆存储库上运行（甚至在沙箱内），则项目级技能会随代码一起移动，并且可以从存储库的目录树中进行扫描。
* **用户级别和组织级别技能** 沙箱中不存在。您需要从外部源配置它们 - 例如，克隆配置存储库、通过 agent 的设置接受技能 URL 或包，或者让用户通过 Web UI 上传技能目录。
* **内置技能**可以在agent的部署工件中打包为静态资产，使它们在每个会话中都可用，无需外部获取。

一旦 agent 掌握了技能，生命周期的其余部分——解析、披露、激活——就会进行相同的工作。

## 步骤2：解析`SKILL.md`文件

对于每个发现的 `SKILL.md`，提取元数据和正文内容。

### 前题提取

一个 `SKILL.md` 文件有两部分： `---` 分隔符之间的 YAML frontmatter 和结束分隔符之后的 markdown 正文。解析：

1. 找到文件开头的开头 `---` 和其后的结尾 `---`。
2. 解析它们之间的 YAML 块。提取 `name` 和 `description`（必填）以及任何可选字段。
3. 关闭后的所有内容`---`，经过修剪后，就是技能的正文内容。

有关完整的 frontmatter 字段集及其约束，请参阅 [ 规范 ](<https://agentskills.io/specification>)。

### 处理格式错误YAML

为其他客户端编写的技能文件可能包含其解析器碰巧接受的技术上无效的 YAML。最常见的问题是包含冒号的未加引号的值：

```yaml theme={null}
# Technically invalid YAML — the colon breaks parsing
description: Use this skill when: the user asks about PDFs
```

考虑在重试之前将这些值用引号括起来或将它们转换为 YAML 块标量的后备方案。这以最小的成本提高了跨客户端兼容性。

### 宽松的验证

对问题发出警告，但仍尽可能加载技能：

* 名称与父目录名称不匹配 → 警告，仍然加载
* 名称超过 64 个字符→警告，无论如何加载
* 描述缺失或为空 → 跳过该技能（描述对于披露至关重要），记录错误
* YAML 完全无法解析 → 跳过技能，记录错误

记录诊断信息，以便可以向用户显示（在调试命令、日志文件或 UI 中），但不要阻止外观问题的技能加载。



**注释：**

[规范](<https://agentskills.io/specification>)定义了对`name`字段的严格约束（匹配父目录、字符集、最大长度）。上述宽松的方法故意放宽了这些限制，以提高与为其他客户编写的技能的兼容性。


### 储存什么

每个技能记录至少需要三个字段：

|领域|描述|
| - | - |
| `name` |来自前线|
| `description` |来自前线|
| `location` |`SKILL.md` 文件的绝对路径|

将它们存储在由 `name` 键入的内存映射中，以便在激活期间快速查找。

您还可以在发现时存储 **body**（frontmatter 之后的 markdown 内容），或在激活时从 `location` 读取它。存储它可以使激活更快；在激活时读取它总共使用较少的内存，并在激活之间获取技能文件的更改。

稍后需要该技能的 ** 基本目录 **（`location` 的父目录）来解析相对路径并枚举捆绑资源 - 在需要时从 `location` 派生它。

## 步骤 3：向模型披露可用技能

告诉模型存在哪些技能，而无需加载其完整内容。这是 [层 1 渐进式披露](<#核心原则渐进式披露>).

### 建立技能目录

对于每项发现的技能，请以适合您的堆栈的任何结构化格式包含 `name`、`description` 和可选的 `location`（`SKILL.md` 文件的路径） - XML、JSON 或所有工作的项目符号列表：

```xml theme={null}
<available_skills>
  <skill>
    <name>pdf-processing</name>
    <description>Extract PDF text, fill forms, merge files. Use when handling PDFs.</description>
    <location>/home/user/.agents/skills/pdf-processing/SKILL.md</location>
  </skill>
  <skill>
    <name>data-analysis</name>
    <description>Analyze datasets, generate charts, and create summary reports.</description>
    <location>/home/user/project/.agents/skills/data-analysis/SKILL.md</location>
  </skill>
</available_skills>
```

`location` 字段有两个用途：它启用文件读取激活（请参阅 [步骤 4](<#步骤4激活技能>)），并为模型提供用于解析技能主体中的相对引用的基本路径（如 `scripts/evaluate.py`）。如果您的专用激活工具在其结果中提供了技能目录路径（请参阅步骤 4 中的 [结构化包装 ](<#结构化包裹>)），您可以从目录中省略 `location`。否则，将其包含在内。

每个技能都会向目录中添加大约 50-100 标记。即使安装了数十种技能，目录仍然很紧凑。

### 目录放置在哪里

有两种常见方法：

**系统提示部分**：将目录添加为系统提示中的标记部分，前面是如何使用技能的简要说明。这是最简单的方法，适用于任何可以访问文件读取工具的模型。

**工具说明**：将目录嵌入到专用技能激活工具的说明中（参见[步骤4](<#步骤4激活技能>)）。这可以保持系统提示干净，并自然地将发现与激活结合起来。

两者都有效。系统提示放置更简单，兼容更广泛；当您有专用的激活工具时，工具描述嵌入会更清晰。

### 行为指示

在目录旁边包含一个简短的说明块，告诉模型如何以及何时使用技能。措辞取决于您支持的激活机制（请参阅[步骤4](<#步骤4激活技能>)）：

**若模型通过读取文件激活技能：**

```
The following skills provide specialized instructions for specific tasks.
When a task matches a skill's description, use your file-read tool to load
the SKILL.md at the listed location before proceeding.
When a skill references relative paths, resolve them against the skill's
directory (the parent of SKILL.md) and use absolute paths in tool calls.
```

**如果模型通过专用工具激活技能：**

```
The following skills provide specialized instructions for specific tasks.
When a task matches a skill's description, call the activate_skill tool
with the skill's name to load its full instructions.
```

保持这些说明简洁。目标是告诉模型技能存在以及如何加载它们——技能内容本身在加载后提供详细的说明。

### 过滤

某些技能应从目录中排除。常见原因：

* 用户已在设置中禁用该技能
* 权限系统拒绝访问该技能
* 该技能已选择退出模型驱动激活（例如，通过 `disable-model-invocation` 标志）

**从目录中完全隐藏过滤的技能**，而不是列出它们并在激活时阻止。这可以防止模型浪费时间尝试加载它无法使用的技能。

### 当没有可用技能时

如果没有发现技能，则完全忽略目录和行为说明。不要显示空的 `<available_skills/>` 块或注册没有有效选项的技能工具 - 这会使模型感到困惑。

## 步骤4：激活技能

当模型或用户选择一项技能时，将完整的说明传递到对话上下文中。这是 [层 2 渐进式披露](<#核心原则渐进式披露>).

### 模型驱动激活

大多数实现依赖于模型自身的判断作为激活机制，而不是实现线束端触发匹配或关键字检测。模型读取目录（来自[步骤3](<#步骤-3向模型披露可用技能>)），确定技能与当前任务相关，然后加载它。

两种实现模式：

**文件读取激活**：模型使用目录中的`SKILL.md`路径调用其标准文件读取工具。不需要特殊的基础设施——agent现有的文件读取能力就足够了。模型接收文件内容作为工具结果。当模型具有文件访问权限时，这是最简单的方法。

**专用工具激活**：注册一个获取技能名称并返回内容的工具（例如`activate_skill`）。当模型无法直接读取文件时，这是必需的，即使可以，也是可选的（但有用）。相对于原始文件读取的优点：

* 控制返回的内容 - 例如，剥离 YAML frontmatter 或保留它（请参阅下面的 [ 模型收到的内容 ](<#模型收到什么>)）
* 将内容包装在结构化标签中，以便在上下文管理期间进行识别
* 在说明旁边列出捆绑资源（例如，`references/*`）
* 强制执行权限或提示用户同意
* 跟踪激活分析



**提示：**

如果您使用专用激活工具，请将 `name` 参数限制为有效技能名称集（例如，作为工具架构中的枚举）。这可以防止模型产生幻觉不存在的技能名称。如果没有可用的技能，则根本不要注册该工具。


### 用户显式激活

用户还应该能够直接激活技能，而无需等待模型决定。最常见的模式是线束拦截的 ** 斜杠命令或提及语法 **（`/skill-name` 或 `$skill-name`）。具体语法由您决定 - 关键思想是该工具处理查找和注入，因此模型无需本身执行激活操作即可接收技能内容。

自动完成小部件（在用户键入时列出可用技能）也可以使这一点变得可发现。

### 模型收到什么

当技能被激活时，模型会收到该技能的指令。该内容的具体外观有两个选项：

**完整文件**：模型看到整个`SKILL.md`，包括YAML frontmatter。这是文件读取激活的自然结果，其中模型读取原始文件。它也是专用工具的有效选择。 frontmatter 可能包含在激活时有用的字段 - 例如，[`compatibility`](<https://agentskills.io/specification#compatibility-field>) 注释可以告知模型如何执行技能指令的环境要求。

**仅正文（已删除 frontmatter）**：该工具解析并删除 YAML frontmatter，仅返回 markdown 指令。在具有专用激活工具的现有实现中，大多数都采用这种方法 - 在发现过程中提取 `name` 和 `description` 后剥离 frontmatter。

这两种方法在实践中都有效。

### 结构化包裹

如果您使用专用激活工具，请考虑将技能内容包装在识别标签中。例如：

```xml theme={null}
<skill_content name="pdf-processing">
# PDF Processing

## When to use this skill
Use this skill when the user needs to work with PDF files...

[rest of SKILL.md body]

Skill directory: /home/user/.agents/skills/pdf-processing
Relative paths in this skill are relative to the skill directory.

<skill_resources>
  <file>scripts/extract.py</file>
  <file>scripts/merge.py</file>
  <file>references/pdf-spec-summary.md</file>
</skill_resources>
</skill_content>
```

这有实际的好处：

* 该模型可以清楚地区分技能指令和其他对话内容
* 线束可以在上下文压缩期间识别技能内容（[步骤5](<#步骤-5随着时间的推移管理技能背景>)）
* 捆绑的资源会显示在模型中，而无需急切加载

### 列出捆绑资源

当专用激活工具返回技能内容时，它还可以枚举技能目录中的支持文件（脚本、参考、资产）——但它不应该**急切地读取它们**。当技能指令引用特定文件时，模型会使用其文件读取工具按需加载特定文件。

对于大型技能目录，请考虑限制列表并注意它可能不完整。

### 权限白名单

如果您的 agent 具有控制文件访问的权限系统，** 将技能目录列入白名单 **，以便模型可以读取捆绑的资源，而不会触发用户确认提示。如果没有这个，对捆绑脚本或参考文件的每次引用都会导致权限对话框，从而破坏包含 `SKILL.md` 本身之外的资源的技能流程。

## 步骤 5：随着时间的推移管理技能背景

一旦技能说明出现在对话上下文中，请使其在会话期间保持有效。

### 保护技能内容免受上下文压缩

如果您的 agent 在上下文窗口填满时截断或总结较旧的消息，** 会免除修剪技能内容 **。技能指令是持久的行为指导——在对话中丢失它们会默默地降低 agent 的性能，而不会出现任何明显的错误。该模型继续运行，但没有技能提供的专门指令。

常见方法：

* 将技能工具输出标记为受保护，以便修剪算法跳过它们
* 使用步骤 4 中的 [ 结构化标签 ](<#结构化包裹>) 来识别技能内容并在压缩期间保留它

### 重复激活激活

考虑跟踪当前会话中已激活哪些技能。如果模型（或用户）尝试加载上下文中已有的技能，您可以跳过重新注入，以避免相同的指令在对话中多次出现。

### 子代理委托（可选）

这是仅某些客户端支持的高级模式。该技能不是在主对话中注入技能指令，而是在 ** 单独的子代理会话 ** 中运行。子代理接收技能指令，执行任务，并将其工作摘要返回给主对话。

当技能的工作流程足够复杂以至于可以从专门的、集中的会话中受益时，此模式非常有用。


## 原文对照

<details>
<summary>展开完整原文</summary>

> ## Documentation Index
> Fetch the complete documentation index at: https://agentskills.io/llms.txt
> Use this file to discover all available pages before exploring further.

# How to add skills support to your agent

> A guide for adding Agent Skills support to an AI agent or development tool.

This guide walks through how to add Agent Skills support to an AI agent or development tool. It covers the full lifecycle: discovering skills, telling the model about them, loading their content into context, and keeping that content effective over time.

The core integration is the same regardless of your agent's architecture. The implementation details vary based on two factors:

* **Where do skills live?** A locally-running agent can scan the user's filesystem for skill directories. A cloud-hosted or sandboxed agent will need an alternative discovery mechanism — an API, a remote registry, or bundled assets.
* **How does the model access skill content?** If the model has file-reading capabilities, it can read `SKILL.md` files directly. Otherwise, you'll provide a dedicated tool or inject skill content into the prompt programmatically.

The guide notes where these differences matter. You don't need to support every scenario — follow the path that fits your agent.

**Prerequisites**: Familiarity with the [Agent Skills specification](<https://agentskills.io/specification>), which defines the `SKILL.md` file format, frontmatter fields, and directory conventions.

## The core principle: progressive disclosure

Every skills-compatible agent follows the same three-tier loading strategy:

| Tier | What's loaded | When | Token cost |
| - | - | - | - |
| 1. Catalog | Name + description | Session start | \~50-100 tokens per skill |
| 2. Instructions | Full `SKILL.md` body | When the skill is activated | \<5000 tokens (recommended) |
| 3. Resources | Scripts, references, assets | When the instructions reference them | Varies |

The model sees the catalog from the start, so it knows what skills are available. When it decides a skill is relevant, it loads the full instructions. If those instructions reference supporting files, the model loads them individually as needed.

This keeps the base context small while giving the model access to specialized knowledge on demand. An agent with 20 installed skills doesn't pay the token cost of 20 full instruction sets upfront — only the ones actually used in a given conversation.

## Step 1: Discover skills

At session startup, find all available skills and load their metadata.

### Where to scan

Which directories you scan depends on your agent's environment. Most locally-running agents scan at least two scopes:

* **Project-level** (relative to the working directory): Skills specific to a project or repository.
* **User-level** (relative to the home directory): Skills available across all projects for a given user.

Other scopes are possible too — for example, organization-wide skills deployed by an admin, or skills bundled with the agent itself. The right set of scopes depends on your agent's deployment model.

Within each scope, consider scanning both a **client-specific directory** and the **`.agents/skills/` convention**:

| Scope | Path | Purpose |
| - | - | - |
| Project | `<project>/.<your-client>/skills/` | Your client's native location |
| Project | `<project>/.agents/skills/` | Cross-client interoperability |
| User | `~/.<your-client>/skills/` | Your client's native location |
| User | `~/.agents/skills/` | Cross-client interoperability |

The `.agents/skills/` paths have emerged as a widely-adopted convention for cross-client skill sharing. While the Agent Skills specification does not mandate where skill directories live (it only defines what goes inside them), scanning `.agents/skills/` means skills installed by other compliant clients are automatically visible to yours, and vice versa.



**注释：**

  Some implementations also scan `.claude/skills/` (both project-level and user-level) for pragmatic compatibility, since many existing skills are installed there. Other additional locations include ancestor directories up to the git root (useful for monorepos), [XDG](<https://specifications.freedesktop.org/basedir-spec/latest/>) config directories, and user-configured paths.


### What to scan for

Within each skills directory, look for **subdirectories containing a file named exactly `SKILL.md`**:

```
~/.agents/skills/
├── pdf-processing/
│   ├── SKILL.md          ← discovered
│   └── scripts/
│       └── extract.py
├── data-analysis/
│   └── SKILL.md          ← discovered
└── README.md             ← ignored (not a skill directory)
```

Practical scanning rules:

* Skip directories that won't contain skills, such as `.git/` and `node_modules/`
* Optionally respect `.gitignore` to avoid scanning build artifacts
* Set reasonable bounds (e.g., max depth of 4-6 levels, max 2000 directories) to prevent runaway scanning in large directory trees

### Handling name collisions

When two skills share the same `name`, apply a deterministic precedence rule.

The universal convention across existing implementations: **project-level skills override user-level skills.**

Within the same scope (e.g., two skills named `code-review` found under both `<project>/.agents/skills/` and `<project>/.<your-client>/skills/`), either first-found or last-found is acceptable — pick one and be consistent. Log a warning when a collision occurs so the user knows a skill was shadowed.

### Trust considerations

Project-level skills come from the repository being worked on, which may be untrusted (e.g., a freshly cloned open-source project). Consider gating project-level skill loading on a trust check — only load them if the user has marked the project folder as trusted. This prevents untrusted repositories from silently injecting instructions into the agent's context.

### Cloud-hosted and sandboxed agents

If your agent runs in a container or on a remote server, it won't have access to the user's local filesystem. Discovery needs to work differently depending on the skill scope:

* **Project-level skills** are often the easiest case. If the agent operates on a cloned repository (even inside a sandbox), project-level skills travel with the code and can be scanned from the repo's directory tree.
* **User-level and organization-level skills** don't exist in the sandbox. You'll need to provision them from an external source — for example, cloning a configuration repository, accepting skill URLs or packages through your agent's settings, or letting users upload skill directories through a web UI.
* **Built-in skills** can be packaged as static assets within the agent's deployment artifact, making them available in every session without external fetching.

Once skills are available to the agent, the rest of the lifecycle — parsing, disclosure, activation — works the same.

## Step 2: Parse `SKILL.md` files

For each discovered `SKILL.md`, extract the metadata and body content.

### Frontmatter extraction

A `SKILL.md` file has two parts: YAML frontmatter between `---` delimiters, and a markdown body after the closing delimiter. To parse:

1. Find the opening `---` at the start of the file and the closing `---` after it.
2. Parse the YAML block between them. Extract `name` and `description` (required), plus any optional fields.
3. Everything after the closing `---`, trimmed, is the skill's body content.

See the [specification](<https://agentskills.io/specification>) for the full set of frontmatter fields and their constraints.

### Handling malformed YAML

Skill files authored for other clients may contain technically invalid YAML that their parsers happen to accept. The most common issue is unquoted values containing colons:

```yaml theme={null}
# Technically invalid YAML — the colon breaks parsing
description: Use this skill when: the user asks about PDFs
```

Consider a fallback that wraps such values in quotes or converts them to YAML block scalars before retrying. This improves cross-client compatibility at minimal cost.

### Lenient validation

Warn on issues but still load the skill when possible:

* Name doesn't match the parent directory name → warn, load anyway
* Name exceeds 64 characters → warn, load anyway
* Description is missing or empty → skip the skill (a description is essential for disclosure), log the error
* YAML is completely unparseable → skip the skill, log the error

Record diagnostics so they can be surfaced to the user (in a debug command, log file, or UI), but don't block skill loading on cosmetic issues.



**注释：**

  The [specification](<https://agentskills.io/specification>) defines strict constraints on the `name` field (matching the parent directory, character set, max length). The lenient approach above deliberately relaxes these to improve compatibility with skills authored for other clients.


### What to store

At minimum, each skill record needs three fields:

| Field | Description |
| - | - |
| `name` | From frontmatter |
| `description` | From frontmatter |
| `location` | Absolute path to the `SKILL.md` file |

Store these in an in-memory map keyed by `name` for fast lookup during activation.

You can also store the **body** (the markdown content after the frontmatter) at discovery time, or read it from `location` at activation time. Storing it makes activation faster; reading it at activation time uses less memory in aggregate and picks up changes to skill files between activations.

The skill's **base directory** (the parent directory of `location`) is needed later to resolve relative paths and enumerate bundled resources — derive it from `location` when needed.

## Step 3: Disclose available skills to the model

Tell the model what skills exist without loading their full content. This is [tier 1 of progressive disclosure](<#the-core-principle-progressive-disclosure>).

### Building the skill catalog

For each discovered skill, include `name`, `description`, and optionally `location` (the path to the `SKILL.md` file) in whatever structured format suits your stack — XML, JSON, or a bulleted list all work:

```xml theme={null}
<available_skills>
  <skill>
    <name>pdf-processing</name>
    <description>Extract PDF text, fill forms, merge files. Use when handling PDFs.</description>
    <location>/home/user/.agents/skills/pdf-processing/SKILL.md</location>
  </skill>
  <skill>
    <name>data-analysis</name>
    <description>Analyze datasets, generate charts, and create summary reports.</description>
    <location>/home/user/project/.agents/skills/data-analysis/SKILL.md</location>
  </skill>
</available_skills>
```

The `location` field serves two purposes: it enables file-read activation (see [Step 4](<#step-4-activate-skills>)), and it gives the model a base path for resolving relative references in the skill body (like `scripts/evaluate.py`). If your dedicated activation tool provides the skill directory path in its result (see [Structured wrapping](<#structured-wrapping>) in Step 4), you can omit `location` from the catalog. Otherwise, include it.

Each skill adds roughly 50-100 tokens to the catalog. Even with dozens of skills installed, the catalog remains compact.

### Where to place the catalog

Two approaches are common:

**System prompt section**: Add the catalog as a labeled section in the system prompt, preceded by brief instructions on how to use skills. This is the simplest approach and works with any model that has access to a file-reading tool.

**Tool description**: Embed the catalog in the description of a dedicated skill-activation tool (see [Step 4](<#step-4-activate-skills>)). This keeps the system prompt clean and naturally couples discovery with activation.

Both work. System prompt placement is simpler and more broadly compatible; tool description embedding is cleaner when you have a dedicated activation tool.

### Behavioral instructions

Include a short instruction block alongside the catalog telling the model how and when to use skills. The wording depends on which activation mechanism you support (see [Step 4](<#step-4-activate-skills>)):

**If the model activates skills by reading files:**

```
The following skills provide specialized instructions for specific tasks.
When a task matches a skill's description, use your file-read tool to load
the SKILL.md at the listed location before proceeding.
When a skill references relative paths, resolve them against the skill's
directory (the parent of SKILL.md) and use absolute paths in tool calls.
```

**If the model activates skills via a dedicated tool:**

```
The following skills provide specialized instructions for specific tasks.
When a task matches a skill's description, call the activate_skill tool
with the skill's name to load its full instructions.
```

Keep these instructions concise. The goal is to tell the model that skills exist and how to load them — the skill content itself provides the detailed instructions once loaded.

### Filtering

Some skills should be excluded from the catalog. Common reasons:

* The user has disabled the skill in settings
* A permission system denies access to the skill
* The skill has opted out of model-driven activation (e.g., via a `disable-model-invocation` flag)

**Hide filtered skills entirely** from the catalog rather than listing them and blocking at activation time. This prevents the model from wasting turns attempting to load skills it can't use.

### When no skills are available

If no skills are discovered, omit the catalog and behavioral instructions entirely. Don't show an empty `<available_skills/>` block or register a skill tool with no valid options — this would confuse the model.

## Step 4: Activate skills

When the model or user selects a skill, deliver the full instructions into the conversation context. This is [tier 2 of progressive disclosure](<#the-core-principle-progressive-disclosure>).

### Model-driven activation

Most implementations rely on the model's own judgment as the activation mechanism, rather than implementing harness-side trigger matching or keyword detection. The model reads the catalog (from [Step 3](<#step-3-disclose-available-skills-to-the-model>)), decides a skill is relevant to the current task, and loads it.

Two implementation patterns:

**File-read activation**: The model calls its standard file-read tool with the `SKILL.md` path from the catalog. No special infrastructure needed — the agent's existing file-reading capability is sufficient. The model receives the file content as a tool result. This is the simplest approach when the model has file access.

**Dedicated tool activation**: Register a tool (e.g., `activate_skill`) that takes a skill name and returns the content. This is required when the model can't read files directly, and optional (but useful) even when it can. Advantages over raw file reads:

* Control what content is returned — e.g., strip YAML frontmatter or preserve it (see [What the model receives](<#what-the-model-receives>) below)
* Wrap content in structured tags for identification during context management
* List bundled resources (e.g., `references/*`) alongside the instructions
* Enforce permissions or prompt for user consent
* Track activation for analytics



**提示：**

  If you use a dedicated activation tool, constrain the `name` parameter to the set of valid skill names (e.g., as an enum in the tool schema). This prevents the model from hallucinating nonexistent skill names. If no skills are available, don't register the tool at all.


### User-explicit activation

Users should also be able to activate skills directly, without waiting for the model to decide. The most common pattern is a **slash command or mention syntax** (`/skill-name` or `$skill-name`) that the harness intercepts. The specific syntax is up to you — the key idea is that the harness handles the lookup and injection, so the model receives skill content without needing to take an activation action itself.

An autocomplete widget (listing available skills as the user types) can also make this discoverable.

### What the model receives

When a skill is activated, the model receives the skill's instructions. Two options for what exactly that content looks like:

**Full file**: The model sees the entire `SKILL.md` including YAML frontmatter. This is the natural outcome with file-read activation, where the model reads the raw file. It's also a valid choice for dedicated tools. The frontmatter may contain fields useful at activation time — for example, [`compatibility`](<https://agentskills.io/specification#compatibility-field>) notes environment requirements that could inform how the model executes the skill's instructions.

**Body only (frontmatter stripped)**: The harness parses and removes the YAML frontmatter, returning only the markdown instructions. Among existing implementations with dedicated activation tools, most take this approach — stripping the frontmatter after extracting `name` and `description` during discovery.

Both approaches work in practice.

### Structured wrapping

If you use a dedicated activation tool, consider wrapping skill content in identifying tags. For example:

```xml theme={null}
<skill_content name="pdf-processing">
# PDF Processing

## When to use this skill
Use this skill when the user needs to work with PDF files...

[rest of SKILL.md body]

Skill directory: /home/user/.agents/skills/pdf-processing
Relative paths in this skill are relative to the skill directory.

<skill_resources>
  <file>scripts/extract.py</file>
  <file>scripts/merge.py</file>
  <file>references/pdf-spec-summary.md</file>
</skill_resources>
</skill_content>
```

This has practical benefits:

* The model can clearly distinguish skill instructions from other conversation content
* The harness can identify skill content during context compaction ([Step 5](<#step-5-manage-skill-context-over-time>))
* Bundled resources are surfaced to the model without being eagerly loaded

### Listing bundled resources

When a dedicated activation tool returns skill content, it can also enumerate supporting files (scripts, references, assets) in the skill directory — but it should **not eagerly read them**. The model loads specific files on demand using its file-read tools when the skill's instructions reference them.

For large skill directories, consider capping the listing and noting that it may be incomplete.

### Permission allowlisting

If your agent has a permission system that gates file access, **allowlist skill directories** so the model can read bundled resources without triggering user confirmation prompts. Without this, every reference to a bundled script or reference file results in a permission dialog, breaking the flow for skills that include resources beyond the `SKILL.md` itself.

## Step 5: Manage skill context over time

Once skill instructions are in the conversation context, keep them effective for the duration of the session.

### Protect skill content from context compaction

If your agent truncates or summarizes older messages when the context window fills up, **exempt skill content from pruning**. Skill instructions are durable behavioral guidance — losing them mid-conversation silently degrades the agent's performance without any visible error. The model continues operating but without the specialized instructions the skill provided.

Common approaches:

* Flag skill tool outputs as protected so the pruning algorithm skips them
* Use the [structured tags](<#structured-wrapping>) from Step 4 to identify skill content and preserve it during compaction

### Deduplicate activations

Consider tracking which skills have been activated in the current session. If the model (or user) attempts to load a skill that's already in context, you can skip the re-injection to avoid the same instructions appearing multiple times in the conversation.

### Subagent delegation (optional)

This is an advanced pattern only supported by some clients. Instead of injecting skill instructions into the main conversation, the skill is run in a **separate subagent session**. The subagent receives the skill instructions, performs the task, and returns a summary of its work to the main conversation.

This pattern is useful when a skill's workflow is complex enough to benefit from a dedicated, focused session.


</details>
