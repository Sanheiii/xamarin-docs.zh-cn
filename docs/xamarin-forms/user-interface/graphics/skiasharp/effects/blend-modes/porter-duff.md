---
title: Porter-Duff blend 模式
description: 使用 Porter-Duff blend 模式来基于源和目标映像编写场景。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 57F172F8-BA03-43EC-A215-ED6B78696BB5
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 12e3e95b0f87d0e93d157bebe057874430866c2b
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91560775"
---
# <a name="porter-duff-blend-modes"></a>Porter-Duff blend 模式

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Duff 混合模式是在 Thomas Porter 和 Tom Duff 之后命名的，他们在处理 Lucasfilm 时开发了代数。 在_计算机图形_7 月1984问题中发布了其纸张[_合成数字图像_](https://graphics.pixar.com/library/Compositing/paper.pdf)，页面253到259。 这些混合模式对于组合非常重要，这是将各种图像组装成复合场景：

![Porter-Duff 示例](porter-duff-images/PorterDuffSample.png "Porter-Duff 示例")

## <a name="porter-duff-concepts"></a>Porter-Duff 概念

假设 brownish 矩形占据显示图面的左侧和顶部三分之二：

![Porter-Duff 目标](porter-duff-images/PorterDuffDst.png "Porter-Duff 目标")

此区域称为 "_目标_" 或 "有时_background_是背景_或背景"。_

要绘制下面的矩形，该矩形的大小与目标相同。 矩形是透明的，只不过 bluish 区域位于右和下三分之二：

![Porter-Duff Source](porter-duff-images/PorterDuffSrc.png "Porter-Duff Source")

这称为 _源_ 或 _前台_。

当你在目标上显示源时，你需要以下内容：

![Porter-Duff Source Over](porter-duff-images/PorterDuffSrcOver.png "Porter-Duff Source Over")

源的透明像素允许背景透过，而 bluish 源像素则遮盖背景。 这是正常情况，在 SkiaSharp 中称为 `SKBlendMode.SrcOver` 。 该值是 `BlendMode` `SKPaint` 第一次实例化对象时属性的默认设置。

但是，可以为不同的效果指定不同的混合模式。 如果指定 `SKBlendMode.DstOver` ，则在源和目标相交的区域中，将显示目标而不是源：

![Porter-Duff Destination](porter-duff-images/PorterDuffDstOver.png "Porter-Duff Destination")

`SKBlendMode.DstIn`Blend 模式仅显示目标和源使用目标颜色相交的区域：

![Porter-Duff Destination In](porter-duff-images/PorterDuffDstIn.png "Porter-Duff Destination In")

混合模式 `SKBlendMode.Xor` (异或) 会导致在两个区域重叠的位置不显示任何内容：

![Porter-Duff Exclusive 或](porter-duff-images/PorterDuffXor.png "Porter-Duff Exclusive 或")

彩色目标和源矩形有效地将显示图面划分为四个唯一的区域，这些区域可以通过与目标和源矩形的存在相对应的各种方式进行着色：

![Porter-Duff](porter-duff-images/PorterDuff.png "Porter-Duff")

右上角和左下方矩形始终为空白，因为在这些区域中，目标和源都是透明的。 目标颜色占用左上角区域，因此区域既可以用目标颜色着色，也可以根本不着色。 同样，源颜色占据右下区域，以便可以将区域与源颜色一起着色或根本不着色。 中间的目标和源的交集可以用目标颜色、源颜色或根本不着色。

组合的总数量为 2 (，适用于中心 (的右下) 倍 3) 的左上) 时间 2 (，即12。 这些是12个基本 Porter Duff 合成模式。

在 _复合数字图像_ (page 256) 的末尾，Porter 和 Duff 将添加一个名为 _plus_ 的第13个模式， (对应于 SKIASHARP `SKBlendMode.Plus` 成员，以及 w3c _浅_ 模式 (，该模式不会与 w3c _淡化_ 模式混淆。 ) 此 `Plus` 模式会添加目标颜色和源颜色，稍后将更详细地介绍此过程。

Skia 添加了一个名为 `Modulate` 的14模式，它非常类似于， `Plus` 但目标颜色和源颜色相乘。 它可以被视为附加的 Porter-Duff 混合模式。

下面是 SkiaSharp 中定义的 14 Porter Duff 模式。 该表显示了如何为上面的关系图中三个非空白区域的每个区域着色：

| 模式       | 目标 | 线 | 源 |
| ---------- |:-----------:|:------------:|:------:|
| `Clear`    |             |              |        |
| `Src`      |             | 源       | X      |
| `Dst`      | X           | 目标  |        |
| `SrcOver`  | X           | 源       | X      |
| `DstOver`  | X           | 目标  | X      |
| `SrcIn`    |             | 源       |        |
| `DstIn`    |             | 目标  |        |
| `SrcOut`   |             |              | X      |
| `DstOut`   | X           |              |        |
| `SrcATop`  | X           | 源       |        |
| `DstATop`  |             | 目标  | X      |
| `Xor`      | X           |              | X      |
| `Plus`     | X           | Sum          | X      |
| `Modulate` |             | 产品      |        | 

这些 blend 模式为对称模式。 可以交换源和目标，并且所有模式仍可用。

模式的命名约定遵循几个简单规则：

- **Src** 或 **Dst** 本身意味着只有源或目标像素可见。
- **Over**后缀指示交集中的可见内容。 将源或目标绘制为 "over"。
- **In**后缀意味着只有交集处于彩色。 输出仅限于源或目标的部分，该部分位于中。
- **Out**后缀表示没有为交集着色。 输出只是位于交集的源或目标的部分。
- 内 **后缀是** **In** 和 **Out**的联合。它包括源或目标为另一位置的 "顶部" 区域。

请注意与和模式之间的差异 `Plus` `Modulate` 。 这些模式在源和目标像素上执行不同类型的计算。 稍后将对其进行更详细的介绍。

**Porter-Duff 网格**页在一个屏幕上以网格的形式显示所有14个模式。 每个模式都是单独的实例 `SKCanvasView` 。 出于此原因，类派生自命名的 `SKCanvasView` `PorterDuffCanvasView` 。 静态构造函数创建两个大小相同的位图，一个位图的左上角区域为 brownish 矩形，另一个具有 bluish 矩形：

```csharp
class PorterDuffCanvasView : SKCanvasView
{
    static SKBitmap srcBitmap, dstBitmap;

    static PorterDuffCanvasView()
    {
        dstBitmap = new SKBitmap(300, 300);
        srcBitmap = new SKBitmap(300, 300);

        using (SKPaint paint = new SKPaint())
        {
            using (SKCanvas canvas = new SKCanvas(dstBitmap))
            {
                canvas.Clear();
                paint.Color = new SKColor(0xC0, 0x80, 0x00);
                canvas.DrawRect(new SKRect(0, 0, 200, 200), paint);
            }
            using (SKCanvas canvas = new SKCanvas(srcBitmap))
            {
                canvas.Clear();
                paint.Color = new SKColor(0x00, 0x80, 0xC0);
                canvas.DrawRect(new SKRect(100, 100, 300, 300), paint);
            }
        }
    }
    ···
}
```

实例构造函数具有类型的参数 `SKBlendMode` 。 它在字段中保存此参数。 

```csharp
class PorterDuffCanvasView : SKCanvasView
{
    ···
    SKBlendMode blendMode;

    public PorterDuffCanvasView(SKBlendMode blendMode)
    {
        this.blendMode = blendMode;
    }

    protected override void OnPaintSurface(SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Find largest square that fits
        float rectSize = Math.Min(info.Width, info.Height);
        float x = (info.Width - rectSize) / 2;
        float y = (info.Height - rectSize) / 2;
        SKRect rect = new SKRect(x, y, x + rectSize, y + rectSize);

        // Draw destination bitmap
        canvas.DrawBitmap(dstBitmap, rect);

        // Draw source bitmap
        using (SKPaint paint = new SKPaint())
        {
            paint.BlendMode = blendMode;
            canvas.DrawBitmap(srcBitmap, rect, paint);
        }

        // Draw outline
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Black;
            paint.StrokeWidth = 2;
            rect.Inflate(-1, -1);
            canvas.DrawRect(rect, paint);
        }
    }
}
```

`OnPaintSurface`重写将绘制两个位图。 首先绘制：

```csharp
canvas.DrawBitmap(dstBitmap, rect);
```

第二个使用对象进行绘制， `SKPaint` 其中， `BlendMode` 属性已设置为构造函数参数：

```csharp
using (SKPaint paint = new SKPaint())
{
    paint.BlendMode = blendMode;
    canvas.DrawBitmap(srcBitmap, rect, paint);
}
```

重写的其余部分在 `OnPaintSurface` 位图周围绘制一个矩形，以指示其大小。

`PorterDuffGridPage`类创建十四个实例 `PorterDurffCanvasView` ，每个实例对应于数组的每个成员 `blendModes` 。 `SKBlendModes`数组中成员的顺序与表稍有不同，以便将相似的模式彼此相邻。 的14个实例 `PorterDuffCanvasView` 与中的标签一起组织在中 `Grid` ：

```csharp
public class PorterDuffGridPage : ContentPage
{
    public PorterDuffGridPage()
    {
        Title = "Porter-Duff Grid";

        SKBlendMode[] blendModes =
        {
            SKBlendMode.Src, SKBlendMode.Dst, SKBlendMode.SrcOver, SKBlendMode.DstOver,
            SKBlendMode.SrcIn, SKBlendMode.DstIn, SKBlendMode.SrcOut, SKBlendMode.DstOut,
            SKBlendMode.SrcATop, SKBlendMode.DstATop, SKBlendMode.Xor, SKBlendMode.Plus,
            SKBlendMode.Modulate, SKBlendMode.Clear
        };

        Grid grid = new Grid
        {
            Margin = new Thickness(5)
        };

        for (int row = 0; row < 4; row++)
        {
            grid.RowDefinitions.Add(new RowDefinition { Height = GridLength.Auto });
            grid.RowDefinitions.Add(new RowDefinition { Height = GridLength.Star });
        }

        for (int col = 0; col < 3; col++)
        {
            grid.ColumnDefinitions.Add(new ColumnDefinition { Width = GridLength.Star });
        }

        for (int i = 0; i < blendModes.Length; i++)
        {
            SKBlendMode blendMode = blendModes[i];
            int row = 2 * (i / 4);
            int col = i % 4;

            Label label = new Label
            {
                Text = blendMode.ToString(),
                HorizontalTextAlignment = TextAlignment.Center
            };
            Grid.SetRow(label, row);
            Grid.SetColumn(label, col);
            grid.Children.Add(label);

            PorterDuffCanvasView canvasView = new PorterDuffCanvasView(blendMode);

            Grid.SetRow(canvasView, row + 1);
            Grid.SetColumn(canvasView, col);
            grid.Children.Add(canvasView);
        }

        Content = grid;
    }
}
```

结果如下：

[![Porter-Duff 网格](porter-duff-images/PorterDuffGrid.png "Porter-Duff 网格")](porter-duff-images/PorterDuffGrid-Large.png#lightbox)

你需要说服，透明度对于正常运行 Porter-Duff blend 模式至关重要。 `PorterDuffCanvasView`类总共包含三次对方法的调用 `Canvas.Clear` 。 所有这些方法都使用无参数方法，该方法将所有像素设置为透明：

```csharp
canvas.Clear();
```

尝试更改这些调用中的任何一种，使像素设置为不透明的白色：

```csharp
canvas.Clear(SKColors.White);
```

完成这项更改后，某些混合模式看起来很正常，但有些则不起作用。 如果将源位图的背景设置为白色，则该 `SrcOver` 模式不起作用，因为在源位图中没有透明像素允许目标显示。 如果将目标位图的背景或画布设置为白色，则 `DstOver` 不起作用，因为目标没有任何透明像素。

可能有一个用来将 **Porter-Duff 网格** 页中的位图替换为更简单的 `DrawRect` 调用。 这适用于目标矩形，但不适用于源矩形。 源矩形必须包含的区域不只是 bluish。 源矩形必须包含与目标的彩色区域相对应的透明区域。 只有这样才会起作用。

## <a name="using-mattes-with-porter-duff"></a>将 Porter 与 Duff 配合使用

" **砖墙合成** " 页显示了一个经典合成任务的示例：需要从多个部分组合图片，包括需要消除背景的位图。 下面是有问题背景的 **SeatedMonkey.jpg** 位图：

![本身的猴子](porter-duff-images/SeatedMonkey.jpg "本身的猴子")

为准备合成，将创建相应的 _遮罩_ ，这是一个黑色的另一个位图，你希望图像显示为黑色，否则为透明。 此文件的名称为 " **SeatedMonkeyMatte.png** "，位于[**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)示例中 "**媒体**" 文件夹的资源中：

![固定的猴子遮罩](porter-duff-images/SeatedMonkeyMatte.png "固定的猴子遮罩")

这并 _不_ 是熟练创建的遮罩。 在最佳情况下，遮罩应在黑色像素边缘周围包含部分透明像素，而此遮罩不应。

程序 **块-墙合成** 页的 XAML 文件实例化一个 `SKCanvasView` 和一个 `Button` ，它将引导用户完成构成最终映像的过程：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.BrickWallCompositingPage"
             Title="Brick-Wall Compositing">

    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Button Text="Show sitting monkey"
                HorizontalOptions="Center"
                Margin="0, 10"
                Clicked="OnButtonClicked" />

    </StackLayout>
</ContentPage>
```

代码隐藏文件加载所需的两个位图，并处理的 `Clicked` 事件 `Button` 。 对于每次 `Button` 单击，该 `step` 字段将递增，并为设置新的 `Text` 属性 `Button` 。 `step`达到5时，它将设置回0：

```csharp
public partial class BrickWallCompositingPage : ContentPage
{
    SKBitmap monkeyBitmap = BitmapExtensions.LoadBitmapResource(
        typeof(BrickWallCompositingPage), 
        "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

    SKBitmap matteBitmap = BitmapExtensions.LoadBitmapResource(
        typeof(BrickWallCompositingPage), 
        "SkiaSharpFormsDemos.Media.SeatedMonkeyMatte.png");

    int step = 0;

    public BrickWallCompositingPage ()
    {
        InitializeComponent ();
    }

    void OnButtonClicked(object sender, EventArgs args)
    {
        Button btn = (Button)sender;
        step = (step + 1) % 5;

        switch (step)
        {
            case 0: btn.Text = "Show sitting monkey"; break;
            case 1: btn.Text = "Draw matte with DstIn"; break;
            case 2: btn.Text = "Draw sidewalk with DstOver"; break;
            case 3: btn.Text = "Draw brick wall with DstOver"; break;
            case 4: btn.Text = "Reset"; break;
        }

        canvasView.InvalidateSurface();
    }
    
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        ···
    }
}
```

程序首次运行时，不会显示任何内容，只是 `Button` ：

[![砖墙合成步骤0](porter-duff-images/BrickWallCompositing0.png "砖墙合成步骤0")](porter-duff-images/BrickWallCompositing0-Large.png#lightbox)

按 `Button` 一次会使 `step` 增量为1， `PaintSurface` 处理程序现在显示 **SeatedMonkey.jpg**：

```csharp
public partial class BrickWallCompositingPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        float x = (info.Width - monkeyBitmap.Width) / 2;
        float y = info.Height - monkeyBitmap.Height;

        // Draw monkey bitmap
        if (step >= 1)
        {
            canvas.DrawBitmap(monkeyBitmap, x, y);
        }
        ···
    }
}
```

没有任何 `SKPaint` 对象，因此没有混合模式。 位图出现在屏幕的底部：

[![砖墙合成步骤1](porter-duff-images/BrickWallCompositing1.png "砖墙合成步骤1")](porter-duff-images/BrickWallCompositing1-Large.png#lightbox)

再次按 `Button` ，并按 `step` 2 递增。 这是显示 **SeatedMonkeyMatte.png** 文件的关键步骤：

```csharp
public partial class BrickWallCompositingPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        // Draw matte to exclude monkey's surroundings
        if (step >= 2)
        {
            using (SKPaint paint = new SKPaint())
            {
                paint.BlendMode = SKBlendMode.DstIn;
                canvas.DrawBitmap(matteBitmap, x, y, paint);
            }
        }
        ···
    }
}
```

混合模式是 `SKBlendMode.DstIn` ，这意味着目标将保留在与源的非透明区域相对应的区域中。 对应于原始位图的目标矩形的剩余部分变为透明：

[![砖墙合成步骤2](porter-duff-images/BrickWallCompositing2.png "砖墙合成步骤2")](porter-duff-images/BrickWallCompositing2-Large.png#lightbox)

已经删除了背景。 

下一步是绘制类似于人行道的矩形。 此人行道的外观基于两个着色器的组合：纯色着色器和 Perlin 杂色着色器：

```csharp
public partial class BrickWallCompositingPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        const float sidewalkHeight = 80;
        SKRect rect = new SKRect(info.Rect.Left, info.Rect.Bottom - sidewalkHeight,
                                 info.Rect.Right, info.Rect.Bottom);

        // Draw gravel sidewalk for monkey to sit on
        if (step >= 3)
        {
            using (SKPaint paint = new SKPaint())
            {
                paint.Shader = SKShader.CreateCompose(
                                    SKShader.CreateColor(SKColors.SandyBrown),
                                    SKShader.CreatePerlinNoiseTurbulence(0.1f, 0.3f, 1, 9));

                paint.BlendMode = SKBlendMode.DstOver;
                canvas.DrawRect(rect, paint);
            }
        }
        ···
    }
}
```

由于此人行道必须位于猴子后面，因此混合模式为 `DstOver` 。 目标仅显示在背景为透明的位置：

[![砖墙合成步骤3](porter-duff-images/BrickWallCompositing3.png "砖墙合成步骤3")](porter-duff-images/BrickWallCompositing3-Large.png#lightbox)

最后一个步骤是添加程序块墙。 该程序使用作为类中的静态属性的程序块墙位图磁贴 `BrickWallTile` `AlgorithmicBrickWallPage` 。 将转换转换添加到对的调用，以将 `SKShader.CreateBitmap` 磁贴移动到最下面的行：

```csharp
public partial class BrickWallCompositingPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        // Draw bitmap tiled brick wall behind monkey
        if (step >= 4)
        {
            using (SKPaint paint = new SKPaint())
            {
                SKBitmap bitmap = AlgorithmicBrickWallPage.BrickWallTile;
                float yAdjust = (info.Height - sidewalkHeight) % bitmap.Height;

                paint.Shader = SKShader.CreateBitmap(bitmap,
                                                     SKShaderTileMode.Repeat,
                                                     SKShaderTileMode.Repeat,
                                                     SKMatrix.MakeTranslation(0, yAdjust));
                paint.BlendMode = SKBlendMode.DstOver;
                canvas.DrawRect(info.Rect, paint);
            }
        }
    }
}
```

为方便起见，该 `DrawRect` 调用在整个画布上显示此着色器，但该 `DstOver` 模式将输出限制为仅在画布上保持透明的区域：

[![砖墙合成步骤4](porter-duff-images/BrickWallCompositing4.png "砖墙合成步骤4")](porter-duff-images/BrickWallCompositing4-Large.png#lightbox)

显而易见，还可以通过其他方式撰写此场景。 它可以从后台开始，开始前进到前台。 但使用混合模式可提供更大的灵活性。 特别是，使用遮罩允许将位图的背景从组合场景中排除。

正如您在 [利用路径和区域进行剪辑](../../curves/clipping.md)中所了解的， `SKCanvas` 类定义了三种类型的剪辑，它们对应于 [`ClipRect`](xref:SkiaSharp.SKCanvas.ClipRect*) 、 [`ClipPath`](xref:SkiaSharp.SKCanvas.ClipPath*) 和 [`ClipRegion`](xref:SkiaSharp.SKCanvas.ClipRegion*) 方法。 Porter Duff 混合模式添加了另一种类型的剪辑，这允许将图像限制为可绘制的任何内容，包括位图。 **砖墙组合**中使用的遮罩实质上定义了剪辑区域。

## <a name="gradient-transparency-and-transitions"></a>梯度透明度和过渡

本文前面所示的 Porter-Duff blend 模式的示例包含包含不透明像素和透明像素的所有涉及的图像，但不包括部分透明的像素。 还会为这些像素定义混合模式函数。 下表是 Porter-Duff 混合模式的更正式定义，该模式使用在 Skia [**SkBlendMode 引用**](https://skia.org/user/api/SkBlendMode_Reference)中找到的表示法。  (，因为 **SkBlendMode 引用** 是 Skia 引用，将使用 c + + 语法。 ) 

从概念上讲，每个像素的红色、绿色、蓝色和 alpha 分量都从字节转换为0到1范围内的浮点数。 对于 alpha 通道，0表示完全透明，1表示完全不透明

下表中的表示法使用以下缩写形式：

- **Da** 是目标 alpha 通道
- **Dc** 是目标 RGB 颜色
- **Sa** 是源 alpha 通道
- **Sc** 是源 RGB 颜色

RGB 颜色按照 alpha 值进行预处理。 例如，如果 **Sc** 表示纯红色但 **Sa** 为0x80，则 RGB 颜色为 ** (0x80，0，0) **。 如果 **Sa** 为0，则所有 RGB 组件也都为零。

结果显示在带 alpha 通道的方括号中，RGB 颜色用逗号分隔： **[alpha，color]**。 对于颜色，为红色、绿色和蓝色分量单独执行计算：

| 模式       | 操作 |
| ---------- | --------- |
| `Clear`    | [0，0]    |
| `Src`      | [Sa，Sc]  |
| `Dst`      | [Da，Dc]  |
| `SrcOver`  | [Sa + Da · (1 – Sa) ，Sc + Dc · (1 – Sa)  | 
| `DstOver`  | [Da + Sa · (1 – Da) ，Dc + Sc · (1 – Da)  |
| `SrcIn`    | 成为Da，Sc ·特里斯坦 |
| `DstIn`    | 特里斯坦Sa，Dc ·成为 |
| `SrcOut`   | [Sa · (1 – Da) ，Sc · (1 – Da) ] |
| `DstOut`   | [Da · (1 – Sa) ，Dc · (1 – Sa) ] |
| `SrcATop`  | [Da，Sc ·Da + Dc · (1 – Sa) ] |
| `DstATop`  | [Sa，Dc ·Sa + Sc · (1 – Da) ] |
| `Xor`      | [Sa + Da –2·成为Da，Sc · (1 – Da) + Dc · (1 – Sa) ] |
| `Plus`     | [Sa + Da，Sc + Dc] |
| `Modulate` | 成为Da，Sc ·台 | 

当 **Da** 和 **Sa** 为0或1时，可以更轻松地分析这些操作。 例如，对于默认 `SrcOver` 模式，如果 **Sa** 为0，则 **Sc** 也是0，结果为 **[Da，Dc]**，目标 alpha 和颜色。 如果 **Sa** 为1，则结果为 **[Sa，Sc]**，源 alpha 和颜色，或者 **[1，Sc]**。

`Plus`和 `Modulate` 模式与其他模式略有不同，因为这种新颜色可以通过源和目标的组合生成。 `Plus`可以通过字节组件或浮点组件来解释此模式。 在前面所示的 **Porter-Duff 网格** 页中，目标颜色为 ** (0xC0，0x80，0x00) ** 并且源颜色为 ** (0x00，0x80，0xC0) **。 每对组件都将被添加，但并不限制。 结果是 ** (0xC0，0xff，0xC0) **的颜色。 这是交集中显示的颜色。

对于 `Modulate` 模式，RGB 值必须转换为浮点值。 目标颜色为 ** (0.75、0.5、0) ** 并且源为 ** (0、0.5、0.75) **。 RGB 组件分别相乘，结果为 ** (0，0.25，0) **。 这是此模式的 **Porter-Duff 网格** 页中的交集显示的颜色。

通过 **Porter-Duff 透明度** 页，可以检查 Porter-Duff blend 模式对部分透明的图形对象的操作方式。 XAML 文件包括 `Picker` 具有 Porter-Duff 模式的：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaviews="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.PorterDuffTransparencyPage"
             Title="Porter-Duff Transparency">

    <StackLayout>
        <skiaviews:SKCanvasView x:Name="canvasView"
                                VerticalOptions="FillAndExpand"
                                PaintSurface="OnCanvasViewPaintSurface" />

        <Picker x:Name="blendModePicker"
                Title="Blend Mode"
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKBlendMode}">
                    <x:Static Member="skia:SKBlendMode.Clear" />
                    <x:Static Member="skia:SKBlendMode.Src" />
                    <x:Static Member="skia:SKBlendMode.Dst" />
                    <x:Static Member="skia:SKBlendMode.SrcOver" />
                    <x:Static Member="skia:SKBlendMode.DstOver" />
                    <x:Static Member="skia:SKBlendMode.SrcIn" />
                    <x:Static Member="skia:SKBlendMode.DstIn" />
                    <x:Static Member="skia:SKBlendMode.SrcOut" />
                    <x:Static Member="skia:SKBlendMode.DstOut" />
                    <x:Static Member="skia:SKBlendMode.SrcATop" />
                    <x:Static Member="skia:SKBlendMode.DstATop" />
                    <x:Static Member="skia:SKBlendMode.Xor" />
                    <x:Static Member="skia:SKBlendMode.Plus" />
                    <x:Static Member="skia:SKBlendMode.Modulate" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                3
            </Picker.SelectedIndex>
        </Picker>
    </StackLayout>
</ContentPage>
```

