---
title: IOS 上的主指示器可见性
description: 平台说明允许使用仅在特定平台上可用的功能，而无需实现自定义呈现器或效果。 本文介绍如何使用 iOS 平台特定的来设置页面上主指示器的可见性。
ms.prod: xamarin
ms.assetid: F81022E0-3C6C-49C0-A000-FAF6574D3FB7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/09/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6a5249b6929b283cbbcb5ea4a4bc1f136003ea43
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563648"
---
# <a name="home-indicator-visibility-on-ios"></a>IOS 上的主指示器可见性

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平台特定用于设置上主指示器的可见性 [`Page`](xref:Xamarin.Forms.Page) 。 它通过将 [`Page.PrefersHomeIndicatorAutoHidden`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Page.PrefersHomeIndicatorAutoHiddenProperty) 可绑定的属性设置为来在 XAML 中使用 `boolean` ：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.PrefersHomeIndicatorAutoHidden="true">
    ...
</ContentPage>
```

此外，还可以使用 Fluent API 从 c # 使用该方法：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetPrefersHomeIndicatorAutoHidden(true);
```

`Page.On<iOS>`方法指定此平台特定的仅在 iOS 上运行。 [ `Page.SetPrefersHomeIndicatorAutoHidden` ] (x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. SetPrefersHomeIndicatorAutoHidden (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration、 Xamarin.Forms 。Page}，system.string) # A3 方法，在 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 命名空间中控制主指示器的可见性。 此外，[ `Page.PrefersHomeIndicatorAutoHidden` ] (x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. PrefersHomeIndicatorAutoHidden (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration、 Xamarin.Forms 。Page} ) # A3 方法可用于检索主指示器的可见性。

结果就是可以控制上的主指示器的可见性 [`Page`](xref:Xamarin.Forms.Page) ：

![IOS 页面上主指示器可见性的屏幕截图](page-home-indicator-images/home-indicator-visibility.png "主页指示器可见性")

> [!NOTE]
> 此特定于平台的可应用于 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 、 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 、 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 和 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 对象。

## <a name="related-links"></a>相关链接

- [PlatformSpecifics (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)