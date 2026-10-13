# 用 AI 更新社区协作文件

提供更新 README、贡献规范等文件的提示和检查流程，涵盖资料准备、安全、输出审查与反馈验证。

- 来源：GitHub／工程文章
- 资料类型：存档文档
- 文档时间：发表：[2025-10-21](<https://github.blog/ai-and-ml/github-copilot/how-to-update-community-health-files-with-ai/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.blog/ai-and-ml/github-copilot/how-to-update-community-health-files-with-ai/>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/工程文章/用 AI 更新社区协作文件.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 如何使用 AI 更新社区健康档案

维护项目的社区健康文件不应妨碍编写代码。 [GitHub Copilot](<https://github.com/features/copilot>) 可以帮助您更新和增强文档，以便您可以专注于真正重要的事情：从事最令您兴奋的项目。

在本博客中，我们将介绍一些最常见的社区健康文件（重点是 `README`、贡献者指南和许可证）以及它们对维护人员如此重要的原因，以及将它们添加到项目中可以采取的可行步骤。 ✨

## 什么是社区健康档案？为什么它们如此重要？

**社区健康文件**是标准化文档，有助于在[开源项目](<https://github.blog/open-source/new-to-open-source-heres-everything-you-need-to-get-started/>)中维护一个欢迎、有组织和协作的环境。这些文件传达期望、指导贡献者并支持存储库的整体健康状况。他们确实*不*包含技术文档或代码本身，而是包含支持健康协作的脚手架。您通常可以在存储库的根目录或特殊的 `.github` 文件夹中找到它们（如果它们需要跨多个存储库应用）。

保持这些文件最新应该被视为[对项目的未来和声誉的实际投资](<https://opensource.guide/building-community/>)，因为它们通常是新贡献者的第一个接触点，它们的存在标志着项目的成熟度和可维护性。它们不仅提高了透明度、一致性和协作性，而且还有助于为贡献者和维护者如何有效地互动和参与奠定基调。

如果重要的社区健康档案丢失或过时，每个人都会感受到影响。想象一下：您的开源项目开始受到新贡献者的关注。他们想要提供帮助，但您的存储库没有正确的文件，这会导致贡献者无意中错误地格式化拉取请求，打开模糊的问题，甚至引入安全漏洞 - 所有这些都是因为他们从一开始就不知道正确的程序。现在，您的维护人员不知所措，面临着一遍又一遍地回答相同的问题，同时还试图追溯执行标准。

很明显，这些文件的存在有助于提高效率并清晰地传达最佳实践，这反过来又为贡献者创造了更好的环境，并使维护人员的生活变得更轻松 - 并且由于 AI，该过程不必是手动的。例如，AI 工具（例如 GitHub Copilot）可以自动检测丢失或过时的文件，建议更新，甚至生成草稿，从而节省时间并减少人为错误。

以下是三种常见类型的社区健康文件，以及为什么它们对于建立一个热情的社区如此重要（别担心，我们将在本博客后面详细告诉您如何使用 Copilot 生成自己的文件！）：

**README** 
通常是访问者在查看存储库时首先看到的内容之一，[**`README.MD`**](<http://readme.md>) 介绍该项目并解释其目的以及如何开始。本文档旨在帮助消除障碍，为用户提供快速启动和运行所需的重要信息，例如项目是什么、其功能信息以及如何安装或使用它。

**CONTRIBUTOR GUIDE**A **贡献者指南** 提供了有关贡献者如何以及应该如何参与的指南，例如编码标准和拉取请求说明。本指南告诉用户如何有效地做出贡献以及期望什么。例如，该项目是否接受贡献？贡献者指南有助于设定标准和期望。

**LICENSE**A **许可证**指定了项目可以使用、修改和分发的法律条款。简而言之，它告诉人们如何使用您的软件。此类文件的常见示例是 MIT 许可证。

以下是其他一些流行的 [ 社区健康文件 ](<https://docs.github.com/en/communities/setting-up-your-project-for-healthy-contributions/creating-a-default-community-health-file>)：

|  |  |
| --- | --- |
| **ISSUE/PULL REQUEST TEMPLATES** |标准化提交问题或拉取请求时所需的格式和信息。|
| **SECURITY** |提供报告漏洞的说明并概述项目的安全策略。|
| **GOVERNANCE** |解释如何管理项目，包括角色、职责和决策过程。|
| **CODE OF CONDUCT** |定义如何参与社区的标准。|
| **SUPPORT** |分享有关其他人如何获得项目帮助的具体指导。|
| **FUNDING** |在存储库中显示赞助商按钮，以提高开源项目资助选项的可见性。|

虽然它不完全被视为社区健康文件，但我们想荣誉提及...... [**Copilot 指令文件 **](<https://docs.github.com/en/copilot/how-tos/configure-custom-instructions/add-repository-instructions>)，这是一个补充健康文档的 AI 配置。它使用其他社区健康文件作为上下文，并准确告诉 GitHub Copilot 如何与代码库交互，包括优先考虑或避免的内容。此文件有助于奠定 LLM — 无论您是在 VS Code、[github.com](<https://github.com>) 上使用 GitHub Copilot 或其他 LLM，还是使用 Copilot 编码agent - 让它了解您的项目是什么及其结构，从而实现整个代码库的一致性。

在项目中拥有这些类型的文件非常重要，尤其是在扩展开源项目时，维护人员可能没有时间亲自帮助每个贡献者。
  
这就是 GitHub Copilot 等节省时间的工具派上用场的地方。继续阅读可操作的后续步骤、提示和教程，了解将这些文件添加到存储库的最有效方法。 ✨

## 入门套件：如何使用 GitHub Copilot 更新社区健康文件

我们为您创建了一个入门工具包，解释了如何使用 AI 将这些有价值的文件添加到您的项目中，并提供提示最佳实践、包含需要考虑的事项的清单以及有关如何使用 Copilot 将三个常见文件添加到存储库的分步教程。让我们深入了解一下。

### 第一部分：提示

无论您是从头开始还是完善现有文档，GitHub Copilot 都可以帮助您通过几个提示编写更清晰、更一致的社区健康文件。

需要注意的一件事：支持 GitHub Copilot 的 LLM 是不确定的，这意味着您每次提示模型时都可以收到不同的输出。 [提示工程](<https://github.blog/ai-and-ml/generative-ai/prompt-engineering-guide-generative-ai-llms/>)可以极大地提高从LLM获得的输出的质量和相关性，但您仍然需要验证这些输出的准确性，特别是在使用Copilot生成更敏感的文件（例如具有合法效力的许可证）时。

### 第二部分：清单

此清单有助于确保 Copilot 生成的内容准确、包容、安全并与您的项目目标保持一致。

#### 🔍开始之前

* 您是否审查过类似或相关存储库中的现有社区健康文件？
* 您对于每个文件应传达的内容是否有明确的目标（例如，入门、行为期望、安全报告）？
* 您熟悉贵组织的 GitHub 使用政策和品牌指南吗？

#### 🧠 有效提示Copilot

* 您的提示是否具体且符合上下文？ （例如，“使用代码风格指南为基于 Python 的开源项目生成 `CONTRIBUTING.md`。”）
* 您的提示中是否包含了示例或语气偏好？ （例如，“使用包容性的语言和欢迎的语气。”）

#### 🛡️ 安全和隐私

* 您是否避免出现包含敏感或专有信息（例如内部凭据、私有 URL、机密项目名称）的提示？
* 您是否检查了存储库的可见性设置（公共与私有）并确保社区健康文件适合该受众？
* 您是否熟悉GitHub Copilot的隐私设置以及您的提示和建议如何处理？
* 您的 `SECURITY.md` 是否包括：
  + 报告漏洞的明确联系方式？
  + 简要说明如何对安全问题进行分类？
  + 是否有与贵组织负责任的披露政策相关的链接？

#### 🧾 审查 Copilot 输出

* 生成的内容是否反映了您项目的价值观和社区标准？
* 您是否检查过不存在的幻觉链接、名称或策略？
* 所有对外部资源的引用是否准确且最新？

#### 🧪 测试与反馈

* 您是否要求队友或贡献者检查生成的文件？
* 您是否测试过任何指令（例如，`README` 或 `CONTRIBUTING` 中的设置步骤）以确保它们有效？
* 您是否愿意根据社区反馈进行迭代？

### 第三部分：教程

在本教程中，我们将介绍如何使用 Copilot 快速轻松地更新 `README.md`、`LICENSE` 文件和 `CONTRIBUTING.md`。

#### 📝 创建一个 README

**为什么要创建 `README`？** 添加 `README` 可以清晰地概述您的项目，帮助用户和贡献者快速了解其目的、设置和用法。如果没有它，潜在用户可能会由于混乱或缺乏上下文而放弃您的存储库。

制作方法如下：

1. **在您的IDE中打开GitHub Copilot聊天**（例如，VS Code）。
2. **切换到agent模式**以启用项目感知帮助。
3. **选择您的首选型号**（例如Claude以获得强大的书写和编码支持）。
4. **确保您的项目在 IDE 中打开 **，以便 Copilot 可以读取其上下文（例如，`package.json`、`app.tsx`）。
5. 在聊天窗口中，输入：“帮我为我的项目编写 `README.md`。确保它包含安装说明、项目概述，并遵循标准 `README` 实践。”
6. **查看生成的README.md**。 Copilot 将分析您的项目文件并生成结构化的 `README.md`。
7. **手动验证安装说明**以确保准确性（法学硕士可能[产生幻觉）](<https://github.blog/ai-and-ml/llms/demystifying-llms-how-they-can-do-things-they-werent-trained-to-do/#hallucinations>)。
8. 如果满意，**点击“保留”**保存`README.md`文件。
9. **将 `README.md`** 提交到您的存储库。

#### 📄 添加许可证

**为什么要制作许可证？** 许可证定义了其他人如何合法地使用、修改和分发您的代码，从而保护您和他们的权利。它消除了歧义并防止滥用，使您的项目更安全地采用和贡献。

添加方法如下：

1. **在您的IDE中打开GitHub Copilot聊天**。
2. 决定[您要添加哪种类型的许可证](<https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/licensing-a-repository>)。
3. 输入以下提示：“您可以将[您想要的许可证]添加到我的项目中吗？”
4. Copilot 将生成带有您选择的许可证的 `LICENSE` 文件。
5. **查看许可证**以确保其准确（尤其是任何版权所有者姓名和声明）。
6. 如果正确，**点击“保留”**保存文件。
7. **将`LICENSE`文件**提交到您的存储库。

#### 🤝  创建贡献者指南

**为什么要制定贡献者指南？** 贡献者指南通过概述贡献标准、工作流程和期望来简化协作。这使得其他人更容易参与您的项目。目标是减少摩擦和错误，同时鼓励一致、可扩展的贡献。

创建方法如下：

1. **在您的IDE中打开GitHub Copilot聊天**。
2. 点击**“+”图标**开始新的聊天。
3. 输入以下提示：*“创建遵循最佳实践的贡献指南文件并将其链接到 `README`。”*
4. Copilot 将生成一个 `CONTRIBUTING.md` 文件，其中包含：
   * 贡献指南
   * 代码标准
   * 拉取请求说明
   * 问题报告流程
5. **查看并编辑**指南以匹配您团队的工作流程。
6. **保存并提交** `CONTRIBUTING.md` 文件。
7. **更新您的 README** 以包含贡献者指南的链接：

```
## Contributing
See CONTRIBUTING.md for guidelines.
```

## 随身携带这个

GitHub Copilot 不仅仅用于编写代码，它也可以成为您的文档助手。 Copilot 帮助您更智能、更快速、更少摩擦地编写内容，完善您的社区健康文件，扩展最佳实践，并将良好的意图转化为出色的文档。

结果呢？更好的文档、更强大的社区和更快乐的维护者。

[阅读文档](<https://docs.github.com/en/copilot/about-github-copilot/github-copilot-features>)以了解有关GitHub Copilot功能的更多信息，或立即[开始使用](<https://resources.github.com/copilot-for-business/?ef_id=_k_Cj0KCQjwsp6pBhCfARIsAD3GZubTXuCGU1hy65GlbZ2fA1YjoRRhw64GoF8UI-lrQsnWSqAWJ7dC3QoaAqQ4EALw_wcB_k_&OCID=AIDcmmc3fhtaow_SEM__k_Cj0KCQjwsp6pBhCfARIsAD3GZubTXuCGU1hy65GlbZ2fA1YjoRRhw64GoF8UI-lrQsnWSqAWJ7dC3QoaAqQ4EALw_wcB_k_&gclid=Cj0KCQjwsp6pBhCfARIsAD3GZubTXuCGU1hy65GlbZ2fA1YjoRRhw64GoF8UI-lrQsnWSqAWJ7dC3QoaAqQ4EALw_wcB>)。

---

## 标签：

* [社区健康档案](<https://github.blog/tag/community-health-files/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [维护者](<https://github.blog/tag/maintainers/>)

## 撰写者

![亚历山德拉·利茨克](<https://avatars.githubusercontent.com/u/188614820?v=4&s=200>)

### [亚历山德拉·利茨克](<https://github.blog/author/lexilietzke/>)

[@lexi-lietzke](<https://github.com/lexi-lietzke>)

内容作家


## 原文对照

<details>
<summary>展开完整原文</summary>

# How to update community health files with AI

Maintaining your project’s community health files shouldn’t get in the way of writing code. [GitHub Copilot](<https://github.com/features/copilot>) can help you update and enhance your documentation, so you can stay focused on what really matters: working on the projects that excite you most.

In this blog, we’ll touch on some of the most common community health files (focusing on `README`, contributor guides, and licenses) and why they’re so important for maintainers, along with actionable steps you can take to add them to your projects. ✨

## What are community health files and why are they so important?

**Community health files** are standardized documents that help maintain a welcoming, organized, and collaborative environment in [open source projects](<https://github.blog/open-source/new-to-open-source-heres-everything-you-need-to-get-started/>). These files communicate expectations, guide contributors, and support the overall health of a repository. They do *not* include technical documentation or code itself, but rather the scaffolding that supports healthy collaboration. You can typically find them in a repository’s root directory or in a special `.github` folder (if they need to be applied across multiple repositories).

Keeping these files up-to-date should be considered [a practical investment into your project’s future and reputation](<https://opensource.guide/building-community/>), as they’re often the first touchpoint for new contributors, and their existence signals project maturity and maintainability. They not only improve transparency, consistency, and collaboration, but also help set the tone for how contributors and maintainers interact and engage productively.

If crucial community health files are missing or outdated, everyone feels the effects. Picture this: Your open source project starts gaining traction with new contributors. They want to help, but your repository doesn’t have the right files, which leads to contributors unintentionally formatting pull requests incorrectly, opening vague issues, and even introducing security vulnerabilities—all because they didn’t know the proper procedures from the start. Now, your maintainers are overwhelmed and faced with answering the same questions over and over, while also trying to retroactively enforce standards.

It’s clear that the presence of these files helps promote efficiency and clearly communicates best practices, which in turn, creates a better environment for contributors and makes life easier for maintainers—and thanks to AI, the process doesn’t have to be manual. AI tools like GitHub Copilot, for example, can automatically detect missing or stale files, suggest updates, and even generate drafts—saving time and reducing human error.

Here are three common types of community health files and why they’re so important for building a welcoming community (and don’t worry, we’ll tell you exactly how you can generate your own with Copilot later in this blog!):

**README**  
Often one of the first things a visitor sees when viewing a repository, a[**`README.MD`**](<http://readme.md>)introduces the project and explains its purpose, along with how to get started. Intended to help remove barriers, this document gives your users crucial information they need to quickly get up and running—like what the project is, information on its features, and how to install or use it.

**CONTRIBUTOR GUIDE**A **contributor guide** provides guidelines on how contributors can and should participate—things like coding standards and pull request instructions. This guide tells users how they can efficiently contribute and what to expect. For instance, does the project even accept contributions? Contributor guides help set standards and expectations.

**LICENSE**A **license** specifies the legal terms under which the project can be used, modified, and distributed. In short, it tells people how they can use your software. A common example of this type of file is the MIT License.

Here are some other popular [community health files](<https://docs.github.com/en/communities/setting-up-your-project-for-healthy-contributions/creating-a-default-community-health-file>):

|  |  |
| --- | --- |
| **ISSUE/PULL REQUEST TEMPLATES** | Standardizes the format and information required when submitting issues or pull requests. |
| **SECURITY** | Provides instructions for reporting vulnerabilities and outlines the project’s security policy. |
| **GOVERNANCE** | Explains how the project is managed, including roles, responsibilities, and decision-making processes. |
| **CODE OF CONDUCT** | Defines standards for how to engage in a community. |
| **SUPPORT** | Shares specific guidance on how others can get help with your project. |
| **FUNDING** | Displays a sponsor button in your repository to increase the visibility of funding options for your open source project. |

And while it’s not exactly considered a community health file, we wanted to give an honorable mention to… the [**Copilot instructions file**](<https://docs.github.com/en/copilot/how-tos/configure-custom-instructions/add-repository-instructions>), which is an AI configuration that complements health docs. It uses the other community health files as context and tells GitHub Copilot exactly how to interact with the codebase, including what to prioritize or avoid. This file helps ground the LLM—whether you’re using GitHub Copilot or another LLM in VS Code, on [github.com](<https://github.com>), or Copilot coding agent—giving it an understanding of what your project is and how it’s structured, allowing for consistency across your codebase.

Having these kinds of files in your project is so important, especially when it comes to scaling open source projects where maintainers probably don’t have time to personally help every contributor.  
  
That’s where time-saving tools like GitHub Copilot come in handy. Keep on reading for actionable next steps, tips, and tutorials on the most efficient ways to add these files to your repositories. ✨

## Starter kit: How to update community health files using GitHub Copilot

We created a starter kit for you that explains how you can use AI to add these valuable files to your projects, complete with prompting best practices, a checklist full of things to consider, and step-by-step tutorials on how to add three common files to your repository using Copilot. Let’s dive in.

### Part one: Prompting

Whether you’re starting from scratch or refining existing documentation, GitHub Copilot can help you write clearer, more consistent community health files with just a few prompts.

One thing to note: The LLMs powering GitHub Copilot are nondeterministic, which means that you can receive different outputs each time you prompt the model. [Prompt engineering](<https://github.blog/ai-and-ml/generative-ai/prompt-engineering-guide-generative-ai-llms/>) can drastically improve the quality and relevance of the outputs you get from an LLM, but you’ll still want to verify the accuracy of these outputs, especially when using Copilot to generate more sensitive files like licenses that have legal weight.

### Part two: Checklist

This checklist helps ensure that Copilot-generated content is accurate, inclusive, secure, and aligned with your project’s goals.

#### 🔍 Before you start

* Have you reviewed existing community health files in similar or related repositories?
* Do you have clear goals for what each file should communicate (e.g., onboarding, behavior expectations, security reporting)?
* Are you familiar with your organization’s GitHub usage policies and branding guidelines?

#### 🧠 Prompting Copilot effectively

* Are your prompts specific and contextual? (e.g., “Generate a `CONTRIBUTING.md` for a Python-based open source project with a code style guide.”)
* Have you included examples or tone preferences in your prompt? (e.g., “Use inclusive language and a welcoming tone.”)

#### 🛡️ Security & privacy

* Are you avoiding prompts that include sensitive or proprietary information (e.g., internal credentials, private URLs, confidential project names)?
* Have you reviewed your repository’s visibility settings (public vs. private) and ensured that community health files are appropriate for that audience?
* Are you familiar with GitHub Copilot’s privacy settings and how your prompts and suggestions are handled?
* Will your `SECURITY.md` include:
  + A clear contact method for reporting vulnerabilities?
  + A brief explanation of how security issues are triaged?
  + Any relevant links to your organization’s responsible disclosure policy?

#### 🧾 Reviewing Copilot output

* Does the generated content reflect your project’s values and community standards?
* Have you checked for hallucinated links, names, or policies that don’t exist?
* Are all references to external resources accurate and up-to-date?

#### 🧪 Testing & feedback

* Have you asked a teammate or contributor to review the generated files?
* Have you tested any instructions (e.g., setup steps in `README` or `CONTRIBUTING`) to ensure they work?
* Are you open to iterating based on community feedback?

### Part three: Tutorial

In this tutorial, we’ll walk through how you can use Copilot to quickly and easily update `README.md`, a `LICENSE` file, and `CONTRIBUTING.md`.

#### 📝 Create a README

**Why make a `README`?** Adding a `README` provides a clear overview of your project, helping users and contributors quickly understand its purpose, setup, and usage. Without it, potential users could abandon your repository due to confusion or lack of context.

Here’s how to make one:

1. **Open GitHub Copilot Chat** in your IDE (e.g., VS Code).
2. **Switch to agent mode** to enable project-aware assistance.
3. **Select your preferred model** (e.g., Claude for strong writing and coding support).
4. **Ensure your project is open** in the IDE so Copilot can read its context (e.g., `package.json`, `app.tsx`).
5. In the chat window, type: “Help me write a `README.md` for my project. Ensure it includes installation instructions, a project overview, and follows standard `README` practices.”
6. **Review the generated README.md**. Copilot will analyze your project files and generate a structured `README.md`.
7. **Validate the installation instructions manually** to ensure accuracy (LLMs may [hallucinate)](<https://github.blog/ai-and-ml/llms/demystifying-llms-how-they-can-do-things-they-werent-trained-to-do/#hallucinations>).
8. If satisfied, **click “Keep”** to save the `README.md` file.
9. **Commit the `README.md`** to your repository.

#### 📄 Add a license

**Why make a license?** A license defines how others can legally use, modify, and distribute your code, protecting both your rights and theirs. It removes ambiguity and prevents misuse, making your project safer to adopt and contribute to.

Here’s how to add one:

1. **Open GitHub Copilot Chat** in your IDE.
2. Decide [what kind of license you want to add](<https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/licensing-a-repository>).
3. Type the following prompt: “Can you add [the license you want] to my project?”
4. Copilot will generate a `LICENSE` file with the license of your choice.
5. **Review the license** to ensure it’s accurate (especially any copyright owner names and statements).
6. If correct, **click “Keep”** to save the file.
7. **Commit the `LICENSE` file** to your repository.

#### 🤝  Create a contributor guide

**Why make a contributor guide?** A contributor guide streamlines collaboration by outlining contribution standards, workflows, and expectations. This makes it easier for others to get involved with your project. The goal is to reduce friction and errors while also encouraging consistent, scalable contributions.

Here’s how to create one:

1. **Open GitHub Copilot Chat** in your IDE.
2. Click the **“+” icon** to start a new chat.
3. Type this prompt: *“Create a contributing guide file that follows best practices and link it in the `README`.”*
4. Copilot will generate a `CONTRIBUTING.md` file with:
   * Contribution guidelines
   * Code standards
   * Pull request instructions
   * Issue reporting process
5. **Review and edit** the guide to match your team’s workflow.
6. **Save and commit** the `CONTRIBUTING.md` file.
7. **Update your README** to include a link to the contributor guide:

```
## Contributing
See CONTRIBUTING.md for guidelines.
```

## Take this with you

GitHub Copilot isn’t just for writing code—it can be your documentation sidekick, too. Helping you write smarter, faster, and with less friction, Copilot sharpens your community health files, scales best practices, and turns good intentions into great documentation.

The result? Better docs, stronger communities, and happier maintainers.

[Read the Docs](<https://docs.github.com/en/copilot/about-github-copilot/github-copilot-features>) to learn more about GitHub Copilot features or [get started](<https://resources.github.com/copilot-for-business/?ef_id=_k_Cj0KCQjwsp6pBhCfARIsAD3GZubTXuCGU1hy65GlbZ2fA1YjoRRhw64GoF8UI-lrQsnWSqAWJ7dC3QoaAqQ4EALw_wcB_k_&OCID=AIDcmmc3fhtaow_SEM__k_Cj0KCQjwsp6pBhCfARIsAD3GZubTXuCGU1hy65GlbZ2fA1YjoRRhw64GoF8UI-lrQsnWSqAWJ7dC3QoaAqQ4EALw_wcB_k_&gclid=Cj0KCQjwsp6pBhCfARIsAD3GZubTXuCGU1hy65GlbZ2fA1YjoRRhw64GoF8UI-lrQsnWSqAWJ7dC3QoaAqQ4EALw_wcB>) today.

---

## Tags:

* [community health files](<https://github.blog/tag/community-health-files/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [maintainers](<https://github.blog/tag/maintainers/>)

## Written by

![Alexandra Lietzke](<https://avatars.githubusercontent.com/u/188614820?v=4&s=200>)

### [Alexandra Lietzke](<https://github.blog/author/lexilietzke/>)

[@lexi-lietzke](<https://github.com/lexi-lietzke>)

Content Writer


</details>
