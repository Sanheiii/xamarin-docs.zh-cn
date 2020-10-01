---
title: 创建平台视频播放器
description: 本文介绍如何使用 Xamarin.Forms 在每个平台上实现视频播放器自定义呈现器。
ms.prod: xamarin
ms.assetid: EEE2FB9B-EB73-4A3F-A859-7A1D4808E149
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7de5f0ec67a4735b8157b539b80e193fa67256b1
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563479"
---
# <a name="creating-the-platform-video-players"></a>创建平台视频播放器

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)

[VideoPlayerDemos](/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos) 解决方案包含为 Xamarin.Forms 实现视频播放器的所有代码。 它还包括一系列页面，演示如何在应用程序中使用视频播放器。 所有 `VideoPlayer` 代码及其平台呈现器都存于名为 `FormsVideoLibrary` 的项目文件夹中，并且也使用命名空间 `FormsVideoLibrary`。 这样应可以很容易地将文件复制到自己的应用程序中并引用这些类。

## <a name="the-video-player"></a>视频播放器

[`VideoPlayer`](https://github.com/xamarin/xamarin-forms-samples/blob/master/CustomRenderers/VideoPlayerDemos/VideoPlayerDemos/VideoPlayerDemos/FormsVideoLibrary/VideoPlayer.cs) 类是各个平台之间共享的“VideoPlayerDemos”.NET Standard 库的一部分。 它派生自 `View`：

```csharp
using System;
using Xamarin.Forms;

namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
    }
}
```

此类的成员（以及 `IVideoPlayerController` 接口）在下面的文章中进行描述。

每个平台都包含名为 `VideoPlayerRenderer` 的类，该类包含实现视频播放器的特定于平台的代码。 该呈现器的主要任务是为该平台创建视频播放器。

### <a name="the-ios-player-view-controller"></a>IOS 播放器视图控制器

在 iOS 中实现视频播放器时，涉及到几个类。 应用程序首先创建 [`AVPlayerViewController`](xref:AVKit.AVPlayerViewController)，然后将 [`Player`](xref:AVKit.AVPlayerViewController.Player*) 属性设置为类型 [`AVPlayer`](xref:AVFoundation.AVPlayer) 的对象。 给播放器分配视频源时，需要额外的类。

像所有呈现器一样，iOS [`VideoPlayerRenderer`](https://github.com/xamarin/xamarin-forms-samples/blob/master/CustomRenderers/VideoPlayerDemos/VideoPlayerDemos/VideoPlayerDemos.iOS/FormsVideoLibrary/VideoPlayerRenderer.csVideoPlayerRenderer.cs) 也包含 `ExportRenderer` 属性，该属性使用呈现器标识 `VideoPlayer` 视图：

```csharp
using System;
using System.ComponentModel;
using System.IO;

using AVFoundation;
using AVKit;
using CoreMedia;
using Foundation;
using UIKit;

using Xamarin.Forms;
using Xamarin.Forms.Platform.iOS;

[assembly: ExportRenderer(typeof(FormsVideoLibrary.VideoPlayer),
                          typeof(FormsVideoLibrary.iOS.VideoPlayerRenderer))]

namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
    }
}
```

通常，设置平台控件的呈现器派生自 [`ViewRenderer<View, NativeView>`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/ViewRenderer.cs) 类，其中 `View` 是 Xamarin.Forms `View` 派生物（在本例中为 `VideoPlayer`），并且 `NativeView` 是呈现器类的 iOS `UIView` 派生物。 对于此呈现器，该通用参数简单地设置为 `UIView`，下文将分析原因。

如果呈现器基于 `UIViewController` 派生物（如同此项），那么类应替代 `ViewController` 属性并返回视图控制器，即本例中的 `AVPlayerViewController`。 这就是 `_playerViewController` 字段的用途：

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        AVPlayer player;
        AVPlayerItem playerItem;
        AVPlayerViewController _playerViewController;       // solely for ViewController property

        public override UIViewController ViewController => _playerViewController;

        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            base.OnElementChanged(args);

            if (args.NewElement != null)
            {
                if (Control == null)
                {
                    // Create AVPlayerViewController
                    _playerViewController = new AVPlayerViewController();

                    // Set Player property to AVPlayer
                    player = new AVPlayer();
                    _playerViewController.Player = player;

                    // Use the View from the controller as the native control
                    SetNativeControl(_playerViewController.View);
                }
                ···
            }
        }
        ···
    }
}
```

`OnElementChanged` 替代的主要职责是检查 `Control` 属性是否为 `null`，如果是，则创建平台控件，并将其传递给 `SetNativeControl` 方法。 在这种情况下，该对象只能从 `AVPlayerViewController` 的 `View` 属性中获取。 `UIView` 的派生物碰巧是名为 `AVPlayerView` 的专用类，但是因为它是专用的，所以它不能显式地指定为 `ViewRenderer` 的第二个泛型参数。

通常，此后呈现器类的 `Control` 属性引用用于实现该呈现器的 `UIView`，但在这种情况下，`Control` 属性不在其他地方使用。

### <a name="the-android-video-view"></a>Android 视频视图

`VideoPlayer` 的 Android 呈现器基于 Android [`VideoView`](xref:Android.Widget.VideoView) 类。 但是，如果 `VideoView` 本身用于在 Xamarin.Forms 应用程序中播放视频，则视频填充分配给 `VideoPlayer` 的区域，且无需保持正确的纵横比。 出于这个原因（很快将进行说明），`VideoView` 设置为 Android `RelativeLayout` 的子类。 `using` 指令定义 `ARelativeLayout` 来区别于 Xamarin.Forms `RelativeLayout`，并且这是 `ViewRenderer` 中的第二个泛型参数：

```csharp
using System;
using System.ComponentModel;
using System.IO;

