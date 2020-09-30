---
title: 旋转转换
description: 本文探讨了 SkiaSharp 旋转转换可以实现的效果和动画，并通过示例代码对此进行了演示。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: CBB3CD72-4377-4EA3-A768-0C4228229FC2
author: davidbritch
ms.author: dabritch
ms.date: 03/23/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2a6e19d25cefb4b8fcd870a9b590f129b2d9d85b
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91557876"
---
# <a name="the-rotate-transform"></a>旋转转换

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_探索 SkiaSharp 旋转转换可能出现的效果和动画_

旋转变换后，SkiaSharp 图形对象会断开与水平轴和垂直轴对齐的约束：

![围绕中心旋转的文本](rotate-images/rotateexample.png)

若要围绕点旋转图形对象 (0，0) ，SkiaSharp 同时支持 [`RotateDegrees`](xref:SkiaSharp.SKCanvas.RotateDegrees(System.Single)) 方法和 [`RotateRadians`](xref:SkiaSharp.SKCanvas.RotateRadians(System.Single)) 方法：

```csharp
public void RotateDegrees (Single degrees)

public Void RotateRadians (Single radians)
```

360度的圆与 twoπ弧度相同，因此可以轻松地在两个单位之间进行转换。 使用哪种便捷。 .NET 类中的所有三角函数均 [`Math`](xref:System.Math) 使用弧度单位。

旋转以顺时针角度增加角度。  (尽管在笛卡尔坐标系统上的旋转按惯例逆时针旋转，但顺时针旋转与相对于在 SkiaSharp 中增加的 Y 坐标保持一致。 ) 负值和大于360度的角。

用于旋转的转换公式比用于平移和缩放的公式更复杂。 对于α角度，转换公式为：

x ' = x • cos (α) – y • sin (α)    

y ' = x • sin (α) + y • cos (α) 

**基本旋转**页面演示了 `RotateDegrees` 方法。 [**BasicRotate.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicRotatePage.xaml.cs)文件显示其基线位于页面上的某些文本，并基于 `Slider` 范围为–360到360的进行旋转。 下面是处理程序的相关部分 `PaintSurface` ：

```csharp
using (SKPaint textPaint = new SKPaint
{
    Style = SKPaintStyle.Fill,
    Color = SKColors.Blue,
    TextAlign = SKTextAlign.Center,
    TextSize = 100
})
{
    canvas.RotateDegrees((float)rotateSlider.Value);
    canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
}
```

由于旋转围绕画布的左上角，因此对于在此程序中设置的大多数角度，文本将在屏幕上旋转：

