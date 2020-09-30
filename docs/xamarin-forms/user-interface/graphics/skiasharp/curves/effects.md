---
title: SkiaSharp 中的路径效果
description: 本文介绍了各种 SkiaSharp 路径效果，这些效果允许将路径用于描边和填充，并使用示例代码对此进行演示。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 95167D1F-A718-405A-AFCC-90E596D422F3
author: davidbritch
ms.author: dabritch
ms.date: 07/29/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 074af11d7873ed44c0a48f923f7560dd50cea6a5
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563232"
---
# <a name="path-effects-in-skiasharp"></a>SkiaSharp 中的路径效果

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_发现允许将路径用于描边和填充的各种路径效果_

*路径效果*是 [`SKPathEffect`](xref:SkiaSharp.SKPathEffect) 类的一个实例，该类是使用类定义的8个静态创建方法中的一种创建的。 然后，将 `SKPathEffect` 对象的属性设置为 [`PathEffect`](xref:SkiaSharp.SKPaint.PathEffect) [`SKPaint`](xref:SkiaSharp.SKPaint) 对象的属性，以获得各种有趣的效果，例如，使用较小的复制路径对线条进行描边：

![链接链示例](effects-images/patheffectsample.png)

路径效果允许：

- 使用圆点和短划线绘制线条
- 使用任意填充路径为线条描边
- 使用影线线条填充区域
- 使用平铺路径填充区域
- 使尖角圆角
- 向直线和曲线添加随机 "抖动"

此外，您可以将两个或多个路径效果组合在一起。

本文还演示了如何使用 [`GetFillPath`](xref:SkiaSharp.SKPaint.GetFillPath*) 的方法 `SKPaint` 将一个路径转换为另一个路径，方法是应用的属性 `SKPaint` ，包括 `StrokeWidth` 和 `PathEffect` 。 这会产生一些有趣的技巧，如获取作为另一个路径轮廓的路径。 `GetFillPath` 在与路径效果建立连接时也有帮助。

## <a name="dots-and-dashes"></a>点和短划线

[`PathEffect.CreateDash`](xref:SkiaSharp.SKPathEffect.CreateDash(System.Single[],System.Single))文章[**点和短划线**](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md)中介绍了方法的用法。 该方法的第一个参数是一个数组，该数组包含一个偶数个两个或更多值，在短划线的长度和短划线之间的间隔长度之间交替：

```csharp
public static SKPathEffect CreateDash (Single[] intervals, Single phase)
```

这些值与笔划宽度 *无关* 。 例如，如果笔划宽度为10，并且您需要包含正方形虚线和正方形间隙的线条，请将 `intervals` 数组设置为 {10，10}。 `phase`自变量指示行的开始位置。 在此示例中，如果希望行以正方形间隙开头，请将设置 `phase` 为10。

虚线的末尾受的 `StrokeCap` 属性的影响 `SKPaint` 。 对于宽笔划宽度，将此属性设置为 `SKStrokeCap.Round` 以舍入虚线的末尾非常常见。 在这种情况下，数组中的值 `intervals` *不* 包括舍入所得的额外长度。 这种情况意味着圆点需要指定宽度为零。 若要将笔划宽度为10，若要在同一直径的点之间创建具有圆点和间隙的线条，请使用 `intervals` 数组 {0，20}。

**动态虚线文本**页类似于通过将对象的属性设置为来显示分级文本字符一[**文中**](~/xamarin-forms/user-interface/graphics/skiasharp/basics/text.md)所**述的文本**页面 `Style` `SKPaint` `SKPaintStyle.Stroke` 。 此外， **动态虚线文本** 使用 `SKPathEffect.CreateDash` 为此轮廓提供虚线外观，并且该程序还对该方法的参数进行了动画处理， `phase` `SKPathEffect.CreateDash` 以使点看起来像是文本字符。 页面处于横向模式：

