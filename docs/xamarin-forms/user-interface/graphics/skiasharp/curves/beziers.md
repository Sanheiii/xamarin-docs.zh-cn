---
title: 贝塞尔曲线的三种类型
description: 本文介绍如何使用 SkiaSharp 在应用程序中呈现三次方贝塞尔曲线，并使用 Xamarin.Forms 示例代码对此进行演示。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 8FE0F6DC-16BC-435F-9626-DD1790C0145A
author: davidbritch
ms.author: dabritch
ms.date: 05/25/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9193cef76a5f474f3681b15a1315e5840b41d88a
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562972"
---
# <a name="three-types-of-bzier-curves"></a>贝塞尔曲线的三种类型

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_了解如何使用 SkiaSharp 呈现立方、二次和圆锥次贝塞尔曲线_

贝塞尔曲线在圣皮埃尔贝赛尔 (1910 – 1999) ，即汽车公司 Renault 的法语工程，其使用汽车的计算机辅助设计的曲线。

贝塞尔曲线非常适用于交互式设计：它们的行为非常好，也就是 &mdash; singularities 的，它们不会导致曲线变得无限大或不实用， &mdash; 并且通常具。

![样本贝塞尔曲线](beziers-images/beziersample.png)

基于计算机的字体的字符轮廓通常定义为贝塞尔曲线。

[**贝塞尔曲线**](https://en.wikipedia.org/wiki/B%C3%A9zier_curve)上的维基百科文章包含一些有用的背景信息。 术语 " *贝塞尔曲线* " 实际上指的是一系列相似的曲线。 SkiaSharp 支持三种类型的贝赛尔曲线，称为 *三*类、 *二*次和 *圆锥*。 圆锥也称为 *有理数二次*。

## <a name="the-cubic-bzier-curve"></a>三次方贝塞尔曲线

立方米是大多数开发人员在 Bezier 曲线出现时的情况的类型。

可以 `SKPath` 通过使用 [`CubicTo`](xref:SkiaSharp.SKPath.CubicTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,SkiaSharp.SKPoint)) 带有三个参数的方法 `SKPoint` 或 [`CubicTo`](xref:SkiaSharp.SKPath.CubicTo(System.Single,System.Single,System.Single,System.Single,System.Single,System.Single)) 具有单独 `x` 参数和参数的重载， `y` 将三次贝塞尔曲线添加到对象中：

```csharp
public void CubicTo (SKPoint point1, SKPoint point2, SKPoint point3)

public void CubicTo (Single x1, Single y1, Single x2, Single y2, Single x3, Single y3)
```

曲线从轮廓的当前点开始。 完整的三次方贝塞尔曲线由四个点定义：

- 起点：等高线中的当前点，或者 (0，0) 如果尚未 `MoveTo` 调用
- 第一个控制点： `point1` 在 `CubicTo` 调用中
- 第二个控制点： `point2` 在 `CubicTo` 调用中
- 终结点： `point3` 在 `CubicTo` 呼叫中

生成的曲线从起点开始，到终点结束。 曲线通常不通过两个控制点;相反，控制点的工作方式非常类似于磁体来向它们拉动曲线。

