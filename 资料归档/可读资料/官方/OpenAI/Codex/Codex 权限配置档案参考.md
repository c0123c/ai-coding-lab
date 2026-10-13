# Codex 权限配置档案参考

说明定义选择与继承配置档案、文件和网络权限、规则匹配及旧沙箱设置迁移；正文标记为测试能力。

- 来源：OpenAI／Codex
- 资料类型：存档文档
- 文档时间：官方未公开发表/更新时间（[已复查](<https://learn.chatgpt.com/docs/permissions>)）；本地抓取：2026-10-09
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://learn.chatgpt.com/docs/permissions.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Codex/Codex 权限配置档案参考.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 权限

> 完整的文档索引，请参见[llms.txt](<https://learn.chatgpt.com/llms.txt>)。通过将 `.md` 附加到页面 URL 可以获得文档页面的 Markdown 版本。

贝塔。权限配置文件正在积极开发中，可能会发生变化。

权限配置文件不与旧的沙箱设置组合。配置 
 `default_permissions` 和 `[permissions]`，或 `sandbox_mode` /
 `sandbox_workspace_write`，但不能同时配置两者。如果 `sandbox_mode` 出现在任何 
 加载的配置文件中，则传递 `--sandbox`，或选定的配置文件集 
 `sandbox_mode`、Codex 使用那些旧的沙箱设置而不是 
 `default_permissions`。

托管 `allowed_permission_profiles` 是例外：它使得 Codex 使用
权限配置文件。删除旧设置，例如
`sandbox_mode` 和 `[sandbox_workspace_write]` 在部署托管之前
配置文件白名单。对于混合版本企业部署，您可以保留
管理的 `allowed_sandbox_modes` 作为临时兼容性的要求
约束直到每个客户端都运行 Codex 0.138.0 或稍后。

权限配置文件允许您将最小权限边界应用于代表您运行的本地命令
Codex。配置文件是一个命名策略，它结合了文件系统 
 规则（定义哪些命令可以读取或写入）和网络规则（
 定义命令可以到达哪些目标）。

配置文件的 `network.enabled = true` 允许命令网络访问，但它 
 不会启动网络代理。要强制执行配置文件域规则，还需在 `config.toml` 中设置 
 `features.network_proxy = true`，或使用启用的 
 管理员管理的 `[experimental_network]` 要求。如果没有活动的 
 代理，配置文件域规则不会限制直接网络访问。

使用配置文件为 Codex 提供足够的当前聊天访问权限，而无需授予 
 对您的计算机或网络的广泛访问权限。例如，只读配置文件可以
让Codex检查项目而不对其进行编辑，而可写配置文件
可以限制对选定工作空间根目录的编辑。

支持本地权限配置文件 macOS、Linux、 WSL，和本机
窗户。参见 [范围和执行](<#范围和执行>) 对于特定于平台的
详细信息和注意事项。

有关当前 Codex 云网络设置，请参阅[连接到服务](<https://learn.chatgpt.com/docs/environments/cloud-environments#connect-to-services>)。对于Codex云（旧版），请参阅[互联网访问](<https://learn.chatgpt.com/docs/cloud/internet-access>)。

## 定义并选择配置文件

Codex 包括三个内置权限配置文件：

- `:read-only` 将本地命令执行保持为只读。
- `:workspace` 允许在活动工作空间根目录和系统临时目录内进行写入。
- `:danger-full-access` 删除本地沙箱限制，应使用
只有当这种广泛的访问是有意为之时。

在 `[permissions.<name>]` 下创建一个命名配置文件，然后将顶级 
`default_permissions` 键设置为该配置文件名称或上面的内置项之一。
在此示例中，`project-edit` 是用户定义的配置文件名称，而不是内置的 
 值。

企业管理员可以定义配置文件并限制用户可以通过托管 `requirements.toml` 选择哪些配置文件 
。一旦存在
`allowed_permission_profiles`，省略的配置文件将被拒绝，
包括省略的内置程序和在未来的Codex版本中添加的配置文件。有关推荐的托管配置，请参阅
[控制可用的权限配置文件](<https://learn.chatgpt.com/docs/enterprise/managed-configuration#control-available-permission-profiles>)
。

自定义配置文件使用两个相关的概念：

- `[permissions.<name>.workspace_roots]` 添加了应该的具体目录
计为该配置文件的工作区根。
- `[permissions.<name>.filesystem.":workspace_roots"]` 定义文件系统
规则 Codex 适用于每个有效工作空间根：当前 
 会话的运行时工作空间根加上上面配置文件定义的根。

配置文件也使用普通的配置层模型。较高优先级层可以
添加或替换相同配置文件名称下的条目，而无需重新声明整个
配置文件。

例如，组织级配置和用户级配置可以独立扩展
相同的配置文件：

```toml
# /etc/codex/config.toml
[permissions.server.workspace_roots]
"~/code/server" = true
```

```toml
# ~/.codex/config.toml
[permissions.server.workspace_roots]
"~/code/mobile-app" = true
```

当 `server` 处于活动状态时，两个工作区根都参与有效的 
 配置文件。

```toml
default_permissions = "project-edit"

[features]
network_proxy = true

[permissions.project-edit.workspace_roots]
"~/code/app" = true
"~/code/shared-lib" = true

[permissions.project-edit.filesystem]
":minimal" = "read"

[permissions.project-edit.filesystem.":workspace_roots"]
"." = "write"
".devcontainer" = "read"
"**/*.env" = "deny"

[permissions.project-edit.network]
enabled = true

[permissions.project-edit.network.domains]
"api.openai.com" = "allow"
"objects.githubusercontent.com" = "allow"
"*.github.com" = "allow"
"tracking.example.com" = "deny"
```

此简介：

- 读取常见开发人员工具所需的最小运行时路径。
- 将相同的工作区根规则应用于当前会话和
配置文件定义的根。
- 将 IDE- 相邻设置（例如 `.devcontainer/`）保留为只读
根。
- 拒绝使用 glob 规则匹配环境文件。
- 仅允许通过配置的域策略进行网络访问。

在活动配置文件内，即使较宽的 
 路径可读或可写，较窄的拒绝规则仍然有效。例如，配置文件可以使工作区根目录 
 可写，同时仍将匹配的 `.env` 路径设置为 `deny`。

## 扩展个人资料

当配置文件与内置配置文件或另一个命名的 
 配置文件基本相同时，请使用 `extends`。更喜欢扩展内置配置文件而不是从头开始，以便 
 基线保护得以延续。例如，扩展 `:workspace` 会使 
 工作区根目录的 `.codex` 目录保持只读状态，除非您明确 
 覆盖它。设置父级一次，然后仅添加或覆盖
不同的规则。

```toml
default_permissions = "project-edit"

[features]
network_proxy = true

[permissions.project-edit]
description = "Project editing with OpenAI API access."
extends = ":workspace"

[permissions.project-edit.filesystem.":workspace_roots"]
"**/*.env" = "deny"

[permissions.project-edit.network]
enabled = true

[permissions.project-edit.network.domains]
"api.openai.com" = "allow"
```

此配置文件以 `:workspace` 开头，保持匹配的 `.env` 文件被拒绝，并且 
 允许对 `api.openai.com` 的请求。配置文件可以扩展 `:read-only`、
`:workspace` 或其他命名配置文件。无法扩展
`:danger-full-access`； Codex 也拒绝未知的父母和继承
循环。

## 配置规格

|参赛作品|类型/值|默认|详情|
| ----------------------------------------------------------------- | -------------------------- | ----------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `default_permissions`                                             |字符串配置文件名称|无|默认情况下应用的权限配置文件名称为 Codex。它必须与 `[permissions]` 下的配置文件或内置配置文件（例如 `:workspace`）匹配。明确设置它以实现可预测的行为；仅当 `:workspace` 和 `:read-only` 均明确允许时，托管要求才可以忽略它。 Codex 使用较旧的沙箱设置，除非托管 `allowed_permission_profiles` 告诉它在此设置中使用权限配置文件。|
| `[permissions.<name>]`                                            |表|无|定义命名配置文件。 `default_permissions`默认选择一种配置文件；其他权限配置文件设置也使用配置文件名称。|
| `permissions.<name>.description`                                  |字符串|无|为配置文件提供人类可读的描述。配置文件不会通过 `extends` 继承其父级的描述。|
| `permissions.<name>.extends`                                      |字符串配置文件名称|无|从另一个命名配置文件或内置 `:read-only` 或 `:workspace` 配置文件启动此配置文件。 Codex 拒绝 `:danger-full-access`、未知的父母和继承周期。|
| `[permissions.<name>.workspace_roots]`                            |表|无|添加配置文件定义的工作区根，这些工作区根接收 `:workspace_roots` 文件系统规则以及当前会话的运行时工作区根。|
| `permissions.<name>.workspace_roots."<path>"`                     |布尔值| `false`                 |当 `true` 时，将路径添加到配置文件的工作区根集。设置为 `false` 的条目保持不活动状态。|
| `[permissions.<name>.filesystem]`                                 |表|无|将文件系统路径映射到访问值或作用域子路径映射。丢失或空的文件系统表会限制文件系统访问并发出启动警告。|
| `permissions.<name>.filesystem.glob_scan_max_depth`               |数量|无|当 Codex 快照在沙箱启动前匹配时，限制 Linux、WSL 和本机 Windows 上的拒绝读取 glob 扩展。较大的值会增加启动扫描工作。当无界 `**` 模式需要有界预扩展时，至少使用 `1` 的值。|
| `[permissions.<name>.filesystem]."<path>"`                        |`read`、`write` 或 `deny`|无|授予对受支持路径的直接访问权限。 `deny` 拒绝访问并赢得同样具体的 `write` 或 `read` 条目。 Codex 拒绝活动运行时无法强制执行的直接写入规则。|
| `[permissions.<name>.filesystem."<path>"]."<subpath>"`            |`read`、`write` 或 `deny`|无|授予 `<path>` 后代的访问权限。使用 `.` 作为基本路径。其他子路径必须是相对后代，并且不能包含 `.` 或 `..` 组件。|
| `[permissions.<name>.network]`                                    |表|无|配置命令网络访问和活动网络代理强制执行的策略。除非管理员管理的网络要求启动代理，否则启用 `features.network_proxy`。|
| `permissions.<name>.network.enabled`                              |布尔值| `false`                 |为配置文件中的命令启用网络访问。它不会启动网络代理；没有活动代理，命令可以直接连接，不受域限制。|
| `[permissions.<name>.network.domains]`                            |表|无|将主机模式映射到 `allow` 或 `deny`。仅当网络代理处于活动状态时，规则才适用。如果没有 `allow` 条目，则活动代理会阻止域请求，并且拒绝条目会覆盖允许条目。|
| `permissions.<name>.network.domains."<pattern>"`                  |`allow` 或 `deny`|无|支持精确主机，`*.example.com` 表示子域，`**.example.com` 表示顶点加子域，`*` 作为仅允许的全局通配符。主机模式通过修剪、转换为小写、去除尾随点以及去除端口号或括号来标准化。|
| `[permissions.<name>.network.unix_sockets]`                       |表|无|映射 Unix 套接字白名单覆盖。仅用于本地集成，例如 Docker。|
| `permissions.<name>.network.unix_sockets."<path>"`                |`allow` 或 `deny`|无|使用 `allow` 将绝对 Unix 套接字路径添加到有效白名单，或使用 `deny` 拒绝它。被拒绝的条目将从有效允许列表中删除。|
| `permissions.<name>.network.proxy_url`                            |URL 字符串| `http://127.0.0.1:3128` |HTTP代理侦听器，用于`HTTP_PROXY`、`HTTPS_PROXY`、WebSocket代理变量以及相关工具代理环境变量。|
| `permissions.<name>.network.enable_socks5`                        |布尔值| `true`                  |启用用于 `ALL_PROXY` 和 FTP 代理变量的 SOCKS5 侦听器。|
| `permissions.<name>.network.socks_url`                            |URL 字符串| `http://127.0.0.1:8081` |SOCKS5监听地址。|
| `permissions.<name>.network.enable_socks5_udp`                    |布尔值| `true`                  |当启用 SOCKS5 监听器时启用 SOCKS5 UDP 支持。|
| `permissions.<name>.network.allow_upstream_proxy`                 |布尔值| `true`                  |允许网络沙箱代理尊重出站请求的上游 `HTTP(S)_PROXY` 和 `ALL_PROXY` 设置。|
| `permissions.<name>.network.allow_local_binding`                  |布尔值| `false`                 |当 `true` 时禁用 local/private-network 防护。当 `false` 时，必须明确将精确的本地文字（例如 `localhost` 或 `127.0.0.1`）列入白名单，并且解析为本地或私有 IP 的主机名仍会被阻止。|
| `permissions.<name>.network.dangerously_allow_non_loopback_proxy` |布尔值| `false`                 |允许代理侦听器绑定非环回地址。不为当地的普通发展做好准备。|
| `permissions.<name>.network.dangerously_allow_all_unix_sockets`   |布尔值| `false`                 |绕过支持 Unix 套接字代理的 Unix 套接字白名单。这是一个宽阔的当地逃生舱口。|

## 文件系统权限

文件系统条目使用 `read`、`write` 或 `deny`：

|访问|含义|
| ------- | --------------------------------------------------------------------------------------------------------------------------------- |
| `read`  |允许命令读取路径下的文件和列出目录。命令无法在那里创建、修改、重命名或删除文件。|
| `write` |允许命令读取和修改路径下的文件，包括在OS允许的情况下创建、重命名和删除文件。|
| `deny`  |拒绝该路径下的读取和写入。使用它从更广泛的 `read` 或 `write` 授权中开辟出被拒绝的子路径。|

更具体的条目会覆盖更广泛的条目。当两个条目针对 
 相同路径时，`deny` 优先于 `write`，并且 `write` 优先于 
 优先于 `read`。

此优先级让配置文件首先描述一个广泛的工作区域，然后将 
out 文件或目录保留为不可读：

```toml
[permissions.project-edit.filesystem]
":minimal" = "read"

[permissions.project-edit.filesystem.":workspace_roots"]
"." = "write"
".devcontainer" = "read"
"**/*.env" = "deny"
```

在此示例中，工作区根目录保持可写，`.devcontainer/` 保持 
 可读但不变得可写，并且匹配的环境文件保持 
 对沙盒命令不可用。

更具体的路径还可以在更广泛的拒绝内重新打开更窄的子树：

```toml
[permissions.project-edit.filesystem]
"~/Documents" = "deny"
"~/Documents/codex" = "write"
```

支持的路径形式：

|路径|含义|作用域子路径|
| ------------------ | ------------------------------------------------------------------------------------------- | --------------- |
| `:root`            |文件系统根|仅`.`|
| `:minimal`         |常用工具所需的平台和运行时路径|仅`.`|
| `:workspace_roots` |当前会话的工作区根加上任何启用的配置文件定义的工作区根|是的|
| `:tmpdir`          |`$TMPDIR` 位置（如果有）|仅`.`|
| `:slash_tmp`       |`/tmp` 文件夹（如果存在）|仅`.`|
| `/absolute/path`   |平台绝对路径，例如 macOS/Linux/WSL 上的 `/path` 或本机 Windows 上的 `C:\path`|是的|
| `~/path`           |当前用户主目录下的路径|是的|

在本机 Windows 上，主相对路径也可以使用反斜杠，例如 
`~\work`。

仅当配置文件有意需要广泛的阅读覆盖范围时才使用 `:root`：

```toml
[permissions.audit.filesystem]
":root" = "read"
```

使用 `:workspace_roots` 下的嵌套条目来限制对工作区根目录 
 相对子路径的访问：

```toml
[permissions.project-edit.filesystem.":workspace_roots"]
"." = "write"          # each workspace root
"docs" = "read"        # each workspace-root docs directory
"generated" = "deny"   # each workspace-root generated directory
```

嵌套子路径必须保留在其工作区根目录内。诸如
`../other-repo`之类的父遍历被拒绝。

### 拒绝使用精确路径或 glob 进行读取

对 Codex 不应读取的文件或子树使用 `deny`，即使更广泛的 
 配置文件规则授予附近的访问权限也是如此。精确路径适用于稳定位置 
，例如 `~/.ssh`。当配置文件需要覆盖 
 系列敏感文件（其确切位置因存储库而异）时，Glob 模式效果更好。

当 glob 位于 `:workspace_roots` 下时，Codex 会相对于每个 
 有效工作空间根来解释它。例如：

```toml
[permissions.project-edit.filesystem.":workspace_roots"]
"**/*.env" = "deny"
```

此规则拒绝读取在每个运行时或 
 配置文件定义的工作区根下找到的匹配 `.env` 文件。当您想要保留正常的 
 工作空间写入，同时保持环境文件、生成的机密或类似的 
 凭证承载文件不可读时，请使用它。

`deny` 支持 glob 模式作为拒绝读取规则。 `read` 或 `write` 全局变量 
 在 Linux、WSL 和本机 Windows 沙箱上的可移植性较差，因此尽可能首选精确的 
 路径或子树规则，例如 `"docs/**" = "read"`。

在 Linux、WSL 和本机 Windows 上，无界 `**` 拒绝读取模式可能需要在沙箱启动之前进行 
 有界预扩展。当
使用无界模式（例如`"**/*.env" = "deny"`）时，设置`glob_scan_max_depth`：

```toml
[permissions.project-edit.filesystem]
glob_scan_max_depth = 3

[permissions.project-edit.filesystem.":workspace_roots"]
"**/*.env" = "deny"
```

`glob_scan_max_depth` 必须至少为 `1`。较高的值在
沙箱启动之前扫描得更深，这可以在 Linux、WSL 和本机 Windows 上添加启动工作。
如果您不喜欢使用有界扩展，请列出显式深度，例如 
`*.env`、`*/*.env` 和 `*/*/*.env`。

当相同的规则应适用于
而不是当前会话根时，将可重用工作区根添加到配置文件中：

```toml
[permissions.project-edit.workspace_roots]
"~/code/app" = true
"~/code/shared-lib" = true
```

当此配置文件处于活动状态时，Codex 将 `:workspace_roots` 规则应用于 
 当前会话的运行时工作空间根以及每个启用的配置文件定义的 
 工作空间根。

在本机 Windows 上，支持将驱动器号路径（例如 `D:\work`）和 UNC 路径（例如 
`\\server\share`）作为绝对路径。

## 网络权限

网络访问和网络过滤是单独的设置。设置
`permissions.<name>.network.enabled = true`以允许命令访问网络，
并启用`features.network_proxy`以强制执行配置文件的域规则：

```toml
[features]
network_proxy = true

[permissions.project-edit.network]
enabled = true

[permissions.project-edit.network.domains]
"example.com" = "allow"      # exact host
"*.example.com" = "allow"    # subdomains only
"**.example.com" = "allow"   # apex and subdomains
"ads.example.com" = "deny"   # deny wins over allow
```

结果行为取决于这两个设置：

- 网络关闭：无论代理如何，命令都无法访问网络
功能。
- 网络打开，代理关闭：命令具有直接、不受限制的网络
访问。不强制执行权限配置文件中的域规则。
- 网络打开，代理打开：命令使用代理，该代理强制执行配置文件的
域规则。如果活动代理没有允许的域，它将阻止外部 
 目标。

添加`[permissions.<name>.network.domains]`或设置
`permissions.<name>.network.enabled = true`不会启用
`features.network_proxy`。作为替代方案，管理员可以使用 `requirements.toml` 中的 `[experimental_network]` 启用 
 代理。请参阅
[托管配置](<https://learn.chatgpt.com/docs/enterprise/managed-configuration#configure-network-access-requirements>)。

当活动时，网络沙箱代理默认绑定到本地侦听器：

```toml
[permissions.project-edit.network]
enabled = true
proxy_url = "http://127.0.0.1:3128"
enable_socks5 = true
socks_url = "http://127.0.0.1:8081"
enable_socks5_udp = true
```

将这些侦听器设置保留为默认值，除非您要与 
 特定运行时集成。 `dangerously_*` 网络密钥是 
 专业环境的逃生口，不应用于普通的本地开发。

### 本地和专用网络

当网络代理处于活动状态时，Codex 默认应用 
 的 local/private-network 防护，以防御 DNS 重新绑定和意外访问本地 
 服务。要有意允许文字本地目标，请将确切的 
 主机或 IP 文字列入白名单：

```toml
[permissions.project-edit.network.domains]
"localhost" = "allow"
"127.0.0.1" = "allow"
```

仅当配置文件必须到达解析为本地或私有地址的白名单 
 主机名时，才设置 `allow_local_binding = true`：

```toml
[permissions.project-edit.network]
enabled = true
allow_local_binding = true

[permissions.project-edit.network.domains]
"localhost" = "allow"
```

### Unix 套接字

Unix 套接字代理是 Docker 等工具的本地逃生口。谨慎使用它
：

```toml
[permissions.project-edit.network.unix_sockets]
"/var/run/docker.sock" = "allow"
"/tmp/old.sock" = "deny"
```

使用 `deny` 拒绝套接字路径，包括继承的允许条目。有效白名单中省略了拒绝的
套接字路径。

启用 Unix 套接字后，将代理侦听器绑定到环回地址。

## 从旧的沙箱设置迁移

权限配置文件取代了旧的组合 `sandbox_mode` 和
`sandbox_workspace_write` 当您想要一个可重复使用的配置文件来描述两者时
文件系统和网络行为。使用一个系统或另一个系统进行会话，而不是
两者都有。

建议的起点：

- 对于只读工作流程，请使用内置的 `:read-only` 配置文件或定义
仅在需要时具有读取访问权限的自定义配置文件。
- 对于工作区编辑，请使用内置的 `:workspace` 配置文件或定义一个
自定义配置文件写入 `:workspace_roots` 并仅添加工作流所需的额外 
 临时或缓存路径。
- 对于不受限制的本地执行，请使用 `:danger-full-access` 只有当你
有意想要最广泛的本地访问模型。

配置文件描述了会话的本地默认状态。组织管理的
要求仍然可以添加用户配置不应
扩大的限制。有关管理员强制执行的文件系统和网络约束，请参阅[托管配置](<https://learn.chatgpt.com/docs/enterprise/managed-configuration>)
。

## 范围和执行

<a id="synced-work-and-environment-policy"></a>

### 使用 Work Cloud 和环境策略访问本地计算机

查看使用 Work Cloud 运行本地计算机访问的每个环境的策略：

- **企业要求：**对于使用本地访问和点，支持全局策略在启用托管策略时管理共享云编排器；适用的本地执行要求控制连接的计算机。
- **云执行：**工作云容器使用现有的工作云策略。
- **本地策略顺序：** MDM 和旧受管设备要求高于 Agent 安全性。该设备的系统要求文件排名低于 Agent 安全性。
- **策略优先级：**即使较低优先级的策略更具体，较高优先级的策略也会获胜。在一项策略中，OS-特定环境覆盖优先于所有OS环境覆盖，其次是全局。

某些要求具有特定于字段的合并规则。有关支持的字段和运行时限制，请参阅[托管配置](<https://learn.chatgpt.com/docs/enterprise/managed-configuration>)和[配置参考](<https://learn.chatgpt.com/docs/config-file/config-reference>)。不要假设 Work Cloud 支持此处显示的 Codex 权限配置文件。

权限配置文件定义本地沙盒命令
执行的边界。将它们与审批策略和单独的控件 
 一起使用，用于 Web 搜索、连接器、MCP 服务器、内置浏览器、计算机使用、
 和 Codex 云。

### 配置文件控制哪些内容

- **本地命令执行：**权限配置文件管理沙盒命令
在您的机器上运行的。连接器、MCP 服务器、浏览器或
 计算机使用界面、Codex 云环境设置和批准的
 升级使用自己的控件。
- **文件系统写入：** 具有写入功能的配置文件可以创建持久更改。
将脚本、构建步骤、包管理器挂钩、shell 启动
 文件和共享目录的写入视为敏感，因为后续工具或用户可以
 在原始沙箱上下文之外执行这些文件。
- **出站目的地：** 网络域规则限制沙盒位置
仅当网络代理处于活动状态时，命令流量才能进行。它们不会
确定允许的目的地是否值得信赖，并且通配符允许
规则保持广泛。
- **本地服务：**活动网络代理阻止本地和专用网络
默认目标。将 `localhost`、私有 IP、Unix 套接字列入白名单或设置 
 `allow_local_binding = true` 显式打开对本地服务的访问。

### 网络代理无法控制的内容

网络代理仅过滤来自在 
 沙箱内运行的本地命令的流量。它不会将配置文件的域白名单应用于：

- **网页搜索：** 托管搜索工具使用自己的访问设置。使用
`web_search`，对于托管客户端，`allowed_web_search_modes` 来控制 
。 `tools.web_search.allowed_domains` 过滤搜索结果，而不是命令
 网络访问。
- **应用程序和连接器：**连接器支持的工具使用自己的服务端
连接、工作区权限以及应用程序或工具设置。
- **MCP 服务器：** 本地和远程 MCP 服务器使用自己的进程或
运输。使用 `mcp_servers` 配置和托管服务器 
 允许列表来控制它们。
- **浏览器和计算机使用：**浏览器导航和计算机使用操作
使用自己的功能和审批控制。
- **Codex服务流量：**模型、认证等客户端服务
请求使用客户端单独的 HTTP 和系统代理设置。
- **Codex 云：** 这些任务使用其环境自己的
[网络设置](<https://learn.chatgpt.com/docs/environments/cloud-environments#connect-to-services>)。
 Codex云（旧版）具有单独的
 [互联网访问设置](<https://learn.chatgpt.com/docs/cloud/internet-access>)。

要限制这些表面，请直接配置每个功能。命令网络
白名单并不是针对Codex可以执行的每个操作的全局网络策略。

### 执法如何运作

- 在 macOS 上，Codex 使用 Seatbelt 沙箱配置文件。如果所选策略不能
由平台沙箱强制执行，Codex 拒绝运行该命令，而不是
 在沙箱中静默运行它。
- 在 Linux 和 WSL 上，Codex 使用 [bubblewrap](<https://github.com/containers/bubblewrap>)
和 [seccomp](<https://www.kernel.org/doc/html/latest/userspace-api/seccomp_filter.html>)、
，其中 Landlock 可用于兼容性回退路径。最强大的
执行路径取决于用户命名空间和内核支持；受限制的
容器主机可以强制兼容路径，并且不支持的拆分策略
会被拒绝。
- 在 Windows 上，MXC 对文件系统和网络权限使用进程隔离。
在启用之前检查 [MXC 兼容性 ](<https://learn.chatgpt.com/docs/windows/windows-sandbox#mxc-compatibility>)
。旧版 `elevated` 回退使用专用的低权限 
 沙箱用户、文件系统权限边界和防火墙规则。
 `unelevated` 是旧版回退，网络隔离较弱，无法强制执行 
 每个拆分 read/write 剥离，因此不支持的政策将被拒绝。当您需要Linux沙箱模型时，请使用WSL
。

### 操作指导

选择仍然可以完成任务的最窄配置文件，尤其是当
您授予写入或出站网络访问权限时。保留审批策略、秘密
处理，并允许与该访问级别一致的规则。

## 常用型材

### 具有网络允许列表的只读状态

```toml
default_permissions = "readonly-net"

[features]
network_proxy = true

[permissions.readonly-net.filesystem]
":minimal" = "read"

[permissions.readonly-net.filesystem.":workspace_roots"]
"." = "read"

[permissions.readonly-net.network]
enabled = true

[permissions.readonly-net.network.domains]
"api.openai.com" = "allow"
```

### 文件访问仅限于工作区

下面是一个权限配置文件的示例，它将使您的工作区文件夹可由 Codex 写入，同时拒绝读取文件系统的其余部分（有限的例外情况，由 `:minimal` 确定）。

```toml
default_permissions = "workspace-only"

[permissions.workspace-only]
# By extending the :workspace profile, you get Codex's safeguards to ensure
# subfolders such as .codex/ and .git/ within a workspace root are read-only
# while the rest of the folder is writable.
extends = ":workspace"

[permissions.workspace-only.filesystem]
# By default, deny read access to all files on disk.
":root" = "deny"

# Though in practice, a software agent needs to be able to read folders that
# contain common tools, such as `/usr/bin`, to get work done, so grant access
# to a "minimal" set of files and folders, as determined by Codex.
":minimal" = "read"

# By extending the :workspace profile, :tmpdir and :slash_tmp are "write" by
# default, though you can deny access to them altogether, if desired.
":tmpdir" = "deny"
":slash_tmp" = "deny"
```

### 工作区无网络写入

```toml
default_permissions = "project-edit"

[permissions.project-edit.filesystem]
":minimal" = "read"

[permissions.project-edit.filesystem.":workspace_roots"]
"." = "write"

[permissions.project-edit.network]
enabled = false
```

### 具有公共 Web 访问权限的工作区写入

```toml
default_permissions = "workspace-net"

[features]
network_proxy = true

[permissions.workspace-net.filesystem]
":minimal" = "read"

[permissions.workspace-net.filesystem.":workspace_roots"]
"." = "write"

[permissions.workspace-net.network]
enabled = true

[permissions.workspace-net.network.domains]
"*" = "allow"
```

仅当您打算允许公共网络
访问时，才使用全局`"*"`允许规则。拒绝规则可以缩小广泛的允许名单范围。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Permissions

> For the complete documentation index, see [llms.txt](<https://learn.chatgpt.com/llms.txt>). Markdown versions of documentation pages are available by appending `.md` to the page URL.

Beta. Permission profiles are under active development and may change.

Permission profiles do not compose with the older sandbox settings. Configure
  either `default_permissions` and `[permissions]`, or `sandbox_mode` /
  `sandbox_workspace_write`, but not both. If `sandbox_mode` appears in any
  loaded config file, you pass `--sandbox`, or the selected config profile sets
  `sandbox_mode`, Codex uses those older sandbox settings instead of
  `default_permissions`.

Managed `allowed_permission_profiles` is the exception: it makes Codex use
permission profiles. Remove older settings such as
`sandbox_mode` and `[sandbox_workspace_write]` before deploying a managed
profile allowlist. For a mixed-version enterprise rollout, you can keep the
managed `allowed_sandbox_modes` requirement as a temporary compatibility
constraint until every client runs Codex 0.138.0 or later.

Permission profiles let you apply least-privilege boundaries to local commands
Codex runs on your behalf. A profile is a named policy that combines filesystem
rules, which define what commands can read or write, with network rules, which
define which destinations commands can reach.

A profile's `network.enabled = true` permits command network access, but it
  does not start the network proxy. To enforce profile domain rules, also set
  `features.network_proxy = true` in `config.toml`, or use enabled,
  administrator-managed `[experimental_network]` requirements. Without an active
  proxy, profile domain rules do not restrict direct network access.

Use profiles to give Codex enough access for the current chat without granting
broad access to your machine or network. For example, a read-only profile can
let Codex inspect a project without editing it, while a write-capable profile
can limit edits to selected workspace roots.

Local permission profiles are supported on macOS, Linux, WSL, and native
Windows. See [Scope and enforcement](<#scope-and-enforcement>) for platform-specific
details and caveats.

For current Codex Cloud network settings, see [Connect to services](<https://learn.chatgpt.com/docs/environments/cloud-environments#connect-to-services>). For Codex Cloud (Legacy), see [Internet access](<https://learn.chatgpt.com/docs/cloud/internet-access>).

## Define and select a profile

Codex includes three built-in permission profiles:

- `:read-only` keeps local command execution read-only.
- `:workspace` allows writes inside the active workspace roots and system temp directories.
- `:danger-full-access` removes local sandbox restrictions and should be used
  only when that broad access is intentional.

Create a named profile under `[permissions.<name>]`, then set the top-level
`default_permissions` key to that profile name or to one of the built-ins above.
In this example, `project-edit` is a user-defined profile name, not a built-in
value.

Enterprise administrators can define profiles and restrict which profiles
users may select through managed `requirements.toml`. Once
`allowed_permission_profiles` is present, omitted profiles are denied,
including omitted built-ins and profiles added in future Codex versions. See
[Control available permission profiles](<https://learn.chatgpt.com/docs/enterprise/managed-configuration#control-available-permission-profiles>)
for the recommended managed configuration.

Custom profiles use two related concepts:

- `[permissions.<name>.workspace_roots]` adds concrete directories that should
  count as workspace roots for that profile.
- `[permissions.<name>.filesystem.":workspace_roots"]` defines the filesystem
  rules Codex applies inside every effective workspace root: the current
  session's runtime workspace roots plus the profile-defined roots above.

Profiles also use the normal config-layer model. Higher-precedence layers can
add or replace entries under the same profile name without restating the whole
profile.

For example, an organization-level config and a user-level config can extend
the same profile independently:

```toml
# /etc/codex/config.toml
[permissions.server.workspace_roots]
"~/code/server" = true
```

```toml
# ~/.codex/config.toml
[permissions.server.workspace_roots]
"~/code/mobile-app" = true
```

When `server` is active, both workspace roots participate in the effective
profile.

```toml
default_permissions = "project-edit"

[features]
network_proxy = true

[permissions.project-edit.workspace_roots]
"~/code/app" = true
"~/code/shared-lib" = true

[permissions.project-edit.filesystem]
":minimal" = "read"

[permissions.project-edit.filesystem.":workspace_roots"]
"." = "write"
".devcontainer" = "read"
"**/*.env" = "deny"

[permissions.project-edit.network]
enabled = true

[permissions.project-edit.network.domains]
"api.openai.com" = "allow"
"objects.githubusercontent.com" = "allow"
"*.github.com" = "allow"
"tracking.example.com" = "deny"
```

This profile:

- Reads the minimal runtime paths common developer tools need.
- Applies the same workspace-root rules to the current session and the
  profile-defined roots.
- Keeps IDE-adjacent settings such as `.devcontainer/` read-only under each
  root.
- Denies matching environment files with a glob rule.
- Allows network access only through the configured domain policy.

Inside an active profile, narrower deny rules stay in force even when a broader
path is readable or writable. For example, a profile can make workspace roots
writable while still setting a matching `.env` path to `deny`.

## Extend a profile

Use `extends` when a profile is mostly the same as a built-in or another named
profile. Prefer extending a built-in profile over starting from scratch so
baseline protections carry forward. Extending `:workspace`, for example, keeps
the workspace root's `.codex` directory read-only unless you explicitly
override it. Set the parent once, then add or override only the rules that
differ.

```toml
default_permissions = "project-edit"

[features]
network_proxy = true

[permissions.project-edit]
description = "Project editing with OpenAI API access."
extends = ":workspace"

[permissions.project-edit.filesystem.":workspace_roots"]
"**/*.env" = "deny"

[permissions.project-edit.network]
enabled = true

[permissions.project-edit.network.domains]
"api.openai.com" = "allow"
```

This profile starts with `:workspace`, keeps matching `.env` files denied, and
allows requests to `api.openai.com`. A profile can extend `:read-only`,
`:workspace`, or another named profile. It cannot extend
`:danger-full-access`; Codex also rejects unknown parents and inheritance
cycles.

## Configuration spec

| Entry                                                             | Type / values              | Default                 | Details                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ----------------------------------------------------------------- | -------------------------- | ----------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `default_permissions`                                             | String profile name        | None                    | Names the permissions profile Codex applies by default. It must match a profile under `[permissions]` or a built-in such as `:workspace`. Set it explicitly for predictable behavior; managed requirements may omit it only when both `:workspace` and `:read-only` are explicitly allowed. Codex uses older sandbox settings unless managed `allowed_permission_profiles` tells it to use permission profiles in this setup. |
| `[permissions.<name>]`                                            | Table                      | None                    | Defines a named profile. `default_permissions` selects one profile as the default; other permission-profile settings also use the profile name.                                                                                                                                                                                                                                                                               |
| `permissions.<name>.description`                                  | String                     | None                    | Provides a human-readable description for the profile. A profile does not inherit its parent's description through `extends`.                                                                                                                                                                                                                                                                                                 |
| `permissions.<name>.extends`                                      | String profile name        | None                    | Starts this profile from another named profile or the built-in `:read-only` or `:workspace` profile. Codex rejects `:danger-full-access`, unknown parents, and inheritance cycles.                                                                                                                                                                                                                                            |
| `[permissions.<name>.workspace_roots]`                            | Table                      | None                    | Adds profile-defined workspace roots that receive `:workspace_roots` filesystem rules alongside the current session's runtime workspace roots.                                                                                                                                                                                                                                                                                |
| `permissions.<name>.workspace_roots."<path>"`                     | boolean                    | `false`                 | Adds the path to the profile's workspace root set when `true`. Entries set to `false` remain inactive.                                                                                                                                                                                                                                                                                                                        |
| `[permissions.<name>.filesystem]`                                 | Table                      | None                    | Maps filesystem paths to access values or scoped subpath maps. Missing or empty filesystem tables keep filesystem access restricted and emit a startup warning.                                                                                                                                                                                                                                                               |
| `permissions.<name>.filesystem.glob_scan_max_depth`               | Number                     | None                    | Limits deny-read glob expansion on Linux, WSL, and native Windows when Codex snapshots matches before sandbox startup. Larger values can increase startup scanning work. Use a value of at least `1` when an unbounded `**` pattern needs bounded pre-expansion.                                                                                                                                                              |
| `[permissions.<name>.filesystem]."<path>"`                        | `read`, `write`, or `deny` | None                    | Grants direct access for a supported path. `deny` denies access and wins over equally specific `write` or `read` entries. Codex rejects direct write rules that the active runtime cannot enforce.                                                                                                                                                                                                                            |
| `[permissions.<name>.filesystem."<path>"]."<subpath>"`            | `read`, `write`, or `deny` | None                    | Grants access to a descendant of `<path>`. Use `.` for the base path. Other subpaths must be relative descendants and cannot contain `.` or `..` components.                                                                                                                                                                                                                                                                  |
| `[permissions.<name>.network]`                                    | Table                      | None                    | Configures command network access and the policy that an active network proxy enforces. Enable `features.network_proxy` unless administrator-managed network requirements start the proxy.                                                                                                                                                                                                                                    |
| `permissions.<name>.network.enabled`                              | boolean                    | `false`                 | Enables network access for commands in the profile. It does not start the network proxy; without an active proxy, commands can connect directly without domain restrictions.                                                                                                                                                                                                                                                  |
| `[permissions.<name>.network.domains]`                            | Table                      | None                    | Maps host patterns to `allow` or `deny`. Rules apply only when the network proxy is active. The active proxy blocks domain requests if there are no `allow` entries, and deny entries override allow entries.                                                                                                                                                                                                                 |
| `permissions.<name>.network.domains."<pattern>"`                  | `allow` or `deny`          | None                    | Supports exact hosts, `*.example.com` for subdomains, `**.example.com` for apex plus subdomains, and `*` as an allow-only global wildcard. Host patterns are normalized by trimming, converting to lowercase, stripping a trailing dot, and stripping port numbers or brackets.                                                                                                                                               |
| `[permissions.<name>.network.unix_sockets]`                       | Table                      | None                    | Maps Unix socket allowlist overrides. Use only for local integrations such as Docker.                                                                                                                                                                                                                                                                                                                                         |
| `permissions.<name>.network.unix_sockets."<path>"`                | `allow` or `deny`          | None                    | Adds an absolute Unix socket path to the effective allowlist with `allow`, or rejects it with `deny`. Denied entries are omitted from the effective allowlist.                                                                                                                                                                                                                                                                |
| `permissions.<name>.network.proxy_url`                            | URL string                 | `http://127.0.0.1:3128` | HTTP proxy listener used for `HTTP_PROXY`, `HTTPS_PROXY`, WebSocket proxy variables, and related tool proxy environment variables.                                                                                                                                                                                                                                                                                            |
| `permissions.<name>.network.enable_socks5`                        | boolean                    | `true`                  | Enables the SOCKS5 listener used for `ALL_PROXY` and FTP proxy variables.                                                                                                                                                                                                                                                                                                                                                     |
| `permissions.<name>.network.socks_url`                            | URL string                 | `http://127.0.0.1:8081` | SOCKS5 listener address.                                                                                                                                                                                                                                                                                                                                                                                                      |
| `permissions.<name>.network.enable_socks5_udp`                    | boolean                    | `true`                  | Enables SOCKS5 UDP support when the SOCKS5 listener is enabled.                                                                                                                                                                                                                                                                                                                                                               |
| `permissions.<name>.network.allow_upstream_proxy`                 | boolean                    | `true`                  | Allows the network sandbox proxy to respect upstream `HTTP(S)_PROXY` and `ALL_PROXY` settings for outbound requests.                                                                                                                                                                                                                                                                                                          |
| `permissions.<name>.network.allow_local_binding`                  | boolean                    | `false`                 | Disables the local/private-network guard when `true`. When `false`, exact local literals such as `localhost` or `127.0.0.1` must be explicitly allowlisted, and hostnames that resolve to local or private IPs remain blocked.                                                                                                                                                                                                |
| `permissions.<name>.network.dangerously_allow_non_loopback_proxy` | boolean                    | `false`                 | Allows proxy listeners to bind non-loopback addresses. Leave unset for ordinary local development.                                                                                                                                                                                                                                                                                                                            |
| `permissions.<name>.network.dangerously_allow_all_unix_sockets`   | boolean                    | `false`                 | Bypasses the Unix socket allowlist where Unix socket proxying is supported. This is a broad local escape hatch.                                                                                                                                                                                                                                                                                                               |

## Filesystem permissions

Filesystem entries use `read`, `write`, or `deny`:

| Access  | Meaning                                                                                                                           |
| ------- | --------------------------------------------------------------------------------------------------------------------------------- |
| `read`  | Allows commands to read files and list directories under the path. Commands cannot create, modify, rename, or delete files there. |
| `write` | Allows commands to read and modify files under the path, including creating, renaming, and deleting files when the OS allows it.  |
| `deny`  | Denies both reads and writes under the path. Use it to carve out a denied subpath from a broader `read` or `write` grant.         |

More specific entries override broader entries. When two entries target the
same path, `deny` takes precedence over `write`, and `write` takes precedence
over `read`.

This precedence lets a profile describe a broad working area first, then carve
out files or directories that should stay unreadable:

```toml
[permissions.project-edit.filesystem]
":minimal" = "read"

[permissions.project-edit.filesystem.":workspace_roots"]
"." = "write"
".devcontainer" = "read"
"**/*.env" = "deny"
```

In this example, the workspace root stays writable, `.devcontainer/` stays
readable without becoming writable, and matching environment files remain
unavailable to sandboxed commands.

A more specific path can also reopen a narrower subtree inside a broader deny:

```toml
[permissions.project-edit.filesystem]
"~/Documents" = "deny"
"~/Documents/codex" = "write"
```

Supported path forms:

| Path               | Meaning                                                                                     | Scoped subpaths |
| ------------------ | ------------------------------------------------------------------------------------------- | --------------- |
| `:root`            | The filesystem root                                                                         | `.` only        |
| `:minimal`         | Platform and runtime paths needed by common tools                                           | `.` only        |
| `:workspace_roots` | The current session's workspace roots plus any enabled profile-defined workspace roots      | Yes             |
| `:tmpdir`          | The `$TMPDIR` location, when one is available                                               | `.` only        |
| `:slash_tmp`       | The `/tmp` folder, if it exists                                                             | `.` only        |
| `/absolute/path`   | A platform absolute path, such as `/path` on macOS/Linux/WSL or `C:\path` on native Windows | Yes             |
| `~/path`           | A path under the current user's home directory                                              | Yes             |

On native Windows, home-relative paths can also use backslashes, such as
`~\work`.

Use `:root` only when a profile intentionally needs broad read coverage:

```toml
[permissions.audit.filesystem]
":root" = "read"
```

Use nested entries under `:workspace_roots` to scope access to workspace-root
relative subpaths:

```toml
[permissions.project-edit.filesystem.":workspace_roots"]
"." = "write"          # each workspace root
"docs" = "read"        # each workspace-root docs directory
"generated" = "deny"   # each workspace-root generated directory
```

Nested subpaths must stay inside their workspace root. Parent traversal such as
`../other-repo` is rejected.

### Deny reads with exact paths or globs

Use `deny` for files or subtrees that Codex should not read, even when a broader
profile rule grants access nearby. Exact paths work well for stable locations
such as `~/.ssh`. Glob patterns work better when a profile needs to cover a
family of sensitive files whose exact locations vary across repositories.

When a glob sits under `:workspace_roots`, Codex interprets it relative to each
effective workspace root. For example:

```toml
[permissions.project-edit.filesystem.":workspace_roots"]
"**/*.env" = "deny"
```

This rule denies reads for matching `.env` files found beneath each runtime or
profile-defined workspace root. Use it when you want to preserve normal
workspace writes while keeping environment files, generated secrets, or similar
credential-bearing files unreadable.

`deny` glob patterns are supported as deny-read rules. `read` or `write` globs
are less portable on Linux, WSL, and native Windows sandboxing, so prefer exact
paths or subtree rules such as `"docs/**" = "read"` when possible.

On Linux, WSL, and native Windows, an unbounded `**` deny-read pattern may need
bounded pre-expansion before the sandbox starts. Set `glob_scan_max_depth` when
you use an unbounded pattern such as `"**/*.env" = "deny"`:

```toml
[permissions.project-edit.filesystem]
glob_scan_max_depth = 3

[permissions.project-edit.filesystem.":workspace_roots"]
"**/*.env" = "deny"
```

`glob_scan_max_depth` must be at least `1`. Higher values scan deeper before
sandbox startup, which can add startup work on Linux, WSL, and native Windows.
If you prefer not to use bounded expansion, list explicit depths such as
`*.env`, `*/*.env`, and `*/*/*.env`.

Add reusable workspace roots to the profile when the same rules should apply to
more than the current session root:

```toml
[permissions.project-edit.workspace_roots]
"~/code/app" = true
"~/code/shared-lib" = true
```

When this profile is active, Codex applies the `:workspace_roots` rules to the
current session's runtime workspace roots and to each enabled profile-defined
workspace root.

On native Windows, drive-letter paths such as `D:\work` and UNC paths such as
`\\server\share` are supported as absolute paths.

## Network permissions

Network access and network filtering are separate settings. Set
`permissions.<name>.network.enabled = true` to let commands access the network,
and enable `features.network_proxy` to enforce the profile's domain rules:

```toml
[features]
network_proxy = true

[permissions.project-edit.network]
enabled = true

[permissions.project-edit.network.domains]
"example.com" = "allow"      # exact host
"*.example.com" = "allow"    # subdomains only
"**.example.com" = "allow"   # apex and subdomains
"ads.example.com" = "deny"   # deny wins over allow
```

The resulting behavior depends on both settings:

- Network off: Commands cannot access the network, regardless of the proxy
  feature.
- Network on, proxy off: Commands have direct, unrestricted network
  access. Domain rules in the permission profile are not enforced.
- Network on, proxy on: Commands use the proxy, which enforces the profile's
  domain rules. If the active proxy has no allowed domains, it blocks external
  destinations.

Adding `[permissions.<name>.network.domains]` or setting
`permissions.<name>.network.enabled = true` does not enable
`features.network_proxy`. As an alternative, administrators can enable the
proxy with `[experimental_network]` in `requirements.toml`. See
[Managed configuration](<https://learn.chatgpt.com/docs/enterprise/managed-configuration#configure-network-access-requirements>).

When active, the network sandbox proxy binds to local listeners by default:

```toml
[permissions.project-edit.network]
enabled = true
proxy_url = "http://127.0.0.1:3128"
enable_socks5 = true
socks_url = "http://127.0.0.1:8081"
enable_socks5_udp = true
```

Leave these listener settings at their defaults unless you are integrating with
a specific runtime. The `dangerously_*` network keys are escape hatches for
specialized environments and should not be used for ordinary local development.

### Local and private networks

When the network proxy is active, Codex applies a local/private-network guard by
default as a defense against DNS rebinding and accidental access to local
services. To intentionally allow a literal local target, allowlist the exact
host or IP literal:

```toml
[permissions.project-edit.network.domains]
"localhost" = "allow"
"127.0.0.1" = "allow"
```

Set `allow_local_binding = true` only when the profile must reach allowlisted
hostnames that resolve to local or private addresses:

```toml
[permissions.project-edit.network]
enabled = true
allow_local_binding = true

[permissions.project-edit.network.domains]
"localhost" = "allow"
```

### Unix sockets

Unix socket proxying is a local escape hatch for tools such as Docker. Use it
sparingly:

```toml
[permissions.project-edit.network.unix_sockets]
"/var/run/docker.sock" = "allow"
"/tmp/old.sock" = "deny"
```

Use `deny` to reject a socket path, including an inherited allow entry. Denied
socket paths are omitted from the effective allowlist.

When Unix sockets are enabled, keep proxy listeners bound to loopback addresses.

## Migrate from older sandbox settings

Permission profiles replace the older combination of `sandbox_mode` and
`sandbox_workspace_write` when you want one reusable profile to describe both
filesystem and network behavior. Use one system or the other for a session, not
both.

Suggested starting points:

- For a read-only workflow, use the built-in `:read-only` profile or define a
  custom profile with read access only where needed.
- For workspace editing, use the built-in `:workspace` profile or define a
  custom profile that writes through `:workspace_roots` and adds only the extra
  temp or cache paths the workflow needs.
- For unrestricted local execution, use `:danger-full-access` only when you
  intentionally want the broadest local access model.

Profiles describe the local default posture for a session. Organization-managed
requirements can still add restrictions that user configuration should not
broaden. See [Managed configuration](<https://learn.chatgpt.com/docs/enterprise/managed-configuration>)
for admin-enforced filesystem and network constraints.

## Scope and enforcement

<a id="synced-work-and-environment-policy"></a>

### Local computer access with Work Cloud and environment policy

Review the policy for each environment where Local computer access with Work Cloud runs:

- **Enterprise requirements:** For Work with local access and dots, supported Global policy governs the shared cloud orchestrator when managed policy is enabled; applicable local execution requirements govern the connected computer.
- **Cloud execution:** Work cloud containers use existing Work Cloud policies.
- **Local policy order:** MDM and legacy managed-device requirements rank above Agent Security. The device's system requirements file ranks below Agent Security.
- **Policy priority:** A higher-priority policy wins even if a lower-priority policy is more specific. Within one policy, OS-specific environment overrides take priority over all-OS environment overrides, followed by Global.

Some requirements have field-specific merge rules. See [Managed configuration](<https://learn.chatgpt.com/docs/enterprise/managed-configuration>) and the [Configuration Reference](<https://learn.chatgpt.com/docs/config-file/config-reference>) for supported fields and runtime limits. Don't assume Work Cloud supports the Codex permission profiles shown here.

Permission profiles define the boundaries for local sandboxed command
execution. Use them together with approval policies and the separate controls
for web search, connectors, MCP servers, the built-in browser, Computer Use,
and Codex cloud.

### What profiles control

- **Local command execution:** Permission profiles govern sandboxed commands
  that run on your machine. Connectors, MCP servers, browser or
  computer-use surfaces, Codex cloud environment settings, and approved
  escalations use their own controls.
- **Filesystem writes:** A write-capable profile can create persistent changes.
  Treat writes to scripts, build steps, package manager hooks, shell startup
  files, and shared directories as sensitive because later tools or users can
  execute those files outside the original sandbox context.
- **Outbound destinations:** Network domain rules constrain where sandboxed
  command traffic can go only while the network proxy is active. They do not
  determine whether an allowed destination is trustworthy, and wildcard allow
  rules stay broad.
- **Local services:** An active network proxy blocks local and private network
  targets by default. Allowlisting `localhost`, private IPs, Unix sockets, or setting
  `allow_local_binding = true` explicitly opens access to local services.

### What the network proxy does not control

The network proxy only filters traffic from local commands that run inside the
sandbox. It does not apply the profile's domain allowlist to:

- **Web search:** The hosted search tool uses its own access settings. Use
  `web_search` and, for managed clients, `allowed_web_search_modes` to control
  it. `tools.web_search.allowed_domains` filters search results, not command
  network access.
- **Apps and connectors:** Connector-backed tools use their own service-side
  connections, workspace permissions, and app or tool settings.
- **MCP servers:** Local and remote MCP servers use their own process or
  transport. Control them with `mcp_servers` configuration and managed server
  allowlists.
- **Browser and Computer Use:** Browser navigation and computer-use actions
  use their own feature and approval controls.
- **Codex service traffic:** Model, authentication, and other client service
  requests use the client's separate HTTP and system-proxy settings.
- **Codex Cloud:** These tasks use their environment's own
  [network settings](<https://learn.chatgpt.com/docs/environments/cloud-environments#connect-to-services>).
  Codex Cloud (Legacy) has separate
  [internet access settings](<https://learn.chatgpt.com/docs/cloud/internet-access>).

To limit these surfaces, configure each capability directly. A command network
allowlist is not a global network policy for every action Codex can perform.

### How enforcement works

- On macOS, Codex uses Seatbelt sandbox profiles. If the selected policy cannot
  be enforced by the platform sandbox, Codex refuses to run the command instead
  of silently running it unsandboxed.
- On Linux and WSL, Codex uses [bubblewrap](<https://github.com/containers/bubblewrap>)
  and [seccomp](<https://www.kernel.org/doc/html/latest/userspace-api/seccomp_filter.html>),
  with Landlock available for compatibility fallback paths. The strongest
  enforcement path depends on user namespaces and kernel support; restricted
  container hosts can force compatibility paths, and unsupported split policies
  are refused.
- On Windows, MXC uses process isolation for filesystem and network permissions.
  Review [MXC compatibility](<https://learn.chatgpt.com/docs/windows/windows-sandbox#mxc-compatibility>)
  before enabling it. The legacy `elevated` fallback uses dedicated lower-privilege
  sandbox users, filesystem permission boundaries, and firewall rules.
  `unelevated` is a legacy fallback with weaker network isolation and cannot enforce
  every split read/write carveout, so unsupported policies are refused. Use WSL
  when you need the Linux sandbox model.

### Operational guidance

Choose the narrowest profile that still lets the task complete, especially when
you grant writes or outbound network access. Keep approval policy, secret
handling, and allow rules aligned with that access level.

## Common profiles

### Read-only with network allowlist

```toml
default_permissions = "readonly-net"

[features]
network_proxy = true

[permissions.readonly-net.filesystem]
":minimal" = "read"

[permissions.readonly-net.filesystem.":workspace_roots"]
"." = "read"

[permissions.readonly-net.network]
enabled = true

[permissions.readonly-net.network.domains]
"api.openai.com" = "allow"
```

### File access limited to workspace

Here is an example of a permission profile that will make your workspace folders writable by Codex while denying reads to the rest of the filesystem (with limited exceptions, as determined by `:minimal`).

```toml
default_permissions = "workspace-only"

[permissions.workspace-only]
# By extending the :workspace profile, you get Codex's safeguards to ensure
# subfolders such as .codex/ and .git/ within a workspace root are read-only
# while the rest of the folder is writable.
extends = ":workspace"

[permissions.workspace-only.filesystem]
# By default, deny read access to all files on disk.
":root" = "deny"

# Though in practice, a software agent needs to be able to read folders that
# contain common tools, such as `/usr/bin`, to get work done, so grant access
# to a "minimal" set of files and folders, as determined by Codex.
":minimal" = "read"

# By extending the :workspace profile, :tmpdir and :slash_tmp are "write" by
# default, though you can deny access to them altogether, if desired.
":tmpdir" = "deny"
":slash_tmp" = "deny"
```

### Workspace write without network

```toml
default_permissions = "project-edit"

[permissions.project-edit.filesystem]
":minimal" = "read"

[permissions.project-edit.filesystem.":workspace_roots"]
"." = "write"

[permissions.project-edit.network]
enabled = false
```

### Workspace write with public web access

```toml
default_permissions = "workspace-net"

[features]
network_proxy = true

[permissions.workspace-net.filesystem]
":minimal" = "read"

[permissions.workspace-net.filesystem.":workspace_roots"]
"." = "write"

[permissions.workspace-net.network]
enabled = true

[permissions.workspace-net.network.domains]
"*" = "allow"
```

Use the global `"*"` allow rule only when you intend to allow public network
access. Deny rules can narrow a broad allowlist.


</details>
