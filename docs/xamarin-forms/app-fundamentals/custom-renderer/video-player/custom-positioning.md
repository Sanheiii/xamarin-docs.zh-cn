---
title: 自定义视频定位
description: 本文介绍如何使用 Xamarin.Forms 在视频播放器应用程序中实现自定义定位条。
ms.prod: xamarin
ms.assetid: 6D792264-30FF-46F7-8C1B-2FEF9D277DF4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 00b6617f754eb251eae0a7be9715deb840c33102
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562738"
---
# <a name="custom-video-positioning"></a>自定义视频定位

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)

由每个平台实现的传输控件都有一个定位条。 该定位条类似于滑块或滚动条，用于显示视频进度。 此外，用户可以操纵该定位条，前移或后移到视频中的新位置。

本文演示如何实现自己的自定义定位条。

## <a name="the-duration-property"></a>Duration 属性

`VideoPlayer` 支持自定义定位条的所需信息之一是视频的持续时间。 `VideoPlayer` 定义类型 `TimeSpan` 的只读 `Duration` 属性：

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        // Duration read-only property
        private static readonly BindablePropertyKey DurationPropertyKey =
            BindableProperty.CreateReadOnly(nameof(Duration), typeof(TimeSpan), typeof(VideoPlayer), new TimeSpan(),
                propertyChanged: (bindable, oldValue, newValue) => ((VideoPlayer)bindable).SetTimeToEnd());

        public static readonly BindableProperty DurationProperty = DurationPropertyKey.BindableProperty;

        public TimeSpan Duration
        {
            get { return (TimeSpan)GetValue(DurationProperty); }
        }

        TimeSpan IVideoPlayerController.Duration
        {
            set { SetValue(DurationPropertyKey, value); }
            get { return Duration; }
        }
        ···
    }
}
```

就像 [上一篇文章](custom-transport.md) 中描述的 `Status` 属性一样，该 `Duration` 属性也是只读状态。 它由专属的 `BindablePropertyKey` 定义，并只能通过引用 `IVideoPlayerController` 接口进行设置，该接口包含 `Duration` 属性：

```csharp
namespace FormsVideoLibrary
{
    public interface IVideoPlayerController
    {
        VideoStatus Status { set; get; }

        TimeSpan Duration { set; get; }
    }
}
```

另请注意，本文稍后将介绍调用名为 `SetTimeToEnd` 方法的属性更改处理程序。

设置 `VideoPlayer` 的 `Source` 属性后，无法立即获取视频持续时间。 必须先下载部分视频文件，然后基础视频播放器才能确定其持续时间。

下面是每个平台呈现器获取视频持续时间的方法：

### <a name="video-duration-in-ios"></a>iOS 中的视频持续时间

在 iOS 中，是从 `AVPlayerItem` 的 `Duration` 属性获取视频持续时间，但不是在创建 `AVPlayerItem` 后立即获得。 可以为 `Duration` 属性设置 iOS 观察程序，但 `VideoPlayerRenderer` 是使用每秒调用 10 次的 `UpdateStatus` 方法获取持续时间：

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            ···
            if (playerItem != null)
            {
                ((IVideoPlayerController)Element).Duration = ConvertTime(playerItem.Duration);
                ···
            }
        }

        TimeSpan ConvertTime(CMTime cmTime)
        {
            return TimeSpan.FromSeconds(Double.IsNaN(cmTime.Seconds) ? 0 : cmTime.Seconds);

        }
        ···
    }
}
```

`ConvertTime` 方法将 `CMTime` 对象转换为 `TimeSpan` 值。

### <a name="video-duration-in-android"></a>Android 中的视频持续时间

触发 `VideoView` 的 `Prepared` 事件时，Android `VideoView` 的 `Duration` 属性以毫秒为单位报告有效持续时间。 Android `VideoPlayerRenderer` 类使用该处理程序获取 `Duration` 属性：

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        void OnVideoViewPrepared(object sender, EventArgs args)
        {
            ···
            ((IVideoPlayerController)Element).Duration = TimeSpan.FromMilliseconds(videoView.Duration);
        }
        ···
    }
}
```

### <a name="video-duration-in-uwp"></a>UWP 中的视频持续时间

`MediaElement` 的 `NaturalDuration` 属性是一个 `TimeSpan` 值，该值在 `MediaElement` 触发 `MediaOpened` 事件时有效：

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        ···
        void OnMediaElementMediaOpened(object sender, RoutedEventArgs args)
        {
            ((IVideoPlayerController)Element).Duration = Control.NaturalDuration.TimeSpan;
        }
        ···
    }
}
```

