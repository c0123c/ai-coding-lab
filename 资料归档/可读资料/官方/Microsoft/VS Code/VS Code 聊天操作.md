# VS Code 聊天操作

介绍发送代码问题与任务、提供上下文、查看响应和审查生成修改，帮助控制编辑器中的聊天工作流。

- 来源：Microsoft／VS Code
- 资料类型：存档文档
- 文档时间：官方审核：[2026-10-07](<https://code.visualstudio.com/docs/chat/chat-overview>)；官方文件提交（截至抓取）：[2026-10-06](<https://github.com/microsoft/vscode-docs/commit/4fe6b03408632f2e9338a970d26afa480e4a022d>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://code.visualstudio.com/docs/chat/chat-overview>) · [原始文件](<../../../../原始抓取备份/原文/官方/Microsoft/VS Code/VS Code 聊天操作.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

在 VS Code 中使用聊天

	

	
	
	
	
	

	
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
            
              
                    
        
        
        
            
            
                
                    
                    
本页有15sectionsOn此页
                    
                        
                        
选择聊天地点
                        
                        
发送聊天请求
                        
                        
从聊天中运行终端命令
                        
                        
在请求运行时发送消息
                        
                        
为您的提示添加上下文
                        
                        
图片轮播
                        
                        
审查和管理变更
                        
                        
获取有关聊天回复的通知
                        
                        
在聊天会话中查找文本
                        
                        
在聊天会话中的提示之间导航
                        
                        
个性化聊天
                        
                        
获得更好的回应
                        
                        
解决聊天交互问题
                        
                        
支持
                        
                        
相关资源
                        
                    
                    
                
                
                
                    

                        
相关
                    
                    
                    
                    
开始使用 agents
                        
                        
按照实践教程使用 VS Code 中的 AI agents 构建应用程序。
                        
                        
                            
                            
                            
开始代理编码教程
                            
                            
                            
                        
                    
                    
                
                
            
            
            
            
                
                
在 VS Code 中使用聊天
                
                

	
		
			
			复制为 Markdown
		
		
			
		
		
			

				
					
					复制为 Markdown
				
			
			

				
					
					查看为 Markdown
					
				
			
			

			

				
					
					询问VS Code
				
			
			

				
					
					在 VS Code 业内人士中询问
				
			
		
	
	

                
                
                

Visual Studio Code 中的聊天可让您使用自然语言与 AI agents 进行交互。询问有关代码的问题、获得理解复杂逻辑的帮助、生成新功能、修复错误等等，所有这些都通过对话界面进行。

本文介绍了 VS Code 中聊天界面共享的交互机制，包括如何发送和引导请求、添加上下文、导航对话以及个性化聊天显示。有关 AI 工作流程、agents 和可用接口的介绍，请参阅 VS Code 中的 AI 构建。

选择聊天地点

本文中的交互机制适用于聊天视图、聊天编辑器选项卡和 Agents 窗口。要将这些界面与浏览器和终端选项进行比较，请参阅使用方法 agents。

对于轻量级交互，可以使用内联聊天来编辑代码或获取适当的终端建议，或者打开快速聊天进行临时对话。

发送聊天请求

在聊天输入框中键入消息，然后按 Enter 或选择“发送”按钮。 agent 可以回答问题、分析代码、运行工具、进行更改并以摘要进行响应。然后，您可以通过后续消息继续对话。例如，您可以要求 agent 进行此更改：

将输入验证添加到注册表单

要进行免提对话，请从聊天输入启动语音模式。语音模式监听后续请求并大声读出 agent 的响应。
  
语音模式是实验性的，可能会更改或删除。

要提供额外的特定于项目的上下文，您可以通过使用 # 提及引用文件、符号或其他信息来将上下文添加到提示中。

对于常见任务，您可以使用斜杠命令作为常用提示的快捷方式或调用 agent skills。在聊天输入中键入 / 以查看所有可用命令。

您可以并行运行多个会话并在它们之间切换，而不会丢失上下文。在管理 agent 会话中了解更多信息。

每个会话都有控制 agent 响应方式的控件：会话目标、Agent、语言模型和权限。了解如何配置 agent 会话。

      
        
提示
      
为了获得最佳结果，请具体说明您想要什么，提供相关背景并编写清晰的说明。有关详细信息，请参阅获得更好的响应。

从聊天中运行终端命令

开始一条消息！直接从聊天视图或 Agents 窗口运行终端命令。这 ！必须是第一个字符，并且后面必须跟一个命令。

例如，输入以下消息来运行测试：

!npm测试

该命令立即在会话的默认 shell 中运行，而不将消息发送到 agent 或请求批准。它使用会话文件夹或工作树作为工作目录。命令输出和退出状态显示在聊天记录中。

      
        
注意事项
      
的！命令仅在 Agent 主机会话中可用。

在请求运行时发送消息

您无需等待回复完成即可发送下一条消息。当请求正在进行时，“发送”按钮会变为一个下拉列表，为您提供如何处理新消息的三个选项。

添加到队列：您的消息将等待并在当前响应完成后自动发送。当前响应不间断地完成。

通过消息转向：在完成当前工具执行后发出当前请求让步的信号。当前响应停止，您的新消息立即处理。当 agent 朝错误方向行驶时，使用此功能可对其进行重定向。

停止并发送：完全取消当前请求并立即发送新消息。

停止请求不会撤消文件编辑、终端命令或已完成的其他操作。要恢复受影响的工作区文件和聊天历史记录，请恢复检查点。检查点不会逆转对外部服务的更改。

有关何时引导或停止请求以及如何编写重点更正的示例，请参阅让 agent 回到正轨。

发送按钮的默认操作是可配置的。使用
    
      
聊天。requestQueuing。defaultAction
      
    
    
        
    
    
在 VS Code
 中打开 在 VS Code 内部人员中打开
    
将其设置为引导（默认）或队列。

对待处理消息重新排序

当您有多个待处理消息（排队或转向）时，您可以拖放它们以更改它们的处理顺序。当多条相同类型的消息待处理时，鼠标悬停时会出现拖动手柄。

为您的提示添加上下文

提供正确的上下文有助于 AI 生成更相关和更准确的响应。

隐式上下文：VS Code 自动包含活动文件、当前选择以及文件名作为上下文。当您使用 agents 时，agent 会自主决定是否需要附加上下文。

#-提及：在聊天输入中键入 # 以显式引用文件 (#file)、文件夹、符号、代码库 (#codebase)、终端输出 (#terminalSelection) 或 #fetch 等工具。

愿景：附加图像，例如屏幕截图或 UI 模型，作为提示的上下文。

浏览器元素：从集成浏览器中选择元素以将 HTML、CSS 和屏幕截图上下文添加到提示中。

了解有关管理 AI 上下文的更多信息。

图片轮播

您可以选择聊天回复中的图像或视频来打开专用的轮播视图。来自工具结果（例如集成浏览器、Playwright 或其他 MCP 服务器）的媒体文件和内嵌的助理消息都可以从轮播中访问。

要禁用图像轮播，请设置
    
      
imageCarousel.chat.enabled
      
    
    
        
    
    
在 VS Code
 中打开 在 VS Code 内部人员中打开
    
为假。

审查和管理变更

AI 更改文件后，请在提交或集成之前检查并验证结果。在聊天视图中，选择响应中已更改的文件以打开其差异。在 Agents 窗口中，使用“更改”面板。

要通过键盘和屏幕阅读器访问差异，请使用可访问差异查看器。

有关请求修订、恢复检查点以及集成文件夹或工作树更改的说明，请参阅查看 AI- 生成的代码编辑。

获取有关聊天回复的通知

当您在其他窗口或应用程序中工作时，VS Code 可以向您发送 OS 通知，让您了解重要的聊天事件，这样您就不必不断查看。

使用
    
      
聊天。notifyWindowOnResponseReceived
      
    
    
        
    
    
在 VS Code
 中打开 在 VS Code 内部人员中打开
    
配置在收到聊天回复时何时收到 OS 通知。该通知包括响应的预览，选择它会将焦点转移到聊天会话。

使用
    
      
聊天。notifyWindowOnConfirmation
      
    
    
        
    
    
在 VS Code
 中打开 在 VS Code 内部人员中打开
    
配置当agent需要您输入或确认才能继续时，您收到OS通知。

这两个设置都有三个可能的值：

off：从不显示通知

windowNotFocused（默认）：仅当VS Code窗口未聚焦时显示通知

始终：即使 VS Code 窗口处于焦点状态也显示通知

      
        
提示
      
如果您想在 VS Code 的其他部分工作时（例如在后台运行长时间的 agent 任务时）保持对聊天活动的了解，请将该值设置为始终。

在聊天会话中查找文本

按 ⌘F（Windows、Linux Ctrl+F）搜索整个对话。查找可在聊天视图、聊天编辑器选项卡和 Agents 窗口中使用。它搜索提示和响应，包括屏幕外内容和代码块。

使用 Enter 和 ⇧Enter（Windows、Linux Shift+Enter）在匹配项之间导航。当比赛位于折叠的“已完成 N 步”部分内时，导航到该比赛会展开该部分。使用“区分大小写”、“全字匹配”和“正则表达式”选项来优化结果。

      
        
注意事项
      
查找在快速聊天或内联聊天中不可用。它不会搜索工具丸或崩溃推理中的文本。

在聊天会话中的提示之间导航

使用以下键盘快捷键在聊天会话中的提示之间导航：

⌥⌘↑（Windows、Linux Ctrl+Alt+Up）：Go 到聊天会话中的上一个提示。

⌥⌘↓（Windows、Linux Ctrl+Alt+Down）：Go 到聊天会话中的下一个提示。

⌥⌘PageUp（Windows、Linux Ctrl+Alt+PageUp）：Go 到聊天会话中的上一个代码块。

⌥⌘PageDown（Windows、Linux Ctrl+Alt+PageDown）：Go 到聊天会话中的下一个代码块。

要使用键盘或屏幕阅读器逐字符或逐行检查聊天响应，请运行打开辅助视图 (⌥F2 (Windows Alt+F2、Linux Shift+Alt+F2))。了解有关无障碍视图的更多信息。

个性化聊天

调整聊天内容的显示方式、添加互动宠物或在 Agents 窗口中设置装饰背景。

自定义聊天显示

使用这些设置来调整聊天记录：

显示选项
设置

Markdown 字体
设置字体系列
    
      
聊天。fontFamily
      
    
    
        
    
    
在 VS Code
 中打开 在 VS Code 内部人员中打开
    
和字体大小
    
      
聊天。fontSize
      
    
    
        
    
    
在 VS Code
 中打开 在 VS Code 内部人员中打开
    
  .

代码块字体和布局
设置字体系列、大小、粗细和行高
    
      
聊天.编辑器。fontFamily
      
    
    
        
    
    
在 VS Code
 中打开 在 VS Code 内部人员中打开
    
  , 
    
      
聊天.编辑器。fontSize
      
    
    
        
    
    
在 VS Code
 中打开 在 VS Code 内部人员中打开
    
  , 
    
      
聊天.编辑器。fontWeight
      
    
    
        
    
    
在 VS Code
 中打开 在 VS Code 内部人员中打开
    
, 和
    
      
聊天.编辑器。lineHeight
      
    
    
        
    
    
在 VS Code
 中打开 在 VS Code 内部人员中打开
    
。控制换行
    
      
聊天.编辑器。wordWrap
      
    
    
        
    
    
在 VS Code
 中打开 在 VS Code 内部人员中打开
    
  .

置顶提示
使用
    
      
聊天。stickyScroll.启用
      
    
    
        
    
    
在 VS Code
 中打开 在 VS Code 内部人员中打开
    
在滚动时将当前提示固定到记录的顶部。

请求时间戳
使用
    
      
聊天详细信息
      
    
    
        
    
    
在 VS Code
 中打开 在 VS Code 内部人员中打开
    
显示或隐藏请求和完成时间戳。将鼠标悬停在完成时间戳上可查看已用响应时间。

持续进步（实验）
使用
    
      
聊天.实验。persistentProgress
      
    
    
        
    
    
在 VS Code
 中打开 在 VS Code 内部人员中打开
    
保持进度指示器可见，直到 agent 响应完成。选择彩色或单色绘制动画，或关闭指示器。

随着持续的进展，推理出现在单独的可折叠预览中，并且工具调用在运行时保持可见。
    
      
聊天.实验。persistentProgressVerbosity
      
    
    
        
    
    
在 VS Code
 中打开 在 VS Code 内部人员中打开
    
控制已完成的工具调用详细信息。当响应文本继续或响应完成时，紧凑会用可扩展摘要替换已完成的组。详细使完整的细节可见。

持续进度在 Insiders 中默认为 Draw，在 Stable 中默认为 Off。实验可以在推出期间更改任一默认值，但显式设置始终优先。

有关更多聊天首选项，请参阅 AI 设置参考。

自定义【Agents】窗口聊天背景

实验性
 Agents 窗口聊天背景是实验性的，可能会更改或被删除。

在Agents窗口的聊天区域添加装饰背景，而不改变主VS Code窗口中的聊天。

要设置背景，请从命令面板运行聊天：设置背景...命令 (⇧⌘P (Windows、Linux Ctrl+Shift+P)) 或右键单击聊天的空白区域。然后，选择以下选项之一：

无背景：删除当前颜色主题的背景。

Codicons：使用内置 VS Code 图标的主题感知模式。

图像...：从您的机器中选择图像。

最近使用的：重复使用最近的五张背景图像之一。

对于图像背景，请运行聊天：更改背景布局... 以针对当前颜色主题沿边缘或角落重复、拉伸、居中或定位图像。浏览布局选项可预览每一项。选择一个选项进行保存，或关闭选择器以恢复以前的布局。

      
        
注意事项
      
浅色和深色主题具有单独的背景和图像布局。这些选择存储在当前计算机上，并且不会跨设备同步。

背景隐藏在高对比度主题中，以保持聊天的可读性。当高对比度主题处于活动状态时，背景命令也不可用。

获得更好的回应

聊天提供了多种方法来提高 AI 响应的质量和相关性：

编写有效的提示：具体说明您想要什么，引用相关文件和符号，并使用 / 命令来执行常见任务。从提示示例中获取灵感或查看完整的提示工程指南。

自定义 AI：通过添加自定义指令、创建可重用提示文件或为专门工作流程构建自定义 agents，根据您的项目定制 AI 的行为。例如，创建一个“代码审阅者”agent，提供有关代码质量和遵守团队编码标准的反馈。

使用工具扩展：连接 MCP 服务器或安装提供工具的扩展，以使 agent 能够访问外部服务、数据库或 API。

有关更多信息，请参阅在 VS Code 中自定义 agent 行为。

解决聊天交互问题

使用 Agent 日志和聊天调试视图来检查发送提示时发生的情况。 Agent 日志显示工具调用、LLM 请求和提示文件发现的按时间顺序排列的事件日志。聊天调试视图显示每次交互的原始系统提示、用户提示、上下文和工具负载。这些工具对于理解 AI 以某种方式响应的原因或排除意外结果非常有用。

支持

对 GitHub Copilot 聊天的支持由 GitHub 提供，可以通过 https://support.github.com. 联系

要了解有关 Copilot 的安全性、隐私性、合规性和透明度的更多信息，请参阅 GitHub Copilot 信任中心 FAQ。

相关资源

创建和管理 agent 会话

选择会话目标并配置权限

提示示例

                
                
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

Use chat in VS Code

	

	
	
	
	
	

	
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
            
              
                    
        
        
        
            
            
                
                    
                    
On this page there are 15 sectionsOn this page
                    
                        
                        
Choose where to chat
                        
                        
Send a chat request
                        
                        
Run terminal commands from chat
                        
                        
Send messages while a request is running
                        
                        
Add context to your prompts
                        
                        
Image carousel
                        
                        
Review and manage changes
                        
                        
Get notified about chat responses
                        
                        
Find text in a chat session
                        
                        
Navigate between prompts in a chat session
                        
                        
Personalize chat
                        
                        
Get better responses
                        
                        
Troubleshoot chat interactions
                        
                        
Support
                        
                        
Related resources
                        
                    
                    
                
                
                
                    

                        
                        Related
                    
                    
                    
                    
                        Get started with agents
                        
                        
Follow a hands-on tutorial to build an app with AI agents in VS Code.
                        
                        
                            
                            
                            
                                Start agentic coding tutorial
                            
                            
                            
                        
                    
                    
                
                
            
            
            
            
                
                
Use chat in VS Code
                
                

	
		
			
			Copy as Markdown
		
		
			
		
		
			

				
					
					Copy as Markdown
				
			
			

				
					
					View as Markdown
					
				
			
			

			

				
					
					Ask in VS Code
				
			
			

				
					
					Ask in VS Code Insiders
				
			
		
	
	

                
                
                

Chat in Visual Studio Code lets you use natural language for interacting with AI agents. Ask questions about your code, get help understanding complex logic, generate new features, fix bugs, and more, all through a conversational interface.

This article describes interaction mechanics shared by chat surfaces in VS Code, including how to send and steer requests, add context, navigate a conversation, and personalize the chat display. For an introduction to AI workflows, agents, and the available interfaces, see Build with AI in VS Code.

Choose where to chat

The interaction mechanics in this article apply to the Chat view, chat editor tabs, and the Agents window. To compare these interfaces with browser and terminal options, see Ways to work with agents.

For lightweight interactions, use inline chat to edit code or get terminal suggestions in place, or open Quick Chat for a temporary conversation.

Send a chat request

Type your message in the chat input box and press Enter or select the Send button. The agent can answer questions, analyze your code, run tools, make changes, and respond with a summary. You can then continue the conversation with follow-up messages. For example, you might ask an agent to make this change:

Add input validation to the signup form

For a hands-free conversation, start Voice Mode from the chat input. Voice Mode listens for follow-up requests and reads the agent's responses aloud. 
  
  Voice Mode is experimental and might change or be removed.

To give extra project-specific context, you can add context to your prompt by referencing files, symbols, or other information with #-mentions.

For common tasks, you can use slash commands as shortcuts for frequently used prompts or to invoke agent skills. Type / in the chat input to see all available commands.

You can run multiple sessions in parallel and switch between them without losing context. Learn more in Manage agent sessions.

Each session has controls that shape how the agent responds: Session Target, Agent, Language model, and Permissions. Learn how to configure an agent session.

      
        
        Tip
      
To get the best results, be specific about what you want, provide relevant context, and write clear instructions. For more information, see Get better responses.

Run terminal commands from chat

Start a message with ! to run a terminal command directly from the Chat view or Agents window. The ! must be the first character and must be followed by a command.

For example, enter the following message to run your tests:

!npm test

The command runs immediately in the session's default shell, without sending the message to the agent or asking for approval. It uses the session folder or worktree as the working directory. The command output and exit status appear in the chat transcript.

      
        
        Note
      
The ! command is only available in Agent Host sessions.

Send messages while a request is running

You don't have to wait for a response to finish before sending your next message. While a request is in progress, the Send button changes to a dropdown that gives you three options for how to handle the new message.

Add to Queue: your message waits and sends automatically after the current response completes. The current response finishes uninterrupted.

Steer with Message: signals the current request to yield after finishing the current tool execution. The current response stops and your new message processes immediately. Use this to redirect the agent when it's heading in the wrong direction.

Stop and Send: cancels the current request entirely and sends your new message right away.

Stopping a request doesn't undo file edits, terminal commands, or other actions that already completed. To restore affected workspace files and chat history, restore a checkpoint. Checkpoints don't reverse changes to external services.

For examples of when to steer or stop a request and how to write a focused correction, see Get an agent back on track.

The default action for the Send button is configurable. Use 
    
      
      chat.requestQueuing.defaultAction
      
    
    
        
    
    
      Open in VS Code
      Open in VS Code Insiders
    
   to set it to steer (default) or queue.

Reorder pending messages

When you have multiple pending messages (queued or steering), you can drag and drop them to change the order in which they are processed. A drag handle appears on hover when more than one message of the same type is pending.

Add context to your prompts

Providing the right context helps the AI generate more relevant and accurate responses.

Implicit context: VS Code automatically includes the active file, your current selection, and the file name as context. When you use agents, the agent decides autonomously if additional context is needed.

#-mentions: type # in the chat input to explicitly reference files (#file), folders, symbols, your codebase (#codebase), terminal output (#terminalSelection), or tools like #fetch.

Vision: attach images, such as screenshots or UI mockups, as context for your prompt.

Browser elements: select elements from the integrated browser to add HTML, CSS, and screenshot context to your prompt.

Learn more about managing context for AI.

Image carousel

You can select images or videos in chat responses to open a dedicated carousel view. Media files from tool results (such as the integrated browser, Playwright, or other MCP servers) and inlined in assistant messages are all accessible from the carousel.

To disable the image carousel, set 
    
      
      imageCarousel.chat.enabled
      
    
    
        
    
    
      Open in VS Code
      Open in VS Code Insiders
    
   to false.

Review and manage changes

After the AI changes files, review and validate the result before you commit or integrate it. In the Chat view, select a changed file in the response to open its diff. In the Agents window, use the Changes panel.

For keyboard and screen reader access to a diff, use the Accessible Diff Viewer.

For instructions about requesting revisions, restoring checkpoints, and integrating folder or worktree changes, see Review AI-generated code edits.

Get notified about chat responses

When you're working in another window or application, VS Code can send you OS notifications to let you know about important chat events, so you don't have to keep checking back.

Use 
    
      
      chat.notifyWindowOnResponseReceived
      
    
    
        
    
    
      Open in VS Code
      Open in VS Code Insiders
    
   to configure when you receive an OS notification when a chat response is received. The notification includes a preview of the response, and selecting it brings focus to the chat session.

Use 
    
      
      chat.notifyWindowOnConfirmation
      
    
    
        
    
    
      Open in VS Code
      Open in VS Code Insiders
    
   to configure when you receive an OS notification when the agent needs your input or confirmation to continue.

Both settings have three possible values:

off: never show notifications

windowNotFocused (default): show notifications only when the VS Code window is not focused

always: show notifications even when the VS Code window is in focus

      
        
        Tip
      
Set the value to always if you want to stay aware of chat activity while working in other parts of VS Code, such as when running long agent tasks in the background.

Find text in a chat session

Press ⌘F (Windows, Linux Ctrl+F) to search the entire conversation. Find is available in the Chat view, chat editor tabs, and the Agents window. It searches prompts and responses, including off-screen content and code blocks.

Use Enter and ⇧Enter (Windows, Linux Shift+Enter) to navigate between matches. When a match is inside a collapsed Completed N steps section, navigating to the match expands the section. Use the Match Case, Whole Word, and Regular Expression options to refine the results.

      
        
        Note
      
Find is not available in Quick Chat or inline chat. It does not search text inside tool pills or collapsed reasoning.

Navigate between prompts in a chat session

Use the following keyboard shortcuts to navigate between prompts in a chat session:

⌥⌘↑ (Windows, Linux Ctrl+Alt+Up): Go to the previous prompt in the chat session.

⌥⌘↓ (Windows, Linux Ctrl+Alt+Down): Go to the next prompt in the chat session.

⌥⌘PageUp (Windows, Linux Ctrl+Alt+PageUp): Go to the previous code block in the chat session.

⌥⌘PageDown (Windows, Linux Ctrl+Alt+PageDown): Go to the next code block in the chat session.

To inspect a chat response character by character or line by line with a keyboard or screen reader, run Open Accessible View (⌥F2 (Windows Alt+F2, Linux Shift+Alt+F2)). Learn more about the Accessible View.

Personalize chat

Adjust how chat content appears, add an interactive pet, or set a decorative background in the Agents window.

Customize the chat display

Use these settings to adjust the chat transcript:

Display option
Settings

Markdown font
Set the font family with 
    
      
      chat.fontFamily
      
    
    
        
    
    
      Open in VS Code
      Open in VS Code Insiders
    
   and the font size with 
    
      
      chat.fontSize
      
    
    
        
    
    
      Open in VS Code
      Open in VS Code Insiders
    
  .

Code block font and layout
Set the font family, size, weight, and line height with 
    
      
      chat.editor.fontFamily
      
    
    
        
    
    
      Open in VS Code
      Open in VS Code Insiders
    
  , 
    
      
      chat.editor.fontSize
      
    
    
        
    
    
      Open in VS Code
      Open in VS Code Insiders
    
  , 
    
      
      chat.editor.fontWeight
      
    
    
        
    
    
      Open in VS Code
      Open in VS Code Insiders
    
  , and 
    
      
      chat.editor.lineHeight
      
    
    
        
    
    
      Open in VS Code
      Open in VS Code Insiders
    
  . Control line wrapping with 
    
      
      chat.editor.wordWrap
      
    
    
        
    
    
      Open in VS Code
      Open in VS Code Insiders
    
  .

Sticky prompts
Use 
    
      
      chat.stickyScroll.enabled
      
    
    
        
    
    
      Open in VS Code
      Open in VS Code Insiders
    
   to pin the current prompt to the top of the transcript while you scroll.

Request timestamps
Use 
    
      
      chat.verbose
      
    
    
        
    
    
      Open in VS Code
      Open in VS Code Insiders
    
   to show or hide request and completion timestamps. Hover over a completion timestamp to view the elapsed response time.

Persistent progress (Experimental)
Use 
    
      
      chat.experimental.persistentProgress
      
    
    
        
    
    
      Open in VS Code
      Open in VS Code Insiders
    
   to keep a progress indicator visible until an agent response finishes. Choose a colored or monochrome Draw animation, or turn the indicator off.

With persistent progress, reasoning appears in separate collapsible previews and tool calls remain visible while they run. 
    
      
      chat.experimental.persistentProgressVerbosity
      
    
    
        
    
    
      Open in VS Code
      Open in VS Code Insiders
    
   controls completed tool-call details. Compact replaces completed groups with expandable summaries when response text resumes or the response finishes. Verbose keeps the full details visible.

Persistent progress defaults to Draw in Insiders and Off in Stable. Experiments can change either default during rollout, but an explicit setting always takes precedence.

For more chat preferences, see the AI settings reference.

Customize the Agents window chat background

  Experimental
  Agents window chat backgrounds is experimental and might change or be removed.

Add a decorative background to the chat area of the Agents window without changing chat in the main VS Code window.

To set a background, run the Chat: Set Background... command from the Command Palette (⇧⌘P (Windows, Linux Ctrl+Shift+P)) or right-click an empty area of the chat. Then, choose one of these options:

No Background: remove the background for the current color theme.

Codicons: use a theme-aware pattern of built-in VS Code icons.

Image...: select an image from your machine.

Recently used: reuse one of your five most recent background images.

For an image background, run Chat: Change Background Layout... to repeat, stretch, center, or position the image along an edge or in a corner for the current color theme. Moving through the layout options previews each one. Select an option to save it, or dismiss the picker to restore the previous layout.

      
        
        Note
      
Light and dark color themes have separate backgrounds and image layouts. These choices are stored on the current machine and don't sync across devices.

Backgrounds are hidden in high contrast themes to preserve chat readability. The background commands are also unavailable while a high contrast theme is active.

Get better responses

Chat provides several ways to improve the quality and relevance of AI responses:

Write effective prompts: be specific about what you want, reference relevant files and symbols, and use / commands for common tasks. Get inspired by prompt examples or review the full prompt engineering guide.

Customize the AI: tailor the AI's behavior to your project by adding custom instructions, creating reusable prompt files, or building custom agents for specialized workflows. For example, create a "Code Reviewer" agent that provides feedback on code quality and adherence to your team's coding standards.

Extend with tools: connect MCP servers or install extensions that contribute tools to give the agent access to external services, databases, or APIs.

For more information, see Customize agent behavior in VS Code.

Troubleshoot chat interactions

Use Agent Logs and the Chat Debug view to inspect what happens when you send a prompt. Agent Logs shows a chronological event log of tool calls, LLM requests, and prompt file discovery. The Chat Debug view shows the raw system prompt, user prompt, context, and tool payloads for each interaction. These tools are useful for understanding why the AI responded in a certain way or for troubleshooting unexpected results.

Support

Support for GitHub Copilot Chat is provided by GitHub and can be reached at https://support.github.com.

To learn more about Copilot's security, privacy, compliance, and transparency, see the GitHub Copilot Trust Center FAQ.

Related resources

Create and manage agent sessions

Choose a session target and configure permissions

Prompt examples

                
                
                10/7/2026
                
            
        
        
        
        
            
        
    

		
	

	
		
			
				
					
						
						
							
								
								
							
						
					
				
				
			
			
				
			
			
		
	

	
		
			
				
					
						

							
						
						

							
						
						

							
						
						

							
						
						

							
						
						

							
						
						

							
						
						

							
						
						

							
						
						
					
					
						
					
				
			
			
				
					
Support
					
Privacy
					
Manage Cookies
					
Terms of Use
					
License
				
			
			
				
					

						
						Your Privacy Choices
					
Consumer Health Privacy


</details>
