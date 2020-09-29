---
title: 使用 Xamarin 中的数据填充表
description: 本文档介绍如何在 Xamarin iOS 应用程序中填充包含数据的表。 它讨论了 UITableViewSource、单元重用、添加索引、页眉和页脚。
ms.prod: xamarin
ms.assetid: 6FE64DDF-1029-EB9B-6EEC-1C7DFDFDF3AF
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 819e4f89b1443b2e1154aedfb51007ca38fdca02
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91429914"
---
# <a name="populating-a-table-with-data-in-xamarinios"></a>使用 Xamarin 中的数据填充表

若要将行添加到中， `UITableView` 需要实现 `UITableViewSource` 子类并重写表视图调用以填充自身的方法。

本指南涵盖：

- UITableViewSource 的子类
- 单元格重复使用
- 添加索引
- 添加页眉和页脚

<a name="Subclassing_UITableViewSource"></a>

## <a name="subclassing-uitableviewsource"></a>子类 UITableViewSource

`UITableViewSource`子类分配给每个 `UITableView` 。 表视图查询源类以确定如何呈现自身 (例如，所需的行数和每个行的高度（如果不同于默认) ）。 最重要的是，源提供每个填充了数据的单元格。

要使表显示数据，只需要两个必需的方法：

- **RowsInSection** –返回  [`nint`](~/cross-platform/macios/nativetypes.md) 该表应显示的数据的总行数。
- **GetCell** –  `UITableViewCell` 为传递给方法的相应行索引返回填充了数据的。

BasicTable 示例文件 **TableSource.cs** 的可能实现的最简单实现 `UITableViewSource` 。 在下面的代码片段中可以看到，它接受要在表中显示的字符串数组，并返回包含每个字符串的默认单元格样式：

```csharp
public class TableSource : UITableViewSource {

        string[] TableItems;
        string CellIdentifier = "TableCell";

        public TableSource (string[] items)
        {
            TableItems = items;
        }

        public override nint RowsInSection (UITableView tableview, nint section)
        {
            return TableItems.Length;
        }

        public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
        {
            UITableViewCell cell = tableView.DequeueReusableCell (CellIdentifier);
            string item = TableItems[indexPath.Row];

            //if there are no cells to reuse, create a new one
            if (cell == null)
            { 
                cell = new UITableViewCell (UITableViewCellStyle.Default, CellIdentifier); 
            }

            cell.TextLabel.Text = item;

            return cell;
        }
}
```

`UITableViewSource`可以使用任何数据结构，从简单的字符串数组 (如本示例中所示) 到列表 <> 或其他集合。 方法的实现将 `UITableViewSource` 表与基础数据结构隔离开来。

若要使用此子类，请创建一个字符串数组来构造源，然后将其分配给实例 `UITableView` ：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    table = new UITableView(View.Bounds); // defaults to Plain style
    string[] tableItems = new string[] {"Vegetables","Fruits","Flower Buds","Legumes","Bulbs","Tubers"};
    table.Source = new TableSource(tableItems);
    Add (table);
}
```

生成的表如下所示：

 [![运行的示例表](populating-a-table-with-data-images/image3.png)](populating-a-table-with-data-images/image3.png#lightbox)

大多数表使用户能够通过触摸行来选择它，并执行一些其他操作 (如播放歌曲、调用联系人或显示另一个屏幕) 。 若要实现此目的，需要执行一些操作。 首先，让我们创建一个 AlertController，通过向方法中添加以下内容，在用户单击行时显示一条消息 `RowSelected` ：

```csharp
public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
{
    UIAlertController okAlertController = UIAlertController.Create ("Row Selected", tableItems[indexPath.Row], UIAlertControllerStyle.Alert);
    okAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));
    ...

    tableView.DeselectRow (indexPath, true);
}
```

接下来，创建视图控制器的实例：

```csharp
HomeScreen owner;
```

向 UITableViewSource 类添加一个构造函数，该构造函数将视图控制器作为参数，并将其保存在字段中：

```csharp
public TableSource (string[] items, HomeScreen owner)
{
    ...
    this.owner = owner;

}
```

修改创建 UITableViewSource 类的 ViewDidLoad 方法以传递 `this` 引用：

```csharp
table.Source = new TableSource(tableItems, this);
```

最后，返回到 `RowSelected` 方法， `PresentViewController` 对缓存的字段调用：

```csharp
public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
{
    ...
    owner.PresentViewController (okAlertController, true, null);

    ...
}
```

现在用户可以触摸一行，此时将显示一个警报：

 [![选定的行警报](populating-a-table-with-data-images/image4.png)](populating-a-table-with-data-images/image4.png#lightbox)

## <a name="cell-reuse"></a>单元格重复使用

在此示例中，只有六个项目，因此不需要重新使用单元。 但是，在显示几百行或数千行时， `UITableViewCell` 如果一次只在屏幕上容纳几个对象，则会浪费内存来创建数百或数千个对象。

若要避免这种情况，当单元格从屏幕上消失时，其视图将放置在队列中以供重复使用。 当用户滚动时，表将调用 `GetCell` 以请求显示新视图–若要重用现有单元 (当前未显示) 只需调用 `DequeueReusableCell` 方法。 如果某个单元格可用于重用，则将返回，否则将返回 null，并且你的代码必须创建新的单元格实例。

该示例中的代码片段演示了模式：

```csharp
// request a recycled cell to save memory
UITableViewCell cell = tableView.DequeueReusableCell (cellIdentifier);
// if there are no cells to reuse, create a new one
if (cell == null)
    cell = new UITableViewCell (UITableViewCellStyle.Default, cellIdentifier);
