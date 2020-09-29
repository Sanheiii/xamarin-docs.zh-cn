---
title: Xamarin 中的大纲视图
description: 本文介绍如何在 Xamarin. Mac 应用程序中使用大纲视图。 它介绍了如何在 Xcode 和 Interface Builder 中创建和维护大纲视图，以及如何以编程方式使用它们。
ms.prod: xamarin
ms.assetid: 043248EE-11DA-4E96-83A3-08824A4F2E01
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: c5120fead9084b50912fb4ea1e8cfa100f4b3bbe
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436328"
---
# <a name="outline-views-in-xamarinmac"></a>Xamarin 中的大纲视图

_本文介绍如何在 Xamarin. Mac 应用程序中使用大纲视图。它介绍了如何在 Xcode 和 Interface Builder 中创建和维护大纲视图，以及如何以编程方式使用它们。_

在 Xamarin 应用 *程序中使用* c # 和 .net 时，可以访问在 *Xcode 和* 中工作的开发人员所使用的相同大纲视图。 由于 Xamarin 与 Xcode 直接集成，因此可以使用 Xcode 的 _Interface Builder_ 创建和维护大纲视图 (或者在 c # 代码) 中直接创建它们。

大纲视图是一种允许用户展开或折叠分层数据行的表。 与表视图类似，大纲视图显示一组相关项的数据，行表示各个项，列表示这些项的属性。 与表视图不同，大纲视图中的项不在简单列表中，它们在层次结构中进行组织，如硬盘驱动器上的文件和文件夹。

