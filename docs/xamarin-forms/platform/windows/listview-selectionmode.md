---
title: Windows 上的 ListView SelectionMode
description: 平台说明允许使用仅在特定平台上可用的功能，而无需实现自定义呈现器或效果。 本文介绍如何使用特定于 Windows 平台的来控制 ListView 中的项目是否可以响应点击手势。
ms.prod: xamarin
ms.assetid: 57EF3A7F-1407-4B31-AE21-D149293D4228
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: de412e064fa84e516dcb8e9b604068c84a2689e6
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563609"
---
# <a name="listview-selectionmode-on-windows"></a>Windows 上的 ListView SelectionMode

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

在通用 Windows 平台上，默认情况下， Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) 使用本机 `ItemClick` 事件来响应交互，而不是本机 `Tapped` 事件。 这提供了辅助功能，使 Windows 讲述人和键盘可以与进行交互 `ListView` 。 但是，它还会在不可操作的内呈现任何点击手势 `ListView` 。

此通用 Windows 平台平台特定的控制中的项是否 [`ListView`](xref:Xamarin.Forms.ListView) 可响应点击手势，并因此是否 `ListView` 引发 `ItemClick` 或 `Tapped` 事件。 它通过将 [`ListView.SelectionMode`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.SelectionModeProperty) 附加属性设置为枚举的值，在 XAML 中使用 [`ListViewSelectionMode`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) ：

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <ListView ... windows:ListView.SelectionMode="Inaccessible">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

此外，还可以使用 Fluent API 从 c # 使用该方法：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

listView.On<Windows>().SetSelectionMode(ListViewSelectionMode.Inaccessible);
```

`ListView.On<Windows>`方法指定此平台特定的仅在通用 Windows 平台上运行。 [ `ListView.SetSelectionMode` ] (x： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. SetSelectionMode (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration、 Xamarin.Forms 。ListView}， Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. ListViewSelectionMode) # A3 方法在 [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) 命名空间中，用来控制中的项是否 [`ListView`](xref:Xamarin.Forms.ListView) 可响应点击手势，并 [`ListViewSelectionMode`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) 提供可提供两个可能值的枚举：

- [`Accessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Accessible) -指示 `ListView` 将激发本机 `ItemClick` 事件来处理交互，因此提供了辅助功能。 因此，Windows 讲述人和键盘可以与进行交互 `ListView` 。 但是，中的项 `ListView` 无法响应点击手势。 这是通用 Windows 平台上的实例的默认行为 `ListView` 。
- [`Inaccessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Inaccessible) -指示 `ListView` 将激发本机 `Tapped` 事件来处理交互。 因此，中的项 `ListView` 可以响应点击手势。 但是，没有可访问的功能，因此 Windows 讲述人和键盘无法与交互 `ListView` 。

> [!NOTE]
> `Accessible`和 `Inaccessible` 选择模式是互斥的，你将需要在 [`ListView`](xref:Xamarin.Forms.ListView) `ListView` 可响应点击手势的可访问或之间进行选择。

此外，[ `GetSelectionMode` ] (x： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. GetSelectionMode (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration、 Xamarin.Forms 。ListView} ) # A3 方法可用于返回当前 [`ListViewSelectionMode`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) 。

结果是，指定的 [`ListViewSelectionMode`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) 将应用于 [`ListView`](xref:Xamarin.Forms.ListView) ，后者控制中的项是否 `ListView` 可响应点击手势，并因此是否 `ListView` 引发 `ItemClick` 或 `Tapped` 事件。

## <a name="related-links"></a>相关链接

- [PlatformSpecifics (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [WindowsSpecific API](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)