---
title: Xamarin.Forms 形状：多边形
description: Xamarin.Forms多边形类可用于绘制多边形，多边形是形成闭合形状的连接的一系列线条。
ms.prod: xamarin
ms.assetid: D6539F60-A5AC-46EF-86EB-E9F508EB1FA8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7464b3e4088986a87aa29c77ee8c6baa78266f84
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91558968"
---
# <a name="no-locxamarinforms-shapes-polygon"></a>Xamarin.Forms 形状：多边形

![预发行版 API](~/media/shared/preview.png)

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

`Polygon`类派生自 `Shape` 类，可用于绘制多边形，这系列线条是形成闭合形状的线。 有关 `Polygon` 该类继承自类的属性的信息 `Shape` ，请参阅[ Xamarin.Forms 形状](index.md)。

`Polygon` 定义以下属性:

- `Points`，类型为 `PointCollection` ，它是 [`Point`](xref:Xamarin.Forms.Point) 描述多边形顶点的结构的集合。
- `FillRule`，类型为 `FillRule` ，它指定如何确定形状的内部填充。 此属性的默认值为 `FillRule.EvenOdd`。

这些属性由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象提供支持；也就是说，它们可以作为数据绑定的目标，并能进行样式设置。

`PointsCollection`类型是 `ObservableCollection` [`Point`](xref:Xamarin.Forms.Point) 对象的。 `Point`结构定义 `X` 类型的和属性， `Y` `double` 表示二维空间中的 x 坐标和 y 坐标对。 因此， `Points` 应将属性设置为 x 坐标和 y 坐标对的列表，它们描述多边形顶点点，并用单个逗号和/或一个或多个空格分隔。 例如，"40，10 70，80" 和 "40 10，70 80" 都是有效的。

有关枚举的详细信息 `FillRule` ，请参阅[ Xamarin.Forms 形状：填充规则](fillrules.md)。

## <a name="create-a-polygon"></a>创建多边形

若要绘制多边形，请创建 `Polygon` 对象并将其 `Points` 属性设置为形状的顶点。 将自动绘制一条线，用于连接第一个点和最后一个点。 若要在多边形内绘制，请将其 `Fill` 属性设置为 [`Color`](xref:Xamarin.Forms.Color) 。 若要为多边形指定轮廓，请将其 `Stroke` 属性设置为 [`Color`](xref:Xamarin.Forms.Color) 。 `StrokeThickness`属性指定多边形轮廓的粗细。

下面的 XAML 示例演示如何绘制实心多边形：

```xaml
<Polygon Points="40,10 70,80 10,50"
         Fill="AliceBlue"
         Stroke="Green"
         StrokeThickness="5" />
```

在此示例中，绘制了表示三角形的实心多边形：

![填充多边形](polygon-images/filled.png "填充多边形")

下面的 XAML 示例演示如何绘制虚线多边形：

```xaml
<Polygon Points="40,10 70,80 10,50"
         Fill="AliceBlue"
         Stroke="Green"
         StrokeThickness="5"
         StrokeDashArray="1,1"
         StrokeDashOffset="6" />
```

在此示例中，多边形轮廓为虚线：

![虚线多边形](polygon-images/dashed.png "虚线多边形")

有关绘制虚线多边形的详细信息，请参阅 [绘制虚线形状](index.md#draw-dashed-shapes)。

下面的 XAML 示例显示了使用默认填充规则的多边形：

```xaml
<Polygon Points="0 48, 0 144, 96 150, 100 0, 192 0, 192 96, 50 96, 48 192, 150 200 144 48"
         Fill="Blue"
         Stroke="Red"
         StrokeThickness="3" />
```

在此示例中，每个多边形的填充行为都是使用 `EvenOdd` 填充规则确定的。

![EvenOdd 多边形](polygon-images/evenodd.png "EvenOdd 多边形")

下面的 XAML 示例显示了一个使用 `Nonzero` 填充规则的多边形：

```xaml
<Polygon Points="0 48, 0 144, 96 150, 100 0, 192 0, 192 96, 50 96, 48 192, 150 200 144 48"
         Fill="Black"
         FillRule="Nonzero"
         Stroke="Yellow"
         StrokeThickness="3" />
```

![非零多边形](polygon-images/nonzero.png "非零多边形")

在此示例中，每个多边形的填充行为都是使用 `Nonzero` 填充规则确定的。

## <a name="related-links"></a>相关链接

- [ShapeDemos (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms 形状](index.md)
- [Xamarin.Forms 形状：填充规则](fillrules.md)