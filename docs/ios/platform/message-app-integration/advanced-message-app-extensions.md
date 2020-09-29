---
title: Xamarin 中的高级消息应用扩展
description: 本文介绍了在 Xamarin iOS 解决方案中使用消息应用扩展的高级方法，该解决方案与 Messages 应用集成并向用户提供新功能。
ms.prod: xamarin
ms.assetid: 394A1FDA-AF70-4493-9B2C-4CFE4BE791B6
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 8a1f386209ccc1f2cb33348930f29bf5ac65ce4f
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435614"
---
# <a name="advanced-message-app-extensions-in-xamarinios"></a>Xamarin 中的高级消息应用扩展

_本文介绍了在 Xamarin iOS 解决方案中使用消息应用扩展的高级方法，该解决方案与 Messages 应用集成并向用户提供新功能。_

新的 iOS 10，消息应用扩展与 **Messages** 应用集成并向用户提供新功能。 此扩展可以发送文本、不干胶标签、媒体文件和交互式消息。

## <a name="about-message-app-extensions"></a>关于消息应用扩展

如上所述，消息应用扩展与 **Messages** 应用集成，并向用户提供新功能。 此扩展可以发送文本、不干胶标签、媒体文件和交互式消息。 提供两种类型的消息应用扩展：

- **不干胶标签** -包含用户可添加到消息中的不干胶标签集合。 无需编写任何代码即可创建不干胶标签。
- **IMessage 应用** -可在邮件应用中提供自定义用户界面，用于选择不干胶标签，输入文本，包括 (具有可选类型转换的媒体文件) 以及创建、编辑和发送交互消息。

消息应用扩展提供了三种主要内容类型：

- **交互式消息** -应用生成的一种自定义消息内容，当用户点击消息时，将在前台启动应用。
- **不干胶标签** -由应用生成的映像，可包含在用户之间发送的消息中。 若要实现不干胶标签包应用的示例，请参阅 [冰淇淋生成器](/samples/xamarin/ios-samples/ios10-icecreambuilder) 示例应用。
- **其他支持的内容** -应用可以提供消息应用始终支持的类型的内容，如照片、视频、文本或链接。

新的 iOS 10，消息应用现在包含其自己的内置应用商店。 任何包含消息应用扩展的应用都将在此存储中显示和升级。 "新邮件" 应用抽屉将显示已从 "邮件" 应用商店下载的任何应用，以提供对用户的快速访问。

此外，iOS 10 中的新增功能，Apple 添加了内嵌应用归属，使用户能够轻松发现应用。 例如，如果一个用户从第2个用户未安装的应用发送内容 (例如) 的不干胶标签，则发送应用的名称将列在消息历史记录中的内容下。 如果用户点击应用程序的名称，将打开 "消息应用商店"，并在存储中选择应用。

消息应用扩展与开发人员熟悉的现有 iOS 应用类似，他们将有权访问标准 iOS 应用的所有标准框架和功能。 例如：

- 他们有权访问应用内购买。
- 他们有权访问 Apple Pay。
- 它们有权访问设备硬件，如相机。

仅在 iOS 10 上支持消息应用扩展，但是，可以在 watchOS 和 macOS 设备上查看这些扩展发送的内容。 添加到 watchOS 3 的新 _最近页面_ 将显示已从手机发送的最近不干胶标签，包括来自邮件应用扩展的标签，并允许用户从手表发送这些不干胶标签。

## <a name="about-interactive-messages"></a>关于交互消息

交互式消息显示自定义消息冒泡，并通过消息应用扩展提供。 它们允许用户创建交互式消息内容，将其插入消息输入字段并发送。

