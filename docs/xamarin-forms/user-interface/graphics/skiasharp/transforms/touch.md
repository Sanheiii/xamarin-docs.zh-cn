---
title: 触摸操作
description: 本文介绍如何使用矩阵变换实现触控拖动、收缩和旋转，并使用示例代码对此进行演示。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: A0B8DD2D-7392-4EC5-BFB0-6209407AD650
author: davidbritch
ms.author: dabritch
ms.date: 09/14/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c6d568e948f02952fa71ed21af48160a53bfc419
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556537"
---
# <a name="touch-manipulations"></a>触摸操作

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_使用矩阵变换实现触控拖动、收缩和旋转_

在多点触控环境（如移动设备上的环境）中，用户通常使用其手指操作屏幕上的对象。 常见的手势（如一个指尖拖动和一个双指的挤压）可以移动和缩放对象，甚至可以旋转对象。 这些手势通常是使用转换矩阵实现的，本文介绍了如何执行此操作。

![用于转换、缩放和旋转的位图](touch-images/touchmanipulationsexample.png)

此处显示的所有示例都使用 Xamarin.Forms 在 [**调用来自效果的事件**](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)一文中介绍的触摸跟踪效果。

## <a name="dragging-and-translation"></a>拖动和转换

Matrix 转换的最重要的应用程序之一是触控处理。 单个 [`SKMatrix`](xref:SkiaSharp.SKMatrix) 值可以合并一系列触控操作。 

对于单指拖动， `SKMatrix` 值执行转换。 **位图拖动**页面中演示了这一点。 XAML 文件 `SKCanvasView` 在中实例化 Xamarin.Forms `Grid` 。 已将 `TouchEffect` 对象添加到 `Effects` 的集合中 `Grid` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             xmlns:tt="clr-namespace:TouchTracking"
             x:Class="SkiaSharpFormsDemos.Transforms.BitmapDraggingPage"
             Title="Bitmap Dragging">
    
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

理论 `TouchEffect` 上，可以将对象直接添加到 `Effects` 的集合 `SKCanvasView` ，但这不适用于所有平台。 由于的 `SKCanvasView` 大小与 `Grid` 此配置中的大小相同，因此，将其附加到也是如此 `Grid` 。

代码隐藏文件在其构造函数中加载到位图资源中，并将其显示在 `PaintSurface` 处理程序中：

```csharp
public partial class BitmapDraggingPage : ContentPage
{
    // Bitmap and matrix for display
    SKBitmap bitmap;
    SKMatrix matrix = SKMatrix.MakeIdentity();
    ···

    public BitmapDraggingPage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Display the bitmap
        canvas.SetMatrix(matrix);
        canvas.DrawBitmap(bitmap, new SKPoint());
    }
}
```

如果没有任何其他代码， `SKMatrix` 值始终为 "标识" 矩阵，并且不会影响位图的显示。 `OnTouchEffectAction`XAML 文件中的处理程序集的目标是更改矩阵值，以反映触控操作。

`OnTouchEffectAction`处理程序从将值转换 Xamarin.Forms `Point` 为 SkiaSharp 值开始 `SKPoint` 。 这只是基于 `Width` 与设备无关的单位 (的和属性进行缩放的简单因素， `Height` `SKCanvasView`) 和 `CanvasSize` 属性（以像素为单位）：

```csharp
public partial class BitmapDraggingPage : ContentPage
{
    ···
    // Touch information
    long touchId = -1;
    SKPoint previousPoint;
    ···
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        // Convert Xamarin.Forms point to pixels
        Point pt = args.Location;
        SKPoint point = 
            new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                        (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));

        switch (args.Type)
        {
            case TouchActionType.Pressed:
                // Find transformed bitmap rectangle
                SKRect rect = new SKRect(0, 0, bitmap.Width, bitmap.Height);
                rect = matrix.MapRect(rect);

                // Determine if the touch was within that rectangle
                if (rect.Contains(point))
                {
                    touchId = args.Id;
                    previousPoint = point;
                }
                break;

            case TouchActionType.Moved:
                if (touchId == args.Id)
                {
                    // Adjust the matrix for the new position
                    matrix.TransX += point.X - previousPoint.X;
                    matrix.TransY += point.Y - previousPoint.Y;
                    previousPoint = point;
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                touchId = -1;
                break;
        }
    }
    ···
}
```

当手指首次触摸屏幕时， `TouchActionType.Pressed` 将激发类型为的事件。 第一种任务是确定手指是否接触到位。 此类任务通常称为 _命中测试_。 在这种情况下，可以通过以下方法来完成命中测试：创建 `SKRect` 对应于位图的值，对其应用矩阵转换，然后 `MapRect` 确定触摸点是否位于已转换的矩形内。

如果是这种情况，则 `touchId` 字段将设置为 TOUCH ID，并保存 finger 位置。

对于该 `TouchActionType.Moved` 事件，将根据 `SKMatrix` 手指的当前位置和手指的新位置调整值的转换因子。 新位置将在下一次保存，并使 `SKCanvasView` 失效。

当你尝试使用此程序时，请注意，你只能在手指接触到显示位图的区域时拖动位图。 尽管此限制对于此程序并不重要，但在操作多个位图时，这一点非常重要。

## <a name="pinching-and-scaling"></a>收缩和缩放

如果两根手指接触位图，会发生什么情况？ 如果两根手指并行移动，则可能希望位图与手指一起移动。 如果两根手指执行 "延展" 或 "拉伸" 操作，则您可能希望将位图旋转 (以便在下一部分) 或缩放。 缩放位图时，两根手指在相对于位图的位置保持不变，并相应地缩放位图。

同时处理两个手指看起来很复杂，但请记住， `TouchAction` 处理程序一次只接收一根手指的信息。 如果两根手指正在操作位图，则对于每个事件，一个手指已更改位置，而另一个手指未更改。 在下面的 **位图缩放** 页代码中，未更改位置的手指称为 _透视_ 点，因为转换相对于该点。

