---
title: 访问设备的视频库
description: 本文说明如何使用 Xamarin.Forms 在视频播放器应用程序中访问设备的视频库。
ms.prod: xamarin
ms.assetid: 364C1D43-EAAE-45B9-BE24-0DA5AE74C4D9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ff9f44ad790f3acda1e855f3308942bb702e66ee
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563895"
---
# <a name="accessing-the-devices-video-library"></a>访问设备的视频库

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)

大多数现代移动设备和台式电脑都能够使用其设备的摄像头录制视频。 用户创建的视频之后将以文件形式存储在设备上。 可从图片库中检索这些文件，并通过 `VideoPlayer` 类像任何其他视频一样播放它们。

## <a name="the-photo-picker-dependency-service"></a>照片选取器依赖项服务

每个平台都包含允许用户从设备图片库中选择照片或视频的工具。 从设备图片库播放视频的第一步是构建在每个平台上调用该图片选取器的依赖项服务。 下面描述的依赖项服务非常类似于[从图片库选取照片](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)一文中所描述的依赖项服务，除了视频选取器返回的是文件名，而不是 `Stream` 对象。

.NET Standard 库项目为依赖项服务定义名为 `IVideoPicker` 的接口：

```csharp
namespace FormsVideoLibrary
{
    public interface IVideoPicker
    {
        Task<string> GetVideoFileAsync();
    }
}
```

每个平台都包含实现此接口的 `VideoPicker` 类。

### <a name="the-ios-video-picker"></a>iOS 视频选取器

iOS `VideoPicker` 使用 iOS [`UIImagePickerController`](xref:UIKit.UIImagePickerController) 访问图片库，并指定在 iOS `MediaType` 属性中其应限制为视频（称为“电影”）。 请注意，`VideoPicker` 显式实现 `IVideoPicker` 接口。 还要注意将此类标识为依赖项服务的 `Dependency` 属性。 这是 Xamarin.Forms 在平台项目中找到依赖项服务的两个要求：

```csharp
using System;
using System.Threading.Tasks;
using UIKit;
using Xamarin.Forms;

[assembly: Dependency(typeof(FormsVideoLibrary.iOS.VideoPicker))]

namespace FormsVideoLibrary.iOS
{
    public class VideoPicker : IVideoPicker
    {
        TaskCompletionSource<string> taskCompletionSource;
        UIImagePickerController imagePicker;

        public Task<string> GetVideoFileAsync()
        {
            // Create and define UIImagePickerController
            imagePicker = new UIImagePickerController
            {
                SourceType = UIImagePickerControllerSourceType.SavedPhotosAlbum,
                MediaTypes = new string[] { "public.movie" }
            };

            // Set event handlers
            imagePicker.FinishedPickingMedia += OnImagePickerFinishedPickingMedia;
            imagePicker.Canceled += OnImagePickerCancelled;

            // Present UIImagePickerController;
            UIWindow window = UIApplication.SharedApplication.KeyWindow;
            var viewController = window.RootViewController;
            viewController.PresentViewController(imagePicker, true, null);

            // Return Task object
            taskCompletionSource = new TaskCompletionSource<string>();
            return taskCompletionSource.Task;
        }

        void OnImagePickerFinishedPickingMedia(object sender, UIImagePickerMediaPickedEventArgs args)
        {
            if (args.MediaType == "public.movie")
            {
                taskCompletionSource.SetResult(args.MediaUrl.AbsoluteString);
            }
            else
            {
                taskCompletionSource.SetResult(null);
            }
            imagePicker.DismissModalViewController(true);
        }

        void OnImagePickerCancelled(object sender, EventArgs args)
        {
            taskCompletionSource.SetResult(null);
            imagePicker.DismissModalViewController(true);
        }
    }
}
```

### <a name="the-android-video-picker"></a>Android 视频选取器

`IVideoPicker` 的 Android 实现需要回调方法，该回调方法是应用程序活动的一部分。 因此，`MainActivity` 类定义两个属性，即字段和回调方法：

