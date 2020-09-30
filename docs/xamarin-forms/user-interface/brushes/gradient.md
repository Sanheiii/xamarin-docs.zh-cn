---
title: Xamarin.Forms 画笔：渐变
description: Xamarin.FormsGradientBrush 类是一个抽象类，描述由渐变停止点组成的渐变。
ms.prod: xamarin
ms.assetid: 24763E56-74EC-4082-897B-E4EAACCADFEE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/27/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 08a423830ee3db55cb0ec7facfa5630c8832885b
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562933"
---
# <a name="no-locxamarinforms-brushes-gradients"></a>Xamarin.Forms 画笔：渐变

![预览 API](~/media/shared/preview.png "此 API 当前为预发布版本")

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-brushdemos/)

`GradientBrush`类派生自 `Brush` 类，是描述渐变（由梯度停止点）的抽象类。 渐变画笔使用沿轴相互混合的多种颜色绘制区域。 派生自的类 `GradientBrush` 描述了解释渐变停止点的不同方式，并 Xamarin.Forms 提供了以下渐变画笔：

- `LinearGradientBrush`，它使用线性渐变绘制区域。 有关详细信息，请参阅[ Xamarin.Forms 画笔：线性渐变](lineargradient.md)。
- `RadialGradientBrush`使用径向渐变绘制区域。 有关详细信息，请参阅[ Xamarin.Forms 画笔：放射梯度](radialgradient.md)。

`GradientBrush`类定义 `GradientStops` 类型的属性，该属性 `GradientStopsCollection` 表示画笔的梯度停止点，其中每个色标都指定一个颜色和一个沿画笔渐变轴的偏移量。 `GradientStopsCollection`是 `ObservableCollection` `GradientStop` 对象的。 `GradientStops`对象支持属性 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，这意味着它可以是数据绑定的目标和样式。

> [!NOTE]
> `GradientStops`属性是类的 `ContentProperty` `GradientBrush` ，因此不需要从 XAML 显式设置。

## <a name="gradient-stops"></a>渐变停点

梯度停止点是渐变画笔的构建基块，它指定渐变中的颜色及其在渐变轴上的位置。 使用对象指定梯度停止点 `GradientStop` 。

`GradientStop` 类定义了以下属性：

- `Color`，类型为 [`Color`](xref:Xamarin.Forms.Color) ，表示梯度停止点的颜色。 此属性的默认值为 `Color.Default`。
- `Offset`，类型为 `float` ，表示渐变向量中渐变停止点的位置。 此属性的默认值为0，有效值介于 0.0-1.0 之间。 此值越接近0，颜色就越接近渐变的起点。 同样，此值越接近1，颜色就越接近渐变的结尾。

这些属性由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象提供支持；也就是说，它们可以作为数据绑定的目标，并能进行样式设置。

> [!IMPORTANT]
> 渐变所使用的坐标系统相对于输出区域的边界框。 0 表示边界框为 0%，1 表示边界框为 100%。 因此， (0.5，0.5) 描述边界框中间的一个点， (1，1) 描述边界框右下方的一个点。

下面的 XAML 示例 `LinearGradientBrush` 使用四种颜色创建对角线：

```xaml
<LinearGradientBrush StartPoint="0,0"
                     EndPoint="1,1">
    <GradientStop Color="Yellow"
                  Offset="0.0" />
    <GradientStop Color="Red"
                  Offset="0.25" />
    <GradientStop Color="Blue"
                  Offset="0.75" />             
    <GradientStop Color="LimeGreen"
                  Offset="1.0" />
</LinearGradientBrush>                                                       
```

梯度停止点之间的每个点的颜色都是由两个边界梯度停止点指定的颜色组合而成。 下图显示了上一示例中的梯度停止点：

![使用对角 LinearGradientBrush 绘制的帧](gradient-images/gradient-stops.png)

在此关系图中，圆圈标记梯度停止点的位置，虚线显示渐变轴。 第一个梯度停止点指定偏移量为0.0 的黄色。 第二个梯度停止点指定偏移量为0.25 的红色。 在沿渐变轴向右移动时，这两个梯度停止点之间的点逐渐从黄色更改为红色。 第三个梯度停止点指定偏移量为0.75 的蓝色。 第二个和第三个梯度停止点之间的点渐渐由红色变为蓝色。 第四个梯度停止点在偏移量为1.0 时指定彩色橙色绿色。 第三个和第四个梯度停止点之间的点渐渐由蓝色变为浅绿色。

## <a name="related-links"></a>相关链接

- [BrushesDemos (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-brushdemos/)
- [Xamarin.Forms 画笔：线性渐变](lineargradient.md)
- [Xamarin.Forms 画笔：径向渐变](radialgradient.md)