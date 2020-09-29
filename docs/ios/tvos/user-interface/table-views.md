---
title: 在 Xamarin 中使用 tvOS 表视图
description: 本文介绍如何在 tvOS 应用程序中设计和使用表视图和表视图控制器。
ms.prod: xamarin
ms.assetid: D8F80FA9-6400-4DB7-AFC9-A28A54AD04E8
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 364aa1ebc70517ee8378e603922486ae29adf6c1
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436435"
---
# <a name="working-with-tvos-table-views-in-xamarin"></a>在 Xamarin 中使用 tvOS 表视图

_本文介绍如何在 tvOS 应用程序中设计和使用表视图和表视图控制器。_

在 tvOS 中，表视图作为滚动行的单个列提供，可选择性地将其组织为多个组或部分。 如果需要以清晰的方式向用户显示大量数据，则应使用表视图。

表视图通常显示在 " [拆分" 视图](~/ios/tvos/user-interface/split-views.md) 的一侧，作为导航，其中所选项目的详细信息显示在另一侧：

[![示例表视图](table-views-images/intro01.png)](table-views-images/intro01.png#lightbox)

<a name="About-Table-Views"></a>

## <a name="about-table-views"></a>关于表视图

将 `UITableView` 可滚动行的单个列显示为可选择性地组织到组或部分的信息的分层列表： 

[![选定的项](table-views-images/table01.png)](table-views-images/table01.png#lightbox)

Apple 对使用表有以下建议：

- 请**注意宽度**-尝试在表宽度中按正确的平衡。 如果表太宽，则很难从远处扫描，并且可以从 "可用内容" 区域中取出。 如果表太窄，则可能会导致信息被截断或换行，这可能会使用户难以从聊天室读取内容。
- **快速显示表内容** -对于大型数据列表，延迟加载内容，并在向用户显示信息时立即开始显示信息。 如果表需要很长时间才能加载，则用户可能会失去对应用程序的兴趣，或认为它已锁定。
- **通知用户长内容加载** -如果长表加载时间不可避免，则显示一个 [进度栏或活动指示器](~/ios/tvos/user-interface/progress-indicators.md) ，使其知道该应用尚未锁定。

<a name="Table-Cell-Types"></a>

## <a name="table-view-cell-types"></a>表视图单元格类型

`UITableViewCell`用于在表视图中表示单独的数据行。 Apple 定义了几个默认的表单元类型：

- **默认值** -此类型在单元格的左侧和右侧的左对齐标题中显示选项图像。 
- **副标题** -此类型在第一行显示左对齐标题，在下一行显示较小的左对齐副标题。
- **值 1** -此类型将在同一行上显示具有较浅颜色、右对齐副标题的左对齐标题。
- **值 2** -此类型显示右对齐标题，并在同一行上显示具有较浅颜色、左对齐的副标题。

所有默认表视图单元格类型还支持图形元素，如泄露指示器或选中标记。 

此外，您还可以定义 **自定义** 表视图单元格类型，并提供可在接口设计器或代码中创建的 _原型单元_。

Apple 对于使用表视图单元有以下建议：

- **避免文本剪辑** -将文本的各个行保持简短，使其不会被截断。 截断的单词或短语对于用户来说很难在房间内进行分析。
- **请考虑重点的行状态** -因为行变大，当焦点更大时，需要测试单元格在所有状态下的外观。 图像或文本可能会在焦点状态中被剪裁或看起来不正确。
- **谨慎使用可编辑表** -在 tvOS 上移动或删除表行比 iOS 更耗时。 如果此功能将在 tvOS 应用中添加或打乱，则需要仔细决定。
- **根据需要创建自定义单元类型** -尽管内置表视图单元格类型在很多情况下都很有用，但请考虑为非标准信息创建自定义单元类型，以提供更好的控制和更好地向用户提供信息。

<a name="Working-With-Table-Views"></a>

## <a name="working-with-table-views"></a>使用表视图

在 tvOS 应用程序中使用表视图的最简单方法是在接口设计器中创建和修改它们的外观。

若要开始，请执行以下操作：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 Visual Studio for Mac 中，启动新的 tvOS 应用程序项目，并选择**tvOS**  >  **应用**  >  **单视图应用**，并单击 "**下一步**" 按钮： 

    [![选择单一视图应用](table-views-images/table02.png)](table-views-images/table02.png#lightbox)
1. 输入应用的 **名称** ，然后单击 " **下一步**"： 

    [![输入应用的名称](table-views-images/table03.png)](table-views-images/table03.png#lightbox)
1. 调整 **项目名称** 和 **解决方案名称** 或接受默认值，然后单击 " **创建** " 按钮创建新解决方案： 

    [![项目名称和解决方案名称](table-views-images/table04.png)](table-views-images/table04.png#lightbox)
1. 在 **Solution Pad**中，双击该 `Main.storyboard` 文件以在 iOS 设计器中将其打开： 

    [![主情节提要文件](table-views-images/table05.png)](table-views-images/table05.png#lightbox)
1. 选择并删除 **默认视图控制器**： 

    [![选择并删除默认视图控制器](table-views-images/table06.png)](table-views-images/table06.png#lightbox)
1. 从 "**工具箱**" 中选择**拆分视图控制器**，然后将其拖动到 Design Surface 上。
1. 默认情况下，你将获得一个具有**导航视图控制器**的[拆分视图](~/ios/tvos/user-interface/split-views.md)，并在右侧显示一个**表视图控制器**和一个**视图控制器**。 这是 Apple 在 tvOS 中的表视图的建议用法： 

    [![添加拆分视图](table-views-images/table08.png)](table-views-images/table08.png#lightbox)
1. 您需要选择表视图的每个部分，并在 "**属性资源管理器**" 的 "**小组件**" 选项卡中为其分配一个自定义**类名**，以便以后可以在 c # 代码中访问它。 例如， **表视图控制器**： 

    [![指定类名](table-views-images/table09.png)](table-views-images/table09.png#lightbox)
1. 确保为 **表视图控制器**、 **表视图** 和所有 **原型单元**创建一个自定义类。 在创建自定义类时，Visual Studio for Mac 会将它们添加到项目树中： 

    [![项目树中的自定义类](table-views-images/table10.png)](table-views-images/table10.png#lightbox)
1. 接下来，选择 Design Surface 中的表视图，并根据需要调整其属性。 例如， **原型元格** 的数量和 **样式** (纯) 或分组的： 

    [!["小组件" 选项卡](table-views-images/table11.png)](table-views-images/table11.png#lightbox)
1. 对于每个**原型单元**，在 "**属性资源管理器**" 的 "**小组件**" 选项卡中选择它并分配唯一**标识符**。 此步骤 _非常重要_ ，因为你将在以后填充表时需要此标识符。 例如 `AttrCell`： 

    [!["小组件" 选项卡](table-views-images/table12.png)](table-views-images/table12.png#lightbox)
1. 还可以选择通过 "**样式**" 下拉列表将单元格显示为[默认表视图单元格类型](#table-view-cell-types)之一，或将其设置为 "**自定义**"，并通过从**工具箱**中拖动其他 UI 小组件来使用 Design Surface 布局单元： 

    [![单元格布局](table-views-images/table13.png)](table-views-images/table13.png#lightbox)
1. 在 "**属性资源管理器**" 的 "**小组件**" 选项卡中，为原型单元设计中的每个 UI 元素指定唯一的**名称**，以便以后可以在 c # 代码中访问这些元素： 

    [![指定名称](table-views-images/table14.png)](table-views-images/table14.png#lightbox)
1. 对于表视图中的所有原型单元，请重复上述步骤。
1. 接下来，将自定义类分配给 UI 设计的其余部分，对详细信息视图进行布局，并为详细信息视图中的每个 UI 元素分配唯一的 **名称** ，以便可以在 c # 中访问它们。 例如： 

    [![UI 布局](table-views-images/table15.png)](table-views-images/table15.png#lightbox)
1. 保存对情节提要所做的更改。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 在 Visual Studio 中，启动新的 tvOS 应用程序项目，并选择 " **tvOS**" "  >  **单一视图应用**"，然后输入应用的名称。 单击 " **确定** " 按钮以创建新的解决方案： 

    [![选择单一视图应用](table-views-images/table02-vs.png)](table-views-images/table02-vs.png#lightbox)
1. 在 **解决方案资源管理器**中，双击该 `Main.storyboard` 文件以在 iOS 设计器中将其打开： 

    [![主情节提要文件](table-views-images/table05-vs.png)](table-views-images/table05-vs.png#lightbox)
1. 选择并删除 **默认视图控制器**： 

    [![选择并删除默认视图控制器](table-views-images/table06-vs.png)](table-views-images/table06-vs.png#lightbox)
1. 从 "**工具箱**" 中选择**拆分视图控制器**，然后将其拖动到 Design Surface： 

    [![拆分视图控制器](table-views-images/table07-vs.png)](table-views-images/table07-vs.png#lightbox)
1. 默认情况下，你将获得一个具有**导航视图控制器**的[拆分视图](~/ios/tvos/user-interface/split-views.md)，并在右侧显示一个**表视图控制器**和一个**视图控制器**。 这是 Apple 在 tvOS 中的表视图的建议用法： 

    [![布局 UI](table-views-images/table08-vs.png)](table-views-images/table08-vs.png#lightbox)
1. 您需要选择表视图的每个部分，并在 "**属性资源管理器**" 的 "**小组件**" 选项卡中为其分配一个自定义**类名**，以便以后可以在 c # 代码中访问它。 例如， **表视图控制器**： 

    [!["小组件" 选项卡](table-views-images/table09-vs.png)](table-views-images/table09-vs.png#lightbox)
1. 确保为 **表视图控制器**、 **表视图** 和所有 **原型单元**创建一个自定义类。 在创建自定义类时，Visual Studio for Mac 会将它们添加到项目树中： 

    [![项目树中的自定义类](table-views-images/table10-vs.png)](table-views-images/table10-vs.png#lightbox)
1. 接下来，选择 Design Surface 中的表视图，并根据需要调整其属性。 例如， **原型元格** 的数量和 **样式** (纯) 或分组的： 

    [!["小组件" 选项卡](table-views-images/table11-vs.png)](table-views-images/table11-vs.png#lightbox)
1. 对于每个**原型单元**，在 "**属性资源管理器**" 的 "**小组件**" 选项卡中选择它并分配唯一**标识符**。 此步骤 _非常重要_ ，因为你将在以后填充表时需要此标识符。 例如 `AttrCell`： 

    [![分配标识符](table-views-images/table12-vs.png)](table-views-images/table12-vs.png#lightbox)
1. 还可以选择通过 "**样式**" 下拉列表将单元格显示为[默认表视图单元格类型](#table-view-cell-types)之一，或将其设置为 "**自定义**"，并通过从**工具箱**中拖动其他 UI 小组件来使用 Design Surface 布局单元： 

    [![样式下拉列表](table-views-images/table13-vs.png)](table-views-images/table13-vs.png#lightbox)
1. 在 "**属性资源管理器**" 的 "**小组件**" 选项卡中，为原型单元设计中的每个 UI 元素指定唯一的**名称**，以便以后可以在 c # 代码中访问这些元素： 

    [!["小组件" 选项卡](table-views-images/table14-vs.png)](table-views-images/table14-vs.png#lightbox)
1. 对于表视图中的所有原型单元，请重复上述步骤。
1. 接下来，将自定义类分配给 UI 设计的其余部分，对详细信息视图进行布局，并为详细信息视图中的每个 UI 元素分配唯一的 **名称** ，以便可以在 c # 中访问它们。 例如： 

    [![UI 布局](table-views-images/table15.png)](table-views-images/table15.png#lightbox)
1. 保存对情节提要所做的更改。

-----

<a name="Designing-a-Data-Model"></a>

## <a name="designing-a-data-model"></a>设计数据模型

若要使用表视图显示的信息，并且在用户在表) 视图中选择或突出显示行时 (显示详细信息，则可以创建一个自定义类或类作为显示的信息的数据模型。

采用包含城市列表的旅行预订应用的示例，其中每个 **城市**都包含用户可选择的 **Attractions** 的唯一列表。 用户可以将引力标记为 *收藏*项，选择此项可获取引力的 *方向* 并将 *航班预订* 给给定城市。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

若要创建**引力**的数据模型，请在**Solution Pad**中右键单击项目名称，然后选择 "**添加**  >  **新文件 ...**"。输入 `AttractionInformation` 作为**名称**，然后单击 "**新建**" 按钮： 

[![为名称输入 AttractionInformation](table-views-images/data01.png)](table-views-images/data01.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

若要创建**引力**的数据模型，请在**解决方案资源管理器**中右键单击项目名称，然后选择 "**添加**  >  **新项 ...**"。选择 "**类**"，并输入 `AttractionInformation` 作为**名称**，然后单击 "**添加**" 按钮： 

[![选择 "类"，并输入 AttractionInformation 作为名称](table-views-images/data01-vs.png)](table-views-images/data01-vs.png#lightbox)

-----

编辑 `AttractionInformation.cs` 文件，使其类似于以下内容：

```csharp
using System;
using Foundation;

namespace tvTable
{
    public class AttractionInformation : NSObject
    {
        #region Computed Properties
        public CityInformation City { get; set;}
        public string Name { get; set;}
        public string Description { get; set;}
        public string ImageName { get; set;}
        public bool IsFavorite { get; set;}
        public bool AddDirections { get; set;}
        #endregion

        #region Constructors
        public AttractionInformation (string name, string description, string imageName)
        {
            // Initialize
            this.Name = name;
            this.Description = description;
            this.ImageName = imageName;
        }
        #endregion
    }
}
```

此类提供属性以存储有关给定 **引力**的信息。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

接下来，再次右键单击 " **Solution Pad**中的项目名称，然后选择"**添加**  >  **新文件 ...**"。输入 `CityInformation` 作为**名称**，然后单击 "**新建**" 按钮： 

[![为名称输入 CityInformation](table-views-images/data02.png)](table-views-images/data02.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

接下来，再次右键单击 "**解决方案资源管理器**中的项目名称，然后选择"**添加**  >  **新项 ...**"。输入 `CityInformation` 作为**名称**，然后单击 "**添加**" 按钮： 

[![为名称输入 CityInformation](table-views-images/data02-vs.png)](table-views-images/data02-vs.png#lightbox)

-----

编辑 `CityInformation.cs` 文件，使其类似于以下内容：

```csharp
using System;
using System.Collections.Generic;
using Foundation;

namespace tvTable
{
    public class CityInformation : NSObject
    {
        #region Computed Properties
        public string Name { get; set; }
        public List<AttractionInformation> Attractions { get; set;}
        public bool FlightBooked { get; set;}
        #endregion

        #region Constructors
        public CityInformation (string name)
        {
            // Initialize
            this.Name = name;
            this.Attractions = new List<AttractionInformation> ();
        }
        #endregion

        #region Public Methods
        public void AddAttraction (AttractionInformation attraction)
        {
            // Mark as belonging to this city
            attraction.City = this;

            // Add to collection
            Attractions.Add (attraction);
        }

        public void AddAttraction (string name, string description, string imageName)
        {
            // Create attraction
            var attraction = new AttractionInformation (name, description, imageName);

            // Mark as belonging to this city
            attraction.City = this;

            // Add to collection
            Attractions.Add (attraction);
        }
        #endregion
    }
}
```

此类包含有关目标 **城市**的所有信息、该城市的 **Attractions** 集合，并提供两个帮助器方法 (`AddAttraction`) ，以便更轻松地将 Attractions 添加到城市。

<a name="The-Table-Data-Source"></a>

## <a name="the-table-view-data-source"></a>表视图数据源

每个表视图都需要 () 的数据源， `UITableViewDataSource` 以便为表提供数据并按表视图所需的方式生成所需的行。

对于上面给出的示例，在**解决方案资源管理器**中右键单击项目名称，选择 "**添加**  >  **新文件 ...** "，然后调用它 `AttractionTableDatasource` 并单击 "**新建**" 按钮以创建。 接下来，编辑 `AttractionTableDatasource.cs` 文件，使其类似于以下内容：

```csharp
using System;
using System.Collections.Generic;
using UIKit;

namespace tvTable
{
    public class AttractionTableDatasource : UITableViewDataSource
    {
        #region Constants
        const string CellID = "AttrCell";
        #endregion

        #region Computed Properties
        public AttractionTableViewController Controller { get; set;}
        public List<CityInformation> Cities { get; set;}
        #endregion

        #region Constructors
        public AttractionTableDatasource (AttractionTableViewController controller)
        {
            // Initialize
            this.Controller = controller;
            this.Cities = new List<CityInformation> ();
            PopulateCities ();
        }
        #endregion

        #region Public Methods
        public void PopulateCities ()
        {
            // Clear existing
            Cities.Clear ();

            // Define cities and attractions
            var Paris = new CityInformation ("Paris");
            Paris.AddAttraction ("Eiffel Tower", "Is a wrought iron lattice tower on the Champ de Mars in Paris, France.", "EiffelTower");
            Paris.AddAttraction ("Musée du Louvre", "is one of the world's largest museums and a historic monument in Paris, France.", "Louvre");
            Paris.AddAttraction ("Moulin Rouge", "French for 'Red Mill', is a cabaret in Paris, France.", "MoulinRouge");
            Paris.AddAttraction ("La Seine", "Is a 777-kilometre long river and an important commercial waterway within the Paris Basin.", "RiverSeine");
            Cities.Add (Paris);

            var SanFran = new CityInformation ("San Francisco");
            SanFran.AddAttraction ("Alcatraz Island", "Is located in the San Francisco Bay, 1.25 miles (2.01 km) offshore from San Francisco.", "Alcatraz");
            SanFran.AddAttraction ("Golden Gate Bridge", "Is a suspension bridge spanning the Golden Gate strait between San Francisco Bay and the Pacific Ocean", "GoldenGateBridge");
            SanFran.AddAttraction ("San Francisco", "Is the cultural, commercial, and financial center of Northern California.", "SanFrancisco");
            SanFran.AddAttraction ("Telegraph Hill", "Is primarily a residential area, much quieter than adjoining North Beach.", "TelegraphHill");
            Cities.Add (SanFran);

            var Houston = new CityInformation ("Houston");
            Houston.AddAttraction ("City Hall", "It was constructed in 1938-1939, and is located in Downtown Houston.", "CityHall");
            Houston.AddAttraction ("Houston", "Is the most populous city in Texas and the fourth most populous city in the US.", "Houston");
            Houston.AddAttraction ("Texas Longhorn", "Is a breed of cattle known for its characteristic horns, which can extend to over 6 ft.", "LonghornCattle");
            Houston.AddAttraction ("Saturn V Rocket", "was an American human-rated expendable rocket used by NASA between 1966 and 1973.", "Rocket");
            Cities.Add (Houston);
        }
        #endregion

        #region Override Methods
        public override UITableViewCell GetCell (UITableView tableView, Foundation.NSIndexPath indexPath)
        {
            // Get cell
            var cell = tableView.DequeueReusableCell (CellID) as AttractionTableCell;

            // Populate cell
            cell.Attraction = Cities [indexPath.Section].Attractions [indexPath.Row];

            // Return new cell
            return cell;
        }

        public override nint NumberOfSections (UITableView tableView)
        {
            // Return number of cities
            return Cities.Count;
        }

        public override nint RowsInSection (UITableView tableView, nint section)
        {
            // Return the number of attractions in the given city
            return Cities [(int)section].Attractions.Count;
        }

        public override string TitleForHeader (UITableView tableView, nint section)
        {
            // Get the name of the current city
            return Cities [(int)section].Name;
        }
        #endregion
    }
}
```

让我们详细了解类的几个部分。

首先，我们定义了一个常数，用于保存原型单元的唯一标识符 (这是) 上的接口设计器中分配的同一标识符，并将快捷方式添加回表视图控制器并为数据创建了存储：

```csharp
const string CellID = "AttrCell";
public AttractionTableViewController Controller { get; set;}
public List<CityInformation> Cities { get; set;}
```

接下来，我们将保存表视图控制器，然后使用) 在创建类时定义的数据模型生成并填充数据源 (：

```csharp
public AttractionTableDatasource (AttractionTableViewController controller)
{
    // Initialize
    this.Controller = controller;
    this.Cities = new List<CityInformation> ();
    PopulateCities ();
}
```

例如，此 `PopulateCities` 方法只是在内存中创建数据模型对象，但可以在实际应用中轻松地从数据库或 web 服务中读取这些对象：

```csharp
public void PopulateCities ()
{
    // Clear existing
    Cities.Clear ();

    // Define cities and attractions
    var Paris = new CityInformation ("Paris");
    Paris.AddAttraction ("Eiffel Tower", "Is a wrought iron lattice tower on the Champ de Mars in Paris, France.", "EiffelTower");
    ...
}
```

`NumberOfSections`方法返回表中部分的数目：

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    // Return number of cities
    return Cities.Count;
}
```

对于 **纯** 样式的表视图，始终返回1。

`RowsInSection`方法返回当前节中的行数：

```csharp
public override nint RowsInSection (UITableView tableView, nint section)
{
    // Return the number of attractions in the given city
    return Cities [(int)section].Attractions.Count;
}
```

同样，对于 **纯** 表视图，返回数据源中的总项数。

`TitleForHeader`方法返回给定节的标题：

```csharp
public override string TitleForHeader (UITableView tableView, nint section)
{
    // Get the name of the current city
    return Cities [(int)section].Name;
}
```

对于 **纯** 表格视图类型，请将标题留空 (`""`) 。

最后，在表视图请求时，使用方法创建并填充原型单元 `GetCell` ： 

```csharp
public override UITableViewCell GetCell (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    // Get cell
    var cell = tableView.DequeueReusableCell (CellID) as AttractionTableCell;

    // Populate cell
    cell.Attraction = Cities [indexPath.Section].Attractions [indexPath.Row];

    // Return new cell
    return cell;
}
```

有关使用的详细信息 `UITableViewDatasource` ，请参阅 Apple 的 [UITableViewDatasource](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40006941) 文档。

<a name="The-Table-View-Delegate"></a>

## <a name="the-table-view-delegate"></a>表视图委托

每个表视图都需要一个委托 (`UITableViewDelegate`) 来响应表上的用户交互或其他系统事件。

对于上面给出的示例，在**解决方案资源管理器**中右键单击项目名称，选择 "**添加**  >  **新文件 ...** "，然后调用它 `AttractionTableDelegate` 并单击 "**新建**" 按钮以创建。 接下来，编辑 `AttractionTableDelegate.cs` 文件，使其类似于以下内容：

```csharp
using System;
using System.Collections.Generic;
using UIKit;

namespace tvTable
{
    public class AttractionTableDelegate : UITableViewDelegate
    {
        #region Computed Properties
        public AttractionTableViewController Controller { get; set;}
        #endregion

        #region Constructors
        public AttractionTableDelegate (AttractionTableViewController controller)
        {
            // Initializw
            this.Controller = controller;
        }
        #endregion

        #region Override Methods
        public override void RowSelected (UITableView tableView, Foundation.NSIndexPath indexPath)
        {
            var attraction = Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row];
            attraction.IsFavorite = (!attraction.IsFavorite);

            // Update UI
            Controller.TableView.ReloadData ();
        }

        public override bool CanFocusRow (UITableView tableView, Foundation.NSIndexPath indexPath)
        {
            // Inform caller of highlight change
            RaiseAttractionHighlighted (Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row]);
            return true;
        }
        #endregion

        #region Events
        public delegate void AttractionHighlightedDelegate (AttractionInformation attraction);
        public event AttractionHighlightedDelegate AttractionHighlighted;

        internal void RaiseAttractionHighlighted (AttractionInformation attraction)
        {
            // Inform caller
            if (this.AttractionHighlighted != null) this.AttractionHighlighted (attraction);
        }
        #endregion
    }
}
```

让我们详细了解此类的几个部分。

首先，创建类时，将创建表视图控制器的快捷方式：

```csharp
public AttractionTableViewController Controller { get; set;}
...

public AttractionTableDelegate (AttractionTableViewController controller)
{
    // Initialize
    this.Controller = controller;
}
```

然后，在选择行时 (用户单击 Apple 远程) 的触摸界面时，我们想要将所选行所表示的 **引力** 标记为收藏行：

```csharp
public override void RowSelected (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    var attraction = Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row];
    attraction.IsFavorite = (!attraction.IsFavorite);

    // Update UI
    Controller.TableView.ReloadData ();
}
```

接下来，当用户突出显示某个行 (通过使用 Apple 远程触摸 Surface) ，我们想要在拆分视图控制器的详细信息部分中显示该行所表示的 **引力** 的详细信息：

```csharp
public override bool CanFocusRow (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    // Inform caller of highlight change
    RaiseAttractionHighlighted (Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row]);
    return true;
}
...

public delegate void AttractionHighlightedDelegate (AttractionInformation attraction);
public event AttractionHighlightedDelegate AttractionHighlighted;

internal void RaiseAttractionHighlighted (AttractionInformation attraction)
{
    // Inform caller
    if (this.AttractionHighlighted != null) this.AttractionHighlighted (attraction);
}
```

为 `CanFocusRow` 每个要在表视图中获得焦点的行调用方法。 `true`如果行可以获得焦点，则返回; 否则返回 `false` 。 在此示例中，我们创建了一个自定义 `AttractionHighlighted` 事件，该事件将在收到焦点时在每一行上引发。

有关使用的详细信息 `UITableViewDelegate` ，请参阅 Apple 的 [UITableViewDelegate](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40006942) 文档。

<a name="The-Table-View-Cell"></a>

## <a name="the-table-view-cell"></a>表视图单元格

对于添加到接口设计器中的表视图的每个原型单元，还会创建表视图单元格的自定义实例 (`UITableViewCell`) ，以便在创建新单元格 (行) 时填充该单元格。

对于示例应用，双击该 `AttractionTableCell.cs` 文件以将其打开以进行编辑，并使其看起来像下面这样：

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class AttractionTableCell : UITableViewCell
    {
        #region Private Variables
        private AttractionInformation _attraction = null;
        #endregion

        #region Computed Properties
        public AttractionInformation Attraction {
            get { return _attraction; }
            set {
                _attraction = value;
                UpdateUI ();
            }
        }
        #endregion

        #region Constructors
        public AttractionTableCell (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Private Methods
        private void UpdateUI ()
        {
            // Trap all errors
            try {
                Title.Text = Attraction.Name;
                Favorite.Hidden = (!Attraction.IsFavorite);
            } catch {
                // Since the UI might not be fully loaded, ignore
                // all errors at this point
            }
        }
        #endregion
    }
}
```

此类为引力数据模型对象提供存储 (`AttractionInformation` 如上所定义) 在给定行中显示：

```csharp
private AttractionInformation _attraction = null;
...

public AttractionInformation Attraction {
    get { return _attraction; }
    set {
        _attraction = value;
        UpdateUI ();
    }
}
```

`UpdateUI`方法根据需要填充添加到接口设计器中的单元格原型 (的 UI 小组件) ：

```csharp
private void UpdateUI ()
{
    // Trap all errors
    try {
        Title.Text = Attraction.Name;
        Favorite.Hidden = (!Attraction.IsFavorite);
    } catch {
        // Since the UI might not be fully loaded, ignore
        // all errors at this point
    }
}
```

有关使用的详细信息 `UITableViewCell` ，请参阅 Apple 的 [UITableViewCell](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewCell_Class/index.html#//apple_ref/doc/uid/TP40006938) 文档。

<a name="The-Table-View-Controller"></a>

## <a name="the-table-view-controller"></a>表视图控制器

表视图控制器 (`UITableViewController`) 管理已通过接口设计器添加到情节提要的表视图。

对于示例应用，双击该 `AttractionTableViewController.cs` 文件以将其打开以进行编辑，并使其看起来像下面这样：

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class AttractionTableViewController : UITableViewController
    {
        #region Computed Properties
        public AttractionTableDatasource Datasource {
            get { return TableView.DataSource as AttractionTableDatasource; }
        }

        public AttractionTableDelegate TableDelegate {
            get { return TableView.Delegate as AttractionTableDelegate; }
        }
        #endregion

        #region Constructors
        public AttractionTableViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Setup table
            TableView.DataSource = new AttractionTableDatasource (this);
            TableView.Delegate = new AttractionTableDelegate (this);
            TableView.ReloadData ();
        }
        #endregion
    }
}
```

让我们进一步了解一下此类。 首先，我们创建了快捷方式，使您可以更轻松地访问表视图 `DataSource` 和 `TableDelegate` 。 稍后我们将使用这些信息在拆分视图左侧的表视图和右侧的详细信息视图之间进行通信。

最后，在将表视图加载到内存中时，将创建 `AttractionTableDatasource` 和 (上面创建的和的实例 `AttractionTableDelegate`) 并将它们附加到表视图。

有关使用的详细信息 `UITableViewController` ，请参阅 Apple 的 [UITableViewController](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewController_Class/index.html#//apple_ref/doc/uid/TP40007523) 文档。

<a name="Pulling-it-All-Together"></a>

## <a name="pulling-it-all-together"></a>全部提取

正如本文档开头所述，表视图通常显示在 " [拆分" 视图](~/ios/tvos/user-interface/split-views.md) 的一侧，作为导航，其中显示的项目的详细信息显示在另一侧。 例如： 

[![示例应用运行](table-views-images/intro01.png)](table-views-images/intro01.png#lightbox)

由于这是 tvOS 中的一种标准模式，因此让我们看看将所有内容组合在一起的最后一个步骤，并使拆分视图的左右两侧相互交互。

<a name="The-Detail-View"></a>

### <a name="the-detail-view"></a>详细信息视图

在上面提供的旅游应用的示例中，为 "拆分" `AttractionViewController` 视图右侧显示的 "标准视图控制器" () 定义为详细信息视图：

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class AttractionViewController : UIViewController
    {
        #region Private Variables
        private AttractionInformation _attraction = null;
        #endregion

        #region Computed Properties
        public AttractionInformation Attraction {
            get { return _attraction; }
            set {
                _attraction = value;
                UpdateUI ();
            }
        }

        public MasterSplitView SplitView { get; set;}
        #endregion

        #region Constructors
        public AttractionViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Public Methods
        public void UpdateUI ()
        {
            // Trap all errors
            try {
                City.Text = Attraction.City.Name;
                Title.Text = Attraction.Name;
                SubTitle.Text = Attraction.Description;

                IsFlighBooked.Hidden = (!Attraction.City.FlightBooked);
                IsFavorite.Hidden = (!Attraction.IsFavorite);
                IsDirections.Hidden = (!Attraction.AddDirections);
                BackgroundImage.Image = UIImage.FromBundle (Attraction.ImageName);
                AttractionImage.Image = BackgroundImage.Image;
            } catch {
                // Since the UI might not be fully loaded, ignore
                // all errors at this point
            }
        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear (bool animated)
        {
            base.ViewWillAppear (animated);

            // Ensure the UI Updates
            UpdateUI ();
        }
        #endregion

        #region Actions
        partial void BookFlight (NSObject sender)
        {
            // Ask user to book flight
            AlertViewController.PresentOKCancelAlert ("Book Flight",
                                                      string.Format ("Would you like to book a flight to {0}?", Attraction.City.Name),
                                                      this,
                                                      (ok) => {
                Attraction.City.FlightBooked = ok;
                IsFlighBooked.Hidden = (!Attraction.City.FlightBooked);
            });
        }

        partial void GetDirections (NSObject sender)
        {
            // Ask user to add directions
            AlertViewController.PresentOKCancelAlert ("Add Directions",
                                                     string.Format ("Would you like to add directions to {0} to you itinerary?", Attraction.Name),
                                                     this,
                                                     (ok) => {
                                                         Attraction.AddDirections = ok;
                                                         IsDirections.Hidden = (!Attraction.AddDirections);
                                                     });
        }

        partial void MarkFavorite (NSObject sender)
        {
            // Flip favorite state
            Attraction.IsFavorite = (!Attraction.IsFavorite);
            IsFavorite.Hidden = (!Attraction.IsFavorite);

            // Reload table
            SplitView.Master.TableController.TableView.ReloadData ();
        }
        #endregion
    }
}
```

在这里，我们提供了 **引力** (`AttractionInformation`) 显示为属性并创建了一个 `UpdateUI` 方法，该方法填充添加到接口设计器中的视图的 UI 小组件。

我们还定义了一个指向拆分视图控制器 (`SplitView`) 的快捷方式，用于将更改传递回表视图 (`AcctractionTableView`) 。

最后， (事件) 的自定义操作添加到了 `UIButton` 在界面设计器中创建的三个实例，该实例允许用户将引力标记为 _收藏_项、获取引力的 _方向_ 并将 _航班预订_ 给给定城市。

<a name="The-Navigation-View-Controller"></a>

### <a name="the-navigation-view-controller"></a>导航视图控制器

因为表视图控制器嵌套在 "拆分" 视图左侧的导航视图控制器中，所以在 "接口设计器" 中为导航视图控制器分配了自定义类 (`MasterNavigationController`) ，并定义如下：

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class MasterNavigationController : UINavigationController
    {
        #region Computed Properties
        public MasterSplitView SplitView { get; set;}
        public AttractionTableViewController TableController {
            get { return TopViewController as AttractionTableViewController; }
        }
        #endregion

        #region Constructors
        public MasterNavigationController (IntPtr handle) : base (handle)
        {
        }
        #endregion
    }
}
```

同样，此类只定义一些快捷方式，使其更易于在拆分视图控制器的两个边之间进行通信：

- `SplitView` -指向拆分视图控制器 (`MainSpiltViewController`) 导航视图控制器所属的链接。
- `TableController` -获取在 `AttractionTableViewController` 导航视图控制器中显示为顶部视图的表视图控制器 () 。

<a name="The-Split-View-Controller"></a>

### <a name="the-split-view-controller"></a>拆分视图控制器

由于拆分视图控制器是我们的应用程序的基础，因此，我们在接口设计器中创建了一个自定义类 () ，并按如下所示 `MasterSplitViewController` 对其进行定义：

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class MasterSplitView : UISplitViewController
    {
        #region Computed Properties
        public AttractionViewController Details {
            get { return ViewControllers [1] as AttractionViewController; }
        }

        public MasterNavigationController Master {
            get { return ViewControllers [0] as MasterNavigationController; }
        }
        #endregion

        #region Constructors
        public MasterSplitView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Initialize
            Master.SplitView = this;
            Details.SplitView = this;

            // Wire-up events
            Master.TableController.TableDelegate.AttractionHighlighted += (attraction) => {
                // Display new attraction
                Details.Attraction = attraction;
            };
        }
        #endregion
    }
}
```

首先，创建 "拆分" 视图 ** (的快捷** 方式的快捷方式，将 `AttractionViewController`) 和 **主** 端 (`MasterNavigationController`) 。 同样，这使您可以更轻松地在这两个边之间进行通信。

接下来，当拆分视图加载到内存中时，会将拆分视图控制器附加到拆分视图的两侧，并通过在 " `AttractionHighlighted` 拆分" 视图的 " **详细信息** " 一侧显示新的引力来响应用户突出显示表视图中的引力 () 。

请参阅 [tvTables](/samples/xamarin/ios-samples/tvos-tvtable) 示例应用，以便在拆分视图中完整实现表视图。

## <a name="table-views-in-detail"></a>详细信息表视图

由于 tvOS 基于 iOS，因此，表视图和表视图控制器的设计和行为方式类似。 若要详细了解如何在 Xamarin 应用中使用表视图，请参阅我们的 iOS [使用表和单元格](~/ios/user-interface/controls/tables/index.md) 文档。

<a name="Summary"></a>

## <a name="summary"></a>总结

本文介绍了如何在 tvOS 应用程序中设计和使用表视图。 并提供了一个示例，该示例演示如何在拆分视图中使用表视图，这是 tvOS 应用程序中的表视图的典型用法。

## <a name="related-links"></a>相关链接

- [tvOS 示例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [UITableViewController](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewController_Class/index.html#//apple_ref/doc/uid/TP40007523)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人体学接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [TvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)