---
title: IOS 上的页面状态栏可见性
description: 平台说明允许使用仅在特定平台上可用的功能，而无需实现自定义呈现器或效果。 本文介绍如何使用 iOS 平台特定的来设置页面上状态栏的可见性。
ms.prod: xamarin
ms.assetid: D8BB7C24-A27F-4758-8557-6A81F909ABD9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 65bdeef161ce4bfed18ca07966a0ab3189c65ba2
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556706"
---
# <a name="page-status-bar-visibility-on-ios"></a>IOS 上的页面状态栏可见性

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平台特定用于设置上状态栏的可见性 [`Page`](xref:Xamarin.Forms.Page) ，并且它包括控制状态栏进入或离开的方式 `Page` 。 它通过将 `Page.PrefersStatusBarHidden` 附加属性设置为枚举的值 `StatusBarHiddenMode` ，并根据需要将 `Page.PreferredStatusBarUpdateAnimation` 附加属性设置为枚举的值，以在 XAML 中使用 `UIStatusBarAnimation` ：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.PrefersStatusBarHidden="True"
             ios:Page.PreferredStatusBarUpdateAnimation="Fade">
  ...
</ContentPage>
```

此外，还可以使用 Fluent API 从 c # 使用该方法：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetPrefersStatusBarHidden(StatusBarHiddenMode.True)
         .SetPreferredStatusBarUpdateAnimation(UIStatusBarAnimation.Fade);
```

`Page.On<iOS>`方法指定此平台特定的仅在 iOS 上运行。 `Page.SetPrefersStatusBarHidden`命名空间中的方法 `Xamarin.Forms.PlatformConfiguration.iOSSpecific` 可通过指定枚举值之一来设置上状态栏的可见性 [`Page`](xref:Xamarin.Forms.Page) `StatusBarHiddenMode` ： `Default` 、 `True` 或 `False` 。 `StatusBarHiddenMode.True`和 `StatusBarHiddenMode.False` 值设置状态栏可见性，而不考虑设备方向，而 `StatusBarHiddenMode.Default` 值则在垂直紧凑型环境中隐藏状态栏。

结果是可以设置上状态栏的可见性 [`Page`](xref:Xamarin.Forms.Page) ：

![状态栏可见性平台特定](page-status-bar-visibility-images/hide-status-bar.png)

> [!NOTE]
> 在上 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) ，指定的 `StatusBarHiddenMode` 枚举值还将更新所有子页面上的状态栏。 对于所有其他 [`Page`](xref:Xamarin.Forms.Page) 派生类型，指定的 `StatusBarHiddenMode` 枚举值将仅更新当前页面上的状态栏。

`Page.SetPreferredStatusBarUpdateAnimation`方法用于通过指定枚举值之一来设置状态栏进入或离开的方式 [`Page`](xref:Xamarin.Forms.Page) `UIStatusBarAnimation` ： `None` 、 `Fade` 或 `Slide` 。 如果 `Fade` 指定了或 `Slide` 枚举值，则会在状态栏进入或离开时执行0.25 秒动画 `Page` 。

## <a name="related-links"></a>相关链接

- [PlatformSpecifics (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)