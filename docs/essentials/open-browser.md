---
title: Xamarin.Essentials 打开浏览器
description: Xamarin.Essentials 中的 Browser 类允许应用程序在优化的系统首选浏览器或外部浏览器中打开 Web 链接。
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 09/24/2020
ms.custom: video
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0c38949e9c8c0a957a7afa37206683588ffbb4cf
ms.sourcegitcommit: 3a15d9b29d65139b18dcf0871fe00cffb2a56357
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91353403"
---
# <a name="no-locxamarinessentials-browser"></a>Xamarin.Essentials：浏览者

Browser 类允许应用程序在优化的系统首选浏览器或外部浏览器中打开 Web 链接。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

若要访问 Browser 功能，需要以下特定于平台的设置。

# <a name="android"></a>[Android](#tab/android)

如果项目的目标 Android 版本设置为 Android 11 (R API 30)，则必须使用与新的[包可见性要求](https://developer.android.com/preview/privacy/package-visibility)一起使用的查询来更新 Android 清单。

打开 Properties 文件夹下的 AndroidManifest.xml 文件，并在“manifest”节点内添加以下代码  ：

```xml
<queries>
  <intent>
    <action android:name="android.intent.action.VIEW" />
    <data android:scheme="http"/>
  </intent>
  <intent>
    <action android:name="android.intent.action.VIEW" />
    <data android:scheme="https"/>
  </intent>
</queries>
```

# <a name="ios"></a>[iOS](#tab/ios)

无需其他设置。

# <a name="uwp"></a>[UWP](#tab/uwp)

无平台差异。

-----

## <a name="using-browser"></a>使用 Browser

在类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

Browser 功能通过调用具有 `Uri` 和 `BrowserLaunchMode` 的 `OpenAsync` 方法工作。

```csharp

public class BrowserTest
{
    public async Task OpenBrowser(Uri uri)
    {
        await Browser.OpenAsync(uri, BrowserLaunchMode.SystemPreferred);
    }
}
```

此方法在用户启动并关闭（不一定）浏览器后返回 。  `bool` 结果表明启动是否是成功。

## <a name="customization"></a>自定义

使用系统首选浏览器时，iOS 和 Android 有多种自定义选项。 这包括 `TitleMode`（仅限 Android ），以及出现的 `Toolbar`（iOS 和 Android）和 `Controls`（仅限 iOS）的首选颜色选项。

调用 `OpenAsync` 时，使用 `BrowserLaunchOptions` 指定这些选项。

```csharp
await Browser.OpenAsync(uri, new BrowserLaunchOptions
                {
                    LaunchMode = BrowserLaunchMode.SystemPreferred,
                    TitleMode = BrowserTitleMode.Show,
                    PreferredToolbarColor = Color.AliceBlue,
                    PreferredControlColor = Color.Violet
                });
```

![浏览器选项](images/browser-options.png)

## <a name="platform-implementation-specifics"></a>平台实现细节

# <a name="android"></a>[Android](#tab/android)

启动模式确定浏览器的启动方式：

## <a name="system-preferred"></a>系统首选

[自定义选项卡](https://developer.chrome.com/multidevice/android/customtabs)将尝试用于加载 URI 并保持导航识别。

## <a name="external"></a>外部

`Intent` 将用于请求通过系统常规浏览器打开的 URI。

# <a name="ios"></a>[iOS](#tab/ios)

## <a name="system-preferred"></a>系统首选

[SFSafariViewController](xref:SafariServices.SFSafariViewController) 用于加载 URI 并保持导航识别。

## <a name="external"></a>外部

主应用程序上的标准 `OpenUrl` 用于启动应用程序之外的默认浏览器。

# <a name="uwp"></a>[UWP](#tab/uwp)

无论 `BrowserLaunchMode` 如何，将始终启动用户的默认浏览器。

--------------

## <a name="api"></a>API

- [Browser 源代码](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Browser)
- [Browser API 文档](xref:Xamarin.Essentials.Browser)

## <a name="related-video"></a>相关视频

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Open-Browser-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
