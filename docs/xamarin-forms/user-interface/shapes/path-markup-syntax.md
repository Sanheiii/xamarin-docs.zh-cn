---
title: Xamarin.Forms 形状：路径标记语法
description: Xamarin.Forms 路径标记语法使你可以简洁地在 XAML 中指定路径几何。
ms.prod: xamarin
ms.assetid: A2C1BD59-1A16-4E26-A825-0338E2AF9E65
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/19/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 743f6366cc7e798247ea0151720a81e9cbddfeeb
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91559099"
---
# <a name="no-locxamarinforms-shapes-path-markup-syntax"></a>Xamarin.Forms 形状：路径标记语法

![预发行版 API](~/media/shared/preview.png)

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

Xamarin.Forms 路径标记语法使你可以简洁地在 XAML 中指定路径几何。 语法指定为属性的字符串值 `Path.Data` ：

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Data="M13.908992,16.207977 L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983Z" />
```

路径标记语法由一个可选 `FillRule` 值和一个或多个图形说明组成。 此语法可表示为： `<Path Data="` *[fillRule]* *figureDescription* *[figureDescription]* * `" ... />`

采用以下语法：

- *fillRule* 是一个可选 `Xamarin.Forms.Shapes.FillRule` 的，它指定几何是否应使用 `EvenOdd` 或 `Nonzero` `FillRule` 。 `F0` 用于指定 `EvenOdd` 填充规则，而 `F1` 用于指定 `Nonzero` 填充规则。 有关填充规则的详细信息，请参阅[ Xamarin.Forms 形状：填充规则](fillrules.md)。
- *figureDescription* 表示由移动命令、绘图命令和可选的 "关闭" 命令构成的数字。 Move 命令指定图形的起点。 绘图命令描述了图形的内容，可选的 "关闭" 命令关闭了图形。

在上面的示例中，路径标记语法指定一个开始点，其中使用 move 命令 (`M`) ，一系列使用 line 命令 () 的直线， `L` 并使用 close 命令 () 关闭路径 `Z` 。

在路径标记语法中，命令前后不需要空格。 此外，两个数字无需用逗号或空格分隔，但这种情况只能在字符串明确时实现。

> [!TIP]
> 路径标记语言使用与 (SVG) 图像路径定义兼容的可伸缩矢量图形兼容的语法，因此它可用于从 SVG 格式移植图形。

## <a name="move-command"></a>移动命令

Move 命令指定新图形的起点。 此命令的语法为： `M` *startPoint* 或 `m` *startPoint*。

在此语法中， *startPoint* 是一个 [`Point`](xref:Xamarin.Forms.Point) 指定新图形的起点的结构。 如果在 move 命令后列出了多个点，则会向这些点绘制一条线。

`M 10,10` 是有效的 move 命令的一个示例。

## <a name="draw-commands"></a>绘制命令

绘制命令可以由几个形状命令组成。 以下绘图命令可用：

- 行 (`L` 或 `l`) 。
- 水平线 (`H` 或 `h`) 。
- 竖线 (`V` 或 `v`) 。
- 椭圆弧 (`A` 或 `a`) 。
- 三次方贝塞尔曲线 (`C` 或 `c`) 。
- 二次贝塞尔曲线 (`Q` 或 `q`) 。
- 平滑三次方贝塞尔曲线 (`S` 或 `s`) 。
- 平滑二次贝塞尔曲线 (`T` 或 `t`) 。

使用不区分大小写的字母指定每个 draw 命令。 当连续输入了同一类型的多个命令时，你可以忽略重复的命令输入。 例如， `L 100,200 300,400` 等效于 `L 100,200 L 300,400` 。

### <a name="line-command"></a>直线命令

Line 命令在当前点和指定的终点之间创建一条直线。 此命令的语法为： `L` *endpoint* 或 `l` *endpoint*。

在此语法中， *终结点* 是一个 [`Point`](xref:Xamarin.Forms.Point) ，表示线条的终点。

`L 20,30` 和 `L 20 30` 是有效行命令的示例。

有关以对象的形式创建直线的信息 `PathGeometry` ，请参阅 [Create a system.windows.media.linesegment>](geometries.md#create-a-linesegment)。

### <a name="horizontal-line-command"></a>横线命令

水平线命令在当前点和指定的 x 坐标之间创建水平线条。 此命令的语法为： `H` *x* 或 `h` *x*。

在此语法中， *x* 是一个 `double` ，它表示直线终点的 x 坐标。

`H 90` 是有效水平线命令的示例。

### <a name="vertical-line-command"></a>竖线命令

竖线命令在当前点和指定的 y 坐标之间创建一条竖线。 此命令的语法是： `V` *y* 或 `v` *y*。

在此语法中， *y* 是 `double` 表示直线终点的 y 坐标的。

`V 90` 是有效竖线命令的示例。

### <a name="elliptical-arc-command"></a>椭圆弧命令

椭圆弧线命令在当前点和指定的终点之间创建一条椭圆弧。 此命令的语法为： `A` *size* *rotationAngle* *isLargeArcFlag* *sweepDirectionFlag* *终结点* 或 `a` *大小* *rotationAngle* *isLargeArcFlag* *sweepDirectionFlag* *终结点*。

采用以下语法：

- `size` 一个 [`Size`](xref:Xamarin.Forms.Size) ，它表示圆弧的 x 轴半径和 y 轴半径。
- `rotationAngle``double`表示椭圆旋转（以度为单位）的。
- `isLargeArcFlag` 如果弧的角度应为180度或更高，则应设置为1，否则将其设置为0。
- `sweepDirectionFlag` 如果以正角方向绘制弧，则应设置为 1; 否则设置为0。
- `endPoint` 是 [`Point`](xref:Xamarin.Forms.Point) 弧线绘制到的。

`A 150,150 0 1,0 150,-150` 是有效的椭圆形弧线命令的一个示例。

有关创建椭圆弧作为对象的信息 `PathGeometry` ，请参阅 [创建 ArcSegment](geometries.md#create-an-arcsegment)。

### <a name="cubic-bezier-curve-command"></a>三次方贝塞尔曲线命令

三次方贝塞尔曲线命令通过使用两个指定控制点在当前点和指定的终点之间创建三次方贝塞尔曲线。 此命令的语法为： `C` *controlPoint1* *controlPoint2* *endpoint* 或 `c` *controlPoint1* *controlPoint2* *终结点*。

采用以下语法：

- *controlPoint1* 是一个 [`Point`](xref:Xamarin.Forms.Point) ，表示曲线的第一个控制点，它确定曲线的起始正切值。
- *controlPoint2* 是一个 [`Point`](xref:Xamarin.Forms.Point) ，它表示曲线的第二个控制点，它确定曲线的结束正切值。
- *终结点* 是一个 [`Point`](xref:Xamarin.Forms.Point) ，表示曲线绘制到的点。

`C 100,200 200,400 300,200` 是有效的三次方贝塞尔曲线命令的示例。

有关创建三次方贝塞尔曲线作为对象的信息 `PathGeometry` ，请参阅 [Create a system.windows.media.beziersegment>](geometries.md#create-a-beziersegment)。

### <a name="quadratic-bezier-curve-command"></a>二次贝塞尔曲线命令

二次贝塞尔曲线命令使用指定的控制点在当前点和指定的终点之间创建二次贝塞尔曲线。 此命令的语法为： `Q` *controlPoint* *终结点* 或 `q` *controlPoint* *终结点*。

采用以下语法：

- *controlPoint* 是 [`Point`](xref:Xamarin.Forms.Point) 表示曲线控制点的，它确定曲线的起始和结束切线。
- *终结点* 是一个 [`Point`](xref:Xamarin.Forms.Point) ，表示曲线绘制到的点。

`Q 100,200 300,200` 为有效二次贝塞尔曲线命令的示例。

有关创建二次贝塞尔曲线作为对象的信息 `PathGeometry` ，请参阅 [创建 system.windows.media.quadraticbeziersegment>](geometries.md#create-a-quadraticbeziersegment)。

### <a name="smooth-cubic-bezier-curve-command"></a>平滑三次贝塞尔曲线命令

平滑三次方贝塞尔曲线命令通过使用指定的控制点，在当前点和指定的终点之间创建三次方贝塞尔曲线。 此命令的语法为： `S` *controlPoint2* *终结点*或 `s` *controlPoint2* *终结点*。  

采用以下语法：

- *controlPoint2* 是一个 [`Point`](xref:Xamarin.Forms.Point) ，它表示曲线的第二个控制点，它确定曲线的结束正切值。
- *终结点* 是一个 [`Point`](xref:Xamarin.Forms.Point) ，表示曲线绘制到的点。

第一个控制点被假定为前一个命令的第二个控制点的反射（相对于当前点）。 如果没有上一个命令，或者前一个命令不是三次贝塞尔曲线命令或平滑三次贝塞尔曲线命令，则假定第一个控制点与当前点重合。

`S 100,200 200,300` 是有效的平滑三次贝塞尔曲线命令的示例。

### <a name="smooth-quadratic-bezier-curve-command"></a>平滑二次贝塞尔曲线命令

平滑二次贝塞尔曲线命令通过使用控制点在当前点和指定的终点之间创建二次贝塞尔曲线。 此命令的语法为： `T` *endpoint* 或 `t` *endpoint*。

在此语法中， *终结点* 是 [`Point`](xref:Xamarin.Forms.Point) 表示曲线绘制点的。

假设控制点为相对于当前点前一命令的控制点的反射。 如果没有上一个命令，或如果上一个命令不是二次贝塞尔曲线或平滑二次贝塞尔曲线命令，则假定控制点与当前点重合。

`T 100,30` 是有效的平滑二次方贝塞尔曲线命令的一个示例。

## <a name="close-command"></a>关闭命令

Close 命令结束当前图形，并创建将当前点连接到图形起点的线条。 因此，此命令将在上一段和该图的第一段之间创建行联接。

Close 命令的语法是： `Z` 或 `z` 。

## <a name="additional-values"></a>其他值

除了标准数值，还可以使用以下区分大小写的特殊值：

- `Infinity` 表示 `double.PositiveInfinity` 。
- `-Infinity`表示 `double.NegativeInfinity` 。
- `NaN` 表示 `double.NaN` 。

此外，还可以使用不区分大小写的科学记数法。 因此， `+1.e17` 是有效的值。

## <a name="related-links"></a>相关链接

- [ShapeDemos (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms 形状：几何图形](geometries.md)
- [Xamarin.Forms 形状：填充规则](fillrules.md)