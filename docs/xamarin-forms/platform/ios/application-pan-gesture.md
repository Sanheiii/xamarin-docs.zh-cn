---
title: IOS 上的并发平移手势识别
description: 平台说明允许使用仅在特定平台上可用的功能，而无需实现自定义呈现器或效果。 本文介绍如何使用 iOS 平台特定的，以便能够在应用程序中使用同时使用的平移手势识别。
ms.prod: xamarin
ms.assetid: 883D89DA-F8FF-4B97-9C3F-2DD05C96A495
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5bea8b58d8b80ced97856fc7c981afdd5c2102a7
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562400"
---
# <a name="simultaneous-pan-gesture-recognition-on-ios"></a>IOS 上的并发平移手势识别

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

当 [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) 附加到滚动视图内的视图时，将捕获所有平移手势，而不会将其 `PanGestureRecognizer` 传递到滚动视图。 因此，滚动视图将不再滚动。

此 iOS 平台特定 `PanGestureRecognizer` 用于在滚动视图中使用滚动视图来捕获和共享平移手势。 它通过将附加属性设置为来在 XAML 中使用 [`Application.PanGestureRecognizerShouldRecognizeSimultaneously`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Application.PanGestureRecognizerShouldRecognizeSimultaneouslyProperty) `true` ：

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.PanGestureRecognizerShouldRecognizeSimultaneously="true">
    ...
</Application>
```

此外，还可以使用 Fluent API 从 c # 使用该方法：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetPanGestureRecognizerShouldRecognizeSimultaneously(true);
```

`Application.On<iOS>`方法指定此平台特定的仅在 iOS 上运行。 [ `Application.SetPanGestureRecognizerShouldRecognizeSimultaneously` ] (x： Xamarin.Forms 。PlatformConfiguration. SetPanGestureRecognizerShouldRecognizeSimultaneously (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration、 Xamarin.Forms 。应用程序}，system.string) # A3 方法， [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 用于控制滚动视图中的平移手势识别器是捕获平移手势，还是使用滚动视图捕获和共享平移手势。 此外，[ `Application.GetPanGestureRecognizerShouldRecognizeSimultaneously` ] (x： Xamarin.Forms 。PlatformConfiguration. GetPanGestureRecognizerShouldRecognizeSimultaneously (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration、 Xamarin.Forms 。应用程序} ) # A3 方法可用于返回是否与包含的滚动视图共享平移手势 [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) 。

因此，在此特定于平台的情况下，当包含时， [`ListView`](xref:Xamarin.Forms.ListView) [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) `ListView` 和都 `PanGestureRecognizer` 将接收平移手势并进行处理。 但是，在此特定于平台的禁用中，当包含时， `ListView` `PanGestureRecognizer` `PanGestureRecognizer` 将捕获平移手势并进行处理，并且不会 `ListView` 收到平移手势。

## <a name="related-links"></a>相关链接

- [PlatformSpecifics (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)