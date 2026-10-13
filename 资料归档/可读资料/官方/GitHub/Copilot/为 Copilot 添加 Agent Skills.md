# 为 Copilot 添加 Agent Skills

介绍编写 SKILL.md、组织附属资源、执行脚本和安装共享技能的方法，说明项目与个人位置以及使用 GitHub CLI 管理技能的流程。

- 来源：GitHub／Copilot
- 资料类型：存档文档
- 文档时间：官方文件提交（截至抓取）：[2026-06-08](<https://github.com/github/docs/commit/8af2b1df30eca5809ff2700a880f4c7291b511c6>)；发表/更新：官方未公开；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/add-skills>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/为 Copilot 添加 Agent Skills.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 为 GitHub Copilot 添加 agent skills

当Copilot执行特定任务时，你可以修改它的行为和能力。

> \[！NOTE]
> Agent skills 与 Copilot 云 agent、Copilot 代码审查、GitHub Copilot CLI、GitHub Copilot 应用程序合作，以及Visual Studio Code 中的 agent 模式。

Agent skills 是 Copilot 可以在相关时加载的指令、脚本和资源的文件夹，以提高其在专门任务中的性能。欲了解更多信息，请参阅[关于agent skills](<https://docs.github.com/en/copilot/concepts/agents/about-agent-skills>)。

## 创建和添加技能

要创建 agent 技能，您需要编写一个 `SKILL.md` 文件以及您在 `SKILL.md` 说明中引用的其他资源（可选），例如补充 Markdown 文件或脚本。

1. 如果您尚未这样做，请在以下位置之一创建 `skills` 目录。您可以在这里找到您的技能以及您将来可能想要创建的任何其他技能。

对于特定于单个存储库的 ** 项目技能 **，请在存储库中创建 `.github/skills`、`.claude/skills` 或 `.agents/skills` 目录。

对于跨项目共享的 ** 个人技能 **，请在本地主目录中创建 `~/.copilot/skills` 或 `~/.agents/skills` 目录。

2. 在 `skills` 目录中，为您的新技能创建一个子目录。每个技能都应该有自己的目录（例如，`.github/skills/webapp-testing`）。

技能子目录名称应小写，并使用连字符作为空格。

3. 在您的技能子目录中，创建一个包含技能说明的 `SKILL.md` 文件。

   > \[！IMPORTANT]
   > 技能文件必须命名为`SKILL.md`。

`SKILL.md` 文件是带有 YAML frontmatter 的 Markdown 文件。最简单的形式包括：

   * YAML 前题
     * **名称**（必填）：技能的唯一标识符。必须是小写，使用连字符作为空格。通常，这与技能目录的名称匹配。
     * **描述**（必需）：该技能的用途以及何时Copilot应该使用该技能的描述。
     * **许可证**（可选）：适用于该技能的许可证的说明。
   * Markdown 正文，包含 Copilot 需要遵循的说明、示例和指南。

4. （可选）将脚本、示例或其他资源添加到您的技能目录中。

有关详细信息，请参阅“[启用技能来运行脚本](<#启用运行脚本的技能>)”。

### 示例 `SKILL.md` 文件

对于 ** 项目技能 **，此文件将位于存储库的 `.github/skills/github-actions-failure-debugging` 目录中。

对于 ** 个人技能 **，此文件将位于 `~/.copilot/skills/github-actions-failure-debugging` 目录中。

```markdown copy
---
name: github-actions-failure-debugging
description: Guide for debugging failing GitHub Actions workflows. Use this when asked to debug failing GitHub Actions workflows.
---

To debug failing GitHub Actions workflows in a pull request, follow this process, using tools provided from the GitHub MCP Server:

1. Use the `list_workflow_runs` tool to look up recent workflow runs for the pull request and their status
2. Use the `summarize_job_log_failures` tool to get an AI summary of the logs for failed jobs, to understand what went wrong without filling your context windows with thousands of lines of logs
3. If you still need more information, use the `get_job_logs` or `get_workflow_run_logs` tool to get the full, detailed failure logs
4. Try to reproduce the failure yourself in your own environment.
5. Fix the failing build. If you were able to reproduce the failure yourself, make sure it is fixed before committing your changes.
```

### 启用运行脚本的技能

调用技能时，Copilot 会自动发现技能目录中的所有文件，并使它们与技能指令一起可用。这意味着您可以在技能目录中包含脚本或其他资源，并在 `SKILL.md` 说明中引用它们。

要创建运行脚本的技能：

1. **将脚本添加到您的技能目录。** 例如，将 SVG 图像转换为 PNG 的技能可能具有以下结构。

   ```text
   .github/skills/image-convert/
   ├── SKILL.md
   └── convert-svg-to-png.sh
   ```

2. **可以选择预先批准技能所需的工具。** 在您的 `SKILL.md` frontmatter 中，您可以使用 `allowed-tools` 字段列出 Copilot 可以使用的工具，而无需每次都要求确认。如果某个工具未在 `allowed-tools` 字段中列出，Copilot 将在使用它之前提示您获得许可。

   ```markdown
   ---
   name: image-convert
   description: Converts SVG images to PNG format. Use when asked to convert SVG files.
   allowed-tools: shell
   ---
   ```

   > \[！WARNING]
   > 仅在您已查看此技能和任何引用的脚本并且完全信任其来源的情况下才预先批准 `shell` 或 `bash` 工具。预先批准 `shell` 或 `bash` 会删除运行终端命令的确认步骤，并可能允许攻击者控制的技能或提示注入在您的环境中执行任意命令。如有疑问，请从 `allowed-tools` 中省略 `shell` 和 `bash`，以便 Copilot 在运行终端命令之前必须要求您明确确认。

3. **编写说明，告诉Copilot如何使用脚本。**在`SKILL.md`的Markdown正文中，描述何时以及如何运行脚本。

   ```markdown
   When asked to convert an SVG to PNG, run the `convert-svg-to-png.sh` script
   from this skill's base directory, passing the input SVG file path as the
   first argument.
   ```

## 添加其他人创建的技能

除了创建自己的技能之外，您还可以添加其他人创建的技能。

> \[！TIP]
> 您还可以使用GitHubCLI中的`gh skill`来搜索、安装、更新和发布agent skills。有关更多信息，请参阅[为GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/add-skills#managing-skills-with-github-cli>)添加agent skills。

1. 下载技能目录（即包含 SKILL.md 文件以及其他文件和子目录（可选）的目录）。

例如，从 Awesome GitHub Copilot 存储库下载技能：<https://awesome-copilot.github.com/skills/>。

2. 如果您下载了 `.zip` 文件，请将其解压缩。

3. 将技能目录移动到所需位置：

   * 对于 ** 项目技能 **，特定于存储库中的单个存储库：`.github/skills`、`.claude/skills` 或 `.agents/skills`。

   * 对于**个人技能**，跨项目共享：本地主目录中的`~/.copilot/skills`或`~/.agents/skills`。

## 使用 GitHub CLI 管理技能

> \[！NOTE]
> `gh skill` 处于公共预览阶段，可能会发生变化。要使用它，请将 GitHub CLI 更新到版本 2.90.0 或更高版本。

您可以使用 GitHub CLI 中的 `gh skill` 命令从 GitHub 存储库发现、安装、更新和发布 agent skills。

有关 `gh skill` 子命令的完整列表，请运行 `gh skill --help` 或参阅 GitHub CLI 手册的 [`gh skill`](<https://cli.github.com/manual/gh_skill>) 部分。

### 安装技巧

您可以搜索技能、预览它们并从 GitHub 存储库安装它们。

> \[！WARNING]
> 技能未经GitHub验证，可能包含提示注入、隐藏指令或恶意脚本。安装前请务必使用 `gh skill preview` 检查技能内容。

1. 按主题搜索技能：

   ```shell
   gh skill search TOPIC
   ```

2. 在安装之前预览技能以检查其内容。这将在您的终端中呈现技能的 `SKILL.md` 和文件树，而无需安装任何内容：

   ```shell
   gh skill preview OWNER/REPOSITORY SKILL
   ```

3. 安装技能。您可以运行不带参数的 `gh skill install` 以实现完全交互式流程，或指定存储库以交互方式浏览其技能：

   ```shell
   gh skill install OWNER/REPOSITORY
   ```

直接安装特定技能：

   ```shell
   gh skill install OWNER/REPOSITORY SKILL
   ```

例如，要从 [`github/awesome-copilot`](<https://github.com/github/awesome-copilot>) 存储库安装技能：

   ```shell copy
   gh skill install github/awesome-copilot documentation-writer
   ```

您可以使用 `@TAG` 或 `@SHA` 安装特定版本：

   ```shell copy
   gh skill install github/awesome-copilot documentation-writer@v1.2.0
   ```

要将技能锁定到特定版本（或提交 SHA）以便在更新期间跳过它，请使用 `--pin`：

   ```shell copy
   gh skill install github/awesome-copilot documentation-writer --pin v1.2.0
   ```

   > \[!NOTE]
   > `@VERSION`语法和`--pin`标志是互斥的。使用其中之一，而不是同时使用两者。

要为特定的 agent 主机安装技能，请使用 `--agent` 标志。要控制安装范围，请使用`--scope`：

   ```shell copy
   gh skill install github/awesome-copilot documentation-writer --agent claude-code --scope user
   ```

技能会自动安装到您的 agent 主机的正确目录中。默认情况下，在项目范围内为 Copilot 安装技能。

### 更新技能

当您使用 `gh skill` 安装技能时，出处元数据将写入技能的 `SKILL.md` frontmatter 中，包括源存储库、引用和树 SHA。 `gh skill update` 命令使用此元数据来检查上游更改。

要以交互方式检查更新：

```shell
gh skill update
```

要更新特定技能：

```shell
gh skill update SKILL
```

要更新所有已安装的技能而不提示：

```shell
gh skill update --all
```

更新期间会跳过固定技能。要更新固定技能，请使用新的 `--pin` 值重新安装。

### 出版技巧

如果您维护技能存储库，则可以使用 GitHub CLI 验证和发布您的技能。

要根据 [Agent Skills 规范 ](<https://agentskills.io/specification>) 验证您的技能并检查标签保护、秘密扫描和代码扫描等远程设置，而无需发布，请使用 `--dry-run`：

```shell
gh skill publish --dry-run
```

要自动修复技能文件中的元数据问题，请使用 `--fix`。这不会公布您的技能：

```shell
gh skill publish --fix
```

要验证并发布您的技能：

```shell
gh skill publish
```

## Copilot如何使用agent skills

执行任务时，Copilot会根据你的提示和技能描述来决定何时使用你的技能。

当Copilot选择使用技能时，`SKILL.md`文件将被注入到agent的上下文中，从而使agent能够访问您的指令。然后，它可以按照这些说明进行操作，并使用您可能已包含在技能目录中的任何脚本或示例。

对于 GitHub 上的 Copilot 代码审查，请记住以下几点：

* 如果要确保 Copilot 代码审查能够读取并使用技能，请使用以审查为中心的技能目录名称，例如 `code-review`。
* 当 `.github/skills` 目录中的现有技能与审核相关时，Copilot 代码审核也可以自动使用它们。

## 技能与自定义指令

您可以使用技能和自定义指令来教授 Copilot 如何在存储库中工作以及如何执行特定任务。

我们建议使用 ** 自定义指令 ** 来获取与几乎每个任务相关的简单指令（例如有关存储库编码标准的信息），并使用 ** 技能 ** 来获取 Copilot 仅在相关时才应访问的更详细指令。

要了解有关存储库自定义指令的更多信息，请参阅 [为 GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions>) 添加存储库自定义指令。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Adding agent skills for GitHub Copilot

You can modify Copilot's behavior and abilities when it works on particular tasks.

> \[!NOTE]
> Agent skills work with Copilot cloud agent, Copilot code review, the GitHub Copilot CLI, the GitHub Copilot app, and agent mode in Visual Studio Code.

Agent skills are folders of instructions, scripts, and resources that Copilot can load when relevant to improve its performance in specialized tasks. For more information, see [About agent skills](<https://docs.github.com/en/copilot/concepts/agents/about-agent-skills>).

## Creating and adding a skill

To create an agent skill, you write a `SKILL.md` file and, optionally, other resources, such as supplementary Markdown files, or scripts, which you reference in the `SKILL.md` instructions.

1. If you haven't already done so, create a `skills` directory in one of the following locations. This is where you will locate your skill, and any others you may want to create in the future.

   For **project skills**, specific to a single repository, create a `.github/skills`, `.claude/skills`, or `.agents/skills` directory in your repository.

   For **personal skills**, shared across projects, create a `~/.copilot/skills` or `~/.agents/skills` directory in your local home directory.

2. Within the `skills` directory, create a subdirectory for your new skill. Each skill should have its own directory (for example, `.github/skills/webapp-testing`).

   Skill subdirectory names should be lowercase and use hyphens for spaces.

3. In your skill subdirectory, create a `SKILL.md` file containing your skill's instructions.

   > \[!IMPORTANT]
   > Skill files must be named `SKILL.md`.

   `SKILL.md` files are Markdown files with YAML frontmatter. In their simplest form, they include:

   * YAML frontmatter
     * **name** (required): A unique identifier for the skill. This must be lowercase, using hyphens for spaces. Typically, this matches the name of the skill's directory.
     * **description** (required): A description of what the skill does, and when Copilot should use it.
     * **license** (optional): A description of the license that applies to this skill.
   * A Markdown body, with the instructions, examples and guidelines for Copilot to follow.

4. Optionally, add scripts, examples or other resources to your skill's directory.

   For more information, see "[Enabling a skill to run a script](<#enabling-a-skill-to-run-a-script>)."

### Example `SKILL.md` file

For a **project skill**, this file would be located in a `.github/skills/github-actions-failure-debugging` directory of your repository.

For a **personal skill**, this file would be located in a `~/.copilot/skills/github-actions-failure-debugging` directory.

```markdown copy
---
name: github-actions-failure-debugging
description: Guide for debugging failing GitHub Actions workflows. Use this when asked to debug failing GitHub Actions workflows.
---

To debug failing GitHub Actions workflows in a pull request, follow this process, using tools provided from the GitHub MCP Server:

1. Use the `list_workflow_runs` tool to look up recent workflow runs for the pull request and their status
2. Use the `summarize_job_log_failures` tool to get an AI summary of the logs for failed jobs, to understand what went wrong without filling your context windows with thousands of lines of logs
3. If you still need more information, use the `get_job_logs` or `get_workflow_run_logs` tool to get the full, detailed failure logs
4. Try to reproduce the failure yourself in your own environment.
5. Fix the failing build. If you were able to reproduce the failure yourself, make sure it is fixed before committing your changes.
```

### Enabling a skill to run a script

When a skill is invoked, Copilot automatically discovers all of the files in the skill's directory and makes them available alongside the skill's instructions. This means you can include scripts or other resources in the skill directory and reference them in your `SKILL.md` instructions.

To create a skill that runs a script:

1. **Add the script to your skill's directory.** For example, a skill for converting SVG images to PNG might have the following structure.

   ```text
   .github/skills/image-convert/
   ├── SKILL.md
   └── convert-svg-to-png.sh
   ```

2. **Optionally pre-approve the tools the skill needs.** In your `SKILL.md` frontmatter, you can use the `allowed-tools` field to list the tools Copilot may use without asking for confirmation each time. If a tool is not listed in the `allowed-tools` field, Copilot will prompt you for permission before using it.

   ```markdown
   ---
   name: image-convert
   description: Converts SVG images to PNG format. Use when asked to convert SVG files.
   allowed-tools: shell
   ---
   ```

   > \[!WARNING]
   > Only pre-approve the `shell` or `bash` tools if you have reviewed this skill and any referenced scripts, and you fully trust their source. Pre-approving `shell` or `bash` removes the confirmation step for running terminal commands and can allow attacker-controlled skills or prompt injections to execute arbitrary commands in your environment. When in doubt, omit `shell` and `bash` from `allowed-tools` so that Copilot must ask for your explicit confirmation before running terminal commands.

3. **Write instructions that tell Copilot how to use the script.** In the Markdown body of `SKILL.md`, describe when and how to run the script.

   ```markdown
   When asked to convert an SVG to PNG, run the `convert-svg-to-png.sh` script
   from this skill's base directory, passing the input SVG file path as the
   first argument.
   ```

## Adding a skill that someone else has created

In addition to creating your own skills, you can also add skills that other people have created.

> \[!TIP]
> You can also use `gh skill` in GitHub CLI to search for, install, update, and publish agent skills. For more information, see [Adding agent skills for GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/add-skills#managing-skills-with-github-cli>).

1. Download a skill directory (that is, a directory containing a SKILL.md file and, optionally, other files and subdirectories).

   For example, download a skill from the Awesome GitHub Copilot repository: <https://awesome-copilot.github.com/skills/>.

2. If you downloaded a `.zip` file, unzip this.

3. Move the skill directory to the required location:

   * For **project skills**, specific to a single repository: `.github/skills`, `.claude/skills`, or `.agents/skills` in your repository.

   * For **personal skills**, shared across projects: `~/.copilot/skills` or `~/.agents/skills` in your local home directory.

## Managing skills with GitHub CLI

> \[!NOTE]
> `gh skill` is in public preview and subject to change. To use it, update GitHub CLI to version 2.90.0 or later.

You can use the `gh skill` command in GitHub CLI to discover, install, update, and publish agent skills from GitHub repositories.

For the full list of `gh skill` subcommands, run `gh skill --help` or see the [`gh skill`](<https://cli.github.com/manual/gh_skill>) section of the GitHub CLI manual.

### Installing skills

You can search for skills, preview them, and install them from GitHub repositories.

> \[!WARNING]
> Skills are not verified by GitHub and may contain prompt injections, hidden instructions, or malicious scripts. Always inspect the content of a skill before installation using `gh skill preview`.

1. Search for skills by topic:

   ```shell
   gh skill search TOPIC
   ```

2. Preview a skill to inspect its contents before installing. This renders the skill's `SKILL.md` and file tree in your terminal without installing anything:

   ```shell
   gh skill preview OWNER/REPOSITORY SKILL
   ```

3. Install a skill. You can run `gh skill install` with no arguments for a fully interactive flow, or specify a repository to browse its skills interactively:

   ```shell
   gh skill install OWNER/REPOSITORY
   ```

   To install a specific skill directly:

   ```shell
   gh skill install OWNER/REPOSITORY SKILL
   ```

   For example, to install a skill from the [`github/awesome-copilot`](<https://github.com/github/awesome-copilot>) repository:

   ```shell copy
   gh skill install github/awesome-copilot documentation-writer
   ```

   You can install a specific version using `@TAG` or `@SHA`:

   ```shell copy
   gh skill install github/awesome-copilot documentation-writer@v1.2.0
   ```

   To lock a skill to a specific version (or commit SHA) so it is skipped during updates, use `--pin`:

   ```shell copy
   gh skill install github/awesome-copilot documentation-writer --pin v1.2.0
   ```

   > \[!NOTE]
   > The `@VERSION` syntax and `--pin` flag are mutually exclusive. Use one or the other, not both.

   To install a skill for a specific agent host, use the `--agent` flag. To control the install scope, use `--scope`:

   ```shell copy
   gh skill install github/awesome-copilot documentation-writer --agent claude-code --scope user
   ```

Skills are automatically installed to the correct directory for your agent host. By default, skills are installed for Copilot at project scope.

### Updating skills

When you install a skill with `gh skill`, provenance metadata is written into the skill's `SKILL.md` frontmatter, including the source repository, ref, and tree SHA. The `gh skill update` command uses this metadata to check for upstream changes.

To check for updates interactively:

```shell
gh skill update
```

To update a specific skill:

```shell
gh skill update SKILL
```

To update all installed skills without prompting:

```shell
gh skill update --all
```

Pinned skills are skipped during updates. To update a pinned skill, reinstall it with a new `--pin` value.

### Publishing skills

If you maintain a skills repository, you can validate and publish your skills using GitHub CLI.

To validate your skills against the [Agent Skills specification](<https://agentskills.io/specification>) and check remote settings like tag protection, secret scanning, and code scanning, without publishing, use `--dry-run`:

```shell
gh skill publish --dry-run
```

To auto-fix metadata issues in your skill files, use `--fix`. This does not publish your skills:

```shell
gh skill publish --fix
```

To validate and publish your skills:

```shell
gh skill publish
```

## How Copilot uses agent skills

When performing tasks, Copilot will decide when to use your skills based on your prompt and the skill's description.

When Copilot chooses to use a skill, the `SKILL.md` file will be injected in the agent's context, giving the agent access to your instructions. It can then follow those instructions and use any scripts or examples you may have included in the skill's directory.

For Copilot code review on GitHub, keep the following in mind:

* If you want to ensure that Copilot code review will read and use a skill, use a review-focused skill directory name such as `code-review`.
* Existing skills within the `.github/skills` directory can also be used by Copilot code review automatically when they are relevant to the review.

## Skills versus custom instructions

You can use both skills and custom instructions to teach Copilot how to work in your repository and how to perform specific tasks.

We recommend using **custom instructions** for simple instructions relevant to almost every task (for example information about your repository's coding standards), and **skills** for more detailed instructions that Copilot should only access when relevant.

To learn more about repository custom instructions, see [Adding repository custom instructions for GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions>).


</details>
