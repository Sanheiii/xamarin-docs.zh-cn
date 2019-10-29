---
title: Xamarin 中的映射
description: 本文档介绍 iOS MapKit 框架，以及如何将其与 Xamarin 一起使用。 它讨论了如何添加地图、样式、平移和缩放、使用用户位置、添加注释、使用标注和叠加以及执行本地搜索。
ms.prod: xamarin
ms.assetid: 5DD8E56D-51C1-4AFA-B387-79B5734698ED
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 3eb50c97521d11944e6d549018e057416b9dc2b2
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022034"
---
# <a name="maps-in-xamarinios"></a>Xamarin 中的映射

在所有新式移动操作系统中，地图是一项常见功能。 iOS 通过地图工具包框架以本机方式提供映射支持。 借助 Map 工具包，应用程序可以轻松地添加丰富的交互式地图。 可以通过多种方式自定义这些地图，如添加批注来标记地图上的位置以及覆盖任意形状的图形。 Map 工具包甚至内置了对显示设备当前位置的支持。

## <a name="adding-a-map"></a>添加映射

向应用程序添加映射是通过将 `MKMapView` 实例添加到视图层次结构来完成的，如下所示：

```csharp
// map is an MKMapView declared as a class variable
map = new MKMapView (UIScreen.MainScreen.Bounds);
View = map;
```

`MKMapView` 是显示地图的 `UIView` 子类。 只需使用上面的代码添加地图就会生成一个交互式地图：

![](images/00-map.png "A sample map")

## <a name="map-style"></a>地图样式

`MKMapView` 支持3种不同的地图样式。 若要应用地图样式，只需将 `MapType` 属性设置为 `MKMapType` 枚举中的一个值：

```csharp
map.MapType = MKMapType.Standard; //road map
map.MapType = MKMapType.Satellite;
map.MapType = MKMapType.Hybrid;
```

以下屏幕截图显示了可用的不同地图样式：

![](images/01-mapstyles.png "This screenshot show the different map styles that are available")

## <a name="panning-and-zooming"></a>平移和缩放

`MKMapView` 包括对地图交互功能的支持，例如：

- 通过挤压手势缩放
- 通过平移手势平移

只需设置 `MKMapView` 实例的 `ZoomEnabled` 和 `ScrollEnabled` 属性，即可启用或禁用这些功能，其中，默认值为 true。 例如，若要显示静态映射，只需将相应的属性设置为 false：

```csharp
map.ZoomEnabled = false;
map.ScrollEnabled = false;
```

## <a name="user-location"></a>用户位置

除了用户交互，`MKMapView` 还提供内置的支持来显示设备的位置。 它使用*核心位置*框架来实现此功能。 必须先提示用户，然后才能访问用户的位置。 为此，请创建 `CLLocationManager` 实例，并调用 `RequestWhenInUseAuthorization`。

```csharp
CLLocationManager locationManager = new CLLocationManager();
locationManager.RequestWhenInUseAuthorization();
//locationManager.RequestAlwaysAuthorization(); //requests permission for access to location data while running in the background
```

请注意，在8.0 之前的 iOS 版本中，尝试调用 `RequestWhenInUseAuthorization` 将导致错误。 如果你打算支持8之前的版本，请确保在进行此调用之前检查 iOS 版本。

访问用户的位置时，还需要修改**info.plist**。 应设置如下与位置数据相关的键：

- **NSLocationWhenInUseUsageDescription** - 用于用户在和你的应用互动时访问用户的位置。
- **NSLocationAlwaysUsageDescription** - 用于应用在后台访问用户的位置时。

可以通过打开**info.plist**并选择编辑器底部的 "*源*" 来添加这些项。

更新**info.plist**并提示用户提供访问其位置的权限后，可以通过将 `ShowsUserLocation` 属性设置为 true 来在地图上显示用户的位置：

```csharp
map.ShowsUserLocation = true;
```

 ![](images/02-location-alert.png "The allow location access alert")

## <a name="annotations"></a>批注

 `MKMapView` 还支持在地图上显示图像（称为批注）。 它们可以是自定义图像，也可以是各种颜色的系统定义的 pin。 例如，以下屏幕截图显示了一个地图，其中包含一个 pin 和一个自定义映像：

 ![](images/03-annotations.png "This screenshot shows a map with a both a pin and a custom image")

