---
title: SkiaSharp 中的三维旋转
description: 本文介绍如何使用非仿射转换在三维空间中旋转2D 对象，并使用示例代码对此进行演示。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: B5894EA0-C415-41F9-93A4-BBF6EC72AFB9
author: davidbritch
ms.author: dabritch
ms.date: 04/14/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9bb2720d60f62c0bac6ab30007e226c362d1c5cd
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563869"
---
# <a name="3d-rotations-in-skiasharp"></a>SkiaSharp 中的三维旋转

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_使用非仿射转换在三维空间中旋转2D 对象。_

非仿射转换的一个常见应用是模拟3D 空间中2D 对象的旋转：

![在3D 空间中旋转的文本字符串](3d-rotation-images/3drotationsexample.png)

此作业涉及使用三维旋转，然后派生执行这些3D 旋转的非仿射 `SKMatrix` 转换。

很难开发此转换， `SKMatrix` 只在两个维度中进行。 当使用三维图形中的 4 x 4 矩阵派生时，该作业就变得更加容易。 SkiaSharp 包括 [`SKMatrix44`](xref:SkiaSharp.SKMatrix44) 类用于实现此目的，但3d 图形中的某些背景是了解3d 旋转和 4 x 4 转换矩阵的必要。

三维坐标系统添加了名为 Z 的第三个轴。从概念上讲，Z 轴与屏幕直角。 三维空间中的坐标点用三个数字表示： (x、y、z) 。 在本文使用的3D 坐标系中，X 的增加值为向右和递增的 Y 值，就像在两个维度中一样。 增大正 Z 值将出现在屏幕上。 原点为左上角，就像在2D 图形中一样。 你可以将屏幕视为 XY 平面，其中 Z 轴与此平面的位置相同。

