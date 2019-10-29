---
title: Xamarin 中的集合视图
description: 本文介绍如何在 Xamarin. Mac 应用中使用集合视图。 它介绍了如何在 Xcode 中创建和维护集合视图，并 Interface Builder 以编程方式使用它们。
ms.prod: xamarin
ms.assetid: 6EE32256-5948-4AE4-8133-6D0B3F4173E8
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 05/24/2017
ms.openlocfilehash: 565441762bc7d9dcf7f73b42a34e3feb0bff86f1
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025825"
---
# <a name="collection-views-in-xamarinmac"></a>Xamarin 中的集合视图

_本文介绍如何在 Xamarin. Mac 应用中使用集合视图。它介绍了如何在 Xcode 中创建和维护集合视图，并 Interface Builder 以编程方式使用它们。_

在 Xamarin 应用C# *程序中使用*和 .net 时，开发人员有权访问在 AppKit 和*Xcode*中工作的开发人员。 因为 Xamarin 与 Xcode 直接集成，所以开发人员使用 Xcode 的_Interface Builder_来创建和维护集合视图。

`NSCollectionView` 显示使用 `NSCollectionViewLayout`组织的子视图网格。 网格中的每个子视图都由一个 `NSCollectionViewItem` 表示，该用于管理从 `.xib` 文件加载视图内容。

