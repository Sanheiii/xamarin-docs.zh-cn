---
title: 播放 Web 视频
description: 本文说明如何使用 Xamarin.Forms 在视频播放器应用程序中播放 Web 视频。
ms.prod: xamarin
ms.assetid: 75781A10-865D-4BA8-8D6B-E3DA012922BC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 113265ffd26fcb1daf1cc85867e55f3e8ebd091f
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91557941"
---
# <a name="playing-a-web-video"></a>播放 Web 视频

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)

`VideoPlayer` 类定义用于指定视频文件源的 `Source` 属性，以及 `AutoPlay` 属性。 `AutoPlay` 默认设置为 `true`，这意味着视频应在设置 `Source` 后自动开始播放：

```csharp
using System;
using Xamarin.Forms;

namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        // Source property
        public static readonly BindableProperty SourceProperty =
            BindableProperty.Create(nameof(Source), typeof(VideoSource), typeof(VideoPlayer), null);

        [TypeConverter(typeof(VideoSourceConverter))]
        public VideoSource Source
        {
            set { SetValue(SourceProperty, value); }
            get { return (VideoSource)GetValue(SourceProperty); }
        }

        // AutoPlay property
        public static readonly BindableProperty AutoPlayProperty =
            BindableProperty.Create(nameof(AutoPlay), typeof(bool), typeof(VideoPlayer), true);

        public bool AutoPlay
        {
            set { SetValue(AutoPlayProperty, value); }
            get { return (bool)GetValue(AutoPlayProperty); }
        }
        ···
    }
}
```

`Source` 属性为 `VideoSource` 类型，它根据 Xamarin.Forms [`ImageSource`](xref:Xamarin.Forms.ImageSource) 抽象类及其三个派生类 [`UriImageSource`](xref:Xamarin.Forms.UriImageSource)、[`FileImageSource`](xref:Xamarin.Forms.FileImageSource) 和 [`StreamImageSource`](xref:Xamarin.Forms.StreamImageSource) 进行模式化。 但是，`VideoPlayer` 没有可用的数据流选项，因为 iOS 和 Android 不支持播放来自数据流的视频。

## <a name="video-sources"></a>视频源

`VideoSource` 抽象类仅由三个静态方法组成，这些方法实例化派生自 `VideoSource` 的三个类：

```csharp
namespace FormsVideoLibrary
{
    [TypeConverter(typeof(VideoSourceConverter))]
    public abstract class VideoSource : Element
    {
        public static VideoSource FromUri(string uri)
        {
            return new UriVideoSource() { Uri = uri };
        }

        public static VideoSource FromFile(string file)
        {
            return new FileVideoSource() { File = file };
        }

        public static VideoSource FromResource(string path)
        {
            return new ResourceVideoSource() { Path = path };
        }
    }
}
```

`UriVideoSource` 类用于指定带有 URI 的可下载视频文件。 它定义类型为 `string` 的单一属性：

```csharp
namespace FormsVideoLibrary
{
    public class UriVideoSource : VideoSource
    {
        public static readonly BindableProperty UriProperty =
            BindableProperty.Create(nameof(Uri), typeof(string), typeof(UriVideoSource));

        public string Uri
        {
            set { SetValue(UriProperty, value); }
            get { return (string)GetValue(UriProperty); }
        }
    }
}
```

下文介绍如何处理类型为 `UriVideoSource` 的对象。

`ResourceVideoSource` 类用于访问在平台应用程序中作为嵌入资源存储的视频文件，该类也使用 `string` 属性进行指定：

```csharp
namespace FormsVideoLibrary
{
    public class ResourceVideoSource : VideoSource
    {
        public static readonly BindableProperty PathProperty =
            BindableProperty.Create(nameof(Path), typeof(string), typeof(ResourceVideoSource));

        public string Path
        {
            set { SetValue(PathProperty, value); }
            get { return (string)GetValue(PathProperty); }
        }
    }
}
```

[加载应用程序资源视频](loading-resources.md)一文中介绍了如何处理类型为 `ResourceVideoSource` 的对象。 `VideoPlayer` 类没有可用于加载在 .NET Standard 库中作为资源存储的视频文件的功能。

`FileVideoSource` 类用于访问设备视频库中的视频文件。 该单一属性也为 `string` 类型：

```csharp
namespace FormsVideoLibrary
{
    public class FileVideoSource : VideoSource
    {
        public static readonly BindableProperty FileProperty =
                  BindableProperty.Create(nameof(File), typeof(string), typeof(FileVideoSource));

        public string File
        {
            set { SetValue(FileProperty, value); }
            get { return (string)GetValue(FileProperty); }
        }
    }
}
```

[访问设备视频库](accessing-library.md)一文中介绍了如何处理类型为 `FileVideoSource` 的对象。

`VideoSource` 类包含引用 `VideoSourceConverter` 的 `TypeConverter` 属性：

