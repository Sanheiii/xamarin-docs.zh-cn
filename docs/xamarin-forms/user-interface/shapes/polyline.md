---
title: Xamarin.Forms 形状：折线
description: Xamarin.Forms折线类可用于绘制一系列连接的直线。
ms.prod: xamarin
ms.assetid: 15D02690-AC03-457E-8815-8E4C17E4D642
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/21/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3926e063fcabf9c70103e3ee72a4723358f26b2a
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91558864"
---
# <a name="no-locxamarinforms-shapes-polyline"></a>Xamarin.Forms 形状：折线

![预发行版 API](~/media/shared/preview.png)

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

`Polyline`类派生自 `Shape` 类，可用于绘制一系列连接的直线。 折线类似于多边形，只不过折线中的最后一个点未连接到第一个点。 有关 `Polyline` 该类继承自类的属性的信息 `Shape` ，请参阅[ Xamarin.Forms 形状](index.md)。

`Polyline` 定义以下属性:

- `Points`，类型为 `PointCollection` ，它是 `Point` 描述折线顶点的结构的集合。
- `FillRule`，类型为 `FillRule` ，指定如何组合折线中的相交区域。 此属性的默认值为 `FillRule.EvenOdd`。

这些属性由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象提供支持；也就是说，它们可以作为数据绑定的目标，并能进行样式设置。

`PointsCollection`类型是 `ObservableCollection` [`Point`](xref:Xamarin.Forms.Point) 对象的。 `Point`结构定义 `X` 类型的和属性， `Y` `double` 表示二维空间中的 x 坐标和 y 坐标对。 因此， `Points` 应将属性设置为描述折线顶点点的 x 坐标和 y 坐标对的列表，并用单个逗号和/或一个或多个空格分隔。 例如，"40，10 70，80" 和 "40 10，70 80" 都是有效的。

有关枚举的详细信息 `FillRule` ，请参阅[ Xamarin.Forms 形状：填充规则](fillrules.md)。

## <a name="create-a-polyline"></a>创建折线

若要绘制折线，请创建 `Polyline` 对象并将其 `Points` 属性设置为形状的顶点。 若要为折线指定轮廓，请将其 `Stroke` 属性设置为 [`Color`](xref:Xamarin.Forms.Color) 。 `StrokeThickness`属性指定折线轮廓的粗细。

> [!IMPORTANT]
> 如果将的 `Fill` 属性设置 `Polyline` 为，则 [`Color`](xref:Xamarin.Forms.Color) 会绘制折线的内部空间，即使起点和终点不相交也是如此。

下面的 XAML 示例演示如何绘制折线：

```xaml
<Polyline Points="0,0 10,30, 15,0 18,60 23,30 35,30 40,0 43,60 48,30 100,30"
          Stroke="Red"
          StrokeThickness="1" />
```

在此示例中，绘制了红色折线：

![折线](polyline-images/stroke.png "折线")

下面的 XAML 示例演示如何绘制虚线折线：

```xaml
<Polyline Points="0,0 10,30, 15,0 18,60 23,30 35,30 40,0 43,60 48,30 100,30"
          Stroke="Red"
          StrokeThickness="2"
          StrokeDashArray="1,1"
          StrokeDashOffset="6" />
```

在此示例中，折线为虚线：

![虚线折线](polyline-images/dashed.png "虚线折线")

有关绘制虚线折线的详细信息，请参阅 [绘制虚线形状](index.md#draw-dashed-shapes)。

下面的 XAML 示例显示了使用默认填充规则的折线：

```xaml
<Polyline Points="0 48, 0 144, 96 150, 100 0, 192 0, 192 96, 50 96, 48 192, 150 200 144 48"
          Fill="Blue"
          Stroke="Red"
          StrokeThickness="3" />
```

在此示例中，使用填充规则确定折线的填充行为 `EvenOdd` 。

![EvenOdd 折线](polyline-images/evenodd.png "EvenOdd polyine")

下面的 XAML 示例显示了一个使用 `Nonzero` 填充规则的折线：

```xaml
<Polyline Points="0 48, 0 144, 96 150, 100 0, 192 0, 192 96, 50 96, 48 192, 150 200 144 48"
          Fill="Black"
          FillRule="Nonzero"
          Stroke="Yellow"
          StrokeThickness="3" />
```

![非零折线](polyline-images/nonzero.png "非零折线")

在此示例中，使用填充规则确定折线的填充行为 `Nonzero` 。

## <a name="related-links"></a>相关链接

- [ShapeDemos (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms 形状](index.md)
- [Xamarin.Forms 形状：填充规则](fillrules.md)