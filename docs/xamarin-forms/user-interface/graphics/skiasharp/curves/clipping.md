---
title: 按路径和区域进行剪裁
description: 本文介绍如何使用 SkiaSharp 路径将图形剪辑到特定区域和创建区域，并使用示例代码对此进行演示。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 8022FBF9-2208-43DB-94D8-0A4E9A5DA07F
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 604d2aa8368c85b852530f999601fffca8e104e6
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562920"
---
# <a name="clipping-with-paths-and-regions"></a>按路径和区域进行剪裁

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_使用路径将图形剪辑到特定区域和创建区域_

有时需要将图形呈现限制为特定区域。 这称为 " *剪裁*"。 您可以使用剪辑来实现特殊效果，例如通过 keyhole 查看的这一图像：

![通过 keyhole 的猴子](clipping-images/clippingsample.png)

*剪辑区域*是呈现图形的屏幕区域。 不会呈现在剪辑区域之外显示的任何内容。 剪辑区域通常由矩形或 [`SKPath`](xref:SkiaSharp.SKPath) 对象定义，但您也可以使用对象定义剪辑区域 [`SKRegion`](xref:SkiaSharp.SKRegion) 。 这两种类型的对象最初看起来是相关的，因为你可以从路径创建区域。 但是，您不能从区域创建路径，并且它们在内部都非常不同：路径由一系列线条和曲线组成，而区域由一系列水平扫描线定义。

