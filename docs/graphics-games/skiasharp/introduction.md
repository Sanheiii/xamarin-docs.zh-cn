---
title: 独立于平台的 SkiaSharp 示例
description: 本文档简要介绍了核心 SkiaSharp 的概念。 具体而言，它讨论了如何在 SKCanvas 上获取和绘制。
ms.prod: xamarin
ms.techonology: xamarin-skiasharp
ms.assetid: 19506F08-2603-465E-A806-6BD01638DE90
author: davidbritch
ms.author: dabritch
ms.date: 10/03/2018
ms.openlocfilehash: fd8926edce6b2310271e15418f2498162723ba49
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436457"
---
# <a name="skiasharp-platform-independent-examples"></a>独立于平台的 SkiaSharp 示例

_这为 SkiaSharp 背后的概念提供了一个与平台无关的简短介绍。_

SkiaSharp 提供了丰富且功能强大的2D 图形 API，可用于呈现为2D 缓冲区。  您可以使用这些内容来实现可合并到您的应用程序中的自定义用户界面元素和二维图形。 SkiaSharp 是一个到 [Skia](https://skia.org) 库的 .net 绑定，它继承了此库的功能和能力。

库当前以跨平台 [NuGet 包](https://www.nuget.org/packages/SkiaSharp)的形式提供，你可以通过添加 NuGet 引用将其添加到你的项目中。

为了进行绘制，你的代码将创建一个， `SkCanvas` 它描述了将发生绘图操作的图面。

## <a name="obtaining-an-skcanvas"></a>获取 SKCanvas

```csharp
using (var surface = SKSurface.Create (width: 640, height: 480, SKImageInfo.PlatformColorType, SKAlphaType.Premul)) {
    SKCanvas myCanvas = surface.Canvas;

    // Your drawing code goes here.
}
```

## <a name="drawing-on-skcanvas"></a>在 SKCanvas 上绘图

`SKCanvas`使用与您可能熟悉的其他绘图模型相似的绘图模型，它使用带有可选透明度通道的颜色，并且可以绘制线条、弧形、文本和图像。

下面只是使用 SkiaSharp 可以完成的几个不同的操作。  在下面的示例中，变量 `canvas` 的类型为 SKCanvas。

### <a name="drawing-xamagon"></a>绘制 Xamagon

此示例在 Xamagon 中绘制 Xamarin 的徽标：

```csharp
// clear the canvas / fill with white
canvas.Clear (SKColors.White);

// set up drawing tools
using (var paint = new SKPaint ()) {
  paint.IsAntialias = true;
  paint.Color = new SKColor (0x2c, 0x3e, 0x50);
  paint.StrokeCap = SKStrokeCap.Round;

  // create the Xamagon path
  using (var path = new SKPath ()) {
    path.MoveTo (71.4311121f, 56f);
    path.CubicTo (68.6763107f, 56.0058575f, 65.9796704f, 57.5737917f, 64.5928855f, 59.965729f);
    path.LineTo (43.0238921f, 97.5342563f);
    path.CubicTo (41.6587026f, 99.9325978f, 41.6587026f, 103.067402f, 43.0238921f, 105.465744f);
    path.LineTo (64.5928855f, 143.034271f);
    path.CubicTo (65.9798162f, 145.426228f, 68.6763107f, 146.994582f, 71.4311121f, 147f);
    path.LineTo (114.568946f, 147f);
    path.CubicTo (117.323748f, 146.994143f, 120.020241f, 145.426228f, 121.407172f, 143.034271f);
    path.LineTo (142.976161f, 105.465744f);
    path.CubicTo (144.34135f, 103.067402f, 144.341209f, 99.9325978f, 142.976161f, 97.5342563f);
    path.LineTo (121.407172f, 59.965729f);
    path.CubicTo (120.020241f, 57.5737917f, 117.323748f, 56.0054182f, 114.568946f, 56f);
    path.LineTo (71.4311121f, 56f);
    path.Close ();

    // draw the Xamagon path
    canvas.DrawPath (path, paint);
  }
}
```

### <a name="drawing-text"></a>绘制文本

```csharp
// clear the canvas / fill with white
canvas.DrawColor (SKColors.White);

// set up drawing tools
using (var paint = new SKPaint ()) {
  paint.TextSize = 64.0f;
  paint.IsAntialias = true;
  paint.Color = new SKColor (0x42, 0x81, 0xA4);
  paint.IsStroke = false;

  // draw the text
  canvas.DrawText ("Skia", 0.0f, 64.0f, paint);
}
```

### <a name="drawing-bitmaps"></a>绘图位图

```csharp
Stream fileStream = File.OpenRead ("MyImage.png");

// clear the canvas / fill with white
canvas.DrawColor (SKColors.White);

// decode the bitmap from the stream
using (var stream = new SKManagedStream(fileStream))
using (var bitmap = SKBitmap.Decode(stream))
using (var paint = new SKPaint()) {
  canvas.DrawBitmap(bitmap, SKRect.Create(Width, Height), paint);
}
```

### <a name="drawing-with-image-filters"></a>用图像筛选器绘制

```csharp
Stream fileStream = File.OpenRead ("MyImage.png"); // open a stream to an image file

// clear the canvas / fill with white
canvas.DrawColor (SKColors.White);

// decode the bitmap from the stream
using (var stream = new SKManagedStream(fileStream))
using (var bitmap = SKBitmap.Decode(stream))
using (var paint = new SKPaint()) {
  // create the image filter
  using (var filter = SKImageFilter.CreateBlur(5, 5)) {
    paint.ImageFilter = filter;

    // draw the bitmap through the filter
    canvas.DrawBitmap(bitmap, SKRect.Create(width, height), paint);
  }
}
```

## <a name="more-information"></a>详细信息

有关使用 SkiaSharp 的详细信息，请参阅 [API 文档](/dotnet/api/skiasharp)