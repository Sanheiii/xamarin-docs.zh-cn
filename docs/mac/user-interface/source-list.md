---
title: Xamarin 中的源列表
description: '本文介绍如何在 Xamarin. Mac 应用程序中使用源列表。 它介绍了如何在 Xcode 中创建和维护源列表，并 Interface Builder 在 c # 代码中与它们进行交互。'
ms.prod: xamarin
ms.assetid: 651A3649-5AA8-4133-94D6-4873D99F7FCC
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: ae82918c1f87e47be9446e80f99ebc4dc967c078
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431945"
---
# <a name="source-lists-in-xamarinmac"></a>Xamarin 中的源列表

_本文介绍如何在 Xamarin. Mac 应用程序中使用源列表。它介绍了如何在 Xcode 中创建和维护源列表，并 Interface Builder 在 c # 代码中与它们进行交互。_

在 Xamarin 应用 *程序中使用* c # 和 .net 时，您可以访问与在 *Xcode 和* 中工作的开发人员相同的源列表。 由于 Xamarin 与 Xcode 直接集成，因此可以使用 Xcode 的 _Interface Builder_ 来创建和维护源列表 (或在 c # 代码) 中直接创建它们。

源列表是一种特殊类型的大纲视图，用于显示操作的源，例如查找器或 iTunes 中的侧栏。

