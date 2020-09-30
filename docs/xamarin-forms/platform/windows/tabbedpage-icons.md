---
title: Windows 上的 TabbedPage 图标
description: 平台说明允许使用仅在特定平台上可用的功能，而无需实现自定义呈现器或效果。 本文介绍如何使用 Windows 平台特定的，该平台允许在 TabbedPage 工具栏上显示页面图标。
ms.prod: xamarin
ms.assetid: 7C5031A5-74EE-4469-994E-BEA7BA9D33CB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 60b331b01f7ba16bd2ea828d429ee64ae25766ea
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91560268"
---
# <a name="tabbedpage-icons-on-windows"></a>Windows 上的 TabbedPage 图标

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此通用 Windows 平台平台特定，可以在工具栏上显示页面图标，还可以 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 选择指定图标大小。 它通过将 [`TabbedPage.HeaderIconsEnabled`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.HeaderIconsEnabledProperty) 附加属性设置为 `true` ，并根据需要将 [`TabbedPage.HeaderIconsSize`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.HeaderIconsSizeProperty) 附加属性设置为一个值，在 XAML 中使用 [`Size`](xref:Xamarin.Forms.Size) ：

```xaml
<TabbedPage ...
            xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
            windows:TabbedPage.HeaderIconsEnabled="true">
    <windows:TabbedPage.HeaderIconsSize>
        <Size>
            <x:Arguments>
                <x:Double>24</x:Double>
                <x:Double>24</x:Double>
            </x:Arguments>
        </Size>
    </windows:TabbedPage.HeaderIconsSize>
    <ContentPage Title="Todo" IconImageSource="todo.png">
        ...
    </ContentPage>
    <ContentPage Title="Reminders" IconImageSource="reminders.png">
        ...
    </ContentPage>
    <ContentPage Title="Contacts" IconImageSource="contacts.png">
        ...
    </ContentPage>
</TabbedPage>
```

此外，还可以使用 Fluent API 从 c # 使用该方法：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

public class WindowsTabbedPageIconsCS : Xamarin.Forms.TabbedPage
{
  public WindowsTabbedPageIconsCS()
  {
    On<Windows>().SetHeaderIconsEnabled(true);
    On<Windows>().SetHeaderIconsSize(new Size(24, 24));

    Children.Add(new ContentPage { Title = "Todo", IconImageSource = "todo.png" });
    Children.Add(new ContentPage { Title = "Reminders", IconImageSource = "reminders.png" });
    Children.Add(new ContentPage { Title = "Contacts", IconImageSource = "contacts.png" });
  }
}
```

`TabbedPage.On<Windows>`方法指定此平台特定的仅在通用 Windows 平台上运行。 [ `TabbedPage.SetHeaderIconsEnabled` ] (x： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. TabbedPage. SetHeaderIconsEnabled (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration、 Xamarin.Forms 。TabbedPage} （在命名空间中，) # A3 方法） [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) 用于打开或关闭标题图标。 [ `TabbedPage.SetHeaderIconsSize` ] (x： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. TabbedPage. SetHeaderIconsSize (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration、 Xamarin.Forms 。TabbedPage}， Xamarin.Forms 。Size) # A3 方法可选择使用值指定标头图标大小 [`Size`](xref:Xamarin.Forms.Size) 。

此外， `TabbedPage` 命名空间中的类 `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` 还提供了一个 [`EnableHeaderIcons`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.EnableHeaderIcons*) 方法，该方法可启用标头图标、 [`DisableHeaderIcons`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.DisableHeaderIcons*) 禁用标题图标的方法和 [`IsHeaderIconsEnabled`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.IsHeaderIconsEnabled*) 返回 `boolean` 指示是否启用了标头图标的值的方法。

结果是，页面图标可以显示在 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 工具栏上，并可以选择将图标大小设置为所需的大小：

![启用了平台特定的 TabbedPage 图标](tabbedpage-icons-images/tabbedpage-icons.png "启用了平台特定的 TabbedPage 图标")

## <a name="related-links"></a>相关链接

- [PlatformSpecifics (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [WindowsSpecific API](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)