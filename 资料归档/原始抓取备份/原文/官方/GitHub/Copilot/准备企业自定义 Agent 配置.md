---
vendor: "GitHub"
repository: "github/docs"
path: "content/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/prepare-for-custom-agents.md"
source_url: "https://github.com/github/docs/blob/main/content/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/prepare-for-custom-agents.md"
raw_url: "https://raw.githubusercontent.com/github/docs/main/content/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/prepare-for-custom-agents.md"
resolved_url: "https://raw.githubusercontent.com/github/docs/main/content/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/prepare-for-custom-agents.md"
tree_snapshot_sha: "be38ec5d78e24172587e61b3c6ff40ace1865c71"
sha256_body: "36de5cb87687aec828cde79a331d13772816c828780561161a79eedc78e5b565"
archive: "01_资料来源/官方资料/审计记录/官方GitHub相关正文_2026-10-10/official-github-pages-0004.jsonl.gz:49"
---

---
title: Preparing to use custom agents in your enterprise
intro: 'Set up your enterprise for {% data variables.copilot.custom_agents_short %} by configuring their source organization and repository, availability, and management permissions.'
permissions: Enterprise owners
versions:
  feature: copilot
shortTitle: Prepare for custom agents
contentType: how-tos
category:
  - Configure Copilot
  - Manage Copilot for a team
---

Enterprise-level {% data variables.copilot.custom_agents_short %} are defined in a `.github-private` repository within an organization in your enterprise. Preparing your enterprise involves creating that repository, configuring it as your source of governance, protecting your agent files, and deciding who can manage {% data variables.copilot.custom_agents_short %}.

Work through the following steps to set up your enterprise.

## Set up your governance repository

1. **Create a `.github-private` repository** to house your enterprise's {% data variables.copilot.agent_profiles %}, client permissions, and plugin settings.
1. **Select the repository as your source of governance** so that your enterprise reads its settings from the repository.

For both steps, see [AUTOTITLE](/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/create-github-private-repo).

## Protecting your agent files using rulesets

To automatically configure a ruleset that allows only enterprise owners to edit {% data variables.copilot.agent_profiles %} across your enterprise:

{% data reusables.enterprise-accounts.access-enterprise %}
{% data reusables.enterprise-accounts.ai-controls-tab %}
1. On the "Agents" tab, in the "Protect agent files using rulesets" section, click **Create ruleset**.

> [!NOTE]
> * Members of your enterprise with write access to the {% data variables.copilot.copilot_custom_agent_short %} repository can still create pull requests proposing changes to your {% data variables.copilot.agent_profiles %}. Enterprise members with bypass access to the ruleset can then merge those pull requests as they see fit.
> * Creating this ruleset will also block organization owners in your enterprise from creating or editing organization-level {% data variables.copilot.custom_agents_short %}. To prevent this, you can edit the ruleset to target only the organization containing your enterprise-level {% data variables.copilot.custom_agents_short %}.

## Decide who manages your custom agents

To reduce your administrative burden and empower your SMEs, you can delegate the creation and management of {% data variables.copilot.custom_agents_short %} in your enterprise by creating a team of AI managers. See [AUTOTITLE](/copilot/tutorials/roll-out-at-scale/govern-at-scale/establish-ai-managers).

If you prefer to maintain full control over your enterprise's tooling to ensure security and compliance, you can create and manage {% data variables.copilot.custom_agents_short %} yourself. See [AUTOTITLE](/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/test-custom-agents).

## Next steps

To centrally control {% data variables.product.prodname_copilot_short %} client behavior across your enterprise, configure enterprise managed settings. See [AUTOTITLE](/copilot/how-tos/administer-copilot/manage-for-enterprise/use-managed-settings/get-started).
