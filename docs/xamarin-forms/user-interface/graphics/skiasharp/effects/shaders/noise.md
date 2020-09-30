---
title: SkiaSharp 干扰和撰写
description: 生成 Perlin 杂色着色器并与其他着色器组合。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 90C2D00A-2876-43EA-A836-538C3318CF93
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 00b6251f530a4927d069ae92ec919645a06baf15
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91555393"
---
# <a name="skiasharp-noise-and-composing"></a>SkiaSharp 干扰和撰写

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

简单的矢量图形往往看起来非自然。 直线、平滑曲线和纯色与现实世界对象的缺陷不相似。 使用用于1982电影 _Tron_的计算机生成的图形时，计算机科学家 Ken Perlin 开始开发算法，这些算法使用随机过程为这些图像提供更逼真的纹理。 在1997中，Ken Perlin 赢得了一项院校奖励。 他的工作被称为 Perlin 干扰，在 SkiaSharp 中受支持。 下面是一个示例：

![Perlin 噪音示例](noise-images/NoiseSample.png "Perlin 噪音示例")

正如您所看到的，每个像素不是随机颜色值。 从像素到像素的连续性导致随机形状。

Skia 中对 Perlin 干扰的支持基于 CSS 和 SVG 的 W3C 规范。 第8.20 节 [**筛选器效果模块级别 1**](https://www.w3.org/TR/filter-effects-1/#feTurbulenceElement) 在 C 代码中包含基础 Perlin 干扰算法。

## <a name="exploring-perlin-noise"></a>探索 Perlin 干扰

[`SKShader`](xref:SkiaSharp.SKShader)类定义了两个不同的静态方法用于生成 Perlin 干扰： [`CreatePerlinNoiseFractalNoise`](xref:SkiaSharp.SKShader.CreatePerlinNoiseFractalNoise*) 和 [`CreatePerlinNoiseTurbulence`](xref:SkiaSharp.SKShader.CreatePerlinNoiseTurbulence*) 。 参数相同：

```csharp
public static SkiaSharp CreatePerlinNoiseFractalNoise (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed);

public static SkiaSharp.SKShader CreatePerlinNoiseTurbulence (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed);
```

这两种方法也存在于包含附加参数的重载版本中 `SKPointI` 。 [**平铺 Perlin 噪音**](#tiling-perlin-noise)部分讨论了这些重载。

这两个 `baseFrequency` 参数在 SkiaSharp 文档中定义为正值，范围为0到1，但也可设置为更高的值。 值越大，在水平和垂直方向上，随机图像中的变化越大。

`numOctaves`该值是1或更高的整数。 它与算法中的迭代系数相关。 每个额外的 octave 都将影响到前面 octave 的一半，因此效果会随较高的 octave 值降低。

`seed`参数是随机数生成器的起点。 虽然指定为浮点值，但该分数在使用之前将被截断，0与1相同。

利用[ **SkiaSharpFormsDemos**) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)示例中的**Perlin 噪声**页，您可以试验和参数的各种 `baseFrequency` 值 `numOctaves` 。 下面是 XAML 文件：

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.PerlinNoisePage"
             Title="Perlin Noise">

    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="baseFrequencyXSlider"
                Maximum="4"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="baseFrequencyXText"
               HorizontalTextAlignment="Center" />

        <Slider x:Name="baseFrequencyYSlider"
                Maximum="4"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="baseFrequencyYText"
               HorizontalTextAlignment="Center" />

        <StackLayout Orientation="Horizontal"
                     HorizontalOptions="Center"
                     Margin="10">

            <Label Text="{Binding Source={x:Reference octavesStepper},
                                  Path=Value,
                                  StringFormat='Number of Octaves: {0:F0}'}"
                   VerticalOptions="Center" />

            <Stepper x:Name="octavesStepper"
                     Minimum="1"
                     ValueChanged="OnStepperValueChanged" />
        </StackLayout>
    </StackLayout>
