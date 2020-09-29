---
title: 在 Xamarin 中使用 tvOS 集合视图
description: 本文档介绍如何在使用 Xamarin 生成的 tvOS 应用中使用集合视图。 它介绍了集合视图布局、创建单元格和辅助视图、响应用户事件等。
ms.prod: xamarin
ms.assetid: 5125C4C7-2DDF-4C19-A362-17BB2B079178
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 2adc161742e7891fcce65cf2025a1b7c531c7a39
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435238"
---
# <a name="working-with-tvos-collection-views-in-xamarin"></a>在 Xamarin 中使用 tvOS 集合视图

集合视图允许使用任意布局显示一组内容。 使用内置支持，可以轻松创建类似于网格的类似或线性布局，同时还支持自定义布局。

[![示例集合视图](collection-views-images/collection01.png)](collection-views-images/collection01.png#lightbox)

集合视图使用委托和数据源维护项的集合，以提供用户交互和集合内容。 由于集合视图基于独立于视图本身的布局子系统，因此提供不同布局可以轻松地即时更改集合视图数据的显示方式。

<a name="About-Collection-Views"></a>

## <a name="about-collection-views"></a>关于集合视图

如上所述，收集视图 (`UICollectionView`) 管理有序的项集合，并显示具有可自定义布局的项。 集合视图的工作方式类似于 () 的表视图 `UITableView` ，只不过它们可以使用布局来提供多个只包含在单个列中的项。

使用 tvOS 中的集合视图时，应用负责使用数据源 () 来提供与集合关联的数据 `UICollectionViewDataSource` 。 可以选择组织集合视图数据并将其显示到不同的组中 (节) 。

"集合" 视图使用单元格 () 提供一个单元格，该单元格 `UICollectionViewCell` 提供集合中给定信息的表示形式 (如图像及其标题) 。

或者，可以将补充视图添加到集合视图的演示中，以用作节和单元格的页眉和页脚。 集合视图的布局负责定义这些视图的位置以及单个单元。

集合视图可以使用委托 () 来响应用户交互 `UICollectionViewDelegate` 。 如果已突出显示某个单元格或已选中某个单元格，此委托还负责确定给定的单元格是否可以获得焦点。 在某些情况下，委托确定各个单元格的大小。

<a name="Collection-View-Layouts"></a>

## <a name="collection-view-layouts"></a>集合视图布局

集合视图的一项重要功能是其所呈现的数据与其布局之间的分离。 "集合视图布局 `UICollectionViewLayout` " () 负责提供组织和单元格位置 (以及在集合视图的屏幕演示中) 与任何补充视图一起使用。

单个单元格由集合视图从其附加数据源创建，然后按给定的集合视图布局进行排列和显示。

集合视图布局通常在创建集合视图时提供。 不过，您可以随时更改 "集合" 视图布局，并使用提供的新布局自动更新 "集合" 视图的数据的屏幕显示形式。

集合视图布局提供多种方法，这些方法可用于对两个不同布局之间的转换进行动画处理 (默认情况下，不会) 完成任何动画。 此外，集合视图布局可以与手势识别器结合使用，以进一步动画显示导致布局变化的用户交互。

<a name="Creating-Cells-and-Supplementary-Views"></a>

## <a name="creating-cells-and-supplementary-views"></a>创建单元和辅助视图

集合视图的数据源不仅负责提供支持集合项的数据，还负责显示内容所用的单元。

由于集合视图被设计为处理大的项集合，因此可以取消排队并重复使用单个单元，以防止 overrunning 的内存限制。 取消排队视图有两种不同的方法：

- `DequeueReusableCell` -按照应用的情节提要) 中指定的方式创建或返回给定类型的单元格 (。
- `DequeueReusableSupplementaryView` -按照应用的情节提要) 中指定的方式创建或返回给定类型的辅助视图 (。

在调用这些方法中的任何一种之前，必须注册用于使用 `.xib` 集合视图创建单元格视图的类、情节提要或文件。 例如：

```csharp
public CityCollectionView (IntPtr handle) : base (handle)
{
    // Initialize
    RegisterClassForCell (typeof(CityCollectionViewCell), CityViewDatasource.CardCellId);
    ...
}
```

其中 `typeof(CityCollectionViewCell)` 提供支持视图的类，并 `CityViewDatasource.CardCellId` 提供在取消排队 (或视图) 时使用的 ID。

取消对单元格的排队后，您可以对其进行配置，使其包含它所表示的项的数据，并返回到要显示的集合视图。

<a name="About-Collection-View-Controllers"></a>

## <a name="about-collection-view-controllers"></a>关于集合视图控制器

集合视图控制器 (`UICollectionViewController`) 是专用视图控制器 (`UIViewController`) 提供以下行为：

- 它负责从其情节提要或文件加载集合视图 `.xib` ，并实例化视图。 如果在代码中创建，它将自动创建一个新的、未配置的集合视图。
- 加载集合视图后，控制器会尝试从情节提要或文件加载其数据源和委托 `.xib` 。 如果没有可用的，它会将其自身设置为源。
- 确保在第一次显示集合视图之前加载数据，并重新加载并清除每个后续显示的选择。

此外，集合视图控制器还提供可重写的方法，这些方法可用于管理集合视图（例如和）的生命周期 `AwakeFromNib` `ViewWillDisplay` 。

<a name="Collection-Views-and-Storyboards"></a>

## <a name="collection-views-and-storyboards"></a>集合视图和情节提要

若要在 tvOS 应用程序中使用集合视图，最简单的方法是将其添加到其情节提要中。 作为一个简单的示例，我们将创建一个显示图像、标题和 "选择" 按钮的示例应用。 如果用户单击 "选择" 按钮，将显示允许用户选择新图像的集合视图。 选择图像后，将关闭集合视图，并显示新图像和标题。

让我们执行以下操作：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 Visual Studio for Mac 中启动新的 **单一视图 TvOS 应用** 。
1. 在 **解决方案资源管理器**中，双击该 `Main.storyboard` 文件，然后在 iOS 设计器中将其打开。
1. 向现有视图添加一个图像视图、一个标签和一个按钮，并将其配置为如下所示： 

    [![示例布局](collection-views-images/collection02.png)](collection-views-images/collection02.png#lightbox)
1. 在 "**属性资源管理器**" 的 "**小组件" 选项卡**中为图像视图和标签指定一个**名称**。 例如： 

    [![设置名称](collection-views-images/collection03.png)](collection-views-images/collection03.png#lightbox)
1. 接下来，将集合视图控制器拖动到情节提要： 

    [![集合视图控制器](collection-views-images/collection04.png)](collection-views-images/collection04.png#lightbox)
1. Control-从按钮拖动到集合视图控制器，然后从弹出窗口中选择 " **推送** "： 

    [![从弹出窗口中选择 "推送"](collection-views-images/collection05.png)](collection-views-images/collection05.png#lightbox)
1. 当应用运行时，这将使集合视图在用户单击按钮时显示。
1. 选择集合视图，并在 "**属性资源管理器**" 的 "**布局" 选项卡**中输入以下值： 

    [![属性资源管理器](collection-views-images/collection06.png)](collection-views-images/collection06.png#lightbox)
1. 这会控制单个单元格的大小和集合视图的单元格与外边缘之间的边框。
1. 选择集合视图控制器，并 `CityCollectionViewController` 在 " **小组件" 选项卡**中将其类设置为： 

    [![将类设置为 CityCollectionViewController](collection-views-images/collection07.png)](collection-views-images/collection07.png#lightbox)
1. 选择集合视图，并 `CityCollectionView` 在 " **小组件" 选项卡**中将其类设置为： 

    [![将类设置为 CityCollectionView](collection-views-images/collection08.png)](collection-views-images/collection08.png#lightbox)
1. 选择集合视图单元，并 `CityCollectionViewCell` 在 " **小组件" 选项卡**中将其类设置为： 

    [![将类设置为 CityCollectionViewCell](collection-views-images/collection09.png)](collection-views-images/collection09.png#lightbox)
1. 在 " **小组件" 选项卡** 中，确保 **布局** 为 `Flow` ， **滚动方向** 为 `Vertical` "集合" 视图： 

    [!["小组件" 选项卡](collection-views-images/collection10.png)](collection-views-images/collection10.png#lightbox)
1. 选择集合视图单元并**Identity** `CityCell` 在 "**小组件" 选项卡**中将其标识设置为： 

    [![将标识设置为 CityCell](collection-views-images/collection11.png)](collection-views-images/collection11.png#lightbox)
1. 保存所做更改。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 在 Visual Studio 中启动新的 **单一视图 TvOS 应用** 。
1. 在 **解决方案资源管理器**中，双击该 `Main.storyboard` 文件，然后在 iOS 设计器中将其打开。
1. 向现有视图添加一个图像视图、一个标签和一个按钮，并将其配置为如下所示： 

    [![配置布局](collection-views-images/collection02vs.png)](collection-views-images/collection02vs.png#lightbox)
1. 在 "**属性资源管理器**" 的 "**小组件" 选项卡**中为图像视图和标签指定一个**名称**。 例如： 

    [![属性资源管理器](collection-views-images/collection03vs.png)](collection-views-images/collection03vs.png#lightbox)
1. 接下来，将集合视图控制器拖动到情节提要： 

    [![集合视图控制器](collection-views-images/collection04vs.png)](collection-views-images/collection04vs.png#lightbox)
1. Control-从按钮拖动到集合视图控制器，然后从弹出窗口中选择 " **推送** "： 

    [![从弹出窗口中选择 "推送"](collection-views-images/collection05vs.png)](collection-views-images/collection05vs.png#lightbox)
1. 当应用运行时，这将使集合视图在用户单击按钮时显示。
1. 选择 "集合" 视图，并在 "**属性资源管理器**" 的 "**布局" 选项卡**中，将**宽度**输入为_361_ ，**高度**为_256_ 
1. 这会控制单个单元格的大小和集合视图的单元格与外边缘之间的边框。
1. 选择集合视图控制器，并 `CityCollectionViewController` 在 " **小组件" 选项卡**中将其类设置为： 

    [![将类设置为 CityCollectionViewController](collection-views-images/collection07vs.png)](collection-views-images/collection07vs.png#lightbox)
1. 选择集合视图，并 `CityCollectionView` 在 " **小组件" 选项卡**中将其类设置为： 

    [![将类设置为 CityCollectionView](collection-views-images/collection08vs.png)](collection-views-images/collection08vs.png#lightbox)
1. 选择集合视图单元，并 `CityCollectionViewCell` 在 " **小组件" 选项卡**中将其类设置为： 

    [![将类设置为 CityCollectionViewCell](collection-views-images/collection09vs.png)](collection-views-images/collection09vs.png#lightbox)
1. 在 " **小组件" 选项卡** 中，确保 **布局** 为 `Flow` ， **滚动方向** 为 `Vertical` "集合" 视图： 

    [!["小组件" 选项卡](collection-views-images/collection10vs.png)](collection-views-images/collection10vs.png#lightbox)
1. 选择集合视图单元并**Identity** `CityCell` 在 "**小组件" 选项卡**中将其标识设置为： 

    [![将标识设置为 CityCell](collection-views-images/collection11vs.png)](collection-views-images/collection11vs.png#lightbox)
1. 保存所做更改。

-----

如果选择了 `Custom` "集合" 视图的 **布局**，则可以指定自定义布局。 Apple 提供了一个内置的 `UICollectionViewFlowLayout` ， `UICollectionViewDelegateFlowLayout` 可以在基于网格的布局中轻松地呈现数据 (这些数据由 `flow` 布局样式) 使用。 

有关使用情节提要的详细信息，请参阅 [tvOS 快速入门指南](~/ios/tvos/get-started/hello-tvos.md)。

<a name="Providing-Data-for-the-Collection-View"></a>

## <a name="providing-data-for-the-collection-view"></a>为集合视图提供数据

现在我们已将集合视图 (和集合视图控制器) 添加到了情节提要，接下来需要为集合提供数据。 

<a name="The-Data-Model"></a>

### <a name="the-data-model"></a>数据模型

首先，我们将为数据创建一个模型，其中包含要显示的图像的文件名、标题和允许选择城市的标志。

创建一个 `CityInfo` 类，使其看起来像下面这样：

```csharp
using System;

namespace tvCollection
{
    public class CityInfo
    {
        #region Computed Properties
        public string ImageFilename { get; set; }
        public string Title { get; set; }
        public bool CanSelect{ get; set; }
        #endregion

        #region Constructors
        public CityInfo (string filename, string title, bool canSelect)
        {
            // Initialize
            this.ImageFilename = filename;
            this.Title = title;
            this.CanSelect = canSelect;
        }
        #endregion
    }
}
```

### <a name="the-collection-view-cell"></a>集合视图单元格

现在，我们需要定义每个单元格的数据显示方式。 编辑 `CityCollectionViewCell.cs` (自动从情节提要文件创建的文件) 并使其类似于以下内容：

```csharp
using System;
using Foundation;
using UIKit;
using CoreGraphics;

namespace tvCollection
{
    public partial class CityCollectionViewCell : UICollectionViewCell
    {
        #region Private Variables
        private CityInfo _city;
        #endregion

        #region Computed Properties
        public UIImageView CityView { get ; set; }
        public UILabel CityTitle { get; set; }

        public CityInfo City {
            get { return _city; }
            set {
                _city = value;
                CityView.Image = UIImage.FromFile (City.ImageFilename);
                CityView.Alpha = (City.CanSelect) ? 1.0f : 0.5f;
                CityTitle.Text = City.Title;
            }
        }
        #endregion

        #region Constructors
        public CityCollectionViewCell (IntPtr handle) : base (handle)
        {
            // Initialize
            CityView = new UIImageView(new CGRect(22, 19, 320, 171));
            CityView.AdjustsImageWhenAncestorFocused = true;
            AddSubview (CityView);

            CityTitle = new UILabel (new CGRect (22, 209, 320, 21)) {
                TextAlignment = UITextAlignment.Center,
                TextColor = UIColor.White,
                Alpha = 0.0f
            };
            AddSubview (CityTitle);
        }
        #endregion
    

    }
}
```

对于 tvOS 应用，我们将显示一个图像和一个可选的标题。 如果无法选择给定的 city，则使用以下代码将图像视图变暗：

```csharp
CityView.Alpha = (City.CanSelect) ? 1.0f : 0.5f;
```

当用户将包含图像的单元格集中在一起时，我们想要对其设置以下属性，使用内置的视差效果：

```csharp
CityView.AdjustsImageWhenAncestorFocused = true;
```

有关导航和焦点的详细信息，请参阅 [使用导航和焦点](~/ios/tvos/app-fundamentals/navigation-focus.md) 和 [Siri 远程和蓝牙控制器](~/ios/tvos/platform/remote-bluetooth.md) 文档。

<a name="The-Collection-View-Data-Provider"></a>

### <a name="the-collection-view-data-provider"></a>集合视图数据提供程序

创建数据模型并定义单元布局后，让我们为集合视图创建数据源。 数据源不仅负责提供支持数据，而且还会取消排队单元格以在屏幕上显示单个单元。

创建一个 `CityViewDatasource` 类，使其看起来像下面这样：

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Foundation;
using CoreGraphics;
using ObjCRuntime;

namespace tvCollection
{
    public class CityViewDatasource : UICollectionViewDataSource
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Static Constants
        public static NSString CardCellId = new NSString ("CityCell");
        #endregion

        #region Computed Properties
        public List<CityInfo> Cities { get; set; } = new List<CityInfo>();
        public CityCollectionView ViewController { get; set; }
        #endregion

        #region Constructors
        public CityViewDatasource (CityCollectionView controller)
        {
            // Initialize
            this.ViewController = controller;
            PopulateCities ();
        }
        #endregion

        #region Public Methods
        public void PopulateCities() {

            // Clear existing cities
            Cities.Clear();

            // Add new cities
            Cities.Add(new CityInfo("City01.jpg", "Houses by Water", false));
            Cities.Add(new CityInfo("City02.jpg", "Turning Circle", true));
            Cities.Add(new CityInfo("City03.jpg", "Skyline at Night", true));
            Cities.Add(new CityInfo("City04.jpg", "Golden Gate Bridge", true));
            Cities.Add(new CityInfo("City05.jpg", "Roads by Night", true));
            Cities.Add(new CityInfo("City06.jpg", "Church Domes", true));
            Cities.Add(new CityInfo("City07.jpg", "Mountain Lights", true));
            Cities.Add(new CityInfo("City08.jpg", "City Scene", false));
            Cities.Add(new CityInfo("City09.jpg", "House in Winter", true));
            Cities.Add(new CityInfo("City10.jpg", "By the Lake", true));
            Cities.Add(new CityInfo("City11.jpg", "At the Dome", true));
            Cities.Add(new CityInfo("City12.jpg", "Cityscape", true));
            Cities.Add(new CityInfo("City13.jpg", "Model City", true));
            Cities.Add(new CityInfo("City14.jpg", "Taxi, Taxi!", true));
            Cities.Add(new CityInfo("City15.jpg", "On the Sidewalk", true));
            Cities.Add(new CityInfo("City16.jpg", "Midnight Walk", true));
            Cities.Add(new CityInfo("City17.jpg", "Lunchtime Cafe", true));
            Cities.Add(new CityInfo("City18.jpg", "Coffee Shop", true));
            Cities.Add(new CityInfo("City19.jpg", "Rustic Tavern", true));
        }
        #endregion

        #region Override Methods
        public override nint NumberOfSections (UICollectionView collectionView)
        {
            return 1;
        }

        public override nint GetItemsCount (UICollectionView collectionView, nint section)
        {
            return Cities.Count;
        }

        public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
        {
            var cityCell = (CityCollectionViewCell)collectionView.DequeueReusableCell (CardCellId, indexPath);
            var city = Cities [indexPath.Row];

            // Initialize city
            cityCell.City = city;

            return cityCell;
        }
        #endregion
    }
}
```

让我们详细了解此类。 首先，我们从继承 `UICollectionViewDataSource` ，并为在 IOS 设计器中分配的单元 ID (提供一个快捷方式) ：

```csharp
public static NSString CardCellId = new NSString ("CityCell");
```

接下来，我们为集合数据提供存储空间，并提供一个用于填充数据的类：

```csharp
public List<CityInfo> Cities { get; set; } = new List<CityInfo>();
...

public void PopulateCities() {

    // Clear existing cities
    Cities.Clear();

    // Add new cities
    Cities.Add(new CityInfo("City01.jpg", "Houses by Water", false));
    Cities.Add(new CityInfo("City02.jpg", "Turning Circle", true));
    ...
}
```

然后，重写 `NumberOfSections` 方法，并返回集合视图) 的项 (组的数目。 在这种情况下，只有一个：

```csharp
public override nint NumberOfSections (UICollectionView collectionView)
{
    return 1;
}
```

接下来，使用以下代码返回集合中的项数：

```csharp
public override nint GetItemsCount (UICollectionView collectionView, nint section)
{
    return Cities.Count;
}
```

最后，当集合视图请求带有以下代码时，我们将取消可重用单元的排队：

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
{
    var cityCell = (CityCollectionViewCell)collectionView.DequeueReusableCell (CardCellId, indexPath);
    var city = Cities [indexPath.Row];

    // Initialize city
    cityCell.City = city;

    return cityCell;
}
```

获取类型的集合视图单元后，将 `CityCollectionViewCell` 使用给定的项来填充它。

<a name="Responding-to-User-Events"></a>

## <a name="responding-to-user-events"></a>响应用户事件

由于我们希望用户能够从我们的集合中选择项，因此，我们需要提供一个集合视图委托来处理此交互。 我们还需要提供一种方法，让我们的调用视图知道用户所选择的项。

<a name="The-App-Delegate"></a>

### <a name="the-app-delegate"></a>应用委托

我们需要一种方法将当前所选项从集合视图关联回调用视图。 我们将在上使用自定义属性 `AppDelegate` 。 编辑 `AppDelegate.cs` 文件并添加以下代码：

```csharp
public CityInfo SelectedCity { get; set;} = new CityInfo("City02.jpg", "Turning Circle", true);
```

这将定义属性，并设置最初将显示的默认城市。 稍后，我们将使用此属性来显示用户的选择并允许更改选择。

<a name="The-Collection-View-Delegate"></a>

### <a name="the-collection-view-delegate"></a>集合视图委托

接下来，向项目中添加一个新 `CityViewDelegate` 类，并使其类似于以下内容：

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Foundation;
using CoreGraphics;

namespace tvCollection
{
    public class CityViewDelegate : UICollectionViewDelegateFlowLayout
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Constructors
        public CityViewDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override CGSize GetSizeForItem (UICollectionView collectionView, UICollectionViewLayout layout, NSIndexPath indexPath)
        {
            return new CGSize (361, 256);
        }

        public override bool CanFocusItem (UICollectionView collectionView, NSIndexPath indexPath)
        {
            if (indexPath == null) {
                return false;
            } else {
                var controller = collectionView as CityCollectionView;
                return controller.Source.Cities[indexPath.Row].CanSelect;
            }
        }

        public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
        {
            var controller = collectionView as CityCollectionView;
            App.SelectedCity = controller.Source.Cities [indexPath.Row];

            // Close Collection
            controller.ParentController.DismissViewController(true,null);
        }
        #endregion
    }
}
```

让我们进一步了解一下此类。 首先，我们从继承 `UICollectionViewDelegateFlowLayout` 。 从此类而不是继承的原因 `UICollectionViewDelegate` 是我们使用的是内置的 `UICollectionViewFlowLayout` 来显示项，而不是自定义布局类型。

接下来，使用以下代码返回各个项的大小：

```csharp
public override CGSize GetSizeForItem (UICollectionView collectionView, UICollectionViewLayout layout, NSIndexPath indexPath)
{
    return new CGSize (361, 256);
}
```

然后，使用以下代码决定给定单元格是否可以获得焦点： 

```csharp
public override bool CanFocusItem (UICollectionView collectionView, NSIndexPath indexPath)
{
    if (indexPath == null) {
        return false;
    } else {
        var controller = collectionView as CityCollectionView;
        return controller.Source.Cities[indexPath.Row].CanSelect;
    }
}
```

我们检查一段给定的后备数据是否 `CanSelect` 将其标志设置为 `true` 并返回该值。 有关导航和焦点的详细信息，请参阅 [使用导航和焦点](~/ios/tvos/app-fundamentals/navigation-focus.md) 和 [Siri 远程和蓝牙控制器](~/ios/tvos/platform/remote-bluetooth.md) 文档。

最后，使用以下代码响应用户选择项：

```csharp
public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
{
    var controller = collectionView as CityCollectionView;
    App.SelectedCity = controller.Source.Cities [indexPath.Row];

    // Close Collection
    controller.ParentController.DismissViewController(true,null);
}
```

此处，我们将的 `SelectedCity` 属性设置 `AppDelegate` 为用户选择的项，并关闭集合视图控制器，返回到名为 us 的视图。 我们 `ParentController` 尚未定义集合视图的属性，接下来我们将执行此操作。

<a name="Configuring-the-Collection-View"></a>

## <a name="configuring-the-collection-view"></a>配置集合视图

现在，我们需要编辑集合视图，并分配数据源和委托。 编辑 `CityCollectionView.cs` 从情节提要) 自动为我们创建的文件 (，使其看起来像下面这样：

```csharp
using System;
using Foundation;
using UIKit;

namespace tvCollection
{
    public partial class CityCollectionView : UICollectionView
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Computed Properties
        public CityViewDatasource Source {
            get { return DataSource as CityViewDatasource;}
        }

        public CityCollectionViewController ParentController { get; set;}
        #endregion

        #region Constructors
        public CityCollectionView (IntPtr handle) : base (handle)
        {
            // Initialize
            RegisterClassForCell (typeof(CityCollectionViewCell), CityViewDatasource.CardCellId);
            DataSource = new CityViewDatasource (this);
            Delegate = new CityViewDelegate ();
        }
        #endregion

        #region Override Methods
        public override nint NumberOfSections ()
        {
            return 1;
        }

        public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
        {
            var previousItem = context.PreviouslyFocusedView as CityCollectionViewCell;
            if (previousItem != null) {
                Animate (0.2, () => {
                    previousItem.CityTitle.Alpha = 0.0f;
                });
            }

            var nextItem = context.NextFocusedView as CityCollectionViewCell;
            if (nextItem != null) {
                Animate (0.2, () => {
                    nextItem.CityTitle.Alpha = 1.0f;
                });
            }
        }
        #endregion
    }
}
```

首先，我们提供访问以下内容的快捷方式 `AppDelegate` ： 

```csharp
public static AppDelegate App {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
```

接下来，我们提供了集合视图的数据源的快捷方式，并提供了一个用于访问集合视图控制器 (的属性，该属性用于在用户进行选择) 时关闭集合：

```csharp
public CityViewDatasource Source {
    get { return DataSource as CityViewDatasource;}
}

public CityCollectionViewController ParentController { get; set;}
```

然后，使用以下代码初始化集合视图，并分配我们的单元类、数据源和委托：

```csharp
public CityCollectionView (IntPtr handle) : base (handle)
{
    // Initialize
    RegisterClassForCell (typeof(CityCollectionViewCell), CityViewDatasource.CardCellId);
    DataSource = new CityViewDatasource (this);
    Delegate = new CityViewDelegate ();
}
```

最后，我们希望图像下的标题仅在用户突出显示 (焦点) 时可见。 我们通过以下代码来实现此目的：

```csharp
public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
{
    var previousItem = context.PreviouslyFocusedView as CityCollectionViewCell;
    if (previousItem != null) {
        Animate (0.2, () => {
            previousItem.CityTitle.Alpha = 0.0f;
        });
    }

    var nextItem = context.NextFocusedView as CityCollectionViewCell;
    if (nextItem != null) {
        Animate (0.2, () => {
            nextItem.CityTitle.Alpha = 1.0f;
        });
    }
}
```

我们将 (0) 的上一项的 transparence 设置为零，并将下一项的 transparence 提升为100%。 这些转换也会动态显示。

## <a name="configuring-the-collection-view-controller"></a>配置集合视图控制器

现在，我们需要对集合视图执行最终配置，并允许控制器设置定义的属性，以便在用户做出选择后可以关闭集合视图。

编辑 `CityCollectionViewController.cs` 从情节提要) 自动创建 (文件，使其类似于以下内容：

```csharp
// This file has been autogenerated from a class added in the UI designer.

using System;

using Foundation;
using UIKit;

namespace tvCollection
{
    public partial class CityCollectionViewController : UICollectionViewController
    {
        #region Computed Properties
        public CityCollectionView Collection {
            get { return CollectionView as CityCollectionView; }
        }
        #endregion

        #region Constructors
        public CityCollectionViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Save link to controller
            Collection.ParentController = this;
        }
        #endregion
    }
}

```

## <a name="putting-it-all-together"></a>将其全部放在一起 

现在，我们已将所有部分组合在一起来填充和控制我们的集合视图，我们需要在主视图中进行最后的编辑，将所有内容整合在一起。

编辑 `ViewController.cs` 从情节提要) 自动创建 (文件，使其类似于以下内容：

```csharp
using System;
using Foundation;
using UIKit;
using tvCollection;

namespace MySingleView
{
    public partial class ViewController : UIViewController
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Constructors
        public ViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();
            // Perform any additional setup after loading the view, typically from a nib.
        }

        public override void ViewWillAppear (bool animated)
        {
            base.ViewWillAppear (animated);

            // Update image with the currently selected one
            CityView.Image = UIImage.FromFile(App.SelectedCity.ImageFilename);
            BackgroundView.Image = CityView.Image;
            CityTitle.Text = App.SelectedCity.Title;
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
        #endregion
    }
}
```

下面的代码最初显示的属性中的选定项 `SelectedCity` `AppDelegate` ，并在用户从集合视图中进行选择时将其重新显示：

```csharp
public override void ViewWillAppear (bool animated)
{
    base.ViewWillAppear (animated);

    // Update image with the currently selected one
    CityView.Image = UIImage.FromFile(App.SelectedCity.ImageFilename);
    BackgroundView.Image = CityView.Image;
    CityTitle.Text = App.SelectedCity.Title;
}
```

<a name="Testing-the-app"></a>

## <a name="testing-the-app"></a>测试应用

完成所有操作后，如果你构建并运行应用程序，主视图将显示为默认城市：

[![主屏幕](collection-views-images/run01.png)](collection-views-images/run01.png#lightbox)

如果用户单击 " **选择视图** " 按钮，将显示集合视图：

[![集合视图](collection-views-images/run02.png)](collection-views-images/run02.png#lightbox)

将其 `CanSelect` 属性设置为的任何城市 `false` 都将显示为灰色，用户将无法对其设置焦点。 当用户突出显示某个项 (使其处于焦点) 将显示标题，并可使用视差效果个很微妙倾斜三维图像。

当用户单击 "选择" 图像时，将关闭 "集合" 视图，并使用新图像重新显示主视图：

[![主屏幕上的新图像](collection-views-images/run03.png)](collection-views-images/run03.png#lightbox)

<a name="Creating-Custom-Layout-and-Reordering-Items"></a>

## <a name="creating-custom-layout-and-reordering-items"></a>创建自定义布局和重新排序项

使用集合视图的主要功能之一是能够创建自定义布局。 由于 tvOS 继承自 iOS，因此创建自定义布局的过程是相同的。 有关详细信息，请参阅 [集合视图文档简介](~/ios/user-interface/controls/uicollectionview.md) 。

最近添加到 iOS 9 的集合视图的功能是能够轻松地允许对集合中的项进行重新排序。 同样，由于 tvOS 9 是 iOS 9 的子集，因此以相同的方式执行此操作。 有关更多详细信息，请参阅 [集合视图更改](~/ios/user-interface/controls/uicollectionview.md) 文档。

<a name="Summary"></a>

## <a name="summary"></a>总结

本文介绍了如何在 tvOS 应用程序中设计和使用集合视图。 首先，它讨论了构成集合视图的所有元素。 接下来，该示例演示如何使用情节提要设计和实现集合视图。 最后提供了有关创建自定义布局和重新排序项的信息的链接。

## <a name="related-links"></a>相关链接

- [tvOS 示例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人体学接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [TvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)