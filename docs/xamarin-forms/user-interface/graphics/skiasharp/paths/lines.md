---
title: 线和笔划大写字母
description: 本文介绍如何使用 SkiaSharp 在应用程序中使用不同的笔划帽绘制线条 Xamarin.Forms ，并使用示例代码对此进行演示。
ms.prod: xamarin
ms.assetid: 1F854DDD-5D1B-4DE4-BD2D-584439429FDB
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 75d19e41243076da127d58defdabeca908d900cd
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556082"
---
# <a name="lines-and-stroke-caps"></a>线和笔划大写字母

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_了解如何使用 SkiaSharp 来绘制具有不同笔划帽的线条_

在 SkiaSharp 中，呈现单行与呈现一系列连接的直线非常不同。 不过，即使在绘制单条行时，也经常需要为行指定一个特定的笔划宽度。 当这些行变宽时，行尾的外观也会变得很重要。 线条末尾的外观称为 " *笔划帽*"：

![三个笔划大写字母选项](lines-images/strokecapsexample.png)

对于绘制单行， `SKCanvas` 定义一个简单的 [`DrawLine`](xref:SkiaSharp.SKCanvas.DrawLine(System.Single,System.Single,System.Single,System.Single,SkiaSharp.SKPaint)) 方法，该方法的参数指示行的起始和结束坐标与 `SKPaint` 对象：

```csharp
canvas.DrawLine (x0, y0, x1, y1, paint);
```

默认情况下， [`StrokeWidth`](xref:SkiaSharp.SKPaint.StrokeWidth) 新实例化的对象的属性 `SKPaint` 为0，它在呈现宽度为1个像素的行时具有与值1相同的效果。 这在高分辨率设备（如手机）上显得非常小，因此你可能需要将设置 `StrokeWidth` 为更大的值。 但一旦开始绘制可调整大小的粗细的线条，就会引发另一个问题：如何呈现这些粗线条的开头和结尾？

行的开头和结尾的外观称为 *线帽* ，在 Skia 中，为 *描边帽*。 这种上下文中的 "cap" 一词是指位于行末尾的一种 hat &mdash; 。 将对象的 [`StrokeCap`](xref:SkiaSharp.SKPaint.StrokeCap) 属性设置 `SKPaint` 为枚举的以下成员之一 [`SKStrokeCap`](xref:SkiaSharp.SKStrokeCap) ：

- `Butt` (默认) 
- `Square`
- `Round`

