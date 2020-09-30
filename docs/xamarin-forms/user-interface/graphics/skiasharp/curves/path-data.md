---
title: SkiaSharp 中的 SVG 路径数据
description: 本文介绍如何使用可缩放矢量图形格式的文本字符串来定义 SkiaSharp 路径，并使用示例代码对此进行演示。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 1D53067B-3502-4D74-B89D-7EC496901AE2
author: davidbritch
ms.author: dabritch
ms.date: 05/24/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f2e6735be2593133f755f87b365b8352bf09ebb4
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563076"
---
# <a name="svg-path-data-in-skiasharp"></a>SkiaSharp 中的 SVG 路径数据

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_使用可缩放矢量图形格式的文本字符串定义路径_

[`SKPath`](xref:SkiaSharp.SKPath)类支持通过可缩放矢量图形 (SVG) 规范所建立的格式的文本字符串来定义整个路径对象。 本文稍后会介绍如何在文本字符串中表示整个路径，例如：

![使用 SVG 路径数据定义的示例路径](path-data-images/pathdatasample.png)

SVG 是基于 XML 的 web 页面编程语言。 由于 SVG 必须允许在标记中定义路径而不是一系列函数调用，因此 SVG 标准包含一种将整个图形路径指定为文本字符串的极其简洁的方式。

在 SkiaSharp 中，此格式称为 "SVG 路径-数据"。 基于 Windows XAML 的编程环境中也支持该格式，其中包括 Windows Presentation Foundation 和通用 Windows 平台，其中称为 " [路径标记语法](/dotnet/framework/wpf/graphics-multimedia/path-markup-syntax) " 或 " [移动和绘制命令" 语法](/windows/uwp/xaml-platform/move-draw-commands-syntax/)。 它还可用作矢量图形图像的交换格式，尤其是在基于文本的文件（如 XML）中。

[`SKPath`](xref:SkiaSharp.SKPath)类定义了两个方法，其中的 `SvgPathData` 名称中包含单词：

```csharp
public static SKPath ParseSvgPathData(string svgPath)

public string ToSvgPathData()
```

静态 [`ParseSvgPathData`](xref:SkiaSharp.SKPath.ParseSvgPathData(System.String)) 方法将字符串转换为 `SKPath` 对象，同时 [`ToSvgPathData`](xref:SkiaSharp.SKPath.ToSvgPathData) 将 `SKPath` 对象转换为字符串。

下面是一个 SVG 字符串，其中有一个位于点 (0，0) 的点，半径为100：

```
"M 0 -100 L 58.8 90.9, -95.1 -30.9, 95.1 -30.9, -58.8 80.9 Z"
```

字母是用于生成对象的命令 `SKPath` ： `M` 指示 `MoveTo` 调用， `L` 为 `LineTo` ，并 `Z` `Close` 关闭轮廓。 每个数字对提供点的 X 和 Y 坐标。 请注意，该 `L` 命令后跟以逗号分隔的多个点。 在一系列坐标和点中，将以相同的方式处理逗号和空白。 某些程序员喜欢在 X 和 Y 坐标之间（而不是在点之间）加上逗号，但只需用逗号或空格来避免多义性。 这是完全合法的：

```
"M0-100L58.8 90.9-95.1-30.9 95.1-30.9-58.8 80.9Z"
```

