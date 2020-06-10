---
标题： " Xamarin.Forms MediaElement" 说明： "本文介绍了如何在应用程序中使用 MediaElement 播放视频和音频 Xamarin.Forms 。"
ms-chap： xamarin assetid： e65f1e56-a80d-46c7-9ff4-7ae6650a3165： xamarin 窗体作者： davidbritch： dabritch ms. 日期：02/18/2020 非 loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="xamarinforms-mediaelement"></a>Xamarin.FormsMediaElement

![](~/media/shared/preview.png "This API is currently pre-release")

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-mediaelementdemos/)

[`MediaElement`](xref:Xamarin.Forms.MediaElement)是用于播放视频和音频的视图。 可从以下源播放底层平台支持的媒体：

- Web，使用 URI （HTTP 或 HTTPS）。
- 使用 URI 方案嵌入到平台应用程序中的资源 `ms-appx:///` 。
- 使用 URI 方案，来自应用的本地和临时数据文件夹的文件 `ms-appdata:///` 。
- 设备的库。

[`MediaElement`](xref:Xamarin.Forms.MediaElement)可以使用平台播放控件，这些控件称为传输控件。 但是，它们在默认情况下处于禁用状态，可以替换为您自己的传输控件。 以下屏幕截图显示了 `MediaElement` 使用平台传输控件播放视频的步骤：