这称为左侧坐标系统。 如果向右) 方向 (正 X 坐标的方向，并将中间指的 Y 坐标 (向下向下移动) ，则在 Z 坐标增加的方向上，你的拇指将从屏幕延伸。

在三维图形中，转换基于 4 x 4 矩阵。 下面是4四个标识矩阵：

<pre>
|  1  0  0  0  |
|  0  1  0  0  |
|  0  0  1  0  |
|  0  0  0  1  |
</pre>

使用4个 4 x 4 的矩阵时，可以方便地识别包含行号和列号的单元格：

<pre>
|  M11  M12  M13  M14  |
|  M21  M22  M23  M24  |
|  M31  M32  M33  M34  |
|  M41  M42  M43  M44  |
</pre>

但是，SkiaSharp `Matrix44` 类略有不同。 在中设置或获取单个单元格值的唯一方式 `SKMatrix44` 是使用 [`Item`](xref:SkiaSharp.SKMatrix44.Item(System.Int32,System.Int32)) 索引器。 行索引和列索引是从零开始的，而不是从1开始的，并交换行和列。 使用对象中的索引器访问上图中的单元 M14 `[3, 0]` `SKMatrix44` 。

在3D 图形系统中，三维点 (x、y、z) 转换为 1 x 4 的矩阵，以便乘以 4 x 4 的转换矩阵：

<pre>
                 |  M11  M12  M13  M14  |
| x  y  z  1 | × |  M21  M22  M23  M24  | = | x'  y'  z'  w' |
                 |  M31  M32  M33  M34  |
                 |  M41  M42  M43  M44  |
</pre>

与在三个维度中发生的2D 转换类似，3D 变换假设在四个维度中进行。 第四个维度称为 W，而3D 空间则假定存在于4D 空间中，其中 W 坐标等于1。 转换公式如下所示：

`x' = M11·x + M21·y + M31·z + M41`

`y' = M12·x + M22·y + M32·z + M42`

`z' = M13·x + M23·y + M33·z + M43`

`w' = M14·x + M24·y + M34·z + M44`

从转换公式可以看出，单元格 `M11` `M22` `M33` 是 x、y 和 z 方向的缩放因子，且 `M41` `M42` 和 `M43` 是 x、y 和 z 方向的平移因子。

若要将这些坐标转换回三维空间，其中 W 等于1，x "，y" 和 "z" 坐标全部除以 w "：

`x" = x' / w'`

`y" = y' / w'`

`z" = z' / w'`

`w" = w' / w' = 1`

这种按 w 划分的会在三维空间中提供透视。 如果 w 等于1，则不会发生透视。

三维空间的旋转可能非常复杂，但最简单的旋转是围绕 X、Y 和 Z 轴的旋转。 围绕 X 轴旋转角度α是此矩阵：

<pre>
|  1     0       0     0  |
|  0   cos(α)  sin(α)  0  |
|  0  –sin(α)  cos(α)  0  |
|  0     0       0     1  |
</pre>

对于此转换，X 的值将保持不变。 绕 Y 轴旋转将 Y 值保持不变：

<pre>
|  cos(α)  0  –sin(α)  0  |
|    0     1     0     0  |
|  sin(α)  0   cos(α)  0  |
|    0     0     0     1  |
</pre>

围绕 Z 轴的旋转与2D 图形相同：

<pre>
|  cos(α)  sin(α)  0  0  |
| –sin(α)  cos(α)  0  0  |
|    0       0     1  0  |
|    0       0     0  1  |
</pre>

旋转方向是指坐标系统的左右手使用习惯。 这是一种左手系统，因此，如果您将左侧的值向左旋转到 X 轴并向右旋转绕 Y 轴旋转，并向您旋转围绕 Z 轴的旋转，则另一根手指的曲线会指示旋转方向为正角。

`SKMatrix44` 具有通用 [`CreateRotation`](xref:SkiaSharp.SKMatrix44.CreateRotation(System.Single,System.Single,System.Single,System.Single)) 的静态 [`CreateRotationDegrees`](xref:SkiaSharp.SKMatrix44.CreateRotationDegrees(System.Single,System.Single,System.Single,System.Single)) 方法和方法，使您可以指定旋转发生的轴：

```csharp
public static SKMatrix44 CreateRotationDegrees (Single x, Single y, Single z, Single degrees)
```

若要绕 X 轴旋转，请将前三个参数设置为1，0，0。 若要绕 Y 轴旋转，请将其设置为0、1、0，并绕 Z 轴旋转，将其设置为0，0，1。

4 x 4 的第四列适用于透视。 没有 `SKMatrix44` 用于创建透视转换的方法，但你可以使用以下代码自行创建：

```csharp
SKMatrix44 perspectiveMatrix = SKMatrix44.CreateIdentity();
perspectiveMatrix[3, 2] = -1 / depth;
```

参数名称的原因 `depth` 很快就会变。 该代码将创建矩阵：

<pre>
|  1  0  0      0     |
|  0  1  0      0     |
|  0  0  1  -1/depth  |
|  0  0  0      1     |
</pre>

转换公式导致计算 w "：

`w' = –z / depth + 1`

这适用于以下情况：减小 X 和 Y 坐标，此时 Z 的值 (在概念上小于 XY 平面) 并增加 Z 值 Z 的 X 和 Y 坐标。当 Z 坐标为等于 `depth` ，then w 为零时，坐标变为无限大。 三维图形系统围绕照相机比喻构建， `depth` 此处的值表示相机与坐标系统原点的距离。 如果图形对象的 Z 坐标为 `depth` 从原点开始的单位，则它会在概念上触及相机的镜头，并将会无限大。

请记住，您可能会将此 `perspectiveMatrix` 值与旋转矩阵结合使用。 如果旋转的图形对象的 X 或 Y 坐标大于 `depth` ，则此对象在三维空间中的旋转可能涉及 Z 坐标大于 `depth` 。 必须避免此情况！ 当创建时 `perspectiveMatrix` ， `depth` 无论绘图对象的旋转方式如何，都要将其设置为一个足够大的值。 这可确保永远不会有零除。

结合3D 旋转和透视需要将 4 x 4 个矩阵相乘。 出于此目的， `SKMatrix44` 定义了串联方法。 如果 `A` 和 `B` 是 `SKMatrix44` 对象，则以下代码会将设置为等于 a × B：

```csharp
A.PostConcat(B);
```

当二维图形系统中使用 4 x 4 转换矩阵时，它将应用于2D 对象。 这些对象是平面的，假定 Z 坐标为零。 转换乘法比之前所示的转换简单得多：

<pre>
                 |  M11  M12  M13  M14  |
| x  y  0  1 | × |  M21  M22  M23  M24  | = | x'  y'  z'  w' |
                 |  M31  M32  M33  M34  |
                 |  M41  M42  M43  M44  |
</pre>

对于 z 值，该值为0时，转换的公式不涉及矩阵的第三行中的任何单元格：

x ' = M11 · x + M21 · y + M41

y ' = M12 · x + M22 · y + M42

z "= M13 · x + M23 · y + M43

w ' = M14 · x + M24 · y + M44

此外，z 坐标也不相关。 当三维对象显示在2D 图形系统中时，它通过忽略 Z 坐标值折叠为二维对象。 转换公式实际上只是这两个公式：

`x" = x' / w'`

`y" = y' / w'`

这意味着，可以忽略 4 x 4 矩阵的第三行 *和* 第三列。

但如果是这样，那么为什么第一次只需要4个矩阵的矩阵？

尽管 4 x 4 的第三行和第三列与二维转换无关，但在将各种值相乘后，第三行和第三 *列就会扮演一个* 角色 `SKMatrix44` 。 例如，假设您将围绕 Y 轴的旋转与透视转换相乘：

<pre>
|  cos(α)  0  –sin(α)  0  |   |  1  0  0      0     |   |  cos(α)  0  –sin(α)   sin(α)/depth  |
|    0     1     0     0  | × |  0  1  0      0     | = |    0     1     0           0        |
|  sin(α)  0   cos(α)  0  |   |  0  0  1  -1/depth  |   |  sin(α)  0   cos(α)  -cos(α)/depth  |  
|    0     0     0     1  |   |  0  0  0      1     |   |    0     0     0           1        |
</pre>

在产品中，该单元 `M14` 现在包含一个透视值。 如果要将该矩阵应用于2D 对象，则会取消第三行和列，以将其转换为 3 x 3 矩阵：

<pre>
|  cos(α)  0  sin(α)/depth  |
|    0     1       0        |
|    0     0       1        |
</pre>

现在，它可用于转换2D 点：

<pre>
                |  cos(α)  0  sin(α)/depth  |
|  x  y  1  | × |    0     1       0        | = |  x'  y'  z'  |
                |    0     0       1        |
</pre>

转换公式为：

`x' = cos(α)·x`

`y' = y`

`z' = (sin(α)/depth)·x + 1`

现在，将所有内容除以 z "：

`x" = cos(α)·x / ((sin(α)/depth)·x + 1)`

`y" = y / ((sin(α)/depth)·x + 1)`

当围绕 Y 轴旋转二维对象时，recede 到背景的正值 X 值，而负 X 值将位于前台。 X 值看起来更接近 Y 轴 (它受余弦) 值控制，因为在从查看器进一步或更接近查看器后，从 Y 轴放大的坐标将变小或更大。

使用时 `SKMatrix44` ，通过将各种值相乘来执行所有3d 旋转和透视操作 `SKMatrix44` 。 然后，可以使用类的属性，从 4 x 4 矩阵提取二维 3 x 3 矩阵 [`Matrix`](xref:SkiaSharp.SKMatrix44.Matrix) `SKMatrix44` 。 此属性返回一个熟悉的 `SKMatrix` 值。

**旋转三维**页面使您可以体验3d 旋转。 [**Rotation3DPage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/Rotation3DPage.xaml)文件实例化四个滑块，用于设置围绕 X、Y 和 Z 轴的旋转，并设置一个深度值：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Transforms.Rotation3DPage"
             Title="Rotation 3D">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
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
                    <Setter Property="Margin" Value="20, 0" />
                    <Setter Property="Maximum" Value="360" />
                </Style>
            </ResourceDictionary>
        </Grid.Resources>

        <Slider x:Name="xRotateSlider"
                Grid.Row="0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference xRotateSlider},
                              Path=Value,
                              StringFormat='X-Axis Rotation = {0:F0}'}"
               Grid.Row="1" />

        <Slider x:Name="yRotateSlider"
                Grid.Row="2"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference yRotateSlider},
                              Path=Value,
                              StringFormat='Y-Axis Rotation = {0:F0}'}"
               Grid.Row="3" />

        <Slider x:Name="zRotateSlider"
                Grid.Row="4"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference zRotateSlider},
                              Path=Value,
                              StringFormat='Z-Axis Rotation = {0:F0}'}"
               Grid.Row="5" />

        <Slider x:Name="depthSlider"
                Grid.Row="6"
                Maximum="2500"
                Minimum="250"
                ValueChanged="OnSliderValueChanged" />

        <Label Grid.Row="7"
               Text="{Binding Source={x:Reference depthSlider},
                              Path=Value,
                              StringFormat='Depth = {0:F0}'}" />

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="8"
                           PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

