---
title: 与集成 Xamarin.Forms
description: 本文介绍如何创建响应触控和元素的 SkiaSharp 图形 Xamarin.Forms ，并通过示例代码对此进行演示。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 288224F1-7AEE-4148-A88D-A70C03F83D7A
author: davidbritch
ms.author: dabritch
ms.date: 02/09/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: cca93508e022420e0d5d11de079420b367188052
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562244"
---
# <a name="integrating-with-no-locxamarinforms"></a>与集成 Xamarin.Forms

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_创建响应触摸和元素的 SkiaSharp 图形 Xamarin.Forms_

SkiaSharp 图形可通过多种方式与的其余部分集成 Xamarin.Forms 。 可以将 SkiaSharp 画布和元素合并到 Xamarin.Forms 同一个页面上，甚至将 Xamarin.Forms 元素放置在 SkiaSharp 画布的顶部：

![使用滑块选择颜色](integration-images/integrationexample.png)

在中创建交互式 SkiaSharp 图形的另一种方法 Xamarin.Forms 是通过触摸。
[**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)计划中的第二页有权**点击 "切换填充**"。 它绘制一个简单的圆点 &mdash; ，无需填充，并 &mdash; 通过点击切换填充。 [`TapToggleFillPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/TapToggleFillPage.xaml.cs)类显示如何更改 SkiaSharp 图形以响应用户输入。

对于此页，在 `SKCanvasView` [TapToggleFill](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/TapToggleFillPage.xaml) 文件中实例化类，该文件还在 Xamarin.Forms [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) 视图上设置了：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.TapToggleFillPage"
             Title="Tap Toggle Fill">

    <skia:SKCanvasView PaintSurface="OnCanvasViewPaintSurface">
        <skia:SKCanvasView.GestureRecognizers>
            <TapGestureRecognizer Tapped="OnCanvasViewTapped" />
        </skia:SKCanvasView.GestureRecognizers>
    </skia:SKCanvasView>
</ContentPage>
```

请注意 `skia` XML 命名空间声明。

`Tapped`对象的处理程序 `TapGestureRecognizer` 只是切换布尔字段的值并调用的 [`InvalidateSurface`](xref:SkiaSharp.Views.Forms.SKCanvasView.InvalidateSurface) 方法 `SKCanvasView` ：

```csharp
bool showFill = true;
...
void OnCanvasViewTapped(object sender, EventArgs args)
{
    showFill ^= true;
    (sender as SKCanvasView).InvalidateSurface();
}
```

调用可 `InvalidateSurface` 有效生成对处理程序的调用 `PaintSurface` ，该处理程序使用 `showFill` 字段填充或不填充圆：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = Color.Red.ToSKColor(),
        StrokeWidth = 50
    };
    canvas.DrawCircle(info.Width / 2, info.Height / 2, 100, paint);

    if (showFill)
    {
        paint.Style = SKPaintStyle.Fill;
        paint.Color = SKColors.Blue;
        canvas.DrawCircle(info.Width / 2, info.Height / 2, 100, paint);
    }
}
```

该 `StrokeWidth` 属性已设置为50，强调设计差异。 您还可以通过先绘制内部，然后绘制轮廓来查看整个线条宽度。 默认情况下，在事件处理程序中的后面绘制的图形图会 `PaintSurface` 掩盖之前在处理程序中绘制的图形。

" **颜色浏览** " 页演示了如何将 SkiaSharp 图形与其他 Xamarin.Forms 元素集成，还演示了在 SkiaSharp 中定义颜色的两种替代方法之间的差异。 静态 [`SKColor.FromHsl`](xref:SkiaSharp.SKColor.FromHsl(System.Single,System.Single,System.Single,System.Byte)) 方法 `SKColor` 基于 "色调-饱和度-亮度" 模型创建值：

```csharp
public static SKColor FromHsl (Single h, Single s, Single l, Byte a)
```

静态 [`SKColor.FromHsv`](xref:SkiaSharp.SKColor.FromHsv(System.Single,System.Single,System.Single,System.Byte)) 方法 `SKColor` 基于类似的色调-饱和度值模型创建值：

```csharp
public static SKColor FromHsv (Single h, Single s, Single v, Byte a)
```

在这两种情况下， `h` 自变量的范围介于0到360之间。 `s`、 `l` 和参数的 `v` 范围为0到100。 `a` (alpha 或不透明度) 参数的范围为0到255。

[**ColorExplorePage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ColorExplorePage.xaml)文件将并行创建两个 `SKCanvasView` 对象 `StackLayout` `Slider` 以及 `Label` 允许用户选择 HSL 和 HSV 颜色值的视图：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Basics.ColorExplorePage"
             Title="Color Explore">
    <StackLayout>
        <!-- Hue slider -->
        <Slider x:Name="hueSlider"
                Maximum="360"
                Margin="20, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label HorizontalTextAlignment="Center"
               Text="{Binding Source={x:Reference hueSlider},
                              Path=Value,
                              StringFormat='Hue = {0:F0}'}" />

        <!-- Saturation slider -->
        <Slider x:Name="saturationSlider"
                Maximum="100"
                Margin="20, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label HorizontalTextAlignment="Center"
               Text="{Binding Source={x:Reference saturationSlider},
                              Path=Value,
                              StringFormat='Saturation = {0:F0}'}" />

        <!-- Lightness slider -->
        <Slider x:Name="lightnessSlider"
                Maximum="100"
                Margin="20, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label HorizontalTextAlignment="Center"
               Text="{Binding Source={x:Reference lightnessSlider},
                              Path=Value,
                              StringFormat='Lightness = {0:F0}'}" />

        <!-- HSL canvas view -->
        <Grid VerticalOptions="FillAndExpand">
            <skia:SKCanvasView x:Name="hslCanvasView"
                               PaintSurface="OnHslCanvasViewPaintSurface" />

            <Label x:Name="hslLabel"
                   HorizontalOptions="Center"
                   VerticalOptions="Center"
                   BackgroundColor="Black"
                   TextColor="White" />
        </Grid>

        <!-- Value slider -->
        <Slider x:Name="valueSlider"
                Maximum="100"
                Margin="20, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label HorizontalTextAlignment="Center"
               Text="{Binding Source={x:Reference valueSlider},
                              Path=Value,
                              StringFormat='Value = {0:F0}'}" />

        <!-- HSV canvas view -->
        <Grid VerticalOptions="FillAndExpand">
            <skia:SKCanvasView x:Name="hsvCanvasView"
                               PaintSurface="OnHsvCanvasViewPaintSurface" />

            <Label x:Name="hsvLabel"
                   HorizontalOptions="Center"
                   VerticalOptions="Center"
                   BackgroundColor="Black"
                   TextColor="White" />
        </Grid>
    </StackLayout>
</ContentPage>
```

