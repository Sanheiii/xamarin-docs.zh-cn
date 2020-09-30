---
title: SkiaSharp 图像筛选器
description: 了解如何使用图像筛选器创建模糊和投影。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 173E7B22-AEC8-4F12-B657-1C0CEE01AD63
author: davidbritch
ms.author: dabritch
ms.date: 08/27/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4f10d39ff9fb08897f12cf1991ddcd2d7793b695
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91564025"
---
# <a name="skiasharp-image-filters"></a>SkiaSharp 图像筛选器

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

图像筛选器是对构成图像的所有颜色位进行操作的效果。 这些筛选器比掩码筛选器更通用，只在 alpha 通道上操作，如 [**SkiaSharp mask 筛选器**](mask-filters.md)一文中所述。 若要使用图像筛选器，请将的 [`ImageFilter`](xref:SkiaSharp.SKPaint.ImageFilter) 属性设置 `SKPaint` 为 [`SKImageFilter`](xref:SkiaSharp.SKImageFilter) 通过调用类的静态方法之一创建的类型的对象。

熟悉掩码筛选器的最佳方式是通过试验这些静态方法。 您可以使用掩码筛选器来模糊整个位图：

![模糊示例](image-filters-images/ImageFilterExample.png "模糊示例")

本文还演示了如何使用图像筛选器创建投影，以及如何使用浮雕和 engraving 效果。

## <a name="blurring-vector-graphics-and-bitmaps"></a>模糊矢量图形和位图

静态方法创建的模糊效果与 [`SKImageFilter.CreateBlur`](xref:SkiaSharp.SKImageFilter.CreateBlur*) 类中的模糊方法相比具有明显的优势 [`SKMaskFilter`](xref:SkiaSharp.SKMaskFilter) ：图像筛选器可以对整个位图进行模糊。 方法具有以下语法：

```csharp
public static SkiaSharp.SKImageFilter CreateBlur (float sigmaX, float sigmaY,
                                                  SKImageFilter input = null,
                                                  SKImageFilter.CropRect cropRect = null);
```

此方法具有两个西格玛值 &mdash; ：第一个用于水平方向的模糊范围，第二个值用于垂直方向。 您可以通过将其他图像筛选器指定为可选的第三个参数来层叠图像筛选器。 还可以指定裁剪矩形。

[**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)中的 "**图像模糊试验**" 页包含两个 `Slider` 视图，可让您试验设置各种模糊级别：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.ImageBlurExperimentPage"
             Title="Image Blur Experiment">

    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="sigmaXSlider"
                Maximum="10"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference sigmaXSlider},
                              Path=Value,
                              StringFormat='Sigma X = {0:F1}'}"
               HorizontalTextAlignment="Center" />

        <Slider x:Name="sigmaYSlider"
                Maximum="10"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference sigmaYSlider},
                              Path=Value,
                              StringFormat='Sigma Y = {0:F1}'}"
               HorizontalTextAlignment="Center" />
    </StackLayout>
</ContentPage>
```

代码隐藏文件使用两个 `Slider` 值来调用 `SKImageFilter.CreateBlur` `SKPaint` 用于显示文本和位图的对象：

```csharp
public partial class ImageBlurExperimentPage : ContentPage
{
    const string TEXT = "Blur My Text";

    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                            typeof(MaskBlurExperimentPage),
                            "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

