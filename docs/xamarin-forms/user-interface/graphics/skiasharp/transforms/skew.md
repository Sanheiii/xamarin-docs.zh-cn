---
title: 倾斜转换
description: 本文介绍了倾斜变换如何在 SkiaSharp 中创建倾斜的图形对象，并通过示例代码对此进行了演示。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: FDD16186-E3B7-4FF6-9BC2-8A2974BFF616
author: davidbritch
ms.author: dabritch
ms.date: 03/20/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ed1bcbed86874362e291fb23ce86dea8992d3408
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563726"
---
# <a name="the-skew-transform"></a>倾斜转换

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_了解倾斜变换如何在 SkiaSharp 中创建倾斜的图形对象_

在 SkiaSharp 中，倾斜变换会倾斜图形对象，如此图中的阴影：

![倾斜阴影文本程序倾斜的示例](skew-images/skewexample.png)

倾斜会将矩形变为平行四边形，但倾斜的椭圆仍是一个椭圆。

尽管 Xamarin.Forms 为转换、缩放和旋转定义属性，但中没有相应 Xamarin.Forms 的属性用于倾斜。

的 [`Skew`](xref:SkiaSharp.SKCanvas.Skew(System.Single,System.Single)) 方法 `SKCanvas` 接受两个自变量用于水平倾斜和垂直倾斜：

```csharp
public void Skew (Single xSkew, Single ySkew)
```

第二种 [`Skew`](xref:SkiaSharp.SKCanvas.Skew(SkiaSharp.SKPoint)) 方法将这些参数合并为一个 `SKPoint` 值：

```csharp
public void Skew (SKPoint skew)
```

但是，不太可能使用这两种方法中的一种。

" **倾斜试验** " 页允许您试验介于-10 和10之间的倾斜值。 文本字符串位于页面的左上角，其中包含从两个元素获得的倾斜值 `Slider` 。 下面是 `PaintSurface` 类中的处理程序 [`SkewExperimentPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SkewExperimentPage.xaml.cs) ：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 200
    })
    {
        string text = "SKEW";
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(text, ref textBounds);

        canvas.Skew((float)xSkewSlider.Value, (float)ySkewSlider.Value);
        canvas.DrawText(text, 0, -textBounds.Top, textPaint);
    }
}
```

参数的值将 `xSkew` 向右移动文本右侧的正值或向左移动负值。 `ySkew`对于正值，将向下移动文本右侧的值; 对于负值，则为负值：

[!["倾斜试验" 页的三向屏幕截图](skew-images/skewexperiment-small.png)](skew-images/skewexperiment-large.png#lightbox ""倾斜试验" 页的三向屏幕截图")

如果 `xSkew` 该值为值的负值，则 `ySkew` 结果是旋转，但也会在某种程度上进行缩放。

转换公式如下所示：

x ' = x + xSkew ·误差

y ' = ySkew ·x + y

例如，对于正值 `xSkew` ，转换后的值将 `x'` 增加 `y` 。 这就是导致倾斜的原因。

如果三角形200像素宽和100像素高，则定位在 (0，0) 点的左上角，而呈现 `xSkew` 的值为1.5，则以下平行四边形结果：

![倾斜变换对矩形的影响](skew-images/skeweffect.png)

下边缘的坐标 `y` 值为100，因此它向右移位150像素。

对于或的非零值 `xSkew` `ySkew` ，只 (0，0) 的点保持不变。 可以将该点视为倾斜中心。 如果需要将扭曲中心作为其他 (通常是) 的情况，则没有 `Skew` 方法可以提供该内容。 你需要通过调用显式合并 `Translate` 调用 `Skew` 。 若要在 `px` 和中居中倾斜 `py` ，请进行以下调用：

```csharp
canvas.Translate(px, py);
canvas.Skew(xSkew, ySkew);
canvas.Translate(-px, -py);
```

复合转换公式为：

x ' = x + xSkew · (y – py) 

y ' = ySkew · (x-px) + y

如果 `ySkew` 为零，则 `px` 不使用该值。 值不相关，并且 `ySkew` 与和类似 `py` 。

您可能更愿意将倾斜指定为倾斜角度，如此图中的α角度：

![倾斜变换在指定了倾斜角度的矩形上的效果](skew-images/skewangleeffect.png)

150像素移位到100像素垂直的比率是该角度的正切值，在此示例中为56.3 度。

" **倾斜角度试验** " 页的 XAML 文件类似于 " **倾斜角度** " 页，只不过 `Slider` 元素范围为–90度到90度。 [`SkewAngleExperiment`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SkewAngleExperimentPage.xaml.cs)代码隐藏文件使页面上的文本居中，并使用将 `Translate` 倾斜中心设置到页面中心。 `SkewDegrees`代码底部的简短方法将角度转换为倾斜值：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 200
    })
    {
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        string text = "SKEW";
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(text, ref textBounds);
        float xText = xCenter - textBounds.MidX;
        float yText = yCenter - textBounds.MidY;

        canvas.Translate(xCenter, yCenter);
        SkewDegrees(canvas, xSkewSlider.Value, ySkewSlider.Value);
        canvas.Translate(-xCenter, -yCenter);
        canvas.DrawText(text, xText, yText, textPaint);
    }
}

