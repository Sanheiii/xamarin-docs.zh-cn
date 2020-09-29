---
title: Xamarin 中的表部件和功能
description: 本文档介绍 iOS 中 UITableView 的各个部分。 它讨论了节标头、单元格、节尾、索引和编辑模式。
ms.prod: xamarin
ms.assetid: B4139C8B-28F2-4C0F-297F-BF5432C5A915
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 55f471fe02618becbfc4a7ab8571d9690fe44843
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430592"
---
# <a name="table-parts-and-functionality-in-xamarinios"></a>Xamarin 中的表部件和功能

UITableView 可以具有 "分组" 或 "纯" 样式，由以下部分组成：

- [节标头](#Section_Header)
-  (或行的[单元格](#Cells)（如果你喜欢）) 
- [节页脚](#Section_Footer)
- [索引](#Index)
- [编辑模式](#Edit_Features) (包含 "轻扫到删除" 和拖动控点以更改行顺序)  

这些屏幕截图显示部分行、页眉、页脚、编辑控件和索引的显示方式。

 [![这些屏幕截图显示部分行、页眉、页脚、编辑控件和索引的显示方式](table-parts-and-functionality-images/image1a.png)](table-parts-and-functionality-images/image1a.png#lightbox)

下面更详细地介绍了这些部分：

<a name="Section_Header"></a>

## <a name="section-header"></a>节标头

可以选择性地将单元分组为多个节，使用自定义标头进行标记，并/或用页脚标记。 可以使用字符串值设置标头，也可以提供自定义视图以允许不同的布局或样式。

<a name="Cells"></a>

## <a name="cells"></a>单元

单元是表的主要用户界面元素。 正确实现时，将重新使用单元以提高内存效率。 有四种内置单元样式，您可以创建自己的自定义单元格（在代码中），也可以在设计器中使用情节提要。

<a name="Section_Footer"></a>

## <a name="section-footer"></a>节页脚

可选节页脚可以使用字符串值进行设置，也可以提供自定义视图以允许不同的布局或样式。 节标头和表尾可以单独设置。

<a name="Index"></a>

## <a name="index"></a>索引

索引在表的右边缘下显示为一条字符。
触摸或拖动索引会加速滚动到该表的该部分。 索引是可选的，但建议使用它来帮助导航较长的列表。 索引通常不与分组样式一起使用。

<a name="Edit_Features"></a>

## <a name="editing-mode"></a>编辑模式

有几种不同的编辑功能可用：

- 滑动以删除单个单元。
- 进入编辑模式以显示每行中的 "删除" 按钮 
- 进入编辑模式以显示重新排序句柄。 
- 插入具有动画)  (的新单元。

本文档的其余部分介绍如何通过 Xamarin 实现所有这些 UITableView 功能。

## <a name="classes-overview"></a>类概述

用于显示表视图的主要类如下所示：

[![此处显示了用于显示表视图的主类](table-parts-and-functionality-images/classdiagram.png)](table-parts-and-functionality-images/classdiagram.png#lightbox)

每个类的用途如下所述：

- **UITableView** –一个视图，其中包含滚动容器中单元格的集合。 表格视图通常使用 iPhone 应用中的整个屏幕，但可能作为 iPad (上的较大视图的一部分存在，也可能出现在 segue 的) 中。 
- **UITableViewCell** –一个视图，该视图表示表视图中 (或行) 的单个单元格。 有四种内置单元类型，可以在 c # 中创建自定义单元，也可以在 iOS 设计器中创建。 
- **UITableViewSource** – Xamarin 专用抽象类，提供显示表所需的所有方法，包括行计数、返回每行的单元格视图、处理行选择以及许多其他可选功能。 要使 UITableView 正常工作，  *必须* 为此划分子类。 
- **Nsindexpath.fromitemsection** –包含唯一标识表中单元格位置的行和节属性。 
- **UITableViewController** –一种随时可用的 UIViewController，它将 UITableView 硬编码为其视图，并可通过 TableView 属性进行访问。 
- **UIViewController** –如果表不占用整个屏幕，则可以将 UITableView 添加到任何 UIViewController 并适当地设置其框架。 

UITableViewSource 替换了以下两个类，这些类在 Xamarin 中仍可用，但通常不需要：

- **UITableViewDataSource** –作为抽象类在 Xamarin 中建模的目标为 C 的协议。 必须是子类才能为表提供每个单元的视图，以及有关表中的表头、表尾以及行数和节数的信息。 
- **UITableViewDelegate** –作为类在 Xamarin 中建模的目标为 C 的协议。 处理选择、编辑功能和其他可选的表功能。 

在本文档中，所有示例都使用 UITableViewSource 并忽略这两个类。 由于 Apple 文档中的任何目标为 C 的示例都引用了这些示例，因此在此处提到它们是非常有用的，因此，了解它们的作用 (并且可以使用 Xamarin 的 UITableViewSource 而不是) 。

## <a name="related-links"></a>相关链接

- [WorkingWithTables (示例) ](/samples/xamarin/ios-samples/workingwithtables)