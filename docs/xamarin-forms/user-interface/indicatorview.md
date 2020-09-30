---
title: Xamarin.Forms IndicatorView
description: IndicatorView 是一个控件，用于显示表示 CarouselView 中的项数和当前位置的指示器。
ms.prod: xamarin
ms.assetId: BBCC223B-4B02-46B7-80BB-EE0E86A67CE2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/27/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 95c4e247ca0be6e5f0f39a7bc95c41a73b3f590e
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556784"
---
# <a name="no-locxamarinforms-indicatorview"></a>Xamarin.Forms IndicatorView

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-indicatorviewdemos/)

`IndicatorView`是一个控件，该控件在中显示表示项数以及当前位置的指示器 `CarouselView` 。

[![IOS 和 Android 上的 CarouselView 和 IndicatorView 屏幕截图](indicatorview-images/circles.png "IndicatorView 圆圈")](indicatorview-images/circles-large.png#lightbox "IndicatorView 圆圈")

`IndicatorView` 定义以下属性:

- `Count`，类型为 `int` ，指示器的数目。
- `HideSingle`类型为的， `bool` 指示指示器是否应仅在存在时隐藏。 默认值为 `true`。
- `IndicatorColor`，类型为 `Color` ，指示器的颜色。
- `IndicatorSize`，类型为 `double` ，指示器的大小。 默认值为6.0。
- `IndicatorLayout`类型为的， `Layout<View>` 用于定义用于呈现的布局类 `IndicatorView` 。 此属性由设置 Xamarin.Forms ，并且通常不需要由开发人员设置。
- `IndicatorTemplate`类型为的 `DataTemplate` 模板，用于定义每个指示器的外观。
- `IndicatorsShape`，类型为 `IndicatorShape` ，每个指示器的形状。
- `ItemsSource`，类型为 `IEnumerable` ，将为其显示指示器的集合。 设置属性时，将自动设置此属性 `CarouselView.IndicatorView` 。
- `MaximumVisible`，类型为 `int` ，可见指示器的最大数目。 默认值为 `int.MaxValue`。
- `Position`类型为的， `int` 当前选定的指示器索引。 此属性使用 `TwoWay` 绑定。 设置属性时，将自动设置此属性 `CarouselView.IndicatorView` 。
- `SelectedIndicatorColor`，类型为 `Color` ，表示中的当前项的指示器的颜色 `CarouselView` 。

这些属性由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象提供支持；也就是说，它们可以作为数据绑定的目标，并能进行样式设置。

## <a name="create-an-indicatorview"></a>创建 IndicatorView

下面的示例演示如何 `IndicatorView` 在 XAML 中实例化：

```xaml
<StackLayout>
    <CarouselView ItemsSource="{Binding Monkeys}"
                  IndicatorView="indicatorView">
        <CarouselView.ItemTemplate>
            <!-- DataTemplate that defines item appearance -->
        </CarouselView.ItemTemplate>
    </CarouselView>
    <IndicatorView x:Name="indicatorView"
                   IndicatorColor="LightGray"
                   SelectedIndicatorColor="DarkGray"
                   HorizontalOptions="Center" />
</StackLayout>
```

在此示例中，在下 `IndicatorView` 呈现 `CarouselView` ，并且中的每一项都有一个指示符 `CarouselView` 。 `IndicatorView`使用数据填充数据，方法是将 `CarouselView.IndicatorView` 属性设置为 `IndicatorView` 对象。 每个指示器都是浅灰色圆圈，而表示中的当前项的指示器 `CarouselView` 是深灰色。

> [!IMPORTANT]
> 设置 `CarouselView.IndicatorView` 属性将导致 `IndicatorView.Position` 属性绑定到 `CarouselView.Position` 属性，并将 `IndicatorView.ItemsSource` 属性绑定到 `CarouselView.ItemsSource` 属性。

## <a name="change-indicator-shape"></a>更改指示器形状

`IndicatorView`类具有一个 `IndicatorsShape` 属性，该属性确定指示器的形状。 此属性可以设置为 `IndicatorShape` 枚举成员之一：

- `Circle` 指定指示器形状将为圆形。 这是 `IndicatorView.IndicatorsShape` 属性的默认值。
- `Square` 指示指示器形状将为正方形。

下面的示例演示了一个 `IndicatorView` 配置为使用方指示器的：

```xaml
<IndicatorView x:Name="indicatorView"
               IndicatorsShape="Square"
               IndicatorColor="LightGray"
               SelectedIndicatorColor="DarkGray" />
```

## <a name="change-indicator-size"></a>更改指示器大小

`IndicatorView`类具有一个 `IndicatorSize` 类型为的属性，该属性 `double` 确定指示器在与设备无关的单位中的大小。 此属性的默认值为6.0。

下面的示例演示了一个 `IndicatorView` 配置为显示更大指示器的：

```xaml
<IndicatorView x:Name="indicatorView"
               IndicatorSize="18" />
```

## <a name="limit-the-number-of-indicators-displayed"></a>限制显示的指示器数量

`IndicatorView`类具有一个 `MaximumVisible` 类型为的属性 `int` ，该属性确定可见指示器的最大数目。

下面的示例演示了一个 `IndicatorView` 配置为最多显示六个指示器的：

```xaml
<IndicatorView x:Name="indicatorView"
               MaximumVisible="6" />
```

## <a name="define-indicator-appearance"></a>定义指示器外观

可以通过将属性设置为来定义每个指示器的外观 `IndicatorView.IndicatorTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ：

```xaml
<StackLayout>
    <CarouselView ItemsSource="{Binding Monkeys}"
                  IndicatorView="indicatorView">
        <CarouselView.ItemTemplate>
            <!-- DataTemplate that defines item appearance -->
        </CarouselView.ItemTemplate>
    </CarouselView>
    <IndicatorView x:Name="indicatorView"
                   IndicatorColor="LightGray"
                   SelectedIndicatorColor="Black"
                   HorizontalOptions="Center">
        <IndicatorView.IndicatorTemplate>
            <DataTemplate>
                <Image Source="{FontImage &#xf30c;, FontFamily={OnPlatform iOS=Ionicons, Android=ionicons.ttf#}, Size=12}" />
            </DataTemplate>
        </IndicatorView.IndicatorTemplate>
    </IndicatorView>
</StackLayout>
```

在中指定的元素 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 定义每个指示器的外观。 在此示例中，每个指示器都是一个 [`Image`](xref:Xamarin.Forms.Image) ，它使用标记扩展显示字体图标 `FontImage` 。

以下屏幕截图显示了使用字体图标呈现的指示器：

[![IOS 和 Android 上的模板化 IndicatorView 的屏幕截图](indicatorview-images/templated.png "模板化 IndicatorView")](indicatorview-images/templated-large.png#lightbox "模板化 IndicatorView")

有关标记扩展的详细信息 `FontImage` ，请参阅 [FontImage 标记扩展](~/xamarin-forms/xaml/markup-extensions/consuming.md#fontimage-markup-extension)。

## <a name="related-links"></a>相关链接

- [IndicatorView (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-indicatorviewdemos/)
- [FontImage 标记扩展](~/xamarin-forms/xaml/markup-extensions/consuming.md#fontimage-markup-extension)