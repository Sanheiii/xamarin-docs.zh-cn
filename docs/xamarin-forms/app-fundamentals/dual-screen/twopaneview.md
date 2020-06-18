---
title: Xamarin.Forms 双屏布局
description: 本指南介绍了如何使用 Xamarin.Forms TwoPaneView 来优化 Surface Duo 和 Surface Neo 等双屏设备的应用体验。
ms.prod: xamarin
ms.assetid: 17ee8afa-5e7c-4a4f-a9b6-2aca03f30fe3
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 28d4b3da44cc1a022b70c0de0720be747e047f9f
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138887"
---
# <a name="xamarinforms-dual-screen-layout"></a>Xamarin.Forms 双屏布局

![](~/media/shared/preview.png "This API is currently pre-release")

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-dualscreendemos/)

`TwoPaneView` 类表示一个带有两个视图的容器，这些视图会调整内容的大小且在可用空间内并排或按从上到下的顺序放置内容。 `TwoPaneView` 继承自 `Grid`，因此考虑这些属性时最简单的方式是看作它们要应用于网格。

## <a name="set-up-twopaneview"></a>设置 TwoPaneView

`TwoPaneView.Source` 属性可以是 URI，也可以是本地文件路径。 媒体打开时会立即开始播放：

```xaml
<ContentPage xmlns:dualScreen="clr-namespace:Xamarin.Forms.DualScreen;assembly=Xamarin.Forms.DualScreen">
    <dualScreen:TwoPaneView>
        <dualScreen:TwoPaneView.Pane1>
            <StackLayout>
                <Label Text="Pane1 Content" />
            </StackLayout>
        </dualScreen:TwoPaneView.Pane1>
        <dualScreen:TwoPaneView.Pane2>
            <StackLayout>
                <Label Text="Pane2 Content" />
            </StackLayout>
        </dualScreen:TwoPaneView.Pane2>
    </dualScreen:TwoPaneView>
</ContentPage>
```

## <a name="understand-twopaneview-modes"></a>了解 TwoPaneView 模式

只有其中一个模式可处于活动状态：

- `SinglePane` 当前只有一个窗格可见。
- `Wide`：这两个窗格按水平布局。 一个窗格在左侧，另一个在右侧。 当位于两个屏幕上时，这是设备纵向显示时的模式。
- `Tall`：这两个窗格按垂直布局。 一个窗格在顶部，另一个在底部。 当位于两个屏幕上时，这是设备横向显示时的模式。

## <a name="control-twopaneview-when-its-only-on-one-screen"></a>当仅在一个屏幕上时控制 TwoPaneView

当 `TwoPaneView` 占用单个屏幕时，将应用以下属性：

- `MinTallModeHeight` 指示控件进入 Tall 模式所必需的最小高度。
- `MinWideModeWidth` 指示控件进入 Wide 模式所必需的最小宽度。
- `Pane1Length` 在 Wide 模式中设置 Pane1 的宽度，在 Tall 模式中设置 Pane1 的高度，在 SinglePane 模式下不起作用。
- `Pane2Length` 在横向模式中设置 Pane2 的宽度，在纵向模式中设置 Pane2 的高度，在 SinglePane 模式下不起作用。

> [!IMPORTANT]
> 如果 `TwoPaneView` 横跨两个屏幕，则这些属性不起作用。

## <a name="properties-that-apply-when-on-one-screen-or-two"></a>在一个或两个屏幕上时应用的属性

当 `TwoPaneView` 占用一个或两个屏幕时，将应用以下属性：

- 在 Tall 模式下时，`TallModeConfiguration` 指示从左到右的排序方式；如果你根据 TwoPaneViewPriority 的定义仅希望使一个窗格可见，也可采用此属性。
- 在 Wide 模式下时，`WideModeConfiguration` 指示从上到下的排序方式；如果你根据 TwoPaneViewPriority 的定义仅希望使一个窗格可见，也可采用此属性。
- `PanePriority` 指示在 SinglePane 模式下时是显示 Pane1 还是显示 Pane2。

## <a name="related-links"></a>相关链接

- [（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-dualscreendemos/)
