---
title: watchOS 3 简介
description: 本文介绍适用于 Xamarin 开发人员的 watchOS 3 中提供的所有新的和修改的 Api 和功能。
ms.prod: xamarin
ms.assetid: B8ABE1E1-8688-4262-BE66-A16813C2D671
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/07/2017
ms.openlocfilehash: ec43c98b891ee1541be9b1ac2563e280730628e3
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430497"
---
# <a name="introduction-to-watchos-3"></a>watchOS 3 简介

_本文介绍适用于 Xamarin 开发人员的 watchOS 3 中提供的所有新的和修改的 Api 和功能。_

本文档包含以下主题：

- [WatchOS 3 中的新增功能](#Whats-New-in-watchOS-3)
  - [Apple Pay 增强功能](#Apple-Pay-Enhancements) 增加了 Apple Watch 上应用内付款的支持。
  - [后台任务](#Background-Tasks) 使应用能够在后台更新其信息，使其在用户需要时随时可用。
  - 为应用提供新功能的 watchOS 3 进行了[复杂的增强](#Complications-Enhancements)。
  - [新提供的框架](#Newly-Available-Frameworks) 公开了 watchOS 应用。
  - [主动建议](#Proactive-Suggestions) 使应用能够主动向用户显示信息。
  - 已对 watchOS 3 进行了几项 [安全和隐私增强](#Security-and-Privacy-Enhancements) 。
  - [快照和停靠](#Snapshots-and-Dock) 使用户可以快速访问应用 watchOS 应用。
  - [用户通知](#User-Notifications) 向用户提供本地和远程通知。
  - WatchOS 3 中已进行了多个 [监视连接框架增强](#Watch-Connectivity-Framework-Enhancements) 。
  - 已在 watchOS 3 中进行了多个 [WatchKit 框架增强](#WatchKit-Framework-Enhancements) 。
  - [健身应用增强功能](#Workout-App-Enhancements) 为与健身相关的 Apple Watch 应用提供了新的功能。
- 在整个 watchOS 3 中进行了[其他框架更改](#Additional-Framework-Changes)。
- WatchOS 3 中[弃用的 api](#Deprecated-APIs) 。

<a name="Whats-New-in-watchOS-3"></a>

## <a name="whats-new-in-watchos-3"></a>WatchOS 3 中的新增功能

Apple 在 watchOS 3 中添加了几个新的 Api 和服务，并提供了对现有功能的许多增强功能，其中包括：

<a name="Apple-Pay-Enhancements"></a>

## <a name="apple-pay-enhancements"></a>Apple Pay 增强功能

在 watchOS 3 中，已扩展 PassKit 框架，以允许在 Apple Watch 上运行的应用的物理货物和服务) 支持 (的应用内付款。

使用新的 [PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller) 和 [PKPaymentAuthorizationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate) 类提供并响应用户可在其中授权付款请求的接口。

若要了解详细信息，请参阅我们的 [Apple Pay 增强](~/ios/watchos/platform/apple-pay.md) 指南。

<a name="Background-Tasks"></a>

## <a name="background-tasks"></a>后台任务

watchOS 3 引入了多个后台任务，应用可以使用这些任务来更新其信息，确保在打开该应用程序之前，它具有用户需要的内容。

可用的新后台任务如下：

- **后台应用刷新** - [WKApplicationRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkapplicationrefreshbackgroundtask) 任务允许应用在后台更新其状态。 通常，这会包括其他任务，例如，使用 [NSUrlSession](https://developer.apple.com/reference/foundation/nsurlsession)从 internet 下载新内容。
- **后台快照刷新** - [WKSnapshotRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask) 任务允许应用程序在系统拍摄用于填充停靠的快照之前更新其内容和 UI。
- **后台监视连接** -从配对的 iPhone 接收背景数据时，会为应用启动 [WKWatchConnectivityRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkwatchconnectivityrefreshbackgroundtask) 任务。
- **后台 URL 会话** -在后台传输需要授权或 (成功或) 时，为应用启动 [WKURLSessionRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkurlsessionrefreshbackgroundtask) 任务。

若要了解详细信息，请参阅我们的 [背景任务](~/ios/watchos/platform/background-tasks.md) 指南。

<a name="Complications-Enhancements"></a>

## <a name="complications-enhancements"></a>复杂增强

复杂的是提供有用信息的小视觉元素。 根据所选的监视面，用户可以自定义一个或多个复杂的手表面。

watchOS 3 为应用提供创建一个或多个 "监视" 应用的相关功能，使用户可以从手表表面一目了然地访问其信息。

此外，复杂性具有以下优势：

- 用户可以通过直接在手表中点击难点来启动应用。
- 在手表面上应用程序的一个复杂问题导致系统使应用程序处于随时可用状态，在该状态下，它会尝试在后台启动应用程序，将其保存在内存中，并为其提供额外的更新时间。
- 最复杂的一天保证至少50推送更新。
- 当应用包含复杂性时，它将在 Apple Watch 人脸库中提供。

在 watchOS 3 中，ClockKit 框架现在包含几个新的模板，用于实现额外的大复杂，例如 [CLKComplicationTemplateExtraLargeColumnsText](https://developer.apple.com/reference/clockkit/clkcomplicationtemplateextralargecolumnstext) 和 [CLKComplicationTemplateExtraLargeRingImage](https://developer.apple.com/reference/clockkit/clkcomplicationtemplateextralargeringimage)。 此外，若要创建可本地化的文本，请使用 [CLKTextProvider](https://developer.apple.com/reference/clockkit/clktextprovider) 类的新方法。

若要了解详细信息，请参阅 [watchOS 3 指南的快速交互技术](~/ios/watchos/platform/quick-interaction-techniques.md) 。

<a name="Newly-Available-Frameworks"></a>

## <a name="newly-available-frameworks"></a>新可用框架

watchOS 3 包括之前无法使用的多个现有 Apple framework，如：

- **SceneKit** -使用 SceneKit 将3d 模型包含在手表应用的 UI 中，其中包括其他平台上的大部分可用功能，如光照、阴影、动画、物理学和粒子系统。 不支持3D 空间音频、自定义金属或 OpenGL 着色器、核心图像筛选器和基于物理的材料。
- **SpriteKit** -使用 SpriteKit 在应用程序监视应用的 UI 中呈现和动画动画活动，包括其他平台上提供的大多数功能，如操作、物理学、照明和粒子系统。 不支持3D 空间音频、视频播放和核心图像筛选器。
- **AVFoundation** -用于管理和播放音频。
- **CloudKit** -用于在监视应用和 iCloud 容器之间移动数据。
- **核心音频** -管理用于表示音频流、复杂缓冲区和时间值的数据类型。
- **Gamekit** -创建社交游戏。

<a name="Proactive-Suggestions"></a>

## <a name="proactive-suggestions"></a>主动建议

watchOS 3 允许应用在给定上下文中主动向用户显示信息。 为支持此功能， [NSUserActivity](https://developer.apple.com/reference/foundation/nsuseractivity) 现在包含 `MapItem` 属性，该属性使应用程序能够提供位置信息供其他应用以后使用。

若要了解详细信息，请参阅 [前瞻性建议指南简介](~/ios/watchos/platform/proactive-suggestions.md) 。

<a name="Security-and-Privacy-Enhancements"></a>

## <a name="security-and-privacy-enhancements"></a>安全和隐私增强

Apple 在 watchOS 3 中对安全和隐私进行了多项改进，有助于开发人员提高应用程序的安全性并确保最终用户的隐私。

因此，在 watchOS 3 (或) 更高版本上运行的应用程序必须通过在其文件中输入一个或多个特定于隐私的密钥来向用户提供一个或多个特定于隐私的密钥 `Info.plist` ，向用户说明应用希望获取访问权限的原因。

由于 watchOS 3 与 iOS 10 共享这些更改，请参阅我们的 iOS 10 [安全和隐私增强](~/ios/app-fundamentals/security-privacy.md) 指南以获取详细信息。

<a name="Snapshots-and-Dock"></a>

## <a name="snapshots-and-dock"></a>快照和停靠

在 watchOS 3 中，Apple 添加了插接，用户可在其中固定喜爱的应用并对其进行快速访问。 当用户按下 Apple Watch 上的侧按钮时，将显示固定应用程序快照的库。 用户可以向左或向右轻扫以查找所需的应用程序，然后点击应用程序以启动它，并将快照替换为正在运行的应用程序的接口。

系统会定期拍摄应用 UI 的快照，并使用这些快照来填充文档。watchOS 使应用有机会在拍摄此快照之前更新其内容和 UI。

有关详细信息，请参阅我们的 [背景任务](~/ios/watchos/platform/background-tasks.md) 指南和 Apple 的 [WKSnapshotRefreshBackgroundTask 参考](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask) 。

<a name="User-Notifications"></a>

## <a name="user-notifications"></a>用户通知

WatchOS 3 中引入的用户通知框架支持向 Apple Watch 传递本地和远程通知。 此框架用于根据特定条件（如日期或地点的时间，以及接收和处理通知）来计划通知。

若要了解详细信息，请参阅 [watchOS 3 指南的快速交互技术](~/ios/watchos/platform/quick-interaction-techniques.md) 。

<a name="Watch-Connectivity-Framework-Enhancements"></a>

## <a name="watch-connectivity-framework-enhancements"></a>观看连接框架增强功能

`HasContentPending` [WCSession](https://developer.apple.com/reference/watchconnectivity/wcsession)类的新属性指示会话已在后台接收需要处理的数据。 `RemainingComplicationUserInfoTransfers`属性可返回 iOS 应用程序更新其 watchOS 的剩余时间。

若要了解详细信息，请参阅我们的 [背景任务](~/ios/watchos/platform/background-tasks.md) 指南。

<a name="WatchKit-Framework-Enhancements"></a>

## <a name="watchkit-framework-enhancements"></a>WatchKit 框架增强功能

watchOS 3 包含对 WatchKit 框架的几项增强功能，包括以下内容：

- 应用程序可以使用新的 [WKCrownSequencer](https://developer.apple.com/reference/watchkit/wkcrownsequencer) 类获取 Digital Crown 的状态，并在用户使用 [WKCrownDelegate](https://developer.apple.com/reference/watchkit/wkcrowndelegate) 类旋转王冠时接收更新。
- [WKExtension](https://developer.apple.com/reference/watchkit/wkextension)类现在包含 `ApplicationState` 方法和[WKApplicationState](https://developer.apple.com/reference/watchkit/wkapplicationstate)常量，应用可使用该常量来跟踪应用程序的运行时状态。 `WKExtension` 还提供了两个可用于计划后台任务的新方法。
- [WKExtensionDelegate](https://developer.apple.com/reference/watchkit/wkextensiondelegate)现在包括新的 `ApplicationWillEnterForeground` `ApplicationDidEnterBackground` 和方法， `HandleBackgroundTasks` 用于监视应用状态中的更改并处理后台任务更新。
- 添加了一个新的 [WKGestureRecognizer](https://developer.apple.com/reference/watchkit/wkgesturerecognizer) 类，以便为监视应用提供以下类型的手势识别： [WKLongPressGestureRecognizer](https://developer.apple.com/reference/watchkit/wklongpressgesturerecognizer)、 [WKPanGestureRecognizer](https://developer.apple.com/reference/watchkit/wkpangesturerecognizer)、 [WKSwipeGestureRecognizer](https://developer.apple.com/reference/watchkit/wkswipegesturerecognizer) 和 [WKTapGestureRecognizer](https://developer.apple.com/reference/watchkit/wktapgesturerecognizer)。
- 新的 [WKinterfaceHMCamera](https://developer.apple.com/reference/watchkit/wkinterfacehmcamera) 类为所有 HomeKit 连接的 IP 照相机提供一个接口。
- 新的 [WKInterfaceInlineMovie](https://developer.apple.com/reference/watchkit/wkinterfaceinlinemovie) 类允许应用显示电影 "海报"，它在用户点击它时由正在运行的电影替换。
- 新的 [WKInterfacePaymentButton](https://developer.apple.com/reference/watchkit/wkinterfacepaymentbutton) 类允许应用在其用户界面中显示一个 Apple Pay 按钮，该按钮将在点击时启动付款请求。
- 新的 [WKInterfaceSCNScene](https://developer.apple.com/reference/watchkit/wkinterfacescnscene) 类提供了一个接口，用于在 Apple Watch 上显示一个 SceneKit 场景。
- 新的 [WKInterfaceSKScene](https://developer.apple.com/reference/watchkit/wkinterfaceskscene) 类提供了一个接口，用于在 Apple Watch 上显示一个 SpriteKit 场景。

若要了解详细信息，请参阅 [watchOS 3 指南的快速交互技术](~/ios/watchos/platform/quick-interaction-techniques.md) 。

<a name="Workout-App-Enhancements"></a>

## <a name="workout-app-enhancements"></a>测验应用增强功能

新到 watchOS 3，健身相关应用可以在 Apple Watch 的后台运行。 若要启用此功能 (并获取对 HealthKit 数据的访问权限) ，应用必须 `WKBackgroundModes` 在文件中包含值为的键 `Info.plist` `workout-processing` 。

此外，开发人员现在可以从配对的 iPhone 上的 iOS 应用程序版本启动 watchOS 健身应用。

若要了解详细信息，请参阅 [健身应用增强](~/ios/watchos/platform/workout-apps.md) 指南。

<a name="Additional-Framework-Changes"></a>

## <a name="additional-framework-changes"></a>其他框架更改

除了上面列出的主要框架更改和添加以外，Apple 还在 watchOS 3 中进行了许多其他的次要框架更改。

若要了解详细信息，请参阅我们的 [其他框架更改](~/ios/watchos/platform/introduction-to-watchos3/additional-framework-changes.md) 指南。

<a name="Deprecated-APIs"></a>

## <a name="deprecated-apis"></a>弃用的 API

WatchOS 3 中已弃用以下 Api：

- `UILocalNotification`UIKit 的类已弃用，应该替换为用户通知框架。

有关弃用功能和更改的完整列表，请参阅 Apple 的 [watchOS 2.2 到 watchOS 3.0 API 差异](https://developer.apple.com/library/prerelease/content/releasenotes/General/watchOS30APIDiffs/index.html) 文档。

## <a name="related-links"></a>相关链接

- [watchOS 示例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2bwatchOS)
- [WatchOS 3 中的新增功能](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)