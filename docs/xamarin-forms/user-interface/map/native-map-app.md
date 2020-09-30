---
title: 从启动本机映射应用 Xamarin.Forms
description: 可以 Xamarin.Forms 通过启动器类从应用程序启动每个平台上的本机地图应用 Xamarin.Essentials 。
ms.prod: xamarin
ms.assetid: 5CF7CD67-3F20-4D80-B99E-D35A5FD1019A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/30/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ede34258650c378a45ee694f90de1e8b249acf71
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91559878"
---
# <a name="launch-the-native-map-app-from-no-locxamarinforms"></a>从启动本机映射应用 Xamarin.Forms

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

可以 Xamarin.Forms 通过类从应用程序启动每个平台上的本机映射应用 Xamarin.Essentials `Launcher` 。 通过此类，应用程序可以通过其自定义 URI 方案打开其他应用程序。 可以通过方法调用启动器功能 `OpenAsync` ，并传入 `string` 或 `Uri` 自变量，以表示要打开的自定义 URL 方案。 有关的详细信息 Xamarin.Essentials ，请参阅 [Xamarin.Essentials](~/essentials/index.md?context=xamarin/xamarin-forms) 。

> [!NOTE]
> 使用类的替代方法 Xamarin.Essentials `Launcher` 是使用类 `Map` 。 有关详细信息，请参阅[ Xamarin.Essentials ： Map](~/essentials/maps.md?context=xamarin/xamarin-forms)。

每个平台上的 maps 应用使用唯一的自定义 URI 方案。 有关在 iOS 上映射 URI 方案的信息，请参阅映射 developer.apple.com 上的 [链接](https://developer.apple.com/library/archive/featuredarticles/iPhoneURLScheme_Reference/MapLinks/MapLinks.html) 。 有关 Android 上 maps URI 方案的详细信息，请参阅 [Maps 开发人员指南](https://developer.android.com/guide/components/intents-common.html#Maps) 和 [适用于 android 的 Developers.android.com 上的 Google maps 意向](https://developers.google.com/maps/documentation/urls/android-intents) 。 有关通用 Windows 平台 (UWP) 上的地图 URI 方案的信息，请参阅 [启动 Windows maps 应用](/windows/uwp/launch-resume/launch-maps-app)。

## <a name="launch-the-map-app-at-a-specific-location"></a>在特定位置启动地图应用

可以通过向每个地图应用的自定义 URI 方案添加适当的查询参数来打开本机地图应用中的位置：

```csharp
if (Device.RuntimePlatform == Device.iOS)
{
    // https://developer.apple.com/library/ios/featuredarticles/iPhoneURLScheme_Reference/MapLinks/MapLinks.html
    await Launcher.OpenAsync("http://maps.apple.com/?q=394+Pacific+Ave+San+Francisco+CA");
}
else if (Device.RuntimePlatform == Device.Android)
{
    // open the maps app directly
    await Launcher.OpenAsync("geo:0,0?q=394+Pacific+Ave+San+Francisco+CA");
}
else if (Device.RuntimePlatform == Device.UWP)
{
    await Launcher.OpenAsync("bingmaps:?where=394 Pacific Ave San Francisco CA");
}
```

此示例代码会导致在每个平台上启动本机映射应用，并将映射居中于表示指定位置的 pin：

[![IOS 和 Android 上的本机映射应用屏幕截图](native-map-app-images/location.png "本机映射应用")](native-map-app-images/location-large.png#lightbox "本机映射应用")

## <a name="launch-the-map-app-with-directions"></a>按说明启动地图应用

可以通过将相应的查询参数添加到每个地图应用的自定义 URI 方案来启动显示说明的本机地图应用：

```csharp
if (Device.RuntimePlatform == Device.iOS)
{
    // https://developer.apple.com/library/ios/featuredarticles/iPhoneURLScheme_Reference/MapLinks/MapLinks.html
    await Launcher.OpenAsync("http://maps.apple.com/?daddr=San+Francisco,+CA&saddr=cupertino");
}
else if (Device.RuntimePlatform == Device.Android)
{
    // opens the 'task chooser' so the user can pick Maps, Chrome or other mapping app
    await Launcher.OpenAsync("http://maps.google.com/?daddr=San+Francisco,+CA&saddr=Mountain+View");
}
else if (Device.RuntimePlatform == Device.UWP)
{
    await Launcher.OpenAsync("bingmaps:?rtp=adr.394 Pacific Ave San Francisco CA~adr.One Microsoft Way Redmond WA 98052");
}
```

此示例代码会导致在每个平台上启动本机映射应用，并将地图居中于指定位置之间的路由：

[![IOS 和 Android 上的本机映射应用程序路由屏幕截图](native-map-app-images/directions.png "本机地图应用程序方向")](native-map-app-images/directions-large.png#lightbox "本机地图应用程序方向")

## <a name="related-links"></a>相关链接

- [地图示例](/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Xamarin.Essentials](~/essentials/index.md?context=xamarin/xamarin-forms)
- [映射链接](https://developer.apple.com/library/archive/featuredarticles/iPhoneURLScheme_Reference/MapLinks/MapLinks.html)
- [Maps 开发人员指南](https://developer.android.com/guide/components/intents-common.html#Maps)
- [适用于 Android 的 Google Maps 意向](https://developers.google.com/maps/documentation/)
- [启动 Windows 地图应用](/windows/uwp/launch-resume/launch-maps-app)