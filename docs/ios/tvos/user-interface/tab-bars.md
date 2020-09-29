---
title: 在 Xamarin 中使用 tvOS 选项卡栏控制器
description: 本文档介绍如何在使用 Xamarin 生成的 tvOS 应用中使用选项卡栏控制器。 它提供高级别的选项卡栏视图，并讨论选项卡栏项、情节提要集成和选项卡栏项。
ms.prod: xamarin
ms.assetid: 99A2D7C6-0324-4DE5-B6E9-D39D0BAD8370
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 813a4de1966aa53e9c1447804deda8f55e5672f3
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430286"
---
# <a name="working-with-tvos-tab-bar-controllers-in-xamarin"></a>在 Xamarin 中使用 tvOS 选项卡栏控制器

对于许多类型的 tvOS 应用，主导航显示为在屏幕顶部运行的选项卡栏。 用户 swipes 可能的类别列表和更改下方的内容区域，以反映用户的选择。

[![示例选项卡栏](tab-bars-images/tab01.png)](tab-bars-images/tab01.png#lightbox)

默认情况下，选项卡栏是透明的，并且始终显示在屏幕的顶部。 当焦点位于焦点上方时，选项卡栏将覆盖屏幕的前140像素，但当焦点移到下方的内容区域时，它将迅速滑出。

<a name="Tab-Bars-in-tvOS"></a>

## <a name="tab-bars-in-tvos"></a>TvOS 中的选项卡栏

的 `UITabViewController` 工作方式类似，在 tvOS 上的工作方式与在 iOS 中的作用类似，但有以下主要区别：

- 不同于 iOS 上显示在屏幕底部的选项卡栏，tvOS 中的选项卡栏占用屏幕的顶部140像素，默认情况下为半透明。
- 当焦点离开下面的内容区域的选项卡栏时，选项卡栏将快速滑出屏幕的顶部并隐藏。 用户可以点击 "菜单" 按钮一次，或在 [Siri 遥控器](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote) 上向上轻扫以显示选项卡栏。
- 在 Siri 遥控器上向下轻扫会将焦点移到选项卡下面的内容区域，以显示内容中的第一个可获得 [焦点的项](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection) 。 同样，这将会在焦点移位后隐藏选项卡栏。
- 单击以选择显示在选项卡栏中的类别将切换到该类别的内容，焦点将切换到该视图中的第一个可设定焦点的项目。
- 选项卡栏中显示的类别数应为固定，并且所有类别都应始终都可访问，因此绝不应禁用给定的类别。
- 选项卡栏不支持对 tvOS 的自定义。 此外，如果不能在选项卡栏中容纳更多类别，它们将不会显示 (（例如 iOS） **更多** 的类别) 。

Apple 对于使用选项卡栏有以下建议：

- **使用选项卡栏来以逻辑方式组织内容** -使用选项卡栏以逻辑方式组织 tvOS 应用使用的内容。 例如，特色、热门图表、购买和搜索。
- **添加徽章以通知用户新内容** -你可以选择将徽章标记 (带有白数值或惊叹号) 红色椭圆，以通知用户类别中存在新内容。
- **谨慎使用徽章** -不要使选项卡栏与徽章混乱，只显示他们向用户提供重要信息的位置。
- **限制类别数** -为了降低复杂性并使应用程序保持可管理性，请不要将选项卡栏重载为类别，并确保所有类别都可见且不太拥挤。 简单、简短标题的效果最佳。
- **不要禁用类别** -所有选项卡 (类别) 应始终始终可见并启用。 如果给定的选项卡没有内容，请提供用户的说明。 例如，如果用户未购买，则 "购买" 选项卡将为空。

<a name="Tab-Bar-Items"></a>

## <a name="tab-bar-items"></a>选项卡栏项

选项卡栏中的每个类别 (选项卡) 由) 的选项卡项 (表示 `UITabBarItem` 。 Apple 对于使用选项卡栏项有以下建议：

- **使用基于文本的选项** 卡-当选项卡栏项可以表示为图标时，Apple 建议仅使用文本，因为简明标题比图标更容易理解。
- **使用简短、有意义的名词或动词** -选项卡栏项应清楚地中继其包含的内容，并且当它是一个简单名词 (（如照片、电影或音乐) 或动词 (如 "搜索" 或 "播放") ）时最适合。

<a name="Tab-Bars-and-Storyboards"></a>

## <a name="tab-bars-and-storyboards"></a>选项卡栏和情节提要

在 tvOS 应用中使用选项卡栏的最简单方法是使用 iOS 设计器将它们添加到应用的 UI。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 启动新的 tvOS 应用，并选择**tvOS**  >  **应用**  >  **选项卡式应用**： 

    [![选择选项卡式应用](tab-bars-images/tab02.png)](tab-bars-images/tab02.png#lightbox)
1. 按照所有提示创建新的 tvOS 解决方案。
1. 在 **Solution Pad**中，双击该文件， `Main.storyboard` 然后将其打开进行编辑。
1. 若要更改给定类别的**图标**或**标题**，请在**文档大纲**中选择**视图控制器**的**选项卡项**：

    [![文档大纲中视图控制器的选项卡项](tab-bars-images/tab03a.png)](tab-bars-images/tab03a.png#lightbox)
1. 然后在**属性资源管理器**的 "**小组件" 选项卡**中设置所需的属性： 

    [!["小组件" 选项卡](tab-bars-images/tab03.png)](tab-bars-images/tab03.png#lightbox)
1. 若要)  (选项卡中添加新类别，请将 **视图控制器** 拖到设计图面上： 

    [![视图控制器](tab-bars-images/tab04.png)](tab-bars-images/tab04.png#lightbox)
1. 按住 ctrl 并从 **选项卡视图控制器** 拖动到新的 **视图控制器**。
1. 从弹出菜单中，选择 " **视图控制器** " 以将新视图作为选项卡添加 (类别) ： 

    [![选择选项卡](tab-bars-images/tab05.png)](tab-bars-images/tab05.png#lightbox)
1. 通过在 iOS 设计器中添加 UI 元素，以常规方式为每个 Caterogies 内容区域设计 UI 布局。
1. 公开任何所需的事件以使用 c # 代码中的 UI 控件。
1. 命名要在 c # 代码中公开的任何 UI 控件。
1. 保存所做更改。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 启动新的 tvOS 应用，并选择**tvOS**  >  **应用**  >  **选项卡式应用**： 

    [![选择选项卡式应用](tab-bars-images/tab02vs.png)](tab-bars-images/tab02vs.png#lightbox)
1. 按照所有提示创建新的 tvOS 解决方案。
1. 在 **解决方案资源管理器**中，双击该文件， `Main.storyboard` 然后将其打开进行编辑。
1. 若要更改给定类别的**图标**或**标题**，请在**文档大纲**中选择**视图控制器**的**选项卡项**：

    [![文档大纲中的视图控制器](tab-bars-images/tab03avs.png)](tab-bars-images/tab03avs.png#lightbox)
1. 然后在**属性资源管理器**的 "**小组件" 选项卡**中设置所需的属性： 

    [!["小组件" 选项卡](tab-bars-images/tab03vs.png)](tab-bars-images/tab03vs.png#lightbox)
1. 若要)  (选项卡中添加新类别，请将 " **视图控制器** " 从 " **工具箱** " 拖放到设计图面上： 

    [![视图控制器](tab-bars-images/tab04vs.png)](tab-bars-images/tab04vs.png#lightbox)
1. 按住 ctrl 并从 **选项卡视图控制器** 拖动到新的 **视图控制器**。
1. 从弹出菜单中，选择 " **视图控制器** " 以将新视图作为选项卡添加 (类别) ： 

    [![选择选项卡](tab-bars-images/tab05vs.png)](tab-bars-images/tab05vs.png#lightbox)
1. 通过在 iOS 设计器中添加 UI 元素，以常规方式为每个 Caterogies 内容区域设计 UI 布局。
1. 公开任何所需的事件以使用 c # 代码中的 UI 控件。
1. 命名要在 c # 代码中公开的任何 UI 控件。
1. 保存所做更改。

-----

> [!IMPORTANT]
> 尽管可以在 iOS 设计器中将事件（例如）分配给 `TouchUpInside` UI 元素 (如 `UIButton`) ，但永远不会调用此类事件，因为 Apple TV 没有触摸屏或支持触控事件。 `Primary Action`创建 tvOS 用户界面元素的事件处理程序时，应始终使用事件。

有关使用情节提要的详细信息，请参阅 [tvOS 快速入门指南](~/ios/tvos/get-started/hello-tvos.md)。 

<a name="Working-with-Tab-Bars"></a>

## <a name="working-with-tab-bars"></a>使用选项卡栏

使用 `Items` 的属性可将 `UITabBar` `UITabBarItems` 其包含的集合作为零 (0) 索引数组进行访问。 `SelectedItem`属性将返回当前选定的选项卡 (类别) 为 `UITabBarItem` 。

<a name="Working-with-Tab-Bar-Items"></a>

## <a name="working-with-tab-bar-items"></a>使用选项卡栏项

若要在给定选项卡上显示徽章 (带有白色文本) 的红色椭圆，请使用以下代码：

```csharp
// Display a badge
TabBar.Items [2].BadgeValue = "10";
```

这会在运行时生成以下结果：

[![带有徽章的选项卡项](tab-bars-images/tab06.png)](tab-bars-images/tab06.png#lightbox)

使用 `Title` 的属性 `UITabBarItem` 可更改标题和 `Image` 属性以更改图标。

<a name="Summary"></a>

## <a name="summary"></a>总结

本文介绍了如何在 tvOS 应用内设计和使用选项卡栏控制器。

## <a name="related-links"></a>相关链接

- [tvOS 示例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人体学接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [TvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)