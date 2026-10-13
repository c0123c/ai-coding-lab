---
vendor: "GitHub"
repository: "github/docs"
path: "content/copilot/how-tos/administer-copilot/manage-mcp-usage/restrict-based-on-registry.md"
source_url: "https://github.com/github/docs/blob/main/content/copilot/how-tos/administer-copilot/manage-mcp-usage/restrict-based-on-registry.md"
raw_url: "https://raw.githubusercontent.com/github/docs/main/content/copilot/how-tos/administer-copilot/manage-mcp-usage/restrict-based-on-registry.md"
resolved_url: "https://raw.githubusercontent.com/github/docs/main/content/copilot/how-tos/administer-copilot/manage-mcp-usage/restrict-based-on-registry.md"
tree_snapshot_sha: "be38ec5d78e24172587e61b3c6ff40ace1865c71"
sha256_body: "56926c82123451dbd393e1a3dfb10a4809c2a004cd859d7b23b9a8bb5034d056"
archive: "01_资料来源/官方资料/审计记录/官方GitHub相关正文_2026-10-10/official-github-pages-0004.jsonl.gz:54"
---

---
title: Restrict MCP server access to a custom registry
intro: You can configure an MCP registry URL and access control policy to determine which MCP servers developers can discover and use in supported IDEs and {% data variables.copilot.copilot_cli_short %}.
permissions: Enterprise owners and organization owners
product: '{% data variables.copilot.copilot_enterprise_short %} or {% data variables.copilot.copilot_business_short %}'
versions:
  feature: copilot
shortTitle: Restrict based on registry
redirect_from:
  - /copilot/how-tos/administer-copilot/configure-mcp-server-access
  - /copilot/how-tos/administer-copilot/manage-for-organization/set-extension-permissions
  - /copilot/how-tos/administer-copilot/manage-mcp-usage/configure-mcp-server-access
contentType: how-tos
category:
  - Manage Copilot for a team
---

> [!IMPORTANT] {% data reusables.copilot.mcp.registry-alternative %}

## Prerequisites

Before you can fully configure MCP server access for your company, you need to create an MCP registry. See [AUTOTITLE](/copilot/how-tos/administer-copilot/manage-mcp-usage/configure-mcp-registry).

## Configuring the MCP allowlist policy for an enterprise

To ensure uniform access, you can set and maintain your MCP registry URL and allowlist policy at the enterprise level. Otherwise, if your teams have different needs, you should configure separate policies for each organization.

{% data reusables.enterprise-accounts.access-enterprise %}
{% data reusables.enterprise-accounts.ai-controls-tab %}
{% data reusables.enterprise-accounts.view-mcp-policies %}
1. Ensure **MCP servers in {% data variables.product.prodname_copilot_short %}** is set to **Enabled everywhere**.
1. In the **MCP Registry URL** section, enter the URL of your registry, then click **Save**.

    {% data reusables.copilot.mcp.azure-api-center-url %}

1. In the **Restrict MCP access to registry servers** section, select the dropdown menu, then click one of the following options:
   * **Allow all**: No restrictions. All MCP servers can be used.
   * **Registry only**: Only servers from the registry may run.

    Your chosen policy will immediately apply to developers in your enterprise.

## Configuring the MCP allowlist policy for an organization

{% data reusables.profile.access_org %}
{% data reusables.profile.org_settings %}
1. {% data reusables.user-settings.code-planning-automation %} click **{% octicon "copilot" aria-hidden="true" aria-label="copilot" %} {% data variables.product.prodname_copilot_short %}**, then click **Policies**.
1. In the "Features" section, ensure **MCP servers in {% data variables.product.prodname_copilot_short %}** is set to **Enabled**.
1. In the **MCP Registry URL (optional)** field, enter the URL of your registry, then click **Save**.

    {% data reusables.copilot.mcp.azure-api-center-url %}

1. In the **Restrict MCP access to registry servers** section, select the dropdown menu, then click one of the following options:
   * **Allow all**: No restrictions. All MCP servers can be used.
   * **Registry only**: Only servers from the registry may run.

    Your chosen policy will immediately apply to developers in your organization.

## Next steps

For detailed information on MCP allowlist enforcement and limitations, see [AUTOTITLE](/copilot/reference/enterprise-administrators/mcp-private-registry-enforcement).

## Further reading

* [AUTOTITLE](/copilot/reference/supported-surfaces-for-policies)
