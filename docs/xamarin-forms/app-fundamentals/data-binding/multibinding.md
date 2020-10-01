---
title: Xamarin.Forms 多绑定
description: 本文介绍了如何使用 MultiBinding 类将 Binding 对象的集合附加到一个绑定目标属性。
ms.prod: xamarin
ms.assetid: E73AE622-664C-4A90-B5B2-BD47D0E7A1A7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/18/2020
ms.openlocfilehash: 0c10e73d8d6c2dcafacbb069eaf905a227030b87
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91557525"
---
# <a name="xamarinforms-multi-bindings"></a>Xamarin.Forms 多绑定

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

借助多绑定，可以将 [`Binding`](xref:Xamarin.Forms.Binding) 对象的集合附加到一个绑定目标属性。 它们是使用 `MultiBinding` 类创建的，此类计算其所有 `Binding` 对象，并通过应用程序提供的 `IMultiValueConverter` 实例返回一个值。 此外，当任何绑定数据发生更改时，`MultiBinding` 会重新计算其所有 `Binding` 对象。

`MultiBinding` 类定义了以下属性：

- `Bindings`：类型为 `IList<BindingBase>`，表示 `MultiBinding` 实例内 [`Binding`](xref:Xamarin.Forms.Binding) 对象的集合。
- `Converter`：类型为 `IMultiValueConverter`，表示用于在源值和目标值之间来回转换的转换器。
- `ConverterParameter`：类型为 `object`，表示要传递给 `Converter` 的可选参数。

由于 `Bindings` 属性是 `MultiBinding` 类的内容属性，因此不需要通过 XAML 显式设置。

此外，`MultiBinding` 类还从 `BindingBase` 类继承了以下属性：

- `FallbackValue`：类型为 `object`，表示在多绑定无法返回值时使用的值。
- `Mode`：类型为 [`BindingMode`](xref:Xamarin.Forms.BindingMode)，指明多绑定的数据流方向。
- `StringFormat`：类型为 `string`，指定如何设置显示为字符串的多绑定结果的格式。
- `TargetNullValue`：类型为 `object`，表示当源值为 `null` 时在目标中使用的值。

`MultiBinding` 必须使用 `IMultiValueConverter` 以根据 `Bindings` 集合中的绑定值来生成绑定目标的值。 例如，可以使用 red、blue 和 green 值计算 [`Color`](xref:Xamarin.Forms.Color)，这些值可以来自相同或不同的绑定源对象。 当值从目标移动到源时，目标属性值被转换为一组值，这些值反馈回绑定。

> [!IMPORTANT]
> `Bindings` 集合中的各个绑定都可以有自己的值转换器。

`Mode` 属性的值决定了 `MultiBinding` 的功能，并用作集合中所有绑定的绑定模式，除非某个绑定替代了此属性。 例如，如果 `MultiBinding` 对象的 `Mode` 属性设置为 [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay)，那么集合中的所有绑定都会被视为 `TwoWay`，除非你在其中一个绑定上显式设置了不同的 `Mode` 值。

## <a name="define-a-imultivalueconverter"></a>定义 IMultiValueConverter

使用 `IMultiValueConverter` 接口，可以将自定义逻辑应用于 `MultiBinding`。 若要将转换器与 `MultiBinding` 相关联，请先创建实现 `IMultiValueConverter` 接口的类，再实现 `Convert` 和 `ConvertBack` 方法：

