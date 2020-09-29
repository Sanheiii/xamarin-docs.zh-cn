---
title: watchOS 简介
description: 本文档概述了 watchOS，描述了应用程序生命周期、用户界面类型、屏幕大小、限制等。
ms.prod: xamarin
ms.assetid: 99c316d6-6707-40f6-bec9-801d05888759
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/13/2016
ms.openlocfilehash: 9cbf6e58586a223de51f1294aa1b4235b3b631f8
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436286"
---
# <a name="introduction-to-watchos"></a>watchOS 简介

> [!NOTE]
> 有关最新功能的概述，请查看 [watchOS 3 简介](~/ios/watchos/platform/introduction-to-watchos3/index.md) 。

## <a name="about-watchos"></a>关于 watchOS

WatchOS 应用解决方案包含3个项目：

- **监视扩展** -包含 Watch 应用代码的项目。
- **监视应用** -包含用户界面情节提要和资源。
- **IOS 父应用** –此应用是普通的 iPhone 应用。 监视应用和扩展捆绑到 iPhone 应用中，以便交付到用户的手表。

在 watchOS 1 应用中，扩展中的代码在 iPhone 上运行– Apple Watch 实际上是外部显示。 watchOS 2 和3应用完全在 Apple Watch 上运行。 下图显示了这种差异：