```

`cellIdentifier`为不同类型的单元格有效地创建单独的队列。 在此示例中，所有单元格都具有相同的外观，因此只使用一个硬编码标识符。 如果有不同类型的单元格，则每个单元格都有不同的标识符字符串，它们都是实例化的，并且从重复使用队列请求它们时。

### <a name="cell-reuse-in-ios-6"></a>IOS 6 + 中的单元重用

iOS 6 添加了一种单元重用模式，这与集合视图简介类似。 虽然对于向后兼容仍支持上面所示的现有重用模式，但这种新模式更可取，因为它不再需要对单元格进行 null 检查。

使用新模式，应用程序通过调用 `RegisterClassForCellReuse` 或 `RegisterNibForCellReuse` 在控制器的构造函数中调用，来注册要使用的 cell 类或 xib。 然后，在取消排队方法中的单元格时 `GetCell` ，只需调用 `DequeueReusableCell` 传递您为 cell 类或 xib 和索引路径注册的标识符。

例如，以下代码在 UITableViewController 中注册自定义单元类：

```csharp
public class MyTableViewController : UITableViewController
{
  static NSString MyCellId = new NSString ("MyCellId");

  public MyTableViewController ()
  {
    TableView.RegisterClassForCellReuse (typeof(MyCell), MyCellId);
  }
  ...
}
```

注册 MyCell 类后，可以在的方法中取消对单元的排队， `GetCell` `UITableViewSource` 无需执行额外的 null 检查，如下所示：

```csharp
class MyTableSource : UITableViewSource
{
  public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
  {
    // if cell is not available in reuse pool, iOS will create one automatically
    // no need to do null check and create cell manually
    var cell = (MyCell) tableView.DequeueReusableCell (MyCellId, indexPath);

    // do whatever you need to with cell, such as assigning properties, etc.

    return cell;
  }
}
```

请注意，在使用带有自定义单元类的新的重复使用模式时，需要实现采用的构造函数 `IntPtr` ，如下面的代码段所示，否则，目标 C 将无法构造 cell 类的实例：

```csharp
public class MyCell : UITableViewCell
{
  public MyCell (IntPtr p):base(p)
  {
  }
  ...
}
```

你可以在链接到本文的 **BasicTable** 示例中查看上述主题的示例。

<a name="Adding_an_Index"></a>

## <a name="adding-an-index"></a>添加索引

索引可帮助用户滚动长列表（通常按字母顺序排序），尽管你可以按所需的任何条件进行索引。 **BasicTableIndex**示例从文件中加载更长的项列表，以演示索引。 索引中的每一项对应于表的 "section"。

 [![索引显示](populating-a-table-with-data-images/image5.png)](populating-a-table-with-data-images/image5.png#lightbox)

若要支持 "节"，需要对表后面的数据进行分组，因此，BasicTableIndex 示例 `Dictionary<>` 使用每个项的第一个字母作为字典键，从字符串数组创建一个。

```csharp
indexedTableItems = new Dictionary<string, List<string>>();
foreach (var t in items) {
    if (indexedTableItems.ContainsKey (t[0].ToString ())) {
        indexedTableItems[t[0].ToString ()].Add(t);
    } else {
        indexedTableItems.Add (t[0].ToString (), new List<string>() {t});
    }
}
keys = indexedTableItems.Keys.ToArray ();
```

`UITableViewSource`然后，子类需要添加或修改以下方法来使用 `Dictionary<>` ：

- **NumberOfSections** –此方法是可选的，默认情况下，该表假设有一个部分。 显示索引时，此方法应返回索引中的项数 (例如，如果索引包含英文字母) 的所有字母，则为26。
- **RowsInSection** –返回给定节中的行数。
- **SectionIndexTitles** –返回将用于显示索引的字符串数组。 示例代码返回一个字母数组。

示例文件 **BasicTableIndex/TableSource** 中的更新的方法如下所示：

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    return keys.Length;
}
public override nint RowsInSection (UITableView tableview, nint section)
{
    return indexedTableItems[keys[section]].Count;
}
public override string[] SectionIndexTitles (UITableView tableView)
{
    return keys;
}
```

索引通常只与纯表样式一起使用。

<a name="Adding_Headers_and_Footers"></a>

## <a name="adding-headers-and-footers"></a>添加页眉和页脚

可以使用页眉和页脚直观地对表中的行进行分组。 所需的数据结构非常类似于添加索引–工作非常 `Dictionary<>` 好。 此示例将按住类型对蔬菜分组，而不是使用字母对单元进行分组。
输出如下所示：

 [![示例页眉和页脚](populating-a-table-with-data-images/image6.png)](populating-a-table-with-data-images/image6.png#lightbox)

若要显示页眉和页脚， `UITableViewSource` 子类需要以下附加方法：

- **TitleForHeader** –返回用作标题的文本
- **TitleForFooter** –返回要用作页脚的文本。

示例文件 **BasicTableHeaderFooter/Code/TableSource** 中的更新的方法如下所示：

```csharp
public override string TitleForHeader (UITableView tableView, nint section)
{
    return keys[section];
}
public override string TitleForFooter (UITableView tableView, nint section)
{
    return indexedTableItems[keys[section]].Count + " items";
}
```

使用 `GetViewForHeader` 和 `GetViewForFooter` 方法替代在上，您可以使用 View 对象进一步自定义页眉和页脚的外观 `UITableViewSource` 。

## <a name="related-links"></a>相关链接

- [WorkingWithTables (示例) ](/samples/xamarin/ios-samples/workingwithtables)