# How to orchestrate agents using mission control

We recently shipped [Agent HQ’s mission control](https://github.blog/changelog/2025-10-28-a-mission-control-to-assign-steer-and-track-copilot-coding-agent-tasks/), a unified interface for managing [GitHub Copilot](https://github.com/features/copilot?utm_source=blog-mission-control&utm_medium=blog&utm_campaign=universe25post) coding agent tasks.

Now, you can now assign tasks to Copilot across repos, pick a custom agent, watch real‑time session logs, steer mid-run (pause, refine, or restart), and jump straight into the resulting pull requests—all in one place. Instead of bouncing between pages to see status, rationale, and changes, mission control centralizes assignment, oversight, and review.

Having the tool is one thing. Knowing how to use it effectively is another. This guide shows you how to orchestrate multiple agents, when to intervene, and how to review their work efficiently. Being great at orchestrating agents means unblocking parallel work in the same timeframe you’d spend on one task, stepping in when logs show drift, tests fail, or scope creeps.

## The mental model shift

### From sequential to parallel

If you’re already used to working with an agent one at a time, you know it’s inherently sequential. You submit a prompt, wait for a response, review it, make adjustments, and move to the next task.

Mission control changes this. You can kick off multiple tasks in minutes—across one repo or many. Previously, you’d navigate to different repos, open issues in each one, and assign Copilot separately. Now you can enter prompts in one place, and Copilot coding agent goes to work across all of them.

That being said, there is a trade-off to keep in mind: Instead of each task taking30 seconds to a few minutes to complete, your agents might spend a few minutes to an hour on a draft. But you’re no longer just waiting. You’re orchestrating.

### When to stay sequential

Not everything belongs in parallel. Use sequential workflows when:

* Tasks have dependencies
* You’re exploring unfamiliar territory
* Complex problems require validating assumptions between steps

When assigning multiple tasks from the same repo, consider overlap. Agents working in parallel can create merge conflicts if they touch the same files. Be thoughtful about partitioning work.

Tasks that typically run well in parallel:

* Research work (finding feature flags, configuration options)
* Analysis (log analysis, performance profiling)
* Documentation generation
* Security reviews
* Work in different modules or components

## Tips for getting started

The shift is simple: you move from waiting on a single run to overseeing multiple progressing in parallel, stepping in for failed tests, scope drift, or correcting unclear intent where guidance will save time.

### Write clear prompts with context

Specificity matters. Describe the task precisely. Good context remains critical for good results.

Helpful context includes:

* Screenshots showing the problem
* Code snippets illustrating the pattern you want
* Links to relevant documentation or examples

**Weak prompt**: “Fix the authentication bug.”

**Strong prompt**: “Users report ‘Invalid token’ errors after 30 minutes of activity. JWT tokens are configured with 1-hour expiration in auth.config.js. Investigate why tokens expire early and fix the validation logic. Create the pull request in the api-gateway repo.”

### Use custom agents for consistency

Mission control lets you select custom agents that use agents.md files from your selected repo. These files give your agent a persona and pre-written context, removing the burden of constantly providing the same examples or instructions.

If you manage repos where your team regularly uses agents, consider creating agents.md files tailored to your common workflows. This ensures consistency across tasks and reduces the cognitive load of crafting detailed prompts each time.

Once you’ve written your prompt and selected your custom agent (if applicable), kick off the task. Your agent gets to work immediately.

## Tips for active orchestration

You’re now a conductor of agents. Each task might take a minute or an hour, depending on complexity. You have two choices: watch your agents work so you can intervene if needed, or step away and come back when they’re done.

### Reading the signals

Below are some common indicators that your agent is not on the right track and needs additional guidance:

* **Failing tests, integrations, or fetches**: The agent can’t fetch dependencies, authentication fails, or unit tests break repeatedly.
* **Unexpected files being created**: Files outside the scope appear in the diff, or the agent modifies shared configuration.
* **Scope creep beyond what you requested**: The agent starts refactoring adjacent code or “improving” things you didn’t ask for.
* **Misunderstanding your intent**: The session log reveals the agent interpreted your prompt differently than you meant.
* **Circular behavior**: The agent tries the same failing approach multiple times without adjusting.

When you spot issues, evaluate their severity. Is that failing test critical? Does that integration point matter for this task? The session log typically shows intent before action, giving you a chance to intervene if you’re monitoring.

### The art of steering

When you need to redirect an agent, be specific. Explain why you’re redirecting and how you want it to proceed.

**Bad steering**: “This doesn’t look right.”

**Good steering**: “Don’t modify database.js—that file is shared across services. Instead, add the connection pool configuration in api/config/db-pool.js. This keeps the change isolated to the API layer.”

Timing matters. Catch a problem five minutes in, and you might save an hour of ineffective work. Don’t wait until the agent finishes to provide feedback.

You can also stop an agent mid-task and give it refined instructions. Restarting with better direction is simple and often faster than letting a misaligned agent continue.

### Why session logs matter

Session logs show reasoning, not just actions. They reveal misunderstandings before they become pull requests, and they improve your future prompts and orchestration practices. When Copilot says “I’m going to refactor the entire authentication system,” that’s your cue to steer.

## Tips for the review phase

When your agents finish, you’ll have pull requests to review. Here’s how to do it efficiently. Ensure you review:

1. **Session logs**: Understand what the agent did and why. Look for reasoning errors before they become merged code. Did the agent misinterpret your intent? Did it assume something incorrectly?
2. **Files changed**: Review the actual code changes. Focus on:
   * Files you didn’t expect to see modified
   * Changes that touch shared, risky, or critical code paths
   * Patterns that don’t match your team’s standards/practices
   * Missing edge case handling
3. **Checks**: Verify that tests pass (your unit tests, Playwright, CI/CD, etc.). When checks fail, don’t just restart the agent. Investigate why. A failing test might reveal the agent misunderstood requirements, not just wrote buggy code.

This pattern gives you the full picture: intent, implementation, and validation.

### Ask Copilot to review its own work

After an agent completes a task, ask it:

* “What edge cases am I missing?”
* “What test coverage is incomplete?”
* “How should I fix this failing test?”

Copilot can often identify gaps in its own work, saving you time and improving the final result. Treat it like a junior developer who’s willing to explain their reasoning.

### Batch similar reviews

Generating code with agents is straightforward. Reviewing that code—ensuring it meets your standards, does what you want, and that it can be maintained by your team—still requires human judgment.

Improve your review process by grouping similar work together. Review all API changes in one session. Review all documentation changes in another. Your brain context-switches less, and you’ll spot patterns and inconsistencies more easily.

## What’s changed for the better

Mission control moves you from babysitting single agent runs to orchestrating a small fleet. You define clear, scoped tasks. You supply just enough context. You launch several agents. The speed gain is not that each task finishes faster; it’s that you unblock more work in the same timeframe.

What makes this possible is discipline: specific prompts, not vague requests. Custom agents in agents.md that carry your patterns so you don’t repeat yourself. Early steering when session logs show drift. Treating logs as reasoning artifacts you mine to write a sharper next prompt. And batching reviews so your brain stays in one mental model long enough to spot subtle inconsistencies. Lead your own team of agents to create something great!

Ready to start? [Visit mission control](https://github.com/copilot/agents?utm_source=blog-mission-control&utm_medium=blog&utm_campaign=universe25post) or [learn more about GitHub Copilot](https://github.com/features/copilot?utm_source=blog-mission-control&utm_medium=blog&utm_campaign=universe25post) for your organization.

---

## Tags:

* [AI agents](https://github.blog/tag/ai-agents/)
* [GitHub Copilot](https://github.blog/tag/github-copilot/)
* [GitHub Copilot coding agent](https://github.blog/tag/github-copilot-coding-agent/)
* [mission control](https://github.blog/tag/mission-control/)

## Written by

![Matt Nigh](https://avatars.githubusercontent.com/u/5862416?v=4&s=200)

### [Matt Nigh](https://github.blog/author/mattnigh/)

[@mattnigh](https://github.com/mattnigh)

Program Manager Director, I lead the AI for Everyone program at GitHub.
