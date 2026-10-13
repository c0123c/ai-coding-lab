# Gemini CLI 集成测试

介绍集成测试环境、运行方式和测试编写，验证 CLI 与工具链的实际交互；主要面向项目开发者。

- 来源：Google／Gemini CLI
- 资料类型：存档文档
- 文档时间：更新：[2026-04-10](<https://geminicli.com/docs/integration-tests/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://geminicli.com/docs/integration-tests/>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Gemini CLI/Gemini CLI 集成测试.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

集成测试 | Gemini CLI
 跳到内容 
未付费层和 Google 一名用户：Gemini CLI 于 6 月 18 日被反重力 
 CLI 取代， 2026。要了解更多信息，请参阅我们的博客文章。
 Gemini CLI 
 主页 
 计划 
 扩展 
 浏览扩展 
 关于扩展 
文档 
 参考 
 资源 
 变更日志搜索 CtrlK 取消反馈 GitHub GitHub 选择主题 DarkLightAuto 
 主页
 计划 
 扩展 
 浏览扩展 
 关于扩展 
 文档 
 开始使用 
 概述 
 快速入门
 安装 
 身份验证 
 CLI 备忘单 
 Gemini 3 在 Gemini CLI 
 使用Gemini CLI 
 文件管理 
 开始使用 Agent Skills 
 管理上下文和内存 
 执行 shell 命令 
 管理会话和历史记录 
 规划任务待办事项 
 使用计划模式进行模型引导 🔬 
 网络搜索和获取 
 设置 MCP 服务器 
 自动执行任务 
 功能 
 扩展
 概述 
 用户指南：安装和管理 
 开发人员指南：构建扩展 
 开发人员指南：最佳实践 
 开发人员指南：发布 
 开发人员指南：参考 
 Agent Skills 
 概述 
 开始使用 Agent Skills 
 创建 Agent Skills 
 使用 Agent Skills 
 开发人员指南：最佳实践 
 自动内存 🔬 
 检查点 
 无头模式 
 Git 工作树 🔬 
 钩子 
 概述 
 参考
 IDE 集成 
 概述 
 开发人员指南：ACP 模式 
 MCP 服务器 
 概述 
 资源工具
 模型路由 
 模型选择 
 模型转向 🔬 
 通知 🔬 
 计划模式 
 子代理 
 远程子代理
 快退 
 沙箱 
 设置 
 遥测 
 令牌缓存 
 配置 
 自定义命令 
 企业配置
 忽略文件 (.geminiignore) 
 模型配置 
 项目上下文 (GEMINI.md) 
 设置 
 系统提示覆盖 
 主题
 可信文件夹 
 开发 
 行为评估 
 贡献指南 
 集成测试 
 问题和 PR 自动化 
 本地开发
 NPM 封装结构 
 参考 
 命令参考 
 配置参考 
 键盘快捷键 
 内存导入处理器 
 策略引擎
 工具参考 
 资源 
 FAQ 
 配额和定价 
 条款和隐私 
 疑难解答 
 卸载
 变更日志 
 发行说明 
 稳定版本 
 预览版本 
 开始使用 
 概述 
 快速入门 
 安装
 认证 
CLI 备忘单 
 Gemini 3 上 Gemini CLI 
 使用 Gemini CLI 
 文件管理 
 开始使用Agent Skills 
 管理上下文和内存 
 执行 shell 命令 
 管理会话和历史记录 
 使用待办事项计划任务 
 使用计划模式进行模型控制 🔬 
 Web 搜索和获取
 设置 MCP 服务器 
 自动化任务 
 功能 
 扩展 
 概述 
 用户指南：安装和管理 
 开发人员指南：构建扩展 
 开发者指南：最佳实践 
 开发者指南：发布 
 开发者指南：参考 
 Agent Skills 
 概述 
 开始使用 Agent Skills 
 创建 Agent Skills 
 使用 Agent Skills 
 开发人员指南：最佳实践 
 自动内存 🔬 
 检查点 
 无头模式
 Git 工作树🔬 
 挂钩 
 概述 
 参考 
 IDE 集成 
 概述 
 开发人员指南： ACP 模式 
 MCP 服务器 
 概述 
 资源工具 
 模型路由 
 模型选择 
 模型转向 🔬 
 通知 🔬 
 计划模式 
 子代理 
 远程子代理 
 倒带 
 沙盒 
 设置 
遥测 
 令牌缓存 
 配置 
 自定义命令 
 企业配置 
 忽略文件 (.geminiignore) 
 模型配置 
 项目上下文(GEMINI.md) 
 设置 
 系统提示覆盖 
 主题 
 受信任文件夹 
 开发 
 行为评估 
 贡献指南 
 集成测试 
 问题和 PR 自动化 
 本地开发 
 NPM 包结构 GitHub 选择主题 DarkLightAuto 
 参考 
 命令参考 
 配置参考 
 键盘快捷键 
 内存导入处理器 
 策略引擎 
 工具参考 GitHub 选择主题DarkLightAuto 
 资源 
 FAQ 
 配额和定价 
 条款和隐私 
 疑难解答 
 卸载GitHub 选择主题 DarkLightAuto 
 发布 
 发行说明 
 稳定版本 
 预览版本 GitHub 选择主题 DarkLightAuto 在此页面
 简介 
 概述 
 构建测试 
 运行测试 
 运行一组特定的测试 
 按名称运行单个测试 
 重新生成模型响应
 清理测试 
 清理脚本 
 运行所有测试 
 沙箱矩阵 
 内存回归测试 
 运行内存测试 
 更新基线
 工作原理 
 性能回归测试 
 运行性能测试 
 更新基线 
 工作原理 
 诊断 
 保留测试输出
 详细输出 
 Linting 和格式化 
 运行 linter 
 目录结构 
持续集成 
本页 
 简介 
 概述 
 构建测试 
 运行测试 
 运行一组特定的测试 
 按名称运行单个测试
 重新生成模型响应 
 清理测试 
 清理脚本 
 运行所有测试 
 沙箱矩阵 
 内存回归测试 
 运行内存测试
 更新基线 
 工作原理 
 性能回归测试 
 运行性能测试 
 更新基线 
 工作原理 
 诊断
 保留测试输出 
 详细输出 
 语法检查和格式化 
 运行 linter 
 目录结构 
 持续集成 
 集成测试 复制为 Markdown 已复制！             
本文档提供了有关此项目中使用的集成测试框架
的信息。

OverviewSection 标题为“概述”

集成测试旨在验证 
Gemini CLI 的端到端功能。他们在受控环境中执行构建的二进制文件，并验证
它在与文件系统交互时的行为是否符合预期。

这些测试位于集成测试目录中，并使用
自定义测试运行器运行。

构建标题为“构建测试”的 testsSection

在运行任何集成测试之前，您需要创建一个要实际测试的发布包
：
终端窗口
npm运行包

对 CLI 源代码进行任何更改后，必须重新运行此命令，
 但在对测试进行更改后则不能。

运行标题为“运行测试”的 testsSection

集成测试不作为默认 npm 运行测试命令的一部分运行。
必须使用 npm run test:integration:all 脚本显式运行它们。

还可以使用以下快捷方式运行集成测试：
终端窗口
npm run test:e2e

运行一组特定的 testsSection，标题为“运行一组特定的测试”

要运行测试文件的子集，您可以使用 
npm run <integration test command> <file_name1> .... 其中 <integration
test command> 是 test:e2e 或 test:integration* 且 <file_name>
 是以下任意一个.test.js 集成测试/目录中的文件。对于
示例，以下命令运行 list_directory.test.js 和 
write_file.test.js:
终端窗口
npm run test:e2e list_directory write_file

由 nameSection 运行一个标题为“按名称运行单个测试”的测试

要按名称运行单个测试，请使用 --test-name-pattern 标志：
终端窗口
npm run test:e2e -- --test-name-pattern“读取文件”

重新生成模型 responsesSection 标题为“重新生成模型响应”

一些集成测试使用伪造的模型响应，随着实现的变化，可能需要不时重新生成
。

要重新生成这些黄金文件，请在运行测试时将 REGENERATE_MODEL_GOLDENS 环境 
 变量设置为“true”，例如：

WARNING：如果在本地运行，您应该查看这些更新的响应，以获取 
Gemini 可能已包含在 
 这些响应中的有关您自己或您的系统的任何信息。
终端窗口 
REGENERATE_MODEL_GOLDENS="true" npm 运行测试：e2e

WARNING：确保在测试结束时运行await rig.cleanup()，
否则黄金文件将不会更新。

消除标题为“消除测试”的 testSection

在添加新的集成测试之前，您应该使用 deflake 脚本或工作流程对其进行至少 5 次 
 测试，以确保其不不稳定。

Deflake scriptSection 标题为“Deflake 脚本”
终端窗口
npm 运行 deflake -- --runs=5 --command="npm 运行测试：e2e -- -- --test-name-pattern '<your-new-test-name>'"

Deflake workflowSection 标题为“Deflake 工作流程”
终端窗口
gh 工作流程运行 deflake.yml --ref <your-branch> -f test_name_pattern="<your-test-name-pattern>"

运行所有标题为“运行所有测试”的 testsSection

要运行整套集成测试，请使用以下命令：
终端窗口
npm run test:integration:all

沙盒 matrixSection 标题为“沙盒矩阵”

all 命令将运行无沙箱、docker 和 podman 的测试。
每种类型都可以使用以下命令运行：
终端窗口
npm run test:integration:sandbox:none
终端窗口
npm run测试：集成：沙箱：docker
终端窗口
npm运行测试：集成：沙箱：podman

内存回归 testsSection 标题为“内存回归测试”

内存回归测试旨在检测关键 
CLI 场景中的堆增长和泄漏。它们位于内存测试目录中。

这些测试与标准集成测试不同，因为它们测量
内存使用情况并将其与提交的基线进行比较。

运行内存 testsSection 标题为“运行内存测试”

内存测试不会作为默认 npm run test 或 
npm run test:e2e 命令的一部分运行。它们每晚在 CI 中运行，但可以手动运行：
终端窗口
npm运行测试：内存

更新 baselinesSection 标题为“更新基线”

如果您有意更改影响内存使用的行为，则可能需要
更新基线。将 UPDATE_MEMORY_BASELINES 环境变量设置为 
true:
终端窗口
UPDATE_MEMORY_BASELINES=true npm run test:memory

这将运行测试、拍摄中值快照并覆盖 
memory-tests/baselines.json. 您应该查看更改并提交 
 更新的基线文件。

它是如何工作的 worksSection 标题为“它是如何工作的”

线束（packages/test-utils 中的 MemoryTestHarness）：

多次强制垃圾收集以减少噪音。

拍摄中值快照来过滤峰值。

与具有 10% 容差的基线进行比较。

可以使用 analyzeSnapshots() 分析 3 快照中的持续泄漏。

性能回归 testsSection 标题为“性能回归测试”

性能回归测试旨在检测关键 CLI 场景中的挂钟时间、CPU 使用情况、
 和事件循环延迟回归。它们位于 
perf-tests 目录中。

这些测试与标准集成测试不同，因为它们测量
性能指标并将其与承诺的基线进行比较。

运行性能testsSection标题为“运行性能测试”

性能测试不作为默认 npm run test 或 
npm run test:e2e 命令的一部分运行。它们每晚在 CI 中运行，但可以手动运行：
终端窗口
npm run test:perf

更新 baselinesSection 标题为“更新基线”

如果您有意更改影响性能的行为，则可能需要
更新基线。将 UPDATE_PERF_BASELINES 环境变量设置为 
true:
终端窗口
UPDATE_PERF_BASELINES=true npm run test:perf

这将多次运行测试（带预热），应用 IQR 离群值 
 过滤，并覆盖 perf-tests/baselines.json. 您应该查看 
 更改并提交更新的基线文件。

它是如何工作的 worksSection 标题为“它是如何工作的”

线束（packages/test-utils 中的 PerfTestHarness）：

使用 Performance.now() 测量挂钟时间。

使用 process.cpuUsage() 测量 CPU 使用情况。

使用 perf_hooks.monitorEventLoopDelay() 监视事件循环延迟。

应用 IQR（四分位数范围）过滤来删除异常值样本。

与具有 15% 容差的基线进行比较。

DiagnosticsSection 标题为“诊断”

集成测试运行程序提供了多个诊断选项，以帮助
跟踪测试失败。

保持测试 outputSection 标题为“保持测试输出”

您可以保留测试运行期间创建的临时文件以供检查。
这对于调试文件系统操作问题很有用。

要保持测试输出，请将 KEEP_OUTPUT 环境变量设置为 true。
终端窗口
KEEP_OUTPUT=true npm run test:integration:sandbox:none

保留输出时，测试运行器将打印测试运行的唯一目录
的路径。

详细 outputSection 标题为“详细输出”

要进行更详细的调试，请将 VERBOSE 环境变量设置为 true。
终端窗口
VERBOSE=true npm run test:integration:sandbox:none

在同一命令中使用 VERBOSE=true 和 KEEP_OUTPUT=true 时，输出 
 将流式传输到控制台，并保存到测试的 
 临时目录中的日志文件中。

详细输出的格式是为了清楚地识别日志的来源：

--- TEST: <log dir>:<test-name> ---...gemini 命令的输出 ...--- END TEST: <log dir>:<test-name> ---

Linting 和 formattingSection 标题为“Linting 和格式化”

为了确保代码质量和一致性，集成测试文件被检查为
主要构建过程的一部分。您还可以手动运行 linter 和 
 自动修复程序。

运行标题为“运行 linter”的 linterSection

要检查 linting 错误，请运行以下命令：
终端窗口
npm run lint

您可以在命令中包含 :fix 标志来自动修复任何可修复的 
linting 错误：
终端窗口
npm run lint:fix

标题为“目录结构”的目录 structureSection

集成测试为 
.integration-tests 目录中的每个测试运行创建一个唯一的目录。在此目录中，为每个测试文件创建一个子目录
，并在其中为每个
单独的测试用例创建一个子目录。

这种结构可以轻松找到特定测试运行、
文件或案例的工件。

.integration-tests/└── <run-id>/ └── <test-file-name>.test.js/ └── <test-case-name>/ ├── 输出.log └── ...其他测试工件...

连续 integrationSection 标题为“持续集成”

为了确保集成测试始终运行，GitHub操作工作流在github/workflows/chained_e2e.yml.中定义
。此工作流自动运行
针对主分支的拉取请求的集成测试，或者当
拉取请求添加到合并队列时。

该工作流程在不同的沙箱环境中运行测试，以确保 
Gemini CLI 在每个沙箱环境中进行测试：

sandbox:none：在没有任何沙箱的情况下运行测试。

sandbox:docker：在 Docker 容器中运行测试。

sandbox:podman：在 Podman 容器中运行测试。
 最后更新时间：4 月 10、2026 
此网站使用来自 Google 的 cookie 来提供和增强其服务质量并分析 
 流量。
 
我明白。
 条款 | 隐私


## 原文对照

<details>
<summary>展开完整原文</summary>

Integration tests | Gemini CLI
  Skip to content   
Unpaid tier and Google One users: Gemini CLI was replaced by Antigravity
    CLI on June 18th, 2026. To learn more, see our blog post.
                    Gemini CLI      
  Home  
  Plans  
   Extensions    
  Browse extensions  
  About Extensions     
  Docs  
  Reference  
  Resources  
  Changelog              Search  CtrlK       Cancel                 Feedback  GitHub  GitHub       Select theme   DarkLightAuto                      
  Home   
  Plans   
    Extensions       
  Browse extensions   
  About Extensions       
    Docs       
    Get started       
  Overview   
  Quickstart   
  Installation   
  Authentication   
  CLI cheatsheet   
  Gemini 3 on Gemini CLI       
    Use Gemini CLI       
  File management   
  Get started with Agent Skills   
  Manage context and memory   
  Execute shell commands   
  Manage sessions and history   
  Plan tasks with todos   
  Use Plan Mode with model steering 🔬  
  Web search and fetch   
  Set up an MCP server   
  Automate tasks       
    Features       
    Extensions       
  Overview   
  User guide: Install and manage   
  Developer guide: Build extensions   
  Developer guide: Best practices   
  Developer guide: Releasing   
  Developer guide: Reference       
    Agent Skills       
  Overview   
  Get started with Agent Skills   
  Creating Agent Skills   
  Using Agent Skills   
  Developer guide: Best practices       
  Auto Memory 🔬  
  Checkpointing   
  Headless mode   
  Git worktrees 🔬  
    Hooks       
  Overview   
  Reference       
    IDE integration       
  Overview   
  Developer guide: ACP mode       
    MCP servers       
  Overview   
  Resource tools       
  Model routing   
  Model selection   
  Model steering 🔬  
  Notifications 🔬  
  Plan mode   
  Subagents   
  Remote subagents   
  Rewind   
  Sandboxing   
  Settings   
  Telemetry   
  Token caching       
    Configuration       
  Custom commands   
  Enterprise configuration   
  Ignore files (.geminiignore)   
  Model configuration   
  Project context (GEMINI.md)   
  Settings   
  System prompt override   
  Themes   
  Trusted folders       
    Development       
  Behavioral evaluations   
  Contribution guide   
  Integration testing   
  Issue and PR automation   
  Local development   
  NPM package structure           
    Reference       
  Command reference   
  Configuration reference   
  Keyboard shortcuts   
  Memory import processor   
  Policy engine   
  Tools reference       
    Resources       
  FAQ   
  Quota and pricing   
  Terms and privacy   
  Troubleshooting   
  Uninstall       
    Changelog       
  Release notes   
  Stable release   
  Preview release                
    Get started       
  Overview   
  Quickstart   
  Installation   
  Authentication   
  CLI cheatsheet   
  Gemini 3 on Gemini CLI       
    Use Gemini CLI       
  File management   
  Get started with Agent Skills   
  Manage context and memory   
  Execute shell commands   
  Manage sessions and history   
  Plan tasks with todos   
  Use Plan Mode with model steering 🔬  
  Web search and fetch   
  Set up an MCP server   
  Automate tasks       
    Features       
    Extensions       
  Overview   
  User guide: Install and manage   
  Developer guide: Build extensions   
  Developer guide: Best practices   
  Developer guide: Releasing   
  Developer guide: Reference       
    Agent Skills       
  Overview   
  Get started with Agent Skills   
  Creating Agent Skills   
  Using Agent Skills   
  Developer guide: Best practices       
  Auto Memory 🔬  
  Checkpointing   
  Headless mode   
  Git worktrees 🔬  
    Hooks       
  Overview   
  Reference       
    IDE integration       
  Overview   
  Developer guide: ACP mode       
    MCP servers       
  Overview   
  Resource tools       
  Model routing   
  Model selection   
  Model steering 🔬  
  Notifications 🔬  
  Plan mode   
  Subagents   
  Remote subagents   
  Rewind   
  Sandboxing   
  Settings   
  Telemetry   
  Token caching       
    Configuration       
  Custom commands   
  Enterprise configuration   
  Ignore files (.geminiignore)   
  Model configuration   
  Project context (GEMINI.md)   
  Settings   
  System prompt override   
  Themes   
  Trusted folders       
    Development       
  Behavioral evaluations   
  Contribution guide   
  Integration testing   
  Issue and PR automation   
  Local development   
  NPM package structure                 GitHub     Select theme   DarkLightAuto                
    Reference       
  Command reference   
  Configuration reference   
  Keyboard shortcuts   
  Memory import processor   
  Policy engine   
  Tools reference                 GitHub     Select theme   DarkLightAuto                
    Resources       
  FAQ   
  Quota and pricing   
  Terms and privacy   
  Troubleshooting   
  Uninstall                 GitHub     Select theme   DarkLightAuto                
    Releases       
  Release notes   
  Stable release   
  Preview release                 GitHub     Select theme   DarkLightAuto                         On this page 
  Introduction   
  Overview   
  Building the tests   
  Running the tests   
  Running a specific set of tests   
  Running a single test by name   
  Regenerating model responses   
  Deflaking a test   
  Deflake script   
  Running all tests   
  Sandbox matrix      
  Memory regression tests   
  Running memory tests   
  Updating baselines   
  How it works      
  Performance regression tests   
  Running performance tests   
  Updating baselines   
  How it works      
  Diagnostics   
  Keeping test output   
  Verbose output      
  Linting and formatting   
  Running the linter      
  Directory structure   
  Continuous integration     
On this page 
  Introduction   
  Overview   
  Building the tests   
  Running the tests   
  Running a specific set of tests   
  Running a single test by name   
  Regenerating model responses   
  Deflaking a test   
  Deflake script   
  Running all tests   
  Sandbox matrix      
  Memory regression tests   
  Running memory tests   
  Updating baselines   
  How it works      
  Performance regression tests   
  Running performance tests   
  Updating baselines   
  How it works      
  Diagnostics   
  Keeping test output   
  Verbose output      
  Linting and formatting   
  Running the linter      
  Directory structure   
  Continuous integration                 
 Integration tests     Copy as Markdown Copied!             
This document provides information about the integration testing framework used
in this project.

OverviewSection titled “Overview”

The integration tests are designed to validate the end-to-end functionality of
Gemini CLI. They execute the built binary in a controlled environment and verify
that it behaves as expected when interacting with the file system.

These tests are located in the integration-tests directory and are run using a
custom test runner.

Building the testsSection titled “Building the tests”

Prior to running any integration tests, you need to create a release bundle that
you want to actually test:
Terminal window
npm run bundle

You must re-run this command after making any changes to the CLI source code,
but not after making changes to tests.

Running the testsSection titled “Running the tests”

The integration tests are not run as part of the default npm run test command.
They must be run explicitly using the npm run test:integration:all script.

The integration tests can also be run using the following shortcut:
Terminal window
npm run test:e2e

Running a specific set of testsSection titled “Running a specific set of tests”

To run a subset of test files, you can use
npm run <integration test command> <file_name1> .... where <integration
test command> is either test:e2e or test:integration* and <file_name>
is any of the .test.js files in the integration-tests/ directory. For
example, the following command runs list_directory.test.js and
write_file.test.js:
Terminal window
npm run test:e2e list_directory write_file

Running a single test by nameSection titled “Running a single test by name”

To run a single test by its name, use the --test-name-pattern flag:
Terminal window
npm run test:e2e -- --test-name-pattern "reads a file"

Regenerating model responsesSection titled “Regenerating model responses”

Some integration tests use faked out model responses, which may need to be
regenerated from time to time as the implementations change.

To regenerate these golden files, set the REGENERATE_MODEL_GOLDENS environment
variable to “true” when running the tests, for example:

WARNING: If running locally you should review these updated responses for
any information about yourself or your system that gemini may have included in
these responses.
Terminal window
REGENERATE_MODEL_GOLDENS="true" npm run test:e2e

WARNING: Make sure you run await rig.cleanup() at the end of your test,
else the golden files will not be updated.

Deflaking a testSection titled “Deflaking a test”

Before adding a new integration test, you should test it at least 5 times
with the deflake script or workflow to make sure that it is not flaky.

Deflake scriptSection titled “Deflake script”
Terminal window
npm run deflake -- --runs=5 --command="npm run test:e2e -- -- --test-name-pattern '<your-new-test-name>'"

Deflake workflowSection titled “Deflake workflow”
Terminal window
gh workflow run deflake.yml --ref <your-branch> -f test_name_pattern="<your-test-name-pattern>"

Running all testsSection titled “Running all tests”

To run the entire suite of integration tests, use the following command:
Terminal window
npm run test:integration:all

Sandbox matrixSection titled “Sandbox matrix”

The all command will run tests for no sandboxing, docker and podman.
Each individual type can be run using the following commands:
Terminal window
npm run test:integration:sandbox:none
Terminal window
npm run test:integration:sandbox:docker
Terminal window
npm run test:integration:sandbox:podman

Memory regression testsSection titled “Memory regression tests”

Memory regression tests are designed to detect heap growth and leaks across key
CLI scenarios. They are located in the memory-tests directory.

These tests are distinct from standard integration tests because they measure
memory usage and compare it against committed baselines.

Running memory testsSection titled “Running memory tests”

Memory tests are not run as part of the default npm run test or
npm run test:e2e commands. They are run nightly in CI but can be run manually:
Terminal window
npm run test:memory

Updating baselinesSection titled “Updating baselines”

If you intentionally change behavior that affects memory usage, you may need to
update the baselines. Set the UPDATE_MEMORY_BASELINES environment variable to
true:
Terminal window
UPDATE_MEMORY_BASELINES=true npm run test:memory

This will run the tests, take median snapshots, and overwrite
memory-tests/baselines.json. You should review the changes and commit the
updated baseline file.

How it worksSection titled “How it works”

The harness (MemoryTestHarness in packages/test-utils):

Forces garbage collection multiple times to reduce noise.

Takes median snapshots to filter spikes.

Compares against baselines with a 10% tolerance.

Can analyze sustained leaks across 3 snapshots using analyzeSnapshots().

Performance regression testsSection titled “Performance regression tests”

Performance regression tests are designed to detect wall-clock time, CPU usage,
and event loop delay regressions across key CLI scenarios. They are located in
the perf-tests directory.

These tests are distinct from standard integration tests because they measure
performance metrics and compare it against committed baselines.

Running performance testsSection titled “Running performance tests”

Performance tests are not run as part of the default npm run test or
npm run test:e2e commands. They are run nightly in CI but can be run manually:
Terminal window
npm run test:perf

Updating baselinesSection titled “Updating baselines”

If you intentionally change behavior that affects performance, you may need to
update the baselines. Set the UPDATE_PERF_BASELINES environment variable to
true:
Terminal window
UPDATE_PERF_BASELINES=true npm run test:perf

This will run the tests multiple times (with warmup), apply IQR outlier
filtering, and overwrite perf-tests/baselines.json. You should review the
changes and commit the updated baseline file.

How it worksSection titled “How it works”

The harness (PerfTestHarness in packages/test-utils):

Measures wall-clock time using performance.now().

Measures CPU usage using process.cpuUsage().

Monitors event loop delay using perf_hooks.monitorEventLoopDelay().

Applies IQR (Interquartile Range) filtering to remove outlier samples.

Compares against baselines with a 15% tolerance.

DiagnosticsSection titled “Diagnostics”

The integration test runner provides several options for diagnostics to help
track down test failures.

Keeping test outputSection titled “Keeping test output”

You can preserve the temporary files created during a test run for inspection.
This is useful for debugging issues with file system operations.

To keep the test output set the KEEP_OUTPUT environment variable to true.
Terminal window
KEEP_OUTPUT=true npm run test:integration:sandbox:none

When output is kept, the test runner will print the path to the unique directory
for the test run.

Verbose outputSection titled “Verbose output”

For more detailed debugging, set the VERBOSE environment variable to true.
Terminal window
VERBOSE=true npm run test:integration:sandbox:none

When using VERBOSE=true and KEEP_OUTPUT=true in the same command, the output
is streamed to the console and also saved to a log file within the test’s
temporary directory.

The verbose output is formatted to clearly identify the source of the logs:

--- TEST: <log dir>:<test-name> ---... output from the gemini command ...--- END TEST: <log dir>:<test-name> ---

Linting and formattingSection titled “Linting and formatting”

To ensure code quality and consistency, the integration test files are linted as
part of the main build process. You can also manually run the linter and
auto-fixer.

Running the linterSection titled “Running the linter”

To check for linting errors, run the following command:
Terminal window
npm run lint

You can include the :fix flag in the command to automatically fix any fixable
linting errors:
Terminal window
npm run lint:fix

Directory structureSection titled “Directory structure”

The integration tests create a unique directory for each test run inside the
.integration-tests directory. Within this directory, a subdirectory is created
for each test file, and within that, a subdirectory is created for each
individual test case.

This structure makes it easy to locate the artifacts for a specific test run,
file, or case.

.integration-tests/└── <run-id>/    └── <test-file-name>.test.js/        └── <test-case-name>/            ├── output.log            └── ...other test artifacts...

Continuous integrationSection titled “Continuous integration”

To ensure the integration tests are always run, a GitHub Actions workflow is
defined in .github/workflows/chained_e2e.yml. This workflow automatically runs
the integrations tests for pull requests against the main branch, or when a
pull request is added to a merge queue.

The workflow runs the tests in different sandboxing environments to ensure
Gemini CLI is tested across each:

sandbox:none: Runs the tests without any sandboxing.

sandbox:docker: Runs the tests in a Docker container.

sandbox:podman: Runs the tests in a Podman container.
   Last updated: Apr 10, 2026                
This website uses cookies from Google to deliver and enhance the quality of its services and to analyze
        traffic.
 
I understand.
                Terms | Privacy


</details>
