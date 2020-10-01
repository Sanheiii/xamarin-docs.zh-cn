---
title: Xamarin.Forms 绑定回退
description: 本文介绍了如何通过定义绑定失败时将使用的回退值来使绑定更加可靠。
ms.prod: xamarin
ms.assetid: 637ACD9D-3E5D-4014-86DE-A77D1FEF238A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/16/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 24a9a351dbe6932b09add2ee7c3111256e013201
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91560684"
---
# <a name="no-locxamarinforms-binding-fallbacks"></a>Xamarin.Forms 绑定回退

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

有时数据绑定会失败，因为无法解析绑定源，或者因为绑定成功但返回 `null` 值。 虽然可以使用值转换器或其他附加代码处理这些情况，但是通过定义在绑定过程失败时要使用的回退值，可以使数据绑定更加可靠。 这可以通过定义绑定表达式中的 [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) 和 [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) 属性来实现。 因为这些属性位于 [`BindingBase`](xref:Xamarin.Forms.BindingBase) 类中，它们可以与绑定、多绑定、编译绑定和 `Binding` 标记扩展一起使用。

> [!NOTE]
> 可以选择利用绑定表达式中的 [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) 和 [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) 属性。

## <a name="defining-a-fallback-value"></a>定义一个回退值

[`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) 属性允许定义在无法解析绑定源时使用的回退值**。 设置此属性的常见方案是绑定到可能不存在于异类类型的绑定集合中的所有对象上的源属性。

MonkeyDetail 页说明了设置 [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) 属性的方法****：

```xaml
<Label Text="{Binding Population, FallbackValue='Population size unknown'}"
       ... />   
```

[`Label`](xref:Xamarin.Forms.Label) 上的绑定定义了 [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) 值，如果无法解析绑定源，将在目标上设置该值。 因此，如果 `Population` 属性不存在于绑定对象中，则显示 `FallbackValue` 属性定义的值。 请注意，此处 `FallbackValue` 属性值由单引号（撇号）字符分隔。

比起内联定义 [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) 属性值，更推荐将它们定义为 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 中的资源。 这种方法的优点是在一个位置一次性地定义这些值，并且更容易本地化。 然后可以使用 `StaticResource` 标记扩展检索资源：

```xaml
<Label Text="{Binding Population, FallbackValue={StaticResource populationUnknown}}"
       ... />  
```

> [!NOTE]
> 不能使用绑定表达式设置 `FallbackValue` 属性。

下面是正在运行的程序：

![FallbackValue 绑定](binding-fallbacks-images/bindingunavailable-detail-cropped.png "FallbackValue 绑定")

如果未在绑定表达式中设置 `FallbackValue` 属性且未解析绑定路径或路径的一部分，则会在目标上设置 [`BindableProperty.DefaultValue`](xref:Xamarin.Forms.BindableProperty.DefaultValue)。 但是，当设置了 `FallbackValue` 属性，且未解析绑定路径或部分路径，则会在目标上设置 `FallbackValue` 值属性的值。 因此，在 **MonkeyDetail**页面上，[`Label`](xref:Xamarin.Forms.Label) 显示“人口大小未知”，因为绑定对象缺少 `Population` 属性。

> [!IMPORTANT]
> 当设置了 [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) 时，则不会执行绑定表达式中的已定义的值转换器。

## <a name="defining-a-null-replacement-value"></a>定义 null 替换值

[`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) 属性允许定义替换值，该值将在解析绑定源时使用，但值为 `null`**。 设置此属性的常见方案是绑定到绑定集合中可能为 `null` 的源属性。

**Monkeys** 页说明了设置 [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) 属性的方法：

```xaml
<ListView ItemsSource="{Binding Monkeys}"
          ...>
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
                <Grid>
                    ...
                    <Image Source="{Binding ImageUrl, TargetNullValue='https://upload.wikimedia.org/wikipedia/commons/2/20/Point_d_interrogation.jpg'}"
                           ... />
                    ...
                    <Label Text="{Binding Location, TargetNullValue='Location unknown'}"
                           ... />
                </Grid>
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

[`Image`](xref:Xamarin.Forms.Image) 和 [`Label`](xref:Xamarin.Forms.Label) 上的绑定都定义了 [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) 值，如果绑定路径返回 `null` 将应用这些值。 因此，将为集合中未定义 `ImageUrl` 和 `Location` 属性的任何对象显示由 `TargetNullValue` 属性定义的值。 请注意，此处 `TargetNullValue` 属性值由单引号（撇号）字符分隔。

比起内联定义 [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) 属性值，更推荐将它们定义为 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 中的资源。 这种方法的优点是在一个位置一次性地定义这些值，并且更容易本地化。 然后可以使用 `StaticResource` 标记扩展检索资源：

```xaml
<Image Source="{Binding ImageUrl, TargetNullValue={StaticResource fallbackImageUrl}}"
       ... />
<Label Text="{Binding Location, TargetNullValue={StaticResource locationUnknown}}"
       ... />
```

> [!NOTE]
> 不能使用绑定表达式设置 `TargetNullValue` 属性。

下面是正在运行的程序：

[![TargetNullValue 绑定](binding-fallbacks-images/bindingunavailable-small.png "TargetNullValue 绑定")](binding-fallbacks-images/bindingunavailable-large.png#lightbox "TargetNullValue 绑定")

如果未在绑定表达式中设置 `TargetNullValue` 属性，则在定义了值转换器时将转换源值 `null`，如果定义了 `StringFormat` 则将其格式化，然后在目标上设置结果。 但是，当设置了 `TargetNullValue` 属性时，如果定义了值转换器，则将转换源值 `null`，如果转换后它仍为 `null`，则会在目标上设置 `TargetNullValue` 属性的值。

> [!IMPORTANT]
> 设置了 `TargetNullValue` 属性时，将不会在绑定表达式中应用字符串格式设置。

## <a name="related-links"></a>相关链接

- [数据绑定演示（示例）](/samples/xamarin/xamarin-forms-samples/databindingdemos)