请注意， `depthSlider` 初始化时的 `Minimum` 值为250。 这意味着，此处旋转的2D 对象的 X 和 Y 坐标限制为围绕原点的250像素的半径定义的圆。 此对象在三维空间中的任何旋转都将始终导致坐标值小于250。

[**Rotation3DPage.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/Rotation3DPage.xaml.cs)代码隐藏文件在300像素正方形的位图中加载：

```csharp
public partial class Rotation3DPage : ContentPage
{
    SKBitmap bitmap;

    public Rotation3DPage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        if (canvasView != null)
        {
            canvasView.InvalidateSurface();
        }
    }
    ...
}
```

如果3D 变换在此位图上居中，则 X 和 Y 坐标范围为–150到150之间，而角则为从中心212像素，因此所有内容都在250像素半径内。

`PaintSurface`处理程序根据 `SKMatrix44` 滑块创建对象，并使用将它们相乘 `PostConcat` 。 `SKMatrix`从最后一个对象中提取的值 `SKMatrix44` 会被转换为在屏幕中心旋转旋转，以使旋转居中：

```csharp
public partial class Rotation3DPage : ContentPage
{
    SKBitmap bitmap;

    public Rotation3DPage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        if (canvasView != null)
        {
            canvasView.InvalidateSurface();
        }
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Find center of canvas
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        // Translate center to origin
        SKMatrix matrix = SKMatrix.MakeTranslation(-xCenter, -yCenter);

        // Use 3D matrix for 3D rotations and perspective
        SKMatrix44 matrix44 = SKMatrix44.CreateIdentity();
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(1, 0, 0, (float)xRotateSlider.Value));
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(0, 1, 0, (float)yRotateSlider.Value));
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(0, 0, 1, (float)zRotateSlider.Value));

        SKMatrix44 perspectiveMatrix = SKMatrix44.CreateIdentity();
        perspectiveMatrix[3, 2] = -1 / (float)depthSlider.Value;
        matrix44.PostConcat(perspectiveMatrix);

        // Concatenate with 2D matrix
        SKMatrix.PostConcat(ref matrix, matrix44.Matrix);

        // Translate back to center
        SKMatrix.PostConcat(ref matrix,
            SKMatrix.MakeTranslation(xCenter, yCenter));

        // Set the matrix and display the bitmap
        canvas.SetMatrix(matrix);
        float xBitmap = xCenter - bitmap.Width / 2;
        float yBitmap = yCenter - bitmap.Height / 2;
        canvas.DrawBitmap(bitmap, xBitmap, yBitmap);
    }
}
```

