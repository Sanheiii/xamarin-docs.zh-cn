---
title: Xamarin.Forms 编译的绑定
description: 本文介绍如何使用已编译的绑定来提升 Xamarin.Forms 应用程序中的数据绑定性能。
ms.prod: xamarin
ms.assetid: ABE6B7F7-875E-4402-A1D2-845CE374402B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/18/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1bc2ed53b12e6953033760b072d38353c65a6708
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563453"
---
# <a name="no-locxamarinforms-compiled-bindings"></a>Xamarin.Forms 编译的绑定

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

已编译的绑定的解析速度快于传统绑定解析，因而可提升 Xamarin.Forms 应用程序中的数据绑定的性能。

数据绑定存在两个主要问题：

1. 绑定表达式不具有编译时验证。 相反，绑定在运行时解析。 因此，在应用程序未按预期运行或出现错误消息时，直到运行时才会检测到任何无效绑定。
1. 它们不具有成本效益。 使用常规对象检查（反射）在运行时解析绑定，并且执行此操作的开销因平台而异。

已编译的绑定通过在编译时而不是运行时解析绑定表达式来提升 Xamarin.Forms 应用程序中的数据绑定性能。 此外，绑定表达式的这种编译时验证可以提供更好的开发人员故障排除体验，因为无效绑定会被报告为生成错误。

使用已编译的绑定的过程：

1. 启用 XAML 编译。 有关 XAML 编译的详细信息，请参阅 [XAML 编译](~/xamarin-forms/xaml/xamlc.md)。
1. 将 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 上的 `x:DataType` 属性设置为 `VisualElement` 及其子元素将绑定到的对象类型。

> [!NOTE]
> 建议在设置 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 的视图层次结构中将 `x:DataType` 属性设置为相同级别。 但可以在视图层次结构中的任意位置重新定义此属性。

若要使用已编译的绑定，必须将 `x:DataType` 属性设置为字符串文字或使用 `x:Type` 标记扩展的类型。 在 XAML 编译时，会将任何无效绑定表达式报告为生成错误。 但是，XAML 编译器仅报告遇到的第一个无效绑定表达式的生成错误。 无论是在 XAML 还是代码中设置 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)，都将编译在 `VisualElement` 或其子元素上定义的任何有效绑定表达式。 编译绑定表达式会生成编译代码，该代码将从源上的属性获取值，并将在标记中指定的目标的属性上对其进行设置 。 此外，根据绑定表达式，生成的代码可能会观察到源属性值的更改并刷新目标属性，并可能会将更改从目标推送回源   。

> [!IMPORTANT]
> 目前，定义 [`Source`](xref:Xamarin.Forms.Binding.Source) 属性的任何绑定表达式都禁用了编译绑定。 这是因为 `Source` 属性始终使用 `x:Reference` 标记扩展进行设置，该设置在编译时无法解析。

## <a name="use-compiled-bindings"></a>使用已编译的绑定

“已编译的颜色选择器”页演示如何在 Xamarin.Forms 视图和 viewmodel 属性之间使用已编译的绑定：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.CompiledColorSelectorPage"
             Title="Compiled Color Selector">
    ...
    <StackLayout x:DataType="local:HslColorViewModel">
        <StackLayout.BindingContext>
            <local:HslColorViewModel Color="Sienna" />
        </StackLayout.BindingContext>
        <BoxView Color="{Binding Color}"
                 ... />
        <StackLayout Margin="10, 0">
            <Label Text="{Binding Name}" />
            <Slider Value="{Binding Hue}" />
            <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}" />
            <Slider Value="{Binding Saturation}" />
            <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}" />
            <Slider Value="{Binding Luminosity}" />
            <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}" />
        </StackLayout>
    </StackLayout>    