此程序与上一个程序之间的一个区别是必须保存多个 touch Id。 字典用于此目的，其中 touch ID 是字典键，字典值是该手指的当前位置：

```csharp
public partial class BitmapScalingPage : ContentPage
{
    ···
    // Touch information
    Dictionary<long, SKPoint> touchDictionary = new Dictionary<long, SKPoint>();
    ···
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        // Convert Xamarin.Forms point to pixels
        Point pt = args.Location;
        SKPoint point =
            new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                        (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));

        switch (args.Type)
        {
            case TouchActionType.Pressed:
                // Find transformed bitmap rectangle
                SKRect rect = new SKRect(0, 0, bitmap.Width, bitmap.Height);
                rect = matrix.MapRect(rect);

                // Determine if the touch was within that rectangle
                if (rect.Contains(point) && !touchDictionary.ContainsKey(args.Id))
                {
                    touchDictionary.Add(args.Id, point);
                }
                break;

            case TouchActionType.Moved:
                if (touchDictionary.ContainsKey(args.Id))
                {
                    // Single-finger drag
                    if (touchDictionary.Count == 1)
                    {
                        SKPoint prevPoint = touchDictionary[args.Id];

                        // Adjust the matrix for the new position
                        matrix.TransX += point.X - prevPoint.X;
                        matrix.TransY += point.Y - prevPoint.Y;
                        canvasView.InvalidateSurface();
                    }
                    // Double-finger scale and drag
                    else if (touchDictionary.Count >= 2)
                    {
                        // Copy two dictionary keys into array
                        long[] keys = new long[touchDictionary.Count];
                        touchDictionary.Keys.CopyTo(keys, 0);

                        // Find index of non-moving (pivot) finger
                        int pivotIndex = (keys[0] == args.Id) ? 1 : 0;

                        // Get the three points involved in the transform
                        SKPoint pivotPoint = touchDictionary[keys[pivotIndex]];
                        SKPoint prevPoint = touchDictionary[args.Id];
                        SKPoint newPoint = point;

                        // Calculate two vectors
                        SKPoint oldVector = prevPoint - pivotPoint;
                        SKPoint newVector = newPoint - pivotPoint;

                        // Scaling factors are ratios of those
                        float scaleX = newVector.X / oldVector.X;
                        float scaleY = newVector.Y / oldVector.Y;

                        if (!float.IsNaN(scaleX) && !float.IsInfinity(scaleX) &&
                            !float.IsNaN(scaleY) && !float.IsInfinity(scaleY))
                        {
                            // If something bad hasn't happened, calculate a scale and translation matrix
                            SKMatrix scaleMatrix = 
                                SKMatrix.MakeScale(scaleX, scaleY, pivotPoint.X, pivotPoint.Y);

                            SKMatrix.PostConcat(ref matrix, scaleMatrix);
                            canvasView.InvalidateSurface();
                        }
                    }

                    // Store the new point in the dictionary
                    touchDictionary[args.Id] = point;
                }

                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                if (touchDictionary.ContainsKey(args.Id))
                {
                    touchDictionary.Remove(args.Id);
                }
                break;
        }
    }
    ···
}
```

操作的处理 `Pressed` 几乎与上一个程序相同，不同之处在于 ID 和触点添加到字典中。 `Released`和 `Cancelled` 操作删除字典项。

`Moved`不过，对操作的处理更为复杂。 如果只涉及一根手指，则处理过程与以前的程序非常相似。 对于两个或多个手指，程序还必须从涉及未移动的手指的字典中获取信息。 它通过将字典键复制到数组中，然后将第一个键与要移动的 finger 的 ID 进行比较来实现此功能。 这允许程序获取与未移动的手指相对应的透视点。

接下来，该程序计算与透视点相关的新手指位置的两个向量，以及相对于该轴点的旧 finger 位置。 这些矢量的比率是缩放系数。 由于可以使用被零除的情况，因此必须检查这些值是否为无限值或 NaN (不是数值) 值。 如果一切正常，将使用 `SKMatrix` 保存为字段的值连接缩放转换。

当你尝试此页时，你会注意到，你可以用一或两根手指拖动位图，或者用两根手指缩放位图。 缩放为 _各向异性_，这意味着在水平和垂直方向上缩放可能会有所不同。 这会扭曲纵横比，还允许您翻转位图以生成镜像图像。 您还可能发现您可以将位图缩小为零维度，并且它将消失。 在生产代码中，您需要防范这种情况。

## <a name="two-finger-rotation"></a>双指旋转

" **位图旋转** " 页允许您使用两根手指进行旋转或 isotropic 缩放。 位图始终保持正确的纵横比。 同时使用两根手指进行旋转和各向异性缩放不会很好地工作，因为这两个任务的手指移动非常类似。

此程序的第一个重要区别是命中测试逻辑。 之前的程序使用的 `Contains` 方法 `SKRect` 来确定触摸点是否在与位图相对应的已转换矩形内。 但当用户对位图进行操作时，位图可能会旋转，并且 `SKRect` 无法正确表示旋转的矩形。 在这种情况下，你可能会担心命中测试逻辑需要实现而不是复杂的分析几何。

但是，可以使用快捷方式：确定某个点是否位于已转换矩形的边界内与确定反转转换的点是否位于未经转换矩形的边界内相同。 这是一种更简单的计算，逻辑可继续使用简单的 `Contains` 方法：

