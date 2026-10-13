# Continue 旧版 @Codebase 和 @Folder 上下文参考

旧版参考。保留索引、检索、重排、忽略文件等旧配置，并明确要求迁移到代码库与文档感知新方式；适合排查旧项目和理解迁移关系。

- 来源：Continue
- 资料类型：旧版参考
- 存档版本：`5522c6f44ca0`。
- 文档时间：源码提交（存档版）：[2025-12-02](<https://github.com/continuedev/continue/commit/23b359aab0dc964d6e96a2bb641538d13b285e7f>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：原文已标记 Deprecated，不作为新项目默认配置指南。

[原文发布页](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/reference/deprecated-codebase.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Continue/Continue 旧版 @Codebase 和 @Folder 上下文参考.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: "@Codebase (Deprecated)"
description: Deprecated codebase context provider - use the new codebase awareness approach instead
keywords: [deprecated, codebase, embeddings, context, legacy]
noindex: true
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

**注意：**

**此功能已被弃用。** `@Codebase` 上下文提供程序已被弃用，以支持更集成的代码库感知方法。请参阅我们的 [使 Agent 模式了解代码库和文档的指南 ](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/reference/Continue_让 Continue Agent 获取代码库和文档上下文.md>) 了解推荐的方法。

## 迁移指南

如果您当前正在使用 `@Codebase` 或 `@Folder` 上下文提供程序，请迁移到我们的 [ 代码库和文档意识指南 ](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/reference/Continue_让 Continue Agent 获取代码库和文档上下文.md>) 中概述的新方法。新方法提供：

- 与Continue的Agent模式功能更好地集成
- 更智能的上下文选择
- 提高性能和准确性

## 遗留文档

*以下是`@Codebase`上下文提供程序的原始文档，保留以供参考*

Continue 对您的代码库进行索引，以便稍后可以自动从整个工作区中提取最相关的上下文。这是通过基于嵌入的检索和关键字搜索的组合来完成的。默认情况下，所有嵌入均使用 `transformers.js` 本地计算并本地存储在 `~/.continue/index` 中。

**说明：**

**注意：** `transformers.js` 不能在 JetBrains IDE 中使用。但是，您可以
从[列表
此处](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/customize/model-roles/embeddings>)中选择不同的嵌入模型。

## 如何使用 @Codebase 和 @Folder 上下文提供程序

目前，代码库检索功能可用作“代码库”和“文件夹”上下文提供程序。您可以通过在输入框中键入 `@Codebase` 或 `@Folder`，然后提出问题来使用它们。输入框的内容将与代码库（或文件夹）其余部分的嵌入进行比较，以确定相关文件。

### @Codebase Context Provider 何时有用

以下是一些有用的常见用例：

- **询问有关代码库的高级问题**
  - “如何向服务器添加新端点？”
  - “我们是否在任何地方使用 VS Code 的 CodeLens 功能？”
  - “是否已经编写了任何代码来将 HTML 转换为 Markdown？”
- **使用现有示例作为参考生成代码**
  - “使用与现有组件相同的模式，生成带有日期选择器的新 React 组件”
  - “使用 Python 的 argparse 为该项目编写 CLI 应用程序草稿”
  - “在 `bar` 类中实现 `foo` 方法，遵循 `baz` 的其他子类中看到的模式。
- **使用 `@Folder` 提出有关特定文件夹的问题，增加相关结果的可能性**
  - “这个文件夹的主要用途是什么？”
  - “我们如何使用VS Code的CodeLensAPI？”
  - 或上述任何示例，但使用 `@Folder` 而不是 `@Codebase`

### 当 @Codebase Context Provider 没有用时

以下是它没有用的用例：

- **当您需要LLM来查看_代码库中的每个_文件时**
  - “查找调用 `foo` 函数的所有位置”
  - “检查我们的代码库并发现任何拼写错误”
- **重构任务**
  - “向 `bar` 函数添加新参数并更新用法”

## 如何配置 @Codebase 上下文提供程序设置

有几个选项可让您配置 `@codebase` 上下文提供程序的行为，这些选项与 `@folder` 上下文提供程序相同：

#### YAML

**代码示例：** `config.yaml`

```yaml
name: My Config
version: 0.0.1
schema: v1

context:
  - provider: codebase
    params:
      nRetrieve: 25
      nFinal: 5
      useReranking: true
```

#### JSON

**代码示例：** `config.json`

```json
{
  "contextProviders": [
    {
      "name": "codebase",
      "params": {
        "nRetrieve": 25,
        "nFinal": 5,
        "useReranking": true
      }
    }
  ]
}
```

### `nRetrieve`

最初从矢量数据库检索的结果数（默认值：25）

### `nFinal`

重新排名后使用的最终结果数（默认值：5）

### `useReranking`

是否使用重新排名，这将允许初始选择 `nRetrieve` 结果，然后将使用 LLM 选择顶部 `nFinal` 结果（默认值：true）

## 如何在索引过程中忽略文件

Continue 尊重 `.gitignore` 文件以确定哪些文件不应建立索引。如果您想排除其他文件，可以将它们添加到 `.continueignore` 文件中，该文件遵循与 `.gitignore` 完全相同的规则。

Continue 还支持 ** 全局 ** `.continueignore` 文件，所有工作区都将遵守该文件，可以在 `~/.continue/.continueignore` 创建该文件。

如果您想准确查看 Continue 已索引哪些文件，则元数据存储在 `~/.continue/index/index.sqlite` 中。您可以使用 [DB Browser for SQLite](<https://sqlitebrowser.org/>) 等工具来查看此文件中的 `tag_catalog` 表。

如果需要强制刷新索引，请使用`cmd/ctrl` + `shift` + `p` +“重新加载窗口”重新加载VS Code窗口。


## 原文对照

<details>
<summary>展开完整原文</summary>

**注意：**

**This feature is deprecated.** The `@Codebase` context provider has been deprecated in favor of a more integrated approach to codebase awareness. Please refer to our [Guide on Making Agent Mode Aware of Codebases and Documentation](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/reference/Continue_让 Continue Agent 获取代码库和文档上下文.md>) for the recommended approach.

## Migration Guide

If you're currently using `@Codebase` or `@Folder` context providers, please migrate to the new approach outlined in our [codebase and documentation awareness guide](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/reference/Continue_让 Continue Agent 获取代码库和文档上下文.md>). The new approach provides:

- Better integration with Continue's Agent mode features
- More intelligent context selection
- Improved performance and accuracy

## Legacy Documentation

*Below is the original documentation for the `@Codebase` context provider, preserved for reference*

Continue indexes your codebase so that it can later automatically pull in the most relevant context from throughout your workspace. This is done via a combination of embeddings-based retrieval and keyword search. By default, all embeddings are calculated locally using `transformers.js` and stored locally in `~/.continue/index`.

**说明：**

**Note:** `transformers.js` cannot be used in JetBrains IDEs. However, you can
select a different embeddings model from [the list
here](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/customize/model-roles/embeddings>).

## How to Use @Codebase and @Folder Context Providers

Currently, the codebase retrieval feature is available as the "codebase" and "folder" context providers. You can use them by typing `@Codebase` or `@Folder` in the input box, and then asking a question. The contents of the input box will be compared with the embeddings from the rest of the codebase (or folder) to determine relevant files.

### When @Codebase Context Provider Is Useful

Here are some common use cases where it can be useful:

- **Asking high-level questions about your codebase**
  - "How do I add a new endpoint to the server?"
  - "Do we use VS Code's CodeLens feature anywhere?"
  - "Is there any code written already to convert HTML to markdown?"
- **Generate code using existing samples as reference**
  - "Generate a new React component with a date picker, using the same patterns as existing components"
  - "Write a draft of a CLI application for this project using Python's argparse"
  - "Implement the `foo` method in the `bar` class, following the patterns seen in other subclasses of `baz`.
- **Use `@Folder` to ask questions about a specific folder, increasing the likelihood of relevant results**
  - "What is the main purpose of this folder?"
  - "How do we use VS Code's CodeLens API?"
  - Or any of the above examples, but with `@Folder` instead of `@Codebase`

### When @Codebase Context Provider Is Not Useful

Here are use cases where it is not useful:

- **When you need the LLM to see _literally every_ file in your codebase**
  - "Find everywhere where the `foo` function is called"
  - "Review our codebase and find any spelling mistakes"
- **Refactoring tasks**
  - "Add a new parameter to the `bar` function and update usages"

## How to Configure @Codebase Context Provider Settings

There are a few options that let you configure the behavior of the `@codebase` context provider, which are the same for the `@folder` context provider:

#### YAML

**代码示例：** `config.yaml`

```yaml
name: My Config
version: 0.0.1
schema: v1

context:
  - provider: codebase
    params:
      nRetrieve: 25
      nFinal: 5
      useReranking: true
```

#### JSON

**代码示例：** `config.json`

```json
{
  "contextProviders": [
    {
      "name": "codebase",
      "params": {
        "nRetrieve": 25,
        "nFinal": 5,
        "useReranking": true
      }
    }
  ]
}
```

### `nRetrieve`

Number of results to initially retrieve from vector database (default: 25)

### `nFinal`

Final number of results to use after re-ranking (default: 5)

### `useReranking`

Whether to use re-ranking, which will allow initial selection of `nRetrieve` results, then will use an LLM to select the top `nFinal` results (default: true)

## How to Ignore Files During Indexing

Continue respects `.gitignore` files in order to determine which files should not be indexed. If you'd like to exclude additional files, you can add them to a `.continueignore` file, which follows the exact same rules as `.gitignore`.

Continue also supports a **global** `.continueignore` file that will be respected for all workspaces, which can be created at `~/.continue/.continueignore`.

If you want to see exactly what files Continue has indexed, the metadata is stored in `~/.continue/index/index.sqlite`. You can use a tool like [DB Browser for SQLite](<https://sqlitebrowser.org/>) to view the `tag_catalog` table within this file.

If you need to force a refresh of the index, reload the VS Code window with `cmd/ctrl` + `shift` + `p` + "Reload Window".


</details>
