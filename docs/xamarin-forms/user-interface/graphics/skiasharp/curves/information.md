---
title: 路径信息和枚举
description: 本文介绍如何获取有关 SkiaSharp 路径和枚举内容的信息，并通过示例代码对此进行演示。
ms.prod: xamarin
ms.assetid: 8E8C5C6A-F324-4155-8652-7A77D231B3E5
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 09/12/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: cf9ebb819d5b424963170d563575c4900bbed28b
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556355"
---
# <a name="path-information-and-enumeration"></a>路径信息和枚举

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_获取有关路径和枚举内容的信息_

[`SKPath`](xref:SkiaSharp.SKPath)类定义多个属性和方法，它们允许你获取有关路径的信息。 [`Bounds`](xref:SkiaSharp.SKPath.Bounds)和 [`TightBounds`](xref:SkiaSharp.SKPath.TightBounds) 属性 (和相关方法) 获取路径的度量尺寸。 使用 [`Contains`](xref:SkiaSharp.SKPath.Contains(System.Single,System.Single)) 方法可以确定特定点是否在路径内。

有时，确定构成路径的所有直线和曲线的总长度很有用。 计算此长度并不是一个算法简单的任务，因此，一个名为的整个类 [`PathMeasure`](xref:SkiaSharp.SKPathMeasure) 将专门用于该任务。

还可用于获取构成路径的所有绘图操作和点。 首先，此功能可能看起来不必要：如果程序创建了路径，则程序已经知道了内容。 但是，你已了解到，路径也可以通过 [路径效果](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md) 创建，并将 [文本字符串转换为路径](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md)。 还可以获取构成这些路径的所有绘图操作和点。 一种可能的方法是将算法转换应用于所有点，例如，在半球环绕文本：

![半球上环绕的文本](information-images/pathenumerationsample.png)

## <a name="getting-the-path-length"></a>获取路径长度

在文章 [**路径和文本**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md) 中，你看到了如何使用 [`DrawTextOnPath`](xref:SkiaSharp.SKCanvas.DrawTextOnPath(System.String,SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPaint)) 方法绘制一个文本字符串，该文本字符串的基线遵循路径的过程。 但是，如果要调整文本大小以使其正好适合路径，该怎么办？ 围绕圆绘制文本非常简单，因为圆的周长非常简单。 但椭圆的圆周或贝塞尔曲线的长度并不简单。

[`SKPathMeasure`](xref:SkiaSharp.SKPathMeasure)类可帮助。 [构造函数](xref:SkiaSharp.SKPathMeasure.%23ctor(SkiaSharp.SKPath,System.Boolean,System.Single))接受 `SKPath` 参数， [`Length`](xref:SkiaSharp.SKPathMeasure.Length) 属性会显示其长度。

此类在 " **路径长度** " 示例中进行了演示，该示例基于 **贝塞尔曲线** 页面。 [**PathLengthPage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathLengthPage.xaml)文件是从派生的 `InteractivePage` ，其中包含触摸接口：

```xaml
<local:InteractivePage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       xmlns:local="clr-namespace:SkiaSharpFormsDemos"
                       xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
                       xmlns:tt="clr-namespace:TouchTracking"
                       x:Class="SkiaSharpFormsDemos.Curves.PathLengthPage"
                       Title="Path Length">
    <Grid BackgroundColor="White">
        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface" />
        <Grid.Effects>
            <tt:TouchEffect Capture="True"
                            TouchAction="OnTouchEffectAction" />
        </Grid.Effects>
    </Grid>
</local:InteractivePage>
```

[**PathLengthPage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathLengthPage.xaml.cs)代码隐藏文件允许您移动四个触摸点以定义三次方贝塞尔曲线的终点和控制点。 三个字段定义文本字符串、 `SKPaint` 对象和计算出的文本宽度：

```csharp
public partial class PathLengthPage : InteractivePage
{
    const string text = "Compute length of path";

    static SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Black,
        TextSize = 10,
    };

    static readonly float baseTextWidth = textPaint.MeasureText(text);
    ...
}
```

该 `baseTextWidth` 字段是文本的宽度（以10的设置为基础） `TextSize` 。

