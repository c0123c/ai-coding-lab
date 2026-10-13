# Git 工作树是什么以及何时使用

比较分支与暂存的切换方式和独立工作树，说明创建、管理、使用成本及桌面 Agent 并行任务中的用途。

- 来源：GitHub／工程文章
- 资料类型：存档文档
- 文档时间：发表：[2026-06-16](<https://github.blog/ai-and-ml/github-copilot/what-are-git-worktrees-and-why-should-i-use-them/>)；更新：[2026-07-13](<https://github.blog/ai-and-ml/github-copilot/what-are-git-worktrees-and-why-should-i-use-them/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.blog/ai-and-ml/github-copilot/what-are-git-worktrees-and-why-should-i-use-them/>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/工程文章/Git 工作树是什么以及何时使用.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 什么是 git 工作树，为什么要使用它们？

最近 git 中最热门的似乎是工作树的概念。这……有点有趣，因为它们从 2015 就已经存在了。

但是，尽管如此，它们很酷，您可能想知道为什么要使用它们，它们与分支有何不同，以及为什么它们突然如此受欢迎。

我们来谈谈吧！

## 使用分支和存储进行上下文切换

假设您生活在一个没有工作树的世界中，正在处理一张票证，突然出现一个紧急错误，您必须切换上下文。

首先，您可以隐藏您的工作：

```
git stash "wip feature login"
```

然后你将切换到主分支并更新：

```
git checkout main 
git pull origin main
```

然后创建一个错误修复分支：

```
git checkout -b hotfix-bug
```

然后你修复所有内容，提交并推送分支：

```
git add . 
git commit -m "fix broken submit button" 
git push origin hotfix-bug
```

然后，在合并拉取请求后，您可能会返回计算机并拉取 main 并删除 bug 分支：

```
git checkout main 
git pull origin main 
git branch -d hotfix-bug
```

然后你可以回到你正在开发的功能：

```
git checkout feature-login 
git stash pop
```

唷。我们在哪里？

切换、重新加载文件、根据更改的内容重新安装 `node_modules` 等等的心理开销很大。上下文切换负担很重。

现在，这是一个基本的示例，但有时开发人员会通过执行一些更复杂的 `git stash` 命令，甚至同一存储库的多个克隆（我对此感到内疚）来解决这种混乱。

直到……工作树！

## 使用工作树进行上下文切换

使用工作树，您永远不会离开分支，也永远不会隐藏，并且原始功能的编辑器上下文保持不变。

```
git worktree add ../hotfix-workspace -b hotfix-bug main
```

这会立即创建一个名为 `hotfix-workspace` 的同级文件夹，并将其基于 main，并检查一个名为 `hotfix-bug` 的新分支。

现在您可以在新的编辑器窗口中打开该文件夹（或在其中打开 `cd`）并修复错误。您的原始编辑器窗口与您离开时完全相同。

```
cd ../hotfix-workspace 
# ...fix fix fix... 
git add . 
git commit -m "fix broken submit button" 
git push origin hotfix-bug
```

您可以像以前一样在线合并拉取请求，合并后，您可以简单地删除临时文件夹。

```
cd ../main-project 
git worktree remove ../hotfix-workspace
```

这样就顺利多了！工作树也可以超越 git 命令行。例如，[VS Code 在](<https://code.visualstudio.com/docs/sourcecontrol/branches-worktrees#_working-with-git-worktrees>) 中内置了完整的工作树支持。你有选择！无论您在哪里工作，工作树都可以让您零存储冲突风险，不会出现编辑器中断，并且您可以真正并行工作。

## 那么……为什么是现在？

在很长一段时间里，工作树相对不为人所知。大多数开发人员从未听说过它们，因为要么 Git GUI 不支持它们（或将它们视为二等公民），要么因为它们通常遵循已知的功能分支模式，然后工作，然后拉取请求，然后合并，然后重复。

现在，我们作为开发人员的工作发生了变化。 AI 使我们比软件开发历史上任何时候都更加并行工作。开发人员并行运行如此多的会话，“代码审查文化”正在超越“代码编写文化”。

Agents 并且人类可以通过工作树并行地完成更多工作。这是 [GitHub Copilot 应用程序 ](<https://github.com/features/ai/github-app?utm_source=blog-git-worktree&utm_medium=blog&utm_campaign=github-copilot-app-ga-2026>) 以及许多其他现代工具的默认模式。

## 有什么问题吗？

工作树确实解决了很多问题，但肯定有一些事情需要注意。

* 依赖项膨胀：每个工作树文件夹都需要自己的项目依赖项副本。如果您在多个计算机上运行 `npm install` 或 `pip install`，您的计算机可能会很快变得非常满。
* 文件夹管理：您必须删除工作树文件夹，以避免随着时间的推移使父目录变得混乱。像 GitHub Copilot 应用程序这样的应用程序通常会为您处理这个问题，但如果您自己在终端中操作，这仍然是您可能必须自己做的事情。
* 全局 `.gitignore` 要求：如果您在主存储库目录中创建工作树文件夹，则必须手动将它们添加到 `.gitignore` 以免意外跟踪它们。您*可以*在您的主存储库之外创建这些工作树（许多应用程序默认这样做），但值得注意。
* 一个分支限制：Git 阻止您同时检出两个不同工作树中完全相同的分支，以防止数据损坏。

## 如何在 GitHub Copilot 应用程序中使用工作树？

好问题！很棒的是它们开箱即用。当您打开应用程序时，会出现一个下拉菜单，询问您要在主屏幕上的哪个位置运行新会话。默认是一个新的工作树。

![GitHub Copilot 应用程序中“新工作树”下拉列表的屏幕截图。选项包括“新工作树”、“本地存储库”或“云”。](<https://github.blog/wp-content/uploads/2026/06/Screenshot-2026-06-16-at-1.35.20-PM.png?resize=924%2C819>)

然后，一旦您启动一个新会话，您可以单击应用程序顶部的会话名称，您将看到（有趣！）生成的工作树名称，以及它所在的路径、工作树所在的项目以及有关您所做更改的详细信息。

![上一步生成的工作树的屏幕截图。](<https://github.blog/wp-content/uploads/2026/06/Screenshot-2026-06-16-at-1.36.45-PM.png?resize=877%2C331>)

简单的豌豆柠檬挤！

## 我应该使用工作树吗？

我会给你最资深的开发人员答案：这要看情况！您可能更喜欢以一种或另一种方式工作。您可能不会并行执行那么多工作，也不会喜欢分支和存储的思维模型。从现在开始你可能只做工作树。您可能想两者都做！

世界尽在掌握，您今天就可以在 [GitHub Copilot 应用程序 ](<https://github.com/features/ai/github-app?utm_source=blog-git-worktree&utm_medium=blog&utm_campaign=github-copilot-app-ga-2026>) 中尝试所有这些。

---

## 标签：

* [Git](<https://github.blog/tag/git/>)
* [git工作树](<https://github.blog/tag/git-worktrees/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [GitHub Copilot 应用程序](<https://github.blog/tag/github-copilot-app/>)

## 撰写者

![卡西迪·威廉姆斯](<https://avatars.githubusercontent.com/u/1454517?v=4&s=200>)

### [卡西迪·威廉姆斯](<https://github.blog/author/cassidoo/>)

[@cassidoo](<https://github.com/cassidoo>)

Cassidy 是 GitHub 的开发者宣传高级总监。她喜欢构建软件、为初创公司提供建议以及教导开发人员如何构建更好的软件。她在 cassidoo.co/newsletter 上有一份每周时事通讯，您可以在其中获取她的最新动态、练习编码问题，并在收件箱中查看笑话！


## 原文对照

<details>
<summary>展开完整原文</summary>

# What are git worktrees, and why should I use them?

It seems like the latest hotness in git these days is the concept of worktrees. Which… is kind of funny because they’ve been around since 2015.

But, nevertheless, they are cool, and you might be wondering why you’d use them, how they differ from branches, and why they are suddenly so popular.

Let’s talk about it!

## Context switching with branches and stashing

Let’s say you lived in a worktree-less world, and were working on a ticket, and suddenly an urgent bug came to you and you had to switch contexts.

First, you might stash your work:

```
git stash "wip feature login"
```

Then you’d switch to your main branch and update:

```
git checkout main 
git pull origin main
```

Then make a bugfix branch:

```
git checkout -b hotfix-bug
```

Then you’d fix everything, commit, and push the branch:

```
git add . 
git commit -m "fix broken submit button" 
git push origin hotfix-bug
```

Then after merging a pull request, you might return back to your computer and pull main and remove the bug branch:

```
git checkout main 
git pull origin main 
git branch -d hotfix-bug
```

And then you could go back to the feature you were working on:

```
git checkout feature-login 
git stash pop
```

Phew. Where were we?

The mental overhead of switching around, reloading files, reinstalling `node_modules` based on whatever changed, and so on, is a lot. The context switching burden is heavy.

Now, this is a basic example, but sometimes developers would work around this kind of chaos with doing some more complicated `git stash` commands, or even multiple clones of the same repo (I’m guilty of that one).

Until… worktrees!

## Context switching with worktrees

With worktrees, you never leave your branch and you never stash, and your editor context for your original feature stays untouched.

```
git worktree add ../hotfix-workspace -b hotfix-bug main
```

This instantly creates a sibling folder called `hotfix-workspace`, and bases it on main, and checks out a new branch called `hotfix-bug`.

Now you can open that folder in a new editor window (or `cd` into it) and fix the bug. Your original editor window stays exactly as you left it.

```
cd ../hotfix-workspace 
# ...fix fix fix... 
git add . 
git commit -m "fix broken submit button" 
git push origin hotfix-bug
```

You merge the pull request online just like before, and once it’s merged, you can simply delete the temporary folder.

```
cd ../main-project 
git worktree remove ../hotfix-workspace
```

This is so much smoother! Worktrees can go beyond the git command line, too. For example, [VS Code has full worktree support built in](<https://code.visualstudio.com/docs/sourcecontrol/branches-worktrees#_working-with-git-worktrees>). You have options! And no matter where you work, worktrees give you zero risk of stash conflicts, there’s no editor disruption, and you can truly work in parallel.

## So… why now?

For a really long time, worktrees were relatively unknown. Most developers had never heard of them, because either Git GUIs didn’t support them (or treated them as second-class citizens), or because they just usually followed the known pattern of feature branch, then work, then pull request, then merge, then repeat.

Now, our work as developers has changed. AI has made us work in parallel more than we ever have before in the history of software development. Developers run so many sessions in parallel, and “code review culture” is growing beyond “code writing culture.”

Agents and humans can do more in parallel with worktrees. It’s the default mode for the [GitHub Copilot app](<https://github.com/features/ai/github-app?utm_source=blog-git-worktree&utm_medium=blog&utm_campaign=github-copilot-app-ga-2026>), and for many other modern tools.

## What’s the catch?

Worktrees do solve a whole lot of issues, but there’s definitely some things to watch out for.

* Dependency bloat: each worktree folder requires its own copy of your project dependencies. If you’re running `npm install` or `pip install` across multiple of them, your computer might get very full, very quickly.
* Folder management: you have to delete the worktree folders, to avoid cluttering your parent directory over time. Apps like the GitHub Copilot app do often handle this for you, but it’s still something you might have to do yourself if you’re operating in the terminal yourself.
* Global `.gitignore` requirements: if you create worktree folders inside your main repo directory, you have to manually add them to `.gitignore` to not accidentally track them. You *can* make these worktrees outside of your main repo (and many apps do that by default), but it’s worth noting.
* One branch limits: Git prevents you from checking out the exact same branch in two different worktrees at the same time to prevent data corruption.

## How do I use worktrees in the GitHub Copilot app?

Great question! What’s awesome is they “just work” out of the box. When you open the app, there’s a dropdown that asks you where you want to run your new session on the home screen. The default is a new worktree.

![Screenshot of the 'New worktree' dropdown in the GitHub Copilot app. Options are 'New worktree', 'Local repository', or 'Cloud.'](<https://github.blog/wp-content/uploads/2026/06/Screenshot-2026-06-16-at-1.35.20-PM.png?resize=924%2C819>)

Then, once you kick off a new session, you can click the session name at the top of the app, and you’ll see the (fun!) generated name of your worktree, as well as the path where it’s located, the project that worktree is for, and details about the changes that you’ve made.

![Screenshot of the worktree generated in the previous step.](<https://github.blog/wp-content/uploads/2026/06/Screenshot-2026-06-16-at-1.36.45-PM.png?resize=877%2C331>)

Easy peasy lemon squeezy!

## Should I use worktrees?

I will give you the most senior developer answer I can: It depends! You might prefer working in one way or another. You might not do as much work in parallel and like the mental model of branches and stashing. You might only do worktrees from now on. You might want to do both!

The world’s your oyster, and you can try them all in the [GitHub Copilot app](<https://github.com/features/ai/github-app?utm_source=blog-git-worktree&utm_medium=blog&utm_campaign=github-copilot-app-ga-2026>) today.

---

## Tags:

* [Git](<https://github.blog/tag/git/>)
* [git worktrees](<https://github.blog/tag/git-worktrees/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [GitHub Copilot app](<https://github.blog/tag/github-copilot-app/>)

## Written by

![Cassidy Williams](<https://avatars.githubusercontent.com/u/1454517?v=4&s=200>)

### [Cassidy Williams](<https://github.blog/author/cassidoo/>)

[@cassidoo](<https://github.com/cassidoo>)

Cassidy is senior director for developer advocacy here at GitHub. She enjoys building software, advising startups, and teaching developers how to build better. She has a weekly newsletter at cassidoo.co/newsletter where you can get her updates, practice coding problems, and a joke in your inbox!


</details>