```csharp
public partial class BitmapRotationPage : ContentPage
{
    ···
    // Touch information
    Dictionary<long, SKPoint> touchDictionary = new Dictionary<long, SKPoint>();
    ···
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        // Convert Xamarin.Forms point to pixels
        Point pt = args.Location;
        SKPoint point =
            new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                        (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));

        switch (args.Type)
        {
            case TouchActionType.Pressed:
                if (!touchDictionary.ContainsKey(args.Id))
                {
                    // Invert the matrix
                    if (matrix.TryInvert(out SKMatrix inverseMatrix))
                    {
                        // Transform the point using the inverted matrix
                        SKPoint transformedPoint = inverseMatrix.MapPoint(point);

                        // Check if it's in the untransformed bitmap rectangle
                        SKRect rect = new SKRect(0, 0, bitmap.Width, bitmap.Height);

                        if (rect.Contains(transformedPoint))
                        {
                            touchDictionary.Add(args.Id, point);
                        }
                    }
                }
                break;

            case TouchActionType.Moved:
                if (touchDictionary.ContainsKey(args.Id))
                {
                    // Single-finger drag
                    if (touchDictionary.Count == 1)
                    {
                        SKPoint prevPoint = touchDictionary[args.Id];

                        // Adjust the matrix for the new position
                        matrix.TransX += point.X - prevPoint.X;
                        matrix.TransY += point.Y - prevPoint.Y;
                        canvasView.InvalidateSurface();
                    }
                    // Double-finger rotate, scale, and drag
                    else if (touchDictionary.Count >= 2)
                    {
                        // Copy two dictionary keys into array
                        long[] keys = new long[touchDictionary.Count];
                        touchDictionary.Keys.CopyTo(keys, 0);

                        // Find index non-moving (pivot) finger
                        int pivotIndex = (keys[0] == args.Id) ? 1 : 0;

                        // Get the three points in the transform
                        SKPoint pivotPoint = touchDictionary[keys[pivotIndex]];
                        SKPoint prevPoint = touchDictionary[args.Id];
                        SKPoint newPoint = point;

                        // Calculate two vectors
                        SKPoint oldVector = prevPoint - pivotPoint;
                        SKPoint newVector = newPoint - pivotPoint;

                        // Find angles from pivot point to touch points
                        float oldAngle = (float)Math.Atan2(oldVector.Y, oldVector.X);
                        float newAngle = (float)Math.Atan2(newVector.Y, newVector.X);

                        // Calculate rotation matrix
                        float angle = newAngle - oldAngle;
                        SKMatrix touchMatrix = SKMatrix.MakeRotation(angle, pivotPoint.X, pivotPoint.Y);

                        // Effectively rotate the old vector
                        float magnitudeRatio = Magnitude(oldVector) / Magnitude(newVector);
                        oldVector.X = magnitudeRatio * newVector.X;
                        oldVector.Y = magnitudeRatio * newVector.Y;

                        // Isotropic scaling!
                        float scale = Magnitude(newVector) / Magnitude(oldVector);

                        if (!float.IsNaN(scale) && !float.IsInfinity(scale))
                        {
                            SKMatrix.PostConcat(ref touchMatrix,
                                SKMatrix.MakeScale(scale, scale, pivotPoint.X, pivotPoint.Y));

                            SKMatrix.PostConcat(ref matrix, touchMatrix);
                            canvasView.InvalidateSurface();
                        }
                    }

                    // Store the new point in the dictionary
                    touchDictionary[args.Id] = point;
                }

                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                if (touchDictionary.ContainsKey(args.Id))
                {
                    touchDictionary.Remove(args.Id);
                }
                break;
        }
    }

    float Magnitude(SKPoint point)
    {
        return (float)Math.Sqrt(Math.Pow(point.X, 2) + Math.Pow(point.Y, 2));
    }
    ···
}
```

事件的逻辑 `Moved` 与上一个程序类似。 两个名为和的向量 `oldVector` `newVector` 基于移动 finger 的上一个和当前点，以及 unmoving 手指的中心点。 但会确定这些矢量的角度，差别在于旋转角度。

还可能会涉及缩放，因此将基于旋转角度旋转旧矢量。 现在，两个向量的相对大小为缩放系数。 请注意，相同的 `scale` 值用于水平和垂直缩放，因此缩放是 isotropic 的。 此 `matrix` 字段由旋转矩阵和刻度矩阵进行调整。

如果你的应用程序需要为单个位图实现触控处理 (或其他对象) ，你可以将这三个示例中的代码调整为你自己的应用程序。 但如果需要为多个位图实现触控处理，则可能需要在其他类中封装这些触控操作。

## <a name="encapsulating-the-touch-operations"></a>封装触控操作

**触摸操作**页演示了单个位图的触摸操作，但使用了多个封装以上所示逻辑的其他文件。 其中的第一个文件是 [`TouchManipulationMode`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationMode.cs) 枚举，它指示你将看到的代码实现的不同类型的触摸操作：

```csharp
enum TouchManipulationMode
{
    None,
    PanOnly,
    IsotropicScale,     // includes panning
    AnisotropicScale,   // includes panning
    ScaleRotate,        // implies isotropic scaling
    ScaleDualRotate     // adds one-finger rotation
}
```

`PanOnly` 是使用平移实现的单指拖动。 所有后续选项还包括平移，但涉及两根手指： `IsotropicScale` 是一项挤压操作，使对象在水平和垂直方向上均匀缩放。 `AnisotropicScale` 允许不相等缩放。

`ScaleRotate`选项用于双指缩放和旋转。 缩放是 isotropic 的。 如前文所述，通过各向异性缩放实现双指旋转是有问题的，因为 finger 移动实质上是相同的。

`ScaleDualRotate`选项会添加一根手指旋转。 当单个手指拖动对象时，拖动的对象首先围绕其中心旋转，使对象的中心与拖动向量对齐。

[**TouchManipulationPage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationPage.xaml)文件包含 `Picker` 具有枚举成员的 `TouchManipulationMode` ：

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             xmlns:tt="clr-namespace:TouchTracking"
             xmlns:local="clr-namespace:SkiaSharpFormsDemos.Transforms"
             x:Class="SkiaSharpFormsDemos.Transforms.TouchManipulationPage"
             Title="Touch Manipulation">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Picker Title="Touch Mode"
                Grid.Row="0"
                SelectedIndexChanged="OnTouchModePickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type local:TouchManipulationMode}">
                    <x:Static Member="local:TouchManipulationMode.None" />
                    <x:Static Member="local:TouchManipulationMode.PanOnly" />
                    <x:Static Member="local:TouchManipulationMode.IsotropicScale" />
                    <x:Static Member="local:TouchManipulationMode.AnisotropicScale" />
                    <x:Static Member="local:TouchManipulationMode.ScaleRotate" />
                    <x:Static Member="local:TouchManipulationMode.ScaleDualRotate" />
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                4
            </Picker.SelectedIndex>
        </Picker>
        
        <Grid BackgroundColor="White"
              Grid.Row="1">
            
            <skia:SKCanvasView x:Name="canvasView"
                               PaintSurface="OnCanvasViewPaintSurface" />
            <Grid.Effects>
                <tt:TouchEffect Capture="True"
                                TouchAction="OnTouchEffectAction" />
            </Grid.Effects>
        </Grid>
    </Grid>
