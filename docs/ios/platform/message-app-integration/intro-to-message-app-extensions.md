---
title: Xamarin 中的消息应用扩展基础知识
description: 本文介绍如何在与 Messages 应用集成的 Xamarin iOS 解决方案中包含消息应用扩展，并向用户提供新功能。
ms.prod: xamarin
ms.assetid: 0CFB494C-376C-449D-B714-9E82644F9DA3
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/02/2017
ms.openlocfilehash: 0a2ca9fea8135e0188829b8651e9ec15303b933d
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436511"
---
# <a name="message-app-extension-basics-in-xamarinios"></a>Xamarin 中的消息应用扩展基础知识

_本文介绍如何在与 Messages 应用集成的 Xamarin iOS 解决方案中包含消息应用扩展，并向用户提供新功能。_

新的 iOS 10，消息应用扩展与 **Messages** 应用集成并向用户提供新功能。 此扩展可以发送文本、不干胶标签、媒体文件和交互式消息。

## <a name="about-message-app-extensions"></a>关于消息应用扩展

如上所述，消息应用扩展与 **Messages** 应用集成，并向用户提供新功能。 此扩展可以发送文本、不干胶标签、媒体文件和交互式消息。 提供两种类型的消息应用扩展：

- **不干胶标签** -包含用户可添加到消息中的不干胶标签集合。 无需编写任何代码即可创建不干胶标签。
- **IMessage 应用** -可在邮件应用中提供自定义用户界面，用于选择不干胶标签，输入文本，包括 (具有可选类型转换的媒体文件) 以及创建、编辑和发送交互消息。

消息应用扩展提供了三种主要内容类型：

- **交互式消息** -应用生成的一种自定义消息内容，当用户点击消息时，将在前台启动应用。
- **不干胶标签** -由应用生成的映像，可包含在用户之间发送的消息中。
- **其他受支持的内容** -该应用可以提供内容（如照片、视频、文本或指向邮件应用始终支持的任何其他内容类型的链接）。

新的 iOS 10，消息应用现在包含其自己的内置应用商店。 任何包含消息应用扩展的应用都将在此存储中显示和升级。 "新邮件" 应用抽屉将显示已从 "邮件" 应用商店下载的任何应用，以提供对用户的快速访问。

此外，iOS 10 中的新增功能，Apple 添加了内嵌应用归属，使用户能够轻松发现应用。 例如，如果一个用户从第2个用户未安装的应用发送内容 (例如) 的不干胶标签，则发送应用的名称将列在消息历史记录中的内容下。 如果用户点击应用程序的名称，将打开 "消息应用商店"，并在存储中选择应用。

消息应用扩展与开发人员熟悉创建的现有 iOS 应用程序类似，他们将有权访问标准 iOS 应用程序的所有标准框架和功能。 例如：

- 他们有权访问应用内购买。
- 他们有权访问 Apple Pay。
- 它们有权访问设备硬件，如相机。

仅在 iOS 10 上支持消息应用扩展，但是，可以在 watchOS 和 macOS 设备上查看这些扩展发送的内容。 添加到 watchOS 3 的新 _最近页面_ 将显示已从手机发送的最近不干胶标签，包括来自邮件应用扩展的标签，并允许用户从手表发送这些不干胶标签。

## <a name="about-the-messages-framework"></a>关于消息框架

新的 iOS 10，消息框架在用户的 iOS 设备上的消息应用扩展和消息应用之间提供接口。 当用户从消息应用内启动应用时，此框架允许发现应用并提供布局其 UI 所需的数据和上下文。

在应用程序启动后，用户将与之进行交互，以通过消息创建要共享的新内容。 然后，应用程序使用 Messages framework 将新创建的内容传输到 Messages 应用程序进行处理。

消息框架和消息应用扩展是基于预先存在的 iOS 应用扩展技术构建的。 有关应用扩展的详细信息，请参阅 Apple 的 [应用扩展编程指南](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)。

与 Apple 在整个系统中提供的其他扩展点不同，开发人员无需为其消息应用扩展提供主机应用，因为消息应用本身充当容器。 但是，开发人员可以选择在新的或现有的 iOS 应用中包含消息应用扩展，并将其与捆绑包一起传送。