</ContentPage>
```

根 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 实例化 `HslColorViewModel` 并初始化 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 属性的属性元素标记内的 `Color` 属性。 此根 `StackLayout` 还将 `x:DataType` 属性定义为 viewmodel 类型，指示将编译根 `StackLayout` 视图层次结构中的任何绑定表达式。 通过更改任何绑定表达式以绑定到不存在的 viewmodel 属性，可对此进行验证，这将导致生成错误。 尽管此示例将 `x:DataType` 特性设置为字符串文字，但也可以将其设置为具有 `x:Type` 标记扩展的类型。 有关 `x:Type` 标记扩展的详细信息，请参阅 [x:Type 标记扩展](~/xamarin-forms/xaml/markup-extensions/consuming.md#xtype-markup-extension)。

> [!IMPORTANT]
> 可以在视图层次结构中的任意点重新定义 `x:DataType` 属性。

[`BoxView`](xref:Xamarin.Forms.BoxView)、[`Label`](xref:Xamarin.Forms.Label) 元素和 [`Slider`](xref:Xamarin.Forms.Slider) 视图从 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 继承绑定上下文。 这些视图都是引用 viewmodel 中的源属性的绑定目标。 对于 [`BoxView.Color`](xref:Xamarin.Forms.BoxView.Color) 属性和 [`Label.Text`](xref:Xamarin.Forms.Label.Text) 属性，数据绑定为 `OneWay` - 视图中的属性根据 viewmodel 中的属性进行设置。 但是，[`Slider.Value`](xref:Xamarin.Forms.Slider.Value) 属性使用 `TwoWay` 绑定。 此模式允许从 viewmodel 设置每个 `Slider`，也允许从每个 `Slider` 设置 viewmodel。

首次运行应用程序时，[`BoxView`](xref:Xamarin.Forms.BoxView)、[`Label`](xref:Xamarin.Forms.Label) 元素和 [`Slider`](xref:Xamarin.Forms.Slider) 元素均根据实例化 viewmodel 时设置的初始 `Color` 属性集从 viewmodel 中进行设置。 以下屏幕截图演示了此过程：

[![已编译的颜色选择器](compiled-bindings-images/compiledcolorselector-small.png "已编译的颜色选择器")](compiled-bindings-images/compiledcolorselector-large.png#lightbox "已编译的颜色选择器")

随着滑块的操作，[`BoxView`](xref:Xamarin.Forms.BoxView) 和 [`Label`](xref:Xamarin.Forms.Label) 元素会进行相应更新。

有关此颜色选择器的详细信息，请参阅 [Viewmodel 和属性更改通知](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md#viewmodels-and-property-change-notifications)。

## <a name="use-compiled-bindings-in-a-datatemplate"></a>在 DataTemplate 中使用已编译的绑定

[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 中的绑定在模板化的对象的上下文中进行解释。 因此，在 `DataTemplate` 中使用已编译的绑定时，`DataTemplate` 需要使用 `x:DataType` 属性来声明其数据对象的类型。

已编译的颜色列表页演示在 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 中使用已编译的绑定：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.CompiledColorListPage"
             Title="Compiled Color List">
    <Grid>
        ...
        <ListView x:Name="colorListView"
                  ItemsSource="{x:Static local:NamedColor.All}"
                  ... >
            <ListView.ItemTemplate>
                <DataTemplate x:DataType="local:NamedColor">
                    <ViewCell>
                        <StackLayout Orientation="Horizontal">
                            <BoxView Color="{Binding Color}"
                                     ... />
                            <Label Text="{Binding FriendlyName}"
                                   ... />
                        </StackLayout>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
        <!-- The BoxView doesn't use compiled bindings -->
        <BoxView Color="{Binding Source={x:Reference colorListView}, Path=SelectedItem.Color}"
                 ... />
    </Grid>
</ContentPage>
```

将 [`ListView.ItemsSource`](xref:Xamarin.Forms.ListView) 属性设置为静态 `NamedColor.All` 属性。 `NamedColor` 类使用 .NET 反射来枚举 [`Color`](xref:Xamarin.Forms.Color) 结构中的所有静态公共字段，并将它们以及它们的名称存储在可从静态 `All` 属性访问的集合中。 因此，`ListView` 由所有 `NamedColor` 实例填充。 对于 `ListView` 中的每个项，项的绑定上下文都被设置为 `NamedColor` 对象。 [`ViewCell`](xref:Xamarin.Forms.ViewCell) 中的 [`BoxView`](xref:Xamarin.Forms.BoxView) 和 [`Label`](xref:Xamarin.Forms.Label) 元素被绑定到 `NamedColor` 属性。

