# GitHub Copilot CLI for Beginners: Overview of common slash commands

Welcome back to GitHub Copilot CLI for Beginners! In this series (available in [video](https://www.youtube.com/playlist?list=PL0lo9MOBetEHvO-spzKBAITkkTqv4RvNl) and [blog](https://github.blog/tag/github-copilot-cli-for-beginners/) format), we’ll give you everything you need to get started using [GitHub Copilot CLI](https://github.com/features/copilot/cli?utm_source=blog-cli-beginners-ep1-features-cta&utm_medium=blog&utm_campaign=dev-pod-copilot-cli-2026). So far in this series, we’ve covered [how to get started](https://github.blog/ai-and-ml/github-copilot/github-copilot-cli-for-beginners-getting-started-with-github-copilot-cli/) and [when to use interactive and non-interactive modes](https://github.blog/ai-and-ml/github-copilot/github-copilot-cli-for-beginners-interactive-v-non-interactive-mode/). In this edition, we’ll learn what slash commands are, why they matter, and how to use slash commands to control GitHub Copilot efficiently. You can complete tasks like switching models, checking token usage, and resuming past sessions right from your terminal.

Let’s dive in!

## Understanding slash commands in GitHub Copilot CLI

When working in Copilot CLI, one of the most powerful concepts to learn early on is **slash commands.** Slash commands are built-in controls that you can access directly from the command line. Acting as your control surface within Copilot CLI, slash commands allow you to:

* Guide Copilot’s behavior
* Inspect changes
* Manage context
* Move efficiently across sessions and projects
* Keep permissions tidy

Slash commands can be thought of as your command center for interacting with Copilot CLI. To look at all of the options available, just type `/` in the command line for a scrollable list of all currently supported slash commands.

Let’s take a look at some of the most popular ones.

## Choosing the right model

Different models are optimized for different kinds of work. If you want to switch models, type `/model` into the command line. This will display a list of available models, along with key details like:

* **Capabilities**: Some are better for quick, lightweight tasks like refactoring, while others more efficiently handle deeper reasoning such as feature planning.
* **Availability**: The list may vary depending on your plan or organization’s settings.
* **Cost**: Numbers shown on the right of each model indicate cost multiplier, helping you choose the right balance between performance and usage in relation to your plan.

Choosing the right model can significantly impact both speed and results.

## Managing context and token usage

Copilot CLI operates within a context window, which determines how much information it can “remember” during a session. If you want to check your current usage, type `/context` to learn how many tokens you have left, along with system usage and available buffer.

If you find that you’re running low on space, you can free up space by typing `/compact` in the command line. This summarizes your current conversation so you can continue without having to start a new session. Copilot CLI will do this automatically when you approach the limit, but you can also do this manually if you want to transition to a new task or clean up context mid-session.

If you’d rather start fresh and completely reset your environment, you can use `/clear` to clear the session entirely.

## Working across sessions

If you want to resume a previous session, you can type `/resume`. This will bring up a list of previous sessions you’ve had, including both local and remote sessions. Entering a previous session will show you your session history, and you can pick up right where you left off.

## Inspecting changes

As you work with Copilot to make changes to your project, it’s important to keep track of what’s changed. If you want to see what the changes are, run `/diff` to see recent updates. This gives you a clear view of what modifications were made during your session, so you can validate changes before moving forward.

## Navigating projects and directories

If you want to work across repositories or directories, you don’t have to exit Copilot. You can type `/cwd` to change your working directory to another repository. This allows you to scope Copilot’s work to a specific part of your project and helps you stay efficient while multitasking across codebases.

## Managing tool permissions

In the past, you might have granted Copilot CLI permission to perform actions like editing files. Say you’re switching to a repository you want to be more careful in and want to reset those permissions: you can do so by running `/reset-allowed-tools`.

## Take this with you

Using these slash commands gives you even better control over Copilot CLI—and the more familiar you become with them, the more deliberate your workflow becomes.

Whether you’re switching models, managing context, or navigating across projects, using slash commands in CLI gives you the tools you need to stay in control. And if you haven’t already: open up your terminal, type `/`, and explore! There are many more slash commands to discover.

Happy coding!

**Looking to try GitHub Copilot CLI?** [Read the docs](https://docs.github.com/en/copilot/concepts/agents/about-copilot-cli) and [get started](https://github.com/features/copilot/cli?utm_source=blog-cli-beginners-ep1-features-cta&utm_medium=blog&utm_campaign=dev-pod-copilot-cli-2026) today.

**More resources to explore:**

* [GitHub Copilot CLI for Beginners video series](https://www.youtube.com/playlist?list=PL0lo9MOBetEHvO-spzKBAITkkTqv4RvNl)
* [GitHub Copilot CLI for Beginners: Getting started with GitHub Copilot CLI](https://github.blog/ai-and-ml/github-copilot/github-copilot-cli-for-beginners-getting-started-with-github-copilot-cli/?utm_source=blog-announcement-cli-tutorial&utm_medium=blog&utm_campaign=universe25post)
* [GitHub Copilot CLI for Beginners: Interactive v. non-interactive mode](https://github.blog/ai-and-ml/github-copilot/github-copilot-cli-for-beginners-interactive-v-non-interactive-mode/)
* [GitHub Copilot CLI 101: How to use GitHub Copilot from the command line](https://github.blog/ai-and-ml/github-copilot-cli-101-how-to-use-github-copilot-from-the-command-line/?utm_source=blog-announcement-cli-tutorial&utm_medium=blog&utm_campaign=universe25post)
* [Best practices for GitHub Copilot CLI](https://docs.github.com/en/copilot/how-tos/copilot-cli/cli-best-practices?utm_campaign=copilot-brand&utm_medium=sem&utm_source=google&ocid=AIDcmmh2h80ugd_SEM__k_CjwKCAjw-dfOBhAjEiwAq0RwI0TIeyL9bjDmXlY26JKPbDvHGzBcaZUa4LR8u8SJuGbIke6e7U2YXRoCzGQQAvD_BwE_k_)

---

## Tags:

* [GitHub Copilot](https://github.blog/tag/github-copilot/)
* [GitHub Copilot CLI](https://github.blog/tag/github-copilot-cli/)
* [GitHub Copilot CLI for Beginners](https://github.blog/tag/github-copilot-cli-for-beginners/)

## Written by

![Kayla Cinnamon](https://avatars.githubusercontent.com/u/48369326?v=4&s=200)

### [Kayla Cinnamon](https://github.blog/author/cinnamonmsft/)

[@cinnamon-msft](https://github.com/cinnamon-msft)

Senior AI Developer Tools Advocate
