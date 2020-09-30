---
title: 分离混合模式
description: 使用 "可分离混合模式" 更改红色、绿色和蓝色颜色。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 66D1A537-A247-484E-B5B9-FBCB7838FBE9
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0fd934a305e34bb7406a0379a0882873e3400fe8
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91558357"
---
# <a name="the-separable-blend-modes"></a>分离混合模式

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

正如你在 [**SkiaSharp Porter-Duff blend 模式一**](porter-duff.md)文中看到的那样，Porter-Duff blend 模式通常执行剪辑操作。 分离混合模式不同。 分离模式改变图像的各个红色、绿色和蓝色成分。 分离混合模式可以混合颜色，以表明红色、绿色和蓝色的组合确实是白色的：

![主要颜色](separable-images/SeparableSample.png "主要颜色")

## <a name="lighten-and-darken-two-ways"></a>以两种方式变亮和加深 

常见的位图太暗或太浅。 可以使用分离混合模式使 imabe 变浅或加深。  事实上，枚举中的两个分离混合模式 [`SKBlendMode`](xref:SkiaSharp.SKBlendMode) 都命名为 `Lighten` 和 `Darken` 。 

这两种模式在 " **变亮并变暗** " 页中进行演示。 XAML 文件实例化两个 `SKCanvasView` 对象和两个 `Slider` 视图：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.LightenAndDarkenPage"
             Title="Lighten and Darken">
    <StackLayout>
        <skia:SKCanvasView x:Name="lightenCanvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="lightenSlider"
                Margin="10"
                ValueChanged="OnSliderValueChanged" />

        <skia:SKCanvasView x:Name="darkenCanvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="darkenSlider"
                Margin="10"
                ValueChanged="OnSliderValueChanged" />
    </StackLayout>
