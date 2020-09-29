---
title: Xamarin 中的触摸和手势
description: 如今的许多设备上的触摸屏允许用户以自然直观的方式快速、高效地与设备交互。 这种交互不只局限于简单的触摸式检测，还可以使用手势。 例如，通过使用两个手指（用户可以放大或缩小）收缩屏幕的一部分，缩小到缩放手势的情况非常常见。本指南检查 Android 中的触摸和手势。
ms.prod: xamarin
ms.assetid: 61874769-978A-4562-9B2A-7FFD45F58B38
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: b4740b91b3d59a3c50696af06eec4ff82bbf9b1e
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91455191"
---
# <a name="touch-and-gestures-in-xamarinandroid"></a>Xamarin 中的触摸和手势

_如今的许多设备上的触摸屏允许用户以自然直观的方式快速、高效地与设备交互。这种交互不只局限于简单的触摸式检测，还可以使用手势。例如，通过使用两个手指（用户可以放大或缩小）收缩屏幕的一部分，缩小到缩放手势的情况非常常见。本指南检查 Android 中的触摸和手势。_

## <a name="touch-overview"></a>触摸概述

iOS 和 Android 的处理方式类似于处理触控的方式。 两者都可以在屏幕上和复杂的手势上支持多点触控。 本指南介绍了概念中的一些相似性，以及在这两个平台上实现触控和手势的 particularities。

Android 使用 `MotionEvent` 对象封装触控数据，并使用视图对象上的方法侦听接触。

除了捕获触控数据之外，iOS 和 Android 都提供了一种方法来解释对手势的接触模式。 这些手势识别器可用于解释特定于应用程序的命令，例如，旋转图像或打开页面。 Android 提供少量支持的手势，还提供了一些资源，使你可以轻松地添加复杂的自定义手势。

无论你使用的是 Android 还是 iOS，都可以选择触摸和手势识别器。 本指南建议在一般情况下，应将首选项指定给手势识别器。 手势识别器作为离散类实现，这可提供更大的问题分离和更好的封装。 这样就可以轻松地在不同视图之间共享逻辑，从而最大程度地减少编写的代码量。

本指南针对每个操作系统遵循类似的格式：首先，会引入和解释平台的触控 Api，因为它们是构建触摸交互的基础。 接下来，我们通过探索一些常见手势，并完成为应用程序创建自定义手势，深入探讨了手势识别器的世界。 最后，你将了解如何使用底层触摸跟踪来跟踪单个手指，以创建手指画图程序。

## <a name="sections"></a>部分

- [Android 中的触控](~/android/app-fundamentals/touch/android-touch-walkthrough.md)
- [演练：在 Android 中使用触控](~/android/app-fundamentals/touch/android-touch-walkthrough.md)
- [多点触控跟踪](touch-tracking.md)

## <a name="summary"></a>总结

在本指南中，我们检查了 Android 中的触控。 对于这两种操作系统，我们学习了如何启用触控以及如何响应触控事件。 接下来，我们了解了一些笔势和一些手势识别器，Android 和 iOS 都提供这些方法来处理一些更常见的方案。 我们检查了如何在应用程序中创建自定义笔势并实现它们。 本演练演示了操作中每个操作系统的概念和 Api，还介绍了如何跟踪单个手指。

## <a name="related-links"></a>相关链接

- [Android Touch 开始 (示例) ](/samples/xamarin/monodroid-samples/applicationfundamentals-touch-start)
- [Android Touch 最终 (示例) ](/samples/xamarin/monodroid-samples/applicationfundamentals-touch-final)
- [FingerPaint (示例) ](/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint)