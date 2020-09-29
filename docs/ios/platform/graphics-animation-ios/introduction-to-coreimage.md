---
title: Xamarin 中的核心映像
description: 核心映像是 iOS 5 引入的新框架，用于提供图像处理和实时视频增强功能。 本文介绍了 Xamarin iOS 示例中的这些功能。
ms.prod: xamarin
ms.assetid: 91E0780B-FF8A-E70D-9CD4-419119612B2D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 7cb94956266a3094cc62ccc66c3d44e97884f04d
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435289"
---
# <a name="core-image-in-xamarinios"></a>Xamarin 中的核心映像

_核心映像是 iOS 5 引入的新框架，用于提供图像处理和实时视频增强功能。本文介绍了 Xamarin iOS 示例中的这些功能。_

核心映像是 iOS 5 中引入的一种新框架，提供了许多内置筛选器和影响，可应用于图像和视频，包括面部检测。

本文档包含的简单示例：

- 人脸检测。
- 将筛选器应用于映像
- 列出可用的筛选器。

这些示例可帮助你开始将核心映像功能合并到你的 Xamarin iOS 应用程序中。

## <a name="requirements"></a>要求

必须使用最新版本的 Xcode。

## <a name="face-detection"></a>人脸检测

核心图像面部检测功能只是它所说的内容，它会尝试确定照片中的人脸，并返回它识别的任何面部的坐标。 此信息可用于计算图像中人员的数量，在图像上绘制标记 (例如。 对于照片中的 "标记" 用户) ，或者您可以考虑的任何其他内容。

此 CoreImage\SampleCode.cs 中的代码演示如何在嵌入图像上创建和使用人脸检测：

```csharp
var image = new UIImage("photoFace.JPG");
var context = CIContext.FromOptions(null);
var detector = CIDetector.CreateFaceDetector (context, true);
var ciImage = CIImage.FromCGImage(image.CGImage);
CIFeature[] features = detector.FeaturesInImage(ciImage);
```