代码隐藏文件使用线性渐变填充大小相同的两个矩形。 目标渐变是从右上角到左下角的。 它在右上角 brownish，但朝着中心开始淡出透明，在左下角是透明的。 

源矩形具有从左上角到右下角的渐变。 左上角是 bluish 的，但再次淡化为透明，在右下角是透明的。 

```csharp
public partial class PorterDuffTransparencyPage : ContentPage
{
    public PorterDuffTransparencyPage()
    {
        InitializeComponent();
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

        // Make square display rectangle smaller than canvas
        float size = 0.9f * Math.Min(info.Width, info.Height);
        float x = (info.Width - size) / 2;
        float y = (info.Height - size) / 2;
        SKRect rect = new SKRect(x, y, x + size, y + size);

        using (SKPaint paint = new SKPaint())
        {
            // Draw destination
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(rect.Right, rect.Top),
                                new SKPoint(rect.Left, rect.Bottom),
                                new SKColor[] { new SKColor(0xC0, 0x80, 0x00),
                                                new SKColor(0xC0, 0x80, 0x00, 0) },
                                new float[] { 0.4f, 0.6f },
                                SKShaderTileMode.Clamp);

            canvas.DrawRect(rect, paint);

            // Draw source
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(rect.Left, rect.Top),
                                new SKPoint(rect.Right, rect.Bottom),
                                new SKColor[] { new SKColor(0x00, 0x80, 0xC0), 
                                                new SKColor(0x00, 0x80, 0xC0, 0) },
                                new float[] { 0.4f, 0.6f },
                                SKShaderTileMode.Clamp);

            // Get the blend mode from the picker
            paint.BlendMode = blendModePicker.SelectedIndex == -1 ? 0 :
                                    (SKBlendMode)blendModePicker.SelectedItem;

            canvas.DrawRect(rect, paint);

            // Stroke surrounding rectangle
            paint.Shader = null;
            paint.BlendMode = SKBlendMode.SrcOver;
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Black;
            paint.StrokeWidth = 3;
            canvas.DrawRect(rect, paint);
        }
    }
}
```