[![示例源列表](source-list-images/source05.png)](source-list-images/source05.png#lightbox)

在本文中，我们将介绍在 Xamarin. Mac 应用程序中使用源列表的基本知识。 强烈建议您先完成 [Hello，Mac](~/mac/get-started/hello-mac.md) 一文，特别是 [Xcode 和 Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) 及 [输出口和操作](~/mac/get-started/hello-mac.md#outlets-and-actions) 部分的简介，因为它涵盖了我们将在本文中使用的重要概念和技巧。

你可能想要查看[Xamarin 内部](~/mac/internals/how-it-works.md)示例文档的 "将[c # 类/方法公开到目标 c](~/mac/internals/how-it-works.md) " 部分， `Register` 并说明用于将 `Export` C # 类连接到目标 C 对象和 UI 元素的和命令。

<a name="Introduction_to_Outline_Views"></a>

## <a name="introduction-to-source-lists"></a>源列表简介

如上所述，源列表是一种特殊类型的大纲视图，用于显示操作的源，例如查找器或 iTunes 中的侧栏。 源列表是一种允许用户展开或折叠分层数据行的表。 与表视图不同，源列表中的项不在简单列表中，它们在层次结构中进行组织，如硬盘驱动器上的文件和文件夹。 如果源列表中的某个项包含其他项，则可以由用户展开或折叠。

源列表是一种特殊的样式大纲视图 (`NSOutlineView`) ，它本身是表视图的子类 (`NSTableView`) 如此，它从其父类继承了很多这类行为。 因此，大纲视图支持的许多操作也受源列表支持。 Xamarin 应用程序可以控制这些功能，可以将源列表的参数配置 (在代码中或 Interface Builder) ，以允许或禁止特定操作。

源列表不存储它自己的数据，而是依赖于数据源 (`NSOutlineViewDataSource`) 以根据需要提供所需的行和列。

可以自定义源列表的行为，方法是提供大纲视图委托的子类 (`NSOutlineViewDelegate`) 支持大纲类型，以选择功能、项选择和编辑、自定义跟踪以及单个项的自定义视图。

由于源列表与表视图和大纲视图共用其行为和功能，您可能需要浏览 [表视图](~/mac/user-interface/table-view.md) 和 [大纲视图](~/mac/user-interface/outline-view.md) 文档，然后再继续阅读本文。

<a name="Working_with_Source_Lists"></a>

## <a name="working-with-source-lists"></a>使用源列表

源列表是一种特殊类型的大纲视图，用于显示操作的源，例如查找器或 iTunes 中的侧栏。 与大纲视图不同，在 Interface Builder 中定义源列表之前，让我们在 Xamarin 中创建支持类。

首先，让我们创建一个新 `SourceListItem` 类来保存源列表的数据。 在**解决方案资源管理器**中，右键单击项目，然后选择 "**添加**  >  **新文件 ...** "选择 "**常规**  >  " "**空类**"，输入 `SourceListItem` 作为**名称**，然后单击 "**新建**" 按钮：

[![添加空类](source-list-images/source01.png)](source-list-images/source01.png#lightbox)

使文件如下所 `SourceListItem.cs` 示： 

```csharp
using System;
using System.Collections;
using System.Collections.Generic;
using AppKit;
using Foundation;

namespace MacOutlines
{
    public class SourceListItem: NSObject, IEnumerator, IEnumerable
    {
        #region Private Properties
        private string _title;
        private NSImage _icon;
        private string _tag;
        private List<SourceListItem> _items = new List<SourceListItem> ();
        #endregion

        #region Computed Properties
        public string Title {
            get { return _title; }
            set { _title = value; }
        }

        public NSImage Icon {
            get { return _icon; }
            set { _icon = value; }
        }

        public string Tag {
            get { return _tag; }
            set { _tag=value; }
        }
        #endregion

        #region Indexer
        public SourceListItem this[int index]
        {
            get
            {
                return _items[index];
            }

            set
            {
                _items[index] = value;
            }
        }

        public int Count {
            get { return _items.Count; }
        }

        public bool HasChildren {
            get { return (Count > 0); }
        }
        #endregion

        #region Enumerable Routines
        private int _position = -1;

        public IEnumerator GetEnumerator()
        {
            _position = -1;
            return (IEnumerator)this;
        }

        public bool MoveNext()
        {
            _position++;
            return (_position < _items.Count);
        }

        public void Reset()
        {_position = -1;}

        public object Current
        {
            get 
            { 
                try 
                {
                    return _items[_position];
                }

                catch (IndexOutOfRangeException)
                {
                    throw new InvalidOperationException();
                }
            }
        }
        #endregion

        #region Constructors
        public SourceListItem ()
        {
        }

        public SourceListItem (string title)
        {
            // Initialize
            this._title = title;
        }

        public SourceListItem (string title, string icon)
        {
            // Initialize
            this._title = title;
            this._icon = NSImage.ImageNamed (icon);
        }

        public SourceListItem (string title, string icon, ClickedDelegate clicked)
        {
            // Initialize
            this._title = title;
            this._icon = NSImage.ImageNamed (icon);
            this.Clicked = clicked;
        }

        public SourceListItem (string title, NSImage icon)
        {
            // Initialize
            this._title = title;
            this._icon = icon;
        }

        public SourceListItem (string title, NSImage icon, ClickedDelegate clicked)
        {
            // Initialize
            this._title = title;
            this._icon = icon;
            this.Clicked = clicked;
        }

        public SourceListItem (string title, NSImage icon, string tag)
        {
            // Initialize
            this._title = title;
            this._icon = icon;
            this._tag = tag;
        }

        public SourceListItem (string title, NSImage icon, string tag, ClickedDelegate clicked)
        {
            // Initialize
            this._title = title;
            this._icon = icon;
            this._tag = tag;
            this.Clicked = clicked;
        }
        #endregion

        #region Public Methods
        public void AddItem(SourceListItem item) {
            _items.Add (item);
        }

        public void AddItem(string title) {
            _items.Add (new SourceListItem (title));
        }

        public void AddItem(string title, string icon) {
            _items.Add (new SourceListItem (title, icon));
        }

        public void AddItem(string title, string icon, ClickedDelegate clicked) {
            _items.Add (new SourceListItem (title, icon, clicked));
        }

        public void AddItem(string title, NSImage icon) {
            _items.Add (new SourceListItem (title, icon));
        }

        public void AddItem(string title, NSImage icon, ClickedDelegate clicked) {
            _items.Add (new SourceListItem (title, icon, clicked));
        }

        public void AddItem(string title, NSImage icon, string tag) {
            _items.Add (new SourceListItem (title, icon, tag));
        }

        public void AddItem(string title, NSImage icon, string tag, ClickedDelegate clicked) {
            _items.Add (new SourceListItem (title, icon, tag, clicked));
        }

        public void Insert(int n, SourceListItem item) {
            _items.Insert (n, item);
        }

        public void RemoveItem(SourceListItem item) {
            _items.Remove (item);
        }

        public void RemoveItem(int n) {
            _items.RemoveAt (n);
        }

        public void Clear() {
            _items.Clear ();
        }
        #endregion

        #region Events
        public delegate void ClickedDelegate();
        public event ClickedDelegate Clicked;

        internal void RaiseClickedEvent() {
            // Inform caller
            if (this.Clicked != null)
                this.Clicked ();
        }
        #endregion
    }
}
```

在**解决方案资源管理器**中，右键单击项目，然后选择 "**添加**  >  **新文件 ...** "选择 "**常规**  >  **空类**"，在 "名称" 中输入， `SourceListDataSource` 然后单击 "**新建**" 按钮。 **Name** 使文件如下所 `SourceListDataSource.cs` 示：

```csharp
using System;
using System.Collections;
using System.Collections.Generic;
using AppKit;
using Foundation;

namespace MacOutlines
{
    public class SourceListDataSource : NSOutlineViewDataSource
    {
        #region Private Variables
        private SourceListView _controller;
        #endregion

        #region Public Variables
        public List<SourceListItem> Items = new List<SourceListItem>();
        #endregion

        #region Constructors
        public SourceListDataSource (SourceListView controller)
        {
            // Initialize
            this._controller = controller;
        }
        #endregion

        #region Override Properties
        public override nint GetChildrenCount (NSOutlineView outlineView, Foundation.NSObject item)
        {
            if (item == null) {
                return Items.Count;
            } else {
                return ((SourceListItem)item).Count;
            }
        }

        public override bool ItemExpandable (NSOutlineView outlineView, Foundation.NSObject item)
        {
            return ((SourceListItem)item).HasChildren;
        }

        public override NSObject GetChild (NSOutlineView outlineView, nint childIndex, Foundation.NSObject item)
        {
            if (item == null) {
                return Items [(int)childIndex];
            } else {
                return ((SourceListItem)item) [(int)childIndex]; 
            }
        }

        public override NSObject GetObjectValue (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item)
        {
            return new NSString (((SourceListItem)item).Title);
        }
        #endregion

        #region Internal Methods
        internal SourceListItem ItemForRow(int row) {
            int index = 0;

            // Look at each group
            foreach (SourceListItem item in Items) {
                // Is the row inside this group?
                if (row >= index && row <= (index + item.Count)) {
                    return item [row - index - 1];
                }

                // Move index
                index += item.Count + 1;
            }

            // Not found 
            return null;
        }
        #endregion
    }
}
```

这将为源列表提供数据。

在**解决方案资源管理器**中，右键单击项目，然后选择 "**添加**  >  **新文件 ...** "选择 "**常规**  >  **空类**"，在 "名称" 中输入， `SourceListDelegate` 然后单击 "**新建**" 按钮。 **Name** 使文件如下所 `SourceListDelegate.cs` 示：

```csharp
using System;
using AppKit;
using Foundation;

namespace MacOutlines
{
    public class SourceListDelegate : NSOutlineViewDelegate
    {
        #region Private variables
        private SourceListView _controller;
        #endregion

        #region Constructors
        public SourceListDelegate (SourceListView controller)
        {
            // Initialize
            this._controller = controller;
        }
        #endregion

        #region Override Methods
        public override bool ShouldEditTableColumn (NSOutlineView outlineView, NSTableColumn tableColumn, Foundation.NSObject item)
        {
            return false;
        }

        public override NSCell GetCell (NSOutlineView outlineView, NSTableColumn tableColumn, Foundation.NSObject item)
        {
            nint row = outlineView.RowForItem (item);
            return tableColumn.DataCellForRow (row);
        }

        public override bool IsGroupItem (NSOutlineView outlineView, Foundation.NSObject item)
        {
            return ((SourceListItem)item).HasChildren;
        }

        public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item)
        {
            NSTableCellView view = null;

            // Is this a group item?
            if (((SourceListItem)item).HasChildren) {
                view = (NSTableCellView)outlineView.MakeView ("HeaderCell", this);
            } else {
                view = (NSTableCellView)outlineView.MakeView ("DataCell", this);
                view.ImageView.Image = ((SourceListItem)item).Icon;
            }

            // Initialize view
            view.TextField.StringValue = ((SourceListItem)item).Title;

            // Return new view
            return view;
        }

        public override bool ShouldSelectItem (NSOutlineView outlineView, Foundation.NSObject item)
        {
            return (outlineView.GetParent (item) != null);
        }

        public override void SelectionDidChange (NSNotification notification)
        {
            NSIndexSet selectedIndexes = _controller.SelectedRows;

            // More than one item selected?
            if (selectedIndexes.Count > 1) {
                // Not handling this case
            } else {
                // Grab the item
                var item = _controller.Data.ItemForRow ((int)selectedIndexes.FirstIndex);

                // Was an item found?
                if (item != null) {
                    // Fire the clicked event for the item
                    item.RaiseClickedEvent ();

                    // Inform caller of selection
                    _controller.RaiseItemSelected (item);
                }
            }
        }
        #endregion
    }
}
```

这将为源列表提供行为。

最后，在**解决方案资源管理器**中，右键单击项目，然后选择 "**添加**  >  **新文件 ...** "选择 "**常规**  >  **空类**"，在 "名称" 中输入， `SourceListView` 然后单击 "**新建**" 按钮。 **Name** 使文件如下所 `SourceListView.cs` 示：

```csharp
using System;
using AppKit;
using Foundation;

namespace MacOutlines
{
    [Register("SourceListView")]
    public class SourceListView : NSOutlineView
    {
        #region Computed Properties
        public SourceListDataSource Data {
            get {return (SourceListDataSource)this.DataSource; }
        }
        #endregion

        #region Constructors
        public SourceListView ()
        {

        }

        public SourceListView (IntPtr handle) : base(handle)
        {

        }

        public SourceListView (NSCoder coder) : base(coder)
        {

        }

        public SourceListView (NSObjectFlag t) : base(t)
        {

        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();
        }
        #endregion

        #region Public Methods
        public void Initialize() {

            // Initialize this instance
            this.DataSource = new SourceListDataSource (this);
            this.Delegate = new SourceListDelegate (this);

        }
        
        public void AddItem(SourceListItem item) {
            if (Data != null) {
                Data.Items.Add (item);
            }
        }
        #endregion

        #region Events
        public delegate void ItemSelectedDelegate(SourceListItem item);
        public event ItemSelectedDelegate ItemSelected;

        internal void RaiseItemSelected(SourceListItem item) {
            // Inform caller
            if (this.ItemSelected != null) {
                this.ItemSelected (item);
            }
        }
        #endregion
    }
}
```

这会创建一个自定义的可重用子类 `NSOutlineView` (`SourceListView`) ，我们可以使用它们来驱动我们所做的任何 Xamarin 应用程序中的源列表。

<a name="Creating_and_Maintaining_Source_Lists_in_Xcode"></a>

## <a name="creating-and-maintaining-source-lists-in-xcode"></a>在 Xcode 中创建和维护源列表

现在，让我们在 Interface Builder 中设计源列表。 双击该 `Main.storyboard` 文件以将其打开，以便在 Interface Builder 中进行编辑，并从 **库检查器**中拖动拆分视图，将其添加到视图控制器，并将其设置为在 " **约束编辑器**" 中调整视图的大小：

[![编辑约束](source-list-images/source00.png)](source-list-images/source00.png#lightbox)

接下来，从 **库检查器**中拖出源列表，将其添加到拆分视图的左侧，并将其设置为与 " **约束编辑器**" 中的视图一起调整大小：

[![编辑约束](source-list-images/source02.png)](source-list-images/source02.png#lightbox)

接下来，切换到 " **标识" 视图**，选择 "源" 列表，并将其 " **类** " 更改为 `SourceListView` ：

[![设置类名](source-list-images/source03.png)](source-list-images/source03.png#lightbox)

最后，为文件中调用的源列表创建一个 **插座** `SourceList` `ViewController.h` ：

[![配置插座](source-list-images/source04.png)](source-list-images/source04.png#lightbox)

保存更改并返回到 Visual Studio for Mac 以与 Xcode 同步。

<a name="Populating the Source List"></a>

## <a name="populating-the-source-list"></a>填充源列表

让我们 `RotationWindow.cs` 在 Visual Studio for Mac 中编辑文件，使其 `AwakeFromNib` 方法如下所示：

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Populate source list
    SourceList.Initialize ();

    var library = new SourceListItem ("Library");
    library.AddItem ("Venues", "house.png", () => {
        Console.WriteLine("Venue Selected");
    });
    library.AddItem ("Singers", "group.png");
    library.AddItem ("Genre", "cards.png");
    library.AddItem ("Publishers", "box.png");
    library.AddItem ("Artist", "person.png");
    library.AddItem ("Music", "album.png");
    SourceList.AddItem (library);

    // Add Rotation 
    var rotation = new SourceListItem ("Rotation"); 
    rotation.AddItem ("View Rotation", "redo.png");
    SourceList.AddItem (rotation);

    // Add Kiosks
    var kiosks = new SourceListItem ("Kiosks");
    kiosks.AddItem ("Sign-in Station 1", "imac");
    kiosks.AddItem ("Sign-in Station 2", "ipad");
    SourceList.AddItem (kiosks);

    // Display side list
    SourceList.ReloadData ();
    SourceList.ExpandItem (null, true);

}
```

在将 `Initialize ()` 任何项添加到源列表的**插座**_之前_，需要对其调用方法。 对于每个项组，我们创建一个父项，然后将子项添加到该组项。 然后，将每个组添加到源列表的集合 `SourceList.AddItem (...)` 中。 最后两行加载源列表的数据，并展开所有组：

```csharp
// Display side list
SourceList.ReloadData ();
SourceList.ExpandItem (null, true);
```

最后，编辑 `AppDelegate.cs` 文件并使方法如下所 `DidFinishLaunching` 示：

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    mainWindowController = new MainWindowController ();
    mainWindowController.Window.MakeKeyAndOrderFront (this);

    var rotation = new RotationWindowController ();
    rotation.Window.MakeKeyAndOrderFront (this);
}
```

如果我们运行应用程序，将显示以下内容：

[![示例应用运行](source-list-images/source05.png)](source-list-images/source05.png#lightbox)

<a name="Summary"></a>

## <a name="summary"></a>总结

本文详细介绍了如何在 Xamarin. Mac 应用程序中使用源列表。 我们已了解如何在 Xcode 的 Interface Builder 中创建和维护源列表，以及如何在 c # 代码中使用源列表。

## <a name="related-links"></a>相关链接

- [MacOutlines (示例) ](/samples/xamarin/mac-samples/macoutlines)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [表视图](~/mac/user-interface/table-view.md)
- [大纲视图](~/mac/user-interface/outline-view.md)
- [OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [大纲视图简介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/OutlineView/OutlineView.html#//apple_ref/doc/uid/10000023i)
- [NSOutlineView](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSOutlineView_Class/index.html#//apple_ref/doc/uid/TP40004079)
- [NSOutlineViewDataSource](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSOutlineViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40004175)
- [NSOutlineViewDelegate](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSOutlineViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40008609)