[![在 iOS 和 Android 上播放视频的 MediaElement 屏幕截图](mediaelement-images/playback-controls.png "播放视频的 MediaElement")](mediaelement-images/playback-controls-large.png#lightbox "播放视频的 MediaElement")

[`MediaElement`](xref:Xamarin.Forms.MediaElement)在4.5 中提供 Xamarin.Forms 。 但是，当前正在试验，只能通过将以下代码行添加到*App.xaml.cs*文件中来使用：

```csharp
Device.SetFlags(new string[]{ "MediaElement_Experimental" });
```

> [!NOTE]
> [`MediaElement`](xref:Xamarin.Forms.MediaElement)在 iOS、Android、通用 Windows 平台（UWP）、macOS、Windows Presentation Foundation 和 Tizen 上可用。

[`MediaElement`](xref:Xamarin.Forms.MediaElement)定义以下属性：

- [`Aspect`](xref:Xamarin.Forms.MediaElement.Aspect)类型为的， [`Aspect`](xref:Xamarin.Forms.Aspect) 确定如何缩放媒体以适应显示区域。 此属性的默认值为 `AspectFit`。
- [`AutoPlay`](xref:Xamarin.Forms.MediaElement.AutoPlay)，类型为 `bool` ，指示在设置属性时是否自动开始播放媒体 [`Source`](xref:Xamarin.Forms.MediaElement.Source) 。 此属性的默认值为 `true`。
- [`BufferingProgress`](xref:Xamarin.Forms.MediaElement.BufferingProgress)类型为的 `double` 指示当前缓冲进度。 此属性的默认值为0.0。
- [`CanSeek`](xref:Xamarin.Forms.MediaElement.CanSeek)类型为的， `bool` 指示是否可以通过设置属性的值来重定位媒体 [`Position`](xref:Xamarin.Forms.MediaElement.Position) 。 这是只读属性。
- [`CurrentState`](xref:Xamarin.Forms.MediaElement.CurrentState)类型为的 [`MediaElementState`](xref:Xamarin.Forms.MediaElementState) 指示控件的当前状态。 这是一个只读属性，其默认值为 `MediaElementState.Closed` 。
- [`Duration`](xref:Xamarin.Forms.MediaElement.Duration)类型为的， `TimeSpan?` 指示当前打开的媒体的持续时间。 这是一个只读属性，其默认值为 `null` 。
- [`IsLooping`](xref:Xamarin.Forms.MediaElement.IsLooping)，类型为 `bool` ，描述当前加载的媒体源是否应在到达其结束后从开始处恢复播放。 此属性的默认值为 `false`。
- [`KeepScreenOn`](xref:Xamarin.Forms.MediaElement.KeepScreenOn)类型为的，用于 `bool` 确定在播放媒体的过程中设备屏幕是否应保持打开。 此属性的默认值为 `false`。
- [`Position`](xref:Xamarin.Forms.MediaElement.Position)类型为的，它 `TimeSpan` 描述了整个媒体播放时间的当前进度。 此属性的默认值为 `TimeSpan.Zero`。
- [`ShowsPlaybackControls`](xref:Xamarin.Forms.MediaElement.ShowsPlaybackControls)类型为的，用于 `bool` 确定是否显示平台播放控件。 此属性的默认值为 `false`。
- [`Source`](xref:Xamarin.Forms.MediaElement.Source)类型为的， [`MediaSource`](xref:Xamarin.Forms.MediaSource) 指示加载到控件中的媒体源。
- [`VideoHeight`](xref:Xamarin.Forms.MediaElement.VideoHeight)类型为的， `int` 指示控件的高度。 这是只读属性。
- [`VideoWidth`](xref:Xamarin.Forms.MediaElement.VideoWidth)类型为的， `int` 指示控件的宽度。 这是只读属性。
- [`Volume`](xref:Xamarin.Forms.MediaElement.Volume)类型为的， `double` 它确定媒体的卷，它在0和1之间的线性刻度上表示。 此属性使用 `TwoWay` 绑定，其默认值为1。

这些属性（ `CanSeek` 属性除外）由对象提供支持， [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 这意味着它们可以是数据绑定的目标和样式。

[`MediaElement`](xref:Xamarin.Forms.MediaElement)类还定义四个事件：

- [`MediaOpened`](xref:Xamarin.Forms.MediaElement.MediaOpened)当媒体流已验证并打开时，将激发。
- [`MediaEnded`](xref:Xamarin.Forms.MediaElement.MediaEnded)当 `MediaElement` 完成播放其媒体时，将激发。
- [`MediaFailed`](xref:Xamarin.Forms.MediaElement.MediaFailed)当存在与媒体源关联的错误时，将激发。
- [`SeekCompleted`](xref:Xamarin.Forms.MediaElement.SeekCompleted)当请求的查找操作的查找点准备好播放时，将激发。

此外，还 [`MediaElement`](xref:Xamarin.Forms.MediaElement) 包括 [`Play`](xref:Xamarin.Forms.MediaElement.Play) 、 [`Pause`](xref:Xamarin.Forms.MediaElement.Pause) 和 [`Stop`](xref:Xamarin.Forms.MediaElement.Stop) 方法。

有关 Android 上支持的媒体格式的信息，请参阅 developer.android.com 上[支持的媒体格式](https://developer.android.com/guide/topics/media/media-formats)。 有关支持的媒体格式通用 Windows 平台（UWP）的信息，请参阅[支持的编解码器](/windows/uwp/audio-video-camera/supported-codecs)。

## <a name="play-remote-media"></a>播放远程媒体

[`MediaElement`](xref:Xamarin.Forms.MediaElement)可以使用 HTTP 和 HTTPS URI 方案播放远程媒体文件。 这是通过将属性设置 [`Source`](xref:Xamarin.Forms.MediaElement.Source) 为媒体文件的 URI 来完成的：

```xaml
<MediaElement Source="https://sec.ch9.ms/ch9/5d93/a1eab4bf-3288-4faf-81c4-294402a85d93/XamarinShow_mid.mp4"
              ShowsPlaybackControls="True" />
```

默认情况下，在打开介质后，属性定义的介质会 [`Source`](xref:Xamarin.Forms.MediaElement.Source) 立即播放。 若要禁止自动播放媒体，请将 [`AutoPlay`](xref:Xamarin.Forms.MediaElement.AutoPlay) 属性设置为 `false` 。

默认情况下，禁用媒体播放控件，并通过将属性设置 [`ShowsPlaybackControls`](xref:Xamarin.Forms.MediaElement.ShowsPlaybackControls) 为来启用它们 `true` 。 [`MediaElement`](xref:Xamarin.Forms.MediaElement)然后，将使用平台播放控件。

## <a name="play-local-media"></a>播放本地媒体

可从以下源播放本地媒体：

- 使用 URI 方案嵌入到平台应用程序中的资源 `ms-appx:///` 。
- 使用 URI 方案，来自应用的本地和临时数据文件夹的文件 `ms-appdata:///` 。
- 设备的库。

有关这些 URI 方案的详细信息，请参阅[uri 方案](/windows/uwp/app-resources/uri-schemes)。

### <a name="play-media-embedded-in-the-app-package"></a>播放嵌入在应用包中的媒体

[`MediaElement`](xref:Xamarin.Forms.MediaElement)可以使用 URI 方案播放嵌入到应用程序包中的媒体文件 `ms-appx:///` 。 媒体文件通过将其放置在平台项目中，嵌入到应用程序包中。

对于每个平台，将媒体文件存储在平台项目中是不同的：

- 在 iOS 上，必须将媒体文件存储在**resources 文件夹或** **resources**文件夹的子文件夹中。 媒体文件必须具有 `Build Action` 的 `BundleResource` 。
- 在 Android 上，媒体文件必须存储在名为**raw**的**资源**的子文件夹中。 “raw”文件夹不能包含子文件夹。 媒体文件必须具有 `Build Action` 的 `AndroidResource` 。
- 在 UWP 上，媒体文件可以存储在项目中的任何文件夹中。 媒体文件必须具有 `BuildAction` 的 `Content` 。

然后，可以使用 URI 方案播放满足这些条件的媒体文件 `ms-appx:///` ：

```xaml
<MediaElement Source="ms-appx:///XamarinForms101UsingEmbeddedImages.mp4"
              ShowsPlaybackControls="True" />
```

使用数据绑定时，值转换器可用于应用此 URI 方案：

```csharp
public class VideoSourceConverter : IValueConverter
{
    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        if (value == null)
            return null;

        if (string.IsNullOrWhiteSpace(value.ToString()))
            return null;

        if (Device.RuntimePlatform == Device.UWP)
            return new Uri($"ms-appx:///Assets/{value}");
        else
            return new Uri($"ms-appx:///{value}");
    }
    // ...
}
```

`VideoSourceConverter`然后，可以使用的实例将 URI 方案应用于 `ms-appx:///` 嵌入的媒体文件：

```xaml
<MediaElement Source="{Binding MediaSource, Converter={StaticResource VideoSourceConverter}}"
              ShowsPlaybackControls="True" />
```

有关 ms-appx URI 方案的详细信息，请参阅 " [ms-appx" 和 "ms-appx](/windows/uwp/app-resources/uri-schemes#ms-appx-and-ms-appx-web)"。

### <a name="play-media-from-the-apps-local-and-temporary-folders"></a>从应用的本地文件夹和临时文件夹播放媒体

[`MediaElement`](xref:Xamarin.Forms.MediaElement)可以使用 URI 方案播放复制到应用的本地或临时数据文件夹中的媒体文件 `ms-appdata:///` 。

下面的示例演示 [`Source`](xref:Xamarin.Forms.MediaElement.Source) 如何将属性设置为存储在应用的本地数据文件夹中的媒体文件：

```xaml
<MediaElement Source="ms-appdata:///local/XamarinVideo.mp4"
              ShowsPlaybackControls="True" />
```

以下示例显示了 [`Source`](xref:Xamarin.Forms.MediaElement.Source) 存储在应用临时数据文件夹中的媒体文件的属性：

```xaml
<MediaElement Source="ms-appdata:///temp/XamarinVideo.mp4"
              ShowsPlaybackControls="True" />
```

> [!IMPORTANT]
> 除了播放存储在应用的本地或临时数据文件夹中的媒体文件，UWP 还可以播放位于应用的漫游文件夹中的媒体文件。 这可以通过使用为媒体文件的前缀来实现 `ms-appdata:///roaming/` 。

使用数据绑定时，值转换器可用于应用此 URI 方案：

```csharp
public class VideoSourceConverter : IValueConverter
{
    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        if (value == null)
            return null;

        if (string.IsNullOrWhiteSpace(value.ToString()))
            return null;

        return new Uri($"ms-appdata:///{value}");
    }
    // ...
}
```

`VideoSourceConverter`然后，可以使用的实例将 URI 方案应用于 `ms-appdata:///` 应用的本地或临时数据文件夹中的媒体文件：

```xaml
<MediaElement Source="{Binding MediaSource, Converter={StaticResource VideoSourceConverter}}"
              ShowsPlaybackControls="True" />
```

有关 appdata URI 方案的详细信息，请参阅[appdata](/windows/uwp/app-resources/uri-schemes#ms-appdata)。

#### <a name="copying-a-media-file-to-the-apps-local-or-temporary-data-folder"></a>将媒体文件复制到应用的本地或临时数据文件夹

播放存储在应用的本地或临时数据文件夹中的媒体文件需要应用在其中复制媒体文件。 例如，可以通过从应用包中复制媒体文件来完成此操作：

```csharp
// This method copies the video from the app package to the app data
// directory for your app. To copy the video to the temp directory
// for your app, comment out the first line of code, and uncomment
// the second line of code.
public static async Task CopyVideoIfNotExists(string filename)
{
    string folder = FileSystem.AppDataDirectory;
    //string folder = Path.GetTempPath();
    string videoFile = Path.Combine(folder, "XamarinVideo.mp4");

    if (!File.Exists(videoFile))
    {
        using (Stream inputStream = await FileSystem.OpenAppPackageFileAsync(filename))
        {
            using (FileStream outputStream = File.Create(videoFile))
            {
                await inputStream.CopyToAsync(outputStream);
            }
        }
    }
}
```

> [!NOTE]
> 上面的代码示例使用 `FileSystem` 中包含的类 Xamarin.Essentials 。 有关详细信息，请参阅[ Xamarin.Essentials ：文件系统帮助](~/essentials/file-system-helpers.md?context=xamarin%2Fxamarin-forms&tabs=android)程序。

### <a name="play-media-from-the-device-library"></a>从设备库播放媒体

大多数新式移动设备和台式计算机都可以使用设备的照相机和麦克风录制视频和音频。 然后，创建的媒体将以文件的形式存储在设备上。 可以从库中检索这些文件并由播放 [`MediaElement`](xref:Xamarin.Forms.MediaElement) 。

每个平台都包含一个设备，使用户能够从设备的库中选择媒体。 在中 Xamarin.Forms ，平台项目可以调用此功能，并且可由 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 类调用。

示例应用程序中使用的视频选取依赖关系服务与[从图片库选取照片](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)中定义的服务非常相似，不同之处在于，选取器会返回文件名而不是 `Stream` 对象。 共享代码项目定义了一个名为 `IVideoPicker` 的接口，该接口定义了一个名为的方法 `GetVideoFileAsync` 。 然后，每个平台在类中实现此接口 `VideoPicker` 。

下面的代码示例演示如何从设备库检索媒体文件：

```csharp
string filename = await DependencyService.Get<IVideoPicker>().GetVideoFileAsync();
if (!string.IsNullOrWhiteSpace(filename))
{
    mediaElement.Source = new FileMediaSource
    {
        File = filename
    };
}
```

视频选取依赖项服务通过调用 `DependencyService.Get` 方法来调用，以获取 `IVideoPicker` 平台项目中接口的实现。 `GetVideoFileAsync`然后，在该实例上调用方法，并使用返回的文件名创建 [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource) 对象并将其设置为 [`Source`](xref:Xamarin.Forms.MediaElement.Source) 的属性 [`MediaElement`](xref:Xamarin.Forms.MediaElement) 。

## <a name="change-video-aspect-ratio"></a>更改视频纵横比

[`Aspect`](xref:Xamarin.Forms.MediaElement.Aspect)属性确定如何缩放视频媒体以适应显示区域。 默认情况下，此属性设置为 `AspectFit` 枚举成员，但它可以设置为任何 [`Aspect`](xref:Xamarin.Forms.Aspect) 枚举成员：

- `AspectFit`指示在保持纵横比的同时，要在显示区域中 letterboxed 视频。
- `AspectFill`指示将剪切视频以使其填充显示区域，同时保留纵横比。
- `Fill`指示将拉伸视频以填充显示区域。

## <a name="poll-for-position-data"></a>轮询位置数据

可绑定属性的属性更改通知 [`Position`](xref:Xamarin.Forms.MediaElement.Position) 仅在关键时刻触发，如播放开始和结束，以及暂停发生。 因此，到属性的数据绑定 `Position` 不会生成准确的位置数据。 相反，您必须设置计时器并轮询属性。

要执行此操作的一个好时机是在 `OnAppearing` 重写需要播放媒体的位置数据的页面中：

```csharp
bool polling = true;

protected override void OnAppearing()
{
    base.OnAppearing();

    Device.StartTimer(TimeSpan.FromMilliseconds(1000), () =>
    {
        Device.BeginInvokeOnMainThread(() =>
        {
            positionLabel.Text = mediaElement.Position.ToString("hh\\:mm\\:ss");
        });
        return polling;
    });
}

protected override void OnDisappearing()
{
    base.OnDisappearing();
    polling = false;
}
```

在此示例中， `OnAppearing` 重写启动一个计时器， `positionLabel` `Position` 每秒更新一次值。 每秒调用计时器回调，直到回调返回 `false` 。 发生页面导航时 `OnDisappearing` ，将执行替代，这将停止正在调用的计时器回调。

## <a name="understand-mediasource-types"></a>了解 MediaSource 类型

[`MediaElement`](xref:Xamarin.Forms.MediaElement)可以通过将媒体的属性设置 [`Source`](xref:Xamarin.Forms.MediaElement.Source) 为远程或本地媒体文件来播放媒体。 `Source`属性的类型为 [`MediaSource`](xref:Xamarin.Forms.MediaSource) ，此类定义两种静态方法：

- [`FromFile`](xref:Xamarin.Forms.MediaSource.FromFile*)返回 [`MediaSource`](xref:Xamarin.Forms.MediaSource) 自变量的实例 `string` 。
- [`FromUri`](xref:Xamarin.Forms.MediaSource.FromUri*)返回 [`MediaSource`](xref:Xamarin.Forms.MediaSource) 自变量的实例 `Uri` 。

此外， [`MediaSource`](xref:Xamarin.Forms.MediaSource) 类还具有 `MediaSource` 从和参数返回实例的隐式 `string` 运算符 `Uri` 。

> [!NOTE]
> 当在 [`Source`](xref:Xamarin.Forms.MediaElement.Source) XAML 中设置属性时，将调用类型转换器以 [`MediaSource`](xref:Xamarin.Forms.MediaSource) 从或返回实例 `string` `Uri` 。

[`MediaSource`](xref:Xamarin.Forms.MediaSource)类还具有两个派生类：

- [`UriMediaSource`](xref:Xamarin.Forms.UriMediaSource)用于从 URI 指定远程媒体文件的。 此类具有一个 [`Uri`](xref:Xamarin.Forms.UriMediaSource.Uri) 属性，该属性可以设置为 `Uri` 。
- [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource)用于指定中的本地媒体文件的 `string` 。 此类具有一个 [`File`](xref:Xamarin.Forms.FileMediaSource.File) 属性，该属性可以设置为 `string` 。 此外，此类具有隐式运算符，用于将转换 `string` 为 `FileMediaSource` 对象，将对象转换为 `FileMediaSource` `string` 。

> [!NOTE]
> 当 [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource) 在 XAML 中创建对象时，将调用类型转换器以 [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource) 从返回实例 `string` 。

## <a name="determine-mediaelement-status"></a>确定 MediaElement 状态

[`MediaElement`](xref:Xamarin.Forms.MediaElement)类定义一个名为、类型为的只读可绑定属性 [`CurrentState`](xref:Xamarin.Forms.MediaElement.CurrentState) [`MediaElementState`](xref:Xamarin.Forms.MediaElementState) 。 此属性指示控件的当前状态，例如是否正在播放或暂停媒体，或者，如果尚未准备好播放媒体，则为。

[`MediaElementState`](xref:Xamarin.Forms.MediaElementState)枚举定义以下成员：

- `Closed`指示 `MediaElement` 不包含媒体。
- `Opening`指示正在 `MediaElement` 验证并尝试加载指定的源。
- `Buffering`指示 `MediaElement` 正在加载要播放的媒体。 [`Position`](xref:Xamarin.Forms.MediaElement.Position)在此状态下，其属性不会提升。 如果 `MediaElement` 正在播放视频，它将继续显示最后显示的帧。
- `Playing`指示 `MediaElement` 正在播放媒体源。
- `Paused`指示不 `MediaElement` 向前移动其 [`Position`](xref:Xamarin.Forms.MediaElement.Position) 属性。 如果 `MediaElement` 正在播放视频，它将继续显示当前帧。
- `Stopped`指示 `MediaElement` 包含媒体，但未播放或暂停。 其 [`Position`](xref:Xamarin.Forms.MediaElement.Position) 属性为0，并且不会继续。 如果加载的媒体是视频，则会 `MediaElement` 显示第一帧。

[`CurrentState`](xref:Xamarin.Forms.MediaElement.CurrentState)使用传输控件时，通常不需要检查属性 [`MediaElement`](xref:Xamarin.Forms.MediaElement) 。 但是，当实现您自己的传输控制时，此属性会变得很重要。

## <a name="implement-custom-transport-controls"></a>实现自定义传输控件

媒体播放器的传输控制包括执行这些功能的按钮： "**播放**"、"**暂停**" 和 "**停止**"。 这些按钮通常使用熟悉的图标而非文本来标识，且“播放”和“暂停”功能通常合并成一个按钮 。

默认情况下， [`MediaElement`](xref:Xamarin.Forms.MediaElement) 播放控件处于禁用状态。 这使你能够以 `MediaElement` 编程方式控制，或者提供自己的传输控件。 为支持此， `MediaElement` 包括 [`Play`](xref:Xamarin.Forms.MediaElement.Play) 、 [`Pause`](xref:Xamarin.Forms.MediaElement.Pause) 和 [`Stop`](xref:Xamarin.Forms.MediaElement.Stop) 方法。

下面的 XAML 示例显示了一个包含 [`MediaElement`](xref:Xamarin.Forms.MediaElement) 和自定义传输控件的页面：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MediaElementDemos.CustomTransportPage"
             Title="Custom transport">
    <Grid>
        ...
        <MediaElement x:Name="mediaElement"
                      AutoPlay="False"
                      ... />
        <StackLayout BindingContext="{x:Reference mediaElement}"
                     ...>
            <Button Text="&#x25B6;&#xFE0F; Play"
                    HorizontalOptions="CenterAndExpand"
                    Clicked="OnPlayPauseButtonClicked">
                <Button.Triggers>
                    <DataTrigger TargetType="Button"
                                 Binding="{Binding CurrentState}"
                                 Value="{x:Static MediaElementState.Playing}">
                        <Setter Property="Text"
                                Value="&#x23F8; Pause" />
                    </DataTrigger>
                    <DataTrigger TargetType="Button"
                                 Binding="{Binding CurrentState}"
                                 Value="{x:Static MediaElementState.Buffering}">
                        <Setter Property="IsEnabled"
                                Value="False" />
                    </DataTrigger>
                </Button.Triggers>
            </Button>
            <Button Text="&#x23F9; Stop"
                    HorizontalOptions="CenterAndExpand"
                    Clicked="OnStopButtonClicked">
                <Button.Triggers>
                    <DataTrigger TargetType="Button"
                                 Binding="{Binding CurrentState}"
                                 Value="{x:Static MediaElementState.Stopped}">
                        <Setter Property="IsEnabled"
                                Value="False" />
                    </DataTrigger>
                </Button.Triggers>
            </Button>
        </StackLayout>
    </Grid>
</ContentPage>
```

在此示例中，自定义传输控件定义为 [`Button`](xref:Xamarin.Forms.Button) 对象。 但是，只有两个 `Button` 对象，第一个对象 `Button` 表示 "**播放**" 和 "**暂停**"，第二个对象 `Button` 表示 "**停止**"。 [`DataTrigger`](xref:Xamarin.Forms.DataTrigger)对象用于启用和禁用按钮，并用于在**播放**和**暂停**之间切换第一个按钮。 有关数据触发器的详细信息，请参阅[ Xamarin.Forms 触发器](~/xamarin-forms/app-fundamentals/triggers.md)。

代码隐藏文件具有事件的处理程序 [`Clicked`](xref:Xamarin.Forms.Button.Clicked) ：

```csharp
void OnPlayPauseButtonClicked(object sender, EventArgs args)
{
    if (mediaElement.CurrentState == MediaElementState.Stopped ||
        mediaElement.CurrentState == MediaElementState.Paused)
    {
        mediaElement.Play();
    }
    else if (mediaElement.CurrentState == MediaElementState.Playing)
    {
        mediaElement.Pause();
    }
}

void OnStopButtonClicked(object sender, EventArgs args)
{
    mediaElement.Stop();
}
```

启用后，可以按 "**播放**" 按钮开始播放：

[![在 iOS 和 Android 上使用自定义传输控件的 MediaElement 屏幕截图](mediaelement-images/custom-transport-playback.png "播放视频的 MediaElement")](mediaelement-images/custom-transport-playback-large.png#lightbox "播放视频的 MediaElement")

按 "**暂停**" 按钮会导致播放暂停：

[![在 iOS 和 Android 上暂停播放 MediaElement 的屏幕截图](mediaelement-images/custom-transport-paused.png "带有暂停视频的 MediaElement")](mediaelement-images/custom-transport-paused-large.png#lightbox "带有暂停视频的 MediaElement")

按 "**停止**" 按钮将停止播放，并将媒体文件的位置返回到开始位置。

## <a name="implement-a-custom-position-bar"></a>实现自定义位置栏

由每个平台实现的传输控件都有一个定位条。 此栏类似于滑块，并显示介质在其总持续时间内的当前位置。 此外，您还可以操作位置栏以便向前或向后移动到视频中的新位置。

实现自定义位置栏需要知道媒体的持续时间和当前播放位置。 此数据在 [`Duration`](xref:Xamarin.Forms.MediaElement.Duration) 和属性中可用 [`Position`](xref:Xamarin.Forms.MediaElement.Position) 。

> [!IMPORTANT]
> [`Position`](xref:Xamarin.Forms.MediaElement.Position)必须轮询，才能获取准确的位置数据。 有关详细信息，请参阅[轮询位置数据](#poll-for-position-data)。

可以使用来实现自定义位置栏 [`Slider`](xref:Xamarin.Forms.Slider) ，如下面的示例中所示：

```csharp
public class PositionSlider : Slider
{
    public static readonly BindableProperty DurationProperty =
        BindableProperty.Create(nameof(Duration), typeof(TimeSpan), typeof(PositionSlider), new TimeSpan(1),
                                propertyChanged: (bindable, oldValue, newValue) =>
                                {
                                    ((PositionSlider)bindable).SetTimeToEnd();
                                    double seconds = ((TimeSpan)newValue).TotalSeconds;
                                    ((Slider)bindable).Maximum = seconds <= 0 ? 1 : seconds;
                                });

    public TimeSpan Duration
    {
        get { return (TimeSpan)GetValue(DurationProperty); }
        set { SetValue(DurationProperty, value); }
    }

    public static readonly BindableProperty PositionProperty =
        BindableProperty.Create(nameof(Position), typeof(TimeSpan), typeof(PositionSlider), new TimeSpan(0),
                                propertyChanged: (bindable, oldValue, newValue) => ((PositionSlider)bindable).SetTimeToEnd());

    public TimeSpan Position
    {
        get { return (TimeSpan)GetValue(PositionProperty); }
        set { SetValue(PositionProperty, value); }
    }

    static readonly BindablePropertyKey TimeToEndPropertyKey =
        BindableProperty.CreateReadOnly(nameof(TimeToEnd), typeof(TimeSpan), typeof(PositionSlider), new TimeSpan());

    public static readonly BindableProperty TimeToEndProperty = TimeToEndPropertyKey.BindableProperty;

    public TimeSpan TimeToEnd
    {
        get { return (TimeSpan)GetValue(TimeToEndProperty); }
        private set { SetValue(TimeToEndPropertyKey, value); }
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

    void SetTimeToEnd()
    {
        TimeToEnd = Duration - Position;
    }
}
```

`PositionSlider`类定义其自己的 `Duration` 可 `Position` 绑定属性和可 `TimeToEnd` 绑定属性。 所有三个属性都属于类型 `TimeSpan` 。 属性的属性更改处理程序 `Duration` 将 `Maximum` 的属性设置 [`Slider`](xref:Xamarin.Forms.Slider) 为值的 `TotalSeconds` 属性 `TimeSpan` 。 `TimeToEnd`属性是基于和属性的更改计算的 `Duration` `Position` ，并在媒体的持续时间内开始，并在播放过程中减少到零。

`PositionSlider`当移动时，将从基础更新， [`Slider`](xref:Xamarin.Forms.Slider) `Slider` 以指示媒体应为高级或反转到新位置。 在 `PropertyChanged` 构造函数中的处理程序中检测到这种情况 `PositionSlider` 。 处理程序检查 `Value` 属性中的更改，并且如果它与 `Position` 属性不同，那么 `Position` 属性将从 `Value` 属性进行设置。 有关使用的详细信息 [`Slider`](xref:Xamarin.Forms.Slider) ，请参阅[ Xamarin.Forms 滑块](~/xamarin-forms/user-interface/slider.md)

> [!NOTE]
> 在 Android 上， [`Slider`](xref:Xamarin.Forms.Slider) 只具有1000个离散步骤，而不考虑 `Minimum` 和 `Maximum` 设置。 如果媒体长度大于1000秒，则两个不同的 `Position` 值与相同 `Value` `Slider` 。 这就是上面的代码检查新位置和现有位置是否大于总体持续时间的百分之一的原因。

下面的示例演示如何 `PositionSlider` 在页面上使用：

```xaml
<controls:PositionSlider x:Name="positionSlider"
                         BindingContext="{x:Reference mediaElement}"
                         Duration="{Binding Duration}"
                         ValueChanged="OnPositionSliderValueChanged">
    <controls:PositionSlider.Triggers>
        <DataTrigger TargetType="controls:PositionSlider"
                     Binding="{Binding CurrentState}"
                     Value="{x:Static MediaElementState.Buffering}">
            <Setter Property="IsEnabled" Value="False" />
        </DataTrigger>
    </controls:PositionSlider.Triggers>
</controls:PositionSlider>
```

在此示例中， `Duration` 的属性 `PositionSlider` 被数据绑定到 [`Duration`](xref:Xamarin.Forms.MediaElement.Duration) 的属性 [`MediaElement`](xref:Xamarin.Forms.MediaElement) 。 更改的 [`Value`](xref:Xamarin.Forms.Slider.Value) 属性时 [`Slider`](xref:Xamarin.Forms.Slider) ，将 `ValueChanged` 激发事件并 `OnPositionSliderValueChanged` 执行处理程序。 此处理程序将 [`MediaElement.Position`](xref:Xamarin.Forms.MediaElement.Position) 属性设置为属性的值 `PositionSlider.Position` 。 因此，将 `Slider` 结果拖动到媒体播放位置：

[![在 iOS 和 Android 上具有自定义位置栏的 MediaElement 屏幕截图](mediaelement-images/custom-position-bar.png "使用自定义位置栏的 MediaElement")](mediaelement-images/custom-position-bar-large.png#lightbox "使用自定义位置栏的 MediaElement")

此外， [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) 对象用于在 `PositionSlider` 媒体缓冲时禁用。 有关数据触发器的详细信息，请参阅[ Xamarin.Forms 触发器](~/xamarin-forms/app-fundamentals/triggers.md)。

## <a name="implement-a-custom-volume-control"></a>实现自定义卷控件

每个平台实现的媒体播放控件都包含一个卷条。 此栏类似于滑块，用于显示介质的音量。 此外，还可以操作音量栏来增加或减少音量。

自定义卷条可以使用来实现 [`Slider`](xref:Xamarin.Forms.Slider) ，如以下示例中所示：

```xaml
<StackLayout>
    <MediaElement AutoPlay="False"
                  Source="{StaticResource AdvancedAsync}" />
    <Slider Maximum="1.0"
            Minimum="0.0"
            Value="{Binding Volume}"
            Rotation="270"
            WidthRequest="100" />
</StackLayout>
```

在此示例中， [`Slider`](xref:Xamarin.Forms.Slider) 数据将其 `Value` 属性绑定到 [`Volume`](xref:Xamarin.Forms.MediaElement.Volume) 的属性 [`MediaElement`](xref:Xamarin.Forms.MediaElement) 。 这是可能的，因为 `Volume` 属性使用 `TwoWay` 绑定。 因此，更改 `Value` 属性将导致 `Volume` 属性发生变化。

> [!NOTE]
> [`Volume`](xref:Xamarin.Forms.MediaElement.Volume)属性具有 vlidation 回调，可确保其值大于或等于0.0 且小于或等于1.0。

有关使用的详细信息 [`Slider`](xref:Xamarin.Forms.Slider) ，请参阅[ Xamarin.Forms 滑块](~/xamarin-forms/user-interface/slider.md)

## <a name="related-links"></a>相关链接

- [MediaElementDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-mediaelementdemos/)
- [URI 方案](/windows/uwp/app-resources/uri-schemes)
- [Xamarin.Forms 触发器](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin.Forms滑动](~/xamarin-forms/user-interface/slider.md)
- [Android：支持的媒体格式](https://developer.android.com/guide/topics/media/media-formats)
- [UWP：支持的编解码器](/windows/uwp/audio-video-camera/supported-codecs)
