# 构建 Skills

说明技能文件、发现位置、调用、分享、安装和元数据配置，帮助把开发操作整理成可复用能力。

- 来源：OpenAI／Codex
- 资料类型：存档文档
- 文档时间：官方未公开发表/更新时间（[已复查](<https://learn.chatgpt.com/docs/build-skills>)）；本地抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://learn.chatgpt.com/docs/build-skills.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Codex/构建 Skills.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 培养技能

> 完整的文档索引，请参见[llms.txt](<https://learn.chatgpt.com/llms.txt>)。通过将 `.md` 附加到页面 URL 可以获得文档页面的 Markdown 版本。

使用 agent skills 来扩展 ChatGPT 和 Codex 特定于任务的功能。 
技能包含说明、资源和可选脚本，因此任一产品
都可以可靠地遵循工作流程。技能建立在
[开放agent skills标准](<https://agentskills.io>)之上。

技能是可重用工作流程的创作格式。插件分发
通过通用插件目录共享可重用的技能和连接器
通过 ChatGPT 和 Codex。插件可在聊天和工作中使用 ChatGPT 在网络上，
桌面版和移动版 Codex 在 ChatGPT 桌面应用程序，并通过 Codex
CLI。使用技能来设计工作流程本身，然后将其打包为
[插件](<https://developers.openai.com/plugins/build/plugins>) 当你想要的时候
其他人来安装它。

独立技能可在 ChatGPT 桌面应用程序、Codex CLI 和 IDE
 扩展中使用。插件中捆绑的技能也可在网络、桌面和移动设备上的 
ChatGPT 的聊天和工作中使用。

在 ChatGPT 桌面应用程序中，打开侧边栏中的 ** 技能 **，以查看和探索在您的项目中创建的技能 
。


  

> 插图：技能选择器显示 ChatGPT 桌面应用程序中的可用技能




技能使用**渐进式披露**来有效地管理上下文。 ChatGPT 和 
Codex 从每个技能的名称和描述开始，然后在决定使用该技能时加载完整的 
`SKILL.md` 说明。

在Codex中，初始列表还包括每个技能的文件路径。为了避免 
 挤掉提示的其余部分，此列表最多使用模型的 
 上下文窗口的 2% 个字符，或者当上下文窗口未知时使用 8,000 个字符。如果安装了很多
技能，Codex会先缩短技能描述。对于大型技能
集，Codex可能会从初始列表中省略一些技能并显示警告。

此预算仅适用于初始技能列表。当Codex选择一项技能时，它仍然会读取该技能的完整SKILL.md说明。

技能是一个包含 `SKILL.md` 文件以及可选脚本和参考的目录。 `SKILL.md` 文件必须包含 `name` 和 `description`。

<FileTree
 class="mt-4"
 tree={[
 {
 name: "my-skill/",
 open: true,
 Children: [
 {
 name: "SKILL.md",
 comment: "必需：指令+元数据",
 },
 {
 name: "scripts/",
 comment: "可选：可执行代码",
 },
 {
 name: "references/",
 comment: "可选：文档",
 },
 {
 name: "assets/",
 comment: "可选：模板、资源",
 },
 {
 name: "agents/",
 open: true,
 Children: [
 {
 name: "openai.yaml",
 comment: "可选：外观和依赖项",
 },
 ],
 },
 ],
 },

]}
/>

<a id="how-codex-uses-skills"></a>

## ChatGPT和Codex如何使用技能

ChatGPT和Codex可以通过两种方式激活技能：

1. **显式调用：**直接在提示中包含技能。在
ChatGPT，输入`@`选择技能。在 Codex CLI 或 IDE 扩展中，运行 
 `/skills` 或输入 `$` 来提及技能。
2. **隐式调用：** ChatGPT或Codex可以在任务时选择技能
匹配技能`description`。

由于隐式匹配依赖于`description`，因此请编写简洁的描述
，并具有明确的范围和边界。预先加载关键用例和触发词
，以便主机在描述缩短的情况下仍然可以匹配技能。

## 创建技能

如果您已经了解工作流程并且展示比描述更容易，请使用
[记录和重播](<https://learn.chatgpt.com/docs/extend/record-and-replay>)。记录器捕获 
 工作流程，检查步骤，并从 
 演示中起草可重用的技能。

如果您想描述该技能，请使用内置创建器。在ChatGPT
Work中，将其调用为`@skill-creator`。在Codex中，将其调用为：

```text
$skill-creator
```

创建者会询问该技能的作用、何时触发以及是否应该仅包含指令或包含脚本。默认情况下仅指示。

您还可以通过创建包含 `SKILL.md` 文件的文件夹来手动创建技能：

```md
---
name: skill-name
description: Explain exactly when this skill should and should not trigger.
---

Skill instructions for ChatGPT or Codex to follow.
```

Codex自动检测技能变化。如果未出现更新，请重新启动 Codex。

<a id="where-to-save-skills"></a>

## Codex 加载本地技能的位置

Codex 从存储库、用户、管理员和系统位置读取技能。对于存储库，Codex 扫描从当前工作目录到存储库根目录的每个目录中的 `.agents/skills`。如果两个技能共享相同的`name`，则Codex不会将它们合并；两者都可以出现在技能选择器中。

|技能范围|地点|建议使用|
| :---------- | :-------------------------------------------------------------------------------------------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `REPO`      |`$CWD/.agents/skills` <br /> 当前工作目录：启动 Codex 的位置。|如果您处于存储库或代码环境中，团队可以签入与工作文件夹相关的技能。例如，仅与微服务或模块相关的技能。|
| `REPO`      |`$CWD/../.agents/skills` <br /> 当您在 Git 存储库中启动 Codex 时，CWD 上方的文件夹。|如果您位于包含嵌套文件夹的存储库中，组织可以签入与父文件夹中的共享区域相关的技能。|
| `REPO`      |`$REPO_ROOT/.agents/skills` <br /> 在 Git 存储库中启动 Codex 时的最顶层根文件夹。|如果您位于包含嵌套文件夹的存储库中，组织可以签入与使用该存储库的每个人相关的技能。这些作为根技能可用于存储库中的任何子文件夹。|
| `USER`      |`$HOME/.agents/skills` <br /> 检入用户个人文件夹中的任何技能。|用于管理与用户相关的技能，这些技能适用于用户可能使用的任何存储库。|
| `ADMIN`     |`/etc/codex/skills` <br /> 在共享系统位置签入计算机或容器的任何技能。|用于 SDK 脚本、自动化以及检查计算机上每个用户可用的默认管理技能。|
| `SYSTEM`    |与 OpenAI 的 Codex 捆绑在一起。|与广泛受众相关的有用技能，例如技能创建者和计划技能。每个人在启动时都可以使用 Codex。|

Codex 支持符号链接技能文件夹，并在扫描这些位置时遵循符号链接目标。

这些位置用于创作和本地发现。当您想要
在单个存储库之外分发可重用技能，或者选择将它们与
连接器捆绑在一起时，请使用[插件](<https://developers.openai.com/plugins/build/plugins>)。

## 通过插件分发技能

直接技能文件夹最适合本地创作和存储库范围的工作流程。如果
您想要分发可重用技能，将两个或多个技能捆绑在一起，或者
将技能与连接器一起发送，请将它们打包为
[插件](<https://developers.openai.com/plugins/build/plugins>)。

插件可以包含一项或多项技能。他们还可以选择将
注册的MCP服务器连接、捆绑的MCP服务器配置和
演示资源捆绑在单个包中。

## 安装精选技能以供本地使用

要为您自己的本地 Codex 设置添加内置技能之外的精选技能，请使用 `$skill-installer`。例如，要安装`$linear`技能：

```bash
$skill-installer linear
```

您还可以提示安装程序从其他存储库下载技能。
Codex自动检测新安装的技能；如果没有出现，
重新启动Codex。

使用它进行本地设置和实验。为了可重复使用地分发您的
自己的技能，最好使用插件。

## 启用或禁用本地Codex技能

使用`~/.codex/config.toml`中的`[[skills.config]]`条目禁用技能而不删除它：

```toml
[[skills.config]]
path = "/path/to/skill/SKILL.md"
enabled = false
```

更改`~/.codex/config.toml`后重新启动Codex。

## 可选元数据

添加 `agents/openai.yaml` 以在 [ChatGPT 桌面应用程序 ](<https://learn.chatgpt.com/docs/app>) 中配置 UI 元数据，设置调用策略，并声明工具依赖项，以获得更无缝的技能使用体验。

```yaml
interface:
  display_name: "Optional user-facing name"
  short_description: "Optional user-facing description"
  icon_small: "./assets/small-logo.svg"
  icon_large: "./assets/large-logo.png"
  brand_color: "#3B82F6"
  default_prompt: "Optional surrounding prompt to use the skill with"

policy:
  allow_implicit_invocation: false

dependencies:
  tools:
    - type: "mcp"
      value: "openaiDeveloperDocs"
      description: "OpenAI Docs MCP server"
      transport: "streamable_http"
      url: "https://developers.openai.com/mcp"
```

`allow_implicit_invocation`（默认：`true`）：当`false`、Codex时，不会根据用户提示隐式调用技能；显式 `$skill` 调用仍然有效。

## 最佳实践

- 让每项技能都专注于一项工作。
- 优先使用指令而不是脚本，除非您需要确定性行为或外部工具。
- 编写具有明确输入和输出的命令式步骤。
- 根据技能描述测试提示以确认正确的触发行为。

有关更多示例，请参阅
[GitHub CI 修复](<https://github.com/openai/skills/tree/main/skills/.curated/gh-fix-ci>)、
[PDF](<https://github.com/openai/skills/tree/main/skills/.curated/pdf>)、
[线性](<https://github.com/openai/skills/tree/main/skills/.curated/linear>)、
[openai/skills](<https://github.com/openai/skills>) 和
[agent skills规格](<https://agentskills.io/specification>)。对于
可安装发行版，首选[插件](<https://developers.openai.com/plugins/build/plugins>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Build skills

> For the complete documentation index, see [llms.txt](<https://learn.chatgpt.com/llms.txt>). Markdown versions of documentation pages are available by appending `.md` to the page URL.

Use agent skills to extend ChatGPT and Codex with task-specific capabilities. A
skill packages instructions, resources, and optional scripts so either product
can follow a workflow reliably. Skills build on the
[open agent skills standard](<https://agentskills.io>).

Skills are the authoring format for reusable workflows. Plugins distribute
reusable skills and connectors through the universal plugin directory shared
by ChatGPT and Codex. Plugins work in Chat and Work across ChatGPT on the web,
desktop, and mobile, in Codex in the ChatGPT desktop app, and through Codex
CLI. Use skills to design the workflow itself, then package it as a
[plugin](<https://developers.openai.com/plugins/build/plugins>) when you want
other people to install it.

Standalone skills are available in the ChatGPT desktop app, Codex CLI, and IDE
extension. Skills bundled in plugins are also available in Chat and Work across
ChatGPT on the web, desktop, and mobile.

In the ChatGPT desktop app, open **Skills** in the sidebar to view and explore skills
created across your projects.


  

> Illustration: Skills picker showing available skills in the ChatGPT desktop app




Skills use **progressive disclosure** to manage context efficiently. ChatGPT and
Codex start with each skill's name and description, then load the full
`SKILL.md` instructions when they decide to use that skill.

In Codex, the initial list also includes each skill's file path. To avoid
crowding out the rest of the prompt, this list uses at most 2% of the model's
context window, or 8,000 characters when the context window is unknown. If many
skills are installed, Codex shortens skill descriptions first. For large skill
sets, Codex may omit some skills from the initial list and show a warning.

This budget applies only to the initial skills list. When Codex selects a skill, it still reads the full SKILL.md instructions for that skill.

A skill is a directory with a `SKILL.md` file plus optional scripts and references. The `SKILL.md` file must include `name` and `description`.

<FileTree
  class="mt-4"
  tree={[
    {
      name: "my-skill/",
      open: true,
      children: [
        {
          name: "SKILL.md",
          comment: "Required: instructions + metadata",
        },
        {
          name: "scripts/",
          comment: "Optional: executable code",
        },
        {
          name: "references/",
          comment: "Optional: documentation",
        },
        {
          name: "assets/",
          comment: "Optional: templates, resources",
        },
        {
          name: "agents/",
          open: true,
          children: [
            {
              name: "openai.yaml",
              comment: "Optional: appearance and dependencies",
            },
          ],
        },
      ],
    },

]}
/>

<a id="how-codex-uses-skills"></a>

## How ChatGPT and Codex use skills

ChatGPT and Codex can activate skills in two ways:

1. **Explicit invocation:** Include the skill directly in your prompt. In
   ChatGPT, type `@` to select a skill. In Codex CLI or the IDE extension, run
   `/skills` or type `$` to mention a skill.
2. **Implicit invocation:** ChatGPT or Codex can choose a skill when your task
   matches the skill `description`.

Because implicit matching depends on `description`, write concise descriptions
with clear scope and boundaries. Front-load the key use case and trigger words
so a host can still match the skill if descriptions are shortened.

## Create a skill

If you already know the workflow and it's easier to show than describe, use
[Record & Replay](<https://learn.chatgpt.com/docs/extend/record-and-replay>). The recorder captures the
workflow, inspects the steps, and drafts a reusable skill from the
demonstration.

If you want to describe the skill instead, use the built-in creator. In ChatGPT
Work, invoke it as `@skill-creator`. In Codex, invoke it as:

```text
$skill-creator
```

The creator asks what the skill does, when it should trigger, and whether it should stay instruction-only or include scripts. Instruction-only is the default.

You can also create a skill manually by creating a folder with a `SKILL.md` file:

```md
---
name: skill-name
description: Explain exactly when this skill should and should not trigger.
---

Skill instructions for ChatGPT or Codex to follow.
```

Codex detects skill changes automatically. If an update doesn't appear, restart Codex.

<a id="where-to-save-skills"></a>

## Where Codex loads local skills

Codex reads skills from repository, user, admin, and system locations. For repositories, Codex scans `.agents/skills` in every directory from your current working directory up to the repository root. If two skills share the same `name`, Codex doesn't merge them; both can appear in skill selectors.

| Skill Scope | Location                                                                                                  | Suggested use                                                                                                                                                                                        |
| :---------- | :-------------------------------------------------------------------------------------------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `REPO`      | `$CWD/.agents/skills` <br /> Current working directory: where you launch Codex.                           | If you're in a repository or code environment, teams can check in skills relevant to a working folder. For example, skills only relevant to a microservice or a module.                              |
| `REPO`      | `$CWD/../.agents/skills` <br /> A folder above CWD when you launch Codex inside a Git repository.         | If you're in a repository with nested folders, organizations can check in skills relevant to a shared area in a parent folder.                                                                       |
| `REPO`      | `$REPO_ROOT/.agents/skills` <br /> The topmost root folder when you launch Codex inside a Git repository. | If you're in a repository with nested folders, organizations can check in skills relevant to everyone using the repository. These serve as root skills available to any subfolder in the repository. |
| `USER`      | `$HOME/.agents/skills` <br /> Any skills checked into the user's personal folder.                         | Use to curate skills relevant to a user that apply to any repository the user may work in.                                                                                                           |
| `ADMIN`     | `/etc/codex/skills` <br /> Any skills checked into the machine or container in a shared, system location. | Use for SDK scripts, automation, and for checking in default admin skills available to each user on the machine.                                                                                     |
| `SYSTEM`    | Bundled with Codex by OpenAI.                                                                             | Useful skills relevant to a broad audience such as the skill-creator and plan skills. Available to everyone when they start Codex.                                                                   |

Codex supports symlinked skill folders and follows the symlink target when scanning these locations.

These locations are for authoring and local discovery. When you want to
distribute reusable skills beyond a single repo, or optionally bundle them with
connectors, use [plugins](<https://developers.openai.com/plugins/build/plugins>).

## Distribute skills with plugins

Direct skill folders are best for local authoring and repo-scoped workflows. If
you want to distribute a reusable skill, bundle two or more skills together, or
ship a skill alongside a connector, package them as a
[plugin](<https://developers.openai.com/plugins/build/plugins>).

Plugins can include one or more skills. They can also optionally bundle
registered MCP server connections, bundled MCP server configuration, and
presentation assets in a single package.

## Install curated skills for local use

To add curated skills beyond the built-ins for your own local Codex setup, use `$skill-installer`. For example, to install the `$linear` skill:

```bash
$skill-installer linear
```

You can also prompt the installer to download skills from other repositories.
Codex detects newly installed skills automatically; if one doesn't appear,
restart Codex.

Use this for local setup and experimentation. For reusable distribution of your
own skills, prefer plugins.

## Enable or disable local Codex skills

Use `[[skills.config]]` entries in `~/.codex/config.toml` to disable a skill without deleting it:

```toml
[[skills.config]]
path = "/path/to/skill/SKILL.md"
enabled = false
```

Restart Codex after changing `~/.codex/config.toml`.

## Optional metadata

Add `agents/openai.yaml` to configure UI metadata in the [ChatGPT desktop app](<https://learn.chatgpt.com/docs/app>), to set invocation policy, and to declare tool dependencies for a more seamless experience with using the skill.

```yaml
interface:
  display_name: "Optional user-facing name"
  short_description: "Optional user-facing description"
  icon_small: "./assets/small-logo.svg"
  icon_large: "./assets/large-logo.png"
  brand_color: "#3B82F6"
  default_prompt: "Optional surrounding prompt to use the skill with"

policy:
  allow_implicit_invocation: false

dependencies:
  tools:
    - type: "mcp"
      value: "openaiDeveloperDocs"
      description: "OpenAI Docs MCP server"
      transport: "streamable_http"
      url: "https://developers.openai.com/mcp"
```

`allow_implicit_invocation` (default: `true`): When `false`, Codex won't implicitly invoke the skill based on user prompt; explicit `$skill` invocation still works.

## Best practices

- Keep each skill focused on one job.
- Prefer instructions over scripts unless you need deterministic behavior or external tooling.
- Write imperative steps with explicit inputs and outputs.
- Test prompts against the skill description to confirm the right trigger behavior.

For more examples, see
[GitHub CI repair](<https://github.com/openai/skills/tree/main/skills/.curated/gh-fix-ci>),
[PDF](<https://github.com/openai/skills/tree/main/skills/.curated/pdf>),
[Linear](<https://github.com/openai/skills/tree/main/skills/.curated/linear>),
[openai/skills](<https://github.com/openai/skills>), and the
[agent skills specification](<https://agentskills.io/specification>). For
installable distribution, prefer [plugins](<https://developers.openai.com/plugins/build/plugins>).


</details>
