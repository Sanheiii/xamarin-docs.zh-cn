---
title: IOS 上的 TimePicker 项选择
description: 平台说明允许使用仅在特定平台上可用的功能，而无需实现自定义呈现器或效果。 本文介绍如何使用特定于 iOS 平台的来控制何时在 TimePicker 中进行项目选择。
ms.prod: xamarin
ms.assetid: 554AC877-8698-4B8C-A676-80DD64305A06
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/15/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 372c268c13c50719953ac63bcc43cc8d9bff4bdd
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563713"
---
# <a name="timepicker-item-selection-on-ios"></a>IOS 上的 TimePicker 项选择

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平台特定的控件：当项在中发生选择时 [`TimePicker`](xref:Xamarin.Forms.TimePicker) ，允许用户指定在浏览控件中的项时或在按 " **完成** " 按钮后所做的选择。 它通过将 `TimePicker.UpdateMode` 附加属性设置为枚举的值，在 XAML 中使用 `UpdateMode` ：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
       <TimePicker Time="14:00:00"
                   ios:TimePicker.UpdateMode="WhenFinished" />
       ...
    </StackLayout>
</ContentPage>
```

此外，还可以使用 Fluent API 从 c # 使用该方法：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

timePicker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
```

`TimePicker.On<iOS>`方法指定此平台特定的仅在 iOS 上运行。 `TimePicker.SetUpdateMode`命名空间中的方法 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 用于控制项的选择发生的时间， `UpdateMode` 枚举提供了两个可能的值：

- `Immediately` –当用户浏览中的项时，将发生项选择 [`TimePicker`](xref:Xamarin.Forms.TimePicker) 。 这是中的默认行为 Xamarin.Forms 。
- `WhenFinished` -仅当用户按下了 " **完成** " 按钮后，才会进行项目选择 [`TimePicker`](xref:Xamarin.Forms.TimePicker) 。

此外，该 `SetUpdateMode` 方法可用于通过调用返回当前的方法来切换枚举值 `UpdateMode` `UpdateMode` ：

```csharp
switch (timePicker.On<iOS>().UpdateMode())
{
    case UpdateMode.Immediately:
        timePicker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
        break;
    case UpdateMode.WhenFinished:
        timePicker.On<iOS>().SetUpdateMode(UpdateMode.Immediately);
        break;
}
```

结果是，将指定 `UpdateMode` 应用于 [`TimePicker`](xref:Xamarin.Forms.TimePicker) ，后者控制何时发生项选择：

[![TimePicker 更新模式的屏幕截图](timepicker-selection-images/timepicker-updatemode.png "TimePicker UpdateMode 特定于平台")](timepicker-selection-images/timepicker-updatemode-large.png#lightbox "TimePicker UpdateMode 特定于平台")

## <a name="related-links"></a>相关链接

- [PlatformSpecifics (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)