### <a name="adding-an-annotation"></a>添加批注

批注本身包含两部分：

- `MKAnnotation` 对象，其中包括有关批注的模型数据，例如批注的标题和位置。
- `MKAnnotationView`，其中包含要显示的图像，还可以选择在用户点击批注时显示的标注。

Map 工具包使用 iOS 委托模式将批注添加到地图，其中，`MKMapView` 的 `Delegate` 属性设置为 `MKMapViewDelegate`的实例。 此委托的实现负责为批注返回 `MKAnnotationView`。

若要添加批注，请先通过对 `MKMapView` 实例调用 `AddAnnotations` 来添加批注：

```csharp
// add an annotation
map.AddAnnotations (new MKPointAnnotation (){
    Title="MyAnnotation",
    Coordinate = new CLLocationCoordinate2D (42.364260, -71.120824)
});
```

当批注的位置在地图上变为可见时，`MKMapView` 将调用其委托的 `GetViewForAnnotation` 方法来获取要显示的 `MKAnnotationView`。

例如，下面的代码返回系统提供的 `MKPinAnnotationView`：

```csharp
string pId = "PinAnnotation";

public override MKAnnotationView GetViewForAnnotation (MKMapView mapView, NSObject annotation)
{
    if (annotation is MKUserLocation)
        return null;

    // create pin annotation view
    MKAnnotationView pinView = (MKPinAnnotationView)mapView.DequeueReusableAnnotation (pId);

    if (pinView == null)
        pinView = new MKPinAnnotationView (annotation, pId);

    ((MKPinAnnotationView)pinView).PinColor = MKPinAnnotationColor.Red;
    pinView.CanShowCallout = true;

    return pinView;
}
```

### <a name="reusing-annotations"></a>重复使用批注

为了节省内存，`MKMapView` 允许对批注视图进行缓冲以便重用，这与重复使用表单元的方式类似。 使用对 `DequeueReusableAnnotation`的调用来从池中获取注释视图：

```csharp
MKAnnotationView pinView = (MKPinAnnotationView)mapView.DequeueReusableAnnotation (pId);
```

#### <a name="showing-callouts"></a>显示标注

如前文所述，批注可以选择性地显示标注。 若要显示标注，只需在 `MKAnnotationView`将 `CanShowCallout` 设置为 true。 这会导致在点击批注时显示批注标题，如下所示：

 ![](images/04-callout.png "The annotations title being displayed")

### <a name="customizing-the-callout"></a>自定义标注

还可以自定义标注以显示左侧和右侧的附件视图，如下所示：

```csharp
pinView.RightCalloutAccessoryView = UIButton.FromType (UIButtonType.DetailDisclosure);
pinView.LeftCalloutAccessoryView = new UIImageView(UIImage.FromFile ("monkey.png"));
```

此代码生成以下标注：

 ![](images/05-callout-accessories.png "An example callout")

若要处理用户点击正确的附件，只需在 `MKMapViewDelegate`中实现 `CalloutAccessoryControlTapped` 方法：

```csharp
public override void CalloutAccessoryControlTapped (MKMapView mapView, MKAnnotationView view, UIControl control)
{
    ...
}
```

### <a name="overlays"></a>标记

在地图上对图形进行分层的另一种方法是使用叠加。 叠加层支持绘制在地图缩放时随之缩放的图形内容。 iOS 提供多种类型的覆盖支持，包括：

- 多边形-通常用于突出显示地图上的某个区域。
- 折线-在显示路线时通常会出现这种情况。
- 圆圈-用于突出显示地图的圆形区域。

此外，还可以创建自定义覆盖，以显示具有精细自定义绘图代码的任意几何图形。 例如，天气雷达图非常适合用于自定义覆盖区。

#### <a name="adding-an-overlay"></a>添加覆盖区

与批注类似，添加覆盖区涉及2部分：

- 为覆盖对象创建模型对象并将其添加到 `MKMapView`。
- 在 `MKMapViewDelegate` 中为覆盖创建视图。

覆盖模型可以是任何 `MKShape` 子类。 Xamarin 包括分别通过 `MKPolygon`、`MKPolyline` 和 `MKCircle` 类 `MKShape` 多边形、折线和圆圈的子类。

例如，下面的代码用于添加 `MKCircle`：

```csharp
var circleOverlay = MKCircle.Circle (mapCenter, 1000);
map.AddOverlay (circleOverlay);
```

