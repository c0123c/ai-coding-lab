# A guide to slash commands in the GitHub Copilot app

If you’ve used slash commands in the GitHub Copilot CLI, you already know how powerful a quick `/` can be. In the GitHub Copilot app, slash commands take that idea further, giving you shortcuts for managing sessions, navigating projects, and customizing your Copilot workflow.

## What are slash commands?

Slash commands are text shortcuts you type directly into the GitHub Copilot app’s chat composer. Start by typing `/` and an autocomplete menu appears, showing the commands available in your current context. It’s a small character with a lot of potential, opening the door to shortcuts that help you work with Copilot in new ways.

If you’re coming from the CLI, here’s the key difference: CLI slash commands are designed around a terminal-first workflow. Things like adding directories, setting your working directory, and managing terminal access happen through commands. This makes sense, because the CLI lives inside your terminal where there’s no visual interface.

|  |
| --- |
| 💡 **Tip**: If you’ve used slash commands in the Copilot CLI, you’ll notice some familiar faces. Commands like `/clear` and `/model` work in both places. But the GitHub Copilot app-specific commands are tailored for the multi-session workflow that the desktop app provides. |

The app, on the other hand, provides a visual interface for managing context. File access commands like `/add-dir` or `/cwd` aren’t needed since the app manages project context automatically. App slash commands are more about workflows. You can navigate between sessions, manage projects, and control how the agent works.

## Why use slash commands?

Slash commands may look like simple shortcuts, but they can change how you interact with Copilot. They help you move faster, stay focused, and quickly access the workflows you need. Instead of digging through options or breaking your focus to find the right tool, you can type a command and keep moving. A single `/` opens up the list of slash commands that can help you move faster, explore new ideas, and get the most out of the app.

Let’s take a look at some of the slash commands available in the GitHub Copilot app and how they can fit into your everyday workflows.

## Before you write code, make a `/plan`

Good code starts with a good plan. `/plan` helps you break down a task before you start writing code, think through your approach, identify potential challenges, and decide what needs to happen next. It also switches your session into **Plan** mode, which you can also select from the **Mode** dropdown in the chat composer.

* **Plan a new feature.** Break down new feature ideas before jumping into implementation. Have Copilot identify files, components, and dependencies so you have a clearer path forward.
* `/plan I need to add two-factor authentication to our application. Help me break down the work involved, identify what files need to change, and outline an implementation approach.`
* **Prepare for a large refactor.** Map out complex changes before touching your code. Uncover potential risks and develop an incremental approach for making large changes safely.
* `/plan We want to refactor our notification system code to make it easier to support new channels like push notifications. Help me understand the changes needed and create an incremental migration plan.`
* **Triage and fix bugs.** If you know something is wrong but aren’t sure where to start, `/plan` can help you explore possible causes and outline the steps needed to diagnose and resolve the problem.
* `/plan Users are reporting that our checkout flow randomly fails after payment processing. Help me investigate possible causes and create a plan to diagnose and fix the issue.`

## Let Copilot play devil’s advocate with `/spar`

Sometimes the best way to validate an idea is to challenge it. `/spar` is like that one teammate who raises their hand and asks, “Have we thought about what happens when this goes wrong?” It helps you pressure-test your approach by having Copilot question your assumptions and point out potential risks or tradeoffs before you commit to a solution. Here are a few ways you can use it:

* **Validate an architecture choice.** Pitch your plan to use Redis for caching and have Copilot question your invalidation strategy, scalability, or whether another approach better fits your workload.
* `/spar I'm planning to use Redis as a caching layer for our product API. Challenge my approach and point out any scalability or consistency concerns I may have missed.`
* **Compare implementation options.** Ask Copilot to debate the pros and cons of REST versus GraphQL, or synchronous versus asynchronous processing, based on your application’s requirements.
* `/spar Help me decide between REST and GraphQL for a customer-facing API. Ask questions, challenge my assumptions, and recommend which approach fits best for an app with mobile clients.`
* **Review a migration plan.** Walk through a database migration or infrastructure change and have Copilot identify edge cases, risks, or rollout concerns before you begin.
* `/spar I'm migrating our database to a new managed service with minimal downtime. Poke holes in my migration plan and identify any risks or edge cases I should account for.`
* **Challenge a performance optimization.** Share an optimization you’re considering and ask Copilot to point out hidden bottlenecks, unintended side effects, or simpler alternatives.
* `/spar I'm planning to lazy load most of the components on my site to improve initial load time. Critique my approach and tell me where it could hurt user experience or introduce unnecessary complexity.`

## `/autopilot` take the wheel

