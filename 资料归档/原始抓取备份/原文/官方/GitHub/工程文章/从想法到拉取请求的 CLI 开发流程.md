# From idea to pull request: A practical guide to building with GitHub Copilot CLI

Most developers already do real work in the terminal.

We initialize projects there, run tests there, debug CI failures there, and make fast, mechanical changes there before anything is ready for review. [GitHub Copilot CLI](https://github.com/features/copilot/cli?utm_source=blog-idea-to-pull-request-cta&utm_medium=blog&utm_campaign=dev-pod-copilot-cli-2026) fits into that reality by helping you move from intent to reviewable diffs directly in your terminal—and then carry that work into your editor or pull request.

This blog walks through a practical workflow for using Copilot CLI to create and evolve an application, [based on a new GitHub Skills exercise](https://github.com/skills/create-applications-with-the-copilot-cli). The Skills exercise provides a guided, hands-on walkthrough; this post focuses on *why* each step works and *when* to use it in real projects.

## What Copilot CLI is (and is not)

Copilot CLI is a GitHub-aware coding agent in your terminal. You can describe what you want in natural language, use `/plan` to outline the work before touching code, and then review concrete commands or diffs before anything runs. Copilot may reason internally, but it only executes commands or applies changes after you explicitly approve them.

In practice, Copilot CLI helps you:

* Explore a problem based on your intent
* Propose structured plans using `/plan` (or you can hit `Shift` + `Tab` to enter planning mode), or suggest concrete commands and diffs you can review
* Generate or modify files
* Explain failures where they occur

What it does *not* do:

* Silently run commands or apply changes without your approval
* Replace careful design work
* Eliminate the need for review

You stay in control of what runs, what changes, and what ships.

## Step 1: Start with intent, not scaffolding

Instead of starting by choosing a framework or copying a template, start by stating what you want to build.

From an empty directory, run:

```
copilot
> Create a small web service with a single JSON endpoint and basic tests
```

If you want to generate a proposal in a single prompt instead of entering interactive mode, you can also run:

```
copilot -p "Create a small web service with a single JSON endpoint and basic tests"
```

In the Skills exercise, this pattern is used repeatedly: describe intent first, then decide which suggested commands you actually want to run.

At this stage, Copilot CLI is exploring the problem space. It may:

* Suggest a stack
* Outline files
* Propose setup commands

Nothing runs automatically. You inspect everything before deciding what to execute. This makes the CLI a good place to experiment before committing to a design.

## Step 2: Scaffold only what you’re ready to own

Once you see a direction you’re comfortable with, ask Copilot CLI to help scaffold:

```
> Scaffold this as a minimal Node.js project with a test runner and README
```

This is where Copilot CLI is most immediately useful. It can:

* Create directories and config,
* Wire basic project structure,
* Generate boilerplate you would otherwise type or copy by hand.

Copilot CLI does not “own” the project structure. It suggests scaffolding based on common conventions, which you should treat as a starting point, not a prescription.

The important constraint is that you’re always responsible for the result. Treat the output like code from a teammate: review it, edit it, or discard it.

## Step 3: Iterate at the point of failure

Run your tests directly inside Copilot CLI:

```
Run all my tests and make sure they pass
```

When something fails, ask Copilot about that exact failure in the same session:

```
> Why are these tests failing?
```

If you want a concrete proposal instead of an explanation, try:

```
> Fix this test failure and show the diff
```

This pattern—run (`!command`), inspect, ask, review diff—keeps the agent grounded in real output instead of abstract prompts.

💡**Pro tip:** In practice, `explain` is useful when you want understanding, while `suggest` is better when you want a concrete proposal you can review. [Learn more about slash commands in Copilot CLI in our guide](https://github.blog/ai-and-ml/github-copilot/a-cheat-sheet-to-slash-commands-in-github-copilot-cli/).

## Step 4: Make mechanical or repo-wide changes

Copilot CLI is also well suited to changes that are easy to describe but tedious to execute:

```
> Rename all instances of X to Y across the repository and update tests
```

Because these changes are mechanical and scoped, they’re easy to review and easy to roll back. The CLI gives you a concrete diff instead of a wall of generated text.

## Step 5: Move into your editor when you need to start shaping your code

Eventually, speed matters less than precision.

This is the natural handoff point to your editor or IDE, so it can:

* Reason about edge cases
* Refine APIs
* Make design decisions

Copilot works there too, but the key point is *why* you switch environments. The CLI helps you quickly get to something real. The IDE is where you can shape your code into exactly what you want.

A good rule of thumb:

* **CLI:** use `/plan`, generate a `/diff`, and move quickly with low ceremony
* **IDE:** use `/IDE` when you need to refine logic and make decisions you’ll defend in review
* **GitHub:** commit, open a pull request with the command `/delegate`, and collaborate asynchronously

## Step 6: Ship on GitHub

Once the changes look good, commit and open a pull request which you can do through the Copilot CLI in natural language:

```
Add and commit all files with a applicable descriptive messages, push the changes.
```

```
Create a pull request and add Copilot as a reviewer
```

Now the work becomes durable:

* Reviewable by teammates
* Testable in CI
* Ready for async iteration

This is where Copilot’s value compounds as part of a flow that ends with shipping versus just being a single surface. The Skills exercise intentionally ends here, because this is where Copilot’s value becomes durable: in commits, pull requests, and review (not just suggestions).

## One workflow, three moments

A helpful mental model for Copilot looks like this:

* **CLI**: prove value quickly with low ceremony
* **IDE**: shape and refine your code
* **GitHub**: review, collaborate, and ship

Copilot CLI is powerful precisely because it fits into this system instead of trying to replace it.

## Take this with you

Copilot CLI is most useful when you treat it like a tool for momentum, not a replacement for judgment.

Used well, it helps you move from intent to concrete changes faster: exploring ideas, scaffolding projects, diagnosing failures, and handling mechanical work directly in the terminal. When precision matters, you move into your editor. When the work is ready to share, it lands on GitHub as a pull request—reviewable, testable, and shippable.

That flow matters more than any single command.

If you take one thing away from this guide, it’s this: Copilot works best when it fits naturally into how developers already build software. Start in the CLI to get unstuck or move quickly, slow down in the IDE to make decisions you can stand behind, and rely on GitHub to make the work durable.

Get started with [GitHub Copilot CLI](https://github.com/features/copilot/cli?utm_source=blog-idea-to-pull-request-cta&utm_medium=blog&utm_campaign=dev-pod-copilot-cli-2026) or take the [Skills course >](https://github.com/skills/create-applications-with-the-copilot-cli)

---

## Tags:

* [GitHub Copilot](https://github.blog/tag/github-copilot/)
* [GitHub Copilot CLI](https://github.blog/tag/github-copilot-cli/)
* [GitHub Skills](https://github.blog/tag/github-skills/)

## Written by

![Ari LiVigni](https://avatars.githubusercontent.com/u/4140343?v=4&s=200)

### [Ari LiVigni](https://github.blog/author/arilivigni/)

[@arilivigni](https://github.com/arilivigni)

Senior Service Delivery Engineer, GitHub
