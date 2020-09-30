---
title: Android 上的 VisualElement 旧版颜色模式
description: 平台说明允许使用仅在特定平台上可用的功能，而无需实现自定义呈现器或效果。 本文介绍如何使用禁用旧版颜色模式的 Android 平台特定 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: 37D95A2D-74AC-488A-B903-2BDD799EAA5C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ca8fdf101d521bf5cc58edecc523a035577be09b
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563362"
---
# <a name="visualelement-legacy-color-mode-on-android"></a>Android 上的 VisualElement 旧版颜色模式

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

某些视图的 Xamarin.Forms 功能是旧的颜色模式。 在此模式下，当 [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) 视图的属性设置为时 `false` ，视图将覆盖用户为禁用状态的默认本机颜色设置的颜色。 为实现向后兼容性，这种旧的颜色模式仍适用于受支持视图的默认行为。

此 Android 平台特定的禁用了此旧版颜色模式，因此即使在禁用视图时，用户也仍会保留对视图设置的颜色。 它通过将附加属性设置为来在 XAML 中使用 [`VisualElement.IsLegacyColorModeEnabled`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.VisualElement.IsLegacyColorModeEnabledProperty) `false` ：

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Button Text="Button"
                TextColor="Blue"
                BackgroundColor="Bisque"
                android:VisualElement.IsLegacyColorModeEnabled="False" />
        ...
    </StackLayout>
</ContentPage>
```

此外，还可以使用 Fluent API 从 c # 使用该方法：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

_legacyColorModeDisabledButton.On<Android>().SetIsLegacyColorModeEnabled(false);
```

`VisualElement.On<Android>`方法指定此平台特定的仅在 Android 上运行。 [ `VisualElement.SetIsLegacyColorModeEnabled` ] (x： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. VisualElement. SetIsLegacyColorModeEnabled (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。VisualElement} （在命名空间中）) # A3 方法 [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) 用于控制是否禁用旧颜色模式。 此外，[ `VisualElement.GetIsLegacyColorModeEnabled` ] (x： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. VisualElement. GetIsLegacyColorModeEnabled (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。VisualElement} ) # A3 方法可用于返回是否禁用旧颜色模式。

结果就是可以禁用旧的颜色模式，这样，即使在禁用视图时，用户也仍会保留对视图设置的颜色：

![已禁用旧版颜色模式](legacy-color-mode-images/legacy-color-mode-disabled.png)

> [!NOTE]
> 在 [`VisualStateGroup`](xref:Xamarin.Forms.VisualStateGroup) 视图上设置时，旧的颜色模式完全被忽略。 有关可视状态的详细信息，请 [参阅 Xamarin.Forms 可视状态管理器](~/xamarin-forms/user-interface/visual-state-manager.md)。

## <a name="related-links"></a>相关链接

- [PlatformSpecifics (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)