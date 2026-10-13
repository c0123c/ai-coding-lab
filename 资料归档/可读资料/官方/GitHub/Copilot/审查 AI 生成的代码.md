# 审查 AI 生成的代码

从功能、需求意图、代码质量、依赖和 AI 常见失误等方面介绍检查方法，并提供可用于辅助审查的提示示例。

- 来源：GitHub／Copilot
- 资料类型：存档文档
- 文档时间：官方文件提交（截至抓取）：[2026-10-05](<https://github.com/github/docs/commit/4780141748da1eac03fa8ac65c96e2bb550bec96>)；发表/更新：官方未公开；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://docs.github.com/en/copilot/tutorials/review-ai-generated-code>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/审查 AI 生成的代码.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 查看 AI- 生成的代码

了解验证和验证 AI- 生成的代码的技术，以及 Copilot 聊天如何提供帮助。

审查由 AI 工具（例如 GitHub Copilot、ChatGPT 或其他 agents）生成的代码正在成为现代开发人员工作流程的重要组成部分。本指南提供实用技术，强调人工监督和测试的重要性，并包含示例提示来展示 AI 如何协助审核过程。

对于遗留代码库和更大的拉取请求，特别是，彻底的审查过程至关重要。将人类专业知识与自动化工具相结合可以确保 AI- 生成的代码符合质量标准、与项目目标保持一致并遵循最佳实践。

借助 Copilot，您可以简化审核流程并增强识别 AI- 生成的代码中潜在问题的能力。

## 1。从功能检查开始

始终首先运行自动化测试和静态分析工具。

* 确保代码可以编译并且所有测试都通过。检查是否有任何新的警告或错误。
* 使用 [CodeQL](<https://codeql.github.com/>) 和 [Dependabot](<https://docs.github.com/en/code-security/concepts/supply-chain-security/dependabot-version-updates>) 等工具来捕获漏洞和依赖性问题。
* 使用 GitHub 代码质量来捕获可靠性和可维护性问题并查看代码覆盖率。 [GitHub 代码质量](<https://docs.github.com/en/code-security/concepts/code-quality/code-quality>)。
* 有关使用 Copilot 验证代码的示例，请参阅[生成单元测试](<https://docs.github.com/en/copilot/tutorials/copilot-cookbook/testing-code/generate-unit-tests>)和[为网页创建端到端测试](<https://docs.github.com/en/copilot/tutorials/copilot-cookbook/testing-code/create-end-to-end-tests>)。

### 提示示例

* `What functional tests to validate this code change do not exist or are missing?`
* `What possible vulnerabilities or security issues could this code introduce?`

## 2。验证上下文和意图

检查 AI- 生成的代码是否适合您项目的目的和架构。

* 查看 AI 输出是否与您的要求和设计模式保持一致。
* 问问自己：“这段代码是否解决了正确的问题？它遵循我们的约定吗？”
* 使用您的 README、文档和最近的拉取请求作为 AI 上下文的起点。告诉 AI 哪些来源值得信任，哪些来源不值得使用，并为其提供良好的示例。
* 尝试[综合研究](<https://docs.github.com/en/copilot/tutorials/copilot-cookbook/communicate-effectively/synthesizing-research>)，看看Copilot如何使用文档和研究来指导代码生成。
* 当要求 AI 执行研究和规划任务时，请考虑将 AI 输出提炼为结构化工件，然后成为未来 AI 任务（例如代码生成）的上下文。

### 提示示例

* `How does this refactored code section align with our project architecture?`
* `What similar features or established design patterns did you identify and model your code after?`
* `When examining this code, what assumptions about business logic, design preferences, or user behaviors have been made?`
* `What are the potential issues or limitations with this approach?`

## 3。评估代码质量

除了 GitHub 代码质量等自动化工具之外，人类标准仍然很重要。

* 寻找可读性、可维护性和清晰的命名。
* 避免接受难以理解的代码，或者重构比重写需要更长的时间的代码。
* 更喜欢有详细文档并包含清晰注释的代码。
* 检查[提高代码可读性和可维护性](<https://docs.github.com/en/copilot/tutorials/copilot-cookbook/refactor-code/improve-code-readability>)以获取有关检查和重构生成代码的提示和技巧。

### 提示示例

* `What are some readability and maintainability issues in this code?`
* `How can this code be improved for clarity and simplicity? Suggest an alternative structure or variable names to enhance clarity.`
* `How could this code be broken down into smaller, testable units?`

## 4。仔细检查依赖关系

对新的软件包和库保持警惕。

* 检查建议的依赖项是否存在并且是否得到积极维护。考虑新依赖项的起源和贡献者，以确保它们来自信誉良好的、非竞争的来源。
* 审查许可。避免引入与项目许可证不兼容的代码或依赖项（例如，MIT 许可项目中的 AGPL-3.0，或没有声明许可证的依赖项）。
* 提防幻觉或可疑的软件包（例如实际上并不存在的软件包）或slopsquatting（使用虚假或恶意软件包对LLM 进行理论上的攻击）。
* [创建模板](<https://docs.github.com/en/copilot/tutorials/copilot-cookbook/communicate-effectively/creating-templates>)演示了Copilot如何帮助进行依赖项设置，但是最好始终亲自验证建议的包。
* 使用 [GitHub Copilot 在 IDE 中](<https://docs.github.com/en/copilot/concepts/copilot-surfaces/copilot-in-ides#references-to-matching-public-code>) 检查与公开可用代码的匹配。

### 提示示例

* `Analyze the attached package.json file and list all dependencies with their respective licenses.`
* `Are each of the dependencies listed in this package.json file actively maintained (that is, not archived and have recent maintainer activity)?`

## 5。现货【AI-】具体陷阱

AI 工具可能会犯独特的错误。

* 寻找幻觉的 API、忽略的约束或不正确的逻辑。
* 留意被删除或跳过的测试，而不是修复的测试。
* 对“看起来正确”但与您的意图不符的代码持怀疑态度。
* 请参阅[调试无效JSON](<https://docs.github.com/en/copilot/tutorials/copilot-cookbook/debug-errors/debug-invalid-json>)作为捕获细微错误并使用Copilot进行调试的示例。

### 提示示例

* `What was the reasoning behind the code change to delete the failing test? Suggest some alternatives that would fix the test instead of deleting it.`
* `What potential complexities, edge cases, or scenarios are there that this code might not handle correctly?`
* `What specific technical questions does this code raise that require human judgment or domain expertise to evaluate properly?`

## 6。使用协作评审

结对和团队投入有助于发现微妙的问题。

* 要求队友审查复杂或敏感的变更。
* 使用清单确保涵盖所有关键审查点（功能、安全性、可维护性）。
* 分享成功的提示和模式，供整个团队使用 AI。
* 请参阅 [有效沟通 ](<https://docs.github.com/en/copilot/tutorials/copilot-cookbook/communicate-effectively>)，了解如何与 Copilot 协作并记录调查结果的示例。

## 7。自动化您能做的事情

让工具来处理重复性工作。

* 设置 CI 检查样式、linting、安全性、代码质量和代码覆盖率。
* 使用 Dependabot 进行依赖项更新和警报。
* 应用 CodeQL 或类似扫描仪进行静态分析。
* [查找与 GitHub Copilot 建议匹配的公共代码 ](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/copilot-for-common-tasks/find-matching-code>) 展示了 Copilot 如何帮助跟踪代码模式并自动执行搜索任务。
* 考虑具有推理功能的 AI agents 是否可以帮助您实现部分审核流程的自动化。例如，构建一个自我审查 agent，根据您的标准评估草稿拉取请求，在 * 请求人工审查之前检查准确性、适当的基调和业务逻辑 *。

## 8。不断改进您的工作流程

采用新的 AI 工具和技术可以使您的工作流程更加高效。

* 记录审查 AI- 生成的代码的最佳实践。
* 鼓励团队中的“AI冠军”分享技巧和工作流程。
* 更新您的入职和贡献指南，以包含您的 AI 审核技术和资源。使用存储库中的 `CONTRIBUTING.md` 文件来记录您对 AI- 生成的源代码和内容的期望，请参阅 [ 存储库贡献者设置指南 ](<https://docs.github.com/en/communities/setting-up-your-project-for-healthy-contributions/setting-guidelines-for-repository-contributors>)。
* 参考 [GitHub Copilot 食谱 ](<https://docs.github.com/en/copilot/tutorials/copilot-cookbook>) 获取灵感并在团队文档中分享有用的食谱。

## 进一步阅读

* [现代代码审查中的人类监督](<https://resources.github.com/enterprise/human-oversight-modern-code-review/>)，位于GitHub资源中


## 原文对照

<details>
<summary>展开完整原文</summary>

# Review AI-generated code

Learn techniques to verify and validate AI-generated code, and how Copilot Chat can help.

Reviewing code generated by AI tools like GitHub Copilot, ChatGPT, or other agents is becoming an essential part of the modern developer workflow. This guide provides practical techniques, emphasizes the importance of human oversight and testing, and includes example prompts to showcase how AI can assist in the review process.

For both legacy codebases and larger pull requests in particular, a thorough review process is critical. Combining human expertise with automated tools can ensure that AI-generated code meets quality standards, aligns with project goals, and adheres to best practices.

With Copilot, you can streamline your review process and enhance your ability to identify potential issues in AI-generated code.

## 1. Start with functional checks

Always run automated tests and static analysis tools first.

* Make sure the code compiles and all tests pass. Check for any new warnings or errors.
* Use tools like [CodeQL](<https://codeql.github.com/>) and [Dependabot](<https://docs.github.com/en/code-security/concepts/supply-chain-security/dependabot-version-updates>) to catch vulnerabilities and dependency issues.
* Use GitHub Code Quality to catch reliability and maintainability issues and see code coverage. [GitHub Code Quality](<https://docs.github.com/en/code-security/concepts/code-quality/code-quality>).
* See [Generating unit tests](<https://docs.github.com/en/copilot/tutorials/copilot-cookbook/testing-code/generate-unit-tests>) and [Creating end-to-end tests for a webpage](<https://docs.github.com/en/copilot/tutorials/copilot-cookbook/testing-code/create-end-to-end-tests>) for examples of verifying code with Copilot.

### Example prompts

* `What functional tests to validate this code change do not exist or are missing?`
* `What possible vulnerabilities or security issues could this code introduce?`

## 2. Verify context and intent

Check that the AI-generated code fits the purpose and architecture of your project.

* Review the AI output for alignment with your requirements and design patterns.
* Ask yourself: “Does this code solve the right problem? Does it follow our conventions?”
* Use your README, docs, and recent pull requests as a starting point for context for AI. Tell AI what sources to trust, what not to use, and give it good examples to work with.
* Try [Synthesizing research](<https://docs.github.com/en/copilot/tutorials/copilot-cookbook/communicate-effectively/synthesizing-research>) to see how Copilot uses documentation and research to inform code generation.
* When asking AI to perform research and planning tasks, consider distilling the AI output into structured artifacts to then become context for future AI tasks such as code generation.

### Example prompts

* `How does this refactored code section align with our project architecture?`
* `What similar features or established design patterns did you identify and model your code after?`
* `When examining this code, what assumptions about business logic, design preferences, or user behaviors have been made?`
* `What are the potential issues or limitations with this approach?`

## 3. Assess code quality

In addition to automated tools like GitHub Code Quality, human standards still matter.

* Look for readability, maintainability, and clear naming.
* Avoid accepting code that is hard to follow or would take longer to refactor than to rewrite.
* Prefer code that is well-documented and includes clear comments.
* Check [Improving code readability and maintainability](<https://docs.github.com/en/copilot/tutorials/copilot-cookbook/refactor-code/improve-code-readability>) for prompts and tips on reviewing and refactoring generated code.

### Example prompts

* `What are some readability and maintainability issues in this code?`
* `How can this code be improved for clarity and simplicity? Suggest an alternative structure or variable names to enhance clarity.`
* `How could this code be broken down into smaller, testable units?`

## 4. Scrutinize dependencies

Be vigilant with new packages and libraries.

* Check if suggested dependencies exist and are actively maintained. Consider the origins and contributors of new dependencies to ensure they come from reputable, non-competing sources.
* Review licensing. Avoid introducing code or dependencies that are incompatible with your project’s license (for example, AGPL-3.0 in a MIT licensed project, or dependencies with no declared license).
* Watch out for hallucinated or suspicious packages (such as packages that don't actually exist), or slopsquatting (a theoretical attack on LLMs using fake or malicious packages).
* [Creating templates](<https://docs.github.com/en/copilot/tutorials/copilot-cookbook/communicate-effectively/creating-templates>) demonstrates how Copilot can assist with dependency setup, however it is good practice to always verify suggested packages yourself.
* Use [GitHub Copilot in IDEs](<https://docs.github.com/en/copilot/concepts/copilot-surfaces/copilot-in-ides#references-to-matching-public-code>) to review matches with publicly available code.

### Example prompts

* `Analyze the attached package.json file and list all dependencies with their respective licenses.`
* `Are each of the dependencies listed in this package.json file actively maintained (that is, not archived and have recent maintainer activity)?`

## 5. Spot AI-specific pitfalls

AI tools can make unique mistakes.

* Look for hallucinated APIs, ignored constraints, or incorrect logic.
* Watch for tests that are deleted or skipped, instead of fixed.
* Be skeptical of code that “looks right” but doesn’t match your intent.
* See [Debugging invalid JSON](<https://docs.github.com/en/copilot/tutorials/copilot-cookbook/debug-errors/debug-invalid-json>) as an example of catching subtle errors and debugging with Copilot.

### Example prompts

* `What was the reasoning behind the code change to delete the failing test? Suggest some alternatives that would fix the test instead of deleting it.`
* `What potential complexities, edge cases, or scenarios are there that this code might not handle correctly?`
* `What specific technical questions does this code raise that require human judgment or domain expertise to evaluate properly?`

## 6. Use collaborative reviews

Pairing and team input helps catch subtle issues.

* Ask teammates to review complex or sensitive changes.
* Use checklists to ensure all key review points (functionality, security, maintainability) are covered.
* Share successful prompts and patterns for AI use across your team.
* See [Communicate effectively](<https://docs.github.com/en/copilot/tutorials/copilot-cookbook/communicate-effectively>) for examples of how to work with Copilot collaboratively and document findings.

## 7. Automate what you can

Let tools handle the repetitive work.

* Set up CI checks for style, linting, security, code quality and code coverage.
* Use Dependabot for dependency updates and alerts.
* Apply CodeQL or similar scanners for static analysis.
* [Finding public code that matches GitHub Copilot suggestions](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/copilot-for-common-tasks/find-matching-code>) shows how Copilot can help track down code patterns and automate search tasks.
* Consider if AI agents with reasoning capabilities can assist in automating parts of your review process. For example, build a self-reviewing agent that evaluates draft pull requests against your standards, checking for accuracy, appropriate tone, and business logic *before* requesting human review.

## 8. Keep improving your workflow

Embracing new AI tools and techniques can make your workflow even more effective.

* Document your best practices for reviewing AI-generated code.
* Encourage “AI champions” on your team to share tips and workflows.
* Update your onboarding and contribution guides to include your AI review techniques and resources. Use a `CONTRIBUTING.md` file in your repository to document your expectations for AI-generated source code and content, see [Setting guidelines for repository contributors](<https://docs.github.com/en/communities/setting-up-your-project-for-healthy-contributions/setting-guidelines-for-repository-contributors>).
* Reference [GitHub Copilot Cookbook](<https://docs.github.com/en/copilot/tutorials/copilot-cookbook>) for inspiration and share useful recipes in your team docs.

## Further reading

* [Human Oversight in Modern Code Review](<https://resources.github.com/enterprise/human-oversight-modern-code-review/>) in GitHub Resources


</details>
