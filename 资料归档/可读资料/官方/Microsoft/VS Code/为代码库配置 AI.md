# 为代码库配置 AI

通过项目结构与验证示例组织指令、技能和自定义 Agent，说明多个运行框架中的配置位置和使用方式。

- 来源：Microsoft／VS Code
- 资料类型：存档文档
- 文档时间：官方审核：[2026-10-07](<https://code.visualstudio.com/docs/agents/guides/customize-copilot-guide>)；官方文件提交（截至抓取）：[2026-10-06](<https://github.com/microsoft/vscode-docs/commit/4fe6b03408632f2e9338a970d26afa480e4a022d>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://code.visualstudio.com/docs/agents/guides/customize-copilot-guide>) · [原始文件](<../../../../原始抓取备份/原文/官方/Microsoft/VS Code/为代码库配置 AI.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

为您的代码库配置 AI

	

	
	
	
	
	

	
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
            
              
                    
        
        
        
            
            
                
                    
                    
本页有11sectionsOn此页
                    
                        
                        
先决条件
                        
                        
建立基线
                        
                        
步骤1：创建项目指令
                        
                        
步骤 2：查看生成的指令
                        
                        
步骤 3：验证改进
                        
                        
步骤4：共享配置
                        
                        
步骤5：添加针对性指令（可选）
                        
                        
步骤 6：打包重复工作流程（可选）
                        
                        
步骤7：添加专门的agent（可选）
                        
                        
你配置了什么
                        
                        
后续步骤
                        
                    
                    
                
                
            
            
            
            
                
                
为您的代码库配置 AI
                
                

	
		
			
			复制为 Markdown
		
		
			
		
		
			

				
					
					复制为 Markdown
				
			
			

				
					
					查看为 Markdown
					
				
			
			

			

				
					
					询问VS Code
				
			
			

				
					
					在 VS Code 业内人士中询问
				
			
		
	
	

                
                
                

AI agents 当他们了解您的代码库的结构、要运行哪些命令以及要遵循哪些约定时，可以产生更好的结果。将此信息配置为存储库自定义一次，而不是在每个提示中重复它。

本指南帮助您配置 Visual Studio Code 中的 GitHub Copilot、Anthropic Claude 或 OpenAI Codex 线束。从观察到的项目问题和代表性任务开始。进行最小的有用自定义，确认其适用，重复任务，并在共享配置之前比较结果。工作流程是共享的，带有用于特定于线束的格式和行为的选项卡。

要了解自定义类型如何不同以及如何协同工作，请参阅 Agent 自定义。

先决条件

下载并安装Visual Studio Code。

完成您的安全带的设置和身份验证。

打开您要配置的存储库根目录。

启动会话并从会话目标控件中选择 Copilot、Claude 或 Codex。创建和测试其定制时使用相同的线束。

对于本演练，请在当前文件夹中工作。如果您使用的是 Agents 窗口，请不要选择“新建工作树”，以便新会话可以访问您未提交的自定义文件。

选择适合您的工具的选项卡，而不是您的语言模型。例如，Copilot 会话中的 Claude 模型仍使用 Copilot 线束。

      
        
注意事项
      
本地线束还支持本指南中所示的 Copilot 文件布局，但工具名称和某些激活行为有所不同。对于特定于本地的配置，请参阅自定义说明 agent skills 和自定义 agents。

建立基线

选择一个重复出现的特定于项目的问题，例如使用错误的测试命令、将文件放在错误的目录中或选择项目不使用的库。一次性需求属于当前请求而不是共享项目配置。

定义一个具有明确成功标准的小型代表性任务。记录当前结果或使用最近任务的证据：

agent 创建或更改哪些文件。

它遵循哪些项目模式和库。

它运行哪些命令和测试，包括失败或跳过的检查。

您必须提供哪些更正。

如果结果已经满足您的成功标准，请停止。您不需要针对该行为进行自定义。对于编码任务，请参阅添加功能指南以获取有关建立基线测试和验证结果的指导。

步骤1：创建项目指令

从项目说明文件开始，获取适用于您的代码库的信息。将初始更改集中在 agent 无法从存储库可靠推断出的观察到的问题和决策上。每个线束都有自己的发现规则，因此请使用其预期的文件名和位置。

打开所选线束的 Agent 自定义编辑器。在聊天视图中，运行聊天：从命令面板打开自定义 (⇧⌘P (Windows、Linux Ctrl+Shift+P))。

在“概述”部分中，输入下面的线束提示，然后按 Enter 键提交。

Continue 在打开的聊天中。确认它使用预期的工具和存储库，并回答任何澄清问题。

查看更改并将说明保存在预期位置。如果该文件已存在，请查看更新，而不是在不检查的情况下替换现有指南。

CopilotClaudeCodex
在存储库根目录中使用 .github/copilot-instructions.md 来获取项目范围的说明。

分析此代码库并使用项目范围的指令创建或更新 .github/copilot-instructions.md。涵盖架构、重要目录、构建和测试命令、编码约定以及完成更改的要求。保留有用的现有指导，避免重复，并询问您无法从存储库中确定的任何内容。

使用存储库根目录中的 CLAUDE.md 来获取项目范围的说明。

分析此代码库并使用项目范围的说明在存储库根目录中创建或更新 CLAUDE.md。涵盖架构、重要目录、构建和测试命令、编码约定以及完成更改的要求。保留有用的现有指导，避免重复，并询问您无法从存储库中确定的任何内容。

使用存储库根目录中的 AGENTS.md 来获取项目范围的说明。

分析此代码库并使用项目范围的说明在存储库根目录中创建或更新 AGENTS.md。涵盖架构、重要目录、构建和测试命令、编码约定以及完成更改的要求。保留有用的现有指导，避免重复，并询问您无法从存储库中确定的任何内容。

      
        
提示
      
当项目说明记录 agent 无法仅从代码可靠地推断出的决策时，项目说明最有用。避免适用于任何项目的通用建议。

步骤 2：查看生成的指令

将生成的指令视为起点。检查它们是否包含准确的、特定于项目的信息：

观察到的问题：仅包括改进基线行为所需的指导。

架构：描述重要的目录、组件边界以及在哪里添加不同类型的代码。

命令：包括正确的构建、测试、lint 和格式化命令。

技术选择：确定首选框架、库和模式。

约定：捕获命名、错误处理、测试、安全和文档要求。

完成的定义：说明 agent 在完成任务之前应运行哪些验证。

删除文件中其他地方通用、过时或重复的信息。解决相互冲突的指令并保持每条规则的简洁。

例如，特定于项目的说明可能包括：

## 项目结构

* 在`src/api/routes`下添加API路由。
* 将共享验证模式放入 `src/schemas` 中。
* 将数据库访问保留在存储库层。

## 验证

* 对更改的包运行单元测试。
* 在完成代码更改之前运行 linter。
* 为每个行为变化添加或更新测试。

详细了解如何编写有效的自定义指令。

步骤 3：验证改进

确认说明适用，然后从您的基线重复代表性任务。在可行的情况下保持线束、模型、工具、任务和相关上下文相同，以便定制是主要的更改。

在 Agent 自定义编辑器中，选择您的线束的说明并确认列出了项目说明文件。这会检查发现，而不是检查 agent 是否遵循每条指令。

使用相同的工具和存储库开始新的聊天，以便比较不依赖于创建对话的指导。

展开响应中的参考并确认包含预期的说明。

以相同的成功标准重复代表性任务。

将结果与基线进行比较。检查文件放置、项目模式、库、工具活动、命令、测试和错误，而不是依赖 agent 的摘要。

如果文件丢失、无效或包含但未遵循，请使用 agent 自定义故障排除指南来确定故障，然后再添加更多说明。

如果包含了说明，但结果仍然未达到成功标准，请澄清一条相关规则并在另一个新聊天中重复该任务。当任务满足标准时停止。更多指令会消耗上下文，并且可能会引入冲突，而不会改善结果。

要进行更广泛的受控比较，包括质量、可靠性、信用、令牌、持续时间和工具调用，请使用使用优化循环。

步骤4：共享配置

提交项目说明文件并通过存储库的正常拉取请求或审核流程共享它。获取更新文件并使用兼容工具的贡献者可以重复使用该指南，而无需重新创建它。

像其他开发配置一样查看说明。使用您现有的所有权和审查流程来明确文件的责任。当架构、命令、依赖项或团队实践发生变化时进行更新，并删除陈旧或重复的规则。

如果您的团队使用多个工具，请保持共享指南的一致性并避免相互矛盾的副本。一个线束支持的文件不会自动受到所有其他线束的支持。

如果代表性任务现在满足成功标准，则停止。您的存储库有一个有用的基线自定义。其余步骤是可选的。当代码库的不同部分需要不同的指导或当您的团队重复执行相同的工作流程时，添加它们。

步骤5：添加针对性指令（可选）

当代码库的不同部分需要不同的指导时，添加有针对性的说明。例如，前端代码和基础设施代码可能遵循不同的约定。将项目范围的标准保留在步骤 1 的说明文件中。

在 Agent 自定义编辑器的概述部分中，提交请求以在下面为您的线束创建文件。描述它应该涵盖的约定，并使用存储库中的现有文件类型或目录。
CopilotClaudeCodex
创建 .github/instructions/frontend.instructions.md. 对于 TypeScript React 文件，请在 Markdown 说明之前包含此 YAML frontmatter：

---
applyTo: "**/*.tsx"
---

该模式匹配整个存储库中的 .tsx 文件。通过要求 agent 对特定匹配文件进行小更改来检查模式并测试说明。

了解有关基于文件的指令的更多信息。
创建 .claude/rules/frontend.md. Claude 对于路径范围的规则使用路径而不是 applyTo：

---
路径：
  - "**/*.tsx"
---

通过要求 agent 对特定匹配文件进行小更改来检查模式并测试规则。如果没有路径，规则文件将普遍适用，而不仅仅是匹配文件。

了解有关 Claude 路径特定规则的更多信息。
在需要其自身指导的目录中使用 AGENTS.md 文件，例如 packages/frontend/AGENTS.md. Codex 发现从存储库根目录到会话工作目录的指令。这是基于目录的指导，而不是 applyTo 或路径全局。

要测试嵌套指令，请启动一个新的 Codex 会话，并将该子目录作为其工作文件夹。在编辑器中打开文件不会更改会话的工作目录。

了解有关 Codex 指令发现的更多信息。

查看并保存生成的文件，然后使用相同的工具在新聊天中对其进行测试。确认结果遵循目标约定。如果没有，请在修改说明之前检查发现和范围。

如果您更改了会话的工作文件夹来测试嵌套指令，请在继续之前返回到存储库根目录。

步骤 6：打包重复工作流程（可选）

当您的团队重复解释相同的多步骤流程时，创建 agent 技能。技能可以包括 agent 相关时加载的指令、脚本、模板、示例和其他资源。

例如，创建一个验证更改技能，选择并运行代码更改的相关检查，然后报告哪些内容通过、失败或未运行。在 Agent 自定义编辑器的概述部分提交针对您的工具的提示：
CopilotClaudeCodex
在 处创建名为 validate-change 的工作区技能。github/skills/validate-change/SKILL.md. 它应该检查代码更改，使用此存储库的命令选择并运行相关测试和 lint 检查，并报告通过、失败或未运行的内容。描述何时在其 frontmatter 中使用它。

在 .claude/skills/validate-change/SKILL.md. 创建一个名为 validate-change 的工作区技能，它应该检查代码更改，使用此存储库的命令选择并运行相关测试和 lint 检查，并报告通过、失败或未运行的内容。描述何时在其 frontmatter 中使用它。

在 .agents/skills/validate-change/SKILL.md. 创建一个名为 validate-change 的工作区技能，它应该检查代码更改，使用此存储库的命令选择并运行相关测试和 lint 检查，并报告通过、失败或未运行的内容。描述何时在其 frontmatter 中使用它。

生成技能后：

查看 SKILL.md 文件。检查其名称是否与目录名称匹配，其描述解释了它的用途以及何时使用它。

查看技能目录中的所有脚本、模板或其他资源，然后保存更改。

使用相同的工具和存储库开始新的聊天。在聊天输入中键入 / 并选择 validate-change 以显式调用该技能。识别要验证的代码更改。

查看工具活动和结果。检查 agent 是否加载技能、选择适当的检查并准确报告失败或跳过的检查。

您还可以通过要求 agent 验证更改而不命名技能，在单独的聊天中测试自动选择。如果未选择技能，请检查描述和调用设置。显式调用和模型选择使用是单独的检查。

了解有关 agent skills 的更多信息。

步骤7：添加专门的agent（可选）

当角色需要集中指令时，创建自定义 agent。例如，代码库研究人员可以在您决定更改内容之前调查实施细节并报告结果。工具限制取决于工具，因此不要假设角色描述本身会阻止编辑。

在 Agent 自定义编辑器的概述部分中，提交针对您的工具的提示：
CopilotClaudeCodex
在 处创建一个名为研究人员的自定义工作区 agent。github/agents/researcher.agent.md. 它应该跟踪功能的工作方式并报告文件位置的结果，而无需更改存储库。将其工具配置为仅使用 Copilot 线束的文件读取和代码搜索工具。排除可以编辑文件、运行 shell 命令或委托给其他 agents 的工具。

查看生成的文件中的工具白名单。确认它仅包含预期的阅读和搜索工具，而不是不受限制的工具访问。
在 处创建一个名为研究人员的自定义工作区 agent。claude/agents/researcher.md. 它应该跟踪功能的工作方式并报告文件位置的发现，而无需更改存储库。将其工具限制为 Read、Grep 和 Glob。

检查生成的 agent 的工具 frontmatter，以确认它将 agent 限制为那些阅读和搜索工具。了解有关 Claude 自定义 agents 和工具访问的更多信息。
在 VS Code 中为 Codex 创建名为研究员的工作区自定义 agent。github/agents/researcher.agent.md。它应该跟踪功能的工作原理并报告文件位置的结果。指示它不要编辑文件或运行更改存储库的命令。

VS Code 中的 Codex 集成从该文件加载角色指令，但不使用其工具 frontmatter 作为工具白名单。将禁止编辑指令视为指导，而不是强制执行。使用 agent 之前，请检查 Codex 权限。

生成agent后：

检查并保存文件。检查说明是否定义了明确的角色，并且不与您的项目指南相冲突。

使用相同的工具和存储库开始新的聊天，然后从 agents 下拉列表中选择研究员。

要求它研究某个功能的工作原理。检查它是否识别相关文件、用证据支持调查结果并遵循其配置的角色。

检查任何更改和工具活动，而不是假设 agent 的名称或说明使其只读。

了解有关定制 agents 的更多信息。

你配置了什么

如果您完成了可选步骤，您的存储库可以包含所选线束的以下文件。前端目录是一个例子；使用存储库的结构。
CopilotClaudeCodex
your-project/
 .github/
 copilot-instructions.md
 instructions/
 frontend.instructions.md
技能/
验证更改/
 SKILL.md
 agents/
 researcher.agent.md

your-project/
 CLAUDE.md
 .claude/
 规则/
 frontend.md
 技能/
 验证更改/
 SKILL.md
 agents/
 researcher.md

your-project/
 AGENTS.md
 包/
 前端/
 AGENTS.md
 .agents/
技能/
验证更改/
SKILL.md
.github/
 agents/
 researcher.agent.md

项目说明提供了基线。有针对性的指令集中指导部分代码库。技能包重复工作流程和自定义 agents 定义具有特定于线束的功能的专门角色。随着存储库需求的增长，您可以独立采用每一层。

通过与项目说明相同的流程查看和共享任何可选的自定义文件。包括技能的支持资源，以便其他贡献者可以使用它。

后续步骤

从 Agent 自定义编辑器创建和管理自定义。

添加MCP服务器以将agent连接到外部工具和服务。

当操作必须在特定的 agent 生命周期事件中运行时设置挂钩。

                
                
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

Configure AI for your codebase

	

	
	
	
	
	

	
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
            
              
                    
        
        
        
            
            
                
                    
                    
On this page there are 11 sectionsOn this page
                    
                        
                        
Prerequisites
                        
                        
Establish a baseline
                        
                        
Step 1: Create project instructions
                        
                        
Step 2: Review the generated instructions
                        
                        
Step 3: Verify the improvement
                        
                        
Step 4: Share the configuration
                        
                        
Step 5: Add targeted instructions (optional)
                        
                        
Step 6: Package a recurring workflow (optional)
                        
                        
Step 7: Add a specialized agent (optional)
                        
                        
What you configured
                        
                        
Next steps
                        
                    
                    
                
                
            
            
            
            
                
                
Configure AI for your codebase
                
                

	
		
			
			Copy as Markdown
		
		
			
		
		
			

				
					
					Copy as Markdown
				
			
			

				
					
					View as Markdown
					
				
			
			

			

				
					
					Ask in VS Code
				
			
			

				
					
					Ask in VS Code Insiders
				
			
		
	
	

                
                
                

AI agents can produce better results when they understand how your codebase is structured, which commands to run, and which conventions to follow. Configure this information once as repository customizations instead of repeating it in every prompt.

This guide helps you configure the GitHub Copilot, Anthropic Claude, or OpenAI Codex harness in Visual Studio Code. Start with an observed project problem and a representative task. Make the smallest useful customization, confirm that it applies, repeat the task, and compare the results before you share the configuration. The workflow is shared, with tabs for harness-specific formats and behavior.

To understand how the customization types differ and work together, see Agent customization.

Prerequisites

Download and install Visual Studio Code.

Complete the setup and authentication for your harness.

Open the repository root that you want to configure.

Start a session and select Copilot, Claude, or Codex from the Session Target control. Use the same harness when creating and testing its customizations.

For this walkthrough, work in the current folder. If you're using the Agents window, leave New Worktree unselected so new sessions can access your uncommitted customization files.

Choose the tabs for your harness, not your language model. For example, a Claude model in a Copilot session still uses the Copilot harness.

      
        
        Note
      
The Local harness also supports the Copilot file layout shown in this guide, but tool names and some activation behavior differ. For Local-specific configuration, see custom instructions, agent skills, and custom agents.

Establish a baseline

Choose one project-specific problem that occurs repeatedly, such as using the wrong test command, placing files in the wrong directory, or selecting a library that your project doesn't use. A one-time requirement belongs in the current request rather than in shared project configuration.

Define a small representative task with a clear success criterion. Record the current result or use evidence from a recent task:

Which files the agent creates or changes.

Which project patterns and libraries it follows.

Which commands and tests it runs, including failures or skipped checks.

Which corrections you have to provide.

If the result already meets your success criterion, stop. You don't need a customization for that behavior. For a coding task, see the add a feature guide for guidance on establishing baseline tests and verifying the outcome.

Step 1: Create project instructions

Start with a project instructions file for information that applies across your codebase. Focus the initial change on the observed problem and decisions the agent can't reliably infer from the repository. Each harness has its own discovery rules, so use its expected file name and location.

Open the Agent Customizations editor for the selected harness. In the Chat view, run Chat: Open Customizations from the Command Palette (⇧⌘P (Windows, Linux Ctrl+Shift+P)).

In the Overview section, enter the prompt for your harness below and press Enter to submit it.

Continue in the chat that opens. Confirm that it uses the intended harness and repository, and answer any clarifying questions.

Review the changes and save the instructions in the expected location. If the file already exists, review the update rather than replacing existing guidance without checking it.

CopilotClaudeCodex
Use .github/copilot-instructions.md at the repository root for project-wide instructions.

Analyze this codebase and create or update .github/copilot-instructions.md with project-wide instructions. Cover architecture, important directories, build and test commands, coding conventions, and requirements for completing a change. Preserve useful existing guidance, avoid duplication, and ask about anything you cannot determine from the repository.

Use CLAUDE.md at the repository root for project-wide instructions.

Analyze this codebase and create or update CLAUDE.md at the repository root with project-wide instructions. Cover architecture, important directories, build and test commands, coding conventions, and requirements for completing a change. Preserve useful existing guidance, avoid duplication, and ask about anything you cannot determine from the repository.

Use AGENTS.md at the repository root for project-wide instructions.

Analyze this codebase and create or update AGENTS.md at the repository root with project-wide instructions. Cover architecture, important directories, build and test commands, coding conventions, and requirements for completing a change. Preserve useful existing guidance, avoid duplication, and ask about anything you cannot determine from the repository.

      
        
        Tip
      
Project instructions are most useful when they document decisions the agent cannot reliably infer from the code alone. Avoid generic advice that applies to any project.

Step 2: Review the generated instructions

Treat generated instructions as a starting point. Check that they contain accurate, project-specific information:

Observed problem: include only the guidance needed to improve the baseline behavior.

Architecture: describe important directories, component boundaries, and where to add different types of code.

Commands: include the correct build, test, lint, and formatting commands.

Technology choices: identify preferred frameworks, libraries, and patterns.

Conventions: capture naming, error-handling, testing, security, and documentation requirements.

Definition of done: state which validations the agent should run before completing a task.

Remove information that is generic, obsolete, or duplicated elsewhere in the file. Resolve conflicting instructions and keep each rule concise.

For example, project-specific instructions might include:

## Project structure

* Add API routes under `src/api/routes`.
* Put shared validation schemas in `src/schemas`.
* Keep database access in the repository layer.

## Validation

* Run the unit tests for the changed package.
* Run the linter before completing a code change.
* Add or update tests for every behavior change.

Learn more about writing effective custom instructions.

Step 3: Verify the improvement

Confirm that the instructions apply, and then repeat the representative task from your baseline. Keep the harness, model, tools, task, and relevant context the same where practical so that the customization is the main change.

In the Agent Customizations editor, select Instructions for your harness and confirm that the project instructions file is listed. This checks discovery, not whether the agent follows every instruction.

Start a new chat with the same harness and repository so the comparison doesn't rely on guidance from the creation conversation.

Expand References in the response and confirm that the expected instructions were included.

Repeat the representative task with the same success criterion.

Compare the result with the baseline. Review file placement, project patterns, libraries, tool activity, commands, tests, and errors rather than relying on the agent's summary.

If the file is missing, invalid, or included but not followed, use the agent customization troubleshooting guide to identify the failure before adding more instructions.

If the instructions are included but the result still misses the success criterion, clarify one relevant rule and repeat the task in another new chat. Stop when the task meets the criterion. More instructions consume context and can introduce conflicts without improving the result.

For a broader controlled comparison that includes quality, reliability, credits, tokens, duration, and tool calls, use the usage optimization loop.

Step 4: Share the configuration

Commit the project instructions file and share it through your repository's normal pull request or review process. Contributors who obtain the updated files and use a compatible harness can reuse the guidance without recreating it.

Review the instructions like other development configuration. Use your existing ownership and review process to make responsibility for the file clear. Update it when the architecture, commands, dependencies, or team practices change, and remove stale or duplicate rules.

If your team uses multiple harnesses, keep shared guidance consistent and avoid contradictory copies. A file supported by one harness isn't automatically supported by every other harness.

If the representative task now meets the success criterion, stop. Your repository has a useful baseline customization. The remaining steps are optional. Add them when different parts of the codebase need distinct guidance or when your team repeatedly performs the same workflow.

Step 5: Add targeted instructions (optional)

Add targeted instructions when different parts of your codebase need different guidance. For example, frontend code and infrastructure code might follow different conventions. Keep project-wide standards in the instructions file from step 1.

In the Agent Customizations editor's Overview section, submit a request to create the file for your harness below. Describe the conventions it should cover and use an existing file type or directory from your repository.
CopilotClaudeCodex
Create .github/instructions/frontend.instructions.md. For TypeScript React files, include this YAML frontmatter before the Markdown instructions:

---
applyTo: "**/*.tsx"
---

The pattern matches .tsx files throughout the repository. Review the pattern and test the instructions by asking the agent to make a small change to a specific matching file.

Learn more about file-based instructions.
Create .claude/rules/frontend.md. Claude uses paths rather than applyTo for path-scoped rules:

---
paths:
  - "**/*.tsx"
---

Review the pattern and test the rules by asking the agent to make a small change to a specific matching file. Without paths, the rule file applies generally rather than only to matching files.

Learn more about Claude path-specific rules.
Use an AGENTS.md file in the directory that needs its own guidance, such as packages/frontend/AGENTS.md. Codex discovers instructions from the repository root down to the session's working directory. This is directory-based guidance, not an applyTo or paths glob.

To test the nested instructions, start a new Codex session with that subdirectory as its working folder. Opening a file in the editor doesn't change the session's working directory.

Learn more about Codex instruction discovery.

Review and save the generated file, then test it in a new chat with the same harness. Confirm that the result follows the targeted conventions. If it doesn't, check discovery and scope before revising the instructions.

If you changed the session's working folder to test nested instructions, return to the repository root before continuing.

Step 6: Package a recurring workflow (optional)

Create an agent skill when your team repeatedly explains the same multi-step process. A skill can include instructions, scripts, templates, examples, and other resources that the agent loads when relevant.

For example, create a validate-change skill that selects and runs the relevant checks for a code change, then reports what passed, failed, or wasn't run. Submit the prompt for your harness in the Agent Customizations editor's Overview section:
CopilotClaudeCodex
Create a workspace skill named validate-change at .github/skills/validate-change/SKILL.md. It should inspect a code change, choose and run the relevant tests and lint checks using this repository's commands, and report what passed, failed, or was not run. Describe when to use it in its frontmatter.

Create a workspace skill named validate-change at .claude/skills/validate-change/SKILL.md. It should inspect a code change, choose and run the relevant tests and lint checks using this repository's commands, and report what passed, failed, or was not run. Describe when to use it in its frontmatter.

Create a workspace skill named validate-change at .agents/skills/validate-change/SKILL.md. It should inspect a code change, choose and run the relevant tests and lint checks using this repository's commands, and report what passed, failed, or was not run. Describe when to use it in its frontmatter.

After generating the skill:

Review the SKILL.md file. Check that its name matches the directory name and its description explains what it does and when to use it.

Review any scripts, templates, or other resources in the skill directory, and save your changes.

Start a new chat with the same harness and repository. Type / in the chat input and select validate-change to invoke the skill explicitly. Identify the code change to validate.

Review the tool activity and results. Check that the agent loads the skill, selects appropriate checks, and reports failures or skipped checks accurately.

You can also test automatic selection in a separate chat by asking the agent to validate a change without naming the skill. If it doesn't select the skill, review the description and invocation settings. Explicit invocation and model-selected use are separate checks.

Learn more about agent skills.

Step 7: Add a specialized agent (optional)

Create a custom agent when a role needs focused instructions. For example, a codebase researcher can investigate implementation details and report findings before you decide what to change. Tool restrictions depend on the harness, so don't assume a role description alone prevents edits.

In the Agent Customizations editor's Overview section, submit the prompt for your harness:
CopilotClaudeCodex
Create a workspace custom agent named researcher at .github/agents/researcher.agent.md. It should trace how features work and report findings with file locations, without changing the repository. Configure its tools to use only the Copilot harness's file-reading and code-search tools. Exclude tools that can edit files, run shell commands, or delegate to other agents.

Review the tools allowlist in the generated file. Confirm that it contains only the intended reading and search tools, rather than unrestricted tool access.
Create a workspace custom agent named researcher at .claude/agents/researcher.md. It should trace how features work and report findings with file locations, without changing the repository. Restrict its tools to Read, Grep, and Glob.

Review the generated agent's tools frontmatter to confirm that it limits the agent to those reading and search tools. Learn more about Claude custom agents and tool access.
Create a workspace custom agent named researcher at .github/agents/researcher.agent.md for Codex in VS Code. It should trace how features work and report findings with file locations. Instruct it not to edit files or run commands that change the repository.

The Codex integration in VS Code loads the role instructions from this file, but doesn't use its tools frontmatter as a tool allowlist. Treat the no-edit instruction as guidance, not enforcement. Review Codex permissions before using the agent.

After generating the agent:

Review and save the file. Check that the instructions define a clear role and don't conflict with your project guidance.

Start a new chat with the same harness and repository, and select researcher from the agents dropdown.

Ask it to research how a feature works. Check that it identifies relevant files, supports findings with evidence, and follows its configured role.

Review any changes and tool activity rather than assuming the agent's name or instructions make it read-only.

Learn more about custom agents.

What you configured

If you completed the optional steps, your repository can contain the following files for your selected harness. The frontend directory is an example; use the structure of your repository.
CopilotClaudeCodex
your-project/
  .github/
    copilot-instructions.md
    instructions/
      frontend.instructions.md
    skills/
      validate-change/
        SKILL.md
    agents/
      researcher.agent.md

your-project/
  CLAUDE.md
  .claude/
    rules/
      frontend.md
    skills/
      validate-change/
        SKILL.md
    agents/
      researcher.md

your-project/
  AGENTS.md
  packages/
    frontend/
      AGENTS.md
  .agents/
    skills/
      validate-change/
        SKILL.md
  .github/
    agents/
      researcher.agent.md

Project instructions provide the baseline. Targeted instructions focus guidance on part of the codebase. Skills package recurring workflows, and custom agents define specialized roles with harness-specific capabilities. You can adopt each layer independently as your repository's needs grow.

Review and share any optional customization files through the same process as the project instructions. Include a skill's supporting resources so other contributors can use it.

Next steps

Create and manage customizations from the Agent Customizations editor.

Add MCP servers to connect the agent to external tools and services.

Set up hooks when an action must run at a specific agent lifecycle event.

                
                
                10/7/2026
                
            
        
        
        
        
            
        
    

		
	

	
		
			
				
					
						
						
							
								
								
							
						
					
				
				
			
			
				
			
			
		
	

	
		
			
				
					
						

							
						
						

							
						
						

							
						
						

							
						
						

							
						
						

							
						
						

							
						
						

							
						
						

							
						
						
					
					
						
					
				
			
			
				
					
Support
					
Privacy
					
Manage Cookies
					
Terms of Use
					
License
				
			
			
				
					

						
						Your Privacy Choices
					
Consumer Health Privacy


</details>