[![此图显示了 watchOS 1 和 watchOS 2 (和更高) 之间的差异](intro-to-watchos-images/arch-sml.png)](intro-to-watchos-images/arch.png#lightbox)

无论 watchOS 的版本是哪个版本，Visual Studio for Mac 的 Solution Pad 完整的解决方案将如下所示：

[![Solution Pad](intro-to-watchos-images/projectstructure-sml.png)](intro-to-watchos-images/projectstructure.png#lightbox)

WatchOS 解决方案中的 *父应用* 是一种常规的 iOS 应用。 这是解决方案中可在 **手机上**看到的唯一项目。 此应用程序的用例包括教程、管理屏幕和中间层筛选 cacheing 等。但是，用户在没有打开父应用程序 **的情况下** 也可以安装和运行 watch 应用程序/扩展，因此，如果你需要父应用程序运行一次或管理，则需要对你的监视应用程序/扩展进行编程，告诉用户。

虽然父应用程序提供了 watch 应用和扩展，但它们在不同的沙箱中运行。

在 watchOS 1 上，他们可以通过共享应用组共享数据，也可以通过静态函数共享数据 `WKInterfaceController.OpenParentApplication` ，这将触发 `UIApplicationDelegate.HandleWatchKitExtensionRequest` 父应用的 (中的方法， `AppDelegate` 请参阅使用 [父应用](~/ios/watchos/app-fundamentals/parent-app.md)) 。

在 watchOS 2 或更高版本上，手表连接框架用于通过使用类与父应用进行通信 `WCSession` 。

## <a name="application-lifecycle"></a>应用程序生命周期

在监视扩展中，为 `WKInterfaceController` 每个情节提要场景创建类的子类。

这些 `WKInterfaceController` 类类似于 `UIViewController` iOS 编程中的对象，但对视图没有相同的访问级别。
例如，不能将控件动态添加到 UI 或重构 UI。
但是，您可以隐藏和显示控件，并在某些控件中更改其大小、透明度和外观选项。

对象的生命周期 `WKInterfaceController` 涉及以下调用：

- [唤醒](xref:WatchKit.WKInterfaceController.Awake*) ：应在此方法中执行大多数初始化。
- [WillActivate](xref:WatchKit.WKInterfaceController.WillActivate) ：在向用户显示 Watch 应用之前立即调用。 使用此方法可以执行最后一次初始化、启动动画等。
- 此时，将显示 "监视" 应用，并且扩展会开始响应用户输入，并根据应用程序逻辑更新监视应用的显示。
- [DidDeactivate](xref:WatchKit.WKInterfaceController.DidDeactivate) 用户消除了 Watch 应用后，将调用此方法。 此方法返回后，用户界面控件在下一次调用之前不能修改 `WillActivate` 。 如果与 iPhone 的连接断开，还会调用此方法。
- 停用扩展后，程序将无法访问该扩展。 **将不**会调用挂起的异步函数。 手表工具包扩展不能使用后台处理模式。 如果用户重新激活了该程序，但该应用程序尚未被操作系统终止，则第一个调用的方法将是 `WillActivate` 。

![应用程序生命周期概述](intro-to-watchos-images/wkinterfacecontrollerlifecycle.png)

## <a name="types-of-user-interface"></a>用户界面的类型

有三种类型的用户可与手表应用交互。
所有都是使用的自定义子类进行编程的 `WKInterfaceController` ，因此，之前讨论的生命周期序列应用通用 (通知使用的子类进行编程 `WKUserNotificationController` ，这本身就是) 的子类 `WKInterfaceController` ：

### <a name="normal-interaction"></a>正常交互

大多数监视应用/扩展交互将与 `WKInterfaceController` 你编写的的子类结合在手表应用的 **界面**中的场景中。 [安装](~/ios/watchos/get-started/installation.md)和[入门](~/ios/watchos/get-started/index.md)文章中对此进行了详细介绍。
下图显示了 " [观看工具包目录](/samples/xamarin/ios-samples/watchos-watchkitcatalog) " 示例的情节提要的一部分。 对于此处显示的每个场景，在 `WKInterfaceController` `LabelDetailController` `ButtonDetailController` `SwitchDetailController` 扩展项目中都有相应的自定义 (、、等 ) 。

![正常交互示例](intro-to-watchos-images/scenes.png)

### <a name="notifications"></a>通知

[通知](~/ios/watchos/platform/notifications.md) 是 Apple Watch 的主要用例。 支持本地和远程通知。 与通知的交互发生在两个阶段中，称为短期和长期。

简短外观显示，并显示 "监视应用" 图标、名称和标题 (与 `WKInterfaceController.SetTitle`) 指定。

长时间的外观将系统提供的 **窗扇** 区域和 "解除" 按钮与自定义的基于情节提要的内容合并在一起。

`WKUserNotificationInterfaceController``WKInterfaceController`与方法和扩展 `DidReceiveLocalNotification` `DidReceiveRemoteNotification` 。
重写这些方法以响应通知事件。

有关通知 UI 设计的详细信息，请参阅 [Apple Watch 人体学接口准则](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/Notifications.html#//apple_ref/doc/uid/TP40014992-CH20-SW1)

![示例通知](intro-to-watchos-images/notifications.png)

## <a name="screen-sizes"></a>屏幕大小

Apple Watch 有两种表面大小：38mm 和42mm，两者都具有5:4 显示比例和 Retina 显示。 可用大小为：

- 38mm： 136 x 170 逻辑像素 (272 x 340 物理像素) 
- 42mm： 156 x 195 逻辑像素 (312 x 390 物理像素) 。

用于 `WKInterfaceDevice.ScreenBounds` 确定监视应用正在运行的显示。

通常，通过更受限制的38mm 显示来开发文本和布局设计会更容易，然后增加。
如果从较大的环境开始，缩小可能会导致不太好的重叠或文本截断。

阅读更多有关使用 [屏幕大小的](~/ios/watchos/app-fundamentals/screen-sizes.md)信息。

## <a name="limitations-of-watchos"></a>WatchOS 的限制

开发 watchOS 应用时，需要注意一些限制：

- Apple Watch 设备具有有限的存储空间，请在下载大文件之前注意可用空间 (例如。 ) 音频或电影文件。

- 许多 watchOS [控件](~/ios/watchos/user-interface/index.md) 在 UIKit 中都有物，但它们 )  (而不是不同的类， `WKInterfaceButton` `UIButton` `WKInterfaceSwitch` `UISwitch` 等等。 此外，watchOS 还提供了一些控件，如 `WKInterfaceDate` 用于显示 UIKit 不具有的日期和时间) 的 (。

  - 不能将通知只路由到手表，或仅限 iPhone (Apple) 未公布用户通过路由的控件类型。

一些其他已知限制/常见问题：

- Apple 将不允许第三方自定义监视面。

- 允许监视控制连接的电话上的 iTunes 的 Api 是专用的。

## <a name="further-reading"></a>深入阅读

查看 Apple 的文档：

- [针对手表工具包进行开发](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/index.html#//apple_ref/doc/uid/TP40014969-CH8-SW1)

- [观看工具包编程指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/DesigningaWatchKitApp.html)

- [Apple Watch 人体学接口准则](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/index.html#//apple_ref/doc/uid/TP40014992-CH3-SW1)

## <a name="related-links"></a>相关链接

- [watchOS 3 目录 (示例) ](/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [watchOS 1 Catalog (示例) ](/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [设置和安装](~/ios/watchos/get-started/installation.md)
- [第一次观看应用视频](https://blog.xamarin.com/your-first-watch-kit-app/)
- [Apple 开发观看套件指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/index.html)
- [Apple 的 WatchKit 提示](https://developer.apple.com/watchkit/tips/)
- [watchOS 3 简介](~/ios/watchos/platform/introduction-to-watchos3/index.md)