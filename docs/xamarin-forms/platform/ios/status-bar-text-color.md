---
title: IOS 上的 NavigationPage 栏文本颜色模式
description: 平台说明允许使用仅在特定平台上可用的功能，而无需实现自定义呈现器或效果。 本文介绍如何使用 iOS 平台特定的来控制 NavigationPage 上的状态栏文本颜色是否与导航栏的发光度相匹配。
ms.prod: xamarin
ms.assetid: 03698A44-39F1-4030-9AF5-F10A6713828A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 09b5f145b273d234322eb560207c65aab3313558
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91560789"
---
# <a name="navigationpage-bar-text-color-mode-on-ios"></a>IOS 上的 NavigationPage 栏文本颜色模式

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此特定于平台的控件控制上的状态栏文本颜色是否 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 经过调整以匹配导航栏的发光度。 它通过将 [`NavigationPage.StatusBarTextColorMode`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.StatusBarTextColorModeProperty) 附加属性设置为枚举的值，在 XAML 中使用 [`StatusBarTextColorMode`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode) ：

```xaml
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
    x:Class="PlatformSpecifics.iOSStatusBarTextColorModePage">
    <MasterDetailPage.Master>
        <ContentPage Title="Master Page Title" />
    </MasterDetailPage.Master>
    <MasterDetailPage.Detail>
        <NavigationPage BarBackgroundColor="Blue" BarTextColor="White"
                        ios:NavigationPage.StatusBarTextColorMode="MatchNavigationBarTextLuminosity">
            <x:Arguments>
                <ContentPage>
                    <Label Text="Slide the master page to see the status bar text color mode change." />
                </ContentPage>
            </x:Arguments>
        </NavigationPage>
    </MasterDetailPage.Detail>
</MasterDetailPage>

```

此外，还可以使用 Fluent API 从 c # 使用该方法：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

IsPresentedChanged += (sender, e) =>
{
    var mdp = sender as MasterDetailPage;
    if (mdp.IsPresented)
        ((Xamarin.Forms.NavigationPage)mdp.Detail)
          .On<iOS>()
          .SetStatusBarTextColorMode(StatusBarTextColorMode.DoNotAdjust);
    else
        ((Xamarin.Forms.NavigationPage)mdp.Detail)
          .On<iOS>()
          .SetStatusBarTextColorMode(StatusBarTextColorMode.MatchNavigationBarTextLuminosity);
};
```

`NavigationPage.On<iOS>`方法指定此平台特定的仅在 iOS 上运行。 [ `NavigationPage.SetStatusBarTextColorMode` ] (x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. NavigationPage. SetStatusBarTextColorMode (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration、 Xamarin.Forms 。NavigationPage}， Xamarin.Forms 。命名空间中的 PlatformConfiguration) # A3 方法 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 控制是否调整上的状态栏文本颜色 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 以匹配导航栏的发光度，并 [`StatusBarTextColorMode`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode) 提供可提供两个可能值的枚举：

- [`DoNotAdjust`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode.DoNotAdjust) –指示不应调整状态栏文本颜色。
- [`MatchNavigationBarTextLuminosity`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode.MatchNavigationBarTextLuminosity) –指示状态栏文本颜色应与导航栏的发光度匹配。

此外，[ `GetStatusBarTextColorMode` ] (x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. NavigationPage. GetStatusBarTextColorMode (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration、 Xamarin.Forms 。NavigationPage} ) # A3 方法可用于检索 [`StatusBarTextColorMode`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode) 应用到的枚举的当前值 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 。

因此，可以调整上的状态栏文本颜色 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) ，使其与导航栏的发光度相匹配。 在此示例中，当用户在 [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) 的和页面之间切换时，状态栏文本颜色会发生更改 [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) ：

![状态栏文本颜色模式特定于平台](status-bar-text-color-images/status-bar-text-color-mode.png)

## <a name="related-links"></a>相关链接

- [PlatformSpecifics (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)