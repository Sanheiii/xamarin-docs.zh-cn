---
title: 在 Xamarin 中创建用户界面对象
description: '本文档概述了如何在 Xamarin 中创建用户界面。 它讨论了 iOS 设计器、Xcode Interface Builder、c # 和情节提要。'
ms.prod: xamarin
ms.assetid: 4D6B136C-744A-4936-8655-A77E62BA7A60
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 5b1bd4a07f9df13bff517db28715d985fc33e322
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430253"
---
# <a name="creating-user-interface-objects-in-xamarinios"></a>在 Xamarin 中创建用户界面对象

Apple 将相关功能部分分组到与 Xamarin 命名空间相同的 "框架" 中。 `UIKit` 命名空间，包含适用于 iOS 的所有用户界面控件。

只要代码需要引用用户界面控件（如标签或按钮），请记住包含以下 using 语句：

```csharp
using UIKit;
```

本章中讨论的所有控件都在 UIKit 命名空间中，每个用户控件类名都有 `UI` 前缀。

可以通过以下三种方式编辑 UI 控件和布局：

- **[Xamarin IOS 设计器](~/ios/user-interface/designer/index.md)** –使用 Xamarin 的内置布局设计器设计屏幕。 双击 "情节提要" 或 "XIB 文件" 以利用内置设计器进行编辑。
- **Xcode Interface Builder** -将控件拖动到 Interface Builder 的屏幕布局。 右键单击 " **Solution Pad** " 中的文件，然后选择 " **打开方式" > Xcode Interface Builder**，在 Xcode 中打开情节提要或 XIB 文件。
- **使用 c #** –控件还可以使用代码以编程方式构造并添加到视图层次结构中。

可以通过右键单击 iOS 项目，然后选择 " **添加 > 新文件 ...**" 来添加新的情节提要和 XIB 文件。

无论使用哪种方法，都可以在应用程序逻辑中使用 c # 对控件属性和事件进行操作。

## <a name="using-xamarin-ios-designer"></a>使用 Xamarin iOS 设计器

