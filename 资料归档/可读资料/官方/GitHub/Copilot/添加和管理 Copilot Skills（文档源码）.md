# 添加和管理 Copilot Skills（文档源码）

介绍技能适用位置、gh skill 的搜索预览、安装更新与发布；创建步骤引用共享模板，所存源码保留这些引用。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 文档时间：官方文件提交（截至抓取）：[2026-06-08](<https://github.com/github/docs/commit/8af2b1df30eca5809ff2700a880f4c7291b511c6>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://raw.githubusercontent.com/github/docs/main/content/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/add-skills.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/添加和管理 Copilot Skills（文档源码）.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Adding agent skills for {% data variables.product.prodname_copilot %}
shortTitle: Add agent skills
allowTitleToDifferFromFilename: true
intro: 'You can modify {% data variables.product.prodname_copilot_short %}''s behavior and abilities when it works on particular tasks.'
versions:
  feature: copilot
contentType: how-tos
category:
  - Configure Copilot
  - Author and optimize with Copilot
redirect_from:
  - /copilot/how-tos/use-copilot-agents/coding-agent/create-skills
  - /copilot/how-tos/use-copilot-agents/cloud-agent/create-skills
  - /copilot/how-tos/use-copilot-agents/cloud-agent/add-skills
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

> [!NOTE]
> Agent skills 与 {% data variables.product.prodname_vscode %} 中的 {% data variables.copilot.copilot_cloud_agent %}、{% data variables.copilot.copilot_code-review_short %}、{% data variables.copilot.copilot_cli %}、{% data variables.copilot.github_copilot_app %} 和 agent 模式配合使用。

Agent skills 是 {% data variables.product.prodname_copilot_short %} 可以在相关时加载的指令、脚本和资源的文件夹，以提高其在专门任务中的性能。欲了解更多信息，请参阅[AUTOTITLE](<https://raw.githubusercontent.com/copilot/concepts/agents/about-agent-skills>)。

{% data reusables.copilot.creating-adding-skills %}

## 使用 {% data variables.product.prodname_cli %} 管理技能

> [！NOTE]
> `gh skill` 位于 {% data variables.release-phases.public_preview %} 中，可能会发生变化。要使用它，请将{% data variables.product.prodname_cli %}更新到版本2.90.0或更高版本。

您可以使用 {% data variables.product.prodname_cli %} 中的 `gh skill` 命令从 {% data variables.product.github %} 存储库发现、安装、更新和发布 agent skills。

有关 `gh skill` 子命令的完整列表，请运行 `gh skill --help` 或参阅 {% data variables.product.prodname_cli %} 手册的 [`gh skill`](<https://cli.github.com/manual/gh_skill>) 部分。

### 安装技巧

您可以搜索技能、预览它们并从 {% data variables.product.github %} 存储库安装它们。

> [！WARNING]
> 技能未经{% data variables.product.github %}验证，可能包含提示注入、隐藏指令或恶意脚本。安装前请务必使用 `gh skill preview` 检查技能内容。

1. 按主题搜索技能：

   ```shell
   gh skill search TOPIC
   ```

1. 在安装之前预览技能以检查其内容。这将在您的终端中呈现技能的 `SKILL.md` 和文件树，而无需安装任何内容：

   ```shell
   gh skill preview OWNER/REPOSITORY SKILL
   ```

1. 安装技能。您可以运行不带参数的 `gh skill install` 以实现完全交互式流程，或指定存储库以交互方式浏览其技能：

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

   > [!NOTE]
   > `@VERSION`语法和`--pin`标志是互斥的。使用其中之一，而不是同时使用两者。

要为特定的 agent 主机安装技能，请使用 `--agent` 标志。要控制安装范围，请使用`--scope`：

   ```shell copy
   gh skill install github/awesome-copilot documentation-writer --agent claude-code --scope user
   ```

技能会自动安装到您的 agent 主机的正确目录中。默认情况下，在项目范围内为 {% data variables.product.prodname_copilot_short %} 安装技能。

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

如果您维护技能存储库，则可以使用 {% data variables.product.prodname_cli %} 验证和发布您的技能。

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

## {% data variables.product.prodname_copilot_short %}如何使用agent skills

{% data reusables.copilot.skills-using %}

对于 {% data variables.product.github %} 上的 {% data variables.copilot.copilot_code-review_short %}，请记住以下几点：

* 如果要确保 {% data variables.copilot.copilot_code-review_short %} 能够读取并使用技能，请使用以复习为中心的技能目录名称，例如 `code-review`。
* `.github/skills`目录中的现有技能在与审核相关时也可以被{% data variables.copilot.copilot_code-review_short %}自动使用。

{% data reusables.copilot.skills-compared %}


## 原文对照

<details>
<summary>展开完整原文</summary>

> [!NOTE]
> Agent skills work with {% data variables.copilot.copilot_cloud_agent %}, {% data variables.copilot.copilot_code-review_short %}, the {% data variables.copilot.copilot_cli %}, the {% data variables.copilot.github_copilot_app %}, and agent mode in {% data variables.product.prodname_vscode %}.

Agent skills are folders of instructions, scripts, and resources that {% data variables.product.prodname_copilot_short %} can load when relevant to improve its performance in specialized tasks. For more information, see [AUTOTITLE](<https://raw.githubusercontent.com/copilot/concepts/agents/about-agent-skills>).

{% data reusables.copilot.creating-adding-skills %}

## Managing skills with {% data variables.product.prodname_cli %}

> [!NOTE]
> `gh skill` is in {% data variables.release-phases.public_preview %} and subject to change. To use it, update {% data variables.product.prodname_cli %} to version 2.90.0 or later.

You can use the `gh skill` command in {% data variables.product.prodname_cli %} to discover, install, update, and publish agent skills from {% data variables.product.github %} repositories.

For the full list of `gh skill` subcommands, run `gh skill --help` or see the [`gh skill`](<https://cli.github.com/manual/gh_skill>) section of the {% data variables.product.prodname_cli %} manual.

### Installing skills

You can search for skills, preview them, and install them from {% data variables.product.github %} repositories.

> [!WARNING]
> Skills are not verified by {% data variables.product.github %} and may contain prompt injections, hidden instructions, or malicious scripts. Always inspect the content of a skill before installation using `gh skill preview`.

1. Search for skills by topic:

   ```shell
   gh skill search TOPIC
   ```

1. Preview a skill to inspect its contents before installing. This renders the skill's `SKILL.md` and file tree in your terminal without installing anything:

   ```shell
   gh skill preview OWNER/REPOSITORY SKILL
   ```

1. Install a skill. You can run `gh skill install` with no arguments for a fully interactive flow, or specify a repository to browse its skills interactively:

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

   > [!NOTE]
   > The `@VERSION` syntax and `--pin` flag are mutually exclusive. Use one or the other, not both.

   To install a skill for a specific agent host, use the `--agent` flag. To control the install scope, use `--scope`:

   ```shell copy
   gh skill install github/awesome-copilot documentation-writer --agent claude-code --scope user
   ```

Skills are automatically installed to the correct directory for your agent host. By default, skills are installed for {% data variables.product.prodname_copilot_short %} at project scope.

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

If you maintain a skills repository, you can validate and publish your skills using {% data variables.product.prodname_cli %}.

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

## How {% data variables.product.prodname_copilot_short %} uses agent skills

{% data reusables.copilot.skills-using %}

For {% data variables.copilot.copilot_code-review_short %} on {% data variables.product.github %}, keep the following in mind:

* If you want to ensure that {% data variables.copilot.copilot_code-review_short %} will read and use a skill, use a review-focused skill directory name such as `code-review`.
* Existing skills within the `.github/skills` directory can also be used by {% data variables.copilot.copilot_code-review_short %} automatically when they are relevant to the review.

{% data reusables.copilot.skills-compared %}


</details>
