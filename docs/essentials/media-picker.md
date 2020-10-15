---
title: Xamarin.Essentials：媒体选取器
description: 通过 Xamarin.Essentials 的 MediaPicker 类，用户可以在设备上选取或拍摄照片或视频。
ms.assetid: 23460875-6cf9-4440-a97b-46c55b0bca69
author: jamesmontemagno
ms.author: jamont
ms.date: 09/22/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7c4299abf9c461a16f67ccf3d8caf03d5e568f13
ms.sourcegitcommit: 827daa78c090bf79a1b55da45bb8012a1723b720
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91997506"
---
# <a name="no-locxamarinessentials-media-picker"></a>Xamarin.Essentials：媒体选取器

通过 MediaPicker 类，用户可以在设备上选取或拍摄照片或视频。

![预发行版 API](~/media/shared/preview.png)

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

若要访问 MediaPicker 功能，需要以下特定于平台的设置。

# <a name="android"></a>[Android](#tab/android)

需要具有以下权限，并且必须在 Android 项目中进行配置。 可以通过以下方法添加此权限：

打开 Properties 文件夹下的 AssemblyInfo.cs 文件并添加 ：

```csharp
// Needed for Picking photo/video
[assembly: UsesPermission(Android.Manifest.Permission.ReadExternalStorage)]

// Needed for Taking photo/video
[assembly: UsesPermission(Android.Manifest.Permission.WriteExternalStorage)]
[assembly: UsesPermission(Android.Manifest.Permission.Camera)]

// Add these properties if you would like to filter out cameras that do not have cameras or set to false to make them optional
[assembly: UsesFeature("android.hardware.camera", Required = true)]
[assembly: UsesFeature("android.hardware.camera.autofocus", Required = true)]
```

或更新 Android 清单：

打开 Properties 文件夹下的 AndroidManifest.xml 文件，并在“manifest”节点内添加以下代码  。

```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.CAMERA" />
```

或右键单击 Android 项目并打开项目的属性。 在“Android 清单”下找到“所需权限:”区域，然后选中这些权限。 这样会自动更新 AndroidManifest.xml 文件。

# <a name="ios"></a>[iOS](#tab/ios)

在 `Info.plist` 中添加以下项：

```xml
<key>NSCameraUsageDescription</key>
<string>This app needs access to the camera to take photos.</string>
<key>NSMicrophoneUsageDescription</key>
<string>This app needs access to microphone for taking videos.</string>
<key>NSPhotoLibraryAddUsageDescription</key>
<string>This app needs access to the photo gallery for picking photos and videos.</string>
<key>NSPhotoLibraryUsageDescription</key>
<string>This app needs access to photos gallery for picking photos and videos.</string>
```

确保将每项中的 `<string>` 更新为应用程序的特定说明，因为它将向用户显示。

# <a name="uwp"></a>[UWP](#tab/uwp)

在“功能”下的 `Package.appxmanifest` 中，确保已选中了 `Microphone` 和 `Webcam` 功能。

-----

## <a name="using-media-picker"></a>使用媒体选取器

`MediaPicker` 类包含以下方法，这些方法都会返回一个 `FileResult`，可使用它来获取文件位置，也可以将它作为 `Stream` 读取。

* `PickPhotoAsync`：打开媒体浏览器以选择照片。
* `CapturePhotoAsync`：打开相机以拍摄照片。
* `PickVideoAsync`：打开媒体浏览器以选择视频。
* `CaptureVideoAsync`：打开相机以拍摄视频。

每个方法都可以选择采用 `MediaPickerOptions` 参数，通过该参数，可以在某些操作系统上设置 `Title` 来向用户显示。

## <a name="general-usage"></a>常规使用情况

```csharp
async Task TakePhotoAsync()
{
    try
    {
        var photo = await MediaPicker.CapturePhotoAsync();
        await LoadPhotoAsync(photo);
        Console.WriteLine($"CapturePhotoAsync COMPLETED: {PhotoPath}");
    }
    catch (Exception ex)
    {
        Console.WriteLine($"CapturePhotoAsync THREW: {ex.Message}");
    }
}

async Task LoadPhotoAsync(FileResult photo)
{
    // canceled
    if (photo == null)
    {
        PhotoPath = null;
        return;
    }
    // save the file into local storage
    var newFile = Path.Combine(FileSystem.CacheDirectory, photo.FileName);
    using (var stream = await photo.OpenReadAsync())
    using (var newStream = File.OpenWrite(newFile))
        await stream.CopyToAsync(newStream);

    PhotoPath = newFile;
}
```


## <a name="api"></a>API

- [MediaPicker 源代码](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/MediaPicker)
- [MediaPicker API 文档](xref:Xamarin.Essentials.MediaPicker)
