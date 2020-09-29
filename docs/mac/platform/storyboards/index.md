---
title: Xamarin 中的情节提要简介
description: 本文介绍了如何在 Xamarin 应用程序中使用情节提要。 其中介绍了如何使用 Storyboard 和 Xcode 的 Interface Builder 创建和维护应用 UI。
ms.prod: xamarin
ms.assetid: F37BA503-0B25-489F-80A8-58C493291A55
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 24a1e1af5b56a3cd10557658a0cad0c4e73ebac8
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433374"
---
# <a name="introduction-to-storyboards-in-xamarinmac"></a>Xamarin 中的情节提要简介

_本文介绍了如何在 Xamarin 应用程序中使用情节提要。其中介绍了如何使用情节提要和 Xcode 的 Interface Builder 创建和维护应用的 UI。_

利用情节提要，你可以为 Xamarin 应用程序开发一个用户界面，该用户界面不仅包括窗口定义和控件，还包含不同 windows (通过 segue) 和视图状态之间的链接。

[![Xcode 中的示例 UI](images/intro01.png)](images/intro01.png#lightbox)

本文将介绍如何使用情节提要定义 Xamarin 应用程序的用户界面。

<a name="What-are-Storyboards"></a>

## <a name="what-are-storyboards"></a>什么是情节提要？

通过使用情节提要，可以在单个位置定义 Xamarin 应用程序的所有 UI，并在其各个元素和用户界面之间进行所有导航。 Xamarin 的情节提要，工作方式与 Xamarin 的情节提要的工作方式非常类似。 但是，它们包含不同的 _Segue 类型_ 集，因为不同的接口惯例。

<a name="Working-with-Scenes"></a>

### <a name="working-with-scenes"></a>使用场景

如上所述，情节提要将给定应用的所有 UI 定义为细分为其 _视图控制器_的功能概述。 在 Xcode 的 Interface Builder 中，其中每个控制器都位于其自身的 _场景_中。

[![示例视图控制器](images/intro02.png)](images/intro02.png#lightbox)

每个场景表示给定的视图和视图控制器对，其中一组线条 (称为 Segue) ，用于连接 UI 中的每个场景，从而显示其关系。 某些 Segue 定义了一个视图控制器如何包含一个或多个子视图或视图控制器。 其他 Segue，定义视图控制器 (之间的转换，如) 显示 "segue" 或 "对话框"。 

[![示例 segue](images/intro03.png)](images/intro03.png#lightbox)

要注意的最重要的一点是，每个 Segue 表示应用 UI 的给定元素之间某种形式的数据流。

<a name="Working-with-View-Controllers"></a>

### <a name="working-with-view-controllers"></a>使用视图控制器

视图控制器定义 Mac 应用内的给定信息视图与提供该信息的数据模型之间的关系。 情节提要中的每个顶层场景都代表 Xamarin 应用程序代码中的一个视图控制器。

[![示例单视图控制器](images/intro04.png)](images/intro04.png#lightbox)

通过这种方式，每个视图控制器都是自包含的、可重复使用的信息的可视化表示形式的配对 (视图) 以及用于显示和控制该信息的逻辑。

在给定场景中，您可以执行通常由单个文件处理的所有操作 `.xib` ： 

- 将子视图和控件 (例如按钮和文本框) 中。
- 定义元素位置和自动布局约束。
- 用于向代码公开 UI 元素的连线操作和插座。

<a name="Working-with-Segues"></a>

### <a name="working-with-segues"></a>使用 Segue

如上所述，Segue 提供了定义应用 UI 的所有场景之间的关系。 如果你熟悉 iOS 中的情节提要，你会知道 Segue for iOS 通常定义全屏视图之间的转换。 这不同于 macOS，Segue 通常定义 "包含" (其中一个场景是父场景) 的子场景。

在 macOS 中，大多数应用都倾向于使用 UI 元素（如拆分视图和选项卡）在同一个窗口中将其视图组合在一起。 不同于 iOS，其中的视图需要在屏幕上转换，因为物理显示空间有限。

对于 macOS 的倾向，在某些情况下，使用 _演示 segue_ ，如模式窗口、工作表视图和 Popovers。

使用 Presentation Segue 时，可以重写用于显示的 `PrepareForSegue` 父视图控制器的方法来初始化和变量，并向显示的视图控制器提供任何数据。

<a name="Design-and-Run-Times"></a>

### <a name="design-and-run-times"></a>设计和运行时间

在设计时 (在 Xcode 的 Interface Builder) 中布局 UI 时，应用的 UI 的每个元素将细分为其构成项：

- **场景** -由以下内容组成：
  - **查看控制器** -定义视图与支持它们的数据之间的关系。
  - **视图和子视图** -组成用户界面的实际元素。
  - **包含 segue** -定义场景之间的父子关系。
- **表示 segue** -定义单独的演示模式。 

通过以这种方式定义每个元素，它只允许在运行时中需要的每个元素的延迟加载。 在 macOS 中，整个过程旨在使开发人员能够创建复杂、灵活的用户界面，该界面需要最少的支持代码来使其正常工作，同时尽可能提高系统资源的效率。

<a name="Storyboard-Quick-Start"></a>

## <a name="storyboard-quick-start"></a>情节提要快速入门

在 [情节提要快速入门](~/mac/platform/storyboards/quickstart.md) 指南中，我们将创建一个简单的 Xamarin 应用程序，它介绍了使用情节提要创建用户界面的关键概念。 该示例应用包含一个包含 _内容区域_ 和 _检查器区域_ 的分割视图，它将显示一个简单的首选项对话框窗口。 我们将使用 Segue 将所有用户界面元素绑定在一起。

<a name="Working-with-Storyboards"></a>

## <a name="working-with-storyboards"></a>使用情节提要

本部分介绍如何在 Xamarin 应用程序中使用 [情节提要](~/mac/platform/storyboards/indepth.md) 的详细信息。 我们深入了解了幕后，并介绍了它们如何由视图控制器和视图组成。 接下来，我们将介绍如何将场景与 Segue 相关联。 最后，我们将介绍如何使用自定义 Segue 类型。 

<a name="Complex-Storyboard-Example"></a>

## <a name="complex-storyboard-example"></a>复杂情节提要示例

有关在 Xamarin 应用程序中使用情节提要的复杂示例示例，请参阅 [SourceWriter 示例应用](/samples/xamarin/mac-samples/sourcewriter)。 SourceWriter 是一个非常简单的源代码编辑器，提供代码补全和简单语法突出显示支持。

SourceWriter 代码已经完全注释，且在可用时，提供了相关链接，链接涵盖了从关键技术或方法到 Xamarin.Mac 指南文档中的相关信息。

<a name="Summary"></a>

## <a name="summary"></a>总结

本文大致介绍了如何在 Xamarin 应用程序中使用情节提要。 我们了解到如何使用情节提要创建新的应用程序，以及如何定义用户界面。 我们还了解了如何使用 segue 在不同窗口和视图状态之间导航。

## <a name="related-links"></a>相关链接

- [Hello，Mac（示例）](/samples/xamarin/mac-samples/hello-mac)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [使用 Windows](~/mac/user-interface/window.md)
- [OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Windows 简介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)