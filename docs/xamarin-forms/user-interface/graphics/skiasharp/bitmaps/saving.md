---
title: 将 SkiaSharp 位图保存到文件中
description: 浏览 SkiaSharp 支持的各种文件格式，以便在用户的照片库中保存位图。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 2D696CB6-B31B-42BC-8D3B-11D63B1E7D9C
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2e0c4d247f3ecf9c1b8e077c4a96712e9ed138ca
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562946"
---
# <a name="saving-skiasharp-bitmaps-to-files"></a>将 SkiaSharp 位图保存到文件中

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

在 SkiaSharp 应用程序创建或修改位图后，应用程序可能需要将位图保存到用户的照片库：

![保存位图](saving-images/SavingSample.png "保存位图")

此任务包括两个步骤：

- 将 SkiaSharp 位图转换为特定文件格式的数据，例如 JPEG 或 PNG。
- 使用平台特定的代码将结果保存到照片库。

## <a name="file-formats-and-codecs"></a>文件格式和编解码器

当今最常用的位图文件格式使用压缩来减少存储空间。 压缩技术的两大类别称为有 _损_ 和 _无损_。 这些术语指示压缩算法是否会导致数据丢失。

最常见的损失格式由联合图像专家组开发，称为 JPEG。 JPEG 压缩算法使用称为 _离散余弦转换_的数学工具来分析图像，并尝试删除对保留图像视觉保真不重要的数据。 可以使用通常称为 " _质量_" 的设置来控制压缩程度。 较高的质量设置会导致较大的文件。

与此相反，无损压缩算法会分析图像的重复和像素模式，这种模式可通过减少数据的方式进行编码，但不会导致丢失任何信息。 原始位图数据可完全从压缩文件还原。 目前使用的主要无损压缩文件格式是可移植网络图形 (PNG) 。

通常，JPEG 用于照片，而 PNG 用于已手动或算法生成的映像。 减小某些文件大小的任何无损压缩算法都必须增加其他文件的大小。 幸运的是，这种大小的增加通常仅适用于包含大量随机 (或似乎随机) 信息的数据。

压缩算法非常复杂，足以保证描述压缩和解压缩过程的两个术语：

- _解码_ &mdash; 读取位图文件格式并将其解压缩
- _编码_ &mdash; 压缩位图并写入位图文件格式

[`SKBitmap`](xref:SkiaSharp.SKBitmap)类包含多个名为 `Decode` 的方法，这些方法 `SKBitmap` 从压缩的源创建。 所需的只是提供文件名、流或字节数组。 解码器可以确定文件格式，并将其交给正确的内部解码功能。

