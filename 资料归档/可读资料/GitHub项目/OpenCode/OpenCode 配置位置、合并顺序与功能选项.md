# OpenCode 配置位置、合并顺序与功能选项

配置参考。解释 JSON/JSONC、远端、全局、项目和管理设置的优先级，列出模型、Agent、权限、上下文压缩、LSP、MCP、插件与指令等配置。

- 来源：OpenCode
- 资料类型：配置参考
- 存档版本：`055d95bb7e27`。
- 文档时间：源码提交（存档版）：[2026-09-02](<https://github.com/anomalyco/opencode/commit/b04697366f05419e9bd7a92f841813dd976161c9>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：适用于所存版本，不将旧配置格式与后续版本混用。

[原文发布页](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/config.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenCode/OpenCode 配置位置、合并顺序与功能选项.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Config
description: Using the OpenCode JSON config.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

您可以使用 JSON 配置文件配置 OpenCode。

---

## 格式

OpenCode 支持 **JSON** 和 **JSONC**（JSON 带注释）格式。

**代码示例：** `opencode.jsonc`

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "model": "anthropic/claude-sonnet-4-5",
  "autoupdate": true,
  "server": {
    "port": 4096,
  },
}
```

---

## 地点

您可以将配置放置在几个不同的位置，它们具有
不同的优先顺序。

**注释：**

配置文件**合并在一起**，而不是替换。

配置文件被合并在一起，而不是被替换。以下配置位置的设置被合并。仅当密钥冲突时，后面的配置才会覆盖前面的配置。保留所有配置中的非冲突设置。

例如，如果您的全局配置集 `autoupdate: true` 和项目配置集 `model: "anthropic/claude-sonnet-4-5"`，则最终配置将包括这两个设置。

---

### 优先顺序

配置源按此顺序加载（后面的源覆盖前面的源）：

1. **远程配置**（来自`.well-known/opencode`）-组织默认值
2. **全局配置** (`~/.config/opencode/opencode.json`) - 用户首选项
3. **自定义配置**（`OPENCODE_CONFIG`环境变量）-自定义覆盖
4. **项目配置**（项目中的`opencode.json`）-项目特定设置
5. **`.opencode` 目录** - agents、命令、插件
6. **内联配置**（`OPENCODE_CONFIG_CONTENT`环境变量）-运行时覆盖
7. **托管配置文件**（macOS上的`/Library/Application Support/opencode/`） - 管理员控制
8. **macOS 管理首选项**（`.mobileconfig` 通过 MDM） - 最高优先级，用户不可覆盖

这意味着项目配置可以覆盖全局默认值，全局配置可以覆盖远程组织默认值。托管设置覆盖一切。

**注释：**

`.opencode`和`~/.config/opencode`目录使用**复数名称**作为子目录：`agents/`，`commands/`，`modes/`，`plugins/`， `skills/`、`tools/` 和 `themes/`。为了向后兼容，还支持单数名称（例如，`agent/`）。

---

### 远程

组织可以通过 `.well-known/opencode` 端点提供默认配置。当您向支持它的提供商进行身份验证时，会自动获取该信息。

首先加载远程配置，作为基础层。所有其他配置源（全局、项目）都可以覆盖这些默认值。

例如，如果您的组织提供默认禁用的 MCP 服务器：

**代码示例：** `Remote config from .well-known/opencode`

```json
{
  "mcp": {
    "jira": {
      "type": "remote",
      "url": "https://jira.example.com/mcp",
      "enabled": false
    }
  }
}
```

您可以在本地配置中启用特定服务器：

**代码示例：** `opencode.json`

```json
{
  "mcp": {
    "jira": {
      "type": "remote",
      "url": "https://jira.example.com/mcp",
      "enabled": true
    }
  }
}
```

---

### 全球

将您的全局 OpenCode 配置放在 `~/.config/opencode/opencode.json` 中。使用全局配置来实现用户范围的 server/runtime 首选项，例如提供程序、模型和权限。

对于 TUI- 特定设置，请使用 `~/.config/opencode/tui.json`。

全局配置覆盖远程组织默认值。

---

### 每个项目

在项目根目录中添加 `opencode.json`。项目配置在标准配置文件中具有最高优先级 - 它覆盖全局配置和远程配置。

对于特定于项目的 TUI 设置，请在其旁边添加 `tui.json`。

**提示：**

将项目特定配置放在项目的根目录中。

当OpenCode启动时，它首先在当前目录中查找配置文件，然后遍历到最近的Git目录。

这也可以安全地签入 Git 并使用与全局模式相同的模式。

---

### 自定义路径

使用 `OPENCODE_CONFIG` 环境变量指定自定义配置文件路径。

```bash
export OPENCODE_CONFIG=/path/to/my/custom-config.json
opencode run "Hello world"
```

自定义配置按优先顺序在全局配置和项目配置之间加载。

---

### 自定义目录

使用 `OPENCODE_CONFIG_DIR`
 环境变量指定自定义配置目录。该目录将像标准的 `.opencode` 目录一样搜索 agents、命令、
 模式和插件，并且应该 
 遵循相同的结构。

```bash
export OPENCODE_CONFIG_DIR=/path/to/my/config-directory
opencode run "Hello world"
```

自定义目录在全局配置和`.opencode`目录之后加载，因此**可以覆盖**它们的设置。

---

### 托管设置

组织可以强制执行用户无法覆盖的配置。托管设置在最高优先级加载。

#### 基于文件

将 `opencode.json` 或 `opencode.jsonc` 文件拖放到系统管理的配置目录中：

|平台|路径|
| -------- | ---------------------------------------- |
| macOS    | `/Library/Application Support/opencode/` |
|Linux| `/etc/opencode/`                         |
|窗户| `%ProgramData%\opencode`                 |

这些目录需要 admin/root 访问权限才能写入，因此用户无法修改它们。

#### macOS 托管首选项

在 macOS 上，OpenCode 从 `ai.opencode.managed` 首选项域读取托管首选项。通过 MDM（Jamf、Kandji、FleetDM）部署 `.mobileconfig`，设置会自动强制执行。

OpenCode 检查这些路径：

1. `/Library/Managed Preferences/<user>/ai.opencode.managed.plist`
2. `/Library/Managed Preferences/ai.opencode.managed.plist`

plist 键直接映射到 `opencode.json` 字段。 MDM 元数据键（`PayloadUUID`、`PayloadType` 等）将被自动删除。

**创建一个`.mobileconfig`**

使用`ai.opencode.managed`PayloadType。 OpenCode 配置键直接进入有效负载字典中：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN"
  "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>PayloadContent</key>
  <array>
    <dict>
      <key>PayloadType</key>
      <string>ai.opencode.managed</string>
      <key>PayloadIdentifier</key>
      <string>com.example.opencode.config</string>
      <key>PayloadUUID</key>
      <string>GENERATE-YOUR-OWN-UUID</string>
      <key>PayloadVersion</key>
      <integer>1</integer>
      <key>share</key>
      <string>disabled</string>
      <key>server</key>
      <dict>
        <key>hostname</key>
        <string>127.0.0.1</string>
      </dict>
      <key>permission</key>
      <dict>
        <key>*</key>
        <string>ask</string>
        <key>bash</key>
        <dict>
          <key>*</key>
          <string>ask</string>
          <key>rm -rf *</key>
          <string>deny</string>
        </dict>
      </dict>
    </dict>
  </array>
  <key>PayloadType</key>
  <string>Configuration</string>
  <key>PayloadIdentifier</key>
  <string>com.example.opencode</string>
  <key>PayloadUUID</key>
  <string>GENERATE-YOUR-OWN-UUID</string>
  <key>PayloadVersion</key>
  <integer>1</integer>
</dict>
</plist>
```

使用 `uuidgen` 生成唯一的 UUID。自定义设置以满足您组织的要求。

**通过MDM**部署

- **Jamf Pro：** 计算机 > 配置文件 > 上传 > 目标设备或智能组的范围
- **FleetDM:** 将 `.mobileconfig` 添加到 `mdm.macos_settings.custom_settings` 下的 gitops 存储库中并运行 `fleetctl apply`

**在设备上验证**

双击`.mobileconfig`本地安装进行测试（显示在系统设置 > 隐私和安全 > 配置文件中），然后运行：

```bash
opencode debug config
```

所有托管首选项键都显示在已解析的配置中，并且不能被用户或项目配置覆盖。

---

## 模式

server/runtime 配置模式在 [**`opencode.ai/config.json`**](<https://opencode.ai/config.json>) 中定义。

TUI 配置使用 [**`opencode.ai/tui.json`**](<https://opencode.ai/tui.json>)。

您的编辑器应该能够根据架构进行验证和自动完成。

---

### TUI

使用专用的 `tui.json`（或 `tui.jsonc`）文件进行 TUI- 特定设置。

**代码示例：** `tui.json`

```json
{
  "$schema": "https://opencode.ai/tui.json",
  "scroll_speed": 3,
  "scroll_acceleration": {
    "enabled": true
  },
  "diff_style": "auto",
  "cursor": {
    "style": "block",
    "blinking": true
  },
  "mouse": true,
  "attention": {
    "enabled": true,
    "notifications": true,
    "sound": true,
    "volume": 0.4
  }
}
```

使用 `OPENCODE_TUI_CONFIG` 指向自定义 TUI 配置文件。

当`cursor.style`为`"default"`时，恢复终端默认的cursor，因此`cursor.blinking`不起作用。

设置`attention.enabled`以打开TUI桌面通知和声音。参见[TUI关注](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 终端界面的上下文、命令与撤销.md#attention>)。

`opencode.json` 中的旧版 `theme`、`keybinds` 和 `tui` 键已弃用，并在可能的情况下自动迁移。

---

### 服务器

您可以通过 `server` 选项配置 `opencode serve` 和 `opencode web` 命令的服务器设置。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "server": {
    "port": 4096,
    "hostname": "0.0.0.0",
    "mdns": true,
    "mdnsDomain": "myproject.local",
    "cors": ["http://localhost:5173"]
  }
}
```

可用选项：

- `port` - 监听的端口。
- `hostname` - 要侦听的主机名。当启用`mdns`且未设置主机名时，默认为`0.0.0.0`。
- `mdns` - 启用 mDNS 服务发现。这允许网络上的其他设备发现您的 OpenCode 服务器。
- `mdnsDomain` - mDNS 服务的自定义域名。默认为 `opencode.local`。对于在同一网络上运行多个实例很有用。
- `cors` - 从基于浏览器的客户端使用 HTTP 服务器时允许 CORS 的其他来源。值必须是完整来源（方案 + 主机 + 可选端口），例如 `https://app.example.com`。

[在此处了解有关服务器的更多信息](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode HTTP 服务、认证与 API.md>)。

---

### 壳牌

您可以使用 `shell` 选项配置用于交互式终端的 shell。兼容的 shell 也用于 agent 工具调用。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "shell": "pwsh"
}
```

如果未指定，OpenCode 将根据您的操作系统自动发现并使用合理的默认值（例如，Windows 上的 `pwsh` 或 `cmd.exe`，macOS/Linux 上的 `/bin/zsh` 或 `/bin/bash`）。您可以提供绝对路径或短名称。

---

### 工具

您可以通过`tools`选项管理LLM可以使用的工具。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "tools": {
    "write": false,
    "bash": false
  }
}
```

[在此处了解有关工具的更多信息](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 内置、自定义与 MCP 工具.md>)。

---

### 型号

您可以通过 `provider`、`model` 和 `small_model` 选项来配置要在 OpenCode 配置中使用的提供商和模型。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {},
  "model": "anthropic/claude-sonnet-4-5",
  "small_model": "anthropic/claude-haiku-4-5"
}
```

`small_model` 选项为标题生成等轻量级任务配置单独的模型。默认情况下，如果您的提供商提供了更便宜的型号，OpenCode 会尝试使用更便宜的型号，否则它将回退到您的主要型号。

提供程序选项可以包括 `timeout`、`headerTimeout`、`chunkTimeout` 和 `setCacheKey`：

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "anthropic": {
      "options": {
        "timeout": 600000,
        "chunkTimeout": 30000,
        "setCacheKey": true
      }
    }
  }
}
```

- `timeout` - 请求超时（以毫秒为单位）（默认值：300000）。设置为 `false` 以禁用。
- `headerTimeout` - 等待响应标头的超时时间（以毫秒为单位）（默认值：300000 或 5 分钟）。一旦标头到达，此计时器就会停止，并且不会限制流式响应正文。设置为 `false` 以禁用。
- `chunkTimeout` - 流式响应块之间的超时（以毫秒为单位）（默认值：300000 或 5 分钟）。如果没有块及时到达，则请求将中止。设置为 `false` 以禁用。
- `setCacheKey` - 确保始终为指定的提供程序设置缓存密钥。

您还可以配置[本地型号](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 模型选择、默认值与变体.md#local>)。 [了解更多](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 模型选择、默认值与变体.md>)。

---

### 政策

使用 `experimental.policies` 选项允许或拒绝对已配置资源执行 OpenCode 操作。目前，策略可以控制 OpenCode 可以使用哪些提供商。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "experimental": {
    "policies": [
      {
        "effect": "deny",
        "action": "provider.use",
        "resource": "openai"
      }
    ]
  }
}
```

[在此处了解有关政策的更多信息](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 实验性资源访问策略.md>)。

---

### 图片附件

OpenCode 在将图像附件发送到模型之前对其进行标准化。默认情况下，图像在超过 `2000x2000` 像素或 `5242880` base64 字节时会调整大小。

使用 `attachment.image` 选项配置图像附件限制：

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "attachment": {
    "image": {
      "auto_resize": true,
      "max_width": 2000,
      "max_height": 2000,
      "max_base64_bytes": 5242880
    }
  }
}
```

- `auto_resize` - 在提供商请求之前调整超出配置限制的图像大小。设置为 `false` 可拒绝过大的图像。
- `max_width` - 调整大小或拒绝之前的最大图像宽度（以像素为单位）。
- `max_height` - 调整大小或拒绝之前的最大图像高度（以像素为单位）。
- `max_base64_bytes` - 最大编码图像有效负载大小。这是 Base64 有效负载大小，而不是原始文件大小。

如果调整大小后图像仍然无法容纳，OpenCode 会忽略过大的工具结果图像，或因图像尺寸错误而无法处理过大的用户提供的图像。

---

#### 特定于提供商的选项

某些提供商支持通用 `timeout` 和 `apiKey` 设置之外的其他配置选项。

##### 亚马逊基岩

Amazon Bedrock 支持 AWS- 特定配置：

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "amazon-bedrock": {
      "options": {
        "region": "us-east-1",
        "profile": "my-aws-profile",
        "endpoint": "https://bedrock-runtime.us-east-1.vpce-xxxxx.amazonaws.com"
      }
    }
  }
}
```

- `region` - 基岩的 AWS 区域（默认为 `AWS_REGION` env var 或 `us-east-1`）
- `profile` - 来自 `~/.aws/credentials` 的 AWS 命名配置文件（默认为 `AWS_PROFILE` 环境变量）
- `endpoint` - VPC 端点的自定义端点 URL。这是使用 AWS- 特定术语的通用 `baseURL` 选项的别名。如果两者都指定，则 `endpoint` 优先。

**注释：**

承载令牌（`AWS_BEARER_TOKEN_BEDROCK` 或 `/connect`）优先于基于配置文件的身份验证。详情请参见[认证优先级](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 云端与本地模型服务接入.md#authentication-precedence>)。

[了解有关 Amazon Bedrock 配置的更多信息](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 云端与本地模型服务接入.md#amazon-bedrock>)。

---

### 主题

在`tui.json`中设置您的UI主题。

**代码示例：** `tui.json`

```json
{
  "$schema": "https://opencode.ai/tui.json",
  "theme": "tokyonight"
}
```

[在此处了解更多信息](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 终端主题与显示配置.md>)。

---

### Agents

您可以通过`agent`选项为特定任务配置专门的agents。

**代码示例：** `opencode.jsonc`

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "agent": {
    "code-reviewer": {
      "description": "Reviews code for best practices and potential issues",
      "model": "anthropic/claude-sonnet-4-5",
      "prompt": "You are a code reviewer. Focus on security, performance, and maintainability.",
      "tools": {
        // Disable file modification tools for review-only agent
        "write": false,
        "edit": false,
      },
    },
  },
}
```

