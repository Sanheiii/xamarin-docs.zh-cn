---
title: iOS 7 用户界面概述
description: iOS 7 引入了用户界面更改的很多。 本文重点介绍了在控件和支持新设计的 Api 的视觉外观中的一些较大更改。
ms.prod: xamarin
ms.assetid: FADCEA7C-8968-42A1-9E9E-F4BBAB7BCF2C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: c02b810cc61779f5c3b5ee5eb61169e8c3fceab4
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2020
ms.locfileid: "86931204"
---
# <a name="ios-7-user-interface-overview"></a>iOS 7 用户界面概述

_iOS 7 引入了用户界面更改的很多。本文重点介绍了在控件和支持新设计的 Api 的视觉外观中的一些较大更改。_

iOS 7 重点介绍内容。 IOS 7 中的用户界面元素通过删除多余的边框、状态栏和导航栏等特性来消除 chrome，这会减少内容视图使用的屏幕空间量。 在 iOS 7 中，内容设计为使用整个屏幕。

iOS 7 引入了多个其他更改：颜色用于区分用户界面元素，而不是属性（如按钮边框）。 许多元素，如导航栏和状态栏，它们现在是模糊的、半透明的或透明的，其中的内容视图位于其下方。 这些内容视图通过模糊条显示，并在用户界面中传达更深入的感受。

本文介绍了 iOS 7 中的用户界面元素以及与新的用户界面设计相关的各种 Api 的几项更改。

## <a name="view-and-control-changes"></a>查看和控制更改

UIKit 中的所有视图都符合 iOS 7 的全新外观。 本部分重点介绍这些视图的一些更改，以及已更改为支持新 UI 的相关 Api。

### <a name="uibutton"></a>UIButton

默认情况下，从类创建的按钮 `UIButton` 是无边框的，默认情况下没有背景，如下所示：

 ![示例 UIButton](ios7-ui-images/button.png)

`UIButtonType.RoundedRect`样式已被弃用。 如果在 iOS 7 中使用， `UIButtonType.RoundedRect` 将导致 `UIButtonType.System` 使用，这将产生没有背景或可见边缘的默认按钮样式，如上所示。

### <a name="uibarbuttonitem"></a>UIBarButtonItem

类似于 `UIButton` ，条形按钮也有无边框，默认为如下 `UIBarButtonItemStyle.Plain` 所示的新样式：

 ![示例 UIBarButtonItem](ios7-ui-images/barbuttonplain.png)

此外， `UIBarButtonItemStyle.Bordered` 样式已不推荐使用。 `UIBarButtonItemStyle.Bordered`IOS 7 中的设置将导致 `UIBarButtonItemStyle.Plain` 使用样式。

`UIBarButtonItemStyle.Done`尚未弃用样式。 不过，它还将创建一个无边框按钮，只使用粗体文本样式，如下所示：

 ![完成样式中的 UIBarButtonItem 示例](ios7-ui-images/barbuttondone.png)

### <a name="uialertview"></a>UIAlertView

除了新的 iOS 7 外观的样式变化外，警报视图不再支持通过子视图进行的自定义。 即使 `UIAlertView` 从继承 `UIView` ，也不会 `AddSubview` `UIAlertView` 产生任何影响。 例如，考虑以下代码：

```csharp
UIBarButtonItem button = new UIBarButtonItem ("Bar Button", UIBarButtonItemStyle.Plain, (s,e) =>
{
    UIAlertView alert = new UIAlertView ("Title", "Message", null, "Cancel", "OK");

    alert.AddSubview (new UIView () {
        Frame = new CGRect(50, 50,100, 100),
        BackgroundColor = UIColor.Green
    });

    alert.Show ();
});
```

这会生成一个标准警报视图，其中的子视图被忽略，如下所示：

 ![示例 UIAlertView](ios7-ui-images/alert.png)

 注意： UIAlertView 在 iOS 8 中已弃用。 查看有关在 iOS 8 及更高版本中使用警报视图的[警报控制器](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller)食谱。

### <a name="uisegmentedcontrol"></a>UISegmentedControl

IOS 7 中的分段控件是透明的，并且支持的色调颜色。 淡色颜色用于文本和边框颜色。 选择段后，颜色将在背景和文本之间交换，使用淡色颜色突出显示选定段，如下所示：

 ![示例 UISegmentedControl](ios7-ui-images/segmentedcontrol.png)

此外， `UISegmentedControlStyle` iOS 7 中已弃用。

### <a name="picker-views"></a>选取器视图

选择器视图的 API 在很大程度上保持不变;但是，iOS 7 设计指导原则现在应以内联方式展示选取器视图，而不是显示为在屏幕底部或新控制器中推入到导航控制器堆栈上的新控制器的输入视图。 此操作可在系统日历应用中查看：

 ![此项可在系统日历应用中查看](ios7-ui-images/inlinepicker.png)

### <a name="uisearchdisplaycontroller"></a>UISearchDisplayController

如果将 `UISearchDisplayController.DisplaysSearchBarInNavigationBar` 属性设置为 true，则该搜索栏现在会显示在导航栏中。 如果设置为 false （默认值），则在显示搜索控制器时将隐藏导航栏。

