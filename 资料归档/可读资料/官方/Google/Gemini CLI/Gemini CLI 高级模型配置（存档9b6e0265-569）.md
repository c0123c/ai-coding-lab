# Gemini CLI 高级模型配置

介绍模型别名、覆盖项与配置解析优先级，说明高级场景中的模型选择规则和完整配置字段。

- 来源：Google／Gemini CLI
- 资料类型：存档文档
- 存档版本：`9b6e0265d16b`。
- 文档时间：源码提交（存档版）：[2026-04-10](<https://github.com/google-gemini/gemini-cli/commit/e09410b6e5b7c56222c6583e8f028a35d623c764>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/google-gemini/gemini-cli/blob/main/docs/cli/generation-settings.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Gemini CLI/docs/cli/generation-settings.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 高级模型配置

本指南详细介绍了 Gemini CLI 中的模型配置系统。该系统专为研究人员、AI 质量工程师和高级用户设计
，提供
严格的框架来管理生成模型超参数和
行为。

<!-- prettier-ignore -->
> [！WARNING]
> 这是高级用户功能。配置值通过最少的验证直接传递给模型提供者
> 。不正确的设置
> （例如，不兼容的参数组合）可能会导致API出现运行时
> 错误。

## 1。系统概述

模型配置系统 (`ModelConfigService`) 可实现对模型生成的确定性 
 控制。它将请求的模型标识符（例如，
，CLI 标志或 agent 请求）与底层 API 配置分离。
这允许：

- **精确超参数调整**：直接控制`temperature`、`topP`、
`thinkingBudget`，以及其他SDK-级别参数。
- **环境特定行为**：针对不同的环境有不同的配置
操作环境（例如，测试与生产）。
- **Agent-范围自定义**：仅当
特别的 agent 处于活动状态。

该系统在两个核心原语上运行：**别名**和**覆盖**。

## 2。配置原语

这些设置位于配置 
 文件中的 `modelConfigs` 键下。

### 别名 (`customAliases`)

别名是已命名的、可重用的配置预设。用户应在 `customAliases` 映射中定义自己的 
 别名（或覆盖系统默认值）。

- **继承**：一个别名可以`extends`另一个别名（包括系统
默认如`chat-base`），继承其`modelConfig`。子别名可以
覆盖或增加继承的设置。
- **抽象别名**：不需要别名来指定具体的`model`
如果它纯粹用作其他别名的基础。

**示例层次结构**：

```json
"modelConfigs": {
  "customAliases": {
    "base": {
      "modelConfig": {
        "generateContentConfig": { "temperature": 0.0 }
      }
    },
    "chat-base": {
      "extends": "base",
      "modelConfig": {
        "generateContentConfig": { "temperature": 0.7 }
      }
    }
  }
}
```

### 覆盖（`overrides`）

覆盖是基于运行时
上下文注入配置的条件规则。针对每个模型请求动态评估它们。

- **匹配条件**：当请求上下文与
指定 `match` 属性。
  - `model`：匹配请求的型号名称或别名。
  - `overrideScope`：匹配请求的不同范围（通常是
agent名称，例如`codebaseInvestigator`）。

**覆盖示例**：

```json
"modelConfigs": {
  "overrides": [
    {
      "match": {
        "overrideScope": "codebaseInvestigator"
      },
      "modelConfig": {
        "generateContentConfig": { "temperature": 0.1 }
      }
    }
  ]
}
```

## 3。解决策略

`ModelConfigService`通过两步
过程解析最终配置：

### 步骤 1：别名解析

在系统`aliases`
和用户`customAliases`的合并映射中查找请求的模型字符串。

1.  如果找到，系统递归解析`extends`链。
2.  设置从父级合并到子级（子级获胜）。
3.  这会产生一个基数 `ResolvedModelConfig`。
4.  如果未找到，则请求的字符串将被视为原始模型名称。

### 步骤 2：覆盖应用程序

系统根据请求上下文（`model`
 和 `overrideScope`）评估 `overrides` 列表。

1.  **过滤**：识别所有匹配的覆盖。
2.  **排序**：匹配按**特异性**优先（
`match` 对象中匹配的键）。
    - 特定匹配（例如，`model` + `overrideScope`）覆盖广泛匹配
匹配（例如，仅限 `model`）。
    - 打破平局：如果特异性相同，则定义中的顺序
`overrides` 数组被保留（最后一个获胜）。
3.  **合并**：合并排序覆盖中的配置
按顺序到基本配置。

## 4。配置参考

配置遵循`ModelConfigServiceConfig`界面。

### `ModelConfig` 对象

定义模型的实际参数。

|财产|类型|描述|
| :---------------------- | :------- | :------------------------------------------------------------------------ |
| `model`                 | `string` |要调用的模型的标识符（例如，`gemini-2.5-pro`）。|
| `generateContentConfig` | `object` |传递给`@google/genai`的配置对象SDK。|

### `GenerateContentConfig`（常用参数）

直接映射到SDK的`GenerateContentConfig`。常用参数包括：

- **`temperature`**: (`number`) 控制输出随机性。较低的值 (0.0)
是确定性的；较高的值 (>0.7) 具有创意。
- **`topP`**: (`number`) 核采样概率。
- **`maxOutputTokens`**: (`number`) 生成的响应长度限制。
- **`thinkingConfig`**: (`object`) 带推理模型的配置
功能（例如，`thinkingBudget`、`includeThoughts`）。

## 5。实际例子

### 定义确定性基线

为需要高精度的任务创建别名，扩展标准聊天
配置但强制执行零温度。

```json
"modelConfigs": {
  "customAliases": {
    "precise-mode": {
      "extends": "chat-base",
      "modelConfig": {
        "generateContentConfig": {
          "temperature": 0.0,
          "topP": 1.0
        }
      }
    }
  }
}
```

### Agent-具体参数注入

对特定 agent 实施扩展思维预算，而不更改 
 全局默认值，例如 `codebaseInvestigator`。

```json
"modelConfigs": {
  "overrides": [
    {
      "match": {
        "overrideScope": "codebaseInvestigator"
      },
      "modelConfig": {
        "generateContentConfig": {
          "thinkingConfig": { "thinkingBudget": 4096 }
        }
      }
    }
  ]
}
```

### 实验模型评估

将特定别名的流量路由到预览模型以进行 A/B 测试，而无需 
 更改客户端代码。

```json
"modelConfigs": {
  "overrides": [
    {
      "match": {
        "model": "gemini-2.5-pro"
      },
      "modelConfig": {
        "model": "gemini-2.5-pro-experimental-001"
      }
    }
  ]
}
```


## 原文对照

<details>
<summary>展开完整原文</summary>

# Advanced Model Configuration

This guide details the Model Configuration system within Gemini CLI. Designed
for researchers, AI quality engineers, and advanced users, this system provides
a rigorous framework for managing generative model hyperparameters and
behaviors.

<!-- prettier-ignore -->
> [!WARNING]
> This is a power-user feature. Configuration values are passed
> directly to the model provider with minimal validation. Incorrect settings
> (for example, incompatible parameter combinations) may result in runtime
> errors from the API.

## 1. System Overview

The Model Configuration system (`ModelConfigService`) enables deterministic
control over model generation. It decouples the requested model identifier (for
example, a CLI flag or agent request) from the underlying API configuration.
This allows for:

- **Precise Hyperparameter Tuning**: Direct control over `temperature`, `topP`,
  `thinkingBudget`, and other SDK-level parameters.
- **Environment-Specific Behavior**: Distinct configurations for different
  operating contexts (for example, testing vs. production).
- **Agent-Scoped Customization**: Applying specific settings only when a
  particular agent is active.

The system operates on two core primitives: **Aliases** and **Overrides**.

## 2. Configuration Primitives

These settings are located under the `modelConfigs` key in your configuration
file.

### Aliases (`customAliases`)

Aliases are named, reusable configuration presets. Users should define their own
aliases (or override system defaults) in the `customAliases` map.

- **Inheritance**: An alias can `extends` another alias (including system
  defaults like `chat-base`), inheriting its `modelConfig`. Child aliases can
  overwrite or augment inherited settings.
- **Abstract Aliases**: An alias is not required to specify a concrete `model`
  if it serves purely as a base for other aliases.

**Example Hierarchy**:

```json
"modelConfigs": {
  "customAliases": {
    "base": {
      "modelConfig": {
        "generateContentConfig": { "temperature": 0.0 }
      }
    },
    "chat-base": {
      "extends": "base",
      "modelConfig": {
        "generateContentConfig": { "temperature": 0.7 }
      }
    }
  }
}
```

### Overrides (`overrides`)

Overrides are conditional rules that inject configuration based on the runtime
context. They are evaluated dynamically for each model request.

- **Match Criteria**: Overrides apply when the request context matches the
  specified `match` properties.
  - `model`: Matches the requested model name or alias.
  - `overrideScope`: Matches the distinct scope of the request (typically the
    agent name, for example, `codebaseInvestigator`).

**Example Override**:

```json
"modelConfigs": {
  "overrides": [
    {
      "match": {
        "overrideScope": "codebaseInvestigator"
      },
      "modelConfig": {
        "generateContentConfig": { "temperature": 0.1 }
      }
    }
  ]
}
```

## 3. Resolution Strategy

The `ModelConfigService` resolves the final configuration through a two-step
process:

### Step 1: Alias Resolution

The requested model string is looked up in the merged map of system `aliases`
and user `customAliases`.

1.  If found, the system recursively resolves the `extends` chain.
2.  Settings are merged from parent to child (child wins).
3.  This results in a base `ResolvedModelConfig`.
4.  If not found, the requested string is treated as the raw model name.

### Step 2: Override Application

The system evaluates the `overrides` list against the request context (`model`
and `overrideScope`).

1.  **Filtering**: All matching overrides are identified.
2.  **Sorting**: Matches are prioritized by **specificity** (the number of
    matched keys in the `match` object).
    - Specific matches (for example, `model` + `overrideScope`) override broad
      matches (for example, `model` only).
    - Tie-breaking: If specificity is equal, the order of definition in the
      `overrides` array is preserved (last one wins).
3.  **Merging**: The configurations from the sorted overrides are merged
    sequentially onto the base configuration.

## 4. Configuration Reference

The configuration follows the `ModelConfigServiceConfig` interface.

### `ModelConfig` Object

Defines the actual parameters for the model.

| Property                | Type     | Description                                                               |
| :---------------------- | :------- | :------------------------------------------------------------------------ |
| `model`                 | `string` | The identifier of the model to be called (for example, `gemini-2.5-pro`). |
| `generateContentConfig` | `object` | The configuration object passed to the `@google/genai` SDK.               |

### `GenerateContentConfig` (Common Parameters)

Directly maps to the SDK's `GenerateContentConfig`. Common parameters include:

- **`temperature`**: (`number`) Controls output randomness. Lower values (0.0)
  are deterministic; higher values (>0.7) are creative.
- **`topP`**: (`number`) Nucleus sampling probability.
- **`maxOutputTokens`**: (`number`) Limit on generated response length.
- **`thinkingConfig`**: (`object`) Configuration for models with reasoning
  capabilities (for example, `thinkingBudget`, `includeThoughts`).

## 5. Practical Examples

### Defining a Deterministic Baseline

Create an alias for tasks requiring high precision, extending the standard chat
configuration but enforcing zero temperature.

```json
"modelConfigs": {
  "customAliases": {
    "precise-mode": {
      "extends": "chat-base",
      "modelConfig": {
        "generateContentConfig": {
          "temperature": 0.0,
          "topP": 1.0
        }
      }
    }
  }
}
```

### Agent-Specific Parameter Injection

Enforce extended thinking budgets for a specific agent without altering the
global default, for example for the `codebaseInvestigator`.

```json
"modelConfigs": {
  "overrides": [
    {
      "match": {
        "overrideScope": "codebaseInvestigator"
      },
      "modelConfig": {
        "generateContentConfig": {
          "thinkingConfig": { "thinkingBudget": 4096 }
        }
      }
    }
  ]
}
```

### Experimental Model Evaluation

Route traffic for a specific alias to a preview model for A/B testing, without
changing client code.

```json
"modelConfigs": {
  "overrides": [
    {
      "match": {
        "model": "gemini-2.5-pro"
      },
      "modelConfig": {
        "model": "gemini-2.5-pro-experimental-001"
      }
    }
  ]
}
```


</details>