您还可以使用 `~/.config/opencode/agents/` 或 `.opencode/agents/` 中的 markdown 文件定义 agents。 [在此处了解更多信息](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 主 Agent、子 Agent 与配置.md>)。

---

### 默认 agent

您可以使用 `default_agent` 选项设置默认 agent。当没有明确指定时，这确定使用哪个agent。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "default_agent": "plan"
}
```

默认 agent 必须是主代理 agent（不是子代理）。这可以是内置的 agent，例如 `"build"` 或 `"plan"`，也可以是您定义的 [ 自定义 agent](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 主 Agent、子 Agent 与配置.md>)。如果指定的 agent 不存在或者是子代理，OpenCode 将回退到 `"build"` 并发出警告。

此设置适用于所有接口：TUI、CLI (`opencode run`)、桌面应用程序和 GitHub 操作。

---

### 子代理深度

您可以使用 `subagent_depth` 选项控制子代理调用其他子代理的深度。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "subagent_depth": 2
}
```

默认值为 `1`，它允许主 agents 启动子代理，但阻止这些子代理启动其他子代理。将其设置为 `2` 以允许额外一层嵌套子代理，或设置为 `0` 以阻止所有子代理启动。

---

### 分享

您可以通过`share`选项配置[共享](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 会话分享、取消与数据边界.md>)功能。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "share": "manual"
}
```

这需要：

- `"manual"` - 允许通过命令手动共享（默认）
- `"auto"` - 自动分享新对话
- `"disabled"` - 完全禁用共享

默认情况下，共享设置为手动模式，您需要使用 `/share` 命令显式共享对话。

---

### 命令

您可以通过`command`选项为重复任务配置自定义命令。

**代码示例：** `opencode.jsonc`

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "command": {
    "test": {
      "template": "Run the full test suite with coverage report and show any failures.\nFocus on the failing tests and suggest fixes.",
      "description": "Run tests with coverage",
      "agent": "build",
      "model": "anthropic/claude-haiku-4-5",
    },
    "component": {
      "template": "Create a new React component named $ARGUMENTS with TypeScript support.\nInclude proper typing and basic structure.",
      "description": "Create a new component",
    },
  },
}
```

