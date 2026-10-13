# Cloudflare 部署技能

按计算、存储、网络和基础设施需求定位服务，检查认证并执行 Workers 或 Pages 等部署；属于官方技能实现。

- 来源：OpenAI／Skills
- 资料类型：存档文档
- 存档版本：`49f948faa925`。
- 文档时间：源码提交（存档版）：[2026-02-02](<https://github.com/openai/skills/commit/bf9e226541ec801750547acfa8cf761b7c878f27>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/skills/blob/main/skills/.curated/cloudflare-deploy/SKILL.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Skills/Cloudflare 部署技能.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: cloudflare-deploy
description: Deploy applications and infrastructure to Cloudflare using Workers, Pages, and related platform services. Use when the user asks to deploy, host, publish, or set up a project on Cloudflare.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# Cloudflare 部署

在 Cloudflare 平台上构建的综合技能。使用下面的决策树找到合适的产品，然后加载详细的参考资料。

## 先决条件

- 当沙箱阻止部署网络调用时，请使用 `sandbox_permissions=require_escalated` 重新运行。
- 部署可能需要几分钟时间。使用适当的超时值。

## 身份验证（部署前需要）

在 `wrangler deploy`、`wrangler pages deploy` 或 `npm run deploy` 之前验证身份验证：

```bash
npx wrangler whoami    # Shows account if authenticated
```

未认证？ → `references/wrangler/auth.md`
- Interactive/local: `wrangler login`（一次性 OAuth）
- CI/CD：设置`CLOUDFLARE_API_TOKEN`环境变量

## 快速决策树

### “我需要运行代码”

```
Need to run code?
├─ Serverless functions at the edge → workers/
├─ Full-stack web app with Git deploys → pages/
├─ Stateful coordination/real-time → durable-objects/
├─ Long-running multi-step jobs → workflows/
├─ Run containers → containers/
├─ Multi-tenant (customers deploy code) → workers-for-platforms/
├─ Scheduled tasks (cron) → cron-triggers/
├─ Lightweight edge logic (modify HTTP) → snippets/
├─ Process Worker execution events (logs/observability) → tail-workers/
└─ Optimize latency to backend infrastructure → smart-placement/
```

### “我需要存储数据”

```
Need storage?
├─ Key-value (config, sessions, cache) → kv/
├─ Relational SQL → d1/ (SQLite) or hyperdrive/ (existing Postgres/MySQL)
├─ Object/file storage (S3-compatible) → r2/
├─ Message queue (async processing) → queues/
├─ Vector embeddings (AI/semantic search) → vectorize/
├─ Strongly-consistent per-entity state → durable-objects/ (DO storage)
├─ Secrets management → secrets-store/
├─ Streaming ETL to R2 → pipelines/
└─ Persistent cache (long-term retention) → cache-reserve/
```

### “我需要AI/ML”

```
Need AI?
├─ Run inference (LLMs, embeddings, images) → workers-ai/
├─ Vector database for RAG/search → vectorize/
├─ Build stateful AI agents → agents-sdk/
├─ Gateway for any AI provider (caching, routing) → ai-gateway/
└─ AI-powered search widget → ai-search/
```

### “我需要networking/connectivity”

```
Need networking?
├─ Expose local service to internet → tunnel/
├─ TCP/UDP proxy (non-HTTP) → spectrum/
├─ WebRTC TURN server → turn/
├─ Private network connectivity → network-interconnect/
├─ Optimize routing → argo-smart-routing/
├─ Optimize latency to backend (not user) → smart-placement/
└─ Real-time video/audio → realtimekit/ or realtime-sfu/
```

### “我需要安全感”

```
Need security?
├─ Web Application Firewall → waf/
├─ DDoS protection → ddos/
├─ Bot detection/management → bot-management/
├─ API protection → api-shield/
├─ CAPTCHA alternative → turnstile/
└─ Credential leak detection → waf/ (managed ruleset)
```

### “我需要media/content”

```
Need media?
├─ Image optimization/transformation → images/
├─ Video streaming/encoding → stream/
├─ Browser automation/screenshots → browser-rendering/
└─ Third-party script management → zaraz/
```

### “我需要基础设施即代码”

```
Need IaC? → pulumi/ (Pulumi), terraform/ (Terraform), or api/ (REST API)
```

## 产品索引

### 计算和运行时
|产品展示|参考|
|---------|-----------|
|工人| `references/workers/` |
|页数| `references/pages/` |
|页面功能| `references/pages-functions/` |
|耐用的物体| `references/durable-objects/` |
|工作流程| `references/workflows/` |
|集装箱| `references/containers/` |
|平台工作者| `references/workers-for-platforms/` |
|计划任务触发器| `references/cron-triggers/` |
|尾工| `references/tail-workers/` |
|片段| `references/snippets/` |
|智能布局| `references/smart-placement/` |

### 存储和数据
|产品展示|参考|
|---------|-----------|
| KV | `references/kv/` |
| D1 | `references/d1/` |
| R2 | `references/r2/` |
|队列| `references/queues/` |
|超光速引擎| `references/hyperdrive/` |
|DO 存储| `references/do-storage/` |
|秘密商店| `references/secrets-store/` |
|管道| `references/pipelines/` |
|R2 数据目录| `references/r2-data-catalog/` |
| R2 SQL | `references/r2-sql/` |

### AI 与机器学习
|产品展示|参考|
|---------|-----------|
|工人AI| `references/workers-ai/` |
|矢量化| `references/vectorize/` |
| Agents SDK | `references/agents-sdk/` |
|AI 网关| `references/ai-gateway/` |
|AI 搜索| `references/ai-search/` |

### 网络与连接
|产品展示|参考|
|---------|-----------|
|隧道| `references/tunnel/` |
|光谱| `references/spectrum/` |
| TURN | `references/turn/` |
|网络互连| `references/network-interconnect/` |
|Argo智能路由| `references/argo-smart-routing/` |
|工人VPC| `references/workers-vpc/` |

### 安全性
|产品展示|参考|
|---------|-----------|
| WAF | `references/waf/` |
|DDoS 防护| `references/ddos/` |
|机器人管理| `references/bot-management/` |
|API 盾牌| `references/api-shield/` |
|旋转门| `references/turnstile/` |

### 媒体与内容
|产品展示|参考|
|---------|-----------|
|图片| `references/images/` |
|串流| `references/stream/` |
|浏览器渲染| `references/browser-rendering/` |
|扎拉兹| `references/zaraz/` |

### 实时通讯
|产品展示|参考|
|---------|-----------|
| RealtimeKit | `references/realtimekit/` |
|实时 SFU| `references/realtime-sfu/` |

### 开发者工具
|产品展示|参考|
|---------|-----------|
|牧马人| `references/wrangler/` |
|迷你耀斑| `references/miniflare/` |
| C3 | `references/c3/` |
|可观察性| `references/observability/` |
|分析引擎| `references/analytics-engine/` |
|网络分析| `references/web-analytics/` |
|沙盒| `references/sandbox/` |
|工人德| `references/workerd/` |
|工人游乐场| `references/workers-playground/` |

### 基础设施即代码
|产品展示|参考|
|---------|-----------|
|普鲁米| `references/pulumi/` |
|地形| `references/terraform/` |
| API | `references/api/` |

### 其他服务
|产品展示|参考|
|---------|-----------|
|电子邮件路由| `references/email-routing/` |
|电子邮件工作者| `references/email-workers/` |
|静态资源| `references/static-assets/` |
|绑定| `references/bindings/` |
|缓存预留| `references/cache-reserve/` |

## 故障排除

### 升级网络访问

如果部署由于网络问题（超时、DNS 错误、连接重置）而失败，请使用升级的权限重新运行部署（使用 `sandbox_permissions=require_escalated`）。当沙箱网络阻止出站请求时，部署需要升级网络访问。

对用户的指导示例：

```
The deploy needs escalated network access to deploy to Cloudflare. I can rerun the command with escalated permissions—want me to proceed?
```


## 原文对照

<details>
<summary>展开完整原文</summary>

# Cloudflare Deploy

Consolidated skill for building on the Cloudflare platform. Use decision trees below to find the right product, then load detailed references.

## Prerequisites

- When sandboxing blocks the deployment network calls, rerun with `sandbox_permissions=require_escalated`.
- The deployment might take a few minutes. Use appropriate timeout values.

## Authentication (Required Before Deploy)

Verify auth before `wrangler deploy`, `wrangler pages deploy`, or `npm run deploy`:

```bash
npx wrangler whoami    # Shows account if authenticated
```

Not authenticated? → `references/wrangler/auth.md`
- Interactive/local: `wrangler login` (one-time OAuth)
- CI/CD: Set `CLOUDFLARE_API_TOKEN` env var

## Quick Decision Trees

### "I need to run code"

```
Need to run code?
├─ Serverless functions at the edge → workers/
├─ Full-stack web app with Git deploys → pages/
├─ Stateful coordination/real-time → durable-objects/
├─ Long-running multi-step jobs → workflows/
├─ Run containers → containers/
├─ Multi-tenant (customers deploy code) → workers-for-platforms/
├─ Scheduled tasks (cron) → cron-triggers/
├─ Lightweight edge logic (modify HTTP) → snippets/
├─ Process Worker execution events (logs/observability) → tail-workers/
└─ Optimize latency to backend infrastructure → smart-placement/
```

### "I need to store data"

```
Need storage?
├─ Key-value (config, sessions, cache) → kv/
├─ Relational SQL → d1/ (SQLite) or hyperdrive/ (existing Postgres/MySQL)
├─ Object/file storage (S3-compatible) → r2/
├─ Message queue (async processing) → queues/
├─ Vector embeddings (AI/semantic search) → vectorize/
├─ Strongly-consistent per-entity state → durable-objects/ (DO storage)
├─ Secrets management → secrets-store/
├─ Streaming ETL to R2 → pipelines/
└─ Persistent cache (long-term retention) → cache-reserve/
```

### "I need AI/ML"

```
Need AI?
├─ Run inference (LLMs, embeddings, images) → workers-ai/
├─ Vector database for RAG/search → vectorize/
├─ Build stateful AI agents → agents-sdk/
├─ Gateway for any AI provider (caching, routing) → ai-gateway/
└─ AI-powered search widget → ai-search/
```

### "I need networking/connectivity"

```
Need networking?
├─ Expose local service to internet → tunnel/
├─ TCP/UDP proxy (non-HTTP) → spectrum/
├─ WebRTC TURN server → turn/
├─ Private network connectivity → network-interconnect/
├─ Optimize routing → argo-smart-routing/
├─ Optimize latency to backend (not user) → smart-placement/
└─ Real-time video/audio → realtimekit/ or realtime-sfu/
```

### "I need security"

```
Need security?
├─ Web Application Firewall → waf/
├─ DDoS protection → ddos/
├─ Bot detection/management → bot-management/
├─ API protection → api-shield/
├─ CAPTCHA alternative → turnstile/
└─ Credential leak detection → waf/ (managed ruleset)
```

### "I need media/content"

```
Need media?
├─ Image optimization/transformation → images/
├─ Video streaming/encoding → stream/
├─ Browser automation/screenshots → browser-rendering/
└─ Third-party script management → zaraz/
```

### "I need infrastructure-as-code"

```
Need IaC? → pulumi/ (Pulumi), terraform/ (Terraform), or api/ (REST API)
```

## Product Index

### Compute & Runtime
| Product | Reference |
|---------|-----------|
| Workers | `references/workers/` |
| Pages | `references/pages/` |
| Pages Functions | `references/pages-functions/` |
| Durable Objects | `references/durable-objects/` |
| Workflows | `references/workflows/` |
| Containers | `references/containers/` |
| Workers for Platforms | `references/workers-for-platforms/` |
| Cron Triggers | `references/cron-triggers/` |
| Tail Workers | `references/tail-workers/` |
| Snippets | `references/snippets/` |
| Smart Placement | `references/smart-placement/` |

### Storage & Data
| Product | Reference |
|---------|-----------|
| KV | `references/kv/` |
| D1 | `references/d1/` |
| R2 | `references/r2/` |
| Queues | `references/queues/` |
| Hyperdrive | `references/hyperdrive/` |
| DO Storage | `references/do-storage/` |
| Secrets Store | `references/secrets-store/` |
| Pipelines | `references/pipelines/` |
| R2 Data Catalog | `references/r2-data-catalog/` |
| R2 SQL | `references/r2-sql/` |

### AI & Machine Learning
| Product | Reference |
|---------|-----------|
| Workers AI | `references/workers-ai/` |
| Vectorize | `references/vectorize/` |
| Agents SDK | `references/agents-sdk/` |
| AI Gateway | `references/ai-gateway/` |
| AI Search | `references/ai-search/` |

### Networking & Connectivity
| Product | Reference |
|---------|-----------|
| Tunnel | `references/tunnel/` |
| Spectrum | `references/spectrum/` |
| TURN | `references/turn/` |
| Network Interconnect | `references/network-interconnect/` |
| Argo Smart Routing | `references/argo-smart-routing/` |
| Workers VPC | `references/workers-vpc/` |

### Security
| Product | Reference |
|---------|-----------|
| WAF | `references/waf/` |
| DDoS Protection | `references/ddos/` |
| Bot Management | `references/bot-management/` |
| API Shield | `references/api-shield/` |
| Turnstile | `references/turnstile/` |

### Media & Content
| Product | Reference |
|---------|-----------|
| Images | `references/images/` |
| Stream | `references/stream/` |
| Browser Rendering | `references/browser-rendering/` |
| Zaraz | `references/zaraz/` |

### Real-Time Communication
| Product | Reference |
|---------|-----------|
| RealtimeKit | `references/realtimekit/` |
| Realtime SFU | `references/realtime-sfu/` |

### Developer Tools
| Product | Reference |
|---------|-----------|
| Wrangler | `references/wrangler/` |
| Miniflare | `references/miniflare/` |
| C3 | `references/c3/` |
| Observability | `references/observability/` |
| Analytics Engine | `references/analytics-engine/` |
| Web Analytics | `references/web-analytics/` |
| Sandbox | `references/sandbox/` |
| Workerd | `references/workerd/` |
| Workers Playground | `references/workers-playground/` |

### Infrastructure as Code
| Product | Reference |
|---------|-----------|
| Pulumi | `references/pulumi/` |
| Terraform | `references/terraform/` |
| API | `references/api/` |

### Other Services
| Product | Reference |
|---------|-----------|
| Email Routing | `references/email-routing/` |
| Email Workers | `references/email-workers/` |
| Static Assets | `references/static-assets/` |
| Bindings | `references/bindings/` |
| Cache Reserve | `references/cache-reserve/` |

## Troubleshooting

### Escalated Network Access

If deployment fails due to network issues (timeouts, DNS errors, connection resets), rerun the deploy with escalated permissions (use `sandbox_permissions=require_escalated`). The deploy requires escalated network access when sandbox networking blocks outbound requests.

Example guidance to the user:

```
The deploy needs escalated network access to deploy to Cloudflare. I can rerun the command with escalated permissions—want me to proceed?
```


</details>
