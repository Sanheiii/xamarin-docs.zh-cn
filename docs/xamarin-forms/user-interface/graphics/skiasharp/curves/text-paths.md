---
title: SkiaSharp 中的路径和文本
description: 本文探讨了 SkiaSharp 路径和文本的交集，并通过示例代码对此进行了演示。
ms.prod: xamarin
ms.assetid: C14C07F6-4A84-4A8C-BDB4-CD61FBF0F79B
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 08/01/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c78d6777db4e8dcc3a8302d418f1c87440dc90a5
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562049"
---
# <a name="paths-and-text-in-skiasharp"></a>SkiaSharp 中的路径和文本

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_探索路径和文本的交集_

在新式图形系统中，文本字体是字符轮廓的集合，通常由二次贝塞尔曲线定义。 因此，许多新式图形系统都包含用于将文本字符转换为图形路径的工具。

你已经了解到，你可以对文本字符的轮廓进行描边，并对其进行填充。 这使您可以使用特定的笔划宽度显示这些字符轮廓，甚至显示路径 [**效果**](effects.md) 一文中所述的路径效果。 但也可以将字符字符串转换为 `SKPath` 对象。 这意味着，可以将文本轮廓用于使用 " [**使用路径和区域剪辑**](clipping.md) " 一文中所述的技术进行剪辑。

除了使用路径效果来为字符轮廓描边，还可以创建基于从字符串派生的路径的路径效果，甚至可以将这两个效果组合在一起：

![文本路径效果](text-paths-images/pathsandtextsample.png)

在前面的文章中 [**，您**](effects.md)了解了如何 [`GetFillPath`](xref:SkiaSharp.SKPaint.GetFillPath(SkiaSharp.SKPath,SkiaSharp.SKPath,SkiaSharp.SKRect,System.Single)) `SKPaint` 获取描边轨迹的轮廓。 你还可以将此方法用于派生自字符轮廓的路径。

最后，本文演示路径和文本的另一交集：的 [`DrawTextOnPath`](xref:SkiaSharp.SKCanvas.DrawTextOnPath(System.String,SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPaint)) 方法 `SKCanvas` 允许您显示文本字符串，使文本基线跟随曲线路径。

## <a name="text-to-path-conversion"></a>文本到路径的转换

[`GetTextPath`](xref:SkiaSharp.SKPaint.GetTextPath(System.String,System.Single,System.Single)) `SKPaint` 将字符串转换为对象的方法 `SKPath` ：

```csharp
public SKPath GetTextPath (String text, Single x, Single y)
```

`x`和 `y` 参数指示文本左侧基线的起始点。 它们在此处与的方法扮演相同的角色 `DrawText` `SKCanvas` 。 在路径中，文本左侧的基线的坐标为 x，y)  (。

`GetTextPath`如果只是想要填充或勾画生成的路径，则该方法为多余。 普通 `DrawText` 方法允许执行此操作。 此 `GetTextPath` 方法对于涉及路径的其他任务更有用。

其中一项任务是剪辑。 " **剪辑文本** " 页基于 "代码" 一词的字符轮廓创建剪切路径。 此路径将拉伸到页面大小，以剪切包含 **剪辑文本** 源代码的图像的位图：

[![剪贴文本页的三向屏幕截图](text-paths-images/clippingtext-small.png)](text-paths-images/clippingtext-large.png#lightbox "剪贴文本页的三向屏幕截图")

[`ClippingTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ClippingTextPage.cs)类构造函数在解决方案的**媒体**文件夹中加载存储为嵌入资源的位图：

```csharp
public class ClippingTextPage : ContentPage
{
    SKBitmap bitmap;

    public ClippingTextPage()
    {
        Title = "Clipping Text";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        string resourceID = "SkiaSharpFormsDemos.Media.PageOfCode.png";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }
    ...
}
```

`PaintSurface`处理程序首先创建适合于 `SKPaint` 文本的对象。 `Typeface` `TextSize` 即使对于此特定的应用程序，属性也设置为 " `TextSize` 完全任意"。 另请注意，没有任何 `Style` 设置。

`TextSize`和 `Style` 属性设置并不是必需的，因为此 `SKPaint` 对象仅用于 `GetTextPath` 使用文本字符串 "CODE" 的调用。 然后，处理程序将测量生成的 `SKPath` 对象，并应用三个变换以使其居中，并将其缩放到页面的大小。 然后，可以将路径设置为剪切路径：

```csharp
public class ClippingTextPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Blue);

        using (SKPaint paint = new SKPaint())
        {
            paint.Typeface = SKTypeface.FromFamilyName(null, SKTypefaceStyle.Bold);
            paint.TextSize = 10;

            using (SKPath textPath = paint.GetTextPath("CODE", 0, 0))
            {
                // Set transform to center and enlarge clip path to window height
                SKRect bounds;
                textPath.GetTightBounds(out bounds);

                canvas.Translate(info.Width / 2, info.Height / 2);
                canvas.Scale(info.Width / bounds.Width, info.Height / bounds.Height);
                canvas.Translate(-bounds.MidX, -bounds.MidY);

                // Set the clip path
                canvas.ClipPath(textPath);
            }
        }

        // Reset transforms
        canvas.ResetMatrix();

        // Display bitmap to fill window but maintain aspect ratio
        SKRect rect = new SKRect(0, 0, info.Width, info.Height);
        canvas.DrawBitmap(bitmap,
            rect.AspectFill(new SKSize(bitmap.Width, bitmap.Height)));
    }
}
```

设置剪切路径后，可以显示位图，并将其剪裁到字符轮廓。 请注意，使用的 [`AspectFill`](xref:SkiaSharp.SKRect.AspectFill(SkiaSharp.SKSize)) 方法 `SKRect` 会计算用于填充页面的矩形，同时保留纵横比。

" **文本路径效果** " 页将单个 "&" 字符转换为路径，以创建一维路径效果。 然后，使用带有此路径效果的 "画图" 对象来勾画相同字符的较大版本的轮廓：

[![文本路径效果页面的三向屏幕截图](text-paths-images/textpatheffect-small.png)](text-paths-images/textpatheffect-large.png#lightbox "文本路径效果页面的三向屏幕截图")

类中的大部分工作都 [`TextPathEffectPath`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TextPathEffectPage.cs) 出现在字段和构造函数中。 `SKPaint`定义为字段的两个对象用于两个不同的目的：第一个名为) 的 (`textPathPaint` 用于将与50的符号转换为一 `TextSize` 维路径效果的路径。 第二个 (`textPaint`) 用于显示与该路径效果更大的 "and" 版本。 出于此原因， `Style` 此第二个 paint 对象的设置为 `Stroke` ，但 `StrokeWidth` 未设置属性，因为在使用1d 路径效果时，此属性不是必需的：

```csharp
public class TextPathEffectPage : ContentPage
{
    const string character = "@";
    const float littleSize = 50;