</ContentPage>
```

它对两个参数使用两个 `Slider` 视图 `baseFrequency` 。 若要展开较低值的范围，滑块为对数。 代码隐藏文件计算 `SKShader` 值的值中的方法的参数 `Slider` 。 `Label`视图显示计算后的值：

```csharp
float baseFreqX = (float)Math.Pow(10, baseFrequencyXSlider.Value - 4);
baseFrequencyXText.Text = String.Format("Base Frequency X = {0:F4}", baseFreqX);

float baseFreqY = (float)Math.Pow(10, baseFrequencyYSlider.Value - 4);
baseFrequencyYText.Text = String.Format("Base Frequency Y = {0:F4}", baseFreqY);
```

`Slider`值1对应于0.001， `Slider` 值 os 2 对应于0.01，值3对应于 `Slider` 0.1， `Slider` 值4对应于1。

下面是包含该代码的代码隐藏文件：

```csharp
public partial class PerlinNoisePage : ContentPage
{
    public PerlinNoisePage()
    {
        InitializeComponent();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnStepperValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Get values from sliders and stepper
        float baseFreqX = (float)Math.Pow(10, baseFrequencyXSlider.Value - 4);
        baseFrequencyXText.Text = String.Format("Base Frequency X = {0:F4}", baseFreqX);

        float baseFreqY = (float)Math.Pow(10, baseFrequencyYSlider.Value - 4);
        baseFrequencyYText.Text = String.Format("Base Frequency Y = {0:F4}", baseFreqY);

        int numOctaves = (int)octavesStepper.Value;

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader =
                SKShader.CreatePerlinNoiseFractalNoise(baseFreqX,
                                                       baseFreqY,
                                                       numOctaves,
                                                       0);

            SKRect rect = new SKRect(0, 0, info.Width, info.Height / 2);
            canvas.DrawRect(rect, paint);

            paint.Shader =
                SKShader.CreatePerlinNoiseTurbulence(baseFreqX,
                                                     baseFreqY,
                                                     numOctaves,
                                                     0);

            rect = new SKRect(0, info.Height / 2, info.Width, info.Height);
            canvas.DrawRect(rect, paint);
        }
    }
}
```

下面是在 iOS、Android 和通用 Windows 平台 (UWP) 设备上运行的程序。 分形噪音显示在画布的上半部分。 Turbulence 噪声在下半部分：

[![Perlin 干扰](noise-images/PerlinNoise.png "Perlin 干扰")](noise-images/PerlinNoise-Large.png#lightbox)

相同的参数始终生成从左上角开始的相同模式。 当调整 UWP 窗口的宽度和高度时，这种一致性非常明显。 当 Windows 10 重绘屏幕时，画布上半部分的模式将保持不变。

干扰模式包含各种透明度。 如果在调用中设置了颜色，透明度就会变得很明显 `canvas.Clear()` 。 该颜色在模式中变得很明显。 在 [**组合多个着色**](#combining-multiple-shaders)器部分中，您还会看到此效果。

这些 Perlin 干扰模式本身很少使用。 通常，它们会被用于混合模式和在以后的文章中讨论的颜色筛选器。

## <a name="tiling-perlin-noise"></a>平铺 Perlin 干扰

`SKShader`用于创建 Perlin 噪音的两种静态方法也存在于重载版本中。 [`CreatePerlinNoiseFractalNoise`](xref:SkiaSharp.SKShader.CreatePerlinNoiseFractalNoise(System.Single,System.Single,System.Int32,System.Single,SkiaSharp.SKPointI))和 [`CreatePerlinNoiseTurbulence`](xref:SkiaSharp.SKShader.CreatePerlinNoiseFractalNoise(System.Single,System.Single,System.Int32,System.Single,SkiaSharp.SKPointI)) 重载具有一个附加 `SKPointI` 参数：

```csharp
public static SKShader CreatePerlinNoiseFractalNoise (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed, SKPointI tileSize);

