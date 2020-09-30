---
title: Xamarin.Forms 画笔：线性渐变
description: Xamarin.FormsLinearGradientBrush 类使用线性渐变绘制区域。
ms.prod: xamarin
ms.assetid: BEA2B3F5-96B0-4E39-88A6-0FAFE95C3DCD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/28/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: be4b868a2f38063f3a46d57ecb190b377eb89dce
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563583"
---
# <a name="no-locxamarinforms-brushes-linear-gradients"></a>Xamarin.Forms 画笔：线性渐变

![预览 API](~/media/shared/preview.png "此 API 当前为预发布版本")

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-brushdemos/)

`LinearGradientBrush`类派生自 `GradientBrush` 类，并使用线性渐变绘制区域，这种方式将两个或更多的颜色沿称为渐变轴的线混合。 `GradientStop` 对象用于指定渐变中的颜色及其位置。 有关对象的详细信息 `GradientStop` ，请参阅[ Xamarin.Forms 刷子：梯度](gradient.md)。

`LinearGradientBrush` 类定义了以下属性：

- `StartPoint`，类型为 [`Point`](xref:Xamarin.Forms.Point) ，表示线性渐变的二维起始坐标。 此属性的默认值为 (0，0) 。
- `EndPoint`，类型为 [`Point`](xref:Xamarin.Forms.Point) ，表示线性渐变的二维终止坐标。 此属性的默认值为 (1、1) 。

这些属性由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象提供支持；也就是说，它们可以作为数据绑定的目标，并能进行样式设置。

`LinearGradientBrush`类也作为 `IsEmpty` 方法，该方法返回一个 `bool` ，该方法表示是否已为画笔分配了任何 `GradientStop` 对象。

> [!NOTE]
> 还可以通过 CSS 函数创建线性渐变 `linear-gradient()` 。

## <a name="create-a-lineargradientbrush"></a>创建 LinearGradientBrush

线性渐变画笔的梯度停止点沿渐变轴放置。 可以使用画笔的和属性更改渐变轴的方向和大小 `StartPoint` `EndPoint` 。 通过对这些属性进行操作，您可以创建水平、垂直和对角线渐变、反转渐变方向、紧缩渐变传播等。

`StartPoint`和 `EndPoint` 属性相对于所绘制的区域。  (0，0) 表示所绘制区域的左上角， (1，1) 表示所绘制区域的右下角。 下图显示对角线性渐变画笔的渐变轴：

![带有对角渐变轴的帧](lineargradient-images/gradient-axis.png)

在此图中，虚线显示了渐变轴，它突出显示了从起点到终点的渐变的内插路径。

### <a name="create-a-horizontal-linear-gradient"></a>创建水平线性渐变

若要创建水平线性渐变，请创建 `LinearGradientBrush` 对象并将其设置 `StartPoint` 为 (0，0) ，并将其设置 `EndPoint` 为 (1，0) 。 然后，将两个或多个 `GradientStop` 对象添加到 `LinearGradientBrush.GradientStops` 集合中，这些对象指定渐变中的颜色及其位置。

下面的 XAML 示例显示了一个 `LinearGradientBrush` 设置为 `Background` 的的水平 [`Frame`](xref:Xamarin.Forms.Frame) ：

```xaml
<Frame BorderColor="LightGray"
       HasShadow="True"
       CornerRadius="12"
       HeightRequest="120"
       WidthRequest="120">
    <Frame.Background>
        <!-- StartPoint defaults to (0,0) -->
        <LinearGradientBrush EndPoint="1,0">
            <GradientStop Color="Yellow"
                          Offset="0.1" />
            <GradientStop Color="Green"
                          Offset="1.0" />
        </LinearGradientBrush>
    </Frame.Background>
</Frame>  
```

在此示例中，的背景 [`Frame`](xref:Xamarin.Forms.Frame) 使用 `LinearGradientBrush` 从黄色插到绿色的水平进行绘制：

![使用水平 LinearGradientBrush 绘制的帧](lineargradient-images/horizontal.png)

### <a name="create-a-vertical-linear-gradient"></a>创建垂直线性渐变

若要创建垂直线性渐变，请创建 `LinearGradientBrush` 对象并将其设置 `StartPoint` 为 (0，0) ，并将其设置 `EndPoint` 为 (0，1) 。 然后，将两个或多个 `GradientStop` 对象添加到 `LinearGradientBrush.GradientStops` 集合中，这些对象指定渐变中的颜色及其位置。

下面的 XAML 示例显示了一个垂直 `LinearGradientBrush` 的，它设置为 `Background` 的 [`Frame`](xref:Xamarin.Forms.Frame) ：

```xaml
<Frame BorderColor="LightGray"
       HasShadow="True"
       CornerRadius="12"
       HeightRequest="120"
       WidthRequest="120">
    <Frame.Background>
        <!-- StartPoint defaults to (0,0) -->    
        <LinearGradientBrush EndPoint="0,1">
            <GradientStop Color="Yellow"
                          Offset="0.1" />
            <GradientStop Color="Green"
                          Offset="1.0" />
        </LinearGradientBrush>
    </Frame.Background>
</Frame>
```

在此示例中，的背景 [`Frame`](xref:Xamarin.Forms.Frame) 是使用 `LinearGradientBrush` 从黄色插到绿色的垂直进行绘制的：

![使用垂直 LinearGradientBrush 绘制的帧](lineargradient-images/vertical.png)

### <a name="create-a-diagonal-linear-gradient"></a>创建对角线性渐变

若要创建对角线性渐变，请创建 `LinearGradientBrush` 对象并将其设置 `StartPoint` 为 (0，0) ，并将其设置 `EndPoint` 为 (1，1) 。 然后，将两个或多个 `GradientStop` 对象添加到 `LinearGradientBrush.GradientStops` 集合中，这些对象指定渐变中的颜色及其位置。

下面的 XAML 示例显示了一个 `LinearGradientBrush` 设置为 `Background` 的的对角线 [`Frame`](xref:Xamarin.Forms.Frame) ：

```xaml
<Frame BorderColor="LightGray"
       HasShadow="True"
       CornerRadius="12"
       HeightRequest="120"
       WidthRequest="120">
    <Frame.Background>
        <!-- StartPoint defaults to (0,0)      
             Endpoint defaults to (1,1) -->
        <LinearGradientBrush>
            <GradientStop Color="Yellow"
                          Offset="0.1" />
            <GradientStop Color="Green"
                          Offset="1.0" />
        </LinearGradientBrush>
    </Frame.Background>
</Frame>
```

在此示例中，的背景 [`Frame`](xref:Xamarin.Forms.Frame) 使用 `LinearGradientBrush` 从黄色插到绿色的对角进行绘制：

![使用对角 LinearGradientBrush 绘制的帧](lineargradient-images/diagonal.png)

## <a name="related-links"></a>相关链接

- [BrushesDemos (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-brushdemos/)
- [Xamarin.Forms 画笔：渐变](gradient.md)