# What’s new with GitHub Copilot coding agent

You open an issue before lunch. By the time you’re back, there’s a pull request waiting.

That’s what [GitHub Copilot coding agent](https://docs.github.com/en/copilot/concepts/agents/coding-agent/about-coding-agent) is built for. It works in the background, fixing bugs, adding tests, cleaning up debt, and comes back with a pull request when it’s done. While you’re writing code in your editor with Copilot in real time, the coding agent is handling the work you’ve delegated.

A few recent updates make that handoff more useful. Here’s what shipped and how to start using it.

*Visual learner? Watch the video above! ☝️*

## Choose the right model for each task

The Agents panel now includes a model picker.

Before, every background task ran on a single default model. You couldn’t pay for a more robust model to complete harder work or prioritize speed on routine tasks.

Now you can. Use a faster model for straightforward work like adding unit tests. Upgrade your model for a gnarly refactor or integration tests with real edge cases. If you’d rather not think about it, leave it on auto.

**To get started:**

* Open the Agents panel (top-right in GitHub), select your repo, and pick a model.
* Write a clear prompt and kick off the task.
* Leave the model on auto if you’d rather let GitHub choose.

Model selection is available for Copilot Pro and Pro+ users now, with support for Business and Enterprise coming soon.

[Learn more about model selection with Copilot coding agent. 👉](https://docs.github.com/copilot/how-tos/use-copilot-agents/coding-agent/changing-the-ai-model)

## Pull requests that arrive in better shape

The painful part of reviewing agent output has always been the cleanup. You open the diff and there it is: logic that technically works, but nobody would write it that way.

Copilot coding agent now reviews its own changes using Copilot code review before it opens the pull request. It gets feedback, iterates, and improves the patch. By the time you’re tagged for review, someone already went through it.

[In one session](https://youtu.be/S1ch_6fjp5M?t=212&si=wVXe0uClUTohv1qW), the agent caught that its own string concatenation was overly complex and fixed it before the pull request landed. That kind of thing used to be your problem.

**To get started:**

* Assign an issue to Copilot or create a task from the Agents panel.
* Click into the task to view the logs.
* See the moments where the agent ran Copilot code review and applied feedback.

Review the pull request when prompted. Copilot requests your review only after it has iterated.

[Learn more about Copilot code review + Copilot coding agent. 👉](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/request-a-code-review/use-code-review)

## Security checks that run while the agent works

Just like with human-generated code, AI-generated code can introduce real risks: vulnerable patterns, secrets accidentally committed, dependencies with known CVEs. The difference is it does it faster. And you really don’t want to find that in review.

Copilot coding agent now runs code scanning, secret scanning, and dependency vulnerability checks directly inside its workflow. If a dependency has a known issue, or something looks like a committed API key, it gets flagged before the pull request opens.

Code scanning is normally part of GitHub Advanced Security. With Copilot coding agent, you get it for free.

**To get started:**

* Run any task through the Agents panel.
* Check the session logs as it runs. You’ll see scanning entries as the agent works.
* Review the pull request. It’s already been through the security filter.

[Learn more about security scanning in Copilot coding agent. 👉](https://github.blog/changelog/2025-10-28-copilot-coding-agent-now-automatically-validates-code-security-and-quality/)

## Custom agents that follow your team’s process

A short prompt leaves a lot to judgment. And that judgment isn’t always consistent with how your team actually works.

Custom agents let you codify it. Create a file under `.github/agents/` and define a specific approach. A performance optimizer agent, for example, can be wired to benchmark first, make the change, then measure the difference before opening a pull request.

In a [recent GitHub Checkout demo](https://youtu.be/S1ch_6fjp5M?t=400&si=xvLSQSMWzdapU5Hf), that’s exactly what happened. The agent benchmarked a lookup, made a targeted fix, and came back with a 99% improvement on that one function. Small scope, real data, no guessing.

You can share custom agents across your org or enterprise too, so the same process applies everywhere teams are using the coding agent.

**To get started:**

* Create an agent file under `.github/agents/` in your repo.
* Open the Agents panel and start a new task.
* Select your custom agent from the options.
* Write a prompt scoped to what that agent does.

[Learn more about creating custom agents. 👉](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/coding-agent/create-custom-agents?utm_source=chatgpt.com)

## Move between cloud and local without losing context

Sometimes you start something in the cloud and want to finish it locally. Sometimes you’re deep in your terminal and want to hand something off without losing your flow. Either way, switching contexts used to mean starting the conversation over.

Now it doesn’t. Pull a cloud session into your terminal and you get the branch, the logs, and the full context. Or press `&` in the CLI to push work back to the cloud and keep going on your end.

**To get started:**

* Start a task with Copilot coding agent and wait for the session to appear.
* Click “Continue in Copilot CLI” and copy the command.
* Paste it in your terminal to load the session locally with branch, logs, and context intact.
* Press the ampersand symbol (`&`) in the CLI to delegate work back to the cloud and keep going locally.

[Learn more about Copilot coding agent + CLI handoff. 👉](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/manage-agents)

## What this adds up to

Copilot coding agent has come a long way. Model selection, self-review, security scanning, custom agents, CLI handoff—and that’s just what shipped recently. The team is actively working on private mode, planning before coding, and using the agent for things that don’t even need a pull request, like summarizing issues or generating reports. There’s a lot more coming. Stay tuned.

Share feedback on what ships next in [GitHub Community discussions](https://github.com/orgs/community/discussions/categories/copilot-conversations).

Get started with [GitHub Copilot coding agent >](https://docs.github.com/en/copilot/concepts/agents/coding-agent/about-coding-agent)

---

## Tags:

* [GitHub Checkout](https://github.blog/tag/github-checkout/)
* [GitHub Copilot](https://github.blog/tag/github-copilot/)
* [GitHub Copilot coding agent](https://github.blog/tag/github-copilot-coding-agent/)

## Written by

![Andrea Griffiths](https://github.blog/wp-content/uploads/2025/08/Andrea-Griffiths_avatar_1755783168-200x200.jpeg)

### [Andrea Griffiths](https://github.blog/author/andreagriffiths11/)

[@AndreaGriffiths11](https://github.com/andreagriffiths11)

Andrea is a Senior Developer Advocate at GitHub with over a decade of experience in developer tools. She combines technical depth with a mission to make advanced technologies more accessible. After transitioning from Army service and construction management to software development, she brings a unique perspective to bridging complex engineering concepts with practical implementation. She lives in Florida with her Welsh partner, two sons, and two dogs, where she continues to drive innovation and support open source through GitHub's global initiatives. Find her online @acolombiadev.
