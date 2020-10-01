---
title: 将视频源绑定到播放器
description: 本文介绍如何使用 Xamarin.Forms 将视频源绑定到视频播放器。
ms.prod: xamarin
ms.assetid: 504E0C7E-051A-4AF2-B654-BAB4D0957928
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d417f5e0b2b0bea5a3e129c87e94b289dc7201ed
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562478"
---
# <a name="binding-video-sources-to-the-player"></a>将视频源绑定到播放器

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)

如果将 `VideoPlayer` 视图的 `Source` 属性设置为新视频文件，当前视频会停止播放并且开始播放新视频。 这通过[“VideoPlayerDemos”](/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)示例的“选择 Web 视频”页演示 。 该页包含 `ListView`，并且三个视频的标题引用自“App.xaml”文件：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.SelectWebVideoPage"
             Title="Select Web Video">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="2*" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <video:VideoPlayer x:Name="videoPlayer"
                           Grid.Row="0" />

        <ListView Grid.Row="1"
                  ItemSelected="OnListViewItemSelected">
            <ListView.ItemsSource>
                <x:Array Type="{x:Type x:String}">
                    <x:String>Elephant's Dream</x:String>
                    <x:String>Big Buck Bunny</x:String>
                    <x:String>Sintel</x:String>
                </x:Array>
            </ListView.ItemsSource>
        </ListView>
    </Grid>
</ContentPage>
```

选中视频后，执行代码隐藏文件中的 `ItemSelected` 事件处理程序。 该处理程序删除标题中的任何空格和撇号，并将其用作密钥以获取 App.xaml 文件中定义的某一资源。 该 `UriVideoSource` 对象设置为 `VideoPlayer` 的 `Source` 属性。

```csharp
namespace VideoPlayerDemos
{
    public partial class SelectWebVideoPage : ContentPage
    {
        public SelectWebVideoPage()
        {
            InitializeComponent();
        }

        void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs args)
        {
            if (args.SelectedItem != null)
            {
                string key = ((string)args.SelectedItem).Replace(" ", "").Replace("'", "");
                videoPlayer.Source = (UriVideoSource)Application.Current.Resources[key];
            }
        }
    }
}
```

首次加载页面时，未选择 `ListView` 中的任何项，因此必须选择一个视频开始播放：

[![选择 Web 视频](source-bindings-images/selectwebvideo-small.png "选择 Web 视频")](source-bindings-images/selectwebvideo-large.png#lightbox "选择 Web 视频")

`VideoPlayer` 的 `Source` 属性由可绑定属性支持，这意味着它可以是数据绑定的目标。 这通过“绑定到视频播放器”页进行演示。 BindToVideoPlayer.xaml 文件中的标记由以下封装视频标题和相应 `VideoSource` 对象的类支持：

```csharp
namespace VideoPlayerDemos
{
    public class VideoInfo
    {
        public string DisplayName { set; get; }

        public VideoSource VideoSource { set; get; }

        public override string ToString()
        {
            return DisplayName;
        }
    }
}
```

BindToVideoPlayer.xaml 文件中的 `ListView` 包含这些 `VideoInfo` 对象的数组，其中每个对象都由视频标题和 `UriVideoSource` 对象初始化（`UriVideoSource` 对象来自 App.xaml 中的资源字典） ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:VideoPlayerDemos"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.BindToVideoPlayerPage"
             Title="Bind to VideoPlayer">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="2*" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <video:VideoPlayer x:Name="videoPlayer"
                           Grid.Row="0"
                           Source="{Binding Source={x:Reference listView},
                                            Path=SelectedItem.VideoSource}" />

        <ListView x:Name="listView"
                  Grid.Row="1">
            <ListView.ItemsSource>
                <x:Array Type="{x:Type local:VideoInfo}">
                    <local:VideoInfo DisplayName="Elephant's Dream"
                                     VideoSource="{StaticResource ElephantsDream}" />

                    <local:VideoInfo DisplayName="Big Buck Bunny"
                                     VideoSource="{StaticResource BigBuckBunny}" />

                    <local:VideoInfo DisplayName="Sintel"
                                     VideoSource="{StaticResource Sintel}" />
                </x:Array>
            </ListView.ItemsSource>
        </ListView>
    </Grid>
</ContentPage>
```

`VideoPlayer` 的 `Source` 属性绑定到 `ListView`。 绑定的 `Path` 指定为由两个属性所组成的复合路径 `SelectedItem.VideoSource`：`SelectedItem` 是 `ListView` 的属性。 所选项目类型为 `VideoInfo`，它具有 `VideoSource` 属性。

正如第一个“选择 Web 视频”页一样，最初没有从 `ListView` 中选择任何项，因此需要选择一个视频，然后开始播放。

## <a name="related-links"></a>相关链接

- [视频播放器演示（示例）](/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)