获得三次方贝塞尔曲线的最佳方式是试验。 这是从派生的 **贝塞尔曲线** 页面的用途 `InteractivePage` 。 [**BezierCurvePage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCurvePage.xaml)文件实例化 `SKCanvasView` 和 `TouchEffect` 。 [**BezierCurvePage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCurvePage.xaml.cs)代码隐藏文件 `TouchPoint` 在其构造函数中创建四个对象。 `PaintSurface`事件处理程序创建一个 `SKPath` 来基于四个对象呈现贝塞尔曲线 `TouchPoint` ，还绘制从控件点到终点的点线切线：

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
    }

    // Draw tangent lines
    canvas.DrawLine(touchPoints[0].Center.X,
                    touchPoints[0].Center.Y,
                    touchPoints[1].Center.X,
                    touchPoints[1].Center.Y, dottedStrokePaint);

    canvas.DrawLine(touchPoints[2].Center.X,
                    touchPoints[2].Center.Y,
                    touchPoints[3].Center.X,
                    touchPoints[3].Center.Y, dottedStrokePaint);

    foreach (TouchPoint touchPoint in touchPoints)
    {
       touchPoint.Paint(canvas);
    }
}
```

此处运行的是：

[![贝塞尔曲线页面的三向屏幕截图](beziers-images/beziercurve-small.png)](beziers-images/beziercurve-large.png#lightbox)

从数学上来说，曲线是一个三个多项式。 曲线在最多三个点相交一条直线。 在起始点，曲线始终以与从起点到第一个控制点的直线相同的方向进行相切。 在终结点，曲线始终与第二个控制点到终点的直线相切，并以与之相同的方向。

三次点连接三个点，三次方贝塞尔曲线总是由凸四边形包围。 这称为凸形 *凸*形。 如果控制点位于起点和终点之间的直线上，则 Bezier 曲线将呈现为一条直线。 除了第三个屏幕截图所示，曲线还可以交叉。

路径等高线可包含多个连接的三次贝塞尔曲线，但只有在以下三个点 (colinear 时，两个立方贝塞尔曲线之间的连接才会平滑) ：

- 第一条曲线的第二个控制点
- 第一条曲线的终点，它也是第二条曲线的起点
- 第二条曲线的第一个控制点

在下一篇文章中，你将发现 [**一项可**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md)简化平滑连接的 bezier 曲线定义的工具。

有时，了解呈现三次贝塞尔曲线的基础参数方程会很有用。 对于 *t* （范围从0到1），参数公式如下所示：

x (t) = (1 – t) ³ x ₀ + 3t (1 – t) ² x ₁ + 3t ² (1 – t) x ₂ + t ³ x ₃

y (t) = (1 – t) ³ y ₀ + 3t (1 – t) ² y ₁ + 3t ² (1 – t) y ₂ + t ³ y ₃

最高的指数（3）确认了三个 polynomials。 当 `t` 等于0时，点是 (x ₀，y ₀) ，即开始点，而当 `t` 等于1时，点是 (x ₃，y ₃) ，这是终点。 接近开始点 () 低值 `t` 后，第一个控制点 (x ₁，y ₁) 有一个明显的效果，接近结束点 (值 "t" )  (x ₂，y ₂) 的第二个控制点会有很大影响。

## <a name="bezier-curve-approximation-to-circular-arcs"></a>贝塞尔曲线近似于圆弧

有时使用 Bezier 曲线来渲染圆弧会很方便。一条三次贝塞尔曲线可以使圆弧非常接近于一个季度圆，因此，四个已连接的贝塞尔曲线可以定义一个圆形。 这项近似值在25年前发布的两篇文章中进行了讨论：

> Tor Dokken，al al，"按曲率准确的圆圈-连续贝塞尔曲线"，" *计算机辅助几何设计 7* (1990) ，33-41。

> Michael Goldapp "Polynomials，" *计算机辅助几何设计 8* (1991) ，227-238。

下图显示了四个标记为、和的点， `pto` `pt1` `pt2` 并 `pt3` 定义了一条贝塞尔曲线 (以红色) 显示，该曲线近似为圆弧：

![具有贝塞尔曲线的圆弧的近似值](beziers-images/bezierarc45.png)

起点和终点到控制点的直线分别与圆圈和贝塞尔曲线相切，而且其长度为 *L*。上面提到的第一篇文章指出，在计算此长度 *L* 时，贝塞尔曲线最接近圆弧：

L = 4 × tan (α/4) /3

下图显示45度的角度，因此 L 等于0.265。 在代码中，该值将乘以所需的圆圈半径。

使用 " **贝塞尔圆弧** " 页，您可以尝试定义一条贝塞尔曲线，以使圆圆弧的角度接近于180度。 [**BezierCircularArcPage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCircularArcPage.xaml)文件实例化 `SKCanvasView` 和， `Slider` 以选择角度。 `PaintSurface` [**BezierCircularArgPage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCircularArcPage.xaml.cs)代码隐藏文件中的事件处理程序使用转换将 (0，0) 的点设置为画布的中心。 它在该点上绘制一个围绕比较的圆，然后计算贝塞尔曲线的两个控制点：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Translate to center
    canvas.Translate(info.Width / 2, info.Height / 2);

    // Draw the circle
    float radius = Math.Min(info.Width, info.Height) / 3;
    canvas.DrawCircle(0, 0, radius, blackStroke);

    // Get the value of the Slider
    float angle = (float)angleSlider.Value;

    // Calculate length of control point line
    float length = radius * 4 * (float)Math.Tan(Math.PI * angle / 180 / 4) / 3;

    // Calculate sin and cosine for half that angle
    float sin = (float)Math.Sin(Math.PI * angle / 180 / 2);
    float cos = (float)Math.Cos(Math.PI * angle / 180 / 2);

    // Find the end points
    SKPoint point0 = new SKPoint(-radius * sin, radius * cos);
    SKPoint point3 = new SKPoint(radius * sin, radius * cos);

    // Find the control points
    SKPoint point0Normalized = Normalize(point0);
    SKPoint point1 = point0 + new SKPoint(length * point0Normalized.Y,
                                          -length * point0Normalized.X);

    SKPoint point3Normalized = Normalize(point3);
    SKPoint point2 = point3 + new SKPoint(-length * point3Normalized.Y,
                                          length * point3Normalized.X);

    // Draw the points
    canvas.DrawCircle(point0.X, point0.Y, 10, blackFill);
    canvas.DrawCircle(point1.X, point1.Y, 10, blackFill);
    canvas.DrawCircle(point2.X, point2.Y, 10, blackFill);
    canvas.DrawCircle(point3.X, point3.Y, 10, blackFill);

    // Draw the tangent lines
    canvas.DrawLine(point0.X, point0.Y, point1.X, point1.Y, dottedStroke);
    canvas.DrawLine(point3.X, point3.Y, point2.X, point2.Y, dottedStroke);

    // Draw the Bezier curve
    using (SKPath path = new SKPath())
    {
        path.MoveTo(point0);
        path.CubicTo(point1, point2, point3);
        canvas.DrawPath(path, redStroke);
    }
}

// Vector methods
SKPoint Normalize(SKPoint v)
{
    float magnitude = Magnitude(v);
    return new SKPoint(v.X / magnitude, v.Y / magnitude);
}

float Magnitude(SKPoint v)
{
    return (float)Math.Sqrt(v.X * v.X + v.Y * v.Y);
}

```

 (和) 的起点和 `point0` 终点 `point3` 基于圆圈的普通参数方程进行计算。 由于圆圈位于 (0，0) ，因此也可以将这些点视为从圆圈中心到圆周的放射向量。 控制点位于与圆圈相切的行上，因此它们与这些辐射向量是直角的。 与另一个坐标相反的向量只是交换了 X 和 Y 坐标的原始向量，其中一个坐标为负数。

