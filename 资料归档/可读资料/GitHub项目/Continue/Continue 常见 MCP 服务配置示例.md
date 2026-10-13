# Continue 常见 MCP 服务配置示例

配置示例集合。列出 Sentry、GitHub、Supabase、Netlify、Atlassian、Snyk、Chrome DevTools 等连接方式和后续指南；用于按开发需求选择服务，不是每个服务都要接入。

- 来源：Continue
- 资料类型：配置示例集合
- 存档版本：`5522c6f44ca0`。
- 文档时间：源码提交（存档版）：[2026-03-25](<https://github.com/continuedev/continue/commit/f14f6ef7b09a930c2fe61320ecc8cb24fc64b6e2>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：原文说明 MCP 仅用于 Agent 模式；各第三方服务条件独立。

[原文发布页](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/customize/deep-dives/mcp-examples.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Continue/Continue 常见 MCP 服务配置示例.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: "Example MCP Servers"
keywords: [mcp, integrations, sentry, slack, linear, posthog, supabase, netlify, atlassian, github, sanity, snyk, dlt, chrome-devtools]
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

适用于流行工具和服务的即用型 MCP 服务器配置。将这些复制到您的 `config.yaml` 中即可开始。

MCP服务器仅在**agent**模式下可用。有关配置概念和设置详细信息，请参阅 [MCP 深入探讨 ](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/customize/deep-dives/Continue_Continue MCP 设置、传输与凭据管理.md>)。

## 哨兵

Sentry 的 MCP 服务器提供了用于查询问题、查看堆栈跟踪和搜索事件的工具。

**远程（SSE）**：

```yaml
mcpServers:
  - name: Sentry
    type: sse
    url: https://mcp.sentry.io
```

**本地（stdio）** — 需要身份验证令牌：

```yaml
mcpServers:
  - name: Sentry
    command: npx
    args:
      - "-y"
      - "@sentry/mcp-server@latest"
      - "--transport"
      - "stdio"
    env:
      SENTRY_AUTH_TOKEN: ${{ secrets.SENTRY_AUTH_TOKEN }}
```

另请参阅：[Sentry MCP 错误监控指南](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/customize/deep-dives/Continue_用 Continue 与 Sentry MCP 分析生产错误.md>)

## 松弛

```yaml
mcpServers:
  - name: Slack
    command: npx
    args:
      - "-y"
      - "@anthropic/slack-mcp"
    env:
      SLACK_OAUTH_TOKEN: ${{ secrets.SLACK_OAUTH_TOKEN }}
```

## 线性

```yaml
mcpServers:
  - name: Linear
    type: sse
    url: https://mcp.linear.app/sse
    apiKey: ${{ secrets.LINEAR_OAUTH_TOKEN }}
```

## PostHog

```yaml
mcpServers:
  - name: PostHog
    type: streamable-http
    url: https://mcp.posthog.com/mcp
    apiKey: ${{ secrets.POSTHOG_API_KEY }}
```

另请参阅：[PostHog + GitHub 连续 AI 指南 ](<https://docs.continue.dev/guides/posthog-github-continuous-ai>)

## 苏帕贝斯

```yaml
mcpServers:
  - name: Supabase
    type: streamable-http
    url: https://mcp.supabase.com/mcp
    apiKey: ${{ secrets.SUPABASE_ACCESS_TOKEN }}
```

另请参阅：[Supabase MCP 数据库工作流程指南](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/customize/deep-dives/Continue_用 Continue 与 Supabase MCP 审查 RLS 和数据库权限.md>)

## 网络化

```yaml
mcpServers:
  - name: Netlify
    type: streamable-http
    url: https://openapi.netlify.com/v1/mcp
    apiKey: ${{ secrets.NETLIFY_API_TOKEN }}
```

另请参阅：[Netlify MCP 持续部署指南](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/customize/deep-dives/Continue_用 Continue 与 Netlify MCP 分析部署性能.md>)

## 阿特拉斯

```yaml
mcpServers:
  - name: Atlassian
    type: sse
    url: https://mcp.atlassian.com/v1/sse
    env:
      ATLASSIAN_API_TOKEN: ${{ secrets.ATLASSIAN_API_TOKEN }}
```

另请参阅：[Atlassian MCP 食谱](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/customize/deep-dives/Continue_用 Continue 与 Atlassian MCP 处理开发协作.md>)

## GitHub

```yaml
mcpServers:
  - name: GitHub
    command: npx
    args:
      - "-y"
      - "@modelcontextprotocol/server-github"
    env:
      GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

另请参阅：[GitHub MCP 食谱](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/customize/deep-dives/Continue_用 Continue 与 GitHub MCP 处理 Issue 和 PR.md>)

## 理智

```yaml
mcpServers:
  - name: Sanity
    command: npx
    args:
      - "-y"
      - "@sanity/mcp"
    env:
      SANITY_PROJECT_ID: ${{ secrets.SANITY_PROJECT_ID }}
      SANITY_DATASET: ${{ secrets.SANITY_DATASET }}
      SANITY_API_TOKEN: ${{ secrets.SANITY_API_TOKEN }}
      MCP_USER_ROLE: ${{ secrets.MCP_USER_ROLE }}
```

另请参阅：[理智MCP食谱](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/customize/deep-dives/Continue_用 Continue 与 Sanity MCP 开发内容管理流程.md>)

## 斯尼克

```yaml
mcpServers:
  - name: Snyk
    command: npx
    args:
      - "-y"
      - "@snyk/mcp"
    env:
      SNYK_API_TOKEN: ${{ secrets.SNYK_API_TOKEN }}
```

另请参阅：[Snyk MCP食谱](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/customize/deep-dives/Continue_用 Continue 与 Snyk MCP 执行安全扫描.md>)

## 数据表

```yaml
mcpServers:
  - name: dlt
    command: npx
    args:
      - "-y"
      - "dlthub/dlt-mcp"
```

另请参阅：[dlt MCP食谱](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/customize/deep-dives/Continue_用 Continue 与 dlt MCP 开发和排查数据流水线.md>)

## 镀铬 DevTools

```yaml
mcpServers:
  - name: Chrome DevTools
    command: npx
    args:
      - "-y"
      - "chrome-devtools-mcp@latest"
```

另请参阅：[Chrome DevTools MCP 指南](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/customize/deep-dives/Continue_用 Continue 与 Chrome DevTools MCP 检查网页性能.md>)

## 其他指南

这些集成有可用的指南，但使用直接的 API 集成而不是 MCP：

- [Notion](<https://docs.continue.dev/guides/notion-continue-guide>)


## 原文对照

<details>
<summary>展开完整原文</summary>

Ready-to-use MCP server configurations for popular tools and services. Copy these into your `config.yaml` to get started.

MCP servers are only available in **agent** mode. For configuration concepts and setup details, see the [MCP deep dive](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/customize/deep-dives/Continue_Continue MCP 设置、传输与凭据管理.md>).

## Sentry

Sentry's MCP server provides tools for querying issues, viewing stack traces, and searching events.

**Remote (SSE)**:

```yaml
mcpServers:
  - name: Sentry
    type: sse
    url: https://mcp.sentry.io
```

**Local (stdio)** — requires an auth token:

```yaml
mcpServers:
  - name: Sentry
    command: npx
    args:
      - "-y"
      - "@sentry/mcp-server@latest"
      - "--transport"
      - "stdio"
    env:
      SENTRY_AUTH_TOKEN: ${{ secrets.SENTRY_AUTH_TOKEN }}
```

See also: [Sentry MCP Error Monitoring guide](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/customize/deep-dives/Continue_用 Continue 与 Sentry MCP 分析生产错误.md>)

## Slack

```yaml
mcpServers:
  - name: Slack
    command: npx
    args:
      - "-y"
      - "@anthropic/slack-mcp"
    env:
      SLACK_OAUTH_TOKEN: ${{ secrets.SLACK_OAUTH_TOKEN }}
```

## Linear

```yaml
mcpServers:
  - name: Linear
    type: sse
    url: https://mcp.linear.app/sse
    apiKey: ${{ secrets.LINEAR_OAUTH_TOKEN }}
```

## PostHog

```yaml
mcpServers:
  - name: PostHog
    type: streamable-http
    url: https://mcp.posthog.com/mcp
    apiKey: ${{ secrets.POSTHOG_API_KEY }}
```

See also: [PostHog + GitHub Continuous AI guide](<https://docs.continue.dev/guides/posthog-github-continuous-ai>)

## Supabase

```yaml
mcpServers:
  - name: Supabase
    type: streamable-http
    url: https://mcp.supabase.com/mcp
    apiKey: ${{ secrets.SUPABASE_ACCESS_TOKEN }}
```

See also: [Supabase MCP Database Workflow guide](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/customize/deep-dives/Continue_用 Continue 与 Supabase MCP 审查 RLS 和数据库权限.md>)

## Netlify

```yaml
mcpServers:
  - name: Netlify
    type: streamable-http
    url: https://openapi.netlify.com/v1/mcp
    apiKey: ${{ secrets.NETLIFY_API_TOKEN }}
```

See also: [Netlify MCP Continuous Deployment guide](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/customize/deep-dives/Continue_用 Continue 与 Netlify MCP 分析部署性能.md>)

## Atlassian

```yaml
mcpServers:
  - name: Atlassian
    type: sse
    url: https://mcp.atlassian.com/v1/sse
    env:
      ATLASSIAN_API_TOKEN: ${{ secrets.ATLASSIAN_API_TOKEN }}
```

See also: [Atlassian MCP cookbook](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/customize/deep-dives/Continue_用 Continue 与 Atlassian MCP 处理开发协作.md>)

## GitHub

```yaml
mcpServers:
  - name: GitHub
    command: npx
    args:
      - "-y"
      - "@modelcontextprotocol/server-github"
    env:
      GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

See also: [GitHub MCP cookbook](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/customize/deep-dives/Continue_用 Continue 与 GitHub MCP 处理 Issue 和 PR.md>)

## Sanity

```yaml
mcpServers:
  - name: Sanity
    command: npx
    args:
      - "-y"
      - "@sanity/mcp"
    env:
      SANITY_PROJECT_ID: ${{ secrets.SANITY_PROJECT_ID }}
      SANITY_DATASET: ${{ secrets.SANITY_DATASET }}
      SANITY_API_TOKEN: ${{ secrets.SANITY_API_TOKEN }}
      MCP_USER_ROLE: ${{ secrets.MCP_USER_ROLE }}
```

See also: [Sanity MCP cookbook](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/customize/deep-dives/Continue_用 Continue 与 Sanity MCP 开发内容管理流程.md>)

## Snyk

```yaml
mcpServers:
  - name: Snyk
    command: npx
    args:
      - "-y"
      - "@snyk/mcp"
    env:
      SNYK_API_TOKEN: ${{ secrets.SNYK_API_TOKEN }}
```

See also: [Snyk MCP cookbook](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/customize/deep-dives/Continue_用 Continue 与 Snyk MCP 执行安全扫描.md>)

## dlt

```yaml
mcpServers:
  - name: dlt
    command: npx
    args:
      - "-y"
      - "dlthub/dlt-mcp"
```

See also: [dlt MCP cookbook](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/customize/deep-dives/Continue_用 Continue 与 dlt MCP 开发和排查数据流水线.md>)

## Chrome DevTools

```yaml
mcpServers:
  - name: Chrome DevTools
    command: npx
    args:
      - "-y"
      - "chrome-devtools-mcp@latest"
```

See also: [Chrome DevTools MCP guide](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/customize/deep-dives/Continue_用 Continue 与 Chrome DevTools MCP 检查网页性能.md>)

## Other guides

These integrations have guides available but use direct API integration rather than MCP:

- [Notion](<https://docs.continue.dev/guides/notion-continue-guide>)


</details>
