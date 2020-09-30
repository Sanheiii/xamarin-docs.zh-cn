---
title: Xamarin.Forms 形状：几何图形
description: Xamarin.Forms 使用 geometry 类可以描述二维形状的几何。
ms.prod: xamarin
ms.assetid: 07DE3D66-1820-4642-BDDF-84146D40C99D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/24/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c6c6fb0ea9d3e2b728072fa36c3cdb2a86ade131
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91559319"
---
# <a name="no-locxamarinforms-shapes-geometries"></a>Xamarin.Forms 形状：几何图形

![预发行版 API](~/media/shared/preview.png)

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

`Geometry`类以及从中派生的类使你可以描述二维形状的几何。 `Geometry` 从两个或多个 geometry 对象创建的对象可以很简单，例如矩形和圆圈，或复合对象。 此外，还可以创建包含圆弧和曲线的更复杂的几何。

`Geometry`类是定义不同类别的几何图形的多个类的父类：

- `EllipseGeometry`，表示椭圆或圆的几何。
- `GeometryGroup`，它表示可将多个 geometry 对象组合成单个对象的容器。
- `LineGeometry`，它表示线条的几何。
- `PathGeometry`，它表示可由弧形、曲线、椭圆、直线和矩形组成的复杂形状的几何。
- `RectangleGeometry`，表示矩形或正方形的几何。

`Geometry`和 `Shape` 类看起来类似，因为它们都描述了二维形状，但有一个重要的差异。 类 `Geometry` 派生自 [`BindableObject`](xref:Xamarin.Forms.BindableObject) 类，而 `Shape` 类派生自 [`View`](xref:Xamarin.Forms.View) 类。 因此， `Shape` 对象可以呈现自身并参与布局系统，而对象则 `Geometry` 不能。 尽管 `Shape` 对象比对象更易于使用 `Geometry` ，但 `Geometry` 对象更通用。 `Shape`使用对象呈现2d 图形时， `Geometry` 可以使用对象来定义2d 图形的几何区域，并定义用于剪裁的区域。

以下类具有可设置为对象的属性 `Geometry` ：

