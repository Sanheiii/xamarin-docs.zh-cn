---
title: Xamarin.Forms 画笔：径向渐变
description: Xamarin.FormsRadialGradientBrush 类使用径向渐变绘制区域。
ms.prod: xamarin
ms.assetid: 099BA530-3B38-4005-9B19-A0EB4D4DEEFC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/28/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a56d2f590b78bef0f47c764862b891c9c0d46129
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563570"
---
# <a name="no-locxamarinforms-brushes-radial-gradients"></a>Xamarin.Forms 画笔：径向渐变

![预览 API](~/media/shared/preview.png "此 API 当前为预发布版本")

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-brushdemos/)

`RadialGradientBrush`类派生自 `GradientBrush` 类，并使用径向渐变绘制区域，这会在一个圆之间混合两种或多种颜色。 `GradientStop` 对象用于指定渐变中的颜色及其位置。 有关对象的详细信息 `GradientStop` ，请参阅[ Xamarin.Forms 刷子：梯度](gradient.md)。

`RadialGradientBrush` 类定义了以下属性：

- `Center`，类型为 [`Point`](xref:Xamarin.Forms.Point) ，表示径向渐变的圆的中心点。 此属性的默认值为0.5，0.5)  (。
- `Radius`，类型为 `double` ，表示径向渐变的圆的半径。 此属性的默认值为0.5。

这些属性由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象提供支持；也就是说，它们可以作为数据绑定的目标，并能进行样式设置。

`RadialGradientBrush`类还具有一个 `IsEmpty` 方法，该方法返回一个 `bool` ，该方法表示是否已为画笔分配了任何 `GradientStop` 对象。

> [!NOTE]
> 还可以通过 CSS 函数创建径向渐变 `radial-gradient()` 。

## <a name="create-a-radialgradientbrush"></a>创建 RadialGradientBrush

径向渐变画笔的梯度停止点沿着由圆圈定义的渐变轴放置。 渐变轴 radiates 从圆的中心到圆周。 可以使用画笔的和属性更改圆的位置和大小 `Center` `Radius` 。 圆圈定义渐变的终点。 因此，在1.0 的渐变停止点定义圆形圆周处的颜色。 位于0.0 的渐变停止点定义圆的中心颜色。

若要创建径向渐变，请创建 `RadialGradientBrush` 对象并设置其 `Center` 和 `Radius` 属性。 然后，将两个或多个 `GradientStop` 对象添加到 `RadialGradientBrush.GradientStops` 集合中，这些对象指定渐变中的颜色及其位置。

下面的 XAML 示例显示了一个 `RadialGradientBrush` 设置为 `Background` 的的 [`Frame`](xref:Xamarin.Forms.Frame) ：

```xaml    
<Frame BorderColor="LightGray"
       HasShadow="True"
       CornerRadius="12"
       HeightRequest="120"
       WidthRequest="120">
    <Frame.Background>
        <!-- Center defaults to (0.5,0.5)
             Radius defaults to (0.5) -->
        <RadialGradientBrush>
            <GradientStop Color="Red"
                          Offset="0.1" />
            <GradientStop Color="DarkBlue"
                          Offset="1.0" />
        </RadialGradientBrush>
    </Frame.Background>
</Frame>
```

在此示例中，的背景 [`Frame`](xref:Xamarin.Forms.Frame) 使用 `RadialGradientBrush` 从红色插到深蓝的进行绘制。 径向渐变的中心位于 `Frame` 以下位置：

![使用居中 RadialGradientBrush 绘制的帧](radialgradient-images/center.png)

下面的 XAML 示例将放射状渐变的中心移动到的左上角 [`Frame`](xref:Xamarin.Forms.Frame) ：

```xaml
<!-- Radius defaults to (0.5) -->
<RadialGradientBrush Center="0.0,0.0">
    <GradientStop Color="Red"
                  Offset="0.1" />
    <GradientStop Color="DarkBlue"
                  Offset="1.0" />
</RadialGradientBrush>
```

在此示例中，的背景 [`Frame`](xref:Xamarin.Forms.Frame) 使用 `RadialGradientBrush` 从红色插到深蓝的进行绘制。 径向渐变的中心位于以下位置的左上角 `Frame` ：

![使用左上角的 RadialGradientBrush 绘制的帧](radialgradient-images/top-left.png)

下面的 XAML 示例将放射状渐变的中心移动到右下角 [`Frame`](xref:Xamarin.Forms.Frame) ：

```xaml
<!-- Radius defaults to (0.5) -->
<RadialGradientBrush Center="1.0,1.0">
    <GradientStop Color="Red"
                  Offset="0.1" />
    <GradientStop Color="DarkBlue"
                  Offset="1.0" />
</RadialGradientBrush>            
```

在此示例中，的背景 [`Frame`](xref:Xamarin.Forms.Frame) 使用 `RadialGradientBrush` 从红色插到深蓝的进行绘制。 径向渐变的中心位于以下位置的底部 `Frame` ：

![用右下 RadialGradientBrush 绘制的帧](radialgradient-images/bottom-right.png)

## <a name="related-links"></a>相关链接

- [BrushesDemos (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-brushdemos/)
- [Xamarin.Forms 画笔：渐变](gradient.md)