[![示例应用运行](collection-view-images/intro01.png)](collection-view-images/intro01.png#lightbox)

本文介绍在 Xamarin. Mac 应用中使用集合视图的基础知识。 强烈建议您先完成[Hello，Mac](~/mac/get-started/hello-mac.md)一文，特别是[Xcode 和 Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)与[插座和操作](~/mac/get-started/hello-mac.md#outlets-and-actions)部分的简介，因为它涵盖了所使用的重要概念和技巧贯穿本文。

你可能想要查看[Xamarin](~/mac/internals/how-it-works.md)示例文档的 " C# [公开C#类/方法到目标-c](~/mac/internals/how-it-works.md) " 部分，并说明用于将类连接到目标的`Register`和`Export`命令-c对象和 UI 元素。

<a name="About_Collection_Views"/>

## <a name="about-collection-views"></a>关于集合视图

集合视图（`NSCollectionView`）的主要目标是使用集合视图布局（`NSCollectionViewLayout`）以有序的方式排列一组对象，其中每个单独的对象（`NSCollectionViewItem`）在更大的集合中获取其自己的视图。 集合视图通过数据绑定和键/值编码方法工作，因此，在继续阅读本文之前，您应该阅读[数据绑定和键/值编码](~/mac/app-fundamentals/databinding.md)文档。

集合视图没有标准的内置集合视图项（例如大纲视图或表视图），因此开发人员_负责使用其他_AppKit 控件（如图像字段、文本字段、标签、等.此原型视图将用于显示和使用由集合视图管理的每个项，并存储在 `.xib` 文件中。

由于开发人员负责收集视图项的外观，因此 "集合" 视图不支持突出显示网格中选定的项。 本文将介绍如何实现此功能。

<a name="Defining_your_Data_Model"/>

## <a name="defining-the-data-model"></a>定义数据模型

在 Interface Builder 中绑定集合视图之前，必须在 Xamarin 应用程序中定义一个键/值编码（KVC）/Key-Value KVO （）兼容类，才能充当绑定的_数据模型_。 数据模型提供将显示在集合中的所有数据，并在运行应用程序时接收用户在 UI 中所做数据的任何修改。

采用管理一组员工的应用示例，可以使用以下类定义数据模型：

```csharp
using System;
using Foundation;
using AppKit;

namespace MacDatabinding
{
    [Register("PersonModel")]
    public class PersonModel : NSObject
    {
        #region Private Variables
        private string _name = "";
        private string _occupation = "";
        private bool _isManager = false;
        private NSMutableArray _people = new NSMutableArray();
        #endregion

        #region Computed Properties
        [Export("Name")]
        public string Name {
            get { return _name; }
            set {
                WillChangeValue ("Name");
                _name = value;
                DidChangeValue ("Name");
            }
        }

        [Export("Occupation")]
        public string Occupation {
            get { return _occupation; }
            set {
                WillChangeValue ("Occupation");
                _occupation = value;
                DidChangeValue ("Occupation");
            }
        }

        [Export("isManager")]
        public bool isManager {
            get { return _isManager; }
            set {
                WillChangeValue ("isManager");
                WillChangeValue ("Icon");
                _isManager = value;
                DidChangeValue ("isManager");
                DidChangeValue ("Icon");
            }
        }

        [Export("isEmployee")]
        public bool isEmployee {
            get { return (NumberOfEmployees == 0); }
        }

        [Export("Icon")]
        public NSImage Icon
        {
            get
            {
                if (isManager)
                {
                    return NSImage.ImageNamed("IconGroup");
                }
                else
                {
                    return NSImage.ImageNamed("IconUser");
                }
            }
        }

        [Export("personModelArray")]
        public NSArray People {
            get { return _people; }
        }

        [Export("NumberOfEmployees")]
        public nint NumberOfEmployees {
            get { return (nint)_people.Count; }
        }
        #endregion

        #region Constructors
        public PersonModel ()
        {
        }

        public PersonModel (string name, string occupation)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
        }

        public PersonModel (string name, string occupation, bool manager)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
            this.isManager = manager;
        }
        #endregion

        #region Array Controller Methods
        [Export("addObject:")]
        public void AddPerson(PersonModel person) {
            WillChangeValue ("personModelArray");
            isManager = true;
            _people.Add (person);
            DidChangeValue ("personModelArray");
        }

        [Export("insertObject:inPersonModelArrayAtIndex:")]
        public void InsertPerson(PersonModel person, nint index) {
            WillChangeValue ("personModelArray");
            _people.Insert (person, index);
            DidChangeValue ("personModelArray");
        }

        [Export("removeObjectFromPersonModelArrayAtIndex:")]
        public void RemovePerson(nint index) {
            WillChangeValue ("personModelArray");
            _people.RemoveObject (index);
            DidChangeValue ("personModelArray");
        }

        [Export("setPersonModelArray:")]
        public void SetPeople(NSMutableArray array) {
            WillChangeValue ("personModelArray");
            _people = array;
            DidChangeValue ("personModelArray");
        }
        #endregion
    }
}
```

本文的其余部分将使用 `PersonModel` 数据模型。

<a name="Working_with_a_Collection_View"/>

## <a name="working-with-a-collection-view"></a>使用集合视图

使用集合视图进行数据绑定非常类似于与表视图绑定，因为 `NSCollectionViewDataSource` 用于为集合提供数据。 由于集合视图没有预设的显示格式，因此提供用户交互反馈和跟踪用户选择时需要执行更多的工作。

<a name="Creating-the-Cell-Prototype"/>

### <a name="creating-the-cell-prototype"></a>创建单元格原型

由于集合视图不包含默认单元原型，开发人员需要将一个或多个 `.xib` 文件添加到 Xamarin 应用程序中，以定义各个单元格的布局和内容。

请执行以下操作：

1. 在**解决方案资源管理器**中，右键单击项目名称，然后选择 "**添加** > **新文件 ...** "
2. 选择 " **Mac** > **查看控制器**"，为其指定一个名称（如本示例中的 `EmployeeItem`），然后单击 "**新建**" 按钮以创建： 

    ![添加新的视图控制器](collection-view-images/proto01.png)

    这会将 `EmployeeItem.cs`、`EmployeeItemController.cs` 和 `EmployeeItemController.xib` 文件添加到项目的解决方案中。
3. 双击 `EmployeeItemController.xib` 文件，将其打开，以便在 Xcode 的 Interface Builder 中进行编辑。
4. 将 `NSBox`、`NSImageView` 和两个 `NSLabel` 控件添加到视图，并按如下所示对其进行布局：

    ![设计单元原型的布局](collection-view-images/proto02.png)
5. 打开**助手编辑器**并为 `NSBox` 创建一个**插座**，使其可用于指示单元格的选择状态：

    ![公开插座中的 NSBox](collection-view-images/proto03.png)
6. 返回到**标准编辑器**，然后选择 "图像" 视图。
7. 在**绑定检查器**中，选择 "**绑定到** > **文件所有者**"，然后输入 `self.Person.Icon`的**模型密钥路径**：

    ![绑定图标](collection-view-images/proto04.png)
8. 选择第一个标签，然后在**绑定检查器**中，选择 "**绑定到** > **文件的所有者**"，然后输入 `self.Person.Name`的**模型密钥路径**：

    ![绑定名称](collection-view-images/proto05.png)
9. 选择第二个标签，然后在**绑定检查器**中，选择 "**绑定到** > **文件的所有者**"，然后输入 `self.Person.Occupation`的**模型密钥路径**：

    ![绑定职业](collection-view-images/proto06.png)
10. 保存对 `.xib` 文件所做的更改，并返回到 Visual Studio 以同步更改。

编辑 `EmployeeItemController.cs` 文件，使其类似于以下内容：

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using Foundation;
using AppKit;

namespace MacCollectionNew
{
    /// <summary>
    /// The Employee item controller handles the display of the individual items that will
    /// be displayed in the collection view as defined in the associated .XIB file.
    /// </summary>
    public partial class EmployeeItemController : NSCollectionViewItem
    {
        #region Private Variables
        /// <summary>
        /// The person that will be displayed.
        /// </summary>
        private PersonModel _person;
        #endregion

        #region Computed Properties
        // strongly typed view accessor
        public new EmployeeItem View
        {
            get
            {
                return (EmployeeItem)base.View;
            }
        }

        /// <summary>
        /// Gets or sets the person.
        /// </summary>
        /// <value>The person that this item belongs to.</value>
        [Export("Person")]
        public PersonModel Person
        {
            get { return _person; }
            set
            {
                WillChangeValue("Person");
                _person = value;
                DidChangeValue("Person");
            }
        }

        /// <summary>
        /// Gets or sets the color of the background for the item.
        /// </summary>
        /// <value>The color of the background.</value>
        public NSColor BackgroundColor {
            get { return Background.FillColor; }
            set { Background.FillColor = value; }
        }

        /// <summary>
        /// Gets or sets a value indicating whether this <see cref="T:MacCollectionNew.EmployeeItemController"/> is selected.
        /// </summary>
        /// <value><c>true</c> if selected; otherwise, <c>false</c>.</value>
        /// <remarks>This also changes the background color based on the selected state
        /// of the item.</remarks>
        public override bool Selected
        {
            get
            {
                return base.Selected;
            }
            set
            {
                base.Selected = value;

                // Set background color based on the selection state
                if (value) {
                    BackgroundColor = NSColor.DarkGray;
                } else {
                    BackgroundColor = NSColor.LightGray;
                }
            }
        }
        #endregion

        #region Constructors
        // Called when created from unmanaged code
        public EmployeeItemController(IntPtr handle) : base(handle)
        {
            Initialize();
        }

        // Called when created directly from a XIB file
        [Export("initWithCoder:")]
        public EmployeeItemController(NSCoder coder) : base(coder)
        {
            Initialize();
        }

        // Call to load from the XIB/NIB file
        public EmployeeItemController() : base("EmployeeItem", NSBundle.MainBundle)
        {
            Initialize();
        }

        // Added to support loading from XIB/NIB
        public EmployeeItemController(string nibName, NSBundle nibBundle) : base(nibName, nibBundle) {

            Initialize();
        }

        // Shared initialization code
        void Initialize()
        {
        }
        #endregion
    }
}
```

查看此代码的详细信息，类从 `NSCollectionViewItem` 继承，因此它可以充当集合视图单元的原型。 `Person` 属性公开用于数据绑定到 Xcode 中的图像视图和标签的类。 这是上面创建的 `PersonModel` 的实例。

`BackgroundColor` 属性是 `NSBox` 控件 `FillColor` 的快捷方式，将用于显示单元格的选择状态。 通过重写 `NSCollectionViewItem`的 `Selected` 属性，以下代码设置或清除此选择状态：

```csharp
public override bool Selected
{
    get
    {
        return base.Selected;
    }
    set
    {
        base.Selected = value;

        // Set background color based on the selection state
        if (value) {
            BackgroundColor = NSColor.DarkGray;
        } else {
            BackgroundColor = NSColor.LightGray;
        }
    }
}
```

<a name="Creating-the-Collection-View-Data-Source"/>

### <a name="creating-the-collection-view-data-source"></a>创建集合视图数据源

集合视图数据源（`NSCollectionViewDataSource`）提供集合视图的所有数据，并根据集合中每一项的需要，创建并填充集合视图单元格（使用 `.xib` 原型）。

将新类添加到项目，将其调用 `CollectionViewDataSource` 并使其类似于以下内容：

```csharp
using System;
using System.Collections.Generic;
using AppKit;
using Foundation;

namespace MacCollectionNew
{
    /// <summary>
    /// Collection view data source provides the data for the collection view.
    /// </summary>
    public class CollectionViewDataSource : NSCollectionViewDataSource
    {
        #region Computed Properties
        /// <summary>
        /// Gets or sets the parent collection view.
        /// </summary>
        /// <value>The parent collection view.</value>
        public NSCollectionView ParentCollectionView { get; set; }

        /// <summary>
        /// Gets or sets the data that will be displayed in the collection.
        /// </summary>
        /// <value>A collection of PersonModel objects.</value>
        public List<PersonModel> Data { get; set; } = new List<PersonModel>();
        #endregion

        #region Constructors
        /// <summary>
        /// Initializes a new instance of the <see cref="T:MacCollectionNew.CollectionViewDataSource"/> class.
        /// </summary>
        /// <param name="parent">The parent collection that this datasource will provide data for.</param>
        public CollectionViewDataSource(NSCollectionView parent)
        {
            // Initialize
            ParentCollectionView = parent;

            // Attach to collection view
            parent.DataSource = this;

        }
        #endregion

        #region Override Methods
        /// <summary>
        /// Gets the number of sections.
        /// </summary>
        /// <returns>The number of sections.</returns>
        /// <param name="collectionView">The parent Collection view.</param>
        public override nint GetNumberOfSections(NSCollectionView collectionView)
        {
            // There is only one section in this view
            return 1;
        }

        /// <summary>
        /// Gets the number of items in the given section.
        /// </summary>
        /// <returns>The number of items.</returns>
        /// <param name="collectionView">The parent Collection view.</param>
        /// <param name="section">The Section number to count items for.</param>
        public override nint GetNumberofItems(NSCollectionView collectionView, nint section)
        {
            // Return the number of items
            return Data.Count;
        }

        /// <summary>
        /// Gets the item for the give section and item index.
        /// </summary>
        /// <returns>The item.</returns>
        /// <param name="collectionView">The parent Collection view.</param>
        /// <param name="indexPath">Index path specifying the section and index.</param>
        public override NSCollectionViewItem GetItem(NSCollectionView collectionView, NSIndexPath indexPath)
        {
            var item = collectionView.MakeItem("EmployeeCell", indexPath) as EmployeeItemController;
            item.Person = Data[(int)indexPath.Item];

            return item;
        }
        #endregion
    }
}
```

查看此代码的详细信息，类继承自 `NSCollectionViewDataSource`，并通过其 `Data` 属性公开 `PersonModel` 实例的列表。

由于此集合只有一个部分，因此代码会重写 `GetNumberOfSections` 方法，并始终返回 `1`。 此外，`GetNumberofItems` 方法将在其返回 `Data` 属性列表中的项数时被重写。

只要需要新的单元格，就会调用 `GetItem` 方法，如下所示：

```csharp
public override NSCollectionViewItem GetItem(NSCollectionView collectionView, NSIndexPath indexPath)
{
    var item = collectionView.MakeItem("EmployeeCell", indexPath) as EmployeeItemController;
    item.Person = Data[(int)indexPath.Item];

    return item;
}
```

将调用集合视图的 `MakeItem` 方法来创建或返回 `EmployeeItemController` 的可重用实例，并将其 `Person` 属性设置为所请求单元中显示的项。 

使用以下代码，必须预先向集合视图控制器注册 `EmployeeItemController`：

```csharp
EmployeeCollection.RegisterClassForItem(typeof(EmployeeItemController), "EmployeeCell");
``` 

`MakeItem` 调用中使用的**标识符**（`EmployeeCell`）_必须_与在集合视图中注册的视图控制器的名称相匹配。 下面将详细介绍此步骤。

<a name="Handling-Item-Selection"/>

### <a name="handling-item-selection"></a>处理项选择

若要处理集合中项的选择和 deselection，则需要 `NSCollectionViewDelegate`。 由于此示例将使用内置 `NSCollectionViewFlowLayout` 布局类型，因此将需要此委托的 `NSCollectionViewDelegateFlowLayout` 特定版本。

向项目中添加一个新类，将其调用 `CollectionViewDelegate` 并使其类似于以下内容：

```csharp
using System;
using Foundation;
using AppKit;

namespace MacCollectionNew
{
    /// <summary>
    /// Collection view delegate handles user interaction with the elements of the 
    /// collection view for the Flow-Based layout type.
    /// </summary>
    public class CollectionViewDelegate : NSCollectionViewDelegateFlowLayout
    {
        #region Computed Properties
        /// <summary>
        /// Gets or sets the parent view controller.
        /// </summary>
        /// <value>The parent view controller.</value>
        public ViewController ParentViewController { get; set; }
        #endregion

        #region Constructors
        /// <summary>
        /// Initializes a new instance of the <see cref="T:MacCollectionNew.CollectionViewDelegate"/> class.
        /// </summary>
        /// <param name="parentViewController">Parent view controller.</param>
        public CollectionViewDelegate(ViewController parentViewController)
        {
            // Initialize
            ParentViewController = parentViewController;
        }
        #endregion

        #region Override Methods
        /// <summary>
        /// Handles one or more items being selected.
        /// </summary>
        /// <param name="collectionView">The parent Collection view.</param>
        /// <param name="indexPaths">The Index paths of the items being selected.</param>
        public override void ItemsSelected(NSCollectionView collectionView, NSSet indexPaths)
        {
            // Dereference path
            var paths = indexPaths.ToArray<NSIndexPath>();
            var index = (int)paths[0].Item;

            // Save the selected item
            ParentViewController.PersonSelected = ParentViewController.Datasource.Data[index];

        }

        /// <summary>
        /// Handles one or more items being deselected.
        /// </summary>
        /// <param name="collectionView">The parent Collection view.</param>
        /// <param name="indexPaths">The Index paths of the items being deselected.</param>
        public override void ItemsDeselected(NSCollectionView collectionView, NSSet indexPaths)
        {
            // Dereference path
            var paths = indexPaths.ToArray<NSIndexPath>();
            var index = paths[0].Item;

            // Clear selection
            ParentViewController.PersonSelected = null;
        }
        #endregion
    }
}
``` 

当用户选择或取消选择项时，将重写 `ItemsSelected` 和 `ItemsDeselected` 方法，并用于设置或清除正在处理集合视图的视图控制器的 `PersonSelected` 属性。 下面将详细介绍这一点。

<a name="Creating-the-Collection-View-in-Interface-Builder"/>

### <a name="creating-the-collection-view-in-interface-builder"></a>在 Interface Builder 中创建集合视图

使用所需的所有支持块后，可以编辑主情节提要，并向其添加集合视图。

请执行以下操作：

1. 双击**解决方案资源管理器**中的 `Main.Storyboard` 文件，将其打开，以便在 Xcode 的 Interface Builder 中进行编辑。
2. 将集合视图拖到主视图中，并调整其大小以填充视图：

    ![向布局添加集合视图](collection-view-images/collection01.png)
3. 选择集合视图后，在调整大小后使用约束编辑器将其固定到视图：

    ![添加约束](collection-view-images/collection02.png)
4. 确保在 " **Design Surface** （而不是包含它的带边框的**滚动视图**或**剪辑视图**）中选择" 集合 "视图，切换到"**助手编辑器**"并为" 集合 "视图创建一个**插座**：

    ![添加约束](collection-view-images/collection03.png)
5. 保存更改并返回到 Visual Studio 以进行同步。

<a name="Bringing-it-all-Together"/>

## <a name="bringing-it-all-together"></a>全部整合

现在，所有支持部分都已与一个类结合使用，该类可用作数据模型（`PersonModel`）、添加了一个 `NSCollectionViewDataSource` 来提供数据、创建了 `NSCollectionViewDelegateFlowLayout` 来处理项选择和将 `NSCollectionView` 添加到主情节提要以及作为插座公开（`EmployeeCollection`）。

最后一步是编辑包含集合视图的视图控制器，并将所有部分组合在一起，以填充集合并处理项选择。

编辑 `ViewController.cs` 文件，使其类似于以下内容：

```csharp
using System;
using AppKit;
using Foundation;
using CoreGraphics;

namespace MacCollectionNew
{
    /// <summary>
    /// The View controller controls the main view that houses the Collection View.
    /// </summary>
    public partial class ViewController : NSViewController
    {
        #region Private Variables
        private PersonModel _personSelected;
        private bool shouldEdit = true;
        #endregion

        #region Computed Properties
        /// <summary>
        /// Gets or sets the datasource that provides the data to display in the 
        /// Collection View.
        /// </summary>
        /// <value>The datasource.</value>
        public CollectionViewDataSource Datasource { get; set; }

        /// <summary>
        /// Gets or sets the person currently selected in the collection view.
        /// </summary>
        /// <value>The person selected or <c>null</c> if no person is selected.</value>
        [Export("PersonSelected")]
        public PersonModel PersonSelected
        {
            get { return _personSelected; }
            set
            {
                WillChangeValue("PersonSelected");
                _personSelected = value;
                DidChangeValue("PersonSelected");
                RaiseSelectionChanged();
            }
        }
        #endregion

        #region Constructors
        /// <summary>
        /// Initializes a new instance of the <see cref="T:MacCollectionNew.ViewController"/> class.
        /// </summary>
        /// <param name="handle">Handle.</param>
        public ViewController(IntPtr handle) : base(handle)
        {
        }
        #endregion

        #region Override Methods
        /// <summary>
        /// Called after the view has finished loading from the Storyboard to allow it to
        /// be configured before displaying to the user.
        /// </summary>
        public override void ViewDidLoad()
        {
            base.ViewDidLoad();

            // Initialize Collection View
            ConfigureCollectionView();
            PopulateWithData();
        }
        #endregion

        #region Private Methods
        /// <summary>
        /// Configures the collection view.
        /// </summary>
        private void ConfigureCollectionView()
        {
            EmployeeCollection.RegisterClassForItem(typeof(EmployeeItemController), "EmployeeCell");

            // Create a flow layout
            var flowLayout = new NSCollectionViewFlowLayout()
            {
                ItemSize = new CGSize(150, 150),
                SectionInset = new NSEdgeInsets(10, 10, 10, 20),
                MinimumInteritemSpacing = 10,
                MinimumLineSpacing = 10
            };
            EmployeeCollection.WantsLayer = true;

            // Setup collection view
            EmployeeCollection.CollectionViewLayout = flowLayout;
            EmployeeCollection.Delegate = new CollectionViewDelegate(this);

        }

        /// <summary>
        /// Populates the Datasource with data and attaches it to the collection view.
        /// </summary>
        private void PopulateWithData()
        {
            // Make datasource
            Datasource = new CollectionViewDataSource(EmployeeCollection);

            // Build list of employees
            Datasource.Data.Add(new PersonModel("Craig Dunn", "Documentation Manager", true));
            Datasource.Data.Add(new PersonModel("Amy Burns", "Technical Writer"));
            Datasource.Data.Add(new PersonModel("Joel Martinez", "Web & Infrastructure"));
            Datasource.Data.Add(new PersonModel("Kevin Mullins", "Technical Writer"));
            Datasource.Data.Add(new PersonModel("Mark McLemore", "Technical Writer"));
            Datasource.Data.Add(new PersonModel("Tom Opgenorth", "Technical Writer"));
            Datasource.Data.Add(new PersonModel("Larry O'Brien", "API Docs Manager", true));
            Datasource.Data.Add(new PersonModel("Mike Norman", "API Documentor"));

            // Populate collection view
            EmployeeCollection.ReloadData();
        }
        #endregion

        #region Events
        /// <summary>
        /// Selection changed delegate.
        /// </summary>
        public delegate void SelectionChangedDelegate();

        /// <summary>
        /// Occurs when selection changed.
        /// </summary>
        public event SelectionChangedDelegate SelectionChanged;

        /// <summary>
        /// Raises the selection changed event.
        /// </summary>
        internal void RaiseSelectionChanged() {
            // Inform caller
            if (this.SelectionChanged != null) SelectionChanged();
        }
        #endregion
    }
}
```

查看此代码的详细信息，定义了一个 `Datasource` 属性，用于保存将为集合视图提供数据的 `CollectionViewDataSource` 的实例。 定义了一个 `PersonSelected` 属性，用于保存表示集合视图中当前选定项的 `PersonModel`。 当所选内容更改时，此属性还会引发 `SelectionChanged` 事件。

`ConfigureCollectionView` 类用于注册使用集合视图作为单元原型的视图控制器，使用以下行：

```csharp
EmployeeCollection.RegisterClassForItem(typeof(EmployeeItemController), "EmployeeCell");
```

请注意，用于注册原型的标识符（`EmployeeCell`）与前面定义的 `CollectionViewDataSource` 的 `GetItem` 方法中调用的**标识符**（）匹配：

```csharp
var item = collectionView.MakeItem("EmployeeCell", indexPath) as EmployeeItemController;
...
```

此外，视图控制器的类型**必须**与定义原型的 `.xib` 文件的名称**完全**匹配。 在此示例中，`EmployeeItemController` 和 `EmployeeItemController.xib`。

集合视图中项的实际布局由集合视图布局类控制，可以通过将新实例分配到 `CollectionViewLayout` 属性在运行时动态更改。 更改此属性将更新集合视图外观，而不会对更改进行动画处理。

Apple 提供了两种内置布局类型，其中的集合视图将处理最典型的使用： `NSCollectionViewFlowLayout` 和 `NSCollectionViewGridLayout`。 如果开发人员需要自定义格式（例如，在圆形中布置项），则可以创建 `NSCollectionViewLayout` 的自定义实例，并覆盖所需的方法以实现所需的效果。

此示例使用默认流布局，以便它创建 `NSCollectionViewFlowLayout` 类的实例，并按如下所示对其进行配置：

```csharp
var flowLayout = new NSCollectionViewFlowLayout()
{
    ItemSize = new CGSize(150, 150),
    SectionInset = new NSEdgeInsets(10, 10, 10, 20),
    MinimumInteritemSpacing = 10,
    MinimumLineSpacing = 10
};
```

`ItemSize` 属性定义集合中每个单独单元格的大小。 `SectionInset` 属性定义单元格将在其中布局的集合的边缘的嵌入。 `MinimumInteritemSpacing` 定义项之间的最小间距，`MinimumLineSpacing` 定义集合中各行之间的最小间距。

布局将分配给集合视图，并附加 `CollectionViewDelegate` 的实例来处理项选择：

```csharp
// Setup collection view
EmployeeCollection.CollectionViewLayout = flowLayout;
EmployeeCollection.Delegate = new CollectionViewDelegate(this);
```

`PopulateWithData` 方法创建 `CollectionViewDataSource`的新实例，使用数据填充该实例，将其附加到集合视图，并调用 `ReloadData` 方法来显示这些项：

```csharp
private void PopulateWithData()
{
    // Make datasource
    Datasource = new CollectionViewDataSource(EmployeeCollection);

    // Build list of employees
    Datasource.Data.Add(new PersonModel("Craig Dunn", "Documentation Manager", true));
    ...

    // Populate collection view
    EmployeeCollection.ReloadData();
}
```

重写 `ViewDidLoad` 方法，并调用 `ConfigureCollectionView` 和 `PopulateWithData` 方法来向用户显示最终的集合视图：

```csharp
public override void ViewDidLoad()
{
    base.ViewDidLoad();

    // Initialize Collection View
    ConfigureCollectionView();
    PopulateWithData();
}
```

<a name="Summary"/>

## <a name="summary"></a>总结

本文详细介绍了如何在 Xamarin. Mac 应用程序中使用集合视图。 首先，它介绍了如何使用C#键/值编码（KVC）和键-值观察（KVO）向目标-C 公开一个类。 接下来，该示例演示如何使用 KVO 兼容类并将其绑定到 Xcode 的 Interface Builder 中的集合视图。 最后，它演示了如何在代码中C#与集合视图交互。

## <a name="related-links"></a>相关链接

- [MacCollectionNew （示例）](https://docs.microsoft.com/samples/xamarin/mac-samples/maccollectionnew)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [数据绑定和键值编码](~/mac/app-fundamentals/databinding.md)
- [NSCollectionView](https://developer.apple.com/reference/appkit/nscollectionview)
- [OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
