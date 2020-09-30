---
title: Xamarin.Forms 单元
description: Xamarin.Forms 可以将单元格添加到 Listview 和 TableViews 中。 本文列出了中包含的单元 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: 77DA0C89-35D6-4C09-A072-3ADE53FD56CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: be5b2854c2f546e2194bd4dc95db77c9dcaf0760
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562803"
---
# <a name="no-locxamarinforms-cells"></a>Xamarin.Forms 单元

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_Xamarin.Forms 可以将单元格添加到 Listview 和 TableViews 中。_

*单元*是用于表中的项的专用元素，描述如何呈现列表中的每一项。 [`Cell`](xref:Xamarin.Forms.Cell)类派生自 [`Element`](xref:Xamarin.Forms.Element) ，后者 [`VisualElement`](xref:Xamarin.Forms.Element) 也派生自。 单元本身不是可视元素;它是用于创建视觉元素的模板。

`Cell` 专门用于 [`ListView`](xref:Xamarin.Forms.ListView) 和 [`TableView`](xref:Xamarin.Forms.TableView) 控件。 若要了解如何使用和自定义单元，请参阅 [`ListView`](~/xamarin-forms/user-interface/listview/index.md) 和 [`TableView`](~/xamarin-forms/user-interface/tableview.md) 文档。

## <a name="cells"></a>单元

Xamarin.Forms 支持以下单元类型：

| 类型 | 说明 | 外观 |
| --- | --- | --- |
| `TextCell` | [`TextCell`](xref:Xamarin.Forms.TextCell)显示一个或两个文本字符串。 将 [`Text`](xref:Xamarin.Forms.TextCell.Text) 属性设置为，还可以选择将 [`Detail`](xref:Xamarin.Forms.TextCell.Detail) 属性设置为这些文本字符串。<br /><br />[API 文档](xref:Xamarin.Forms.TextCell)  / [指南](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#textcell) | [![TextCell 示例](cells-images/TextCell.png "TextCell 示例")](cells-images/TextCell-Large.png#lightbox "TextCell 示例")<br />[此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TextCellDemoPage.cs)  /  的 c # 代码[XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TextCellDemoPage.xaml) |
| `ImageCell` | 将 [`ImageCell`](xref:Xamarin.Forms.ImageCell) 显示相同的信息， [`TextCell`](xref:Xamarin.Forms.TextCell) 但包含使用属性设置的位图 [`Source`](xref:Xamarin.Forms.Image.Source) 。<br /><br />[API 文档](xref:Xamarin.Forms.ImageCell)  / [指南](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#imagecell) | [![ImageCell 示例](cells-images/ImageCell.png "ImageCell 示例")](cells-images/ImageCell-Large.png#lightbox "ImageCell 示例")<br />[此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageCellDemoPage.cs)  /  的 c # 代码[XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageCellDemoPage.xaml) |
| `SwitchCell` | [`SwitchCell`](xref:Xamarin.Forms.SwitchCell)包含带有属性的文本集 [`Text`](xref:Xamarin.Forms.SwitchCell.Text) ，以及最初使用布尔值属性设置的打开/关闭开关 [`On`](xref:Xamarin.Forms.SwitchCell.On) 。 处理在 [`OnChanged`](xref:Xamarin.Forms.SwitchCell.OnChanged) 属性更改时要通知的事件 `On` 。<br /><br />[API 文档](xref:Xamarin.Forms.SwitchCell)  / [指南](~/xamarin-forms/user-interface/tableview.md#switchcell) | [![SwitchCell 示例](cells-images/SwitchCell.png "SwitchCell 示例")](cells-images/SwitchCell-Large.png#lightbox "SwitchCell 示例")<br />[此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchCellDemoPage.cs)  /  的 c # 代码[XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchCellDemoPage.xaml) |
| `EntryCell` | [`EntryCell`](xref:Xamarin.Forms.EntryCell)定义一个 [`Label`](xref:Xamarin.Forms.EntryCell.Label) 属性，该属性标识属性中的单元格和一行可编辑文本 [`Text`](xref:Xamarin.Forms.EntryCell.Text) 。 处理 [`Completed`](xref:Xamarin.Forms.EntryCell.Completed) 事件，以便在用户完成文本输入时收到通知。<br /><br />[API 文档](xref:Xamarin.Forms.EntryCell)  / [指南](~/xamarin-forms/user-interface/tableview.md#entrycell) | [![EntryCell 示例](cells-images/EntryCell.png "EntryCell 示例")](cells-images/EntryCell-Large.png#lightbox "EntryCell 示例")<br />[此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryCellDemoPage.cs)  /  的 c # 代码[XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryCellDemoPage.xaml) |
| | | |

## <a name="related-links"></a>相关链接

- [Xamarin.Forms FormsGallery 示例](/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.Forms 示例](/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.Forms API 文档](/dotnet/api/xamarin.forms?view=xamarin-forms)