[![示例应用运行](outline-view-images/populate03.png)](outline-view-images/populate03.png#lightbox)

在本文中，我们将介绍在 Xamarin. Mac 应用程序中使用大纲视图的基础知识。 强烈建议您先完成 [Hello，Mac](~/mac/get-started/hello-mac.md) 一文，特别是 [Xcode 和 Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) 及 [输出口和操作](~/mac/get-started/hello-mac.md#outlets-and-actions) 部分的简介，因为它涵盖了我们将在本文中使用的重要概念和技巧。

你可能想要查看[Xamarin 内部](~/mac/internals/how-it-works.md)示例文档的 "将[c # 类/方法公开到目标 c](~/mac/internals/how-it-works.md) " 部分， `Register` 并说明用于将 `Export` C # 类连接到目标 C 对象和 UI 元素的和命令。

<a name="Introduction_to_Outline_Views"></a>

## <a name="introduction-to-outline-views"></a>大纲视图简介

大纲视图是一种允许用户展开或折叠分层数据行的表。 与表视图类似，大纲视图显示一组相关项的数据，行表示各个项，列表示这些项的属性。 与表视图不同，大纲视图中的项不在简单列表中，它们在层次结构中进行组织，如硬盘驱动器上的文件和文件夹。

如果大纲视图中的项包含其他项，则可以由用户展开或折叠。 可展开的项会显示一个公开三角形，该三角形在项目折叠时指向右侧，并在展开项时向下指向。 单击泄露三角会导致项目展开或折叠。

大纲视图 (`NSOutlineView`) 是表视图的子类 (`NSTableView`) 因此，它从其父类继承了很多这类行为。 因此，表视图支持的许多操作（如选择行或列、通过拖动列标题重定位列等）也受大纲视图的支持。 Xamarin 应用程序可以控制这些功能，可以将大纲视图的参数 (在代码中或 Interface Builder) 配置为允许或禁止特定操作。

大纲视图不存储它自己的数据，而是依赖于数据源 (`NSOutlineViewDataSource`) 按需提供所需的行和列。

大纲视图的行为可通过提供大纲视图委托的子类 (`NSOutlineViewDelegate`) 来支持大纲列管理、键入以选择功能、行选择和编辑、自定义跟踪以及单独的列和行的自定义视图。

由于大纲视图在表视图中共享了很多这种行为和功能，因此，您可能想要浏览 [表视图](~/mac/user-interface/table-view.md) 文档，然后再继续阅读本文。

<a name="Creating_and_Maintaining_Outline_Views_in_Xcode"></a>

## <a name="creating-and-maintaining-outline-views-in-xcode"></a>在 Xcode 中创建和维护大纲视图

创建新的 Xamarin Cocoa 应用程序时，默认情况下会获得一个标准空白窗口。 此窗口在 `.storyboard` 项目中自动包含的文件中定义。 若要编辑 windows 设计，请在 **解决方案资源管理器**中双击该 `Main.storyboard` 文件：

[![选择主情节提要](outline-view-images/edit01.png)](outline-view-images/edit01.png#lightbox)

这将在 Xcode 的 Interface Builder 中打开窗口设计：

[![在 Xcode 中编辑 UI](outline-view-images/edit02.png)](outline-view-images/edit02.png#lightbox)

`outline`在**库检查器的**搜索框中键入，以便更轻松地查找大纲视图控件：

[![从库中选择大纲视图](outline-view-images/edit03.png)](outline-view-images/edit03.png#lightbox)

将大纲视图拖到 " **界面编辑器**" 中的视图控制器上，使其填充视图控制器的内容区域，并将其设置为在 " **约束编辑器**" 中的窗口缩小和增长的位置：

[![编辑约束](outline-view-images/edit04.png)](outline-view-images/edit04.png#lightbox)

选择 **接口层次结构** 中的大纲视图， **属性检查器**中提供以下属性：

[![特性检查器](outline-view-images/edit05.png)](outline-view-images/edit05.png#lightbox)

- **大纲列** -显示层次结构数据的表列。
- **自动保存大纲列** -如果 `true` 为，则将在应用程序运行之间自动保存和还原大纲列。
- **缩进** -扩展项下的列缩进量。
- **跟随单元格缩进** -如果为 `true` ，则缩进标记将与单元一起缩进。
- **自动保存展开的项** -如果为 `true` ，则项的展开/折叠状态将自动保存并在应用程序运行之间还原。
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
> 除非您维护的是旧的 Xamarin 应用程序，否则 `NSView` 应该在基于的表视图上使用基于的大纲视图 `NSCell` 。 `NSCell` 被视为旧的，可能不会受到支持。

选择 **接口层次结构** 中的表列，" **属性检查器**" 中提供以下属性：

[![特性检查器](outline-view-images/edit06.png)](outline-view-images/edit06.png#lightbox)

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

[![特性检查器](outline-view-images/edit07.png)](outline-view-images/edit07.png#lightbox)

这些是标准视图的所有属性。 你还可以选择在此处调整此列的行的大小。

选择表视图单元 (默认情况下，这是 `NSTextField` **接口层次结构** 中的) ， **特性检查器**中提供了以下属性：

[![特性检查器](outline-view-images/edit08.png)](outline-view-images/edit08.png#lightbox)

你将在此处设置标准文本字段的所有属性。 默认情况下，标准文本字段用于显示列中单元格的数据。

在 `NSTableFieldCell` **接口层次结构** 中选择一个表单元格视图 () ， **特性检查器**中提供以下属性：

[![特性检查器](outline-view-images/edit09.png)](outline-view-images/edit09.png#lightbox)

最重要的设置如下：

- **布局** -选择此列中的单元格的布局方式。
- **使用单行模式** -如果为 `true` ，则单元格限制为单个行。
- **第一种运行时布局宽度** -如果 `true` 为，则单元格将更喜欢在首次运行应用程序时，手动或自动)  (设置的宽度。
- **操作** -控制向单元格发送编辑 **操作** 的时间。
- **行为** -定义单元是否可选择或可编辑。
- **格式文本** -如果 `true` 为，则单元格可以显示已设置格式的文本。
- **Undo** -如果为 `true` ，则单元格会对其撤消行为承担责任。

选择 "表单元" 视图 (在 `NSTableFieldCell` **接口层次结构**中表列的底部) ：

[![选择表单元格视图](outline-view-images/edit11.png)](outline-view-images/edit10.png#lightbox)

这允许您编辑作为给定列创建的所有单元格的基本 _模式_ 的表单元视图。

<a name="Adding_Actions_and_Outlets"></a>

### <a name="adding-actions-and-outlets"></a>添加操作和插座

就像任何其他 Cocoa UI 控件一样，我们需要根据所需的功能) 使用 **操作** 和 **插座** 将大纲视图及其列和单元格公开给 c # 代码 (。

对于想要公开的任何大纲视图元素，此过程是相同的：

1. 切换到 " **助手编辑器** " 并确保 `ViewController.h` 已选中此文件：

    [![选择正确的 .h 文件](outline-view-images/edit11.png)](outline-view-images/edit11.png#lightbox)
2. 从 **接口层次结构**中选择 "大纲" 视图，然后单击并拖动到该 `ViewController.h` 文件。
3. 为名**Outlet**为的大纲视图创建输出口 `ProductOutline` ：

    [![配置插座](outline-view-images/edit13.png)](outline-view-images/edit13.png#lightbox)
4. 为 tables 列创建 **插座** `ProductColumn` ，并调用 `DetailsColumn` ：

    [![配置插座](outline-view-images/edit14.png)](outline-view-images/edit14.png#lightbox)
5. 保存更改并返回到 Visual Studio for Mac 以与 Xcode 同步。

接下来，我们将编写代码，以便在应用程序运行时显示大纲的一些数据。

<a name="Populating_the_Table_View"></a>

## <a name="populating-the-outline-view"></a>填充大纲视图

通过 Interface Builder 中设计的大纲视图，并通过 **插座**公开，接下来，我们需要创建 c # 代码来填充它。

首先，让我们创建一个新 `Product` 类，以便保存子产品的各个行和组的信息。 在**解决方案资源管理器**中，右键单击项目，然后选择 "**添加**  >  **新文件 ...** "选择 "**常规**  >  " "**空类**"，输入 `Product` 作为**名称**，然后单击 "**新建**" 按钮：

[![创建空类](outline-view-images/populate01.png)](outline-view-images/populate01.png#lightbox)

使文件如下所 `Product.cs` 示：

```csharp
using System;
using Foundation;
using System.Collections.Generic;

namespace MacOutlines
{
    public class Product : NSObject
    {
        #region Public Variables
        public List<Product> Products = new List<Product>();
        #endregion

        #region Computed Properties
        public string Title { get; set;} = "";
        public string Description { get; set;} = "";
        public bool IsProductGroup {
            get { return (Products.Count > 0); }
        }
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

接下来，我们需要创建一个子类，为 `NSOutlineDataSource` 大纲提供所需数据。 在**解决方案资源管理器**中，右键单击项目，然后选择 "**添加**  >  **新文件 ...** "选择 "**常规**  >  **空类**"，在 "名称" 中输入， `ProductOutlineDataSource` 然后单击 "**新建**" 按钮。 **Name**

编辑 `ProductTableDataSource.cs` 文件，使其类似于以下内容：

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;

namespace MacOutlines
{
    public class ProductOutlineDataSource : NSOutlineViewDataSource
    {
        #region Public Variables
        public List<Product> Products = new List<Product>();
        #endregion

        #region Constructors
        public ProductOutlineDataSource ()
        {
        }
        #endregion

        #region Override Methods
        public override nint GetChildrenCount (NSOutlineView outlineView, NSObject item)
        {
            if (item == null) {
                return Products.Count;
            } else {
                return ((Product)item).Products.Count;
            }

        }

        public override NSObject GetChild (NSOutlineView outlineView, nint childIndex, NSObject item)
        {
            if (item == null) {
                return Products [childIndex];
            } else {
                return ((Product)item).Products [childIndex];
            }

        }

        public override bool ItemExpandable (NSOutlineView outlineView, NSObject item)
        {
            if (item == null) {
                return Products [0].IsProductGroup;
            } else {
                return ((Product)item).IsProductGroup;
            }

        }
        #endregion
    }
}
```

此类具有大纲视图项的存储，并重写 `GetChildrenCount` 以返回表中的行数。 `GetChild` (按照大纲视图的请求) 返回特定的父项或子项项，并将 `ItemExpandable` 指定项定义为父级或子级。

最后，我们需要创建一个子类来为 `NSOutlineDelegate` 大纲提供行为。 在**解决方案资源管理器**中，右键单击项目，然后选择 "**添加**  >  **新文件 ...** "选择 "**常规**  >  **空类**"，在 "名称" 中输入， `ProductOutlineDelegate` 然后单击 "**新建**" 按钮。 **Name**

编辑 `ProductOutlineDelegate.cs` 文件，使其类似于以下内容：

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;

namespace MacOutlines
{
    public class ProductOutlineDelegate : NSOutlineViewDelegate
    {
        #region Constants
        private const string CellIdentifier = "ProdCell";
        #endregion

        #region Private Variables
        private ProductOutlineDataSource DataSource;
        #endregion

        #region Constructors
        public ProductOutlineDelegate (ProductOutlineDataSource datasource)
        {
            this.DataSource = datasource;
        }
        #endregion

        #region Override Methods

        public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item) {
            // This pattern allows you reuse existing views when they are no-longer in use.
            // If the returned view is null, you instance up a new view
            // If a non-null view is returned, you modify it enough to reflect the new data
            NSTextField view = (NSTextField)outlineView.MakeView (CellIdentifier, this);
            if (view == null) {
                view = new NSTextField ();
                view.Identifier = CellIdentifier;
                view.BackgroundColor = NSColor.Clear;
                view.Bordered = false;
                view.Selectable = false;
                view.Editable = false;
            }

            // Cast item
            var product = item as Product;

            // Setup view based on the column selected
            switch (tableColumn.Title) {
            case "Product":
                view.StringValue = product.Title;
                break;
            case "Details":
                view.StringValue = product.Description;
                break;
            }

            return view;
        }
        #endregion
    }
}
```

当我们创建的实例时 `ProductOutlineDelegate` ，我们还会传入为 `ProductOutlineDataSource` 大纲提供数据的实例。 `GetView`方法负责返回一个视图 (数据) 以显示给定列和行的单元格。 如果可能，如果不创建新视图，则将重复使用现有视图来显示该单元。

若要填充轮廓，请编辑 `MainWindow.cs` 文件，使该方法如下所 `AwakeFromNib` 示：

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Create data source and populate
    var DataSource = new ProductOutlineDataSource ();

    var Vegetables = new Product ("Vegetables", "Greens and Other Produce");
    Vegetables.Products.Add (new Product ("Cabbage", "Brassica oleracea - Leaves, axillary buds, stems, flowerheads"));
    Vegetables.Products.Add (new Product ("Turnip", "Brassica rapa - Tubers, leaves"));
    Vegetables.Products.Add (new Product ("Radish", "Raphanus sativus - Roots, leaves, seed pods, seed oil, sprouting"));
    Vegetables.Products.Add (new Product ("Carrot", "Daucus carota - Root tubers"));
    DataSource.Products.Add (Vegetables);

    var Fruits = new Product ("Fruits", "Fruit is a part of a flowering plant that derives from specific tissues of the flower");
    Fruits.Products.Add (new Product ("Grape", "True Berry"));
    Fruits.Products.Add (new Product ("Cucumber", "Pepo"));
    Fruits.Products.Add (new Product ("Orange", "Hesperidium"));
    Fruits.Products.Add (new Product ("Blackberry", "Aggregate fruit"));
    DataSource.Products.Add (Fruits);

    var Meats = new Product ("Meats", "Lean Cuts");
    Meats.Products.Add (new Product ("Beef", "Cow"));
    Meats.Products.Add (new Product ("Pork", "Pig"));
    Meats.Products.Add (new Product ("Veal", "Young Cow"));
    DataSource.Products.Add (Meats);

    // Populate the outline
    ProductOutline.DataSource = DataSource;
    ProductOutline.Delegate = new ProductOutlineDelegate (DataSource);

}
```

如果运行该应用程序，将显示以下内容：

[![折叠视图](outline-view-images/populate02.png)](outline-view-images/populate02.png#lightbox)

如果展开大纲视图中的节点，它将如下所示：

[![展开的视图](outline-view-images/populate03.png)](outline-view-images/populate03.png#lightbox)

<a name="Sorting_by_Column"></a>

## <a name="sorting-by-column"></a>按列排序

允许用户通过单击列标题对大纲中的数据进行排序。 首先，双击该 `Main.storyboard` 文件以将其打开，以便在 Interface Builder 中进行编辑。 选择 " `Product` 列"，为 `Title` "**排序关键字**" 输入，为选择器输入， `compare:` 并为**Selector** `Ascending` **顺序**选择：

[![设置排序键顺序](outline-view-images/sort01.png)](outline-view-images/sort01.png#lightbox)

保存更改并返回到 Visual Studio for Mac 以与 Xcode 同步。

现在，让我们编辑 `ProductOutlineDataSource.cs` 文件并添加以下方法：

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
    }
}

public override void SortDescriptorsChanged (NSOutlineView outlineView, NSSortDescriptor[] oldDescriptors)
{
    // Sort the data
    Sort (oldDescriptors [0].Key, oldDescriptors [0].Ascending);
    outlineView.ReloadData ();
}
```

使用 `Sort` 方法，我们可以基于给定的 `Product` 类字段按升序或降序对数据源中的数据进行排序。 `SortDescriptorsChanged`每次单击列标题时，将调用重写的方法。 它将被传递到 Interface Builder 中设置的 **键值** 和该列的排序顺序。

如果运行应用程序，并单击列标题中的行，则将按该列对行进行排序：

[![排序输出的示例](outline-view-images/sort02.png)](outline-view-images/sort02.png#lightbox)

<a name="Row_Selection"></a>

## <a name="row-selection"></a>行选择

如果要允许用户选择单个行，请双击该 `Main.storyboard` 文件以将其打开，以便在 Interface Builder 中进行编辑。 选择**接口层次结构**中的大纲视图，并取消选中 "**属性检查器**" 中的 "**多个**" 复选框：

[![特性检查器](outline-view-images/select01.png)](outline-view-images/select01.png#lightbox)

保存更改并返回到 Visual Studio for Mac 以与 Xcode 同步。

接下来，编辑 `ProductOutlineDelegate.cs` 文件并添加以下方法：

```csharp
public override bool ShouldSelectItem (NSOutlineView outlineView, NSObject item)
{
    // Don't select product groups
    return !((Product)item).IsProductGroup;
}
```

这将允许用户在大纲视图中选择任何单个行。 `false` `ShouldSelectItem` 如果你不希望用户能够选择任何项，则为你不希望用户能够选择的任何项的，或者 `false` 为每个项返回。

<a name="Multiple_Row_Selection"></a>

## <a name="multiple-row-selection"></a>多行选择

如果要允许用户选择多个行，请双击该 `Main.storyboard` 文件以将其打开，以便在 Interface Builder 中进行编辑。 选择**接口层次结构**中的 "大纲" 视图，并选中 "**属性检查器**" 中的 "**多个**" 复选框：

[![特性检查器](outline-view-images/select02.png)](outline-view-images/select02.png#lightbox)

保存更改并返回到 Visual Studio for Mac 以与 Xcode 同步。

接下来，编辑 `ProductOutlineDelegate.cs` 文件并添加以下方法：

```csharp
public override bool ShouldSelectItem (NSOutlineView outlineView, NSObject item)
{
    // Don't select product groups
    return !((Product)item).IsProductGroup;
}
```

这将允许用户在大纲视图中选择任何单个行。 `false` `ShouldSelectRow` 如果你不希望用户能够选择任何项，则为你不希望用户能够选择的任何项的，或者 `false` 为每个项返回。

<a name="Type_to_Select_Row"></a>

## <a name="type-to-select-row"></a>键入以选择行

如果要允许用户在选中大纲视图的情况下键入字符，并选择包含该字符的第一行，请双击该 `Main.storyboard` 文件以将其打开，以便在 Interface Builder 中进行编辑。 选择**接口层次结构**中的 "大纲" 视图，并选中 "**属性检查器**" 中的 "**类型选择**" 复选框：

[![编辑行类型](outline-view-images/type01.png)](outline-view-images/type01.png#lightbox)

保存更改并返回到 Visual Studio for Mac 以与 Xcode 同步。

现在，让我们编辑 `ProductOutlineDelegate.cs` 文件并添加以下方法：

```csharp
public override NSObject GetNextTypeSelectMatch (NSOutlineView outlineView, NSObject startItem, NSObject endItem, string searchString)
{
    foreach(Product product in DataSource.Products) {
        if (product.Title.Contains (searchString)) {
            return product;
        }
    }

    // Not found
    return null;
}
```

`GetNextTypeSelectMatch`方法采用给定的 `searchString` 并返回 `Product` 中包含该字符串的第一个项 `Title` 。

<a name="Reordering_Columns"></a>

## <a name="reordering-columns"></a>重新排序列

如果要允许用户在大纲视图中拖动重新排序列，请双击该 `Main.storyboard` 文件以将其打开，以便在 Interface Builder 中进行编辑。 选择**接口层次结构**中的大纲视图，并选中 "**属性检查器**" 中的 "重新**排序**" 复选框：

[![特性检查器](outline-view-images/reorder01.png)](outline-view-images/reorder01.png#lightbox)

如果我们为 " **自动保存** " 属性提供一个值，并选中 " **列信息** " 字段，则对该表的布局所做的任何更改都将自动保存，并在下次运行应用程序时还原。

保存更改并返回到 Visual Studio for Mac 以与 Xcode 同步。

现在，让我们编辑 `ProductOutlineDelegate.cs` 文件并添加以下方法：

```csharp
public override bool ShouldReorder (NSOutlineView outlineView, nint columnIndex, nint newColumnIndex)
{
    return true;
}
```

`ShouldReorder` `true` 对于要允许将其拖动到中的任何列，方法应返回 `newColumnIndex` ，否则返回 `false` ;

如果运行应用程序，我们可以拖动列标题以对列进行重新排序：

[![重新排序列的示例](outline-view-images/reorder02.png)](outline-view-images/reorder02.png#lightbox)

<a name="Editing_Cells"></a>

## <a name="editing-cells"></a>编辑单元格

如果希望允许用户编辑给定单元的值，请编辑 `ProductOutlineDelegate.cs` 文件，并 `GetViewForItem` 按如下所示更改方法：

```csharp
public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item) {
    // Cast item
    var product = item as Product;

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTextField view = (NSTextField)outlineView.MakeView (tableColumn.Title, this);
    if (view == null) {
        view = new NSTextField ();
        view.Identifier = tableColumn.Title;
        view.BackgroundColor = NSColor.Clear;
        view.Bordered = false;
        view.Selectable = false;
        view.Editable = !product.IsProductGroup;
    }

    // Tag view
    view.Tag = outlineView.RowForItem (item);

    // Allow for edit
    view.EditingEnded += (sender, e) => {

        // Grab product
        var prod = outlineView.ItemAtRow(view.Tag) as Product;

        // Take action based on type
        switch(view.Identifier) {
        case "Product":
            prod.Title = view.StringValue;
            break;
        case "Details":
            prod.Description = view.StringValue;
            break;
        }
    };

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.StringValue = product.Title;
        break;
    case "Details":
        view.StringValue = product.Description;
        break;
    }

    return view;
}
```

现在，如果我们运行应用程序，则用户可以在表视图中编辑单元：

[![编辑单元格的示例](outline-view-images/editing01.png)](outline-view-images/editing01.png#lightbox)

<a name="Using_Images_in_Outline_Views"></a>

## <a name="using-images-in-outline-views"></a>在大纲视图中使用图像

若要在中包含图像作为单元的一部分 `NSOutlineView` ，需要更改大纲视图的方法返回数据的方式， `NSTableViewDelegate's` `GetView` 以使用 `NSTableCellView` 而不是典型的 `NSTextField` 。 例如：

```csharp
public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item) {
    // Cast item
    var product = item as Product;

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTableCellView view = (NSTableCellView)outlineView.MakeView (tableColumn.Title, this);
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
        view.TextField.Editable = !product.IsProductGroup;
    }

    // Tag view
    view.TextField.Tag = outlineView.RowForItem (item);

    // Allow for edit
    view.TextField.EditingEnded += (sender, e) => {

        // Grab product
        var prod = outlineView.ItemAtRow(view.Tag) as Product;

        // Take action based on type
        switch(view.Identifier) {
        case "Product":
            prod.Title = view.TextField.StringValue;
            break;
        case "Details":
            prod.Description = view.TextField.StringValue;
            break;
        }
    };

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.ImageView.Image = NSImage.ImageNamed (product.IsProductGroup ? "tags.png" : "tag.png");
        view.TextField.StringValue = product.Title;
        break;
    case "Details":
        view.TextField.StringValue = product.Description;
        break;
    }

    return view;
}
```

有关详细信息，请参阅使用[图像](~/mac/app-fundamentals/image.md)文档中的[使用带有大纲视图的图像](~/mac/app-fundamentals/image.md)部分。

<a name="Data_Binding_Outline_Views"></a>

## <a name="data-binding-outline-views"></a>数据绑定大纲视图

通过在 Xamarin 应用程序中使用键/值编码和数据绑定技术，可以极大地减少需要编写和维护的代码量，以填充和处理 UI 元素。 你还可以从你的前端用户界面 (_模型-视图-控制器_) 中进一步分离你的支持数据 (_数据) 模型_，从而更易于维护、更灵活的应用程序设计。

键-值编码 (KVC) 是一种用于间接访问对象属性的机制， (专门设置格式的字符串中的键) 标识属性，而不是通过实例变量或通过 () 的访问器方法来访问这些属性 `get/set` 。 通过在 Xamarin 应用程序中实现符合键值的代码访问器，你可以访问其他 macOS 功能，如 (KVO) 、数据绑定、核心数据、Cocoa 绑定和 scriptability 的键-值观察。

有关详细信息，请参阅[数据绑定和键-值编码](~/mac/app-fundamentals/databinding.md)文档的[大纲视图数据绑定](~/mac/app-fundamentals/databinding.md#Outline_View_Data_Binding)部分。

<a name="Summary"></a>

## <a name="summary"></a>总结

本文详细介绍了如何在 Xamarin. Mac 应用程序中使用大纲视图。 我们看到了不同的类型和使用大纲视图，如何在 Xcode 的 Interface Builder 中创建和维护大纲视图，以及如何在 c # 代码中使用大纲视图。

## <a name="related-links"></a>相关链接

- [MacOutlines (示例) ](/samples/xamarin/mac-samples/macoutlines)
- [MacImages（示例）](/samples/xamarin/mac-samples/macimages)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [表视图](~/mac/user-interface/table-view.md)
- [源列表](~/mac/user-interface/source-list.md)
- [数据绑定和键值编码](~/mac/app-fundamentals/databinding.md)
- [OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [大纲视图简介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/OutlineView/OutlineView.html#//apple_ref/doc/uid/10000023i)
- [NSOutlineView](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSOutlineView_Class/index.html#//apple_ref/doc/uid/TP40004079)
- [NSOutlineViewDataSource](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSOutlineViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40004175)
- [NSOutlineViewDelegate](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSOutlineViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40008609)