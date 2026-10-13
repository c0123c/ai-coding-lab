# 监控企业中的 Agent 活动

说明管理员如何查看活跃会话、使用审计日志和流式日志，并配置遥测，用于追踪企业内的 Agent 使用。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 文档时间：源码提交（存档版）：[2026-09-15](<https://github.com/github/docs/commit/1413b140620171c5e4494c52e28a736e4b68b36e>)；发表/更新：官方未公开；本地抓取：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://raw.githubusercontent.com/github/docs/main/content/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/monitor-agentic-activity.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/监控企业中的 Agent 活动.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
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
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

## 查看企业中活动的和最近的代理会话

{% data reusables.enterprise-accounts.access-enterprise %}
{% data reusables.enterprise-accounts.ai-controls-tab %}
1. 在页面顶部的“Agent 会话”部分中，您将看到企业中最近的三个 agent 会话。要查看过去 24 小时内的所有 agent 会话，请单击 **查看全部 **。
1. 要过滤 agent 会话，请单击列表顶部的搜索栏，然后按 <kbd>空格键</kbd>。从出现的下拉菜单中，选择您的过滤条件。有关可用过滤器的列表，请参阅 [AUTOTITLE](<https://raw.githubusercontent.com/copilot/reference/enterprise-administrators/agent-session-filters>)。

## 通过审核日志跟踪企业中的代理活动

跟踪 {% data variables.product.github %} 上的代理活动或通过流式传输到外部目的地。

### 在 {% data variables.product.github %} 上的审核日志中查看代理活动

{% data reusables.enterprise-accounts.access-enterprise %}
{% data reusables.enterprise-accounts.ai-controls-tab %}
1. 在页面底部，点击【{% octicon "log" aria-hidden="true" aria-label="log" %}】【**】审核日志【**】。

### 从审核日志流式传输代理活动

{% data reusables.copilot.agent-session-streaming-availability-note %}

要为 {% data variables.product.prodname_copilot_short %} agent 会话事件启用流式传输并从企业审核日志设置配置流式传输目标，请参阅 [AUTOTITLE](<https://raw.githubusercontent.com/enterprise-cloud@latest/admin/monitoring-activity-in-your-enterprise/reviewing-audit-logs-for-your-enterprise/streaming-the-audit-log-for-your-enterprise#enabling-audit-log-streaming-of-copilot-agent-session-events>)。

除了串流之外，您还可以通过RESTAPI检索Copilot使用数据。参见[AUTOTITLE](<https://raw.githubusercontent.com/rest/copilot/copilot-usage-metrics#get-copilot-usage-records-for-an-enterprise>)。

## 启用OpenTelemetry

OpenTelemetry (OTel) 是一个开源可观察性框架。它提供了一种收集遥测事件和指标并将其导出到兼容的可观察性工具的标准方法。

当您启用 OTel 监控时，您可以将数据从用户的 {% data variables.product.prodname_copilot_short %} 客户端发送到 OTel 兼容后端。这使您可以分析 agent 会话并了解整个企业中的 agent 使用情况。

欲了解更多信息，请参阅[AUTOTITLE](<https://raw.githubusercontent.com/copilot/concepts/enterprise/opentelemetry>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

## Viewing active and recent agentic sessions in your enterprise

{% data reusables.enterprise-accounts.access-enterprise %}
{% data reusables.enterprise-accounts.ai-controls-tab %}
1. Towards the top of the page, in the "Agent sessions" section, you will see the three most recent agent sessions in your enterprise. To view all agent sessions from the last 24 hours, click **View all**.
1. To filter agent sessions, click the search bar at the top of the list, then press <kbd>Space</kbd>. From the dropdown menu that appears, select your filter criteria. For a list of available filters, see [AUTOTITLE](<https://raw.githubusercontent.com/copilot/reference/enterprise-administrators/agent-session-filters>).

## Tracking agentic activity in your enterprise through the audit log

Track agentic activity on {% data variables.product.github %} or through streaming to an external destination.

### Viewing agentic activity in the audit log on {% data variables.product.github %}

{% data reusables.enterprise-accounts.access-enterprise %}
{% data reusables.enterprise-accounts.ai-controls-tab %}
1. At the bottom of the page, click {% octicon "log" aria-hidden="true" aria-label="log" %} **Audit logs**.

### Streaming agentic activity from the audit log

{% data reusables.copilot.agent-session-streaming-availability-note %}

To enable streaming for {% data variables.product.prodname_copilot_short %} agent session events and configure a streaming destination from your enterprise audit log settings, see [AUTOTITLE](<https://raw.githubusercontent.com/enterprise-cloud@latest/admin/monitoring-activity-in-your-enterprise/reviewing-audit-logs-for-your-enterprise/streaming-the-audit-log-for-your-enterprise#enabling-audit-log-streaming-of-copilot-agent-session-events>).

In addition to streaming, you can also retrieve Copilot usage data through the REST API. See [AUTOTITLE](<https://raw.githubusercontent.com/rest/copilot/copilot-usage-metrics#get-copilot-usage-records-for-an-enterprise>).

## Enabling OpenTelemetry

OpenTelemetry (OTel) is an open source observability framework. It provides a standard way to collect telemetry events and metrics and export them to compatible observability tools.

When you enable OTel monitoring, you can send data from users' {% data variables.product.prodname_copilot_short %} clients to an OTel-compatible backend. This lets you analyze agent sessions and understand agent usage across your enterprise.

For more information, see [AUTOTITLE](<https://raw.githubusercontent.com/copilot/concepts/enterprise/opentelemetry>).


</details>