</ContentPage>
```

到底部是一个 `SKCanvasView` ， `TouchEffect` 附加到括着它的单单元格 `Grid` 。

[**TouchManipulationPage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationPage.xaml.cs)代码隐藏文件有一个 `bitmap` 字段，但它的类型不是 `SKBitmap` 。 此类型 `TouchManipulationBitmap` (你将很快) 的类：

```csharp
public partial class TouchManipulationPage : ContentPage
{
    TouchManipulationBitmap bitmap;
    ...

    public TouchManipulationPage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.MountainClimbers.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            SKBitmap bitmap = SKBitmap.Decode(stream);
            this.bitmap = new TouchManipulationBitmap(bitmap);
            this.bitmap.TouchManager.Mode = TouchManipulationMode.ScaleRotate;
        }
    }
    ...
}
```

构造函数实例化 `TouchManipulationBitmap` 对象，并传递到 `SKBitmap` 从嵌入资源获取的构造函数。 构造函数通过将对象的 `Mode` 属性的属性设置 `TouchManager` `TouchManipulationBitmap` 为枚举的成员来结束 `TouchManipulationMode` 。

`SelectedIndexChanged`的处理程序 `Picker` 还设置此 `Mode` 属性：

```csharp
public partial class TouchManipulationPage : ContentPage
{
    ...
    void OnTouchModePickerSelectedIndexChanged(object sender, EventArgs args)
    {
        if (bitmap != null)
        {
            Picker picker = (Picker)sender;
            bitmap.TouchManager.Mode = (TouchManipulationMode)picker.SelectedItem;
        }
    }
    ...
}
```

`TouchAction` `TouchEffect` XAML 文件中实例化的处理程序调用 `TouchManipulationBitmap` 名为和的两个方法 `HitTest` `ProcessTouchEvent` ：

```csharp
public partial class TouchManipulationPage : ContentPage
{
    ...
    List<long> touchIds = new List<long>();
    ...
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        // Convert Xamarin.Forms point to pixels
        Point pt = args.Location;
        SKPoint point =
            new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                        (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));

        switch (args.Type)
        {
            case TouchActionType.Pressed:
                if (bitmap.HitTest(point))
                {
                    touchIds.Add(args.Id);
                    bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                    break;
                }
                break;

            case TouchActionType.Moved:
                if (touchIds.Contains(args.Id))
                {
                    bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                if (touchIds.Contains(args.Id))
                {
                    bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                    touchIds.Remove(args.Id);
                    canvasView.InvalidateSurface();
                }
                break;
        }
    }
    ...
}
```

如果该 `HitTest` 方法返回表示 `true` &mdash; 手指已在由位图占用的区域内触摸屏幕，则 &mdash; touch ID 将添加到 `TouchIds` 集合中。 此 ID 表示该手指的触摸事件顺序，直到手指从屏幕上移开。 如果有多个手指触摸位图，则 `touchIds` 集合中每个手指都包含一个 TOUCH ID。

`TouchAction`处理程序还会调用 `ProcessTouchEvent` 中的类 `TouchManipulationBitmap` 。 在这种情况下，某些 (（但不是所有）实际触控处理) 。

[`TouchManipulationBitmap`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationBitmap.cs)类是的包装类 `SKBitmap` ，它包含用于呈现位图和处理触摸事件的代码。 它与类中更一般化的代码一起工作， `TouchManipulationManager` (稍后会看到) 。

`TouchManipulationBitmap`构造函数保存 `SKBitmap` 并实例化两个属性 `TouchManager` ：类型的属性 `TouchManipulationManager` 和类型为的 `Matrix` 属性 `SKMatrix` ：

```csharp
class TouchManipulationBitmap
{
    SKBitmap bitmap;
    ...

    public TouchManipulationBitmap(SKBitmap bitmap)
    {
        this.bitmap = bitmap;
        Matrix = SKMatrix.MakeIdentity();

        TouchManager = new TouchManipulationManager
        {
            Mode = TouchManipulationMode.ScaleRotate
        };
    }

    public TouchManipulationManager TouchManager { set; get; }

    public SKMatrix Matrix { set; get; }
    ...
}
```

此 `Matrix` 属性是由所有触控活动生成的累积转换。 正如您将看到的，每个触控事件都解析为一个矩阵，然后将其与属性存储的值连接在一起 `SKMatrix` `Matrix` 。

`TouchManipulationBitmap`对象在其方法中绘制自身 `Paint` 。 参数是一个 `SKCanvas` 对象。 `SKCanvas`可能已对其应用了转换，因此该方法会将 `Paint` `Matrix` 与此位图关联的属性连接到现有的转换，并在画布完成后还原画布：

```csharp
class TouchManipulationBitmap
{
    ...
    public void Paint(SKCanvas canvas)
    {
        canvas.Save();
        SKMatrix matrix = Matrix;
        canvas.Concat(ref matrix);
        canvas.DrawBitmap(bitmap, 0, 0);
        canvas.Restore();
    }
    ...
}
```

`HitTest` `true` 如果用户在位图边界内的某个点上触及屏幕，则该方法返回。 这将使用前面在 **位图旋转** 页面中显示的逻辑：

```csharp
class TouchManipulationBitmap
{
    ...
    public bool HitTest(SKPoint location)
    {
        // Invert the matrix
        SKMatrix inverseMatrix;

        if (Matrix.TryInvert(out inverseMatrix))
        {
            // Transform the point using the inverted matrix
            SKPoint transformedPoint = inverseMatrix.MapPoint(location);

            // Check if it's in the untransformed bitmap rectangle
            SKRect rect = new SKRect(0, 0, bitmap.Width, bitmap.Height);
            return rect.Contains(transformedPoint);
        }
        return false;
    }
    ...
}
```

中的第二个公共方法 `TouchManipulationBitmap` 为 `ProcessTouchEvent` 。 调用此方法时，已建立触控事件属于此特定位图。 方法维护对象的字典 [`TouchManipulationInfo`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationInfo.cs) ，该字典只是上一个点和每个手指的新点：

```csharp
class TouchManipulationInfo
{
    public SKPoint PreviousPoint { set; get; }

