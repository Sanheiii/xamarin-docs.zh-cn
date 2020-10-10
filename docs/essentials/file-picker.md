---
title: Xamarin.Essentials：文件选取器
description: 通过 Xamarin.Essentials 的 FilePicker 类，用户可以从设备中选取单个或多个文件。
ms.assetid: 00bdbd57-56b1-47ca-8abe-cebe1b01f61a
author: jamesmontemagno
ms.author: jamont
ms.date: 09/22/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b3ee20de5534329f9e4686c908fe923ba94f3fff
ms.sourcegitcommit: 744f977b0595f489c592e29c8a3ba548fde02b6f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2020
ms.locfileid: "91414657"
---
# <a name="no-locxamarinessentials-file-picker"></a>Xamarin.Essentials：文件选取器

通过 FilePicker 类，用户可以从设备中选取单个或多个文件。

![预发行版 API](~/media/shared/preview.png)

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

若要访问 FilePicker 功能，需要以下特定于平台的设置。

# <a name="android"></a>[Android](#tab/android)

需要具有 `ReadExternalStorage` 权限，并且必须在 Android 项目中进行配置。 可以通过以下方法添加此权限：

打开 Properties 文件夹下的 AssemblyInfo.cs 文件并添加 ：

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.ReadExternalStorage)]
```

或更新 Android 清单：

打开 Properties 文件夹下的 AndroidManifest.xml 文件，并在“manifest”节点内添加以下代码  。

```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
```

或右键单击 Android 项目并打开项目的属性。 在“Android 清单”下找到“所需权限:”区域，然后选中该权限。 这样会自动更新 AndroidManifest.xml 文件。

# <a name="ios"></a>[iOS](#tab/ios)

无需其他设置。

# <a name="uwp"></a>[UWP](#tab/uwp)

无需其他设置。

-----

## <a name="pick-file"></a>选取单个文件

通过 `FilePicker.PickAsync()` 方法，用户可以从设备中选取单个文件。 调用该方法时，你可以指定各种 `PickOptions`，从而指定要显示的标题以及允许用户选取的文件类型。 默认情况下 

```csharp
async Task<FileResult> PickAndShow(PickOptions options)
{
    try
    {
        var result = await FilePicker.PickAsync();
        if (result != null)
        {
            Text = $"File Name: {result.FileName}";
            if (result.FileName.EndsWith("jpg", StringComparison.OrdinalIgnoreCase) ||
                result.FileName.EndsWith("png", StringComparison.OrdinalIgnoreCase))
            {
                var stream = await result.OpenReadAsync();
                Image = ImageSource.FromStream(() => stream);
            }
        }
    }
    catch (Exception ex)
    {
        // The user canceled or something went wrong
    }
}
```

提供的默认文件类型包括 `FilePickerFileType.Images`、`FilePickerFileType.Png` 和 `FilePickerFilerType.Videos`。 可以在创建 `PickOptions` 时指定自定义文件类型，并且可以根据平台自定义文件类型。 下面的示例说明了如何指定特定 comic 文件类型：

```csharp
var customFileType =
    new FilePickerFileType(new Dictionary<DevicePlatform, IEnumerable<string>>
    {
        { DevicePlatform.iOS, new[] { "public.my.comic.extension" } }, // or general UTType values
        { DevicePlatform.Android, new[] { "application/comics" } },
        { DevicePlatform.UWP, new[] { ".cbr", ".cbz" } },
        { DevicePlatform.Tizen, new[] { "*/*" } },
        { DevicePlatform.macOS, new[] { "cbr", "cbz" } }, // or general UTType values
    });
var options = new PickOptions
{
    PickerTitle = "Please select a comic file",
    FileTypes = customFileType,
};
```

## <a name="pick-multiple-files"></a>选取多个文件

如果希望用户可以选择多个文件，则可以调用 `FilePicker.PickMultipleAsync()` 方法。 该方法也采用 `PickOptions` 作为参数，来指定其他信息。 结果与 `PickAsync` 相同，但返回的内容不是单个 `FileResult`，而是一个可循环访问的 `IEnumerable<FileResult>`。

## <a name="api"></a>API

- [FilePicker 源代码](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/FilePicker)
- [FilePicker API 文档](xref:Xamarin.Essentials.FilePicker)
