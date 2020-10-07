---
title: Android 平台功能
description: 本文介绍如何将特定于 Android 的功能添加到 Xamarin.Forms 应用程序。
ms.prod: xamarin
ms.assetid: E24168F3-0138-4814-86EA-B467F6B8A545
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: cce3fbf6f80b8a0fddff2ed2b2af027721faf59d
ms.sourcegitcommit: bfe713c038a0d6c4362da4c3221d7aee9ab03d2b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91805604"
---
# <a name="android-platform-features"></a>Android 平台功能

开发 Xamarin.Forms 适用于 Android 的应用程序需要 Visual Studio。 " [支持的平台" 页](~/get-started/supported-platforms.md) 包含有关先决条件的详细信息。

## <a name="platform-specifics"></a>平台特定内容

平台说明允许使用仅在特定平台上可用的功能，而无需实现自定义呈现器或效果。

为 Xamarin.Forms Android 上的视图、页面和布局提供了以下特定于平台的功能：

- 控制视觉对象的 Z 顺序以确定绘制顺序。 有关详细信息，请参阅 [Android 上的 VisualElement 提升](visualelement-elevation.md)。
- 禁用受支持的旧版颜色模式 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。 有关详细信息，请参阅 [VisualElement 旧版彩色模式（Android](legacy-color-mode.md)）。

为 Android 上的视图提供了以下特定于平台的功能 Xamarin.Forms ：

- 使用 Android 按钮的默认填充和阴影值。 有关详细信息，请参阅 [Android 上的按钮填充和阴影](button-padding-shadow.md)。
- 设置用于的软键盘的输入法编辑器选项 [`Entry`](xref:Xamarin.Forms.Entry) 。 有关详细信息，请参阅 [Android 上的条目输入法编辑器选项](entry-ime-options.md)。
- 在上启用投影 `ImageButton` 。 有关详细信息，请参阅 [Android 上的 ImageButton 投影](imagebutton-drop-shadow.md)。
- 在中启用快速滚动 [`ListView`](xref:Xamarin.Forms.ListView) 。 有关详细信息，请参阅 [Android 上的 ListView 快速滚动](listview-fast-scrolling.md)。
- 控制在打开时使用的转换 `SwipeView` 。 有关详细信息，请参阅 [SwipeView 滑动过渡模式](swipeview-swipetransitionmode.md)。
- 控制是否 [`WebView`](xref:Xamarin.Forms.WebView) 可以显示混合内容。 有关详细信息，请参阅 [Android 上的 Web 视图混合内容](webview-mixed-content.md)。
- 启用缩放 [`WebView`](xref:Xamarin.Forms.WebView) 。 有关详细信息，请参阅 [Android 上的 Web 视图缩放](webview-zoom-controls.md)。

为 Android 上的单元提供了以下特定于平台的功能 Xamarin.Forms ：

- 启用 [`ViewCell`](xref:Xamarin.Forms.ViewCell) 上下文操作旧版模式，以便在更改中的选定项时不会更新上下文操作菜单 [`ListView`](xref:Xamarin.Forms.ListView) 。 有关详细信息，请参阅 [Android 上的 ViewCell 上下文操作](viewcell-context-actions.md)。

为 Android 上的页面提供了以下特定于平台的功能 Xamarin.Forms ：

- 设置上导航栏的高度 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 。 有关详细信息，请参阅 [Android 上的 NavigationPage Bar Height](navigationpage-bar-height.md)。
- 在中的页面间导航时禁用转换动画 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 。 有关详细信息，请参阅 [Android 上的 TabbedPage 页面过渡动画](tabbedpage-transition-animations.md)。
- 在中的页之间启用轻扫 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 。 有关详细信息，请参阅 [Android 上的 TabbedPage Page 轻扫](tabbedpage-page-swiping.md)。
- 在上设置工具栏的位置和颜色 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 。 有关详细信息，请参阅 [TabbedPage Toolbar 在 Android 上的位置和颜色](tabbedpage-toolbar-placement-color.md)。

Xamarin.Forms在 Android 上为类提供了以下特定于平台的功能 [`Application`](xref:Xamarin.Forms.Application) ：

- 设置软键盘的操作模式。 有关详细信息，请参阅 [Android 上的软键盘输入模式](soft-keyboard-input-mode.md)。
- [`Disappearing`](xref:Xamarin.Forms.Page.Appearing) [`Appearing`](xref:Xamarin.Forms.Page.Appearing) 对于使用 AppCompat 的应用程序，分别在 "暂停" 和 "恢复" 上禁用和页面生命周期事件。 有关详细信息，请参阅 [Android 上的页面生命周期事件](page-lifecycle-events.md)。

## <a name="platform-support"></a>平台支持

最初，默认 Xamarin.Forms android 项目使用早于 Android 5.0 之前的常用控件呈现样式。 使用该模板构建的应用程序将 `FormsApplicationActivity` 其作为其主活动的基类。

## <a name="material-design-via-appcompat"></a>通过 AppCompat 的材料设计

Xamarin.Forms Android 项目现在用作 `FormsAppCompatActivity` 其主活动的基类。 此类使用 Android 提供的 **AppCompat** 功能来实现材料设计主题。

若要将材料设计主题添加到 Xamarin.Forms Android 项目，请[按照 AppCompat 支持的安装说明进行](appcompat-material-design.md)操作

下面是带有默认值的 **Todo** 示例 `FormsApplicationActivity` ：

[![不带 AppCompat 的 Todo 示例应用程序](images/before-appcompat-sml.png)](images/before-appcompat.png#lightbox "不带 AppCompat 的 Todo 示例应用程序")

在升级项目以使用 `FormsAppCompatActivity` (并添加附加主题信息) 之后，这是同一代码：

[![具有 AppCompat 和主题的 Todo 示例应用程序](images/post-appcompat-sml.png)](images/post-appcompat.png#lightbox "具有 AppCompat 和主题的 Todo 示例应用程序")

> [!NOTE]
> 使用时 `FormsAppCompatActivity` ， [某些 Android 自定义呈现](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md) 器的基类将有所不同。

## <a name="androidx-migration"></a>AndroidX 迁移

AndroidX 替换 Android 支持库。 若要了解有关 AndroidX 以及如何迁移 Xamarin.Forms 应用以使用 AndroidX 库的信息，请参阅[中 Xamarin.Forms 的 AndroidX 迁移](~/xamarin-forms/platform/android/androidx-migration.md)。

## <a name="related-links"></a>相关链接

- [添加材料设计支持](appcompat-material-design.md)
