---
title: SkiaSharp 中的 Finger 绘图
description: 本文介绍如何使用手指在应用程序的 SkiaSharp 画布上进行绘制 Xamarin.Forms ，并使用示例代码对此进行演示。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 56929D74-8F2C-44C6-90E6-3FBABCDC0A4B
author: davidbritch
ms.author: dabritch
ms.date: 04/05/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 98722d29a674e4d69f31a670a541eaabb550898e
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91555640"
---
# <a name="finger-painting-in-skiasharp"></a>SkiaSharp 中的 Finger 绘图

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_使用手指在画布上绘制。_

`SKPath`可以持续更新和显示对象。 此功能允许将路径用于交互式绘图，例如在手指绘画程序中。

![手指绘制中的练习](finger-paint-images/fingerpaintsample.png)

中的触摸支持 Xamarin.Forms 不允许在屏幕上跟踪单个手指，因此开发了 Xamarin.Forms 触摸跟踪效果以提供额外的触控支持。 此效果在 [**从效果中调用事件**](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)一文中进行了介绍。 示例程序 [**触摸跟踪效果演示**](/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/) 包含使用 SkiaSharp 的两个页面，其中包括一个手指刷程序。

[**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)解决方案包括此触摸跟踪事件。 .NET Standard 库项目包括 `TouchEffect` 类、 `TouchActionType` 枚举、 `TouchActionEventHandler` 委托和 `TouchActionEventArgs` 类。 每个平台项目都包含一个 `TouchEffect` 适用于该平台的类; iOS 项目还包含一个 `TouchRecognizer` 类。

**SkiaSharpFormsDemos**中的**finger 绘图**页是一种简化的 finger 绘制实现。 它不允许选择颜色或描边宽度，它无法清除画布，当然也无法保存图稿。

[**FingerPaintPage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/FingerPaintPage.xaml)文件将放 `SKCanvasView` 入单个单元 `Grid` ，并将附加 `TouchEffect` 到 `Grid` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             xmlns:tt="clr-namespace:TouchTracking"
             x:Class="SkiaSharpFormsDemos.Paths.FingerPaintPage"
             Title="Finger Paint">

    <Grid BackgroundColor="White">
        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface" />
        <Grid.Effects>
            <tt:TouchEffect Capture="True"
                            TouchAction="OnTouchEffectAction" />
        </Grid.Effects>
    </Grid>
</ContentPage>
```

将 `TouchEffect` 直接附加到在 `SKCanvasView` 所有平台下都不起作用。

[**FingerPaintPage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/FingerPaintPage.xaml.cs)代码隐藏文件定义了两个用于存储对象的集合 `SKPath` ，以及 `SKPaint` 用于呈现这些路径的对象：

```csharp
public partial class FingerPaintPage : ContentPage
{
    Dictionary<long, SKPath> inProgressPaths = new Dictionary<long, SKPath>();
    List<SKPath> completedPaths = new List<SKPath>();

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = 10,
        StrokeCap = SKStrokeCap.Round,
        StrokeJoin = SKStrokeJoin.Round
    };

    public FingerPaintPage()
    {
        InitializeComponent();
    }
    ...
}
```

顾名思义， `inProgressPaths` 字典会将当前正在绘制的路径存储在一个或多个手指上。 字典键是触控事件随附的 touch ID。 此 `completedPaths` 字段是一组路径的集合，这些路径在绘制从屏幕提升的路径时已完成。

`TouchAction`处理程序管理这两个集合。 当手指首次触摸屏幕时，将向添加一个新的 `SKPath` `inProgressPaths` 。 当该手指移动时，附加的点将添加到该路径。 当指针被释放时，路径会传输到集合中 `completedPaths` 。 可以同时绘制多个手指。 每次更改路径或集合后， `SKCanvasView` 就会失效：

```csharp
public partial class FingerPaintPage : ContentPage
{
    ...
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        switch (args.Type)
        {
            case TouchActionType.Pressed:
                if (!inProgressPaths.ContainsKey(args.Id))
                {
                    SKPath path = new SKPath();
                    path.MoveTo(ConvertToPixel(args.Location));
                    inProgressPaths.Add(args.Id, path);
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Moved:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    SKPath path = inProgressPaths[args.Id];
                    path.LineTo(ConvertToPixel(args.Location));
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Released:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    completedPaths.Add(inProgressPaths[args.Id]);
                    inProgressPaths.Remove(args.Id);
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Cancelled:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    inProgressPaths.Remove(args.Id);
                    canvasView.InvalidateSurface();
                }
                break;
        }
    }
    ...
    SKPoint ConvertToPixel(Point pt)
    {
        return new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                           (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));
    }
}
```

触摸跟踪事件随附的点是 Xamarin.Forms 坐标，这些点必须转换为 SkiaSharp 坐标，这是像素。 这就是方法的用途 `ConvertToPixel` 。

然后，该 `PaintSurface` 处理程序只呈现两个路径集合。 先前完成的路径显示在 "正在进行的路径" 下面：

```csharp
public partial class FingerPaintPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKCanvas canvas = args.Surface.Canvas;
        canvas.Clear();

        foreach (SKPath path in completedPaths)
        {
            canvas.DrawPath(path, paint);
        }

        foreach (SKPath path in inProgressPaths.Values)
        {
            canvas.DrawPath(path, paint);
        }
    }
    ...
}
```

手指气急败坏仅受你的人才的限制：

[![手指绘制页的三重屏幕截图](finger-paint-images/fingerpaint-small.png)](finger-paint-images/fingerpaint-large.png#lightbox "手指绘制页的三重屏幕截图")

现在，您已了解如何绘制线条并使用参数方程定义曲线。 后面的有关 [**SkiaSharp 曲线和路径**](../curves/index.md) 的部分介绍了支持的各种曲线类型 `SKPath` 。 但有一个有用的先决条件就是探索 [**SkiaSharp 转换**](../transforms/index.md)。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [触摸跟踪效果演示 (示例) ](/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/)
- [从效果调用事件](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)