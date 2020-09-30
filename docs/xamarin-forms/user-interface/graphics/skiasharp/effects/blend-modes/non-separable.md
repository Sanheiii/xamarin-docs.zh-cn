---
title: 非分离混合模式
description: 使用非分离混合模式可更改色调、饱和度或发光度。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 97FA2730-87C0-4914-8C9F-C64A02CF9EEF
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a77ebb07a09c1bbd2df482c81040f271cdf8f56e
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556342"
---
# <a name="the-non-separable-blend-modes"></a>非分离混合模式

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

正如您在 [**SkiaSharp 分离混合模式**](separable.md)一文中看到的那样，分离混合模式分别对红色、绿色和蓝色通道执行操作。 非分离混合模式不能。 通过在颜色的色调、饱和度和发光度上操作，不可分离的混合模式可以以有趣的方式改变颜色：

![不可分离的示例](non-separable-images/NonSeparableSample.png "不可分离的示例")

## <a name="the-hue-saturation-luminosity-model"></a>色调-饱和度-明度模型

若要理解非分离混合模式，必须将目标和源像素视为色调-饱和度-明度模型中的颜色。  (发光度也称为亮度。 ) 

HSL 颜色模型已在 "[**与 Xamarin.Forms 集成**](../../basics/integration.md)" 和 "示例程序" 一文中讨论，它允许使用 HSL 颜色进行试验。 您可以通过 `SKColor` 静态方法使用色调、饱和度和亮度值创建值 [`SKColor.FromHsl`](xref:SkiaSharp.SKColor.FromHsl*) 。

色调表示颜色的主导波长。 色调值介于0到360之间，并循环到加法和 subtractive 主副本： Red 为值0，黄色为60，绿色表示120，青色为180，蓝色表示240，洋红色为300，循环返回到红色（在360）。

例如，如果没有主颜色 &mdash; （例如，颜色为白色或黑色）或灰色阴影，则不 &mdash; 定义色相，通常将其设置为0。 

饱和度值的范围为0到100，表示颜色的纯度。 饱和度值100是最纯粹颜色，而小于100的值会导致颜色变得更 grayish。 饱和度值为0时会产生灰色阴影。

亮度 (或亮度) 值指示颜色的明亮程度。 亮度值0为黑色，而不考虑其他设置。 同样，发光度值100为白色。 

HSL 值 (0，100，50) 是 RGB 值 (FF，00，00) ，它是纯红色。 HSL 值 (180，100，50) 是 RGB 值 (00，FF，FF) ，纯青色。 当饱和度降低时，主导色组件会减少，并增加其他组件。 如果饱和度级别为0，则所有组件都是相同的，颜色为灰色阴影。 降低亮度以转为黑色;增加亮度以转为白。

## <a name="the-blend-modes-in-detail"></a>混合模式详细信息

与其他混合模式一样，四个非分离混合模式都涉及目标 (这通常是位图图像) 和源（通常是一种颜色或渐变）。 混合模式结合了目标和源中的色相、饱和度和明度值：

| 混合模式   | 来自源的组件 | 目标中的组件 |
| ------------ | ---------------------- | --------------------------- |
| `Hue`        | 色调                    | 饱和度和发光度   |
| `Saturation` | 饱和度             | 色调和发光度          |
| `Color`      | 色调和饱和度     | 明度                  | 
| `Luminosity` | 明度             | 色调和饱和度          | 

