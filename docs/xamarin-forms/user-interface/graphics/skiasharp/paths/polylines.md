---
title: 折线和参数等式
description: 本文介绍如何使用 SkiaSharp 呈现可以使用参数化公式定义的任何行，并使用示例代码对此进行演示。
ms.prod: xamarin
ms.assetid: 85AEBB33-E954-4364-A6E1-808FAB197BEE
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 75f6f9b3e7a493121b8f4dacb87749d4a9616dee
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91557096"
---
# <a name="polylines-and-parametric-equations"></a>折线和参数等式

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_使用 SkiaSharp 呈现可以使用参数化公式定义的任何行_

在本指南的 " [**SkiaSharp 曲线和路径**](../curves/index.md) " 部分中，您将看到 [`SKPath`](xref:SkiaSharp.SKPath) 定义用于呈现特定曲线类型的各种方法。 但是，有时需要绘制不直接支持的曲线类型 `SKPath` 。 在这种情况下，您可以使用折线 (一系列连接的直线) 绘制您可以数学定义的任何曲线。 如果使线条足够小并且足够大，结果将显示为曲线。 此螺旋实际上是3600小行：

![螺旋](polylines-images/spiralexample.png)

通常，最好按照一对参数方程来定义曲线。 它们是 X 和 Y 坐标的等式，它们依赖于第三个变量（有时称为 "时间"） `t` 。 例如，以下参数公式定义一个圆，其半径为1，介于0和 *1 之间 (* 0，0) ：

`x = cos(2πt)`

`y = sin(2πt)`

 如果希望半径大于1，则只需将正弦值与余弦值相乘即可，如果需要将中心移动到另一个位置，请添加这些值：

`x = xCenter + radius·cos(2πt)`

`y = yCenter + radius·sin(2πt)`

对于其轴平行于水平和垂直的椭圆，将涉及两个半径：

`x = xCenter + xRadius·cos(2πt)`

`y = yCenter + yRadius·sin(2πt)`

然后，你可以将等效的 SkiaSharp 代码放在一个循环中，该循环计算各个点并将这些点添加到一个路径中。 下面的 SkiaSharp 代码将 `SKPath` 为填充显示图面的椭圆创建一个对象。 循环直接循环使用360度。 中心是显示图面的宽度和高度的一半，因此，这两个半径如下：

```csharp
SKPath path = new SKPath();

for (float angle = 0; angle < 360; angle += 1)
{
    double radians = Math.PI * angle / 180;
    float x = info.Width / 2 + (info.Width / 2) * (float)Math.Cos(radians);
    float y = info.Height / 2 + (info.Height / 2) * (float)Math.Sin(radians);

    if (angle == 0)
    {
        path.MoveTo(x, y);
    }
    else
    {
        path.LineTo(x, y);
    }
}
path.Close();
```

这会生成一个由360小直线定义的椭圆。 呈现时，它看起来平滑。

当然，您无需使用折线创建椭圆，因为其中 `SKPath` 包括 `AddOval` 用于执行此过程的方法。 但您可能需要绘制一个不由提供的视觉对象 `SKPath` 。

**Archimedean 螺旋线**页面包含类似于椭圆代码的代码，但差别很重。 它会围绕圆的360度循环10次，并持续调整半径：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
    float radius = Math.Min(center.X, center.Y);

    using (SKPath path = new SKPath())
    {
        for (float angle = 0; angle < 3600; angle += 1)
        {
            float scaledRadius = radius * angle / 3600;
            double radians = Math.PI * angle / 180;
            float x = center.X + scaledRadius * (float)Math.Cos(radians);
            float y = center.Y + scaledRadius * (float)Math.Sin(radians);
            SKPoint point = new SKPoint(x, y);

            if (angle == 0)
            {
                path.MoveTo(point);
            }
            else
            {
                path.LineTo(point);
            }
        }

        SKPaint paint = new SKPaint
        {
            Style = SKPaintStyle.Stroke,
            Color = SKColors.Red,
            StrokeWidth = 5
        };

        canvas.DrawPath(path, paint);
    }
}
```

结果也称为 *算术螺旋* ，因为每个循环之间的偏移是常量：

[![Archimedean 螺旋页面的三向屏幕截图](polylines-images/archimedeanspiral-small.png)](polylines-images/archimedeanspiral-large.png#lightbox "Archimedean 螺旋页面的三向屏幕截图")

请注意， `SKPath` 是在块中创建的 `using` 。 这会 `SKPath` 占用比 `SKPath` 上一个程序中的对象更多的内存，这表明 `using` 更适合使用块来释放任何非托管资源。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)