using Android.Content;
using Android.Media;
using Android.Widget;
using ARelativeLayout = Android.Widget.RelativeLayout;

using Xamarin.Forms;
using Xamarin.Forms.Platform.Android;

[assembly: ExportRenderer(typeof(FormsVideoLibrary.VideoPlayer),
                          typeof(FormsVideoLibrary.Droid.VideoPlayerRenderer))]

namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        public VideoPlayerRenderer(Context context) : base(context)
        {
        }
        ···
    }
}
```

从 Xamarin.Forms 2.5 开始，Android 呈现器应包含带有 `Context` 参数的构造函数。

`OnElementChanged` 替代创建 `VideoView` 和 `RelativeLayout`，并为 `VideoView` 设置布局参数，使其在 `RelativeLayout` 中居中。

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        VideoView videoView;
        MediaController mediaController;    // Used to display transport controls
        bool isPrepared;
        ···
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            base.OnElementChanged(args);

            if (args.NewElement != null)
            {
                if (Control == null)
                {
                    // Save the VideoView for future reference
                    videoView = new VideoView(Context);

                    // Put the VideoView in a RelativeLayout
                    ARelativeLayout relativeLayout = new ARelativeLayout(Context);
                    relativeLayout.AddView(videoView);

                    // Center the VideoView in the RelativeLayout
                    ARelativeLayout.LayoutParams layoutParams =
                        new ARelativeLayout.LayoutParams(LayoutParams.MatchParent, LayoutParams.MatchParent);
                    layoutParams.AddRule(LayoutRules.CenterInParent);
                    videoView.LayoutParameters = layoutParams;

                    // Handle a VideoView event
                    videoView.Prepared += OnVideoViewPrepared;

                    // Use the RelativeLayout as the native control
                    SetNativeControl(relativeLayout);
                }
                ···
            }
            ···
        }

        protected override void Dispose(bool disposing)
        {
            if (Control != null && videoView != null)
            {
                videoView.Prepared -= OnVideoViewPrepared;
            }
            base.Dispose(disposing);
        }

        void OnVideoViewPrepared(object sender, EventArgs args)
        {
            isPrepared = true;
            ···
        }
        ···
    }
}
```

使用此方法附加 `Prepared` 事件的处理程序，并使用 `Dispose` 方法进行拆离。 `VideoView` 有足够信息开始播放视频文件时，触发此事件。

### <a name="the-uwp-media-element"></a>UWP 媒体元素

在通用 Windows 平台 (UWP) 中，最常见的视频播放器是 [`MediaElement`](xref:Windows.UI.Xaml.Controls.MediaElement)。 `MediaElement` 的相关文档指出，如果只需支持从版本 1607 开始的 Windows 10 版本，则应使用 [`MediaPlayerElement`](xref:Windows.UI.Xaml.Controls.MediaPlayerElement)。

`OnElementChanged` 替代需要创建 `MediaElement`、设置两个事件处理程序并将 `MediaElement` 对象传递给 `SetNativeControl`：

```csharp
using System;
using System.ComponentModel;

using Windows.Storage;
using Windows.Storage.Streams;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Media;

using Xamarin.Forms;
using Xamarin.Forms.Platform.UWP;

[assembly: ExportRenderer(typeof(FormsVideoLibrary.VideoPlayer),
                          typeof(FormsVideoLibrary.UWP.VideoPlayerRenderer))]

namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            base.OnElementChanged(args);

            if (args.NewElement != null)
            {
                if (Control == null)
                {
                    MediaElement mediaElement = new MediaElement();
                    SetNativeControl(mediaElement);

                    mediaElement.MediaOpened += OnMediaElementMediaOpened;
                    mediaElement.CurrentStateChanged += OnMediaElementCurrentStateChanged;
                }
                ···
            }
            ···
        }

        protected override void Dispose(bool disposing)
        {
            if (Control != null)
            {
                Control.MediaOpened -= OnMediaElementMediaOpened;
                Control.CurrentStateChanged -= OnMediaElementCurrentStateChanged;
            }

            base.Dispose(disposing);
        }        
        ···
    }
}
```

这两个事件处理程序在呈现器的 `Dispose` 事件中进行分离。