```csharp
namespace FormsVideoLibrary
{
    [TypeConverter(typeof(VideoSourceConverter))]
    public abstract class VideoSource : Element
    {
        ···
    }
}
```

当 `Source` 属性设置为 XAML 中的字符串时，将调用此类型转换器。 以下是 `VideoSourceConverter` 类：

```csharp
namespace FormsVideoLibrary
{
    public class VideoSourceConverter : TypeConverter
    {
        public override object ConvertFromInvariantString(string value)
        {
            if (!String.IsNullOrWhiteSpace(value))
            {
                Uri uri;
                return Uri.TryCreate(value, UriKind.Absolute, out uri) && uri.Scheme != "file" ?
                                VideoSource.FromUri(value) : VideoSource.FromResource(value);
            }

            throw new InvalidOperationException("Cannot convert null or whitespace to ImageSource");
        }
    }
}
```

`ConvertFromInvariantString` 方法尝试将字符串转换为 `Uri` 对象。 如果成功，并且方案不为 `file:`，则该方法返回 `UriVideoSource`。 否则，将返回 `ResourceVideoSource`。

## <a name="setting-the-video-source"></a>设置视频源

涉及视频源的所有其他逻辑都在各个平台呈现器中实现。 以下各部分介绍当 `Source` 属性设置为 `UriVideoSource` 对象时，平台呈现器如何播放视频。

### <a name="the-ios-video-source"></a>iOS 视频源

`VideoPlayerRenderer` 的两个节与设置视频播放器的视频源有关。 当 Xamarin.Forms 首次创建类型为 `VideoPlayer` 的对象时，将调用 `OnElementChanged` 方法，并会将 arguments 对象的 `NewElement` 属性设置为该 `VideoPlayer`。 `OnElementChanged` 方法调用 `SetSource`：

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                SetSource();
                ···
            }
        }

        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.SourceProperty.PropertyName)
            {
                SetSource();
            }
            ···
        }
        ···
    }
}
```

稍后，当 `Source` 属性更改时，将使用“Source”的 `PropertyName` 属性调用 `OnElementPropertyChanged` 方法，并再次调用 `SetSource`。

若要在 iOS 中播放视频文件，首先需要创建类型为 [`AVAsset`](xref:AVFoundation.AVAsset) 的对象来封装视频文件，该对象会用于创建 [`AVPlayerItem`](xref:AVFoundation.AVPlayerItem)，后者之后会传递到 `AVPlayer` 对象。 以下代码展示 `SetSource` 方法在 `Source` 属性为 `UriVideoSource` 类型时如何对其进行处理：

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        AVPlayer player;
        AVPlayerItem playerItem;
        ···
        void SetSource()
        {
            AVAsset asset = null;

            if (Element.Source is UriVideoSource)
            {
                string uri = (Element.Source as UriVideoSource).Uri;

                if (!String.IsNullOrWhiteSpace(uri))
                {
                    asset = AVAsset.FromUrl(new NSUrl(uri));
                }
            }
            ···
            if (asset != null)
            {
                playerItem = new AVPlayerItem(asset);
            }
            else
            {
                playerItem = null;
            }

            player.ReplaceCurrentItemWithPlayerItem(playerItem);

            if (playerItem != null && Element.AutoPlay)
            {
                player.Play();
            }
        }
        ···
    }
}
```

`AutoPlay` 属性在 iOS 视频类中无模拟属性，因此会在 `SetSource` 方法的末尾检查该属性，以便调用 `AVPlayer` 对象上的 `Play` 方法。

在某些情况下，视频会在包含 `VideoPlayer` 的页面导航回主页后继续播放。 为了停止视频，`Dispose` 替代中还会设置 `ReplaceCurrentItemWithPlayerItem`：

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        protected override void Dispose(bool disposing)
        {
            base.Dispose(disposing);

            if (player != null)
            {
                player.ReplaceCurrentItemWithPlayerItem(null);
            }
        }
        ···
    }
}
```

### <a name="the-android-video-source"></a>Android 视频源

Android `VideoPlayerRenderer` 需要在首次创建 `VideoPlayer` 时以及稍后 `Source` 属性更改时设置播放器的视频源：

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                SetSource();
                ···
            }
        }
        ···
        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.SourceProperty.PropertyName)
            {
                SetSource();
            }
            ···
        }
        ···
    }
}
```

