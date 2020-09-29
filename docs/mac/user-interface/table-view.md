---
title: Xamarin 中的表视图
description: 本文介绍如何在 Xamarin. Mac 应用程序中使用表视图。 它介绍了如何在 Xcode 中创建表视图，并 Interface Builder 在代码中与它们进行交互。
ms.prod: xamarin
ms.assetid: 3B55B858-4769-4331-966A-7F53B3B7C720
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: d8c5cc10b4bce507f7a1d7896a41730745b08cbd
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431637"
---
# <a name="table-views-in-xamarinmac"></a>Xamarin 中的表视图

_本文介绍如何在 Xamarin. Mac 应用程序中使用表视图。它介绍了如何在 Xcode 中创建表视图，并 Interface Builder 在代码中与它们进行交互。_

在 Xamarin 应用 *程序中使用* c # 和 .net 时，您可以访问与在 *Xcode 和* 中工作的开发人员相同的表视图。 由于 Xamarin 与 Xcode 直接集成，因此可以使用 Xcode 的 _Interface Builder_ 创建和维护表 (视图，也可以选择直接在 c # 代码) 中创建它们。

表格视图以表格格式显示数据，其中包含多个行中的一列或多列信息。 根据所创建的表视图的类型，用户可以按列排序、重新组织列、添加列、删除列或编辑表中包含的数据。

