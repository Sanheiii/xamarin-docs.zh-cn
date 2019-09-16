---
title: Xamarin.Forms ListView
description: 本指南介绍了 Xamarin. 窗体 ListView，可用于在交互式列表中显示数据。
ms.prod: xamarin
ms.assetid: FEFDF7E0-720F-4BD1-863F-4477226AA695
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/04/2019
ms.openlocfilehash: 9d7dd98b98c7722d6fcdcb49c10e326732fa9f0b
ms.sourcegitcommit: a5ef4497db04dfa016865bc7454b3de6ff088554
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2019
ms.locfileid: "70997985"
---
# <a name="xamarinforms-listview"></a>Xamarin.Forms ListView

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistview)

[`ListView`](xref:Xamarin.Forms.ListView)是用于显示数据列表的视图，特别是需要滚动的长列表。

> [!IMPORTANT]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView) 是使用不同布局规范显示数据列表的视图。 它旨在提供更灵活、更高的性能替代方法[`ListView`](xref:Xamarin.Forms.ListView)。 有关详细信息，请参阅 [Xamarin.Forms CollectionView](~/xamarin-forms/user-interface/collectionview/index.md)。

## <a name="use-cases"></a>用例

在显示可滚动数据列表的任何情况下，都可以使用控件。`ListView` `ListView`类支持上下文操作和数据绑定。

控件不应[`TableView`](~/xamarin-forms/user-interface/tableview.md)与控件混淆。 `ListView` 当你有选项或数据的非绑定列表时，控件是更好的选项，因为它允许在XAML中指定预定义的选项。`TableView` 例如，"iOS 设置" 应用程序（其中包含一组预定义的选项）更适合使用一个`TableView`。 `ListView`

类不支持在 XAML 中定义列表项，您必须`ItemsSource`使用属性`ItemTemplate`或数据绑定来定义列表中的项。 `ListView`

`ListView`最适合包含一种数据类型的集合。 此要求是因为只有一种类型的单元格可用于列表中的每一行。 `TableView`控件可以支持多个单元格类型，因此，在需要显示多个数据类型时，它是更好的选择。

有关将数据绑定到`ListView`实例的详细信息，请参阅[ListView 数据源](~/xamarin-forms/user-interface/listview/data-and-databinding.md)。

## <a name="components"></a>组件

`ListView`控件具有许多可用于测试每个平台的本机功能的组件。 以下部分定义了这些组件。

### <a name="headers-and-footerscustomizing-list-appearancemdheaders-and-footers"></a>[页眉和页脚](customizing-list-appearance.md#headers-and-footers)

页眉和页脚组件显示在列表的开头和末尾，独立于列表的数据。 页眉和页脚可以绑定到 ListView 的数据源中的单独数据源。

### <a name="groupscustomizing-list-appearancemdgrouping"></a>[组合](customizing-list-appearance.md#grouping)

可以对中`ListView`的数据进行分组，以便于导航。 组通常是数据绑定的。 以下屏幕截图显示了`ListView`具有分组数据的：

Listview 中的[分组数据的 "listview" 分组数据![](images/grouping-depth-cropped.png)](images/grouping-depth.png#lightbox "")

### <a name="cellscustomizing-cell-appearancemd"></a>[单元格](customizing-cell-appearance.md)

中的`ListView`数据项称为 "单元格"。 每个单元格对应于一行数据。 有内置的单元格可供选择，也可以定义自己的自定义单元格。 内置和自定义单元格可以是在 XAML 或代码中使用或定义。

- [内置单元](customizing-cell-appearance.md#built-in-cells)（如`TextCell`和`ImageCell`）对应于本机控件，尤其是高性能。
  - [`TextCell`](customizing-cell-appearance.md#textcell)显示文本字符串，还可以选择包含详细信息文本。 详细信息的文本呈现为与强调文字颜色较小的字体中的第二个行。
  - [`ImageCell`](customizing-cell-appearance.md#imagecell)显示带有文本的图像。 显示为`TextCell` ，左侧有图像。
- [自定义单元](customizing-cell-appearance.md#custom-cells)用于显示复杂数据。 例如，自定义单元可用于显示包含唱片集和艺术家的歌曲列表。

以下屏幕截图显示了`ListView`具有 ImageCell 项的：

(images/image-cell-default-cropped.png)](images/image-cell-default.png#lightbox "Listview 中")的["ImageCell 项" ImageCell 项![]

若要详细了解如何在中`ListView`自定义单元，请参阅[自定义 ListView 单元格外观](customizing-cell-appearance.md)。

## <a name="functionality"></a>功能

`ListView`类支持多种交互样式。

- 通过[请求刷新](interactivity.md#pull-to-refresh)，用户可以请求`ListView`向下移动以刷新内容。
- [上下文操作](interactivity.md#context-actions)允许开发人员指定单个列表项的自定义操作。 例如，您可以在 iOS 上，实现轻扫操作或长按在 Android 上的操作。
- [选择](interactivity.md#selection-and-taps)允许开发人员将功能附加到列表项的选择和 deselection 事件。

以下屏幕截图显示了`ListView`具有上下文操作的：

(images/context-default-cropped.png)](images/context-default.png#lightbox "Listview 中")的["在 listview 中的上下文操作" 上下文操作![]

若要了解有关的交互功能的`ListView`详细信息，请参阅[与 ListView 交互 & 的操作](interactivity.md)。

## <a name="related-links"></a>相关链接

- [使用与 ListView （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistview)
- [两个双向绑定 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-switchentrytwobinding)
- [构建在单元格 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-builtincells)
- [自定义单元格 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)
- [分组 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-grouping)
- [自定义呈现器视图 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative/)
- [ListView 交互性 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)