[![基本旋转页面的三向屏幕截图](rotate-images/basicrotate-small.png)](rotate-images/basicrotate-large.png#lightbox "基本旋转页面的三向屏幕截图")

通常，您需要使用以下版本的和方法围绕指定的透视点旋转中心内容 [`RotateDegrees`](xref:SkiaSharp.SKCanvas.RotateDegrees(System.Single,System.Single,System.Single)) [`RotateRadians`](xref:SkiaSharp.SKCanvas.RotateRadians(System.Single,System.Single,System.Single)) ：

```csharp
public void RotateDegrees (Single degrees, Single px, Single py)

public void RotateRadians (Single radians, Single px, Single py)
```

**居中旋转**页面与**基本旋转**类似，不同之处在于使用的扩展版本 `RotateDegrees` 将旋转中心设置为用于放置文本的同一点：

```csharp
using (SKPaint textPaint = new SKPaint
{
    Style = SKPaintStyle.Fill,
    Color = SKColors.Blue,
    TextAlign = SKTextAlign.Center,
    TextSize = 100
})
{
    canvas.RotateDegrees((float)rotateSlider.Value, info.Width / 2, info.Height / 2);
    canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
}
```

现在，文本围绕用于放置文本的点进行旋转，这是文本基线的水平中心：

[![居中旋转页面的三向屏幕截图](rotate-images/centeredrotate-small.png)](rotate-images/centeredrotate-large.png#lightbox "居中旋转页面的三向屏幕截图")

与方法的居中版本一样 `Scale` ，调用的居中版本 `RotateDegrees` 是一个快捷方式。 下面是方法：

```csharp
RotateDegrees (degrees, px, py);
```

该调用等效于以下内容：

```csharp
canvas.Translate(px, py);
canvas.RotateDegrees(degrees);
canvas.Translate(-px, -py);
```

你将发现，你有时可以将 `Translate` 调用与调用组合在一起 `Rotate` 。 例如，下面是中的 `RotateDegrees` 和 `DrawText` 在 **居中旋转** 页中调用。

```csharp
canvas.RotateDegrees((float)rotateSlider.Value, info.Width / 2, info.Height / 2);
canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
```

此 `RotateDegrees` 调用等效于两个 `Translate` 调用，而不是居中 `RotateDegrees` ：

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.Translate(-info.Width / 2, -info.Height / 2);
canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
```

在 `DrawText` 特定位置上显示文本的调用等效于对 `Translate` 该位置的调用，后跟 `DrawText` (0，0) 的点：

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.Translate(-info.Width / 2, -info.Height / 2);
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.DrawText(Title, 0, 0, textPaint);
```

两个连续的 `Translate` 调用会取消彼此的连接：

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.DrawText(Title, 0, 0, textPaint);
```

从概念上讲，这两个转换的应用顺序与代码中出现的顺序相反。 `DrawText`调用在画布的左上角显示文本。 `RotateDegrees`调用会相对于左上角旋转该文本。 然后， `Translate` 调用将文本移动到画布的中心。

通常有多种方法可用于合并旋转和转换。 **旋转的文本**页会创建以下显示内容：

[![旋转文本页的三向屏幕截图](rotate-images/rotatedtext-small.png)](rotate-images/rotatedtext-large.png#lightbox "旋转文本页的三向屏幕截图")

下面是 `PaintSurface` 类的处理程序 [`RotatedTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/RotatedTextPage.cs) ：

```csharp
static readonly string text = "    ROTATE";
...
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint
    {
        Color = SKColors.Black,
        TextSize = 72
    })
    {
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        SKRect textBounds = new SKRect();
        textPaint.MeasureText(text, ref textBounds);
        float yText = yCenter - textBounds.Height / 2 - textBounds.Top;

        for (int degrees = 0; degrees < 360; degrees += 30)
        {
            canvas.Save();
            canvas.RotateDegrees(degrees, xCenter, yCenter);
            canvas.DrawText(text, xCenter, yText, textPaint);
            canvas.Restore();
        }
    }
}

```

`xCenter`和 `yCenter` 值指示画布的中心。 `yText`该值与此值为小偏移量。 此值是对文本进行定位所需的 Y 坐标，使其在页面上真正垂直居中。 `for`然后，循环将基于画布中心设置旋转。 旋转的增量为30度。 使用值绘制文本 `yText` 。 值中 "旋转" 字样之前的空格数 `text` 被确定为凭经验，这12个文本字符串之间的连接似乎是一个 dodecagon。

简化此代码的一种方法是在调用后每次循环，将旋转角度增加30度 `DrawText` 。 这样就无需调用 `Save` 和 `Restore` 。 请注意，该 `degrees` 变量不再在块体中使用 `for` ：

```csharp
for (int degrees = 0; degrees < 360; degrees += 30)
{
    canvas.DrawText(text, xCenter, yText, textPaint);
    canvas.RotateDegrees(30, xCenter, yCenter);
}

```

还可以通过调用来使用的简单窗体来 `RotateDegrees` `Translate` 将所有内容移至画布中心：

```csharp
float yText = -textBounds.Height / 2 - textBounds.Top;

canvas.Translate(xCenter, yCenter);

for (int degrees = 0; degrees < 360; degrees += 30)
{
    canvas.DrawText(text, 0, yText, textPaint);
    canvas.RotateDegrees(30);
}
```

修改后的 `yText` 计算不再合并 `yCenter` 。 现在，此调用会使 `DrawText` 文本在画布的顶部垂直居中。

由于转换在概念上的应用方式与代码中出现的方式相反，因此通常可以从更多的全局转换开始，后跟更多本地转换。 这通常是合并旋转和平移的最简单方法。

例如，假设您要绘制一个图形对象，该对象围绕其中心旋转，就像在其轴上旋转的行星。 不过，您也希望此对象围绕屏幕的中心旋转，就像围绕太阳旋转的行星。

为此，您可以将对象定位在画布的左上角，然后使用动画围绕该角来旋转。 接下来，将对象水平平移，如 orbital 半径。 现在，还应应用另一个动画旋转，也围绕原点。 这使对象围绕角旋转。 现在，转换为画布的中心。

下面是 `PaintSurface` 按相反顺序包含这些转换调用的处理程序：

```csharp
float revolveDegrees, rotateDegrees;
...
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint fillPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Red
    })
    {
        // Translate to center of canvas
        canvas.Translate(info.Width / 2, info.Height / 2);

        // Rotate around center of canvas
        canvas.RotateDegrees(revolveDegrees);

        // Translate horizontally
        float radius = Math.Min(info.Width, info.Height) / 3;
        canvas.Translate(radius, 0);

        // Rotate around center of object
        canvas.RotateDegrees(rotateDegrees);

        // Draw a square
        canvas.DrawRect(new SKRect(-50, -50, 50, 50), fillPaint);
    }
}
```

`revolveDegrees`和 `rotateDegrees` 字段已进行动画处理。 此程序使用基于类的不同动画技术 Xamarin.Forms [`Animation`](xref:Xamarin.Forms.Animation) 。  (此类在[*创建移动 Xamarin.Forms 应用*](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)) 的第22章中进行了介绍， `OnAppearing` 重写使用 `Animation` 回叫方法创建两个对象，然后对其调用 `Commit` 动画持续时间：

```csharp
protected override void OnAppearing()
{
    base.OnAppearing();

    new Animation((value) => revolveDegrees = 360 * (float)value).
        Commit(this, "revolveAnimation", length: 10000, repeat: () => true);

    new Animation((value) =>
    {
        rotateDegrees = 360 * (float)value;
        canvasView.InvalidateSurface();
    }).Commit(this, "rotateAnimation", length: 1000, repeat: () => true);
}
```

第一个 `Animation` 对象 `revolveDegrees` 从0度到360度的动画效果超过10秒。 第二个动画 `rotateDegrees` 从0度到360度，每隔1秒进行一次动画处理，并使图面使其生成另一个对 `PaintSurface` 处理程序的调用。 `OnDisappearing`重写将取消这两个动画：

```csharp
protected override void OnDisappearing()
{
    base.OnDisappearing();
    this.AbortAnimation("revolveAnimation");
    this.AbortAnimation("rotateAnimation");
}
```

由于后面的一篇文章中将介绍一个更具吸引力的模拟时钟程序，因此 (很好的 **模拟时钟** 程序) 使用旋转绘制时钟的分钟和小时标记并旋转。 该程序根据中心点使用任意坐标系统来绘制时钟，该圆位于中心点 (0，0) ，半径为100。 它使用平移和缩放功能在页面上展开并居中该圆形。

`Translate`和 `Scale` 调用将全局应用于时钟，因此在初始化对象之后将调用它们 `SKPaint` ：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint strokePaint = new SKPaint())
    using (SKPaint fillPaint = new SKPaint())
    {
        strokePaint.Style = SKPaintStyle.Stroke;
        strokePaint.Color = SKColors.Black;
        strokePaint.StrokeCap = SKStrokeCap.Round;

        fillPaint.Style = SKPaintStyle.Fill;
        fillPaint.Color = SKColors.Gray;

        // Transform for 100-radius circle centered at origin
        canvas.Translate(info.Width / 2f, info.Height / 2f);
        canvas.Scale(Math.Min(info.Width / 200f, info.Height / 200f));
        ...
    }
}
```

有60个不同大小的标记，它们必须在一个围绕时钟的圆圈中绘制。 该 `DrawCircle` 调用将在 (0，-90) 点绘制圆圈，该点相对于时钟中心对应12:00。 调用将在 `RotateDegrees` 每个刻度线后将旋转角度增加6度。 `angle`变量仅用于确定是否绘制大圆圈或小圆圈：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
        // Hour and minute marks
        for (int angle = 0; angle < 360; angle += 6)
        {
            canvas.DrawCircle(0, -90, angle % 30 == 0 ? 4 : 2, fillPaint);
            canvas.RotateDegrees(6);
        }
    ...
    }
}
```

