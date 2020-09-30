---
title: 非仿射转换
description: 本文介绍如何在转换矩阵的第三列中创建透视和锥度效果，并通过示例代码对此进行演示。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 785F4D13-7430-492E-B24E-3B45C560E9F1
author: davidbritch
ms.author: dabritch
ms.date: 04/14/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6de5e21c509203c5402ed8c7e75908b54808d140
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556888"
---
# <a name="non-affine-transforms"></a>非仿射转换

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_利用转换矩阵的第三列创建透视和锥度效果_

平移、缩放、旋转和倾斜都分类为 *仿射* 转换。 仿射转换保留了平行线。 如果在转换之前并行两行，它们将在转换后保持平行。 矩形始终转换为 parallelograms。

但是，SkiaSharp 还支持非仿射转换，这种转换可以将矩形转换为任意凸四边形：

![转换为凸四边形的位图](non-affine-images/nonaffinetransformexample.png)

凸四边形是一个四面图形，其中的内部角度始终小于180度，而且没有彼此相交的边。

当转换矩阵的第三行设置为0、0和1以外的值时，非仿射转换结果。 完整的 `SKMatrix` 乘法是：

<pre>
              │ ScaleX  SkewY   Persp0 │
| x  y  1 | × │ SkewX   ScaleY  Persp1 │ = | x'  y'  z' |
              │ TransX  TransY  Persp2 │
</pre>

生成的转换公式为：

x ' = ScaleX · x + SkewX · y + TransX

y ' = SkewY · x + System.windows.media.scaletransform.scaley · y + TransY

z "= Persp0 · x + Persp1 · y + Persp2

对于二维转换，使用 3 x 3 矩阵的基本规则是：所有内容保留在 Z 等于1的平面上。 除非 `Persp0` 和 `Persp1` 是0， `Persp2` 等于1，否则，转换已将 Z 坐标移出该平面。

若要将此还原为二维转换，必须将坐标移回该平面。 需要另一个步骤。 X "，y" 和 "z" 值必须除以 z "：

x "= x"/z

y "= y"/z

z "= z"/z "= 1

这些 *坐标称为同类坐标* ，它们是由 Mathematician 8 月 Ferdinand Möbius 开发的，更好地称为拓扑 Oddity，Möbius 条。

如果 z "为0，则除法运算将导致无限坐标。 事实上，开发同类坐标的 Möbius's 动机之一是能够用有限数值来表示无限值。

但是，在显示图形时，您希望避免呈现转换为无限值的坐标的内容。 不会呈现这些坐标。 这些坐标的邻近范围内的所有内容都将非常大，并且可能不会有明显的连贯。

在此公式中，不希望 z "的值变为零：

z "= Persp0 · x + Persp1 · y + Persp2

因此，这些值有一些切实可行的限制： 

`Persp2`单元格可以为零或不为零。 如果 `Persp2` 为零，则点 (0，0) ，z "为零，这通常不是必需的，因为该点在二维图形中非常常见。 如果不 `Persp2` 等于零，则如果 `Persp2` 固定在1，则不会丢失一般性。 例如，如果您确定 `Persp2` 应该为5，则可以只将矩阵中的所有单元除以5，这 `Persp2` 等于1，结果将相同。

由于这些原因， `Persp2` 通常在1处固定，这与恒等矩阵中的值相同。

通常， `Persp0` 和 `Persp1` 是小数值。 例如，假设您从一个恒等矩阵开始，但将其设置 `Persp0` 为0.01：

<pre>
| 1  0   0.01 |
| 0  1    0   |
| 0  0    1   |
</pre>

转换公式为：

x ' = x/ (0.01 · x + 1) 

y "= y/ (0.01 · x + 1) 

现在使用此转换来呈现位于原点的100像素方框。 下面是四个角的转换方式：

 (0，0) → (0，0) 

 (0，100) → (0，100) 

 (100，0) → (50，0) 

 (100，100) → (50，50) 

当 x 为100时，z 的分母为2，因此 x 和 y 坐标实际上减半。 框的右侧将变短于左侧：

![用于非仿射转换的 box](non-affine-images/nonaffinetransform.png)

`Persp`这些单元格名称的一部分是指 "透视"，因为透视收缩建议该框现在与查看器的右侧倾斜。

