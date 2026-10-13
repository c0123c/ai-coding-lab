# 60 million Copilot code reviews and counting

Since our initial launch of Copilot code review (CCR) last April, usage has grown 10X, now accounting for more than one in five code reviews on GitHub.

Behind the scenes, we’ve been running continuous experiments to enhance comment quality. We also moved to an agentic architecture that retrieves repository context and reasons across changes. At every step of the way, we’ve listened to your feedback: your survey answers and even your simple thumbs-up and thumbs-down reactions on comments have helped us identify key issues and iterate on our UX to provide a comprehensive review experience.

> Copilot code review handles pull request reviews and summaries, allowing teams to focus on more complex tasks.
>
> Suvarna Rane, Software Development Manager, General Motors

## Redefining a “good” code review

As Copilot code review evolved over time, so has our definition of a “good code review.” When we started building it in 2024, our goal was simple thoroughness. Since then, we’ve learned that what developers actually value is high-signal feedback that helps them move a pull request forward quickly. Today, Copilot code review leverages the best models, memory, and agentic tool-calling to conduct comprehensive reviews. To get here, we’ve used a continuous evaluation loop to tune the agent’s judgment, focusing on three qualities that shape that experience: accuracy, signal, and speed.

### Accuracy

Our aim has been for Copilot code review to deliver sound judgment, prioritizing consequential logic and maintainability issues. We evaluate performance in two ways: through internal testing against known code issues, and through production signals from real pull requests. In production, we track two key indicators:

* **Developer feedback**: Thumbs-up and thumbs-down reactions on comments help us understand whether suggestions are helpful.
* **Production signals**: We measure whether flagged issues are resolved before merging.

Together, these signals help ensure that Copilot code review surfaces issues that matter, and that faster merges come from confident fixes, not less scrutiny.

