---
title: 用 Xamarin 编辑表
description: 本文档介绍如何在 Xamarin 中编辑表。 它讨论了如何轻扫到删除、编辑模式和行插入。
ms.prod: xamarin
ms.assetid: EC197F25-E865-AFA3-E5CF-B33FAB7744A0
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 3a33c2191f39ea72113a1d7eab660e1a172f752f
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430877"
---
# <a name="editing-tables-with-xamarinios"></a>用 Xamarin 编辑表

通过重写子类中的方法可启用表编辑功能 `UITableViewSource` 。 最简单的编辑行为是可使用单个方法重写实现的轻扫到删除手势。
更复杂的编辑 (包括移动行) 可在编辑模式下对表执行。

## <a name="swipe-to-delete"></a>刷到删除

"轻扫到删除" 功能是用户期望的 iOS 中自然的手势。 

 [![刷卡器到删除的示例](editing-images/image10.png)](editing-images/image10.png#lightbox)

有三种方法重写，它们会影响滑动手势，以便在单元格中显示 " **删除** " 按钮：

- **CommitEditingStyle** -表源检测是否重写了此方法，并自动启用了轻扫到删除手势。 方法的实现应  `DeleteRows` 在上调用  `UITableView` 以使单元格消失，还会从模型中删除基础数据 (例如，数组、字典或数据库) 。 
- **CanEditRow** –如果重写 CommitEditingStyle，则假定所有行都是可编辑的。 如果实现此方法并为某些特定行返回 false (，或为所有) 行返回 false，则将无法在该单元格中使用向下轻扫手势。 
- **TitleForDeleteConfirmation** –（可选）指定 "  **删除** " 按钮的文本。 如果未实现此方法，则按钮文本将为 "Delete"。 

这些方法在类中实现，如下所示 `TableSource` ：

```csharp
public override void CommitEditingStyle (UITableView tableView, UITableViewCellEditingStyle editingStyle, Foundation.NSIndexPath indexPath)
{
    switch (editingStyle) {
        case UITableViewCellEditingStyle.Delete:
            // remove the item from the underlying data source
            tableItems.RemoveAt(indexPath.Row);
            // delete the row from the table
            tableView.DeleteRows (new NSIndexPath[] { indexPath }, UITableViewRowAnimation.Fade);
            break;
        case UITableViewCellEditingStyle.None:
            Console.WriteLine ("CommitEditingStyle:None called");
            break;
    }
}
public override bool CanEditRow (UITableView tableView, NSIndexPath indexPath)
{
    return true; // return false if you wish to disable editing for a specific indexPath or for all rows
}
public override string TitleForDeleteConfirmation (UITableView tableView, NSIndexPath indexPath)
{   // Optional - default text is 'Delete'
    return "Trash (" + tableItems[indexPath.Row].SubHeading + ")";
}
```

在此示例中，已 `UITableViewSource` 更新为使用 `List<TableItem>` (而不是字符串) 数组作为数据源，因为它支持添加和删除集合中的项。

## <a name="edit-mode"></a>编辑模式

当表处于编辑模式时，用户会在每一行上看到一个红色的 "停止" 小组件，在接触时显示 "删除" 按钮。 该表还会显示一个 "句柄" 图标，以指示可以通过拖动行来更改顺序。
**TableEditMode**示例实现了这些功能，如下所示。

 [![TableEditMode 示例实现了这些功能，如下所示](editing-images/image11.png)](editing-images/image11.png#lightbox)

上有许多不同的方法 `UITableViewSource` 可影响表的编辑模式行为：

- **CanEditRow** –是否可以编辑每一行。 返回 false 可防止在编辑模式下进行轻扫到删除和删除操作。 
- **CanMoveRow** –返回 true 以启用移动 "handle" 或 false 以阻止移动。 
- **EditingStyleForRow** -当表处于编辑模式时，此方法的返回值将确定该单元格是否显示 "红色删除" 图标或 "绿色添加" 图标。 `UITableViewCellEditingStyle.None`如果行不应为可编辑，则返回。 
- **MoveRow** –移动行时调用，以便可以修改基础数据结构以匹配表中显示的数据。 

前三个方法的实现是相对直接的–除非您希望使用 `indexPath` 来更改特定行的行为，而只是将整个表的返回值硬编码。

```csharp
public override bool CanEditRow (UITableView tableView, NSIndexPath indexPath)
{
    return true; // return false if you wish to disable editing for a specific indexPath or for all rows
}
public override bool CanMoveRow (UITableView tableView, NSIndexPath indexPath)
{
    return true; // return false if you don't allow re-ordering
}
public override UITableViewCellEditingStyle EditingStyleForRow (UITableView tableView, NSIndexPath indexPath)
{
    return UITableViewCellEditingStyle.Delete; // this example doesn't support Insert
}
```

`MoveRow`实现稍微复杂一些，因为它需要更改基础数据结构以匹配新的顺序。 由于数据是作为 `List` 以下代码实现的：删除其旧位置的数据项，然后将其插入到新位置。 如果数据存储在具有 "order" 列 (例如) 的 SQLite 数据库表中，则此方法将改为需要执行某些 SQL 操作，以便对该列中的数字重新排序。

```csharp
public override void MoveRow (UITableView tableView, NSIndexPath sourceIndexPath, NSIndexPath destinationIndexPath)
{
    var item = tableItems[sourceIndexPath.Row];
    var deleteAt = sourceIndexPath.Row;
    var insertAt = destinationIndexPath.Row;
    
    // are we inserting 
    if (destinationIndexPath.Row < sourceIndexPath.Row) {
        // add one to where we delete, because we're increasing the index by inserting
        deleteAt += 1;
    } else {
        // add one to where we insert, because we haven't deleted the original yet
        insertAt += 1;
    }
    tableItems.Insert (insertAt, item);
    tableItems.RemoveAt (deleteAt);
}
```

最后，若要使表进入编辑模式，需要按如下所示调用 "**编辑**" 按钮 `SetEditing`

```csharp
table.SetEditing (true, true);
```

完成用户编辑后，" **完成** " 按钮应关闭编辑模式：

```csharp
table.SetEditing (false, true);
```

## <a name="row-insertion-editing-style"></a>行插入编辑样式

表中的行插入是一种不常见的用户界面–标准 iOS 应用中的主要示例是 " **编辑联系人** " 屏幕。 此屏幕截图显示行插入功能的工作方式–在编辑模式下，单击) 将其他行插入到数据中时，会 (额外的行。 完成编辑后，将删除临时 ** (添加新) ** 行。

 [![完成编辑后，将删除临时添加新行](editing-images/image12.png)](editing-images/image12.png#lightbox)

上有许多不同的方法 `UITableViewSource` 可影响表的编辑模式行为。 下面的示例代码中实现了这些方法：

- **EditingStyleForRow** –  `UITableViewCellEditingStyle.Delete` 为包含数据的行返回，并  `UITableViewCellEditingStyle.Insert` 为最后一行 (返回，这将专门添加到) 的 "插入" 按钮。 
- **CustomizeMoveTarget** -当用户正在移动某个单元时，此可选方法的返回值可以覆盖其位置选择。 这意味着可以防止它们 "删除" 特定位置的单元格（例如，在  ** (添加新) ** 行后阻止移动任何行的此示例）。 
- **CanMoveRow** –返回 true 以启用移动 "handle" 或 false 以阻止移动。 在此示例中，最后一行会隐藏移动 "句柄"，因为它仅用于作为 "插入" 按钮。 

我们还添加了两个自定义方法来添加 "insert" 行，然后在不再需要时再次将其删除。 它们是从 " **编辑** " 和 " **完成** " 按钮调用的：

- **WillBeginTableEditing** –当 "  **编辑** " 按钮触摸时，它将调用  `SetEditing` 以在编辑模式下放置表。 这会触发 WillBeginTableEditing 方法，在此方法中，我们将显示 (在表的末尾  ** 添加新的) ** 行以作为 "插入按钮"。 
- **DidFinishTableEditing** –再次调用 "完成" 按钮  `SetEditing` 以关闭编辑模式。 示例代码删除了 "不再需要编辑时，从表中  ** 添加新) 行 (** 。 

示例文件 **TableEditModeAdd/Code/TableSource**中实现了这些方法重写：

```csharp
public override UITableViewCellEditingStyle EditingStyleForRow (UITableView tableView, NSIndexPath indexPath)
{
    if (tableView.Editing) {
        if (indexPath.Row == tableView.NumberOfRowsInSection (0) - 1)
            return UITableViewCellEditingStyle.Insert;
        else
            return UITableViewCellEditingStyle.Delete;
    } else // not in editing mode, enable swipe-to-delete for all rows
        return UITableViewCellEditingStyle.Delete;
}
public override NSIndexPath CustomizeMoveTarget (UITableView tableView, NSIndexPath sourceIndexPath, NSIndexPath proposedIndexPath)
{
    var numRows = tableView.NumberOfRowsInSection (0) - 1; // less the (add new) one
    if (proposedIndexPath.Row >= numRows)
        return NSIndexPath.FromRowSection(numRows - 1, 0);
    else
        return proposedIndexPath;
} 
public override bool CanMoveRow (UITableView tableView, NSIndexPath indexPath)
{
    return indexPath.Row < tableView.NumberOfRowsInSection (0) - 1;
}
```

当启用或禁用表的编辑模式时，这两种自定义方法用于添加和删除 ** (添加新) ** 行：

```csharp
public void WillBeginTableEditing (UITableView tableView)
{
    tableView.BeginUpdates ();
    // insert the 'ADD NEW' row at the end of table display
    tableView.InsertRows (new NSIndexPath[] { 
            NSIndexPath.FromRowSection (tableView.NumberOfRowsInSection (0), 0) 
        }, UITableViewRowAnimation.Fade);
    // create a new item and add it to our underlying data (it is not intended to be permanent)
    tableItems.Add (new TableItem ("(add new)"));
    tableView.EndUpdates (); // applies the changes
}
public void DidFinishTableEditing (UITableView tableView)
{
    tableView.BeginUpdates ();
    // remove our 'ADD NEW' row from the underlying data
    tableItems.RemoveAt ((int)tableView.NumberOfRowsInSection (0) - 1); // zero based :)
    // remove the row from the table display
    tableView.DeleteRows (new NSIndexPath[] { NSIndexPath.FromRowSection (tableView.NumberOfRowsInSection (0) - 1, 0) }, UITableViewRowAnimation.Fade);
    tableView.EndUpdates (); // applies the changes
}
```

最后，此代码将实例化 " **编辑** " 和 " **完成** " 按钮，并在接触时启用或禁用编辑模式：

```csharp
done = new UIBarButtonItem(UIBarButtonSystemItem.Done, (s,e)=>{
    table.SetEditing (false, true);
    NavigationItem.RightBarButtonItem = edit;
    tableSource.DidFinishTableEditing(table);
});
edit = new UIBarButtonItem(UIBarButtonSystemItem.Edit, (s,e)=>{
    if (table.Editing)
        table.SetEditing (false, true); // if we've half-swiped a row
    tableSource.WillBeginTableEditing(table);
    table.SetEditing (true, true);
    NavigationItem.LeftBarButtonItem = null;
    NavigationItem.RightBarButtonItem = done;
});
```

不经常使用此行插入 UI 模式，不过，您也可以使用 `UITableView.BeginUpdates` 和 `EndUpdates` 方法对任何表中的单元格的插入或删除操作进行动画处理。 使用这些方法的规则是在 `RowsInSection` 和调用之间返回的值之间的差异 `BeginUpdates` 必须与 `EndUpdates` 使用和方法添加/删除的单元格数匹配 `InsertRows` `DeleteRows` 。 如果基础数据源未更改为与表视图中的插入/删除相匹配，则会发生错误。

## <a name="related-links"></a>相关链接

- [WorkingWithTables (示例) ](/samples/xamarin/ios-samples/workingwithtables)