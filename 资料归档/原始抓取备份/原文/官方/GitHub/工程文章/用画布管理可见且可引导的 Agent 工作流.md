# How canvases make agentic workflows visible, steerable, and cost-efficient

When I was in college, I joined the beta for one of the first versions of AI inline completions in VS Code. It felt like a game changer. Since then, GenAI has fundamentally changed software development: hybrid teams where agents and humans work in tandem, with the developer at the center as visionary and orchestrator. We are living in that transition right now.

![A software lifecycle timeline — Plan, Build, Review, Ship — with intertwining green and purple paths representing a human and an AI agent working in tandem.](https://github.blog/wp-content/uploads/2026/08/623371402-ddb0772d-e566-4895-b030-6be9b0fe304c.png?resize=1024%2C312)

As a natural byproduct of how fast innovation in GenAI has moved, we now have tools to help us plan, build, review, and ship code. But in the current state, many workflows still feel disjointed. Context gets lost across threads and surfaces, and too much time gets spent reviewing agent-generated work. Agents can produce changes faster than any human can review them, and most developer tools were not originally designed for multi-agent orchestration. It becomes easy to lose track of what ran, what changed, what was validated, and what still needs human judgment.

The GitHub Copilot app is a major step toward addressing this. One feature in particular that I’ve learned to love and use almost every day is **canvases**. Canvases let developers and agents interact on a durable, shared surface. Instead of treating chat as the only place where work happens, canvases make work visible, steerable, and approvable as it unfolds.

## Chat is great for intent, but weak for durable execution

I still believe chat is one of the best interfaces we have for intent. It’s where you can think, refine, and direct. It’s fast and flexible, especially when the problem is still ambiguous.

But once an agent starts doing real work, chat becomes a long scroll of instructions, logs, pivots, and corrections. The important parts are technically there, but buried: the plan, decision points, validations, and approval moments. If you have to reconstruct all of that from history, you’re already paying a coordination tax.

Canvases solve that by giving workflows a home. They make state explicit and persistent. Humans can inspect and guide. Agents can update and progress. Both can stay aligned without constantly replaying context.

## The first build: Java Modernization Studio

One of the first canvases I built was **Java Modernization Studio**. Java modernization is exactly the kind of workflow where visibility and governance matter: assessment, planning, migration tasks, validation gates, and readiness to ship.

In a chat-only experience, those steps blur together. You can still move forward, but it gets harder to audit and harder to trust at scale, especially with multiple contributors. Teams keep asking the same expensive questions: What stage are we in? What decisions were made? What is blocked? What still needs human approval?

The studio made each phase explicit and inspectable. Instead of parsing narrative history, teams could see operational state directly. Instead of guessing what happened, they could verify it. Human reviewers could focus on high-signal judgments while agents kept execution moving between checkpoints.

![The Java Modernization Studio canvas Overview tab, showing the modernization journey (Assess, Remediate, Validate, Ship), a compile-blocker card, a Run on autopilot option, assessment findings with P0–P3 severity counts, and the detected stack.](https://github.blog/wp-content/uploads/2026/08/623375232-cbf590d3-74d6-471d-be21-76add276959b.png?resize=2200%2C2494)

[**Explore the Java Modernization Studio canvas >**](https://awesome-copilot.github.com/extension/appmod-cockpit/)

## The second build: Site Studio

After that, I built **Site Studio** for a very different workflow: creating and managing personal site content. It’s content-heavy rather than migration-heavy, but the orchestration challenge is similar: section progress, iterative edits, review loops, and status transitions.

In a chat-only flow, content can drift quickly. A section gets revised, then revised again, and confidence drops in what is current. Feedback gets scattered, drafts repeat, and momentum slows because each iteration starts by rebuilding context.

Site Studio keeps that state durable. Section status is visible. Draft values are persisted as work happens. Human review points are explicit. The agent can keep moving while the human can steer, approve, or redirect without losing the thread.

![The Site Studio canvas Content tab at 100% completion, with editable sections — Design System, Hero, Navigation, About, Conference Talks, Videos, and Contact — each showing filled fields and a Mark ready for review action.](https://github.blog/wp-content/uploads/2026/08/623387227-87abbc64-0005-4816-a6f5-3c86756e1651.png?resize=1185%2C2623)

**[Explore the Site Studio canvas >](https://awesome-copilot.github.com/extension/site-studio/)**

## The repeatable pattern

Across both canvases, I found the same repeatable blueprint:

1. Define workflow states clearly.
2. Surface the decisions that matter.
3. Persist progress and drafts immediately.
4. Keep explicit human approval points.

This shifts the model from prompt-by-prompt interaction to durable collaborative workflows. You stop treating each turn like a fresh start and start treating each workflow like a system with memory, structure, and control.

## Cost and efficiency: yes, canvases are an investment

I also want to be explicit about cost: canvases can be an investment. For instance, Site Studio cost me about 2,000 AI credits, and the modernization canvas cost me about 3,000 AI credits. They take effort to design and shape well.

But in the long run, especially for repeated workflows, that investment pays back. Durable surfaces reduce repeated prompting, reduce context loss, reduce unnecessary back-and-forth, and reduce rework. Over time, that can save both time and money while improving trust and throughput.

So for me, this is not “spend more tokens for nicer UX.” It’s “invest in better workflow architecture so recurring work becomes more efficient, predictable, and governable.”

## Available now in awesome-copilot

The canvases I built—**Java Modernization Studio** and **Site Studio**—[are available in **awesome-copilot**](https://awesome-copilot.github.com/extensions/) for anyone who wants to use them, adapt them, or learn from them.

If you are already using Copilot agents, a practical next step is to pick one repeated workflow and build a minimal canvas around it with `/create-canvas`. Start small, run real work, and iterate from actual usage. If it helps your team, contribute it back to **awesome-copilot** so others can benefit too.

We’re still early in this transition, but the direction is clear. Agents can accelerate execution. Humans provide vision, judgment, and accountability. Canvases are one way to make that partnership real, durable, and scalable.

[Build your own canvas with /create-canvas](https://awesome-copilot.github.com/extensions/) and contribute it back to awesome-copilot >

---

## Tags:

* [awesome-copilot](https://github.blog/tag/awesome-copilot/)
* [canvases](https://github.blog/tag/canvases/)
* [developer productivity](https://github.blog/tag/developer-productivity/)
* [GitHub Copilot](https://github.blog/tag/github-copilot/)
* [GitHub Copilot app](https://github.blog/tag/github-copilot-app/)

## Written by

![Ayan Gupta](https://avatars.githubusercontent.com/u/74832088?v=4&s=200)

### [Ayan Gupta](https://github.blog/author/ayangupt/)

[@ayangupt](https://github.com/ayangupt)

Ayan Gupta is a Developer Advocate at GitHub, where he helps developers and teams adopt practical, AI-powered development workflows across GitHub Copilot, Azure, and open source. A Forbes 30 Under 30 honoree, he previously co-founded technology ventures focused on expanding access to wellness and education. His work today centers on making agent-driven workflows transparent, steerable, and repeatable — and outside of it, he enjoys woodworking and volunteering.