此外， [`SKCodec`](xref:SkiaSharp.SKCodec) 类具有两个名为 `Create` 的方法，该方法可以 `SKCodec` 从压缩的源创建对象，并允许应用程序在解码过程中更多地涉及。  (`SKCodec` 类将在 [**SkiaSharp 位图的动画处理**](animating.md#gif-animation) 中显示，并对动画 GIF 文件进行解码。 ) 

对位图进行编码时，需要更多信息：编码器必须知道应用程序想要使用的特定文件格式 (JPEG 或 PNG 或其他) 的内容。 如果需要有损格式，则编码还必须了解所需的质量级别。

`SKBitmap`类定义了一个 [`Encode`](xref:SkiaSharp.SKBitmap.Encode(SkiaSharp.SKWStream,SkiaSharp.SKEncodedImageFormat,System.Int32)) 具有以下语法的方法：

```csharp
public Boolean Encode (SKWStream dst, SKEncodedImageFormat format, Int32 quality)
```

稍后将对此方法进行更详细的介绍。 编码的位图将写入到可写流。  (中的 "W" `SKWStream` 表示 "可写"。第二个和第三个参数 ) 指定文件格式，并将有损格式的 () 所需质量介于0到100之间。

此外， [`SKImage`](xref:SkiaSharp.SKImage) 和 [`SKPixmap`](xref:SkiaSharp.SKPixmap) 类还定义 `Encode` 了一些更通用的方法，您可能更喜欢这些方法。 您可以 `SKImage` `SKBitmap` 使用静态方法从对象轻松创建对象 [`SKImage.FromBitmap`](xref:SkiaSharp.SKImage.FromBitmap(SkiaSharp.SKBitmap)) 。 您可以 `SKPixmap` `SKBitmap` 使用方法从对象获取对象 [`PeekPixels`](xref:SkiaSharp.SKBitmap.PeekPixels) 。

[`Encode`](xref:SkiaSharp.SKImage.Encode)定义的方法之一 `SKImage` 没有参数，并自动保存为 PNG 格式。 此无参数方法非常易于使用。

## <a name="platform-specific-code-for-saving-bitmap-files"></a>用于保存位图文件的特定于平台的代码

将 `SKBitmap` 对象编码为特定的文件格式时，通常会保留一个流对象，其中包含某种类型的流对象或一组数据。 某些 `Encode` 方法 (包括不含参数的方法 `SKImage`) 返回 [`SKData`](xref:SkiaSharp.SKData) 对象，可以使用方法将其转换为字节数组 [`ToArray`](xref:SkiaSharp.SKData.ToArray) 。 然后，必须将此数据保存到文件。

在应用程序本地存储中保存到文件相当容易，因为可以 `System.IO` 对此任务使用标准类和方法。 此方法在以下文章中进行演示：在连接中对 [**SkiaSharp 位图**](animating.md#bitmap-animation) 进行动画处理，并对 Mandelbrot 集的一系列位图进行动画处理。

如果要使文件与其他应用程序共享，则必须将该文件保存到用户的照片库。 此任务需要平台特定的代码和使用 Xamarin.Forms [`DependencyService`](xref:Xamarin.Forms.DependencyService) 。

[**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)应用程序中的**SkiaSharpFormsDemo**项目定义 `IPhotoLibrary` 与类一起使用的接口 `DependencyService` 。 这会定义方法的语法 `SavePhotoAsync` ：

```csharp
public interface IPhotoLibrary
{
    Task<Stream> PickPhotoAsync();

    Task<bool> SavePhotoAsync(byte[] data, string folder, string filename);
}
```

此接口还定义了 `PickPhotoAsync` 方法，该方法用于为设备的照片库打开特定于平台的文件选取器。

对于 `SavePhotoAsync` ，第一个参数是一个字节数组，其中包含已编码为特定文件格式（例如 JPEG 或 PNG）的位图。 应用程序可能需要将其创建的所有位图隔离到特定的文件夹中，该文件夹在下一个参数中指定，后跟文件名。 方法返回一个布尔值，指示是否成功。

以下部分介绍如何 `SavePhotoAsync` 在每个平台上实现。

### <a name="the-ios-implementation"></a>IOS 实现

的 iOS 实现 `SavePhotoAsync` 使用的 [`SaveToPhotosAlbum`](xref:UIKit.UIImage.SaveToPhotosAlbum*) 方法 `UIImage` ：

```csharp
public class PhotoLibrary : IPhotoLibrary
{
    ···
    public Task<bool> SavePhotoAsync(byte[] data, string folder, string filename)
    {
        NSData nsData = NSData.FromArray(data);
        UIImage image = new UIImage(nsData);
        TaskCompletionSource<bool> taskCompletionSource = new TaskCompletionSource<bool>();

        image.SaveToPhotosAlbum((UIImage img, NSError error) =>
        {
            taskCompletionSource.SetResult(error == null);
        });

        return taskCompletionSource.Task;
    }
}
```

遗憾的是，没有办法为映像指定文件名或文件夹。

IOS 项目中的 **info.plist** 文件需要一个键，指示它将图像添加到照片库：

```xml
<key>NSPhotoLibraryAddUsageDescription</key>
<string>SkiaSharp Forms Demos adds images to your photo library</string>
```

小心！ 仅访问照片库的权限密钥非常相似，但并不相同：

```xml
<key>NSPhotoLibraryUsageDescription</key>
<string>SkiaSharp Forms Demos accesses your photo library</string>
```

### <a name="the-android-implementation"></a>Android 实现

Android 实现 `SavePhotoAsync` 首先检查 `folder` 参数是否为 `null` 或空字符串。 如果是这样，则位图保存在照片库的根目录中。 否则，将获取该文件夹，如果该文件夹不存在，则创建它：

```csharp
public class PhotoLibrary : IPhotoLibrary
{
    ···
    public async Task<bool> SavePhotoAsync(byte[] data, string folder, string filename)
    {
        try
        {
            File picturesDirectory = Environment.GetExternalStoragePublicDirectory(Environment.DirectoryPictures);
            File folderDirectory = picturesDirectory;

            if (!string.IsNullOrEmpty(folder))
            {
                folderDirectory = new File(picturesDirectory, folder);
                folderDirectory.Mkdirs();
            }

            using (File bitmapFile = new File(folderDirectory, filename))
            {
                bitmapFile.CreateNewFile();

                using (FileOutputStream outputStream = new FileOutputStream(bitmapFile))
                {
                    await outputStream.WriteAsync(data);
                }

                // Make sure it shows up in the Photos gallery promptly.
                MediaScannerConnection.ScanFile(MainActivity.Instance,
                                                new string[] { bitmapFile.Path },
                                                new string[] { "image/png", "image/jpeg" }, null);
            }
        }
        catch
        {
            return false;
        }

        return true;
    }
}
```

对的调用 `MediaScannerConnection.ScanFile` 并不是绝对必需的，但如果您通过立即检查照片库来测试您的程序，它将通过更新库的 "库" 视图来提供很多帮助。

**AndroidManifest.xml**文件需要以下权限标记：

```xml
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

### <a name="the-uwp-implementation"></a>UWP 实现

的 UWP 实现 `SavePhotoAsync` 在结构上非常类似于 Android 实现：

```csharp
public class PhotoLibrary : IPhotoLibrary
{
    ···
    public async Task<bool> SavePhotoAsync(byte[] data, string folder, string filename)
    {
        StorageFolder picturesDirectory = KnownFolders.PicturesLibrary;
        StorageFolder folderDirectory = picturesDirectory;

        // Get the folder or create it if necessary
        if (!string.IsNullOrEmpty(folder))
        {
            try
            {
                folderDirectory = await picturesDirectory.GetFolderAsync(folder);
            }
            catch
            { }

            if (folderDirectory == null)
            {
                try
                {
                    folderDirectory = await picturesDirectory.CreateFolderAsync(folder);
                }
                catch
                {
                    return false;
                }
            }
        }

        try
        {
            // Create the file.
            StorageFile storageFile = await folderDirectory.CreateFileAsync(filename,
                                                CreationCollisionOption.GenerateUniqueName);

            // Convert byte[] to Windows buffer and write it out.
            IBuffer buffer = WindowsRuntimeBuffer.Create(data, 0, data.Length, data.Length);
            await FileIO.WriteBufferAsync(storageFile, buffer);
        }
        catch
        {
            return false;
        }

        return true;
    }
}
```

**Appxmanifest.xml**文件的 "**功能**" 部分需要 "**图片库**"。

## <a name="exploring-the-image-formats"></a>浏览图像格式

下面是再次介绍的 [`Encode`](xref:SkiaSharp.SKBitmap.Encode(SkiaSharp.SKWStream,SkiaSharp.SKEncodedImageFormat,System.Int32)) 方法 `SKImage` ：

```csharp
public Boolean Encode (SKWStream dst, SKEncodedImageFormat format, Int32 quality)
```

[`SKEncodedImageFormat`](xref:SkiaSharp.SKEncodedImageFormat) 是包含11个位图文件格式的成员的枚举，其中的一些是，而不是难懂：

- `Astc`&mdash;自适应可伸缩纹理压缩
- `Bmp`&mdash;Windows 位图
- `Dng`&mdash;Adobe 数字负数
- `Gif`&mdash;图形交换格式
- `Ico`&mdash;Windows 图标图像
- `Jpeg`&mdash;联合图像专家组
- `Ktx`&mdash;OpenGL 的 Khronos 纹理格式
- `Pkm`&mdash;Pokémon 保存文件
- `Png`&mdash;可移植网络图形
- `Wbmp`&mdash;无线应用程序协议位图格式 (每像素1位) 
- `Webp`&mdash;Google WebP 格式

正如您很快就会看到的，SkiaSharp 实际上只支持其中三个文件格式 (`Jpeg` 、 `Png` 和 `Webp`) 。

若要将 `SKBitmap` 名为 `bitmap` 的对象保存到用户的照片库，还需要一个名为的 `SKEncodedImageFormat` 枚举成员 `imageFormat` ，并 () 整数变量的有损格式 `quality` 。 你可以使用以下代码将该位图保存到文件夹中名称为的文件 `filename` 中 `folder` ：

```csharp
using (MemoryStream memStream = new MemoryStream())
using (SKManagedWStream wstream = new SKManagedWStream(memStream))
{
    bitmap.Encode(wstream, imageFormat, quality);
    byte[] data = memStream.ToArray();

    // Check the data array for content!

    bool success = await DependencyService.Get<IPhotoLibrary>().SavePhotoAsync(data, folder, filename);

    // Check return value for success!
}
```

`SKManagedWStream`类派生自 `SKWStream` (，它表示 "可写流" ) 。 `Encode`方法将编码的位图文件写入该流。 此代码中的注释引用可能需要执行的一些错误检查。

[**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)应用程序中的 "**保存文件格式**" 页使用类似的代码，使你可以尝试使用各种格式保存位图。

XAML 文件包含一个 `SKCanvasView` 显示位图的，而页面的其余部分包含应用程序调用的方法所需的所有内容 `Encode` `SKBitmap` 。 它具有一个 `Picker` 用于枚举成员的 `SKEncodedImageFormat` ，一个用于有 `Slider` 损位图格式的质量参数，两个 `Entry` 视图用于表示文件名和文件夹名称，另一个用于 `Button` 保存文件。

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Bitmaps.SaveFileFormatsPage"
             Title="Save Bitmap Formats">

    <StackLayout Margin="10">
        <skiaforms:SKCanvasView PaintSurface="OnCanvasViewPaintSurface"
                                VerticalOptions="FillAndExpand" />

        <Picker x:Name="formatPicker"
                Title="image format"
                SelectedIndexChanged="OnFormatPickerChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKEncodedImageFormat}">
                    <x:Static Member="skia:SKEncodedImageFormat.Astc" />
                    <x:Static Member="skia:SKEncodedImageFormat.Bmp" />
                    <x:Static Member="skia:SKEncodedImageFormat.Dng" />
                    <x:Static Member="skia:SKEncodedImageFormat.Gif" />
                    <x:Static Member="skia:SKEncodedImageFormat.Ico" />
                    <x:Static Member="skia:SKEncodedImageFormat.Jpeg" />
                    <x:Static Member="skia:SKEncodedImageFormat.Ktx" />
                    <x:Static Member="skia:SKEncodedImageFormat.Pkm" />
                    <x:Static Member="skia:SKEncodedImageFormat.Png" />
                    <x:Static Member="skia:SKEncodedImageFormat.Wbmp" />
                    <x:Static Member="skia:SKEncodedImageFormat.Webp" />
                </x:Array>
            </Picker.ItemsSource>
        </Picker>

        <Slider x:Name="qualitySlider"
                Maximum="100"
                Value="50" />

        <Label Text="{Binding Source={x:Reference qualitySlider},
                              Path=Value,
                              StringFormat='Quality = {0:F0}'}"
               HorizontalTextAlignment="Center" />

        <StackLayout Orientation="Horizontal">
            <Label Text="Folder Name: "
                   VerticalOptions="Center" />

            <Entry x:Name="folderNameEntry"
                   Text="SaveFileFormats"
                   HorizontalOptions="FillAndExpand" />
        </StackLayout>

        <StackLayout Orientation="Horizontal">
            <Label Text="File Name: "
                   VerticalOptions="Center" />

            <Entry x:Name="fileNameEntry"
                   Text="Sample.xxx"
                   HorizontalOptions="FillAndExpand" />
        </StackLayout>

        <Button Text="Save"
                Clicked="OnButtonClicked">
            <Button.Triggers>
                <DataTrigger TargetType="Button"
                             Binding="{Binding Source={x:Reference formatPicker},
                                               Path=SelectedIndex}"
                             Value="-1">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>

                <DataTrigger TargetType="Button"
                             Binding="{Binding Source={x:Reference fileNameEntry},
                                               Path=Text.Length}"
                             Value="0">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>
            </Button.Triggers>
        </Button>

        <Label x:Name="statusLabel"
               Text="OK"
               Margin="10, 0" />
    </StackLayout>
</ContentPage>
```

代码隐藏文件加载位图资源，并使用 `SKCanvasView` 来显示位图资源。 该位图永远不会更改。 `SelectedIndexChanged`的处理程序 `Picker` 使用与枚举成员相同的扩展名修改文件名：

```csharp
public partial class SaveFileFormatsPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(typeof(SaveFileFormatsPage),
        "SkiaSharpFormsDemos.Media.MonkeyFace.png");

    public SaveFileFormatsPage ()
    {
        InitializeComponent ();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        args.Surface.Canvas.DrawBitmap(bitmap, args.Info.Rect, BitmapStretch.Uniform);
    }

    void OnFormatPickerChanged(object sender, EventArgs args)
    {
        if (formatPicker.SelectedIndex != -1)
        {
            SKEncodedImageFormat imageFormat = (SKEncodedImageFormat)formatPicker.SelectedItem;
            fileNameEntry.Text = Path.ChangeExtension(fileNameEntry.Text, imageFormat.ToString());
            statusLabel.Text = "OK";
        }
    }

    async void OnButtonClicked(object sender, EventArgs args)
    {
        SKEncodedImageFormat imageFormat = (SKEncodedImageFormat)formatPicker.SelectedItem;
        int quality = (int)qualitySlider.Value;

        using (MemoryStream memStream = new MemoryStream())
        using (SKManagedWStream wstream = new SKManagedWStream(memStream))
        {
            bitmap.Encode(wstream, imageFormat, quality);
            byte[] data = memStream.ToArray();

            if (data == null)
            {
                statusLabel.Text = "Encode returned null";
            }
            else if (data.Length == 0)
            {
                statusLabel.Text = "Encode returned empty array";
            }
            else
            {
                bool success = await DependencyService.Get<IPhotoLibrary>().
                    SavePhotoAsync(data, folderNameEntry.Text, fileNameEntry.Text);

                if (!success)
                {
                    statusLabel.Text = "SavePhotoAsync return false";
                }
                else
                {
                    statusLabel.Text = "Success!";
                }
            }
        }
    }
}
```

`Clicked`的处理程序 `Button` 执行所有实际工作。 它从和获取了两个自变量 `Encode` `Picker` ，然后 `Slider` 使用前面显示的代码为方法创建一个 `SKManagedWStream` `Encode` 。 这两个 `Entry` 视图提供方法的文件夹名称和文件名 `SavePhotoAsync` 。

此方法的大多数用于处理问题或错误。 如果 `Encode` 创建一个空数组，则意味着不支持特定的文件格式。 如果 `SavePhotoAsync` 返回 `false` ，则不能成功保存文件。

下面是运行的程序：

[![保存文件格式](saving-images/SaveFileFormats.png "保存文件格式")](saving-images/SaveFileFormats-Large.png#lightbox)

此屏幕截图显示了这些平台支持的三种格式：

- JPEG
- PNG
- WebP

对于所有其他格式，该 `Encode` 方法不会将任何内容写入流，并且生成的字节数组为空。

**保存文件格式**页保存的位图为600像素的正方形。 对于每个像素4个字节，这是内存中的总1440000字节。 下表显示了文件格式和质量的各种组合的文件大小：

|格式|质量|大小|
|------|------:|---:|
| PNG | 空值 | 492K |
| JPEG | 0 | 2.95 k |
|      | 50 | 22.1 k |
|      | 100 | 206K |
| WebP | 0 | 2.71 k |
|      | 50 | 11.9 k |
|      | 100 | 101K |

可以尝试各种质量设置并检查结果。

## <a name="saving-finger-paint-art"></a>保存 finger-画图

位图的一个常见用途是在绘图程序中，它的作用就像是 _阴影位图_。 所有绘图都将保留在位图上，然后由程序显示。 位图还能为保存绘图提供便利。

[**SkiaSharp 文章中的 Finger 绘图**](../paths/finger-paint.md)演示了如何使用触摸跟踪来实现基元 Finger 绘制程序。 程序只支持一种颜色和一个笔划宽度，但会将整个绘图保留在对象的集合中 `SKPath` 。

在[**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)示例中，**使用 Save 页的 Finger 画图**还会将整个绘图保留在对象的集合中 `SKPath` ，但它还会在位图上呈现绘图，并可将其保存到照片库。

此程序的大部分内容与原始 **Finger 画图** 程序类似。 其中一项改进是 XAML 文件现在实例化标记为 " **清除** 并 **保存**" 的按钮：

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             xmlns:tt="clr-namespace:TouchTracking"
             x:Class="SkiaSharpFormsDemos.Bitmaps.FingerPaintSavePage"
             Title="Finger Paint Save">

    <StackLayout>
        <Grid BackgroundColor="White"
              VerticalOptions="FillAndExpand">
            <skia:SKCanvasView x:Name="canvasView"
                               PaintSurface="OnCanvasViewPaintSurface" />
            <Grid.Effects>
                <tt:TouchEffect Capture="True"
                                TouchAction="OnTouchEffectAction" />
            </Grid.Effects>
        </Grid>

        <Grid>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="*" />
            </Grid.ColumnDefinitions>
        </Grid>

        <Button Text="Clear"
                Grid.Row="0"
                Margin="50, 5"
                Clicked="OnClearButtonClicked" />

        <Button Text="Save"
                Grid.Row="1"
                Margin="50, 5"
                Clicked="OnSaveButtonClicked" />

    </StackLayout>
</ContentPage>
```

代码隐藏文件维护名为的类型的字段 `SKBitmap` `saveBitmap` 。 只要显示图面的大小改变，就会在处理程序中创建或重新创建此位图 `PaintSurface` 。 如果需要重新创建位图，则会将现有位图的内容复制到新位图，这样无论显示图面的大小如何变化，都将保留所有内容：

```csharp
public partial class FingerPaintSavePage : ContentPage
{
    ···
    SKBitmap saveBitmap;

    public FingerPaintSavePage ()
    {
        InitializeComponent ();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        // Create bitmap the size of the display surface
        if (saveBitmap == null)
        {
            saveBitmap = new SKBitmap(info.Width, info.Height);
        }
        // Or create new bitmap for a new size of display surface
        else if (saveBitmap.Width < info.Width || saveBitmap.Height < info.Height)
        {
            SKBitmap newBitmap = new SKBitmap(Math.Max(saveBitmap.Width, info.Width),
                                              Math.Max(saveBitmap.Height, info.Height));

            using (SKCanvas newCanvas = new SKCanvas(newBitmap))
            {
                newCanvas.Clear();
                newCanvas.DrawBitmap(saveBitmap, 0, 0);
            }

            saveBitmap = newBitmap;
        }

        // Render the bitmap
        canvas.Clear();
        canvas.DrawBitmap(saveBitmap, 0, 0);
    }
    ···
}
```

处理程序完成的绘图 `PaintSurface` 在最末尾进行，并且只包含一个呈现位图。

触摸处理与前面的程序类似。 程序维护两个集合： `inProgressPaths` 和 `completedPaths` ，其中包含自上次清除显示后用户绘制的所有内容。 对于每个触控事件， `OnTouchEffectAction` 处理程序将调用 `UpdateBitmap` ：

```csharp
public partial class FingerPaintSavePage : ContentPage
{
    Dictionary<long, SKPath> inProgressPaths = new Dictionary<long, SKPath>();
    List<SKPath> completedPaths = new List<SKPath>();

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = 10,
        StrokeCap = SKStrokeCap.Round,
        StrokeJoin = SKStrokeJoin.Round
    };
    ···
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        switch (args.Type)
        {
            case TouchActionType.Pressed:
                if (!inProgressPaths.ContainsKey(args.Id))
                {
                    SKPath path = new SKPath();
                    path.MoveTo(ConvertToPixel(args.Location));
                    inProgressPaths.Add(args.Id, path);
                    UpdateBitmap();
                }
                break;

            case TouchActionType.Moved:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    SKPath path = inProgressPaths[args.Id];
                    path.LineTo(ConvertToPixel(args.Location));
                    UpdateBitmap();
                }
                break;

            case TouchActionType.Released:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    completedPaths.Add(inProgressPaths[args.Id]);
                    inProgressPaths.Remove(args.Id);
                    UpdateBitmap();
                }
                break;

            case TouchActionType.Cancelled:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    inProgressPaths.Remove(args.Id);
                    UpdateBitmap();
                }
                break;
        }
    }

    SKPoint ConvertToPixel(Point pt)
    {
        return new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                            (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));
    }

    void UpdateBitmap()
    {
        using (SKCanvas saveBitmapCanvas = new SKCanvas(saveBitmap))
        {
            saveBitmapCanvas.Clear();

            foreach (SKPath path in completedPaths)
            {
                saveBitmapCanvas.DrawPath(path, paint);
            }

            foreach (SKPath path in inProgressPaths.Values)
            {
                saveBitmapCanvas.DrawPath(path, paint);
            }
        }

        canvasView.InvalidateSurface();
    }
    ···
}
```

此 `UpdateBitmap` 方法 `saveBitmap` 通过创建一个新的 `SKCanvas` ，清除它，然后呈现位图上的所有路径来重绘。 它以失效的结束， `canvasView` 以便可以在显示时绘制位图。

下面是两个按钮的处理程序。 " **清除** " 按钮清除两个路径集合，更新 `saveBitmap` (导致清除位图) ，并使 `SKCanvasView` ：

```csharp
public partial class FingerPaintSavePage : ContentPage
{
    ···
    void OnClearButtonClicked(object sender, EventArgs args)
    {
        completedPaths.Clear();
        inProgressPaths.Clear();
        UpdateBitmap();
        canvasView.InvalidateSurface();
    }

