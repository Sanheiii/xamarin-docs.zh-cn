---
title: 裁剪 SkiaSharp 位图
description: 了解如何使用 SkiaSharp 设计用于以交互方式 desribing 裁剪矩形的用户界面。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 0A79AB27-C69F-4376-8FFE-FF46E4783F30
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 45634695050dc6f74a9b1617a2180481e3788d11
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91557317"
---
# <a name="cropping-skiasharp-bitmaps"></a>裁剪 SkiaSharp 位图

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

[**创建和绘图 SkiaSharp 位图**](drawing.md)一文介绍了如何 `SKBitmap` 将对象传递到 `SKCanvas` 构造函数。 在该画布上调用的任何绘图方法都将导致在位图上呈现图形。 这些绘图方法包括 `DrawBitmap` ，这意味着此方法允许将部分或全部位图传输到另一个位图，可能会应用转换。

您可以使用该 [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKRect,SkiaSharp.SKPaint)) 方法通过使用源和目标矩形调用方法来裁剪位图：

```csharp
canvas.DrawBitmap(bitmap, sourceRect, destRect);
```

但是，实现裁剪的应用程序通常会提供一个接口，使用户能够以交互方式选择裁剪矩形：

![裁剪示例](cropping-images/CroppingSample.png "裁剪示例")

本文重点介绍该界面。

## <a name="encapsulating-the-cropping-rectangle"></a>封装裁剪矩形

在名为的类中隔离某些裁剪逻辑会很有帮助 `CroppingRectangle` 。 构造函数参数包括最大矩形，这通常是要裁剪的位图的大小和可选的纵横比。 构造函数首先定义初始裁剪矩形，它在 `Rect` 类型的属性中公开 `SKRect` 。 此初始裁剪矩形的宽度和高度均为80%，但如果指定了纵横比，则会调整此矩形：

```csharp
class CroppingRectangle
{
    ···
    SKRect maxRect;             // generally the size of the bitmap
    float? aspectRatio;

    public CroppingRectangle(SKRect maxRect, float? aspectRatio = null)
    {
        this.maxRect = maxRect;
        this.aspectRatio = aspectRatio;

        // Set initial cropping rectangle
        Rect = new SKRect(0.9f * maxRect.Left + 0.1f * maxRect.Right,
                          0.9f * maxRect.Top + 0.1f * maxRect.Bottom,
                          0.1f * maxRect.Left + 0.9f * maxRect.Right,
                          0.1f * maxRect.Top + 0.9f * maxRect.Bottom);

        // Adjust for aspect ratio
        if (aspectRatio.HasValue)
        {
            SKRect rect = Rect;
            float aspect = aspectRatio.Value;

            if (rect.Width > aspect * rect.Height)
            {
                float width = aspect * rect.Height;
                rect.Left = (maxRect.Width - width) / 2;
                rect.Right = rect.Left + width;
            }
            else
            {
                float height = rect.Width / aspect;
                rect.Top = (maxRect.Height - height) / 2;
                rect.Bottom = rect.Top + height;
            }

            Rect = rect;
        }
    }

    public SKRect Rect { set; get; }
    ···
}
```

还提供了一个有用的信息片段，它 `CroppingRectangle` 是一个值的数组，这些 `SKPoint` 值对应于按左上角、右上方、右下方和左下角的裁剪矩形的四个角：

```csharp
class CroppingRectangle
{
    ···
    public SKPoint[] Corners
    {
        get
        {
            return new SKPoint[]
            {
                new SKPoint(Rect.Left, Rect.Top),
                new SKPoint(Rect.Right, Rect.Top),
                new SKPoint(Rect.Right, Rect.Bottom),
                new SKPoint(Rect.Left, Rect.Bottom)
            };
        }
    }
    ···
}
```

此数组用于以下调用的方法 `HitTest` 。 `SKPoint`参数是与手指触摸或鼠标单击对应的点。 方法返回 (0、1、2或 3) 的索引，该索引对应于手指或鼠标指针接触的角，在参数给定的距离内 `radius` ：