    public SKPoint NewPoint { set; get; }
}
```

下面是字典和 `ProcessTouchEvent` 方法本身：

```csharp
class TouchManipulationBitmap
{
    ...
    Dictionary<long, TouchManipulationInfo> touchDictionary =
        new Dictionary<long, TouchManipulationInfo>();
    ...
    public void ProcessTouchEvent(long id, TouchActionType type, SKPoint location)
    {
        switch (type)
        {
            case TouchActionType.Pressed:
                touchDictionary.Add(id, new TouchManipulationInfo
                {
                    PreviousPoint = location,
                    NewPoint = location
                });
                break;

            case TouchActionType.Moved:
                TouchManipulationInfo info = touchDictionary[id];
                info.NewPoint = location;
                Manipulate();
                info.PreviousPoint = info.NewPoint;
                break;

            case TouchActionType.Released:
                touchDictionary[id].NewPoint = location;
                Manipulate();
                touchDictionary.Remove(id);
                break;

            case TouchActionType.Cancelled:
                touchDictionary.Remove(id);
                break;
        }
    }
    ...
}
```

在 `Moved` 和 `Released` 事件中，方法调用 `Manipulate` 。 在这些情况下， `touchDictionary` 包含一个或多个 `TouchManipulationInfo` 对象。 如果 `touchDictionary` 包含一个项，则 `PreviousPoint` 和 `NewPoint` 值可能不相等，并且表示手指的移动。 如果有多个手指接触位图，则字典包含多个项，但其中只有一个项具有不同的 `PreviousPoint` 和 `NewPoint` 值。 所有其余部分都具有相同的 `PreviousPoint` `NewPoint` 值和值。

这一点很重要： `Manipulate` 方法可以假定它只处理一个手指的移动。 在此调用时，任何其他手指都不会移动，如果它们确实移动 (可能会) ，则在以后对这些移动进行处理 `Manipulate` 。

此 `Manipulate` 方法首先将字典复制到数组，以方便使用。 它忽略前两项以外的任何项。 如果两个手指尝试操作位图，则忽略其他手指。 `Manipulate` 是的最终成员 `TouchManipulationBitmap` ：

```csharp
class TouchManipulationBitmap
{
    ...
    void Manipulate()
    {
        TouchManipulationInfo[] infos = new TouchManipulationInfo[touchDictionary.Count];
        touchDictionary.Values.CopyTo(infos, 0);
        SKMatrix touchMatrix = SKMatrix.MakeIdentity();

        if (infos.Length == 1)
        {
            SKPoint prevPoint = infos[0].PreviousPoint;
            SKPoint newPoint = infos[0].NewPoint;
            SKPoint pivotPoint = Matrix.MapPoint(bitmap.Width / 2, bitmap.Height / 2);

            touchMatrix = TouchManager.OneFingerManipulate(prevPoint, newPoint, pivotPoint);
        }
        else if (infos.Length >= 2)
        {
            int pivotIndex = infos[0].NewPoint == infos[0].PreviousPoint ? 0 : 1;
            SKPoint pivotPoint = infos[pivotIndex].NewPoint;
            SKPoint newPoint = infos[1 - pivotIndex].NewPoint;
            SKPoint prevPoint = infos[1 - pivotIndex].PreviousPoint;

            touchMatrix = TouchManager.TwoFingerManipulate(prevPoint, newPoint, pivotPoint);
        }

        SKMatrix matrix = Matrix;
        SKMatrix.PostConcat(ref matrix, touchMatrix);
        Matrix = matrix;
    }
}
```

如果一个手指正在操作位图，则 `Manipulate` 调用该 `OneFingerManipulate` 对象的方法 `TouchManipulationManager` 。 对于两个手指，它调用 `TwoFingerManipulate` 。 这些方法的参数是相同的： `prevPoint` 和 `newPoint` 参数表示移动的手指。 但 `pivotPoint` 这两个调用的参数不同：

对于单指操作， `pivotPoint` 是位图的中心。 这是为了允许单指旋转。 对于双指操作，该事件指示只移动一个手指，使 `pivotPoint` 成为不移动的手指。

在这两种情况下， `TouchManipulationManager` 将返回一个 `SKMatrix` 值，该方法将该方法与 `Matrix` `TouchManipulationPage` 用来呈现位图的当前属性连接起来。

`TouchManipulationManager` 是通用化的，除了使用以外的其他文件 `TouchManipulationMode` 。 您可以在自己的应用程序中使用此类，而无需更改。 它定义类型为 `TouchManipulationMode` 的单一属性：

```csharp
class TouchManipulationManager
{
    public TouchManipulationMode Mode { set; get; }
    ...
}
```

不过，您可能希望避免此 `AnisotropicScale` 选项。 通过此选项，可以非常轻松地处理位图，使其中一个缩放因子变为零。 这会使位图消失，不会返回。 如果确实需要进行各向异性缩放，则需要增强逻辑，以避免产生不良结果。

`TouchManipulationManager` 使用向量，但由于 `SKVector` SkiaSharp 中没有结构，因此 `SKPoint` 改用。 `SKPoint` 支持减法运算符，并可将结果视为向量。 唯一需要添加的特定于向量的逻辑是 `Magnitude` 计算：

```csharp
class TouchManipulationManager
{
    ...
    float Magnitude(SKPoint point)
    {
        return (float)Math.Sqrt(Math.Pow(point.X, 2) + Math.Pow(point.Y, 2));
    }
}
```

选择旋转后，两个单向和双指操作方法首先处理旋转。 如果检测到任何旋转，则会有效地删除旋转组件。 仍会将其视为平移和缩放。

下面是 `OneFingerManipulate` 方法。 如果尚未启用单指旋转，则逻辑简单， &mdash; 只需使用上一个点和新点来构造一个名为 `delta` 的向量，该向量精确对应于转换。 启用了单指旋转后，该方法使用从) 位图中心 (点到上一个点和新点的角度来构造旋转矩阵：

```csharp
class TouchManipulationManager
{
    public TouchManipulationMode Mode { set; get; }

