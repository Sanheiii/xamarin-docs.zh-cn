---
title: 使用 Android 资产
ms.prod: xamarin
ms.assetid: 70ECDDC9-FA40-03B4-BF04-E7CFFFE4260D
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/13/2018
ms.openlocfilehash: 9c8db5ad7bcb012befb2fa8dcd1ecd13fa355a55
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025432"
---
# <a name="using-android-assets"></a>使用 Android 资产

_资产_提供了一种在应用程序中包含文本、xml、字体、音乐和视频等任意文件的方法。 如果尝试将这些文件作为 "资源" 包含，则 Android 会将它们处理到其资源系统中，你将无法获取原始数据。 如果要访问无需访问的数据，则可以采用以下方法之一。

添加到你的项目的资产将与可使用[AssetManager](xref:Android.Content.Res.AssetManager)从你的应用程序中读取的文件系统类似。
在此简单演示中，我们将向项目添加一个文本文件资产，使用 `AssetManager`读取该文件，并在 TextView 中显示它。

## <a name="add-asset-to-project"></a>向项目添加资产

资产位于项目的 `Assets` 文件夹中。 向此文件夹中添加名为 "`read_asset.txt`" 的新文本文件。 在其中放置一些文本，如 "我来自资产！"。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Visual Studio 应将此文件的**生成操作**设置为**AndroidAsset**：

![将生成操作设置为 AndroidAsset](android-assets-images/asset-properties-vs.png) 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

Visual Studio for Mac 应将此文件的**生成操作**设置为**AndroidAsset**：

[![将生成操作设置为 AndroidAsset](android-assets-images/asset-properties-xs-sml.png)](android-assets-images/asset-properties-xs.png#lightbox)

-----

选择正确的**BuildAction**可确保在编译时将该文件打包到 APK 中。

## <a name="reading-assets"></a>读取资产

使用[AssetManager](xref:Android.Content.Res.AssetManager)读取资产。 `AssetManager` 的实例可以通过访问 `Android.Content.Context`上的[资产](xref:Android.Content.Context.Assets)属性（例如活动）来获取。
在下面的代码中，我们将打开我们的**read_asset**资产，读取内容并使用 TextView 将其显示。

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Create a new TextView and set it as our view
    TextView tv = new TextView (this);
    
    // Read the contents of our asset
    string content;
    AssetManager assets = this.Assets;
    using (StreamReader sr = new StreamReader (assets.Open ("read_asset.txt")))
    {
        content = sr.ReadToEnd ();
    }

    // Set TextView.Text to our asset content
    tv.Text = content;
    SetContentView (tv);
}
```

## <a name="running-the-application"></a>运行应用程序

运行应用程序，你应该会看到以下内容：

![示例屏幕快照](android-assets-images/screenshot.png)

## <a name="related-links"></a>相关链接

- [AssetManager](xref:Android.Content.Res.AssetManager)
- [快捷](xref:Android.Content.Context)
