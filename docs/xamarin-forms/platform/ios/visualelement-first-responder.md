---
title: IOS 上的 VisualElement 第一个响应程序
description: 平台说明允许使用仅在特定平台上可用的功能，而无需实现自定义呈现器或效果。 本文介绍如何使用特定于 iOS 平台的，使 VisualElement 对象成为触控事件的第一个响应方。
ms.prod: xamarin
ms.assetid: 3A77BA02-B87A-44EC-AC51-9D3130EF314C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/15/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ebe153ab8dfe4645b6c71fc6b973a6f001f97c0d
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91564064"
---
# <a name="visualelement-first-responder-on-ios"></a>IOS 上的 VisualElement 第一个响应程序

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平台特定 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 用于使对象成为触控事件的第一个响应方，而不是包含元素的页。 它通过将 `VisualElement.CanBecomeFirstResponder` 可绑定的属性设置为来在 XAML 中使用 `true` ：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <Entry Placeholder="Enter text" />
        <Button ios:VisualElement.CanBecomeFirstResponder="True"
                Text="OK" />
    </StackLayout>
</ContentPage>
```

此外，还可以使用 Fluent API 从 c # 使用该方法：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Entry entry = new Entry { Placeholder = "Enter text" };
Button button = new Button { Text = "OK" };
button.On<iOS>().SetCanBecomeFirstResponder(true);
```

`VisualElement.On<iOS>`方法指定此平台特定的仅在 iOS 上运行。 `VisualElement.SetCanBecomeFirstResponder`命名空间中的方法 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 用于将设置 `VisualElement` 为触控事件的第一个响应方。 此外， `VisualElement.CanBecomeFirstResponder` 可以使用方法来返回是否 `VisualElement` 为触控事件的第一个响应方。

结果是， [`VisualElement`](xref:Xamarin.Forms.VisualElement) 可以成为触控事件的第一个响应方，而不是包含该元素的页。 这样，聊天应用程序就可以在点击时关闭键盘 [`Button`](xref:Xamarin.Forms.Button) 。

## <a name="related-links"></a>相关链接

- [PlatformSpecifics (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)