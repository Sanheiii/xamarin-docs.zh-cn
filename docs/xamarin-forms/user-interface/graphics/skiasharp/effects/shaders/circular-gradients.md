---
title: SkiaSharp 循环渐变
description: 了解基于圆圈的不同类型的渐变。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 400AE23A-6A0B-4FA8-BD6B-DE4146B04732
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ec84ac906ac146f37ba5b161a898582ce483bc95
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556667"
---
# <a name="the-skiasharp-circular-gradients"></a>SkiaSharp 循环渐变

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

[`SKShader`](xref:SkiaSharp.SKShader)类定义静态方法以创建四种不同类型的渐变。 [**SkiaSharp 线性渐变**](linear-gradient.md)文章讨论了 [`CreateLinearGradient`](xref:SkiaSharp.SKShader.CreateLinearGradient*) 方法。 本文介绍了另外三种类型的渐变，它们都基于圆圈。

[`CreateRadialGradient`](xref:SkiaSharp.SKShader.CreateRadialGradient*)方法创建一个从圆的中心 emanates 的渐变：

![径向渐变示例](circular-gradients-images/RadialGradientSample.png)

[`CreateSweepGradient`](xref:SkiaSharp.SKShader.CreateSweepGradient*)方法创建一个围绕圆圈中心进行扫描的渐变：

![扫描渐变示例](circular-gradients-images/SweepGradientSample.png)

第三种类型的渐变非常罕见。 它被称为双点圆锥渐变，由 [`CreateTwoPointConicalGradient`](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient*) 方法定义。 渐变从一个圆圈延伸到另一个圆圈：

![圆锥渐变示例](circular-gradients-images/ConicalGradientSample.png)

如果这两个圆圈的大小不同，则渐变采用圆锥的形式。

本文更详细地探讨了这些渐变。

## <a name="the-radial-gradient"></a>径向渐变

[`CreateRadialGradient`](xref:SkiaSharp.SKShader.CreateRadialGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode))方法具有以下语法：

```csharp
public static SKShader CreateRadialGradient (SKPoint center, 
                                             Single radius, 
                                             SKColor[] colors, 
                                             Single[] colorPos, 
                                             SKShaderTileMode mode)
```

[`CreateRadialGradient`](xref:SkiaSharp.SKShader.CreateRadialGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix))重载还包含转换矩阵参数。

