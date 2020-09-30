---
title: Android 上的 Web 视图缩放
description: 平台说明允许使用仅在特定平台上可用的功能，而无需实现自定义呈现器或效果。 本文介绍如何使用 Android 平台特定的，该平台可对 Web 视图进行缩放。
ms.prod: xamarin
ms.assetid: DC1A3762-6A42-4298-929C-445F416C3E60
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/09/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1e41efd4bf100205f20b8f4e02a74fad193134a4
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556420"
---
# <a name="webview-zoom-on-android"></a>Android 上的 Web 视图缩放

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 Android 平台特定平台支持 "缩小到缩放" 和 "缩放" 控件 [`WebView`](xref:Xamarin.Forms.WebView) 。 它通过将 `WebView.EnableZoomControls` 和 `WebView.DisplayZoomControls` 可绑定属性设置为值，在 XAML 中使用 `boolean` ：

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView Source="https://www.xamarin.com"
             android:WebView.EnableZoomControls="true"
             android:WebView.DisplayZoomControls="true" />
</ContentPage>
```

`WebView.EnableZoomControls`可绑定属性控制是否在上启用了 "缩小到缩放" [`WebView`](xref:Xamarin.Forms.WebView) ，并 `WebView.DisplayZoomControls` 控制是否在上覆盖缩放控件 `WebView` 。

或者，可以使用 Fluent API 从 c # 中使用特定于平台的：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>()
    .EnableZoomControls(true)
    .DisplayZoomControls(true);
```

`WebView.On<Android>`方法指定此平台特定的仅在 Android 上运行。 `WebView.EnableZoomControls`命名空间中的方法 [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) 用于控制是否在上启用了 "缩小到缩放" [`WebView`](xref:Xamarin.Forms.WebView) 。 `WebView.DisplayZoomControls`同一命名空间中的方法用于控制缩放控件是否在上重叠 `WebView` 。 此外， `WebView.ZoomControlsEnabled` 和 `WebView.ZoomControlsDisplayed` 方法可用于分别返回是否启用了 "缩小到缩放" 和 "缩放" 控件。

因此，可以在上启用 "缩小到缩放 [`WebView`](xref:Xamarin.Forms.WebView) "，并在上重叠缩放控件 `WebView` ：

[![Android 上缩放的 Web 视图的屏幕截图](webview-zoom-controls-images/webview-zoom.png "缩放的 Web 视图")](webview-zoom-controls-images/webview-zoom-large.png#lightbox "缩放的 Web 视图")

> [!IMPORTANT]
> 缩放控件必须通过各自的可绑定属性或方法启用和显示，才能在上重叠 [`WebView`](xref:Xamarin.Forms.WebView) 。

## <a name="related-links"></a>相关链接

- [PlatformSpecifics (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)