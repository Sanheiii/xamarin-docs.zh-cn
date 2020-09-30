---
title: 动画处理 SkiaSharp 位图
description: 了解如何按顺序显示一系列位图并渲染动画 GIF 文件，从而执行位图动画。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 97142ADC-E2FD-418C-8A09-9C561AEE5BFD
author: davidbritch
ms.author: dabritch
ms.date: 07/12/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d9a96de5520a03b2ef51426be2c589c736ca2396
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562387"
---
# <a name="animating-skiasharp-bitmaps"></a>动画处理 SkiaSharp 位图

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

对 SkiaSharp 图形进行动画处理的应用程序通常会按 `InvalidateSurface` `SKCanvasView` 固定速率（通常每16毫秒）调用。 使图面失效将触发对 `PaintSurface` 处理程序的调用以重绘显示。 当视觉对象每秒重绘60次时，它们看起来非常平滑。

但是，如果图形过于复杂而无法在16毫秒内呈现，动画可能会抖动。 程序员可能会选择将刷新频率减少到30次，或一秒15次，但有时甚至是不够的。 有时，图形很复杂，以致于无法实时呈现。

一种解决方法是在一系列位图上呈现动画的各个帧，以便预先准备好动画。 若要显示动画，只需按60次一秒的顺序显示这些位图。

当然，这可能会产生大量的位图，但这就是如何进行大范围三维动画播放的。 三维图形太复杂，无法实时呈现。 渲染每个帧需要大量的处理时间。 观看电影时看到的内容实质上是一系列位图。

可以在 SkiaSharp 中执行类似操作。 本文介绍两种类型的位图动画。 第一个示例是 Mandelbrot 集的动画：

![动画采样](animating-images/AnimatingSample.png "动画采样")

第二个示例演示如何使用 SkiaSharp 来呈现动态 GIF 文件。

## <a name="bitmap-animation"></a>位图动画