如果 iOS 应用程序的捆绑包中包含了消息应用扩展，则应用图标将同时显示在设备的主屏幕和消息应用抽屉中的消息应用中。 如果它未包含在应用捆绑包中，则消息应用扩展将仅显示在消息应用银箱中。

即使 "消息应用扩展" 未包含在主机应用捆绑包中，开发人员也需要在消息应用扩展的捆绑包中提供一个应用图标，因为这是将在系统的其他部件中显示的图标，如 "消息应用" 抽屉或 "设置"。

## <a name="about-stickers"></a>关于不干胶标签

Apple 设计的不干胶标签是一种新的 iMessage 用户进行通信的方式，即允许将不干胶标签以内联方式发送到任何其他消息内容，或者将标签附加到会话内的上一条消息冒泡。

什么是不干胶标签？

- 它们是消息应用扩展提供的映像。
- 它们可以是动画或静态图像。
- 它们提供了一种在应用内部共享图像内容的新方法。

可以通过两种方式创建不干胶标签：

1. 可以从 Xcode 内部创建不干胶标签包，而无需包含任何代码。 所有所需的都是不干胶标签和应用图标的资产。
2. 通过创建标准的消息应用扩展插件，该扩展通过消息框架提供代码中的不干胶标签。

### <a name="creating-sticker-packs"></a>创建不干胶标签

标签包是从 Xcode 内的特殊模板创建的，只需提供一组可用作不干胶标签的静态图像资产。 如上所述，它们不需要任何代码，开发人员只需将图像文件拖入标签资产目录内的不干胶标签文件夹中。

对于要包含在不干胶标签中的图像，必须满足以下要求：

- 图像必须为 PNG、APNG、GIF 或 JPEG 格式。 Apple 建议在提供不干胶标签资产时仅使用 PNG 和 APNG 格式。
- 动画不干胶标签只支持 APNG 和 GIF 格式。
- 不干胶标签图像应提供透明背景，因为用户可以将其放置在会话中的消息气泡上。
- 单个图像文件必须小于500kb。
- 图像不能小于100x100 点，或大于 206 x 206 磅。

> [!IMPORTANT]
> 应始终在 `@3x` 300 x 300 到 618 x 618 像素范围内的分辨率提供不干胶标签图像。 系统将 `@2x` `@1x` 在运行时根据需要自动生成和版本。

Apple 建议根据各种不同的彩色背景 (（如白色、黑色、红色、黄色和多彩色) 和照片上方）测试不干胶标签图像资产，以确保它们在所有可能的情况下都能发挥最好的效果。

不干胶标签可以按以下三种可用大小之一提供不干胶标签：

- **小型** -100 x 100 点。
- **中** -136 x 136 磅。 这是默认大小。
- **大** -206 x 206 点。

使用 Xcode 的 "属性" 检查器设置整个不干胶包的大小，并且仅提供与所请求的大小匹配的图像资产，以便在 "邮件" 应用内的不干胶标签浏览器中获得最佳结果。

