---
title: Xamarin 中的 tvOS 简介
description: 本文档链接到演示如何通过 Xamarin 构建 tvOS 应用程序的各种指南和示例。 这些指南介绍了各种功能，如用户界面开发、数据存储、图标等。
ms.prod: xamarin
ms.assetid: 14345503-1742-41F5-B2EF-EE31AB7C3516
ms.technology: xamarin-ios
ms.custom: xamu-video
author: davidortinau
ms.author: daortin
ms.date: 02/02/2018
ms.openlocfilehash: a001cabae5e137cda4b31e15d3e75401575b96a7
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435032"
---
# <a name="introduction-to-tvos-in-xamarin"></a>Xamarin 中的 tvOS 简介

## <a name="introducing-tvos"></a>TvOS 简介

Apple 已发布第5代 Apple TV 硬件，即 Apple TV 4K，它运行最新版本的 tvOS 操作系统，并基于 iOS 11。

Apple TV 平台面向开发人员，使他们能够创建丰富的沉浸式应用程序，并通过 Apple TV 的内置应用商店进行发布。

有关 tvOS 的详细信息，请参阅 [入门](~/ios/tvos/get-started/index.md) 文档。

> [!VIDEO https://youtube.com/embed/Q04oIYymfGM]

**带 Xamarin 视频的 tvOS**

## <a name="documentation"></a>文档

以下文档将帮助你开始通过 Xamarin 构建 tvOS 应用：

- [TvOS 11 简介](~/ios/tvos/platform/introduction-to-tvos11.md) -本文介绍 tvOS 11 中适用于 tvOS 开发人员的新增功能。
- [TvOS 10 简介](~/ios/tvos/platform/introduction-to-tvos10/index.md) -本文介绍了 tvOS 10 中所有新的和修改的 api，以及 tvOS 开发人员提供的功能。
- [TvOS 9 简介](~/ios/tvos/platform/tvos9.md) -本文介绍了 tvOS 9 中所有适用于 Xamarin tvOS 开发人员的新的和修改的 api 和功能。 
- [你好，tvOS 快速入门指南](~/ios/tvos/get-started/hello-tvos.md) –本指南介绍了如何创建你的第一个 tvOS 应用程序，并在此过程中引入了开发工具链，其中包括 Visual Studio for Mac、Xcode 和 Interface Builder。 它还介绍了将 UI 控件公开给代码的插座和操作，最后还介绍了如何生成、运行和测试 tvOS 应用程序。
- [使用图标和图像](~/ios/tvos/app-fundamentals/icons-images.md) -本文介绍如何在 tvOS 应用内设计和处理图标和图像。
- [使用导航和焦点](~/ios/tvos/app-fundamentals/navigation-focus.md) –本文介绍了焦点的概念以及如何使用它来呈现和处理 tvOS 应用内部的导航。
- [资源和数据存储](~/ios/tvos/app-fundamentals/resources-data-storage.md) -本文介绍如何在 tvOS 应用中处理资源和永久性数据存储。
- [Siri 远程和蓝牙控制器](~/ios/tvos/platform/remote-bluetooth.md) -本文介绍了如何在 tvOS 应用程序中支持新的 Siri 远程和蓝牙游戏控制器。
- [用户界面](~/ios/tvos/user-interface/index.md) -一般用户体验 (UX) 覆盖率，包括用户界面 (UI) 控件，请在使用 tvOS 时使用 Xcode 的 INTERFACE BUILDER 和 UX 设计原则。
- [部署、测试和指标](~/ios/tvos/deploy-test/index.md) –本部分介绍了用于测试应用程序以及如何分发应用程序的主题。 此处的主题包括用于调试的工具、部署到测试人员以及如何将应用程序发布到 Apple TV 应用商店。
- [支持的程序集](~/ios/tvos/internals/assemblies.md) –这是 Xamarin 为 tvOS 应用程序支持的程序集的列表。
- [支持的和不受](~/ios/tvos/internals/frameworks.md) 支持的框架– xamarin 适用于你的 tvOS 应用程序所支持的框架列表。

## <a name="sample-projects"></a>示例项目

用 Xamarin 构建的示例 tvOS 应用：

- [你好，tvOS](/samples/xamarin/ios-samples/tvos-hello-tvos) –此示例在 tvOS 中实现一个简单的 "Hello World" 应用，并提供使用 tvOS 的基本知识。
- [tvAlerts](/samples/xamarin/ios-samples/tvos-tvalerts) -此示例演示如何在 tvOS 应用中使用警报。
- [tvButtons](/samples/xamarin/ios-samples/tvos-tvbuttons) -此示例演示如何使用按钮是 tvOS 应用。
- [tvRemote](/samples/xamarin/ios-samples/tvos-tvremote) -此示例介绍了几种方法，即 tvOS 应用可与 Siri 远程交互以导航用户界面。
- [tvCollection](/samples/xamarin/ios-samples/tvos-tvcollection) -此示例演示如何在 tvOS 应用中使用集合视图控制器。
- [tvNavBars](/samples/xamarin/ios-samples/tvos-tvnavbars) -此示例演示如何在 tvOS 应用程序中使用导航栏。
- [tvPages](/samples/xamarin/ios-samples/tvos-tvpages) -此示例演示如何在 tvOS 应用程序中使用页面控件。
- [tvProgress](/samples/xamarin/ios-samples/tvos-tvprogress) -此示例演示如何在 tvOS 应用中处理进度指示器。
- [tvSplit](/samples/xamarin/ios-samples/tvos-tvsplit) -此示例演示如何在 tvOS 应用程序中使用拆分视图控制器。
- [tvStackView](/samples/xamarin/ios-samples/tvos-tvstackview) -此示例演示如何在 tvOS 应用程序中使用堆栈视图。
- [UICatalog](/samples/xamarin/ios-samples/tvos-uicatalog) -演示如何在 tvOS 上的 UIKit 框架中使用多个视图和控件。 如果正在查找系统提供的特定控件或视图，请参阅此示例。

此外，Apple 提供了以下示例应用，这些应用可转码到 c # 以使用 Xamarin 对 tvOS 应用的支持：

- [DemoBots：使用 SpriteKit 和 GameplayKit 构建跨平台游戏](https://developer.apple.com/library/prerelease/tvos/samplecode/DemoBots/)

## <a name="known-issues-and-troubleshooting"></a>已知问题和疑难解答

如果在通过 Xamarin 生成 tvOS 时遇到任何问题，请查看 [发行说明](/xamarin/ios/release-notes/)、 [xamarin iOS 论坛](https://forums.xamarin.com/categories/ios)、 [Xamarin Bugzilla 跟踪](https://bugzilla.xamarin.com/query.cgi?product=iOS)器和 [GitHub](https://github.com/xamarin/xamarin-macios/issues) 了解现有问题。

[在 GitHub 上](https://github.com/xamarin/xamarin-macios/issues)报告新问题和建议。

## <a name="related-links"></a>相关链接

- [tvOS 示例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人体学接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [TvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
- [用 Xamarin (视频构建适用于 tvOS 的应用) ](https://university.xamarin.com/lightninglectures/tvos-with-xamarin)