</ContentPage>
```

第一个 `SKCanvasView` 和 `Slider` 演示 `SKBlendMode.Lighten` ，第二个对演示 `SKBlendMode.Darken` 。 这两个 `Slider` 视图共享同一 `ValueChanged` 处理程序，这两个视图 `SKCanvasView` 共享同一 `PaintSurface` 处理程序。 这两个事件处理程序都会检查哪个对象要触发事件：

```csharp
public partial class LightenAndDarkenPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                typeof(SeparableBlendModesPage),
                "SkiaSharpFormsDemos.Media.Banana.jpg");

    public LightenAndDarkenPage ()
    {
        InitializeComponent ();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        if ((Slider)sender == lightenSlider)
        {
            lightenCanvasView.InvalidateSurface();
        }
        else
        {
            darkenCanvasView.InvalidateSurface();
        }
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Find largest size rectangle in canvas
        float scale = Math.Min((float)info.Width / bitmap.Width,
                               (float)info.Height / bitmap.Height);
        SKRect rect = SKRect.Create(scale * bitmap.Width, scale * bitmap.Height);
        float x = (info.Width - rect.Width) / 2;
        float y = (info.Height - rect.Height) / 2;
        rect.Offset(x, y);

        // Display bitmap
        canvas.DrawBitmap(bitmap, rect);

        // Display gray rectangle with blend mode
        using (SKPaint paint = new SKPaint())
        {
            if ((SKCanvasView)sender == lightenCanvasView)
            {
                byte value = (byte)(255 * lightenSlider.Value);
                paint.Color = new SKColor(value, value, value);
                paint.BlendMode = SKBlendMode.Lighten;
            }
            else
            {
                byte value = (byte)(255 * (1 - darkenSlider.Value));
                paint.Color = new SKColor(value, value, value);
                paint.BlendMode = SKBlendMode.Darken;
            }

            canvas.DrawRect(rect, paint);
        }
    }
}
```

`PaintSurface`处理程序计算适用于位图的矩形。 处理程序显示该位图，然后使用 `SKPaint` 对象 `BlendMode` 将其属性设置为或的对象在位图上显示一个矩形 `SKBlendMode.Lighten` `SKBlendMode.Darken` 。 `Color`属性是基于的灰色阴影 `Slider` 。 对于 `Lighten` 模式，颜色范围是从黑色到白色，但对于模式， `Darken` 它的范围是从白色到黑色。

从左到右的屏幕截图显示越来越大 `Slider` 的值，因为顶部图像越浅，底部图像越暗：

[![淡化和加深](separable-images/LightenAndDarken.png "淡化和加深")](separable-images/LightenAndDarken-Large.png#lightbox)

此程序演示使用分离混合模式的正常方式：目标是某种类型的图像，通常是位图。 源是使用 `SKPaint` 对象（其 `BlendMode` 属性设置为分离混合模式）显示的矩形。 此矩形可以是 () 或渐变的纯色。 透明度一般 _不_ 与分离混合模式一起使用。

当您试验此程序时，您会发现这两种混合模式不会将图像均匀地淡化和加深。 相反， `Slider` 似乎设置了某种类型的阈值。 例如，当你为模式增加 `Slider` `Lighten` 时，图像的较暗区域将首先获取光，而较亮的区域会保持不变。

对于 `Lighten` 模式，如果目标像素是 RGB 颜色值 (Dr，Dg，Db) ，源像素是 (Sr，Sg，Sb) ，则输出为 (或，Og，Ob) 计算如下：

 `Or = max(Dr, Sr)` `Og = max(Dg, Sg)`
 `Ob = max(Db, Sb)`

对于红色、绿色和蓝色，结果是目标和源的较大。 这就产生了首先导致目标的暗区的效果。

`Darken`模式类似，只不过结果是目标和源的较小者：

 `Or = min(Dr, Sr)` `Og = min(Dg, Sg)`
 `Ob = min(Db, Sb)`

红色、绿色和蓝色分量分别进行单独处理，这就是这些混合模式被称为 _分离_ 混合模式的原因。 出于此原因，缩写 **Dc** 和 **Sc** 可用于目标和源颜色，并且了解到分别将计算应用于每个红色、绿色和蓝色组件。

下表显示了所有可分离混合模式，并简要说明了它们的作用。 第二列显示不产生变化的源颜色：

| 混合模式   | 没有变化 | 操作 |
| ------------ | --------- | --------- |
| `Plus`       | 黑色     | 通过添加颜色变亮： Sc + Dc |
| `Modulate`   | 白色     | 通过按颜色加深： Sc ·台 | 
| `Screen`     | 黑色     | 补充的产品： Sc + Dc &ndash; Sc ·台 |
| `Overlay`    | 灰色      | 逆变 `HardLight` |
| `Darken`     | 白色     | 最小颜色：最小 (Sc，Dc)  |
| `Lighten`    | 黑色     | 最大颜色：最大 (Sc，Dc)  |
| `ColorDodge` | 黑色     | 基于源的变亮目标 |
| `ColorBurn`  | 白色     | 基于源加深目标 | 
| `HardLight`  | 灰色      | 类似于恶劣聚光灯的效果 |
| `SoftLight`  | 灰色      | 类似于软聚光灯的效果 | 
| `Difference` | 黑色     | 从较浅的： Abs (Dc Sc 中减去较暗 &ndash;)  | 
| `Exclusion`  | 黑色     | 类似于， `Difference` 但对比度更低 |
| `Multiply`   | 白色     | 通过按颜色加深： Sc ·台 |

虽然这两个源中的表示法不相同，但可以在 W3C [**合成和混合级别 1**](https://www.w3.org/TR/compositing-1/) 规范和 Skia [**SkBlendMode 引用**](https://skia.org/user/api/SkBlendMode_Reference)中找到更详细的算法。 请记住， `Plus` 通常被视为 Porter-Duff blend 模式，并且 `Modulate` 不是 W3C 规范的组成部分。

如果源是透明的，则对于除之外的所有分离混合模式 `Modulate` ，混合模式不起作用。 正如前面所看到的， `Modulate` blend 模式将 alpha 通道合并为乘法。 否则，与 `Modulate` 的效果相同 `Multiply` 。 

请注意两个名为和的模式 `ColorDodge` `ColorBurn` 。 图像暗室实践中的单词 _减减_ 和 _加深_ 。 弱视通过将光向负照射来进行照相打印。 无光源，打印为白色。 当打印时间较长时，打印会变暗。 打印人员通常使用手或小对象来阻止某些光源在打印的某个部分下降，使该区域变浅。 这称为 _dodging_。 反之，其中包含洞的不透明材料 (或直接阻碍大多数光源) ，可用于指示特定位置中的更多光源，使其变得加深（称为 _烧_）。

**减减和加深**程序非常类似于**淡化和变暗**。 XAML 文件的结构是相同的，但具有不同的元素名称，并且代码隐藏文件也非常相似，但这两种混合模式的效果完全不同：

[![减减和加深](separable-images/DodgeAndBurn.png "减减和加深")](separable-images/DodgeAndBurn-Large.png#lightbox)

对于较小 `Slider` 的值， `Lighten` 模式首先会使暗区域变亮，同时使 `ColorDodge` 更均匀地变浅。

图像处理应用程序通常允许将 dodging 和刻录限制为特定区域，就像在暗室中一样。 这可以通过渐变来实现，也可以通过具有不同灰色阴影的位图来实现。

## <a name="exploring-the-separable-blend-modes"></a>探索分离混合模式

" **分离混合模式** " 页允许您检查所有分离混合模式。 它使用一种 blend 模式显示位图目标和彩色矩形源。 

XAML 文件定义 `Picker` (来选择混合模式) 和四个滑块。 前三个滑杆允许您设置源的红色、绿色和蓝色部分。 第四个滑块旨在通过设置灰色底纹来覆盖这些值。 不确定各个滑杆，但颜色表示其功能：

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaviews="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.SeparableBlendModesPage"
             Title="Separable Blend Modes">

    <StackLayout>
        <skiaviews:SKCanvasView x:Name="canvasView"
                                VerticalOptions="FillAndExpand"
                                PaintSurface="OnCanvasViewPaintSurface" />

        <Picker x:Name="blendModePicker"
                Title="Blend Mode"
                Margin="10, 0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKBlendMode}">
                    <x:Static Member="skia:SKBlendMode.Plus" />
                    <x:Static Member="skia:SKBlendMode.Modulate" />
                    <x:Static Member="skia:SKBlendMode.Screen" />
                    <x:Static Member="skia:SKBlendMode.Overlay" />
                    <x:Static Member="skia:SKBlendMode.Darken" />
                    <x:Static Member="skia:SKBlendMode.Lighten" />
                    <x:Static Member="skia:SKBlendMode.ColorDodge" />
                    <x:Static Member="skia:SKBlendMode.ColorBurn" />
                    <x:Static Member="skia:SKBlendMode.HardLight" />
                    <x:Static Member="skia:SKBlendMode.SoftLight" />
                    <x:Static Member="skia:SKBlendMode.Difference" />
                    <x:Static Member="skia:SKBlendMode.Exclusion" />
                    <x:Static Member="skia:SKBlendMode.Multiply" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Slider x:Name="redSlider"
                MinimumTrackColor="Red"
                MaximumTrackColor="Red"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Slider x:Name="greenSlider"
                MinimumTrackColor="Green"
                MaximumTrackColor="Green"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Slider x:Name="blueSlider"
                MinimumTrackColor="Blue"
                MaximumTrackColor="Blue"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Slider x:Name="graySlider"
                MinimumTrackColor="Gray"
                MaximumTrackColor="Gray"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="colorLabel"
               HorizontalTextAlignment="Center" />

    </StackLayout>
</ContentPage>
```