有关详细信息，请参阅我们的 [冰淇淋 Builder](/samples/xamarin/ios-samples/ios10-icecreambuilder) 应用和 Apple [消息参考](https://developer.apple.com/reference/messages)。

## <a name="creating-a-custom-sticker-experience"></a>创建自定义不干胶标签体验

如果应用需要比标签包提供的更多控制或灵活性，则它可以包含消息应用扩展，并通过 Messages 框架提供不干胶标签以获取自定义不干胶标签。

创建自定义不干胶标签体验有哪些好处？

1. 允许应用自定义如何向应用程序的用户显示不干胶标签。 例如，若要以标准网格布局或不同的彩色背景格式呈现不干胶标签。
2. 允许通过代码动态创建不干胶标签，而不是将其包含为静态图像资产。
3. 允许从开发人员的 web 服务器动态下载不干胶标签图像资产，而无需将新版本发布到 App Store。
4. 允许访问设备的相机，以即时创建不干胶标签。
5. 允许应用内购买，使用户可以从应用内部购买更多不干胶标签。

若要创建自定义不干胶标签，请执行以下操作：

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 启动 Visual Studio for Mac。
2. 打开解决方案以将消息应用扩展添加到。
3. 选择 " **iOS**  >  **扩展**  >  **iMessage 扩展**"，然后单击 "**下一步**" 按钮：

    [![选择 iMessage 扩展](intro-to-message-app-extensions-images/message01.png)](intro-to-message-app-extensions-images/message01.png#lightbox)
4. 输入 **扩展名称** 并单击 " **下一步** " 按钮：

    [![输入扩展名称](intro-to-message-app-extensions-images/message02.png)](intro-to-message-app-extensions-images/message02.png#lightbox)
5. 单击 " **创建** " 按钮以生成扩展：

    [![单击 "创建" 按钮](intro-to-message-app-extensions-images/message03.png)](intro-to-message-app-extensions-images/message03.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 启动 Visual Studio。
2. 打开解决方案以添加消息应用扩展。
3. 选择 **Ios 扩展 > IMessage Extension (ios) ** ，并单击 " **下一步** " 按钮：

    [![选择 iMessage Extension (iOS) ](intro-to-message-app-extensions-images/message01.w157-sml.png)](intro-to-message-app-extensions-images/message01.w157.png#lightbox)

4. 输入 **名称** ，然后单击 **"确定"** 按钮

-----

默认情况下，该 `MessagesViewController.cs` 文件将添加到解决方案中。 这是扩展插件的主要入口点，它继承自 `MSMessageAppViewController` 类。

消息框架提供用于向用户显示可用不干胶标签的类：

- `MSStickerBrowserViewController` -控制将显示不干胶标签的视图。 它还符合 `IMSStickerBrowserViewDataSource` 接口以返回给定浏览器索引的不干胶标签和不干胶标签。
- `MSStickerBrowserView` -可用不干胶标签显示在此视图中。
- `MSStickerSize` -决定浏览器视图中显示的不干胶标签网格的单个单元大小。

### <a name="creating-a-custom-sticker-browser"></a>创建自定义标签浏览器

开发人员可以通过 `MSMessageAppBrowserViewController` 在消息应用扩展中提供自定义的不干胶浏览器 () ，进一步自定义用户的不干胶标签体验。 自定义标签浏览器会在选择要包含在消息流中的不干胶标签时更改向用户显示不干胶标签的方式。

执行以下操作：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在**Solution Pad**中，右键单击扩展的项目名称，然后选择 "**添加**  >  **新文件 ...**  >  "**iOS |Apple Watch**  >  **接口控制器**。
2. 输入 `StickerBrowserViewController` 作为 **名称** ，然后单击 " **新建** " 按钮：

    [![为名称输入 StickerBrowserViewController](intro-to-message-app-extensions-images/browser01.png)](intro-to-message-app-extensions-images/browser01.png#lightbox)
3. 打开 `StickerBrowserViewController.cs` 文件进行编辑。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 在**解决方案资源管理器**中，右键单击扩展的项目名称，然后选择 "**添加**  >  **新文件 ...**  >  "**iOS |Apple Watch**  >  **接口控制器**。
2. 输入 `StickerBrowserViewController` 作为 **名称** ，然后单击 " **新建** " 按钮：

    [![为名称输入 StickerBrowserViewController](intro-to-message-app-extensions-images/browser01.w157-sml.png)](intro-to-message-app-extensions-images/browser01.w157.png#lightbox)
3. 打开 `StickerBrowserViewController.cs` 文件进行编辑。

-----

如下所示 `StickerBrowserViewController.cs` ：

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Messages;
using Foundation;

namespace MonkeyStickers
{
    public partial class StickerBrowserViewController : MSStickerBrowserViewController
    {
        #region Computed Properties
        public List<MSSticker> Stickers { get; set; } = new List<MSSticker> ();
        #endregion

        #region Constructors
        public StickerBrowserViewController (MSStickerSize stickerSize) : base (stickerSize)
        {
        }
        #endregion

        #region Private Methods
        private void CreateSticker (string assetName, string localizedDescription)
        {

            // Get path to asset
            var path = NSBundle.MainBundle.PathForResource (assetName, "png");
            if (path == null) {
                Console.WriteLine ("Couldn't create sticker {0}.", assetName);
                return;
            }

            // Build new sticker
            var stickerURL = new NSUrl (path);
            NSError error = null;
            var sticker = new MSSticker (stickerURL, localizedDescription, out error);
            if (error == null) {
                // Add to collection
                Stickers.Add (sticker);
            } else {
                // Report error
                Console.WriteLine ("Error, couldn't create sticker {0}: {1}", assetName, error);
            }
        }

        private void LoadStickers ()
        {

            // Load sticker assets from disk
            CreateSticker ("canada", "Canada Sticker");
            CreateSticker ("clouds", "Clouds Sticker");
            ...
            CreateSticker ("tree", "Tree Sticker");
        }
        #endregion

        #region Public Methods
        public void ChangeBackgroundColor (UIColor color)
        {
            StickerBrowserView.BackgroundColor = color;

        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Initialize
            LoadStickers ();
        }

        public override nint GetNumberOfStickers (MSStickerBrowserView stickerBrowserView)
        {
            return Stickers.Count;
        }

        public override MSSticker GetSticker (MSStickerBrowserView stickerBrowserView, nint index)
        {
            return Stickers[(int)index];
        }
        #endregion
    }
}
```

请详细了解上面的代码。 它为扩展提供的不干胶标签创建存储：

```csharp
public List<MSSticker> Stickers { get; set; } = new List<MSSticker> ();
```

和重写类的两个方法 `MSStickerBrowserViewController` ，以便为浏览器提供此数据存储区中的数据：

```csharp
public override nint GetNumberOfStickers (MSStickerBrowserView stickerBrowserView)
{
    return Stickers.Count;
}

public override MSSticker GetSticker (MSStickerBrowserView stickerBrowserView, nint index)
{
    return Stickers[(int)index];
}
```

`CreateSticker`方法从扩展的捆绑包获取图像资产的路径，并使用它从此资产中创建的新实例，并 `MSSticker` 将其添加到集合中：

```csharp
private void CreateSticker (string assetName, string localizedDescription)
{

    // Get path to asset
    var path = NSBundle.MainBundle.PathForResource (assetName, "png");
    if (path == null) {
        Console.WriteLine ("Couldn't create sticker {0}.", assetName);
        return;
    }

    // Build new sticker
    var stickerURL = new NSUrl (path);
    NSError error = null;
    var sticker = new MSSticker (stickerURL, localizedDescription, out error);
    if (error == null) {
        // Add to collection
        Stickers.Add (sticker);
    } else {
        // Report error
        Console.WriteLine ("Error, couldn't create sticker {0}: {1}", assetName, error);
    }
}
```

`LoadSticker`从中调用方法 `ViewDidLoad` ，以通过包含在应用捆绑包中的命名图像资产 (来创建标签) 并将其添加到不干胶标签集合。

若要实现自定义的不干胶标签，请编辑 `MessagesViewController.cs` 文件，使其类似于以下内容：

```csharp
using System;
using UIKit;
using Messages;

namespace MonkeyStickers
{
    public partial class MessagesViewController : MSMessagesAppViewController
    {
        #region Computed Properties
        public StickerBrowserViewController BrowserViewController { get; set;}
        #endregion

        #region Constructors
        protected ViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Create new browser and configure it
            BrowserViewController = new StickerBrowserViewController (MSStickerSize.Regular);
            BrowserViewController.View.Frame = View.Frame;
            BrowserViewController.ChangeBackgroundColor (UIColor.Gray);

            // Add to view
            AddChildViewController (BrowserViewController);
            BrowserViewController.DidMoveToParentViewController (this);
            View.AddSubview (BrowserViewController.View);
        }
        #endregion
    }
}
```

查看此代码的详细信息，为自定义浏览器创建存储：

```csharp
public StickerBrowserViewController BrowserViewController { get; set;}
```

在方法中 `ViewDidLoad` ，它将实例化并配置一个新浏览器：

```csharp
// Create new browser and configure it
BrowserViewController = new StickerBrowserViewController (MSStickerSize.Regular);
BrowserViewController.View.Frame = View.Frame;
BrowserViewController.ChangeBackgroundColor (UIColor.Gray);
```

然后，将浏览器添加到该视图以显示它：

```csharp
// Add to view
AddChildViewController (BrowserViewController);
BrowserViewController.DidMoveToParentViewController (this);
View.AddSubview (BrowserViewController.View);
```

### <a name="further-sticker-customization"></a>进一步的标签自定义

可以通过在消息应用扩展中包括两个类，进一步进行不干胶标签自定义：

- `MSStickerView`
- `MSSticker`

使用上述方法，扩展可支持不依赖于标准不干胶标签浏览器方法的不干胶标签选择。 此外，可以在两种不同的视图模式间切换不干胶标签：

- **Compact** -这是默认模式，其中标签视图占据消息视图的底部25%。
- 已**展开**-标签视图将填充整个消息视图。

此标签视图可以通过编程方式或由用户手动切换。

请看下面的示例，该示例在两个不同的视图模式之间处理切换。 每个状态都需要两个不同的视图控制器。 `StickerBrowserViewController`处理**精简**视图，如下所示：

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Messages;
using Foundation;

namespace MessageExtension
{
    public partial class StickerBrowserViewController : MSStickerBrowserViewController
    {
        #region Computed Properties
        public MessagesViewController MessagesAppViewController { get; set; }
        public List<MSSticker> Stickers { get; set; } = new List<MSSticker> ();
        #endregion

        #region Constructors
        public StickerBrowserViewController (MessagesViewController messagesAppViewController, MSStickerSize stickerSize) : base (stickerSize)
        {
            // Initialize
            this.MessagesAppViewController = messagesAppViewController;
        }
        #endregion

        #region Private Methods
        private void CreateSticker (string assetName, string localizedDescription)
        {

            // Get path to asset
            var path = NSBundle.MainBundle.PathForResource (assetName, "png");
            if (path == null) {
                Console.WriteLine ("Couldn't create sticker {0}.", assetName);
                return;
            }

            // Build new sticker
            var stickerURL = new NSUrl (path);
            NSError error = null;
            var sticker = new MSSticker (stickerURL, localizedDescription, out error);
            if (error == null) {
                // Add to collection
                Stickers.Add (sticker);
            } else {
                // Report error
                Console.WriteLine ("Error, couldn't create sticker {0}: {1}", assetName, error);
            }
        }

        private void LoadStickers ()
        {

            // Load sticker assets from disk
            CreateSticker ("add", "Add New Sticker");
            CreateSticker ("canada", "Canada Sticker");
            CreateSticker ("clouds", "Clouds Sticker");
            CreateSticker ("tree", "Tree Sticker");
        }
        #endregion

        #region Public Methods
        public void ChangeBackgroundColor (UIColor color)
        {
            StickerBrowserView.BackgroundColor = color;

        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Initialize
            LoadStickers ();

        }

        public override nint GetNumberOfStickers (MSStickerBrowserView stickerBrowserView)
        {
            return Stickers.Count;
        }

        public override MSSticker GetSticker (MSStickerBrowserView stickerBrowserView, nint index)
        {
            // Wanting to add a new sticker?
            if (index == 0) {
                // Yes, ask controller to present add sticker interface
                MessagesAppViewController.AddNewSticker ();
                return null;
            } else {
                // No, return existing sticker
                return Stickers [(int)index];
            }
        }
        #endregion
    }
}
```

`AddStickerViewController`将处理**展开**的标签视图，如下所示：

```csharp
using System;
using Foundation;
using UIKit;
using Messages;

namespace MessageExtension
{
    public class AddStickerViewController : UIViewController
    {
        #region Computed Properties
        public MessagesViewController MessagesAppViewController { get; set;}
        public MSSticker NewSticker { get; set;}
        #endregion

        #region Constructors
        public AddStickerViewController (MessagesViewController messagesAppViewController)
        {
            // Initialize
            this.MessagesAppViewController = messagesAppViewController;
        }
        #endregion

        #region Override Method
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Build interface to create new sticker
            var cancelButton = new UIButton (UIButtonType.RoundedRect);
            cancelButton.TouchDown += (sender, e) => {
                // Cancel add new sticker
                MessagesAppViewController.CancelAddNewSticker ();
            };
            View.AddSubview (cancelButton);

            var doneButton = new UIButton (UIButtonType.RoundedRect);
            doneButton.TouchDown += (sender, e) => {
                // Add new sticker to collection
                MessagesAppViewController.AddStickerToCollection (NewSticker);
            };
            View.AddSubview (doneButton);

            ...
        }
        #endregion
    }
}
```

`MessageViewController`实现这些视图控制器以驱动请求的状态：

```csharp
using System;
using UIKit;
using Messages;

namespace MessageExtension
{
    public partial class MessagesViewController : MSMessagesAppViewController
    {
        #region Computed Properties
        public bool IsAddingSticker { get; set;}
        public StickerBrowserViewController BrowserViewController { get; set; }
        public AddStickerViewController AddStickerController { get; set;}
        #endregion

        #region Constructors
        protected MessagesViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Public Methods
        public void PresentStickerBrowser ()
        {
            // Is the Add sticker view being displayed?
            if (IsAddingSticker) {
                // Yes, remove it from view
                AddStickerController.RemoveFromParentViewController ();
                AddStickerController.View.RemoveFromSuperview ();
            }

            // Add to view
            AddChildViewController (BrowserViewController);
            BrowserViewController.DidMoveToParentViewController (this);
            View.AddSubview (BrowserViewController.View);

            // Save mode
            IsAddingSticker = false;
        }

        public void PresentAddSticker ()
        {
            // Is the sticker browser being displayed?
            if (!IsAddingSticker) {
                // Yes, remove it from view
                BrowserViewController.RemoveFromParentViewController ();
                BrowserViewController.View.RemoveFromSuperview ();
            }

            // Add to view
            AddChildViewController (AddStickerController);
            AddStickerController.DidMoveToParentViewController (this);
            View.AddSubview (AddStickerController.View);

            // Save mode
            IsAddingSticker = true;
        }

        public void AddNewSticker ()
        {
            // Switch to expanded view mode
            Request (MSMessagesAppPresentationStyle.Expanded);
        }

        public void CancelAddNewSticker ()
        {
            // Switch to compact view mode
            Request (MSMessagesAppPresentationStyle.Compact);
        }

        public void AddStickerToCollection (MSSticker sticker)
        {
            // Add sticker to collection
            BrowserViewController.Stickers.Add (sticker);

            // Switch to compact view mode
            Request (MSMessagesAppPresentationStyle.Compact);
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Create new browser and configure it
            BrowserViewController = new StickerBrowserViewController (this, MSStickerSize.Regular);
            BrowserViewController.View.Frame = View.Frame;
            BrowserViewController.ChangeBackgroundColor (UIColor.Gray);

            // Create new Add controller and configure it as well
            AddStickerController = new AddStickerViewController (this);
            AddStickerController.View.Frame = View.Frame;

            // Initially present the sticker browser
            PresentStickerBrowser ();
        }

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
        #endregion
    }
}
```

当用户请求向其可用集合添加新不干胶标签时，会将一个新的 `AddStickerViewController` 显示为可见控制器，而不干胶标签将进入 **展开** 的视图：

```csharp
// Switch to expanded view mode
Request (MSMessagesAppPresentationStyle.Expanded);
```

如果用户选择要添加的标签，则会将其添加到可用集合，并请求 **精简** 视图：

```csharp
public void AddStickerToCollection (MSSticker sticker)
{
    // Add sticker to collection
    BrowserViewController.Stickers.Add (sticker);

    // Switch to compact view mode
    Request (MSMessagesAppPresentationStyle.Compact);
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

## <a name="summary"></a>总结

本文涵盖的 Xamarin iOS 解决方案中包含消息应用扩展，该扩展与 **Messages** 应用集成并向用户提供新功能。 它涉及到使用此扩展发送文本、不干胶标签、媒体文件和交互式消息。

## <a name="related-links"></a>相关链接

- [冰淇淋生成器 (示例) ](/samples/xamarin/ios-samples/ios10-icecreambuilder)
- [消息参考](https://developer.apple.com/reference/messages)
- [应用扩展编程指南](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)