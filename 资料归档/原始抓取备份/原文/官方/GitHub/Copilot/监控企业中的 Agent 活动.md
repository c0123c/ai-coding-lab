---
vendor: "GitHub / Microsoft"
title: "Viewing active and recent agentic sessions in your enterprise"
official_url: https://raw.githubusercontent.com/github/docs/main/content/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/monitor-agentic-activity.md
resolved_url: https://raw.githubusercontent.com/github/docs/be38ec5d78e24172587e61b3c6ff40ace1865c71/content/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/monitor-agentic-activity.md
fetched_at: 2026-10-10T15:19:43+00:00
status: fetched
scope: core_ai_coding
source_kind: official_github_raw
repository_revision: be38ec5d78e24172587e61b3c6ff40ace1865c71
sha256_raw: 1b0ea1c66a0dedec2e3c00080c24684e53febe7e4d53fda9030e646d3ad2896f
sha256_body: 1b0ea1c66a0dedec2e3c00080c24684e53febe7e4d53fda9030e646d3ad2896f
---

# 官方正文

---
title: Monitoring agentic activity in your enterprise
intro: 'Track agentic activity in your enterprise to ensure continued compliance.'
permissions: Enterprise owners
versions:
  feature: copilot
shortTitle: Monitor agentic activity
contentType: how-tos
category:
  - Manage Copilot for a team
docsTeamMetrics:
  - ai-governance
---

## Viewing active and recent agentic sessions in your enterprise

{% data reusables.enterprise-accounts.access-enterprise %}
{% data reusables.enterprise-accounts.ai-controls-tab %}
1. Towards the top of the page, in the "Agent sessions" section, you will see the three most recent agent sessions in your enterprise. To view all agent sessions from the last 24 hours, click **View all**.
1. To filter agent sessions, click the search bar at the top of the list, then press <kbd>Space</kbd>. From the dropdown menu that appears, select your filter criteria. For a list of available filters, see [AUTOTITLE](/copilot/reference/enterprise-administrators/agent-session-filters).

## Tracking agentic activity in your enterprise through the audit log

Track agentic activity on {% data variables.product.github %} or through streaming to an external destination.

### Viewing agentic activity in the audit log on {% data variables.product.github %}

{% data reusables.enterprise-accounts.access-enterprise %}
{% data reusables.enterprise-accounts.ai-controls-tab %}
1. At the bottom of the page, click {% octicon "log" aria-hidden="true" aria-label="log" %} **Audit logs**.

### Streaming agentic activity from the audit log

{% data reusables.copilot.agent-session-streaming-availability-note %}

To enable streaming for {% data variables.product.prodname_copilot_short %} agent session events and configure a streaming destination from your enterprise audit log settings, see [AUTOTITLE](/enterprise-cloud@latest/admin/monitoring-activity-in-your-enterprise/reviewing-audit-logs-for-your-enterprise/streaming-the-audit-log-for-your-enterprise#enabling-audit-log-streaming-of-copilot-agent-session-events).

In addition to streaming, you can also retrieve Copilot usage data through the REST API. See [AUTOTITLE](/rest/copilot/copilot-usage-metrics#get-copilot-usage-records-for-an-enterprise).

## Enabling OpenTelemetry

OpenTelemetry (OTel) is an open source observability framework. It provides a standard way to collect telemetry events and metrics and export them to compatible observability tools.

When you enable OTel monitoring, you can send data from users' {% data variables.product.prodname_copilot_short %} clients to an OTel-compatible backend. This lets you analyze agent sessions and understand agent usage across your enterprise.

For more information, see [AUTOTITLE](/copilot/concepts/enterprise/opentelemetry).
