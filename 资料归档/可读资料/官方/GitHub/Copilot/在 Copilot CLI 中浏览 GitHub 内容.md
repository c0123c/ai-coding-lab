# 在 Copilot CLI 中浏览 GitHub 内容

介绍终端标签页中的 Issue、PR 和 Gist 浏览、搜索、修改与快捷键，减少开发操作时的界面切换。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-09-10](<https://github.com/github/docs/commit/3cdc3598ef267c21f8b93923b545ab923cddaae9>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/copilot-cli/use-copilot-cli/browse-issues-prs-gists.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/在 Copilot CLI 中浏览 GitHub 内容.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Browsing issues, pull requests, and gists from {% data variables.copilot.copilot_cli %}
shortTitle: Browse issues, PRs, gists
allowTitleToDifferFromFilename: true
intro: 'Use the tabs in an interactive {% data variables.copilot.copilot_cli_short %} session to browse issues, pull requests and gists, without leaving the terminal.'
versions:
  feature: copilot
contentType: how-tos
category:
  - Author and optimize with Copilot # Copilot discovery page
  - Build with Copilot CLI # Copilot CLI bespoke page
docsTeamMetrics:
  - copilot-cli
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

默认情况下，Git 存储库的交互式 {% data variables.copilot.copilot_cli_short %} 会话在屏幕顶部有四个选项卡：
* **会话**：输入{% data variables.product.prodname_copilot_short %}提示的常规聊天体验。
* **问题**：{% data variables.product.prodname_dotcom %}上当前存储库中的未解决问题。
* **拉取请求**：在{% data variables.product.prodname_dotcom %}上的当前存储库中打开拉取请求。
* **要点**：您在{% data variables.product.prodname_dotcom %}上的要点。

通过 **Issues**、**Pull requests** 和 **Gists** 选项卡，您可以浏览 {% data variables.product.prodname_dotcom_the_website %} 中的内容，而无需切换到浏览器。当您想要执行以下操作时，这很有用：

* **查找要处理的问题或拉取请求**。
* **将项目拉入您的聊天中** — 快速将所选项目的引用插入到提示框中，以便您可以要求 {% data variables.product.prodname_copilot_short %} 进行调查、修复、评论或审核。
* **跳转到 {% data variables.product.prodname_dotcom_the_website %}** 上的某个项目 - 例如，当您想要评论某个问题、合并拉取请求或编辑要点时。

> [！NOTE]
> **问题**和**拉取请求**选项卡仅当{% data variables.copilot.copilot_cli_short %}在{% data variables.product.prodname_dotcom %}内运行时显示存储库。在其他目录中，仅显示 **Session** 和 **Gists** 选项卡。

## 在选项卡之间切换

* 按<kbd>Tab</kbd>移动到下一个选项卡。
* 按<kbd>Shift</kbd>+<kbd>Tab</kbd>移动到上一个选项卡。
* 使用鼠标单击选项卡即可切换到该选项卡。

> [！NOTE]
> 单击选项卡需要鼠标支持。默认情况下启用此功能，但可以使用 `--no-mouse` 命令行选项禁用。如果鼠标支持已被禁用，请使用 `--mouse=on` 选项重新启用鼠标支持。

当 CLI 的另一部分（例如斜杠命令选择器）正在观察您的击键时，选项卡切换会暂停。

## 常用键盘控制

**问题**、**拉取请求**和**要点**选项卡都使用相同的控件。无论您使用的是以下哪个选项卡：

* 使用向上和向下箭头键突出显示列表中的项目。
* 使用<kbd>Shift</kbd>+<kbd>↑</kbd>和<kbd>Shift</kbd>+<kbd>↓</kbd>在多页中移动到上一页或下一页列表。
* 按<kbd>输入</kbd>可显示突出显示项目的详细视图。在详细信息视图中按 <kbd>Esc</kbd> 返回列表。
* 按 <kbd>o</kbd> 打开 {% data variables.product.prodname_dotcom_the_website %} 上突出显示的项目（或者在详细视图中，打开当前项目）。
* 按<kbd>c</kbd>将对该项目的引用插入提示输入区域，并跳回**会话**选项卡。
* 按 <kbd>/</kbd>（在 **Issues** 和 **Pull requests** 选项卡上）使用您自己的搜索词过滤列表。输入您的术语，按 <kbd>Enter</kbd> 运行搜索，然后按 <kbd>Esc</kbd> 取消或清除搜索。

有关您可以使用的全套按键，请参阅本文末尾的[键盘参考](<#键盘参考>)。

## 浏览问题

**问题**选项卡列出了当前存储库中涉及您的**开放**问题 - 您创作、分配、提及或评论的问题。每行显示问题标题、问题编号、作者以及问题打开的时间。

用于填充列表的 {% data variables.product.prodname_dotcom %} 搜索查询显示在其上方。按 <kbd>a</kbd> 在仅显示涉及您的问题和显示存储库中的每个未解决问题之间切换。

按 <kbd>c</kbd> 会将对该问题的引用插入到 **Session** 选项卡上的提示框中。然后您可以输入与此问题相关的提示。例如：

```copilot
#1234 suggest a fix for this bug
```

## 浏览拉取请求

**拉取请求**选项卡列出了当前存储库中涉及您的**打开**拉取请求 - 您编写的、被分配的、被提及的、被要求审查或评论的拉取请求。每行显示拉取请求标题、编号、作者以及拉取请求打开的时间。

用于填充列表的 {% data variables.product.prodname_dotcom %} 搜索查询显示在其上方。按 <kbd>a</kbd> 可在仅显示涉及您的拉取请求和显示存储库中每个打开的拉取请求之间切换。

按 <kbd>c</kbd> 将对拉取请求的引用插入到 **Session** 选项卡上的提示框中。然后，您可以输入与此拉取请求相关的提示。例如：

```copilot
#5678 check this out and run tests
```

## 搜索问题和拉取请求

默认情况下，**问题**和**拉取请求**选项卡显示涉及您的项目。按 <kbd>a</kbd> 在此 (`involves:@me`) 和所有打开的项目之间切换。

要运行您自己的搜索，请按 <kbd>/</kbd>。将打开一个内联搜索框，您可以在其中键入搜索词，然后按 <kbd>Enter</kbd> 运行搜索。

按 <kbd>Esc</kbd> 可在键入时取消，或清除已应用的搜索并返回到默认列表。

您的搜索词将添加到选项卡已应用的限定符中，因此结果始终仅限于当前存储库中打开的项目，并且除非您按了 <kbd>a</kbd>，否则结果仅限于涉及您的项目。输入普通搜索词而不是搜索限定符：包含冒号的文本将作为文字短语进行搜索，因此 `label:bug` 等术语不会被解释为限定词。

## 浏览您的要点

**Gists** 选项卡列出了您登录的 {% data variables.product.prodname_dotcom %} 帐户拥有的要点。显示了公开和秘密的要点。与 **Issues** 和 **Pull requests** 选项卡不同，**Gists** 选项卡的范围不限于存储库 — 无论您在何处启动，它始终可用。 CLI。

按 <kbd>c</kbd> 会将要点的 URL 插入到 **Session** 选项卡上的提示框中。然后，您可以输入与此要点相关的提示。例如：

```copilot
https://gist.github.com/USERNAME/GIST-ID summarize this
```

## 修改问题、拉取请求和要点

**问题**、**拉取请求**和**要点**选项卡是只读环境。您可以通过两种方式处理在这些选项卡之一中找到的项目：

* **按<kbd>o</kbd>在{% data variables.product.prodname_dotcom_the_website %}**上打开它并使用网页UI修改该项目。
* **按<kbd>c</kbd>将参考放入提示框**并要求{% data variables.product.prodname_copilot_short %}为您执行该活动。例如：

  ```copilot
  #1234 add a comment: "Any update on this?"
  ```

  ```copilot
  #5678 merge this
  ```

  ```copilot
  https://gist.github.com/USERNAME/GIST-ID delete this
  ```

## 自定义选项卡

您可以使用 `tabs` 对象重新排序、隐藏或关闭设置文件 (`~/.copilot/settings.json`) 中的选项卡：

```json copy
{
    "tabs": {
        "enabled": true,
        "sort": ["copilot", "pull-requests", "issues", "gists"],
        "hide": ["gists"]
    }
}
```

* `enabled`：设置为`false`以完全关闭选项卡式界面。
* `sort`：选项卡出现的顺序。使用标识符 `copilot`（**Session** 选项卡）、`issues`、`pull-requests` 和 `gists`。您省略的任何选项卡都会在您列出的选项卡之后保留默认顺序。未知的标识符将被忽略。
* `hide`：要隐藏的选项卡，使用相同的标识符。 **会话**选项卡(`copilot`)无法隐藏。

## 键盘参考

**Issues**、**Pull requests** 和 **Gists** 选项卡中的页脚提示栏总结了可用的键：

|钥匙|哪里|行动|
| --- | --- | --- |
|<kbd>选项卡</kbd> / <kbd>Shift</kbd>+<kbd>选项卡</kbd>|任何主页选项卡|切换到下一个或上一个主页选项卡。|
| <kbd>↓</kbd> / <kbd>↑</kbd><br><kbd>j</kbd> / <kbd>k</kbd> |列表视图|突出显示列表中的下一个或上一个项目。|
|<kbd>Shift</kbd>+<kbd>↓</kbd> / <kbd>Shift</kbd>+<kbd>↑</kbd>|列表视图|显示多页列表中的下一页或上一页。|
|<kbd>输入</kbd>|列表视图|打开突出显示项目的详细信息视图。|
| <kbd>o</kbd> |列表视图或详细视图|在浏览器中打开 {% data variables.product.prodname_dotcom_the_website %} 上突出显示的项目。|
| <kbd>c</kbd> |列表视图或详细视图|在提示输入区域插入对该项目的引用，然后跳回 **Session** 选项卡。|
| <kbd>a</kbd> |**问题**和**拉取请求**选项卡上的列表视图|在仅显示涉及您的项目和显示存储库中的每个打开的项目之间切换。|
| <kbd>/</kbd> |**问题**和**拉取请求**选项卡上的列表视图|打开搜索框。|
|<kbd>输入</kbd>|搜索框|运行搜索查询。|
|<kbd>Esc</kbd>|搜索框/应用搜索|取消搜索框，或关闭搜索结果。|
|<kbd>Esc</kbd>|详情查看|返回到列表视图。|

## 进一步阅读

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/use-copilot-cli/manage-pull-requests>)
* [AUTOTITLE](<https://github.com/copilot/reference/copilot-cli-reference/cli-command-reference#slash-commands-in-the-interactive-interface>)


## 原文对照

<details>
<summary>展开完整原文</summary>

By default, interactive {% data variables.copilot.copilot_cli_short %} sessions for Git repositories have four tabs at the top of the screen:
* **Session**: The regular chat experience where you enter prompts for {% data variables.product.prodname_copilot_short %}.
* **Issues**: Open issues in the current repository on {% data variables.product.prodname_dotcom %}.
* **Pull requests**: Open pull requests in the current repository on {% data variables.product.prodname_dotcom %}.
* **Gists**: Your gists on {% data variables.product.prodname_dotcom %}.

The **Issues**, **Pull requests**, and **Gists** tabs let you browse content from {% data variables.product.prodname_dotcom_the_website %} without having to switch to a browser. This is useful when you want to:

* **Find an issue or pull request to work on**.
* **Pull an item into your chat** — quickly insert a reference to the selected item into the prompt box so that you can ask {% data variables.product.prodname_copilot_short %} to investigate, fix, comment on, or review it.
* **Jump to an item on {% data variables.product.prodname_dotcom_the_website %}** — for example when you want to comment on an issue, merge a pull request, or edit a gist.

> [!NOTE]
> The **Issues** and **Pull requests** tabs are only shown when {% data variables.copilot.copilot_cli_short %} is running inside a {% data variables.product.prodname_dotcom %} repository. In other directories, only the **Session** and **Gists** tabs are shown.

## Switching between tabs

* Press <kbd>Tab</kbd> to move to the next tab.
* Press <kbd>Shift</kbd>+<kbd>Tab</kbd> to move to the previous tab.
* Use the mouse to click on a tab to switch to it.

> [!NOTE]
> Clicking tabs requires mouse support. This is enabled by default but can be disabled with the `--no-mouse` command-line option. Use the `--mouse=on` option to re-enable mouse support if it has been disabled.

Tab switching is paused while another part of the CLI—such as the slash command picker—is observing your keystrokes.

## Common keyboard controls

The **Issues**, **Pull requests**, and **Gists** tabs all use the same controls. Regardless of which of these tabs you're on:

* Use the up and down arrow keys to highlight an item in the list.
* Use <kbd>Shift</kbd>+<kbd>↑</kbd> and <kbd>Shift</kbd>+<kbd>↓</kbd> to move to the previous or next page in a multi-page list.
* Press <kbd>Enter</kbd> to display a detailed view of the highlighted item. Press <kbd>Esc</kbd> in the details view to return to the list.
* Press <kbd>o</kbd> to open the highlighted item (or, in the detailed view, the current item) on {% data variables.product.prodname_dotcom_the_website %}.
* Press <kbd>c</kbd> to insert a reference to the item into the prompt input area and jump back to the **Session** tab.
* Press <kbd>/</kbd> (on the **Issues** and **Pull requests** tabs) to filter the list with your own search terms. Type your terms, press <kbd>Enter</kbd> to run the search, and <kbd>Esc</kbd> to cancel or clear it.

For the full set of keypresses you can use, see [Keyboard reference](<#keyboard-reference>) at the end of this article.

## Browsing issues

The **Issues** tab lists the **open** issues in the current repository that involve you—issues you authored, were assigned, were mentioned in, or commented on. Each row shows the issue title, the issue number, the author, and how long ago the issue was opened.

The {% data variables.product.prodname_dotcom %} search query used to populate the list is shown above it. Press <kbd>a</kbd> to toggle between showing only issues that involve you and showing every open issue in the repository.

Pressing <kbd>c</kbd> inserts a reference to the issue into the prompt box on the **Session** tab. You can then enter a prompt that relates to this issue. For example:

```copilot
#1234 suggest a fix for this bug
```

## Browsing pull requests

The **Pull requests** tab lists the **open** pull requests in the current repository that involve you—pull requests you authored, were assigned, were mentioned in, were asked to review, or commented on. Each row shows the pull request title, number, author, and how long ago the pull request was opened.

The {% data variables.product.prodname_dotcom %} search query used to populate the list is shown above it. Press <kbd>a</kbd> to toggle between showing only pull requests that involve you and showing every open pull request in the repository.

Pressing <kbd>c</kbd> inserts a reference to the pull request into the prompt box on the **Session** tab. You can then enter a prompt that relates to this pull request. For example:

```copilot
#5678 check this out and run tests
```

## Searching issues and pull requests

By default, the **Issues** and **Pull requests** tabs show items that involve you. Press <kbd>a</kbd> to toggle between this (`involves:@me`) and all open items.

To run your own search, press <kbd>/</kbd>. An inline search box opens where you can type your search terms, then press <kbd>Enter</kbd> to run the search.

Press <kbd>Esc</kbd> to cancel while typing, or to clear an applied search and return to the default list.

Your search terms are added to the qualifiers the tab already applies, so results are always limited to open items in the current repository—and, unless you have pressed <kbd>a</kbd>, to items that involve you. Enter plain search terms rather than search qualifiers: text containing a colon is searched for as a literal phrase, so a term such as `label:bug` is not interpreted as a qualifier.

## Browsing your gists

The **Gists** tab lists the gists owned by the {% data variables.product.prodname_dotcom %} account you are signed in to. Both public and secret gists are shown. Unlike the **Issues** and **Pull requests** tabs, the **Gists** tab is not scoped to a repository—it is always available, regardless of where you started the CLI.

Pressing <kbd>c</kbd> inserts the gist's URL into the prompt box on the **Session** tab. You can then enter a prompt that relates to this gist. For example:

```copilot
https://gist.github.com/USERNAME/GIST-ID summarize this
```

## Modifying issues, pull requests, and gists

The **Issues**, **Pull requests**, and **Gists** tabs are read-only environments. There are two ways you can work on an item you find in one of these tabs:

* **Press <kbd>o</kbd> to open it on {% data variables.product.prodname_dotcom_the_website %}** and use the web UI to modify the item.
* **Press <kbd>c</kbd> to drop a reference into the prompt box** and ask {% data variables.product.prodname_copilot_short %} to perform the activity for you. For example:

  ```copilot
  #1234 add a comment: "Any update on this?"
  ```

  ```copilot
  #5678 merge this
  ```

  ```copilot
  https://gist.github.com/USERNAME/GIST-ID delete this
  ```

## Customizing the tabs

You can reorder, hide, or turn off the tabs in your settings file (`~/.copilot/settings.json`) using the `tabs` object:

```json copy
{
    "tabs": {
        "enabled": true,
        "sort": ["copilot", "pull-requests", "issues", "gists"],
        "hide": ["gists"]
    }
}
```

* `enabled`: set to `false` to turn off the tabbed interface entirely.
* `sort`: the order in which tabs appear. Use the identifiers `copilot` (the **Session** tab), `issues`, `pull-requests`, and `gists`. Any tabs you omit keep their default order after the ones you list. Unknown identifiers are ignored.
* `hide`: tabs to hide, using the same identifiers. The **Session** tab (`copilot`) cannot be hidden.

## Keyboard reference

The footer hint bar in the **Issues**, **Pull requests**, and **Gists** tabs summarizes the available keys:

| Key | Where | Action |
| --- | --- | --- |
| <kbd>Tab</kbd> / <kbd>Shift</kbd>+<kbd>Tab</kbd> | Any home tab | Switch to the next or previous home tab. |
| <kbd>↓</kbd> / <kbd>↑</kbd><br><kbd>j</kbd> / <kbd>k</kbd> | List view | Highlight the next or previous item in a list. |
| <kbd>Shift</kbd>+<kbd>↓</kbd> / <kbd>Shift</kbd>+<kbd>↑</kbd> | List view | Display the next or previous page in a multi-page list. |
| <kbd>Enter</kbd> | List view | Open the details view for the highlighted item. |
| <kbd>o</kbd> | List view or details view | Open the highlighted item on {% data variables.product.prodname_dotcom_the_website %} in your browser. |
| <kbd>c</kbd> | List view or details view | Insert a reference to the item into the prompt input area and jump back to the **Session** tab. |
| <kbd>a</kbd> | List view on **Issues** and **Pull requests** tabs | Toggle between showing only items that involve you and showing every open item in the repository. |
| <kbd>/</kbd> | List view on **Issues** and **Pull requests** tabs | Open a search box. |
| <kbd>Enter</kbd> | Search box | Run the search query. |
| <kbd>Esc</kbd> | Search box / applied search | Cancel the search box, or dismiss the search results. |
| <kbd>Esc</kbd> | Details view | Return to the list view. |

## Further reading

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/use-copilot-cli/manage-pull-requests>)
* [AUTOTITLE](<https://github.com/copilot/reference/copilot-cli-reference/cli-command-reference#slash-commands-in-the-interactive-interface>)


</details>