Svg 路径数据的语法在 [svg 规范的8.3 节](https://www.w3.org/TR/SVG11/paths.html#PathData)中进行了正式记录。 下面是一个摘要：

## <a name="moveto"></a>**MoveTo**

```
M x y
```

这将通过设置当前位置在路径中开始一个新的轮廓。 路径数据应始终以命令开头 `M` 。

## <a name="lineto"></a>**LineTo**

```
L x y ...
```

此命令向路径添加一条直线 (或线条) 并将新的当前位置设置为最后一行的末尾。 可以在命令后跟 `L` 多对 *x* 和 *y* 坐标。

## <a name="horizontal-lineto"></a>**水平 LineTo**

```
H x ...
```

此命令向路径添加水平线，并将新的当前位置设置为行的末尾。 您可以在命令后跟 `H` 多个 *x* 坐标，但这并不是很有意义。

## <a name="vertical-line"></a>**竖线**

```
V y ...
```

此命令向路径添加一条竖线，并将新的当前位置设置为行的末尾。

## <a name="close"></a>**关闭**

```
Z
```

`C`命令通过添加从当前位置到等高线开头的直线来关闭等高线。

## <a name="arcto"></a>**ArcTo**

向等高线添加椭圆弧的命令是整个 SVG 路径-数据规范中最复杂的命令。 这是唯一可用于表示坐标值以外的数字的命令：

```
A rx ry rotation-angle large-arc-flag sweep-flag x y ...
```

*Rx*和*r*参数是椭圆的水平半径和垂直半径。 *旋转角度*以顺时针度为单位。

对于大弧，将 *大弧线标志* 设置为1，将小弧线设置为0。

将 " *扫描-标志* " 设置为 "1"，顺时针，将设置为 "0"。

圆弧绘制到 (*x*， *y*) 点，这将成为新的当前位置。

## <a name="cubicto"></a>**CubicTo**

```
C x1 y1 x2 y2 x3 y3 ...
```

此命令将从当前位置到 (*x3*， *y3*) 的立方贝塞尔曲线添加到新的当前位置。  (*x1*， *y1*) 和 (*x2*， *y2*) 为控制点。

单个命令可以指定多个贝塞尔曲线 `C` 。 点数必须为3的倍数。

还有一个 "平滑" 贝塞尔曲线命令：

```
S x2 y2 x3 y3 ...
```

此命令应遵循常规的 Bezier 命令 (，不过这并不是严格需要的) 。 平滑 Bezier 命令计算第一个控制点，使其成为其各个点周围的前一贝塞尔曲线的第二个控制点的反射。 因此，这三个点是 colinear 的，两个 Bezier 曲线之间的连接是平滑的。

## <a name="quadto"></a>**QuadTo**

```
Q x1 y1 x2 y2 ...
```

对于二次贝塞尔曲线，点数必须是2的倍数。 控制点为 (*x1*， *y1*) ，终点 (，新的当前位置) 为 (*x2*， *y2*) 

还有一个平滑的二次曲线命令：

```
T x2 y2 ...
```

控制点是根据上一次曲线的控制点计算的。

所有这些命令也可用于 "相对" 版本，其中坐标点相对于当前位置。 这些相对命令以小写字母开头，例如， `c` 而不是 `C` 以三次方贝塞尔命令的相对版本为例。

这是 SVG 路径数据定义的范围。 没有用于重复执行的命令组或用于执行任何类型的计算的工具。 `ConicTo`或其他类型的 arc 规范的命令均不可用。

静态 [`SKPath.ParseSvgPathData`](xref:SkiaSharp.SKPath.ParseSvgPathData(System.String)) 方法需要 SVG 命令的有效字符串。 如果检测到任何语法错误，则该方法将返回 `null` 。 这是唯一的错误指示。

[`ToSvgPathData`](xref:SkiaSharp.SKPath.ToSvgPathData)方法非常方便，可用于从现有对象获取 SVG 路径数据 `SKPath` 以传输到其他程序，或者存储为基于文本的文件格式（如 XML）。  (`ToSvgPathData` 本文的示例代码中未演示方法。 ) *不* 希望 `ToSvgPathData` 返回与创建该路径的方法调用完全对应的字符串。 特别是，您将发现该弧形转换为多个 `QuadTo` 命令，这就是它们在从返回的路径数据中的显示方式 `ToSvgPathData` 。

**路径数据 Hello**页面使用 SVG 路径数据拼写了单词 "hello"。 `SKPath`和 `SKPaint` 对象均定义为类中的字段 [`PathDataHelloPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataHelloPage.cs) ：

```csharp
public class PathDataHelloPage : ContentPage
{
    SKPath helloPath = SKPath.ParseSvgPathData(
        "M 0 0 L 0 100 M 0 50 L 50 50 M 50 0 L 50 100" +                // H
        "M 125 0 C 60 -10, 60 60, 125 50, 60 40, 60 110, 125 100" +     // E
        "M 150 0 L 150 100, 200 100" +                                  // L
        "M 225 0 L 225 100, 275 100" +                                  // L
        "M 300 50 A 25 50 0 1 0 300 49.9 Z");                           // O

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = 10,
        StrokeCap = SKStrokeCap.Round,
        StrokeJoin = SKStrokeJoin.Round
    };
    ...
}
```

定义文本字符串的路径从 (0，0) 点的左上角开始。 每个字母为50单位宽，100个单位高，字母用另外25个单位分隔，这意味着整个路径为350个单位。

"H" 由 3 1 行等高线组成，而 "E" 是两个连接的三次贝塞尔曲线。 请注意，该 `C` 命令后跟六个点，两个控制点的 y 坐标为–10和110，这会将它们放在其他字母的 y 坐标范围之外。 "L" 是两条连接的线，而 "O" 是使用命令呈现的椭圆 `A` 。

请注意， `M` 最后一个等高线的命令会将位置设置为 (350，50) 点，这是 "O" 左侧的垂直中心。 如命令后面的第一个数字所示 `A` ，椭圆的水平半径为25，垂直半径为50。 结束点通过命令中的最后一对数字进行 `A` 表示，该数字表示点 (300，49.9) 。 这与开始点特意略有不同。 如果端点设置为等于开始点，则不会呈现弧。 若要绘制完整的椭圆，你必须将终结点设置为接近 (但不等于开始点) ，或者必须使用两个或更多 `A` 命令，每个命令都适用于完整椭圆的一部分。

你可能想要将以下语句添加到页面的构造函数中，然后设置一个断点来检查生成的字符串：

```csharp
string str = helloPath.ToSvgPathData();
```

您将发现， `Q` 使用二次贝塞尔曲线使弧线的段落近似为一系列命令的弧线已被替换。

`PaintSurface`处理程序获取路径的紧密边界，该边界不包括 "E" 和 "O" 曲线的控制点。 这三种转换将路径中心移动到 (0，0) 的点，将路径缩放为画布的大小 (但同时将笔划宽度纳入) ，然后将路径中心移至画布中心：

```csharp
public class PathDataHelloPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKRect bounds;
        helloPath.GetTightBounds(out bounds);

        canvas.Translate(info.Width / 2, info.Height / 2);

        canvas.Scale(info.Width / (bounds.Width + paint.StrokeWidth),
                     info.Height / (bounds.Height + paint.StrokeWidth));

        canvas.Translate(-bounds.MidX, -bounds.MidY);

        canvas.DrawPath(helloPath, paint);
    }
}
```

该路径填充画布，在横向模式下查看时，外观更合理：

[![路径数据 Hello 页面的三向屏幕截图](path-data-images/pathdatahello-small.png)](path-data-images/pathdatahello-large.png#lightbox "路径数据 Hello 页面的三向屏幕截图")

**路径数据 Cat**页类似。 Path 和 paint 对象同时定义为类中的字段 [`PathDataCatPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataCatPage.cs) ：

