---
title: Xamarin.Essentials：屏幕快照
description: 本文档介绍了 Xamarin.Essentials 的 Screenshot 类，通过此类，可以捕获应用当前显示的屏幕。
ms.assetid: C52AE99A-0FB3-425D-9106-3DA5777FEFA0
author: jamesmontemagno
ms.author: jamont
ms.date: 09/22/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 085da722aa2e893f97efb1c89f20b03da330ac3e
ms.sourcegitcommit: 744f977b0595f489c592e29c8a3ba548fde02b6f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2020
ms.locfileid: "91414665"
---
# <a name="no-locxamarinessentials-screenshot"></a>Xamarin.Essentials：屏幕快照

通过 Screenshot 类，可以捕获应用当前显示的屏幕。

![预发行版 API](~/media/shared/preview.png)


## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-screenshot"></a>使用屏幕截图

在类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

然后调用 `CaptureAsync`，以捕获正在运行的应用程序当前屏幕的屏幕截图。 接下来会返回 `ScreenshotResult`，然后可使用它获取所捕获的屏幕截图的 `Width`、`Height` 和 `Stream`。


```csharp
async Task CaptureScreenshot()
{
    var screenshot = await Screenshot.CaptureAsync();
    var stream = await screenshot.OpenReadAsync();

    Image = ImageSource.FromStream(() => stream);
}
```


## <a name="api"></a>API

- [Screenshot 源代码](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Screenshot)
- [屏幕截图 API 文档](xref:Xamarin.Essentials.Screenshot)
