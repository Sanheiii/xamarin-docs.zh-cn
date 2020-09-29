---
title: Xamarin 中的堆栈视图
description: 本文介绍如何在 Xamarin iOS 应用中使用新的 UIStackView 控件来管理水平或垂直排列的堆栈中的一组子视图。
ms.prod: xamarin
ms.assetid: 20246E87-2A49-438A-9BD7-756A1B50A617
ms.technology: xamarin-ios
ms.custom: xamu-video
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: a926ae3aa91b7c1dbe13ba78c28f4df63228e151
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436496"
---
# <a name="stack-views-in-xamarinios"></a>Xamarin 中的堆栈视图

_本文介绍如何在 Xamarin iOS 应用中使用新的 UIStackView 控件来管理水平或垂直排列的堆栈中的一组子视图。_

> [!IMPORTANT]
> 请注意，尽管在 iOS 设计器中支持 System.windows.forms.toolstrip.stackview，但使用稳定通道时可能会遇到可用性错误。 切换 Beta 版或 Alpha 通道应能缓解此问题。 我们决定在稳定通道中实现所需的修补程序之前，使用 Xcode 提供本演练。

堆栈视图控件 (`UIStackView`) 利用自动布局和大小类的功能来管理子视图的堆栈（水平或垂直），这会动态响应 iOS 设备的方向和屏幕大小。

附加到堆栈视图的所有子视图的布局都是基于开发人员定义的属性（如轴、分布、对齐方式和间距）管理的：