以下屏幕截图显示了中的搜索栏 `UISearchDisplayController` ：

 ![示例 UISearchDisplayController](ios7-ui-images/searchbar.png)

### <a name="uitableview"></a>UITableView

围绕的 Api 在很大程度 `UITableView` 上是不变的; 但是，样式发生了重大改变，以符合新的用户界面设计。 内部视图层次结构也略有不同。 此更改不会影响大多数应用程序，但需要注意一些问题。

#### <a name="grouped-table-style"></a>分组表样式

已更改的分组样式已经更新，内容现在延伸到屏幕边缘，如下所示：

 ![示例分组表样式](ios7-ui-images/table1.png)

#### <a name="separatorinset"></a>SeparatorInset

现在可以通过设置属性缩进行分隔符 `UITableVIewCell.SeparatorInset` 。 例如，以下代码将用于从左边缘缩进单元：

```csharp
cell.SeparatorInset = new UIEdgeInsets (0, 50, 0, 0);
```

这会在表视图中生成缩进的单元格，如下所示：

 ![UITableView SeparatorInset 示例](ios7-ui-images/separatorinset.png)

#### <a name="table-button-styles"></a>表按钮样式

在表视图中使用的各种按钮都已更改。 以下屏幕截图显示了编辑模式下的表视图：

 ![此屏幕快照在编辑模式下显示了表视图](ios7-ui-images/table2.png)

### <a name="additional-control-changes"></a>其他控件更改

其他 UIKit 控件也已更改，包括滑块、开关和 steppers。 这些更改是纯粹的视觉对象。 有关详细信息，请参阅 Apple 的[iOS 7 UI 转换指南](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/TransitionGuide/index.html)。

## <a name="general-user-interface-changes"></a>常规用户界面更改

除了 UIKit 中的更改之外，iOS 7 还引入了对 UI 的各种视觉更改，包括：

- 全屏内容
- 栏外观
- 淡色颜色

<a name="fullscreen"></a>

### <a name="full-screen-content"></a>全屏内容

iOS 7 设计为允许应用程序利用整个屏幕。 视图控制器现在显示为与状态栏和导航栏重叠（如果有），而不是显示在状态和导航栏的下方。

为 iOS 7 准备应用程序时，可以使用*Interface Builder*或*Xamarin iOS 设计器*以可视方式对子视图进行重新对齐。 你还可以使用一个新的 Api 以编程方式处理全屏内容。 下面介绍了这些 Api。

