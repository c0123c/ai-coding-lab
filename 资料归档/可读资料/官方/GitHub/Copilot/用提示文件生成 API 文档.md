# 用提示文件生成 API 文档

提供从代码生成 OpenAPI 说明的提示模板及使用方式；属于文档生成样例。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-03-23](<https://github.com/github/docs/commit/31396cc18d4689913d9751797e7e6c418252e41b>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/tutorials/customization-library/prompt-files/document-api.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/用提示文件生成 API 文档.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Document API
intro: 'Generate comprehensive API documentation from your code.'
versions:
  feature: copilot
category:
  - Prompt files
  - Development workflows
  - Configure Copilot
  - Scale institutional knowledge
complexity:
  - Advanced
octicon: copilot
contentType: tutorials
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

{% data reusables.copilot.prompt-files-preview-note %}

此提示文件通过分析您的 API 代码并创建标准化的机器可读文档，为 REST API 端点生成 OpenAPI 3.0 规范。

## OpenAPI规格提示

```text copy
---
agent: 'agent'
description: 'Generate OpenAPI 3.0 specification for API endpoints'
---

## Task

Analyze the API endpoint code and generate a valid OpenAPI 3.0 specification in YAML format.

## OpenAPI Structure

Generate a complete OpenAPI spec including:

1. **OpenAPI Header**
   - OpenAPI version (3.0.3)
   - API info (title, description, version)
   - Server configuration

2. **Path Definitions**
   - HTTP method and path
   - Operation summary and description
   - Tags for organization

3. **Parameters Schema**
   - Path parameters with type validation
   - Query parameters with constraints and defaults
   - Request body schema using proper JSON Schema
   - Required vs optional parameters

4. **Response Schemas**
   - Success responses (200, 201, etc.) with schema definitions
   - Error responses (400, 401, 404, 500) with error schema
   - Content-Type specifications
   - Realistic example values

5. **Components Section**
   - Reusable schemas for request/response models
   - Security schemes (Bearer token, API key, etc.)
   - Common parameter definitions

## Requirements

- Generate valid OpenAPI 3.0.3 YAML that passes validation
- Use proper JSON Schema for all data models
- Include realistic example values, not placeholders
- Define reusable components to avoid duplication
- Add appropriate data validation (required fields, formats, constraints)
- Include security requirements where applicable

Focus on: ${input:endpoint_focus:Which specific endpoint or endpoints should be documented?}

Generate production-ready OpenAPI specification that can be used with Swagger UI, Postman, and code generators.
```

## 如何使用这个提示文件

1. 将以上内容另存为`.github/prompts`文件夹中的`document-api.prompt.md`。
1. 在“{% data variables.product.prodname_vscode %}”中，显示“{% data variables.copilot.copilot_chat_short %}”视图，并输入“`/document-api`”。或者，您还可以通过键入 `endpoint_focus=GET /activities` 来指定您想要文档的特定端点。

{% data reusables.copilot.prompt-files-further-reading %}


## 原文对照

<details>
<summary>展开完整原文</summary>

{% data reusables.copilot.prompt-files-preview-note %}

This prompt file generates OpenAPI 3.0 specifications for REST API endpoints by analyzing your API code and creating standardized, machine-readable documentation.

## OpenAPI specification prompt

```text copy
---
agent: 'agent'
description: 'Generate OpenAPI 3.0 specification for API endpoints'
---

## Task

Analyze the API endpoint code and generate a valid OpenAPI 3.0 specification in YAML format.

## OpenAPI Structure

Generate a complete OpenAPI spec including:

1. **OpenAPI Header**
   - OpenAPI version (3.0.3)
   - API info (title, description, version)
   - Server configuration

2. **Path Definitions**
   - HTTP method and path
   - Operation summary and description
   - Tags for organization

3. **Parameters Schema**
   - Path parameters with type validation
   - Query parameters with constraints and defaults
   - Request body schema using proper JSON Schema
   - Required vs optional parameters

4. **Response Schemas**
   - Success responses (200, 201, etc.) with schema definitions
   - Error responses (400, 401, 404, 500) with error schema
   - Content-Type specifications
   - Realistic example values

5. **Components Section**
   - Reusable schemas for request/response models
   - Security schemes (Bearer token, API key, etc.)
   - Common parameter definitions

## Requirements

- Generate valid OpenAPI 3.0.3 YAML that passes validation
- Use proper JSON Schema for all data models
- Include realistic example values, not placeholders
- Define reusable components to avoid duplication
- Add appropriate data validation (required fields, formats, constraints)
- Include security requirements where applicable

Focus on: ${input:endpoint_focus:Which specific endpoint or endpoints should be documented?}

Generate production-ready OpenAPI specification that can be used with Swagger UI, Postman, and code generators.
```

## How to use this prompt file

1. Save the above content as `document-api.prompt.md` in your `.github/prompts` folder.
1. In {% data variables.product.prodname_vscode %}, display the {% data variables.copilot.copilot_chat_short %} view and enter `/document-api`. Optionally, you can also specify what specific endpoint you want documentation for by typing `endpoint_focus=GET /activities`, for example.

{% data reusables.copilot.prompt-files-further-reading %}


</details>