上图是通过 **Keyhole** 页创建的。 [`MonkeyThroughKeyholePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/MonkeyThroughKeyholePage.cs)类使用 SVG 数据定义路径，并使用构造函数从程序资源加载位图：

```csharp
public class MonkeyThroughKeyholePage : ContentPage
{
    SKBitmap bitmap;
    SKPath keyholePath = SKPath.ParseSvgPathData(
        "M 300 130 L 250 350 L 450 350 L 400 130 A 70 70 0 1 0 300 130 Z");

    public MonkeyThroughKeyholePage()
    {
        Title = "Monkey through Keyhole";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }
    ...
}
```

尽管 `keyholePath` 对象描述了 keyhole 的轮廓，但坐标完全是随意的，并反映了在设计路径数据时方便的操作。 出于此原因， `PaintSurface` 处理程序将获取此路径的界限并调用 `Translate` 并 `Scale` 将路径移动到屏幕的中心，使其与屏幕的高度大致相同：

```csharp
public class MonkeyThroughKeyholePage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Set transform to center and enlarge clip path to window height
        SKRect bounds;
        keyholePath.GetTightBounds(out bounds);

        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(0.98f * info.Height / bounds.Height);
        canvas.Translate(-bounds.MidX, -bounds.MidY);

        // Set the clip path
        canvas.ClipPath(keyholePath);

        // Reset transforms
        canvas.ResetMatrix();

        // Display monkey to fill height of window but maintain aspect ratio
        canvas.DrawBitmap(bitmap,
            new SKRect((info.Width - info.Height) / 2, 0,
                       (info.Width + info.Height) / 2, info.Height));
    }
}
```

但路径不会呈现。 相反，在转换后，路径用于使用以下语句设置剪辑区域：

```csharp
canvas.ClipPath(keyholePath);
```

`PaintSurface`然后，处理程序使用对的调用重置转换 `ResetMatrix` ，并绘制位图以扩展到屏幕的全部高度。 此代码假定位图为方形，这一特定位图为方形。 仅在由剪切路径定义的区域内呈现位图：

[![通过 Keyhole 页面的三重屏幕截图](clipping-images/monkeythroughkeyhole-small.png)](clipping-images/monkeythroughkeyhole-large.png#lightbox)

当调用方法时，剪切路径服从于有效的转换 `ClipPath` ，而不是在显示图形对象 (如位图) 时生效的转换。 剪切路径是画布状态的一部分，该状态随方法一起保存 `Save` 并通过 `Restore` 方法还原。

## <a name="combining-clipping-paths"></a>合并剪切路径

严格地说，方法不会 "设置" 剪辑区域 `ClipPath` 。 相反，它与现有的剪切路径组合在一起，后者作为大小等于画布的矩形开始。 您可以使用属性或属性获取剪辑区域的矩形边界 [`ClipBounds`](xref:SkiaSharp.SKCanvas.ClipBounds) [`ClipDeviceBounds`](xref:SkiaSharp.SKCanvas.ClipDeviceBounds) 。 `ClipBounds`属性返回一个 `SKRect` 值，该值反映任何可能生效的转换。 `ClipDeviceBounds`属性返回一个 `RectI` 值。 这是包含整数维度的矩形，并描述了实际像素维度中的剪辑区域。

对的任何调用都 `ClipPath` 通过将剪辑区域与新区域相结合来减小剪辑区域。 该方法的完整语法 [`ClipPath`](xref:SkiaSharp.SKCanvas.ClipPath(SkiaSharp.SKPath,SkiaSharp.SKClipOperation,System.Boolean)) 为：

```csharp
public void ClipPath(SKPath path, SKClipOperation operation = SKClipOperation.Intersect, Boolean antialias = false);
```

还有一个 [`ClipRect`](xref:SkiaSharp.SKCanvas.ClipRect(SkiaSharp.SKRect,SkiaSharp.SKClipOperation,System.Boolean)) 方法，该方法将剪辑区域与一个矩形组合在一起：

```csharp
public Void ClipRect(SKRect rect, SKClipOperation operation = SKClipOperation.Intersect, Boolean antialias = false);
```

默认情况下，生成的剪辑区域是现有剪辑区域与或 `SKPath` 方法中指定的或的交集 `SKRect` `ClipPath` `ClipRect` 。 这会在 **四个圆圈相交** 的 "剪切" 页中进行演示。 `PaintSurface`类中的处理程序 [`FourCircleInteresectClipPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/FourCircleIntersectClipPage.cs) 重用相同的 `SKPath` 对象来创建四个重叠圆圈，其中每个圆通过连续调用来减小剪辑区域 `ClipPath` ：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float size = Math.Min(info.Width, info.Height);
    float radius = 0.4f * size;
    float offset = size / 2 - radius;

    // Translate to center
    canvas.Translate(info.Width / 2, info.Height / 2);

    using (SKPath path = new SKPath())
    {
        path.AddCircle(-offset, -offset, radius);
        canvas.ClipPath(path, SKClipOperation.Intersect);

        path.Reset();
        path.AddCircle(-offset, offset, radius);
        canvas.ClipPath(path, SKClipOperation.Intersect);

        path.Reset();
        path.AddCircle(offset, -offset, radius);
        canvas.ClipPath(path, SKClipOperation.Intersect);

        path.Reset();
        path.AddCircle(offset, offset, radius);
        canvas.ClipPath(path, SKClipOperation.Intersect);

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Fill;
            paint.Color = SKColors.Blue;
            canvas.DrawPaint(paint);
        }
    }
}
```

剩下的是以下四个圆圈的交集：

[![四个圆圈交叉剪裁页面的三向屏幕截图](clipping-images//fourcircleintersectclip-small.png)](clipping-images/fourcircleintersectclip-large.png#lightbox)

[`SKClipOperation`](xref:SkiaSharp.SKClipOperation)枚举只有两个成员：

- `Difference` 从现有剪辑区域中移除指定的路径或矩形

- `Intersect` 与指定的路径或矩形与现有剪辑区域相交

如果将类中的四个 `SKClipOperation.Intersect` 参数替换 `FourCircleIntersectClipPage` 为 `SKClipOperation.Difference` ，则会看到以下内容：

[![这四个圆圈相交于剪裁页面并带有差异操作](clipping-images//fourcircledifferenceclip-small.png)](clipping-images/fourcircledifferenceclip-large.png#lightbox)

已从剪辑区域中删除四个重叠圆圈。

" **剪辑操作** " 页面说明了这两个操作之间的区别，只包含一对圆圈。 左侧的第一个圆圈将添加到剪辑区域，其中包含的默认剪辑操作 `Intersect` ，而右侧的第二个圆圈添加到剪辑区域，其中包含由文本标签指示的剪辑操作：

[!["剪辑操作" 页的三个屏幕截图](clipping-images//clipoperations-small.png)](clipping-images/clipoperations-large.png#lightbox)

[`ClipOperationsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ClipOperationsPage.cs)类将两个 `SKPaint` 对象定义为字段，然后将屏幕分成两个矩形区域。 根据手机处于纵向还是横向模式，这些区域是不同的。 `DisplayClipOp`然后，类显示文本，并调用 `ClipPath` 两个圆圈路径来说明每个剪辑操作：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float x = 0;
    float y = 0;

    foreach (SKClipOperation clipOp in Enum.GetValues(typeof(SKClipOperation)))
    {
        // Portrait mode
        if (info.Height > info.Width)
        {
            DisplayClipOp(canvas, new SKRect(x, y, x + info.Width, y + info.Height / 2), clipOp);
            y += info.Height / 2;
        }
        // Landscape mode
        else
        {
            DisplayClipOp(canvas, new SKRect(x, y, x + info.Width / 2, y + info.Height), clipOp);
            x += info.Width / 2;
        }
    }
}