`PaintSurface`处理程序绘制贝塞尔曲线，然后调整文本大小以适应其完整长度：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Draw path with cubic Bezier curve
    using (SKPath path = new SKPath())
    {
        path.MoveTo(touchPoints[0].Center);
        path.CubicTo(touchPoints[1].Center,
                     touchPoints[2].Center,
                     touchPoints[3].Center);

        canvas.DrawPath(path, strokePaint);

        // Get path length
        SKPathMeasure pathMeasure = new SKPathMeasure(path, false, 1);

        // Find new text size
        textPaint.TextSize = pathMeasure.Length / baseTextWidth * 10;

        // Draw text on path
        canvas.DrawTextOnPath(text, path, 0, 0, textPaint);
    }
    ...
}
```

`Length`新创建的对象的属性 `SKPathMeasure` 获取路径的长度。 路径长度除以 `baseTextWidth` 值 (这是文本的宽度（基于文本大小为 10) ，然后再乘以基准文本大小10。 结果是一种新的文本大小，用于沿该路径显示文本：

[![路径长度页面的三次屏幕截图](information-images/pathlength-small.png)](information-images/pathlength-large.png#lightbox "路径长度页面的三次屏幕截图")

当 Bezier 曲线变得更长或更短时，您可以看到文本大小的变化。

## <a name="traversing-the-path"></a>遍历路径

`SKPathMeasure` 只需测量路径的长度。 对于介于零和路径长度之间的任何值， `SKPathMeasure` 对象可以获取路径上的位置以及该点处到路径曲线的切线。 相切作为对象形式的矢量提供 `SKPoint` ，或者作为封装在对象中的旋转 `SKMatrix` 。 以下是 `SKPathMeasure` 通过各种灵活的方式获取此信息的方法：

```csharp
Boolean GetPosition (Single distance, out SKPoint position)

Boolean GetTangent (Single distance, out SKPoint tangent)

Boolean GetPositionAndTangent (Single distance, out SKPoint position, out SKPoint tangent)