请参阅算法的 W3C [**合成和混合等级 1**](https://www.w3.org/TR/compositing-1/) 规范。

**非分离混合模式**页面包含 `Picker` 用于选择其中一个混合模式的，三个 `Slider` 视图用于选择 HSL 颜色：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaviews="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.NonSeparableBlendModesPage"
             Title="Non-Separable Blend Modes">

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
                    <x:Static Member="skia:SKBlendMode.Hue" />
                    <x:Static Member="skia:SKBlendMode.Saturation" />
                    <x:Static Member="skia:SKBlendMode.Color" />
                    <x:Static Member="skia:SKBlendMode.Luminosity" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Slider x:Name="hueSlider"
                Maximum="360"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Slider x:Name="satSlider"
                Maximum="100"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Slider x:Name="lumSlider"
                Maximum="100"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <StackLayout Orientation="Horizontal">
            <Label x:Name="hslLabel"
                   HorizontalOptions="CenterAndExpand" />

            <Label x:Name="rgbLabel"
                   HorizontalOptions="CenterAndExpand" />

        </StackLayout>
    </StackLayout>
</ContentPage>
```

为了节省空间，在 `Slider` 程序的用户界面中不会标识这三个视图。 您需要记住，顺序是色调、饱和度和发光度。 页面底部的两个 `Label` 视图显示 HSL 和 RGB 颜色值。

代码隐藏文件加载一个位图资源，在画布上将其显示为尽可能大，然后使用矩形覆盖画布。 矩形颜色基于三个 `Slider` 视图，混合模式是在中选择的一种 `Picker` ：

```csharp
public partial class NonSeparableBlendModesPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(NonSeparableBlendModesPage),
                        "SkiaSharpFormsDemos.Media.Banana.jpg");
    SKColor color;

    public NonSeparableBlendModesPage()
    {
        InitializeComponent();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs e)
    {
        // Calculate new color based on sliders
        color = SKColor.FromHsl((float)hueSlider.Value,
                                (float)satSlider.Value,
                                (float)lumSlider.Value);

        // Use labels to display HSL and RGB color values
        color.ToHsl(out float hue, out float sat, out float lum);

        hslLabel.Text = String.Format("HSL = {0:F0} {1:F0} {2:F0}",
                                      hue, sat, lum);

        rgbLabel.Text = String.Format("RGB = {0:X2} {1:X2} {2:X2}",
                                      color.Red, color.Green, color.Blue);

        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform);

        // Get blend mode from Picker
        SKBlendMode blendMode =
            (SKBlendMode)(blendModePicker.SelectedIndex == -1 ?
                                        0 : blendModePicker.SelectedItem);

        using (SKPaint paint = new SKPaint())
        {
            paint.Color = color;
            paint.BlendMode = blendMode;
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

请注意，该程序不显示由三个滑块选定的 HSL 颜色值。 相反，它会从这些滑块创建颜色值，然后使用 [`ToHsl`](xref:SkiaSharp.SKColor.ToHsl*) 方法来获取色相、饱和度和发光度值。 这是因为 `FromHsl` 方法会将 HSL 颜色转换为 RGB 颜色，这是在结构内部存储的 `SKColor` 。 `ToHsl`方法将从 RGB 转换为 HSL，但结果不会始终是原始值。 

例如， `FromHsl` 将 HSL 值 (180、50、0) 转换为 RGB 颜色 (0，0，0) ，因为 `Luminosity` 为零。 `ToHsl`方法将 (0，0，0) 的 RGB 颜色转换为 HSL 色 (0，0，0) ，因为色相和饱和度值无关。 使用此程序时，您可以看到该程序正在使用的 HSL 颜色的表示形式，而不是您使用滑块指定的那个颜色。

`SKBlendModes.Hue`混合模式使用源的色调级别，同时保留目标的饱和度和发光度级别。 在测试此混合模式时，饱和度和发光度滑块必须设置为0或100以外的内容，因为在这些情况下，不会唯一定义色调。

[![非分离混合模式-色调](non-separable-images/NonSeparableBlendModes-Hue.png "非分离混合模式-色调")](non-separable-images/NonSeparableBlendModes-Hue-Large.png#lightbox)

使用将滑块设置为0时 (如) 左侧的 iOS 屏幕截图所示，所有内容都会变为 reddish。 但这并不意味着图像完全没有绿色和蓝色。 显然，结果中仍存在灰色阴影。 例如，RGB 颜色 (40，40，C0) 等效于 HSL 色 (240，50，50) 。 色调为蓝色，但饱和度值为50，表示还存在红色和绿色组件。 如果色调设置为0，则 `SKBlendModes.Hue` HSL 颜色为 (0，50，50) ，这是 RGB 颜色 (c0，40，40) 。 还有蓝色和绿色的组件，但现在主导组件为红色。

`SKBlendModes.Saturation`混合模式将源的饱和度级别与目标的色调和发光度组合在一起。 与色调一样，如果亮度为0或100，则饱和度不会定义得很好。 理论上，这两个极端之间的任何发光度设置都应该有效。 但是，发光度设置似乎会影响结果的影响。 将亮度设置为50，你可以了解如何设置图片的饱和度级别：

[![非分离混合模式-饱和度](non-separable-images/NonSeparableBlendModes-Saturation.png "非分离混合模式-饱和度")](non-separable-images/NonSeparableBlendModes-Saturation-Large.png#lightbox)

您可以使用这种混合模式增加单调图像的颜色饱和度，也可以将饱和度降低到零 (如) 的 iOS 屏幕截图中的 iOS 屏幕截图，只包含灰色阴影。

`SKBlendModes.Color`混合模式保留目标的发光度，但使用源的色调和饱和度。 同样，这意味着，极端情况下的亮度滑块的任何设置都应能正常工作。 

[![非分离混合模式-颜色](non-separable-images/NonSeparableBlendModes-Color.png "非分离混合模式-颜色")](non-separable-images/NonSeparableBlendModes-Color-Large.png#lightbox)

稍后，你将看到此混合模式的应用程序。

最后， `SKBlendModes.Luminosity` blend 模式与相反 `SKBlendModes.Color` 。 它保留目标的色相和饱和度，但使用源的发光度。 `Luminosity`混合模式是批处理中最神秘的模式： "色调" 和 "饱和度" 滑块会影响图像，但是即使在中等亮度，图像也不是不同的：

[![非分离混合模式-亮度](non-separable-images/NonSeparableBlendModes-Luminosity.png "非分离混合模式-亮度")](non-separable-images/NonSeparableBlendModes-Luminosity-Large.png#lightbox)

理论上，增加或减少图像发光度应使其变得更浅或更暗。 有趣的是， [Skia **SkBlendMode 引用**中的发光度示例](https://skia.org/user/api/SkBlendMode_Reference#Luminosity)非常类似。

通常情况下，您可能不希望使用一种不可分离的混合模式，其中的源包含应用于整个目标图像的单个颜色。 效果只是太棒了。 你需要将效果限制为图像的一个部分。 在这种情况下，源可能会纳入 transparancy，或者可能会将源限制为较小的图形。

## <a name="a-matte-for-a-separable-mode"></a>分离模式的遮罩

下面是 [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) 示例中作为一项资源包含的位图。 文件名为 **Banana.jpg**：

![香蕉猴子](non-separable-images/Banana.jpg "香蕉猴子")

可以创建只包含香蕉的遮罩。 这也是 [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) 示例中的资源。 文件名为 **BananaMatte.png**：

![香蕉遮罩](non-separable-images/BananaMatte.png "香蕉遮罩")

除了黑色香蕉形状外，位图的其余部分也是透明的。

**Blue 香蕉**页面使用该遮罩改变猴子所持有的香蕉的色调和饱和度，但不更改图像中的任何其他内容。 

在下面的 `BlueBananaPage` 类中， **Banana.jpg** 位图作为一个字段加载。 构造函数将 **BananaMatte.png** 位图作为对象加载 `matteBitmap` ，但它不会将该对象保留在构造函数之外。 而是创建一个名为的第三个位图 `blueBananaBitmap` 。 `matteBitmap`绘制 `blueBananaBitmap` 后跟一个， `SKPaint` 其 `Color` 设置为蓝色，其 `BlendMode` 设置为 `SKBlendMode.SrcIn` 。 `blueBananaBitmap`通常是透明的，但具有纯纯蓝色的香蕉图像：

```csharp
public class BlueBananaPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
        typeof(BlueBananaPage),
        "SkiaSharpFormsDemos.Media.Banana.jpg");

    SKBitmap blueBananaBitmap;

    public BlueBananaPage()
    {
        Title = "Blue Banana";

        // Load banana matte bitmap (black on transparent)
        SKBitmap matteBitmap = BitmapExtensions.LoadBitmapResource(
            typeof(BlueBananaPage),
            "SkiaSharpFormsDemos.Media.BananaMatte.png");

        // Create a bitmap with a solid blue banana and transparent otherwise
        blueBananaBitmap = new SKBitmap(matteBitmap.Width, matteBitmap.Height);

        using (SKCanvas canvas = new SKCanvas(blueBananaBitmap))
        {
            canvas.Clear();
            canvas.DrawBitmap(matteBitmap, new SKPoint(0, 0));

            using (SKPaint paint = new SKPaint())
            {
                paint.Color = SKColors.Blue;
                paint.BlendMode = SKBlendMode.SrcIn;
                canvas.DrawPaint(paint);
            }
        }

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

        canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform);

        using (SKPaint paint = new SKPaint())
        {
            paint.BlendMode = SKBlendMode.Color;
            canvas.DrawBitmap(blueBananaBitmap, 
                              info.Rect, 
                              BitmapStretch.Uniform, 
                              paint: paint);
        }
    }
}
```

`PaintSurface`处理程序将用保存香蕉的猴子来绘制位图。 此代码后跟的显示为 `blueBananaBitmap` `SKBlendMode.Color` 。 在香蕉的表面上，每个像素的色调和饱和度均替换为纯色，这对应于色相值240，饱和度值为100。 不过，发光度仍保持不变，这意味着香蕉仍具有逼真的纹理，而不管其新颜色：

[![Blue 香蕉](non-separable-images/BlueBanana.png "Blue 香蕉")](non-separable-images/BlueBanana-Large.png#lightbox)

尝试将混合模式更改为 `SKBlendMode.Saturation` 。 香蕉是黄色的，但它的黄色更明显。 在实际应用程序中，这可能比将香蕉变为蓝色更为理想。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)