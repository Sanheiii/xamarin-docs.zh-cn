---
title: Xamarin.Forms 形状：填充规则
description: Xamarin.Forms 形状填充规则确定点是否位于形状的填充区域。
ms.prod: xamarin
ms.assetid: 5CABB22B-C6BE-43D1-91D9-6E90A4BD5622
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/24/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a1277b0acda598128e69b39a99910a468f6baa11
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91559254"
---
# <a name="no-locxamarinforms-shapes-fill-rules"></a>Xamarin.Forms 形状：填充规则

![预发行版 API](~/media/shared/preview.png)

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

多个 Xamarin.Forms 形状类具有 `FillRule` 类型为的属性 `FillRule` 。 其中包括 `Polygon` 、 `Polyline` 和 `GeometryGroup` 。

`FillRule`枚举定义 `EvenOdd` 和 `Nonzero` 成员。 每个成员都代表一个不同的规则，用于确定某个点是否位于形状的填充区域。

> [!IMPORTANT]
> 出于填充规则的目的，所有形状都被视为已关闭。

## <a name="evenodd"></a>EvenOdd

`EvenOdd`填充规则按任意方向从点到无限大绘制一条射线，并计算该射线所交叉的形状内的段数。 如果此数为奇数，则该点在内部。 如果此数值为偶数，则该点在外部。

下面的 XAML 示例创建并呈现复合形状，其中 `FillRule` 默认为 `EvenOdd` ：

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Fill="#CCCCFF"
      Aspect="Uniform"
      HorizontalOptions="Start">
    <Path.Data>
        <!-- FillRule doesn't need to be set, because EvenOdd is the default. -->
        <GeometryGroup>
            <EllipseGeometry RadiusX="50"
                             RadiusY="50"
                             Center="75,75" />
            <EllipseGeometry RadiusX="70"
                             RadiusY="70"
                             Center="75,75" />
            <EllipseGeometry RadiusX="100"
                             RadiusY="100"
                             Center="75,75" />
            <EllipseGeometry RadiusX="120"
                             RadiusY="120"
                             Center="75,75" />
        </GeometryGroup>
    </Path.Data>
</Path>
```

在此示例中，将显示由一系列同心圆环组成的复合形状：

![带有 EvenOdd 填充规则的复合形状](fillrule-images/evenodd.png "带有 EvenOdd 填充规则的复合形状")

在复合形状中，请注意，中心和第三个环不会填满。 这是因为从这两个环内任意一个点绘制的一条射线经过偶数个段：

![用 EvenOdd 填充规则批注的复合形状](fillrule-images/evenodd-annotated.png "用 EvenOdd 填充规则批注的复合形状")

在上面的图像中，红色圆圈表示点，线条表示任意光线。 对于上方点，两个任意光线都通过偶数个直线段。 因此，点不会被填充。 对于下一个点，每个任意射线都通过奇数个直线段。 因此，该点位于的环已填充。

## <a name="nonzero"></a>非零

`Nonzero`填充规则在任意方向上从点到无穷画一条射线，然后检查形状段与射线相交的位置。 从零计数开始，每次段从左到右交叉时，计数就会递增，并且每次从右到左对一段时间都进行递减。 计算交点后，如果结果为零，则点在多边形之外。 否则，在内部。

下面的 XAML 示例创建并呈现复合形状，并 `FillRule` 将设置为 `Nonzero` ：

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Fill="#CCCCFF"
      Aspect="Uniform"
      HorizontalOptions="Start">
    <Path.Data>
        <GeometryGroup FillRule="Nonzero">
            <EllipseGeometry RadiusX="50"
                             RadiusY="50"
                             Center="75,75" />
            <EllipseGeometry RadiusX="70"
                             RadiusY="70"
                             Center="75,75" />
            <EllipseGeometry RadiusX="100"
                             RadiusY="100"
                             Center="75,75" />
            <EllipseGeometry RadiusX="120"
                             RadiusY="120"
                             Center="75,75" />
        </GeometryGroup>
    </Path.Data>
</Path>
```

