# VS Code Agent 的信任与安全控制

讨论审查、信任边界、沙箱和操作系统限制，以及防护未覆盖的范围和模型局限，帮助控制代码变更。

- 来源：Microsoft／VS Code
- 资料类型：文档源码（含未展开模板）
- 存档版本：`7dde53519982`。
- 文档时间：官方审核：[2026-10-07](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/concepts/trust-and-safety.md>)；源码提交（存档版）：[2026-10-06](<https://github.com/microsoft/vscode-docs/commit/4fe6b03408632f2e9338a970d26afa480e4a022d>)；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/concepts/trust-and-safety.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Microsoft/VS Code/VS Code Agent 的信任与安全控制.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
ContentId: a7b8c9d0-1e2f-3a4b-5c6d-7e8f9a0b1c2d
DateApproved: 10/7/2026
MetaDescription: Understand approvals, review, sandboxing, and security considerations for AI agents in {% data variables.product.prodname_vscode_shortname %}.
MetaSocialImage: ../images/shared/github-copilot-social.png
Keywords:
- copilot
- ai
- trust
- safety
- security
- sandbox
- sandboxing
- review
- checkpoints
- tool approval
- limitations
- prompt injection
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 了解 AI agents 的信任和安全

AI-生成的输出需要审核。 {% data variables.product.prodname_vscode %} 包含多种机制，可让您控制代码库的更改。本文解释了控制机制、AI 限制以及您应该注意的安全注意事项。

<div class="docs-action" data-show-in-doc="false" data-show-in-sidebar="true" title="Learn more about AI safety">
了解{% data variables.product.prodname_vscode_shortname %}中使用AI的控制机制和安全注意事项。

* [了解AI安全](<https://github.com/docs/agents/run/security.md>)

</div>

## 保持控制

Agents可以读取文件、编辑代码、运行终端命令以及调用外部服务。 {% data variables.product.prodname_vscode_shortname %} 的信任模型分层了多种控制机制，因此您仍然可以掌控到达代码库的内容：

* **集成前检查。** Agents可以将编辑直接保存在会话文件夹或工作树中。在提交、合并或创建拉取请求之前检查差异中的更改。对于支持的会话，[检查点](<https://github.com/docs/agents/run/review-code-edits.md#edit-requests-and-restore-checkpoints>)将受影响的工作区文件和聊天恢复到较早的点。
* **批准敏感操作。** 对于 Agent 主机会话中的 [手动权限 ](<https://github.com/docs/agents/run/approvals.md#permission-levels>)，您的批准设置未涵盖的操作需要确认。文件编辑可能会自动批准。当编辑必须在应用前进行确认时，请配置 [ 敏感文件批准 ](<https://github.com/docs/agents/run/review-code-edits.md#edit-sensitive-files>)。
* **限制自主权。** [权限级别](<https://github.com/docs/agents/run/approvals.md#permission-levels>)决定agent自行运行的程度，从每次呼叫批准到广泛的自动批准，直至使用 Autopilot 进行完全自主操作。
* **在OS级别强制执行边界。**[Agent沙箱](<#agent-沙箱>)限制终端命令的文件系统和网络访问，因此自动批准的操作无法逃脱定义的范围。
* **信任边界。** {% data variables.product.prodname_vscode_shortname %} 使用工作区、扩展、MCP 服务器和网络域的信任决策。 Workspace MCP 服务器继承 Workspace 信任。

有关这些控件的逐步配置 - 批准规则、敏感文件保护、沙箱设置、组织策略 - 请参阅 [AI 安全在 {% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/agents/run/security.md>).

在提交之前始终检查 AI- 生成的代码。验证它是否可以处理边缘情况、遵循项目的约定并且不会引入安全问题。停止请求或恢复工作区文件不会撤消已完成的终端命令、网络请求、部署或对外部服务的更改。

## 信任边界

{% data variables.product.prodname_vscode_shortname %} 的安全模型使用信任边界来限制不受信任代码的潜在影响。在边界被视为可信之前，必须先授予信任。单个信任决策可以涵盖相关边界，例如工作区及其 MCP 服务器配置。

* **工作空间**：控制{% data variables.product.prodname_vscode_shortname %}是否启用可以从项目执行代码的任务、调试和工作空间设置等功能。不受信任的工作区在 [ 受限模式 ](<https://github.com/docs/editing/workspaces/workspace-trust.md>) 下运行，这也会禁用 agents。
* **扩展发布者**：控制是否可以安装和运行来自给定发布者的扩展。 {% data variables.product.prodname_vscode_shortname %} 提示您在激活其扩展程序之前 [ 信任发布者 ](<https://github.com/docs/configure/extensions/extension-runtime-security.md>)。
* **MCP服务器**：控制MCP服务器是否可以启动并提供工具。 `.vscode/mcp.json` 或工作区根 `.mcp.json` 中配置的服务器继承工作区信任。来自其他来源的服务器可能需要 [单独的 MCP 服务器信任决策 ](<https://github.com/docs/agent-customization/mcp-servers.md#mcp-server-trust>) 并在配置更改后再次提示。
* **网络域**：控制agent是否可以从URL获取内容。 {% data variables.product.prodname_vscode_shortname %} 会提示您在向域发出请求之前信任该域，该域与 [ 受信任域 ](<https://github.com/docs/editing/editingevolved.md#outgoing-link-protection>) 列表集成。您还可以启用 `setting(chat.agent.networkFilter)` 来限制获取工具和集成浏览器的域。 [沙箱网络控制](<https://github.com/docs/agents/run/agent-sandboxing.md#configure-network-access>)单独应用于终端命令。

您可以随时通过命令面板中的专用命令撤销信任。更改工作空间信任可控制工作空间是否 MCP 服务器可以运行。对于具有单独信任决策的服务器，运行 **MCP：重置信任**。有关配置这些控件的步骤，请参阅 [AI 安全在 {% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/agents/run/security.md>).

## Agent 沙箱

Agent 沙箱使用操作系统级隔离来限制终端命令可以在您的计算机上访问的内容。沙箱不是仅仅依赖于每个操作之前的批准提示，而是强制执行配置的文件系统和网络边界。

在本地会话中，沙箱适用于终端命令及其子进程。在{% data variables.product.prodname_copilot_short %} Agent主机会话中，它主要限制shell执行和子进程。它还可以沙箱 MCP 服务器和 Agent 主机在相应设置处于活动状态时启动的语言服务器。默认情况下，这两个设置均处于活动状态。内置工具和其他非进程工具保留在该沙箱之外，并使用单独的权限检查。默认情况下，打开沙箱不会阻止出站网络访问。了解如何[配置agent沙箱](<https://github.com/docs/agents/run/agent-sandboxing.md>)。

默认情况下，{% data variables.product.prodname_vscode_shortname %} 自动批准在沙箱中运行的终端命令，而无需确认提示，因为它们已经在受控环境中运行。

### 为什么沙箱很重要

基于批准的安全性要求您在运行之前确认每个终端命令或工具调用。虽然这提供了控制，但它有实际限制：

* **批准疲劳。** 重复批准命令可能会导致您不太注意正在批准的内容，尤其是在长时间的 agent 会话期间。

* **解析限制。**自动批准规则使用尽力而为命令解析，该解析具有已知的限制。 Shell 别名、引号串联和复杂的 Shell 语法可以绕过规则并在不被发现的情况下通过。

* ** 提示注入。** 文件、工具输出或网页中的恶意内容可能会尝试欺骗 agent 运行有害命令。如果未经仔细审查就批准，可能会导致意外操作和安全风险。

* **对外部服务的意外操作。** 即使没有恶意，具有网络访问权限的 agent 也可以代表您执行难以逆转的操作。例如，agent 可能会配置云资源、修改基础设施设置、将代码推送到远程存储库，或调用触发部署或财务交易的 API。阻止出站连接或将其限制到支持域过滤的所需域，可以降低沙盒命令的风险。

沙箱通过在 OS 级别强制执行边界来解决这些挑战。沙箱可防止自动批准的命令访问允许范围之外的文件或网络资源。如果需要额外权限，{% data variables.product.prodname_vscode_shortname %} 会提示您在沙箱外运行该命令。您可以将 {% data variables.product.prodname_vscode_shortname %} 配置为在显示提升提示之前在沙箱内尝试该命令。

### 沙箱如何工作

沙盒强制执行两种类型的隔离：

* **文件系统隔离**限制对配置路径的读写访问。它保护敏感位置，例如 SSH 密钥和 shell 配置，并适用于子进程，例如包管理器和构建脚本。
* **网络隔离**阻止出站连接，或者在支持的情况下将其限制到已配置的域。域过滤功能因终端实现和平台而异。

终端和子进程边界使用操作系统保护。其他支持的操作可以使用 Agent 主机进程内部的检查。您可以单独配置文件系统和网络访问。有关默认行为和配置步骤，请参阅 [沙箱 agent 终端命令 ](<https://github.com/docs/agents/run/agent-sandboxing.md>)。

### OS-级别执行

Agent 沙箱依赖于 OS- 级安全原语来实施文件系统和网络限制。由于强制执行发生在内核级别，因此沙箱进程及其所有子进程无法绕过这些边界，即使精心设计了一个命令来尝试它。

|平台|技术|先决条件|
|----------|-----------|---------------|
| macOS |Apple 的沙盒框架（“安全带”）内置于操作系统中。在内核级别实施细粒度的文件系统和网络限制。|没有。开箱即用。|
|Linux 和 WSL2|[bubblewrap](<https://github.com/containers/bubblewrap>) 用于文件系统隔离，`socat` 用于网络代理。|安装所需的软件包：`sudo apt-get install bubblewrap socat`（Debian 和 Ubuntu）或 `sudo dnf install bubblewrap socat`（Fedora）。|
|窗户|Microsoft MXC 进程容器将文件系统和网络策略应用于命令进程。|安装适用的 Windows 安全更新。|

不支持 WSL 版本 1，因为 bubblewrap 需要仅在 WSL2 中可用的 Linux 内核功能（用户命名空间）。

### 沙盒不涵盖哪些内容

Agent 沙箱是其所覆盖进程的附加层。它不是虚拟机或用户帐户边界、独立的安全边界，也不是端点安全的替代品。沙箱不保护您显式注入的凭据。开发人员工具配置和缓存、允许的路径、不受限制的或本地网络访问、非沙盒回退和旁路也会削弱其隔离性。

内置工具和其他非进程工具不被进程沙箱覆盖。 agent的读取、编辑和写入工具直接使用{% data variables.product.prodname_vscode_shortname %}的权限系统。

> [！TIP]
> `setting(chat.agent.networkFilter)` 设置为获取工具和集成浏览器提供网络域过滤，独立于终端沙箱。它不会将域过滤添加到 {% data variables.product.prodname_copilot_short %} Agent 主机内置 shell 或 Windows 终端沙箱。请参阅[沙箱网络功能](<https://github.com/docs/agents/run/agent-sandboxing.md#configure-network-access>)。

使用[审核流程](<https://github.com/docs/agents/run/review-code-edits.md>)和[敏感文件保护](<https://github.com/docs/agents/run/review-code-edits.md#edit-sensitive-files>)来控制这些操作。

[开发容器会话](<https://github.com/docs/agents/run/agents-window.md#run-a-session-in-a-dev-container>)在项目容器内、您的计算机或支持的连接主机上运行Agent主机和工作区工具。这与 Git 工作树和终端沙箱不同，Git 工作树将代码更改分开，终端沙箱限制命令访问。

Dev Container 不会自动阻止对主机或网络的所有访问。其边界取决于容器配置，包括安装的主机文件夹、权限和网络。检查该配置并继续使用适用的审批和安全控制。

Agent 沙盒不断发展以涵盖更多工具和场景。

## AI 需要注意的限制

**输出不正确。**模型可以生成看起来正确但包含错误、使用已弃用的 API 或不处理边缘情况的代码。始终测试 AI- 生成的代码，尤其是影响安全性、数据完整性或关键流程的逻辑。

**提示注入。** 文件、工具输出或网页中的恶意内容可能会尝试重定向 agent 的行为。这就是为什么 {% data variables.product.prodname_vscode_shortname %} 包含工具批准门和信任边界。了解有关 [AI 安全性 ](<https://github.com/docs/agents/run/security.md>) 的更多信息。

将 AI- 生成的输出视为初稿：作为起点很有用，但始终需要您的审查和判断。有关模型如何工作的更多信息，包括不确定性、知识边界和上下文限制，请参阅[语言模型](<https://github.com/docs/agents/concepts/language-models.md>)。

## 相关资源

* [AI安全考虑](<https://github.com/docs/agents/run/security.md>)
* [终端沙箱配置](<https://github.com/docs/agents/run/agent-sandboxing.md>)
* [审查代码编辑](<https://github.com/docs/agents/run/review-code-edits.md>)


## 原文对照

<details>
<summary>展开完整原文</summary>

# Understand trust and safety for AI agents

AI-generated output requires review. {% data variables.product.prodname_vscode %} includes multiple mechanisms to keep you in control of what changes reach your codebase. This article explains the control mechanisms, AI limitations, and security considerations you should be aware of.

<div class="docs-action" data-show-in-doc="false" data-show-in-sidebar="true" title="Learn more about AI safety">
Understand the control mechanisms and safety considerations for using AI in {% data variables.product.prodname_vscode_shortname %}.

* [Learn about AI safety](<https://github.com/docs/agents/run/security.md>)

</div>

## Stay in control

Agents can read files, edit code, run terminal commands, and call external services. {% data variables.product.prodname_vscode_shortname %}'s trust model layers several control mechanisms so you remain in charge of what reaches your codebase:

* **Review before integrating.** Agents can save edits directly in the session folder or worktree. Inspect the changes in a diff before you commit, merge, or create a pull request. For supported sessions, [checkpoints](<https://github.com/docs/agents/run/review-code-edits.md#edit-requests-and-restore-checkpoints>) restore affected workspace files and chat to an earlier point.
* **Approve sensitive actions.** With [Manual permissions](<https://github.com/docs/agents/run/approvals.md#permission-levels>) in Agent Host sessions, actions that aren't covered by your approval settings require confirmation. File edits might be auto-approved. Configure [sensitive-file approval](<https://github.com/docs/agents/run/review-code-edits.md#edit-sensitive-files>) when an edit must require confirmation before it is applied.
* **Constrain autonomy.** [Permission levels](<https://github.com/docs/agents/run/approvals.md#permission-levels>) decide how much the agent runs on its own, from per-call approvals to broad auto-approval, up to fully autonomous operation with Autopilot.
* **Enforce boundaries at the OS level.** [Agent sandboxing](<#agent-sandboxing>) restricts file system and network access for terminal commands so auto-approved actions cannot escape a defined scope.
* **Trust boundaries.** {% data variables.product.prodname_vscode_shortname %} uses trust decisions for workspaces, extensions, MCP servers, and network domains. Workspace MCP servers inherit Workspace Trust.

For step-by-step configuration of these controls — approval rules, sensitive-file protection, sandboxing setup, organization policies — see [AI security in {% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/agents/run/security.md>).

Always review AI-generated code before committing. Verify that it handles edge cases, follows your project's conventions, and doesn't introduce security issues. Stopping a request or restoring workspace files doesn't reverse completed terminal commands, network requests, deployments, or changes to external services.

## Trust boundaries

{% data variables.product.prodname_vscode_shortname %}'s security model uses trust boundaries to limit the potential impact of untrusted code. Trust must be granted before a boundary is considered trusted. A single trust decision can cover related boundaries, such as a workspace and its MCP server configuration.

* **Workspace**: controls whether {% data variables.product.prodname_vscode_shortname %} enables features like tasks, debugging, and workspace settings that can execute code from the project. An untrusted workspace runs in [restricted mode](<https://github.com/docs/editing/workspaces/workspace-trust.md>), which also disables agents.
* **Extension publisher**: controls whether extensions from a given publisher can be installed and run. {% data variables.product.prodname_vscode_shortname %} prompts you to [trust the publisher](<https://github.com/docs/configure/extensions/extension-runtime-security.md>) before activating their extensions.
* **MCP server**: controls whether an MCP server can start and provide tools. Servers configured in `.vscode/mcp.json` or workspace-root `.mcp.json` inherit Workspace Trust. Servers from other sources can require a [separate MCP server trust decision](<https://github.com/docs/agent-customization/mcp-servers.md#mcp-server-trust>) and prompt again after configuration changes.
* **Network domain**: controls whether the agent can fetch content from a URL. {% data variables.product.prodname_vscode_shortname %} prompts you to trust a domain before making requests to it, integrated with the [Trusted Domains](<https://github.com/docs/editing/editingevolved.md#outgoing-link-protection>) list. You can also enable `setting(chat.agent.networkFilter)` to restrict domains for the fetch tool and integrated browser. [Sandbox network controls](<https://github.com/docs/agents/run/agent-sandboxing.md#configure-network-access>) apply separately to terminal commands.

You can revoke trust at any time through dedicated commands in the Command Palette. Changing Workspace Trust controls whether workspace MCP servers can run. For servers with a separate trust decision, run **MCP: Reset Trust**. For steps to configure these controls, see [AI security in {% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/agents/run/security.md>).

## Agent sandboxing

Agent sandboxing uses operating system-level isolation to restrict what terminal commands can access on your machine. Instead of relying solely on approval prompts before each action, sandboxing enforces configured file system and network boundaries.

In Local sessions, sandboxing applies to terminal commands and their child processes. In {% data variables.product.prodname_copilot_short %} Agent Host sessions, it primarily confines shell execution and child processes. It can also sandbox MCP servers and language servers that the Agent Host launches when the corresponding settings are active. Both settings are active by default. Built-in and other non-process tools remain outside this sandbox and use separate permission checks. Turning on sandboxing does not block outbound network access by default. Learn how to [configure agent sandboxing](<https://github.com/docs/agents/run/agent-sandboxing.md>).

By default, {% data variables.product.prodname_vscode_shortname %} automatically approves terminal commands that run in the sandbox without a confirmation prompt because they already run in a controlled environment.

### Why sandboxing matters

Approval-based security requires you to confirm each terminal command or tool call before it runs. While this provides control, it has practical limits:

* **Approval fatigue.** Repeatedly approving commands can cause you to pay less attention to what you're approving, especially during long agent sessions.

* **Parsing limitations.** Auto-approval rules use best-effort command parsing, which has known limitations. Shell aliases, quote concatenation, and complex shell syntax can bypass the rules and slip through undetected.

* **Prompt injection.** Malicious content in files, tool outputs, or web pages can attempt to trick the agent into running harmful commands. If you approve without careful review, it might result in unintended actions and security risks.

* **Unintended actions on external services.** Even without malicious intent, an agent with network access can perform actions on your behalf that are difficult to reverse. For example, the agent might provision cloud resources, modify infrastructure settings, push code to a remote repository, or call an API that triggers a deployment or a financial transaction. Blocking outbound connections, or limiting them to required domains where domain filtering is supported, reduces this risk for sandboxed commands.

Sandboxing addresses these challenges by enforcing boundaries at the OS level. The sandbox prevents auto-approved commands from accessing files or network resources outside the permitted scope. If additional permissions are required, {% data variables.product.prodname_vscode_shortname %} prompts you to run the command outside the sandbox. You can configure {% data variables.product.prodname_vscode_shortname %} to try the command inside the sandbox before showing that elevation prompt.

### How sandboxing works

Sandboxing enforces two types of isolation:

* **File system isolation** limits read and write access to configured paths. It protects sensitive locations, such as SSH keys and shell configuration, and applies to child processes such as package managers and build scripts.
* **Network isolation** blocks outbound connections or, where supported, limits them to configured domains. Domain-filtering capabilities vary by terminal implementation and platform.

Terminal and child-process boundaries use operating system protections. Other supported operations can use checks inside the Agent Host process. You can configure file system and network access separately. For default behavior and configuration steps, see [Sandbox agent terminal commands](<https://github.com/docs/agents/run/agent-sandboxing.md>).

### OS-level enforcement

Agent sandboxing relies on OS-level security primitives to enforce file system and network restrictions. Because the enforcement happens at the kernel level, sandboxed processes and all of their child processes cannot bypass these boundaries, even if a command is crafted to attempt it.

| Platform | Technology | Prerequisites |
|----------|-----------|---------------|
| macOS | Apple's sandboxing framework ("Seatbelt"), built into the operating system. Enforces fine-grained file system and network restrictions at the kernel level. | None. Works out of the box. |
| Linux and WSL2 | [bubblewrap](<https://github.com/containers/bubblewrap>) for file system isolation and `socat` for network proxying. | Install required packages: `sudo apt-get install bubblewrap socat` (Debian and Ubuntu) or `sudo dnf install bubblewrap socat` (Fedora). |
| Windows | Microsoft MXC process containers apply file system and network policies to the command process. | Install the applicable Windows security update. |

WSL version 1 is not supported because bubblewrap requires Linux kernel features (user namespaces) that are only available in WSL2.

### What sandboxing does not cover

Agent sandboxing is an added layer for the processes it covers. It is not a virtual machine or user-account boundary, a standalone security boundary, or a replacement for endpoint security. The sandbox does not protect credentials that you explicitly inject. Developer-tool configuration and caches, allowed paths, unrestricted or local network access, unsandboxed fallback, and bypass also weaken its isolation.

Built-in and other non-process tools are not covered by the process sandbox. The agent's read, edit, and write tools use {% data variables.product.prodname_vscode_shortname %}'s permission system directly.

> [!TIP]
> The `setting(chat.agent.networkFilter)` setting provides network domain filtering for the fetch tool and integrated browser, independently of terminal sandboxing. It does not add domain filtering to the {% data variables.product.prodname_copilot_short %} Agent Host built-in shell or the Windows terminal sandbox. See [sandbox network capabilities](<https://github.com/docs/agents/run/agent-sandboxing.md#configure-network-access>).

Use the [review flow](<https://github.com/docs/agents/run/review-code-edits.md>) and [sensitive file protection](<https://github.com/docs/agents/run/review-code-edits.md#edit-sensitive-files>) to control these operations.

A [Dev Container session](<https://github.com/docs/agents/run/agents-window.md#run-a-session-in-a-dev-container>) runs the Agent Host and workspace tools inside the project's container, on your machine or a supported connected host. This is different from a Git worktree, which separates code changes, and terminal sandboxing, which restricts command access.

A Dev Container does not automatically block all access to the host or network. Its boundary depends on the container configuration, including mounted host folders, permissions, and networking. Review that configuration and continue to use the applicable approval and security controls.

Agent sandboxing continues to evolve to cover more tools and scenarios.

## AI limitations to watch for

**Incorrect output.** Models can generate code that looks correct but contains bugs, uses deprecated APIs, or doesn't handle edge cases. Always test AI-generated code, especially for logic that affects security, data integrity, or critical flows.

**Prompt injection.** Malicious content in files, tool outputs, or web pages can attempt to redirect the agent's behavior. This is why {% data variables.product.prodname_vscode_shortname %} includes tool approval gates and trust boundaries. Learn more about [AI security](<https://github.com/docs/agents/run/security.md>).

Treat AI-generated output as a first draft: useful as a starting point, but always requiring your review and judgment. For more on how models work, including nondeterminism, knowledge boundaries, and context limits, see [Language models](<https://github.com/docs/agents/concepts/language-models.md>).

## Related resources

* [AI security considerations](<https://github.com/docs/agents/run/security.md>)
* [Terminal sandbox configuration](<https://github.com/docs/agents/run/agent-sandboxing.md>)
* [Reviewing code edits](<https://github.com/docs/agents/run/review-code-edits.md>)


</details>
