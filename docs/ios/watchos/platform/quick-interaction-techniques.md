---
title: Xamarin 中 watchOS 3 的快速交互技术
description: 本文介绍了 Apple 在 watchOS 3 中添加的快速交互技术，以及如何在 Apple Watch 的 Xamarin 中实现它们。
ms.prod: xamarin
ms.assetid: 26697F68-AF7E-4A36-988F-85E2674A4DD1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 0252626cb58ff334e3cff2cc60e6544e4eba18c0
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435391"
---
# <a name="quick-interaction-techniques-for-watchos-3-in-xamarin"></a>Xamarin 中 watchOS 3 的快速交互技术

_本文介绍了 Apple 在 watchOS 3 中添加的快速交互技术，以及如何在 Apple Watch 的 Xamarin 中实现它们。_

提供快速用户交互对于创建引人注目的 Apple Watch 应用和复杂性至关重要。 WatchOS 3 中的新增技术，Apple 添加了对手势识别器的支持、对 Digital Crown 的访问权限以及新用户通知和导航技术的支持。 这同时增加了对 SceneKit 和 SpriteKit 的支持，使开发人员能够轻松地创建功能丰富的 glanceable 接口，这些接口既快速又有响应。

## <a name="what-are-quick-interactions"></a>什么是快速交互

对于用于创建适用于 iOS 或 macOS (的应用程序的开发人员来说，用户与应用程序进行交互的时间量以分钟或小时为单位) ，为 Apple Watch 设计成功的应用可能是一项挑战，需要不同的方法。

在 watchOS 中，用户通常希望抬起其手腕，快速与应用进行交互 (通常只需几秒钟的时间) ，然后删除手腕并继续操作。

下面是有关 Apple Watch 上的典型快速交互的几个示例：

- 启动计时器。
- 查看天气。
- 将项标记为 todo 列表。

若要实现这些目标，Apple Watch 上的应用必须：

- **Glanceable** -这意味着，用户应能一目了然地获取所需的信息。 
- 可**操作**-这意味着用户应该能够做出更明智的决策。
- **响应** -这意味着用户永远不会等待接收他们所需的信息或实现所需的操作。

### <a name="quick-interactions-length"></a>快速交互长度

由于 Apple Watch 应用的 glanceable 特性，Apple 建议快速交互的理想长度应为两秒钟或更少。 由于这两次限制，开发人员需要花费大量时间来设计和实现 Apple Watch 应用。 

## <a name="new-watchos-3-features-and-apis"></a>新 watchOS 3 功能和 Api

Apple 向 WatchKit 添加了一些新功能和 Api，以帮助开发人员向其 Apple Watch 应用程序添加快速交互：

- watchOS 3 提供对新类型的用户输入的访问权限，例如：
  - 手势识别器
  - Digital Crown 旋转 
- watchOS 3 提供了显示和更新信息的新方法，例如：
  - 增强的表导航
  - 新用户通知框架支持
  - SpriteKit 和 SceneKit 集成

通过实现这些新功能，开发人员可以确保其 watchOS 3 应用程序 Glanceable、可操作和响应能力。

### <a name="gesture-recognizer-support"></a>手势识别器支持

如果开发人员已在 iOS 中实现了手势识别器，则应该非常熟悉手势识别器在 watchOS 3 中的工作方式。 若要刷新，笔势识别器是将低级别触控事件分析为可识别的预定义手势的对象。

watchOS 3 将支持以下四个手势识别器：

- 离散手势类型：
  - 滑动手势 (`WKSwipeGestureRecognizer`) 。
  - 攻丝 (`WKTapGestureRecognizer`) 。