使用 " **测试透视** " 页，您可以体验的值 `Persp0` 并对 `Pers1` 其工作方式感到满意。 这些矩阵单元的合理值非常小，因为 `Slider` 通用 Windows 平台无法正确处理它们。 为了容纳 UWP 问题， `Slider` 需要将 [**TestPerspective**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TestPerspectivePage.xaml) 中的两个元素初始化为介于-1 到1之间的范围：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Transforms.TestPerspectivePage"
             Title="Test Perpsective">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Grid.Resources>
            <ResourceDictionary>
                <Style TargetType="Label">
                    <Setter Property="HorizontalTextAlignment" Value="Center" />
                </Style>

                <Style TargetType="Slider">
                    <Setter Property="Minimum" Value="-1" />
                    <Setter Property="Maximum" Value="1" />
                    <Setter Property="Margin" Value="20, 0" />
                </Style>
            </ResourceDictionary>
        </Grid.Resources>

        <Slider x:Name="persp0Slider"
                Grid.Row="0"
                ValueChanged="OnPersp0SliderValueChanged" />

        <Label x:Name="persp0Label"
               Text="Persp0 = 0.0000"
               Grid.Row="1" />

        <Slider x:Name="persp1Slider"
                Grid.Row="2"
                ValueChanged="OnPersp1SliderValueChanged" />

        <Label x:Name="persp1Label"
               Text="Persp1 = 0.0000"
               Grid.Row="3" />

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="4"
                           PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

代码隐藏文件中滑块的事件处理程序将 [`TestPerspectivePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TestPerspectivePage.xaml.cs) 值除以100，使其范围介于-0.01 和0.01 之间。 此外，构造函数在位图中加载：

```csharp
public partial class TestPerspectivePage : ContentPage
{
    SKBitmap bitmap;

    public TestPerspectivePage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }

    void OnPersp0SliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        Slider slider = (Slider)sender;
        persp0Label.Text = String.Format("Persp0 = {0:F4}", slider.Value / 100);
        canvasView.InvalidateSurface();
    }

    void OnPersp1SliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        Slider slider = (Slider)sender;
        persp1Label.Text = String.Format("Persp1 = {0:F4}", slider.Value / 100);
        canvasView.InvalidateSurface();
    }
    ...
}
```

`PaintSurface`处理程序将根据 `SKMatrix` `perspectiveMatrix` 这两个滑杆除以100的值来计算一个名为的值。 这会结合两个转换转换，将此转换的中心置于位图中心：

```csharp
public partial class TestPerspectivePage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Calculate perspective matrix
        SKMatrix perspectiveMatrix = SKMatrix.MakeIdentity();
        perspectiveMatrix.Persp0 = (float)persp0Slider.Value / 100;
        perspectiveMatrix.Persp1 = (float)persp1Slider.Value / 100;

        // Center of screen
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        SKMatrix matrix = SKMatrix.MakeTranslation(-xCenter, -yCenter);
        SKMatrix.PostConcat(ref matrix, perspectiveMatrix);
        SKMatrix.PostConcat(ref matrix, SKMatrix.MakeTranslation(xCenter, yCenter));

        // Coordinates to center bitmap on canvas
        float x = xCenter - bitmap.Width / 2;
        float y = yCenter - bitmap.Height / 2;

        canvas.SetMatrix(matrix);
        canvas.DrawBitmap(bitmap, x, y);
    }
}
```

下面是一些示例图像：

[![测试透视页面的三向屏幕截图](non-affine-images/testperspective-small.png)](non-affine-images/testperspective-large.png#lightbox "测试透视页面的三向屏幕截图")

当您试验滑块时，您会发现值超过0.0066 或更低–0.0066 导致图像突然断开并一致。 正在转换的位图为300像素的正方形。 它相对于其中心进行转换，因此位图范围从–150到150的坐标。 请记住，z "的值为：

z "= Persp0 · x + Persp1 · y + 1

如果 `Persp0` 或 `Persp1` 大于0.0066 或更低–0.0066，则该位图始终有一个坐标，导致 z 值为零。 这会导致被零除，呈现变得混乱。 如果使用非仿射转换，则需要避免使用导致除数为零的坐标呈现任何内容。

通常，不会设置 `Persp0` 和 `Persp1` 隔离。 通常还需要将矩阵中的其他单元格设置为实现某些类型的非仿射转换。

这种非仿射转换是一个 *锥形转换*。 这种类型的非仿射转换将保留矩形的整体尺寸，但 tapers 一侧：

![对锥形转换的 box](non-affine-images/tapertransform.png)

[`TaperTransform`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TaperTransform.cs)类基于这些参数执行非仿射转换的一般化计算：

- 正在转换的图像的矩形大小，
- 一个枚举，指示 tapers 的矩形的边。
- 另一个枚举，该枚举指示它如何 tapers 和
- tapering 的范围。

代码如下：

```csharp
enum TaperSide { Left, Top, Right, Bottom }

