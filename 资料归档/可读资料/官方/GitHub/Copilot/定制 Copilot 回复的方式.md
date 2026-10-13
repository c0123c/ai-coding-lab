# 定制 Copilot 回复的方式

比较个人、仓库和组织指令与提示文件，解释作用范围、优先级和 IDE 差异，帮助让回复符合项目约定。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-09-30](<https://github.com/github/docs/commit/d9fd377ea956f59385ac8cb2034ba5c613b6fd85>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/concepts/prompting/response-customization.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/定制 Copilot 回复的方式.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: About customizing GitHub Copilot responses
shortTitle: Response customization
intro: Learn about customizing the behavior of {% data variables.product.prodname_copilot %} to fit with your preferences and requirements.
versions:
  feature: copilot
redirect_from:
  - /copilot/customizing-copilot/about-customizing-github-copilot-chat-responses
  - /copilot/concepts/about-customizing-github-copilot-chat-responses
  - /copilot/concepts/code-review/coding-guidelines
  - /copilot/concepts/response-customization
contentType: concepts
category:
  - Configure Copilot
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

<!-- START WEB BROWSER TAB -->

{% webui %}

> [！NOTE] 本文的这个版本是关于 {% data variables.product.github %} 网站上的自定义指令。单击上面的选项卡了解其他环境。 <!-- markdownlint-disable-line MD027 -->

## 关于自定义 {% data variables.product.prodname_copilot_short %} 响应

如果您提供足够的上下文，{% data variables.product.prodname_copilot %} 可以提供根据您的个人喜好、您的团队工作方式、您使用的工具或您项目的具体情况量身定制的回复。您可以创建自动为您添加此信息的自定义说明，而不是重复将此上下文详细信息添加到提示中。附加信息不会显示，但可供 {% data variables.product.prodname_copilot_short %} 使用，以使其能够生成更高质量的响应。

{% data reusables.copilot.custom-insts-nondeterministic %}

## 自定义指令的类型

您可以使用三种主要类型的自定义指令来自定义 {% data variables.product.github %} 网站上的 {% data variables.product.prodname_copilot_short %} 响应：

* **[个人说明](<#关于个人指示>)**适用于您在{% data variables.product.github %}网站上与{% data variables.copilot.copilot_chat_short %}进行的所有对话。它们允许您指定您的个人偏好，例如首选语言或响应方式，确保响应适合您的个人需求。
* **[存储库自定义指令](<#关于存储库自定义说明>)**适用于特定存储库上下文中的对话。它们对于定义特定于项目的编码标准、框架或工具非常有用。例如，您可以指定存储库使用 TypeScript 和特定库，以确保所有贡献者的响应一致。
* **[组织自定义说明](<#关于组织自定义说明>)**适用于{% data variables.product.github %}网站上组织上下文中的对话。它们非常适合执行组织范围内的偏好，例如通用语言或安全准则。组织自定义说明只能由具有 {% data variables.copilot.copilot_business_short %} 或 {% data variables.copilot.copilot_enterprise_short %} 订阅的组织的组织所有者设置。

## 关于个人指示

{% data reusables.copilot.personal-instructions-note %}

您可以通过添加个人说明来自定义{% data variables.copilot.copilot_chat_short %}如何在{% data variables.product.prodname_dotcom_the_website %}上回复您，{% data variables.product.prodname_copilot_short %}仅适用于您。您可以在 {% data variables.product.prodname_dotcom_the_website %} 上的 {% data variables.copilot.copilot_chat_short %} 页面上的弹出窗口中执行此操作。参见[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-personal-instructions>)。

## 关于存储库自定义说明

您可以在{% data variables.product.prodname_dotcom_the_website %}上的{% data variables.product.prodname_copilot_short %}中使用三种类型的存储库自定义指令：

{% data reusables.copilot.custom-instructions-about %}

## 关于组织自定义说明

{% data reusables.copilot.custom-instructions-org-support %}

组织所有者可以添加 {% data variables.product.prodname_copilot_short %} 的说明，以根据组织的需求和偏好定制响应。例如，您可以选择始终让 {% data variables.product.prodname_copilot_short %} 以您公司选择的语言或特定风格进行回复。

组织的 {% data variables.product.prodname_copilot_short %} 设置中定义的自定义指令适用于该组织的所有成员，无论他们是否从该组织收到 {% data variables.product.prodname_copilot_short %} 订阅。

您可以添加的一些说明示例包括：
* `Always respond in Spanish.`
* `Do not generate code blocks in responses.`
* `For questions related to security, use the Security Docs Knowledge Base.`

## 自定义指令的优先级

{% data reusables.copilot.custom-instructions-chat-precedence %}

以下列表显示了完整的优先顺序，列表中较高的指令优先于列表中较低的指令：

* **个人**使用说明
* **存储库**自定义说明：
  * 任何适用的 `.github/instructions/**/*.instructions.md` 文件中的 ** 特定于路径的 ** 指令
  * ** `.github/copilot-instructions.md` 文件中的存储库范围 ** 指令
  * **Agent** 指令（例如，在 `AGENTS.md` 文件中）
* **组织**自定义说明

{% data reusables.copilot.custom-instructions-conflict %}

## 使用自定义指令

自定义指令由自然语言指令组成，当它们是简短的、独立的语句时最有效。选择是否在个人、存储库或组织级别添加指令时，请考虑您希望指令应用的范围。

以下是每种类型的自定义指令的一些常见用例和示例：
* **个人说明：**
  * 首选个人语言：`Always respond in Portuguese.`
  * 个人回复偏好：`Explain a single concept per line. Be clear and concise.`
* **存储库自定义说明：**
  * 编码标准：【`Use early returns whenever possible.`】
  * 框架：`Use Vue with the PrimeVue library.` 或 `Use Typescript rather than Javascript.`
  * 代码风格偏好：`Use camel case for variable names.`
* **组织自定义说明：**
  * 描述如何回答某些问题：`For questions related to security, use the Security Docs Knowledge Base or advise people to consult with #security on Slack.`
  * 专门使用单一语言的公司的首选语言：`Always respond in Portuguese.`
  * 组织范围的首选项：`Do not generate code blocks in responses.`

{% data reusables.copilot.code-review.custom-instructions-limit %}

{% data reusables.copilot.custom-instructions-effective %}

## 后续步骤

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-personal-instructions>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-organization-instructions>)

## 进一步阅读

* [AUTOTITLE](<https://github.com/copilot/concepts/agents/copilot-memory>)
* [AUTOTITLE](<https://github.com/copilot/reference/customization-cheat-sheet>)

{% endwebui %}

<!-- end of web browser tab -->

<!-- START VS CODE TAB -->

{% vscode %}

> [！NOTE] 本文的这个版本是关于{% data variables.product.prodname_vscode_shortname %}中的自定义指令和提示文件。单击上面的选项卡了解其他环境。 <!-- markdownlint-disable-line MD027 -->

## 关于自定义 {% data variables.product.prodname_copilot_short %} 响应

如果您提供足够的上下文，{% data variables.product.prodname_copilot %} 可以提供根据您的团队工作方式、您使用的工具或项目具体情况量身定制的响应。您可以在存储库中创建自动为您添加此信息的文件，而不是重复将此上下文详细信息添加到提示中。

您可以使用两种类型的文件为 {% data variables.product.prodname_vscode_shortname %} 中的 {% data variables.product.prodname_copilot_short %} 提供上下文和说明：

* **存储库自定义指令**允许您指定{% data variables.product.prodname_copilot_short %}在存储库上下文中工作时将考虑的指令和首选项。
* **提示文件**（公共预览版）允许您将常见提示说明和相关上下文保存在 Markdown 文件 (`*.prompt.md`) 中，然后您可以在聊天提示中重复使用。 {% data reusables.copilot.prompt-files-available-in-editors %}

虽然自定义指令有助于将代码库范围的上下文添加到每个 AI 工作流程，但提示文件可让您将指令添加到特定的聊天交互。

{% data reusables.copilot.custom-insts-nondeterministic %}

## 关于存储库自定义说明

您可以在{% data variables.product.prodname_vscode_shortname %}中使用三种类型的存储库自定义指令：

{% data reusables.copilot.custom-instructions-about %}

## 关于提示文件

> [!NOTE] 提示文件为 {% data variables.release-phases.public_preview %} 并且可能会更改。

提示文件允许您构建和共享可重用的提示指令以及附加上下文。提示文件是存储在工作区中的 Markdown 文件，它模仿 {% data variables.copilot.copilot_chat_short %} 中编写提示的现有格式（例如，`Rewrite #file:x.ts`）。这允许混合自然语言指令、附加上下文，甚至作为依赖项链接到其他提示文件。

常见用例包括：

* **代码生成**。为组件、测试或迁移创建可重用提示（例如，React 表单或 API 模拟）。
* **领域专业知识**。通过提示分享专业知识，例如安全实践或合规性检查。
* **团队协作**。参考规范和文档记录模式和指南。
* **载入**。为复杂流程或特定于项目的模式创建分步指南。

您的工作区中可以有多个提示文件，每个文件都定义了用于不同目的的提示。

### 示例

以下示例演示如何使用提示文件。

* `New React form.prompt.md` - 包含使用 React 生成表单的可重用任务的指令。

  ```markdown
  Your goal is to generate a new React form component.

  Ask for the form name and fields if not provided.

  Requirements for the form:
  - Use form design system components: [design-system/Form.md](../docs/design-system/Form.md)
  - Use `react-hook-form` for form state management:
    - Always define TypeScript types for your form data
    - Prefer *uncontrolled* components using register
    - Use `defaultValues` to prevent unnecessary rerenders
  - Use `yup` for validation:
    - Create reusable validation schemas in separate files
    - Use TypeScript types to ensure type safety
    - Customize UX-friendly validation rules
  ```

* `API security review.prompt.md` - 包含有关 REST API 安全实践的可重用信息，可用于对 REST API 进行安全审查。

  ```markdown
  Secure REST API review:
  - Ensure all endpoints are protected by authentication and authorization
  - Validate all user inputs and sanitize data
  - Implement rate limiting and throttling
  - Implement logging and monitoring for security events
  …
  ```

有关如何启用、创建和使用提示文件的信息，请参阅 [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/customize-copilot/configure-custom-instructions/add-repository-instructions-in-your-ide?tool=vscode#enabling-and-using-prompt-files>)。

{% data reusables.copilot.custom-instructions-effective %}

## 后续步骤

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions>)
* [AUTOTITLE](<https://github.com/copilot/tutorials/customize-code-review>)
* [AUTOTITLE](<https://github.com/copilot/reference/customization-cheat-sheet>)

{% endvscode %}

<!-- end of vs code tab -->

<!-- START VISUAL STUDIO TAB -->

{% visualstudio %}

> [！NOTE] 本文的这个版本是关于{% data variables.product.prodname_vs %}中的自定义指令和提示文件。单击上面的选项卡了解其他环境。 <!-- markdownlint-disable-line MD027 -->

## 关于自定义 {% data variables.product.prodname_copilot_short %} 响应

如果您提供足够的上下文，{% data variables.product.prodname_copilot %} 可以提供根据您的团队工作方式、您使用的工具或项目具体情况量身定制的响应。您可以在存储库中创建自动为您添加此信息的文件，而不是重复将此上下文详细信息添加到提示中。

您可以使用两种类型的文件为 {% data variables.product.prodname_vs %} 中的 {% data variables.product.prodname_copilot_short %} 提供上下文和说明：

* **存储库自定义指令**允许您指定{% data variables.product.prodname_copilot_short %}在存储库上下文中工作时将考虑的指令和首选项。
* **提示文件**允许您将常见提示说明和相关上下文保存在Markdown文件(`*.prompt.md`)中，然后您可以在聊天提示中重复使用。 {% data reusables.copilot.prompt-files-available-in-editors %}

虽然自定义指令有助于将代码库范围的上下文添加到每个 AI 工作流程，但提示文件可让您将指令添加到特定的聊天交互。

{% data reusables.copilot.custom-insts-nondeterministic %}

## 关于存储库自定义说明

您可以在{% data variables.product.prodname_vs %}中使用两种类型的存储库自定义指令：

* **存储库范围的自定义指令**，适用于在存储库上下文中发出的所有请求。

这些在存储库的 `.github` 目录中的 `copilot-instructions.md` 文件中指定。

* **特定于路径的自定义指令**，适用于在与指定路径匹配的文件上下文中发出的请求。

这些在存储库的 `.github/instructions` 目录内或下方的一个或多个 `NAME.instructions.md` 文件中指定。

通过使用特定于路径的指令，您可以避免使用仅适用于某些类型的文件或某些目录中的信息使存储库范围的指令过载。

有关跨不同 {% data variables.product.prodname_copilot_short %} 功能对每种类型的存储库自定义指令的支持的详细信息，请参阅 [AUTOTITLE](<https://github.com/copilot/reference/custom-instructions-support>)。

有关精选示例集合，请参阅 [AUTOTITLE](<https://github.com/copilot/tutorials/customization-library/custom-instructions>)。

## 关于提示文件

提示文件允许您构建和共享可重用的提示指令以及附加上下文。提示文件是存储在工作区中的 Markdown 文件，它模仿 {% data variables.copilot.copilot_chat_short %} 中编写提示的现有格式（例如，`Rewrite #file:x.ts`）。这允许混合自然语言指令、附加上下文，甚至作为依赖项链接到其他提示文件。

常见用例包括：

* **代码生成**。为组件、测试或迁移创建可重用提示（例如，React 表单或 API 模拟）。
* **领域专业知识**。通过提示分享专业知识，例如安全实践或合规性检查。
* **团队协作**。参考规范和文档记录模式和指南。
* **载入**。为复杂流程或特定于项目的模式创建分步指南。

您的工作区中可以有多个提示文件，每个文件都定义了用于不同目的的提示。

### 示例

以下示例演示如何使用提示文件。

* `New React form.prompt.md` - 包含使用 React 生成表单的可重用任务的指令。

  ```markdown
  Your goal is to generate a new React form component.

  Ask for the form name and fields if not provided.

  Requirements for the form:
  - Use form design system components: [design-system/Form.md](../docs/design-system/Form.md)
  - Use `react-hook-form` for form state management:
    - Always define TypeScript types for your form data
    - Prefer *uncontrolled* components using register
    - Use `defaultValues` to prevent unnecessary rerenders
  - Use `yup` for validation:
    - Create reusable validation schemas in separate files
    - Use TypeScript types to ensure type safety
    - Customize UX-friendly validation rules
  ```

* `API security review.prompt.md` - 包含有关 REST API 安全实践的可重用信息，可用于对 REST API 进行安全审查。

  ```markdown
  Secure REST API review:
  - Ensure all endpoints are protected by authentication and authorization
  - Validate all user inputs and sanitize data
  - Implement rate limiting and throttling
  - Implement logging and monitoring for security events
  …
  ```

有关如何创建和使用提示文件的信息，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/customize-copilot/configure-custom-instructions/add-repository-instructions-in-your-ide?tool=visualstudio#using-prompt-files>)。

{% data reusables.copilot.custom-instructions-effective %}

## 后续步骤

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions>)
* [AUTOTITLE](<https://github.com/copilot/reference/customization-cheat-sheet>)

{% endvisualstudio %}

<!-- end of vs tab -->

<!-- START JETBRAINS TAB -->

{% jetbrains %}

> [!NOTE] 本文的这个版本是关于 JetBrains IDE 中的自定义指令和提示文件。单击上面的选项卡了解其他环境。 <!-- markdownlint-disable-line MD027 -->

## 关于自定义 {% data variables.product.prodname_copilot_short %} 响应

如果您提供足够的上下文，{% data variables.product.prodname_copilot %} 可以提供根据您的团队工作方式、您使用的工具或项目具体情况量身定制的响应。您可以在存储库中创建一个自定义说明文件，自动为您添加此信息，而不是重复将此上下文详细信息添加到提示中。

您可以使用两种类型的文件为 JetBrains IDE 中的 {% data variables.product.prodname_copilot_short %} 提供上下文和说明：

* **存储库自定义指令**允许您指定{% data variables.product.prodname_copilot_short %}在存储库上下文中工作时将考虑的指令和首选项。
* **提示文件**（公共预览版）允许您将常见提示说明和相关上下文保存在 Markdown 文件 (`*.prompt.md`) 中，然后您可以在聊天提示中重复使用。 {% data reusables.copilot.prompt-files-available-in-editors %}

虽然自定义指令有助于将代码库范围的上下文添加到每个 AI 工作流程，但提示文件可让您将指令添加到特定的聊天交互。

{% data reusables.copilot.custom-insts-nondeterministic %}

## 关于存储库自定义说明

在 JetBrains IDE 中，存储库自定义指令由您在存储库中创建的单个文件 `.github/copilot-instructions.md` 组成。您添加到文件中的说明应该是简短的、独立的语句，添加上下文或相关信息以补充 {% data variables.product.prodname_copilot_short %} 提示。

### 支持存储库自定义指令

有关各种环境支持哪些类型的自定义指令的详细信息，请参阅[AUTOTITLE](<https://github.com/copilot/reference/custom-instructions-support>)。

### 自定义指令的用例

自定义指令的常见用例包括：

* **测试生成。**创建测试生成指令，例如指定某个测试框架的使用。
* **代码审查。** 指定审查代码的说明，例如告诉审查者查找代码中的特定错误。
* **提交消息生成。**编写用于生成提交消息的指令，例如要包含的信息的格式或类型。

### 示例

{% data reusables.copilot.repository-custom-instructions-example %}

有关精选示例集合，请参阅 [AUTOTITLE](<https://github.com/copilot/tutorials/customization-library/custom-instructions>)。

## 关于提示文件

> [!NOTE] 提示文件为 {% data variables.release-phases.public_preview %} 并且可能会更改。

提示文件允许您构建和共享可重用的提示指令以及附加上下文。提示文件是存储在工作区中的 Markdown 文件，它模仿 {% data variables.copilot.copilot_chat_short %} 中编写提示的现有格式（例如，`Rewrite #file:x.ts`）。这允许混合自然语言指令和附加上下文。

常见用例包括：

* **代码生成**。为组件、测试或迁移创建可重用提示（例如，React 表单或 API 模拟）。
* **领域专业知识**。通过提示分享专业知识，例如安全实践或合规性检查。
* **团队协作**。参考规范和文档记录模式和指南。
* **载入**。为复杂流程或特定于项目的模式创建分步指南。

您的工作区中可以有多个提示文件，每个文件都定义了用于不同目的的提示。

### 示例

以下示例演示如何使用提示文件。

* `New React form.prompt.md` - 包含使用 React 生成表单的可重用任务的指令。

  ```markdown
  Your goal is to generate a new React form component.

  Ask for the form name and fields if not provided.

  Requirements for the form:
  - Use form design system components: [design-system/Form.md](../docs/design-system/Form.md)
  - Use `react-hook-form` for form state management:
    - Always define TypeScript types for your form data
    - Prefer *uncontrolled* components using register
    - Use `defaultValues` to prevent unnecessary rerenders
  - Use `yup` for validation:
    - Create reusable validation schemas in separate files
    - Use TypeScript types to ensure type safety
    - Customize UX-friendly validation rules
  ```

* `API security review.prompt.md` - 包含有关 REST API 安全实践的可重用信息，可用于对 REST API 进行安全审查。

  ```markdown
  Secure REST API review:
  - Ensure all endpoints are protected by authentication and authorization
  - Validate all user inputs and sanitize data
  - Implement rate limiting and throttling
  - Implement logging and monitoring for security events
  …
  ```

有关精选示例集合，请参阅 [AUTOTITLE](<https://github.com/copilot/tutorials/customization-library/prompt-files>)。

{% data reusables.copilot.custom-instructions-effective %}

## 后续步骤

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions>)
* [AUTOTITLE](<https://github.com/copilot/tutorials/customize-code-review>)
* [AUTOTITLE](<https://github.com/copilot/reference/customization-cheat-sheet>)

{% endjetbrains %}

<!-- end of jetbrains tab -->

<!-- START XCODE TAB -->

{% xcode %}

> [!NOTE] 本文的这个版本是关于 Xcode 中的自定义指令。单击上面的选项卡了解其他环境。 <!-- markdownlint-disable-line MD027 -->

## 关于自定义 {% data variables.product.prodname_copilot_short %} 响应

如果您提供足够的上下文，{% data variables.product.prodname_copilot %} 可以提供根据您的团队工作方式、您使用的工具或项目具体情况量身定制的响应。您可以在存储库中创建一个自定义说明文件，自动为您添加此信息，而不是重复将此上下文详细信息添加到提示中。附加信息不会显示在聊天输入框中，但可供 {% data variables.product.prodname_copilot_short %} 使用，以使其生成更高质量的响应。

{% data reusables.copilot.custom-insts-nondeterministic %}

## 关于存储库自定义说明

在 Xcode 中，存储库自定义指令由您在存储库中创建的单个文件 `.github/copilot-instructions.md` 组成。您添加到文件中的说明应该是简短的、独立的语句，添加上下文或相关信息以补充 {% data variables.product.prodname_copilot_short %} 提示。

### 支持存储库自定义指令

有关各种环境支持哪些类型的自定义指令的详细信息，请参阅[AUTOTITLE](<https://github.com/copilot/reference/custom-instructions-support>)。

### 自定义指令的用例

存储库自定义指令的常见用例包括：

* **测试生成。**创建测试生成指令，例如指定某个测试框架的使用。
* **代码审查。** 指定审查代码的说明，例如告诉审查者查找代码中的特定错误。
* **提交消息生成。**编写用于生成提交消息的指令，例如要包含的信息的格式或类型。

### 示例

{% data reusables.copilot.repository-custom-instructions-example %}

有关精选示例集合，请参阅 [AUTOTITLE](<https://github.com/copilot/tutorials/customization-library/custom-instructions>)。

{% data reusables.copilot.custom-instructions-effective %}

## 后续步骤

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions>)
* [AUTOTITLE](<https://github.com/copilot/tutorials/customize-code-review>)
* [AUTOTITLE](<https://github.com/copilot/reference/customization-cheat-sheet>)

{% endxcode %}

<!-- end of xcode tab -->

<!-- START ECLIPSE TAB -->

{% eclipse %}

> [!NOTE] 本文的这个版本是关于 Eclipse 中的自定义指令。单击上面的选项卡了解其他环境。 <!-- markdownlint-disable-line MD027 -->

## 关于自定义 {% data variables.product.prodname_copilot_short %} 响应

如果您提供足够的上下文，{% data variables.product.prodname_copilot %} 可以提供根据您的团队工作方式、您使用的工具或项目具体情况量身定制的响应。您可以在存储库中创建一个自定义说明文件，自动为您添加此信息，而不是重复将此上下文详细信息添加到提示中。附加信息不会显示在聊天输入框中，但可供 {% data variables.product.prodname_copilot_short %} 使用，以使其生成更高质量的响应。

您可以使用两种类型的存储库自定义指令来为 Eclipse 中的 {% data variables.product.prodname_copilot_short %} 提供上下文和指令：

* **工作区自定义指令**适用于工作区中的所有项目，并允许您在单个文件中指定工作区范围的指令和首选项。
* **项目自定义说明**适用于特定项目。

{% data reusables.copilot.custom-insts-nondeterministic %}

## 关于存储库自定义说明

**工作区自定义指令**适用于工作区中的所有项目，并允许您指定工作区范围的指令和首选项。您可以使用工作区自定义指令向 Eclipse 中的 {% data variables.product.prodname_copilot_short %} 提供上下文和指令。

**项目自定义指令**由您在项目中创建的单个文件`.github/copilot-instructions.md`组成。您添加到文件中的说明应该是简短的、独立的语句，添加上下文或相关信息以补充 {% data variables.product.prodname_copilot_short %} 提示。

常见用例包括：

* **测试生成。**创建测试生成指令，例如指定某个测试框架的使用。
* **代码审查。** 指定审查代码的说明，例如告诉审查者查找代码中的特定错误。
* **提交消息生成。**编写用于生成提交消息的指令，例如要包含的信息的格式或类型。

### 示例

{% data reusables.copilot.repository-custom-instructions-example %}

有关精选示例集合，请参阅 [AUTOTITLE](<https://github.com/copilot/tutorials/customization-library/custom-instructions>)。

{% data reusables.copilot.custom-instructions-effective %}

## 后续步骤

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions>)
* [AUTOTITLE](<https://github.com/copilot/tutorials/customize-code-review>)
* [AUTOTITLE](<https://github.com/copilot/reference/customization-cheat-sheet>)

{% endeclipse %}

<!-- end of eclipse tab -->


## 原文对照

<details>
<summary>展开完整原文</summary>

<!-- START WEB BROWSER TAB -->

{% webui %}

> [!NOTE] This version of this article is about custom instructions on the {% data variables.product.github %} website. Click the tabs above for other environments. <!-- markdownlint-disable-line MD027 -->

## About customizing {% data variables.product.prodname_copilot_short %} responses

{% data variables.product.prodname_copilot %} can provide responses that are tailored to your personal preferences, the way your team works, the tools you use, or the specifics of your project, if you provide it with enough context to do so. Instead of repeatedly adding this contextual detail to your prompts, you can create custom instructions that automatically add this information for you. The additional information is not displayed, but is available to {% data variables.product.prodname_copilot_short %} to allow it to generate higher quality responses.

{% data reusables.copilot.custom-insts-nondeterministic %}

## Types of custom instructions

There are three main types of custom instructions that you can use to customize {% data variables.product.prodname_copilot_short %} responses on the {% data variables.product.github %} website:

* **[Personal instructions](<#about-personal-instructions>)** apply to all conversations you have with {% data variables.copilot.copilot_chat_short %} across the {% data variables.product.github %} website. They allow you to specify your individual preferences, such as preferred language or response style, ensuring that the responses are tailored to your personal needs.
* **[Repository custom instructions](<#about-repository-custom-instructions>)** apply to conversations within the context of a specific repository. They are useful for defining project-specific coding standards, frameworks, or tools. For example, you can specify that a repository uses TypeScript and a particular library, ensuring consistent responses for all contributors.
* **[Organization custom instructions](<#about-organization-custom-instructions>)** apply to conversations within the context of an organization on the {% data variables.product.github %} website. They are ideal for enforcing organization-wide preferences, such as a common language or security guidelines. Organization custom instructions can only be set by organization owners for organizations with a {% data variables.copilot.copilot_business_short %} or {% data variables.copilot.copilot_enterprise_short %} subscription.

## About personal instructions

{% data reusables.copilot.personal-instructions-note %}

You can customize how {% data variables.copilot.copilot_chat_short %} responds to you on {% data variables.product.prodname_dotcom_the_website %} by adding personal instructions, which {% data variables.product.prodname_copilot_short %} will only apply to you. You do this in a popup on the {% data variables.copilot.copilot_chat_short %} page on {% data variables.product.prodname_dotcom_the_website %}. See [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-personal-instructions>).

## About repository custom instructions

You can use three types of repository custom instructions in {% data variables.product.prodname_copilot_short %} on {% data variables.product.prodname_dotcom_the_website %}:

{% data reusables.copilot.custom-instructions-about %}

## About organization custom instructions

{% data reusables.copilot.custom-instructions-org-support %}

Organization owners can add instructions for {% data variables.product.prodname_copilot_short %}, to tailor responses to the needs and preferences of your organization. For example, you can choose to always have {% data variables.product.prodname_copilot_short %} respond in your company's language of choice or with a particular style.

Custom instructions defined in an organization's {% data variables.product.prodname_copilot_short %} settings are used for all members of the organization, irrespective of whether they receive their {% data variables.product.prodname_copilot_short %} subscription from that organization.

Some examples of instructions you could add are:
* `Always respond in Spanish.`
* `Do not generate code blocks in responses.`
* `For questions related to security, use the Security Docs Knowledge Base.`

## Precedence of custom instructions

{% data reusables.copilot.custom-instructions-chat-precedence %}

The following list shows the complete order of precedence, with instructions higher in this list taking precedence over those lower in the list:

* **Personal** instructions
* **Repository** custom instructions:
  * **Path-specific** instructions in any applicable `.github/instructions/**/*.instructions.md` file
  * **Repository-wide** instructions in the `.github/copilot-instructions.md` file
  * **Agent** instructions (for example, in an `AGENTS.md` file)
* **Organization** custom instructions

{% data reusables.copilot.custom-instructions-conflict %}

## Using custom instructions

Custom instructions consist of natural language instructions and are most effective when they are short, self-contained statements. Consider the scope over which you want the instruction to apply when choosing whether to add an instruction on the personal, repository, or organization level.

Here are some common use cases and examples for each type of custom instructions:
* **Personal instructions:**
  * Preferred individual language: `Always respond in Portuguese.`
  * Individual response preferences: `Explain a single concept per line. Be clear and concise.`
* **Repository custom instructions:**
  * Coding standards: `Use early returns whenever possible.`
  * Frameworks:  `Use Vue with the PrimeVue library.` or  `Use Typescript rather than Javascript.`
  * Code style preferences: `Use camel case for variable names.`
* **Organization custom instructions:**
  * Describe how to answer certain questions: `For questions related to security, use the Security Docs Knowledge Base or advise people to consult with #security on Slack.`
  * Preferred language for a company which exclusively speaks a single language: `Always respond in Portuguese.`
  * Organization-wide preferences: `Do not generate code blocks in responses.`

{% data reusables.copilot.code-review.custom-instructions-limit %}

{% data reusables.copilot.custom-instructions-effective %}

## Next steps

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-personal-instructions>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-organization-instructions>)

## Further reading

* [AUTOTITLE](<https://github.com/copilot/concepts/agents/copilot-memory>)
* [AUTOTITLE](<https://github.com/copilot/reference/customization-cheat-sheet>)

{% endwebui %}

<!-- end of web browser tab -->

<!-- START VS CODE TAB -->

{% vscode %}

> [!NOTE] This version of this article is about custom instructions and prompt files in {% data variables.product.prodname_vscode_shortname %}. Click the tabs above for other environments. <!-- markdownlint-disable-line MD027 -->

## About customizing {% data variables.product.prodname_copilot_short %} responses

{% data variables.product.prodname_copilot %} can provide responses that are tailored to the way your team works, the tools you use, or the specifics of your project, if you provide it with enough context to do so. Instead of repeatedly adding this contextual detail to your prompts, you can create files in your repository that automatically add this information for you.

There are two types of files you can use to provide context and instructions to {% data variables.product.prodname_copilot_short %} in {% data variables.product.prodname_vscode_shortname %}:

* **Repository custom instructions** allow you to specify instructions and preferences that {% data variables.product.prodname_copilot_short %} will consider when working in the context of the repository.
* **Prompt files** (public preview) allow you to save common prompt instructions and relevant context in Markdown files (`*.prompt.md`) that you can then reuse in your chat prompts. {% data reusables.copilot.prompt-files-available-in-editors %}

While custom instructions help to add codebase-wide context to each AI workflow, prompt files let you add instructions to a specific chat interaction.

{% data reusables.copilot.custom-insts-nondeterministic %}

## About repository custom instructions

You can use three types of repository custom instructions in {% data variables.product.prodname_vscode_shortname %}:

{% data reusables.copilot.custom-instructions-about %}

## About prompt files

> [!NOTE] Prompt files are {% data variables.release-phases.public_preview %} and subject to change.

Prompt files let you build and share reusable prompt instructions with additional context. A prompt file is a Markdown file, stored in your workspace, that mimics the existing format of writing prompts in {% data variables.copilot.copilot_chat_short %} (for example, `Rewrite #file:x.ts`). This allows blending natural language instructions, additional context, and even linking to other prompt files as dependencies.

Common use cases include:

* **Code generation**. Create reusable prompts for components, tests, or migrations (for example, React forms, or API mocks).
* **Domain expertise**. Share specialized knowledge through prompts, such as security practices, or compliance checks.
* **Team collaboration**. Document patterns and guidelines with references to specs and documentation.
* **Onboarding**. Create step-by-step guides for complex processes or project-specific patterns.

You can have multiple prompt files in your workspace, each of which defines a prompt for a different purpose.

### Examples

The following examples demonstrate how to use prompt files.

* `New React form.prompt.md` - contains instructions for a reusable task to generate a form using React.

  ```markdown
  Your goal is to generate a new React form component.

  Ask for the form name and fields if not provided.

  Requirements for the form:
  - Use form design system components: [design-system/Form.md](../docs/design-system/Form.md)
  - Use `react-hook-form` for form state management:
    - Always define TypeScript types for your form data
    - Prefer *uncontrolled* components using register
    - Use `defaultValues` to prevent unnecessary rerenders
  - Use `yup` for validation:
    - Create reusable validation schemas in separate files
    - Use TypeScript types to ensure type safety
    - Customize UX-friendly validation rules
  ```

* `API security review.prompt.md` - contains reusable information about security practices for REST APIs, which can be used to do security reviews of REST APIs.

  ```markdown
  Secure REST API review:
  - Ensure all endpoints are protected by authentication and authorization
  - Validate all user inputs and sanitize data
  - Implement rate limiting and throttling
  - Implement logging and monitoring for security events
  …
  ```

For information on how to enable, create, and use prompt files, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/customize-copilot/configure-custom-instructions/add-repository-instructions-in-your-ide?tool=vscode#enabling-and-using-prompt-files>).

{% data reusables.copilot.custom-instructions-effective %}

## Next steps

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions>)
* [AUTOTITLE](<https://github.com/copilot/tutorials/customize-code-review>)
* [AUTOTITLE](<https://github.com/copilot/reference/customization-cheat-sheet>)

{% endvscode %}

<!-- end of vs code tab -->

<!-- START VISUAL STUDIO TAB -->

{% visualstudio %}

> [!NOTE] This version of this article is about custom instructions and prompt files in {% data variables.product.prodname_vs %}. Click the tabs above for other environments. <!-- markdownlint-disable-line MD027 -->

## About customizing {% data variables.product.prodname_copilot_short %} responses

{% data variables.product.prodname_copilot %} can provide responses that are tailored to the way your team works, the tools you use, or the specifics of your project, if you provide it with enough context to do so. Instead of repeatedly adding this contextual detail to your prompts, you can create files in your repository that automatically add this information for you.

There are two types of files you can use to provide context and instructions to {% data variables.product.prodname_copilot_short %} in {% data variables.product.prodname_vs %}:

* **Repository custom instructions** allow you to specify instructions and preferences that {% data variables.product.prodname_copilot_short %} will consider when working in the context of the repository.
* **Prompt files** allow you to save common prompt instructions and relevant context in Markdown files (`*.prompt.md`) that you can then reuse in your chat prompts. {% data reusables.copilot.prompt-files-available-in-editors %}

While custom instructions help to add codebase-wide context to each AI workflow, prompt files let you add instructions to a specific chat interaction.

{% data reusables.copilot.custom-insts-nondeterministic %}

## About repository custom instructions

You can use two types of repository custom instructions in {% data variables.product.prodname_vs %}:

* **Repository-wide custom instructions**, which apply to all requests made in the context of a repository.

  These are specified in a `copilot-instructions.md` file in the `.github` directory of the repository.

* **Path-specific custom instructions**, which apply to requests made in the context of files that match a specified path.

  These are specified in one or more `NAME.instructions.md` files within or below the `.github/instructions` directory in the repository.

  By using path-specific instructions you can avoid overloading your repository-wide instructions with information that only applies to files of certain types, or in certain directories.

For details of support for each of these types of repository custom instructions across different {% data variables.product.prodname_copilot_short %} features, see [AUTOTITLE](<https://github.com/copilot/reference/custom-instructions-support>).

For a curated collection of examples, see [AUTOTITLE](<https://github.com/copilot/tutorials/customization-library/custom-instructions>).

## About prompt files

Prompt files let you build and share reusable prompt instructions with additional context. A prompt file is a Markdown file, stored in your workspace, that mimics the existing format of writing prompts in {% data variables.copilot.copilot_chat_short %} (for example, `Rewrite #file:x.ts`). This allows blending natural language instructions, additional context, and even linking to other prompt files as dependencies.

Common use cases include:

* **Code generation**. Create reusable prompts for components, tests, or migrations (for example, React forms, or API mocks).
* **Domain expertise**. Share specialized knowledge through prompts, such as security practices, or compliance checks.
* **Team collaboration**. Document patterns and guidelines with references to specs and documentation.
* **Onboarding**. Create step-by-step guides for complex processes or project-specific patterns.

You can have multiple prompt files in your workspace, each of which defines a prompt for a different purpose.

### Examples

The following examples demonstrate how to use prompt files.

* `New React form.prompt.md` - contains instructions for a reusable task to generate a form using React.

  ```markdown
  Your goal is to generate a new React form component.

  Ask for the form name and fields if not provided.

  Requirements for the form:
  - Use form design system components: [design-system/Form.md](../docs/design-system/Form.md)
  - Use `react-hook-form` for form state management:
    - Always define TypeScript types for your form data
    - Prefer *uncontrolled* components using register
    - Use `defaultValues` to prevent unnecessary rerenders
  - Use `yup` for validation:
    - Create reusable validation schemas in separate files
    - Use TypeScript types to ensure type safety
    - Customize UX-friendly validation rules
  ```

* `API security review.prompt.md` - contains reusable information about security practices for REST APIs, which can be used to do security reviews of REST APIs.

  ```markdown
  Secure REST API review:
  - Ensure all endpoints are protected by authentication and authorization
  - Validate all user inputs and sanitize data
  - Implement rate limiting and throttling
  - Implement logging and monitoring for security events
  …
  ```

For information on how to create and use prompt files, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/customize-copilot/configure-custom-instructions/add-repository-instructions-in-your-ide?tool=visualstudio#using-prompt-files>).

{% data reusables.copilot.custom-instructions-effective %}

## Next steps

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions>)
* [AUTOTITLE](<https://github.com/copilot/reference/customization-cheat-sheet>)

{% endvisualstudio %}

<!-- end of vs tab -->

<!-- START JETBRAINS TAB -->

{% jetbrains %}

> [!NOTE] This version of this article is about custom instructions and prompt files in JetBrains IDEs. Click the tabs above for other environments. <!-- markdownlint-disable-line MD027 -->

## About customizing {% data variables.product.prodname_copilot_short %} responses

{% data variables.product.prodname_copilot %} can provide responses that are tailored to the way your team works, the tools you use, or the specifics of your project, if you provide it with enough context to do so. Instead of repeatedly adding this contextual detail to your prompts, you can create a custom instructions file in your repository that automatically adds this information for you.

There are two types of files you can use to provide context and instructions to {% data variables.product.prodname_copilot_short %} in JetBrains IDEs:

* **Repository custom instructions** allow you to specify instructions and preferences that {% data variables.product.prodname_copilot_short %} will consider when working in the context of the repository.
* **Prompt files** (public preview) allow you to save common prompt instructions and relevant context in Markdown files (`*.prompt.md`) that you can then reuse in your chat prompts. {% data reusables.copilot.prompt-files-available-in-editors %}

While custom instructions help to add codebase-wide context to each AI workflow, prompt files let you add instructions to a specific chat interaction.

{% data reusables.copilot.custom-insts-nondeterministic %}

## About repository custom instructions

In JetBrains IDEs, repository custom instructions consist of a single file, `.github/copilot-instructions.md`, that you create in a repository. The instructions you add to the file should be short, self-contained statements that add context or relevant information to supplement a {% data variables.product.prodname_copilot_short %} prompt.

### Support for repository custom instructions

For details of which types of custom instructions are supported across various environments, see [AUTOTITLE](<https://github.com/copilot/reference/custom-instructions-support>).

### Use cases for custom instructions

Common use cases for custom instructions include:

* **Test generation.** Create instructions for test generation, such as specifying the use of a certain test framework.
* **Code review.** Specify instructions for reviewing code, such as telling a reviewer to look for a specific error in the code.
* **Commit message generation.** Write instructions for generating commit messages, such as format or the type of information to include.

### Example

{% data reusables.copilot.repository-custom-instructions-example %}

For a curated collection of examples, see [AUTOTITLE](<https://github.com/copilot/tutorials/customization-library/custom-instructions>).

## About prompt files

> [!NOTE] Prompt files are {% data variables.release-phases.public_preview %} and subject to change.

Prompt files let you build and share reusable prompt instructions with additional context. A prompt file is a Markdown file, stored in your workspace, that mimics the existing format of writing prompts in {% data variables.copilot.copilot_chat_short %} (for example, `Rewrite #file:x.ts`). This allows blending natural language instructions and additional context.

Common use cases include:

* **Code generation**. Create reusable prompts for components, tests, or migrations (for example, React forms, or API mocks).
* **Domain expertise**. Share specialized knowledge through prompts, such as security practices, or compliance checks.
* **Team collaboration**. Document patterns and guidelines with references to specs and documentation.
* **Onboarding**. Create step-by-step guides for complex processes or project-specific patterns.

You can have multiple prompt files in your workspace, each of which defines a prompt for a different purpose.

### Examples

The following examples demonstrate how to use prompt files.

* `New React form.prompt.md` - contains instructions for a reusable task to generate a form using React.

  ```markdown
  Your goal is to generate a new React form component.

  Ask for the form name and fields if not provided.

  Requirements for the form:
  - Use form design system components: [design-system/Form.md](../docs/design-system/Form.md)
  - Use `react-hook-form` for form state management:
    - Always define TypeScript types for your form data
    - Prefer *uncontrolled* components using register
    - Use `defaultValues` to prevent unnecessary rerenders
  - Use `yup` for validation:
    - Create reusable validation schemas in separate files
    - Use TypeScript types to ensure type safety
    - Customize UX-friendly validation rules
  ```

* `API security review.prompt.md` - contains reusable information about security practices for REST APIs, which can be used to do security reviews of REST APIs.

  ```markdown
  Secure REST API review:
  - Ensure all endpoints are protected by authentication and authorization
  - Validate all user inputs and sanitize data
  - Implement rate limiting and throttling
  - Implement logging and monitoring for security events
  …
  ```

For a curated collection of examples, see [AUTOTITLE](<https://github.com/copilot/tutorials/customization-library/prompt-files>).

{% data reusables.copilot.custom-instructions-effective %}

## Next steps

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions>)
* [AUTOTITLE](<https://github.com/copilot/tutorials/customize-code-review>)
* [AUTOTITLE](<https://github.com/copilot/reference/customization-cheat-sheet>)

{% endjetbrains %}

<!-- end of jetbrains tab -->

<!-- START XCODE TAB -->

{% xcode %}

> [!NOTE] This version of this article is about custom instructions in Xcode. Click the tabs above for other environments. <!-- markdownlint-disable-line MD027 -->

## About customizing {% data variables.product.prodname_copilot_short %} responses

{% data variables.product.prodname_copilot %} can provide responses that are tailored to the way your team works, the tools you use, or the specifics of your project, if you provide it with enough context to do so. Instead of repeatedly adding this contextual detail to your prompts, you can create a custom instructions file in your repository that automatically adds this information for you. The additional information is not displayed in the chat input box, but is available to {% data variables.product.prodname_copilot_short %} to allow it to generate higher quality responses.

{% data reusables.copilot.custom-insts-nondeterministic %}

## About repository custom instructions

In Xcode, repository custom instructions consist of a single file, `.github/copilot-instructions.md`, that you create in a repository. The instructions you add to the file should be short, self-contained statements that add context or relevant information to supplement a {% data variables.product.prodname_copilot_short %} prompt.

### Support for repository custom instructions

For details of which types of custom instructions are supported across various environments, see [AUTOTITLE](<https://github.com/copilot/reference/custom-instructions-support>).

### Use cases for custom instructions

Common use cases for repository custom instructions include:

* **Test generation.** Create instructions for test generation, such as specifying the use of a certain test framework.
* **Code review.** Specify instructions for reviewing code, such as telling a reviewer to look for a specific error in the code.
* **Commit message generation.** Write instructions for generating commit messages, such as format or the type of information to include.

### Example

{% data reusables.copilot.repository-custom-instructions-example %}

For a curated collection of examples, see [AUTOTITLE](<https://github.com/copilot/tutorials/customization-library/custom-instructions>).

{% data reusables.copilot.custom-instructions-effective %}

## Next steps

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions>)
* [AUTOTITLE](<https://github.com/copilot/tutorials/customize-code-review>)
* [AUTOTITLE](<https://github.com/copilot/reference/customization-cheat-sheet>)

{% endxcode %}

<!-- end of xcode tab -->

<!-- START ECLIPSE TAB -->

{% eclipse %}

> [!NOTE] This version of this article is about custom instructions in Eclipse. Click the tabs above for other environments. <!-- markdownlint-disable-line MD027 -->

## About customizing {% data variables.product.prodname_copilot_short %} responses

{% data variables.product.prodname_copilot %} can provide responses that are tailored to the way your team works, the tools you use, or the specifics of your project, if you provide it with enough context to do so. Instead of repeatedly adding this contextual detail to your prompts, you can create a custom instructions file in your repository that automatically adds this information for you. The additional information is not displayed in the chat input box, but is available to {% data variables.product.prodname_copilot_short %} to allow it to generate higher quality responses.

There are two types of repository custom instructions you can use to provide context and instructions to {% data variables.product.prodname_copilot_short %} in Eclipse:

* **Workspace custom instructions** which apply to all projects in a workspace, and allow you to specify workspace-wide instructions and preferences, in a single file.
* **Project custom instructions** which apply to a specific project.

{% data reusables.copilot.custom-insts-nondeterministic %}

## About repository custom instructions

**Workspace custom instructions** apply to all projects in a workspace, and allow you to specify workspace-wide instructions and preferences. You can use workspace custom instructions to provide context and instructions to {% data variables.product.prodname_copilot_short %} in Eclipse.

**Project custom instructions** consist of a single file, `.github/copilot-instructions.md`, that you create in a project. The instructions you add to the file should be short, self-contained statements that add context or relevant information to supplement a {% data variables.product.prodname_copilot_short %} prompt.

Common use cases include:

* **Test generation.** Create instructions for test generation, such as specifying the use of a certain test framework.
* **Code review.** Specify instructions for reviewing code, such as telling a reviewer to look for a specific error in the code.
* **Commit message generation.** Write instructions for generating commit messages, such as format or the type of information to include.

### Example

{% data reusables.copilot.repository-custom-instructions-example %}

For a curated collection of examples, see [AUTOTITLE](<https://github.com/copilot/tutorials/customization-library/custom-instructions>).

{% data reusables.copilot.custom-instructions-effective %}

## Next steps

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions>)
* [AUTOTITLE](<https://github.com/copilot/tutorials/customize-code-review>)
* [AUTOTITLE](<https://github.com/copilot/reference/customization-cheat-sheet>)

{% endeclipse %}

<!-- end of eclipse tab -->


</details>