您还可以使用 `~/.config/opencode/commands/` 或 `.opencode/commands/` 中的 markdown 文件定义命令。 [在此处了解更多信息](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 自定义命令和提示模板.md>)。

---

### 按键绑定

使用 `keybinds` 自定义 `tui.json` 中的 TUI 键盘快捷键。

**代码示例：** `tui.json`

```json
{
  "$schema": "https://opencode.ai/tui.json",
  "keybinds": {
    "command_list": "ctrl+p"
  }
}
```

`keybinds` 与内置默认值合并，因此您只需配置要更改的快捷方式。

[在此处了解更多信息](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 快捷键与终端输入设置.md>)。

---

### 快照

OpenCode 使用快照来跟踪 agent 操作期间的文件更改，使您能够撤消和恢复会话中的更改。默认情况下启用快照。

对于大型存储库或具有许多子模块的项目，快照系统可能会导致索引缓慢和大量磁盘使用，因为它使用内部 git 存储库跟踪所有更改。您可以使用 `snapshot` 选项禁用快照。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "snapshot": false
}
```

请注意，禁用快照意味着 agent 所做的更改无法通过 UI 回滚。

---

### 自动更新

OpenCode 启动时将自动下载任何新更新。您可以使用 `autoupdate` 选项禁用此功能。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "autoupdate": false
}
```

