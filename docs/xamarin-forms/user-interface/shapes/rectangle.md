---
title: Xamarin.Forms形状：矩形
description: Xamarin.FormsRectangle 类可用于绘制矩形。
ms.prod: xamarin
ms.assetid: 2DD663D3-DAEC-495C-AB6D-8A143FC97637
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/20/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 91960c08640a412e02298ed051469a86c1a9f368
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2020
ms.locfileid: "86930567"
---
# <a name="xamarinforms-shapes-rectangle"></a>Xamarin.Forms形状：矩形

![预发布 API](~/media/shared/preview.png "此 API 当前为预发布版本")

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

`Rectangle`类派生自 `Shape` 类，可用于绘制矩形和正方形。 有关 `Rectangle` 该类继承自类的属性的信息 `Shape` ，请参阅[ Xamarin.Forms 形状](index.md)。

`Rectangle` 定义以下属性:

- `RadiusX`，类型为 `double` ，它是用于圆角化矩形角的 x 轴半径。 此属性的默认值为0.0。
- `RadiusY`，类型为 `double` ，它是用于圆角化矩形角的 y 轴半径。 此属性的默认值为0.0。

这些属性是由对象支持的 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，这意味着它们可以是数据绑定的目标和样式。

`Rectangle`类将 `Aspect` 从类继承的属性设置 `Shape` 为 `Stretch.Fill` 。 有关属性的详细信息 `Aspect` ，请参阅[拉伸形状](index.md#stretch-shapes)。

## <a name="create-a-rectangle"></a>创建矩形

若要绘制矩形，请创建 `Rectangle` 对象并设置其 `WidthRequest` 和 `HeightRequest` 属性。 若要绘制矩形内部，请将其 `Fill` 属性设置为 [`Color`](xref:Xamarin.Forms.Color) 。 若要为矩形指定轮廓，请将其 `Stroke` 属性设置为 [`Color`](xref:Xamarin.Forms.Color) 。 `StrokeThickness`属性指定矩形边框的粗细。

若要为矩形指定圆角，请设置其 `RadiusX` 和 `RadiusY` 属性。 这些属性设置用于圆角化矩形角的 x 轴半径和 y 轴半径。

若要绘制正方形，请使 `WidthRequest` 对象的和 `HeightRequest` 属性 `Rectangle` 相等。

下面的 XAML 示例演示如何绘制实心矩形：

```xaml
<Rectangle Fill="Red"
           WidthRequest="150"
           HeightRequest="50"
           HorizontalOptions="Start" />
```

在此示例中，绘制了一个具有维度150x50 （与设备无关的单位）的红色实心矩形：

![实心矩形](rectangle-images/filled.png "实心矩形")

下面的 XAML 示例演示如何绘制带有圆角的实心矩形：

```xaml
<Rectangle Fill="Blue"
           Stroke="Black"
           StrokeThickness="3"
           RadiusX="50"
           RadiusY="10"
           WidthRequest="200"
           HeightRequest="100"
           HorizontalOptions="Start" />
```

在此示例中，绘制了带有圆角的蓝色实心矩形：

![带圆角的矩形](rectangle-images/rounded.png "带圆角的矩形")

有关绘制虚线矩形的信息，请参阅[绘制虚线形状](index.md#draw-dashed-shapes)。

## <a name="related-links"></a>相关链接

- [ShapeDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms形状](index.md)