# Claude Code 桌面应用

介绍桌面中的会话启动、上下文输入、权限选择、代码编辑、终端和应用预览，并涵盖 Git 隔离、并行工作与其他桌面能力。

- 来源：Anthropic／Claude Code
- 资料类型：存档文档
- 文档时间：官网现页更新：[2026-10-11](<https://code.claude.com/docs/en/desktop>)；本地抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://code.claude.com/docs/en/desktop.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude Code/Claude Code 桌面应用.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

> ## 文档索引
> 在以下位置获取完整的文档索引： https://code.claude.com/docs/llms.txt
> 在进一步探索之前使用此文件来发现所有可用页面。

# 桌面应用程序

> 充分利用Claude Code桌面：具有Git隔离的并行会话、拖放窗格布局、集成终端和文件编辑器、侧面聊天、计算机使用、从手机调度会话、视觉差异审查、应用程序预览、PR监控、连接器和企业配置。

Claude 桌面应用程序具有三个选项卡：**聊天** 用于对话，**Cowork** 用于[调度和长期代理工作](<https://claude.com/product/cowork>)，以及**软件开发代码**。此页面是“代码”选项卡的参考。



  

#### [下载macOS](<https://claude.ai/api/desktop/darwin/universal/dmg/latest/redirect?utm_source=claude_code&utm_medium=docs>)

适用于英特尔和 Apple Silicon 的通用构建
  


  

#### [Windows 版下载](<https://claude.ai/api/desktop/win32/x64/setup/latest/redirect?utm_source=claude_code&utm_medium=docs>)

对于 x64 处理器
  


  

#### [获取适用于 Linux 的 Claude（测试版）](<https://code.claude.com/docs/en/desktop-linux>)

Ubuntu 和 Debian 的 apt 或 .deb
  


对于 Windows ARM64，请下载 [ARM64 安装程序 ](<https://claude.ai/api/desktop/win32/arm64/setup/latest/redirect?utm_source=claude_code\&utm_medium=docs>)。在 Linux 上，使用 apt 安装；请参阅 [Claude Linux 上的桌面 ](<https://code.claude.com/docs/en/desktop-linux>)。

安装后，启动Claude，登录，然后单击**代码**选项卡。有关第一次会话的演练，请参阅 [ 入门指南 ](<https://code.claude.com/docs/en/desktop-quickstart>)。

在“代码”选项卡中，每个对话都是一个**会话**：它有自己的聊天历史记录和项目文件夹，独立于任何其他会话。侧边栏列出了您的会话，并允许您并行运行多个会话。在会话中您可以：

* [审查并评论差异](<#使用-diff-视图查看更改>)，然后[观看生成的PR到CI](<#监控拉取请求状态>)
* [在浏览器窗格中预览正在运行的应用程序](<#预览您的应用程序>)，同时Claude验证其自身的更改，并[在其旁边打开外部站点](<#浏览外部网站>)
* 在 iOS 模拟器窗格中观看 Claude [ 运行并测试您的 iOS 应用程序 ](<https://code.claude.com/docs/en/desktop-ios-simulator>)
* [并排排列聊天、差异、浏览器、终端和文件编辑器的窗格](<#安排你的工作空间>)
* 提出一个 [ 附带问题 ](<#在不影响会议的情况下提出附带问题>)，该问题使用会话的上下文而不破坏它
* 让Claude [查看、发送消息或存档您的其他会话](<#跨会话工作>)
* [连接外部工具](<#连接外部工具>)，例如GitHub、Slack和Linear
* 让Claude [打开应用程序并控制您的屏幕](<#让claude使用你的电脑>)
* 在您的计算机上、[云](<#在云中运行长时间运行的任务>)或[SSH](<#ssh-会话>)上运行

对于[计划的重复工作](<https://code.claude.com/docs/en/desktop-scheduled-tasks>)、[键盘快捷键](<#键盘快捷键>)或[从手机发送任务](<#来自-dispatch-的会话>)，请参阅链接的页面和部分。如果您已经使用基于终端的 CLI，请参阅 [CLI 比较 ](<#来自cli>) 了解继承的内容。

## 开始会话

在发送第一条消息之前，请在提示区域中配置四项内容：

* **环境**: 选择哪里 Claude 运行。选择 **本地** 对于您的机器， **云** 对于一个 [云会话](<#云会议>) 关闭应用程序后继续， [**SSH 连接**](<#ssh-会话>) 对于您管理的远程计算机，或在 Windows 上 [**WSL 分布**](<https://code.claude.com/docs/en/desktop-wsl>)。参见 [环境配置](<#环境配置>).
* **项目文件夹**：选择Claude所在的文件夹或存储库。对于云会话，您可以添加[多个存储库](<#在云中运行长时间运行的任务>)。
* **型号**：从发送按钮旁边的下拉列表中选择[型号](<https://code.claude.com/docs/en/model-config#available-models>)。您可以在会话期间更改此设置。
* **权限模式**：从[模式选择器](<#选择权限模式>)中选择Claude拥有多少自主权。您可以在会话期间更改此设置。

输入您的任务并按**输入**开始。每个会话都会跟踪自己的上下文并独立更改。

## 使用代码

为 Claude 提供正确的上下文，控制它自己执行的操作，并查看它更改的内容。

### 使用提示框

输入您想要Claude执行的操作，然后按**输入**发送。 Claude 读取您的项目文件，进行更改，并根据您的 [ 权限模式 ](<#选择权限模式>) 运行命令。您可以随时重定向 Claude：单击停止按钮立即中断，或输入更正并按 **Enter** 发送它而不停止正在运行的操作。 Claude 当前操作完成后立即读取更正，并在下一步之前进行调整。

提示框旁边的 **+** 按钮可让您访问文件附件、[技能](<#使用技巧>)、[连接器](<#连接外部工具>)和[插件](<#安装插件>)。

### 接受建议的提示

Claude回复后，“代码”选项卡可以在空提示框中将建议的下一个提示显示为灰色文本。 Claude Code [ 根据您的对话生成每条建议 ](<https://code.claude.com/docs/en/interactive-mode#prompt-suggestions>)，并附带一个简短的后台请求，该请求计入您的计划的使用限制或您的 API 费用。

* **使用建议**：按**Tab**或**右箭头**将其放置在提示框中，如果需要编辑它，然后按**输入**发送。在接受建议之前按 ** 输入 ** 不会发送建议。
* **编写您自己的提示**：开始输入。仅当提示框为空且没有附加文件时才会显示建议。

Go 至 ** 设置 > Claude Code** 并关闭 ** 会话 ** 下的 ** 提示建议 **，以在每个会话下次启动或恢复时停止建议。

### 将文件和上下文添加到提示中

提示框支持两种方式引入外部上下文：

* **@提及文件**：键入 `@` 后跟文件名，以将文件添加到对话上下文中。 Claude 然后可以读取并引用该文件。 @mention 在云或 WSL 会话中不可用。
* **附加文件**：使用附件按钮将图像、PDF 和其他文件附加到提示中，或将文件直接拖放到提示中。这对于共享错误的屏幕截图、设计模型或参考文档非常有用。

### 选择权限模式

权限模式控制 Claude 在会话期间拥有多少自主权：是否在编辑文件、运行命令或两者之前询问。您可以使用发送按钮旁边的模式选择器随时切换权限模式。要亲自批准每项更改，请切换到“手动”。

要为新的本地会话设置默认模式，请将 `permissions.defaultMode` 添加到您的 [ 设置文件 ](<https://code.claude.com/docs/en/settings#where-settings-live>)。桌面应用程序读取与 CLI 相同的设置文件。您在选择器中选择的模式会按文件夹进行记忆，并且优先于该文件夹的 `defaultMode`，计划除外，该模式仅适用于当前会话。

|模式|设置键|行为|
| - | - | - |
|**说明书**| `default` |Claude 在编辑文件或运行命令之前询问。您会看到差异并可以接受或拒绝每个更改。|
|**接受编辑**| `acceptEdits` |Claude 自动接受文件编辑和常见文件系统命令，例如 `mkdir`、`touch` 和 `mv`，但在运行其他终端命令之前仍会询问。当您信任文件更改并希望更快地迭代时，请使用此选项。|
|**计划**| `plan` |Claude 读取文件并运行命令进行探索，然后提出一个计划，而无需编辑源代码。适合您想要首先查看方法的复杂任务。|
|**自动**| `auto` |Claude运行无常规提示；在运行 shell 命令和网络请求等操作之前，后台分类器会检查它们是否与您的请求一致。当[自动模式可用时出现](<#auto-mode-availability>)；没有单独的设置开关。|
|**绕过权限**| `bypassPermissions` |Claude 在没有权限提示的情况下运行，但 [ 无模式自动批准的操作 ](<https://code.claude.com/docs/en/permission-modes#actions-no-mode-auto-approves>)、Claude [ 在外部站点上操作时的安全分类器 ](<#浏览外部网站>) 或桌面操作除外Claude 始终首先询问，例如 [ 归档会话 ](<#跨会话工作>)。相当于CLI中的`--dangerously-skip-permissions`。在 Pro 和 Max 套餐中，在“设置”→“允许绕过权限模式”下的 Claude Code 中启用它；在团队和企业计划中，没有设置切换，而是组织策略控制它。仅在沙盒容器或虚拟机中使用此功能。|

早期版本的“代码”选项卡将这些模式标记为询问权限、自动接受编辑和计划模式。

`dontAsk`权限模式仅在[CLI](<https://code.claude.com/docs/en/permission-modes#allow-only-pre-approved-tools-with-dontask-mode>)中有效。



**提示：**

在计划中启动复杂的任务，以便 Claude 在进行更改之前制定出一种方法。批准计划后，切换到“接受编辑”或“手动”来执行它。有关此工作流程的更多信息，请参阅 [ 先探索，然后规划，然后编码 ](<https://code.claude.com/docs/en/best-practices#explore-first-then-plan-then-code>)。


云会话支持接受编辑、计划和自动。接受编辑对应于 `default` 模式：云会话预先批准文件编辑，因此选择器显示接受编辑而不是手动。绕过权限在云会话中不可用，包括 [ 自托管环境 ](<https://code.claude.com/docs/en/self-hosted-environments>) 中的会话。

企业管理员可以限制可用的权限模式。具体参见【[】企业配置【](<#企业配置>)】。

<h4 id="auto-mode-availability">
 自动模式可用性
</h4>

自动模式适用于 Anthropic API 上的所有用户，并且需要 Claude Opus 4.6 或更高版本、十四行诗 4.6 或更高版本、俳句 5.5 或 [ 寓言型号](<https://code.claude.com/docs/en/model-config#work-with-fable>)。组织管理员可以使用 [ 托管设置 ](<#托管设置>) 中的 `disableAutoMode` 键关闭自动模式。

在将桌面路由到的企业部署中 Google 云的 Agent 平台，默认也有自动模式；看到 [基岩上的自动模式， Agent 平台或代工厂](<https://code.claude.com/docs/en/permission-modes#enable-auto-mode-on-bedrock-agent-platform-or-foundry>) 对于支持的型号。

### 预览您的应用程序

Claude 可以启动开发服务器并在浏览器窗格中打开它以验证其更改。这适用于前端 Web 应用程序以及后端服务器：Claude 可以测试 API 端点、查看服务器日志并迭代它发现的问题。在大多数情况下，Claude在编辑项目文件后会自动启动服务器。您也可以随时要求Claude预览。默认情况下，Claude [会在每次编辑后自动验证](<#自动验证更改>) 更改。

浏览器窗格还可以打开项目中的静态 HTML 文件、PDF、图像和视频。单击聊天中的 HTML、PDF、图像或视频路径以将其打开。

从浏览器窗格中，您可以：

* 直接在浏览器窗格中与正在运行的应用程序交互
* 观看 Claude 自动验证其自身的更改：它会截取屏幕截图、检查 DOM、单击元素、填写表单并修复发现的问题
* 从浏览器窗格标题中的 ** 开发服务器 ** 菜单启动或停止服务器，或立即停止所有服务器
* 使用浏览器窗格的 **⋮** 菜单中的 **保留 cookie** 选择浏览器是否在退出应用程序后保留 cookie，这样您在开发过程中无需再次登录

Claude 根据您的项目创建初始服务器配置。如果您的应用程序使用自定义开发命令，请编辑 `.claude/launch.json` 以匹配您的设置。请参阅 [配置预览服务器 ](<#配置预览服务器>) 以获取完整参考。

要清除浏览器保存的数据，请在浏览器窗格的**⋮**菜单中选择**清除浏览数据**。要完全关闭浏览器，请在 ** 设置 > Claude Code** 中关闭 ** 浏览器工具 **。

### 浏览外部网站

浏览器窗格是一个选项卡式浏览器，因此您可以打开文档、问题跟踪器或正在运行的应用程序旁边的任何其他站点。要打开浏览器，请在 macOS 上按 **Cmd+Shift+B** 或在 Windows 上按 **Ctrl+Shift+B**，或单击会话标题中的 **浏览器**酒吧。您可以登录到该窗格中的站点，包括弹出登录流程，例如 Google OAuth。

第一次在聊天中单击外部链接时，会出现一个对话框，询问链接是在浏览器窗格还是在默认浏览器中打开。要稍后更改您的选择，请使用浏览器窗格的 **⋮** 菜单中的 **在内置浏览器中打开链接 **。 **Cmd**-单击macOS或**Ctrl**-单击Windows直接在默认浏览器中打开链接。

Claude 可以使用与 [ 验证您的应用程序 ](<#预览您的应用程序>) 相同的工具读取外部页面并与之交互，并进行两项额外的安全检查：

* 安全分类器会审查 Claude 在每种权限模式下在外部页面上的写入操作，例如单击和键入。这些分类器与 [ 自动模式 ](<#选择权限模式>) 使用的分类器相同，当它们标记某个操作时，无论模式如何，您都会收到权限提示。
* 在除“自动”和“绕过”权限之外的权限模式中，在 Claude 导航到新站点之前也适用域白名单检查。

#### 批准 Claude 在网站上的操作

Claude第一次对外部站点进行操作时，会出现一个权限卡，并且Claude等待您的选择：**允许一次**，**始终允许**，或**否认**。 **允许一次**批准该操作而不保存任何内容。 **始终允许** 会在您的设备上保存对该网站的批准，您可以在“设置”中撤销它。每个站点都需要自己的批准，包括子域。您的本地开发服务器和项目文件不需要批准，因此 [ 自动验证 ](<#自动验证更改>) 可以在没有提示的情况下继续工作。

即使在经过批准的网站上， Claude 如果没有您的输入，不会购买商品、创建帐户或绕过验证码。在浏览器窗格中浏览使用与在浏览器窗格中相同的安全模型 [Claude 在 Chrome 扩展程序中](<https://code.claude.com/docs/en/chrome>)。参见 [使用 Claude 安全地在 Chrome 中](<https://support.claude.com/en/articles/12902428-using-claude-in-chrome-safely>) 为如何 Claude 处理敏感站点和危险行为。

#### 在浏览器和 Chrome 扩展程序之间进行选择

浏览器窗格使用干净的浏览器配置文件，与您的个人浏览器分开，没有保存的登录信息或历史记录。使用它来构建和测试您的应用程序以及不需要您身份的网站。当你想要的时候 Claude 要在登录会话中充当您的角色，请使用 [Claude 在 Chrome 扩展程序中](<https://code.claude.com/docs/en/chrome>) 相反，它共享您的浏览器的登录状态。

#### 限制您组织的外部浏览

浏览器遵循与 Chrome 扩展中的 Claude 相同的 [ 站点允许列表和阻止列表控制 ](<https://support.claude.com/en/articles/13065128-claude-in-chrome-admin-controls>)。如果您的组织已经为扩展程序配置了这些列表，则浏览器会自动考虑它们。管理员还可以通过[`browserExternalPageTools`托管设置](<#托管设置>)关闭外部页面上Claude的工具。禁用工具后，用户仍然可以访问外部站点； Claude 的工具无法读取或操作它们。

要完全关闭外部浏览，请将 [`disableBrowserExternalNavigation` 托管设置 ](<#托管设置>) 设置为 `true`。这会阻止浏览器中的所有外部导航，包括组织白名单上的网站；本地主机开发服务器和文件预览继续工作。使用`browserExternalPageTools`可以让用户在没有Claude工具的情况下继续浏览外部网站，并使用`disableBrowserExternalNavigation`来阻止用户和Claude的外部网站。

### 使用 diff 视图查看更改

Claude 对代码进行更改后，差异视图可让您在创建拉取请求之前逐个文件地查看修改。

当 Claude 更改文件时，会出现差异统计指示器，显示添加和删除的行数，例如 `+12 -1`。单击此指示器可打开差异查看器，该查看器在左侧显示文件列表，在右侧显示每个文件的更改。

要对特定行进行评论，请单击差异中的任意行以打开评论框。输入您的反馈并按 **Enter** 添加评论。添加多行评论后，一次性提交所有评论：

* **macOS**：按**Cmd+Enter**
* **Windows**：按**Ctrl+Enter**

Claude 读取您的评论并进行所需的更改，这些更改将显示为您可以查看的新差异。

### 检查您的代码

要让 Claude 在提交之前检查您的更改，请在 [ 提示框 ](<#使用提示框>) 中输入 `/code-review`。审查完成后，调查结果将出现在对话中。

在本地、[SSH](<#ssh-会话>) 和 [WSL](<https://code.claude.com/docs/en/desktop-wsl>) 会话中，结果显示为按文件分组的 ** 代码审查 ** 卡。使用卡片对它们进行操作：

* 单击 **Walk through in diff** 打开 diff 视图并一次逐步浏览结果。当前差异中的发现会出现在其所在行，您可以在其中单击 **修复此问题** 或将其关闭。
* 单击 **应用修复 ** 要求 Claude 修复仍处于打开状态的调查结果。

在任何会话中，您还可以在提示框中询问 Claude 以修复审核发现的问题。有关 `/code-review` 查找的内容及其采用的参数，请参阅 [在本地查看差异 ](<https://code.claude.com/docs/en/code-review#review-a-diff-locally>)。

### 监控拉取请求状态

打开拉取请求后，会话中会出现 CI 状态栏。 Claude Code 使用 GitHub CLI 轮询检查结果和表面故障。

* **自动修复CI和地址注释**：启用后，Claude自动尝试通过读取失败输出和迭代来修复失败的CI检查。在本地会话中，当作者是存储库所有者、组织成员、协作者或 GitHub 应用程序时，Claude 还会处理除您之外的其他人留下的新审阅评论。
* **就绪时自动合并**：启用后，一旦所有检查通过，Claude 就会合并 PR。合并方法是squam。首先在您的 [GitHub 存储库设置 ](<https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/configuring-pull-request-merges/managing-auto-merge-for-pull-requests-in-your-repository>) 中启用自动合并；没有它，Claude无法合并PR。

要打开这些，请单击 **CI** 在状态栏中。一旦会话自动存档 PR 合并或关闭，打开 [自动存档](<#与会话并行工作>) 在 **设置 > Claude Code**.



**注释：**

PR 监控需要在您的计算机上安装 [GitHub CLI (`gh`)](<https://cli.github.com/>) 并进行身份验证。如果未安装 `gh`，Desktop 会在您第一次尝试创建 PR 时提示您安装。


## 安排你的工作空间

“代码”选项卡围绕可按任何布局排列的窗格构建：聊天、差异、浏览器、终端、文件、计划、任务和子代理，以及 macOS 上的 [iOS 模拟器 ](<https://code.claude.com/docs/en/desktop-ios-simulator>)。拖动窗格的标题以重新定位它，或拖动窗格边缘以调整其大小。在 macOS 上按 **Cmd+\\** 或在 Windows 上按 **Ctrl+\\** 可关闭焦点窗格。单击会话标题栏中的 ** 终端 **、** 更改 ** 或 ** 浏览器 ** 以打开终端、差异或浏览器窗格。它们旁边的 **⋮** 菜单会打开更多窗格，例如 ** 文件 **，并在窗口太窄而无法显示这些按钮时保留这些按钮。

要跨屏幕工作，请将差异或终端等窗格弹出到其自己的窗口中，然后在完成后将其停靠回去。 Claude 继续在主窗口中工作。



**注释：**

本节中的窗格布局、终端、文件编辑器和查看模式需要 Claude 桌面 v1.2581.0 或更高版本。打开 **Claude → 检查 macOS 上的更新 ** 或 ** 帮助 → 检查 Windows 上的更新 ** 进行更新。


### 在终端中运行命令

集成终端可让您在会话期间运行命令，而无需切换到其他应用程序。单击会话标题栏中的 ** 终端 ** 或在 macOS 或 Windows 上按 **Ctrl+\`**。终端在会话的工作目录中打开，并与 Claude 共享相同的环境，因此“npm test` or `git status”等命令会看到 Claude 正在编辑的相同文件。要打开第二个终端选项卡，请单击终端窗格标题中的 **+**，或右键单击聊天中的文件夹以选择 **在终端中打开 **。该终端在本地和 [SSH](<#ssh-会话>) 会话中可用。

### 打开和编辑文件

单击聊天或差异查看器中的文件路径以在文件窗格中将其打开。 HTML、PDF、图像和视频路径改为在 [浏览器窗格](<#预览您的应用程序>) 中打开。进行现场编辑并单击**保存**将其写回。如果文件在您打开后在磁盘上发生了更改，该窗格会向您发出警告并允许您覆盖或放弃。单击 ** 放弃 ** 以恢复编辑，或单击窗格标题中的路径以复制绝对路径。

文件窗格在本地和 SSH 会话中可用。对于云会话，请要求 Claude 进行更改。

### 在其他应用程序中打开文件

右键单击聊天、差异查看器或文件窗格中的任何文件路径以打开上下文菜单：

* **附加为上下文**：将文件添加到下一个提示中
* **在**中打开：在已安装的编辑器中打开文件，例如VS Code、Cursor或Zed
* **在 macOS 上的 Finder 中显示**，**在 Windows 上的资源管理器中显示**：打开包含的文件夹
* **复制路径**：复制绝对路径到剪贴板

### 切换视图模式

查看模式控制聊天记录中显示的详细信息量。要切换视图模式，请从会话标题旁边的插入符号中打开会话菜单，然后选择 ** 脚本视图 **，或在 macOS 或 Windows 上按 **Ctrl+O** 循环浏览它们。仅当 Claude 在您正在查看的会话中产生思考后，思考模式才会出现在菜单中。

|模式|它显示了什么|
| - | - |
|**正常**|工具调用折叠成摘要，并带有全文响应|
|**思考**|工具调用折叠成摘要，加上Claude的思考|
|**详细**|每个工具调用、文件读取和中间步骤Claude所采取的，加上Claude的思考|

使用 Thinking 来遵循 Claude 的推理，工具调用仍然崩溃。调试 Claude 采取特定操作的原因时使用详细信息。 Claude 1.46388.1 之前的桌面版本还列出了摘要模式，并且更新后仍设置为摘要的会话会以“正常”打开。

### 键盘快捷键

在 macOS 上按 **Cmd+/** 或在 Windows 上按 **Ctrl+/** 可查看“代码”选项卡中的所有可用快捷方式。在 Windows 上，使用 **Ctrl** 代替 **Cmd** 作为以下快捷方式。会话循环、终端切换和视图模式切换在每个平台上都使用 **Ctrl**。

|快捷方式|行动|
| - | - |
| `Cmd` `/` |显示键盘快捷键|
| `Cmd` `N` |新会话|
| `Cmd` `W` |结束会议|
| `Ctrl` `Tab` / `Ctrl` `Shift` `Tab` |下一个或上一个会话|
| `Cmd` `Shift` `]` / `Cmd` `Shift` `[` |下一个或上一个会话|
| `Esc` |停止Claude的回应|
| `Tab` / `Right arrow` |[接受空提示框中的建议提示](<#接受建议的提示>)|
| `Cmd` `Shift` `D` |切换差异窗格|
| `Cmd` `Shift` `B` |切换浏览器窗格|
| `Cmd` `Shift` `S` |在浏览器中选择一个元素|
| `Ctrl` `` ` `` |切换终端窗格|
| `Cmd` `\` |关闭聚焦窗格|
| `Cmd` `;` |打开侧边聊天|
| `Ctrl` `O` |循环查看模式|
| `Cmd` `Shift` `M` |打开权限模式菜单|
| `Cmd` `Shift` `I` |打开模型菜单|
| `Cmd` `Shift` `E` |打开努力菜单|
| `1`–`9` |在打开的菜单中选择项目|

这些快捷键适用于“代码”选项卡。在桌面中，`Shift+Tab` 不会像在终端的 [ 交互模式 ](<https://code.claude.com/docs/en/interactive-mode#keyboard-shortcuts>) 中那样循环权限模式。

### 检查使用情况

单击模型选择器旁边的使用情况环可查看您当前的上下文窗口使用情况以及该期间的计划使用情况。上下文使用是针对每个会话的；计划使用情况在您的所有 Claude Code 表面上共享。

## 让Claude使用你的电脑

使用计算机可以让 Claude 打开您的应用程序、控制您的屏幕并按照您的方式直接在您的计算机上工作。要求 Claude 与没有 CLI 的桌面工具交互，或者自动化只能通过 GUI 工作的东西。为了运行和测试 iOS 应用程序，桌面会打开专用的 [iOS 模拟器窗格 ](<https://code.claude.com/docs/en/desktop-ios-simulator>)，而不是控制您的屏幕；该窗格无需启用计算机即可工作。



**注释：**

计算机使用是 macOS 和 Windows 上的研究预览，需要 Pro 或 Max 计划。它不适用于团队或企业计划。 Claude 桌面应用程序必须正在运行。


默认情况下，计算机的使用处于关闭状态。 [先在设置](<#启用计算机使用>)中启用它，然后Claude才能控制您的屏幕。在macOS上，您还需要授予辅助功能和屏幕录制权限。

在macOS上，计算机使用也可以在后台运行：Claude在您继续工作的同时在您批准的应用程序中运行。



**注意：**

与 [ 沙盒 Bash 工具 ](<https://code.claude.com/docs/en/sandboxing>) 不同，计算机使用在您的实际桌面上运行，可以访问您批准的任何内容。 Claude 检查每个操作并标记来自屏幕内容的潜在提示注入，但信任边界不同。请参阅 [计算机使用安全指南 ](<https://support.claude.com/en/articles/14128542>) 了解最佳实践。


### 当使用计算机时

Claude 有多种与应用程序或服务交互的方式，其中计算机使用最广泛且速度最慢。它首先尝试最精确的工具：

* 如果您有用于服务的 [ 连接器 ](<#连接外部工具>)，则 Claude 使用该连接器。
* 如果任务是 shell 命令，Claude 使用 Bash。
* 如果任务是浏览器工作并且您有 [Claude 在 Chrome 中](<https://code.claude.com/docs/en/chrome>) 设立, Claude 使用那个。
* 如果任务正在运行或测试 iOS 应用程序，Claude 将使用 [iOS 模拟器窗格 ](<https://code.claude.com/docs/en/desktop-ios-simulator>)，该窗格不使用屏幕控制。
* 如果这些都不适用，Claude 使用计算机。

[每个应用程序访问层](<#应用程序权限>)强化了这一点：浏览器仅限于查看，终端和IDE仅限点击，即使在计算机使用处于活动状态时，也将Claude转向专用工具。屏幕控制保留给其他任何东西都无法到达的东西，例如本机应用程序、硬件控制面板或没有 API 的专有工具。

### 启用计算机使用

默认情况下，计算机的使用处于关闭状态。如果您要求 Claude 在关闭时执行需要它的操作，Claude 会告诉您，如果您在“设置”中启用计算机使用，它就可以执行该任务。



  

#### 更新桌面应用程序

确保您拥有最新版本的 Claude Desktop。在macOS和Windows上，在[claude.com/download](<https://claude.com/download>)下载或更新；在 Linux 上，通过包管理器进行更新（[说明](<https://code.claude.com/docs/en/desktop-linux>)）。然后重新启动应用程序。
  


  

#### 打开开关

在桌面应用程序中，转至 ** 设置 > 此计算机 > 系统 **。在**计算机使用**下，打开**启用计算机使用**。在 Windows 上，切换立即生效并完成设置。在 macOS 上，继续下一步。

如果您没有看到切换开关，请确认您使用的是 macOS 或 Windows，并且拥有 Pro 或 Max 套餐，然后更新并重新启动应用程序。
  


  

#### 授予macOS权限

在macOS上，在切换生效之前授予两个系统权限：

    * **辅助功能**：让Claude单击、键入和滚动
    * **屏幕录制**：让Claude查看屏幕上的内容

设置页面显示每个权限的当前状态。如果其中任何一个被拒绝，请单击徽章以打开相关的系统设置窗格。
  


### 应用程序权限

第一次 Claude 需要使用应用程序时，您的会话中会出现提示。单击**允许此会话**或**拒绝**。批准持续当前会话，或 [ 调度生成的会话 ](<#来自-dispatch-的会话>) 中的 30 分钟。

该提示还显示了该应用程序 Claude 获得的控制级别。这些级别由应用类别固定，无法更改：

|等级|Claude 可以做什么|适用于|
| :- | :- | :- |
|仅供查看|在屏幕截图中查看该应用程序|浏览器、交易平台|
|仅点击|单击并滚动，但不要键入或使用键盘快捷键|终端、IDE|
|完全控制|单击、键入、拖动和使用键盘快捷键|其他一切|

具有广泛覆盖范围的应用程序（例如终端、Finder 或文件资源管理器以及系统设置或设置）会在提示中显示额外警告，以便您知道批准它们会授予什么权限。

**设置 > 此计算机 > 系统 ** 中的 **计算机使用 ** 部分包括以下选项：

* **拒绝的应用程序**：在此处添加应用程序以拒绝它们而不提示。 Claude 仍可能通过允许的应用程序中的操作间接影响被拒绝的应用程序，但它无法直接与被拒绝的应用程序交互。
* **Claude 完成时取消隐藏应用程序**：当计算机使用未在后台运行时，Claude 会在运行时隐藏您的其他窗口，以便仅与批准的应用程序交互。 Claude 完成后，隐藏的窗口将恢复，除非您关闭此设置。

## 管理会话

每个会话都是一次独立的对话，有其自己的上下文和变化。您可以并行运行多个会话、分支聊天、让 Claude 检查其他会话并向其发送消息、将工作发送到云端，或者让 Dispatch 从您的手机为您启动会话。

### 与会话并行工作

单击边栏中的 **+ 新会话 **，或在 macOS 上按 **Cmd+N** 或在 Windows 上按 **Ctrl+N**，并行处理多个任务。按 **Ctrl+Tab** 和 **Ctrl+Shift+Tab** 在侧栏中循环浏览会话。对于 Git 存储库，选择分支名称旁边的 **worktree** 选项，以使用 [Git worktrees](<https://code.claude.com/docs/en/worktrees>) 为会话提供项目自己的独立副本。

要同时查看两个会话，请在 macOS 上按住 **Cmd** 或在 Windows 上按住 **Ctrl**，然后单击侧栏中的会话。该会话将在您已打开的窗格旁边的第二个窗格中打开。当拆分处于活动状态时，单击另一个侧边栏会话将替换具有焦点的窗格。在 macOS 上按 **Cmd+\\** 或在 Windows 上按 **Ctrl+\\** 可关闭焦点窗格并返回到单个会话。

工作树存储在 `<project-root>/.claude/worktrees/` 默认情况下。您可以在“设置”→中将其更改为自定义目录 Claude Code 在“工作树位置”下。您还可以设置一个分支前缀，该前缀会添加到每个工作树分支名称的前面，这对于保持 Claude- 成立有组织的分支机构。要在完成后删除工作树，请将鼠标悬停在侧栏中的会话上，然后单击存档图标。要让会话在拉取请求合并或关闭时自行存档，请打开 **之后自动存档 PR 合并或关闭** 在设置→ Claude Code。自动存档仅适用于已完成运行的本地会话。

要在新工作树中包含 gitignored 文件，例如 `.env`，请在项目根目录中创建一个 [`.worktreeinclude` 文件 ](<https://code.claude.com/docs/en/worktrees#copy-gitignored-files-into-worktrees>)。



**注释：**

会话隔离需要 [Git](<https://git-scm.com/downloads>)。大多数 Mac 默认包含 Git。在终端中运行`git --version`进行检查；如果它打印版本号，则说明 Git 已安装。如果您遇到 Git 错误，请询问 [Cowork 选项卡 ](<https://claude.com/product/cowork>) 中的 Claude 以帮助解决您的设置问题。


使用侧边栏顶部的控件按状态、项目或环境过滤会话，并按项目对会话进行分组。要重命名会话，请单击活动会话顶部工具栏中的会话标题。

要检查上下文使用情况，请参阅[检查使用情况](<#检查使用情况>)。当上下文填满时，Claude 会自动总结对话并继续工作。您还可以输入 `/compact` 以提前触发摘要并释放上下文空间。有关压缩工作原理的详细信息，请参阅 [ 上下文窗口 ](<https://code.claude.com/docs/en/how-claude-code-works#the-context-window>)。

当代码会话完成任务并且您当前未查看该会话时，桌面应用程序会发送 OS 通知。对于属于 [ 项目 ](<https://code.claude.com/docs/en/claude-projects#see-what-needs-you-in-overview>) 的会话，您会收到项目的通知。

### 在不影响会议的情况下提出附带问题

通过旁聊，您可以询问 Claude 一个使用会话上下文的问题，但不会将任何内容添加回主对话中。当您想要理解一段代码、检查假设或探索一个想法而不使会话偏离方向时，请使用它。

在 macOS 上按 **Cmd+;** 或在 Windows 上按 **Ctrl+;** 打开侧聊，或在提示框中输入 `/btw`。侧边聊天可以读取主线程中到目前为止的所有内容。完成后，关闭副聊天并从上次中断的地方继续主会话。

侧聊可在本地、SSH 和 WSL 会话中使用。桌面应用程序不会将侧聊保存到磁盘，因此关闭应用程序后您无法返回到侧聊。

### 观看后台任务

任务窗格显示当前会话中运行的后台工作：子代理、后台 shell 命令和 [ 动态工作流程 ](<https://code.claude.com/docs/en/workflows>)。会话进行后台工作后，使用标题栏的 **⋮** 菜单中的 **后台任务 ** 打开窗格。

单击任意条目可在子代理窗格中查看其输出或停止它。要查看其他会话正在做什么，请使用[侧边栏](<#与会话并行工作>)，或要求Claude[为您检查它们](<#跨会话工作>)。

### 跨会话工作

Claude 可以列出您的其他“代码”选项卡会话，读取每个会话正在执行的操作，并在它们之间发送消息。用简单的语言询问：“哪个会话涉及身份验证重构？”、“API 会话得出了什么结论？”或“告诉支付会话架构已更改”。您还可以要求 Claude 重命名或存档会话。 Claude 归档会话的方式与侧边栏归档图标的方式相同，因此要求它清理 PR 已合并的会话。

通过此表面，Claude 只能看到桌面应用程序本身运行的会话：“代码”选项卡中的本地、[SSH](<#ssh-会话>) 和 [WSL](<https://code.claude.com/docs/en/desktop-wsl>) 会话。 Claude 看不到云会话，或者从终端 CLI 或 VS Code 扩展启动的会话，即使在同一项目的工作树中也是如此，因此在打开九个终端工作树和两个桌面会话的情况下，Claude 在其中一个会话中应答会报告另一个桌面会话。 Claude 从未列出您请求的会话。默认情况下，它会看到 20 最近活动的会话并跳过存档的会话，除非您要求它们。 [跨会话消息传送](<https://code.claude.com/docs/en/cross-session-messaging>)单独让Claude消息[您的其他Claude Code会话](<https://code.claude.com/docs/en/cross-session-messaging#see-which-sessions-claude-can-reach>)，包括终端会话。

当 Claude 通过此表面向另一个会话发送消息时，Claude Code 将其显示为一张卡片，标有发送会话的标题和返回链接，因此您始终可以知道消息来自何处。如果接收会话正在执行任务，Claude Code 会保留该消息，并在当前工作完成后 Claude 读取该消息。接收方Claude可以回复，Claude Code通过该表面将回复传递回来。 Claude 无法传送到存档会话，并会在消息未通过时通知您。

Claude Code 在会话中应用四种安全行为：

* 在存档任何会话之前，Claude 首先会询问您。您可以在每种权限模式下看到批准卡，包括自动和绕过权限。
* 通过这一表面，Claude 无法从无人监视的会话（例如计划任务运行）发送跨会话消息，也无法将消息传递到其中。
* Claude Code 根据接收会话的 [ 入站控制 ](<https://code.claude.com/docs/en/cross-session-messaging#control-inbound-messages>) 检查来自此表面的每条消息，即使接收会话本身没有 [ 跨会话消息传递 ](<https://code.claude.com/docs/en/cross-session-messaging#availability>) 也是如此。如果您在接收会话中将 [`crossSessionInbound`](<https://code.claude.com/docs/en/settings-reference#crosssessioninbound>) 设置为 `refuse`，则 Claude Code 会从此表面删除消息。 Claude Code 向 Claude 桌面应用报告拒绝情况。在 v2.1.234 之前，Claude Code 将此表面的每条消息都丢弃到接收会话，而无需跨会话消息传递。
* Claude Code 引用每条传入消息并将其归因于发送该消息的会话，并且 Claude 在处理消息时仍遵循接收会话自己的权限设置。

Claude 还可以建议新会话。当它发现某些值得修复的内容超出了当前任务的范围时，它会在聊天中将该工作作为任务筹码提供。单击该芯片即可在具有自己的工作树的新会话中开始该工作； Claude 不间断地继续当前会话。

### 在云中运行长时间运行的任务

对于大型重构、测试套件、迁移或其他长时间运行的任务，请在启动会话时选择 **Cloud** 而不是 **Local**。默认情况下，云会话在 Anthropic 管理的基础架构上运行，即使您关闭应用程序或关闭计算机，云会话也会继续运行。请随时回来查看进度或将 Claude 转向不同的方向。您还可以从 [claude.ai/code](<https://claude.ai/code>) 或 [Claude 移动应用程序 ](<https://code.claude.com/docs/en/mobile>) 监控云会话。

云会话还支持多个存储库。选择云环境后，单击所选存储库旁边的**+**按钮，将更多存储库添加到会话中。每个存储库都有自己的分支选择器。这对于跨越多个代码库的任务非常有用，例如更新共享库及其使用者。

有关云会话如何工作的更多信息，请参阅[在云中使用Claude Code](<https://code.claude.com/docs/en/claude-code-on-the-web>)。当一项工作需要多个云会话时，请在侧边栏中选择“**项目**”以创建一个“[项目](<https://code.claude.com/docs/en/claude-projects>)”，其中“Claude”将启动并跟踪一次对话中的会话。

### Continue 在另一个表面

要在其他位置继续会话，请从会话标题旁边的插入符号或侧边栏中的会话行打开会话菜单，然后选择 **在 ** 中打开：

* 选择**云**以[云会话](<https://code.claude.com/docs/en/claude-code-on-the-web>)的形式继续会话，并将您的对话作为摘要进行。在您确认之前，该对话框会说明您的文件是否也移动，以及云准备就绪后是否会存档此会话。您无法以这种方式移动在 [SSH](<#ssh-会话>) 或 [WSL](<https://code.claude.com/docs/en/desktop-wsl>) 中运行的会话。
* 选择已安装的编辑器或文件管理器以打开磁盘上的会话文件夹。

### 控制哪些会话显示在您的其他设备上

[远程控制](<https://code.claude.com/docs/en/remote-control>)连接后，本地会话就会显示在您的其他设备上。连接的会话将显示在 [claude.ai/code](<https://claude.ai/code>) 的会话列表中以及登录到您的 claude.ai 帐户的设备上的 Claude 应用程序中。

当您打开本地会话的远程控制时，或者当本地会话启动时自动连接时，本地会话就会连接：

* **您为该会话打开它**：使用会话的**远程控制**开关，或通过在其提示框中键入`/remote-control`。
* **启动时连接**：当**将新会话连接到远程控制**在**设置> Claude Code**中打开时，新会话会自动连接。如果您从未更改过该设置，Desktop 将遵循您的用户或托管设置中的 [`remoteControlAtStartup`](<https://code.claude.com/docs/en/settings-reference#remotecontrolatstartup>)，然后是您组织的默认设置。

要查看会话是否已连接，请查看工具栏中会话标题之前的笔记本电脑图标。当会话已连接或正在连接时，该图标会突出显示。单击它可打开会话的**远程控制**开关。

要使会话远离其他设备，请在您需要的级别关闭远程控制：

* **一次会话**：关闭其**远程控制**开关。在启动时连接的会话中，键入 `/remote-control` 会使远程控制保持打开状态并显示 `Remote Control is already on. This session connected automatically when it started.` 单击该线路上的 ** 关闭 ** 以断开连接。
* **此计算机上的新桌面会话**：在**设置 > Claude Code**中关闭**将新会话连接到远程控制**。如果它已经显示，请将其打开然后关闭，以便 Desktop 保存您的选择。保存后，它优先于 `remoteControlAtStartup` 和默认值。
* **此计算机上的任何会话，包括CLI**：将[`disableRemoteControl`](<https://code.claude.com/docs/en/settings-reference#disableremotecontrol>)设置为`~/.claude/settings.json`中的`true`以停止会话连接。保存文件时已连接的会话将保持连接状态，直到您关闭其远程控制。

要隐藏其他设备上已显示的会话，请将其存档在桌面中。桌面也会存档会话的远程控制副本，因此它会在这些设备上保留默认会话列表。要在那里查看或删除它，请参阅[存档会话](<https://code.claude.com/docs/en/claude-code-on-the-web#archive-sessions>)。

### 来自 Dispatch 的会话

[Dispatch](<https://support.claude.com/en/articles/13947068>) 是与 Claude 的持续对话，该对话位于 [Cowork](<https://claude.com/product/cowork>) 选项卡中。你发消息调度一个任务，它决定如何处理它。

一项任务可以通过两种方式结束为代码会话：您直接请求一种，例如“打开 Claude Code 会话并修复登录错误”，或者 Dispatch 确定该任务是开发工作并自行生成一个。通常路由到代码的任务包括修复错误、更新依赖项、运行测试或打开拉取请求。研究、文档编辑和电子表格工作都留在 Cowork 中。

无论哪种方式，代码会话都会显示在“代码”选项卡的侧栏中，并带有 **Dispatch** 徽章。当完成或需要您的批准时，您会在手机上收到推送通知。

如果您启用了[计算机使用](<#让claude使用你的电脑>)，则调度生成的代码会话也可以使用它。这些会话中的应用程序批准会在 30 分钟后过期并重新提示，而不是像常规代码会话那样持续整个会话。

有关设置、配对和调度设置，请参阅 [调度帮助文章 ](<https://support.claude.com/en/articles/13947068>)。 Dispatch 需要 Pro 或 Max 计划，并且不适用于 Team 或 Enterprise 计划。

调度是当您离开终端时使用 Claude 的几种方法之一。有关与其他选项的比较，请参阅[平台和集成](<https://code.claude.com/docs/en/platforms#work-when-you-are-away-from-your-terminal>)。

## 延长Claude Code

连接外部服务、添加可重用工作流程、自定义 Claude 的行为以及配置预览服务器。要在一个位置管理连接器、技能和插件，请单击侧边栏中的**自定义**。桌面应用程序中的 [Cowork](<https://claude.com/product/cowork>) 选项卡从此自定义配置中获取其技能、插件和连接器，该配置通过您的 claude.ai 帐户同步，而不是从 CLI 的 `~/.claude` 目录同步。

Claude Code 还会在您使用同一帐户登录的终端会话中加载为您的 claude.ai 帐户启用的技能和插件。参见 [从 claude.ai 同步的技能](<https://code.claude.com/docs/en/skills#how-synced-skills-behave>) 和 [从 claude.ai 同步的插件](<https://code.claude.com/docs/en/plugins/loading#synced-plugins>).

### 连接外部工具

对于本地和 [SSH](<#ssh-会话>) 会话，单击提示框旁边的 **+** 按钮，然后选择 ** 连接器 ** 以添加集成，例如 Google 日历、Slack、GitHub、线性、 Notion 等。您可以在会话之前或会话期间添加连接器。 **+** 按钮在云或 WSL 会话中不可用，但 [ 例程 ](<https://code.claude.com/docs/en/routines>) 在例程创建时配置连接器。

要管理或断开连接器，请转至桌面应用程序中的设置→连接器，或从提示框中的连接器菜单中选择**管理连接器**。

连接后，Claude 可以读取您的日历、发送消息、创建问题以及直接与您的工具交互。您可以询问 Claude 您的会话中配置了哪些连接器。

连接器是具有图形设置流程的[MCP服务器](<https://code.claude.com/docs/en/mcp>)。使用它们与支持的服务快速集成。对于连接器中未列出的集成，请通过 [ 设置文件 ](<https://code.claude.com/docs/en/mcp#installing-mcp-servers>) 手动添加 MCP 服务器。您还可以[创建自定义连接器](<https://support.claude.com/en/articles/11175166-getting-started-with-custom-connectors-using-remote-mcp>)。

### 使用技巧

[技能](<https://code.claude.com/docs/en/skills>)扩展了Claude的能力。 Claude 会在相关时自动加载它们，或者您可以直接调用它们：在提示框中键入 `/` 或单击 **+** 按钮并选择 ** 斜线命令 ** 来浏览可用的内容。这包括 [ 内置命令 ](<https://code.claude.com/docs/en/commands>)、您的 [ 自定义技能 ](<https://code.claude.com/docs/en/skills#create-your-first-skill>)、代码库中的项目技能以及任何 [ 安装的插件 ](<https://code.claude.com/docs/en/plugins/install>) 中的技能。选择一个，它会在输入字段中突出显示。在其后输入您的任务并照常发送。

您可以在 Claude 工作时发送命令，与任何其他消息相同，一旦回合结束，会话将返回空闲状态。在 v2.1.206 之前，中途发送的命令可能会使会话显示为正在运行，并且您随后发送的消息不会被传递。

本地会话从 `~/.claude/skills/` 加载您的个人技能。 [SSH](<#ssh-会话>) 会话从远程主机的主目录（而不是您的计算机）读取 `~/.claude/skills/`。

本地和云会话还会加载为您的 claude.ai 帐户启用的技能，但当您的组织设置 [`disableSideloadFlags`](<https://code.claude.com/docs/en/settings-reference#disablesideloadflags>) 时，本地会话除外。云会话加载它们而不是 `~/.claude/skills/`，如 [Cowork 和云会话技能 ](<https://code.claude.com/docs/en/skills#skills-in-cowork-and-cloud-sessions>) 中所述。

### 安装插件

[插件](<https://code.claude.com/docs/en/plugins/overview>)是可重用的软件包，可将技能、agents、挂钩、MCP服务器和LSP配置添加到Claude Code。您可以从桌面应用程序安装插件，而无需使用终端。

对于本地和[SSH](<#ssh-会话>)会话，点击提示框旁边的**+**按钮，选择**插件**即可查看您安装的插件及其技能。要添加插件，请从子菜单中选择**添加插件**以打开插件浏览器，其中显示您配置的[市场](<https://code.claude.com/docs/en/plugins/overview>)中的可用插件，包括官方Anthropic市场。选择**管理插件**以启用、禁用或卸载插件。

您可以将插件范围限定为您的用户帐户、特定项目或仅限本地。如果您的组织集中管理插件，则这些插件在桌面会话中的使用方式与在 CLI 中的方式相同，但桌面应用程序 [ 在 `disableSideloadFlags`](<https://code.claude.com/docs/en/settings-reference#disablesideloadflags>) 下保留的插件除外。

插件浏览器在云会话中不可用，并且从桌面应用程序安装的插件不可用于云会话。云会话也不会安装存储库的 `.claude/settings.json` 声明的插件，如 [从您的设置中继承的内容](<https://code.claude.com/docs/en/cloud-environments#what-carries-over-from-your-setup>) 解释的那样。插件在 WSL 会话中不可用。有关完整的插件参考，包括创建您自己的插件，请参阅[插件](<https://code.claude.com/docs/en/plugins/overview>)。

### 配置预览服务器

Claude 会自动检测您的开发服务器设置，并将配置存储在您在启动会话时选择的文件夹根目录下的 `.claude/launch.json` 中。预览使用此文件夹作为其工作目录，因此如果您选择父文件夹，则不会自动检测具有自己的开发服务器的子文件夹。要使用子文件夹的服务器，请直接在该文件夹中启动会话或手动添加配置。

要自定义服务器的启动方式，例如使用 `yarn dev` 而不是 `npm run dev` 或更改端口，请编辑 `.claude/launch.json`。该文件支持带有注释的JSON。

```json theme={null}
{
  "version": "0.0.1",
  "configurations": [
    {
      "name": "my-app",
      "runtimeExecutable": "npm",
      "runtimeArgs": ["run", "dev"],
      "port": 3000
    }
  ]
}
```

您可以定义多个配置来运行同一项目中的不同服务器，例如前端和 API。请参阅下面的 [ 示例 ](<#示例>)。

#### 自动验证更改

当启用`autoVerify`时，Claude在编辑文件后自动验证代码更改。在完成响应之前，它会截取屏幕截图、检查错误并确认更改工作。

默认情况下自动验证处于开启状态。通过将 `"autoVerify": false` 添加到 `.claude/launch.json` 来按项目禁用它，或者在浏览器窗格的 **⋮** 菜单中关闭 **自动验证更改 **。

```json theme={null}
{
  "version": "0.0.1",
  "autoVerify": false,
  "configurations": [
    {
      "name": "my-app",
      "runtimeExecutable": "npm",
      "runtimeArgs": ["run", "dev"],
      "port": 3000
    }
  ]
}
```

禁用后，预览工具仍然可用，您可以随时要求Claude进行验证。自动验证使其在每次编辑后自动进行。

#### 配置字段

`configurations` 数组中的每个条目接受以下字段：

|领域|类型|描述|
| - | - | - |
| `name` |字符串|该服务器的唯一标识符|
| `runtimeExecutable` |字符串|要运行的命令，例如 `npm`、`yarn` 或 `node`|
| `runtimeArgs` |字符串\[]|传递给`runtimeExecutable`的参数，例如`["run", "dev"]`|
| `port` |数量|您的服务器侦听的端口。默认为 3000|
| `cwd` |字符串|相对于项目根目录的工作目录。默认为项目根目录。使用 `${workspaceFolder}` 显式引用项目根目录|
| `env` |对象|其他环境变量作为键值对，例如 `{ "NODE_ENV": "development" }`。不要在此处放置机密，因为该文件已提交到您的存储库。要将机密传递到您的开发服务器，请在 [ 本地环境编辑器 ](<#本地会议>) 中设置它们。|
| `autoPort` |布尔值|如何处理端口冲突。请参阅[端口冲突](<#端口冲突>)|
| `program` |字符串|使用 `node` 运行的脚本。请参阅 [ 何时使用 `program` 与 `runtimeExecutable`](<#何时使用-program-与-runtimeexecutable>)|
| `args` |字符串\[]|参数传递给 `program`。仅当设置`program`时使用|
| `url` |字符串|预览打开的地址而不是 `http://localhost:<port>`。请参阅[在特定URL](<#在特定的-url-打开预览>)处打开预览|

<a id="when-to-use-program-vs-runtimeexecutable" />

##### 何时使用 `program` 与 `runtimeExecutable`

将 `runtimeExecutable` 与 `runtimeArgs` 结合使用，通过包管理器启动开发服务器。例如，`"runtimeExecutable": "npm"` 与 `"runtimeArgs": ["run", "dev"]` 运行 `npm run dev`。

当您想要直接使用 `node` 运行独立脚本时，请使用 `program`。例如，`"program": "server.js"` 运行 `node server.js`。使用 `args` 传递附加标志。

<a id="open-the-preview-at-a-specific-url" />

##### 在特定的 URL 打开预览

默认情况下，预览打开 `http://localhost:<port>`。当您的服务器需要不同的地址时，请设置`url`。常见情况是需要本地 HTTPS 的服务器、使用 `*.localhost` 子域的应用程序以及通过重定向登录的应用程序。

```json theme={null}
{
  "version": "0.0.1",
  "configurations": [
    {
      "name": "my-app",
      "runtimeExecutable": "npm",
      "runtimeArgs": ["run", "dev"],
      "port": 8443,
      "url": "https://localhost:8443"
    }
  ]
}
```

本地主机地址直接打开，与默认端口地址完全相同。这包括 `localhost`、任何 `*.localhost` 子域、`127.0.0.1` 和 `::1`。为了安全起见，本地主机 `url` 必须是服务器的单独来源，没有路径或查询。它的端口必须与条目的端口匹配。要显示特定页面，请要求 Claude 在预览打开后导航到该页面。具有路径、查询或不匹配端口的本地主机 `url` 会报告为配置错误，该错误会命名 url 并显示修复程序。

对于任何其他地址，Desktop 会在预览第一次打开它时请求您的许可，就像您在预览中浏览到新网站时一样。外部地址可以包括路径。选择 ** 始终允许 ** 将来跳过该站点的提示。在预览中限制外部站点的组织策略仍然适用。

要预览您自己运行的服务器，请设置 `url`，无需使用命令。 Claude 将预览附加到正在运行的服务器，而不是启动服务器：

```json theme={null}
{
  "version": "0.0.1",
  "configurations": [
    {
      "name": "my-app",
      "url": "https://app.localhost:3000"
    }
  ]
}
```

`url`必须是`http`或`https`，并且不能包含用户名或密码。

#### 端口冲突

`autoPort` 字段控制当您的首选端口已在使用时会发生什么：

* **`true`**：Claude自动查找并使用空闲端口。适用于大多数开发服务器。
* **`false`**：Claude 失败并出现错误。当您的服务器必须使用特定端口（例如 OAuth 回调或 CORS 白名单）时，请使用此端口。
* **未设置（默认）**：Claude询问服务器是否需要该确切端口，然后保存您的答案。

当 Claude 选择不同的端口时，它会通过 `PORT` 环境变量将分配的端口传递到您的服务器。

#### 示例

这些配置显示了不同项目类型的常见设置：



  

#### `Next.js`

此配置在端口 3000 上使用 Yarn 运行 Next.js 应用程序：

    ```json theme={null}
    {
      "version": "0.0.1",
      "configurations": [
        {
          "name": "web",
          "runtimeExecutable": "yarn",
          "runtimeArgs": ["dev"],
          "port": 3000
        }
      ]
    }
    ```
  


  

#### 多台服务器

对于具有前端和 API 服务器的 monorepo，定义多个配置。前端使用 `autoPort: true`，因此如果采用 3000，它会选择一个空闲端口，而 API 服务器完全需要端口 8080：

    ```json theme={null}
    {
      "version": "0.0.1",
      "configurations": [
        {
          "name": "frontend",
          "runtimeExecutable": "npm",
          "runtimeArgs": ["run", "dev"],
          "cwd": "apps/web",
          "port": 3000,
          "autoPort": true
        },
        {
          "name": "api",
          "runtimeExecutable": "npm",
          "runtimeArgs": ["run", "start"],
          "cwd": "server",
          "port": 8080,
          "env": { "NODE_ENV": "development" },
          "autoPort": false
        }
      ]
    }
    ```
  


  

#### Node.js 脚本

要直接运行 Node.js 脚本而不是使用包管理器命令，请使用 `program` 字段：

    ```json theme={null}
    {
      "version": "0.0.1",
      "configurations": [
        {
          "name": "server",
          "program": "server.js",
          "args": ["--verbose"],
          "port": 4000
        }
      ]
    }
    ```
  


## 环境配置

您在[启动会话](<#开始会话>)时选择的环境决定了Claude的执行位置以及连接方式：

* **本地**：在您的计算机上运行，可以直接访问您的文件
* **云**：默认在Anthropic管理的基础设施上运行。即使您关闭应用程序，会话也会继续。
* **SSH**：在您通过 SSH 连接的远程计算机上运行，例如您自己的服务器、云虚拟机或开发容器
* **WSL** (Windows)：使用其 Linux 工具链和本机路径在计算机上的 [WSL 2 发行版 ](<https://code.claude.com/docs/en/desktop-wsl>) 内运行

### 本地会议

桌面应用程序并不总是继承您的完整 shell 环境。在 macOS 上，当您从 Dock 或 Finder 启动应用程序时，它会读取您的 shell 配置文件（例如 `~/.zshrc` 或 `~/.bashrc`），以提取 `PATH` 和一组固定的 Claude Code 变量，但不会选取您导出的其他变量。在 Windows 上，应用程序继承用户和系统环境变量，但不读取 PowerShell 配置文件。

要在任何平台上为本地会话和开发服务器设置环境变量，请打开提示框中的环境下拉列表，将鼠标悬停在 **Local** 上，然后单击齿轮图标打开本地环境编辑器。您在此处保存的变量会加密存储在您的计算机上，并应用于您启动的每个本地会话和预览服务器。您还可以将变量添加到 `~/.claude/settings.json` 文件中的 `env` 键，尽管这些变量仅到达 Claude 会话，而不到达开发服务器。请参阅[环境变量](<https://code.claude.com/docs/en/env-vars>)以获取支持的变量的完整列表。

[扩展思维](<https://code.claude.com/docs/en/model-config#extended-thinking>)默认启用，这可以提高复杂推理任务的性能，但会使用额外的标记。在AnthropicAPI上，在本地环境编辑器中将`MAX_THINKING_TOKENS`设置为`0`以关闭思考；这对 Opus 5.5、Sonnet 5.5、Haiku 5.5 或 Fable 模型没有影响，这些模型总是使用扩展思维。当关闭 Anthropic API 上的思考时，Claude Code 将努力 `high` 而不是更高级别发送到它知道 [ 不接受该组合 ](<https://code.claude.com/docs/en/errors#effort-isnt-available-with-thinking-turned-off>) 的模型，例如 Opus 5。

在具有 [ 自适应推理 ](<https://code.claude.com/docs/en/model-config#adjust-effort-level>) 的模型上，Claude Code 会忽略正 `MAX_THINKING_TOKENS` 值中的数字本身，因为自适应推理反而控制思维深度。在 Opus 4.6 和 Sonnet 4.6 上，将 `CLAUDE_CODE_DISABLE_ADAPTIVE_THINKING` 设置为 `1` 以使用固定的思维预算； Fable 模型、Sonnet 5 及更高版本、Haiku 5.5 和 Opus 4.7 及更高版本始终使用自适应推理，并且没有固定预算模式。

#### 托管设备上的本地会话

您的管理员可以使用 [`disableDesktopLocalSessions` 托管设置 ](<#托管设置>) 关闭本地会话。当他们这样做时，**本地**保留在环境下拉列表中，但呈灰色且无法选择，并有一个工具提示表明您的组织已将其关闭，并且在Windows上，[WSL](<https://code.claude.com/docs/en/desktop-wsl>)条目在受管设备上的可用性由[单独管理](<https://code.claude.com/docs/en/admin-setup#wsl-sessions-in-claude-code-desktop>)，呈灰色显示同样的方式。新会话默认为第一个 SSH 连接（如果已配置），并且如果您尝试继续现有会话，桌面会显示一条消息，表明本地会话在此设备上不可用。请选择 [SSH](<#ssh-会话>) 或 [ 云 ](<#云会议>) 环境，或联系您的 IT 团队。

### 云会议

即使您关闭应用程序，云会话也会在后台继续。使用量计入您的 [ 订阅计划限制 ](<https://code.claude.com/docs/en/costs>)，无需单独的计算费用。

您可以创建具有不同网络访问级别和环境变量的自定义云环境。要管理它们，请打开提示框中的环境下拉列表，然后选择**云**：

* **添加环境**：选择**添加云环境**
* **编辑或存档您自己的环境之一**：将鼠标悬停在其上并单击齿轮图标

有关配置网络访问和环境变量的详细信息，请参阅[配置云环境](<https://code.claude.com/docs/en/cloud-environments>)。

### SSH 会话

SSH 会话允许您在远程计算机上运行 Claude Code，同时使用桌面应用程序作为界面。这对于使用位于云虚拟机、开发容器或具有特定硬件或依赖项的服务器上的代码库非常有用。

要添加 SSH 连接，请在开始会话之前打开提示框中的环境下拉列表，然后选择 **SSH > 添加 SSH 连接...** 并填写连接详细信息：

* **名称**：此连接的友好标签
* **SSH主机**：`user@hostname`或`~/.ssh/config`中定义的主机
* **SSH 端口 **：如果留空，则默认为 22，或者使用 SSH 配置中的端口
* **SSH密钥（可选）**：您的私钥路径，例如`~/.ssh/id_ed25519`。留空以使用您的 SSH 配置或 SSH agent。

添加后，连接将显示在环境下拉列表中的 **SSH** 下。选择它以在该计算机上启动会话。 Claude 在远程计算机上运行，​​可以访问其文件和工具。

远程计算机必须运行 Linux 或 macOS。首次连接时，Desktop 会自动在远程计算机上安装 Claude Code。连接后，SSH 会话支持权限模式、连接器、插件和 MCP 服务器。

#### 从链接打开 SSH 会话

`claude://code/new` 链接可打开 Desktop 的新会话页面，它可以命名 SSH 连接。将此类链接放入 Runbook、仪表板或 wiki 页面中，以打开为正确的计算机和文件夹设置的桌面。对于剥离此类链接的平台，请参阅 [该链接呈现为纯文本而不是可点击的](<https://code.claude.com/docs/en/deep-links#the-link-renders-as-plain-text-instead-of-being-clickable>)。

SSH 链接需要 Claude 桌面版 v2.110.0 或更高版本。

以下链接将 `build.example.com` 上的用户 `dev`、端口 2222 和文件夹 `/srv/payments` 命名，并填写提示：

```text theme={null}
claude://code/new?ssh_host=dev%40build.example.com&ssh_port=2222&ssh_folder=/srv/payments&q=Investigate%20the%20failed%20deploy
```

SSH 链接采用以下参数，并且仅需要 `ssh_host`：

|参数|价值|
| :- | :- |
| `ssh_host` |`host` 或 `user@host`，如 **SSH 主机 ** 字段中所示。不能以`-`开头，主机部分只能由字母、数字、`.`、`_`、`:`、`-`组成。|
| `ssh_port` |端口号从 1 到 65535|
| `ssh_folder` |远程计算机上的文件夹。以 `/` 或 `~/` 开头，或使用 `~`|
| `q` |URL-提示框的编码文本|

`~/.ssh/config` 中的别名仅适用于拥有该条目的人`ssh_host`。要匹配人们已有的连接，请为该链接提供与该连接相同的用户、主机和端口。

当您打开链接时，Desktop 会在选择连接之前要求您确认：

* **您已有的连接**：如果主机、用户和端口与您的连接之一匹配，Desktop 会询问是否使用它，并显示连接的名称和主机，以及文件夹（如果链接指定一个连接）。
* **新连接**：否则桌面将打开用于添加SSH连接的对话框。添加连接时，Desktop 会在保存任何内容之前询问是否连接，并显示链接中的主机以及端口和文件夹（如果链接命名）。

在您确认之前，Desktop 不会保存链接中的主机、端口或文件夹，也不会选择或打开与它们的连接。如果已选择 SSH 连接，则新会话页面仍可以自行连接到它，就像没有链接时一样，即使链接命名相同的主机也是如此。链接不能携带密钥文件、密码或命令。

任何人都可以编写链接，因此请检查其中填写的内容：

* **确认之前**：检查主机和文件夹。
* **发送之前**：检查提示和所选环境。

当链接打开时，Desktop 会填写提示，代替您尚未发送的任何文本，并且永远不会为您发送。它将提示视为纯文本，因此前导 `/` 或 `!` 和 `@` 文件提及不会充当命令或提及。如果取消，提示将保留在框中，并且您选择的环境不会更改。

链接不会绕过 [`sshHostAllowlist`](<#限制用户可以连接到哪些-ssh-主机>)。 Desktop 在连接时检查白名单。

如果链接打开 Desktop 时没有出现有关连接的对话框，请查找以下原因之一：

* **您已退出**：登录，然后再次打开链接。
* **另一个对话框已打开**：关闭它，然后再次打开链接。
* **链接无效**：桌面显示一条消息，说明要修复的内容，但不填写提示。
* **桌面版本早于v2.110.0**：早期版本会忽略SSH参数，打开新会话页面时仅出现提示。
* **SSH 会话已关闭**：如果管理员将允许列表设置为空数组，则 Desktop 会拒绝 SSH 链接。

#### 为您的团队预先配置 SSH 连接

管理员可以通过在 [ 托管设置 ](<https://code.claude.com/docs/en/managed-settings>) 中设置 `sshConfigs` 将 SSH 连接分发给团队成员。以这种方式定义的连接会自动显示在每个用户的环境下拉列表中，并显示为托管连接，因此用户可以选择它们，但无法在应用程序中编辑或删除它们。

以下示例预配置单个连接：

```json theme={null}
{
  "sshConfigs": [
    {
      "id": "shared-dev-vm",
      "name": "Shared Dev VM",
      "sshHost": "user@dev.example.com",
      "sshPort": 22,
      "sshIdentityFile": "~/.ssh/id_ed25519"
    }
  ]
}
```

每个条目需要 `id`、`name` 和 `sshHost`。 `sshPort` 和 `sshIdentityFile` 字段是可选的。用户还可以将`sshConfigs`添加到自己的`~/.claude/settings.json`中。

#### 限制用户可以连接到哪些 SSH 主机

管理员可以通过在 [ 托管设置 ](<https://code.claude.com/docs/en/managed-settings>) 中设置 `sshHostAllowlist` 将 Desktop 的 SSH 会话限制为一组已批准的主机。设置后，用户只能连接到其解析主机名与其​​中一种模式匹配的主机。将其设置为空数组以禁用 SSH 会话。 [`sshHostAllowlist` 参考条目](<https://code.claude.com/docs/en/settings-reference#sshhostallowlist>) 说明了空数组如何与其他托管源中的列表组合。

以下示例允许连接到 `devboxes.example.com` 下的任何主机以及单个指定的堡垒主机：

```json theme={null}
{
  "sshHostAllowlist": ["*.devboxes.example.com", "bastion.example.com"]
}
```



**注意：**

  如果您的组织提供 [服务器管理的设置](<https://code.claude.com/docs/en/server-managed-settings>), 设置 `sshHostAllowlist` 那里。默认情况下，Desktop 仅从 [提供策略密钥的排名最高的托管源](<https://code.claude.com/docs/en/managed-settings#how-claude-code-combines-managed-sources>)。如果该源未设置密钥，则 Desktop 会忽略排名较低的列表 MDM 策略或托管设置文件并将密钥视为 [未设置](<https://code.claude.com/docs/en/settings-reference#sshhostallowlist>)。桌面没有显示任何警告。

还要在每个用户的计算机上排名最高的 MDM 策略或托管设置文件中保留相同的列表。桌面在启动时获取服务器管理的设置，并且不保留缓存副本，因此在获取成功之前，计算机的列表才是适用的。


模式不区分大小写。 `*` 匹配任何主机，`*.example.com` 匹配 `example.com` 和任何子域。其他任何内容都是完全匹配的。该检查在通过 `ssh -G` 解析 `~/.ssh/config` 后针对主机名运行，因此只要解析的 `HostName` 匹配，就允许 `Host` 别名和 `ProxyCommand`/`ProxyJump` 条目。

`sshHostAllowlist` 仅从托管设置中读取；用户或项目设置中的值将被忽略。只有 Claude 桌面应用程序支持此设置； Claude Code CLI 和 IDE 扩展不读取它，并且它不限制通过 Bash 工具运行的 `ssh` 命令。它控制桌面应用程序连接到的主机，而不是网络出口，因此如果您需要硬边界，请将其与组织的网络或零信任控制配对。

## 企业配置

团队或企业计划的组织可以通过管理控制台控件、托管设置文件和设备管理策略来管理桌面应用程序行为。

### 管理控制台控件

这些设置是通过 [ 管理设置控制台 ](<https://claude.ai/admin-settings/claude-code>) 配置的：

* **桌面**：控制组织中的用户是否可以访问桌面应用中的Claude Code
* **云会话**：为您的组织启用或禁用[云会话](<https://code.claude.com/docs/en/claude-code-on-the-web>)
* **远程控制**：为您的组织启用或禁用[远程控制](<https://code.claude.com/docs/en/remote-control>)

在启用了 HIPAA 的企业组织中，**桌面** 开关默认处于关闭状态，[所有者](<https://code.claude.com/docs/en/server-managed-settings#access-control>) 可以将其打开。应用 [HIPAA 配置 ](<https://code.claude.com/docs/en/hipaa-setup>) 会将其关闭，即使它已打开，因此所有者必须随后将其打开。默认情况下，**云会话**和**远程控制**也处于关闭状态，一旦组织应用了HIPAA配置，所有者就无法打开它们。



**注释：**

  的 OpenTelemetry Cowork 表格如下 **监控** 在管理控制台的 [数据和隐私设置](<https://claude.ai/admin-settings/data-privacy-controls>) 仅适用于 Cowork 会话。在此计算机上的 Cowork 会话中，桌面应用程序将该收集器传递给 Claude Code 作为 `OTEL_*` 环境变量，所以即使形式 Claude Code 在那次会议上 [从不获取管理控制台设置](<#托管设置>).

  要从“代码”选项卡会话导出遥测数据，请设置 `CLAUDE_CODE_ENABLE_TELEMETRY` 和 `OTEL_*` 中的变量 `env` 你的块 Claude Code 托管设置，如图所示 [用于监控的管理员配置](<https://code.claude.com/docs/en/monitoring-usage#administrator-configuration>)。本地、云和 SSH 每个会话读取 [来自不同来源的托管设置](<#托管设置>)。对于云会话可以到达的主机，请参阅 [网络访问](<https://code.claude.com/docs/en/cloud-environments#network-access>)。对于 `service.name` 代码选项卡会话报告，请参阅 [服务信息](<https://code.claude.com/docs/en/monitoring-usage#service-information>).


### 托管设置

托管设置覆盖项目和用户设置并应用于 Desktop 中的 Claude Code 会话。您可以在组织的 [ 托管设置 ](<https://code.claude.com/docs/en/managed-settings>) 文件中设置这些密钥，或通过管理控制台远程推送它们。

|钥匙|描述|
| - | - |
| `permissions.disableBypassPermissionsMode` |设置为 `"disable"` 以防止用户启用绕过权限模式。|
| `disableAutoMode` |设置为 `"disable"` 可从模式选择器中删除 [自动](<https://code.claude.com/docs/en/permission-modes#eliminate-prompts-with-auto-mode>) 模式。也根据 `permissions` 接受。|
| `autoMode` |自定义自动模式分类器在整个组织中信任和阻止的内容。请参见[配置自动模式](<https://code.claude.com/docs/en/auto-mode-config>)。|
| `browserExternalPageTools` |设置为 `"disabled"` 以防止 Claude 使用工具读取或操作 [ 浏览器窗格 ](<#浏览外部网站>) 中的外部页面。用户仍然可以自己导航到外部站点，并且本地开发服务器预览不受影响。|
| `disableMobileSimulatorTools` |设置为 `true` 以阻止 Claude 在 [iOS 模拟器窗格 ](<https://code.claude.com/docs/en/desktop-ios-simulator#turn-off-simulator-access>) 中控制和捕获设备的工具。该窗格仍可用于用户自己的点击；仅删除Claude的访问权限。该值必须是JSON布尔值`true`；字符串 `"true"` 被忽略。|
| `disableBrowserExternalNavigation` |设置为 `true` 以完全关闭 [ 浏览器窗格 ](<#浏览外部网站>) 中的外部浏览。用户和 Claude 都无法导航到外部站点，并且本地主机开发服务器预览不受影响。该值必须是JSON布尔值`true`；字符串 `"true"` 被忽略。|
| `sshConfigs` |预配置环境下拉列表中显示的 [SSH 连接 ](<#为您的团队预先配置-ssh-连接>)。用户无法编辑或删除托管连接。|
| `sshHostAllowlist` |将 [SSH 会话 ](<#限制用户可以连接到哪些-ssh-主机>) 限制为解析主机名与这些模式之一匹配的主机。仅从托管设置中读取。|
| `disableDesktopLocalSessions` | 设置为 `true` 关闭 [在设备上运行的代码会话](<#托管设备上的本地会话>)，离开 SSH 与其他主机的会话和可用的云会话。该值必须是 JSON 布尔值 `true`。仅从托管设置中读取。需要 Claude 桌面 v1.37937.0 或稍后。 |
| `disableSshSavedPasswords` |设置为 `true` 以阻止 Desktop 记住 SSH 密码以及使用或显示之前保存的密码。打开它不会删除它们。仅从托管设置中读取。需要 Claude 桌面版 v1.49585.0 或更高版本。|
| `managedMcpServers` |将 MCP 服务器配置推送给所有用户。仅适用于第三方 (3P) 桌面部署。在每个条目中，设置 `"http"`、`"sse"` 或 `"stdio"` 的传输、连接详细信息以及可选的 `toolPolicy` 映射以限制用户可以调用该服务器的哪些工具。通过托管设置文件 MDM 或 Claude 应用网关策略的 [`desktop` 块 ](<https://code.claude.com/docs/en/claude-apps-gateway-config#claude-desktop-overlay>) 传递它，因为 3P 部署不接收管理控制台设置。要通过网关传送它，网关服务器上需要 Claude Code v2.1.232 或更高版本。这是桌面应用程序自己的密钥； Claude Code 读取其自己的 [ 同名托管设置 ](<https://code.claude.com/docs/en/managed-mcp#provide-servers-through-managed-settings>)，但条目形状不同。|

哪些托管设置到达桌面会话取决于该会话的运行位置。诸如 [`availableModels`](<https://code.claude.com/docs/en/model-config#restrict-model-selection>) 之类的模型限制在桌面 Claude Code 会话中以与终端 CLI 中相同的方式强制执行；参见[表面覆盖率](<https://code.claude.com/docs/en/model-config#surface-coverage>)。

* **此计算机上的本地会话**：应用部署到磁盘的托管设置文件。当会话使用 [ 合格登录名 ](<https://code.claude.com/docs/en/server-managed-settings#platform-availability>) 进行身份验证时，通过管理控制台远程推送的托管设置也会到达 Anthropic 的 API 上的这些会话，遵循与终端相同的 [ 设置优先级 ](<https://code.claude.com/docs/en/settings#settings-precedence>) CLI。
* **[云会话](<#云会议>)**：接收[服务器管理的设置](<https://code.claude.com/docs/en/server-managed-settings>)；设备部署的文件无法到达它们，因为它们在 Anthropic 管理的 VM 上运行。路由到 [ 自托管环境 ](<https://code.claude.com/docs/en/self-hosted-environments>) 的会话还会读取运行程序映像中的托管设置文件。 [如何Claude Code组合托管源](<https://code.claude.com/docs/en/managed-settings#how-claude-code-combines-managed-sources>)说明该文件何时适用。
* **[SSH 会话](<#ssh-会话>)**：会话从远程主机读取托管设置文件。 Desktop 本身在本地计算机上读取 `sshConfigs`、`sshHostAllowlist`、`disableSshSavedPasswords` 和 `disableDesktopLocalSessions`。如果您交付多个托管源，默认情况下它会从 [ 一个 ](<https://code.claude.com/docs/en/managed-settings#how-claude-code-combines-managed-sources>) 读取它们。
* **[Cowork](<https://claude.com/docs/cowork/overview>) 会话**：在此计算机上的 Cowork 会话中，Claude Code 永远不会获取管理控制台设置，即使用户使用团队或企业帐户登录，并读取部署到计算机的策略，除非您的 Claude 桌面配置集`requireCoworkFullVmSandbox`。远程 Cowork 会话两者都不会收到。请参阅 [ 何时何地应用策略 ](<https://code.claude.com/docs/en/managed-settings#where-and-when-a-policy-applies>) 了解哪些设备文件到达 Cowork，以及 [MCP 权限规则 ](<https://code.claude.com/docs/en/permissions#mcp>) 了解 `Bash` 和 `WebFetch` 规则如何应用于 Cowork 的工具。

在本地和 SSH 会话中，桌面应用程序将每个用户连接的 claude.ai 连接器直接传送到 Claude Code。无论您使用哪个设置源或文件位置，MCP 设置或 `managed-mcp.json` 都不会到达这些连接器。要在这些会话中阻止连接器工具，请使用您组织的 [ 连接器工具控件 ](<https://code.claude.com/docs/en/mcp#organization-controls-on-connector-tools>)。 [连接器如何到达 Claude Code](<https://code.claude.com/docs/en/mcp#how-connectors-reach-claude-code>) 显示哪些设置控制每种会话中的连接器。

`permissions.disableBypassPermissionsMode` 和 `disableAutoMode` 也适用于用户和项目设置，但将它们放在托管设置中可防止用户覆盖它们。

对于只有托管源可以设置的权限、插件和交付密钥，请参阅[只有托管设置可以设置的密钥](<https://code.claude.com/docs/en/managed-settings#managed-only-settings>)。

### 设备管理策略

IT 团队可以通过 macOS 上的 MDM、Windows 上的组策略或 Linux 上的策略文件来管理桌面应用程序。可用策略包括启用或禁用 [​​Claude Code 功能、控制 macOS 和 Windows 上的自动更新以及设置自定义部署 URL。

* **macOS**：使用 Jamf 或 Kandji 等工具通过 `com.anthropic.claudefordesktop` 首选项域进行配置
* **Windows**：通过`SOFTWARE\Policies\Claude`处的注册表进行配置
* **Linux**：通过 `/etc/claude-desktop/managed-settings.json` 处的 root 拥有的文件进行配置，该文件将策略密钥保存为 JSON 对象。如果除 root 之外的任何人都可以写入该文件或其文件夹，则 Desktop 会拒绝该文件。它与 Claude Code 的 [ 托管设置文件 ](<https://code.claude.com/docs/en/managed-settings>) 不同。

### 网络接入要求

Desktop 从 Anthropic CDN 主机加载其应用程序代码和用户内容。

```text theme={null}
anthropic.com
*.anthropic.com
claude.ai
*.claude.ai
claude.com
*.claude.com
claude.app
*.claude.app
*.claudeusercontent.com
*.claudemcpcontent.com
```

除非您为 [OTLP](<https://code.claude.com/docs/en/monitoring-usage>)、LLM 网关或 MCP 服务器配置自定义端口，否则流量为 443 上的 HTTPS。

对于代理服务器、自定义证书颁发机构、mTLS 以及独立 CLI 所需的域，请参阅[网络配置](<https://code.claude.com/docs/en/network-config>)。

要减少防火墙通配符的数量，请改为允许这些 Anthropic 主机。某些子域是动态生成的，并且必须保留通配符。

```text theme={null}
anthropic.com
api.anthropic.com
a-api.anthropic.com
a-cdn.anthropic.com
s-cdn.anthropic.com
assets-proxy.anthropic.com
claude.ai
a.claude.ai
assets.claude.ai
downloads.claude.ai
*.livepreview.claude.ai
claude.com
platform.claude.com
*.livepreview.claude.app
*.claudeusercontent.com
*.claudemcpcontent.com
```

如果您的组织为 Claude 启用了 [IP 白名单 ](<https://support.claude.com/en/articles/13200993-restrict-access-to-claude-with-ip-allowlisting>)，请通过与 `claude.ai` 和 `api.anthropic.com` 相同的代理出口路由 `bridge.claudeusercontent.com`。如果您无法以这种方式进行路由，请将您的代理用于该主机的出口地址添加到您组织的 IP 允许列表中，但前提是该地址专用于您的组织：共享代理出口范围还允许代理供应商的其他客户。

Anthropic 使用连接来自的地址对照您组织的 IP 白名单检查与该主机的连接。如果您的代理通过不在该允许列表中的地址发送流量，则 Chrome 中的 Claude 和通过网桥连接的其他功能将停止工作，而应用程序的其余部分将继续工作。

安 [神器](<https://code.claude.com/docs/en/artifacts>) 加载字体 [Google 字体](<https://code.claude.com/docs/en/artifacts#improve-the-visual-design>) 还要求 `fonts.googleapis.com` 和 `fonts.gstatic.com`。两个主机都是可选的。如果您阻止它们，工件将以后备字体呈现。通过快速拒绝而不是静默下降来阻止，以便字体请求立即失败而不是延迟页面的首次渲染。

工件还可以从 `cdnjs.cloudflare.com`、`cdn.jsdelivr.net`、`cdn.tailwindcss.com`、`code.jquery.com` 和 `unpkg.com` 加载 JavaScript 库，例如 React 或图表包，而不是从其他外部主机加载。如果您阻止这些主机，依赖于库的工件部分将不起作用，并且与阻止的字体不同，阻止的库没有后备。这里也使用快速拒绝进行阻止，因此被阻止的库请求会立即失败，而不是挂起直到超时。

### 身份验证和SSO

团队和企业组织可以要求所有用户使用 SSO。有关计划级别的详细信息，请参阅[身份验证](<https://code.claude.com/docs/en/authentication>)，以及[为SAML配置设置SSO](<https://support.claude.com/en/articles/13132885-setting-up-single-sign-on-sso>)； OIDC 设置包含在 [Claude 企业管理员指南 ](<https://claude.com/resources/tutorials/claude-enterprise-administrator-guide>) 中。

### 数据处理

Claude Code 在本地会话中或在 Anthropic 管理的基础设施上的云会话中本地处理您的代码，除非您的组织将它们路由到 [ 自托管环境 ](<https://code.claude.com/docs/en/self-hosted-environments>)。云会话（包括在自托管环境中）将对话和代码上下文发送到 Anthropic 的 API 进行处理；本地和 SSH 会话将它们发送到您的部署配置的 [ 模型提供程序 ](<#特性比较>)，默认情况下 Anthropic 的 API。有关数据保留、隐私和合规性的详细信息，请参阅[数据处理](<https://code.claude.com/docs/en/data-usage>)。

### 部署

桌面可以通过企业部署工具进行分发：

* **macOS**：使用 `.dmg` 安装程序通过 Jamf 或 Kandji 等 MDM 分发
* **Windows**：通过MSIX包部署。请参阅 [部署 Claude Windows 桌面 ](<https://support.claude.com/en/articles/12622703-deploy-claude-desktop-for-windows>) 了解企业部署选项（包括静默安装）

有关防火墙中要列入白名单的域，请参阅上面的 [ 网络访问要求 ](<#网络接入要求>)。有关代理设置、自定义证书颁发机构和 LLM 网关的信息，请参阅[网络配置](<https://code.claude.com/docs/en/network-config>)。

有关完整的企业配置参考，请参阅[企业配置指南](<https://support.claude.com/en/articles/12622667-enterprise-configuration>)。

## 来自CLI？

如果您已经使用Claude Code CLI，桌面将运行具有图形界面的相同底层引擎。您可以在同一台计算机上甚至在同一个项目上同时运行这两者。每个都保留自己的会话列表，您可以将 CLI 会话带入桌面。它们通过 CLAUDE.md 文件共享配置和项目内存。

要将 CLI 会话移至桌面，请在终端中运行 `/desktop`。 Claude 保存您的会话并在桌面应用程序中打开它，然后退出 CLI。当您使用 Claude 订阅登录时，此命令可在 macOS 和 x64 Windows 上使用。它不适用于 API 密钥身份验证，也不适用于 Amazon Bedrock、Google Cloud 的 Agent 平台或 Microsoft Foundry。

从您的 shell 中，[`claude --desktop`](<https://code.claude.com/docs/en/cli-reference#cli-flags>) 直接打开桌面，而无需启动终端会话。它需要 Claude Code v2.1.285 或更高版本，并且具有与 `/desktop` 相同的平台和登录要求。如果没有其他参数，它将在当前目录中打开桌面。要在 Desktop 中打开现有的 CLI 会话，请添加此目录中最近对话的 `--continue`，或添加 `--resume` 以及 `/status` 显示的会话 ID：

```bash theme={null}
claude --desktop --resume <session-id>
```

Claude Code 打印 `Opening session <session-id> in Claude Desktop`，会话在应用程序中打开，命令退出。会话名称不能代替 ID。 Claude Code 不会移动在另一个终端中打开或仍在后台运行的会话。如果未安装 Claude Desktop，该命令将打印下载链接并退出。

您还可以使用 `/resume` 从桌面内部获取 CLI 会话。该命令在本地会话中可用，但在 SSH、WSL 或云会话中不可用。

要在 Desktop 中继续终端会话：

1. 关闭终端中的会话。
2. 在桌面提示框中输入`/resume`。桌面列出了您从此计算机上的 CLI 启动的会话。按标题、文件夹或分支进行搜索，并预览每一项的结束位置。
3. 选择会话。它在应用程序中继续提供完整的对话和上下文。

桌面继续相同的会话而不是副本，因此终端中的 `claude --resume` 之后仍然可以找到它。



**提示：**

何时使用桌面与 CLI：当您想要在一个窗口中管理并行会话、并排排列窗格或直观地查看更改时，请使用桌面。当您需要脚本、自动化或更喜欢终端工作流程时，请使用 CLI。


### CLI 标志等效项

此表显示了常见 CLI 标志的桌面应用程序等效项。未列出的标志没有桌面等效项，因为它们是为脚本或自动化而设计的。

| CLI |桌面等效项|
| - | - |
| `--model sonnet` |发送按钮旁边的模型下拉菜单|
| `--resume`, `--continue` |单击侧栏中的会话，或在提示框中键入 `/resume` 以选取您从 CLI 启动的会话|
| `--permission-mode` |发送按钮旁边的模式选择器|
| `--dangerously-skip-permissions` |绕过权限模式。在Pro和Max套餐中，在设置→Claude Code→“允许绕过权限模式”中启用它；在团队和企业计划中，组织策略控制它|
| `--add-dir` |在云会话中使用 **+** 按钮添加多个存储库|
| `--allowedTools`, `--disallowedTools` |没有每个会话的等效项。 [设置文件](<https://code.claude.com/docs/en/settings>)中的权限规则仍然适用。|
| `--verbose` |[详细视图模式](<#切换视图模式>)|
| `--print`, `--output-format` |不可用。桌面仅是交互式的。|
|`ANTHROPIC_MODEL` 环境变量|发送按钮旁边的模型下拉菜单|
|`MAX_THINKING_TOKENS` 环境变量|在本地环境编辑器中设置。参见[环境配置](<#环境配置>)。|

### 共享配置

Desktop 和 CLI 读取相同的配置文件，因此您的设置会保留：

* 项目中的 **[CLAUDE.md](<https://code.claude.com/docs/en/memory>)** 和 `CLAUDE.local.md` 文件被两者使用
* 在 `~/.claude.json` 或 `.mcp.json` 中配置的 **[MCP 服务器 ](<https://code.claude.com/docs/en/mcp>)** 在两者中均有效
* 设置中定义的**[挂钩](<https://code.claude.com/docs/en/hooks>)**和**[技能](<https://code.claude.com/docs/en/skills>)**适用于两者
* **[`~/.claude.json`和`~/.claude/settings.json`中的设置](<https://code.claude.com/docs/en/settings>)**是共享的。 `settings.json` 中的权限规则、允许的工具和其他设置适用于桌面会话。
* **型号**：两者均提供相同的[型号](<https://code.claude.com/docs/en/model-config#available-models>)。在 Desktop 中，从发送按钮旁边的下拉列表中选择型号。您可以从同一下拉列表中更改模型。

#### 来自 Claude 桌面聊天应用程序的 MCP 服务器

桌面应用程序将 `claude_desktop_config.json` 中的 MCP 服务器以及来自 `~/.claude.json` 和 `.mcp.json` 的服务器加载到本地代码选项卡会话中。您在 `claude_desktop_config.json` 中定义的服务器在桌面聊天界面和本地代码选项卡会话中都可用。

如果您在 `claude_desktop_config.json` 和 `~/.claude.json` 或 `.mcp.json` 中定义相同的服务器名称，本地会话中的“代码”选项卡将连接一次并使用 `claude_desktop_config.json` 定义。

该应用程序还将 stdio 服务器从 `~/.claude.json` 重新传送到本地会话中的嵌入式 CLI。当 `~/.claude.json`（用户范围）和 `.mcp.json` 的顶层定义相同的 stdio 服务器名称时，“代码”选项卡使用 `~/.claude.json` 定义，与 CLI [ 范围层次结构 ](<https://code.claude.com/docs/en/mcp#scope-hierarchy-and-precedence>) 不同。



**注释：**

独立的CLI不读取`claude_desktop_config.json`。在macOS和WSL上，运行`claude mcp add-from-claude-desktop`将这些服务器复制到`~/.claude.json`中。有关导入流程和范围选项，请参阅 [从 Claude 桌面 ](<https://code.claude.com/docs/en/mcp#import-mcp-servers-from-claude-desktop>) 导入 MCP 服务器。


### 特性比较

该表比较了 CLI 和台式机之间的核心功能。有关 CLI 标志的完整列表，请参阅 [CLI 参考文献 ](<https://code.claude.com/docs/en/cli-reference>)。

|特点| CLI |桌面|
| - | - | - |
|权限模式|所有模式，包括 `dontAsk`|手动、接受编辑、计划和自动。启用后，绕过权限将显示在模式选择器中：通过 Pro 和 Max 计划上的“设置”切换，或通过 Team 和 Enterprise 计划上的组织策略|
|[第三方提供商](<https://code.claude.com/docs/en/third-party-integrations>)|Amazon Bedrock、Google 云的 Agent 平台、Microsoft Foundry| Anthropic's API 默认情况下。对于网关路由，请参阅 [将桌面应用程序连接到网关](<https://code.claude.com/docs/en/llm-gateway-connect#desktop-app>)。要在 Amazon Bedrock 上运行“代码”选项卡， Google 云的 Agent 平台， Microsoft Foundry，或自托管 LLM 网关，参见 [Claude 3P 桌面](<https://claude.com/docs/third-party/claude-desktop/overview>). |
|[MCP 服务器](<https://code.claude.com/docs/en/mcp>)|在设置文件中配置|用于本地和 SSH 会话的连接器 UI 或设置文件|
|[插件](<https://code.claude.com/docs/en/plugins/overview>)|`/plugin`命令|插件管理器 UI|
|@提及文件|基于文本|具有自动完成功能；仅限本地和 SSH 会话|
|文件附件|不可用|图片、PDF|
|会话隔离|[`--worktree`](<https://code.claude.com/docs/en/cli-reference>) 标志|启动会话时的 **worktree** 选项|
|多次会议|独立的终端|侧边栏选项卡|
|重复任务|Cron 作业，CI 管道|[计划任务](<https://code.claude.com/docs/en/desktop-scheduled-tasks>)|
|电脑使用|[通过macOS上的`/mcp`](<https://code.claude.com/docs/en/computer-use>)启用|[macOS 和 Windows 上的应用程序和屏幕控制 ](<#让claude使用你的电脑>)|
|iOS模拟器|通过[电脑驱动模拟器使用](<https://code.claude.com/docs/en/computer-use#test-a-simulator-flow>)|[iOS 模拟器窗格](<https://code.claude.com/docs/en/desktop-ios-simulator>) 自动打开|
|调度一体化|不可用|[侧栏中的调度会话](<#来自-dispatch-的会话>)|
|脚本编写和自动化| [`--print`](<https://code.claude.com/docs/en/cli-reference>), [Agent SDK](<https://code.claude.com/docs/en/headless>) |不可用|

### 桌面版中不可用的内容

除非另有说明，以下功能在 Desktop 中不可用：

* **第三方提供商**：桌面默认连接到Anthropic的API。要通过网关路由桌面，或在 Amazon Bedrock、Google 云的 Agent 平台、Microsoft Foundry 或自托管 LLM 网关上运行代码选项卡，请点击 [ 第三方提供商中的链接行](<#特性比较>)。
* **Linux（测试版）**：Linux 桌面应用程序中尚不支持计算机使用。请参阅 [Claude Linux 上的桌面 ](<https://code.claude.com/docs/en/desktop-linux>)。
* **内联代码建议**：桌面不提供自动完成样式的代码完成。它通过对话提示和显式代码更改来工作，并且可以在 Claude 回复后 [ 建议您的下一个提示 ](<#接受建议的提示>)。
* **Agent 团队**：协调团队，其中 Claude 作为团队负责人从共享任务列表中向队友分配任务，可在 [CLI](<https://code.claude.com/docs/en/agent-teams>) 中使用，而不是在桌面中使用。对于一个会话中的多个 agent 工作，请使用在桌面中运行的 [ 动态工作流程 ](<https://code.claude.com/docs/en/workflows>)； Claude还可以直接[发送消息并管理您的其他会话](<#跨会话工作>)。
* **终端对话框命令**：在终端中打开交互式面板的内置命令在“代码”选项卡中的行为有所不同。直接编辑 [ 设置文件 ](<https://code.claude.com/docs/en/settings>) 以管理权限规则和配置，或从独立 CLI 运行命令。
  * 无参数形式的命令，如`/permissions`，回复为`isn't available in this environment`。
  * `/config`打开设置→Claude Code。命令后的文本将被忽略，因此 `/config theme=dark` 不会设置主题。

## 故障排除

以下各节介绍特定于桌面应用程序的问题。对于聊天中出现的运行时 API 错误，例如 `API Error: 500`、`529 Overloaded`、`429` 或 `Prompt is too long`，请参阅 [错误参考 ](<https://code.claude.com/docs/en/errors>)。这些错误及其修复在 CLI、桌面和 Web 上都是相同的。

### 检查您的版本

要查看您正在运行的桌面应用程序的版本：

* **macOS**：点击菜单栏中的**Claude**，然后点击**关于Claude**
* **Windows**：点击**帮助**，然后点击**关于Claude**

单击版本号将其复制到剪贴板。

#### 代码选项卡中的 Claude Code 版本

要查看会话运行的 Claude Code 版本，请在本地会话中的 **Code** 选项卡中键入 `/status`，然后读取 **Claude Code** 行，该行显示 `2.1.286` 等版本。

要获取本地会话的较新版本，请在 macOS 上打开 **Claude → 检查更新 ** 或在 Windows 上打开 ** 帮助 → 检查更新 **，然后启动新会话。

在本地会话中，**Code**选项卡运行自己的Claude Code副本，该副本具有自己的版本号。桌面应用程序会下载并更新该副本，因此它可能与终端中的 `claude` 命令不同，并且更新一个副本不会更新另一个副本。

### 403 或代码选项卡中的身份验证错误

如果您在使用“代码”选项卡时看到 `Error 403: Forbidden` 或其他身份验证失败：

1. 从应用程序菜单注销并重新登录。这是最常见的修复方法。
2. 验证您有有效的付费订阅：Pro、Max、Team 或 Enterprise。
3. 如果 CLI 可以运行，但桌面不能运行，请完全退出桌面应用程序，而不仅仅是关闭窗口，然后重新打开并再次登录。
4. 检查您的互联网连接和代理设置。

### 启动时出现空白或卡住的屏幕

如果应用程序打开但显示空白或无响应的屏幕：

1. 重新启动应用程序。
2. 检查待处理的更新。在 macOS 和 Windows 上，应用程序会在启动时自动更新；在 Linux 上，通过 apt 进行更新，如 [Claude Linux 上的桌面 ](<https://code.claude.com/docs/en/desktop-linux>) 中所述。
3. 在托管网络上，确认您的防火墙允许 [ 网络访问要求 ](<#网络接入要求>) 中的 CDN 主机。
4. 在 Windows 上，检查事件查看器中 **Windows 日志 → 应用程序 ** 下的崩溃日志。

### “加载会话失败”

如果您看到 `Failed to load session`，则所选文件夹可能不再存在，Git 存储库可能需要未安装的 Git LFS，或者文件权限可能会阻止访问。尝试选择其他文件夹或重新启动应用程序。

### 会话未找到已安装的工具

如果 Claude 找不到 `npm`、`node` 或其他 CLI 命令等工具，请验证这些工具在常规终端中是否正常工作，检查 shell 配置文件是否正确设置 PATH，然后重新启动桌面应用程序以重新加载环境变量。

### Git 和 Git LFS 错误

在自己的工作树中运行的会话需要 Git。如果您看到“需要 Git”，请在 Windows 上安装 [Git](<https://git-scm.com/downloads>) 或 [Git for Windows](<https://git-scm.com/downloads/win>)，然后重试。在 Windows 上，Claude 1.49585.0 之前的桌面版本在启动任何本地会话之前要求使用 Git；如果您看到该提示并且没有使用工作树，请更新应用程序。

如果您看到“此存储库需要 Git LFS，但尚未安装”，请从 [git-lfs.com](<https://git-lfs.com/>) 安装 Git LFS，运行 `git lfs install`，然后重新启动应用程序。

### MCP 服务器无法在 Windows 上运行

如果 MCP 服务器切换不响应或服务器无法在 Windows 上连接，请检查服务器是否在您的设置中正确配置，重新启动应用程序，验证服务器进程是否在任务管理器中运行，并查看服务器日志是否有连接错误。

### 应用程序不会退出

* **macOS**：按 Cmd+Q。如果应用程序没有响应，请使用 Cmd+Option+Esc 强制退出，选择 Claude，然后单击强制退出。
* **Windows**：使用任务管理器并按 Ctrl+Shift+Esc 结束 Claude 进程。

### Windows 特定问题

* **PATH安装后未更新**：打开一个新的终端窗口。 PATH 更新仅适用于新的终端会话。
* **并发安装错误**：如果您看到有关正在进行的另一安装的错误，但实际没有安装，请尝试以管理员身份运行安装程序。

### 在 CLI 中打开时显示“分支尚不存在”

云会话可以创建本地计算机上不存在的分支。点击会话中的分支名称，选择**复制分支名称**，然后在本地获取：

```bash theme={null}
git fetch origin <branch-name>
git checkout <branch-name>
```

### 还卡住了吗？

* 在桌面应用程序中打开“帮助”→“获取支持”，或直接访问[Claude支持中心](<https://support.claude.com/>)
* 对于在独立版 `claude` CLI 中重现的问题，请在 [GitHub 问题 ](<https://github.com/anthropics/claude-code/issues>) 上搜索或提交错误

报告问题时，请包括您的桌面应用程序版本、操作系统、确切的错误消息和相关日志。在 macOS 上，检查 Console.app。在 Windows 上，检查事件查看器 → Windows 日志 → 应用程序。在将日志摘录发布到公共问题之前查看日志摘录；它们可以包含您环境中的文件路径和其他详细信息。


## 原文对照

<details>
<summary>展开完整原文</summary>

> ## Documentation Index
> Fetch the complete documentation index at: https://code.claude.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Desktop application

> Get more out of Claude Code Desktop: parallel sessions with Git isolation, drag-and-drop pane layout, integrated terminal and file editor, side chats, computer use, Dispatch sessions from your phone, visual diff review, app previews, PR monitoring, connectors, and enterprise configuration.

The Claude Desktop app has three tabs: **Chat** for conversations, **Cowork** for [Dispatch and longer agentic work](<https://claude.com/product/cowork>), and **Code** for software development. This page is the reference for the Code tab.



  

#### [Download for macOS](<https://claude.ai/api/desktop/darwin/universal/dmg/latest/redirect?utm_source=claude_code&utm_medium=docs>)

    Universal build for Intel and Apple Silicon
  


  

#### [Download for Windows](<https://claude.ai/api/desktop/win32/x64/setup/latest/redirect?utm_source=claude_code&utm_medium=docs>)

    For x64 processors
  


  

#### [Get Claude for Linux (beta)](<https://code.claude.com/docs/en/desktop-linux>)

    apt or .deb for Ubuntu and Debian
  


For Windows ARM64, download the [ARM64 installer](<https://claude.ai/api/desktop/win32/arm64/setup/latest/redirect?utm_source=claude_code\&utm_medium=docs>). On Linux, install with apt; see [Claude Desktop on Linux](<https://code.claude.com/docs/en/desktop-linux>).

After installing, launch Claude, sign in, and click the **Code** tab. For a walkthrough of your first session, see the [Get started guide](<https://code.claude.com/docs/en/desktop-quickstart>).

In the Code tab, each conversation is a **session**: it has its own chat history and project folder, independent of any other session. The sidebar lists your sessions and lets you run several in parallel. Within a session you can:

* [Review and comment on diffs](<#review-changes-with-diff-view>), then [watch the resulting PR through CI](<#monitor-pull-request-status>)
* [Preview your running app](<#preview-your-app>) in the Browser pane while Claude verifies its own changes, and [open external sites](<#browse-external-sites>) alongside it
* Watch Claude [run and test your iOS app](<https://code.claude.com/docs/en/desktop-ios-simulator>) in the iOS Simulator pane
* [Arrange panes](<#arrange-your-workspace>) for the chat, diff, browser, terminal, and file editor side by side
* Ask a [side question](<#ask-a-side-question-without-derailing-the-session>) that uses the session's context without derailing it
* Let Claude [check on, message, or archive your other sessions](<#work-across-sessions>)
* [Connect external tools](<#connect-external-tools>) like GitHub, Slack, and Linear
* Let Claude [open apps and control your screen](<#let-claude-use-your-computer>)
* Run on your machine, in the [cloud](<#run-long-running-tasks-in-the-cloud>), or over [SSH](<#ssh-sessions>)

For [scheduled recurring work](<https://code.claude.com/docs/en/desktop-scheduled-tasks>), [keyboard shortcuts](<#keyboard-shortcuts>), or [sending tasks from your phone](<#sessions-from-dispatch>), see the linked pages and sections. If you already use the terminal-based CLI, see the [CLI comparison](<#coming-from-the-cli>) for what carries over.

## Start a session

Before you send your first message, configure four things in the prompt area:

* **Environment**: choose where Claude runs. Select **Local** for your machine, **Cloud** for a [cloud session](<#cloud-sessions>) that continues after you close the app, an [**SSH connection**](<#ssh-sessions>) for a remote machine you manage, or on Windows a [**WSL distribution**](<https://code.claude.com/docs/en/desktop-wsl>). See [environment configuration](<#environment-configuration>).
* **Project folder**: select the folder or repository Claude works in. For cloud sessions, you can add [multiple repositories](<#run-long-running-tasks-in-the-cloud>).
* **Model**: pick a [model](<https://code.claude.com/docs/en/model-config#available-models>) from the dropdown next to the send button. You can change this during the session.
* **Permission mode**: choose how much autonomy Claude has from the [mode selector](<#choose-a-permission-mode>). You can change this during the session.

Type your task and press **Enter** to start. Each session tracks its own context and changes independently.

## Work with code

Give Claude the right context, control how much it does on its own, and review what it changed.

### Use the prompt box

Type what you want Claude to do and press **Enter** to send. Claude reads your project files, makes changes, and runs commands based on your [permission mode](<#choose-a-permission-mode>). You can redirect Claude at any point: click the stop button to interrupt immediately, or type a correction and press **Enter** to send it without stopping the running action. Claude reads the correction as soon as the current action completes and adjusts before its next step.

The **+** button next to the prompt box gives you access to file attachments, [skills](<#use-skills>), [connectors](<#connect-external-tools>), and [plugins](<#install-plugins>).

### Accept a suggested prompt

After Claude replies, the Code tab can show a suggested next prompt as gray text in the empty prompt box. Claude Code [generates each suggestion](<https://code.claude.com/docs/en/interactive-mode#prompt-suggestions>) from your conversation with a short background request that counts toward your plan's usage limits or your API costs.

* **Use the suggestion**: press **Tab** or **Right arrow** to place it in the prompt box, edit it if you want, then press **Enter** to send it. Pressing **Enter** before you accept the suggestion doesn't send it.
* **Write your own prompt**: start typing. The suggestion shows only while the prompt box is empty and has no attached files.

Go to **Settings > Claude Code** and turn off **Prompt suggestions** under **Sessions** to stop suggestions in each session from the next time it starts or resumes.

### Add files and context to prompts

The prompt box supports two ways to bring in external context:

* **@mention files**: type `@` followed by a filename to add a file to the conversation context. Claude can then read and reference that file. @mention is not available in cloud or WSL sessions.
* **Attach files**: attach images, PDFs, and other files to your prompt using the attachment button, or drag and drop files directly into the prompt. This is useful for sharing screenshots of bugs, design mockups, or reference documents.

### Choose a permission mode

Permission modes control how much autonomy Claude has during a session: whether it asks before editing files, running commands, or both. You can switch permission modes at any time using the mode selector next to the send button. To approve each change yourself, switch to Manual.

To set a default mode for new local sessions, add `permissions.defaultMode` to your [settings file](<https://code.claude.com/docs/en/settings#where-settings-live>). The desktop app reads the same settings files as the CLI. A mode you pick in the selector is remembered per folder and takes precedence over `defaultMode` for that folder, except Plan, which applies to the current session only.

| Mode | Settings key | Behavior |
| - | - | - |
| **Manual** | `default` | Claude asks before editing files or running commands. You see a diff and can accept or reject each change. |
| **Accept edits** | `acceptEdits` | Claude auto-accepts file edits and common filesystem commands like `mkdir`, `touch`, and `mv`, but still asks before running other terminal commands. Use this when you trust file changes and want faster iteration. |
| **Plan** | `plan` | Claude reads files and runs commands to explore, then proposes a plan without editing your source code. Good for complex tasks where you want to review the approach first. |
| **Auto** | `auto` | Claude runs without routine prompts; before actions such as shell commands and network requests run, a background classifier checks that they align with your request. Appears when [auto mode is available](<#auto-mode-availability>); there is no separate Settings toggle for it. |
| **Bypass permissions** | `bypassPermissions` | Claude runs without permission prompts, except for the [actions no mode auto-approves](<https://code.claude.com/docs/en/permission-modes#actions-no-mode-auto-approves>), safety classifiers when Claude [acts on external sites](<#browse-external-sites>), or desktop actions where Claude always asks first, such as [archiving a session](<#work-across-sessions>). Equivalent to `--dangerously-skip-permissions` in the CLI. On Pro and Max plans, enable it in your Settings → Claude Code under "Allow bypass permissions mode"; on Team and Enterprise plans there is no Settings toggle, and organization policy controls it instead. Only use this in sandboxed containers or VMs. |

Earlier versions of the Code tab labeled these modes Ask permissions, Auto accept edits, and Plan mode.

The `dontAsk` permission mode is available only in the [CLI](<https://code.claude.com/docs/en/permission-modes#allow-only-pre-approved-tools-with-dontask-mode>).



**提示：**

  Start complex tasks in Plan so Claude maps out an approach before making changes. Once you approve the plan, switch to Accept edits or Manual to execute it. See [explore first, then plan, then code](<https://code.claude.com/docs/en/best-practices#explore-first-then-plan-then-code>) for more on this workflow.


Cloud sessions support Accept edits, Plan, and Auto. Accept edits corresponds to `default` mode: cloud sessions pre-approve file edits, so the selector shows Accept edits instead of Manual. Bypass permissions isn't available in cloud sessions, including sessions in a [self-hosted environment](<https://code.claude.com/docs/en/self-hosted-environments>).

Enterprise admins can restrict which permission modes are available. See [enterprise configuration](<#enterprise-configuration>) for details.

<h4 id="auto-mode-availability">
  Auto mode availability
</h4>

Auto mode is available to all users on the Anthropic API and requires Claude Opus 4.6 or later, Sonnet 4.6 or later, Haiku 5.5, or a [Fable model](<https://code.claude.com/docs/en/model-config#work-with-fable>). Organization administrators can turn auto mode off with the `disableAutoMode` key in [managed settings](<#managed-settings>).

In Enterprise deployments that route Desktop to Google Cloud's Agent Platform, auto mode is also available by default; see [Auto mode on Bedrock, Agent Platform, or Foundry](<https://code.claude.com/docs/en/permission-modes#enable-auto-mode-on-bedrock-agent-platform-or-foundry>) for the supported models.

### Preview your app

Claude can start a dev server and open it in the Browser pane to verify its changes. This works for frontend web apps as well as backend servers: Claude can test API endpoints, view server logs, and iterate on issues it finds. In most cases, Claude starts the server automatically after editing project files. You can also ask Claude to preview at any time. By default, Claude [auto-verifies](<#auto-verify-changes>) changes after every edit.

The Browser pane can also open static HTML files, PDFs, images, and videos from your project. Click an HTML, PDF, image, or video path in the chat to open it there.

From the Browser pane, you can:

* Interact with your running app directly in the Browser pane
* Watch Claude verify its own changes automatically: it takes screenshots, inspects the DOM, clicks elements, fills forms, and fixes issues it finds
* Start or stop servers, or stop them all at once, from the **Dev servers** menu in the Browser pane's header
* Choose whether the Browser keeps cookies after you quit the app with **Keep cookies** in the Browser pane's **⋮** menu, so you don't have to sign in again during development

Claude creates the initial server configuration based on your project. If your app uses a custom dev command, edit `.claude/launch.json` to match your setup. See [Configure preview servers](<#configure-preview-servers>) for the full reference.

To clear the Browser's saved data, select **Clear browsing data** in the Browser pane's **⋮** menu. To turn the Browser off entirely, turn off **Browser tools** in **Settings > Claude Code**.

### Browse external sites

The Browser pane is a tabbed browser, so you can open documentation, issue trackers, or any other site next to your running app. To open the Browser, press **Cmd+Shift+B** on macOS or **Ctrl+Shift+B** on Windows, or click **Browser** in the session's title bar. You can sign in to sites in the pane, including popup sign-in flows such as Google OAuth.

The first time you click an external link in the chat, a dialog asks whether links open in the Browser pane or in your default browser. To change your choice later, use **Open links in built-in browser** in the Browser pane's **⋮** menu. **Cmd**-click on macOS or **Ctrl**-click on Windows opens a link in your default browser directly.

Claude can read and interact with external pages using the same tools it uses to [verify your app](<#preview-your-app>), with two additional safety checks:

* Safety classifiers review Claude's write actions on external pages, such as clicking and typing, in every permission mode. These are the same classifiers [auto mode](<#choose-a-permission-mode>) uses, and when they flag an action, you get a permission prompt regardless of mode.
* In permission modes other than Auto and Bypass permissions, a domain allowlist check also applies before Claude navigates to a new site.

#### Approve Claude's actions on a site

The first time Claude acts on an external site, a permission card appears and Claude waits for your choice: **Allow once**, **Always allow**, or **Deny**. **Allow once** approves the action without saving anything. **Always allow** saves the approval for that site on your device, and you can revoke it in Settings. Each site needs its own approval, including subdomains. Your local dev servers and project files don't need approval, so [auto-verify](<#auto-verify-changes>) keeps working without prompts.

Even on an approved site, Claude won't purchase items, create accounts, or bypass CAPTCHAs without your input. Browsing in the Browser pane uses the same safety model as the [Claude in Chrome extension](<https://code.claude.com/docs/en/chrome>). See [Using Claude in Chrome safely](<https://support.claude.com/en/articles/12902428-using-claude-in-chrome-safely>) for how Claude handles sensitive sites and risky actions.

#### Choose between the Browser and the Chrome extension

The Browser pane uses a clean browser profile, separate from your personal browser, with none of your saved logins or history. Use it for building and testing your app and for sites that don't need your identity. When you want Claude to act as you in your logged-in sessions, use the [Claude in Chrome extension](<https://code.claude.com/docs/en/chrome>) instead, which shares your browser's login state.

#### Restrict external browsing for your organization

The Browser follows the same [site allowlist and blocklist controls](<https://support.claude.com/en/articles/13065128-claude-in-chrome-admin-controls>) as the Claude in Chrome extension. If your organization already configured those lists for the extension, the Browser respects them automatically. Administrators can also turn off Claude's tools on external pages with the [`browserExternalPageTools` managed setting](<#managed-settings>). With tools disabled, users can still visit external sites; Claude's tools can't read or act on them.

To turn off external browsing entirely, set the [`disableBrowserExternalNavigation` managed setting](<#managed-settings>) to `true`. This blocks all external navigation in the Browser, including sites on your organization's allowlist; localhost dev servers and file previews keep working. Use `browserExternalPageTools` to let users keep browsing external sites without Claude's tools, and `disableBrowserExternalNavigation` to block external sites for both users and Claude.

### Review changes with diff view

After Claude makes changes to your code, the diff view lets you review modifications file by file before creating a pull request.

When Claude changes files, a diff stats indicator appears showing the number of lines added and removed, such as `+12 -1`. Click this indicator to open the diff viewer, which displays a file list on the left and the changes for each file on the right.

To comment on specific lines, click any line in the diff to open a comment box. Type your feedback and press **Enter** to add the comment. After adding comments to multiple lines, submit all comments at once:

* **macOS**: press **Cmd+Enter**
* **Windows**: press **Ctrl+Enter**

Claude reads your comments and makes the requested changes, which appear as a new diff you can review.

### Review your code

To have Claude review your changes before you commit, enter `/code-review` in the [prompt box](<#use-the-prompt-box>). The findings arrive in the conversation when the review completes.

In local, [SSH](<#ssh-sessions>), and [WSL](<https://code.claude.com/docs/en/desktop-wsl>) sessions, the findings appear as a **Code review** card, grouped by file. Use the card to act on them:

* Click **Walk through in diff** to open the diff view and step through the findings one at a time. A finding in the current diff appears at its line, where you can click **Fix this one** or dismiss it.
* Click **Apply fixes** to ask Claude to fix the findings that are still open.

In any session, you can also ask Claude in the prompt box to fix what the review found. For what `/code-review` looks for and the arguments it takes, see [Review a diff locally](<https://code.claude.com/docs/en/code-review#review-a-diff-locally>).

### Monitor pull request status

After you open a pull request, a CI status bar appears in the session. Claude Code uses the GitHub CLI to poll check results and surface failures.

* **Auto-fix CI & address comments**: when enabled, Claude automatically attempts to fix failing CI checks by reading the failure output and iterating. In a local session, Claude also addresses new review comments that someone other than you leaves, when the author is the repository's owner, an organization member, a collaborator, or a GitHub App.
* **Auto-merge when ready**: when enabled, Claude merges the PR once all checks pass. The merge method is squash. Enable auto-merge in your [GitHub repository settings](<https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/configuring-pull-request-merges/managing-auto-merge-for-pull-requests-in-your-repository>) first; without it, Claude can't merge the PR.

To turn these on, click **CI** in the status bar. To archive the session automatically once the PR merges or closes, turn on [auto-archive](<#work-in-parallel-with-sessions>) in **Settings > Claude Code**.



**注释：**

  PR monitoring requires the [GitHub CLI (`gh`)](<https://cli.github.com/>) to be installed and authenticated on your machine. If `gh` is not installed, Desktop prompts you to install it the first time you try to create a PR.


## Arrange your workspace

The Code tab is built around panes you can arrange in any layout: chat, diff, browser, terminal, file, plan, tasks, and subagent, along with the [iOS Simulator](<https://code.claude.com/docs/en/desktop-ios-simulator>) on macOS. Drag a pane by its header to reposition it, or drag a pane edge to resize it. Press **Cmd+\\** on macOS or **Ctrl+\\** on Windows to close the focused pane. Click **Terminal**, **Changes**, or **Browser** in the session's title bar to open the terminal, diff, or Browser pane. The **⋮** menu beside them opens more panes, such as **Files**, and holds those buttons when the window is too narrow to show them.

To work across screens, pop a pane such as the diff or terminal out into its own window, and dock it back when you're done. Claude keeps working in the main window.



**注释：**

  The pane layout, terminal, file editor, and view modes in this section require Claude Desktop v1.2581.0 or later. Open **Claude → Check for Updates** on macOS or **Help → Check for Updates** on Windows to update.


### Run commands in the terminal

The integrated terminal lets you run commands alongside your session without switching to another app. Click **Terminal** in the session's title bar or press **Ctrl+\`** on macOS or Windows. The terminal opens in your session's working directory and shares the same environment as Claude, so commands like `npm test` or `git status` see the same files Claude is editing. To open a second terminal tab, click **+** in the terminal pane header or right-click a folder in the chat to choose **Open in terminal**. The terminal is available in local and [SSH](<#ssh-sessions>) sessions.

### Open and edit files

Click a file path in the chat or diff viewer to open it in the file pane. HTML, PDF, image, and video paths open in the [Browser pane](<#preview-your-app>) instead. Make spot edits and click **Save** to write them back. If the file changed on disk since you opened it, the pane warns you and lets you override or discard. Click **Discard** to revert your edits, or click the path in the pane header to copy the absolute path.

The file pane is available in local and SSH sessions. For cloud sessions, ask Claude to make the change.

### Open files in other apps

Right-click any file path in the chat, diff viewer, or file pane to open a context menu:

* **Attach as context**: add the file to your next prompt
* **Open in**: open the file in an installed editor such as VS Code, Cursor, or Zed
* **Show in Finder** on macOS, **Show in Explorer** on Windows: open the containing folder
* **Copy path**: copy the absolute path to your clipboard

### Switch view modes

View modes control how much detail appears in the chat transcript. To switch view modes, open the session menu from the caret beside the session title and select **Transcript view**, or press **Ctrl+O** on macOS or Windows to cycle through them. The Thinking mode appears in the menu only after Claude has produced thinking in the session you're viewing.

| Mode | What it shows |
| - | - |
| **Normal** | Tool calls collapsed into summaries, with full text responses |
| **Thinking** | Tool calls collapsed into summaries, plus Claude's thinking |
| **Verbose** | Every tool call, file read, and intermediate step Claude takes, plus Claude's thinking |

Use Thinking to follow Claude's reasoning with tool calls still collapsed. Use Verbose when debugging why Claude took a particular action. Claude Desktop versions before 1.46388.1 also list a Summary mode, and a session still set to Summary opens in Normal once you update.

### Keyboard shortcuts

Press **Cmd+/** on macOS or **Ctrl+/** on Windows to see all shortcuts available in the Code tab. On Windows, use **Ctrl** in place of **Cmd** for the shortcuts below. Session cycling, the terminal toggle, and the view-mode toggle use **Ctrl** on every platform.

| Shortcut | Action |
| - | - |
| `Cmd` `/` | Show keyboard shortcuts |
| `Cmd` `N` | New session |
| `Cmd` `W` | Close session |
| `Ctrl` `Tab` / `Ctrl` `Shift` `Tab` | Next or previous session |
| `Cmd` `Shift` `]` / `Cmd` `Shift` `[` | Next or previous session |
| `Esc` | Stop Claude's response |
| `Tab` / `Right arrow` | [Accept the suggested prompt](<#accept-a-suggested-prompt>) in an empty prompt box |
| `Cmd` `Shift` `D` | Toggle diff pane |
| `Cmd` `Shift` `B` | Toggle Browser pane |
| `Cmd` `Shift` `S` | Select an element in the Browser |
| `Ctrl` `` ` `` | Toggle terminal pane |
| `Cmd` `\` | Close focused pane |
| `Cmd` `;` | Open side chat |
| `Ctrl` `O` | Cycle view modes |
| `Cmd` `Shift` `M` | Open permission mode menu |
| `Cmd` `Shift` `I` | Open model menu |
| `Cmd` `Shift` `E` | Open effort menu |
| `1`–`9` | Select item in an open menu |

These shortcuts apply to the Code tab. In Desktop, `Shift+Tab` doesn't cycle permission modes as it does in the terminal's [interactive mode](<https://code.claude.com/docs/en/interactive-mode#keyboard-shortcuts>).

### Check usage

Click the usage ring next to the model picker to see your current context window usage and your plan usage for the period. Context usage is per session; plan usage is shared across all your Claude Code surfaces.

## Let Claude use your computer

Computer use lets Claude open your apps, control your screen, and work directly on your machine the way you would. Ask Claude to interact with a desktop tool that has no CLI, or automate something that only works through a GUI. For running and testing iOS apps, Desktop opens the dedicated [iOS Simulator pane](<https://code.claude.com/docs/en/desktop-ios-simulator>) instead of controlling your screen; the pane works without enabling computer use.



**注释：**

  Computer use is a research preview on macOS and Windows that requires a Pro or Max plan. It is not available on Team or Enterprise plans. The Claude Desktop app must be running.


Computer use is off by default. [Enable it in Settings](<#enable-computer-use>) before Claude can control your screen. On macOS, you also need to grant Accessibility and Screen Recording permissions.

On macOS, computer use can also run in the background: Claude works in the apps you've approved while you keep working.



**注意：**

  Unlike the [sandboxed Bash tool](<https://code.claude.com/docs/en/sandboxing>), computer use runs on your actual desktop with access to whatever you approve. Claude checks each action and flags potential prompt injection from on-screen content, but the trust boundary is different. See the [computer use safety guide](<https://support.claude.com/en/articles/14128542>) for best practices.


### When computer use applies

Claude has several ways to interact with an app or service, and computer use is the broadest and slowest. It tries the most precise tool first:

* If you have a [connector](<#connect-external-tools>) for a service, Claude uses the connector.
* If the task is a shell command, Claude uses Bash.
* If the task is browser work and you have [Claude in Chrome](<https://code.claude.com/docs/en/chrome>) set up, Claude uses that.
* If the task is running or testing an iOS app, Claude uses the [iOS Simulator pane](<https://code.claude.com/docs/en/desktop-ios-simulator>), which doesn't use screen control.
* If none of those apply, Claude uses computer use.

The [per-app access tiers](<#app-permissions>) reinforce this: browsers are capped at view-only, and terminals and IDEs at click-only, steering Claude toward the dedicated tool even when computer use is active. Screen control is reserved for things nothing else can reach, like native apps, hardware control panels, or proprietary tools without an API.

### Enable computer use

Computer use is off by default. If you ask Claude to do something that needs it while it's off, Claude tells you it could do the task if you enable computer use in Settings.



  

#### Update the desktop app

    Make sure you have the latest version of Claude Desktop. On macOS and Windows, download or update at [claude.com/download](<https://claude.com/download>); on Linux, update through your package manager ([instructions](<https://code.claude.com/docs/en/desktop-linux>)). Then restart the app.
  


  

#### Turn on the toggle

    In the desktop app, go to **Settings > This computer > System**. Under **Computer use**, turn on **Enable computer use**. On Windows, the toggle takes effect immediately and setup is complete. On macOS, continue to the next step.

    If you don't see the toggle, confirm you're on macOS or Windows with a Pro or Max plan, then update and restart the app.
  


  

#### Grant macOS permissions

    On macOS, grant two system permissions before the toggle takes effect:

    * **Accessibility**: lets Claude click, type, and scroll
    * **Screen Recording**: lets Claude see what's on your screen

    The Settings page shows the current status of each permission. If either is denied, click the badge to open the relevant System Settings pane.
  


### App permissions

The first time Claude needs to use an app, a prompt appears in your session. Click **Allow for this session** or **Deny**. Approvals last for the current session, or 30 minutes in [Dispatch-spawned sessions](<#sessions-from-dispatch>).

The prompt also shows what level of control Claude gets for that app. These tiers are fixed by app category and can't be changed:

| Tier | What Claude can do | Applies to |
| :- | :- | :- |
| View only | See the app in screenshots | Browsers, trading platforms |
| Click only | Click and scroll, but not type or use keyboard shortcuts | Terminals, IDEs |
| Full control | Click, type, drag, and use keyboard shortcuts | Everything else |

Apps with broad reach, like terminals, Finder or File Explorer, and System Settings or Settings, show an extra warning in the prompt so you know what approving them grants.

The **Computer use** section in **Settings > This computer > System** includes these options:

* **Denied apps**: add apps here to reject them without prompting. Claude may still affect a denied app indirectly through actions in an allowed app, but it can't interact with the denied app directly.
* **Unhide apps when Claude finishes**: when computer use isn't running in the background, Claude hides your other windows while it works so it interacts with only the approved app. When Claude finishes, hidden windows are restored unless you turn this setting off.

## Manage sessions

Each session is an independent conversation with its own context and changes. You can run multiple sessions in parallel, branch off side chats, let Claude check on and message your other sessions, send work to the cloud, or let Dispatch start sessions for you from your phone.

### Work in parallel with sessions

Click **+ New session** in the sidebar, or press **Cmd+N** on macOS or **Ctrl+N** on Windows, to work on multiple tasks in parallel. Press **Ctrl+Tab** and **Ctrl+Shift+Tab** to cycle through sessions in the sidebar. For Git repositories, select the **worktree** option next to the branch name to give the session its own isolated copy of your project using [Git worktrees](<https://code.claude.com/docs/en/worktrees>).

To view two sessions at once, hold **Cmd** on macOS or **Ctrl** on Windows and click a session in the sidebar. The session opens in a second pane alongside the one you already have open. While the split is active, clicking another sidebar session replaces whichever pane has focus. Press **Cmd+\\** on macOS or **Ctrl+\\** on Windows to close the focused pane and return to a single session.

Worktrees are stored in `<project-root>/.claude/worktrees/` by default. You can change this to a custom directory in Settings → Claude Code under "Worktree location". You can also set a branch prefix that gets prepended to every worktree branch name, which is useful for keeping Claude-created branches organized. To remove a worktree when you're done, hover over the session in the sidebar and click the archive icon. To have sessions archive themselves when their pull request merges or closes, turn on **Auto-archive after PR merge or close** in Settings → Claude Code. Auto-archive only applies to local sessions that have finished running.

To include gitignored files like `.env` in new worktrees, create a [`.worktreeinclude` file](<https://code.claude.com/docs/en/worktrees#copy-gitignored-files-into-worktrees>) in your project root.



**注释：**

  Session isolation requires [Git](<https://git-scm.com/downloads>). Most Macs include Git by default. Run `git --version` in Terminal to check; if it prints a version number, Git is installed. If you run into Git errors, ask Claude in the [Cowork tab](<https://claude.com/product/cowork>) to help troubleshoot your setup.


Use the controls at the top of the sidebar to filter sessions by status, project, or environment, and to group sessions by project. To rename a session, click the session title in the toolbar at the top of the active session.

To check context usage, see [Check usage](<#check-usage>). When context fills up, Claude automatically summarizes the conversation and continues working. You can also type `/compact` to trigger summarization earlier and free up context space. See [the context window](<https://code.claude.com/docs/en/how-claude-code-works#the-context-window>) for details on how compaction works.

The desktop app sends an OS notification when a Code session finishes a task and you aren't currently viewing that session. For sessions that belong to a [project](<https://code.claude.com/docs/en/claude-projects#see-what-needs-you-in-overview>), you get the project's notifications instead.

### Ask a side question without derailing the session

A side chat lets you ask Claude a question that uses your session's context but doesn't add anything back to the main conversation. Use it when you want to understand a piece of code, check an assumption, or explore an idea without steering the session off course.

Press **Cmd+;** on macOS or **Ctrl+;** on Windows to open a side chat, or type `/btw` in the prompt box. The side chat can read everything in the main thread up to that point. When you're done, close the side chat and continue the main session where you left off.

Side chats are available in local, SSH, and WSL sessions. The desktop app doesn't save side chats to disk, so you can't return to one after you close the app.

### Watch background tasks

The tasks pane shows the background work running inside the current session: subagents, background shell commands, and [dynamic workflows](<https://code.claude.com/docs/en/workflows>). Once the session has background work, open the pane with **Background tasks** in the title bar's **⋮** menu.

Click any entry to see its output in the subagent pane or stop it. To see what other sessions are doing, use the [sidebar](<#work-in-parallel-with-sessions>), or ask Claude to [check on them for you](<#work-across-sessions>).

### Work across sessions

Claude can list your other Code tab sessions, read what each has been doing, and send messages between them. Ask in plain language: "which session touched the auth refactor?", "what did the API session conclude?", or "tell the payments session the schema changed". You can also ask Claude to rename or archive a session. Claude archives a session the same way the sidebar's archive icon does, so ask it to clean up sessions whose PRs have merged.

Through this surface, Claude sees only the sessions the desktop app runs itself: local, [SSH](<#ssh-sessions>), and [WSL](<https://code.claude.com/docs/en/desktop-wsl>) sessions in the Code tab. Claude doesn't see cloud sessions, or sessions you started from the terminal CLI or the VS Code extension, even in worktrees of the same project, so with nine terminal worktrees open and two desktop sessions, Claude answering in one of them reports the one other desktop session. Claude never lists the session you're asking from. By default it sees the 20 most recently active sessions and skips archived sessions unless you ask for them. [Cross-session messaging](<https://code.claude.com/docs/en/cross-session-messaging>) separately lets Claude message [your other Claude Code sessions](<https://code.claude.com/docs/en/cross-session-messaging#see-which-sessions-claude-can-reach>), including terminal sessions.

When Claude messages another session through this surface, Claude Code shows it there as a card labeled with the sending session's title and a link back, so you can always tell where a message came from. If the receiving session is mid-task, Claude Code holds the message and Claude reads it once the current work finishes. The receiving Claude can reply, and Claude Code delivers the reply back through this surface. Claude can't deliver to an archived session, and tells you when a message doesn't go through.

Claude Code applies four safety behaviors across sessions:

* Before archiving any session, Claude asks you first. You see the approval card in every permission mode, including Auto and Bypass permissions.
* Through this surface, Claude can't send cross-session messages from a session nobody is watching, such as a scheduled-task run, and can't deliver messages into one.
* Claude Code checks each message from this surface against the receiving session's [inbound controls](<https://code.claude.com/docs/en/cross-session-messaging#control-inbound-messages>), even when the receiving session doesn't have [cross-session messaging](<https://code.claude.com/docs/en/cross-session-messaging#availability>) itself. If you set [`crossSessionInbound`](<https://code.claude.com/docs/en/settings-reference#crosssessioninbound>) to `refuse` in the receiving session, Claude Code drops messages from this surface. Claude Code reports the refusal to the Claude desktop app. Before v2.1.234, Claude Code dropped every message from this surface to a receiving session without cross-session messaging.
* Claude Code quotes each incoming message and attributes it to the session that sent it, and Claude still follows the receiving session's own permission settings when acting on one.

Claude can also suggest new sessions. When it notices something worth fixing that's out of scope for the current task, it offers the work as a task chip in the chat. Click the chip to start that work in a new session with its own worktree; Claude continues your current session uninterrupted.

### Run long-running tasks in the cloud

For large refactors, test suites, migrations, or other long-running tasks, select **Cloud** instead of **Local** when starting a session. Cloud sessions run on Anthropic-managed infrastructure by default and continue even if you close the app or shut down your computer. Check back anytime to see progress or steer Claude in a different direction. You can also monitor cloud sessions from [claude.ai/code](<https://claude.ai/code>) or the [Claude mobile app](<https://code.claude.com/docs/en/mobile>).

Cloud sessions also support multiple repositories. After selecting a cloud environment, click the **+** button next to the selected repository to add more repositories to the session. Each repo gets its own branch selector. This is useful for tasks that span multiple codebases, such as updating a shared library and its consumers.

See [Use Claude Code in the cloud](<https://code.claude.com/docs/en/claude-code-on-the-web>) for more on how cloud sessions work. When one body of work needs many cloud sessions, select **Projects** in the sidebar to create a [project](<https://code.claude.com/docs/en/claude-projects>), where Claude starts and tracks the sessions for you from one conversation.

### Continue in another surface

To continue a session somewhere else, open the session menu from the caret beside the session title or from the session's row in the sidebar, then select **Open in**:

* Select **Cloud** to continue the session as a [cloud session](<https://code.claude.com/docs/en/claude-code-on-the-web>), with your conversation carried over as a summary. Before you confirm, the dialog states whether your files move too and whether this session is archived once the cloud one is ready. You can't move a session that runs over [SSH](<#ssh-sessions>) or in [WSL](<https://code.claude.com/docs/en/desktop-wsl>) this way.
* Select an installed editor or your file manager to open the session's folder on disk there.

### Control which sessions appear on your other devices

A local session shows up on your other devices once [Remote Control](<https://code.claude.com/docs/en/remote-control>) connects it. A connected session appears in the session list at [claude.ai/code](<https://claude.ai/code>) and in the Claude apps on devices signed in to your claude.ai account.

A local session connects when you turn Remote Control on for it, or when it connects automatically as it starts:

* **You turn it on for that session**: with the session's **Remote Control** switch, or by typing `/remote-control` in its prompt box.
* **It connects when it starts**: new sessions connect automatically while **Connect new sessions to Remote Control** is on in **Settings > Claude Code**. If you've never changed that setting, Desktop follows [`remoteControlAtStartup`](<https://code.claude.com/docs/en/settings-reference#remotecontrolatstartup>) in your user or managed settings, then your organization's default.

To see whether a session is connected, look at the laptop icon before the session title in the toolbar. The icon is highlighted while the session is connected or connecting. Click it to open the session's **Remote Control** switch.

To keep sessions off your other devices, turn Remote Control off at the level you need:

* **One session**: turn off its **Remote Control** switch. In a session that connected when it started, typing `/remote-control` leaves Remote Control on and shows `Remote Control is already on. This session connected automatically when it started.` Click **Turn off** on that line to disconnect.
* **New Desktop sessions on this computer**: turn off **Connect new sessions to Remote Control** in **Settings > Claude Code**. If it already shows off, turn it on and then off so Desktop saves your choice. Once saved, it takes precedence over `remoteControlAtStartup` and the defaults.
* **Any session on this computer, including the CLI**: set [`disableRemoteControl`](<https://code.claude.com/docs/en/settings-reference#disableremotecontrol>) to `true` in `~/.claude/settings.json` to stop sessions from connecting. A session that was already connected when you saved the file stays connected until you turn Remote Control off for it.

To hide a session that already appears on your other devices, archive it in Desktop. Desktop archives the session's Remote Control copy too, so it leaves the default session list on those devices. To view or delete it there, see [Archive sessions](<https://code.claude.com/docs/en/claude-code-on-the-web#archive-sessions>).

### Sessions from Dispatch

[Dispatch](<https://support.claude.com/en/articles/13947068>) is a persistent conversation with Claude that lives in the [Cowork](<https://claude.com/product/cowork>) tab. You message Dispatch a task, and it decides how to handle it.

A task can end up as a Code session in two ways: you ask for one directly, such as "open a Claude Code session and fix the login bug", or Dispatch decides the task is development work and spawns one on its own. Tasks that typically route to Code include fixing bugs, updating dependencies, running tests, or opening pull requests. Research, document editing, and spreadsheet work stay in Cowork.

Either way, the Code session appears in the Code tab's sidebar with a **Dispatch** badge. You get a push notification on your phone when it finishes or needs your approval.

If you have [computer use](<#let-claude-use-your-computer>) enabled, Dispatch-spawned Code sessions can use it too. App approvals in those sessions expire after 30 minutes and re-prompt, rather than lasting the full session like regular Code sessions.

For setup, pairing, and Dispatch settings, see the [Dispatch help article](<https://support.claude.com/en/articles/13947068>). Dispatch requires a Pro or Max plan and is not available on Team or Enterprise plans.

Dispatch is one of several ways to work with Claude when you're away from your terminal. For a comparison with the other options, see [Platforms and integrations](<https://code.claude.com/docs/en/platforms#work-when-you-are-away-from-your-terminal>).

## Extend Claude Code

Connect external services, add reusable workflows, customize Claude's behavior, and configure preview servers. To manage connectors, skills, and plugins in one place, click **Customize** in the sidebar. The [Cowork](<https://claude.com/product/cowork>) tab in the Desktop app sources its skills, plugins, and connectors from this Customize configuration, which syncs through your claude.ai account, not from the CLI's `~/.claude` directory.

Claude Code also loads the skills and plugins enabled for your claude.ai account in terminal sessions where you sign in with the same account. See [Skills synced from claude.ai](<https://code.claude.com/docs/en/skills#how-synced-skills-behave>) and [Plugins synced from claude.ai](<https://code.claude.com/docs/en/plugins/loading#synced-plugins>).

### Connect external tools

For local and [SSH](<#ssh-sessions>) sessions, click the **+** button next to the prompt box and select **Connectors** to add integrations like Google Calendar, Slack, GitHub, Linear, Notion, and more. You can add connectors before or during a session. The **+** button is not available in cloud or WSL sessions, but [routines](<https://code.claude.com/docs/en/routines>) configure connectors at routine creation time.

To manage or disconnect connectors, go to Settings → Connectors in the desktop app, or select **Manage connectors** from the Connectors menu in the prompt box.

Once connected, Claude can read your calendar, send messages, create issues, and interact with your tools directly. You can ask Claude what connectors are configured in your session.

Connectors are [MCP servers](<https://code.claude.com/docs/en/mcp>) with a graphical setup flow. Use them for quick integration with supported services. For integrations not listed in Connectors, add MCP servers manually via [settings files](<https://code.claude.com/docs/en/mcp#installing-mcp-servers>). You can also [create custom connectors](<https://support.claude.com/en/articles/11175166-getting-started-with-custom-connectors-using-remote-mcp>).

### Use skills

[Skills](<https://code.claude.com/docs/en/skills>) extend what Claude can do. Claude loads them automatically when relevant, or you can invoke one directly: type `/` in the prompt box or click the **+** button and select **Slash commands** to browse what's available. This includes [built-in commands](<https://code.claude.com/docs/en/commands>), your [custom skills](<https://code.claude.com/docs/en/skills#create-your-first-skill>), project skills from your codebase, and skills from any [installed plugins](<https://code.claude.com/docs/en/plugins/install>). Select one and it appears highlighted in the input field. Type your task after it and send as usual.

You can send a command while Claude is working, the same as any other message, and the session returns to idle once the turn finishes. Before v2.1.206, a command sent mid-turn could leave the session showing as running and messages you sent afterward weren't delivered.

Local sessions load your personal skills from `~/.claude/skills/`. An [SSH](<#ssh-sessions>) session reads `~/.claude/skills/` from the remote host's home directory, not from your machine.

Local and cloud sessions also load the skills enabled for your claude.ai account, except local sessions when your organization sets [`disableSideloadFlags`](<https://code.claude.com/docs/en/settings-reference#disablesideloadflags>). Cloud sessions load them instead of `~/.claude/skills/`, as [Skills in Cowork and cloud sessions](<https://code.claude.com/docs/en/skills#skills-in-cowork-and-cloud-sessions>) describes.

### Install plugins

[Plugins](<https://code.claude.com/docs/en/plugins/overview>) are reusable packages that add skills, agents, hooks, MCP servers, and LSP configurations to Claude Code. You can install plugins from the desktop app without using the terminal.

For local and [SSH](<#ssh-sessions>) sessions, click the **+** button next to the prompt box and select **Plugins** to see your installed plugins and their skills. To add a plugin, select **Add plugin** from the submenu to open the plugin browser, which shows available plugins from your configured [marketplaces](<https://code.claude.com/docs/en/plugins/overview>) including the official Anthropic marketplace. Select **Manage plugins** to enable, disable, or uninstall plugins.

You can scope plugins to your user account, a specific project, or local-only. If your organization manages plugins centrally, those plugins are available in desktop sessions the same way they are in the CLI, except the ones the desktop app [withholds under `disableSideloadFlags`](<https://code.claude.com/docs/en/settings-reference#disablesideloadflags>).

The plugin browser is not available in cloud sessions, and plugins you install from the desktop app aren't available for cloud sessions. A cloud session also doesn't install plugins that the repository's `.claude/settings.json` declares, as [What carries over from your setup](<https://code.claude.com/docs/en/cloud-environments#what-carries-over-from-your-setup>) explains. Plugins aren't available in WSL sessions. For the full plugin reference including creating your own plugins, see [plugins](<https://code.claude.com/docs/en/plugins/overview>).

### Configure preview servers

Claude automatically detects your dev server setup and stores the configuration in `.claude/launch.json` at the root of the folder you selected when starting the session. Preview uses this folder as its working directory, so if you selected a parent folder, subfolders with their own dev servers won't be detected automatically. To work with a subfolder's server, either start a session in that folder directly or add a configuration manually.

To customize how your server starts, for example to use `yarn dev` instead of `npm run dev` or to change the port, edit `.claude/launch.json`. The file supports JSON with comments.

```json theme={null}
{
  "version": "0.0.1",
  "configurations": [
    {
      "name": "my-app",
      "runtimeExecutable": "npm",
      "runtimeArgs": ["run", "dev"],
      "port": 3000
    }
  ]
}
```

You can define multiple configurations to run different servers from the same project, such as a frontend and an API. See the [examples](<#examples>) below.

#### Auto-verify changes

When `autoVerify` is enabled, Claude automatically verifies code changes after editing files. It takes screenshots, checks for errors, and confirms changes work before completing its response.

Auto-verify is on by default. Disable it per-project by adding `"autoVerify": false` to `.claude/launch.json`, or turn off **Auto-verify changes** in the Browser pane's **⋮** menu.

```json theme={null}
{
  "version": "0.0.1",
  "autoVerify": false,
  "configurations": [
    {
      "name": "my-app",
      "runtimeExecutable": "npm",
      "runtimeArgs": ["run", "dev"],
      "port": 3000
    }
  ]
}
```

When disabled, preview tools are still available and you can ask Claude to verify at any time. Auto-verify makes it automatic after every edit.

#### Configuration fields

Each entry in the `configurations` array accepts the following fields:

| Field | Type | Description |
| - | - | - |
| `name` | string | A unique identifier for this server |
| `runtimeExecutable` | string | The command to run, such as `npm`, `yarn`, or `node` |
| `runtimeArgs` | string\[] | Arguments passed to `runtimeExecutable`, such as `["run", "dev"]` |
| `port` | number | The port your server listens on. Defaults to 3000 |
| `cwd` | string | Working directory relative to your project root. Defaults to the project root. Use `${workspaceFolder}` to reference the project root explicitly |
| `env` | object | Additional environment variables as key-value pairs, such as `{ "NODE_ENV": "development" }`. Don't put secrets here since this file is committed to your repo. To pass secrets to your dev server, set them in the [local environment editor](<#local-sessions>) instead. |
| `autoPort` | boolean | How to handle port conflicts. See [Port conflicts](<#port-conflicts>) |
| `program` | string | A script to run with `node`. See [when to use `program` vs `runtimeExecutable`](<#when-to-use-program-vs-runtimeexecutable>) |
| `args` | string\[] | Arguments passed to `program`. Only used when `program` is set |
| `url` | string | The address the preview opens instead of `http://localhost:<port>`. See [open the preview at a specific URL](<#open-the-preview-at-a-specific-url>) |

<a id="when-to-use-program-vs-runtimeexecutable" />

##### When to use `program` vs `runtimeExecutable`

Use `runtimeExecutable` with `runtimeArgs` to start a dev server through a package manager. For example, `"runtimeExecutable": "npm"` with `"runtimeArgs": ["run", "dev"]` runs `npm run dev`.

Use `program` when you have a standalone script you want to run with `node` directly. For example, `"program": "server.js"` runs `node server.js`. Pass additional flags with `args`.

<a id="open-the-preview-at-a-specific-url" />

##### Open the preview at a specific URL

By default, the preview opens `http://localhost:<port>`. Set `url` when your server needs a different address. Common cases are servers that require local HTTPS, apps that use `*.localhost` subdomains, and apps that sign you in through a redirect.

```json theme={null}
{
  "version": "0.0.1",
  "configurations": [
    {
      "name": "my-app",
      "runtimeExecutable": "npm",
      "runtimeArgs": ["run", "dev"],
      "port": 8443,
      "url": "https://localhost:8443"
    }
  ]
}
```

Localhost addresses open directly, exactly like the default port address. This includes `localhost`, any `*.localhost` subdomain, `127.0.0.1`, and `::1`. For security, a localhost `url` must be your server's origin alone, with no path or query. Its port must match the entry's port. To show a specific page, ask Claude to navigate there after the preview opens. A localhost `url` with a path, query, or mismatched port is reported as a configuration error that names the url and shows the fix.

For any other address, Desktop asks for your permission the first time the preview opens it, the same as when you browse to a new site in the preview. External addresses may include paths. Choose **Always allow** to skip the prompt for that site in the future. Organization policies that restrict external sites in the preview still apply.

To preview a server you already run yourself, set `url` without a command. Claude attaches the preview to your running server instead of starting one:

```json theme={null}
{
  "version": "0.0.1",
  "configurations": [
    {
      "name": "my-app",
      "url": "https://app.localhost:3000"
    }
  ]
}
```

The `url` must be `http` or `https`, and must not contain a username or password.

#### Port conflicts

The `autoPort` field controls what happens when your preferred port is already in use:

* **`true`**: Claude finds and uses a free port automatically. Suitable for most dev servers.
* **`false`**: Claude fails with an error. Use this when your server must use a specific port, such as for OAuth callbacks or CORS allowlists.
* **Not set (default)**: Claude asks whether the server needs that exact port, then saves your answer.

When Claude picks a different port, it passes the assigned port to your server via the `PORT` environment variable.

#### Examples

These configurations show common setups for different project types:



  

#### `Next.js`

    This configuration runs a Next.js app using Yarn on port 3000:

    ```json theme={null}
    {
      "version": "0.0.1",
      "configurations": [
        {
          "name": "web",
          "runtimeExecutable": "yarn",
          "runtimeArgs": ["dev"],
          "port": 3000
        }
      ]
    }
    ```
  


  

#### Multiple servers

    For a monorepo with a frontend and an API server, define multiple configurations. The frontend uses `autoPort: true` so it picks a free port if 3000 is taken, while the API server requires port 8080 exactly:

    ```json theme={null}
    {
      "version": "0.0.1",
      "configurations": [
        {
          "name": "frontend",
          "runtimeExecutable": "npm",
          "runtimeArgs": ["run", "dev"],
          "cwd": "apps/web",
          "port": 3000,
          "autoPort": true
        },
        {
          "name": "api",
          "runtimeExecutable": "npm",
          "runtimeArgs": ["run", "start"],
          "cwd": "server",
          "port": 8080,
          "env": { "NODE_ENV": "development" },
          "autoPort": false
        }
      ]
    }
    ```
  


  

#### Node.js script

    To run a Node.js script directly instead of using a package manager command, use the `program` field:

    ```json theme={null}
    {
      "version": "0.0.1",
      "configurations": [
        {
          "name": "server",
          "program": "server.js",
          "args": ["--verbose"],
          "port": 4000
        }
      ]
    }
    ```
  


## Environment configuration

The environment you pick when [starting a session](<#start-a-session>) determines where Claude executes and how you connect:

* **Local**: runs on your machine with direct access to your files
* **Cloud**: runs on Anthropic-managed infrastructure by default. Sessions continue even if you close the app.
* **SSH**: runs on a remote machine you connect to over SSH, such as your own servers, cloud VMs, or dev containers
* **WSL** (Windows): runs inside a [WSL 2 distribution](<https://code.claude.com/docs/en/desktop-wsl>) on your machine, using its Linux toolchain and native paths

### Local sessions

The desktop app does not always inherit your full shell environment. On macOS, when you launch the app from the Dock or Finder, it reads your shell profile, such as `~/.zshrc` or `~/.bashrc`, to extract `PATH` and a fixed set of Claude Code variables, but other variables you export there are not picked up. On Windows, the app inherits user and system environment variables but does not read PowerShell profiles.

To set environment variables for local sessions and dev servers on any platform, open the environment dropdown in the prompt box, hover over **Local**, and click the gear icon to open the local environment editor. Variables you save here are stored encrypted on your machine and apply to every local session and preview server you start. You can also add variables to the `env` key in your `~/.claude/settings.json` file, though these reach Claude sessions only and not dev servers. See [environment variables](<https://code.claude.com/docs/en/env-vars>) for the full list of supported variables.

[Extended thinking](<https://code.claude.com/docs/en/model-config#extended-thinking>) is enabled by default, which improves performance on complex reasoning tasks but uses additional tokens. On the Anthropic API, set `MAX_THINKING_TOKENS` to `0` in the local environment editor to turn thinking off; this has no effect on Opus 5.5, Sonnet 5.5, Haiku 5.5, or the Fable models, which always use extended thinking. With thinking turned off on the Anthropic API, Claude Code sends effort `high` instead of a higher level to models it knows [don't accept that combination](<https://code.claude.com/docs/en/errors#effort-isnt-available-with-thinking-turned-off>), such as Opus 5.

On models with [adaptive reasoning](<https://code.claude.com/docs/en/model-config#adjust-effort-level>), Claude Code ignores the number itself in a positive `MAX_THINKING_TOKENS` value because adaptive reasoning controls thinking depth instead. On Opus 4.6 and Sonnet 4.6, set `CLAUDE_CODE_DISABLE_ADAPTIVE_THINKING` to `1` to use a fixed thinking budget; Fable models, Sonnet 5 and later, Haiku 5.5, and Opus 4.7 and later always use adaptive reasoning and have no fixed-budget mode.

#### Local sessions on managed devices

Your administrator can turn off local sessions with the [`disableDesktopLocalSessions` managed setting](<#managed-settings>). When they have, **Local** stays in the environment dropdown but is grayed out and can't be selected, with a tooltip saying your organization turned it off, and on Windows the [WSL](<https://code.claude.com/docs/en/desktop-wsl>) entry, whose availability on managed devices is [governed separately](<https://code.claude.com/docs/en/admin-setup#wsl-sessions-in-claude-code-desktop>), is grayed out the same way. New sessions default to the first SSH connection if one is configured, and Desktop shows a message that local sessions aren't available on this device if you try to continue an existing one. Choose an [SSH](<#ssh-sessions>) or [cloud](<#cloud-sessions>) environment instead, or contact your IT team.

### Cloud sessions

Cloud sessions continue in the background even if you close the app. Usage counts toward your [subscription plan limits](<https://code.claude.com/docs/en/costs>) with no separate compute charges.

You can create custom cloud environments with different network access levels and environment variables. To manage them, open the environment dropdown in the prompt box and select **Cloud**:

* **Add an environment**: select **Add cloud environment**
* **Edit or archive one of your own environments**: hover over it and click the gear icon

See [Configure cloud environments](<https://code.claude.com/docs/en/cloud-environments>) for details on configuring network access and environment variables.

### SSH sessions

SSH sessions let you run Claude Code on a remote machine while using the desktop app as your interface. This is useful for working with codebases that live on cloud VMs, dev containers, or servers with specific hardware or dependencies.

To add an SSH connection, open the environment dropdown in the prompt box before you start a session, then select **SSH > Add SSH connection…** and fill in the connection details:

* **Name**: a friendly label for this connection
* **SSH host**: `user@hostname` or a host defined in `~/.ssh/config`
* **SSH port**: defaults to 22 if left empty, or uses the port from your SSH config
* **SSH key (optional)**: path to your private key, such as `~/.ssh/id_ed25519`. Leave empty to use your SSH config or SSH agent.

Once added, the connection appears under **SSH** in the environment dropdown. Select it to start a session on that machine. Claude runs on the remote machine with access to its files and tools.

The remote machine must run Linux or macOS. Desktop installs Claude Code on the remote machine automatically the first time you connect. Once connected, SSH sessions support permission modes, connectors, plugins, and MCP servers.

#### Open an SSH session from a link

A `claude://code/new` link opens Desktop's new-session page, and it can name an SSH connection. Put such a link in a runbook, dashboard, or wiki page to open Desktop set up for the right machine and folder. For platforms that strip such links, see [The link renders as plain text instead of being clickable](<https://code.claude.com/docs/en/deep-links#the-link-renders-as-plain-text-instead-of-being-clickable>).

SSH links require Claude Desktop v2.110.0 or later.

The following link names the user `dev` on `build.example.com`, port 2222, and the folder `/srv/payments`, and fills in a prompt:

```text theme={null}
claude://code/new?ssh_host=dev%40build.example.com&ssh_port=2222&ssh_folder=/srv/payments&q=Investigate%20the%20failed%20deploy
```

An SSH link takes these parameters, and only `ssh_host` is required:

| Parameter | Value |
| :- | :- |
| `ssh_host` | `host` or `user@host`, written as in the **SSH host** field. The value can't start with `-`, and the host part takes only letters, digits, `.`, `_`, `:`, and `-` |
| `ssh_port` | A port number from 1 to 65535 |
| `ssh_folder` | A folder on the remote machine. Start it with `/` or `~/`, or use `~` |
| `q` | URL-encoded text for the prompt box |

An alias from `~/.ssh/config` works as `ssh_host` only for people who have that entry. To match a connection people already have, give the link the same user, host, and port as that connection.

When you open the link, Desktop asks you to confirm before it selects the connection:

* **A connection you already have**: if the host, user, and port match one of your connections, Desktop asks whether to use it, and shows you the connection's name and host, and the folder if the link names one.
* **A new connection**: otherwise Desktop opens the dialog for adding an SSH connection. When you add the connection, Desktop asks whether to connect before it saves anything, and shows you the host from the link, and the port and folder if the link names them.

Until you confirm, Desktop doesn't save the host, port, or folder from the link, and doesn't select or open a connection with them. If an SSH connection is already selected, the new-session page can still connect to it on its own, as it does without a link, even when the link names the same host. A link can't carry a key file, a password, or a command.

Anyone can write a link, so check what it filled in:

* **Before you confirm**: check the host and folder.
* **Before you send**: check the prompt and the selected environment.

Desktop fills in the prompt when the link opens, in place of any text you haven't sent, and never sends it for you. It treats the prompt as plain text, so a leading `/` or `!` and an `@` file mention don't act as a command or a mention. If you cancel, the prompt stays in the box and the environment you had selected doesn't change.

A link doesn't bypass [`sshHostAllowlist`](<#restrict-which-ssh-hosts-users-can-connect-to>). Desktop checks the allowlist when it connects.

If the link opens Desktop without a dialog about the connection, look for one of these causes:

* **You're signed out**: sign in, then open the link again.
* **Another dialog is open**: close it, then open the link again.
* **The link isn't valid**: Desktop shows a message that says what to fix, and doesn't fill in the prompt.
* **Desktop is older than v2.110.0**: earlier versions ignore the SSH parameters and open the new-session page with only the prompt.
* **SSH sessions are turned off**: if your administrator sets the allowlist to an empty array, Desktop refuses SSH links.

#### Pre-configure SSH connections for your team

Administrators can distribute SSH connections to team members by setting `sshConfigs` in [managed settings](<https://code.claude.com/docs/en/managed-settings>). Connections defined this way appear in each user's environment dropdown automatically and are shown as managed, so users can select them but can't edit or delete them in the app.

The following example pre-configures a single connection:

```json theme={null}
{
  "sshConfigs": [
    {
      "id": "shared-dev-vm",
      "name": "Shared Dev VM",
      "sshHost": "user@dev.example.com",
      "sshPort": 22,
      "sshIdentityFile": "~/.ssh/id_ed25519"
    }
  ]
}
```

Each entry requires `id`, `name`, and `sshHost`. The `sshPort` and `sshIdentityFile` fields are optional. Users can also add `sshConfigs` to their own `~/.claude/settings.json`.

#### Restrict which SSH hosts users can connect to

Administrators can limit Desktop's SSH sessions to an approved set of hosts by setting `sshHostAllowlist` in [managed settings](<https://code.claude.com/docs/en/managed-settings>). When set, users can only connect to hosts whose resolved hostname matches one of the patterns. Set it to an empty array to disable SSH sessions. The [`sshHostAllowlist` reference entry](<https://code.claude.com/docs/en/settings-reference#sshhostallowlist>) says how an empty array combines with lists in other managed sources.

The following example allows connections to any host under `devboxes.example.com` and to a single named bastion host:

```json theme={null}
{
  "sshHostAllowlist": ["*.devboxes.example.com", "bastion.example.com"]
}
```



**注意：**

  If your organization delivers [server-managed settings](<https://code.claude.com/docs/en/server-managed-settings>), set `sshHostAllowlist` there. By default, Desktop reads the key only from the [highest-ranked managed source that delivers a policy key](<https://code.claude.com/docs/en/managed-settings#how-claude-code-combines-managed-sources>). If that source leaves the key unset, Desktop ignores a list in a lower-ranked MDM policy or managed settings file and treats the key as [unset](<https://code.claude.com/docs/en/settings-reference#sshhostallowlist>). Desktop shows no warning.

  Also keep the same list on each user's machine, in the highest-ranked MDM policy or managed settings file there. Desktop fetches server-managed settings at launch and keeps no cached copy, so until a fetch succeeds the machine's list is the one that applies.


Patterns are case-insensitive. `*` matches any host, and `*.example.com` matches `example.com` and any subdomain. Anything else is an exact match. The check runs against the hostname after `~/.ssh/config` resolution via `ssh -G`, so `Host` aliases and `ProxyCommand`/`ProxyJump` entries are permitted as long as the resolved `HostName` matches.

`sshHostAllowlist` is read from managed settings only; values in user or project settings are ignored. Only the Claude Desktop app honors this setting; the Claude Code CLI and IDE extensions do not read it, and it does not restrict `ssh` commands run through the Bash tool. It governs which hosts the Desktop app connects to, not network egress, so pair it with your organization's network or zero-trust controls if you need a hard boundary.

## Enterprise configuration

Organizations on Team or Enterprise plans can manage desktop app behavior through admin console controls, managed settings files, and device management policies.

### Admin console controls

These settings are configured through the [admin settings console](<https://claude.ai/admin-settings/claude-code>):

* **Desktop**: control whether users in your organization can access Claude Code in the desktop app
* **Cloud sessions**: enable or disable [cloud sessions](<https://code.claude.com/docs/en/claude-code-on-the-web>) for your organization
* **Remote Control**: enable or disable [Remote Control](<https://code.claude.com/docs/en/remote-control>) for your organization

In Enterprise organizations that have HIPAA enabled, the **Desktop** toggle is off by default and an [Owner](<https://code.claude.com/docs/en/server-managed-settings#access-control>) can turn it on. Applying the [HIPAA configuration](<https://code.claude.com/docs/en/hipaa-setup>) turns it off, even if it was on, so an Owner has to turn it on afterward. **Cloud sessions** and **Remote Control** are also off by default, and an Owner can't turn them on once the organization has the HIPAA configuration applied.



**注释：**

  The OpenTelemetry form for Cowork under **Monitoring** in the admin console's [Data and privacy settings](<https://claude.ai/admin-settings/data-privacy-controls>) applies to Cowork sessions only. In a Cowork session on this machine, the desktop app passes that collector to Claude Code as `OTEL_*` environment variables, so the form takes effect even though Claude Code in that session [never fetches admin-console settings](<#managed-settings>).

  To export telemetry from Code tab sessions, set `CLAUDE_CODE_ENABLE_TELEMETRY` and the `OTEL_*` variables in the `env` block of your Claude Code managed settings, as shown in [administrator configuration for monitoring](<https://code.claude.com/docs/en/monitoring-usage#administrator-configuration>). Local, cloud, and SSH sessions each read [managed settings from different sources](<#managed-settings>). For the hosts a cloud session can reach, see [network access](<https://code.claude.com/docs/en/cloud-environments#network-access>). For the `service.name` that Code tab sessions report, see [service information](<https://code.claude.com/docs/en/monitoring-usage#service-information>).


### Managed settings

Managed settings override project and user settings and apply to Claude Code sessions in Desktop. You can set these keys in your organization's [managed settings](<https://code.claude.com/docs/en/managed-settings>) file or push them remotely through the admin console.

| Key | Description |
| - | - |
| `permissions.disableBypassPermissionsMode` | set to `"disable"` to prevent users from enabling Bypass permissions mode. |
| `disableAutoMode` | set to `"disable"` to remove [Auto](<https://code.claude.com/docs/en/permission-modes#eliminate-prompts-with-auto-mode>) mode from the mode selector. Also accepted under `permissions`. |
| `autoMode` | customize what the auto mode classifier trusts and blocks across your organization. See [Configure auto mode](<https://code.claude.com/docs/en/auto-mode-config>). |
| `browserExternalPageTools` | set to `"disabled"` to prevent Claude from using tools to read or act on external pages in the [Browser pane](<#browse-external-sites>). Users can still navigate to external sites themselves, and local dev server previews are unaffected. |
| `disableMobileSimulatorTools` | set to `true` to block Claude's tools for controlling and capturing devices in the [iOS Simulator pane](<https://code.claude.com/docs/en/desktop-ios-simulator#turn-off-simulator-access>). The pane stays usable for the user's own taps; only Claude's access is removed. The value must be the JSON boolean `true`; the string `"true"` is ignored. |
| `disableBrowserExternalNavigation` | set to `true` to turn off external browsing in the [Browser pane](<#browse-external-sites>) entirely. Neither users nor Claude can navigate to external sites, and localhost dev server previews are unaffected. The value must be the JSON boolean `true`; the string `"true"` is ignored. |
| `sshConfigs` | pre-configure [SSH connections](<#pre-configure-ssh-connections-for-your-team>) that appear in the environment dropdown. Users cannot edit or delete managed connections. |
| `sshHostAllowlist` | restrict [SSH sessions](<#restrict-which-ssh-hosts-users-can-connect-to>) to hosts whose resolved hostname matches one of these patterns. Read from managed settings only. |
| `disableDesktopLocalSessions` | set to `true` to turn off [Code sessions that run on the device](<#local-sessions-on-managed-devices>), leaving SSH sessions to other hosts and cloud sessions available. The value must be the JSON boolean `true`. Read from managed settings only. Requires Claude Desktop v1.37937.0 or later. |
| `disableSshSavedPasswords` | set to `true` to stop Desktop from offering to remember SSH passwords and from using or showing the ones it saved earlier. Turning it on doesn't delete them. Read from managed settings only. Requires Claude Desktop v1.49585.0 or later. |
| `managedMcpServers` | push MCP server configurations to all users. Available in third-party (3P) Desktop deployments only. In each entry, set a transport of `"http"`, `"sse"`, or `"stdio"`, connection details, and optionally a `toolPolicy` map to restrict which of that server's tools users can invoke. Deliver it through the managed settings file, MDM, or a Claude apps gateway policy's [`desktop` block](<https://code.claude.com/docs/en/claude-apps-gateway-config#claude-desktop-overlay>), since 3P deployments don't receive admin-console settings. To deliver it through the gateway, you need Claude Code v2.1.232 or later on the gateway server. This is the desktop app's own key; Claude Code reads a [same-named managed setting](<https://code.claude.com/docs/en/managed-mcp#provide-servers-through-managed-settings>) of its own, with a different entry shape. |

Which managed settings reach a Desktop session depends on where that session runs. Model restrictions such as [`availableModels`](<https://code.claude.com/docs/en/model-config#restrict-model-selection>) are enforced in Desktop's Claude Code sessions the same way as in the terminal CLI; see [surface coverage](<https://code.claude.com/docs/en/model-config#surface-coverage>).

* **Local sessions on this machine**: a managed settings file deployed to disk applies. Managed settings pushed remotely through the admin console also reach these sessions on Anthropic's API when the session authenticates with an [eligible login](<https://code.claude.com/docs/en/server-managed-settings#platform-availability>), following the same [settings precedence](<https://code.claude.com/docs/en/settings#settings-precedence>) as the terminal CLI.
* **[Cloud sessions](<#cloud-sessions>)**: receive [server-managed settings](<https://code.claude.com/docs/en/server-managed-settings>); device-deployed files don't reach them, because they run on Anthropic-managed VMs. Sessions routed to a [self-hosted environment](<https://code.claude.com/docs/en/self-hosted-environments>) also read the managed settings file in the runner image. [How Claude Code combines managed sources](<https://code.claude.com/docs/en/managed-settings#how-claude-code-combines-managed-sources>) says when that file applies.
* **[SSH sessions](<#ssh-sessions>)**: the session reads the managed settings file from the remote host. Desktop itself reads `sshConfigs`, `sshHostAllowlist`, `disableSshSavedPasswords`, and `disableDesktopLocalSessions` on the local machine. If you deliver more than one managed source, it reads them from [one by default](<https://code.claude.com/docs/en/managed-settings#how-claude-code-combines-managed-sources>).
* **[Cowork](<https://claude.com/docs/cowork/overview>) sessions**: in a Cowork session on this machine, Claude Code never fetches admin-console settings, even when the user signs in with a Team or Enterprise account, and reads policy deployed to the machine unless your Claude Desktop configuration sets `requireCoworkFullVmSandbox`. Remote Cowork sessions receive neither. See [where and when a policy applies](<https://code.claude.com/docs/en/managed-settings#where-and-when-a-policy-applies>) for which device files reach Cowork, and [MCP permission rules](<https://code.claude.com/docs/en/permissions#mcp>) for how `Bash` and `WebFetch` rules apply to Cowork's tools.

In local and SSH sessions, the desktop app delivers each user's connected claude.ai connectors to Claude Code directly. No MCP setting or `managed-mcp.json` reaches those connectors, whichever settings source or file location you use. To block a connector's tools in these sessions, use your organization's [connector tool controls](<https://code.claude.com/docs/en/mcp#organization-controls-on-connector-tools>). [How connectors reach Claude Code](<https://code.claude.com/docs/en/mcp#how-connectors-reach-claude-code>) shows which settings govern connectors in each kind of session.

`permissions.disableBypassPermissionsMode` and `disableAutoMode` also work in user and project settings, but placing them in managed settings prevents users from overriding them.

For the permission, plugin, and delivery keys only a managed source can set, see [Keys only managed settings can set](<https://code.claude.com/docs/en/managed-settings#managed-only-settings>).

### Device management policies

IT teams can manage the desktop app through MDM on macOS, group policy on Windows, or a policy file on Linux. Available policies include enabling or disabling the Claude Code feature, controlling auto-updates on macOS and Windows, and setting a custom deployment URL.

* **macOS**: configure via `com.anthropic.claudefordesktop` preference domain using tools like Jamf or Kandji
* **Windows**: configure via registry at `SOFTWARE\Policies\Claude`
* **Linux**: configure via a root-owned file at `/etc/claude-desktop/managed-settings.json`, which holds the policy keys as a JSON object. Desktop refuses the file if anyone but root can write to it or to its folder. It's a different file from Claude Code's [managed settings file](<https://code.claude.com/docs/en/managed-settings>).

### Network access requirements

Desktop loads its application code and user content from Anthropic CDN hosts.

```text theme={null}
anthropic.com
*.anthropic.com
claude.ai
*.claude.ai
claude.com
*.claude.com
claude.app
*.claude.app
*.claudeusercontent.com
*.claudemcpcontent.com
```

Traffic is HTTPS on port 443 unless you configure a custom port for [OTLP](<https://code.claude.com/docs/en/monitoring-usage>), an LLM gateway, or an MCP server.

For proxy servers, custom certificate authorities, mTLS, and the domains the standalone CLI needs, see [network configuration](<https://code.claude.com/docs/en/network-config>).

To reduce the number of firewall wildcards, allow these Anthropic hosts instead. Certain subdomains are dynamically generated and must remain wildcards.

```text theme={null}
anthropic.com
api.anthropic.com
a-api.anthropic.com
a-cdn.anthropic.com
s-cdn.anthropic.com
assets-proxy.anthropic.com
claude.ai
a.claude.ai
assets.claude.ai
downloads.claude.ai
*.livepreview.claude.ai
claude.com
platform.claude.com
*.livepreview.claude.app
*.claudeusercontent.com
*.claudemcpcontent.com
```

If your organization has [IP allowlisting](<https://support.claude.com/en/articles/13200993-restrict-access-to-claude-with-ip-allowlisting>) enabled for Claude, route `bridge.claudeusercontent.com` through the same proxy egress as `claude.ai` and `api.anthropic.com`. If you can't route it that way, add the egress address your proxy uses for that host to your organization's IP allowlist, but only when that address is dedicated to your organization: a shared proxy egress range also admits the proxy vendor's other customers.

Anthropic checks connections to that host against your organization's IP allowlist using the address they arrive from. If your proxy sends traffic for it out through an address that isn't on that allowlist, Claude in Chrome and other features that connect through the bridge stop working while the rest of the app keeps working.

An [artifact](<https://code.claude.com/docs/en/artifacts>) that loads a typeface from [Google Fonts](<https://code.claude.com/docs/en/artifacts#improve-the-visual-design>) also requests `fonts.googleapis.com` and `fonts.gstatic.com`. Both hosts are optional. If you block them, artifacts render in fallback typefaces. Block with a fast rejection rather than a silent drop so the font request fails immediately instead of delaying the page's first render.

Artifacts can also load JavaScript libraries, such as React or a charting package, from `cdnjs.cloudflare.com`, `cdn.jsdelivr.net`, `cdn.tailwindcss.com`, `code.jquery.com`, and `unpkg.com`, and from no other external host. If you block those hosts, the parts of an artifact that depend on a library don't work, and unlike a blocked font, a blocked library has no fallback. Block with a fast rejection here too, so a blocked library request fails at once rather than hanging until it times out.

### Authentication and SSO

Team and Enterprise organizations can require SSO for all users. See [authentication](<https://code.claude.com/docs/en/authentication>) for plan-level details and [Setting up SSO](<https://support.claude.com/en/articles/13132885-setting-up-single-sign-on-sso>) for SAML configuration; OIDC setup is covered in the [Claude Enterprise Administrator Guide](<https://claude.com/resources/tutorials/claude-enterprise-administrator-guide>).

### Data handling

Claude Code processes your code locally in local sessions, or in cloud sessions on Anthropic-managed infrastructure, unless your organization routes them to a [self-hosted environment](<https://code.claude.com/docs/en/self-hosted-environments>). Cloud sessions, including in a self-hosted environment, send conversations and code context to Anthropic's API for processing; local and SSH sessions send them to whichever [model provider](<#feature-comparison>) your deployment configures, Anthropic's API by default. See [data handling](<https://code.claude.com/docs/en/data-usage>) for details on data retention, privacy, and compliance.

### Deployment

Desktop can be distributed through enterprise deployment tools:

* **macOS**: distribute via MDM such as Jamf or Kandji using the `.dmg` installer
* **Windows**: deploy via the MSIX package. See [Deploy Claude Desktop for Windows](<https://support.claude.com/en/articles/12622703-deploy-claude-desktop-for-windows>) for enterprise deployment options including silent installation

For the domains to allowlist in your firewall, see [network access requirements](<#network-access-requirements>) above. For proxy settings, custom certificate authorities, and LLM gateways, see [network configuration](<https://code.claude.com/docs/en/network-config>).

For the full enterprise configuration reference, see the [enterprise configuration guide](<https://support.claude.com/en/articles/12622667-enterprise-configuration>).

## Coming from the CLI?

If you already use the Claude Code CLI, Desktop runs the same underlying engine with a graphical interface. You can run both simultaneously on the same machine, even on the same project. Each keeps its own session list, and you can bring a CLI session into Desktop. They share configuration and project memory via CLAUDE.md files.

To move a CLI session into Desktop, run `/desktop` in the terminal. Claude saves your session and opens it in the desktop app, then exits the CLI. This command is available on macOS and x64 Windows when you are signed in with a Claude subscription. It is not available with API key authentication or on Amazon Bedrock, Google Cloud's Agent Platform, or Microsoft Foundry.

From your shell, [`claude --desktop`](<https://code.claude.com/docs/en/cli-reference#cli-flags>) opens Desktop directly without starting a terminal session. It requires Claude Code v2.1.285 or later and has the same platform and sign-in requirements as `/desktop`. With no other arguments, it opens Desktop on the current directory. To open an existing CLI session in Desktop, add `--continue` for the most recent conversation in this directory, or `--resume` with the session ID that `/status` shows:

```bash theme={null}
claude --desktop --resume <session-id>
```

Claude Code prints `Opening session <session-id> in Claude Desktop`, the session opens in the app, and the command exits. A session name doesn't work in place of the ID. Claude Code doesn't move a session that is open in another terminal or still running in the background. If Claude Desktop isn't installed, the command prints a download link and exits.

You can also pick up a CLI session from inside Desktop with `/resume`. The command is available in local sessions, not in SSH, WSL, or cloud sessions.

To continue a terminal session in Desktop:

1. Close the session in the terminal.
2. In the Desktop prompt box, type `/resume`. Desktop lists the sessions you started from the CLI on this computer. Search by title, folder, or branch, and preview where each one left off.
3. Select the session. It continues in the app with its full conversation and context.

Desktop continues the same session rather than a copy, so `claude --resume` in the terminal still finds it afterwards.



**提示：**

  When to use Desktop vs CLI: use Desktop when you want to manage parallel sessions in one window, arrange panes side by side, or review changes visually. Use the CLI when you need scripting, automation, or prefer a terminal workflow.


### CLI flag equivalents

This table shows the desktop app equivalent for common CLI flags. Flags not listed have no desktop equivalent because they are designed for scripting or automation.

| CLI | Desktop equivalent |
| - | - |
| `--model sonnet` | Model dropdown next to the send button |
| `--resume`, `--continue` | Click a session in the sidebar, or type `/resume` in the prompt box to pick up a session you started from the CLI |
| `--permission-mode` | Mode selector next to the send button |
| `--dangerously-skip-permissions` | Bypass permissions mode. On Pro and Max plans, enable it in Settings → Claude Code → "Allow bypass permissions mode"; on Team and Enterprise plans, organization policy controls it |
| `--add-dir` | Add multiple repos with the **+** button in cloud sessions |
| `--allowedTools`, `--disallowedTools` | No per-session equivalent. Permission rules in [settings files](<https://code.claude.com/docs/en/settings>) still apply. |
| `--verbose` | [Verbose view mode](<#switch-view-modes>) |
| `--print`, `--output-format` | Not available. Desktop is interactive only. |
| `ANTHROPIC_MODEL` env var | Model dropdown next to the send button |
| `MAX_THINKING_TOKENS` env var | Set in the local environment editor. See [environment configuration](<#environment-configuration>). |

### Shared configuration

Desktop and CLI read the same configuration files, so your setup carries over:

* **[CLAUDE.md](<https://code.claude.com/docs/en/memory>)** and `CLAUDE.local.md` files in your project are used by both
* **[MCP servers](<https://code.claude.com/docs/en/mcp>)** configured in `~/.claude.json` or `.mcp.json` work in both
* **[Hooks](<https://code.claude.com/docs/en/hooks>)** and **[skills](<https://code.claude.com/docs/en/skills>)** defined in settings apply to both
* **[Settings](<https://code.claude.com/docs/en/settings>)** in `~/.claude.json` and `~/.claude/settings.json` are shared. Permission rules, allowed tools, and other settings in `settings.json` apply to Desktop sessions.
* **Models**: the same [models](<https://code.claude.com/docs/en/model-config#available-models>) are available in both. In Desktop, select the model from the dropdown next to the send button. You can change the model mid-session from the same dropdown.

#### MCP servers from the Claude Desktop chat app

The Desktop app loads MCP servers from `claude_desktop_config.json` into local Code tab sessions, alongside servers from `~/.claude.json` and `.mcp.json`. A server you define in `claude_desktop_config.json` is available in both the Desktop chat surface and local Code tab sessions.

If you define the same server name in `claude_desktop_config.json` and in `~/.claude.json` or `.mcp.json`, the Code tab in local sessions connects once and uses the `claude_desktop_config.json` definition.

The app also re-delivers stdio servers from `~/.claude.json` to the embedded CLI in local sessions. When the top level of `~/.claude.json` (user scope) and `.mcp.json` define the same stdio server name, the Code tab uses the `~/.claude.json` definition, departing from the CLI [scope hierarchy](<https://code.claude.com/docs/en/mcp#scope-hierarchy-and-precedence>).



**注释：**

  The standalone CLI does not read `claude_desktop_config.json`. On macOS and WSL, run `claude mcp add-from-claude-desktop` to copy those servers into `~/.claude.json`. See [Import MCP servers from Claude Desktop](<https://code.claude.com/docs/en/mcp#import-mcp-servers-from-claude-desktop>) for the import flow and scope options.


### Feature comparison

This table compares core capabilities between the CLI and Desktop. For a full list of CLI flags, see the [CLI reference](<https://code.claude.com/docs/en/cli-reference>).

| Feature | CLI | Desktop |
| - | - | - |
| Permission modes | All modes including `dontAsk` | Manual, Accept edits, Plan, and Auto. Bypass permissions appears in the mode selector once enabled: through the Settings toggle on Pro and Max plans, or through organization policy on Team and Enterprise plans |
| [Third-party providers](<https://code.claude.com/docs/en/third-party-integrations>) | Amazon Bedrock, Google Cloud's Agent Platform, Microsoft Foundry | Anthropic's API by default. For gateway routing, see [connect the desktop app to a gateway](<https://code.claude.com/docs/en/llm-gateway-connect#desktop-app>). To run the Code tab on Amazon Bedrock, Google Cloud's Agent Platform, Microsoft Foundry, or a self-hosted LLM gateway, see [Claude Desktop on 3P](<https://claude.com/docs/third-party/claude-desktop/overview>). |
| [MCP servers](<https://code.claude.com/docs/en/mcp>) | Configure in settings files | Connectors UI for local and SSH sessions, or settings files |
| [Plugins](<https://code.claude.com/docs/en/plugins/overview>) | `/plugin` command | Plugin manager UI |
| @mention files | Text-based | With autocomplete; local and SSH sessions only |
| File attachments | Not available | Images, PDFs |
| Session isolation | [`--worktree`](<https://code.claude.com/docs/en/cli-reference>) flag | **worktree** option when starting a session |
| Multiple sessions | Separate terminals | Sidebar tabs |
| Recurring tasks | Cron jobs, CI pipelines | [Scheduled tasks](<https://code.claude.com/docs/en/desktop-scheduled-tasks>) |
| Computer use | [Enable via `/mcp`](<https://code.claude.com/docs/en/computer-use>) on macOS | [App and screen control](<#let-claude-use-your-computer>) on macOS and Windows |
| iOS simulator | Drive the simulator via [computer use](<https://code.claude.com/docs/en/computer-use#test-a-simulator-flow>) | [iOS Simulator pane](<https://code.claude.com/docs/en/desktop-ios-simulator>) opens automatically |
| Dispatch integration | Not available | [Dispatch sessions](<#sessions-from-dispatch>) in the sidebar |
| Scripting and automation | [`--print`](<https://code.claude.com/docs/en/cli-reference>), [Agent SDK](<https://code.claude.com/docs/en/headless>) | Not available |

### What's not available in Desktop

The following features aren't available in Desktop, except where noted:

* **Third-party providers**: Desktop connects to Anthropic's API by default. To route Desktop through a gateway, or to run the Code tab on Amazon Bedrock, Google Cloud's Agent Platform, Microsoft Foundry, or a self-hosted LLM gateway, follow the links in the [Third-party providers row](<#feature-comparison>).
* **Linux (beta)**: Computer Use isn't yet available in the Linux desktop app. See [Claude Desktop on Linux](<https://code.claude.com/docs/en/desktop-linux>).
* **Inline code suggestions**: Desktop doesn't offer autocomplete-style code completions. It works through conversational prompts and explicit code changes, and can [suggest your next prompt](<#accept-a-suggested-prompt>) after Claude replies.
* **Agent teams**: coordinated teams, where Claude as the team lead assigns tasks to teammates from a shared task list, are available in the [CLI](<https://code.claude.com/docs/en/agent-teams>), not in Desktop. For multi-agent work inside one session, use [dynamic workflows](<https://code.claude.com/docs/en/workflows>), which run in Desktop; Claude can also [message and manage your other sessions](<#work-across-sessions>) directly.
* **Terminal-dialog commands**: built-in commands that open an interactive panel in the terminal behave differently in the Code tab. Edit [settings files](<https://code.claude.com/docs/en/settings>) directly to manage permission rules and configuration, or run the commands from the standalone CLI.
  * Commands with no argument form, such as `/permissions`, reply with `isn't available in this environment`.
  * `/config` opens Settings → Claude Code. Text after the command is ignored, so `/config theme=dark` doesn't set the theme.

## Troubleshooting

The sections below cover issues specific to the desktop app. For runtime API errors that appear in the chat such as `API Error: 500`, `529 Overloaded`, `429`, or `Prompt is too long`, see the [Error reference](<https://code.claude.com/docs/en/errors>). Those errors and their fixes are the same across the CLI, desktop, and web.

### Check your version

To see which version of the desktop app you're running:

* **macOS**: click **Claude** in the menu bar, then **About Claude**
* **Windows**: click **Help**, then **About Claude**

Click the version number to copy it to your clipboard.

#### Claude Code version in the Code tab

To see which version of Claude Code a session runs, type `/status` in a local session in the **Code** tab and read the **Claude Code** row, which shows a version such as `2.1.286`.

To get a newer version for local sessions, open **Claude → Check for Updates** on macOS or **Help → Check for Updates** on Windows, then start a new session.

In a local session, the **Code** tab runs its own copy of Claude Code, which has its own version number. The desktop app downloads and updates that copy, so it can differ from the `claude` command in your terminal, and updating one doesn't update the other.

### 403 or authentication errors in the Code tab

If you see `Error 403: Forbidden` or other authentication failures when using the Code tab:

1. Sign out and back in from the app menu. This is the most common fix.
2. Verify you have an active paid subscription: Pro, Max, Team, or Enterprise.
3. If the CLI works but Desktop does not, quit the desktop app completely, not just close the window, then reopen and sign in again.
4. Check your internet connection and proxy settings.

### Blank or stuck screen on launch

If the app opens but shows a blank or unresponsive screen:

1. Restart the app.
2. Check for pending updates. On macOS and Windows the app auto-updates on launch; on Linux, update through apt as described in [Claude Desktop on Linux](<https://code.claude.com/docs/en/desktop-linux>).
3. On a managed network, confirm your firewall allows the CDN hosts in [network access requirements](<#network-access-requirements>).
4. On Windows, check Event Viewer for crash logs under **Windows Logs → Application**.

### "Failed to load session"

If you see `Failed to load session`, the selected folder may no longer exist, a Git repository may require Git LFS that isn't installed, or file permissions may prevent access. Try selecting a different folder or restarting the app.

### Session not finding installed tools

If Claude can't find tools like `npm`, `node`, or other CLI commands, verify the tools work in your regular terminal, check that your shell profile properly sets up PATH, and restart the desktop app to reload environment variables.

### Git and Git LFS errors

Sessions that run in their own worktree need Git. If you see "Git is required," install [Git](<https://git-scm.com/downloads>), or [Git for Windows](<https://git-scm.com/downloads/win>) on Windows, and try again. On Windows, Claude Desktop versions before 1.49585.0 asked for Git before starting any local session; if you see that prompt and aren't using worktrees, update the app.

If you see "Git LFS is required by this repository but is not installed," install Git LFS from [git-lfs.com](<https://git-lfs.com/>), run `git lfs install`, and restart the app.

### MCP servers not working on Windows

If MCP server toggles don't respond or servers fail to connect on Windows, check that the server is properly configured in your settings, restart the app, verify the server process is running in Task Manager, and review server logs for connection errors.

### App won't quit

* **macOS**: press Cmd+Q. If the app doesn't respond, use Force Quit with Cmd+Option+Esc, select Claude, and click Force Quit.
* **Windows**: use Task Manager with Ctrl+Shift+Esc to end the Claude process.

### Windows-specific issues

* **PATH not updated after install**: open a new terminal window. PATH updates only apply to new terminal sessions.
* **Concurrent installation error**: if you see an error about another installation in progress but there isn't one, try running the installer as Administrator.

### "Branch doesn't exist yet" when opening in CLI

Cloud sessions can create branches that don't exist on your local machine. Click the branch name in the session and select **Copy branch name**, then fetch it locally:

```bash theme={null}
git fetch origin <branch-name>
git checkout <branch-name>
```

### Still stuck?

* Open Help → Get Support in the desktop app, or visit the [Claude support center](<https://support.claude.com/>) directly
* For problems that also reproduce in the standalone `claude` CLI, search or file a bug on [GitHub Issues](<https://github.com/anthropics/claude-code/issues>)

When reporting a problem, include your desktop app version, your operating system, the exact error message, and relevant logs. On macOS, check Console.app. On Windows, check Event Viewer → Windows Logs → Application. Review log excerpts before posting them to a public issue; they can include file paths and other details from your environment.


</details>