如果您不想更新但希望在新版本可用时收到通知，请将 `autoupdate` 设置为 `"notify"`。
请注意，这仅在未使用软件包管理器（例如 Homebrew）安装时才有效。

---

### 格式化程序

您可以通过`formatter`选项启用和配置代码格式化程序。省略它可以禁用格式化程序。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "formatter": true
}
```

在配置覆盖或自定义格式化程序时，使用对象来保持内置功能启用。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "formatter": {
    "prettier": {
      "disabled": true
    },
    "custom-prettier": {
      "command": ["npx", "prettier", "--write", "$FILE"],
      "environment": {
        "NODE_ENV": "development"
      },
      "extensions": [".js", ".ts", ".jsx", ".tsx"]
    }
  }
}
```

[在此处了解有关格式化程序的更多信息](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 格式化器启用与定制.md>)。

---

### LSP 服务器

您可以通过`lsp`选项启用和配置LSP服务器。省略它可使 LSP 保持禁用状态。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "lsp": true
}
```

在配置覆盖或自定义 LSP 服务器时，使用对象来保持内置功能启用。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "lsp": {
    "typescript": {
      "disabled": true
    }
  }
}
```

[在此处](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode LSP 诊断反馈与服务器配置.md>)了解有关LSP服务器的更多信息。

---

### 权限

默认情况下，opencode **允许所有操作**，无需明确批准。您可以使用 `permission` 选项更改此设置。

例如，为确保 `edit` 和 `bash` 工具需要用户批准：

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "edit": "ask",
    "bash": "ask"
  }
}
```

[在此处了解有关权限的更多信息](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 工具权限与细粒度匹配.md>)。

---

### 压实

您可以通过 `compaction` 选项控制上下文压缩行为。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "compaction": {
    "auto": true,
    "prune": false,
    "reserved": 10000
  }
}
```

- `auto` - 当上下文已满时自动压缩会话（默认值：`true`）。
- `prune` - 删除旧工具输出以保存令牌（默认值：`false`）。设置为 `true` 以启用修剪。
- `reserved` - 用于压缩的令牌缓冲区。留下足够的窗口以避免压缩期间溢出。

---

### 守望者

您可以通过 `watcher` 选项配置文件观察器忽略模式。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "watcher": {
    "ignore": ["node_modules/**", "dist/**", ".git/**"]
  }
}
```

模式遵循 glob 语法。使用它可以从文件监视中排除嘈杂的目录。

---

### MCP 服务器

您可以通过`mcp`选项配置要使用的MCP服务器。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {}
}
```

[在此处了解更多信息](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode MCP 连接、OAuth 与工具范围.md>)。

---

### 插件

[插件](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 插件加载、事件和上下文扩展.md>)通过自定义工具、挂钩和集成扩展了OpenCode。

将插件文件放在`.opencode/plugins/`或`~/.config/opencode/plugins/`中。您还可以通过`plugin`选项从npm加载插件。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "plugin": ["opencode-helicone-session", "@my-org/custom-plugin"]
}
```

[在此处了解更多信息](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 插件加载、事件和上下文扩展.md>)。

---

### 使用说明

您可以通过`instructions`选项为您所使用的型号配置说明。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "instructions": ["CONTRIBUTING.md", "docs/guidelines.md", ".cursor/rules/*.md"]
}
```

这需要指令文件的路径和全局模式数组。 [了解更多
有关规则的信息](<https://github.com/docs/rules>)。

---

### 残疾服务提供者

您可以通过 `disabled_providers` 选项禁用自动加载的提供程序。当您想要阻止加载某些提供程序（即使其凭据可用）时，这非常有用。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "disabled_providers": ["openai", "gemini"]
}
```

**注释：**

`disabled_providers`优先于`enabled_providers`。

`disabled_providers` 选项接受提供者 ID 数组。当提供者被禁用时：

- 即使设置了环境变量也不会加载。
- 即使通过`/connect`命令配置了API键，也不会加载它。
- 提供商的型号不会出现在型号选择列表中。

---

### 启用的提供商

您可以通过 `enabled_providers` 选项指定提供商的白名单。设置后，仅启用指定的提供程序，所有其他提供程序将被忽略。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "enabled_providers": ["anthropic", "openai"]
}
```

当您想要限制 OpenCode 仅使用特定的提供程序而不是一一禁用它们时，这非常有用。