    public SKMatrix OneFingerManipulate(SKPoint prevPoint, SKPoint newPoint, SKPoint pivotPoint)
    {
        if (Mode == TouchManipulationMode.None)
        {
            return SKMatrix.MakeIdentity();
        }

        SKMatrix touchMatrix = SKMatrix.MakeIdentity();
        SKPoint delta = newPoint - prevPoint;

        if (Mode == TouchManipulationMode.ScaleDualRotate)  // One-finger rotation
        {
            SKPoint oldVector = prevPoint - pivotPoint;
            SKPoint newVector = newPoint - pivotPoint;

            // Avoid rotation if fingers are too close to center
            if (Magnitude(newVector) > 25 && Magnitude(oldVector) > 25)
            {
                float prevAngle = (float)Math.Atan2(oldVector.Y, oldVector.X);
                float newAngle = (float)Math.Atan2(newVector.Y, newVector.X);

                // Calculate rotation matrix
                float angle = newAngle - prevAngle;
                touchMatrix = SKMatrix.MakeRotation(angle, pivotPoint.X, pivotPoint.Y);

                // Effectively rotate the old vector
                float magnitudeRatio = Magnitude(oldVector) / Magnitude(newVector);
                oldVector.X = magnitudeRatio * newVector.X;
                oldVector.Y = magnitudeRatio * newVector.Y;

                // Recalculate delta
                delta = newVector - oldVector;
            }
        }

        // Multiply the rotation matrix by a translation matrix
        SKMatrix.PostConcat(ref touchMatrix, SKMatrix.MakeTranslation(delta.X, delta.Y));

        return touchMatrix;
    }
    ...
}
```

在 `TwoFingerManipulate` 方法中，"透视点" 是指在此特定触摸事件中未移动的手指的位置。 旋转非常类似于单指旋转，然后基于上一个点) 的名为 `oldVector` (的矢量针对旋转进行调整。 其余移动被解释为缩放：

```csharp
class TouchManipulationManager
{
    ...
    public SKMatrix TwoFingerManipulate(SKPoint prevPoint, SKPoint newPoint, SKPoint pivotPoint)
    {
        SKMatrix touchMatrix = SKMatrix.MakeIdentity();
        SKPoint oldVector = prevPoint - pivotPoint;
        SKPoint newVector = newPoint - pivotPoint;

        if (Mode == TouchManipulationMode.ScaleRotate ||
            Mode == TouchManipulationMode.ScaleDualRotate)
        {
            // Find angles from pivot point to touch points
            float oldAngle = (float)Math.Atan2(oldVector.Y, oldVector.X);
            float newAngle = (float)Math.Atan2(newVector.Y, newVector.X);

            // Calculate rotation matrix
            float angle = newAngle - oldAngle;
            touchMatrix = SKMatrix.MakeRotation(angle, pivotPoint.X, pivotPoint.Y);

            // Effectively rotate the old vector
            float magnitudeRatio = Magnitude(oldVector) / Magnitude(newVector);
            oldVector.X = magnitudeRatio * newVector.X;
            oldVector.Y = magnitudeRatio * newVector.Y;
        }

        float scaleX = 1;
        float scaleY = 1;

        if (Mode == TouchManipulationMode.AnisotropicScale)
        {
            scaleX = newVector.X / oldVector.X;
            scaleY = newVector.Y / oldVector.Y;

        }
        else if (Mode == TouchManipulationMode.IsotropicScale ||
                 Mode == TouchManipulationMode.ScaleRotate ||
                 Mode == TouchManipulationMode.ScaleDualRotate)
        {
            scaleX = scaleY = Magnitude(newVector) / Magnitude(oldVector);
        }

        if (!float.IsNaN(scaleX) && !float.IsInfinity(scaleX) &&
            !float.IsNaN(scaleY) && !float.IsInfinity(scaleY))
        {
            SKMatrix.PostConcat(ref touchMatrix,
                SKMatrix.MakeScale(scaleX, scaleY, pivotPoint.X, pivotPoint.Y));
        }

        return touchMatrix;
    }
    ...
}
```

你会注意到此方法中没有显式转换。 但是， `MakeRotation` 和 `MakeScale` 方法都基于透视点，并且包括隐式转换。 如果在位图上使用两根手指并将其沿相同方向拖动，则 `TouchManipulation` 会在两个手指之间交替出现一系列触摸事件。 由于每个手指相对于另一个指针进行移动、缩放或旋转结果，但通过另一手指的移动来求反，因此结果为转换。

**触摸操作**页的唯一剩余部分是 `PaintSurface` 代码隐藏文件中的处理程序 `TouchManipulationPage` 。 这将调用 `Paint` 的方法，该方法 `TouchManipulationBitmap` 将应用表示累积触控活动的矩阵：

```csharp
public partial class TouchManipulationPage : ContentPage
{
    ...
    MatrixDisplay matrixDisplay = new MatrixDisplay();
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Display the bitmap
        bitmap.Paint(canvas);

        // Display the matrix in the lower-right corner
        SKSize matrixSize = matrixDisplay.Measure(bitmap.Matrix);

