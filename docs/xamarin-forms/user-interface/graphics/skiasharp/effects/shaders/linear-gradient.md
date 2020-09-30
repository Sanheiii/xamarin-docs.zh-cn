---
title: SkiaSharp 线性渐变
description: 了解如何使用两种颜色的渐变混合绘制线条或填充区域。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 20A2A8C4-FEB7-478D-BF57-C92E26117B6A
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2cc0806af28360cf4bf2bb7e382e8d0a423abab9
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91555523"
---
# <a name="the-skiasharp-linear-gradient"></a>SkiaSharp 线性渐变

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

[`SKPaint`](xref:SkiaSharp.SKPaint)类定义一个 [`Color`](xref:SkiaSharp.SKPaint.Color) 属性，该属性用于对线条进行描边或用纯色填充区域。 您还可以通过渐变线条或用 _渐变_填充区域，这是一种逐渐混合的颜色：

![线性渐变示例](linear-gradient-images/LinearGradientSample.png "线性渐变示例")

最基本的渐变类型是 _线性_ 渐变。 颜色混合出现 _在) 从_ 一个点到另一个点的 (。 垂直于渐变线的线条具有相同的颜色。 使用这两种静态方法之一创建线性渐变 [`SKShader.CreateLinearGradient`](xref:SkiaSharp.SKShader.CreateLinearGradient*) 。 这两个重载的区别在于其中一个重载包含矩阵转换，而另一个重载不包含矩阵转换。 

这些方法返回一个类型为的对象 [`SKShader`](xref:SkiaSharp.SKShader) ，该对象的 [`Shader`](xref:SkiaSharp.SKPaint.Shader) 属性设置为 `SKPaint` 。 如果该 `Shader` 属性为非 null，则它将重写 `Color` 属性。 描边的任何线条或使用此对象填充的任何区域 `SKPaint` 都基于渐变而不是纯色。

> [!NOTE]
> `Shader`如果 `SKPaint` 在调用中包含对象，则将忽略属性 `DrawBitmap` 。 您可以使用的 `Color` 属性 `SKPaint` 来设置显示位图 (的透明度级别，如) [显示 SkiaSharp 位图](../../bitmaps/displaying.md#displaying-in-pixel-dimensions) 一文中所述），但不能使用 `Shader` 属性来显示具有渐变透明度的位图。 其他方法可用于显示具有渐变透明度的位图：文章 [SkiaSharp 循环渐变](circular-gradients.md#radial-gradients-for-masking) 和 [SkiaSharp 合成模式和混合模式](../blend-modes/porter-duff.md#gradient-transparency-and-transitions)。

## <a name="corner-to-corner-gradients"></a>角部渐变

通常，线性渐变将从矩形的一个角延伸到另一个角。 如果起始点是矩形的左上角，则渐变可以扩展：

- 垂直于左下角
- 水平到右上角
- 对角向右下角

对角线性渐变在 SkiaSharp 着色器的第一页和[**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)示例的**其他效果**部分中进行了演示。 **角部渐变**页面 `SKCanvasView` 在其构造函数中创建。 `PaintSurface`处理程序 `SKPaint` 在语句中创建一个对象 `using` ，然后定义一个中心在画布中的300像素的方形矩形：

```csharp
public class CornerToCornerGradientPage : ContentPage
{
    ···
    public CornerToCornerGradientPage ()
    {
        Title = "Corner-to-Corner Gradient";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
        ···
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            // Create 300-pixel square centered rectangle
            float x = (info.Width - 300) / 2;
            float y = (info.Height - 300) / 2;
            SKRect rect = new SKRect(x, y, x + 300, y + 300);

            // Create linear gradient from upper-left to lower-right
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(rect.Left, rect.Top),
                                new SKPoint(rect.Right, rect.Bottom),
                                new SKColor[] { SKColors.Red, SKColors.Blue },
                                new float[] { 0, 1 },
                                SKShaderTileMode.Repeat);

            // Draw the gradient on the rectangle
            canvas.DrawRect(rect, paint);
            ···
        }
    }
}
```

的 `Shader` 属性 `SKPaint` `SKShader` 从静态方法分配了返回值 `SKShader.CreateLinearGradient` 。 这五个参数如下所示：

- 渐变的起始点，在此处设置为矩形的左上角
- 渐变的结束点，在此处设置为该矩形的右下角
- 构成渐变的两种或多种颜色的数组
- 值的数组 `float` ，指示渐变线中颜色的相对位置
- 枚举的成员 [`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode) ，指示渐变的行为如何超出渐变线的末端

创建渐变对象之后，该方法会 `DrawRect` 使用包含着色器的对象绘制300像素的正方形矩形 `SKPaint` 。 此处它在 iOS、Android 和通用 Windows 平台 (UWP) 上运行：

[![角部到角渐变](linear-gradient-images/CornerToCornerGradient.png "角部到角渐变")](linear-gradient-images/CornerToCornerGradient-Large.png#lightbox)

渐变线由指定为前两个自变量的两个点定义。 请注意，这些点相对于 _画布_ ，而 _不_ 是与渐变一起显示的图形对象。 沿渐变线，颜色逐渐从左上角的红色过渡到右下角的蓝色。 垂直于渐变线的任何线条都具有恒定颜色。

`float`指定为第四个参数的值数组与颜色数组之间具有一对一的对应关系。 值指示在渐变线上出现这些颜色的相对位置。 此处，0表示 `Red` 在渐变线的开头发生，1表示 `Blue` 在行尾发生。 数字必须是升序，并且应在0到1的范围内。 如果它们不在该范围内，则将其调整为在该范围内。

数组中的两个值可以设置为0和1以外的值。 试试看：

```csharp
new float[] { 0.25f, 0.75f }
```

现在，渐变线的第一个季度是纯红色，最后一个季度是纯蓝色。 红色和蓝色的组合仅限于渐变线的中半部分。

通常情况下，您需要将这些位置值的空间均匀地分布在0和1之间。 如果是这种情况，只需 `null` 作为的第四个参数提供 `CreateLinearGradient` 。

尽管此渐变是在300像素正方形矩形的两个角之间定义的，但它并不限于填充该矩形。 **角部到角渐变**页面包含一些额外的代码，这些代码可响应页面上的点击或鼠标单击。 `drawBackground` `true` 每点击一次，该字段将在和之间切换 `false` 。 如果值为 `true` ，则 `PaintSurface` 处理程序将使用相同的 `SKPaint` 对象填充整个画布，然后绘制一个黑色矩形，指示较小的矩形： 

```csharp
public class CornerToCornerGradientPage : ContentPage
{
    bool drawBackground;

    public CornerToCornerGradientPage ()
    {
        ···
        TapGestureRecognizer tap = new TapGestureRecognizer();
        tap.Tapped += (sender, args) =>
        {
            drawBackground ^= true;
            canvasView.InvalidateSurface();
        };
        canvasView.GestureRecognizers.Add(tap);
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        using (SKPaint paint = new SKPaint())
        {
            ···
            if (drawBackground)
            {
                // Draw the gradient on the whole canvas
                canvas.DrawRect(info.Rect, paint);

                // Outline the smaller rectangle
                paint.Shader = null;
                paint.Style = SKPaintStyle.Stroke;
                paint.Color = SKColors.Black;
                canvas.DrawRect(rect, paint);
            }
        }
    }
}
```

点击屏幕后会显示以下内容：

[![角部到角梯度已满](linear-gradient-images/CornerToCornerGradientFull.png "角部到角梯度已满")](linear-gradient-images/CornerToCornerGradientFull-Large.png#lightbox)

请注意，渐变在定义渐变线的点之外的同一模式中重复出现。 发生此重复的原因是的最后一个参数 `CreateLinearGradient` 为 `SKShaderTileMode.Repeat` 。  (稍后会显示其他选项。 ) 

另请注意，用来指定渐变线的点不是唯一的。 垂直于渐变线的线条具有相同的颜色，因此，您可以为相同的效果指定无限多的渐变线条。 例如，在使用水平渐变填充矩形时，可以指定左上角和右上角，或指定左上角和右下角，或者使用和与这些线条并行的任意两个点。

## <a name="interactively-experiment"></a>交互式试验

可以通过 **交互式线性渐变** 页面以交互方式试验线性渐变。 本页使用 `InteractivePage` [**第三种方法绘制圆弧**](../../curves/arcs.md)一文中引入的类。 `InteractivePage` 处理 [`TouchEffect`](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md) 事件以维护 `TouchPoint` 对象的集合，这些对象可使用手指或鼠标移动。

XAML 文件将附加 `TouchEffect` 到的父 `SKCanvasView` ，还包括一个 `Picker` ，它允许您选择枚举的三个成员之一 [`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode) ：

```xaml
<local:InteractivePage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       xmlns:local="clr-namespace:SkiaSharpFormsDemos"
                       xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
                       xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
                       xmlns:tt="clr-namespace:TouchTracking"
                       x:Class="SkiaSharpFormsDemos.Effects.InteractiveLinearGradientPage"
                       Title="Interactive Linear Gradient">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid BackgroundColor="White"
              Grid.Row="0">
            <skiaforms:SKCanvasView x:Name="canvasView"
                                    PaintSurface="OnCanvasViewPaintSurface" />
            <Grid.Effects>
                <tt:TouchEffect Capture="True"
                                TouchAction="OnTouchEffectAction" />
            </Grid.Effects>
        </Grid>

        <Picker x:Name="tileModePicker" 
                Grid.Row="1"
                Title="Shader Tile Mode" 
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKShaderTileMode}">
                    <x:Static Member="skia:SKShaderTileMode.Clamp" />
                    <x:Static Member="skia:SKShaderTileMode.Repeat" />
                    <x:Static Member="skia:SKShaderTileMode.Mirror" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>
    </Grid>
</local:InteractivePage>
```

代码隐藏文件中的构造函数 `TouchPoint` 为线性渐变的起点和终点创建两个对象。 `PaintSurface`处理程序定义三种颜色的数组， (用于从红色到绿色到蓝色) 的渐变，并从获取当前的 `SKShaderTileMode` `Picker` ：

```csharp
public partial class InteractiveLinearGradientPage : InteractivePage
{
    public InteractiveLinearGradientPage ()
    {
        InitializeComponent ();

        touchPoints = new TouchPoint[2];

        for (int i = 0; i < 2; i++)
        { 
            touchPoints[i] = new TouchPoint
            {
                Center = new SKPoint(100 + i * 200, 100 + i * 200)
            };
        }

        InitializeComponent();
        baseCanvasView = canvasView;
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKColor[] colors = { SKColors.Red, SKColors.Green, SKColors.Blue };
        SKShaderTileMode tileMode =
            (SKShaderTileMode)(tileModePicker.SelectedIndex == -1 ?
                                        0 : tileModePicker.SelectedItem);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateLinearGradient(touchPoints[0].Center,
                                                         touchPoints[1].Center,
                                                         colors,
                                                         null,
                                                         tileMode);
            canvas.DrawRect(info.Rect, paint);
        }
        ···
    }
}
```

`PaintSurface`处理程序 `SKShader` 从所有这些信息创建对象，并使用它来为整个画布着色。 值的数组 `float` 设置为 `null` 。 否则，若要使三种颜色相等，可以将该参数设置为值0、0.5 和1的数组。

处理程序的大部分 `PaintSurface` 专门用于显示多个对象：触控点为轮廓圆圈、渐变线条和与触控点的渐变线垂直的线条：

```csharp
public partial class InteractiveLinearGradientPage : InteractivePage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        // Display the touch points here rather than by TouchPoint
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Black;
            paint.StrokeWidth = 3;

            foreach (TouchPoint touchPoint in touchPoints)
            {
                canvas.DrawCircle(touchPoint.Center, touchPoint.Radius, paint);
            }

            // Draw gradient line connecting touchpoints
            canvas.DrawLine(touchPoints[0].Center, touchPoints[1].Center, paint);

            // Draw lines perpendicular to the gradient line
            SKPoint vector = touchPoints[1].Center - touchPoints[0].Center;
            float length = (float)Math.Sqrt(Math.Pow(vector.X, 2) +
                                            Math.Pow(vector.Y, 2));
            vector.X /= length;
            vector.Y /= length;
            SKPoint rotate90 = new SKPoint(-vector.Y, vector.X);
            rotate90.X *= 200;
            rotate90.Y *= 200;

            canvas.DrawLine(touchPoints[0].Center, 
                            touchPoints[0].Center + rotate90, 
                            paint);

            canvas.DrawLine(touchPoints[0].Center,
                            touchPoints[0].Center - rotate90,
                            paint);

            canvas.DrawLine(touchPoints[1].Center,
                            touchPoints[1].Center + rotate90,
                            paint);

            canvas.DrawLine(touchPoints[1].Center,
                            touchPoints[1].Center - rotate90,
                            paint);
        }
    }
}
```

连接两个 touchpoints 的渐变线非常容易绘制，但垂直线需要更多的工作。 渐变线条转换为矢量，并规范化为具有一个单位的长度，然后旋转90度。 然后为该向量指定长度为200像素的。 它用于绘制四条从触控点延伸到渐变线条的直线。

垂直线与渐变的开头和结尾重合。 超过这些行的情况取决于枚举的设置 `SKShaderTileMode` ：

[![交互式线性渐变](linear-gradient-images/InteractiveLinearGradient.png "交互式线性渐变")](linear-gradient-images/InteractiveLinearGradient-Large.png#lightbox)

这三个屏幕截图显示了的三个不同值的结果 [`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode) 。 IOS 屏幕快照显示了 `SKShaderTileMode.Clamp` ，它只是扩展渐变边框上的颜色。 `SKShaderTileMode.Repeat`Android 屏幕截图中的选项显示如何重复渐变模式。 `SKShaderTileMode.Mirror`UWP 屏幕截图中的选项还会重复模式，但每次都会反转模式，导致不会中断颜色。

## <a name="gradients-on-gradients"></a>渐变渐变

`SKShader`类不定义除之外的公共属性或方法 `Dispose` 。 `SKShader`因此，通过其静态方法创建的对象是不可变的。 即使您对两个不同的对象使用相同的渐变，您也可能希望略微改变梯度。 为此，您需要创建一个新的 `SKShader` 对象。

" **渐变文本** " 页显示带有类似渐变的彩色文本和后台：

[![渐变文本](linear-gradient-images/GradientText.png "渐变文本")](linear-gradient-images/GradientText-Large.png#lightbox)

渐变的唯一区别是起点和终点。 用于显示文本的渐变基于文本边框边角上的两个点。 对于背景，这两个点都基于整个画布。 代码如下：

```csharp
public class GradientTextPage : ContentPage
{
    const string TEXT = "GRADIENT";

    public GradientTextPage ()
    {
        Title = "Gradient Text";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            // Create gradient for background
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(0, 0),
                                new SKPoint(info.Width, info.Height),
                                new SKColor[] { new SKColor(0x40, 0x40, 0x40),
                                                new SKColor(0xC0, 0xC0, 0xC0) },
                                null,
                                SKShaderTileMode.Clamp);

            // Draw background
            canvas.DrawRect(info.Rect, paint);

            // Set TextSize to fill 90% of width
            paint.TextSize = 100;
            float width = paint.MeasureText(TEXT);
            float scale = 0.9f * info.Width / width;
            paint.TextSize *= scale;

            // Get text bounds
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);

            // Calculate offsets to center the text on the screen
            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2 - textBounds.MidY;

            // Shift textBounds by that amount
            textBounds.Offset(xText, yText);

            // Create gradient for text
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(textBounds.Left, textBounds.Top),
                                new SKPoint(textBounds.Right, textBounds.Bottom),
                                new SKColor[] { new SKColor(0x40, 0x40, 0x40),
                                                new SKColor(0xC0, 0xC0, 0xC0) },
                                null,
                                SKShaderTileMode.Clamp);

            // Draw text
            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

`Shader` `SKPaint` 首先设置对象的属性，以显示用于覆盖背景的渐变。 渐变点设置为画布的左上角和右下角。

该代码设置 `TextSize` 对象的属性 `SKPaint` ，以使文本显示在画布宽度的90%。 文本边界用于计算要 `xText` `yText` 传递给方法的值并将其作为 `DrawText` 中心文本。

但是，第二次调用的渐变点 `CreateLinearGradient` 必须引用文本在显示时相对于画布的左上角和右下角。 这是通过将矩形移位 `textBounds` 相同的和值来实现的 `xText` `yText` ：

```csharp
textBounds.Offset(xText, yText);
```

现在，可以使用矩形的左上角和右下角来设置渐变的起始点和结束点。

## <a name="animating-a-gradient"></a>对渐变进行动画处理

可以通过多种方式对渐变进行动画处理。 一种方法是对起点和终点进行动画处理。 **渐变动画**页面在画布上中心的圆圈内移动两个点。 此圆的半径为画布宽度或高度的一半，以较小者为准。 起点和终点在此圆圈上彼此相反，渐变从白色到黑色，并带有 `Mirror` 磁贴模式：

[![渐变动画](linear-gradient-images/GradientAnimation.png "渐变动画")](linear-gradient-images/GradientAnimation-Large.png#lightbox)

构造函数创建 `SKCanvasView` 。 `OnAppearing`和 `OnDisappearing` 方法处理动画逻辑：

```csharp
public class GradientAnimationPage : ContentPage
{
    SKCanvasView canvasView;
    bool isAnimating;
    double angle;
    Stopwatch stopwatch = new Stopwatch();

    public GradientAnimationPage()
    {
        Title = "Gradient Animation";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    protected override void OnAppearing()
    {
        base.OnAppearing();

        isAnimating = true;
        stopwatch.Start();
        Device.StartTimer(TimeSpan.FromMilliseconds(16), OnTimerTick);
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();

        stopwatch.Stop();
        isAnimating = false;
    }

    bool OnTimerTick()
    {
        const int duration = 3000;
        angle = 2 * Math.PI * (stopwatch.ElapsedMilliseconds % duration) / duration;
        canvasView.InvalidateSurface();

        return isAnimating;
    }
    ···
}
```

`OnTimerTick`方法计算 `angle` 每3秒从0到2π的动画值。 

下面是计算这两个渐变点的一种方法。 `SKPoint`将计算一个名为的值 `vector` ，以将其从画布中心延伸到圆圈半径上的某个点。 此向量的方向基于正弦值和角度的余弦值。 然后计算两个相反的渐变点：通过从中心点中减去该向量来计算一个点，并通过将矢量添加到中心点来计算其他点：

```csharp
public class GradientAnimationPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            SKPoint center = new SKPoint(info.Rect.MidX, info.Rect.MidY);
            int radius = Math.Min(info.Width, info.Height) / 2;
            SKPoint vector = new SKPoint((float)(radius * Math.Cos(angle)),
                                         (float)(radius * Math.Sin(angle)));

            paint.Shader = SKShader.CreateLinearGradient(
                                center - vector,
                                center + vector,
                                new SKColor[] { SKColors.White, SKColors.Black },
                                null,
                                SKShaderTileMode.Mirror);

            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

略有不同的方法需要较少的代码。 此方法使用 [`SKShader.CreateLinearGradient`](xref:SkiaSharp.SKShader.CreateLinearGradient(SkiaSharp.SKPoint,SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) 带有矩阵转换的重载方法作为最后一个参数。 此方法是 [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) 示例中的版本：

```csharp
public class GradientAnimationPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(0, 0),
                                info.Width < info.Height ? new SKPoint(info.Width, 0) : 
                                                           new SKPoint(0, info.Height),
                                new SKColor[] { SKColors.White, SKColors.Black },
                                new float[] { 0, 1 },
                                SKShaderTileMode.Mirror,
                                SKMatrix.MakeRotation((float)angle, info.Rect.MidX, info.Rect.MidY));

            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

如果画布的宽度小于高度，则将两个渐变点设置为 (0，0) ， (`info.Width` ，0) 。 作为最后一个参数传递的旋转转换，以 `CreateLinearGradient` 有效地在屏幕中心周围旋转这两个点。

请注意，如果角度为0，则没有旋转，两个渐变点为画布的左上角和右上角。 这些点不是计算得出的相同渐变点，如前一次调用中所示 `CreateLinearGradient` 。 但这些点 _平行_ 于 bisects 画布中心的水平渐变线，它们会产生相同的渐变效果。

**彩虹渐变**

**彩虹渐变**页面从画布的左上角到右下角绘制一个彩虹。 但这种彩虹的渐变与真实的彩虹无关。 它是直接的，而不是弯曲的，但它基于8个 HSL (色调-饱和度-亮度) 颜色，这些颜色通过从0到360的色调值进行循环确定：

```csharp
SKColor[] colors = new SKColor[8];

for (int i = 0; i < colors.Length; i++)
{
    colors[i] = SKColor.FromHsl(i * 360f / (colors.Length - 1), 100, 50);
}
```

该代码属于 `PaintSurface` 下面所示的处理程序。 处理程序首先创建一个路径，该路径定义从画布的左上角到右下角的半侧多边形：

```csharp
public class RainbowGradientPage : ContentPage
{
    public RainbowGradientPage ()
    {
        Title = "Rainbow Gradient";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath path = new SKPath())
        {
            float rainbowWidth = Math.Min(info.Width, info.Height) / 2f;

            // Create path from upper-left to lower-right corner
            path.MoveTo(0, 0);
            path.LineTo(rainbowWidth / 2, 0);
            path.LineTo(info.Width, info.Height - rainbowWidth / 2);
            path.LineTo(info.Width, info.Height);
            path.LineTo(info.Width - rainbowWidth / 2, info.Height);
            path.LineTo(0, rainbowWidth / 2);
            path.Close();

            using (SKPaint paint = new SKPaint())
            {
                SKColor[] colors = new SKColor[8];

                for (int i = 0; i < colors.Length; i++)
                {
                    colors[i] = SKColor.FromHsl(i * 360f / (colors.Length - 1), 100, 50);
                }

                paint.Shader = SKShader.CreateLinearGradient(
                                    new SKPoint(0, rainbowWidth / 2), 
                                    new SKPoint(rainbowWidth / 2, 0),
                                    colors,
                                    null,
                                    SKShaderTileMode.Repeat);

                canvas.DrawPath(path, paint);
            }
        }
    }
}
```

方法中的两个渐变点 `CreateLinearGradient` 基于定义此路径的两个点：两个点接近左上角。 第一个位于画布的上边缘，第二个位于画布的左边缘。 结果如下：

[![彩虹梯度错误](linear-gradient-images/RainbowGradientFaulty.png "彩虹梯度错误")](linear-gradient-images/RainbowGradientFaulty-Large.png#lightbox)

这是一个有趣的图像，但并不是那么重要。 问题在于，当创建线性渐变时，恒定颜色的线条垂直于渐变线条。 渐变线条基于该图形触及顶部和左侧的点，并且该线条通常不垂直于向右下角延伸的图形的边缘。 仅当画布是正方形时，此方法才有效。

若要创建适当的彩虹渐变，渐变线必须垂直于彩虹的边缘。 这是一个更复杂的计算。 必须定义平行于图形长边的矢量。 矢量旋转了90度，以使其垂直于该边。 然后，通过将其与相乘，使其成为图形的宽度 `rainbowWidth` 。 这两个渐变点根据图形一侧的点进行计算，而该点与矢量相加。 下面是在[**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)示例的**彩虹渐变**页面中显示的代码：

```csharp
public class RainbowGradientPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        using (SKPath path = new SKPath())
        {
            ···
            using (SKPaint paint = new SKPaint())
            {
                ···
                // Vector on lower-left edge, from top to bottom 
                SKPoint edgeVector = new SKPoint(info.Width - rainbowWidth / 2, info.Height) - 
                                     new SKPoint(0, rainbowWidth / 2);

                // Rotate 90 degrees counter-clockwise:
                SKPoint gradientVector = new SKPoint(edgeVector.Y, -edgeVector.X);

                // Normalize
                float length = (float)Math.Sqrt(Math.Pow(gradientVector.X, 2) +
                                                Math.Pow(gradientVector.Y, 2));
                gradientVector.X /= length;
                gradientVector.Y /= length;

                // Make it the width of the rainbow
                gradientVector.X *= rainbowWidth;
                gradientVector.Y *= rainbowWidth;

                // Calculate the two points
                SKPoint point1 = new SKPoint(0, rainbowWidth / 2);
                SKPoint point2 = point1 + gradientVector;

                paint.Shader = SKShader.CreateLinearGradient(point1,
                                                             point2,
                                                             colors,
                                                             null,
                                                             SKShaderTileMode.Repeat);

                canvas.DrawPath(path, paint);
            }
        }
    }
}
```

现在，彩虹色与图对齐：

[![彩虹渐变](linear-gradient-images/RainbowGradient.png "彩虹渐变")](linear-gradient-images/RainbowGradient-Large.png#lightbox)

**无穷色**

" **无限大颜色** " 页中也使用彩虹渐变。 此页使用 [**第三种贝塞尔曲线类型**](../../curves/beziers.md#bezier-curve-approximation-to-circular-arcs)一文中所述的路径对象来绘制无穷号。 然后，将使用一个动画彩虹渐变对图像进行着色，并跨图像持续扫描。

构造函数创建 `SKPath` 描述无穷符号的对象。 创建路径后，构造函数还可以获取路径的矩形边界。 然后计算一个名为的值 `gradientCycleLength` 。 如果渐变基于矩形的左上角和右下角 `pathBounds` ，则此 `gradientCycleLength` 值为渐变模式的总水平宽度：

```csharp
public class InfinityColorsPage : ContentPage
{
    ···
    SKCanvasView canvasView;

    // Path information 
    SKPath infinityPath;
    SKRect pathBounds;
    float gradientCycleLength;

    // Gradient information
    SKColor[] colors = new SKColor[8];
    ···

    public InfinityColorsPage ()
    {
        Title = "Infinity Colors";

        // Create path for infinity sign
        infinityPath = new SKPath();
        infinityPath.MoveTo(0, 0);                                  // Center
        infinityPath.CubicTo(  50,  -50,   95, -100,  150, -100);   // To top of right loop
        infinityPath.CubicTo( 205, -100,  250,  -55,  250,    0);   // To far right of right loop
        infinityPath.CubicTo( 250,   55,  205,  100,  150,  100);   // To bottom of right loop
        infinityPath.CubicTo(  95,  100,   50,   50,    0,    0);   // Back to center  
        infinityPath.CubicTo( -50,  -50,  -95, -100, -150, -100);   // To top of left loop
        infinityPath.CubicTo(-205, -100, -250,  -55, -250,    0);   // To far left of left loop
        infinityPath.CubicTo(-250,   55, -205,  100, -150,  100);   // To bottom of left loop
        infinityPath.CubicTo( -95,  100, - 50,   50,    0,    0);   // Back to center
        infinityPath.Close();

        // Calculate path information 
        pathBounds = infinityPath.Bounds;
        gradientCycleLength = pathBounds.Width +
            pathBounds.Height * pathBounds.Height / pathBounds.Width;

        // Create SKColor array for gradient
        for (int i = 0; i < colors.Length; i++)
        {
            colors[i] = SKColor.FromHsl(i * 360f / (colors.Length - 1), 100, 50);
        }

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }
    ···
}
```

构造函数还 `colors` 为彩虹和对象创建数组 `SKCanvasView` 。

重写 `OnAppearing` 和方法会对 `OnDisappearing` 动画产生开销。 此 `OnTimerTick` 方法将 `offset` 字段从0动画处理为 `gradientCycleLength` 每两秒一次：

```csharp
public class InfinityColorsPage : ContentPage
{
    ···
    // For animation
    bool isAnimating;
    float offset;
    Stopwatch stopwatch = new Stopwatch();
    ···

    protected override void OnAppearing()
    {
        base.OnAppearing();

        isAnimating = true;
        stopwatch.Start();
        Device.StartTimer(TimeSpan.FromMilliseconds(16), OnTimerTick);
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();

        stopwatch.Stop();
        isAnimating = false;
    }

    bool OnTimerTick()
    {
        const int duration = 2;     // seconds
        double progress = stopwatch.Elapsed.TotalSeconds % duration / duration;
        offset = (float)(gradientCycleLength * progress);
        canvasView.InvalidateSurface();

        return isAnimating;
    }
    ···
}
```

最后， `PaintSurface` 处理程序将呈现无穷符号。 由于路径包含围绕 (0，0) 中心点的负坐标和正坐标，因此 `Translate` 画布上的变换用于将其移到中心。 转换转换后跟一个 `Scale` 转换，该转换应用可使无穷符号尽可能大的缩放因子，同时仍保持在画布宽度和高度的95% 范围内。 

请注意， `STROKE_WIDTH` 常数添加到了路径边框的宽度和高度。 该路径将使用此宽度的线条进行描边，因此，呈现的无穷大小的大小将在所有四个边的宽度上增加一半：

```csharp
public class InfinityColorsPage : ContentPage
{
    const int STROKE_WIDTH = 50;
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Set transforms to shift path to center and scale to canvas size
        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(0.95f * 
            Math.Min(info.Width / (pathBounds.Width + STROKE_WIDTH),
                     info.Height / (pathBounds.Height + STROKE_WIDTH)));

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.StrokeWidth = STROKE_WIDTH;
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(pathBounds.Left, pathBounds.Top),
                                new SKPoint(pathBounds.Right, pathBounds.Bottom),
                                colors,
                                null,
                                SKShaderTileMode.Repeat,
                                SKMatrix.MakeTranslation(offset, 0));

            canvas.DrawPath(infinityPath, paint);
        }
    }
}
```

查看作为的前两个参数传递的点 `SKShader.CreateLinearGradient` 。 这些点基于原始路径边界矩形。 第一点是 (&ndash; 250， &ndash; 100) ，第二个点是 (250，100) 。 在 SkiaSharp 内部，这些点受到当前画布转换的干扰，使其与显示的无穷符号正确对齐。

如果没有的最后一个参数 `CreateLinearGradient` ，您将看到一个从无穷符号的左上角到右下角的彩虹梯度。  (实际上，渐变从边框的左上角到右下角进行扩展。 呈现的无穷符号大于边界矩形的 `STROKE_WIDTH` 所有边的值的一半。 因为渐变在开始和结束时都是红色的，而渐变是用来创建的 `SKShaderTileMode.Repeat` ，所以差异并不明显。 ) 

对于的最后一个参数 `CreateLinearGradient` ，渐变模式将在图像上持续扫描：

[![无穷色](linear-gradient-images/InfinityColors.png "无穷色")](linear-gradient-images/InfinityColors-Large.png#lightbox)

## <a name="transparency-and-gradients"></a>透明度和渐变

用于渐变的颜色可以包含透明度。 渐变可以从一种颜色渐变到透明，而不是渐变。 

此方法可用于某些有趣的效果。 其中一个典型示例显示了图形对象及其反射：

[![反射渐变](linear-gradient-images/ReflectionGradient.png "反射渐变")](linear-gradient-images/ReflectionGradient-Large.png#lightbox)

倒置文本的颜色为渐变，其顶部为50% 透明，位于底部，完全透明。 这些透明度级别与值0x80 和0的 alpha 值相关联。

`PaintSurface`**反射渐变**页中的处理程序将文本的大小缩放为画布宽度的90%。 然后，它将计算 `xText` 和 `yText` 值以将文本水平居中，但置于相对于页面垂直中心的基线上：

```csharp
public class ReflectionGradientPage : ContentPage
{
    const string TEXT = "Reflection";

    public ReflectionGradientPage ()
    {
        Title = "Reflection Gradient";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            // Set text color to blue
            paint.Color = SKColors.Blue;

            // Set text size to fill 90% of width
            paint.TextSize = 100;
            float width = paint.MeasureText(TEXT);
            float scale = 0.9f * info.Width / width;
            paint.TextSize *= scale;

            // Get text bounds
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);

            // Calculate offsets to position text above center
            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2;

            // Draw unreflected text
            canvas.DrawText(TEXT, xText, yText, paint);

            // Shift textBounds to match displayed text
            textBounds.Offset(xText, yText);

            // Use those offsets to create a gradient for the reflected text
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(0, textBounds.Top),
                                new SKPoint(0, textBounds.Bottom),
                                new SKColor[] { paint.Color.WithAlpha(0),
                                                paint.Color.WithAlpha(0x80) },
                                null,
                                SKShaderTileMode.Clamp);

            // Scale the canvas to flip upside-down around the vertical center
            canvas.Scale(1, -1, 0, yText);

            // Draw reflected text
            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

这些值 `xText` 和 `yText` 值与用于在 `DrawText` 处理程序底部的调用中显示反射文本的值相同 `PaintSurface` 。 但在该代码之前，你会看到对的方法的调用 `Scale` `SKCanvas` 。 此 `Scale` 方法水平缩放 (不执行任何操作) 但会将其垂直 &ndash; 翻转1，这会有效地翻转所有内容。 旋转中心设置为 (0) 的点 `yText` ，其中 `yText` 是画布的垂直中心，最初 `info.Height` 按2进行计算。

请记住，在画布转换之前，Skia 使用梯度来着色图形对象。 绘制 unreflected 文本后， `textBounds` 矩形将会移位，使其对应于显示的文本：

```csharp
textBounds.Offset(xText, yText);
```

该 `CreateLinearGradient` 调用定义了从该矩形顶部到底部的渐变。 渐变是从完全透明的蓝色 (`paint.Color.WithAlpha(0)`) 到50% 透明蓝色 (`paint.Color.WithAlpha(0x80)`) 。 画布转换反转文本，因此，50% 透明蓝色从基线开始，并在文本顶部变为透明。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)