前两个自变量指定圆的中心和半径。 渐变从该中心开始，并向外扩展以获取 `radius` 像素。 发生的情况 `radius` 取决于 [`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode) 参数。 `colors`参数是一个由两个或多个颜色组成的数组， (与线性渐变方法) 的一样， `colorPos` 是介于0到1范围内的整数数组。 这些整数表示颜色沿线条的相对位置 `radius` 。 您可以将该参数设置为， `null` 以便对颜色均匀地设置间距。

如果使用 `CreateRadialGradient` 填充圆，则可以将渐变中心设置为圆的中心，将渐变的半径设置为圆的半径。 在这种情况下，该 `SKShaderTileMode` 参数对渐变的呈现不起作用。 但如果渐变填充的区域大于渐变定义的圆，则 `SKShaderTileMode` 参数对圆外发生的情况有深远的影响。

的效果在 `SKShaderMode` [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)示例中的**径向渐变**页中进行了演示。 此页的 XAML 文件将实例化 `Picker` ，使您可以选择枚举中的三个成员之一 `SKShaderTileMode` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.RadialGradientPage"
             Title="Radial Gradient">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <skiaforms:SKCanvasView x:Name="canvasView"
                                Grid.Row="0"
                                PaintSurface="OnCanvasViewPaintSurface" />

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
</ContentPage>
```

代码隐藏文件将整个画布的颜色与径向渐变颜色一起使用。 渐变中心设置为画布的中心，半径设置为100像素。 渐变只包含两种颜色：黑色和白色：

```csharp
public partial class RadialGradientPage : ContentPage
{
    public RadialGradientPage ()
    {
        InitializeComponent ();
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

        SKShaderTileMode tileMode =
            (SKShaderTileMode)(tileModePicker.SelectedIndex == -1 ?
                                        0 : tileModePicker.SelectedItem);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateRadialGradient(
                                new SKPoint(info.Rect.MidX, info.Rect.MidY),
                                100,
                                new SKColor[] { SKColors.Black, SKColors.White },
                                null,
                                tileMode);

            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

此代码在中心创建一个带有黑色的渐变，并从中心逐渐淡化为白色的100像素。 超出 radius 的情况取决于 `SKShaderTileMode` 参数：

[![径向渐变](circular-gradients-images/RadialGradient.png "径向渐变")](circular-gradients-images/RadialGradient-Large.png#lightbox)

在所有三种情况下，渐变都会填充画布。 在左侧的 "iOS" 屏幕上，超过半径的渐变将继续，最后一种颜色为白色。 这就是的结果 `SKShaderTileMode.Clamp` 。 Android 屏幕显示 `SKShaderTileMode.Repeat` ：从中心开始100像素的效果，渐变将再次开始，第一种颜色为黑色。 渐变每100像素重复一次。 

右侧的 "通用 Windows 平台屏幕显示了如何 `SKShaderTileMode.Mirror` 使渐变成为备用方向。 第一种渐变是从中心以100像素的半径从中心到白色。 接下来是从100像素的半径到黑色，200像素的半径，下一次渐变再次反转。

可以在径向渐变中使用两种以上的颜色。 **彩虹 Arc 渐变**示例创建了8种颜色的数组，这些颜色对应于彩虹的颜色，以红色结束，还创建了一个包含八个位置值的数组：

```csharp
public class RainbowArcGradientPage : ContentPage
{
    public RainbowArcGradientPage ()
    {
        Title = "Rainbow Arc Gradient";

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
            float rainbowWidth = Math.Min(info.Width, info.Height) / 4f;

            // Center of arc and gradient is lower-right corner
            SKPoint center = new SKPoint(info.Width, info.Height);

            // Find outer, inner, and middle radius
            float outerRadius = Math.Min(info.Width, info.Height);
            float innerRadius = outerRadius - rainbowWidth;
            float radius = outerRadius - rainbowWidth / 2;

            // Calculate the colors and positions
            SKColor[] colors = new SKColor[8];
            float[] positions = new float[8];

            for (int i = 0; i < colors.Length; i++)
            {
                colors[i] = SKColor.FromHsl(i * 360f / 7, 100, 50);
                positions[i] = (i + (7f - i) * innerRadius / outerRadius) / 7f;
            }

            // Create sweep gradient based on center and outer radius
            paint.Shader = SKShader.CreateRadialGradient(center, 
                                                         outerRadius, 
                                                         colors, 
                                                         positions, 
                                                         SKShaderTileMode.Clamp);
            // Draw a circle with a wide line
            paint.Style = SKPaintStyle.Stroke;
            paint.StrokeWidth = rainbowWidth;

            canvas.DrawCircle(center, radius, paint);
        }
    }
}
```

假设画布的宽度和高度的最小值为1000，这意味着 `rainbowWidth` 值为250。 `outerRadius`和 `innerRadius` 值分别设置为1000和750。 这些值用于计算 `positions` 数组; 8 个值介于 0.75 f 到1之间。 `radius`该值用于为圆形描边。 如果值为875，则表示250像素的笔划宽度在750像素半径和1000像素半径之间扩展：

[![彩虹弧线渐变](circular-gradients-images/RainbowArcGradient.png "彩虹弧线渐变")](circular-gradients-images/RainbowArcGradient-Large.png#lightbox)

如果用此渐变填充了整个画布，则会看到它在内半径内为红色。 这是因为 `positions` 数组不是以0开头。 第一种颜色用于0到第一个数组值的偏移量。 渐变还会超出外部半径。 这就是 `Clamp` 平铺模式的结果。 由于渐变用于粗线，因此这些红色区域不可见。

## <a name="radial-gradients-for-masking"></a>屏蔽的径向渐变

与线性渐变一样，径向渐变可以包含透明或部分透明的颜色。 此功能适用于称为 " _屏蔽_" 的进程，该进程隐藏部分图像以强调设计图像的另一部分。

**径向渐变掩码**页显示了一个示例。 程序加载一个资源位图。 `CENTER`和 `RADIUS` 字段是从位图检查中确定的，并引用应突出显示的区域。 `PaintSurface`处理程序首先计算一个矩形以便显示位图，然后将其显示在该矩形中：

```csharp
public class RadialGradientMaskPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
        typeof(RadialGradientMaskPage),
        "SkiaSharpFormsDemos.Media.MountainClimbers.jpg");

    static readonly SKPoint CENTER = new SKPoint(180, 300);
    static readonly float RADIUS = 120;

    public RadialGradientMaskPage ()
    {
        Title = "Radial Gradient Mask";

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

        // Find rectangle to display bitmap
        float scale = Math.Min((float)info.Width / bitmap.Width,
                                (float)info.Height / bitmap.Height);

        SKRect rect = SKRect.Create(scale * bitmap.Width, scale * bitmap.Height);

        float x = (info.Width - rect.Width) / 2;
        float y = (info.Height - rect.Height) / 2;
        rect.Offset(x, y);

        // Display bitmap in rectangle
        canvas.DrawBitmap(bitmap, rect);

        // Adjust center and radius for scaled and offset bitmap
        SKPoint center = new SKPoint(scale * CENTER.X + x,
                                     scale * CENTER.Y + y);
        float radius = scale * RADIUS;

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateRadialGradient(
                                center,
                                radius,
                                new SKColor[] { SKColors.Transparent,
                                                SKColors.White },
                                new float[] { 0.6f, 1 },
                                SKShaderTileMode.Clamp);

            // Display rectangle using that gradient
            canvas.DrawRect(rect, paint);
        }
    }
}
```

绘制位图后，一些简单的代码会将 `CENTER` 和转换 `RADIUS` 为 `center` 和 `radius` ，它们引用位图中突出显示的区域，以供显示。 这些值用于创建具有该中心和半径的径向渐变。 这两种颜色在中心的透明位置和半径的前60。 然后渐变过渡到白色：

[![径向渐变掩码](circular-gradients-images/RadialGradientMask.png "径向渐变掩码")](circular-gradients-images/RadialGradientMask-Large.png#lightbox)

此方法不是掩蔽位图的最佳方式。 问题在于掩码大多具有白色颜色，这是为与画布背景相匹配而选择的。 如果背景为某种其他颜色， &mdash; 或者可能是渐变本身， &mdash; 则不匹配。 [SkiaSharp Porter-Duff blend 模式](../blend-modes/porter-duff.md)一文中显示了更好的屏蔽方法。

## <a name="radial-gradients-for-specular-highlights"></a>反射高光的径向渐变

当光源出现圆时，它会在许多方向上反映光，但某些光源会直接转换为观看者的眼睛。 这通常会在图面上创建一个称为 _反射高光_的模糊空白区域。

在三维图形中，反射高光通常由用于确定光路径和阴影的算法引起。 在二维图形中，反射高光有时会添加到建议三维表面的外观。 反射高光可以将平面红色圆圈转换为圆形红球。

**径向镜面高光**页面使用径向渐变来精确执行此操作。 `PaintSurface`处理程序就是通过计算圆的半径，将两个 `SKPoint` 值 &mdash; a `center` 和一个 `offCenter` 介于中心与圆的左上方之间的中间边缘：

```csharp
public class RadialSpecularHighlightPage : ContentPage
{
    public RadialSpecularHighlightPage()
    {
        Title = "Radial Specular Highlight";

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

        float radius = 0.4f * Math.Min(info.Width, info.Height);
        SKPoint center = new SKPoint(info.Rect.MidX, info.Rect.MidY);
        SKPoint offCenter = center - new SKPoint(radius / 2, radius / 2);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateRadialGradient(
                                offCenter,
                                radius / 2,
                                new SKColor[] { SKColors.White, SKColors.Red },
                                null,
                                SKShaderTileMode.Clamp);

            canvas.DrawCircle(center, radius, paint);
        }
    }
}
```

`CreateRadialGradient`调用会创建一个渐变，该渐变在该点处以白色开始，以 `offCenter` 红色以半径的一半结束。 它的外观如下所示：

[![径向镜面高光](circular-gradients-images/RadialSpecularHighlight.png "径向镜面高光")](circular-gradients-images/RadialSpecularHighlight-Large.png#lightbox)

如果你仔细查看此渐变，你可能会认为它是有缺陷的。 渐变中心围绕某个特定点，你可能希望它的对称程度略低，以反映圆角面。 在这种情况下，您可能更倾向于[**如下反射高光：**](#conical-gradients-for-specular-highlights)

## <a name="the-sweep-gradient"></a>扫描渐变

[`CreateSweepGradient`](xref:SkiaSharp.SKShader.CreateSweepGradient(SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[]))方法具有所有渐变创建方法的最简单语法：

```csharp
public static SKShader CreateSweepGradient (SKPoint center, 
                                            SKColor[] colors, 
                                            Single[] colorPos)
