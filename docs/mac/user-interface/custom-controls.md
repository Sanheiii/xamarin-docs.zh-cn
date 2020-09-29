---
title: 在 Xamarin 中创建自定义控件
description: 本文档介绍如何在 Xamarin 中生成自定义控件。 它演示如何生成自定义控件、跟踪其状态、绘制其接口、响应用户输入以及在应用程序中使用控件。
ms.prod: xamarin
ms.assetid: 004534B1-5AEE-452C-BBBE-8C2673FD49B7
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: db4cab313b1c1f2fc1aaa969735f7f136feb7015
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91429689"
---
# <a name="creating-custom-controls-in-xamarinmac"></a>在 Xamarin 中创建自定义控件

在 Xamarin 应用*程序中使用*c # 和 .net 时，可以访问在 Xcode、 *Swift*和*Xcode*中工作的开发人员的相同用户控件。 由于 Xamarin 与 Xcode 直接集成，因此可以使用 Xcode 的 _Interface Builder_ 创建和维护用户控件 (或者在 c # 代码) 中直接创建用户控件。

尽管 macOS 提供了大量的内置用户控件，但有时你可能需要创建自定义控件来提供未现成提供的功能，或与自定义 UI 主题 (例如游戏界面) 相匹配。

[![自定义 UI 控件的示例](custom-controls-images/intro01.png)](custom-controls-images/intro01.png#lightbox)

在本文中，我们将介绍在 Xamarin. Mac 应用程序中创建可重用自定义用户界面控件的基本知识。 强烈建议您先完成 [Hello，Mac](~/mac/get-started/hello-mac.md) 一文，特别是 [Xcode 和 Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) 及 [输出口和操作](~/mac/get-started/hello-mac.md#outlets-and-actions) 部分的简介，因为它涵盖了我们将在本文中使用的重要概念和技巧。

你可能想要查看[Xamarin 内部](~/mac/internals/how-it-works.md)示例文档的 "将[c # 类/方法公开到目标 c](~/mac/internals/how-it-works.md) " 部分， `Register` 并说明用于将 `Export` C # 类连接到目标 C 对象和 UI 元素的和命令。

<a name="Introduction-to-Outline-Views"></a>

## <a name="introduction-to-custom-controls"></a>自定义控件简介

如上所述，有时需要创建可重用的自定义用户界面控件，为 Xamarin 应用程序的 UI 提供独特的功能，或创建自定义 UI 主题 (例如游戏界面) 。

在这些情况下，可以轻松继承 `NSControl` 并创建可通过 c # 代码或通过 Xcode 的 Interface Builder 添加到应用的 UI 的自定义工具。 通过继承 `NSControl` 自定义控件，内置用户界面控件的所有标准功能都将自动具有 (如 `NSButton`) 。

如果你的自定义用户界面控件只显示 (如) 自定义图表和图形工具的信息，则你可能希望从 `NSView` 而不是继承 `NSControl` 。

无论使用哪个基类，创建自定义控件的基本步骤都是相同的。

在本文中，将创建一个自定义翻转交换机组件，该组件提供唯一的用户界面主题，并提供了一个示例，说明如何生成功能齐全的自定义用户界面控件。

<a name="Building-the-Custom-Control"></a>

## <a name="building-the-custom-control"></a>生成自定义控件

由于我们创建的自定义控件将响应用户输入 (鼠标左键单击) ，我们将从继承 `NSControl` 。 通过这种方式，自定义控件将自动拥有内置用户界面控件具有的所有标准功能，并像标准 macOS 控件一样做出响应。

在 Visual Studio for Mac 中，打开要为其创建自定义用户界面控件的 Xamarin (项目，或创建一个新的) 。 添加新类并调用它 `NSFlipSwitch` ：

[![添加新类](custom-controls-images/custom01.png)](custom-controls-images/custom01.png#lightbox)

接下来，编辑 `NSFlipSwitch.cs` 类，使其类似于以下内容：

```csharp
using Foundation;
using System;
using System.CodeDom.Compiler;
using AppKit;
using CoreGraphics;

namespace MacCustomControl
{
    [Register("NSFlipSwitch")]
    public class NSFlipSwitch : NSControl
    {
        #region Private Variables
        private bool _value = false;
        #endregion

        #region Computed Properties
        public bool Value {
            get { return _value; }
            set {
                // Save value and force a redraw
                _value = value;
                NeedsDisplay = true;
            }
        }
        #endregion

        #region Constructors
        public NSFlipSwitch ()
        {
            // Init
            Initialize();
        }

        public NSFlipSwitch (IntPtr handle) : base (handle)
        {
            // Init
            Initialize();
        }

        [Export ("initWithFrame:")]
        public NSFlipSwitch (CGRect frameRect) : base(frameRect) {
            // Init
            Initialize();
        }

        private void Initialize() {
            this.WantsLayer = true;
            this.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
        }
        #endregion

        #region Draw Methods
        public override void DrawRect (CGRect dirtyRect)
        {
            base.DrawRect (dirtyRect);

            // Use Core Graphic routines to draw our UI
            ...

        }
        #endregion

        #region Private Methods
        private void FlipSwitchState() {
            // Update state
            Value = !Value;
        }
        #endregion

    }
}
```

首先要注意的是，我们要从继承 `NSControl` ，并使用 **Register** 命令向目标 C 和 Xcode 的 Interface Builder 公开此类：

```csharp
[Register("NSFlipSwitch")]
public class NSFlipSwitch : NSControl
```

在以下部分中，我们将详细介绍上述代码的其余部分。

<a name="Tracking-the-Controls-State"></a>

### <a name="tracking-the-controls-state"></a>跟踪控件的状态

由于自定义控件是一个交换机，因此我们需要一种方法来跟踪开关的开/关状态。 我们在中处理以下代码 `NSFlipSwitch` ：

```csharp
private bool _value = false;
...

public bool Value {
    get { return _value; }
    set {
        // Save value and force a redraw
        _value = value;
        NeedsDisplay = true;
    }
}
```

交换机的状态发生更改时，我们需要一种方法来更新 UI。 为此，请强制控件使用重绘其 UI `NeedsDisplay = true` 。

如果我们的控件需要多个 "开/关" 状态 (例如，具有3个位置) 多状态交换机，则可以使用 **枚举** 来跟踪状态。 对于我们的示例，简单的 **布尔** 值将执行此操作。

我们还添加了一个帮助器方法，以便在打开和关闭之间交换开关状态：

```csharp
private void FlipSwitchState() {
    // Update state
    Value = !Value;
}
```

稍后，我们将展开此帮助器类，以在交换机状态发生更改时通知调用方。

<a name="Drawing-the-Controls-Interface"></a>

### <a name="drawing-the-controls-interface"></a>绘制控件的接口

我们将使用核心图形绘图例程在运行时绘制自定义控件的用户界面。 在执行此操作之前，我们需要为控件打开层。 我们通过以下私有方法来实现此目的：

```csharp
private void Initialize() {
    this.WantsLayer = true;
    this.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
}
```

此方法从控件的每个构造函数中调用，以确保正确配置控件。 例如：

```csharp
public NSFlipSwitch (IntPtr handle) : base (handle)
{
    // Init
    Initialize();
}
```

接下来，需要重写 `DrawRect` 方法，并添加核心图形例程来绘制控件：

```csharp
public override void DrawRect (CGRect dirtyRect)
{
    base.DrawRect (dirtyRect);

    // Use Core Graphic routines to draw our UI
    ...

}
```

当控件的状态发生更改时，我们将调整控件的可视化表示形式 (例如， **从开始** 到 **关闭**) 。 状态发生更改时，可以使用 `NeedsDisplay = true` 命令强制控件重绘新状态。

<a name="Responding-to-User-Input"></a>

### <a name="responding-to-user-input"></a>响应用户输入

可以通过两种基本方式将用户输入添加到自定义控件： **覆盖鼠标处理例程** 或 **笔势识别**器。 使用哪种方法，将基于控件所需的功能。

> [!IMPORTANT]
> 对于您创建的任何自定义控件，都应使用**Override 方法**_或_**笔势识别**器，但不能同时使用这两种方法互相冲突。

<a name="Summary"></a>

#### <a name="handling-user-input-with-override-methods"></a>用 Override 方法处理用户输入

继承自 `NSControl` (或) 的对象 `NSView` 具有多个用于处理鼠标或键盘输入的重写方法。 对于我们的示例控件，我们希望在用户用鼠标左键单击控件时， **在打开** 和 **关闭** 之间切换切换状态。 我们可以向类添加以下重写方法 `NSFlipSwitch` 来处理此操作：

```csharp
#region Mouse Handling Methods
// --------------------------------------------------------------------------------
// Handle mouse with Override Methods.
// NOTE: Use either this method or Gesture Recognizers, NOT both!
// --------------------------------------------------------------------------------
public override void MouseDown (NSEvent theEvent)
{
    base.MouseDown (theEvent);

    FlipSwitchState ();
}

public override void MouseDragged (NSEvent theEvent)
{
    base.MouseDragged (theEvent);
}

public override void MouseUp (NSEvent theEvent)
{
    base.MouseUp (theEvent);
}

public override void MouseMoved (NSEvent theEvent)
{
    base.MouseMoved (theEvent);
}
## endregion
```

在上面的代码中，我们调用了 `FlipSwitchState` 前面定义的方法 () 来翻转方法中的开关状态 `MouseDown` 。 这也会强制重新绘制控件以反映当前状态。

<a name="Handling-User-Input-with-Gesture-Recognizers"></a>

#### <a name="handling-user-input-with-gesture-recognizers"></a>用手势识别器处理用户输入

（可选）可以使用笔势识别器来处理与控件交互的用户。 删除上面添加的替代，编辑 `Initialize` 方法，使其看起来如下所示：

```csharp
private void Initialize() {
    this.WantsLayer = true;
    this.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;

    // --------------------------------------------------------------------------------
    // Handle mouse with Gesture Recognizers.
    // NOTE: Use either this method or the Override Methods, NOT both!
    // --------------------------------------------------------------------------------
    var click = new NSClickGestureRecognizer (() => {
        FlipSwitchState();
    });
    AddGestureRecognizer (click);
}
```

在这里，我们将创建一个新的 `NSClickGestureRecognizer` 并调用 `FlipSwitchState` 方法，以便在用户使用鼠标左键单击时更改开关的状态。 `AddGestureRecognizer (click)`方法将笔势识别器添加到控件。

同样，我们使用哪种方法取决于我们使用我们的自定义控件所要完成的工作。 如果需要对用户交互使用低级别访问权限，请使用重写方法。 如果需要预定义的功能（如鼠标单击），请使用手势识别器。

<a name="Responding-to-State-Change-Events"></a>

### <a name="responding-to-state-change-events"></a>响应状态更改事件

当用户更改自定义控件的状态时，需要一种方法来响应代码中的状态变化 (例如，在单击自定义按钮) 时执行一些操作。

若要提供此功能，请编辑 `NSFlipSwitch` 类并添加以下代码：

```csharp
#region Events
public event EventHandler ValueChanged;

internal void RaiseValueChanged() {
    if (this.ValueChanged != null)
        this.ValueChanged (this, EventArgs.Empty);

    // Perform any action bound to the control from Interface Builder
    // via an Action.
    if (this.Action !=null)
        NSApplication.SharedApplication.SendAction (this.Action, this.Target, this);
}
## endregion
```

接下来，编辑 `FlipSwitchState` 方法并使其类似于以下内容：

```csharp
private void FlipSwitchState() {
    // Update state
    Value = !Value;
    RaiseValueChanged ();
}
```

首先，我们提供了一个 `ValueChanged` 事件，可在 c # 代码中将处理程序添加到，以便在用户更改开关状态时执行操作。

其次，由于自定义控件继承自 `NSControl` ，因此它自动具有可在 Xcode 的 Interface Builder 中分配的 **操作** 。 若要在状态更改时调用此 **操作** ，请使用以下代码：

```csharp
if (this.Action !=null)
    NSApplication.SharedApplication.SendAction (this.Action, this.Target, this);
```

首先，检查是否已将 **操作** 分配给控件。 接下来，我们调用 **操作** （如果已定义）。

<a name="Using-the-Custom-Control"></a>

## <a name="using-the-custom-control"></a>使用自定义控件

自定义控件完全定义后，可以使用 c # 代码或 Xcode 的 Interface Builder 将其添加到 Xamarin 应用程序的 UI 中。

若要使用 Interface Builder 添加控件，请先执行 Xamarin. Mac 项目的干净生成，然后双击该 `Main.storyboard` 文件以在 Interface Builder 进行编辑的情况下将其打开：

[![在 Xcode 中编辑情节提要](custom-controls-images/custom02.png)](custom-controls-images/custom02.png#lightbox)

接下来，将拖 `Custom View` 入用户界面设计：

[![从库中选择自定义视图](custom-controls-images/custom03.png)](custom-controls-images/custom03.png#lightbox)

在 "自定义视图" 仍处于选中状态的情况下，切换到 **标识检查器** 并将视图的 **类** 更改为 `NSFlipSwitch` ：

[![设置视图的类](custom-controls-images/custom04.png)](custom-controls-images/custom04.png#lightbox)

切换到 **助手编辑器** 并为自定义控件创建一个 **插座** (确保将其绑定到 `ViewController.h` 文件而不是 `.m` 文件) ：

[![配置新插座](custom-controls-images/custom05.png)](custom-controls-images/custom05.png#lightbox)

保存所做的更改，返回 Visual Studio for Mac，并允许更改同步。编辑 `ViewController.cs` 文件并使方法如下所 `ViewDidLoad` 示：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Do any additional setup after loading the view.
    OptionTwo.ValueChanged += (sender, e) => {
        // Display the state of the option switch
        Console.WriteLine("Option Two: {0}", OptionTwo.Value);
    };
}
```

在这里，我们将响应 `ValueChanged` 我们在类上定义的事件 `NSFlipSwitch` ，并在用户单击控件时写出当前 **值** 。

根据需要，我们可以返回到 Interface Builder 并定义控件上的 **操作** ：

[![配置新操作](custom-controls-images/custom06.png)](custom-controls-images/custom06.png#lightbox)

同样，编辑 `ViewController.cs` 文件并添加以下方法：

```csharp
partial void OptionTwoFlipped (Foundation.NSObject sender) {
    // Display the state of the option switch
    Console.WriteLine("Option Two: {0}", OptionTwo.Value);
}
```

> [!IMPORTANT]
> 您应该使用 **事件** 或在 Interface Builder 中定义 **操作** ，但是不应同时使用这两种方法，也不能相互冲突。

<a name="Summary"></a>

## <a name="summary"></a>总结

本文详细介绍了如何在 Xamarin. Mac 应用程序中创建可重用的自定义用户界面控件。 我们了解了如何绘制自定义控件 UI，这是响应鼠标和用户输入的两个主要方法，以及如何向 Xcode 的 Interface Builder 中的操作公开新控件。

## <a name="related-links"></a>相关链接

- [MacCustomControl (示例) ](/samples/xamarin/mac-samples/maccustomcontrol)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [数据绑定和键值编码](~/mac/app-fundamentals/databinding.md)
- [OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [处理鼠标事件](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/EventOverview/HandlingMouseEvents/HandlingMouseEvents.html)