    SKPathEffect pathEffect;

    SKPaint textPathPaint = new SKPaint
    {
        TextSize = littleSize
    };

    SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black
    };

    public TextPathEffectPage()
    {
        Title = "Text Path Effect";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Get the bounds of textPathPaint
        SKRect textPathPaintBounds = new SKRect();
        textPathPaint.MeasureText(character, ref textPathPaintBounds);

        // Create textPath centered around (0, 0)
        SKPath textPath = textPathPaint.GetTextPath(character,
                                                    -textPathPaintBounds.MidX,
                                                    -textPathPaintBounds.MidY);
        // Create the path effect
        pathEffect = SKPathEffect.Create1DPath(textPath, littleSize, 0,
                                               SKPath1DPathEffectStyle.Translate);
    }
    ...
}
```

构造函数首先使用 `textPathPaint` 对象，用50来度量与号 `TextSize` 。 然后，将该矩形的中心坐标的负片传递给 `GetTextPath` 方法，以将文本转换为路径。 生成的路径在字符中心有 (0，0) 点，这非常适用于1D 路径效果。

您可能认为， `SKPathEffect` 在构造函数末尾创建的对象可以设置为的 `PathEffect` 属性， `textPaint` 而不是保存为字段。 但这种方法并不能正常工作，因为它会导致 `MeasureText` 处理程序中调用的结果失真 `PaintSurface` ：

```csharp
public class TextPathEffectPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Set textPaint TextSize based on screen size
        textPaint.TextSize = Math.Min(info.Width, info.Height);

        // Do not measure the text with PathEffect set!
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(character, ref textBounds);

        // Coordinates to center text on screen
        float xText = info.Width / 2 - textBounds.MidX;
        float yText = info.Height / 2 - textBounds.MidY;

        // Set the PathEffect property and display text
        textPaint.PathEffect = pathEffect;
        canvas.DrawText(character, xText, yText, textPaint);
    }
}
```

该 `MeasureText` 调用用于使字符在页面上居中。 为避免出现问题，在 `PathEffect` 文本经过度量之后但在显示之前，该属性设置为 paint 对象。

## <a name="outlines-of-character-outlines"></a>字符轮廓的轮廓

通常情况下， [`GetFillPath`](xref:SkiaSharp.SKPaint.GetFillPath(SkiaSharp.SKPath,SkiaSharp.SKPath,SkiaSharp.SKRect,System.Single)) 方法是 `SKPaint` 通过应用画图属性将一个路径转换为另一个路径，最明显的是笔划宽度和路径效果。 在没有路径效果的情况下使用时，会 `GetFillPath` 有效地创建一个描述另一个路径的路径。 在[**路径效果**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md)文章的 "路径" 页的 "**点击概述**" 部分中对此进行了演示。

你还可以调用 `GetFillPath` 从返回的路径， `GetTextPath` 但最初你可能不能完全确定其外观。

" **字符轮廓轮廓** " 页演示了该技术。 所有相关代码都在类的 `PaintSurface` 处理程序中 [`CharacterOutlineOutlinesPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CharacterOutlineOutlinesPage.cs) 。

