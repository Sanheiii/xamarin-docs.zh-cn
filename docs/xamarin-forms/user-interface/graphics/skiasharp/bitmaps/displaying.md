---
title: 显示 SkiaSharp 位图
description: 了解如何以像素大小显示 SkiaSharp 位图，并在保持纵横比的同时扩展以填充矩形。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 8E074F8D-4715-4146-8CC0-FD7A8290EDE9
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1427b6f8461c74ded933fe562a7d17221790383a
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562309"
---
# <a name="displaying-skiasharp-bitmaps"></a>显示 SkiaSharp 位图

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

SkiaSharp 位图的主题在 SkiaSharp 中的 **[位图基础知识](../basics/bitmaps.md)** 一文中引入。 本文介绍了三种加载位图和三种显示位图的方式。 本文介绍加载位图并更深入地使用方法的方法 `DrawBitmap` `SKCanvas` 。

![显示示例](displaying-images/DisplayingSample.png "显示示例")

`DrawBitmapLattice` `DrawBitmapNinePatch` **[SkiaSharp 位图的分段显示](segmented.md)** 文章中讨论了和方法。

本页中的示例来自 **[SkiaSharpFormsDemos](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** 应用程序。 在该应用程序的主页中，选择 " **SkiaSharp 位图**"，然后切换到 " **显示位图** " 部分。

## <a name="loading-a-bitmap"></a>加载位图

SkiaSharp 应用程序使用的位图通常来自三个不同源之一：

- 通过 Internet
- 从可执行文件中嵌入的资源
- 用户照片库中的

SkiaSharp 应用程序还可以创建新的位图，然后在其上进行绘制或设置位图位算法。 这些技术在 **[SkiaSharp 位图上的 "创建和绘制](drawing.md)** " 和 " **[访问 SkiaSharp 位图像素](pixel-bits.md)**" 文章中进行了介绍。

在以下三个加载位图的代码示例中，假定类包含类型为的字段 `SKBitmap` ：

```csharp
SKBitmap bitmap;
```

如 SkiaSharp 中所述的 **[位图基础知识中](../basics/bitmaps.md)** 所述，通过 Internet 加载位图的最佳方式是使用 [`HttpClient`](xref:System.Net.Http.HttpClient) 类。 类的单个实例可以定义为字段：

```csharp
HttpClient httpClient = new HttpClient();
```

使用 `HttpClient` iOS 和 Android 应用程序时，需要按照 **[传输层安全性 (TLS) 1.2](~/cross-platform/app-fundamentals/transport-layer-security.md)** 上的文档中所述设置项目属性。

使用的代码 `HttpClient` 通常涉及 `await` 运算符，因此它必须位于 `async` 方法中：

```csharp
try
{
    using (Stream stream = await httpClient.GetStreamAsync("https:// ··· "))
    using (MemoryStream memStream = new MemoryStream())
    {
        await stream.CopyToAsync(memStream);
        memStream.Seek(0, SeekOrigin.Begin);

        bitmap = SKBitmap.Decode(stream);
        ···
    };
}
catch
{
    ···
}
```

请注意， `Stream` 从获取的对象 `GetStreamAsync` 将被复制到中 `MemoryStream` 。 Android 不允许 `Stream` `HttpClient` 主线程处理 from，但异步方法除外。 

[`SKBitmap.Decode`](xref:SkiaSharp.SKBitmap.Decode(System.IO.Stream))执行大量工作： `Stream` 传递给它的对象引用一个内存块，其中包含一个通用位图文件格式（通常为 JPEG、PNG 或 GIF）中的整个位图。 `Decode`方法必须确定格式，然后将位图文件解码为 SkiaSharp 的内部位图格式。

代码调用后 `SKBitmap.Decode` ，它可能会使 `CanvasView` `PaintSurface` 处理程序能够显示新加载的位图。

加载位图的第二种方法是将位图作为嵌入资源包含在单个平台项目所引用的 .NET Standard 库中。 资源 ID 被传递给 [`GetManifestResourceStream`](xref:System.Reflection.Assembly.GetManifestResourceStream(System.String)) 方法。 此资源 ID 包括程序集名称、文件夹名称和以句点分隔的资源文件名：

```csharp
string resourceID = "assemblyName.folderName.fileName";
Assembly assembly = GetType().GetTypeInfo().Assembly;

using (Stream stream = assembly.GetManifestResourceStream(resourceID))
{
    bitmap = SKBitmap.Decode(stream);
    ···
}
```

位图文件也可以存储为 iOS、Android 和通用 Windows 平台 (UWP) 的单个平台项目中的资源。 但是，加载这些位图需要位于平台项目中的代码。

获取位图的第三种方法是从用户的图片库中获取。 以下代码使用 **[SkiaSharpFormsDemos](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** 应用程序中包含的依赖项服务。 **SkiaSharpFormsDemo** .NET Standard 库包含 `IPhotoLibrary` 接口，而每个平台项目都包含一个 `PhotoLibrary` 实现该接口的类。

```csharp
IPhotoicturePicker picturePicker = DependencyService.Get<IPhotoLibrary>();

using (Stream stream = await picturePicker.GetImageStreamAsync())
{
    if (stream != null)
    {
        bitmap = SKBitmap.Decode(stream);
        ···
    }
}
```

通常，此类代码还 `CanvasView` `PaintSurface` 会使处理程序能够显示新位图，从而使处理程序能够显示新位图。

`SKBitmap`类定义了多个有用的属性，包括 [`Width`](xref:SkiaSharp.SKBitmap.Width) 和 [`Height`](xref:SkiaSharp.SKBitmap.Height) ，它们显示位图的像素尺寸以及多种方法，包括用于创建位图、复制和公开像素位的方法。 

## <a name="displaying-in-pixel-dimensions"></a>以像素尺寸显示

SkiaSharp [`Canvas`](xref:SkiaSharp.SKCanvas) 类定义四个 `DrawBitmap` 方法。 这些方法允许以两种根本不同的方式显示位图： 

- 指定 `SKPoint` 值 (或单独的 `x` 和 `y` 值) 在其像素尺寸中显示位图。 位图的像素直接映射到视频显示的像素。
- 指定矩形会导致位图拉伸到矩形的大小和形状。 

使用 [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKPoint,SkiaSharp.SKPaint)) 带 `SKPoint` 参数的或 [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,System.Single,System.Single,SkiaSharp.SKPaint)) 带单独 `x` 参数的和 `y` 参数在其像素维度中显示位图：

```csharp
DrawBitmap(SKBitmap bitmap, SKPoint pt, SKPaint paint = null)

DrawBitmap(SKBitmap bitmap, float x, float y, SKPaint paint = null)
```

这两种方法在功能上相同。 指定的点指示位图的左上角相对于画布的位置。 由于移动设备的像素分辨率很高，因此在这些设备上，较小的位图通常会显得很小。

可选 `SKPaint` 参数允许使用透明度显示位图。 为此，请创建一个 `SKPaint` 对象，并将该 `Color` 属性设置为 `SKColor` alpha 通道小于1的任何值。 例如：

```csharp
paint.Color = new SKColor(0, 0, 0, 0x80);
```

作为最后一个参数传递的0x80 指示50% 的透明度。 您还可以在其中一个预定义的颜色上设置 alpha 通道：

```csharp
paint.Color = SKColors.Red.WithAlpha(0x80);
```

但颜色本身是不相关的。 在调用中使用对象时，仅检查 alpha 通道 `SKPaint` `DrawBitmap` 。

`SKPaint`当使用 blend 模式或筛选器效果显示位图时，该对象也会扮演角色。 这些文章在 [SkiaSharp 合成和 blend 模式](../effects/blend-modes/index.md) 和 [SkiaSharp 映像筛选器](../effects/image-filters.md)中进行了演示。

**[SkiaSharpFormsDemos](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** 示例程序中的 "**像素维度**" 页显示的位图资源宽度为320像素，高度为240像素：

```csharp
public class PixelDimensionsPage : ContentPage
{
    SKBitmap bitmap;

    public PixelDimensionsPage()
    {
        Title = "Pixel Dimensions";

        // Load the bitmap from a resource
        string resourceID = "SkiaSharpFormsDemos.Media.Banana.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }

        // Create the SKCanvasView and set the PaintSurface handler
        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        float x = (info.Width - bitmap.Width) / 2;
        float y = (info.Height - bitmap.Height) / 2;

        canvas.DrawBitmap(bitmap, x, y);
    }
}
```

`PaintSurface`处理程序通过 `x` `y` 基于显示图面的像素尺寸和位图的像素尺寸计算和值来使位图居中：

[![像素尺寸](displaying-images/PixelDimensions.png "像素尺寸")](displaying-images/PixelDimensions-Large.png#lightbox)

如果应用程序希望在其左上角显示位图，只需传递 (0，0) 的坐标。 

## <a name="a-method-for-loading-resource-bitmaps"></a>用于加载资源位图的方法

其中的许多示例将需要加载位图资源。 `BitmapExtensions` **[SkiaSharpFormsDemos](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** 解决方案中的静态类包含方法来帮助你：

```csharp
static class BitmapExtensions
{
    public static SKBitmap LoadBitmapResource(Type type, string resourceID)
    {
        Assembly assembly = type.GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            return SKBitmap.Decode(stream);
        }
    }
    ···
}
```

请注意 `Type` 参数。 这可以是 `Type` 与存储位图资源的程序集中的任何类型相关联的对象。

此 `LoadBitmapResource` 方法将用于需要位图资源的所有后续示例中。

## <a name="stretching-to-fill-a-rectangle"></a>拉伸以填充矩形

`SKCanvas`该类还定义了一个 [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKPaint)) 方法，该方法将位图呈现为一个矩形，并定义另一个 [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKRect,SkiaSharp.SKPaint)) 方法，该方法将位图的矩形子集呈现为一个矩形：

```
DrawBitmap(SKBitmap bitmap, SKRect dest, SKPaint paint = null)

DrawBitmap(SKBitmap bitmap, SKRect source, SKRect dest, SKPaint paint = null)
```

在这两种情况下，都会拉伸位图以填充名为的矩形 `dest` 。 在第二个方法中， `source` 矩形允许您选择位图的子集。 该 `dest` 矩形是相对于输出设备的; `source` 矩形相对于位图。

" **填充矩形** " 页通过在与画布相同大小的矩形中显示与前面示例中所用相同的位图来演示这两种方法中的第一个。 

```csharp
public class FillRectanglePage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(FillRectanglePage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");
    public FillRectanglePage ()
    {
        Title = "Fill Rectangle";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        canvas.DrawBitmap(bitmap, info.Rect);
    }
}
```

请注意，使用新 `BitmapExtensions.LoadBitmapResource` 方法设置 `SKBitmap` 字段。 目标矩形是从的属性获取的 [`Rect`](xref:SkiaSharp.SKImageInfo.Rect) `SKImageInfo` ，它介绍显示图面的大小：

[![填充矩形](displaying-images/FillRectangle.png "填充矩形")](displaying-images/FillRectangle-Large.png#lightbox)

这通常 _不_ 是你想要的。 在水平和垂直方向上以不同的方式拉伸图像会扭曲。 当在非像素大小以外的其他内容中显示位图时，通常需要保留位图的原始纵横比。

## <a name="stretching-while-preserving-the-aspect-ratio"></a>在保持纵横比的同时拉伸

在保持纵横比的同时拉伸位图也称为 _统一缩放_。 这一术语建议使用算法方法。 **统一缩放**页面中显示了一个可能的解决方案：

```csharp
public class UniformScalingPage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(UniformScalingPage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");
    public UniformScalingPage()
    {
        Title = "Uniform Scaling";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        float scale = Math.Min((float)info.Width / bitmap.Width, 
                               (float)info.Height / bitmap.Height);
        float x = (info.Width - scale * bitmap.Width) / 2;
        float y = (info.Height - scale * bitmap.Height) / 2;
        SKRect destRect = new SKRect(x, y, x + scale * bitmap.Width, 
                                           y + scale * bitmap.Height);

        canvas.DrawBitmap(bitmap, destRect);
    }
}
```

`PaintSurface`处理程序计算一个 `scale` 系数，这是显示宽度和高度与位图宽度和高度之比的最小值。 `x`然后， `y` 可以计算和值，以便在显示宽度和高度范围内居中缩放位图。 目标矩形的左上角和 `x` `y` 这些值的右下角加上位图的缩放宽度和高度：

[![统一缩放](displaying-images/UniformScaling.png "统一缩放")](displaying-images/UniformScaling-Large.png#lightbox)

将手机侧向旋转，以查看延伸到该区域的位图：

[![均匀缩放横向](displaying-images/UniformScaling-Landscape.png "均匀缩放横向")](displaying-images/UniformScaling-Landscape-Large.png#lightbox)

`scale`如果要实现略微不同的算法，使用此因素的优点会很明显。 假设您要保留位图的纵横比，同时填充目标矩形。 这种方法的唯一方法是通过裁剪部分图像来实现，但只需 `Math.Min` `Math.Max` 在上述代码中将更改为即可实现该算法。 结果如下： 

[![统一缩放替代方法](displaying-images/UniformScaling-Alternative.png "统一缩放替代方法")](displaying-images/UniformScaling-Alternative-Large.png#lightbox)

位图的纵横比会保留，但会裁剪位图左侧和右侧的区域。

## <a name="a-versatile-bitmap-display-function"></a>用途位图显示功能

基于 XAML 的编程环境 (例如 UWP 和 Xamarin.Forms) 具有一项功能，可用于扩展或缩小位图大小，同时保留其纵横比。 虽然 SkiaSharp 不包含此功能，但您也可以自行实现。 `BitmapExtensions` [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)应用程序中包含的类演示了如何操作。 类定义了两个 `DrawBitmap` 执行纵横比计算的新方法。 这些新方法是的扩展方法 `SKCanvas` 。

新 `DrawBitmap` 方法包含类型的参数 `BitmapStretch` ，该参数是在 **BitmapExtensions.cs** 文件中定义的枚举：

```csharp
public enum BitmapStretch
{
    None,
    Fill,
    Uniform,
    UniformToFill,
    AspectFit = Uniform,
    AspectFill = UniformToFill
}
```

`None`、、 `Fill` `Uniform` 和成员与 `UniformToFill` UWP 枚举中的成员相同 [`Stretch`](/uwp/api/Windows.UI.Xaml.Media.Stretch) 。 类似的 Xamarin.Forms [`Aspect`](xref:Xamarin.Forms.Aspect) 枚举定义成员 `Fill` 、 `AspectFit` 和 `AspectFill` 。

上面所示的 " **统一缩放** " 页将位图嵌入到矩形中，但你可能需要其他选项，例如将位图放置在矩形的左侧或右侧，或者顶部或底部。 这就是枚举的目的 `BitmapAlignment` ：

```csharp
public enum BitmapAlignment
{
    Start,
    Center,
    End
}
```

当与一起使用时，对齐设置不起作用 `BitmapStretch.Fill` 。

第一个 `DrawBitmap` 扩展函数包含一个目标矩形，但没有源矩形。 定义了默认值，因此，如果想要将位图居中，只需指定一个 `BitmapStretch` 成员：

```csharp
static class BitmapExtensions
{
    ···
    public static void DrawBitmap(this SKCanvas canvas, SKBitmap bitmap, SKRect dest, 
                                  BitmapStretch stretch, 
                                  BitmapAlignment horizontal = BitmapAlignment.Center, 
                                  BitmapAlignment vertical = BitmapAlignment.Center, 
                                  SKPaint paint = null)
    {
        if (stretch == BitmapStretch.Fill)
        {
            canvas.DrawBitmap(bitmap, dest, paint);
        }
        else
        {
            float scale = 1;

            switch (stretch)
            {
                case BitmapStretch.None:
                    break;

                case BitmapStretch.Uniform:
                    scale = Math.Min(dest.Width / bitmap.Width, dest.Height / bitmap.Height);
                    break;

                case BitmapStretch.UniformToFill:
                    scale = Math.Max(dest.Width / bitmap.Width, dest.Height / bitmap.Height);
                    break;
            }

            SKRect display = CalculateDisplayRect(dest, scale * bitmap.Width, scale * bitmap.Height, 
                                                  horizontal, vertical);

            canvas.DrawBitmap(bitmap, display, paint);
        }
    }
    ···
}
```

此方法的主要目的是计算名为的缩放系数 `scale` ，然后在调用方法时将其应用到位图宽度和高度 `CalculateDisplayRect` 。 这是一种方法，该方法根据水平和垂直对齐方式计算用于显示位图的矩形：

```csharp
static class BitmapExtensions
{
    ···
    static SKRect CalculateDisplayRect(SKRect dest, float bmpWidth, float bmpHeight, 
                                       BitmapAlignment horizontal, BitmapAlignment vertical)
    {
        float x = 0;
        float y = 0;

        switch (horizontal)
        {
            case BitmapAlignment.Center:
                x = (dest.Width - bmpWidth) / 2;
                break;

            case BitmapAlignment.Start:
                break;

            case BitmapAlignment.End:
                x = dest.Width - bmpWidth;
                break;
        }

        switch (vertical)
        {
            case BitmapAlignment.Center:
                y = (dest.Height - bmpHeight) / 2;
                break;

            case BitmapAlignment.Start:
                break;

            case BitmapAlignment.End:
                y = dest.Height - bmpHeight;
                break;
        }

        x += dest.Left;
        y += dest.Top;

        return new SKRect(x, y, x + bmpWidth, y + bmpHeight);
    }
}
```

`BitmapExtensions`类包含其他 `DrawBitmap` 方法，该方法具有用于指定位图子集的源矩形。 此方法类似于第一个方法，只不过缩放因子是根据矩形计算的 `source` ，然后应用到对的 `source` 调用中的矩形 `CalculateDisplayRect` ：

```csharp
static class BitmapExtensions
{
    ···
    public static void DrawBitmap(this SKCanvas canvas, SKBitmap bitmap, SKRect source, SKRect dest,
                                  BitmapStretch stretch,
                                  BitmapAlignment horizontal = BitmapAlignment.Center,
                                  BitmapAlignment vertical = BitmapAlignment.Center,
                                  SKPaint paint = null)
    {
        if (stretch == BitmapStretch.Fill)
        {
            canvas.DrawBitmap(bitmap, source, dest, paint);
        }
        else
        {
            float scale = 1;

            switch (stretch)
            {
                case BitmapStretch.None:
                    break;

                case BitmapStretch.Uniform:
                    scale = Math.Min(dest.Width / source.Width, dest.Height / source.Height);
                    break;

                case BitmapStretch.UniformToFill:
                    scale = Math.Max(dest.Width / source.Width, dest.Height / source.Height);
                    break;
            }

            SKRect display = CalculateDisplayRect(dest, scale * source.Width, scale * source.Height, 
                                                  horizontal, vertical);

            canvas.DrawBitmap(bitmap, source, display, paint);
        }
    }
    ···
}
```

这两种新方法中的第一种 `DrawBitmap` 方法在 **缩放模式** 页中进行演示。 XAML 文件包含三个 `Picker` 元素，这些元素使您可以选择 `BitmapStretch` 和 `BitmapAlignment` 枚举的成员：

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:SkiaSharpFormsDemos"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Bitmaps.ScalingModesPage"
             Title="Scaling Modes">

    <Grid Padding="10">
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>

        <skia:SKCanvasView x:Name="canvasView" 
                           Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Label Text="Stretch:"
               Grid.Row="1" Grid.Column="0"
               VerticalOptions="Center" />

        <Picker x:Name="stretchPicker"
                Grid.Row="1" Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type local:BitmapStretch}">
                    <x:Static Member="local:BitmapStretch.None" />
                    <x:Static Member="local:BitmapStretch.Fill" />
                    <x:Static Member="local:BitmapStretch.Uniform" />
                    <x:Static Member="local:BitmapStretch.UniformToFill" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Label Text="Horizontal Alignment:"
               Grid.Row="2" Grid.Column="0"
               VerticalOptions="Center" />

        <Picker x:Name="horizontalPicker" 
                Grid.Row="2" Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type local:BitmapAlignment}">
                    <x:Static Member="local:BitmapAlignment.Start" />
                    <x:Static Member="local:BitmapAlignment.Center" />
                    <x:Static Member="local:BitmapAlignment.End" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Label Text="Vertical Alignment:"
               Grid.Row="3" Grid.Column="0"
               VerticalOptions="Center" />

        <Picker x:Name="verticalPicker" 
                Grid.Row="3" Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type local:BitmapAlignment}">
                    <x:Static Member="local:BitmapAlignment.Start" />
                    <x:Static Member="local:BitmapAlignment.Center" />
                    <x:Static Member="local:BitmapAlignment.End" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>
    </Grid>
</ContentPage>
```

`CanvasView`当任何项发生更改时，代码隐藏文件就会 `Picker` 无效。 `PaintSurface`处理程序访问 `Picker` 用于调用扩展方法的三个视图 `DrawBitmap` ：

```csharp
public partial class ScalingModesPage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(ScalingModesPage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");
    public ScalingModesPage()
    {
        InitializeComponent();
    }

    private void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKRect dest = new SKRect(0, 0, info.Width, info.Height);

        BitmapStretch stretch = (BitmapStretch)stretchPicker.SelectedItem;
        BitmapAlignment horizontal = (BitmapAlignment)horizontalPicker.SelectedItem;
        BitmapAlignment vertical = (BitmapAlignment)verticalPicker.SelectedItem;

        canvas.DrawBitmap(bitmap, dest, stretch, horizontal, vertical);
    }
}
```

下面是一些选项组合：

[![缩放模式](displaying-images/ScalingModes.png "缩放模式")](displaying-images/ScalingModes-Large.png#lightbox)

**矩形子集**页与**缩放模式**几乎具有相同的 XAML 文件，但代码隐藏文件定义了由字段给定的位图的矩形子集 `SOURCE` ： 

```csharp
public partial class ScalingModesPage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(ScalingModesPage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");

    static readonly SKRect SOURCE = new SKRect(94, 12, 212, 118);

    public RectangleSubsetPage()
    {
        InitializeComponent();
    }

    private void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKRect dest = new SKRect(0, 0, info.Width, info.Height);

        BitmapStretch stretch = (BitmapStretch)stretchPicker.SelectedItem;
        BitmapAlignment horizontal = (BitmapAlignment)horizontalPicker.SelectedItem;
        BitmapAlignment vertical = (BitmapAlignment)verticalPicker.SelectedItem;

        canvas.DrawBitmap(bitmap, SOURCE, dest, stretch, horizontal, vertical);
    }
}
```

此矩形源隔离了猴子的标头，如以下屏幕截图所示：

[![矩形子集](displaying-images/RectangleSubset.png "矩形子集")](displaying-images/RectangleSubset-Large.png#lightbox)

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)