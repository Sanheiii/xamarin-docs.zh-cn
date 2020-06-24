---
title: Xamarin.Essentials 打开浏览器
description: Xamarin.Essentials 中的 Browser 类允许应用程序在优化的系统首选浏览器或外部浏览器中打开 Web 链接。
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 04/02/2019
ms.custom: video
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 997c6b66b5dba43eb440130f3f58d31a5a274815
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2020
ms.locfileid: "84802245"
---
# <a name="xamarinessentials-browser"></a>Xamarin.Essentials：浏览者

Browser 类允许应用程序在优化的系统首选浏览器或外部浏览器中打开 Web 链接。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

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