在此示例中，将显示由一系列同心圆环组成的复合形状：

![带有非零填充规则的复合形状](fillrule-images/nonzero.png "带有非零填充规则的复合形状")

请注意，在复合形状中，所有环都已填充。 这是因为所有段都以相同方向运行，因此，从任何点绘制的射线将跨一个或多个段，并且交叉的总和不等于零：

![带有非零填充规则的带批注复合形状](fillrule-images/nonzero-annotated.png "带有非零填充规则的带批注复合形状")

在上图中，红色箭头表示段绘制方向，黑色箭头表示从最内部环中的点运行的任意射线。 从零值开始，对于该射线相交的每个段，会加值“1”，因为从左到右该段与该射线相交。

需要在不同方向上以不同方向运行的更复杂的形状，才能更好地演示 `Nonzero` 填充规则的行为。 下面的 XAML 示例创建与前面的示例类似的形状，只不过它是使用而不是创建的 `PathGeometry` `EllipseGeometry` ：

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Fill="#CCCCFF">
     <Path.Data>
         <GeometryGroup FillRule="Nonzero">
             <PathGeometry>
                 <PathGeometry.Figures>
                     <!-- Inner ring -->
                     <PathFigure StartPoint="120,120">
                         <PathFigure.Segments>
                             <PathSegmentCollection>
                                 <ArcSegment Size="50,50"
                                             IsLargeArc="True"
                                             SweepDirection="CounterClockwise"
                                             Point="140,120" />
                             </PathSegmentCollection>
                         </PathFigure.Segments>
                     </PathFigure>

                     <!-- Second ring -->
                     <PathFigure StartPoint="120,100">
                         <PathFigure.Segments>
                             <PathSegmentCollection>
                                 <ArcSegment Size="70,70"
                                             IsLargeArc="True"
                                             SweepDirection="CounterClockwise"
                                             Point="140,100" />
                             </PathSegmentCollection>
                         </PathFigure.Segments>
                     </PathFigure>

                     <!-- Third ring  -->
                         <PathFigure StartPoint="120,70">
                         <PathFigure.Segments>
                             <PathSegmentCollection>
                                 <ArcSegment Size="100,100"
                                             IsLargeArc="True"
                                             SweepDirection="CounterClockwise"
                                             Point="140,70" />
                             </PathSegmentCollection>
                         </PathFigure.Segments>
                     </PathFigure>

                     <!-- Outer ring -->
                     <PathFigure StartPoint="120,300">
                         <PathFigure.Segments>
                             <ArcSegment Size="130,130"
                                         IsLargeArc="True"
                                         SweepDirection="Clockwise"
                                         Point="140,300" />
                         </PathFigure.Segments>
                     </PathFigure>
                 </PathGeometry.Figures>
             </PathGeometry>
         </GeometryGroup>
     </Path.Data>
 </Path>
```

在此示例中，绘制了一系列不会关闭的圆弧段：

![带有非零填充规则的复合形状](fillrule-images/nonzero-gaps.png "带有非零填充规则的复合形状")

在上面的图像中，不填充中心的第三个弧。 这是因为给定射线中的值与其路径中的段之间的总和为零：

![带有非零填充规则的带批注复合形状](fillrule-images/nonzero-gaps-annotated.png "带有非零填充规则的带批注复合形状")

在上面的图像中，红色圆圈表示一个点，黑色线条表示从非填充区域中的点移出的任意光线，红色箭头表示段的绘制方向。 可以看出，段与段之间的值的总和为零：

- 对角向右的任意射线跨越在不同方向上运行的两个段。 因此，段会取消零值。
- 对角向左移动的任意射线跨越总共六个段。 但是，交叉点会取消每个其他操作，使零成为最终总和。

如果未填充环，则总和为零。

## <a name="related-links"></a>相关链接

- [ShapeDemos (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms 形状](index.md)