    public ImageBlurExperimentPage ()
    {
        InitializeComponent ();
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

        canvas.Clear(SKColors.Pink);

        // Get values from sliders
        float sigmaX = (float)sigmaXSlider.Value;
        float sigmaY = (float)sigmaYSlider.Value;

        using (SKPaint paint = new SKPaint())
        {
            // Set SKPaint properties
            paint.TextSize = (info.Width - 100) / (TEXT.Length / 2);
            paint.ImageFilter = SKImageFilter.CreateBlur(sigmaX, sigmaY);

            // Get text bounds and calculate display rectangle
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);
            SKRect textRect = new SKRect(0, 0, info.Width, textBounds.Height + 50);

            // Center the text in the display rectangle
            float xText = textRect.Width / 2 - textBounds.MidX;
            float yText = textRect.Height / 2 - textBounds.MidY;

            canvas.DrawText(TEXT, xText, yText, paint);

            // Calculate rectangle for bitmap
            SKRect bitmapRect = new SKRect(0, textRect.Bottom, info.Width, info.Height);
            bitmapRect.Inflate(-50, -50);

            canvas.DrawBitmap(bitmap, bitmapRect, BitmapStretch.Uniform, paint: paint);
        }
    }
}
```

这三个屏幕截图显示了和设置的各种设置 `sigmaX` `sigmaY` ：

[![图像模糊试验](image-filters-images/ImageBlurExperiment.png "图像模糊试验")](image-filters-images/ImageBlurExperiment-Large.png#lightbox)

为了使不同显示大小和分辨率中的模糊保持一致，请将 `sigmaX` 和设置为与应用了模糊的图像的 `sigmaY` 呈现像素大小成正比的值。

## <a name="drop-shadow"></a>投影

[`SKImageFilter.CreateDropShadow`](xref:SkiaSharp.SKImageFilter.CreateDropShadow*)静态方法 `SKImageFilter` 为投影创建一个对象：

```csharp
public static SKImageFilter CreateDropShadow (float dx, float dy,
                                              float sigmaX, float sigmaY,
                                              SKColor color,
                                              SKDropShadowImageFilterShadowMode shadowMode,
                                              SKImageFilter input = null,
                                              SKImageFilter.CropRect cropRect = null);
```

将此对象设置为 `ImageFilter` 对象的属性 `SKPaint` ，并且使用该对象绘制的任何内容将在其后面放置一个阴影。

`dx`和 `dy` 参数指示阴影的水平和垂直偏移量（以像素为单位）。 2D 图形中的约定是假设从左上方开始的光源，这表示这两个参数都应为正值，以将阴影放置在图形对象的下方和右侧。

`sigmaX`和 `sigmaY` 参数是投影的模糊因数。

`color`参数是投影的颜色。 此 `SKColor` 值可以包含透明度。 一种可能的颜色值是将 `SKColors.Black.WithAlpha(0x80)` 任何颜色背景变暗。

最后两个参数是可选的。

**Drop shadow 试验**程序允许您试验 `dx` 、、和的值 `dy` ， `sigmaX` `sigmaY` 以显示带有投影的文本字符串。 XAML 文件实例化四个 `Slider` 视图以设置这些值：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.DropShadowExperimentPage"
             Title="Drop Shadow Experiment">
    <ContentPage.Resources>
        <Style TargetType="Slider">
            <Setter Property="Margin" Value="10, 0" />
        </Style>

        <Style TargetType="Label">
            <Setter Property="HorizontalTextAlignment" Value="Center" />
        </Style>
    </ContentPage.Resources>

    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="dxSlider"
                Minimum="-20"
                Maximum="20"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference dxSlider},
                              Path=Value,
                              StringFormat='Horizontal offset = {0:F1}'}" />

        <Slider x:Name="dySlider"
                Minimum="-20"
                Maximum="20"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference dySlider},
                              Path=Value,
                              StringFormat='Vertical offset = {0:F1}'}" />

        <Slider x:Name="sigmaXSlider"
                Maximum="10"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference sigmaXSlider},
                              Path=Value,
                              StringFormat='Sigma X = {0:F1}'}" />

        <Slider x:Name="sigmaYSlider"
                Maximum="10"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference sigmaYSlider},
                              Path=Value,
                              StringFormat='Sigma Y = {0:F1}'}" />
    </StackLayout>
</ContentPage>
```

代码隐藏文件使用这些值在蓝色文本字符串上创建红色投影：