请注意，[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 将 `x:DataType` 属性定义为 `NamedColor` 类型，表示将编译 `DataTemplate` 视图结构中的任意绑定表达式。 通过更改任何绑定表达式来绑定到不存在的 `NamedColor` 属性，可对此进行验证，这将导致生成错误。  尽管此示例将 `x:DataType` 特性设置为字符串文字，但也可以将其设置为具有 `x:Type` 标记扩展的类型。 有关 `x:Type` 标记扩展的详细信息，请参阅 [x:Type 标记扩展](~/xamarin-forms/xaml/markup-extensions/consuming.md#xtype-markup-extension)。

首次运行应用程序时，[`ListView`](xref:Xamarin.Forms.ListView) 由 `NamedColor` 实例填充。 选中 `ListView` 中的项时，[`BoxView.Color`](xref:Xamarin.Forms.BoxView.Color) 属性被设置为 `ListView` 中所选项的颜色：

[![已编译的颜色列表](compiled-bindings-images/compiledcolorlist-small.png "已编译的颜色列表]")](compiled-bindings-images/compiledcolorlist-large.png#lightbox "已编译的颜色列表")

选择 [`ListView`](xref:Xamarin.Forms.BoxView) 中的其他项会更新 [`BoxView`](xref:Xamarin.Forms.BoxView) 的颜色。

## <a name="combine-compiled-bindings-with-classic-bindings"></a>将已编译的绑定与传统绑定相结合

绑定表达式仅针对定义了 `x:DataType` 属性的视图层次结构进行编译。 相反，未定义 `x:DataType` 属性的层次结构中的任何视图都将使用传统绑定。 因此，可以在页面上组合已编译的绑定和传统绑定。 例如，在之前的部分中，[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 中的视图使用已编译的绑定，而设置为 [`ListView`](xref:Xamarin.Forms.ListView) 中所选颜色的 [`BoxView`](xref:Xamarin.Forms.BoxView) 则不使用。

因此仔细构造 `x:DataType` 属性可以生成使用已编译绑定和传统绑定的页面。 或者，可以使用 `x:Null` 标记扩展在视图层次结构中的任意点将 `x:DataType` 属性重新定义为 `null`。 执行此操作表示视图层次结构中的任何绑定表达式都将使用传统绑定。 混合绑定页展示这种方法：

```xaml
<StackLayout x:DataType="local:HslColorViewModel">
    <StackLayout.BindingContext>
        <local:HslColorViewModel Color="Sienna" />
    </StackLayout.BindingContext>
    <BoxView Color="{Binding Color}"
             VerticalOptions="FillAndExpand" />
    <StackLayout x:DataType="{x:Null}"
                 Margin="10, 0">
        <Label Text="{Binding Name}" />
        <Slider Value="{Binding Hue}" />
        <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}" />
        <Slider Value="{Binding Saturation}" />
        <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}" />
        <Slider Value="{Binding Luminosity}" />
        <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}" />
    </StackLayout>
</StackLayout>   
```

根 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 将 `x:DataType` 属性设置为 `HslColorViewModel` 类型，表示将编译根 `StackLayout` 视图结构中的任意绑定表达式。 但是，内部 `StackLayout` 使用 `x:Null` 标记表达式将 `x:DataType` 属性重新定义为 `null`。 因此，内部 `StackLayout` 中的绑定表达式使用传统绑定。 只有根 `StackLayout` 视图结构层次中的 [`BoxView`](xref:Xamarin.Forms.BoxView) 使用已编译的绑定。

有关 `x:Null` 标记表达式的详细信息，请参阅 [x:Null 标记扩展](~/xamarin-forms/xaml/markup-extensions/consuming.md#xnull-markup-extension)。

## <a name="performance"></a>性能

已编译的绑定可提升数据绑定性能，性能优势各不相同。 单元测试揭示了：

- 使用属性更改通知（即 `OneWay`、`OneWayToSource` 或 `TwoWay` 绑定）的已编译绑定的解析速度比传统绑定快约 8 倍。
- 不使用属性更改通知（例如 `OneTime` 绑定）的已编译绑定的解析速度比传统绑定快约 20 倍。
- 在使用属性更改通知（即 `OneWay`、`OneWayToSource` 或 `TwoWay` 绑定）的已编译绑定上设置 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 比在经典绑定上设置 `BindingContext` 快大约 5 倍。
- 在不使用属性更改通知（即 `OneTime` 绑定）的已编译绑定上设置 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 比在经典绑定上设置 `BindingContext` 快大约 7 倍。

这些性能差异在移动设备上更为明显，具体取决于所使用的平台、正在使用的操作系统的版本以及运行应用程序的设备。

## <a name="related-links"></a>相关链接

- [数据绑定演示（示例）](/samples/xamarin/xamarin-forms-samples/databindingdemos)