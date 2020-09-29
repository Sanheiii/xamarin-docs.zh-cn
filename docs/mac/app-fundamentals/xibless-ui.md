---
title: xib-在 Xamarin 中的用户界面设计更少
description: '本文介绍如何直接从 c # 代码创建 Xamarin 应用程序的用户界面，而无需使用 storyboard 文件、xib 文件或 Interface Builder。'
ms.prod: xamarin
ms.assetid: 02310F58-DCF1-4589-9F4A-065DF64FC0E1
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 47025f48b1f3455f70a077f2ca55da2cdeeba761
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430885"
---
# <a name="storyboardxib-less-user-interface-design-in-xamarinmac"></a>xib-在 Xamarin 中的用户界面设计更少

_本文介绍如何直接从 c # 代码创建 Xamarin 应用程序的用户界面，而无需使用 storyboard 文件、xib 文件或 Interface Builder。_

## <a name="overview"></a>概述

在 Xamarin Mac 应用程序中使用 c # 和 .NET 时，可以 *访问在* *Xcode 和* 中工作的开发人员所使用的相同用户界面元素和工具。 通常，在创建 Xamarin Mac 应用程序时，将使用 Xcode 的 Interface Builder，使用 xib 文件创建和维护应用程序的用户界面。

你还可以选择直接在 c # 代码中创建部分或全部 Xamarin 应用程序的 UI。 本文介绍如何在 c # 代码中创建用户界面和 UI 元素。