```csharp
class CroppingRectangle
{
    ···
    public int HitTest(SKPoint point, float radius)
    {
        SKPoint[] corners = Corners;

        for (int index = 0; index < corners.Length; index++)
        {
            SKPoint diff = point - corners[index];

            if ((float)Math.Sqrt(diff.X * diff.X + diff.Y * diff.Y) < radius)
            {
                return index;
            }
        }

        return -1;
    }
    ···
}
```

如果触摸或鼠标点不在 `radius` 任何角的单元内，此方法将返回 &ndash; 1。

调用中的最后一个方法 `CroppingRectangle` `MoveCorner` ，该方法是为了响应触控或鼠标移动而调用的。 这两个参数指示要移动的角的索引以及该角的新位置。 方法的前半部分根据角的新位置调整裁剪矩形，但始终在的边界内调整 `maxRect` ，这是位图的大小。 此逻辑还会考虑字段的 `MINIMUM` 作用，以避免将裁剪矩形折叠为任何内容：

```csharp
class CroppingRectangle
{
    const float MINIMUM = 10;   // pixels width or height
    ···
    public void MoveCorner(int index, SKPoint point)
    {
        SKRect rect = Rect;

        switch (index)
        {
            case 0: // upper-left
                rect.Left = Math.Min(Math.Max(point.X, maxRect.Left), rect.Right - MINIMUM);
                rect.Top = Math.Min(Math.Max(point.Y, maxRect.Top), rect.Bottom - MINIMUM);
                break;

            case 1: // upper-right
                rect.Right = Math.Max(Math.Min(point.X, maxRect.Right), rect.Left + MINIMUM);
                rect.Top = Math.Min(Math.Max(point.Y, maxRect.Top), rect.Bottom - MINIMUM);
                break;

            case 2: // lower-right
                rect.Right = Math.Max(Math.Min(point.X, maxRect.Right), rect.Left + MINIMUM);
                rect.Bottom = Math.Max(Math.Min(point.Y, maxRect.Bottom), rect.Top + MINIMUM);
                break;

            case 3: // lower-left
                rect.Left = Math.Min(Math.Max(point.X, maxRect.Left), rect.Right - MINIMUM);
                rect.Bottom = Math.Max(Math.Min(point.Y, maxRect.Bottom), rect.Top + MINIMUM);
                break;
        }

        // Adjust for aspect ratio
        if (aspectRatio.HasValue)
        {
            float aspect = aspectRatio.Value;

            if (rect.Width > aspect * rect.Height)
            {
                float width = aspect * rect.Height;

                switch (index)
                {
                    case 0:
                    case 3: rect.Left = rect.Right - width; break;
                    case 1:
                    case 2: rect.Right = rect.Left + width; break;
                }
            }
            else
            {
                float height = rect.Width / aspect;

                switch (index)
                {
                    case 0:
                    case 1: rect.Top = rect.Bottom - height; break;
                    case 2:
                    case 3: rect.Bottom = rect.Top + height; break;
                }
            }
        }

        Rect = rect;
    }
}
```

方法的后半部分会调整可选纵横比。

请记住，此类中的所有内容都以像素为单位。

## <a name="a-canvas-view-just-for-cropping"></a>仅用于剪切的画布视图

`CroppingRectangle`刚才看到的类由 `PhotoCropperCanvasView` 派生自的类使用 `SKCanvasView` 。 此类负责显示位图和裁剪矩形，并处理触控或鼠标事件以便更改裁剪矩形。

`PhotoCropperCanvasView`构造函数需要位图。 纵横比是可选的。 构造函数 `CroppingRectangle` 根据此位图和纵横比实例化类型的对象，并将其保存为字段：

```csharp
class PhotoCropperCanvasView : SKCanvasView
{
    ···
    SKBitmap bitmap;
    CroppingRectangle croppingRect;
    ···
    public PhotoCropperCanvasView(SKBitmap bitmap, float? aspectRatio = null)
    {
        this.bitmap = bitmap;

        SKRect bitmapRect = new SKRect(0, 0, bitmap.Width, bitmap.Height);
        croppingRect = new CroppingRectangle(bitmapRect, aspectRatio);
        ···
    }
    ···
}
```

