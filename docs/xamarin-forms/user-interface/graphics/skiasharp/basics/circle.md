---
title: 在 SkiaSharp 中绘制简单圆圈
description: 本文介绍了 SkiaSharp 的基础知识，包括画布和画图对象（在应用程序中）， Xamarin.Forms 并通过示例代码对此进行了演示。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: E3A4E373-F65D-45C8-8E77-577A804AC3F8
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 538a3ea3bd5b2293f93047d9796a185abfa5f2b9
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556589"
---
# <a name="drawing-a-simple-circle-in-skiasharp"></a>在 SkiaSharp 中绘制简单圆圈

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_了解 SkiaSharp 的基础知识，包括画布和画图对象_

本文介绍如何使用 SkiaSharp 中的绘制图形的概念 Xamarin.Forms ，包括创建 `SKCanvasView` 用于承载图形的对象、处理事件以及 `PaintSurface` 使用 `SKPaint` 对象指定颜色和其他绘制属性。

[**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)程序包含此系列 SkiaSharp 文章的所有示例代码。 第一页的标题为 **简单圆圈** ，并调用 page 类 [`SimpleCirclePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SimpleCirclePage.cs) 。 此代码演示如何在页面中心绘制一个圆，半径为100像素。 圆圈的轮廓为红色，圆的内部为蓝色。

![红色圆圈](circle-images/circleexample.png)

[`SimpleCircle`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SimpleCirclePage.cs)Page 类派生自 `ContentPage` ，其中包含 `using` SkiaSharp 命名空间的两个指令：

```csharp
using SkiaSharp;
using SkiaSharp.Views.Forms;
```

下面的类构造函数创建一个 [`SKCanvasView`](xref:SkiaSharp.Views.Forms.SKCanvasView) 对象，附加该事件的处理程序， [`PaintSurface`](xref:SkiaSharp.Views.Forms.SKCanvasView.PaintSurface) 并将该 `SKCanvasView` 对象设置为该页面的内容：

```csharp
public SimpleCirclePage()
{
    Title = "Simple Circle";

    SKCanvasView canvasView = new SKCanvasView();
    canvasView.PaintSurface += OnCanvasViewPaintSurface;
    Content = canvasView;
}
```

`SKCanvasView`占据页面的整个内容区域。 您也可以 `SKCanvasView` 将与其他 Xamarin.Forms `View` 派生项合并，如您将在其他示例中所见。

`PaintSurface`您可以在事件处理程序中执行所有绘图操作。 当程序运行时，可以多次调用此方法，因此它应保留重新创建图形显示所需的所有信息：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
}

```

[`SKPaintSurfaceEventArgs`](xref:SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs)事件附带的对象有两个属性：

- [`SKImageInfo`](xref:SkiaSharp.SKImageInfo) 类型的 [`Info`](xref:SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs.Info)
- [`SKSurface`](xref:SkiaSharp.SKSurface) 类型的 [`Surface`](xref:SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs.Surface)

`SKImageInfo`结构包含有关绘制图面的信息，最重要的是，它的宽度和高度（以像素为单位）。 `SKSurface`对象表示绘图图面本身。 在此程序中，绘图图面为视频显示，但在其他程序中， `SKSurface` 对象还可以表示使用 SkiaSharp 在上进行绘制的位图。

的最重要的属性 `SKSurface` [`Canvas`](xref:SkiaSharp.SKSurface.Canvas) 的类型为 [`SKCanvas`](xref:SkiaSharp.SKCanvas) 。 此类是一个图形绘图上下文，用于执行实际绘图。 `SKCanvas`对象封装图形状态，其中包括图形转换和剪辑。

下面是 `PaintSurface` 事件处理程序的典型启动：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();
    ...
}

```

[`Clear`](xref:SkiaSharp.SKCanvas.Clear)方法使用透明颜色清除画布。 重载允许您为画布指定背景色。

此处的目标是绘制一个蓝色实心圆圈。 由于此特定图形包含两种不同的颜色，因此需要执行两个步骤。 第一步是绘制圆形的轮廓。 若要指定线条的颜色和其他特性，可以创建和初始化 [`SKPaint`](xref:SkiaSharp.SKPaint) 对象：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = Color.Red.ToSKColor(),
        StrokeWidth = 25
    };
    ...
}
```

[`Style`](xref:SkiaSharp.SKPaint.Style)属性指示要在此例中为线条 (*笔画*，) 而不是*填充*内部。 枚举的三个成员 [`SKPaintStyle`](xref:SkiaSharp.SKPaintStyle) 如下：

