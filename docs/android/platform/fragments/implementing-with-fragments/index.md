---
title: 实现片段 - 演练
description: 本文演练如何使用片段开发 Xamarin.Android 应用程序。
ms.topic: tutorial
ms.prod: xamarin
ms.assetid: A71E9D87-CB69-10AB-CE51-357A05C76BCD
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/26/2018
ms.openlocfilehash: ab680020f62548eed4d1da0e4dbb13434a6d8ce7
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453943"
---
# <a name="implementing-fragments---walkthrough"></a>实现片段 - 演练

片段是自包含模块化组件，可帮助解决面向具有各种屏幕大小的设备的 Android 应用的复杂性。本文演练如何在开发 Xamarin.Android 应用程序时创建和使用片段。

## <a name="overview"></a>概述

在此部分中，会演练如何在 Xamarin.Android 应用程序中创建和使用片段。 此应用程序会在列表中显示威廉·莎士比亚的多个戏剧的标题。 当用户点击某个戏剧标题时，应用会在单独活动中显示该戏剧中的引言：

[![在处于纵向模式的 Android 手机上运行的应用](./images/intro-screenshot-phone-sml.png)](./images/intro-screenshot-phone.png#lightbox)

当手机旋转到横向模式时，应用的外观会更改：戏剧列表和引言会出现在同一个活动中。 选择戏剧时，引言会显示在同一个活动中：

[![在处于横向模式的 Android 手机上运行的应用](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

最后，如果应用在平板电脑上运行：

[![在 Android 平板电脑上运行的应用](./images/intro-screenshot-tablet-sml.png)](./images/intro-screenshot-tablet.png#lightbox)

此示例应用程序可以使用片段和[备用布局](../../../app-fundamentals/resources-in-android/alternate-resources.md)，轻松适应不同外形规格和方向，同时尽量减少代码更改。

应用程序的数据会存在于两个字符串数组中，这些数组在应用中硬编码为 C# 字符串数组。 每个数组都用作一个片段的数据源。  一个数组保存某些莎士比亚戏剧的名称，另一个数组保存该戏剧中的引言。 当应用启动时，它会在 `ListFragment` 中显示戏剧名称。 当用户在 `ListFragment` 中单击某个戏剧时，应用会启动另一个活动，该活动会显示引言。

应用的用户界面会包含两个布局，一个用于纵向模式，另一个用于横向模式。 在运行时，Android 会基于设备的方向确定要加载的布局，并为要呈现的活动提供该布局。 用于响应用户单击和显示数据的所有逻辑都包含在片段中。 应用中的活动仅作为将承载片段的容器而存在。

本演练分为两个指南。 [第一部分](./walkthrough.md)侧重于应用程序的核心部分。 将创建一组布局（为纵向模式进行了优化），以及两个片段和两个活动：

1. `MainActivity` &nbsp; 这是应用的启动活动。
1. `TitlesFragment` &nbsp; 此片段会显示威廉·莎士比亚所写戏剧的标题列表。 它会由 `MainActivity` 托管。
1. `PlayQuoteActivity` &nbsp; `TitlesFragment` 会启动 `PlayQuoteActivity`，以响应用户在 `TitlesFragment` 中选择戏剧。
1. `PlayQuoteFragment` &nbsp; 此片段会显示威廉·莎士比亚戏剧中的引文。 它会由 `PlayQuoteActivity` 托管。

[本演练的第二部分](./walkthrough-landscape.md)将讨论如何添加备用布局（针对横向模式进行了优化），它会在屏幕上同时显示两个片段。 此外，还会对代码进行一些轻微代码更改，以便应用使其行为适应在屏幕上并发显示的片段数。

## <a name="related-links"></a>相关链接

- [FragmentsWalkthrough（示例）](/samples/xamarin/monodroid-samples/fragmentswalkthrough)
- [设计器概述](~/android/user-interface/android-designer/index.md)
- [实现片段](https://developer.android.com/guide/topics/fundamentals/fragments.html)
- [支持包](https://developer.android.com/sdk/compatibility-library.html)