---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c65cac4c777150185524b291adc6e9d1e79958d3
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138549"
---
# <a name="datepicker-item-selection-on-ios"></a>IOS 上的 DatePicker 项选择

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平台特定的控件：当项在中发生选择时 [`DatePicker`](xref:Xamarin.Forms.DatePicker) ，允许用户指定在浏览控件中的项时或在按 "**完成**" 按钮后所做的选择。 它通过将 `DatePicker.UpdateMode` 附加属性设置为枚举的值，在 XAML 中使用 `UpdateMode` ：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
       <DatePicker MinimumDate="01/01/2020"
                   MaximumDate="12/31/2020"
                   ios:DatePicker.UpdateMode="WhenFinished" />
       ...
    </StackLayout>
</ContentPage>
```

此外，还可以使用 Fluent API 从 c # 使用该方法：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

datePicker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
```

`DatePicker.On<iOS>`方法指定此平台特定的仅在 iOS 上运行。 `DatePicker.SetUpdateMode`命名空间中的方法 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 用于控制项的选择发生的时间， `UpdateMode` 枚举提供了两个可能的值：

- `Immediately`–当用户浏览中的项时，将发生项选择 [`DatePicker`](xref:Xamarin.Forms.DatePicker) 。 这是中的默认行为 Xamarin.Forms 。
- `WhenFinished`-仅当用户按下了 "**完成**" 按钮后，才会进行项目选择 [`DatePicker`](xref:Xamarin.Forms.DatePicker) 。

此外，该 `SetUpdateMode` 方法可用于通过调用返回当前的方法来切换枚举值 `UpdateMode` `UpdateMode` ：

```csharp
switch (datePicker.On<iOS>().UpdateMode())
{
    case UpdateMode.Immediately:
        datePicker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
        break;
    case UpdateMode.WhenFinished:
        datePicker.On<iOS>().SetUpdateMode(UpdateMode.Immediately);
        break;
}
```

结果是，将指定 `UpdateMode` 应用于 [`DatePicker`](xref:Xamarin.Forms.DatePicker) ，后者控制何时发生项选择：

[![DatePicker 更新模式的屏幕截图](datepicker-selection-images/datepicker-updatemode.png "DatePicker UpdateMode 特定于平台")](datepicker-selection-images/datepicker-updatemode-large.png#lightbox "DatePicker UpdateMode 特定于平台")

## <a name="related-links"></a>相关链接

- [PlatformSpecifics （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
