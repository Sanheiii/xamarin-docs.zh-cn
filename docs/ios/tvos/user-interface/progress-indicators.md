---
title: 在 Xamarin 中使用 tvOS 进度指示器
description: 本文档介绍如何在使用 Xamarin 生成的 tvOS 应用中处理进度指示器。 它讨论进度栏和活动指示器。
ms.prod: xamarin
ms.assetid: 582B6D0C-1F16-4299-A9A6-5651E76009FE
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/25/2018
ms.openlocfilehash: 4f79fe550966c36a2be15431757fb7443e090ec9
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436396"
---
# <a name="working-with-tvos-progress-indicators-in-xamarin"></a>在 Xamarin 中使用 tvOS 进度指示器

_本文介绍如何设计和使用 tvOS 应用程序中的进度指示器。_

有时，tvOS 应用需要加载新内容或执行漫长的处理操作。 在这段时间内，你应该显示活动指示器或进度栏，以让用户知道该应用仍在运行，并为其提供一些指示，指示正在运行的任务的长度。

![样本进度指示器](progress-indicators-images/intro01.png "样本进度指示器")

## <a name="about-activity-indicators"></a>关于活动指示器

活动指示器显示为旋转齿轮，用于表示不确定长度的任务。 当任务开始和任务完成时，将显示指示器。

Apple 对于使用活动指示器有以下建议：

- **请尽可能使用进度栏，** 因为活动指示器为用户提供了对运行进程所需时间的反馈，如果长度已知，请始终使用进度栏 (例如，要在文件) 中下载的字节数。
- **使指示器具有动画效果** -用户将静止活动指示器与停止应用相关联，因此，在显示指示器时，应始终对其进行动画处理。
- **描述正在处理的任务** -只显示活动指示器是不够的;用户需要知道他们正在等待的进程。 包含有意义的标签 (通常是一个完整的句子) ，它可明确定义任务。

## <a name="about-progress-bars"></a>关于进度栏

进度栏显示为一条线条，其中填充了颜色以指示耗时任务的状态和时间长度。 当任务的长度已知或可以计算时，应始终使用进度栏。

Apple 对于使用进度条有以下建议：

- **准确报告进度** -进度栏应始终准确表示完成任务所需的时间。 请不要将应用程序显示为忙的时间。
- 用于**定义完善的持续**时间-进度栏不仅应显示长时间的任务，还应为用户提供完成任务的时间和估计剩余时间。

## <a name="progress-indicators-and-storyboards"></a>进度指示器和情节提要

若要在 tvOS 应用中处理进度指示器，最简单的方法是使用 iOS 设计器将其添加到应用的 UI。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 **Solution Pad**中，双击 **主情节提要** 文件，然后将其打开进行编辑。

2. 从 "**工具箱**" 中将**活动指示器**拖放到视图上： 

    ![活动指示器](progress-indicators-images/activity01.png "活动指示器")

3. 在**Properties Pad**的 "**小组件**" 选项卡中，可以调整活动指示器的多个属性，如其**样式**、**行为**和**名称**： 

    ![活动指示器的 "小组件" 选项卡](progress-indicators-images/activity02.png "活动指示器的 "小组件" 选项卡")
    
    **名称**确定属性的名称，该属性表示 c # 代码中的活动指示器。

4. 从 "**工具箱**" 中拖动**进度视图**，并将其放置在视图上： 

    ![进度视图](progress-indicators-images/activity03.png "进度视图")

5. 在**属性资源管理器**的 "**小组件**" 选项卡中，可以调整进度视图的多个属性，例如其**样式**、**进度** (完成百分比) 和**名称**： 

    ![进度视图的 "小组件" 选项卡](progress-indicators-images/activity04.png "进度视图的 "小组件" 选项卡")
    
    **名称**确定属性的名称，该属性表示 c # 代码中的进度视图。

6. 保存所做更改。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 在 **解决方案资源管理器**中，双击 **主情节提要** 文件，然后将其打开进行编辑。

2. 从 "**工具箱**" 中将**活动指示器**拖放到视图上： 

    ![活动指示器](progress-indicators-images/activity01-vs.png
    "活动指示器")

3. 在 "**属性资源管理器**" 的 "**小组件**" 选项卡中，可以调整活动指示器的多个属性，如其**样式**、**行为**和**名称**： 

    ![活动指示器的 "小组件" 选项卡](progress-indicators-images/activity02-vs.png "活动指示器的 "小组件" 选项卡")

    **名称**确定属性的名称，该属性表示 c # 代码中的活动指示器。

4. 从 "**工具箱**" 中拖动**进度视图**，并将其放置在视图上： 

   ![进度视图](progress-indicators-images/activity03-vs.png "进度视图")

5. 在**属性资源管理器**的 "**小组件**" 选项卡中，可以调整进度视图的多个属性，例如其**样式**、**进度** (完成百分比) 和**名称**： 

    ![进度视图的 "小组件" 选项卡](progress-indicators-images/activity04-vs.png "进度视图的 "小组件" 选项卡")
    
    **名称**确定属性的名称，该属性表示 c # 代码中的进度视图。

6. 保存所做更改。

-----

有关使用情节提要的详细信息，请参阅 [tvOS 快速入门指南](~/ios/tvos/get-started/hello-tvos.md)。 

## <a name="working-with-activity-indicators"></a>使用活动指示器

如上所述，当应用运行不确定长度的长进程时，应显示活动指示器。

在任何时候，都可以通过检查活动指示器的属性来查看活动指示器是否已进行动画处理 `IsAnimating` 。 如果 `HidesWhenStopped` 属性为 `true` ，则活动指示器在其动画停止后会自动隐藏。

你可以使用以下代码来启动动画： 

```csharp
ActivityIndicator.StartAnimating();
```

以下操作将停止动画：

```csharp
ActivityIndicator.StopAnimating();
```

> [!NOTE]
> 这些代码段假定在 iOS 设计器的 "**小组件**" 选项卡中，活动指示器的**名称**设置为**ActivityIndicator** 。

## <a name="working-with-progress-bars"></a>使用进度栏

同样，在应用程序执行已知持续时间长的长时间运行的任务时，应使用进度栏。 

`Progress`属性用于将已完成的任务量从0% 设置为 100% (0.0 以 1.0) 。 使用 `ProgressTintColor` 属性可设置 "已完成空间量" 栏的颜色和 `TrackTintColor` 属性，以将背景色设置 (未完成数量) 。

## <a name="summary"></a>总结

本文介绍了如何设计和使用 tvOS 应用程序中的进度指示器。

## <a name="related-links"></a>相关链接

- [tvOS 示例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人体学接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [TvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)