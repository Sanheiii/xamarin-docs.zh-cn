---
title: xib-在 Xamarin 中的用户界面设计更少
description: 本文介绍如何直接从C#代码创建 Xamarin 应用程序的用户界面，而无需使用 storyboard 文件、xib 文件或 Interface Builder。
ms.prod: xamarin
ms.assetid: 02310F58-DCF1-4589-9F4A-065DF64FC0E1
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: b189f80e2875e1e025128fee372e732f3ef28f22
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021713"
---
# <a name="storyboardxib-less-user-interface-design-in-xamarinmac"></a>xib-在 Xamarin 中的用户界面设计更少

_本文介绍如何直接从C#代码创建 Xamarin 应用程序的用户界面，而无需使用 storyboard 文件、xib 文件或 Interface Builder。_

## <a name="overview"></a>概述

在 Xamarin Mac C#应用程序中使用和 .net 时，可以访问在*Xcode 和* *中工作*的开发人员所使用的相同用户界面元素和工具。 通常，在创建 Xamarin Mac 应用程序时，将使用 Xcode 的 Interface Builder，使用 xib 文件创建和维护应用程序的用户界面。

你还可以选择直接在代码中C#创建部分或全部 Xamarin 应用程序的 UI。 本文介绍如何在代码中C#创建用户界面和 UI 元素。

