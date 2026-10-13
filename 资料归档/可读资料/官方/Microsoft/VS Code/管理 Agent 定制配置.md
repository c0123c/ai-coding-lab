# 管理 Agent 定制配置

说明跨工作区和用户配置创建、管理、迁移与排查定制项的方式，帮助维持配置一致性。

- 来源：Microsoft／VS Code
- 资料类型：存档文档
- 文档时间：官方审核：[2026-10-07](<https://code.visualstudio.com/docs/agent-customization/overview>)；官方文件提交（截至抓取）：[2026-10-07](<https://github.com/microsoft/vscode-docs/commit/33d3464fea2225e5e495e276ed6364fca3d2da32>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://code.visualstudio.com/docs/agent-customization/overview>) · [原始文件](<../../../../原始抓取备份/原文/官方/Microsoft/VS Code/管理 Agent 定制配置.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

创建和管理 agent 自定义项

	

	
	
	
	
	

	
		AI agents：请参阅 llms.txt 以了解文档发现和导航。文档文章也可以通过将 .md 附加到文章 URL 中以 Markdown 形式提供。
	
	
	

	
		
			
						
					
						
							Visual Studio Code
						
						
							
								

									
										特点
									
									
										
Agents
										
Agent 定制
										
模型和 Agent 安全带
										
企业
										
可扩展性
									
								
								

									
										文档
									
									
										
文档
										
API
										
FAQ
									
								
								
发行说明
								
								
博客
								
学习
								
活动
								

									
										资源
									
									
										
扩展
										
MCP
									
								
							
							
								

									
										下载
									
								
							
						
						
							
								
									
										
											
												
												
											
										
										
										搜索
										
									
													
							
								
								
							
							
								下载
							
						
						
							
							
							
						
					
				
			
				
			
				
🎬 观看VS Code的故事！
				
VS Code 现已在 Instagram 上发布，请关注 @vscode.ig 以获取更新、提示等。
				
重新观看 GitHub Copilot 日。
			
			关闭此更新
		
		
		
		
		
			
    
        
        
            
              
              
文档
              
                
                

概述
                
            
                
            

开始使用
              
                
                

概述
                
            
                

Agents 快速入门
                
            
                

编辑器教程
                
            
                

介绍视频
                
            
              
            
            
            

              Agents
              
                
                

AI 概述
                
            
            

开始使用
              
                
                

Agents 快速入门
                
            
                

寻找指南
                
            
                

设置 GitHub Copilot
                
            
                

Agents教程
                
            
                

最佳实践
                
            
              
            
            
            

概念
              
                
                

                  Agents
                
            
                

模型如何工作
                
            
                

背景
                
            
                

工具
                
            
                

会话和聊天
                
            
                

信任与安全
                
            
                

Agent 定制
                
            
                

Agent 安全带
                
            
                

工作空间上下文
                
            
                

Agent 主机架构
                
            
              
            
            
            

使用Agents
              
                
                

聊天视图和布局
                
            
                

Agents 窗口
                
            
                

选择 Agent 安全带
                
            
                

配置Agents窗口
                
            
                

聊天交互控件
                
            
            

计划和运行任务
              
                
                

计划工作
                
            
                

使用工具
                
            
                

浏览器工具
                
            
                

子代理
                
            
                

自动化
                
            
              
            
            
            

会议和结果
              
                
                

管理会话
                
            
                

会话历史记录
                
            
                

远程 Agent 会话
                
            
                

内存
                
            
                

查看并恢复更改
                
            
                

文物
                
            
              
            
            
            

权限和安全
              
                
                

批准和许可
                
            
                

Agent 沙箱
                
            
                

AI 安全
                
            
              
            
            
              
            
            
            

定制【agents】
              
                
                

创建和管理
                
            
                

使用说明
                
            
                

                  Agent Skills
                
            
                

定制【Agents】
                
            
                

选择和配置模型
                
            
                

                  MCP
                
            
                

挂钩
                
            
                

插件
                
            
                

提示文件
                
            
                

迁移现有自定义
                
            
              
            
            
            

教程和指南
              
                
                

探索代码库
                
            
                

添加功能
                
            
                

修复一个错误
                
            
                

安全重构
                
            
                

添加测试
                
            
                

测试网络应用程序
                
            
                

运行并行任务
                
            
                

回到正轨
                
            
                

使 Agents 适应您的项目
                
            
                

情境工程
                
            
                

测试驱动开发
                
            
                

Jupyter 笔记本
                
            
                

减少信贷使用
                
            
                

提示示例
                
            
              
            
            
            

参考
              
                
                

备忘单
                
            
                

工具和背景
                
            
                

设置参考
                
            
                

VS Code 宠物
                
            
                

MCP 配置
                
            
                

钩子参考
                
            
                

OpenTelemetry 监控
                
            
              
            
            
            

故障排除
              
                
                

故障排除
                
            
                

调试聊天交互
                
            
                

诊断提示缓存
                
            
                

                  FAQ
                
            
              
            
            
              
            
            
            

聊天
              
                
                

聊天交互控件
                
            
                

内联和快速聊天
                
            
                

添加提示上下文
                
            
              
            
            
            

编辑
              
                
                

概述
                
            
            

开始使用
              
                
                

编辑器教程
                
            
                

用户界面
                
            
                

提示和技巧
                
            
              
            
            
            

编写代码
              
                
                

基础编辑
                
            
                

                  IntelliSense
                
            
                

内嵌建议
                
            
                

聪明的行动
                
            
                

代码导航
                
            
                

重构
                
            
                

片段
                
            
            

工作空间
              
                
                

概述
                
            
                

多根工作区
                
            
                

工作空间信托
                
            
              
            
            
              
            
            
            

配置编辑器
              
                
                

显示语言
                
            
                

布局
                
            
                

键盘快捷键
                
            
                

设置
                
            
                

设置同步
                
            
            

扩展
              
                
                

概述
                
            
                

扩展市场
                
            
                

扩展运行时安全性
                
            
              
            
            
                

主题
                
            
                

型材
                
            
            

无障碍
              
                
                

概述
                
            
                

语音交互
                
            
              
            
            
                

命令行界面
                
            
                

遥测
                
            
              
            
            
            

参考
              
                
                

默认键盘快捷键
                
            
                

默认设置
                
            
                

替代变量
                
            
                

任务架构
                
            
              
            
            
              
            
            
            

源代码控制
              
                
                

概述
                
            
                

快速入门
                
            
                

存储库和遥控器
                
            
                

暂存、提交和撤消
                
            
                

历史与指责
                
            
                

分支、储藏室和工作树
                
            
                

合并冲突
                
            
                

协作 GitHub
                
            
                

故障排除
                
            
                

                  FAQ
                
            
              
            
            
            

终端
              
                
                

开始使用
                
            
                

终端基础知识
                
            
                

终端简介
                
            
                

外壳集成
                
            
                

外观
                
            
                

高级
                
            
              
            
            
            

调试与测试
              
                
                

调试
                
            
                

调试配置
                
            
                

任务
                
            
                

测试
                
            
                

集成浏览器
                
            
                

端口转发
                
            
            

指南和教程
              
                
                

测试驱动开发
                
            
                

测试网络应用程序
                
            
              
            
            
              
            
            
            

企业
              
                
                

概述
                
            
                

企业政策
                
            
                

管理 AI 设置
                
            
                

扩展
                
            
                

遥测
                
            
                

更新
                
            
              
            
            
            

远程
              
                
                

概述
                
            
                

VS Code 网页版
                
            
                

                  SSH
                
            
                

SSH教程
                
            
                

隧道
                
            
                

开发容器
                
            
                

                  WSL
                
            
                

WSL教程
                
            
                

GitHub 代码空间
                
            
                

VS Code 服务器
                
            
                

Linux 先决条件
                
            
                

提示和技巧
                
            
                

                  FAQ
                
            
              
            
            
            

高级设置
              
                
                

GitHub Copilot 设置
                
            
                

Linux
                
            
                

                  macOS
                
            
                

窗户
                
            
                

树莓派
                
            
                

网络
                
            
                

便携模式
                
            
                

附加组件
                
            
                

卸载
                
            
              
            
            
                

语言和运行时
                
            
                

扩展文档
                
            
              
            
            
主题
              
                
                
概述
            
                
概述
            
Agents 快速入门
            
编辑器教程
            
介绍视频
            
AI 概述
 开始使用
     Agents 快速入门
            
    寻找指南
            
    设置 GitHub Copilot
            
    Agents教程
            
    最佳实践
 概念
     Agents
            
    模型如何工作
            
    背景
            
    工具
            
    会话和聊天
            
    信任与安全
            
    Agent 定制
            
    Agent 安全带
            
    工作空间上下文
            
    Agent 主机架构
 使用 Agents
     聊天视图和布局
            
    Agents 窗口
            
    选择 Agent 安全带
            
    配置Agents窗口
            
    聊天交互控件
     计划和运行任务
         计划工作
            
        使用工具
            
        浏览器工具
            
        子代理
            
        自动化
     会话和结果
         管理会话
            
        会话历史记录
            
        远程 Agent 会话
            
        内存
            
        查看并恢复更改
            
        工件
     权限和安全
         批准和权限
            
        Agent 沙箱
            
        AI 安全性
 自定义 agents
     创建和管理
            
    使用说明
            
                    Agent Skills
            
    定制【Agents】
            
    选择和配置模型
            
                    MCP
            
    挂钩
            
    插件
            
    提示文件
            
    迁移现有自定义
 教程和指南
     探索代码库
            
    添加功能
            
    修复一个错误
            
    安全重构
            
    添加测试
            
    测试网络应用程序
            
    运行并行任务
            
    回到正轨
            
    使 Agents 适应您的项目
            
    情境工程
            
    测试驱动开发
            
    Jupyter 笔记本
            
    减少信贷使用
            
    提示示例
 参考
     备忘单
            
    工具和背景
            
    设置参考
            
    VS Code 宠物
            
    MCP 配置
            
    钩子参考
            
    OpenTelemetry 监控
 故障排除
     故障排除
            
    调试聊天交互
            
    诊断提示缓存
            
                    FAQ
            
聊天交互控件
            
内联和快速聊天
            
添加提示上下文
            
概述
 入门
     编辑器教程
            
    用户界面
            
    提示和技巧
 编写代码
     基本编辑
            
                    IntelliSense
            
    内嵌建议
            
    聪明的行动
            
    代码导航
            
    重构
            
    片段
     工作区
         概述
            
        多根工作区
            
        Workspace Trust
 配置编辑器
     显示语言
            
    布局
            
    键盘快捷键
            
    设置
            
    设置同步
     扩展
         概述
            
        扩展市场
            
        扩展运行时安全性
            
    主题
            
    配置文件
     辅助功能
         概述
            
        语音交互
            
    命令行界面
            
    遥测
 参考
     默认键盘快捷键
            
    默认设置
            
    替代变量
            
    任务架构
            
概述
            
快速入门
            
存储库和遥控器
            
暂存、提交和撤消
            
历史与指责
            
分支、储藏室和工作树
            
合并冲突
            
协作 GitHub
            
故障排除
            
                FAQ
            
开始使用
            
终端基础知识
            
终端简介
            
外壳集成
            
外观
            
高级
            
调试
            
调试配置
            
任务
            
测试
            
集成浏览器
            
端口转发
 指南和教程
     测试驱动开发
            
    测试网络应用程序
            
概述
            
企业政策
            
管理 AI 设置
            
扩展
            
遥测
            
更新
            
概述
            
VS Code 网页版
            
                SSH
            
SSH教程
            
隧道
            
开发容器
            
                WSL
            
WSL教程
            
GitHub 代码空间
            
VS Code 服务器
            
Linux 先决条件
            
提示和技巧
            
                FAQ
            
GitHub Copilot 设置
            
Linux
            
                macOS
            
窗户
            
树莓派
            
网络
            
便携模式
            
附加组件
            
卸载
            
语言和运行时
            
扩展文档
            
              
                    
        
        
        
            
            
                
                    
                    
本页有9sectionsOn此页
                    
                        
                        
Agent 自定义编辑器
                        
                        
选择定制范围
                        
                        
创建定制
                        
                        
管理现有的定制
                        
                        
验证定制
                        
                        
解决自定义问题
                        
                        
在单一存储库中使用自定义
                        
                        
评估和改进自定义文件（预览）
                        
                        
相关资源
                        
                    
                    
                
                
                
                    

                        
相关
                    
                    
                    
                    
核心理念
                        
                        
了解不同的自定义类型以及何时使用每种类型。
                        
                        
                            
                            
                            
定制理念
                            
                            
                            
                        
                    
                    
                    
教程
                        
                        
按照实践演练为您的项目自定义 AI。
                        
                        
                            
                            
                            
为您的项目定制AI
                            
                            
                            
                        
                    
                    
                
                
            
            
            
            
                
                
创建和管理 agent 自定义项
                
                

	
		
			
			复制为 Markdown
		
		
			
		
		
			

				
					
					复制为 Markdown
				
			
			

				
					
					查看为 Markdown
					
				
			
			

			

				
					
					询问VS Code
				
			
			

				
					
					在 VS Code 业内人士中询问
				
			
		
	
	

                
                
                

Agent 自定义使 agents 适应您的编码标准、工作流程和开发系统。本文介绍如何在 Visual Studio Code 中创建、管理、评估自定义项并对其进行故障排除。要比较自定义类型并了解它们如何协同工作，请参阅 Agent 自定义概念。

Agent 自定义编辑器

Agent 自定义编辑器提供了一个发现、创建和管理自定义的中心位置。它将自定义类型组织成单独的部分，并包括具有语法突出显示和验证功能的编辑器。

当您打开自定义编辑器时，自定义的范围仅限于选定的 agent 线束。在打开编辑器之前，从聊天输入的下拉列表中选择线束，以确保自定义应用到正确的上下文。

按照以下步骤打开 Agent 自定义编辑器：
Agents windowChat 视图
在左侧边栏中，在“自定义”面板中选择自定义类型。

在聊天视图中，选择配置聊天（齿轮图标）或运行聊天：从命令面板打开自定义 (⇧⌘P (Windows、Linux Ctrl+Shift+P)）。

发现市场定制（实验性）

定制市场提供了一个统一的发现页面，用于从配置的源查找技能、MCP 服务器、插件和其他 agent 定制。要使用市场，请打开
    
      
chat.customizations.marketplace.enabled
      
    
    
        
    
    
在 VS Code
 中打开 在 VS Code 内部人员中打开
    
设置。

启用市场后，GitHub Feed 源默认可用。使用
    
      
chat.customizations.marketplace.sources.publicFeed.enabled
      
    
    
        
    
    
在 VS Code
 中打开 在 VS Code 内部人员中打开
    
设置显示或隐藏此源。

要查找并安装自定义：

打开 Agent 自定义编辑器并选择发现。

使用源菜单浏览所有源或选择特定源，例如 GitHub Feed。

按名称搜索或使用类型过滤器，例如@type:skill 或@type:mcp。

选择一个项目并查看其发布者、来源、安装详细信息以及任何设置要求。

选择安装并在出现提示时选择目标位置。

有些项目可供发现，但不支持自动安装。请按照发布者提供的设置说明进行操作。

市场项目在 Discover 中显示源或发布者图像，如果可用，则在已安装的项目及其详细信息页面上显示。使用此信息和项目详细信息在安装前验证来源。

VS Code 将市场状态与确切安装的目标关联起来，而不仅仅是其显示名称。本地自定义和 Marketplace 安装的同名自定义保持独立：

Installed 表示录制的目标可用。

丢失文件表示市场安装的技能中的文件不再存在。选择“修复”以仅恢复丢失的文件。修复会保留您编辑或添加的文件。

删除 Marketplace 安装的自定义会删除其确切的安装。它不会删除具有相同名称的不相关本地自定义。

选择定制范围

将自定义存储在与您想要的使用和共享方式相匹配的最窄范围内：

用户：在您的工作区中使用自定义。用户自定义是特定于您的，并不致力于项目。

工作区：通过源代码控制与项目贡献者共享定制。

组织：集中管理支持的自定义类型并跨存储库共享它们。组织自定义是通过 GitHub 配置的，而不是在 Agent 自定义编辑器中创建的。

范围决定了定制的可用范围以及谁可以共享定制。并非每种自定义类型都支持所有范围。所选的 agent 线束还决定了它支持哪些自定义类型和位置。请参阅每种自定义类型的指南以了解其支持的位置。

      
        
注意事项
      
对于在 Agent 主机上运行的会话，agent 从 ~/.copilot (Copilot) 和 ~/.claude (Claude) 等受支持的文件夹中读取用户级自定义设置，而不是从 VS Code 配置文件用户数据中读取。有关建议的用户级位置，请参阅说明、自定义 agents 和提示文件。

创建定制

您可以使用 AI 创建自定义文件或手动创建它们。在依赖每个定制来执行开发任务之前，请先检查并测试每个定制。

使用 AI 创建自定义

打开 Agent 自定义编辑器。

在概述部分中，输入描述您要创建的内容的提示。

回答 agent 提出的有关缺失详细信息的任何问题。

查看生成的自定义，更正任何不准确的信息，然后保存文件。

例如，要创建代码审阅者技能，您可以输入以下提示：

为 Python 和 JavaScript 文件创建工作区代码审阅者技能。根据此存储库中的约定检查代码，识别正确性和可维护性问题，并报告结果以及文件位置和建议的修复。

手动创建自定义

打开 Agent 自定义编辑器并选择自定义类型。

从“新建”下拉列表中，选择用户或工作区自定义。

输入名称并在出现提示时选择存储位置。

在内联编辑器中编辑自定义内容，该编辑器提供语法突出显示和验证。

查看并保存自定义。

您还可以从命令面板 (⇧⌘P (Windows、Linux Ctrl+Shift+P)) 运行聊天：新建 <customization-type> 命令。该命令在支持的位置为该自定义类型创建相应的文件。

对于 Agent Skills、MCP 服务器和 agent 插件，请使用 Discover 浏览市场源、安装项目并从相应部分管理安装。

请参阅自定义指令、agent skills、提示文件、自定义 agents、MCP 服务器、挂钩和 agent 插件的指南，了解其文件格式和配置选项。

管理现有的定制

使用 Agent 自定义编辑器查找并更新所选线束的自定义：

打开 Agent 自定义编辑器并选择自定义类型。

使用搜索框查找定制。

选择自定义以打开其详细信息或编辑其文件。

检查并保存您的更改。

可用的操作取决于自定义类型及其来源。使用内联操作或上下文菜单访问支持的操作，例如显示操作系统中的可编辑文件或将其删除。从插件部分管理插件提供的自定义。

实验性
 用户自定义迁移是实验性的，可能会更改或删除。

      
        
重要
      
如果 Agent 自定义编辑器显示迁移，则 Copilot 无法在其当前位置或格式中使用一项或多项自定义。选择“迁移”以查看所需的工作，并按照 VS Code 中的“迁移 Copilot 自定义”了解详细步骤。

验证定制

使用代表性任务测试新的或更新的自定义。检查 agent 是否遵循预期的说明、工作流程、工具配置或生命周期操作。有关说明和技能，请展开聊天响应中的参考部分，以确认包含预期的自定义。

如果结果与您的意图不符，请使自定义更加具体，解决相互冲突的指导，然后重复该任务。有关完整的项目工作流程，包括如何查看和共享生成的说明，请参阅为您的代码库配置 AI。

解决自定义问题

如果未应用自定义或导致意外行为，请检查以下各项：

选择预期的 agent 线束。

定制存储在线束支持的位置。

任何激活条件都与当前文件或任务匹配。

自定义文件没有验证错误。

如果问题仍然存在，请打开 Agent 调试日志视图以解决 agent 问题。运行开发人员：从命令面板 (⇧⌘P (Windows、Linux Ctrl+Shift+P)) 打开 Agent 调试日志，或在聊天视图中选择省略号 (...) 菜单，然后选择显示 Agent 调试日志。

在单一存储库中使用自定义

在 monorepo 设置中，您可以在 VS Code 中打开存储库的子文件夹，而不是存储库根目录。默认情况下，VS Code 仅发现打开的工作区文件夹中的自定义文件。启用
    
      
聊天。useCustomizationsInParentRepositories
      
    
    
        
    
    
在 VS Code
 中打开 在 VS Code 内部人员中打开
    
设置还可以从父存储库中发现自定义项。

启用此设置后，VS Code 会从每个工作区文件夹向上遍历文件夹层次结构，直到找到 .git 文件夹。如果找到，它会从工作区文件夹和存储库根（包括）之间的所有文件夹收集自定义设置。这适用于所有自定义类型：始终开启指令（copilot-instructions.md、AGENTS.md、CLAUDE.md）、基于文件的指令、提示文件、自定义 agents、agent skills 和挂钩。

例如，考虑以下 monorepo 结构：

my-monorepo/ # repo根目录（有.git文件夹）
├── .github/
│ ├── copilot-instructions.md
│ ├── instructions/
 │ │ └── style.instructions.md
│ ├──提示/
│ │ └── review.prompt.md
│ └── agents/
│ └── reviewer.agent.md
├── 包/
│ └── 前端/ # 作为工作区打开文件夹
│ └── src/

如果仅打开 VS Code 中的 packages/frontend/ 并启用该设置，VS Code 会发现存储库根目录下的自定义文件，例如 copilot-instructions.md、style.instructions.md、review.prompt.md 和 reviewer.agent.md。

父存储库发现的条件：

工作区文件夹不包含 .git 文件夹（它本身不是存储库根）。

父文件夹包含一个 .git 文件夹。

父存储库文件夹是受信任的。 VS Code 在工作区打开时提示您信任父文件夹。

      
        
注意事项
      
的
    
      
聊天。useCustomizationsInParentRepositories
      
    
    
        
    
    
在 VS Code
 中打开 在 VS Code 内部人员中打开
    
默认情况下禁用设置。

评估和改进自定义文件（预览）

随着您的自定义文件集合的增长，可能很难判断它们是否真正按照您想要的方式引导 AI。含糊的措辞、相互矛盾的规则或过于复杂的说明都会降低结果的质量。聊天自定义评估扩展通过分析您的文件并提出具体改进建议，帮助您在依赖自定义之前发现这些问题。

      
        
注意事项
      
聊天自定义评估扩展与 VS Code 分开发布。从 Visual Studio Marketplace 安装它。

该扩展适用于技能文件 (SKILL.md)、自定义 agent 文件 (*.agent.md)、说明文件 (*.instructions.md) 和提示文件（*。prompt.md）。

打开自定义文件并运行聊天自定义评估：从命令面板分析命令 (⇧⌘P (Windows、Linux Ctrl+Shift+P))。
该扩展使用 GitHub Copilot 来检测以下问题：

逻辑、行为和格式矛盾

措辞不明确，建议重写

相互冲突的角色特征和语气漂移

深层嵌套条件造成的过度认知负荷

意图处理中的差距和缺失的错误路径

文件与其链接到的其他自定义文件之间的冲突

诊断显示在“问题”面板 (⇧⌘M（Windows、Linux Ctrl+Shift+M））中，并包含行和列位置。分析完成后，选择实施建议以应用建议的改进。

      
        
提示
      
您还可以使用 /analyze-prompt 斜线命令从聊天开始分析，该命令直接在聊天视图中总结活动自定义文件的诊断信息。

对于技能文件，该扩展与 Waza 评估框架集成，以衡量技能针对一组测试用例的执行情况。运行聊天自定义评估：下载 Waza 二进制文件以安装 Waza，聊天自定义评估：创建 Waza Eval 支架以生成活动技能的评估文件，以及聊天自定义评估：运行 Waza 评估以运行套件。如需分步指导，请运行聊天自定义评估：打开分析和修复用户指南。

相关资源

定制理念

为您的项目指南定制 AI

                
                
                10/7/2026
                
            
        
        
        
        
            
        
    

		
	

	
		
			
				
					
						
						
							
								
								
							
						
					
				
				
			
			
				
			
			
		
	

	
		
			
				
					
						

							
						
						

							
						
						

							
						
						

							
						
						

							
						
						

							
						
						

							
						
						

							
						
						

							
						
						
					
					
						
					
				
			
			
				
					
支持
					
隐私
					
管理 Cookie
					
使用条款
					
许可证
				
			
			
				
					

						
						您的隐私选择
					
消费者健康隐私


## 原文对照

<details>
<summary>展开完整原文</summary>

Create and manage agent customizations

	

	
	
	
	
	

	
		AI agents: See llms.txt for documentation discovery and navigation. Documentation articles are also available as Markdown by appending .md to the article URL.
	
	
	

	
		
			
						
					
						
							Visual Studio Code
						
						
							
								

									
										Features 
									
									
										
Agents
										
Agent Customization
										
Models & Agent Harnesses
										
Enterprise
										
Extensibility
									
								
								

									
										Docs 
									
									
										
Documentation
										
API
										
FAQ
									
								
								
Release Notes
								
								
Blog
								
Learn
								
Events
								

									
										Resources 
									
									
										
Extensions
										
MCP
									
								
							
							
								

									
										Download
									
								
							
						
						
							
								
									
										
											
												
												
											
										
										
										Search
										
									
													
							
								
								
							
							
								Download
							
						
						
							
							
							
						
					
				
			
				
			
				
🎬 Watch The Story of VS Code!
				
VS Code is now on Instagram, follow @vscode.ig for updates, tips, and more.
				
Rewatch GitHub Copilot Day.
			
			Dismiss this update
		
		
		
		
		
			
    
        
        
            
              
              
Documentation
              
                
                

                  Overview
                
            
                
            

              Get Started
              
                
                

                  Overview
                
            
                

                  Agents Quickstart
                
            
                

                  Editor Tutorial
                
            
                

                  Intro Videos
                
            
              
            
            
            

              Agents
              
                
                

                  AI Overview
                
            
            

              Get started
              
                
                

                  Agents Quickstart
                
            
                

                  Find a Guide
                
            
                

                  Set Up GitHub Copilot
                
            
                

                  Agents Tutorial
                
            
                

                  Best Practices
                
            
              
            
            
            

              Concepts
              
                
                

                  Agents
                
            
                

                  How Models Work
                
            
                

                  Context
                
            
                

                  Tools
                
            
                

                  Sessions and Chats
                
            
                

                  Trust & Safety
                
            
                

                  Agent Customization
                
            
                

                  Agent Harnesses
                
            
                

                  Workspace Context
                
            
                

                  Agent Host Architecture
                
            
              
            
            
            

              Use Agents
              
                
                

                  Chat View and Layout
                
            
                

                  Agents Window
                
            
                

                  Choose an Agent Harness
                
            
                

                  Configure the Agents Window
                
            
                

                  Chat Interaction Controls
                
            
            

              Plan and Run Tasks
              
                
                

                  Plan Work
                
            
                

                  Use Tools
                
            
                

                  Browser Tools
                
            
                

                  Subagents
                
            
                

                  Automations
                
            
              
            
            
            

              Sessions and Results
              
                
                

                  Manage Sessions
                
            
                

                  Session History
                
            
                

                  Remote Agent Sessions
                
            
                

                  Memory
                
            
                

                  Review & Revert Changes
                
            
                

                  Artifacts
                
            
              
            
            
            

              Permissions and Security
              
                
                

                  Approvals & Permissions
                
            
                

                  Agent Sandboxing
                
            
                

                  AI Security
                
            
              
            
            
              
            
            
            

              Customize agents
              
                
                

                  Create and Manage
                
            
                

                  Instructions
                
            
                

                  Agent Skills
                
            
                

                  Custom Agents
                
            
                

                  Choose and Configure Models
                
            
                

                  MCP
                
            
                

                  Hooks
                
            
                

                  Plugins
                
            
                

                  Prompt Files
                
            
                

                  Migrate Existing Customizations
                
            
              
            
            
            

              Tutorials & guides
              
                
                

                  Explore a Codebase
                
            
                

                  Add a Feature
                
            
                

                  Fix a Bug
                
            
                

                  Refactor Safely
                
            
                

                  Add Tests
                
            
                

                  Test Web Apps
                
            
                

                  Run Parallel Tasks
                
            
                

                  Get Back on Track
                
            
                

                  Adapt Agents to Your Project
                
            
                

                  Context Engineering
                
            
                

                  Test-Driven Development
                
            
                

                  Jupyter Notebooks
                
            
                

                  Reduce Credit Usage
                
            
                

                  Prompt Examples
                
            
              
            
            
            

              Reference
              
                
                

                  Cheat Sheet
                
            
                

                  Tools and Context
                
            
                

                  Settings Reference
                
            
                

                  VS Code Pet
                
            
                

                  MCP Configuration
                
            
                

                  Hooks Reference
                
            
                

                  OpenTelemetry Monitoring
                
            
              
            
            
            

              Troubleshooting
              
                
                

                  Troubleshooting
                
            
                

                  Debug Chat Interactions
                
            
                

                  Diagnose Prompt Caching
                
            
                

                  FAQ
                
            
              
            
            
              
            
            
            

              Chat
              
                
                

                  Chat Interaction Controls
                
            
                

                  Inline & Quick Chat
                
            
                

                  Add Prompt Context
                
            
              
            
            
            

              Editor
              
                
                

                  Overview
                
            
            

              Getting Started
              
                
                

                  Editor Tutorial
                
            
                

                  User Interface
                
            
                

                  Tips and Tricks
                
            
              
            
            
            

              Write code
              
                
                

                  Basic Editing
                
            
                

                  IntelliSense
                
            
                

                  Inline Suggestions
                
            
                

                  Smart Actions
                
            
                

                  Code Navigation
                
            
                

                  Refactoring
                
            
                

                  Snippets
                
            
            

              Workspaces
              
                
                

                  Overview
                
            
                

                  Multi-Root Workspaces
                
            
                

                  Workspace Trust
                
            
              
            
            
              
            
            
            

              Configure the editor
              
                
                

                  Display Language
                
            
                

                  Layout
                
            
                

                  Keyboard Shortcuts
                
            
                

                  Settings
                
            
                

                  Settings Sync
                
            
            

              Extensions
              
                
                

                  Overview
                
            
                

                  Extension Marketplace
                
            
                

                  Extension Runtime Security
                
            
              
            
            
                

                  Themes
                
            
                

                  Profiles
                
            
            

              Accessibility
              
                
                

                  Overview
                
            
                

                  Voice Interactions
                
            
              
            
            
                

                  Command Line Interface
                
            
                

                  Telemetry
                
            
              
            
            
            

              Reference
              
                
                

                  Default Keyboard Shortcuts
                
            
                

                  Default Settings
                
            
                

                  Substitution Variables
                
            
                

                  Tasks Schema
                
            
              
            
            
              
            
            
            

              Source Control
              
                
                

                  Overview
                
            
                

                  Quickstart
                
            
                

                  Repositories & Remotes
                
            
                

                  Staging, Commits & Undo
                
            
                

                  History & Blame
                
            
                

                  Branches, Stashes & Worktrees
                
            
                

                  Merge Conflicts
                
            
                

                  Collaborate on GitHub
                
            
                

                  Troubleshooting
                
            
                

                  FAQ
                
            
              
            
            
            

              Terminal
              
                
                

                  Get Started
                
            
                

                  Terminal Basics
                
            
                

                  Terminal Profiles
                
            
                

                  Shell Integration
                
            
                

                  Appearance
                
            
                

                  Advanced
                
            
              
            
            
            

              Debugging & Testing
              
                
                

                  Debugging
                
            
                

                  Debug Configuration
                
            
                

                  Tasks
                
            
                

                  Testing
                
            
                

                  Integrated Browser
                
            
                

                  Port Forwarding
                
            
            

              Guides & Tutorials
              
                
                

                  Test-Driven Development
                
            
                

                  Test Web Apps
                
            
              
            
            
              
            
            
            

              Enterprise
              
                
                

                  Overview
                
            
                

                  Enterprise Policies
                
            
                

                  Manage AI Settings
                
            
                

                  Extensions
                
            
                

                  Telemetry
                
            
                

                  Updates
                
            
              
            
            
            

              Remote
              
                
                

                  Overview
                
            
                

                  VS Code for the Web
                
            
                

                  SSH
                
            
                

                  SSH Tutorial
                
            
                

                  Tunnels
                
            
                

                  Dev Containers
                
            
                

                  WSL
                
            
                

                  WSL Tutorial
                
            
                

                  GitHub Codespaces
                
            
                

                  VS Code Server
                
            
                

                  Linux Prerequisites
                
            
                

                  Tips and Tricks
                
            
                

                  FAQ
                
            
              
            
            
            

              Advanced Setup
              
                
                

                  GitHub Copilot Setup
                
            
                

                  Linux
                
            
                

                  macOS
                
            
                

                  Windows
                
            
                

                  Raspberry Pi
                
            
                

                  Network
                
            
                

                  Portable Mode
                
            
                

                  Additional Components
                
            
                

                  Uninstall
                
            
              
            
            
                

                  Languages & Runtimes
                
            
                

                  Extension Docs
                
            
              
            
            
              Topics
              
                
                
                Overview
            
                
                Overview
            
                Agents Quickstart
            
                Editor Tutorial
            
                Intro Videos
            
                AI Overview
            Get started
                    Agents Quickstart
            
                    Find a Guide
            
                    Set Up GitHub Copilot
            
                    Agents Tutorial
            
                    Best Practices
            Concepts
                    Agents
            
                    How Models Work
            
                    Context
            
                    Tools
            
                    Sessions and Chats
            
                    Trust & Safety
            
                    Agent Customization
            
                    Agent Harnesses
            
                    Workspace Context
            
                    Agent Host Architecture
            Use Agents
                    Chat View and Layout
            
                    Agents Window
            
                    Choose an Agent Harness
            
                    Configure the Agents Window
            
                    Chat Interaction Controls
                Plan and Run Tasks
                        Plan Work
            
                        Use Tools
            
                        Browser Tools
            
                        Subagents
            
                        Automations
                Sessions and Results
                        Manage Sessions
            
                        Session History
            
                        Remote Agent Sessions
            
                        Memory
            
                        Review & Revert Changes
            
                        Artifacts
                Permissions and Security
                        Approvals & Permissions
            
                        Agent Sandboxing
            
                        AI Security
            Customize agents
                    Create and Manage
            
                    Instructions
            
                    Agent Skills
            
                    Custom Agents
            
                    Choose and Configure Models
            
                    MCP
            
                    Hooks
            
                    Plugins
            
                    Prompt Files
            
                    Migrate Existing Customizations
            Tutorials & guides
                    Explore a Codebase
            
                    Add a Feature
            
                    Fix a Bug
            
                    Refactor Safely
            
                    Add Tests
            
                    Test Web Apps
            
                    Run Parallel Tasks
            
                    Get Back on Track
            
                    Adapt Agents to Your Project
            
                    Context Engineering
            
                    Test-Driven Development
            
                    Jupyter Notebooks
            
                    Reduce Credit Usage
            
                    Prompt Examples
            Reference
                    Cheat Sheet
            
                    Tools and Context
            
                    Settings Reference
            
                    VS Code Pet
            
                    MCP Configuration
            
                    Hooks Reference
            
                    OpenTelemetry Monitoring
            Troubleshooting
                    Troubleshooting
            
                    Debug Chat Interactions
            
                    Diagnose Prompt Caching
            
                    FAQ
            
                Chat Interaction Controls
            
                Inline & Quick Chat
            
                Add Prompt Context
            
                Overview
            Getting Started
                    Editor Tutorial
            
                    User Interface
            
                    Tips and Tricks
            Write code
                    Basic Editing
            
                    IntelliSense
            
                    Inline Suggestions
            
                    Smart Actions
            
                    Code Navigation
            
                    Refactoring
            
                    Snippets
                Workspaces
                        Overview
            
                        Multi-Root Workspaces
            
                        Workspace Trust
            Configure the editor
                    Display Language
            
                    Layout
            
                    Keyboard Shortcuts
            
                    Settings
            
                    Settings Sync
                Extensions
                        Overview
            
                        Extension Marketplace
            
                        Extension Runtime Security
            
                    Themes
            
                    Profiles
                Accessibility
                        Overview
            
                        Voice Interactions
            
                    Command Line Interface
            
                    Telemetry
            Reference
                    Default Keyboard Shortcuts
            
                    Default Settings
            
                    Substitution Variables
            
                    Tasks Schema
            
                Overview
            
                Quickstart
            
                Repositories & Remotes
            
                Staging, Commits & Undo
            
                History & Blame
            
                Branches, Stashes & Worktrees
            
                Merge Conflicts
            
                Collaborate on GitHub
            
                Troubleshooting
            
                FAQ
            
                Get Started
            
                Terminal Basics
            
                Terminal Profiles
            
                Shell Integration
            
                Appearance
            
                Advanced
            
                Debugging
            
                Debug Configuration
            
                Tasks
            
                Testing
            
                Integrated Browser
            
                Port Forwarding
            Guides & Tutorials
                    Test-Driven Development
            
                    Test Web Apps
            
                Overview
            
                Enterprise Policies
            
                Manage AI Settings
            
                Extensions
            
                Telemetry
            
                Updates
            
                Overview
            
                VS Code for the Web
            
                SSH
            
                SSH Tutorial
            
                Tunnels
            
                Dev Containers
            
                WSL
            
                WSL Tutorial
            
                GitHub Codespaces
            
                VS Code Server
            
                Linux Prerequisites
            
                Tips and Tricks
            
                FAQ
            
                GitHub Copilot Setup
            
                Linux
            
                macOS
            
                Windows
            
                Raspberry Pi
            
                Network
            
                Portable Mode
            
                Additional Components
            
                Uninstall
            
                Languages & Runtimes
            
                Extension Docs
            
              
                    
        
        
        
            
            
                
                    
                    
On this page there are 9 sectionsOn this page
                    
                        
                        
Agent Customizations editor
                        
                        
Choose a customization scope
                        
                        
Create a customization
                        
                        
Manage existing customizations
                        
                        
Verify a customization
                        
                        
Troubleshoot customization issues
                        
                        
Use customizations in a monorepo
                        
                        
Evaluate and improve customization files (Preview)
                        
                        
Related resources
                        
                    
                    
                
                
                
                    

                        
                        Related
                    
                    
                    
                    
                        Core concepts
                        
                        
Learn about the different customization types and when to use each one.
                        
                        
                            
                            
                            
                                Customization concepts
                            
                            
                            
                        
                    
                    
                    
                        Tutorial
                        
                        
Follow a hands-on walkthrough to customize AI for your project.
                        
                        
                            
                            
                            
                                Customize AI for your project
                            
                            
                            
                        
                    
                    
                
                
            
            
            
            
                
                
Create and manage agent customizations
                
                

	
		
			
			Copy as Markdown
		
		
			
		
		
			

				
					
					Copy as Markdown
				
			
			

				
					
					View as Markdown
					
				
			
			

			

				
					
					Ask in VS Code
				
			
			

				
					
					Ask in VS Code Insiders
				
			
		
	
	

                
                
                

Agent customizations adapt agents to your coding standards, workflows, and development systems. This article explains how to create, manage, evaluate, and troubleshoot customizations in Visual Studio Code. To compare the customization types and understand how they work together, see Agent customization concepts.

Agent Customizations editor

The Agent Customizations editor provides a central place to discover, create, and manage customizations. It organizes customization types into separate sections and includes an editor with syntax highlighting and validation.

When you open the Customizations editor, the customizations are scoped to the selected agent harness. Select the harness from the dropdown in the chat input before you open the editor to ensure the customizations apply to the correct context.

Follow these steps to open the Agent Customizations editor:
Agents windowChat view
In the left sidebar, select a customization type in the Customizations panel.

In the Chat view, select the Configure Chat (gear icon) or run Chat: Open Customizations from the Command Palette (⇧⌘P (Windows, Linux Ctrl+Shift+P)).

Discover marketplace customizations (Experimental)

The Customization Marketplace provides a unified Discover page for finding skills, MCP servers, plugins, and other agent customizations from configured sources. To use the marketplace, turn on the 
    
      
      chat.customizations.marketplace.enabled
      
    
    
        
    
    
      Open in VS Code
      Open in VS Code Insiders
    
   setting.

The GitHub Feed source is available by default when the marketplace is enabled. Use the 
    
      
      chat.customizations.marketplace.sources.publicFeed.enabled
      
    
    
        
    
    
      Open in VS Code
      Open in VS Code Insiders
    
   setting to show or hide this source.

To find and install a customization:

Open the Agent Customizations editor and select Discover.

Use the source menu to browse all sources or select a specific source, such as GitHub Feed.

Search by name or use a type filter, such as @type:skill or @type:mcp.

Select an item and review its publisher, source, installation details, and any setup requirements.

Select Install and choose a destination when prompted.

Some items are available for discovery but don't support automatic installation. Follow the publisher's setup instructions when they are provided.

Marketplace items show a source or publisher image in Discover and, when available, on the installed item and its details page. Use this information together with the item details to verify the source before installation.

VS Code associates marketplace state with the exact installed target, not only its display name. A local customization and a Marketplace-installed customization with the same name remain separate:

Installed indicates that the recorded target is available.

Missing files indicates that files from a Marketplace-installed skill are no longer present. Select Repair to restore only the missing files. Repair preserves files that you edited or added.

Deleting a Marketplace-installed customization removes its exact installation. It doesn't remove an unrelated local customization with the same name.

Choose a customization scope

Store a customization at the narrowest scope that matches how you want to use and share it:

User: use the customization across your workspaces. User customizations are specific to you and are not committed to the project.

Workspace: share the customization with project contributors through source control.

Organization: centrally manage supported customization types and share them across repositories. Organization customizations are configured through GitHub rather than created in the Agent Customizations editor.

Scope determines where a customization is available and who can share it. Not every customization type supports every scope. The selected agent harness also determines which customization types and locations it supports. See the guide for each customization type for its supported locations.

      
        
        Note
      
For sessions that run on Agent Host, the agent reads user-level customizations from supported folders like ~/.copilot (Copilot) and ~/.claude (Claude), rather than from your VS Code profile user data. See instructions, custom agents, and prompt files for the recommended user-level locations.

Create a customization

You can create customization files with AI or create them manually. Review and test every customization before you rely on it for development tasks.

Create a customization with AI

Open the Agent Customizations editor.

In the Overview section, enter a prompt that describes what you want to create.

Answer any questions the agent asks about missing details.

Review the generated customization, correct any inaccurate information, and save the file.

For example, to create a code reviewer skill, you can enter the following prompt:

Create a workspace code reviewer skill for Python and JavaScript files. Check the code against the conventions in this repository, identify correctness and maintainability issues, and report findings with file locations and suggested fixes.

Create a customization manually

Open the Agent Customizations editor and select a customization type.

From the New dropdown, choose a user or workspace customization.

Enter a name and choose a storage location when prompted.

Edit the customization in the inline editor, which provides syntax highlighting and validation.

Review and save the customization.

You can also run the Chat: New <customization-type> command from the Command Palette (⇧⌘P (Windows, Linux Ctrl+Shift+P)). The command creates the corresponding file in a supported location for that customization type.

For Agent Skills, MCP servers, and agent plugins, use Discover to browse marketplace sources, install an item, and manage the installation from the corresponding section.

See the guides for custom instructions, agent skills, prompt files, custom agents, MCP servers, hooks, and agent plugins for their file formats and configuration options.

Manage existing customizations

Use the Agent Customizations editor to find and update customizations for the selected harness:

Open the Agent Customizations editor and select the customization type.

Use the search box to find the customization.

Select the customization to open its details or edit its file.

Review and save your changes.

The available actions depend on the customization type and its source. Use the inline actions or context menu to access supported operations, such as revealing an editable file in your operating system or deleting it. Manage plugin-provided customizations from the Plugins section.

  Experimental
  User customization migration is experimental and might change or be removed.

      
        
        Important
      
If the Agent Customizations editor shows Migrations, Copilot cannot use one or more customizations in their current location or format. Select Migrations to review the required work, and follow Migrate Copilot customizations in VS Code for detailed steps.

Verify a customization

Test a new or updated customization with a representative task. Check that the agent follows the expected instructions, workflow, tool configuration, or lifecycle action. For instructions and skills, expand the References section in the chat response to confirm that the expected customization was included.

If the result doesn't match your intent, make the customization more specific, resolve conflicting guidance, and repeat the task. For a complete project workflow, including how to review and share generated instructions, see Configure AI for your codebase.

Troubleshoot customization issues

If a customization isn't applied or causes unexpected behavior, check the following:

The intended agent harness is selected.

The customization is stored in a location that the harness supports.

Any activation conditions match the current file or task.

The customization file has no validation errors.

If the issue continues, open the Agent Debug Logs view to troubleshoot agent issues. Run Developer: Open Agent Debug Logs from the Command Palette (⇧⌘P (Windows, Linux Ctrl+Shift+P)), or in the Chat view select the ellipsis (...) menu and select Show Agent Debug Logs.

Use customizations in a monorepo

In monorepo setups, you might open a subfolder of a repository in VS Code rather than the repo root. By default, VS Code only discovers customization files within your open workspace folder(s). Enable the 
    
      
      chat.useCustomizationsInParentRepositories
      
    
    
        
    
    
      Open in VS Code
      Open in VS Code Insiders
    
   setting to also discover customizations from the parent repository.

When this setting is enabled, VS Code walks up the folder hierarchy from each workspace folder until it finds a .git folder. If found, it collects customizations from all folders between the workspace folder and the repository root (inclusive). This applies to all customization types: always-on instructions (copilot-instructions.md, AGENTS.md, CLAUDE.md), file-based instructions, prompt files, custom agents, agent skills, and hooks.

For example, consider the following monorepo structure:

my-monorepo/              # repo root (has .git folder)
├── .github/
│   ├── copilot-instructions.md
│   ├── instructions/
│   │   └── style.instructions.md
│   ├── prompts/
│   │   └── review.prompt.md
│   └── agents/
│       └── reviewer.agent.md
├── packages/
│   └── frontend/          # opened as workspace folder
│       └── src/

If you open only packages/frontend/ in VS Code and enable the setting, VS Code discovers the customization files at the repo root, such as copilot-instructions.md, style.instructions.md, review.prompt.md, and reviewer.agent.md.

Conditions for parent repository discovery:

The workspace folder does not contain a .git folder (it is not itself a repository root).

A parent folder contains a .git folder.

The parent repository folder is trusted. VS Code prompts you to trust the parent folder when the workspace is opened.

      
        
        Note
      
The 
    
      
      chat.useCustomizationsInParentRepositories
      
    
    
        
    
    
      Open in VS Code
      Open in VS Code Insiders
    
   setting is disabled by default.

Evaluate and improve customization files (Preview)

As your collection of customization files grows, it can be hard to tell whether they actually guide the AI the way you intend. Vague wording, contradictory rules, or overly complex instructions can degrade results. The Chat Customizations Evaluations extension helps you catch these problems before you rely on a customization, by analyzing your files and suggesting concrete improvements.

      
        
        Note
      
The Chat Customizations Evaluations extension is published separately from VS Code. Install it from the Visual Studio Marketplace.

The extension works with skill files (SKILL.md), custom agent files (*.agent.md), instructions files (*.instructions.md), and prompt files (*.prompt.md).

Open a customization file and run the Chat Customizations Evaluations: Analyze command from the Command Palette (⇧⌘P (Windows, Linux Ctrl+Shift+P)).
The extension uses GitHub Copilot to detect issues such as:

Logical, behavioral, and format contradictions

Ambiguous wording, with suggested rewrites

Conflicting persona traits and tone drift

Excessive cognitive load from deeply nested conditions

Gaps in intent handling and missing error paths

Conflicts between a file and other customization files it links to

Diagnostics appear in the Problems panel (⇧⌘M (Windows, Linux Ctrl+Shift+M)) with line and column locations. After the analysis completes, select Implement Suggestions to apply the suggested improvements.

      
        
        Tip
      
You can also start an analysis from chat with the /analyze-prompt slash command, which summarizes the diagnostics for the active customization file directly in the Chat view.

For skill files, the extension integrates with the Waza evaluation framework to measure how well a skill performs against a set of test cases. Run Chat Customizations Evaluations: Download Waza Binary to install Waza, Chat Customizations Evaluations: Create Waza Eval Scaffold to generate evaluation files for the active skill, and Chat Customizations Evaluations: Run Waza Evaluation to run the suite. For step-by-step guidance, run Chat Customizations Evaluations: Open Analysis and Fix User Guide.

Related resources

Customization concepts

Customize AI for your project guide

                
                
                10/7/2026
                
            
        
        
        
        
            
        
    

		
	

	
		
			
				
					
						
						
							
								
								
							
						
					
				
				
			
			
				
			
			
		
	

	
		
			
				
					
						

							
						
						

							
						
						

							
						
						

							
						
						

							
						
						

							
						
						

							
						
						

							
						
						

							
						
						
					
					
						
					
				
			
			
				
					
Support
					
Privacy
					
Manage Cookies
					
Terms of Use
					
License
				
			
			
				
					

						
						Your Privacy Choices
					
Consumer Health Privacy


</details>
