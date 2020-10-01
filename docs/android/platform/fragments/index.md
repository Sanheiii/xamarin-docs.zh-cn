---
title: Fragments
description: Android 3.0 引入了 Fragment 功能，展示如何支持针对手机和平板电脑的许多不同屏幕尺寸更灵活地设计。 本文介绍如何使用 Fragment 开发 Xamarin.Android 应用程序，并介绍如何支持将 Fragment 用于预装了 Android 3.0（API 级别 11）的设备。
ms.prod: xamarin
ms.assetid: 1AFB4242-A337-F8E0-83D9-B8D850D7F384
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/15/2018
ms.openlocfilehash: 960f64c05bcc474a7e197c06990840d5e9b88caf
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453839"
---
# <a name="fragments"></a>Fragments

Android 3.0 引入了片段，展示了如何支持更灵活的设计，以用于电话和平板电脑的许多不同屏幕尺寸。本文将介绍如何使用片段开发 Xamarin.Android 应用程序，以及如何在版本低于 Android 3.0（API 级别 11）的设备上支持片段。

## <a name="fragments-overview"></a>片段概述

大多数平板电脑的屏幕尺寸较大，这额外增加了 Android 开发的复杂度，因为专为较小屏幕设计的布局不一定适用于较大屏幕，反之亦然。 为了降低由此带来的复杂度，Android 3.0 新增了两个功能，即片段和支持包。

可以将片段看作是用户界面模块。 使用片段，开发人员可以将用户界面划分为可重用的独立部分，这些部分能够在单独的活动中运行。 在运行时，这些活动本身将决定要使用哪些片段。

支持包最初称为“兼容性库”，用于支持在版本低于 Android 3.0（API 级别 11）的设备上使用片段。

例如，下图展示了一个应用程序如何跨各种设备外形规格使用片段。

[![展示如何在平板电脑和电话上使用片段的图](images/00.png)](images/00.png#lightbox)

片段 A 包含列表，片段 B 包含此列表中选定项的详细信息。 当应用程序在平板电脑上运行时，它可以在同一个活动上同时显示这两个片段。 如果相同的应用程序是在电话（屏幕较小）上运行，那么片段是在两个单独的活动中托管。 在两种外形规格上，片段 A 和片段 B 是相同的，但托管它们的活动是不同的。

为了帮助活动协调和管理所有这些片段，Android 引入了名为 FragmentManager 的新类。 每个活动都有自己的 `FragmentManager` 实例，用于添加、删除和查找托管的片段。 下图展示了片段和活动之间的关系：

[![展示活动、片段管理器和片段之间关系的图](images/01.png)](images/01.png#lightbox)

在某些方面，可以将片段看作是复合控件或迷你活动。 它们将 UI 片段捆绑成可重用模块，然后由开发人员在活动中单独使用。 片段确实有与活动类似的视图层次结构，但与活动不同的是，它可以跨屏幕共享。 视图不同于片段，因为片段有自己的生命周期，而视图则没有。

虽然活动托管一个或多个片段，但它对片段本身并不直接了解。 同样，片段也不直接了解托管活动中的其他片段。 不过，片段和活动了解其活动中的 `FragmentManager`。 通过使用 `FragmentManager`，活动或片段可以获取对特定片段实例的引用，然后对此实例调用方法。 这样，活动或片段就可以与其他片段进行通信和交互。

本指南全面介绍了如何使用片段，具体包括：

- **创建片段** - 如何创建基本片段和必须实现的关键方法。
- **片段管理和事务** - 如何在运行时控制片段。
- **Android 支持包** - 如何使用允许在旧版 Android 上使用片段的库。

## <a name="requirements"></a>要求

片段在自 API 级别 11 (Android 3.0) 起的 Android SDK 中可用，如下面的屏幕截图所示：

[![在 Android SDK 管理器中选择 API 级别](images/02.png)](images/02.png#lightbox)

片段在 Xamarin.Android 4.0 及更高版本中可用。 Xamarin.Android 应用程序必须至少定目标到 API 级别 11 (Android 3.0) 或更高版本，才能使用片段。 可以在项目的“属性”中设置目标框架，如下所示：

[![在项目选项中设置目标框架 API 级别](images/03-sml.png)](images/03.png#lightbox)

使用 Android 支持包和 Xamarin.Android 4.2 或更高版本，可以在旧版 Android 中使用片段。 本节的文档中更详细地介绍了具体操作。

## <a name="related-links"></a>相关链接

- [Honeycomb Gallery（示例）](/samples/xamarin/monodroid-samples/honeycombgallery)
- [片段](https://developer.android.com/guide/topics/fundamentals/fragments.html)
- [支持包](https://developer.android.com/sdk/compatibility-library.html)