[![动态虚线文本页的三向屏幕截图](effects-images/animateddottedtext-small.png)](effects-images/animateddottedtext-large.png#lightbox)

[`AnimatedDottedTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DotDashMorphPage.cs)类从定义一些常量开始，还会替代动画的 `OnAppearing` 和 `OnDisappearing` 方法：

```csharp
public class AnimatedDottedTextPage : ContentPage
{
    const string text = "DOTTED";
    const float strokeWidth = 10;
    static readonly float[] dashArray = { 0, 2 * strokeWidth };

    SKCanvasView canvasView;
    bool pageIsActive;

    public AnimatedDottedTextPage()
    {
        Title = "Animated Dotted Text";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    protected override void OnAppearing()
    {
        base.OnAppearing();
        pageIsActive = true;

        Device.StartTimer(TimeSpan.FromSeconds(1f / 60), () =>
        {
            canvasView.InvalidateSurface();
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

`PaintSurface`处理程序首先创建一个 `SKPaint` 用于显示文本的对象。 根据  `TextSize` 屏幕的宽度调整属性：

```csharp
public class AnimatedDottedTextPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Create an SKPaint object to display the text
        using (SKPaint textPaint = new SKPaint
            {
                Style = SKPaintStyle.Stroke,
                StrokeWidth = strokeWidth,
                StrokeCap = SKStrokeCap.Round,
                Color = SKColors.Blue,
            })
        {
            // Adjust TextSize property so text is 95% of screen width
            float textWidth = textPaint.MeasureText(text);
            textPaint.TextSize *= 0.95f * info.Width / textWidth;

            // Find the text bounds
            SKRect textBounds = new SKRect();
            textPaint.MeasureText(text, ref textBounds);

            // Calculate offsets to center the text on the screen
            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2 - textBounds.MidY;

            // Animate the phase; t is 0 to 1 every second
            TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
            float t = (float)(timeSpan.TotalSeconds % 1 / 1);
            float phase = -t * 2 * strokeWidth;

            // Create dotted line effect based on dash array and phase
            using (SKPathEffect dashEffect = SKPathEffect.CreateDash(dashArray, phase))
            {
                // Set it to the paint object
                textPaint.PathEffect = dashEffect;

                // And draw the text
                canvas.DrawText(text, xText, yText, textPaint);
            }
        }
    }
}
```

在方法的末尾， `SKPathEffect.CreateDash` 使用 `dashArray` 定义为字段的和经过动画处理的值调用方法 `phase` 。 `SKPathEffect`实例设置为 `PathEffect` `SKPaint` 要显示文本的对象的属性。

或者，您可以在 `SKPathEffect` 测量文本之前将对象设置为 `SKPaint` 对象，并在页面上居中。 但是，在这种情况下，动态点和虚线会导致呈现文本的大小变化，而文本往往振动。  (试试！ ) 

你还会注意到，在文本字符的周围有围绕的圆点，在每个闭合曲线中都有特定的点，这些点看起来好像是弹入和退出。 这是定义字符轮廓的路径的开始和结束位置。 如果路径长度不是短划线模式长度的整数倍 (在本例中为20个像素) ，则仅该模式的一部分可容纳在路径的末尾。

可以调整虚线模式的长度以适合路径长度，但这需要确定路径的长度，这是一种在项目 [**路径信息和枚举**](information.md)中介绍的技术。

**点/虚线平滑**计划对虚线模式本身进行动画处理，使虚线看起来像是一个点，这将再次组合起来以形成破折号：

[![点划线平滑页面的三向屏幕截图](effects-images/dotdashmorph-small.png)](effects-images/dotdashmorph-large.png#lightbox)

[`DotDashMorphPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DotDashMorphPage.cs) `OnAppearing` 与上一程序一样，类会重写和 `OnDisappearing` 方法，但类将对象定义 `SKPaint` 为字段：

```csharp
public class DotDashMorphPage : ContentPage
{
    const float strokeWidth = 30;
    static readonly float[] dashArray = new float[4];

    SKCanvasView canvasView;
    bool pageIsActive = false;

    SKPaint ellipsePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = strokeWidth,
        StrokeCap = SKStrokeCap.Round,
        Color = SKColors.Blue
    };
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Create elliptical path
        using (SKPath ellipsePath = new SKPath())
        {
            ellipsePath.AddOval(new SKRect(50, 50, info.Width - 50, info.Height - 50));

            // Create animated path effect
            TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
            float t = (float)(timeSpan.TotalSeconds % 3 / 3);
            float phase = 0;

            if (t < 0.25f)  // 1, 0, 1, 2 --> 0, 2, 0, 2
            {
                float tsub = 4 * t;
                dashArray[0] = strokeWidth * (1 - tsub);
                dashArray[1] = strokeWidth * 2 * tsub;
                dashArray[2] = strokeWidth * (1 - tsub);
                dashArray[3] = strokeWidth * 2;
            }
            else if (t < 0.5f)  // 0, 2, 0, 2 --> 1, 2, 1, 0
            {
                float tsub = 4 * (t - 0.25f);
                dashArray[0] = strokeWidth * tsub;
                dashArray[1] = strokeWidth * 2;
                dashArray[2] = strokeWidth * tsub;
                dashArray[3] = strokeWidth * 2 * (1 - tsub);
                phase = strokeWidth * tsub;
            }
            else if (t < 0.75f) // 1, 2, 1, 0 --> 0, 2, 0, 2
            {
                float tsub = 4 * (t - 0.5f);
                dashArray[0] = strokeWidth * (1 - tsub);
                dashArray[1] = strokeWidth * 2;
                dashArray[2] = strokeWidth * (1 - tsub);
                dashArray[3] = strokeWidth * 2 * tsub;
                phase = strokeWidth * (1 - tsub);
            }
            else               // 0, 2, 0, 2 --> 1, 0, 1, 2
            {
                float tsub = 4 * (t - 0.75f);
                dashArray[0] = strokeWidth * tsub;
                dashArray[1] = strokeWidth * 2 * (1 - tsub);
                dashArray[2] = strokeWidth * tsub;
                dashArray[3] = strokeWidth * 2;
            }

            using (SKPathEffect pathEffect = SKPathEffect.CreateDash(dashArray, phase))
            {
                ellipsePaint.PathEffect = pathEffect;
                canvas.DrawPath(ellipsePath, ellipsePaint);
            }
        }
    }
}
```

`PaintSurface`处理程序基于页面大小创建一个椭圆路径，并执行用于设置和变量的长代码段 `dashArray` `phase` 。 由于动画变量的 `t` 范围介于0和1之间，因此 `if` 块会将时间分解为四个季度，其中每个季度的 `tsub` 范围也从0到1。 最后，程序将创建， `SKPathEffect` 并将其设置为 `SKPaint` 用于绘制的对象。

## <a name="from-path-to-path"></a>从路径到路径

[`GetFillPath`](xref:SkiaSharp.SKPaint.GetFillPath(SkiaSharp.SKPath,SkiaSharp.SKPath,System.Single))方法 `SKPaint` 根据对象中的设置将一个路径转换为另一个路径 `SKPaint` 。 若要查看其工作原理，请将 `canvas.DrawPath` 上一个程序中的调用替换为以下代码：

```csharp
SKPath newPath = new SKPath();
bool fill = ellipsePaint.GetFillPath(ellipsePath, newPath);
SKPaint newPaint = new SKPaint
{
    Style = fill ? SKPaintStyle.Fill : SKPaintStyle.Stroke
};
canvas.DrawPath(newPath, newPaint);

```

在此新代码中， `GetFillPath` 调用会将 `ellipsePath` 只是一个) 为的椭圆的 (转换为 `newPath` ，然后将显示 `newPaint` 。 `newPaint`对象是用所有默认属性设置创建的，只不过 `Style` 属性是基于的布尔返回值设置的 `GetFillPath` 。

除颜色外，视觉对象完全相同，但在中设置的颜色除外 `ellipsePaint` `newPaint` 。 不是在中定义的简单 `ellipsePath` 椭圆 `newPath` 包含多个用于定义点和短划线序列的路径轮廓。 这是应用 (的各种属性的结果 `ellipsePaint` ，具体而言，、 `StrokeWidth` `StrokeCap` 和 `PathEffect`) `ellipsePath` 并将生成的路径放置在中 `newPath` 。 `GetFillPath`方法返回一个布尔值，指示是否要填充目标路径; 在此示例中，返回值 `true` 用于填充路径。

尝试将上的 `Style` 设置更改 `newPaint` 为 `SKPaintStyle.Stroke` ，你将看到以一像素宽度行为分级显示的各个路径轮廓。

## <a name="stroking-with-a-path"></a>使用路径进行描边

[`SKPathEffect.Create1DPath`](xref:SkiaSharp.SKPathEffect.Create1DPath(SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPath1DPathEffectStyle))方法在概念上类似于， `SKPathEffect.CreateDash` 只不过您指定了一个路径，而不是短划线和间隙的模式。 此路径将被多次复制，以对直线或曲线进行描边。

语法为：

```csharp
public static SKPathEffect Create1DPath (SKPath path, Single advance,
                                         Single phase, SKPath1DPathEffectStyle style)
```

通常，您传递给的路径将很 `Create1DPath` 小，并围绕点 (0，0) 。 `advance`参数指示路径中心之间的距离，因为在线条上复制路径。 通常将此参数设置为路径的大致宽度。 `phase`与在方法中一样，自变量在此处扮演相同的角色 `CreateDash` 。

[`SKPath1DPathEffectStyle`](xref:SkiaSharp.SKPath1DPathEffectStyle)有三个成员：

- `Translate`
- `Rotate`
- `Morph`

该 `Translate` 成员会使路径在沿直线或曲线复制时保持相同的方向。 对于 `Rotate` ，该路径将根据曲线的切线旋转。 该路径的水平线条方向相同。 `Morph` 类似于， `Rotate` 不同之处在于路径本身也是弯曲的，以匹配所绘制线条的曲率。

" **1D 路径效果** " 页演示了这三个选项。 [**OneDimensionalPathEffectPage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/OneDimensionalPathEffectPage.xaml)文件定义一个选取器，其中包含与枚举的三个成员相对应的三个项：

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Curves.OneDimensionalPathEffectPage"
             Title="1D Path Effect">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Picker x:Name="effectStylePicker"
                Title="Effect Style"
                Grid.Row="0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type x:String}">
                    <x:String>Translate</x:String>
                    <x:String>Rotate</x:String>
                    <x:String>Morph</x:String>
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface"
                           Grid.Row="1" />
    </Grid>
</ContentPage>
```

[**OneDimensionalPathEffectPage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/OneDimensionalPathEffectPage.xaml.cs)代码隐藏文件将三个 `SKPathEffect` 对象定义为字段。 这些都是通过使用 `SKPathEffect.Create1DPath` 创建的对象创建的 `SKPath` `SKPath.ParseSvgPathData` 。 第一个是简单的框，第二个是菱形形状，第三个是矩形。 它们用于演示三种效果样式：

```csharp
public partial class OneDimensionalPathEffectPage : ContentPage
{
    SKPathEffect translatePathEffect =
        SKPathEffect.Create1DPath(SKPath.ParseSvgPathData("M -10 -10 L 10 -10, 10 10, -10 10 Z"),
                                  24, 0, SKPath1DPathEffectStyle.Translate);

    SKPathEffect rotatePathEffect =
        SKPathEffect.Create1DPath(SKPath.ParseSvgPathData("M -10 0 L 0 -10, 10 0, 0 10 Z"),
                                  20, 0, SKPath1DPathEffectStyle.Rotate);

    SKPathEffect morphPathEffect =
        SKPathEffect.Create1DPath(SKPath.ParseSvgPathData("M -25 -10 L 25 -10, 25 10, -25 10 Z"),
                                  55, 0, SKPath1DPathEffectStyle.Morph);

    SKPaint pathPaint = new SKPaint
    {
        Color = SKColors.Blue
    };

    public OneDimensionalPathEffectPage()
    {
        InitializeComponent();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        if (canvasView != null)
        {
            canvasView.InvalidateSurface();
        }
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath path = new SKPath())
        {
            path.MoveTo(new SKPoint(0, 0));
            path.CubicTo(new SKPoint(2 * info.Width, info.Height),
                         new SKPoint(-info.Width, info.Height),
                         new SKPoint(info.Width, 0));

            switch ((string)effectStylePicker.SelectedItem))
            {
                case "Translate":
                    pathPaint.PathEffect = translatePathEffect;
                    break;

                case "Rotate":
                    pathPaint.PathEffect = rotatePathEffect;
                    break;

                case "Morph":
                    pathPaint.PathEffect = morphPathEffect;
                    break;
            }

            canvas.DrawPath(path, pathPaint);
        }
    }
}
```

`PaintSurface`处理程序创建一条贝塞尔曲线，该曲线会循环其自身，并访问选取器来确定 `PathEffect` 应该使用哪个曲线来对其进行描边。 这三个选项（ `Translate` 、 `Rotate` 和 `Morph` ）从左到右显示：

[![1D 路径效果页面的三向屏幕截图](effects-images/1dpatheffect-small.png)](effects-images/1dpatheffect-large.png#lightbox)

始终填充方法中指定的路径 `SKPathEffect.Create1DPath` 。 `DrawPath`如果 `SKPaint` 对象的 `PathEffect` 属性设置为1d 路径效果，则方法中指定的路径始终为描边。 请注意， `pathPaint` 对象没有任何 `Style` 设置，这通常默认为 `Fill` ，但不考虑该路径。

示例中使用的框 `Translate` 是20像素正方形， `advance` 参数设置为24。 这种差异会导致线条的水平或垂直之间出现间隔，但当线条为对角时，它们会重叠一点，因为框的对角线为28.3 像素。

示例中的菱形形状的 `Rotate` 宽度也为20个像素。 `advance`设置为20，这样，点就会继续触摸，因为菱形与直线的曲率一起旋转。

该示例中的矩形形状 `Morph` 为50像素宽， `advance` 设置为55可在矩形之间进行较小的间隔，因为它们在贝塞尔曲线周围弯曲。

如果 `advance` 参数小于路径大小，则复制的路径可能会重叠。 这可能会导致一些有趣的效果。 " **链接链** " 页显示一系列重叠的圆，它们看起来像是一个链接链，它在 catenary 的独特形状中挂起：

[![链接链页面的三向屏幕截图](effects-images/linkedchain-small.png)](effects-images/linkedchain-large.png#lightbox)

看起来非常接近，你会发现这些并不是真正的圆。 链中的每个链接都是两个弧形、调整大小和定位，因此它们看起来像是通过相邻链接连接的。

均匀权重分布的链或电缆以 catenary 的形式挂起。 以 catenary 的形式生成的一种形式是从一个拱的权重的压力分布中获益。 Catenary 具有看似简单的数学说明：

`y = a · cosh(x / a)`

*Cosh*是双曲余弦函数。 如果*x*等于0，则*cosh*为零， *y*等于 *。* 这就是 catenary 的中心。 与 *余弦* 函数一样， *cosh* *也称为，这*意味着 *cosh ( – x) * 等于 *cosh (x) *，并且值将增加以增加正值或负参数。 这些值描述构成 catenary 边的曲线。

查找 *适当的值，使* 其适应手机页面尺寸的 catenary 不是直接计算。 如果 *w* 和 *h* 是矩形的宽度和高度， *则的最佳值满足以下* 公式：

`cosh(w / 2 / a) = 1 + h / a`

类中的以下方法 [`LinkedChainPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/LinkedChainPage.cs) 通过引用等号左侧和右侧的两个表达式来合并此相等性 `left` `right` 。 对于的较小*值*， `left` 大于 `right` ; 对于的较大值， *a* `left` 小于 `right` 。 该 `while` 循环缩小 *了的*最佳值：

```csharp
float FindOptimumA(float width, float height)
{
    Func<float, float> left = (float a) => (float)Math.Cosh(width / 2 / a);
    Func<float, float> right = (float a) => 1 + height / a;

    float gtA = 1;         // starting value for left > right
    float ltA = 10000;     // starting value for left < right

    while (Math.Abs(gtA - ltA) > 0.1f)
    {
        float avgA = (gtA + ltA) / 2;

        if (left(avgA) < right(avgA))
        {
            ltA = avgA;
        }
        else
        {
            gtA = avgA;
        }
    }

    return (gtA + ltA) / 2;
}
```

`SKPath`链接的对象是在类的构造函数中创建的，然后将生成的 `SKPathEffect` 对象设置为 `PathEffect` `SKPaint` 存储为字段的对象的属性：

```csharp
public class LinkedChainPage : ContentPage
{
    const float linkRadius = 30;
    const float linkThickness = 5;

    Func<float, float, float> catenary = (float a, float x) => (float)(a * Math.Cosh(x / a));

    SKPaint linksPaint = new SKPaint
    {
        Color = SKColors.Silver
    };

    public LinkedChainPage()
    {
        Title = "Linked Chain";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Create the path for the individual links
        SKRect outer = new SKRect(-linkRadius, -linkRadius, linkRadius, linkRadius);
        SKRect inner = outer;
        inner.Inflate(-linkThickness, -linkThickness);

        using (SKPath linkPath = new SKPath())
        {
            linkPath.AddArc(outer, 55, 160);
            linkPath.ArcTo(inner, 215, -160, false);
            linkPath.Close();

            linkPath.AddArc(outer, 235, 160);
            linkPath.ArcTo(inner, 395, -160, false);
            linkPath.Close();

            // Set that path as the 1D path effect for linksPaint
            linksPaint.PathEffect =
                SKPathEffect.Create1DPath(linkPath, 1.3f * linkRadius, 0,
                                          SKPath1DPathEffectStyle.Rotate);
        }
    }
    ...
}
```

处理程序的主要工作 `PaintSurface` 是为 catenary 本身创建一个路径。 确定最佳 *a* 并将其存储在变量中后 `optA` ，它还需要计算从窗口顶部的偏移量。 然后，它可以累积 catenary 的值的集合，将其 `SKPoint` 转换为路径，并使用以前创建的对象绘制路径 `SKPaint` ：

```csharp
public class LinkedChainPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Black);

        // Width and height of catenary
        int width = info.Width;
        float height = info.Height - linkRadius;

        // Find the optimum 'a' for this width and height
        float optA = FindOptimumA(width, height);

        // Calculate the vertical offset for that value of 'a'
        float yOffset = catenary(optA, -width / 2);

        // Create a path for the catenary
        SKPoint[] points = new SKPoint[width];

        for (int x = 0; x < width; x++)
        {
            points[x] = new SKPoint(x, yOffset - catenary(optA, x - width / 2));
        }

        using (SKPath path = new SKPath())
        {
            path.AddPoly(points, false);

            // And render that path with the linksPaint object
            canvas.DrawPath(path, linksPaint);
        }
    }
    ...
}
```

此程序定义中使用的路径 `Create1DPath` ，使其在中心的 (0，0) 点。 这似乎是合理的，因为路径 (0，0) 点与它装饰的线条或曲线对齐。 不过，您可以使用非中心 (0，0) 点来实现某些特殊效果。

**传送带**会创建一个路径，该路径类似于椭圆形传送带，其上凸和下凸，大小为窗口的尺寸。 该路径以一个简单的 `SKPaint` 对象为20个像素宽和灰色着色，然后再次使用对象（ `SKPaint` 该对象 `SKPathEffect` 引用类似于小 bucket 的路径）对另一个对象进行描边：

[![传送带页面的三向屏幕截图](effects-images/conveyorbelt-small.png)](effects-images/conveyorbelt-large.png#lightbox)

Bucket 路径 (0，0) 点是句柄，因此，在对 `phase` 参数进行动画处理时，存储桶看起来像是围绕传送带旋转，可能 scooping 在底部，并将其转储到顶部。

[`ConveyorBeltPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConveyorBeltPage.cs)类实现的动画具有和方法的重写 `OnAppearing` `OnDisappearing` 。 Bucket 的路径在页的构造函数中定义：

```csharp
public class ConveyorBeltPage : ContentPage
{
    SKCanvasView canvasView;
    bool pageIsActive = false;

    SKPaint conveyerPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 20,
        Color = SKColors.DarkGray
    };

    SKPath bucketPath = new SKPath();

    SKPaint bucketsPaint = new SKPaint
    {
        Color = SKColors.BurlyWood,
    };

    public ConveyorBeltPage()
    {
        Title = "Conveyor Belt";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Create the path for the bucket starting with the handle
        bucketPath.AddRect(new SKRect(-5, -3, 25, 3));

        // Sides
        bucketPath.AddRoundedRect(new SKRect(25, -19, 27, 18), 10, 10,
                                  SKPathDirection.CounterClockwise);
        bucketPath.AddRoundedRect(new SKRect(63, -19, 65, 18), 10, 10,
                                  SKPathDirection.CounterClockwise);

        // Five slats
        for (int i = 0; i < 5; i++)
        {
            bucketPath.MoveTo(25, -19 + 8 * i);
            bucketPath.LineTo(25, -13 + 8 * i);
            bucketPath.ArcTo(50, 50, 0, SKPathArcSize.Small,
                             SKPathDirection.CounterClockwise, 65, -13 + 8 * i);
            bucketPath.LineTo(65, -19 + 8 * i);
            bucketPath.ArcTo(50, 50, 0, SKPathArcSize.Small,
                             SKPathDirection.Clockwise, 25, -19 + 8 * i);
            bucketPath.Close();
        }

        // Arc to suggest the hidden side
        bucketPath.MoveTo(25, -17);
        bucketPath.ArcTo(50, 50, 0, SKPathArcSize.Small,
                         SKPathDirection.Clockwise, 65, -17);
        bucketPath.LineTo(65, -19);
        bucketPath.ArcTo(50, 50, 0, SKPathArcSize.Small,
                         SKPathDirection.CounterClockwise, 25, -19);
        bucketPath.Close();

        // Make it a little bigger and correct the orientation
        bucketPath.Transform(SKMatrix.MakeScale(-2, 2));
        bucketPath.Transform(SKMatrix.MakeRotationDegrees(90));
    }
    ...
```

Bucket 创建代码完成了两个变换，使 bucket 变大，使其侧向旋转。 应用这些转换比调整前面代码中的所有坐标更容易。

`PaintSurface`处理程序首先定义传送带的路径。 这只是一对线条和一对用20个像素宽暗灰色线条绘制的半圆圈：

```csharp
public class ConveyorBeltPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        float width = info.Width / 3;
        float verticalMargin = width / 2 + 150;

        using (SKPath conveyerPath = new SKPath())
        {
            // Straight verticals capped by semicircles on top and bottom
            conveyerPath.MoveTo(width, verticalMargin);
            conveyerPath.ArcTo(width / 2, width / 2, 0, SKPathArcSize.Large,
                               SKPathDirection.Clockwise, 2 * width, verticalMargin);
            conveyerPath.LineTo(2 * width, info.Height - verticalMargin);
            conveyerPath.ArcTo(width / 2, width / 2, 0, SKPathArcSize.Large,
                               SKPathDirection.Clockwise, width, info.Height - verticalMargin);
            conveyerPath.Close();

            // Draw the conveyor belt itself
            canvas.DrawPath(conveyerPath, conveyerPaint);

            // Calculate spacing based on length of conveyer path
            float length = 2 * (info.Height - 2 * verticalMargin) +
                           2 * ((float)Math.PI * width / 2);

            // Value will be somewhere around 200
            float spacing = length / (float)Math.Round(length / 200);

            // Now animate the phase; t is 0 to 1 every 2 seconds
            TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
            float t = (float)(timeSpan.TotalSeconds % 2 / 2);
            float phase = -t * spacing;

            // Create the buckets PathEffect
            using (SKPathEffect bucketsPathEffect =
                        SKPathEffect.Create1DPath(bucketPath, spacing, phase,
                                                  SKPath1DPathEffectStyle.Rotate))
            {
                // Set it to the Paint object and draw the path again
                bucketsPaint.PathEffect = bucketsPathEffect;
                canvas.DrawPath(conveyerPath, bucketsPaint);
            }
        }
    }
}
```

用于绘制传送带的逻辑在横向模式下不起作用。

在传送带上，bucket 的间距应约为200像素。 但是，传送带的长度可能不是200像素的倍数，这意味着，作为的 `phase` 自变量 `SKPathEffect.Create1DPath` 进行动画处理时，存储桶将弹出和退出存在。

出于此原因，程序首先计算一个名为 `length` 的值，它是传送带的长度。 因为传送带包含直线和半圆，所以这是一个简单的计算。 接下来，使用除以200计算存储桶的数目 `length` 。 此值舍入为最接近的整数，然后将该数值分成 `length` 。 结果是整数 bucket 的间距。 `phase`参数只是的一小部分。

## <a name="from-path-to-path-again"></a>再次从路径到路径

在 `DrawSurface` **传送带**中处理程序的底部，注释掉 `canvas.DrawPath` 调用并将其替换为以下代码：

```csharp
SKPath newPath = new SKPath();
bool fill = bucketsPaint.GetFillPath(conveyerPath, newPath);
SKPaint newPaint = new SKPaint
{
    Style = fill ? SKPaintStyle.Fill : SKPaintStyle.Stroke
};
canvas.DrawPath(newPath, newPaint);
```

与前面的示例一样 `GetFillPath` ，你会看到除颜色之外的结果是相同的。 执行后 `GetFillPath` ，该 `newPath` 对象包含存储桶路径的多个副本，每个副本都位于动画将其置于调用时的相同位置。

## <a name="hatching-an-area"></a>为面积影线

[`SKPathEffect.Create2DLines`](xref:SkiaSharp.SKPathEffect.Create2DLine(System.Single,SkiaSharp.SKMatrix))方法使用平行线填充区域，通常称为*阴影线*。 方法具有以下语法：

```csharp
public static SKPathEffect Create2DLine (Single width, SKMatrix matrix)
```

`width`参数指定阴影线的笔划宽度。 `matrix`参数是缩放和可选旋转的组合。 缩放系数指示 Skia 用来对阴影线进行间距调整的像素增量。 线条之间的分隔是缩放系数减去 `width` 自变量。 如果缩放系数小于或等于 `width` 值，阴影线之间将不会有空格，并且区域将显示为已填充。 为水平和垂直缩放指定相同的值。

默认情况下，阴影线是水平的。 如果 `matrix` 参数包含旋转，则为阴影线顺时针旋转。

" **阴影填充** " 页演示了此路径效果。 [`HatchFillPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/HatchFillPage.cs)类将三个路径效果定义为字段，将第一个用于宽度为3个像素、比例因子为3个像素的水平影线线，指示它们之间相隔6个像素的间距。 因此，各行之间的分离是三个像素。 第二个路径效果适用于宽度为六个像素且间距为24个像素、 (因此分隔) 为18个像素的垂直阴影线，第三个路径效果用于相距为36像素的对角影线线条。

```csharp
public class HatchFillPage : ContentPage
{
    SKPaint fillPaint = new SKPaint();

    SKPathEffect horzLinesPath = SKPathEffect.Create2DLine(3, SKMatrix.MakeScale(6, 6));

    SKPathEffect vertLinesPath = SKPathEffect.Create2DLine(6,
        Multiply(SKMatrix.MakeRotationDegrees(90), SKMatrix.MakeScale(24, 24)));

    SKPathEffect diagLinesPath = SKPathEffect.Create2DLine(12,
        Multiply(SKMatrix.MakeScale(36, 36), SKMatrix.MakeRotationDegrees(45)));

    SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 3,
        Color = SKColors.Black
    };
    ...
    static SKMatrix Multiply(SKMatrix first, SKMatrix second)
    {
        SKMatrix target = SKMatrix.MakeIdentity();
        SKMatrix.Concat(ref target, first, second);
        return target;
    }
}
```

请注意 matrix `Multiply` 方法。 由于水平和垂直比例因子相同，因此缩放和旋转矩阵的乘法顺序并不重要。

`PaintSurface`处理程序将这三个具有三种不同颜色的路径效果结合起来使用 `fillPaint` ，以填充经过调整大小以适合页面的圆角矩形。 `Style`忽略设置的属性 `fillPaint` ; 当 `SKPaint` 对象包括从创建的路径效果时，将 `SKPathEffect.Create2DLine` 填充区域，而不考虑：

```csharp
public class HatchFillPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath roundRectPath = new SKPath())
        {
            // Create a path
            roundRectPath.AddRoundedRect(
                new SKRect(50, 50, info.Width - 50, info.Height - 50), 100, 100);

            // Horizontal hatch marks
            fillPaint.PathEffect = horzLinesPath;
            fillPaint.Color = SKColors.Red;
            canvas.DrawPath(roundRectPath, fillPaint);

            // Vertical hatch marks
            fillPaint.PathEffect = vertLinesPath;
            fillPaint.Color = SKColors.Blue;
            canvas.DrawPath(roundRectPath, fillPaint);

            // Diagonal hatch marks -- use clipping
            fillPaint.PathEffect = diagLinesPath;
            fillPaint.Color = SKColors.Green;

            canvas.Save();
            canvas.ClipPath(roundRectPath);
            canvas.DrawRect(new SKRect(0, 0, info.Width, info.Height), fillPaint);
            canvas.Restore();

            // Outline the path
            canvas.DrawPath(roundRectPath, strokePaint);
        }
    }
    ...
}
```

如果你仔细查看结果，你会看到红色和蓝色阴影线不会精确地限制为圆角矩形。  (这显然是基本 Skia 代码的特征。 ) 如果不满意，则会为绿色对角阴影线显示一种替代方法：圆角矩形用作剪切路径，阴影线在整个页面上绘制。

`PaintSurface`处理程序会通过调用来结束，只需对圆角矩形进行笔划，这样您就可以看到红和蓝影线线条的差异：

[!["阴影填充" 页的三向屏幕截图](effects-images/hatchfill-small.png)](effects-images/hatchfill-large.png#lightbox)

Android 屏幕并不真正类似于：屏幕截图的缩放导致了细的红线和细的空格，以将其合并到看似更宽的红线和更宽的空间。

## <a name="filling-with-a-path"></a>使用路径填充

[`SKPathEffect.Create2DPath`](xref:SkiaSharp.SKPathEffect.Create2DPath(SkiaSharp.SKMatrix,SkiaSharp.SKPath))利用，您可以使用水平和垂直复制的路径填充区域，这会使区域平铺：

```csharp
public static SKPathEffect Create2DPath (SKMatrix matrix, SKPath path)
```

`SKMatrix`缩放系数指示复制路径的水平和垂直间距。 但不能使用此参数来旋转路径 `matrix` ; 如果要旋转路径，则使用 `Transform` 由定义的方法来旋转路径本身 `SKPath` 。

复制的路径通常与屏幕的左边缘和上边缘对齐，而不是与要填充的区域对齐。 您可以通过提供0和比例因子之间的转换因子来重写此行为，以指定从左到右的水平和垂直偏移量。

**路径磁贴填充**页演示了此路径效果。 用于平铺区域的路径定义为类中的字段 [`PathTileFillPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathTileFillPage.cs) 。 水平和垂直坐标范围为–40到40，这表示此路径为80像素正方形：

```csharp
public class PathTileFillPage : ContentPage
{
    SKPath tilePath = SKPath.ParseSvgPathData(
        "M -20 -20 L 2 -20, 2 -40, 18 -40, 18 -20, 40 -20, " +
        "40 -12, 20 -12, 20 12, 40 12, 40 40, 22 40, 22 20, " +
        "-2 20, -2 40, -20 40, -20 8, -40 8, -40 -8, -20 -8 Z");
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            paint.Color = SKColors.Red;

            using (SKPathEffect pathEffect =
                   SKPathEffect.Create2DPath(SKMatrix.MakeScale(64, 64), tilePath))
            {
                paint.PathEffect = pathEffect;

                canvas.DrawRoundRect(
                    new SKRect(50, 50, info.Width - 50, info.Height - 50),
                    100, 100, paint);
            }
        }
    }
}
```

在 `PaintSurface` 处理程序中， `SKPathEffect.Create2DPath` 调用将水平间距和垂直间距设置为64，以使80像素的方块重叠。 幸运的是，该路径类似于谜题，结合与相邻磁贴非常相似：

[![路径磁贴填充页的三向屏幕截图](effects-images/pathtilefill-small.png)](effects-images/pathtilefill-large.png#lightbox)

从原始屏幕快照进行缩放会导致某种变形，特别是在 Android 屏幕上。

请注意，这些磁贴始终显示为整体，并且永远不会被截断。 在前两个屏幕截图上，很明显，要填充的区域是一个圆角矩形。 如果要将这些磁贴截断到特定区域，请使用剪切路径。

尝试将 `Style` 对象的属性设置 `SKPaint` 为 `Stroke` ，您将看到各个磁贴已列出，而不是填充。

还可以使用平铺位图填充区域，如 [**SkiaSharp 位图平铺**](../effects/shaders/bitmap-tiling.md)一文中所示。

## <a name="rounding-sharp-corners"></a>圆角尖角

这三种方法中提供了 **圆角 Heptagon** 程序，用于 [**绘制弧形文章，这三种方法**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md) 使用相切圆弧曲线绘制出七个点的点。 **另一个圆形 Heptagon**页面显示了一种更简单的方法，该方法使用从方法创建的路径效果 [`SKPathEffect.CreateCorner`](xref:SkiaSharp.SKPathEffect.CreateCorner(System.Single)) ：

```csharp
public static SKPathEffect CreateCorner (Single radius)
```

虽然单个参数名为 `radius` ，但必须将其设置为所需的角半径一半。  (这是基础 Skia 代码的特征。 ) 

下面是 `PaintSurface` 类中的处理程序 [`AnotherRoundedHeptagonPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/AnotherRoundedHeptagonPage.cs) ：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    int numVertices = 7;
    float radius = 0.45f * Math.Min(info.Width, info.Height);
    SKPoint[] vertices = new SKPoint[numVertices];
    double vertexAngle = -0.5f * Math.PI;       // straight up

    // Coordinates of the vertices of the polygon
    for (int vertex = 0; vertex < numVertices; vertex++)
    {
        vertices[vertex] = new SKPoint(radius * (float)Math.Cos(vertexAngle),
                                       radius * (float)Math.Sin(vertexAngle));
        vertexAngle += 2 * Math.PI / numVertices;
    }

    float cornerRadius = 100;

    // Create the path
    using (SKPath path = new SKPath())
    {
        path.AddPoly(vertices, true);

        // Render the path in the center of the screen
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Blue;
            paint.StrokeWidth = 10;

            // Set argument to half the desired corner radius!
            paint.PathEffect = SKPathEffect.CreateCorner(cornerRadius / 2);

            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.DrawPath(path, paint);

            // Uncomment DrawCircle call to verify corner radius
            float offset = cornerRadius / (float)Math.Sin(Math.PI * (numVertices - 2) / numVertices / 2);
            paint.Color = SKColors.Green;
            // canvas.DrawCircle(vertices[0].X, vertices[0].Y + offset, cornerRadius, paint);
        }
    }
}
```

您可以将此效果用于描边，或根据对象的 `Style` 属性进行填充 `SKPaint` 。 此处运行的是：

[![另一个圆形 Heptagon 页面的三向屏幕截图](effects-images/anotherroundedheptagon-small.png)](effects-images/anotherroundedheptagon-large.png#lightbox)

你会看到此舍入 heptagon 与之前的程序相同。 如果需要更好地说服角半径确实为100，而不是调用中指定的 50 `SKPathEffect.CreateCorner` ，则可以取消注释程序中的最后一个语句，并查看叠加在角上的100半径圆圈。

## <a name="random-jitter"></a>随机抖动

有时，计算机图形的完美直行并不是您想要的，并且需要一些随机性。 在这种情况下，需要尝试 [`SKPathEffect.CreateDiscrete`](xref:SkiaSharp.SKPathEffect.CreateDiscrete(System.Single,System.Single,System.UInt32)) 方法：

```csharp
public static SKPathEffect CreateDiscrete (Single segLength, Single deviation, UInt32 seedAssist)
```

可以将此路径效果用于描边或填充。 行分为连接段（由指定的大致长度 `segLength` ），并按不同方向扩展。 与原始行的偏差的范围由指定 `deviation` 。

最终参数是用于生成用于效果的伪随机序列的种子。 对于不同的种子，抖动效果看起来会稍有不同。 自变量的默认值为零，这意味着每次运行程序时，效果都是相同的。 如果在重画屏幕时需要不同的抖动，可以将种子设置为值的 `Millisecond` 属性 `DataTime.Now` (例如) 。

通过 " **抖动实验** " 页，您可以在一个矩形的描边内尝试不同的值：

[![JitterExperiment 页面的三向屏幕截图](effects-images/jitterexperiment-small.png)](effects-images/jitterexperiment-large.png#lightbox)

程序非常简单。 [**JitterExperimentPage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterExperimentPage.xaml)文件实例化两个 `Slider` 元素和一个 `SKCanvasView` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Curves.JitterExperimentPage"
             Title="Jitter Experiment">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Grid.Resources>
            <ResourceDictionary>
                <Style TargetType="Label">
                    <Setter Property="HorizontalTextAlignment" Value="Center" />
                </Style>

                <Style TargetType="Slider">
                    <Setter Property="Margin" Value="20, 0" />
                    <Setter Property="Minimum" Value="0" />
                    <Setter Property="Maximum" Value="100" />
                </Style>
            </ResourceDictionary>
        </Grid.Resources>

        <Slider x:Name="segLengthSlider"
                Grid.Row="0"
                ValueChanged="sliderValueChanged" />

        <Label Text="{Binding Source={x:Reference segLengthSlider},
                              Path=Value,
                              StringFormat='Segment Length = {0:F0}'}"
               Grid.Row="1" />

        <Slider x:Name="deviationSlider"
                Grid.Row="2"
                ValueChanged="sliderValueChanged" />

        <Label Text="{Binding Source={x:Reference deviationSlider},
                              Path=Value,
                              StringFormat='Deviation = {0:F0}'}"
               Grid.Row="3" />

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="4"
                           PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

`PaintSurface`只要值发生更改，就会调用[**JitterExperimentPage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterExperimentPage.xaml.cs)代码隐藏文件中的处理程序 `Slider` 。 它 `SKPathEffect.CreateDiscrete` 使用两个值调用， `Slider` 并使用它来对矩形进行笔画操作：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float segLength = (float)segLengthSlider.Value;
    float deviation = (float)deviationSlider.Value;

    using (SKPaint paint = new SKPaint())
    {
        paint.Style = SKPaintStyle.Stroke;
        paint.StrokeWidth = 5;
        paint.Color = SKColors.Blue;

        using (SKPathEffect pathEffect = SKPathEffect.CreateDiscrete(segLength, deviation))
        {
            paint.PathEffect = pathEffect;

            SKRect rect = new SKRect(100, 100, info.Width - 100, info.Height - 100);
            canvas.DrawRect(rect, paint);
        }
    }
}
```

您也可以使用此效果进行填充，在这种情况下，填充区域的轮廓服从这些随机偏差。 " **抖动文本** " 页演示了如何使用此路径效果来显示文本。 类的处理程序中的大部分代码 `PaintSurface` [`JitterTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterTextPage.cs) 专用于调整文本的大小和居中：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    string text = "FUZZY";

    using (SKPaint textPaint = new SKPaint())
    {
        textPaint.Color = SKColors.Purple;
        textPaint.PathEffect = SKPathEffect.CreateDiscrete(3f, 10f);

        // Adjust TextSize property so text is 95% of screen width
        float textWidth = textPaint.MeasureText(text);
        textPaint.TextSize *= 0.95f * info.Width / textWidth;

        // Find the text bounds
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(text, ref textBounds);

        // Calculate offsets to center the text on the screen
        float xText = info.Width / 2 - textBounds.MidX;
        float yText = info.Height / 2 - textBounds.MidY;

        canvas.DrawText(text, xText, yText, textPaint);
    }
}
```

它在横向模式下运行：

[![JitterText 页面的三向屏幕截图](effects-images/jittertext-small.png)](effects-images/jittertext-large.png#lightbox)

## <a name="path-outlining"></a>路径大纲显示

您已经看到了的两个方法示例 [`GetFillPath`](xref:SkiaSharp.SKPaint.GetFillPath*) `SKPaint` ，其中有两个版本：

```csharp
public Boolean GetFillPath (SKPath src, SKPath dst, Single resScale = 1)

public Boolean GetFillPath (SKPath src, SKPath dst, SKRect cullRect, Single resScale = 1)
```

只有前两个参数是必需的。 方法访问参数所引用的路径 `src` ，根据对象中的笔划属性修改路径数据 `SKPaint` (包括) 的属性，然后将 `PathEffect` 结果写入路径中 `dst` 。 `resScale`参数允许减少精度以创建较小的目标路径，并且 `cullRect` 参数可以消除矩形外的轮廓。

此方法的一个基本用途根本不涉及路径效果：如果 `SKPaint` 对象的 `Style` 属性设置为 `SKPaintStyle.Stroke` ，并且 *没有* 其 `PathEffect` 设置，则会 `GetFillPath` 创建一个路径，该路径表示源路径的 *轮廓* ，就像它已被绘制属性描边一样。

例如，如果该 `src` 路径是一个简单的半径为500的圆，而该 `SKPaint` 对象指定的笔划宽度为100，则该 `dst` 路径将变成两个同心圆，一个半径为450，另一个半径为550。 调用方法是 `GetFillPath` 因为填充此路径与为 `dst` 路径描边的路径相同 `src` 。 但您也可以通过笔划 `dst` 路径来查看路径轮廓。

**点击以对路径进行分级**演示。 `SKCanvasView`和 `TapGestureRecognizer` 在[**TapToOutlineThePathPage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TapToOutlineThePathPage.xaml)文件中实例化。 [**TapToOutlineThePathPage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TapToOutlineThePathPage.xaml.cs)代码隐藏文件将三个 `SKPaint` 对象定义为字段，两个用于描边宽度为100和20的描边，第三个对象用于填充：

```csharp
public partial class TapToOutlineThePathPage : ContentPage
{
    bool outlineThePath = false;

    SKPaint redThickStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 100
    };

    SKPaint redThinStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 20
    };

    SKPaint blueFill = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue
    };

    public TapToOutlineThePathPage()
    {
        InitializeComponent();
    }

    void OnCanvasViewTapped(object sender, EventArgs args)
    {
        outlineThePath ^= true;
        (sender as SKCanvasView).InvalidateSurface();
    }
    ...
}
```

如果未点击屏幕， `PaintSurface` 处理程序将使用 `blueFill` 和 `redThickStroke` paint 对象呈现循环路径：

```csharp
public partial class TapToOutlineThePathPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath circlePath = new SKPath())
        {
            circlePath.AddCircle(info.Width / 2, info.Height / 2,
                                 Math.Min(info.Width / 2, info.Height / 2) -
                                 redThickStroke.StrokeWidth);

            if (!outlineThePath)
            {
                canvas.DrawPath(circlePath, blueFill);
                canvas.DrawPath(circlePath, redThickStroke);
            }
            else
            {
                using (SKPath outlinePath = new SKPath())
                {
                    redThickStroke.GetFillPath(circlePath, outlinePath);

                    canvas.DrawPath(outlinePath, blueFill);
                    canvas.DrawPath(outlinePath, redThinStroke);
                }
            }
        }
    }
}
```

该圆形会按预期方式填充和描边：

[![正常点击以概述路径页面的三向屏幕截图](effects-images/taptooutlinethepathnormal-small.png)](effects-images/taptooutlinethepathnormal-large.png#lightbox)

点击屏幕时，将 `outlineThePath` 设置为 `true` ，并且 `PaintSurface` 处理程序将创建一个新的 `SKPath` 对象，并将其用作对 `GetFillPath` paint 对象的调用中的目标路径 `redThickStroke` 。 然后，将填充该目标路径并将其与相交，这会 `redThinStroke` 导致以下结果：

[![边框点击以概述 "路径" 页的三向屏幕截图](effects-images/taptooutlinethepathoutlined-small.png)](effects-images/taptooutlinethepathoutlined-large.png#lightbox)

这两个红色圆圈清楚地表明原循环路径已转换为两个圆形等高线。

此方法在开发用于方法的路径时非常有用 `SKPathEffect.Create1DPath` 。 在这些方法中指定的路径在复制路径时始终会被填充。 如果您不希望填充整个路径，则必须仔细定义轮廓。

例如，在 **链接链** 示例中，链接是使用一系列四个弧定义的，每对都基于两个半径来勾勒要填充的路径区域。 可以将类中的代码替换为 `LinkedChainPage` 略有不同。

首先，需要重新定义 `linkRadius` 常量：

```csharp
const float linkRadius = 27.5f;
const float linkThickness = 5;
```

`linkPath`现在，只有两个基于该半径的弧形，具有所需的起始角度和扫描角度：

```csharp
using (SKPath linkPath = new SKPath())
{
    SKRect rect = new SKRect(-linkRadius, -linkRadius, linkRadius, linkRadius);
    linkPath.AddArc(rect, 55, 160);
    linkPath.AddArc(rect, 235, 160);

    using (SKPaint strokePaint = new SKPaint())
    {
        strokePaint.Style = SKPaintStyle.Stroke;
        strokePaint.StrokeWidth = linkThickness;

        using (SKPath outlinePath = new SKPath())
        {
            strokePaint.GetFillPath(linkPath, outlinePath);

            // Set that path as the 1D path effect for linksPaint
            linksPaint.PathEffect =
                SKPathEffect.Create1DPath(outlinePath, 1.3f * linkRadius, 0,
                                          SKPath1DPathEffectStyle.Rotate);

        }

    }
}
```

`outlinePath`对象在 `linkPath` 与中指定的属性进行描边时，为大纲的接收方 `strokePaint` 。

使用此方法的另一个示例是在方法中使用的路径的下一个示例。

## <a name="combining-path-effects"></a>组合路径效果

的两个最终静态创建方法 `SKPathEffect` 是 [`SKPathEffect.CreateSum`](xref:SkiaSharp.SKPathEffect.CreateSum(SkiaSharp.SKPathEffect,SkiaSharp.SKPathEffect)) 和 [`SKPathEffect.CreateCompose`](xref:SkiaSharp.SKPathEffect.CreateCompose(SkiaSharp.SKPathEffect,SkiaSharp.SKPathEffect)) ：

```csharp
public static SKPathEffect CreateSum (SKPathEffect first, SKPathEffect second)

