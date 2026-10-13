# GitHub Copilot tutorial: How to build, test, review, and ship code faster (with real prompts)

If you haven’t used [GitHub Copilot](https://github.com/features/copilot?utm_source=blog-copilot-feature&utm_medium=blog&utm_campaign=universe25post) since before mission control launched, you haven’t experienced what it can do now.

Copilot used to be an autocomplete tool. Now, it’s a full AI coding assistant that can run multi-step workflows, fix failing tests, review pull requests, and ship code—directly inside VS Code or GitHub.

Back in 2021, Copilot changed how you *edited* code. [Today with Agent HQ and mission control, it’s changing how you build, review, secure and ship software](https://github.blog/news-insights/company-news/welcome-home-agents/).

Here’s one example:

```
// Before
"Write tests for this module" = manual setup, fixtures, and edge cases

// Now
Ask Copilot: "Generate Jest tests for userSessionService with cache-enabled branch coverage"
Full test suite + explanations in record time
```

Under the hood, Copilot runs on multiple models tuned for reasoning, speed, and code understanding. It can see more of your project, generate more accurate results, and move naturally between your editor, terminal, and GitHub.

This guide walks through every part of the new Copilot experience with working examples, best practices, and prompts you can try right now (which you should).

## What’s new with Copilot

### Larger context + cross-file reasoning (now surfaced through mission control)

Early versions of Copilot saw only what you were typing. Now, it can read across multiple files, helping it understand intent and relationships between modules.

**Ask**: In mission control: “Find every function using outdated crypto libraries and refactor them to the new API. Open a draft PR.”

Copilot can trace patterns across your codebase, make updates, and explain what changed.

### You can choose the right model for the job

You can now choose models based on your needs: one optimized for speed when prototyping, another for deeper reasoning during complex refactors.

### It goes beyond code completion

Copilot is now a suite of tools built for every step of the workflow:

* [**Mission control**](https://github.blog/changelog/2025-10-28-a-mission-control-to-assign-steer-and-track-copilot-coding-agent-tasks/)**:** Run multi-step tasks, generate tests, and open pull requests.
* [**Agent mode**](https://github.blog/ai-and-ml/github-copilot/agent-mode-101-all-about-github-copilots-powerful-mode/?utm_campaign=copilot-generic-promotion-blog-launch-oct-2025&utm_source=blog-prom-release-oct-2025)**:** Define the outcome, and Copilot determines the best approach seeking feedback from you as needed, testing its own solutions, and refining its work in real time.
* [**Copilot CLI**](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/use-copilot-cli)**:** Automate and explore your repository directly from the terminal.
* [**Coding agent**](https://docs.github.com/en/copilot/concepts/agents/coding-agent/about-coding-agent)**:** Offload routine fixes or scaffolding to Copilot.
* [**Code review**](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/request-a-code-review/use-code-review)**:** Let Copilot highlight risky diffs or missing tests before you merge.
* **Scoped agents:** Offload routine fixes, refactors, docs, or test generation.

## How to use GitHub Copilot **(with examples)**

Here are actionable items for each mode of Copilot, with code snippets and prompt examples.

### Build faster with mission control and agent mode in VS Code

[![](https://github.blog/wp-content/uploads/2025/11/Screenshot-2025-11-04-at-2.22.51-PM.png)](https://github.blog/wp-content/uploads/2025/11/Custom-agent-mission-control-CAPS.mp4)

Once you’ve installed the Copilot extension, [enable *agent mode* in settings](https://github.blog/ai-and-ml/github-copilot/agent-mode-101-all-about-github-copilots-powerful-mode/?utm_campaign=copilot-generic-promotion-blog-launch-oct-2025&utm_source=blog-prom-release-oct-2025) and open mission control from the sidebar. Start by selecting a workflow (tests, refactor, documentation) or run a custom prompt.

**Prompt pattern:**

```
# Add caching to userSessionService to reduce DB hits
```

In mission control: “Add a Redis caching layer to userSessionService, generate hit/miss tests, and open a draft PR.”

Copilot will create a new file, update the service, add tests, and open a draft pull request with a summary of changes.

**Tip:** Write comments that explain *why*, not just *what*.

```
// Cache responses by userId for 30s to reduce DB hits >1000/min
```

Short, specific comments make Copilot work better.

### Break into the terminal with Copilot CLI

[![](https://github.blog/wp-content/uploads/2025/11/Screenshot-2025-11-04-at-2.38.31-PM.png)](https://github.blog/wp-content/uploads/2025/11/GitHUb-Copilot-CLI-final-with-caps.mp4)

[Copilot CLI brings the same intelligence to your terminal](https://github.com/github/copilot-cli?utm_source=blog-prom-release-oct-2025&utm_campaign=copilot-generic-promotion-blog-launch-oct-2025).  To install it, use the following command in your terminal:

```
npm install -g @github/copilot-cli
copilot /login
```

Once installed and authenticated:

```
npm install -g @github/copilot-cli
copilot /login
```

Then run:

```
copilot explain .
```

You’ll get a structured summary of your repository, dependencies, test coverage, and potential issues.

**Here are some common, useful commands:**

```
copilot explain .
copilot fix tests
copilot setup project
copilot edit src/**/*.py
```

**Try this:**

After a failing CI run, use the following command to have Copilot locate the issue, explain why it’s failing, and propose a fix for review.

```
copilot fix tests
```

### **Use Copilot code review**

Copilot can now review pull requests directly in GitHub—no plugins required. It identifies risky diffs, missing test coverage, and potential bugs.

Enable [Copilot code review](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/request-a-code-review/use-code-review?utm_campaign=copilot-generic-promotion-blog-launch-oct-2025&utm_source=blog-prom-release-oct-2025) via your repository settings to get started.

When a pull request is created, Copilot can comment on:

* Missing test coverage
* Potential bug/edge-case
* Security vulnerabilities

**Here’s an example**

In your pull request chat, try writing:

```
Summarize the potential risks in this diff and suggest missing test coverage.
```

Copilot will reply inline with notes you can accept or ignore. It’s not here to merge for you. It’s here to help you think through issues and concepts faster.

### Setting up async tasks with Copilot coding agent

[Copilot coding agent](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/coding-agent?utm_campaign=copilot-generic-promotion-blog-launch-oct-2025&utm_source=blog-prom-release-oct-2025) can take a structured issue, write code, and open a draft pull request—all asynchronously.

**Here’s an example issue:**

```
### Feature Request: CSV Import for User Sessions  
- File: import_user_sessions.py  
- Parse CSV with headers userId, timestamp, action  
- Validate: action in {login, logout, timeout}  
- Batch size: up to 10k rows  
- On success: append to session table  
- Include: tests, docs, API endpoint
```

Assign that issue to Copilot. It will clone the repo, implement the feature, and open a draft pull request for your review.

**Coding agent is best for:**

* Repetitive refactors
* Boilerplate or scaffolding
* Docs and test generation

You always review before merge, but Copilot accelerates everything leading up to it.

### Best practices and guardrails

* **Review everything.** AI writes code; you approve it. Always check logic, style, docs before you ship.
* **Prompt with context.** The better your prompt (why, how, constraints), the better the output.
* **Use small increments.** For agent mode or CLI edits, do one module at a time. Avoid “rewrite entire app in one shot.”
* **Keep developers in the loop.** Especially for security, architecture, design decisions.
* **Document prompts and decisions.** Maintain a log: “Used prompt X, result good/bad, adjustments made”. This helps refine your usage.
* **Build trust slowly.** Use Copilot for non-critical paths first (tests, refactors), then expand to core workflows.
* **Keep context limits in mind.** Although Copilot handles more context now, extremely large monolithic repos may still expose limitations.

## Why this matters

[More than 36 million developers joined GitHub this year](https://github.blog/news-insights/octoverse/octoverse-a-new-developer-joins-github-every-second-as-ai-leads-typescript-to-1/?utm_source=octoverse-homepage&utm_medium=blog&utm_campaign=universe25) (that’s more than one every second!), and 80% used Copilot in their first week.

AI-powered coding is no longer experimental. It’s part of the job.

[Typed languages like TypeScript and Python dominate GitHub today](https://github.blog/news-insights/octoverse/octoverse-a-new-developer-joins-github-every-second-as-ai-leads-typescript-to-1/?utm_source=blog-prom-release-oct-2025&utm_medium=blog&utm_campaign=copilot-generic-promotion-blog-launch-oct-2025#the-top-programming-languages-of-2025-typescript-jumps-to-1-while-python-takes-2), and their structure makes them ideal partners for Copilot. Strong types plus smart suggestions equals faster feedback loops and fewer regressions.

And now with mission control, everything’s in one place. You don’t need five AI tools, ten browser tabs, or a separate review bot. Everything happens where you already build software.

## Take this with you

If you’ve been waiting to see what Copilot can really do, mission control is the moment.

With GitHub Copilot—in the editor, in your terminal, in your reviews, and in the background of your team—you’re getting a toolkit designed to help you do real work faster, smarter, and on your terms.

You decide the architecture. You write the tests (or at least the ones you want to write). You merge the pull requests. Copilot helps with boilerplate, scaffolding, and routine tasks so you can keep your focus on the problem that really matters.

Pick one part of your stack this week—tests, docs, refactor—and run it through mission control. See where it saves time, then scale up.

This guide is your map. The tools are in your hands. Now it’s your turn to build.

Start using [GitHub Copilot >](https://github.com/features/copilot?utm_source=blog-copilot-feature&utm_medium=blog&utm_campaign=universe25post)

---

## Tags:

* [GitHub Copilot](https://github.blog/tag/github-copilot/)

## Written by

![Aaron Winston](https://avatars.githubusercontent.com/u/87450290?v=4&s=200)

### [Aaron Winston](https://github.blog/author/aaronwinston/)

[@aaronwinston](https://github.com/aaronwinston)

Aaron helps lead content strategy at GitHub with a focus on everything developers need to know to stay ahead of what's next. Also, he still likes the em dash despite its newfound bad rap.