[![Visual Studio for Mac 代码编辑器](xibless-ui-images/intro01.png "Visual Studio for Mac 代码编辑器")](xibless-ui-images/intro01-large.png#lightbox)

<a name="Switching_a_Window_to_use_Code" />

## <a name="switching-a-window-to-use-code"></a>切换窗口以使用代码

创建新的 Xamarin Cocoa 应用程序时，默认情况下会获得一个标准空白窗口。 此窗口是在 Mainwindow.xaml 中定义的，它是在项目中自动包含的**主情节提要**（或传统上的**xib**）文件中定义的。 这还包括一个**ViewController.cs**文件，该文件用于管理应用的主视图（或同样传统上的**MainWindow.cs**和**MainWindowController.cs**文件）。

若要切换到应用程序的 Xibless 窗口，请执行以下操作：

1. 使用 `.storyboard` 或 xib 文件打开要停止的应用程序，以便在 Visual Studio for Mac 中定义用户界面。
2. 在**Solution Pad**中，右键单击**mainwindow.xaml**或 xib文件，然后选择 "**删除**"：

    ![删除主情节提要或窗口](xibless-ui-images/switch01.png "删除主情节提要或窗口")
3. 从 "**删除" 对话框**中，单击 "**删除**" 按钮，从项目中完全删除 xib 或。

    ![确认删除](xibless-ui-images/switch02.png "确认删除")

现在，我们需要修改**MainWindow.cs**文件以定义窗口的布局，并修改**ViewController.cs**或**MainWindowController.cs**文件，以创建 `MainWindow` 类的实例，因为我们不再使用情节提要或xib 文件。

将情节提要用于其用户界面的新式 Xamarin 应用程序可能不会自动包括**MainWindow.cs**、 **ViewController.cs**或**MainWindowController.cs**文件。 根据需要，只需向项目中C#添加一个新的空类（**添加** > **新建文件 ...**  > **常规** > **空类**），并将其命名为与缺少的文件相同。

### <a name="defining-the-window-in-code"></a>在代码中定义窗口

接下来，编辑**MainWindow.cs**文件并使其类似于以下内容：

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

首先，我们添加了几个_计算属性_，这些属性的作用类似于插座（就像是在 xib 文件中创建了该窗口一样）：

```csharp
public NSButton ClickMeButton { get; set;}
public NSTextField ClickMeLabel { get ; set;}
```

这样，我们就可以访问要在窗口中显示的 UI 元素。 由于此窗口不是从 xib 文件中放大的，因此我们需要一种方法来对其进行实例化（如稍后将在 `MainWindowController` 类中）。 这就是这个新的构造函数方法的作用：

```csharp
public MainWindow(CGRect contentRect, NSWindowStyle aStyle, NSBackingStore bufferingType, bool deferCreation): base (contentRect, aStyle,bufferingType,deferCreation) {
    ...
}
```

我们将在这里设计窗口布局，并放置创建所需用户界面所需的任何 UI 元素。 在可以将任何 UI 元素添加到窗口之前，它需要一个_内容视图_来包含元素：

```csharp
ContentView = new NSView (Frame);
```

这将创建一个将填充窗口的内容视图。 现在，我们将 `NSButton` 的第一个 UI 元素添加到窗口：

```csharp
ClickMeButton = new NSButton (new CGRect (10, Frame.Height-70, 100, 30)){
    AutoresizingMask = NSViewResizingMask.MinYMargin
};
ContentView.AddSubview (ClickMeButton);
```

这里要注意的第一点是，与 iOS 不同，macOS 使用数学表示法来定义其窗口坐标系统。 因此，原点位于窗口的左下角，值在窗口的右上角向右和向右边缘增加。 当我们创建新的 `NSButton` 时，我们将考虑这一点，因为我们在屏幕上定义其位置和大小。

当垂直调整窗口大小时，"`AutoresizingMask = NSViewResizingMask.MinYMargin`" 属性会告诉按钮，使其保持在窗口顶部的相同位置。 同样，这是必需的，因为（0，0）位于窗口的左下角。

最后，`ContentView.AddSubview (ClickMeButton)` 方法将 `NSButton` 添加到内容视图中，以便在应用程序运行时显示在屏幕上，并显示窗口。

接下来，将在窗口中添加一个标签，其中显示了 `NSButton` 的点击次数：

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

由于 macOS 没有特定的_标签_UI 元素，因此我们添加了一个特殊的样式化、不可编辑的 `NSTextField`，以用作标签。 就像之前的按钮一样，大小和位置会考虑（0，0）在窗口的左下角。 `AutoresizingMask = NSViewResizingMask.WidthSizable | NSViewResizingMask.MinYMargin` 属性使用**or**运算符将两个 `NSViewResizingMask` 功能组合在一起。 这会使在垂直调整窗口大小时，标签将保留在窗口顶部的同一位置，并在水平调整窗口大小时缩小并放大。

同样，`ContentView.AddSubview (ClickMeLabel)` 方法将 `NSTextField` 添加到内容视图中，以便在应用程序运行和打开窗口时，它将显示在屏幕上。

### <a name="adjusting-the-window-controller"></a>调整窗口控制器

由于 `MainWindow` 的设计不再从 storyboard 或 xib 文件加载，因此我们需要对窗口控制器进行一些调整。 编辑**MainWindowController.cs**文件并使其类似于以下内容：

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

首先，我们定义 `MainWindow` 类的新实例，并将其分配给基窗口控制器的 `Window` 属性：

```csharp
CGRect contentRect = new CGRect (0, 0, 1000, 500);
base.Window = new MainWindow(contentRect, (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable), NSBackingStore.Buffered, false);
```

使用 `CGRect` 定义屏幕的位置。 与窗口的坐标系统一样，屏幕将（0，0）定义为左下角。 接下来，使用**Or**运算符来定义窗口的样式，以合并两个或更多 `NSWindowStyle` 功能：

```csharp
... (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable) ...
```

以下 `NSWindowStyle` 功能可用：

- 无**边框-窗口**不包含边框。
- **标题**-该窗口将具有标题栏。
- **Closable** -该窗口有一个 "关闭" 按钮，可以关闭。
- **Miniaturizable** -该窗口有一个 Miniaturize 按钮，可以最小化。
- 可**调整大小**-该窗口将具有调整大小按钮并可调整大小。
- **实用工具**-窗口是一个实用工具样式窗口（面板）。
- **DocModal** -如果窗口是一个面板，它将是文档模式而非系统模式。
- **NonactivatingPanel** -如果窗口是一个面板，则它不会成为主窗口。
- **TexturedBackground** -该窗口将具有纹理背景。
- 未**缩放-将不缩放窗口**。
- **UnifiedTitleAndToolbar** -将联接窗口的标题和工具栏区域。
- **Hud** -该窗口将显示为一个打印头显示面板。
- **FullScreenWindow** -该窗口可以进入全屏模式。
- **FullSizeContentView** -窗口的内容视图位于标题和工具栏区域后面。

最后两个属性定义窗口的_缓冲类型_，并且如果窗口的绘制将延迟，则为。 有关 `NSWindows` 的详细信息，请参阅 Apple 的[Windows 简介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)文档。

最后，由于窗口不是从 xib 文件中放大的，因此我们需要通过调用 windows `AwakeFromNib` 方法在我们的**MainWindowController.cs**中模拟它：

```csharp
Window.AwakeFromNib ();
```

这将允许你对窗口进行编码，就像从 xib 或文件加载的标准窗口一样。

### <a name="displaying-the-window"></a>显示窗口

如果删除了 xib 文件并修改了**MainWindow.cs**和**MainWindowController.cs**文件，则将使用窗口，就像使用 xib 文件 Xcode 的 Interface Builder 中创建的任何正常窗口一样。

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

如果只想要将代码添加到现有 Xamarin Mac 应用程序，请在**Solution Pad**中右键单击该项目，然后选择 "**添加** > **新文件 ...** "在 "**新建文件**" 对话框中，选择**包含控制器的** **Xamarin**  >  Cocoa "窗口，如下所示：

![添加新的窗口控制器](xibless-ui-images/add01.png "添加新的窗口控制器")

就像以前一样，我们将从项目中删除默认的 xib 文件（在本例中为**SecondWindow**），并按照上面的[切换窗口以使用代码](#Switching_a_Window_to_use_Code)部分中所述的步骤，将窗口的定义覆盖到代码。

## <a name="adding-a-ui-element-to-a-window-in-code"></a>在代码中将 UI 元素添加到窗口

无论是在代码中创建窗口还是从情节提要或 xib 文件加载窗口，有时都可能需要将 UI 元素从代码添加到窗口。 例如:

```csharp
var ClickMeButton = new NSButton (new CGRect (10, 10, 100, 30)){
    AutoresizingMask = NSViewResizingMask.MinYMargin
};
MyWindow.ContentView.AddSubview (ClickMeButton);
```

上面的代码创建一个新 `NSButton`，并将其添加到 `MyWindow` 窗口实例中以供显示。 可以在 Xcode 中的 Interface Builder 中定义的任何 UI 元素，也可以在代码中创建，并将其显示在窗口中。

## <a name="defining-the-menu-bar-in-code"></a>在代码中定义菜单栏

由于 Xamarin 中的当前限制，不建议你在代码中创建**Xamarin. mac**应用程序的菜单栏（`NSMenuBar` –在代码中），但继续使用**xib**文件进行定义。 也就是说，您可以在代码中C#添加和删除菜单和菜单项。

例如，编辑**AppDelegate.cs**文件并使 `DidFinishLaunching` 方法如下所示：

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

上述代码从代码创建状态栏菜单，并在启动应用程序时显示它。 有关使用菜单的详细信息，请参阅我们的[菜单](~/mac/user-interface/menu.md)文档。

## <a name="summary"></a>总结

本文详细介绍了如何在代码中C#创建 Xamarin 应用程序的用户界面，而不是使用 Xcode 的 Interface Builder 和 xib 文件。

## <a name="related-links"></a>相关链接

- [MacXibless （示例）](https://docs.microsoft.com/samples/xamarin/mac-samples/macxibless)
- [Windows](~/mac/user-interface/window.md)
- [菜单](~/mac/user-interface/menu.md)
- [macOS 人机界面指南](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
- [Windows 简介](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/WinPanel/Introduction.html)