public static SKPathEffect CreateCompose (SKPathEffect outer, SKPathEffect inner)
```

这两种方法组合了两个路径效果来创建复合路径效果。 `CreateSum`方法创建的路径效果类似于分别应用的两个路径效果，而 `CreateCompose` () 应用一个路径效果， `inner` 然后将应用 `outer` 到该效果。

你已经了解了的 `GetFillPath` 方法如何 `SKPaint` 根据 (包括) 的属性将一个路径转换为另一个路径，因此， `SKPaint` `PathEffect` 不应 *太* 神秘 `SKPaint` 对象如何使用或方法中指定的两个路径效果两次执行该操作 `CreateSum` `CreateCompose` 。

的一个明显用途 `CreateSum` 是定义一个 `SKPaint` 对象，该对象使用一个路径效果填充路径，并使用另一个路径效果来描边路径。 " **帧中的猫** " 示例演示了这种情况，它在具有相框边缘的帧中显示猫数组：

[![框架页中猫的三向屏幕截图](effects-images/catsinframe-small.png)](effects-images/catsinframe-large.png#lightbox)

[`CatsInFramePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CatsInFramePage.cs)类首先定义多个字段。 您可以 [`PathDataCatPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataCatPage.cs) 通过 [**SVG 路径数据**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md) 一文中的类来识别第一个字段。 第二个路径基于帧的 scallop 模式的直线和弧线：

```csharp
public class CatsInFramePage : ContentPage
{
    // From PathDataCatPage.cs
    SKPath catPath = SKPath.ParseSvgPathData(
        "M 160 140 L 150 50 220 103" +              // Left ear
        "M 320 140 L 330 50 260 103" +              // Right ear
        "M 215 230 L 40 200" +                      // Left whiskers
        "M 215 240 L 40 240" +
        "M 215 250 L 40 280" +
        "M 265 230 L 440 200" +                     // Right whiskers
        "M 265 240 L 440 240" +
        "M 265 250 L 440 280" +
        "M 240 100" +                               // Head
        "A 100 100 0 0 1 240 300" +
        "A 100 100 0 0 1 240 100 Z" +
        "M 180 170" +                               // Left eye
        "A 40 40 0 0 1 220 170" +
        "A 40 40 0 0 1 180 170 Z" +
        "M 300 170" +                               // Right eye
        "A 40 40 0 0 1 260 170" +
        "A 40 40 0 0 1 300 170 Z");

    SKPaint catStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 5
    };

    SKPath scallopPath =
        SKPath.ParseSvgPathData("M 0 0 L 50 0 A 60 60 0 0 1 -50 0 Z");

    SKPaint framePaint = new SKPaint
    {
        Color = SKColors.Black
    };
    ...
}
```

`catPath` `SKPathEffect.Create2DPath` 如果 `SKPaint` 对象 `Style` 属性设置为，则可在方法中使用 `Stroke` 。 但是，如果 `catPath` 直接在此程序中使用，则将填充 cat 的整个 head，并且须线甚至不可见。  (试试！ ) 需要获取该路径的轮廓并在方法中使用该轮廓 `SKPathEffect.Create2DPath` 。

构造函数执行此作业。 第一种方法是将两个转换应用到， `catPath` 将 (0，0) 点向中心移动，并将其缩小。 `GetFillPath` 获取中的轮廓的所有轮廓 `outlinedCatPath` ，并在调用中使用该对象 `SKPathEffect.Create2DPath` 。 值中的缩放系数 `SKMatrix` 略微大于 cat 的水平和垂直大小，以在图块之间提供小的缓冲，而平移因子则衍生为一些凭经验，以便完全猫在帧的左上角显示：

```csharp
public class CatsInFramePage : ContentPage
{
    ...
    public CatsInFramePage()
    {
        Title = "Cats in Frame";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Move (0, 0) point to center of cat path
        catPath.Transform(SKMatrix.MakeTranslation(-240, -175));

        // Now catPath is 400 by 250
        // Scale it down to 160 by 100
        catPath.Transform(SKMatrix.MakeScale(0.40f, 0.40f));

        // Get the outlines of the contours of the cat path
        SKPath outlinedCatPath = new SKPath();
        catStroke.GetFillPath(catPath, outlinedCatPath);

        // Create a 2D path effect from those outlines
        SKPathEffect fillEffect = SKPathEffect.Create2DPath(
            new SKMatrix { ScaleX = 170, ScaleY = 110,
                           TransX = 75, TransY = 80,
                           Persp2 = 1 },
            outlinedCatPath);

        // Create a 1D path effect from the scallop path
        SKPathEffect strokeEffect =
            SKPathEffect.Create1DPath(scallopPath, 75, 0, SKPath1DPathEffectStyle.Rotate);

        // Set the sum the effects to frame paint
        framePaint.PathEffect = SKPathEffect.CreateSum(fillEffect, strokeEffect);
    }
    ...
}
```

然后，构造函数调用 `SKPathEffect.Create1DPath` 相框帧。 请注意，该路径的宽度为100像素，但前进量为75像素，以便复制的路径与帧重叠。 构造函数的最后一条语句调用 `SKPathEffect.CreateSum` 来合并这两个路径效果，并将结果设置为 `SKPaint` 对象。

所有这些工作都允许 `PaintSurface` 处理程序非常简单。 它只需定义一个矩形并使用绘制它 `framePaint` ：

```csharp
public class CatsInFramePage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKRect rect = new SKRect(50, 50, info.Width - 50, info.Height - 50);
        canvas.ClipRect(rect);
        canvas.DrawRect(rect, framePaint);
    }
}
```

路径效果后面的算法始终导致显示用于描边或填充的整个路径，这可能会导致某些视觉对象出现在矩形外。 `ClipRect`调用之前的调用 `DrawRect` 允许视觉对象变得更加整洁。  (尝试不剪裁！ ) 

通常使用 `SKPathEffect.CreateCompose` 向另一个路径效果添加一些抖动。 当然，您可以自行试验，但下面的示例有点不同：

**虚线阴影线**使用变成虚线的影线线条填充椭圆。 类中的大部分工作 [`DashedHatchLinesPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DashedHatchLinesPage.cs) 均在字段定义中直接执行。 这些字段定义短划线效果和阴影效果。 它们定义为， `static` 因为随后会在定义的调用中引用它们 `SKPathEffect.CreateCompose` `SKPaint` ：