```csharp
namespace VideoPlayerDemos.Droid
{
    ···
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
    {
        protected override void OnCreate(Bundle bundle)
        {
            Current = this;
            ···
        }

        // Field, properties, and method for Video Picker
        public static MainActivity Current { private set; get; }

        public static readonly int PickImageId = 1000;

        public TaskCompletionSource<string> PickImageTaskCompletionSource { set; get; }

        protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
        {
            base.OnActivityResult(requestCode, resultCode, data);

            if (requestCode == PickImageId)
            {
                if ((resultCode == Result.Ok) && (data != null))
                {
                    // Set the filename as the completion of the Task
                    PickImageTaskCompletionSource.SetResult(data.DataString);
                }
                else
                {
                    PickImageTaskCompletionSource.SetResult(null);
                }
            }
        }
    }
}
```

`MainActivity` 中的 `OnCreate` 方法将自己的实例存储在静态 `Current` 属性中。 这使得 `IVideoPicker` 的实现可以获得 `MainActivity` 实例，以启动“选择视频”选择器：

```csharp
using System;
using System.Threading.Tasks;
using Android.Content;
using Xamarin.Forms;

// Need application's MainActivity
using VideoPlayerDemos.Droid;

[assembly: Dependency(typeof(FormsVideoLibrary.Droid.VideoPicker))]

namespace FormsVideoLibrary.Droid
{
    public class VideoPicker : IVideoPicker
    {
        public Task<string> GetVideoFileAsync()
        {
            // Define the Intent for getting images
            Intent intent = new Intent();
            intent.SetType("video/*");
            intent.SetAction(Intent.ActionGetContent);

            // Get the MainActivity instance
            MainActivity activity = MainActivity.Current;

            // Start the picture-picker activity (resumes in MainActivity.cs)
            activity.StartActivityForResult(
                Intent.CreateChooser(intent, "Select Video"),
                MainActivity.PickImageId);

            // Save the TaskCompletionSource object as a MainActivity property
            activity.PickImageTaskCompletionSource = new TaskCompletionSource<string>();

            // Return Task object
            return activity.PickImageTaskCompletionSource.Task;
        }
    }
}
```

添加到 `MainActivity` 对象的部分是 [VideoPlayerDemos](/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos) 解决方案中惟一的代码，在该方案中需更改常规应用程序代码以支持 `FormsVideoLibrary` 类。

### <a name="the-uwp-video-picker"></a>UWP 视频选取器

`IVideoPicker` 接口的 UWP 实现使用 UWP [`FileOpenPicker`](/uwp/api/Windows.Storage.Pickers.FileOpenPicker/)。 选取器从图片库开始搜索文件，并将文件类型限制为 MP4 和 WMV（Windows Media 视频）：

```csharp
using System;
using System.Threading.Tasks;
using Windows.Storage;
using Windows.Storage.Pickers;
using Xamarin.Forms;

[assembly: Dependency(typeof(FormsVideoLibrary.UWP.VideoPicker))]

namespace FormsVideoLibrary.UWP
{
    public class VideoPicker : IVideoPicker
    {
        public async Task<string> GetVideoFileAsync()
        {
            // Create and initialize the FileOpenPicker
            FileOpenPicker openPicker = new FileOpenPicker
            {
                ViewMode = PickerViewMode.Thumbnail,
                SuggestedStartLocation = PickerLocationId.PicturesLibrary
            };

            openPicker.FileTypeFilter.Add(".wmv");
            openPicker.FileTypeFilter.Add(".mp4");

            // Get a file and return the path
            StorageFile storageFile = await openPicker.PickSingleFileAsync();
            return storageFile?.Path;
        }
    }
}
```

## <a name="invoking-the-dependency-service"></a>调用依赖项服务

