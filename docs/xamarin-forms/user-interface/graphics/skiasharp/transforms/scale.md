---
title: 缩放转换
description: Thhis 文章探讨了用于将对象缩放到各种大小的 SkiaSharp 缩放转换，并通过示例代码对此进行了演示。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 54A43F3D-9DA8-44A7-9AE4-7E3025129A0B
author: davidbritch
ms.author: dabritch
ms.date: 03/23/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5ea4fa88925925ba7c81f47c30875c3419603ab8
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91555458"
---
# <a name="the-scale-transform"></a>缩放转换

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_发现用于将对象缩放到各种大小的 SkiaSharp 缩放变换_

正如你在 [**翻译转换**](translate.md) 一文中看到的那样，转换转换可以将图形对象从一个位置移到另一个位置。 与此相反，缩放变换会改变图形对象的大小：

![缩放大小为高的一字](scale-images/scaleexample.png)

缩放转换通常还会使图形坐标在变大时移动。

之前，您看到了两个转换公式，用于描述和的转换因素的影响 `dx` `dy` ：

x ' = x + dx

y "= y + dy

和的缩放 `sx` 因子 `sy` 是乘法，而不是加法：

x ' = sx ·x-blade

y ' = sy ·误差

转换系数的默认值为 0;缩放系数的默认值为1。

`SKCanvas`类定义四个 `Scale` 方法。 第一 [`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single)) 种方法适用于需要相同的水平和垂直比例因子的情况：

```csharp
public void Scale (Single s)
```

这就是这*isotropic* &mdash; 两个方向上的 isotropic 缩放缩放。 Isotropic 缩放保留对象的纵横比。

第二种 [`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single)) 方法允许您为水平和垂直缩放指定不同的值：

```csharp
public void Scale (Single sx, Single sy)
```

这会导致 *各向异性* 缩放。
第三种 [`Scale`](xref:SkiaSharp.SKCanvas.Scale(SkiaSharp.SKPoint)) 方法将两个缩放系数合并为一个 `SKPoint` 值：

```csharp
public void Scale (SKPoint size)
```

`Scale`稍后将介绍第四种方法。

" **基本缩放** " 页演示了 `Scale` 方法。 [**BasicScalePage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml)文件包含两个 `Slider` 元素，可让你选择0到10之间的水平和垂直缩放系数。 [**BasicScalePage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml.cs)代码隐藏文件使用这些值 `Scale` 在显示用虚线描的圆角矩形之前调用，并调整大小以适合画布左上角的某些文本：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear(SKColors.SkyBlue);

    using (SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 3,
        PathEffect = SKPathEffect.CreateDash(new float[] {  7, 7 }, 0)
    })
    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 50
    })
    {
        canvas.Scale((float)xScaleSlider.Value,
                     (float)yScaleSlider.Value);

        SKRect textBounds = new SKRect();
        textPaint.MeasureText(Title, ref textBounds);

        float margin = 10;
        SKRect borderRect = SKRect.Create(new SKPoint(margin, margin), textBounds.Size);
        canvas.DrawRoundRect(borderRect, 20, 20, strokePaint);
        canvas.DrawText(Title, margin, -textBounds.Top + margin, textPaint);
    }
}
```

您可能想知道：缩放系数如何影响从方法返回的值 `MeasureText` `SKPaint` ？ 答案是：根本就是这样。 `Scale` 是的方法 `SKCanvas` 。 它不会影响使用对象执行的任何操作， `SKPaint` 除非你使用该对象在画布上呈现内容。

正如您所看到的，调用后绘制的所有内容都 `Scale` 按比例增加：

[![基本缩放页面的三向屏幕截图](scale-images/basicscale-small.png)](scale-images/basicscale-large.png#lightbox "基本缩放页面的三向屏幕截图")

文本、虚线的宽度、该线条的划线的长度、角的舍入和画布的左边缘和上边缘之间的10像素的边距，都服从相同的缩放比例。

> [!IMPORTANT]
> 通用 Windows 平台不会正确呈现 anisotropicly 缩放文本。

如果行与水平轴和垂直轴对齐，则各向异性缩放会使笔划宽度变得不同。  (此页上的第一个图像也有明显的 ) 。如果您不希望笔划宽度受缩放系数的影响，请将其设置为0，它将始终为1个像素宽，而不考虑 `Scale` 设置。

缩放相对于画布的左上角。 这可能正是你想要的，但它可能不是。 假设您希望将文本和矩形放置在画布上的其他位置，并且希望相对于其中心缩放文本和矩形。 在这种情况下，可以使用方法的第四个版本 [`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single,System.Single,System.Single)) ，其中包括两个附加参数用于指定缩放中心：

```csharp
public void Scale (Single sx, Single sy, Single px, Single py)
```

`px`和 `py` 参数定义有时称为 "*缩放中心*" 的点，但在 SkiaSharp 文档中称为 "*透视点*"。 此点相对于不受缩放影响的画布的左上角。 所有缩放均相对于该中心进行。

