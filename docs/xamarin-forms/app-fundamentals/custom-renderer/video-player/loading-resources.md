---
title: 加载应用程序资源视频
description: 本文介绍如何使用 Xamarin.Forms 在视频播放器应用程序中加载以应用程序资源形式存储的视频。
ms.prod: xamarin
ms.assetid: F75BD540-9354-4C17-A119-57F3DEC66D54
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a1a0febfdb38e577363ba59e8d0a5ceda9464a26
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563492"
---
# <a name="loading-application-resource-videos"></a>加载应用程序资源视频

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)

`VideoPlayer` 视图的自定义呈现器可以播放以应用程序资源形式嵌入各个平台项目中的视频文件。 但是，当前版本的 `VideoPlayer` 无法访问 .NET Standard 库中嵌入的资源。

若要加载这些资源，可通过将 `Path` 设置为资源的文件名创建 `ResourceVideoSource` 的实例。 或者，可以调用静态 `VideoSource.FromResource` 方法以引用该资源。 然后，将 `ResourceVideoSource` 对象设置为 `VideoPlayer` 的 `Source` 属性。

## <a name="storing-the-video-files"></a>存储视频文件

将视频文件存储在平台项目中对每个平台都是不同的。

### <a name="ios-video-resources"></a>iOS 视频资源

在 iOS 项目中，可在资源文件夹或资源文件夹的子文件夹中存储视频 。 视频文件必须具有 `BundleResource` 的 `Build Action`。 将 `ResourceVideoSource` 的 `Path` 属性设置为文件名，例如，MyFile.mp4 表示资源文件夹中的某个文件，或 MyFolder/MyFile.mp4，其中 MyFolder 是资源的子文件夹    。

在 VideoPlayerDemos 解决方案中，VideoPlayerDemos.iOS 项目包含名为“Videos”的资源的子文件夹，其中包含名为 iOSApiVideo.mp4 的文件    。 这是一个简短的视频，显示如何使用 Xamarin 网站找到 iOS `AVPlayerViewController` 类的文档。

### <a name="android-video-resources"></a>Android 视频资源

在 Android 项目中，视频必须存储在名为“raw”的资源的子文件中 。 “raw”文件夹不能包含子文件夹。 为视频文件授予 `AndroidResource` 的 `Build Action`。 将 `ResourceVideoSource` 的 `Path` 属性设置为文件名，例如 MyFile.mp4。

VideoPlayerDemos.Android 项目包含名为“raw”的资源子文件夹，其中包含名为 AndroidApiVideo.mp4 的文件   。

### <a name="uwp-video-resources"></a>UWP 视频资源

在通用 Windows 平台项目中，可以将视频存储在项目的任何文件夹中。 为文件提供 `Content` 的 `Build Action`。 将 `ResourceVideoSource` 的 `Path` 属性设置为文件夹和文件名，例如，MyFolder/MyVideo.mp4。

VideoPlayerDemos.UWP 项目包含名为“Videos”的文件夹，其中包含 UWPApiVideo.mp4 文件  。

## <a name="loading-the-video-files"></a>加载视频文件

每个平台呈现器类都在其 `SetSource` 方法中包含代码，用于加载以资源形式存储的视频文件。

### <a name="ios-resource-loading"></a>iOS 资源加载

iOS 版本的 `VideoPlayerRenderer` 使用 `NSBundle` 方法的 `GetUrlForResource` 加载资源。 完整路径必须划分为文件名、扩展名和目录。 该代码使用 .NET `System.IO` 命名空间中的 `Path` 类，以将文件路径划分为以下组件：

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        void SetSource()
        {
            AVAsset asset = null;
            ···
            else if (Element.Source is ResourceVideoSource)
            {
                string path = (Element.Source as ResourceVideoSource).Path;

                if (!String.IsNullOrWhiteSpace(path))
                {
                    string directory = Path.GetDirectoryName(path);
                    string filename = Path.GetFileNameWithoutExtension(path);
                    string extension = Path.GetExtension(path).Substring(1);
                    NSUrl url = NSBundle.MainBundle.GetUrlForResource(filename, extension, directory);
                    asset = AVAsset.FromUrl(url);
                }
            }
            ···
        }
        ···
    }
}
```

### <a name="android-resource-loading"></a>Android 资源加载

Android `VideoPlayerRenderer` 使用文件名和包名称来构造 `Uri` 对象。 包名称是应用程序的名称，在此示例中为 VideoPlayerDemos.Android，可从静态 `Context.PackageName` 属性中获得。 然后，生成的 `Uri` 对象将传递给 `VideoView` 的 `SetVideoURI` 方法：

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···    
        void SetSource()
        {
            isPrepared = false;
            bool hasSetSource = false;
            ···
            else if (Element.Source is ResourceVideoSource)
            {
                string package = Context.PackageName;
                string path = (Element.Source as ResourceVideoSource).Path;

                if (!String.IsNullOrWhiteSpace(path))
                {
                    string filename = Path.GetFileNameWithoutExtension(path).ToLowerInvariant();
                    string uri = "android.resource://" + package + "/raw/" + filename;
                    videoView.SetVideoURI(Android.Net.Uri.Parse(uri));
                    hasSetSource = true;
                }
            }
            ···
        }
        ···
    }
}
```

### <a name="uwp-resource-loading"></a>UWP 资源加载

UWP `VideoPlayerRenderer` 为路径构造 `Uri` 对象，并将其设置为 `MediaElement` 的 `Source` 属性：

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        ···
        async void SetSource()
        {
            bool hasSetSource = false;
            ···
            else if (Element.Source is ResourceVideoSource)
            {
                string path = "ms-appx:///" + (Element.Source as ResourceVideoSource).Path;

                if (!String.IsNullOrWhiteSpace(path))
                {
                    Control.Source = new Uri(path);
                    hasSetSource = true;
                }
            }
        }
        ···
    }
}
```

## <a name="playing-the-resource-file"></a>播放资源文件

VideoPlayerDemos 解决方案中的“播放视频资源”页使用 `OnPlatform` 类为每个平台指定视频文件 ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.PlayVideoResourcePage"
             Title="Play Video Resource">
    <video:VideoPlayer>
        <video:VideoPlayer.Source>
            <video:ResourceVideoSource>
                <video:ResourceVideoSource.Path>
                    <OnPlatform x:TypeArguments="x:String">
                        <On Platform="iOS" Value="Videos/iOSApiVideo.mp4" />
                        <On Platform="Android" Value="AndroidApiVideo.mp4" />
                        <On Platform="UWP" Value="Videos/UWPApiVideo.mp4" />
                    </OnPlatform>
                </video:ResourceVideoSource.Path>
            </video:ResourceVideoSource>
        </video:VideoPlayer.Source>
    </video:VideoPlayer>
</ContentPage>
```

如果 iOS 资源存储在“Resources”文件夹中，并且如果 UWP 资源存储在项目的根文件夹中，则可以对每个平台使用相同的文件名。 如果出现这种情况，则可以直接将该名称设置为 `VideoPlayer` 的 `Source` 属性。

下面是该页面的运行结果：

[![播放视频资源](loading-resources-images/playvideoresource-small.png "播放视频资源")](loading-resources-images/playvideoresource-large.png#lightbox "播放视频资源")

现在，已了解如何[从 Web URI 中加载视频](web-videos.md)和如何播放嵌入的资源。 此外，还可[从设备的视频库中加载视频](accessing-library.md)。

## <a name="related-links"></a>相关链接

- [视频播放器演示（示例）](/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)