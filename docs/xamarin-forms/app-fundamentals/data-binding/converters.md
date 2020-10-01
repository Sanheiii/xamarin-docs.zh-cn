---
title: Xamarin.Forms 绑定值转换器
description: 此文章介绍了如何通过实现值转换器（也称为绑定转换器或绑定值转换器）来强制转换或转换 Xamarin.Forms 数据绑定中的值。
ms.prod: xamarin
ms.assetid: 02B1BBE6-D804-490D-BDD4-8ACED8B70C92
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2a5912d18787543b05b3937f2b8b5c6a36b04873
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556810"
---
# <a name="no-locxamarinforms-binding-value-converters"></a>Xamarin.Forms 绑定值转换器

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

一般情况下数据绑定会将数据从源属性传递到目标属性，某些情况下则从目标属性传递到源属性。 当源和目标属性都是同一类型，或当一个类型可以隐式转换为另一种类型时，这类传递都是非常简单的。 如果不是这种情况，则必须执行类型转换。

[字符串格式设置](string-formatting.md)一文已介绍如何使用数据绑定的 `StringFormat` 属性将任意类型转换为字符串。 对于其他类型的转换，需要在类中编写一些专门的代码以实现 [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) 接口。 （通用 Windows 平台在 `Windows.UI.Xaml.Data` 命名空间中包含一个名为 [`IValueConverter`](/uwp/api/Windows.UI.Xaml.Data.IValueConverter/) 的类似的类，但此 `IValueConverter` 在 `Xamarin.Forms` 命名空间中。）实现 `IValueConverter` 的类被称为“值转换器”，但它们通常也被称为“绑定转换器”或“绑定值转换器”  。

## <a name="the-ivalueconverter-interface"></a>IValueConverter 接口

假设想要定义源属性为类型 `int` 但目标属性为 `bool` 的数据绑定。 想要此数据绑定在整数源等于 0 时生成 `false` 值，在其他情况则生成 `true`。  

可以使用实现 `IValueConverter` 接口的类做到这一点：

```csharp
public class IntToBoolConverter : IValueConverter
{
    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (int)value != 0;
    }

    public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (bool)value ? 1 : 0;
    }
}
```

将此类的实例设为 `Binding` 类的 [`Converter`](xref:Xamarin.Forms.Binding.Converter) 属性或 `Binding` 标记扩展的 [`Converter`](xref:Xamarin.Forms.Xaml.BindingExtension.Converter) 属性。 此类成为数据绑定的一部分。

当数据在 `OneWay` 或 `TwoWay` 绑定中由源移动到目标时，将调用 `Convert` 方法。 `value` 是来自数据绑定源的对象或值。 该方法必须返回数据绑定目标类型的值。 此处所示的方法将 `value` 参数强制转换为 `int`，然后将其与 0 比较并得到 `bool` 返回值。

当数据在 `TwoWay` 或 `OneWayToSource` 绑定中由目标移动到源时，将调用 `ConvertBack` 方法。 `ConvertBack`执行相反的转换：它假定 `value` 参数是来自目标的 `bool`，然后将其转换为源的 `int` 返回值。

如果数据绑定还包括 `StringFormat` 设置，则在结果格式化为字符串之前调用值转换器。

[数据绑定演示](/samples/xamarin/xamarin-forms-samples/databindingdemos)示例中的启用按钮页面演示了如何在数据绑定中使用此值转换器 。 `IntToBoolConverter` 在页面的资源字典中实例化。 然后使用 `StaticResource` 标记扩展引用它以在两个数据绑定中设置 `Converter` 属性。 在页面中的多个数据绑定中共享数据转换器是很常见的：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.EnableButtonsPage"
             Title="Enable Buttons">
    <ContentPage.Resources>
        <ResourceDictionary>
            <local:IntToBoolConverter x:Key="intToBool" />
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Padding="10, 0">
        <Entry x:Name="entry1"
               Text=""
               Placeholder="enter search term"
               VerticalOptions="CenterAndExpand" />

        <Button Text="Search"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                IsEnabled="{Binding Source={x:Reference entry1},
                                    Path=Text.Length,
                                    Converter={StaticResource intToBool}}" />

        <Entry x:Name="entry2"
               Text=""
               Placeholder="enter destination"
               VerticalOptions="CenterAndExpand" />

        <Button Text="Submit"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                IsEnabled="{Binding Source={x:Reference entry2},
                                    Path=Text.Length,
                                    Converter={StaticResource intToBool}}" />
    </StackLayout>
