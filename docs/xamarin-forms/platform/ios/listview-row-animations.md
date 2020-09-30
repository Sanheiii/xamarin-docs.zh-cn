---
title: IOS 上的 ListView 行动画
description: 平台说明允许使用仅在特定平台上可用的功能，而无需实现自定义呈现器或效果。 本文介绍如何使用特定于 iOS 平台的来控制在更新 ListView 项集合时是否禁用行动画。
ms.prod: xamarin
ms.assetid: E8F5103F-4D8E-4A5A-A16C-7FA14EE786AC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/21/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 53930f6b6b83ea855db9538f19e88482a4d1a82f
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91557447"
---
# <a name="listview-row-animations-on-ios"></a>IOS 上的 ListView 行动画

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平台特定控制在更新项集合时是否禁用行动画 [`ListView`](xref:Xamarin.Forms.ListView) 。 它通过将 `ListView.RowAnimationsEnabled` 可绑定的属性设置为来在 XAML 中使用 `false` ：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ... ios:ListView.RowAnimationsEnabled="false">
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

listView.On<iOS>().SetRowAnimationsEnabled(false);
```

`ListView.On<iOS>`方法指定此平台特定的仅在 iOS 上运行。 `ListView.SetRowAnimationsEnabled`命名空间中的方法 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 用于控制在更新项集合时是否禁用行动画 [`ListView`](xref:Xamarin.Forms.ListView) 。 此外， `ListView.GetRowAnimationsEnabled` 方法可用于返回是否在上禁用了行动画 `ListView` 。

> [!NOTE]
> [`ListView`](xref:Xamarin.Forms.ListView) 默认情况下启用行动画。 因此，当将新行插入到时，会发生动画 `ListView` 。

## <a name="related-links"></a>相关链接

- [PlatformSpecifics (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)