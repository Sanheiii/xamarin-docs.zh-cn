---
title: Xamarin.Forms 画笔：纯色
description: Xamarin.FormsSystem.windows.media.solidcolorbrush> 类使用纯色绘制区域。
ms.prod: xamarin
ms.assetid: 4225D40A-16C1-40E1-ACBE-23E321E7FDE4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/27/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3c3caf064ca550086f8e7924786ac8bcaf1badfc
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556277"
---
# <a name="no-locxamarinforms-brushes-solid-colors"></a>Xamarin.Forms 画笔：纯色

![预览 API](~/media/shared/preview.png "此 API 当前为预发布版本")

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-brushdemos/)

`SolidColorBrush`类派生自 `Brush` 类，用于绘制带有纯色的区域。 有多种方法可以指定的颜色 `SolidColorBrush` 。 例如，你可以使用值指定其颜色， [`Color`](xref:Xamarin.Forms.Color) 或者使用类提供的预定义对象之一指定其颜色 `SolidColorBrush` `Brush` 。

`SolidColorBrush`类定义类型的 `Color` 属性，该属性 [`Color`](xref:Xamarin.Forms.Color) 表示画笔的颜色。 此属性由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象支持，这意味着它可以是数据绑定的目标和样式。

`SolidColorBrush`类还具有一个 `IsEmpty` 方法，该方法返回一个 `bool` ，该方法表示画笔是否已被分配了颜色。

## <a name="create-a-solidcolorbrush"></a>创建 System.windows.media.solidcolorbrush>

有三种主要方法可用于创建 `SolidColorBrush` 。 你可以从创建 `SolidColorBrush` [`Color`](xref:Xamarin.Forms.Color) ，使用预定义的画笔，或 `SolidColorBrush` 使用十六进制表示法创建。

### <a name="use-a-predefined-color"></a>使用预定义的颜色

Xamarin.Forms 包括从值创建的类型转换器 `SolidColorBrush` [`Color`](xref:Xamarin.Forms.Color) 。 在 XAML 中，这使得可以 `SolidColorBrush` 从预定义的 `Color` 值创建：

```xaml
<Frame Background="DarkBlue"
       BorderColor="LightGray"
       HasShadow="True"
       CornerRadius="12"
       HeightRequest="120"
       WidthRequest="120" />
```

在此示例中，的背景 [`Frame`](xref:Xamarin.Forms.Frame) 以深蓝色绘制 `SolidColorBrush` ：

![用预定义颜色绘制的帧](solidcolor-images/predefined-color.png)

或者， [`Color`](xref:Xamarin.Forms.Color) 可以使用属性标记语法指定值：

```xaml
<Frame BorderColor="LightGray"
       HasShadow="True"
       CornerRadius="12"
       HeightRequest="120"
       WidthRequest="120">
       <Frame.Background>
           <SolidColorBrush Color="DarkBlue" />
       </Frame.Background>
</Frame>
```

在此示例中，的背景 [`Frame`](xref:Xamarin.Forms.Frame) 使用 `SolidColorBrush` 通过设置属性指定的颜色进行绘制 `SolidColorBrush.Color` 。

### <a name="use-a-predefined-brush"></a>使用预定义的画笔

`Brush`类定义一组常用的 `SolidColorBrush` 对象。 下面的示例使用以下预定义 `SolidColorBrush` 对象之一：

```xaml
<Frame Background="{x:Static Brush.Indigo}"
       BorderColor="LightGray"
       HasShadow="True"
       CornerRadius="12"
       HeightRequest="120"
       WidthRequest="120" />       
```

等效 C# 代码如下：

```csharp
Frame frame = new Frame
{
    Background = Brush.Indigo,
    BorderColor = Color.LightGray,
    // ...
};
```

在此示例中，的背景 [`Frame`](xref:Xamarin.Forms.Frame) 使用靛蓝色绘制 `SolidColorBrush` ：

![用预定义的 System.windows.media.solidcolorbrush> 绘制的帧](solidcolor-images/predefined-brush.png)

有关类提供的预定义 `SolidColorBrush` 对象的列表 `Brush` ，请参阅 [纯色画笔](#solid-color-brushes)。

### <a name="use-hexadecimal-notation"></a>使用十六进制表示法

`SolidColorBrush` 还可以使用十六进制表示法来创建对象。 使用此方法时，将使用红色、绿色和蓝色的量来指定颜色，以将其合并为一种颜色。 使用十六进制表示法指定颜色的主要格式为 `#rrggbb` ，其中：

- `rr` 指定红色相对量的两位十六进制数。
- `gg` 指定绿色相对量的两位十六进制数。
- `bb` 指定蓝色的相对量的两位十六进制数。

此外，还可以指定颜色， `#aarrggbb` 其中 `aa` 指定颜色的 alpha 值或透明度。 使用此方法能够创建部分透明的颜色。

下面的示例 `SolidColorBrush` 使用十六进制表示法设置的颜色值：

```xaml
<Frame Background="#FF9988"
       BorderColor="LightGray"
       HasShadow="True"
       CornerRadius="12"
       HeightRequest="120"
       WidthRequest="120" />
```

在此示例中，的背景 [`Frame`](xref:Xamarin.Forms.Frame) 使用橙红着色 `SolidColorBrush` ：

![使用十六进制表示法创建的 System.windows.media.solidcolorbrush> 所绘制的帧](solidcolor-images/hex.png)

有关描述颜色的其他方法，请参阅[中 Xamarin.Forms 的颜色](~/xamarin-forms/user-interface/colors.md)。

## <a name="solid-color-brushes"></a>纯色画笔

为方便起见， `Brush` 该类提供了一组常用 `SolidColorBrush` 对象，例如 `AliceBlue` 和 `YellowGreen` 。 下图显示了每个预定义画笔的颜色、其名称和十六进制值：

[![颜色表，包括颜色样本、颜色名称和十六进制值](solidcolor-images/solidcolorbrushes.png)](solidcolor-images/solidcolorbrushes-large.png#lightbox)

## <a name="related-links"></a>相关链接

- [BrushesDemos (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-brushdemos/)
- [颜色 Xamarin.Forms](~/xamarin-forms/user-interface/colors.md)