---
title: SkiaSharp 透明度
description: 使用透明在单个场景中合并多个对象。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: B62F9487-C30E-4C63-BAB1-4C091FF50378
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: af1747a19af6c715d1594122393e500d1f73c79b
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562517"
---
# <a name="skiasharp-transparency"></a>SkiaSharp 透明度

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

如您所见， [`SKPaint`](xref:SkiaSharp.SKPaint) 该类包含一个 [`Color`](xref:SkiaSharp.SKPaint.Color) 类型为的属性 [`SKColor`](xref:SkiaSharp.SKColor) 。 `SKColor` 包含 alpha 通道，因此，使用值的任何颜色都 `SKColor` 可能是部分透明的。 

在 SkiaSharp 文章的 [**基本动画**](animation.md) 中演示了一些透明度。 本文稍微深入地探讨了如何将多个对象组合到一个场景中，这种方法有时也称为 " _混合_"。 [**SkiaSharp 着色**](../effects/shaders/index.md)器部分的文章中介绍了更高级的混合技术。

首次使用四参数构造函数创建颜色时，可以设置透明度级别 [`SKColor`](xref:SkiaSharp.SKColor.%23ctor(System.Byte,System.Byte,System.Byte,System.Byte)) ：

```csharp
SKColor (byte red, byte green, byte blue, byte alpha);
```

Alpha 值0是完全透明的，alpha 值为完全不透明。 这两个极端之间的值创建部分透明的颜色。

此外，还 `SKColor` 定义了一种方便的 [`WithAlpha`](xref:SkiaSharp.SKColor.WithAlpha*) 方法，该方法可根据现有颜色而不是指定的 alpha 级别创建新颜色：

```csharp
SKColor halfTransparentBlue = SKColors.Blue.WithAlpha(0x80);
```

[**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)示例的**代码更多代码**页中演示了部分透明文本的使用。 此页通过在值中包含透明度来将两个文本字符串淡入和淡出 `SKColor` ：

```csharp
public class CodeMoreCodePage : ContentPage
{
    SKCanvasView canvasView;
    bool isAnimating;
    Stopwatch stopwatch = new Stopwatch();
    double transparency;

    public CodeMoreCodePage ()
    {
        Title = "Code More Code";

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
        const int duration = 5;     // seconds
        double progress = stopwatch.Elapsed.TotalSeconds % duration / duration;
        transparency = 0.5 * (1 + Math.Sin(progress * 2 * Math.PI));
        canvasView.InvalidateSurface();

        return isAnimating;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        const string TEXT1 = "CODE";
        const string TEXT2 = "MORE";

        using (SKPaint paint = new SKPaint())
        {
            // Set text width to fit in width of canvas
            paint.TextSize = 100;
            float textWidth = paint.MeasureText(TEXT1);
            paint.TextSize *= 0.9f * info.Width / textWidth;

            // Center first text string
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT1, ref textBounds);

            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2 - textBounds.MidY;

            paint.Color = SKColors.Blue.WithAlpha((byte)(0xFF * (1 - transparency)));
            canvas.DrawText(TEXT1, xText, yText, paint);

            // Center second text string
            textBounds = new SKRect();
            paint.MeasureText(TEXT2, ref textBounds);

            xText = info.Width / 2 - textBounds.MidX;
            yText = info.Height / 2 - textBounds.MidY;

            paint.Color = SKColors.Blue.WithAlpha((byte)(0xFF * transparency));
            canvas.DrawText(TEXT2, xText, yText, paint);
        }
    }
}
```

此 `transparency` 字段的动画效果为0到1之间的变化，并将在正弦节奏中再次返回。 显示的第一个文本字符串带有通过从1中减去值计算得出的 alpha 值 `transparency` ：

```csharp
paint.Color = SKColors.Blue.WithAlpha((byte)(0xFF * (1 - transparency)));
```

