---
vendor: "GitHub / Microsoft"
title: "Blocking {% data variables.copilot.copilot_cloud_agent %}"
official_url: https://raw.githubusercontent.com/github/docs/main/content/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/block-agentic-features.md
resolved_url: https://raw.githubusercontent.com/github/docs/be38ec5d78e24172587e61b3c6ff40ace1865c71/content/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/block-agentic-features.md
fetched_at: 2026-10-10T15:19:43+00:00
status: fetched
scope: core_ai_coding
source_kind: official_github_raw
repository_revision: be38ec5d78e24172587e61b3c6ff40ace1865c71
sha256_raw: 408f262583a81565ef80152ec35769c77c5138e7c995fc4487bac0132e2bd8c3
sha256_body: 408f262583a81565ef80152ec35769c77c5138e7c995fc4487bac0132e2bd8c3
---

# 官方正文

---
title: Blocking agentic features in your enterprise
intro: Disable features for all users and repositories.
permissions: Enterprise owners and AI managers
versions:
  feature: copilot
shortTitle: Block agentic features
contentType: how-tos
category:
  - Manage Copilot for a team
allowTitleToDifferFromFilename: true
redirect_from:
  - /copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/block-copilot-cloud-agent
---

Most {% data variables.product.prodname_copilot_short %} policies only affect users who receive a {% data variables.product.prodname_copilot_short %} license from your enterprise or organizations. However, there are dedicated policies to completely disable features in your repositories, including for users who get access to {% data variables.product.prodname_copilot_short %} from a personal plan or another enterprise.

## Blocking {% data variables.copilot.copilot_cloud_agent %}

{% data reusables.enterprise-accounts.access-enterprise %}
{% data reusables.enterprise-accounts.ai-controls-tab %}
{% data reusables.enterprise-accounts.cca-policies %}
1. Next to **Block {% data variables.copilot.copilot_cloud_agent %} in all repositories owned by ENTERPRISE-NAME**, click the toggle.

## Blocking {% data variables.copilot.copilot_code-review_short %}

{% data reusables.enterprise-accounts.access-enterprise %}
{% data reusables.enterprise-accounts.ai-controls-tab %}
1. In the left sidebar, click {% octicon "agent" aria-hidden="true" aria-label="agent" %} **Agents**.
1. Under "Available Agents", click **{% data variables.copilot.copilot_code-review_short %}**.
1. Next to **Block {% data variables.copilot.copilot_code-review_short %} in all enterprise repositories**, click the toggle.