**注释：**

`disabled_providers`优先于`enabled_providers`。

如果某个提供商同时出现在 `enabled_providers` 和 `disabled_providers` 中，则 `disabled_providers` 优先考虑向后兼容。

---

### 实验性的

`experimental` 密钥包含正在积极开发的选项。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "experimental": {}
}
```

**注意：**

实验选项不稳定。它们可能会更改或被删除，恕不另行通知。

---

## 变量

您可以在配置文件中使用变量替换来引用环境变量和文件内容。

---

### 环境变量

使用`{env:VARIABLE_NAME}`替换环境变量：

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "model": "{env:OPENCODE_MODEL}",
  "provider": {
    "anthropic": {
      "models": {},
      "options": {
        "apiKey": "{env:ANTHROPIC_API_KEY}"
      }
    }
  }
}
```

如果未设置环境变量，它将被替换为空字符串。

---

### 文件

使用 `{file:path/to/file}` 替换文件的内容：

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "instructions": ["./custom-instructions.md"],
  "provider": {
    "openai": {
      "options": {
        "apiKey": "{file:~/.secrets/openai-key}"
      }
    }
  }
}
```

文件路径可以是：

- 相对于配置文件目录
- 或者以 `/` 或 `~` 开头的绝对路径

这些对于：

- 将 API 密钥等敏感数据保存在单独的文件中。
- 包含大型指令文件，而不会弄乱您的配置。
- 跨多个配置文件共享通用配置片段。


## 原文对照

<details>
<summary>展开完整原文</summary>

You can configure OpenCode using a JSON config file.

---

## Format

OpenCode supports both **JSON** and **JSONC** (JSON with Comments) formats.

**代码示例：** `opencode.jsonc`

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "model": "anthropic/claude-sonnet-4-5",
  "autoupdate": true,
  "server": {
    "port": 4096,
  },
}
```

---

## Locations

You can place your config in a couple of different locations and they have a
different order of precedence.

**注释：**

Configuration files are **merged together**, not replaced.

Configuration files are merged together, not replaced. Settings from the following config locations are combined. Later configs override earlier ones only for conflicting keys. Non-conflicting settings from all configs are preserved.

For example, if your global config sets `autoupdate: true` and your project config sets `model: "anthropic/claude-sonnet-4-5"`, the final configuration will include both settings.

---

### Precedence order

Config sources are loaded in this order (later sources override earlier ones):

1. **Remote config** (from `.well-known/opencode`) - organizational defaults
2. **Global config** (`~/.config/opencode/opencode.json`) - user preferences
3. **Custom config** (`OPENCODE_CONFIG` env var) - custom overrides
4. **Project config** (`opencode.json` in project) - project-specific settings
5. **`.opencode` directories** - agents, commands, plugins
6. **Inline config** (`OPENCODE_CONFIG_CONTENT` env var) - runtime overrides
7. **Managed config files** (`/Library/Application Support/opencode/` on macOS) - admin-controlled
8. **macOS managed preferences** (`.mobileconfig` via MDM) - highest priority, not user-overridable

This means project configs can override global defaults, and global configs can override remote organizational defaults. Managed settings override everything.

**注释：**

The `.opencode` and `~/.config/opencode` directories use **plural names** for subdirectories: `agents/`, `commands/`, `modes/`, `plugins/`, `skills/`, `tools/`, and `themes/`. Singular names (e.g., `agent/`) are also supported for backwards compatibility.

---

### Remote

Organizations can provide default configuration via the `.well-known/opencode` endpoint. This is fetched automatically when you authenticate with a provider that supports it.

Remote config is loaded first, serving as the base layer. All other config sources (global, project) can override these defaults.

For example, if your organization provides MCP servers that are disabled by default:

**代码示例：** `Remote config from .well-known/opencode`

```json
{
  "mcp": {
    "jira": {
      "type": "remote",
      "url": "https://jira.example.com/mcp",
      "enabled": false
    }
  }
}
```

You can enable specific servers in your local config:

**代码示例：** `opencode.json`

```json
{
  "mcp": {
    "jira": {
      "type": "remote",
      "url": "https://jira.example.com/mcp",
      "enabled": true
    }
  }
}
```

---

### Global

Place your global OpenCode config in `~/.config/opencode/opencode.json`. Use global config for user-wide server/runtime preferences like providers, models, and permissions.

For TUI-specific settings, use `~/.config/opencode/tui.json`.

Global config overrides remote organizational defaults.

---

### Per project

Add `opencode.json` in your project root. Project config has the highest precedence among standard config files - it overrides both global and remote configs.

For project-specific TUI settings, add `tui.json` alongside it.

**提示：**

Place project specific config in the root of your project.

When OpenCode starts up, it first looks for a config file in the current directory, then traverses up to the nearest Git directory.

This is also safe to be checked into Git and uses the same schema as the global one.

---

### Custom path

Specify a custom config file path using the `OPENCODE_CONFIG` environment variable.

```bash
export OPENCODE_CONFIG=/path/to/my/custom-config.json
opencode run "Hello world"
```

Custom config is loaded between global and project configs in the precedence order.

---

### Custom directory

Specify a custom config directory using the `OPENCODE_CONFIG_DIR`
environment variable. This directory will be searched for agents, commands,
modes, and plugins just like the standard `.opencode` directory, and should
follow the same structure.

```bash
export OPENCODE_CONFIG_DIR=/path/to/my/config-directory
opencode run "Hello world"
```

The custom directory is loaded after the global config and `.opencode` directories, so it **can override** their settings.

---

### Managed settings

Organizations can enforce configuration that users cannot override. Managed settings are loaded at the highest priority tier.

#### File-based

Drop an `opencode.json` or `opencode.jsonc` file in the system managed config directory:

| Platform | Path                                     |
| -------- | ---------------------------------------- |
| macOS    | `/Library/Application Support/opencode/` |
| Linux    | `/etc/opencode/`                         |
| Windows  | `%ProgramData%\opencode`                 |

These directories require admin/root access to write, so users cannot modify them.

#### macOS managed preferences