Boolean GetMatrix (Single distance, out SKMatrix matrix, SKPathMeasureMatrixFlags flag)
```

枚举的成员 [`SKPathMeasureMatrixFlags`](xref:SkiaSharp.SKPathMeasureMatrixFlags) 包括：

- `GetPosition`
- `GetTangent`
- `GetPositionAndTangent`

**Unicycle 半管道**页面在看上去沿三次贝赛尔曲线来回退的 Unicycle 上动画

[![Unicycle 半管道页面的三向屏幕截图](information-images/unicyclehalfpipe-small.png)](information-images/unicyclehalfpipe-large.png#lightbox "Unicycle 半管道页面的三向屏幕截图")

`SKPaint`用于对半管道和 unicycle 进行描边的对象定义为类中的字段 `UnicycleHalfPipePage` 。 此外，还定义了 `SKPath` unicycle 的对象：

```csharp
public class UnicycleHalfPipePage : ContentPage
{
    ...
    SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 3,
        Color = SKColors.Black
    };

    SKPath unicyclePath = SKPath.ParseSvgPathData(
        "M 0 0" +
        "A 25 25 0 0 0 0 -50" +
        "A 25 25 0 0 0 0 0 Z" +
        "M 0 -25 L 0 -100" +
        "A 15 15 0 0 0 0 -130" +
        "A 15 15 0 0 0 0 -100 Z" +
        "M -25 -85 L 25 -85");
    ...
}
```

类包含动画的和方法的标准重写 `OnAppearing` `OnDisappearing` 。 `PaintSurface`处理程序创建半管道的路径，然后绘制该路径。 `SKPathMeasure`然后，将基于此路径创建对象：

```csharp
public class UnicycleHalfPipePage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath pipePath = new SKPath())
        {
            pipePath.MoveTo(50, 50);
            pipePath.CubicTo(0, 1.25f * info.Height,
                             info.Width - 0, 1.25f * info.Height,
                             info.Width - 50, 50);

            canvas.DrawPath(pipePath, strokePaint);

            using (SKPathMeasure pathMeasure = new SKPathMeasure(pipePath))
            {
                float length = pathMeasure.Length;

                // Animate t from 0 to 1 every three seconds
                TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
                float t = (float)(timeSpan.TotalSeconds % 5 / 5);

                // t from 0 to 1 to 0 but slower at beginning and end
                t = (float)((1 - Math.Cos(t * 2 * Math.PI)) / 2);

                SKMatrix matrix;
                pathMeasure.GetMatrix(t * length, out matrix,
                                      SKPathMeasureMatrixFlags.GetPositionAndTangent);

                canvas.SetMatrix(matrix);
                canvas.DrawPath(unicyclePath, strokePaint);
            }
        }
    }
}
```

`PaintSurface`处理程序计算的值为 `t` 每五秒从0到1。 然后，它使用 `Math.Cos` 函数将其转换为 `t` 0 到1之间的值，并返回到0，其中0对应于左上角的 unicycle，而1对应于右上方的 unicycle。 余弦函数会导致在管道顶部速度最慢，底部的速度最快。

请注意，此值 `t` 必须与第一个参数的路径长度相乘 `GetMatrix` 。 然后，将矩阵应用到 `SKCanvas` 用于绘制 unicycle 路径的对象。

## <a name="enumerating-the-path"></a>枚举路径

的两个嵌入类 `SKPath` 可用于枚举路径的内容。 这些类为 [`SKPath.Iterator`](xref:SkiaSharp.SKPath.Iterator) 和 [`SKPath.RawIterator`](xref:SkiaSharp.SKPath.RawIterator) 。 这两个类非常相似，但 `SKPath.Iterator` 可以在路径中消除长度为零的元素，或接近零长度。 `RawIterator`下面的示例中使用了。

可以通过调用的方法来获取类型的对象 `SKPath.RawIterator` [`CreateRawIterator`](xref:SkiaSharp.SKPath.CreateRawIterator) `SKPath` 。 通过重复调用方法可以实现路径的枚举 [`Next`](xref:SkiaSharp.SKPath.RawIterator.Next*) 。 向它传递四个值的数组 `SKPoint` ：

```csharp
SKPoint[] points = new SKPoint[4];
...
SKPathVerb pathVerb = rawIterator.Next(points);
```

`Next`方法返回 [`SKPathVerb`](xref:SkiaSharp.SKPathVerb) 枚举类型的成员。 这些值指示路径中的特定绘图命令。 插入到数组中的有效点的数目取决于此谓词：

- `Move` 使用单个点
- `Line` 具有两个点
- `Cubic` 具有四个点
- `Quad` 有三个点
- `Conic` (的三个点，还会调用 [`ConicWeight`](xref:SkiaSharp.SKPath.RawIterator.ConicWeight*) 方法以实现权重) 
- `Close` 有一个点
- `Done`

`Done`谓词指示路径枚举已完成。

请注意，没有 `Arc` 谓词。 这表示在添加到路径时，所有弧线都转换为贝塞尔曲线。

数组中的某些信息 `SKPoint` 是冗余的。 例如，如果 `Move` 谓词后跟 `Line` 谓词，则伴随的两个点中的第一个与 `Line` `Move` 点相同。 实际上，这种冗余非常有用。 当你获取 `Cubic` 谓词时，将附带定义三次方贝塞尔曲线的四个点。 不需要保留上一个谓词建立的当前位置。

但有问题的谓词是 `Close` 。 此命令绘制从当前位置到该命令之前建立的等高线开头的直线 `Move` 。 理想情况下， `Close` 谓词应提供这两个点，而不只是一个点。 更糟的是，动词附带的点 `Close` 始终 (0，0) 。 当您枚举路径时，您可能需要保留 `Move` 点和当前位置。

## <a name="enumerating-flattening-and-malforming"></a>枚举、平展和 Malforming

有时需要将算法转换应用于路径，以便以某种方式对其进行 malform：

![半球上环绕的文本](information-images/pathenumerationsample.png)

其中的大多数字母都由直线组成，但这些直线显然都在弯曲。 这是如何实现的？

关键在于，原始直线分为一系列较小的直线。 然后，可以用不同的方式来处理这些单独的直线，以形成一条曲线。

为了帮助进行此过程， [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) 示例包含一个静态类，该静态类包含一个 [`PathExtensions`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathExtensions.cs) `Interpolate` 方法，该方法将一条直线向下分割为多个长度仅为一个单位的短行。 此外，类还包含若干方法，这些方法可将这三种贝塞尔曲线转换为一系列接近曲线的小直线。  (中提供了 [**三种类型的贝塞尔曲线**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/beziers.md)的参数化公式。 ) 此过程称为 " _平展_ 曲线"：

```csharp
static class PathExtensions
{
    ...
    static SKPoint[] Interpolate(SKPoint pt0, SKPoint pt1)
    {
        int count = (int)Math.Max(1, Length(pt0, pt1));
        SKPoint[] points = new SKPoint[count];

        for (int i = 0; i < count; i++)
        {
            float t = (i + 1f) / count;
            float x = (1 - t) * pt0.X + t * pt1.X;
            float y = (1 - t) * pt0.Y + t * pt1.Y;
            points[i] = new SKPoint(x, y);
        }

        return points;
    }

