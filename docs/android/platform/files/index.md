---
title: 使用 Xamarin.Android 的文件访问
description: 本指南说明 Xamarin.Android 中的文件访问
ms.prod: xamarin
ms.assetid: FC1CFC58-B799-4DD6-8ED1-DE36B0E56856
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/23/2018
ms.openlocfilehash: 69c99d6aa132bb5326085f752b9220054e9f3e69
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453917"
---
# <a name="file-storage-and-access-with-xamarinandroid"></a>使用 Xamarin.Android 进行文件存储和访问

Android 应用的一个常见要求是对文件执行操作 &ndash; 保存图片、下载文档或导出数据以与其他程序共享。 Android（基于 Linux）通过提供文件存储空间来支持这一点。 Android 将文件系统分为两种不同类型的存储：

* **内部存储** &ndash; 即文件系统中只能由应用程序或操作系统访问的部分。
* **外部存储** &ndash; 一个用于文件存储的分区，可由所有应用、用户以及可能由其他设备访问。 在某些设备上，外部存储可能是可移动存储（例如 SD 卡）。

这些分组只是概念性的，不一定指设备上的单个分区或目录。 Android 设备始终为内部存储和外部存储提供分区。 某些设备可能具有多个被视为外部存储器的分区。 无论分区如何，用于读取、写入或创建文件的 API 都是相同的。 Xamarin.Android 应用程序可以使用两组 API 来访问文件：

1. **.NET API（由 Mono 提供、由 Xamarin.Android 包装）** &ndash; 包括 [Xamarin.Essentials](~/essentials/index.md?context=xamarin/android) 提供的[文件系统帮助程序](~/essentials/file-system-helpers.md?context=xamarin/android)。 .NET API 提供最佳的跨平台兼容性，因此本指南的重点是这些 API。
1. **本机 Java 文件访问 API（由 Java 提供、由 Xamarin.Android 包装）** &ndash; Java 提供自己的 API，用于读取和写入文件。 它们是 .NET API 的完全可接受的替代项，但特定于 Android，不适用于打算跨平台的应用。

在 Xamarin.Android 中，对文件的读取和写入几乎与任何其他 .NET 应用程序完全相同。 Xamarin.Android 应用会确定将操作的文件的路径，然后使用标准 .NET 习惯进行文件访问。 由于内部和外部存储的实际路径可能因设备或 Android 版本而异，因此不建议对文件路径进行硬编码。 而是建议使用 Xamarin.Android API 来确定文件的路径。 这样，用于读写文件的 .NET API 公开有助于确定内部和外部存储上文件的路径的本机 Android API。

在讨论与文件访问相关的 API 之前，了解与内外部存储相关的一些详细信息非常重要。 下一节将对此进行讨论。

## <a name="internal-vs-external-storage"></a>内部存储和外部存储

从概念上讲，内部存储和外部存储非常相似 &ndash; 它们都是 Xamarin.Android 应用可能用于保存文件的位置。 这种相似性可能会让不熟悉 Android 的开发人员感到困惑，因为他们不清楚应用应该何时使用内部存储，何时使用外部存储。

