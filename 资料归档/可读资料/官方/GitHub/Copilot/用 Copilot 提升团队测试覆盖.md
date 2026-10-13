# 用 Copilot 提升团队测试覆盖

讨论生成测试、补充边界用例、理解新增代码及辅助 CI/CD，并给出开发习惯和衡量指标，供团队组织测试工作时参考。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-10-05](<https://github.com/github/docs/commit/4780141748da1eac03fa8ac65c96e2bb550bec96>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/tutorials/roll-out-at-scale/drive-downstream-impact/increase-test-coverage.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/用 Copilot 提升团队测试覆盖.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Increasing test coverage in your company with GitHub Copilot
shortTitle: Increase test coverage
intro: 'Understand features, enable developers, and measure Copilot''s impact.'
versions:
  feature: copilot
product: '{% data variables.copilot.copilot_for_business %} or {% data variables.copilot.copilot_enterprise %}'
allowTitleToDifferFromFilename: true
redirect_from:
  - /copilot/tutorials/rolling-out-github-copilot-at-scale/driving-downstream-impact/increase-test-coverage
  - /copilot/tutorials/rolling-out-github-copilot-at-scale/drive-downstream-impact/increase-test-coverage
contentType: tutorials
category:
  - Roll Copilot out at scale
  - Manage Copilot for a team
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

{% data reusables.copilot.essp-intro %}

## 1。识别成功的障碍

{% data reusables.copilot.identify-barriers-intro %}

由于单元测试覆盖率较低，许多软件团队在维护高质量代码方面面临着持续的挑战。在快节奏的开发环境中，编写测试通常被视为耗时或不必要的，特别是当团队面临快速交付功能的压力时。

因此，可以在开发生命周期的后期（通常是在暂存或生产环境中）发现关键错误。

这会导致一系列负面结果：

* **错误率较高** 和客户报告的问题
* **部署后修复错误的成本增加**
* **开发人员对其代码稳定性的信心降低**
* **由于反应式调试和修补而导致发布周期变慢**

在遗留系统中，由于复杂的依赖关系或缺乏文档记录的代码，测试覆盖率可能更难解决。开发人员可能不熟悉旧的代码库或一般测试框架，这进一步加剧了问题。

提高测试覆盖率是公认的最佳实践，但它需要许多团队难以分配的时间和专业知识。

## 2。评估您的选择

{% data reusables.copilot.evaluate-options-intro %}

<a href="https://github.com/github-copilot/purchase?ref_product=copilot&ref_type=trial&ref_style=button" target="_blank" class="btn btn-primary mt-3 mr-3 no-underline"><span>注册{% data variables.product.prodname_copilot_short %}</span>{% octicon "link-external" height:16 aria-label="link-external" %}</a>

### {% data variables.product.prodname_copilot_short %} 如何提供帮助

{% data variables.product.prodname_copilot %}可以显着加速和简化编写单元测试的过程。通过了解周围的代码和上下文，{% data variables.product.prodname_copilot_short %}可以建议与被测试代码的结构和逻辑相匹配的测试函数。

{% data variables.product.prodname_copilot_short %} 的功能在多种场景下都很有用：

* 当开发人员编写新功能时，{% data variables.product.prodname_copilot_short %}可以自动建议相应的内联测试用例。
* 重构遗留代码时，{% data variables.product.prodname_copilot_short %}可以帮助生成测试脚手架以防止回归。
* 对于未经测试的模块，即使测试覆盖率缺失或不一致，开发人员也可以提示 {% data variables.product.prodname_copilot_short %} 生成有意义的测试用例。

通过使单元测试变得更容易、更快、更少的手动，{% data variables.product.prodname_copilot_short %} 减少了可能导致测试覆盖率差距的摩擦，并帮助团队采用质量第一的思维方式。

#### 使用案例

* **内联测试生成**：开发人员可以要求{% data variables.product.prodname_copilot_short %}为特定功能或模块生成测试，而无需切换上下文。
* **更好的边缘情况覆盖**：通过针对边缘场景（例如空输入、空列表或无效状态）提示{% data variables.product.prodname_copilot_short %}，开发人员可以快速覆盖更多逻辑分支。
* **加速入门**：新团队成员可以使用{% data variables.product.prodname_copilot_short %}通过查看生成的测试用例来了解功能的预期行为。
* **CI/CD** 的协助：{% data variables.product.prodname_copilot_short %} 可以建议如何将测试集成到构建管道中，确保覆盖率改进直接支持质量门。

### 文化考虑

{% data reusables.copilot.cultural-factors-intro %}

* 团队可能 ** 依赖手动测试 ** 或自动化测试不足。这可能是由于自动化的资源限制或缺乏现代测试工具的经验造成的。
* 团队可能 ** 等待太长时间才能发布 **，一次部署大量代码，这使得错误和回归更难以检测。这可能是由于 CI/CD 管道成熟度不足、严格的合规性要求或 PR 与部署之间的审核周期较长造成的。

## 3。实施变革

{% data reusables.copilot.implement-changes-intro %}

* [生成内联测试](<#内联生成测试>)
* [覆盖边缘情况](<#覆盖边缘情况>)
* [了解新代码](<#理解新代码>)
* [获取有关 CI/CD](<#获取有关-cicd-的帮助>) 的帮助
* [开发者最佳实践](<#开发人员的最佳实践>)
* [开发者资源](<#开发人员资源>)
* [推荐功能](<#推荐功能>)

### 内联生成测试

1. 在【VS Code】中选择需要测试的功能，提示【{% data variables.product.prodname_copilot_short %}】：【`Generate a unit test for this code.`】
1. {% data variables.product.prodname_copilot_short %} 根据语言和结构生成内联测试或在单独的测试文件中。
1. 审查、完善并接受建议。

### 覆盖边缘情况

1. 写完测试后，问{% data variables.product.prodname_copilot_short %}：`What are some edge cases I should test for this function?`

或：`Write test cases for when the input is null or empty.`

1. {% data variables.product.prodname_copilot_short %} 建议附加测试用例来覆盖边界条件。
1. 检查测试并将它们合并到您的测试套件中。

### 理解新代码

1. 选择旧功能并询问{% data variables.product.prodname_copilot_short %}：`Explain what this function does and generate a test to validate it.`
1. {% data variables.product.prodname_copilot_short %} 解释了该函数的用途并建议了相应的测试用例。
1. 查看测试用例以了解预期行为并快速构建上下文。

### 获取有关 CI/CD 的帮助

1. 检查您的测试用例并将其提交到代码库。
1. 询问{% data variables.product.prodname_copilot_short %}：`Where should I place this test if I want it to run in CI?`
1. 根据代码库的结构，{% data variables.product.prodname_copilot_short %}将建议放置测试文件的位置以及如何更新管道配置。

### 开发人员的最佳实践

开发者**应该**：

* 与 {% data variables.product.prodname_copilot_short %} 聊天时使用描述性评论或提示。例如：`Generate unit tests for a function that calculates discounts based on user type and purchase amount.`
* 使用 {% data variables.product.prodname_copilot_short %} 探索逻辑覆盖率。例如：`What branches or conditions does this function have that should be tested?`
* 探索不同的提示技术并比较不同 AI 模型的结果。

开发者**不应该**：

* 接受生成的测试而不检查逻辑。确保测试反映实际需求并处理实际的输入和输出。
* 跳过断言边缘行为。如果您只测试“快乐路径”，您就有可能错过回归。
* 依靠{% data variables.product.prodname_copilot_short %}猜测未记录的业务规则。始终通过提示或评论提供上下文。
* 将 {% data variables.product.prodname_copilot_short %} 视为人工代码审查的替代品。 {% data variables.product.prodname_copilot_short %} 加速了该过程，但您仍然需要应用工程判断。

### 开发人员资源

* [AUTOTITLE](<https://github.com/copilot/tutorials/write-tests>)
* [如何使用{% data variables.product.prodname_copilot %}生成单元测试：提示和示例](<https://github.blog/ai-and-ml/github-copilot/how-to-generate-unit-tests-with-github-copilot-tips-and-examples/>)
* [{% data variables.product.prodname_copilot %} 是 Visual Studio 中的 EVERYWHERE ](<https://learn.microsoft.com/en-us/shows/github-copilot-for-visual-studio/github-copilot-is-everywhere-in-visual-studio-miniseries>)（包含测试部分的视频内容）
* [AUTOTITLE](<https://github.com/copilot/concepts/prompting/prompt-engineering>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/change-the-chat-model>)

### 推荐功能

* [{% data variables.copilot.copilot_chat_dotcom_short %}](<https://github.com/copilot/how-tos/copilot-on-github/chat-with-copilot/chat-in-github>)
* [{% data variables.product.prodname_copilot_short %} 内联建议](<https://github.com/copilot/how-tos/copilot-in-your-ide/copilot-for-common-tasks/get-ide-code-suggestions>)
* [{% data variables.copilot.copilot_chat_short %} 在 IDE](<https://github.com/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/chat-in-ide>) 中
* [{% data variables.copilot.copilot_cloud_agent %}](<https://github.com/copilot/concepts/copilot-surfaces/copilot-on-github>)

## 值得关注的指标

{% data reusables.copilot.measure-changes-intro %}

* **测试覆盖范围**：跟踪采用{% data variables.product.prodname_copilot_short %}后线路和分支覆盖范围的增加。如果可能，请查看来自 CI 管道的测试覆盖率报告。
* **部署后的错误率**：生产环境中应该报告更少的错误。
* **开发人员信心**：使用调查或回顾来评估开发人员对交付新代码的信心。
* **编写测试的时间**：衡量创建单元测试所用时间的减少。


## 原文对照

<details>
<summary>展开完整原文</summary>

{% data reusables.copilot.essp-intro %}

## 1. Identify barriers to success

{% data reusables.copilot.identify-barriers-intro %}

Many software teams face persistent challenges in maintaining high-quality code due to low unit test coverage. In fast-paced development environments, writing tests is often seen as time-consuming or non-essential, especially when teams are under pressure to deliver features quickly.

As a result, critical bugs can be discovered late in the development lifecycle, often in staging or production environments.

This leads to a chain of negative outcomes:

* **Higher bug rates** and customer-reported issues
* **Increased cost** of fixing bugs after deployment
* **Reduced developer confidence** in the stability of their code
* **Slower release cycles** due to reactive debugging and patching

In legacy systems, test coverage can be even harder to address because of complex dependencies or poorly documented code. Developers may lack familiarity with older codebases or with testing frameworks in general, further compounding the problem.

Improving test coverage is a recognized best practice, but it requires time and expertise that many teams struggle to allocate.

## 2. Evaluate your options

{% data reusables.copilot.evaluate-options-intro %}

<a href="https://github.com/github-copilot/purchase?ref_product=copilot&ref_type=trial&ref_style=button" target="_blank" class="btn btn-primary mt-3 mr-3 no-underline"><span>Sign up for {% data variables.product.prodname_copilot_short %}</span>{% octicon "link-external" height:16 aria-label="link-external" %}</a>

### How {% data variables.product.prodname_copilot_short %} can help

{% data variables.product.prodname_copilot %} can significantly accelerate and simplify the process of writing unit tests. By understanding the surrounding code and context, {% data variables.product.prodname_copilot_short %} can suggest test functions that match the structure and logic of the code being tested.

{% data variables.product.prodname_copilot_short %}'s capabilities are useful across multiple scenarios:

* As developers write new functions, {% data variables.product.prodname_copilot_short %} can automatically suggest corresponding test cases inline.
* When refactoring legacy code, {% data variables.product.prodname_copilot_short %} can help generate test scaffolding to prevent regressions.
* For untested modules, developers can prompt {% data variables.product.prodname_copilot_short %} to generate meaningful test cases, even when test coverage is missing or inconsistent.

By making unit testing easier, faster, and less manual, {% data variables.product.prodname_copilot_short %} reduces the friction that can lead to gaps in test coverage, and helps teams adopt a quality-first mindset.

#### Use cases

* **Inline test generation**: Developers can ask {% data variables.product.prodname_copilot_short %} to generate tests for a specific function or module without switching context.
* **Better edge case coverage**: By prompting {% data variables.product.prodname_copilot_short %} for edge scenarios (such as null inputs, empty lists, or invalid states), developers can quickly cover more branches of logic.
* **Accelerated onboarding**: New team members can use {% data variables.product.prodname_copilot_short %} to understand how a function is expected to behave by looking at the generated test cases.
* **Assistance with CI/CD**: {% data variables.product.prodname_copilot_short %} can suggest how to integrate tests into your build pipeline, ensuring that coverage improvements directly support quality gates.

### Cultural considerations

{% data reusables.copilot.cultural-factors-intro %}

* Teams might **rely on manual testing** or insufficient automated testing. This could be caused by resource constraints for automation or a lack of experience with modern test tools.
* Teams might **wait too long to release**, deploying large batches of code at once, which makes bugs and regressions harder to detect. This could be caused by a lack of CI/CD pipeline maturity, strict compliance requirements, or long review cycles between PR and deployment.

## 3. Implement changes

{% data reusables.copilot.implement-changes-intro %}

* [Generate tests inline](<#generate-tests-inline>)
* [Cover edge cases](<#cover-edge-cases>)
* [Understand new code](<#understand-new-code>)
* [Get assistance with CI/CD](<#get-assistance-with-cicd>)
* [Best practices for developers](<#best-practices-for-developers>)
* [Resources for developers](<#resources-for-developers>)
* [Recommended features](<#recommended-features>)

### Generate tests inline

1. In VS Code, select the function you want to test and prompt {% data variables.product.prodname_copilot_short %}: `Generate a unit test for this code.`
1. {% data variables.product.prodname_copilot_short %} generates a test inline or in a separate test file, depending on the language and structure.
1. Review, refine, and accept the suggestion.

### Cover edge cases

1. After writing a test, ask {% data variables.product.prodname_copilot_short %}: `What are some edge cases I should test for this function?`

   Or: `Write test cases for when the input is null or empty.`

1. {% data variables.product.prodname_copilot_short %} suggests additional test cases to cover boundary conditions.
1. Review the tests and incorporate them into your test suite.

### Understand new code

1. Select a legacy function and ask {% data variables.product.prodname_copilot_short %}: `Explain what this function does and generate a test to validate it.`
1. {% data variables.product.prodname_copilot_short %} explains the function's purpose and suggests corresponding test cases.
1. Look at the test cases to understand the expected behavior and quickly build context.

### Get assistance with CI/CD

1. Review your test cases and commit them to the codebase.
1. Ask {% data variables.product.prodname_copilot_short %}: `Where should I place this test if I want it to run in CI?`
1. Based on the structure of the codebase, {% data variables.product.prodname_copilot_short %} will suggest where to place test files and how to update pipeline configurations.

### Best practices for developers

Developers **should**:

* Use descriptive comments or prompts when chatting with {% data variables.product.prodname_copilot_short %}. For example: `Generate unit tests for a function that calculates discounts based on user type and purchase amount.`
* Use {% data variables.product.prodname_copilot_short %} to explore logic coverage. For example: `What branches or conditions does this function have that should be tested?`
* Explore different prompt techniques and compare results from different AI models.

Developers **should not**:

* Accept generated tests without reviewing logic. Make sure the tests reflect actual requirements and handle realistic inputs and outputs.
* Skip asserting edge behavior. If you only test "happy paths," you risk missing regressions.
* Rely on {% data variables.product.prodname_copilot_short %} to guess undocumented business rules. Always provide context through prompts or comments.
* Treat {% data variables.product.prodname_copilot_short %} as a substitute for human code reviews. {% data variables.product.prodname_copilot_short %} accelerates the process, but you still need to apply engineering judgment.

### Resources for developers

* [AUTOTITLE](<https://github.com/copilot/tutorials/write-tests>)
* [How to generate unit tests with {% data variables.product.prodname_copilot %}: Tips and examples](<https://github.blog/ai-and-ml/github-copilot/how-to-generate-unit-tests-with-github-copilot-tips-and-examples/>)
* [{% data variables.product.prodname_copilot %} is EVERYWHERE in Visual Studio](<https://learn.microsoft.com/en-us/shows/github-copilot-for-visual-studio/github-copilot-is-everywhere-in-visual-studio-miniseries>) (video content with a section on testing)
* [AUTOTITLE](<https://github.com/copilot/concepts/prompting/prompt-engineering>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/change-the-chat-model>)

### Recommended features

* [{% data variables.copilot.copilot_chat_dotcom_short %}](<https://github.com/copilot/how-tos/copilot-on-github/chat-with-copilot/chat-in-github>)
* [{% data variables.product.prodname_copilot_short %} inline suggestions](<https://github.com/copilot/how-tos/copilot-in-your-ide/copilot-for-common-tasks/get-ide-code-suggestions>)
* [{% data variables.copilot.copilot_chat_short %} in the IDE](<https://github.com/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/chat-in-ide>)
* [{% data variables.copilot.copilot_cloud_agent %}](<https://github.com/copilot/concepts/copilot-surfaces/copilot-on-github>)

## Metrics to watch

{% data reusables.copilot.measure-changes-intro %}

* **Test coverage**: Track increases in line and branch coverage after {% data variables.product.prodname_copilot_short %} adoption. If possible, look at test coverage reports from your CI pipelines.
* **Bug rate after deployment**: Fewer bugs should be reported in production environments.
* **Developer confidence**: Use surveys or retrospectives to assess how confident developers feel shipping new code.
* **Time to write tests**: Measure reduction in time spent creating unit tests.


</details>
