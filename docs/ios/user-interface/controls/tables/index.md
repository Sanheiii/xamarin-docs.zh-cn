---
title: 在 Xamarin 中使用表和单元格
description: 本文档链接到各种指南，这些指南介绍如何在 Xamarin iOS 应用中使用 UITableView 控件显示数据。
ms.prod: xamarin
ms.assetid: 04DF47DD-4E17-75D7-AC7C-8CF4A574CD21
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 01/06/2016
ms.openlocfilehash: fc2a76d97fe6c3bb52877bd58707872068f25968
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430807"
---
# <a name="working-with-tables-and-cells-in-xamarinios"></a>在 Xamarin 中使用表和单元格

本部分介绍用于创建和显示表的类，然后提供有关如何在 Xamarin 中使用它们的示例。 它将介绍如何使用表的默认外观，自定义布局，实现编辑，并使用 Xamarin iOS 设计器直观地设计表。 有时，显示的行 (如音乐应用程序) ，有时很难识别表控件 (例如，在 "联系人" 应用程序或 "消息" 应用) 的会话中进行编辑。

对于使用 Xamarin 的跨平台应用程序，UITableView 控件类似于 Android 中的 ListView 类 (，UITableViewSource 类类似于 Android 的适配器类) 。

这些文章全面介绍了如何使用表，其中包括：

- **表部分** –介绍和解释控件的可视元素  `UITableView` 。 
- **在表中显示数据** -演示如何创建和填充表，如何使用不同的表和单元格样式，并通过回收单元对象避免出现内存问题。 
- **高级用法** –生成自定义单元，并使用 UITableView 类的编辑功能。 
- **直观地创建表** –使用 Xamarin Designer for iOS 创建包含情节提要的表驱动接口。 

## <a name="contents"></a>目录

 [表部件 &amp; 功能](~/ios/user-interface/controls/tables/table-parts-and-functionality.md)

 [使用数据填充表](~/ios/user-interface/controls/tables/populating-a-table-with-data.md)

 [自定义表的外观](~/ios/user-interface/controls/tables/customizing-table-appearance.md)

 [编辑](~/ios/user-interface/controls/tables/editing.md)

 [行操作](~/ios/user-interface/controls/tables/row-action.md)

 [在情节提要中创建表](~/ios/user-interface/controls/tables/creating-tables-in-a-storyboard.md)

 [自动调整行高](~/ios/user-interface/controls/tables/autosizing-row-height.md)

## <a name="related-links"></a>相关链接

- [WorkingWithTables (示例) ](/samples/xamarin/ios-samples/workingwithtables)
- [情节提要中的表 (示例) ](/samples/xamarin/ios-samples/storyboardtable)
- [情节提要简介](~/ios/user-interface/storyboards/index.md)
- [情节提要 TableView 食谱](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/storyboard/storyboard_a_tableview)
- [Monotouch.dialog 简介](~/ios/user-interface/monotouch.dialog/index.md)
- [Github 上的 TableEditing 示例](https://github.com/xamarin/monotouch-samples/tree/master/TableEditing)
- [Github 上的 TableParts 示例](https://github.com/xamarin/monotouch-samples/tree/master/TableParts)
- [Github 上的 TableAndCellStyles 示例](https://github.com/xamarin/mobile-samples/tree/master/TablesLists)
- [UITableView 类引用](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableView_Class/)
- [UITableViewCell 类引用](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewCell_Class/)
- [UITableViewDelegate](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDelegate_Protocol/)
- [UITableViewDataSource](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDataSource_Protocol/)