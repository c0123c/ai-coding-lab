# 用 Continue 与 Snyk MCP 执行安全扫描

工程教程。展示代码、依赖、基础设施、容器和 PR 扫描，通过规则及 Actions 建立安全检查，说明认证、修复验证和连接故障排查。

- 来源：Continue
- 资料类型：工程教程
- 存档版本：`5522c6f44ca0`。
- 文档时间：源码提交（存档版）：[2026-03-24](<https://github.com/continuedev/continue/commit/bd36638ee0cf9fb90c314647bab2f1c6897aa6fe>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：需要 Snyk 和目标项目权限；扫描结果及修复须独立核验。

[原文发布页](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/guides/snyk-mcp-continue-cookbook.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Continue/用 Continue 与 Snyk MCP 执行安全扫描.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: "Automated Security Scanning with Snyk MCP and Continue"
description: "Set up an AI-powered security workflow that automatically scans your code, dependencies, infrastructure, and containers using natural language commands."
sidebarTitle: "Snyk Security Scanning with Continue"
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

> 阅读说明：原站会自动选择操作系统选项；本地阅读版已展开所有安装选项。

#### 你将构建什么

一个自动安全扫描系统，使用 Continue 的 AI agent 和 Snyk
MCP 来识别代码、依赖项、基础设施和 
 容器中的漏洞 - 全部通过简单的自然语言提示

## 演示视频

[Snyk MCP Continue 食谱演示](<https://www.youtube.com/embed/cwVnKOf3tVg>)

## 先决条件

开始之前，请确保您拥有：

- Node.js 18+本地安装
- [Snyk 帐户](<https://snyk.io/>)（免费层作品）
- 用于扫描漏洞的本地项目

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

#### 将您的项目添加到 Snyk

1. 在 [snyk.io](<https://snyk.io/>) 注册 Snyk 帐户
2. 通过导入代码存储库 (Git
提供商或手动上传）
3. 在本地安装并验证 Snyk CLI：
```bash
npm install -g snyk
snyk auth
```
这将打开您的浏览器以使用您的 Snyk 帐户进行身份验证。

**注释：**

**重要**：Snyk MCP 要求 Snyk CLI 在本地进行身份验证。在将 Continue agent 与 Snyk MCP 一起使用之前，运行 `snyk auth` 进行身份验证。

**注意：**

要在无头模式下使用agents，您需要[Continue API密钥](<https://continue.dev/settings/api-keys>)。

## Snyk Continuous AI 工作流程选项

#### 🚀 最快的成功之路

跳过手动设置并使用我们预构建的 Snyk Continuous AI agent，其中包括 
Snyk MCP 和优化的安全扫描工作流程，以获得更一致的结果。

确保满足上述 **先决条件 ** 后，您有两条途径可以开始：

#### ⚡ 快速入门（推荐）

#### 加载预建的Agent

导航到您的项目目录并运行：
```bash
cn --agent continuedev/snyk-continuous-ai-agent
```

此 agent 包括：
- **Snyk MCP** 已预先配置并可供使用
- **以安全为中心的规则**最佳实践

#### 运行您的第一次安全扫描

从您的项目目录开始进行全面的安全扫描：
```bash
 # Headless mode
cn -p "Run a complete security scan on this project including code vulnerabilities, dependencies, and any IaC files. Summarize findings by severity." --auto
```

就是这样！ agent 自动处理一切。

**说明：**

**为什么使用Agent？**预构建的agent提供一致的安全扫描工作流程并自动处理MCP配置，从而更轻松地开始使用AI-支持的安全扫描。

#### 🛠️手动设置

#### 将 Snyk MCP 添加到 Continue CLI

将 Snyk MCP 添加到您的配置中：

```bash
npx -y snyk@latest mcp -t stdio
```

**提示：**

**安装方法：**
1. **快速CLI安装**：`cn --mcp snyk/snyk-mcp`
2. **手动配置**：将MCP添加到`mcpServers`部分下的`~/.continue/config.json`

安装后，Snyk MCP 工具即可用于您的 Continue agent 的所有提示。

**说明：**

MCP 首次使用时将请求身份验证和文件夹信任权限。
这由 Continue agent 自动处理。

#### 添加启动时安全规则

将安全扫描规则添加到您的配置中以启用自动安全扫描。

这些规则将您的 agent 配置为：
- **运行 [SAST](<https://snyk.io/learn/application-security/sast/>) 扫描 ** 新生成或修改的代码
- **添加或更新包时检查依赖关系**
- **使用 Snyk 的建议自动修复问题**，然后重新扫描

#### 运行您的第一次安全扫描

从全面的安全扫描开始：
```bash
# TUI mode
cn "Run a complete security scan on this project including code vulnerabilities, dependencies, and any IaC files. Summarize findings by severity."
```

#### Agent 要求

要使用预构建的 agent，您需要：
    - **Continue CLI 专业计划 ** 以及模型附加组件，OR
    - **您自己的API密钥**配置为环境变量

agent 将自动检测并使用您的配置以及预配置的 Snyk MCP 进行安全扫描操作。

---

## 安全扫描食谱

现在，您可以使用自然语言提示来运行全面的安全扫描。 Continue agent 自动调用适当的 Snyk MCP 工具。

**说明：**

**在哪里运行这些工作流程：**
- **IDE 扩展 **：在 VS Code、JetBrains 或其他支持的 IDE 中使用 Continue
- **终端（TUI模式）**：运行`cn`进入交互模式，然后输入提示
- **CLI（无头模式）**：使用 `cn -p "your prompt" --auto` 进行无头命令

**首先在计划模式下测试**：在运行可能会导致
更改的安全扫描之前，请在计划模式下测试提示（请参阅[计划模式
指南](<https://github.com/guides/plan-mode-guide>)；按**Shift+Tab**切换TUI/IDE中的模式）。这个
向您展示了agent在不执行它的情况下会做什么。例如：“运行 
Snyk 代码扫描并修复最重要的 3 问题”`

### 代码漏洞扫描（[SAST](<https://snyk.io/learn/application-security/sast/>)）

#### 静态应用程序安全测试

扫描源代码以查找安全漏洞和代码质量问题。

**TUI 模式提示：**
```
Run a Snyk Code scan on this repo with severity threshold medium.
Summarize issues with file:line. Propose minimal diffs for the top 3
and rerun to verify.
```

**无头模式提示：**
```bash
cn -p "Run a Snyk Code scan on this repo with severity threshold medium. Summarize issues with file:line. Propose minimal diffs for the top 3 and rerun to verify." --auto
```

### 依赖关系扫描（[SCA](<https://snyk.io/learn/software-composition-analysis-sca/>)）

#### 软件构成分析

检查开源依赖项是否存在已知漏洞。

**TUI 模式提示：**
```
Run Snyk Open Source on this repo (include dev deps).
Summarize vulnerable paths and propose a minimal-risk upgrade plan.
Re-test after the plan (dry run).
```

**无头模式提示：**
```bash
cn -p "Run Snyk Open Source on this repo (include dev deps). Summarize vulnerable paths and propose a minimal-risk upgrade plan. Re-test after the plan (dry run)." --auto
```

### 基础设施即代码 ([IaC](<https://snyk.io/learn/infrastructure-as-code-iac/>))

#### IaC 安全

扫描 Terraform、CloudFormation 和 Kubernetes 配置是否存在错误配置。

**TUI 模式提示：**
```
Scan ./infra with Snyk IaC. Report high/critical misconfigs
with exact files/lines. Provide code changes and re-scan to confirm.
```

**无头模式提示：**
```bash
cn -p "Scan ./infra with Snyk IaC. Report high/critical misconfigs with exact files/lines. Provide code changes and re-scan to confirm." --auto
```

### 集装箱扫描

#### 集装箱安全

分析 Docker 映像以查找基础映像和包中的漏洞。

**TUI 模式提示：**
```
Scan image my-api:latest. Exclude base image vulns.
Print dependency tree. Recommend a safer base image or upgrades.
Re-test after the change (dry run).
```

**无头模式提示：**
```bash
cn -p "Scan image my-api:latest. Exclude base image vulns. Print dependency tree. Recommend a safer base image or upgrades. Re-test after the change (dry run)." --auto
```

### 拉取请求扫描

#### 仅更改的文件

重点扫描修改后的文件以在合并之前发现问题。

**TUI 模式提示：**
```
Scan only files changed since origin/main with Snyk Code.
Block if new high issues would be introduced. Show deltas.
```

**无头模式提示：**
```bash
cn -p "Scan only files changed since origin/main with Snyk Code. Block if new high issues would be introduced. Show deltas." --auto
```

### 安全学习

#### Snyk 学习集成

根据已识别的漏洞访问安全教育资源 ([CWE](<https://cwe.mitre.org/>))。

**TUI 模式提示：**
```
Open Snyk Learn lessons related to the top CWE(s) from this scan.
```

**无头模式提示：**
```bash
cn -p "Open Snyk Learn lessons related to the top CWE(s) from this scan." --auto
```

## 通过 GitHub 操作实现持续安全

此示例演示了一个 **连续 AI 工作流程 **，其中安全扫描在拉取请求上自动运行，生成 AI- 支持的缓解建议，并将其作为 PR 评论发布。

**说明：**

**关于--auto标志**：`--auto`标志使工具能够连续运行，无需手动确认。这对于无头模式至关重要，其中 agent 需要自动执行多个工具来完成安全扫描、漏洞分析和修复验证等任务。

### 添加 GitHub 秘密

导航到 ** 存储库设置 → 秘密和变量 → 操作 ** 并添加：

- `CONTINUE_API_KEY`：来自 [continue.dev/settings/api-keys](<https://continue.dev/settings/api-keys>) 的 Continue API 密钥
- `SNYK_TOKEN`：来自 [app.snyk.io/account](<https://app.snyk.io/account>) 的 Snyk 身份验证令牌

### 创建工作流程文件

在您的存储库中创建 `.github/workflows/snyk-security.yml`：

```yaml
name: Snyk Security Scanning

on:
  pull_request:
    branches: [main]

jobs:
  security-scan:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0 # Fetch all history for git diff

      - name: Get Changed Files
        id: changed-files
        run: |
          echo "📝 Getting changed files since main branch..."
          CHANGED_FILES=$(git diff --name-only origin/${{ github.base_ref }}...HEAD | tr '\n' ' ')
          echo "changed_files=$CHANGED_FILES" >> $GITHUB_OUTPUT
          echo "Changed files: $CHANGED_FILES"

      - name: Set up Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '18'

      - name: Install Snyk CLI
        run: |
          npm install -g snyk
          echo "✅ Snyk CLI installed"

      - name: Install Continue CLI
        run: |
          npm install -g @continuedev/cli
          echo "✅ Continue CLI installed"

      - name: Validate Secrets
        run: |
          if [ -z "${{ secrets.SNYK_TOKEN }}" ]; then
            echo "❌ Error: SNYK_TOKEN secret is not set"
            exit 1
          fi
          if [ -z "${{ secrets.CONTINUE_API_KEY }}" ]; then
            echo "⚠️ Warning: CONTINUE_API_KEY not set - AI mitigation suggestions will be skipped"
          fi
          echo "✅ Required secrets validated"

      - name: Authenticate Snyk
        env:
          SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
        run: |
          snyk auth "$SNYK_TOKEN"
          echo "✅ Snyk authenticated"

      - name: Run Security Scans
        id: security-scan
        continue-on-error: true
        env:
          SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
          CHANGED_FILES: ${{ steps.changed-files.outputs.changed_files }}
        run: |
          SCAN_FAILED=0

          if [ -n "$CHANGED_FILES" ]; then
            echo "🔍 Running targeted scan on changed files..."
            echo "Changed files: $CHANGED_FILES"
            FILE_ARGS=""
            for file in $CHANGED_FILES; do
              if [[ "$file" =~ \.(js|jsx|ts|tsx|py|java|go|rb)$ ]]; then
                FILE_ARGS="$FILE_ARGS --file=$file"
              fi
            done

            if [ -n "$FILE_ARGS" ]; then
              echo "🔍 Running Snyk Code scan on changed files..."
              snyk code test $FILE_ARGS --severity-threshold=high --json > snyk-code-results.json || {
                echo "❌ Snyk Code found high severity issues in changed files"
                SCAN_FAILED=1
              }
            else
              echo "⚠️ No scannable code files changed, skipping Snyk Code scan"
              echo '{"runs": [{"results": []}]}' > snyk-code-results.json
            fi
          else
            echo "⚠️ No changed files detected, creating empty results"
            echo '{"runs": [{"results": []}]}' > snyk-code-results.json
          fi

          echo "📦 Checking dependencies..."
          snyk test --severity-threshold=high --json > snyk-oss-results.json || {
            echo "❌ Snyk Open Source found high severity issues"
            SCAN_FAILED=1
          }

          if [ $SCAN_FAILED -eq 1 ]; then
            echo "scan_status=failed" >> $GITHUB_OUTPUT
            echo "⚠️ Scans completed with issues - continuing to generate mitigation suggestions"
          else
            echo "scan_status=passed" >> $GITHUB_OUTPUT
            echo "✅ All security scans passed"
          fi

      - name: Generate AI Mitigation Suggestions
        if: always() && steps.security-scan.outputs.scan_status == 'failed'
        continue-on-error: true
        env:
          CONTINUE_API_KEY: ${{ secrets.CONTINUE_API_KEY }}
          SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
        run: |
          echo "🤖 Generating AI-powered mitigation suggestions..."

          # Create a summary of findings for Continue CLI
          FINDINGS_SUMMARY=$(cat snyk-code-results.json snyk-oss-results.json | jq -r '
            if .runs then
              .runs[0].results[] | "Code Issue: \(.message.text) in \(.locations[0].physicalLocation.artifactLocation.uri) (Severity: \(.level))"
            elif .vulnerabilities then
              .vulnerabilities[] | "Dependency Issue: \(.title) in \(.packageName)@\(.version) (Severity: \(.severity))"
            else
              empty
            end
          ' | head -20)

          if [ -n "$FINDINGS_SUMMARY" ]; then
            echo "📋 Security findings to analyze:"
            echo "$FINDINGS_SUMMARY"
            echo ""

            # Use Continue CLI to generate mitigation suggestions
            PROMPT="Analyze these Snyk security findings and provide specific, actionable mitigation steps for each issue. Focus on: 1) Root cause, 2) Immediate fix, 3) Long-term prevention. Findings: $FINDINGS_SUMMARY. Provide clear, prioritized recommendations."

            cn --agent continuedev/snyk-continuous-ai-agent -p "$PROMPT" --auto > mitigation-suggestions.md || {
              echo "⚠️ Warning: Could not generate AI suggestions"
              exit 0
            }

            if [ -f mitigation-suggestions.md ]; then
              echo "✅ AI mitigation suggestions generated"
              echo ""
              echo "--- Mitigation Suggestions ---"
              cat mitigation-suggestions.md
            fi
          else
            echo "⚠️ No findings to analyze"
          fi

      - name: Post Mitigation Summary to PR
        if: steps.security-scan.outputs.scan_status == 'failed' && hashFiles('mitigation-suggestions.md') != ''
        continue-on-error: true
        env:
          GH_TOKEN: ${{ github.token }}
        run: |
          if [ -f mitigation-suggestions.md ]; then
            echo "💬 Posting mitigation summary to PR..."

            # Create PR comment with mitigation suggestions
            cat > pr-comment.md <<'EOF'
          ## 🛡️ Snyk Mitigation Summary

          Snyk has identified security issues in this PR. Here are AI-generated mitigation recommendations:

          EOF

            cat mitigation-suggestions.md >> pr-comment.md

            cat >> pr-comment.md <<EOF

          ---
          **Scan Details:**
          - 📊 Full report available in workflow artifacts
          - 🔍 Review the [workflow run](${{ github.server_url }}/${{ github.repository }}/actions/runs/${{ github.run_id }}) for complete details
          - 🤖 Generated with Continue CLI + Snyk

          _This is an automated security analysis. Please review and address the findings before merging._
          EOF

            gh pr comment ${{ github.event.pull_request.number }} --body-file pr-comment.md

            echo "✅ Mitigation summary posted to PR"
          else
            echo "⚠️ No mitigation suggestions file found"
          fi

      - name: Generate Security Report
        if: always()
        continue-on-error: true
        env:
          SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
        run: |
          echo "📊 Generating security report..."
          {
            echo "# Security Scan Report"
            echo ""
            echo "**Date:** $(date -u +"%Y-%m-%d %H:%M:%S UTC")"
            echo "**Branch:** ${{ github.ref_name }}"
            echo "**Commit:** ${{ github.sha }}"
            echo "**Status:** ${{ steps.security-scan.outputs.scan_status }}"
            echo ""

            echo "## Snyk Code Scan Results"
            if [ -f snyk-code-results.json ]; then
              jq -r '.runs[0].results[] | "- **\(.level | ascii_upcase)**: \(.message.text) in \(.locations[0].physicalLocation.artifactLocation.uri)"' snyk-code-results.json || echo "No code issues found"
            else
              echo "No scan results available"
            fi
            echo ""

            echo "## Snyk Open Source Scan Results"
            if [ -f snyk-oss-results.json ]; then
              jq -r '.vulnerabilities[] | "- **\(.severity | ascii_upcase)**: \(.title) (\(.packageName)@\(.version))"' snyk-oss-results.json || echo "No dependency issues found"
            else
              echo "No scan results available"
            fi
            echo ""

            if [ -f mitigation-suggestions.md ]; then
              echo "## AI-Powered Mitigation Suggestions"
              echo ""
              cat mitigation-suggestions.md
            fi
          } > scan-results.md

          if [ -f scan-results.md ]; then
            echo "✅ Security report generated successfully"
            cat scan-results.md
          else
            echo "⚠️ Warning: scan-results.md was not created"
          fi

      - name: Upload Security Report
        if: always()
        continue-on-error: true
        uses: actions/upload-artifact@v4
        with:
          name: security-scan-results
          path: |
            scan-results.md
            snyk-code-results.json
            snyk-oss-results.json
            mitigation-suggestions.md
          if-no-files-found: warn

      - name: Fail if Security Issues Found
        if: steps.security-scan.outputs.scan_status == 'failed'
        run: |
          echo "❌ Security scan failed - high severity issues found"
          echo "📋 Review the security report artifact for details and mitigation suggestions"
          exit 1
```

**说明：**

**关于SNYK_TOKEN**：工作流程以两种方式使用SNYK_TOKEN：
1. **Direct Snyk CLI 身份验证** - 对 Snyk CLI 进行身份验证以运行扫描
2. **Continue CLI 访问 ** - 当 Continue 生成 AI 缓解建议时可用作环境变量

当 Snyk MCP 操作需要时，`cn` agent 自动使用 SNYK_TOKEN。

**说明：**

此工作流程演示了几个高级功能：
- **更改文件检测**：仅扫描PR中修改的文件
- **AI 缓解措施 **：使用 Continue CLI 生成可操作的缓解步骤
- **PR 评论**：自动将缓解建议发布为 PR 评论
- **综合报告**：生成包含工件的详细安全报告

## 安全护栏

使用 Continue 的规则系统实施自动化安全策略。请参阅 [规则深入探讨 ](<https://docs.continue.dev/customize/deep-dives/rules>) 了解创作技巧。

#### 预提交扫描

```bash
"Always run Snyk Code before committing newly
generated code; refuse to proceed if high
issues remain."
```

#### 依赖安全

```bash
"When adding/updating a dependency, run Snyk Open Source, choose the
lowest-risk upgrade, and re-test."
```

#### 容器硬化

```bash
"Before building containers, scan base images and recommend
security-hardened alternatives."
```

#### IaC 合规性

```bash
"Scan all Terraform changes for compliance
violations before applying infrastructure."
```

**提示：**

在配置中启用 ** 启动时安全 ** 规则，以自动将 
 这些防护措施应用于所有代码生成和修改。

## 故障排除

### 身份验证问题

```bash
"Check Snyk auth status and current org. If not authenticated,
help me authenticate. Then run a quick Code scan on ./
with severity medium and print one example issue."
```

### 修复验证

```bash
"Propose minimal diffs only in affected files,
then rerun the same Snyk scan to confirm resolution."
```

### 连接问题

**检查：**

**验证步骤：**
- Snyk MCP 已安装并配置
- 启用初始安全规则
- 身份验证成功完成
- 项目文件夹已受信任

## 你建造了什么

完成本指南后，您将拥有一个完整的 **AI- 支持的安全系统 **，该系统：

- ✅ 使用自然语言 — 简单的提示而不是复杂的 CLI 命令
- ✅ 自动修复 - AI 建议并验证安全修复
- ✅ 连续运行 — 在 CI/CD 管道中自动扫描
- ✅ 实施护栏——安全规则防止易受攻击的代码被发送

#### 连续AI

您的安全工作流程现在在 **[ 级别 2 连续 
AI](<https://blog.continue.dev/what-is-continuous-ai-a-developers-guide/>)** -
AI 运行，通过审核和批准修复，在人工监督下处理例行安全扫描和修复
。

## 下一步

1. **运行第一次扫描** - 在当前项目上尝试 [SAST](<https://snyk.io/learn/application-security/sast/>) 提示符
2. **审查结果** - 分析安全报告并实施修复
3. **设置 CI 管道** - 将 GitHub 操作工作流程添加到您的存储库
4. **自定义规则** - 添加项目特定的安全策略
5. **监控趋势** - 跟踪[漏洞](<https://snyk.io/learn/security-vulnerability/>)随着时间的推移减少

## 其他资源

#### [Snyk 文档](<https://docs.snyk.io>)

完整的 Snyk 平台文档

#### [Continue 文档](<https://docs.continue.dev>)

探索 Continue 文档和指南

#### [安全最佳实践](<https://snyk.io/learn>)

了解安全编码实践

#### [MCP 概念](<https://docs.snyk.io/integrations/developer-guardrails-for-agentic-workflows>)

了解 MCP 架构


## 原文对照

<details>
<summary>展开完整原文</summary>

> 阅读说明：原站会自动选择操作系统选项；本地阅读版已展开所有安装选项。

#### What You'll Build

An automated security scanning system that uses Continue's AI agent with Snyk
MCP to identify vulnerabilities in code, dependencies, infrastructure, and
containers - all through simple natural language prompts

## Demo Video

[Snyk MCP Continue Cookbook Demo](<https://www.youtube.com/embed/cwVnKOf3tVg>)

## Prerequisites

Before starting, ensure you have:

- Node.js 18+ installed locally
- [Snyk account](<https://snyk.io/>) (free tier works)
- A local project to scan for vulnerabilities

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

#### Add Your Project to Snyk

1. Sign up for a Snyk account at [snyk.io](<https://snyk.io/>)
2. Create a new project in Snyk by importing your code repository (Git 
provider or manual upload)
3. Install and authenticate the Snyk CLI locally:
```bash
npm install -g snyk
snyk auth
```
 This will open your browser to authenticate with your Snyk account.

**注释：**

**Important**: The Snyk MCP requires the Snyk CLI to be authenticated locally. Run `snyk auth` to authenticate before using the Continue agent with Snyk MCP.

**注意：**

To use agents in headless mode, you need a [Continue API key](<https://continue.dev/settings/api-keys>).

## Snyk Continuous AI Workflow Options

#### 🚀 Fastest Path to Success

Skip the manual setup and use our pre-built Snyk Continuous AI agent that includes
the Snyk MCP and optimized security scanning workflows for more consistent results.

After ensuring you meet the **Prerequisites** above, you have two paths to get started:

#### ⚡ Quick Start (Recommended)

#### Load the Pre-Built Agent

Navigate to your project directory and run:
```bash
cn --agent continuedev/snyk-continuous-ai-agent
```

This agent includes:
- **Snyk MCP** pre-configured and ready to use
- **Security-focused rules** for best practices

#### Run Your First Security Scan

From your project directory, start with a comprehensive security scan:
```bash
 # Headless mode
cn -p "Run a complete security scan on this project including code vulnerabilities, dependencies, and any IaC files. Summarize findings by severity." --auto
```

That's it! The agent handles everything automatically.

**说明：**

**Why Use the Agent?** The pre-built agent provides consistent security scanning workflows and handles MCP configuration automatically, making it easier to get started with AI-powered security scanning.

#### 🛠️ Manual Setup

#### Add Snyk MCP to Continue CLI

Add the Snyk MCP to your configuration:

```bash
npx -y snyk@latest mcp -t stdio
```

**提示：**

**Installation methods:**
1. **Quick CLI install**: `cn --mcp snyk/snyk-mcp`
2. **Manual configuration**: Add the MCP to your `~/.continue/config.json` under the `mcpServers` section

Once installed, Snyk MCP tools become available to your Continue agent for all prompts.

**说明：**

The MCP will request authentication and folder trust permissions when first used.
This is handled automatically by the Continue agent.

#### Add Secure-at-Inception Rules

Add security scanning rules to your configuration to enable automatic security scanning.

These rules configure your agent to:
- **Run [SAST](<https://snyk.io/learn/application-security/sast/>) scans** on newly generated or modified code
- **Check dependencies** when adding or updating packages
- **Auto-fix issues** using Snyk's recommendations, then rescan

#### Run Your First Security Scan

Start with a comprehensive security scan:
```bash
# TUI mode
cn "Run a complete security scan on this project including code vulnerabilities, dependencies, and any IaC files. Summarize findings by severity."
```

#### Agent Requirements

  To use the pre-built agent, you need either:
    - **Continue CLI Pro Plan** with the models add-on, OR
    - **Your own API keys** configured as environment variables

  The agent will automatically detect and use your configuration along with the pre-configured Snyk MCP for security scanning operations.

---

## Security Scanning Recipes

Now you can use natural language prompts to run comprehensive security scans. The Continue agent automatically calls the appropriate Snyk MCP tools.

**说明：**

**Where to run these workflows:**
- **IDE Extensions**: Use Continue in VS Code, JetBrains, or other supported IDEs
- **Terminal (TUI mode)**: Run `cn` to enter interactive mode, then type your prompts
- **CLI (headless mode)**: Use `cn -p "your prompt" --auto` for headless commands

**Test in Plan Mode First**: Before running security scans that might make
changes, test your prompts in plan mode (see the [Plan Mode
Guide](<https://github.com/guides/plan-mode-guide>); press **Shift+Tab** to switch modes in TUI/IDE). This
shows you what the agent will do without executing it. For example: `"Run a
Snyk Code scan and fix the top 3 issues"`

### Code Vulnerability Scanning ([SAST](<https://snyk.io/learn/application-security/sast/>))

#### Static Application Security Testing

Scan your source code for security vulnerabilities and code quality issues.

**TUI Mode Prompt:**
```
Run a Snyk Code scan on this repo with severity threshold medium.
Summarize issues with file:line. Propose minimal diffs for the top 3
and rerun to verify.
```

**Headless Mode Prompt:**
```bash
cn -p "Run a Snyk Code scan on this repo with severity threshold medium. Summarize issues with file:line. Propose minimal diffs for the top 3 and rerun to verify." --auto
```

### Dependency Scanning ([SCA](<https://snyk.io/learn/software-composition-analysis-sca/>))

#### Software Composition Analysis

Check open source dependencies for known vulnerabilities.

**TUI Mode Prompt:**
```
Run Snyk Open Source on this repo (include dev deps).
Summarize vulnerable paths and propose a minimal-risk upgrade plan.
Re-test after the plan (dry run).
```

**Headless Mode Prompt:**
```bash
cn -p "Run Snyk Open Source on this repo (include dev deps). Summarize vulnerable paths and propose a minimal-risk upgrade plan. Re-test after the plan (dry run)." --auto
```

### Infrastructure as Code ([IaC](<https://snyk.io/learn/infrastructure-as-code-iac/>))

#### IaC Security

Scan Terraform, CloudFormation, and Kubernetes configs for misconfigurations.

**TUI Mode Prompt:**
```
Scan ./infra with Snyk IaC. Report high/critical misconfigs
with exact files/lines. Provide code changes and re-scan to confirm.
```

**Headless Mode Prompt:**
```bash
cn -p "Scan ./infra with Snyk IaC. Report high/critical misconfigs with exact files/lines. Provide code changes and re-scan to confirm." --auto
```

### Container Scanning

#### Container Security

Analyze Docker images for vulnerabilities in base images and packages.

**TUI Mode Prompt:**
```
Scan image my-api:latest. Exclude base image vulns.
Print dependency tree. Recommend a safer base image or upgrades.
Re-test after the change (dry run).
```

**Headless Mode Prompt:**
```bash
cn -p "Scan image my-api:latest. Exclude base image vulns. Print dependency tree. Recommend a safer base image or upgrades. Re-test after the change (dry run)." --auto
```

### Pull Request Scanning

#### Changed Files Only

Focus scanning on modified files to catch issues before merging.

**TUI Mode Prompt:**
```
Scan only files changed since origin/main with Snyk Code.
Block if new high issues would be introduced. Show deltas.
```

**Headless Mode Prompt:**
```bash
cn -p "Scan only files changed since origin/main with Snyk Code. Block if new high issues would be introduced. Show deltas." --auto
```

### Security Learning

#### Snyk Learn Integration

Access security education resources based on identified vulnerabilities ([CWE](<https://cwe.mitre.org/>)).

**TUI Mode Prompt:**
```
Open Snyk Learn lessons related to the top CWE(s) from this scan.
```

**Headless Mode Prompt:**
```bash
cn -p "Open Snyk Learn lessons related to the top CWE(s) from this scan." --auto
```

## Continuous Security with GitHub Actions

This example demonstrates a **Continuous AI workflow** where security scanning runs automatically on pull requests, generates AI-powered mitigation suggestions, and posts them as PR comments.

**说明：**

**About the --auto flag**: The `--auto` flag enables tools to run continuously without manual confirmation. This is essential for headless mode where the agent needs to execute multiple tools automatically to complete tasks like security scanning, vulnerability analysis, and fix validation.

### Add GitHub Secrets

Navigate to **Repository Settings → Secrets and variables → Actions** and add:

- `CONTINUE_API_KEY`: Your Continue API key from [continue.dev/settings/api-keys](<https://continue.dev/settings/api-keys>)
- `SNYK_TOKEN`: Your Snyk authentication token from [app.snyk.io/account](<https://app.snyk.io/account>)

### Create Workflow File

Create `.github/workflows/snyk-security.yml` in your repository:

```yaml
name: Snyk Security Scanning

on:
  pull_request:
    branches: [main]

jobs:
  security-scan:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0 # Fetch all history for git diff

      - name: Get Changed Files
        id: changed-files
        run: |
          echo "📝 Getting changed files since main branch..."
          CHANGED_FILES=$(git diff --name-only origin/${{ github.base_ref }}...HEAD | tr '\n' ' ')
          echo "changed_files=$CHANGED_FILES" >> $GITHUB_OUTPUT
          echo "Changed files: $CHANGED_FILES"

      - name: Set up Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '18'

      - name: Install Snyk CLI
        run: |
          npm install -g snyk
          echo "✅ Snyk CLI installed"

      - name: Install Continue CLI
        run: |
          npm install -g @continuedev/cli
          echo "✅ Continue CLI installed"

      - name: Validate Secrets
        run: |
          if [ -z "${{ secrets.SNYK_TOKEN }}" ]; then
            echo "❌ Error: SNYK_TOKEN secret is not set"
            exit 1
          fi
          if [ -z "${{ secrets.CONTINUE_API_KEY }}" ]; then
            echo "⚠️ Warning: CONTINUE_API_KEY not set - AI mitigation suggestions will be skipped"
          fi
          echo "✅ Required secrets validated"

      - name: Authenticate Snyk
        env:
          SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
        run: |
          snyk auth "$SNYK_TOKEN"
          echo "✅ Snyk authenticated"

      - name: Run Security Scans
        id: security-scan
        continue-on-error: true
        env:
          SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
          CHANGED_FILES: ${{ steps.changed-files.outputs.changed_files }}
        run: |
          SCAN_FAILED=0

          if [ -n "$CHANGED_FILES" ]; then
            echo "🔍 Running targeted scan on changed files..."
            echo "Changed files: $CHANGED_FILES"
            FILE_ARGS=""
            for file in $CHANGED_FILES; do
              if [[ "$file" =~ \.(js|jsx|ts|tsx|py|java|go|rb)$ ]]; then
                FILE_ARGS="$FILE_ARGS --file=$file"
              fi
            done

            if [ -n "$FILE_ARGS" ]; then
              echo "🔍 Running Snyk Code scan on changed files..."
              snyk code test $FILE_ARGS --severity-threshold=high --json > snyk-code-results.json || {
                echo "❌ Snyk Code found high severity issues in changed files"
                SCAN_FAILED=1
              }
            else
              echo "⚠️ No scannable code files changed, skipping Snyk Code scan"
              echo '{"runs": [{"results": []}]}' > snyk-code-results.json
            fi
          else
            echo "⚠️ No changed files detected, creating empty results"
            echo '{"runs": [{"results": []}]}' > snyk-code-results.json
          fi

          echo "📦 Checking dependencies..."
          snyk test --severity-threshold=high --json > snyk-oss-results.json || {
            echo "❌ Snyk Open Source found high severity issues"
            SCAN_FAILED=1
          }

          if [ $SCAN_FAILED -eq 1 ]; then
            echo "scan_status=failed" >> $GITHUB_OUTPUT
            echo "⚠️ Scans completed with issues - continuing to generate mitigation suggestions"
          else
            echo "scan_status=passed" >> $GITHUB_OUTPUT
            echo "✅ All security scans passed"
          fi

      - name: Generate AI Mitigation Suggestions
        if: always() && steps.security-scan.outputs.scan_status == 'failed'
        continue-on-error: true
        env:
          CONTINUE_API_KEY: ${{ secrets.CONTINUE_API_KEY }}
          SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
        run: |
          echo "🤖 Generating AI-powered mitigation suggestions..."

          # Create a summary of findings for Continue CLI
          FINDINGS_SUMMARY=$(cat snyk-code-results.json snyk-oss-results.json | jq -r '
            if .runs then
              .runs[0].results[] | "Code Issue: \(.message.text) in \(.locations[0].physicalLocation.artifactLocation.uri) (Severity: \(.level))"
            elif .vulnerabilities then
              .vulnerabilities[] | "Dependency Issue: \(.title) in \(.packageName)@\(.version) (Severity: \(.severity))"
            else
              empty
            end
          ' | head -20)

          if [ -n "$FINDINGS_SUMMARY" ]; then
            echo "📋 Security findings to analyze:"
            echo "$FINDINGS_SUMMARY"
            echo ""

            # Use Continue CLI to generate mitigation suggestions
            PROMPT="Analyze these Snyk security findings and provide specific, actionable mitigation steps for each issue. Focus on: 1) Root cause, 2) Immediate fix, 3) Long-term prevention. Findings: $FINDINGS_SUMMARY. Provide clear, prioritized recommendations."

            cn --agent continuedev/snyk-continuous-ai-agent -p "$PROMPT" --auto > mitigation-suggestions.md || {
              echo "⚠️ Warning: Could not generate AI suggestions"
              exit 0
            }

            if [ -f mitigation-suggestions.md ]; then
              echo "✅ AI mitigation suggestions generated"
              echo ""
              echo "--- Mitigation Suggestions ---"
              cat mitigation-suggestions.md
            fi
          else
            echo "⚠️ No findings to analyze"
          fi

      - name: Post Mitigation Summary to PR
        if: steps.security-scan.outputs.scan_status == 'failed' && hashFiles('mitigation-suggestions.md') != ''
        continue-on-error: true
        env:
          GH_TOKEN: ${{ github.token }}
        run: |
          if [ -f mitigation-suggestions.md ]; then
            echo "💬 Posting mitigation summary to PR..."

            # Create PR comment with mitigation suggestions
            cat > pr-comment.md <<'EOF'
          ## 🛡️ Snyk Mitigation Summary

          Snyk has identified security issues in this PR. Here are AI-generated mitigation recommendations:

          EOF

            cat mitigation-suggestions.md >> pr-comment.md

            cat >> pr-comment.md <<EOF

          ---
          **Scan Details:**
          - 📊 Full report available in workflow artifacts
          - 🔍 Review the [workflow run](${{ github.server_url }}/${{ github.repository }}/actions/runs/${{ github.run_id }}) for complete details
          - 🤖 Generated with Continue CLI + Snyk

          _This is an automated security analysis. Please review and address the findings before merging._
          EOF

            gh pr comment ${{ github.event.pull_request.number }} --body-file pr-comment.md

            echo "✅ Mitigation summary posted to PR"
          else
            echo "⚠️ No mitigation suggestions file found"
          fi

      - name: Generate Security Report
        if: always()
        continue-on-error: true
        env:
          SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
        run: |
          echo "📊 Generating security report..."
          {
            echo "# Security Scan Report"
            echo ""
            echo "**Date:** $(date -u +"%Y-%m-%d %H:%M:%S UTC")"
            echo "**Branch:** ${{ github.ref_name }}"
            echo "**Commit:** ${{ github.sha }}"
            echo "**Status:** ${{ steps.security-scan.outputs.scan_status }}"
            echo ""

            echo "## Snyk Code Scan Results"
            if [ -f snyk-code-results.json ]; then
              jq -r '.runs[0].results[] | "- **\(.level | ascii_upcase)**: \(.message.text) in \(.locations[0].physicalLocation.artifactLocation.uri)"' snyk-code-results.json || echo "No code issues found"
            else
              echo "No scan results available"
            fi
            echo ""

            echo "## Snyk Open Source Scan Results"
            if [ -f snyk-oss-results.json ]; then
              jq -r '.vulnerabilities[] | "- **\(.severity | ascii_upcase)**: \(.title) (\(.packageName)@\(.version))"' snyk-oss-results.json || echo "No dependency issues found"
            else
              echo "No scan results available"
            fi
            echo ""

            if [ -f mitigation-suggestions.md ]; then
              echo "## AI-Powered Mitigation Suggestions"
              echo ""
              cat mitigation-suggestions.md
            fi
          } > scan-results.md

          if [ -f scan-results.md ]; then
            echo "✅ Security report generated successfully"
            cat scan-results.md
          else
            echo "⚠️ Warning: scan-results.md was not created"
          fi

      - name: Upload Security Report
        if: always()
        continue-on-error: true
        uses: actions/upload-artifact@v4
        with:
          name: security-scan-results
          path: |
            scan-results.md
            snyk-code-results.json
            snyk-oss-results.json
            mitigation-suggestions.md
          if-no-files-found: warn

      - name: Fail if Security Issues Found
        if: steps.security-scan.outputs.scan_status == 'failed'
        run: |
          echo "❌ Security scan failed - high severity issues found"
          echo "📋 Review the security report artifact for details and mitigation suggestions"
          exit 1
```

**说明：**

**About SNYK_TOKEN**: The workflow uses the SNYK_TOKEN in two ways:
1. **Direct Snyk CLI authentication** - Authenticates the Snyk CLI for running scans
2. **Continue CLI access** - Available as an environment variable when Continue generates AI mitigation suggestions

The `cn` agent automatically uses the SNYK_TOKEN when needed for Snyk MCP operations.

**说明：**

This workflow demonstrates several advanced features:
- **Changed Files Detection**: Only scans files modified in the PR
- **AI Mitigation**: Uses Continue CLI to generate actionable mitigation steps
- **PR Comments**: Automatically posts mitigation suggestions as PR comments
- **Comprehensive Reporting**: Generates detailed security reports with artifacts

## Security Guardrails

Implement automated security policies using Continue's rule system. See the [Rules deep dive](<https://docs.continue.dev/customize/deep-dives/rules>) for authoring tips.

#### Pre-commit Scanning

```bash
"Always run Snyk Code before committing newly
generated code; refuse to proceed if high
issues remain."
```

#### Dependency Safety

```bash
"When adding/updating a dependency, run Snyk Open Source, choose the
lowest-risk upgrade, and re-test."
```

#### Container Hardening

```bash
"Before building containers, scan base images and recommend
security-hardened alternatives."
```

#### IaC Compliance

```bash
"Scan all Terraform changes for compliance
violations before applying infrastructure."
```

**提示：**

Enable the **Secure-at-Inception** rules in your configuration to automatically apply
these guardrails to all code generation and modifications.

## Troubleshooting

### Authentication Issues

```bash
"Check Snyk auth status and current org. If not authenticated,
help me authenticate. Then run a quick Code scan on ./
with severity medium and print one example issue."
```

### Fix Validation

```bash
"Propose minimal diffs only in affected files,
then rerun the same Snyk scan to confirm resolution."
```

### Connection Problems

**检查：**

**Verification Steps:**
- Snyk MCP is installed and configured
- Secure-at-Inception rules are enabled
- Authentication completed successfully
- Project folder has been trusted

## What You've Built

After completing this guide, you have a complete **AI-powered security system** that:

- ✅ Uses natural language — Simple prompts instead of complex CLI commands
- ✅ Fixes automatically — AI suggests and validates security fixes
- ✅ Runs continuously — Automated scanning in CI/CD pipelines
- ✅ Enforces guardrails — Security rules prevent vulnerable code from shipping

#### Continuous AI

Your security workflow now operates at **[Level 2 Continuous
AI](<https://blog.continue.dev/what-is-continuous-ai-a-developers-guide/>)** -
AI handles routine security scanning and remediation with human oversight
through review and approval of fixes.

## Next Steps

1. **Run your first scan** - Try the [SAST](<https://snyk.io/learn/application-security/sast/>) prompt on your current project
2. **Review findings** - Analyze the security report and implement fixes
3. **Set up CI pipeline** - Add the GitHub Actions workflow to your repo
4. **Customize rules** - Add project-specific security policies
5. **Monitor trends** - Track [vulnerability](<https://snyk.io/learn/security-vulnerability/>) reduction over time

## Additional Resources

#### [Snyk Documentation](<https://docs.snyk.io>)

Complete Snyk platform documentation

#### [Continue Documentation](<https://docs.continue.dev>)

Explore Continue documentation and guides

#### [Security Best Practices](<https://snyk.io/learn>)

Learn about secure coding practices

#### [MCP Concepts](<https://docs.snyk.io/integrations/developer-guardrails-for-agentic-workflows>)

Understanding MCP architecture


</details>
