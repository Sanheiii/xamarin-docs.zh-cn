---
title: ScrollView 内容涉及 iOS
description: 平台说明允许使用仅在特定平台上可用的功能，而无需实现自定义呈现器或效果。 本文介绍如何使用特定于 iOS 平台的来控制 ScrollView 是否处理触摸手势或将其传递给其内容。
ms.prod: xamarin
ms.assetid: 99F823DB-B379-40F0-A343-A9783C341120
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 128d7271175846f415aa115c377bad0e0e2adaf5
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563960"
---
# <a name="scrollview-content-touches-on-ios"></a>ScrollView 内容涉及 iOS

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

当触摸手势在 [`ScrollView`](xref:Xamarin.Forms.ScrollView) iOS 上开始，并根据 `ScrollView` 计时器范围内的用户操作确定时，将触发隐式计时器，不管是处理手势还是将其传递到其内容。 默认情况下，iOS `ScrollView` 延迟内容的发生，但在某些情况下，这可能会导致问题，其中的 `ScrollView` 内容不会在其应该时获胜。 因此，此特定于平台的控制是否 `ScrollView` 处理触摸手势或将其传递给其内容。 它通过将 `ScrollView.ShouldDelayContentTouches` 附加属性设置为值在 XAML 中使用 `boolean` ：

```xaml
<MasterDetailPage ...
                  xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <MasterDetailPage.Master>
        <ContentPage Title="Menu" BackgroundColor="Blue" />
    </MasterDetailPage.Master>
    <MasterDetailPage.Detail>
        <ContentPage>
            <ScrollView x:Name="scrollView" ios:ScrollView.ShouldDelayContentTouches="false">
                <StackLayout Margin="0,20">
                    <Slider />
                    <Button Text="Toggle ScrollView DelayContentTouches" Clicked="OnButtonClicked" />
                </StackLayout>
            </ScrollView>
        </ContentPage>
    </MasterDetailPage.Detail>
</MasterDetailPage>
```

此外，还可以使用 Fluent API 从 c # 使用该方法：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

scrollView.On<iOS>().SetShouldDelayContentTouches(false);
```

`ScrollView.On<iOS>`方法指定此平台特定的仅在 iOS 上运行。 `ScrollView.SetShouldDelayContentTouches`命名空间中的方法 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 用于控制是否 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 处理触摸手势或将其传递给其内容。 此外， `SetShouldDelayContentTouches` 可以通过调用 `ShouldDelayContentTouches` 方法来返回内容接触是否延迟，使用方法来切换延迟内容润色：

```csharp
scrollView.On<iOS>().SetShouldDelayContentTouches(!scrollView.On<iOS>().ShouldDelayContentTouches());
```

结果是 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 可以禁止延迟接收内容的接收，因此，在此方案中，会 [`Slider`](xref:Xamarin.Forms.Slider) 接收该笔势，而不是接收的 [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) 页面 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) ：

[![ScrollView 延迟内容触及特定于平台](scrollview-content-touches-images/scrollview-delay-content-touches.png)](scrollview-content-touches-images/scrollview-delay-content-touches-large.png#lightbox "ScrollView 延迟内容触及特定于平台")

## <a name="related-links"></a>相关链接

- [PlatformSpecifics (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)