覆盖的视图是 `MKMapViewDelegate`中 `GetViewForOverlay` 返回的 `MKOverlayView` 实例。 每个 `MKShape` 都有一个相应的 `MKOverlayView`，它知道如何显示给定的形状。 对于 `MKPolygon` `MKPolygonView`。 同样，`MKPolyline` 对应于 `MKPolylineView`，`MKCircle` 存在 `MKCircleView`。

例如，以下代码返回 `MKCircle`的 `MKCircleView`：

```csharp
public override MKOverlayView GetViewForOverlay (MKMapView mapView, NSObject overlay)
{
    var circleOverlay = overlay as MKCircle;
    var circleView = new MKCircleView (circleOverlay);
    circleView.FillColor = UIColor.Blue;
    return circleView;
}
```

这会在地图上显示一个圆圈，如下所示：

 ![](images/06-circle-overlay.png "A circle displayed on the map")

## <a name="local-search"></a>本地搜索

iOS 包含带有地图工具包的本地搜索 API，该 API 允许在指定地理区域中对感兴趣的点进行异步搜索。

若要执行本地搜索，应用程序必须执行以下步骤：

1. 创建 `MKLocalSearchRequest` 对象。
1. 从 `MKLocalSearchRequest` 创建 `MKLocalSearch` 对象。
1. 对 `MKLocalSearch` 对象调用 `Start` 方法。
1. 检索回调中的 `MKLocalSearchResponse` 对象。

本地搜索 API 本身不提供用户界面。 它甚至不需要使用地图。 但是，若要充分利用本地搜索，应用程序需要提供某种方式来指定搜索查询并显示结果。 此外，由于结果将包含位置数据，因此在地图上显示它们通常是有意义的。

<a name="Adding_a_Local_Search_UI"/>

### <a name="adding-a-local-search-ui"></a>添加本地搜索 UI

接受搜索输入的一种方法是使用 `UISearchBar`，它由 `UISearchController` 提供，并将结果显示在表中。

下面的代码在 `MapViewController`的 `ViewDidLoad` 方法中添加 `UISearchController` （具有搜索栏属性）：

```csharp
//Creates an instance of a custom View Controller that holds the results
var searchResultsController = new SearchResultsViewController (map);

//Creates a search controller updater
var searchUpdater = new SearchResultsUpdator ();
searchUpdater.UpdateSearchResults += searchResultsController.Search;

//add the search controller
searchController = new UISearchController (searchResultsController) {
                SearchResultsUpdater = searchUpdater
            };

//format the search bar
searchController.SearchBar.SizeToFit ();
searchController.SearchBar.SearchBarStyle = UISearchBarStyle.Minimal;
searchController.SearchBar.Placeholder = "Enter a search query";

//the search bar is contained in the navigation bar, so it should be visible
searchController.HidesNavigationBarDuringPresentation = false;

//Ensure the searchResultsController is presented in the current View Controller
DefinesPresentationContext = true;

//Set the search bar in the navigation bar
NavigationItem.TitleView = searchController.SearchBar;
```

请注意，您负责将搜索栏对象并入用户界面。 在此示例中，我们将其分配给导航栏的 TitleView，但如果您的应用程序中未使用导航控制器，则必须找到另一个位置来显示它。

在此代码片段中，我们创建了另一个自定义视图控制器– `searchResultsController` –显示搜索结果，然后使用此对象创建搜索控制器对象。 我们还创建了一个新的搜索更新程序，当用户与搜索栏交互时，它将变为活动状态。 它接收每个击键的搜索通知，并负责更新 UI。
我们将介绍如何实现本指南后面的 `searchResultsController` 和 `searchResultsUpdater`。

这会导致在地图上显示搜索栏，如下所示：

 ![](images/07-searchbar.png "A search bar displayed over the map")

### <a name="displaying-the-search-results"></a>显示搜索结果

若要显示搜索结果，需要创建一个自定义视图控制器;通常为 `UITableViewController`。 如上所示，在创建时，`searchResultsController` 会传递到 `searchController` 的构造函数。
下面的代码示例演示如何创建此自定义视图控制器：