    async void OnSaveButtonClicked(object sender, EventArgs args)
    {
        using (SKImage image = SKImage.FromBitmap(saveBitmap))
        {
            SKData data = image.Encode();
            DateTime dt = DateTime.Now;
            string filename = String.Format("FingerPaint-{0:D4}{1:D2}{2:D2}-{3:D2}{4:D2}{5:D2}{6:D3}.png",
                                            dt.Year, dt.Month, dt.Day, dt.Hour, dt.Minute, dt.Second, dt.Millisecond);

            IPhotoLibrary photoLibrary = DependencyService.Get<IPhotoLibrary>();
            bool result = await photoLibrary.SavePhotoAsync(data.ToArray(), "FingerPaint", filename);

            if (!result)
            {
                await DisplayAlert("FingerPaint", "Artwork could not be saved. Sorry!", "OK");
            }
        }
    }
}
```

**保存**按钮处理程序使用中的简化 [`Encode`](xref:SkiaSharp.SKImage.Encode) 方法 `SKImage` 。 此方法使用 PNG 格式进行编码。 `SKImage`对象是基于创建的 `saveBitmap` ， `SKData` 对象包含编码的 PNG 文件。

的 `ToArray` 方法 `SKData` 获取字节数组。 这是传递给方法的内容 `SavePhotoAsync` 、固定文件夹名称以及从当前日期和时间构造的唯一文件名。

下面是正在执行操作的程序：

[![Finger 画图保存](saving-images/FingerPaintSave.png "Finger 画图保存")](saving-images/FingerPaintSave-Large.png#lightbox)

在 [**SpinPaint**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-spinpaint) 示例中使用非常类似的技术。 这也是一个手指刷程序，只不过用户在旋转后的磁盘上进行绘制，然后在其他四个象限上重现设计。 当磁盘旋转时，手指画图的颜色会变化：

[![旋转画图](saving-images/SpinPaint.png "旋转画图")](saving-images/SpinPaint-Large.png#lightbox)

类的 " **保存** " 按钮 `SpinPaint` 类似于 " **手指画图** "，因为它将图像保存到固定文件夹名称 (**SpainPaint**) 和从日期和时间构造的文件名。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [SpinPaint (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-spinpaint)