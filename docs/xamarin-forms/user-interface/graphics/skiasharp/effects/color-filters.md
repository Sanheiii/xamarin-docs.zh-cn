---
title: SkiaSharp 颜色筛选器
description: 使用颜色筛选器可使用转换或表转换颜色。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 774E7B55-AEC8-4F12-B657-1C0CEE01AD63
author: davidbritch
ms.author: dabritch
ms.date: 08/28/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 809477fe466ee7a8f0985308896c14341f2dd460
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91561932"
---
# <a name="skiasharp-color-filters"></a>SkiaSharp 颜色筛选器

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

颜色筛选器可以将位图 (或其他图像) 中的颜色转换为其他颜色，如 posterization：

![颜色筛选器示例](color-filters-images/ColorFiltersExample.png "颜色筛选器示例")

若要使用颜色筛选器，请将的 [`ColorFilter`](xref:SkiaSharp.SKPaint.ColorFilter) 属性设置 `SKPaint` 为类型的对象，该对象是 [`SKColorFilter`](xref:SkiaSharp.SKColorFilter) 由该类中的一个静态方法创建的。 本文演示： 

- 使用方法创建的颜色转换 [`CreateColorMatrix`](xref:SkiaSharp.SKColorFilter.CreateColorMatrix*) 。
- 使用方法创建的颜色表 [`CreateTable`](xref:SkiaSharp.SKColorFilter.CreateTable*) 。

## <a name="the-color-transform"></a>颜色转换

颜色转换涉及使用矩阵来修改颜色。 与大多数2D 图形系统一样，SkiaSharp 使用最多的矩阵将坐标点转换为 SkiaSharp 中的文章 [**矩阵转换中**](../transforms/matrix.md)的 iscussed。 [`SKColorFilter`](xref:SkiaSharp.SKColorFilter)还支持矩阵转换，但矩阵转换 RGB 颜色。 若要理解这些颜色转换，一定要熟悉矩阵概念。 

颜色转换矩阵具有四行和5列的维度：

<pre>
| M11 M12 M13 M14 M15 |
| M21 M22 M23 M24 M25 |
| M31 M32 M33 M34 M35 |
| M41 M42 M43 M44 M45 |
</pre>

它将一个 RGB 源颜色转换 (R、G、B、) 到目标颜色 (R '、G '、B ' 和 ' ) 。 

为了准备矩阵乘法，源颜色转换为5×1矩阵：

<pre>
| R |
| G |
| B |
| A |
| 1 |
</pre>

这些 R、G、B 和 A 值是介于0到255之间的原始字节。 它们 _不_ 会规范化为0到1范围内的浮点值。

翻译因素需要额外的单元格。 这类似于使用3×3矩阵转换二维坐标点，如有关使用矩阵转换坐标点一文中的 " [**3 x 3 的原因**](../transforms/matrix.md#the-reason-for-the-3-by-3-matrix) " 一节中所述。

4×5矩阵乘以5×1矩阵，该产品是具有转换后的颜色的4×1矩阵：

<pre>
| M11 M12 M13 M14 M15 |    | R |   | R' |
| M21 M22 M23 M24 M25 |    | G |   | G' |
| M31 M32 M33 M34 M35 |  × | B | = | B' |
| M41 M42 M43 M44 M45 |    | A |   | A' |
                           | 1 |
</pre>

以下是 R '、G '、B ' 和 A 的单独公式：

`R' = M11·R + M12·G + M13·B + M14·A + M15` 

`G' = M21·R + M22·G + M23·B + M24·A + M25` 

`B' = M31·R + M32·G + M33·B + M34·A + M35` 

`A' = M41·R + M42·G + M43·B + M44·A + M45` 

其中的大多数矩阵都包含介于0到2范围内的乘法系数。 不过，最后一列 (M15 到 M45) 包含在公式中添加的值。 这些值通常介于0到255之间。 值介于0到255之间的限制。

标识矩阵为：

<pre>
| 1 0 0 0 0 |
| 0 1 0 0 0 |
| 0 0 1 0 0 |
| 0 0 0 1 0 |
</pre>

这不会对颜色进行任何更改。 转换公式为：

`R' = R` 

`G' = G`

`B' = B`

`A' = A`

M44 单元格非常重要，因为它保留不透明度。 通常情况下，M41、M42 和 M43 均为零，因为您可能不希望不透明度基于红色、绿色和蓝色值。 但如果 M44 为零，则 "将为零，而不会显示任何内容。

颜色矩阵最常见的用途之一是将颜色位图转换为灰色缩放位图。 这涉及到红色、绿色和蓝色值的加权平均值的公式。 对于使用 sRGB ( "标准红色绿色蓝色" ) 颜色空间显示的视频，此公式为：

灰色-底纹 = 0.2126 ·R + 0.7152 ·G + 0.0722 ·B

若要将颜色位图转换为灰色缩放位图，R '、G ' 和 B ' 结果必须等于相同的值。 矩阵为：

<pre>
| 0.21 0.72 0.07 0 0 |
| 0.21 0.72 0.07 0 0 |
| 0.21 0.72 0.07 0 0 |
| 0    0    0    1 0 |
</pre>

没有与此矩阵相对应的 SkiaSharp 数据类型。 相反，您必须将矩阵表示为 `float` 按行顺序排列的20个值的数组：第一行、第二行，依此类推。

静态 [`SKColorFilter.CreateColorMatrix`](xref:SkiaSharp.SKColorFilter.CreateColorMatrix*) 方法具有以下语法：