Mandelbrot 集在视觉上沉迷但 computionally 长。  (本文中所用的 Mandelbrot 集和数学的讨论，请参阅第20章，其中介绍[了_ Xamarin.Forms _ ](https://xamarin.azureedge.net/developer/xamarin-forms-book/XamarinFormsBook-Ch20-Apr2016.pdf)从666页开始创建移动应用。 以下说明假定你的背景知识。 ) 

[**Mandelbrot 动画**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-mandelanima)示例使用位图动画来模拟 Mandelbrot 集中固定点的连续缩放。 放大之后会进行缩小，然后循环会持续重复或直到你结束该程序。

该程序通过最多创建50个在应用程序本地存储中存储的位图来准备此动画。 每个位图的宽度和高度都是上一个位图的复杂平面的一半。  (在程序中，这些位图称为表示整数 _缩放级别_。 ) 然后按顺序显示位图。 每个位图的缩放都进行了动画处理，以提供从一个位图到另一个位图的平滑进度。

与_ Xamarin.Forms 使用创建移动应用_的第20章所述的最后一个程序一样， **Mandelbrot 动画**中 Mandelbrot 集的计算是一个具有八个参数的异步方法。 参数包括复杂中心点以及围绕该中心点的复杂平面的宽度和高度。 接下来的三个参数是要创建的位图的像素宽度和高度，以及递归计算的最大迭代数。 `progress`参数用于显示此计算的进度。 `cancelToken`此程序中未使用参数：

```csharp
static class Mandelbrot
{
    public static Task<BitmapInfo> CalculateAsync(Complex center,
                                                  double width, double height,
                                                  int pixelWidth, int pixelHeight,
                                                  int iterations,
                                                  IProgress<double> progress,
                                                  CancellationToken cancelToken)
    {
        return Task.Run(() =>
        {
            int[] iterationCounts = new int[pixelWidth * pixelHeight];
            int index = 0;

            for (int row = 0; row < pixelHeight; row++)
            {
                progress.Report((double)row / pixelHeight);
                cancelToken.ThrowIfCancellationRequested();

                double y = center.Imaginary + height / 2 - row * height / pixelHeight;

                for (int col = 0; col < pixelWidth; col++)
                {
                    double x = center.Real - width / 2 + col * width / pixelWidth;
                    Complex c = new Complex(x, y);

                    if ((c - new Complex(-1, 0)).Magnitude < 1.0 / 4)
                    {
                        iterationCounts[index++] = -1;
                    }
                    // http://www.reenigne.org/blog/algorithm-for-mandelbrot-cardioid/
                    else if (c.Magnitude * c.Magnitude * (8 * c.Magnitude * c.Magnitude - 3) < 3.0 / 32 - c.Real)
                    {
                        iterationCounts[index++] = -1;
                    }
                    else
                    {
                        Complex z = 0;
                        int iteration = 0;

                        do
                        {
                            z = z * z + c;
                            iteration++;
                        }
                        while (iteration < iterations && z.Magnitude < 2);

                        if (iteration == iterations)
                        {
                            iterationCounts[index++] = -1;
                        }
                        else
                        {
                            iterationCounts[index++] = iteration;
                        }
                    }
                }
            }
            return new BitmapInfo(pixelWidth, pixelHeight, iterationCounts);
        }, cancelToken);
    }
}
```

方法返回类型为的对象 `BitmapInfo` ，该对象提供创建位图的信息：

```csharp
class BitmapInfo
{
    public BitmapInfo(int pixelWidth, int pixelHeight, int[] iterationCounts)
    {
        PixelWidth = pixelWidth;
        PixelHeight = pixelHeight;
        IterationCounts = iterationCounts;
    }

    public int PixelWidth { private set; get; }

    public int PixelHeight { private set; get; }

    public int[] IterationCounts { private set; get; }
}
```

**Mandelbrot 动画**XAML 文件包括两个 `Label` 视图 `ProgressBar` ：和和 `Button` `SKCanvasView` ：

```csharp
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="MandelAnima.MainPage"
             Title="Mandelbrot Animation">

    <StackLayout>
        <Label x:Name="statusLabel"
               HorizontalTextAlignment="Center" />
        <ProgressBar x:Name="progressBar" />

        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <StackLayout Orientation="Horizontal"
                     Padding="5">
            <Label x:Name="storageLabel"
                   VerticalOptions="Center" />

            <Button x:Name="deleteButton"
                    Text="Delete All"
                    HorizontalOptions="EndAndExpand"
                    Clicked="OnDeleteButtonClicked" />
        </StackLayout>
    </StackLayout>
</ContentPage>
```

代码隐藏文件首先定义三个关键常量和一组位图：

```csharp
public partial class MainPage : ContentPage
{
    const int COUNT = 10;           // The number of bitmaps in the animation.
                                    // This can go up to 50!

    const int BITMAP_SIZE = 1000;   // Program uses square bitmaps exclusively

    // Uncomment just one of these, or define your own
    static readonly Complex center = new Complex(-1.17651152924355, 0.298520986549558);
    //   static readonly Complex center = new Complex(-0.774693089457127, 0.124226621261617);
    //   static readonly Complex center = new Complex(-0.556624880053304, 0.634696788141351);

    SKBitmap[] bitmaps = new SKBitmap[COUNT];   // array of bitmaps
    ···
}
```

在某些时候，你可能需要将 `COUNT` 值更改为50，以查看动画的全部范围。 大于50的值将不起作用。 相对于48或更高的缩放级别，对双精度浮点数的解析对于 Mandelbrot 集计算来说是不够的。 有关_创建移动应用 Xamarin.Forms _的第684页上讨论了此问题。

`center`值非常重要。 这是动画缩放的焦点。 该文件中的三个值是在684第20章_创建移动应用 Xamarin.Forms _程序的第20章的第20个屏幕截图中所使用的值，但您可以在该章节中试验程序，以提供您自己的值之一。

**Mandelbrot 动画**示例将这些 `COUNT` 位图存储在本地应用程序存储中。 50位图需要在你的设备上存储超过 20 mb 的存储空间，因此你可能想要知道这些位图占用了多少存储空间，并且在某些时候，你可能想要将其全部删除。 这就是这两个方法在类的底部 `MainPage` ：

```csharp
public partial class MainPage : ContentPage
{
    ···
    void TallyBitmapSizes()
    {
        long fileSize = 0;

        foreach (string filename in Directory.EnumerateFiles(FolderPath()))
        {
            fileSize += new FileInfo(filename).Length;
        }

        storageLabel.Text = $"Total storage: {fileSize:N0} bytes";
    }

    void OnDeleteButtonClicked(object sender, EventArgs args)
    {
        foreach (string filepath in Directory.EnumerateFiles(FolderPath()))
        {
            File.Delete(filepath);
        }

        TallyBitmapSizes();
    }
}
```

您可以删除本地存储区中的位图，同时程序会将这些位图动态保存在内存中。 但下次运行该程序时，将需要重新创建位图。

存储在本地应用程序存储中的位图 `center` 会将值纳入其文件名，因此，如果更改此 `center` 设置，则在存储中将不会替换现有的位图，并将继续占用空间。

下面是 `MainPage` 用来构造文件名的方法，以及 `MakePixel` 用于根据颜色组件定义像素值的方法：

```csharp
public partial class MainPage : ContentPage
{
    ···
    // File path for storing each bitmap in local storage
    string FolderPath() =>
        Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData);

    string FilePath(int zoomLevel) =>
        Path.Combine(FolderPath(),
                     String.Format("R{0}I{1}Z{2:D2}.png", center.Real, center.Imaginary, zoomLevel));

    // Form bitmap pixel for Rgba8888 format
    uint MakePixel(byte alpha, byte red, byte green, byte blue) =>
        (uint)((alpha << 24) | (blue << 16) | (green << 8) | red);
    ···
}
```

`zoomLevel`参数的 `FilePath` 范围为0到 `COUNT` 常数减1之间的范围。

`MainPage`构造函数调用 `LoadAndStartAnimation` 方法：

```csharp
public partial class MainPage : ContentPage
{
    ···
    public MainPage()
    {
        InitializeComponent();

        LoadAndStartAnimation();
    }
    ···
}
```

此 `LoadAndStartAnimation` 方法负责访问应用程序本地存储，以便加载在以前运行程序时可能创建的任何位图。 它会遍历 `zoomLevel` 0 到之间的值 `COUNT` 。 如果该文件存在，它将加载到 `bitmaps` 数组中。 否则，需要通过调用为特定的 `center` 和值创建位图 `zoomLevel` `Mandelbrot.CalculateAsync` 。 此方法获取每个像素的迭代次数，此方法会将此方法转换为颜色：

```csharp
public partial class MainPage : ContentPage
{
    ···
    async void LoadAndStartAnimation()
    {
        // Show total bitmap storage
        TallyBitmapSizes();

        // Create progressReporter for async operation
        Progress<double> progressReporter =
            new Progress<double>((double progress) => progressBar.Progress = progress);

        // Create (unused) CancellationTokenSource for async operation
        CancellationTokenSource cancelTokenSource = new CancellationTokenSource();

        // Loop through all the zoom levels
        for (int zoomLevel = 0; zoomLevel < COUNT; zoomLevel++)
        {
            // If the file exists, load it
            if (File.Exists(FilePath(zoomLevel)))
            {
                statusLabel.Text = $"Loading bitmap for zoom level {zoomLevel}";

                using (Stream stream = File.OpenRead(FilePath(zoomLevel)))
                {
                    bitmaps[zoomLevel] = SKBitmap.Decode(stream);
                }
            }
            // Otherwise, create a new bitmap
            else
            {
                statusLabel.Text = $"Creating bitmap for zoom level {zoomLevel}";

                CancellationToken cancelToken = cancelTokenSource.Token;

                // Do the (generally lengthy) Mandelbrot calculation
                BitmapInfo bitmapInfo =
                    await Mandelbrot.CalculateAsync(center,
                                                    4 / Math.Pow(2, zoomLevel),
                                                    4 / Math.Pow(2, zoomLevel),
                                                    BITMAP_SIZE, BITMAP_SIZE,
                                                    (int)Math.Pow(2, 10), progressReporter, cancelToken);

                // Create bitmap & get pointer to the pixel bits
                SKBitmap bitmap = new SKBitmap(BITMAP_SIZE, BITMAP_SIZE, SKColorType.Rgba8888, SKAlphaType.Opaque);
                IntPtr basePtr = bitmap.GetPixels();

                // Set pixel bits to color based on iteration count
                for (int row = 0; row < bitmap.Width; row++)
                    for (int col = 0; col < bitmap.Height; col++)
                    {
                        int iterationCount = bitmapInfo.IterationCounts[row * bitmap.Width + col];
                        uint pixel = 0xFF000000;            // black

                        if (iterationCount != -1)
                        {
                            double proportion = (iterationCount / 32.0) % 1;
                            byte red = 0, green = 0, blue = 0;

                            if (proportion < 0.5)
                            {
                                red = (byte)(255 * (1 - 2 * proportion));
                                blue = (byte)(255 * 2 * proportion);
                            }
                            else
                            {
                                proportion = 2 * (proportion - 0.5);
                                green = (byte)(255 * proportion);
                                blue = (byte)(255 * (1 - proportion));
                            }

                            pixel = MakePixel(0xFF, red, green, blue);
                        }

                        // Calculate pointer to pixel
                        IntPtr pixelPtr = basePtr + 4 * (row * bitmap.Width + col);

                        unsafe     // requires compiling with unsafe flag
                        {
                            *(uint*)pixelPtr.ToPointer() = pixel;
                        }
                    }

                // Save as PNG file
                SKData data = SKImage.FromBitmap(bitmap).Encode();

                try
                {
                    File.WriteAllBytes(FilePath(zoomLevel), data.ToArray());
                }
                catch
                {
                    // Probably out of space, but just ignore
                }

                // Store in array
                bitmaps[zoomLevel] = bitmap;

                // Show new bitmap sizes
                TallyBitmapSizes();
            }

            // Display the bitmap
            bitmapIndex = zoomLevel;
            canvasView.InvalidateSurface();
        }

        // Now start the animation
        stopwatch.Start();
        Device.StartTimer(TimeSpan.FromMilliseconds(16), OnTimerTick);
    }
    ···
}
```

请注意，该程序将这些位图存储在本地应用程序存储中，而不是存储在设备的照片库中。 .NET Standard 2.0 库允许 `File.OpenRead` 为此任务使用熟悉的和 `File.WriteAllBytes` 方法。

创建所有位图或将其加载到内存中后，该方法将启动一个 `Stopwatch` 对象并调用 `Device.StartTimer` 。 `OnTimerTick`每16毫秒调用一次方法。

`OnTimerTick` 计算介于 `time` 0 到6000次之间的值（以毫秒为单位） `COUNT` ，每个位图的显示时间为6秒 apportions。 `progress`该值使用 `Math.Sin` 值创建正弦动画，该动画在循环开始时速度较慢，结束时速度较慢。

此 `progress` 值的范围为0到 `COUNT` 。 这意味着的整数部分 `progress` 是数组的索引 `bitmaps` ，而的小数部分 `progress` 指示该特定位图的缩放级别。 这些值存储在 `bitmapIndex` 和字段中 `bitmapProgress` ，并由 `Label` 和显示 `Slider` 在 XAML 文件中。 `SKCanvasView`无效，以更新位图显示：

```csharp
public partial class MainPage : ContentPage
{
    ···
    Stopwatch stopwatch = new Stopwatch();      // for the animation
    int bitmapIndex;
    double bitmapProgress = 0;
    ···
    bool OnTimerTick()
    {
        int cycle = 6000 * COUNT;       // total cycle length in milliseconds

        // Time in milliseconds from 0 to cycle
        int time = (int)(stopwatch.ElapsedMilliseconds % cycle);

        // Make it sinusoidal, including bitmap index and gradation between bitmaps
        double progress = COUNT * 0.5 * (1 + Math.Sin(2 * Math.PI * time / cycle - Math.PI / 2));

        // These are the field values that the PaintSurface handler uses
        bitmapIndex = (int)progress;
        bitmapProgress = progress - bitmapIndex;

        // It doesn't often happen that we get up to COUNT, but an exception would be raised
        if (bitmapIndex < COUNT)
        {
            // Show progress in UI
            statusLabel.Text = $"Displaying bitmap for zoom level {bitmapIndex}";
            progressBar.Progress = bitmapProgress;

            // Update the canvas
            canvasView.InvalidateSurface();
        }

        return true;
    }
    ···
}
```

最后， `PaintSurface` 的处理程序 `SKCanvasView` 计算一个目标矩形，以尽可能大地显示位图，同时保持纵横比。 源矩形基于 `bitmapProgress` 值。 `fraction`此处计算的值范围从0到0时，如果为0，则 `bitmapProgress` 显示整个位图; 如果为1，则为 0.25 `bitmapProgress` ; 如果为1，则显示位图的宽度和高度的一半，有效地放大：

```csharp
public partial class MainPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        if (bitmaps[bitmapIndex] != null)
        {
            // Determine destination rect as square in canvas
            int dimension = Math.Min(info.Width, info.Height);
            float x = (info.Width - dimension) / 2;
            float y = (info.Height - dimension) / 2;
            SKRect destRect = new SKRect(x, y, x + dimension, y + dimension);

            // Calculate source rectangle based on fraction:
            //  bitmapProgress == 0: full bitmap
            //  bitmapProgress == 1: half of length and width of bitmap
            float fraction = 0.5f * (1 - (float)Math.Pow(2, -bitmapProgress));
            SKBitmap bitmap = bitmaps[bitmapIndex];
            int width = bitmap.Width;
            int height = bitmap.Height;
            SKRect sourceRect = new SKRect(fraction * width, fraction * height,
                                           (1 - fraction) * width, (1 - fraction) * height);

            // Display the bitmap
            canvas.DrawBitmap(bitmap, sourceRect, destRect);
        }
    }
    ···
}
```

下面是正在运行的程序：

[![Mandelbrot 动画](animating-images/MandelbrotAnimation.png "Mandelbrot 动画")](animating-images/MandelbrotAnimation-Large.png#lightbox)

## <a name="gif-animation"></a>GIF 动画

图形交换格式 (GIF) 规范包含一项功能，该功能允许单个 GIF 文件包含多个连续帧，这种情况通常会出现在循环中。 这些文件称为 _动画 gif_。 Web 浏览器可以播放动态 Gif，SkiaSharp 允许应用程序从动画 GIF 文件中提取帧并按顺序显示。

[SkiaSharpFormsDemos](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)示例包含一个名为**Newtons_cradle_animation_book_2.gif**的动画 GIF 资源，由 DemonDeLuxe 创建并从维基百科的[牛顿底座](https://en.wikipedia.org/wiki/Newton%27s_cradle)页面下载。 **动画 GIF**页面包含一个 XAML 文件，该文件提供该信息并实例化 `SKCanvasView` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Bitmaps.AnimatedGifPage"
             Title="Animated GIF">

    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="0"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Label Text="GIF file by DemonDeLuxe from Wikipedia Newton's Cradle page"
               Grid.Row="1"
               Margin="0, 5"
               HorizontalTextAlignment="Center" />
    </Grid>
</ContentPage>
```

代码隐藏文件未通用化为播放任何动画 GIF 文件。 它将忽略某些可用的信息（特别是重复计数），只需在循环中播放动画 GIF 即可。

使用 SkisSharp 来提取动画 GIF 文件的帧似乎未记录在任何位置，因此下面的代码说明比平时更详细：

经过动画处理的 GIF 文件解码出现在页的构造函数中，要求 `Stream` 使用引用位图的对象创建 `SKManagedStream` 对象，然后使用对象 [`SKCodec`](xref:SkiaSharp.SKCodec) 。 [`FrameCount`](xref:SkiaSharp.SKCodec.FrameCount)属性指示构成动画的帧数。

这些帧最终将另存为单独的位图，因此构造函数使用在 `FrameCount` `SKBitmap` 每个帧的持续时间内分配一个类型为的数组和两个 `int` 数组，并 (以使动画逻辑) 累计的持续时间。

[`FrameInfo`](xref:SkiaSharp.SKCodec.FrameInfo)类的属性 `SKCodec` 是值的数组 [`SKCodecFrameInfo`](xref:SkiaSharp.SKCodecFrameInfo) ，每个帧一个值的数组，但此程序从该结构中获取的唯一内容是帧的 [`Duration`](xref:SkiaSharp.SKCodecFrameInfo.Duration) （以毫秒为单位）。

`SKCodec` 定义一个类型为 [`Info`](xref:SkiaSharp.SKCodec.Info) 的属性 [`SKImageInfo`](xref:SkiaSharp.SKImageInfo) ，但 `SKImageInfo` 该值指示至少 (此图像) 颜色类型为 `SKColorType.Index8` ，这意味着每个像素是一个颜色类型的索引。 为了避免麻烦使用颜色表，该程序使用该 [`Width`](xref:SkiaSharp.SKImageInfo.Width) [`Height`](xref:SkiaSharp.SKImageInfo.Height) 结构中的和信息来构造自己的全颜色 `ImageInfo` 值。 每个 `SKBitmap` 都是从创建的。

的 `GetPixels` 方法 `SKBitmap` 返回 `IntPtr` 引用该位图的像素位的。 尚未设置这些像素位。 `IntPtr`传递给的 [`GetPixels`](xref:SkiaSharp.SKCodec.GetPixels(SkiaSharp.SKImageInfo,System.IntPtr,SkiaSharp.SKCodecOptions)) 方法之一 `SKCodec` 。 该方法将帧从 GIF 文件复制到所引用的内存空间中 `IntPtr` 。 [`SKCodecOptions`](xref:SkiaSharp.SKCodecOptions)构造函数指示帧数：

```csharp
public partial class AnimatedGifPage : ContentPage
{
    SKBitmap[] bitmaps;
    int[] durations;
    int[] accumulatedDurations;
    int totalDuration;
    ···

    public AnimatedGifPage ()
    {
        InitializeComponent ();

        string resourceID = "SkiaSharpFormsDemos.Media.Newtons_cradle_animation_book_2.gif";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        using (SKManagedStream skStream = new SKManagedStream(stream))
        using (SKCodec codec = SKCodec.Create(skStream))
        {
            // Get frame count and allocate bitmaps
            int frameCount = codec.FrameCount;
            bitmaps = new SKBitmap[frameCount];
            durations = new int[frameCount];
            accumulatedDurations = new int[frameCount];

            // Note: There's also a RepetitionCount property of SKCodec not used here

            // Loop through the frames
            for (int frame = 0; frame < frameCount; frame++)
            {
                // From the FrameInfo collection, get the duration of each frame
                durations[frame] = codec.FrameInfo[frame].Duration;

                // Create a full-color bitmap for each frame
                SKImageInfo imageInfo = code.new SKImageInfo(codec.Info.Width, codec.Info.Height);
                bitmaps[frame] = new SKBitmap(imageInfo);

                // Get the address of the pixels in that bitmap
                IntPtr pointer = bitmaps[frame].GetPixels();

                // Create an SKCodecOptions value to specify the frame
                SKCodecOptions codecOptions = new SKCodecOptions(frame, false);

                // Copy pixels from the frame into the bitmap
                codec.GetPixels(imageInfo, pointer, codecOptions);
            }

            // Sum up the total duration
            for (int frame = 0; frame < durations.Length; frame++)
            {
                totalDuration += durations[frame];
            }

            // Calculate the accumulated durations
            for (int frame = 0; frame < durations.Length; frame++)
            {
                accumulatedDurations[frame] = durations[frame] +
                    (frame == 0 ? 0 : accumulatedDurations[frame - 1]);
            }
        }
    }
    ···
}
```

尽管 `IntPtr` 值不 `unsafe` 是必需的，但不需要任何代码，因为决不会 `IntPtr` 转换为 c # 指针值。

提取每个帧后，构造函数将合计所有帧的持续时间，然后用累计持续时间初始化另一个数组。

代码隐藏文件的其余部分专用于动画。 `Device.StartTimer`方法用于启动计时器， `OnTimerTick` 回调使用 `Stopwatch` 对象来确定所用的时间（以毫秒为单位）。 遍历累计持续时间数组足以找到当前帧：

```csharp
public partial class AnimatedGifPage : ContentPage
{
    SKBitmap[] bitmaps;
    int[] durations;
    int[] accumulatedDurations;
    int totalDuration;

    Stopwatch stopwatch = new Stopwatch();
    bool isAnimating;

    int currentFrame;
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
        int msec = (int)(stopwatch.ElapsedMilliseconds % totalDuration);
        int frame = 0;

        // Find the frame based on the elapsed time
        for (frame = 0; frame < accumulatedDurations.Length; frame++)
        {
            if (msec < accumulatedDurations[frame])
            {
                break;
            }
        }

        // Save in a field and invalidate the SKCanvasView.
        if (currentFrame != frame)
        {
            currentFrame = frame;
            canvasView.InvalidateSurface();
        }

        return isAnimating;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Black);

        // Get the bitmap and center it
        SKBitmap bitmap = bitmaps[currentFrame];
        canvas.DrawBitmap(bitmap,info.Rect, BitmapStretch.Uniform);
    }
}
```

每次 `currentframe` 变量更改时， `SKCanvasView` 都会失效，并显示新帧：

[![动画 GIF](animating-images/AnimatedGif.png "动画 GIF")](animating-images/AnimatedGif-Large.png#lightbox)

当然，您需要自己运行程序以查看动画。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [Mandelbrot 动画 (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-mandelanima)