```

它只是一个中心、一组颜色和颜色位置。 渐变在中心点的右侧开始，并沿中心顺时针方向旋转360度。 请注意，没有 `SKShaderTileMode` 参数。

[`CreateSweepGradient`](xref:SkiaSharp.SKShader.CreateSweepGradient(SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKMatrix))带有矩阵转换参数的重载也可用。 您可以对渐变应用旋转转换以更改起点。 还可以应用缩放转换，将方向从顺时针更改为逆时针。

" **扫描渐变** " 页使用扫描渐变为笔划宽度为50像素的圆形着色：

[![扫描渐变](circular-gradients-images/SweepGradient.png "扫描渐变")](circular-gradients-images/SweepGradient-Large.png#lightbox)

`SweepGradientPage`类定义具有不同色相值的8种颜色的数组。 请注意，该数组以 red (值0或 360) （显示在屏幕截图的最右侧）开头和结尾：

```csharp
public class SweepGradientPage : ContentPage
{
    bool drawBackground;

    public SweepGradientPage ()
    {
        Title = "Sweep Gradient";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

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
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            // Define an array of rainbow colors
            SKColor[] colors = new SKColor[8];

            for (int i = 0; i < colors.Length; i++)
            {
                colors[i] = SKColor.FromHsl(i * 360f / 7, 100, 50);
            }

            SKPoint center = new SKPoint(info.Rect.MidX, info.Rect.MidY);

            // Create sweep gradient based on center of canvas
            paint.Shader = SKShader.CreateSweepGradient(center, colors, null);

            // Draw a circle with a wide line
            const int strokeWidth = 50;
            paint.Style = SKPaintStyle.Stroke;
            paint.StrokeWidth = strokeWidth;

            float radius = (Math.Min(info.Width, info.Height) - strokeWidth) / 2;
            canvas.DrawCircle(center, radius, paint);

            if (drawBackground)
            {
                // Draw the gradient on the whole canvas
                paint.Style = SKPaintStyle.Fill;
                canvas.DrawRect(info.Rect, paint);
            }
        }
    }
}
```

此程序还实现了一个 `TapGestureRecognizer` ，它在处理程序的末尾启用了一些代码 `PaintSurface` 。 此代码使用相同的渐变填充画布：

[![扫描梯度已满](circular-gradients-images/SweepGradientFull.png "扫描梯度已满")](circular-gradients-images/SweepGradientFull-Large.png#lightbox)

这些屏幕截图演示了渐变填充了其颜色的所有区域。 如果渐变不是以相同颜色开始和结束，则中心点的右侧会出现不一致的情况。

## <a name="the-two-point-conical-gradient"></a>双点圆锥渐变

[`CreateTwoPointConicalGradient`](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode))方法具有以下语法：

```csharp
public static SKShader CreateTwoPointConicalGradient (SKPoint startCenter, 
                                                      Single startRadius, 
                                                      SKPoint endCenter, 
                                                      Single endRadius, 
                                                      SKColor[] colors, 
                                                      Single[] colorPos, 
                                                      SKShaderTileMode mode)
