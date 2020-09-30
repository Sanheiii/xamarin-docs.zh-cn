---
title: SkiaSharp 中的位图基础知识
description: 本文介绍如何从各种源加载 SkiaSharp 中的位图并在应用程序中显示它们 Xamarin.Forms ，并通过示例代码对此进行演示。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 32C95DFF-9065-42D7-966C-D3DBD16906B3
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a62d8f8e04d97b45fff779739d817defce1414f2
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556862"
---
# <a name="bitmap-basics-in-skiasharp"></a>SkiaSharp 中的位图基础知识

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_从各种源加载位图并显示它们。_

支持 SkiaSharp 中的位图。 本文仅介绍 &mdash; 如何加载位图以及如何显示它们：

![两个位图的显示](bitmaps-images/basicbitmaps-small.png)

可以在 [SkiaSharp 位图](../bitmaps/index.md)部分找到更深入的位图浏览。

SkiaSharp 位图是类型的对象 [`SKBitmap`](xref:SkiaSharp.SKBitmap) 。 有多种方法可以创建位图，但本文会将自身限制为 [`SKBitmap.Decode`](xref:SkiaSharp.SKBitmap.Decode(System.IO.Stream)) 从 .net 对象加载位图的方法 `Stream` 。

**SkiaSharpFormsDemos**程序中的**基本位图**页演示了如何从三个不同的源加载位图：

- 通过 Internet
- 从可执行文件中嵌入的资源
- 用户照片库中的

`SKBitmap`这三个源的三个对象定义为类中的字段 [`BasicBitmapsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/BasicBitmapsPage.cs) ：

```csharp
public class BasicBitmapsPage : ContentPage
{
    SKCanvasView canvasView;
    SKBitmap webBitmap;
    SKBitmap resourceBitmap;
    SKBitmap libraryBitmap;

    public BasicBitmapsPage()
    {
        Title = "Basic Bitmaps";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
        ...
    }
    ...
}
```

## <a name="loading-a-bitmap-from-the-web"></a>从 Web 加载位图

若要基于 URL 加载位图，可以使用 [`HttpClient`](/dotnet/api/system.net.http.httpclient?view=netstandard-2.0) 类。 只应实例化一个实例， `HttpClient` 并重复使用它，以便将其存储为字段：

```csharp
HttpClient httpClient = new HttpClient();
```

使用 `HttpClient` iOS 和 Android 应用程序时，需要按照 **[传输层安全性 (TLS) 1.2](~/cross-platform/app-fundamentals/transport-layer-security.md)** 上的文档中所述设置项目属性。

由于将运算符与结合使用最为方便 `await` ，因此 `HttpClient` 不能在构造函数中执行代码 `BasicBitmapsPage` 。 相反，它是替代的一部分 `OnAppearing` 。 此处的 URL 指向 Xamarin 网站上的某个区域，其中包含一些示例位图。 网站上的包允许追加用于将位图调整为特定宽度的规范：

```csharp
protected override async void OnAppearing()
{
    base.OnAppearing();

    // Load web bitmap.
    string url = "https://developer.xamarin.com/demo/IMG_3256.JPG?width=480";

    try
    {
        using (Stream stream = await httpClient.GetStreamAsync(url))
        using (MemoryStream memStream = new MemoryStream())
        {
            await stream.CopyToAsync(memStream);
            memStream.Seek(0, SeekOrigin.Begin);

            webBitmap = SKBitmap.Decode(memStream);
            canvasView.InvalidateSurface();
        };
    }
    catch
    {
    }
}
```

当使用从方法中返回的时，Android 操作系统会引发异常， `Stream` `GetStreamAsync` `SKBitmap.Decode` 因为它在主线程上执行较长的操作。 出于此原因，将使用将位图文件的内容复制到 `MemoryStream` 对象 `CopyToAsync` 。

静态 `SKBitmap.Decode` 方法负责对位图文件进行解码。 它使用 JPEG、PNG 和 GIF 位图格式，并将结果存储为内部 SkiaSharp 格式。 此时， `SKCanvasView` 需要使 `PaintSurface` 处理程序能够更新显示的状态是无效的。

## <a name="loading-a-bitmap-resource"></a>加载位图资源

就代码而言，加载位图的最简单方法是直接在应用程序中包括位图资源。 **SkiaSharpFormsDemos**程序包含一个名为**Media**的文件夹，其中包含多个位图文件，其中包含一个名为**monkey.png**。 对于存储为程序资源的位图，必须使用 "**属性**" 对话框为该文件提供**嵌入资源**的**生成操作**！

每个嵌入资源都有一个 *资源 ID* ，其中包含项目名称、文件夹和文件名，所有这些都按句点进行连接： **SkiaSharpFormsDemos.Media.monkey.png**。 可以通过指定资源 ID 作为类的方法的参数来获取对此资源的访问权限 [`GetManifestResourceStream`](xref:System.Reflection.Assembly.GetManifestResourceStream(System.String)) [`Assembly`](xref:System.Reflection.Assembly) ：

```csharp
string resourceID = "SkiaSharpFormsDemos.Media.monkey.png";
Assembly assembly = GetType().GetTypeInfo().Assembly;

using (Stream stream = assembly.GetManifestResourceStream(resourceID))
{
    resourceBitmap = SKBitmap.Decode(stream);
}
```

`Stream`可以将此对象直接传递给 `SKBitmap.Decode` 方法。

## <a name="loading-a-bitmap-from-the-photo-library"></a>从照片库加载位图

用户还可以从设备的图片库中加载照片。 此功能本身不提供此功能 Xamarin.Forms 。 该作业需要一个依赖项服务，例如 [从图片库中选取照片](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)一文中所述的服务。

**SkiaSharpFormsDemos**项目中的**IPhotoLibrary.cs**文件和平台项目中的三个**PhotoLibrary.cs**文件已从该文章中改编。 此外，Android **MainActivity.cs** 文件已按照本文中的说明进行了修改，并且已向 iOS 项目授予访问照片库的权限，并在 **info.plist** 文件的底部有两行。

`BasicBitmapsPage`构造函数将添加 `TapGestureRecognizer` 到 `SKCanvasView` 以在点击时获得通知。 收到点击后， `Tapped` 处理程序将获取对图片选取器依赖项服务的访问权限，并调用 `PickPhotoAsync` 。 如果 `Stream` 返回对象，则会将该对象传递给 `SKBitmap.Decode` 方法：

```csharp
// Add tap gesture recognizer
TapGestureRecognizer tapRecognizer = new TapGestureRecognizer();
tapRecognizer.Tapped += async (sender, args) =>
{
    // Load bitmap from photo library
    IPhotoLibrary photoLibrary = DependencyService.Get<IPhotoLibrary>();

    using (Stream stream = await photoLibrary.PickPhotoAsync())
    {
        if (stream != null)
        {
            libraryBitmap = SKBitmap.Decode(stream);
            canvasView.InvalidateSurface();
        }
    }
};
canvasView.GestureRecognizers.Add(tapRecognizer);
```

请注意， `Tapped` 处理程序还将调用 `InvalidateSurface` 对象的方法 `SKCanvasView` 。 这会生成对处理程序的新调用 `PaintSurface` 。

## <a name="displaying-the-bitmaps"></a>显示位图

`PaintSurface`处理程序需要显示三个位图。 处理程序假定手机处于纵向模式，并将画布垂直拆分为三个相等的部分。

第一个位图以最简单的 [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,System.Single,System.Single,SkiaSharp.SKPaint)) 方法显示。 你需要指定的所有内容都是 X 和 Y 坐标，位图的左上角位于该位置：

```csharp
public void DrawBitmap (SKBitmap bitmap, Single x, Single y, SKPaint paint = null)
```

尽管定义了一个 `SKPaint` 参数，但它具有默认值， `null` 您可以将其忽略。 位图的像素只是以一对一的映射传输到显示图面的像素。 在下一部分中，你将在 `SKPaint` [**SkiaSharp 透明度**](transparency.md)上看到此参数的应用程序。

程序可以使用和属性获取位图的像素尺寸 [`Width`](xref:SkiaSharp.SKBitmap.Width) [`Height`](xref:SkiaSharp.SKBitmap.Height) 。 这些属性允许程序计算坐标，以将位图置于画布的三分之一的中心：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    if (webBitmap != null)
    {
        float x = (info.Width - webBitmap.Width) / 2;
        float y = (info.Height / 3 - webBitmap.Height) / 2;
        canvas.DrawBitmap(webBitmap, x, y);
    }
    ...
}
```

另外两个位图以具有参数的版本显示 [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKPaint)) `SKRect` ：

