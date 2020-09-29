---
title: 生成新式 macOS 应用
description: 本文介绍了开发人员可用于在 Xamarin 中生成新式 macOS 应用程序的几个提示、功能和技术。
ms.prod: xamarin
ms.assetid: F20EE590-246E-40EB-B309-D9D8C090C7F1
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: d2a3181360abd7ee7a5124602d0c4186b0a8ec38
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430156"
---
# <a name="building-modern-macos-apps"></a>生成新式 macOS 应用

_本文介绍了开发人员可用于在 Xamarin 中生成新式 macOS 应用程序的几个提示、功能和技术。_

<a name="Building-Modern-Looks-with-Modern-Views"></a>

## <a name="building-modern-looks-with-modern-views"></a>新式视图构建新式外观

新式外观将包括一个新式窗口和工具栏外观，如下面所示的示例应用：

[![新式 Mac 应用 UI 的示例](modern-cocoa-apps-images/content08.png)](modern-cocoa-apps-images/content08.png#lightbox)

<a name="Enabling-Full-Sized-Content-Views"></a>

### <a name="enabling-full-sized-content-views"></a>启用完全大小的内容视图

为实现此目的，开发人员需要使用 _完整大小的内容视图_，这意味着内容会在工具和标题栏区域下扩展，并将通过 macOS 自动模糊显示。

若要在代码中启用此功能，请为创建一个自定义类， `NSWindowController` 并使其类似于以下内容：

```csharp
using System;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainWindowController : NSWindowController
    {
        #region Constructor
        public MainWindowController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void WindowDidLoad ()
        {
            base.WindowDidLoad ();

            // Set window to use Full Size Content View
            Window.StyleMask = NSWindowStyle.FullSizeContentView;
        }
        #endregion
    }
}
```

还可以通过选择窗口并检查 **完整大小的内容视图**，在 Xcode 的 Interface Builder 中启用此功能：

[![编辑 Xcode 的主情节提要 Interface Builder](modern-cocoa-apps-images/content01.png)](modern-cocoa-apps-images/content01.png#lightbox)

使用完整大小的内容视图时，开发人员可能需要偏移标题和工具栏区域下的内容，以便特定内容 (如标签) 不会滑下。

为了使此问题复杂化，标题和工具栏区域可以基于用户当前正在执行的操作、用户已安装的 macOS 的版本以及运行应用的 Mac 硬件，提供动态高度。

因此，对用户界面进行布局时，只需对偏移进行硬编码即可。 开发人员需要采用动态方法。

Apple 包含类的 [键值可观察](~/mac/app-fundamentals/databinding.md#Observing_Value_Changes) `ContentLayoutRect` 属性 `NSWindow` ，可在代码中获取当前内容区域。 当内容区域发生更改时，开发人员可以使用此值手动定位所需的元素。

更好的解决方案是使用自动布局和大小类在代码或 Interface Builder 中定位 UI 元素。

类似以下示例的代码可用于在应用的视图控制器中使用自动布局和大小类定位 UI 元素：

```csharp
using System;
using AppKit;
using Foundation;

namespace MacModern
{
    public partial class ViewController : NSViewController
    {
        #region Computed Properties
        public NSLayoutConstraint topConstraint { get; set; }
        #endregion

        ...

        #region Override Methods
        public override void UpdateViewConstraints ()
        {
            // Has the constraint already been set?
            if (topConstraint == null) {
                // Get the top anchor point
                var contentLayoutGuide = ItemTitle.Window?.ContentLayoutGuide as NSLayoutGuide;
                var topAnchor = contentLayoutGuide.TopAnchor;

                // Found?
                if (topAnchor != null) {
                    // Assemble constraint and activate it
                    topConstraint = topAnchor.ConstraintEqualToAnchor (topAnchor, 20);
                    topConstraint.Active = true;
                }
            }

            base.UpdateViewConstraints ();
        }
        #endregion
    }
}
```

此代码将为顶级约束创建存储，此约束将应用于 () 的标签， `ItemTitle` 以确保它不会在标题和工具栏区域下滑动：

```csharp
public NSLayoutConstraint topConstraint { get; set; }
```

通过重写视图控制器的 `UpdateViewConstraints` 方法，开发人员可以进行测试，以查看是否已生成所需的约束并在需要时创建。

如果需要生成新的约束，则 `ContentLayoutGuide` 访问需要约束的控件的窗口的属性，并将其强制转换为 `NSLayoutGuide` ：

```csharp
var contentLayoutGuide = ItemTitle.Window?.ContentLayoutGuide as NSLayoutGuide;
```

访问的 TopAnchor 属性， `NSLayoutGuide` 如果可用，将使用它来生成具有所需偏移量的新约束，并使新约束生效：

```csharp
// Assemble constraint and activate it
topConstraint = topAnchor.ConstraintEqualToAnchor (topAnchor, 20);
topConstraint.Active = true;
```

<a name="Enabling-Streamlined-Toolbars"></a>

### <a name="enabling-streamlined-toolbars"></a>启用简化的工具栏

正常的 macOS 窗口包括运行时的标准标题栏以及窗口的上边缘。 如果该窗口还包含工具栏，它将显示在此标题栏区域下：

[![标准 Mac 工具栏](modern-cocoa-apps-images/content02.png)](modern-cocoa-apps-images/content02.png#lightbox)

使用简化的工具栏时，标题区域会消失，工具栏会向上移动到标题栏的位置，并使窗口关闭、最小化和最大化按钮：

[![简化的 Mac 工具栏](modern-cocoa-apps-images/content03.png)](modern-cocoa-apps-images/content03.png#lightbox)

通过重写的方法来启用简化的工具栏 `ViewWillAppear` `NSViewController` ，使其看起来如下所示：

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Enable streamlined Toolbars
    View.Window.TitleVisibility = NSWindowTitleVisibility.Hidden;
}
```

此效果通常用于 _Shoebox 应用程序_ (一个) 例如地图、日历、注释和系统首选项的窗口应用。

<a name="Using-Accessory-View-Controllers"></a>

### <a name="using-accessory-view-controllers"></a>使用附件视图控制器

根据应用程序的设计，开发人员可能还需要使用显示在标题/工具栏区域下方的附件视图控制器来补充标题栏区域，以根据当前所采用的活动向用户提供上下文相关控件：

[![示例附件视图控制器](modern-cocoa-apps-images/content04.png)](modern-cocoa-apps-images/content04.png#lightbox)

系统无需开发人员干预，会自动对附件视图控制器进行模糊并调整其大小。

若要添加附件视图控制器，请执行以下操作：

1. 在“解决方案资源管理器”**** 中，双击 `Main.storyboard` 文件，将其打开进行编辑。
2. 将 **自定义视图控制器** 拖动到窗口的层次结构中：

    [![添加新的自定义视图控制器](modern-cocoa-apps-images/content05.png)](modern-cocoa-apps-images/content05.png#lightbox)
3. 布局附件视图的 UI：

    [![设计新视图](modern-cocoa-apps-images/content06.png)](modern-cocoa-apps-images/content06.png#lightbox)
4. 将附件视图公开为 **插座** ，并向其 UI 公开任何其他 **操作** 或 **插座** ：

    [![添加所需的插座](modern-cocoa-apps-images/content07.png)](modern-cocoa-apps-images/content07.png#lightbox)
5. 保存更改。
6. 返回到 Visual Studio for Mac 以同步更改。

编辑 `NSWindowController` 并使其类似于以下内容：

```csharp
using System;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainWindowController : NSWindowController
    {
        #region Constructor
        public MainWindowController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void WindowDidLoad ()
        {
            base.WindowDidLoad ();

            // Create a title bar accessory view controller and attach
            // the view created in Interface Builder
            var accessoryView = new NSTitlebarAccessoryViewController ();
            accessoryView.View = AccessoryViewGoBar;

            // Set the location and attach the accessory view to the
            // titlebar to be displayed
            accessoryView.LayoutAttribute = NSLayoutAttribute.Bottom;
            Window.AddTitlebarAccessoryViewController (accessoryView);

        }
        #endregion
    }
}
```

此代码的关键点是将视图设置为在 Interface Builder 中定义的自定义视图，并作为 **插座**公开：

```csharp
accessoryView.View = AccessoryViewGoBar;
```

和 `LayoutAttribute` ，用于定义将显示附件的 _位置_ ：

```csharp
accessoryView.LayoutAttribute = NSLayoutAttribute.Bottom;
```

由于 macOS 现在已完全本地化，因此 `Left` 和 `Right` `NSLayoutAttribute` 属性已弃用，应该替换为 `Leading` 和 `Trailing` 。

<a name="Using-Tabbed-Windows"></a>

### <a name="using-tabbed-windows"></a>使用选项卡式窗口

此外，macOS 系统可能会将附件视图控制器添加到应用的窗口中。 例如，若要创建选项卡式窗口，其中有多个应用程序窗口合并为一个虚拟窗口：

[![选项卡式 Mac 窗口的示例](modern-cocoa-apps-images/content08.png)](modern-cocoa-apps-images/content08.png#lightbox)

通常，开发人员需要采取有限的操作，使用其 Xamarin 应用程序中的选项卡式窗口，系统将自动处理它们，如下所示：

- 调用方法时，Windows 将自动处于选项卡式 `OrderFront` 。
- 调用方法时，将自动 Untabbed Windows `OrderOut` 。
- 在代码中，所有的选项卡式窗口仍被视为 "可见" 状态，但任何非最前面的选项卡都将被系统使用 CoreGraphics 隐藏。
- 使用的 `TabbingIdentifier` 属性将 `NSWindow` 窗口一起组合到选项卡中。
- 如果它是 `NSDocument` 基于的应用程序，则会自动启用这些功能中的几个 (例如，添加到选项卡栏中的加号按钮) 无需任何开发人员操作。
- 基于非 `NSDocument` 基于的应用程序可以通过重写的方法，使选项卡组中的 "加" 按钮添加新文档 `GetNewWindowForTab` `NSWindowsController` 。

将所有部分组合在一起， `AppDelegate` 需要使用基于系统的选项卡式窗口的应用程序的外观如下所示：

```csharp
using AppKit;
using Foundation;

namespace MacModern
{
    [Register ("AppDelegate")]
    public class AppDelegate : NSApplicationDelegate
    {
        #region Computed Properties
        public int NewDocumentNumber { get; set; } = 0;
        #endregion

        #region Constructors
        public AppDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void DidFinishLaunching (NSNotification notification)
        {
            // Insert code here to initialize your application
        }

        public override void WillTerminate (NSNotification notification)
        {
            // Insert code here to tear down your application
        }
        #endregion

        #region Custom Actions
        [Export ("newDocument:")]
        public void NewDocument (NSObject sender)
        {
            // Get new window
            var storyboard = NSStoryboard.FromName ("Main", null);
            var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

            // Display
            controller.ShowWindow (this);
        }
        #endregion
    }
}
```

其中， `NewDocumentNumber` 属性跟踪已创建的新文档数， `NewDocument` 方法会创建一个新文档并将其显示。

如下所 `NSWindowController` 示：

```csharp
using System;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainWindowController : NSWindowController
    {
        #region Application Access
        /// <summary>
        /// A helper shortcut to the app delegate.
        /// </summary>
        /// <value>The app.</value>
        public static AppDelegate App {
            get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Constructor
        public MainWindowController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Public Methods
        public void SetDefaultDocumentTitle ()
        {
            // Is this the first document?
            if (App.NewDocumentNumber == 0) {
                // Yes, set title and increment
                Window.Title = "Untitled";
                ++App.NewDocumentNumber;
            } else {
                // No, show title and count
                Window.Title = $"Untitled {App.NewDocumentNumber++}";
            }
        }
        #endregion

        #region Override Methods
        public override void WindowDidLoad ()
        {
            base.WindowDidLoad ();

            // Prefer Tabbed Windows
            Window.TabbingMode = NSWindowTabbingMode.Preferred;
            Window.TabbingIdentifier = "Main";

            // Set default window title
            SetDefaultDocumentTitle ();

            // Set window to use Full Size Content View
            // Window.StyleMask = NSWindowStyle.FullSizeContentView;

            // Create a title bar accessory view controller and attach
            // the view created in Interface Builder
            var accessoryView = new NSTitlebarAccessoryViewController ();
            accessoryView.View = AccessoryViewGoBar;

            // Set the location and attach the accessory view to the
            // titlebar to be displayed
            accessoryView.LayoutAttribute = NSLayoutAttribute.Bottom;
            Window.AddTitlebarAccessoryViewController (accessoryView);

        }

        public override void GetNewWindowForTab (NSObject sender)
        {
            // Ask app to open a new document window
            App.NewDocument (this);
        }
        #endregion
    }
}
```

，其中 static `App` 属性提供了访问的快捷方式 `AppDelegate` 。 `SetDefaultDocumentTitle`方法基于创建的新文档数设置新的文档标题。

下面的代码告诉 macOS，应用程序首选使用选项卡，并提供允许将应用程序的窗口分组到选项卡的字符串：

```csharp
// Prefer Tabbed Windows
Window.TabbingMode = NSWindowTabbingMode.Preferred;
Window.TabbingIdentifier = "Main";
```

以下重写方法向选项卡栏添加一个加号按钮，用户在单击该按钮时将创建一个新文档：

```csharp
public override void GetNewWindowForTab (NSObject sender)
{
    // Ask app to open a new document window
    App.NewDocument (this);
}
```

<a name="Using-Core-Animation"></a>

### <a name="using-core-animation"></a>使用核心动画

核心动画是 macOS 中内置的高功率图形渲染引擎。 已对核心动画进行优化，以利用了新式 macOS 硬件中的 GPU (图形处理单元) ，而不是在 CPU 上运行图形操作，这会降低计算机的速度。

`CALayer`核心动画提供的可用于快速、流畅滚动和动画等任务。 应用的用户界面应由多个子视图和层组成，以充分利用核心动画。

`CALayer`对象提供多个属性，使开发人员能够控制屏幕上向用户显示的内容，例如：

- `Content` -可以是 `NSImage` 或 `CGImage` ，它提供层的内容。
- `BackgroundColor` -将层的背景色设置为 `CGColor`
- `BorderWidth` -设置边框宽度。
- `BorderColor` -设置边框颜色。

若要在应用的 UI 中利用核心图形，必须使用 _支持层_ 的视图，Apple 建议开发人员始终在窗口的内容视图中启用。 这样一来，所有子视图也会自动继承层后备。

此外，Apple 建议使用分层支持的视图，而不是将新的添加 `CALayer` 为子层，因为系统将自动处理几个所需的设置 (如 Retina 显示) 所需的设置。

可以通过 `WantsLayer` `NSView` 检查 " `true` **核心动画" 层**，在 "**查看效果" 检查器**下，通过将 Xcode 的 Interface Builder 设置为或内部来启用层后备：

[![视图效果检查器](modern-cocoa-apps-images/content09.png)](modern-cocoa-apps-images/content09.png#lightbox)

<a name="Redrawing-Views-with-Layers"></a>

#### <a name="redrawing-views-with-layers"></a>用层重绘视图

在 Xamarin for Mac 应用中使用支持层级视图的另一个重要步骤是将的设置 `LayerContentsRedrawPolicy` `NSView` 为 `OnSetNeedsDisplay` 中的 `NSViewController` 。 例如：

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set the content redraw policy
    View.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
}
```

如果开发人员未设置此属性，则只要视图的框架源发生更改，就会重新绘制视图，出于性能方面的原因，这是不需要的。 如果将此属性设置为 `OnSetNeedsDisplay` "开发人员"，则必须将设置为，以 `NeedsDisplay` `true` 强制内容重绘。

当视图被标记为 "已更新" 时，系统将检查 `WantsUpdateLayer` 视图的属性。 如果它返回 `true` ，则 `UpdateLayer` 调用方法，否则，将 `DrawRect` 调用视图的方法来更新视图的内容。

Apple 提供以下建议，用于在需要时更新视图内容：

- Apple 首选使用 `UpdateLater` over， `DrawRect` 因为这样可以显著提高性能。
- `layer.Contents`对类似的 UI 元素使用相同的。
- Apple 还首选开发人员使用标准视图（例如）来编写 UI `NSTextField` ，并尽可能再次使用。

若要使用 `UpdateLayer` ，请为创建一个自定义类， `NSView` 并使代码如下所示：

```csharp
using System;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainView : NSView
    {
        #region Computed Properties
        public override bool WantsLayer {
            get { return true; }
        }

        public override bool WantsUpdateLayer {
            get { return true; }
        }
        #endregion

        #region Constructor
        public MainView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void DrawRect (CoreGraphics.CGRect dirtyRect)
        {
            base.DrawRect (dirtyRect);

        }

        public override void UpdateLayer ()
        {
            base.UpdateLayer ();

            // Draw view
            Layer.BackgroundColor = NSColor.Red.CGColor;
        }
        #endregion
    }
}
```

<a name="Using-Modern-Drag-and-Drop"></a>

## <a name="using-modern-drag-and-drop"></a>使用新式拖放

为了向用户提供现代的拖放体验，开发人员应在其应用的拖放操作中采用 _拖动群_ 。 拖动群是指每个要拖动的单个文件或项最初显示为一个元素， (该元素) 在用户继续拖动操作时所的项目数的计数。

如果用户终止拖动操作，则单个元素将 Unflock 并返回到其原始位置。

下面的示例代码允许在自定义视图上拖动群：

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainView : NSView, INSDraggingSource, INSDraggingDestination
    {
        #region Constructor
        public MainView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void MouseDragged (NSEvent theEvent)
        {
            // Create group of string to be dragged
            var string1 = new NSDraggingItem ((NSString)"Item 1");
            var string2 = new NSDraggingItem ((NSString)"Item 2");
            var string3 = new NSDraggingItem ((NSString)"Item 3");

            // Drag a cluster of items
            BeginDraggingSession (new [] { string1, string2, string3 }, theEvent, this);
        }
        #endregion
    }
}
```

群效果是通过将拖动到的方法的每一项 `BeginDraggingSession` `NSView` 作为数组中的单独元素进行发送而实现的。

使用 `NSTableView` 或时 `NSOutlineView` ，请使用类的 `PastboardWriterForRow` 方法 `NSTableViewDataSource` 来启动拖动操作：

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace MacModern
{
    public class ContentsTableDataSource: NSTableViewDataSource
    {
        #region Constructors
        public ContentsTableDataSource ()
        {
        }
        #endregion

        #region Override Methods
        public override INSPasteboardWriting GetPasteboardWriterForRow (NSTableView tableView, nint row)
        {
            // Return required pasteboard writer
            ...

            // Pasteboard writer failed
            return null;
        }
        #endregion
    }
}
```

这允许开发人员为 `NSDraggingItem` 要拖动的表中的每个项提供一个单独的，而不是将 `WriteRowsWith` 所有行作为单个组写入粘贴到剪贴板的旧方法。

使用时 `NSCollectionViews` ，请再次使用方法，而不是在 `PasteboardWriterForItemAt` `WriteItemsAt` 拖动开始时使用方法。

开发人员应始终避免将大型文件放在粘贴板上。 MacOS Sierra 的新用户， _文件承诺_ 允许开发人员将对给定文件的引用放置在以后在用户使用新 `NSFilePromiseProvider` 的和类完成删除操作时将完成的粘贴板上 `NSFilePromiseReceiver` 。

<a name="Using-Modern-Event-Tracking"></a>

## <a name="using-modern-event-tracking"></a>使用新式事件跟踪

对于已 `NSButton` 添加到标题或工具栏区域的用户界面元素 (如) ，用户应该能够单击该元素，并使它像普通 (（如显示弹出窗口) ）触发事件。 但是，由于项也在标题或工具栏区域中，因此用户也应该能够单击并拖动元素来移动窗口。

若要在代码中完成此操作，请为元素创建一个自定义类 (例如 `NSButton`) 并重写 `MouseDown` 事件，如下所示：

```csharp
public override void MouseDown (NSEvent theEvent)
{
    var shouldCallSuper = false;

    Window.TrackEventsMatching (NSEventMask.LeftMouseUp, 2000, NSRunLoop.NSRunLoopEventTracking, (NSEvent evt, ref bool stop) => {
        // Handle event as normal
        stop = true;
        shouldCallSuper = true;
    });

    Window.TrackEventsMatching(NSEventMask.LeftMouseDragged, 2000, NSRunLoop.NSRunLoopEventTracking, (NSEvent evt, ref bool stop) => {
        // Pass drag event to window
        stop = true;
        Window.PerformWindowDrag (evt);
    });

    // Call super to handle mousedown
    if (shouldCallSuper) {
        base.MouseDown (theEvent);
    }
}
```

此代码使用 `TrackEventsMatching` `NSWindow` UI 元素附加的的方法来截获 `LeftMouseUp` 和 `LeftMouseDragged` 事件。 对于 `LeftMouseUp` 事件，UI 元素会按正常方式进行响应。 对于 `LeftMouseDragged` 事件，事件会传递到 `NSWindow` 的 `PerformWindowDrag` 方法，以在屏幕上移动窗口。

调用 `PerformWindowDrag` 类的方法 `NSWindow` 具有以下优点：

- 即使应用程序挂起 (如处理 deep 循环) 时，它也允许窗口移动。
- 空间切换会按预期方式工作。
- 空格栏将显示为 "正常"。
- 窗口对齐和对齐工作正常。

<a name="Using-Modern-Container-View-Controls"></a>

## <a name="using-modern-container-view-controls"></a>使用新式容器视图控件

macOS Sierra 为以前版本的操作系统中可用的现有容器视图控件提供了很多新式改进。

<a name="Table View Enhancements"></a>

## <a name="table-view-enhancements"></a>表视图增强功能

开发人员应始终使用基于新 `NSView` 版本的容器视图控件，例如 `NSTableView` 。 例如：

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace MacModern
{
    public class ContentsTableDelegate : NSTableViewDelegate
    {
        #region Constructors
        public ContentsTableDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
        {
            // Build new view
            var view = new NSView ();
            ...

            // Return the view representing the item to display
            return view;
        }
        #endregion
    }
}
```

这允许将自定义的表行操作附加到表中的给定行 (如轻扫权限以删除行) 。 若要启用此行为，请重写 `RowActions` 的方法 `NSTableViewDelegate` ：

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace MacModern
{
    public class ContentsTableDelegate : NSTableViewDelegate
    {
        #region Constructors
        public ContentsTableDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
        {
            // Build new view
            var view = new NSView ();
            ...

            // Return the view representing the item to display
            return view;
        }

        public override NSTableViewRowAction [] RowActions (NSTableView tableView, nint row, NSTableRowActionEdge edge)
        {
            // Take action based on the edge
            if (edge == NSTableRowActionEdge.Trailing) {
                // Create row actions
                var editAction = NSTableViewRowAction.FromStyle (NSTableViewRowActionStyle.Regular, "Edit", (action, rowNum) => {
                    // Handle row being edited
                    ...
                });

                var deleteAction = NSTableViewRowAction.FromStyle (NSTableViewRowActionStyle.Destructive, "Delete", (action, rowNum) => {
                    // Handle row being deleted
                    ...
                });

                // Return actions
                return new [] { editAction, deleteAction };
            } else {
                // No matching actions
                return null;
            }
        }
        #endregion
    }
}
```

Static `NSTableViewRowAction.FromStyle` 用于创建以下样式的新的表行操作：

- `Regular` -执行标准的非破坏性操作，例如编辑行的内容。
- `Destructive` -执行破坏性操作，例如删除表中的行。 这些操作将呈现为红色背景。

<a name="Scroll-View-Enhancements"></a>

## <a name="scroll-view-enhancements"></a>滚动视图增强功能

当使用滚动视图直接 (`NSScrollView`) 时，或作为另一个控件 (如) ）的一部分时 `NSTableView` ，滚动视图的内容可以使用新式外观和视图在 Xamarin 应用程序的标题和工具栏区域下滑动。

因此，"滚动视图内容" 区域中的第一项可由标题和工具栏区域部分遮盖。

为了更正此问题，Apple 已向类添加了两个新属性 `NSScrollView` ：

- `ContentInsets` -允许开发人员提供 `NSEdgeInsets` 定义将应用于滚动视图顶部的偏移量的对象。
- `AutomaticallyAdjustsContentInsets` -如果 `true` 滚动视图将自动处理 `ContentInsets` 开发人员的。

使用 `ContentInsets` 开发人员可以调整滚动视图的开始时间，以允许包含附件，如：

- 类似于邮件应用中所示的排序指示器。
- 搜索字段。
- "刷新" 或 "更新" 按钮。

<a name="Auto-Layout-and-Localization-in-Modern-Apps"></a>

## <a name="auto-layout-and-localization-in-modern-apps"></a>新式应用中的自动布局和本地化

Apple 在 Xcode 中加入了几项技术，使开发人员可以轻松创建国际化 macOS 应用。 Xcode 现在允许开发人员将面向用户的文本从其情节提要文件中的应用程序的用户界面设计中分离出来，并提供在 UI 更改时用于维护此分离的工具。

有关详细信息，请参阅 Apple 的 [国际化和本地化指南](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPInternational/InternationalizingYourUserInterface/InternationalizingYourUserInterface.html)。

<a name="Implementing-Base-Internationalization"></a>

### <a name="implementing-base-internationalization"></a>实现基本国际化

通过实现基本国际化，开发人员可以提供单个 Storyboard 文件来表示应用的 UI 并分离出所有面向用户的字符串。

当开发人员创建) 定义应用程序用户界面的初始情节提要文件 (或文件时，会在基本国际化 (开发人员所) 说的语言。

接下来，开发人员可以将本地化和基本国际化字符串 (在可翻译为多种语言的情节提要 UI 设计) 中。

稍后，可以导入这些本地化，Xcode 将生成情节提要的特定于语言的字符串文件。

<a name="Implementing-Auto-Layout-to-Support-Localization"></a>

### <a name="implementing-auto-layout-to-support-localization"></a>实现自动布局以支持本地化

由于字符串值的本地化版本可能具有极大的大小和/或读取方向，因此开发人员应使用自动布局来定位和调整应用程序的用户界面在情节提要文件中的位置。

Apple 建议执行以下操作：

- **删除固定宽度约束** -应允许所有基于文本的视图根据其内容进行调整。 固定宽度视图可以裁剪特定语言的内容。
- **使用内部内容大小** -默认情况下，基于文本的视图会自动调整大小以适应其内容。 对于未正确调整大小的基于文本的视图，请在 Xcode 的 Interface Builder 中选择它们，然后选择 "**编辑**  >  **大小" 以适应内容**。
- **应用前导和尾随属性** -由于文本的方向可以根据用户的语言更改，因此，请使用新的 `Leading` 和 `Trailing` 约束属性，而不是现有的 `Right` 和 `Left` 属性。 `Leading` 和 `Trailing` 将根据语言方向自动调整。
- **将视图固定到相邻视图** -这允许视图在其更改为响应所选语言时进行重定位和调整大小。
- **不要设置 windows 的最小值和/或最大大小** -允许 windows 更改大小，因为所选的语言会调整其内容区域的大小。
- **不断变化的测试布局更改** -在应用程序的开发过程中，应以不同的语言不断地测试。 有关更多详细信息，请参阅 Apple 的 [测试国际化应用](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPInternational/TestingYourInternationalApp/TestingYourInternationalApp.html#//apple_ref/doc/uid/10000171i-CH7-SW1) 文档。
- **使用 NSStackViews 将视图固定在一起**  -  `NSStackViews`允许其内容以可预测的方式进行移动和增长，并根据所选的语言更改内容大小。

<a name="Localizing-in-Xcodes-Interface-Builder"></a>

### <a name="localizing-in-xcodes-interface-builder"></a>在 Xcode 的 Interface Builder 中进行本地化

Apple 在 Xcode 的 Interface Builder 中提供了若干功能，开发人员可以在设计或编辑应用的 UI 时使用这些功能来支持本地化。 **特性检查器**的 "**文本方向**" 部分允许开发人员提供有关如何使用和更新基于选择文本的视图的提示 (如 `NSTextField`) ：

[![文本方向选项](modern-cocoa-apps-images/content10.png)](modern-cocoa-apps-images/content10.png#lightbox)

**文本方向**有三个可能的值：

- **自然** -布局基于分配给控件的字符串。
- **从左到右** -布局始终强制从左到右。
- 从**右到左**-布局始终强制从右到左。

**布局**有两个可能的值：

- **从左到右** -布局始终从左到右。
- 从**右到左**-布局始终从右到左。

通常不应更改这些更改，除非需要特定的对齐方式。

**Mirror**属性告知系统 (翻转特定控件属性，如单元图像位置) 。 它有三个可能的值：

- **自动** -根据所选语言的方向，自动更改位置。
- 从**右到左界面**-位置只会按从右到左的语言进行更改。
- **从不** -位置永远不会改变。

如果开发人员对基于文本的视图的内容指定了 " **中心**"、" **两端** 对齐" 或 " **完全** 对齐"，则将不会根据所选的语言翻转这些内容。

在 macOS Sierra 之前，不会自动镜像在代码中创建的控件。 开发人员必须使用类似于下面的代码来处理镜像：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Setting a button's mirroring based on the layout direction
    var button = new NSButton ();
    if (button.UserInterfaceLayoutDirection == NSUserInterfaceLayoutDirection.LeftToRight) {
        button.Alignment = NSTextAlignment.Right;
        button.ImagePosition = NSCellImagePosition.ImageLeft;
    } else {
        button.Alignment = NSTextAlignment.Left;
        button.ImagePosition = NSCellImagePosition.ImageRight;
    }
}
```

，其中 `Alignment` 和 `ImagePosition` 是根据控件的进行设置的 `UserInterfaceLayoutDirection` 。

macOS Sierra 通过静态方法添加了几个新的便利构造函数 (`CreateButton`) 采用多个参数 (例如标题、图像和操作) 并将自动正确镜像。 例如：

```csharp
var button2 = NSButton.CreateButton (myTitle, myImage, () => {
    // Take action when the button is pressed
    ...
});
```

<a name="Using-System-Appearances"></a>

## <a name="using-system-appearances"></a>使用系统外观

新式 macOS 应用可采用全新的深色界面外观，适用于创建、编辑或展示应用的映像：

[![深色 Mac 窗口 UI 的示例](modern-cocoa-apps-images/content11.png)](modern-cocoa-apps-images/content11.png#lightbox)

这可以通过在显示窗口之前添加一行代码来完成。 例如：

```csharp
using System;
using AppKit;
using Foundation;

namespace MacModern
{
    public partial class ViewController : NSViewController
    {
        ...

        #region Override Methods
        public override void ViewWillAppear ()
        {
            base.ViewWillAppear ();

            // Apply the Dark Interface Appearance
            View.Window.Appearance = NSAppearance.GetAppearance (NSAppearance.NameVibrantDark);

            ...
        }
        #endregion
    }
}
```

类的静态 `GetAppearance` 方法 `NSAppearance` 用于从系统 (获取命名外观， `NSAppearance.NameVibrantDark`) 。

Apple 对于使用系统外观具有以下建议：

- 使用硬编码值的命名颜色， (例如 `LabelColor` 和 `SelectedControlColor`) 。
- 尽可能使用系统标准控件样式。

对于已启用 "系统首选项" 应用的辅助功能的用户，使用系统外观的 macOS 应用将自动正常工作。 因此，Apple 建议开发人员始终在其 macOS 应用程序中使用系统外观。

<a name="Designing-UIs-with-Storyboards"></a>

## <a name="designing-uis-with-storyboards"></a>用情节提要设计 Ui

使用情节提要，开发人员不仅可以设计构成应用程序用户界面的单个元素，还可以直观显示和设计 UI 流以及给定元素的层次结构。

控制器允许开发人员将元素收集到组合和 Segue 抽象单元中，并删除在整个视图层次结构中移动所需的典型 "胶水代码"：

[![在 Xcode 的 Interface Builder 中编辑 UI](modern-cocoa-apps-images/content12.png)](modern-cocoa-apps-images/content12.png#lightbox)

有关详细信息，请参阅 [介绍情节提要](~/mac/platform/storyboards/index.md) 文档。

在很多情况下，情节提要中定义的给定场景将需要视图层次结构中前一场景的数据。 Apple 提供以下建议，用于在场景间传递信息：

- 数据 dependancies 应始终沿层次结构向下传递。
- 避免硬编码 UI 结构 dependancies，因为这限制了 UI 的灵活性。
- 使用 c # 接口提供一般的数据 dependancies。

作为 Segue 的源的视图控制器，可以重写 `PrepareForSegue` 方法，并执行所需的任何初始化 (例如，在 Segue 执行之前传递) 数据，以显示目标视图控制器。 例如：

```csharp
public override void PrepareForSegue (NSStoryboardSegue segue, NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    // Take action based on Segue ID
    switch (segue.Identifier) {
    case "MyNamedSegue":
        // Prepare for the segue to happen
        ...
        break;
    }
}
```

有关详细信息，请参阅我们的 [segue](~/mac/platform/storyboards/indepth.md#Segues) 文档。

<a name="Propagating-Actions"></a>

## <a name="propagating-actions"></a>传播操作

基于 macOS 应用程序的设计，有时 UI 控件上某个操作的最佳处理程序可能位于 UI 层次结构中的其他位置。 通常，这种情况下，菜单和菜单项位于其自身的场景中，与应用程序的其余部分分开。

若要处理这种情况，开发人员可以创建自定义操作并在响应方链上传递操作。 有关详细信息，请参阅 [使用自定义窗口操作](~/mac/user-interface/menu.md) 文档。

<a name="Modern-Mac-Features"></a>

## <a name="modern-mac-features"></a>新式 Mac 功能

Apple 在 macOS Sierra 中包含了几个面向用户的功能，使开发人员能够充分利用 Mac 平台，如：

- **NSUserActivity** -这允许应用描述用户当前涉及的活动。 `NSUserActivity` 最初创建它是为了支持移交，在这种情况下，可以在另一台设备上选择并继续使用在某个用户的设备上启动的活动。 `NSUserActivity` 在 macOS 中的工作方式与在 iOS 中的工作方式相同，有关详细信息，请参阅关于移交 ios 文档的 [简介](~/ios/platform/handoff.md) 。
- Siri**上的 Siri**使用当前活动 (`NSUserActivity`) 为用户可以发出的命令提供上下文。
- **状态还原** -当用户在 macOS 上退出应用程序，然后 relaunches 它后，该应用程序将自动恢复到以前的状态。 开发人员在向用户显示用户界面之前，可以使用状态还原 API 编码和还原暂时性 UI 状态。 如果应用是 `NSDocument` 基于的，则会自动处理状态还原。 若要为非基于的应用启用状态还原 `NSDocument` ，请将类的设置 `Restorable` `NSWindow` 为 `true` 。
- **云中的文档-在** macOS Sierra 之前，应用程序必须明确选择使用用户的 iCloud 驱动器中的文档。 在 macOS Sierra 用户的 **桌面** 和 **文档** 文件夹可能会由系统自动与其 iCloud 驱动器同步。 因此，可能会删除文档的本地副本，以释放用户计算机上的空间。 `NSDocument` 基于的应用会自动处理此更改。 所有其他应用程序类型都需要使用 `NSFileCoordinator` 来同步文档的读取和写入。

<a name="Summary"></a>

## <a name="summary"></a>总结

本文介绍了一些提示、功能和技术，开发人员可以使用这些技巧在 Xamarin 中构建现代 macOS 应用。

## <a name="related-links"></a>相关链接

- [macOS 示例](/samples/browse/?products=xamarin&term=Xamarin.Mac)