# 定制 Qwen Code 容器沙箱

说明从源码构建 Docker 或 Podman 镜像、项目 Dockerfile 和链接操作；正文指出 npm 安装环境的构建限制。

- 来源：Qwen／Qwen Code
- 资料类型：存档文档
- 存档版本：`7e3de59d3aba`。
- 文档时间：源码提交（存档版）：[2026-03-06](<https://github.com/QwenLM/qwen-code/commit/3a549419baf215875b004254a46ddc6c9ac329b4>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/QwenLM/qwen-code/blob/main/docs/developers/tools/sandbox.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Qwen/Qwen Code/docs/developers/tools/sandbox.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

## 自定义沙箱环境（Docker/Podman）

### 目前该项目不支持通过npm包安装后使用BUILD_SANDBOX功能

1. 要构建自定义沙箱，您需要访问源代码存储库中的构建脚本（scripts/build_sandbox.js）。
2. 这些构建脚本不包含在npm发布的软件包中。
3. 该代码包含硬编码路径检查，明确拒绝来自非源代码环境的构建请求。

如果容器内部需要额外的工具（例如`git`、`python`、`rg`），请创建自定义的Dockerfile，具体操作如下

#### 1、首先克隆qwen code项目，https://github.com/QwenLM/qwen-code.git

#### 2、确保在源码仓库目录下执行以下操作

```bash
# 1. First, install the dependencies of the project
npm install

# 2. Build the Qwen Code project
npm run build

# 3. Verify that the dist directory has been generated
ls -la packages/cli/dist/

# 4. Create a global link in the CLI package directory
cd packages/cli
npm link

# 5. Verification link (it should now point to the source code)
which qwen
# Expected output: /xxx/xxx/.nvm/versions/node/v24.11.1/bin/qwen
# Or similar paths, but it should be a symbolic link

# 6. For details of the symbolic link, you can see the specific source code path
ls -la $(dirname $(which qwen))/../lib/node_modules/@qwen-code/qwen-code
# It should show that this is a symbolic link pointing to your source code directory

# 7.Test the version of qwen
qwen -v
# npm link will overwrite the global qwen. To avoid being unable to distinguish the same version number, you can uninstall the global CLI first

```

#### 3、在自己的项目根目录下创建你的沙箱Dockerfile

- 路径：`.qwen/sandbox.Dockerfile`

- 官方镜像地址：https://github.com/QwenLM/qwen-code/pkgs/container/qwen-code

```bash
# Based on the official Qwen sandbox image (It is recommended to explicitly specify the version)
FROM ghcr.io/qwenlm/qwen-code:sha-570ec43
# Add your extra tools here
RUN apt-get update && apt-get install -y \
    git \
    python3 \
    ripgrep
```

#### 4、在项目根目录下创建第一个沙箱镜像

```bash
QWEN_SANDBOX=docker BUILD_SANDBOX=1 qwen -s
# Observe whether the sandbox version of the tool you launched is consistent with the version of your custom image. If they are consistent, the startup will be successful
```

这将基于默认沙箱映像构建特定于项目的映像。

#### 删除npm链接

- 如果您想恢复qwen官方CLI，请删除npm链接

```bash
# Method 1: Unlink globally
npm unlink -g @qwen-code/qwen-code

# Method 2: Remove it in the packages/cli directory
cd packages/cli
npm unlink

# Verification has been lifted
which qwen
# It should display "qwen not found"

# Reinstall the global version if necessary
npm install -g @qwen-code/qwen-code

# Verification Recovery
which qwen
qwen --version
```


## 原文对照

<details>
<summary>展开完整原文</summary>

## Customizing the sandbox environment (Docker/Podman)

### Currently, the project does not support the use of the BUILD_SANDBOX function after installation through the npm package

1. To build a custom sandbox, you need to access the build scripts (scripts/build_sandbox.js) in the source code repository.
2. These build scripts are not included in the packages released by npm.
3. The code contains hard-coded path checks that explicitly reject build requests from non-source code environments.

If you need extra tools inside the container (e.g., `git`, `python`, `rg`), create a custom Dockerfile, The specific operation is as follows

#### 1、Clone qwen code project first, https://github.com/QwenLM/qwen-code.git

#### 2、Make sure you perform the following operation in the source code repository directory

```bash
# 1. First, install the dependencies of the project
npm install

# 2. Build the Qwen Code project
npm run build

# 3. Verify that the dist directory has been generated
ls -la packages/cli/dist/

# 4. Create a global link in the CLI package directory
cd packages/cli
npm link

# 5. Verification link (it should now point to the source code)
which qwen
# Expected output: /xxx/xxx/.nvm/versions/node/v24.11.1/bin/qwen
# Or similar paths, but it should be a symbolic link

# 6. For details of the symbolic link, you can see the specific source code path
ls -la $(dirname $(which qwen))/../lib/node_modules/@qwen-code/qwen-code
# It should show that this is a symbolic link pointing to your source code directory

# 7.Test the version of qwen
qwen -v
# npm link will overwrite the global qwen. To avoid being unable to distinguish the same version number, you can uninstall the global CLI first

```

#### 3、Create your sandbox Dockerfile under the root directory of your own project

- Path: `.qwen/sandbox.Dockerfile`

- Official mirror image address:https://github.com/QwenLM/qwen-code/pkgs/container/qwen-code

```bash
# Based on the official Qwen sandbox image (It is recommended to explicitly specify the version)
FROM ghcr.io/qwenlm/qwen-code:sha-570ec43
# Add your extra tools here
RUN apt-get update && apt-get install -y \
    git \
    python3 \
    ripgrep
```

#### 4、Create the first sandbox image under the root directory of your project

```bash
QWEN_SANDBOX=docker BUILD_SANDBOX=1 qwen -s
# Observe whether the sandbox version of the tool you launched is consistent with the version of your custom image. If they are consistent, the startup will be successful
```

This builds a project-specific image based on the default sandbox image.

#### Remove npm link

- If you want to restore the official CLI of qwen, please remove the npm link

```bash
# Method 1: Unlink globally
npm unlink -g @qwen-code/qwen-code

# Method 2: Remove it in the packages/cli directory
cd packages/cli
npm unlink

# Verification has been lifted
which qwen
# It should display "qwen not found"

# Reinstall the global version if necessary
npm install -g @qwen-code/qwen-code

# Verification Recovery
which qwen
qwen --version
```


</details>