public static SKShader CreatePerlinNoiseTurbulence (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed, SKPointI tileSize);
```

[`SKPointI`](xref:SkiaSharp.SKPointI)结构是熟悉的结构的整数版本 [`SKPoint`](xref:SkiaSharp.SKPoint) 。 `SKPointI` 定义 `X` `Y` 类型的和属性， `int` 而不是 `float` 。

这些方法创建指定大小的重复模式。 在每个磁贴中，右边缘与左边缘相同，上边缘与下边缘相同。 此特征在 **平铺的 Perlin 噪声** 页中进行了演示。 该 XAML 文件与前面的示例类似，但它只有一个 `Stepper` 用于更改参数的视图 `seed` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.TiledPerlinNoisePage"
             Title="Tiled Perlin Noise">

    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <StackLayout Orientation="Horizontal"
                     HorizontalOptions="Center"
                     Margin="10">

            <Label Text="{Binding Source={x:Reference seedStepper},
                                  Path=Value,
                                  StringFormat='Seed: {0:F0}'}"
                   VerticalOptions="Center" />

            <Stepper x:Name="seedStepper"
                     Minimum="1"
                     ValueChanged="OnStepperValueChanged" />

        </StackLayout>
    </StackLayout>
</ContentPage>
```

代码隐藏文件为磁贴大小定义一个常数。 `PaintSurface`处理程序将创建该大小的位图和 `SKCanvas` 用于绘制到该位图的。 `SKShader.CreatePerlinNoiseTurbulence`方法创建具有该图块大小的着色器。 此着色器是在位图上绘制的：

```csharp
public partial class TiledPerlinNoisePage : ContentPage
{
    const int TILE_SIZE = 200;

    public TiledPerlinNoisePage()
    {
        InitializeComponent();
    }

    void OnStepperValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Get seed value from stepper
        float seed = (float)seedStepper.Value;

        SKRect tileRect = new SKRect(0, 0, TILE_SIZE, TILE_SIZE);

        using (SKBitmap bitmap = new SKBitmap(TILE_SIZE, TILE_SIZE))
        {
            using (SKCanvas bitmapCanvas = new SKCanvas(bitmap))
            {
                bitmapCanvas.Clear();

                // Draw tiled turbulence noise on bitmap
                using (SKPaint paint = new SKPaint())
                {
                    paint.Shader = SKShader.CreatePerlinNoiseTurbulence(
                                        0.02f, 0.02f, 1, seed,
                                        new SKPointI(TILE_SIZE, TILE_SIZE));

                    bitmapCanvas.DrawRect(tileRect, paint);
                }
            }

            // Draw tiled bitmap shader on canvas
            using (SKPaint paint = new SKPaint())
            {
                paint.Shader = SKShader.CreateBitmap(bitmap,
                                                     SKShaderTileMode.Repeat,
                                                     SKShaderTileMode.Repeat);
                canvas.DrawRect(info.Rect, paint);
            }

            // Draw rectangle showing tile
            using (SKPaint paint = new SKPaint())
            {
                paint.Style = SKPaintStyle.Stroke;
                paint.Color = SKColors.Black;
                paint.StrokeWidth = 2;

                canvas.DrawRect(tileRect, paint);
            }
        }
    }
}
```

创建位图后，将使用另一个 `SKPaint` 对象通过调用来创建平铺位图模式 `SKShader.CreateBitmap` 。 请注意以下两个参数 `SKShaderTileMode.Repeat` ：

```csharp
paint.Shader = SKShader.CreateBitmap(bitmap,
                                     SKShaderTileMode.Repeat,
                                     SKShaderTileMode.Repeat);
```

此着色器用于涵盖画布。 最后，另一个 `SKPaint` 对象用于对显示原始位图大小的矩形进行描边。

仅 `seed` 参数可从用户界面中选择。 如果在 `seed` 每个平台上使用相同的模式，则它们将显示相同的模式。 不同的 `seed` 值会导致不同的模式：