        matrixDisplay.Paint(canvas, bitmap.Matrix,
            new SKPoint(info.Width - matrixSize.Width,
                        info.Height - matrixSize.Height));
    }
}
```

`PaintSurface`处理程序通过显示一个 `MatrixDisplay` 显示累积 touch 矩阵的对象结束：

[![触摸操作页的三向屏幕截图](touch-images/touchmanipulation-small.png)](touch-images/touchmanipulation-large.png#lightbox "触摸操作页的三向屏幕截图")

## <a name="manipulating-multiple-bitmaps"></a>处理多个位图

在类（如和）中隔离触摸处理代码的优点之一 `TouchManipulationBitmap` `TouchManipulationManager` 是能够在允许用户操作多个位图的程序中重复使用这些类。

**位图散点视图**页面说明了如何执行此操作。 类不是定义类型的字段 `TouchManipulationBitmap` ，而是 [`BitmapScatterPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BitmapScatterViewPage.xaml.cs) 定义 `List` 位图对象的：

```csharp
public partial class BitmapScatterViewPage : ContentPage
{
    List<TouchManipulationBitmap> bitmapCollection =
        new List<TouchManipulationBitmap>();
    ...
    public BitmapScatterViewPage()
    {
        InitializeComponent();

        // Load in all the available bitmaps
        Assembly assembly = GetType().GetTypeInfo().Assembly;
        string[] resourceIDs = assembly.GetManifestResourceNames();
        SKPoint position = new SKPoint();

        foreach (string resourceID in resourceIDs)
        {
            if (resourceID.EndsWith(".png") ||
                resourceID.EndsWith(".jpg"))
            {
                using (Stream stream = assembly.GetManifestResourceStream(resourceID))
                {
                    SKBitmap bitmap = SKBitmap.Decode(stream);
                    bitmapCollection.Add(new TouchManipulationBitmap(bitmap)
                    {
                        Matrix = SKMatrix.MakeTranslation(position.X, position.Y),
                    });
                    position.X += 100;
                    position.Y += 100;
                }
            }
        }
    }
    ...
}
```

构造函数在所有作为嵌入资源提供的位图中加载，并将其添加到中 `bitmapCollection` 。 请注意， `Matrix` 属性在每个对象上初始化 `TouchManipulationBitmap` ，因此每个位图的左上角将偏移100像素。

`BitmapScatterView`该页还需要处理多个位图的触控事件。 `List` `TouchManipulationBitmap` 此程序需要字典，而不是为当前操作的对象定义 touch id：

```csharp
public partial class BitmapScatterViewPage : ContentPage
{
    ...
    Dictionary<long, TouchManipulationBitmap> bitmapDictionary =
       new Dictionary<long, TouchManipulationBitmap>();
    ...
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        // Convert Xamarin.Forms point to pixels
        Point pt = args.Location;
        SKPoint point =
            new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                        (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));

        switch (args.Type)
        {
            case TouchActionType.Pressed:
                for (int i = bitmapCollection.Count - 1; i >= 0; i--)
                {
                    TouchManipulationBitmap bitmap = bitmapCollection[i];

                    if (bitmap.HitTest(point))
                    {
                        // Move bitmap to end of collection
                        bitmapCollection.Remove(bitmap);
                        bitmapCollection.Add(bitmap);

                        // Do the touch processing
                        bitmapDictionary.Add(args.Id, bitmap);
                        bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                        canvasView.InvalidateSurface();
                        break;
                    }
                }
                break;

            case TouchActionType.Moved:
                if (bitmapDictionary.ContainsKey(args.Id))
                {
                    TouchManipulationBitmap bitmap = bitmapDictionary[args.Id];
                    bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                if (bitmapDictionary.ContainsKey(args.Id))
                {
                    TouchManipulationBitmap bitmap = bitmapDictionary[args.Id];
                    bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                    bitmapDictionary.Remove(args.Id);
                    canvasView.InvalidateSurface();
                }
                break;
        }
    }
    ...
}
```

请注意 `Pressed` 逻辑如何 `bitmapCollection` 反向循环。 位图经常相互重叠。 集合中后面的位图直观地位于集合中前面的位图之上。 如果在屏幕上按下了多个位图，则最顶部的位图必须是该手指操作的位图。

还会注意到， `Pressed` 逻辑将该位图移动到集合的末尾，以便它能够直观地移动到其他位图的第一堆。

在 `Moved` 和 `Released` 事件中， `TouchAction` 处理程序将 `ProcessingTouchEvent` `TouchManipulationBitmap` 像以前的程序一样调用中的方法。

最后，该 `PaintSurface` 处理程序会调用 `Paint` 每个 `TouchManipulationBitmap` 对象的方法：

```csharp
public partial class BitmapScatterViewPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKCanvas canvas = args.Surface.Canvas;
        canvas.Clear();

        foreach (TouchManipulationBitmap bitmap in bitmapCollection)
        {
            bitmap.Paint(canvas);
        }
    }
}
```

代码循环遍历集合，并显示从集合开头到末尾的位图堆：

