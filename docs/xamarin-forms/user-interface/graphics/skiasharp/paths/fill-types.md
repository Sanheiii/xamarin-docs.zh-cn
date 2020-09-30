---
title: 路径填充类型
description: 本文探讨了 SkiaSharp 路径填充类型可能产生的不同效果，并通过示例代码对此进行演示。
ms.prod: xamarin
ms.assetid: 57103A7A-49A2-46AE-894C-7C2664682644
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: aa427035dc33b617bed14e0c6aa0b0db27efca7f
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556563"
---
# <a name="the-path-fill-types"></a>路径填充类型

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_发现 SkiaSharp 路径填充类型可能产生的不同效果_

路径中的两个轮廓可以重叠，构成单个等高线的线条可能会重叠。 任何封闭区域都可以进行填充，但您可能不希望填写所有包含的区域。 下面是一个示例：

![五角星部分 filles](fill-types-images/filltypeexample.png)

对此有一些控制。 填充算法由的 [`SKFillType`](xref:SkiaSharp.SKPath.FillType) 属性控制 `SKPath` ，您可以将其设置为枚举的成员 [`SKPathFillType`](xref:SkiaSharp.SKPathFillType) ：

- `Winding`（默认值）
- `EvenOdd`
- `InverseWinding`
- `InverseEvenOdd`

缠绕和偶奇算法都确定是否根据从该区域绘制的假设直线填充任何封闭区域。 该行跨越了一个或多个构成路径的边界线。 在缠绕模式下，如果在一个方向上绘制的边界线的数量与另一个方向上绘制的行数的平衡，则不填充该区域。 否则，将填充该区域。 如果边界行数为奇数，则奇数算法将填充区域。

对于许多例程路径，缠绕算法通常会填充路径的所有封闭区域。 偶-奇算法通常产生更有趣的结果。

典型示例是一个五向星形，如 **五星星形** 页中所示。 [**FivePointedStarPage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/FivePointedStarPage.xaml)文件实例化两个 `Picker` 视图以选择路径填充类型，并确定路径是描边还是填充，或按何种顺序进行：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Paths.FivePointedStarPage"
             Title="Five-Pointed Star">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>

        <Picker x:Name="fillTypePicker"
                Title="Path Fill Type"
                Grid.Row="0"
                Grid.Column="0"
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKPathFillType}">
                    <x:Static Member="skia:SKPathFillType.Winding" />
                    <x:Static Member="skia:SKPathFillType.EvenOdd" />
                    <x:Static Member="skia:SKPathFillType.InverseWinding" />
                    <x:Static Member="skia:SKPathFillType.InverseEvenOdd" />
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Picker x:Name="drawingModePicker"
                Title="Drawing Mode"
                Grid.Row="0"
                Grid.Column="1"
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type x:String}">
                    <x:String>Fill only</x:String>
                    <x:String>Stroke only</x:String>
                    <x:String>Stroke then Fill</x:String>
                    <x:String>Fill then Stroke</x:String>
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <skiaforms:SKCanvasView x:Name="canvasView"
                                Grid.Row="1"
                                Grid.Column="0"
                                Grid.ColumnSpan="2"
                                PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

代码隐藏文件使用这两个 `Picker` 值来绘制五角星：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
    float radius = 0.45f * Math.Min(info.Width, info.Height);

    SKPath path = new SKPath
    {
        FillType = (SKPathFillType)fillTypePicker.SelectedItem
    };
    path.MoveTo(info.Width / 2, info.Height / 2 - radius);

    for (int i = 1; i < 5; i++)
    {
        // angle from vertical
        double angle = i * 4 * Math.PI / 5;
        path.LineTo(center + new SKPoint(radius * (float)Math.Sin(angle),
                                        -radius * (float)Math.Cos(angle)));
    }
    path.Close();

    SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 50,
        StrokeJoin = SKStrokeJoin.Round
    };

    SKPaint fillPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue
    };

    switch ((string)drawingModePicker.SelectedItem)
    {
        case "Fill only":
            canvas.DrawPath(path, fillPaint);
            break;

        case "Stroke only":
            canvas.DrawPath(path, strokePaint);
            break;

        case "Stroke then Fill":
            canvas.DrawPath(path, strokePaint);
            canvas.DrawPath(path, fillPaint);
            break;

        case "Fill then Stroke":
            canvas.DrawPath(path, fillPaint);
            canvas.DrawPath(path, strokePaint);
            break;
    }
}
```

通常，路径填充类型只会影响填充，而不会影响笔划，但这两种 `Inverse` 模式会影响填充和笔划。 对于填充，两种 `Inverse` 类型填充区域 oppositely，以使星形外的区域填满。 对于笔划，这两种 `Inverse` 类型的颜色都与笔划不同。 使用这些反转填充类型可能会产生一些奇怪的效果，如 iOS 屏幕截图所示：

[![五指星形页面的三向屏幕截图](fill-types-images/fivepointedstar-small.png)](fill-types-images/fivepointedstar-large.png#lightbox "五指星形页面的三向屏幕截图")

Android 屏幕快照显示了典型的偶数和缠绕效果，但笔划和填充的顺序也会影响结果。

缠绕算法依赖于绘制线条的方向。 通常，在创建路径时，可以在指定从一个点到另一个点绘制线条时控制该方向。 但是， `SKPath` 类还定义 `AddRect` `AddCircle` 绘制整个轮廓的方法，如和。 若要控制这些对象的绘制方式，方法包含类型的参数 [`SKPathDirection`](xref:SkiaSharp.SKPathDirection) ，该参数具有两个成员：

- `Clockwise`
- `CounterClockwise`

中 `SKPath` 包含参数的方法 `SKPathDirection` 为其指定了默认值 `Clockwise` 。

" **重叠圆圈** " 页将创建一个路径，该路径包含四个具有偶数类路径填充类型的重叠圆圈：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
    float radius = Math.Min(info.Width, info.Height) / 4;

    SKPath path = new SKPath
    {
        FillType = SKPathFillType.EvenOdd
    };

    path.AddCircle(center.X - radius / 2, center.Y - radius / 2, radius);
    path.AddCircle(center.X - radius / 2, center.Y + radius / 2, radius);
    path.AddCircle(center.X + radius / 2, center.Y - radius / 2, radius);
    path.AddCircle(center.X + radius / 2, center.Y + radius / 2, radius);

    SKPaint paint = new SKPaint()
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Cyan
    };

    canvas.DrawPath(path, paint);

    paint.Style = SKPaintStyle.Stroke;
    paint.StrokeWidth = 10;
    paint.Color = SKColors.Magenta;

    canvas.DrawPath(path, paint);
}
```

使用最少的代码创建了一个有趣的图像：

[![重叠圆圈页面的三向屏幕截图](fill-types-images/overlappingcircles-small.png)](fill-types-images/overlappingcircles-large.png#lightbox "重叠圆圈页面的三向屏幕截图")

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)