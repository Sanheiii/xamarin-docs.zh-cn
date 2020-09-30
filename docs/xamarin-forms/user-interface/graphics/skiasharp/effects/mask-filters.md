---
title: SkiaSharp 掩码筛选器
description: 了解如何使用掩码筛选器创建模糊和其他 alpha 效果。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 940422A1-8BC0-4039-8AD7-26C61320F858
author: davidbritch
ms.author: dabritch
ms.date: 08/27/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 827b5618dce019e2dedb773f270fe1090da5d616
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562504"
---
# <a name="skiasharp-mask-filters"></a>SkiaSharp 掩码筛选器

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

掩码筛选器是操作图形对象的几何和 alpha 通道的效果。 若要使用掩码筛选器，请将的 [`MaskFilter`](xref:SkiaSharp.SKPaint.MaskFilter) 属性设置 `SKPaint` 为 [`SKMaskFilter`](xref:SkiaSharp.SKMaskFilter) 通过调用静态方法之一创建的类型的对象 `SKMaskFilter` 。

熟悉掩码筛选器的最佳方式是通过试验这些静态方法。 最有用的掩码筛选器可创建模糊：

![模糊示例](mask-filters-images/MaskFilterExample.png "模糊示例")

这是本文中所述的唯一掩码筛选器功能。 下一篇有关 [**SkiaSharp 图像筛选器**](image-filters.md) 的文章还介绍了您可能更喜欢这一点的模糊效果。 

静态 [`SKMaskFilter.CreateBlur`](xref:SkiaSharp.SKMaskFilter.CreateBlur(SkiaSharp.SKBlurStyle,System.Single)) 方法具有以下语法：

```csharp
public static SKMaskFilter CreateBlur (SKBlurStyle blurStyle, float sigma);
```

重载允许为用于创建模糊的算法指定标志，并使用矩形避免在将被其他图形对象覆盖的区域中模糊。

[`SKBlurStyle`](xref:SkiaSharp.SKBlurStyle) 是具有以下成员的枚举：

- `Normal`
- `Solid`
- `Outer`
- `Inner`

下面的示例演示了这些样式的效果。 `sigma`参数指定模糊的范围。 在较旧版本的 Skia 中，使用半径值指示模糊的范围。 如果某个 radius 值适用于你的应用程序，则可以使用一个静态 [`SKMaskFilter.ConvertRadiusToSigma`](xref:SkiaSharp.SKMaskFilter.ConvertRadiusToSigma*) 方法，从一个转换到另一个。 方法将半径乘以0.57735 并添加0.5。

[**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)示例中的 "**屏蔽模糊试验**" 页允许您试验模糊样式和 sigma 值。 XAML 文件实例化一个 `Picker` 具有四个 `SKBlurStyle` 枚举成员的，一个 `Slider` 用于指定 sigma 值：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.MaskBlurExperimentPage"
             Title="Mask Blur Experiment">
    
    <StackLayout>
        <skiaforms:SKCanvasView x:Name="canvasView"
                                VerticalOptions="FillAndExpand"
                                PaintSurface="OnCanvasViewPaintSurface" />

        <Picker x:Name="blurStylePicker" 
                Title="Filter Blur Style" 
                Margin="10, 0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKBlurStyle}">
                    <x:Static Member="skia:SKBlurStyle.Normal" />
                    <x:Static Member="skia:SKBlurStyle.Solid" />
                    <x:Static Member="skia:SKBlurStyle.Outer" />
                    <x:Static Member="skia:SKBlurStyle.Inner" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Slider x:Name="sigmaSlider"
                Maximum="10"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference sigmaSlider},
                              Path=Value,
                              StringFormat='Sigma = {0:F1}'}"
               HorizontalTextAlignment="Center" />
    </StackLayout>
</ContentPage>
```

代码隐藏文件使用这些值来创建 `SKMaskFilter` 对象并将其设置为 `MaskFilter` 对象的属性 `SKPaint` 。 此 `SKPaint` 对象用于绘制文本字符串和位图：

```csharp
public partial class MaskBlurExperimentPage : ContentPage
{
    const string TEXT = "Blur My Text";

    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                            typeof(MaskBlurExperimentPage), 
                            "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

