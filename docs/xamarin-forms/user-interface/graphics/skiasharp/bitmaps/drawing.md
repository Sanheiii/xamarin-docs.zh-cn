---
title: 在 SkiaSharp 位图上创建和绘制
description: 了解如何创建 SkiaSharp 位图，然后在这些位图上创建基于它们的画布。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 79BD3266-D457-4E50-BDDF-33450035FA0F
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 91051b1ffc859d4e3deb62d41709db0c587b2789
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91560827"
---
# <a name="creating-and-drawing-on-skiasharp-bitmaps"></a>在 SkiaSharp 位图上创建和绘制

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

已了解应用程序如何从 Web、应用程序资源以及用户的照片库中加载位图。 还可以在应用程序中创建新的位图。 最简单的方法包括以下构造函数之一 [`SKBitmap`](xref:SkiaSharp.SKBitmap.%23ctor(System.Int32,System.Int32,System.Boolean)) ：

```csharp
SKBitmap bitmap = new SKBitmap(width, height);
```

`width`和 `height` 参数是整数，它指定位图的像素尺寸。 此构造函数将创建一个具有四个字节/像素的全色位图：红色、绿色、蓝色和 alpha (不透明度) 组件的一个字节。

创建新的位图后，需要在位图表面上获得一些内容。 通常采用以下两种方式之一执行此操作：

- 使用标准绘图方法在位图上绘图 `Canvas` 。
- 直接访问像素位。

本文介绍了第一种方法：

![绘图示例](drawing-images/DrawingSample.png "绘图示例")

第二种方法在 [**访问 SkiaSharp 位图像素**](pixel-bits.md)一文中进行讨论。

## <a name="drawing-on-the-bitmap"></a>在位图上绘制

在位图表面上绘制与在视频显示器上绘图的方式相同。 若要在视频显示器上绘图，请 `SKCanvas` 从 `PaintSurface` 事件参数获取对象。 若要在位图上绘图，可 `SKCanvas` 使用 [`SKCanvas`](xref:SkiaSharp.SKCanvas.%23ctor(SkiaSharp.SKBitmap)) 构造函数创建对象：

```csharp
SKCanvas canvas = new SKCanvas(bitmap);
```

在位图上完成绘制后，可以释放 `SKCanvas` 对象。 出于此原因， `SKCanvas` 通常在语句中调用构造函数 `using` ：

```csharp
using (SKCanvas canvas = new SKCanvas(bitmap))
{
    ··· // call drawing function
}
```

然后，可以显示位图。 稍后，该程序可以创建 `SKCanvas` 基于该位图的新对象，并对其进行更多的绘制。

