---
title: SkiaSharp 位图的分段显示
description: 显示 SkiaSharp 位图，以便拉伸某些区域，而不是某些区域。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 79AE2033-C41C-4447-95A6-76D22E913D19
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9a39433a0bad518055542adb190f4f441675ddfb
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556368"
---
# <a name="segmented-display-of-skiasharp-bitmaps"></a>SkiaSharp 位图的分段显示

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

SkiaSharp `SKCanvas` 对象定义了一个名为 `DrawBitmapNinePatch` 的方法和两个名为的方法，这种方法 `DrawBitmapLattice` 非常相似。 这两种方法都将位图呈现给目标矩形的大小，而不是以统一方式拉伸位图，而是以像素尺寸显示位图的各个部分，并拉伸位图的其他部分，使其适应矩形：

![分段样本](segmented-images/SegmentedSample.png "分段样本")

这些方法通常用于呈现构成部分用户界面对象（如按钮）的位图。 设计按钮时，通常希望按钮的大小基于按钮的内容，但您可能希望按钮的边框与按钮的内容相同，而不管按钮的内容如何。 这是的理想应用 `DrawBitmapNinePatch` 。

`DrawBitmapNinePatch` 是一种特殊情况， `DrawBitmapLattice` 但使用和理解这种方法更为简单。

## <a name="the-nine-patch-display"></a>九修补程序显示 

从概念上讲， [`DrawBitmapNinePatch`](xref:SkiaSharp.SKCanvas.DrawBitmapNinePatch(SkiaSharp.SKBitmap,SkiaSharp.SKRectI,SkiaSharp.SKRect,SkiaSharp.SKPaint)) 将位图分为九个矩形：

![九修补程序](segmented-images/NinePatch.png "九修补程序")

四个角的矩形以像素大小显示。 当箭头指示时，位图边缘上的其他区域会水平或垂直拉伸到目标矩形的区域。 中心矩形沿水平方向和垂直方向拉伸。 

如果目标矩形中的空间不足以显示其像素尺寸中的四个角，则将其缩小到可用大小，而不会显示四个角。

若要将位图分割成这九个矩形，只需在中心指定矩形。 下面是方法的语法 `DrawBitmapNinePatch` ：

```csharp
canvas.DrawBitmapNinePatch(bitmap, centerRectangle, destRectangle, paint);
```