    static SKPoint[] FlattenCubic(SKPoint pt0, SKPoint pt1, SKPoint pt2, SKPoint pt3)
    {
        int count = (int)Math.Max(1, Length(pt0, pt1) + Length(pt1, pt2) + Length(pt2, pt3));
        SKPoint[] points = new SKPoint[count];

        for (int i = 0; i < count; i++)
        {
            float t = (i + 1f) / count;
            float x = (1 - t) * (1 - t) * (1 - t) * pt0.X +
                        3 * t * (1 - t) * (1 - t) * pt1.X +
                        3 * t * t * (1 - t) * pt2.X +
                        t * t * t * pt3.X;
            float y = (1 - t) * (1 - t) * (1 - t) * pt0.Y +
                        3 * t * (1 - t) * (1 - t) * pt1.Y +
                        3 * t * t * (1 - t) * pt2.Y +
                        t * t * t * pt3.Y;
            points[i] = new SKPoint(x, y);
        }

        return points;
    }

    static SKPoint[] FlattenQuadratic(SKPoint pt0, SKPoint pt1, SKPoint pt2)
    {
        int count = (int)Math.Max(1, Length(pt0, pt1) + Length(pt1, pt2));
        SKPoint[] points = new SKPoint[count];

        for (int i = 0; i < count; i++)
        {
            float t = (i + 1f) / count;
            float x = (1 - t) * (1 - t) * pt0.X + 2 * t * (1 - t) * pt1.X + t * t * pt2.X;
            float y = (1 - t) * (1 - t) * pt0.Y + 2 * t * (1 - t) * pt1.Y + t * t * pt2.Y;
            points[i] = new SKPoint(x, y);
        }

        return points;
    }

    static SKPoint[] FlattenConic(SKPoint pt0, SKPoint pt1, SKPoint pt2, float weight)
    {
        int count = (int)Math.Max(1, Length(pt0, pt1) + Length(pt1, pt2));
        SKPoint[] points = new SKPoint[count];

        for (int i = 0; i < count; i++)
        {
            float t = (i + 1f) / count;
            float denominator = (1 - t) * (1 - t) + 2 * weight * t * (1 - t) + t * t;
            float x = (1 - t) * (1 - t) * pt0.X + 2 * weight * t * (1 - t) * pt1.X + t * t * pt2.X;
            float y = (1 - t) * (1 - t) * pt0.Y + 2 * weight * t * (1 - t) * pt1.Y + t * t * pt2.Y;
            x /= denominator;
            y /= denominator;
            points[i] = new SKPoint(x, y);
        }

        return points;
    }

