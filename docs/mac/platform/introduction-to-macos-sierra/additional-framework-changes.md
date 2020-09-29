---
title: 其他 macOS Sierra Framework 更改
description: 本文档介绍 macOS Sierra 中引入的现有框架的小更改和增强功能。 它会检查加速框架、AppKit、AVFoundation、Core 数据、核心映像、基础等的更改。
ms.prod: xamarin
ms.assetid: CA701269-D11E-4DE3-89C1-58EF8993A482
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 9bee1cb5108053dac74fcde4f4f5171c24c7ce1a
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91437220"
---
# <a name="additional-macos-sierra-framework-changes"></a>其他 macOS Sierra Framework 更改

<a name="Accelerate-Framework-Enhancements"></a>

## <a name="accelerate-framework-enhancements"></a>加速框架增强

已对 macOS Sierra 的加速框架进行了以下改进：

- 添加了 Quadrature (整型微积分) 。
- 添加了用于构造神经网络的基本函数。
- 添加了要测试的几何谓词函数，例如两个几何对象的交集。

<a name="AppKit-Framework-Enhancements"></a>

## <a name="appkit-framework-enhancements"></a>AppKit 框架增强功能

已对 macOS Sierra 的 AppKit 框架进行了以下改进：

- 一些增强功能， `NSCollectionView` 例如：
  - 可**折叠部分**-允许用户将集合视图部分折叠到单个水平行中。
  - 可以使用与 iOS 中[UICollectionView](https://developer.apple.com/reference/uikit/uicollectionview)相同的 API，在流布局中浮动**标头**-标头和表尾)  (。
  - 可**滚动的背景视图**-现在可以将 "集合视图背景" 设置为随内容一起滚动。
- 已对延迟的视图布局经过优化和扩展。
- 拖放 API 现在包含新的 `NSFilePromiseProvider` 和 `NSFilePromiseReceiver` 类以支持拖动群。
- 已向现有控件添加了几个便利性构造函数：
  - `NSButton` 包含用于创建推送按钮、复选框和单选按钮的新构造函数。
  - `NSTextField` 包括用于创建环绕和非包装标签、特性化标签和可编辑文本字段的新构造函数。
  - `NSSegmentedControl` 包含用于从一组标签或图像创建分段控件的新构造函数。
  - `NSSlider` 包括用于创建水平线性滑块的新构造函数。
  - `NSImageView` 包括用于从给定创建不可编辑的图像视图的新构造函数 `NSImage` 。
- 添加了新的 `NSGridView` ，以自动将子视图的集合布局为具有可变大小的行和列的网格，可以动态隐藏或显示这些列。

<a name="AVFoundation-Framework-Enhancements"></a>

## <a name="avfoundation-framework-enhancements"></a>AVFoundation 框架增强功能

已对 macOS Sierra 的 AVFoundation 框架进行了以下改进：

- 在 macOS 中，应用程序不再需要基于内容类型实现不同的 [AVPlayerItem](https://developer.apple.com/reference/avfoundation/avplayeritem) 行为。 只需设置 `Rate` 属性，AVFoundation 就会确定在没有停止的情况下是否有足够的内容可用于播放。
- 使用新 `AVPlayerLooper` 类可以更轻松地在播放期间循环给定的媒体。
- `AVAssetDownloadURLSession`类允许下载和以后播放 FairPlay 加密的 HLS 流。

<a name="Core-Data-Framework-Enhancements"></a>

## <a name="core-data-framework-enhancements"></a>核心数据框架增强功能

针对 macOS Sierra 的核心数据框架进行了以下改进：

- 根 [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) 对象支持并发错误和不序列化的提取。
- [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator)类维护 SQLite 数据存储的池。
- 在 WAL 日志模式下，具有 SQLite 数据存储的 [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) 对象支持新的查询生成功能，在该功能中，托管对象上下文 (MOC) 可以固定到特定数据库版本，以供将来获取和出错事务。
- 使用高级 `NSPersistenceContainer` 引用 `NSPersistentStoreCoordinator` 、 [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel) 和其他核心数据配置资源。
- 添加了几个新的便利方法，以便 `NSManagedObject` 更轻松地执行提取和创建子类。

有关详细信息，请参阅 Apple 的 [核心数据框架参考](https://developer.apple.com/reference/coredata)。

<a name="Core-Image-Framework-Enhancements"></a>

## <a name="core-image-framework-enhancements"></a>核心映像框架增强功能

已对 macOS Sierra 的核心图像框架进行了以下改进：

- `ImageWithExtent` [CIFilter](https://developer.apple.com/reference/coreimage/cifilter)类的方法可用于将自定义处理插入筛选器操作。 核心映像将在处理图像以进行输出或显示时调用筛选器之间的给定回调。
- 应用现在可以通过在处理之前和之后转换颜色空间来处理核心图像上下文工作颜色空间之外的颜色空间中的图像。
- 核心映像内核现在可以请求特定的像素输出格式。
- 添加了以下新的映像筛选器： `CINinePartTitled` 、 `CINinePartStretched` 、 `CIHueSaturationValueGradient` `CIEdgePreserveUpsampleFilter` 和 `CIClamp` 。

<a name="Foundation-Framework-Enhancements"></a>

## <a name="foundation-framework-enhancements"></a>基础框架增强功能

已对 macOS Sierra 的基础框架进行了以下改进：

- 使用 [NSDimentions](https://developer.apple.com/reference/foundation/nsdimension) API 来表示、转换和显示许多最常见的物理单位，例如大容量、长度、速度、持续时间和温度。
- 使用 [NSISO8601DateFormatter](https://developer.apple.com/reference/foundation/nsiso8601dateformatter) 类来分析和生成 ISO 8601 格式的日期。
- 使用新的 [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) 类可以进行日期和时间间隔计算，如持续时间，用于比较间隔和测试间隔交点。
- 使用 [NSPersonNameComponentsFormatter](https://developer.apple.com/reference/foundation/nspersonnamecomponentsformatter) 类可从字符串分析人员姓名的元素。
- 使用新的 [NSURLSessionTaskMetrics](https://developer.apple.com/reference/foundation/nsurlsessiontaskmetrics) 类获取 URL 网络会话的指标。

有关详细信息，请参阅 [适用于 OS X v 10.12 和 iOS 10 的 Apple Foundation 发行说明](https://developer.apple.com/library/prerelease/content/releasenotes/Miscellaneous/RN-Foundation-OSX10.12/index.html)。

<a name="GameKit-Framework-Enhancements"></a>

## <a name="gamekit-framework-enhancements"></a>Gamekit\ 框架增强功能

已对 macOS Sierra 的 Gamekit\ 框架进行了以下改进：

- **Game Center 应用**已弃用，并已从 macOS 中删除。 如果应用使用 Gamekit\，它 _必须_ 提供其自己的接口以显示 gamekit\ 功能，如排行榜等。
- [GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer)类已经实现了新的仅限 iCloud 的帐户类型。
- 新的 [GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession) 类提供了用于在 Game Center 上管理持久数据存储的通用解决方案。 `GKGameSession` 维护播放器列表，应用负责实现如何以及何时在参与者之间存储、检索或交换参与者日期。 在许多情况下，游戏会话可以替换现有的基于转换的匹配、实时匹配或持久的游戏保存方法。

<a name="GamePlayKit-Framework-Enhancements"></a>

## <a name="gameplaykit-framework-enhancements"></a>GamePlayKit 框架增强功能

已对 macOS Sierra 的 GamePlayKit 框架进行了以下改进：

- 添加了过程干扰，并可用于在自然的纹理中提高真实感，增加了对相机运动的真实感，并帮助生成了丰富的游戏世界。
- 使用空间分区对游戏世界数据进行分区，以便高效搜索。
- 添加了新的 Monte Carlo 战略家 ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) 。
- 添加了新的决策树 API ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree) 和 [GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) 增强游戏构建 AI。
- 已使用新的 [GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d) 和 [GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d) 类将3d 支持添加到现有代理和路径查找行为。
- 使用新的 [GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph) 类来提供高性能、外观上的路径。
- 新的 [GKScene](https://developer.apple.com/reference/gameplaykit/gkscene) 和 [GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent) 类使得 GameplayKit 和 SpriteKit 的组合比以往更容易。

<a name="Metal-Framework-Enhancements"></a>

## <a name="metal-framework-enhancements"></a>金属框架增强功能

已对 macOS Sierra 的金属框架进行了以下改进：

- 3D 应用和游戏现在可以通过 GPU 使用 _分割_ 来有效地呈现复杂的场景和几何。
- 使用函数特殊化为场景创建高优化的材料和光源组合函数集合。
- 提供资源分配的精细控制，使用资源堆和 Memoryless 呈现目标优化基于金属的应用的性能。

若要了解详细信息，请参阅 Apple 的 [金属编程指南](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221)。

<a name="ModelIO-Framework-Enhancements"></a>

## <a name="model-io-framework-enhancements"></a>模型 i/o 框架增强功能

已对 macOS Sierra 的模型 i/o 框架进行了以下改进：

- 现在支持 USD 文件格式。
- 使用新 `MDLMaterialPropertyGraph` 类可轻松支持对模型的运行时更改。
- 已将已签名的距离字段支持添加到了 [MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray) 类中。
- 使用新 `MDLLightProbeIrradianceDataSource` 类来帮助进行轻型探测放置。

<a name="Photos-Framework-Enhancements"></a>

## <a name="photos-framework-enhancements"></a>照片框架增强功能

已对 macOS Sierra 的照片框架进行了以下改进：

- 实时照片编辑现在适用于支持照片框架和照片编辑扩展的应用程序， (在) 的照片和照相机应用程序中使用。
- 使用新的 [PHLivePhotoEditingContext](https://developer.apple.com/reference/photos/phlivephotoeditingcontext) 类将编辑同时应用于视频和活动照片的内容。
- 使用 [CIImageProcessorInput](https://developer.apple.com/reference/coreimage/ciimageprocessorinput) 和 [CIImageProcessorOutput](https://developer.apple.com/reference/coreimage/ciimageprocessoroutput) 类，利用新的核心映像处理器功能执行编辑。
- 若要支持实时照片， [PHLivePhoto](https://developer.apple.com/reference/photos/phlivephoto) 和 [PHLivePhotoView](https://developer.apple.com/reference/photosui/phlivephotoview) 类已从 iOS 移植到 macOS。

<a name="SceneKit-Framework-Enhancements"></a>

## <a name="scenekit-framework-enhancements"></a>SceneKit 框架增强功能

已对 macOS Sierra 的 SceneKit 框架进行了以下改进：

- 现在提供了一个新的基于物理的渲染 (.PBR) 系统，通过更简单的资产创作获得更真实的结果。
- 使用新的 [SCNLightingModelPhysicallyBased](https://developer.apple.com/reference/scenekit/scnlightingmodelphysicallybased) 明暗度模型来模拟各种现实的阴影效果，同时只需 (和) 三个基础属性 `Diffuse` `Metalness` `Roughness` 。
- 由于 .PBR 着色最适用于基于环境的照明，因此使用 `LightingEnvironment` 属性将基于图像的照明分配给茶色的整个场景。
- 使用 " `IESProfileURL` 属性" 可导入实际温度装置，这些光源定义实世界值（如) 流明中的强度 (）和 "开氏度) 中的色温 ("。
- 使用 HDR 特性和效果， [SCNCamera](https://developer.apple.com/reference/scenekit/scncamera) 类可提供更大的真实感。 使用自适应曝光创建自动效果，或使用 vignetting、颜色 fringing 和颜色评分向游戏添加 filmatic 效果。
- 与传统的呈现技术相比，.PBR 和 HDR 相机功能提供了更好的结果，因此，SceneKit 现在会在线性颜色空间中执行所有颜色计算 (使用彩色设备上的 P3 色域显示) 。
- SceneKit 通过阅读颜色配置文件信息，使颜色与所有颜色匹配。
- SceneKit 解释所有着色器类型的线性 RGB 颜色空间中的颜色分量值。
- 由于 SceneKit 读取和调整纹理图像中的颜色配置文件信息，因此请对所有图像使用资产目录，以确保提供此信息。
- 可以通过 `SCNDisableLinearSpaceRendering` `SCNDisableWideGamut` 在应用的中指定和键来禁用线性颜色空间渲染和宽颜色 `Info.plist` 。
- 生成任意多边形灵长类动物 (从文件加载或以编程方式生成) 以通过新的 [SCNGeometryPrimitiveTypePolygon](https://developer.apple.com/documentation/scenekit/scngeometryprimitivetype/scngeometryprimitivetypepolygon?language=objc) 类指定几何图形。

<a name="Security-Framework-Enhancements"></a>

## <a name="security-framework-enhancements"></a>安全框架增强功能

已对 macOS Sierra 的安全框架进行了以下改进：

- 在 `SecKey` 所有平台 (iOS、tvOS、watchOS 和 macOS) 中，接口已现代化且统一。

<a name="SpriteKit-Framework-Enhancements"></a>

## <a name="spritekit-framework-enhancements"></a>SpriteKit 框架增强功能

已对 macOS Sierra 的 SpriteKit 框架进行了以下改进：

- Tilemaps 现在支持使用 `SKTileMapMode` 、 `SKTileGroup` `SKTileGroupRule` 和类的2d、2.5 d 和侧滚动游戏的正方形、六边形和等角磁贴形状 `SKTileSet` 。
- 使用新 `SKWarpGeometry` 类伸展或扭曲 [SKSpriteNode](https://developer.apple.com/reference/spritekit/skspritenode) 或 [SKEffectNode](https://developer.apple.com/reference/spritekit/skeffectnode) 渲染。 新的 [SKAction](https://developer.apple.com/reference/spritekit/skaction) 类可用于对扭曲效果之间的转换进行动画处理。
- 自定义着色器可以 (`SKAttribute`) 提供属性，这些属性可通过 () 提供属性值，通过使用着色器的每个节点单独进行配置 `SKAttributeValue` 。
- [SKView](https://developer.apple.com/reference/spritekit/skview)类提供了几种新的方法，可对场景的呈现时间和方式进行精细的控制。

<a name="New-Frameworks"></a>

## <a name="new-frameworks"></a>新框架

以下框架已添加到 macOS Sierra：

- **意向框架** -此框架允许应用检查交互 (例如) 的位置或用户操作，并根据该信息采取措施。
- **SafariServices 框架** -此框架允许应用为 Safari (（例如 MacOS 和 iOS 的内容) 阻止程序）开发应用扩展。

## <a name="related-links"></a>相关链接

- [Mac 示例](/samples/browse/?products=xamarin&term=Xamarin.Mac)
- [OS X 10.12 中的新增功能](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)