#### <a name="toplayoutguide-and-bottomlayoutguide"></a>TopLayoutGuide 和 BottomLayoutGuide

 `TopLayoutGuide`和用作 `BottomLayoutGuide` 视图的开始或结束位置的参考，以便内容不会被半透明 `UIKit` 栏重叠，如以下示例中所示：

 [![不是由透明的 UIKit 栏重叠的示例内容](ios7-ui-images/clipped.png)](ios7-ui-images/clipped.png#lightbox)

这些 Api 可用于计算视图在屏幕顶部或底部的置换，并相应地调整内容位置：

```csharp
public override void ViewDidLayoutSubviews ()
{
    base.ViewDidLayoutSubviews ();

    if (UIDevice.CurrentDevice.CheckSystemVersion (7, 0)) { 
        nfloat displacement_y = this.TopLayoutGuide.Length;

        //load subviews with displacement
    }

}
```

我们可以使用以上计算得出的值 `ImageView` ，从屏幕顶部设置置换，使整个图像可见：

 [![屏幕顶部的 ImageViews 置换示例](ios7-ui-images/good2.png)](ios7-ui-images/good2.png#lightbox)

有关工作示例，请参阅[ImageViewer](https://docs.microsoft.com/samples/xamarin/ios-samples/ios7-ui-updates/) 。

在将视图添加到层次结构中后，会动态生成置换值，因此 `TopLayoutGuide` 在中尝试读取和 `BottomLayoutGuide` 值 `ViewDidLoad` 将返回0。 在加载视图之后计算值-例如，在中 `ViewDidLayoutSubviews` 。

> [!IMPORTANT]
> `TopLayoutGuide`和在 `BottomLayoutGuide` iOS 11 中已弃用，以支持新的安全区域布局。 Apple 规定，使用 safe 区域与 iOS 11 之前的 iOS 版本兼容。 有关详细信息，请参阅[更新适用于 iOS 的应用程序 11](~/ios/platform/introduction-to-ios11/updating-your-app/visual-design.md#fullscreen)指南。

#### <a name="edgesforextendedlayout"></a>EdgesForExtendedLayout

此 API 指定视图的哪些边缘应扩展到全屏，而不考虑半透明度的条形。 在 iOS 7 中，导航栏和工具栏显示在控制器视图上方，与以前的 iOS 版本不同，它们不占用相同空间。 IOS 7 照片应用程序演示了默认 `UIViewController.EdgesForExtendedLayout` 值 `UIRectEdge.All` 。 此设置用内容填充视图中的所有四个边缘，从而创建重叠和全屏效果：

 [![示例 EdgesForExtendedLayout](ios7-ui-images/photos.png)](ios7-ui-images/photos.png#lightbox)

点击图像会删除条形，并以全屏显示图像：

 [![删除了条带的 EdgesForExtendedLayout](ios7-ui-images/photos2.png)](ios7-ui-images/photos2.png#lightbox)

由于全屏内容是默认内容，因此，为 iOS 6 配置的应用程序将具有剪裁视图的一部分，如以下屏幕截图所示：

 [![为 iOS 6 配置的应用将包含已剪裁的视图的一部分，如以下屏幕截图所示](ios7-ui-images/clipped.png)](ios7-ui-images/clipped.png#lightbox)

修改 `UIViewController.EdgesForExtendedLayout` 属性将针对此行为进行调整。 我们可以指定视图不填充任何边缘，因此我们的视图将避免在导航或工具栏（每个方向）所占用的空间中显示内容：

```csharp
if (UIDevice.CurrentDevice.CheckSystemVersion (7, 0)) { 
    this.EdgesForExtendedLayout = UIRectEdge.None;
}
```

在我们的应用程序中，我们会看到视图再次重新定位，使整个图像可见：

 [![整个图像可见的示例](ios7-ui-images/good.png)](ios7-ui-images/good.png#lightbox)

请注意，虽然 `TopLayoutGuide/BottomLayoutGuide` 和 api 的作用 `EdgesForExtendedLayout` 类似，但它们的作用是用于填充不同的目标。 `EdgesForExtendedLayout`默认情况下，更改此设置可能会修复为 iOS 6 设计的应用程序中的剪辑视图，但良好的 iOS 7 设计应遵循全屏幕美观，并提供全屏查看体验，并依赖 `TopLayoutGuide` 并 `BottomLayoutGuide` 适当地将旨在操纵的内容定位到用户的舒适位置。

有关工作示例，请参阅[ImageViewer](https://docs.microsoft.com/samples/xamarin/ios-samples/ios7-ui-updates/) 。

### <a name="status-and-navigation-bars"></a>状态和导航栏

状态栏和导航栏是透明的。 状态栏是透明的，而工具栏和导航栏是半透明且模糊的，可以在用户界面中表达深度。 以下屏幕截图显示了这种模糊和透明度，其中，集合视图的蓝色背景色在状态和导航栏中显示，为其提供浅蓝色外观：

 ![示例状态和导航栏模糊](ios7-ui-images/transparent-navbar.png)

#### <a name="status-bar-styles"></a>状态栏样式

除了模糊和透明度，状态栏的前景可以是浅或深色（暗为默认值）。 状态栏样式可从视图控制器进行设置。 视图控制器还可以设置是隐藏还是显示状态栏。

例如，下面的代码将重写 `PreferredStatusBarStyle` 视图控制器的方法，使状态栏显示浅前景：

```csharp
public override UIStatusBarStyle PreferredStatusBarStyle ()
{
    return UIStatusBarStyle.LightContent;
}
```

这将导致状态栏显示如下：

 ![示例状态栏](ios7-ui-images/light-status-bar.png)

若要将状态栏从视图控制器的代码中隐藏，请重写 `PrefersStatusBarHidden` ，如下所示：

```csharp
public override bool PrefersStatusBarHidden ()
{
    return true;
}
```

这将隐藏状态栏：

 ![隐藏状态栏](ios7-ui-images/status-bar-hidden.png)

### <a name="tint-color"></a>淡色颜色

按钮现在显示为 chrome 文本。 可以使用中的新属性控制文本颜色 `TintColor` `UIView` 。 设置 `TintColor` 会将颜色应用于设置它的视图的整个视图层次结构。 若要在 `TintColor` 整个应用中应用，请在上进行设置 `Window` 。 还可以通过方法来检测色调颜色何时发生变化 `UIView.TintColorDidChange` 。

例如，以下屏幕截图显示了将导航控制器的视图上的色调颜色更改为紫色的效果：

 ![导航控制器视图上的紫色淡色颜色](ios7-ui-images/tint-color.png)

当设置为时，可以将淡色颜色应用于图像 `RenderingMode` `UIImageRenderingMode.AlwaysTemplate` 。

> [!IMPORTANT]
> 不能使用设置淡色颜色 `UIAppearance` 。

### <a name="dynamic-type"></a>动态类型

在 iOS 7 中，用户可以在系统设置中指定文本大小。 对于动态类型，会动态调整字体，使其看起来很好，而不考虑大小。 `UIFont.PreferredFontForTextStyle`应使用来获取为用户控制的大小优化的字体。

## <a name="summary"></a>摘要

本文介绍 iOS 7 中对用户界面元素所做的更改。 它将检查对 UIKit 中的视图和控件所做的几项更改，同时突出显示视觉对象更改以及相关 Api 更改。 最后，它引入了新的 Api 来处理全屏内容、新的淡色颜色支持和动态类型。

## <a name="related-links"></a>相关链接

- [ImageViewer （示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/ios7-ui-updates)
