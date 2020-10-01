---
title: Xamarin.Mac 性能
description: 本文档提供了 Xamarin.Mac 应用一些性能注意事项。 其中讨论了新式目标框架、链接器、AOT、委托、用于重用视图的 Cocoa API 和异步代码。
ms.prod: xamarin
ms.assetid: 54B07DED-FDF2-49B2-A5FB-3A9357E65922
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 11/10/2017
ms.openlocfilehash: a1649c4b8498f3e7ad3684cb4d119236b4ceaf57
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435479"
---
# <a name="xamarinmac-performance"></a>Xamarin.Mac 性能

## <a name="overview"></a>概述

Xamarin.Mac 应用程序类似于 Xamarin.iOS，许多相同的性能建议均适用：

- [Xamarin.iOS 性能](~/ios/deploy-test/performance.md)
- [跨平台性能](~/cross-platform/deploy-test/memory-perf-best-practices.md)

但有很多特定于 macOS 的建议可能有所帮助。

## <a name="prefer-modern-target-framework"></a>首选新式目标框架

有许多性能特征和功能不同的[目标框架](~/mac/platform/target-framework.md)可供 Xamarin.Mac 应用程序使用。

若可行，请首选“新式”并使用依赖库来添加支持。 只有新式目标框架允许可大幅缩减程序集大小的链接。 这一点在启用 AOT 时尤为重要，因为完整程序集的 AOT 编译会生成较大的最终捆绑包。

## <a name="enable-the-linker"></a>启用链接器

加载和“及时”(JIT) 中的启动时间在某种程序上随最终二进制文件的大小呈线性缩放。 最简单的改进方式是删除带[链接器](~/mac/deploy-test/linker.md)的死代码。

此建议主要适用于新式目标框架用户，使用[平台链接](~/mac/deploy-test/linker.md)也可在有限范围内实现性能提升。

## <a name="enable-aot-when-appropriate"></a>适用时启用 AOT

启动性能的另一个方面是将程序集通过 JIT 编译为机器码。 预先 (AOT) 编译可大幅缩减启动时间，但存在很多折衷之处，详见 [AOT 文档](~/mac/internals/aot.md)。

## <a name="ensure-performant-delegates"></a>确保性能委托

许多 Xamarin.Mac 应用程序围绕着 `NSCollectionView`、`NSOutlineView` 或 `NSTableView` 等 Cocoa 视图。 通常，这些视图由你提供给 Cocoa 的 `Delegate` 和 `DataSource` 进行支持，用于解答有关显示内容的问题。

其中很多入口点经常被调用，有时在滚动时一秒被调用多次。

确保这些函数返回轻松计算的值，或使用已缓存的信息以防止用户界面遭到阻止。

## <a name="use-cocoa-provided-apis-for-reusing-views"></a>使用 Cocoa 提供的 API 来重用视图

许多包含大量子视图或单元格的 Cocoa 视图（如 `NSCollectionView`、`NSOutlineView` 和 `NSTableView`）都提供 API 来创建和重用视图。 这些 API 在快速滚动视图时创建共享项目池并防止出现性能问题。

## <a name="use-async-and-do-not-block-the-ui"></a>使用 async，不阻止 UI

桌面应用程序通常处理大量数据，非常易于阻止等待同步操作的 UI 线程。

只要可能，请使用 [async](~/cross-platform/platform/async.md) 和线程来防止 UI 被阻止。

对于长时间运行的操作，请考虑使用 [NSProgressIndicator](/samples/xamarin/mac-samples/progressbarexample) 或 Apple [HIG](https://developer.apple.com/macos/human-interface-guidelines/indicators/progress-indicators/) 中的其他选项来通知用户。

## <a name="related-links"></a>相关链接

- [跨平台性能](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [Xamarin.iOS 性能](~/ios/deploy-test/performance.md)