[VideoPlayerDemos](/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos) 程序的“播放库视频”页面演示如何使用视频选取器依赖项服务 。 XAML 文件包含 `VideoPlayer` 实例和标记为“显示视频库”的 `Button`：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.PlayLibraryVideoPage"
             Title="Play Library Video">
    <StackLayout>
        <video:VideoPlayer x:Name="videoPlayer"
                           VerticalOptions="FillAndExpand" />

        <Button Text="Show Video Library"
                Margin="10"
                HorizontalOptions="Center"
                Clicked="OnShowVideoLibraryClicked" />
    </StackLayout>
</ContentPage>
```

代码隐藏文件包含 `Button` 的 `Clicked` 处理程序。 调用依赖项服务需要调用 `DependencyService.Get` 来获取平台项目中 `IVideoPicker` 接口的实现。 然后在该实例上调用 `GetVideoFileAsync` 方法：

```csharp
namespace VideoPlayerDemos
{
    public partial class PlayLibraryVideoPage : ContentPage
    {
        public PlayLibraryVideoPage()
        {
            InitializeComponent();
        }

       async void OnShowVideoLibraryClicked(object sender, EventArgs args)
        {
            Button btn = (Button)sender;
            btn.IsEnabled = false;

            string filename = await DependencyService.Get<IVideoPicker>().GetVideoFileAsync();

            if (!String.IsNullOrWhiteSpace(filename))
            {
                videoPlayer.Source = new FileVideoSource
                {
                    File = filename
                };
            }

            btn.IsEnabled = true;
        }
    }
}
```

然后，`Clicked` 处理程序使用该文件名创建 `FileVideoSource` 对象，并将其设置为 `VideoPlayer` 的 `Source` 属性。

每个 `VideoPlayerRenderer` 类都包含类型为 `FileVideoSource` 的对象的 `SetSource` 方法中的代码。 如下所示：

### <a name="handling-ios-files"></a>处理 iOS 文件

iOS 版本的 `VideoPlayerRenderer` 通过使用带有文件名的静态 `Asset.FromUrl` 方法处理 `FileVideoSource` 对象。 这会创建一个 `AVAsset` 对象，表示设备图片库中的文件：

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
            else if (Element.Source is FileVideoSource)
            {
                string uri = (Element.Source as FileVideoSource).File;

                if (!String.IsNullOrWhiteSpace(uri))
                {
                    asset = AVAsset.FromUrl(new NSUrl(uri));
                }
            }
            ···
        }
        ···
    }
}
```

### <a name="handling-android-files"></a>处理 Android 文件

处理类型为 `FileVideoSource` 的对象时，`VideoPlayerRenderer` 的 Android 实现使用 `VideoView` 的 `SetVideoPath` 方法指定设备图片库中的文件：

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
            else if (Element.Source is FileVideoSource)
            {
                string filename = (Element.Source as FileVideoSource).File;

                if (!String.IsNullOrWhiteSpace(filename))
                {
                    videoView.SetVideoPath(filename);
                    hasSetSource = true;
                }
            }
            ···
        }
        ···
    }
}
```

### <a name="handling-uwp-files"></a>处理 UWP 文件

处理类型为 `FileVideoSource` 的对象时，`SetSource` 方法的 UWP 实现需要创建 `StorageFile` 对象、打开该文件进行读取并将流对象传递给 `MediaElement` 的 `SetSource` 方法：

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
        async void SetSource()
        {
            bool hasSetSource = false;
            ···
            else if (Element.Source is FileVideoSource)
            {
                // Code requires Pictures Library in Package.appxmanifest Capabilities to be enabled
                string filename = (Element.Source as FileVideoSource).File;

                if (!String.IsNullOrWhiteSpace(filename))
                {
                    StorageFile storageFile = await StorageFile.GetFileFromPathAsync(filename);
                    IRandomAccessStreamWithContentType stream = await storageFile.OpenReadAsync();
                    Control.SetSource(stream, storageFile.ContentType);
                    hasSetSource = true;
                }
            }
            ···
        }
        ···
    }
}
```

对于每个平台，由于文件就在设备上，不需要下载，因此视频几乎可在视频源设置完成后立即开始播放。

## <a name="related-links"></a>相关链接

- [视频播放器演示（示例）](/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)
- [从图片库中选取照片](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)