此程序演示了 Porter-Duff blend 模式可用于除位图以外的图形对象。 但源必须包含透明区域。 这是因为渐变填充了矩形，而渐变的一部分是透明的。

下面是三个示例：

[![Porter-Duff 透明度](porter-duff-images/PorterDuffTransparency.png "Porter-Duff 透明度")](porter-duff-images/PorterDuffTransparency-Large.png#lightbox)

目标和源的配置与原始 Porter-Duff [_合成数字图像_](https://graphics.pixar.com/library/Compositing/paper.pdf) 纸张的第255页中显示的关系图非常相似，但此页说明混合模式对于部分透明度的区域而言是良好的。

可对某些不同效果使用透明渐变。 一种可能是屏蔽，这类似于 " **SkiaSharp 循环渐变" 页**的 "[**遮蔽渐变**](../shaders/circular-gradients.md#radial-gradients-for-masking)" 部分中显示的方法。 很多 **组合掩码** 页与上述程序类似。 它加载位图资源并确定要在其中显示它的矩形。 径向渐变基于预先确定的中心和半径创建：

```csharp
public class CompositingMaskPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
        typeof(CompositingMaskPage),
        "SkiaSharpFormsDemos.Media.MountainClimbers.jpg");

    static readonly SKPoint CENTER = new SKPoint(180, 300);
    static readonly float RADIUS = 120;

    public CompositingMaskPage ()
    {
        Title = "Compositing Mask";

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
                                new SKColor[] { SKColors.Black,
                                                SKColors.Transparent },
                                new float[] { 0.6f, 1 },
                                SKShaderTileMode.Clamp);

            paint.BlendMode = SKBlendMode.DstIn;

            // Display rectangle using that gradient and blend mode
            canvas.DrawRect(rect, paint);
        }

        canvas.DrawColor(SKColors.Pink, SKBlendMode.DstOver);
    }
}
```

此程序的不同之处在于，渐变从中心以黑色开始，以透明度结束。 它显示在混合模式为的位图上 `DstIn` ，仅在不透明的源区域中显示目标。

调用后 `DrawRect` ，画布的整个图面都是透明的，但径向渐变定义的圆圈除外。 进行最后的调用：

```csharp
canvas.DrawColor(SKColors.Pink, SKBlendMode.DstOver);
```

画布的所有透明区域均为粉红色：

[![合成掩码](porter-duff-images/CompositingMask.png "合成掩码")](porter-duff-images/CompositingMask-Large.png#lightbox)

还可以使用 Porter-Duff 模式，并使用部分透明的渐变从一个图像转换到另一个图像。 " **渐变转换** " 页包含一个 `Slider` 指示进度级别（从0到1的转换）和一个， `Picker` 用于选择所需的转换类型：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.GradientTransitionsPage"
             Title="Gradient Transitions">
    
    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="progressSlider"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference progressSlider},
                              Path=Value,
                              StringFormat='Progress = {0:F2}'}"
               HorizontalTextAlignment="Center" />

        <Picker x:Name="transitionPicker" 
                Title="Transition" 
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged" />
        
    </StackLayout>
</ContentPage>
```

代码隐藏文件加载两个位图资源以演示转换。 本文前面的 **位图溶解** 页中使用的两个图像相同。 该代码还定义了一个枚举，其中三个成员对应于三种类型的渐变： &mdash; 线性、径向和扫描。 这些值将加载到中 `Picker` ：

```csharp
public partial class GradientTransitionsPage : ContentPage
{
    SKBitmap bitmap1 = BitmapExtensions.LoadBitmapResource(
        typeof(GradientTransitionsPage),
        "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

    SKBitmap bitmap2 = BitmapExtensions.LoadBitmapResource(
        typeof(GradientTransitionsPage),
        "SkiaSharpFormsDemos.Media.FacePalm.jpg");

    enum TransitionMode
    {
        Linear,
        Radial,
        Sweep
    };

    public GradientTransitionsPage ()
    {
        InitializeComponent ();

        foreach (TransitionMode mode in Enum.GetValues(typeof(TransitionMode)))
        {
            transitionPicker.Items.Add(mode.ToString());
        }

        transitionPicker.SelectedIndex = 0;
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }
    ···
}
```

代码隐藏文件创建三个 `SKPaint` 对象。 `paint0`对象不使用混合模式。 此画图对象用于绘制一个矩形，该矩形具有从黑色到透明的渐变，如数组中所示 `colors` 。 `positions`数组基于的位置 `Slider` ，但在某种程度上进行了调整。 如果 `Slider` 为最小值或最大值，则 `progress` 值为0或1，两个位图中的一个应完全可见。 `positions`必须对这些值设置相应的数组。

如果 `progress` 该值为0，则 `positions` 数组包含值-0.1 和0。 SkiaSharp 会将第一个值调整为等于0，这意味着渐变仅在0时为黑色，否则为透明。 当 `progress` 为0.5 时，数组包含值0.45 和0.55。 渐变是从0到0.45 的黑色，然后转换为透明，从0.55 到1是完全透明的。 如果 `progress` 为1，则 `positions` 数组为1和1.1，这意味着渐变是从0到1的黑色。

`colors`和 `position` 数组同时用于创建渐变的的三个方法 `SKShader` 。 这三个着色器仅根据 `Picker` 所选内容创建： 

```csharp
public partial class GradientTransitionsPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Assume both bitmaps are square for display rectangle
        float size = Math.Min(info.Width, info.Height);
        SKRect rect = SKRect.Create(size, size);
        float x = (info.Width - size) / 2;
        float y = (info.Height - size) / 2;
        rect.Offset(x, y);

        using (SKPaint paint0 = new SKPaint())
        using (SKPaint paint1 = new SKPaint())
        using (SKPaint paint2 = new SKPaint())
        {
            SKColor[] colors = new SKColor[] { SKColors.Black,
                                               SKColors.Transparent };

            float progress = (float)progressSlider.Value;

            float[] positions = new float[]{ 1.1f * progress - 0.1f,
                                             1.1f * progress };

            switch ((TransitionMode)transitionPicker.SelectedIndex)
            {
                case TransitionMode.Linear:
                    paint0.Shader = SKShader.CreateLinearGradient(
                                        new SKPoint(rect.Left, 0),
                                        new SKPoint(rect.Right, 0),
                                        colors,
                                        positions,
                                        SKShaderTileMode.Clamp);
                    break;

                case TransitionMode.Radial:
                    paint0.Shader = SKShader.CreateRadialGradient(
                                        new SKPoint(rect.MidX, rect.MidY),
                                        (float)Math.Sqrt(Math.Pow(rect.Width / 2, 2) +
                                                         Math.Pow(rect.Height / 2, 2)),
                                        colors,
                                        positions,
                                        SKShaderTileMode.Clamp);
                    break;

                case TransitionMode.Sweep:
                    paint0.Shader = SKShader.CreateSweepGradient(
                                        new SKPoint(rect.MidX, rect.MidY),
                                        colors,
                                        positions);
                    break;
            }

            canvas.DrawRect(rect, paint0);

            paint1.BlendMode = SKBlendMode.SrcOut;
            canvas.DrawBitmap(bitmap1, rect, paint1);

            paint2.BlendMode = SKBlendMode.DstOver;
            canvas.DrawBitmap(bitmap2, rect, paint2);
        }
    }
}
```

该渐变显示在没有混合模式的矩形中。 在该 `DrawRect` 调用后，画布只包含从黑色到透明的渐变。 黑色增加时的 `Slider` 值越高。

在处理程序的最后四个语句中 `PaintSurface` ，将显示两个位图。 `SrcOut`混合模式意味着第一个位图只能显示在背景的透明区域中。 `DstOver`第二个位图的模式表示第二个位图仅显示在第一个位图未显示的区域中。

以下屏幕截图显示了三种不同的转换类型，每种类型分别位于50% 标记：

[![梯度转换](porter-duff-images/GradientTransitions.png "梯度转换")](porter-duff-images/GradientTransitions-Large.png#lightbox)

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)