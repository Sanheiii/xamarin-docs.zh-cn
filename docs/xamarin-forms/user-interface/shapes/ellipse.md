---
title: Xamarin.Forms 形状：椭圆形
description: Xamarin.Forms可以使用椭圆形类来绘制椭圆和圆圈。
ms.prod: xamarin
ms.assetid: 5BF81E25-12E5-49F0-A40C-0CF4C5D63B9B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/20/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d0379f37a48a4cd11de06868a7616882a598e6a6
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91559241"
---
# <a name="no-locxamarinforms-shapes-ellipse"></a>Xamarin.Forms 形状：椭圆形

![预发行版 API](~/media/shared/preview.png)

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

`Ellipse`类派生自 `Shape` 类，可用于绘制椭圆和圆。 有关 `Ellipse` 该类继承自类的属性的信息 `Shape` ，请参阅[ Xamarin.Forms 形状](index.md)。

`Ellipse`类将 `Aspect` 从类继承的属性设置 `Shape` 为 `Stretch.Fill` 。 有关属性的详细信息 `Aspect` ，请参阅 [拉伸形状](index.md#stretch-shapes)。

## <a name="create-an-ellipse"></a>创建一个椭圆形

若要绘制一个椭圆，请创建一个 `Ellipse` 对象并设置其 `WidthRequest` 和 `HeightRequest` 属性。 若要绘制椭圆内部，请将其 `Fill` 属性设置为 [`Color`](xref:Xamarin.Forms.Color) 。 若要为椭圆指定轮廓，请将其 `Stroke` 属性设置为 [`Color`](xref:Xamarin.Forms.Color) 。 `StrokeThickness`属性指定椭圆轮廓的粗细。

若要绘制圆形，请使 `WidthRequest` 对象的和 `HeightRequest` 属性 `Ellipse` 相等。

下面的 XAML 示例演示如何绘制实心椭圆：

```xaml
<Ellipse Fill="Red"
         WidthRequest="150"
         HeightRequest="50"
         HorizontalOptions="Start" />
```

在此示例中，绘制了维度为 150x50 (与设备无关的单位) 的红色实心椭圆：

![实心椭圆](ellipse-images/filled.png "实心椭圆")

下面的 XAML 示例演示如何绘制圆形：

```xaml
<Ellipse Stroke="Red"
         StrokeThickness="4"
         WidthRequest="150"
         HeightRequest="150"
         HorizontalOptions="Start" />
```

在此示例中，将绘制一个带维度150x150 的红色圆圈，)  (与设备无关的单位：

![圆形](ellipse-images/circle.png "圆形")

有关绘制虚线椭圆的信息，请参阅 [绘制虚线形状](index.md#draw-dashed-shapes)。

## <a name="related-links"></a>相关链接

- [ShapeDemos (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms 形状](index.md)