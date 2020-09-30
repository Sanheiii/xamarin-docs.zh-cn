---
title: IOS 上的安全区域布局指南
description: 平台说明允许使用仅在特定平台上可用的功能，而无需实现自定义呈现器或效果。 本文介绍如何使用特定于 iOS 平台的，确保页面内容定位在屏幕上，此区域对于使用 iOS 11 及更高版本的所有设备是安全的。
ms.prod: xamarin
ms.assetid: 2B6789C1-39B4-4C16-ADE1-3ED3378EAC63
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c4bdc3a5d952fda525b3e62cfbaabd9b5272bd6a
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556381"
---
# <a name="safe-area-layout-guide-on-ios"></a>IOS 上的安全区域布局指南

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平台特定用于确保页面内容定位在屏幕上，此区域对于使用 iOS 11 及更高版本的所有设备是安全的。 具体而言，它有助于确保不会通过圆角设备、主指示器或 iPhone X 上的传感器机架来剪裁内容。它通过将 `Page.UseSafeArea` 附加属性设置为值在 XAML 中使用 `boolean` ：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             Title="Safe Area"
             ios:Page.UseSafeArea="true">
    <StackLayout>
        ...
    </StackLayout>
</ContentPage>
```

此外，还可以使用 Fluent API 从 c # 使用该方法：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetUseSafeArea(true);
```

`Page.On<iOS>`方法指定此平台特定的仅在 iOS 上运行。 `Page.SetUseSafeArea`命名空间中的方法 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 控制是否已启用 "安全区域布局指南"。

结果就是页面内容可以定位在屏幕上的所有 Iphone 都安全的区域上：

[![安全区域布局指南](page-safe-area-images/safe-area-layout.png)](page-safe-area-images/safe-area-layout-large.png#lightbox "安全区域布局指南")

> [!NOTE]
> Apple 定义的安全区域用于 Xamarin.Forms 设置 [`Page.Padding`](xref:Xamarin.Forms.Page.Padding) 属性，并将覆盖已设置的此属性以前的任何值。

可以通过 [`Thickness`](xref:Xamarin.Forms.Thickness) 使用 `Page.SafeAreaInsets` 命名空间中的方法检索安全区域，从而对其进行自定义 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 。 然后，可以根据需要对其进行修改，并将其重新分配给 `Padding` 替代中的属性 [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) ：

```csharp
protected override void OnAppearing()
{
    base.OnAppearing();

    var safeInsets = On<iOS>().SafeAreaInsets();
    safeInsets.Left = 20;
    Padding = safeInsets;
}
```

## <a name="related-links"></a>相关链接

- [PlatformSpecifics (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)