```csharp
public void DrawBitmap (SKBitmap bitmap, SKRect dest, SKPaint paint = null)
```

的第三个版本 [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKRect,SkiaSharp.SKPaint)) 具有两个 `SKRect` 参数，用于指定要显示的位图的矩形子集，但本文中未使用该版本。

下面是用于显示从嵌入资源位图加载的位图的代码：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    if (resourceBitmap != null)
    {
        canvas.DrawBitmap(resourceBitmap,
            new SKRect(0, info.Height / 3, info.Width, 2 * info.Height / 3));
    }
    ...
}
```

位图延伸到矩形的尺寸，这就是在这些屏幕截图中，猴子水平拉伸的原因：

[![基本位图页面的三个屏幕截图](bitmaps-images/basicbitmaps-small.png)](bitmaps-images/basicbitmaps-large.png#lightbox "基本位图页面的三个屏幕截图")

第三个图像 &mdash; ，您只能查看是否运行程序并从自己的图片库中加载照片 &mdash; ，还会在矩形中显示，但会调整矩形的位置和大小以保持位图的纵横比。 此计算更多，因为它需要根据位图和目标矩形的大小计算缩放因子，并在该区域中居中矩形：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    if (libraryBitmap != null)
    {
        float scale = Math.Min((float)info.Width / libraryBitmap.Width,
                               info.Height / 3f / libraryBitmap.Height);

        float left = (info.Width - scale * libraryBitmap.Width) / 2;
        float top = (info.Height / 3 - scale * libraryBitmap.Height) / 2;
        float right = left + scale * libraryBitmap.Width;
        float bottom = top + scale * libraryBitmap.Height;
        SKRect rect = new SKRect(left, top, right, bottom);
        rect.Offset(0, 2 * info.Height / 3);

        canvas.DrawBitmap(libraryBitmap, rect);
    }
    else
    {
        using (SKPaint paint = new SKPaint())
        {
            paint.Color = SKColors.Blue;
            paint.TextAlign = SKTextAlign.Center;
            paint.TextSize = 48;

            canvas.DrawText("Tap to load bitmap",
                info.Width / 2, 5 * info.Height / 6, paint);
        }
    }
}
```

如果尚未从图片库中加载位图，则 `else` 块会显示一些文本以提示用户点击屏幕。

可以显示具有各种透明度的位图， [**SkiaSharp 透明度**](transparency.md) 上的下一篇文章介绍了如何操作。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [从图片库中选取照片](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)