## <a name="showing-the-transport-controls"></a>显示传输控件

平台中包含的所有视频播放器都支持一组默认传输控件，其中包括用于播放和暂停的按钮，以及用于指示视频的当前位置和用于移动到新位置的定位条。

`VideoPlayer` 类定义名为 `AreTransportControlsEnabled` 的属性，并将默认值设置为 `true`：

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        // AreTransportControlsEnabled property
        public static readonly BindableProperty AreTransportControlsEnabledProperty =
            BindableProperty.Create(nameof(AreTransportControlsEnabled), typeof(bool), typeof(VideoPlayer), true);

        public bool AreTransportControlsEnabled
        {
            set { SetValue(AreTransportControlsEnabledProperty, value); }
            get { return (bool)GetValue(AreTransportControlsEnabledProperty); }
        }
        ···
    }
}
```

尽管此属性同时具有 `set` 和 `get` 访问器，但呈现器仅在设置属性时才处理事例。 `get` 访问器只返回该属性的当前值。

像 `AreTransportControlsEnabled` 这样的属性在平台呈现器中以两种方式进行处理：

- 第一次是 Xamarin.Forms 创建 `VideoPlayer` 元素时。 如果 `NewElement` 属性不是 `null`，则会在呈现器的 `OnElementChanged` 替代中说明这一点。 此时，呈现器可以根据 `VideoPlayer` 中定义的属性初始值设置自己的平台视频播放器。

- 如果稍后 `VideoPlayer` 中的属性发生变化，则调用呈现器中的 `OnElementPropertyChanged` 方法。 这允许呈现器基于新的属性设置更新平台视频播放器。

下面几节讨论如何在每个平台上处理 `AreTransportControlsEnabled` 属性。

### <a name="ios-playback-controls"></a>iOS 播放控件

管理传输控件显示的 iOS `AVPlayerViewController` 属性是 [`ShowsPlaybackControls`](xref:AVKit.AVPlayerViewController.ShowsPlaybackControls*)。 下面介绍如何在 iOS `VideoViewRenderer` 中设置该属性：

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        AVPlayerViewController _playerViewController;       // solely for ViewController property

        public override UIViewController ViewController => _playerViewController;

        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                SetAreTransportControlsEnabled();
                ···
            }
        }

        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(sender, args);

            if (args.PropertyName == VideoPlayer.AreTransportControlsEnabledProperty.PropertyName)
            {
                SetAreTransportControlsEnabled();
            }
            ···
        }

        void SetAreTransportControlsEnabled()
        {
            ((AVPlayerViewController)ViewController).ShowsPlaybackControls = Element.AreTransportControlsEnabled;
        }
        ···
    }
}
```

呈现器的 `Element` 属性引用 `VideoPlayer` 类。

### <a name="the-android-media-controller"></a>Android 媒体控制器

在 Android 中，显示传输控件需要创建 [`MediaController`](xref:Android.Widget.MediaController) 对象，并将其与 `VideoView` 对象关联。 该机制采用 `SetAreTransportControlsEnabled` 方法进行演示：

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        VideoView videoView;
        MediaController mediaController;    // Used to display transport controls
        bool isPrepared;

        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                SetAreTransportControlsEnabled();
                ···
            }
        }

        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(sender, args);

            if (args.PropertyName == VideoPlayer.AreTransportControlsEnabledProperty.PropertyName)
            {
                SetAreTransportControlsEnabled();
            }
            ···
        }

        void SetAreTransportControlsEnabled()
        {
            if (Element.AreTransportControlsEnabled)
            {
                mediaController = new MediaController(Context);
                mediaController.SetMediaPlayer(videoView);
                videoView.SetMediaController(mediaController);
            }
            else
            {
                videoView.SetMediaController(null);

                if (mediaController != null)
                {
                    mediaController.SetMediaPlayer(null);
                    mediaController = null;
                }
            }
        }
        ···
    }
}
```

### <a name="the-uwp-transport-controls-property"></a>UWP 传输控件属性

UWP `MediaElement` 定义名为 [`AreTransportControlsEnabled`](xref:Windows.UI.Xaml.Controls.MediaElement.AreTransportControlsEnabled*) 的属性，因此，该属性是根据同名的 `VideoPlayer` 属性设置的：

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
                SetAreTransportControlsEnabled();
                ···
            }
        }

        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(sender, args);

            if (args.PropertyName == VideoPlayer.AreTransportControlsEnabledProperty.PropertyName)
            {
                SetAreTransportControlsEnabled();
            }
            ···
        }

        void SetAreTransportControlsEnabled()
        {
            Control.AreTransportControlsEnabled = Element.AreTransportControlsEnabled;
        }
        ···
    }
}
```

还需要另一个属性才能开始播放视频：这是引用视频文件的非常重要的 `Source` 属性。 将在下一篇文章（[播放 Web 视频](web-videos.md)）中介绍 `Source` 属性的实现。

## <a name="related-links"></a>相关链接

- [视频播放器演示（示例）](/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)