</ContentPage>
```

如果在应用程序的多个页面中使用值转换器，则可以在 App.xaml 文件的资源字典中将其实例化。

启用按钮页面演示了当 `Button` 基于用户在 `Entry` 视图中键入的文本执行操作时的常见需求。 如果用户没有在 `Entry` 中键入任何内容，则应禁用 `Button`。 每个 `Button` 都包含其 `IsEnabled` 属性的数据绑定。 数据绑定源是相应 `Entry` 的 `Text` 属性的 `Length` 属性。 如果 `Length`属性不是 0，则值转换器返回 `true` 并启用 `Button`：

[![启用按钮](converters-images/enablebuttons-small.png "启用按钮")](converters-images/enablebuttons-large.png#lightbox "启用按钮")

请注意，每个 `Entry` 中的 `Text` 属性都将初始化为空字符串。 默认情况下 `Text` 属性为 `null`，且在此情况下不会运行数据绑定。

某些值转换器是专门为特定应用程序编写的，而其他值转换器已通用化。 如果知道值转换器将仅用于 `OneWay` 绑定，则 `ConvertBack` 方法只能返回 `null`。

上面所示的 `Convert` 方法隐式假设 `value` 参数的类型为 `int`，返回值的类型必须为 `bool`。 同样，`ConvertBack` 方法假设 `value` 参数的类型为 `bool`，返回值为 `int`。 如果不是这种情况，将发生运行时异常。

可以将值转换器编写得更加通用化且可以接受多种不同类型的数据。 `Convert` 和 `ConvertBack` 方法可以使用带有 `value` 参数的 `as` 或 `is` 运算符，或者可以在该参数上调用 `GetType` 以确定其类型，然后再执行适当的操作。 每个方法的返回值的预期类型由 `targetType` 参数给出。 有时，值转换器与不同目标类型的数据绑定一起使用；值转换器可以使用 `targetType` 参数来执行正确类型的转换。

如果正在执行对于不同区域性会有所不同的转换，请使用 `culture` 参数实现此目的。 `Convert` 和 `ConvertBack` 的 `parameter` 参数稍后将在本文中介绍。

## <a name="binding-converter-properties"></a>绑定转换器属性

值转换器类可以具有属性和泛型参数。 此特定值转换器将 `bool` 从源转换为目标的 `T` 类型对象：

```csharp
public class BoolToObjectConverter<T> : IValueConverter
{
    public T TrueObject { set; get; }

    public T FalseObject { set; get; }

    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (bool)value ? TrueObject : FalseObject;
    }

    public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return ((T)value).Equals(TrueObject);
    }
}
```

切换指示器页面演示了如何使用它来显示 `Switch` 视图的值。 虽然将值转换器实例化为资源字典中的资源很常见，但此页面演示了另一种选择：每个值转换器都在 `Binding.Converter` 属性元素标记之间实例化。 `x:TypeArguments` 指示泛型参数，并将 `TrueObject` 和 `FalseObject` 都设置为该类型的对象：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.SwitchIndicatorsPage"
             Title="Switch Indicators">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="FontSize" Value="18" />
                <Setter Property="VerticalOptions" Value="Center" />
            </Style>

            <Style TargetType="Switch">
                <Setter Property="VerticalOptions" Value="Center" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Padding="10, 0">
        <StackLayout Orientation="Horizontal"
                     VerticalOptions="CenterAndExpand">
            <Label Text="Subscribe?" />
            <Switch x:Name="switch1" />
            <Label>
                <Label.Text>
                    <Binding Source="{x:Reference switch1}"
                             Path="IsToggled">
                        <Binding.Converter>
                            <local:BoolToObjectConverter x:TypeArguments="x:String"
                                                         TrueObject="Of course!"
                                                         FalseObject="No way!" />
                        </Binding.Converter>
                    </Binding>
                </Label.Text>
            </Label>
        </StackLayout>

        <StackLayout Orientation="Horizontal"
                     VerticalOptions="CenterAndExpand">
            <Label Text="Allow popups?" />
            <Switch x:Name="switch2" />
            <Label>
                <Label.Text>
                    <Binding Source="{x:Reference switch2}"
                             Path="IsToggled">
                        <Binding.Converter>
                            <local:BoolToObjectConverter x:TypeArguments="x:String"
                                                         TrueObject="Yes"
                                                         FalseObject="No" />
                        </Binding.Converter>
                    </Binding>
                </Label.Text>
                <Label.TextColor>
                    <Binding Source="{x:Reference switch2}"
                             Path="IsToggled">
                        <Binding.Converter>
                            <local:BoolToObjectConverter x:TypeArguments="Color"
                                                         TrueObject="Green"
                                                         FalseObject="Red" />
                        </Binding.Converter>
                    </Binding>
                </Label.TextColor>
            </Label>
        </StackLayout>

        <StackLayout Orientation="Horizontal"
                     VerticalOptions="CenterAndExpand">
            <Label Text="Learn more?" />
            <Switch x:Name="switch3" />
            <Label FontSize="18"
                   VerticalOptions="Center">
                <Label.Style>
                    <Binding Source="{x:Reference switch3}"
                             Path="IsToggled">
                        <Binding.Converter>
                            <local:BoolToObjectConverter x:TypeArguments="Style">
                                <local:BoolToObjectConverter.TrueObject>
                                    <Style TargetType="Label">
                                        <Setter Property="Text" Value="Indubitably!" />
                                        <Setter Property="FontAttributes" Value="Italic, Bold" />
                                        <Setter Property="TextColor" Value="Green" />
                                    </Style>                                    
                                </local:BoolToObjectConverter.TrueObject>

                                <local:BoolToObjectConverter.FalseObject>
                                    <Style TargetType="Label">
                                        <Setter Property="Text" Value="Maybe later" />
                                        <Setter Property="FontAttributes" Value="None" />
                                        <Setter Property="TextColor" Value="Red" />
                                    </Style>
                                </local:BoolToObjectConverter.FalseObject>
                            </local:BoolToObjectConverter>
                        </Binding.Converter>
                    </Binding>
                </Label.Style>
            </Label>
        </StackLayout>
    </StackLayout>
</ContentPage>
```

