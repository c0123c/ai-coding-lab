---
vendor: "Google"
repository: "google/adk-docs"
path: "docs/workflows/index.md"
source_url: "https://github.com/google/adk-docs/blob/main/docs/workflows/index.md"
raw_url: "https://raw.githubusercontent.com/google/adk-docs/main/docs/workflows/index.md"
resolved_url: "https://raw.githubusercontent.com/google/adk-docs/main/docs/workflows/index.md"
tree_snapshot_sha: "12712d00d48712998f15a2a9d8bd26d708b727a3"
sha256_body: "71f09baedaf6239660438cd1d279d56ba8463db1f341d3a5a4ff4309881fcf3f"
archive: "01_资料来源/官方资料/审计记录/官方GitHub相关正文_2026-10-10/official-github-pages-0006.jsonl.gz:48"
---

# Workflows: multi-agent, multi-node applications

<div class="language-support-tag">
  <span class="lst-supported">Supported in ADK</span><span class="lst-python">Python v0.1.0</span><span class="lst-typescript">TypeScript v0.2.0</span><span class="lst-go">Go v0.1.0</span><span class="lst-java">Java v0.1.0</span>
</div>

As agentic applications grow in complexity, structuring them as a single,
monolithic agent can become challenging to develop, evaluate, and maintain.
Agent Development Kit (ADK) supports building sophisticated agent applications
by composing multiple agents and executable nodes into *agent
workflows*. Structuring agents using multiple elements can provide a number
of benefits as your agent applications grow more complex and sophisticated:

* **Predictability:** Create more controlled task execution flow using templated
  logic or graph-based execution mechanisms.
* **Reliability:** Ensure tasks run in the required order or pattern
  consistently.
* **Structure:** Build complex processes more manageably by composing agents
  elements, separating task responsibilities, and limiting data contexts for
  given tasks.

Workflows can be built using several structures and architectures, as
illustrated in the following diagram:

<img src="/assets/workflows_overview.svg" alt="Workflow types in ADK">

**Figure 1.** ADK workflows can have flexible execution paths, or follow
specific, templated execution patterns.

The following is a quick guide to the multiple methods for building workflows
for your agent application with ADK:

*   [**Graph-based workflows:**](/graphs/) (ADK 2.0 and higher)
    This workflow type allows you to compose both AI-powered agents and deterministic
    execution nodes into a flexible execution graph that can include decision branching.

*   [**Dynamic workflows:**](/graphs/dynamic/) (ADK 2.0 and higher)
    This workflow type allows you to compose AI-powered agents and deterministic
    execution nodes using full programmatic code logic.

*   [**Collaborative workflows:**](/workflows/collaboration/) (ADK 2.0 and higher)
    This workflow type allows a single agent to act in a dynamic coordinator role
    to accomplish tasks with a set of specified sub-agents.

*   [**Template workflows:**](/agents/workflow-agents/)
    These pre-built workflows are extended from ***BaseAgent*** and provide fixed
    execution logic structures including sequences, loops, and parallel execution.

Follow the links provide above for more information about each type of ADK workflow
architecture.

!!! example "Experimental: Agent Routing"

    Agent Routing is an experimental feature that allows you to select between
    multiple agents at runtime using router functions for fallback,
    A/B testing, and auto-routing. For more information, see
    [Agent Routing](/agents/routing/).
