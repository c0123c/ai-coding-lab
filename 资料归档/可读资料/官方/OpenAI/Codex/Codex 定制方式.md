# Codex 定制方式

比较 AGENTS.md、Skills、MCP 和子 Agent，说明组合使用的场景以及怎样逐步配置项目能力。

- 来源：OpenAI／Codex
- 资料类型：存档文档
- 文档时间：官方未公开发表/更新时间（[已复查](<https://learn.chatgpt.com/docs/customization/overview>)）；本地抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://learn.chatgpt.com/docs/customization/overview.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Codex/Codex 定制方式.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 定制化

> 完整的文档索引，请参见[llms.txt](<https://learn.chatgpt.com/llms.txt>)。通过将 `.md` 附加到页面 URL 可以获得文档页面的 Markdown 版本。

定制是您如何让 Codex 按照您的团队的方式工作。

在 Codex 中，定制来自协同工作的几个层：

- **项目指导 (`AGENTS.md`)** 用于持久指令
- **[记忆](<https://learn.chatgpt.com/docs/customization/memories>)**从以前的工作中学到的有用的上下文
- **可重用工作流程和领域专业知识的技能**
- **[MCP](<https://learn.chatgpt.com/docs/extend/mcp>)** 用于访问外部工具和共享系统
- **[子代理](<https://learn.chatgpt.com/docs/agent-configuration/subagents>)**用于将工作委派给专门的子代理

这些是互补的，而不是竞争的。 `AGENTS.md` 塑造行为、记忆 
 推进本地环境、技能包可重复流程，并且 
[MCP](<https://learn.chatgpt.com/docs/extend/mcp>) 将 Codex 连接到本地工作空间之外的系统。

## AGENTS 指导

`AGENTS.md` 提供 Codex 持久的项目指导，该指导与您的存储库一起移动并在 agent 开始工作之前应用。保持较小。

将其用于您希望 Codex 每次在存储库中遵循的规则，例如：

- 构建和测试命令
- 回顾期望
- 特定于回购协议的约定
- 特定于目录的说明

当 agent 对您的代码库做出错误假设时，请在 `AGENTS.md` 中更正它们，并要求 agent 更新 `AGENTS.md`，以便修复持续存在。将其视为反馈循环。

**更新`AGENTS.md`：**仅从重要的指令开始。整理定期审核反馈，将指南放在最适用的目录中，并告诉 agent 在您更正某些内容时更新 `AGENTS.md`，以便将来的会话继承修复。

### 何时更新`AGENTS.md`

- **重复错误**：如果agent重复犯同样的错误，则添加规则。
- **阅读过多**：如果它找到了正确的文件但读取了太多文档，请添加路由指南（优先考虑directories/files）。
- **重复PR反馈**：如果您多次留下相同的反馈，请将其编码。
- **在 GitHub** 中：在拉取请求注释中，使用请求（例如 `@codex add this to AGENTS.md`）标记 `@codex`，以将更新委托给云聊天。
- **自动漂移检查**：使用[计划任务](<https://learn.chatgpt.com/docs/automations>)运行定期检查（例如每天），以查找指导差距并建议向`AGENTS.md`添加哪些内容。

将 `AGENTS.md` 与执行这些规则的基础设施配对：预提交挂钩、linter 和类型检查器在您发现问题之前捕获问题，因此系统可以更智能地防止重复出现错误。

Codex 可以从多个位置加载指导：Codex 主目录中的全局文件（供开发人员使用）以及团队可以签入的存储库特定文件。靠近工作目录的文件优先。
使用全局文件来决定 Codex 与您的沟通方式（例如，审阅风格、详细程度和内容）默认值），并使存储库文件重点关注团队和代码库规则。

<FileTree
  class="mt-4"
  tree={[
    {
      name: "~/.codex/",
      open: true,
      children: [
        { name: "AGENTS.md", comment: "Global (for you as a developer)" },
      ],
    },
    {
      name: "repo-root/",
      open: true,
      children: [
        { name: "AGENTS.md", comment: "repo-specific (for your team)" },
      ],
    },
  ]}
/>

[自定义指令AGENTS.md](<https://learn.chatgpt.com/docs/agent-configuration/agents-md>)

## 技能

技能为 Codex 可重复使用的工作流程提供了可重用的功能。
技能通常最适合可重用的工作流程，因为它们支持更丰富的指令、脚本和参考，同时保持跨任务的可重用性。
技能已加载并对 agent（至少是其元数据）可见，因此Codex可以隐式发现并选择它们。这使得丰富的工作流程可用，而无需预先增加上下文。

使用技能文件夹在本地创作和迭代工作流程。如果工作流程已存在插件 
，请先安装它以重复使用经过验证的设置。当
您想要跨团队分发自己的工作流程或将其与
连接器捆绑时，请将其打包为[插件](<https://learn.chatgpt.com/docs/build-plugins>)。技能仍采用
创作格式；插件是可安装的分发单元。

技能通常是 `SKILL.md` 文件加上可选脚本、参考和资产。

<FileTree
  class="mt-4"
  tree={[
    {
      name: "my-skill/",
      open: true,
      children: [
        { name: "SKILL.md", comment: "Required: instructions + metadata" },
        { name: "scripts/", comment: "Optional: executable code" },
        { name: "references/", comment: "Optional: documentation" },
        { name: "assets/", comment: "Optional: templates, resources" },
      ],
    },
  ]}
/>

技能目录可以包含 `scripts/` 文件夹，其中包含 CLI 脚本，Codex 作为工作流程的一部分调用（例如，种子数据或运行验证）。当工作流程需要外部系统（问题跟踪器、设计工具、文档服务器）时，将该技能与 [MCP](<https://learn.chatgpt.com/docs/extend/mcp>) 配对。

示例`SKILL.md`：

```md
---
name: commit
description: Stage and commit changes in semantic groups. Use when the user wants to commit, organize commits, or clean up a branch before pushing.
---

1. Do not run `git add .`. Stage files in logical groups by purpose.
2. Group into separate commits: feat → test → docs → refactor → chore.
3. Write concise commit messages that match the change scope.
4. Keep each commit focused and reviewable.
```

使用技能：

- 可重复的工作流程（发布步骤、审核例程、文档更新）
- 团队特定的专业知识
- 需要示例、参考或帮助脚本的过程

技能可以是全局的（对于作为开发人员的您来说，在您的用户目录中）或特定于存储库的技能（对于您的团队来说，签入 `.agents/skills`）。当工作流程适用于该项目时，将存储库技能放入 `.agents/skills` 中；使用您的用户目录来获取所有存储库中您想要的技能。

|图层|全球|回购协议|
| :----- | :------------------- | :--------------------------------------------- |
| AGENTS | `~/.codex/AGENTS.md` |`AGENTS.md` 在存储库根目录或嵌套目录中|
|技能| `~/.agents/skills`   |仓库中的 `.agents/skills`|

Codex 使用渐进式披露技能：

- 它以用于发现的元数据（`name`、`description`）开始
- 仅当选择技能时才加载`SKILL.md`
- 仅在需要时读取引用或运行脚本

技能可以显式调用，Codex也可以在任务与技能描述匹配时隐式选择它们。清晰的技能描述提高了触发的可靠性。

[构建技能](<https://learn.chatgpt.com/docs/build-skills>)

## MCP

MCP (Model Context Protocol) 是将 Codex 连接到外部工具和上下文提供程序的标准方法。
它对于远程托管系统（例如Figma、Linear、GitHub）或您的团队依赖的内部知识服务特别有用。

当 Codex 需要本地存储库之外的功能（例如问题跟踪器、设计工具、浏览器或共享文档系统）时，请使用 MCP。

一种思考方式：

- **主机**：Codex
- **客户端**：Codex内的MCP连接
- **服务器**：外部工具或上下文提供者

MCP 服务器可以公开：

- **工具**（操作）
- **资源**（可读数据）
- **提示**（可重复使用的提示模板）

这种分离可以帮助您推理信任和能力边界。一些服务器主要提供上下文，而另一些服务器则提供强大的操作。

在实践中，MCP 在与技能配对时通常最有用：

- 技能定义工作流程并命名要使用的 MCP 工具

[Model Context Protocol](<https://learn.chatgpt.com/docs/extend/mcp>)

## 子代理

您可以为不同的角色创建不同的agents，并提示他们以不同的方式使用工具。例如，一个 agent 可能运行特定的测试命令和配置，而另一个具有 MCP 服务器来获取生产日志以进行调试。每个子代理都保持专注并使用正确的工具来完成其工作。

[子代理](<https://learn.chatgpt.com/docs/agent-configuration/subagents>)

## 技能+MCP一起

技能加 MCP 是这一切的结合点：技能定义可重复的工作流程，而 MCP 将它们连接到外部工具和系统。
如果技能依赖于 MCP，请在 `agents/openai.yaml` 中声明该依赖关系，以便 Codex 可以自动安装和连接它（请参阅[培养技能](<https://learn.chatgpt.com/docs/build-skills>)）。

## 下一步

按此顺序构建：

1. [带有 AGENTS.md](<https://learn.chatgpt.com/docs/agent-configuration/agents-md>) 的自定义指令，因此 Codex 遵循您的存储库约定。添加预提交挂钩和 l​​inter 来强制执行这些规则。
2. 当可重用工作流程已存在时，安装 [ 插件 ](<https://learn.chatgpt.com/docs/plugins>)。否则，当你想分享时，创建一个[技能](<https://learn.chatgpt.com/docs/build-skills>)并将其打包为插件。
3. [MCP](<https://learn.chatgpt.com/docs/extend/mcp>) 当工作流程需要外部系统时（Linear、GitHub、文档服务器、设计工具）。
4. [子代理](<https://learn.chatgpt.com/docs/agent-configuration/subagents>) 当您准备好将嘈杂或专门的任务委派给子代理时。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Customization

> For the complete documentation index, see [llms.txt](<https://learn.chatgpt.com/llms.txt>). Markdown versions of documentation pages are available by appending `.md` to the page URL.

Customization is how you make Codex work the way your team works.

In Codex, customization comes from a few layers that work together:

- **Project guidance (`AGENTS.md`)** for persistent instructions
- **[Memories](<https://learn.chatgpt.com/docs/customization/memories>)** for useful context learned from prior work
- **Skills** for reusable workflows and domain expertise
- **[MCP](<https://learn.chatgpt.com/docs/extend/mcp>)** for access to external tools and shared systems
- **[Subagents](<https://learn.chatgpt.com/docs/agent-configuration/subagents>)** for delegating work to specialized subagents

These are complementary, not competing. `AGENTS.md` shapes behavior, memories
carry local context forward, skills package repeatable processes, and
[MCP](<https://learn.chatgpt.com/docs/extend/mcp>) connects Codex to systems outside the local workspace.

## AGENTS Guidance

`AGENTS.md` gives Codex durable project guidance that travels with your repository and applies before the agent starts work. Keep it small.

Use it for the rules you want Codex to follow every time in a repo, such as:

- Build and test commands
- Review expectations
- repo-specific conventions
- Directory-specific instructions

When the agent makes incorrect assumptions about your codebase, correct them in `AGENTS.md` and ask the agent to update `AGENTS.md` so the fix persists. Treat it as a feedback loop.

**Updating `AGENTS.md`:** Start with only the instructions that matter. Codify recurring review feedback, put guidance in the closest directory where it applies, and tell the agent to update `AGENTS.md` when you correct something so future sessions inherit the fix.

### When to update `AGENTS.md`

- **Repeated mistakes**: If the agent makes the same mistake repeatedly, add a rule.
- **Too much reading**: If it finds the right files but reads too many documents, add routing guidance (which directories/files to prioritize).
- **Recurring PR feedback**: If you leave the same feedback more than once, codify it.
- **In GitHub**: In a pull request comment, tag `@codex` with a request (for example, `@codex add this to AGENTS.md`) to delegate the update to a cloud chat.
- **Automate drift checks**: Use [scheduled tasks](<https://learn.chatgpt.com/docs/automations>) to run recurring checks (for example, daily) that look for guidance gaps and suggest what to add to `AGENTS.md`.

Pair `AGENTS.md` with infrastructure that enforces those rules: pre-commit hooks, linters, and type checkers catch issues before you see them, so the system gets smarter about preventing recurring mistakes.

Codex can load guidance from multiple locations: a global file in your Codex home directory (for you as a developer) and repo-specific files that teams can check in. Files closer to the working directory take precedence.
Use the global file to shape how Codex communicates with you (for example, review style, verbosity, and defaults), and keep repo files focused on team and codebase rules.

<FileTree
  class="mt-4"
  tree={[
    {
      name: "~/.codex/",
      open: true,
      children: [
        { name: "AGENTS.md", comment: "Global (for you as a developer)" },
      ],
    },
    {
      name: "repo-root/",
      open: true,
      children: [
        { name: "AGENTS.md", comment: "repo-specific (for your team)" },
      ],
    },
  ]}
/>

[Custom instructions with AGENTS.md](<https://learn.chatgpt.com/docs/agent-configuration/agents-md>)

## Skills

Skills give Codex reusable capabilities for repeatable workflows.
Skills are often the best fit for reusable workflows because they support richer instructions, scripts, and references while staying reusable across tasks.
Skills are loaded and visible to the agent (at least their metadata), so Codex can discover and choose them implicitly. This keeps rich workflows available without bloating context up front.

Use skill folders to author and iterate on workflows locally. If a plugin
already exists for the workflow, install it first to reuse a proven setup. When
you want to distribute your own workflow across teams or bundle it with
connectors, package it as a [plugin](<https://learn.chatgpt.com/docs/build-plugins>). Skills remain the
authoring format; plugins are the installable distribution unit.

A skill is typically a `SKILL.md` file plus optional scripts, references, and assets.

<FileTree
  class="mt-4"
  tree={[
    {
      name: "my-skill/",
      open: true,
      children: [
        { name: "SKILL.md", comment: "Required: instructions + metadata" },
        { name: "scripts/", comment: "Optional: executable code" },
        { name: "references/", comment: "Optional: documentation" },
        { name: "assets/", comment: "Optional: templates, resources" },
      ],
    },
  ]}
/>

The skill directory can include a `scripts/` folder with CLI scripts that Codex invokes as part of the workflow (for example, seed data or run validations). When the workflow needs external systems (issue trackers, design tools, docs servers), pair the skill with [MCP](<https://learn.chatgpt.com/docs/extend/mcp>).

Example `SKILL.md`:

```md
---
name: commit
description: Stage and commit changes in semantic groups. Use when the user wants to commit, organize commits, or clean up a branch before pushing.
---

1. Do not run `git add .`. Stage files in logical groups by purpose.
2. Group into separate commits: feat → test → docs → refactor → chore.
3. Write concise commit messages that match the change scope.
4. Keep each commit focused and reviewable.
```

Use skills for:

- Repeatable workflows (release steps, review routines, docs updates)
- Team-specific expertise
- Procedures that need examples, references, or helper scripts

Skills can be global (in your user directory, for you as a developer) or repo-specific (checked into `.agents/skills`, for your team). Put repo skills in `.agents/skills` when the workflow applies to that project; use your user directory for skills you want across all repos.

| Layer  | Global               | repo                                           |
| :----- | :------------------- | :--------------------------------------------- |
| AGENTS | `~/.codex/AGENTS.md` | `AGENTS.md` in repo root or nested directories |
| Skills | `~/.agents/skills`   | `.agents/skills` in repo                       |

Codex uses progressive disclosure for skills:

- It starts with metadata (`name`, `description`) for discovery
- It loads `SKILL.md` only when a skill is chosen
- It reads references or runs scripts only when needed

Skills can be invoked explicitly, and Codex can also choose them implicitly when the task matches the skill description. Clear skill descriptions improve triggering reliability.

[Build skills](<https://learn.chatgpt.com/docs/build-skills>)

## MCP

MCP (Model Context Protocol) is the standard way to connect Codex to external tools and context providers.
It's especially useful for remotely hosted systems such as Figma, Linear, GitHub, or internal knowledge services your team depends on.

Use MCP when Codex needs capabilities that live outside the local repo, such as issue trackers, design tools, browsers, or shared documentation systems.

One way to think about it:

- **Host**: Codex
- **Client**: the MCP connection inside Codex
- **Server**: the external tool or context provider

MCP servers can expose:

- **Tools** (actions)
- **Resources** (readable data)
- **Prompts** (reusable prompt templates)

This separation helps you reason about trust and capability boundaries. Some servers mainly provide context, while others expose powerful actions.

In practice, MCP is often most useful when paired with skills:

- A skill defines the workflow and names the MCP tools to use

[Model Context Protocol](<https://learn.chatgpt.com/docs/extend/mcp>)

## Subagents

You can create different agents with different roles and prompt them to use tools differently. For example, one agent might run specific testing commands and configurations, while another has MCP servers that fetch production logs for debugging. Each subagent stays focused and uses the right tools for its job.

[Subagents](<https://learn.chatgpt.com/docs/agent-configuration/subagents>)

## Skills + MCP together

Skills plus MCP is where it all comes together: skills define repeatable workflows, and MCP connects them to external tools and systems.
If a skill depends on MCP, declare that dependency in `agents/openai.yaml` so Codex can install and wire it automatically (see [Build skills](<https://learn.chatgpt.com/docs/build-skills>)).

## Next step

Build in this order:

1. [Custom instructions with AGENTS.md](<https://learn.chatgpt.com/docs/agent-configuration/agents-md>) so Codex follows your repo conventions. Add pre-commit hooks and linters to enforce those rules.
2. Install a [plugin](<https://learn.chatgpt.com/docs/plugins>) when a reusable workflow already exists. Otherwise, create a [skill](<https://learn.chatgpt.com/docs/build-skills>) and package it as a plugin when you want to share it.
3. [MCP](<https://learn.chatgpt.com/docs/extend/mcp>) when workflows need external systems (Linear, GitHub, docs servers, design tools).
4. [Subagents](<https://learn.chatgpt.com/docs/agent-configuration/subagents>) when you're ready to delegate noisy or specialized tasks to subagents.


</details>