```csharp
public class AllTrueMultiConverter : IMultiValueConverter
{
    public object Convert(object[] values, Type targetType, object parameter, CultureInfo culture)
    {
        if (values == null || !targetType.IsAssignableFrom(typeof(bool)))
        {
            return false;
            // Alternatively, return BindableProperty.UnsetValue to use the binding FallbackValue
        }

        foreach (var value in values)
        {
            if (!(value is bool b))
            {
                return false;
                // Alternatively, return BindableProperty.UnsetValue to use the binding FallbackValue
            }
            else if (!b)
            {
                return false;
            }
        }
        return true;
    }

    public object[] ConvertBack(object value, Type[] targetTypes, object parameter, CultureInfo culture)
    {
        if (!(value is bool b) || targetTypes.Any(t => !t.IsAssignableFrom(typeof(bool))))
        {
            // Return null to indicate conversion back is not possible
            return null;
        }

        if (b)
        {
            return targetTypes.Select(t => (object)true).ToArray();
        }
        else
        {
            // Can't convert back from false because of ambiguity
            return null;
        }
    }
}
```

`Convert` 方法将源值转换为绑定目标的值。 Xamarin.Forms 在将值从源绑定传播到绑定目标时调用此方法。 此方法接受四个参数：

- `values`：类型为 `object[]`，表示 `MultiBinding` 中的源绑定生成的值数组。
- `targetType`：类型为 `Type`，表示绑定目标属性的类型。
- `parameter`：类型为 `object`，表示要使用的转换器参数。
- `culture`：类型为 `CultureInfo`，表示要在转换器中使用的区域性。

`Convert` 方法返回表示转换后的值的 `object`。 此方法应返回：

- `BindableProperty.UnsetValue`：指明转换器没有生成值，绑定将使用 `FallbackValue`。
- `Binding.DoNothing`：指示 Xamarin.Forms 不要执行任何操作。 例如，指示 Xamarin.Forms 不要将值传输到绑定目标，或不要使用 `FallbackValue`。
- `null`：指明转换器无法执行转换，绑定将使用 `TargetNullValue`。

> [!IMPORTANT]
> 从 `Convert` 方法接收 `BindableProperty.UnsetValue` 的 `MultiBinding` 必须定义其 [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) 属性。 同样，从 `Convert` 方法接收 `null` 的 `MultiBinding` 必须定义其 [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) 属性。

`ConvertBack` 方法将绑定目标转换为源绑定值。 此方法接受四个参数：

- `value`：类型为 `object`，表示绑定目标生成的值。
- `targetTypes`：类型为 `Type[]`，表示要转换为的类型数组。 数组长度指示为要返回的方法所建议的值的数量与类型。
- `parameter`：类型为 `object`，表示要使用的转换器参数。
- `culture`：类型为 `CultureInfo`，表示要在转换器中使用的区域性。

`ConvertBack` 方法返回类型为 `object[]` 的值数组，这些值已从目标值转换回源值。 此方法应返回：

- `BindableProperty.UnsetValue`（在位置 `i` 处）：指明转换器无法在索引 `i` 处提供源绑定的值，并且不会对源绑定设置任何值。
- `Binding.DoNothing`（在位置 `i` 处）：指明在索引 `i` 处不会对源绑定设置任何值。
- `null`：指明转换器无法执行转换，或者不支持此方向的转换。

## <a name="consume-a-imultivalueconverter"></a>使用 IMultiValueConverter

`IMultiValueConverter` 的使用方式为，在资源字典中实例化它，然后使用 `StaticResource` 标记扩展来引用它，以设置 `MultiBinding.Converter` 属性：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.MultiBindingConverterPage"
             Title="MultiBinding Converter demo">

    <ContentPage.Resources>
        <local:AllTrueMultiConverter x:Key="AllTrueConverter" />
        <local:InverterConverter x:Key="InverterConverter" />
    </ContentPage.Resources>

    <CheckBox>
        <CheckBox.IsChecked>
            <MultiBinding Converter="{StaticResource AllTrueConverter}">
                <Binding Path="Employee.IsOver16" />
                <Binding Path="Employee.HasPassedTest" />
                <Binding Path="Employee.IsSuspended"
                         Converter="{StaticResource InverterConverter}" />
            </MultiBinding>
        </CheckBox.IsChecked>
    </CheckBox>
