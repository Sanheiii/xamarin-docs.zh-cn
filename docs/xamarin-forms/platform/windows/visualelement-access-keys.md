---
title: Windows 上的 VisualElement 访问密钥
description: 平台说明允许使用仅在特定平台上可用的功能，而无需实现自定义呈现器或效果。 本文介绍如何使用特定于 Windows 平台的来指定 VisualElement 的访问密钥。
ms.prod: xamarin
ms.assetid: 771AF785-76B8-4372-89F5-E4D521D21E0C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6e25654fb856935c119d731df5db3eaa2d501930
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91560242"
---
# <a name="visualelement-access-keys-on-windows"></a>Windows 上的 VisualElement 访问密钥

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

访问密钥是通过键盘快捷方式提高应用程序的可用性和可访问性通用 Windows 平台 (UWP) ，为用户提供一种直观的方式，使用户能够通过键盘而不是通过触摸或鼠标快速导航并与应用程序的可见 UI 交互。 它们是 Alt 键和一个或多个字母数字键的组合，通常按顺序排列。 使用单个字母数字字符的访问密钥会自动支持键盘快捷方式。

访问键提示是在包含访问键的控件的旁边显示的浮动徽章。 每个访问键提示都包含激活关联控件的字母数字键。 当用户按下 Alt 键时，将显示访问键提示。

此 UWP 特定于平台的用于指定的访问键 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。 它在 XAML 中使用，方法是将 [`VisualElement.AccessKey`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyProperty) 附加属性设置为字母数字值，并根据需要将 [`VisualElement.AccessKeyPlacement`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyPlacementProperty) 附加属性设置为枚举的值，将附加属性设置为 [`AccessKeyPlacement`](xref:Xamarin.Forms.AccessKeyPlacement) [`VisualElement.AccessKeyHorizontalOffset`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyHorizontalOffsetProperty) `double` ，并将 [`VisualElement.AccessKeyVerticalOffset`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyVerticalOffsetProperty) 附加属性设置为 `double` ：

```xaml
<TabbedPage ...
            xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <ContentPage Title="Page 1"
                 windows:VisualElement.AccessKey="1">
        <StackLayout Margin="20">
            ...
            <Switch windows:VisualElement.AccessKey="A" />
            <Entry Placeholder="Enter text here"
                   windows:VisualElement.AccessKey="B" />
            ...
            <Button Text="Access key F, placement top with offsets"
                    Margin="20"
                    Clicked="OnButtonClicked"
                    windows:VisualElement.AccessKey="F"
                    windows:VisualElement.AccessKeyPlacement="Top"
                    windows:VisualElement.AccessKeyHorizontalOffset="20"
                    windows:VisualElement.AccessKeyVerticalOffset="20" />
            ...
        </StackLayout>
    </ContentPage>
    ...
</TabbedPage>
```

此外，还可以使用 Fluent API 从 c # 使用该方法：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

var page = new ContentPage { Title = "Page 1" };
page.On<Windows>().SetAccessKey("1");

var switchView = new Switch();
switchView.On<Windows>().SetAccessKey("A");
var entry = new Entry { Placeholder = "Enter text here" };
entry.On<Windows>().SetAccessKey("B");
...

var button4 = new Button { Text = "Access key F, placement top with offsets", Margin = new Thickness(20) };
button4.Clicked += OnButtonClicked;
button4.On<Windows>()
    .SetAccessKey("F")
    .SetAccessKeyPlacement(AccessKeyPlacement.Top)
    .SetAccessKeyHorizontalOffset(20)
    .SetAccessKeyVerticalOffset(20);
