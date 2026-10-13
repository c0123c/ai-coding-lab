# Opus 4.5 迁移插件

介绍将指定旧模型的代码和提示迁移到 Opus 4.5 的插件及使用入口；这是针对特定历史版本的工具说明。

- 来源：Anthropic／Claude Code
- 资料类型：存档文档
- 存档版本：`2301018b1f61`。
- 文档时间：源码提交（存档版）：[2025-11-24](<https://github.com/anthropics/claude-code/commit/a194a9d41b7384879fe88368d67367d7ae1a59e7>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/anthropics/claude-code/blob/main/plugins/claude-opus-4-5-migration/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude Code/Opus 4.5 迁移插件.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Claude Opus 4.5 迁移插件

将代码和提示从 Sonnet 4.x 和 Opus 4.1 迁移到 Opus 4.5。

## 概述

此技能会更新您的代码并提示与 Opus 4.5 兼容。它自动执行迁移过程，处理模型字符串、测试标头和其他配置详细信息。如果您在迁移后遇到 Opus 4.5 的任何问题，您可以继续使用此技能来调整提示。

## 用途

```
"Migrate my codebase to Opus 4.5"
```

## 了解更多

请参阅我们的 [ 提示指南 ](<https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-4-best-practices>)，了解提示 Claude 模型的最佳实践。

## 作者

胡威廉 (whu@anthropic.com)


## 原文对照

<details>
<summary>展开完整原文</summary>

# Claude Opus 4.5 Migration Plugin

Migrate your code and prompts from Sonnet 4.x and Opus 4.1 to Opus 4.5.

## Overview

This skill updates your code and prompts to be compatible with Opus 4.5. It automates the migration process, handling model strings, beta headers, and other configuration details. If you run into any issues with Opus 4.5 after migration, you can continue using this skill to adjust your prompts.

## Usage

```
"Migrate my codebase to Opus 4.5"
```

## Learn More

Refer to our [prompting guide](<https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-4-best-practices>) for best practices on prompting Claude models.

## Authors

William Hu (whu@anthropic.com)


</details>
