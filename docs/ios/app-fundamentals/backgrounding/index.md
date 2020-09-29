---
title: Xamarin 中的后台处理
description: 后台处理或后台处理是让应用程序在后台中执行任务的过程。 本指南将介绍 iOS 中的后台处理。
ms.prod: xamarin
ms.assetid: F377440C-C5D9-4267-85D8-2C816E3A0300
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/05/2018
ms.openlocfilehash: 3d50bf91502e7a3b7331348a61af50a5c789f838
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91432428"
---
# <a name="backgrounding-in-xamarinios"></a>Xamarin 中的后台处理

_后台处理或后台处理是让应用程序在后台中执行任务的过程。本指南将介绍 iOS 中的后台处理。_

在移动应用程序中，后台处理在本质上与桌面上多任务的传统概念不同。 台式机有多种可用于应用程序的资源，包括屏幕房地产、电源和内存。 应用程序能够并行运行，并且保持性能和可用性。 在移动设备上，资源的限制更多。 很难在一个小屏幕上显示多个应用程序，并且以全速运行多个应用程序会耗尽电池。 后台处理是在为应用程序提供资源以运行他们需要执行的后台任务和保持前置应用程序和设备的响应性之间的一个恒定的折衷。 IOS 和 Android 都具有后台处理的预配，但以非常不同的方式对其进行处理。

在 iOS 中，后台处理被识别为应用程序状态，应用将根据应用程序和用户的行为移入和移出后台状态。 iOS 还提供了多个选项用于使应用程序在后台运行，其中包括要求 OS 时间完成重要任务、作为一种已知的后台必需应用程序运行，以及按指定的时间间隔刷新应用程序的内容。

在本指南和随附的演练中，我们将学习如何在后台执行应用程序任务。 我们将介绍重要的概念和最佳做法，并逐步介绍如何创建在后台接收位置更新的实际应用。

## <a name="contents"></a>目录

1. [iOS 中的后台处理简介](~/ios/app-fundamentals/backgrounding/introduction-to-backgrounding-in-ios.md)
1. [应用程序生命周期演示](~/ios/app-fundamentals/backgrounding/application-lifecycle-demo.md)
1. [iOS 后台处理技术](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/index.md)
1. [演练：iOS 中的后台处理](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/index.md)
1. [iOS 后台处理指南](~/ios/app-fundamentals/backgrounding/ios-backgrounding-guidance.md)

## <a name="summary"></a>总结

在本指南中，我们介绍了在 iOS 中进行后台处理的不同方式。 我们介绍了 iOS 应用程序状态并检查了 iOS 应用程序生命周期中的角色后台处理重头戏。 此外，我们还了解了如何在 iOS 中注册各个任务或整个应用程序以后台运行。 最后，通过构建在后台执行更新的应用程序，加强对 iOS 上后台处理的理解。

## <a name="related-links"></a>相关链接

- [Android 上的后台处理](~/android/app-fundamentals/services/index.md)
- [LifecycleDemo (示例) ](/samples/xamarin/ios-samples/lifecycledemo)
- [位置 (示例) ](/samples/xamarin/ios-samples/location)
- [简单的后台传输 (示例) ](/samples/xamarin/ios-samples/simplebackgroundtransfer)
- [iOS 后台执行](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/BackgroundExecution/BackgroundExecution.html)