On macOS, OpenCode reads managed preferences from the `ai.opencode.managed` preference domain. Deploy a `.mobileconfig` via MDM (Jamf, Kandji, FleetDM) and the settings are enforced automatically.

OpenCode checks these paths:

1. `/Library/Managed Preferences/<user>/ai.opencode.managed.plist`
2. `/Library/Managed Preferences/ai.opencode.managed.plist`

The plist keys map directly to `opencode.json` fields. MDM metadata keys (`PayloadUUID`, `PayloadType`, etc.) are stripped automatically.

**Creating a `.mobileconfig`**

Use the `ai.opencode.managed` PayloadType. The OpenCode config keys go directly in the payload dict:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN"
  "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>PayloadContent</key>
  <array>
    <dict>
      <key>PayloadType</key>
      <string>ai.opencode.managed</string>
      <key>PayloadIdentifier</key>
      <string>com.example.opencode.config</string>
      <key>PayloadUUID</key>
      <string>GENERATE-YOUR-OWN-UUID</string>
      <key>PayloadVersion</key>
      <integer>1</integer>
      <key>share</key>
      <string>disabled</string>
      <key>server</key>
      <dict>
        <key>hostname</key>
        <string>127.0.0.1</string>
      </dict>
      <key>permission</key>
      <dict>
        <key>*</key>
        <string>ask</string>
        <key>bash</key>
        <dict>
          <key>*</key>
          <string>ask</string>
          <key>rm -rf *</key>
          <string>deny</string>
        </dict>
      </dict>
    </dict>
  </array>
  <key>PayloadType</key>
  <string>Configuration</string>
  <key>PayloadIdentifier</key>
  <string>com.example.opencode</string>
  <key>PayloadUUID</key>
  <string>GENERATE-YOUR-OWN-UUID</string>
  <key>PayloadVersion</key>
  <integer>1</integer>
</dict>
</plist>
```

Generate unique UUIDs with `uuidgen`. Customize the settings to match your organization's requirements.

**Deploying via MDM**

- **Jamf Pro:** Computers > Configuration Profiles > Upload > scope to target devices or smart groups
- **FleetDM:** Add the `.mobileconfig` to your gitops repo under `mdm.macos_settings.custom_settings` and run `fleetctl apply`

**Verifying on a device**

Double-click the `.mobileconfig` to install locally for testing (shows in System Settings > Privacy & Security > Profiles), then run:

```bash
opencode debug config
```

All managed preference keys appear in the resolved config and cannot be overridden by user or project configuration.

---

## Schema

The server/runtime config schema is defined in [**`opencode.ai/config.json`**](<https://opencode.ai/config.json>).

TUI config uses [**`opencode.ai/tui.json`**](<https://opencode.ai/tui.json>).

Your editor should be able to validate and autocomplete based on the schema.

---

### TUI

Use a dedicated `tui.json` (or `tui.jsonc`) file for TUI-specific settings.

**代码示例：** `tui.json`

```json
{
  "$schema": "https://opencode.ai/tui.json",
  "scroll_speed": 3,
  "scroll_acceleration": {
    "enabled": true
  },
  "diff_style": "auto",
  "cursor": {
    "style": "block",
    "blinking": true
  },
  "mouse": true,
  "attention": {
    "enabled": true,
    "notifications": true,
    "sound": true,
    "volume": 0.4
  }
}
```

Use `OPENCODE_TUI_CONFIG` to point to a custom TUI config file.

When `cursor.style` is `"default"`, the terminal default cursor is restored, so `cursor.blinking` has no effect.

Set `attention.enabled` to turn on TUI desktop notifications and sounds. See [TUI attention](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 终端界面的上下文、命令与撤销.md#attention>).

Legacy `theme`, `keybinds`, and `tui` keys in `opencode.json` are deprecated and automatically migrated when possible.

---

### Server

You can configure server settings for the `opencode serve` and `opencode web` commands through the `server` option.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "server": {
    "port": 4096,
    "hostname": "0.0.0.0",
    "mdns": true,
    "mdnsDomain": "myproject.local",
    "cors": ["http://localhost:5173"]
  }
}
```

Available options:

- `port` - Port to listen on.
- `hostname` - Hostname to listen on. When `mdns` is enabled and no hostname is set, defaults to `0.0.0.0`.
- `mdns` - Enable mDNS service discovery. This allows other devices on the network to discover your OpenCode server.
- `mdnsDomain` - Custom domain name for mDNS service. Defaults to `opencode.local`. Useful for running multiple instances on the same network.
- `cors` - Additional origins to allow for CORS when using the HTTP server from a browser-based client. Values must be full origins (scheme + host + optional port), eg `https://app.example.com`.

[Learn more about the server here](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode HTTP 服务、认证与 API.md>).

---

### Shell

You can configure the shell used for the interactive terminal using the `shell` option. Compatible shells are also used for agent tool calls.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "shell": "pwsh"
}
```

If not specified, OpenCode will automatically discover and use a sensible default based on your operating system (e.g. `pwsh` or `cmd.exe` on Windows, `/bin/zsh` or `/bin/bash` on macOS/Linux). You can provide an absolute path or a short name.

---

### Tools

You can manage the tools an LLM can use through the `tools` option.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "tools": {
    "write": false,
    "bash": false
  }
}
```

[Learn more about tools here](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 内置、自定义与 MCP 工具.md>).

---

### Models

You can configure the providers and models you want to use in your OpenCode config through the `provider`, `model` and `small_model` options.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {},
  "model": "anthropic/claude-sonnet-4-5",
  "small_model": "anthropic/claude-haiku-4-5"
}
```

The `small_model` option configures a separate model for lightweight tasks like title generation. By default, OpenCode tries to use a cheaper model if one is available from your provider, otherwise it falls back to your main model.

Provider options can include `timeout`, `headerTimeout`, `chunkTimeout`, and `setCacheKey`:

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "anthropic": {
      "options": {
        "timeout": 600000,
        "chunkTimeout": 30000,
        "setCacheKey": true
      }
    }
  }
}
```