```csharp
public class DashedHatchLinesPage : ContentPage
{
    static SKPathEffect dashEffect =
        SKPathEffect.CreateDash(new float[] { 30, 30 }, 0);

    static SKPathEffect hatchEffect = SKPathEffect.Create2DLine(20,
        Multiply(SKMatrix.MakeScale(60, 60),
                 SKMatrix.MakeRotationDegrees(45)));

    SKPaint paint = new SKPaint()
    {
        PathEffect = SKPathEffect.CreateCompose(dashEffect, hatchEffect),
        StrokeCap = SKStrokeCap.Round,
        Color = SKColors.Blue
    };
    ...
    static SKMatrix Multiply(SKMatrix first, SKMatrix second)
    {
        SKMatrix target = SKMatrix.MakeIdentity();
        SKMatrix.Concat(ref target, first, second);
        return target;
    }
}
```

`PaintSurface`处理程序只需包含标准开销和一个对的调用 `DrawOval` ：

```csharp
public class DashedHatchLinesPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        canvas.DrawOval(info.Width / 2, info.Height / 2,
                        0.45f * info.Width, 0.45f * info.Height,
                        paint);
    }
    ...
}
```

正如您已发现的，阴影线并未精确地限制在区域的内部，在此示例中，它们始终以整破折号开头：

[![虚线影线线条页面的三向屏幕截图](effects-images/dashedhatchlines-small.png)](effects-images/dashedhatchlines-large.png#lightbox)

现在，您已经看到了从简单点和短划线到奇怪组合的路径效果，可以使用您的想像并了解您可以创建的内容。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)