</ContentPage>    
```

在此示例中，`MultiBinding` 对象使用 `AllTrueMultiConverter` 实例将 [`CheckBox.IsChecked`](xref:Xamarin.Forms.CheckBox.IsChecked) 属性设置为 `true`，前提是三个 [`Binding`](xref:Xamarin.Forms.Binding) 对象的计算结果为 `true`。 否则，`CheckBox.IsChecked` 属性设置为 `false`。

默认情况下，[`CheckBox.IsChecked`](xref:Xamarin.Forms.CheckBox.IsChecked) 属性使用 [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) 绑定。 因此，如果用户取消选中 [`CheckBox`](xref:Xamarin.Forms.CheckBox)（即将源绑定值设置为 `CheckBox.IsChecked` 属性的值），就会执行 `AllTrueMultiConverter` 实例的 `ConvertBack` 方法。

## <a name="format-strings"></a>格式字符串

`MultiBinding` 可以使用 `StringFormat` 属性来设置任何显示为字符串的多绑定结果的格式。 此属性可以设置为包含占位符的标准 .NET 格式设置字符串，用于指定如何设置多绑定结果的格式：

```xaml
<Label>
    <Label.Text>
        <MultiBinding StringFormat="{}{0} {1} {2}">
            <Binding Path="Employee1.Forename" />
            <Binding Path="Employee1.MiddleName" />
            <Binding Path="Employee1.Surname" />
        </MultiBinding>
    </Label.Text>
</Label>
```

在此示例中，`StringFormat` 属性将三个绑定值合并为一个由 [`Label`](xref:Xamarin.Forms.Label) 显示的字符串。

> [!IMPORTANT]
> 采用复合字符串格式的参数数量不得超过 `MultiBinding` 中子 `Binding` 对象的数量。

如果同时设置了 `Converter` 和 `StringFormat` 属性，转换器先应用于数据值，然后应用 `StringFormat`。

若要详细了解 Xamarin.Forms 中的字符串格式设置，请参阅 [Xamarin.Forms 字符串格式设置](string-formatting.md)。

## <a name="provide-fallback-values"></a>提供回退值

通过定义在绑定过程失败时使用的回退值，可以提高数据绑定的可靠性。 为此，可以视需要选择在 `MultiBinding` 对象上定义 [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) 和 [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) 属性。

当 `IMultiValueConverter` 实例的 `Convert` 方法返回 `BindableProperty.UnsetValue`（指明转换器没有生成值）时，`MultiBinding` 就会使用其 [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue)。 当 `IMultiValueConverter` 实例的 `Convert` 方法返回 `null`（指明转换器无法执行转换）时，`MultiBinding` 就会使用其 [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue)。

若要详细了解绑定回退，请参阅 [Xamarin.Forms 绑定回退](binding-fallbacks.md)。

## <a name="nest-multibinding-objects"></a>嵌套 MultiBinding 对象

`MultiBinding` 对象是可以嵌套的，这样就能计算多个 `MultiBinding` 对象，以通过 `IMultiValueConverter` 实例返回一个值：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.NestedMultiBindingPage"
             Title="Nested MultiBinding demo">

    <ContentPage.Resources>
        <local:AllTrueMultiConverter x:Key="AllTrueConverter" />
        <local:AnyTrueMultiConverter x:Key="AnyTrueConverter" />
        <local:InverterConverter x:Key="InverterConverter" />
    </ContentPage.Resources>

    <CheckBox>
        <CheckBox.IsChecked>
            <MultiBinding Converter="{StaticResource AnyTrueConverter}">
                <MultiBinding Converter="{StaticResource AllTrueConverter}">
                    <Binding Path="Employee.IsOver16" />
                    <Binding Path="Employee.HasPassedTest" />
                    <Binding Path="Employee.IsSuspended" Converter="{StaticResource InverterConverter}" />                        
                </MultiBinding>
                <Binding Path="Employee.IsMonarch" />
            </MultiBinding>
        </CheckBox.IsChecked>
    </CheckBox>
</ContentPage>
```

