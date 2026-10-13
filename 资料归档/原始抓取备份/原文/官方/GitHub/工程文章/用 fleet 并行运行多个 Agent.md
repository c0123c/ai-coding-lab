# Run multiple agents at once with /fleet in Copilot CLI

What if [GitHub Copilot CLI](https://github.com/features/copilot/cli?utm_source=blog-cli-fleet-agents-cta&utm_medium=blog&utm_campaign=dev-pod-copilot-cli-2026) could work on five files at the same time instead of one? That’s where `/fleet` comes in.

`/fleet` is a slash command in Copilot CLI that enables Copilot to simultaneously work with multiple subagents in parallel. Instead of working through tasks sequentially, Copilot now has a behind the scenes orchestrator that plans and breaks your objective into independent work items and dispatches multiple agents to execute them simultaneously. On different files, in different parts of your codebase, all at once.

Want to learn more about how `/fleet` works, and more importantly, how to use it most effectively? Let’s jump in.

## How it works

When you run `/fleet` with a prompt, the behind-the-scenes orchestrator:

1. Decomposes your task into discrete work items with dependencies.
2. Identifies which items can run in parallel versus which must wait.
3. Dispatches independent items as background sub-agents simultaneously.
4. Polls for completion, then dispatches the next wave.
5. Verifies outputs and synthesizes any final artifacts.

Each sub-agent gets its own context window but shares the same filesystem. They can’t talk to each other directly; only the orchestrator coordinates.

Think of it as a project lead who assigns work to a team, checks in on progress, and assembles the final deliverable.

## Getting started

Start fleet mode by sending `/fleet <YOUR OBJECTIVE PROMPT>`. For example:

```
/fleet Refactor the auth module, update tests, and fix the related docs in the folder docs/auth/
```

That’s it. The orchestrator takes your objective, figures out what can be parallelized, and starts dispatching.

You can also run it non-interactively in your terminal:

```
copilot -p "/fleet <YOUR TASK>" --no-ask-user
```

The `--no-ask-user` flag is required for non-interactive mode, since there’s no way to respond to prompts. Now let’s look at what makes a good prompt.

## Write prompts that parallelize well

The quality of your `/fleet` prompt determines how effectively work gets distributed. The key is giving the orchestrator enough structure to cleanly break down your task.

A good way to do that is being specific about deliverables. Map every work item to a concrete artifact like a file, a test suite, or a section of documentation. Vague prompts lead to sequential execution because the orchestrator can’t identify independent pieces.

For example, instead of: `/fleet Build the documentation`, you could try:

```
/fleet Create docs for the API module: 

- docs/authentication.md covering token flow and examples 

- docs/endpoints.md with request/response schemas for all REST endpoints 

- docs/errors.md with error codes and troubleshooting steps 

- docs/index.md linking to all three pages (depends on the others finishing first)
```

The second prompt gives the orchestrator four distinct deliverables, three of which can run in parallel, and one that depends on them.

## Set explicit boundaries

Sub-agents work best when they know exactly where their scope starts and ends. When writing your prompt include:

* File or module boundaries: Which directories or files each track owns
* Constraints: What not to touch (e.g., no test changes, no dependency upgrades)
* Validation criteria: Lint, type checks, tests that must pass

Here’s a prompt that showcases these boundaries:

```
/fleet Implement feature flags in three tracks: 

1. API layer: add flag evaluation to src/api/middleware/ and include unit tests that look for successful flag evaluation and tests API endpoints  

2. UI: wire toggle components in src/components/flags/ and introduce no new dependencies 

3. Config: add flag definitions to config/features.yaml  and validate against schema 

Run independent tracks in parallel. No changes outside assigned directories.
```

## Declare dependencies when they exist

If one piece of work depends on another, say so. The orchestrator will serialize those items and parallelize the rest. For example:

```
/fleet Migrate the database layer: 

1. Write new schema in migrations/005_users.sql 

2. Update the ORM models in src/models/user.ts (depends on 1) 

3. Update API handlers in src/api/users.ts (depends on 2) 

4. Write integration tests in tests/users.test.ts (depends on 2) 

 Items 3 and 4 can run in parallel after item 2 completes.
```

## Use custom agents for different jobs

You can define specialized agents in `.github/agents/` and reference them in your `/fleet` prompt. Each agent can specify its own model, tools, and instructions. Be aware that if you don’t specify which model to use, agents will use the current default model.

```
# .github/agents/technical-writer.md 

--- 

name: technical-writer 

description: Documentation specialist 

model: claude-sonnet-4 

tools: ["bash", "create", "edit", "view"] 

--- 

You write clear, concise technical documentation. Follow the project style guide in /docs/styleguide.md.
```

Then reference the custom agent in your prompt:

```
/fleet Use @technical-writer.md as the agent for all docs tasks and the default agent for code changes.
```

This is useful when different tracks need different strengths such as using a heavier model for complex logic and a lighter one for boilerplate documentation.

## How to verify subagents are deploying

Watch how the orchestrator deploys subagents, it’s the fastest way to learn how to write prompts that parallelize well.

Use this quick checklist:

* Decomposition appears: Before it starts working, review the plan Copilot shares with you to see if it breaks work into multiple tracks, instead of one long linear plan.
* Background task UI confirms activity: Once it begins working, run `/tasks` to open the tasks dialog and inspect running background tasks.
* Parallel progress appears: Updates reference separate tracks moving at the same time.

If the fleet doesn’t seem to be parallelizing, try stopping Copilot’s work and asking for an explicit decomposition:

```
Decompose this into independent tracks first, then execute tracks in parallel. Report each track separately with status and blockers.
```

## Avoiding common pitfalls

Fleet is powerful, but a few gotchas are worth knowing upfront.

### Partition your files

Sub-agents share a filesystem with no file locking. If two agents write to the same file, the last one to finish wins—silently. No error, no merge, just an overwrite.

The fix is to assign each agent distinct files in your prompt. If multiple agents need to contribute to a single file, consider having each write to a temporary path and let the orchestrator merge them at the end. Or set an explicit order for the agents to follow.

### Keep prompts self-contained

Sub-agents can’t see the orchestrator’s conversation history. When the orchestrator dispatches a sub-agent, it passes along a prompt, but that prompt needs to include everything the sub-agent needs. If you’ve already gathered useful context earlier in the session, make sure your `/fleet` prompt includes it (or references files the sub-agents can read).

### Steering a fleet in progress

After dispatching, you can send follow-up prompts to guide the orchestrator:

* `Prioritize failing tests first, then complete remaining tasks.`
* `List active sub-agents and what each is currently doing.`
* `Mark done only when lint, type check, and all tests pass.`

## When to use `/fleet` (and when not to)

`/fleet` shines when your task has natural parallelism—multiple files, independent modules, or separable concerns. It’s particularly effective for:

* Refactoring across multiple files simultaneously.
* Generating documentation for several components at once.
* Implementing a feature that spans API, UI, and tests.
* Running independent code modifications that don’t share state.

For strictly linear, single-file work, regular Copilot CLI prompts are simpler and just as fast. Fleet adds coordination overhead, so it pays off when there’s real work to distribute.

`/fleet` is most useful when you treat it like a team, not a magic trick. Start small. Pick a task with clear outputs, clean file boundaries, and obvious parallelism. See how the orchestrator decomposes the work, where it helps, and where it gets in the way. As you get more comfortable, push it further with larger refactors, multi‑track features, or docs and tests in parallel. The fastest way to learn when `/fleet` pays off is to try it on real work and adjust your prompts based on what you see.

[Get started with GitHub Copilot CLI >](https://github.com/features/copilot/cli?utm_source=blog-cli-fleet-agents-cta&utm_medium=blog&utm_campaign=dev-pod-copilot-cli-2026)

---

## Tags:

* [GitHub Copilot](https://github.blog/tag/github-copilot/)
* [GitHub Copilot CLI](https://github.blog/tag/github-copilot-cli/)

## Written by

![Matt Nigh](https://avatars.githubusercontent.com/u/5862416?v=4&s=200)

### [Matt Nigh](https://github.blog/author/mattnigh/)

[@mattnigh](https://github.com/mattnigh)

Program Manager Director, I lead the AI for Everyone program at GitHub.

![Brian LaFlamme](https://avatars.githubusercontent.com/u/18745491?v=4&s=200)

### [Brian LaFlamme](https://github.blog/author/briancl2/)

[@briancl2](https://github.com/briancl2)

Senior Enterprise Account Executive
