---
vendor: "GitHub"
repository: "github/docs"
path: "content/copilot/concepts/agents/anthropic-claude.md"
source_url: "https://github.com/github/docs/blob/main/content/copilot/concepts/agents/anthropic-claude.md"
raw_url: "https://raw.githubusercontent.com/github/docs/main/content/copilot/concepts/agents/anthropic-claude.md"
resolved_url: "https://raw.githubusercontent.com/github/docs/main/content/copilot/concepts/agents/anthropic-claude.md"
tree_snapshot_sha: "be38ec5d78e24172587e61b3c6ff40ace1865c71"
sha256_body: "729ad7b000c255e209620e97946aa31f69667ba000e753b4e211bb732598aedf"
archive: "01_资料来源/官方资料/审计记录/官方GitHub相关正文_2026-10-10/official-github-pages-0004.jsonl.gz:30"
---

---
title: Anthropic Claude
shortTitle: Anthropic Claude
intro: 'Use the {% data variables.product.prodname_anthropic_claude %} coding agent powered by {% data variables.product.prodname_copilot_short %}.'
product: '{% data reusables.gated-features.anthropic-claude %}<br><a href="https://github.com/features/copilot/plans?ref_product=copilot&ref_type=purchase&ref_style=button" target="_blank" class="btn btn-primary mt-3 mr-3 no-underline"><span>Sign up for {% data variables.product.prodname_copilot_short %}</span> {% octicon "link-external" height:16 %}</a>'
versions:
  feature: copilot
contentType: concepts
---

> [!NOTE] The {% data variables.product.prodname_anthropic_claude %} coding agent is currently in {% data variables.release-phases.public_preview %}.

## Introduction

The {% data variables.product.prodname_anthropic_claude %} coding agent uses the Claude Agent SDK and can be powered by your existing {% data variables.product.prodname_copilot_short %} subscription. For information about {% data variables.product.prodname_anthropic_claude %}, see the [{% data variables.product.prodname_anthropic_claude %} documentation](https://platform.claude.com/docs/en/agent-sdk/overview).

## {% data variables.product.prodname_anthropic_claude %} coding agent

Before you can assign tasks to {% data variables.product.prodname_anthropic_claude %} coding agent, it must be enabled. See [AUTOTITLE](/copilot/concepts/agents/about-third-party-coding-agents#making-coding-agents-available).

To learn more about using third-party coding agents, see [AUTOTITLE](/copilot/concepts/agents/about-third-party-coding-agents).

### Supported models

When starting a task with the {% data variables.product.prodname_anthropic_claude %} coding agent, you can select the AI model used by the agent. The following models are available:

* Auto
{% data reusables.copilot.anthropic-claude-agent-models %}

If you select **Auto**, it will choose one of the available models listed above. **Auto** in {% data variables.product.prodname_anthropic_claude %} coding agent does **not** leverage {% data variables.copilot.copilot_auto_model_selection %}.
