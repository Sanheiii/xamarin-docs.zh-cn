---
title: Xamarin 中的 macOS 用户界面控件
description: 本文档链接到介绍适用于 Xamarin 开发人员的各种用户界面控件的指南。 链接内容查看窗口、对话框、警报、菜单、工具栏、表视图、大纲视图等。
ms.prod: xamarin
ms.assetid: 876B6EC2-E158-43F2-B9C9-03F54F3D2A49
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/27/2018
ms.openlocfilehash: 7ae33883b83a4080903b524ea78e0da009ad0a3a
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431527"
---
# <a name="macos-user-interface-controls-in-xamarinmac"></a>Xamarin 中的 macOS 用户界面控件

_本文链接到描述各种 macOS UI 控件的指南。_

在 Xamarin 应用 *程序中使用* c # 和 .net 时，您可以访问与在 *Xcode 和* 中工作的开发人员相同的用户界面控件。 由于 Xamarin 与 Xcode 直接集成，因此可以使用 Xcode 的 _Interface Builder_ 来创建和维护用户界面 (或选择直接在 c # 代码) 中创建它们。

下面列出的指南提供了有关在 Xamarin 应用程序中使用 macOS UI 元素的详细信息。 强烈建议您先完成 [Hello，Mac](~/mac/get-started/hello-mac.md) 一文，特别是 [Xcode 和 Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) 及 [输出口和操作](~/mac/get-started/hello-mac.md#outlets-and-actions) 部分的简介，因为它涵盖了我们在每篇文章中将使用的主要概念和技巧。

你可能想要查看[Xamarin 内部](~/mac/internals/how-it-works.md)示例文档的 "将[c # 类/方法公开到目标 c](~/mac/internals/how-it-works.md#exposing-c-classes--methods-to-objective-c) " 部分，因为它说明了 `Register` 用于将 `Export` C # 类连接到目标 C 对象和 UI 元素的和特性。

## <a name="windows"></a>[Windows](~/mac/user-interface/window.md)

本文介绍如何在 Xamarin. Mac 应用程序中使用 windows 和面板。 它介绍了如何在 Xcode 和 Interface Builder 中创建和维护窗口和面板，如何使用 windows 在 c # 代码中加载、使用 windows 和响应 windows 中的窗口和面板。

## <a name="dialogs"></a>[对话框](~/mac/user-interface/dialog.md)

本文介绍如何在 Xamarin. Mac 应用程序中使用对话框和模式窗口。 它介绍了如何在 Xcode 和 Interface Builder 中创建和维护模式窗口，如何处理标准对话框，以及如何在 c # 代码中显示和响应 windows。

## <a name="alerts"></a>[警报](~/mac/user-interface/alert.md)

本文介绍如何在 Xamarin. Mac 应用程序中使用警报。 其中介绍了如何创建和显示 c # 代码中的警报以及如何响应警报。

## <a name="menus"></a>[菜单](~/mac/user-interface/menu.md)

菜单用于 Mac 应用程序用户界面的各个部分;在屏幕顶部的应用程序主菜单中，可显示在窗口中任意位置的弹出菜单和上下文菜单。 菜单是构成 Mac 应用程序用户体验的一个组成部分。 本文介绍如何在 Xamarin. Mac 应用程序中使用 Cocoa 菜单。

## <a name="standard-controls"></a>[标准控件](~/mac/user-interface/standard-controls.md)

使用 AppKit 应用程序中的标准控件，如按钮、标签、文本字段、复选框和分段控件。 本指南介绍如何将它们添加到 Xcode 的 Interface Builder 中的用户界面设计中，如何将它们通过输出口和操作公开给代码，以及如何使用 c # 代码中的 AppKit 控件。

## <a name="toolbars"></a>[工具栏](~/mac/user-interface/toolbar.md)

本文介绍如何在 Xamarin. Mac 应用程序中使用工具栏。 它介绍了如何在 Xcode 和 Interface Builder 中创建和维护工具栏，如何使用输出口和操作将工具栏项公开到代码，如何启用和禁用工具栏项，最后响应 c # 代码中的工具栏项。

## <a name="table-views"></a>[表视图](~/mac/user-interface/table-view.md)

本文介绍如何在 Xamarin. Mac 应用程序中使用表视图。 它介绍了如何创建和维护 Xcode 和 Interface Builder 中的表视图，如何使用输出口和操作将表视图项公开到代码，如何填充表视图，以及如何响应 c # 代码中的表视图项。

## <a name="outline-views"></a>[大纲视图](~/mac/user-interface/outline-view.md)

本文介绍如何在 Xamarin. Mac 应用程序中使用大纲视图。 它介绍了如何在 Xcode 和 Interface Builder 中创建和维护大纲视图、如何使用输出口和操作向代码公开大纲视图项、如何填充大纲视图，以及如何响应 c # 代码中的大纲视图项。

## <a name="source-lists"></a>[源列表](~/mac/user-interface/source-list.md)

本文介绍如何在 Xamarin. Mac 应用程序中使用源列表。 它介绍了如何在 Xcode 和 Interface Builder 中创建和维护源列表，如何使用输出口和操作向代码公开源列表项，如何填充源列表，以及如何响应 c # 代码中的源列表项。

## <a name="collection-views"></a>[集合视图](~/mac/user-interface/collection-view.md)

本文介绍如何在 Xamarin. Mac 应用程序中使用集合视图。 它介绍了如何创建和维护 Xcode 和 Interface Builder 中的集合视图、如何使用输出口和操作向代码公开集合视图项、如何填充集合视图，以及如何响应 c # 代码中的集合视图。

## <a name="creating-custom-controls"></a>[创建自定义控件](~/mac/user-interface/custom-controls.md)

本文介绍了如何创建自定义用户界面控件 (方法是从 `NSControl`) 继承、绘制控件的自定义接口以及创建可用于 Xcode 的 Interface Builder 的自定义操作。

## <a name="mac-samples-gallery"></a>Mac 示例库

我们还建议查看 [Mac 示例库](/samples/browse/?products=xamarin&term=Xamarin.Mac)。 它包含大量随时可用的代码，可帮助你快速地从根本上获取 Xamarin Mac 项目。

## <a name="related-links"></a>相关链接

- [macOS 人机界面指南](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)