下面是一个示例程序的最佳说明。 [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)程序的 " **SkiaSharp 行和路径**" 部分从标题为 "**笔划大写**" 的页开始 [`StrokeCapsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/StrokeCapsPage.cs) 。 此页定义了一个 `PaintSurface` 事件处理程序，该事件处理程序遍历枚举的三个成员 `SKStrokeCap` ，同时显示枚举成员的名称并使用该笔划帽绘制一行：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint textPaint = new SKPaint
    {
        Color = SKColors.Black,
        TextSize = 75,
        TextAlign = SKTextAlign.Center
    };

    SKPaint thickLinePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Orange,
        StrokeWidth = 50
    };

    SKPaint thinLinePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 2
    };

    float xText = info.Width / 2;
    float xLine1 = 100;
    float xLine2 = info.Width - xLine1;
    float y = textPaint.FontSpacing;

    foreach (SKStrokeCap strokeCap in Enum.GetValues(typeof(SKStrokeCap)))
    {
        // Display text
        canvas.DrawText(strokeCap.ToString(), xText, y, textPaint);
        y += textPaint.FontSpacing;

        // Display thick line
        thickLinePaint.StrokeCap = strokeCap;
        canvas.DrawLine(xLine1, y, xLine2, y, thickLinePaint);

        // Display thin line
        canvas.DrawLine(xLine1, y, xLine2, y, thinLinePaint);
        y += 2 * textPaint.FontSpacing;
    }
}
```

对于枚举中的每个成员 `SKStrokeCap` ，处理程序将绘制两行，其中一个笔画的粗细为50像素，另一个行位于顶部，并以两个像素为笔画宽度。 此第二行旨在说明行的几何起点和终点（与线条粗细和笔划帽无关）：

[![笔划顶端页面的三向屏幕截图](lines-images/strokecaps-small.png)](lines-images/strokecaps-large.png#lightbox "笔划顶端页面的三向屏幕截图")

正如您所看到的， `Square` 和 `Round` 笔划的 caps 会有效地将行的长度扩展到行首的半个笔划宽度处，并在末尾再次扩展。 如果需要确定呈现的图形对象的维度，则此扩展插件非常重要。

`SKCanvas`类还包含用于绘制多行的另一种方法，该方法有点特殊：

```csharp
DrawPoints (SKPointMode mode, points, paint)
```

`points`参数是一个值数组，它 `SKPoint` `mode` 是枚举的成员 [`SKPointMode`](xref:SkiaSharp.SKPointMode) ，它具有三个成员：

- `Points` 呈现各个点
- `Lines` 连接每对点
- `Polygon` 连接所有连续点

" **多行** " 页演示了此方法。 [**MultipleLinesPage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/MultipleLinesPage.xaml)文件实例化两个 `Picker` 视图，使您可以选择枚举的成员 `SKPointMode` 和枚举的成员 `SKStrokeCap` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Paths.MultipleLinesPage"
             Title="Multiple Lines">
    <Grid>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>

        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Picker x:Name="pointModePicker"
                Title="Point Mode"
                Grid.Row="0"
                Grid.Column="0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKPointMode}">
                    <x:Static Member="skia:SKPointMode.Points" />
                    <x:Static Member="skia:SKPointMode.Lines" />
                    <x:Static Member="skia:SKPointMode.Polygon" />
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Picker x:Name="strokeCapPicker"
                Title="Stroke Cap"
                Grid.Row="0"
                Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKStrokeCap}">
                    <x:Static Member="skia:SKStrokeCap.Butt" />
                    <x:Static Member="skia:SKStrokeCap.Round" />
                    <x:Static Member="skia:SKStrokeCap.Square" />
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <skiaforms:SKCanvasView x:Name="canvasView"
                                PaintSurface="OnCanvasViewPaintSurface"
                                Grid.Row="1"
                                Grid.Column="0"
                                Grid.ColumnSpan="2" />
    </Grid>
</ContentPage>
```

请注意，SkiaSharp 命名空间声明略有不同，因为 `SkiaSharp` 需要命名空间才能引用和枚举的成员 `SKPointMode` `SKStrokeCap` 。 `SelectedIndexChanged`这两个视图的处理程序 `Picker` 只是使 `SKCanvasView` 对象失效：

```csharp
void OnPickerSelectedIndexChanged(object sender, EventArgs args)
{
    if (canvasView != null)
    {
        canvasView.InvalidateSurface();
    }
}
```

此处理程序需要检查对象是否存在 `SKCanvasView` ，因为当 `SelectedIndex` XAML 文件中的属性设置为0时，将首先调用事件处理程序 `Picker` ，并在 `SKCanvasView` 实例化之前发生。

`PaintSurface`处理程序从视图中获取两个枚举值 `Picker` ：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Create an array of points scattered through the page
    SKPoint[] points = new SKPoint[10];

    for (int i = 0; i < 2; i++)
    {
        float x = (0.1f + 0.8f * i) * info.Width;

        for (int j = 0; j < 5; j++)
        {
            float y = (0.1f + 0.2f * j) * info.Height;
            points[2 * j + i] = new SKPoint(x, y);
        }
    }

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.DarkOrchid,
        StrokeWidth = 50,
        StrokeCap = (SKStrokeCap)strokeCapPicker.SelectedItem
    };

    // Render the points by calling DrawPoints
    SKPointMode pointMode = (SKPointMode)pointModePicker.SelectedItem;
    canvas.DrawPoints(pointMode, points, paint);
}
```

屏幕截图显示了多种 `Picker` 选择：

[![多行页面的三个屏幕截图](lines-images/multiplelines-small.png)](lines-images/multiplelines-large.png#lightbox "多行页面的三个屏幕截图")

左侧的 iPhone 显示 `SKPointMode.Points` 枚举成员如何导致 `DrawPoints` 数组中的每个点呈现 `SKPoint` 为一个正方形（如果行帽为 `Butt` 或） `Square` 。 如果线帽为，则呈现圆圈 `Round` 。

Android 屏幕截图显示了的结果 `SKPointMode.Lines` 。 `DrawPoints` `SKPoint` 在这种情况下，方法使用指定的线帽在每对值之间绘制一条线 `Round` 。

改为使用时 `SKPointMode.Polygon` ，会在数组中的连续点之间绘制一条线，但如果非常接近，则会看到这些行未连接。 每个单独的行都以指定的线帽开始和结束。 如果选择了 " `Round` cap"，则可能看起来好像已连接，但它们却没有连接。

是否连接或未连接行是使用图形路径的关键方面。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)