```csharp
public class PathDataCatPage : ContentPage
{
    SKPath catPath = SKPath.ParseSvgPathData(
        "M 160 140 L 150 50 220 103" +              // Left ear
        "M 320 140 L 330 50 260 103" +              // Right ear
        "M 215 230 L 40 200" +                      // Left whiskers
        "M 215 240 L 40 240" +
        "M 215 250 L 40 280" +
        "M 265 230 L 440 200" +                     // Right whiskers
        "M 265 240 L 440 240" +
        "M 265 250 L 440 280" +
        "M 240 100" +                               // Head
        "A 100 100 0 0 1 240 300" +
        "A 100 100 0 0 1 240 100 Z" +
        "M 180 170" +                               // Left eye
        "A 40 40 0 0 1 220 170" +
        "A 40 40 0 0 1 180 170 Z" +
        "M 300 170" +                               // Right eye
        "A 40 40 0 0 1 260 170" +
        "A 40 40 0 0 1 300 170 Z");

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Orange,
        StrokeWidth = 5
    };
    ...
}
```

Cat 的头是一个圆圈，这里显示了两个 `A` 命令，每个命令都绘制一个半圆。 `A`头的两个命令都定义100的水平半径和垂直半径。 第一条弧从 (240，100) 开始，以 (240，300) 结束，这将成为第二个弧线的起点，该曲线将以 (240、100) 结束。