```csharp
public static SKColorFilter CreateColorMatrix (float[] matrix);
```

其中 `matrix` ，为20个值的数组 `float` 。 在 c # 中创建数组时，可以轻松地设置数字的格式，使其类似于4×5矩阵。 这会在[**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)示例的**灰色刻度矩阵**页中进行演示：

```csharp
public class GrayScaleMatrixPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(CenteredTilesPage),
                        "SkiaSharpFormsDemos.Media.Banana.jpg");

    public GrayScaleMatrixPage()
    {
        Title = "Gray-Scale Matrix";

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
            paint.ColorFilter =
                SKColorFilter.CreateColorMatrix(new float[]
                {
                    0.21f, 0.72f, 0.07f, 0, 0,
                    0.21f, 0.72f, 0.07f, 0, 0,
                    0.21f, 0.72f, 0.07f, 0, 0,
                    0,     0,     0,     1, 0
                });

            canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform, paint: paint);
        }
    }
}
```

`DrawBitmap`此代码中使用的方法来自[**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)示例附带的**BitmapExtension.cs**文件。 

以下是 iOS、Android 和通用 Windows 平台上运行的结果：

[![灰色刻度矩阵](color-filters-images/GrayScaleMatrix.png "灰色刻度矩阵")](color-filters-images/GrayScaleMatrix-Large.png#lightbox)

注意第四行、第四列中的值。 这是与转换后的颜色值的原始颜色值相乘的关键因素。 如果该单元格为零，则不会显示任何内容，并且问题可能难以找到。

当试验颜色矩阵时，可以从源的透视或目标的角度来处理转换。 源的红色像素如何构成目标的红色、绿色和蓝色像素？ 这取决于矩阵第一 _列_ 中的值。 或者，目标红色像素应如何受到源的红色、绿色和蓝色像素的影响？ 这取决于矩阵的第 _一行_ 。

有关如何使用颜色转换的一些建议，请参阅重新 [**着色图像**](/dotnet/framework/winforms/advanced/recoloring-images) 页面。 讨论 Windows 窗体和矩阵的不同格式相同，但概念是相同的。

**蜡笔矩阵**通过 attenuating 源红色像素来计算目标红像素，并稍微强调红色和绿色像素。 对于绿色和蓝色像素，此过程类似：

```csharp
public class PastelMatrixPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(PastelMatrixPage),
                        "SkiaSharpFormsDemos.Media.MountainClimbers.jpg");

    public PastelMatrixPage()
    {
        Title = "Pastel Matrix";

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
            paint.ColorFilter =
                SKColorFilter.CreateColorMatrix(new float[]
                {
                    0.75f, 0.25f, 0.25f, 0, 0,
                    0.25f, 0.75f, 0.25f, 0, 0,
                    0.25f, 0.25f, 0.75f, 0, 0,
                    0, 0, 0, 1, 0
                });

            canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform, paint: paint);
        }
    }
}
```

结果是将颜色强度静音，如此处所示：

[![蜡笔矩阵](color-filters-images/PastelMatrix.png "蜡笔矩阵")](color-filters-images/PastelMatrix-Large.png#lightbox)

## <a name="color-tables"></a>颜色表

静态 [`SKColorFilter.CreateTable`](xref:SkiaSharp.SKColorFilter.CreateTable*) 方法分为两个版本：

```csharp
public static SKColorFilter CreateTable (byte[] table);

public static SKColorFilter CreateTable (byte[] tableA, byte[] tableR, byte[] tableG, byte[] tableB);
```

数组始终包含256项。 在 `CreateTable` 包含一个表的方法中，同一表用于红色、绿色和蓝色分量。 这是一个简单的查找表：如果源颜色是 (R、G、B) ，目标颜色是 (R '，B '，G ' ) ，则使用源组件建立索引会获得目标组件 `table` ：

`R' = table[R]`

`G' = table[G]`

`B' = table[B]`

在第二种方法中，四个颜色组件中的每一个都可以有单独的颜色表，或者在两个或多个组件之间共享相同的颜色表。

若要将第二个方法的参数之一设置为 `CreateTable` 包含序列0到255的颜色表，可以 `null` 改用。 通常， `CreateTable` 调用具有 `null` alpha 通道的第一个参数。

在有关[访问 SkiaSharp 位图像素位](../bitmaps/pixel-bits.md#posterization)的文章**Posterization**中的部分，你已了解如何修改位图的各个像素位以降低其颜色分辨率。 这是一种称为 _posterization_的技术。 

还可以使用颜色表来分离位图。 " **色调分离表** " 页的构造函数创建一个颜色表，将其索引映射到第6位设置为零的字节：

```csharp
public class PosterizeTablePage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(PosterizeTablePage),
                        "SkiaSharpFormsDemos.Media.MonkeyFace.png");

    byte[] colorTable = new byte[256];

    public PosterizeTablePage()
    {
        Title = "Posterize Table";

        // Create color table
        for (int i = 0; i < 256; i++)
        {
            colorTable[i] = (byte)(0xC0 & i);
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

        using (SKPaint paint = new SKPaint())
        {
            paint.ColorFilter =
                SKColorFilter.CreateTable(null, null, colorTable, colorTable);

            canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform, paint: paint);
        }
    }
}
```

程序选择仅为绿色和蓝色通道使用此颜色表。 红色通道继续具有完整的分辨率：

[![分离表](color-filters-images/PosterizeTable.png "分离表")](color-filters-images/PosterizeTable-Large.png#lightbox)

您可以为不同的颜色通道使用各种颜色表来实现各种效果。 

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)