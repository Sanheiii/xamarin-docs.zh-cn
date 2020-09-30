---
title: IOS 上的 VisualElement 投影
description: 平台说明允许使用仅在特定平台上可用的功能，而无需实现自定义呈现器或效果。 本文介绍如何使用 iOS 平台特定的，以便在 VisualElement 上启用投影。
ms.prod: xamarin
ms.assetid: 2147FD66-058E-4BE5-840A-369842B26EC4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2e54182ca5c1f2afbc107011aea6b84af864e78d
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562336"
---
# <a name="visualelement-drop-shadows-on-ios"></a>IOS 上的 VisualElement 投影

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平台特定用于在上启用投影 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。 它在 XAML 中使用，方法是将 [`VisualElement.IsShadowEnabled`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.IsShadowEnabledProperty) 附加属性设置为，并将多 `true` 个其他可选附加属性设置为控制投影：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <BoxView ...
                 ios:VisualElement.IsShadowEnabled="true"
                 ios:VisualElement.ShadowColor="Purple"
                 ios:VisualElement.ShadowOpacity="0.7"
                 ios:VisualElement.ShadowRadius="12">
            <ios:VisualElement.ShadowOffset>
                <Size>
                    <x:Arguments>
                        <x:Double>10</x:Double>
                        <x:Double>10</x:Double>
                    </x:Arguments>
                </Size>
            </ios:VisualElement.ShadowOffset>
         </BoxView>
        ...
    </StackLayout>
</ContentPage>
```

此外，还可以使用 Fluent API 从 c # 使用该方法：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var boxView = new BoxView { Color = Color.Aqua, WidthRequest = 100, HeightRequest = 100 };
boxView.On<iOS>()
       .SetIsShadowEnabled(true)
       .SetShadowColor(Color.Purple)
       .SetShadowOffset(new Size(10,10))
       .SetShadowOpacity(0.7)
       .SetShadowRadius(12);
```

`VisualElement.On<iOS>`方法指定此平台特定的仅在 iOS 上运行。 [ `VisualElement.SetIsShadowEnabled` ] (x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. VisualElement. SetIsShadowEnabled (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration、 Xamarin.Forms 。VisualElement} （在命名空间中）) # A3 方法 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 用于控制是否在上启用了投影 `VisualElement` 。 此外，可以调用以下方法来控制投影：

- [ `SetShadowColor` ] (x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. VisualElement. SetShadowColor (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration、 Xamarin.Forms 。VisualElement}， Xamarin.Forms 。Color) # A3 –设置投影的颜色。 默认颜色为 [`Color.Default`](xref:Xamarin.Forms.Color.Default*) 。
- [ `SetShadowOffset` ] (x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. VisualElement. SetShadowOffset (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration、 Xamarin.Forms 。VisualElement}， Xamarin.Forms 。Size) # A3 –设置投影的偏移量。 偏移量会更改阴影的转换方向，并指定为 [`Size`](xref:Xamarin.Forms.Size) 值。 `Size`结构值以与设备无关的单位表示，其第一个值为向左 (负值) 或右 (正值) ，第二个值是高于正值 (或低于) 负值 (的距离。 此属性的默认值为 (0.0，0.0) ，这会导致在的每一侧周围都有投影 `VisualElement` 。
- [ `SetShadowOpacity` ] (x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. VisualElement. SetShadowOpacity (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration、 Xamarin.Forms 。VisualElement}，System.object) # A3 –设置投影的不透明度，其值为 0.0 (透明) 到 1.0 (不透明) 。 默认不透明度值为0.5。
- [ `SetShadowRadius` ] (x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. VisualElement. SetShadowRadius (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration、 Xamarin.Forms 。VisualElement}，System.object) # A3 –设置用于渲染投影的模糊半径。 默认半径值为10.0。

> [!NOTE]
> 可以通过调用 [ `GetIsShadowEnabled` ] (x：来查询投影的状态 Xamarin.Forms 。PlatformConfiguration. iOSSpecific. VisualElement. GetIsShadowEnabled (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration、 Xamarin.Forms 。VisualElement} ) # A3，[ `GetShadowColor` ] (x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. VisualElement. GetShadowColor (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration、 Xamarin.Forms 。VisualElement} ) # A7，[ `GetShadowOffset` ] (x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. VisualElement. GetShadowOffset (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration、 Xamarin.Forms 。VisualElement} ) # A11，[ `GetShadowOpacity` ] (x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. VisualElement. GetShadowOpacity (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration、 Xamarin.Forms 。VisualElement} ) # A15，[ `GetShadowRadius` ] (x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. VisualElement. GetShadowRadius (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration、 Xamarin.Forms 。VisualElement} ) # A19 方法。

结果就是可以在上启用投影 [`VisualElement`](xref:Xamarin.Forms.VisualElement) ：

![已启用投影](drop-shadow-images/drop-shadow.png)

## <a name="related-links"></a>相关链接

- [PlatformSpecifics (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)