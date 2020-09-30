---
title: Xamarin.Forms 形状：路径
description: Xamarin.FormsPath 类可用于绘制曲线和复杂形状。
ms.prod: xamarin
ms.assetid: B29486F4-9A5E-4588-ABDF-7EB1E69B9AE6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/21/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 80b0bcd84602fc999764e7e0896a44f83401a9dd
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91558890"
---
# <a name="no-locxamarinforms-shapes-path"></a>Xamarin.Forms 形状：路径

![预发行版 API](~/media/shared/preview.png)

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

`Path`类派生自 `Shape` 类，可用于绘制曲线和复杂形状。 这些曲线和形状通常使用对象进行描述 `Geometry` 。 有关 `Path` 该类继承自类的属性的信息 `Shape` ，请参阅[ Xamarin.Forms 形状](index.md)。

`Path` 定义以下属性:

- `Data`，类型为 `Geometry` ，它指定要绘制的形状。
- `RenderTransform`，类型为 `Transform` ，表示在绘制路径之前应用于该路径的几何图形的转换。

这些属性由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象提供支持；也就是说，它们可以作为数据绑定的目标，并能进行样式设置。

有关转换的详细信息，请参阅[ Xamarin.Forms 路径转换](path-transforms.md)。

## <a name="create-a-path"></a>创建路径

若要绘制路径，请创建 `Path` 对象并设置其 `Data` 属性。 设置属性有两种方法 `Data` ：

- `Data`使用路径标记语法，可以在 XAML 中设置的字符串值。 使用此方法时， `Path.Data` 值将对图形使用序列化格式。 通常，在创建此字符串值后，你不会手动对其进行编辑。 相反，您可以使用设计工具来处理数据，并将其导出为属性可以使用的字符串片段 `Data` 。
- 可以将属性设置 `Data` 为 `Geometry` 对象。 这可以是一个特定的 `Geometry` 对象，也可以是一个 `GeometryGroup` 可将多个 geometry 对象组合成单个对象的容器。

### <a name="create-a-path-with-path-markup-syntax"></a>使用路径标记语法创建路径

下面的 XAML 示例演示如何使用路径标记语法绘制三角形：

```xaml
<Path Data="M 10,100 L 100,100 100,50Z"
      Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Start" />
```

该 `Data` 字符串以 move 命令开头，并由指示 `M` ，这将为路径建立绝对起始点。 `L` line 命令，它创建从起点到指定终点的直线。 `Z` 是 close 命令，用于创建将当前点连接到起始点的线条。 结果是一个三角形：

![路径三角形](path-images/triangle.png "路径三角形")

> [!NOTE]
> 路径标记语法仅适用于 XAML。

有关路径标记语法的详细信息，请参阅[ Xamarin.Forms 路径标记语法](path-markup-syntax.md)。

### <a name="create-a-path-with-geometry-objects"></a>使用 Geometry 对象创建路径

可以使用对象描述曲线和形状 `Geometry` ，这些对象用于设置 `Path` 对象的 `Data` 属性。 有多种 `Geometry` 可供选择的对象。 `EllipseGeometry`、 `LineGeometry` 和 `RectangleGeometry` 类描述相对简单的形状。 若要创建更复杂的形状或创建曲线，请使用 `PathGeometry` 。

`PathGeometry` 对象由一个或多个 `PathFigure` 对象组成。 每个 `PathFigure` 对象都表示不同的形状。 每个 `PathFigure` 对象都由一个或多个 `PathSegment` 对象组成，每个对象表示形状的连接部分。 段类型包括 `LineSegment` 、 `BezierSegment` 、和 `ArcSegment` 类。

下面的 XAML 示例演示如何使用对象绘制三角形 `PathGeometry` ：

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

在此示例中，三角形的起始点为 10100)  (。 直线段从 (10100) 绘制到 (100100) ，从 (100100) 到 (100，50) 。 然后，将连接图的第一个和最后一个线段，因为 `PathFigure.IsClosed` 属性设置为 `true` 。 结果是一个三角形：

![路径三角形](path-images/triangle.png "路径三角形")

有关几何图形的详细信息，请参阅[ Xamarin.Forms 几何图形](geometries.md)。

## <a name="related-links"></a>相关链接

- [ShapeDemos (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms 形状](index.md)
- [Xamarin.Forms 几何图形](geometries.md)
- [Xamarin.Forms 路径标记语法](path-markup-syntax.md)
- [Xamarin.Forms 路径转换](path-transforms.md)