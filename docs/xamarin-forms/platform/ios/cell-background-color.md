---
title: IOS 上的单元格背景色
description: 平台说明允许使用仅在特定平台上可用的功能，而无需实现自定义呈现器或效果。 本文介绍如何使用 iOS 平台特定的来设置 iOS 上单元格的默认背景色。
ms.prod: xamarin
ms.assetid: 2A3FDACF-5AE2-40DE-8488-6FE41733712F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a9ab12b5aabee03d84c58580ec200de4b63d5106
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562426"
---
# <a name="cell-background-color-on-ios"></a>IOS 上的单元格背景色

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平台特定的设置实例的默认背景色 [`Cell`](xref:Xamarin.Forms.Cell) 。 它通过将 `Cell.DefaultBackgroundColor` 可绑定的属性设置为来在 XAML 中使用 [`Color`](xref:Xamarin.Forms.Color) ：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ItemsSource="{Binding GroupedEmployees}"
                  IsGroupingEnabled="true">
            <ListView.GroupHeaderTemplate>
                <DataTemplate>
                    <ViewCell ios:Cell.DefaultBackgroundColor="Teal">
                        <Label Margin="10,10"
                               Text="{Binding Key}"
                               FontAttributes="Bold" />
                    </ViewCell>
                </DataTemplate>
            </ListView.GroupHeaderTemplate>
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

此外，还可以使用 Fluent API 从 c # 使用该方法：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var viewCell = new ViewCell { View = ... };
viewCell.On<iOS>().SetDefaultBackgroundColor(Color.Teal);
```

`ListView.On<iOS>`方法指定此平台特定的仅在 iOS 上运行。 `Cell.SetDefaultBackgroundColor`命名空间中的方法 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 将单元格背景色设置为指定的 [`Color`](xref:Xamarin.Forms.Color) 。 此外，该 `Cell.DefaultBackgroundColor` 方法可用于检索当前单元格背景色。

结果就是，可以将中的背景色 [`Cell`](xref:Xamarin.Forms.Cell) 设置为特定 [`Color`](xref:Xamarin.Forms.Color) ：

[![IOS 上的青色组标题单元的屏幕截图](cell-background-color-images/group-header-cell-color.png "带有蓝绿色组头单元格的 ListView")](cell-background-color-images/group-header-cell-color-large.png#lightbox "带有蓝绿色组头单元格的 ListView")

## <a name="related-links"></a>相关链接

- [PlatformSpecifics (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)