最后， `PaintSurface` 处理程序将获取当前时间并计算小时、分钟和秒的旋转度数。 每个手在12:00 位置绘制，使旋转角度相对于：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
        DateTime dateTime = DateTime.Now;

        // Hour hand
        strokePaint.StrokeWidth = 20;
        canvas.Save();
        canvas.RotateDegrees(30 * dateTime.Hour + dateTime.Minute / 2f);
        canvas.DrawLine(0, 0, 0, -50, strokePaint);
        canvas.Restore();

        // Minute hand
        strokePaint.StrokeWidth = 10;
        canvas.Save();
        canvas.RotateDegrees(6 * dateTime.Minute + dateTime.Second / 10f);
        canvas.DrawLine(0, 0, 0, -70, strokePaint);
        canvas.Restore();

        // Second hand
        strokePaint.StrokeWidth = 2;
        canvas.Save();
        canvas.RotateDegrees(6 * dateTime.Second);
        canvas.DrawLine(0, 10, 0, -80, strokePaint);
        canvas.Restore();
    }
}
```

虽然这种情况相当粗糙，但时钟当然是有效的：

[!["有缺陷的模拟时钟文本" 页的三向屏幕截图](rotate-images/uglyanalogclock-small.png)](rotate-images/uglyanalogclock-large.png#lightbox "极差模拟页面的三向屏幕截图")

若要获得更具吸引力的时钟，请参阅文章 [**SkiaSharp 中的 SVG 路径数据**](../curves/path-data.md)。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)