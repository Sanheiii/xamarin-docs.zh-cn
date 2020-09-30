---
title: 像素和与设备无关的单位
description: 本文探讨了 SkiaSharp 坐标和坐标之间的差异 Xamarin.Forms ，并通过示例代码对此进行了演示。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 26C25BB8-FBE8-4B77-B01D-16A163A16890
author: davidbritch
ms.author: dabritch
ms.date: 02/09/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3df3a606c54d7367079638d48330dfe1b214043b
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563180"
---
# <a name="pixels-and-device-independent-units"></a>像素和与设备无关的单位

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_探索 SkiaSharp 坐标和坐标之间的差异 Xamarin.Forms_

本文探讨了 SkiaSharp 和中使用的坐标系统之间的差异 Xamarin.Forms 。 您可以获取信息以在两个坐标系统之间进行转换，还可以绘制填充特定区域的图形：

![填充屏幕的椭圆](pixels-images/screenfillexample.png)

如果已在中进行了 Xamarin.Forms 一段时间的编程，则可能会有 Xamarin.Forms 坐标和大小。 前面的两篇文章中所画的圆可能对您而言有些小。

与大小相比，这些圆圈 *很* 小 Xamarin.Forms 。 默认情况下，SkiaSharp 在 Xamarin.Forms 基础平台建立的与设备无关的单位上，以像素为单位绘制。 有关坐标系的详细信息， Xamarin.Forms 请参阅[第5章中的 (。处理](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter05.md)*通过 Xamarin.Forms 创建移动应用*的书籍大小 ) 

[**SkewSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)程序中名为 "**表面大小**" 的页面使用 SkiaSharp 文本输出来显示三个不同源的显示图面大小：

- 对象的正常 Xamarin.Forms [`Width`](xref:Xamarin.Forms.VisualElement.Width) 和 [`Height`](xref:Xamarin.Forms.VisualElement.Height) 属性 `SKCanvasView` 。
- [`CanvasSize`](xref:SkiaSharp.Views.Forms.SKCanvasView.CanvasSize)对象的属性 `SKCanvasView` 。
- [`Size`](xref:SkiaSharp.SKImageInfo.Size)值的属性 `SKImageInfo` ，它与 `Width` `Height` 前两页中使用的和属性一致。

