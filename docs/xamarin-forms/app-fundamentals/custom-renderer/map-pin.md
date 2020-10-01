---
title: 自定义图钉
description: 本文介绍如何为地图控件创建自定义呈现器，该控件显示带有自定义图钉的本机地图以及每个平台上图钉数据的自定义视图。
ms.prod: xamarin
ms.assetid: C5481D86-80E9-4E3D-9FB6-57B0F93711A6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/06/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 13d9ddb5ba13960b4ed5af8f4fdb98b486b7e93e
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562270"
---
# <a name="customizing-a-map-pin"></a>自定义图钉

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-map-pin)

本文介绍如何为地图控件创建自定义呈现器，该控件显示带有自定义图钉的本机地图以及每个平台上图钉数据的自定义视图。

每个 Xamarin.Forms 视图都有一个附带的呈现器，适用于创建本机控件实例的各个平台。 当 iOS 中的 Xamarin.Forms 应用程序呈现 [`Map`](xref:Xamarin.Forms.Maps.Map) 时，将实例化 `MapRenderer` 类，而该操作又会实例化本机 `MKMapView` 控件。 在 Android 平台上，`MapRenderer` 类实例化本机 `MapView` 控件。 在通用 Windows 平台 (UWP) 上，`MapRenderer` 类实例化本机 `MapControl`。 有关 Xamarin.Forms 控件映射到的呈现器和本机控件类的详细信息，请参阅[呈现器基类和本机控件](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

下图说明了 [`Map`](xref:Xamarin.Forms.Maps.Map) 和实现它的相应本机控件之间的关系：

![地图控件和实现的本机控件之间的关系](map-pin-images/map-classes.png)

通过在每个平台上为 [`Map`](xref:Xamarin.Forms.Maps.Map) 创建自定义呈现器，可以使用呈现过程来实现特定于平台的自定义。 执行此操作的过程如下：

1. [创建](#creating-the-custom-map) Xamarin.Forms 自定义地图。
1. [使用](#consuming-the-custom-map) Xamarin.Forms 中的自定义地图。
1. 在每个平台上为地图[创建](#creating-the-custom-renderer-on-each-platform)自定义呈现器。

现在将依次讨论每个项，以实现 `CustomMap` 呈现器，该呈现器显示具有自定义图钉的本机地图和每个平台上图钉数据的自定义视图。

> [!NOTE]
> 必须在使用前对 [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) 进行初始化和配置。 有关详细信息，请参阅 [`Maps Control`](~/xamarin-forms/user-interface/map/index.md)。

## <a name="creating-the-custom-map"></a>创建自定义地图

通过子类化 [`Map`](xref:Xamarin.Forms.Maps.Map) 类，可以创建自定义地图控件，如下面的代码示例中所示：

```csharp
public class CustomMap : Map
{
    public List<CustomPin> CustomPins { get; set; }
}
```

`CustomMap` 控件在 .NET Standard 库项目中创建，该控件定义自定义地图的 API。 自定义地图公开 `CustomPins` 属性，该属性表示将由每个平台上的本机地图控件呈现的 `CustomPin` 对象集合。 下面的代码示例对 `CustomPin` 类进行了演示：

```csharp
public class CustomPin : Pin
{
    public string Name { get; set; }
    public string Url { get; set; }
}
```

此类将 `CustomPin` 定义为继承 [`Pin`](xref:Xamarin.Forms.Maps.Pin) 类的属性，并添加 `Name` 和 `Url` 属性。

## <a name="consuming-the-custom-map"></a>使用自定义地图

通过在自定义地图控件上声明 `CustomMap` 控件位置的命名空间并使用命名空间前缀，可以在 .NET Standard 库项目的 XAML 中引用该控件。 下面的代码示例演示 XAML 页可以如何使用 `CustomMap` 控件：

```xaml
<ContentPage ...
                   xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer">
    <local:CustomMap x:Name="customMap"
                   MapType="Street" />
</ContentPage>

```

`local` 命名空间前缀可以命名为任何内容。 但是，`clr-namespace` 和 `assembly` 值必须与自定义地图的详细信息相匹配。 声明命名空间后，前缀用于引用自定义地图。

下面的代码示例演示 C# 页可以如何使用 `CustomMap` 控件：

```csharp
public class MapPageCS : ContentPage
{
    public MapPageCS()
    {
        CustomMap customMap = new CustomMap
        {
            MapType = MapType.Street
        };
        // ...
        Content = customMap;
    }
}
```

`CustomMap` 实例将用于显示每个平台上的本机地图。 它的 [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) 属性设置 [`Map`](xref:Xamarin.Forms.Maps.Map) 的显示样式，在 [`MapType`](xref:Xamarin.Forms.Maps.MapType) 枚举中定义可能的值。

初始化地图的位置及其包含的图钉，如以下代码示例所示：

```csharp
public MapPage()
{
    // ...
    CustomPin pin = new CustomPin
    {
        Type = PinType.Place,
        Position = new Position(37.79752, -122.40183),
        Label = "Xamarin San Francisco Office",
        Address = "394 Pacific Ave, San Francisco CA",
        Name = "Xamarin",
        Url = "http://xamarin.com/about/"
    };
    customMap.CustomPins = new List<CustomPin> { pin };
    customMap.Pins.Add(pin);
    customMap.MoveToRegion(MapSpan.FromCenterAndRadius(new Position(37.79752, -122.40183), Distance.FromMiles(1.0)));
}
```

此初始化添加自定义图钉，并使用 [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) 方法定位地图视图，该方法通过从 [`Position`](xref:Xamarin.Forms.Maps.Position) 和 [`Distance`](xref:Xamarin.Forms.Maps.Distance) 创建 [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) 来更改地图的位置和缩放级别。

现在可以向每个应用程序项目添加自定义呈现器，以自定义本机地图控件。

## <a name="creating-the-custom-renderer-on-each-platform"></a>在每个平台上创建自定义呈现器

创建自定义呈现器类的过程如下所示：

1. 创建呈现自定义地图的 `MapRenderer` 类的子类。
1. 替代呈现自定义地图的 `OnElementChanged` 方法，并编写逻辑以自定义地图。 创建相应的 Xamarin.Forms 自定义地图时，调用此方法。
1. 向自定义呈现器类添加 `ExportRenderer` 属性，以指定其将用于呈现 Xamarin.Forms 自定义地图。 此属性用于向 Xamarin.Forms 注册自定义呈现器。

> [!NOTE]
> 可选择在每个平台项目中提供自定义呈现器。 如果未注册自定义呈现器，将使用控件基类的默认呈现器。

下图说明了示例应用程序中每个项目的职责，以及它们之间的关系：

![CustomMap 自定义呈现器项目的职责](map-pin-images/solution-structure.png)

`CustomMap` 控件由平台特定的呈现器类呈现，这些类均派生自各平台的 `MapRenderer` 类。 这导致每个 `CustomMap` 控件都使用特定于平台的控件呈现，如下面的屏幕截图所示：

![每个平台上的 CustomMap](map-pin-images/screenshots.png)

`MapRenderer` 类公开 `OnElementChanged` 方法，创建 Xamarin.Forms 自定义地图时调用此方法以呈现对应的本机控件。 此方法采用 `ElementChangedEventArgs` 参数，其中包含 `OldElement` 和 `NewElement` 属性。 这两个属性分别表示呈现器“曾经”附加到的 Xamarin.Forms 元素和呈现器“现在”附加到的 Xamarin.Forms 元素 。 在示例应用程序中，`OldElement` 属性将为 `null`，且 `NewElement` 属性将包含对 `CustomMap` 实例的引用。

在每个特定于平台的呈现器类中，`OnElementChanged` 方法的替代版本可执行本机控件自定义。 可以通过 `Control` 属性访问平台上使用的对本机控件的类型化引用。 此外，可以通过 `Element` 属性获取正在呈现的 Xamarin.Forms 控件的引用。

订阅 `OnElementChanged` 方法中的事件处理程序时必须小心，如以下代码示例所示：

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<Xamarin.Forms.View> e)
{
  base.OnElementChanged (e);

  if (e.OldElement != null)
  {
      // Unsubscribe from event handlers
  }

  if (e.NewElement != null)
  {
      // Configure the native control and subscribe to event handlers
  }
}
```

仅当自定义呈现器附加到新的 Xamarin.Forms 元素时，才应配置本机控件并订阅事件处理程序。 同样，仅当呈现器所附加到的元素更改时，才应取消订阅任何订阅的事件处理程序。 采用此方法将有助于创建不会遭受内存泄漏的自定义呈现器。

每个自定义呈现器类均用 `ExportRenderer` 属性修饰，该属性向 Xamarin.Forms 注册呈现器。 该属性采用两个参数：要呈现的 Xamarin.Forms 自定义控件的类型名称和自定义呈现器的类型名称。 属性的 `assembly` 前缀指示属性适用于整个程序集。

以下各节讨论每个平台特定的自定义呈现器类的实现。

### <a name="creating-the-custom-renderer-on-ios"></a>在 iOS 上创建自定义呈现器

下面的屏幕截图显示自定义前后的地图：

![自定义前后的地图控件](map-pin-images/map-layout-ios.png)

在 iOS 上，图钉称为“注释”，可以是自定义映像，也可以是不同颜色的系统定义的图钉。 注释可以选择性地显示标注，以在用户选择注释时作出响应。 标注显示 `Pin` 实例的 `Label` 和 `Address` 属性，以及可选的左和右附件视图。 上面的屏幕截图中，左附件视图是猴子图像，而右附件视图是“信息”按钮。

以下代码示例展示了适用于 iOS 平台的自定义呈现器：

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace CustomRenderer.iOS
{
    public class CustomMapRenderer : MapRenderer
    {
        UIView customPinView;
        List<CustomPin> customPins;

        protected override void OnElementChanged(ElementChangedEventArgs<View> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                var nativeMap = Control as MKMapView;
                if (nativeMap != null)
                {
                    nativeMap.RemoveAnnotations(nativeMap.Annotations);
                    nativeMap.GetViewForAnnotation = null;
                    nativeMap.CalloutAccessoryControlTapped -= OnCalloutAccessoryControlTapped;
                    nativeMap.DidSelectAnnotationView -= OnDidSelectAnnotationView;
                    nativeMap.DidDeselectAnnotationView -= OnDidDeselectAnnotationView;
                }
            }

            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                var nativeMap = Control as MKMapView;
                customPins = formsMap.CustomPins;

                nativeMap.GetViewForAnnotation = GetViewForAnnotation;
                nativeMap.CalloutAccessoryControlTapped += OnCalloutAccessoryControlTapped;
                nativeMap.DidSelectAnnotationView += OnDidSelectAnnotationView;
                nativeMap.DidDeselectAnnotationView += OnDidDeselectAnnotationView;
            }
        }
        // ...
    }
}
```

如果自定义呈现器附加到新的 Xamarin.Forms 元素，则 `OnElementChanged` 方法执行 [`MKMapView`](xref:MapKit.MKMapView) 实例的以下配置：

- [`GetViewForAnnotation`](xref:MapKit.MKMapView.GetViewForAnnotation*) 属性设置为 `GetViewForAnnotation` 方法. 此方法在[地图上显示注释位置](#displaying-the-annotation)时调用，用于在显示前自定义注释。
- 已注册用于 `CalloutAccessoryControlTapped`、`DidSelectAnnotationView` 和 `DidDeselectAnnotationView` 事件的事件处理程序。 这些事件在以下情况下触发：用户[点击标注中的右附件](#tapping-on-the-right-callout-accessory-view)、用户[选择](#selecting-the-annotation)和[取消选择](#deselecting-the-annotation)注释。 仅当呈现器附加到的元素更改时，才取消订阅事件。

#### <a name="displaying-the-annotation"></a>显示注释

`GetViewForAnnotation` 方法在地图上显示注释位置时调用，用于在显示前自定义注释。 注释分为两个部分：

- `MkAnnotation` - 包括标题、副标题和注释位置。
- `MkAnnotationView` - 包含表示该注释的图像，和（可选）用户点击注释时显示的标注。

`GetViewForAnnotation` 方法接受包含注释数据并返回用于显示在地图上的 `MKAnnotationView` 的 `IMKAnnotation`，如以下代码示例所示：

```csharp
protected override MKAnnotationView GetViewForAnnotation(MKMapView mapView, IMKAnnotation annotation)
{
    MKAnnotationView annotationView = null;

    if (annotation is MKUserLocation)
        return null;

    var customPin = GetCustomPin(annotation as MKPointAnnotation);
    if (customPin == null)
    {
        throw new Exception("Custom pin not found");
    }

    annotationView = mapView.DequeueReusableAnnotation(customPin.Name);
    if (annotationView == null)
    {
        annotationView = new CustomMKAnnotationView(annotation, customPin.Name);
        annotationView.Image = UIImage.FromFile("pin.png");
        annotationView.CalloutOffset = new CGPoint(0, 0);
        annotationView.LeftCalloutAccessoryView = new UIImageView(UIImage.FromFile("monkey.png"));
        annotationView.RightCalloutAccessoryView = UIButton.FromType(UIButtonType.DetailDisclosure);
        ((CustomMKAnnotationView)annotationView).Name = customPin.Name;
        ((CustomMKAnnotationView)annotationView).Url = customPin.Url;
    }
    annotationView.CanShowCallout = true;

    return annotationView;
}
```

此方法确保注释显示为自定义图像，而不是系统定义的图钉，并确保点击注释时将显示标注，该标注包含注释标题和地址左侧和右侧的其他内容。 这通过以下操作实现：

1. 调用 `GetCustomPin` 方法以返回注释的自定义图钉数据。
1. 为了节省内存，将集中注释视图，以重用对 [`DequeueReusableAnnotation`](xref:MapKit.MKMapView.DequeueReusableAnnotation*) 的调用。
1. `CustomMKAnnotationView` 类使用 `Name` 和 `Url` 属性扩展 `MKAnnotationView` 类，这些属性对应 `CustomPin` 实例中相同的属性。 如果注释为 `null`，则创建 `CustomMKAnnotationView` 的新实例：
    - `CustomMKAnnotationView.Image` 属性设置为将表示地图上的注释的图像。
    - `CustomMKAnnotationView.CalloutOffset` 属性设置为 `CGPoint`，它指定在注释上方将标注居中。
    - `CustomMKAnnotationView.LeftCalloutAccessoryView` 属性设置为猴子图像，该图像将出现在注释标题和地址的左侧。
    - `CustomMKAnnotationView.RightCalloutAccessoryView` 属性设置为“信息”按钮，其将出现在注释标题和地址的右侧。
    - `CustomMKAnnotationView.Name` 属性设置为 `GetCustomPin` 方法返回的 `CustomPin.Name` 属性。 这使得注释能被识别，以便需要时其[标注可以进一步自定义](#selecting-the-annotation)。
    - `CustomMKAnnotationView.Url` 属性设置为 `GetCustomPin` 方法返回的 `CustomPin.Url` 属性。 用户[点击右标注附件视图中显示的按钮](#tapping-on-the-right-callout-accessory-view)时，将导航到该 URL。
1. [`MKAnnotationView.CanShowCallout`](xref:MapKit.MKAnnotationView.CanShowCallout*) 属性设置为 `true`，以便点击注释时显示标注。
1. 返回注释以在地图上显示。

#### <a name="selecting-the-annotation"></a>选择注释

用户点击注释时，触发 `DidSelectAnnotationView` 事件，该事件进而执行 `OnDidSelectAnnotationView` 方法：

```csharp
void OnDidSelectAnnotationView(object sender, MKAnnotationViewEventArgs e)
{
    CustomMKAnnotationView customView = e.View as CustomMKAnnotationView;
    customPinView = new UIView();

    if (customView.Name.Equals("Xamarin"))
    {
        customPinView.Frame = new CGRect(0, 0, 200, 84);
        var image = new UIImageView(new CGRect(0, 0, 200, 84));
        image.Image = UIImage.FromFile("xamarin.png");
        customPinView.AddSubview(image);
        customPinView.Center = new CGPoint(0, -(e.View.Frame.Height + 75));
        e.View.AddSubview(customPinView);
    }
}
```

如果所选注释将其 `Name` 属性设置为 `Xamarin`，则此方法通过将 `UIView` 实例添加到包含 Xamarin 徽标图像的现有标注，以扩展现有标注（其中包含左和右附件视图）。 这支持为不同的注释显示不同的标注。 `UIView` 实例会在现有标注上居中显示。

#### <a name="tapping-on-the-right-callout-accessory-view"></a>点击右标注附件视图

用户点击右标注附件视图中的“信息”按钮时，触发 `CalloutAccessoryControlTapped` 事件，该事件进而执行 `OnCalloutAccessoryControlTapped` 方法：

```csharp
void OnCalloutAccessoryControlTapped(object sender, MKMapViewAccessoryTappedEventArgs e)
{
    CustomMKAnnotationView customView = e.View as CustomMKAnnotationView;
    if (!string.IsNullOrWhiteSpace(customView.Url))
    {
        UIApplication.SharedApplication.OpenUrl(new Foundation.NSUrl(customView.Url));
    }
}
```

此方法打开 Web 浏览器并导航到 `CustomMKAnnotationView.Url` 属性中存储的地址。 请注意，地址是在 .NET Standard 库项目中创建 `CustomPin` 集合时定义的。

#### <a name="deselecting-the-annotation"></a>取消选择注释

注释显示且用户点击地图时，触发 `DidDeselectAnnotationView` 事件，该事件进而执行 `OnDidDeselectAnnotationView` 方法：

```csharp
void OnDidDeselectAnnotationView(object sender, MKAnnotationViewEventArgs e)
{
    if (!e.View.Selected)
    {
        customPinView.RemoveFromSuperview();
        customPinView.Dispose();
        customPinView = null;
    }
}
```

此方法确保在未选择现有标注时，标注的扩展部分（Xamarin 徽标的图像）也将停止显示，并释放其资源。

有关自定义 `MKMapView` 实例的详细信息，请参阅 [iOS Maps](~/ios/user-interface/controls/ios-maps/index.md)。

### <a name="creating-the-custom-renderer-on-android"></a>在 Android 上创建自定义呈现器

下面的屏幕截图显示自定义前后的地图：

![自定义前后的地图控件](map-pin-images/map-layout-android.png)

在 Android 上，图钉称为“标记”，可以是自定义图像，也可以是不同颜色的系统定义的标记。 标记可以显示信息窗口，在用户点击标记时作出响应。 信息窗口显示 `Pin` 实例的 `Label` 和 `Address` 属性，并可以通过自定义包含其他内容。 但是，一次只可以显示一个信息窗口。

以下代码示例展示了适用于 Android 平台的自定义呈现器：

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace CustomRenderer.Droid
{
    public class CustomMapRenderer : MapRenderer, GoogleMap.IInfoWindowAdapter
    {
        List<CustomPin> customPins;

        public CustomMapRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(Xamarin.Forms.Platform.Android.ElementChangedEventArgs<Map> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                NativeMap.InfoWindowClick -= OnInfoWindowClick;
            }

            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                customPins = formsMap.CustomPins;
            }
        }

        protected override void OnMapReady(GoogleMap map)
        {
            base.OnMapReady(map);

            NativeMap.InfoWindowClick += OnInfoWindowClick;
            NativeMap.SetInfoWindowAdapter(this);
        }
        ...
    }
}
```

如果自定义呈现器附加到新的 Xamarin.Forms 元素，则 `OnElementChanged` 方法从控件中检索自定义图钉的列表。 一旦 `GoogleMap` 实例可用，将调用 `OnMapReady` 替代。 此方法为 `InfoWindowClick` 事件注册事件处理程序，[信息窗口被点击](#clicking-on-the-info-window)时激发该事件，并且仅当呈现器所附加到的元素更改时才取消订阅该事件。 `OnMapReady` 替代还会调用 `SetInfoWindowAdapter` 方法，以指定 `CustomMapRenderer` 类实例将提供自定义信息窗口的方法。

`CustomMapRenderer` 类实现 `GoogleMap.IInfoWindowAdapter` 接口以[自定义信息窗口](#customizing-the-info-window)。 此接口指定必须实现以下方法：

- `public Android.Views.View GetInfoWindow(Marker marker)` - 调用此方法以返回标记的自定义信息窗口。 如果它返回 `null`，则之后将使用默认窗口呈现。 如果它返回 `View`，则会将 `View` 置于信息窗口框架内。
- `public Android.Views.View GetInfoContents(Marker marker)` - 调用此方法以返回包含信息窗口内容的 `View`，并且将仅在 `GetInfoWindow` 方法返回 `null` 时调用。 如果它返回 `null`，则将使用信息窗口内容的默认呈现。

在示例应用程序中，仅自定义信息窗口内容，因此 `GetInfoWindow` 方法返回 `null` 以启用此功能。

#### <a name="customizing-the-marker"></a>自定义标记

可以通过调用 `MarkerOptions.SetIcon` 方法自定义用于表示标记的图标。 这可以通过重写 `CreateMarker` 方法来实现，该方法针对添加到地图的每个 `Pin` 调用：

```csharp
protected override MarkerOptions CreateMarker(Pin pin)
{
    var marker = new MarkerOptions();
    marker.SetPosition(new LatLng(pin.Position.Latitude, pin.Position.Longitude));
    marker.SetTitle(pin.Label);
    marker.SetSnippet(pin.Address);
    marker.SetIcon(BitmapDescriptorFactory.FromResource(Resource.Drawable.pin));
    return marker;
}
```

此方法为每个 `Pin` 实例创建新的 `MarkerOption` 实例。 设置位置、标签和标记的地址后，使用 `SetIcon` 方法设置其图标。 此方法采用 `BitmapDescriptor` 对象，该对象包含呈现图标的必要数据，`BitmapDescriptorFactory` 类提供帮助程序方法以简化 `BitmapDescriptor` 的创建。 有关使用 `BitmapDescriptorFactory` 类自定义标记的详细信息，请参阅[自定义标记](~/android/platform/maps-and-location/maps/maps-api.md)。

> [!NOTE]
> 如果需要，可以在地图呈现器中调用 `GetMarkerForPin` 方法以检索 `Pin` 中的 `Marker`。

#### <a name="customizing-the-info-window"></a>自定义信息窗口

用户点击标记时，如果 `GetInfoWindow` 方法返回 `null`，则执行 `GetInfoContents` 方法。 下面的代码示例说明 `GetInfoContents` 方法：

```csharp
public Android.Views.View GetInfoContents(Marker marker)
{
    var inflater = Android.App.Application.Context.GetSystemService(Context.LayoutInflaterService) as Android.Views.LayoutInflater;
    if (inflater != null)
    {
        Android.Views.View view;

        var customPin = GetCustomPin(marker);
        if (customPin == null)
        {
            throw new Exception("Custom pin not found");
        }

        if (customPin.Name.Equals("Xamarin"))
        {
            view = inflater.Inflate(Resource.Layout.XamarinMapInfoWindow, null);
        }
        else
        {
            view = inflater.Inflate(Resource.Layout.MapInfoWindow, null);
        }

        var infoTitle = view.FindViewById<TextView>(Resource.Id.InfoWindowTitle);
        var infoSubtitle = view.FindViewById<TextView>(Resource.Id.InfoWindowSubtitle);

        if (infoTitle != null)
        {
            infoTitle.Text = marker.Title;
        }
        if (infoSubtitle != null)
        {
            infoSubtitle.Text = marker.Snippet;
        }

        return view;
    }
    return null;
}
```

此方法返回包含信息窗口内容的 `View`。 这通过以下操作实现：

- 检索 `LayoutInflater` 实例。 这用于将布局 XML 文件实例化到其对应的 `View`。
- 调用 `GetCustomPin` 方法以返回信息窗口的自定义图钉数据。
- 如果 `CustomPin.Name` 属性等于 `Xamarin`，则放大 `XamarinMapInfoWindow` 布局。 否则，放大 `MapInfoWindow` 布局。 这支持为不同的标记显示不同的信息窗口布局。
- 从放大的布局检索 `InfoWindowTitle` 和 `InfoWindowSubtitle` 资源，如果资源不是 `null`，则 `Text` 属性设置为 `Marker` 实例中的相应数据。
- 返回 `View` 实例以便在地图上显示。

> [!NOTE]
> 信息窗口不是实时的 `View`。 Android 会将 `View` 转换为静态位图并将其显示为图像。 这意味着，尽管信息窗口可以响应单击事件，但它不能响应任何触摸事件或手势，而且信息窗口中的各个控件不能响应它们自己的单击事件。

#### <a name="clicking-on-the-info-window"></a>单击信息窗口

用户点击信息窗口时，触发 `InfoWindowClick` 事件，然后该事件执行 `OnInfoWindowClick` 方法：

```csharp
void OnInfoWindowClick(object sender, GoogleMap.InfoWindowClickEventArgs e)
{
    var customPin = GetCustomPin(e.Marker);
    if (customPin == null)
    {
        throw new Exception("Custom pin not found");
    }

    if (!string.IsNullOrWhiteSpace(customPin.Url))
    {
        var url = Android.Net.Uri.Parse(customPin.Url);
        var intent = new Intent(Intent.ActionView, url);
        intent.AddFlags(ActivityFlags.NewTask);
        Android.App.Application.Context.StartActivity(intent);
    }
}
```

此方法打开 Web 浏览器并导航到检索到的 `Marker` 的 `CustomPin` 实例的 `Url` 属性中存储的地址。 请注意，地址是在 .NET Standard 库项目中创建 `CustomPin` 集合时定义的。

有关自定义 `MapView` 实例的详细信息，请参阅[地图 API](~/android/platform/maps-and-location/maps/maps-api.md)。

### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>在通用 Windows 平台上创建自定义呈现器

下面的屏幕截图显示自定义前后的地图：

![自定义前后的地图控件](map-pin-images/map-layout-uwp.png)

在 UWP 上，图钉称为“地图图标”，可以是自定义图像，也可以是系统定义的默认图像。 地图图标可以显示 `UserControl`，在用户点击地图图标时显示。 `UserControl` 可以显示任何内容，包括 `Pin` 实例的 `Label` 和 `Address` 属性。

以下代码示例显示 UWP 自定义呈现器：

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace CustomRenderer.UWP
{
    public class CustomMapRenderer : MapRenderer
    {
        MapControl nativeMap;
        List<CustomPin> customPins;
        XamarinMapOverlay mapOverlay;
        bool xamarinOverlayShown = false;

        protected override void OnElementChanged(ElementChangedEventArgs<Map> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                nativeMap.MapElementClick -= OnMapElementClick;
                nativeMap.Children.Clear();
                mapOverlay = null;
                nativeMap = null;
            }

            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                nativeMap = Control as MapControl;
                customPins = formsMap.CustomPins;

                nativeMap.Children.Clear();
                nativeMap.MapElementClick += OnMapElementClick;

                foreach (var pin in customPins)
                {
                    var snPosition = new BasicGeoposition { Latitude = pin.Pin.Position.Latitude, Longitude = pin.Pin.Position.Longitude };
                    var snPoint = new Geopoint(snPosition);

                    var mapIcon = new MapIcon();
                    mapIcon.Image = RandomAccessStreamReference.CreateFromUri(new Uri("ms-appx:///pin.png"));
                    mapIcon.CollisionBehaviorDesired = MapElementCollisionBehavior.RemainVisible;
                    mapIcon.Location = snPoint;
                    mapIcon.NormalizedAnchorPoint = new Windows.Foundation.Point(0.5, 1.0);

                    nativeMap.MapElements.Add(mapIcon);                    
                }
            }
        }
        ...
    }
}
```

如果自定义呈现器附加到新的 Xamarin.Forms 元素，则 `OnElementChanged` 方法执行以下操作：

- 在注册 `MapElementClick` 事件的事件处理程序之前，它清除 `MapControl.Children` 集合以删除地图中的任何现有的用户界面元素。 用户点击或单击 `MapControl` 上的 `MapElement` 时，触发此事件，并且仅当呈现器附加到的元素更改时才取消订阅。
- `customPins` 集合中的每个图钉显示在地图的正确地理位置上，如下所示:
  - 图钉位置创建为 `Geopoint` 实例。
  - 创建 `MapIcon` 实例以表示图钉。
  - 通过设置 `MapIcon.Image` 属性来指定用于表示 `MapIcon` 的图像。 但是，并不能保证始终显示地图图标的图像，因为地图上的其他元素可能遮挡该图像。 因此，地图图标的 `CollisionBehaviorDesired` 属性设置为`MapElementCollisionBehavior.RemainVisible`，以确保它始终显示。
  - 通过设置 `MapIcon.Location` 属性指定 `MapIcon` 的位置。
  - `MapIcon.NormalizedAnchorPoint` 属性设置为图像上指针的近似位置。 如果此属性保留其默认值 (0，0)（表示图像的左上角），地图缩放级别的更改可能导致图像指向其他位置。
  - 向 `MapControl.MapElements` 集合添加 `MapIcon` 实例。 这导致地图图标显示在 `MapControl` 上。

> [!NOTE]
> 将同一图像用于多个地图图标时，为了获得最佳性能，应该在页面或应用程序级别声明 `RandomAccessStreamReference` 实例。

#### <a name="displaying-the-usercontrol"></a>显示用户控件

用户点击地图图标时，执行 `OnMapElementClick` 方法。 下面的代码示例演示此方法：

```csharp
private void OnMapElementClick(MapControl sender, MapElementClickEventArgs args)
{
    var mapIcon = args.MapElements.FirstOrDefault(x => x is MapIcon) as MapIcon;
    if (mapIcon != null)
    {
        if (!xamarinOverlayShown)
        {
            var customPin = GetCustomPin(mapIcon.Location.Position);
            if (customPin == null)
            {
                throw new Exception("Custom pin not found");
            }

            if (customPin.Name.Equals("Xamarin"))
            {
                if (mapOverlay == null)
                {
                    mapOverlay = new XamarinMapOverlay(customPin);
                }

                var snPosition = new BasicGeoposition { Latitude = customPin.Position.Latitude, Longitude = customPin.Position.Longitude };
                var snPoint = new Geopoint(snPosition);

                nativeMap.Children.Add(mapOverlay);
                MapControl.SetLocation(mapOverlay, snPoint);
                MapControl.SetNormalizedAnchorPoint(mapOverlay, new Windows.Foundation.Point(0.5, 1.0));
                xamarinOverlayShown = true;
            }
        }
        else
        {
            nativeMap.Children.Remove(mapOverlay);
            xamarinOverlayShown = false;
        }
    }
}
```

此方法创建显示图钉信息的 `UserControl` 实例。 这通过以下操作实现：

- 检索 `MapIcon` 实例。
- 调用 `GetCustomPin` 方法以返回将显示的图钉数据。
- 创建 `XamarinMapOverlay` 实例以显示自定义的图钉数据。 此类是用户控件。
- 将在 `MapControl` 上显示 `XamarinMapOverlay` 实例的地理位置创建为 `Geopoint` 实例。
- 向 `MapControl.Children` 集合添加 `XamarinMapOverlay` 实例。 此集合包含将在地图上显示的 XAML 用户界面元素。
- 通过调用 `SetLocation` 方法在地图上设置 `XamarinMapOverlay` 实例的地理位置。
- 通过调用 `SetNormalizedAnchorPoint` 方法来设置对应特定位置的 `XamarinMapOverlay` 实例的相对位置。 这可确保地图缩放级别的更改导致 `XamarinMapOverlay` 实例始终在正确位置显示。

或者，如果地图上已显示有关图钉的信息，点击地图以移除 `MapControl.Children` 集合中的 `XamarinMapOverlay` 实例。

#### <a name="tapping-on-the-information-button"></a>点击“信息”按钮

用户点击 `XamarinMapOverlay` 用户控件中的“信息”按钮时，触发 `Tapped` 事件，然后该事件执行 `OnInfoButtonTapped` 方法：

```csharp
private async void OnInfoButtonTapped(object sender, TappedRoutedEventArgs e)
{
    await Launcher.LaunchUriAsync(new Uri(customPin.Url));
}
```

此方法打开 Web 浏览器并导航到 `CustomPin` 实例的 `Url` 属性中存储的地址。 请注意，地址是在 .NET Standard 库项目中创建 `CustomPin` 集合时定义的。

有关自定义 `MapControl` 实例的详细信息，请参阅 MSDN 上的[地图和位置概述](/windows/uwp/maps-and-location/)。

## <a name="related-links"></a>相关链接

- [地图控件](~/xamarin-forms/user-interface/map/index.md)
- [iOS 地图](~/ios/user-interface/controls/ios-maps/index.md)
- [地图 API](~/android/platform/maps-and-location/maps/maps-api.md)
- [自定义的图钉（示例）](/samples/xamarin/xamarin-forms-samples/customrenderers-map-pin)