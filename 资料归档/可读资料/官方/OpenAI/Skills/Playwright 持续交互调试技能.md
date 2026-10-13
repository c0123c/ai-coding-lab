# Playwright 持续交互调试技能

通过保持浏览器或 Electron 会话进行迭代，组织功能和视觉检查、重载及截图验证，减少重复初始化。

- 来源：OpenAI／Skills
- 资料类型：存档文档
- 存档版本：`49f948faa925`。
- 文档时间：源码提交（存档版）：[2026-03-11](<https://github.com/openai/skills/commit/ecc1fb8bc0b82f1d3c7a92a82167ae8e8e613a67>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/skills/blob/main/skills/.curated/playwright-interactive/SKILL.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Skills/Playwright 持续交互调试技能.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: "playwright-interactive"
description: "Persistent browser and Electron interaction through `js_repl` for fast iterative UI debugging."
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 剧作家互动技巧

使用持久的 `js_repl` Playwright 会话来调试本地 Web 或 Electron 应用程序，在迭代中保持相同的句柄处于活动状态，并运行功能加视觉 QA，而无需重新启动整个工具链，除非进程所有权发生更改。

## 前提条件

- 该技能必须启用`js_repl`。
- 如果缺少`js_repl`，请在`~/.codex/config.toml`中启用它：

```toml
[features]
js_repl = true
```

- 您还可以使用 `--enable js_repl`（相当于 `-c features.js_repl=true`）开始新会话。
- 启用 `js_repl` 后，启动新的 Codex 会话，以便工具列表刷新。
- 现在，在禁用沙箱的情况下运行此工作流程：使用 `--sandbox danger-full-access`（或 `sandbox_mode=danger-full-access` 的等效配置）启动 Codex。这是临时要求，沙盒内的 `js_repl` + Playwright 支持仍在完成中。
- 从需要调试的同一项目目录运行安装程序。
- 将 `js_repl_reset` 视为恢复工具，而不是例行清理。重置内核会破坏您的 Playwright 句柄。

## 一次性设置

```bash
test -f package.json || npm init -y
npm install playwright
# Web-only, for headed Chromium or mobile emulation:
# npx playwright install chromium
# Electron-only, and only if the target workspace is the app itself:
# npm install --save-dev electron
node -e "import('playwright').then(() => console.log('playwright import ok')).catch((error) => { console.error(error); process.exit(1); })"
```

如果您稍后切换到其他工作区，请在那里重复设置。

## 核心工作流程

1. 测试前先写一个简短的【QA】盘点：
   - 从三个来源构建清单：用户请求的要求、您实际实现的用户可见的功能或行为以及您期望在最终响应中提出的声明。
   - 这三个来源中任何一个中出现的任何内容都必须在签核之前映射到至少一项 QA 检查。
   - 列出您打算签署的用户可见声明。
   - 列出每个有意义的面向用户的控制、模式切换或实现的交互行为。
   - 列出每个控件或实现的行为可能导致的状态更改或视图更改。
   - 将此用作功能 QA 和视觉 QA 的共享覆盖范围列表。
   - 对于每个声明或控制状态对，请记下预期的功能检查、必须进行目视检查的特定状态以及您期望捕获的证据。
   - 如果某个需求在视觉上是中心的但是主观的，请将其转换为可观察的 QA 检查，而不是将其隐式保留。
   - 至少添加 2 个可能暴露脆弱行为的探索性或不愉快的场景。
2. 运行引导单元一次。
3. 在持久 TTY 会话中启动或确认任何所需的开发服务器。
4. 启动正确的运行时并继续重用相同的 Playwright 句柄。
5. 每次代码更改后，重新加载仅渲染器更改或重新启动 main-process/startup 更改。
6. 使用正常用户输入运行功能 QA。
7. 运行单独的视觉 QA 通道。
8. 验证视口是否合适并捕获支持您的主张所需的屏幕截图。
9. 仅当任务实际完成时才清理 Playwright 会话。

## 引导程序（运行一次）

```javascript
var chromium;
var electronLauncher;
var browser;
var context;
var page;
var mobileContext;
var mobilePage;
var electronApp;
var appWindow;

try {
  ({ chromium, _electron: electronLauncher } = await import("playwright"));
  console.log("Playwright loaded");
} catch (error) {
  throw new Error(
    `Could not load playwright from the current js_repl cwd. Run the setup commands from this workspace first. Original error: ${error}`
  );
}
```

绑定规则：

- 使用 `var` 作为共享的顶级剧作家句柄，因为稍后的 `js_repl` 单元会重用它们。
- 下面的设置单元是故意设计的简短的快乐路径。如果句柄看起来过时，请将该绑定设置为 `undefined` 并重新运行单元，而不是在各处添加恢复逻辑。
- 更喜欢为您关心的每个表面分配一个命名句柄（`page`、`mobilePage`、`appWindow`），而不是从上下文中反复重新发现页面。

共享网络助手：

```javascript
var resetWebHandles = function () {
  context = undefined;
  page = undefined;
  mobileContext = undefined;
  mobilePage = undefined;
};

var ensureWebBrowser = async function () {
  if (browser && !browser.isConnected()) {
    browser = undefined;
    resetWebHandles();
  }

  browser ??= await chromium.launch({ headless: false });
  return browser;
};

var reloadWebContexts = async function () {
  for (const currentContext of [context, mobileContext]) {
    if (!currentContext) continue;
    for (const p of currentContext.pages()) {
      await p.reload({ waitUntil: "domcontentloaded" });
    }
  }
  console.log("Reloaded existing web tabs");
};
```

## 选择会话模式

对于 Web 应用程序，默认情况下使用显式视口，并将本机窗口模式视为单独的验证过程。

- 使用显式视口进行例行迭代、断点检查、可重现的屏幕截图、快照差异和模型辅助定位。这是默认设置，因为它在机器之间保持稳定，并且避免了主机窗口管理器的可变性。
- 当您需要确定性高 DPI 行为时，请保留显式视口并添加 `deviceScaleFactor`，而不是直接切换到本机窗口模式。
- 当您需要验证启动的窗口大小、OS-级别DPI行为、浏览器chrome交互或可能取决于主机显示配置的错误时，请使用本机窗口模式（`viewport: null`）进行单独的引导传递。
- 对于 Electron，始终假设本机窗口行为。 Electron 通过 Playwright 与 `noDefaultViewport` 启动，因此请将其视为真正的桌面窗口，并在调整任何大小之前检查启动时的大小和布局。
- 当签核同时取决于布局断点和实际桌面行为时，请执行两遍：首先进行显式视口以确定性 QA，然后进行本机窗口验证以进行最终的特定于环境的检查。
- 将切换模式视为上下文重置。不要将视口模拟的 `context` 重复用于本机窗口通道，反之亦然；关闭旧的`page`和`context`，然后为新模式创建一个新的。

## 启动或重用 Web 会话

桌面和移动 Web 会话共享相同的 `browser`、帮助程序和 QA 流程。主要区别在于您创建的上下文和页面对。

### 桌面网络上下文

将 `TARGET_URL` 设置为您正在调试的应用程序。对于本地服务器，优先选择 `127.0.0.1` 而不是 `localhost`。

```javascript
var TARGET_URL = "http://127.0.0.1:3000";

if (page?.isClosed()) page = undefined;

await ensureWebBrowser();
context ??= await browser.newContext({
  viewport: { width: 1600, height: 900 },
});
page ??= await context.newPage();

await page.goto(TARGET_URL, { waitUntil: "domcontentloaded" });
console.log("Loaded:", await page.title());
```

如果 `context` 或 `page` 已过时，请设置 `context = page = undefined` 并重新运行单元格。

### 移动网络环境

当`TARGET_URL`已经存在时，重用它；否则直接设置移动目标。

```javascript
var MOBILE_TARGET_URL = typeof TARGET_URL === "string"
  ? TARGET_URL
  : "http://127.0.0.1:3000";

if (mobilePage?.isClosed()) mobilePage = undefined;

await ensureWebBrowser();
mobileContext ??= await browser.newContext({
  viewport: { width: 390, height: 844 },
  isMobile: true,
  hasTouch: true,
});
mobilePage ??= await mobileContext.newPage();

await mobilePage.goto(MOBILE_TARGET_URL, { waitUntil: "domcontentloaded" });
console.log("Loaded mobile:", await mobilePage.title());
```

如果 `mobileContext` 或 `mobilePage` 已过时，请设置 `mobileContext = mobilePage = undefined` 并重新运行单元格。

### 本机窗口 Web Pass

```javascript
var TARGET_URL = "http://127.0.0.1:3000";

await ensureWebBrowser();

await page?.close().catch(() => {});
await context?.close().catch(() => {});
page = undefined;
context = undefined;

browser ??= await chromium.launch({ headless: false });
context = await browser.newContext({ viewport: null });
page = await context.newPage();

await page.goto(TARGET_URL, { waitUntil: "domcontentloaded" });
console.log("Loaded native window:", await page.title());
```

## 启动或重用 Electron 会话

当当前工作区是 Electron 应用程序且 `package.json` 将 `main` 指向右侧入口文件时，将 `ELECTRON_ENTRY` 设置为 `.`。如果您需要直接定位特定的主进程文件，请改用 `./main.js` 等路径。

```javascript
var ELECTRON_ENTRY = ".";

if (appWindow?.isClosed()) appWindow = undefined;

if (!appWindow && electronApp) {
  await electronApp.close().catch(() => {});
  electronApp = undefined;
}

electronApp ??= await electronLauncher.launch({
  args: [ELECTRON_ENTRY],
});

appWindow ??= await electronApp.firstWindow();

console.log("Loaded Electron window:", await appWindow.title());
```

如果 `js_repl` 尚未从 Electron 应用程序工作区运行，请在启动时显式传递 `cwd`。

如果应用程序进程看起来过时，请设置 `electronApp = appWindow = undefined` 并重新运行单元。

如果您已经有一个 Electron 会话，但在主进程、预加载或启动更改后需要一个新进程，请使用下一节中的重新启动单元，而不是重新运行此单元。

## 在迭代期间重用会话

尽可能保持同一个会话处于活动状态。

网页渲染器重新加载：

```javascript
await reloadWebContexts();
```

仅电子渲染器重新加载：

```javascript
await appWindow.reload({ waitUntil: "domcontentloaded" });
console.log("Reloaded Electron window");
```

主进程、预加载或启动更改后电子重新启动：

```javascript
await electronApp.close().catch(() => {});
electronApp = undefined;
appWindow = undefined;

electronApp = await electronLauncher.launch({
  args: [ELECTRON_ENTRY],
});

appWindow = await electronApp.firstWindow();
console.log("Relaunched Electron window:", await appWindow.title());
```

如果您的启动需要显式 `cwd`，请在此处包含相同的 `cwd`。

默认姿势：

- 保持每个 `js_repl` 单元格简短并专注于一次交互爆发。
- 重用现有的顶级绑定（`browser`、`context`、`page`、`electronApp`、`appWindow`）而不是重新声明它们。
- 如果需要隔离，请在同一浏览器中创建新页面或新上下文。
- 对于 Electron，仅将 `electronApp.evaluate(...)` 用于主流程检查或专用诊断。
- 就地修复助手错误；不要重置 REPL，除非内核确实损坏。

## 清单

### 会话循环

- Bootstrap `js_repl` 一次，然后在迭代中保持相同的 Playwright 句柄处于活动状态。
- 从当前工作区启动目标运行时。
- 更改代码。
- 使用该更改的正确路径重新加载或重新启动。
- 如果探索发现额外的控制、状态或可见声明，请更新共享的 QA 库存。
- 重新运行功能 QA。
- 重新运行视觉效果 QA。
- 仅在当前状态是您正在评估的状态之后才捕获最终工件。

### 重新加载决定

- 仅渲染器更改：重新加载现有页面或 Electron 窗口。
- 主进程、预加载或启动更改：重新启动 Electron。
- 关于进程所有权或启动代码的新不确定性：重新启动而不是猜测。

### 功能性QA

- 使用真实的用户控件进行签核：键盘、鼠标、单击、触摸或等效的 Playwright 输入 API。
- 验证至少一个端到端关键流程。
- 确认该流程的可见结果，而不仅仅是内部状态。
- 对于实时或动画较多的应用程序，请验证实际交互计时下的行为。
- 完成共享的 QA 库存，而不是临时抽查。
- 在签收之前至少覆盖一次每个明显的可见控制，而不仅仅是主要的快乐路径。
- 对于清单中的可逆控制或有状态切换，测试整个周期：初始状态、更改状态和返回到初始状态。
- 脚本化检查通过后，使用正常输入进行 30-90 秒的简短探索，而不是仅遵循预期路径。
- 如果探索阶段揭示了新的状态、控制或声明，请将其添加到共享 QA 库存中，并在签核之前覆盖它。
- `page.evaluate(...)` 和 `electronApp.evaluate(...)` 可以检查或暂存状态，但它们不计为签核输入。

### 视觉QA

- 将视觉 QA 与功能 QA 分开。
- 使用测试前定义并在 QA 期间更新的相同共享 QA 库存；不要从不同的隐式列表开始视觉覆盖。
- 重申用户可见的声明并明确验证每一项；不要假设功能通行证证明了视觉主张。
- 用户可见的声明只有在其应被感知的特定状态下经过检查后才会被签署。
- 滚动之前检查初始视口。
- 确认初始视图明显支持界面的主要主张；如果核心承诺元素在那里无法清晰感知，请将其视为错误。
- 检查所有必需的可见区域，而不仅仅是主要交互表面。
- 检查共享 QA 清单中已枚举的状态和模式，包括任务交互时至少一种有意义的交互后状态。
- 如果运动或转换是体验的一部分，除了已确定的端点之外，还至少检查一种转换中状态。
- 如果标签、覆盖、注释、指南或突出显示旨在跟踪更改的内容，请在相关状态更改后验证该关系。
- 对于动态或交互相关的视觉效果，检查足够长的时间来判断稳定性、层次感和可读性；不要依赖单个屏幕截图进行签核。
- 对于加载或交互后可能变得更密集的界面，请检查在 QA 期间可以达到的最密集的现实状态，而不仅仅是空、加载或折叠状态。
- 如果产品定义了支持的最小视口或窗口大小，请在此处运行单独的视觉 QA 传递；否则，选择一个较小但仍然现实的尺寸并明确检查它。
- 区分存在与实现：如果技术上存在预期的可供性，但由于对比度弱、遮挡、剪裁或不稳定而无法清晰感知，则将其视为视觉失败。
- 如果在您正在评估的状态下任何所需的可见区域被剪裁、切断、遮挡或推到视口之外，即使页面级滚动指标看起来可以接受，也请将其视为错误。
- 查找剪裁、溢出、扭曲、布局不平衡、间距不一致、对齐问题、文本难以辨认、对比度弱、分层损坏和尴尬的运动状态。
- 判断审美质量以及正确性。 UI 应该感觉任务是有意的、连贯的、视觉上令人愉悦的。
- 更喜欢使用视口屏幕截图进行签核。仅使用全页捕获作为辅助调试工件，并在某个区域需要仔细检查时捕获重点屏幕截图。
- 如果运动导致屏幕截图不明确，请短暂等待 UI 稳定下来，然后捕获您实际评估的图像。
- 在签核之前，明确询问：该界面的哪些可见部分我还没有仔细检查？
- 在签收之前，明确询问：如果用户仔细观察，什么明显的缺陷最有可能使这个结果变得尴尬？

### 签核

- 功能路径通过正常用户输入传递。
- 覆盖范围是针对共享 QA 清单的明确内容：记下执行了哪些要求、已实现的功能、控制、状态和声明，并指出任何有意排除的情况。
- 视觉QA通行证覆盖了整个相关界面。
- 每个用户可见的声明都有匹配的视觉检查和审查的屏幕截图工件，来自该声明重要的状态和视口或窗口大小。
- 针对预期初始视图和任何所需的最小支持视口或窗口大小通过了视口适合检查。
- 如果产品在窗口中启动，则在手动调整大小或重新定位之前会检查启动时的尺寸、位置和初始布局。
- UI 不仅具有功能性，而且还具有实用性。它在视觉上是连贯的，并且在美学上不弱于任务。
- 功能正确性、视口适配性和视觉质量必须各自通过；其中之一并不意味着其他。
- 交互式产品的简短探索通行证已完成，响应提到了该通行证涵盖的内容。
- 如果屏幕截图审查和数字检查在任何时候不一致，则在签收之前对差异进行调查；屏幕截图中可见的剪辑是无法解决的问题，而不是指标可以推翻的问题。
- 包括对您检查但未发现的主要缺陷类别的简短否定确认。
- 已执行清理，或者您故意使会话保持活动状态以进行进一步的工作。

## 截图示例

如果您打算通过 `codex.emitImage(...)` 发出屏幕截图，请默认使用下一节中的 CSS- 规范化路径。这些是将由模型解释或用于基于坐标的后续操作的典型屏幕截图示例。仅将原始捕获保留为保真度敏感调试的例外；原始异常示例出现在规范化指导之后。

### 模型绑定屏幕截图（默认）

如果您要发出带有 `codex.emitImage(...)` 的屏幕截图以进行模型解释，请将其标准化为 CSS 像素，以获取在发出之前捕获的确切区域。如果回复稍后用于点击，这将使返回的坐标与 Playwright CSS 像素对齐，并且还可以减少图像有效负载大小和模型令牌成本。

默认情况下不发出原始本机窗口屏幕截图。仅当您明确需要设备像素保真度时才跳过标准化，例如 Retina 或 DPI 工件调试、像素精确渲染检查或原始像素比有效负载大小更重要的其他保真度敏感情况。对于不会发送到模型的仅本地检查，原始捕获就可以了。

不要假设 `page.screenshot({ scale: "css" })` 在本机窗口模式 (`viewport: null`) 中就足够了。在 macOS Retina 显示屏上的 Chromium 中，即使请求 `scale: "css"`，头显本机窗口屏幕截图仍然可以以设备像素大小返回。同样的警告也适用于通过 Playwright 启动的 Electron 窗口，因为 Electron 与 `noDefaultViewport` 一起运行，并且 `appWindow.screenshot({ scale: "css" })` 可能仍然返回设备像素输出。

对网页和 Electron 窗口使用单独的标准化路径：

- Web：直接选择`page.screenshot({ scale: "css" })`。如果本机窗口 Chromium 仍然返回设备像素输出，请使用画布在当前页面内调整大小；不需要草稿页。
- Electron：不要使用`appWindow.context().newPage()`或`electronApp.context().newPage()`作为草稿页。 Electron 上下文不可靠地支持该路径。使用 `BrowserWindow.capturePage(...)` 在主进程中捕获，使用 `nativeImage.resize(...)` 调整大小，然后直接发出这些字节。

共享助手和约定：

```javascript
var emitJpeg = async function (bytes) {
  await codex.emitImage({
    bytes,
    mimeType: "image/jpeg",
    detail: "original",
  });
};

var emitWebJpeg = async function (surface, options = {}) {
  await emitJpeg(await surface.screenshot({
    type: "jpeg",
    quality: 85,
    scale: "css",
    ...options,
  }));
};

var clickCssPoint = async function ({ surface, x, y, clip }) {
  await surface.mouse.click(
    clip ? clip.x + x : x,
    clip ? clip.y + y : y
  );
};

var tapCssPoint = async function ({ page, x, y, clip }) {
  await page.touchscreen.tap(
    clip ? clip.x + x : x,
    clip ? clip.y + y : y
  );
};
```

- 对于 Web 使用 `page` 或 `mobilePage`，对于 Electron 使用 `appWindow` 作为 `surface`。
- 在渲染器中将 `clip` 视为来自 `getBoundingClientRect()` 的 CSS 像素。
- 除非特别要求无损保真度，否则优先选择 JPEG 和 `quality: 85`。
- 对于全图像捕获，直接使用返回的`{ x, y }`。
- 对于剪辑捕获，请在单击时重新添加剪辑原点。

### 网页 CSS 标准化

显式视口上下文的首选 Web 路径，通常用于一般 Web：

```javascript
await emitWebJpeg(page);
```

移动网络使用相同的路径；将 `mobilePage` 替换为 `page`：

```javascript
await emitWebJpeg(mobilePage);
```

如果模型返回`{ x, y }`，直接点击：

```javascript
await clickCssPoint({ surface: page, x, y });
```

移动网页点击路径：

```javascript
await tapCssPoint({ page: mobilePage, x, y });
```

对于此正常路径中的网页`clip`屏幕截图或元素屏幕截图，`scale: "css"`通常直接工作。单击时重新添加区域原点。

- `await emitWebJpeg(page, { clip })`
- `await emitWebJpeg(mobilePage, { clip })`
- `await clickCssPoint({ surface: page, clip, x, y })`
- `await tapCssPoint({ page: mobilePage, clip, x, y })`
- `await clickCssPoint({ surface: page, clip: box, x, y })` 之后 `const box = await locator.boundingBox()`

当 `scale: "css"` 仍然以设备像素大小返回时，Web 本机窗口回退：

```javascript
var emitWebScreenshotCssScaled = async function ({ page, clip, quality = 0.85 } = {}) {
  var NodeBuffer = (await import("node:buffer")).Buffer;
  const target = clip
    ? { width: clip.width, height: clip.height }
    : await page.evaluate(() => ({
        width: window.innerWidth,
        height: window.innerHeight,
      }));

  const screenshotBuffer = await page.screenshot({
    type: "png",
    ...(clip ? { clip } : {}),
  });

  const bytes = await page.evaluate(
    async ({ imageBase64, targetWidth, targetHeight, quality }) => {
      const image = new Image();
      image.src = `data:image/png;base64,${imageBase64}`;
      await image.decode();

      const canvas = document.createElement("canvas");
      canvas.width = targetWidth;
      canvas.height = targetHeight;

      const ctx = canvas.getContext("2d");
      ctx.imageSmoothingEnabled = true;
      ctx.drawImage(image, 0, 0, targetWidth, targetHeight);

      const blob = await new Promise((resolve) =>
        canvas.toBlob(resolve, "image/jpeg", quality)
      );

      return new Uint8Array(await blob.arrayBuffer());
    },
    {
      imageBase64: NodeBuffer.from(screenshotBuffer).toString("base64"),
      targetWidth: target.width,
      targetHeight: target.height,
      quality,
    }
  );

  await emitJpeg(bytes);
};
```

对于完整视口回退捕获，请将返回的 `{ x, y }` 视为直接 CSS 坐标：

```javascript
await emitWebScreenshotCssScaled({ page });
await clickCssPoint({ surface: page, x, y });
```

对于剪辑后备捕获，请添加剪辑原点：

```javascript
await emitWebScreenshotCssScaled({ page, clip });
await clickCssPoint({ surface: page, clip, x, y });
```

### 电子 CSS 归一化

对于 Electron，在主进程中进行标准化，而不是打开临时的 Playwright 页面。下面的帮助程序返回完整内容区域或剪辑的 CSS- 像素区域的 CSS- 缩放字节。将 `clip` 视为内容区域 CSS 像素，例如从渲染器中的 `getBoundingClientRect()` 获取的值。

```javascript
var emitElectronScreenshotCssScaled = async function ({ electronApp, clip, quality = 85 } = {}) {
  const bytes = await electronApp.evaluate(async ({ BrowserWindow }, { clip, quality }) => {
    const win = BrowserWindow.getAllWindows()[0];
    const image = clip ? await win.capturePage(clip) : await win.capturePage();

    const target = clip
      ? { width: clip.width, height: clip.height }
      : (() => {
          const [width, height] = win.getContentSize();
          return { width, height };
        })();

    const resized = image.resize({
      width: target.width,
      height: target.height,
      quality: "best",
    });

    return resized.toJPEG(quality);
  }, { clip, quality });

  await emitJpeg(bytes);
};
```

全电子窗口：

```javascript
await emitElectronScreenshotCssScaled({ electronApp });
await clickCssPoint({ surface: appWindow, x, y });
```

使用渲染器中的 CSS 像素剪切电子区域：

```javascript
var clip = await appWindow.evaluate(() => {
  const rect = document.getElementById("board").getBoundingClientRect();
  return {
    x: Math.round(rect.x),
    y: Math.round(rect.y),
    width: Math.round(rect.width),
    height: Math.round(rect.height),
  };
});

await emitElectronScreenshotCssScaled({ electronApp, clip });
await clickCssPoint({ surface: appWindow, clip, x, y });
```

### 原始屏幕截图异常示例

仅当原始像素比 CSS- 坐标对齐更重要时才使用这些，例如 Retina 或 DPI 工件调试、像素精确的渲染检查或其他保真度敏感的审查。

Web 桌面原始发射：

```javascript
await codex.emitImage({
  bytes: await page.screenshot({ type: "jpeg", quality: 85 }),
  mimeType: "image/jpeg",
  detail: "original",
});
```

原始电子发射：

```javascript
await codex.emitImage({
  bytes: await appWindow.screenshot({ type: "jpeg", quality: 85 }),
  mimeType: "image/jpeg",
  detail: "original",
});
```

移动 Web 上下文已经运行后，移动原始数据会发出：

```javascript
await codex.emitImage({
  bytes: await mobilePage.screenshot({ type: "jpeg", quality: 85 }),
  mimeType: "image/jpeg",
  detail: "original",
});
```

## 视口适合检查（必需）

不要仅仅因为主窗口部件可见就认为屏幕截图是可以接受的。在签核之前，使用屏幕截图审查和数字检查来明确验证预期的初始视图是否符合产品要求。

- 在签收之前定义预期的初始视图。对于可滚动页面，这是首屏体验。对于类似应用程序的 shell、游戏、编辑器、仪表板或工具，这是完整的交互式界面以及使用它所需的控件和状态。
- 使用屏幕截图作为适合的主要证据。数值检查支持截图；它们不会否定可见的剪裁。
- 如果任何所需的可见区域被剪裁、切断、遮挡或推到预期初始视图中的视口之外，即使页面级滚动指标看起来可以接受，签核也会失败。
- 当产品被设计为滚动并且初始视图仍然传达核心体验并公开主要的号召性用语或所需的启动上下文时，滚动是可以接受的。
- 对于固定外壳界面，如果需要到达主要交互表面或基本控件的一部分，则滚动不是可接受的解决方法。
- 不要单独依赖文档滚动指标。固定高度外壳、内部窗格和隐藏溢出容器可以剪辑所需的 UI，同时页面级滚动检查仍然看起来干净。
- 检查区域边界，而不仅仅是文档边界。验证每个所需的可见区域是否适合启动状态下的视口。
- 对于 Electron 或桌面应用程序，在进行任何手动调整大小或重新定位之前，请验证启动的窗口大小和位置以及渲染器的初始可见布局。
- 通过视口适合检查仅证明预期的初始视图是可见的，而不会出现意外的剪切或滚动。它并不能证明 UI 在视觉上是正确的或在美学上是成功的。

Web 或渲染器检查：

```javascript
console.log(await page.evaluate(() => ({
  innerWidth: window.innerWidth,
  innerHeight: window.innerHeight,
  clientWidth: document.documentElement.clientWidth,
  clientHeight: document.documentElement.clientHeight,
  scrollWidth: document.documentElement.scrollWidth,
  scrollHeight: document.documentElement.scrollHeight,
  canScrollX: document.documentElement.scrollWidth > document.documentElement.clientWidth,
  canScrollY: document.documentElement.scrollHeight > document.documentElement.clientHeight,
})));
```

电子检查：

```javascript
console.log(await appWindow.evaluate(() => ({
  innerWidth: window.innerWidth,
  innerHeight: window.innerHeight,
  clientWidth: document.documentElement.clientWidth,
  clientHeight: document.documentElement.clientHeight,
  scrollWidth: document.documentElement.scrollWidth,
  scrollHeight: document.documentElement.scrollHeight,
  canScrollX: document.documentElement.scrollWidth > document.documentElement.clientWidth,
  canScrollY: document.documentElement.scrollHeight > document.documentElement.clientHeight,
})));
```

当剪切是一种实际的故障模式时，使用 `getBoundingClientRect()` 检查特定 UI 中所需的可见区域来增强数字检查；对于固定 shell 来说，仅文档级指标是不够的。

## 开发服务器

对于本地 Web 调试，请保持应用程序在持久 TTY 会话中运行。不要依赖来自短期 shell 的一次性后台命令。

使用项目的正常启动命令，例如：

```bash
npm start
```

在 `page.goto(...)` 之前，验证所选端口正在侦听并且应用程序响应。

对于 Electron 调试，从 `js_repl` 到 `_electron.launch(...)` 启动应用程序，以便同一个会话拥有该进程。如果 Electron 渲染器依赖于单独的开发服务器（例如 Vite 或 Next），请保持该服务器在持久的 TTY 会话中运行，然后从 `js_repl` 重新启动或重新加载 Electron 应用程序。

## 清理

仅在任务实际完成时运行清理：

- 此清理是手动的。退出 Codex、关闭终端或丢失 `js_repl` 会话不会隐式运行 `electronApp.close()`、`context.close()` 或 `browser.close()`。
- 特别是对于 Electron，假设如果您在没有先执行清理单元的情况下离开会话，应用程序可能会继续运行。

```javascript
if (electronApp) {
  await electronApp.close().catch(() => {});
}

if (mobileContext) {
  await mobileContext.close().catch(() => {});
}

if (context) {
  await context.close().catch(() => {});
}

if (browser) {
  await browser.close().catch(() => {});
}

browser = undefined;
context = undefined;
page = undefined;
mobileContext = undefined;
mobilePage = undefined;
electronApp = undefined;
appWindow = undefined;

console.log("Playwright session closed");
```

如果您计划在调试后立即退出 Codex，请先运行清理单元并等待 `"Playwright session closed"` 日志后再退出。

## 常见故障模式

- `Cannot find module 'playwright'`：在当前工作区中运行一次性安装程序并在使用`js_repl`之前验证导入。
- Playwright 软件包已安装，但缺少浏览器可执行文件：运行 `npx playwright install chromium`。
- `page.goto: net::ERR_CONNECTION_REFUSED`：确保开发服务器仍在持久的 TTY 会话中运行，重新检查端口，并首选 `http://127.0.0.1:<port>`。
- `electron.launch` 挂起、超时或立即退出：验证本地 `electron` 依赖项，确认 `args` 目标，并确保任何渲染器开发服务器在启动前已在运行。
- `Identifier has already been declared`：重用现有的顶级绑定，选择新名称，或将代码包装在`{ ... }`中。仅当内核确实卡住时才使用`js_repl_reset`。
- `browserContext.newPage: Protocol error (Target.createTarget): Not supported` 使用 Electron 时：不要使用 `appWindow.context().newPage()` 或 `electronApp.context().newPage()` 作为草稿页；在模型绑定屏幕截图部分中使用特定于 Electron 的屏幕截图标准化流程。
- `js_repl` 超时或重置：重新运行引导单元并使用更短、更集中的单元重新创建会话。
- 浏览器启动或网络操作立即失败：确认会话是使用 `--sandbox danger-full-access` 启动的，并在需要时以这种方式重新启动。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Playwright Interactive Skill

Use a persistent `js_repl` Playwright session to debug local web or Electron apps, keep the same handles alive across iterations, and run functional plus visual QA without restarting the whole toolchain unless the process ownership changed.

## Preconditions

- `js_repl` must be enabled for this skill.
- If `js_repl` is missing, enable it in `~/.codex/config.toml`:

```toml
[features]
js_repl = true
```

- You can also start a new session with `--enable js_repl` (equivalent to `-c features.js_repl=true`).
- After enabling `js_repl`, start a new Codex session so the tool list refreshes.
- For now, run this workflow with sandboxing disabled: start Codex with `--sandbox danger-full-access` (or the equivalent config for `sandbox_mode=danger-full-access`). This is a temporary requirement while `js_repl` + Playwright support inside the sandbox is still being completed.
- Run setup from the same project directory you need to debug.
- Treat `js_repl_reset` as a recovery tool, not routine cleanup. Resetting the kernel destroys your Playwright handles.

## One-time setup

```bash
test -f package.json || npm init -y
npm install playwright
# Web-only, for headed Chromium or mobile emulation:
# npx playwright install chromium
# Electron-only, and only if the target workspace is the app itself:
# npm install --save-dev electron
node -e "import('playwright').then(() => console.log('playwright import ok')).catch((error) => { console.error(error); process.exit(1); })"
```

If you switch to a different workspace later, repeat setup there.

## Core Workflow

1. Write a brief QA inventory before testing:
   - Build the inventory from three sources: the user's requested requirements, the user-visible features or behaviors you actually implemented, and the claims you expect to make in the final response.
   - Anything that appears in any of those three sources must map to at least one QA check before signoff.
   - List the user-visible claims you intend to sign off on.
   - List every meaningful user-facing control, mode switch, or implemented interactive behavior.
   - List the state changes or view changes each control or implemented behavior can cause.
   - Use this as the shared coverage list for both functional QA and visual QA.
   - For each claim or control-state pair, note the intended functional check, the specific state where the visual check must happen, and the evidence you expect to capture.
   - If a requirement is visually central but subjective, convert it into an observable QA check instead of leaving it implicit.
   - Add at least 2 exploratory or off-happy-path scenarios that could expose fragile behavior.
2. Run the bootstrap cell once.
3. Start or confirm any required dev server in a persistent TTY session.
4. Launch the correct runtime and keep reusing the same Playwright handles.
5. After each code change, reload for renderer-only changes or relaunch for main-process/startup changes.
6. Run functional QA with normal user input.
7. Run a separate visual QA pass.
8. Verify viewport fit and capture the screenshots needed to support your claims.
9. Clean up the Playwright session only when the task is actually finished.

## Bootstrap (Run Once)

```javascript
var chromium;
var electronLauncher;
var browser;
var context;
var page;
var mobileContext;
var mobilePage;
var electronApp;
var appWindow;

try {
  ({ chromium, _electron: electronLauncher } = await import("playwright"));
  console.log("Playwright loaded");
} catch (error) {
  throw new Error(
    `Could not load playwright from the current js_repl cwd. Run the setup commands from this workspace first. Original error: ${error}`
  );
}
```

Binding rules:

- Use `var` for the shared top-level Playwright handles because later `js_repl` cells reuse them.
- The setup cells below are intentionally short happy paths. If a handle looks stale, set that binding to `undefined` and rerun the cell instead of adding recovery logic everywhere.
- Prefer one named handle per surface you care about (`page`, `mobilePage`, `appWindow`) over repeatedly rediscovering pages from the context.

Shared web helpers:

```javascript
var resetWebHandles = function () {
  context = undefined;
  page = undefined;
  mobileContext = undefined;
  mobilePage = undefined;
};

var ensureWebBrowser = async function () {
  if (browser && !browser.isConnected()) {
    browser = undefined;
    resetWebHandles();
  }

  browser ??= await chromium.launch({ headless: false });
  return browser;
};

var reloadWebContexts = async function () {
  for (const currentContext of [context, mobileContext]) {
    if (!currentContext) continue;
    for (const p of currentContext.pages()) {
      await p.reload({ waitUntil: "domcontentloaded" });
    }
  }
  console.log("Reloaded existing web tabs");
};
```

## Choose Session Mode

For web apps, use an explicit viewport by default and treat native-window mode as a separate validation pass.

- Use an explicit viewport for routine iteration, breakpoint checks, reproducible screenshots, snapshot diffs, and model-assisted localization. This is the default because it is stable across machines and avoids host window-manager variability.
- When you need deterministic high-DPI behavior, keep the explicit viewport and add `deviceScaleFactor` rather than switching straight to native-window mode.
- Use native-window mode (`viewport: null`) for a separate headed pass when you need to validate launched window size, OS-level DPI behavior, browser chrome interactions, or bugs that may depend on the host display configuration.
- For Electron, assume native-window behavior all the time. Electron launches through Playwright with `noDefaultViewport`, so treat it like a real desktop window and check the as-launched size and layout before resizing anything.
- When signoff depends on both layout breakpoints and real desktop behavior, do both passes: explicit viewport first for deterministic QA, then native-window validation for final environment-specific checks.
- Treat switching modes as a context reset. Do not reuse a viewport-emulated `context` for a native-window pass or vice versa; close the old `page` and `context`, then create a new one for the new mode.

## Start or Reuse Web Session

Desktop and mobile web sessions share the same `browser`, helpers, and QA flow. The main difference is which context and page pair you create.

### Desktop Web Context

Set `TARGET_URL` to the app you are debugging. For local servers, prefer `127.0.0.1` over `localhost`.

```javascript
var TARGET_URL = "http://127.0.0.1:3000";

if (page?.isClosed()) page = undefined;

await ensureWebBrowser();
context ??= await browser.newContext({
  viewport: { width: 1600, height: 900 },
});
page ??= await context.newPage();

await page.goto(TARGET_URL, { waitUntil: "domcontentloaded" });
console.log("Loaded:", await page.title());
```

If `context` or `page` is stale, set `context = page = undefined` and rerun the cell.

### Mobile Web Context

Reuse `TARGET_URL` when it already exists; otherwise set a mobile target directly.

```javascript
var MOBILE_TARGET_URL = typeof TARGET_URL === "string"
  ? TARGET_URL
  : "http://127.0.0.1:3000";

if (mobilePage?.isClosed()) mobilePage = undefined;

await ensureWebBrowser();
mobileContext ??= await browser.newContext({
  viewport: { width: 390, height: 844 },
  isMobile: true,
  hasTouch: true,
});
mobilePage ??= await mobileContext.newPage();

await mobilePage.goto(MOBILE_TARGET_URL, { waitUntil: "domcontentloaded" });
console.log("Loaded mobile:", await mobilePage.title());
```

If `mobileContext` or `mobilePage` is stale, set `mobileContext = mobilePage = undefined` and rerun the cell.

### Native-Window Web Pass

```javascript
var TARGET_URL = "http://127.0.0.1:3000";

await ensureWebBrowser();

await page?.close().catch(() => {});
await context?.close().catch(() => {});
page = undefined;
context = undefined;

browser ??= await chromium.launch({ headless: false });
context = await browser.newContext({ viewport: null });
page = await context.newPage();

await page.goto(TARGET_URL, { waitUntil: "domcontentloaded" });
console.log("Loaded native window:", await page.title());
```

## Start or Reuse Electron Session

Set `ELECTRON_ENTRY` to `.` when the current workspace is the Electron app and `package.json` points `main` to the right entry file. If you need to target a specific main-process file directly, use a path such as `./main.js` instead.

```javascript
var ELECTRON_ENTRY = ".";

if (appWindow?.isClosed()) appWindow = undefined;

if (!appWindow && electronApp) {
  await electronApp.close().catch(() => {});
  electronApp = undefined;
}

electronApp ??= await electronLauncher.launch({
  args: [ELECTRON_ENTRY],
});

appWindow ??= await electronApp.firstWindow();

console.log("Loaded Electron window:", await appWindow.title());
```

If `js_repl` is not already running from the Electron app workspace, pass `cwd` explicitly when launching.

If the app process looks stale, set `electronApp = appWindow = undefined` and rerun the cell.

If you already have an Electron session but need a fresh process after a main-process, preload, or startup change, use the restart cell in the next section instead of rerunning this one.

## Reuse Sessions During Iteration

Keep the same session alive whenever you can.

Web renderer reload:

```javascript
await reloadWebContexts();
```

Electron renderer-only reload:

```javascript
await appWindow.reload({ waitUntil: "domcontentloaded" });
console.log("Reloaded Electron window");
```

Electron restart after main-process, preload, or startup changes:

```javascript
await electronApp.close().catch(() => {});
electronApp = undefined;
appWindow = undefined;

electronApp = await electronLauncher.launch({
  args: [ELECTRON_ENTRY],
});

appWindow = await electronApp.firstWindow();
console.log("Relaunched Electron window:", await appWindow.title());
```

If your launch requires an explicit `cwd`, include the same `cwd` here.

Default posture:

- Keep each `js_repl` cell short and focused on one interaction burst.
- Reuse the existing top-level bindings (`browser`, `context`, `page`, `electronApp`, `appWindow`) instead of redeclaring them.
- If you need isolation, create a new page or a new context inside the same browser.
- For Electron, use `electronApp.evaluate(...)` only for main-process inspection or purpose-built diagnostics.
- Fix helper mistakes in place; do not reset the REPL unless the kernel is actually broken.

## Checklists

### Session Loop

- Bootstrap `js_repl` once, then keep the same Playwright handles alive across iterations.
- Launch the target runtime from the current workspace.
- Make the code change.
- Reload or relaunch using the correct path for that change.
- Update the shared QA inventory if exploration reveals an additional control, state, or visible claim.
- Re-run functional QA.
- Re-run visual QA.
- Capture final artifacts only after the current state is the one you are evaluating.

### Reload Decision

- Renderer-only change: reload the existing page or Electron window.
- Main-process, preload, or startup change: relaunch Electron.
- New uncertainty about process ownership or startup code: relaunch instead of guessing.

### Functional QA

- Use real user controls for signoff: keyboard, mouse, click, touch, or equivalent Playwright input APIs.
- Verify at least one end-to-end critical flow.
- Confirm the visible result of that flow, not just internal state.
- For realtime or animation-heavy apps, verify behavior under actual interaction timing.
- Work through the shared QA inventory rather than ad hoc spot checks.
- Cover every obvious visible control at least once before signoff, not only the main happy path.
- For reversible controls or stateful toggles in the inventory, test the full cycle: initial state, changed state, and return to the initial state.
- After the scripted checks pass, do a short exploratory pass using normal input for 30-90 seconds instead of following only the intended path.
- If the exploratory pass reveals a new state, control, or claim, add it to the shared QA inventory and cover it before signoff.
- `page.evaluate(...)` and `electronApp.evaluate(...)` may inspect or stage state, but they do not count as signoff input.

### Visual QA

- Treat visual QA as separate from functional QA.
- Use the same shared QA inventory defined before testing and updated during QA; do not start visual coverage from a different implicit list.
- Restate the user-visible claims and verify each one explicitly; do not assume a functional pass proves a visual claim.
- A user-visible claim is not signed off until it has been inspected in the specific state where it is meant to be perceived.
- Inspect the initial viewport before scrolling.
- Confirm that the initial view visibly supports the interface's primary claims; if a core promised element is not clearly perceptible there, treat that as a bug.
- Inspect all required visible regions, not just the main interaction surface.
- Inspect the states and modes already enumerated in the shared QA inventory, including at least one meaningful post-interaction state when the task is interactive.
- If motion or transitions are part of the experience, inspect at least one in-transition state in addition to the settled endpoints.
- If labels, overlays, annotations, guides, or highlights are meant to track changing content, verify that relationship after the relevant state change.
- For dynamic or interaction-dependent visuals, inspect long enough to judge stability, layering, and readability; do not rely on a single screenshot for signoff.
- For interfaces that can become denser after loading or interaction, inspect the densest realistic state you can reach during QA, not only the empty, loading, or collapsed state.
- If the product has a defined minimum supported viewport or window size, run a separate visual QA pass there; otherwise, choose a smaller but still realistic size and inspect it explicitly.
- Distinguish presence from implementation: if an intended affordance is technically there but not clearly perceptible because of weak contrast, occlusion, clipping, or instability, treat that as a visual failure.
- If any required visible region is clipped, cut off, obscured, or pushed outside the viewport in the state you are evaluating, treat that as a bug even if page-level scroll metrics appear acceptable.
- Look for clipping, overflow, distortion, layout imbalance, inconsistent spacing, alignment problems, illegible text, weak contrast, broken layering, and awkward motion states.
- Judge aesthetic quality as well as correctness. The UI should feel intentional, coherent, and visually pleasing for the task.
- Prefer viewport screenshots for signoff. Use full-page captures only as secondary debugging artifacts, and capture a focused screenshot when a region needs closer inspection.
- If motion makes a screenshot ambiguous, wait briefly for the UI to settle, then capture the image you are actually evaluating.
- Before signoff, explicitly ask: what visible part of this interface have I not yet inspected closely?
- Before signoff, explicitly ask: what visible defect would most likely embarrass this result if the user looked closely?

### Signoff

- The functional path passed with normal user input.
- Coverage is explicit against the shared QA inventory: note which requirements, implemented features, controls, states, and claims were exercised, and call out any intentional exclusions.
- The visual QA pass covered the whole relevant interface.
- Each user-visible claim has a matching visual check and reviewed screenshot artifact from the state and viewport or window size where that claim matters.
- The viewport-fit checks passed for the intended initial view and any required minimum supported viewport or window size.
- If the product launches in a window, the as-launched size, placement, and initial layout were checked before any manual resize or repositioning.
- The UI is not just functional; it is visually coherent and not aesthetically weak for the task.
- Functional correctness, viewport fit, and visual quality must each pass on their own; one does not imply the others.
- A short exploratory pass was completed for interactive products, and the response mentions what that pass covered.
- If screenshot review and numeric checks disagreed at any point, the discrepancy was investigated before signoff; visible clipping in screenshots is a failure to resolve, not something metrics can overrule.
- Include a brief negative confirmation of the main defect classes you checked for and did not find.
- Cleanup was executed, or you intentionally kept the session alive for further work.

## Screenshot Examples

If you plan to emit a screenshot through `codex.emitImage(...)`, use the CSS-normalized paths in the next section by default. Those are the canonical examples for screenshots that will be interpreted by the model or used for coordinate-based follow-up actions. Keep raw captures as an exception for fidelity-sensitive debugging only; the raw exception examples appear after the normalization guidance.

### Model-bound screenshots (default)

If you will emit a screenshot with `codex.emitImage(...)` for model interpretation, normalize it to CSS pixels for the exact region you captured before emitting. This keeps returned coordinates aligned with Playwright CSS pixels if the reply is later used for clicking, and it also reduces image payload size and model token cost.

Do not emit raw native-window screenshots by default. Skip normalization only when you explicitly need device-pixel fidelity, such as Retina or DPI artifact debugging, pixel-accurate rendering inspection, or another fidelity-sensitive case where raw pixels matter more than payload size. For local-only inspection that will not be emitted to the model, raw capture is fine.

Do not assume `page.screenshot({ scale: "css" })` is enough in native-window mode (`viewport: null`). In Chromium on macOS Retina displays, headed native-window screenshots can still come back at device-pixel size even when `scale: "css"` is requested. The same caveat applies to Electron windows launched through Playwright because Electron runs with `noDefaultViewport`, and `appWindow.screenshot({ scale: "css" })` may still return device-pixel output.

Use separate normalization paths for web pages and Electron windows:

- Web: prefer `page.screenshot({ scale: "css" })` directly. If native-window Chromium still returns device-pixel output, resize inside the current page with canvas; no scratch page is required.
- Electron: do not use `appWindow.context().newPage()` or `electronApp.context().newPage()` as a scratch page. Electron contexts do not support that path reliably. Capture in the main process with `BrowserWindow.capturePage(...)`, resize with `nativeImage.resize(...)`, and emit those bytes directly.

Shared helpers and conventions:

```javascript
var emitJpeg = async function (bytes) {
  await codex.emitImage({
    bytes,
    mimeType: "image/jpeg",
    detail: "original",
  });
};

var emitWebJpeg = async function (surface, options = {}) {
  await emitJpeg(await surface.screenshot({
    type: "jpeg",
    quality: 85,
    scale: "css",
    ...options,
  }));
};

var clickCssPoint = async function ({ surface, x, y, clip }) {
  await surface.mouse.click(
    clip ? clip.x + x : x,
    clip ? clip.y + y : y
  );
};

var tapCssPoint = async function ({ page, x, y, clip }) {
  await page.touchscreen.tap(
    clip ? clip.x + x : x,
    clip ? clip.y + y : y
  );
};
```

- Use `page` or `mobilePage` for web, or `appWindow` for Electron, as the `surface`.
- Treat `clip` as CSS pixels from `getBoundingClientRect()` in the renderer.
- Prefer JPEG at `quality: 85` unless lossless fidelity is specifically required.
- For full-image captures, use returned `{ x, y }` directly.
- For clipped captures, add the clip origin back when clicking.

### Web CSS normalization

Preferred web path for explicit-viewport contexts, and often for web in general:

```javascript
await emitWebJpeg(page);
```

Mobile web uses the same path; substitute `mobilePage` for `page`:

```javascript
await emitWebJpeg(mobilePage);
```

If the model returns `{ x, y }`, click it directly:

```javascript
await clickCssPoint({ surface: page, x, y });
```

Mobile web click path:

```javascript
await tapCssPoint({ page: mobilePage, x, y });
```

For web `clip` screenshots or element screenshots in this normal path, `scale: "css"` usually works directly. Add the region origin back when clicking.

- `await emitWebJpeg(page, { clip })`
- `await emitWebJpeg(mobilePage, { clip })`
- `await clickCssPoint({ surface: page, clip, x, y })`
- `await tapCssPoint({ page: mobilePage, clip, x, y })`
- `await clickCssPoint({ surface: page, clip: box, x, y })` after `const box = await locator.boundingBox()`

Web native-window fallback when `scale: "css"` still comes back at device-pixel size:

```javascript
var emitWebScreenshotCssScaled = async function ({ page, clip, quality = 0.85 } = {}) {
  var NodeBuffer = (await import("node:buffer")).Buffer;
  const target = clip
    ? { width: clip.width, height: clip.height }
    : await page.evaluate(() => ({
        width: window.innerWidth,
        height: window.innerHeight,
      }));

  const screenshotBuffer = await page.screenshot({
    type: "png",
    ...(clip ? { clip } : {}),
  });

  const bytes = await page.evaluate(
    async ({ imageBase64, targetWidth, targetHeight, quality }) => {
      const image = new Image();
      image.src = `data:image/png;base64,${imageBase64}`;
      await image.decode();

      const canvas = document.createElement("canvas");
      canvas.width = targetWidth;
      canvas.height = targetHeight;

      const ctx = canvas.getContext("2d");
      ctx.imageSmoothingEnabled = true;
      ctx.drawImage(image, 0, 0, targetWidth, targetHeight);

      const blob = await new Promise((resolve) =>
        canvas.toBlob(resolve, "image/jpeg", quality)
      );

      return new Uint8Array(await blob.arrayBuffer());
    },
    {
      imageBase64: NodeBuffer.from(screenshotBuffer).toString("base64"),
      targetWidth: target.width,
      targetHeight: target.height,
      quality,
    }
  );

  await emitJpeg(bytes);
};
```

For a full viewport fallback capture, treat returned `{ x, y }` as direct CSS coordinates:

```javascript
await emitWebScreenshotCssScaled({ page });
await clickCssPoint({ surface: page, x, y });
```

For a clipped fallback capture, add the clip origin back:

```javascript
await emitWebScreenshotCssScaled({ page, clip });
await clickCssPoint({ surface: page, clip, x, y });
```

### Electron CSS normalization

For Electron, normalize in the main process instead of opening a scratch Playwright page. The helper below returns CSS-scaled bytes for the full content area or for a clipped CSS-pixel region. Treat `clip` as content-area CSS pixels, for example values taken from `getBoundingClientRect()` in the renderer.

```javascript
var emitElectronScreenshotCssScaled = async function ({ electronApp, clip, quality = 85 } = {}) {
  const bytes = await electronApp.evaluate(async ({ BrowserWindow }, { clip, quality }) => {
    const win = BrowserWindow.getAllWindows()[0];
    const image = clip ? await win.capturePage(clip) : await win.capturePage();

    const target = clip
      ? { width: clip.width, height: clip.height }
      : (() => {
          const [width, height] = win.getContentSize();
          return { width, height };
        })();

    const resized = image.resize({
      width: target.width,
      height: target.height,
      quality: "best",
    });

    return resized.toJPEG(quality);
  }, { clip, quality });

  await emitJpeg(bytes);
};
```

Full Electron window:

```javascript
await emitElectronScreenshotCssScaled({ electronApp });
await clickCssPoint({ surface: appWindow, x, y });
```

Clipped Electron region using CSS pixels from the renderer:

```javascript
var clip = await appWindow.evaluate(() => {
  const rect = document.getElementById("board").getBoundingClientRect();
  return {
    x: Math.round(rect.x),
    y: Math.round(rect.y),
    width: Math.round(rect.width),
    height: Math.round(rect.height),
  };
});

await emitElectronScreenshotCssScaled({ electronApp, clip });
await clickCssPoint({ surface: appWindow, clip, x, y });
```

### Raw Screenshot Exception Examples

Use these only when raw pixels matter more than CSS-coordinate alignment, such as Retina or DPI artifact debugging, pixel-accurate rendering inspection, or other fidelity-sensitive review.

Web desktop raw emit:

```javascript
await codex.emitImage({
  bytes: await page.screenshot({ type: "jpeg", quality: 85 }),
  mimeType: "image/jpeg",
  detail: "original",
});
```

Electron raw emit:

```javascript
await codex.emitImage({
  bytes: await appWindow.screenshot({ type: "jpeg", quality: 85 }),
  mimeType: "image/jpeg",
  detail: "original",
});
```

Mobile raw emit after the mobile web context is already running:

```javascript
await codex.emitImage({
  bytes: await mobilePage.screenshot({ type: "jpeg", quality: 85 }),
  mimeType: "image/jpeg",
  detail: "original",
});
```

## Viewport Fit Checks (Required)

Do not assume a screenshot is acceptable just because the main widget is visible. Before signoff, explicitly verify that the intended initial view matches the product requirement, using both screenshot review and numeric checks.

- Define the intended initial view before signoff. For scrollable pages, this is the above-the-fold experience. For app-like shells, games, editors, dashboards, or tools, this is the full interactive surface plus the controls and status needed to use it.
- Use screenshots as the primary evidence for fit. Numeric checks support the screenshots; they do not overrule visible clipping.
- Signoff fails if any required visible region is clipped, cut off, obscured, or pushed outside the viewport in the intended initial view, even if page-level scroll metrics appear acceptable.
- Scrolling is acceptable when the product is designed to scroll and the initial view still communicates the core experience and exposes the primary call to action or required starting context.
- For fixed-shell interfaces, scrolling is not an acceptable workaround if it is needed to reach part of the primary interactive surface or essential controls.
- Do not rely on document scroll metrics alone. Fixed-height shells, internal panes, and hidden-overflow containers can clip required UI while page-level scroll checks still look clean.
- Check region bounds, not just document bounds. Verify that each required visible region fits within the viewport in the startup state.
- For Electron or desktop apps, verify both the launched window size and placement and the renderer's initial visible layout before any manual resize or repositioning.
- Passing viewport-fit checks only proves that the intended initial view is visible without unintended clipping or scrolling. It does not prove that the UI is visually correct or aesthetically successful.

Web or renderer check:

```javascript
console.log(await page.evaluate(() => ({
  innerWidth: window.innerWidth,
  innerHeight: window.innerHeight,
  clientWidth: document.documentElement.clientWidth,
  clientHeight: document.documentElement.clientHeight,
  scrollWidth: document.documentElement.scrollWidth,
  scrollHeight: document.documentElement.scrollHeight,
  canScrollX: document.documentElement.scrollWidth > document.documentElement.clientWidth,
  canScrollY: document.documentElement.scrollHeight > document.documentElement.clientHeight,
})));
```

Electron check:

```javascript
console.log(await appWindow.evaluate(() => ({
  innerWidth: window.innerWidth,
  innerHeight: window.innerHeight,
  clientWidth: document.documentElement.clientWidth,
  clientHeight: document.documentElement.clientHeight,
  scrollWidth: document.documentElement.scrollWidth,
  scrollHeight: document.documentElement.scrollHeight,
  canScrollX: document.documentElement.scrollWidth > document.documentElement.clientWidth,
  canScrollY: document.documentElement.scrollHeight > document.documentElement.clientHeight,
})));
```

Augment the numeric check with `getBoundingClientRect()` checks for the required visible regions in your specific UI when clipping is a realistic failure mode; document-level metrics alone are not sufficient for fixed shells.

## Dev Server

For local web debugging, keep the app running in a persistent TTY session. Do not rely on one-shot background commands from a short-lived shell.

Use the project's normal start command, for example:

```bash
npm start
```

Before `page.goto(...)`, verify the chosen port is listening and the app responds.

For Electron debugging, launch the app from `js_repl` through `_electron.launch(...)` so the same session owns the process. If the Electron renderer depends on a separate dev server (for example Vite or Next), keep that server running in a persistent TTY session and then relaunch or reload the Electron app from `js_repl`.

## Cleanup

Only run cleanup when the task is actually finished:

- This cleanup is manual. Exiting Codex, closing the terminal, or losing the `js_repl` session does not implicitly run `electronApp.close()`, `context.close()`, or `browser.close()`.
- For Electron specifically, assume the app may keep running if you leave the session without executing the cleanup cell first.

```javascript
if (electronApp) {
  await electronApp.close().catch(() => {});
}

if (mobileContext) {
  await mobileContext.close().catch(() => {});
}

if (context) {
  await context.close().catch(() => {});
}

if (browser) {
  await browser.close().catch(() => {});
}

browser = undefined;
context = undefined;
page = undefined;
mobileContext = undefined;
mobilePage = undefined;
electronApp = undefined;
appWindow = undefined;

console.log("Playwright session closed");
```

If you plan to exit Codex immediately after debugging, run the cleanup cell first and wait for the `"Playwright session closed"` log before quitting.

## Common Failure Modes

- `Cannot find module 'playwright'`: run the one-time setup in the current workspace and verify the import before using `js_repl`.
- Playwright package is installed but the browser executable is missing: run `npx playwright install chromium`.
- `page.goto: net::ERR_CONNECTION_REFUSED`: make sure the dev server is still running in a persistent TTY session, recheck the port, and prefer `http://127.0.0.1:<port>`.
- `electron.launch` hangs, times out, or exits immediately: verify the local `electron` dependency, confirm the `args` target, and make sure any renderer dev server is already running before launch.
- `Identifier has already been declared`: reuse the existing top-level bindings, choose a new name, or wrap the code in `{ ... }`. Use `js_repl_reset` only when the kernel is genuinely stuck.
- `browserContext.newPage: Protocol error (Target.createTarget): Not supported` while working with Electron: do not use `appWindow.context().newPage()` or `electronApp.context().newPage()` as a scratch page; use the Electron-specific screenshot normalization flow in the model-bound screenshots section.
- `js_repl` timed out or reset: rerun the bootstrap cell and recreate the session with shorter, more focused cells.
- Browser launch or network operations fail immediately: confirm the session was started with `--sandbox danger-full-access` and restart that way if needed.


</details>