若要开始在 iOS 设计器中创建用户界面，请双击情节提要文件。 控件可从 **工具箱** 拖到设计图面上，如下所示：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

 [![工具箱面板](creating-ui-objects-images/image2b.png)](creating-ui-objects-images/image2b.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

 [![工具箱面板-视觉对象 Studio](creating-ui-objects-images/image2b-vs.png)](creating-ui-objects-images/image2b.png#lightbox)

-----

在设计图面上选择控件时， **Properties Pad** 将显示该控件的特性。 在下面的屏幕截图中填充的 **小组件 > 标识 > Name** "字段，用作 *插座* 名称。 这是你可以在 c # 中引用控件的方式：

 [![属性小组件板](creating-ui-objects-images/image3b.png)](creating-ui-objects-images/image3b.png#lightbox)

有关使用 iOS 设计器的更深入了解，请参阅 [Ios 设计器简介](~/ios/user-interface/designer/introduction.md) 指南。

## <a name="using-xcode-interface-builder"></a>使用 Xcode Interface Builder

如果不熟悉如何使用 Interface Builder，请参阅 Apple 的 [Interface Builder](https://developer.apple.com/xcode/interface-builder/) 文档。

若要在 Xcode 中打开情节提要，请右键单击以访问情节提要文件的上下文菜单，然后选择使用 **Xcode Interface Builder**打开：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

 [![情节提要上下文菜单-Xcode](creating-ui-objects-images/imagexcode.png)](creating-ui-objects-images/imagexcode.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![情节提要上下文菜单-Xcode](creating-ui-objects-images/imagexcode-vs.png)](creating-ui-objects-images/imagexcode-vs.png#lightbox)

-----

控件可以从如下所示的 **对象库** 拖动到 Design Surface：

 [![Xcode 对象库](creating-ui-objects-images/image5a.png)](creating-ui-objects-images/image5a.png#lightbox)

使用 Interface Builder 设计 UI 时，必须为希望在 c # 中引用的每个控件创建一个 **插座** 。 这是通过使用 Xcode 工具栏按钮上的 "中心**编辑器**" 按钮打开**助手编辑器**来完成的：

 [!["助手编辑器" 按钮](creating-ui-objects-images/image6a.png)](creating-ui-objects-images/image6a.png#lightbox)

单击用户界面对象;然后， **控制拖动** 到 .h 文件中。 若要 **控制拖动**，请按住 ctrl 键，然后单击并按住要为其创建插座 (或操作) 的用户界面对象。 在拖动到标头文件中时，请按住 Ctrl 键。 完成定义下面的拖动操作 `@interface` 。 将显示一条带有标题插入插座或插座的蓝线，如以下屏幕截图中所示。

当你发布单击时，系统将提示你提供插座的名称，此名称将用于创建可在代码中引用的 c # 属性：

 [![创建输出口](creating-ui-objects-images/image8a.png)](creating-ui-objects-images/image8a.png#lightbox)

有关 Xcode Interface Builder 如何与 Visual Studio for Mac 集成的详细信息，请参阅 [Xib 代码生成](~/ios/internals/xib-code-generation.md#generated) 文档。

## <a name="using-c"></a>使用 C\#

如果决定使用 c # (在视图或视图控制器中以编程方式创建用户界面对象，例如) ，请执行以下步骤：

- 为用户界面对象声明类级别的字段。 创建控件本身， `ViewDidLoad` 例如。 然后，可以在视图控制器的整个生命周期方法中引用该对象 (例如。
`ViewWillAppear`).
- 创建一个 `CGRect` ，它定义控件的框架 (其在屏幕上的 X 和 Y 坐标以及其宽度和高度) 。 你需要确保具有 `using CoreGraphics` 此的指令。
- 调用构造函数来创建和分配控件。
- 设置任何属性或事件处理程序。
- 调用 `Add()` 将控件添加到视图层次结构。

下面是 `UILabel` 使用 c # 在视图控制器中创建的一个简单示例：

```csharp
UILabel label1;
public override void ViewDidLoad () {
    base.ViewDidLoad ();
    var frame = new CGRect(10, 10, 300, 30);
    label1 = new UILabel(frame);
    label1.Text = "New Label";
    View.Add (label1);
}
```

<a name="partial_classes"></a>

## <a name="using-c-and-storyboards"></a>使用 c # 和情节提要

将视图控制器添加到 Design Surface 时，将在项目中创建两个对应的 c # 文件。 在此示例中， `ControlsViewController.cs` 和已 `ControlsViewController.designer.cs` 自动创建：

 [![ViewController 分部类](creating-ui-objects-images/image9b.png)](creating-ui-objects-images/image9b.png#lightbox)

此 `ControlsViewController.cs` 文件用于您的 *代码*。 这是 `View` 实现生命周期方法（如和）的位置 `ViewDidLoad` `ViewWillAppear` ，可以在其中添加自己的属性、字段和方法。

`ControlsViewController.designer.cs`是生成的包含分部类的代码。 在 Visual Studio for Mac 中的设计图面上命名控件或在 Xcode 中创建插座或操作时，会将相应的属性或分部方法添加到设计器 (designer.cs) 文件中。 下面的代码演示了一个为两个按钮和一个文本视图生成的代码示例，其中一个按钮也有一个 `TouchUpInside` 事件。

利用分部类的这些元素，你的代码可以引用控件并响应设计图面上声明的操作：

```csharp
[Register ("ControlsViewController")]
    partial class ControlsViewController
    {
        [Outlet]
        [GeneratedCodeAttribute ("iOS Designer", "1.0")]
        UIKit.UIButton Button1 { get; set; }

        [Outlet]
        [GeneratedCodeAttribute ("iOS Designer", "1.0")]
        UIKit.UIButton Button2 { get; set; }

        [Outlet]
        [GeneratedCodeAttribute ("iOS Designer", "1.0")]
        UIKit.UITextField textfield1 { get; set; }

        [Action ("button2_TouchUpInside:")]
        [GeneratedCodeAttribute ("iOS Designer", "1.0")]
        partial void button2_TouchUpInside (UIButton sender);

        void ReleaseDesignerOutlets ()
        {
            if (Button1 != null) {
                Button1.Dispose ();
                Button1 = null;
            }
            if (Button2 != null) {
                Button2.Dispose ();
                Button2 = null;
            }
            if (textfield1 != null) {
                textfield1.Dispose ();
                textfield1 = null;
            }
        }
    }
}
```

`designer.cs`不应手动编辑该文件– IDE (Visual Studio for Mac 或 Visual Studio) 负责使其与情节提要保持同步。

以编程方式向或添加用户界面对象时， `View` `ViewController` 你会自行实例化和管理对象引用，因此不需要设计器文件。

## <a name="related-links"></a>相关链接

- [控制 (示例) ](/samples/xamarin/ios-samples/controls)