---
title: Xamarin.Essentials：振动
description: 本文档介绍 Xamarin.Essentials 中的 Vibration 类，你可通过此类在所需的时间内启动和停止振动功能。
ms.assetid: 7E8B24C4-2625-4DAE-A129-383542D34F1E
author: jamesmontemagno
ms.custom: video
ms.author: jamont
ms.date: 11/04/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 77ef074ab23532249e7a22bdd0cf8ecd429120a3
ms.sourcegitcommit: 744f977b0595f489c592e29c8a3ba548fde02b6f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2020
ms.locfileid: "91410670"
---
# <a name="no-locxamarinessentials-vibration"></a>Xamarin.Essentials：振动

Vibration 类使你能够在所需的时间内启动和停止振动功能。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

若要访问 Vibration 功能，需要以下特定于平台的设置。

# <a name="android"></a>[Android](#tab/android)

需要具有 Vibrate 权限，并且必须在 Android 项目中进行配置。 可以通过以下方法添加此权限：

打开 Properties 文件夹下的 AssemblyInfo.cs 文件并添加 ：

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Vibrate)]
```

或更新 Android 清单：

打开 Properties 文件夹下的 AndroidManifest.xml 文件，并在“manifest”节点内添加以下代码  。

```xml
<uses-permission android:name="android.permission.VIBRATE" />
```

或右键单击 Android 项目并打开项目的属性。 在“Android 清单”下找到“所需权限:”区域，然后选中“VIBRATE”权限  。 这样会自动更新 AndroidManifest.xml 文件。

# <a name="ios"></a>[iOS](#tab/ios)

无需其他设置。

# <a name="uwp"></a>[UWP](#tab/uwp)

无需其他设置。

-----

## <a name="using-vibration"></a>使用 Vibration

在类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

可以通过设置所需的时间量或使用默认 500 毫秒来使用 Vibration 功能。

```csharp
try
{
    // Use default vibration length
    Vibration.Vibrate();

    // Or use specified time
    var duration = TimeSpan.FromSeconds(1);
    Vibration.Vibrate(duration);
}
catch (FeatureNotSupportedException ex)
{
    // Feature not supported on device
}
catch (Exception ex)
{
    // Other error has occurred.
}
```

可以使用 `Cancel` 方法来请求取消使用设备振动：

```csharp
try
{
    Vibration.Cancel();
}
catch (FeatureNotSupportedException ex)
{
    // Feature not supported on device
}
catch (Exception ex)
{
    // Other error has occurred.
}
```

## <a name="platform-differences"></a>平台差异

# <a name="android"></a>[Android](#tab/android)

无平台差异。

# <a name="ios"></a>[iOS](#tab/ios)

- 仅在设备设置为“响铃时振动”时振动。
- 振动时长始终为 500 毫秒。
- 无法取消振动。

# <a name="uwp"></a>[UWP](#tab/uwp)

无平台差异。

-----

## <a name="api"></a>API

- [Vibration 源代码](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Vibration)
- [Vibration API 文档](xref:Xamarin.Essentials.Vibration)

## <a name="related-video"></a>相关视频

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Vibration-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
