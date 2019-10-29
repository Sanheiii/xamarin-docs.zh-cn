---
title: Xamarin 中的用户界面控件
description: 本文档链接到介绍适用于 Xamarin iOS 开发人员的各种 iOS 用户界面控件的指南。 链接内容讨论了警报、按钮、集合视图、图像、手动照相机控件、地图、标签、选取器、日期选取器等。
ms.prod: xamarin
ms.assetid: C00EA232-ADCC-42AD-BF86-B526414A21C6
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: fbf847ef49be83494f593291fbb0a00934bc3ced
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022047"
---
# <a name="user-interface-controls-in-xamarinios"></a>Xamarin 中的用户界面控件

本文档介绍了一些最常见的 iOS 用户界面控件以及如何使用它们。

## <a name="alertsalertsmd"></a>[警报](alerts.md)

从 iOS 8 开始，UIAlertController 已完成替换为 UIActionSheet 和 UIAlertView，这两者现已弃用。

## <a name="buttonsbuttonsmd"></a>[按钮](buttons.md)

UIButton 类用于表示 iOS 屏幕中各种不同的按钮样式。 本部分介绍在 iOS 中使用按钮的不同选项。

## <a name="collection-viewsuicollectionviewmd"></a>[集合视图](uicollectionview.md)

`UICollectionView` 类中提供的集合视图是 iOS 6 中的一种新概念，使用布局引入屏幕上的多个项。 向 `UICollectionView` 提供数据以创建项以及与这些项进行交互的模式遵循的是在 iOS 开发中经常使用的相同委托和数据源模式。

## <a name="imagesimagemd"></a>[图像](image.md)

向应用程序添加图像需要两个步骤：首先，将图像添加到项目中;然后，添加控件和代码，将其显示在屏幕上。 有关 Xamarin 中的映像处理的详细信息，请参阅使用[图像](~/ios/app-fundamentals/images-icons/index.md)一文。

## <a name="manual-camera-controlsintro-to-manual-camera-controlsmd"></a>[手动相机控件](intro-to-manual-camera-controls.md)

手动相机控制（由 iOS 8 中的 `AVFoundation Framework` 提供）允许移动应用程序完全控制 iOS 设备的照相机。 这一精细的控制级别可用于创建专业级相机应用程序，并通过在拍摄静止图像或视频的同时调整照相机的参数来提供艺术家组合。

## <a name="mapsios-mapsindexmd"></a>[地图](ios-maps/index.md)

在所有新式移动操作系统中，地图是一项常见功能。 iOS 通过地图工具包框架以本机方式提供映射支持。 借助 Map 工具包，应用程序可以轻松地添加丰富的交互式地图。 可以通过多种方式自定义这些地图，如添加批注来标记地图上的位置以及覆盖任意形状的图形。 Map 工具包甚至内置了对显示设备当前位置的支持。

## <a name="labelslabelsmd"></a>[标签](labels.md)

`UILabel` 控件用于显示单行和多行只读文本。

## <a name="pickers-and-date-pickerspickermd"></a>[选取器和日期选取器](picker.md)

选取器控件显示 "滚轮状" 控件，该控件包含具有突出显示的所选值的值的可滚动列表。 用户轮换滑轮来选择所需的选项。

选取器用于设置日期和/或时间的特定用户案例。 若要为此 Apple 提供，请创建名为 UIDatePicker 的 UIPickerView 类的自定义子类。

## <a name="progress-and-activity-indicatorsprogress-activity-indicatormd"></a>[进度和活动指示器](progress-activity-indicator.md)

iOS 提供两种主要方法来指示应用中的进度：活动指示器（包括特定_网络_活动指示器）和进度栏。

## <a name="search-barssearchbarmd"></a>[搜索栏](searchbar.md)

UISearchBar 用于在值列表中进行搜索。 

## <a name="sliders-switches-and-segmented-controlsslider-switch-segmented-controlsmd"></a>[滑块、开关和分段控件](slider-switch-segmented-controls.md)

使用滑块控件，可以在范围中简单选择数字值。 iOS 将 `UISwitch` 用作可通过其他平台上的单选按钮表示的布尔输入。 分段控件是一种允许用户与少量选项交互的组织方式。

## <a name="stack-viewuistackviewmd"></a>[堆栈视图](uistackview.md)

堆栈视图控件（`UIStackView`）利用自动布局和大小类的强大功能来管理子视图的堆栈（水平或垂直），这会动态响应 iOS 设备的方向和屏幕大小。

## <a name="tables-and-cellstablesindexmd"></a>[表和单元格](tables/index.md)

本部分介绍用于创建和显示表的类，并提供有关如何在 Xamarin 中使用它们的示例。 它将介绍如何使用表的默认外观，自定义布局，实现编辑，并使用 Xamarin iOS 设计器直观地设计表。 有时，显示的是行（如 "音乐" 应用）的列表，而另一些时候很难识别表控件（例如在 "联系人" 应用程序中编辑或在 Messages 应用中进行的对话）。

## <a name="text-inputtext-inputmd"></a>[文本输入](text-input.md)

接受用户文本输入是通过用于单行输入的 `UITextField` 和多行可编辑文本的 UITextView 来完成的。 您可以将其中任一控件拖动到屏幕上，然后双击以设置初始文本。

## <a name="tab-bars-and-tab-bar-controllerscreating-tabbed-applicationsmd"></a>[选项卡栏和选项卡栏控制器](creating-tabbed-applications.md)

使用选项卡导航 UI 的 iOS 应用程序是使用 UITabBarController 类生成的。 本文介绍如何设置包含多个控制器和视图的选项卡式应用程序。 接下来，我们将检查如何加载不是根控制器的 UITabBarController，例如登录屏幕之后。

## <a name="web-viewsuiwebviewmd"></a>[Web 视图](uiwebview.md)

在本文中，我们将探讨 Apple 提供的三个 Web 视图（`UIWebView`、`WKWebview`和 `SFSafariViewController`，它们之间的相似性和差异，以及如何使用它们。

## <a name="related-links"></a>相关链接

- [控件（示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/controls)
