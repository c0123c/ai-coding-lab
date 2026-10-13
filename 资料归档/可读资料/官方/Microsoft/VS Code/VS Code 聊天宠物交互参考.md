# VS Code 聊天宠物交互参考

列出宠物对聊天、移动和用户操作的反应及个性化入口；属于界面功能说明，与开发方法关系较弱。

- 来源：Microsoft／VS Code
- 资料类型：文档源码（含未展开模板）
- 存档版本：`7dde53519982`。
- 文档时间：官方审核：[2026-10-07](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/reference/chat-pet.md>)；源码提交（存档版）：[2026-10-06](<https://github.com/microsoft/vscode-docs/commit/4fe6b03408632f2e9338a970d26afa480e4a022d>)；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/reference/chat-pet.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Microsoft/VS Code/VS Code 聊天宠物交互参考.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
ContentId: 587745a8-bc1a-4a6c-9570-93c10066cbe2
DateApproved: 10/7/2026
MetaDescription: Customize Blobby, the {% data variables.product.prodname_vscode_shortname %} pet, with colors, achievements, movement, and chat reactions.
MetaSocialImage: ../images/shared/github-copilot-social.png
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# VS Code 宠物互动和反应

`feature(chat-pet)`

Blobby，{% data variables.product.prodname_vscode %} 宠物，坐在聊天输入框上方，并在您使用 agents 时做出反应。此参考列出了 Blobby 对聊天活动、移动、直接交互和上下文菜单操作的反应。

![屏幕截图显示 VS Code 宠物进入和离开聊天输入（稳定蓝色和 Insiders 绿色）。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-pet/toggle.png>)

要了解如何显示、隐藏和控制宠物，请参阅[个性化聊天](<https://github.com/docs/chat/chat-overview.md#use-the-vs-code-pet>)。

## 聊天活动反应

|活动|宠物反应|预览|
|----------|--------------|---------|
|在聊天输入周围移动指针|它的学生跟随指针移动。|![显示稳定和内部宠物用眼睛跟随指针的屏幕截图。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-pet/cursor-gaze.png>)|
|输入提示|它在它的小终端上工作。|![显示 Stable 和 Insiders 宠物在小型终端上打字的屏幕截图。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-pet/typing-v2.png>)|
|等待请求运行|它用动画气泡来思考。|![显示稳定和内部宠物处理带有动画气泡的请求的屏幕截图。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-pet/processing.png>)|
|回复确认或问题|当 agent 需要您的输入时，它会拍手。|![显示稳定和内部宠物在需要输入时鼓掌的屏幕截图。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-pet/needs-input.png>)|
|让聊天保持空闲 20 秒|当睡眠气泡长大并从鼻子中飘出时，它就会睡着。与宠物互动或在输入框中键入以唤醒它。|![屏幕截图显示稳定和内部宠物在睡眠中睡眠并在互动后醒来。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-pet/sleep-wake-pr330399.png>)|
|完成回复|它执行按钮按下庆祝活动。|![显示稳定和内部宠物按下红色按钮的屏幕截图。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-pet/click-button-v2.png>)|

## 运动反应

|互动|宠物反应|预览|
|-------------|--------------|---------|
|拖放|宠物会跟随指针直到您释放它。|![显示稳定和内部宠物被举起并放到聊天输入上的屏幕截图。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-pet/drag-drop-v2.png>)|
|弗利克|在重力将其拉下之前，宠物会继续沿着手势的方向前进。| |
|按`kbstyle(Left)`或`kbstyle(Right)`|宠物沿着输入框跳跃，直到到达边缘。|![显示稳定和内部宠物使用箭头键左右跳跃的屏幕截图。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-pet/keyboard-hop.png>)|
|按`kbstyle(Shift+Left)`或`kbstyle(Shift+Right)`|宠物被扔向墙壁。| |
|在宠物的左侧和右侧之间快速移动指针，或交替使用箭头键|宠物变得头晕。|![屏幕截图显示稳定和内部宠物跟随快速移动的指针并变得头晕。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-pet/dizzy.png>)|
|将宠物扔过或扔过输入框|宠物在聊天底部陷入消失效果，重新出现在顶部，并落在输入框上。|![屏幕截图显示稳定和内部宠物落入传送门，重新出现在顶部，并降落在聊天输入上。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-pet/fall-respawn-pr330399.png>)|

## 活化反应

选择正在休息的宠物，或将其聚焦并按`kbstyle(Enter)`或`kbstyle(Space)`，可随机触发其中一种反应。宠物不会立即重复之前的反应。

|反应|行为|预览|
|----------|----------|---------|
|按下按钮|按下红色按钮。|![显示稳定和内部宠物按下红色按钮的屏幕截图。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-pet/click-button-v2.png>)|
|心|与心反应。|![显示稳定和内部宠物与红心反应的屏幕截图。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-pet/click-love.png>)|
|酷|戴上太阳镜。|![显示稳定和内部宠物戴上墨镜的屏幕截图。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-pet/click-cool.png>)|
| VS Code |侧躺并狂吠。|![屏幕截图显示稳定和内部宠物侧躺并吠叫。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-pet/click-yap-static.png>)|
|唱歌|唱出一个音符。|![显示稳定和内部宠物唱歌音符的屏幕截图。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-pet/click-sing-v2.png>)|
|无语|变得无语。|![显示稳定和内部宠物变得无语的截图。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-pet/click-speechless.png>)|
|担心|看起来很担心。|![屏幕截图显示稳定和内部宠物看起来很担心。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-pet/click-worry.png>)|

## 成就和上下文菜单操作

当您解锁成就时，宠物身上会出现一颗金色星星，持续 10 秒。当星星可见时选择宠物以打开**成就**。

打开宠物的上下文菜单以访问以下操作：

|行动|行为|预览|
|--------|----------|---------|
|**成就**|查看锁定和解锁的成就及其附属奖励。| |
|**Go 正在奔跑** 或 ** 回来**|宠物会躲到视线之外，偶尔偷看聊天内容，并在被叫时返回。|![显示马厩和内部宠物奔跑、向外看、然后返回的屏幕截图。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-pet/on-the-run-v2.png>)|
|**增大**、**缩小**或**重置大小**|以 20 个百分比的步长调整宠物的大小，或恢复其默认大小。您可以将其缩小到 40% 或继续增大。|![显示稳定宠物和内部宠物生长和缩小的屏幕截图。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-pet/grow-shrink.png>)|
|**改变颜色**|选择“稳定”或“内部人士”，或在聊天中输入 `/blobby` 以解锁预设和自定义颜色。对于自定义颜色，请使用颜色选择器或输入十六进制值，然后选择 ** 应用颜色 **。|![屏幕截图显示 Blobby 为稳定蓝色和 Insiders 绿色。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-pet/toggle.png>)|

## 相关资源

* [在{% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/chat/chat-overview.md>)中使用聊天
* [使用{% data variables.copilot.agents_window %}](<https://github.com/docs/agents/run/agents-window.md>)


## 原文对照

<details>
<summary>展开完整原文</summary>

# VS Code pet interactions and reactions

`feature(chat-pet)`

Blobby, the {% data variables.product.prodname_vscode %} pet, sits above the chat input box and reacts as you work with agents. This reference lists Blobby's reactions to chat activity, movement, direct interactions, and context-menu actions.

![Screenshot showing the VS Code pet entering and leaving the chat input in Stable blue and Insiders green.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-pet/toggle.png>)

To learn how to show, hide, and control the pet, see [Personalize chat](<https://github.com/docs/chat/chat-overview.md#use-the-vs-code-pet>).

## Chat activity reactions

| Activity | Pet reaction | Preview |
|----------|--------------|---------|
| Move the pointer around the chat input | Its pupils follow the pointer. | ![Screenshot showing the Stable and Insiders pets following a pointer with their eyes.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-pet/cursor-gaze.png>) |
| Type a prompt | It works at its tiny terminal. | ![Screenshot showing the Stable and Insiders pets typing at a tiny terminal.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-pet/typing-v2.png>) |
| Wait for a request to run | It thinks with an animated speech bubble. | ![Screenshot showing the Stable and Insiders pets processing a request with an animated speech bubble.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-pet/processing.png>) |
| Respond to a confirmation or question | It claps when the agent needs your input. | ![Screenshot showing the Stable and Insiders pets clapping when input is needed.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-pet/needs-input.png>) |
| Leave chat idle for 20 seconds | It falls asleep as a sleep bubble grows and drifts from its nose. Interact with the pet or type in the input box to wake it. | ![Screenshot showing the Stable and Insiders pets sleeping with growing sleep bubbles and waking after an interaction.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-pet/sleep-wake-pr330399.png>) |
| Finish a response | It performs the button-press celebration. | ![Screenshot showing the Stable and Insiders pets pressing their red buttons.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-pet/click-button-v2.png>) |

## Movement reactions

| Interaction | Pet reaction | Preview |
|-------------|--------------|---------|
| Drag and drop | The pet follows the pointer until you release it. | ![Screenshot showing the Stable and Insiders pets being lifted and dropped onto the chat input.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-pet/drag-drop-v2.png>) |
| Flick | The pet continues in the direction of the gesture before gravity pulls it down. | |
| Press `kbstyle(Left)` or `kbstyle(Right)` | The pet hops along the input box until it reaches an edge. | ![Screenshot showing the Stable and Insiders pets hopping right and left with the arrow keys.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-pet/keyboard-hop.png>) |
| Press `kbstyle(Shift+Left)` or `kbstyle(Shift+Right)` | The pet is thrown toward a wall. | |
| Move the pointer quickly between the pet's left and right sides, or alternate the arrow keys | The pet becomes dizzy. | ![Screenshot showing the Stable and Insiders pets following a rapidly moving pointer and becoming dizzy.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-pet/dizzy.png>) |
| Drop or throw the pet past the input box | The pet falls into a despawn effect at the bottom of chat, reappears at the top, and lands on the input box. | ![Screenshot showing the Stable and Insiders pets falling into portals, reappearing at the top, and landing on the chat input.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-pet/fall-respawn-pr330399.png>) |

## Activation reactions

Select the pet while it is resting, or focus it and press `kbstyle(Enter)` or `kbstyle(Space)`, to trigger one of these reactions at random. The pet doesn't immediately repeat its previous reaction.

| Reaction | Behavior | Preview |
|----------|----------|---------|
| Button press | Presses its red button. | ![Screenshot showing the Stable and Insiders pets pressing their red buttons.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-pet/click-button-v2.png>) |
| Heart | Reacts with hearts. | ![Screenshot showing the Stable and Insiders pets reacting with hearts.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-pet/click-love.png>) |
| Cool | Puts on sunglasses. | ![Screenshot showing the Stable and Insiders pets putting on sunglasses.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-pet/click-cool.png>) |
| VS Code | Lies on its side and yaps. | ![Screenshot showing the Stable and Insiders pets lying on their sides and yapping.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-pet/click-yap-static.png>) |
| Sing | Sings a musical note. | ![Screenshot showing the Stable and Insiders pets singing a musical note.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-pet/click-sing-v2.png>) |
| Speechless | Becomes speechless. | ![Screenshot showing the Stable and Insiders pets becoming speechless.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-pet/click-speechless.png>) |
| Worry | Looks worried. | ![Screenshot showing the Stable and Insiders pets looking worried.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-pet/click-worry.png>) |

## Achievements and context-menu actions

When you unlock an achievement, a gold star appears on the pet for 10 seconds. Select the pet while the star is visible to open **Achievements**.

Open the pet's context menu to access these actions:

| Action | Behavior | Preview |
|--------|----------|---------|
| **Achievements** | View locked and unlocked achievements and their accessory rewards. | |
| **Go on the Run** or **Come Back** | The pet ducks out of sight, occasionally peeks into chat, and returns when called. | ![Screenshot showing the Stable and Insiders pets going on the run, peeking out, and coming back.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-pet/on-the-run-v2.png>) |
| **Grow**, **Shrink**, or **Reset Size** | Resize the pet in 20 percent steps, or restore its default size. You can shrink it to 40 percent or continue to grow it. | ![Screenshot showing the Stable and Insiders pets growing and shrinking.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-pet/grow-shrink.png>) |
| **Change Color** | Choose Stable or Insiders, or enter `/blobby` in chat to unlock presets and custom colors. For a custom color, use the color picker or enter a hex value, and then select **Apply Color**. | ![Screenshot showing Blobby in Stable blue and Insiders green.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/chat-pet/toggle.png>) |

## Related resources

* [Use chat in {% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/chat/chat-overview.md>)
* [Use the {% data variables.copilot.agents_window %}](<https://github.com/docs/agents/run/agents-window.md>)


</details>