- `timeout` - Request timeout in milliseconds (default: 300000). Set to `false` to disable.
- `headerTimeout` - Timeout in milliseconds to wait for response headers (default: 300000, or 5 minutes). This timer stops once headers arrive and does not limit the streamed response body. Set to `false` to disable.
- `chunkTimeout` - Timeout in milliseconds between streamed response chunks (default: 300000, or 5 minutes). If no chunk arrives in time, the request is aborted. Set to `false` to disable.
- `setCacheKey` - Ensure a cache key is always set for designated provider.

You can also configure [local models](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 模型选择、默认值与变体.md#local>). [Learn more](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 模型选择、默认值与变体.md>).

---

### Policies

Use the `experimental.policies` option to allow or deny OpenCode actions on configured resources. Currently, policies can control which providers OpenCode may use.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "experimental": {
    "policies": [
      {
        "effect": "deny",
        "action": "provider.use",
        "resource": "openai"
      }
    ]
  }
}
```

[Learn more about policies here](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 实验性资源访问策略.md>).

---

### Image attachments

OpenCode normalizes image attachments before sending them to the model. By default, images are resized when they exceed `2000x2000` pixels or `5242880` base64 bytes.

Configure image attachment limits with the `attachment.image` option:

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "attachment": {
    "image": {
      "auto_resize": true,
      "max_width": 2000,
      "max_height": 2000,
      "max_base64_bytes": 5242880
    }
  }
}
```

- `auto_resize` - Resize images that exceed the configured limits before provider requests. Set to `false` to reject oversized images instead.
- `max_width` - Maximum image width in pixels before resizing or rejection.
- `max_height` - Maximum image height in pixels before resizing or rejection.
- `max_base64_bytes` - Maximum encoded image payload size. This is the base64 payload size, not the original file size.

If an image still cannot fit after resizing, OpenCode omits oversized tool-result images or fails oversized user-provided images with an image size error.

---

#### Provider-Specific Options

Some providers support additional configuration options beyond the generic `timeout` and `apiKey` settings.

##### Amazon Bedrock

Amazon Bedrock supports AWS-specific configuration:

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "amazon-bedrock": {
      "options": {
        "region": "us-east-1",
        "profile": "my-aws-profile",
        "endpoint": "https://bedrock-runtime.us-east-1.vpce-xxxxx.amazonaws.com"
      }
    }
  }
}
```

- `region` - AWS region for Bedrock (defaults to `AWS_REGION` env var or `us-east-1`)
- `profile` - AWS named profile from `~/.aws/credentials` (defaults to `AWS_PROFILE` env var)
- `endpoint` - Custom endpoint URL for VPC endpoints. This is an alias for the generic `baseURL` option using AWS-specific terminology. If both are specified, `endpoint` takes precedence.

**注释：**

Bearer tokens (`AWS_BEARER_TOKEN_BEDROCK` or `/connect`) take precedence over profile-based authentication. See [authentication precedence](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 云端与本地模型服务接入.md#authentication-precedence>) for details.

[Learn more about Amazon Bedrock configuration](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 云端与本地模型服务接入.md#amazon-bedrock>).

---

### Themes

Set your UI theme in `tui.json`.

**代码示例：** `tui.json`

```json
{
  "$schema": "https://opencode.ai/tui.json",
  "theme": "tokyonight"
}
```

[Learn more here](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 终端主题与显示配置.md>).

---

### Agents

You can configure specialized agents for specific tasks through the `agent` option.

**代码示例：** `opencode.jsonc`

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "agent": {
    "code-reviewer": {
      "description": "Reviews code for best practices and potential issues",
      "model": "anthropic/claude-sonnet-4-5",
      "prompt": "You are a code reviewer. Focus on security, performance, and maintainability.",
      "tools": {
        // Disable file modification tools for review-only agent
        "write": false,
        "edit": false,
      },
    },
  },
}
```

You can also define agents using markdown files in `~/.config/opencode/agents/` or `.opencode/agents/`. [Learn more here](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 主 Agent、子 Agent 与配置.md>).

---

### Default agent

You can set the default agent using the `default_agent` option. This determines which agent is used when none is explicitly specified.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "default_agent": "plan"
}
```

The default agent must be a primary agent (not a subagent). This can be a built-in agent like `"build"` or `"plan"`, or a [custom agent](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 主 Agent、子 Agent 与配置.md>) you've defined. If the specified agent doesn't exist or is a subagent, OpenCode will fall back to `"build"` with a warning.

This setting applies across all interfaces: TUI, CLI (`opencode run`), desktop app, and GitHub Action.

---

### Subagent depth

You can control how deeply subagents can invoke other subagents using the `subagent_depth` option.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "subagent_depth": 2
}
```

The default is `1`, which allows primary agents to launch subagents but prevents those subagents from launching additional subagents. Set it to `2` to allow one additional level of nested subagents, or `0` to prevent all subagent launches.

---

### Sharing

You can configure the [share](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 会话分享、取消与数据边界.md>) feature through the `share` option.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "share": "manual"
}
```

This takes:

- `"manual"` - Allow manual sharing via commands (default)
- `"auto"` - Automatically share new conversations
- `"disabled"` - Disable sharing entirely

By default, sharing is set to manual mode where you need to explicitly share conversations using the `/share` command.

---

### Commands

You can configure custom commands for repetitive tasks through the `command` option.

**代码示例：** `opencode.jsonc`

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "command": {
    "test": {
      "template": "Run the full test suite with coverage report and show any failures.\nFocus on the failing tests and suggest fixes.",
      "description": "Run tests with coverage",
      "agent": "build",
      "model": "anthropic/claude-haiku-4-5",
    },
    "component": {
      "template": "Create a new React component named $ARGUMENTS with TypeScript support.\nInclude proper typing and basic structure.",
      "description": "Create a new component",
    },
  },
}
```

You can also define commands using markdown files in `~/.config/opencode/commands/` or `.opencode/commands/`. [Learn more here](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 自定义命令和提示模板.md>).

---

### Keybinds

Customize TUI keyboard shortcuts in `tui.json` with `keybinds`.

**代码示例：** `tui.json`