```csharp
public partial class DropShadowExperimentPage : ContentPage
{
    const string TEXT = "Drop Shadow";

    public DropShadowExperimentPage ()
    {
        InitializeComponent ();
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

        // Get values from sliders
        float dx = (float)dxSlider.Value;
        float dy = (float)dySlider.Value;
        float sigmaX = (float)sigmaXSlider.Value;
        float sigmaY = (float)sigmaYSlider.Value;

        using (SKPaint paint = new SKPaint())
        {
            // Set SKPaint properties
            paint.TextSize = info.Width / 7;
            paint.Color = SKColors.Blue;
            paint.ImageFilter = SKImageFilter.CreateDropShadow(
                                    dx,
                                    dy,
                                    sigmaX,
                                    sigmaY,
                                    SKColors.Red,
                                    SKDropShadowImageFilterShadowMode.DrawShadowAndForeground);

            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);

            // Center the text in the display rectangle
            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2 - textBounds.MidY;

            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

下面是正在运行的程序：

[![删除阴影试验](image-filters-images/DropShadowExperiment.png "删除阴影试验")](image-filters-images/DropShadowExperiment-Large.png#lightbox)

最右侧通用 Windows 平台屏幕截图中的负偏移量值会导致阴影显示在文本的上方和左侧。 这会推荐右下方的光源，这并不是计算机图形的约定。 但这种方法似乎并不是错误的，可能是因为阴影的效果也很模糊，并看起来比大多数投影 ornamental。

## <a name="lighting-effects"></a>照明效果

此 `SKImageFilter` 类定义了六个具有相似名称和参数的方法，这些方法在此处按递增复杂度的顺序列出：

- [`CreateDistantLitDiffuse`](xref:SkiaSharp.SKImageFilter.CreateDistantLitDiffuse*)
- [`CreateDistantLitSpecular`](xref:SkiaSharp.SKImageFilter.CreateDistantLitSpecular*)
- [`CreatePointLitDiffuse`](xref:SkiaSharp.SKImageFilter.CreatePointLitDiffuse*)
- [`CreatePointLitSpecular`](xref:SkiaSharp.SKImageFilter.CreatePointLitSpecular*)
- [`CreateSpotLitDiffuse`](xref:SkiaSharp.SKImageFilter.CreateSpotLitDiffuse*)
- [`CreateSpotLitSpecular`](xref:SkiaSharp.SKImageFilter.CreateSpotLitSpecular*)

这些方法创建的图像筛选器模拟三维表面上不同种类的光的效果。 生成的图像筛选器会将二维对象视为3D 空间中存在的对象，这可能会导致这些对象显示为升高或凹陷，或具有镜面突出显示。

`Distant`灯光方法假设光源来自远处。 用于照亮对象的情况下，光源被认为是三维空间中的一个一致方向，非常类似于地球上的一个小区域。 `Point`灯光方法模拟三维空间中放置光线的灯泡。 `Spot`光源具有位置和方向，与闪光灯非常类似。

三维空间中的位置和方向都是用结构的值指定的 [`SKPoint3`](xref:SkiaSharp.SKPoint3) ，它类似于 `SKPoint` 但具有三个名为 `X` 、和的属性 `Y` `Z` 。

这些方法的参数的数量和复杂性使得它们的试验非常困难。 为了使你开始，" **远处轻型实验** " 页可让你体验方法的参数 `CreateDistantLightDiffuse` ：

```csharp
public static SKImageFilter CreateDistantLitDiffuse (SKPoint3 direction,
                                                     SKColor lightColor,
                                                     float surfaceScale,
                                                     float kd,
                                                     SKImageFilter input = null,
                                                     SKImageFilter.CropRect cropRect = null);
