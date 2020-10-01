---
title: Xamarin.Essentials：手电筒
description: 本文档介绍 Xamarin.Essentials 中的 Flashlight 类，你可使用此类打开或关闭设备的照相机闪光灯，将其转换为手电筒。
ms.assetid: 06A03553-D212-43A2-9E6E-C2D2D93EB136
author: jamesmontemagno
ms.custom: video
ms.author: jamont
ms.date: 11/04/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a7341137d7f7ddf1d0db47672af8810380805478
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434359"
---
# <a name="no-locxamarinessentials-flashlight"></a>Xamarin.Essentials：手电筒

Flashlight 类，此类使你能够打开或关闭设备的照相机闪光灯，将其转换为一个手电筒。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

若要访问 Flashlight 功能，需要以下特定于平台的设置。

# <a name="android"></a>[Android](#tab/android)

需要具有 Flashlight 和 Camera 权限，并且必须在 Android 项目中进行配置。 可以通过以下方法添加权限：

打开 Properties 文件夹下的 AssemblyInfo.cs 文件并添加 ：

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Flashlight)]
[assembly: UsesPermission(Android.Manifest.Permission.Camera)]
```

或更新 Android 清单：

打开 Properties 文件夹下的 AndroidManifest.xml 文件，并在“manifest”节点内添加以下代码  。

```xml
<uses-permission android:name="android.permission.FLASHLIGHT" />
<uses-permission android:name="android.permission.CAMERA" />
```

或右键单击 Android 项目并打开项目的属性。 在“Android 清单”下找到“所需权限:”区域，然后选中“FLASHLIGHT”和“CAMERA”权限   。 这样会自动更新 AndroidManifest.xml 文件。

通过添加这些权限，[Google Play 将自动筛选出设备](https://developer.android.com/guide/topics/manifest/uses-feature-element.html#permissions-features)，而无需任何特定硬件。 可以通过将以下代码添加到 Android 项目中的 AssemblyInfo.cs 文件中来绕过此操作：

```csharp
[assembly: UsesFeature("android.hardware.camera", Required = false)]
[assembly: UsesFeature("android.hardware.camera.autofocus", Required = false)]
```

[!include[](~/essentials/includes/android-permissions.md)]

# <a name="ios"></a>[iOS](#tab/ios)

无需其他设置。

# <a name="uwp"></a>[UWP](#tab/uwp)

无需其他设置。

-----

## <a name="using-flashlight"></a>使用 Flashlight

在类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

可以通过 `TurnOnAsync` 和 `TurnOffAsync` 方法来打开或关闭手电筒：

```csharp
try
{
    // Turn On
    await Flashlight.TurnOnAsync();

    // Turn Off
    await Flashlight.TurnOffAsync();
}
catch (FeatureNotSupportedException fnsEx)
{
    // Handle not supported on device exception
}
catch (PermissionException pEx)
{
    // Handle permission exception
}
catch (Exception ex)
{
    // Unable to turn on/off flashlight
}
```

## <a name="platform-implementation-specifics"></a>平台实现细节

### <a name="android"></a>[Android](#tab/android)

Flashlight 类已根据设备的操作系统进行了优化。

#### <a name="api-level-23-and-higher"></a>API 级别 23 及更高版本

在更新的 API 级别上，[Torch 模式](https://developer.android.com/reference/android/hardware/camera2/CameraManager.html#setTorchMode)将用于打开或关闭设备的闪光单元。

#### <a name="api-level-22-and-lower"></a>API 级别 22 及更高版本

创建一个相机表面纹理以打开或关闭相机单元的 `FlashMode`。

### <a name="ios"></a>[iOS](#tab/ios)

[AVCaptureDevice](xref:AVFoundation.AVCaptureDevice) 用于打开和关闭设备的 Torch 和 Flash 模式。

### <a name="uwp"></a>[UWP](#tab/uwp)

[Lamp](/uwp/api/windows.devices.lights.lamp) 用于检测设备背面的第一盏灯是否打开或关闭。

-----

## <a name="api"></a>API

- [Flashlight 源代码](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Flashlight)
- [Flashlight API 文档](xref:Xamarin.Essentials.Flashlight)

## <a name="related-video"></a>相关视频

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Flashlight-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]