---
title: 在 Xamarin 中使用 tvOS 堆积视图
description: 本文档介绍如何在使用 Xamarin 生成的应用程序中使用 tvOS 堆积视图。 它提供堆积视图的高级概述，并讨论自动布局、定位和调整堆积视图、常见用途、与情节提要的集成等。
ms.prod: xamarin
ms.assetid: 00B07F85-F30B-4DD4-8664-A61D0A1CDB0E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: b318f11e866c94ab1f5aa8f6b4f351ac88475ac3
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91437052"
---
# <a name="working-with-tvos-stacked-views-in-xamarin"></a>在 Xamarin 中使用 tvOS 堆积视图

堆栈视图控件 (`UIStackView`) 利用自动布局和大小类的功能来管理子视图的堆栈（水平或垂直），这会动态响应 APPLE TV 设备的内容更改和屏幕大小。

附加到堆栈视图的所有子视图的布局都是基于开发人员定义的属性（如轴、分布、对齐方式和间距）管理的：

[![子视图布局关系图](stacked-views-images/stacked01.png)](stacked-views-images/stacked01.png#lightbox)

`UIStackView`在 tvOS 应用中使用时，开发人员可以在 IOS 设计器中的情节提要内定义子视图，也可以通过在 c # 代码中添加和删除子视图来定义。

## <a name="about-stacked-view-controls"></a>关于堆积视图控件

`UIStackView`设计为非呈现容器视图，因此，它不像的其他子类一样绘制到画布 `UIView` 。 设置属性（如 `BackgroundColor` 或替代） `DrawRect` 将没有视觉效果。

有几个属性控制堆栈视图如何排列其子视图集合：

- **Axis** –确定堆栈视图是 **水平** 排列还是 **垂直**排列子视图。
- **对齐方式** –控制子视图在堆栈视图中的对齐方式。
- **分布** –控制子视图在堆栈视图中的大小。
- **间距** –控制堆栈视图中每个子视图之间的最小空间。
- **基线相对** -如果 `true` 为，则每个子视图的垂直间距都将从它的基线派生。
- **相对于布局边距** –相对于标准布局边距放置子视图。

通常，您将使用堆栈视图来排列少量的子视图。 可以通过将一个或多个堆栈视图彼此嵌套，来创建更复杂的用户界面。

可以通过将其他约束添加到子视图 (来进一步精细调整 Ui 外观，例如，) 控制高度或宽度。 但是，应注意不要为堆栈视图本身引入的约束包含冲突约束。

<a name="Auto-Layout-and-Size-Classes"></a>

## <a name="auto-layout-and-size-classes"></a>自动布局和大小类

如果将子视图添加到堆栈视图中，则该堆栈视图将完全控制其布局，并使用自动布局和大小类来定位和调整排列视图的大小。

堆栈视图将其集合中的第一个和最后一个子视图固定到垂直堆栈视图的**上**边缘和**下**边缘，或者_固定_到水平堆栈视图的**左边**缘和**右**边缘。 如果将属性设置 `LayoutMarginsRelativeArrangement` 为，则视图会将 `true` 子视图固定到相关边距，而不是边缘。

`IntrinsicContentSize`沿定义的 `Axis` (（) 除外）计算子视图大小时，堆栈视图将使用子视图的属性 `FillEqually Distribution` 。 `FillEqually Distribution`会调整所有子视图的大小，使其大小相同，从而沿显示堆栈视图 `Axis` 。

除了之外 `Fill Alignment` ，堆栈视图使用子视图的 `IntrinsicContentSize` 属性来计算视图与给定的垂直大小 `Axis` 。 对于 `Fill Alignment` ，所有子视图都将调整大小，以使其填充垂直于给定的堆栈视图 `Axis` 。

<a name="Positioning-and-Sizing-the-Stack-View"></a>

## <a name="positioning-and-sizing-the-stack-view"></a>定位堆栈视图并调整其大小

尽管 "堆栈" 视图对 (基于属性（如和) ）的所有子视图的布局具有完全控制 `Axis` `Distribution` ，但仍需使用 "自动布局" 和 "大小" 类将堆栈视图 (`UIStackView`) 置于其父视图中。

通常，这意味着至少固定堆栈视图的两个边缘来展开和收缩，从而定义其位置。 如果没有任何其他约束，堆栈视图将自动调整大小以适合其所有子视图，如下所示：

- 沿其大小 `Axis` 将为所有子视图大小加上在每个子视图之间定义的任何空间的总和。
- 如果 `LayoutMarginsRelativeArrangement` 属性为 `true` ，则堆栈视图大小还将包括边距空间。
- 垂直于的大小 `Axis` 将设置为集合中的最大子视图。

此外，还可以指定堆栈视图的 **高度** 和 **宽度**约束。 在这种情况下，子视图将布局 (大小) ，以填充由和属性确定的堆栈视图指定的空间 `Distribution` `Alignment` 。

如果 `BaselineRelativeArrangement` 属性为 `true` ，则将基于第一个或最后一个子视图的基线来布局子视图，而不是使用**上**、**下**或 **中心* -  **Y**位置。 这些是在堆栈视图的内容上计算的，如下所示：

- 垂直堆栈视图将返回第一个基线和最后一个基线的第一个子视图。 如果这两个子视图本身都是堆栈视图，则将使用其第一个或最后一个基线。
- 水平堆栈视图将为第一个和最后一个基线使用最高的子视图。 如果最高视图也是堆栈视图，它将使用最高的子视图作为基线。

> [!IMPORTANT]
> 基线对齐不适用于拉伸或压缩的子视图大小，因为基线将计算到错误的位置。 对于基线对齐，请确保子视图的 **高度** 与内部内容视图的 **高度**匹配。

<a name="Common-Stack-View-Uses"></a>

## <a name="common-stack-view-uses"></a>常见堆栈视图使用

有几种布局类型适用于堆栈视图控件。 根据 Apple，以下是一些更常见的用法：

- **沿轴定义大小** –将两个边缘沿堆栈视图 `Axis` 和一个相邻边缘固定以设置位置时，堆栈视图将沿轴增长以适应其子视图定义的空间。
- **定义子视图的位置** –通过将堆栈视图的相邻边缘固定到其父视图，堆栈视图将以这两个维度增长，以适合包含子视图。
- **定义堆栈的大小和位置** –通过将堆栈视图的所有四个边缘固定到父视图，堆栈视图会根据堆栈视图中定义的空间排列子视图。
- **定义垂直于轴的大小** –通过将垂直于堆栈视图的边缘 `Axis` 和沿轴的一个边缘固定到设置位置，堆栈视图将与轴垂直增长以适应其子视图定义的空间。

<a name="Stack-Views-and-Storyboards"></a>

## <a name="stack-views-and-storyboards"></a>堆栈视图和情节提要

在 tvOS 应用中使用堆栈视图的最简单方法是使用 iOS 设计器将它们添加到应用的 UI。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 **Solution Pad**中，双击 `Main.storyboard` 文件并将其打开以进行编辑。
1. 设计要添加到堆栈视图中的单个元素的布局：

    [![元素布局示例](stacked-views-images/layout01.png)](stacked-views-images/layout01.png#lightbox)
1. 向元素添加所需的任何约束，以确保它们能正确缩放。 将元素添加到堆栈视图后，此步骤非常重要。
1. 在这种情况下，请将所需数量的副本 (四) ：

    [![所需的副本数](stacked-views-images/layout02.png)](stacked-views-images/layout02.png#lightbox)
1. 将 " **堆栈" 视图** 从 " **工具箱** " 拖放到视图上：

    [![堆栈视图](stacked-views-images/layout03.png)](stacked-views-images/layout03.png#lightbox)
1. 选择 "堆栈" 视图，在 **"小组件" 选项** **Properties Pad**卡中，选择 "填充" 作为 "**对齐**" 的 "**填充**"，为**分布****均匀填充**，并输入 `25` **间距**：

    [!["小组件" 选项卡](stacked-views-images/layout04.png)](stacked-views-images/layout04.png#lightbox)
1. 将堆栈视图定位到所需的屏幕上，并添加约束，使其保持在所需的位置。
1. 选择各个元素，并将其拖到 "堆栈" 视图中：

    [![堆栈视图中的单个元素](stacked-views-images/layout05.png)](stacked-views-images/layout05.png#lightbox)
1. 将调整布局，并根据上面设置的属性将元素排列在堆栈视图中。
1. 在 "**属性资源管理器**" 的 "**小组件" 选项卡**中分配**名称**，以使用 c # 代码中的 UI 控件。
1. 保存所做更改。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 在 **解决方案资源管理器**中，双击 `Main.storyboard` 文件并将其打开以进行编辑。
1. 设计要添加到堆栈视图中的单个元素的布局：

    [![示例元素布局](stacked-views-images/layout01.png)](stacked-views-images/layout01.png#lightbox)
1. 向元素添加所需的任何约束，以确保它们能正确缩放。 将元素添加到堆栈视图后，此步骤非常重要。
1. 在这种情况下，请将所需数量的副本 (四) ：

    [![所需的副本数](stacked-views-images/layout02.png)](stacked-views-images/layout02.png#lightbox)
1. 将 " **堆栈" 视图** 从 " **工具箱** " 拖放到视图上：

    [![堆栈视图](stacked-views-images/layout03-vs.png)](stacked-views-images/layout03-vs.png#lightbox)
1. 选择 "堆栈" 视图，在 "**属性资源管理器**" 的 "**小组件" 选项卡**中，选择 "**填充**" 进行**对齐**，按**比例填充****分布**并输入 `25` **间距**：

    [!["小组件" 选项卡](stacked-views-images/layout04-vs.png)](stacked-views-images/layout04-vs.png#lightbox)
1. 将堆栈视图定位到所需的屏幕上，并添加约束，使其保持在所需的位置。
1. 选择各个元素，并将其拖到 "堆栈" 视图中：

    [![堆栈视图中的单个元素](stacked-views-images/layout05-vs.png)](stacked-views-images/layout05-vs.png#lightbox)
1. 将调整布局，并根据上面设置的属性将元素排列在堆栈视图中。
1. 在 "**属性资源管理器**" 的 "**小组件" 选项卡**中分配**名称**，以使用 c # 代码中的 UI 控件。
1. 保存所做更改。

-----

> [!IMPORTANT]
> 尽管可以在 `TouchUpInside` 创建事件处理程序时将操作（如 (如 `UIButton` IOS 设计器中的) ）分配给 UI 元素，但永远不会调用该操作，因为 Apple TV 没有触摸屏或支持触控事件。 `Action Type`为 tvOS 用户界面元素创建操作时，应始终使用默认值。

有关使用情节提要的详细信息，请参阅 [tvOS 快速入门指南](~/ios/tvos/get-started/hello-tvos.md)。

在我们的示例中，我们为每个 "播放机卡" 的段控件和插座公开了输出口和操作。 在代码中，我们将基于当前段隐藏和显示播放机。 例如：

```csharp
partial void PlayerCountChanged (Foundation.NSObject sender) {

    // Take Action based on the segment
    switch(PlayerCount.SelectedSegment) {
    case 0:
        Player1.Hidden = false;
        Player2.Hidden = true;
        Player3.Hidden = true;
        Player4.Hidden = true;
        break;
    case 1:
        Player1.Hidden = false;
        Player2.Hidden = false;
        Player3.Hidden = true;
        Player4.Hidden = true;
        break;
    case 2:
        Player1.Hidden = false;
        Player2.Hidden = false;
        Player3.Hidden = false;
        Player4.Hidden = true;
        break;
    case 3:
        Player1.Hidden = false;
        Player2.Hidden = false;
        Player3.Hidden = false;
        Player4.Hidden = false;
        break;
    }
}
```

当应用运行时，这四个元素将在堆栈视图中同样分布：

[![当应用程序运行时，这四个元素将在堆栈视图中同样分布](stacked-views-images/layout06.png)](stacked-views-images/layout06.png#lightbox)

如果玩家数量减少，则不使用的视图将隐藏，堆栈视图将布局调整为合适大小：

[![如果玩家数量减少，则隐藏未使用的视图，堆栈视图将布局调整为合适大小](stacked-views-images/layout07.png)](stacked-views-images/layout07.png#lightbox)

<a name="Populate-a-Stack-View-from-Code"></a>

### <a name="populate-a-stack-view-from-code"></a>从代码填充堆栈视图

除了在 iOS 设计器中完全定义堆栈视图的内容和布局外，还可以从 c # 代码动态创建和删除它。

采用以下示例，该示例使用堆栈视图来处理 "星星" (1 到 5) ：

```csharp
public int Rating { get; set;} = 0;
...

partial void IncreaseRating (Foundation.NSObject sender) {

    // Maximum of 5 "stars"
    if (++Rating > 5 ) {
        // Abort
        Rating = 5;
        return;
    }

    // Create new rating icon and add it to stack
    var icon = new UIImageView (new UIImage("icon.png"));
    icon.ContentMode = UIViewContentMode.ScaleAspectFit;
    RatingView.AddArrangedSubview(icon);

    // Animate stack
    UIView.Animate(0.25, ()=>{
        // Adjust stack view
        RatingView.LayoutIfNeeded();
    });

}

partial void DecreaseRating (Foundation.NSObject sender) {

    // Minimum of zero "stars"
    if (--Rating < 0) {
        // Abort
        Rating =0;
        return;
    }

    // Get the last subview added
    var icon = RatingView.ArrangedSubviews[RatingView.ArrangedSubviews.Length-1];

    // Remove from stack and screen
    RatingView.RemoveArrangedSubview(icon);
    icon.RemoveFromSuperview();

    // Animate stack
    UIView.Animate(0.25, ()=>{
        // Adjust stack view
        RatingView.LayoutIfNeeded();
    });
}
```

让我们详细了解一下此代码的几个部分。 首先，我们使用 `if` 语句来检查是否不超过五个 "星星" 或小于零。

若要添加新的 "星星"，请加载其图像，并将其 **内容模式** 设置为 " **缩放**"：

```csharp
var icon = new UIImageView (new UIImage("icon.png"));
icon.ContentMode = UIViewContentMode.ScaleAspectFit;
```

这会使 "星星" 图标在添加到堆栈视图时不会被扭曲。

接下来，将新的 "星星" 图标添加到堆栈视图的子视图集合：

```csharp
RatingView.AddArrangedSubview(icon);
```

你会注意到，我们将添加 `UIImageView` 到 `UIStackView` 的 `ArrangedSubviews` 属性而不是 `SubView` 。 您希望堆栈视图控制其布局的任何视图都必须添加到 `ArrangedSubviews` 属性中。

若要从堆栈视图中删除子视图，首先会获得要删除的子视图：

```csharp
var icon = RatingView.ArrangedSubviews[RatingView.ArrangedSubviews.Length-1];
```

接下来，需要从 `ArrangedSubviews` 集合和超级视图中删除它：

```csharp
// Remove from stack and screen
RatingView.RemoveArrangedSubview(icon);
icon.RemoveFromSuperview();
```

从集合中删除子视图 `ArrangedSubviews` 会将其从堆栈视图的控件中删除，但不会将其从屏幕上删除。

<a name="Dynamically-Changing-Content"></a>

## <a name="dynamically-changing-content"></a>动态更改内容

只要添加、删除或隐藏子视图，堆栈视图就会自动调整子视图的布局。 如果将堆栈视图的任何属性调整 (如) ，则还会调整布局 `Axis` 。

可以通过将布局更改放置在动画块中来对其进行动画处理，例如：

```csharp
// Animate stack
UIView.Animate(0.25, ()=>{
    // Adjust stack view
    RatingView.LayoutIfNeeded();
});
```

可以使用情节提要中的大小类指定许多堆栈视图的属性。 这些属性将自动进行动画处理，以响应大小或方向更改。

<a name="Summary"></a>

## <a name="summary"></a>总结

本文介绍了如何在 tvOS 应用内设计和使用堆叠式视图。

## <a name="related-links"></a>相关链接

- [tvOS 示例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人体学接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [TvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)