由于此类派生自 `SKCanvasView` ，因此不需要安装事件的处理程序 `PaintSurface` 。 它可以改为重写其 `OnPaintSurface` 方法。 方法显示位图，并使用 `SKPaint` 保存为字段的几个对象来绘制当前裁剪矩形：

```csharp
class PhotoCropperCanvasView : SKCanvasView
{
    const int CORNER = 50;      // pixel length of cropper corner
    ···
    SKBitmap bitmap;
    CroppingRectangle croppingRect;
    SKMatrix inverseBitmapMatrix;
    ···
    // Drawing objects
    SKPaint cornerStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.White,
        StrokeWidth = 10
    };

    SKPaint edgeStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.White,
        StrokeWidth = 2
    };
    ···
    protected override void OnPaintSurface(SKPaintSurfaceEventArgs args)
    {
        base.OnPaintSurface(args);

        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Gray);

        // Calculate rectangle for displaying bitmap
        float scale = Math.Min((float)info.Width / bitmap.Width, (float)info.Height / bitmap.Height);
        float x = (info.Width - scale * bitmap.Width) / 2;
        float y = (info.Height - scale * bitmap.Height) / 2;
        SKRect bitmapRect = new SKRect(x, y, x + scale * bitmap.Width, y + scale * bitmap.Height);
        canvas.DrawBitmap(bitmap, bitmapRect);

        // Calculate a matrix transform for displaying the cropping rectangle
        SKMatrix bitmapScaleMatrix = SKMatrix.MakeIdentity();
        bitmapScaleMatrix.SetScaleTranslate(scale, scale, x, y);

        // Display rectangle
        SKRect scaledCropRect = bitmapScaleMatrix.MapRect(croppingRect.Rect);
        canvas.DrawRect(scaledCropRect, edgeStroke);

        // Display heavier corners
        using (SKPath path = new SKPath())
        {
            path.MoveTo(scaledCropRect.Left, scaledCropRect.Top + CORNER);
            path.LineTo(scaledCropRect.Left, scaledCropRect.Top);
            path.LineTo(scaledCropRect.Left + CORNER, scaledCropRect.Top);

            path.MoveTo(scaledCropRect.Right - CORNER, scaledCropRect.Top);
            path.LineTo(scaledCropRect.Right, scaledCropRect.Top);
            path.LineTo(scaledCropRect.Right, scaledCropRect.Top + CORNER);

            path.MoveTo(scaledCropRect.Right, scaledCropRect.Bottom - CORNER);
            path.LineTo(scaledCropRect.Right, scaledCropRect.Bottom);
            path.LineTo(scaledCropRect.Right - CORNER, scaledCropRect.Bottom);

            path.MoveTo(scaledCropRect.Left + CORNER, scaledCropRect.Bottom);
            path.LineTo(scaledCropRect.Left, scaledCropRect.Bottom);
            path.LineTo(scaledCropRect.Left, scaledCropRect.Bottom - CORNER);

            canvas.DrawPath(path, cornerStroke);
        }

        // Invert the transform for touch tracking
        bitmapScaleMatrix.TryInvert(out inverseBitmapMatrix);
    }
    ···
}
```

类中的代码以 `CroppingRectangle` 位图的像素大小为裁剪矩形。 但是，类的位图显示 `PhotoCropperCanvasView` 是根据显示区域的大小进行缩放的。 `bitmapScaleMatrix`在重写中计算的将 `OnPaintSurface` 从位图像素映射到位图显示时的大小和位置。 然后，此矩阵用于转换裁剪矩形，使其可以相对于位图显示。

重写的最后一行 `OnPaintSurface` 取反的 `bitmapScaleMatrix` ，并将其保存为 `inverseBitmapMatrix` 字段。 这用于触控处理。

`TouchEffect`对象被实例化为字段，并且构造函数将处理程序附加到 `TouchAction` 事件，但需要将其 `TouchEffect` 添加到 `Effects` 导数的_父级_的集合 `SKCanvasView` ，以便在重写中完成此 `OnParentSet` 操作：

