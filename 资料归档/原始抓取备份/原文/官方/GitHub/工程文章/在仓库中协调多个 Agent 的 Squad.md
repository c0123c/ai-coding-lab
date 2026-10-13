# How Squad runs coordinated AI agents inside your repository

If you’ve used AI coding tools before, you know the pattern. You write a prompt, the model misunderstands, you refine it, and you coax better output. Progress depends more on steering the model than on building the software.

As projects grow, the challenge stops being “how do I prompt?” and starts becoming “how do I coordinate design, implementation, testing, and review without losing context along the way.”

Multi-agent systems are a great way to move past this plateau, but usually require a massive amount of setup. People spend hours building orchestration layers, wiring up frameworks, and configuring vector databases before they can delegate a single task.

[Squad](https://github.com/bradygaster/squad), an open source project built on GitHub Copilot, initializes a preconfigured AI team directly inside your repository. It is a bet that multi-agent development can be accessible, legible, and useful without requiring heavy orchestration infrastructure or deep prompt engineering expertise.

Two commands—`npm install -g @bradygaster/squad-cli` once globally, `squad init` once per repo—and Squad drops a specialized AI team: a lead, frontend developer, backend developer, and tester directly against your repository.

Instead of a single chatbot switching roles, **Squad demonstrates repository native multi-agent orchestration without heavy centralized infrastructure**.

## How Squad coordinates work across agents

You describe the work you need done in natural language. From there, a coordinator agent inside Squad figures out the routing, loads repository context, and spawns specialists with task-specific instructions.

For example, you type: “*Team, I need JWT auth—refresh tokens, bcrypt, the works.*” Then you watch the team spin up in parallel. The backend specialist takes the implementation. The tester starts writing the accompanying test suite. A documentation specialist opens a pull request. Within minutes, files are written and branches are created. These specialists already know your naming conventions and what you decided about database connections last Tuesday—not because you put it in the prompt, but because agents load from shared team decisions and their own project history files committed to the repository.

Instead of forcing you to manually test the output and prompt the model through multiple rounds of fixes, Squad handles iteration internally. Once the backend specialist drafts the initial implementation, the tester runs their test suite against it. If those tests fail, the tester rejects the code. Crucially, the orchestration layer prevents the original agent from revising its own work. Squad’s reviewer protocol can prevent the original author from revising rejected work, and a different agent must step in to fix it. **This forces genuine independent review with a separate context window and a fresh perspective, rather than asking a single AI to review its own mistakes**. In workflows where reviewer automation is enabled, you review the pull request that survives this internal loop rather than every intermediate attempt.

It’s not autopilot, and it’s not magic on session one. Agents will ask clarifying questions and sometimes make reasonable but wrong assumptions. You still review and merge every pull request. It is collaborative orchestration, not autonomous execution.

## Architectural patterns behind repository-native orchestration

Whether you use Squad or build your own multi-agent workflows, there are a few architectural patterns we’ve learned from building repository-native orchestration. These patterns move the architecture away from “black box” behavior toward something inspectable and predictable at the repository level.

### 1. The “Drop-box” pattern for shared memory

Most AI orchestration relies on real-time chat or complex vector database lookups to keep agents in sync. We’ve found that this is often too fragile; synchronizing state across live agents is a fool’s errand.

Instead, Squad uses a “drop-box” pattern. Every architectural choice, like choosing a specific library or a naming convention, is appended as a structured block to a versioned `decisions.md` file in the repository. This is a bet that asynchronous knowledge sharing inside the repository scales better than real-time synchronization. By treating a markdown file as the team’s shared brain, you get persistence, legibility, and a perfect audit trail of every decision the team has made. Because this memory lives in project files rather than a live session, the team can also recover context after disconnects or restarts and continue from where it left off.

### 2. Context replication over context splitting

One of the biggest hurdles in AI development is the context window limit. When a single agent tries to do everything, the “working memory” gets crowded with meta-management, leading to hallucinations.

Squad solves this by ensuring the coordinator agent remains a thin router. It doesn’t do the work; it spawns specialists. Because each specialist runs as a separate inference call with its own large context window (e.g., up to 200K tokens on supported models), you aren’t splitting one context among four agents, you’re replicating repository context across them.

Running multiple specialists in parallel gives you multiple independent reasoning contexts operating simultaneously. This allows each agent to “see” the relevant parts of the repository without competing for space with the other agents’ thoughts.

### 3. Explicit memory in the prompt vs. implicit memory in the weights

We believe an AI team’s memory should be legible and versioned. You shouldn’t have to wonder what an agent “knows” about your project.

In Squad, an agent’s identity is built primarily on two repository files: a charter (who they are) and a history (what they’ve done), alongside shared team decisions. These are plain text. Because these live in your `.squad/` folder, the AI’s memory is versioned right alongside your code. When you clone a repo, you aren’t just getting the code; you are getting an already “onoboarded” AI team because their memory lives alongside the code directly in the repository.

## Lowering the barrier to multi-agent workflows

Our biggest win with Squad is that it makes it easy for anyone to get started with agentic development in a low-touch, low-ceremony way. You shouldn’t have to spend hours wrestling with infrastructure, learning complex prompt engineering, or managing convoluted CLI interactions just to get an AI team to help you write code.

To see what repository-native orchestration feels like, check out the [Squad repository](https://github.com/bradygaster/squad) and throw a squad at a problem to see how the workflow evolves.

---

## Tags:

* [AI](https://github.blog/tag/ai/)
* [GitHub Copilot](https://github.blog/tag/github-copilot/)
* [multi-agent workflows](https://github.blog/tag/multi-agent-workflows/)

## Written by

![Brady Gaster](https://secure.gravatar.com/avatar/c4c1d68f4766046384a4c7ee192c152e0bf240745b6b84a0baa656e9b0abd0b1?s=200&d=mm&r=g)

### [Brady Gaster](https://github.blog/author/bradyg/)

[@bradygaster](https://github.com/bradygaster)

Principal PM Architect in CoreAI Apps & Agents at Microsoft, where I build stuff to make it easier for people to party with Copilot in the cloud.