[![示例表](table-view-images/intro01.png)](table-view-images/intro01.png#lightbox)

在本文中，我们将介绍在 Xamarin. Mac 应用程序中使用表视图的基础知识。 强烈建议您先完成 [Hello，Mac](~/mac/get-started/hello-mac.md) 一文，特别是 [Xcode 和 Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) 及 [输出口和操作](~/mac/get-started/hello-mac.md#outlets-and-actions) 部分的简介，因为它涵盖了我们将在本文中使用的重要概念和技巧。

你可能想要查看[Xamarin 内部](~/mac/internals/how-it-works.md)示例文档的 "将[c # 类/方法公开到目标 c](~/mac/internals/how-it-works.md) " 部分， `Register` 并说明用于将 `Export` C # 类连接到目标 C 对象和 UI 元素的和命令。

<a name="Introduction_to_Table_Views"></a>

## <a name="introduction-to-table-views"></a>表视图简介

表格视图以表格格式显示数据，其中包含多个行中的一列或多列信息。 表视图显示在滚动视图 (`NSScrollView`) 并从 macOS 10.7 开始，你可以使用任何 `NSView` (`NSCell`) 的单元格来显示行和列。 也就是说，你仍可以使用 `NSCell` ，但你通常会 `NSTableCellView` 创建自定义行和列的子类。

表视图不存储它自己的数据，而是依赖于数据源 (`NSTableViewDataSource`) 按需提供所需的行和列。

可以通过提供表视图委托的子类来自定义表视图的行为 (`NSTableViewDelegate`) 以支持表列管理、键入以选择功能、行选择和编辑、自定义跟踪以及单独的列和行的自定义视图。

创建表视图时，Apple 建议以下内容：

- 允许用户通过单击列标题对表进行排序。
- 创建用名词或短名词短语描述列中显示的数据的列标题。

有关详细信息，请参阅 Apple [OS X 人体学接口准则](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)的[内容视图](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsView.html#//apple_ref/doc/uid/20000957-CH52-SW1)部分。

<a name="Creating-and-Maintaining-Table-Views-in-Xcode"></a>

## <a name="creating-and-maintaining-table-views-in-xcode"></a>在 Xcode 中创建和维护表视图

创建新的 Xamarin Cocoa 应用程序时，默认情况下会获得一个标准空白窗口。 此窗口在 `.storyboard` 项目中自动包含的文件中定义。 若要编辑 windows 设计，请在 **解决方案资源管理器**中双击该 `Main.storyboard` 文件：

[![选择主情节提要](table-view-images/edit01.png)](table-view-images/edit01.png#lightbox)

这将在 Xcode 的 Interface Builder 中打开窗口设计：

[![在 Xcode 中编辑 UI](table-view-images/edit02.png)](table-view-images/edit02.png#lightbox)

`table`在**库检查器的**搜索框中键入，以便更轻松地查找表视图控件：

[![从库中选择表视图](table-view-images/edit03.png)](table-view-images/edit03.png#lightbox)

将表视图拖到 " **界面编辑器**" 中的视图控制器上，使其填充视图控制器的内容区域，并将其设置为在 " **约束编辑器**" 中的窗口缩小和增长的位置：

[![编辑约束](table-view-images/edit04.png)](table-view-images/edit04.png#lightbox)

选择 **接口层次结构** 中的表视图， **属性检查器**中提供以下属性：

[![特性检查器](table-view-images/edit05.png)](table-view-images/edit05.png#lightbox)

- **内容模式** -允许使用视图 (`NSView`) 或单元格 (`NSCell`) 以显示行和列中的数据。 从 macOS 10.7 开始，应使用视图。
- **浮点组行** -如果 `true` 为，则表视图将绘制分组的单元格，就好像它们是浮动的。
- **列** -定义显示的列数。
- **标头** -如果 `true` 为，则这些列将具有标头。
- 重新**排序**-如果为 `true` ，则用户将能够拖放表中的列。
- **调整大小** -如果为 `true` ，则用户将能够拖动列标题以调整列的大小。
- **列大小调整** -控制表自动调整列大小的方式。
- **突出显示** -控制在选择单元格时表使用的突出显示类型。
- **替换行** -如果 `true` 其他行都有不同的背景色。
- **水平网格** -选择在单元格之间水平绘制的边框类型。
- **垂直网格** -选择在单元格之间垂直绘制的边框类型。
- **网格颜色** -设置单元格边框颜色。
- **背景** -设置单元格背景色。
- **选择** -允许你控制用户如何选择表中的单元格，如下所示：
  - **多** -如果 `true` 为，则用户可以选择多个行和列。
  - **列** -如果 `true` 为，则用户可以选择列。
  - **键入 select** -如果为 `true` ，则用户可以键入字符来选择行。
  - **空** -如果为 `true` ，则不要求用户选择行或列，表根本不允许任何选择。
- **自动保存-表** 格式将自动保存在下面的名称。
- **列信息** -如果 `true` ，列的顺序和宽度将自动保存。
- **换行符** -选择单元格如何处理分行符。
- **截断最后一个可见行** -如果为 `true` ，则数据中的单元格将被截断，而不能容纳在它的边界内。

> [!IMPORTANT]
> 除非您维护的是旧的 Xamarin Mac 应用程序，否则，基于表视图 `NSView` 应使用基于的表视图 `NSCell` 。 `NSCell` 被视为旧的，可能不会受到支持。

选择 **接口层次结构** 中的表列，" **属性检查器**" 中提供以下属性：

[![特性检查器](table-view-images/edit06.png)](table-view-images/edit06.png#lightbox)

- **标题** -设置列的标题。
- **对齐方式** -设置单元格中的文本对齐方式。
- **标题字体** -选择单元格标题文本的字体。
- **排序键** -用于对列中的数据进行排序的键。 如果用户不能对此列进行排序，则保留为空。
- **选择器** -用于执行排序的 **操作** 。 如果用户不能对此列进行排序，则保留为空。
- **Order** -列数据的排序顺序。
- **调整大小** -选择列的调整大小类型。
- **可编辑** -如果 `true` 为，则用户可以在基于单元格的表中编辑单元格。
- **Hidden** -如果为 `true` ，则隐藏列。

还可以通过将列的控点 (垂直方向拖动到左侧或右侧) ，来调整列的大小。

让我们选择表视图中的每一列，并为第一列指定标题，并为第二列指定 **标题** `Product` `Details` 。

在 `NSTableViewCell` **接口层次结构** 中选择一个表单元格视图 () ， **特性检查器**中提供以下属性：

[![特性检查器](table-view-images/edit07.png)](table-view-images/edit07.png#lightbox)

这些是标准视图的所有属性。 你还可以选择在此处调整此列的行的大小。

选择表视图单元 (默认情况下，这是 `NSTextField` **接口层次结构** 中的) ， **特性检查器**中提供了以下属性：

[![特性检查器](table-view-images/edit08.png)](table-view-images/edit08.png#lightbox)

你将在此处设置标准文本字段的所有属性。 默认情况下，标准文本字段用于显示列中单元格的数据。

在 `NSTableFieldCell` **接口层次结构** 中选择一个表单元格视图 () ， **特性检查器**中提供以下属性：

[![特性检查器](table-view-images/edit09.png)](table-view-images/edit09.png#lightbox)

最重要的设置如下：

- **布局** -选择此列中的单元格的布局方式。
- **使用单行模式** -如果为 `true` ，则单元格限制为单个行。
- **第一种运行时布局宽度** -如果 `true` 为，则单元格将更喜欢在首次运行应用程序时，手动或自动)  (设置的宽度。
- **操作** -控制向单元格发送编辑 **操作** 的时间。
- **行为** -定义单元是否可选择或可编辑。
- **格式文本** -如果 `true` 为，则单元格可以显示已设置格式的文本。
- **Undo** -如果为 `true` ，则单元格会对其撤消行为承担责任。

选择 "表单元" 视图 (在 `NSTableFieldCell` **接口层次结构**中表列的底部) ：

[![选择表单元格视图](table-view-images/edit10.png)](table-view-images/edit10.png#lightbox)

这允许您编辑作为给定列创建的所有单元格的基本 _模式_ 的表单元视图。

<a name="Adding_Actions_and_Outlets"></a>

### <a name="adding-actions-and-outlets"></a>添加操作和插座

就像任何其他 Cocoa UI 控件一样，我们需要根据所需的功能) 将表视图及其列和单元格**Actions**公开给 c #**代码 (。**

对于要公开的任何表视图元素，此过程都是相同的：

1. 切换到 " **助手编辑器** " 并确保 `ViewController.h` 已选中此文件： 

    [![助手编辑器](table-view-images/edit11.png)](table-view-images/edit11.png#lightbox)
2. 从 **接口层次结构**中选择表视图，然后单击并拖动到该 `ViewController.h` 文件。
3. 为名为的表视图创建一个 **插座** `ProductTable` ： 

    [![配置插座](table-view-images/edit13.png)](table-view-images/edit13.png#lightbox)
4. 为 tables 列创建 **插座** `ProductColumn` ，并调用 `DetailsColumn` ： 

    [![配置插座](table-view-images/edit14.png)](table-view-images/edit14.png#lightbox)
5. 保存更改并返回到 Visual Studio for Mac 以与 Xcode 同步。

接下来，我们将编写代码，以便在运行应用程序时显示表的一些数据。

<a name="Populating_the_Table_View"></a>

## <a name="populating-the-table-view"></a>填充表视图

使用在 Interface Builder 中设计的表视图并通过 **插座**公开后，接下来需要创建 c # 代码来填充它。

首先，让我们创建一个新 `Product` 类来保存各个行的信息。 在**解决方案资源管理器**中，右键单击项目，然后选择 "**添加**  >  **新文件 ...** "选择 "**常规**  >  " "**空类**"，输入 `Product` 作为**名称**，然后单击 "**新建**" 按钮：

[![创建空类](table-view-images/populate01.png)](table-view-images/populate01.png#lightbox)

使文件如下所 `Product.cs` 示：

```csharp
using System;

namespace MacTables
{
  public class Product
  {
    #region Computed Properties
    public string Title { get; set;} = "";
    public string Description { get; set;} = "";
    #endregion

    #region Constructors
    public Product ()
    {
    }

    public Product (string title, string description)
    {
      this.Title = title;
      this.Description = description;
    }
    #endregion
  }
}

```

接下来，我们需要创建一个子类， `NSTableDataSource` 以便在请求表时为其提供数据。 在**解决方案资源管理器**中，右键单击项目，然后选择 "**添加**  >  **新文件 ...** "选择 "**常规**  >  **空类**"，在 "名称" 中输入， `ProductTableDataSource` 然后单击 "**新建**" 按钮。 **Name**

编辑 `ProductTableDataSource.cs` 文件，使其类似于以下内容：

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;

namespace MacTables
{
  public class ProductTableDataSource : NSTableViewDataSource
  {
    #region Public Variables
    public List<Product> Products = new List<Product>();
    #endregion

    #region Constructors
    public ProductTableDataSource ()
    {
    }
    #endregion

    #region Override Methods
    public override nint GetRowCount (NSTableView tableView)
    {
      return Products.Count;
    }
    #endregion
  }
}

```

此类具有表视图项的存储，并重写 `GetRowCount` 以返回表中的行数。

最后，我们需要创建一个子类 `NSTableDelegate` 来提供表的行为。 在**解决方案资源管理器**中，右键单击项目，然后选择 "**添加**  >  **新文件 ...** "选择 "**常规**  >  **空类**"，在 "名称" 中输入， `ProductTableDelegate` 然后单击 "**新建**" 按钮。 **Name**

编辑 `ProductTableDelegate.cs` 文件，使其类似于以下内容：

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;

namespace MacTables
{
  public class ProductTableDelegate: NSTableViewDelegate
  {
    #region Constants 
    private const string CellIdentifier = "ProdCell";
    #endregion

    #region Private Variables
    private ProductTableDataSource DataSource;
    #endregion

    #region Constructors
    public ProductTableDelegate (ProductTableDataSource datasource)
    {
      this.DataSource = datasource;
    }
    #endregion

    #region Override Methods
    public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
    {
      // This pattern allows you reuse existing views when they are no-longer in use.
      // If the returned view is null, you instance up a new view
      // If a non-null view is returned, you modify it enough to reflect the new data
      NSTextField view = (NSTextField)tableView.MakeView (CellIdentifier, this);
      if (view == null) {
        view = new NSTextField ();
        view.Identifier = CellIdentifier;
        view.BackgroundColor = NSColor.Clear;
        view.Bordered = false;
        view.Selectable = false;
        view.Editable = false;
      }

      // Setup view based on the column selected
      switch (tableColumn.Title) {
      case "Product":
        view.StringValue = DataSource.Products [(int)row].Title;
        break;
      case "Details":
        view.StringValue = DataSource.Products [(int)row].Description;
        break;
      }

      return view;
    }
    #endregion
  }
}
```

当我们创建的实例时 `ProductTableDelegate` ，我们还会传入为 `ProductTableDataSource` 表提供数据的实例。 `GetViewForItem`方法负责返回一个视图 (数据) 以显示给定列和行的单元格。 如果可能，如果不创建新视图，则将重复使用现有视图来显示该单元。

若要填充表，我们要编辑该 `ViewController.cs` 文件并使该 `AwakeFromNib` 方法如下所示：

```csharp
public override void AwakeFromNib ()
{
  base.AwakeFromNib ();

  // Create the Product Table Data Source and populate it
  var DataSource = new ProductTableDataSource ();
  DataSource.Products.Add (new Product ("Xamarin.iOS", "Allows you to develop native iOS Applications in C#"));
  DataSource.Products.Add (new Product ("Xamarin.Android", "Allows you to develop native Android Applications in C#"));
  DataSource.Products.Add (new Product ("Xamarin.Mac", "Allows you to develop Mac native Applications in C#"));

  // Populate the Product Table
  ProductTable.DataSource = DataSource;
  ProductTable.Delegate = new ProductTableDelegate (DataSource);
}
```

如果运行该应用程序，将显示以下内容：

[![示例应用运行](table-view-images/populate02.png)](table-view-images/populate02.png#lightbox)

<a name="Sorting_by_Column"></a>

## <a name="sorting-by-column"></a>按列排序

允许用户通过单击列标题来对表中的数据进行排序。 首先，双击该 `Main.storyboard` 文件以将其打开，以便在 Interface Builder 中进行编辑。 选择 " `Product` 列"，为 `Title` "**排序关键字**" 输入，为选择器输入， `compare:` 并为**Selector** `Ascending` **顺序**选择：

[![设置排序关键字](table-view-images/sort01.png)](table-view-images/sort01.png#lightbox)

选择 " `Details` 列"，为 `Description` "**排序关键字**" 输入，为选择器输入， `compare:` 并为**Selector** `Ascending` **顺序**选择：

[![设置排序关键字](table-view-images/sort02.png)](table-view-images/sort02.png#lightbox)

保存更改并返回到 Visual Studio for Mac 以与 Xcode 同步。

现在，让我们编辑 `ProductTableDataSource.cs` 文件并添加以下方法：

```csharp
public void Sort(string key, bool ascending) {

  // Take action based on key
  switch (key) {
  case "Title":
    if (ascending) {
      Products.Sort ((x, y) => x.Title.CompareTo (y.Title));
    } else {
      Products.Sort ((x, y) => -1 * x.Title.CompareTo (y.Title));
    }
    break;
  case "Description":
    if (ascending) {
      Products.Sort ((x, y) => x.Description.CompareTo (y.Description));
    } else {
      Products.Sort ((x, y) => -1 * x.Description.CompareTo (y.Description));
    }
    break;
  }

}

public override void SortDescriptorsChanged (NSTableView tableView, NSSortDescriptor[] oldDescriptors)
{
  // Sort the data
  if (oldDescriptors.Length > 0) {
    // Update sort
    Sort (oldDescriptors [0].Key, oldDescriptors [0].Ascending);
  } else {
    // Grab current descriptors and update sort
    NSSortDescriptor[] tbSort = tableView.SortDescriptors; 
    Sort (tbSort[0].Key, tbSort[0].Ascending); 
  }
      
  // Refresh table
  tableView.ReloadData ();
}
```

使用 `Sort` 方法，我们可以基于给定的 `Product` 类字段按升序或降序对数据源中的数据进行排序。 `SortDescriptorsChanged`每次单击列标题时，将调用重写的方法。 它将被传递到 Interface Builder 中设置的 **键值** 和该列的排序顺序。

如果运行应用程序，并单击列标题中的行，则将按该列对行进行排序：

[![示例应用运行](table-view-images/sort03.png)](table-view-images/sort03.png#lightbox)

<a name="Row_Selection"></a>

## <a name="row-selection"></a>行选择

如果要允许用户选择单个行，请双击该 `Main.storyboard` 文件以将其打开，以便在 Interface Builder 中进行编辑。 选择**接口层次结构**中的表视图，并取消选中 "**属性检查器**" 中的 "**多个**" 复选框：

[![特性检查器](table-view-images/select01.png)](table-view-images/select01.png#lightbox)

保存更改并返回到 Visual Studio for Mac 以与 Xcode 同步。

接下来，编辑 `ProductTableDelegate.cs` 文件并添加以下方法：

```csharp
public override bool ShouldSelectRow (NSTableView tableView, nint row)
{
  return true;
}
```

这将允许用户在表视图中选择任何单个行。 对于 `false` `ShouldSelectRow` 您不希望用户可以选择的任何行， `false` 如果您不希望用户能够选择任何行，则返回。

) 的表视图 (`NSTableView` 包含以下用于处理行选择的方法：

- `DeselectRow(nint)` -取消选择表中给定的行。
- `SelectRow(nint,bool)` -选择给定行。 `false`为第二个参数传递，一次只能选择一行。
- `SelectedRow` -返回在表中选择的当前行。
- `IsRowSelected(nint)` - `true` 如果选择了给定行，则返回。

<a name="Multiple_Row_Selection"></a>

## <a name="multiple-row-selection"></a>多行选择

如果要允许用户选择多个行，请双击该 `Main.storyboard` 文件以将其打开，以便在 Interface Builder 中进行编辑。 选择**接口层次结构**中的表视图，并选中 "**属性检查器**" 中的 "**多个**" 复选框：

[![特性检查器](table-view-images/select02.png)](table-view-images/select02.png#lightbox)

保存更改并返回到 Visual Studio for Mac 以与 Xcode 同步。

接下来，编辑 `ProductTableDelegate.cs` 文件并添加以下方法：

```csharp
public override bool ShouldSelectRow (NSTableView tableView, nint row)
{
  return true;
}
```

这将允许用户在表视图中选择任何单个行。 对于 `false` `ShouldSelectRow` 您不希望用户可以选择的任何行， `false` 如果您不希望用户能够选择任何行，则返回。

) 的表视图 (`NSTableView` 包含以下用于处理行选择的方法：

- `DeselectAll(NSObject)` -取消选择表中的所有行。 用于 `this` 在执行选择的对象中发送的第一个参数。 
- `DeselectRow(nint)` -取消选择表中给定的行。
- `SelectAll(NSobject)` -选择表中的所有行。 用于 `this` 在执行选择的对象中发送的第一个参数。
- `SelectRow(nint,bool)` -选择给定行。 `false`为第二个参数传递清除所选内容并仅选择单个行，传递 `true` 以扩展选定内容并包括此行。
- `SelectRows(NSIndexSet,bool)` -选择给定的行集。 如果 `false` 为第二个参数传递，则清除所选内容并仅选择这些行，传递 `true` 以扩展选定内容并包含这些行。
- `SelectedRow` -返回在表中选择的当前行。
- `SelectedRows` -返回， `NSIndexSet` 其中包含所选行的索引。
- `SelectedRowCount` -返回选定行的数目。
- `IsRowSelected(nint)` - `true` 如果选择了给定行，则返回。

<a name="Type_to_Select_Row"></a>

## <a name="type-to-select-row"></a>键入以选择行

如果要允许用户在选中表视图的情况下键入字符并选择包含该字符的第一行，请双击该 `Main.storyboard` 文件以将其打开，以便在 Interface Builder 中进行编辑。 选择**接口层次结构**中的表视图，并选中 "**属性检查器**" 中的 "**类型选择**" 复选框：

[![设置选择类型](table-view-images/type01.png)](table-view-images/type01.png#lightbox)

保存更改并返回到 Visual Studio for Mac 以与 Xcode 同步。

现在，让我们编辑 `ProductTableDelegate.cs` 文件并添加以下方法：

```csharp
public override nint GetNextTypeSelectMatch (NSTableView tableView, nint startRow, nint endRow, string searchString)
{
  nint row = 0;
  foreach(Product product in DataSource.Products) {
    if (product.Title.Contains(searchString)) return row;

    // Increment row counter
    ++row;
  }

  // If not found select the first row
  return 0;
}
```

`GetNextTypeSelectMatch`方法采用给定的 `searchString` ，并返回第一个 `Product` 中包含该字符串的行 `Title` 。

如果运行应用程序并键入一个字符，则将选择一行：

[![示例应用运行](table-view-images/type02.png)](table-view-images/type02.png#lightbox)

<a name="Reordering_Columns"></a>

## <a name="reordering-columns"></a>重新排序列

如果要允许用户在表视图中拖动重新排序列，请双击该 `Main.storyboard` 文件以将其打开，以便在 Interface Builder 中进行编辑。 选择**接口层次结构**中的表视图，并选中 "**属性检查器**" 中的 "重新**排序**" 复选框：

[![特性检查器](table-view-images/reorder01.png)](table-view-images/reorder01.png#lightbox)

如果我们为 " **自动保存** " 属性提供一个值，并选中 " **列信息** " 字段，则对该表的布局所做的任何更改都将自动保存，并在下次运行应用程序时还原。

保存更改并返回到 Visual Studio for Mac 以与 Xcode 同步。

现在，让我们编辑 `ProductTableDelegate.cs` 文件并添加以下方法：

```csharp
public override bool ShouldReorder (NSTableView tableView, nint columnIndex, nint newColumnIndex)
{
  return true;
}
```

`ShouldReorder` `true` 对于要允许将其拖动到中的任何列，方法应返回 `newColumnIndex` ，否则返回 `false` ;

如果运行应用程序，我们可以拖动列标题以对列进行重新排序：

[![重新排序的列的示例](table-view-images/reorder02.png)](table-view-images/reorder02.png#lightbox)

<a name="Editing_Cells"></a>

## <a name="editing-cells"></a>编辑单元格

如果希望允许用户编辑给定单元的值，请编辑 `ProductTableDelegate.cs` 文件，并 `GetViewForItem` 按如下所示更改方法：

```csharp
public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
{
  // This pattern allows you reuse existing views when they are no-longer in use.
  // If the returned view is null, you instance up a new view
  // If a non-null view is returned, you modify it enough to reflect the new data
  NSTextField view = (NSTextField)tableView.MakeView (tableColumn.Title, this);
  if (view == null) {
    view = new NSTextField ();
    view.Identifier = tableColumn.Title;
    view.BackgroundColor = NSColor.Clear;
    view.Bordered = false;
    view.Selectable = false;
    view.Editable = true;

    view.EditingEnded += (sender, e) => {
          
      // Take action based on type
      switch(view.Identifier) {
      case "Product":
        DataSource.Products [(int)view.Tag].Title = view.StringValue;
        break;
      case "Details":
        DataSource.Products [(int)view.Tag].Description = view.StringValue;
        break; 
      }
    };
  }

  // Tag view
  view.Tag = row;

  // Setup view based on the column selected
  switch (tableColumn.Title) {
  case "Product":
    view.StringValue = DataSource.Products [(int)row].Title;
    break;
  case "Details":
    view.StringValue = DataSource.Products [(int)row].Description;
    break;
  }

  return view;
}
```

现在，如果我们运行应用程序，则用户可以在表视图中编辑单元：

[![编辑单元格的示例](table-view-images/editing01.png)](table-view-images/editing01.png#lightbox)

<a name="Using_Images_in_Table_Views"></a>

## <a name="using-images-in-table-views"></a>在表视图中使用图像

若要在中包含图像作为单元的一部分 `NSTableView` ，您需要更改表视图的方法返回数据的方式 `NSTableViewDelegate's` `GetViewForItem` 以使用 `NSTableCellView` 而非典型的 `NSTextField` 。 例如：

```csharp
public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
{

  // This pattern allows you reuse existing views when they are no-longer in use.
  // If the returned view is null, you instance up a new view
  // If a non-null view is returned, you modify it enough to reflect the new data
  NSTableCellView view = (NSTableCellView)tableView.MakeView (tableColumn.Title, this);
  if (view == null) {
    view = new NSTableCellView ();
    if (tableColumn.Title == "Product") {
      view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
      view.AddSubview (view.ImageView);
      view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
    } else {
      view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
    }
    view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
    view.AddSubview (view.TextField);
    view.Identifier = tableColumn.Title;
    view.TextField.BackgroundColor = NSColor.Clear;
    view.TextField.Bordered = false;
    view.TextField.Selectable = false;
    view.TextField.Editable = true;

    view.TextField.EditingEnded += (sender, e) => {

      // Take action based on type
      switch(view.Identifier) {
      case "Product":
        DataSource.Products [(int)view.TextField.Tag].Title = view.TextField.StringValue;
        break;
      case "Details":
        DataSource.Products [(int)view.TextField.Tag].Description = view.TextField.StringValue;
        break; 
      }
    };
  }

  // Tag view
  view.TextField.Tag = row;

  // Setup view based on the column selected
  switch (tableColumn.Title) {
  case "Product":
    view.ImageView.Image = NSImage.ImageNamed ("tags.png");
    view.TextField.StringValue = DataSource.Products [(int)row].Title;
    break;
  case "Details":
    view.TextField.StringValue = DataSource.Products [(int)row].Description;
    break;
  }

  return view;
}
```

有关详细信息，请参阅使用[图像](~/mac/app-fundamentals/image.md)文档中的[使用映像和表视图](~/mac/app-fundamentals/image.md)部分。

<a name="Adding-a-Delete-Button-to-a-Row"></a>

## <a name="adding-a-delete-button-to-a-row"></a>向行中添加 "删除" 按钮

根据应用程序的要求，在某些情况下，您可能需要为表中的每一行提供一个操作按钮。 作为此示例的示例，让我们展开上面创建的表视图示例，在每一行上包含一个 " **删除** " 按钮。

首先，编辑 `Main.storyboard` Xcode 的 Interface Builder 中的，选择表视图，并将列数增加到3个 (3) 。 接下来，将新列的 **标题** 更改为 `Action` ：

[![编辑列名称](table-view-images/delete01.png)](table-view-images/delete01.png#lightbox)

保存对情节提要所做的更改并返回到 Visual Studio for Mac 以同步更改。

接下来，编辑 `ViewController.cs` 文件并添加以下公共方法：

```csharp
public void ReloadTable ()
{
  ProductTable.ReloadData ();
}
```

在同一文件中，按如下所示修改在方法内创建新的表视图委托 `ViewDidLoad` ：

```csharp
// Populate the Product Table
ProductTable.DataSource = DataSource;
ProductTable.Delegate = new ProductTableDelegate (this, DataSource);
```

现在，编辑该 `ProductTableDelegate.cs` 文件以包含与视图控制器的专用连接，并在创建新的委托实例时使控制器作为参数：

```csharp
#region Private Variables
private ProductTableDataSource DataSource;
private ViewController Controller;
#endregion

#region Constructors
public ProductTableDelegate (ViewController controller, ProductTableDataSource datasource)
{
  this.Controller = controller;
  this.DataSource = datasource;
}
#endregion
```

接下来，将以下新的私有方法添加到类：

```csharp
private void ConfigureTextField (NSTableCellView view, nint row)
{
  // Add to view
  view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
  view.AddSubview (view.TextField);

  // Configure
  view.TextField.BackgroundColor = NSColor.Clear;
  view.TextField.Bordered = false;
  view.TextField.Selectable = false;
  view.TextField.Editable = true;

  // Wireup events
  view.TextField.EditingEnded += (sender, e) => {

    // Take action based on type
    switch (view.Identifier) {
    case "Product":
      DataSource.Products [(int)view.TextField.Tag].Title = view.TextField.StringValue;
      break;
    case "Details":
      DataSource.Products [(int)view.TextField.Tag].Description = view.TextField.StringValue;
      break;
    }
  };

  // Tag view
  view.TextField.Tag = row;
}
```

这将使用之前在方法中完成的所有文本视图配置 `GetViewForItem` ，并将其放在一个可调用位置 (，因为表的最后一列不包含文本视图，而按钮) 。

最后，编辑 `GetViewForItem` 方法并使其类似于以下内容：

```csharp
public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
{

  // This pattern allows you reuse existing views when they are no-longer in use.
  // If the returned view is null, you instance up a new view
  // If a non-null view is returned, you modify it enough to reflect the new data
  NSTableCellView view = (NSTableCellView)tableView.MakeView (tableColumn.Title, this);
  if (view == null) {
    view = new NSTableCellView ();

    // Configure the view
    view.Identifier = tableColumn.Title;

    // Take action based on title
    switch (tableColumn.Title) {
    case "Product":
      view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
      view.AddSubview (view.ImageView);
      view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
      ConfigureTextField (view, row);
      break;
    case "Details":
      view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
      ConfigureTextField (view, row);
      break;
    case "Action":
      // Create new button
      var button = new NSButton (new CGRect (0, 0, 81, 16));
      button.SetButtonType (NSButtonType.MomentaryPushIn);
      button.Title = "Delete";
      button.Tag = row;

      // Wireup events
      button.Activated += (sender, e) => {
        // Get button and product
        var btn = sender as NSButton;
        var product = DataSource.Products [(int)btn.Tag];

        // Configure alert
        var alert = new NSAlert () {
          AlertStyle = NSAlertStyle.Informational,
          InformativeText = $"Are you sure you want to delete {product.Title}? This operation cannot be undone.",
          MessageText = $"Delete {product.Title}?",
        };
        alert.AddButton ("Cancel");
        alert.AddButton ("Delete");
        alert.BeginSheetForResponse (Controller.View.Window, (result) => {
          // Should we delete the requested row?
          if (result == 1001) {
            // Remove the given row from the dataset
            DataSource.Products.RemoveAt((int)btn.Tag);
            Controller.ReloadTable ();
          }
        });
      };

      // Add to view
      view.AddSubview (button);
      break;
    }

  }

  // Setup view based on the column selected
  switch (tableColumn.Title) {
  case "Product":
    view.ImageView.Image = NSImage.ImageNamed ("tag.png");
    view.TextField.StringValue = DataSource.Products [(int)row].Title;
    view.TextField.Tag = row;
    break;
  case "Details":
    view.TextField.StringValue = DataSource.Products [(int)row].Description;
    view.TextField.Tag = row;
    break;
  case "Action":
    foreach (NSView subview in view.Subviews) {
      var btn = subview as NSButton;
      if (btn != null) {
        btn.Tag = row;
      }
    }
    break;
  }

  return view;
}
```

让我们更详细地了解此代码的多个部分。 首先，如果正在创建新的，则 `NSTableViewCell` 会基于列名称执行操作。 对于)  (**产品** 和 **详细信息** 的前两列，将 `ConfigureTextField` 调用新方法。

对于 " **操作** " 列，将创建一个新的，并将其 `NSButton` 作为子视图添加到单元格中：

```csharp
// Create new button
var button = new NSButton (new CGRect (0, 0, 81, 16));
button.SetButtonType (NSButtonType.MomentaryPushIn);
button.Title = "Delete";
button.Tag = row;
...

// Add to view
view.AddSubview (button);
```

按钮的 `Tag` 属性用于保存当前正在处理的行的编号。 以后当用户请求在按钮事件中删除某行时，将使用此数字 `Activated` ：

```csharp
// Wireup events
button.Activated += (sender, e) => {
  // Get button and product
  var btn = sender as NSButton;
  var product = DataSource.Products [(int)btn.Tag];

  // Configure alert
  var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = $"Are you sure you want to delete {product.Title}? This operation cannot be undone.",
    MessageText = $"Delete {product.Title}?",
  };
  alert.AddButton ("Cancel");
  alert.AddButton ("Delete");
  alert.BeginSheetForResponse (Controller.View.Window, (result) => {
    // Should we delete the requested row?
    if (result == 1001) {
      // Remove the given row from the dataset
      DataSource.Products.RemoveAt((int)btn.Tag);
      Controller.ReloadTable ();
    }
  });
};
```

在事件处理程序开始时，将获得给定表行上的按钮和产品。 然后向用户显示警报，确认行删除。 如果用户选择删除该行，则从数据源中删除给定行，并重新加载该表：

```csharp
// Remove the given row from the dataset
DataSource.Products.RemoveAt((int)btn.Tag);
Controller.ReloadTable ();
```

最后，如果正在重用表视图单元，而不是创建新的，则以下代码将基于正在处理的列对其进行配置：

```csharp
// Setup view based on the column selected
switch (tableColumn.Title) {
case "Product":
  view.ImageView.Image = NSImage.ImageNamed ("tag.png");
  view.TextField.StringValue = DataSource.Products [(int)row].Title;
  view.TextField.Tag = row;
  break;
case "Details":
  view.TextField.StringValue = DataSource.Products [(int)row].Description;
  view.TextField.Tag = row;
  break;
case "Action":
  foreach (NSView subview in view.Subviews) {
    var btn = subview as NSButton;
    if (btn != null) {
      btn.Tag = row;
    }
  }
  break;
}

```

对于 " **操作** " 列，将扫描所有子视图 `NSButton` ，直到找到为止，然后将其 `Tag` 属性更新为指向当前行。

进行这些更改后，在运行应用时，每行都有一个 " **删除** " 按钮：

[![带有 "删除" 按钮的表视图](table-view-images/delete02.png)](table-view-images/delete02.png#lightbox)

当用户单击 " **删除** " 按钮时，将显示一个警报，要求他们删除给定行：

[![删除行警报](table-view-images/delete03.png)](table-view-images/delete03.png#lightbox)

如果用户选择 "删除"，则将删除行并重新绘制该表：

[![删除行后的表](table-view-images/delete04.png)](table-view-images/delete04.png#lightbox)

<a name="Data_Binding_Table_Views"></a>

## <a name="data-binding-table-views"></a>数据绑定表视图

通过在 Xamarin 应用程序中使用键/值编码和数据绑定技术，可以极大地减少需要编写和维护的代码量，以填充和处理 UI 元素。 你还可以从你的前端用户界面 (_模型-视图-控制器_) 中进一步分离你的支持数据 (_数据) 模型_，从而更易于维护、更灵活的应用程序设计。

键-值编码 (KVC) 是一种用于间接访问对象属性的机制， (专门设置格式的字符串中的键) 标识属性，而不是通过实例变量或通过 () 的访问器方法来访问这些属性 `get/set` 。 通过在 Xamarin 应用程序中实现符合键值的代码访问器，你可以访问其他 macOS 功能，如 (KVO) 、数据绑定、核心数据、Cocoa 绑定和 scriptability 的键-值观察。

有关详细信息，请参阅[数据绑定和键/值编码](~/mac/app-fundamentals/databinding.md)文档中的[表视图数据绑定](~/mac/app-fundamentals/databinding.md#Table_View_Data_Binding)部分。

<a name="Summary"></a>

## <a name="summary"></a>总结

本文详细介绍了如何在 Xamarin. Mac 应用程序中使用表视图。 我们看到了不同的表视图类型和用法，如何在 Xcode 的 Interface Builder 中创建和维护表视图，以及如何在 c # 代码中使用表视图。

## <a name="related-links"></a>相关链接

- [MacTables (示例) ](/samples/xamarin/mac-samples/mactables)
- [MacImages（示例）](/samples/xamarin/mac-samples/macimages)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [大纲视图](~/mac/user-interface/outline-view.md)
- [源列表](~/mac/user-interface/source-list.md)
- [数据绑定和键值编码](~/mac/app-fundamentals/databinding.md)
- [OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [NSTableView](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSTableView_Class/index.html#//apple_ref/doc/uid/TP40004125)
- [NSTableViewDelegate](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSTableViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40008622)
- [NSTableViewDataSource](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSTableDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40004178)