代码隐藏文件加载一个位图资源，并将其绘制两次，一次在画布的上半部分，并再次出现在画布的下半部分：

```csharp
public partial class SeparableBlendModesPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(SeparableBlendModesPage),
                        "SkiaSharpFormsDemos.Media.Banana.jpg"); 

    public SeparableBlendModesPage()
    {
        InitializeComponent();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs e)
    {
        if (sender == graySlider)
        {
            redSlider.Value = greenSlider.Value = blueSlider.Value = graySlider.Value;
        }

        colorLabel.Text = String.Format("Color = {0:X2} {1:X2} {2:X2}",
                                        (byte)(255 * redSlider.Value),
                                        (byte)(255 * greenSlider.Value),
                                        (byte)(255 * blueSlider.Value));

        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Draw bitmap in top half
        SKRect rect = new SKRect(0, 0, info.Width, info.Height / 2);
        canvas.DrawBitmap(bitmap, rect, BitmapStretch.Uniform);

        // Draw bitmap in bottom halr
        rect = new SKRect(0, info.Height / 2, info.Width, info.Height);
        canvas.DrawBitmap(bitmap, rect, BitmapStretch.Uniform);

        // Get values from XAML controls
        SKBlendMode blendMode =
            (SKBlendMode)(blendModePicker.SelectedIndex == -1 ?
                                        0 : blendModePicker.SelectedItem);

        SKColor color = new SKColor((byte)(255 * redSlider.Value),
                                    (byte)(255 * greenSlider.Value),
                                    (byte)(255 * blueSlider.Value));

        // Draw rectangle with blend mode in bottom half
        using (SKPaint paint = new SKPaint())
        {
            paint.Color = color;
            paint.BlendMode = blendMode;
            canvas.DrawRect(rect, paint);
        }
    }
}
```