" [**居中缩放**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/CenteredScalePage.xaml.cs) " 页显示其工作原理。 `PaintSurface`处理程序类似于**基本缩放**程序 `margin` ，只不过值计算为水平居中文本，这表示该程序在纵向模式下的效果最佳：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear(SKColors.SkyBlue);

    using (SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 3,
        PathEffect = SKPathEffect.CreateDash(new float[] { 7, 7 }, 0)
    })
    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 50
    })
    {
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(Title, ref textBounds);
        float margin = (info.Width - textBounds.Width) / 2;

        float sx = (float)xScaleSlider.Value;
        float sy = (float)yScaleSlider.Value;
        float px = margin + textBounds.Width / 2;
        float py = margin + textBounds.Height / 2;

        canvas.Scale(sx, sy, px, py);

        SKRect borderRect = SKRect.Create(new SKPoint(margin, margin), textBounds.Size);
        canvas.DrawRoundRect(borderRect, 20, 20, strokePaint);
        canvas.DrawText(Title, margin, -textBounds.Top + margin, textPaint);
    }
}
```

圆角矩形的左上角定位 `margin` 于画布左侧的像素和 `margin` 顶部的像素。 方法的最后两个参数 `Scale` 设置为这些值以及文本的宽度和高度，这也是圆角矩形的宽度和高度。 这意味着所有缩放均相对于该矩形的中心：

[![中心刻度页面的三向屏幕截图](scale-images/centeredscale-small.png)](scale-images/centeredscale-large.png#lightbox "中心刻度页面的三向屏幕截图")

`Slider`此程序中的元素的范围为 &ndash; 10 到10。 正如您所看到的，垂直缩放的负值 (例如，在中心的 Android 屏幕上) 导致对象在水平轴上翻转，沿缩放中心穿过。 水平缩放的负值 (如右侧的 UWP 屏幕中) 会导致对象沿缩放中心穿过垂直轴。

[`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single,System.Single,System.Single))使用透视点的方法的版本是一系列三个和调用的快捷 `Translate` 方式 `Scale` 。 你可能想要了解如何通过 `Scale` 将 " **中心刻度** " 页中的方法替换为以下内容来实现此操作：

```csharp
canvas.Translate(-px, -py);
```

这些是透视点坐标的负片。

现在再次运行程序。 你会看到矩形和文本已移动，因此中心位于画布的左上角。 您几乎可以看到它。 由于目前的程序根本无法进行缩放，因此无法使用滑块。

现在 `Scale` ，在该调用 *之前* ，将基本调用添加 (没有缩放中心) `Translate` ：

```csharp
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

如果您在其他图形编程系统中熟悉此练习，您可能认为这是错误的，但不是。 Skia 处理连续转换调用的方式与你可能熟悉的方式稍有不同。

如果是连续的 `Scale` 并 `Translate` 调用，则圆角矩形的中心仍处于左上角，但现在可以相对于画布的左上角（也是圆角矩形的中心）对其进行缩放。

现在，在该 `Scale` 调用之前，添加另一个 `Translate` 具有中心值的调用：

```csharp
canvas.Translate(px, py);
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

这会将缩放后的结果移回原始位置。 这三个调用等效于：

```csharp
canvas.Scale(sx, sy, px, py);
```

每个转换都是复合的，因此总转换公式为：

 x ' = sx · (x-px) + px

 y ' = sy · (y – py) + py

请记住，和的默认值 `sx` `sy` 为1。 您可以轻松地说服，透视点 (px，py) 不会被这些公式转换。 它与画布保持在同一位置。

当你组合 `Translate` 和 `Scale` 调用时，顺序很重要。 如果位于 `Translate` 之后 `Scale` ，则转换因素会按缩放比例有效地缩放。 如果位于 `Translate` 之前 `Scale` ，则不缩放转换因素。 此过程在某种程度上变得更清晰 (不过，当引入变换矩阵的主题时，更有数学) 。

`SKPath`类定义一个只读 [`Bounds`](xref:SkiaSharp.SKPath.Bounds) 属性，该属性返回 `SKRect` 定义路径中的坐标范围。 例如，当 `Bounds` 从前面创建的 hendecagram 路径中获取属性时，矩形的 `Left` 和 `Top` 属性大约为–100，并且和属性约 `Right` 为 `Bottom` 100， `Width` 并且和 `Height` 属性大约为200。  (大多数实际值稍少一些，因为星形的点由半径为100的圆定义，但只有顶部点与水平轴或垂直轴平行。 ) 

此信息的可用性意味着，应该可以派生适合缩放画布大小路径的缩放和转换因素。 " [**各向异性缩放**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AnisotropicScalingPage.cs) " 页通过11形星形向此演示。 " *各向异性* " 刻度表示水平和垂直方向不相等，这意味着星号不会保留其原始纵横比。 下面是处理程序中的相关代码 `PaintSurface` ：

```csharp
SKPath path = HendecagramPage.HendecagramPath;
SKRect pathBounds = path.Bounds;

using (SKPaint fillPaint = new SKPaint
{
    Style = SKPaintStyle.Fill,
    Color = SKColors.Pink
})
using (SKPaint strokePaint = new SKPaint
{
    Style = SKPaintStyle.Stroke,
    Color = SKColors.Blue,
    StrokeWidth = 3,
    StrokeJoin = SKStrokeJoin.Round
})
{
    canvas.Scale(info.Width / pathBounds.Width,
                 info.Height / pathBounds.Height);
    canvas.Translate(-pathBounds.Left, -pathBounds.Top);

    canvas.DrawPath(path, fillPaint);
    canvas.DrawPath(path, strokePaint);
}
```

