---
title: 翻译转换
description: 本文介绍如何使用转换转换在应用程序中转换 SkiaSharp 图形 Xamarin.Forms ，并使用示例代码对此进行演示。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: BD28ADA1-49F9-44E2-A548-46024A29882F
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 31a76e160186caec74665b7615323eb8e60b698a
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562322"
---
# <a name="the-translate-transform"></a>翻译转换

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_了解如何使用转换转换 SkiaSharp 图形_

SkiaSharp 中最简单的转换类型 *是转换转换* 或 *转换* 转换。 此转换以水平和垂直方向变换图形对象。 从某种意义上讲，转换是最不必要的转换，因为您通常只需更改在绘图函数中使用的坐标即可实现同样的效果。 但是，在呈现路径时，所有坐标都封装在路径中，因此，更轻松地应用转换转换来移动整个路径。

翻译对于动画和简单的文本效果也很有用：

![文本阴影、engraving 和带平移的浮雕](translate-images/translateexample.png)

[`Translate`](xref:SkiaSharp.SKCanvas.Translate(System.Single,System.Single))中的方法 `SKCanvas` 具有两个参数，这两个参数会导致在水平方向和垂直方向移动图形对象：

```csharp
public void Translate (Single dx, Single dy)
```

这些参数可能为负数。 第二种 [`Translate`](xref:SkiaSharp.SKCanvas.Translate(SkiaSharp.SKPoint)) 方法将两个翻译值合并为一个 `SKPoint` 值：

```csharp
public void Translate (SKPoint point)
```

