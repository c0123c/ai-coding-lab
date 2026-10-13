# 用 Google ADK 构建多工具 Agent

通过安装、项目结构、模型认证及本地运行示例展示工具接入；属于通用 Agent 开发入门，并非专门的编码 Agent 教程。

- 来源：Google／Agent Development Kit
- 资料类型：存档文档
- 存档版本：`12712d00d487`。
- 文档时间：源码提交（存档版）：[2026-09-10](<https://github.com/google/adk-docs/commit/66b0cbca8d6a5f40e8892212f555f117015dd098>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/google/adk-docs/blob/main/docs/tutorials/multi-tool-agent.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Agent Development Kit/用 Google ADK 构建多工具 Agent.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 打造多功能工具 agent

<div class="language-support-tag">
 <span class="lst-supported">ADK</span><span class="lst-python">Python v0.1.0</span><span class="lst-typescript">TypeScript v0.2.0</span><span class="lst-go">Go v0.1.0</span><span class="lst-java">Java v0.1.0</span><span class="lst-kotlin">Kotlin v0.1.0</span>
</div> 支持

本快速入门将指导您安装 Agent 开发套件 (ADK)、
 使用多个工具设置基本 agent，并在 
 终端或基于浏览器的交互式开发 UI 中本地运行它。

<!-- 

![快速入门设置](<https://raw.githubusercontent.com/google/adk-docs/12712d00d48712998f15a2a9d8bd26d708b727a3/assets/quickstart.png>)

 -->

本快速入门假设本地 IDE（VS Code、PyCharm、IntelliJ IDEA 等）包含 
Python 3.10+ 或 Java 17+和终端访问。此方法完全在您的计算机上运行应用程序
，建议用于内部开发。

## 1。设置环境并安装 ADK { #set-up-environment-install-adk }

=== "Python"

创建并激活虚拟环境（推荐）：

    ```bash
    # Create
    python3 -m venv .venv
    # Activate (each new terminal)
    # macOS/Linux: source .venv/bin/activate
    # Windows CMD: .venv\Scripts\activate.bat
    # Windows PowerShell: .venv\Scripts\Activate.ps1
    ```

安装ADK：

    ```bash
    pip install google-adk
    ```

=== "TypeScript"

创建一个新的项目目录，初始化它，并安装依赖项：

    ```bash
    mkdir my-adk-agent
    cd my-adk-agent
    npm init -y
    npm install @google/adk @google/adk-devtools
    npm install -D typescript
    ```

创建一个包含以下内容的 `tsconfig.json` 文件。此配置
可确保您的项目正确处理现代Node.js模块。

    ```json title="tsconfig.json"
    {
      "compilerOptions": {
        "target": "es2020",
        "module": "nodenext",
        "moduleResolution": "nodenext",
        "esModuleInterop": true,
        "strict": true,
        "skipLibCheck": true,
        // set to false to allow CommonJS module syntax:
        "verbatimModuleSyntax": false
      }
    }
    ```

=== "Go"

    ## 创建一个新的Go模块

如果您要开始一个新项目，您可以创建一个新的 Go 模块：

    ```bash
    mkdir my-adk-agent
    cd my-adk-agent
    go mod init example.com/my-agent
    ```

    ## 安装ADK

要将 ADK 添加到您的项目中，请运行以下命令：

    ```bash
    go get google.golang.org/adk/v2
    ```

这会将 ADK 作为依赖项添加到您的 `go.mod` 文件中。

=== "Java"

要安装ADK Java并设置环境，请参阅[Java
快速入门](<https://google.github.io/adk-docs/get-started/java/>)。

=== "Kotlin"

要安装ADK Kotlin并设置环境，请参阅[Kotlin
快速入门](<https://google.github.io/adk-docs/get-started/kotlin/>)。

## 2。创建Agent项目{ #create-agent-project }

### 项目结构

=== "Python"

您将需要创建以下项目结构：

    ```console
    parent_folder/
        multi_tool_agent/
            __init__.py
            agent.py
            .env
    ```

创建文件夹`multi_tool_agent`：

    ```bash
    mkdir multi_tool_agent/
    ```

！！！信息“Windows 用户注意事项”

在 Windows 上使用 ADK 执行接下来的几个步骤时，我们建议使用文件资源管理器或 IDE 创建 
 Python 文件，因为以下命令 
（`mkdir`、`echo`）通常会生成包含空字节的文件and/or 不正确的
 编码。

    ### `__init__.py`

现在在文件夹中创建一个`__init__.py`文件：

    ```shell
    echo "from . import agent" > multi_tool_agent/__init__.py
    ```

您的 `__init__.py` 现在应该如下所示：

    ```python title="multi_tool_agent/__init__.py"
    --8<-- "examples/python/snippets/get-started/multi_tool_agent/__init__.py"
    ```

    ### `agent.py`

在同一文件夹中创建 `agent.py` 文件：

===“OS X &amp; Linux”
        ```shell
        touch multi_tool_agent/agent.py
        ```

===“视窗”
        ```shell
        type nul > multi_tool_agent/agent.py
        ```

将以下代码复制并粘贴到`agent.py`中：

    ```python title="multi_tool_agent/agent.py"
    --8<-- "examples/python/snippets/get-started/multi_tool_agent/agent.py"
    ```

    ### `.env`

在同一文件夹中创建一个`.env`文件：

===“OS X &amp; Linux”
        ```shell
        touch multi_tool_agent/.env
        ```

===“视窗”
        ```shell
        type nul > multi_tool_agent\.env
        ```

有关此文件的更多说明将在下一节 [设置模型 ](<#set-up-the-model>) 中进行描述。

=== "TypeScript"

您需要在 `my-adk-agent` 目录中创建以下项目结构：

    ```console
    my-adk-agent/
        agent.ts
        .env
        package.json
        tsconfig.json
    ```

    ### `agent.ts`

在项目文件夹中创建一个 `agent.ts` 文件：

===“OS X &amp; Linux”
        ```shell
        touch agent.ts
        ```

===“视窗”
        ```shell
        type nul > agent.ts
        ```

将以下代码复制并粘贴到`agent.ts`中：

    ```typescript title="agent.ts"
    --8<-- "examples/typescript/snippets/get-started/multi_tool_agent/agent.ts"
    ```

    ### `.env`

在同一文件夹中创建一个`.env`文件：

===“OS X &amp; Linux”
        ```shell
        touch .env
        ```

===“视窗”
        ```shell
        type nul > .env
        ```

有关此文件的更多说明将在下一节 [设置模型 ](<#set-up-the-model>) 中进行描述。

=== "Go"

您将需要创建以下项目结构：

    ```console
    my-adk-agent/
        agent.go
        .env
        go.mod
    ```

    ### `agent.go`

在项目文件夹中创建一个 `agent.go` 文件：

===“OS X &amp; Linux”
        ```bash
        touch agent.go
        ```

===“视窗”
        ```console
        type nul > agent.go
        ```

将以下代码复制并粘贴到`agent.go`中：

    ```go title="agent.go"
    --8<-- "examples/go/snippets/get-started/multi_tool_agent/main.go:full_code"
    ```

    ### `.env`

在同一文件夹中创建一个`.env`文件：

===“OS X &amp; Linux”
        ```bash
        touch .env
        ```

===“视窗”
        ```console
        type nul > .env
        ```

=== "Java"

Java项目一般具有以下项目结构：

    ```console
    project_folder/
    ├── pom.xml (or build.gradle)
    ├── src/
    ├── └── main/
    │       └── java/
    │           └── agents/
    │               └── multitool/
    └── test/
    ```

    ### 创建`MultiToolAgent.java`

在`src/main/java/agents/multitool/`目录下的`agents.multitool`包
中创建`MultiToolAgent.java`源文件。

将以下代码复制并粘贴到`MultiToolAgent.java`中：

    ```java title="agents/multitool/MultiToolAgent.java"
    --8<-- "examples/java/cloud-run/src/main/java/agents/multitool/MultiToolAgent.java:full_code"
    ```

=== "Kotlin"

Kotlin项目一般具有以下项目结构：

    ```console
    project_folder/
    ├── build.gradle.kts
    ├── src/
    ├── └── main/
    │       └── kotlin/
    │           └── agents/
    │               └── multitool/
    ```

    ### 创建`MultiToolAgent.kt`

在`src/main/kotlin/agents/multitool/`目录中创建`MultiToolAgent.kt`源文件。

将以下代码复制并粘贴到`MultiToolAgent.kt`中：

    ```kotlin title="src/main/kotlin/agents/multitool/MultiToolAgent.kt"
    --8<-- "examples/kotlin/snippets/get-started/multi_tool_agent/MultiToolAgent.kt"
    ```

![intro_components.png](<https://raw.githubusercontent.com/google/adk-docs/12712d00d48712998f15a2a9d8bd26d708b727a3/docs/assets/quickstart-flow-tool.png>)

## 3。设置模型 { #set-up-the-model }

您的 agent 理解用户请求并生成响应的能力是由生成 AI 模型或大型语言模型 (LLM) 提供支持的 
。本指南使用 Gemini 型号作为 
 示例，但 ADK 与 Google 和其他 
 提供商的许多 AI 型号兼容。有关可用型号以及如何配置 
 的更多信息，请参阅 [AI ADK 的型号 agents](<https://google.github.io/adk-docs/agents/models/>)。

### 模型连接和认证

通过服务（例如 Google 云上的 Gemini API 或 Gemini
企业 Agent 平台）使用 AI 模型时，您必须提供 API 密钥或
通过服务进行身份验证。提供此信息的最直接方法
是使用环境变量或`.env`文件。以下示例显示
配置agent以与GeminiAPI或Gemini
企业Agent平台一起使用的最常见方法。

=== "Gemini API"

    ```
    # .env configuration file
    GOOGLE_API_KEY="PASTE_YOUR_GEMINI_API_KEY_HERE"
    ```

===“Google云Agent平台”

    ```
    # .env configuration file
    GOOGLE_CLOUD_PROJECT=your-project-id
    GOOGLE_CLOUD_LOCATION=location-code        # example: us-central1
    GOOGLE_GENAI_USE_ENTERPRISE=True
    ```

有关将 ADK agents 连接到 Google 云托管模型和服务 
 包括 Gemini 企业 Agent 平台的更多详细信息，请参阅
[连接到 Google 云和Agent平台](<https://google.github.io/adk-docs/get-started/google-cloud/>)指南。

## 4。运行你的Agent { #run-your-agent }

=== "Python"

使用终端，导航到 agent 项目 
 的父目录（例如使用 `cd ..`）：

    ```console
    parent_folder/      <-- navigate to this directory
        multi_tool_agent/
            __init__.py
            agent.py
            .env
    ```

有多种方式与您的 agent 互动：

===“开发UI（adk网络）”

！！！ success "Agent平台用户的认证设置"
 如果您在上一步中选择了**"Gemini - Google云Agent平台"**，则必须通过Google 启动开发之前的云UI。

运行此命令并按照提示操作：
            ```bash
            gcloud auth application-default login
            ```

**注意：** 如果您使用“Gemini - Google AI Studio”，请跳过此步骤。

运行以下命令启动 **dev UI**。

        ```shell
        adk web
        ```

！！！警告“警告：ADK Web 仅用于开发”

ADK Web ***不适合在生产部署中使用***。您应该
使用ADK Web仅用于开发和调试目的。

!!!info“Windows 用户注意事项”

当点击 `_make_subprocess_transport NotImplementedError` 时，请考虑使用 `adk web --no-reload`。


**步骤1：**直接在浏览器中打开提供的URL（通常是`http://localhost:8000`或
 `http://127.0.0.1:8000`）。

**步骤2。** 在UI的左上角，您可以在
下拉列表中选择您的agent。选择“multi_tool_agent”。

！！！注意“故障排除”

如果您在下拉菜单中没有看到“multi_tool_agent”，请确保您
正在**父文件夹**中运行`adk web`，该文件夹位于agent文件夹
的父文件夹中multi_tool_agent）。

**步骤3。**现在您可以使用文本框与您的agent聊天：

        ![adk-web-dev-ui-chat.png](<https://raw.githubusercontent.com/google/adk-docs/12712d00d48712998f15a2a9d8bd26d708b727a3/docs/assets/adk-web-dev-ui-chat.png>)


**步骤4。**通过使用左侧的`Events`选项卡，您可以通过单击
操作来检查
单个函数调用、响应和模型响应：

        ![adk-web-dev-ui-function-call.png](<https://raw.githubusercontent.com/google/adk-docs/12712d00d48712998f15a2a9d8bd26d708b727a3/docs/assets/adk-web-dev-ui-function-call.png>)

在 `Events` 选项卡上，您还可以单击 `Trace` 按钮查看每个事件的跟踪日志，其中显示每个函数调用的延迟：

        ![adk-web-dev-ui-trace.png](<https://raw.githubusercontent.com/google/adk-docs/12712d00d48712998f15a2a9d8bd26d708b727a3/docs/assets/adk-web-dev-ui-trace.png>)

**步骤5。**您还可以启用麦克风并与agent通话：

！！！注意“模型支持 voice/video 流”

为了在ADK中使用voice/video流，您需要使用支持直播API的Gemini型号。您可以在文档中找到支持 Gemini Live API 的 ** 型号 ID(s)**：

            - [Google AI 工作室：Gemini 现场 API](<https://ai.google.dev/gemini-api/docs/models#live-api>)
            - [Agent 平台：Gemini 直播 API](<https://cloud.google.com/vertex-ai/generative-ai/docs/live-api>)

然后，您可以替换之前创建的 `agent.py` 文件中 `root_agent` 中的 `model` 字符串（[跳转到 ](<#agentpy>) 部分）。您的代码应该类似于：

            ```py
            root_agent = Agent(
                name="weather_time_agent",
                model="replace-me-with-model-id", #e.g. gemini-live-2.5-flash-native-audio
                ...
            ```

        ![adk-web-dev-ui-audio.png](<https://raw.githubusercontent.com/google/adk-docs/12712d00d48712998f15a2a9d8bd26d708b727a3/docs/assets/adk-web-dev-ui-audio.png>)

===“终端（adk运行）”

！！！小费

使用 `adk run` 时，您可以将提示注入到 agent 中，以通过 
 管道文本到命令启动，如下所示：

            ```shell
            echo "Please start by listing files" | adk run multi_tool_agent
            ```

运行以下命令，与您的天气 agent 聊天。

        ```
        adk run multi_tool_agent
        ```

        ![adk-run.png](<https://raw.githubusercontent.com/google/adk-docs/12712d00d48712998f15a2a9d8bd26d708b727a3/docs/assets/adk-run.png>)

要退出，请使用 Cmd/Ctrl+C.

===“API服务器（adk api_server）”

`adk api_server` 使您能够在单个 
 命令中创建本地 FastAPI 服务器，使您能够在部署 
 agent 之前测试本地 cURL 请求。

        ![adk-api-server.png](<https://raw.githubusercontent.com/google/adk-docs/12712d00d48712998f15a2a9d8bd26d708b727a3/docs/assets/adk-api-server.png>)

要了解如何使用`adk api_server`进行测试，请参阅
 [有关使用API服务器](<https://google.github.io/adk-docs/runtime/api-server/>)的文档。

=== "TypeScript"

使用终端导航到您的 agent 项目目录：

    ```console
    my-adk-agent/      <-- navigate to this directory
        agent.ts
        .env
        package.json
        tsconfig.json
    ```

有多种方式与您的 agent 互动：

===“开发UI（adk网络）”

运行以下命令启动 **dev UI**。

        ```shell
        npx adk web
        ```

**步骤1：**直接在浏览器中打开提供的URL（通常是`http://localhost:8000`或
 `http://127.0.0.1:8000`）。

**步骤2。** 在UI的左上角，从下拉列表中选择您的agent。 agents 按文件名列出，因此您应该选择“agent”。

！！！注意“故障排除”

如果您在下拉菜单中没有看到“agent”，请确保您
正在包含`agent.ts`文件的目录中运行`npx adk web`。

**步骤3。**现在您可以使用文本框与您的agent聊天：

        ![adk-web-dev-ui-chat.png](<https://raw.githubusercontent.com/google/adk-docs/12712d00d48712998f15a2a9d8bd26d708b727a3/docs/assets/adk-web-dev-ui-chat.png>)


**步骤4。**通过使用左侧的`Events`选项卡，您可以通过单击
操作来检查
单个函数调用、响应和模型响应：

        ![adk-web-dev-ui-function-call.png](<https://raw.githubusercontent.com/google/adk-docs/12712d00d48712998f15a2a9d8bd26d708b727a3/docs/assets/adk-web-dev-ui-function-call.png>)

在 `Events` 选项卡上，您还可以单击 `Trace` 按钮查看每个事件的跟踪日志，其中显示每个函数调用的延迟：

        ![adk-web-dev-ui-trace.png](<https://raw.githubusercontent.com/google/adk-docs/12712d00d48712998f15a2a9d8bd26d708b727a3/docs/assets/adk-web-dev-ui-trace.png>)

===“终端（adk运行）”

运行以下命令与您的agent聊天。

        ```
        npx adk run agent.ts
        ```

        ![adk-run.png](<https://raw.githubusercontent.com/google/adk-docs/12712d00d48712998f15a2a9d8bd26d708b727a3/docs/assets/adk-run.png>)

要退出，请使用 Cmd/Ctrl+C.

===“API服务器（adk api_server）”

`npx adk api_server` 使您能够在单个 
 命令中创建本地 Express.js 服务器，使您能够在部署 
 agent 之前测试本地 cURL 请求。

        ![adk-api-server.png](<https://raw.githubusercontent.com/google/adk-docs/12712d00d48712998f15a2a9d8bd26d708b727a3/docs/assets/adk-api-server.png>)

要了解如何使用`api_server`进行测试，请参阅
 [测试文档](<https://google.github.io/adk-docs/runtime/api-server/>)。

=== "Go"

使用终端导航到您的 agent 项目目录：

    ```console
    my-adk-agent/      <-- navigate to this directory
        agent.go
        .env
        go.mod
    ```

有多种方式与您的 agent 互动：

===“开发UI（网络）”

运行以下命令启动 **dev UI**。您必须指定要激活的子启动器（例如，`webui`、`api`）。

        ```bash
        go run agent.go web webui api
        ```

**步骤1：**直接在浏览器中打开提供的URL（通常是`http://localhost:8080`）。

**步骤2。** 在UI的左上角，从下拉列表中选择您的agent。应该是“weather_time_agent”。

**步骤3。** 现在您可以使用文本框与您的agent聊天。

===“终端（控制台）”

运行以下命令在终端中与您的agent聊天。

        ```bash
        go run agent.go console
        ```

**注意：** 如果 `console` 是代码中的第一个子启动器（与 `full.NewLauncher()` 一样），您也可以只运行 `go run agent.go`。

要退出，请使用 Cmd/Ctrl+C.

=== "Java"

使用终端，导航到 agent 项目 
 的父目录（例如使用 `cd ..`）：

    ```console
    project_folder/                <-- navigate to this directory
    ├── pom.xml (or build.gradle)
    ├── src/
    ├── └── main/
    │       └── java/
    │           └── agents/
    │               └── multitool/
    │                   └── MultiToolAgent.java
    └── test/
    ```

===“开发UI”

从终端运行以下命令以启动 Dev UI。

**DO NOT 更改Dev UI 服务器的主类名称。**

        ```console title="terminal"
        mvn exec:java \
            -Dexec.mainClass="com.google.adk.web.AdkWebServer" \
            -Dexec.args="--adk.agents.source-dir=src/main/java" \
            -Dexec.classpathScope="compile"
        ```

**步骤1：**直接在浏览器中打开提供的URL（通常是`http://localhost:8080`或
 `http://127.0.0.1:8080`）。

**步骤2。** 在UI的左上角，您可以在
下拉列表中选择您的agent。选择“multi_tool_agent”。

！！！注意“故障排除”

如果您在下拉菜单中没有看到“multi_tool_agent”，请确保您
正在Java源代码
所在位置（通常是`src/main/java`）运行`mvn`命令。

**步骤3。**现在您可以使用文本框与您的agent聊天：

        ![adk-web-dev-ui-chat.png](<https://raw.githubusercontent.com/google/adk-docs/12712d00d48712998f15a2a9d8bd26d708b727a3/docs/assets/adk-web-dev-ui-chat.png>)

**步骤4。**您还可以通过单击操作来检查单个函数调用、响应和
模型响应：

        ![adk-web-dev-ui-function-call.png](<https://raw.githubusercontent.com/google/adk-docs/12712d00d48712998f15a2a9d8bd26d708b727a3/docs/assets/adk-web-dev-ui-function-call.png>)

！！！警告“警告：ADK Web 仅用于开发”

ADK Web ***不适合在生产部署中使用***。您应该
使用ADK Web仅用于开发和调试目的。

===“马夫”

使用 Maven，使用以下命令运行 Java 类 
 的 `main()` 方法：

        ```console title="terminal"
        mvn compile exec:java -Dexec.mainClass="agents.multitool.MultiToolAgent"
        ```

===“摇篮”

使用 Gradle，`build.gradle` 或 `build.gradle.kts` 构建文件 
 的 `plugins` 部分应具有以下 Java 插件：

        ```groovy
        plugins {
            id('java')
            // other plugins
        }
        ```

然后，在构建文件的其他位置，在顶层，
 创建一个新任务来运行 agent 的 `main()` 方法：

        ```groovy
        tasks.register('runAgent', JavaExec) {
            classpath = sourceSets.main.runtimeClasspath
            mainClass = 'agents.multitool.MultiToolAgent'
        }
        ```

最后，在命令行上运行以下命令：

        ```console
        gradle runAgent
        ```

=== "Kotlin"

使用终端导航到您的 agent 项目目录：

    ```console
    project_folder/                <-- navigate to this directory
    ├── build.gradle.kts
    ├── src/
    ├── └── main/
    │       └── kotlin/
    │           └── agents/
    │               └── multitool/
    │                   └── MultiToolAgent.kt
    ```

    ### 运行你的Agent

您可以使用 Gradle 运行 Kotlin 类的 `main()` 方法：

    ```console
    ./gradlew run
    ```

或者，如果您使用的是IntelliJ IDEA，则只需单击`main()`功能旁边的绿色运行箭头即可。

### 📝 示例提示尝试

* 纽约 的天气怎么样？
* 纽约现在几点了？
* 巴黎 的天气怎么样？
* 巴黎现在几点了？

## 🎉恭喜！

您已经使用ADK成功创建了您的第一个agent并与之互动！

---

## 🛣️后续步骤

* **Go 教程 **：了解如何向 agent 添加内存、会话、状态：
[教程](<https://google.github.io/adk-docs/tutorials/>)。
* **深入研究高级配置：**探索[设置](<https://google.github.io/adk-docs/get-started/installation/>)
部分深入探讨项目结构、配置和其他 
 接口。
* **了解核心概念：**了解
[agents概念](<https://google.github.io/adk-docs/agents/>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Build a multi-tool agent

<div class="language-support-tag">
  <span class="lst-supported">Supported in ADK</span><span class="lst-python">Python v0.1.0</span><span class="lst-typescript">TypeScript v0.2.0</span><span class="lst-go">Go v0.1.0</span><span class="lst-java">Java v0.1.0</span><span class="lst-kotlin">Kotlin v0.1.0</span>
</div>

This quickstart guides you through installing Agent Development Kit (ADK),
setting up a basic agent with multiple tools, and running it locally either in
the terminal or in the interactive, browser-based dev UI.

<!-- 

![Quickstart setup](<https://raw.githubusercontent.com/google/adk-docs/12712d00d48712998f15a2a9d8bd26d708b727a3/assets/quickstart.png>)

 -->

This quickstart assumes a local IDE (VS Code, PyCharm, IntelliJ IDEA, etc.) with
Python 3.10+ or Java 17+ and terminal access. This method runs the application
entirely on your machine and is recommended for internal development.

## 1. Set up Environment & Install ADK { #set-up-environment-install-adk }

=== "Python"

    Create & Activate Virtual Environment (Recommended):

    ```bash
    # Create
    python3 -m venv .venv
    # Activate (each new terminal)
    # macOS/Linux: source .venv/bin/activate
    # Windows CMD: .venv\Scripts\activate.bat
    # Windows PowerShell: .venv\Scripts\Activate.ps1
    ```

    Install ADK:

    ```bash
    pip install google-adk
    ```

=== "TypeScript"

    Create a new project directory, initialize it, and install dependencies:

    ```bash
    mkdir my-adk-agent
    cd my-adk-agent
    npm init -y
    npm install @google/adk @google/adk-devtools
    npm install -D typescript
    ```

    Create a `tsconfig.json` file with the following content. This configuration
    ensures your project correctly handles modern Node.js modules.

    ```json title="tsconfig.json"
    {
      "compilerOptions": {
        "target": "es2020",
        "module": "nodenext",
        "moduleResolution": "nodenext",
        "esModuleInterop": true,
        "strict": true,
        "skipLibCheck": true,
        // set to false to allow CommonJS module syntax:
        "verbatimModuleSyntax": false
      }
    }
    ```

=== "Go"

    ## Create a new Go module

    If you are starting a new project, you can create a new Go module:

    ```bash
    mkdir my-adk-agent
    cd my-adk-agent
    go mod init example.com/my-agent
    ```

    ## Install ADK

    To add the ADK to your project, run the following command:

    ```bash
    go get google.golang.org/adk/v2
    ```

    This will add the ADK as a dependency to your `go.mod` file.

=== "Java"

    To install ADK Java and set up the environment, see the [Java
    Quickstart](<https://google.github.io/adk-docs/get-started/java/>).

=== "Kotlin"

    To install ADK Kotlin and set up the environment, see the [Kotlin
    Quickstart](<https://google.github.io/adk-docs/get-started/kotlin/>).

## 2. Create Agent Project { #create-agent-project }

### Project structure

=== "Python"

    You will need to create the following project structure:

    ```console
    parent_folder/
        multi_tool_agent/
            __init__.py
            agent.py
            .env
    ```

    Create the folder `multi_tool_agent`:

    ```bash
    mkdir multi_tool_agent/
    ```

    !!! info "Note for Windows users"

        When using ADK on Windows for the next few steps, we recommend creating
        Python files using File Explorer or an IDE because the following commands
        (`mkdir`, `echo`) typically generate files with null bytes and/or incorrect
        encoding.

    ### `__init__.py`

    Now create an `__init__.py` file in the folder:

    ```shell
    echo "from . import agent" > multi_tool_agent/__init__.py
    ```

    Your `__init__.py` should now look like this:

    ```python title="multi_tool_agent/__init__.py"
    --8<-- "examples/python/snippets/get-started/multi_tool_agent/__init__.py"
    ```

    ### `agent.py`

    Create an `agent.py` file in the same folder:

    === "OS X &amp; Linux"
        ```shell
        touch multi_tool_agent/agent.py
        ```

    === "Windows"
        ```shell
        type nul > multi_tool_agent/agent.py
        ```

    Copy and paste the following code into `agent.py`:

    ```python title="multi_tool_agent/agent.py"
    --8<-- "examples/python/snippets/get-started/multi_tool_agent/agent.py"
    ```

    ### `.env`

    Create a `.env` file in the same folder:

    === "OS X &amp; Linux"
        ```shell
        touch multi_tool_agent/.env
        ```

    === "Windows"
        ```shell
        type nul > multi_tool_agent\.env
        ```

    More instructions about this file are described in the next section on [Set up the model](<#set-up-the-model>).

=== "TypeScript"

    You will need to create the following project structure in your `my-adk-agent` directory:

    ```console
    my-adk-agent/
        agent.ts
        .env
        package.json
        tsconfig.json
    ```

    ### `agent.ts`

    Create an `agent.ts` file in your project folder:

    === "OS X &amp; Linux"
        ```shell
        touch agent.ts
        ```

    === "Windows"
        ```shell
        type nul > agent.ts
        ```

    Copy and paste the following code into `agent.ts`:

    ```typescript title="agent.ts"
    --8<-- "examples/typescript/snippets/get-started/multi_tool_agent/agent.ts"
    ```

    ### `.env`

    Create a `.env` file in the same folder:

    === "OS X &amp; Linux"
        ```shell
        touch .env
        ```

    === "Windows"
        ```shell
        type nul > .env
        ```

    More instructions about this file are described in the next section on [Set up the model](<#set-up-the-model>).

=== "Go"

    You will need to create the following project structure:

    ```console
    my-adk-agent/
        agent.go
        .env
        go.mod
    ```

    ### `agent.go`

    Create an `agent.go` file in your project folder:

    === "OS X &amp; Linux"
        ```bash
        touch agent.go
        ```

    === "Windows"
        ```console
        type nul > agent.go
        ```

    Copy and paste the following code into `agent.go`:

    ```go title="agent.go"
    --8<-- "examples/go/snippets/get-started/multi_tool_agent/main.go:full_code"
    ```

    ### `.env`

    Create a `.env` file in the same folder:

    === "OS X &amp; Linux"
        ```bash
        touch .env
        ```

    === "Windows"
        ```console
        type nul > .env
        ```

=== "Java"

    Java projects generally feature the following project structure:

    ```console
    project_folder/
    ├── pom.xml (or build.gradle)
    ├── src/
    ├── └── main/
    │       └── java/
    │           └── agents/
    │               └── multitool/
    └── test/
    ```

    ### Create `MultiToolAgent.java`

    Create a `MultiToolAgent.java` source file in the `agents.multitool` package
    in the `src/main/java/agents/multitool/` directory.

    Copy and paste the following code into `MultiToolAgent.java`:

    ```java title="agents/multitool/MultiToolAgent.java"
    --8<-- "examples/java/cloud-run/src/main/java/agents/multitool/MultiToolAgent.java:full_code"
    ```

=== "Kotlin"

    Kotlin projects generally feature the following project structure:

    ```console
    project_folder/
    ├── build.gradle.kts
    ├── src/
    ├── └── main/
    │       └── kotlin/
    │           └── agents/
    │               └── multitool/
    ```

    ### Create `MultiToolAgent.kt`

    Create a `MultiToolAgent.kt` source file in the `src/main/kotlin/agents/multitool/` directory.

    Copy and paste the following code into `MultiToolAgent.kt`:

    ```kotlin title="src/main/kotlin/agents/multitool/MultiToolAgent.kt"
    --8<-- "examples/kotlin/snippets/get-started/multi_tool_agent/MultiToolAgent.kt"
    ```

![intro_components.png](<https://raw.githubusercontent.com/google/adk-docs/12712d00d48712998f15a2a9d8bd26d708b727a3/docs/assets/quickstart-flow-tool.png>)

## 3. Set up the model { #set-up-the-model }

Your agent's ability to understand user requests and generate responses is
powered by a generative AI model or Large Language Model (LLM). This guide uses Gemini models as
examples, but ADK is compatible with many AI models from Google and other
providers. For more information on available models and how to configure
them, see [AI Models for ADK agents](<https://google.github.io/adk-docs/agents/models/>).

### Model connection and authentication

When using an AI model through a service, such as the Gemini API or Gemini
Enterprise Agent Platform on Google Cloud, you must provide an API key or
authenticate with the service. The most direct way to provide this information
is to use environment variables or an `.env` file. The following examples show
the most common way to configure an agent for use with the Gemini API or Gemini
Enterprise Agent Platform.

=== "Gemini API"

    ```
    # .env configuration file
    GOOGLE_API_KEY="PASTE_YOUR_GEMINI_API_KEY_HERE"
    ```

=== "Google Cloud Agent Platform"

    ```
    # .env configuration file
    GOOGLE_CLOUD_PROJECT=your-project-id
    GOOGLE_CLOUD_LOCATION=location-code        # example: us-central1
    GOOGLE_GENAI_USE_ENTERPRISE=True
    ```

For more details on connecting ADK agents to Google Cloud hosted models and services,
including Gemini Enterprise Agent Platform, see the
[Connect to Google Cloud and Agent Platform](<https://google.github.io/adk-docs/get-started/google-cloud/>) guide.

## 4. Run Your Agent { #run-your-agent }

=== "Python"

    Using the terminal, navigate to the parent directory of your agent project
    (e.g. using `cd ..`):

    ```console
    parent_folder/      <-- navigate to this directory
        multi_tool_agent/
            __init__.py
            agent.py
            .env
    ```

    There are multiple ways to interact with your agent:

    === "Dev UI (adk web)"

        !!! success "Authentication Setup for Agent Platform Users"
            If you selected **"Gemini - Google Cloud Agent Platform"** in the previous step, you must authenticate with Google Cloud before launching the dev UI.

            Run this command and follow the prompts:
            ```bash
            gcloud auth application-default login
            ```

            **Note:** Skip this step if you're using "Gemini - Google AI Studio".

        Run the following command to launch the **dev UI**.

        ```shell
        adk web
        ```

        !!! warning "Caution: ADK Web for development only"

            ADK Web is ***not meant for use in production deployments***. You should
            use ADK Web for development and debugging purposes only.

        !!!info "Note for Windows users"

            When hitting the `_make_subprocess_transport NotImplementedError`, consider using `adk web --no-reload` instead.


        **Step 1:** Open the URL provided (usually `http://localhost:8000` or
        `http://127.0.0.1:8000`) directly in your browser.

        **Step 2.** In the top-left corner of the UI, you can select your agent in
        the dropdown. Select "multi_tool_agent".

        !!!note "Troubleshooting"

            If you do not see "multi_tool_agent" in the dropdown menu, make sure you
            are running `adk web` in the **parent folder** of your agent folder
            (i.e. the parent folder of multi_tool_agent).

        **Step 3.** Now you can chat with your agent using the textbox:

        ![adk-web-dev-ui-chat.png](<https://raw.githubusercontent.com/google/adk-docs/12712d00d48712998f15a2a9d8bd26d708b727a3/docs/assets/adk-web-dev-ui-chat.png>)


        **Step 4.**  By using the `Events` tab at the left, you can inspect
        individual function calls, responses and model responses by clicking on the
        actions:

        ![adk-web-dev-ui-function-call.png](<https://raw.githubusercontent.com/google/adk-docs/12712d00d48712998f15a2a9d8bd26d708b727a3/docs/assets/adk-web-dev-ui-function-call.png>)

        On the `Events` tab, you can also click the `Trace` button to see the trace logs for each event that shows the latency of each function calls:

        ![adk-web-dev-ui-trace.png](<https://raw.githubusercontent.com/google/adk-docs/12712d00d48712998f15a2a9d8bd26d708b727a3/docs/assets/adk-web-dev-ui-trace.png>)

        **Step 5.** You can also enable your microphone and talk to your agent:

        !!!note "Model support for voice/video streaming"

            In order to use voice/video streaming in ADK, you will need to use Gemini models that support the Live API. You can find the **model ID(s)** that supports the Gemini Live API in the documentation:

            - [Google AI Studio: Gemini Live API](<https://ai.google.dev/gemini-api/docs/models#live-api>)
            - [Agent Platform: Gemini Live API](<https://cloud.google.com/vertex-ai/generative-ai/docs/live-api>)

            You can then replace the `model` string in `root_agent` in the `agent.py` file you created earlier ([jump to section](<#agentpy>)). Your code should look something like:

            ```py
            root_agent = Agent(
                name="weather_time_agent",
                model="replace-me-with-model-id", #e.g. gemini-live-2.5-flash-native-audio
                ...
            ```

        ![adk-web-dev-ui-audio.png](<https://raw.githubusercontent.com/google/adk-docs/12712d00d48712998f15a2a9d8bd26d708b727a3/docs/assets/adk-web-dev-ui-audio.png>)

    === "Terminal (adk run)"

        !!! tip

            When using `adk run` you can inject prompts into the agent to start by
            piping text to the command like so:

            ```shell
            echo "Please start by listing files" | adk run multi_tool_agent
            ```

        Run the following command, to chat with your Weather agent.

        ```
        adk run multi_tool_agent
        ```

        ![adk-run.png](<https://raw.githubusercontent.com/google/adk-docs/12712d00d48712998f15a2a9d8bd26d708b727a3/docs/assets/adk-run.png>)

        To exit, use Cmd/Ctrl+C.

    === "API Server (adk api_server)"

        `adk api_server` enables you to create a local FastAPI server in a single
        command, enabling you to test local cURL requests before you deploy your
        agent.

        ![adk-api-server.png](<https://raw.githubusercontent.com/google/adk-docs/12712d00d48712998f15a2a9d8bd26d708b727a3/docs/assets/adk-api-server.png>)

        To learn how to use `adk api_server` for testing, refer to the
        [documentation on using the API server](<https://google.github.io/adk-docs/runtime/api-server/>).

=== "TypeScript"

    Using the terminal, navigate to your agent project directory:

    ```console
    my-adk-agent/      <-- navigate to this directory
        agent.ts
        .env
        package.json
        tsconfig.json
    ```

    There are multiple ways to interact with your agent:

    === "Dev UI (adk web)"

        Run the following command to launch the **dev UI**.

        ```shell
        npx adk web
        ```

        **Step 1:** Open the URL provided (usually `http://localhost:8000` or
        `http://127.0.0.1:8000`) directly in your browser.

        **Step 2.** In the top-left corner of the UI, select your agent from the dropdown. The agents are listed by their filenames, so you should select "agent".

        !!!note "Troubleshooting"

            If you do not see "agent" in the dropdown menu, make sure you
            are running `npx adk web` in the directory containing your `agent.ts` file.

        **Step 3.** Now you can chat with your agent using the textbox:

        ![adk-web-dev-ui-chat.png](<https://raw.githubusercontent.com/google/adk-docs/12712d00d48712998f15a2a9d8bd26d708b727a3/docs/assets/adk-web-dev-ui-chat.png>)


        **Step 4.** By using the `Events` tab at the left, you can inspect
        individual function calls, responses and model responses by clicking on the
        actions:

        ![adk-web-dev-ui-function-call.png](<https://raw.githubusercontent.com/google/adk-docs/12712d00d48712998f15a2a9d8bd26d708b727a3/docs/assets/adk-web-dev-ui-function-call.png>)

        On the `Events` tab, you can also click the `Trace` button to see the trace logs for each event that shows the latency of each function calls:

        ![adk-web-dev-ui-trace.png](<https://raw.githubusercontent.com/google/adk-docs/12712d00d48712998f15a2a9d8bd26d708b727a3/docs/assets/adk-web-dev-ui-trace.png>)

    === "Terminal (adk run)"

        Run the following command to chat with your agent.

        ```
        npx adk run agent.ts
        ```

        ![adk-run.png](<https://raw.githubusercontent.com/google/adk-docs/12712d00d48712998f15a2a9d8bd26d708b727a3/docs/assets/adk-run.png>)

        To exit, use Cmd/Ctrl+C.

    === "API Server (adk api_server)"

        `npx adk api_server` enables you to create a local Express.js server in a single
        command, enabling you to test local cURL requests before you deploy your
        agent.

        ![adk-api-server.png](<https://raw.githubusercontent.com/google/adk-docs/12712d00d48712998f15a2a9d8bd26d708b727a3/docs/assets/adk-api-server.png>)

        To learn how to use `api_server` for testing, refer to the
        [documentation on testing](<https://google.github.io/adk-docs/runtime/api-server/>).

=== "Go"

    Using the terminal, navigate to your agent project directory:

    ```console
    my-adk-agent/      <-- navigate to this directory
        agent.go
        .env
        go.mod
    ```

    There are multiple ways to interact with your agent:

    === "Dev UI (web)"

        Run the following command to launch the **dev UI**. You must specify which sub-launchers to activate (e.g., `webui`, `api`).

        ```bash
        go run agent.go web webui api
        ```

        **Step 1:** Open the URL provided (usually `http://localhost:8080`) directly in your browser.

        **Step 2.** In the top-left corner of the UI, select your agent from the dropdown. It should be "weather_time_agent".

        **Step 3.** Now you can chat with your agent using the textbox.

    === "Terminal (console)"

        Run the following command to chat with your agent in the terminal.

        ```bash
        go run agent.go console
        ```

        **Note:** If `console` is the first sublauncher in your code (as it is with `full.NewLauncher()`), you can also just run `go run agent.go`.

        To exit, use Cmd/Ctrl+C.

=== "Java"

    Using the terminal, navigate to the parent directory of your agent project
    (e.g. using `cd ..`):

    ```console
    project_folder/                <-- navigate to this directory
    ├── pom.xml (or build.gradle)
    ├── src/
    ├── └── main/
    │       └── java/
    │           └── agents/
    │               └── multitool/
    │                   └── MultiToolAgent.java
    └── test/
    ```

    === "Dev UI"

        Run the following command from the terminal to launch the Dev UI.

        **DO NOT change the main class name of the Dev UI server.**

        ```console title="terminal"
        mvn exec:java \
            -Dexec.mainClass="com.google.adk.web.AdkWebServer" \
            -Dexec.args="--adk.agents.source-dir=src/main/java" \
            -Dexec.classpathScope="compile"
        ```

        **Step 1:** Open the URL provided (usually `http://localhost:8080` or
        `http://127.0.0.1:8080`) directly in your browser.

        **Step 2.** In the top-left corner of the UI, you can select your agent in
        the dropdown. Select "multi_tool_agent".

        !!!note "Troubleshooting"

            If you do not see "multi_tool_agent" in the dropdown menu, make sure you
            are running the `mvn` command at the location where your Java source code
            is located (usually `src/main/java`).

        **Step 3.** Now you can chat with your agent using the textbox:

        ![adk-web-dev-ui-chat.png](<https://raw.githubusercontent.com/google/adk-docs/12712d00d48712998f15a2a9d8bd26d708b727a3/docs/assets/adk-web-dev-ui-chat.png>)

        **Step 4.** You can also inspect individual function calls, responses and
        model responses by clicking on the actions:

        ![adk-web-dev-ui-function-call.png](<https://raw.githubusercontent.com/google/adk-docs/12712d00d48712998f15a2a9d8bd26d708b727a3/docs/assets/adk-web-dev-ui-function-call.png>)

        !!! warning "Caution: ADK Web for development only"

            ADK Web is ***not meant for use in production deployments***. You should
            use ADK Web for development and debugging purposes only.

    === "Maven"

        With Maven, run the `main()` method of your Java class
        with the following command:

        ```console title="terminal"
        mvn compile exec:java -Dexec.mainClass="agents.multitool.MultiToolAgent"
        ```

    === "Gradle"

        With Gradle, the `build.gradle` or `build.gradle.kts` build file
        should have the following Java plugin in its `plugins` section:

        ```groovy
        plugins {
            id('java')
            // other plugins
        }
        ```

        Then, elsewhere in the build file, at the top-level,
        create a new task to run the `main()` method of your agent:

        ```groovy
        tasks.register('runAgent', JavaExec) {
            classpath = sourceSets.main.runtimeClasspath
            mainClass = 'agents.multitool.MultiToolAgent'
        }
        ```

        Finally, on the command-line, run the following command:

        ```console
        gradle runAgent
        ```

=== "Kotlin"

    Using the terminal, navigate to your agent project directory:

    ```console
    project_folder/                <-- navigate to this directory
    ├── build.gradle.kts
    ├── src/
    ├── └── main/
    │       └── kotlin/
    │           └── agents/
    │               └── multitool/
    │                   └── MultiToolAgent.kt
    ```

    ### Run your Agent

    You can run the `main()` method of your Kotlin class using Gradle:

    ```console
    ./gradlew run
    ```

    Or if you are using IntelliJ IDEA, you can just click the green run arrow next to the `main()` function.

### 📝 Example prompts to try

* What is the weather in New York?
* What is the time in New York?
* What is the weather in Paris?
* What is the time in Paris?

## 🎉 Congratulations!

You've successfully created and interacted with your first agent using ADK!

---

## 🛣️ Next steps

* **Go to the tutorial**: Learn how to add memory, session, state to your agent:
  [tutorial](<https://google.github.io/adk-docs/tutorials/>).
* **Delve into advanced configuration:** Explore the [setup](<https://google.github.io/adk-docs/get-started/installation/>)
  section for deeper dives into project structure, configuration, and other
  interfaces.
* **Understand Core Concepts:** Learn about
  [agents concepts](<https://google.github.io/adk-docs/agents/>).


</details>
