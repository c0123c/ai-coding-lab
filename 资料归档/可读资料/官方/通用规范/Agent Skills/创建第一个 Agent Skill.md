# 创建第一个 Agent Skill

在 VS Code 中通过随机掷骰子技能演示目录创建、SKILL.md 和实际调用，帮助理解基本加载机制。

- 来源：通用规范／Agent Skills
- 资料类型：存档文档
- 文档时间：官网现页更新：[2026-04-22](<https://agentskills.io/skill-creation/quickstart>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://agentskills.io/skill-creation/quickstart.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/通用规范/Agent Skills/创建第一个 Agent Skill.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

> ## 文档索引
> 在以下位置获取完整的文档索引： https://agentskills.io/llms.txt
> 在进一步探索之前使用此文件来发现所有可用页面。

# 快速入门

> 创建你的第一个 Agent 技能并查看它在 VS Code 中的工作情况。

在本教程中，您将创建一项技能，使 agent 能够使用随机数生成器掷骰子。

## 先决条件

* [VS Code](<https://code.visualstudio.com/>) 与 [GitHub Copilot](<https://marketplace.visualstudio.com/items?itemName=GitHub.copilot>)



**注释：**

本教程使用VS Code，但Agent Skills是开放格式。相同的技能适用于任何兼容的 agent，包括 Claude Code 和 OpenAI Codex。


## 创造技能

技能是包含 `SKILL.md` 文件的文件夹。 VS Code默认在`.agents/skills/`中查找技能。在您的项目中创建`.agents/skills/roll-dice/SKILL.md`：

````markdown .agents/skills/roll-dice/SKILL.md theme={null}
---
name: roll-dice
description: Roll dice using a random number generator. Use when asked to roll a die (d6, d20, etc.), roll dice, or generate a random dice roll.
---

To roll a die, use the following command that generates a random number from 1
to the given number of sides:

```bash
echo $((RANDOM % <sides> + 1))
```

```powershell
Get-Random -Minimum 1 -Maximum (<sides> + 1)
```

Replace `<sides>` with the number of sides on the die (e.g., 6 for a standard
die, 20 for a d20).
````

就是这样——一个文件，位于 20 行下。以下是每个部分的作用：

* **`name`** — 技能的简短标识符。必须与文件夹名称匹配。
* **`description`** — 告诉agent何时使用该技能。这就是agent决定是否激活它的方式。
* **身体** - 技能激活时agent跟随的指令。这里，agent被指示使用终端命令生成随机数，替换用户请求的边数。

## 尝试一下

1. 在 VS Code 中打开您的项目。
2. 打开Copilot聊天面板。
3. 从聊天面板底部的模式下拉列表中选择**Agent**模式。
4. 输入 `/skills` 以确认 `roll-dice` 出现在列表中。如果没有，请检查该文件是否位于相对于项目根目录的 `.agents/skills/roll-dice/SKILL.md` 处。
5. 询问：**“掷 d20”**

agent应该激活`roll-dice`技能。它可能会请求运行终端命令的权限 - 允许它。它将运行该命令并返回 1 和 20 之间的随机数。



**注释：**

不同模型的工具使用可靠性各不相同——有些模型遵循技能说明并一致地运行命令，而另一些模型可能会尝试自己回答。如果 agent 在未运行终端命令的情况下响应，请尝试从型号下拉列表中选择其他型号。


## 它是如何运作的

以下是幕后发生的事情：

1. **发现** - 当聊天会话开始时，agent 扫描默认技能目录并找到您的技能。它只读取 `name` 和 `description`，足以知道该技能何时可能相关。

2. **激活** - 当您询问有关掷骰子的问题时，agent 将您的问题与技能描述相匹配，并将完整的 `SKILL.md` 正文加载到上下文中。

3. **执行** — agent 按照正文中的说明进行操作，使终端命令适应您请求中的边数。

此过程使用 ** 渐进式披露 ** 来让 agent 访问许多技能，而无需预先加载所有指令。

## 后续步骤

您已经创建了一项有效的 Agent 技能。从这里开始：

* **[最佳实践](<https://agentskills.io/skill-creation/best-practices>)** - 如何编写范围广泛且有效的技能。
* **[优化技能描述](<https://agentskills.io/skill-creation/optimizing-descriptions>)** - 测试并改进你的技能描述，使其在正确的提示下激活。
* **[规范](<https://agentskills.io/specification>)** — `SKILL.md` 文件的完整格式参考。
* **[示例技能](<https://github.com/anthropics/skills>)** — 在GitHub上浏览现实世界的技能。


## 原文对照

<details>
<summary>展开完整原文</summary>

> ## Documentation Index
> Fetch the complete documentation index at: https://agentskills.io/llms.txt
> Use this file to discover all available pages before exploring further.

# Quickstart

> Create your first Agent Skill and see it work in VS Code.

In this tutorial, you'll create a skill that gives an agent the capability to roll dice using a random number generator.

## Prerequisites

* [VS Code](<https://code.visualstudio.com/>) with [GitHub Copilot](<https://marketplace.visualstudio.com/items?itemName=GitHub.copilot>)



**注释：**

  This tutorial uses VS Code, but Agent Skills are an open format. The same skill works in any compatible agent, including Claude Code and OpenAI Codex.


## Create the skill

A skill is a folder containing a `SKILL.md` file. VS Code looks for skills in `.agents/skills/` by default. Create `.agents/skills/roll-dice/SKILL.md` in your project:

````markdown .agents/skills/roll-dice/SKILL.md theme={null}
---
name: roll-dice
description: Roll dice using a random number generator. Use when asked to roll a die (d6, d20, etc.), roll dice, or generate a random dice roll.
---

To roll a die, use the following command that generates a random number from 1
to the given number of sides:

```bash
echo $((RANDOM % <sides> + 1))
```

```powershell
Get-Random -Minimum 1 -Maximum (<sides> + 1)
```

Replace `<sides>` with the number of sides on the die (e.g., 6 for a standard
die, 20 for a d20).
````

That's it — one file, under 20 lines. Here's what each part does:

* **`name`** — A short identifier for the skill. Must match the folder name.
* **`description`** — Tells the agent when to use this skill. This is how the agent decides whether to activate it.
* **The body** — Instructions the agent follows when the skill activates. Here, the agent is instructed to generate a random number using a terminal command, substituting the number of sides from the user's request.

## Try it out

1. Open your project in VS Code.
2. Open the Copilot Chat panel.
3. Select **Agent** mode from the mode dropdown at the bottom of the chat panel.
4. Type `/skills` to confirm that `roll-dice` appears in the list. If it doesn't, check that the file is at `.agents/skills/roll-dice/SKILL.md` relative to your project root.
5. Ask: **"Roll a d20"**

The agent should activate the `roll-dice` skill. It may ask for permission to run a terminal command — allow it. It will run the command and return a random number between 1 and 20.



**注释：**

  Tool-use reliability varies across models — some follow skill instructions and run commands consistently, while others may attempt to answer on their own. If the agent responds without running a terminal command, try selecting a different model from the model dropdown.


## How it works

Here's what happened behind the scenes:

1. **Discovery** — When the chat session started, the agent scanned default skill directories and found your skill. It read only the `name` and `description`, just enough to know when the skill might be relevant.

2. **Activation** — When you asked about rolling dice, the agent matched your question to the skill's description and loaded the full `SKILL.md` body into context.

3. **Execution** — The agent followed the instructions in the body, adapting the terminal command to the number of sides in your request.

This process uses **progressive disclosure** to let the agent access many skills without loading all their instructions up front.

## Next steps

You've created a working Agent Skill. From here:

* **[Best practices](<https://agentskills.io/skill-creation/best-practices>)** — How to write skills that are well-scoped and effective.
* **[Optimizing skill descriptions](<https://agentskills.io/skill-creation/optimizing-descriptions>)** — Test and improve your skill's description so it activates on the right prompts.
* **[Specification](<https://agentskills.io/specification>)** — The complete format reference for `SKILL.md` files.
* **[Example skills](<https://github.com/anthropics/skills>)** — Browse real-world skills on GitHub.


</details>
