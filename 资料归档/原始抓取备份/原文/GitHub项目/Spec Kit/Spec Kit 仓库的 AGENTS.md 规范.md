---
vendor: "GitHub"
repository: "github/spec-kit"
path: "AGENTS.md"
source_url: "https://github.com/github/spec-kit/blob/main/AGENTS.md"
raw_url: "https://raw.githubusercontent.com/github/spec-kit/main/AGENTS.md"
resolved_url: "https://raw.githubusercontent.com/github/spec-kit/main/AGENTS.md"
tree_snapshot_sha: "0443760542e7e5269724744a2ac55c4bd386d095"
sha256_body: "562f6fb17479d6ae0fe135548490ee9835bd8363e266019011b9c3e3dcab8584"
archive: "01_资料来源/官方资料/审计记录/官方GitHub相关正文_2026-10-10/official-github-pages-0005.jsonl.gz:53"
---

# AGENTS.md

## About Spec Kit and Specify

**GitHub Spec Kit** is a comprehensive toolkit for implementing Spec-Driven Development (SDD) - a methodology that emphasizes creating clear specifications before implementation. The toolkit includes templates, scripts, and workflows that guide development teams through a structured approach to building software.

**Specify CLI** is the command-line interface that bootstraps projects with the Spec Kit framework. It sets up the necessary directory structures, templates, and AI agent integrations to support the Spec-Driven Development workflow.

The toolkit supports multiple AI coding assistants, allowing teams to use their preferred tools while maintaining consistent project structure and development practices.

## Adding or Updating CLI Commands

Before adding, updating, or reorganizing Specify CLI commands, read
[Shared Command Application Architecture](design/shared.md) and
[Specify CLI Command Architecture](design/cli.md). They define the shared
operation boundary, command-module naming, private phases, nested command
groups, registration ownership, and mirrored tests.

## Adding or Updating MCP Commands

Before adding or changing MCP tools for Specify commands, read
[Shared Command Application Architecture](design/shared.md) and
[Specify MCP Command Architecture](design/mcp.md). They define the shared
operation boundary, typed contracts, explicit inventory, side-effect metadata,
the local stdio protocol boundary, and mirrored tests.

## Adding or Updating Agent Integrations

Before adding or changing AI agent integrations, read
[Agent Integration Design](design/integration.md). It covers
delivery routes, output formats, registration, and install/uninstall ownership.

## Adding or Updating Workflow Steps

Before adding or changing workflow step types, read
[Workflow Step Design](design/workflow-step.md). It covers registration,
validation, execution, resume, and installed step packages.

## Testing Executable Behavior

Before changing code or configuration that runs or controls execution without
an LLM, read
[Testing deterministic behavior](CONTRIBUTING.md#testing-deterministic-behavior).
Behavioral changes need positive and negative coverage; bug fixes need
before-and-after regression evidence.

## Branches and Agent Contributions

When creating a branch, follow [Branch naming](CONTRIBUTING.md#branch-naming).
Before authoring commits, opening PRs, or posting review comments, read
[Agent-authored Git and review activity](CONTRIBUTING.md#agent-authored-git-and-review-activity).
Agent-authored commits and AI-generated PRs and comments each require their
own disclosure; a PR-body disclosure alone does not cover later activity.

## Other Contribution Guidance

For contribution or repository-workflow questions not covered above, or when
the applicable guidance is unclear, read [CONTRIBUTING.md](CONTRIBUTING.md)
before acting.

## Common Pitfalls

- **Running tests against the wrong environment:** Run the suite inside this
  worktree's own virtualenv (`uv sync --extra test` then
  `.venv/bin/python -m pytest`). A bare `uv run pytest` can pick up an
  editable install from another worktree and fail to import new subpackages.
