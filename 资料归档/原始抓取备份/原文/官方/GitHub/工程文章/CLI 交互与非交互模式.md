# GitHub Copilot CLI for Beginners: Interactive v. non-interactive mode

Welcome to GitHub Copilot CLI for Beginners! In this series (available in [video](https://www.youtube.com/playlist?list=PL0lo9MOBetEHvO-spzKBAITkkTqv4RvNl) and [blog](https://github.blog/tag/github-copilot-cli-for-beginners/) format), we’ll give you everything you need to get started using [GitHub Copilot CLI](https://github.com/features/copilot/cli?utm_source=blog-cli-beginners-ep2-features-cta&utm_medium=blog&utm_campaign=dev-pod-copilot-cli-2026), from your first prompt to tips for navigating the command line like a pro!

In this blog, we’ll cover the two main modes of the CLI: interactive and non-interactive. You’ll learn the differences between the two modes, how to enter them, and what they’re most useful for.

Let’s dive in!

## What is GitHub Copilot CLI interactive mode?

Interactive mode is a back-and-forth, chat-like experience. When you launch Copilot CLI with Copilot, you’re already in interactive mode—that’s the default. Non-interactive mode is a separate option for when you want a quick, one-off answer without entering a session. (More on non-interactive mode later!)

In interactive mode, you can ask GitHub Copilot a question, review its response, and then either follow up with questions or another prompt—all within the same session. This is the mode for those who want to work hands-on with Copilot and iterate as you go.

Here’s how to enter interactive mode:

* From the command line, type `copilot` and hit `Enter`.
* Copilot may ask you to trust this folder, because it needs permission to read and modify files.
* Ask Copilot a question, like “How do I run this project locally?”
* Copilot will give you instructions, which you can do on your own. But if you want to work collaboratively, you can ask Copilot: “Can you run it for me?”
* Copilot will analyze your project and then start the server.
* We can review our project, decide what changes we want, and continue working with Copilot, all in the same session.

## What is GitHub Copilot CLI non-interactive mode?

On the other hand, non-interactive mode is designed for speed and simplicity. Instead of having to enter a full session, you pass a single prompt right in the command line and get a response almost immediately, without needing to follow up with Copilot.

Designed as an in-line experience, this mode is perfect for quick, one-shot prompts like summarizing a repository, generating code snippets, or plugging Copilot into automated workflows, without leaving your shell context. Once you get an answer, you’re right back in your terminal flow.

Here’s how to enter non-interactive mode:

* Start at the regular command line (if you’re in Copilot, you’ll need to exit).
* Type `copilot -p` and prompt the agent with something like “Quickly summarize what this repository does and the key folders.”
* Copilot will sift through your project files to provide an answer. Ta-da! ✨

Together, these two modes help you tackle all kinds of projects efficiently: interactive for explorative, deeper work, and non-interactive for fast, focused results when you already know exactly what you need.

## How to resume a previous Copilot session

Sometimes, you may want to pick up right where you left off in a previous Copilot session, while retaining all the context from that conversation.

If you’re in interactive mode, you can type `/resume` into the command line and Copilot will let you choose a previous session from a list. If you want to launch directly into the previous session picker from non-interactive mode, use `copilot --resume`.

It only takes one command to pick back up with Copilot, which is super useful if you already know what session you want to work in.

Take this with you GitHub Copilot CLI interactive and non-interactive modes are the fastest ways to prompt Copilot directly from your terminal. Having the option to pick between back-and-forth coding and quick prompting means you can work with Copilot, the way you want.

Keep an eye out for more videos in the GitHub Copilot CLI for Beginners series, where we’ll explore:

* Copilot CLI slash commands
* Using MCP servers with Copilot CLI
* And more!

Happy coding!

**Looking to try GitHub Copilot CLI?** [Read the Docs](https://docs.github.com/copilot/concepts/agents/about-copilot-cli?utm_source=blog-cli-beginners-ep2-features-cta&utm_medium=blog&utm_campaign=dev-pod-copilot-cli-2026) and [get started](https://github.com/features/copilot/cli?utm_source=blog-cli-beginners-ep2-features-cta&utm_medium=blog&utm_campaign=dev-pod-copilot-cli-2026) today.

### More resources to explore:

* [GitHub Copilot CLI for Beginners video series](https://www.youtube.com/playlist?list=PL0lo9MOBetEHvO-spzKBAITkkTqv4RvNl)
* [GitHub Copilot CLI for Beginners: Getting started with GitHub Copilot CLI](https://github.blog/ai-and-ml/github-copilot/github-copilot-cli-for-beginners-getting-started-with-github-copilot-cli/)
* [GitHub Copilot CLI 101: how to use GitHub Copilot from the command line](https://github.blog/ai-and-ml/github-copilot-cli-101-how-to-use-github-copilot-from-the-command-line/?utm_source=blog-announcement-cli-tutorial&utm_medium=blog&utm_campaign=universe25post)
* [Best practices for GitHub Copilot CLI](https://docs.github.com/copilot/how-tos/copilot-cli/cli-best-practices?utm_campaign=copilot-brand&utm_medium=sem&utm_source=google&ocid=AIDcmmh2h80ugd_SEM__k_CjwKCAjw-dfOBhAjEiwAq0RwI0TIeyL9bjDmXlY26JKPbDvHGzBcaZUa4LR8u8SJuGbIke6e7U2YXRoCzGQQAvD_BwE_k_cli?utm_source=blog-cli-beginners-ep2-features-cta&utm_medium=blog&utm_campaign=dev-pod-copilot-cli-2026)

---

## Tags:

* [GitHub Copilot CLI](https://github.blog/tag/github-copilot-cli/)
* [GitHub Copilot CLI for Beginners](https://github.blog/tag/github-copilot-cli-for-beginners/)

## Written by

![Kayla Cinnamon](https://avatars.githubusercontent.com/u/48369326?v=4&s=200)

### [Kayla Cinnamon](https://github.blog/author/cinnamonmsft/)

[@cinnamon-msft](https://github.com/cinnamon-msft)

Senior AI Developer Tools Advocate
