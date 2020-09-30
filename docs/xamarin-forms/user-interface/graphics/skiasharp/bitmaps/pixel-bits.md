---
title: 访问 SkiaSharp 位图像素位
description: 了解访问和修改 SkiaSharp 位图的像素位的各种方法。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: DBB58522-F816-4A8C-96A5-E0236F16A5C6
author: davidbritch
ms.author: dabritch
ms.date: 07/11/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7f0b7c61d44170ab97b060a16693287c28903660
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562959"
---
# <a name="accessing-skiasharp-bitmap-pixel-bits"></a>访问 SkiaSharp 位图像素位

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

正如你在将 [**SkiaSharp 位图保存到文件**](saving.md)这篇文章中所看到的那样，位图通常以压缩格式存储在文件中，例如 JPEG 或 PNG。 在相反中，不压缩存储在内存中的 SkiaSharp 位图。 它存储为一系列有序像素。 此压缩格式便于将位图传输到显示图面。

SkiaSharp 位图占用的内存块以非常简单的方式进行组织：从左到右，从第一行开始，然后继续第二行。 对于全颜色位图，每个像素由四个字节组成，这意味着位图所需的总内存空间是其宽度和高度的四倍。

本文介绍了应用程序如何可以直接通过访问位图的像素内存块来访问这些像素，或间接访问。 在某些情况下，程序可能想要分析图像的像素并构造某种类型的直方图。 更常见的情况是，应用程序可以通过算法创建构成位图的像素来构造唯一的映像：

![像素位示例](pixel-bits-images/PixelBitsSample.png "像素位示例")

## <a name="the-techniques"></a>方法

SkiaSharp 提供了几种用于访问位图的像素位的方法。 选择哪一种通常是编码便利 (之间的折衷，这与维护和) 和性能调试相关。 在大多数情况下，你将使用的以下方法和属性之一 `SKBitmap` 来访问位图的像素：

- `GetPixel`和 `SetPixel` 方法允许您获取或设置单个像素的颜色。
- `Pixels`属性获取整个位图的像素颜色数组，或设置颜色的数组。
- `GetPixels` 返回位图使用的像素内存的地址。
- `SetPixels` 替换位图使用的像素内存的地址。

您可以将前两种方法视为 "高级别"，将第二种方法视为 "低级别"。 还可以使用其他一些方法和属性，但这些方法和属性是最有价值的。

为了使你能够查看这些技术之间的性能差异， [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) 应用程序包含一个名为 " **渐变位图** " 的页面，它创建一个位图，该位图的像素结合了红色和蓝色底纹来创建渐变。 此程序创建了8个不同的位图副本，所有这些副本都使用不同的方法来设置位图像素。 其中的每个位图都是在单独的方法中创建的，该方法还设置了方法的简短文本说明，并计算设置所有像素所需的时间。 每个方法都循环访问像素设置逻辑100次，以获得更好的性能估算。

### <a name="the-setpixel-method"></a>SetPixel 方法

如果只需设置或获取几个单个像素，则 [`SetPixel`](xref:SkiaSharp.SKBitmap.SetPixel(System.Int32,System.Int32,SkiaSharp.SKColor)) 和 [`GetPixel`](xref:SkiaSharp.SKBitmap.GetPixel(System.Int32,System.Int32)) 方法是理想的。 对于这两种方法，请指定整数列和行。 无论使用何种像素格式，这两种方法都可让你获取或设置像素作为 `SKColor` 值：

```csharp
bitmap.SetPixel(col, row, color);

SKColor color = bitmap.GetPixel(col, row);
```

`col`参数的范围必须介于0到 `Width` 位图属性的范围之间， `row` 范围是从0到小于 `Height` 属性。

下面是 **渐变位图** 中的方法，该方法使用方法设置位图的内容 `SetPixel` 。 位图为 256 x 256 像素， `for` 循环使用值范围进行硬编码：

```csharp
public class GradientBitmapPage : ContentPage
{
    const int REPS = 100;

    Stopwatch stopwatch = new Stopwatch();
    ···
    SKBitmap FillBitmapSetPixel(out string description, out int milliseconds)
    {
        description = "SetPixel";
        SKBitmap bitmap = new SKBitmap(256, 256);

        stopwatch.Restart();

        for (int rep = 0; rep < REPS; rep++)
            for (int row = 0; row < 256; row++)
                for (int col = 0; col < 256; col++)
                {
                    bitmap.SetPixel(col, row, new SKColor((byte)col, 0, (byte)row));
                }

        milliseconds = (int)stopwatch.ElapsedMilliseconds;
        return bitmap;
    }
    ···
}
```

