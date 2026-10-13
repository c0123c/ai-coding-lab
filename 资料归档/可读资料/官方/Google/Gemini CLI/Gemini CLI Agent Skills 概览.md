# Gemini CLI Agent Skills 概览

说明技能发现层级、优先级、别名和管理命令，帮助理解技能怎样为具体任务加载专业流程和资源。

- 来源：Google／Gemini CLI
- 资料类型：存档文档
- 文档时间：官方文件提交（截至抓取）：[2026-04-30](<https://github.com/google-gemini/gemini-cli/commit/778da08ee1ec3c3cee2e77014af7fcfa33a915ec>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://raw.githubusercontent.com/google-gemini/gemini-cli/main/docs/cli/skills.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Gemini CLI/Gemini CLI Agent Skills 概览.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Agent Skills

Agent Skills 让您可以利用专业知识、程序性 
 工作流程和特定于任务的资源来扩展 Gemini CLI。基于
[Agent Skills](<https://agentskills.io>)开放标准，“技能”是一个
独立的目录，它将指令和资产打包到
可发现的功能中。

与一般上下文文件不同（[GEMINI.md](<通过 GEMINI.md 提供项目上下文.md>)），这提供了
持久的工作空间背景，技能代表 **按需专业知识**.
这让 Gemini CLI 维护一个庞大的专业能力库，例如
安全审计、云部署或代码库迁移——不混乱
模型的直接上下文窗口。

## 它是如何运作的

Agent 技能的生命周期涉及发现、激活和有条件的 
 资源访问。

1.  **发现**：在会话开始时，Gemini CLI扫描发现
层并将所有启用的技能的名称和描述注入到 
 系统提示符中。
2.  **激活**：当Gemini识别出与技能匹配的任务时
描述，它调用`activate_skill`工具。
3.  **同意**：您将在UI中看到一条确认提示，详细说明了
技能的名称、用途以及它将访问的目录路径。
4.  **注射**：经您批准：
    - `SKILL.md` 正文和文件夹结构已添加到对话中
历史。
    - 该技能的目录被添加到agent允许的文件路径中，授予
它允许读取任何捆绑的资产。
5.  **执行**：模型以活跃的专业知识进行。它
被指示在合理范围内优先考虑技能的程序指导。

## 发现层

Gemini CLI 从多个位置发现技能，遵循特定的优先顺序 
（从最低到最高）：

1.  **内置技能**：Gemini CLI包含的标准技能提供
基础能力。
2.  **扩展技能**：安装中捆绑的技能
[扩展](<https://github.com/google-gemini/gemini-cli/blob//docs/extensions/index.md>)。
3.  **用户技能**：位于`~/.gemini/skills/`或`~/.agents/skills/`
别名。
4.  **工作空间技能**：位于`.gemini/skills/`或`.agents/skills/`
别名。通过版本控制与您的团队共享工作空间技能。

### 优先级和别名

如果多个技能共享相同的名称，则使用优先级较高的 
 位置中的版本。在同一层（用户或工作区）中，
`.agents/skills/` 别名优先于 `.gemini/skills/` 目录。

`.agents/skills/` 别名提供了一个可互操作的路径，用于管理 
agent 特定的专业知识，并且在不同的 AI 工具之间保持兼容。

## 主要优点

Agent Skills 为管理专业知识和
复杂的工作流程提供了多种优势。

- **共享专业知识**：打包复杂的工作流程（如特定团队的PR
审查过程）放入任何人都可以使用的文件夹中。
- **可重复的工作流程**：确保执行复杂的多步骤任务
通过提供程序框架来保持一致。
- **资源捆绑**：包括脚本、模板或示例数据
说明，因此 agent 拥有所需的一切。
- **渐进式披露**：仅技能元数据（名称和描述）
最初加载。仅当
模型显式激活技能并保存上下文令牌时，才会公开详细的指令和资源。

<!-- prettier-ignore -->
> [!NOTE]
> `/skills disable` 和 `/skills enable` 默认为 `user` 范围。使用
> `--scope workspace` 管理特定于工作区的设置。

要查看当前会话中的所有可用技能，请使用 `/skills list`
 命令。

## 管理技巧

你可以管理 Agent Skills 通过交互式会话命令或直接
从您的终端。

### 在互动环节中

使用 `/skills` 斜杠命令查看和管理可用的专业知识：

- `/skills list [all] [nodesc]`：显示已发现的技能。使用 `all` 包含
内置技能和`nodesc`隐藏描述。
- `/skills link <path> [--scope user|workspace]`：链接本地技能
目录。
- `/skills disable <name>`：阻止使用特定技能。
- `/skills enable <name>`：重新启用已禁用的技能。
- `/skills reload`（或`/skills refresh`）：刷新已发现的列表
各个级别的技能。

### 从航站楼出发

`gemini skills`命令提供管理实用程序：

```bash
# List all discovered skills. Use --all to include built-in skills.
gemini skills list --all

# Install a skill from a Git repository or local directory.
# Use --consent to skip the security confirmation prompt.
gemini skills install https://github.com/user/repo.git --consent

# Uninstall a skill.
gemini skills uninstall my-skill --scope workspace
```

#### 命令选项

技能管理命令支持多个全局和命令特定的 
 选项。

- `--scope`：`user`（全局，默认）或`workspace`（本地）
项目）。
- `--path`：包含技能的 Git 存储库中的子目录。
- `--consent`：确认安全风险并跳过交互确认
在安装过程中。

有关 CLI 命令的更多详细信息，请参阅 
[CLI 参考资料 ](<https://github.com/google-gemini/gemini-cli/blob//docs/cli/cli-reference.md#skills-management>)。

## 后续步骤

探索这些资源以提高您的技能并更好地理解框架
。

- [开始使用Agent Skills](<https://github.com/google-gemini/gemini-cli/blob//docs/cli/tutorials/skills-getting-started.md>)：A
触发和使用技能的快速演练。
- [创建Agent Skills](<https://github.com/google-gemini/gemini-cli/blob//docs/cli/creating-skills.md>)：创建你的第一个技能并
捆绑自定义逻辑。
- [使用Agent Skills](<https://github.com/google-gemini/gemini-cli/blob//docs/cli/using-agent-skills.md>)：了解如何利用内置
和定制技能。
- [最佳实践](<https://github.com/google-gemini/gemini-cli/blob//docs/cli/skills-best-practices.md>)：学习构建策略
有效的技能。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Agent Skills

Agent Skills let you extend Gemini CLI with specialized expertise, procedural
workflows, and task-specific resources. Based on the
[Agent Skills](<https://agentskills.io>) open standard, a "skill" is a
self-contained directory that packages instructions and assets into a
discoverable capability.

Unlike general context files ([GEMINI.md](<通过 GEMINI.md 提供项目上下文.md>)), which provide
persistent workspace-wide background, Skills represent **on-demand expertise**.
This lets Gemini CLI maintain a vast library of specialized capabilities—such as
security auditing, cloud deployments, or codebase migrations—without cluttering
the model's immediate context window.

## How it works

The lifecycle of an Agent Skill involves discovery, activation, and conditional
resource access.

1.  **Discovery**: At the start of a session, Gemini CLI scans the discovery
    tiers and injects the name and description of all enabled skills into the
    system prompt.
2.  **Activation**: When Gemini identifies a task matching a skill's
    description, it calls the `activate_skill` tool.
3.  **Consent**: You will see a confirmation prompt in the UI detailing the
    skill's name, purpose, and the directory path it will gain access to.
4.  **Injection**: Upon your approval:
    - The `SKILL.md` body and folder structure is added to the conversation
      history.
    - The skill's directory is added to the agent's allowed file paths, granting
      it permission to read any bundled assets.
5.  **Execution**: The model proceeds with the specialized expertise active. It
    is instructed to prioritize the skill's procedural guidance within reason.

## Discovery tiers

Gemini CLI discovers skills from several locations, following a specific order
of precedence (lowest to highest):

1.  **Built-in skills**: Standard skills included with Gemini CLI that provide
    foundational capabilities.
2.  **Extension skills**: Skills bundled within installed
    [extensions](<https://github.com/google-gemini/gemini-cli/blob//docs/extensions/index.md>).
3.  **User skills**: Located in `~/.gemini/skills/` or the `~/.agents/skills/`
    alias.
4.  **Workspace skills**: Located in `.gemini/skills/` or the `.agents/skills/`
    alias. Workspace skills are shared with your team via version control.

### Precedence and aliases

If multiple skills share the same name, the version from the higher-precedence
location is used. Within the same tier (user or workspace), the
`.agents/skills/` alias takes precedence over the `.gemini/skills/` directory.

The `.agents/skills/` alias provides an interoperable path for managing
agent-specific expertise that remains compatible across different AI tools.

## Key benefits

Agent Skills provide several advantages for managing specialized knowledge and
complex workflows.

- **Shared expertise**: Package complex workflows (like a specific team's PR
  review process) into a folder that anyone can use.
- **Repeatable workflows**: Ensure complex multi-step tasks are performed
  consistently by providing a procedural framework.
- **Resource bundling**: Include scripts, templates, or example data alongside
  instructions so the agent has everything it needs.
- **Progressive disclosure**: Only skill metadata (name and description) is
  loaded initially. Detailed instructions and resources are only disclosed when
  the model explicitly activates the skill, saving context tokens.

<!-- prettier-ignore -->
> [!NOTE]
> `/skills disable` and `/skills enable` default to the `user` scope. Use
> `--scope workspace` to manage workspace-specific settings.

To see all available skills in your current session, use the `/skills list`
command.

## Managing skills

You can manage Agent Skills through interactive session commands or directly
from your terminal.

### In an interactive session

Use the `/skills` slash command to view and manage available expertise:

- `/skills list [all] [nodesc]`: Shows discovered skills. Use `all` to include
  built-in skills and `nodesc` to hide descriptions.
- `/skills link <path> [--scope user|workspace]`: Links skills from a local
  directory.
- `/skills disable <name>`: Prevents a specific skill from being used.
- `/skills enable <name>`: Re-enables a disabled skill.
- `/skills reload` (or `/skills refresh`): Refreshes the list of discovered
  skills from all tiers.

### From the terminal

The `gemini skills` command provides management utilities:

```bash
# List all discovered skills. Use --all to include built-in skills.
gemini skills list --all

# Install a skill from a Git repository or local directory.
# Use --consent to skip the security confirmation prompt.
gemini skills install https://github.com/user/repo.git --consent

# Uninstall a skill.
gemini skills uninstall my-skill --scope workspace
```

#### Command options

The skill management commands support several global and command-specific
options.

- `--scope`: Either `user` (global, default) or `workspace` (local to the
  project).
- `--path`: The sub-directory within a Git repository containing the skill.
- `--consent`: Acknowledge security risks and skip the interactive confirmation
  during installation.

For more details on CLI commands, see the
[CLI reference](<https://github.com/google-gemini/gemini-cli/blob//docs/cli/cli-reference.md#skills-management>).

## Next steps

Explore these resources to refine your skills and understand the framework
better.

- [Get started with Agent Skills](<https://github.com/google-gemini/gemini-cli/blob//docs/cli/tutorials/skills-getting-started.md>): A
  quick walkthrough of triggering and using skills.
- [Creating Agent Skills](<https://github.com/google-gemini/gemini-cli/blob//docs/cli/creating-skills.md>): Create your first skill and
  bundle custom logic.
- [Using Agent Skills](<https://github.com/google-gemini/gemini-cli/blob//docs/cli/using-agent-skills.md>): Learn how to leverage built-in
  and custom skills.
- [Best practices](<https://github.com/google-gemini/gemini-cli/blob//docs/cli/skills-best-practices.md>): Learn strategies for building
  effective skills.


</details>