void DisplayClipOp(SKCanvas canvas, SKRect rect, SKClipOperation clipOp)
{
    float textSize = textPaint.TextSize;
    canvas.DrawText(clipOp.ToString(), rect.MidX, rect.Top + textSize, textPaint);
    rect.Top += textSize;

    float radius = 0.9f * Math.Min(rect.Width / 3, rect.Height / 2);
    float xCenter = rect.MidX;
    float yCenter = rect.MidY;

    canvas.Save();

    using (SKPath path1 = new SKPath())
    {
        path1.AddCircle(xCenter - radius / 2, yCenter, radius);
        canvas.ClipPath(path1);

        using (SKPath path2 = new SKPath())
        {
            path2.AddCircle(xCenter + radius / 2, yCenter, radius);
            canvas.ClipPath(path2, clipOp);

            canvas.DrawPaint(fillPaint);
        }
    }

    canvas.Restore();
}
```

`DrawPaint`通常情况下，调用会使整个画布填充该 `SKPaint` 对象，但在这种情况下，方法只是在剪辑区域内绘制。

## <a name="exploring-regions"></a>探索区域

还可以根据对象定义剪辑区域 [`SKRegion`](xref:SkiaSharp.SKRegion) 。

新创建的 `SKRegion` 对象描述空白区域。 通常，对对象的第一次调用是， [`SetRect`](xref:SkiaSharp.SKRegion.SetRect(SkiaSharp.SKRectI)) 以便区域描述一个矩形区域。 的参数 `SetRect` 是 `SKRectI` 一个值， &mdash; 它包含整数坐标，因为它以像素为单位指定矩形。 然后，可以 [`SetPath`](xref:SkiaSharp.SKRegion.SetPath(SkiaSharp.SKPath,SkiaSharp.SKRegion)) 使用对象调用 `SKPath` 。 这将创建一个区域，该区域与路径的内部同名，但被剪裁到初始矩形区域。

还可以通过调用其中一个方法重载来修改区域 [`Op`](xref:SkiaSharp.SKRegion.Op*) ，例如：

```csharp
public Boolean Op(SKRegion region, SKRegionOperation op)
```

[`SKRegionOperation`](xref:SkiaSharp.SKRegionOperation)枚举类似于， `SKClipOperation` 但它具有更多成员：

- `Difference`

- `Intersect`

- `Union`

- `XOR`

- `ReverseDifference`

- `Replace`

要对其进行调用的区域 `Op` 将与指定为基于成员的参数的区域组合在一起 `SKRegionOperation` 。 最终获得适用于剪辑的区域时，可以使用的方法将其设置为画布的剪辑区域 [`ClipRegion`](xref:SkiaSharp.SKCanvas.ClipRegion(SkiaSharp.SKRegion,SkiaSharp.SKClipOperation)) `SKCanvas` ：

```csharp
public void ClipRegion(SKRegion region, SKClipOperation operation = SKClipOperation.Intersect)
```

以下屏幕截图显示了基于六个区域操作的剪辑区域。 左圆圈是 `Op` 调用方法的区域，右侧圆圈是传递给方法的区域 `Op` ：

[!["区域操作" 页的三个屏幕截图](clipping-images//regionoperations-small.png)](clipping-images/regionoperations-large.png#lightbox)

这两个圆圈的组合是否有可能？ 将生成的映像视为三个组件的组合，这些组件本身在 `Difference` 、 `Intersect` 和操作中可见 `ReverseDifference` 。 组合的总数为第三次幂，即8个。 缺少的两个都是原始区域 (这是因为不会 `Op` 在所有) 和完全空区域调用。

由于需要首先创建一个路径，然后从该路径中创建一个区域，然后组合多个区域，因此很难使用区域进行剪辑。 " **区域操作** " 页的总体结构与 **剪辑操作** 非常相似，但 [`RegionOperationsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RegionOperationsPage.cs) 类将屏幕分为六个区域，并显示使用区域执行此作业所需的额外工作。

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float x = 0;
    float y = 0;
    float width = info.Height > info.Width ? info.Width / 2 : info.Width / 3;
    float height = info.Height > info.Width ? info.Height / 3 : info.Height / 2;

    foreach (SKRegionOperation regionOp in Enum.GetValues(typeof(SKRegionOperation)))
    {
        DisplayClipOp(canvas, new SKRect(x, y, x + width, y + height), regionOp);

        if ((x += width) >= info.Width)
        {
            x = 0;
            y += height;
        }
    }
}

