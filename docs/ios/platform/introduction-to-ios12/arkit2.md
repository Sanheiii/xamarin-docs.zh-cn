---
title: Xamarin 中的 ARKit 2
description: 本文档介绍 iOS 12 中 ARKit 的更新。 其中重点介绍了如何使用引用对象和图像进行检测，包括用于环境纹理的代码，并讨论了 ARKit 编程中的常见问题。
ms.prod: xamarin
ms.assetid: af758092-1523-4ab7-aa53-c37a81fb156a
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/22/2018
ms.openlocfilehash: c460eb28197ded7e56c6e11cde98abe201bd06b5
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434287"
---
# <a name="arkit-2-in-xamarinios"></a>Xamarin 中的 ARKit 2

自去年的 iOS 11 年起，ARKit 已大幅成熟。 首先，您可以检测垂直和水平平面，这大大提高了室内增加的现实体验的实用性。 此外，还有一些新功能：

- 识别引用图像和对象作为现实世界和数字图像之间的接合
- 模拟现实世界照明的新照明模式
- 共享和保留 AR 环境的功能
- 首选存储 AR 内容的新文件格式

## <a name="recognizing-reference-objects"></a>识别引用对象

ARKit 2 中的一个展示功能是识别引用图像和对象的能力。 可以从 ([稍后介绍](#more-tracking-configurations)) 的常规图像文件中加载引用映像，但必须使用以开发人员为中心的引用对象进行扫描 [`ARObjectScanningConfiguration`](xref:ARKit.ARObjectScanningConfiguration) 。

### <a name="sample-app-scanning-and-detecting-3d-objects"></a>示例应用：扫描和检测3D 对象

[扫描和检测3D 对象](/samples/xamarin/ios-samples/ios12-scanninganddetecting3dobjects)示例是[Apple 项目](https://developer.apple.com/documentation/arkit/scanning_and_detecting_3d_objects?language=objc)的一个端口，该端口演示：

- 使用对象的应用程序状态管理 [`NSNotification`](xref:Foundation.NSNotification)
- 自定义可视化效果
- 复杂手势
- 对象扫描
- 存储 [`ARReferenceObject`](xref:ARKit.ARReferenceObject)

扫描引用对象的同时，消耗电池和处理器的旧设备在实现稳定跟踪时通常会遇到问题。

### <a name="state-management-using-nsnotification-objects"></a>使用 NSNotification 对象的状态管理

此应用程序使用状态机，该计算机在以下状态之间过渡：

- `AppState.StartARSession`
- `AppState.NotReady`
- `AppState.Scanning`
- `AppState.Testing`

在中，还使用嵌入的一组状态和转换 `AppState.Scanning` ：

- `Scan.ScanState.Ready`
- `Scan.ScanState.DefineBoundingBox`
- `Scan.ScanState.Scanning`
- `Scan.ScanState.AdjustingOrigin`

应用使用将状态转换通知发布到 [`NSNotificationCenter`](xref:Foundation.NSNotificationCenter) 和订阅这些通知的反应性体系结构。 设置如下所示 `ViewController.cs` ：

```csharp
// Configure notifications for application state changes
var notificationCenter = NSNotificationCenter.DefaultCenter;

notificationCenter.AddObserver(Scan.ScanningStateChangedNotificationName, State.ScanningStateChanged);
notificationCenter.AddObserver(ScannedObject.GhostBoundingBoxCreatedNotificationName, State.GhostBoundingBoxWasCreated);
notificationCenter.AddObserver(ScannedObject.GhostBoundingBoxRemovedNotificationName, State.GhostBoundingBoxWasRemoved);
notificationCenter.AddObserver(ScannedObject.BoundingBoxCreatedNotificationName, State.BoundingBoxWasCreated);
notificationCenter.AddObserver(BoundingBox.ScanPercentageChangedNotificationName, ScanPercentageChanged);
notificationCenter.AddObserver(BoundingBox.ExtentChangedNotificationName, BoundingBoxExtentChanged);
notificationCenter.AddObserver(BoundingBox.PositionChangedNotificationName, BoundingBoxPositionChanged);
notificationCenter.AddObserver(ObjectOrigin.PositionChangedNotificationName, ObjectOriginPositionChanged);
notificationCenter.AddObserver(NSProcessInfo.PowerStateDidChangeNotification, DisplayWarningIfInLowPowerMode);

```

典型的通知处理程序将更新 UI，并可能修改应用程序状态，例如，在扫描对象时更新此处理程序：

```csharp
private void ScanPercentageChanged(NSNotification notification)
{
    var pctNum = TryGet<NSNumber>(notification.UserInfo, BoundingBox.ScanPercentageUserKey);
    if (pctNum == null)
    {
        return;
    }
    double percentage = pctNum.DoubleValue;
    // Switch to the next state if scan is complete
    if (percentage >= 100.0)
    {
        State.SwitchToNextState();
    }
    else
    {
        DispatchQueue.MainQueue.DispatchAsync(() => navigationBarController.SetNavigationBarTitle($"Scan ({percentage})"));
    }
}

```

最后， `Enter{State}` 方法根据新状态修改模型和 UX：

```csharp
internal void EnterStateTesting()
{
    navigationBarController.SetNavigationBarTitle("Testing");
    navigationBarController.ShowBackButton(false);
    loadModelButton.Hidden = true;
    flashlightButton.Hidden = false;
    nextButton.Enabled = true;
    nextButton.SetTitle("Share", UIControlState.Normal);

    testRun = new TestRun(sessionInfo, sceneView);
    TestObjectDetection();
    CancelMaxScanTimeTimer();
}
```

### <a name="custom-visualization"></a>自定义可视化效果

应用显示投影到所检测到的水平平面的边界框内的对象的低级 "点云"。

此点云适用于属性中的开发人员 [`ARFrame.RawFeaturePoints`](xref:ARKit.ARFrame.RawFeaturePoints) 。 有效地可视化点云可能是一个棘手的问题。 遍历点，然后为每个点创建和放置新的 SceneKit 节点会终止帧速率。 或者，如果异步完成，则会有延迟。 该示例通过由三个部分构成的策略来维护性能：

- 使用 unsafe 代码就地固定数据，并将数据解释为字节的原始缓冲区。
- 将该原始缓冲区转换为 [`SCNGeometrySource`](xref:SceneKit.SCNGeometrySource) 并创建 "模板" [`SCNGeometryElement`](xref:SceneKit.SCNGeometryElement) 对象。
- 使用将原始数据和模板快速 [`SCNGeometry.Create(SCNGeometrySource[], SCNGeometryElement[])`](xref:SceneKit.SCNGeometry.Create(SceneKit.SCNGeometrySource[],SceneKit.SCNGeometryElement[]))

```csharp
internal static SCNGeometry CreateVisualization(NVector3[] points, UIColor color, float size)
{
  if (points.Length == 0)
  {
    return null;
  }

  unsafe
  {
    var stride = sizeof(float) * 3;

    // Pin the data down so that it doesn't move
    fixed (NVector3* pPoints = &amp;points[0])
    {
      // Important: Don't unpin until after `SCNGeometry.Create`, because geometry creation is lazy

      // Grab a pointer to the data and treat it as a byte buffer of the appropriate length
      var intPtr = new IntPtr(pPoints);
      var pointData = NSData.FromBytes(intPtr, (System.nuint) (stride * points.Length));

      // Create a geometry source (factory) configured properly for the data (3 vertices)
      var source = SCNGeometrySource.FromData(
        pointData,
        SCNGeometrySourceSemantics.Vertex,
        points.Length,
        true,
        3,
        sizeof(float),
        0,
        stride
      );

      // Create geometry element
      // The null and bytesPerElement = 0 look odd, but this is just a template object
      var template = SCNGeometryElement.FromData(null, SCNGeometryPrimitiveType.Point, points.Length, 0);
      template.PointSize = 0.001F;
      template.MinimumPointScreenSpaceRadius = size;
      template.MaximumPointScreenSpaceRadius = size;

      // Stitch the data (source) together with the template to create the new object
      var pointsGeometry = SCNGeometry.Create(new[] { source }, new[] { template });
      pointsGeometry.Materials = new[] { Utilities.Material(color) };
      return pointsGeometry;
    }
  }
}
```

结果类似以下形式：

![point_cloud](images/arkit_point_cloud.jpeg)

### <a name="complex-gestures"></a>复杂手势

用户可以缩放、旋转和拖动环绕目标对象的边界框。 关联的笔势识别器中有两个有趣的事情。

首先，所有手势识别器仅在达到阈值之后才会激活;例如，手指拖动了太多的像素，或者旋转超出了某个角度。 此方法是累积移动，直到超过阈值，然后增量应用：

```csharp
// A custom rotation gesture recognizer that fires only when a threshold is passed
internal partial class ThresholdRotationGestureRecognizer : UIRotationGestureRecognizer
{
    // The threshold after which this gesture is detected.
    const double threshold = Math.PI / 15; // (12°)

    // Indicates whether the currently active gesture has exceeded the threshold
    private bool thresholdExceeded = false;

    private double previousRotation = 0;
    internal double RotationDelta { get; private set; }

    internal ThresholdRotationGestureRecognizer(IntPtr handle) : base(handle)
    {
    }

    // Observe when the gesture's state changes to reset the threshold
    public override UIGestureRecognizerState State
    {
        get => base.State;
        set
        {
            base.State = value;

            switch(value)
            {
                case UIGestureRecognizerState.Began :
                case UIGestureRecognizerState.Changed :
                    break;
                default :
                    // Reset threshold check
                    thresholdExceeded = false;
                    previousRotation = 0;
                    RotationDelta = 0;
                    break;
            }
        }
    }

    public override void TouchesMoved(NSSet touches, UIEvent evt)
    {
        base.TouchesMoved(touches, evt);

        if (thresholdExceeded)
        {
            RotationDelta = Rotation - previousRotation;
            previousRotation = Rotation;
        }

        if (! thresholdExceeded && Math.Abs(Rotation) > threshold)
        {
            thresholdExceeded = true;
            previousRotation = Rotation;
        }
    }
}
```

与手势相关的第二个有趣的事情就是相对于检测到的实际平面移动边界框的方式。 此 [Xamarin 博客文章](https://blog.xamarin.com/exploring-new-ios-12-arkit-capabilities-with-xamarin/)中讨论了这一点。

## <a name="other-new-features-in-arkit-2"></a>ARKit 2 中的其他新功能

### <a name="more-tracking-configurations"></a>更多跟踪配置

现在，可以使用以下任何一项作为混合现实体验的基础：

- 只有设备加速感应 ([`AROrientationTrackingConfiguration`](xref:ARKit.AROrientationTrackingConfiguration) 、iOS 11) 
- 面部 ([`ARFaceTrackingConfiguration`](xref:ARKit.ARFaceTrackingConfiguration) ，iOS 11) 
-  (的参考映像 [`ARImageTrackingConfiguration`](xref:ARKit.ARImageTrackingConfiguration) ，iOS 12) 
- 在 iOS 12)  (扫描3D 对象 [`ARObjectScanningConfiguration`](xref:ARKit.ARObjectScanningConfiguration)
- Visual 惯性 odometry ([`ARWorldTrackingConfiguration`](xref:ARKit.ARWorldTrackingConfiguration) ，在 iOS 12) 改进

`AROrientationTrackingConfiguration`[这篇博客文章和 F # 示例](https://github.com/lobrien/FSharp_Face_AR)中讨论的内容是最有限的，并提供了较差的混合现实体验，因为它只会使数字对象与设备的运动相关，而不会尝试将设备和屏幕与真实环境相关联。

`ARImageTrackingConfiguration`利用，您可以识别真实的2d 图像 (气急败坏、徽标等 ) 并使用这些图像来定位数字图像：

```csharp
var imagesAndWidths = new[] {
    ("cover1.jpg", 0.185F),
    ("cover2.jpg", 0.185F),
     //...etc...
    ("cover100.jpg", 0.185F),
};

var referenceImages = new NSSet<ARReferenceImage>(
    imagesAndWidths.Select( imageAndWidth =>
    {
      // Tuples cannot be destructured in lambda arguments
        var (image, width) = imageAndWidth;
        // Read the image
        var img = UIImage.FromFile(image).CGImage;
        return new ARReferenceImage(img, ImageIO.CGImagePropertyOrientation.Up, width);
    }).ToArray());

configuration.TrackingImages = referenceImages;
```

此配置有两个有趣的方面：

- 这种方法很有效，可以用于可能很多的引用映像
- 数字图像将锚定到图像，即使该图像在实际情况下移动 (例如，如果书籍的封面被识别，它会在将书籍拉离货位、向下排列，等等 ) 时进行跟踪。

`ARObjectScanningConfiguration`[前面](#recognizing-reference-objects)讨论过，是用于扫描3d 对象的以开发人员为中心的配置。 它会占用大量处理器和电池，不应在最终用户应用程序中使用。 [扫描和检测三维对象](/samples/xamarin/ios-samples/ios12-scanninganddetecting3dobjects)的示例演示如何使用此配置。

最后一个跟踪配置 `ARWorldTrackingConfiguration` 是大多数混合现实体验的主力。 此配置使用 "可视惯性 odometry" 将现实世界 "功能点" 与数字图像相关联。 数字几何或子画面相对于实际的水平和垂直平面定位，或相对于检测到的 `ARReferenceObject` 实例。 在此配置中，世界原点是相机在空间中相对于重心的原始位置，数字对象相对于现实世界中的对象保持不变。

### <a name="environmental-texturing"></a>环境纹理

ARKit 2 支持使用捕获的图像来估算光照，甚至将反射高光应用于光亮对象的 "环境纹理"。 环境立方体贴图是动态构建的，一旦相机进入所有方向，就会产生极其的现实体验：

![环境纹理演示图像](images/arkit_env_texturing.png)

使用环境纹理：

- [`SCNMaterial`](xref:SceneKit.SCNMaterial)对象必须使用并为 [`SCNLightingModel.PhysicallyBased`](xref:SceneKit.SCNLightingModel.PhysicallyBased) 和指定范围0到1之间的值。 [`Metalness.Contents`](xref:SceneKit.SCNMaterial.Metalness) [`Roughness.Contents`](xref:SceneKit.SCNMaterialProperty.Contents)
- 您的跟踪配置必须设置 [`EnvironmentTexturing`](xref:ARKit.ARWorldTrackingConfiguration.EnvironmentTexturing)  =  [`AREnvironmentTexturing.Automatic`](xref:ARKit.AREnvironmentTexturing.Automatic) ：

```csharp
var sphere = SCNSphere.Create(0.33F);
sphere.FirstMaterial.LightingModelName = SCNLightingModel.PhysicallyBased;
// Shiny metallic sphere
sphere.FirstMaterial.Metalness.Contents = new NSNumber(1.0F);
sphere.FirstMaterial.Roughness.Contents = new NSNumber(0.0F);

// Session configuration:
var configuration = new ARWorldTrackingConfiguration
{
    PlaneDetection = ARPlaneDetection.Horizontal | ARPlaneDetection.Vertical,
    LightEstimationEnabled = true,
    EnvironmentTexturing = AREnvironmentTexturing.Automatic
};
```

尽管上述代码段中所示的完美反射纹理在示例中很有趣，但环境纹理可能更好地用于避免，这会触发 "特别低谷" 响应 (纹理只是) 录制的内容的估计值。

### <a name="shared-and-persistent-ar-experiences"></a>共享和持久的 AR 体验

ARKit 2 的另一个主要功能是 [`ARWorldMap`](xref:ARKit.ARWorldMap) 类，它允许您共享或存储世界跟踪数据。 您可以通过或获取当前世界 [`ARSession.GetCurrentWorldMapAsync`](xref:ARKit.ARSession.GetCurrentWorldMapAsync) 地图 [`GetCurrentWorldMap(Action<ARWorldMap,NSError>)`](xref:ARKit.ARSession.GetCurrentWorldMap(System.Action{ARKit.ARWorldMap,Foundation.NSError})) ：

```csharp
// Local storage
var PersistentWorldPath => Environment.GetFolderPath(Environment.SpecialFolder.Personal) + "/arworldmap";

// Later, after scanning the environment thoroughly...
var worldMap = await Session.GetCurrentWorldMapAsync();
if (worldMap != null)
{
    var data = NSKeyedArchiver.ArchivedDataWithRootObject(worldMap, true, out var err);
    if (err != null)
    {
        Console.WriteLine(err);
    }
    File.WriteAllBytes(PersistentWorldPath, data.ToArray());
}
```

共享或还原世界地图：

1. 从文件加载数据，
2. 将其取消存档到一个 `ARWorldMap` 对象中，
3. 将其用作属性的值 [`ARWorldTrackingConfiguration.InitialWorldMap`](xref:ARKit.ARWorldTrackingConfiguration.InitialWorldMap) ：

```csharp
var data = NSData.FromArray(File.ReadAllBytes(PersistentWorldController.PersistenWorldPath));
var worldMap = (ARWorldMap)NSKeyedUnarchiver.GetUnarchivedObject(typeof(ARWorldMap), data, out var err);

var configuration = new ARWorldTrackingConfiguration
{
    PlaneDetection = ARPlaneDetection.Horizontal | ARPlaneDetection.Vertical,
    LightEstimationEnabled = true,
    EnvironmentTexturing = AREnvironmentTexturing.Automatic,
    InitialWorldMap = worldMap
};
```

`ARWorldMap`只包含不可见的世界跟踪数据和 [`ARAnchor`](xref:ARKit.ARAnchor) 对象，它_不_包含数字资产。 若要共享几何或图像，你必须开发自己的策略，该策略适用于你的使用案例 (可能是仅存储/传输几何的位置和方向，并将其应用于静态， `SCNGeometry` 也可能是通过存储/传输序列化对象) 进行。 的好处是，在 `ARWorldMap` 相对于共享的情况下，资产 `ARAnchor` 将在设备或会话之间一致地显示。

### <a name="universal-scene-description-file-format"></a>通用场景描述文件格式

ARKit 2 的最终大字功能是 Apple 采用 Pixar 的 [通用场景说明](https://graphics.pixar.com/usd/docs/Introduction-to-USD.html) 文件格式。 此格式将 Collada 的 DAE 格式替换为用于共享和存储 ARKit 资产的首选格式。 在 iOS 12 和 Mojave 中内置了对可视化资源的支持。 USDZ 文件扩展名是包含 USD 文件的未压缩和未加密的 zip 存档。 Pixar [提供了用于处理 USD 文件的工具](https://graphics.pixar.com/usd/docs/USD-Toolset.html#USDToolset-usdedit) ，但尚不支持很多第三方支持。

## <a name="arkit-programming-tips"></a>ARKit 编程提示

### <a name="manual-resource-management"></a>手动资源管理

在 ARKit 中，手动管理资源非常重要。 这不仅能实现高帧率，而且确实 _需要_ 避免 "屏幕冻结"。 ARKit 框架在 (提供新的照相机帧时非常懒惰 [`ARSession.CurrentFrame`](xref:ARKit.ARSession.CurrentFrame) 。 [`ARFrame`](xref:ARKit.ARFrame)在当前已调用了之前 `Dispose()` ，ARKit 将不会提供新帧！ 这会导致视频 "冻结"，即使应用程序的其余部分都有响应。 解决方法是始终 `ARSession.CurrentFrame` 使用块进行访问 `using` 或对其手动调用 `Dispose()` 。

派生自的所有对象 `NSObject` 均为 `IDisposable` 并 `NSObject` 实现 [Dispose 模式](/dotnet/standard/design-guidelines/dispose-pattern)，因此，通常应遵循 [此模式，以便 `Dispose` 在派生类上实现](/dotnet/standard/garbage-collection/implementing-dispose)。

### <a name="manipulating-transform-matrices"></a>操作转换矩阵

在任何3D 应用程序中，都将处理4x4 变换矩阵，简洁地介绍如何通过三维空间移动、旋转和切变对象。 在 SceneKit 中，这些是 [`SCNMatrix4`](xref:SceneKit.SCNMatrix4) 对象。  

[`SCNNode.Transform`](xref:SceneKit.SCNNode.Transform)属性将返回 `SCNMatrix4` 的转换矩阵，该矩阵 [`SCNNode`](xref:SceneKit.SCNNode) _由_行主 `simdfloat4x4` 类型支持。 例如：

```csharp
var node = new SCNNode { Position = new SCNVector3(2, 3, 4) };  
var xform = node.Transform;
Console.WriteLine(xform);
// Output is: "(1, 0, 0, 0)\n(0, 1, 0, 0)\n(0, 0, 1, 0)\n(2, 3, 4, 1)"
```  

正如您所看到的，在末行的前三个元素中对位置进行编码。

在 Xamarin 中，操作转换矩阵的通用类型为 `NVector4` ，按约定以列为主方式进行解释。 也就是说，转换/位置组件应在 M14、M24、M34、not M41、M42、M43 中。

![行-主要与列-主要](images/arkit_row_vs_column.png)

与所选的矩阵解释保持一致对于正确的行为至关重要。 由于3D 变换矩阵是4x4，因此一致性错误不会生成任何种类的编译时甚至是运行时异常，只是操作会意外执行。 如果 SceneKit/ARKit 对象看似粘滞、飞出或抖动，则不正确的转换矩阵是一种很好的做法。 解决方案很简单： [`NMatrix4.Transpose`](xref:OpenTK.NMatrix4.Transpose*) 将执行元素的就地调换。

## <a name="related-links"></a>相关链接

- [示例应用–扫描和检测3D 对象](/samples/xamarin/ios-samples/ios12-scanninganddetecting3dobjects)
- [ARKit 2 (WWDC 2018 中的新增功能) ](https://developer.apple.com/videos/play/wwdc2018/602/)
- [了解 ARKit 跟踪和检测 (WWDC 2018) ](https://developer.apple.com/videos/play/wwdc2018/610/)
- [Xamarin 中的 ARKit 简介](../introduction-to-ios11/arkit/index.md)