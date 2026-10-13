# Agent 审批与安全

说明沙箱与审批的关系、网络访问与隔离、安全监控及暂停行为，帮助理解运行过程的权限边界。

- 来源：OpenAI／Codex
- 资料类型：存档文档
- 文档时间：官方未公开发表/更新时间（[已复查](<https://learn.chatgpt.com/docs/agent-approvals-security>)）；本地抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://learn.chatgpt.com/docs/agent-approvals-security.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Codex/Agent 审批与安全.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Agent 批准和安全

> 完整的文档索引，请参见[llms.txt](<https://learn.chatgpt.com/llms.txt>)。通过将 `.md` 附加到页面 URL 可以获得文档页面的 Markdown 版本。

Codex 有助于保护您的代码和数据并降低误用的风险。

本页介绍如何安全操作Codex，包括沙箱、审批、
和网络访问。如果您正在寻找Codex安全性，用于
扫描连接的GitHub存储库的产品，请参阅[Codex安全性](<https://learn.chatgpt.com/docs/security>)。

默认情况下，agent 在网络访问关闭的情况下运行。在本地，Codex 使用 OS- 强制沙箱来限制它可以接触的内容（通常是当前工作区），以及控制何时必须停止并在执行操作之前询问您的批准策略。

有关沙盒如何在 ChatGPT 桌面应用程序、
Codex CLI 和 IDE 扩展中工作的高级说明，请参阅 [ 沙盒 ](<https://learn.chatgpt.com/docs/sandboxing>)。
为了更广泛的企业安全概述请参见【[Codex】安全白皮书【](<https://trust.openai.com/?itemUid=382f924d-54f3-43a8-a9df-c39e6c959958&source=click>)】。

## 从已停用的 `untrusted` 批准政策迁移

Codex 和 ChatGPT Work 不再支持 `approval_policy = "untrusted"`。
已停用的设置可能会阻止任一客户端启动。从
用户或项目配置、配置文件、启动脚本和托管
默认值中将其删除。对于交互式、只读用途：

```toml
sandbox_mode = "read-only"
approval_policy = "on-request"
```

或者运行`codex --sandbox read-only --ask-for-approval on-request`。

使用`on-request`，沙箱允许的命令可以在未经批准的情况下运行，
读取可访问的文件，并使用网络访问（如果启用）。

要保留更严格的命令批准规则，请省略显式的 
`approval_policy` 并将项目条目添加到您的用户级别 
`~/.codex/config.toml`：

```toml
[projects."/path/to/project"]
trust_level = "untrusted"
```

然后，除非执行策略规则允许，否则命令需要批准。
这也会禁用项目本地配置。显式设置 `on-request`
 会覆盖项目派生策略；托管 `allowed_approval_policies` 必须 
 包含 `untrusted` 才能允许它。

## 沙箱和批准

使用 Work Cloud 的本地计算机访问具有单独的本地和云执行策略：

- **云执行：**工作云容器使用现有的工作云策略。
- **本地执行：**本地执行要求控制连接的计算机。 MDM 和旧版受管设备要求优先于 Agent 安全性，后者优先于设备的系统要求文件。

对于使用本地访问和点，在启用托管策略时，受支持的全局策略适用于共享云编排器。工作云容器和点云计算机使用自己的执行配置和要求。分别测试本地和云控件。将协调器控制（包括审批和 Web 搜索）保留在全局中。使用专用的“允许的审批策略”和“允许的 Web 搜索模式”控件（如果可用），以及其他支持的字段的 TOML。将 Codex 示例应用到 Work Cloud 的本地计算机访问之前，请检查 [ 配置参考 ](<https://learn.chatgpt.com/docs/config-file/config-reference>) 中的字段列表和执行范围。

在桌面、Web 和移动设备之间移动不会授予对另一台计算机的访问权限。在启用同步或扩展访问权限之前，请查看 Agent 安全基线并测试每个执行环境中允许和阻止的操作。请参阅[托管配置](<https://learn.chatgpt.com/docs/enterprise/managed-configuration>)。

Codex 安全控制来自协同工作的两层：

- **沙盒模式**：Codex执行模型生成的命令时在技术上可以做什么（例如，可以在哪里写入以及是否可以到达网络）。
- **批准策略**：当Codex在执行操作之前必须询问您时（例如，离开沙箱、使用网络或在可信集之外运行命令）。

Codex 根据运行位置使用不同的沙箱模式：

- **[Codex 云（旧版）](<https://learn.chatgpt.com/docs/environments/cloud-environment>)**：在隔离的 OpenAI 管理的容器中运行，防止访问您的主机系统或不相关的数据。使用两阶段运行时模型：安装程序在 agent 阶段之前运行，并且可以访问网络来安装指定的依赖项，然后 agent 阶段默认脱机运行，除非您为该环境启用 Internet 访问。为云环境配置的机密仅在设置期间可用，并在 agent 阶段开始之前删除。
- **当前 Codex 云 **：请参阅 [ 环境变量和网络机密 ](<https://learn.chatgpt.com/docs/environments/cloud-environments#environment-variables-and-secrets>) 了解如何在设置和任务期间处理凭据。
- **Codex CLI / IDE 扩展**：OS-级别机制强制执行沙箱策略。默认值包括无网络访问权限和仅限于活动工作区的写入权限。您可以根据您的风险承受能力配置沙箱、审批策略和网络设置。

在`Auto`预设中（例如`--sandbox workspace-write --ask-for-approval on-request`），Codex可以自动读取工作目录中的文件、进行编辑和运行命令。

Codex 请求批准在工作区之外编辑文件或运行需要网络访问的命令。如果您想在不进行更改的情况下聊天或计划，请使用 `/permissions` 命令切换到 `read-only` 模式。

Codex 还可以征求对宣传副作用的应用程序（连接器）工具调用的批准，即使该操作不是 shell 命令或文件更改。当工具公布破坏性注释时，破坏性 app/MCP 工具调用始终需要批准（除非该工具公布读取注释，该注释具有优先权）。

## 安全监控和暂停任务

GPT-6 Astra 将安全监控纳入 Codex 和 ChatGPT 工作。监控
异步运行，如果检测到潜在不安全模型，可以暂停任务
行为。暂停可以在触发暂停的活动之后到达；监控
不会取代沙箱、权限或结果审查。

如果任务暂停，请阅读通知并查看可用的结果。仅在检查任务可以安全继续后才恢复
。如果通知显示 
 任务已结束或不提供恢复选项，则您无法从该 
 表面恢复。

|表面和数据控制|调查结果和简历|
| ------------------------------------------------------------------------------------------------------- | --------------------------------------------------------- |
|Codex 和 ChatGPT 帮助客户处理结果并恢复流程，无需此处列出的数据控制|在继续之前查看调查结果。|
|Codex CLI 和手机|完整的调查结果和简历不可用。任务结束。|
|零数据保留、修改滥用监控或非 US 数据存储驻留|完整的调查结果和简历不可用。任务结束。|

安全监控评估任务期间的模型行为。
[自动批准审查](<https://learn.chatgpt.com/docs/sandboxing/auto-review>)评估
在运行之前已经需要批准的单个操作。由
自动审批审核批准的操作仍然可以是监控稍后暂停的任务的一部分。

## 网络接入<ElevatedRiskBadge class="ml-2"></ElevatedRiskBadge>

有关当前 Codex 云网络设置，请参阅[连接到服务](<https://learn.chatgpt.com/docs/environments/cloud-environments#connect-to-services>)。对于 Codex 云（旧版），请参阅 [agent 互联网访问 ](<https://learn.chatgpt.com/docs/cloud/internet-access>) 以启用完全互联网访问或域允许列表。

对于 ChatGPT 桌面应用程序、Codex CLI 或 IDE 扩展，默认的 `workspace-write` 沙箱模式会保持网络访问关闭，除非您在配置中启用它：

```toml
[sandbox_workspace_write]
network_access = true
```

### 网络隔离

在受支持的托管 Codex 云执行路径上，Agent 安全要求限制命令网络。 Codex 云环境互联网设置单独适用。 Agent 安全中允许的域不会覆盖云环境互联网设置中的限制。这些命令网络控件本身不会禁用托管网络搜索、应用程序或 MCP。 ChatGPT Work Cloud 具有单独的能力权限，并且不继承这些 Agent 安全要求。

托管命令白名单适用于使用托管代理的命令。如果策略允许完全沙箱升级并获得批准，则该执行可以绕过命令代理。狭窄的网络授权与完整的沙箱升级不同。为预期边界配置强制批准和沙箱要求，并测试普通命令和升级命令。请参阅[托管配置](<https://learn.chatgpt.com/docs/enterprise/managed-configuration#configure-network-access-requirements>)。

网络访问通过适用于脚本、
程序和命令生成的子进程的目标规则进行控制。当命令网络访问 
 已启用时，打开 `network_proxy` 功能以将该流量 
 限制到您配置的网络策略。添加域规则本身不会启用
代理。

```toml
[features.network_proxy]
enabled = true
domains = { "api.openai.com" = "allow", "example.com" = "deny" }
```

对于一次性 CLI 会话，当您只需要 
 切换时使用布尔简写，当您还设置策略选项时使用表格形式：

```bash
codex \
  -c 'features.network_proxy=true' \
  -c 'sandbox_workspace_write.network_access=true'

codex \
  -c 'features.network_proxy.enabled=true' \
  -c 'features.network_proxy.domains={ "api.openai.com" = "allow", "example.com" = "deny" }' \
  -c 'sandbox_workspace_write.network_access=true'
```

该功能改变了启用网络访问的实施方式；它本身不授予
网络访问权限。使用 `sandbox_workspace_write.network_access` 和 
`workspace-write` 配置来决定命令是否具有网络访问权限：

- 网络关闭 + `network_proxy` 打开：网络保持关闭，并且该功能不执行任何操作。
- 网络开启 + `network_proxy` 关闭：网络保持开启且不受限制的直接
出站访问。
- Network on + `network_proxy` on：网络保持打开状态，出站流量为
受配置的网络策略的约束。

代理功能还适用于 [ 权限配置文件 ](<https://learn.chatgpt.com/docs/permissions#network-permissions>)。
 配置文件的 `network.enabled = true` 授予命令网络访问权限，而 
`features.network_proxy = true` 激活该配置文件的域 
 规则的强制执行：

```toml
default_permissions = "project-edit"

[features]
network_proxy = true

[permissions.project-edit]
extends = ":workspace"

[permissions.project-edit.network]
enabled = true

[permissions.project-edit.network.domains]
"api.openai.com" = "allow"
```

如果在此示例中省略代理功能，命令将具有直接网络 
 访问权限，并且 `api.openai.com` 允许规则不会限制其目的地。

管理员管理的 `experimental_network` 要求与用户 
 功能切换是分开的。他们可以在没有 
`features.network_proxy` 的情况下配置和启动沙盒网络，但当活动的 
 沙盒将其关闭时，他们不会打开网络访问。有关管理员端 `requirements.toml` 形状，请参阅 [托管配置 ](<https://learn.chatgpt.com/docs/enterprise/managed-configuration#configure-network-access-requirements>)
。

#### 网络政策

域规则是白名单优先：

- 精确的主机只匹配它们自己。
- `*.example.com` 匹配子域，例如 `api.example.com`，但不匹配
  `example.com`.
- `**.example.com` 匹配顶点域和子域。
- 全局 `*` 允许规则匹配任何未被拒绝的公共主机。治疗`*`
作为广泛的网络访问，并尽可能选择范围规则。
- `deny` 始终胜过 `allow`，全局 `*` 仅对允许规则有效。

#### 本地和私人目的地

默认情况下，`allow_local_binding = false` 阻止环回、链路本地和 
 私有目标：

- 特定例外：添加精确的本地 IP 文字或 `localhost` 允许规则
当命令需要一个本地目标时。
- 更广泛的访问：仅当您有意时才设置`allow_local_binding = true`
想要更广泛的 local/private 覆盖范围。
- 通配符：通配符规则不计为显式本地例外。
- 已解析地址：解析为 local/private IP 的主机名仍被阻止
即使它们符合许可名单。

#### DNS 重新绑定保护

在允许主机名之前，Codex 会尽力执行 DNS 和 IP
 分类检查：

- 失败或超时的查找将被阻止。
- 解析为非公共地址的主机名将被阻止。
- 该检查降低了 DNS 重新绑定风险，但并没有消除它。预防
完全重新绑定需要通过传输
层固定解析的IP。

如果敌对 DNS 在范围内，也在较低层实施出口控制。

#### 危险环境

有两种设置故意拓宽信任边界：

- `dangerously_allow_non_loopback_proxy = true` 可以暴露代理侦听器
环回。
- `dangerously_allow_all_unix_sockets = true` 绕过 Unix 套接字白名单。

仅在严格控制的环境中使用它们。当 Unix 套接字代理
启用时，即使请求非环回绑定，侦听器也保持仅环回状态，
因此沙盒网络不会成为本地守护程序的远程桥。

`network_proxy` 默认关闭。当您启用它时：

|设置|默认|行为|
| -------------------------------------- | ------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `enabled`                              | `false` |仅当命令网络访问已打开时才启动沙盒网络。|
| `domains`                              |未设置|使用白名单行为，因此在您添加 `allow` 规则之前不允许任何外部目标。支持精确主机、作用域通配符和全局 `*` 允许规则； `deny` 总是赢。|
| `unix_sockets`                         |未设置|在添加显式 `allow` 规则之前，不允许使用任何 Unix 套接字目标。|
| `allow_local_binding`                  | `false` |阻止本地和专用网络目标，除非您添加精确的本地 IP 文字或 `localhost` 允许规则，或明确选择更广泛的 local/private 访问。|
| `enable_socks5`                        | `true`  |在政策允许的情况下公开 SOCKS5 支持。|
| `enable_socks5_udp`                    | `true`  |当 SOCKS5 可用时，允许 UDP 超过 SOCKS5。|
| `allow_upstream_proxy`                 | `true`  |让沙盒网络尊重环境中的上游代理。|
| `dangerously_allow_non_loopback_proxy` | `false` |保持侦听器端点处于环回状态，除非您故意将它们公开到本地主机之外。|
| `dangerously_allow_all_unix_sockets`   | `false` |保持 Unix 套接字访问基于白名单，除非您故意绕过该保护。|

### 命令网络代理外部的流量

网络代理过滤在本地命令沙箱内运行
的脚本、程序和子进程。它不会过滤 Web 搜索、应用程序或 
 连接器工具调用、MCP 服务器连接、浏览器或计算机使用活动、
Codex 云任务或客户端的模型和身份验证请求。这些
表面使用单独的服务连接、功能设置、工作区
策略或环境控制。

在访问源之前，浏览器工具会单独检查托管网络拒绝和独占允许列表
。浏览器源策略可以进一步限制站点
访问、上传、下载和开发人员工具。请参阅
[托管浏览器控件](<https://learn.chatgpt.com/docs/enterprise/managed-configuration#control-browser-and-computer-use>)。

对于托管用户，将命令网络策略与应用程序、插件、浏览器或计算机使用的 
`allowed_web_search_modes`、批准的 `mcp_servers` 和功能要求 
 等控件相结合。请参阅
[托管配置](<https://learn.chatgpt.com/docs/enterprise/managed-configuration>)。

您还可以控制 [ 网络搜索工具 ](<https://platform.openai.com/docs/guides/tools-web-search>)，而无需授予对生成命令的完全网络访问权限。 Codex 默认使用网络搜索缓存来访问结果。缓存是 OpenAI 维护的 Web 结果索引，因此缓存模式返回预先索引的结果，而不是获取实时页面。这减少了来自任意实时内容的即时注入的风险。将网络结果视为不可信。如果您使用 `--yolo` 或其他 [ 完全访问沙箱设置 ](<#常见的沙箱和审批组合>)，网络搜索默认为实时结果。使用`--search`或设置`web_search = "live"`以允许实时浏览，或将其设置为`"disabled"`以关闭该工具：

```toml
web_search = "cached"  # default
# web_search = "disabled"
# web_search = "live"  # same as --search
```

当外部 Web 访问应由 
 搜索索引控制时，设置 `web_search = "indexed"`。在 Codex 中启用网络访问或网页搜索时请务必小心。
提示注入可能会导致 agent 获取并遵循不受信任的指令。

## 默认值和建议

- 启动时，Codex 会检测该文件夹是否受版本控制并建议：
  - 版本控制文件夹：`Auto`（工作区写入+按需批准）
  - 非版本控制文件夹：`read-only`
- 根据您的设置，Codex 也可能在 `read-only` 中启动，直到您明确信任工作目录（例如，通过入门提示或 `/permissions`）。
- 工作空间包括当前目录和临时目录，例如`/tmp`。使用`/status`命令查看工作区中有哪些目录。
- 要接受默认值，请运行 `codex`。
- 您可以明确设置这些：
  - `codex --sandbox workspace-write --ask-for-approval on-request`
  - `codex --sandbox read-only --ask-for-approval on-request`

### 可写根中的受保护路径

在默认的`workspace-write`沙箱策略中，可写根仍然包含受保护的路径：

- `<writable_root>/.git` 无论显示为目录还是文件，都被保护为只读。
- 如果 `<writable_root>/.git` 是指针文件 (`gitdir: ...`)，则解析的 Git 目录路径也被保护为只读。
- `<writable_root>/.agents` 当作为目录存在时被保护为只读。
- `<writable_root>/.codex` 当作为目录存在时被保护为只读。
- 保护是递归的，因此这些路径下的所有内容都是只读的。

### 运行时无批准提示

您可以使用 `--ask-for-approval never` 或 `-a never`（简写）禁用批准提示。

此选项适用于所有 `--sandbox` 模式，因此您仍然可以控制 Codex 的自主级别。 Codex 在您设定的限制范围内尽最大努力。

如果您需要 Codex 来读取文件、进行编辑以及在没有批准提示的情况下通过网络访问运行命令，请使用 `--sandbox danger-full-access`（或 `--dangerously-bypass-approvals-and-sandbox` 标志）。这样做之前请务必小心。

对于中间立场，`approval_policy = { granular = { ... } }` 允许您保持特定批准提示类别的交互性，同时自动拒绝其他类别。细粒度策略涵盖沙箱批准、执行策略规则提示、MCP 提示、`request_permissions` 提示和技能脚本批准。

### 自动审批审核

默认情况下，批准请求会发送给您：

```toml
approvals_reviewer = "user"
```

当批准是交互式的时，自动批准审查适用，例如
`approval_policy = "on-request"`或精细的批准策略。设置 
`approvals_reviewer = "auto_review"` 以在 Codex 运行请求之前通过审阅者 agent 路由符合条件的审批请求 
：

```toml
approval_policy = "on-request"
approvals_reviewer = "auto_review"
```

有关完整审阅者生命周期、触发条件、配置优先级
和失败行为，请参阅
[自动审阅](<https://learn.chatgpt.com/docs/sandboxing/auto-review>)。

审核者仅评估已需要批准的操作，例如沙箱
升级、阻止的网络请求、`request_permissions`提示或
副作用应用程序和MCP工具调用。留在沙箱 
 内的操作将继续，无需额外的审核步骤。

审核者策略检查数据泄露、凭证探测、持久
安全削弱和破坏性操作。低风险和中风险行动
可以在政策允许的情况下进行。该策略拒绝关键风险操作。
高风险操作需要足够的用户授权并且没有匹配的拒绝规则。
提示构建、审查会话和解析失败无法关闭。超时
单独出现，但操作仍然不运行。

[默认审阅者策略](<https://github.com/openai/codex/blob/main/codex-rs/core/src/guardian/policy.md>)
位于开源Codex存储库中。企业可以在托管需求中将其
租户特定部分替换为`guardian_policy_config`。
还支持本地`[auto_review].policy`文本，但托管需求
优先。有关设置详细信息，请参阅
[托管配置](<https://learn.chatgpt.com/docs/enterprise/managed-configuration#configure-automatic-review-policy>)。

在 ChatGPT 桌面应用程序中，这些审阅显示为自动审阅项目，状态为 
，例如正在审阅、已批准、已拒绝、已中止或已超时。他们还可以
包括针对已审核的
请求的风险级别和用户授权评估。

自动审查使用额外的模型调用，因此可以增加 Codex 的使用。管理员
可以使用`allowed_approvals_reviewers`对其进行约束。

### 常见的沙箱和审批组合

|意图|标志/配置|效果|
| ------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ |
|自动（预设）|_不需要标志_或`--sandbox workspace-write --ask-for-approval on-request`|Codex 可以在工作区中读取文件、进行编辑和运行命令。 Codex 需要获得批准才能在工作区外进行编辑或访问网络。|
|安全的只读浏览| `--sandbox read-only --ask-for-approval on-request`                                                                                 |Codex可以在只读沙箱中读取文件并运行命令。沙箱之外的操作可能需要批准。|
|只读非交互式 (CI)| `--sandbox read-only --ask-for-approval never`                                                                                      |Codex可以在只读沙箱内读取文件并运行命令；它从不请求批准。|
|自动审核模式|`--sandbox workspace-write --ask-for-approval on-request -c approvals_reviewer=auto_review` 或 `approvals_reviewer = "auto_review"`|与标准按需模式相同的沙箱边界，但合格的批准请求由自动审核进行审核，而不是向用户显示。|
|危险的完全访问|`--dangerously-bypass-approvals-and-sandbox`（别名：`--yolo`）|<ElevatedRiskBadge /> 无沙箱；没有批准_（不推荐）_|

对于非交互式运行，请使用 `codex exec --sandbox workspace-write`； Codex 将旧的 `codex exec --full-auto` 调用保留为已弃用的兼容性路径并打印警告。

#### `config.toml`中的配置

有关更广泛的配置工作流程，请参阅[配置基础知识](<https://learn.chatgpt.com/docs/config-file/config-basic>)、[高级配置](<https://learn.chatgpt.com/docs/config-file/config-advanced#approval-policies-and-sandbox-modes>)和[配置参考](<https://learn.chatgpt.com/docs/config-file/config-reference>)。

```toml
# Interactive approvals with a read-only sandbox
approval_policy = "on-request"
sandbox_mode    = "read-only"
allow_login_shell = false # optional hardening: disallow login shells for shell-based tools

# Optional: Allow network in workspace-write mode
[sandbox_workspace_write]
network_access = true

# Optional: granular approval policy
# approval_policy = { granular = {
#   sandbox_approval = true,
#   rules = true,
#   mcp_elicitations = true,
#   request_permissions = false,
#   skill_approval = false
# } }
```

您还可以将预设另存为[配置文件](<https://learn.chatgpt.com/docs/config-file/config-advanced#profiles>)，然后使用`codex --profile profile-name`选择它们：

```toml
# ~/.codex/full_auto.config.toml
approval_policy = "on-request"
sandbox_mode    = "workspace-write"
```

```toml
# ~/.codex/readonly_quiet.config.toml
approval_policy = "never"
sandbox_mode    = "read-only"
```

### 本地测试沙箱

要查看命令在 Codex 沙箱下运行时会发生什么，请使用以下 Codex CLI 命令：

```bash
# macOS
codex sandbox macos [--permissions-profile <name>] [--log-denials] [COMMAND]...
# Linux
codex sandbox linux [--permissions-profile <name>] [COMMAND]...
# Windows
codex sandbox windows [--permissions-profile <name>] [COMMAND]...
```

`sandbox` 命令也可用作 `codex debug`，并且平台助手具有别名（例如 `codex sandbox seatbelt` 和 `codex sandbox landlock`）。

## OS-关卡沙箱

Codex 根据您的 OS 以不同方式实施沙箱：

- **macOS** 使用安全带策略，并使用 `sandbox-exec` 和与您选择的 `--sandbox` 模式对应的配置文件 (`-p`) 运行命令。当受限读取访问启用平台默认设置时，Codex 会附加精心策划的 macOS 平台策略（而不是广泛允许 `/System`）以保留通用工具兼容性。
- **Linux** 默认使用 `bwrap` 加 `seccomp`。
- **Windows** 支持 [MXC](<https://learn.chatgpt.com/docs/windows/windows-sandbox#windows-sandbox>)，`elevated` 和 `unelevated` 作为旧版后备。在 [Linux 的 Windows 子系统 2 (WSL2)](<https://learn.chatgpt.com/docs/windows/wsl>) 中，Codex 使用 Linux 沙箱实现。 WSL1通过Codex`0.114`支持；从 `0.115` 开始，Linux 沙箱移至 `bwrap`，因此不再支持 WSL1。

如果您在 Windows 上使用 Codex IDE 扩展，则直接支持 WSL2。在 VS Code 设置中进行以下设置，以便在可用时将 agent 保留在 WSL2 内：

```json
{
  "chatgpt.runCodexInWindowsSubsystemForLinux": true
}
```

这确保了 IDE 扩展继承 Linux 沙箱语义以进行命令、批准和文件系统访问，即使主机 OS 是 Windows 也是如此。请参阅 [WSL 指南 ](<https://learn.chatgpt.com/docs/windows/wsl>) 了解更多信息。

在 Windows 上运行时，当设备和策略通过在 `config.toml` 中设置支持它时，首选 MXC：

```toml
[features]
prefer_mxc = true
```

在 `windows.sandbox` 中保留允许的遗留实现以供后备。有关配置和兼容性限制，请参阅 [Windows 设置指南 ](<https://learn.chatgpt.com/docs/windows/windows-sandbox#enable-mxc>)。

当您在 Docker 等容器化环境中运行 Linux 时，如果主机或容器配置阻止 Codex 所需的命名空间、setuid `bwrap` 或 `seccomp` 操作，则沙箱可能无法工作。

在这种情况下，配置 Docker 容器以提供所需的隔离，然后在容器内运行带有 `--sandbox danger-full-access`（或 `--dangerously-bypass-approvals-and-sandbox` 标志）的 `codex`。

### 在开发容器中运行 Codex

如果您的主机无法直接运行 Linux 沙箱，或者您的组织已经标准化了容器化开发，请使用 Dev Containers 运行 Codex，并让 Docker 提供外部隔离边界。这适用于 Visual Studio Code 开发容器和兼容工具。

使用 [Codex 安全开发容器示例 ](<https://github.com/openai/codex/tree/main/.devcontainer>) 作为参考实现。该示例安装Codex、常用开发工具`bubblewrap`和基于防火墙的出站控制。

开发容器提供了实质性的保护，但它们并不能阻止所有
攻击。如果您在容器内使用 `--sandbox danger-full-access` 或 
 `--dangerously-bypass-approvals-and-sandbox` 运行 Codex，则恶意 
 项目可以窃取 dev 容器内可用的任何内容，包括 
 Codex 凭据。仅将此模式用于受信任的存储库，并且 
 监视 Codex 活动，就像在任何其他提升的环境中一样。

参考实现包括：

- 安装了 Codex 和常用开发工具的 Ubuntu 24.04 基础映像；
- 用于出站访问的白名单驱动的防火墙配置文件；
- VS Code 在容器中重新打开工作区的设置和扩展建议；
- 命令历史记录和 Codex 配置的持久安装；
- `bubblewrap`，因此当容器授予所需的功能时，Codex仍然可以使用其Linux沙箱。

尝试一下：

1. 安装 Visual Studio Code 和 [Dev Containers 扩展 ](<https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers>)。
2. 将 Codex 示例 `.devcontainer` 设置复制到您的存储库中，或直接从 Codex 存储库启动。
3. 在VS Code中，运行`Dev Containers: Open Folder in Container...`并选择`.devcontainer/devcontainer.secure.json`。
4. 容器启动后，打开终端并运行`codex`。

您还可以从CLI启动容器：

```bash
devcontainer up --workspace-folder . --config .devcontainer/devcontainer.secure.json
```

该示例包含三个主要部分：

- `.devcontainer/devcontainer.secure.json` 控制容器设置、功能、安装、环境变量和 VS Code 扩展。
- `.devcontainer/Dockerfile.secure` 定义了基于 Ubuntu 的映像和安装的工具。
- `.devcontainer/init-firewall.sh`应用出网策略。

参考防火墙有意作为一个起点。如果您依赖域白名单进行隔离，请实施适合您环境的 DNS 重新绑定和 DNS 刷新保护，例如 TTL- 感知刷新或 DNS- 感知防火墙。

在容器内，选择以下模式之一：

- 如果开发容器配置文件授予 `bwrap` 创建内部沙箱所需的功能，请保持 Codex 的 Linux 沙箱启用。
- 如果容器是您预期的安全边界，请在容器内运行带有 `--sandbox danger-full-access` 的 Codex，这样 Codex 就不会尝试创建第二个沙箱层。

## 版本控制

Codex 最适合版本控制工作流程：

- 在功能分支上工作并在委派之前保持 `git status` 干净。这使得 Codex 补丁更容易隔离和恢复。
- 与直接编辑跟踪文件相比，更喜欢基于补丁的工作流程（例如，`git diff`/`git apply`）。经常提交，以便您可以小幅度回滚。
- 像对待任何其他 PR 一样对待 Codex 建议：运行有针对性的验证、审查差异并记录提交消息中的决策以进行审核。

## 监控和遥测

Codex 支持通过 OpenTelemetry (OTel) 选择加入监控，以帮助团队审核使用情况、调查问题并满足合规性要求，而不会削弱本地安全默认设置。遥测默认关闭；在您的配置中明确启用它。

### 概述

- Codex 默认关闭 OTel 导出以保持本地运行独立。
- 启用后，Codex 会发出结构化日志事件，涵盖聊天、API 请求、SSE/WebSocket 流活动、用户提示（默认情况下已编辑）、工具审批决策和工具结果。
- Codex 使用 `service.name`（发起者）、CLI 版本和环境标签标记导出的事件，以分隔 dev/staging/prod 流量。

### 启用 OTel（选择加入）

将 `[otel]` 块添加到 Codex 配置（通常为 `~/.codex/config.toml`），选择导出器以及是否记录提示文本。

```toml
[otel]
environment = "staging"   # dev | staging | prod
exporter = "none"          # none | otlp-http | otlp-grpc
log_user_prompt = false     # redact prompt text unless policy allows
```

- `exporter = "none"` 使仪器保持活动状态，但不向任何地方发送数据。
- 要将事件发送到您自己的收集器，请选择以下选项之一：

```toml
[otel]
exporter = { otlp-http = {
  endpoint = "https://otel.example.com/v1/logs",
  protocol = "binary",
  headers = { "x-otlp-api-key" = "${OTLP_TOKEN}" }
}}
```

```toml
[otel]
exporter = { otlp-grpc = {
  endpoint = "https://otel.example.com:4317",
  headers = { "x-otlp-meta" = "abc123" }
}}
```

Codex 批处理事件并在关闭时刷新它们。 Codex 仅导出其 OTel 模块生成的遥测数据。

### 活动类别

代表性事件类型包括：

- `codex.conversation_starts`（模型、推理设置、sandbox/approval策略）
- `codex.api_request`（尝试、status/success、持续时间和错误详细信息）
- `codex.sse_event`（流事件类型，success/failure，持续时间，加上令牌计数`response.completed`）
- `codex.websocket_request` 和 `codex.websocket_event`（请求持续时间加上每条消息 kind/success/error）
- `codex.user_prompt`（长度；内容经过编辑，除非明确启用）
- `codex.tool_decision`（approved/denied，来源：配置与用户）
- `codex.tool_result`（持续时间、成功、输出片段）

相关 OTel 指标（计数器加持续时间直方图对）包括 `codex.api_request`、`codex.sse_event`、`codex.websocket.request`、`codex.websocket.event` 和 `codex.tool.call`（以及相应的 `.duration_ms` 仪器）。

有关完整的事件目录和配置参考，请参阅 [Codex 配置文档上 GitHub](<https://github.com/openai/codex/blob/main/docs/config.md#otel>).

### 安全和隐私指导

- 保留 `log_user_prompt = false` 除非策略明确允许存储提示内容。提示可以包括源代码和敏感数据。
- 仅将遥测数据发送给您控制的收集器；应用符合您的合规性要求的保留限制和访问控制。
- 将工具参数和输出视为敏感的。如有可能，请在收集器或 SIEM 处进行编辑。
- 如果您不希望 Codex 将会话记录保存在 `CODEX_HOME` 下，请检查本地数据保留设置（例如，`history.persistence` / `history.max_bytes`）。请参见[高级配置](<https://learn.chatgpt.com/docs/config-file/config-advanced#history-persistence>)和[配置参考](<https://learn.chatgpt.com/docs/config-file/config-reference>)。
- 如果您在关闭网络访问的情况下运行 CLI，OTel 导出将无法到达您的收集器。要导出，请允许 OTel 端点以 `workspace-write` 模式进行网络访问，或从 Codex 云导出，收集器域位于您批准的列表中。
- 定期查看事件以了解 approval/sandbox 更改和意外工具执行。

OTel 是可选的，旨在补充而不是取代上述沙箱和批准保护。

## 托管配置

企业管理员可以在 [托管配置 ](<https://learn.chatgpt.com/docs/enterprise/managed-configuration>) 中为其工作区配置 Codex 安全设置。请参阅该页面了解设置和策略详细信息。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Agent approvals & security

> For the complete documentation index, see [llms.txt](<https://learn.chatgpt.com/llms.txt>). Markdown versions of documentation pages are available by appending `.md` to the page URL.

Codex helps protect your code and data and reduces the risk of misuse.

This page covers how to operate Codex safely, including sandboxing, approvals,
  and network access. If you are looking for Codex Security, the product for
  scanning connected GitHub repositories, see [Codex Security](<https://learn.chatgpt.com/docs/security>).

By default, the agent runs with network access turned off. Locally, Codex uses an OS-enforced sandbox that limits what it can touch (typically to the current workspace), plus an approval policy that controls when it must stop and ask you before acting.

For a high-level explanation of how sandboxing works across the ChatGPT desktop app,
Codex CLI, and IDE extension, see [sandboxing](<https://learn.chatgpt.com/docs/sandboxing>).
For a broader enterprise security overview, see the [Codex security white paper](<https://trust.openai.com/?itemUid=382f924d-54f3-43a8-a9df-c39e6c959958&source=click>).

## Migrate from the retired `untrusted` approval policy

Codex and ChatGPT Work no longer support `approval_policy = "untrusted"`.
The retired setting can prevent either client from starting. Remove it from
user or project configuration, profile files, startup scripts, and managed
defaults. For interactive, read-only use:

```toml
sandbox_mode = "read-only"
approval_policy = "on-request"
```

Or run `codex --sandbox read-only --ask-for-approval on-request`.

With `on-request`, commands allowed by the sandbox can run without approval,
read accessible files, and use network access if enabled.

To preserve the stricter command-approval rule, omit an explicit
`approval_policy` and add a project entry to your user-level
`~/.codex/config.toml`:

```toml
[projects."/path/to/project"]
trust_level = "untrusted"
```

Commands then require approval unless an execution-policy rule allows them.
This also disables project-local configuration. Explicitly setting `on-request`
overrides the project-derived policy; managed `allowed_approval_policies` must
include `untrusted` to permit it.

## Sandbox and approvals

Local computer access with Work Cloud has separate local and cloud execution policies:

- **Cloud execution:** Work cloud containers use existing Work Cloud policies.
- **Local execution:** Local execution requirements govern the connected computer. MDM and legacy managed-device requirements take precedence over Agent Security, which takes precedence over the device's system requirements file.

For Work with local access and dots, supported Global policy applies to the shared cloud orchestrator when managed policy is enabled. Work cloud containers and dots cloud computers use their own execution configuration and requirements. Test local and cloud controls separately. Keep orchestrator controls, including approvals and web search, in Global. Use the dedicated Allowed approval policies and Allowed web search modes controls where available, and TOML for other supported fields. Check the [Configuration Reference](<https://learn.chatgpt.com/docs/config-file/config-reference>) for the field list and execution scope before applying a Codex example to Local computer access with Work Cloud.

Moving between desktop, web, and mobile does not grant access to another computer. Before enabling sync or expanding access, review the Agent Security baseline and test an allowed and a blocked action in each execution environment. See [Managed configuration](<https://learn.chatgpt.com/docs/enterprise/managed-configuration>).

Codex security controls come from two layers that work together:

- **Sandbox mode**: What Codex can do technically (for example, where it can write and whether it can reach the network) when it executes model-generated commands.
- **Approval policy**: When Codex must ask you before it executes an action (for example, leaving the sandbox, using the network, or running commands outside a trusted set).

Codex uses different sandbox modes depending on where you run it:

- **[Codex Cloud (Legacy)](<https://learn.chatgpt.com/docs/environments/cloud-environment>)**: Runs in isolated OpenAI-managed containers, preventing access to your host system or unrelated data. Uses a two-phase runtime model: setup runs before the agent phase and can access the network to install specified dependencies, then the agent phase runs offline by default unless you enable internet access for that environment. Secrets configured for cloud environments are available only during setup and are removed before the agent phase starts.
- **Current Codex Cloud**: See [environment variables and network secrets](<https://learn.chatgpt.com/docs/environments/cloud-environments#environment-variables-and-secrets>) for how credentials are handled during setup and tasks.
- **Codex CLI / IDE extension**: OS-level mechanisms enforce sandbox policies. Defaults include no network access and write permissions limited to the active workspace. You can configure the sandbox, approval policy, and network settings based on your risk tolerance.

In the `Auto` preset (for example, `--sandbox workspace-write --ask-for-approval on-request`), Codex can read files, make edits, and run commands in the working directory automatically.

Codex asks for approval to edit files outside the workspace or to run commands that require network access. If you want to chat or plan without making changes, switch to `read-only` mode with the `/permissions` command.

Codex can also elicit approval for app (connector) tool calls that advertise side effects, even when the action isn't a shell command or file change. Destructive app/MCP tool calls always require approval when the tool advertises a destructive annotation (unless the tool advertises a read annotation, which takes priority).

## Safety monitoring and paused tasks

GPT-6 Astra includes safety monitoring in Codex and ChatGPT Work. Monitoring
runs asynchronously and can pause a task if it detects potentially unsafe model
behavior. A pause can arrive after the activity that triggered it; monitoring
doesn't replace sandboxing, permissions, or review of the result.

If a task pauses, read the notice and review the findings when available. Resume
only after checking that the task can safely continue. If the notice says the
task has ended or doesn't offer an option to resume, you can't resume from that
surface.

| Surface and data controls                                                                               | Findings and resume                                       |
| ------------------------------------------------------------------------------------------------------- | --------------------------------------------------------- |
| Codex and ChatGPT Work clients with the findings and resume flow, without the data controls listed here | Review the findings before resuming.                      |
| Codex CLI and mobile                                                                                    | Full findings and resume aren't available. The task ends. |
| Zero data retention, Modified Abuse Monitoring, or non-US data storage residency                        | Full findings and resume aren't available. The task ends. |

Safety monitoring evaluates model behavior during a task.
[Automatic approval review](<https://learn.chatgpt.com/docs/sandboxing/auto-review>) evaluates individual actions that
already require approval before those actions run. An action approved by
automatic approval review can still be part of a task that monitoring later pauses.

## Network access<ElevatedRiskBadge class="ml-2"></ElevatedRiskBadge>

For current Codex Cloud network settings, see [Connect to services](<https://learn.chatgpt.com/docs/environments/cloud-environments#connect-to-services>). For Codex Cloud (Legacy), see [agent internet access](<https://learn.chatgpt.com/docs/cloud/internet-access>) to enable full internet access or a domain allow list.

For the ChatGPT desktop app, Codex CLI, or IDE extension, the default `workspace-write` sandbox mode keeps network access turned off unless you enable it in your configuration:

```toml
[sandbox_workspace_write]
network_access = true
```

### Network isolation

On supported managed Codex Cloud execution paths, Agent Security requirements constrain command networking. Codex Cloud environment internet settings apply separately. An allowed domain in Agent Security does not override a restriction in the Cloud environment's internet settings. These command-network controls do not, by themselves, disable hosted web search, apps, or MCP. ChatGPT Work Cloud has separate capability permissions and does not inherit these Agent Security requirements.

A managed command allowlist applies to commands using the managed proxy. Where policy permits full sandbox escalation and it is approved, that execution can bypass the command proxy. A narrow network grant is different from full sandbox escalation. Configure enforced approval and sandbox requirements for the intended boundary, and test both ordinary and escalated commands. See [Managed configuration](<https://learn.chatgpt.com/docs/enterprise/managed-configuration#configure-network-access-requirements>).

Network access is controlled through destination rules that apply to scripts,
programs, and subprocesses spawned by commands. When command network access is
already enabled, turn on the `network_proxy` feature to constrain that traffic
to the network policy you configure. Adding domain rules does not enable the
proxy by itself.

```toml
[features.network_proxy]
enabled = true
domains = { "api.openai.com" = "allow", "example.com" = "deny" }
```

For a one-off CLI session, use the boolean shorthand when you only need the
toggle, and the table form when you also set policy options:

```bash
codex \
  -c 'features.network_proxy=true' \
  -c 'sandbox_workspace_write.network_access=true'

codex \
  -c 'features.network_proxy.enabled=true' \
  -c 'features.network_proxy.domains={ "api.openai.com" = "allow", "example.com" = "deny" }' \
  -c 'sandbox_workspace_write.network_access=true'
```

The feature changes how enabled network access is enforced; it does not grant
network access by itself. Use `sandbox_workspace_write.network_access` with
`workspace-write` config to decide whether commands have network access at all:

- Network off + `network_proxy` on: network stays off, and the feature does nothing.
- Network on + `network_proxy` off: network stays on with unrestricted direct
  outbound access.
- Network on + `network_proxy` on: network stays on, and outbound traffic is
  constrained by the configured network policy.

The proxy feature also applies to [permission profiles](<https://learn.chatgpt.com/docs/permissions#network-permissions>).
A profile's `network.enabled = true` grants command network access, while
`features.network_proxy = true` activates enforcement of that profile's domain
rules:

```toml
default_permissions = "project-edit"

[features]
network_proxy = true

[permissions.project-edit]
extends = ":workspace"

[permissions.project-edit.network]
enabled = true

[permissions.project-edit.network.domains]
"api.openai.com" = "allow"
```

If you omit the proxy feature in this example, commands have direct network
access and the `api.openai.com` allow rule does not restrict their destinations.

Admin-managed `experimental_network` requirements are separate from the user
feature toggle. They can configure and start sandboxed networking without
`features.network_proxy`, but they do not turn on network access when the active
sandbox keeps it off. See [Managed configuration](<https://learn.chatgpt.com/docs/enterprise/managed-configuration#configure-network-access-requirements>)
for the administrator-side `requirements.toml` shape.

#### Network policy

Domain rules are allowlist-first:

- Exact hosts match only themselves.
- `*.example.com` matches subdomains such as `api.example.com`, but not
  `example.com`.
- `**.example.com` matches both the apex and subdomains.
- A global `*` allow rule matches any public host that is not denied. Treat `*`
  as broad network access and prefer scoped rules when you can.
- `deny` always wins over `allow`, and global `*` is only valid for allow rules.

#### Local and private destinations

By default, `allow_local_binding = false` blocks loopback, link-local, and
private destinations:

- Specific exceptions: add an exact local IP literal or `localhost` allow rule
  when a command needs one local target.
- Broader access: set `allow_local_binding = true` only when you intentionally
  want wider local/private reach.
- Wildcards: wildcard rules do not count as explicit local exceptions.
- Resolved addresses: hostnames that resolve to local/private IPs stay blocked
  even if they match the allowlist.

#### DNS rebinding protections

Before allowing a hostname, Codex performs a best-effort DNS and IP
classification check:

- Lookups that fail or time out are blocked.
- Hostnames that resolve to non-public addresses are blocked.
- The check reduces DNS rebinding risk, but it does not eliminate it. Preventing
  rebinding completely would require pinning resolved IPs through the transport
  layer.

If hostile DNS is in scope, enforce egress controls at a lower layer too.

#### Dangerous settings

Two settings deliberately widen the trust boundary:

- `dangerously_allow_non_loopback_proxy = true` can expose proxy listeners beyond
  loopback.
- `dangerously_allow_all_unix_sockets = true` bypasses the Unix socket allowlist.

Use them only in tightly controlled environments. When Unix socket proxying is
enabled, listeners stay loopback-only even if non-loopback binding was requested,
so sandboxed networking does not become a remote bridge into local daemons.

`network_proxy` is off by default. When you enable it:

| Setting                                | Default | Behavior                                                                                                                                                                              |
| -------------------------------------- | ------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `enabled`                              | `false` | Starts sandboxed networking only when command network access is already on.                                                                                                           |
| `domains`                              | unset   | Uses allowlist behavior, so no external destinations are allowed until you add `allow` rules. Supports exact hosts, scoped wildcards, and global `*` allow rules; `deny` always wins. |
| `unix_sockets`                         | unset   | No Unix socket destinations are allowed until you add explicit `allow` rules.                                                                                                         |
| `allow_local_binding`                  | `false` | Blocks local and private-network destinations unless you add an exact local IP literal or `localhost` allow rule, or explicitly opt into broader local/private access.                |
| `enable_socks5`                        | `true`  | Exposes SOCKS5 support when policy allows it.                                                                                                                                         |
| `enable_socks5_udp`                    | `true`  | Allows UDP over SOCKS5 when SOCKS5 is available.                                                                                                                                      |
| `allow_upstream_proxy`                 | `true`  | Lets sandboxed networking honor an upstream proxy from the environment.                                                                                                               |
| `dangerously_allow_non_loopback_proxy` | `false` | Keeps listener endpoints on loopback unless you deliberately expose them beyond localhost.                                                                                            |
| `dangerously_allow_all_unix_sockets`   | `false` | Keeps Unix socket access allowlist-based unless you deliberately bypass that protection.                                                                                              |

### Traffic outside the command network proxy

The network proxy filters scripts, programs, and child processes that run
inside the local command sandbox. It does not filter web search, app or
connector tool calls, MCP server connections, browser or Computer Use activity,
Codex cloud tasks, or the client's model and authentication requests. These
surfaces use separate service connections, feature settings, workspace
policies, or environment controls.

Browser tools separately check managed network denies and exclusive allowlists
before accessing an origin. Browser origin policies can further restrict site
access, uploads, downloads, and developer tools. See
[managed browser controls](<https://learn.chatgpt.com/docs/enterprise/managed-configuration#control-browser-and-computer-use>).

For managed users, combine command network policy with controls such as
`allowed_web_search_modes`, approved `mcp_servers`, and feature requirements
for apps, plugins, browsers, or Computer Use. See
[Managed configuration](<https://learn.chatgpt.com/docs/enterprise/managed-configuration>).

You can also control the [web search tool](<https://platform.openai.com/docs/guides/tools-web-search>) without granting full network access to spawned commands. Codex defaults to using a web search cache to access results. The cache is an OpenAI-maintained index of web results, so cached mode returns pre-indexed results instead of fetching live pages. This reduces exposure to prompt injection from arbitrary live content. Treat web results as untrusted. If you are using `--yolo` or another [full access sandbox setting](<#common-sandbox-and-approval-combinations>), web search defaults to live results. Use `--search` or set `web_search = "live"` to allow live browsing, or set it to `"disabled"` to turn the tool off:

```toml
web_search = "cached"  # default
# web_search = "disabled"
# web_search = "live"  # same as --search
```

Set `web_search = "indexed"` when external web access should be gated by the
search index. Use caution when enabling network access or web search in Codex.
Prompt injection can cause the agent to fetch and follow untrusted instructions.

## Defaults and recommendations

- On launch, Codex detects whether the folder is version-controlled and recommends:
  - Version-controlled folders: `Auto` (workspace write + on-request approvals)
  - Non-version-controlled folders: `read-only`
- Depending on your setup, Codex may also start in `read-only` until you explicitly trust the working directory (for example, via an onboarding prompt or `/permissions`).
- The workspace includes the current directory and temporary directories like `/tmp`. Use the `/status` command to see which directories are in the workspace.
- To accept the defaults, run `codex`.
- You can set these explicitly:
  - `codex --sandbox workspace-write --ask-for-approval on-request`
  - `codex --sandbox read-only --ask-for-approval on-request`

### Protected paths in writable roots

In the default `workspace-write` sandbox policy, writable roots still include protected paths:

- `<writable_root>/.git` is protected as read-only whether it appears as a directory or file.
- If `<writable_root>/.git` is a pointer file (`gitdir: ...`), the resolved Git directory path is also protected as read-only.
- `<writable_root>/.agents` is protected as read-only when it exists as a directory.
- `<writable_root>/.codex` is protected as read-only when it exists as a directory.
- Protection is recursive, so everything under those paths is read-only.

### Run without approval prompts

You can disable approval prompts with `--ask-for-approval never` or `-a never` (shorthand).

This option works with all `--sandbox` modes, so you still control Codex's level of autonomy. Codex makes a best effort within the constraints you set.

If you need Codex to read files, make edits, and run commands with network access without approval prompts, use `--sandbox danger-full-access` (or the `--dangerously-bypass-approvals-and-sandbox` flag). Use caution before doing so.

For a middle ground, `approval_policy = { granular = { ... } }` lets you keep specific approval prompt categories interactive while automatically rejecting others. The granular policy covers sandbox approvals, execpolicy-rule prompts, MCP prompts, `request_permissions` prompts, and skill-script approvals.

### Automatic approval reviews

By default, approval requests route to you:

```toml
approvals_reviewer = "user"
```

Automatic approval reviews apply when approvals are interactive, such as
`approval_policy = "on-request"` or a granular approval policy. Set
`approvals_reviewer = "auto_review"` to route eligible approval requests
through a reviewer agent before Codex runs the request:

```toml
approval_policy = "on-request"
approvals_reviewer = "auto_review"
```

For the full reviewer lifecycle, trigger conditions, configuration precedence,
and failure behavior, see
[Auto-review](<https://learn.chatgpt.com/docs/sandboxing/auto-review>).

The reviewer evaluates only actions that already need approval, such as sandbox
escalations, blocked network requests, `request_permissions` prompts, or
side-effecting app and MCP tool calls. Actions that stay inside the sandbox
continue without an extra review step.

The reviewer policy checks for data exfiltration, credential probing, persistent
security weakening, and destructive actions. Low-risk and medium-risk actions
can proceed when policy allows them. The policy denies critical-risk actions.
High-risk actions require enough user authorization and no matching deny rule.
Prompt-build, review-session, and parse failures fail closed. Timeouts are
surfaced separately, but the action still does not run.

The [default reviewer policy](<https://github.com/openai/codex/blob/main/codex-rs/core/src/guardian/policy.md>)
is in the open-source Codex repository. Enterprises can replace its
tenant-specific section with `guardian_policy_config` in managed requirements.
Local `[auto_review].policy` text is also supported, but managed requirements
take precedence. For setup details, see
[Managed configuration](<https://learn.chatgpt.com/docs/enterprise/managed-configuration#configure-automatic-review-policy>).

In the ChatGPT desktop app, these reviews appear as automatic review items with a status
such as Reviewing, Approved, Denied, Aborted, or Timed out. They can also
include a risk level and user-authorization assessment for the reviewed
request.

Automatic review uses extra model calls, so it can add to Codex usage. Admins
can constrain it with `allowed_approvals_reviewers`.

### Common sandbox and approval combinations

| Intent                         | Flags / config                                                                                                                      | Effect                                                                                                                                           |
| ------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| Auto (preset)                  | _no flags needed_ or `--sandbox workspace-write --ask-for-approval on-request`                                                      | Codex can read files, make edits, and run commands in the workspace. Codex requires approval to edit outside the workspace or to access network. |
| Safe read-only browsing        | `--sandbox read-only --ask-for-approval on-request`                                                                                 | Codex can read files and run commands within the read-only sandbox. Actions outside the sandbox can require approval.                            |
| Read-only non-interactive (CI) | `--sandbox read-only --ask-for-approval never`                                                                                      | Codex can read files and run commands within the read-only sandbox; it never asks for approval.                                                  |
| Auto-review mode               | `--sandbox workspace-write --ask-for-approval on-request -c approvals_reviewer=auto_review` or `approvals_reviewer = "auto_review"` | Same sandbox boundary as standard on-request mode, but eligible approval requests are reviewed by Auto-review instead of surfacing to the user.  |
| Dangerous full access          | `--dangerously-bypass-approvals-and-sandbox` (alias: `--yolo`)                                                                      | <ElevatedRiskBadge /> No sandbox; no approvals _(not recommended)_                                                                               |

For non-interactive runs, use `codex exec --sandbox workspace-write`; Codex keeps older `codex exec --full-auto` invocations as a deprecated compatibility path and prints a warning.

#### Configuration in `config.toml`

For the broader configuration workflow, see [Config basics](<https://learn.chatgpt.com/docs/config-file/config-basic>), [Advanced Config](<https://learn.chatgpt.com/docs/config-file/config-advanced#approval-policies-and-sandbox-modes>), and the [Configuration Reference](<https://learn.chatgpt.com/docs/config-file/config-reference>).

```toml
# Interactive approvals with a read-only sandbox
approval_policy = "on-request"
sandbox_mode    = "read-only"
allow_login_shell = false # optional hardening: disallow login shells for shell-based tools

# Optional: Allow network in workspace-write mode
[sandbox_workspace_write]
network_access = true

# Optional: granular approval policy
# approval_policy = { granular = {
#   sandbox_approval = true,
#   rules = true,
#   mcp_elicitations = true,
#   request_permissions = false,
#   skill_approval = false
# } }
```

You can also save presets as [profile files](<https://learn.chatgpt.com/docs/config-file/config-advanced#profiles>), then select them with `codex --profile profile-name`:

```toml
# ~/.codex/full_auto.config.toml
approval_policy = "on-request"
sandbox_mode    = "workspace-write"
```

```toml
# ~/.codex/readonly_quiet.config.toml
approval_policy = "never"
sandbox_mode    = "read-only"
```

### Test the sandbox locally

To see what happens when a command runs under the Codex sandbox, use these Codex CLI commands:

```bash
# macOS
codex sandbox macos [--permissions-profile <name>] [--log-denials] [COMMAND]...
# Linux
codex sandbox linux [--permissions-profile <name>] [COMMAND]...
# Windows
codex sandbox windows [--permissions-profile <name>] [COMMAND]...
```

The `sandbox` command is also available as `codex debug`, and the platform helpers have aliases (for example `codex sandbox seatbelt` and `codex sandbox landlock`).

## OS-level sandbox

Codex enforces the sandbox differently depending on your OS:

- **macOS** uses Seatbelt policies and runs commands using `sandbox-exec` with a profile (`-p`) that corresponds to the `--sandbox` mode you selected. When restricted read access enables platform defaults, Codex appends a curated macOS platform policy (instead of broadly allowing `/System`) to preserve common tool compatibility.
- **Linux** uses `bwrap` plus `seccomp` by default.
- **Windows** supports [MXC](<https://learn.chatgpt.com/docs/windows/windows-sandbox#windows-sandbox>), with `elevated` and `unelevated` as legacy fallbacks. In [Windows Subsystem for Linux 2 (WSL2)](<https://learn.chatgpt.com/docs/windows/wsl>), Codex uses the Linux sandbox implementation. WSL1 was supported through Codex `0.114`; starting in `0.115`, the Linux sandbox moved to `bwrap`, so WSL1 is no longer supported.

If you use the Codex IDE extension on Windows, it supports WSL2 directly. Set the following in your VS Code settings to keep the agent inside WSL2 whenever it's available:

```json
{
  "chatgpt.runCodexInWindowsSubsystemForLinux": true
}
```

This ensures the IDE extension inherits Linux sandbox semantics for commands, approvals, and filesystem access even when the host OS is Windows. Learn more in the [WSL guide](<https://learn.chatgpt.com/docs/windows/wsl>).

When running on Windows, prefer MXC when the device and policy support it by setting this in `config.toml`:

```toml
[features]
prefer_mxc = true
```

Keep a permitted legacy implementation in `windows.sandbox` for fallback. See the [Windows setup guide](<https://learn.chatgpt.com/docs/windows/windows-sandbox#enable-mxc>) for configuration and compatibility limits.

When you run Linux in a containerized environment such as Docker, the sandbox may not work if the host or container configuration blocks the namespace, setuid `bwrap`, or `seccomp` operations that Codex needs.

In that case, configure your Docker container to provide the isolation you need, then run `codex` with `--sandbox danger-full-access` (or the `--dangerously-bypass-approvals-and-sandbox` flag) inside the container.

### Run Codex in Dev Containers

If your host cannot run the Linux sandbox directly, or if your organization already standardizes on containerized development, run Codex with Dev Containers and let Docker provide the outer isolation boundary. This works with Visual Studio Code Dev Containers and compatible tools.

Use the [Codex secure devcontainer example](<https://github.com/openai/codex/tree/main/.devcontainer>) as a reference implementation. The example installs Codex, common development tools, `bubblewrap`, and firewall-based outbound controls.

Devcontainers provide substantial protection, but they do not prevent every
  attack. If you run Codex with `--sandbox danger-full-access` or
  `--dangerously-bypass-approvals-and-sandbox` inside the container, a malicious
  project can exfiltrate anything available inside the devcontainer, including
  Codex credentials. Use this pattern only with trusted repositories, and
  monitor Codex activity as you would in any other elevated environment.

The reference implementation includes:

- an Ubuntu 24.04 base image with Codex and common development tools installed;
- an allowlist-driven firewall profile for outbound access;
- VS Code settings and extension recommendations for reopening the workspace in a container;
- persistent mounts for command history and Codex configuration;
- `bubblewrap`, so Codex can still use its Linux sandbox when the container grants the needed capabilities.

To try it:

1. Install Visual Studio Code and the [Dev Containers extension](<https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers>).
2. Copy the Codex example `.devcontainer` setup into your repository, or start from the Codex repository directly.
3. In VS Code, run `Dev Containers: Open Folder in Container...` and select `.devcontainer/devcontainer.secure.json`.
4. After the container starts, open a terminal and run `codex`.

You can also start the container from the CLI:

```bash
devcontainer up --workspace-folder . --config .devcontainer/devcontainer.secure.json
```

The example has three main pieces:

- `.devcontainer/devcontainer.secure.json` controls container settings, capabilities, mounts, environment variables, and VS Code extensions.
- `.devcontainer/Dockerfile.secure` defines the Ubuntu-based image and installed tools.
- `.devcontainer/init-firewall.sh` applies the outbound network policy.

The reference firewall is intentionally a starting point. If you depend on domain allowlisting for isolation, implement DNS rebinding and DNS refresh protections that fit your environment, such as TTL-aware refreshes or a DNS-aware firewall.

Inside the container, choose one of these modes:

- Keep Codex's Linux sandbox enabled if the Dev Container profile grants the capabilities needed for `bwrap` to create the inner sandbox.
- If the container is your intended security boundary, run Codex with `--sandbox danger-full-access` inside the container so Codex does not try to create a second sandbox layer.

## Version control

Codex works best with a version control workflow:

- Work on a feature branch and keep `git status` clean before delegating. This keeps Codex patches easier to isolate and revert.
- Prefer patch-based workflows (for example, `git diff`/`git apply`) over editing tracked files directly. Commit frequently so you can roll back in small increments.
- Treat Codex suggestions like any other PR: run targeted verification, review diffs, and document decisions in commit messages for auditing.

## Monitoring and telemetry

Codex supports opt-in monitoring via OpenTelemetry (OTel) to help teams audit usage, investigate issues, and meet compliance requirements without weakening local security defaults. Telemetry is off by default; enable it explicitly in your configuration.

### Overview

- Codex turns off OTel export by default to keep local runs self-contained.
- When enabled, Codex emits structured log events covering chats, API requests, SSE/WebSocket stream activity, user prompts (redacted by default), tool approval decisions, and tool results.
- Codex tags exported events with `service.name` (originator), CLI version, and an environment label to separate dev/staging/prod traffic.

### Enable OTel (opt-in)

Add an `[otel]` block to your Codex configuration (typically `~/.codex/config.toml`), choosing an exporter and whether to log prompt text.

```toml
[otel]
environment = "staging"   # dev | staging | prod
exporter = "none"          # none | otlp-http | otlp-grpc
log_user_prompt = false     # redact prompt text unless policy allows
```

- `exporter = "none"` leaves instrumentation active but doesn't send data anywhere.
- To send events to your own collector, pick one of:

```toml
[otel]
exporter = { otlp-http = {
  endpoint = "https://otel.example.com/v1/logs",
  protocol = "binary",
  headers = { "x-otlp-api-key" = "${OTLP_TOKEN}" }
}}
```

```toml
[otel]
exporter = { otlp-grpc = {
  endpoint = "https://otel.example.com:4317",
  headers = { "x-otlp-meta" = "abc123" }
}}
```

Codex batches events and flushes them on shutdown. Codex exports only telemetry produced by its OTel module.

### Event categories

Representative event types include:

- `codex.conversation_starts` (model, reasoning settings, sandbox/approval policy)
- `codex.api_request` (attempt, status/success, duration, and error details)
- `codex.sse_event` (stream event kind, success/failure, duration, plus token counts on `response.completed`)
- `codex.websocket_request` and `codex.websocket_event` (request duration plus per-message kind/success/error)
- `codex.user_prompt` (length; content redacted unless explicitly enabled)
- `codex.tool_decision` (approved/denied, source: configuration vs. user)
- `codex.tool_result` (duration, success, output snippet)

Associated OTel metrics (counter plus duration histogram pairs) include `codex.api_request`, `codex.sse_event`, `codex.websocket.request`, `codex.websocket.event`, and `codex.tool.call` (with corresponding `.duration_ms` instruments).

For the full event catalog and configuration reference, see the [Codex configuration documentation on GitHub](<https://github.com/openai/codex/blob/main/docs/config.md#otel>).

### Security and privacy guidance

- Keep `log_user_prompt = false` unless policy explicitly permits storing prompt contents. Prompts can include source code and sensitive data.
- Route telemetry only to collectors you control; apply retention limits and access controls aligned with your compliance requirements.
- Treat tool arguments and outputs as sensitive. Favor redaction at the collector or SIEM when possible.
- Review local data retention settings (for example, `history.persistence` / `history.max_bytes`) if you don't want Codex to save session transcripts under `CODEX_HOME`. See [Advanced Config](<https://learn.chatgpt.com/docs/config-file/config-advanced#history-persistence>) and [Configuration Reference](<https://learn.chatgpt.com/docs/config-file/config-reference>).
- If you run the CLI with network access turned off, OTel export can't reach your collector. To export, allow network access in `workspace-write` mode for the OTel endpoint, or export from Codex cloud with the collector domain on your approved list.
- Review events periodically for approval/sandbox changes and unexpected tool executions.

OTel is optional and designed to complement, not replace, the sandbox and approval protections described above.

## Managed configuration

Enterprise admins can configure Codex security settings for their workspace in [Managed configuration](<https://learn.chatgpt.com/docs/enterprise/managed-configuration>). See that page for setup and policy details.


</details>
