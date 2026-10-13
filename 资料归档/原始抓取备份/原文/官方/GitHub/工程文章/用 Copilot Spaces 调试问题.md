# How to use GitHub Copilot Spaces to debug issues faster

Every developer knows this pain: you open an issue, and before you can write a single line of code, you’re hunting. You’re digging through old pull requests, searching for that design doc from three months ago, trying to remember which file has the security guidelines.

That hunting phase? It takes forever. And it’s not even the actual work. And even if you *want* to bring AI into the picture, [GitHub Copilot](https://github.com/features/copilot?utm_source=blog-copilot-spaces-checkout&utm_medium=blog&utm_campaign=dec25postuniverse) still needs the same thing you do: context. Without it, you get generic answers that don’t understand your codebase.

[GitHub Copilot Spaces](https://docs.github.com/en/copilot/how-tos/provide-context/use-copilot-spaces/create-copilot-spaces) fixes that.

Spaces gives GitHub Copilot the project knowledge it needs—files, pull requests, issues, repos—so its responses are grounded in your actual code, not guesses.

*Are you a visual learner? Watch the full demo below. 👇*

## How to debug issues with spaces:

### 1. Start with an issue

A contributor opened an issue reporting an unsafe usage of `check_call` in your project.

As a maintainer, you might not know the best way to fix it immediately. On your own, you’d start by searching the repo, checking past pull requests, and combing through security guidelines just to figure out where to begin.

With Spaces, you don’t have to do that manually. Create a space, add the issue and the key files or docs, and let Copilot reason across everything at once.

### 2. Create a space for your project

Inside the space, add:

* Design patterns (e.g., `/docs/security/check-patterns.md`, `/docs/design/architecture-overview.md`)
* Security guidelines
* Accessibility recommendations
* The entire repository (for broad coverage) or a curated set of the most relevant files for your specific use case. Spaces work best when you’re intentional about what you include.
* The URL to the issue itself

### 3. Add Instructions for Copilot

Each space includes an **Instructions** panel. This is where you tell Copilot how you want it to work inside your project.

Here are some example instructions that will help with our task at hand:

```
You are an experienced engineer working on this codebase.
Always ground your answers in the linked docs and sources in this space.
Before writing code, produce a 3–5 step plan that includes:
  - The goal
  - The approach
  - The execution steps
Cite the exact files that justify your recommendations.
After I approve a plan, use the Copilot coding agent to propose a PR.
```

These instructions keep Copilot consistent. It won’t hallucinate patterns that don’t exist in your repo because you’ve told it to cite its sources.

### 4. Ask Copilot to debug the issue

With everything set up, ask Copilot: “Help me debug this issue.”

Copilot already knows which issue you mean because it’s linked to the space. It parses through all the sources, then returns a clear plan:

**Goal:** Fix unsafe usage of `runBinaryCheck` to ensure input paths are validated.

**Approach:**

1. Search the repo for usages of `runBinaryCheck`
2. Compare each usage to the safe pattern in the security docs
3. Identify the required refactor
4. Prepare a diff for each file with unsafe usage

This isn’t a generic LLM answer. It’s grounded in the actual project context.

### 5. Generate the pull request

Once you approve the plan, tell Copilot: “Propose code changes using Copilot coding agent.”

The agent generates a pull request with:

* The before version and the after version
* An explanation of what changed
* References to the exact files that informed the fix
* The instructions that guided its choices

Every file in the pull request shows which source informed the suggestion. You can audit the reasoning before you merge.

### 6. Iterate if you need to

Not happy with something? Mention `@copilot` in the pull request comments to iterate on the existing pull request, or go back to the space to generate a fresh one. Keep working with Copilot until you get exactly what you need.

### 7. Share your space with your team

Spaces are private by default. But you can share them with specific individuals, your entire team, or your whole organization (if admins allow it).

Enterprise admins control who can share what, so you stay aligned with your company’s security policies.

## Use GitHub Copilot Spaces from your IDE

Spaces are now available in your IDE via the GitHub MCP Server.

Install the MCP server, and you can call your spaces directly from your editor. Same curated context, same grounded answers, but right where you’re already working.

Being able to call a space from the IDE has been a game changer for me. It lets me stay focused without switching between the browser and my editor, which cuts out a ton of friction in debugging.

## Coming soon

Here’s what’s on the roadmap:

* **Public API**
* **Image support**
* **Additional file types** like doc/docx and PDFs

## Three ways teams are using spaces right now

**1. Code generation and debugging.** Use spaces with Copilot coding agent to generate pull requests aligned with your patterns, security rules, and architecture.

**2. Planning features.** Link issues, design docs, and repos to plan features and draft requirements. Ask Copilot for a technical plan and it generates a pull request.

**3. Knowledge sharing and onboarding.** Spaces become living knowledge bases. New engineers onboard faster. Existing engineers stop answering the same questions repeatedly.

## Try it on your next issue

Here’s my challenge to you:

1. Create a GitHub Copilot Space.
2. Add one issue and three to four key files.
3. Add simple instructions.
4. Ask Copilot to analyze the issue and propose a debugging plan.
5. Approve the plan.
6. Trigger the coding agent to generate a pull request.

You’ll see exactly how much time you save when Copilot actually knows your project. **Your AI assistant should never lack the right context.** That’s what spaces are for.

**Want to see the full demo?** Watch the [GitHub Checkout episode on Copilot Spaces](https://www.youtube.com/watch?v=noVdaj7sEWA) and [try GitHub Copilot Spaces](https://gh.io/tryspaces).

---

## Tags:

* [GitHub Checkout](https://github.blog/tag/github-checkout/)
* [GitHub Copilot](https://github.blog/tag/github-copilot/)
* [GitHub Copilot Spaces](https://github.blog/tag/github-copilot-spaces/)

## Written by

![Andrea Griffiths](https://github.blog/wp-content/uploads/2025/08/Andrea-Griffiths_avatar_1755783168-200x200.jpeg)

### [Andrea Griffiths](https://github.blog/author/andreagriffiths11/)

[@AndreaGriffiths11](https://github.com/andreagriffiths11)

Andrea is a Senior Developer Advocate at GitHub with over a decade of experience in developer tools. She combines technical depth with a mission to make advanced technologies more accessible. After transitioning from Army service and construction management to software development, she brings a unique perspective to bridging complex engineering concepts with practical implementation. She lives in Florida with her Welsh partner, two sons, and two dogs, where she continues to drive innovation and support open source through GitHub's global initiatives. Find her online @acolombiadev.