这两个眼睛还呈现有两个 `A` 命令，与 cat 头一样，第二个 `A` 命令结束于第一个命令的开头 `A` 。 但是，这些命令对 `A` 不会定义椭圆。 每个弧线的都是40单位，半径也是40个单位，这意味着这些圆弧并不完全 semicircles。

该 `PaintSurface` 处理程序将执行与上一示例类似的转换，但会设置单个 `Scale` 因素来保持纵横比并提供较小的边距，使 cat 的须线不触及屏幕的各边：

```csharp
public class PathDataCatPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Black);

        SKRect bounds;
        catPath.GetBounds(out bounds);

        canvas.Translate(info.Width / 2, info.Height / 2);

        canvas.Scale(0.9f * Math.Min(info.Width / bounds.Width,
                                     info.Height / bounds.Height));

        canvas.Translate(-bounds.MidX, -bounds.MidY);

        canvas.DrawPath(catPath, paint);
    }
}
```

下面是正在运行的程序：

[![路径数据 Cat 页面的三向屏幕截图](path-data-images/pathdatacat-small.png)](path-data-images/pathdatacat-large.png#lightbox "路径数据 Cat 页面的三向屏幕截图")

通常，当 `SKPath` 对象定义为字段时，必须在构造函数或其他方法中定义路径的轮廓。 但是，在使用 SVG 路径数据时，您已看到可以完全在字段定义中指定路径。

"[**旋转转换**](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/rotate.md)" 一文中较早的实用**模拟时钟**示例显示了时钟作为简单线条的手。 下面这个非常好的 **模拟时钟** 程序用 `SKPath` 定义为类中的字段的对象 [`PrettyAnalogClockPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PrettyAnalogClockPage.cs) 和 `SKPaint` 对象替换这些行：

```csharp
public class PrettyAnalogClockPage : ContentPage
{
    ...
    // Clock hands pointing straight up
    SKPath hourHandPath = SKPath.ParseSvgPathData(
        "M 0 -60 C   0 -30 20 -30  5 -20 L  5   0" +
                "C   5 7.5 -5 7.5 -5   0 L -5 -20" +
                "C -20 -30  0 -30  0 -60 Z");

    SKPath minuteHandPath = SKPath.ParseSvgPathData(
        "M 0 -80 C   0 -75  0 -70  2.5 -60 L  2.5   0" +
                "C   2.5 5 -2.5 5 -2.5   0 L -2.5 -60" +
                "C 0 -70  0 -75  0 -80 Z");

    SKPath secondHandPath = SKPath.ParseSvgPathData(
        "M 0 10 L 0 -80");

    // SKPaint objects
    SKPaint handStrokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 2,
        StrokeCap = SKStrokeCap.Round
    };

    SKPaint handFillPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Gray
    };
    ...
}
```

现在，小时和分钟都包含区域。 若要使这些指针彼此不同，则使用和对象绘制黑色轮廓和灰色填充 `handStrokePaint` `handFillPaint` 。

在前面的较小的 **模拟时钟** 示例中，标记为小时和分钟的小圆圈是在循环中绘制的。 在这个 **非常好的模拟时钟** 示例中，使用了完全不同的方法：小时和分钟标记是使用和对象绘制的点线 `minuteMarkPaint` `hourMarkPaint` ：

```csharp
public class PrettyAnalogClockPage : ContentPage
{
    ...
    SKPaint minuteMarkPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 3,
        StrokeCap = SKStrokeCap.Round,
        PathEffect = SKPathEffect.CreateDash(new float[] { 0, 3 * 3.14159f }, 0)
    };

    SKPaint hourMarkPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 6,
        StrokeCap = SKStrokeCap.Round,
        PathEffect = SKPathEffect.CreateDash(new float[] { 0, 15 * 3.14159f }, 0)
    };
    ...
}
```

[**点和短划线**](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md)文章讨论了如何使用 [`SKPathEffect.CreateDash`](xref:SkiaSharp.SKPathEffect.CreateDash*) 方法创建虚线。 第一个参数是一个 `float` 通常包含两个元素的数组：第一个元素是短划线的长度，第二个元素是短划线之间的间隔。 当 `StrokeCap` 属性设置为时 `SKStrokeCap.Round` ，短划线的圆角会有效地将虚线长度延长虚线的两边。 因此，将第一个数组元素设置为0会创建一条虚线。

这些点之间的距离由第二个数组元素控制。 正如您很快就会看到的，这两个 `SKPaint` 对象用于绘制半径为90单位的圆。 因此，此圆的周长为180π，这意味着每个3π单位都必须出现60分钟标记，这是中数组中的第二个值 `float` `minuteMarkPaint` 。 12小时标记必须出现在每个15π单位上，这是第二个数组中的值 `float` 。

`PrettyAnalogClockPage`类设置计时器每16毫秒使图面无效，并 `PaintSurface` 按该速率调用处理程序。 和对象的早期定义 `SKPath` `SKPaint` 允许非常简洁的绘图代码：

```csharp
public class PrettyAnalogClockPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Transform for 100-radius circle in center
        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(Math.Min(info.Width / 200, info.Height / 200));

        // Draw circles for hour and minute marks
        SKRect rect = new SKRect(-90, -90, 90, 90);
        canvas.DrawOval(rect, minuteMarkPaint);
        canvas.DrawOval(rect, hourMarkPaint);

        // Get time
        DateTime dateTime = DateTime.Now;

        // Draw hour hand
        canvas.Save();
        canvas.RotateDegrees(30 * dateTime.Hour + dateTime.Minute / 2f);
        canvas.DrawPath(hourHandPath, handStrokePaint);
        canvas.DrawPath(hourHandPath, handFillPaint);
        canvas.Restore();

        // Draw minute hand
        canvas.Save();
        canvas.RotateDegrees(6 * dateTime.Minute + dateTime.Second / 10f);
        canvas.DrawPath(minuteHandPath, handStrokePaint);
        canvas.DrawPath(minuteHandPath, handFillPaint);
        canvas.Restore();

        // Draw second hand
        double t = dateTime.Millisecond / 1000.0;

        if (t < 0.5)
        {
            t = 0.5 * Easing.SpringIn.Ease(t / 0.5);
        }
        else
        {
            t = 0.5 * (1 + Easing.SpringOut.Ease((t - 0.5) / 0.5));
        }

        canvas.Save();
        canvas.RotateDegrees(6 * (dateTime.Second + (float)t));
        canvas.DrawPath(secondHandPath, handStrokePaint);
        canvas.Restore();
    }
}
```

不过，这是一个特殊的操作。 由于时钟每16毫秒更新一次，因此可能 `Millisecond` `DateTime` 会使用值的属性来对扫描的第二个手势（而不是从第二个移动到第二个的连续跳转）进行动画处理。 但此代码不允许移动平滑。 相反，它使用 Xamarin.Forms [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn) 和 [`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut) 动画缓动函数进行不同类型的移动。 这些缓动函数导致第二个局以 jerkier 的方式进行移动，然后在 &mdash; 移动之前将其拉出，然后稍稍超出了其目标，这种效果在这些静态屏幕截图中无法再现：

[![相当的模拟时钟页的三向屏幕截图](path-data-images/prettyanalogclock-small.png)](path-data-images/prettyanalogclock-large.png#lightbox "相当的模拟时钟页的三向屏幕截图")

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)