## <a name="the-position-property"></a>Position 属性

`VideoPlayer` 还需要 `Position` 属性，该属性在视频播放时从零增加到 `Duration`。 `VideoPlayer` 实现该属性的方式类似于 UWP `MediaElement` 中的 `Position` 属性，它是具有公共的 `set` 和 `get` 访问器的常规可绑定属性：

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        // Position property
        public static readonly BindableProperty PositionProperty =
            BindableProperty.Create(nameof(Position), typeof(TimeSpan), typeof(VideoPlayer), new TimeSpan(),
                propertyChanged: (bindable, oldValue, newValue) => ((VideoPlayer)bindable).SetTimeToEnd());

        public TimeSpan Position
        {
            set { SetValue(PositionProperty, value); }
            get { return (TimeSpan)GetValue(PositionProperty); }
        }
        ···
    }
}
```

播放视频时，`get` 访问器返回当前的视频位置，但 `set` 访问器旨在通过前移或后移视频位置来响应用户对定位条的操作。

在 iOS 和 Android 中，获取当前位置的属性只有一个 `get` 访问器，并且只有一个 `Seek` 方法可用于执行第二个任务。 如果仔细想想，单独的 `Seek` 方法似乎比单个 `Position` 属性更为合理。 单个 `Position` 属性有一个固有问题：视频播放时，必须不断更新 `Position` 属性，以反映新的位置。 但你不希望通过对 `Position` 属性执行太多更改来让视频播放器移动到视频中的新位置。 如果发生这种情况，视频播放器将通过查找 `Position` 属性的最后值来响应，并且视频不会前移。

尽管使用 `set` 和`get` 访问器实现 `Position` 属性有一定难度，仍然选用此方法是因为它与 UWP `MediaElement` 一致，且它在数据绑定方面具有很大的优势：可将 `VideoPlayer` 的 `Position` 属性绑定到用于显示位置和查找新位置的滑块。 但是，实现该 `Position` 属性时需要注意一些事项，以避免反馈循环。

### <a name="setting-and-getting-ios-position"></a>设置和获取 iOS 位置

在 iOS 中，`AVPlayerItem` 对象的 `CurrentTime` 属性表示正在播放的视频的当前位置。 iOS `VideoPlayerRenderer` 在设置 `UpdateStatus` 处理程序中的 `Position` 属性的同时，也设置 `Duration` 属性：

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            ···
            if (playerItem != null)
            {
                ···
                ((IElementController)Element).SetValueFromRenderer(VideoPlayer.PositionProperty, ConvertTime(playerItem.CurrentTime));
            }
        }
        ···
    }
}
```

呈现器可检测出 `VideoPlayer` 的 `Position` 属性集在 `OnElementPropertyChanged` 替代中发生更改的时间，并使用该新值调用 `AVPlayer` 对象上的 `Seek` 方法：

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.PositionProperty.PropertyName)
            {
                TimeSpan controlPosition = ConvertTime(player.CurrentTime);

                if (Math.Abs((controlPosition - Element.Position).TotalSeconds) > 1)
                {
                    player.Seek(CMTime.FromSeconds(Element.Position.TotalSeconds, 1));
                }
            }
        }
        ···
    }
}
```

请记住，每次从 `OnUpdateStatus` 处理程序设置 `VideoPlayer` 中的 `Position` 属性时，`Position` 属性都会触发 `PropertyChanged` 事件，该事件会在 `OnElementPropertyChanged` 替代中检测到。 对于大多数此类更改，`OnElementPropertyChanged` 方法不应执行任何操作。 否则，在视频位置每次发生更改时，它都会移动到刚刚到达的同一位置！

为了避免这种反馈循环，`Position` 属性与 `AVPlayer` 的当前位置的时间差大于一秒时，`OnElementPropertyChanged` 方法才调用 `Seek`。

### <a name="setting-and-getting-android-position"></a>设置和获取 Android 位置

就像在 iOS 呈现器中一样，Android `VideoPlayerRenderer` 为 `OnUpdateStatus` 处理程序中的 `Position` 属性设置新值。 `VideoView` 的 `CurrentPosition` 属性包含以毫秒为单位的新位置：

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            ···
            TimeSpan timeSpan = TimeSpan.FromMilliseconds(videoView.CurrentPosition);
            ((IElementController)Element).SetValueFromRenderer(VideoPlayer.PositionProperty, timeSpan);
        }
        ···
    }
}
```