[`SurfaceSizePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SurfaceSizePage.cs)类显示如何显示这些值。 构造函数将 `SKCanvasView` 对象保存为字段，因此可以在事件处理程序中对其进行访问 `PaintSurface` ：

```csharp
SKCanvasView canvasView;

public SurfaceSizePage()
{
    Title = "Surface Size";

    canvasView = new SKCanvasView();
    canvasView.PaintSurface += OnCanvasViewPaintSurface;
    Content = canvasView;
}
```

`SKCanvas` 包含六种不同 `DrawText` 的方法，但此 [`DrawText`](xref:SkiaSharp.SKCanvas.DrawText(System.String,System.Single,System.Single,SkiaSharp.SKPaint)) 方法是最简单的方法：

```csharp
public void DrawText (String text, Single x, Single y, SKPaint paint)
```

指定文本字符串、要开始文本的 X 和 Y 坐标以及 `SKPaint` 对象。 X 坐标指定文本左侧的位置，但要注意： Y 坐标指定了文本 *基线* 的位置。 如果您曾经在手写纸上手写，则基准是字符所在的行，下两个下行 (如字母 g、p、q 和 y) 下降。

`SKPaint`对象用于指定文本的颜色、字体系列和文本大小。 默认情况下， [`TextSize`](xref:SkiaSharp.SKPaint.TextSize) 属性的值为12，这会导致高分辨率设备（如手机）上出现细小文本。 除了最简单的应用程序之外，您还需要一些有关所显示文本大小的信息。 `SKPaint`类定义了一个 [`FontMetrics`](xref:SkiaSharp.SKPaint.FontMetrics) 属性和几个 [`MeasureText`](xref:SkiaSharp.SKPaint.MeasureText(System.String)) 方法，但对于不太复杂的需求， [`FontSpacing`](xref:SkiaSharp.SKPaint.FontSpacing) 属性提供了一个建议的值，用于对连续的文本行进行间距。

下面的 `PaintSurface` 处理程序将为40像素的创建一个 `SKPaint` 对象，该对象 `TextSize` 是从升部顶部到下行底部的文本的所需垂直高度。 `FontSpacing`对象返回的值 `SKPaint` 比此值小一些，约47像素。

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint paint = new SKPaint
    {
        Color = SKColors.Black,
        TextSize = 40
    };

    float fontSpacing = paint.FontSpacing;
    float x = 20;               // left margin
    float y = fontSpacing;      // first baseline
    float indent = 100;

    canvas.DrawText("SKCanvasView Height and Width:", x, y, paint);
    y += fontSpacing;
    canvas.DrawText(String.Format("{0:F2} x {1:F2}",
                                  canvasView.Width, canvasView.Height),
                    x + indent, y, paint);
    y += fontSpacing * 2;
    canvas.DrawText("SKCanvasView CanvasSize:", x, y, paint);
    y += fontSpacing;
    canvas.DrawText(canvasView.CanvasSize.ToString(), x + indent, y, paint);
    y += fontSpacing * 2;
    canvas.DrawText("SKImageInfo Size:", x, y, paint);
    y += fontSpacing;
    canvas.DrawText(info.Size.ToString(), x + indent, y, paint);
}
```

此方法将在左侧) 的小边距处开始第一行文本，X 坐标为 20 (; Y 坐标为 `fontSpacing` ，这比在显示图面顶部显示第一行文本的完整高度所需的时间稍多。 每次调用后 `DrawText` ，Y 坐标会按的一或两个增量递增 `fontSpacing` 。

下面是正在运行的程序：

[!["表面大小" 页的三向屏幕截图](pixels-images/surfacesize-small.png)](pixels-images/surfacesize-large.png#lightbox ""表面大小" 页的三向屏幕截图")

正如您所看到的， `CanvasSize` 的属性 `SKCanvasView` 和 `Size` 值的属性 `SKImageInfo` 在报告像素维度时是一致的。 的 `Height` 和 `Width` 属性 `SKCanvasView` 是 Xamarin.Forms 属性，并报告了平台定义的与设备无关的单位中的视图大小。

左侧的 iOS 7 在每个与设备无关的单位上有两个像素，而中心的 Android 结点5的每个单位为三个像素。 这就是前面显示的简单圆圈在不同平台上具有不同大小的原因。

如果希望完全使用与设备无关的单位，可以通过将 `IgnorePixelScaling` 的属性设置为来执行此操作 `SKCanvasView` `true` 。 不过，您可能不喜欢结果。 SkiaSharp 在更小的设备表面上呈现图形，并且像素大小等于与设备无关的单位中的视图大小。  (例如，SkiaSharp 将在结点5上使用 360 x 512 像素的显示图面。 ) 它会放大该图像大小，从而导致 jaggies 的位图。

为了保持相同的图像分辨率，更好的解决方案是编写自己的简单函数以便在两个坐标系统之间进行转换。

除了 `DrawCircle` 方法之外， `SKCanvas` 还定义了两种 `DrawOval` 方法来绘制椭圆。 椭圆是通过两个半径而不是单个半径来定义的。 这称为 *主要半径* 和 *次 radius*。 `DrawOval`方法绘制一个椭圆，其中两个半径平行于 X 轴和 Y 轴。  (如果您需要绘制一个椭圆，其中包含与 X 和 Y 轴不平行的轴，则可以使用旋转 [**转换**](../transforms/rotate.md) 一文中所述的旋转转换，如介绍 [**如何绘制圆弧) 的三种方法**](../curves/arcs.md) 中所述。 此方法的重载 [`DrawOval`](xref:SkiaSharp.SKCanvas.DrawOval(System.Single,System.Single,System.Single,System.Single,SkiaSharp.SKPaint)) 将两个半径参数命名 `rx` `ry` 为并指示它们平行于 X 轴和 Y 轴：

```csharp
public void DrawOval (Single cx, Single cy, Single rx, Single ry, SKPaint paint)
```

是否可以绘制填充显示图面的椭圆？ " **椭圆填充** " 页演示了如何操作。 `PaintSurface` [**EllipseFillPage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/EllipseFillPage.xaml.cs)类中的事件处理程序从和值减去半个笔划宽度， `xRadius` `yRadius` 以适应显示图面中的整个椭圆及其轮廓：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float strokeWidth = 50;
    float xRadius = (info.Width - strokeWidth) / 2;
    float yRadius = (info.Height - strokeWidth) / 2;

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = strokeWidth
    };
    canvas.DrawOval(info.Width / 2, info.Height / 2, xRadius, yRadius, paint);
}
```

此处运行的是：

[!["表面大小" 页的三向屏幕截图](pixels-images/ellipsefill-small.png)](pixels-images/ellipsefill-large.png#lightbox ""表面大小" 页的三向屏幕截图")

另一 [`DrawOval`](xref:SkiaSharp.SKCanvas.DrawOval(SkiaSharp.SKRect,SkiaSharp.SKPaint)) 种方法具有 [`SKRect`](xref:SkiaSharp.SKRect) 参数，该参数是根据其左上角和右下角的 X 和 Y 坐标定义的矩形。 该椭圆将填充该矩形，这表示可以在 " **椭圆形填充** " 页中使用它，如下所示：

```csharp
SKRect rect = new SKRect(0, 0, info.Width, info.Height);
canvas.DrawOval(rect, paint);
```

但是，这四个边截断椭圆轮廓的所有边缘。 你需要根据提供的所有 `SKRect` 构造函数参数进行调整 `strokeWidth` ，使此工作正确：

```csharp
SKRect rect = new SKRect(strokeWidth / 2,
                         strokeWidth / 2,
                         info.Width - strokeWidth / 2,
                         info.Height - strokeWidth / 2);
canvas.DrawOval(rect, paint);
```

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)