`SetSource` 方法通过使用自字符串 URI 创建的 Android `Uri` 对象来调用 `VideoView` 上的 `SetVideoUri`，从而处理类型为 `UriVideoSource` 的对象。 此处的 `Uri` 类处于完全限定状态，以便与 .NET `Uri` 类进行区分：

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

            if (Element.Source is UriVideoSource)
            {
                string uri = (Element.Source as UriVideoSource).Uri;

                if (!String.IsNullOrWhiteSpace(uri))
                {
                    videoView.SetVideoURI(Android.Net.Uri.Parse(uri));
                    hasSetSource = true;
                }
            }
            ···

            if (hasSetSource && Element.AutoPlay)
            {
                videoView.Start();
            }
        }
        ···
    }
}
```

Android `VideoView` 没有对应的 `AutoPlay` 属性，因此，如果设置了新视频，则会调用 `Start` 方法。

如果 `VideoPlayer` 的 `Source` 属性设置为 `null`，或如果 `UriVideoSource` 的 `Uri` 属性设置为 `null`或空白字符串，则 iOS 呈现器和 Android 呈现器的行为会有所不同。 如果 iOS 视频播放器目前正在播放视频，并且 `Source` 设置为 `null`（或字符串为 `null` 或空白），则会使用 `null` 值调用 `ReplaceCurrentItemWithPlayerItem`。 当前视频会被替换并停止播放。

Android 不支持类似的功能。 如果 `Source` 属性设置为 `null`，则 `SetSource` 方法会忽略它，并且当前视频会继续播放。

### <a name="the-uwp-video-source"></a>UWP 视频源

UWP `MediaElement` 定义 `AutoPlay` 属性，该属性在呈现器中像任何其他属性一样进行处理：

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                SetSource();
                SetAutoPlay();
                ···    
            }
        }

        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.SourceProperty.PropertyName)
            {
                SetSource();
            }
            else if (args.PropertyName == VideoPlayer.AutoPlayProperty.PropertyName)
            {
                SetAutoPlay();
            }
            ···
        }
        ···
    }
}
```

`SetSource` 属性通过将 `MediaElement` 的 `Source` 属性设置为 .NET `Uri` 值或设置为 `null`（如果 `VideoPlayer` 的 `Source` 属性设置为 `null`）来处理 `UriVideoSource` 对象：

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        ···
        async void SetSource()
        {
            bool hasSetSource = false;

            if (Element.Source is UriVideoSource)
            {
                string uri = (Element.Source as UriVideoSource).Uri;

                if (!String.IsNullOrWhiteSpace(uri))
                {
                    Control.Source = new Uri(uri);
                    hasSetSource = true;
                }
            }
            ···
            if (!hasSetSource)
            {
                Control.Source = null;
            }
        }

        void SetAutoPlay()
        {
            Control.AutoPlay = Element.AutoPlay;
        }
        ···
    }
}
```

## <a name="setting-a-url-source"></a>设置 URL 源

通过在三个呈现器中实现这些属性，可以播放来自 URL 源的视频。 [**VideoPlayDemos**](/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos) 程序中的“播放 Web 视频”页由以下 XAML 文件定义：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.PlayWebVideoPage"
             Title="Play Web Video">

    <video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4" />

</ContentPage>
```

`VideoSourceConverter` 类将字符串转换为 `UriVideoSource`。 导航到“播放 Web 视频”页时，视频会开始加载，并在下载和缓冲足够数量的数据后开始播放。 视频时长约为 10 分钟：

[![播放 Web 视频](web-videos-images/playwebvideo-small.png "播放 Web 视频")](web-videos-images/playwebvideo-large.png#lightbox "播放 Web 视频")

在每个平台上，如果未使用传输控件，则传输控件会淡出，但可以通过点击视频将其恢复为查看状态。

可以通过将 `AutoPlay` 属性设置为 `false` 来阻止视频自动启动：

```xaml
<video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4"
                   AutoPlay="false" />
```

需要按“播放”按钮才能启动视频。

同样，可以通过将 `AreTransportControlsEnabled` 属性设置为 `false` 来禁止显示传输控件：

```xaml
<video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4"
                   AreTransportControlsEnabled="False" />
```

如果将这两个属性都设置为 `false`，则视频不会开始播放，且没有任何方法可以启动视频！ 需要从代码隐藏文件中调用 `Play`，或者创建自己的传输控件，如[实现自定义视频传输控件](custom-transport.md)一文中所述。

**App.xaml** 文件包含用于两个其他视频的资源：

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.App">
    <Application.Resources>
        <ResourceDictionary>

            <video:UriVideoSource x:Key="ElephantsDream"
                                  Uri="https://archive.org/download/ElephantsDream/ed_hd_512kb.mp4" />

            <video:UriVideoSource x:Key="BigBuckBunny"
                                  Uri="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4" />

            <video:UriVideoSource x:Key="Sintel"
                                  Uri="https://archive.org/download/Sintel/sintel-2048-stereo_512kb.mp4" />

        </ResourceDictionary>
    </Application.Resources>
</Application>
```

若要引用这些其他电影之一，可以使用 `StaticResource` 标记扩展替换 **PlayWebVideo.xaml** 文件中的显式 URL，在这种情况下，不需要 `VideoSourceConverter` 即可创建 `UriVideoSource` 对象：

```xaml
<video:VideoPlayer Source="{StaticResource ElephantsDream}" />
```

或者，可以在 `ListView` 中设置视频文件的 `Source` 属性，如下一篇文章[将视频源绑定到播放器](source-bindings.md)中所述。

## <a name="related-links"></a>相关链接

- [视频播放器演示（示例）](/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)