下面是以不同角度运行的程序：

[![贝塞尔圆弧页面的三向屏幕截图](beziers-images/beziercirculararc-small.png)](beziers-images/beziercirculararc-large.png#lightbox)

仔细查看第三个屏幕截图，你会发现，当角度为180度时，Bezier 曲线大致偏离半圆，但 iOS 屏幕显示，当角度为90度时，它看起来像是一个四分之一圆圈。

当季度圆圈的方向如下所示时，计算两个控制点的坐标非常简单：

![贝塞尔曲线的近似值](beziers-images/bezierarc90.png)

如果圆的半径为100，则 *L* 为55，这是一个容易记住的数字。

" **圆** " 页将在圆与正方形之间对图形进行动画处理。 该圆圈由四个 Bezier 曲线近似，其坐标显示在类的此数组定义的第一列中 [`SquaringTheCirclePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/SquaringTheCirclePage.cs) ：

```csharp
public class SquaringTheCirclePage : ContentPage
{
    SKPoint[,] points =
    {
        { new SKPoint(   0,  100), new SKPoint(     0,    125), new SKPoint() },
        { new SKPoint(  55,  100), new SKPoint( 62.5f,  62.5f), new SKPoint() },
        { new SKPoint( 100,   55), new SKPoint( 62.5f,  62.5f), new SKPoint() },
        { new SKPoint( 100,    0), new SKPoint(   125,      0), new SKPoint() },
        { new SKPoint( 100,  -55), new SKPoint( 62.5f, -62.5f), new SKPoint() },
        { new SKPoint(  55, -100), new SKPoint( 62.5f, -62.5f), new SKPoint() },
        { new SKPoint(   0, -100), new SKPoint(     0,   -125), new SKPoint() },
        { new SKPoint( -55, -100), new SKPoint(-62.5f, -62.5f), new SKPoint() },
        { new SKPoint(-100,  -55), new SKPoint(-62.5f, -62.5f), new SKPoint() },
        { new SKPoint(-100,    0), new SKPoint(  -125,      0), new SKPoint() },
        { new SKPoint(-100,   55), new SKPoint(-62.5f,  62.5f), new SKPoint() },
        { new SKPoint( -55,  100), new SKPoint(-62.5f,  62.5f), new SKPoint() },
        { new SKPoint(   0,  100), new SKPoint(     0,    125), new SKPoint() }
    };
    ...
}
```

第二列包含四个贝赛尔曲线的坐标，该曲线定义其面积大致与圆圈面积大致相同的正方形。  (绘制一个正方形，使其与给定圆圈 *完全* 相同，就是无法解决的典型几何 [问题。 ) ](https://en.wikipedia.org/wiki/Squaring_the_circle)用于呈现带有 bezier 曲线的正方形，每条曲线的两个控制点是相同的，它们与起点和终点 colinear，因此 bezier 曲线呈现为一条直线。

数组的第三列适用于动画的内插值。 该页设置了16毫秒的计时器，并 `PaintSurface` 按该速率调用了该处理程序：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    canvas.Translate(info.Width / 2, info.Height / 2);
    canvas.Scale(Math.Min(info.Width / 300, info.Height / 300));

    // Interpolate
    TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
    float t = (float)(timeSpan.TotalSeconds % 3 / 3);   // 0 to 1 every 3 seconds
    t = (1 + (float)Math.Sin(2 * Math.PI * t)) / 2;     // 0 to 1 to 0 sinusoidally

    for (int i = 0; i < 13; i++)
    {
        points[i, 2] = new SKPoint(
            (1 - t) * points[i, 0].X + t * points[i, 1].X,
            (1 - t) * points[i, 0].Y + t * points[i, 1].Y);
    }

    // Create the path and draw it
    using (SKPath path = new SKPath())
    {
        path.MoveTo(points[0, 2]);

        for (int i = 1; i < 13; i += 3)
        {
            path.CubicTo(points[i, 2], points[i + 1, 2], points[i + 2, 2]);
        }
        path.Close();

        canvas.DrawPath(path, cyanFill);
        canvas.DrawPath(path, blueStroke);
    }
}
```

根据的 sinusoidally 振荡值将点插值 `t` 。 然后，将使用内插点来构造一系列四次连接的贝塞尔曲线。 下面是运行的动画：

[![求圆页面](beziers-images/squaringthecircle-small.png)](beziers-images/squaringthecircle-large.png#lightbox)

此类动画将无法使用算法灵活的曲线来呈现为圆弧和直线。

**贝塞尔无穷**页还利用贝塞尔曲线的功能来接近圆弧。下面是 `PaintSurface` 来自类的处理程序 [`BezierInfinityPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierInfinityPage.cs) ：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPath path = new SKPath())
    {
        path.MoveTo(0, 0);                                // Center
        path.CubicTo(  50,  -50,   95, -100,  150, -100); // To top of right loop
        path.CubicTo( 205, -100,  250,  -55,  250,    0); // To far right of right loop
        path.CubicTo( 250,   55,  205,  100,  150,  100); // To bottom of right loop
        path.CubicTo(  95,  100,   50,   50,    0,    0); // Back to center  
        path.CubicTo( -50,  -50,  -95, -100, -150, -100); // To top of left loop
        path.CubicTo(-205, -100, -250,  -55, -250,    0); // To far left of left loop
        path.CubicTo(-250,   55, -205,  100, -150,  100); // To bottom of left loop
        path.CubicTo( -95,  100,  -50,   50,    0,    0); // Back to center
        path.Close();

        SKRect pathBounds = path.Bounds;
        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(0.9f * Math.Min(info.Width / pathBounds.Width,
                                     info.Height / pathBounds.Height));

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Blue;
            paint.StrokeWidth = 5;

            canvas.DrawPath(path, paint);
        }
    }
}
```

最好在图形纸上绘制这些坐标，以查看它们之间的关系。 无穷号围绕 (0，0) 点中心，两个循环的中心为 ( –150，0) ， (150，0) ，半径为100。 在一系列 `CubicTo` 命令中，可以看到对值为–95和– 205 (的控制点的 X 坐标。这些值为–150加上和减去 55) 、205和 95 (150 + 和减号 55) 以及250和–250。 唯一的例外是在中心内的无穷符号上交叉。 在这种情况下，控制点具有50和–50组合的坐标，以使曲线在中心附近的位置伸直。

这是无限大符号：

[![贝塞尔无穷页的三向屏幕截图](beziers-images/bezierinfinity-small.png)](beziers-images/bezierinfinity-large.png#lightbox)

与 " **圆弧无穷** " 页面呈现的无穷符号相比，从 [**三种方法绘制弧形**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md) 文章中，它的距离略有不同。

## <a name="the-quadratic-bzier-curve"></a>二次贝塞尔曲线

二次贝塞尔曲线只有一个控制点，而曲线仅由三个点定义：起点、控制点和终点。 参数方程与三次方贝塞尔曲线非常相似，不同之处在于最高指数为2，因此曲线为二次多项式：

x (t) = (1 – t) ² x ₀ + 2t (1 – t) x ₁ + t ² x ₂

y (t) = (1 – t) ² y ₀ + 2t (1 – t) y ₁ + t ² y ₂

若要将二次贝塞尔曲线添加到路径中，请使用 [`QuadTo`](xref:SkiaSharp.SKPath.QuadTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint)) 方法或 [`QuadTo`](xref:SkiaSharp.SKPath.QuadTo(System.Single,System.Single,System.Single,System.Single)) 具有单独 `x` 坐标和坐标的重载 `y` ：

```csharp
public void QuadTo (SKPoint point1, SKPoint point2)

public void QuadTo (Single x1, Single y1, Single x2, Single y2)
```

方法将从当前位置到的曲线 `point2` 作为控制点添加到 `point1` 。

您可以用 **二次曲线** 页面试验二次贝塞尔曲线，这与 **贝塞尔曲线** 页面非常相似，只不过它只有三个触点。 下面是 `PaintSurface` [**QuadraticCurve.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/QuadraticCurvePage.xaml.cs) 代码隐藏文件中的处理程序：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Draw path with quadratic Bezier
    using (SKPath path = new SKPath())
    {
        path.MoveTo(touchPoints[0].Center);
        path.QuadTo(touchPoints[1].Center,
                    touchPoints[2].Center);

        canvas.DrawPath(path, strokePaint);
    }

    // Draw tangent lines
    canvas.DrawLine(touchPoints[0].Center.X,
                    touchPoints[0].Center.Y,
                    touchPoints[1].Center.X,
                    touchPoints[1].Center.Y, dottedStrokePaint);

    canvas.DrawLine(touchPoints[1].Center.X,
                    touchPoints[1].Center.Y,
                    touchPoints[2].Center.X,
                    touchPoints[2].Center.Y, dottedStrokePaint);

    foreach (TouchPoint touchPoint in touchPoints)
    {
        touchPoint.Paint(canvas);
    }
}
```

这里，它正在运行：

[![二次曲线页面的三向屏幕截图](beziers-images/quadraticcurve-small.png)](beziers-images/quadraticcurve-large.png#lightbox)

虚线与起点和终点的曲线相切，并在控制点处相交。

如果您需要一个常规形状的曲线，但您更喜欢只是一个控制点而不是两个控制点，则二次贝塞尔曲线就好了。 与任何其他曲线相比，二次 Bezier 的呈现效率更高，这就是在 Skia 内部使用它来呈现椭圆弧的原因。

但是，二次贝塞尔曲线的形状不是椭圆形，这就是为什么需要多次二次 Béziers 来接近椭圆弧。次贝塞尔是 parabola 的一段。

## <a name="the-conic-bzier-curve"></a>圆锥贝塞尔曲线

圆锥贝赛尔曲线 &mdash; 也称为 "有理二次贝塞尔曲线" &mdash; ，它是一种相对最近的贝塞尔曲线系列。 与二次贝塞尔曲线一样，有理数二次贝塞尔曲线涉及到起点、终点和一个控制点。 但有理二次贝塞尔曲线还需要一个 *权重* 值。 它被称为 *有理数* ，因为参数公式涉及比率。

X 和 Y 的参数方程是共享同一分母的比率。 下面是 *t* 的分母的公式，范围为0到1，权重值为 *w*：

d (t) = (1 – t) ² + 2wt (1 – t) + t ²

理论上，有理数会涉及三个不同的权重值，其中每个值对应于这三个术语中的一个，但在中间术语上只会将这些值简化为一个权重值。

X 和 Y 坐标的参数方程类似于二次贝塞尔的参数公式，不同之处在于中间术语还包括权重值，表达式除以分母：

x (t) = ( # B3 1 – t) ² x ₀ + 2wt (1 – t) x ₁ + \t\t x ₂) # A8 ÷ d () 

y (t) = ( # B3 1 – t) ²₀ + 2wt (1 – t) y ₁ + t ² y ₂) # A8 ÷ d (

合理的二次贝塞尔曲线也称为 *conics* ，因为它们可以精确表示任意圆锥部分的 &mdash; hyperbolas、parabolas、省略号和圆圈段。

若要将有理二次贝塞尔曲线添加到路径中，请使用 [`ConicTo`](xref:SkiaSharp.SKPath.ConicTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,System.Single)) 方法或 [`ConicTo`](xref:SkiaSharp.SKPath.ConicTo(System.Single,System.Single,System.Single,System.Single,System.Single)) 具有单独 `x` 坐标和坐标的重载 `y` ：

```csharp
public void ConicTo (SKPoint point1, SKPoint point2, Single weight)

public void ConicTo (Single x1, Single y1, Single x2, Single y2, Single weight)
```

请注意最后一个 `weight` 参数。

" **圆锥曲线** " 页允许您试验这些曲线。 `ConicCurvePage` 类从 `InteractivePage` 派生。 [**ConicCurvePage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCurvePage.xaml)文件实例化 `Slider` ，以选择–2到2之间的权重值。 [**ConicCurvePage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCurvePage.xaml.cs)代码隐藏文件会创建三个 `TouchPoint` 对象，并且 `PaintSurface` 处理程序只会将具有切线的结果曲线渲染到控制点：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Draw path with conic curve
    using (SKPath path = new SKPath())
    {
        path.MoveTo(touchPoints[0].Center);
        path.ConicTo(touchPoints[1].Center,
                     touchPoints[2].Center,
                     (float)weightSlider.Value);

        canvas.DrawPath(path, strokePaint);
    }

    // Draw tangent lines
    canvas.DrawLine(touchPoints[0].Center.X,
                    touchPoints[0].Center.Y,
                    touchPoints[1].Center.X,
                    touchPoints[1].Center.Y, dottedStrokePaint);

    canvas.DrawLine(touchPoints[1].Center.X,
                    touchPoints[1].Center.Y,
                    touchPoints[2].Center.X,
                    touchPoints[2].Center.Y, dottedStrokePaint);

    foreach (TouchPoint touchPoint in touchPoints)
    {
        touchPoint.Paint(canvas);
    }
}
```

此处运行的是：

[![圆锥曲线页面的三向屏幕截图](beziers-images/coniccurve-small.png)](beziers-images/coniccurve-large.png#lightbox)

正如您所看到的，当权重较高时，控制点似乎会将曲线拉向其更多。 当权重为零时，曲线将变为从起点到终点的直线。

理论上，允许负权重，并使曲线 *远离* 控制点。 但是，-1 或更低的权重会导致参数方程中的分母对于 *t*的特定值为负。 此原因可能会忽略方法中的负值 `ConicTo` 。 使用 **圆锥曲线** 计划可以设置负值，但正如您通过试验所看到的那样，负权重具有与零权重相同的效果，并导致呈现直线。

可以很容易地通过派生控制点和权重来使用 `ConicTo` 方法将圆弧绘制到 (，但不包括半圆) 。 在下图中，起点和终点之间的相切线在控制点处满足。

![圆弧的圆锥弧线渲染](beziers-images/conicarc.png)

您可以使用三角函数来确定控制点与圆心中心之间的距离：该圆形的半径除以α角度的余弦值。 若要在起点和终点之间绘制圆弧，请将权重设置为该角度的一半的余弦值。 请注意，如果角度为180度，则相切线条从不满足且权重为零。 但对于小于180度的角度，数学计算工作正常。

" **圆锥** 圆弧" 页演示了这一点。 [**ConicCircularArc**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCircularArcPage.xaml)文件实例化一个 `Slider` 用于选择角度的。 `PaintSurface` [**ConicCircularArc.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCircularArcPage.xaml.cs)代码隐藏文件中的处理程序计算控制点和权重：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Translate to center
    canvas.Translate(info.Width / 2, info.Height / 2);

    // Draw the circle
    float radius = Math.Min(info.Width, info.Height) / 4;
    canvas.DrawCircle(0, 0, radius, blackStroke);

    // Get the value of the Slider
    float angle = (float)angleSlider.Value;

    // Calculate sin and cosine for half that angle
    float sin = (float)Math.Sin(Math.PI * angle / 180 / 2);
    float cos = (float)Math.Cos(Math.PI * angle / 180 / 2);

    // Find the points and weight
    SKPoint point0 = new SKPoint(-radius * sin, radius * cos);
    SKPoint point1 = new SKPoint(0, radius / cos);
    SKPoint point2 = new SKPoint(radius * sin, radius * cos);
    float weight = cos;

    // Draw the points
    canvas.DrawCircle(point0.X, point0.Y, 10, blackFill);
    canvas.DrawCircle(point1.X, point1.Y, 10, blackFill);
    canvas.DrawCircle(point2.X, point2.Y, 10, blackFill);

    // Draw the tangent lines
    canvas.DrawLine(point0.X, point0.Y, point1.X, point1.Y, dottedStroke);
    canvas.DrawLine(point2.X, point2.Y, point1.X, point1.Y, dottedStroke);

    // Draw the conic
    using (SKPath path = new SKPath())
    {
        path.MoveTo(point0);
        path.ConicTo(point1, point2, weight);
        canvas.DrawPath(path, redStroke);
    }
}
```

正如您所看到的那样， `ConicTo` 显示为红色的路径与显示为引用的基础圆圈之间没有直观的区别：

[![圆锥圆弧页面的三向屏幕截图](beziers-images/coniccirculararc-small.png)](beziers-images/coniccirculararc-large.png#lightbox)

但将角度设置为180度，而数学则会失败。

这种情况在这种情况下不 `ConicTo` 支持负权重，因为在理论上 (基于参数方程) ，因此可以通过对 `ConicTo` 具有相同点但权重为负值的另一次调用来完成圆圈。 这样，就可以根据 `ConicTo` (之间的任意角度（但不包括) 零度和180度），创建一个包含两条曲线的圆。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)