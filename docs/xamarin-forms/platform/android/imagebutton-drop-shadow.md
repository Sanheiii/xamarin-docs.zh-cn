---
title: Android 上的 ImageButton 投影
description: 平台说明允许使用仅在特定平台上可用的功能，而无需实现自定义呈现器或效果。 本文介绍如何使用 Android 平台特定的，以便在 ImageButton 上启用投影。
ms.prod: xamarin
ms.assetid: D3604D87-9F9F-4FE2-8B10-DF3B143C0734
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c355816ec1b89801814b217cad8a23be2f453226
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563791"
---
# <a name="imagebutton-drop-shadows-on-android"></a>Android 上的 ImageButton 投影

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 Android 平台特定用于在上启用投影 `ImageButton` 。 它在 XAML 中使用，方法是将 `ImageButton.IsShadowEnabled` 可绑定的属性设置为，并将多 `true` 个其他可选的可绑定属性设置为控制投影：

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
       <ImageButton ...
                    Source="XamarinLogo.png"
                    BackgroundColor="GhostWhite"
                    android:ImageButton.IsShadowEnabled="true"
                    android:ImageButton.ShadowColor="Gray"
                    android:ImageButton.ShadowRadius="12">
            <android:ImageButton.ShadowOffset>
                <Size>
                    <x:Arguments>
                        <x:Double>10</x:Double>
                        <x:Double>10</x:Double>
                    </x:Arguments>
                </Size>
            </android:ImageButton.ShadowOffset>
        </ImageButton>
        ...
    </StackLayout>
</ContentPage>
```

此外，还可以使用 Fluent API 从 c # 使用该方法：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

var imageButton = new Xamarin.Forms.ImageButton { Source = "XamarinLogo.png", BackgroundColor = Color.GhostWhite, ... };
imageButton.On<Android>()
           .SetIsShadowEnabled(true)
           .SetShadowColor(Color.Gray)
           .SetShadowOffset(new Size(10, 10))
           .SetShadowRadius(12);
```

> [!IMPORTANT]
> 投影作为背景的一部分绘制 `ImageButton` ，并且只有在设置了属性的情况下，才会绘制背景 `BackgroundColor` 。 因此，如果未设置该属性，则不会绘制投影 `ImageButton.BackgroundColor` 。

`ImageButton.On<Android>`方法指定此平台特定的仅在 Android 上运行。 `ImageButton.SetIsShadowEnabled`命名空间中的方法 [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) 用于控制是否在上启用了投影 `ImageButton` 。 此外，可以调用以下方法来控制投影：

- `SetShadowColor` –设置投影的颜色。 默认颜色为 [`Color.Default`](xref:Xamarin.Forms.Color.Default*) 。
- `SetShadowOffset` –设置投影的偏移量。 偏移量会更改阴影的转换方向，并指定为 [`Size`](xref:Xamarin.Forms.Size) 值。 `Size`结构值以与设备无关的单位表示，其第一个值为向左 (负值) 或右 (正值) ，第二个值是高于正值 (或低于) 负值 (的距离。 此属性的默认值为 (0.0，0.0) ，这会导致在的每一侧周围都有投影 `ImageButton` 。
- `SetShadowRadius`–设置用于呈现投影的模糊半径。 默认半径值为10.0。

> [!NOTE]
> 可以通过调用 `GetIsShadowEnabled` 、 `GetShadowColor` 、 `GetShadowOffset` 和方法来查询投影的状态 `GetShadowRadius` 。

结果就是可以在上启用投影 `ImageButton` ：

![带有投影的 ImageButton](imagebutton-drop-shadow-images/imagebutton-drop-shadow.png)

## <a name="related-links"></a>相关链接

- [PlatformSpecifics (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)