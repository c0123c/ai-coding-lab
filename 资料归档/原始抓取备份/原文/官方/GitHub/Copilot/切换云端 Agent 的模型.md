---
vendor: "GitHub / Microsoft"
title: "Introduction"
official_url: https://raw.githubusercontent.com/github/docs/main/content/copilot/how-tos/use-copilot-agents/cloud-agent/changing-the-ai-model.md
resolved_url: https://raw.githubusercontent.com/github/docs/be38ec5d78e24172587e61b3c6ff40ace1865c71/content/copilot/how-tos/use-copilot-agents/cloud-agent/changing-the-ai-model.md
fetched_at: 2026-10-10T14:03:38+00:00
status: fetched
scope: core_ai_coding
source_kind: official_github_raw
repository_revision: be38ec5d78e24172587e61b3c6ff40ace1865c71
sha256_raw: 0387d5556c052f1e3808aae5e6c7fb2ca47a5fd26a29d74d9582675fbf55ea04
sha256_body: 0387d5556c052f1e3808aae5e6c7fb2ca47a5fd26a29d74d9582675fbf55ea04
---

# 官方正文

---
title: Changing the AI model for GitHub Copilot cloud agent
shortTitle: Changing the AI model
allowTitleToDifferFromFilename: true
intro: 'In supported entrypoints, when starting a task with {% data variables.copilot.copilot_cloud_agent %}, you can select the model used, and, for supported models, the reasoning level.'
product: '{% data reusables.gated-features.copilot-cloud-agent %}<br><a href="https://github.com/features/copilot/plans?ref_product=copilot&ref_type=purchase&ref_style=button" target="_blank" class="btn btn-primary mt-3 mr-3 no-underline"><span>Sign up for {% data variables.product.prodname_copilot_short %}</span> {% octicon "link-external" height:16 %}</a>'
versions:
  feature: copilot
contentType: how-tos
category: 
  - Author and optimize with Copilot
redirect_from:
  - /copilot/how-tos/use-copilot-agents/coding-agent/changing-the-ai-model
---

> [!NOTE]
> For an overview of {% data variables.copilot.copilot_cloud_agent %}, see [AUTOTITLE](/copilot/concepts/copilot-surfaces/copilot-on-github).

## Introduction

In supported entrypoints, you can select the model used by {% data variables.copilot.copilot_cloud_agent %}.

You may find that different models perform better, or provide more useful responses, depending on the type of tasks you give {% data variables.product.prodname_copilot_short %}.

> [!NOTE]
> Model selection for {% data variables.copilot.copilot_cloud_agent %} is only supported when assigning an issue to {% data variables.product.prodname_copilot_short %} on {% data variables.product.prodname_dotcom_the_website %}, when mentioning `@copilot` in a pull request comment on {% data variables.product.prodname_dotcom_the_website %}, or when starting a task from the agents tab, agents panel, {% data variables.product.prodname_mobile %} or the Raycast launcher. Where a model picker is not available, Auto will be used automatically. See [AUTOTITLE](/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions).

## Supported models

The following options are currently available:

* Auto 
{% data reusables.copilot.copilot-cloud-agent-non-auto-models %}

{% data reusables.copilot.auto-model-option %}

## Reasoning level

Some models support configurable reasoning levels, which control how much time and effort the model spends reasoning before it responds. Higher reasoning levels can improve the quality of responses to complex tasks, but may take longer and use more {% data variables.product.prodname_ai_credits_short %}.

If you select a model that supports configurable reasoning, a second dropdown menu appears so you can choose the reasoning level you want {% data variables.copilot.copilot_cloud_agent %} to use for that task. For more information about which models support configurable reasoning, see [AUTOTITLE](/copilot/reference/ai-models/supported-models#models-with-extended-capabilities).

