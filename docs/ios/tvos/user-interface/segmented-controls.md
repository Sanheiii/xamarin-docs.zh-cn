---
title: 在 Xamarin 中使用 tvOS 分段控件
description: 本文档介绍如何在使用 Xamarin 生成的应用程序中使用 tvOS 分段控件。 它讨论了段图标和文本、事件、修改控件的外观等内容。
ms.prod: xamarin
ms.assetid: 23AD94CC-E93A-40B1-8E2B-ECD21FA355BE
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 47530b27fb754bf2efd50615fef9dfc558d9c7d2
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436195"
---
# <a name="working-with-tvos-segmented-controls-in-xamarin"></a>在 Xamarin 中使用 tvOS 分段控件

分段控件提供一组线性元素，每个元素都可以包含一个图标或文本，并用于向用户提供一组相关选项。

[![示例段控件](segmented-controls-images/segment01.png)](segmented-controls-images/segment01.png#lightbox)

Apple 对于使用分段控件有以下建议：

- **提供充足的空间** 以在其他可 [设定焦点的项](~/ios/tvos/app-fundamentals/navigation-focus.md) 和分段控件之间提供充足的空间。 当某个段处于焦点 (而不是在被单击时，它将变为选中状态) 并且当用户实际想在当前段上选择另一个可获得焦点的项时，它可能会意外更改段。
- **使用拆分视图进行内容筛选** -分段控件对于内容筛选不作好选择，因为拆分视图旨在方便在内容和筛选器之间导航。
- **最大值限制为七个段** -应尝试将最大段数保持在八个 (8) 以下，因为这样可以更轻松地在沙发上跨房间进行分析并更易于导航。
- **使用一致段内容大小** -所有段都具有相同的宽度，并且如果可能，应尝试将每个段中的内容保持为相同大小。 这不仅会使段控件更直观地显示，而且便于更轻松地阅读。
- **避免混合使用图标和文本** -每个单独段可以包含图标或文本，但不能同时包含两者。 尽管可以在同一分段控件中混合使用图标和文本，但应避免这样做。

<a name="About-Segment-Icons"></a>

## <a name="about-segment-icons"></a>关于段图标

Apple 建议对段图标使用简单、可识别的图像，例如用于搜索的放大镜。 过于复杂的图标在电视屏幕上难以识别，因此最好将图标限制为简单表示形式。

不能同时在给定段上混合文本和图标，应避免在单个分段控件中混合使用图标和文本。 它应该是所有图标或所有文本。

<a name="Summary"></a>

## <a name="segment-text"></a>分段文本

Apple 提出以下有关使用段文本的建议：

- **使用简短、有意义的名词** -段标题应清楚地说明用户在选择给定段时应期望的内容类型。 例如：音乐或视频。
- **使用标题大小写大写** -段标题的每个单词都应大写，但项目、连词和介词少于四个 (4) 个字符。
- **使用简短的聚焦标题** -在选择段时，保留标题、简短并专注于所需的内容类型。

同样，您不能同时在给定段上混合文本和图标，应避免在单个分段控件中混合使用图标和文本。

<a name="Segment-Controls-and-Storyboards"></a>

## <a name="segment-controls-and-storyboards"></a>段控件和情节提要

在 tvOS 应用中处理段控件的最简单方法是使用 iOS 设计器将它们添加到应用的 UI。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 **Solution Pad**中，双击该文件， `Main.storyboard` 然后将其打开进行编辑。
1. 将 " **段" 控件** 从 " **工具箱** " 拖放到视图上： 

    [![段控件](segmented-controls-images/segment02.png)](segmented-controls-images/segment02.png#lightbox)
1. 在**属性板**的 "**小组件" 选项卡**中，可以调整段控件的多个属性，例如其**样式**和**状态**： 

    [!["小组件" 选项卡](segmented-controls-images/segment03.png)](segmented-controls-images/segment03.png#lightbox)
1. 使用 " **段** " 字段可以控制控制器中段的数量。
1. 从 " **段" 下拉列表** 中选择给定段以调整其各个属性（如 **标题** 或 **图像** ），并控制在显示控件时是 **启用** 还是 **选中** 某个给定段。
1. 最后，为控件分配 **名称** ，以便可以在 c # 代码中对其进行响应。 例如： 

    [![分配名称](segmented-controls-images/segment04.png)](segmented-controls-images/segment04.png#lightbox)
1. 保存所做更改。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 在 **解决方案资源管理器**中，双击该文件， `Main.storyboard` 然后将其打开进行编辑。
1. 将 " **段" 控件** 从 " **工具箱** " 拖放到视图上： 

    [![段控件](segmented-controls-images/segment02-vs.png)](segmented-controls-images/segment02-vs.png#lightbox)
1. 在**属性资源管理器**的 "**小组件" 选项卡**中，可以调整段控件的多个属性，例如其**样式**和**状态**： 

    [!["小组件" 选项卡](segmented-controls-images/segment03-vs.png)](segmented-controls-images/segment03-vs.png#lightbox)
1. 使用 " **段** " 字段可以控制控制器中段的数量。
1. 从 " **段" 下拉列表** 中选择给定段以调整其各个属性（如 **标题** 或 **图像** ），并控制在显示控件时是 **启用** 还是 **选中** 某个给定段。
1. 最后，为控件分配 **名称** ，以便可以在 c # 代码中对其进行响应。 例如： 

    [![分配名称](segmented-controls-images/segment04-vs.png)](segmented-controls-images/segment04-vs.png#lightbox)
1. 保存所做更改。

-----

有关使用情节提要的详细信息，请参阅 [tvOS 快速入门指南](~/ios/tvos/get-started/hello-tvos.md)。 

<a name="Working-with-Segmented-Controls"></a>

## <a name="working-with-segmented-controls"></a>使用分段控件

如上所述，s 分段控件提供一组线性元素，其中每个元素都可以包含一个图标或文本，并用于向用户提供一组相关选项。

可以通过多种不同的方式在 tvOS 应用程序中使用分段控件。

<a name="Exposed-as-Outlets-and-Actions"></a>

## <a name="exposed-as-names-and-events"></a>作为名称和事件公开

如果在接口设计器中创建了段控件，并将其作为命名控件和事件公开，则可以使用以下代码来响应该段变化：

```csharp
partial void PlayerCountChanged (Foundation.NSObject sender) {

    // Take action based on the number of players selected
    switch(PlayerCount.SelectedSegment) {
    case 0:
        // Do something if the segment is selected
        ...
        break;
    case 1:
        // Do something if the segment is selected
        ...
        break;
    case 2:
        // Do something if the segment is selected
        ...
        break;
    }
}
```

对于上述示例，段控件作为 `PlayerCount` 名称和 `PlayerCountChanged` 事件操作公开。 有关使用操作和插座的详细信息，请参阅[Hello，tvOS 快速入门指南](~/ios/tvos/get-started/hello-tvos.md)中的[使用插座和操作编写代码](~/ios/tvos/get-started/hello-tvos.md#Writing-the-Code)部分。

`SelectedSegment`属性获取或设置当前选定的段为零 (0) 的索引。 因此，如果有五个 (5) 段，则第一个段的索引为零 (0) ，最后一个索引为四个 (4) 。

<a name="Modifying-Segments"></a>

## <a name="modifying-segments"></a>修改段

随时都可以修改分段控件的数量和内容。 使用以下代码插入新的图标段：

```csharp
// Icon Segment
SegmentedControl.InsertSegment(UIImage.FromFile("icon.png"), 0, true);

// Text Segment
SegmentedControl.InsertSegment("New Segment", 0, true);
```

第二个参数定义使用零 (0) 的索引插入段的位置。 如果最后一个参数为，则 `true` 将对插入进行动画处理。

若要删除给定段，请使用以下内容：

```csharp
SegmentedControl.RemoveSegmentAtIndex(0, true);
```

或以下各项以删除所有段：

```csharp
SegmentedControl.RemoveAllSegments();
```

同样，如果最后一个参数为 `true` ，则将对删除进行动画处理。 使用 `NumberOfSegments` 属性返回当前段数。

若要获取给定段的 **标题** 或 **图标** ，请使用以下内容：

```csharp
// Get title
var title = SegmentedControl.TitleAt(0);

// Get icon
var icon = SegmentedControl.ImageAt(0);
```

若要更改 **标题** 或 **图标**，请使用以下内容：

```csharp
// Set title
SegmentedControl.SetTitle("New Title", 0);

// Set icon
SegmentedControl.SetImage(UIImage.FromFile("icon.png"), 0);
```

若要查看是否 **已启用**给定段，请使用以下内容：

```csharp
if (SegmentedControl.IsEnabled(0)) {
    // Do something
    ...
}
```

若要 **启用/禁用** 给定段，请使用以下内容：

```csharp
SegmentedControl.SetEnabled(false, 0);
```

<a name="Modifying-the-Segmented-Controls-Appearance"></a>

## <a name="modifying-the-segmented-controls-appearance"></a>修改分段控件的外观

你可以使用以下代码将给定段的背景更改为图像：

```csharp
SegmentedControl.SetBackgroundImage (UIImage.FromFile("background.png"), UIControlState.Normal, UIBarMetrics.Default);
```

其中 `UIControlState` 指定为其设置图像的控件的状态：

- 普通
- Highlighted
- 已禁用
- 已选定
- 已设定焦点

并 `UIBarMetrics` 指定要使用的度量值：

- 默认
- 精简
- DefaultPrompt
- CompactPrompt

此外，还可以使用以下内容设置段间的分隔线：

```csharp
SegmentedControl.SetDividerImage (UIImage.FromFile("divider.png"), UIControlState.Normal, UIControlState.Normal, UIBarMetrics.Default);
```

其中，第一个 `UIControlState` 指定分隔符左侧的段的状态，第二个指定线段的 `UIControlState` 状态。

<a name="Summary"></a>

## <a name="summary"></a>总结

本文介绍了如何在 tvOS 应用内设计和使用分段控件。

## <a name="related-links"></a>相关链接

- [tvOS 示例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人体学接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [TvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)