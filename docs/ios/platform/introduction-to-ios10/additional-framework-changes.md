---
title: 其他 iOS 10 框架更改
description: 本文档介绍了 iOS 10 中现有框架的少量更改和增强功能，并讨论了如何在 Xamarin 中使用这些更新。
ms.prod: xamarin
ms.assetid: 0E2217F1-FC96-4D0A-ABAB-D40AD8F96502
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: b5dbfe4de21cd359e89d84db310a1e1905ddbb1a
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434598"
---
# <a name="additional-ios-10-frameworks-changes"></a>其他 iOS 10 框架更改

_本文介绍适用于 iOS 10 的现有框架的其他较小的更改或增强功能。_

## <a name="av-foundation-framework-additions"></a>AV 基础框架添加

AVFoundation 框架包括以下增强功能：

- 在 iOS 10 中，开发人员不再需要基于内容类型实现不同的 [AVPlayerItem](xref:AVFoundation.AVPlayerItem) 行为。 只需设置 `Rate` 属性，AVFoundation 就会确定在没有停止的情况下是否有足够的内容可用于播放。
- 新的 [AVCapturePhotoOutput](xref:AVFoundation.AVCaptureFileOutput) 类取代了弃用的 `AVCaptureStillImageOutput` 类，并提供了一种统一的方法来处理所有摄影工作流，方法是提供对捕获过程的复杂控制和监视，并支持新功能（如实时照片和原始捕获格式）。
- 使用新 `AVPlayerLooper` 类可以更轻松地在播放期间循环给定的媒体。
- `AVAssetDownloadURLSession`类允许下载和以后播放 FairPlay 加密的 HLS 流。
- 默认情况下，当设备硬件支持时， [AVCaptureSession](xref:AVFoundation.AVCaptureSession) 类会自动支持宽颜色宽范围捕获。 有关更多详细信息，请参阅 Apple 的 [IOS 设备兼容性参考](https://developer.apple.com/library/prerelease/content/documentation/DeviceInformation/Reference/iOSDeviceCompatibility/Introduction/Introduction.html#//apple_ref/doc/uid/TP40013599) 。

## <a name="avkit-additions"></a>添加 AVKit

AVKit 框架现在包含新 `UpdatesNowPlayingInfoCenter` 属性，以指示何时应更新正在播放的信息中心。

## <a name="core-data-enhancements"></a>核心数据增强

iOS 10 对核心数据框架包括以下增强功能：

- 在 WAL 日志模式下，具有 SQLite 数据存储的 [NSManagedObjectContext](xref:CoreData.NSManagedObjectContext) 对象支持新的查询生成功能，在该功能中，托管对象上下文 (MOC) 可以固定到特定数据库版本，以供将来获取和出错事务。
- 根 [NSManagedObjectContext](xref:CoreData.NSManagedObjectContext) 对象支持并发错误和不序列化的提取。
- [NSPersistentStoreCoordinator](xref:CoreData.NSPersistentStoreCoordinator)类维护 SQLite 数据存储的池。
- 添加了几个新的便利方法，以便 `NSManagedObject` 更轻松地执行提取和创建子类。
- 使用高级 `NSPersistenceContainer` 引用 `NSPersistentStoreCoordinator` 、 [NSManagedObjectModel](xref:CoreData.NSManagedObjectModel) 和其他核心数据配置资源。

有关详细信息，请参阅 Apple 的 [核心数据框架参考](https://developer.apple.com/reference/coredata)。

## <a name="core-image-enhancements"></a>核心映像增强功能

iOS 10 对核心映像框架进行了以下改进：

- 开发人员现在可以通过在处理之前和之后转换颜色空间来处理核心图像上下文工作颜色空间之外的颜色空间中的图像。
- 对于使用 A8 或 A9 Cpu 的 iOS 设备，现在支持原始映像格式。 核心映像现在支持从内置的 iSight 照相机或第三方相机对原始图像进行解码。 使用 `FilterWithImageData` `FilterWithImageURL` [CIFilter](xref:CoreImage.CIFilter) 类的或方法来处理原始映像。
- 已进行了多个呈现性能改进，以便 `UIImage` 在由核心映像映像 (备份时将) `UIImageView` 对象中。
- `UIImage` 标记为宽域的对象将在 `UIImageView` 支持宽色的 iOS 设备上的对象中呈现为广角颜色。
- 核心映像内核代码现在可以请求特定的像素输出格式。
- `ImageWithExtent` [CIFilter](xref:CoreImage.CIFilter)类的方法可用于将自定义处理插入筛选器操作。 核心映像将在处理图像以进行输出或显示时调用筛选器之间的给定回调。

此外，还添加了以下新的核心映像筛选器：

- `CINinePartTiled`
- `CINinePartStretched`
- `CIHueSaturationValueGradient`
- `CIEdgePreserveUpsampleFilter`
- `CIClamp`

## <a name="core-motion-additions"></a>核心动作添加

作为 iOS 10 的新手，核心运动框架包括 pedometer 事件，使应用程序能够在运行时，可以快速接收用户暂停和继续跟踪的实时通知。 使用 [CMPedometer](xref:CoreMotion.CMPedometer) 注册前台或后台 pedometer 事件。

## <a name="foundation-enhancements"></a>基础增强功能

已对适用于 iOS 10 的基础框架进行了以下改进：

- 使用新的 [NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter) 类来设置要向最终用户显示的本地化度量值的格式。
- 使用新的 [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) 类可以进行日期和时间间隔计算，如持续时间，用于比较间隔和测试间隔交点。
- 使用新的 [NSMeasurement](https://developer.apple.com/reference/foundation/nsmeasurement) 类在不同的度量单位 (UOM) 之间进行转换，或对不同 UOMs 中的值执行计算。

- 使用新的 [NSUnit](https://developer.apple.com/reference/foundation/nsunit) 和 [NSDimension](https://developer.apple.com/reference/foundation/nsdimension) 类来表示特定的 UOMs。
- 向 [NSLocal](https://developer.apple.com/reference/foundation/nslocale) 类添加了几个新属性，以获取本地信息和可用的显示格式。

## <a name="gamekit-enhancements"></a>Gamekit\ 增强功能

IOS 10 中对 Gamekit\ 框架进行了以下改进：

- **Game Center 应用**已弃用，并已从 iOS 中删除。 如果应用使用 Gamekit\，它 _必须_ 提供其自己的接口以显示 gamekit\ 功能，如排行榜等。
- [GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer)类已经实现了新的仅限 iCloud 的帐户类型。
- 新的 [GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession) 类提供了用于在 Game Center 上管理持久数据存储的通用解决方案。 `GKGameSession` 维护玩家列表，应用负责实现在参与者之间存储、检索或交换参与者日期的方式和时间。 在许多情况下，游戏会话可以替换现有的基于转换的匹配、实时匹配或持久的游戏保存方法。

## <a name="gameplaykit-enhancements"></a>GameplayKit 增强功能

IOS 10 中对 GameplayKit 框架进行了以下改进：

- 使用新的 [GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph) 类来提供高性能、外观上的路径。
- 添加了过程干扰，并可用于在自然的纹理中提高真实感，增加了对相机运动的真实感，并帮助生成了丰富的游戏世界。
- 使用空间分区对游戏世界数据进行分区，以便高效搜索。
- 添加了新的 Monte Carlo 战略家 ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) 。
- 已使用新的 [GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d) 和 [GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d) 类将3d 支持添加到现有代理和路径查找行为。
- 新的 [GKScene](https://developer.apple.com/reference/gameplaykit/gkscene) 和 [GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent) 类使得 GameplayKit 和 SpriteKit 的组合比以往更容易。
- 添加了新的决策树 API ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree) 和 [GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) 增强游戏构建 AI。

## <a name="healthkit-enhancements"></a>HealthKit 增强功能

IOS 10 中对 HealthKit 框架进行了以下改进：

- 添加了新的元数据密钥，以用于天气类型 (例如 `HKWeatherConditionClear` 和 `HKWeatherConditionCloudy`) 和健身类型 (如 `HKWorkoutActivityTypeFlexibility` 和 `HKWorkoutActivityTypeWheelchairRunPace`) 。
- 添加了新 `HKCDADocument` 类，以表示 (CDA) 格式的文档的临床文档体系结构。
- 使用新的 [HKWorkoutConfiguration](https://developer.apple.com/reference/healthkit/hkworkoutconfiguration) 类指定健身的 `ActivityType` 和 `LocationType` 。
- 添加了新的 [HKWheelchairUseObject](https://developer.apple.com/reference/healthkit/hkwheelchairuseobject) 和 `WheelchairUse` [HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore) 类的方法，以便使用轮椅相关的运行状况数据。

## <a name="homekit-enhancements"></a>HomeKit 增强功能

IOS 10 中对 HomeKit 框架进行了以下改进：

- 添加了新的服务和特性。
- IPad 可以配置为充当 HomeKit 中心，以提供远程访问权限、运行自动化触发器和启用共享用户权限。
- 为相机和 doorbell 附件添加了支持。
- 为附件提供了更多上下文和配置。

有关详细信息，请参阅 [HomeKit 文档简介](~/ios/platform/homekit.md) 。

## <a name="metal-enhancements"></a>金属增强功能

IOS 10 中的金属框架进行了以下改进：

- 3D 应用和游戏现在可以通过 GPU 使用 _分割_ 来有效地呈现复杂的场景和几何。
- 提供资源分配的精细控制，使用资源堆和 Memoryless 呈现目标优化基于金属的应用的性能。
- 使用函数特殊化为场景创建高优化的材料和光源组合函数集合。

若要了解详细信息，请参阅 Apple 的 [金属编程指南](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221)。

## <a name="modelio-enhancements"></a>ModelIO 增强功能

IOS 10 中对 ModelIO 框架进行了以下改进：

- 现在支持 USD 文件格式。
- 已将已签名的距离字段支持添加到了 [MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray) 类中。
- 使用新 `MDLLightProbeIrradianceDataSource` 类来帮助进行轻型探测放置。
- 使用新 `MDLMaterialPropertyGraph` 类可轻松支持对模型的运行时更改。

## <a name="photos-enhancements"></a>照片增强功能

IOS 10 中的照片框架进行了以下改进：

- 使用 [CIImageProcessorInput](https://developer.apple.com/reference/coreimage/ciimageprocessorinput) 和 [CIImageProcessorOutput](https://developer.apple.com/reference/coreimage/ciimageprocessoroutput) 类，利用新的核心映像处理器功能执行编辑。
- 实时照片编辑现在适用于支持照片框架和照片编辑扩展的应用程序， (在) 的照片和照相机应用程序中使用。
- 使用新的 [PHLivePhotoEditingContext](https://developer.apple.com/reference/photos/phlivephotoeditingcontext) 类将编辑同时应用于视频和活动照片的内容。

## <a name="replaykit-enhancements"></a>ReplayKit 增强功能

IOS 10 中对 ReplayKit 框架进行了以下改进：

- 使用 [RPScreenRecorder](https://developer.apple.com/reference/replaykit/rpscreenrecorder)、 [RPBroadcastActivityViewController](https://developer.apple.com/reference/replaykit/rpbroadcastactivityviewcontroller) 和 [RPBroadcastController](https://developer.apple.com/reference/replaykit/rpbroadcastcontroller) 类支持通过第三方站点广播录制的媒体。
- 在应用中支持 ReplayKit 第三方广播服务需要广播 UI 和 Broadcast Upload 扩展。

## <a name="scenekit-enhancements"></a>SceneKit 增强功能

IOS 10 中对 SceneKit 框架进行了以下改进：

- 使用 HDR 特性和效果， [SCNCamera](xref:SceneKit.SCNCamera) 类可提供更大的真实感。 使用自适应曝光创建自动效果，或使用 vignetting、颜色 fringing 和颜色评分向游戏添加 fillmatic 效果。
- SceneKit 现在提供了一个新的基于物理的渲染 (.PBR) 系统，通过更简单的资产创作获得更真实的结果。
- 使用新的 [SCNLightingModelPhysicallyBased](https://developer.apple.com/reference/scenekit/scnlightingmodelphysicallybased) 明暗度模型来模拟各种现实的阴影效果，同时只需 (和) 三个基础属性 `Diffuse` `Metalness` `Roughness` 。
- 由于 .PBR 着色最适用于基于环境的照明，因此使用 `LightingEnvironment` 属性将基于图像的照明分配到整个场景。
- 使用 `IESProfileURL` 属性可以导入基于真实世界值（如流明) 中的亮度 (和) 开氏度 (的色温温度）定义光照的实际光源灯具。
- 与传统的呈现技术相比，.PBR 和 HDR 相机功能提供了更好的结果，因此，SceneKit 现在会在线性颜色空间中执行所有颜色计算 (使用彩色设备上的 P3 色域显示) 。
- SceneKit 通过阅读颜色配置文件信息，使颜色与所有颜色匹配。
- SceneKit 解释所有着色器类型的线性 RGB 颜色空间中的颜色分量值。
- 可以通过 `SCNDisableLinearSpaceRendering` `SCNDisableWideGamut` 在应用的中指定和键来禁用线性颜色空间渲染和宽颜色 `Info.plist` 。
- 生成任意多边形灵长类动物 (从文件加载或以编程方式生成) 以通过新的 [SCNGeometryPrimitiveTypePolygon](https://developer.apple.com/documentation/scenekit/scngeometryprimitivetype/scngeometryprimitivetypepolygon) 类指定几何图形。
- 由于 SceneKit 读取和调整纹理图像中的颜色配置文件信息，因此请对所有图像使用资产目录，以确保提供此信息。

## <a name="spritekit-enhancements"></a>SpriteKit 增强功能

IOS 10 中对 SpriteKit 框架进行了以下改进：

- 自定义着色器可以 (`SKAttribute`) 提供属性，这些属性可通过 () 提供属性值，通过使用着色器的每个节点单独进行配置 `SKAttributeValue` 。
- Tilemaps 现在支持使用 `SKTileMapMode` 、 `SKTileGroup` `SKTileGroupRule` 和类的2d、2.5 d 和侧滚动游戏的正方形、六边形和等角磁贴形状 `SKTileSet` 。
- 使用新 `SKWarpGeometry` 类伸展或扭曲 [SKSpriteNode](xref:SpriteKit.SKSpriteNode) 或 [SKEffectNode](xref:SpriteKit.SKEffectNode) 渲染。 新的 [SKAction](xref:SpriteKit.SKAction) 类可用于对扭曲效果之间的转换进行动画处理。
- [SKView](xref:SpriteKit.SKView)类提供了几种新的方法，可对场景的呈现时间和方式进行精细的控制。

## <a name="scrollview-enhancements"></a>ScrollView 增强功能

已在 iOS 10.3 中对 ScrollView 控件进行了以下改进：

- `UIScrollView` 现在包含 `IndexDisplayMode` 属性，用于控制在用户滚动时如何显示索引 `UIScrollViewIndexDisplayMode` ：
  - `Automatic` -索引显示由操作系统控制。
  - `AlwaysHidden` -始终隐藏索引显示。

有关用法，请参阅 [IOSTenThree 示例](/samples/xamarin/ios-samples/ios10-iostenthree) 。

## <a name="uikit-enhancements"></a>UIKit 增强功能

IOS 10 中对 UIKit 框架进行了以下改进：

- 新的 [UIPasteboard](xref:UIKit.UIPasteboard) API 提供新选项 (例如生存期限制) 并将为常见类类型自动声明兼容的内容类型。
- 添加了新的完全交互式的、基于对象的、可中断的动画支持，并可链接到手势。 有关详细信息，请参阅 Apple 的 [UIViewAnimating 协议参考](https://developer.apple.com/reference/uikit/uiviewanimating)、 [UIViewPropertyAnimator 类参考](https://developer.apple.com/reference/uikit/uiviewpropertyanimator)、 [UITimingCurveProvider 协议参考](https://developer.apple.com/reference/uikit/uitimingcurveprovider)、 [UICubicTimingParameters 类参考](https://developer.apple.com/reference/uikit/uicubictimingparameters) 和 [UISpringTimingParameter 类参考](https://developer.apple.com/reference/uikit/uispringtimingparameters) 。
- 新的 `UIPreviewInteraction` 和 `UIPreviewInteractionDelegate` 允许开发人员应用为速览和 pop 操作提供自定义界面。
- 新 `UIAccessibilityCustomRotor` 类允许应用为辅助技术（例如语音）提供自定义的上下文特定功能。
- 使用 `UIAccessibilityIsAssistiveTouchRunning` 和 `UIAccessibilityAssistiveTouchStatusDidChangeNotification` 符号来确定是否启用了 AssistiveTouch。
- 使用 `UIAccessibilityHearingDevicePairedEar` 和 `UIAccessibilityHearingDevicePairedEarDidChangeNotification` 符号可获取任何配对 MFi 听觉助手的状态。
- 为了支持标签中的动态类型，文本字段和文本框使用类的新 `PreferredFontForTextStyle` 方法 `UIFont` 。
- 若要确定在设备发生更改时元素是否应更新其字体 `UIContentSizeCategory` ，请使用 `AdjustsFontForContentSizeCategory` 委托的属性 `UIContentSizeCategoryAdjusting` 。
- `OpenURL`类的方法 `UIApplication` 是异步调用的，现在支持在打开操作完成后调用的完成处理程序。
- 使用新的和类启动 CloudKit 共享并修改其属性 `UICloudSharingController` `UICloudSharingControllerDelegate` 。
- 利用预提取的单元， `UICollectionViews` 通过新委托提高滚动体验 `UICollectionViewDataSourcePrefetching` 。
- 现在，开发人员可以控制选项卡栏项的外观 (如文本和背景色) 。
- 现在，所有滚动视图和滚动视图子类 (如) 支持刷新控件 `UICollectionView` 。

## <a name="webkit-enhancements"></a>WebKit 增强功能

IOS 10 中对 WebKit 框架进行了以下改进：

- 已向类添加了速览和 pop 支持 `WKWebView` 。 使用 `ShouldPreviewElement` 方法来确定给定的 web 视图是否应显示预览。

## <a name="related-links"></a>相关链接

- [iOS 10 示例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS10)
- [IOS 10 中的新增功能](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewIniOS/Articles/iOS10.html#//apple_ref/doc/uid/TP40017084-SW1)