    static double Length(SKPoint pt0, SKPoint pt1)
    {
        return Math.Sqrt(Math.Pow(pt1.X - pt0.X, 2) + Math.Pow(pt1.Y - pt0.Y, 2));
    }
}
```

所有这些方法均通过 `CloneWithTransform` 此类中包含的扩展方法引用，如下所示。 此方法通过枚举路径命令并基于数据构造新路径来克隆路径。 但是，新路径仅包含 `MoveTo` 和 `LineTo` 调用。 所有曲线和直线都缩减为一系列小直线。

调用时 `CloneWithTransform` ，将传递到方法 a `Func<SKPoint, SKPoint>` ，这是一个具有 `SKPaint` 返回值的参数的函数 `SKPoint` 。 为每个点调用此函数以应用自定义算法转换：

```csharp
static class PathExtensions
{
    public static SKPath CloneWithTransform(this SKPath pathIn, Func<SKPoint, SKPoint> transform)
    {
        SKPath pathOut = new SKPath();

        using (SKPath.RawIterator iterator = pathIn.CreateRawIterator())
        {
            SKPoint[] points = new SKPoint[4];
            SKPathVerb pathVerb = SKPathVerb.Move;
            SKPoint firstPoint = new SKPoint();
            SKPoint lastPoint = new SKPoint();

            while ((pathVerb = iterator.Next(points)) != SKPathVerb.Done)
            {
                switch (pathVerb)
                {
                    case SKPathVerb.Move:
                        pathOut.MoveTo(transform(points[0]));
                        firstPoint = lastPoint = points[0];
                        break;

                    case SKPathVerb.Line:
                        SKPoint[] linePoints = Interpolate(points[0], points[1]);

                        foreach (SKPoint pt in linePoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        lastPoint = points[1];
                        break;

                    case SKPathVerb.Cubic:
                        SKPoint[] cubicPoints = FlattenCubic(points[0], points[1], points[2], points[3]);

                        foreach (SKPoint pt in cubicPoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        lastPoint = points[3];
                        break;

                    case SKPathVerb.Quad:
                        SKPoint[] quadPoints = FlattenQuadratic(points[0], points[1], points[2]);

                        foreach (SKPoint pt in quadPoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        lastPoint = points[2];
                        break;

                    case SKPathVerb.Conic:
                        SKPoint[] conicPoints = FlattenConic(points[0], points[1], points[2], iterator.ConicWeight());

                        foreach (SKPoint pt in conicPoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        lastPoint = points[2];
                        break;

                    case SKPathVerb.Close:
                        SKPoint[] closePoints = Interpolate(lastPoint, firstPoint);

                        foreach (SKPoint pt in closePoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        firstPoint = lastPoint = new SKPoint(0, 0);
                        pathOut.Close();
                        break;
                }
            }
        }
        return pathOut;
    }
    ...
}
```

因为克隆的路径会减少到较小的直线，所以转换函数具有将直线转换为曲线的功能。

请注意，该方法将保留名为的变量中每个轮廓的第一个点 `firstPoint` ，并且变量中每个绘图命令后的当前位置 `lastPoint` 。 当遇到谓词时，必须使用这些变量来构造最终的右行 `Close` 。

**GlobularText**示例使用此扩展方法，看起来围绕3d 效果中的半球环绕文本：

[![Globular 文本页的三向屏幕截图](information-images/globulartext-small.png)](information-images/globulartext-large.png#lightbox "Globular 文本页的三向屏幕截图")

[`GlobularTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/GlobularTextPage.cs)类构造函数执行此转换。 它 `SKPaint` 为文本创建一个对象，然后 `SKPath` 从该方法获取一个对象 `GetTextPath` 。 这是传递给 `CloneWithTransform` 扩展方法的路径以及转换函数：

```csharp
public class GlobularTextPage : ContentPage
{
    SKPath globePath;

    public GlobularTextPage()
    {
        Title = "Globular Text";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        using (SKPaint textPaint = new SKPaint())
        {
            textPaint.Typeface = SKTypeface.FromFamilyName("Times New Roman");
            textPaint.TextSize = 100;

            using (SKPath textPath = textPaint.GetTextPath("HELLO", 0, 0))
            {
                SKRect textPathBounds;
                textPath.GetBounds(out textPathBounds);

                globePath = textPath.CloneWithTransform((SKPoint pt) =>
                {
                    double longitude = (Math.PI / textPathBounds.Width) *
                                            (pt.X - textPathBounds.Left) - Math.PI / 2;
                    double latitude = (Math.PI / textPathBounds.Height) *
                                            (pt.Y - textPathBounds.Top) - Math.PI / 2;

                    longitude *= 0.75;
                    latitude *= 0.75;

                    float x = (float)(Math.Cos(latitude) * Math.Sin(longitude));
                    float y = (float)Math.Sin(latitude);

                    return new SKPoint(x, y);
                });
            }
        }
    }
    ...
}
```

转换函数首先计算两个名为 `longitude` 和 `latitude` 的值，范围为-π/2 （位于文本的顶部和左侧）到文本右侧和底部的π/2。 这些值的范围并不令人满意，因此会将它们乘以0.75。  (尝试此代码而不进行这些调整。 该文本在北和南两极中变得太难懂，而且侧面太窄了。 ) 这些三维球状坐标将 `x` 按标准公式转换为二维和 `y` 坐标。

新路径存储为字段。 然后，该 `PaintSurface` 处理程序只需将路径居中和缩放即可显示在屏幕上：

```csharp
public class GlobularTextPage : ContentPage
{
    SKPath globePath;
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint pathPaint = new SKPaint())
        {
            pathPaint.Style = SKPaintStyle.Fill;
            pathPaint.Color = SKColors.Blue;
            pathPaint.StrokeWidth = 3;
            pathPaint.IsAntialias = true;

            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.Scale(0.45f * Math.Min(info.Width, info.Height));     // radius
            canvas.DrawPath(globePath, pathPaint);
        }
    }
}
```

这是一种非常通用的方法。 如果 [**路径效果**](effects.md) 一文中所述的路径效果数组并未包含您认为应该包含的内容，则这是一种填充空白的方法。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)