在三个 `Switch` 和 `Label` 对中的最后一个对中，泛型参数被设为 `Style`，并且为 `TrueObject` 和 `FalseObject` 的值提供了整个 `Style` 对象。 这些替代了资源字典中设置的 `Label` 的隐式样式，因此该样式中的属性被显式分配给 `Label`。 切换 `Switch` 会导致相应的 `Label` 对此更改作出反应：

[![切换指示器](converters-images/switchindicators-small.png "切换指示器")](converters-images/switchindicators-large.png#lightbox "切换指示器")

它也可以使用 [`Triggers`](~/xamarin-forms/app-fundamentals/triggers.md) 在基于其他视图的用户界面中实现类似的更改。

## <a name="binding-converter-parameters"></a>绑定转换器参数

`Binding` 类定义 [`ConverterParameter`](xref:Xamarin.Forms.Binding.ConverterParameter) 属性，`Binding` 标记扩展也定义 [`ConverterParameter`](xref:Xamarin.Forms.Xaml.BindingExtension.ConverterParameter) 属性。 如果设置此属性，则该值将作为 `parameter` 参传递到 `Convert` 和 `ConvertBack` 方法。 即使值转换器实例在多个数据绑定之间共享，`ConverterParameter` 也可能不同，执行的转换就会有所不同。

使用颜色选择程序演示了 `ConverterParameter` 的用法。 在这种情况下，`RgbColorViewModel` 有三个分别名为 `Red`、`Green` 和 `Blue` 的类型 `double` 属性，用于构造 `Color` 值：

```csharp
public class RgbColorViewModel : INotifyPropertyChanged
{
    Color color;
    string name;

    public event PropertyChangedEventHandler PropertyChanged;

    public double Red
    {
        set
        {
            if (color.R != value)
            {
                Color = new Color(value, color.G, color.B);
            }
        }
        get
        {
            return color.R;
        }
    }

    public double Green
    {
        set
        {
            if (color.G != value)
            {
                Color = new Color(color.R, value, color.B);
            }
        }
        get
        {
            return color.G;
        }
    }

    public double Blue
    {
        set
        {
            if (color.B != value)
            {
                Color = new Color(color.R, color.G, value);
            }
        }
        get
        {
            return color.B;
        }
    }

    public Color Color
    {
        set
        {
            if (color != value)
            {
                color = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Red"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Green"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Blue"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Color"));

                Name = NamedColor.GetNearestColorName(color);
            }
        }
        get
        {
            return color;
        }
    }

    public string Name
    {
        private set
        {
            if (name != value)
            {
                name = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Name"));
            }
        }
        get
        {
            return name;
        }
    }
}
```

`Red`、`Green` 和 `Blue` 的属性范围介于 0 和 1 之间。 但是，你可能更愿意将组件显示为两位数的十六进制值。

若要在 XAML 中将这些显示为十六进制值，它们必须乘以 255，转换为整数，然后使用 `StringFormat` 属性中的“X2”规范格式化。 前两个任务（乘以 255 并转换为整数）可以由值转换器处理。 若要尽可能提高值转换器的通用性，可以使用 `ConverterParameter` 属性指定乘法因数，这意味着它将作为 `parameter` 参数进入 `Convert` 和 `ConvertBack` 方法：

```csharp
public class DoubleToIntConverter : IValueConverter
{
    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (int)Math.Round((double)value * GetParameter(parameter));
    }

    public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (int)value / GetParameter(parameter);
    }

    double GetParameter(object parameter)
    {
        if (parameter is double)
            return (double)parameter;

        else if (parameter is int)
            return (int)parameter;

        else if (parameter is string)
            return double.Parse((string)parameter);

        return 1;
    }
}
```

`Convert` 从 `double` 转换为 `int`，同时乘以 `parameter` 值；`ConvertBack` 将整数 `value` 参数除以 `parameter` 并返回 `double` 结果。 （在下面显示的程序中，值转换器仅用于和字符串格式设置的连接中，因此不使用 `ConvertBack`。）

`parameter` 参数的类型可能会有所不同，具体取决于数据绑定是在代码中还是在 XAML 中定义的。 如果 `Binding` 的 `ConverterParameter` 属性是在代码中设置的，则很可能设置为数字值：

```csharp
binding.ConverterParameter = 255;
```

`ConverterParameter` 属性的类型为 `Object`，因此 C# 编译器将文字 255 解释为整数，并将该属性设置为该值。

但是，在 XAML 中，`ConverterParameter` 很可能设置为这样：

```xaml
<Label Text="{Binding Red,
                      Converter={StaticResource doubleToInt},
                      ConverterParameter=255,
                      StringFormat='Red = {0:X2}'}" />
```

255 看起来是一个数字，但因为 `ConverterParameter` 属于类型 `Object`，因此 XAML 分析器将 255 视为字符串。

为此，上述的值转换器包含一个单独的 `GetParameter` 方法，该方法用于处理 `parameter` 属于类型 `double`、`int` 或 `string` 的情况。  

RGB 颜色选择器页面遵照两个隐式样式的定义在其资源字典中实例化 `DoubleToIntConverter`：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.RgbColorSelectorPage"
             Title="RGB Color Selector">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Slider">
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>

            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>

            <local:DoubleToIntConverter x:Key="doubleToInt" />
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout>
        <StackLayout.BindingContext>
            <local:RgbColorViewModel Color="Gray" />
        </StackLayout.BindingContext>

        <BoxView Color="{Binding Color}"
                 VerticalOptions="FillAndExpand" />

        <StackLayout Margin="10, 0">
            <Label Text="{Binding Name}" />

            <Slider Value="{Binding Red}" />
            <Label Text="{Binding Red,
                                  Converter={StaticResource doubleToInt},
                                  ConverterParameter=255,
                                  StringFormat='Red = {0:X2}'}" />

            <Slider Value="{Binding Green}" />
            <Label Text="{Binding Green,
                                  Converter={StaticResource doubleToInt},
                                  ConverterParameter=255,
                                  StringFormat='Green = {0:X2}'}" />

            <Slider Value="{Binding Blue}" />
            <Label>
                <Label.Text>
                    <Binding Path="Blue"
                             StringFormat="Blue = {0:X2}"
                             Converter="{StaticResource doubleToInt}">
                        <Binding.ConverterParameter>
                            <x:Double>255</x:Double>
                        </Binding.ConverterParameter>
                    </Binding>
                </Label.Text>
            </Label>
        </StackLayout>
    </StackLayout>
</ContentPage>    
```

`Red` 和 `Green` 属性的值是使用 `Binding` 标记扩展显示的。 但是，`Blue` 属性实例化 `Binding` 以演示如何将显式 `double` 值设置为 `ConverterParameter` 属性。

结果如下：

[![RGB 颜色选择器](converters-images/rgbcolorselector-small.png "RGB 颜色选择器")](converters-images/rgbcolorselector-large.png#lightbox "RGB 颜色选择器")

## <a name="related-links"></a>相关链接

- [数据绑定演示（示例）](/samples/xamarin/xamarin-forms-samples/databindingdemos)
- [Xamarin.Forms 书籍中的数据绑定章节](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)