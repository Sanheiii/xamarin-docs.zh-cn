---
title: IOS 上的 SwipeView 滑动过渡模式
description: 平台说明允许使用仅在特定平台上可用的功能，而无需实现自定义呈现器或效果。 本文介绍如何使用 iOS 平台特定的来控制打开 SwipeView 时所使用的转换。
ms.prod: xamarin
ms.assetid: C667F24C-BAD8-47E0-9285-D3546BEF703B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a05bdc9d4cef01681701143a3f3a642a884b9d1e
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91560736"
---
# <a name="swipeview-swipe-transition-mode-on-ios"></a>IOS 上的 SwipeView 滑动过渡模式

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平台特定控制打开时使用的转换 `SwipeView` 。 它通过将 `SwipeView.SwipeTransitionMode` 可绑定的属性设置为枚举的值，在 XAML 中使用 `SwipeTransitionMode` ：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <SwipeView ios:SwipeView.SwipeTransitionMode="Drag">
            <SwipeView.LeftItems>
                <SwipeItems>
                    <SwipeItem Text="Delete"
                               IconImageSource="delete.png"
                               BackgroundColor="LightPink"
                               Invoked="OnDeleteSwipeItemInvoked" />
                </SwipeItems>
            </SwipeView.LeftItems>
            <!-- Content -->
        </SwipeView>
    </StackLayout>
</ContentPage>
```

此外，还可以使用 Fluent API 从 c # 使用该方法：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

SwipeView swipeView = new Xamarin.Forms.SwipeView();
swipeView.On<iOS>().SetSwipeTransitionMode(SwipeTransitionMode.Drag);
// ...
```

`SwipeView.On<iOS>`方法指定此平台特定的仅在 iOS 上运行。 `SwipeView.SetSwipeTransitionMode`命名空间中的方法 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 用于控制打开时使用的转换 `SwipeView` 。 `SwipeTransitionMode`枚举提供了两个可能的值：

- `Reveal` 指示将在内容为 "重击" 的情况下显示滑动项 `SwipeView` ，并为属性的默认值 `SwipeView.SwipeTransitionMode` 。
- `Drag` 指示当内容为重击时，将在视图中拖动滑动项 `SwipeView` 。

此外， `SwipeView.GetSwipeTransitionMode` 方法可用于返回 `SwipeTransitionMode` 应用于的 `SwipeView` 。

结果是将指定的 `SwipeTransitionMode` 值应用于 `SwipeView` ，后者控制打开时使用的转换 `SwipeView` ：

[![IOS 上的 SwipeView SwipeTransitionModes 的屏幕截图](swipeview-swipetransitionmode-images/swipetransitionmode.png "IOS 上的 SwipeTransitionModes")](swipeview-swipetransitionmode-images/swipetransitionmode-large.png#lightbox "IOS 上的 SwipeTransitionModes")

## <a name="related-links"></a>相关链接

- [PlatformSpecifics (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)