[![Visual Studio for Mac 代码编辑器](xibless-ui-images/intro01.png "Visual Studio for Mac 代码编辑器")](xibless-ui-images/intro01-large.png#lightbox)

<a name="Switching_a_Window_to_use_Code"></a>

## <a name="switching-a-window-to-use-code"></a>切换窗口以使用代码

创建新的 Xamarin Cocoa 应用程序时，默认情况下会获得一个标准空白窗口。 此窗口是在**mainwindow.xaml** (中定义的，或者传统上的 Xib) 文件自动包含在**项目中。** 这还包括一个 **ViewController.cs** 文件，该文件可管理应用程序的主视图 (或再次使用 **MainWindow.cs** 和 **MainWindowController.cs** 文件) 。

若要切换到应用程序的 Xibless 窗口，请执行以下操作：

1. 打开要停止使用的应用程序 `.storyboard` 或 xib 文件以定义 Visual Studio for Mac 中的用户界面。
2. 在**Solution Pad**中，右键单击**mainwindow.xaml**或 xib **Main.storyboard**文件，然后选择 "**删除**"：

    ![删除主情节提要或窗口](xibless-ui-images/switch01.png "删除主情节提要或窗口")
3. 从 " **删除" 对话框**中，单击 " **删除** " 按钮，从项目中完全删除 xib 或。

    ![确认删除](xibless-ui-images/switch02.png "确认删除")

现在，我们需要修改 **MainWindow.cs** 文件以定义窗口的布局，并修改 **ViewController.cs** 或 **MainWindowController.cs** 文件，以创建类的实例， `MainWindow` 因为我们不再使用 storyboard 或 xib 文件。

将情节提要用于其用户界面的新式 Xamarin 应用程序可能不会自动包括 **MainWindow.cs**、 **ViewController.cs** 或 **MainWindowController.cs** 文件。 根据需要，只需向项目添加一个新的空 c # 类 ("**添加**  >  **新文件 ...**  >  "**常规**  > **空类**) ，并将其命名为与缺少的文件相同。

### <a name="defining-the-window-in-code"></a>在代码中定义窗口

接下来，编辑 **MainWindow.cs** 文件并使其类似于以下内容：

```csharp
using System;
using Foundation;
using AppKit;
using CoreGraphics;

namespace MacXibless
{
    public partial class MainWindow : NSWindow
    {
        #region Private Variables
        private int NumberOfTimesClicked = 0;
        #endregion

        #region Computed Properties
        public NSButton ClickMeButton { get; set;}
        public NSTextField ClickMeLabel { get ; set;}
        #endregion

        #region Constructors
        public MainWindow (IntPtr handle) : base (handle)
        {
        }

        [Export ("initWithCoder:")]
        public MainWindow (NSCoder coder) : base (coder)
        {
        }

        public MainWindow(CGRect contentRect, NSWindowStyle aStyle, NSBackingStore bufferingType, bool deferCreation): base (contentRect, aStyle,bufferingType,deferCreation) {
            // Define the user interface of the window here
            Title = "Window From Code";

            // Create the content view for the window and make it fill the window
            ContentView = new NSView (Frame);

            // Add UI elements to window
            ClickMeButton = new NSButton (new CGRect (10, Frame.Height-70, 100, 30)){
                AutoresizingMask = NSViewResizingMask.MinYMargin
            };
            ContentView.AddSubview (ClickMeButton);

            ClickMeLabel = new NSTextField (new CGRect (120, Frame.Height - 65, Frame.Width - 130, 20)) {
                BackgroundColor = NSColor.Clear,
                TextColor = NSColor.Black,
                Editable = false,
                Bezeled = false,
                AutoresizingMask = NSViewResizingMask.WidthSizable | NSViewResizingMask.MinYMargin,
                StringValue = "Button has not been clicked yet."
            };
            ContentView.AddSubview (ClickMeLabel);
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Wireup events
            ClickMeButton.Activated += (sender, e) => {
                // Update count
                ClickMeLabel.StringValue = (++NumberOfTimesClicked == 1) ? "Button clicked one time." : string.Format("Button clicked {0} times.",NumberOfTimesClicked);
            };
        }
        #endregion

    }
}
```

我们来讨论几个关键元素。

首先，我们添加了几个 _计算属性_ ，这些属性的行为类似于插座 (就像在 xib 文件中创建了该窗口) ：

```csharp
public NSButton ClickMeButton { get; set;}
public NSTextField ClickMeLabel { get ; set;}
```

这样，我们就可以访问要在窗口中显示的 UI 元素。 由于此窗口不是从 xib 文件中放大的，因此我们需要通过一种方法将其实例化 (如稍后将在) 类中所示 `MainWindowController` 。 这就是这个新的构造函数方法的作用：

```csharp
public MainWindow(CGRect contentRect, NSWindowStyle aStyle, NSBackingStore bufferingType, bool deferCreation): base (contentRect, aStyle,bufferingType,deferCreation) {
    ...
}
```

我们将在这里设计窗口布局，并放置创建所需用户界面所需的任何 UI 元素。 在可以将任何 UI 元素添加到窗口之前，它需要一个 _内容视图_ 来包含元素：

```csharp
ContentView = new NSView (Frame);
```

这将创建一个将填充窗口的内容视图。 现在我们将第一个 UI 元素添加 `NSButton` 到窗口：

```csharp
ClickMeButton = new NSButton (new CGRect (10, Frame.Height-70, 100, 30)){
    AutoresizingMask = NSViewResizingMask.MinYMargin
};
ContentView.AddSubview (ClickMeButton);
```

这里要注意的第一点是，与 iOS 不同，macOS 使用数学表示法来定义其窗口坐标系统。 因此，原点位于窗口的左下角，值在窗口的右上角向右和向右边缘增加。 创建新的时 `NSButton` ，我们将考虑这一点，因为我们在屏幕上定义其位置和大小。

`AutoresizingMask = NSViewResizingMask.MinYMargin`属性指示按钮在垂直调整窗口大小时，将其保持在窗口顶部的同一位置。 同样，这是必需的，因为 (0，0) 在窗口的左下角。

最后， `ContentView.AddSubview (ClickMeButton)` 方法将添加 `NSButton` 到内容视图中，以便在运行应用程序和显示窗口时，它将显示在屏幕上。

接下来，会将一个标签添加到窗口，该窗口将显示已单击的次数 `NSButton` ：

```csharp
ClickMeLabel = new NSTextField (new CGRect (120, Frame.Height - 65, Frame.Width - 130, 20)) {
    BackgroundColor = NSColor.Clear,
    TextColor = NSColor.Black,
    Editable = false,
    Bezeled = false,
    AutoresizingMask = NSViewResizingMask.WidthSizable | NSViewResizingMask.MinYMargin,
    StringValue = "Button has not been clicked yet."
};
ContentView.AddSubview (ClickMeLabel);
```

由于 macOS 没有特定的 _标签_ UI 元素，因此我们添加了一个特殊的样式化、不可编辑的 `NSTextField` 来充当标签。 就像之前的按钮一样，大小和位置会考虑 (0，0) 在窗口的左下角。 `AutoresizingMask = NSViewResizingMask.WidthSizable | NSViewResizingMask.MinYMargin`属性使用**or**运算符组合两个 `NSViewResizingMask` 功能。 这会使在垂直调整窗口大小时，标签将保留在窗口顶部的同一位置，并在水平调整窗口大小时缩小并放大。

同样， `ContentView.AddSubview (ClickMeLabel)` 方法会将添加 `NSTextField` 到内容视图中，以便在应用程序运行和打开窗口时，它将显示在屏幕上。

### <a name="adjusting-the-window-controller"></a>调整窗口控制器

由于的设计 `MainWindow` 不再从 storyboard 或 xib 文件加载，因此我们需要对窗口控制器进行一些调整。 编辑 **MainWindowController.cs** 文件并使其类似于以下内容：

```csharp
using System;

using Foundation;
using AppKit;
using CoreGraphics;

namespace MacXibless
{
    public partial class MainWindowController : NSWindowController
    {
        public MainWindowController (IntPtr handle) : base (handle)
        {
        }

        [Export ("initWithCoder:")]
        public MainWindowController (NSCoder coder) : base (coder)
        {
        }

        public MainWindowController () : base ("MainWindow")
        {
            // Construct the window from code here
            CGRect contentRect = new CGRect (0, 0, 1000, 500);
            base.Window = new MainWindow(contentRect, (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable), NSBackingStore.Buffered, false);

            // Simulate Awaking from Nib
            Window.AwakeFromNib ();
        }

        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();
        }

        public new MainWindow Window {
            get { return (MainWindow)base.Window; }
        }
    }
}

```

让我们讨论一下此修改的关键要素。

首先，我们定义类的新实例 `MainWindow` ，并将其分配给基窗口控制器的 `Window` 属性：

```csharp
CGRect contentRect = new CGRect (0, 0, 1000, 500);
base.Window = new MainWindow(contentRect, (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable), NSBackingStore.Buffered, false);
```

我们使用定义屏幕窗口的位置 `CGRect` 。 与窗口的坐标系统一样，屏幕会将 (0，0) 定义为左下角。 接下来，使用 **Or** 运算符来定义窗口的样式，以合并两个或更多 `NSWindowStyle` 功能：

```csharp
... (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable) ...
```

提供以下 `NSWindowStyle` 功能：

- 无**边框-窗口**不包含边框。
- **标题** -该窗口将具有标题栏。
- **Closable** -该窗口有一个 "关闭" 按钮，可以关闭。
- **Miniaturizable** -该窗口有一个 Miniaturize 按钮，可以最小化。
- 可**调整大小**-该窗口将具有调整大小按钮并可调整大小。
- **实用工具** -窗口是一个实用工具样式窗口 (面板) 。
- **DocModal** -如果窗口是一个面板，它将是文档模式而非系统模式。
- **NonactivatingPanel** -如果窗口是一个面板，则它不会成为主窗口。
- **TexturedBackground** -该窗口将具有纹理背景。
- 未**缩放-将不缩放窗口**。
- **UnifiedTitleAndToolbar** -将联接窗口的标题和工具栏区域。
- **Hud** -该窗口将显示为一个打印头显示面板。
- **FullScreenWindow** -该窗口可以进入全屏模式。
- **FullSizeContentView** -窗口的内容视图位于标题和工具栏区域后面。

最后两个属性定义窗口的 _缓冲类型_ ，并且如果窗口的绘制将延迟，则为。 有关的详细信息 `NSWindows` ，请参阅 Apple 的 [Windows 简介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1) 文档。

最后，由于窗口不是从 xib 文件中放大的，因此我们需要通过调用 windows 方法在 **MainWindowController.cs** 中模拟它 `AwakeFromNib` ：

```csharp
Window.AwakeFromNib ();
```

这将允许你对窗口进行编码，就像从 xib 或文件加载的标准窗口一样。

### <a name="displaying-the-window"></a>显示窗口

如果删除了 xib 文件并修改了 **MainWindow.cs** 和 **MainWindowController.cs** 文件，则将使用窗口，就像使用 xib 文件 Xcode 的 Interface Builder 中创建的任何正常窗口一样。

以下操作将创建该窗口及其控制器的新实例，并在屏幕上显示该窗口：

```csharp
private MainWindowController mainWindowController;
...

mainWindowController = new MainWindowController ();
mainWindowController.Window.MakeKeyAndOrderFront (this);
```

此时，如果应用程序正在运行，并且单击了几次按钮，则会显示以下内容：

![示例应用运行](xibless-ui-images/run01.png "示例应用运行")

## <a name="adding-a-code-only-window"></a>添加仅限代码的窗口

如果只想要将代码添加到现有 Xamarin Mac 应用程序，请在**Solution Pad**中右键单击该项目，然后选择 "**添加**  >  **新文件 ...**"在 "**新建文件**" 对话框**Xamarin.Mac**中，选择  >  **包含控制器的 Xamarin Cocoa 窗口**，如下所示：

![添加新的窗口控制器](xibless-ui-images/add01.png "添加新的窗口控制器")

就像以前一样，我们将从项目中删除默认的 xib 文件 (在本例中为 **SecondWindow。 xib**) 并按照上面的 [切换窗口以使用代码](#Switching_a_Window_to_use_Code) 部分中所述的步骤，将窗口的定义覆盖到代码。

## <a name="adding-a-ui-element-to-a-window-in-code"></a>在代码中将 UI 元素添加到窗口

无论是在代码中创建窗口还是从情节提要或 xib 文件加载窗口，有时都可能需要将 UI 元素从代码添加到窗口。 例如：

```csharp
var ClickMeButton = new NSButton (new CGRect (10, 10, 100, 30)){
    AutoresizingMask = NSViewResizingMask.MinYMargin
};
MyWindow.ContentView.AddSubview (ClickMeButton);
```

上面的代码将创建一个新的 `NSButton` ，并将其添加到 `MyWindow` 窗口实例中以供显示。 可以在 Xcode 中的 Interface Builder 中定义的任何 UI 元素，也可以在代码中创建，并将其显示在窗口中。

## <a name="defining-the-menu-bar-in-code"></a>在代码中定义菜单栏

由于 Xamarin 中的当前限制，不建议你在代码中创建 Xamarin 应用程序的菜单栏， `NSMenuBar` 而是继续使用**MainMenu 或 xib**文件对其进行定义**Main.storyboard** 。 也就是说，你可以在 c # 代码中添加和移除菜单和菜单项。

例如，编辑 **AppDelegate.cs** 文件并使 `DidFinishLaunching` 方法类似于以下内容：

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    mainWindowController = new MainWindowController ();
    mainWindowController.Window.MakeKeyAndOrderFront (this);

    // Create a Status Bar Menu
    NSStatusBar statusBar = NSStatusBar.SystemStatusBar;

    var item = statusBar.CreateStatusItem (NSStatusItemLength.Variable);
    item.Title = "Phrases";
    item.HighlightMode = true;
    item.Menu = new NSMenu ("Phrases");

    var address = new NSMenuItem ("Address");
    address.Activated += (sender, e) => {
        Console.WriteLine("Address Selected");
    };
    item.Menu.AddItem (address);

    var date = new NSMenuItem ("Date");
    date.Activated += (sender, e) => {
        Console.WriteLine("Date Selected");
    };
    item.Menu.AddItem (date);

    var greeting = new NSMenuItem ("Greeting");
    greeting.Activated += (sender, e) => {
        Console.WriteLine("Greetings Selected");
    };
    item.Menu.AddItem (greeting);

    var signature = new NSMenuItem ("Signature");
    signature.Activated += (sender, e) => {
        Console.WriteLine("Signature Selected");
    };
    item.Menu.AddItem (signature);
}
```

上述代码从代码创建状态栏菜单，并在启动应用程序时显示它。 有关使用菜单的详细信息，请参阅我们的 [菜单](~/mac/user-interface/menu.md) 文档。

## <a name="summary"></a>总结

本文详细介绍了如何在 c # 代码中创建 Xamarin 应用程序的用户界面，而不是将 Xcode 的 Interface Builder 用于 xib 文件。

## <a name="related-links"></a>相关链接

- [MacXibless (示例) ](/samples/xamarin/mac-samples/macxibless)
- [Windows](~/mac/user-interface/window.md)
- [菜单](~/mac/user-interface/menu.md)
- [macOS 人机界面指南](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
- [Windows 简介](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/WinPanel/Introduction.html)