```

页面不使用最后两个可选参数。

`Slider`XAML 文件中的三个视图允许选择 `Z` `SKPoint3` 值、参数和参数的坐标，该 `surfaceScale` `kd` 参数在 API 文档中定义为 "漫射照明常量"：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaLightExperiment.MainPage"
             Title="Distant Light Experiment">

    <StackLayout>

        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface"
                           VerticalOptions="FillAndExpand" />

        <Slider x:Name="zSlider"
                Minimum="-10"
                Maximum="10"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference zSlider},
                              Path=Value,
                              StringFormat='Z = {0:F0}'}"
               HorizontalTextAlignment="Center" />

        <Slider x:Name="surfaceScaleSlider"
                Minimum="-1"
                Maximum="1"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference surfaceScaleSlider},
                              Path=Value,
                              StringFormat='Surface Scale = {0:F1}'}"
               HorizontalTextAlignment="Center" />

        <Slider x:Name="lightConstantSlider"
                Minimum="-1"
                Maximum="1"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference lightConstantSlider},
                              Path=Value,
                              StringFormat='Light Constant = {0:F1}'}"
               HorizontalTextAlignment="Center" />
    </StackLayout>
</ContentPage>
```

代码隐藏文件获取这三个值，并使用它们创建一个图像筛选器以显示文本字符串：

```csharp
public partial class DistantLightExperimentPage : ContentPage
{
    const string TEXT = "Lighting";

    public DistantLightExperimentPage()
    {
        InitializeComponent();
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

        float z = (float)zSlider.Value;
        float surfaceScale = (float)surfaceScaleSlider.Value;
        float lightConstant = (float)lightConstantSlider.Value;

        using (SKPaint paint = new SKPaint())
        {
            paint.IsAntialias = true;

            // Size text to 90% of canvas width
            paint.TextSize = 100;
            float textWidth = paint.MeasureText(TEXT);
            paint.TextSize *= 0.9f * info.Width / textWidth;

            // Find coordinates to center text
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);

            float xText = info.Rect.MidX - textBounds.MidX;
            float yText = info.Rect.MidY - textBounds.MidY;

            // Create distant light image filter
            paint.ImageFilter = SKImageFilter.CreateDistantLitDiffuse(
                                    new SKPoint3(2, 3, z),
                                    SKColors.White,
                                    surfaceScale,
                                    lightConstant);

            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

的第一个参数 `SKImageFilter.CreateDistantLitDiffuse` 是光的方向。 正 X 和 Y 坐标指示光向右和向下指向。 正 Z 坐标指向屏幕。 XAML 文件允许您选择负 Z 值，但这只是为了您可以看到发生了什么变化：从概念上讲，负 Z 坐标使光源指向屏幕。 对于除小负值以外的其他任何值，灯光效果都将停止工作。

`surfaceScale`参数的范围为-1 到1。  (较高或较低的值没有进一步的影响。 ) 这些是 Z 轴中的相对值，用于指示在此例中为图形对象的位移 (在这种情况下，则为画布表面的文本字符串) 。 使用负值可引发画布表面上方的文本字符串和正值，以将其插入画布中。

`lightConstant`值应为正数。  (程序允许使用负值，这样您就可以看到它们会导致效果停止工作。 ) 较高的值会导致更高的强度。

当为负 (与 iOS 和 Android 屏幕截图相同时，可以平衡这些因素以获得浮雕效果 `surfaceScale`) 并且当为正时，可以使用阴文效果 `surfaceScale` ，如右侧的 UWP 屏幕截图所示：

[![远光源试验](image-filters-images/DistantLightExperiment.png "远光源试验")](image-filters-images/DistantLightExperiment-Large.png#lightbox)

Android 屏幕截图的 Z 值为0，这意味着该光源只指向向下和向右。 背景未亮起，并且文本字符串的图面未亮起。 光源只会影响文本边缘，使其变得非常微妙。

下面演示了 " [转换转换](../transforms/translate.md)" 一文中的一种替代方法：文本字符串显示两次，不同颜色的偏移量略有不同。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)