![Copilot code review comment identifying a missing dependency in a React useCallback hook and suggesting a code change to add handleKeyboardDrag to the dependency array.](https://github.blog/wp-content/uploads/2026/03/Screenshot-2026-03-05-at-10.29.41-AM.png?resize=1024%2C435)

### Signal

In code review, more comments don’t necessarily mean a better review. Our goal isn’t to maximize comment volume, but to surface issues that actually matter.

A high-signal comment helps a developer understand both the problem and the fix:

![Copilot code review comment warning that a retry loop could run indefinitely when an API returns HTTP 429 without a Retry-After header and suggesting adding a retry limit and backoff.](https://github.blog/wp-content/uploads/2026/03/Screenshot-2026-03-05-at-11.07.14-AM.png?resize=1024%2C289)

Silence is better than noise. In 71% of the reviews, Copilot code review surfaces actionable feedback. In the remaining 29%, the agent says nothing at all.

As our ability to identify high-signal findings improves, we’re also able to comment more confidently, now averaging about 5.1 comments per review without increasing review churn or lowering our quality threshold.

### Speed

In code review, speed matters, but signal matters more. Copilot code review is designed to provide a reliable first pass shortly after a pull request is opened. That being said, meaningful reviews still require analysis. As reasoning capabilities improve, so does the computation required to surface deeper issues.

We treat this as a deliberate trade-off. In one recent change, adopting a more advanced reasoning model improved positive feedback rates by 6%, even though review latency increased by 16%.

For us, that’s the right exchange. A slightly slower review that surfaces real issues is far more valuable than instant feedback that adds noise. We continue to reduce latency wherever possible, but never at the expense of high-signal findings developers can trust.

## About the agentic architecture

Given our new definition of “good,” we redeveloped our code review system. Today’s agentic design can retrieve context intelligently and explore the repository to understand logic, architecture, and specific invariants.

This shift alone has driven an initial 8.1% increase in positive feedback.

Here’s why:

* **It catches issues as it reads, not just at the end**: Previously, agents waited until the end of a review to finalize results, which often led to “forgetting” early discoveries.
* **It can maintain memory across reviews**: Now, every pull request doesn’t need to be an isolated event. If it flags a pattern in one part of the codebase, it can reuse that context in future reviews.
* **It keeps long pull requests reviewable with an explicit plan**: It can map out its review strategy ahead of time, significantly improving its performance on long, complex pull requests, where context is easily lost.
* **It reads linked issues and pull requests**: That extra context helps it flag subtle gaps. This includes cases where the code looks reasonable in isolation but doesn’t match the project’s requirements.

## Making reviews easier to navigate

By iterating on how the agent interacts with pull requests, we’ve reduced noise and made feedback more actionable. Here’s what that means for you.

* **Quickly understand feedback (and the fix) with multi-line comments**: We moved away from pinning comments to single lines. By attaching feedback to logical code ranges, Copilot makes it easier to see what it’s referring to and apply the suggested change.

![Copilot code review comment on a GitHub Actions workflow identifying a missing use_caches input parameter and suggesting a code change to add the boolean input to the workflow configuration.](https://github.blog/wp-content/uploads/2026/03/Screenshot-2026-02-01-at-3.30.41-PM.png?resize=1024%2C1010)

* **Keep your pull request timeline readable**: Instead of multiple separate comments for the same pattern error, which can be overwhelming, the agent clusters them into a single, cohesive unit to reduce cognitive load.
* **Fix whole classes of issues at once with batch autofixes**: Apply suggested fixes in batches, resolving an entire class of logic bugs or style issues at once, rather than context-switching through a dozen individual suggestions.

## Take this with you

As AI continues to accelerate software development, it’s more important than ever to help teams review and trust code at scale. Copilot code review helps teams keep pace by surfacing high-signal feedback directly in pull requests, enabling developers to catch issues earlier and merge with greater confidence.

More than 12,000 organizations now run Copilot code review automatically on every pull request. At WEX, this shift toward default AI –assisted reviews has helped scale Copilot adoption across the engineering organization:

Today, two-thirds of developers are using Copilot — including the organization’s most active contributors. WEX has since expanded adoption by making Copilot code review a default across every repository. Developers are also heavily utilizing agent mode and the coding agent to drive autonomy, helping WEX see a huge lift in deployments, with ~30% more code shipped. — [WEX customer story](https://github.com/customer-stories/wex)

Going forward, we’re focused on deeper personalization and high-fidelity interactivity, refining the agent to learn your team’s unwritten preferences while enabling two-way conversations that let you refine fixes and explore alternatives before merging.

As Copilot capabilities continue to evolve, from coding and planning to review and automation, the goal is simple: help developers move faster while maintaining the trust and quality that great software demands.

## Get started today

Copilot code review is a premium feature available with Copilot Pro, Copilot Pro+, Copilot Business, and Copilot Enterprise. See the following resources to:

* [Choose a plan](https://docs.github.com/en/copilot/get-started/plans#ready-to-choose-a-plan)
* [Enable Copilot code review without a Copilot license](https://docs.github.com/en/copilot/concepts/agents/code-review#copilot-code-review-without-a-copilot-license)
* [Watch a demo video](https://youtu.be/HDEGFNAUkX8?si=s9DauqsFZCdtpCtI)

Already enabled Copilot code review? See these docs to [set up automatic Copilot code reviews on every pull request](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/request-a-code-review/configure-automatic-review) within your repository or organization.

Have thoughts or feedback? Please let us know in our [community discussion post](https://github.com/orgs/community/discussions/186303).

---

## Tags:

* [code quality](https://github.blog/tag/code-quality/)
* [GitHub Actions](https://github.blog/tag/github-actions/)
* [GitHub Copilot](https://github.blog/tag/github-copilot/)
* [GitHub Copilot code review](https://github.blog/tag/github-copilot-code-review/)

## Written by

![Ria Gopu](https://avatars.githubusercontent.com/u/189655821?v=4&s=200)

### [Ria Gopu](https://github.blog/author/ria-gopu/)

[@ria-gopu](https://github.com/ria-gopu)

GitHub Product Manager

![David Apirian](https://avatars.githubusercontent.com/u/3904462?v=4&s=200)

### [David Apirian](https://github.blog/author/dapirian/)

[@dapirian](https://github.com/dapirian)

Senior Director, Software Engineering