    public MaskBlurExperimentPage ()
    {
        InitializeComponent ();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
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

        // Get values from XAML controls
        SKBlurStyle blurStyle =
            (SKBlurStyle)(blurStylePicker.SelectedIndex == -1 ?
                                        0 : blurStylePicker.SelectedItem);

        float sigma = (float)sigmaSlider.Value;

        using (SKPaint paint = new SKPaint())
        {
            // Set SKPaint properties
            paint.TextSize = (info.Width - 100) / (TEXT.Length / 2);
            paint.MaskFilter = SKMaskFilter.CreateBlur(blurStyle, sigma);

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

以下是 iOS、Android 和通用 Windows 平台上运行的程序 (UWP) ， `Normal` 并且具有模糊样式和增加 `sigma` 级别：

[![屏蔽模糊试验-正常](mask-filters-images/MaskBlurExperiment-Normal.png "屏蔽模糊试验-正常")](mask-filters-images/MaskBlurExperiment-Normal-Large.png#lightbox)

你会注意到，只有位图的边缘受模糊的影响。 `SKMaskFilter`如果要对整个位图图像进行模糊，则该类不是要使用的正确效果。 为此，你将需要使用 [`SKImageFilter`](xref:SkiaSharp.SKImageFilter) 下一篇有关 [**SkiaSharp 映像筛选器**](image-filters.md)的文章中所述的类。

该文本的变得越来越多，增加了 `sigma` 参数的值。 在试验此程序时，你会注意到，对于特定 `sigma` 值，模糊在 Windows 10 桌面上更极端。 出现这种差异的原因在于，桌面监视器上的像素密度比移动设备上的更低，因此，文本高度以像素为单位。 `sigma`该值与模糊范围成正比（以像素为单位），因此对于给定 `sigma` 值，该效果更极端地显示在较低的分辨率上。 在生产应用程序中，您可能需要计算与 `sigma` 图形大小成正比的值。 

在以最适合您的应用程序的模糊级别进行结算之前，尝试多个值。 例如，在 " **屏蔽模糊实验** " 页上，尝试设置， `sigma` 如下所示：

```csharp
sigma = paint.TextSize / 18;
paint.MaskFilter = SKMaskFilter.CreateBlur(blurStyle, sigma);
```

现在 `Slider` ，不会有任何影响，但这些平台之间的模糊程度是一致的：

[![屏蔽模糊试验-一致](mask-filters-images/MaskBlurExperiment-Consistent.png "屏蔽模糊试验-一致")](mask-filters-images/MaskBlurExperiment-Consistent-Large.png#lightbox)

到目前为止，所有屏幕截图都使用枚举成员创建了模糊 `SKBlurStyle.Normal` 。 以下屏幕截图显示了 `Solid` 、 `Outer` 和模糊样式的效果 `Inner` ：

[![屏蔽模糊试验](mask-filters-images/MaskBlurExperiment.png "屏蔽模糊试验")](mask-filters-images/MaskBlurExperiment-Large.png#lightbox)

IOS 屏幕快照显示 `Solid` 样式：文本字符仍以纯黑色笔划形式出现，并且模糊将添加到这些文本字符之外。 

中间的 Android 屏幕截图显示 `Outer` 样式：字符笔划本身会被消除， (位图) 并且模糊会将文本字符出现在空白区域的位置括起来。 

右侧的 UWP 屏幕截图显示 `Inner` 样式。 模糊限制为通常由文本字符占用的区域。

[**SkiaSharp 线性渐变**](shaders/linear-gradient.md#transparency-and-gradients)文章介绍了使用线性渐变和转换来模拟文本字符串反射的**反射梯度**计划：

[![反射渐变](shaders/linear-gradient-images/ReflectionGradient.png "反射渐变")](shaders/linear-gradient-images/ReflectionGradient-Large.png#lightbox)

**模糊反射**页面向该代码添加单个语句：

```csharp
public class BlurryReflectionPage : ContentPage
{
    const string TEXT = "Reflection";

    public BlurryReflectionPage()
    {
        Title = "Blurry Reflection";

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

            // Create a blur mask filter
            paint.MaskFilter = SKMaskFilter.CreateBlur(SKBlurStyle.Normal, paint.TextSize / 36);

            // Scale the canvas to flip upside-down around the vertical center
            canvas.Scale(1, -1, 0, yText);

            // Draw reflected text
            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

新语句为基于文本大小的反射文本添加模糊筛选器：

```csharp
paint.MaskFilter = SKMaskFilter.CreateBlur(SKBlurStyle.Normal, paint.TextSize / 36);
```

此模糊筛选器使反射看起来更加真实：

[![模糊反射](mask-filters-images/BlurryReflection.png "模糊反射")](mask-filters-images/BlurryReflection-Large.png#lightbox)

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)