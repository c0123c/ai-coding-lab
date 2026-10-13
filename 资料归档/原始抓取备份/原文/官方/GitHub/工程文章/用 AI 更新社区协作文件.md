# How to update community health files with AI

Maintaining your project’s community health files shouldn’t get in the way of writing code. [GitHub Copilot](https://github.com/features/copilot) can help you update and enhance your documentation, so you can stay focused on what really matters: working on the projects that excite you most.

In this blog, we’ll touch on some of the most common community health files (focusing on `README`, contributor guides, and licenses) and why they’re so important for maintainers, along with actionable steps you can take to add them to your projects. ✨

## What are community health files and why are they so important?

**Community health files** are standardized documents that help maintain a welcoming, organized, and collaborative environment in [open source projects](https://github.blog/open-source/new-to-open-source-heres-everything-you-need-to-get-started/). These files communicate expectations, guide contributors, and support the overall health of a repository. They do *not* include technical documentation or code itself, but rather the scaffolding that supports healthy collaboration. You can typically find them in a repository’s root directory or in a special `.github` folder (if they need to be applied across multiple repositories).

Keeping these files up-to-date should be considered [a practical investment into your project’s future and reputation](https://opensource.guide/building-community/), as they’re often the first touchpoint for new contributors, and their existence signals project maturity and maintainability. They not only improve transparency, consistency, and collaboration, but also help set the tone for how contributors and maintainers interact and engage productively.

If crucial community health files are missing or outdated, everyone feels the effects. Picture this: Your open source project starts gaining traction with new contributors. They want to help, but your repository doesn’t have the right files, which leads to contributors unintentionally formatting pull requests incorrectly, opening vague issues, and even introducing security vulnerabilities—all because they didn’t know the proper procedures from the start. Now, your maintainers are overwhelmed and faced with answering the same questions over and over, while also trying to retroactively enforce standards.

It’s clear that the presence of these files helps promote efficiency and clearly communicates best practices, which in turn, creates a better environment for contributors and makes life easier for maintainers—and thanks to AI, the process doesn’t have to be manual. AI tools like GitHub Copilot, for example, can automatically detect missing or stale files, suggest updates, and even generate drafts—saving time and reducing human error.

Here are three common types of community health files and why they’re so important for building a welcoming community (and don’t worry, we’ll tell you exactly how you can generate your own with Copilot later in this blog!):

**README**  
Often one of the first things a visitor sees when viewing a repository, a[**`README.MD`**](http://readme.md)introduces the project and explains its purpose, along with how to get started. Intended to help remove barriers, this document gives your users crucial information they need to quickly get up and running—like what the project is, information on its features, and how to install or use it.

**CONTRIBUTOR GUIDE**A **contributor guide** provides guidelines on how contributors can and should participate—things like coding standards and pull request instructions. This guide tells users how they can efficiently contribute and what to expect. For instance, does the project even accept contributions? Contributor guides help set standards and expectations.

**LICENSE**A **license** specifies the legal terms under which the project can be used, modified, and distributed. In short, it tells people how they can use your software. A common example of this type of file is the MIT License.

Here are some other popular [community health files](https://docs.github.com/en/communities/setting-up-your-project-for-healthy-contributions/creating-a-default-community-health-file):

|  |  |
| --- | --- |
| **ISSUE/PULL REQUEST TEMPLATES** | Standardizes the format and information required when submitting issues or pull requests. |
| **SECURITY** | Provides instructions for reporting vulnerabilities and outlines the project’s security policy. |
| **GOVERNANCE** | Explains how the project is managed, including roles, responsibilities, and decision-making processes. |
| **CODE OF CONDUCT** | Defines standards for how to engage in a community. |
| **SUPPORT** | Shares specific guidance on how others can get help with your project. |
| **FUNDING** | Displays a sponsor button in your repository to increase the visibility of funding options for your open source project. |

And while it’s not exactly considered a community health file, we wanted to give an honorable mention to… the [**Copilot instructions file**](https://docs.github.com/en/copilot/how-tos/configure-custom-instructions/add-repository-instructions), which is an AI configuration that complements health docs. It uses the other community health files as context and tells GitHub Copilot exactly how to interact with the codebase, including what to prioritize or avoid. This file helps ground the LLM—whether you’re using GitHub Copilot or another LLM in VS Code, on [github.com](https://github.com), or Copilot coding agent—giving it an understanding of what your project is and how it’s structured, allowing for consistency across your codebase.

Having these kinds of files in your project is so important, especially when it comes to scaling open source projects where maintainers probably don’t have time to personally help every contributor.  
  
That’s where time-saving tools like GitHub Copilot come in handy. Keep on reading for actionable next steps, tips, and tutorials on the most efficient ways to add these files to your repositories. ✨

## Starter kit: How to update community health files using GitHub Copilot

We created a starter kit for you that explains how you can use AI to add these valuable files to your projects, complete with prompting best practices, a checklist full of things to consider, and step-by-step tutorials on how to add three common files to your repository using Copilot. Let’s dive in.

### Part one: Prompting

Whether you’re starting from scratch or refining existing documentation, GitHub Copilot can help you write clearer, more consistent community health files with just a few prompts.

One thing to note: The LLMs powering GitHub Copilot are nondeterministic, which means that you can receive different outputs each time you prompt the model. [Prompt engineering](https://github.blog/ai-and-ml/generative-ai/prompt-engineering-guide-generative-ai-llms/) can drastically improve the quality and relevance of the outputs you get from an LLM, but you’ll still want to verify the accuracy of these outputs, especially when using Copilot to generate more sensitive files like licenses that have legal weight.

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
7. **Validate the installation instructions manually** to ensure accuracy (LLMs may [hallucinate)](https://github.blog/ai-and-ml/llms/demystifying-llms-how-they-can-do-things-they-werent-trained-to-do/#hallucinations).
8. If satisfied, **click “Keep”** to save the `README.md` file.
9. **Commit the `README.md`** to your repository.

#### 📄 Add a license

**Why make a license?** A license defines how others can legally use, modify, and distribute your code, protecting both your rights and theirs. It removes ambiguity and prevents misuse, making your project safer to adopt and contribute to.

Here’s how to add one:

1. **Open GitHub Copilot Chat** in your IDE.
2. Decide [what kind of license you want to add](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/licensing-a-repository).
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

[Read the Docs](https://docs.github.com/en/copilot/about-github-copilot/github-copilot-features) to learn more about GitHub Copilot features or [get started](https://resources.github.com/copilot-for-business/?ef_id=_k_Cj0KCQjwsp6pBhCfARIsAD3GZubTXuCGU1hy65GlbZ2fA1YjoRRhw64GoF8UI-lrQsnWSqAWJ7dC3QoaAqQ4EALw_wcB_k_&OCID=AIDcmmc3fhtaow_SEM__k_Cj0KCQjwsp6pBhCfARIsAD3GZubTXuCGU1hy65GlbZ2fA1YjoRRhw64GoF8UI-lrQsnWSqAWJ7dC3QoaAqQ4EALw_wcB_k_&gclid=Cj0KCQjwsp6pBhCfARIsAD3GZubTXuCGU1hy65GlbZ2fA1YjoRRhw64GoF8UI-lrQsnWSqAWJ7dC3QoaAqQ4EALw_wcB) today.

---

## Tags:

* [community health files](https://github.blog/tag/community-health-files/)
* [GitHub Copilot](https://github.blog/tag/github-copilot/)
* [maintainers](https://github.blog/tag/maintainers/)

## Written by

![Alexandra Lietzke](https://avatars.githubusercontent.com/u/188614820?v=4&s=200)

### [Alexandra Lietzke](https://github.blog/author/lexilietzke/)

[@lexi-lietzke](https://github.com/lexi-lietzke)

Content Writer
