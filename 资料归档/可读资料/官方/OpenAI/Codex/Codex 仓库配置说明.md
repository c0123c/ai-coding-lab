# Codex 仓库配置说明

提供基本与高级配置的文档路径及生命周期 Hook 相关信息，主要用于定位具体设置参考。

- 来源：OpenAI／Codex
- 资料类型：存档文档
- 文档时间：源码提交（存档版）：[2026-05-13](<https://github.com/openai/codex/commit/913aad4d3c1c8a1671b3ec265e103efc0317308f>)；发表/更新：官方未公开；本地抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://raw.githubusercontent.com/openai/codex/main/docs/config.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Codex/Codex 仓库配置说明.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 配置

有关基本配置说明，请参阅[此文档](<https://developers.openai.com/codex/config-basic>)。

有关高级配置说明，请参阅[此文档](<https://developers.openai.com/codex/config-advanced>)。

有关完整配置参考，请参阅[此文档](<https://developers.openai.com/codex/config-reference>)。

## 生命周期挂钩

管理员可以在 
`requirements.toml` 中设置顶级 `allow_managed_hooks_only = true` 以忽略用户、项目和会话挂钩配置，同时 
 仍然允许来自需求和托管配置层的托管挂钩。此
设置仅在`requirements.toml`中支持；将其置于 `config.toml`
 不会启用仅托管挂钩模式。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Configuration

For basic configuration instructions, see [this documentation](<https://developers.openai.com/codex/config-basic>).

For advanced configuration instructions, see [this documentation](<https://developers.openai.com/codex/config-advanced>).

For a full configuration reference, see [this documentation](<https://developers.openai.com/codex/config-reference>).

## Lifecycle hooks

Admins can set top-level `allow_managed_hooks_only = true` in
`requirements.toml` to ignore user, project, and session hook configs while
still allowing managed hooks from requirements and managed config layers. This
setting is only supported in `requirements.toml`; putting it in `config.toml`
does not enable managed-hooks-only mode.


</details>