```csharp
public class SearchResultsViewController : UITableViewController
{
    static readonly string mapItemCellId = "mapItemCellId";
    MKMapView map;

    public List<MKMapItem> MapItems { get; set; }

    public SearchResultsViewController (MKMapView map)
    {
        this.map = map;

        MapItems = new List<MKMapItem> ();
    }

    public override nint RowsInSection (UITableView tableView, nint section)
    {
        return MapItems.Count;
    }

    public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
    {
        var cell = tableView.DequeueReusableCell (mapItemCellId);

        if (cell == null)
            cell = new UITableViewCell ();

        cell.TextLabel.Text = MapItems [indexPath.Row].Name;
        return cell;
    }

    public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
    {
        // add item to map
        CLLocationCoordinate2D coord = MapItems [indexPath.Row].Placemark.Location.Coordinate;
        map.AddAnnotations (new MKPointAnnotation () {
            Title = MapItems [indexPath.Row].Name,
            Coordinate = coord
        });

        map.SetCenterCoordinate (coord, true);

        DismissViewController (false, null);
    }

    public void Search (string forSearchString)
    {
        // create search request
        var searchRequest = new MKLocalSearchRequest ();
        searchRequest.NaturalLanguageQuery = forSearchString;
        searchRequest.Region = new MKCoordinateRegion (map.UserLocation.Coordinate, new MKCoordinateSpan (0.25, 0.25));

        // perform search
        var localSearch = new MKLocalSearch (searchRequest);

        localSearch.Start (delegate (MKLocalSearchResponse response, NSError error) {
            if (response != null && error == null) {
                this.MapItems = response.MapItems.ToList ();
                this.TableView.ReloadData ();
            } else {
                Console.WriteLine ("local search error: {0}", error);
            }
        });

    }
}
```

### <a name="updating-the-search-results"></a>更新搜索结果

`SearchResultsUpdater` 作为 `searchController`搜索栏和搜索结果之间的转存进程。

在此示例中，我们必须先在 `SearchResultsViewController`中创建搜索方法。 为此，我们必须创建一个 `MKLocalSearch` 对象并使用该对象发出对 `MKLocalSearchRequest`的搜索，并在传递给 `MKLocalSearch` 对象的 `Start` 方法的回调中检索结果。 然后，会在包含 `MKMapItem` 对象数组的 `MKLocalSearchResponse` 对象中返回结果：

```csharp
public void Search (string forSearchString)
{
    // create search request
    var searchRequest = new MKLocalSearchRequest ();
    searchRequest.NaturalLanguageQuery = forSearchString;
    searchRequest.Region = new MKCoordinateRegion (map.UserLocation.Coordinate, new MKCoordinateSpan (0.25, 0.25));

    // perform search
    var localSearch = new MKLocalSearch (searchRequest);

    localSearch.Start (delegate (MKLocalSearchResponse response, NSError error) {
        if (response != null && error == null) {
            this.MapItems = response.MapItems.ToList ();
            this.TableView.ReloadData ();
        } else {
            Console.WriteLine ("local search error: {0}", error);
        }
    });

}
```

然后，在我们的 `MapViewController` 中，我们将创建 `UISearchResultsUpdating`的自定义实现，该实现分配给在 "[添加本地搜索" UI](#Adding_a_Local_Search_UI)部分的 `searchController` 的 `SearchResultsUpdater` 属性：

```csharp
public class SearchResultsUpdator : UISearchResultsUpdating
{
    public event Action<string> UpdateSearchResults = delegate {};

    public override void UpdateSearchResultsForSearchController (UISearchController searchController)
    {
        this.UpdateSearchResults (searchController.SearchBar.Text);
    }
}
```

从结果中选择项时，上面的实现会将批注添加到地图中，如下所示：

 ![](images/08-search-results.png "An annotation added to the map when an item is selected from the results")

> [!IMPORTANT]
> `UISearchController` 是在 iOS 8 中实现的。 如果希望在此之前支持设备，则需要使用 `UISearchDisplayController`。

## <a name="summary"></a>总结

本文介绍适用于 iOS 的*地图* *工具包*框架。 首先，了解 `MKMapView` 类如何允许交互式映射包含在应用程序中。 然后演示了如何使用批注和叠加进一步自定义映射。 最后，它会检查已添加到 iOS 6.1 的地图工具包的本地搜索功能，说明如何使用 "基于位置执行基于位置的查询"，并将它们添加到地图中。

## <a name="related-links"></a>相关链接

- [SearchController](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/search-controller)
- [MapDemo （示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/mapdemo)
