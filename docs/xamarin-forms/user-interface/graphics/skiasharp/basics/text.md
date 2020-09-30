---
title: 将文本和图形集成
description: 本文介绍如何确定呈现的文本字符串的大小，以将包含 SkiaSharp 图形的文本集成到 Xamarin.Forms 应用程序中，并通过示例代码对此进行演示。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: A0B5AC82-7736-4AD8-AA16-FE43E18D203C
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 032a01a1e4e0f2b3e3d394aec6a30bd215fd84f8
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562413"
---
# <a name="integrating-text-and-graphics"></a>将文本和图形集成

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_请参阅如何确定呈现的文本字符串的大小，以将文本与 SkiaSharp 图形集成_

本文演示了如何度量文本，如何将文本缩放到特定大小，以及如何将文本与其他图形集成：

![矩形周围的文本](text-images/textandgraphicsexample.png)

该图像还包括一个圆角矩形。 SkiaSharp `Canvas` 类包括 [`DrawRect`](xref:SkiaSharp.SKCanvas.DrawRect*) 用于绘制矩形的方法以及 [`DrawRoundRect`](xref:SkiaSharp.SKCanvas.DrawRoundRect*) 用于绘制圆角的矩形。 这些方法允许将矩形定义为 `SKRect` 值或以其他方式定义。

带 **框的文本** 页将页面上的短文本字符串居中，并使用由一对圆角矩形组成的帧来包围该文本。 [`FramedTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/FramedTextPage.cs)类显示其工作原理。

在 SkiaSharp 中，可以使用 `SKPaint` 类来设置文本和字体特性，但也可以使用它来获取呈现的文本大小。 下面的 `PaintSurface` 事件处理程序的开头调用两种不同 `MeasureText` 的方法。 第一次 [`MeasureText`](xref:SkiaSharp.SKPaint.MeasureText(System.String)) 调用具有一个简单 `string` 参数，并基于当前字体特性返回文本的像素宽度。 然后，该程序 `TextSize` `SKPaint` 基于呈现的宽度、当前 `TextSize` 属性和显示区域的宽度计算对象的新属性。 此计算旨在设置， `TextSize` 以便在屏幕宽度的90% 呈现文本字符串：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    string str = "Hello SkiaSharp!";

    // Create an SKPaint object to display the text
    SKPaint textPaint = new SKPaint
    {
        Color = SKColors.Chocolate
    };

    // Adjust TextSize property so text is 90% of screen width
    float textWidth = textPaint.MeasureText(str);
    textPaint.TextSize = 0.9f * info.Width * textPaint.TextSize / textWidth;

    // Find the text bounds
    SKRect textBounds = new SKRect();
    textPaint.MeasureText(str, ref textBounds);
    ...
}
```

第二个 [`MeasureText`](xref:SkiaSharp.SKPaint.MeasureText(System.String,SkiaSharp.SKRect@)) 调用具有 `SKRect` 参数，因此它同时获取所呈现文本的宽度和高度。 `Height`此值的属性 `SKRect` 取决于文本字符串中是否存在大写字母、升部和下行字母。 `Height`例如，为文本字符串 "mom"、"cat" 和 "dog" 报告不同的值。

`Left`结构的和 `Top` 属性 `SKRect` 指示呈现文本的左上角的坐标（如果 `DrawText` 使用 X 和 Y 位置的调用显示文本）。 例如，当此程序在 iPhone 7 模拟器上运行时， `TextSize` 会将值90.6254 指定为首次调用后的计算结果 `MeasureText` 。 `SKRect`从的第二次调用获取的值 `MeasureText` 具有以下属性值：

- `Left` = 6
- `Top` = &ndash;68
- `Width` = 664.8214
- `Height` = 88;

请记住，传递给方法的 X 和 Y 坐标 `DrawText` 指定基线文本的左侧。 `Top`值指示文本在基线上方扩展了68像素， (将68从 88) 20 像素减去到基线的下方。 `Left`值6指示文本在调用中 X 值的右侧开始为六个像素 `DrawText` 。 这允许正常的字符间间距。 如果要在显示的左上角显示文本 snugly，请将这些 `Left` 值和值的负值 `Top` 作为 X 和 Y 坐标 `DrawText` （在本示例中为 &ndash; 6 和68）。

`SKRect`结构定义了几个方便的属性和方法，其中一些属性和方法用于处理程序的其余部分 `PaintSurface` 。 `MidX`和 `MidY` 值指示矩形中心的坐标。  (在 iPhone 7 示例中，这些值为338.4107 和 &ndash; 24。 ) 下面的代码使用这些值来实现最简单的坐标计算，使其在显示器上居中文本：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    // Calculate offsets to center the text on the screen
    float xText = info.Width / 2 - textBounds.MidX;
    float yText = info.Height / 2 - textBounds.MidY;

    // And draw the text
    canvas.DrawText(str, xText, yText, textPaint);
    ...
}
```

`SKImageInfo`信息结构还定义 [`Rect`](xref:SkiaSharp.SKImageInfo.Rect) 类型的属性 `SKRect` ，因此您还可以计算 `xText` 和如下所 `yText` 示：

```csharp
float xText = info.Rect.MidX - textBounds.MidX;
float yText = info.Rect.MidY - textBounds.MidY;
```

`PaintSurface`处理程序将通过两次调用来结束 `DrawRoundRect` ，这两者都需要的参数 `SKRect` 。 此 `SKRect` 值基于 `SKRect` 从方法获取的值 `MeasureText` ，但不能相同。 首先，需要稍大一些，以便圆角矩形不会在文本边缘上绘制。 其次，需要在空间中移动，使 `Left` 和 `Top` 值对应于要定位矩形的左上角。 这两个作业由定义的 [`Offset`](xref:SkiaSharp.SKRect.Offset*) 和 [`Inflate`](xref:SkiaSharp.SKRect.Inflate*) 方法完成 `SKRect` ：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    // Create a new SKRect object for the frame around the text
    SKRect frameRect = textBounds;
    frameRect.Offset(xText, yText);
    frameRect.Inflate(10, 10);

    // Create an SKPaint object to display the frame
    SKPaint framePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 5,
        Color = SKColors.Blue
    };

    // Draw one frame
    canvas.DrawRoundRect(frameRect, 20, 20, framePaint);

    // Inflate the frameRect and draw another
    frameRect.Inflate(10, 10);
    framePaint.Color = SKColors.DarkBlue;
    canvas.DrawRoundRect(frameRect, 30, 30, framePaint);
}
```