```csharp
class PhotoCropperCanvasView : SKCanvasView
{
    ···
    const int RADIUS = 100;     // pixel radius of touch hit-test
    ···
    CroppingRectangle croppingRect;
    SKMatrix inverseBitmapMatrix;

    // Touch tracking
    TouchEffect touchEffect = new TouchEffect();
    struct TouchPoint
    {
        public int CornerIndex { set; get; }
        public SKPoint Offset { set; get; }
    }

    Dictionary<long, TouchPoint> touchPoints = new Dictionary<long, TouchPoint>();
    ···
    public PhotoCropperCanvasView(SKBitmap bitmap, float? aspectRatio = null)
    {
        ···
        touchEffect.TouchAction += OnTouchEffectTouchAction;
    }
    ···
    protected override void OnParentSet()
    {
        base.OnParentSet();

        // Attach TouchEffect to parent view
        Parent.Effects.Add(touchEffect);
    }
    ···
    void OnTouchEffectTouchAction(object sender, TouchActionEventArgs args)
    {
        SKPoint pixelLocation = ConvertToPixel(args.Location);
        SKPoint bitmapLocation = inverseBitmapMatrix.MapPoint(pixelLocation);

        switch (args.Type)
        {
            case TouchActionType.Pressed:
                // Convert radius to bitmap/cropping scale
                float radius = inverseBitmapMatrix.ScaleX * RADIUS;

                // Find corner that the finger is touching
                int cornerIndex = croppingRect.HitTest(bitmapLocation, radius);

                if (cornerIndex != -1 && !touchPoints.ContainsKey(args.Id))
                {
                    TouchPoint touchPoint = new TouchPoint
                    {
                        CornerIndex = cornerIndex,
                        Offset = bitmapLocation - croppingRect.Corners[cornerIndex]
                    };

                    touchPoints.Add(args.Id, touchPoint);
                }
                break;

            case TouchActionType.Moved:
                if (touchPoints.ContainsKey(args.Id))
                {
                    TouchPoint touchPoint = touchPoints[args.Id];
                    croppingRect.MoveCorner(touchPoint.CornerIndex,
                                            bitmapLocation - touchPoint.Offset);
                    InvalidateSurface();
                }
                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                if (touchPoints.ContainsKey(args.Id))
                {
                    touchPoints.Remove(args.Id);
                }
                break;
        }
    }

    SKPoint ConvertToPixel(Xamarin.Forms.Point pt)
    {
        return new SKPoint((float)(CanvasSize.Width * pt.X / Width),
                           (float)(CanvasSize.Height * pt.Y / Height));
    }
}
```

处理程序处理的触摸事件 `TouchAction` 采用与设备无关的单位。 首先需要使用类底部的方法将其转换为像素 `ConvertToPixel` ，然后使用将其转换为 `CroppingRectangle` 单位 `inverseBitmapMatrix` 。

对于 `Pressed` 事件， `TouchAction` 处理程序将调用的 `HitTest` 方法 `CroppingRectangle` 。 如果此返回的索引不 &ndash; 是1，则将处理裁剪矩形的一个角。 该索引和角部实际触点的偏移量存储在 `TouchPoint` 对象中并添加到 `touchPoints` 字典中。

对于 `Moved` 事件，将 `MoveCorner` 调用的方法 `CroppingRectangle` 来移动角，同时调整纵横比的可能。

在任何时候，使用的程序 `PhotoCropperCanvasView` 都可以访问 `CroppedBitmap` 属性。 此属性使用 `Rect` 的属性 `CroppingRectangle` 来创建裁剪大小的新位图。 `DrawBitmap`使用目标矩形和源矩形的版本随后提取原始位图的子集：

```csharp
class PhotoCropperCanvasView : SKCanvasView
{
    ···
    SKBitmap bitmap;
    CroppingRectangle croppingRect;
    ···
    public SKBitmap CroppedBitmap
    {
        get
        {
            SKRect cropRect = croppingRect.Rect;
            SKBitmap croppedBitmap = new SKBitmap((int)cropRect.Width,
                                                  (int)cropRect.Height);
            SKRect dest = new SKRect(0, 0, cropRect.Width, cropRect.Height);
            SKRect source = new SKRect(cropRect.Left, cropRect.Top,
                                       cropRect.Right, cropRect.Bottom);

            using (SKCanvas canvas = new SKCanvas(croppedBitmap))
            {
                canvas.DrawBitmap(bitmap, source, dest);
            }

            return croppedBitmap;
        }
    }
    ···
}
```

