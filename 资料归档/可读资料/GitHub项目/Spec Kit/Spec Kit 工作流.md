# Spec Kit 工作流

介绍安装、配置和修改工作流的方式，以及步骤、复用与执行流程，帮助调整项目内的规格开发过程。

- 来源：Spec Kit
- 资料类型：存档文档
- 文档时间：官方文件提交（截至抓取）：[2026-10-09](<https://github.com/github/spec-kit/commit/381ce3bd90dbd0fc53097b614ac1b74d5dcf29d5>)；发表/更新：官方未公开；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.github.com/spec-kit/reference/workflows.html>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Spec Kit/Spec Kit 工作流.md>) · [返回资料总览](<../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

工作流程 | 规格套件文档
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      

      

      

  

  
    
      
        
          
            
规格套件
          
          
            
          
          
            
              
                
                
              
            
          
        
      
    

    

      
        

          
        

        

工作流程

工作流程自动化多步骤规范驱动开发流程 - 将命令、提示、shell 步骤和人工检查点链接到可重复的序列中。它们支持条件逻辑、循环、fan-out/fan-in，并且可以从确切的中断点暂停和恢复。

运行工作流程

指定工作流程运行 <source>

选项
说明

-i / --input
将输入值作为键=值传递（可重复）

--json
将运行结果作为单个 JSON 对象发出

从目录 ID、URL 或本地文件路径运行工作流。工作流程声明的输入可以通过 --input 提供，或者将以交互方式提示。

示例：

指定工作流程运行speckit -i spec =“构建具有拖放任务管理的看板”

使用 --json，打印单个机器可读对象而不是格式化文本（省略标志时默认输出不变）：

指定工作流程运行 my-pipeline.yml --json

{
 "run_id"：“662bf791”，
 "workflow_id"：“构建和审查”，
 "status"：“已暂停”，
 "current_step_id"：“评论”，
 "current_step_index"：0
}

workflow_id是YAML内声明的workflow.id，而不是文件名。该对象的打印方式与所示的完全一样 - 用两个空格缩进漂亮地打印，在没有丰富标记的普通标准输出上 - 因此它总是可以解析。当工作流在 --json 下运行时，步骤将打印的任何进度（例如门提示或提示步骤的 CLI 子进程的输出）都将重定向到 stderr，因此 stdout 仅携带 JSON 对象。从标准输出读取对象；将 stderr 保留到终端或单独捕获它。

对于失败和中止的运行，有效负载包括一个错误字段，其中携带最终步骤的错误消息：

{
 "run_id": "662bf791",
 "workflow_id": "构建并审查",
 "status": "失败",
 "current_step_id"：“繁荣”，
 "current_step_index"：0，
 "error"：“命令退出，代码为3”
}

已完成和暂停的运行忽略错误字段。该错误保留在运行的 state.json 中，因此指定工作流程状态 <run_id> --json 会在事后显示相同的消息。

注意：大多数工作流命令需要已使用指定 init 初始化的项目。例外情况是指定工作流运行 <local-file.{yml,yaml}>，它可以在项目外部运行；在这种情况下，运行状态存储在当前目录的 .specify/workflows/runs/<run_id>/. 下

恢复工作流程

指定工作流程恢复 <run_id>

选项
说明

-i / --input
将输入值更新为键=值（可重复）

--json
将恢复结果作为单个 JSON 对象发出

从暂停或失败的工作流程运行的确切步骤开始恢复。在响应门步骤或修复导致失败的问题后很有用。

提供的 --input 值将合并到运行的存储输入中，并根据工作流的输入类型重新验证，然后使用更新的值重新运行被阻止的步骤。这允许运行继续，并使用仅在暂停后才可用的信息，或者在失败后使用更正的值：

指定工作流程恢复 <run_id> --input cmd="exit 0"

工作流程状态

指定工作流程状态 [<run_id>]

选项
说明

--json
将运行状态（或运行列表）作为 JSON 对象发出

显示特定运行的状态，如果未给出 ID，则列出所有运行。运行状态：已创建、正在运行、已完成、已暂停、失败、已中止。

列出已安装的工作流程

指定工作流程列表

列出当前项目中安装的工作流程。

安装工作流程

指定工作流程添加 <source>

选项
说明

--dev
从本地 YAML 文件、包目录或存档安装

--from <url>
从自定义 URL 安装（<source> 命名预期工作流程 ID）

--version <version>
安装确切的广告目录版本（<source> 必须是工作流程 ID）

从目录、HTTPS URL、本地 YAML 文件、包含 workflow.yml 的 
 目录或 .zip、.tar.gz 或 .tgz
 存档安装工作流。存档可能在根目录或
顶级目录中包含workflow.yml。

目录和存档安装保留完整的工作流程包，
包括脚本和其他配套文件。 ZIP、.tar.gz 和 .tgz
 存档遵循相同的验证和安装行为。

目录条目将当前版本的版本、url、可选 sha256、
 和可选需求保留在顶层。可选版本映射
公布历史版本，而不更改不合格的添加、
搜索、信息或更新选择：

{
 "id"：“示例”，
 "version"：“2.0.0”，
 "url"： "https://example.com/example-2.0.0.zip",
 "releases": {
 "1.0.0": {
 "url": “https://example.com/example-1.0.0.zip”，
 "sha256"： "aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa",
 "requires": {"speckit_version": ">=1.0.0"}
 }
 }
}

每个历史版本都需要自己的URL和SHA-256摘要；需要是
可选，如果存在，必须与下载的工作流定义匹配。
播发版本使用工作流定义的 X.Y.Z 版本格式；
--version 在选择
播发时还接受等效拼写，例如 v1.0 1.0.0 版本。
请求的版本必须存在于提供
工作流程的最高优先级目录中。丢失的版本不会回退到另一个源，并且无法安装 
 仅发现目录。下载的工作流程ID、
版本和声明的摘要在安装前进行验证。 --version 
不适用于本地路径、直接 URL 或 --from 安装。

工作流程叠加

工作流覆盖允许项目扩展或覆盖已安装的工作流，而无需编辑已安装的 workflow.yml。  这可以确保本地自定义在指定捆绑包更新或指定工作流程添加升级中安全。

当指定工作流运行 <workflow-id> 加载工作流时，引擎将使用该工作流 ID 的所有启用的覆盖来组成基本工作流。  结果像任何其他工作流定义一样进行验证。

叠加层的工作原理

覆盖是一个 YAML 文件，它针对基本工作流程的步骤列表声明一组编辑操作。叠加使用较低获胜优先级：首先应用较高优先级的数字，最后应用较低优先级的数字。同等优先级覆盖按 ID 的字母顺序应用，最后一个 ID 赢得冲突。

项目覆盖文件位于：

地点
用途

.specify/workflows/overlays/<id>/*.yml
项目本地定制

覆盖文件格式

推荐的编辑格式使用操作名称作为键，锚点步骤 id 作为值：

id：“my-overlay”
扩展：“speckit”
优先级：10
启用：true
编辑：
  - insert_after：实施
步骤：
 id：run-lint
 类型：shell
 运行：“ruff check src/”

  - 替换：审查规范
步骤：
 id：审查规范
 类型：gate
 消息：“查看生成的规范（覆盖覆盖）。”
 选项：[批准，拒绝]
 on_reject：中止

还支持显式形式：

编辑：
  - 操作：insert_after
锚：实现
 步骤：
 id：run-lint
 类型：shell
 运行：“ruff check src/”

领域

字段
必填
描述

id
是
此叠加层的标识符。用于指定工作流程覆盖 * 命令。只能是小写字母、数字和连字符；没有点、下划线、路径分隔符或覆盖。

扩展
是
此覆盖适用的工作流程ID。使用与 id 相同的 safe-id 格式；覆盖、运行和步骤被保留。

优先级
否
整数；默认为 10。较低的值具有较高的优先级并赢得冲突。缺失或无效的值会回退到 10。

启用
否
布尔值。默认为 true。禁用的叠加层将被忽略。

edits
是
编辑操作的非空列表。

编辑操作

操作
所需步骤
效果

insert_after
是
在锚步骤之后立即插入步骤。

insert_before
是
在锚步骤之前插入步骤。

替换
是
将锚步骤替换为步骤。

删除
no
从列表中删除锚点步骤。

锚点是基本工作流程中步骤的 ID。  锚点在 then、else、steps、cases.* 和 default 块内递归解析，因此也可以定位嵌套的基本步骤。  扇出模板（扇出步骤内的步骤）不是有效的锚点。

步骤 id 不得包含 : — 该字符是为引擎生成的嵌套 id 保留的。

叠加 CLI 命令

添加项目叠加

指定工作流程叠加添加 <path-to-overlay.yml> --priority <n>

验证覆盖文件并将其复制到 .specify/workflows/overlays/<extends>/<id>.yml。 --priority 默认为 10 并覆盖文件中的优先级字段。

列表叠加

指定工作流程覆盖列表 <workflow-id>

显示工作流的所有覆盖，按解析器优先级排序。禁用的叠加在列表中标记为禁用，并在工作流程解析期间被忽略。

改变优先级

指定工作流覆盖设置优先级 <workflow-id> <overlay-id> <n>

启用或禁用

指定工作流程覆盖禁用 <workflow-id> <overlay-id>
指定工作流程覆盖启用 <workflow-id> <overlay-id>

删除

指定工作流程叠加删除 <workflow-id> <overlay-id>

删除项目覆盖文件。

检查组合的工作流程

指定工作流程解析 <workflow-id>

打印图层堆栈（基础 + 覆盖）以及合成后每个步骤的源属性。  对于调试哪个覆盖贡献或覆盖了一个步骤很有用。

示例：实施后添加自动 Linting

给定内置的speckit工作流程，创建project-overlay.yml：

id：“add-lint”
扩展：“speckit”
优先级：10
编辑：
  - insert_after：实施
步骤：
 id：run-lint
 类型：shell
 运行：“ruff check src/”

安装它：

指定工作流程叠加添加 project-overlay.yml --priority 10

运行工作流程：

指定工作流程运行speckit -i spec =“构建看板”

组合的工作流程现在将运行完整的 SDD 循环，并在实施步骤后自动执行 ruff 检查 src/。

示例：更换门

id：“skip-plan-review”
扩展：“speckit”
优先级：5
编辑：
  - 替换：审查计划
步骤：
 id：审查计划
 类型：命令
 命令：speckit.plan
 输入：
 参数：“{{ inputs.spec }}”

优先级值越低，优先级越高。如果必须赢得与上面的 add-lint 覆盖的冲突，请将此覆盖更改为优先级：5。它用非交互式命令取代了审查计划门。

工作流程槽（上游扩展点）

工作流作者可以声明一个命名的、无操作的工作流槽，类型为：槽：

- id：实施后
类型：槽
 名称：“实施后检查”

步骤id是唯一的覆盖锚点；名称是必需的非空白，
仅供人类可读的标签。未填充的槽作为跳过的步骤完成，并输出 
：{slot: <name>}，因此后续步骤正常继续。

使用锚定在步骤 
id（而不是其名称）上的架构有效覆盖替换编辑来填充槽：

id: fill-post-implement
 扩展: my-workflow
 编辑:
  - 替换：实施后
步骤：
 id：实施后
 类型：shell
 运行：“echo 运行项目特定的检查”

当后面的表达式或扇入时，重用槽的 ID。wait_for 引用它。
替换还必须保留后续步骤消耗的每个输出键：
未填充的工作流槽仅提供步骤。<id>.output.slot。 fan-out.step 模板内不支持插槽步骤
，因为运行时倍增的
模板不能是覆盖锚点。

与捆绑包和更新的交互

指定工作流添加 <local-directory> 将完整的本地工作流 
 包安装到 中。specify/workflows/<id>/. 存档安装保留相同的 
 包内容。

刷新或重新安装已安装的工作流程时，.specify/workflows/overlays/<id>/ 中的项目覆盖将被保留，因为它们位于已安装的工作流程目录之外。

局限性

叠加仅在步骤列表上运行。  他们无法更改工作流元数据（名称、描述、输入、要求）或表达式逻辑。

扇出模板不能用作锚点。

当工作流解析时，针对基本工作流中不存在的步骤 ID 的覆盖将引发验证错误。

叠加层无法定位其他叠加层添加的步骤。

叠加无法添加新输入或更改基本工作流的输入架构。

更新工作流程

指定工作流程更新 [workflow_id]

将一个已安装的目录工作流程（或在未给出 ID 时将所有目录工作流程）更新为最新的目录版本。如果下载或验证失败，则提示确认并保留已安装的副本。

启用或禁用工作流程

指定工作流程启用 <workflow_id>
 指定工作流程禁用 <workflow_id>

已禁用的工作流程保持安装状态并列出（标记为 [已禁用]），但在重新启用之前拒绝运行。

删除工作流程

指定工作流程删除 <workflow_id>

从项目中删除已安装的工作流程。

搜索可用的工作流程

指定工作流程搜索[查询]

选项
说明

--tag
按标签过滤

--author
按作者筛选

在所有活动目录中搜索与查询匹配的工作流程。

工作流程信息

指定工作流程信息 <workflow_id>
 指定工作流程信息 <workflow_id> --versions

显示有关工作流程的详细信息，包括其步骤、输入和要求。
--versions 列出当前目录版本，后跟公布的历史
版本，并指示获胜目录是可安装的还是
仅供发现（不可安装）。当本地安装不同版本
时，它也可以工作。

目录管理

工作流目录控制在何处搜索和添加查找工作流。目录按优先顺序检查。

项目的.specify/workflow-catalogs.yml可以指向您未选择的目录进行添加和搜索。在从不熟悉的项目运行工作流之前，请运行指定工作流目录列表（并为其步骤可以引入的步骤目录指定工作流步骤目录列表） - 提供该配置的项目并不证明其工作流或步骤经过审查。维护者不审核运行字段；在运行工作流之前自行阅读工作流的 shell 步骤（请参阅谁维护工作流？）。

列出目录

指定工作流程目录列表

显示所有活动目录源。

添加目录

指定工作流程目录添加<url>

选项
说明

--name <name>
目录的可选名称

将自定义目录 URL 添加到项目的 .specify/workflow-catalogs.yml.

重新添加同名的相同工作流程或步骤目录URL即可成功，无需更改配置；不同的名称会被拒绝。

删除目录

指定工作流程目录删除 <index>

按目录列表中的索引删除目录。

目录解析顺序

目录按以下顺序解析（第一个匹配获胜）：

环境变量 - SPECKIT_WORKFLOW_CATALOG_URL 覆盖所有目录

项目配置 — .specify/workflow-catalogs.yml

用户配置 — ~/.specify/workflow-catalogs.yml

内置默认值——官方目录+社区目录

工作流程定义

工作流程在 YAML 文件中定义。以下是规格套件随附的内置完整 SDD 循环工作流程：

schema_version：“1.0”
工作流程：
 id：“speckit”
名称：“完整SDD周期”
版本： “1.0.1”
 作者：“GitHub”
 描述：“运行指定 → 计划 → 任务 → 使用审查门实施”

需要：
 speckit_version：">=0.8.5"
 集成：
 任何：
      - “阿尔奎米亚”
      - “克劳德”
      - "copilot"
      - “双子座”
      - "opencode"

输入：
 规格：
 类型：字符串
 必需：true
 提示：“描述您要构建的内容”
 集成：
 类型：字符串
 默认值： "auto"
 提示：“要使用的集成（例如 claude、copilot、gemini；'auto' 使用项目的初始化集成）”

步骤：
  - id: 指定
命令：speckit.specify
 集成：“{{ inputs.integration }}”
 输入：
 参数：“{{ inputs.spec }}”

  - id：审查规范
类型：gate
 消息：“在规划之前检查生成的规范。”
 选项：[批准、拒绝]
 on_reject：中止

  - 编号： 计划
命令：speckit.plan
 集成：“{{ inputs.integration }}”
 输入：
 参数：“{{ inputs.spec }}”

  - id：审查计划
类型：gate
 消息：“在生成任务之前检查计划。”
 选项：[批准、拒绝]
 on_reject：中止

  - id: 任务
命令：speckit.tasks
 集成：“{{ inputs.integration }}”
 输入：
 参数：“{{ inputs.spec }}”

  - id: 实施
命令：speckit.implement
 集成：“{{ inputs.integration }}”
 输入：
 参数：“{{ inputs.spec }}”

这会产生以下执行流程：

流程图 TB
 A["指定<br/>(命令)"] --> B{"review-spec<br/>(gate)"}
 B -- 批准 --> C["计划<br/>(命令)"]
 B -- 拒绝 --> X1["⏹ 中止"]
 C --> D{"review-plan<br/>(gate)"}
 D -- 批准 --> E["任务<br/>(命令)"]
 D -- 拒绝 --> X2["⏹ 中止"]
 E --> F["执行<br/>(命令)"]

样式 A 填充:#49a,颜色:#fff
 样式 B 填充:#a94,颜色:#fff
 样式 C 填充:#49a,颜色:#fff
 样式 D 填充:#a94,颜色:#fff
 样式 E填充:#49a,颜色:#fff
 样式 F 填充:#49a,颜色:#fff
 样式 X1 填充:#999,颜色:#fff
 样式 X2填充:#999,颜色:#fff

运行它：

指定工作流程运行speckit -i spec =“构建具有拖放任务管理的看板”

步骤类型

型号
用途

command
调用 Spec Kit 命令（例如，speckit.plan）

提示
发送任意提示到AI编码agent

shell
执行 shell 命令并捕获输出

init
引导项目（如指定init）

slot
命名工作流槽；未填时跳过

门
在继续之前暂停以供人工批准

if
条件分支 (then/else)

switch
表达式上的多分支调度

while
当条件为真时循环

do-while
至少执行一次，然后根据条件循环

fan-out
为列表中的每个项目分派一个步骤

扇入
扇出步骤的聚合结果

安全说明：shell 步骤使用您的权限运行本地命令。没有功能沙箱 - 需要的是咨询前置条件块（规范套件版本、集成），而不是运行时门，因此它不限制步骤可以执行的操作。特别是没有 require.permissions 功能门：它被验证拒绝，正是因为它意味着沙箱不存在。在运行任何目录或下载的工作流程之前对其进行审查，并使用门步骤在敏感或破坏性 shell 命令之前要求明确批准。

自定义步骤包

自定义步骤类型随指定工作流程步骤一起安装。步骤是一个包含元数据和可执行文件Python的
目录包：

my-step/
├── step.yml # 必需，在包根目录
├── __init__.py # 必需，在包根目录
└── helpers.py # 可选的嵌套模块和数据文件

step.yml 声明步骤的标识。步骤。type_key 必须与命令行上传递的 
<step_id> 完全匹配 - 永远不会从包 
 内容推断出 ID：

步骤：
 type_key：我的步骤
 名称：我的步骤
 版本：0.1.0
 作者：你
 描述：此步骤的作用

__init__.py 必须定义一个 StepBase 子类，其 type_key 匹配：

从 specify_cli.workflows.base 导入 StepBase，StepResult

类 MyStep(StepBase):
 type_key = "我的步骤"

    def execute(self, config, context):
        return StepResult(output={"ok": True})

从本地目录安装

指定工作流程步骤添加我的步骤 --dev /path/to/my-step

--dev 采用一个目录（不是存档，也不是裸露的 step.yml），该目录是 
 完整包。这不需要目录、服务器或网络，这使其成为
支持的本地创作循环：

指定工作流程步骤添加我的步骤 --dev ./my-step
指定工作流程步骤列表
指定工作流程步骤信息我的步骤
# 编辑./my-step，然后替换已安装的副本：
指定工作流程步骤 添加我的步骤 --dev ./my-step --force
指定工作流程步骤 删除我的步骤

从存档安装 URL

指定工作流程步骤添加我的步骤 --from https://example.com/my-step.zip

--from 接受 .zip、.tar.gz 或 .tgz 存档（裸 step.yml
URL 不是包）。存档可以将 step.yml 和 __init__.py
 放置在其根目录下或恰好在一个顶级目录下；不相关的顶级
兄弟姐妹被拒绝。由于步骤包包含可执行文件 Python，因此 
 直接 URL 安装会在任何网络 
 请求之前显示默认拒绝信任确认；拒绝取消，没有请求，也没有错误。 HTTPS 是必需的
（HTTP 仅允许环回主机），重定向必须保持安全，并且 
 下载有大小限制。

从目录安装

指定工作流程步骤添加我的步骤

目录安装会解析活动步骤目录中的各个文件 URL，然后
执行与 --dev 和 --from 相同的验证和提交路径。
无法安装仅发现目录。
目录发布历史记录

指定工作流程步骤信息 <id> --versions 列出获奖目录中当前和历史的 
 版本。指定工作流程步骤添加 <id> --version <v>
 选择确切的目录版本；如果没有 --version，则添加安装 
 公布的当前版本。请求的版本永远不会回退到
较低优先级目录，并且仅发现源仍然不可安装。
--version 无法与直接--dev 或--from 安装结合使用。

现有的单一版本目录条目继续有效。将当前的 
 版本、step_yml_url（或 url）、init_url 和可选文件 
 保留在顶层。可选的版本映射添加历史版本：

{
 "steps"：{
 "my-step"：{
 "version"：“2.0”，
 "step_yml_url": "https://example.com/my-step/2.0/step.yml",
 "releases": {
 "1.0": {
 "step_yml_url": “https://example.com/my-step/1.0/step.yml”，
 "init_url"：“https://example.com/my-step/1.0/__init__.py”，
 "extra_files"：{"helper.py": "https://example.com/my-step/1.0/helper.py"}，
 "sha256"： {
 "step.yml": "<64 十六进制数字>",
 "__init__.py": "<64 十六进制数字>",
 "helper.py": "<64 十六进制数字>"
 }
 }
 }
 }
 }
}

每个历史版本必须提供自己的步骤 URL 和 SHA-256 消化
每个下载的文件。 init_url 当派生自 a 时可以省略
step_yml_url 结束于 step.yml。要明确选择当前版本，
它的顶级条目同样必须提供每个文件的 sha256 映射。
特定于版本的文件和要求不是从当前版本继承的
释放。重复或格式错误的版本将被拒绝；同等 PEP 440
拼写如 v1.0 和 1.0 选择相同的广告版本。的
下载的 step.yml 必须声明所选步骤 ID 和版本。安
不合格的旧目录安装不需要摘要或步骤版本。
每个步骤只有一个版本 ID 可以一次安装；使用 --force 到
检查所选软件包后替换以前的安装。

更换及受力

指定工作流程步骤 添加我的步骤 --dev ./my-step --force
指定工作流程步骤 添加我的步骤 --from https://example.com/my-step.zip --force

--force 在接触 
 现有安装之前首先阶段并验证替换，并且可以替换已注册的安装和剩余的 
 未注册目录。验证和暂存失败使之前的 
 包保持不变。如果删除旧目录失败，则不会发布替换内容
。如果在删除
旧目录后发布替换或更新注册表失败，则安装可能不完整：
使用原始源重新运行命令并--force重新安装。未尝试
自动回滚。

包验证

在提交任何内容之前，每个源都会经过相同的验证：

step.yml 和 __init__.py 必须是包 
 根目录下的常规非符号链接文件。

包树以递归方式复制（支持相对导入、嵌套帮助器
模块和数据文件）。符号链接包根、任何
后代符号链接以及任何不是常规文件或
目录的文件系统对象都将被拒绝。

.git、__pycache__ 和 .DS_Store 条目将被跳过而不被
检查：它们不会被复制，排除的目录不会被输入，并且
它们不计入任何限制。

安装包策略允许最多 512 个保留条目（文件 
 和目录组合）、最多 32 级目录嵌套以及 
50 MiB 保留内容。

存档 URL 还会在包 
 验证之前通过 transport/extraction 安全限制：最多 512 个存档条目、下载或提取 50 MiB，以及每个存档成员 
10 MiB。目录文件具有 50 MiB 每个响应绑定。

安装验证并复制包，但不导入或
执行__init__.py。在任何特定的 
 自定义步骤必须执行之前，在工作流添加、工作流运行和工作流恢复的启动 
 期间加载已安装的自定义步骤模块。

安全说明：加载自定义步骤会使用您的 
 权限运行其 Python。仅安装和保留来自您信任的来源的步骤包。

列出、运行和删除

安装的自定义步骤出现在指定工作流程步骤列表中，并通过工作流程添加、工作流程运行和工作流程恢复自动加载
。删除
一个：

指定工作流程步骤删除我的步骤

注册表来源

每个安装的步骤仅记录其源的类型 - 目录 
（可选地包含目录名称）、本地或 url。本地路径和源 
URL 永远不会保留。指定工作流程步骤信息 <id> 显示源。

捆绑本地限制

捆绑包的provides.steps仍然只能通过活动步骤
目录解析。捆绑本地步骤/<id>/有效负载和相对
provides.steps[].source覆盖在此版本中未解决，因此
此类步骤无法离线安装。请参阅捆绑包参考。

每步集成配置

命令步骤可以将结构化运行时配置传递给
支持的集成：

- id: 实现-with-docker-agent
类型：命令
 命令：speckit.implement
 集成：docker-agent
 integration_args：
    - "{{ inputs.agent_config }}"
integration_options：
 agent：根
安全：平衡
模型：“openai/gpt-5”
输入：
参数： “{{ inputs.spec }}”

integration_args 是字符串的有序列表。 integration_options 是带有字符串键的 
 映射。两个字段中的值均通过工作流程
表达式机制解析，并通过所选集成进行验证；不支持、
未知或格式错误的值失败并出现可操作错误。 Docker Agent 使用 
 其单个位置参数作为 agent 配置参考，并接受 
agent 和安全性作为命名集成选项。通过
命令步骤的顶级模型字段配置其模型。

当 Docker Agent integration_args 为命令 
 步骤提供 agent 引用时，它优先于 SPECKIT_INTEGRATION_DOCKER_AGENT_EXTRA_ARGS；
 该步骤的整个旧环境值将被忽略。如果没有每步骤 
agent 引用，旧环境行为将保持不变。

解析的运行时配置记录在工作流运行状态中。当失败的 
 或暂停的命令恢复时，将根据当前输入重新解析完整的调度配置 
，因此工作流恢复 --input 提供的值一致地发挥 
 的作用。没有更新输入的简历会重现相同的值。

表达式

步骤可以使用 {{ expression }} 语法引用输入和上一步输出：

命名空间
描述

input.spec
工作流程输入值

steps.specify.output.file
上一步的输出

item
扇出迭代中的当前项

context.run_id
当前工作流程运行ID

context.workflow_dir
已解析工作流源目录的绝对路径。字符串加载工作流程的空字符串。

可用过滤器：default、join、contains、map、from_json、to_json、upper、lower、split、length。

过滤器
示例
行为

默认
{{ val \| default('fb') }}
无或空字符串的后备

join
{{ list \| join(', ') }}
将列表元素连接成字符串

包含
{{ text \| contains('sub') }}
子字符串或成员资格检查

map
{{ list \| map('attr') }}
从每个项目中提取属性

from_json
{{ out \| from_json }}
将 JSON 字符串解析为键入的值

to_json
{{ obj \| to_json }}
将值序列化为 JSON 字符串 — from_json 的逆；映射键必须是字符串

upper
{{ text \| upper }}
大写字符串

lower
{{ text \| lower }}
小写字符串

split
{{ csv \| split(',') }}
将分隔符上的字符串拆分为字符串列表

length
{{ items \| length }}
列表中的元素数或字符串中的字符数

默认情况下仅对 None 和空字符串进行回退。其他虚假值 - 0、false、[]、{} - 均按原样传递，因此 {{ count | default(10) }} 仍会产生零计数的 0。这里的 Falsy 与空不同。

关于新过滤器的注意事项：

类型是经过验证的，而不是强制的。 upper 和 lower 只接受字符串， split 需要字符串值和非空字符串分隔符， length 接受列表和字符串但拒绝映射。其他任何事情都会引发 ValueError 命名问题。故意不执行强制：类型不匹配几乎总是意味着工作流连接到错误的变量，并且强制结果会隐藏这一点。给定错误数量参数的过滤器（| upper('x')、| split with noseparator, | split(',', 1)) 被报告为已知过滤器误用，这与完全未知的过滤器名称不同：携带多个参数的调用会陷入相同的不支持格式错误，而不是评估为单个表达式。旧的过滤器（join、map、contains）更加宽松且未更改：join 将不支持的值字符串化，map/contains 返回后备而不是提升。

to_json 输出是确定性的。对象键已排序，非 ASCII 字符按原样保留而不是转义，因此相同的值始终序列化为相同的字节。这只能带来可重复性——它并不能让结果安全地通过 shell，因为插值不会添加引用或转义。请勿将不受约束的 JSON 插入运行字段。

to_json 拒绝非有限浮点数。 NaN、Infinity 和 -Infinity 提出 ValueError 命名 to_json，而不是序列化为裸令牌。这三个都不是有效的 JSON，因此发出它们将为符合标准的下游解析器提供一个必须拒绝的字符串。

to_json 需要字符串映射键。 JSON 对象具有字符串键，因此与任何其他键类型（{1: "a"}、{True: "a"}）的映射都会引发命名键类型的 ValueError。留给 json.dumps，整数键将被强制为“1”并与现有的“1”键冲突，而混合键类型将失败 sort_keys，排序 TypeError 仅报告为“不可 JSON- 可序列化”。

过滤器后的尾随比较将被拒绝。解析器在查找运算符之前会在顶级 | 上进行拆分，因此 {{ items | length > 0 }} 会引发而不是求值。在 length 运行之前，计数并不存在，因此无法编写该比较；支持的分支形式是过滤器自身在条件下的真实性：，因为对于空输入，长度返回 0：

条件: "{{ inputs.items | length }}" # 0 为 False，任何非零计数为 True

示例：

条件：“{{ steps.test.output.exit_code == 0 }}”
参数：“{{ inputs.spec }}”
消息：“{{ status | default('pending') }}”
tag_count： “{{ inputs.tags | split(',') | length }}”
shell_flag：“{{ inputs.branch | upper }}”

插值和外壳安全

表达式通过纯字符串替换来解析 - {{ ... }} 的值完全按原样拼接到周围的文本中，不添加任何引用或转义。这对于构建参数和消息字符串很方便，但它对 shell 步骤有一个重要的影响：运行字段被传递给系统 shell（/bin/sh -c on POSIX），因此任何内插值都被解释为 shell 语法，而不仅仅是数据。

如果插值可以包含 ;、|、&、$( )、反引号或引号等字符，则它可以更改或扩展实际运行的命令。当该值不完全处于工作流程作者的控制之下时，这一点最为重要：

工作流程输入。* — 由运行工作流程的人员提供。

前一步的输出，例如{{ steps.plan.output.stdout }} — 对于提示步骤，这是由 AI agent 生成的文本，该文本又可能受到 agent 读取的文件、票证或网页内容的影响。当 agent 输出流入 shell 步骤时，将其视为不受信任。

表达式语言中没有 shell 转义过滤器，shell 步骤周围没有沙箱，因此下面的任何实践都不能被视为消除敌对值的保证。唯一可靠的控制是限制插值的大小，并保留无法完全限制在运行字段之外的值。检查插入您无法控制的值的每个运行字段，并且至少：

使用 enum/an 白名单限制源处的值。当输入。* 提供运行字段时，将其限制为一组固定的已知安全值，以便调用者根本无法提供任意 shell 文本。这是引擎提供的最强大的控制——比任何下游缓解措施更喜欢它。

输入：
 目标：
 类型：字符串
 枚举：[staging，生产] # 调用者无法注入任意文本

保持不受约束的值不受限制。如果某个值无法限制在允许列表中（大多数 agent/prompt 输出），请勿将其插入运行字段。针对固定条件使用 if/switch 对其进行分支，或者在 command/prompt 步骤中对其进行操作，而不是根据它构建的 shell 命令。

引用不是安全边界。用引号 ('{{ inputs.x }}') 包围替换有助于 shell 将可信值视为单个参数并避免在空格上进行分词，但本身包含匹配引号字符的值仍然可以突破并注入 shell 语法。引用约束值的正确性；永远不要依赖引用来保证无约束替换的安全。

盖茨不检查下一步，并且消息被逐字打印。门步骤仅渲染其自己的 message/show_file - 它不会显示、解析或清理其后面的命令，并且批准永远不会中和可注入插值。不要将原始的不受信任的数据插入到消息中：它按原样打印，没有控制字符剥离，因此 agent 或调用者输出可能会注入 terminal/ANSI 转义，从而更改或隐藏批准提示。将消息保留到受信任的、受约束的文本，并通过 show_file 显示不受信任的材料以供审阅 — 其路径和内容在显示前为 control/ANSI-stripped。

shell 步骤在设计上是一个任意命令原语；这些做法可以减少暴露并保持哪个命令运行在作者的控制之下，但它们并不能消除插入您不完全控制的值的风险。

Shell 步骤环境变量

Shell 步骤自动接收以下环境变量：

变量
描述

SPECKIT_WORKFLOW_DIR
已解析的工作流源目录的绝对路径（与 {{ context.workflow_dir }} 的值相同）。当工作流没有源路径时不设置。

输入类型

类型
强制

字符串
传递

数字
“42”→42，“3.14”→3.14

布尔值
“真”/“1”/“是”→ True

状态和简历

每个工作流运行都将其状态保留在 .specify/workflows/runs/<run_id>/：

state.json — 当前运行状态和步骤进度

inputs.json — 已解析的输入值

log.jsonl — 逐步执行日志

这使得指定工作流程恢复能够从运行暂停（例如，在门处）或失败的确切步骤继续。

门判定输入

verdict_input 将门的判决绑定到指定的工作流程输入。输入必须在工作流的输入块中声明；指定工作流程验证报告未声明的参考。

扇出模板内部不支持 verdict_input。扇出项
共享工作流输入，而工作流状态只能代表一个暂停的
门。在扇出之前放置一个门以批准整个批次，或在
扇入之后放置一个门以检查汇总结果。

输入值语义：

值
行为

非空字符串，匹配一个选项（不区分大小写）
门自动决定； output.choice 设置为配置的选项拼写

非空字符串，不匹配
Gate立即失败

非字符串
门立即失败

TTY 上的 
 门提示缺失或为空；否则暂停

默认值语义：非空默认值被用作第一次运行时的判决 - 匹配选项会自动决定门，不匹配会立即失败。

输入：
 spec_verdict：
 类型：字符串
 默认值：“”
步骤：
  - id：审查规范
类型：gate
 消息：“批准规范？”
 选项：[批准、拒绝]
 on_reject：重试
 verdict_input： spec_verdict

恢复时提供判决：

指定工作流程恢复 <run_id> --input spec_verdict=批准

对于 on_reject：重试，在门 
 暂停之前消耗绑定拒绝判决：指定的存储输入重置为“”。因此，稍后的恢复
会再次提示或暂停，直到提供另一个判决。批准、中止和
跳过结果使输入保持不变。

由于该重置，与 on_reject 一起使用的判决输入：重试必须接受 
""。如果它声明一个枚举，请包含空字符串 - 否则重置 
 值会违反输入自己的枚举，并且无法再使用 
 任何输入恢复运行。指定工作流添加将其报告为验证错误。

输入：
 spec_verdict：
 类型：字符串
 枚举：[""，批准，拒绝]
 默认值：""

FAQ

当工作流程遇到关卡步骤时会发生什么？

工作流程暂停并等待人工输入。检查后运行指定工作流程恢复 <run_id> 以继续。

我可以多次运行相同的工作流程吗？

是的。每次运行都会获得一个唯一的 ID 及其自己的状态目录。使用指定工作流程状态来查看所有运行。

谁维护工作流程？

大多数工作流程都是由各自的作者独立创建和维护的。规范套件维护者不审查、审计、认可或支持工作流程代码。在自行决定安装和使用之前，请检查工作流程的源代码。

        
编辑本页
        

        

      

      
        
      
    

    

    
      
        
Spec Kit - 规范驱动的开发工具包


## 原文对照

<details>
<summary>展开完整原文</summary>

Workflows | Spec Kit Documentation 
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      

      

      

  

  
    
      
        
          
            
            Spec Kit
          
          
            
          
          
            
              
                
                
              
            
          
        
      
    

    

      
        

          
        

        

Workflows

Workflows automate multi-step Spec-Driven Development processes — chaining commands, prompts, shell steps, and human checkpoints into repeatable sequences. They support conditional logic, loops, fan-out/fan-in, and can be paused and resumed from the exact point of interruption.

Run a Workflow

specify workflow run <source>

Option
Description

-i / --input
Pass input values as key=value (repeatable)

--json
Emit the run outcome as a single JSON object

Runs a workflow from a catalog ID, URL, or local file path. Inputs declared by the workflow can be provided via --input or will be prompted interactively.

Example:

specify workflow run speckit -i spec="Build a kanban board with drag-and-drop task management"

With --json, a single machine-readable object is printed instead of formatted text (the default output is unchanged when the flag is omitted):

specify workflow run my-pipeline.yml --json

{
  "run_id": "662bf791",
  "workflow_id": "build-and-review",
  "status": "paused",
  "current_step_id": "review",
  "current_step_index": 0
}

workflow_id is the workflow.id declared inside the YAML, not the file name. The object is printed exactly as shown — pretty-printed with two-space indentation, on plain stdout with no Rich markup — so it always parses. While the workflow runs under --json, any progress a step would print (for example a gate prompt, or output from a prompt step's CLI subprocess) is redirected to stderr, so stdout carries only the JSON object. Read the object from stdout; leave stderr attached to the terminal or capture it separately.

For failed and aborted runs, the payload includes an error field carrying the terminal step's error message:

{
  "run_id": "662bf791",
  "workflow_id": "build-and-review",
  "status": "failed",
  "current_step_id": "boom",
  "current_step_index": 0,
  "error": "Command exited with code 3"
}

completed and paused runs omit the error field. The error is persisted in the run's state.json, so specify workflow status <run_id> --json surfaces the same message after the fact.

Note: Most workflow commands require a project already initialized with specify init. The exception is specify workflow run <local-file.{yml,yaml}>, which can run outside a project; in that case, run state is stored under the current directory's .specify/workflows/runs/<run_id>/.

Resume a Workflow

specify workflow resume <run_id>

Option
Description

-i / --input
Updated input values as key=value (repeatable)

--json
Emit the resume outcome as a single JSON object

Resumes a paused or failed workflow run from the exact step where it stopped. Useful after responding to a gate step or fixing an issue that caused a failure.

Supplied --input values are merged over the run's stored inputs and re-validated against the workflow's input types, then the blocked step is re-run with the updated values. This lets a run continue with information that only became available after it paused, or with a corrected value after a failure:

specify workflow resume <run_id> --input cmd="exit 0"

Workflow Status

specify workflow status [<run_id>]

Option
Description

--json
Emit run status (or the runs list) as a JSON object

Shows the status of a specific run, or lists all runs if no ID is given. Run states: created, running, completed, paused, failed, aborted.

List Installed Workflows

specify workflow list

Lists workflows installed in the current project.

Install a Workflow

specify workflow add <source>

Option
Description

--dev
Install from a local YAML file, package directory, or archive

--from <url>
Install from a custom URL (<source> names the expected workflow ID)

--version <version>
Install an exact advertised catalog release (<source> must be a workflow ID)

Installs a workflow from the catalog, an HTTPS URL, a local YAML file, a
directory containing workflow.yml, or a .zip, .tar.gz, or .tgz
archive. Archives may contain workflow.yml at the root or inside one
top-level directory.

Directory and archive installs preserve the complete workflow package,
including scripts and other companion files. ZIP, .tar.gz, and .tgz
archives follow the same validation and installation behavior.

Catalog entries keep the current release's version, url, optional sha256,
and optional requires at the top level. An optional releases mapping
advertises historical versions without changing what unqualified add,
search, info, or update select:

{
  "id": "example",
  "version": "2.0.0",
  "url": "https://example.com/example-2.0.0.zip",
  "releases": {
    "1.0.0": {
      "url": "https://example.com/example-1.0.0.zip",
      "sha256": "aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa",
      "requires": {"speckit_version": ">=1.0.0"}
    }
  }
}

Each historical release needs its own URL and SHA-256 digest; requires is
optional and, when present, must match the downloaded workflow definition.
Advertised versions use the workflow definition's X.Y.Z version format;
--version also accepts equivalent spellings such as v1.0 when selecting an
advertised 1.0.0 release.
The requested version must exist in the highest-priority catalog that provides
the workflow. A missing version does not fall back to another source, and
discovery-only catalogs cannot be installed from. The downloaded workflow ID,
version, and declared digest are verified before installation. --version does
not apply to local paths, direct URLs, or --from installations.

Workflow Overlays

Workflow overlays let a project extend or override an installed workflow without editing the installed workflow.yml.  This keeps local customizations safe across specify bundle update or specify workflow add upgrades.

When specify workflow run <workflow-id> loads a workflow, the engine composes the base workflow with all enabled overlays for that workflow id.  The result is validated like any other workflow definition.

How Overlays Work

An overlay is a YAML file that declares a set of edit operations against the step list of a base workflow. Overlays use lower-wins precedence: higher priority numbers are applied first and lower numbers last. Equal-priority overlays are applied alphabetically by ID, with the last ID winning conflicts.

Project overlay files live at:

Location
Purpose

.specify/workflows/overlays/<id>/*.yml
Project-local customizations

Overlay File Format

The recommended edit format uses the operation name as the key and the anchor step id as the value:

id: "my-overlay"
extends: "speckit"
priority: 10
enabled: true
edits:
  - insert_after: implement
    step:
      id: run-lint
      type: shell
      run: "ruff check src/"

  - replace: review-spec
    step:
      id: review-spec
      type: gate
      message: "Review the generated spec (overlay override)."
      options: [approve, reject]
      on_reject: abort

The explicit form is also supported:

edits:
  - operation: insert_after
    anchor: implement
    step:
      id: run-lint
      type: shell
      run: "ruff check src/"

Fields

Field
Required
Description

id
yes
Identifier for this overlay. Used in specify workflow overlay * commands. Must be lowercase letters, digits, and hyphens only; no dots, underscores, path separators, or overlays.

extends
yes
The workflow id this overlay applies to. Uses the same safe-id format as id; overlays, runs, and steps are reserved.

priority
no
Integer; defaults to 10. Lower values have higher precedence and win conflicts. Missing or invalid values fall back to 10.

enabled
no
Boolean. Defaults to true. Disabled overlays are ignored.

edits
yes
Non-empty list of edit operations.

Edit Operations

Operation
step required
Effect

insert_after
yes
Insert step immediately after the anchor step.

insert_before
yes
Insert step immediately before the anchor step.

replace
yes
Replace the anchor step with step.

remove
no
Remove the anchor step from the list.

The anchor is the id of a step in the base workflow.  Anchors are resolved recursively inside then, else, steps, cases.*, and default blocks, so nested base steps can also be targeted.  Fan-out templates (step inside a fan-out step) are not valid anchors.

Step ids must not contain : — that character is reserved for engine-generated nested ids.

Overlay CLI Commands

Add a Project Overlay

specify workflow overlay add <path-to-overlay.yml> --priority <n>

Validates the overlay file and copies it to .specify/workflows/overlays/<extends>/<id>.yml. --priority defaults to 10 and overrides the priority field in the file.

List Overlays

specify workflow overlay list <workflow-id>

Shows all overlays for the workflow, ordered by resolver precedence. Disabled overlays are marked as disabled in the listing and are ignored during workflow resolution.

Change Priority

specify workflow overlay set-priority <workflow-id> <overlay-id> <n>

Enable or Disable

specify workflow overlay disable <workflow-id> <overlay-id>
specify workflow overlay enable <workflow-id> <overlay-id>

Remove

specify workflow overlay remove <workflow-id> <overlay-id>

Removes the project overlay file.

Inspect the Composed Workflow

specify workflow resolve <workflow-id>

Prints the layer stack (base + overlays) and the source attribution for each step after composition.  Useful for debugging which overlay contributed or overrode a step.

Example: Adding Automated Linting after Implementation

Given the built-in speckit workflow, create project-overlay.yml:

id: "add-lint"
extends: "speckit"
priority: 10
edits:
  - insert_after: implement
    step:
      id: run-lint
      type: shell
      run: "ruff check src/"

Install it:

specify workflow overlay add project-overlay.yml --priority 10

Run the workflow:

specify workflow run speckit -i spec="Build a kanban board"

The composed workflow will now run the full SDD cycle and execute ruff check src/ automatically after the implement step.

Example: Replacing a Gate

id: "skip-plan-review"
extends: "speckit"
priority: 5
edits:
  - replace: review-plan
    step:
      id: review-plan
      type: command
      command: speckit.plan
      input:
        args: "{{ inputs.spec }}"

Lower priority values have higher precedence. Change this overlay to priority: 5 if it must win a conflict with the add-lint overlay above. It replaces the review-plan gate with a non-interactive command.

Workflow slots (upstream extension points)

Workflow authors can declare a named, no-op workflow slot with type: slot:

- id: post-implement
  type: slot
  name: "Post-implementation checks"

The step id is the unique overlay anchor; name is a required non-blank,
human-readable label only. An unfilled slot completes as a skipped step with
output: {slot: <name>}, so subsequent steps continue normally.

Fill a slot with a schema-valid overlay replace edit anchored on the step
id, not its name:

id: fill-post-implement
extends: my-workflow
edits:
  - replace: post-implement
    step:
      id: post-implement
      type: shell
      run: "echo Run project-specific checks"

Reuse the slot's id when later expressions or fan-in.wait_for refer to it.
The replacement must also preserve every output key those later steps consume:
an unfilled workflow slot supplies only steps.<id>.output.slot. Slot steps are
not supported inside fan-out.step templates because runtime-multiplied
templates cannot be overlay anchors.

Interaction with Bundles and Updates

specify workflow add <local-directory> installs the complete local workflow
package into .specify/workflows/<id>/. Archive installs preserve the same
package contents.

When an installed workflow is refreshed or reinstalled, project overlays in .specify/workflows/overlays/<id>/ are preserved because they live outside the installed workflow directory.

Limitations

Overlays operate on the step list only.  They cannot change workflow metadata (name, description, inputs, requires) or expression logic.

Fan-out templates cannot be used as anchors.

An overlay that targets a step id that does not exist in the base workflow will raise a validation error when the workflow is resolved.

Overlays cannot target steps added by other overlays.

Overlays cannot add new inputs or change the input schema of the base workflow.

Update Workflows

specify workflow update [workflow_id]

Updates one installed catalog workflow — or all of them when no ID is given — to the latest catalog version. Prompts for confirmation and keeps the installed copy if a download or validation fails.

Enable or Disable a Workflow

specify workflow enable <workflow_id>
specify workflow disable <workflow_id>

Disabled workflows stay installed and listed (marked [disabled]) but refuse to run until re-enabled.

Remove a Workflow

specify workflow remove <workflow_id>

Removes an installed workflow from the project.

Search Available Workflows

specify workflow search [query]

Option
Description

--tag
Filter by tag

--author
Filter by author

Searches all active catalogs for workflows matching the query.

Workflow Info

specify workflow info <workflow_id>
specify workflow info <workflow_id> --versions

Shows detailed information about a workflow, including its steps, inputs, and requirements.
--versions lists the current catalog version followed by advertised historical
versions and indicates whether the winning catalog is installable or
discovery-only (not installable). It also works when a different version is
installed locally.

Catalog Management

Workflow catalogs control where search and add look for workflows. Catalogs are checked in priority order.

A project's .specify/workflow-catalogs.yml can point add and search at a catalog you didn't choose. Before running a workflow from an unfamiliar project, run specify workflow catalog list (and specify workflow step catalog list for the step catalogs its steps can pull in) — a project supplying that config is not evidence its workflows or steps were vetted. Maintainers do not audit run fields; read a workflow's shell steps yourself before running it (see Who maintains workflows?).

List Catalogs

specify workflow catalog list

Shows all active catalog sources.

Add a Catalog

specify workflow catalog add <url>

Option
Description

--name <name>
Optional name for the catalog

Adds a custom catalog URL to the project's .specify/workflow-catalogs.yml.

Re-adding the same workflow or step catalog URL with the same name succeeds without changing the configuration; a different name is rejected.

Remove a Catalog

specify workflow catalog remove <index>

Removes a catalog by its index in the catalog list.

Catalog Resolution Order

Catalogs are resolved in this order (first match wins):

Environment variable — SPECKIT_WORKFLOW_CATALOG_URL overrides all catalogs

Project config — .specify/workflow-catalogs.yml

User config — ~/.specify/workflow-catalogs.yml

Built-in defaults — official catalog + community catalog

Workflow Definition

Workflows are defined in YAML files. Here is the built-in Full SDD Cycle workflow that ships with Spec Kit:

schema_version: "1.0"
workflow:
  id: "speckit"
  name: "Full SDD Cycle"
  version: "1.0.1"
  author: "GitHub"
  description: "Runs specify → plan → tasks → implement with review gates"

requires:
  speckit_version: ">=0.8.5"
  integrations:
    any:
      - "alquimia"
      - "claude"
      - "copilot"
      - "gemini"
      - "opencode"

inputs:
  spec:
    type: string
    required: true
    prompt: "Describe what you want to build"
  integration:
    type: string
    default: "auto"
    prompt: "Integration to use (e.g. claude, copilot, gemini; 'auto' uses the project's initialized integration)"

steps:
  - id: specify
    command: speckit.specify
    integration: "{{ inputs.integration }}"
    input:
      args: "{{ inputs.spec }}"

  - id: review-spec
    type: gate
    message: "Review the generated spec before planning."
    options: [approve, reject]
    on_reject: abort

  - id: plan
    command: speckit.plan
    integration: "{{ inputs.integration }}"
    input:
      args: "{{ inputs.spec }}"

  - id: review-plan
    type: gate
    message: "Review the plan before generating tasks."
    options: [approve, reject]
    on_reject: abort

  - id: tasks
    command: speckit.tasks
    integration: "{{ inputs.integration }}"
    input:
      args: "{{ inputs.spec }}"

  - id: implement
    command: speckit.implement
    integration: "{{ inputs.integration }}"
    input:
      args: "{{ inputs.spec }}"

This produces the following execution flow:

flowchart TB
    A["specify<br/>(command)"] --> B{"review-spec<br/>(gate)"}
    B -- approve --> C["plan<br/>(command)"]
    B -- reject --> X1["⏹ Abort"]
    C --> D{"review-plan<br/>(gate)"}
    D -- approve --> E["tasks<br/>(command)"]
    D -- reject --> X2["⏹ Abort"]
    E --> F["implement<br/>(command)"]

    style A fill:#49a,color:#fff
    style B fill:#a94,color:#fff
    style C fill:#49a,color:#fff
    style D fill:#a94,color:#fff
    style E fill:#49a,color:#fff
    style F fill:#49a,color:#fff
    style X1 fill:#999,color:#fff
    style X2 fill:#999,color:#fff

Run it with:

specify workflow run speckit -i spec="Build a kanban board with drag-and-drop task management"

Step Types

Type
Purpose

command
Invoke a Spec Kit command (e.g., speckit.plan)

prompt
Send an arbitrary prompt to the AI coding agent

shell
Execute a shell command and capture output

init
Bootstrap a project (like specify init)

slot
Named workflow slot; skipped when unfilled

gate
Pause for human approval before continuing

if
Conditional branching (then/else)

switch
Multi-branch dispatch on an expression

while
Loop while a condition is true

do-while
Execute at least once, then loop on condition

fan-out
Dispatch a step for each item in a list

fan-in
Aggregate results from a fan-out step

Security note: a shell step runs a local command with your privileges. There is no capability sandbox — requires is an advisory pre-condition block (spec-kit version, integrations), not a runtime gate, so it does not restrict what a step can do. In particular there is no requires.permissions capability gate: it is rejected by validation precisely because it would imply a sandbox that does not exist. Review any catalog or downloaded workflow before running it, and use a gate step to require explicit approval before sensitive or destructive shell commands.

Custom step packages

Custom step types are installed with specify workflow step. A step is a
directory package containing metadata and executable Python:

my-step/
├── step.yml        # required, at the package root
├── __init__.py     # required, at the package root
└── helpers.py      # optional nested modules and data files

step.yml declares the step's identity. step.type_key must exactly match the
<step_id> passed on the command line — the ID is never inferred from package
content:

step:
  type_key: my-step
  name: My Step
  version: 0.1.0
  author: you
  description: What this step does

__init__.py must define a StepBase subclass whose type_key matches:

from specify_cli.workflows.base import StepBase, StepResult

class MyStep(StepBase):
    type_key = "my-step"

    def execute(self, config, context):
        return StepResult(output={"ok": True})

Install from a local directory

specify workflow step add my-step --dev /path/to/my-step

--dev takes a directory (not an archive, not a bare step.yml) that is a
complete package. This needs no catalog, server, or network, which makes it the
supported local-authoring loop:

specify workflow step add my-step --dev ./my-step
specify workflow step list
specify workflow step info my-step
# edit ./my-step, then replace the installed copy:
specify workflow step add my-step --dev ./my-step --force
specify workflow step remove my-step

Install from an archive URL

specify workflow step add my-step --from https://example.com/my-step.zip

--from accepts a .zip, .tar.gz, or .tgz archive (a bare step.yml
URL is not a package). The archive may place step.yml and __init__.py
at its root or under exactly one top-level directory; unrelated top-level
siblings are rejected. Because a step package contains executable Python, a
direct URL install shows a default-deny trust confirmation before any network
request; declining cancels with no request and no error. HTTPS is required
(HTTP is permitted only for loopback hosts), redirects must remain secure, and
downloads are size-bounded.

Install from the catalog

specify workflow step add my-step

Catalog installs resolve individual file URLs from the active step catalogs and
then go through the same validation and commit path as --dev and --from.
Discovery-only catalogs cannot be installed from.
Catalog release history

specify workflow step info <id> --versions lists the current and historical
releases in the winning catalog. specify workflow step add <id> --version <v>
selects the exact catalog release; without --version, add installs the
advertised current release. The requested version never falls back to a
lower-priority catalog, and discovery-only sources remain non-installable.
--version cannot be combined with direct --dev or --from installs.

Existing single-version catalog entries continue to work. Keep the current
release's version, step_yml_url (or url), init_url, and optional files
at the top level. An optional releases mapping adds historical versions:

{
  "steps": {
    "my-step": {
      "version": "2.0",
      "step_yml_url": "https://example.com/my-step/2.0/step.yml",
      "releases": {
        "1.0": {
          "step_yml_url": "https://example.com/my-step/1.0/step.yml",
          "init_url": "https://example.com/my-step/1.0/__init__.py",
          "extra_files": {"helper.py": "https://example.com/my-step/1.0/helper.py"},
          "sha256": {
            "step.yml": "<64 hex digits>",
            "__init__.py": "<64 hex digits>",
            "helper.py": "<64 hex digits>"
          }
        }
      }
    }
  }
}

Each historical release must supply its own step URL and SHA-256 digest for
every downloaded file. init_url can be omitted when derived from a
step_yml_url ending in step.yml. To select the current version explicitly,
its top-level entry must likewise supply the per-file sha256 mapping.
Release-specific files and requirements are not inherited from the current
release. Repeated or malformed versions are rejected; equivalent PEP 440
spellings such as v1.0 and 1.0 select the same advertised release. The
downloaded step.yml must declare the selected step ID and version. An
unqualified legacy catalog install does not require digests or a step version.
Only one version of each step ID can be installed at a time; use --force to
replace a previous installation after reviewing the selected package.

Replacement and force

specify workflow step add my-step --dev ./my-step --force
specify workflow step add my-step --from https://example.com/my-step.zip --force

--force first stages and validates the replacement before touching the
existing installation, and can replace both a registered install and a leftover
unregistered directory. Validation and staging failures leave the previous
package untouched. If removing the old directory fails, the replacement is not
published. If publishing the replacement or updating the registry fails after
the old directory has been removed, the installation may be left incomplete:
rerun the command with the original source and --force to reinstall. No
automatic rollback is attempted.

Package validation

Every source is validated identically before anything is committed:

step.yml and __init__.py must be regular, non-symlink files at the package
root.

The package tree is copied recursively (relative imports, nested helper
modules, and data files are supported). A symlinked package root, any
descendant symlink, and any filesystem object that is not a regular file or
directory are rejected.

.git, __pycache__, and .DS_Store entries are skipped without being
inspected: they are not copied, excluded directories are not entered, and
they do not count toward any limit.

The installed-package policy permits at most 512 retained entries (files
and directories combined), at most 32 levels of directory nesting, and
50 MiB of retained content.

Archive URLs also pass transport/extraction safety limits before package
validation: at most 512 archive entries, 50 MiB downloaded or extracted, and
10 MiB per archive member. Catalog files have a 50 MiB per-response bound.

Installation validates and copies the package but does not import or
execute __init__.py. Installed custom step modules are loaded during startup
of workflow add, workflow run, and workflow resume, before any particular
custom step necessarily executes.

Security note: Loading a custom step runs its Python with your
privileges. Only install and retain step packages from sources you trust.

Listing, running, and removing

Installed custom steps appear in specify workflow step list and are loaded
automatically by workflow add, workflow run, and workflow resume. Remove
one with:

specify workflow step remove my-step

Registry provenance

Each installed step records only the kind of its source — catalog
(optionally with the catalog name), local, or url. Local paths and source
URLs are never persisted. specify workflow step info <id> shows the source.

Bundle-local limitation

A bundle's provides.steps still resolves only through the active step
catalogs. Bundle-local steps/<id>/ payloads and relative
provides.steps[].source overrides are not resolved in this release, so
such steps are not installable offline. See the Bundles reference.

Per-Step Integration Configuration

Command steps may pass structured runtime configuration to integrations that
support it:

- id: implement-with-docker-agent
  type: command
  command: speckit.implement
  integration: docker-agent
  integration_args:
    - "{{ inputs.agent_config }}"
  integration_options:
    agent: root
    safety: balanced
  model: "openai/gpt-5"
  input:
    args: "{{ inputs.spec }}"

integration_args is an ordered list of strings. integration_options is a
mapping with string keys. Values in both fields are resolved with the workflow
expression mechanism and validated by the selected integration; unsupported,
unknown, or malformed values fail with an actionable error. Docker Agent uses
its single positional argument as the agent configuration reference and accepts
agent and safety as named integration options. Configure its model through
the command step's top-level model field.

When Docker Agent integration_args supplies an agent reference for a command
step, it takes precedence over SPECKIT_INTEGRATION_DOCKER_AGENT_EXTRA_ARGS;
the entire legacy environment value is ignored for that step. Without a per-step
agent reference, the legacy environment behavior is unchanged.

Resolved runtime configuration is recorded in workflow run state. When a failed
or paused command is resumed, the complete dispatch configuration is re-resolved
from the current inputs, so values supplied with workflow resume --input take
effect consistently. A resume without updated inputs reproduces the same values.

Expressions

Steps can reference inputs and previous step outputs using {{ expression }} syntax:

Namespace
Description

inputs.spec
Workflow input values

steps.specify.output.file
Output from a previous step

item
Current item in a fan-out iteration

context.run_id
Current workflow run ID

context.workflow_dir
Resolved absolute path to the workflow source directory. Empty string for string-loaded workflows.

Available filters: default, join, contains, map, from_json, to_json, upper, lower, split, length.

Filter
Example
Behavior

default
{{ val \| default('fb') }}
Fallback for None or an empty string

join
{{ list \| join(', ') }}
Join list elements into a string

contains
{{ text \| contains('sub') }}
Substring or membership check

map
{{ list \| map('attr') }}
Extract an attribute from each item

from_json
{{ out \| from_json }}
Parse a JSON string into a typed value

to_json
{{ obj \| to_json }}
Serialize a value to a JSON string — the inverse of from_json; mapping keys must be strings

upper
{{ text \| upper }}
Uppercase a string

lower
{{ text \| lower }}
Lowercase a string

split
{{ csv \| split(',') }}
Split a string on a separator into a list of strings

length
{{ items \| length }}
Number of elements in a list, or characters in a string

default falls back only for None and the empty string. Other falsy values — 0, false, [], {} — are passed through unchanged, so {{ count | default(10) }} still yields 0 for a zero count. Falsy is not the same as empty here.

Notes on the newer filters:

Types are validated, not coerced. upper and lower accept strings only, split requires both a string value and a non-empty string separator, and length accepts lists and strings but rejects mappings. Anything else raises a ValueError naming the problem. Coercion is deliberately not performed: a type mismatch nearly always means the workflow is wired to the wrong variable, and a coerced result would hide that. A filter given the wrong number of arguments (| upper('x'), | split with no separator, | split(',', 1)) is reported as a known filter misused, which is distinct from an entirely unknown filter name: a call carrying more than one argument falls through to that same unsupported-form error rather than being evaluated as a single expression. The older filters (join, map, contains) are more permissive and unchanged: join stringifies unsupported values and map/contains return fallbacks rather than raising.

to_json output is deterministic. Object keys are sorted and non-ASCII characters are left as-is rather than escaped, so the same value always serializes to the same bytes. That buys reproducibility only — it does not make the result safe to pass through a shell, because interpolation adds no quoting or escaping. Do not interpolate unconstrained JSON into a run field.

to_json rejects non-finite floats. NaN, Infinity, and -Infinity raise a ValueError naming to_json instead of serializing to bare tokens. None of the three is valid JSON, so emitting them would hand a standards-compliant downstream parser a string it must reject.

to_json requires string mapping keys. JSON objects have string keys, so a mapping with any other key type — {1: "a"}, {True: "a"} — raises a ValueError naming the key type instead. Left to json.dumps, an integer key would be coerced to "1" and collide with an existing "1" key, while mixed key types would fail sort_keys with an ordering TypeError reported only as "not JSON-serializable".

Trailing comparisons after a filter are rejected. The parser splits on the top-level | before looking for operators, so {{ items | length > 0 }} raises rather than evaluating. The count does not exist until length runs, so there is no way to write that comparison; the supported branching form is the filter's own truthiness in a condition:, since length returns 0 for an empty input:

condition: "{{ inputs.items | length }}"   # 0 is False, any non-zero count is True

Example:

condition: "{{ steps.test.output.exit_code == 0 }}"
args: "{{ inputs.spec }}"
message: "{{ status | default('pending') }}"
tag_count: "{{ inputs.tags | split(',') | length }}"
shell_flag: "{{ inputs.branch | upper }}"

Interpolation and shell safety

Expressions are resolved by plain string substitution — the value of {{ ... }} is spliced into the surrounding text exactly as-is, with no quoting or escaping added. That is convenient for building args and message strings, but it has an important consequence for shell steps: a run field is handed to the system shell (/bin/sh -c on POSIX), so any interpolated value is interpreted as shell syntax, not just data.

If an interpolated value can contain characters like ;, |, &, $( ), backticks, or quotes, it can change or extend the command that actually runs. This matters most when the value is not fully under the workflow author's control:

Workflow inputs.* — supplied by whoever runs the workflow.

A prior step's output, e.g. {{ steps.plan.output.stdout }} — for a prompt step this is text produced by the AI agent, which can in turn be influenced by files, tickets, or web content the agent read. Treat agent output as untrusted when it flows into a shell step.

There is no shell-escaping filter in the expression language and no sandbox around a shell step, so none of the practices below can be treated as a guarantee that a hostile value is neutralised. The only reliable control is to constrain what an interpolated value can be, and to keep values you cannot constrain out of run fields entirely. Scrutinise every run field that interpolates a value you do not control, and at minimum:

Constrain the value at the source with enum/an allowlist. When inputs.* feeds a run field, restrict it to a fixed set of known-safe values so a caller cannot supply arbitrary shell text at all. This is the strongest control the engine offers — prefer it over any downstream mitigation.

inputs:
  target:
    type: string
    enum: [staging, production]   # caller cannot inject arbitrary text

Keep unconstrained values out of run. If a value cannot be constrained to an allowlist — most agent/prompt output — do not interpolate it into a run field. Branch on it with if/switch against fixed conditions, or act on it in a command/prompt step rather than a shell command built from it.

Quoting is not a security boundary. Surrounding a substitution with quotes ('{{ inputs.x }}') helps the shell treat a trusted value as a single argument and avoids word-splitting on spaces, but a value that itself contains the matching quote character can still break out and inject shell syntax. Quote for correctness on constrained values; never rely on quoting to make an unconstrained substitution safe.

Gates do not inspect the next step, and message is printed verbatim. A gate step renders only its own message/show_file — it does not display, resolve, or sanitise the command that follows it, and approval never neutralises an injectable interpolation. Do not interpolate raw untrusted data into message: it is printed as-is with no control-character stripping, so agent or caller output could inject terminal/ANSI escapes that alter or hide the approval prompt. Keep message to trusted, constrained text, and surface untrusted material for review via show_file instead — its path and contents are control/ANSI-stripped before display.

A shell step is an arbitrary-command primitive by design; these practices reduce exposure and keep which command runs under the author's control, but they do not eliminate the risk of interpolating values you do not fully control.

Shell Step Environment Variables

Shell steps automatically receive the following environment variables:

Variable
Description

SPECKIT_WORKFLOW_DIR
Resolved absolute path to the workflow source directory (same value as {{ context.workflow_dir }}). Not set when the workflow has no source path.

Input Types

Type
Coercion

string
Pass-through

number
"42" → 42, "3.14" → 3.14

boolean
"true" / "1" / "yes" → True

State and Resume

Each workflow run persists its state at .specify/workflows/runs/<run_id>/:

state.json — current run state and step progress

inputs.json — resolved input values

log.jsonl — step-by-step execution log

This enables specify workflow resume to continue from the exact step where a run was paused (e.g., at a gate) or failed.

Gate Verdict Inputs

verdict_input binds a gate's verdict to a named workflow input. The input must be declared in the workflow's inputs block; specify workflow validate reports an undeclared reference.

verdict_input is not supported inside a fan-out template. Fan-out items
share workflow inputs, while workflow state can represent only one paused
gate. Place a gate before the fan-out to approve the whole batch, or after a
fan-in to review the aggregated results.

Input value semantics:

Value
Behavior

Non-empty string, matches an option (case-insensitive)
Gate auto-decides; output.choice is set to the configured option spelling

Non-empty string, no match
Gate fails immediately

Non-string
Gate fails immediately

Missing or empty
Gate prompts on a TTY; pauses otherwise

Default value semantics: A non-empty default is consumed as a verdict on the first run — matching an option auto-decides the gate, not matching fails it immediately.

inputs:
  spec_verdict:
    type: string
    default: ""
steps:
  - id: review-spec
    type: gate
    message: "Approve the specification?"
    options: [approve, reject]
    on_reject: retry
    verdict_input: spec_verdict

Supply a verdict when resuming:

specify workflow resume <run_id> --input spec_verdict=approve

For on_reject: retry, a bound reject verdict is consumed before the gate
pauses: the named stored input is reset to "". A later resume therefore
prompts or pauses again until another verdict is supplied. Approve, abort, and
skip outcomes leave the input unchanged.

Because of that reset, a verdict input used with on_reject: retry must accept
"". If it declares an enum, include the empty string — otherwise the reset
value violates the input's own enum and the run can no longer be resumed with
any input. specify workflow add reports this as a validation error.

inputs:
  spec_verdict:
    type: string
    enum: ["", approve, reject]
    default: ""

FAQ

What happens when a workflow hits a gate step?

The workflow pauses and waits for human input. Run specify workflow resume <run_id> after reviewing to continue.

Can I run the same workflow multiple times?

Yes. Each run gets a unique ID and its own state directory. Use specify workflow status to see all runs.

Who maintains workflows?

Most workflows are independently created and maintained by their respective authors. The Spec Kit maintainers do not review, audit, endorse, or support workflow code. Review a workflow's source before installing and use at your own discretion.

        
          Edit this page
        

        

      

      
        
      
    

    

    
      
        
          Spec Kit - A specification-driven development toolkit


</details>
