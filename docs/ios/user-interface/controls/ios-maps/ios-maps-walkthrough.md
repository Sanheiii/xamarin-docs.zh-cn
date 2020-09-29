---
title: Xamarin 中的注释和叠加
description: 本文介绍了如何使用地图工具包的批注和叠加功能的分步演练。 它演示如何向应用程序添加映射，该应用程序在 Xamarin 演化2013会议的位置显示批注和覆盖区。
ms.prod: xamarin
ms.assetid: 1BC4F7FC-AE3C-46D7-A4D3-18E142F55B8E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 56306b04b57295a3625804bdbbb6a9810675be2b
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436767"
---
# <a name="annotations-and-overlays-in-xamarinios"></a>Xamarin 中的注释和叠加

我们将在本演练中生成的应用程序如下所示：

 [![示例 MapKit 应用](ios-maps-walkthrough-images/00-map-overlay.png)](ios-maps-walkthrough-images/00-map-overlay.png#lightbox)

可以在 [Maps 演练示例](/samples/xamarin/ios-samples/mapswalkthrough)中找到已完成的代码。

首先创建一个新的 **IOS 空项目**，并为其提供一个相关名称。 首先，将代码添加到视图控制器以显示 MapView，然后为我们的 MapDelegate 和自定义批注创建新类。 为此，请按照以下步骤进行操作：

## <a name="viewcontroller"></a>ViewController

1. 将以下命名空间添加到 `ViewController` ：

    ```csharp
    using MapKit;
    using CoreLocation;
    using UIKit
    using CoreGraphics
    ```

1. 将 `MKMapView` 实例变量与实例一起添加到类中 `MapDelegate` 。 我们很快就会创建 `MapDelegate` ：

    ```csharp
    public partial class ViewController : UIViewController
    {
        MKMapView map;
        MapDelegate mapDelegate;
        ...
    ```

1. 在控制器的 `LoadView` 方法中，添加 `MKMapView` 并将其设置为控制器的 `View` 属性：

    ```csharp
    public override void LoadView ()
    {
        map = new MKMapView (UIScreen.MainScreen.Bounds);
        View = map;
    }
    ```

    接下来，我们将添加代码来初始化 "ViewDidLoad" 方法中的映射。

1. 在 " `ViewDidLoad` 添加代码" 中设置地图类型，显示用户位置，并允许缩放和平移：

    ```csharp
    // change map type, show user location and allow zooming and panning
    map.MapType = MKMapType.Standard;
    map.ShowsUserLocation = true;
    map.ZoomEnabled = true;
    map.ScrollEnabled = true;

    ```

1. 接下来，添加代码以居中地图并设置其区域：

    ```csharp
    double lat = 30.2652233534254;
    double lon = -97.73815460962083;
    CLLocationCoordinate2D mapCenter = new CLLocationCoordinate2D (lat, lon);
    MKCoordinateRegion mapRegion = MKCoordinateRegion.FromDistance (mapCenter, 100, 100);
    map.CenterCoordinate = mapCenter;
    map.Region = mapRegion;

    ```

1. 创建的新实例  `MapDelegate` ，并将其分配给的 `Delegate` `MKMapView` 。 同样，我们将很快实现 `MapDelegate` ：

    ```csharp
    mapDelegate = new MapDelegate ();
    map.Delegate = mapDelegate;
    ```

1. 从 iOS 8 开始，你应该向你的用户请求授权才能使用其位置，因此，让我们将其添加到我们的示例中。 首先，定义一个 `CLLocationManager` 类级变量：

    ```csharp
    CLLocationManager locationManager = new CLLocationManager();
    ```

1. 在 `ViewDidLoad` 方法中，我们想要检查运行应用程序的设备是否使用 iOS 8，如果是，我们将在应用程序正在使用时请求授权：

    ```csharp
    if (UIDevice.CurrentDevice.CheckSystemVersion(8,0)){
        locationManager.RequestWhenInUseAuthorization ();
    }
    ```

1. 最后，我们需要编辑 **info.plist** 文件，以通知用户请求其位置的原因。 在**info.plist**的 "**源**" 菜单中，添加以下项：

    `NSLocationWhenInUseUsageDescription`

    和字符串：

    `Maps Walkthrough Docs Sample`.

## <a name="conferenceannotationcs--a-class-for-custom-annotations"></a>ConferenceAnnotation.cs –自定义批注的类

1. 我们将为名为的批注使用自定义类 `ConferenceAnnotation` 。 将以下类添加到项目中：

    ```csharp
    using System;
    using CoreLocation;
    using MapKit;

    namespace MapsWalkthrough
    {
        public class ConferenceAnnotation : MKAnnotation
        {
            string title;
            CLLocationCoordinate2D coord;

            public ConferenceAnnotation (string title,
            CLLocationCoordinate2D coord)
            {
                this.title = title;
                this.coord = coord;
            }

            public override string Title {
                get {
                    return title;
                }
            }

            public override CLLocationCoordinate2D Coordinate {
                get {
                    return coord;
                }
            }
        }
    }
    ```

## <a name="viewcontroller---adding-the-annotation-and-overlay"></a>ViewController-添加批注和覆盖

1. 现在， `ConferenceAnnotation` 我们可以将其添加到地图中。 返回的 `ViewDidLoad` 方法 `ViewController` ，将批注添加到地图中心坐标：

    ```csharp
    map.AddAnnotations (new ConferenceAnnotation ("Evolve Conference", mapCenter));
    ```

1. 我们还想要覆盖宾馆。 添加以下代码，以 `MKPolygon` 使用提供的酒店坐标创建，并通过调用将其添加到地图中 `AddOverlay` ：

    ```csharp
    // add an overlay of the hotel
    MKPolygon hotelOverlay = MKPolygon.FromCoordinates(
        new CLLocationCoordinate2D[]{
        new CLLocationCoordinate2D(30.2649977168594, -97.73863627705),
        new CLLocationCoordinate2D(30.2648461170005, -97.7381627734755),
        new CLLocationCoordinate2D(30.2648355402574, -97.7381750192576),
        new CLLocationCoordinate2D(30.2647791309417, -97.7379872505988),
        new CLLocationCoordinate2D(30.2654525150319, -97.7377341711021),
        new CLLocationCoordinate2D(30.2654807195004, -97.7377994819399),
        new CLLocationCoordinate2D(30.2655089239607, -97.7377994819399),
        new CLLocationCoordinate2D(30.2656428950368, -97.738346460207),
        new CLLocationCoordinate2D(30.2650364981811, -97.7385709662122),
        new CLLocationCoordinate2D(30.2650470749025, -97.7386199493406)
    });

    map.AddOverlay (hotelOverlay);
    ```

这会完成中的代码 `ViewDidLoad` 。 现在，我们需要实现 `MapDelegate` 类来分别处理创建批注和重叠视图。

## <a name="mapdelegate"></a>MapDelegate

1. 创建一个从继承的名为的类， `MapDelegate` `MKMapViewDelegate` 并包含一个 `annotationId` 要用作批注的重复使用标识符的变量：

    ```csharp
    class MapDelegate : MKMapViewDelegate
    {
        static string annotationId = "ConferenceAnnotation";
        ...
    }
    ```

    这里只有一个批注，因此不需要重复使用代码，但最好将其包含在内。

1. 实现 `GetViewForAnnotation` 方法，以便 `ConferenceAnnotation` 使用本演练中包含的 **conference.png** 图像返回的视图：

    ```csharp
    public override MKAnnotationView GetViewForAnnotation (MKMapView mapView, NSObject annotation)
    {
        MKAnnotationView annotationView = null;

        if (annotation is MKUserLocation)
            return null;

        if (annotation is ConferenceAnnotation) {

            // show conference annotation
            annotationView = mapView.DequeueReusableAnnotation (annotationId);

            if (annotationView == null)
                annotationView = new MKAnnotationView (annotation, annotationId);

            annotationView.Image = UIImage.FromFile ("images/conference.png");
            annotationView.CanShowCallout = true;
        }

        return annotationView;
    }
    ```

1. 当用户点击批注时，我们想要显示显示奥斯汀市的图像。 将以下变量添加到图像的中 `MapDelegate` ，并将其显示为视图：

    ```csharp
    UIImageView venueView;
    UIImage venueImage;
    ```

1. 接下来，若要在点击批注时显示图像，请 `DidSelectAnnotation` 按如下方式实现方法：

    ```csharp
    public override void DidSelectAnnotationView (MKMapView mapView, MKAnnotationView view)
    {
        // show an image view when the conference annotation view is selected
        if (view.Annotation is ConferenceAnnotation) {

            venueView = new UIImageView ();
            venueView.ContentMode = UIViewContentMode.ScaleAspectFit;
            venueImage = UIImage.FromFile ("image/venue.png");
            venueView.Image = venueImage;
            view.AddSubview (venueView);

            UIView.Animate (0.4, () => {
            venueView.Frame = new CGRect (-75, -75, 200, 200); });
        }
    }
    ```

1. 若要在用户通过点击地图上的其他位置取消选择批注时隐藏图像，请 `DidDeselectAnnotationView` 按如下方式实现方法：

    ```csharp
    public override void DidDeselectAnnotationView (MKMapView mapView, MKAnnotationView view)
    {
        // remove the image view when the conference annotation is deselected
        if (view.Annotation is ConferenceAnnotation) {

            venueView.RemoveFromSuperview ();
            venueView.Dispose ();
            venueView = null;
        }
    }
    ```

    现在，我们已准备好批注代码。 剩下的就是将代码添加到 `MapDelegate` 以创建酒店覆盖的视图。

1. 将的以下实现添加 `GetViewForOverlay` 到 `MapDelegate` ：

    ```csharp
    public override MKOverlayView GetViewForOverlay (MKMapView mapView, NSObject overlay)
    {
        // return a view for the polygon
        MKPolygon polygon = overlay as MKPolygon;
        MKPolygonView polygonView = new MKPolygonView (polygon);
        polygonView.FillColor = UIColor.Blue;
        polygonView.StrokeColor = UIColor.Red;
        return polygonView;
    }
    ```

运行应用程序。 现在，我们有了一个交互式地图，其中包含自定义批注和覆盖区！ 点击 "批注"，将显示 "德克萨斯" 的图像，如下所示：

 [![点击 "批注"，将显示 "德克萨斯" 的图像。](ios-maps-walkthrough-images/01-map-image.png)](ios-maps-walkthrough-images/01-map-image.png#lightbox)

## <a name="summary"></a>总结

本文介绍如何将批注添加到地图，以及如何为指定多边形添加覆盖区。 此外，我们还演示了如何向批注添加触控支持，以在地图上对图像进行动画处理。

## <a name="related-links"></a>相关链接

- [Maps 演练示例](/samples/xamarin/ios-samples/mapswalkthrough)
- [地图演示示例](/samples/xamarin/ios-samples/mapdemo)
- [iOS 地图](~/ios/user-interface/controls/ios-maps/index.md)