- `Path`类使用 `Geometry` 来描述其内容。 可以 `Geometry` 通过将 `Path.Data` 属性设置为 `Geometry` 对象，并设置 `Path` 对象的 `Fill` 和 `Stroke` 属性来呈现。
- [`VisualElement`](xref:Xamarin.Forms.VisualElement)类具有一个类型为的 `Clip` 属性 `Geometry` ，该属性定义元素内容的轮廓。 当 `Clip` 属性设置为 `Geometry` 对象时，只有区域中的区域才 `Geometry` 可见。 有关详细信息，请参阅 [使用几何图形进行剪辑](#clip-with-a-geometry)。

派生自类的类 `Geometry` 可以分为三个类别：简单的几何图形、路径几何图形和复合几何图形。

## <a name="simple-geometries"></a>简单几何图形

简单的 geometry 类为 `EllipseGeometry` 、 `LineGeometry` 和 `RectangleGeometry` 。 它们用于创建基本几何形状，如圆、直线和矩形。 可以使用或通过将 geometry 对象组合在一起来创建这些相同的形状和更复杂的形状， `PathGeometry` 但这些类提供了一种更简单的方法来生成这些基本几何形状。

### <a name="ellipsegeometry"></a>System.windows.media.ellipsegeometry>

椭圆几何表示几何或椭圆或圆，并由中心点、x 轴半径和 y 轴半径定义。

`EllipseGeometry` 类定义了以下属性：

- `Center`[`Point`](xref:Xamarin.Forms.Point)表示几何图形中心点的类型的。
- `RadiusX`，类型为 `double` ，表示几何图形的 x 轴半径值。 此属性的默认值为0.0。
- `RadiusY`，类型为 `double` ，表示几何图形的 y 轴半径值。 此属性的默认值为0.0。

这些属性由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象提供支持；也就是说，它们可以作为数据绑定的目标，并能进行样式设置。

下面的示例演示如何 `EllipseGeometry` 在对象中创建和呈现 `Path` ：

```xaml
<Path Fill="Blue"
      Stroke="Red"
      StrokeThickness="1">
  <Path.Data>
    <EllipseGeometry Center="50,50"
                     RadiusX="50"
                     RadiusY="50" />
  </Path.Data>
</Path>
```

在此示例中，的中心 `EllipseGeometry` 设置为 (50，50) ，x 轴半径和 y 轴均设置为50。 这将创建一个红色圆圈，其直径为100与设备无关的单位，其内部绘制为蓝色：

![System.windows.media.ellipsegeometry>](geometry-images/ellipse.png "System.windows.media.ellipsegeometry>")

### <a name="linegeometry"></a>LineGeometry

直线几何表示线条的几何，并通过指定直线的起点和终点来定义。

`LineGeometry` 类定义了以下属性：

- `StartPoint`，类型为 [`Point`](xref:Xamarin.Forms.Point) ，它表示线条的起点。
- `EndPoint`，类型为 [`Point`](xref:Xamarin.Forms.Point) ，它表示线条的终点。

这些属性由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象提供支持；也就是说，它们可以作为数据绑定的目标，并能进行样式设置。

下面的示例演示如何 `LineGeometry` 在对象中创建和呈现 `Path` ：

```xaml
<Path Stroke="Black"
      StrokeThickness="1">
  <Path.Data>
    <LineGeometry StartPoint="10,20"
                  EndPoint="100,130" />
  </Path.Data>
</Path>
```

在此示例中， `LineGeometry` 从 (10，20) 绘制到 (100130) ：

![LineGeometry](geometry-images/line.png "LineGeometry")

> [!NOTE]
> 设置呈现的的 `Fill` 属性 `Path` `LineGeometry` 将不起作用，因为线条没有内部。

### <a name="rectanglegeometry"></a>RectangleGeometry

矩形几何表示矩形或正方形的几何，并定义为 `Rect` 指定其相对位置及其高度和宽度的结构。

`RectangleGeometry`类定义 `Rect` 类型的属性，该属性 `Rect` 表示矩形的尺寸。 此属性由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象支持，这意味着它可以是数据绑定的目标和样式。

下面的示例演示如何 `RectangleGeometry` 在对象中创建和呈现 `Path` ：

```xaml
<Path Fill="Blue"
      Stroke="Red"
      StrokeThickness="1">
  <Path.Data>
    <RectangleGeometry Rect="10,10,150,100" />
  </Path.Data>
</Path>
```

矩形的位置和尺寸由 `Rect` 结构定义。 在此示例中，该位置为 (10，10) ，宽度为150，高度为100与设备无关的单位：

![RectangleGeometry](geometry-images/rectangle.png "RectangleGeometry")

## <a name="path-geometries"></a>路径几何图形

路径几何描述了可由弧形、曲线、椭圆、直线和矩形组成的复杂形状。

`PathGeometry` 类定义了以下属性：

- `Figures`，类型为 `PathFigureCollection` ，它表示 `PathFigure` 描述路径内容的对象的集合。
- `FillRule`，类型为 `FillRule` ，确定如何组合几何图形中包含的相交区域。 此属性的默认值为 `FillRule.EvenOdd`。

这些属性由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象提供支持；也就是说，它们可以作为数据绑定的目标，并能进行样式设置。

有关枚举的详细信息 `FillRule` ，请参阅[ Xamarin.Forms 形状：填充规则](fillrules.md)。

> [!NOTE]
> `Figures`属性是类的 `ContentProperty` `PathGeometry` ，因此不需要从 XAML 显式设置。

由 `PathGeometry` 对象的集合组成 `PathFigure` ，每个对象 `PathFigure` 描述几何图形中的一个形状。 每个 `PathFigure` 对象都由一个或多个 `PathSegment` 对象组成，其中每个对象都描述了一段形状。 有多种类型的段：

- `ArcSegment`，它在两个点之间创建一条椭圆弧。
- `BezierSegment`，用于在两个点之间创建一条三次方贝塞尔曲线。
- `LineSegment`，它在两个点之间创建一条直线。
- `PolyBezierSegment`，它创建一系列三次贝塞尔曲线。
- `PolyLineSegment`，它创建一系列线条。
- `PolyQuadraticBezierSegment`，它创建一系列二次贝塞尔曲线。
- `QuadraticBezierSegment`，它创建二次贝塞尔曲线。

以上所有类均派生自抽象 `PathSegment` 类。

内的段 `PathFigure` 合并为单个几何形状，其中每个段的终点都是下一段的起点。 的 `StartPoint` 属性 `PathFigure` 指定绘制第一段的起点。 每个后续段都从上一段的终点开始。 例如， `10,50` `10,150` 可以通过将 `StartPoint` 属性设置为 `10,50` 并创建 `LineSegment` 具有 `Point` 属性设置的 `10,150` 来定义中的垂直线：

```xaml
<Path Stroke="Black"
      StrokeThickness="1">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigureCollection>
                    <PathFigure StartPoint="10,50">
                        <PathFigure.Segments>
                            <PathSegmentCollection>
                                <LineSegment Point="10,150" />
                            </PathSegmentCollection>
                        </PathFigure.Segments>
                    </PathFigure>
                </PathFigureCollection>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

可以通过组合使用 `PathSegment` 对象和使用中的多个对象，来创建更复杂的几何图形 `PathFigure` `PathGeometry` 。

### <a name="create-an-arcsegment"></a>创建 ArcSegment

在 `ArcSegment` 两个点之间创建一条椭圆弧。 椭圆弧由其起点和终点、x 轴和 y 轴半径、x 轴旋转系数、指示弧是否应大于180度的值以及描述弧线绘制方向的值来定义。

`ArcSegment` 类定义了以下属性：

- `Point`，类型为 [`Point`](xref:Xamarin.Forms.Point) ，表示椭圆弧的终结点。此属性的默认值为 (0，0) 。
- `Size`，类型为 [`Size`](xref:Xamarin.Forms.Size) ，表示圆弧的 x 轴和 y 轴半径。此属性的默认值为 (0，0) 。
- `RotationAngle`，类型为 `double` ，表示椭圆围绕 x 轴旋转的量（以度为单位）。 此属性的默认值为 0。
- `SweepDirection`，类型为 `SweepDirection` ，它指定弧的绘制方向。 此属性的默认值为 `SweepDirection.CounterClockwise`。
- `IsLargeArc`，类型为 `bool` ，指示弧是否应大于180度。 此属性的默认值为 `false`。

这些属性由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象提供支持；也就是说，它们可以作为数据绑定的目标，并能进行样式设置。

> [!NOTE]
> 此 `ArcSegment` 类不包含圆弧起始点的属性。它只定义它表示的弧线的终点。 圆弧的起点是添加到的的当前点 `PathFigure` `ArcSegment` 。

`SweepDirection` 枚举定义下列成员：

- `CounterClockwise`，指定以顺时针方向绘制弧。
- `Clockwise`，指定以逆时针方向绘制弧。

下面的示例演示如何 `ArcSegment` 在对象中创建和呈现 `Path` ：

```xaml
<Path Stroke="Black"
      StrokeThickness="1">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigureCollection>
                    <PathFigure StartPoint="10,100">
                        <PathFigure.Segments>
                            <PathSegmentCollection>
                                <ArcSegment Size="100,50"
                                            RotationAngle="45"
                                            IsLargeArc="True"
                                            SweepDirection="CounterClockwise"
                                            Point="200,100" />
                            </PathSegmentCollection>
                        </PathFigure.Segments>
                    </PathFigure>
                </PathFigureCollection>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

在此示例中，将从 (10100) 到 (200100) 绘制一个椭圆弧。

### <a name="create-a-beziersegment"></a>创建 System.windows.media.beziersegment>

在 `BezierSegment` 两个点之间创建一条三次贝塞尔曲线。 三次方贝塞尔曲线由四个点定义：起点、终点和两个控制点。

`BezierSegment` 类定义了以下属性：

- `Point1`，类型为 [`Point`](xref:Xamarin.Forms.Point) ，它表示曲线的第一个控制点。 此属性的默认值为 (0，0) 。
- `Point2`，类型为 [`Point`](xref:Xamarin.Forms.Point) ，它表示曲线的第二个控制点。 此属性的默认值为 (0，0) 。
- `Point3`，类型为 [`Point`](xref:Xamarin.Forms.Point) ，表示曲线的终点。 此属性的默认值为 (0，0) 。

这些属性由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象提供支持；也就是说，它们可以作为数据绑定的目标，并能进行样式设置。

> [!NOTE]
> `BezierSegment`类不包含曲线起点的属性。 曲线的起点是添加到的的当前点 `PathFigure` `BezierSegment` 。

三次方贝塞尔曲线的两个控制点的行为类似于磁体，可吸引其自身的直线，并生成一条曲线。 第一个控制点影响曲线的开始部分。 第二个控制点影响曲线的结束部分。 曲线不一定要经过任一控制点。 相反，每个控制点会将其行的部分移到其自身，而不是通过其自身移动。

下面的示例演示如何 `BezierSegment` 在对象中创建和呈现 `Path` ：

```xaml
<Path Stroke="Black"
      StrokeThickness="1">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigureCollection>
                    <PathFigure StartPoint="10,10">
                        <PathFigure.Segments>
                            <PathSegmentCollection>
                                <BezierSegment Point1="100,0"
                                               Point2="200,200"
                                               Point3="300,10" />
                            </PathSegmentCollection>
                        </PathFigure.Segments>
                    </PathFigure>
                </PathFigureCollection>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

在此示例中，一条三次方贝塞尔曲线从 (10，10) 绘制到 (300，10) 。 曲线在 (100、0) 和 (200200) 有两个控制点：

![System.windows.media.beziersegment>](geometry-images/beziersegment.png "System.windows.media.beziersegment>")

### <a name="create-a-linesegment"></a>创建 System.windows.media.linesegment>

在 `LineSegment` 两个点之间创建一条直线。

`LineSegment`类定义 `Point` 类型的属性，该属性 [`Point`](xref:Xamarin.Forms.Point) 表示线段的终点。 此属性的默认值为 (0，0) ，并由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象支持，这意味着它可以是数据绑定的目标和样式。

> [!NOTE]
> `LineSegment`类不包含行起点的属性。 它仅定义终结点。 直线的起点是添加到的的当前点 `PathFigure` `LineSegment` 。

下面的示例演示如何创建和呈现 `LineSegment` 对象中的对象 `Path` ：

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Start">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigureCollection>
                    <PathFigure IsClosed="True"
                                StartPoint="10,100">
                        <PathFigure.Segments>
                            <PathSegmentCollection>
                                <LineSegment Point="100,100" />
                                <LineSegment Point="100,50" />
                            </PathSegmentCollection>
                        </PathFigure.Segments>
                    </PathFigure>
                </PathFigureCollection>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

在此示例中，直线段从 (10100) 绘制到 (100100) ，从 (100100) 到 (100，50) 。 此外，将 `PathFigure` 关闭，因为其 `IsClosed` 属性设置为 `true` 。 这会导致绘制三角形：

![LineSegments](geometry-images/linesegments.png "LineSegments")

### <a name="create-a-polybeziersegment"></a>创建 PolyBezierSegment

`PolyBezierSegment`创建一条或多条三次方贝塞尔曲线。

`PolyBezierSegment`类定义 `Points` 类型的属性，该属性 `PointCollection` 表示定义的点 `PolyBezierSegment` 。 `PointCollection`是 `ObservableCollection` [`Point`](xref:Xamarin.Forms.Point) 对象的。 此属性由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象支持，这意味着它可以是数据绑定的目标和样式。

> [!NOTE]
> `PolyBezierSegment`类不包含曲线起点的属性。 曲线的起点是添加到的的当前点 `PathFigure` `PolyBezierSegment` 。

下面的示例演示如何 `PolyBezierSegment` 在对象中创建和呈现 `Path` ：

```xaml
<Path Stroke="Black"
      StrokeThickness="1">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigureCollection>
                    <PathFigure StartPoint="10,10">
                        <PathFigure.Segments>
                            <PathSegmentCollection>
                                <PolyBezierSegment Points="0,0 100,0 150,100 150,0 200,0 300,10" />
                            </PathSegmentCollection>
                        </PathFigure.Segments>
                    </PathFigure>
                </PathFigureCollection>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

在此示例中， `PolyBezierSegment` 指定两条三次贝塞尔曲线。 第一条曲线来自 (10，10) 到 (150100) ，控制点为 (0，0) ，另一个控制点为 (100，0) 。 第二条曲线来自 (150100) 到 (300，10) ，控制点为 (150，0) ，另一个控制点为 (200，0) ：

![PolyBezierSegment](geometry-images/polybeziersegment.png "PolyBezierSegment")

### <a name="create-a-polylinesegment"></a>创建 System.windows.media.polylinesegment>

`PolyLineSegment`创建一个或多个线段。

`PolyLineSegment`类定义 `Points` 类型的属性，该属性 `PointCollection` 表示定义的点 `PolyLineSegment` 。 `PointCollection`是 `ObservableCollection` [`Point`](xref:Xamarin.Forms.Point) 对象的。 此属性由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象支持，这意味着它可以是数据绑定的目标和样式。

> [!NOTE]
> `PolyLineSegment`类不包含行起点的属性。 直线的起点是添加到的的当前点 `PathFigure` `PolyLineSegment` 。

下面的示例演示如何 `PolyLineSegment` 在对象中创建和呈现 `Path` ：

```xaml
<Path Stroke="Black"
      StrokeThickness="1">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigure StartPoint="10,10">
                    <PathFigure.Segments>
                        <PolyLineSegment Points="50,10 50,50" />
                    </PathFigure.Segments>
                </PathFigure>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

在此示例中， `PolyLineSegment` 指定两行。 第一行来自 (10，10) 到 (50，10) ，第二行是从 (50，10)  (到) 50，50：

![System.windows.media.polylinesegment>](geometry-images/polylinesegment.png "System.windows.media.polylinesegment>")

### <a name="create-a-polyquadraticbeziersegment"></a>创建 PolyQuadraticBezierSegment

`PolyQuadraticBezierSegment`创建一个或多个二次贝塞尔曲线。

`PolyQuadraticBezierSegment`类定义 `Points` 类型的属性，该属性 `PointCollection` 表示定义的点 `PolyQuadraticBezierSegment` 。 `PointCollection`是 `ObservableCollection` [`Point`](xref:Xamarin.Forms.Point) 对象的。 此属性由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象支持，这意味着它可以是数据绑定的目标和样式。

> [!NOTE]
> `PolyQuadraticBezierSegment`类不包含曲线起点的属性。 曲线的起点是添加到的的当前点 `PathFigure` `PolyQuadraticBezierSegment` 。

下面的示例演示如何 `PolyQuadraticBezierSegment` 在对象中创建和呈现 `Path` ：

```xaml
<Path Stroke="Black"
      StrokeThickness="1">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigureCollection>
                    <PathFigure StartPoint="10,10">
                        <PathFigure.Segments>
                            <PathSegmentCollection>
                                <PolyQuadraticBezierSegment Points="100,100 150,50 0,100 15,200" />
                            </PathSegmentCollection>
                        </PathFigure.Segments>
                    </PathFigure>
                </PathFigureCollection>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

在此示例中， `PolyQuadraticBezierSegment` 指定两条贝塞尔曲线。 第一条曲线是从 (10，10) 到 (150，50) ， (100100) 使用控制点。 第二条曲线是从 (100100) 到 (15200) ，其中控制点为 (0100) ：

![PolyQuadraticBezierSegment](geometry-images/polyquadraticbeziersegment.png "PolyQuadraticBezierSegment")

### <a name="create-a-quadraticbeziersegment"></a>创建 System.windows.media.quadraticbeziersegment>

在 `QuadraticBezierSegment` 两点之间创建二次贝塞尔曲线。

`QuadraticBezierSegment` 类定义了以下属性：

- `Point1`，类型为 [`Point`](xref:Xamarin.Forms.Point) ，表示曲线的控制点。 此属性的默认值为 (0，0) 。
- `Point2`，类型为 [`Point`](xref:Xamarin.Forms.Point) ，表示曲线的终点。 此属性的默认值为 (0，0) 。

这些属性由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象提供支持；也就是说，它们可以作为数据绑定的目标，并能进行样式设置。

> [!NOTE]
> `QuadraticBezierSegment`类不包含曲线起点的属性。 曲线的起点是添加到的的当前点 `PathFigure` `QuadraticBezierSegment` 。

下面的示例演示如何 `QuadraticBezierSegment` 在对象中创建和呈现 `Path` ：

```xaml
<Path Stroke="Black"
      StrokeThickness="1">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigureCollection>
                    <PathFigure StartPoint="10,10">
                        <PathFigure.Segments>
                            <PathSegmentCollection>
                                <QuadraticBezierSegment Point1="200,200"
                                                        Point2="300,10" />
                            </PathSegmentCollection>
                        </PathFigure.Segments>
                    </PathFigure>
                </PathFigureCollection>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

在此示例中，二次贝塞尔曲线从 (10，10) 绘制到 (300，10) 。 曲线的控制点为 (200200) ：

![System.windows.media.quadraticbeziersegment>](geometry-images/quadraticbeziersegment.png "System.windows.media.quadraticbeziersegment>")

### <a name="create-complex-geometries"></a>创建复杂的几何图形

可以通过组合使用对象来创建更复杂的几何 `PathSegment` 。 下面的示例使用、和创建形状 `BezierSegment` `LineSegment` `ArcSegment` ：

```xaml
<Path Stroke="Black"
      StrokeThickness="1">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigure StartPoint="10,50">
                    <PathFigure.Segments>
                        <BezierSegment Point1="100,0"
                                       Point2="200,200"
                                       Point3="300,100"/>
                        <LineSegment Point="400,100" />
                        <ArcSegment Size="50,50"
                                    RotationAngle="45"
                                    IsLargeArc="True"
                                    SweepDirection="Clockwise"
                                    Point="200,100"/>
                    </PathFigure.Segments>
                </PathFigure>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

在此示例中， `BezierSegment` 首先使用四个点来定义。 然后，该示例添加一个 `LineSegment` ，它在的终点 `BezierSegment` 与指定的点之间绘制 `LineSegment` 。 最后， `ArcSegment` 从的终点绘制 `LineSegment` 到由指定的点 `ArcSegment` 。

使用中的多个对象可以创建更复杂的几何图形 `PathFigure` `PathGeometry` 。 下面的示例 `PathGeometry` 从七个 `PathFigure` 对象创建，其中一些对象包含多个 `PathSegment` 对象：

```xaml
<Path Stroke="Red"
      StrokeThickness="12"
      StrokeLineJoin="Round">
    <Path.Data>
        <PathGeometry>
            <!-- H -->
            <PathFigure StartPoint="0,0">
                <LineSegment Point="0,100" />
            </PathFigure>
            <PathFigure StartPoint="0,50">
                <LineSegment Point="50,50" />
            </PathFigure>
            <PathFigure StartPoint="50,0">
                <LineSegment Point="50,100" />
            </PathFigure>

            <!-- E -->
            <PathFigure StartPoint="125, 0">
                <BezierSegment Point1="60, -10"
                               Point2="60, 60"
                               Point3="125, 50" />
                <BezierSegment Point1="60, 40"
                               Point2="60, 110"
                               Point3="125, 100" />
            </PathFigure>

            <!-- L -->
            <PathFigure StartPoint="150, 0">
                <LineSegment Point="150, 100" />
                <LineSegment Point="200, 100" />
            </PathFigure>

            <!-- L -->
            <PathFigure StartPoint="225, 0">
                <LineSegment Point="225, 100" />
                <LineSegment Point="275, 100" />
            </PathFigure>

            <!-- O -->
            <PathFigure StartPoint="300, 50">
                <ArcSegment Size="25, 50"
                            Point="300, 49.9"
                            IsLargeArc="True" />
            </PathFigure>
        </PathGeometry>
    </Path.Data>
</Path>
```

在此示例中，使用和对象的组合以及单个对象来绘制单词 "Hello" `LineSegment` `BezierSegment` `ArcSegment` ：

![多个 System.windows.media.pathfigure> 对象](geometry-images/multiple-pathfigures.png "多个 System.windows.media.pathfigure> 对象")

## <a name="composite-geometries"></a>复合几何图形

复合几何图形对象可以使用创建 `GeometryGroup` 。 `GeometryGroup`类创建一个或多个对象的复合几何图形 `Geometry` 。 可以将任意数量的 `Geometry` 对象添加到 `GeometryGroup` 中。

`GeometryGroup` 类定义了以下属性：

- `Children`，类型为 `GeometryCollection` ，它物种定义的对象 `GeomtryGroup` 。 `GeometryCollection`是 `ObservableCollection` `Geometry` 对象的。
- `FillRule`，类型为 `FillRule` ，它指定如何组合中的相交区域 `GeometryGroup` 。 此属性的默认值为 `FillRule.EvenOdd`。

这些属性由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象提供支持；也就是说，它们可以作为数据绑定的目标，并能进行样式设置。

> [!NOTE]
> `Children`属性是类的 `ContentProperty` `GeometryGroup` ，因此不需要从 XAML 显式设置。

有关枚举的详细信息 `FillRule` ，请参阅[ Xamarin.Forms 形状：填充规则](fillrules.md)。

若要绘制复合几何图形，请将所需的 `Geometry` 对象设置为的子级 `GeometryGroup` ，并显示 `Path` 对象。 下面的 XAML 演示了一个示例：

```xaml
<Path Stroke="Green"
      StrokeThickness="2"
      Fill="Orange">
    <Path.Data>
        <GeometryGroup>
            <EllipseGeometry RadiusX="100"
                             RadiusY="100"
                             Center="150,150" />
            <EllipseGeometry RadiusX="100"
                             RadiusY="100"
                             Center="250,150" />
            <EllipseGeometry RadiusX="100"
                             RadiusY="100"
                             Center="150,250" />
            <EllipseGeometry RadiusX="100"
                             RadiusY="100"
                             Center="250,250" />
        </GeometryGroup>
    </Path.Data>
</Path>
```

在此示例中， `EllipseGeometry` 组合了具有相同 x 半径和 y 轴坐标但具有不同中心坐标的四个对象。 这会创建四个重叠圆圈，因为默认填充规则，其内部填充了橙色 `EvenOdd` ：

![GeometryGroup](geometry-images/geometrygroup.png "GeometryGroup")

## <a name="clip-with-a-geometry"></a>带有几何图形的剪辑

[`VisualElement`](xref:Xamarin.Forms.VisualElement)类具有一个类型为的 `Clip` 属性 `Geometry` ，该属性定义元素内容的轮廓。 当 `Clip` 属性设置为 `Geometry` 对象时，只有区域中的区域才 `Geometry` 可见。

下面的示例演示如何使用 `Geometry` 对象作为的剪辑区域 [`Image`](xref:Xamarin.Forms.Image) ：

```xaml
<Image Source="monkeyface.png">
    <Image.Clip>
        <EllipseGeometry RadiusX="100"
                         RadiusY="100"
                         Center="180,180" />
    </Image.Clip>
</Image>
```

在此示例中，的值为 `EllipseGeometry` `RadiusX` `RadiusY` 100， `Center` 值为 (180180) 设置为的 `Clip` 属性 [`Image`](xref:Xamarin.Forms.Image) 。 仅显示位于椭圆区域内的图像部分：

![使用 System.windows.media.ellipsegeometry> 剪切图像](geometries-images/clip-ellipsegeometry.png "使用 System.windows.media.ellipsegeometry> 剪切图像")

> [!NOTE]
> 简单的几何图形、路径几何图形和复合几何图形都可用于剪辑 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 对象。

## <a name="other-features"></a>其他功能

`GeometryHelper`类提供以下帮助器方法：

- `FlattenGeometry`，它将 `Geometry` 合并到中 `PathGeometry` 。
- `FlattenCubicBezier`，它将一条三次方贝塞尔曲线平展到 `List<Point>` 集合中。
- `FlattenQuadraticBezier`，它将二次贝塞尔曲线平展到 `List<Point>` 集合中。
- `FlattenArc`，它将椭圆形弧线平展到 `List<Point>` 集合中。

## <a name="related-links"></a>相关链接

- [ShapeDemos (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms 形状](index.md)
- [Xamarin.Forms 形状：填充规则](fillrules.md)