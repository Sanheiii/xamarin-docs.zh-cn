---
title: Xamarin 中的远景框架
description: 本文档介绍如何在 Xamarin 中使用 iOS 11 远景框架。 具体而言，它讨论了矩形检测和面部检测。
ms.prod: xamarin
ms.assetid: 7273ED68-7B7D-4252-B3A0-02DB2E357A8C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/31/2017
ms.openlocfilehash: 9a31cd31f7cbfb4748991d45204ca867b5d62c8c
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436621"
---
# <a name="vision-framework-in-xamarinios"></a>Xamarin 中的远景框架

远景框架向 iOS 11 添加了许多新的图像处理功能，其中包括：

- [矩形检测](#rectangles)
- [人脸检测](#faces)
- [CoreML](~/ios/platform/introduction-to-ios11/coreml.md)) 中讨论机器学习图像分析 (
- 条形码检测
- 图像对齐分析
- 文本检测
- 范围检测
- 对象检测 & 跟踪

![检测到包含三个矩形的照片](vision-images/found-rectangles-tiny.png) ![检测到两个面部的照片](vision-images/xamarin-home-faces-tiny.png)

下面更详细地讨论了矩形检测和人脸检测。

<a name="rectangles"></a>

## <a name="rectangle-detection"></a>矩形检测

[VisionRects 示例](/samples/xamarin/ios-samples/ios11-visionrectangles)演示如何处理图像并在其上绘制检测到的矩形。

### <a name="1-initialize-the-vision-request"></a>1. 初始化远景请求

在中 `ViewDidLoad` ，创建一个 `VNDetectRectanglesRequest` 引用 `HandleRectangles` 将在每个请求结束时调用的方法的：

`MaximumObservations`还应设置属性，否则将默认为1，并且仅返回一个结果。

```csharp
RectangleRequest = new VNDetectRectanglesRequest(HandleRectangles);
RectangleRequest.MaximumObservations = 10;
```

### <a name="2-start-the-vision-processing"></a>2. 启动视觉处理

下面的代码开始处理请求。 在 **VisionRects** 示例中，此代码在用户选择了图像后运行：

```csharp
// Run the rectangle detector
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {RectangleRequest}, out NSError error);
});
```

此处理程序将传递 `ciImage` 到 `VNDetectRectanglesRequest` 在步骤1中创建的远景框架。

### <a name="3-handle-the-results-of-vision-processing"></a>3. 处理视觉处理的结果

完成矩形检测后，框架将执行 `HandleRectangles` 方法，摘要如下所示：

```csharp
private void HandleRectangles(VNRequest request, NSError error){
  var observations = request.GetResults<VNRectangleObservation>();
  // ... omitted error handling ...
  bool atLeastOneValid = false;
  foreach (var o in observations){
    if (InputImage.Extent.Contains(boundingBox)) {
      atLeastOneValid |= true;
    }
  }
  if (!atLeastOneValid) return;
  // Show the pre-processed image
  DispatchQueue.MainQueue.DispatchAsync(() =>
  {
    ClassificationLabel.Text = summary;
    ImageView.Image = OverlayRectangles(RawImage, imageSize, observations);
  });
}
```

### <a name="4-display-the-results"></a>4. 显示结果

`OverlayRectangles` **VisionRectangles**示例中的方法有三个函数：

- 渲染源图像，
- 绘制一个矩形以指示每个的检测位置，并
- 使用 CoreGraphics 为每个矩形添加文本标签。

查看 CoreGraphics 的确切方法 [源](/samples/xamarin/ios-samples/ios11-visionrectangles) 。

![检测到包含三个矩形的照片](vision-images/found-rectangles-phone-sml.png)

### <a name="5-further-processing"></a>5. 进一步处理

矩形检测通常只是操作链中的第一步（如 [此 CoreMLVision 示例](~/ios/platform/introduction-to-ios11/coreml.md#coremlvision)），其中，将矩形传递到 CoreML 模型来分析手写数字。

<a name="faces"></a>

## <a name="face-detection"></a>人脸检测

[VisionFaces 示例](/samples/xamarin/ios-samples/ios11-visionfaces)使用不同的远景请求类，与**VisionRectangles**示例的工作方式类似。

### <a name="1-initialize-the-vision-request"></a>1. 初始化远景请求

在中 `ViewDidLoad` ，创建一个 `VNDetectFaceRectanglesRequest` 引用 `HandleRectangles` 将在每个请求结束时调用的方法的。

```csharp
FaceRectangleRequest = new VNDetectFaceRectanglesRequest(HandleRectangles);
```

### <a name="2-start-the-vision-processing"></a>2. 启动视觉处理

下面的代码开始处理请求。 在 **VisionFaces** 示例中，此代码在用户选择了图像后运行：

```csharp
// Run the face detector
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {FaceRectangleRequest}, out NSError error);
});
```

此处理程序将传递 `ciImage` 到 `VNDetectFaceRectanglesRequest` 在步骤1中创建的远景框架。

### <a name="3-handle-the-results-of-vision-processing"></a>3. 处理视觉处理的结果

面部检测完成后，处理程序将执行方法，该 `HandleRectangles` 方法执行错误处理并显示检测到的人脸的边界，并调用 `OverlayRectangles` 来绘制原始图片上的边框：

```csharp
private void HandleRectangles(VNRequest request, NSError error){
  var observations = request.GetResults<VNFaceObservation>();
  // ... omitted error handling...
  var summary = "";
  var imageSize = InputImage.Extent.Size;
  bool atLeastOneValid = false;
  Console.WriteLine("Faces:");
  summary += "Faces:";
  foreach (var o in observations) {
    // Verify detected rectangle is valid. omitted
    var boundingBox = o.BoundingBox.Scaled(imageSize);
    if (InputImage.Extent.Contains(boundingBox)) {
      atLeastOneValid |= true;
    }
  }
  // Show the pre-processed image (on UI thread)
  DispatchQueue.MainQueue.DispatchAsync(() =>
  {
    ClassificationLabel.Text = summary;
    ImageView.Image = OverlayRectangles(RawImage, imageSize, observations);
  });
}
```

### <a name="4-display-the-results"></a>4. 显示结果

`OverlayRectangles` **VisionFaces**示例中的方法有三个函数：

- 渲染源图像，
- 为检测到的每个面部绘制一个矩形，并
- 使用 CoreGraphics 为每个面部添加一个文本标签。

查看 CoreGraphics 的确切方法 [源](/samples/xamarin/ios-samples/ios11-visionfaces) 。

![检测到两个面部的照片](vision-images/found-faces-phone-sml.png)

### <a name="5-further-processing"></a>5. 进一步处理

远景框架包含其他功能来检测面部功能，如眼睛和嘴。 使用 `VNDetectFaceLandmarksRequest` 类型，它将返回 `VNFaceObservation` 如上第3步中所示的结果，但包含其他 `VNFaceLandmark` 数据。

## <a name="related-links"></a>相关链接

- [ (示例的视觉矩形) ](/samples/xamarin/ios-samples/ios11-visionrectangles)
- [视觉 (示例) ](/samples/xamarin/ios-samples/ios11-visionfaces)
- [核心图像的进步-筛选器、金属、视觉效果，以及更多 (WWDC)  (视频) ](https://developer.apple.com/videos/play/wwdc2017/510/)