当您试验第四个滑块时，您会注意到不同的深度设置并不从查看器进一步移动对象，而是更改透视效果的范围：

[![旋转三维页面的三向屏幕截图](3d-rotation-images/rotation3d-small.png)](3d-rotation-images/rotation3d-large.png#lightbox "旋转三维页面的三向屏幕截图")

**动画旋转三维**也使用 `SKMatrix44` 对三维空间中的文本字符串进行动画处理。 `textPaint`将对象设置为字段在构造函数中用于确定文本的界限：

```csharp
public class AnimatedRotation3DPage : ContentPage
{
    SKCanvasView canvasView;
    float xRotationDegrees, yRotationDegrees, zRotationDegrees;
    string text = "SkiaSharp";
    SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        TextSize = 100,
        StrokeWidth = 3,
    };
    SKRect textBounds;

    public AnimatedRotation3DPage()
    {
        Title = "Animated Rotation 3D";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Measure the text
        textPaint.MeasureText(text, ref textBounds);
    }
    ...
}
```

`OnAppearing`重写定义了三个 Xamarin.Forms `Animation` 对象 `xRotationDegrees` ，以 `yRotationDegrees` 不同速率对、和字段进行动画处理 `zRotationDegrees` 。 请注意，这些动画的时间段设置为质数 (5 秒、7秒、11秒) 因此，总体组合仅每385秒重复一次，或超过10分钟：

```csharp
public class AnimatedRotation3DPage : ContentPage
{
    ...
    protected override void OnAppearing()
    {
        base.OnAppearing();

        new Animation((value) => xRotationDegrees = 360 * (float)value).
            Commit(this, "xRotationAnimation", length: 5000, repeat: () => true);

        new Animation((value) => yRotationDegrees = 360 * (float)value).
            Commit(this, "yRotationAnimation", length: 7000, repeat: () => true);

        new Animation((value) =>
        {
            zRotationDegrees = 360 * (float)value;
            canvasView.InvalidateSurface();
        }).Commit(this, "zRotationAnimation", length: 11000, repeat: () => true);
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();
        this.AbortAnimation("xRotationAnimation");
        this.AbortAnimation("yRotationAnimation");
        this.AbortAnimation("zRotationAnimation");
    }
    ...
}
```

与上一程序一样， `PaintCanvas` 处理程序将创建 `SKMatrix44` 旋转和透视的值，并将它们相乘：

```csharp
public class AnimatedRotation3DPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Find center of canvas
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        // Translate center to origin
        SKMatrix matrix = SKMatrix.MakeTranslation(-xCenter, -yCenter);

        // Scale so text fits
        float scale = Math.Min(info.Width / textBounds.Width,
                               info.Height / textBounds.Height);
        SKMatrix.PostConcat(ref matrix, SKMatrix.MakeScale(scale, scale));

        // Calculate composite 3D transforms
        float depth = 0.75f * scale * textBounds.Width;

        SKMatrix44 matrix44 = SKMatrix44.CreateIdentity();
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(1, 0, 0, xRotationDegrees));
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(0, 1, 0, yRotationDegrees));
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(0, 0, 1, zRotationDegrees));

        SKMatrix44 perspectiveMatrix = SKMatrix44.CreateIdentity();
        perspectiveMatrix[3, 2] = -1 / depth;
        matrix44.PostConcat(perspectiveMatrix);

        // Concatenate with 2D matrix
        SKMatrix.PostConcat(ref matrix, matrix44.Matrix);

        // Translate back to center
        SKMatrix.PostConcat(ref matrix,
            SKMatrix.MakeTranslation(xCenter, yCenter));

        // Set the matrix and display the text
        canvas.SetMatrix(matrix);
        float xText = xCenter - textBounds.MidX;
        float yText = yCenter - textBounds.MidY;
        canvas.DrawText(text, xText, yText, textPaint);
    }
}
```

此3D 旋转围绕着几个2D 变换，将旋转中心移动到屏幕的中心，并缩放文本字符串的大小，使其宽度与屏幕相同：

[![动画旋转三维页面的三向屏幕截图](3d-rotation-images/animatedrotation3d-small.png)](3d-rotation-images/animatedrotation3d-large.png#lightbox "动画旋转三维页面的三向屏幕截图")

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)