这两个 `SKCanvasView` 元素位于单个单元格内， `Grid` 并且位于顶部， `Label` 用于显示生成的 RGB 颜色值。

[**ColorExplorePage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ColorExplorePage.xaml.cs)代码隐藏文件相对简单。 这 `ValueChanged` 三个元素的共享处理程序 `Slider` 只是使这两个 `SKCanvasView` 元素失效。 `PaintSurface`处理程序将使用元素指示的颜色清除画布 `Slider` ，并设置 `Label` 元素的上方 `SKCanvasView` ：

```csharp
public partial class ColorExplorePage : ContentPage
{
    public ColorExplorePage()
    {
        InitializeComponent();

        hueSlider.Value = 0;
        saturationSlider.Value = 100;
        lightnessSlider.Value = 50;
        valueSlider.Value = 100;
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        hslCanvasView.InvalidateSurface();
        hsvCanvasView.InvalidateSurface();
    }

    void OnHslCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKColor color = SKColor.FromHsl((float)hueSlider.Value,
                                        (float)saturationSlider.Value,
                                        (float)lightnessSlider.Value);
        args.Surface.Canvas.Clear(color);

        hslLabel.Text = String.Format(" RGB = {0:X2}-{1:X2}-{2:X2} ",
                                      color.Red, color.Green, color.Blue);
    }

    void OnHsvCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKColor color = SKColor.FromHsv((float)hueSlider.Value,
                                        (float)saturationSlider.Value,
                                        (float)valueSlider.Value);
        args.Surface.Canvas.Clear(color);

        hsvLabel.Text = String.Format(" RGB = {0:X2}-{1:X2}-{2:X2} ",
                                      color.Red, color.Green, color.Blue);
    }
}
```

在 HSL 和 HSV 颜色模型中，色相值的范围为0到360，表示颜色的主导色调。 下面是彩虹的传统颜色：红色、橙色、黄色、绿色、蓝色、靛蓝色、紫色，并返回到红色。

在 HSL 模型中，"亮度" 的值始终为黑色，100值始终为白色。 当饱和度值为0时，介于0到100之间的亮度值为灰色阴影。 增加饱和度会增加颜色。 纯颜色 (这是 RGB 值，其中一个组件等于255，另一个组件等于0，第三个范围从0到 255) 在饱和度为100且亮度为50时出现。

在 HSV 模型中，饱和度和值均为100时，纯颜色会产生结果。 当 Value 为0时，无论是否有任何其他设置，颜色都为黑色。 当饱和度为0并且值范围从0到100时，将发生灰色阴影。

但若要了解这两种模型，最好的方法是自行试验：

[!["颜色浏览" 页的三个屏幕截图](integration-images/colorexplore-large.png)](integration-images/colorexplore-small.png#lightbox ""颜色浏览" 页的三个屏幕截图")

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)