- 连续手势类型：
  - 平移手势 (`WKPanGestureRecognizer`) 。
  - 长按) 笔势 (`WKLongPressGestureRecognizer` 。

若要实现新的笔势识别器之一，只需将其拖动到 Visual Studio for Mac 中的 iOS 设计器的设计图面上，并配置其属性即可。

在代码中，响应识别器的操作以处理用户触发的手势。 同样，此操作的执行方式与在 iOS 中处理的方式相同。

#### <a name="discrete-gesture-states"></a>离散手势状态

对于离散的手势，在识别手势并且将状态 () 指定为时，将调用操作 `WKGestureRecognizerState` ：

[![离散手势状态](quick-interaction-techniques-images/quick01.png)](quick-interaction-techniques-images/quick01.png#lightbox)

所有离散手势都以状态开始 `Possible` ，并转换为 `Failed` 或 `Recognized` 状态。 使用离散手势时，开发人员通常不会直接处理状态。 相反，它们依赖于只识别手势时调用的操作。

#### <a name="continuous-gesture-states"></a>连续手势状态

连续手势与离散手势略有不同，其中操作会在识别手势时多次调用：

[![连续手势状态](quick-interaction-techniques-images/quick02.png)](quick-interaction-techniques-images/quick02.png#lightbox)

同样，连续的手势会开始处于 `Possible` 状态，但会在多个更新上进行。 在这里，开发人员需要考虑识别器的状态，并在阶段更新应用的 UI， `Changed` 直至手势最后 `Recognized` 或 `Canceled` 。

#### <a name="gesture-recognizer-usage-tips"></a>手势识别器使用提示

当使用 watchOS 3 中的笔势识别器时，Apple 建议以下内容：

- 将笔势识别器添加到组元素，而不是单个控件。 由于 Apple Watch 的物理屏幕大小较小，因此组元素趋向于更大、更易于访问的目标。 此外，笔势识别器可能与本机 UI 控件中已有的内置笔势冲突。
- 在手表应用的情节提要中设置依赖关系。
- 某些手势优先于其他笔势类型，如：
  - 滚动
  - Force Touch

### <a name="digital-crown-rotation"></a>Digital Crown 旋转

开发人员可通过在 watchOS 3 应用中实现 Digital Crown 支持，为用户提供更高的导航速度和精度交互。

自 watchOS 2 起，Apple Watch 应用程序可以使用 `WKInterfacePicker` 对象来访问 Digital Crown，方法是提供一个列表 `WKPickerItems` 和一个 (列表、堆积或图像序列) 的选择器样式。 然后，watchOS 允许用户使用 Digital Crown 从列表中选择一项。

使用时 `WKInterfacePicker` ，WatchKit 通过以下方式处理大部分工作：

- 绘制列表和单个接口元素。
- 处理 Digital Crown 事件。
- 在选择项时调用操作。

WatchOS 3 的新增功能现在，开发人员可以直接访问 Digital Crown 轮换事件，这些事件允许用户创建自己的 UI 元素来响应旋转值。

Digital Crown 访问权限由以下元素提供：

- `WKCrownSequencer` -提供对每秒旋转的访问。
- `WKCrownDelegate` -提供对循环增量事件的访问。

#### <a name="rotations-per-second"></a>每秒循环

使用基于物理学的动画时，从 Digital Crown 访问每秒的旋转很有用。 若要访问每秒的旋转，请使用 `CrownSequencer` 监视扩展的的属性 `WKInterfaceController` 。 例如：

```csharp
var rotationsPerSecond = CrownSequencer.RotationsPerSecond;
```

#### <a name="rotational-deltas"></a>旋转增量

使用 Digital Crown 中的循环增量来计算旋转的数目。 使用 `CrownDidRotate` 重写方法 `WKCrownDelegate` 来访问循环增量。 例如：

```csharp
using System;
using WatchKit;
using Foundation;

namespace MonkeyWatch.MonkeySeeExtension
{
  public class CrownDelegate : WKCrownDelegate
  {
    #region Computed Properties
    public double AccumulatedRotations { get; set;}
    #endregion

    #region Constructors
    public CrownDelegate ()
    {
    }
    #endregion

    #region Override Methods
    public override void CrownDidRotate (WKCrownSequencer crownSequencer, double rotationalDelta)
    {
      base.CrownDidRotate (crownSequencer, rotationalDelta);

      // Accumulate rotations
      AccumulatedRotations += rotationalDelta;
    }
    #endregion
  }
}
```

此处的应用程序维护一个积累的 (`AccumulatedRotations`) 来确定旋转的数目。 Digital Crown 的一次完全旋转等于的累计增量 `1.0` ，半旋转将为 `0.5` 。

Apple 将其留给开发人员来确定旋转计数如何与正在更新的 UI 元素上的更改敏感度相对应。

旋转增量的符号 (`+/-`) 指示用户打开 Digital Crown 的方向：

[![循环增量的符号表示用户打开 Digital Crown 的方向](quick-interaction-techniques-images/quick03.png)](quick-interaction-techniques-images/quick03.png#lightbox)

如果用户正在向上滚动，则 WatchKit 将返回正增量，如果向下滚动，则将返回负增量，而不管用户在哪个方向上穿出了手表。

#### <a name="digital-crown-focus"></a>Digital Crown 焦点

就像任何其他接口元素一样，Digital Crown 都具有焦点的概念。 基于用户与手表交互的方式，可以将此焦点移出 Digital Crown 到其他界面元素。 

例如，以下任何控件都可能会盗取 Digital Crown 的焦点：

- 选取器
- 滑块
- 滚动控制器

开发人员需要确定其自定义接口元素何时需要成为 Digital Crown 的焦点。 Apple 建议使用新的手势识别器来获取自定义 UI 元素中的焦点。

### <a name="vertical-paging"></a>垂直分页

用户在 watchOS 应用中导航表视图的标准方式是滚动到所需的数据段，点击特定行以显示详细视图，在完成查看详细信息后点击 "后退" 按钮，并对表中任何感兴趣的其他信息重复此过程：

[![在表和详细信息视图之间移动](quick-interaction-techniques-images/quick04.png)](quick-interaction-techniques-images/quick04.png#lightbox)

WatchOS 3 的新手，开发人员可以对其表视图控件启用垂直分页。 启用此功能后，用户可以滚动查找表视图行并点击该行，查看其详细信息。 不过，他们现在可以向上轻扫来选择表中的下一行或向下滚动，选择上一行 (或使用 Digital Crown) ，所有这些操作都不必首先返回到表视图：

[![在表和详细信息视图之间移动，并在其他行之间向上和向下轻扫](quick-interaction-techniques-images/quick05.png)](quick-interaction-techniques-images/quick05.png#lightbox)

若要启用此模式，请在 Xcode 中打开 watchOS 应用的情节提要进行编辑，选择表视图并选中 " **垂直详细信息分页** " 复选框：

[![选中 "垂直详细信息分页" 复选框](quick-interaction-techniques-images/quick06.png)](quick-interaction-techniques-images/quick06.png#lightbox)

确保表正在使用 Segue 来显示详细视图，并将更改保存到情节提要并返回到 Visual Studio for Mac 进行同步。

开发人员可以通过对表视图使用以下代码，以编程方式向特定行进行垂直分页：

```csharp
// Segue into Vertical Paging and select the first row
MenuTable.PerformSegue (0);
```

使用垂直分页时，开发人员需要注意到，WatchKit 会自动处理控制器的预加载，因此，一些控制器生命周期方法可以在 UI 实际可见之前调用。

### <a name="notification-enhancements"></a>通知增强功能

通知是用户通常在 watchOS 上遇到的快速交互的主要形式，并且自第一次 Apple Watch 和 watchOS 1 后即可使用。

典型的通知快速交互如下：

1. 收到新通知时，用户感觉到 Haptic 通知。
2. 它们会抬起手腕，查看通知的简短外观界面。
3. 如果他们继续保持手腕的手腕，watchOS 会自动转换为长时间的通知界面。

用户可以通过多种方式来响应通知：

- 对于明确定义的通知，用户将不执行任何操作，而只是关闭通知。
- 他们还可以点击通知以启动 watchOS 应用。
- 对于支持自定义操作的通知，用户可以选择一个自定义操作。 这些可能是：
  - **前景操作** -这些操作将启动应用以执行操作。
  - **后台操作** -始终路由到 watchOS 2 中的 iPhone，但可以路由到 watchOS 3 中的 watchApp。

WatchOS 3 的新增内容：

- 通知在所有平台中使用类似的 API (iOS、watchOS、tvOS 和 macOS) 。
- 可以在 Apple Watch 上计划本地通知。
- 如果计划在 Apple Watch 上，则后台通知将路由到应用程序的扩展。

#### <a name="notification-scheduling-and-delivery"></a>通知计划和传递

当发生以下情况时，用户的 iPhone 的通知将转发到 Apple Watch：

- IPhone 的屏幕处于关闭状态。
- Apple Watch 正在磨损，已解除锁定。

在 watchOS 3 中，可以在 Apple Watch 上计划本地通知，并且只能在手表上传递。 如果应用需要，开发人员需要计划相应的 iPhone 通知。

通过在通知的 Apple Watch 和 iPhone 版本上都包含相同的通知标识符，可防止在手表上显示重复的通知。 通知的 Apple Watch 版本将优先于 iPhone 版本。

由于 watchOS 3 使用同一个 `UINotification` API framework 作为 iOS 10，请参阅我们的 ios 10 [用户通知框架](~/ios/platform/user-notifications/index.md) 文档了解更多详细信息。

### <a name="using-spritekit-and-scenekit"></a>使用 SpriteKit 和 SceneKit

新到 watchOS 3，开发人员现在可以在其应用的用户界面设计中同时使用 SpritKit 和 SceneKit 对象，以提供2D 和3D 图形。

添加了两个新接口类以支持此功能：

- `WKInterfaceSKScene` -用于 SpriteKit 2D 图形。
- `WKInterfaceSCNScene` -用于使用 SceneKit 3D 图形。

若要使用这些对象，只需将其拖到 Xcode 的 Interface Builder 中的 watch 应用情节提要内的设计图面上，然后使用 " **属性" 检查器** 对其进行配置。

从此时起，使用 SpriteKit 或 SceneKit 场景的工作方式与在 iOS 应用中的工作方式相同。 Watch 应用程序将 `WKInterfaceSKScene` 通过调用其中一种方法来显示 `Present` 。 对于 SceneKit，只需设置 `Scene` 对象的属性即可 `WKInterfaceSCNScene` 。

## <a name="actionable-complications"></a>可操作的复杂性

在 watchOS 2 中，Apple 引入了第三方应用的复杂性。 在 watchOS 3 中，Apple 已扩展了开发人员可以在 WatchKit 中包括的功能。 

此外，更多的内置观看面现在还可以包括复杂的和现有的手表面，因为现在还能获得更多的复杂性。

新增功能使用户能够快速向左或向右轻扫，以便在其 Apple Watch 上进行的所有手表面之间过渡。 使用 Apple Watch 配套的 iPhone 应用上的新库，用户可以添加和自定义新的手表面及其可包含的任何复杂。

由于这些新功能，Apple 建议 Apple Watch 上的每个应用程序还应包含至少一个复杂的问题，因此，所有本机 Apple Watch 应用程序现在都有了复杂性。

复杂情况为应用程序提供以下功能：

- 它们非常 glanceable，因为它们始终出现在手表面上。
- WatchOS 经常更新复杂性。 任何包括用户当前显示的手表面上的复杂的应用程序，至少每小时更新一次。
- 在用户当前显示的监视面上出现的任何应用都将保留在内存中，这使得应用程序可以快速启动并提高应用程序的响应速度。
- 利用复杂性，用户可以轻松地在 watchOS 应用程序中启动特定功能。

## <a name="glanceable-notification"></a>Glanceable 通知

Apple Watch 上的通知提供了一种很好的可自定义方法，可快速向用户通知事件或新信息，例如传入消息或实现健身应用的目标。

通过使用通知，可以快速向用户提供有价值的信息。 在许多情况下，设计良好的通知可以消除用户实际启动应用的必要性。

新到 watchOS 3 的所有通知现在都支持：

- SpriteKit
- SceneKit
- 内联视频

## <a name="enhanced-ui-with-spritekit-and-scenekit"></a>增强的 UI 和 SpriteKit 和 SceneKit

通常，当提到 SpriteKit 和 SceneKit 时，开发人员可能会想到游戏 UI。 但是，SpriteKit 和 SceneKit 可用于创建非游戏 Ui，其中包括未在 WatchKit 中单独实现的自定义布局、内容和动画。

例如，来自照片共享应用的用户通知可以使用 SpriteKit 来提供丰富的用户体验，方法是将已发布图片的用户连同实际图像和其他丰富用户体验的自定义信息一起提供。

此外，SpriteKit 和 SceneKit 可以与应用用户界面设计中的标准 WatchKit UI 元素混合。

## <a name="simple-navigation"></a>简单导航

watchOS 3 提供了多种方法，开发人员可以在其 watchOS 应用中简化导航，例如新的 [垂直分页](#vertical-paging)、 [手势识别器支持](#gesture-recognizer-support) 和上面提供的 [Digital Crown 旋转](#digital-crown-rotation) 功能。

Digital Crown 是 Apple Watch 独有的，可以通过多种不同的方式使用来简化导航。 例如，计时器应用程序可以使用 Digital Crown 来清理可用的计时器长度。

自定义手势可为用户提供新的独特方法，使用户能够与手表应用交互，还可用于简化应用导航。

Apple 建议查找结合了 watchOS 3 中添加的所有新快速交互功能的方法，以提供丰富、易于使用的 watchOS 应用程序接口。

## <a name="finishing-the-quick-interaction"></a>完成快速交互

设计良好的快速交互体验会使用户满怀信心地删除手腕 (，并在应用程序完成当前交互后，将其与应用) 断开。

这具体会成为一个问题，即 watch 应用正在执行任何类型的网络连接或与其配套的 iPhone 应用共享信息。 在事务发生时，这通常会导致等待指示器，这在快速交互过程中是不可取的。 执行以下示例：

[![执行网络连接并与 iPhone 应用相关的信息的监视应用示意图](quick-interaction-techniques-images/quick07.png)](quick-interaction-techniques-images/quick07.png#lightbox)

1. 用户选择要在手表上购买的项目。
2. 他们点击 "购买" 按钮。
3. 应用程序启动网络事务，并显示加载指示器。
4. 稍后，事务完成，应用显示 "购买聘约确认"。
5. 用户通过该应用删除手腕和 disengages。

在用户点击 "购买" 按钮直到交易完成后，他们就会在加载指示器中看到手腕。 为了解决这种情况，Apple 建议开发人员向用户提供即时反馈，而不是显示加载指示器。 

使用 Apple 的建议模型，再次查看相同的快速交互：

[![苹果建议模型图](quick-interaction-techniques-images/quick08.png)](quick-interaction-techniques-images/quick08.png#lightbox)

1. 用户选择要在手表上购买的项目。
2. 他们点击 "购买" 按钮。
3. 应用启动网络事务，并显示一条消息，指出购买已成功启动。
4. 用户通过该应用删除手腕和 disengages。
5. 当事务在稍后成功完成一段时间后，应用程序将显示一个本地通知，告知用户已成功购买。

这次，用户点击 "购买" 按钮后，就会显示一条消息，指出购买已开始，因此他们可以放心地删除手腕并结束快速交互。 稍后，它们会在用户通知中通知事务的成功或失败。 这样，用户只会在流程的 "活动" 阶段与应用进行交互。

对于正在进行网络处理的应用程序，他们可以使用背景 `NSURLSession` 来处理使用下载任务的网络通信。 这将允许在后台唤醒应用程序来处理下载的信息。 对于需要后台处理的应用程序，请使用后台任务断言来处理所需的处理。

## <a name="quick-interaction-design-tips"></a>快速交互设计提示

由于快速交互所需的长度是两秒钟或更少，开发人员应将精力集中在设计过程的最开始部分。 使用以上所示的方法，查找可简化这些交互的区域 () 并使用 watchOS 3 的新增功能，使应用程序快速和响应能力。

Apple 建议以下内容：

- 通过引入应用程序的最常用功能，重点关注快速交互。
- 使用复杂的和用户通知来呈现常见的特性和功能。
- 通过 SceneKit 和 SpriteKit 创建丰富的 glanceable 用户界面。
- 尽可能简化应用中的导航。
- 切勿使用户等待，使其删除手腕并尽快断开应用。

## <a name="summary"></a>总结

本文介绍了 Apple 在 watchOS 3 中添加的快速交互技术，以及如何在 Apple Watch 的 Xamarin 中实现它们。

## <a name="related-links"></a>相关链接

- [watchOS 示例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2bwatchOS)