enum TaperCorner { LeftOrTop, RightOrBottom, Both }

static class TaperTransform
{
    public static SKMatrix Make(SKSize size, TaperSide taperSide, TaperCorner taperCorner, float taperFraction)
    {
        SKMatrix matrix = SKMatrix.MakeIdentity();

        switch (taperSide)
        {
            case TaperSide.Left:
                matrix.ScaleX = taperFraction;
                matrix.ScaleY = taperFraction;
                matrix.Persp0 = (taperFraction - 1) / size.Width;

                switch (taperCorner)
                {
                    case TaperCorner.RightOrBottom:
                        break;

                    case TaperCorner.LeftOrTop:
                        matrix.SkewY = size.Height * matrix.Persp0;
                        matrix.TransY = size.Height * (1 - taperFraction);
                        break;

                    case TaperCorner.Both:
                        matrix.SkewY = (size.Height / 2) * matrix.Persp0;
                        matrix.TransY = size.Height * (1 - taperFraction) / 2;
                        break;
                }
                break;

            case TaperSide.Top:
                matrix.ScaleX = taperFraction;
                matrix.ScaleY = taperFraction;
                matrix.Persp1 = (taperFraction - 1) / size.Height;

                switch (taperCorner)
                {
                    case TaperCorner.RightOrBottom:
                        break;

                    case TaperCorner.LeftOrTop:
                        matrix.SkewX = size.Width * matrix.Persp1;
                        matrix.TransX = size.Width * (1 - taperFraction);
                        break;

                    case TaperCorner.Both:
                        matrix.SkewX = (size.Width / 2) * matrix.Persp1;
                        matrix.TransX = size.Width * (1 - taperFraction) / 2;
                        break;
                }
                break;

            case TaperSide.Right:
                matrix.ScaleX = 1 / taperFraction;
                matrix.Persp0 = (1 - taperFraction) / (size.Width * taperFraction);

                switch (taperCorner)
                {
                    case TaperCorner.RightOrBottom:
                        break;

                    case TaperCorner.LeftOrTop:
                        matrix.SkewY = size.Height * matrix.Persp0;
                        break;

                    case TaperCorner.Both:
                        matrix.SkewY = (size.Height / 2) * matrix.Persp0;
                        break;
                }
                break;

            case TaperSide.Bottom:
                matrix.ScaleY = 1 / taperFraction;
                matrix.Persp1 = (1 - taperFraction) / (size.Height * taperFraction);

                switch (taperCorner)
                {
                    case TaperCorner.RightOrBottom:
                        break;

                    case TaperCorner.LeftOrTop:
                        matrix.SkewX = size.Width * matrix.Persp1;
                        break;

                    case TaperCorner.Both:
                        matrix.SkewX = (size.Width / 2) * matrix.Persp1;
                        break;
                }
                break;
        }
        return matrix;
    }
}
```

此类在 " **锥度转换** " 页中使用。 XAML 文件实例化两个 `Picker` 元素以选择枚举值，并实例化一个 `Slider` 用于选择锥度分数的元素。 [`PaintSurface`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TaperTransformPage.xaml.cs#L55)处理程序将锥形转换与两个转换转换组合，以使变换相对于位图的左上角：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    TaperSide taperSide = (TaperSide)taperSidePicker.SelectedItem;
    TaperCorner taperCorner = (TaperCorner)taperCornerPicker.SelectedItem;
    float taperFraction = (float)taperFractionSlider.Value;

    SKMatrix taperMatrix =
        TaperTransform.Make(new SKSize(bitmap.Width, bitmap.Height),
                            taperSide, taperCorner, taperFraction);

    // Display the matrix in the lower-right corner
    SKSize matrixSize = matrixDisplay.Measure(taperMatrix);

    matrixDisplay.Paint(canvas, taperMatrix,
        new SKPoint(info.Width - matrixSize.Width,
                    info.Height - matrixSize.Height));

    // Center bitmap on canvas
    float x = (info.Width - bitmap.Width) / 2;
    float y = (info.Height - bitmap.Height) / 2;

    SKMatrix matrix = SKMatrix.MakeTranslation(-x, -y);
    SKMatrix.PostConcat(ref matrix, taperMatrix);
    SKMatrix.PostConcat(ref matrix, SKMatrix.MakeTranslation(x, y));

    canvas.SetMatrix(matrix);
    canvas.DrawBitmap(bitmap, x, y);
}
```