```json
{
  "$schema": "https://opencode.ai/tui.json",
  "keybinds": {
    "command_list": "ctrl+p"
  }
}
```

`keybinds` is merged with built-in defaults, so you only need to configure the shortcuts you want to change.

[Learn more here](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 快捷键与终端输入设置.md>).

---

### Snapshot

OpenCode uses snapshots to track file changes during agent operations, enabling you to undo and revert changes within a session. Snapshots are enabled by default.

For large repositories or projects with many submodules, the snapshot system can cause slow indexing and significant disk usage as it tracks all changes using an internal git repository. You can disable snapshots using the `snapshot` option.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "snapshot": false
}
```

Note that disabling snapshots means changes made by the agent cannot be rolled back through the UI.

---

### Autoupdate

OpenCode will automatically download any new updates when it starts up. You can disable this with the `autoupdate` option.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "autoupdate": false
}
```

If you don't want updates but want to be notified when a new version is available, set `autoupdate` to `"notify"`.
Notice that this only works if it was not installed using a package manager such as Homebrew.

---

### Formatters

You can enable and configure code formatters through the `formatter` option. Omit it to keep formatters disabled.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "formatter": true
}
```

Use an object to keep built-ins enabled while configuring overrides or custom formatters.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "formatter": {
    "prettier": {
      "disabled": true
    },
    "custom-prettier": {
      "command": ["npx", "prettier", "--write", "$FILE"],
      "environment": {
        "NODE_ENV": "development"
      },
      "extensions": [".js", ".ts", ".jsx", ".tsx"]
    }
  }
}
```

[Learn more about formatters here](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 格式化器启用与定制.md>).

---

### LSP Servers

You can enable and configure LSP servers through the `lsp` option. Omit it to keep LSP disabled.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "lsp": true
}
```

Use an object to keep built-ins enabled while configuring overrides or custom LSP servers.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "lsp": {
    "typescript": {
      "disabled": true
    }
  }
}
```

[Learn more about LSP servers here](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode LSP 诊断反馈与服务器配置.md>).

---

### Permissions

By default, opencode **allows all operations** without requiring explicit approval. You can change this using the `permission` option.

For example, to ensure that the `edit` and `bash` tools require user approval:

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "edit": "ask",
    "bash": "ask"
  }
}
```

[Learn more about permissions here](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 工具权限与细粒度匹配.md>).

---

### Compaction

You can control context compaction behavior through the `compaction` option.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "compaction": {
    "auto": true,
    "prune": false,
    "reserved": 10000
  }
}
```

- `auto` - Automatically compact the session when context is full (default: `true`).
- `prune` - Remove old tool outputs to save tokens (default: `false`). Set to `true` to enable pruning.
- `reserved` - Token buffer for compaction. Leaves enough window to avoid overflow during compaction.

---

### Watcher

You can configure file watcher ignore patterns through the `watcher` option.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "watcher": {
    "ignore": ["node_modules/**", "dist/**", ".git/**"]
  }
}
```

Patterns follow glob syntax. Use this to exclude noisy directories from file watching.

---

### MCP servers

You can configure MCP servers you want to use through the `mcp` option.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {}
}
```

[Learn more here](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode MCP 连接、OAuth 与工具范围.md>).

---

### Plugins

[Plugins](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 插件加载、事件和上下文扩展.md>) extend OpenCode with custom tools, hooks, and integrations.

Place plugin files in `.opencode/plugins/` or `~/.config/opencode/plugins/`. You can also load plugins from npm through the `plugin` option.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "plugin": ["opencode-helicone-session", "@my-org/custom-plugin"]
}
```

[Learn more here](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 插件加载、事件和上下文扩展.md>).

---

### Instructions

You can configure the instructions for the model you're using through the `instructions` option.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "instructions": ["CONTRIBUTING.md", "docs/guidelines.md", ".cursor/rules/*.md"]
}
```

This takes an array of paths and glob patterns to instruction files. [Learn more
about rules here](<https://github.com/docs/rules>).

---

### Disabled providers

You can disable providers that are loaded automatically through the `disabled_providers` option. This is useful when you want to prevent certain providers from being loaded even if their credentials are available.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "disabled_providers": ["openai", "gemini"]
}
```

**注释：**

The `disabled_providers` takes priority over `enabled_providers`.

The `disabled_providers` option accepts an array of provider IDs. When a provider is disabled:

- It won't be loaded even if environment variables are set.
- It won't be loaded even if API keys are configured through the `/connect` command.
- The provider's models won't appear in the model selection list.

---

### Enabled providers

You can specify an allowlist of providers through the `enabled_providers` option. When set, only the specified providers will be enabled and all others will be ignored.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "enabled_providers": ["anthropic", "openai"]
}
```

This is useful when you want to restrict OpenCode to only use specific providers rather than disabling them one by one.

**注释：**

The `disabled_providers` takes priority over `enabled_providers`.

If a provider appears in both `enabled_providers` and `disabled_providers`, the `disabled_providers` takes priority for backwards compatibility.

---

### Experimental

The `experimental` key contains options that are under active development.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "experimental": {}
}
```

**注意：**

Experimental options are not stable. They may change or be removed without notice.

---

## Variables

You can use variable substitution in your config files to reference environment variables and file contents.

---

### Env vars

Use `{env:VARIABLE_NAME}` to substitute environment variables:

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "model": "{env:OPENCODE_MODEL}",
  "provider": {
    "anthropic": {
      "models": {},
      "options": {
        "apiKey": "{env:ANTHROPIC_API_KEY}"
      }
    }
  }
}
```

If the environment variable is not set, it will be replaced with an empty string.

---

### Files

Use `{file:path/to/file}` to substitute the contents of a file:

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "instructions": ["./custom-instructions.md"],
  "provider": {
    "openai": {
      "options": {
        "apiKey": "{file:~/.secrets/openai-key}"
      }
    }
  }
}
```

File paths can be:

- Relative to the config file directory
- Or absolute paths starting with `/` or `~`

These are useful for:

- Keeping sensitive data like API keys in separate files.
- Including large instruction files without cluttering your config.
- Sharing common configuration snippets across multiple config files.


</details>
