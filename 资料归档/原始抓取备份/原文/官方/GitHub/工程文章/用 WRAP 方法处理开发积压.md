# WRAP up your backlog with GitHub Copilot coding agent

Engineers at GitHub have been using GitHub Copilot coding agent for over a year now, studying through experience where it can help save developers real time and energy. Through our work, we have developed a handy acronym: WRAP, which stands for:

* W – Write effective issues
* R – Refine your instructions
* A – Atomic tasks
* P – Pair with the coding agent

WRAP will help you get the most out of coding agent. For example, you likely have a backlog full of issues that have been tough to prioritize. Perhaps you’ve had to push off some tech debt improvements in favor of shipping a new feature. Or perhaps your development time has been split between fixing customer bugs and larger passion projects. WRAP can help you quickly get up to speed, using coding agent to tackle tasks that you may not have had time for in the past.

## Write effective issues

The first step of WRAP is to make sure you are writing effective issues to assign to GitHub Copilot coding agent. In essence, you are trying to set coding agent up for success by adding context for the agent, just like you would for a new team member. Here are a set of guidelines you should consider:

* **Write an issue as though it’s for someone brand new to the codebase.** If you consider what context a newcomer might need, you’re likely to include enough context for the coding agent to complete the task effectively.
* **Craft a descriptive title that explains where the work is being done.** One of the best things about the coding agent is that you can assign it as many tasks as you want. But assigning many tasks means you might be juggling a lot of pull request reviews! Make sure your issue title clearly describes the nature of your work and where it occurs so you can keep your agent panel easy to navigate.
* **Add examples of what you want.** If you already know what you’d like the coding agent to implement, such as a specific error-handling pattern, include an example in the issue! This additional context will make it much more likely that you’ll get the output you want.

Here are some example issues to get started:

Instead of:

> Update the entire repository to use async/await

Do something like:

> Update the authentication middleware to use the newer async/await pattern, as shown in the example below. Add unit tests for verification of this work, ensuring edge cases are considered.

```
>

async function exampleFunction() {

  let result = await promise;

  console.log(result); //”done!”

}
```

## Refine your instructions

The next step of WRAP is to make sure that you refine the GitHub Copilot custom instructions to improve the results of your coding agent-created pull requests. There are a variety of different custom instructions that you can create, as well as a variety of different cases where it makes sense to use them.

* **Repository custom instructions.** This is a great place to store any information that might be applicable to your repository as a whole. For example, if you have a Go application and prefer specific ways of writing Go code, that should go in your repository custom instructions. Adding to these instructions over time will improve all the responses from any interactions with GitHub Copilot in that repository. [Here’s a guide on how to get started adding these repository custom instructions](https://docs.github.com/en/copilot/how-tos/configure-custom-instructions/add-repository-instructions).
  + **Note**: This is a great first use case for GitHub Copilot coding agent! Try asking it to generate custom repository instructions for your repository.
* **Organization custom instructions.** Much like how you can create a set of GitHub Copilot custom instructions for an entire repository, you can also create a set of GitHub Copilot custom instructions for your entire organization. This is ideal for anything that applies to all repositories in your organization. For example, you may have requirements around tests that should be created for all applications. [Here’s a guide on how to get started adding organization custom instructions](https://docs.github.com/en/copilot/how-tos/configure-custom-instructions/add-organization-instructions).
* **Coding agent custom agents.** Similar to the repository and organization custom instructions, coding agent custom agents can be created with a natural language text file, and are applicable at either the enterprise, organization, or repository level. These are ideal for any repetitive development task that may occur often, but may not be applicable to every single set of changes. For example, you can create an “Integration Agent” for integrating new products with a specific repository. [Here’s a guide on how to get started creating custom agents](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/coding-agent/create-custom-agents).

## Atomic tasks

Coding agent is very good at handling small, atomic, and well-defined tasks. However, it can also be useful for large problems! If you want it to handle a larger problem, the trick is to break that large problem down into multiple, independent, smaller tasks.

For example, you wouldn’t necessarily want to assign an issue to GitHub Copilot asking it to “Rewrite 3 million lines of code from Java to Golang.” That would probably be too large of a scope for a specific task, and reviewing those changes would likely be pretty painful.

Instead, you could break that larger problem into smaller issues for Copilot to tackle:

* Migrate the authentication module to Golang, ensuring all existing unit tests pass.
* Convert the data validation utilities package to Golang while maintaining the same API interface.
* Rewrite the user management controllers to Golang, preserving existing REST endpoints and responses.

By breaking that large problem into smaller atomic tasks, it will be much easier to test and validate the individual parts of the work and much easier to review the individual pull requests.

## Pair with coding agent

When working with coding agent, it’s important to remember its strengths as a coding agent and your strengths as a human. This will lead to less frustrating experiences down the line if coding agent doesn’t perform like you might want or expect it to.

For example, humans are very good at the following:

* **Understanding the “Why:”** Humans are great at knowing why a task was created. They can also determine whether the changes to a task will actually solve the underlying problem that caused the issue to be created in the first place.
* **Navigating ambiguity:** Humans are much better at navigating ambiguity than AI. For example, as a human, you might be able to omit some parts of an issue that you wouldn’t be able to omit for GitHub Copilot to complete it, such as what sorts of tests and telemetry you expect to be done.
* **Cross-system thinking:** Humans are much better at thinking about the implications of changes made in one system on another system. When coding agent completes a particular task in one repository, it’s unlikely to know about how that task will impact other repositories. Considering cross-system implications is a much better task for humans than for GitHub Copilot.

On the other hand, coding agent is very good at the following:

* **Tireless execution:** You can assign ten different tasks to coding agent right now and expect it to work on each of them.
* **Repetitive tasks:** Humans often get bored or miss things when doing repetitive tasks, such as updating naming conventions across many files. GitHub Copilot is great for completing these things, though!
* **Exploring possibilities:** If you’re considering tackling a problem a couple of different ways, you can assign each one of those different ways to coding agent. This gives you a quick idea of how each of those strategies might play out, without using a lot of development resources.ij

## Take this with you

Lingering backlog issues no longer stand a chance when you are equipped with GitHub Copilot and WRAP.

Have a dependency that needs to be updated? Somewhere that you could use more test coverage? New error handling patterns that you’d like to adopt across your codebase? Or perhaps you’d like to get a jumpstart on adding repository instructions and use the GitHub Copilot coding agent to do so?

Use WRAP to wrap up your backlog!

Get started with [GitHub Copilot](https://github.com/features/copilot) >

---

## Tags:

* [agent mode](https://github.blog/tag/agent-mode/)
* [agentic AI](https://github.blog/tag/agentic-ai/)
* [GitHub Copilot](https://github.blog/tag/github-copilot/)

## Written by

![Brittany Ellich](https://avatars.githubusercontent.com/u/25957322?v=4&s=200)

### [Brittany Ellich](https://github.blog/author/brittanyellich/)

[@brittanyellich](https://github.com/brittanyellich)

Brittany is a software engineer at GitHub, working in Platform and Enterprise.

![Jason Etcovitch](https://avatars.githubusercontent.com/u/10660468?v=4&s=200)

### [Jason Etcovitch](https://github.blog/author/jasonetco/)

[@JasonEtco](https://github.com/jasonetco)