在处理程序的底部 `PaintSurface` ，使用所选混合模式和选定颜色在第二个位图上绘制一个矩形。 可以将底部的已修改位图与顶部的原始位图进行比较：

[![可分离的混合模式](separable-images/SeparableBlendModes.png "可分离的混合模式")](separable-images/SeparableBlendModes-Large.png#lightbox)

## <a name="additive-and-subtractive-primary-colors"></a>加法和 subtractive 主要颜色

" **主要颜色** " 页绘制三个交叠的红色、绿色和蓝色圆圈：

[![加法原色](separable-images/PrimaryColors-Additive.png "加法原色")](separable-images/PrimaryColors-Additive.png#lightbox)

这些是附加的主要颜色。 任意两个的组合产生蓝绿色、洋红色和黄色，三个组合均为白色。

这三个圆圈以模式绘制 `SKBlendMode.Plus` ，但您也可以使用 `Screen` 、 `Lighten` 或 `Difference` 来实现相同效果。 这是程序：

```csharp
public class PrimaryColorsPage : ContentPage
{
    bool isSubtractive;

    public PrimaryColorsPage ()
    {
        Title = "Primary Colors";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;

        // Switch between additive and subtractive primaries at tap
        TapGestureRecognizer tap = new TapGestureRecognizer();
        tap.Tapped += (sender, args) =>
        {
            isSubtractive ^= true;
            canvasView.InvalidateSurface();
        };
        canvasView.GestureRecognizers.Add(tap);

        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKPoint center = new SKPoint(info.Rect.MidX, info.Rect.MidY);
        float radius = Math.Min(info.Width, info.Height) / 4;
        float distance = 0.8f * radius;     // from canvas center to circle center
        SKPoint center1 = center + 
            new SKPoint(distance * (float)Math.Cos(9 * Math.PI / 6),
                        distance * (float)Math.Sin(9 * Math.PI / 6));
        SKPoint center2 = center +
            new SKPoint(distance * (float)Math.Cos(1 * Math.PI / 6),
                        distance * (float)Math.Sin(1 * Math.PI / 6));
        SKPoint center3 = center +
            new SKPoint(distance * (float)Math.Cos(5 * Math.PI / 6),
                        distance * (float)Math.Sin(5 * Math.PI / 6));

        using (SKPaint paint = new SKPaint())
        {
            if (!isSubtractive)
            {
                paint.BlendMode = SKBlendMode.Plus; 
                System.Diagnostics.Debug.WriteLine(paint.BlendMode);

                paint.Color = SKColors.Red;
                canvas.DrawCircle(center1, radius, paint);

                paint.Color = SKColors.Lime;    // == (00, FF, 00)
                canvas.DrawCircle(center2, radius, paint);

                paint.Color = SKColors.Blue;
                canvas.DrawCircle(center3, radius, paint);
            }
            else
            {
                paint.BlendMode = SKBlendMode.Multiply
                System.Diagnostics.Debug.WriteLine(paint.BlendMode);

                paint.Color = SKColors.Cyan;
                canvas.DrawCircle(center1, radius, paint);

                paint.Color = SKColors.Magenta;
                canvas.DrawCircle(center2, radius, paint);

                paint.Color = SKColors.Yellow;
                canvas.DrawCircle(center3, radius, paint);
            }
        }
    }
}
```

该程序包含 `TabGestureRecognizer` 。 点击或单击屏幕时，程序将使用 `SKBlendMode.Multiply` 显示三个 subtractive 主副本：

[![Subtractive 主要颜色](separable-images/PrimaryColors-Subtractive.png "Subtractive 主要颜色")](separable-images/PrimaryColors-Subtractive-Large.png#lightbox)

`Darken`此模式也适用于此相同的效果。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)