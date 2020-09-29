---
title: 在 Xamarin 和磨损操作系统中使用屏幕大小
ms.prod: xamarin
ms.assetid: 77831169-C663-4D42-B742-B8B556B1DA4B
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/25/2018
ms.openlocfilehash: 1747601596cd1772210d9a66755d7aa98ca14052
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457024"
---
# <a name="working-with-screen-sizes"></a>使用屏幕大小

Android 磨损设备可以具有矩形或环形显示，也可以是不同的大小。

![矩形和圆形磨损显示的屏幕截图](screen-sizes-images/moyeu-wear.png)

## <a name="identifying-screen-type"></a>标识屏幕类型

磨损支持库提供了一些控件，可帮助你检测和适应不同的屏幕形状，例如 `WatchViewStub` 和 `BoxInsetLayout` 。

请注意，一些其他支持库控件 (例如 `GridViewPager`) *自动* 检测屏幕形状本身，不应将其添加为下面所述的控件的子级。

### <a name="watchviewstub"></a>WatchViewStub

请参阅 [WatchViewStub](/samples/xamarin/monodroid-samples/wear-watchviewstub) 示例，了解如何检测屏幕类型并为每种类型显示不同的布局。

主布局文件包含一个， `android.support.wearable.view.WatchViewStub` 它使用和属性来引用矩形和圆形屏幕的不同布局 `app:rectLayout` `app:roundLayout` ：

```xml
<android.support.wearable.view.WatchViewStub
    xmlns:app="http://schemas.android.com/apk/res-auto"
  android:layout_width="match_parent"
  android:layout_height="match_parent"
  android:id="@+id/stub"
  app:rectLayout="@layout/rect_layout"
  app:roundLayout="@layout/round_layout" />
```

对于将在运行时选择的每个样式，解决方案都包含不同的布局：

!["资源/布局" 下显示的文件](screen-sizes-images/solution.png)

### <a name="boxinsetlayout"></a>BoxInsetLayout

您还可以创建适应矩形或圆形屏幕的单个视图，而不是为每个屏幕类型构建不同的布局。

此 [Google 示例](https://developer.android.com/training/wearables/ui/layouts.html#same-layout) 演示如何使用在 `BoxInsetLayout` 矩形和圆形屏幕上使用相同的布局。

## <a name="wear-ui-designer"></a>磨损 UI 设计器

Xamarin Android Designer 支持矩形和圆形屏幕：

![选择 Xamarin 中的 Android 磨损方形屏幕 Android Designer](screen-sizes-images/design-screen-type.png)

此处显示了矩形样式的设计图面：

![长方形样式的设计图面](screen-sizes-images/design-rect.png) 

圆形样式中的设计图面如下所示：

![圆形样式的设计图面](screen-sizes-images/design-round.png)

## <a name="wear-simulator"></a>磨损模拟器

**Google 仿真器管理器**包含两种屏幕类型的设备定义。 可以创建矩形和圆形模拟器来测试应用程序。

![Google 仿真器管理器中显示的磨损设备定义](screen-sizes-images/emulator-devices.png)

对于矩形屏幕，模拟器将呈现如下：

![矩形屏幕的仿真程序呈现](screen-sizes-images/recipe-2.png) 

对于圆形屏幕，它将如下所示：

![环形屏幕的仿真程序呈现](screen-sizes-images/recipe-2-round.png)

## <a name="video"></a>视频

[适用于 Android 的 developers.google.com 应用程序](https://www.youtube.com/watch?v=naf_WbtFAlY)。 [developers.google.com](https://www.youtube.com/channel/UC_x5XG1OV2P6uZZ5FSM9Ttw)