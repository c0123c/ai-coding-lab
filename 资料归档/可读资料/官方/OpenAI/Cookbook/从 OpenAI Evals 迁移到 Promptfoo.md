# 从 OpenAI Evals 迁移到 Promptfoo

比较测试数据、提示、模型和评分配置，讲解重建评测、验证迁移、手动处理和本地或 CI 工作流定制；以归档正文描述为准。

- 来源：OpenAI／Cookbook
- 资料类型：存档文档
- 存档版本：`5bd6cb40b6b8`。
- 文档时间：源码提交（存档版）：[2026-08-24](<https://github.com/openai/openai-cookbook/commit/5a49d2a28a2779b1928d9cee988510e85e014910>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-cookbook/blob/main/examples/evaluation/moving-from-openai-evals-to-promptfoo.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Cookbook/从 OpenAI Evals 迁移到 Promptfoo.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

OpenAI 正在逐步关闭 Evals 产品，并建议 [Promptfoo](<https://www.promptfoo.dev/>) 继续并扩展您的评估工作流程。

Promptfoo 是一个开源 CLI 和库，用于评估和红队 AI 应用程序和 agents。它为您提供了一种更灵活的方式来在本地或 CI 中运行、维护和扩展您的评估。

## 当您迁移到 Promptfoo 后会发生什么变化

OpenAI Evals 和 Promptfoo 共享相同的核心构建块：测试数据、提示、providers/models 和输出评分标准。当您在 Promptfoo 中重新创建评估时，请将这些部分映射到可移植配置中，以便您可以继续测试相同的行为。

主要区别在于工作流程。 OpenAI 评估在 OpenAI 平台中进行管理，仪表板中提供评估、运行、结果和相关数据集工作流程。 Promptfoo 以可移植配置文件和 CLI 工作流程为中心。您可以将评估与应用程序代码放在一起，在本地或在 CI 中运行它们，并根据测试需求的变化进行调整。

|OpenAI 评估|提示富|
| --- | --- |
|评估和结果在 OpenAI 平台仪表板中管理。|评估在配置文件中定义，并使用 CLI 或 CI 运行。|
|分级机被配置为评估中的测试标准。|评分行为被配置为断言和指标。|
|运行与平台评估工作流程相关联。|运行可以集成到开发、测试和部署工作流程中。|
|数据集审查和提示迭代等平台功能保留在 OpenAI 中。|提供者选择和评估定制在 Promptfoo 中管理。|

Promptfoo 并不是简单地在另一个工具中重现 OpenAI Evals 界面。它提供了一个更加可移植、面向代码的工作流程，用于随着时间的推移维护、运行和扩展评估。

## 将评估迁移到 Promptfoo

本指南介绍了在 Promptfoo 中手动重新创建评估。它不需要 OpenAI Evals 导出功能。

### 开始之前

- [安装Promptfoo](<https://www.promptfoo.dev/docs/installation/>)。
- 现有的 OpenAI Evals 评估。
- 评估中使用的模型提供程序的 API 密钥，例如 `OPENAI_API_KEY`。

### 创建 Promptfoo 配置

在 `promptfooconfig.yaml` 文件中重新创建评估的提示、提供程序、测试用例和断言。配置格式请参见[在Promptfoo](<https://www.promptfoo.dev/docs/configuration/guide/>)中配置评估。

### 在 Promptfoo 中运行手动重新创建的评估

运行新的评估并打开结果查看器：

```bash
promptfoo eval -c promptfooconfig.yaml --no-cache
promptfoo view
```

`eval` 创建一个新的 Promptfoo 评估运行，而 `view` 在本地打开其结果。此新运行与之前完成的任何 OpenAI 评估运行是分开的。

如果您修改配置或遇到配置错误，请在重试之前验证它：

```bash
promptfoo validate config -c promptfooconfig.yaml
```

![运行重新创建的评估后提示foo结果视图](<https://github.com/images/promptfoo-custom-example-view.png>)

### 验证迁移的评估

第一次运行 Promptfoo 后，检查结果以确认评估的行为符合预期。在正在进行的工作流程中使用 eval 之前，请比较重新创建的提示、断言和输出。

基于相似性的评分可能不会在系统中产生相同的数值结果。任何手动重新创建的评分器，尤其是作为评判评分器的 LLM-，都应该在依赖其进行回归决策之前进行验证。

### 需要手动设置的情况

某些评估可能需要在 Promptfoo 中进行额外设置。

|情况|该怎么办|
| --- | --- |
|需要重新创建评分器。|将其重新创建为 Promptfoo 断言，并在依赖它之前验证其行为。|
|工具、agent 或自定义提供程序工作流程未在您的 Promptfoo 配置中表示。|在 Promptfoo 中配置该工作流程并在使用迁移的 eval 之前对其进行测试。|

### 在 Promptfoo 中自定义您的工作流程

一旦迁移的评估按预期工作，您可以使用 Promptfoo 扩展测试覆盖范围，在 CI 中运行评估，并添加红队测试。

如果您使用 Codex，请参阅 [将评估添加到您的 AI 应用程序 ](<https://github.com/codex/use-cases/ai-app-evals>) 以使用 Promptfoo Codex 插件构建和维护 Promptfoo 评估套件。

### 下一步

- [安装提示foo](<https://www.promptfoo.dev/docs/installation/>)
- [在Promptfoo中配置评估](<https://www.promptfoo.dev/docs/configuration/guide/>)
- [配置断言和预期输出](<https://www.promptfoo.dev/docs/configuration/expected-outputs/>)
- [在CI/CD](<https://www.promptfoo.dev/docs/integrations/ci-cd/>)中运行Promptfoo


## 原文对照

<details>
<summary>展开完整原文</summary>

OpenAI is winding down the Evals product and recommends [Promptfoo](<https://www.promptfoo.dev/>) for continuing and extending your evaluation workflows.

Promptfoo is an open-source CLI and library for evaluating and red-teaming AI apps and agents. It gives you a more flexible way to run, maintain, and extend your evals locally or in CI.

## What Changes When You Move to Promptfoo

OpenAI Evals and Promptfoo share the same core building blocks: test data, prompts, providers/models, and criteria for scoring outputs. When you recreate an evaluation in Promptfoo, map these pieces into a portable configuration so you can continue testing the same behavior.

The main difference is the workflow. OpenAI Evals is managed in the OpenAI Platform, where evaluations, runs, results, and related dataset workflows are available in the dashboard. Promptfoo is centered on a portable configuration file and CLI workflow. You can keep evaluations alongside your application code, run them locally or in CI, and adapt them as your testing needs evolve.

| OpenAI Evals | Promptfoo |
| --- | --- |
| Evaluations and results are managed in the OpenAI Platform dashboard. | Evaluations are defined in a config file and run with the CLI or in CI. |
| Graders are configured as testing criteria in an eval. | Grading behavior is configured as assertions and metrics. |
| Runs are tied to the Platform evaluation workflow. | Runs can be integrated into development, testing, and deployment workflows. |
| Platform features such as dataset review and prompt iteration remain in OpenAI. | Provider selection and evaluation customization are managed in Promptfoo. |

Promptfoo does not simply reproduce the OpenAI Evals interface in another tool. It provides a more portable, code-oriented workflow for maintaining, running, and extending evaluations over time.

## Migrate an Evaluation to Promptfoo

This guide covers manually recreating an evaluation in Promptfoo. It does not require an OpenAI Evals export feature.

### Before You Start

- [Install Promptfoo](<https://www.promptfoo.dev/docs/installation/>).
- An existing OpenAI Evals evaluation.
- An API key for the model provider used in your eval, such as `OPENAI_API_KEY`.

### Create a Promptfoo Configuration

Recreate your evaluation's prompts, provider, test cases, and assertions in a `promptfooconfig.yaml` file. See [Configure evaluations in Promptfoo](<https://www.promptfoo.dev/docs/configuration/guide/>) for the configuration format.

### Run a Manually Recreated Evaluation in Promptfoo

Run a fresh evaluation and open the results viewer:

```bash
promptfoo eval -c promptfooconfig.yaml --no-cache
promptfoo view
```

`eval` creates a new Promptfoo evaluation run, while `view` opens its results locally. This new run is separate from any previously completed OpenAI Evals runs.

If you modify the configuration or encounter a configuration error, validate it before retrying:

```bash
promptfoo validate config -c promptfooconfig.yaml
```

![Promptfoo results view after running the recreated evaluation](<https://github.com/images/promptfoo-custom-example-view.png>)

### Verify the Migrated Evaluation

After your first Promptfoo run, review the results to confirm that the evaluation behaves as expected. Compare the recreated prompts, assertions, and outputs before using the eval in an ongoing workflow.

Similarity-based scoring may not produce identical numerical results across systems. Any manually recreated grader, especially an LLM-as-a-judge grader, should be validated before you rely on it for regression decisions.

### Cases That Require Manual Setup

Some evaluations may require additional setup in Promptfoo.

| Situation | What to do |
| --- | --- |
| A grader needs to be recreated. | Recreate it as a Promptfoo assertion and verify its behavior before relying on it. |
| A tool, agent, or custom provider workflow is not represented in your Promptfoo configuration. | Configure that workflow in Promptfoo and test it before using the migrated eval. |

### Customize Your Workflow in Promptfoo

Once your migrated evaluation is working as expected, you can use Promptfoo to expand your test coverage, run evals in CI, and add red-team testing.

If you use Codex, see [Add evals to your AI application](<https://github.com/codex/use-cases/ai-app-evals>) to build and maintain Promptfoo eval suites with the Promptfoo Codex plugin.

### Next Steps

- [Install Promptfoo](<https://www.promptfoo.dev/docs/installation/>)
- [Configure evaluations in Promptfoo](<https://www.promptfoo.dev/docs/configuration/guide/>)
- [Configure assertions and expected outputs](<https://www.promptfoo.dev/docs/configuration/expected-outputs/>)
- [Run Promptfoo in CI/CD](<https://www.promptfoo.dev/docs/integrations/ci-cd/>)


</details>