此 [`WithAlpha`](xref:SkiaSharp.SKColor.WithAlpha*) 方法在现有颜色上设置 alpha 分量，此处为 `SKColors.Blue` 。 第二个文本字符串使用从值本身计算得出的 alpha 值 `transparency` ：

```csharp
paint.Color = SKColors.Blue.WithAlpha((byte)(0xFF * transparency));
```

动画会在两个单词之间交替，敦促用户 "代码更多" (或可能请求 "更多代码" ) ：

[![代码更多代码](transparency-images/CodeMoreCode.png "代码更多代码")](transparency-images/CodeMoreCode-Large.png#lightbox)

在上一文章 SkiaSharp 中的 [**位图基础**](bitmaps.md)上，你已了解如何使用的方法之一来显示位图 [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap*) `SKCanvas` 。 所有 `DrawBitmap` 方法都包含一个 `SKPaint` 对象作为最后一个参数。 默认情况下，此参数设置为， `null` 您可以将其忽略。 

或者，您可以设置 `Color` 此对象的属性， `SKPaint` 以显示具有一定程度的透明度的位图。 如果在的属性中设置透明度 `Color` `SKPaint` ，则可以使位图淡入和淡出，或将一个位图溶解到另一个位图。 

位图 **溶解在位图溶解** 页中进行了演示。 XAML 文件实例化 `SKCanvasView` 和 `Slider` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.BitmapDissolvePage"
             Title="Bitmap Dissolve">
    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="progressSlider"
                Margin="10"
                ValueChanged="OnSliderValueChanged" />
    </StackLayout>
</ContentPage>
```

代码隐藏文件加载两个位图资源。 这些位图的大小不相同，但纵横比相同：

```csharp
public partial class BitmapDissolvePage : ContentPage
{
    SKBitmap bitmap1;
    SKBitmap bitmap2;

    public BitmapDissolvePage()
    {
        InitializeComponent();

        // Load two bitmaps
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(
                                "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg"))
        {
            bitmap1 = SKBitmap.Decode(stream);
        }
        using (Stream stream = assembly.GetManifestResourceStream(
                                "SkiaSharpFormsDemos.Media.FacePalm.jpg"))
        {
            bitmap2 = SKBitmap.Decode(stream);
        }
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Find rectangle to fit bitmap
        float scale = Math.Min((float)info.Width / bitmap1.Width,
                                (float)info.Height / bitmap1.Height);
        SKRect rect = SKRect.Create(scale * bitmap1.Width,
                                    scale * bitmap1.Height);
        float x = (info.Width - rect.Width) / 2;
        float y = (info.Height - rect.Height) / 2;
        rect.Offset(x, y);

        // Get progress value from Slider
        float progress = (float)progressSlider.Value;

        // Display two bitmaps with transparency
        using (SKPaint paint = new SKPaint())
        {
            paint.Color = paint.Color.WithAlpha((byte)(0xFF * (1 - progress)));
            canvas.DrawBitmap(bitmap1, rect, paint);

            paint.Color = paint.Color.WithAlpha((byte)(0xFF * progress));
            canvas.DrawBitmap(bitmap2, rect, paint);
        }
    }
}
```

`Color`对象的属性 `SKPaint` 设置为两个位图的两个互补 alpha 级别。 使用 `SKPaint` 位图时，值的其余部分并不重要 `Color` 。 重要的是 alpha 通道。 此处的代码只是对 `WithAlpha` 属性的默认值调用方法 `Color` 。

在 `Slider` 一个位图之间移动溶解：

[![位图溶解](transparency-images/BitmapDissolve.png "位图溶解")](transparency-images/BitmapDissolve-Large.png#lightbox)

在过去的几篇文章中，你已了解如何使用 SkiaSharp 绘制文本、圆、椭圆、圆角矩形和位图。 下一步是 [SkiaSharp 线和路径](../paths/index.md) ，您将在其中学习如何在图形路径中绘制连接线条。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)