Once you have a `/plan`, the next step is turning that idea into working code. `/autopilot` helps you work through implementation, make changes, and iterate as needed. Instead of managing each individual step, give Copilot a goal and let it work through the steps needed to complete the task. It also switches your session into **Autopilot** mode, which you can also select from the **Mode** dropdown in the chat composer.

* **Implement a new feature.** Hand off a task and let Copilot work through the implementation steps.
* `/autopilot Add support for exporting user reports as CSV files. Identify the files that need changes, implement the feature, and update any relevant tests.`
* **Complete a larger maintenance task.** Use `/autopilot` for tasks that require multiple steps, such as updating dependencies, refactoring code, or improving documentation.
* `/autopilot Update this project to the latest version of React. Identify breaking changes, update the code where needed, and make sure the test suite passes.`

## Talk it through with `/rubber-duck`

I’ve learned from personal experience that talking things through with your cat isn’t always helpful. Their listening skills are questionable at best, and their debugging advice usually ends with them sitting on my keyboard or chewing my wires. `/rubber-duck` gives you something even better: a fresh set of eyes.

It uses a different model to independently review your work, helping surface blind spots, question assumptions, and catch issues your primary model may have missed. It’s especially useful for complex refactors, architectural decisions, migration plans, or any time you want a second opinion before moving forward.

* **Get a second opinion on a plan.** Before you start implementing a complex feature, have a different model independently review your plan and point out assumptions, missing steps, or potential risks.  
  `/rubber-duck Review the implementation plan we've created for adding two-factor authentication. Identify any blind spots, edge cases, or risks that we may have overlooked.`
* **Review a large refactor.** After making significant changes, ask /rubber-duck to critique the approach and highlight anything that could be simplified, improved, or handled differently.  
  `/rubber-duck Review the refactoring we've completed for the notification system. Look for architectural concerns, unnecessary complexity, or areas that could be improved before I open a pull request.`
* **Validate a migration strategy.** Before rolling out a complex migration, use a second model to independently evaluate your approach and identify rollout or reliability concerns.  
  `/rubber-duck Review our database migration plan and implementation. Point out any blind spots, rollback concerns, or edge cases we should address before deployment.`

## Turn conversations into interactive experiences with `/create-canvas`

Not every problem is best solved through a chat window. `/create-canvas` lets you create interactive interfaces directly from a conversation with Copilot. Instead of working through information in a long chat, you can turn it into a visualization, dashboard, or custom workflow you can interact with. For example:

* `/create-canvas Create an interactive diagram showing how services in this application connect.`
* `/create-canvas Create an issue triage board that lets me review and categorize open issues.`

For more information and examples on creating canvases, [check out our blog post](https://github.blog/ai-and-ml/github-copilot/how-to-build-interactive-experiences-with-canvases/).

## When one task becomes many, use `/orchestrate`

Not every task fits neatly into a single workflow. Sometimes you need to make changes across multiple repositories or tackle several related tasks at once. `/orchestrate` helps you coordinate work across sessions and repositories by breaking larger efforts into smaller tasks that can move forward in parallel.

* **Coordinate changes across repositories.** When a feature touches multiple codebases, `/orchestrate` can help you track the work and coordinate updates across each repository.
* `/orchestrate I need to add support for a new authentication flow across our frontend, backend, and shared repositories. Help me break down the work and coordinate the changes needed in each codebase.`
* **Manage parallel development tasks.** For larger projects, Copilot can divide the work into focused efforts so multiple pieces can move forward together.
* `/orchestrate Prepare this feature for release. Identify the work needed for implementation, testing, documentation, and deployment, then help coordinate each task.`

## Your next workflow starts with `/`

You don’t need to memorize any commands to get started. Pick a few slash commands that match how you work and build from there. The autocomplete menu has your back for the rest. Type `/` and start exploring! You can also [read our docs](https://docs.github.com/copilot/concepts/agents/github-copilot-app) to learn more about the GitHub Copilot app.

[Try slash commands in the GitHub Copilot app](https://github.com/features/ai/github-app), or [check out our docs](https://docs.github.com/copilot/reference/github-copilot-app-reference/slash-commands) for a list of available commands.

---

## Tags:

* [GitHub Copilot](https://github.blog/tag/github-copilot/)
* [GitHub Copilot app](https://github.blog/tag/github-copilot-app/)
* [slash commands](https://github.blog/tag/slash-commands/)

## Written by

![Jacklyn Carroll](https://avatars.githubusercontent.com/u/13090072?v=4&s=200)

### [Jacklyn Carroll](https://github.blog/author/jacklynlee31/)

[@jacklynlee31](https://github.com/jacklynlee31)

Jacklyn is a Content Writer at GitHub and a technical writer with a love for good storytelling.
