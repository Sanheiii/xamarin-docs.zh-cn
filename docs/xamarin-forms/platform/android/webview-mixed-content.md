---
title: Android 上的 Web 视图混合内容
description: 平台说明允许使用仅在特定平台上可用的功能，而无需实现自定义呈现器或效果。 本文介绍如何使用 Android 平台特定的，在面向 API 21 或更高版本的应用程序中显示 Web 视图中的混合内容。
ms.prod: xamarin
ms.assetid: 68F908F3-04C5-4B91-B6E5-B7E8357B4154
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 27d19773d86c125cd5574f7a281c37c26ef643fd
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563193"
---
# <a name="webview-mixed-content-on-android"></a>Android 上的 Web 视图混合内容

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 Android 平台特定的控制是否 [`WebView`](xref:Xamarin.Forms.WebView) 可以在面向 API 21 或更高版本的应用程序中显示混合内容。 混合内容是最初通过 HTTPS 连接加载的内容，但它通过 HTTP 连接加载 (例如图像、音频、视频、样式表、脚本) 等资源。 它通过将 [`WebView.MixedContentMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.MixedContentModeProperty) 附加属性设置为枚举的值，在 XAML 中使用 [`MixedContentHandling`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) ：

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView ... android:WebView.MixedContentMode="AlwaysAllow" />
</ContentPage>
```

此外，还可以使用 Fluent API 从 c # 使用该方法：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>().SetMixedContentMode(MixedContentHandling.AlwaysAllow);
```

`WebView.On<Android>`方法指定此平台特定的仅在 Android 上运行。 [ `WebView.SetMixedContentMode` ] (x： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. SetMixedContentMode (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。Web 视图}， Xamarin.Forms 。命名空间中的 PlatformConfiguration) # A3 方法 [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) 用于控制是否可以显示混合内容， [`MixedContentHandling`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) 枚举提供三个可能的值：

- [`AlwaysAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.AlwaysAllow) -指示 [`WebView`](xref:Xamarin.Forms.WebView) 将允许 HTTPS 源从 HTTP 源加载内容。
- [`NeverAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.NeverAllow) -指示 [`WebView`](xref:Xamarin.Forms.WebView) 将不允许 HTTPS 源从 HTTP 源加载内容。
- [`CompatibilityMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.CompatibilityMode) -指示 [`WebView`](xref:Xamarin.Forms.WebView) 将尝试与最新设备 web 浏览器的方法兼容。 某些 HTTP 内容可能允许通过 HTTPS 源加载，其他类型的内容将被阻止。 每个操作系统版本可能会更改被阻止或允许的内容类型。

结果是，将指定的 [`MixedContentHandling`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) 值应用于 [`WebView`](xref:Xamarin.Forms.WebView) ，这会控制是否可以显示混合内容：

[![Web 视图混合内容处理平台特定的](webview-mixed-content-images/webview-mixedcontent.png "Web 视图混合内容处理平台特定的")](webview-mixed-content-images/webview-mixedcontent-large.png#lightbox "Web 视图混合内容处理平台特定的")

## <a name="related-links"></a>相关链接

- [PlatformSpecifics (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)