在此示例中，`MultiBinding` 对象使用 `AnyTrueMultiConverter` 实例将 [`CheckBox.IsChecked`](xref:Xamarin.Forms.CheckBox.IsChecked) 属性设置为 `true`，前提是内部 `MultiBinding` 对象中的所有 [`Binding`](xref:Xamarin.Forms.Binding) 对象的计算结果都为 `true`，或外部 `MultiBinding` 对象中的 `Binding` 对象的计算结果为 `true`。 否则，`CheckBox.IsChecked` 属性设置为 `false`。

## <a name="use-a-relativesource-binding-in-a-multibinding"></a>在 MultiBinding 中使用 RelativeSource 绑定

`MultiBinding` 对象支持相对绑定，这样就可以设置相对于绑定目标位置的绑定源：

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:DataBindingDemos">
    <ContentPage.Resources>
        <local:AllTrueMultiConverter x:Key="AllTrueConverter" />

        <ControlTemplate x:Key="CardViewExpanderControlTemplate">
            <Expander BindingContext="{Binding Source={RelativeSource TemplatedParent}}"
                      IsExpanded="{Binding IsExpanded, Source={RelativeSource TemplatedParent}}"
                      BackgroundColor="{Binding CardColor}">
                <Expander.IsVisible>
                    <MultiBinding Converter="{StaticResource AllTrueConverter}">
                        <Binding Path="IsExpanded" />
                        <Binding Path="IsEnabled" />
                    </MultiBinding>
                </Expander.IsVisible>
                <Expander.Header>
                    <Grid>
                        <!-- XAML that defines Expander header goes here -->
                    </Grid>
                </Expander.Header>
                <Grid>
                    <!-- XAML that defines Expander content goes here -->
                </Grid>
            </Expander>
        </ControlTemplate>
    </ContentPage.Resources>

    <StackLayout>
        <controls:CardViewExpander BorderColor="DarkGray"
                                   CardTitle="John Doe"
                                   CardDescription="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla elit dolor, convallis non interdum."
                                   IconBackgroundColor="SlateGray"
                                   IconImageSource="user.png"
                                   ControlTemplate="{StaticResource CardViewExpanderControlTemplate}"
                                   IsEnabled="True"
                                   IsExpanded="True" />
    </StackLayout>
</ContentPage>
```

在此示例中，`TemplatedParent` 相对绑定模式用于从控件模板内绑定到模板应用到的运行时对象实例。 `Expander`（[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) 的根元素）将 `BindingContext` 设置为模板应用到的运行时对象实例。 因此，`Expander` 及其子级根据 `CardViewExpander` 对象的属性来解析自己的绑定表达式和 [`Binding`](xref:Xamarin.Forms.Binding) 对象。 `MultiBinding` 使用 `AllTrueMultiConverter` 实例将 `Expander.IsVisible` 属性设置为 `true`，前提是两个 [`Binding`](xref:Xamarin.Forms.Binding) 对象的计算结果为 `true`。 否则，`Expander.IsVisible` 属性设置为 `false`。

有关相对绑定的详细信息，请参阅 [Xamarin.Forms 相对绑定](relative-bindings.md)。 有关控件模板的详细信息，请参阅 [Xamarin.Forms 控件模板](~/xamarin-forms/app-fundamentals/templates/control-template.md)。

## <a name="related-links"></a>相关链接

- [数据绑定演示（示例）](/samples/xamarin/xamarin-forms-samples/databindingdemos)
- [Xamarin.Forms 字符串格式设置](string-formatting.md)
- [Xamarin.Forms 绑定回退](binding-fallbacks.md)
- [Xamarin.Forms 相对绑定](relative-bindings.md)
- [Xamarin.Forms 控件模板](~/xamarin-forms/app-fundamentals/templates/control-template.md)