[![位图散点视图页面的三向屏幕截图](touch-images/bitmapscatterview-small.png)](touch-images/bitmapscatterview-large.png#lightbox "位图散点视图页面的三向屏幕截图")

## <a name="single-finger-scaling"></a>单指缩放

缩放操作通常需要使用两根手指来使用一条挤压手势。 但是，可以通过用手指移动位图的角来实现使用单个手指进行缩放。

这会在 **单指角缩放** 页面中进行演示。 由于此示例使用的缩放类型略微不同于在类中实现的类型 `TouchManipulationManager` ，因此它不使用该类或 `TouchManipulationBitmap` 类。 相反，所有触控逻辑都在代码隐藏文件中。 此逻辑比平时更简单，因为它一次只跟踪一个手指，而只是忽略可能触摸屏幕的任何辅助手指。

[**SingleFingerCornerScale**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SingleFingerCornerScalePage.xaml)页实例化 `SKCanvasView` 类并创建 `TouchEffect` 用于跟踪触控事件的对象：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             xmlns:tt="clr-namespace:TouchTracking"
             x:Class="SkiaSharpFormsDemos.Transforms.SingleFingerCornerScalePage"
             Title="Single Finger Corner Scale">

    <Grid BackgroundColor="White"
          Grid.Row="1">

        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface" />
        <Grid.Effects>
            <tt:TouchEffect Capture="True"
                            TouchAction="OnTouchEffectAction"   />
        </Grid.Effects>
    </Grid>
</ContentPage>
```

[**SingleFingerCornerScalePage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SingleFingerCornerScalePage.xaml.cs)文件从**媒体**目录加载位图资源，并使用 `SKMatrix` 定义为字段的对象将其显示：

```csharp
public partial class SingleFingerCornerScalePage : ContentPage
{
    SKBitmap bitmap;
    SKMatrix currentMatrix = SKMatrix.MakeIdentity();
    ···

    public SingleFingerCornerScalePage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        canvas.SetMatrix(currentMatrix);
        canvas.DrawBitmap(bitmap, 0, 0);
    }
    ···
}
```

此 `SKMatrix` 对象由如下所示的触摸逻辑进行修改：

代码隐藏文件的其余部分是 `TouchEffect` 事件处理程序。 首先，将手指的当前位置转换为一个 `SKPoint` 值。 对于 `Pressed` 操作类型，处理程序将检查没有其他手指触摸屏幕，并且手指位于位图边界内。

代码的关键部分是 `if` 涉及两次方法调用的语句 `Math.Pow` 。 此数学公式检查 finger 位置是否在填充位图的椭圆的外部。 如果是这样，则为缩放操作。 手指位于位图的一个角附近，而一个透视点则确定是相反的角点。 如果手指在此椭圆内，则是一个常规的平移操作：

```csharp
public partial class SingleFingerCornerScalePage : ContentPage
{
    SKBitmap bitmap;
    SKMatrix currentMatrix = SKMatrix.MakeIdentity();

    // Information for translating and scaling
    long? touchId = null;
    SKPoint pressedLocation;
    SKMatrix pressedMatrix;

    // Information for scaling
    bool isScaling;
    SKPoint pivotPoint;
    ···

    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        // Convert Xamarin.Forms point to pixels
        Point pt = args.Location;
        SKPoint point =
            new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                        (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));

        switch (args.Type)
        {
            case TouchActionType.Pressed:
                // Track only one finger
                if (touchId.HasValue)
                    return;

                // Check if the finger is within the boundaries of the bitmap
                SKRect rect = new SKRect(0, 0, bitmap.Width, bitmap.Height);
                rect = currentMatrix.MapRect(rect);
                if (!rect.Contains(point))
                    return;

                // First assume there will be no scaling
                isScaling = false;

                // If touch is outside interior ellipse, make this a scaling operation
                if (Math.Pow((point.X - rect.MidX) / (rect.Width / 2), 2) +
                    Math.Pow((point.Y - rect.MidY) / (rect.Height / 2), 2) > 1)
                {
                    isScaling = true;
                    float xPivot = point.X < rect.MidX ? rect.Right : rect.Left;
                    float yPivot = point.Y < rect.MidY ? rect.Bottom : rect.Top;
                    pivotPoint = new SKPoint(xPivot, yPivot);
                }

                // Common for either pan or scale
                touchId = args.Id;
                pressedLocation = point;
                pressedMatrix = currentMatrix;
                break;

            case TouchActionType.Moved:
                if (!touchId.HasValue || args.Id != touchId.Value)
                    return;

                SKMatrix matrix = SKMatrix.MakeIdentity();

                // Translating
                if (!isScaling)
                {
                    SKPoint delta = point - pressedLocation;
                    matrix = SKMatrix.MakeTranslation(delta.X, delta.Y);
                }
                // Scaling
                else
                {
                    float scaleX = (point.X - pivotPoint.X) / (pressedLocation.X - pivotPoint.X);
                    float scaleY = (point.Y - pivotPoint.Y) / (pressedLocation.Y - pivotPoint.Y);
                    matrix = SKMatrix.MakeScale(scaleX, scaleY, pivotPoint.X, pivotPoint.Y);
                }

                // Concatenate the matrices
                SKMatrix.PreConcat(ref matrix, pressedMatrix);
                currentMatrix = matrix;
                canvasView.InvalidateSurface();
                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                touchId = null;
                break;
        }
    }
}
```

`Moved`操作类型计算与手指在屏幕上按下时的触摸活动相对应的矩阵。 它在手指首次按下该位图时，将该矩阵与矩阵连接起来。 缩放操作始终相对于手指接触的角。

对于小规模或椭圆形的位图，内部椭圆可能会占用大多数位图，并使小区域留在角落以缩放位图。 您可能更倾向于一种略有不同的方法，在这种情况下，您可以将设置为的整个块替换为 `if` `isScaling` `true` 以下代码：

```csharp
float halfHeight = rect.Height / 2;
float halfWidth = rect.Width / 2;

// Top half of bitmap
if (point.Y < rect.MidY)
{
    float yRelative = (point.Y - rect.Top) / halfHeight;

    // Upper-left corner
    if (point.X < rect.MidX - yRelative * halfWidth)
    {
        isScaling = true;
        pivotPoint = new SKPoint(rect.Right, rect.Bottom);
    }
    // Upper-right corner
    else if (point.X > rect.MidX + yRelative * halfWidth)
    {
        isScaling = true;
        pivotPoint = new SKPoint(rect.Left, rect.Bottom);
    }
}
// Bottom half of bitmap
else
{
    float yRelative = (point.Y - rect.MidY) / halfHeight;

    // Lower-left corner
    if (point.X < rect.Left + yRelative * halfWidth)
    {
        isScaling = true;
        pivotPoint = new SKPoint(rect.Right, rect.Top);
    }
    // Lower-right corner
    else if (point.X > rect.Right - yRelative * halfWidth)
    {
        isScaling = true;
        pivotPoint = new SKPoint(rect.Left, rect.Top);
    }
}
```

此代码有效地将位图的面积划分为内部菱形形状，并将四个三角形分割成四个角。 这样，就可以方便地抓住和缩放位图。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [从效果调用事件](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)