中心矩形相对于位图。 它是 `SKRectI`) 的整数版本 (的值 `SKRect` ，并且所有坐标和大小都以像素为单位。 目标矩形相对于显示图面。 `paint` 参数是可选的。

[**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)示例中的 "**九修补程序显示**" 页首先使用静态构造函数来创建类型的公共静态属性 `SKBitmap` ：

```csharp
public partial class NinePatchDisplayPage : ContentPage
{
    static NinePatchDisplayPage()
    {
        using (SKCanvas canvas = new SKCanvas(FiveByFiveBitmap))
        using (SKPaint paint = new SKPaint
        {
            Style = SKPaintStyle.Stroke,
            Color = SKColors.Red,
            StrokeWidth = 10
        })
        {
            for (int x = 50; x < 500; x += 100)
                for (int y = 50; y < 500; y += 100)
                {
                    canvas.DrawCircle(x, y, 40, paint);
                }
        }
    }

    public static SKBitmap FiveByFiveBitmap { get; } = new SKBitmap(500, 500);
    ···
}
```

本文中的两个其他页使用同一位图。 位图为500像素正方形，由25个圆圈组成，每个圆形大小均为100像素的正方形面积：

![圆圈网格](segmented-images/CircleGrid.png "圆圈网格")

该程序的实例构造函数创建一个 `SKCanvasView` 带有 `PaintSurface` 处理程序的，该处理程序使用 `DrawBitmapNinePatch` 将位图拉伸到其整个显示图面：

```csharp
public class NinePatchDisplayPage : ContentPage
{
    ···
    public NinePatchDisplayPage()
    {
        Title = "Nine-Patch Display";

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

        SKRectI centerRect = new SKRectI(100, 100, 400, 400);
        canvas.DrawBitmapNinePatch(FiveByFiveBitmap, centerRect, info.Rect);
    }
}
```

该 `centerRect` 矩形包含16个圆圈的中心数组。 角中的圆以像素尺寸显示，并相应地拉伸所有其他内容：

[![九显示修补程序](segmented-images/NinePatchDisplay.png "九显示修补程序")](segmented-images/NinePatchDisplay-Large.png#lightbox)

UWP 页的宽度为500像素，因此以一系列大小相同的圆显示顶行和后行。 否则，不在拐角内的所有圆圈都将拉伸以构成椭圆。

对于包含圆圈和省略号组合的对象，可尝试定义中心矩形，使其与圆圈的行和列重叠：

```csharp
SKRectI centerRect = new SKRectI(150, 150, 350, 350);
```

## <a name="the-lattice-display"></a>点阵显示

这两种 `DrawBitmapLattice` 方法类似于 `DrawBitmapNinePatch` ，但它们已通用化为任意数量的水平或垂直除法。 这些划分由对应于像素的整数数组定义。 

此 [`DrawBitmapLattice`](xref:SkiaSharp.SKCanvas.DrawBitmapLattice(SkiaSharp.SKBitmap,System.Int32[],System.Int32[],SkiaSharp.SKRect,SkiaSharp.SKPaint)) 整数数组的带参数的方法似乎不起作用。 [`DrawBitmapLattice`](xref:SkiaSharp.SKCanvas.DrawBitmapLattice(SkiaSharp.SKBitmap,SkiaSharp.SKLattice,SkiaSharp.SKRect,SkiaSharp.SKPaint))带有类型的参数的方法 `SKLattice` 确实有效，这是在下面所示的示例中使用的方法。

[`SKLattice`](xref:SkiaSharp.SKLattice)结构定义四个属性：

- [`XDivs`](xref:SkiaSharp.SKLattice.XDivs)，整数数组
- [`YDivs`](xref:SkiaSharp.SKLattice.YDivs)，整数数组
- [`Flags`](xref:SkiaSharp.SKLattice.Flags)，数组 `SKLatticeFlags` ，枚举类型
- [`Bounds`](xref:SkiaSharp.SKLattice.Bounds)`Nullable<SKRectI>`指定位图中的可选源矩形的类型为的

该 `XDivs` 数组将位图的宽度分成竖线。 第一个条带从左侧的像素0向扩展 `XDivs[0]` 。 此带以像素宽度呈现。 第二个块将从扩展 `XDivs[0]` 到 `XDivs[1]` ，并进行延伸。 第三个块从扩展 `XDivs[1]` 到 `XDivs[2]` ，并以其像素宽度呈现。 最后一个块从数组的最后一个元素到位图的右边缘进行扩展。 如果数组有偶数个元素，则它将以其像素宽度显示。 否则，它会被拉伸。 垂直条带总数比数组中的元素数多一个。

`YDivs`数组类似。 它将数组的高度划分为水平条带。

同时， `XDivs` 和 `YDivs` 数组将位图拆分为矩形。 矩形的数目等于水平条纹数和垂直条纹数的乘积。

根据 Skia 文档，该 `Flags` 数组包含一个用于每个矩形的元素，首先是矩形的首行，然后是第二行，依此类推。 `Flags`数组的类型为 [`SKLatticeFlags`](xref:SkiaSharp.SKLatticeFlags) ，具有以下成员的枚举：

- `Default` 值为0
- `Transparent` 值为1

不过，这些标志看起来不像期望的那样工作，因此最好忽略这些标志。 但不要将 `Flags` 属性设置为 `null` 。 将其设置为一个 `SKLatticeFlags` 足够大的值数组，使其包含矩形的总数量。

**点阵9修补程序**页使用 `DrawBitmapLattice` 来模拟 `DrawBitmapNinePatch` 。 它使用在中创建的相同位图 `NinePatchDisplayPage` ：

```csharp
public class LatticeNinePatchPage : ContentPage
{
    SKBitmap bitmap = NinePatchDisplayPage.FiveByFiveBitmap;

    public LatticeNinePatchPage ()
    {
        Title = "Lattice Nine-Patch";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }
    `
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        SKLattice lattice = new SKLattice();
        lattice.XDivs = new int[] { 100, 400 };
        lattice.YDivs = new int[] { 100, 400 };
        lattice.Flags = new SKLatticeFlags[9]; 

        canvas.DrawBitmapLattice(bitmap, lattice, info.Rect);
    }
}
```

`XDivs`和 `YDivs` 属性都设置为仅包含两个整数的数组，在水平和垂直方向上分别将位图拆分为三个分界线：从像素0到像素 100 (在像素大小) 呈现，从像素100到像素 400 (拉伸) ，从像素400到像素 500 (像素大小) 。 同时 `XDivs` `YDivs` 定义总计9个矩形，即数组的大小 `Flags` 。 只需创建数组即可创建 `SKLatticeFlags.Default` 值数组。

此显示与以前的程序相同：

[![点阵 9-修补程序](segmented-images/LatticeNinePatch.png "点阵 9-修补程序")](segmented-images/LatticeNinePatch-Large.png#lightbox)

**点阵显示**页将位图分成16个矩形：

```csharp
public class LatticeDisplayPage : ContentPage
{
    SKBitmap bitmap = NinePatchDisplayPage.FiveByFiveBitmap;

    public LatticeDisplayPage()
    {
        Title = "Lattice Display";

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

        SKLattice lattice = new SKLattice();
        lattice.XDivs = new int[] { 100, 200, 400 };
        lattice.YDivs = new int[] { 100, 300, 400 };

        int count = (lattice.XDivs.Length + 1) * (lattice.YDivs.Length + 1);
        lattice.Flags = new SKLatticeFlags[count];

        canvas.DrawBitmapLattice(bitmap, lattice, info.Rect);
    }
}
```

`XDivs`和 `YDivs` 数组稍有不同，导致显示与前面的示例不完全相同：

[![点阵显示](segmented-images/LatticeDisplay.png "点阵显示")](segmented-images/LatticeDisplay-Large.png#lightbox)

在左侧的 iOS 和 Android 映像中，只有较小的圆圈以其像素大小呈现。 其余所有内容都将被拉伸。

**点阵显示**页通用化创建 `Flags` 数组，使您可以 `XDivs` `YDivs` 更轻松地进行试验。 特别是，您将需要查看将或数组的第一个元素设置为0时所发生的情况 `XDivs` `YDivs` 。 

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)