接下来，该方法的其余部分为直接。 它为边框创建另一个 `SKPaint` 对象，并调用 `DrawRoundRect` 两次。 第二个调用使用放大了10个像素的矩形。 第一次调用指定20像素的拐角半径。 第二个角半径为30像素，因此它们看起来是并行的：

 [![分帧文本页的三向屏幕截图](text-images/framedtext-small.png)](text-images/framedtext-large.png#lightbox "分帧文本页的三向屏幕截图")

你可以通过横向打开手机或模拟器来查看文本和帧大小的增加。

如果只需要在屏幕上居中某些文本，则可以在不测量文本的情况下执行此操作。 相反，请将 [`TextAlign`](xref:SkiaSharp.SKPaint.TextAlign) 的属性设置 `SKPaint` 为枚举成员 [`SKTextAlign.Center`](xref:SkiaSharp.SKTextAlign) 。 在方法中指定 X 坐标后，将 `DrawText` 指示该文本的水平中心的位置。 如果将屏幕的中点传递给 `DrawText` 方法，则文本将水平居中且 *接近* 垂直居中，因为基线将垂直居中。

可以像处理任何其他图形对象一样处理文本。 一个简单的选项是显示文本字符的轮廓：

[![大纲文本页的三屏幕截图](text-images/outlinedtext-small.png)](text-images/outlinedtext-large.png#lightbox "大纲文本页的三向屏幕截图")

只需通过将对象的 "常规" `Style` 属性 `SKPaint` 从其默认设置更改 `SKPaintStyle.Fill` 为 `SKPaintStyle.Stroke` ，并通过指定笔划宽度来完成此操作。 `PaintSurface`**分级**显示的文本页的处理程序显示了它是如何完成的：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    string text = "OUTLINE";

    // Create an SKPaint object to display the text
    SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 1,
        FakeBoldText = true,
        Color = SKColors.Blue
    };

    // Adjust TextSize property so text is 95% of screen width
    float textWidth = textPaint.MeasureText(text);
    textPaint.TextSize = 0.95f * info.Width * textPaint.TextSize / textWidth;

    // Find the text bounds
    SKRect textBounds = new SKRect();
    textPaint.MeasureText(text, ref textBounds);

    // Calculate offsets to center the text on the screen
    float xText = info.Width / 2 - textBounds.MidX;
    float yText = info.Height / 2 - textBounds.MidY;

    // And draw the text
    canvas.DrawText(text, xText, yText, textPaint);
}
```

另一个常见图形对象是位图。 这是 " [**SkiaSharp 位图**](../bitmaps/index.md)" 一节中详细介绍的一个大型主题，但下一篇文章是 [**SkiaSharp 中的位图基础**](bitmaps.md)，它提供简要简介。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)