...
```

`VisualElement.On<Windows>`方法指定此平台特定的仅在通用 Windows 平台上运行。 [ `VisualElement.SetAccessKey` ] (x： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. VisualElement. SetAccessKey (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration、 Xamarin.Forms 。VisualElement} （在命名空间中) # A3 方法） [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) 用于设置的访问密钥值 `VisualElement` 。 [ `VisualElement.SetAccessKeyPlacement` ] (x： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. VisualElement. SetAccessKeyPlacement (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration、 Xamarin.Forms 。VisualElement}， Xamarin.Forms 。AccessKeyPlacement) # A3 方法，还可以选择指定用于显示访问键提示的位置， [`AccessKeyPlacement`](xref:Xamarin.Forms.AccessKeyPlacement) 枚举提供以下可能的值：

- [`Auto`](xref:Xamarin.Forms.AccessKeyPlacement.Auto) -指示访问密钥提示位置将由操作系统确定。
- [`Top`](xref:Xamarin.Forms.AccessKeyPlacement.Top) -指示访问键提示将显示在上边缘的上方 `VisualElement` 。
- [`Bottom`](xref:Xamarin.Forms.AccessKeyPlacement.Bottom) -指示访问键提示将显示在下方的下边缘下 `VisualElement` 。
- [`Right`](xref:Xamarin.Forms.AccessKeyPlacement.Right) -指示访问键提示将显示在右边缘的右侧 `VisualElement` 。
- [`Left`](xref:Xamarin.Forms.AccessKeyPlacement.Left) –指示访问键提示将显示在的左边缘的左侧 `VisualElement` 。
- [`Center`](xref:Xamarin.Forms.AccessKeyPlacement.Center) -指示访问键提示将显示在中心 `VisualElement` 。

> [!NOTE]
> 通常， [`Auto`](xref:Xamarin.Forms.AccessKeyPlacement.Auto) 关键提示位置就足够了，其中包括对自适应用户界面的支持。

[ `VisualElement.SetAccessKeyHorizontalOffset` ] (x： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. VisualElement. SetAccessKeyHorizontalOffset (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration、 Xamarin.Forms 。VisualElement}、system.exception) # A3 和 [ `VisualElement.SetAccessKeyVerticalOffset` ] (x： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. VisualElement. SetAccessKeyVerticalOffset (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration、 Xamarin.Forms 。VisualElement}、system.exception) # A7 方法可用于对访问密钥提示位置进行更精细的控制。 方法的参数 `SetAccessKeyHorizontalOffset` 指示访问键提示向左或向右移动的距离，并且该方法的参数 `SetAccessKeyVerticalOffset` 指示向上或向下移动访问密钥提示的距离。

>[!NOTE]
> 设置访问密钥位置时无法设置访问密钥提示偏移量 `Auto` 。

此外，[ `GetAccessKey` ] (x： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. VisualElement. GetAccessKey (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration、 Xamarin.Forms 。VisualElement} ) # A3，[ `GetAccessKeyPlacement` ] (x： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. VisualElement. GetAccessKeyPlacement (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration、 Xamarin.Forms 。VisualElement} ) # A7，[ `GetAccessKeyHorizontalOffset` ] (x： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. VisualElement. GetAccessKeyHorizontalOffset (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration、 Xamarin.Forms 。VisualElement} ) # A11，[ `GetAccessKeyVerticalOffset` ] (x： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. VisualElement. GetAccessKeyVerticalOffset (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration、 Xamarin.Forms 。VisualElement} ) # A15 方法可用于检索访问密钥值及其位置。

结果是，可以 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 通过按 Alt 键，在任何定义访问密钥的实例旁显示访问键提示：

![VisualElement 访问密钥平台特定](visualelement-access-keys-images/visualelement-accesskeys.png "VisualElement 访问密钥平台特定")

用户激活访问密钥时，按 Alt 键，然后按 "访问键"，将执行的默认操作 `VisualElement` 。 例如，当用户激活上的访问密钥时，将 [`Switch`](xref:Xamarin.Forms.Switch) `Switch` 切换。 当用户激活上的访问密钥时 [`Entry`](xref:Xamarin.Forms.Entry) ， `Entry` 获得焦点。 当用户激活上的访问键时 [`Button`](xref:Xamarin.Forms.Button) ，将执行该事件的事件处理程序 [`Clicked`](xref:Xamarin.Forms.Button.Clicked) 。

有关访问密钥的详细信息，请参阅 [访问密钥](/windows/uwp/design/input/access-keys#key-tip-positioning)。

## <a name="related-links"></a>相关链接

- [PlatformSpecifics (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [WindowsSpecific API](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)