[![创建交互式消息内容](advanced-message-app-extensions-images/interactive01.png)](advanced-message-app-extensions-images/interactive01.png#lightbox)

接收用户可以通过在消息历史记录中点击消息 "冒泡" 来加载创建它的消息应用扩展，来回复交互式消息。 该扩展将以全屏幕启动，并允许用户撰写答复并将其发送回发起方用户。

[![扩展已启动全屏](advanced-message-app-extensions-images/interactive02.png)](advanced-message-app-extensions-images/interactive02.png#lightbox)

下面将详细介绍以下主题：

- 消息 API 概述
- 扩展生命周期
- 撰写消息
- 发送消息

## <a name="messages-api-overview"></a>消息 API 概述

用户调用消息应用扩展时，该扩展将显示在紧凑视图模式下的消息历史记录底部：

[![消息 API 概述](advanced-message-app-extensions-images/interactive03.png)](advanced-message-app-extensions-images/interactive03.png#lightbox)

1. `MSMessageAppViewController`消息应用扩展中的对象是在向用户显示扩展视图时调用的主类。
2. 此对话以对象实例的形式呈现给用户 `MSConversation` 。
3. `MSMessage`类表示会话中给定的消息冒泡。
4. `MSSession` 控制消息的发送方式。
5. `MSMessageTemplateLayout` 控制消息的显示方式

## <a name="the-extension-lifecycle"></a>扩展生命周期

查看正在激活的消息应用扩展过程：

[![消息应用扩展的进程变为活动状态](advanced-message-app-extensions-images/interactive04.png)](advanced-message-app-extensions-images/interactive04.png#lightbox)

1. 启动扩展后 (例如，从应用银箱) 中，消息应用将启动一个进程。
2. `DidBecomeActive`调用方法并传递一个 `MSConversation` ，它表示正在运行消息应用扩展的会话。
3. 因为该扩展是从中的开始 `UIViewController` `ViewWillAppear` ，并且 `ViewDidAppear` 称为。

接下来，请查看正在停用消息应用扩展的过程：

[![停用消息应用扩展的过程](advanced-message-app-extensions-images/interactive05.png)](advanced-message-app-extensions-images/interactive05.png#lightbox)

1. 停用消息应用扩展时， `ViewWillDisappear` 将首先调用方法。
2. 然后， `ViewDidDisappear` 将调用方法。
3. `WillResignActive`调用方法并传递一个 `MSConversation` ，它表示正在运行消息应用扩展的会话。 此时，将会释放邮件应用和扩展之间的连接。
4. 稍后，此过程由 Messages 应用终止。

由于扩展是一个短暂的生存期，系统会主动终止该扩展，以节省处理能力和电池电量。 设计和实现消息应用扩展时，开发人员应牢记这一点。

## <a name="composing-a-message"></a>撰写消息

消息应用扩展在进程中运行并显示其用户界面后，可以使用以下代码编写新消息：

```csharp
MSMessage ComposeMessage (IceCream iceCream, string caption, MSSession session = null)
{
    var components = new NSUrlComponents {
        QueryItems = iceCream.QueryItems
    };

    var layout = new MSMessageTemplateLayout {
        Image = iceCream.RenderSticker (true),
        Caption = caption
    };

    var message = new MSMessage (session ?? new MSSession()) {
        Url = components.Url,
        Layout = layout
    };

    return message;
}
```

此代码创建一个新的 `MSMessage` 并设置多个属性， (如 `Url`) 。 虽然该消息只能在 iOS 上创建，但可以同时发送到 iOS 和 macOS。

如果用户在 macOS 上单击会话中的消息冒泡，Mac 将尝试在 web 浏览器中打开 URL 中指定的地址。 因此，开发人员的网站应该能够在基于 macOS 的计算机上的 web 浏览器中显示邮件的某种表示形式。

`AccessibilityLabel`屏幕读取器使用属性来读取与用户的会话的脚本。 `Layout`属性指定如何显示消息，当前仅支持，并如下所 `MSMessageTemplateLayout` 示：

[![MSMessageTemplateLayout 模板](advanced-message-app-extensions-images/interactive06.png)](advanced-message-app-extensions-images/interactive06.png#lightbox)

`Image`的属性 `MSMessageTemplateLayout` 为屏幕上的 MessageBubble 的主体提供内容。 `MediaFileUrl`属性还为消息冒泡正文提供内容，但允许 (不支持的内容 `UIImage` （例如，将在后台) 循环的视频文件）。 如果同时 `Image` 提供了和 `MediaFileUrl` 属性，则 `Image` 属性将优先。 `MediaFileUrl`支持可以由 Media Player 框架) 媒体格式播放的任何格式的 PNG、JPEG、GIF 和视频 (。

建议的媒体大小为 300 x 300 像素，分辨率为3倍。 还会接受稍大、较小的资产，而且 Apple 建议使用几种不同大小的测试，以获得最佳结果。 此消息应用将会关闭示例并根据需要缩放此媒体。

向接收方发送资产时，"消息" 应用会自动转码附加的任何介质，以优化它们通过网络传输。 因此，Apple 不鼓励在附加到邮件的媒体中包括文本，因为媒体会缩小并压缩以进行传输，从而可能导致文本无法辨认。

`ImageTitle`和 `ImageSubtitle` 属性提供消息气泡中显示的媒体的说明。 这些属性将以文本的形式发送到接收设备，在接收设备上，它们将在图像左下角直接呈现。

`Caption`、 `SubCaption` `TrailingCaption` 和 `TrailingSubcaption` 属性进一步描述了图像，并将呈现在图像下的部分中。 将所有这些属性设置为 `null` 将在没有标题区的情况下创建消息气泡：

[![不带标题区的消息气泡](advanced-message-app-extensions-images/interactive07.png)](advanced-message-app-extensions-images/interactive07.png#lightbox)

最后要注意的是，邮件应用程序会在消息气泡的左上角绘制消息应用扩展的图标。

## <a name="sending-a-message"></a>发送消息

撰写完毕后 `MSMessage` ，可以使用以下代码发送：

```csharp
public void SendMessage (MSMessage message)
{
    // Insert the message into the conversation
    ActiveConversation.InsertMessage (message, (error) => {
        // Did the message send successfully?
        if (error == null) {
            // Handle successful send
        } else {
            // Report Error
            Console.WriteLine ("Error: {0}", error);
        }
    };

}
```

`ActiveConversation`的属性 `MSMessagesAppViewController` 将包含在其中启动消息应用扩展的当前会话。

调用的 `InsertMessage` 以在 `MSConversation` 会话中包含消息，并处理可能出现的任何错误。 如果成功包含该消息，则消息冒泡将显示在输入字段中。

此外，扩展可以向会话发送不同类型的数据，例如：

- **全文** - `ActiveConversation.InsertText ("Message", (error) => {...});`
- **附件** - `ActiveConversation.InsertAttachment (new NSUrl ("path"), "filename", (error) => {...});`
- **不干胶标签**  -  `ActiveConversation.InsertSticker (sticker, (obj) => {...});`其中 `sticker` 是 `MSSticker` 。

新内容出现在输入字段中后，用户就可以通过点击蓝色的 " **发送** " 按钮来发送消息， (就像) 任何典型消息一样。 邮件应用程序扩展无法自动发送内容，此过程完全由用户控制。

## <a name="handling-the-compact-and-expanded-modes"></a>处理紧凑模式和展开模式

可以通过以下两种不同的视图模式之一来显示消息应用扩展：

[![消息应用扩展以两种不同的视图模式显示： Compact & 扩展](advanced-message-app-extensions-images/interactive08.png)](advanced-message-app-extensions-images/interactive08.png#lightbox)

- **Compact** -这是默认模式，其中消息应用扩展占据消息视图的底部25%。 在 Compact 模式下，应用程序不能访问键盘、水平滚动或轻扫手势识别器。 应用可以访问输入字段，并会 `InsertMessage` 立即向用户显示对的调用。
- 已**展开**-消息应用扩展会填充整个消息视图。 它无权访问输入字段，但有权访问键盘、水平滚动和轻扫手势识别器。

用户随时可以通过编程方式或手动方式在这两种模式之间切换消息应用扩展，并且应立即响应视图模式中的任何更改。

请看下面的示例，该示例在两个不同的视图模式之间处理切换。 每个状态都需要两个不同的视图控制器。 `StickerBrowserViewController`处理**精简**视图，并 `AddStickerViewController` 将处理**展开**的视图：

```csharp
using System;

using Messages;
using Foundation;
using UIKit;

namespace MessagesExtension {
    public partial class MessagesViewController : MSMessagesAppViewController, IIceCreamsViewControllerDelegate, IBuildIceCreamViewControllerDelegate {
        public MessagesViewController (IntPtr handle) : base (handle)
        {
        }

        public override void WillBecomeActive (MSConversation conversation)
        {
            base.WillBecomeActive (conversation);

            // Present the view controller appropriate for the conversation and presentation style.
            PresentViewController (conversation, PresentationStyle);
        }

        public override void WillTransition (MSMessagesAppPresentationStyle presentationStyle)
        {
            var conversation = ActiveConversation;
            if (conversation == null)
                throw new Exception ("Expected an active converstation");

            // Present the view controller appropriate for the conversation and presentation style.
            PresentViewController (conversation, presentationStyle);
        }

        void PresentViewController (MSConversation conversation, MSMessagesAppPresentationStyle presentationStyle)
        {
            // Determine the controller to present.
            UIViewController controller;

            if (presentationStyle == MSMessagesAppPresentationStyle.Compact) {
                // Show a list of previously created ice creams.
                controller = InstantiateIceCreamsController ();
            } else {
                var iceCream = new IceCream (conversation.SelectedMessage);
                controller = iceCream.IsComplete ? InstantiateCompletedIceCreamController (iceCream) : InstantiateBuildIceCreamController (iceCream);
            }

            foreach (var child in ChildViewControllers) {
                child.WillMoveToParentViewController (null);
                child.View.RemoveFromSuperview ();
                child.RemoveFromParentViewController ();
            }

            AddChildViewController (controller);
            controller.View.Frame = View.Bounds;
            controller.View.TranslatesAutoresizingMaskIntoConstraints = false;
            View.AddSubview (controller.View);

            controller.View.LeftAnchor.ConstraintEqualTo (View.LeftAnchor).Active = true;
            controller.View.RightAnchor.ConstraintEqualTo (View.RightAnchor).Active = true;
            controller.View.TopAnchor.ConstraintEqualTo (View.TopAnchor).Active = true;
            controller.View.BottomAnchor.ConstraintEqualTo (View.BottomAnchor).Active = true;

            controller.DidMoveToParentViewController (this);
        }

        UIViewController InstantiateIceCreamsController ()
        {
            // Instantiate a `IceCreamsViewController` and present it.
            var controller = Storyboard.InstantiateViewController (IceCreamsViewController.StoryboardIdentifier) as IceCreamsViewController;
            if (controller == null)
                throw new Exception ("Unable to instantiate an IceCreamsViewController from the storyboard");

            controller.Builder = this;
            return controller;
        }

        UIViewController InstantiateBuildIceCreamController (IceCream iceCream)
        {
            // Instantiate a `BuildIceCreamViewController` and present it.
            var controller = Storyboard.InstantiateViewController (BuildIceCreamViewController.StoryboardIdentifier) as BuildIceCreamViewController;
            if (controller == null)
                throw new Exception ("Unable to instantiate an BuildIceCreamViewController from the storyboard");

            controller.IceCream = iceCream;
            controller.Builder = this;

            return controller;
        }

        public UIViewController InstantiateCompletedIceCreamController (IceCream iceCream)
        {
            // Instantiate a `BuildIceCreamViewController` and present it.
            var controller = Storyboard.InstantiateViewController (CompletedIceCreamViewController.StoryboardIdentifier) as CompletedIceCreamViewController;
            if (controller == null)
                throw new Exception ("Unable to instantiate an CompletedIceCreamViewController from the storyboard");

            controller.IceCream = iceCream;
            return controller;
        }

        public void DidSelectAdd (IceCreamsViewController controller)
        {
            Request (MSMessagesAppPresentationStyle.Expanded);
        }

        public void Build (BuildIceCreamViewController controller, IceCreamPart iceCreamPart)
        {
            var conversation = ActiveConversation;
            if (conversation == null)
                throw new Exception ("Expected a conversation");

            var iceCream = controller.IceCream;
            if (iceCream == null)
                throw new Exception ("Expected the controller to be displaying an ice cream");

            string messageCaption = string.Empty;
            var b = iceCreamPart as Base;
            var s = iceCreamPart as Scoops;
            var t = iceCreamPart as Topping;

            if (b != null) {
                iceCream.Base = b;
                messageCaption = "Let's build an ice cream";
            } else if (s != null) {
                iceCream.Scoops = s;
                messageCaption = "I added some scoops";
            } else if (t != null) {
                iceCream.Topping = t;
                messageCaption = "Our finished ice cream";
            } else {
                throw new Exception ("Unexpected type of ice cream part selected.");
            }

            // Create a new message with the same session as any currently selected message.
            var message = ComposeMessage (iceCream, messageCaption, conversation.SelectedMessage?.Session);

            // Add the message to the conversation.
            conversation.InsertMessage (message, null);

            // If the ice cream is complete, save it in the history.
            if (iceCream.IsComplete) {
                var history = IceCreamHistory.Load ();
                history.Append (iceCream);
                history.Save ();
            }

            Dismiss ();
        }

        MSMessage ComposeMessage (IceCream iceCream, string caption, MSSession session = null)
        {
            var components = new NSUrlComponents {
                QueryItems = iceCream.QueryItems
            };

            var layout = new MSMessageTemplateLayout {
                Image = iceCream.RenderSticker (true),
                Caption = caption
            };

            var message = new MSMessage (session ?? new MSSession()) {
                Url = components.Url,
                Layout = layout
            };

            return message;
        }
    }
}
```

`DidTransition`重写方法以处理两种模式间的切换：

```csharp
public override void DidTransition (MSMessagesAppPresentationStyle presentationStyle)
{
    base.DidTransition (presentationStyle);

    // Take action based on style
    switch (presentationStyle) {
    case MSMessagesAppPresentationStyle.Compact:
        PresentStickerBrowser ();
        break;
    case MSMessagesAppPresentationStyle.Expanded:
        PresentAddSticker ();
        break;
    }
}
```

或者，应用程序可以使用 `WillTransition` 方法来处理视图模式更改，然后再将其呈现给用户 (如以上) 上的 Icecream 生成器示例中所做的那样。 有关详细信息，请参阅 [进一步的不干胶标签自定义](~/ios/platform/message-app-integration/intro-to-message-app-extensions.md) 文档。

## <a name="replying-to-a-message"></a>回复消息

在答复消息时，有两种情况需要消息应用扩展才能处理：

[![处于非活动和活动模式的消息应用扩展](advanced-message-app-extensions-images/interactive09.png)](advanced-message-app-extensions-images/interactive09.png#lightbox)

- **扩展处于非活动状态** -消息应用扩展的消息消息中有一个冒泡消息，用户可以点击此项来激活扩展并继续交互式对话。
- **扩展处于活动状态** -用户可以点击消息脚本中的消息应用扩展的消息冒泡来进入展开的视图模式，并在交互进程从中断的位置继续。

### <a name="the-extension-is-inactive"></a>扩展处于非活动状态

当用户在消息脚本中向消息冒泡，并且消息应用扩展处于非活动状态时，将发生以下过程：

[![处理非活动消息气泡](advanced-message-app-extensions-images/interactive10.png)](advanced-message-app-extensions-images/interactive10.png#lightbox)

1. 用户点击扩展的消息冒泡。
2. 启动扩展后，消息应用将启动进程。
3. `DidBecomeActive`调用方法并传递一个 `MSConversation` ，它表示正在运行消息应用扩展的会话。
4. 因为该扩展是从中的开始 `UIViewController` `ViewWillAppear` ，并且 `ViewDidAppear` 称为。

完成此过程后，将在展开的视图模式下显示消息应用扩展。

### <a name="the-extension-is-active"></a>扩展处于活动状态

当用户在消息脚本中向消息冒泡，并且消息应用扩展处于活动状态时，将发生以下过程：

[![处理活动消息气泡](advanced-message-app-extensions-images/interactive11.png)](advanced-message-app-extensions-images/interactive11.png#lightbox)

1. 用户点击扩展的消息冒泡。
2. 由于消息应用扩展已处于活动状态，因此 `WillTransition` 调用的方法 `MSMessagesAppViewController` 来处理从 Compact 切换到展开的视图模式。
3. `DidSelectMessage`调用的方法 `MSMessagesAppViewController` 并传递 `MSMessage` `MSConversation` 消息冒泡所属的和。
4. `DidTransition`调用的方法 `MSMessagesAppViewController` 来处理从 Compact 到展开的视图模式的切换。

同样，在此过程完成后，将在展开的视图模式下显示消息应用扩展。

### <a name="accessing-the-selected-message"></a>访问选定的消息

在这两种情况下，当用户点击属于消息应用扩展的消息气泡时，它需要 `MSMessage` 使用的属性来获取对的访问权限 `SelectedMessage` `MSConversation` 。

例如：

```csharp
using System;
using Foundation;
using Messages;
using UIKit;

namespace MessageExtension
{
    public partial class MessagesViewController : MSMessagesAppViewController
    {
        ...

        #region Override Methods
        public override void DidSelectMessage (MSMessage message, MSConversation conversation)
        {
            base.DidSelectMessage (message, conversation);

            // Get selected message
            var selected = conversation.SelectedMessage;

            // Present the user interface to continue editing
            // the conversation
            ...
        }
        #endregion
    }
}
```

所选消息应显示在消息应用扩展的 UI 中，用户应允许编写响应。

## <a name="removing-partially-completed-messages"></a>删除部分完成的消息

在会话中的两个用户之间发送交互式对话的不同步骤的过程中，部分完成的消息冒泡会开始打乱消息脚本：

[![部分完成的消息冒泡可能会打乱消息脚本](advanced-message-app-extensions-images/interactive12.png)](advanced-message-app-extensions-images/interactive12.png#lightbox)

相反，消息应用扩展应将上一条消息冒泡折叠为消息脚本中的简洁注释：

[![折叠消息脚本中的前一条消息冒泡](advanced-message-app-extensions-images/interactive13.png)](advanced-message-app-extensions-images/interactive13.png#lightbox)

此操作通过使用 `MSSession` 折叠所有现有步骤来处理。 因此， `DidSelectMessage` `MSMessagesAppViewController` 可以将类的方法修改为如下所示：

```csharp
public override void DidSelectMessage (MSMessage message, MSConversation conversation)
{
    base.DidSelectMessage (message, conversation);

    MSSession session;
    var summary = "";

    // Get selected message
    var selected = conversation.SelectedMessage;

    // Does the selected message already have a session?
    if (selected.Session == null) {
        // No, create a new session
        session = new MSSession ();
        summary = "Let's build an ice cream";
    } else {
        // Yes, use the existing session
        session = selected.Session;
        summary = "I added some scoops";
    }

    // Create an instance of the message being composed
    var existingMessage = new MSMessage (session);
    message.SummaryText = summary;
    ...

    // Present the user interface to continue editing
    // the conversation
    ...
}
```

如果所选消息已有退出 `MSSession` ，则使用它来创建新的 `MSSession` 。 `SummaryText`的属性用于向 `MSMessage` 折叠的前面的步骤添加标题。 如果该 `SummaryText` 属性设置为 `null` ，则将从对话脚本中完全删除该会话中的前面的步骤。

## <a name="advanced-message-api-features"></a>高级消息 API 功能

在上面详细介绍了新消息 API 的基本功能后，接下来要检查 Apple 内置于框架中的一些更高级的功能。

首先，类中有几个其他替代方法 `MSMessagesAppViewController` ，可提供对会话的更深层访问：

- `DidStartSendingMessage` -当用户点击 "发送" 按钮时，将调用此项。 这并不意味着该消息实际上已传递给接收方，只是发送进程已启动。
- `DidCancelSendingMessage` -当用户点击对话脚本的消息气泡右上角的 *X* 按钮时，会发生这种情况。
- `DidReceiveMessage` -当消息应用扩展处于活动状态时，将调用此方法。从会话中的某个参与者接收到新消息。

### <a name="group-conversations"></a>组对话

当用户涉及到 (三个或更多个人) 的组对话中时，可以使用消息应用扩展，这在设计和实现消息应用扩展时必须考虑到这一点。

请看一下与三个用户的组对话中的下列交互：

[![与三个用户的组对话中的交互](advanced-message-app-extensions-images/interactive14.png)](advanced-message-app-extensions-images/interactive14.png#lightbox)

1. 用户1向组发送交互式消息，要求用户2和用户3选择快餐浇头。
2. 用户2选择 tomatoes。
3. 用户3选择泡菜。
4. 用户2的和用户3的选择几乎同时到达用户1。 因此，用户2的选择将折叠为摘要行并不可用。 这种情况也可能已翻转，并显示用户2的选项，并且用户3被折叠。

无论是哪种情况，都不会出现这种行为，因为用户1应能够访问用户2的和用户3的选项。 为了应对这种情况，Apple 建议将消息应用扩展存储在云中，并使用在 `MSMessage` 用户) 之间发送的 (的 URL 属性来访问此状态。

当用户发送消息时，将生成会话令牌并将其推送到当前消息状态下的云。 当用户点击对话稿本中的消息冒泡时，会话令牌用于从云中检索当前会话状态。

### <a name="sender-identifiers"></a>发件人标识符

若要讨论如何访问消息发件人的标识符，请使用上面给出的组会话示例：

[![组会话发送标识符](advanced-message-app-extensions-images/interactive15.png)](advanced-message-app-extensions-images/interactive15.png#lightbox)

1. 同样，用户1将发送一个组交互式消息，要求用户2和用户3选择快餐浇头。
2. 用户3选取泡菜。
3. 用户3的选择将向用户1返回，用户2尚未回复。
4. 由于 Apple 非常关注用户隐私，因此，消息应用扩展仅知道唯一标识符 (作为 `NSUUID` 在会话中分配给每个参与者的) 。 在本地设备上，仅知道当前用户的标识符。
5. `MSMessage`具有一个 `SenderIdentifier` 属性，该属性与用户在扩展已知的参与者列表中的一个匹配。
6. 每个用户设备的参与者列表都有自己的副本，但仅知道本地用户的标识符。
7. 发送消息时，其 `SenderIdentifier` 属性也称为本地用户的属性。

可以通过以下方式使用发送方标识符：

- 通过查看参与者列表，扩展可以获取会话中的用户数。
- 当扩展接收到来自用户的消息时，它可以跟踪发送方标识符。 如果它收到具有相同发送方标识符的另一条消息，则该扩展将知道该消息来自同一用户。
- 它们可用于帮助确定会话中的特定用户。

发件人标识符可以在的任何文本字段中使用，方法是使用 `MSMessageTemplateLayout` 美元符号 () 来为其加上前缀 `$` 。 例如：

```csharp
// Pass along the sender identifier
var layout = new MSMessageTemplateLayout()
layout.Caption = string.format("${0} wants pickles.",Conversation.LocalParticipantIdentifier.UuidString);
```

当 "消息" 应用显示具有此类型格式的消息气泡时，它会将替换为 `$uuid...` 发送该消息的会话中的参与者的联系人姓名。

发件人标识符在每个设备上都是唯一的，因此请再次查看上面的关系图，请注意，用户1的设备和用户3的设备对于会话中的每个参与者都有不同的唯一发送方标识符。

发件人标识符的范围限定为安装消息应用扩展。 因此，如果用户卸载并重新安装了邮件应用程序扩展，将为新的安装生成新的发送程序标识符。

若要访问发送方标识符，扩展可以使用以下代码：

```csharp
public override void DidStartSendingMessage (MSMessage message, MSConversation conversation)
{
    base.DidStartSendingMessage (message, conversation);

    // Get the sender's participant ID
    var senderID = message.SenderParticipantIdentifier;

    // Get the local participant ID
    var localID = conversation.LocalParticipantIdentifier;

    // Get the remote participant IDs
    var remoteIDs = conversation.RemoteParticipantIdentifiers;
}
```

## <a name="supported-platforms"></a>支持的平台

消息应用扩展生成的交互式消息将在以下 Apple 平台上传递：

- watchOS 3
- macOS Sierra
- iOS 10

在三个平台中，只有 iOS 10 允许用户生成交互式消息。 在 macOS Sierra 上，如果用户单击交互式消息冒泡，则附加到的 URL `MSMessage` 将在 Safari 中打开，并且应在此处显示消息的表示形式。

在 watchOS 上，消息应用可以将交互式消息移交给用户可以在其中撰写答复的附加 iOS 设备。

如果在较旧的 Apple 平台上收到交互消息，则新消息 API 支持回退：

- watchOS 2 +
- OS X 10.11 +
- iOS 9 +

它们将以回退格式作为两个单独的消息提供：

- 其中一个将是模板布局提供的映像。
- 另一个将是中提供的 URL `MSMessage` 。

## <a name="summary"></a>总结

本文介绍了一些高级技术，适用于在与 **Messages** 应用集成的 Xamarin iOS 解决方案中使用消息应用扩展，并向用户提供新功能。

## <a name="related-links"></a>相关链接

- [冰淇淋生成器 (示例) ](/samples/xamarin/ios-samples/ios10-icecreambuilder)
- [消息参考](https://developer.apple.com/reference/messages)
- [应用扩展编程指南](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)