而且，就像在 iOS 呈现器中一样，在 `Position` 属性已发生变化，但仅在该变化与 `VideoView` 的 `CurrentPosition` 值相差一秒以上时，Android 呈现器才调用 `VideoView` 的 `SeekTo` 方法：

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.PositionProperty.PropertyName)
            {
                if (Math.Abs(videoView.CurrentPosition - Element.Position.TotalMilliseconds) > 1000)
                {
                    videoView.SeekTo((int)Element.Position.TotalMilliseconds);
                }
            }
        }
        ···
    }
}
```

### <a name="setting-and-getting-uwp-position"></a>设置和获取 UWP 位置

UWP `VideoPlayerRenderer` 处理 `Position` 的方式与 iOS 和 Android 呈现器相同，但由于 UWP `MediaElement` 的 `Position` 属性也是 `TimeSpan` 值，因此不需要进行转换：

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        ···
        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.PositionProperty.PropertyName)
            {
                if (Math.Abs((Control.Position - Element.Position).TotalSeconds) > 1)
                {
                    Control.Position = Element.Position;
                }
            }
        }
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            ((IElementController)Element).SetValueFromRenderer(VideoPlayer.PositionProperty, Control.Position);
        }
        ···
    }
}
```

## <a name="calculating-a-timetoend-property"></a>计算 TimeToEnd 属性

有时视频播放器会显示视频的剩余时间。 在视频持续时间中，该值在视频开始时开始计算，在视频结束时减少到零。

`VideoPlayer` 包括一个只读 `TimeToEnd` 属性，该属性完全在该类中根据对 `Duration` 和 `Position` 属性的更改进行处理：

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        private static readonly BindablePropertyKey TimeToEndPropertyKey =
            BindableProperty.CreateReadOnly(nameof(TimeToEnd), typeof(TimeSpan), typeof(VideoPlayer), new TimeSpan());

        public static readonly BindableProperty TimeToEndProperty = TimeToEndPropertyKey.BindableProperty;

        public TimeSpan TimeToEnd
        {
            private set { SetValue(TimeToEndPropertyKey, value); }
            get { return (TimeSpan)GetValue(TimeToEndProperty); }
        }

        void SetTimeToEnd()
        {
            TimeToEnd = Duration - Position;
        }
        ···
    }
}
```

从两个 `Duration` 和 `Position` 的属性更改处理程序调用 `SetTimeToEnd` 方法。

## <a name="a-custom-slider-for-video"></a>用于视频的自定义滑块

可以为定位条写入自定义控件，也可以使用 Xamarin.Forms `Slider` 或派生自 `Slider` 的类，例如下面的 `PositionSlider` 类。 该类定义两个新属性 `Duration` 和 `Position`，它们的类型为 `TimeSpan`，旨在作为 `VideoPlayer` 中同名的两个属性的数据绑定。 注意，`Position` 属性的默认绑定模式为双向：

```csharp
namespace FormsVideoLibrary
{
    public class PositionSlider : Slider
    {
        public static readonly BindableProperty DurationProperty =
            BindableProperty.Create(nameof(Duration), typeof(TimeSpan), typeof(PositionSlider), new TimeSpan(1),
                                    propertyChanged: (bindable, oldValue, newValue) =>
                                    {
                                        double seconds = ((TimeSpan)newValue).TotalSeconds;
                                        ((PositionSlider)bindable).Maximum = seconds <= 0 ? 1 : seconds;
                                    });

        public TimeSpan Duration
        {
            set { SetValue(DurationProperty, value); }
            get { return (TimeSpan)GetValue(DurationProperty); }
        }

        public static readonly BindableProperty PositionProperty =
            BindableProperty.Create(nameof(Position), typeof(TimeSpan), typeof(PositionSlider), new TimeSpan(0),
                                    defaultBindingMode: BindingMode.TwoWay,
                                    propertyChanged: (bindable, oldValue, newValue) =>
                                    {
                                        double seconds = ((TimeSpan)newValue).TotalSeconds;
                                        ((PositionSlider)bindable).Value = seconds;
                                    });

        public TimeSpan Position
        {
            set { SetValue(PositionProperty, value); }
            get { return (TimeSpan)GetValue(PositionProperty); }
        }

