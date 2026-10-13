---
vendor: "GitHub / Microsoft"
title: "test-custom-agents-37bd1619ef"
official_url: "https://raw.githubusercontent.com/github/docs/main/content/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/test-custom-agents.md"
status: "readable_copy_of_existing_archive"
archive_evidence: "01_资料来源/官方资料/审计记录/扩展厂商正文_2026-10-09/GitHub-Copilot-pages.jsonl.gz"
archive_line: 75
archive_body_sha256: "148c9ebedac44e36a39054da47d1a3c96108dad5beda842aa4dec3bdfab25c01"
archive_body_chars: 4117
original_fetched_at: ""
---

# 旧归档可读副本

此页从原有压缩归档按行提取，未重新抓取官网；下方为未经改写的正文。

# 官方正文

---
title: Testing and releasing {% data variables.copilot.custom_agents_short %} in your organization or enterprise
intro: 'Ensure your {% data variables.copilot.custom_agents_short %} are performant and compliant before releasing them to your company.'
versions:
  feature: copilot
shortTitle: Test custom agents
contentType: how-tos
category:
  - Configure Copilot
  - Manage Copilot for a team
redirect_from:
  - /copilot/how-tos/use-copilot-agents/coding-agent/test-custom-agents
  - /copilot/how-tos/use-copilot-agents/cloud-agent/test-custom-agents
---

{% data reusables.copilot.custom-agents-preview-note %}

## Introduction

Before you release or update a {% data variables.copilot.copilot_custom_agent_short %} in your organization or enterprise, you can test the agent privately to ensure it meets your standards. Once you are satisfied, you can then easily change the location of your {% data variables.copilot.agent_profile %} to make it available across your company.

## Prerequisites

Before you can test a {% data variables.copilot.copilot_custom_agent_short %}, you need to set up your organization or enterprise for {% data variables.copilot.custom_agents_short %}. See [AUTOTITLE](/copilot/how-tos/administer-copilot/manage-for-organization/prepare-for-custom-agents) or [AUTOTITLE](/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/prepare-for-custom-agents).

## 1. Create your test {% data variables.copilot.copilot_custom_agent_short %}

1. In your organization or enterprise's `.github-private` repository, create a new directory called `.github/agents`. Agents stored in this directory are only available to members of your organization or enterprise who have access to the `.github-private` repository, and can only be used when they start a task within that repository.
1. In your `.github/agents` directory, create the {% data variables.copilot.agent_profile %} for your test agent. You can create a net-new profile or duplicate an existing profile to test potential updates. For information on configuring an {% data variables.copilot.agent_profile %}, see [AUTOTITLE](/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/create-custom-agents#configuring-an-agent-profile).
1. Merge your test {% data variables.copilot.agent_profile %} into the default branch of your repository.

## 2. Test your {% data variables.copilot.copilot_custom_agent_short %}

1. Go to the agents tab at [https://github.com/copilot/agents](https://github.com/copilot/agents?ref_product=copilot&ref_type=engagement&ref_style=text).
1. Using the dropdown menu in the prompt box, select your `.github-private` repository.
1. Select {% octicon "copilot" aria-label="Select a custom agent" %}, then click your test agent.
1. To test your {% data variables.copilot.copilot_custom_agent_short %}, send {% data variables.product.prodname_copilot_short %} a prompt.
1. In the "Recent sessions" section, click your session to see detailed information about your results.
1. Continue making changes and testing your {% data variables.copilot.copilot_custom_agent_short %} as needed until you are satisfied with its performance.

## 3. Release your {% data variables.copilot.copilot_custom_agent_short %}

1. In your `.github-private` repository, move your {% data variables.copilot.agent_profile %} from the `.github/agents` directory into the `agents` directory.
1. Merge your changes into the default branch. Your {% data variables.copilot.copilot_custom_agent_short %} is now available to all users in your organization or enterprise.

## Next steps

To monitor the usage of {% data variables.copilot.custom_agents_short %} in your organization, filter your organization's audit log by `actor:Copilot`. See [AUTOTITLE](/organizations/keeping-your-organization-secure/managing-security-settings-for-your-organization/reviewing-the-audit-log-for-your-organization).

To monitor the usage of {% data variables.copilot.custom_agents_short %} in your enterprise, see [AUTOTITLE](/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/monitor-agentic-activity).
