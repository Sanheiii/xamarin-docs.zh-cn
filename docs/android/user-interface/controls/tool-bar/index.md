---
title: 工具栏
description: 工具栏是比默认操作栏提供更多灵活性的操作栏组件：可以将其放置在应用中的任何位置，可以更改其大小，还可以使用不同于应用主题的配色方案。 此外，每个应用屏幕都可以有多个工具栏。
ms.prod: xamarin
ms.assetid: 22EE5FBD-3240-4308-AF76-EF45D72936DE
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 85d649ec464b725b5e0b438c650e0c9f32d23a27
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457676"
---
# <a name="toolbar"></a>工具栏

_工具栏是比默认操作栏提供更多灵活性的操作栏组件：可以将其放置在应用中的任何位置，可以更改其大小，还可以使用不同于应用主题的配色方案。此外，每个应用屏幕都可以有多个工具栏。_

## <a name="overview"></a>概述

任何 Android 活动的关键设计元素都是一个 *操作栏*。 操作栏是在 Android 应用中用于导航、搜索、菜单和品牌标记的 UI 组件。 在 android 5.0 棒糖之前的 Android 版本中，操作栏 (也称为 *应用栏*) 是提供此功能的建议组件。 

`Toolbar`Android 5.0 棒糖形) 中引入的小组件 (可以视为操作栏接口的泛化， &ndash; 它旨在替换操作栏。 `Toolbar`可以在应用布局中的任意位置使用，它比操作栏更容易自定义。 以下屏幕截图演示了 `Toolbar` 本指南中创建的自定义示例： 

[![带有编辑、保存和溢出菜单项的工具栏的示例屏幕截图](images/01-toolbar-sml.png)](images/01-toolbar.png#lightbox)

和操作栏之间存在一些重要的区别 `Toolbar` ： 

- `Toolbar`可以放置在用户界面中的任何位置。

- 同一屏幕上可以显示多个工具栏。

- 如果使用片段，则每个片段都可以有自己的片段 `Toolbar` 。 

- `Toolbar`可以配置为仅跨越屏幕的部分宽度。 

- 由于未 `Toolbar` 绑定到活动的窗口 décor 的配色方案，因此它可以有一个视觉上不同的配色方案。 

- 不同于操作栏，不 `Toolbar` 会在左侧包含一个图标。 其菜单上的菜单使用更少的空间。 

- `Toolbar`高度是可调整的。 

- 其他视图可以包括在中 `Toolbar` 。 

`Toolbar`可以包含一个或多个以下元素： 

- 导航按钮

- 品牌徽标图像

- 标题和副标题

- 自定义视图

- “操作”菜单

- 溢出菜单

Google 的 [材料设计指南](https://material.google.com/) 建议利用这些元素，为应用程序 (独特的外观，而不是仅依赖应用程序图标和标题) 。 

本指南涵盖最常使用的 `Toolbar` 方案：

- 将活动的默认操作栏替换为 `Toolbar` 。 

- 将第二个添加 `Toolbar` 到活动。

- 在本指南的其余部分中，使用 **Android 支持库 V7 AppCompat** library (称为 *AppCompat*)   `Toolbar` 在早期版本的 Android 上进行部署。 

## <a name="requirements"></a>要求

`Toolbar` 适用于 Android 5.0 棒糖 (API 21) 和更高版本。 如果面向 android 5.0 之前的 Android 版本，请使用 [Android 支持库 V7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)，它在 NuGet 包中提供向后兼容的 `Toolbar` 支持。 
[工具栏兼容性](~/android/user-interface/controls/tool-bar/toolbar-compatibility.md) 介绍了如何使用此库。 

## <a name="related-links"></a>相关链接

- [棒糖形工具栏 (示例) ](/samples/xamarin/monodroid-samples/android50-toolbar)
- [AppCompat 工具栏 (示例) ](/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar)