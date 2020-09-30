---
title: SkiaSharp 中的基本动画
description: 本文介绍如何在应用程序中对 SkiaSharp 图形进行动画处理 Xamarin.Forms ，并通过示例代码对此进行演示。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 31C96FD6-07E4-4473-A551-24753A5118C3
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3052220b914b09f18490846bbd2558bbf07e4d3a
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562257"
---
# <a name="basic-animation-in-skiasharp"></a>SkiaSharp 中的基本动画

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_了解如何对 SkiaSharp 图形进行动画处理_

您可以通过在中对 SkiaSharp 图形进行动画处理 Xamarin.Forms ，从而使 `PaintSurface` 方法定期调用，每次绘制图形的方式略有不同。 本文后面的动画显示了看似从中心展开的同心圆：

![看似从中心展开的几个同心圆圆圈](animation-images/animationexample.png)

[**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)程序中的**Pulsating 椭圆**页对椭圆的两个轴进行动画处理，使其看起来像 Pulsating，甚至还可以控制此 pulsation 的速率。 [**PulsatingEllipsePage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/PulsatingEllipsePage.xaml)文件实例化 Xamarin.Forms `Slider` 和 `Label` 以显示滑块的当前值。 这是将 `SKCanvasView` 与其他视图集成的常见方法 Xamarin.Forms ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.PulsatingEllipsePage"
             Title="Pulsating Ellipse">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Slider x:Name="slider"
                Grid.Row="0"
                Maximum="10"
                Minimum="0.1"
                Value="5"
                Margin="20, 0" />

        <Label Grid.Row="1"
               Text="{Binding Source={x:Reference slider},
                              Path=Value,
                              StringFormat='Cycle time = {0:F1} seconds'}"
               HorizontalTextAlignment="Center" />

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="2"
                           PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

代码隐藏文件实例化一个 `Stopwatch` 对象，以用作高精度时钟。 `OnAppearing`重写将 `pageIsActive` 字段设置为 `true` ，并调用名为的方法 `AnimationLoop` 。 该 `OnDisappearing` 替代将该 `pageIsActive` 字段设置为 `false` ：

```csharp
Stopwatch stopwatch = new Stopwatch();
bool pageIsActive;
float scale;            // ranges from 0 to 1 to 0

public PulsatingEllipsePage()
{
    InitializeComponent();
}

protected override void OnAppearing()
{
    base.OnAppearing();
    pageIsActive = true;
    AnimationLoop();
}

protected override void OnDisappearing()
{
    base.OnDisappearing();
    pageIsActive = false;
}
```

`AnimationLoop`方法在 `Stopwatch` 为时启动，然后循环 `pageIsActive` `true` 。 当页处于活动状态时，这实质上是一个 "无限循环"，但它不会导致程序挂起，因为循环会通过调用 `Task.Delay` with 运算符来结束 `await` ，这将允许程序函数的其他部分。 用于 `Task.Delay` 使其在 1/30 秒之后完成的自变量。 这会定义动画的帧速率。

```csharp
async Task AnimationLoop()
{
    stopwatch.Start();

    while (pageIsActive)
    {
        double cycleTime = slider.Value;
        double t = stopwatch.Elapsed.TotalSeconds % cycleTime / cycleTime;
        scale = (1 + (float)Math.Sin(2 * Math.PI * t)) / 2;
        canvasView.InvalidateSurface();
        await Task.Delay(TimeSpan.FromSeconds(1.0 / 30));
    }

    stopwatch.Stop();
}

```

`while`循环首先从获取的周期时间开始 `Slider` 。 这是一个时间（以秒为单位），例如5。 第二个语句 `t` 为 *time*计算值。 对于 `cycleTime` 5， `t` 每5秒增加0到1。 `Math.Sin`第二个语句中函数的参数的范围为0到2π，每5秒一次。 `Math.Sin`函数返回一个介于0到1之间的值，返回0，然后返回 &ndash; 1 和0，每5秒一次，但如果值接近1或-1，则值的更改速度将变慢。 添加了值1，因此值始终为正值，然后将其除以2，因此，这些值的范围介于1/2 到1到1/2 到0到1/2 之间，但当值介于1和0之间时，它们会变慢。 此项存储在 `scale` 字段中，并且 `SKCanvasView` 无效。

`PaintSurface`方法使用此 `scale` 值来计算椭圆的两个轴：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float maxRadius = 0.75f * Math.Min(info.Width, info.Height) / 2;
    float minRadius = 0.25f * maxRadius;

    float xRadius = minRadius * scale + maxRadius * (1 - scale);
    float yRadius = maxRadius * scale + minRadius * (1 - scale);

    using (SKPaint paint = new SKPaint())
    {
        paint.Style = SKPaintStyle.Stroke;
        paint.Color = SKColors.Blue;
        paint.StrokeWidth = 50;
        canvas.DrawOval(info.Width / 2, info.Height / 2, xRadius, yRadius, paint);

        paint.Style = SKPaintStyle.Fill;
        paint.Color = SKColors.SkyBlue;
        canvas.DrawOval(info.Width / 2, info.Height / 2, xRadius, yRadius, paint);
    }
}
```

方法基于显示区域的大小计算最大半径，并基于最大半径计算最小半径。 `scale`值在0和1之间进行了动画处理，返回到0，因此该方法使用来计算 `xRadius` 和 `yRadius` 之间的范围 `minRadius` `maxRadius` 。 这些值用于绘制和填充椭圆：

[![Pulsating 椭圆页的三向屏幕截图](animation-images/pulsatingellipse-small.png)](animation-images/pulsatingellipse-large.png#lightbox "Pulsating 椭圆页的三向屏幕截图")

请注意， `SKPaint` 对象是在块中创建的 `using` 。 与许多 SkiaSharp 类一样，派生自，后者 `SKPaint` `SKObject` `SKNativeObject` 实现 [`IDisposable`](xref:System.IDisposable) 接口。 `SKPaint` 重写 `Dispose` 方法以释放非托管资源。

 放 `SKPaint` 入 `using` 块可确保 `Dispose` 在块的末尾调用，以释放这些非托管资源。 当由 `SKPaint` .net 垃圾回收器释放对象所使用的内存，但在动画代码中，最好以更具序的方式释放内存，这种情况仍会发生。

 在这种特定情况下，更好的解决方案是创建两个 `SKPaint` 对象一次，并将其另存为字段。

这就是 **扩展圆圈** 动画的作用。 [`ExpandingCirclesPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ExpandingCirclesPage.cs)类首先定义多个字段，包括 `SKPaint` 对象：

```csharp
public class ExpandingCirclesPage : ContentPage
{
    const double cycleTime = 1000;       // in milliseconds

    SKCanvasView canvasView;
    Stopwatch stopwatch = new Stopwatch();
    bool pageIsActive;
    float t;
    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke
    };

    public ExpandingCirclesPage()
    {
        Title = "Expanding Circles";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }
    ...
}
```

此程序使用不同的方法根据方法进行动画处理 Xamarin.Forms `Device.StartTimer` 。 此 `t` 字段每毫秒从0到1进行动画处理 `cycleTime` ：

```csharp
public class ExpandingCirclesPage : ContentPage
{
    ...
    protected override void OnAppearing()
    {
        base.OnAppearing();
        pageIsActive = true;
        stopwatch.Start();

        Device.StartTimer(TimeSpan.FromMilliseconds(33), () =>
        {
            t = (float)(stopwatch.Elapsed.TotalMilliseconds % cycleTime / cycleTime);
            canvasView.InvalidateSurface();

            if (!pageIsActive)
            {
                stopwatch.Stop();
            }
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

`PaintSurface`处理程序绘制具有动画半径的五个同心圆圆圈。 如果该 `baseRadius` 变量的计算结果为100，则在 `t` 从0到1的动画中，这五个圆圈的半径从0增加到100，100为200，200到300，300到400，400到500。 大多数圆的 `strokeWidth` 是50，但对于第一个圆圈， `strokeWidth` 动画从0到50。 对于大多数圆，颜色为蓝色，但对于最后一个圆圈，颜色将从蓝动画为透明。 请注意 `SKColor` 指定不透明度的构造函数的第四个参数：

```csharp
public class ExpandingCirclesPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
        float baseRadius = Math.Min(info.Width, info.Height) / 12;

        for (int circle = 0; circle < 5; circle++)
        {
            float radius = baseRadius * (circle + t);

            paint.StrokeWidth = baseRadius / 2 * (circle == 0 ? t : 1);
            paint.Color = new SKColor(0, 0, 255,
                (byte)(255 * (circle == 4 ? (1 - t) : 1)));

            canvas.DrawCircle(center.X, center.Y, radius, paint);
        }
    }
}
```

结果是，当 `t` 等于0时，图像看起来相同 `t` ，而圆圈看起来始终持续展开：

[!["扩展圈" 页的三向屏幕截图](animation-images/expandingcircles-small.png)](animation-images/expandingcircles-large.png#lightbox ""扩展圈" 页的三向屏幕截图")

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)