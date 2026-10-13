# Agent Skills 概览

介绍技能由指令、脚本和资源组成，可按任务加载；说明项目与个人技能的位置、支持的 Copilot 界面，以及创建、安装与复用技能的路径。

- 来源：GitHub／Copilot
- 资料类型：存档文档
- 文档时间：官方文件提交（截至抓取）：[2026-07-09](<https://github.com/github/docs/commit/9f5e5c809fcc60731697e1ddebd3b1bc952284d9>)；发表/更新：官方未公开；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://docs.github.com/en/copilot/concepts/agents/about-agent-skills>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/Agent Skills 概览（存档2026-10-11-80）.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 关于agent skills

技能允许Copilot执行专门的任务。

> \[！NOTE]
> Agent skills 与 Copilot 云 agent、Copilot 代码审查、GitHub Copilot CLI、GitHub Copilot 应用程序合作，以及Visual Studio Code 和 JetBrains IDE 中的 agent 模式。

## 关于agent skills

Agent skills 是 Copilot 可以在相关时加载的指令、脚本和资源的文件夹，以提高其在专门任务中的性能。 Agent Skills 规范是一个 [ 开放标准 ](<https://github.com/agentskills/agentskills>)，由一系列不同的 AI 系统使用。

您可以创建自己的技能来教 Copilot 以特定的、可重复的方式执行任务，或者使用在线共享的技能，例如 [`anthropics/skills`](<https://github.com/anthropics/skills>) 存储库或 GitHub 社区创建的 [`github/awesome-copilot`](<https://github.com/github/awesome-copilot>) 集合中的技能。

您还可以使用 GitHub CLI 中的 `gh skill` 从 GitHub 存储库中发现并安装技能。有关更多信息，请参阅[为GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/add-skills#managing-skills-with-github-cli>)添加agent skills。

Copilot 支持：

* 项目技能，存储在您的存储库中（`.github/skills`、`.claude/skills` 或 `.agents/skills`）
* 个人技能，存储在您的主目录中并在项目之间共享（`~/.copilot/skills` 或 `~/.agents/skills`）

## 后续步骤

要创建或添加 agent skills，请参阅：

* [为GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/add-skills>)添加agent skills
* [为GitHub CopilotCLI](<https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/add-skills>)添加agent skills
* [自定义GitHub Copilot应用程序](<https://docs.github.com/en/copilot/how-tos/github-copilot-app/customize-github-copilot-app>)
* [Copilot 定制备忘单](<https://docs.github.com/en/copilot/reference/customization-cheat-sheet>)


## 原文对照

<details>
<summary>展开完整原文</summary>

# About agent skills

Skills allow Copilot to perform specialized tasks.

> \[!NOTE]
> Agent skills work with Copilot cloud agent, Copilot code review, the GitHub Copilot CLI, the GitHub Copilot app, and agent mode in Visual Studio Code and JetBrains IDEs.

## About agent skills

Agent skills are folders of instructions, scripts, and resources that Copilot can load when relevant to improve its performance in specialized tasks. The Agent Skills specification is an [open standard](<https://github.com/agentskills/agentskills>), used by a range of different AI systems.

You can create your own skills to teach Copilot to perform tasks in a specific, repeatable way—or use skills shared online, for example in the [`anthropics/skills`](<https://github.com/anthropics/skills>) repository or GitHub's community-created [`github/awesome-copilot`](<https://github.com/github/awesome-copilot>) collection.

You can also use `gh skill` in GitHub CLI to discover and install skills from GitHub repositories. For more information, see [Adding agent skills for GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/add-skills#managing-skills-with-github-cli>).

Copilot supports:

* Project skills, stored in your repository (`.github/skills`, `.claude/skills`, or `.agents/skills`)
* Personal skills, stored in your home directory and shared across projects (`~/.copilot/skills` or `~/.agents/skills`)

## Next steps

To create or add agent skills, see:

* [Adding agent skills for GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/add-skills>)
* [Adding agent skills for GitHub Copilot CLI](<https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/add-skills>)
* [Customizing the GitHub Copilot app](<https://docs.github.com/en/copilot/how-tos/github-copilot-app/customize-github-copilot-app>)
* [Copilot customization cheat sheet](<https://docs.github.com/en/copilot/reference/customization-cheat-sheet>)


</details>