为每个像素设置的颜色具有与位图列相等的红色分量和一个等于行的蓝色分量。 生成的位图的左上方为黑色，在右上角为红色，在右下方为红色，在其他位置有渐变。

`SetPixel`此方法被称为65536次，无论此方法的效率如何，通常不是很好的做法是，如果有替代方法，则很可能会调用它。 幸运的是，有几个替代方法。

### <a name="the-pixels-property"></a>"像素" 属性

`SKBitmap` 定义一个 [`Pixels`](xref:SkiaSharp.SKBitmap.Pixels) 属性，该属性返回 `SKColor` 整个位图的值数组。 你还可以使用 `Pixels` 设置位图的颜色值数组：

```csharp
SKColor[] pixels = bitmap.Pixels;

bitmap.Pixels = pixels;
```

像素排列在数组中，从第一行开始，从左到右，然后是第二行，依此类推。 数组中的总颜色数等于位图宽度和高度的乘积。

尽管此属性看似有效，但请记住，像素是从位图复制到数组中，并从数组返回到位图，并将像素转换为 `SKColor` 值。

下面是 `GradientBitmapPage` 使用属性设置位图的类中的方法 `Pixels` 。 方法分配一个 `SKColor` 所需大小的数组，但它可能已使用 `Pixels` 属性创建该数组：

```csharp
SKBitmap FillBitmapPixelsProp(out string description, out int milliseconds)
{
    description = "Pixels property";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    SKColor[] pixels = new SKColor[256 * 256];

    for (int rep = 0; rep < REPS; rep++)
        for (int row = 0; row < 256; row++)
            for (int col = 0; col < 256; col++)
            {
                pixels[256 * row + col] = new SKColor((byte)col, 0, (byte)row);
            }

    bitmap.Pixels = pixels;

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

请注意， `pixels` 需要从和变量计算数组的索引 `row` `col` 。 在此) 示例中，将行乘以每行 (256 的像素数，然后添加列。

`SKBitmap` 还定义了一个类似的 `Bytes` 属性，该属性为整个位图返回一个字节数组，但对于全颜色位图更繁琐。

### <a name="the-getpixels-pointer"></a>GetPixels 指针

访问位图像素的最强大的方法可能是 [`GetPixels`](xref:SkiaSharp.SKBitmap.GetPixels) ，而不是与 `GetPixel` 方法或 `Pixels` 属性混淆。 你将立即注意到与的不同之处在于 `GetPixels` ，它返回 c # 编程中不太常见的内容：

```csharp
IntPtr pixelsAddr = bitmap.GetPixels();
```

.NET [`IntPtr`](xref:System.IntPtr) 类型表示指针。 调用此方法是 `IntPtr` 因为它是运行程序的计算机的本机处理器上的整数长度，通常为32位或64位。 `IntPtr`返回的 `GetPixels` 是位图对象用于存储其像素的实际内存块的地址。

可以使用方法将转换为 `IntPtr` c # 指针类型 [`ToPointer`](xref:System.IntPtr.ToPointer) 。 C # 指针语法与 C 和 c + + 相同：

```csharp
byte* ptr = (byte*)pixelsAddr.ToPointer();
```

`ptr`变量的类型为_byte 指针_。 此 `ptr` 变量可用于访问用于存储位图像素的单个内存字节。 使用类似于下面的代码从内存中读取一个字节，或者将一个字节写入内存：

```csharp
byte pixelComponent = *ptr;

