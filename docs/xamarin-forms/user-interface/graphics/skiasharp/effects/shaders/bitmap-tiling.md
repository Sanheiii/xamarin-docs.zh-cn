---
title: SkiaSharp 位图平铺
description: 使用以水平和垂直方式重复的位图平铺区域。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 9ED14E07-4DC8-4B03-8A33-772838BF51EA
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8c6d139e47974247ce4af6bfa6c32331fcf7c824
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563128"
---
# <a name="skiasharp-bitmap-tiling"></a>SkiaSharp 位图平铺

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/catclock)

正如您在前面的两篇文章中看到的那样， [`SKShader`](xref:SkiaSharp.SKShader) 类可以创建线性渐变或循环渐变。 本文重点介绍 `SKShader` 使用位图平铺区域的对象。 可以在其原始方向上水平和垂直方向地重复该位图，或者水平和垂直翻转。 翻转避免了磁贴间的中断：

![位图平铺示例](bitmap-tiling-images/BitmapTilingSample.png "位图平铺示例")

[`SKShader.CreateBitmap`](xref:SkiaSharp.SKShader.CreateBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKShaderTileMode,SkiaSharp.SKShaderTileMode))创建此着色器的静态方法具有一个 `SKBitmap` 参数和一个枚举的两个成员 [`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode) ：

```csharp
public static SKShader CreateBitmap (SKBitmap src, SKShaderTileMode tmx, SKShaderTileMode tmy)
```

这两个参数指示用于水平平铺和垂直平铺的模式。 这 `SKShaderTileMode` 也是与渐变方法一起使用的相同枚举。

[`CreateBitmap`](xref:SkiaSharp.SKShader.CreateBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKShaderTileMode,SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix))重载包含用于对 `SKMatrix` 平铺位图执行转换的参数：

```csharp
public static SKShader CreateBitmap (SKBitmap src, SKShaderTileMode tmx, SKShaderTileMode tmy, SKMatrix localMatrix)
```

本文包含将此矩阵转换用于平铺位图的几个示例。

## <a name="exploring-the-tile-modes"></a>探索磁贴模式

[**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)示例的 "着色器"**和 "其他效果**" 页的 "**位图平铺**" 部分中的第一个程序演示了这两个参数的效果 `SKShaderTileMode` 。 **Bitmap 磁贴翻转模式**XAML 文件实例化一个 `SKCanvasView` 和两个 `Picker` 视图，使用这些视图可以为 `SKShaderTilerMode` 水平平铺和垂直平铺选择值。 请注意，在 `SKShaderTileMode` 部分中定义了成员的数组 `Resources` ：

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.BitmapTileFlipModesPage"
             Title="Bitmap Tile Flip Modes">

    <ContentPage.Resources>
        <x:Array x:Key="tileModes"
                 Type="{x:Type skia:SKShaderTileMode}">
            <x:Static Member="skia:SKShaderTileMode.Clamp" />
            <x:Static Member="skia:SKShaderTileMode.Repeat" />
            <x:Static Member="skia:SKShaderTileMode.Mirror" />
        </x:Array>
    </ContentPage.Resources>

    <StackLayout>
        <skiaforms:SKCanvasView x:Name="canvasView"
                                VerticalOptions="FillAndExpand"
                                PaintSurface="OnCanvasViewPaintSurface" />

        <Picker x:Name="xModePicker"
                Title="Tile X Mode"
                Margin="10, 0"
                ItemsSource="{StaticResource tileModes}"
                SelectedIndex="0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged" />

        <Picker x:Name="yModePicker"
                Title="Tile Y Mode"
                Margin="10, 10"
                ItemsSource="{StaticResource tileModes}"
                SelectedIndex="0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged" />

    </StackLayout>
</ContentPage>
```

代码隐藏文件的构造函数在位图资源中加载，该资源显示了一个的猴子。 它首先使用的方法裁剪图像， [`ExtractSubset`](xref:SkiaSharp.SKBitmap.ExtractSubset(SkiaSharp.SKBitmap,SkiaSharp.SKRectI)) `SKBitmap` 使打印头和脚接触到位图的边缘。 然后，构造函数使用 [`Resize`](xref:SkiaSharp.SKBitmap.Resize(SkiaSharp.SKImageInfo,SkiaSharp.SKBitmapResizeMethod)) 方法创建另一个大小为一半的位图。 这些更改使位图更适用于平铺：

```csharp
public partial class BitmapTileFlipModesPage : ContentPage
{
    SKBitmap bitmap;

    public BitmapTileFlipModesPage ()
    {
        InitializeComponent ();

        SKBitmap origBitmap = BitmapExtensions.LoadBitmapResource(
            GetType(), "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

        // Define cropping rect
        SKRectI cropRect = new SKRectI(5, 27, 296, 260);

        // Get the cropped bitmap
        SKBitmap croppedBitmap = new SKBitmap(cropRect.Width, cropRect.Height);
        origBitmap.ExtractSubset(croppedBitmap, cropRect);

        // Resize to half the width and height
        SKImageInfo info = new SKImageInfo(cropRect.Width / 2, cropRect.Height / 2);
        bitmap = croppedBitmap.Resize(info, SKBitmapResizeMethod.Box);
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

        // Get tile modes from Pickers
        SKShaderTileMode xTileMode =
            (SKShaderTileMode)(xModePicker.SelectedIndex == -1 ?
                                        0 : xModePicker.SelectedItem);
        SKShaderTileMode yTileMode =
            (SKShaderTileMode)(yModePicker.SelectedIndex == -1 ?
                                        0 : yModePicker.SelectedItem);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateBitmap(bitmap, xTileMode, yTileMode);
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

`PaintSurface`处理程序 `SKShaderTileMode` 从两个视图中获取设置 `Picker` ，并 `SKShader` 基于位图和这两个值创建对象。 此着色器用于填充画布：

[![位图磁贴翻转模式](bitmap-tiling-images/BitmapTileFlipModes.png "位图磁贴翻转模式")](bitmap-tiling-images/BitmapTileFlipModes-Large.png#lightbox)

左侧的 iOS 屏幕显示默认值的效果 `SKShaderTileMode.Clamp` 。 位图位于左上角。 在位图下方，像素的下边缘一直重复。 在位图的右侧，最右侧的像素列将重复出现。 画布的其余部分将按位图右下角的深棕色像素着色。 很明显，此 `Clamp` 选项几乎不会与位图平铺一起使用！

此中心中的 Android 屏幕显示 `SKShaderTileMode.Repeat` 两个参数的结果。 磁贴会水平和垂直地重复。 显示通用 Windows 平台屏幕 `SKShaderTileMode.Mirror` 。 磁贴会重复出现，但可以水平和垂直翻转。 此选项的优点是磁贴之间没有任何中断。

请记住，您可以对水平和垂直重复使用不同的选项。 可以将指定为 `SKShaderTileMode.Mirror` 第二个参数， `CreateBitmap` 但 `SKShaderTileMode.Repeat` 作为第三个参数。 在每个行上，猴仍会在普通图像和镜像映像之间交替，但不会将任何猴倒置。

## <a name="patterned-backgrounds"></a>带图案背景

位图平铺通常用于从相对较小的位图创建带图案的背景。 典型示例是程序块墙。

" **算法程序块墙** " 页创建一个小位图，该位图类似于整个程序块和由实体分隔的两部分程序块。 因为在下一个示例中也使用了此程序块，所以它是通过静态构造函数创建的，并使用静态属性公开：

```csharp
public class AlgorithmicBrickWallPage : ContentPage
{
    static AlgorithmicBrickWallPage()
    {
        const int brickWidth = 64;
        const int brickHeight = 24;
        const int morterThickness = 6;
        const int bitmapWidth = brickWidth + morterThickness;
        const int bitmapHeight = 2 * (brickHeight + morterThickness);

        SKBitmap bitmap = new SKBitmap(bitmapWidth, bitmapHeight);

        using (SKCanvas canvas = new SKCanvas(bitmap))
        using (SKPaint brickPaint = new SKPaint())
        {
            brickPaint.Color = new SKColor(0xB2, 0x22, 0x22);

            canvas.Clear(new SKColor(0xF0, 0xEA, 0xD6));
            canvas.DrawRect(new SKRect(morterThickness / 2,
                                       morterThickness / 2,
                                       morterThickness / 2 + brickWidth,
                                       morterThickness / 2 + brickHeight),
                                       brickPaint);

            int ySecondBrick = 3 * morterThickness / 2 + brickHeight;

            canvas.DrawRect(new SKRect(0,
                                       ySecondBrick,
                                       bitmapWidth / 2 - morterThickness / 2,
                                       ySecondBrick + brickHeight),
                                       brickPaint);

            canvas.DrawRect(new SKRect(bitmapWidth / 2 + morterThickness / 2,
                                       ySecondBrick,
                                       bitmapWidth,
                                       ySecondBrick + brickHeight),
                                       brickPaint);
        }

        // Save as public property for other programs
        BrickWallTile = bitmap;
    }

    public static SKBitmap BrickWallTile { private set; get; }
    ···
}
```

生成的位图的宽度为70像素，高60像素：

![算法砖墙磁贴](bitmap-tiling-images/AlgorithmicBrickWallTile.png "算法砖墙磁贴")

" **算法程序块背景** " 页的其余部分将创建一个 `SKShader` 对象，该对象在水平和垂直方向上重复此图像：

```csharp
public class AlgorithmicBrickWallPage : ContentPage
{
    ···
    public AlgorithmicBrickWallPage ()
    {
        Title = "Algorithmic Brick Wall";

        // Create SKCanvasView
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
            // Create bitmap tiling
            paint.Shader = SKShader.CreateBitmap(BrickWallTile,
                                                 SKShaderTileMode.Repeat,
                                                 SKShaderTileMode.Repeat);
            // Draw background
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

结果如下：

[![算法程序块墙](bitmap-tiling-images/AlgorithmicBrickWall.png "算法程序块墙")](bitmap-tiling-images/AlgorithmicBrickWall-Large.png#lightbox)

您可能更喜欢一些更现实的内容。 在这种情况下，可以拍摄实际砖墙的照片，然后裁剪。 此位图为300像素宽，150像素高：

![砖墙磁贴](bitmap-tiling-images/BrickWallTile.jpg "砖墙磁贴")

此位图用于 " **摄影砖墙墙壁** " 页：

```csharp
public class PhotographicBrickWallPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(PhotographicBrickWallPage),
                        "SkiaSharpFormsDemos.Media.BrickWallTile.jpg");

    public PhotographicBrickWallPage()
    {
        Title = "Photographic Brick Wall";

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
            // Create bitmap tiling
            paint.Shader = SKShader.CreateBitmap(bitmap,
                                                 SKShaderTileMode.Mirror,
                                                 SKShaderTileMode.Mirror);
            // Draw background
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

请注意，的 `SKShaderTileMode` 参数 `CreateBitmap` 均为 `Mirror` 。 使用从实际图像创建的磁贴时，通常需要使用此选项。 镜像磁贴可避免中断：

[![照相砖墙墙壁](bitmap-tiling-images/PhotographicBrickWall.png "照相砖墙墙壁")](bitmap-tiling-images/PhotographicBrickWall-Large.png#lightbox)

需要执行一些工作才能获取磁贴的合适位图。 这一问题不太好，因为较暗的程序块太多了。 它会定期出现在重复的图像中，揭示此砖墙墙壁是从较小的位图构造的这一事实。

[**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)示例的**Media**文件夹还包括石头墙壁的图像：

![石头墙壁磁贴](bitmap-tiling-images/StoneWallTile.jpg "石头墙壁磁贴")

但是，图块的原始位图有点太大。 可以调整它的大小，但 `SKShader.CreateBitmap` 方法还可以通过向其应用转换来调整磁贴的大小。 此选项在 " **石头墙壁** " 页中演示：

```csharp
public class StoneWallPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(StoneWallPage),
                        "SkiaSharpFormsDemos.Media.StoneWallTile.jpg");

    public StoneWallPage()
    {
        Title = "Stone Wall";

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
            // Create scale transform
            SKMatrix matrix = SKMatrix.MakeScale(0.5f, 0.5f);

            // Create bitmap tiling
            paint.Shader = SKShader.CreateBitmap(bitmap,
                                                 SKShaderTileMode.Mirror,
                                                 SKShaderTileMode.Mirror,
                                                 matrix);
            // Draw background
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

将 `SKMatrix` 创建一个值，以将图像缩放为其原始大小的一半：

[![石头墙壁](bitmap-tiling-images/StoneWall.png "石头墙壁")](bitmap-tiling-images/StoneWall-Large.png#lightbox)

转换是否对方法中使用的原始位图进行操作 `CreateBitmap` ？ 或者它是否转换图块的结果数组？ 

回答此问题的一种简单方法是在转换过程中加入旋转：

```csharp
SKMatrix matrix = SKMatrix.MakeScale(0.5f, 0.5f);
SKMatrix.PostConcat(ref matrix, SKMatrix.MakeRotationDegrees(15));
```

如果将转换应用于单个磁贴，则会旋转磁贴的每个重复图像，结果将包含多个中断。 但在此屏幕截图中，这会明显转换磁贴的复合数组：

[![石头旋转](bitmap-tiling-images/StoneWallRotated.png "石头旋转")](bitmap-tiling-images/StoneWallRotated-Large.png#lightbox)

在部分 [**磁贴对齐**](#tile-alignment)中，你将看到一个应用于着色器的转换转换示例。

独立的 [**Cat 时钟**](/samples/xamarin/xamarin-forms-samples/catclock) 示例 (不是 **SkiaSharpFormsDemos** 的一部分) 使用基于此240像素正方形位图的位图平铺来模拟木头颗粒背景：

![木材颗粒](bitmap-tiling-images/WoodGrain.png "木材颗粒")

这是木材地面的照片。 `SKShaderTileMode.Mirror`选项允许它显示为更大的木材区域：

[![Cat 时钟](bitmap-tiling-images/CatClock.png "Cat 时钟")](bitmap-tiling-images/CatClock-Large.png#lightbox)

## <a name="tile-alignment"></a>磁贴对齐

迄今为止显示的所有示例都使用了创建的着色器 `SKShader.CreateBitmap` 来覆盖整个画布。 在大多数情况下，你将使用位图平铺来归档较小的区域，或 (更少) 来填充粗线条的内部。 下面是用于较小矩形的摄影砖墙磁贴：

[![磁贴对齐](bitmap-tiling-images/TileAlignment.png "磁贴对齐")](bitmap-tiling-images/TileAlignment-Large.png#lightbox)

这可能看起来很好，甚至可能不会。 您可能已注意到平铺模式不会从矩形左上角的完整程序块开始。 这是因为着色器与画布对齐，而不是其修饰的图形对象。

修复很简单。 `SKMatrix`基于翻译转换创建值。 转换会有效地将平铺模式移动到要对齐磁贴的左上角的点。 此方法在 " **磁贴对齐** " 页中进行演示，此页面创建了上面显示的未对齐磁贴的图像：

```csharp
public class TileAlignmentPage : ContentPage
{
    bool isAligned;

    public TileAlignmentPage()
    {
        Title = "Tile Alignment";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;

        // Add tap handler
        TapGestureRecognizer tap = new TapGestureRecognizer();
        tap.Tapped += (sender, args) =>
        {
            isAligned ^= true;
            canvasView.InvalidateSurface();
        };
        canvasView.GestureRecognizers.Add(tap);

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
            SKRect rect = new SKRect(info.Width / 7,
                                     info.Height / 7,
                                     6 * info.Width / 7,
                                     6 * info.Height / 7);

            // Get bitmap from other program
            SKBitmap bitmap = AlgorithmicBrickWallPage.BrickWallTile;

            // Create bitmap tiling
            if (!isAligned)
            {
                paint.Shader = SKShader.CreateBitmap(bitmap,
                                                     SKShaderTileMode.Repeat,
                                                     SKShaderTileMode.Repeat);
            }
            else
            {
                SKMatrix matrix = SKMatrix.MakeTranslation(rect.Left, rect.Top);

                paint.Shader = SKShader.CreateBitmap(bitmap,
                                                     SKShaderTileMode.Repeat,
                                                     SKShaderTileMode.Repeat,
                                                     matrix);
            }

            // Draw rectangle
            canvas.DrawRect(rect, paint);
        }
    }
}
```

**磁贴对齐**页面包括 `TapGestureRecognizer` 。 点击或单击屏幕，程序会切换到 `SKShader.CreateBitmap` 带参数的方法 `SKMatrix` 。 此转换会将模式移位，使左上角包含完全块区：

[![点击的磁贴对齐](bitmap-tiling-images/TileAlignmentTapped.png "点击的磁贴对齐")](bitmap-tiling-images/TileAlignmentTapped-Large.png#lightbox)

还可以使用此方法确保平铺位图模式在它绘制的区域内居中。 在 " **居中" 磁贴** 页面中， `PaintSurface` 处理程序将首先计算坐标，就好像它将在画布中心显示单个位图。 然后，它使用这些坐标为创建转换转换 `SKShader.CreateBitmap` 。 此转换将移动整个模式，使磁贴居中：

```csharp
public class CenteredTilesPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(CenteredTilesPage),
                        "SkiaSharpFormsDemos.Media.monkey.png");

    public CenteredTilesPage ()
    {
        Title = "Centered Tiles";

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

        // Find coordinates to center bitmap in canvas...
        float x = (info.Width - bitmap.Width) / 2f;
        float y = (info.Height - bitmap.Height) / 2f;

        using (SKPaint paint = new SKPaint())
        {
            // ... but use them to create a translate transform
            SKMatrix matrix = SKMatrix.MakeTranslation(x, y);
            paint.Shader = SKShader.CreateBitmap(bitmap, 
                                                 SKShaderTileMode.Repeat, 
                                                 SKShaderTileMode.Repeat, 
                                                 matrix);

            // Use that tiled bitmap pattern to fill a circle
            canvas.DrawCircle(info.Rect.MidX, info.Rect.MidY,
                              Math.Min(info.Width, info.Height) / 2,
                              paint);
        }
    }
}
```

`PaintSurface`处理程序通过在画布中心绘制一个圆圈来结束。 当然，其中一个磁贴正好位于圆圈中心，而另一个磁贴以对称模式排列：

[![居中磁贴](bitmap-tiling-images/CenteredTiles.png "居中磁贴")](bitmap-tiling-images/CenteredTiles-Large.png#lightbox)

另一种中心方法实际上有点简单。 不是构建将磁贴置于中心的转换转换，您可以将平铺模式的一角居中。 在 `SKMatrix.MakeTranslation` 调用中，使用画布中心的参数：

```csharp
SKMatrix matrix = SKMatrix.MakeTranslation(info.Rect.MidX, info.Rect.MidY);
```

该模式仍处于居中和对称模式，但中心没有磁贴：

[![居中平铺](bitmap-tiling-images/CenteredTilesAlternate.png "居中平铺")](bitmap-tiling-images/CenteredTilesAlternate-Large.png#lightbox)

## <a name="simplification-through-rotation"></a>通过旋转简化

有时，在方法中使用旋转转换 `SKShader.CreateBitmap` 可以简化位图图块。 尝试为链链接围栏定义磁贴时，这会很明显。 **ChainLinkTile.cs**文件会创建此处显示的磁贴 (带有粉红色背景，以便清晰) ：

![硬链接磁贴](bitmap-tiling-images/HardChainLinkTile.png "硬链接磁贴")

磁贴需要包含两个链接，以便代码将磁贴分成四个象限。 左上象限和右下象限相同，但是它们不完整。 线路有小凹槽，它们必须使用右上角和左下象限中的一些额外绘图来处理。 完成所有这些工作的文件的长度为174行。

创建此磁贴要容易得多：

![更轻松的链链接磁贴](bitmap-tiling-images/EasierChainLinkTile.png "更轻松的链链接磁贴")

如果位图磁贴着色器旋转了90度，则视觉对象几乎相同。

用于创建更简单的链链接磁贴的代码是 **链式链接磁贴** 页的一部分。 构造函数根据运行程序的设备类型确定磁贴大小，然后调用 `CreateChainLinkTile` ，使用线条、路径和渐变着色器在位图上绘制：

```csharp
public class ChainLinkFencePage : ContentPage
{
    ···
    SKBitmap tileBitmap;

    public ChainLinkFencePage ()
    {
        Title = "Chain-Link Fence";

        // Create bitmap for chain-link tiling
        int tileSize = Device.Idiom == TargetIdiom.Desktop ? 64 : 128;
        tileBitmap = CreateChainLinkTile(tileSize);

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    SKBitmap CreateChainLinkTile(int tileSize)
    {
        tileBitmap = new SKBitmap(tileSize, tileSize);
        float wireThickness = tileSize / 12f;

        using (SKCanvas canvas = new SKCanvas(tileBitmap))
        using (SKPaint paint = new SKPaint())
        {
            canvas.Clear();
            paint.Style = SKPaintStyle.Stroke;
            paint.StrokeWidth = wireThickness;
            paint.IsAntialias = true;

            // Draw straight wires first
            paint.Shader = SKShader.CreateLinearGradient(new SKPoint(0, 0),
                                                         new SKPoint(0, tileSize),
                                                         new SKColor[] { SKColors.Silver, SKColors.Black },
                                                         new float[] { 0.4f, 0.6f },
                                                         SKShaderTileMode.Clamp);

            canvas.DrawLine(0, tileSize / 2,
                            tileSize / 2, tileSize / 2 - wireThickness / 2, paint);

            canvas.DrawLine(tileSize, tileSize / 2,
                            tileSize / 2, tileSize / 2 + wireThickness / 2, paint);

            // Draw curved wires
            using (SKPath path = new SKPath())
            {
                path.MoveTo(tileSize / 2, 0);
                path.LineTo(tileSize / 2 - wireThickness / 2, tileSize / 2);
                path.ArcTo(wireThickness / 2, wireThickness / 2,
                           0,
                           SKPathArcSize.Small,
                           SKPathDirection.CounterClockwise,
                           tileSize / 2, tileSize / 2 + wireThickness / 2);

                paint.Shader = SKShader.CreateLinearGradient(new SKPoint(0, 0),
                                                             new SKPoint(0, tileSize),
                                                             new SKColor[] { SKColors.Silver, SKColors.Black },
                                                             null,
                                                             SKShaderTileMode.Clamp);
                canvas.DrawPath(path, paint);

                path.Reset();
                path.MoveTo(tileSize / 2, tileSize);
                path.LineTo(tileSize / 2 + wireThickness / 2, tileSize / 2);
                path.ArcTo(wireThickness / 2, wireThickness / 2,
                           0,
                           SKPathArcSize.Small,
                           SKPathDirection.CounterClockwise,
                           tileSize / 2, tileSize / 2 - wireThickness / 2);

                paint.Shader = SKShader.CreateLinearGradient(new SKPoint(0, 0),
                                                             new SKPoint(0, tileSize),
                                                             new SKColor[] { SKColors.White, SKColors.Silver },
                                                             null,
                                                             SKShaderTileMode.Clamp);
                canvas.DrawPath(path, paint);
            }
            return tileBitmap;
        }
    }
    ···
}
```

除了电缆外，磁贴是透明的，这意味着你可以将其显示在其他内容之上。 程序加载到某个位图资源，将其显示为填充画布，然后将着色器绘制到顶部：

```csharp
public class ChainLinkFencePage : ContentPage
{
    SKBitmap monkeyBitmap = BitmapExtensions.LoadBitmapResource(
        typeof(ChainLinkFencePage), "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");
    ···

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        canvas.DrawBitmap(monkeyBitmap, info.Rect, BitmapStretch.UniformToFill, 
                            BitmapAlignment.Center, BitmapAlignment.Start);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateBitmap(tileBitmap, 
                                                 SKShaderTileMode.Repeat,
                                                 SKShaderTileMode.Repeat,
                                                 SKMatrix.MakeRotationDegrees(45));
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

请注意，着色器旋转了45度，因此其方向类似于实链式链接围栏：

[![链式链路防护](bitmap-tiling-images/ChainLinkFence.png "链式链路防护")](bitmap-tiling-images/ChainLinkFence-Large.png#lightbox)

## <a name="animating-bitmap-tiles"></a>为位图图块制作动画

您可以通过对矩阵转换进行动画处理来动态显示整个位图平铺模式。 您可能希望模式水平或垂直移动。 可以通过基于移位坐标创建转换转换来实现此目的。

还可以在小型位图上进行绘制，或以每秒60的速率来操作位图的像素位。 然后，可以将该位图用于拼贴，整个平铺模式可能会进行动画处理。 

**动画位图磁贴**页面演示了这种方法。 位图被实例化为一个字段，其形式为64像素的正方形。 构造函数调用 `DrawBitmap` 来为其指定初始外观。 如果该 `angle` 字段为零 (就像第一次将该方法称为) 时，该位图包含两行作为 X。线条的长度足以始终到达位图边缘，而不考虑 `angle` 值： 

```csharp
public class AnimatedBitmapTilePage : ContentPage
{
    const int SIZE = 64;

    SKCanvasView canvasView;
    SKBitmap bitmap = new SKBitmap(SIZE, SIZE);
    float angle;
    ···

    public AnimatedBitmapTilePage ()
    {
        Title = "Animated Bitmap Tile";

        // Initialize bitmap prior to animation
        DrawBitmap();

        // Create SKCanvasView 
        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }
    ···
    void DrawBitmap()
    {
        using (SKCanvas canvas = new SKCanvas(bitmap))
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Blue;
            paint.StrokeWidth = SIZE / 8;

            canvas.Clear();
            canvas.Translate(SIZE / 2, SIZE / 2);
            canvas.RotateDegrees(angle);
            canvas.DrawLine(-SIZE, -SIZE, SIZE, SIZE, paint);
            canvas.DrawLine(-SIZE, SIZE, SIZE, -SIZE, paint);
        }
    }
    ···
}
```

动画开销出现在 `OnAppearing` 和 `OnDisappearing` 重写中。 此 `OnTimerTick` 方法 `angle` 每隔10秒将值从0度动画处理到360度，以在位图内旋转 X 图：

```csharp
public class AnimatedBitmapTilePage : ContentPage
{
    ···
    // For animation
    bool isAnimating;
    Stopwatch stopwatch = new Stopwatch();
    ···

    protected override void OnAppearing()
    {
        base.OnAppearing();

        isAnimating = true;
        stopwatch.Start();
        Device.StartTimer(TimeSpan.FromMilliseconds(16), OnTimerTick);
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();

        stopwatch.Stop();
        isAnimating = false;
    }

    bool OnTimerTick()
    {
        const int duration = 10;     // seconds
        angle = (float)(360f * (stopwatch.Elapsed.TotalSeconds % duration) / duration);
        DrawBitmap();
        canvasView.InvalidateSurface();

        return isAnimating;
    }
    ···
}
```

由于 X 图的对称性，这与将 `angle` 值从0度旋转到90度（每2.5 秒）是相同的。

`PaintSurface`处理程序从位图创建着色器，并使用 paint 对象为整个画布上色：

```csharp
public class AnimatedBitmapTilePage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateBitmap(bitmap,
                                                 SKShaderTileMode.Mirror,
                                                 SKShaderTileMode.Mirror);
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

这些 `SKShaderTileMode.Mirror` 选项可确保每个位图中 x 的双臂与相邻位图中的 x 在一起，以创建一个整体动画模式，这看起来比简单动画要复杂得多：

[![动画位图磁贴](bitmap-tiling-images/AnimatedBitmapTile.png "动画位图磁贴")](bitmap-tiling-images/AnimatedBitmapTile-Large.png#lightbox)

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [CatClock (示例) ](/samples/xamarin/xamarin-forms-samples/catclock)