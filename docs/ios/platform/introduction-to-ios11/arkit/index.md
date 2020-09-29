---
title: Xamarin 中的 ARKit 简介
description: 本文档介绍 iOS 11 with ARKit 中增加的现实。 它讨论了如何向应用程序添加3D 模型，如何配置视图，如何实现会话委托，如何将三维模型置于世界上，以及如何暂停扩充的现实会话。
ms.prod: xamarin
ms.assetid: 70291430-BCC1-445F-9D41-6FBABE87078E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/30/2017
ms.openlocfilehash: 6803ecf2303ff2c91265f3ac8352a7aa15e74d40
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436206"
---
# <a name="introduction-to-arkit-in-xamarinios"></a>Xamarin 中的 ARKit 简介

_IOS 11 的增强现实_

ARKit 实现了各种增加的现实应用程序和游戏。 本部分涵盖了以下主题：

- [入门与 ARKit](#gettingstarted)
- [将 ARKit 与 UrhoSharp 配合使用](urhosharp.md)

<a name="gettingstarted"></a>

## <a name="getting-started-with-arkit"></a>入门与 ARKit

若要开始使用增强的现实，以下说明将指导你完成一个简单的应用程序：定位3D 模型并让 ARKit 将模型与其跟踪功能保持不变。

![相机图像中的 Jet 3D 模型](images/jet-sml.png)

### <a name="1-add-a-3d-model"></a>1. 添加三维模型

应通过 **SceneKitAsset** 生成操作将资产添加到项目。

![SceneKit 项目中的资产](images/scene-assets.png)

### <a name="2-configure-the-view"></a>2. 配置视图

在视图控制器的 `ViewDidLoad` 方法中，加载场景资产，并在 `Scene` 视图上设置属性：

```csharp
ARSCNView SceneView = (View as ARSCNView);

// Create a new scene
var scene = SCNScene.FromFile("art.scnassets/ship");

// Set the scene to the view
SceneView.Scene = scene;
```

### <a name="3-optionally-implement-a-session-delegate"></a>3. 可以选择实现会话委托

尽管简单情况并不是必需的，但实现会话委托对于调试 ARKit 会话状态 (和实际应用程序非常有用，可向用户) 提供反馈。 使用以下代码创建简单委托：

```csharp
public class SessionDelegate : ARSessionDelegate
{
  public SessionDelegate() {}
  public override void CameraDidChangeTrackingState(ARSession session, ARCamera camera)
  {
    Console.WriteLine("{0} {1}", camera.TrackingState, camera.TrackingStateReason);
  }
}
```

在方法的中分配委托 `ViewDidLoad` ：

```csharp
// Track changes to the session
SceneView.Session.Delegate = new SessionDelegate();
```

### <a name="4-position-the-3d-model-in-the-world"></a>4. 将三维模型置于世界中

在中 `ViewWillAppear` ，以下代码将建立 ARKit 会话，并将3d 模型的位置设置为相对于设备照相机的空间：

```csharp
// Create a session configuration
var configuration = new ARWorldTrackingConfiguration {
  PlaneDetection = ARPlaneDetection.Horizontal,
  LightEstimationEnabled = true
};

// Run the view's session
SceneView.Session.Run(configuration, ARSessionRunOptions.ResetTracking);

// Find the ship and position it just in front of the camera
var ship = SceneView.Scene.RootNode.FindChildNode("ship", true);

ship.Position = new SCNVector3(2f, -2f, -9f);
```

每次运行或恢复应用程序时，3D 模型都将位于照相机的前方。 定位模型后，移动相机并观看 ARKit，使模型保持定位。

### <a name="5-pause-the-augmented-reality-session"></a>5. 暂停扩充的现实会话

当视图控制器在方法中 (不可见时暂停 ARKit 会话是一种很好的做法 `ViewWillDisappear` ：

```csharp
SceneView.Session.Pause();
```

## <a name="summary"></a>总结

上述代码会生成一个简单的 ARKit 应用程序。 更复杂的示例是，要托管扩大现实会话的视图控制器实现 `IARSCNViewDelegate` 并实现其他方法。

ARKit 提供大量更复杂的功能，如面跟踪和用户交互。 有关将 ARKit 跟踪与 UrhoSharp 结合使用的示例，请参阅 [UrhoSharp 演示](urhosharp.md) 。

## <a name="related-links"></a>相关链接

- [ (Apple) 扩充的现实 ](https://developer.apple.com/arkit/)
- [将 ARKit 与 UrhoSharp 配合使用](urhosharp.md)
- [简单的 ARKit (Jet) 示例](/samples/xamarin/ios-samples/ios11-arkitsample)
- [ARKit 放置对象 (示例) ](/samples/xamarin/ios-samples/ios11-arkitplacingobjects)
- [引入 ARKit-)  (视频的 iOS (扩充的现实) ](https://developer.apple.com/videos/play/wwdc2017/602/)