---
title: IOS 11 上 MapKit 中的新功能
description: 本文档介绍 iOS 11 中的新 MapKit 功能：分组标记、罗盘按钮、缩放视图和用户跟踪按钮。
ms.prod: xamarin
ms.assetid: 304AE5A3-518F-422F-BE24-92D62CE30F34
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/30/2017
ms.openlocfilehash: 8ccafec971a97a05dbf16488974d7a8e8e349708
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91437338"
---
# <a name="new-features-in-mapkit-on-ios-11"></a>IOS 11 上 MapKit 中的新功能

iOS 11 向 MapKit 添加了以下新功能：

- [批注聚类](#clustering)
- [罗盘按钮](#compass)
- [缩放视图](#scale)
- ["用户跟踪" 按钮](#user-tracking)

![显示聚集标记和罗盘按钮的地图](mapkit-images/cyclemap-heading.png)

<a name="clustering"></a>

## <a name="automatically-grouping-markers-while-zooming"></a>缩放时自动对标记进行分组

示例 [MapKit 示例 "Tandm"](/samples/xamarin/ios-samples/ios11-mapkitsample) 显示了如何实现新的 iOS 11 批注群集功能。

### <a name="1-create-an-mkpointannotation-subclass"></a>1. 创建 `MKPointAnnotation` 子类

Point 批注类表示地图上的每个标记。 可以通过使用 `MapView.AddAnnotation()` 或从数组中单独添加它们 `MapView.AddAnnotations()` 。

Point 批注类没有直观的表示形式，只需要表示与标记相关联的数据 (最重要的 `Coordinate` 是，它是地图上的纬度和经度) 以及任何自定义属性：

```csharp
public class Bike : MKPointAnnotation
{
  public BikeType Type { get; set; } = BikeType.Tricycle;
  public Bike(){}
  public Bike(NSNumber lat, NSNumber lgn, NSNumber type)
  {
    Coordinate = new CLLocationCoordinate2D(lat.NFloatValue, lgn.NFloatValue);
    switch(type.NUIntValue) {
      case 0:
        Type = BikeType.Unicycle;
        break;
      case 1:
        Type = BikeType.Tricycle;
        break;
    }
  }
}
```

### <a name="2-create-an-mkmarkerannotationview-subclass-for-single-markers"></a>2. `MKMarkerAnnotationView` 为单标记创建子类

标记批注视图是每个批注的可视化表示形式，并且使用属性（例如）进行样式化。

- **MarkerTintColor** –标记的颜色。
- **GlyphText** –标记中显示的文本。
- **GlyphImage** -设置显示在标记中的图像。
- **DisplayPriority** –确定当地图与标记拥挤时)  (堆栈行为的 z 顺序。 使用、或中的一个 `Required` `DefaultHigh` `DefaultLow` 。

若要支持自动群集，还必须设置：

- **ClusteringIdentifier** –此控制将哪些标记聚集在一起。 您可以对所有标记使用相同的标识符，或使用不同的标识符来控制它们组合在一起的方式。

```csharp
[Register("BikeView")]
public class BikeView : MKMarkerAnnotationView
{
  public static UIColor UnicycleColor = UIColor.FromRGB(254, 122, 36);
  public static UIColor TricycleColor = UIColor.FromRGB(153, 180, 44);
  public override IMKAnnotation Annotation
  {
    get {
      return base.Annotation;
    }
    set {
      base.Annotation = value;

      var bike = value as Bike;
      if (bike != null){
        ClusteringIdentifier = "bike";
        switch(bike.Type){
          case BikeType.Unicycle:
            MarkerTintColor = UnicycleColor;
            GlyphImage = UIImage.FromBundle("Unicycle");
            DisplayPriority = MKFeatureDisplayPriority.DefaultLow;
            break;
          case BikeType.Tricycle:
            MarkerTintColor = TricycleColor;
            GlyphImage = UIImage.FromBundle("Tricycle");
            DisplayPriority = MKFeatureDisplayPriority.DefaultHigh;
            break;
        }
      }
    }
  }
```

### <a name="3-create-an-mkannotationview-to-represent-clusters-of-markers"></a>3. 创建 `MKAnnotationView` 以表示标记的分类

尽管表示标记分类的批注视图 _可能_ 是一个简单的图像，但用户希望应用程序能够提供有关已组合在一起的标记数的视觉提示。

[示例代码](/samples/xamarin/ios-samples/ios11-mapkitsample)使用 CoreGraphics 来呈现分类中的标记数，以及每个标记类型的比例的圆关系图表示形式。

还应设置：

- **DisplayPriority** –确定当地图与标记拥挤时)  (堆栈行为的 z 顺序。 使用、或中的一个 `Required` `DefaultHigh` `DefaultLow` 。
- **CollisionMode** - `Circle` 或 `Rectangle` 。

```csharp
[Register("ClusterView")]
public class ClusterView : MKAnnotationView
{
  public static UIColor ClusterColor = UIColor.FromRGB(202, 150, 38);
  public override IMKAnnotation Annotation
  {
    get {
      return base.Annotation;
    }
    set {
      base.Annotation = value;
      var cluster = MKAnnotationWrapperExtensions.UnwrapClusterAnnotation(value);
      if (cluster != null)
      {
        var renderer = new UIGraphicsImageRenderer(new CGSize(40, 40));
        var count = cluster.MemberAnnotations.Length;
        var unicycleCount = CountBikeType(cluster.MemberAnnotations, BikeType.Unicycle);

        Image = renderer.CreateImage((context) => {
          // Fill full circle with tricycle color
          BikeView.TricycleColor.SetFill();
          UIBezierPath.FromOval(new CGRect(0, 0, 40, 40)).Fill();
          // Fill pie with unicycle color
          BikeView.UnicycleColor.SetFill();
          var piePath = new UIBezierPath();
          piePath.AddArc(new CGPoint(20,20), 20, 0, (nfloat)(Math.PI * 2.0 * unicycleCount / count), true);
          piePath.AddLineTo(new CGPoint(20, 20));
          piePath.ClosePath();
          piePath.Fill();
          // Fill inner circle with white color
          UIColor.White.SetFill();
          UIBezierPath.FromOval(new CGRect(8, 8, 24, 24)).Fill();
          // Finally draw count text vertically and horizontally centered
          var attributes = new UIStringAttributes() {
            ForegroundColor = UIColor.Black,
            Font = UIFont.BoldSystemFontOfSize(20)
          };
          var text = new NSString($"{count}");
          var size = text.GetSizeUsingAttributes(attributes);
          var rect = new CGRect(20 - size.Width / 2, 20 - size.Height / 2, size.Width, size.Height);
          text.DrawString(rect, attributes);
        });
      }
    }
  }
  public ClusterView(){}
  public ClusterView(MKAnnotation annotation, string reuseIdentifier) : base(annotation, reuseIdentifier)
  {
    DisplayPriority = MKFeatureDisplayPriority.DefaultHigh;
    CollisionMode = MKAnnotationViewCollisionMode.Circle;
    // Offset center point to animate better with marker annotations
    CenterOffset = new CoreGraphics.CGPoint(0, -10);
  }
  private nuint CountBikeType(IMKAnnotation[] members, BikeType type) {
    nuint count = 0;
    foreach(Bike member in members){
      if (member.Type == type) ++count;
    }
    return count;
  }
}
```

### <a name="4-register-the-view-classes"></a>4. 注册视图类

当创建地图视图控件并将其添加到视图中时，注册批注视图类型可启用自动聚类分析行为，因为地图会放大和缩小：

```csharp
MapView.Register(typeof(BikeView), MKMapViewDefault.AnnotationViewReuseIdentifier);
MapView.Register(typeof(ClusterView), MKMapViewDefault.ClusterAnnotationViewReuseIdentifier);
```

### <a name="5-render-the-map"></a>5. 呈现地图！

在呈现地图时，将根据缩放级别对批注标记进行群集或呈现。 当缩放级别更改时，标记将在群集中进行动画处理。

![显示地图上的聚集标记的模拟器](mapkit-images/cyclemap-sml.png)

有关用 MapKit 显示数据的详细信息，请参阅 [Maps 部分](~/ios/user-interface/controls/ios-maps/index.md) 。

<a name="compass"></a>

## <a name="compass-button"></a>罗盘按钮

iOS 11 增加了从地图中弹出罗盘的功能，并将其呈现在视图中的其他位置。 有关示例，请参阅 [Tandm 示例应用](/samples/xamarin/ios-samples/ios11-mapkitsample) 。

创建一个看起来像罗盘 (的按钮，当地图方向发生更改时，包括实时动画) ，然后在另一个控件上呈现该按钮。

![导航栏中的 "罗盘" 按钮](mapkit-images/compass-sml.png)

下面的代码创建一个罗盘按钮，并将其呈现在导航栏上：

```csharp
var compass = MKCompassButton.FromMapView(MapView);
compass.CompassVisibility = MKFeatureVisibility.Visible;
NavigationItem.RightBarButtonItem = new UIBarButtonItem(compass);
MapView.ShowsCompass = false; // so we don't have two compasses!
```

`ShowsCompass`属性可用于控制地图视图中默认罗盘的可见性。

<a name="scale"></a>

## <a name="scale-view"></a>缩放视图

使用方法，将刻度添加到视图中的其他位置，以便在 `MKScaleView.FromMapView()` 视图层次结构中的其他位置添加缩放视图的实例。

![地图上重叠的缩放视图](mapkit-images/scale-sml.png)

```csharp
var scale = MKScaleView.FromMapView(MapView);
scale.LegendAlignment = MKScaleViewAlignment.Trailing;
scale.TranslatesAutoresizingMaskIntoConstraints = false;
View.AddSubview(scale); // constraints omitted for simplicity
MapView.ShowsScale = false; // so we don't have two scale displays!
```

`ShowsScale`属性可用于控制地图视图中默认罗盘的可见性。

<a name="user-tracking"></a>

## <a name="user-tracking-button"></a>"用户跟踪" 按钮

"用户跟踪" 按钮将地图中心置于用户当前位置。 使用 `MKUserTrackingButton.FromMapView()` 方法可获取按钮实例，应用格式更改，并将其添加到视图层次结构中的其他位置。

![映射到地图上的用户位置按钮](mapkit-images/user-location-sml.png)

```csharp
var button = MKUserTrackingButton.FromMapView(MapView);
button.Layer.BackgroundColor = UIColor.FromRGBA(255,255,255,80).CGColor;
button.Layer.BorderColor = UIColor.White.CGColor;
button.Layer.BorderWidth = 1;
button.Layer.CornerRadius = 5;
button.TranslatesAutoresizingMaskIntoConstraints = false;
View.AddSubview(button); // constraints omitted for simplicity
```

## <a name="related-links"></a>相关链接

- [MapKit 示例 "Tandm"](/samples/xamarin/ios-samples/ios11-mapkitsample)
- [MKCompassButton](https://developer.apple.com/documentation/mapkit/mkcompassbutton)
- [MapKit 中的新增功能 (WWDC)  (视频) ](https://developer.apple.com/videos/play/wwdc2017/237/)