**[SkiaSharpFormsDemos](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** 应用程序中的**Hello 位图**页面写入文本 "Hello，bitmap！" ，然后将该位图多次显示。

的构造函数 `HelloBitmapPage` 首先创建一个 `SKPaint` 用于显示文本的对象。 它确定文本字符串的尺寸，并创建具有这些尺寸的位图。 然后 `SKCanvas` ，它基于该位图创建一个对象，然后调用 `Clear` ，然后调用 `DrawText` 。 最好是 `Clear` 使用新位图调用，因为新创建的位图可能包含随机数据。

构造函数通过创建一个 `SKCanvasView` 显示位图的对象结束：

```csharp
public partial class HelloBitmapPage : ContentPage
{
    const string TEXT = "Hello, Bitmap!";
    SKBitmap helloBitmap;

    public HelloBitmapPage()
    {
        Title = TEXT;

        // Create bitmap and draw on it
        using (SKPaint textPaint = new SKPaint { TextSize = 48 })
        {
            SKRect bounds = new SKRect();
            textPaint.MeasureText(TEXT, ref bounds);

            helloBitmap = new SKBitmap((int)bounds.Right,
                                       (int)bounds.Height);

            using (SKCanvas bitmapCanvas = new SKCanvas(helloBitmap))
            {
                bitmapCanvas.Clear();
                bitmapCanvas.DrawText(TEXT, 0, -bounds.Top, textPaint);
            }
        }

        // Create SKCanvasView to view result
        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Aqua);

        for (float y = 0; y < info.Height; y += helloBitmap.Height)
            for (float x = 0; x < info.Width; x += helloBitmap.Width)
            {
                canvas.DrawBitmap(helloBitmap, x, y);
            }
    }
}
```

`PaintSurface`处理程序在显示的行和列中多次呈现位图。 请注意， `Clear` 处理程序中的方法 `PaintSurface` 具有一个参数 `SKColors.Aqua` ，该参数将显示图面的背景色：

[![你好，Bitmap！](drawing-images/HelloBitmap.png "你好，Bitmap！")](drawing-images/HelloBitmap-Large.png#lightbox)

水绿色背景的外观显示位图是透明的，文本除外。

## <a name="clearing-and-transparency"></a>清除和透明度

" **Hello 位图** " 页的显示表明，该位图创建的位图是透明的，但黑色文本除外。 这就是显示图面的浅绿色色显示的原因。

说明这些方法的文档中的 `Clear` `SKCanvas` 语句： "替换画布的当前剪辑中的所有像素。 使用 "替换" 一词会显示这些方法的一个重要特征：所有的绘图方法均 `SKCanvas` 可向现有的显示图面添加内容。 `Clear`方法_替换_已存在的内容。

`Clear` 存在于两个不同的版本中：

- [`Clear`](xref:SkiaSharp.SKCanvas.Clear(SkiaSharp.SKColor))带参数的方法将 `SKColor` 显示图面的像素替换为该颜色的像素。

- [`Clear`](xref:SkiaSharp.SKCanvas.Clear)不带参数的方法使用颜色替换像素 [`SKColors.Empty`](xref:SkiaSharp.SKColors.Empty) ，颜色是颜色，其中的所有组件 (红色、绿色、蓝色和 alpha) 设置为零。 此颜色有时称为 "透明黑色"。

`Clear`在新位图上不使用任何参数调用会将整个位图初始化为完全透明。 随后在位图上绘制的任何内容通常都是不透明或部分不透明。

下面是要尝试的操作：在 " **Hello 位图** " 页中，将 `Clear` 应用于的方法替换为以下内容 `bitmapCanvas` ：

```csharp
bitmapCanvas.Clear(new SKColor(255, 0, 0, 128));
```

`SKColor`构造函数参数的顺序为红色、绿色、蓝色和 alpha，其中每个值的范围介于0到255之间。 请记住，alpha 值0是透明的，alpha 值255是不透明的。

 (255，0，0，128) 的值会将位图像素清除为50% 不透明度的红色像素。 这意味着位图背景为半透明。 位图的半透明红色背景与显示图面的浅绿色背景相结合，以创建灰色背景。

通过在初始值设定项中放置以下赋值，尝试将文本颜色设置为透明黑色 `SKPaint` ：

```csharp
Color = new SKColor(0, 0, 0, 0)
```

你可能会认为，这一透明文本会创建位图的完全透明区域，你会在该区域中看到显示图面的背景色。 但这并不是这样。 在位图上已有的内容上绘制文本。 透明文本将不可见。

任何 `Draw` 方法都不会使位图更透明。 仅 `Clear` 可执行此操作。

## <a name="bitmap-color-types"></a>位图颜色类型

最简单的 `SKBitmap` 构造函数允许您为位图指定整数像素的宽度和高度。 其他 `SKBitmap` 构造函数更复杂。 这些构造函数需要两个枚举类型的参数： [`SKColorType`](xref:SkiaSharp.SKColorType) 和 [`SKAlphaType`](xref:SkiaSharp.SKAlphaType) 。 其他构造函数使用 [`SKImageInfo`](xref:SkiaSharp.SKImageInfo) 结构，该结构合并此信息。

`SKColorType`枚举具有9个成员。 其中每个成员都说明了存储位图像素的特定方式：

- `Unknown`
- `Alpha8`&mdash;每个像素都是8位，表示从完全透明到完全不透明的 alpha 值
- `Rgb565`&mdash;每个像素为16位，5位表示红色和蓝色，6表示绿色
- `Argb4444`&mdash;每个像素为16位，每个像素分别用于 alpha、红色、绿色和蓝色
- `Rgba8888`&mdash;每个像素为32位，每个像素分别为红色、绿色、蓝色和 alpha
- `Bgra8888`&mdash;每个像素为32位，每个像素分别为蓝色、绿色、红色和 alpha
- `Index8`&mdash;每个像素为8位，表示为[`SKColorTable`](xref:SkiaSharp.SKColorTable)
- `Gray8`&mdash;每个像素都是表示灰色阴影和白色的8位
- `RgbaF16`&mdash;每个像素为64位，在16位浮点格式中为红色、绿色、蓝色和 alpha

两种格式，其中每个像素为32像素 (4 个字节，) 通常称为 _全色_ 格式。 很多其他格式在视频显示时的日期不能为全色。 有限颜色的位图足以满足这些显示器的需要，并允许位图占用更少的内存空间。

如今，程序员几乎始终使用完全颜色的位图，而不会与其他格式一起使用。 例外情况是 `RgbaF16` 格式，它允许比全色格式更好的颜色分辨率。 但是，这种格式用于专用目的（例如医疗图像），在与标准全色显示器一起使用时，这种格式不太合理。

这一系列文章会将自身限制为 `SKBitmap` 默认情况下在未指定任何成员时使用的颜色格式 `SKColorType` 。 此默认格式基于基础平台。 对于支持的平台 Xamarin.Forms ，默认颜色类型为：

- `Rgba8888` 适用于 iOS 和 Android
- `Bgra8888` 对于 UWP

唯一的区别是内存中4个字节的顺序，这仅在直接访问像素位时才会出现问题。 在 [**访问 SkiaSharp 位图像素**](pixel-bits.md)之前，这种情况并不重要。

`SKAlphaType`枚举具有四个成员：

- `Unknown`
- `Opaque`&mdash;位图无透明度
- `Premul`&mdash;颜色组件由 alpha 分量预先乘以
- `Unpremul`&mdash;alpha 分量未预先乘以颜色组件

下面是具有50% 透明度的4字节红色位图像素，其中的字节显示顺序为红色、绿色、蓝色和 alpha：

0xFF 0x00 0x00 0x80

如果在显示图面上呈现包含半透明像素的位图，则每个位图像素的颜色分量必须与该像素的 alpha 值相乘，并且显示图面的相应像素的颜色部分必须乘以255减去 alpha 值。 然后，可以合并这两个像素。 如果位图像素中的颜色组件已经按 alpha 值预 mulitplied，则可以更快地呈现位图。 此红色像素的存储方式如下所示：

0x80 0x00 0x00 0x80

这种性能改进是指 `SkiaSharp` 默认情况下使用格式创建位图的原因 `Premul` 。 但同样，只需要在访问和操作像素位时了解这一点。

## <a name="drawing-on-existing-bitmaps"></a>在现有位图上绘图

不需要创建新的位图来绘制它。 您还可以在现有位图上绘图。

" **猴子 Moustache** " 页使用其构造函数加载 **MonkeyFace.png** 映像。 然后 `SKCanvas` ，它基于该位图创建一个对象，并使用 `SKPaint` 和 `SKPath` 对象在其上绘制 moustache：

```csharp
public partial class MonkeyMoustachePage : ContentPage
{
    SKBitmap monkeyBitmap;

    public MonkeyMoustachePage()
    {
        Title = "Monkey Moustache";

        monkeyBitmap = BitmapExtensions.LoadBitmapResource(GetType(),
            "SkiaSharpFormsDemos.Media.MonkeyFace.png");

        // Create canvas based on bitmap
        using (SKCanvas canvas = new SKCanvas(monkeyBitmap))
        {
            using (SKPaint paint = new SKPaint())
            {
                paint.Style = SKPaintStyle.Stroke;
                paint.Color = SKColors.Black;
                paint.StrokeWidth = 24;
                paint.StrokeCap = SKStrokeCap.Round;

                using (SKPath path = new SKPath())
                {
                    path.MoveTo(380, 390);
                    path.CubicTo(560, 390, 560, 280, 500, 280);

                    path.MoveTo(320, 390);
                    path.CubicTo(140, 390, 140, 280, 200, 280);

                    canvas.DrawPath(path, paint);
                }
            }
        }

        // Create SKCanvasView to view result
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
        canvas.DrawBitmap(monkeyBitmap, info.Rect, BitmapStretch.Uniform);
    }
}
```

构造函数结束时，通过创建一个 `SKCanvasView` 其 `PaintSurface` 处理程序只显示结果的：

[![猴子 Moustache](drawing-images/MonkeyMoustache.png "猴子 Moustache")](drawing-images/MonkeyMoustache-Large.png#lightbox)

## <a name="copying-and-modifying-bitmaps"></a>复制和修改位图

`SKCanvas`可用于在位图上进行绘制的的方法包括 `DrawBitmap` 。 这意味着，可以在另一个位图上绘制位图，通常以某种方式对其进行修改。

修改位图的最通用的方法是通过访问实际像素位，这篇文章介绍了 **[访问 SkiaSharp 位图像素](pixel-bits.md)** 的文章。 但有许多其他方法可用于修改不需要访问像素位的位图。

**[SkiaSharpFormsDemos](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** 应用程序附带的以下位图的高度为360像素，高度为480像素：

![山地 Climbers](drawing-images/MountainClimbers.jpg "山地 Climbers")

假设你没有收到来自左侧的猴子的权限，无法发布此照片。 一种解决方法是使用称为 _pixelization_的技术来掩盖猴子的面孔。 人脸的像素被替换为颜色块，因此您无法对这些功能进行设置。 颜色块通常通过对与这些块相对应的像素颜色进行求平均值，从原始图像派生。 但您不需要自行执行这一要求。 将位图复制到较小的像素维度时，会自动发生这种情况。

左侧的猴子面占据大约72像素的正方形区，其左上角的位置为 (112，238) 。 让我们将72像素的正方形区替换为一组 9 x 9 的彩色块，其中每个块都是 8 x 8 像素正方形。

**Pixelize 图像**页面加载到该位图，并首先创建一个名为的小9像素正方形位图 `faceBitmap` 。 这是一个用于仅复制猴子面的位置。 目标矩形只是9像素正方形，但源矩形为 72-像素正方形。 每 8 8 8 个源像素块仅向下合并到一个像素，只需对颜色求平均值。

下一步是将原始位图复制到相同大小的新位图中 `pixelizedBitmap` 。 然后，将 `faceBitmap` 使用一个72像素的正方形目标矩形将小的复制到其顶部，以便将的每个像素的 `faceBitmap` 大小扩展到8倍：

```csharp
public class PixelizedImagePage : ContentPage
{
    SKBitmap pixelizedBitmap;

    public PixelizedImagePage ()
    {
        Title = "Pixelize Image";

        SKBitmap originalBitmap = BitmapExtensions.LoadBitmapResource(GetType(),
            "SkiaSharpFormsDemos.Media.MountainClimbers.jpg");

        // Create tiny bitmap for pixelized face
        SKBitmap faceBitmap = new SKBitmap(9, 9);

        // Copy subset of original bitmap to that
        using (SKCanvas canvas = new SKCanvas(faceBitmap))
        {
            canvas.Clear();
            canvas.DrawBitmap(originalBitmap,
                              new SKRect(112, 238, 184, 310),   // source
                              new SKRect(0, 0, 9, 9));          // destination

        }

        // Create full-sized bitmap for copy
        pixelizedBitmap = new SKBitmap(originalBitmap.Width, originalBitmap.Height);

        using (SKCanvas canvas = new SKCanvas(pixelizedBitmap))
        {
            canvas.Clear();

            // Draw original in full size
            canvas.DrawBitmap(originalBitmap, new SKPoint());

            // Draw tiny bitmap to cover face
            canvas.DrawBitmap(faceBitmap,
                              new SKRect(112, 238, 184, 310));  // destination
        }

        // Create SKCanvasView to view result
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
        canvas.DrawBitmap(pixelizedBitmap, info.Rect, BitmapStretch.Uniform);
    }
}
```

构造函数通过创建 `SKCanvasView` 以显示结果来结束：

[![Pixelize 图](drawing-images/PixelizeImage.png "Pixelize 图")](drawing-images/PixelizeImage-Large.png#lightbox)

## <a name="rotating-bitmaps"></a>旋转位图

另一种常见任务是旋转位图。 当从 iPhone 或 iPad 照片库中检索位图时，此方法特别有用。 除非在拍摄照片时该设备处于特定方向，否则图片可能会倒置或侧向旋转。

如果打开位图，则需要创建与第一个位图大小相同的另一个位图，然后将变换设置为180度旋转，同时将第一个位图复制到第二个位图。 本节中的所有示例 `bitmap` 都是 `SKBitmap` 需要轮换的对象：

```csharp
SKBitmap rotatedBitmap = new SKBitmap(bitmap.Width, bitmap.Height);

using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
{
    canvas.Clear();
    canvas.RotateDegrees(180, bitmap.Width / 2, bitmap.Height / 2);
    canvas.DrawBitmap(bitmap, new SKPoint());
}
```

旋转90度时，需要通过交换高度和宽度来创建一个与原始尺寸不同的位图。 例如，如果原始位图的宽度为1200像素，800像素高，则旋转后的位图为800像素宽和1200像素宽。 设置平移和旋转，使位图围绕其左上角旋转，然后移动到视图中。  (记住， `Translate` 和 `RotateDegrees` 方法的调用方式与应用它们的方式相反。 ) 以下是顺时针旋转90度的代码：

```csharp
SKBitmap rotatedBitmap = new SKBitmap(bitmap.Height, bitmap.Width);

using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
{
    canvas.Clear();
    canvas.Translate(bitmap.Height, 0);
    canvas.RotateDegrees(90);
    canvas.DrawBitmap(bitmap, new SKPoint());
}
```

下面是一个类似的函数，用于逆时针旋转90度：

```csharp
SKBitmap rotatedBitmap = new SKBitmap(bitmap.Height, bitmap.Width);

using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
{
    canvas.Clear();
    canvas.Translate(0, bitmap.Width);
    canvas.RotateDegrees(-90);
    canvas.DrawBitmap(bitmap, new SKPoint());
}
```

在[**裁剪 SkiaSharp 位图**](cropping.md#cropping-skiasharp-bitmaps)一文中介绍的**照片谜题**页中使用这两种方法。

允许用户使用90度增量来旋转位图的程序只需实现一个函数以90度进行旋转。 然后，用户可以通过重复执行此函数，以90度的任何增量进行旋转。

程序还可以将位图旋转任意量。 一种简单的方法是通过使用通用化变量替换180来修改以180度旋转的函数 `angle` ：

```csharp
SKBitmap rotatedBitmap = new SKBitmap(bitmap.Width, bitmap.Height);

using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
{
    canvas.Clear();
    canvas.RotateDegrees(angle, bitmap.Width / 2, bitmap.Height / 2);
    canvas.DrawBitmap(bitmap, new SKPoint());
}
```

但是，在一般情况下，此逻辑将裁剪旋转位图的角。 更好的方法是使用三角函数来计算旋转位图的大小，使其包含这些角。

此三角函数显示在 **位图旋转** 页中。 XAML 文件实例化 `SKCanvasView` 和，它的范围介于 `Slider` 0 到360度之间，并 `Label` 显示当前值：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Bitmaps.BitmapRotatorPage"
             Title="Bitmap Rotator">
    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="slider"
                Maximum="360"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference slider},
                              Path=Value,
                              StringFormat='Rotate by {0:F0}&#x00B0;'}"
               HorizontalTextAlignment="Center" />

    </StackLayout>
</ContentPage>
```

代码隐藏文件加载位图资源，并将其保存为名为的静态只读字段 `originalBitmap` 。 处理程序中显示的位图 `PaintSurface` 为 `rotatedBitmap` ，最初设置为 `originalBitmap` ：

```csharp
public partial class BitmapRotatorPage : ContentPage
{
    static readonly SKBitmap originalBitmap =
        BitmapExtensions.LoadBitmapResource(typeof(BitmapRotatorPage),
            "SkiaSharpFormsDemos.Media.Banana.jpg");

    SKBitmap rotatedBitmap = originalBitmap;

    public BitmapRotatorPage ()
    {
        InitializeComponent ();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(rotatedBitmap, info.Rect, BitmapStretch.Uniform);
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        double angle = args.NewValue;
        double radians = Math.PI * angle / 180;
        float sine = (float)Math.Abs(Math.Sin(radians));
        float cosine = (float)Math.Abs(Math.Cos(radians));
        int originalWidth = originalBitmap.Width;
        int originalHeight = originalBitmap.Height;
        int rotatedWidth = (int)(cosine * originalWidth + sine * originalHeight);
        int rotatedHeight = (int)(cosine * originalHeight + sine * originalWidth);

        rotatedBitmap = new SKBitmap(rotatedWidth, rotatedHeight);

        using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
        {
            canvas.Clear(SKColors.LightPink);
            canvas.Translate(rotatedWidth / 2, rotatedHeight / 2);
            canvas.RotateDegrees((float)angle);
            canvas.Translate(-originalWidth / 2, -originalHeight / 2);
            canvas.DrawBitmap(originalBitmap, new SKPoint());
        }

        canvasView.InvalidateSurface();
    }
}
```

的 `ValueChanged` 处理程序 `Slider` 执行基于旋转角度创建新的操作 `rotatedBitmap` 。 新的宽度和高度取决于原始宽度和高度的正弦值和余弦的绝对值。 用于在旋转位图上绘制原始位图的变换将原始位图中心移到原点，然后将其旋转指定度数，然后将该中心平移到旋转位图的中心。  (`Translate` 和 `RotateDegrees` 方法的调用顺序与应用方式相反。 ) 

请注意，使用 `Clear` 方法来使背景 `rotatedBitmap` 浅粉色。 这只是为了说明显示的大小 `rotatedBitmap` ：

[![位图旋转](drawing-images/BitmapRotator.png "位图旋转")](drawing-images/BitmapRotator-Large.png#lightbox)

旋转位图的大小刚好足以包含整个原始位图，而不是更大。

## <a name="flipping-bitmaps"></a>翻转位图

通常在位图上执行的另一操作称为 " _翻转_"。 从概念上讲，位图围绕垂直轴或通过位图中心沿水平轴旋转三个尺寸。 垂直翻转创建镜像图像。

**[SkiaSharpFormsDemos](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** 应用程序中的**位图翻转**页面演示了这些过程。 XAML 文件包含一个 `SKCanvasView` 和两个按钮，用于垂直和水平翻转：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Bitmaps.BitmapFlipperPage"
             Title="Bitmap Flipper">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Button Text="Flip Vertical"
                Grid.Row="1" Grid.Column="0"
                Margin="0, 10"
                Clicked="OnFlipVerticalClicked" />

        <Button Text="Flip Horizontal"
                Grid.Row="1" Grid.Column="1"
                Margin="0, 10"
                Clicked="OnFlipHorizontalClicked" />
    </Grid>
</ContentPage>
```

代码隐藏文件在这些按钮的处理程序中实现以下两个操作 `Clicked` ：

```csharp
public partial class BitmapFlipperPage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(BitmapRotatorPage),
            "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

    public BitmapFlipperPage()
    {
        InitializeComponent();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform);
    }

    void OnFlipVerticalClicked(object sender, ValueChangedEventArgs args)
    {
        SKBitmap flippedBitmap = new SKBitmap(bitmap.Width, bitmap.Height);

        using (SKCanvas canvas = new SKCanvas(flippedBitmap))
        {
            canvas.Clear();
            canvas.Scale(-1, 1, bitmap.Width / 2, 0);
            canvas.DrawBitmap(bitmap, new SKPoint());
        }

        bitmap = flippedBitmap;
        canvasView.InvalidateSurface();
    }

    void OnFlipHorizontalClicked(object sender, ValueChangedEventArgs args)
    {
        SKBitmap flippedBitmap = new SKBitmap(bitmap.Width, bitmap.Height);

        using (SKCanvas canvas = new SKCanvas(flippedBitmap))
        {
            canvas.Clear();
            canvas.Scale(1, -1, 0, bitmap.Height / 2);
            canvas.DrawBitmap(bitmap, new SKPoint());
        }

        bitmap = flippedBitmap;
        canvasView.InvalidateSurface();
    }
}
```

垂直翻转是通过缩放变换来完成的，水平比例因子为 &ndash; 1。 缩放中心是位图的垂直中心。 水平翻转是垂直缩放因子为1的缩放变换 &ndash; 。

正如您从猴子衬衫上的反向字母中看到的，翻转与旋转并不相同。 但正如右侧的 UWP 屏幕截图所示，水平翻转和垂直翻转与旋转180度相同：

[![位图翻转](drawing-images/BitmapFlipper.png "位图翻转")](drawing-images/BitmapFlipper-Large.png#lightbox)

可以使用类似的技术来处理的另一个常见任务是将位图裁剪为矩形子集。 下一文章 [**裁剪 SkiaSharp 位图**](cropping.md)中介绍了这种情况。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)