*ptr = pixelComponent;
```

在此上下文中，星号是 c # _间接寻址运算符_ ，用于引用所指向的内存的内容 `ptr` 。 最初， `ptr` 指向位图第一行的第一个像素的第一个字节，但您可以对变量执行算术运算， `ptr` 以将其移动到位图内的其他位置。

一个缺点是只能 `ptr` 在用关键字标记的代码块中使用此变量 `unsafe` 。 此外，必须将程序集标记为允许不安全的块。 这是在项目的属性中完成的。

在 c # 中使用指针非常强大，但也非常危险。 需要注意的是，不要访问超出指针所需引用的内存。 这就是指针使用与 "不安全" 一词相关的原因。

下面是使用方法的类中的方法 `GradientBitmapPage` `GetPixels` 。 请注意 `unsafe` 包含使用字节指针的所有代码的块：

```csharp
SKBitmap FillBitmapBytePtr(out string description, out int milliseconds)
{
    description = "GetPixels byte ptr";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    IntPtr pixelsAddr = bitmap.GetPixels();

    unsafe
    {
        for (int rep = 0; rep < REPS; rep++)
        {
            byte* ptr = (byte*)pixelsAddr.ToPointer();

            for (int row = 0; row < 256; row++)
                for (int col = 0; col < 256; col++)
                {
                    *ptr++ = (byte)(col);   // red
                    *ptr++ = 0;             // green
                    *ptr++ = (byte)(row);   // blue
                    *ptr++ = 0xFF;          // alpha
                }
        }
    }

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

`ptr`第一次从方法获取变量时 `ToPointer` ，它指向位图第一行最左侧像素的第一个字节。 `for`和的循环 `row` `col` 已设置，因此 `ptr` `++` 每个像素的每个字节都被设置后，可以与运算符递增。 对于其他99循环通过像素， `ptr` 必须将设置回位图的开头。

每个像素均为四个字节的内存，因此必须单独设置每个字节。 此处的代码假定字节的顺序为红色、绿色、蓝色和 alpha，这与 `SKColorType.Rgba8888` 颜色类型一致。 你可能会想起这是 iOS 和 Android 的默认颜色类型，而不是通用 Windows 平台的颜色类型。 默认情况下，UWP 创建颜色类型为的位图 `SKColorType.Bgra8888` 。 出于此原因，应在该平台上看到一些不同的结果！

可以将从返回的值强制转换 `ToPointer` 为指针， `uint` 而不是 `byte` 指针。 这允许在一条语句中访问整个像素。 将 `++` 运算符应用于该指针会按四个字节递增，以指向下一个像素：

```csharp
public class GradientBitmapPage : ContentPage
{
    ···
    SKBitmap FillBitmapUintPtr(out string description, out int milliseconds)
    {
        description = "GetPixels uint ptr";
        SKBitmap bitmap = new SKBitmap(256, 256);

        stopwatch.Restart();

        IntPtr pixelsAddr = bitmap.GetPixels();

        unsafe
        {
            for (int rep = 0; rep < REPS; rep++)
            {
                uint* ptr = (uint*)pixelsAddr.ToPointer();

                for (int row = 0; row < 256; row++)
                    for (int col = 0; col < 256; col++)
                    {
                        *ptr++ = MakePixel((byte)col, 0, (byte)row, 0xFF);
                    }
            }
        }

        milliseconds = (int)stopwatch.ElapsedMilliseconds;
        return bitmap;
    }
    ···
    [MethodImpl(MethodImplOptions.AggressiveInlining)]
    uint MakePixel(byte red, byte green, byte blue, byte alpha) =>
            (uint)((alpha << 24) | (blue << 16) | (green << 8) | red);
    ···
}
```

该像素是使用方法设置的 `MakePixel` ，该方法用红色、绿色、蓝色和 alpha 分量构造一个整数像素。 请记住， `SKColorType.Rgba8888` 格式具有如下所示的像素字节顺序：

RR GG BB AA

但对应于这些字节的整数为：

AABBGGRR

此整数的最小有效字节首先按照小字节序体系结构存储。 `MakePixel`对于颜色类型为的位图，此方法将不能正常工作 `Bgra8888` 。

`MakePixel`方法使用 [`MethodImplOptions.AggressiveInlining`](xref:System.Runtime.CompilerServices.MethodImplOptions) 选项进行标记，以鼓励编译器避免使此方法为单独的方法，而是编译调用方法的代码。 这应该会提高性能。

有趣的是，该 `SKColor` 结构定义从 `SKColor` 到无符号整数的显式转换，这意味着 `SKColor` 可创建一个值，并且可以使用的转换， `uint` 而不是 `MakePixel` ：

```csharp
SKBitmap FillBitmapUintPtrColor(out string description, out int milliseconds)
{
    description = "GetPixels SKColor";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    IntPtr pixelsAddr = bitmap.GetPixels();

    unsafe
    {
        for (int rep = 0; rep < REPS; rep++)
        {
            uint* ptr = (uint*)pixelsAddr.ToPointer();

            for (int row = 0; row < 256; row++)
                for (int col = 0; col < 256; col++)
                {
                    *ptr++ = (uint)new SKColor((byte)col, 0, (byte)row);
                }
        }
    }

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

唯一的问题是：是值的整数格式 `SKColor` `SKColorType.Rgba8888` （以颜色类型的顺序）或 `SKColorType.Bgra8888` 颜色类型，还是完全是其他内容？ 此问题的答案应该很快就会发现。

### <a name="the-setpixels-method"></a>SetPixels 方法

`SKBitmap` 还定义了一个名为的方法 [`SetPixels`](xref:SkiaSharp.SKBitmap.SetPixels(System.IntPtr)) ，您可以调用此方法，如下所示：

```csharp
bitmap.SetPixels(intPtr);
```

请记住， `GetPixels` 获取了 `IntPtr` 引用位图用来存储其像素的内存块的。 `SetPixels`调用将该内存块_替换_为由 `IntPtr` 指定的作为参数引用的内存块 `SetPixels` 。 然后，位图释放以前使用过的内存块。 下次调用时 `GetPixels` ，它将获取用设置的内存块 `SetPixels` 。

第一种情况是，如果 `SetPixels` 给出的能力和性能都比 `GetPixels` 低得多。 `GetPixels`获取位图内存块并对其进行访问。 `SetPixels`分配并访问一些内存后，将其设置为位图内存块。

但使用 `SetPixels` 提供了独特的语法优势：它允许使用数组访问位图像素位。 下面是中演示此方法的方法 `GradientBitmapPage` 。 方法首先定义一个多维字节数组，该数组对应于位图的像素数。 第一个维度是行，第二个维度是列，第三个维度对应于每个像素的四个分量：

```csharp
SKBitmap FillBitmapByteBuffer(out string description, out int milliseconds)
{
    description = "SetPixels byte buffer";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    byte[,,] buffer = new byte[256, 256, 4];

    for (int rep = 0; rep < REPS; rep++)
        for (int row = 0; row < 256; row++)
            for (int col = 0; col < 256; col++)
            {
                buffer[row, col, 0] = (byte)col;   // red
                buffer[row, col, 1] = 0;           // green
                buffer[row, col, 2] = (byte)row;   // blue
                buffer[row, col, 3] = 0xFF;        // alpha
            }

    unsafe
    {
        fixed (byte* ptr = buffer)
        {
            bitmap.SetPixels((IntPtr)ptr);
        }
    }

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

然后，在使用像素填充数组后，将 `unsafe` 使用块和 `fixed` 语句获取指向此数组的字节指针。 然后，可以将该字节指针强制转换为 `IntPtr` ，以传递到 `SetPixels` 。

你创建的数组不必是字节数组。 它可以是一个整数数组，其中的行和列只有两个维度：

```csharp
SKBitmap FillBitmapUintBuffer(out string description, out int milliseconds)
{
    description = "SetPixels uint buffer";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    uint[,] buffer = new uint[256, 256];

    for (int rep = 0; rep < REPS; rep++)
        for (int row = 0; row < 256; row++)
            for (int col = 0; col < 256; col++)
            {
                buffer[row, col] = MakePixel((byte)col, 0, (byte)row, 0xFF);
            }

    unsafe
    {
        fixed (uint* ptr = buffer)
        {
            bitmap.SetPixels((IntPtr)ptr);
        }
    }

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

`MakePixel`再次使用方法将颜色组件合并为32位像素。

仅出于完整性考虑，下面是相同的代码，但将 `SKColor` 值强制转换为无符号整数：

```csharp
SKBitmap FillBitmapUintBufferColor(out string description, out int milliseconds)
{
    description = "SetPixels SKColor";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    uint[,] buffer = new uint[256, 256];

    for (int rep = 0; rep < REPS; rep++)
        for (int row = 0; row < 256; row++)
            for (int col = 0; col < 256; col++)
            {
                buffer[row, col] = (uint)new SKColor((byte)col, 0, (byte)row);
            }

    unsafe
    {
        fixed (uint* ptr = buffer)
        {
            bitmap.SetPixels((IntPtr)ptr);
        }
    }

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

### <a name="comparing-the-techniques"></a>比较技术

" **渐变颜色** " 页的构造函数将调用上面所示的所有8个方法，并保存结果：

```csharp
public class GradientBitmapPage : ContentPage
{
    ···
    string[] descriptions = new string[8];
    SKBitmap[] bitmaps = new SKBitmap[8];
    int[] elapsedTimes = new int[8];

    SKCanvasView canvasView;

    public GradientBitmapPage ()
    {
        Title = "Gradient Bitmap";

        bitmaps[0] = FillBitmapSetPixel(out descriptions[0], out elapsedTimes[0]);
        bitmaps[1] = FillBitmapPixelsProp(out descriptions[1], out elapsedTimes[1]);
        bitmaps[2] = FillBitmapBytePtr(out descriptions[2], out elapsedTimes[2]);
        bitmaps[4] = FillBitmapUintPtr(out descriptions[4], out elapsedTimes[4]);
        bitmaps[6] = FillBitmapUintPtrColor(out descriptions[6], out elapsedTimes[6]);
        bitmaps[3] = FillBitmapByteBuffer(out descriptions[3], out elapsedTimes[3]);
        bitmaps[5] = FillBitmapUintBuffer(out descriptions[5], out elapsedTimes[5]);
        bitmaps[7] = FillBitmapUintBufferColor(out descriptions[7], out elapsedTimes[7]);

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }
    ···
}
```

构造函数通过创建 `SKCanvasView` 以显示生成的位图来结束。 `PaintSurface`处理程序将其图面划分为八个矩形，并调用 `Display` 以显示每个矩形：

```csharp
public class GradientBitmapPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        int width = info.Width;
        int height = info.Height;

        canvas.Clear();

        Display(canvas, 0, new SKRect(0, 0, width / 2, height / 4));
        Display(canvas, 1, new SKRect(width / 2, 0, width, height / 4));
        Display(canvas, 2, new SKRect(0, height / 4, width / 2, 2 * height / 4));
        Display(canvas, 3, new SKRect(width / 2, height / 4, width, 2 * height / 4));
        Display(canvas, 4, new SKRect(0, 2 * height / 4, width / 2, 3 * height / 4));
        Display(canvas, 5, new SKRect(width / 2, 2 * height / 4, width, 3 * height / 4));
        Display(canvas, 6, new SKRect(0, 3 * height / 4, width / 2, height));
        Display(canvas, 7, new SKRect(width / 2, 3 * height / 4, width, height));
    }

    void Display(SKCanvas canvas, int index, SKRect rect)
    {
        string text = String.Format("{0}: {1:F1} msec", descriptions[index],
                                    (double)elapsedTimes[index] / REPS);

        SKRect bounds = new SKRect();

        using (SKPaint textPaint = new SKPaint())
        {
            textPaint.TextSize = (float)(12 * canvasView.CanvasSize.Width / canvasView.Width);
            textPaint.TextAlign = SKTextAlign.Center;
            textPaint.MeasureText("Tly", ref bounds);

            canvas.DrawText(text, new SKPoint(rect.MidX, rect.Bottom - bounds.Bottom), textPaint);
            rect.Bottom -= bounds.Height;
            canvas.DrawBitmap(bitmaps[index], rect, BitmapStretch.Uniform);
        }
    }
}
```

为了允许编译器优化代码，此页在 **发布** 模式下运行。 这是在 MacBook Pro 上的 iPhone 8 模拟器上运行的页面，a 结点 5 Android 手机，Surface Pro 3 运行 Windows 10。 由于硬件差异，应避免在设备之间比较性能时间，而应查看每个设备的相对时间：

[![渐变位图](pixel-bits-images/GradientBitmap.png "渐变位图")](pixel-bits-images/GradientBitmap-Large.png#lightbox)

下面是以毫秒为单位的表，用于合并执行时间：

| API       | 数据类型 | iOS  | Android | UWP  |
| --------- | --------- | ----:| -------:| ----:|
| SetPixel  |           | 3.17 |   10.77 | 3.49 |
| 像素    |           | 0.32 |    1.23 | 0.07 |
| GetPixels | 字节      | 0.09 |    0.24 | 0.10 |
|           | uint      | 0.06 |    0.26 | 0.05 |
|           | SKColor   | 0.29 |    0.99 | 0.07 |
| SetPixels | 字节      | 1.33 |    6.78 | 0.11 |
|           | uint      | 0.14 |    0.69 | 0.06 |
|           | SKColor   | 0.35 |    1.93 | 0.10 |

如预期那样，调用 `SetPixel` 65536 时间是设置位图像素的最 effeicient 方法。 填充 `SKColor` 数组并设置 `Pixels` 属性要好得多，甚至比较了一些 `GetPixels` 和 `SetPixels` 技巧。 通常，使用 `uint` 像素值的速度要快于设置单独的 `byte` 组件，并将 `SKColor` 值转换为无符号整数会给进程增加一些开销。

比较各种渐变也是有意义的：每个平台的前几行是相同的，并显示预期的渐变。 这意味着 `SetPixel` 方法和 `Pixels` 属性从颜色中正确地创建像素，而不考虑基础像素格式。

下两行 iOS 和 Android 屏幕快照也是相同的，它确认为 `MakePixel` 这些平台的默认像素格式正确定义了小方法 `Rgba8888` 。

IOS 和 Android 屏幕截图的底部行向后，这表示通过强制转换值获得的无符号整数 `SKColor` 格式如下：

AARRGGBB

字节顺序如下：

BB GG RR AA

这是 `Bgra8888` 排序而不是 `Rgba8888` 排序。 `Brga8888`格式是通用 Windows 平台的默认设置，这就是此屏幕截图最后一行的渐变与第一行相同的原因。 但中间两行不正确，因为创建这些位图的代码假定 `Rgba8888` 排序。

如果要在每个平台上使用相同的代码来访问像素位，则可以 `SKBitmap` 使用或格式显式创建一个 `Rgba8888` `Bgra8888` 。 如果要将值强制转换 `SKColor` 为位图像素，请使用 `Bgra8888` 。

## <a name="random-access-of-pixels"></a>随机访问像素

`FillBitmapBytePtr` `FillBitmapUintPtr` **渐变位图**页中的和方法得益于 `for` 旨在按顺序填充位图的循环、从上到下行、从左到右的每行。 可以通过与指针递增的相同语句来设置像素。

有时需要随机而不是按顺序访问像素。 如果使用的是 `GetPixels` 方法，则需要根据行和列计算指针。 这在 **彩虹正弦** 页面中进行了演示，这会创建一个位图，其中显示一条正弦曲线循环的外形。

使用 HSL (色相、饱和度、亮度) 颜色模型，彩虹的颜色最简单。 `SKColor.FromHsl`方法 `SKColor` 使用介于0到360之间的色调值创建一个值 (例如圆圈的角度，而从红色、绿色和蓝色，再到红色) ，以及从0到100的饱和度和发光度值。 对于彩虹的颜色，饱和度应设置为最大值为100，将亮度设置为50的中点。

**彩虹正弦** 通过循环遍历位图的各个行，然后循环到360的色调值，来创建此图像。 根据每个色相值，计算也基于正弦值的位图列：

```csharp
public class RainbowSinePage : ContentPage
{
    SKBitmap bitmap;

    public RainbowSinePage()
    {
        Title = "Rainbow Sine";

        bitmap = new SKBitmap(360 * 3, 1024, SKColorType.Bgra8888, SKAlphaType.Unpremul);

        unsafe
        {
            // Pointer to first pixel of bitmap
            uint* basePtr = (uint*)bitmap.GetPixels().ToPointer();

            // Loop through the rows
            for (int row = 0; row < bitmap.Height; row++)
            {
                // Calculate the sine curve angle and the sine value
                double angle = 2 * Math.PI * row / bitmap.Height;
                double sine = Math.Sin(angle);

                // Loop through the hues
                for (int hue = 0; hue < 360; hue++)
                {
                    // Calculate the column
                    int col = (int)(360 + 360 * sine + hue);

                    // Calculate the address
                    uint* ptr = basePtr + bitmap.Width * row + col;

                    // Store the color value
                    *ptr = (uint)SKColor.FromHsl(hue, 100, 50);
                }
            }
        }

        // Create the SKCanvasView
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
        canvas.DrawBitmap(bitmap, info.Rect);
    }
}
```

请注意，构造函数基于以下格式创建位图 `SKColorType.Bgra8888` ：

```csharp
bitmap = new SKBitmap(360 * 3, 1024, SKColorType.Bgra8888, SKAlphaType.Unpremul);
```

这使程序可以在 `SKColor` `uint` 无需担心的情况下将值转换为像素。 尽管它在此特定程序中不起作用，但当你使用 `SKColor` 转换设置像素时，还应指定， `SKAlphaType.Unpremul` 因为它 `SKColor` 不会预乘其颜色成分的 alpha 值。

然后，构造函数使用 `GetPixels` 方法获取指向位图第一个像素的指针：

```csharp
uint* basePtr = (uint*)bitmap.GetPixels().ToPointer();
```

对于任何特定的行和列，必须将偏移值添加到中 `basePtr` 。 此偏移量是位图宽度和列的行倍：

```csharp
uint* ptr = basePtr + bitmap.Width * row + col;
```

`SKColor`该值使用以下指针存储在内存中：

```csharp
*ptr = (uint)SKColor.FromHsl(hue, 100, 50);
```

在 `PaintSurface` 的处理程序中 `SKCanvasView` ，位图会拉伸以填充显示区域：

[![彩虹正弦](pixel-bits-images/RainbowSine.png "彩虹正弦")](pixel-bits-images/RainbowSine-Large.png#lightbox)

## <a name="from-one-bitmap-to-another"></a>从一个位图到另一个位图

很多图像处理任务都涉及在将像素从一个位图传输到另一个位图时修改像素。 此方法在 " **颜色调整** " 页中进行演示。 页面加载某个位图资源，然后允许使用三个视图修改图像 `Slider` ：

[![颜色调整](pixel-bits-images/ColorAdjustment.png "颜色调整")](pixel-bits-images/ColorAdjustment-Large.png#lightbox)

对于每个像素颜色，第一个 `Slider` 将值从0到360添加到色相，但随后使用取模运算符将结果保留在0和360之间，从而有效地将颜色沿色谱 (，因为 UWP 屏幕截图演示了) 。 第二种 `Slider` 方式允许您选择要应用于饱和度的0.5 到2之间的乘法系数，第三个系数 `Slider` 对于亮度相同，如 Android 屏幕截图所示。

程序将保留两个位图，即名为的原始源位图 `srcBitmap` 和名为的调整后目标位图 `dstBitmap` 。 每次 `Slider` 移动时，程序将在中计算所有新像素 `dstBitmap` 。 当然，用户将通过 `Slider` 非常快速地移动视图进行试验，因此你希望获得可管理的最佳性能。 这涉及 `GetPixels` 源和目标位图的方法。

**颜色调整**页面不会控制源和目标位图的颜色格式。 相反，它包含和格式略有不同的逻辑 `SKColorType.Rgba8888` `SKColorType.Bgra8888` 。 源和目标可以是不同的格式，程序仍能正常工作。

下面是程序，这种方法除外，这是将 `TransferPixels` 像素格式传输到目标的关键方法。 构造函数设置 `dstBitmap` 等于 `srcBitmap` 。 `PaintSurface`处理程序显示 `dstBitmap` ：

```csharp
public partial class ColorAdjustmentPage : ContentPage
{
    SKBitmap srcBitmap =
        BitmapExtensions.LoadBitmapResource(typeof(FillRectanglePage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");
    SKBitmap dstBitmap;

    public ColorAdjustmentPage()
    {
        InitializeComponent();

        dstBitmap = new SKBitmap(srcBitmap.Width, srcBitmap.Height);
        OnSliderValueChanged(null, null);
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        float hueAdjust = (float)hueSlider.Value;
        hueLabel.Text = $"Hue Adjustment: {hueAdjust:F0}";

        float saturationAdjust = (float)Math.Pow(2, saturationSlider.Value);
        saturationLabel.Text = $"Saturation Adjustment: {saturationAdjust:F2}";

        float luminosityAdjust = (float)Math.Pow(2, luminositySlider.Value);
        luminosityLabel.Text = $"Luminosity Adjustment: {luminosityAdjust:F2}";

        TransferPixels(hueAdjust, saturationAdjust, luminosityAdjust);
        canvasView.InvalidateSurface();
    }
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(dstBitmap, info.Rect, BitmapStretch.Uniform);
    }
}
```

`ValueChanged`视图的处理程序 `Slider` 计算调整值和调用 `TransferPixels` 。

整个 `TransferPixels` 方法被标记为 `unsafe` 。 首先，它从两个位图的像素位获取字节指针，然后遍历所有行和列。 在源位图中，方法为每个像素获取四个字节。 它们可以是 `Rgba8888` 或 `Bgra8888` 顺序。 通过检查颜色类型，可以 `SKColor` 创建一个值。 然后提取、调整并使用 HSL 组件来重新创建 `SKColor` 该值。 根据目标位图是 `Rgba8888` 还是 `Bgra8888` ，字节存储在目标 bitmp 中：

```csharp
public partial class ColorAdjustmentPage : ContentPage
{
    ···
    unsafe void TransferPixels(float hueAdjust, float saturationAdjust, float luminosityAdjust)
    {
        byte* srcPtr = (byte*)srcBitmap.GetPixels().ToPointer();
        byte* dstPtr = (byte*)dstBitmap.GetPixels().ToPointer();

        int width = srcBitmap.Width;       // same for both bitmaps
        int height = srcBitmap.Height;

        SKColorType typeOrg = srcBitmap.ColorType;
        SKColorType typeAdj = dstBitmap.ColorType;

        for (int row = 0; row < height; row++)
        {
            for (int col = 0; col < width; col++)
            {
                // Get color from original bitmap
                byte byte1 = *srcPtr++;         // red or blue
                byte byte2 = *srcPtr++;         // green
                byte byte3 = *srcPtr++;         // blue or red
                byte byte4 = *srcPtr++;         // alpha

                SKColor color = new SKColor();

                if (typeOrg == SKColorType.Rgba8888)
                {
                    color = new SKColor(byte1, byte2, byte3, byte4);
                }
                else if (typeOrg == SKColorType.Bgra8888)
                {
                    color = new SKColor(byte3, byte2, byte1, byte4);
                }

                // Get HSL components
                color.ToHsl(out float hue, out float saturation, out float luminosity);

                // Adjust HSL components based on adjustments
                hue = (hue + hueAdjust) % 360;
                saturation = Math.Max(0, Math.Min(100, saturationAdjust * saturation));
                luminosity = Math.Max(0, Math.Min(100, luminosityAdjust * luminosity));

                // Recreate color from HSL components
                color = SKColor.FromHsl(hue, saturation, luminosity);

                // Store the bytes in the adjusted bitmap
                if (typeAdj == SKColorType.Rgba8888)
                {
                    *dstPtr++ = color.Red;
                    *dstPtr++ = color.Green;
                    *dstPtr++ = color.Blue;
                    *dstPtr++ = color.Alpha;
                }
                else if (typeAdj == SKColorType.Bgra8888)
                {
                    *dstPtr++ = color.Blue;
                    *dstPtr++ = color.Green;
                    *dstPtr++ = color.Red;
                    *dstPtr++ = color.Alpha;
                }
            }
        }
    }
    ···
}
```

通过为源位图和目标位图的颜色类型组合创建单独的方法，可以更进一步提高此方法的性能，并避免检查每个像素的类型。 另一种方法是 `for` `col` 根据颜色类型为变量提供多个循环。

## <a name="posterization"></a>Posterization

其他涉及访问像素位的常见任务是 _posterization_。 如果减少了位图像素中编码的颜色，使结果类似于使用有限调色板的手绘海报，则为。

" **色调分离** " 页对其中一个猴子映像执行此过程：

```csharp
public class PosterizePage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(FillRectanglePage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");
    public PosterizePage()
    {
        Title = "Posterize";

        unsafe
        {
            uint* ptr = (uint*)bitmap.GetPixels().ToPointer();
            int pixelCount = bitmap.Width * bitmap.Height;

            for (int i = 0; i < pixelCount; i++)
            {
                *ptr++ &= 0xE0E0E0FF;
            }
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
        canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform;
    }
}
```

构造函数中的代码访问每个像素，使用值0xE0E0E0FF 执行按位 "与" 运算，然后将结果存储在位图中。 值0xE0E0E0FF 保留每个颜色组件的高3位，并将较低的5位设置为0。 位图的颜色缩减<sup>为2或</sup>512，而不是 2<sup>24</sup>或16777216色：

[![色调](pixel-bits-images/Posterize.png "色调")](pixel-bits-images/Posterize-Large.png#lightbox)

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)