在 `pathBounds` 此代码的顶部附近获取矩形，然后将其用于调用中画布的宽度和高度 `Scale` 。 当通过调用呈现路径时，此调用会缩放路径的坐标， `DrawPath` 而星号将在画布的右上角进行居中。 它需要向下和向左移动。 这是调用的作业 `Translate` 。 这两个属性 `pathBounds` 大约为–100，因此翻译因素约为100。 由于 `Translate` 调用之后调用，因此 `Scale` 这些值通过缩放因子有效地缩放，因此它们将星形中心移动到画布中心：

[!["各向异性缩放" 页的三向屏幕截图](scale-images/anisotropicscaling-small.png)](scale-images/anisotropicscaling-large.png#lightbox ""各向异性缩放" 页的三向屏幕截图")

您可以考虑和调用的另一种方法 `Scale` `Translate` 是通过反向顺序来确定效果：调用会将 `Translate` 路径移动到完全可见，但在画布的左上角。 `Scale`然后，方法会使该星形相对于左上角更大。

实际上，星号显示的比画布要大一些。 问题在于笔划宽度。 的 `Bounds` 属性 `SKPath` 指示在路径中编码的坐标的尺寸，这就是程序用来对其进行缩放的内容。 当使用特定的笔划宽度呈现路径时，呈现的路径大于画布。

若要解决此问题，需要对此进行补偿。 此程序中的一种简单方法是在调用之前添加以下语句 `Scale` ：

```csharp
pathBounds.Inflate(strokePaint.StrokeWidth / 2,
                   strokePaint.StrokeWidth / 2);
```

这会将 `pathBounds` 矩形增加四个边1.5 单位。 只有在舍入笔划联接时，这才是合理的解决方案。 斜接联接可能更长且难以计算。

你还可以使用与文本相同的技术，如 " **各向异性文本** " 页所示。 下面是 `PaintSurface` 来自类的处理程序的相关部分 [`AnisotropicTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AnisotropicTextPage.cs) ：

```csharp
using (SKPaint textPaint = new SKPaint
{
    Style = SKPaintStyle.Stroke,
    Color = SKColors.Blue,
    StrokeWidth = 0.1f,
    StrokeJoin = SKStrokeJoin.Round
})
{
    SKRect textBounds = new SKRect();
    textPaint.MeasureText("HELLO", ref textBounds);

    // Inflate bounds by the stroke width
    textBounds.Inflate(textPaint.StrokeWidth / 2,
                       textPaint.StrokeWidth / 2);

    canvas.Scale(info.Width / textBounds.Width,
                 info.Height / textBounds.Height);
    canvas.Translate(-textBounds.Left, -textBounds.Top);

    canvas.DrawText("HELLO", 0, 0, textPaint);
}
```

这是类似的逻辑，并且文本将基于从 (返回的文本边界矩形（ `MeasureText` 比实际文本) 稍大）扩展到页面的大小：

[!["各向异性测试" 页的三个屏幕截图](scale-images/anisotropictext-small.png)](scale-images/anisotropictext-large.png#lightbox ""各向异性测试" 页的三个屏幕截图")

如果需要保留图形对象的纵横比，则需要使用 isotropic 缩放。 " **Isotropic 缩放** " 页对11形星形显示了此项。 从概念上讲，在页面中心使用 isotropic 缩放显示图形对象的步骤如下：

- 将图形对象的中心转换为左上角。
- 根据水平和垂直页尺寸的最小值，根据图形对象尺寸来缩放对象。
- 将缩放对象的中心转换到页面的中心。

[`IsotropicScalingPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/IsotropicScalingPage.cs)显示星号之前，按逆序执行这些步骤：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPath path = HendecagramArrayPage.HendecagramPath;
    SKRect pathBounds = path.Bounds;

    using (SKPaint fillPaint = new SKPaint())
    {
        fillPaint.Style = SKPaintStyle.Fill;

        float scale = Math.Min(info.Width / pathBounds.Width,
                               info.Height / pathBounds.Height);

        for (int i = 0; i <= 10; i++)
        {
            fillPaint.Color = new SKColor((byte)(255 * (10 - i) / 10),
                                          0,
                                          (byte)(255 * i / 10));
            canvas.Save();
            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.Scale(scale);
            canvas.Translate(-pathBounds.MidX, -pathBounds.MidY);
            canvas.DrawPath(path, fillPaint);
            canvas.Restore();

            scale *= 0.9f;
        }
    }
}
```

该代码还显示星号10次，每次将缩放因子降低10%，并将颜色从红色逐渐更改为蓝色：

[![Isotropic 缩放页的三向屏幕截图](scale-images/isotropicscaling-small.png)](scale-images/isotropicscaling-large.png#lightbox "Isotropic 缩放页的三向屏幕截图")

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)