## <a name="hosting-the-photo-cropper-canvas-view"></a>承载 photo cropper canvas 视图

对于这两个处理裁剪逻辑的类， **[SkiaSharpFormsDemos](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** 应用程序中的**照片裁剪**页面几乎不需要执行任何操作。 XAML 文件将实例化 `Grid` 以承载 `PhotoCropperCanvasView` 和 " **完成** " 按钮：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SkiaSharpFormsDemos.Bitmaps.PhotoCroppingPage"
             Title="Photo Cropping">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid x:Name="canvasViewHost"
              Grid.Row="0"
              BackgroundColor="Gray"
              Padding="5" />

        <Button Text="Done"
                Grid.Row="1"
                HorizontalOptions="Center"
                Margin="5"
                Clicked="OnDoneButtonClicked" />
    </Grid>
</ContentPage>
```

`PhotoCropperCanvasView`无法在 XAML 文件中实例化，因为它需要类型的参数 `SKBitmap` 。

相反， `PhotoCropperCanvasView` 使用资源位图之一在代码隐藏文件的构造函数中实例化：

```csharp
public partial class PhotoCroppingPage : ContentPage
{
    PhotoCropperCanvasView photoCropper;
    SKBitmap croppedBitmap;

    public PhotoCroppingPage ()
    {
        InitializeComponent ();

        SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(GetType(),
            "SkiaSharpFormsDemos.Media.MountainClimbers.jpg");

        photoCropper = new PhotoCropperCanvasView(bitmap);
        canvasViewHost.Children.Add(photoCropper);
    }

    void OnDoneButtonClicked(object sender, EventArgs args)
    {
        croppedBitmap = photoCropper.CroppedBitmap;

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
        canvas.DrawBitmap(croppedBitmap, info.Rect, BitmapStretch.Uniform);
    }
}
```

然后，用户可以操作裁剪矩形：

[![Photo Cropper 1](cropping-images/PhotoCropping1.png "Photo Cropper 1")](cropping-images/PhotoCropping1-Large.png#lightbox)

定义好裁剪矩形后，单击 " **完成** " 按钮。 `Clicked`处理程序从的属性获取裁剪后的位图 `CroppedBitmap` `PhotoCropperCanvasView` ，并用 `SKCanvasView` 显示此裁剪位图的新对象替换页面的所有内容：

[![Photo Cropper 2](cropping-images/PhotoCropping2.png "Photo Cropper 2")](cropping-images/PhotoCropping2-Large.png#lightbox)

尝试将第二个参数设置 `PhotoCropperCanvasView` 为 1.78 f (例如) ：

```csharp
photoCropper = new PhotoCropperCanvasView(bitmap, 1.78f);
```

你将看到裁剪矩形限制为高清晰度电视的16对9纵横比特性。

## <a name="dividing-a-bitmap-into-tiles"></a>将位图分割为磁贴

该 Xamarin.Forms 版本的著名14-15 谜题出现在[_创建移动应用的 Xamarin.Forms _](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)书籍第22章，并可下载为[**XamagonXuzzle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle)。 不过，谜题更有趣 (，当它基于您自己的照片库中的图像时，) 通常更具挑战性。

此版本的14-15 谜题是 **[SkiaSharpFormsDemos](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** 应用程序的一部分，由一系列标题为 **Photo 谜**的页面组成。

**PhotoPuzzlePage1**文件由 `Button` 以下内容组成：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SkiaSharpFormsDemos.Bitmaps.PhotoPuzzlePage1"
             Title="Photo Puzzle">

    <Button Text="Pick a photo from your library"
            VerticalOptions="CenterAndExpand"
            HorizontalOptions="CenterAndExpand"
            Clicked="OnPickButtonClicked"/>

</ContentPage>
```

代码隐藏文件实现了一个 `Clicked` 处理程序，该处理程序使用 `IPhotoLibrary` 依赖项服务来让用户从照片库中选取照片：

```csharp
public partial class PhotoPuzzlePage1 : ContentPage
{
    public PhotoPuzzlePage1 ()
    {
        InitializeComponent ();
    }

    async void OnPickButtonClicked(object sender, EventArgs args)
    {
        IPhotoLibrary photoLibrary = DependencyService.Get<IPhotoLibrary>();
        using (Stream stream = await photoLibrary.PickPhotoAsync())
        {
            if (stream != null)
            {
                SKBitmap bitmap = SKBitmap.Decode(stream);

                await Navigation.PushAsync(new PhotoPuzzlePage2(bitmap));
            }
        }
    }
}
```

然后，该方法导航到 `PhotoPuzzlePage2` ，并将其传递给构造。

从库中选择的照片可能不会在照片库中显示，而是旋转或颠倒。  (这尤其是 iOS 设备的问题。出于这种原因 ) ， `PhotoPuzzlePage2` 你可以将图像旋转到所需的方向。 该 XAML 文件包含三个标签为 **90&#x00B0; 右** (表示顺时针) 、 **90&#x00B0; 左** (逆时针) 和 **完成**。

代码隐藏文件实现了在 **[SkiaSharp 位图上创建和绘制](drawing.md#rotating-bitmaps)** 文章中所示的位图旋转逻辑。 用户可以顺时针或逆时针旋转图像90：

```csharp
public partial class PhotoPuzzlePage2 : ContentPage
{
    SKBitmap bitmap;

    public PhotoPuzzlePage2 (SKBitmap bitmap)
    {
        this.bitmap = bitmap;

        InitializeComponent ();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform);
    }

    void OnRotateRightButtonClicked(object sender, EventArgs args)
    {
        SKBitmap rotatedBitmap = new SKBitmap(bitmap.Height, bitmap.Width);

        using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
        {
            canvas.Clear();
            canvas.Translate(bitmap.Height, 0);
            canvas.RotateDegrees(90);
            canvas.DrawBitmap(bitmap, new SKPoint());
        }

        bitmap = rotatedBitmap;
        canvasView.InvalidateSurface();
    }

    void OnRotateLeftButtonClicked(object sender, EventArgs args)
    {
        SKBitmap rotatedBitmap = new SKBitmap(bitmap.Height, bitmap.Width);

        using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
        {
            canvas.Clear();
            canvas.Translate(0, bitmap.Width);
            canvas.RotateDegrees(-90);
            canvas.DrawBitmap(bitmap, new SKPoint());
        }

        bitmap = rotatedBitmap;
        canvasView.InvalidateSurface();
    }

    async void OnDoneButtonClicked(object sender, EventArgs args)
    {
        await Navigation.PushAsync(new PhotoPuzzlePage3(bitmap));
    }
}
```

当用户单击 " **完成** " 按钮时， `Clicked` 处理程序将导航到 `PhotoPuzzlePage3` ，同时传递该页的构造函数中的最终旋转位图。

`PhotoPuzzlePage3` 允许裁剪照片。 该程序需要将正方形位图分成图块的 4 x 4 网格。

**PhotoPuzzlePage3**文件包含 `Label` 、 `Grid` 用于承载的 `PhotoCropperCanvasView` 和另一个 "**完成**" 按钮：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SkiaSharpFormsDemos.Bitmaps.PhotoPuzzlePage3"
             Title="Photo Puzzle">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Label Text="Crop the photo to a square"
               Grid.Row="0"
               FontSize="Large"
               HorizontalTextAlignment="Center"
               Margin="5" />

        <Grid x:Name="canvasViewHost"
              Grid.Row="1"
              BackgroundColor="Gray"
              Padding="5" />

        <Button Text="Done"
                Grid.Row="2"
                HorizontalOptions="Center"
                Margin="5"
                Clicked="OnDoneButtonClicked" />
    </Grid>
</ContentPage>
```

代码隐藏文件会将替换为 `PhotoCropperCanvasView` 传递给其构造函数的位图。 请注意，将1作为的第二个参数传递给 `PhotoCropperCanvasView` 。 此纵横比为1将使裁剪矩形成为正方形：

```csharp
public partial class PhotoPuzzlePage3 : ContentPage
{
    PhotoCropperCanvasView photoCropper;

    public PhotoPuzzlePage3(SKBitmap bitmap)
    {
        InitializeComponent ();

        photoCropper = new PhotoCropperCanvasView(bitmap, 1f);
        canvasViewHost.Children.Add(photoCropper);
    }

    async void OnDoneButtonClicked(object sender, EventArgs args)
    {
        SKBitmap croppedBitmap = photoCropper.CroppedBitmap;
        int width = croppedBitmap.Width / 4;
        int height = croppedBitmap.Height / 4;

        ImageSource[] imgSources = new ImageSource[15];

        for (int row = 0; row < 4; row++)
        {
            for (int col = 0; col < 4; col++)
            {
                // Skip the last one!
                if (row == 3 && col == 3)
                    break;

                // Create a bitmap 1/4 the width and height of the original
                SKBitmap bitmap = new SKBitmap(width, height);
                SKRect dest = new SKRect(0, 0, width, height);
                SKRect source = new SKRect(col * width, row * height, (col + 1) * width, (row + 1) * height);

                // Copy 1/16 of the original into that bitmap
                using (SKCanvas canvas = new SKCanvas(bitmap))
                {
                    canvas.DrawBitmap(croppedBitmap, source, dest);
                }

                imgSources[4 * row + col] = (SKBitmapImageSource)bitmap;
            }
        }

        await Navigation.PushAsync(new PhotoPuzzlePage4(imgSources));
    }
}
```

" **完成** " 按钮处理程序可获取裁剪后的位图的宽度和高度 (这两个值应为同一) ，然后将其划分为15个单独的位图，其中每个位图的宽度和高度均为1/4。  (不会创建最新的16个位图。 ) `DrawBitmap` 使用源和目标矩形的方法，可基于更大位图的子集创建位图。

## <a name="converting-to-no-locxamarinforms-bitmaps"></a>转换为 Xamarin.Forms 位图

在 `OnDoneButtonClicked` 方法中，为15个位图创建的数组的类型为 [`ImageSource`](xref:Xamarin.Forms.ImageSource) ：

```csharp
ImageSource[] imgSources = new ImageSource[15];
```

`ImageSource` 是 Xamarin.Forms 封装位图的基类型。 幸运的是，SkiaSharp 允许从 SkiaSharp 位图转换为 Xamarin.Forms 位图。 **SkiaSharp**程序集定义一个 [`SKBitmapImageSource`](xref:SkiaSharp.Views.Forms.SKBitmapImageSource) 派生自的类， `ImageSource` 但可以基于 SkiaSharp 对象创建该类 `SKBitmap` 。 `SKBitmapImageSource` 甚至还定义了和之间的转换， `SKBitmapImageSource` `SKBitmap` 这也就是 `SKBitmap` 对象在数组中的存储方式 Xamarin.Forms ：

```csharp
imgSources[4 * row + col] = (SKBitmapImageSource)bitmap;
```

此位图数组作为构造函数传递给 `PhotoPuzzlePage4` 。 该页完全不 Xamarin.Forms 使用任何 SkiaSharp。 它非常类似于 [**XamagonXuzzle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle)，因此不会在此处进行描述，但会显示选定的照片，分为15个正方形磁贴：

[![照片测验题1](cropping-images/PhotoPuzzle1.png "照片测验题1")](cropping-images/PhotoPuzzle1-Large.png#lightbox)

按 " **随机化** " 按钮将会混合所有磁贴：

[![照片测验题2](cropping-images/PhotoPuzzle2.png "照片测验题2")](cropping-images/PhotoPuzzle2-Large.png#lightbox)

现在可以按正确的顺序重新排列它们。 与空白正方形处于相同行或列中的任何磁贴都可以被点击以将它们移到空白正方形中。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)