```

参数以中心点和半径（两个圆圈）开头，称为 _开始_ 圆圈和 _结束_ 圆圈。 其余三个参数与和的参数相同 `CreateLinearGradient` `CreateRadialGradient` 。 [`CreateTwoPointConicalGradient`](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix))重载包括矩阵转换。

渐变从起始圆开始，以结束圆结束。 `SKShaderTileMode`参数控制在两个圆圈外发生的情况。 双点圆锥渐变是唯一不完全填充区域的渐变。 如果两个圆圈具有相同的半径，则将渐变限制为宽度与圆直径相同的矩形。 如果这两个圆的半径不同，则渐变将形成一个锥。

您可能想要试验双点圆锥渐变，因此， **圆锥的渐变** 页面从派生，以允许两个 `InteractivePage` 触摸点四处移动两个圆圈半径：

```xaml
<local:InteractivePage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       xmlns:local="clr-namespace:SkiaSharpFormsDemos"
                       xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
                       xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
                       xmlns:tt="clr-namespace:TouchTracking"
                       x:Class="SkiaSharpFormsDemos.Effects.ConicalGradientPage"
                       Title="Conical Gradient">
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

代码隐藏文件定义了两个 `TouchPoint` 固定半径为50和100的对象：

```csharp
public partial class ConicalGradientPage : InteractivePage
{
    const int RADIUS1 = 50;
    const int RADIUS2 = 100;

    public ConicalGradientPage ()
    {
        touchPoints = new TouchPoint[2];

        touchPoints[0] = new TouchPoint
        {
            Center = new SKPoint(100, 100),
            Radius = RADIUS1
        };

        touchPoints[1] = new TouchPoint
        {
            Center = new SKPoint(300, 300),
            Radius = RADIUS2
        };

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
            paint.Shader = SKShader.CreateTwoPointConicalGradient(touchPoints[0].Center,
                                                                  RADIUS1,
                                                                  touchPoints[1].Center,
                                                                  RADIUS2,
                                                                  colors,
                                                                  null,
                                                                  tileMode);
            canvas.DrawRect(info.Rect, paint);
        }

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
        }
    }
}
```