- [`Fill`](xref:SkiaSharp.SKPaintStyle.Fill)
- [`Stroke`](xref:SkiaSharp.SKPaintStyle.Stroke)
- [`StrokeAndFill`](xref:SkiaSharp.SKPaintStyle.StrokeAndFill)

默认值为 `Fill`。 使用第三个选项可以为线条描边，并使用相同的颜色填充内部。

将 [`Color`](xref:SkiaSharp.SKPaint.Color) 属性设置为类型的值 [`SKColor`](xref:SkiaSharp.SKColor) 。 获取值的一种方法 `SKColor` 是 Xamarin.Forms `Color` 使用扩展方法将值转换为 `SKColor` 值 [`ToSKColor`](xref:SkiaSharp.Views.Forms.Extensions.ToSKColor*) 。 [`Extensions`](xref:SkiaSharp.Views.Forms.Extensions)命名空间中的类 `SkiaSharp.Views.Forms` 包括在 Xamarin.Forms 值和 SkiaSharp 值之间进行转换的其他方法。

[`StrokeWidth`](xref:SkiaSharp.SKPaint.StrokeWidth)属性指示线条的粗细。 此处设置为25像素。

使用该 `SKPaint` 对象绘制圆形：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    canvas.DrawCircle(info.Width / 2, info.Height / 2, 100, paint);
    ...
}
```

相对于显示图面的左上角指定坐标。 X 坐标向右增加，Y 坐标增加。 在有关图形的讨论中，通常使用数学符号 (x、y) 来表示一个点。 点 (0，0) 是显示表面的左上角，通常称为 *原点*。

的前两个参数 `DrawCircle` 指示圆圈中心的 X 和 Y 坐标。 它们被分配到显示图面的宽度和高度的一半，以将圆圈中心置于显示图面的中心。 第三个参数指定圆的半径，最后一个参数是 `SKPaint` 对象。

若要填充圆的内部，可以更改对象的两个属性 `SKPaint` ，并再次调用 `DrawCircle` 。 此代码还介绍了一种 `SKColor` 从结构的多个字段中获取值的另一种方法 [`SKColors`](xref:SkiaSharp.SKColors) ：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    paint.Style = SKPaintStyle.Fill;
    paint.Color = SKColors.Blue;
    canvas.DrawCircle(args.Info.Width / 2, args.Info.Height / 2, 100, paint);
}
```

这次，该 `DrawCircle` 调用使用对象的新属性来填充圆圈 `SKPaint` 。

下面是在 iOS 和 Android 上运行的程序：

[![简单圆形页面的三向屏幕截图](circle-images/simplecircle-small.png)](circle-images/simplecircle-large.png#lightbox "简单圆形页面的三向屏幕截图")

当你自行运行该程序时，你可以将手机或模拟器侧向旋转，以查看图形的重绘方式。 每次需要重绘图形时， `PaintSurface` 都会再次调用事件处理程序。

还可以通过渐变或位图图块为图形对象着色。 [**SkiaSharp 着色**](../effects/shaders/index.md)器部分讨论了这些选项。

`SKPaint`对象只是图形绘图属性的集合。 这些对象为轻型对象。 您可以 `SKPaint` 像此程序一样重复使用对象，也可以 `SKPaint` 为各种绘图属性组合创建多个对象。 您可以在事件处理程序外部创建和初始化这些对象 `PaintSurface` ，并可以将它们保存为页类中的字段。

> [!NOTE]
> `SKPaint`类定义 [`IsAntialias`](xref:SkiaSharp.SKPaint.IsAntialias) 以在呈现图形时启用抗锯齿。 消除锯齿通常会导致视觉对象更光滑，因此您可能希望 `true` 在大多数对象中将此属性设置为 `SKPaint` 。 为了简单起见，在大多数示例页中均 _未_ 设置此属性。

尽管圆轮廓的宽度指定为25像素 &mdash; 或圆圈半径的四分之一 &mdash; ，但有充分的理由需要：线条宽度的一半被蓝色圆圈所遮盖的部分的宽度为一半。 方法的参数 `DrawCircle` 定义圆的抽象几何坐标。 蓝色内部的大小调整为该尺寸到最接近的像素，但25个像素宽的轮廓跨越在外部的 &mdash; 内侧和一半上的几何圆一半。

[与 Xamarin.Forms 文章集成](~/xamarin-forms/user-interface/graphics/skiasharp/basics/integration.md)一文中的下一个示例演示了这一点。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)