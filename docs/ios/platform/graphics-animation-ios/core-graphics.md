---
title: Xamarin 中的核心图形
description: 本文介绍核心图形 iOS 框架。 它演示了如何使用核心图形绘制几何、图像和 Pdf。
ms.prod: xamarin
ms.assetid: 4A30F480-0723-4B8A-9049-7CEB6211304A
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 281595f45db48316feeebb88debf8c70873f87f3
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430856"
---
# <a name="core-graphics-in-xamarinios"></a>Xamarin 中的核心图形

_本文介绍核心图形 iOS 框架。它演示了如何使用核心图形绘制几何、图像和 Pdf。_

iOS 包括 [*核心图形*](https://developer.apple.com/library/prerelease/ios/documentation/CoreGraphics/Reference/CoreGraphics_Framework/index.html) 框架以提供低级别的绘图支持。 这些框架可实现 UIKit 中丰富的图形功能。

核心图形是一种低级别的2D 图形框架，可用于与设备无关的图形。 UIKit 中的所有2D 绘图均使用内部核心图形。

在许多情况下，核心图形支持绘图，其中包括：

- [通过 `UIView` 绘制到屏幕](#Drawing_in_a_UIView_Subclass).
- [在内存或屏幕上绘制图像](#Drawing_Images_and_Text)。
- 创建和绘制到 PDF。
- 读取和绘制现有的 PDF。

## <a name="geometric-space"></a>几何空间

不管采用哪种方案，使用核心图形完成的所有绘图都是在几何空间中完成的，这意味着它在抽象点而不是像素中运行。 您可以在几何图形和绘图状态（如颜色、线条样式等）中描述要绘制的内容，而核心图形会处理将所有内容转换为像素。 此类状态将添加到图形上下文，你可以将其视为油漆画布。

此方法有几个好处：

- 绘图代码变为动态代码，并可在运行时修改图形。
- 减少应用程序捆绑中的静态映像需求会降低应用程序的大小。
- 对于跨设备解决更改，图形变得更具弹性。

<a name="Drawing_in_a_UIView_Subclass"></a>

## <a name="drawing-in-a-uiview-subclass"></a>在 UIView 子类中绘制

每个 `UIView` 都有一个 `Draw` 方法，该方法在需要绘制时由系统调用。 若要将绘制代码添加到视图，子类 `UIView` 和替代 `Draw` ：

```csharp
public class TriangleView : UIView
{
    public override void Draw (CGRect rect)
    {
        base.Draw (rect);
    }
}
```

不应直接调用 Draw。 它由系统在运行循环处理期间调用。 在将视图添加到视图层次结构之后，第一次通过运行循环时，将 `Draw` 调用其方法。 `Draw`当视图被标记为需要通过在视图上调用或进行绘制时，对的后续调用 `SetNeedsDisplay` `SetNeedsDisplayInRect` 。

### <a name="pattern-for-graphics-code"></a>图形代码模式

实现中的代码 `Draw` 应描述要绘制的内容。 绘制代码遵循一种模式，在该模式下，它设置某些绘制状态，并调用方法来请求绘制它。 此模式可以通用化，如下所示：

1. 获取图形上下文。

2. 设置绘制属性。

3. 从绘图基元创建一些几何。

4. 调用绘制或笔划方法。

### <a name="basic-drawing-example"></a>基本绘图示例

以下面的代码片段为例：

```csharp
//get graphics context
using (CGContext g = UIGraphics.GetCurrentContext ()) {

    //set up drawing attributes
    g.SetLineWidth (10);
    UIColor.Blue.SetFill ();
    UIColor.Red.SetStroke ();

    //create geometry
    var path = new CGPath ();

    path.AddLines (new CGPoint[]{
    new CGPoint (100, 200),
    new CGPoint (160, 100),
    new CGPoint (220, 200)});

    path.CloseSubpath ();

    //add geometry to graphics context and draw it
    g.AddPath (path);
    g.DrawPath (CGPathDrawingMode.FillStroke);
}
```

让我们中断此代码：

```csharp
using (CGContext g = UIGraphics.GetCurrentContext ()) {
...
}
```

对于此行，它首先获取要用于绘制的当前图形上下文。 您可以将图形上下文视为绘图所处的画布，其中包含有关绘图的所有状态，如笔划和填充颜色以及要绘制的几何图形。

```csharp
g.SetLineWidth (10);
UIColor.Blue.SetFill ();
UIColor.Red.SetStroke ();
```

获取图形上下文后，代码会设置一些属性，以便在绘制时使用，如上所示。 在这种情况下，将设置线条宽度、描边和填充颜色。 然后，任何后续绘制都将使用这些属性，因为这些属性在图形上下文的状态中进行维护。

若要创建几何图形，代码使用 `CGPath` ，这允许从线条和曲线描述图形路径。 在这种情况下，路径会添加用于连接点数组的行，以构成一个三角形。 如下图所示，核心图形使用坐标系统进行视图绘制，其中原点位于左上角，正 x 向右，y 向下方向：

```csharp
var path = new CGPath ();

path.AddLines (new CGPoint[]{
new CGPoint (100, 200),
new CGPoint (160, 100),
new CGPoint (220, 200)});

path.CloseSubpath ();
```

创建路径后，会将其添加到图形上下文，以便调用 `AddPath` 和分别调用和 `DrawPath` 。

生成的视图如下所示：

 ![示例输出三角形](core-graphics-images/00-bluetriangle.png)

## <a name="creating-gradient-fills"></a>创建渐变填充

还提供了更丰富的绘图形式。 例如，核心图形允许创建渐变填充和应用剪辑路径。 若要在上一个示例中的路径内绘制渐变填充，首先需要将路径设置为剪切路径：

```csharp
// add the path back to the graphics context so that it is the current path
g.AddPath (path);
// set the current path to be the clipping path
g.Clip ();
```

将当前路径设置为剪切路径会限制路径的几何图形中的所有后续绘图，如以下代码，用于绘制线性渐变：

```csharp
// the color space determines how Core Graphics interprets color information
    using (CGColorSpace rgb = CGColorSpace.CreateDeviceRGB()) {
        CGGradient gradient = new CGGradient (rgb, new CGColor[] {
        UIColor.Blue.CGColor,
        UIColor.Yellow.CGColor
    });

// draw a linear gradient
    g.DrawLinearGradient (
        gradient,
        new CGPoint (path.BoundingBox.Left, path.BoundingBox.Top),
        new CGPoint (path.BoundingBox.Right, path.BoundingBox.Bottom),
        CGGradientDrawingOptions.DrawsBeforeStartLocation);
    }
```

这些更改生成渐变填充，如下所示：

 ![带有渐变填充的示例](core-graphics-images/01-gradient-fill.png)

## <a name="modifying-line-patterns"></a>修改线条模式

还可以通过核心图形来修改行的绘制属性。 这包括更改线条宽度和笔触颜色以及线条模式本身，如以下代码所示：

```csharp
//use a dashed line
g.SetLineDash (0, new nfloat[] { 10, 4 * (nfloat)Math.PI });
```

在任何绘图操作之前添加此代码将导致虚线笔划10个单位长，并在短划线之间添加4个单元间距，如下所示：

 ![在任何绘图操作之前添加此代码会导致虚线笔划](core-graphics-images/02-dashed-stroke.png)

请注意，在 Xamarin 中使用 Unified API 时，数组类型需要是 `nfloat` ，并且还需要显式转换为 Math。

<a name="Drawing_Images_and_Text"></a>

## <a name="drawing-images-and-text"></a>绘制图像和文本

除了在视图的图形上下文中绘制路径外，核心图形还支持绘制图像和文本。 若要绘制图像，只需创建一个 `CGImage` 并将其传递给 `DrawImage` 调用：

```csharp
public override void Draw (CGRect rect)
{
    base.Draw (rect);

    using(CGContext g = UIGraphics.GetCurrentContext ()){
        g.DrawImage (rect, UIImage.FromFile ("MyImage.png").CGImage);
    }
}
```

但是，这会生成一个倒置的图像，如下所示：

 ![倒置绘制的图像](core-graphics-images/03-upside-down-monkey.png)

出现这种情况的原因是，图像绘制的核心图形原点在左下角，而视图的原点位于左上角。 因此，若要正确显示图像，需要修改源，这可以通过修改 *当前变换矩阵* * (CTM) *完成。 CTM 定义点的位置，也称为 *用户空间*。 在 y 方向上反方向反转 CTM，并在负 y 方向将其沿边界的高度移位，可以翻转图像。

图形上下文包含用于转换 CTM 的 helper 方法。 在这种情况下， `ScaleCTM` "翻转" 绘图并将 `TranslateCTM` 其移到左上方，如下所示：

```csharp
public override void Draw (CGRect rect)
{
    base.Draw (rect);

    using (CGContext g = UIGraphics.GetCurrentContext ()) {

        // scale and translate the CTM so the image appears upright
        g.ScaleCTM (1, -1);
        g.TranslateCTM (0, -Bounds.Height);
        g.DrawImage (rect, UIImage.FromFile ("MyImage.png").CGImage);
}
```

然后，生成的图像显示为直立的：

 ![垂直显示的示例图像](core-graphics-images/04-upright-monkey.png)

> [!IMPORTANT]
> 对图形上下文所做的更改将应用于所有后续的绘图操作。 因此，在转换 CTM 时，它会影响任何其他绘图。 例如，如果您在 CTM 转换后绘制了三角形，则它会倒置显示。

### <a name="adding-text-to-the-image"></a>向图像添加文本

对于路径和图像，使用核心图形绘制文本涉及到设置某些图形状态和调用要绘制的方法的基本模式。 如果是文本，则显示文本的方法为 `ShowText` 。 添加到图像绘制示例时，以下代码使用核心图形绘制一些文本：

```csharp
public override void Draw (RectangleF rect)
{
    base.Draw (rect);

    // image drawing code omitted for brevity ...

    // translate the CTM by the font size so it displays on screen
    float fontSize = 35f;
    g.TranslateCTM (0, fontSize);

    // set general-purpose graphics state
    g.SetLineWidth (1.0f);
    g.SetStrokeColor (UIColor.Yellow.CGColor);
    g.SetFillColor (UIColor.Red.CGColor);
    g.SetShadow (new CGSize (5, 5), 0, UIColor.Blue.CGColor);

    // set text specific graphics state
    g.SetTextDrawingMode (CGTextDrawingMode.FillStroke);
    g.SelectFont ("Helvetica", fontSize, CGTextEncoding.MacRoman);

    // show the text
    g.ShowText ("Hello Core Graphics");
}
```

正如您所看到的，设置文本绘图的图形状态与绘制几何类似。 但对于文本绘制，还会应用文本绘制模式和字体。 在这种情况下，还会应用阴影，尽管应用阴影对于路径绘制是相同的。

生成的文本将与图像一起显示，如下所示：

 ![生成的文本与图像一起显示](core-graphics-images/05-text-on-image.png)

## <a name="memory-backed-images"></a>支持内存的映像

除了绘制到视图的图形上下文外，核心图形还支持绘图内存已备份的图像，也称为在屏幕上绘制。 这样做需要：

- 创建由内存中位图支持的图形上下文
- 设置绘制状态和发出绘图命令
- 从上下文获取映像
- 删除上下文

不同于 `Draw` 方法，其中的上下文由视图提供，在这种情况下，您可以通过以下两种方式之一创建上下文：

1. 通过调用 `UIGraphics.BeginImageContext` (或 `BeginImageContextWithOptions`) 

2. 通过创建新的 `CGBitmapContextInstance`

 `CGBitmapContextInstance` 当您直接处理图像位（如使用自定义图像操作算法的情况）时，将非常有用。 在所有其他情况下，应使用 `BeginImageContext` 或 `BeginImageContextWithOptions` 。

获得映像上下文后，添加绘图代码就像在子类中一样 `UIView` 。 例如，之前用于绘制三角形的代码示例可用于在内存中（而不是在中）绘制到图像 `UIView` ，如下所示：

```csharp
UIImage DrawTriangle ()
{
    UIImage triangleImage;

    //push a memory backed bitmap context on the context stack
    UIGraphics.BeginImageContext (new CGSize (200.0f, 200.0f));

    //get graphics context
    using(CGContext g = UIGraphics.GetCurrentContext ()){

        //set up drawing attributes
        g.SetLineWidth(4);
        UIColor.Purple.SetFill ();
        UIColor.Black.SetStroke ();

        //create geometry
        path = new CGPath ();

        path.AddLines(new CGPoint[]{
            new CGPoint(100,200),
            new CGPoint(160,100),
            new CGPoint(220,200)});

        path.CloseSubpath();

        //add geometry to graphics context and draw it
        g.AddPath(path);
        g.DrawPath(CGPathDrawingMode.FillStroke);

        //get a UIImage from the context
        triangleImage = UIGraphics.GetImageFromCurrentImageContext ();
    }

    return triangleImage;
}
```

绘制到支持内存的位图的常见用途是从任何捕获映像 `UIView` 。 例如，下面的代码将视图的层呈现为位图上下文，并 `UIImage` 从其创建：

```csharp
UIGraphics.BeginImageContext (cellView.Frame.Size);

//render the view's layer in the current context
anyView.Layer.RenderInContext (UIGraphics.GetCurrentContext ());

//get a UIImage from the context
UIImage anyViewImage = UIGraphics.GetImageFromCurrentImageContext ();
UIGraphics.EndImageContext ();
```

## <a name="drawing-pdfs"></a>绘图 Pdf

除了图像，核心图形还支持 PDF 绘图。 与图像一样，你可以在内存中呈现 PDF，还可以读取 PDF 以便在中呈现 `UIView` 。

### <a name="pdf-in-a-uiview"></a>UIView 中的 PDF

核心图形还支持从文件读取 PDF，并使用类在视图中呈现它 `CGPDFDocument` 。 `CGPDFDocument`类表示代码中的 PDF，可用于读取和绘制页面。

例如，子类中的以下代码将 `UIView` 文件中的 PDF 读入 `CGPDFDocument` ：

```csharp
public class PDFView : UIView
{
    CGPDFDocument pdfDoc;

    public PDFView ()
    {
        //create a CGPDFDocument from file.pdf included in the main bundle
        pdfDoc = CGPDFDocument.FromFile ("file.pdf");
    }

     public override void Draw (Rectangle rect)
    {
        ...
    }
}
```

然后，该 `Draw` 方法可以使用将 `CGPDFDocument` 页面读入 `CGPDFPage` 并通过调用来呈现页面 `DrawPDFPage` ，如下所示：

```csharp
public override void Draw (CGRect rect)
{
    base.Draw (rect);

    //flip the CTM so the PDF will be drawn upright
    using (CGContext g = UIGraphics.GetCurrentContext ()) {
        g.TranslateCTM (0, Bounds.Height);
        g.ScaleCTM (1, -1);

        // render the first page of the PDF
        using (CGPDFPage pdfPage = pdfDoc.GetPage (1)) {

        //get the affine transform that defines where the PDF is drawn
        CGAffineTransform t = pdfPage.GetDrawingTransform (CGPDFBox.Crop, rect, 0, true);

        //concatenate the pdf transform with the CTM for display in the view
        g.ConcatCTM (t);

        //draw the pdf page
        g.DrawPDFPage (pdfPage);
        }
    }
}
```

### <a name="memory-backed-pdf"></a>支持内存的 PDF

对于内存中的 PDF，需要通过调用来创建 PDF 上下文 `BeginPDFContext` 。 绘图到 PDF 精确到页面。 每个页面都是通过调用 `BeginPDFPage` 来启动并通过调用完成 `EndPDFContent` ，并在之间使用图形代码。 同样，与图像绘制一样，支持内存的 PDF 绘图还会在左下角使用原点，这可以通过修改 CTM 来处理，就像使用图像一样。

下面的代码演示如何将文本绘制到 PDF：

```csharp
//data buffer to hold the PDF
NSMutableData data = new NSMutableData ();

//create a PDF with empty rectangle, which will configure it for 8.5x11 inches
UIGraphics.BeginPDFContext (data, CGRect.Empty, null);

//start a PDF page
UIGraphics.BeginPDFPage ();

using (CGContext g = UIGraphics.GetCurrentContext ()) {
    g.ScaleCTM (1, -1);
    g.TranslateCTM (0, -25);
    g.SelectFont ("Helvetica", 25, CGTextEncoding.MacRoman);
    g.ShowText ("Hello Core Graphics");
    }

//complete a PDF page
UIGraphics.EndPDFContent ();
```

生成的文本将被绘制到 PDF，后者随后将包含在 `NSData` 可以保存、上传、通过电子邮件发送等的中。

## <a name="summary"></a>总结

本文介绍了通过 *核心图形* 框架提供的图形功能。 我们了解到如何使用核心图形在的上下文中以及在支持内存的图形上下文中绘制几何、图像和 Pdf `UIView,` 。

## <a name="related-links"></a>相关链接

- [核心图形示例](/samples/xamarin/ios-samples/graphicsandanimation)
- [图形和动画演练](~/ios/platform/graphics-animation-ios/graphics-animation-walkthrough.md)
- [核心动画](~/ios/platform/graphics-animation-ios/core-animation.md)
- [核心动画食谱](https://github.com/xamarin/recipes/tree/master/Recipes/ios/animation/coreanimation)