[![平铺 Perlin 干扰](noise-images/TiledPerlinNoise.png "平铺 Perlin 干扰")](noise-images/TiledPerlinNoise-Large.png#lightbox)

左上角的200像素正方形模式无缝地流入其他磁贴。

## <a name="combining-multiple-shaders"></a>合并多个着色器

`SKShader`类包含 [`CreateColor`](xref:SkiaSharp.SKShader.CreateColor*) 用指定纯色创建着色器的方法。 此着色器本身并不十分有用，因为您只需将该颜色设置为 `Color` 对象的属性 `SKPaint` ，并将 `Shader` 属性设置为 null。

此 `CreateColor` 方法在定义的另一种方法中非常有用 `SKShader` 。 此方法是 [`CreateCompose`](xref:SkiaSharp.SKShader.CreateCompose(SkiaSharp.SKShader,SkiaSharp.SKShader)) ，它将两个着色器组合在一起。 语法如下：

```csharp
public static SKShader CreateCompose (SKShader dstShader, SKShader srcShader);
```

`srcShader` (源着色器) 有效地绘制在 `dstShader` (目标着色器) 的顶层。 如果源着色器是纯色或渐变而没有透明度，则目标着色器将完全遮蔽。

Perlin 杂色着色器包含透明度。 如果该着色器为源，则目标着色器会透过透明区域显示。

**组合 Perlin 噪声**页的 XAML 文件与第一个**Perlin 噪音**页几乎完全相同。 代码隐藏文件也是类似的。 但原始 **Perlin 噪声** 页将的 `Shader` 属性设置 `SKPaint` 为从静态和方法返回的着色 `CreatePerlinNoiseFractalNoise` 器 `CreatePerlinNoiseTurbulence` 。 此组合着色器的 **Perlin 噪音** 页面调用 `CreateCompose` 。 目标是使用创建的纯色蓝色着色器 `CreateColor` 。 源是 Perlin 的杂色着色器：

```csharp
public partial class ComposedPerlinNoisePage : ContentPage
{
    public ComposedPerlinNoisePage()
    {
        InitializeComponent();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnStepperValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Get values from sliders and stepper
        float baseFreqX = (float)Math.Pow(10, baseFrequencyXSlider.Value - 4);
        baseFrequencyXText.Text = String.Format("Base Frequency X = {0:F4}", baseFreqX);

        float baseFreqY = (float)Math.Pow(10, baseFrequencyYSlider.Value - 4);
        baseFrequencyYText.Text = String.Format("Base Frequency Y = {0:F4}", baseFreqY);

        int numOctaves = (int)octavesStepper.Value;

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateCompose(
                SKShader.CreateColor(SKColors.Blue),
                SKShader.CreatePerlinNoiseFractalNoise(baseFreqX,
                                                       baseFreqY,
                                                       numOctaves,
                                                       0));

            SKRect rect = new SKRect(0, 0, info.Width, info.Height / 2);
            canvas.DrawRect(rect, paint);

            paint.Shader = SKShader.CreateCompose(
                SKShader.CreateColor(SKColors.Blue),
                SKShader.CreatePerlinNoiseTurbulence(baseFreqX,
                                                     baseFreqY,
                                                     numOctaves,
                                                     0));

            rect = new SKRect(0, info.Height / 2, info.Width, info.Height);
            canvas.DrawRect(rect, paint);
        }
    }
}
```

分形噪音着色器位于顶部，turbulence 着色器位于底部：

[![组合 Perlin 干扰](noise-images/ComposedPerlinNoise.png "组合 Perlin 干扰")](noise-images/ComposedPerlinNoise-Large.png#lightbox)

请注意，这些着色器比 **Perlin 杂色** 页面显示的 bluer 有多少。 差别说明了噪音着色器中的透明度。

还存在方法的重载 [`CreateCompose`](xref:SkiaSharp.SKShader.CreateCompose(SkiaSharp.SKShader,SkiaSharp.SKShader,SkiaSharp.SKBlendMode)) ：

```csharp
public static SKShader CreateCompose (SKShader dstShader, SKShader srcShader, SKBlendMode blendMode);
```

最后一个参数是枚举的一个成员 `SKBlendMode` ，它是一个包含29个成员的枚举，将在下一系列有关 [**SkiaSharp 组合和 blend 模式**](../blend-modes/index.md)的文章中讨论。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)