`CIFaceFeature`如果检测到任何面部) ，功能阵列将用对象填充 (。 `CIFaceFeature`每个面都有一个。 `CIFaceFeature` 具有以下属性：

- HasMouthPosition-是否为此人脸检测到嘴。
- HasLeftEyePosition-是否为此人脸检测到了左侧眼。
- HasRightEyePosition-是否为此人脸检测到了正确的眼睛。 
- MouthPosition-此人脸的嘴坐标。
- LeftEyePosition-此人脸的左侧眼睛坐标。
- RightEyePosition-此人脸的适当眼睛坐标。

所有这些属性的坐标在左下角均为原点–不同于 UIKit，它使用左上角作为原点。 当使用上的坐标时， `CIFaceFeature` 请确保 "翻转" 它们。 CoreImage\CoreImageViewController.cs 中这个非常基本的自定义图像视图演示了如何在图像上绘制 "面部指示器" 三角形 (注意 `FlipForBottomOrigin` 方法) ：

```csharp
public class FaceDetectImageView : UIView
{
    public Xamarin.iOS.CoreImage.CIFeature[] Features;
    public UIImage Image;
    public FaceDetectImageView (RectangleF rect) : base(rect) {}
    CGPath path;
    public override void Draw (RectangleF rect) {
        base.Draw (rect);
        if (Image != null)
            Image.Draw(rect);

        using (var context = UIGraphics.GetCurrentContext()) {
            context.SetLineWidth(4);
            UIColor.Red.SetStroke ();
            UIColor.Clear.SetFill ();
            if (Features != null) {
                foreach (var feature in Features) { // for each face
                    var facefeature = (CIFaceFeature)feature;
                    path = new CGPath ();
                    path.AddLines(new PointF[]{ // assumes all 3 features found
                        FlipForBottomOrigin(facefeature.LeftEyePosition, 200),
                        FlipForBottomOrigin(facefeature.RightEyePosition, 200),
                        FlipForBottomOrigin(facefeature.MouthPosition, 200)
                    });
                    path.CloseSubpath();
                    context.AddPath(path);
                    context.DrawPath(CGPathDrawingMode.FillStroke);
                }
            }
        }
    }
    /// <summary>
    /// Face recognition coordinates have their origin in the bottom-left
    /// but we are drawing with the origin in the top-left, so "flip" the point
    /// </summary>
    PointF FlipForBottomOrigin (PointF point, int height)
    {
        return new PointF(point.X, height - point.Y);
    }
}
```

然后，在 SampleCode.cs 文件中，映像和功能将在重绘映像之前进行分配：

```csharp
faceView.Image = image;
faceView.Features = features;
faceView.SetNeedsDisplay();
```

屏幕截图显示了示例输出：检测到的面部特征的位置显示在 UITextView 中，并使用 CoreGraphics 将其绘制到源图像上。

由于面部识别的工作方式，有时会检测除人脸 (等玩具猴！ ) 以外的东西。

## <a name="filters"></a>筛选器

有超过50个不同的内置筛选器，并且该框架是可扩展的，以便可以实现新的筛选器。

## <a name="using-filters"></a>使用筛选器

将筛选器应用于映像包含四个不同的步骤：加载图像、创建筛选器、应用筛选器并保存 (或显示结果) 。

首先，将图像加载到 `CIImage` 对象。

```csharp
var uiimage = UIImage.FromFile ("photo.JPG");
var ciimage = new CIImage (uiimage);
```

其次，创建筛选器类并设置其属性。

```csharp
var sepia = new CISepiaTone();
sepia.Image = ciimage;
sepia.Intensity = 0.8f;
```

第三，访问 `OutputImage` 属性并调用 `CreateCGImage` 方法以呈现最终结果。

```csharp
CIImage output = sepia.OutputImage;
var context = CIContext.FromOptions(null);
var cgimage = context.CreateCGImage (output, output.Extent);
```

最后，将图像分配给视图以查看结果。 在实际应用程序中，生成的映像可能会保存到文件系统、相册、推文或电子邮件。

```csharp
var ui = UIImage.FromImage (cgimage);
imgview.Image = ui;
```

这些屏幕截图显示 `CISepia` `CIHueAdjust` 在 CoreImage.zip 示例代码中演示的和筛选器的结果。

有关筛选器的示例，请参阅 [调整图像食谱的协定和亮度](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/coreimage/adjust_contrast_and_brightness_of_an_image) `CIColorControls` 。

```csharp
var uiimage = UIImage.FromFile("photo.JPG");
var ciimage = new CIImage(uiimage);
var hueAdjust = new CIHueAdjust();   // first filter
hueAdjust.Image = ciimage;
hueAdjust.Angle = 2.094f;
var sepia = new CISepiaTone();       // second filter
sepia.Image = hueAdjust.OutputImage; // output from last filter, input to this one
sepia.Intensity = 0.3f;
CIFilter color = new CIColorControls() { // third filter
    Saturation = 2,
    Brightness = 1,
    Contrast = 3,
    Image = sepia.OutputImage    // output from last filter, input to this one
};
var output = color.OutputImage;
var context = CIContext.FromOptions(null);
// ONLY when CreateCGImage is called do all the effects get rendered
var cgimage = context.CreateCGImage (output, output.Extent);
var ui = UIImage.FromImage (cgimage);
imgview.Image = ui;
```

```csharp
var context = CIContext.FromOptions (null);
```

```csharp
var context = CIContext.FromOptions(new CIContextOptions() {
    UseSoftwareRenderer = true  // CPU
});
var cgimage = context.CreateCGImage (output, output.Extent);
var ui = UIImage.FromImage (cgimage);
imgview.Image = ui;
```

### <a name="listing-filters-and-their-properties"></a>列出筛选器及其属性

此 CoreImage\SampleCode.cs 中的代码输出内置筛选器及其参数的完整列表。

```csharp
var filters = CIFilter.FilterNamesInCategories(new string[0]);
foreach (var filter in filters){
   display.Text += filter +"\n";
   var f = CIFilter.FromName (filter);
   foreach (var key in f.InputKeys){
     var attributes = (NSDictionary)f.Attributes[new NSString(key)];
     var attributeClass = attributes[new NSString("CIAttributeClass")];
     display.Text += "   " + key;
     display.Text += "   " + attributeClass + "\n";
   }
}
```

[CIFilter 类引用](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Reference/QuartzCoreFramework/Classes/CIFilter_Class/Reference/Reference.html)描述了50内置筛选器及其属性。 使用上面的代码，你可以查询筛选器类，包括参数的默认值、最大值和最小值允许值 (可用于在应用筛选器) 之前验证输入。

在模拟器上，列表类别的输出如下所示–您可以滚动浏览列表以查看所有筛选器及其参数。

 [![在模拟器上，列表类别的输出如下所示](introduction-to-coreimage-images/coreimage05.png)](introduction-to-coreimage-images/coreimage05.png#lightbox)

列出的每个筛选器已作为 Xamarin 中的类公开，因此你也可以在程序集浏览器中浏览 CoreImage API，或在 Visual Studio for Mac 或 Visual Studio 中使用自动完成。 

## <a name="summary"></a>总结

本文介绍了如何使用一些新的 iOS 5 核图像框架功能，如人脸检测和对图像应用筛选器。 框架中提供了许多不同的映像筛选器供你使用。

## <a name="related-links"></a>相关链接

- [核心映像 (示例) ](/samples/xamarin/ios-samples/coreimage)
- [调整图像食谱的协定和亮度](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/coreimage/adjust_contrast_and_brightness_of_an_image)
- [使用核心映像筛选器](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Conceptual/CoreImaging/ci_tasks/ci_tasks.html)
- [CIFilter 类引用](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Reference/QuartzCoreFramework/Classes/CIFilter_Class/Reference/Reference.htm)