构造函数首先创建一个 `SKPaint` 名为的对象，该对象 `textPaint` `TextSize` 基于页的大小。 使用方法将其转换为路径 `GetTextPath` 。 用于 `GetTextPath` 有效地在屏幕上居中显示路径的坐标参数：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint())
    {
        // Set Style for the character outlines
        textPaint.Style = SKPaintStyle.Stroke;

        // Set TextSize based on screen size
        textPaint.TextSize = Math.Min(info.Width, info.Height);

        // Measure the text
        SKRect textBounds = new SKRect();
        textPaint.MeasureText("@", ref textBounds);

        // Coordinates to center text on screen
        float xText = info.Width / 2 - textBounds.MidX;
        float yText = info.Height / 2 - textBounds.MidY;

        // Get the path for the character outlines
        using (SKPath textPath = textPaint.GetTextPath("@", xText, yText))
        {
            // Create a new path for the outlines of the path
            using (SKPath outlinePath = new SKPath())
            {
                // Convert the path to the outlines of the stroked path
                textPaint.StrokeWidth = 25;
                textPaint.GetFillPath(textPath, outlinePath);

                // Stroke that new path
                using (SKPaint outlinePaint = new SKPaint())
                {
                    outlinePaint.Style = SKPaintStyle.Stroke;
                    outlinePaint.StrokeWidth = 5;
                    outlinePaint.Color = SKColors.Red;

                    canvas.DrawPath(outlinePath, outlinePaint);
                }
            }
        }
    }
}
```

`PaintSurface`然后，处理程序将创建一个名为的新路径 `outlinePath` 。 这将成为对的调用中的目标路径 `GetFillPath` 。 如果 `StrokeWidth` 属性为25，则 `outlinePath` 说明25像素宽路径的轮廓将文本字符描边。 然后，此路径以红色显示，笔划宽度为5：

[![字符轮廓轮廓页面的三向屏幕截图](text-paths-images/characteroutlineoutlines-small.png)](text-paths-images/characteroutlineoutlines-large.png#lightbox "字符轮廓轮廓页面的三向屏幕截图")

仔细查看并看到重叠，其中路径轮廓构成了尖角。 这是此过程的普通项目。

## <a name="text-along-a-path"></a>沿路径的文本

文本通常显示在水平基线上。 文本可以旋转以垂直或对角运行，但基线仍是一条直线。

但有时，您希望文本沿曲线运行。 这是的方法的目的 [`DrawTextOnPath`](xref:SkiaSharp.SKCanvas.DrawTextOnPath(System.String,SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPaint)) `SKCanvas` ：

```csharp
public Void DrawTextOnPath (String text, SKPath path, Single hOffset, Single vOffset, SKPaint paint)
```

在第一个参数中指定的文本将沿指定为第二个参数的路径运行。 可以使用参数从路径开头的偏移量开始文本 `hOffset` 。 通常，路径构成文本基线：文本升部位于路径的一侧，而文本下行字母位于另一侧。 但是，可以将路径中的文本基线偏移自 `vOffset` 变量。

此方法没有任何工具可提供有关将的属性设置为的指导， `TextSize` `SKPaint` 使文本大小完全可以从路径开头到结尾。 有时您可以自行判断文本大小。 其他时候，你将需要使用路径度量函数，详见下一篇有关 [**路径信息和枚举**](information.md)的文章。

**圆形文本**程序环绕圆圈环绕文本。 确定圆的周长很简单，因此可以很容易地调整文本大小以精确地调整大小。 `PaintSurface`类的处理程序 [`CircularTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CircularTextPage.cs) 基于页面大小计算圆的半径。 该圆形变为 `circularPath` ：

```csharp
public class CircularTextPage : ContentPage
{
    const string text = "xt in a circle that shapes the te";
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath circularPath = new SKPath())
        {
            float radius = 0.35f * Math.Min(info.Width, info.Height);
            circularPath.AddCircle(info.Width / 2, info.Height / 2, radius);

            using (SKPaint textPaint = new SKPaint())
            {
                textPaint.TextSize = 100;
                float textWidth = textPaint.MeasureText(text);
                textPaint.TextSize *= 2 * 3.14f * radius / textWidth;

                canvas.DrawTextOnPath(text, circularPath, 0, 0, textPaint);
            }
        }
    }
}
```

然后，对的 `TextSize` 属性 `textPaint` 进行调整，使文本宽度与圆的周长匹配：

[![圆形文本页的三向屏幕截图](text-paths-images/circulartext-small.png)](text-paths-images/circulartext-large.png#lightbox "圆形文本页的三向屏幕截图")

文本本身也被选为有点循环：单词 "circle" 既是句子的主题，也是 prepositional 短语的对象。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)