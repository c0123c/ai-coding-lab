# 用 Continue 与 Netlify MCP 分析部署性能

工程教程。围绕性能基线、分支 A/B、构建与包分析、图像优化、Lighthouse CI 预算和真实用户监测组织流程，包含本地及 GitHub Actions 检查。

- 来源：Continue
- 资料类型：工程教程
- 存档版本：`5522c6f44ca0`。
- 文档时间：源码提交（存档版）：[2026-03-24](<https://github.com/continuedev/continue/commit/f7f3ab7440341f052642b7d0fab72d40fd8e7ad0>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：针对 Netlify 网站和相关监测服务；功能、阈值和部署条件需验证。

[原文发布页](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/guides/netlify-mcp-continuous-deployment.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Continue/用 Continue 与 Netlify MCP 分析部署性能.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: "Netlify Performance Optimization Cookbook"
description: "Optimize web performance with A/B testing, automated monitoring, and data-driven improvements using Netlify MCP and Continue."
sidebarTitle: "Using Netlify MCP for Performance Optimization"
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

> 阅读说明：原站会自动选择操作系统选项；本地阅读版已展开所有安装选项。

#### 交付更快的代码，而不是更慢的站点

使用 AI 自动监控性能指标，在 
 分支之间运行 A/B 测试，并根据来自 Netlify Analytics 的真实用户数据 
 获取可行的优化建议

**说明：**

**你知道吗？** Netlify 不仅仅是静态托管！它提供：
- [针对 A/B 测试分支的拆分测试 ](<https://docs.netlify.com/site-deploys/split-testing/>)
- [Analytics](<https://docs.netlify.com/analytics/get-started/>) 具有 Core Web Vitals 跟踪
- [边缘功能](<https://docs.netlify.com/edge-functions/overview/>)用于边缘个性化
- [通过 100+ 集成构建插件 ](<https://docs.netlify.com/integrations/build-plugins/>) 生态系统
- [表单](<https://docs.netlify.com/forms/setup/>)具有内置垃圾邮件防护
- [身份](<https://docs.netlify.com/visitor-access/identity/>)用于用户身份验证
- [大型媒体](<https://docs.netlify.com/large-media/overview/>) for Git LFS 支持

本指南向您展示如何使用 Continue CLI 通过自然语言利用这些功能！

## 你将学到什么

这本食谱教你：

- 在分支之间运行 [A/B 测试 ](<https://docs.netlify.com/site-deploys/split-testing/>) 以衡量性能影响
- 监控 [Core Web Vitals](<https://docs.netlify.com/analytics/get-started/#core-web-vitals>) 并构建性能指标
- 使用 [部署上下文 ](<https://docs.netlify.com/site-deploys/overview/#deploy-contexts>) 自动阻止会降低性能的部署
- 使用 [ 构建插件 ](<https://docs.netlify.com/integrations/build-plugins/>) 优化构建时间，并使用 AI 帮助优化捆绑包大小

## 先决条件

- GitHub 带有 Web 项目的存储库
- [Netlify 帐户](<https://netlify.com>)（免费层作品）
- Node.js 22+ 已安装（Netlify 需要）
- [Continue CLI](<https://docs.continue.dev/guides/cli>)
- Netlify MCP 已配置
- Netlify开发规则（推荐）

## 快速设置

**说明：**

Netlify 开发规则捆绑包包含以下防护栏：
- 正确的 `.gitignore` 配置
- 功能结构和布局
- 边函数约束
- 本地开发最佳实践

对于所有选项，首先：

#### 安装ContinueCLI

#### macOS/Linux

```bash
curl -fsSL https://raw.githubusercontent.com/continuedev/continue/main/extensions/cli/scripts/install.sh | bash
```

#### 窗户

```powershell
irm https://raw.githubusercontent.com/continuedev/continue/main/extensions/cli/scripts/install.ps1 | iex
```

#### npm（跨平台）

如果您已经有 Node.js 20+：
```bash
npm i -g @continuedev/cli
```

#### 安装 Netlify CLI

1. 安装 Netlify CLI：`npm i -g netlify-cli`
2. 使用 Netlify 进行身份验证：
`netlify login`

## Netlify Continuous AI 工作流程选项

#### 🚀 最快的成功之路

跳过手动设置并使用我们预构建的 Netlify Continuous AI agent，其中包括 
 优化的提示、规则和 Netlify MCP 以获得更一致的结果。

完成上面的**快速设置**后，您有两条路径可以开始：

#### ⚡ 快速入门（推荐）

#### 添加预建的 Agent

运行：

```bash
cn --agent continuedev/netlify-continuous-ai-agent
```

此 agent 包括：
- **内置规则**用于一致的格式和错误处理
- **Netlify MCP** 实现更可靠的 API 交互

#### 验证您的设置

agent 自动使用 Netlify MCP，并包含部署和性能优化的最佳实践规则。

#### 运行性能分析

从您的项目目录中，运行：
```bash
cn "Analyze my Netlify site's performance and optimize it for better Core Web Vitals."
```

就是这样！ agent 自动处理一切。

**说明：**

**为什么使用Agent？** 由于 Netlify MCP 集成和预先测试的提示，结果更加一致，调试也更加容易。您可以稍后重新混合 agent 以根据您的需求进行自定义。

#### 🛠️手动设置

#### 配置 Netlify MCP

1. 使用 Netlify 进行身份验证：
`netlify login`
2. 将 Netlify MCP 添加到您的配置中
3. 按照您的编辑器的配置说明进行操作

#### 添加开发规则

安装 Netlify 开发规则捆绑包以获得最佳实践。将这些规则添加到您的 agent 配置中。

#### 验证设置

测试与cnCLI的连接：

```bash
cn
# Then in TUI mode:
"Check my Netlify auth and list sites"
```

**说明：**

你都准备好了！现在您可以使用 cn CLI 使用自然语言提示与 Netlify 进行交互。查看下面的示例以开始使用。

#### Agent 要求

要使用预构建的 agent，您需要：
    - **Continue CLI 专业计划 ** 以及模型附加组件，OR
    - **您自己的API密钥**配置为环境变量

agent 将自动检测并使用您的配置以及 Netlify MCP 进行部署操作。

---

## 性能优化步骤

### 步骤 1：基准性能指标

使用 cn CLI 建立您当前的性能基准：

```bash
# Start cn in TUI mode
cn

# Then ask:
"Show my site's Core Web Vitals and build times"
```

**提示：**

**首先在计划模式下测试**：在进行
可能影响您的站点的性能优化之前，请在计划模式下测试您的提示（请参阅[计划模式
指南](<https://github.com/guides/plan-mode-guide>)；按**Shift+Tab** 切换模式）。这个
向您展示了agent在不执行它的情况下会做什么。例如：“通过性能监控在主分支和功能分支之间设置
A/B测试”

**说明：**

Netlify 自动跟踪：
  - **构建性能**：编译时间、缓存
点击数
  - **运行时性能**：核心 Web 生命周期、交互时间
  -  **资源使用情况**：带宽、函数执行次数

### 步骤 2：A/B 测试分支性能

比较分支之间的性能：

```bash
# In cn TUI mode:
"Set up A/B test between main and feature branch on Netlify:
- Split traffic 50/50 between branches
- Track Core Web Vitals for each variant
- Monitor conversion metrics and bounce rate
- Enable analytics to measure performance impact
- Configure cookie-based visitor persistence
- Set test duration for 1000 unique visitors
- Auto-conclude test when statistical significance reached
Report winner based on performance + conversion metrics"
```

**说明：**

**增强分析**：将Netlify的A/B测试与PostHog会话
记录相结合，不仅可以了解哪个变体表现更好，还可以了解用户
行为不同的原因。请参阅我们的 [PostHog 会话分析 
 指南 ](<https://github.com/guides/posthog-github-continuous-ai>) 以设置会话跟踪并 
 创建完整的连续 AI 分析工作流程。

### 步骤 3：使用 Netlify 进行高级构建优化

利用 Netlify 强大的构建功能显着缩短构建时间：

```bash
# In cn TUI mode:
"Optimize my Netlify build performance using these features:

1. Enable Netlify Cache Plugin for dependency caching
   - Configure @netlify/plugin-cache for node_modules
   - Set up custom cache directories for .next/cache or .nuxt
   - Enable Gatsby's incremental builds if applicable

2. Set up Build Plugins from Netlify's ecosystem:
   - Install @netlify/plugin-lighthouse for performance monitoring
   - Add netlify-plugin-checklinks to prevent broken links
   - Configure netlify-plugin-submit-sitemap for SEO

3. Implement Conditional Builds:
   - Skip builds when only docs change (ignore: /docs/**)
   - Use build.ignore script for custom logic
   - Set up monorepo-specific build triggers

4. Configure concurrent builds for monorepos
   - Set base directory per package
   - Use pnpm workspaces or yarn workspaces

Show me the netlify.toml configuration and explain each optimization"
```

**说明：**

**您将获得的 Netlify 构建功能**：
- [构建缓存](<https://docs.netlify.com/configure-builds/build-caching/>) - 保留构建之间的依赖关系
- [构建插件](<https://docs.netlify.com/integrations/build-plugins/>) - 使用 100+ 插件扩展构建过程
- [条件构建](<https://docs.netlify.com/configure-builds/ignore-builds/>) - 跳过不必要的构建
- [Monorepo 支持](<https://docs.netlify.com/configure-builds/monorepos/>) - 优化多包存储库

对于大多数项目来说，这些功能可以将构建时间缩短 50-70%！

**提示：**

**发现更多优化提示！** Netlify 社区在其 [支持指南中记录了数十种构建优化策略：如何优化我的 Netlify 构建时间](<https://answers.netlify.com/t/support-guide-how-can-i-optimize-my-netlify-build-time/3907>)。

使用本指南作为本食谱中未涵盖的 cn CLI 提示的灵感，例如：
- `"Configure my builds to skip Dependabot PRs automatically"`
- `"Set up custom ignore patterns for documentation-only changes"`
- `"Optimize my Contentful webhooks to prevent duplicate builds"`
- `"Show me how to use build hooks instead of automatic git triggers"`
- `"Help me choose between Astro and Hugo based on build performance"`

社区指南包含真实场景，您可以将其转化为 AI- 辅助解决方案 - 只需描述您想要实现的目标，然后让 cn CLI 处理实施！

### 步骤 4：使用 Netlify 的内置工具进行捆绑分析

使用 Netlify 的捆绑分析器和优化功能：

```bash
# In cn TUI mode:
"Analyze and optimize my bundle using Netlify's tools:

1. Enable Netlify Bundle Analyzer:
   - Add @netlify/plugin-bundle-analyzer to plugins
   - Configure size thresholds in netlify.toml
   - Generate visual bundle reports

2. Set up Asset Optimization:
   - Enable automatic JS minification
   - Configure CSS optimization
   - Turn on HTML minification
   - Set up image processing pipeline

3. Implement Smart Code Splitting:
   - Analyze current chunks with the bundle analyzer
   - Identify components over 50KB for splitting
   - Configure webpack/vite for optimal chunking
   - Set up route-based code splitting

4. Configure Netlify's CDN for optimal delivery:
   - Set cache headers for static assets
   - Enable Brotli compression
   - Configure edge caching rules

Generate a full report with before/after bundle sizes"
```

#### Netlify 的性能阿森纳

**通过 Netlify 提供的功能 MCP**：
  - [捆绑包
分析器](https://docs.netlify.com/configure-builds/build-plugins/bundle-analyzer/)：可视化您的JavaScript包
  - [资产
优化](https://docs.netlify.com/configure-builds/post-processing/)：
自动缩小和压缩
  - [边缘
网络](https://docs.netlify.com/platform/edge-network/)：全局CDN和
智能缓存
  - [部署
预览](https://docs.netlify.com/site-deploys/deploy-previews/)：生产前测试
优化

### 步骤5：图像优化

优化图像以获得更好的性能：

```bash
# In cn TUI mode:
"Set up Cloudinary image optimization for my Netlify site:
- Install @cloudinary/netlify-plugin via MCP
- Auto-convert images to WebP with fallbacks
- Generate responsive sizes (320w, 640w, 1024w, 1920w)
- Add lazy loading for all images
- Configure blur-up placeholders
- Update netlify.toml with Cloudinary settings
Target: Reduce image payload by 60-80% and improve LCP"
```

## 持续绩效监控

### 步骤 6：使用 Lighthouse CI 执行绩效预算

通过自动化测试设置和执行性能预算：

```bash
# In cn TUI mode:
"Set up Lighthouse CI with performance budgets for my Netlify site:

Requirements:
- LCP must be < 2.5 seconds
- JavaScript bundle must be < 200KB
- Total size < 500KB
- Performance score >= 90

Setup needed:
1. Install @lhci/cli and @netlify/plugin-lighthouse
2. Create lighthouserc.js with these budget assertions
3. Configure netlify.toml to run checks on all deploys
4. Add GitHub status checks to block PRs that exceed budgets
5. Create performance dashboard at /lighthouse-reports

Please configure the complete Lighthouse CI setup with these budgets."
```

**提示：**

这种全面的设置将自动测试每个部署预览和
生产部署，阻止任何违反性能
预算的更改。

### 步骤 7：使用 Netlify Analytics 进行真实用户监控

利用 Netlify 的内置分析并集成先进的 RUM 解决方案：

```bash
# In cn TUI mode:
"Set up comprehensive Real User Monitoring for my Netlify site:

1. Configure Netlify Analytics Pro (requires Pro account):
   - Set up server-side analytics (no JS required)
   - Track Core Web Vitals (LCP, FID, CLS, INP)
   - Monitor top pages by performance score
   - Create custom performance alerts
   - Configure weekly performance reports

2. Integrate Web Vitals tracking:
   - Install web-vitals library for detailed metrics
   - Send metrics to Netlify Functions endpoint
   - Store performance data in Netlify Blobs
   - Create performance dashboard at /metrics

3. Set up Performance Alerts:
   - Alert when P75 LCP > 3 seconds
   - Notify if CLS increases by 20%
   - Monitor JavaScript error rates
   - Track 404s and broken resources

4. Configure Geographic Performance Monitoring:
   - Use Netlify Edge Functions to track region-specific metrics
   - Identify slow regions with Edge geo data
   - Compare performance across CDN nodes
   - Optimize edge caching for slow regions

5. Create Custom Performance Dashboard:
   - Build dashboard page using Netlify Functions
   - Display real-time Core Web Vitals
   - Show performance trends over time
   - Include browser and device breakdowns

Show me the complete implementation with all code and configurations"
```

**注意：**

**注意**：Netlify Analytics Pro 需要付费 Netlify Pro 帐户（每个站点19/月
美元）。上面的提示假设您有此计划。对于免费套餐用户，
重点关注步骤 2-5，其中使用 Netlify 函数和 Edge 函数构建 
自定义分析。

**说明：**

**Netlify Analytics 优势**：
- [服务器端分析](<https://docs.netlify.com/analytics/get-started/>) - 无需客户端JavaScript
- [Core Web Vitals](<https://docs.netlify.com/analytics/core-web-vitals/>) - 自动跟踪 Google 的指标
- [自定义指标](<https://docs.netlify.com/functions/logs/>) - 通过函数跟踪任何指标
- [Edge Insights](<https://docs.netlify.com/edge-functions/overview/#geolocation>) - 地理性能数据
- [不需要 Cookie 横幅](<https://docs.netlify.com/analytics/get-started/#privacy>) - 默认情况下符合 GDPR

与 Google Analytics 不同，Netlify Analytics：

- 不会减慢您的网站速度（服务器端）
- 捕获 100% 流量（无广告拦截器）
- 尊重用户隐私（无cookie）
- 单独显示机器人流量

**提示：**

**专业提示**
将 Netlify Analytics 与 Edge Function 相结合，创建一个 
 强大的 RUM 解决方案，该解决方案：
  - 按用户细分跟踪绩效
  - A/B 测试
性能优化
  - 根据连接速度个性化内容
  - 自动提供较轻的资源以减慢连接速度

## 自动性能检查

### 添加 GitHub 秘密

导航到 ** 存储库设置 → 秘密和变量 → 操作 ** 并添加：

- `CONTINUE_API_KEY`：来自 [continue.dev/settings/api-keys](<https://continue.dev/settings/api-keys>) 的 Continue API 密钥
- `NETLIFY_AUTH_TOKEN`：您的 Netlify 个人访问令牌
- `NETLIFY_SITE_ID`：您的 Netlify 网站 ID

### GitHub 行动性能卫士

阻止降低性能的 PR：

```yaml
name: Performance Check

on:
  pull_request:

jobs:
  performance:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: "22"

      - name: Install Continue CLI
        run: |
          npm install -g @continuedev/cli
          echo "✅ Continue CLI installed"

      - name: Authenticate Continue CLI
        env:
          CONTINUE_API_KEY: ${{ secrets.CONTINUE_API_KEY }}
        run: |
          cn auth login --api-key "$CONTINUE_API_KEY"
          echo "✅ Continue CLI authenticated"

      - name: Deploy and Test Performance
        id: perf
        env:
          NETLIFY_AUTH_TOKEN: ${{ secrets.NETLIFY_AUTH_TOKEN }}
          NETLIFY_SITE_ID: ${{ secrets.NETLIFY_SITE_ID }}
        run: |
          echo "🚀 Deploying PR preview and analyzing performance..."
          cn -p "Deploy PR preview and run Lighthouse.
                 Compare scores with main branch.
                 Output JSON with score deltas." > performance.json

      - name: Comment Performance Results
        uses: actions/github-script@v7
        with:
          script: |
            const fs = require('fs');
            const perf = JSON.parse(fs.readFileSync('performance.json'));

            const emoji = perf.score_delta < -10 ? '🔴' :
                         perf.score_delta < 0 ? '🟡' : '🟢';

            const comment = `## ${emoji} Performance Impact

            | Metric | Main | PR | Delta |
            |--------|------|----|---------|
            | Performance Score | ${perf.main_score} | ${perf.pr_score} | ${perf.score_delta > 0 ? '+' : ''}${perf.score_delta} |
            | LCP | ${perf.main_lcp}s | ${perf.pr_lcp}s | ${perf.lcp_delta > 0 ? '+' : ''}${perf.lcp_delta}s |
            | Bundle Size | ${perf.main_bundle}KB | ${perf.pr_bundle}KB | ${perf.bundle_delta > 0 ? '+' : ''}${perf.bundle_delta}KB |

            ${perf.score_delta < -10 ? '⚠️ **This PR significantly degrades performance. Please optimize before merging.**' : ''}

            [View Full Report](${perf.report_url})`;

            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: comment
            });

            // Fail check if performance degrades significantly
            if (perf.score_delta < -10) {
              core.setFailed('Performance degraded by more than 10 points');
            }
```

**注意：**

此工作流程将：
- **如果性能得分下降>10分，则块合并**
- **对任何性能下降发出警告**
- **庆祝**绿色指标的改进

## 本地性能测试

### 步骤8：本地性能测试

部署前测试性能：

```bash
# In cn TUI mode:
"Run production build and measure bundle sizes"
```

### 步骤 9：预提交性能检查

在性能下降发生之前防止其发生：

```bash
# In cn TUI mode:
"Add pre-commit hooks for bundle size limits"
```

## 性能故障排除

### 调试性能问题

识别并修复性能瓶颈：

```bash
# In cn TUI mode:
"Why did my performance score drop?"
```

### 性能问题快速修复

|问题|快速修复命令（cn TUI）|
| ------------- | --------------------------------- |
|慢LCP| `"Preload critical resources"`    |
|高 CLS| `"Add size attributes to images"` |
|大捆| `"Implement code splitting"`      |
|构建缓慢| `"Enable build caching"`          |
|缓存不佳| `"Configure cache headers"`       |

## 你已经完成了什么

#### 性能优先的开发

您已经构建了一个由 AI- 支持的性能优化系统，该系统：
  - 自动监控核心网络生命
  - 在分支之间运行 A/B 测试
  - 阻止降低性能的部署
  - 提供可行的优化
建议

## 发现 Netlify 隐藏的性能瑰宝

许多开发人员不知道 Netlify 提供的功能：

#### 边缘函数

**[Netlify 边缘函数](<https://docs.netlify.com/edge-functions/overview/>)**
  - 在边缘运行代码，更接近用户
  - 即时转换响应
  - A/B 边缘级别测试
  - 无需客户端即可个性化内容 JS

#### 网络图谱

**[Netlify 图](<https://docs.netlify.com/graph/overview/>)**
- 适用于所有 API 的统一 GraphQL 网关
- 自动生成TypeScript
- 内置身份验证处理
- 需要零客户端 API 密钥

#### 按需构建器

**[按需
构建器](<https://docs.netlify.com/configure-builds/on-demand-builders/>)**
- 仅在请求时生成页面
- 缓存动态生成的内容
- 非常适合大型网站（10k+ 页面）
- 大幅缩短构建时间

#### Netlify 函数

**[后台功能](<https://docs.netlify.com/functions/background-functions/>)**
- 运行任务长达 15 分钟
- 异步处理 webhook
- 处理繁重的计算
- 无需担心超时

```bash
# Try these advanced features in cn TUI mode:
"Show me how to use Netlify Edge Functions for geo-based personalization"
"Set up On-Demand Builders for my blog with 5000 posts"
"Configure Background Functions for image processing"
```

## 性能最佳实践

Netlify 性能规则强制执行：

#### 构建优化

- 启用依赖项缓存
- 尽可能并行构建
- 增量编译
- 智能缓存失效

#### 运行时性能

- 自动图像优化
- 高效的资源提示（预加载、预取）
- 最佳缓存标头
- CDN配置

#### 监测标准

- 执行绩效预算
- 核心网络生命体跟踪
- 真实用户监控
- 自动警报

## 高级绩效策略

### 渐进增强

```bash
# In cn TUI mode:
"Implement progressive enhancement with basic HTML first"
```

### 多变量测试

```bash
# In cn TUI mode:
"Test 3 bundle strategies and auto-select winner"
```

### 预测预取

```bash
# In cn TUI mode:
"Analyze navigation patterns and prefetch next pages"
```

## 下一步

- 在您的 Continue 配置中安装 Netlify MCP
- 设置[性能监控](<https://docs.netlify.com/analytics/get-started/>)
- 配置[A/B测试](<https://docs.netlify.com/split-testing/overview/>)

## 资源

- [Netlify MCP](<https://docs.netlify.com/>)
- [核心网络生命指南](<https://web.dev/vitals/>)
- [Netlify 分析文档](<https://docs.netlify.com/analytics/>)
- [Continue 性能指南](<https://docs.continue.dev/guides>)


## 原文对照

<details>
<summary>展开完整原文</summary>

> 阅读说明：原站会自动选择操作系统选项；本地阅读版已展开所有安装选项。

#### Ship Faster Code, Not Slower Sites

Use AI to automatically monitor performance metrics, run A/B tests between
branches, and get actionable optimization suggestions based on real user data
from Netlify Analytics

**说明：**

**Did You Know?** Netlify is more than just static hosting! It offers:
- [Split Testing](<https://docs.netlify.com/site-deploys/split-testing/>) for A/B testing branches
- [Analytics](<https://docs.netlify.com/analytics/get-started/>) with Core Web Vitals tracking
- [Edge Functions](<https://docs.netlify.com/edge-functions/overview/>) for personalization at the edge
- [Build Plugins](<https://docs.netlify.com/integrations/build-plugins/>) ecosystem with 100+ integrations
- [Forms](<https://docs.netlify.com/forms/setup/>) with built-in spam protection
- [Identity](<https://docs.netlify.com/visitor-access/identity/>) for user authentication
- [Large Media](<https://docs.netlify.com/large-media/overview/>) for Git LFS support

This guide shows you how to leverage these features through natural language with Continue CLI!

## What You'll Learn

This cookbook teaches you to:

- Run [A/B tests](<https://docs.netlify.com/site-deploys/split-testing/>) between branches to measure performance impact
- Monitor [Core Web Vitals](<https://docs.netlify.com/analytics/get-started/#core-web-vitals>) and build performance metrics
- Automatically block deploys that degrade performance using [Deploy Contexts](<https://docs.netlify.com/site-deploys/overview/#deploy-contexts>)
- Optimize build times with [Build Plugins](<https://docs.netlify.com/integrations/build-plugins/>) and bundle sizes with AI assistance

## Prerequisites

- GitHub repository with a web project
- [Netlify account](<https://netlify.com>) (free tier works)
- Node.js 22+ installed (required for Netlify)
- [Continue CLI](<https://docs.continue.dev/guides/cli>)
- Netlify MCP configured
- Netlify Development Rules (recommended)

## Quick Setup

**说明：**

The Netlify Development Rules bundle includes guardrails for:
- Proper `.gitignore` configuration
- Function structure and placement
- Edge function constraints
- Local development best practices

For all options, first:

#### Install Continue CLI

#### macOS / Linux

```bash
curl -fsSL https://raw.githubusercontent.com/continuedev/continue/main/extensions/cli/scripts/install.sh | bash
```

#### Windows

```powershell
irm https://raw.githubusercontent.com/continuedev/continue/main/extensions/cli/scripts/install.ps1 | iex
```

#### npm (cross-platform)

If you already have Node.js 20+:
```bash
npm i -g @continuedev/cli
```

#### Install Netlify CLI

1. Install Netlify CLI: `npm i -g netlify-cli` 
2. Authenticate with Netlify:
`netlify login`

## Netlify Continuous AI Workflow Options

#### 🚀 Fastest Path to Success

Skip the manual setup and use our pre-built Netlify Continuous AI agent that includes
optimized prompts, rules, and the Netlify MCP for more consistent results.

After completing **Quick Setup** above, you have two paths to get started:

#### ⚡ Quick Start (Recommended)

#### Add the Pre-Built Agent

Run:

```bash
cn --agent continuedev/netlify-continuous-ai-agent
```

This agent includes:
- **Built-in rules** for consistent formatting and error handling
- **Netlify MCP** for more reliable API interactions

#### Verify Your Setup

The agent uses the Netlify MCP automatically and includes best practice rules for deployment and performance optimization.

#### Run Performance Analysis

From your project directory, run:
```bash
cn "Analyze my Netlify site's performance and optimize it for better Core Web Vitals."
```

That's it! The agent handles everything automatically.

**说明：**

**Why Use the Agent?** Results are more consistent and debugging is easier thanks to the Netlify MCP integration and pre-tested prompts. You can remix the agent later to customize it to your needs.

#### 🛠️ Manual Setup

#### Configure Netlify MCP

1. Authenticate with Netlify:
`netlify login`
2. Add the Netlify MCP to your configuration
3. Follow the configuration instructions for your editor

#### Add Development Rules

Install the Netlify Development Rules bundle for best practices. Add these rules to your agent configuration.

#### Verify Setup

Test the connection with cn CLI:

```bash
cn
# Then in TUI mode:
"Check my Netlify auth and list sites"
```

**说明：**

You're all set! Now you can use cn CLI to interact with Netlify using natural language prompts. Check out the examples below to get started.

#### Agent Requirements

  To use the pre-built agent, you need either:
    - **Continue CLI Pro Plan** with the models add-on, OR
    - **Your own API keys** configured as environment variables

  The agent will automatically detect and use your configuration along with the Netlify MCP for deployment operations.

---

## Performance Optimization Steps

### Step 1: Baseline Performance Metrics

Establish your current performance baseline using cn CLI:

```bash
# Start cn in TUI mode
cn

# Then ask:
"Show my site's Core Web Vitals and build times"
```

**提示：**

**Test in Plan Mode First**: Before making performance optimizations that
might affect your site, test your prompts in plan mode (see the [Plan Mode
Guide](<https://github.com/guides/plan-mode-guide>); press **Shift+Tab** to switch modes). This
shows you what the agent will do without executing it. For example: `"Set up
A/B testing between main and feature branch with performance monitoring"`

**说明：**

  Netlify automatically tracks: 
  - **Build Performance**: Compile times, cache
  hits 
  - **Runtime Performance**: Core Web Vitals, Time to Interactive 
  -  **Resource Usage**: Bandwidth, function execution times

### Step 2: A/B Test Branch Performance

Compare performance between branches:

```bash
# In cn TUI mode:
"Set up A/B test between main and feature branch on Netlify:
- Split traffic 50/50 between branches
- Track Core Web Vitals for each variant
- Monitor conversion metrics and bounce rate
- Enable analytics to measure performance impact
- Configure cookie-based visitor persistence
- Set test duration for 1000 unique visitors
- Auto-conclude test when statistical significance reached
Report winner based on performance + conversion metrics"
```

**说明：**

**Enhanced Analytics**: Combine Netlify's A/B testing with PostHog session
recordings to understand not just which variant performs better, but why users
behave differently. See our [PostHog session analysis
guide](<https://github.com/guides/posthog-github-continuous-ai>) to set up session tracking and
create a complete continuous AI analytics workflow.

### Step 3: Advanced Build Optimization with Netlify

Leverage Netlify's powerful build features to dramatically reduce build times:

```bash
# In cn TUI mode:
"Optimize my Netlify build performance using these features:

1. Enable Netlify Cache Plugin for dependency caching
   - Configure @netlify/plugin-cache for node_modules
   - Set up custom cache directories for .next/cache or .nuxt
   - Enable Gatsby's incremental builds if applicable

2. Set up Build Plugins from Netlify's ecosystem:
   - Install @netlify/plugin-lighthouse for performance monitoring
   - Add netlify-plugin-checklinks to prevent broken links
   - Configure netlify-plugin-submit-sitemap for SEO

3. Implement Conditional Builds:
   - Skip builds when only docs change (ignore: /docs/**)
   - Use build.ignore script for custom logic
   - Set up monorepo-specific build triggers

4. Configure concurrent builds for monorepos
   - Set base directory per package
   - Use pnpm workspaces or yarn workspaces

Show me the netlify.toml configuration and explain each optimization"
```

**说明：**

**Netlify Build Features You're Getting**:
- [Build Caching](<https://docs.netlify.com/configure-builds/build-caching/>) - Persist dependencies between builds
- [Build Plugins](<https://docs.netlify.com/integrations/build-plugins/>) - Extend build process with 100+ plugins
- [Conditional Builds](<https://docs.netlify.com/configure-builds/ignore-builds/>) - Skip unnecessary builds
- [Monorepo Support](<https://docs.netlify.com/configure-builds/monorepos/>) - Optimize multi-package repos

These features can reduce build times by 50-70% for most projects!

**提示：**

**Discover more optimization prompts!** The Netlify community has documented dozens of build optimization strategies in their [Support Guide: How can I optimize my Netlify build time](<https://answers.netlify.com/t/support-guide-how-can-i-optimize-my-netlify-build-time/3907>). 

Use this guide as inspiration for cn CLI prompts not covered in this cookbook, such as:
- `"Configure my builds to skip Dependabot PRs automatically"`
- `"Set up custom ignore patterns for documentation-only changes"`
- `"Optimize my Contentful webhooks to prevent duplicate builds"`
- `"Show me how to use build hooks instead of automatic git triggers"`
- `"Help me choose between Astro and Hugo based on build performance"`

The community guide contains real-world scenarios that you can turn into AI-assisted solutions - just describe what you want to achieve and let cn CLI handle the implementation!

### Step 4: Bundle Analysis with Netlify's Built-in Tools

Use Netlify's bundle analyzer and optimization features:

```bash
# In cn TUI mode:
"Analyze and optimize my bundle using Netlify's tools:

1. Enable Netlify Bundle Analyzer:
   - Add @netlify/plugin-bundle-analyzer to plugins
   - Configure size thresholds in netlify.toml
   - Generate visual bundle reports

2. Set up Asset Optimization:
   - Enable automatic JS minification
   - Configure CSS optimization
   - Turn on HTML minification
   - Set up image processing pipeline

3. Implement Smart Code Splitting:
   - Analyze current chunks with the bundle analyzer
   - Identify components over 50KB for splitting
   - Configure webpack/vite for optimal chunking
   - Set up route-based code splitting

4. Configure Netlify's CDN for optimal delivery:
   - Set cache headers for static assets
   - Enable Brotli compression
   - Configure edge caching rules

Generate a full report with before/after bundle sizes"
```

#### Netlify's Performance Arsenal

  **Features Available Through Netlify MCP**: 
  - [Bundle
  Analyzer](https://docs.netlify.com/configure-builds/build-plugins/bundle-analyzer/): Visualize your JavaScript bundles 
  - [Asset
  Optimization](https://docs.netlify.com/configure-builds/post-processing/):
  Automatic minification and compression 
  - [Edge
  Network](https://docs.netlify.com/platform/edge-network/): Global CDN with
  smart caching 
  - [Deploy
  Previews](https://docs.netlify.com/site-deploys/deploy-previews/): Test
  optimizations before production

### Step 5: Image Optimization

Optimize images for better performance:

```bash
# In cn TUI mode:
"Set up Cloudinary image optimization for my Netlify site:
- Install @cloudinary/netlify-plugin via MCP
- Auto-convert images to WebP with fallbacks
- Generate responsive sizes (320w, 640w, 1024w, 1920w)
- Add lazy loading for all images
- Configure blur-up placeholders
- Update netlify.toml with Cloudinary settings
Target: Reduce image payload by 60-80% and improve LCP"
```

## Continuous Performance Monitoring

### Step 6: Performance Budget Enforcement with Lighthouse CI

Set and enforce performance budgets with automated testing:

```bash
# In cn TUI mode:
"Set up Lighthouse CI with performance budgets for my Netlify site:

Requirements:
- LCP must be < 2.5 seconds
- JavaScript bundle must be < 200KB
- Total size < 500KB
- Performance score >= 90

Setup needed:
1. Install @lhci/cli and @netlify/plugin-lighthouse
2. Create lighthouserc.js with these budget assertions
3. Configure netlify.toml to run checks on all deploys
4. Add GitHub status checks to block PRs that exceed budgets
5. Create performance dashboard at /lighthouse-reports

Please configure the complete Lighthouse CI setup with these budgets."
```

**提示：**

This comprehensive setup will automatically test every deploy preview and
production deployment, blocking any changes that violate your performance
budgets.

### Step 7: Real User Monitoring with Netlify Analytics

Leverage Netlify's built-in analytics and integrate advanced RUM solutions:

```bash
# In cn TUI mode:
"Set up comprehensive Real User Monitoring for my Netlify site:

1. Configure Netlify Analytics Pro (requires Pro account):
   - Set up server-side analytics (no JS required)
   - Track Core Web Vitals (LCP, FID, CLS, INP)
   - Monitor top pages by performance score
   - Create custom performance alerts
   - Configure weekly performance reports

2. Integrate Web Vitals tracking:
   - Install web-vitals library for detailed metrics
   - Send metrics to Netlify Functions endpoint
   - Store performance data in Netlify Blobs
   - Create performance dashboard at /metrics

3. Set up Performance Alerts:
   - Alert when P75 LCP > 3 seconds
   - Notify if CLS increases by 20%
   - Monitor JavaScript error rates
   - Track 404s and broken resources

4. Configure Geographic Performance Monitoring:
   - Use Netlify Edge Functions to track region-specific metrics
   - Identify slow regions with Edge geo data
   - Compare performance across CDN nodes
   - Optimize edge caching for slow regions

5. Create Custom Performance Dashboard:
   - Build dashboard page using Netlify Functions
   - Display real-time Core Web Vitals
   - Show performance trends over time
   - Include browser and device breakdowns

Show me the complete implementation with all code and configurations"
```

**注意：**

**Note**: Netlify Analytics Pro requires a paid Netlify Pro account ($19/month
per site). The prompt above assumes you have this plan. For free tier users,
focus on steps 2-5 which use Netlify Functions and Edge Functions to build
custom analytics.

**说明：**

**Netlify Analytics Advantages**:
- [Server-side Analytics](<https://docs.netlify.com/analytics/get-started/>) - No client-side JavaScript needed
- [Core Web Vitals](<https://docs.netlify.com/analytics/core-web-vitals/>) - Automatic tracking of Google's metrics
- [Custom Metrics](<https://docs.netlify.com/functions/logs/>) - Track any metric via Functions
- [Edge Insights](<https://docs.netlify.com/edge-functions/overview/#geolocation>) - Geographic performance data
- [No Cookie Banner Required](<https://docs.netlify.com/analytics/get-started/#privacy>) - GDPR compliant by default

Unlike Google Analytics, Netlify Analytics:

- Doesn't slow down your site (server-side)
- Captures 100% of traffic (no ad blockers)
- Respects user privacy (no cookies)
- Shows bot traffic separately

**提示：**

  **Pro Tip**
  Combine Netlify Analytics with Edge Functions to create a
  powerful RUM solution that: 
  - Tracks performance by user segment 
  - A/B tests
  performance optimizations 
  - Personalizes content based on connection speed 
  - Automatically serves lighter assets to slow connections

## Automated Performance Checks

### Add GitHub Secrets

Navigate to **Repository Settings → Secrets and variables → Actions** and add:

- `CONTINUE_API_KEY`: Your Continue API key from [continue.dev/settings/api-keys](<https://continue.dev/settings/api-keys>)
- `NETLIFY_AUTH_TOKEN`: Your Netlify personal access token
- `NETLIFY_SITE_ID`: Your Netlify site ID

### GitHub Actions Performance Guard

Block PRs that degrade performance:

```yaml
name: Performance Check

on:
  pull_request:

jobs:
  performance:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: "22"

      - name: Install Continue CLI
        run: |
          npm install -g @continuedev/cli
          echo "✅ Continue CLI installed"

      - name: Authenticate Continue CLI
        env:
          CONTINUE_API_KEY: ${{ secrets.CONTINUE_API_KEY }}
        run: |
          cn auth login --api-key "$CONTINUE_API_KEY"
          echo "✅ Continue CLI authenticated"

      - name: Deploy and Test Performance
        id: perf
        env:
          NETLIFY_AUTH_TOKEN: ${{ secrets.NETLIFY_AUTH_TOKEN }}
          NETLIFY_SITE_ID: ${{ secrets.NETLIFY_SITE_ID }}
        run: |
          echo "🚀 Deploying PR preview and analyzing performance..."
          cn -p "Deploy PR preview and run Lighthouse.
                 Compare scores with main branch.
                 Output JSON with score deltas." > performance.json

      - name: Comment Performance Results
        uses: actions/github-script@v7
        with:
          script: |
            const fs = require('fs');
            const perf = JSON.parse(fs.readFileSync('performance.json'));

            const emoji = perf.score_delta < -10 ? '🔴' :
                         perf.score_delta < 0 ? '🟡' : '🟢';

            const comment = `## ${emoji} Performance Impact

            | Metric | Main | PR | Delta |
            |--------|------|----|---------|
            | Performance Score | ${perf.main_score} | ${perf.pr_score} | ${perf.score_delta > 0 ? '+' : ''}${perf.score_delta} |
            | LCP | ${perf.main_lcp}s | ${perf.pr_lcp}s | ${perf.lcp_delta > 0 ? '+' : ''}${perf.lcp_delta}s |
            | Bundle Size | ${perf.main_bundle}KB | ${perf.pr_bundle}KB | ${perf.bundle_delta > 0 ? '+' : ''}${perf.bundle_delta}KB |

            ${perf.score_delta < -10 ? '⚠️ **This PR significantly degrades performance. Please optimize before merging.**' : ''}

            [View Full Report](${perf.report_url})`;

            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: comment
            });

            // Fail check if performance degrades significantly
            if (perf.score_delta < -10) {
              core.setFailed('Performance degraded by more than 10 points');
            }
```

**注意：**

This workflow will: 
- **Block merge** if performance score drops >10 points 
- **Warn** on any performance regression 
- **Celebrate** improvements with green indicators

## Performance Testing Locally

### Step 8: Local Performance Testing

Test performance before deploying:

```bash
# In cn TUI mode:
"Run production build and measure bundle sizes"
```

### Step 9: Pre-commit Performance Checks

Prevent performance regressions before they happen:

```bash
# In cn TUI mode:
"Add pre-commit hooks for bundle size limits"
```

## Performance Troubleshooting

### Debug Performance Issues

Identify and fix performance bottlenecks:

```bash
# In cn TUI mode:
"Why did my performance score drop?"
```

### Performance Issue Quick Fixes

| Issue         | Quick Fix Command (in cn TUI)     |
| ------------- | --------------------------------- |
| Slow LCP      | `"Preload critical resources"`    |
| High CLS      | `"Add size attributes to images"` |
| Large bundles | `"Implement code splitting"`      |
| Slow builds   | `"Enable build caching"`          |
| Poor caching  | `"Configure cache headers"`       |

## What You've Accomplished

#### Performance-First Development

  You've built an AI-powered performance optimization system that: 
  - Automatically monitors Core Web Vitals 
  - Runs A/B tests between branches 
  - Blocks deployments that degrade performance 
  - Provides actionable optimization
  suggestions

## Discover Netlify's Hidden Performance Gems

Features many developers don't know Netlify offers:

#### Edge Functions

  **[Netlify Edge Functions](<https://docs.netlify.com/edge-functions/overview/>)**
  - Run code at the edge, closer to users
  - Transform responses on-the-fly
  - A/B test at the edge level
  - Personalize content without client-side JS

#### Netlify Graph

**[Netlify Graph](<https://docs.netlify.com/graph/overview/>)** 
- Unified GraphQL gateway for all your APIs 
- Automatic TypeScript generation 
- Built-in authentication handling 
- Zero client-side API keys needed

#### On-Demand Builders

**[On-Demand
Builders](<https://docs.netlify.com/configure-builds/on-demand-builders/>)** 
- Generate pages only when requested
- Cache dynamically generated content
- Perfect for large sites (10k+ pages)
- Reduce build times dramatically

#### Netlify Functions

**[Background Functions](<https://docs.netlify.com/functions/background-functions/>)**
- Run tasks up to 15 minutes
- Process webhooks asynchronously
- Handle heavy computations
- No timeout worries

```bash
# Try these advanced features in cn TUI mode:
"Show me how to use Netlify Edge Functions for geo-based personalization"
"Set up On-Demand Builders for my blog with 5000 posts"
"Configure Background Functions for image processing"
```

## Performance Best Practices

The Netlify Performance Rules enforce:

#### Build Optimization

- Dependency caching enabled
- Parallel builds when possible
- Incremental compilation
- Smart cache invalidation

#### Runtime Performance

- Automatic image optimization
- Efficient resource hints (preload, prefetch)
- Optimal cache headers
- CDN configuration

#### Monitoring Standards

- Performance budgets enforced
- Core Web Vitals tracking
- Real user monitoring
- Automated alerts

## Advanced Performance Strategies

### Progressive Enhancement

```bash
# In cn TUI mode:
"Implement progressive enhancement with basic HTML first"
```

### Multi-variant Testing

```bash
# In cn TUI mode:
"Test 3 bundle strategies and auto-select winner"
```

### Predictive Prefetching

```bash
# In cn TUI mode:
"Analyze navigation patterns and prefetch next pages"
```

## Next Steps

- Install the Netlify MCP in your Continue configuration
- Set up [Performance Monitoring](<https://docs.netlify.com/analytics/get-started/>)
- Configure [A/B Testing](<https://docs.netlify.com/split-testing/overview/>)

## Resources

- [Netlify MCP](<https://docs.netlify.com/>)
- [Core Web Vitals Guide](<https://web.dev/vitals/>)
- [Netlify Analytics Documentation](<https://docs.netlify.com/analytics/>)
- [Continue Performance Guides](<https://docs.continue.dev/guides>)


</details>
