# 用 Continue 与 Chrome DevTools MCP 检查网页性能

工程教程。通过性能追踪、网络和 JavaScript 分析、限速测试、Core Web Vitals、视觉回归和 CI 预算检查，组织网页性能诊断与持续监测。

- 来源：Continue
- 资料类型：工程教程
- 存档版本：`5522c6f44ca0`。
- 文档时间：源码提交（存档版）：[2026-03-24](<https://github.com/continuedev/continue/commit/bd36638ee0cf9fb90c314647bab2f1c6897aa6fe>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：依赖 Chrome DevTools MCP 和可运行的目标网站，样例阈值需调整。

[原文发布页](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/guides/chrome-devtools-mcp-performance.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Continue/用 Continue 与 Chrome DevTools MCP 检查网页性能.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: "Chrome DevTools Performance Optimization Cookbook"
description: "Measure and optimize web performance with Chrome DevTools MCP, automated performance traces, and Core Web Vitals monitoring using Continue."
sidebarTitle: "Chrome DevTools MCP for Performance"
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

> 阅读说明：原站会自动选择操作系统选项；本地阅读版已展开所有安装选项。

#### 像专业人士一样调试性能

使用 AI 自动跟踪性能、分析 Core Web Vitals、诊断瓶颈并直接从 Chrome DevTools 获取可行的优化建议

**说明：**

**你知道吗？** Chrome DevTools MCP 为您的 AI 工作流程带来了浏览器调试的全部功能：
- [具有自动分析功能的性能跟踪](<https://developer.chrome.com/docs/devtools/performance>)
- [网络请求监控](<https://developer.chrome.com/docs/devtools/network>)用于瓶颈检测
- [控制台调试](<https://developer.chrome.com/docs/devtools/console>)，具有错误模式识别功能
- [CPU 和网络节流](<https://developer.chrome.com/docs/devtools/performance/reference#throttling>) 模拟真实条件
- [性能洞察](<https://developer.chrome.com/docs/devtools/performance-insights>)与AI-动力分析
- [屏幕截图调试](<https://developer.chrome.com/docs/devtools/device-mode>)用于视觉回归检测

本指南向您展示如何使用 Continue CLI 通过自然语言利用这些功能！

## 你将学到什么

这本食谱教你：

- 运行自动 [ 性能跟踪 ](<https://developer.chrome.com/docs/devtools/performance>) 以捕获运行时指标
- 分析 [核心 Web 生命周期 ](<https://web.dev/vitals/>)（LCP、FID、CLS、INP）和性能见解
- 使用网络和控制台分析诊断性能瓶颈
- 使用 [ 节流 ](<https://developer.chrome.com/docs/devtools/performance/reference#throttling>) 测试不同网络和 CPU 条件下的性能
- 使用 [ 屏幕截图 ](<https://developer.chrome.com/docs/devtools/device-mode>) 自动进行视觉回归测试

## 先决条件

- 已安装 Chrome 浏览器
- 具有正在运行的开发服务器的 Web 项目（或已部署的 URL）
- Node.js 20+已安装
- [Continue CLI](<https://docs.continue.dev/guides/cli>)
- [Chrome DevTools MCP](<https://continue.dev>) 已配置

## 快速设置

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

## Chrome DevTools MCP 工作流程选项

#### 最快的成功之路

跳过手动设置并使用我们预构建的 Chrome DevTools agent，其中包括 
 优化的提示、规则和 Chrome DevTools MCP 以获得更一致的结果。

完成上面的**快速设置**后，您有两条路径可以开始：

#### ⚡ 快速入门（推荐）

**完美适用于：** 通过优化的提示和内置性能分析立即获得结果

#### 添加预建的 Agent

运行：

```bash
cn --agent continuedev/chrome-dev-tools-agent
```

此 agent 包括：
- **优化性能分析和调试的提示**
- **内置规则**用于一致的格式和错误处理
- **[Chrome DevTools MCP](<https://continue.dev/google/chrome-devtools-mcp>)** 实现可靠的浏览器自动化

#### 运行性能分析

在打开的 TUI 中，输入：
```
Analyze performance of http://localhost:3000 and provide optimization recommendations
```

就是这样！ agent 自动处理 Chrome 自动化。

**说明：**

**为什么使用 Agent？** 由于 Chrome DevTools MCP 集成和预先测试的提示，结果更加一致，调试也更加容易。

#### 🛠️手动设置

#### 将 Chrome DevTools MCP 添加到您的智能助理

将其添加到您的配置中：

```yaml
name: Chrome DevTools MCP
version: 0.0.1
schema: v1
mcpServers:
  - name: Chrome DevTools MCP
    command: npx
    args:
      - chrome-devtools-mcp@latest
```

MCP 将自动启动 Chrome 并在需要时连接到 DevTools。

#### 验证设置

测试连接：

**TUI 模式提示：**
```
Open web.dev and take a screenshot
```

#### 运行性能分析

导航到您的项目目录：

**TUI 模式提示：**
```
Navigate to http://localhost:3000 and record a performance trace. Analyze LCP, FID, and CLS.
```

**说明：**

**手动设置**：虽然您可以手动配置MCP，但预构建的agent为性能分析工作流程提供了优化的提示和更好的错误处理。

#### Agent 要求

要使用预构建的 agent，您需要：
    - **Continue CLI 专业计划 ** 以及模型附加组件，OR
    - **您自己的API密钥**配置为环境变量
    - **您的系统上安装了 Chrome 浏览器**
    - **Node.js 20+** 通过npx运行MCP

agent将自动检测并使用您的配置。

---

## 绩效衡量工作流程

Chrome DevTools MCP 支持自然语言性能分析。以下是根据实际用例改编的工作流程：

### 快速性能检查

#### 验证代码更改

**TUI 模式提示：**
```
Verify in the browser that your change works as expected.
```

#### 调试损坏的图像

**TUI 模式提示：**
```
A few images on localhost:8080 are not loading. What's happening?
```

#### 调试表单提交问题

**TUI 模式提示：**
```
Why does submitting the form fail after entering an email address?
```

#### 调试布局问题

**TUI 模式提示：**
```
The page on localhost:8080 looks strange and off. Check what's happening there.
```

#### 性能优化

**TUI 模式提示：**
```
Localhost:8080 is loading slowly. Make it load faster.
```

#### 检查核心网络生命力

**TUI 模式提示：**
```
Please check the LCP of web.dev.
```

---

## 性能分析秘诀

现在您可以使用自然语言提示来分析 Web 性能。 Continue agent 会自动调用相应的 Chrome DevTools MCP 工具。

**说明：**

**在哪里运行这些工作流程：**
- **IDE 扩展 **：在 VS Code、JetBrains 或其他支持的 IDE 中使用 Continue
- **终端（TUI模式）**：运行`cn`进入交互模式，然后输入提示
- **CLI（无头模式）**：使用 `cn -p "your prompt"` 进行无头命令

**首先在计划模式下测试**：运行性能测量之前，请在计划模式下测试提示（请参阅[计划模式指南](<https://docs.continue.dev/guides/plan-mode-guide>)；按**Shift+Tab**切换模式）。这向您展示了 agent 在不执行它的情况下会做什么。

### 步骤 1：基线性能跟踪

建立您当前的绩效基准：

**TUI 模式提示：**
```
Navigate to http://localhost:3000 and record a performance trace with page reload. Analyze the trace and show me the LCP, FID, CLS, and total blocking time.
```

**说明：**

Chrome DevTools 性能面板自动跟踪：
  - **核心网络生命**：LCP、FID、CLS、INP
  - **加载性能**：DOMContentLoaded、加载事件、首次绘制
  - **运行时性能**：JavaScript执行时间、布局转换、绘制事件
  - **资源使用情况**：内存消耗，CPU利用率

### 步骤 2：分析性能洞察

深入研究具体的性能问题：

**TUI 模式提示：**
```
Record a performance trace for http://localhost:3000 and analyze all Performance Insights. For each insight found, provide detailed information about:
- The specific issue (e.g., DocumentLatency, SlowCSS)
- Root cause analysis
- Affected resources or code
- Recommended optimizations
- Expected impact on Core Web Vitals
```

**说明：**

**性能洞察**：Chrome DevTools 自动检测常见性能问题，例如：
- **渲染阻塞资源**：延迟首次绘制的CSS和JavaScript
- **布局移位**：页面加载期间移动的元素
- **长任务**：JavaScript执行阻塞主线程
- **网络请求缓慢**：资源加载时间过长
- **未使用CSS/JavaScript**：死代码增加包大小

### 步骤3：网络性能分析

识别缓慢的网络请求和优化机会：

**TUI 模式提示：**
```
Navigate to https://my-site.com and:
1. List all network requests made during page load
2. Identify the 5 slowest requests and their sizes
3. Find any requests over 1MB
4. Detect render-blocking resources
5. Check for inefficient caching (missing cache headers)
6. Suggest specific optimizations for each issue found
```

### 步骤 4：节流性能测试

真实网络和CPU条件下的测试性能：

**TUI 模式提示：**
```
Test my site's performance under different conditions:

1. Navigate to http://localhost:3000
2. Emulate 4x CPU slowdown and Fast 3G network
3. Record a performance trace with reload
4. Analyze LCP and Total Blocking Time
5. Take a screenshot when page is fully loaded

Then repeat the test with:
- Slow 3G network + 6x CPU slowdown
- Offline network (to test service worker)

Compare results and identify which conditions cause the worst performance degradation."
```

**说明：**

**可用网络预设**：
- 无节流
- 快速 3G（1.6 Mbps 下行，0.75 Mbps 上行）
- 慢速 3G（400 Kbps 下降，400 Kbps 上升）
- 离线

**CPU 节流**：4x、6x 或自定义减速以模拟低端设备

### 步骤5：JavaScript性能分析

识别昂贵的 JavaScript 操作：

**TUI 模式提示：**
```
Record a performance trace for http://localhost:3000 and:
1. Identify all long tasks (>50ms) blocking the main thread
2. Find the specific JavaScript functions causing these tasks
3. Measure total JavaScript execution time
4. Detect unused JavaScript being loaded
5. Show the call stack for the longest task
6. Recommend code splitting or lazy loading opportunities"
```

## 自动性能监控

### 步骤 6：核心 Web Vitals 仪表板

为 Core Web Vitals 创建自动监控：

**TUI 模式提示：**
```
Create a performance monitoring script that:
1. Opens my site at http://localhost:3000
2. Records a performance trace with reload
3. Extracts Core Web Vitals (LCP, FID, CLS, INP)
4. Checks console for JavaScript errors
5. Takes a screenshot
6. Saves results to performance-report.json with format:
   {
     'timestamp': 'ISO date',
     'lcp': number,
     'fid': number,
     'cls': number,
     'inp': number,
     'errors': array,
     'screenshot': 'path'
   }

Save this as scripts/performance-monitor.js that I can run regularly"
```

### 步骤7：视觉回归检测

检测意外的视觉变化：

**TUI 模式提示：**
```
Set up visual regression testing:
1. Navigate to http://localhost:3000
2. Take full-page screenshots at:
   - Desktop viewport (1920x1080)
   - Tablet viewport (768x1024)
   - Mobile viewport (375x667)
3. Save screenshots to screenshots/baseline/
4. Create a script to compare future screenshots against baseline
5. Highlight any pixel differences over 5%
6. Generate a visual diff report"
```

### 步骤 8：绩效预算执行

制定并执行绩效预算：

**TUI 模式提示：**
```
Create a performance budget checker that:

Requirements:
- LCP must be < 2.5 seconds
- FID must be < 100ms
- CLS must be < 0.1
- Total JavaScript < 300KB
- Total page size < 1MB
- No console errors

Implementation:
1. Navigate to http://localhost:3000
2. Record performance trace with reload
3. List all network requests and calculate total sizes
4. Check console messages for errors
5. Validate all metrics against budgets
6. Exit with code 1 if any budget is exceeded
7. Generate a detailed report showing pass/fail for each metric

Save as scripts/performance-budget.js for CI/CD integration"
```

## 使用 GitHub 操作进行连续性能测试

此示例演示了一个 **连续 AI 工作流程 **，其中在无头模式下使用 Chrome DevTools MCP 在 CI/CD 管道中自动运行性能验证。

### 添加 GitHub 秘密

导航到 ** 存储库设置 → 秘密和变量 → 操作 ** 并添加：

- `CONTINUE_API_KEY`：来自 [continue.dev/settings/api-keys](<https://continue.dev/settings/api-keys>) 的 Continue API 密钥

### 创建工作流程文件

此工作流程在无头模式下使用 Continue CLI 自动验证拉取请求的 Web 性能。它记录性能跟踪，提取 Core Web Vitals，并以 PR 评论的形式发布摘要报告。

在您的存储库中创建 `.github/workflows/performance-check.yml`：

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
          node-version: "20"

      - name: Install Dependencies
        run: |
          npm install -g @continuedev/cli
          npm ci

      - name: Build Project
        run: npm run build

      - name: Start Dev Server
        run: |
          npm run dev &
          npx wait-on http://localhost:3000

      - name: Run Performance Tests
        env:
          CONTINUE_API_KEY: ${{ secrets.CONTINUE_API_KEY }}
        run: |
          cn --agent continuedev/chrome-dev-tools-agent \
             -p "Navigate to http://localhost:3000 and:
             1. Record performance trace with reload
             2. Extract LCP, FID, CLS values
             3. List network requests and calculate total bundle size
             4. Output results as JSON to performance.json" \
             --auto

      - name: Check Performance Budgets
        run: |
          node << 'EOF'
          const fs = require('fs');
          const perf = JSON.parse(fs.readFileSync('performance.json'));

          const budgets = {
            lcp: 2.5,
            fid: 100,
            cls: 0.1,
            bundleSize: 300000
          };

          let failed = false;
          const results = [];

          if (perf.lcp > budgets.lcp) {
            results.push(`❌ LCP: ${perf.lcp}s (budget: ${budgets.lcp}s)`);
            failed = true;
          } else {
            results.push(`✅ LCP: ${perf.lcp}s`);
          }

          if (perf.fid > budgets.fid) {
            results.push(`❌ FID: ${perf.fid}ms (budget: ${budgets.fid}ms)`);
            failed = true;
          } else {
            results.push(`✅ FID: ${perf.fid}ms`);
          }

          if (perf.cls > budgets.cls) {
            results.push(`❌ CLS: ${perf.cls} (budget: ${budgets.cls})`);
            failed = true;
          } else {
            results.push(`✅ CLS: ${perf.cls}`);
          }

          if (perf.bundleSize > budgets.bundleSize) {
            results.push(`❌ Bundle: ${perf.bundleSize}KB (budget: ${budgets.bundleSize}KB)`);
            failed = true;
          } else {
            results.push(`✅ Bundle: ${perf.bundleSize}KB`);
          }

          console.log(results.join('\n'));

          if (failed) {
            process.exit(1);
          }
          EOF

      - name: Comment Performance Results
        if: always()
        uses: actions/github-script@v7
        with:
          script: |
            const fs = require('fs');
            const perf = JSON.parse(fs.readFileSync('performance.json'));

            const comment = `## 📊 Performance Report

            | Metric | Value | Budget | Status |
            |--------|-------|--------|--------|
            | LCP | ${perf.lcp}s | 2.5s | ${perf.lcp <= 2.5 ? '✅' : '❌'} |
            | FID | ${perf.fid}ms | 100ms | ${perf.fid <= 100 ? '✅' : '❌'} |
            | CLS | ${perf.cls} | 0.1 | ${perf.cls <= 0.1 ? '✅' : '❌'} |
            | Bundle Size | ${perf.bundleSize}KB | 300KB | ${perf.bundleSize <= 300 ? '✅' : '❌'} |

            ${perf.lcp > 2.5 || perf.fid > 100 || perf.cls > 0.1 || perf.bundleSize > 300
              ? '⚠️ **Performance budgets exceeded. Please optimize before merging.**'
              : '✅ **All performance budgets met!**'}`;

            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: comment
            });
```

**说明：**

Chrome DevTools MCP 在无头 Chrome 环境中工作。确保您的 CI 环境已安装 Chrome（它已预安装在 GitHub Actions ubuntu-latest 运行程序上）。

## 高级性能工作流程

### 步骤 9：竞争对手比较

将您的网站的性能与竞争对手进行比较：

**TUI 模式提示：**
```
Compare performance between my site and competitors:

Sites to test:
- http://localhost:3000 (my site)
- https://competitor1.com
- https://competitor2.com

For each site:
1. Navigate to homepage
2. Record performance trace with reload
3. Extract LCP, FID, CLS, Total Blocking Time
4. List network requests and calculate total page size
5. Count JavaScript and CSS files

Create a comparison table showing:
- Site name
- All Core Web Vitals
- Total page size
- Number of requests
- Which site performs best for each metric

Provide specific recommendations for how my site can improve based on what competitors do better."
```

### 步骤10：跨路由的性能测试

测试整个站点的性能一致性：

**TUI 模式提示：**
```
Test performance across all major routes:

Routes to test:
- / (homepage)
- /products
- /products/[id] (pick a sample product)
- /checkout
- /blog

For each route:
1. Navigate to the URL
2. Record performance trace
3. Extract LCP, Total Blocking Time, bundle size
4. Check console for errors
5. Take a screenshot

Generate a report showing:
- Which routes have the worst LCP
- Routes with the most JavaScript errors
- Bundle size variations across routes
- Recommendations for route-specific optimizations"
```

### 步骤 11：移动性能分析

关注特定于移动设备的性能问题：

**TUI 模式提示：**
```
Analyze mobile performance for http://localhost:3000:

1. Resize viewport to mobile (375x667)
2. Emulate Slow 3G network + 4x CPU slowdown
3. Record performance trace with reload
4. Analyze Performance Insights for mobile-specific issues
5. Check for:
   - Touch target sizes too small
   - Horizontal scrolling issues
   - Oversized images not optimized for mobile
   - Excessive JavaScript on mobile
6. Take mobile screenshot
7. Provide mobile-specific optimization recommendations"
```

## 性能故障排除

### 调试性能回归

快速确定导致性能下降的原因：

**TUI 模式提示：**
```
Debug performance regression on http://localhost:3000:

1. Record performance trace
2. Analyze all Performance Insights
3. List console errors and warnings
4. Check network requests for:
   - Failed requests
   - Slow requests (>1s)
   - Large requests (>500KB)
5. Identify the top 3 performance bottlenecks
6. For each bottleneck, suggest:
   - Root cause
   - Specific code or resource causing it
   - Step-by-step fix
   - Expected performance improvement"
```

### 性能问题快速参考

|问题|快速修复命令（cn TUI）|
| :---- | :---------------------------- |
|慢LCP| `"Find render-blocking resources and suggest preloading or deferring"` |
|高 CLS| `"Detect layout shifts and identify unsized images or dynamic content"` |
|长期任务| `"Find JavaScript tasks over 50ms and suggest code splitting"` |
|大捆| `"List all JavaScript files, identify largest ones, suggest lazy loading"` |
|网络慢| `"Find requests over 500KB and suggest compression or optimization"` |
|控制台错误| `"List all console errors and suggest fixes"` |

## 你建造了什么

完成本指南后，您将拥有一个完整的 **AI- 驱动的性能分析系统 **，该系统：

- ✅ **使用自然语言** - 简单的提示而不是复杂的DevTools命令
- ✅ **自动分析** — AI解释性能跟踪并提出修复建议
- ✅ **连续运行** - CI/CD 管道中的自动验证
- ✅ **确保质量** — 性能检查防止运输回归

#### 连续AI

您的性能工作流程现在在 **[ 级别 2 连续 AI](<https://blog.continue.dev/what-is-continuous-ai-a-developers-guide/>)** - AI 运行，通过审核和批准变更，在人工监督下处理日常性能分析和调试。

## Chrome DevTools MCP 功能

#### 绩效追踪

**可用工具**
  - `performance_start_trace`：开始录制并自动重新加载
  - `performance_stop_trace`：停止并分析跟踪
  - `performance_analyze_insight`：深入探讨具体问题

#### 网络调试

**可用工具**
  - `list_network_requests`：查看所有请求和尺寸
  - `get_network_request`：检查具体请求详细信息
  - `emulate_network`：各种网络条件下测试

#### 控制台分析

**可用工具**
  - `list_console_messages`：获取所有控制台日志和错误
  - `evaluate_script`：在页面上下文中运行JavaScript
  - 自动错误模式检测

#### 视觉测试

**可用工具**
  - `take_screenshot`：捕获完整或部分页面
  - `take_snapshot`：获取可访问性树快照
  - `resize_page`：测试响应式布局

## 性能最佳实践

监控最佳网络性能的关键指标：

#### 核心网络生命力

- LCP < 2.5s（好）
- FID < 100ms（良好）
- CLS < 0.1（好）
- INP < 200ms（良好）

#### 负载性能

- 总页面大小 < 1MB
- JavaScript < 300KB
- 交互时间 < 3.8s
- 第一次内容绘制 < 1.8s

#### 运行时性能

- 没有> 50ms的长任务
- 互动期间 60 FPS
- 没有内存泄漏
- 高效的事件监听器

## 高级测试场景

### A/B 测试性能影响

**TUI 模式提示：**
```
Compare performance of two implementations:
1. Test variant A at http://localhost:3000?variant=A
2. Test variant B at http://localhost:3000?variant=B
Run each test 5 times and calculate average LCP, FID, CLS
Determine which variant has better performance and by how much
```

### 灯塔分数追踪

**TUI 模式提示：**
```
Create a script that runs daily performance audits:
1. Navigate to production site
2. Record performance trace
3. Calculate Lighthouse-style performance score based on:
   - FCP (10%)
   - SI (10%)
   - LCP (25%)
   - TTI (10%)
   - TBT (30%)
   - CLS (15%)
4. Track score over time in performance-history.json
5. Alert if score drops below 90
```

### 性能回归检测

**TUI 模式提示：**
```
Set up automated regression detection:
1. Record baseline performance for main branch
2. Save baseline metrics to baseline-perf.json
3. On each PR, run performance tests
4. Compare PR metrics with baseline
5. Flag regressions over 10% for any metric
6. Generate visual diff report with screenshots
```

## 下一步

1. **分析您的第一个站点** - 尝试当前项目的基线性能跟踪
2. **调试瓶颈** - 使用网络分析提示修复缓慢的请求
3. **设置 CI 管道** - 将 GitHub 操作工作流程添加到您的存储库
4. **测试节流** - 测量真实网络条件下的性能
5. **监控趋势** - 跟踪一段时间内的核心网络生命

## 其他资源

#### [镀铬 DevTools MCP](<https://github.com/ChromeDevTools/chrome-devtools-mcp>)

官方 Chrome DevTools MCP 存储库

#### [Continue 文档](<https://docs.continue.dev>)

探索 Continue 文档和指南

#### [DevTools 文档](<https://developer.chrome.com/docs/devtools>)

完整的 Chrome DevTools 文档

#### [Web Vitals 指南](<https://web.dev/vitals/>)

了解核心网络生命力


## 原文对照

<details>
<summary>展开完整原文</summary>

> 阅读说明：原站会自动选择操作系统选项；本地阅读版已展开所有安装选项。

#### Debug Performance Like a Pro

Use AI to automatically trace performance, analyze Core Web Vitals, diagnose bottlenecks, and get actionable optimization suggestions directly from Chrome DevTools

**说明：**

**Did You Know?** Chrome DevTools MCP brings the full power of browser debugging to your AI workflow:
- [Performance Traces](<https://developer.chrome.com/docs/devtools/performance>) with automated analysis
- [Network Request Monitoring](<https://developer.chrome.com/docs/devtools/network>) for bottleneck detection
- [Console Debugging](<https://developer.chrome.com/docs/devtools/console>) with error pattern recognition
- [CPU & Network Throttling](<https://developer.chrome.com/docs/devtools/performance/reference#throttling>) to simulate real-world conditions
- [Performance Insights](<https://developer.chrome.com/docs/devtools/performance-insights>) with AI-powered analysis
- [Screenshot Debugging](<https://developer.chrome.com/docs/devtools/device-mode>) for visual regression detection

This guide shows you how to leverage these features through natural language with Continue CLI!

## What You'll Learn

This cookbook teaches you to:

- Run automated [performance traces](<https://developer.chrome.com/docs/devtools/performance>) to capture runtime metrics
- Analyze [Core Web Vitals](<https://web.dev/vitals/>) (LCP, FID, CLS, INP) and performance insights
- Diagnose performance bottlenecks using network and console analysis
- Test performance under different network and CPU conditions with [throttling](<https://developer.chrome.com/docs/devtools/performance/reference#throttling>)
- Automate visual regression testing with [screenshots](<https://developer.chrome.com/docs/devtools/device-mode>)

## Prerequisites

- Chrome browser installed
- Web project with a running development server (or deployed URL)
- Node.js 20+ installed
- [Continue CLI](<https://docs.continue.dev/guides/cli>)
- [Chrome DevTools MCP](<https://continue.dev>) configured

## Quick Setup

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

## Chrome DevTools MCP Workflow Options

#### Fastest Path to Success

Skip the manual setup and use our pre-built Chrome DevTools agent that includes
optimized prompts, rules, and the Chrome DevTools MCP for more consistent results.

After completing **Quick Setup** above, you have two paths to get started:

#### ⚡ Quick Start (Recommended)

**Perfect for:** Immediate results with optimized prompts and built-in performance analysis

#### Add the Pre-Built Agent

Run:

```bash
cn --agent continuedev/chrome-dev-tools-agent
```

This agent includes:
- **Optimized prompts** for performance analysis and debugging
- **Built-in rules** for consistent formatting and error handling
- **[Chrome DevTools MCP](<https://continue.dev/google/chrome-devtools-mcp>)** for reliable browser automation

#### Run Performance Analysis

In the TUI that opens, type:
```
Analyze performance of http://localhost:3000 and provide optimization recommendations
```

That's it! The agent handles Chrome automation automatically.

**说明：**

**Why Use the Agent?** Results are more consistent and debugging is easier thanks to the Chrome DevTools MCP integration and pre-tested prompts.

#### 🛠️ Manual Setup

#### Add Chrome DevTools MCP to Your Assistant

Add this to your configuration:

```yaml
name: Chrome DevTools MCP
version: 0.0.1
schema: v1
mcpServers:
  - name: Chrome DevTools MCP
    command: npx
    args:
      - chrome-devtools-mcp@latest
```

The MCP will automatically launch Chrome and connect to DevTools when needed.

#### Verify Setup

Test the connection:

**TUI Mode Prompt:**
```
Open web.dev and take a screenshot
```

#### Run Performance Analysis

Navigate to your project directory:

**TUI Mode Prompt:**
```
Navigate to http://localhost:3000 and record a performance trace. Analyze LCP, FID, and CLS.
```

**说明：**

**Manual Setup**: While you can configure the MCP manually, the pre-built agent provides optimized prompts and better error handling for performance analysis workflows.

#### Agent Requirements

  To use the pre-built agent, you need either:
    - **Continue CLI Pro Plan** with the models add-on, OR
    - **Your own API keys** configured as environment variables
    - **Chrome browser** installed on your system
    - **Node.js 20+** to run the MCP via npx

  The agent will automatically detect and use your configuration.

---

## Performance Measurement Workflows

The Chrome DevTools MCP enables natural language performance analysis. Here are workflows adapted from real-world use cases:

### Quick Performance Checks

#### Verify Code Changes

**TUI Mode Prompt:**
```
Verify in the browser that your change works as expected.
```

#### Debug Broken Images

**TUI Mode Prompt:**
```
A few images on localhost:8080 are not loading. What's happening?
```

#### Debug Form Submission Issues

**TUI Mode Prompt:**
```
Why does submitting the form fail after entering an email address?
```

#### Debug Layout Issues

**TUI Mode Prompt:**
```
The page on localhost:8080 looks strange and off. Check what's happening there.
```

#### Performance Optimization

**TUI Mode Prompt:**
```
Localhost:8080 is loading slowly. Make it load faster.
```

#### Check Core Web Vitals

**TUI Mode Prompt:**
```
Please check the LCP of web.dev.
```

---

## Performance Analysis Recipes

Now you can use natural language prompts to analyze web performance. The Continue agent automatically calls the appropriate Chrome DevTools MCP tools.

**说明：**

**Where to run these workflows:**
- **IDE Extensions**: Use Continue in VS Code, JetBrains, or other supported IDEs
- **Terminal (TUI mode)**: Run `cn` to enter interactive mode, then type your prompts
- **CLI (headless mode)**: Use `cn -p "your prompt"` for headless commands

**Test in Plan Mode First**: Before running performance measurements, test your prompts in plan mode (see the [Plan Mode Guide](<https://docs.continue.dev/guides/plan-mode-guide>); press **Shift+Tab** to switch modes). This shows you what the agent will do without executing it.

### Step 1: Baseline Performance Trace

Establish your current performance baseline:

**TUI Mode Prompt:**
```
Navigate to http://localhost:3000 and record a performance trace with page reload. Analyze the trace and show me the LCP, FID, CLS, and total blocking time.
```

**说明：**

  Chrome DevTools Performance Panel automatically tracks:
  - **Core Web Vitals**: LCP, FID, CLS, INP
  - **Loading Performance**: DOMContentLoaded, Load events, First Paint
  - **Runtime Performance**: JavaScript execution time, layout shifts, paint events
  - **Resource Usage**: Memory consumption, CPU utilization

### Step 2: Analyze Performance Insights

Deep dive into specific performance issues:

**TUI Mode Prompt:**
```
Record a performance trace for http://localhost:3000 and analyze all Performance Insights. For each insight found, provide detailed information about:
- The specific issue (e.g., DocumentLatency, SlowCSS)
- Root cause analysis
- Affected resources or code
- Recommended optimizations
- Expected impact on Core Web Vitals
```

**说明：**

**Performance Insights**: Chrome DevTools automatically detects common performance issues like:
- **Render Blocking Resources**: CSS and JavaScript that delay first paint
- **Layout Shifts**: Elements that move during page load
- **Long Tasks**: JavaScript execution blocking the main thread
- **Slow Network Requests**: Resources taking too long to load
- **Unused CSS/JavaScript**: Dead code increasing bundle size

### Step 3: Network Performance Analysis

Identify slow network requests and optimization opportunities:

**TUI Mode Prompt:**
```
Navigate to https://my-site.com and:
1. List all network requests made during page load
2. Identify the 5 slowest requests and their sizes
3. Find any requests over 1MB
4. Detect render-blocking resources
5. Check for inefficient caching (missing cache headers)
6. Suggest specific optimizations for each issue found
```

### Step 4: Throttling Performance Tests

Test performance under real-world network and CPU conditions:

**TUI Mode Prompt:**
```
Test my site's performance under different conditions:

1. Navigate to http://localhost:3000
2. Emulate 4x CPU slowdown and Fast 3G network
3. Record a performance trace with reload
4. Analyze LCP and Total Blocking Time
5. Take a screenshot when page is fully loaded

Then repeat the test with:
- Slow 3G network + 6x CPU slowdown
- Offline network (to test service worker)

Compare results and identify which conditions cause the worst performance degradation."
```

**说明：**

**Available Network Presets**:
- No throttling
- Fast 3G (1.6 Mbps down, 0.75 Mbps up)
- Slow 3G (400 Kbps down, 400 Kbps up)
- Offline

**CPU Throttling**: 4x, 6x, or custom slowdown to simulate low-end devices

### Step 5: JavaScript Performance Analysis

Identify expensive JavaScript operations:

**TUI Mode Prompt:**
```
Record a performance trace for http://localhost:3000 and:
1. Identify all long tasks (>50ms) blocking the main thread
2. Find the specific JavaScript functions causing these tasks
3. Measure total JavaScript execution time
4. Detect unused JavaScript being loaded
5. Show the call stack for the longest task
6. Recommend code splitting or lazy loading opportunities"
```

## Automated Performance Monitoring

### Step 6: Core Web Vitals Dashboard

Create automated monitoring for Core Web Vitals:

**TUI Mode Prompt:**
```
Create a performance monitoring script that:
1. Opens my site at http://localhost:3000
2. Records a performance trace with reload
3. Extracts Core Web Vitals (LCP, FID, CLS, INP)
4. Checks console for JavaScript errors
5. Takes a screenshot
6. Saves results to performance-report.json with format:
   {
     'timestamp': 'ISO date',
     'lcp': number,
     'fid': number,
     'cls': number,
     'inp': number,
     'errors': array,
     'screenshot': 'path'
   }

Save this as scripts/performance-monitor.js that I can run regularly"
```

### Step 7: Visual Regression Detection

Detect unintended visual changes:

**TUI Mode Prompt:**
```
Set up visual regression testing:
1. Navigate to http://localhost:3000
2. Take full-page screenshots at:
   - Desktop viewport (1920x1080)
   - Tablet viewport (768x1024)
   - Mobile viewport (375x667)
3. Save screenshots to screenshots/baseline/
4. Create a script to compare future screenshots against baseline
5. Highlight any pixel differences over 5%
6. Generate a visual diff report"
```

### Step 8: Performance Budget Enforcement

Set and enforce performance budgets:

**TUI Mode Prompt:**
```
Create a performance budget checker that:

Requirements:
- LCP must be < 2.5 seconds
- FID must be < 100ms
- CLS must be < 0.1
- Total JavaScript < 300KB
- Total page size < 1MB
- No console errors

Implementation:
1. Navigate to http://localhost:3000
2. Record performance trace with reload
3. List all network requests and calculate total sizes
4. Check console messages for errors
5. Validate all metrics against budgets
6. Exit with code 1 if any budget is exceeded
7. Generate a detailed report showing pass/fail for each metric

Save as scripts/performance-budget.js for CI/CD integration"
```

## Continuous Performance Testing with GitHub Actions

This example demonstrates a **Continuous AI workflow** where performance validation runs automatically in your CI/CD pipeline using Chrome DevTools MCP in headless mode.

### Add GitHub Secrets

Navigate to **Repository Settings → Secrets and variables → Actions** and add:

- `CONTINUE_API_KEY`: Your Continue API key from [continue.dev/settings/api-keys](<https://continue.dev/settings/api-keys>)

### Create Workflow File

This workflow automatically validates web performance on pull requests using the Continue CLI in headless mode. It records performance traces, extracts Core Web Vitals, and posts a summary report as a PR comment.

Create `.github/workflows/performance-check.yml` in your repository:

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
          node-version: "20"

      - name: Install Dependencies
        run: |
          npm install -g @continuedev/cli
          npm ci

      - name: Build Project
        run: npm run build

      - name: Start Dev Server
        run: |
          npm run dev &
          npx wait-on http://localhost:3000

      - name: Run Performance Tests
        env:
          CONTINUE_API_KEY: ${{ secrets.CONTINUE_API_KEY }}
        run: |
          cn --agent continuedev/chrome-dev-tools-agent \
             -p "Navigate to http://localhost:3000 and:
             1. Record performance trace with reload
             2. Extract LCP, FID, CLS values
             3. List network requests and calculate total bundle size
             4. Output results as JSON to performance.json" \
             --auto

      - name: Check Performance Budgets
        run: |
          node << 'EOF'
          const fs = require('fs');
          const perf = JSON.parse(fs.readFileSync('performance.json'));

          const budgets = {
            lcp: 2.5,
            fid: 100,
            cls: 0.1,
            bundleSize: 300000
          };

          let failed = false;
          const results = [];

          if (perf.lcp > budgets.lcp) {
            results.push(`❌ LCP: ${perf.lcp}s (budget: ${budgets.lcp}s)`);
            failed = true;
          } else {
            results.push(`✅ LCP: ${perf.lcp}s`);
          }

          if (perf.fid > budgets.fid) {
            results.push(`❌ FID: ${perf.fid}ms (budget: ${budgets.fid}ms)`);
            failed = true;
          } else {
            results.push(`✅ FID: ${perf.fid}ms`);
          }

          if (perf.cls > budgets.cls) {
            results.push(`❌ CLS: ${perf.cls} (budget: ${budgets.cls})`);
            failed = true;
          } else {
            results.push(`✅ CLS: ${perf.cls}`);
          }

          if (perf.bundleSize > budgets.bundleSize) {
            results.push(`❌ Bundle: ${perf.bundleSize}KB (budget: ${budgets.bundleSize}KB)`);
            failed = true;
          } else {
            results.push(`✅ Bundle: ${perf.bundleSize}KB`);
          }

          console.log(results.join('\n'));

          if (failed) {
            process.exit(1);
          }
          EOF

      - name: Comment Performance Results
        if: always()
        uses: actions/github-script@v7
        with:
          script: |
            const fs = require('fs');
            const perf = JSON.parse(fs.readFileSync('performance.json'));

            const comment = `## 📊 Performance Report

            | Metric | Value | Budget | Status |
            |--------|-------|--------|--------|
            | LCP | ${perf.lcp}s | 2.5s | ${perf.lcp <= 2.5 ? '✅' : '❌'} |
            | FID | ${perf.fid}ms | 100ms | ${perf.fid <= 100 ? '✅' : '❌'} |
            | CLS | ${perf.cls} | 0.1 | ${perf.cls <= 0.1 ? '✅' : '❌'} |
            | Bundle Size | ${perf.bundleSize}KB | 300KB | ${perf.bundleSize <= 300 ? '✅' : '❌'} |

            ${perf.lcp > 2.5 || perf.fid > 100 || perf.cls > 0.1 || perf.bundleSize > 300
              ? '⚠️ **Performance budgets exceeded. Please optimize before merging.**'
              : '✅ **All performance budgets met!**'}`;

            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: comment
            });
```

**说明：**

The Chrome DevTools MCP works in headless Chrome environments. Make sure your CI environment has Chrome installed (it's pre-installed on GitHub Actions ubuntu-latest runners).

## Advanced Performance Workflows

### Step 9: Competitor Comparison

Compare your site's performance with competitors:

**TUI Mode Prompt:**
```
Compare performance between my site and competitors:

Sites to test:
- http://localhost:3000 (my site)
- https://competitor1.com
- https://competitor2.com

For each site:
1. Navigate to homepage
2. Record performance trace with reload
3. Extract LCP, FID, CLS, Total Blocking Time
4. List network requests and calculate total page size
5. Count JavaScript and CSS files

Create a comparison table showing:
- Site name
- All Core Web Vitals
- Total page size
- Number of requests
- Which site performs best for each metric

Provide specific recommendations for how my site can improve based on what competitors do better."
```

### Step 10: Performance Testing Across Routes

Test performance consistency across your entire site:

**TUI Mode Prompt:**
```
Test performance across all major routes:

Routes to test:
- / (homepage)
- /products
- /products/[id] (pick a sample product)
- /checkout
- /blog

For each route:
1. Navigate to the URL
2. Record performance trace
3. Extract LCP, Total Blocking Time, bundle size
4. Check console for errors
5. Take a screenshot

Generate a report showing:
- Which routes have the worst LCP
- Routes with the most JavaScript errors
- Bundle size variations across routes
- Recommendations for route-specific optimizations"
```

### Step 11: Mobile Performance Analysis

Focus on mobile-specific performance issues:

**TUI Mode Prompt:**
```
Analyze mobile performance for http://localhost:3000:

1. Resize viewport to mobile (375x667)
2. Emulate Slow 3G network + 4x CPU slowdown
3. Record performance trace with reload
4. Analyze Performance Insights for mobile-specific issues
5. Check for:
   - Touch target sizes too small
   - Horizontal scrolling issues
   - Oversized images not optimized for mobile
   - Excessive JavaScript on mobile
6. Take mobile screenshot
7. Provide mobile-specific optimization recommendations"
```

## Performance Troubleshooting

### Debug Performance Regressions

Quickly identify what caused a performance regression:

**TUI Mode Prompt:**
```
Debug performance regression on http://localhost:3000:

1. Record performance trace
2. Analyze all Performance Insights
3. List console errors and warnings
4. Check network requests for:
   - Failed requests
   - Slow requests (>1s)
   - Large requests (>500KB)
5. Identify the top 3 performance bottlenecks
6. For each bottleneck, suggest:
   - Root cause
   - Specific code or resource causing it
   - Step-by-step fix
   - Expected performance improvement"
```

### Performance Issue Quick Reference

| Issue | Quick Fix Command (in cn TUI) |
| :---- | :---------------------------- |
| Slow LCP | `"Find render-blocking resources and suggest preloading or deferring"` |
| High CLS | `"Detect layout shifts and identify unsized images or dynamic content"` |
| Long Tasks | `"Find JavaScript tasks over 50ms and suggest code splitting"` |
| Large Bundles | `"List all JavaScript files, identify largest ones, suggest lazy loading"` |
| Slow Network | `"Find requests over 500KB and suggest compression or optimization"` |
| Console Errors | `"List all console errors and suggest fixes"` |

## What You've Built

After completing this guide, you have a complete **AI-powered performance analysis system** that:

- ✅ **Uses natural language** — Simple prompts instead of complex DevTools commands
- ✅ **Analyzes automatically** — AI interprets performance traces and suggests fixes
- ✅ **Runs continuously** — Automated validation in CI/CD pipelines
- ✅ **Ensures quality** — Performance checks prevent regressions from shipping

#### Continuous AI

Your performance workflow now operates at **[Level 2 Continuous AI](<https://blog.continue.dev/what-is-continuous-ai-a-developers-guide/>)** - AI handles routine performance analysis and debugging with human oversight through review and approval of changes.

## Chrome DevTools MCP Capabilities

#### Performance Tracing

  **Tools Available**
  - `performance_start_trace`: Start recording with auto-reload
  - `performance_stop_trace`: Stop and analyze trace
  - `performance_analyze_insight`: Deep dive into specific issues

#### Network Debugging

  **Tools Available**
  - `list_network_requests`: See all requests and sizes
  - `get_network_request`: Inspect specific request details
  - `emulate_network`: Test under various network conditions

#### Console Analysis

  **Tools Available**
  - `list_console_messages`: Get all console logs and errors
  - `evaluate_script`: Run JavaScript in page context
  - Automatic error pattern detection

#### Visual Testing

  **Tools Available**
  - `take_screenshot`: Capture full or partial page
  - `take_snapshot`: Get accessibility tree snapshot
  - `resize_page`: Test responsive layouts

## Performance Best Practices

Key metrics to monitor for optimal web performance:

#### Core Web Vitals

- LCP < 2.5s (good)
- FID < 100ms (good)
- CLS < 0.1 (good)
- INP < 200ms (good)

#### Loading Performance

- Total page size < 1MB
- JavaScript < 300KB
- Time to Interactive < 3.8s
- First Contentful Paint < 1.8s

#### Runtime Performance

- No long tasks > 50ms
- 60 FPS during interactions
- No memory leaks
- Efficient event listeners

## Advanced Testing Scenarios

### A/B Test Performance Impact

**TUI Mode Prompt:**
```
Compare performance of two implementations:
1. Test variant A at http://localhost:3000?variant=A
2. Test variant B at http://localhost:3000?variant=B
Run each test 5 times and calculate average LCP, FID, CLS
Determine which variant has better performance and by how much
```

### Lighthouse Score Tracking

**TUI Mode Prompt:**
```
Create a script that runs daily performance audits:
1. Navigate to production site
2. Record performance trace
3. Calculate Lighthouse-style performance score based on:
   - FCP (10%)
   - SI (10%)
   - LCP (25%)
   - TTI (10%)
   - TBT (30%)
   - CLS (15%)
4. Track score over time in performance-history.json
5. Alert if score drops below 90
```

### Performance Regression Detection

**TUI Mode Prompt:**
```
Set up automated regression detection:
1. Record baseline performance for main branch
2. Save baseline metrics to baseline-perf.json
3. On each PR, run performance tests
4. Compare PR metrics with baseline
5. Flag regressions over 10% for any metric
6. Generate visual diff report with screenshots
```

## Next Steps

1. **Analyze your first site** - Try the baseline performance trace on your current project
2. **Debug bottlenecks** - Use the network analysis prompt to fix slow requests
3. **Set up CI pipeline** - Add the GitHub Actions workflow to your repo
4. **Test throttling** - Measure performance under real-world network conditions
5. **Monitor trends** - Track Core Web Vitals over time

## Additional Resources

#### [Chrome DevTools MCP](<https://github.com/ChromeDevTools/chrome-devtools-mcp>)

Official Chrome DevTools MCP repository

#### [Continue Documentation](<https://docs.continue.dev>)

Explore Continue documentation and guides

#### [DevTools Documentation](<https://developer.chrome.com/docs/devtools>)

Complete Chrome DevTools documentation

#### [Web Vitals Guide](<https://web.dev/vitals/>)

Learn about Core Web Vitals


</details>