[**SkiaSharpForms**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)示例程序的**累积转换**页面说明了对方法的多个调用 `Translate` 是累积的。 [`AccumulatedTranslatePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AccumulatedTranslatePage.cs)类显示同一个矩形的20个版本，每个版本都与前一个矩形的偏移量刚好足够，因此它们沿对角线拉伸。 `PaintSurface`事件处理程序如下所示：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint strokePaint = new SKPaint())
    {
        strokePaint.Color = SKColors.Black;
        strokePaint.Style = SKPaintStyle.Stroke;
        strokePaint.StrokeWidth = 3;

        int rectangleCount = 20;
        SKRect rect = new SKRect(0, 0, 250, 250);
        float xTranslate = (info.Width - rect.Width) / (rectangleCount - 1);
        float yTranslate = (info.Height - rect.Height) / (rectangleCount - 1);

        for (int i = 0; i < rectangleCount; i++)
        {
            canvas.DrawRect(rect, strokePaint);
            canvas.Translate(xTranslate, yTranslate);
        }
    }
}
```

连续的矩形滴页面：

[![累积转换页面的三向屏幕截图](translate-images/accumulatedtranslate-small.png)](translate-images/accumulatedtranslate-large.png#lightbox "累积转换页面的三向屏幕截图")

如果累积的平移因子为 `dx` 和 `dy` ，并且在绘图函数中指定的点为 (`x` ， `y`) ，则图形对象将呈现 (`x'` ， `y'`) ，其中：

x ' = x + dx

y "= y + dy

这称为 *转换公式* 。 新的和的默认值 `dx` `dy` 为 `SKCanvas` 0。

由于 " **翻译文本效果** " 页面所示，通常使用转换转换来实现阴影效果和类似的技术。 下面是 `PaintSurface` 类中处理程序的相关部分 [`TranslateTextEffectsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TranslateTextEffectsPage.cs) ：

```csharp
float textSize = 150;

using (SKPaint textPaint = new SKPaint())
{
    textPaint.Style = SKPaintStyle.Fill;
    textPaint.TextSize = textSize;
    textPaint.FakeBoldText = true;

    float x = 10;
    float y = textSize;

    // Shadow
    canvas.Translate(10, 10);
    textPaint.Color = SKColors.Black;
    canvas.DrawText("SHADOW", x, y, textPaint);
    canvas.Translate(-10, -10);
    textPaint.Color = SKColors.Pink;
    canvas.DrawText("SHADOW", x, y, textPaint);

    y += 2 * textSize;

    // Engrave
    canvas.Translate(-5, -5);
    textPaint.Color = SKColors.Black;
    canvas.DrawText("ENGRAVE", x, y, textPaint);
    canvas.ResetMatrix();
    textPaint.Color = SKColors.White;
    canvas.DrawText("ENGRAVE", x, y, textPaint);

    y += 2 * textSize;

    // Emboss
    canvas.Save();
    canvas.Translate(5, 5);
    textPaint.Color = SKColors.Black;
    canvas.DrawText("EMBOSS", x, y, textPaint);
    canvas.Restore();
    textPaint.Color = SKColors.White;
    canvas.DrawText("EMBOSS", x, y, textPaint);
}
```

在这三个示例的每个示例中， `Translate` 都将调用以显示文本，以使其与和变量给定的位置偏移 `x` `y` 。 然后，在其他颜色中再次显示文本，而不会产生转换效果：

[!["翻译文本效果" 页面的三向屏幕截图](translate-images/translatetexteffects-small.png)](translate-images/translatetexteffects-large.png#lightbox ""翻译文本效果" 页面的三向屏幕截图")

这三个示例都说明了取消调用的不同方法 `Translate` ：

第一个示例只是 `Translate` 再次调用，但带有负值。 由于 `Translate` 调用是累积性的，因此这一序列的调用只是将总转换还原为默认值零。

第二个示例调用 [`ResetMatrix`](xref:SkiaSharp.SKCanvas.ResetMatrix) 。 这将导致所有转换返回到其默认状态。

第三个示例 `SKCanvas` 通过调用保存对象的状态 [`Save`](xref:SkiaSharp.SKCanvas.Save) ，然后通过调用来还原状态 [`Restore`](xref:SkiaSharp.SKCanvas.Restore) 。 这是操作一系列绘图操作的转换的最通用方法。 这些 `Save` `Restore` 函数和调用类似于 stack 的函数：可以调用多次 `Save` ，然后 `Restore` 在反向序列中调用以返回到以前的状态。 `Save`方法返回一个整数，你可以将该整数传递到 [`RestoreToCount`](xref:SkiaSharp.SKCanvas.RestoreToCount*) 来有效地调用多次 `Restore` 。 [`SaveCount`](xref:SkiaSharp.SKCanvas.SaveCount)属性返回当前在堆栈上保存的状态数。

你还可以使用 [`SKAutoCanvasRestore`](xref:SkiaSharp.SKAutoCanvasRestore) 类来还原画布状态。 此类的构造函数应在语句中调用; 在 `using` 块的末尾自动还原画布状态 `using` 。

但是，您不必担心从处理程序的一次调用 `PaintSurface` 到接下来的转换。 每次新调用都 `PaintSurface` 提供 `SKCanvas` 具有默认转换的全新对象。

转换的另一种常见用途 `Translate` 是呈现一个视觉对象，该对象最初是使用便于绘制的坐标创建的。 例如，你可能想要指定一个 (0，0) 点的模拟时钟的坐标。 然后，可以使用转换在所需的位置显示时钟。 此方法在 [**Hendecagram Array**] 页中进行了演示。 [`HendecagramArrayPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/HendecagramArrayPage.cs)类首先创建一个 `SKPath` 用于11指星形的对象。 `HendecagramPath`对象被定义为公共、静态和只读，以便可以从其他演示程序访问它。 它在静态构造函数中创建：

```csharp
public class HendecagramArrayPage : ContentPage
{
    ...
    public static readonly SKPath HendecagramPath;

    static HendecagramArrayPage()
    {
        // Create 11-pointed star
        HendecagramPath = new SKPath();
        for (int i = 0; i < 11; i++)
        {
            double angle = 5 * i * 2 * Math.PI / 11;
            SKPoint pt = new SKPoint(100 * (float)Math.Sin(angle),
                                    -100 * (float)Math.Cos(angle));
            if (i == 0)
            {
                HendecagramPath.MoveTo(pt);
            }
            else
            {
                HendecagramPath.LineTo(pt);
            }
        }
        HendecagramPath.Close();
    }
}
```

如果星形的中心是 (0，0) 点，则星形的所有点都位于围绕该点的圆。 每个点都是角度的正弦和余弦值的组合，该角度增加了360度的 5/11ths。  (还可以通过将角度增加 2/11、3/11ths 或 4/11 的圆，来创建11尖星形。 ) 该圆圈的半径设置为100。

如果此路径在呈现时不带任何转换，则中心将定位在的左上角 `SKCanvas` ，且只有一个季度会可见。 `PaintSurface`使用的处理程序将 `HendecagramPage` 使用 `Translate` 来平铺包含多个星形副本的画布，其中每个副本都随机着色：

```csharp
public class HendecagramArrayPage : ContentPage
{
    Random random = new Random();
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            for (int x = 100; x < info.Width + 100; x += 200)
                for (int y = 100; y < info.Height + 100; y += 200)
                {
                    // Set random color
                    byte[] bytes = new byte[3];
                    random.NextBytes(bytes);
                    paint.Color = new SKColor(bytes[0], bytes[1], bytes[2]);

                    // Display the hendecagram
                    canvas.Save();
                    canvas.Translate(x, y);
                    canvas.DrawPath(HendecagramPath, paint);
                    canvas.Restore();
                }
        }
    }
}

```

结果如下：

[![Hendecagram 数组页的三向屏幕截图](translate-images/hendecagramarray-small.png)](translate-images/hendecagramarray-large.png#lightbox "Hendecagram 数组页的三向屏幕截图")

动画通常涉及转换。 " **Hendecagram 动画** " 页在圆圈中移动了11形星形。 [`HendecagramAnimationPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/HendecagramAnimationPage.cs)类以某些字段开头，并重写 `OnAppearing` 和 `OnDisappearing` 方法以启动和停止 Xamarin.Forms 计时器：

```csharp
public class HendecagramAnimationPage : ContentPage
{
    const double cycleTime = 5000;      // in milliseconds

    SKCanvasView canvasView;
    Stopwatch stopwatch = new Stopwatch();
    bool pageIsActive;
    float angle;

    public HendecagramAnimationPage()
    {
        Title = "Hedecagram Animation";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    protected override void OnAppearing()
    {
        base.OnAppearing();
        pageIsActive = true;
        stopwatch.Start();

        Device.StartTimer(TimeSpan.FromMilliseconds(33), () =>
        {
            double t = stopwatch.Elapsed.TotalMilliseconds % cycleTime / cycleTime;
            angle = (float)(360 * t);
            canvasView.InvalidateSurface();

            if (!pageIsActive)
            {
                stopwatch.Stop();
            }

            return pageIsActive;
        });
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();
        pageIsActive = false;
    }
    ...
}
```

此 `angle` 字段每5秒从0度到360度进行动画处理。 `PaintSurface`处理程序通过 `angle` 两种方式使用属性：指定方法中颜色的色调 `SKColor.FromHsl` ，并将指定为和方法的参数以 `Math.Sin` `Math.Cos` 控制星形的位置：

```csharp
public class HendecagramAnimationPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.Translate(info.Width / 2, info.Height / 2);
        float radius = (float)Math.Min(info.Width, info.Height) / 2 - 100;

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Fill;
            paint.Color = SKColor.FromHsl(angle, 100, 50);

            float x = radius * (float)Math.Sin(Math.PI * angle / 180);
            float y = -radius * (float)Math.Cos(Math.PI * angle / 180);
            canvas.Translate(x, y);
            canvas.DrawPath(HendecagramPage.HendecagramPath, paint);
        }
    }
}
```

`PaintSurface`处理程序 `Translate` 两次调用方法，首先转换为画布的中心，然后转换为围绕 (0，0) 的圆的周长。 圆的半径设置为尽可能大，同时仍然在页面范围内保留星形：

[![Hendecagram 动画页面的三向屏幕截图](translate-images/hendecagramanimation-small.png)](translate-images/hendecagramanimation-large.png#lightbox "Hendecagram 动画页面的三向屏幕截图")

请注意，星形在页面中心旋转时保持相同的方向。 它根本不会旋转。 这是一个用于旋转转换的作业。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)