        public PositionSlider()
        {
            PropertyChanged += (sender, args) =>
            {
                if (args.PropertyName == "Value")
                {
                    TimeSpan newPosition = TimeSpan.FromSeconds(Value);

                    if (Math.Abs(newPosition.TotalSeconds - Position.TotalSeconds) / Duration.TotalSeconds > 0.01)
                    {
                        Position = newPosition;
                    }
                }
            };
        }
    }
}
```

`Duration` 属性的属性更改处理程序将基础 `Slider` 的 `Maximum` 属性设置为 `TimeSpan` 值的 `TotalSeconds` 属性。 同样，`Position` 的属性更改处理程序设置 `Slider` 的 `Value` 属性。 通过这种方式，基础 `Slider` 跟踪 `PositionSlider` 的位置。

`PositionSlider` 更新自仅一个实例中的基础 `Slider`：用户操作 `Slider` 以指明应该将视频前移或反转到新的位置时。 这是在 `PositionSlider` 构造函数中的 `PropertyChanged` 处理程序中检测到的。 处理程序检查 `Value` 属性中的更改，并且如果它与 `Position` 属性不同，那么 `Position` 属性将从 `Value` 属性进行设置。

理论上，内部的 `if` 语句可以这样写：

```csharp
if (newPosition.Seconds != Position.Seconds)
{
    Position = newPosition;
}
```

然而，无论 `Minimum` 和 `Maximum` 设置如何，`Slider` 的 Android 实现都只有 1000 个独立步骤。 如果视频持续时间大于 1000 秒，那么两个不同的 `Position` 值将对应 `Slider` 的同一 `Value` 设置，并且这个 `if` 语句将因用户操作 `Slider` 而触发误报。 相反，检查新位置和现有位置是否大于总持续时间的百分之一更安全。

## <a name="using-the-positionslider"></a>使用 PositionSlider

UWP [`MediaElement`](/uwp/api/Windows.UI.Xaml.Controls.MediaElement/) 的文档会针对对 `Position` 属性的绑定进行警告，因为属性经常更新。 文档建议使用计时器查询 `Position` 属性。

这是很好的建议，但是这三个 `VideoPlayerRenderer` 类已经间接地使用计时器来更新 `Position` 属性了。 `Position` 属性在 `UpdateStatus` 事件的处理程序中更改，其每秒仅触发 10 次。

因此，`VideoPlayer` 的 `Position` 属性可以绑定到 `PositionSlider` 的 `Position` 属性上，而且不存在性能问题，如“自定义定位条”页面中所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.CustomPositionBarPage"
             Title="Custom Position Bar">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <video:VideoPlayer x:Name="videoPlayer"
                           Grid.Row="0"
                           AreTransportControlsEnabled="False"
                           Source="{StaticResource ElephantsDream}" />

        ···

        <StackLayout Grid.Row="1"
                     Orientation="Horizontal"
                     Margin="10, 0"
                     BindingContext="{x:Reference videoPlayer}">

            <Label Text="{Binding Path=Position,
                                  StringFormat='{0:hh\\:mm\\:ss}'}"
                   VerticalOptions="Center"/>

            ···

            <Label Text="{Binding Path=TimeToEnd,
                                  StringFormat='{0:hh\\:mm\\:ss}'}"
                   VerticalOptions="Center" />
        </StackLayout>

        <video:PositionSlider Grid.Row="2"
                              Margin="10, 0, 10, 10"
                              BindingContext="{x:Reference videoPlayer}"
                              Duration="{Binding Duration}"           
                              Position="{Binding Position}">
            <video:PositionSlider.Triggers>
                <DataTrigger TargetType="video:PositionSlider"
                             Binding="{Binding Status}"
                             Value="{x:Static video:VideoStatus.NotReady}">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>
            </video:PositionSlider.Triggers>
        </video:PositionSlider>
    </Grid>
</ContentPage>
```

第一个省略号 (···) 隐藏 `ActivityIndicator`；与上一个“自定义传输”页面中的省略号相同。 请注意两个显示 `Position` 和 `TimeToEnd` 属性的 `Label` 元素。 这两个 `Label` 元素之间的省略号隐藏“自定义传输”页面中显示的“播放”、“暂停”和“停止”的两个 `Button` 元素。 代码隐藏逻辑也与“自定义传输”页面相同。

[![自定义定位](custom-positioning-images/custompositioning-small.png "自定义定位")](custom-positioning-images/custompositioning-large.png#lightbox "自定义定位")

关于 `VideoPlayer` 的讨论到此为止。

## <a name="related-links"></a>相关链接

- [视频播放器演示（示例）](/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)