void SkewDegrees(SKCanvas canvas, double xDegrees, double yDegrees)
{
    canvas.Skew((float)Math.Tan(Math.PI * xDegrees / 180),
                (float)Math.Tan(Math.PI * yDegrees / 180));
}
```

当角度接近正面或负90度时，相切接近，但最多可使用大约80度的角度：

[![倾斜角度试验页的三向屏幕截图](skew-images/skewangleexperiment-small.png)](skew-images/skewangleexperiment-large.png#lightbox "倾斜角度试验页的三向屏幕截图")

如果 **倾斜文本** 页面说明，较小的负水平倾斜可以模拟倾斜或倾斜文本。 [`ObliqueTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ObliqueTextPage.cs)类显示操作的完成方式：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint()
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Maroon,
        TextAlign = SKTextAlign.Center,
        TextSize = info.Width / 8       // empirically determined
    })
    {
        canvas.Translate(info.Width / 2, info.Height / 2);
        SkewDegrees(canvas, -20, 0);
        canvas.DrawText(Title, 0, 0, textPaint);
    }
}

void SkewDegrees(SKCanvas canvas, double xDegrees, double yDegrees)
{
    canvas.Skew((float)Math.Tan(Math.PI * xDegrees / 180),
                (float)Math.Tan(Math.PI * yDegrees / 180));
}
```

的 `TextAlign` 属性 `SKPaint` 设置为 `Center` 。 如果没有任何转换，则 `DrawText` 通过坐标 (0，0) 的调用会将文本与基线的水平中心置于左上角。 相对于基线，将 `SkewDegrees` 文本水平倾斜20度。 `Translate`调用将文本基线的水平中心移动到画布中心：

[![倾斜文本页的三向屏幕截图](skew-images/obliquetext-small.png)](skew-images/obliquetext-large.png#lightbox "倾斜文本页的三向屏幕截图")

" **倾斜阴影文本** " 页演示了如何结合使用45度倾斜和垂直缩放来使文本阴影远离文本。 下面是处理程序的相关部分 `PaintSurface` ：

```csharp
using (SKPaint textPaint = new SKPaint())
{
    textPaint.Style = SKPaintStyle.Fill;
    textPaint.TextSize = info.Width / 6;   // empirically determined

    // Common to shadow and text
    string text = "Shadow";
    float xText = 20;
    float yText = info.Height / 2;

    // Shadow
    textPaint.Color = SKColors.LightGray;
    canvas.Save();
    canvas.Translate(xText, yText);
    canvas.Skew((float)Math.Tan(-Math.PI / 4), 0);
    canvas.Scale(1, 3);
    canvas.Translate(-xText, -yText);
    canvas.DrawText(text, xText, yText, textPaint);
    canvas.Restore();

    // Text
    textPaint.Color = SKColors.Blue;
    canvas.DrawText(text, xText, yText, textPaint);
}
```

影子首先显示，然后是文本：

[![倾斜阴影文本页的三向屏幕截图](skew-images/skewshadowtext1-small.png)](skew-images/skewshadowtext1-large.png#lightbox "倾斜阴影文本页的三向屏幕截图")

传递给方法的垂直坐标 `DrawText` 指示文本相对于基线的位置。 这是用于倾斜的中心的同一垂直坐标。 如果文本字符串包含下行字母，此方法将不起作用。 例如，用 "古怪" 一词替换 "阴影"，结果如下：

[![倾斜阴影文本页面的三重](skew-images/skewshadowtext2-small.png)](skew-images/skewshadowtext2-large.png#lightbox "倾斜阴影文本页面的三重")

阴影和文本仍在基线上对齐，但效果却出现错误。 若要修复此问题，需要获取文本边界：

```csharp
SKRect textBounds = new SKRect();
textPaint.MeasureText(text, ref textBounds);
```

`Translate`调用需要按照下行字母的高度进行调整：

```csharp
canvas.Translate(xText, yText + textBounds.Bottom);
canvas.Skew((float)Math.Tan(-Math.PI / 4), 0);
canvas.Scale(1, 3);
canvas.Translate(-xText, -yText - textBounds.Bottom);
```

现在，阴影从下行伸的底部延伸：

[![倾斜阴影文本页面的三次屏幕截图，并调整下行](skew-images/skewshadowtext3-small.png)](skew-images/skewshadowtext3-large.png#lightbox "倾斜阴影文本页面的三次屏幕截图，并调整下行")

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)