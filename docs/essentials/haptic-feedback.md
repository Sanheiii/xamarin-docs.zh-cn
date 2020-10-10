---
title: Xamarin.Essentials：Haptic 反馈
description: 本文档介绍了 Xamarin.Essentials 的 HapticFeedback 类，通过此类，可以控制设备的触觉反馈。
ms.assetid: 4462936c-4018-443b-906d-d63da6d0ed7d
author: dimonovdd
ms.author: jamont
ms.date: 09/22/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b1bf597874dc22a95ca9a3db239d9c7d2dd5658a
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436719"
---
# <a name="no-locxamarinessentials-haptic-feedback"></a>Xamarin.Essentials：Haptic 反馈

通过 HapticFeedback 类，可以控制设备的触觉反馈。

![预发行版 API](~/media/shared/preview.png)

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

若要访问 HapticFeedback 功能，需要以下特定于平台的设置。

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

无平台差异。

-----

## <a name="using-haptic-feedback"></a>使用触觉反馈

在类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

可以使用 `Click` 或 `LongPress` 反馈类型来执行触觉反馈功能。

```csharp
try
{
    // Perform click feedback
    HapticFeedback.Perform(HapticFeedbackType.Click);

    // Or use long press    
    HapticFeedback.Perform(HapticFeedbackType.LongPress);
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

## <a name="api"></a>API

- [HapticFeedback 源代码](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/HapticFeedback)
- [HapticFeedback API 文档](xref:Xamarin.Essentials.HapticFeedback)