`colors`数组为红色、绿色和蓝色。 指向处理程序底部的代码 `PaintSurface` 将两个触摸点绘制为黑色圆圈，使其不会妨碍渐变。

请注意， `DrawRect` 调用使用渐变来为整个画布着色。 但是，在一般情况下，uncolored 的很多画布仍是通过渐变来进行的。 以下程序显示了三种可能的配置：

[![圆锥渐变](circular-gradients-images/ConicalGradient.png "圆锥渐变")](circular-gradients-images/ConicalGradient-Large.png#lightbox)

左侧的 iOS 屏幕显示了 `SKShaderTileMode` 设置的效果 `Clamp` 。 在较小圆圈的边缘（相对于第二个圆最近的一侧）以红色开头的渐变。 `Clamp`值还会导致红色，使其继续到圆锥点。 渐变以蓝色结束，位于最靠近第一个圆圈的最大圆圈的外边缘，但在该圆圈和更大范围内继续蓝色。

Android 屏幕类似，但具有 `SKShaderTileMode` 的 `Repeat` 。 现在，渐变在第一个圆圈内开始，并在第二个圆圈外结束。 此 `Repeat` 设置会导致在较大圆圈内再次向此渐变重复一次。

UWP 屏幕显示当小圆圈完全移到较大圆圈内时会发生的情况。 梯度停止为圆锥，而是填充整个区域。 其效果类似于径向渐变，但如果较小的圆不能正好在大圆圈内居中，则会不对称。

当一个圆嵌套在另一个圆中时，您可能会怀疑渐变的实际用处，但它非常适合于反射高光。

## <a name="conical-gradients-for-specular-highlights"></a>镜面高光的圆锥渐变

本文前面介绍了如何使用径向渐变来创建镜面高光。 你还可以使用双点圆锥渐变来实现此目的，并且你可能更喜欢它的外观：

[![圆锥反射高光](circular-gradients-images/ConicalSpecularHighlight.png "圆锥反射高光")](circular-gradients-images/ConicalSpecularHighlight-Large.png#lightbox)

非对称外观更好地建议了对象的圆角面。 

**圆锥反射高光**页中的绘图代码与**径向反射高光**页相同（着色器除外）：

```csharp
public class ConicalSpecularHighlightPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateTwoPointConicalGradient(
                                offCenter,
                                1,
                                center,
                                radius,
                                new SKColor[] { SKColors.White, SKColors.Red },
                                null,
                                SKShaderTileMode.Clamp);

            canvas.DrawCircle(center, radius, paint);
        }
    }
}
```

这两个圆的中心为 `offCenter` 和 `center` 。 位于中心的圆 `center` 与包围整个球的半径相关联，但中心中心的圆的 `offCenter` 半径只有一个像素。 此渐变从该点开始有效地结束，并以球的边缘结束。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)