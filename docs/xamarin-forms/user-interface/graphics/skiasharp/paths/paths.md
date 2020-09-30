---
title: SkiaSharp 中的路径基础知识
description: 本文探讨了用于组合已连接线条和曲线的 SkiaSharp SKPath 对象，并通过示例代码对此进行了演示。
ms.prod: xamarin
ms.assetid: A7EDA6C2-3921-4021-89F3-211551E430F1
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6387da5ffa240c2509a2942a1e721def8f8d39b9
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91555627"
---
# <a name="path-basics-in-skiasharp"></a>SkiaSharp 中的路径基础知识

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_浏览用于组合连接的线条和曲线的 SkiaSharp SKPath 对象_

图形路径的最重要功能之一是能够定义多行的连接时间和不应连接的时间。 差别很大，因为这两个三角形的顶部演示：

![显示连接线路和断开线路之间的差异的两个三角形](paths-images/connectedlinesexample.png)

图形路径由 [`SKPath`](xref:SkiaSharp.SKPath) 对象封装。 路径是一个或多个 *轮廓*的集合。 每个轮廓都是 *连接* 的直线和曲线的集合。 等高线并不相互连接，但它们可能会相互重叠。 有时，单个等高线可能会相互重叠。

等高线通常首先会调用以下方法 `SKPath` ：

- [`MoveTo`](xref:SkiaSharp.SKPath.MoveTo*) 开始新的等高线

该方法的参数是一个点，您可以将它表示为 `SKPoint` 值或单独的 X 和 Y 坐标。 此 `MoveTo` 调用在轮廓的开头和初始 *当前点*之间建立一个点。 您可以调用以下方法，以将直线或曲线从当前点继续到在方法中指定的点，然后将成为新的当前点：

- [`LineTo`](xref:SkiaSharp.SKPath.LineTo*) 向路径添加直线
- [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo*) 添加一条弧线，它是圆或椭圆圆周上的直线
- [`CubicTo`](xref:SkiaSharp.SKPath.CubicTo*) 添加三次方贝塞尔曲线
- [`QuadTo`](xref:SkiaSharp.SKPath.QuadTo*) 添加二次贝塞尔曲线
- [`ConicTo`](xref:SkiaSharp.SKPath.ConicTo*) 若要添加有理数二次贝塞尔曲线，可精确地将圆锥部分渲染 (省略号、parabolas 和 hyperbolas) 

这五种方法均不包含描述直线或曲线所需的所有信息。 这五个方法中的每一个都与前一个方法调用所建立的当前点结合使用。 例如， `LineTo` 方法基于当前点向等高线添加一条直线，因此参数 `LineTo` 只是一个点。

`SKPath`该类还定义了与这六个方法同名但在开头使用的方法 `R` ：

- [`RMoveTo`](xref:SkiaSharp.SKPath.RMoveTo*)
- [`RLineTo`](xref:SkiaSharp.SKPath.RLineTo*)
- [`RArcTo`](xref:SkiaSharp.SKPath.RArcTo*)
- [`RCubicTo`](xref:SkiaSharp.SKPath.RCubicTo*)
- [`RQuadTo`](xref:SkiaSharp.SKPath.RQuadTo*)
- [`RConicTo`](xref:SkiaSharp.SKPath.RConicTo*)

`R`代表*相对*的。 这些方法与相应的方法具有相同的语法， `R` 但它们相对于当前点。 在多次调用的方法中，可以使用这些方法来绘制路径的相似部分。

等高线以对或的另一次调用结束， `MoveTo` `RMoveTo` 后者将开始一个新的轮廓，或对的调用 `Close` ，这将关闭等高线。 `Close`方法自动在从当前点到等高线的第一个点之间追加一条直线，并将该路径标记为闭合，这意味着它将在不使用任何笔触帽的情况下呈现。

