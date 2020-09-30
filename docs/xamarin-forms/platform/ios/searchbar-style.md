---
title: IOS 上的 SearchBar 样式
description: 平台说明允许使用仅在特定平台上可用的功能，而无需实现自定义呈现器或效果。 本文介绍如何使用特定于 iOS 平台的来控制 SearchBar 是否具有背景。
ms.prod: xamarin
ms.assetid: 3D512DD6-078E-4BC6-926E-62BA6F4DE640
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/05/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a9eacc76fb3da6296039a713e15c4eaa30828d44
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91560957"
---
# <a name="searchbar-style-on-ios"></a>IOS 上的 SearchBar 样式

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平台特定控制是否 [`SearchBar`](xref:Xamarin.Forms.SearchBar) 具有背景。 它通过将 `SearchBar.SearchBarStyle` 可绑定的属性设置为枚举的值，在 XAML 中使用 `UISearchBarStyle` ：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <SearchBar ios:SearchBar.SearchBarStyle="Minimal"
                   Placeholder="Enter search term" />
        ...
    </StackLayout>
</ContentPage>
```

此外，还可以使用 Fluent API 从 c # 使用该方法：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

SearchBar searchBar = new SearchBar { Placeholder = "Enter search term" };
searchBar.On<iOS>().SetSearchBarStyle(UISearchBarStyle.Minimal);
```

`SearchBar.On<iOS>`方法指定此平台特定的仅在 iOS 上运行。 `SearchBar.SetSearchBarStyle`命名空间中的方法 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 用于控制是否 [`SearchBar`](xref:Xamarin.Forms.SearchBar) 具有背景。 `UISearchBarStyle`枚举提供了三个可能的值：

- `Default` 指示 [`SearchBar`](xref:Xamarin.Forms.SearchBar) 具有默认样式。 这是可绑定属性的默认值 `SearchBar.SearchBarStyle` 。
- `Prominent` 指示 [`SearchBar`](xref:Xamarin.Forms.SearchBar) 具有半透明背景，搜索字段不透明。
- `Minimal` 指示没有 [`SearchBar`](xref:Xamarin.Forms.SearchBar) 背景，搜索字段为半透明。

此外， `SearchBar.GetSearchBarStyle` 方法可用于返回 `UISearchBarStyle` 应用于的 `SearchBar` 。

结果是将指定的 `UISearchBarStyle` 成员应用于 [`SearchBar`](xref:Xamarin.Forms.SearchBar) ，后者控制是否 `SearchBar` 具有背景：

![IOS 上的 SearchBar 样式屏幕截图](searchbar-style-images/searchbar-styles.png "IOS 上的 SearchBar 样式")

以下屏幕截图显示了 `UISearchBarStyle` 应用于 [`SearchBar`](xref:Xamarin.Forms.SearchBar) 其属性集的对象的成员 `BackgroundColor` ：

![IOS 上具有背景色的 SearchBar 样式屏幕截图](searchbar-style-images/searchbar-background-styles.png "IOS 上带有背景色的 SearchBar 样式")

## <a name="related-links"></a>相关链接

- [PlatformSpecifics (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)