[![堆栈视图布局关系图](uistackview-images/stacked01.png)](uistackview-images/stacked01.png#lightbox)

`UIStackView`在 Xamarin ios 应用中使用时，开发人员可以在 Ios 设计器中定义子视图，也可以通过在 c # 代码中添加和删除子视图来定义。

本文档由两部分组成：一个快速入门，可帮助你实现第一个堆栈视图，然后再了解更多有关其工作原理的技术详细信息。

> [!VIDEO https://youtube.com/embed/p3po6507Ip8]

**UIStackView 视频**

## <a name="uistackview-quickstart"></a>UIStackView 快速入门

作为控件的快速介绍 `UIStackView` ，我们将创建一个简单的接口，该接口允许用户输入1到5之间的评级。 我们将使用两个堆栈视图：一个用于在设备屏幕上垂直排列接口，另一个用于在屏幕上水平排列1-5 评分图标。

### <a name="define-the-ui"></a>定义 UI

启动新的 Xamarin iOS 项目，并编辑 Xcode 的 Interface Builder 中的 **主情节提要** 文件。 首先，在**视图控制器**上拖动单个**垂直堆栈视图**：

[![在视图控制器上拖动单个垂直堆栈视图](uistackview-images/quick01.png)](uistackview-images/quick01.png#lightbox)

在 " **属性检查器**" 中，设置以下选项：

[![设置堆栈视图选项](uistackview-images/quick02.png)](uistackview-images/quick02.png#lightbox)

其中：

- **Axis** –确定堆栈视图是 **水平** 排列还是 **垂直**排列子视图。
- **对齐方式** –控制子视图在堆栈视图中的对齐方式。
- **分布** –控制子视图在堆栈视图中的大小。
- **间距** –控制堆栈视图中每个子视图之间的最小空间。
- **基线相对** –如果选中，则每个子视图的垂直间距都将从它的基线派生。
- **相对于布局边距** –相对于标准布局边距放置子视图。

使用 Stack 视图时，可以将 **对齐方式** 视为子视图的 **X** 和 **Y** 位置，并将 **分布** 视为 **高度** 和 **宽度**。

> [!IMPORTANT]
> `UIStackView` 设计为非渲染容器视图，因此，它不像的其他子类一样绘制到画布 `UIView` 。 因此设置属性（如 `BackgroundColor` 或替代） `DrawRect` 将没有视觉效果。

通过添加标签、ImageView、两个按钮和水平堆栈视图来继续布局应用的界面，使其类似于以下内容：

[![布局堆栈视图 UI](uistackview-images/quick03.png)](uistackview-images/quick03.png#lightbox)

配置具有以下选项的水平堆栈视图：

[![配置水平堆栈视图选项](uistackview-images/quick04.png)](uistackview-images/quick04.png#lightbox)

由于我们不希望在将分级中的每个 "点" 添加到 "水平堆栈" 视图时对该图标进行拉伸，因此我们已将 **对齐方式** 设置为 " **中心** "，将 " **分布** " 设置为 " **均匀填充**"。

最后，将以下 **输出口** 和 **操作**连接到一起：

[![堆栈视图的插座和操作](uistackview-images/quick05.png)](uistackview-images/quick05.png#lightbox)

### <a name="populate-a-uistackview-from-code"></a>填充代码中的 UIStackView

返回到 Visual Studio for Mac 并编辑 **ViewController.cs** 文件，并添加以下代码：

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

### <a name="testing-the-ui"></a>测试 UI

如果已准备好所有必需的 UI 元素和代码，现在可以运行和测试接口。 当显示 UI 时，垂直堆栈视图中的所有元素都将从上到下均匀地分布。

当用户点击 " **增加评分** " 按钮时，会在屏幕上添加另一个 "星星" (最多 5) ：

[![示例应用运行](uistackview-images/intro01.png)](uistackview-images/intro01.png#lightbox)

"星号" 将在水平堆栈视图中自动居中且均匀分布。 当用户点击 " **降低评分** " 按钮时， (会删除 "星"，直到没有) 。

## <a name="stack-view-details"></a>堆栈视图详细信息

现在，我们已经大致了解了控件的定义 `UIStackView` 及其工作原理，接下来让我们进一步了解一下它的一些功能和细节。

### <a name="auto-layout-and-size-classes"></a>自动布局和大小类

如上所述，将子视图添加到堆栈视图时，该堆栈视图将完全控制其布局，使用自动布局和大小类来定位和调整排列视图的大小。

堆栈视图将其集合中的第一个和最后一个子视图固定到垂直堆栈视图的**上**边缘和**下**边缘，或者_固定_到水平堆栈视图的**左边**缘和**右**边缘。 如果将属性设置 `LayoutMarginsRelativeArrangement` 为，则视图会将 `true` 子视图固定到相关边距，而不是边缘。

`IntrinsicContentSize`沿定义的 `Axis` (（) 除外）计算子视图大小时，堆栈视图将使用子视图的属性 `FillEqually Distribution` 。 `FillEqually Distribution`会调整所有子视图的大小，使其大小相同，从而沿显示堆栈视图 `Axis` 。

除了之外 `Fill Alignment` ，堆栈视图使用子视图的 `IntrinsicContentSize` 属性来计算视图与给定的垂直大小 `Axis` 。 对于 `Fill Alignment` ，所有子视图都将调整大小，以使其填充垂直于给定的堆栈视图 `Axis` 。

### <a name="positioning-and-sizing-the-stack-view"></a>定位堆栈视图并调整其大小

尽管 "堆栈" 视图对 (基于属性（如和) ）的所有子视图的布局具有完全控制 `Axis` `Distribution` ，但仍需使用 "自动布局" 和 "大小" 类将堆栈视图 (`UIStackView`) 置于其父视图中。

通常，这意味着至少固定堆栈视图的两个边缘来展开和收缩，从而定义其位置。 如果没有任何其他约束，堆栈视图将自动调整大小以适合其所有子视图，如下所示：

- 沿其大小 `Axis` 将为所有子视图大小加上在每个子视图之间定义的任何空间的总和。
- 如果 `LayoutMarginsRelativeArrangement` 属性为 `true` ，则堆栈视图大小还将包括边距空间。
- 垂直于的大小 `Axis` 将设置为集合中的最大子视图。

此外，还可以指定堆栈视图的 **高度** 和 **宽度**约束。 在这种情况下，子视图将布局 (大小) ，以填充由和属性确定的堆栈视图指定的空间 `Distribution` `Alignment` 。

如果 `BaselineRelativeArrangement` 属性为 `true` ，则将基于第一个或最后一个子视图的基线，而不是使用 "**上**"、"**下**" 或 "**中心**" -  **Y**位置来布局子视图。 这些是在堆栈视图的内容上计算的，如下所示：

- 垂直堆栈视图将返回第一个基线和最后一个基线的第一个子视图。 如果这两个子视图本身都是堆栈视图，则将使用其第一个或最后一个基线。
- 水平堆栈视图将为第一个和最后一个基线使用最高的子视图。 如果最高视图也是堆栈视图，它将使用最高的子视图作为基线。

> [!IMPORTANT]
> 基线对齐不适用于拉伸或压缩的子视图大小，因为基线将计算到错误的位置。 对于基线对齐，请确保子视图的 **高度** 与内部内容视图的 **高度**匹配。

### <a name="common-stack-view-uses"></a>常见堆栈视图使用

有几种布局类型适用于堆栈视图控件。 根据 Apple，以下是一些更常见的用法：

- **沿轴定义大小** –将两个边缘沿堆栈视图 `Axis` 和一个相邻边缘固定以设置位置时，堆栈视图将沿轴增长以适应其子视图定义的空间。
- **定义子视图的位置** –通过将堆栈视图的相邻边缘固定到其父视图，堆栈视图将以这两个维度增长，以适合包含子视图。
- **定义堆栈的大小和位置** –通过将堆栈视图的所有四个边缘固定到父视图，堆栈视图会根据堆栈视图中定义的空间排列子视图。
- **定义垂直于轴的大小** –通过将垂直于堆栈视图的边缘 `Axis` 和沿轴的一个边缘固定到设置位置，堆栈视图将与轴垂直增长以适应其子视图定义的空间。

### <a name="managing-the-appearance"></a>管理外观

`UIStackView`设计为非呈现容器视图，因此，它不像的其他子类一样绘制到画布 `UIView` 。 设置属性（如 `BackgroundColor` 或替代） `DrawRect` 将没有视觉效果。

有几个属性控制堆栈视图如何排列其子视图集合：

- **Axis** –确定堆栈视图是 **水平** 排列还是 **垂直**排列子视图。
- **对齐方式** –控制子视图在堆栈视图中的对齐方式。
- **分布** –控制子视图在堆栈视图中的大小。
- **间距** –控制堆栈视图中每个子视图之间的最小空间。
- **基线相对** -如果 `true` 为，则每个子视图的垂直间距都将从它的基线派生。
- **相对于布局边距** –相对于标准布局边距放置子视图。

通常，您将使用堆栈视图来排列少量的子视图。 可以通过 [) 将一个](#uistackview-quickstart) 或多个堆栈视图嵌套在彼此之间 (来创建更复杂的用户界面。

可以通过将其他约束添加到子视图 (来进一步精细调整 Ui 外观，例如，) 控制高度或宽度。 但是，应注意不要为堆栈视图本身引入的约束包含冲突约束。

### <a name="maintaining-arranged-views-and-sub-views-consistency"></a>维护排列视图和子视图的一致性

堆栈视图将 `ArrangedSubviews` 使用以下规则确保其属性始终是其属性的子集 `Subviews` ：

- 如果将子视图添加到 `ArrangedSubviews` 集合中，则该子视图将自动添加到 `Subviews` 集合 (，除非它已经是该集合) 的一部分。
- 如果从 () 移除的集合中移除子视图 `Subviews` ，则它也会从集合中删除 `ArrangedSubviews` 。
- 从集合中删除子视图 `ArrangedSubviews` 不会将其从集合中删除 `Subviews` 。 这样，堆栈视图将不再布局，但仍会显示在屏幕上。

`ArrangedSubviews`集合始终是集合的一个子集 `Subview` ，但是每个集合中的各个子视图的顺序是由以下各项分隔和控制的：

- 集合中子视图的顺序决定了 `ArrangedSubviews` 它们在堆栈中的显示顺序。
- 集合中子视图的顺序 `Subview` 决定了其在视图中的 Z 顺序 (或分层) 返回到前台。

### <a name="dynamically-changing-content"></a>动态更改内容

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

## <a name="summary"></a>总结

本文介绍了 `UIStackView` 适用于 ios 9 的新控件 () 在 Xamarin iOS 应用中的水平或垂直排列的堆栈中管理一组子视图。
它首先是一个简单的示例，说明如何使用堆栈视图创建 UI，并已完成堆栈视图及其属性和功能的详细介绍。

## <a name="related-links"></a>相关链接

- [iOS 9 示例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS9)
- [适用于开发人员的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [IOS 9.0 中的新增功能](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [UIStackView 引用](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIStackView_Class_Reference/)
- [介绍 UIStackView (视频) ](https://university.xamarin.com/lightninglectures/introducing-uistackview-on-ios9)