void DisplayClipOp(SKCanvas canvas, SKRect rect, SKRegionOperation regionOp)
{
    float textSize = textPaint.TextSize;
    canvas.DrawText(regionOp.ToString(), rect.MidX, rect.Top + textSize, textPaint);
    rect.Top += textSize;

    float radius = 0.9f * Math.Min(rect.Width / 3, rect.Height / 2);
    float xCenter = rect.MidX;
    float yCenter = rect.MidY;

    SKRectI recti = new SKRectI((int)rect.Left, (int)rect.Top,
                                (int)rect.Right, (int)rect.Bottom);

    using (SKRegion wholeRectRegion = new SKRegion())
    {
        wholeRectRegion.SetRect(recti);

        using (SKRegion region1 = new SKRegion(wholeRectRegion))
        using (SKRegion region2 = new SKRegion(wholeRectRegion))
        {
            using (SKPath path1 = new SKPath())
            {
                path1.AddCircle(xCenter - radius / 2, yCenter, radius);
                region1.SetPath(path1);
            }

            using (SKPath path2 = new SKPath())
            {
                path2.AddCircle(xCenter + radius / 2, yCenter, radius);
                region2.SetPath(path2);
            }

            region1.Op(region2, regionOp);

            canvas.Save();
            canvas.ClipRegion(region1);
            canvas.DrawPaint(fillPaint);
            canvas.Restore();
        }
    }
}
```

此方法和方法之间的主要区别如下 `ClipPath` `ClipRegion` ：

> [!IMPORTANT]
> 与 `ClipPath` 方法不同， `ClipRegion` 方法不受转换的影响。

若要理解这种差异的基本原理，了解什么是区域是非常有帮助的。 如果您已了解如何在内部实现剪辑操作或区域操作，则可能看起来非常复杂。 正在合并几个可能非常复杂的路径，并且生成的路径的轮廓可能会产生很大的算法。

如果每个路径都缩减为一系列水平扫描线，如老式的真空角阀电视，则此作业会得到很大程度的简化。 每个扫描行只是一个带有起点和终点的水平线条。 例如，半径为10个像素的圆可以分解为20个水平扫描线，其中每行都从圆圈的左侧部分开始，并在右部分结束。 将两个圆圈与任何区域操作组合起来非常简单，因为这只是检查每对相应扫描行的开始和结束坐标。

这是一个区域的内容：一系列定义区域的水平扫描行。

但是，当某个区域缩小为一系列扫描行时，这些扫描行将基于特定的像素维度。 严格地说，区域不是向量图形对象。 与路径相比，它在本质上更接近压缩的单色位图。 因此，在不丢失保真度的情况下，不能缩放或旋转区域，因此在用于剪辑区域时不会转换区域。

但是，可以将转换应用于区域，以便进行绘制。 **区域画图**程序清晰演示了区域的内部性质。 [`RegionPaintPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RegionPaintPage.cs)类 `SKRegion` 基于 `SKPath` 10 个单位的 radius 圆圈创建一个对象。 然后，转换将该圆圈展开以填充页面：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    int radius = 10;

    // Create circular path
    using (SKPath circlePath = new SKPath())
    {
        circlePath.AddCircle(0, 0, radius);

        // Create circular region
        using (SKRegion circleRegion = new SKRegion())
        {
            circleRegion.SetRect(new SKRectI(-radius, -radius, radius, radius));
            circleRegion.SetPath(circlePath);

            // Set transform to move it to center and scale up
            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.Scale(Math.Min(info.Width / 2, info.Height / 2) / radius);

            // Fill region
            using (SKPaint fillPaint = new SKPaint())
            {
                fillPaint.Style = SKPaintStyle.Fill;
                fillPaint.Color = SKColors.Orange;

                canvas.DrawRegion(circleRegion, fillPaint);
            }

            // Stroke path for comparison
            using (SKPaint strokePaint = new SKPaint())
            {
                strokePaint.Style = SKPaintStyle.Stroke;
                strokePaint.Color = SKColors.Blue;
                strokePaint.StrokeWidth = 0.1f;

                canvas.DrawPath(circlePath, strokePaint);
            }
        }
    }
}
```

`DrawRegion`调用用橙色填充区域，而 `DrawPath` 调用会将原始路径笔划为蓝色以进行比较：

[![区域绘制页面的三向屏幕截图](clipping-images//regionpaint-small.png)](clipping-images/regionpaint-large.png#lightbox)

区域显然是一系列离散坐标。

如果不需要使用转换连接到剪辑区域，则可以使用区域进行剪辑，如 **四叶三月** 页所示。 [`FourLeafCloverPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/FourLeafCloverPage.cs)类从四个圆形区域构造一个复合区域，将该复合区域设置为剪辑区域，然后在页面的中心绘制一系列360直线 emanating：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float xCenter = info.Width / 2;
    float yCenter = info.Height / 2;
    float radius = 0.24f * Math.Min(info.Width, info.Height);

    using (SKRegion wholeScreenRegion = new SKRegion())
    {
        wholeScreenRegion.SetRect(new SKRectI(0, 0, info.Width, info.Height));

        using (SKRegion leftRegion = new SKRegion(wholeScreenRegion))
        using (SKRegion rightRegion = new SKRegion(wholeScreenRegion))
        using (SKRegion topRegion = new SKRegion(wholeScreenRegion))
        using (SKRegion bottomRegion = new SKRegion(wholeScreenRegion))
        {
            using (SKPath circlePath = new SKPath())
            {
                // Make basic circle path
                circlePath.AddCircle(xCenter, yCenter, radius);

                // Left leaf
                circlePath.Transform(SKMatrix.MakeTranslation(-radius, 0));
                leftRegion.SetPath(circlePath);

                // Right leaf
                circlePath.Transform(SKMatrix.MakeTranslation(2 * radius, 0));
                rightRegion.SetPath(circlePath);

                // Make union of right with left
                leftRegion.Op(rightRegion, SKRegionOperation.Union);

                // Top leaf
                circlePath.Transform(SKMatrix.MakeTranslation(-radius, -radius));
                topRegion.SetPath(circlePath);

                // Combine with bottom leaf
                circlePath.Transform(SKMatrix.MakeTranslation(0, 2 * radius));
                bottomRegion.SetPath(circlePath);

                // Make union of top with bottom
                bottomRegion.Op(topRegion, SKRegionOperation.Union);

                // Exclusive-OR left and right with top and bottom
                leftRegion.Op(bottomRegion, SKRegionOperation.XOR);

                // Set that as clip region
                canvas.ClipRegion(leftRegion);

                // Set transform for drawing lines from center
                canvas.Translate(xCenter, yCenter);

                // Draw 360 lines
                for (double angle = 0; angle < 360; angle++)
                {
                    float x = 2 * radius * (float)Math.Cos(Math.PI * angle / 180);
                    float y = 2 * radius * (float)Math.Sin(Math.PI * angle / 180);

                    using (SKPaint strokePaint = new SKPaint())
                    {
                        strokePaint.Color = SKColors.Green;
                        strokePaint.StrokeWidth = 2;

                        canvas.DrawLine(0, 0, x, y, strokePaint);
                    }
                }
            }
        }
    }
}
```

它看起来不像是一个四叶三月，但它是一个图像，如果不进行剪辑，则可能难以呈现：

[![四叶三月页面的三向屏幕截图](clipping-images//fourleafclover-small.png)](clipping-images/fourleafclover-large.png#lightbox)

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)