"打开" 和 "闭合" 等高线的不同之处在于 **两个三角形等高线** 页面，使用 `SKPath` 带有两个轮廓的对象来呈现两个三角形。 第一个等高线是打开的，第二个是闭合的。 下面是 [`TwoTriangleContoursPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/TwoTriangleContoursPage.cs) 类：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Create the path
    SKPath path = new SKPath();

    // Define the first contour
    path.MoveTo(0.5f * info.Width, 0.1f * info.Height);
    path.LineTo(0.2f * info.Width, 0.4f * info.Height);
    path.LineTo(0.8f * info.Width, 0.4f * info.Height);
    path.LineTo(0.5f * info.Width, 0.1f * info.Height);

    // Define the second contour
    path.MoveTo(0.5f * info.Width, 0.6f * info.Height);
    path.LineTo(0.2f * info.Width, 0.9f * info.Height);
    path.LineTo(0.8f * info.Width, 0.9f * info.Height);
    path.Close();

    // Create two SKPaint objects
    SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Magenta,
        StrokeWidth = 50
    };

    SKPaint fillPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Cyan
    };

    // Fill and stroke the path
    canvas.DrawPath(path, fillPaint);
    canvas.DrawPath(path, strokePaint);
}
```

第一条轮廓包含对 [`MoveTo`](xref:SkiaSharp.SKPath.MoveTo(System.Single,System.Single)) X 和 Y 坐标（而不是 `SKPoint` 值）的调用，后面跟有三个对的调用，用来 [`LineTo`](xref:SkiaSharp.SKPath.LineTo(System.Single,System.Single)) 绘制三角形的三个边。 第二个轮廓只包含两个对 `LineTo` 的调用，但通过调用来完成等高线 [`Close`](xref:SkiaSharp.SKPath.Close) ，这会闭合轮廓。 差别很大：

[![两个三角形等高线页面的三向屏幕截图](paths-images/twotrianglecontours-small.png)](paths-images/twotrianglecontours-large.png#lightbox "两个三角形等高线页面的三向屏幕截图")

正如您所看到的，第一个等高线显然是一系列由三个连接的线，但最终不会与开始进行连接。 这两行在顶部重叠。 第二个轮廓明显闭合，并使用较少的调用完成， `LineTo` 因为 `Close` 方法会自动添加最后一行来结束等高线。

`SKCanvas` 仅定义一种 [`DrawPath`](xref:SkiaSharp.SKCanvas.DrawPath(SkiaSharp.SKPath,SkiaSharp.SKPaint)) 方法，在此演示中，此方法调用两次来填充和绘制路径。 所有轮廓都已填充，甚至包括那些未闭合的轮廓。 为了填充未闭合路径，假定在轮廓的起点和终点之间存在直线。 如果从第一个轮廓中删除最后一个轮廓 `LineTo` ，或 `Close` 从第二个轮廓中删除该调用，则每个轮廓将只有两个边，但将填充为三角形。

`SKPath` 定义许多其他方法和属性。 以下方法将整个等高线添加到路径，该路径可能已关闭或未关闭，具体取决于方法：

- [`AddRect`](xref:SkiaSharp.SKPath.AddRect*)
- [`AddRoundedRect`](xref:SkiaSharp.SKPath.AddRoundedRect(SkiaSharp.SKRect,System.Single,System.Single,SkiaSharp.SKPathDirection))
- [`AddCircle`](xref:SkiaSharp.SKPath.AddCircle(System.Single,System.Single,System.Single,SkiaSharp.SKPathDirection))
- [`AddOval`](xref:SkiaSharp.SKPath.AddOval(SkiaSharp.SKRect,SkiaSharp.SKPathDirection))
- [`AddArc`](xref:SkiaSharp.SKPath.AddArc(SkiaSharp.SKRect,System.Single,System.Single)) 在椭圆的圆周上添加曲线
- [`AddPath`](xref:SkiaSharp.SKPath.AddPath*) 向当前路径添加另一个路径
- [`AddPathReverse`](xref:SkiaSharp.SKPath.AddPathReverse(SkiaSharp.SKPath)) 若要反向添加其他路径

请记住， `SKPath` 对象只定义 &mdash; 了一系列点和连接的几何。 仅当 `SKPath` 与对象组合使用时 `SKPaint` ，才是使用特定颜色、笔划宽度等呈现的路径。 另外，请记住， `SKPaint` 传递给方法的对象 `DrawPath` 定义了整个路径的特征。 如果要绘制需要多种颜色的内容，则必须对每种颜色使用单独的路径。

正如直线的起点和终点的外观是通过描边帽定义的，两行间的连接的外观由 *笔划联接*定义。 可以通过将 [`StrokeJoin`](xref:SkiaSharp.SKPaint.StrokeJoin) 的属性设置 `SKPaint` 为枚举的成员来指定此 [`SKStrokeJoin`](xref:SkiaSharp.SKStrokeJoin) 内容：

- `Miter` 对于 pointy 联接
- `Round` 对于舍入联接
- `Bevel` 对于截联接

" **笔划联接** " 页显示了这三个笔划联接，其中包含类似于 " **笔划大写字母** " 页的代码。 这是 `PaintSurface` 类中的事件处理程序 [`StrokeJoinsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/StrokeJoinsPage.cs) ：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint textPaint = new SKPaint
    {
        Color = SKColors.Black,
        TextSize = 75,
        TextAlign = SKTextAlign.Right
    };

    SKPaint thickLinePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Orange,
        StrokeWidth = 50
    };

    SKPaint thinLinePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 2
    };

    float xText = info.Width - 100;
    float xLine1 = 100;
    float xLine2 = info.Width - xLine1;
    float y = 2 * textPaint.FontSpacing;
    string[] strStrokeJoins = { "Miter", "Round", "Bevel" };

    foreach (string strStrokeJoin in strStrokeJoins)
    {
        // Display text
        canvas.DrawText(strStrokeJoin, xText, y, textPaint);

        // Get stroke-join value
        SKStrokeJoin strokeJoin;
        Enum.TryParse(strStrokeJoin, out strokeJoin);

        // Create path
        SKPath path = new SKPath();
        path.MoveTo(xLine1, y - 80);
        path.LineTo(xLine1, y + 80);
        path.LineTo(xLine2, y + 80);

        // Display thick line
        thickLinePaint.StrokeJoin = strokeJoin;
        canvas.DrawPath(path, thickLinePaint);

        // Display thin line
        canvas.DrawPath(path, thinLinePaint);
        y += 3 * textPaint.FontSpacing;
    }
}
```

下面是正在运行的程序：

[!["笔划联接" 页的三向屏幕截图](paths-images/strokejoins-small.png)](paths-images/strokejoins-large.png#lightbox ""笔划联接" 页的三向屏幕截图")

斜接联接包含直线连接的尖点。 当两条直线以小角度联接时，斜切联接可能会非常长。 为了防止过长的斜接联接，斜切联接的长度受的属性值的限制 [`StrokeMiter`](xref:SkiaSharp.SKPaint.StrokeMiter) `SKPaint` 。 超过此长度的斜接联接将截为 "斜角联接"。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)