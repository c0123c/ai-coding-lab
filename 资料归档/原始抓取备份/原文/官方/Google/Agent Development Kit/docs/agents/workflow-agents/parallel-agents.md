---
vendor: "Google"
repository: "google/adk-docs"
path: "docs/agents/workflow-agents/parallel-agents.md"
source_url: "https://github.com/google/adk-docs/blob/main/docs/agents/workflow-agents/parallel-agents.md"
raw_url: "https://raw.githubusercontent.com/google/adk-docs/main/docs/agents/workflow-agents/parallel-agents.md"
resolved_url: "https://raw.githubusercontent.com/google/adk-docs/main/docs/agents/workflow-agents/parallel-agents.md"
tree_snapshot_sha: "12712d00d48712998f15a2a9d8bd26d708b727a3"
sha256_body: "adbc75b0f9f2094de82201f324b6d95d42d3541b69d0d3835d4a31b460c33ec9"
archive: "01_资料来源/官方资料/审计记录/官方GitHub相关正文_2026-10-10/official-github-pages-0006.jsonl.gz:33"
---

# Parallel template workflow agent

<div class="language-support-tag">
  <span class="lst-supported">Supported in ADK</span><span class="lst-python">Python v0.1.0</span><span class="lst-typescript">TypeScript v0.2.0</span><span class="lst-go">Go v0.1.0</span><span class="lst-java">Java v0.2.0</span>
</div>

The ***ParallelAgent*** class is a [template workflow](/agents/workflow-agents/)
agent that executes its sub-agents concurrently. This execution strategy can
dramatically speed up workflows where two or more tasks can be performed
independently. For scenarios prioritizing speed and involving independent,
resource-intensive tasks, this templated workflow facilitates parallel
execution, which can significantly reduce overall processing time. When using
this workflow type, it is important that each sub-agent can operate without
depending on the other sub-agents. This workflow type is particularly beneficial
for operations like multi-source data retrieval or heavy computations, where
parallelization yields substantial performance gains.

As with other templated workflows, the execution of a ***ParallelAgent*** object
is not controlled by an AI model, and is deterministic in how it executes its
sub-agents. The sub-agents specified in the parallel execution set may or may
not utilize AI models, but the overall execution of those sub-agents is
ultimately managed by the ***ParallelAgent*** object you define.

!!! note "Alternative: graph-based workflows"

    Starting in ADK 2.0 for Python and Go, templated workflows have been superseded

    by more flexible workflow structures, including
    [graph-based workflows](/graphs/) and
    [dynamic workflows](/graphs/dynamic/).

### How it works

When the `ParallelAgent`'s `run_async()` method is called:

1. **Concurrent Execution:** It initiates the `run_async()` method of *each* sub-agent present in the `sub_agents` list *concurrently*.  This means all the agents start running at (approximately) the same time.
2. **Independent Branches:**  Each sub-agent operates in its own execution branch.  There is ***no* automatic sharing of conversation history or state between these branches** during execution.
3. **Result Collection:** The `ParallelAgent` manages the parallel execution and, typically, provides a way to access the results from each sub-agent after they have completed (e.g., through a list of results or events). The order of results may not be deterministic.

### Independent Execution and State Management

It's *crucial* to understand that sub-agents within a `ParallelAgent` run independently.  If you *need* communication or data sharing between these agents, you must implement it explicitly.  Possible approaches include:

* **Shared `InvocationContext`:** You could pass a shared `InvocationContext` object to each sub-agent.  This object could act as a shared data store.  However, you'd need to manage concurrent access to this shared context carefully (e.g., using locks) to avoid race conditions.
* **External State Management:**  Use an external database, message queue, or other mechanism to manage shared state and facilitate communication between agents.
* **Post-Processing:** Collect results from each branch, and then implement logic to coordinate data afterwards.

![Parallel Agent](/assets/parallel-agent.png){: width="600"}

### Full Example: Parallel Web Research

Imagine researching multiple topics simultaneously:

1. **Researcher Agent 1:**  An `LlmAgent` that researches "renewable energy sources."
2. **Researcher Agent 2:**  An `LlmAgent` that researches "electric vehicle technology."
3. **Researcher Agent 3:**  An `LlmAgent` that researches "carbon capture methods."

    ```py
    ParallelAgent(sub_agents=[ResearcherAgent1, ResearcherAgent2, ResearcherAgent3])
    ```

These research tasks are independent.  Using a `ParallelAgent` allows them to run concurrently, potentially reducing the total research time significantly compared to running them sequentially. The results from each agent would be collected separately after they finish.

???+ "Full Code"

    === "Python"
        ```py
         --8<-- "examples/python/snippets/agents/workflow-agents/parallel_agent_web_research.py:init"
        ```

    === "TypeScript"
        ```typescript
         --8<-- "examples/typescript/snippets/agents/workflow-agents/parallel_agent_web_research.ts:init"
        ```

    === "Go"
        ```go
         --8<-- "examples/go/snippets/agents/workflow-agents/parallel/main.go:init"
        ```

    === "Java"
        ```java
         --8<-- "examples/java/snippets/src/main/java/agents/workflow/ParallelResearchPipeline.java:full_code"
        ```