内部存储是指 Android 分配给操作系统、APK 和单个应用的非易失性存储。 此空间只能由操作系统或应用访问。 Android 将为每个应用在内部存储分区中分配一个目录。 卸载应用时，系统也将删除保存在该目录中的内部存储上的所有文件。 内部存储最适合于只能由应用访问的文件，这些文件不会与其他应用共享，或者在卸载应用后几乎没有价值。 在 Android 6.0 或更高版本上，Google 可以使用 [Android 6.0 中的自动备份功能](https://developer.android.com/guide/topics/data/autobackup)自动备份内部存储上的文件。 内部存储具有以下缺点：

* 无法共享文件。
* 卸载应用时，将删除这些文件。
* 内部存储上可用的空间可能有限。

外部存储指不在内部存储上，且应用不能以独占方式访问的文件存储。 外部存储的主要用途是提供一个位置，用于放置打算在应用之间共享的文件，或是太大而无法在内部存储中容纳的文件。 外部存储的优点是它拥有的空间往往比内部存储大得多。 然而，我们不能确保外部存储始终位于设备上，并且可能需要用户提供的特殊特权才能访问它。

> [!NOTE]
> 对于支持多个用户的设备，Android 将在内外部存储上为每个用户提供自己的目录。 设备上的其他用户无法访问此目录。 只要应用不对内部或外部存储上文件的路径进行硬编码，这种分离对它们就不可见。

根据经验，Xamarin.Android 应用会在合理的情况下将文件保存在内部存储中，在需要与其他应用共享文件、文件非常大或即使卸载应用也要保留文件时使用外部存储。 例如，配置文件最适合内部存储，因为它只对创建它的应用而言重要。  与此相反，照片非常适合用于外部存储。 它们可能会很大，在许多情况下，用户可能希望共享或访问它们，即使应用已卸载。

本指南将重点介绍内部存储。 有关在 Xamarin.Android 应用程序中使用外部存储的详细信息，请参阅[外部存储](~/android/platform/files/external-storage.md)指南。

## <a name="working-with-internal-storage"></a>使用内部存储

应用程序的内部存储目录由操作系统确定，并由 `Android.Content.Context.FilesDir` 属性向 Android 应用公开。 这将返回一个 `Java.IO.File` 对象，表示 Android 专用于应用的目录。  例如，包名称为 com.companyname 的应用的内部存储目录可能是：

```bash
/data/user/0/com.companyname/files
```

本文档将内部存储目录称为 INTERNAL\_STORAGE。

> [!IMPORTANT]
> 内部存储目录的确切路径可能因设备和 Android 版本而异。 因此，应用不得对内部文件存储目录的路径进行硬编码，而应使用 Xamarin.Android API（例如 `System.Environment.GetFolderPath()`）。

为了最大限度地实现代码共享，Xamarin.Android 应用（或者面向 Xamarin.Android 的 Xamarin.Forms 应用）应该使用 [`System.Environment.GetFolderPath()`](xref:System.Environment.GetFolderPath*) 方法。 在 Xamarin.Android 中，此方法将返回与 `Android.Content.Context.FilesDir` 位置相同的目录的字符串。 此方法采用枚举 `System.Environment.SpecialFolder`，用于标识一组枚举常数，这些常数表示操作系统使用的特殊文件夹的路径。 并非所有 `System.Environment.SpecialFolder` 值都将映射到 Xamarin.Android 上的有效目录。 下表说明可能从已知值 `System.Environment.SpecialFolder` 得到的预期路径：

| System.Environment.SpecialFolder | 路径  |
|----------------------|---|
| `ApplicationData` | **_INTERNAL\_STORAGE_/.config** |
| `Desktop` | **_INTERNAL\_STORAGE_/Desktop** |
| `LocalApplicationData` | **_INTERNAL\_STORAGE_/.local/share** |
| `MyDocuments` | **_INTERNAL\_STORAGE_** |
| `MyMusic` | **_INTERNAL\_STORAGE_/Music** |
| `MyPictures` | **_INTERNAL\_STORAGE_/Pictures** |
| `MyVideos` | **_INTERNAL\_STORAGE_/Videos** |
| `Personal` | **_INTERNAL\_STORAGE_** |
| `Fonts` | **_INTERNAL\_STORAGE_/.fonts** |
| `Templates` | **_INTERNAL\_STORAGE_/Templates** |
| `CommonApplicationData` | **/usr/share** |
| `CommonApplicationData` | **/usr/share** |

### <a name="reading-or-writing-to-files-on-internal-storage"></a>内部存储上的文件读写

任何[用于写入文件的 C# API](/dotnet/csharp/programming-guide/file-system/how-to-write-to-a-text-file) 都可以；要执行的操作只是获取分配给应用程序的目录中的文件的路径。 强烈建议使用 .NET API 的异步版本，以尽量减少与阻止主线程的文件访问相关的任何问题。

以下代码片段是将整数写入应用程序内部存储目录的 UTF-8 文本文件的示例：

```csharp
public async Task SaveCountAsync(int count)
{
    var backingFile = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.Personal), "count.txt");
    using (var writer = File.CreateText(backingFile))
    {
        await writer.WriteLineAsync(count.ToString());
    }
}
```

下一个代码片段提供一种读取存储在文本文件中的整数值的方法：

```csharp
public async Task<int> ReadCountAsync()
{
    var backingFile = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.Personal), "count.txt");

    if (backingFile == null || !File.Exists(backingFile))
    {
        return 0;
    }

    var count = 0;
    using (var reader = new StreamReader(backingFile, true))
    {
        string line;
        while ((line = await reader.ReadLineAsync()) != null)
        {
            if (int.TryParse(line, out var newcount))
            {
                count = newcount;
            }
        }
    }

    return count;
}
```

## <a name="using--xamarinessentials-ndash-file-system-helpers"></a>使用 Xamarin.Essentials &ndash; 文件系统帮助程序

[Xamarin.Essentials](~/essentials/file-system-helpers.md?context=xamarin/android) 是一组用于编写跨平台兼容代码的 API。 [文件系统帮助程序](~/essentials/file-system-helpers.md?context=xamarin/android)是一个包含一系列帮助程序的类，用于简化对应用程序缓存和数据目录的定位。 此代码片段提供如何查找应用的内部存储目录和缓存目录的示例：

```csharp
// Get the path to a file on internal storage
var backingFile = Path.Combine(Xamarin.Essentials.FileSystem.AppDataDirectory, "count.txt");

// Get the path to a file in the cache directory
var cacheFile = Path.Combine(Xamarin.Essentials.FileSystem.CacheDirectory, "count.txt");
```

## <a name="hiding-files-from-the-mediastore"></a>从 `MediaStore` 隐藏文件

`MediaStore` 是一种 Android 组件，用于收集 Android 设备上与媒体文件（视频、音乐、图像）相关的元数据。 它的目的是简化这些文件在设备上所有 Android 应用之间的共享。

专用文件不会显示为可共享媒体。 例如，如果应用将图片保存到其专用外部存储中，则媒体扫描程序 (`MediaStore`) 不会提取该文件。

`MediaStore` 将提取公用文件。 `MediaStore` 不会扫描拥有扩展名为 .NOMEDIA 的零字节文件的目录。

## <a name="related-links"></a>相关链接

* [外部存储](~/android/platform/files/external-storage.md)
* [在设备存储上保存文件](https://developer.android.com/training/data-storage/files)
* [Xamarin.Essentials 文件系统帮助程序](~/essentials/file-system-helpers.md?context=xamarin/android)
* [使用自动备份备份用户数据](https://developer.android.com/guide/topics/data/autobackup)
* [可采用的存储](https://source.android.com/devices/storage/adoptable)