下面是一些示例：

[!["锥度转换" 页的三向屏幕截图](non-affine-images/tapertransform-small.png)](non-affine-images/tapertransform-large.png#lightbox ""锥度转换" 页的三向屏幕截图")

另一种通用化非仿射转换是三维旋转，这在下一篇 [**三维**](3d-rotation.md)旋转中进行了演示。

非仿射转换可以将矩形转换为任意凸四边形。 " **显示非仿射矩阵** " 页演示了这种情况。 它与 "显示[**矩阵转换**](matrix.md)" 一文中的 "**显示仿射矩阵**" 页非常相似，不同之处在于它具有 `TouchPoint` 用于处理位图第四个角的第四个对象：

[!["显示非仿射矩阵" 页的三个屏幕截图](non-affine-images/shownonaffinematrix-small.png)](non-affine-images/shownonaffinematrix-large.png#lightbox ""显示非仿射矩阵" 页的三个屏幕截图")

只要您不尝试使位图的其中一个角的内部角度大于180度，或使两个边彼此交叉，程序就会使用类中的此方法成功计算转换 [`ShowNonAffineMatrixPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowNonAffineMatrixPage.xaml.cs) ：

```csharp
static SKMatrix ComputeMatrix(SKSize size, SKPoint ptUL, SKPoint ptUR, SKPoint ptLL, SKPoint ptLR)
{
    // Scale transform
    SKMatrix S = SKMatrix.MakeScale(1 / size.Width, 1 / size.Height);

    // Affine transform
    SKMatrix A = new SKMatrix
    {
        ScaleX = ptUR.X - ptUL.X,
        SkewY = ptUR.Y - ptUL.Y,
        SkewX = ptLL.X - ptUL.X,
        ScaleY = ptLL.Y - ptUL.Y,
        TransX = ptUL.X,
        TransY = ptUL.Y,
        Persp2 = 1
    };

    // Non-Affine transform
    SKMatrix inverseA;
    A.TryInvert(out inverseA);
    SKPoint abPoint = inverseA.MapPoint(ptLR);
    float a = abPoint.X;
    float b = abPoint.Y;

    float scaleX = a / (a + b - 1);
    float scaleY = b / (a + b - 1);

    SKMatrix N = new SKMatrix
    {
        ScaleX = scaleX,
        ScaleY = scaleY,
        Persp0 = scaleX - 1,
        Persp1 = scaleY - 1,
        Persp2 = 1
    };

    // Multiply S * N * A
    SKMatrix result = SKMatrix.MakeIdentity();
    SKMatrix.PostConcat(ref result, S);
    SKMatrix.PostConcat(ref result, N);
    SKMatrix.PostConcat(ref result, A);

    return result;
}
```

为了便于进行计算，此方法将整体转换获取为三个不同转换的产品，其中之后的箭头显示了这些转换如何修改位图的四个角：

 (0，0) → (0，0) → (0，0) → (x0，y0)  (左上) 

 (0，H) → (0，1) → (0，1) → (x1，y1)  (左下) 

 (W，0) → (1，0) → (1，0) → (x2，y2)  () 

 (W，H) → (1，1) → (a，b) → (x3，y3)  (右下) 

右侧的最终坐标是与四个触控点相关联的四个点。 这是位图拐角的最终坐标。

W 和 H 表示位图的宽度和高度。 第一个转换 `S` 只是将位图缩放为1个像素的正方形。 第二个转换为非仿射转换 `N` ，第三个转换为仿射变换 `A` 。 此仿射转换基于三个点，因此它与早期的仿射方法相同， [`ComputeMatrix`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml.cs#L68) 并且不涉及第四行，其中包含 (a，b) 点。

`a`计算和 `b` 值，以便第三个转换为仿射转换。 此代码获取仿射转换的逆元，然后使用它来映射右下角。 这就是 (a、b) 的要点。

非仿射转换的另一种用法是模拟三维图形。 在下一篇文章中， [**三维旋转**](3d-rotation.md) 可了解如何在三维空间中旋转二维图形。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)