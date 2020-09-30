---
title: 第 4 部分。 数据绑定基础知识
description: 数据绑定允许对两个对象的属性进行链接，以便其中一个对象的更改会导致另一个对象发生变化。
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 342288C3-BB4C-4924-B178-72E112D777BA
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2dd2abed23704f6a67ac34bc828c48e7200cf99a
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91558711"
---
# <a name="part-4-data-binding-basics"></a>第 4 部分。 数据绑定基础知识

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_数据绑定允许对两个对象的属性进行链接，以便其中一个对象的更改会导致另一个对象发生变化。这是一个非常有用的工具，而数据绑定可以完全在代码中定义，XAML 提供快捷方式和便利性。因此，中最重要的标记扩展之一 Xamarin.Forms 是绑定。_

## <a name="data-bindings"></a>数据绑定

数据绑定连接两个对象（称为 " *源* " 和 " *目标*"）的属性。 在代码中，需要两个步骤： `BindingContext` 目标对象的属性必须设置为源对象，并且 `SetBinding` (经常与类一起使用的方法 `Binding`) 必须在目标对象上调用该方法以将该对象的属性绑定到源对象的属性。

目标属性必须是可绑定属性，这意味着目标对象必须派生自 `BindableObject` 。 联机 Xamarin.Forms 文档指示哪些属性是可绑定属性。 等的属性 `Label` `Text` 与可绑定属性关联 `TextProperty` 。

在标记中，你还必须在代码中执行所需的两个步骤，只不过 `Binding` 标记扩展将取代 `SetBinding` 调用和 `Binding` 类。

但是，当您在 XAML 中定义数据绑定时，可以通过多种方式设置 `BindingContext` 目标对象的。 有时，它是从代码隐藏文件（有时使用 `StaticResource` 或 `x:Static` 标记扩展）设置的，有时是 `BindingContext` 属性元素标记的内容。

绑定最常用于将程序的视觉对象连接到基础数据模型，这通常是为了实现 MVVM (模型-ViewModel) 应用程序体系结构，如第 [5 部分所述。从数据绑定到 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)，但可以实现其他方案。

## <a name="view-to-view-bindings"></a>视图到视图绑定

您可以定义数据绑定，以便链接同一页上两个视图的属性。 在这种情况下，可以 `BindingContext` 使用标记扩展设置目标对象的 `x:Reference` 。

下面是一个 XAML 文件，其中包含一个 `Slider` 和两个 `Label` 视图，其中一个视图由值旋转， `Slider` 另一个显示 `Slider` 值：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SliderBindingsPage"
             Title="Slider Bindings Page">

    <StackLayout>
        <Label Text="ROTATION"
               BindingContext="{x:Reference Name=slider}"
               Rotation="{Binding Path=Value}"
               FontAttributes="Bold"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                Maximum="360"
                VerticalOptions="CenterAndExpand" />

        <Label BindingContext="{x:Reference slider}"
               Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
               FontAttributes="Bold"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

`Slider`包含 `x:Name` `Label` 使用标记扩展的两个视图所引用的特性 `x:Reference` 。

`x:Reference`绑定扩展定义了一个名为的属性，该属性 `Name` 设置为所引用元素的名称（在本例中为） `slider` 。 不过， `ReferenceExtension` 定义标记扩展的类 `x:Reference` 还会定义的 `ContentProperty` 属性 `Name` ，这意味着它不是显式必需的。 仅针对各种种类，第一个 `x:Reference` 包括 "Name ="，但第二个包括：

```xaml
BindingContext="{x:Reference Name=slider}"
…
BindingContext="{x:Reference slider}"
```

`Binding`标记扩展本身可具有多个属性，就像 `BindingBase` 和类一样 `Binding` 。 的 `ContentProperty` 为 `Binding` `Path` ，但如果路径为标记扩展中的第一项，则可以省略标记扩展的 "路径 =" 部分 `Binding` 。 第一个示例具有 "Path ="，但第二个示例省略它：

```xaml
Rotation="{Binding Path=Value}"
…
Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
```

这些属性可以位于一行上，也可以分为多行：

```xaml
Text="{Binding Value,
               StringFormat='The angle is {0:F0} degrees'}"
```

执行任何操作都很方便。

请注意 `StringFormat` 第二个标记扩展中的属性 `Binding` 。 在中 Xamarin.Forms ，绑定不会执行任何隐式类型转换，并且如果需要以字符串形式显示非字符串对象，则必须提供类型转换器或使用 `StringFormat` 。 在后台， `String.Format` 使用静态方法实现 `StringFormat` 。 这可能是一个问题，因为 .NET 格式设置规范涉及大括号，后者还用于分隔标记扩展。 这会造成混淆 XAML 分析器的风险。 若要避免出现这种情况，请将整个格式设置字符串放在单引号中：

```xaml
Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
```

下面是正在运行的程序：

[![视图到视图绑定](data-binding-basics-images/sliderbinding.png)](data-binding-basics-images/sliderbinding-large.png#lightbox)

## <a name="the-binding-mode"></a>绑定模式

单个视图可以具有其多个属性的数据绑定。 但是，每个视图只能有一个 `BindingContext` ，因此该视图上的多个数据绑定必须是同一对象的所有引用属性。

此问题和其他问题的解决方案涉及到 `Mode` 属性，该属性设置为枚举的成员 `BindingMode` ：

- `Default`
- `OneWay` -值从源传输到目标
- `OneWayToSource` -值从目标传输到源
- `TwoWay` -源和目标之间的双向传输值
- `OneTime` -数据从源到目标进行，但仅当 `BindingContext` 发生更改时，

下面的程序演示了 `OneWayToSource` 和绑定模式的一个常见用法 `TwoWay` 。 四个 `Slider` 视图旨在控制的 `Scale` 、、 `Rotate` `RotateX` 和 `RotateY` 属性 `Label` 。 最初，似乎这四个属性 `Label` 应为数据绑定目标，因为每个属性都是由设置的 `Slider` 。 但是， `BindingContext` 的只能 `Label` 是一个对象，有四个不同的滑块。

出于此原因，所有绑定都以看似向后的方式进行设置： `BindingContext` 四个滑块的每个滑块的都设置为 `Label` ，并在滑块的属性中设置绑定 `Value` 。 通过使用 `OneWayToSource` 和 `TwoWay` 模式，这些 `Value` 属性可以设置源属性，这些属性是 `Scale` 的、、 `Rotate` `RotateX` 和 `RotateY` 属性 `Label` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SliderTransformsPage"
             Padding="5"
             Title="Slider Transforms Page">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="Auto" />
        </Grid.ColumnDefinitions>

        <!-- Scaled and rotated Label -->
        <Label x:Name="label"
               Text="TEXT"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <!-- Slider and identifying Label for Scale -->
        <Slider x:Name="scaleSlider"
                BindingContext="{x:Reference label}"
                Grid.Row="1" Grid.Column="0"
                Maximum="10"
                Value="{Binding Scale, Mode=TwoWay}" />

        <Label BindingContext="{x:Reference scaleSlider}"
               Text="{Binding Value, StringFormat='Scale = {0:F1}'}"
               Grid.Row="1" Grid.Column="1"
               VerticalTextAlignment="Center" />

        <!-- Slider and identifying Label for Rotation -->
        <Slider x:Name="rotationSlider"
                BindingContext="{x:Reference label}"
                Grid.Row="2" Grid.Column="0"
                Maximum="360"
                Value="{Binding Rotation, Mode=OneWayToSource}" />

        <Label BindingContext="{x:Reference rotationSlider}"
               Text="{Binding Value, StringFormat='Rotation = {0:F0}'}"
               Grid.Row="2" Grid.Column="1"
               VerticalTextAlignment="Center" />

        <!-- Slider and identifying Label for RotationX -->
        <Slider x:Name="rotationXSlider"
                BindingContext="{x:Reference label}"
                Grid.Row="3" Grid.Column="0"
                Maximum="360"
                Value="{Binding RotationX, Mode=OneWayToSource}" />

        <Label BindingContext="{x:Reference rotationXSlider}"
               Text="{Binding Value, StringFormat='RotationX = {0:F0}'}"
               Grid.Row="3" Grid.Column="1"
               VerticalTextAlignment="Center" />

        <!-- Slider and identifying Label for RotationY -->
        <Slider x:Name="rotationYSlider"
                BindingContext="{x:Reference label}"
                Grid.Row="4" Grid.Column="0"
                Maximum="360"
                Value="{Binding RotationY, Mode=OneWayToSource}" />

        <Label BindingContext="{x:Reference rotationYSlider}"
               Text="{Binding Value, StringFormat='RotationY = {0:F0}'}"
               Grid.Row="4" Grid.Column="1"
               VerticalTextAlignment="Center" />
    </Grid>
</ContentPage>
```

这三个视图的绑定 `Slider` 都是 `OneWayToSource` ，这意味着 `Slider` 该值会导致其的属性的更改 `BindingContext` ，这是 `Label` 名为的 `label` 。 这三个 `Slider` 视图将导致 `Rotate` 的、 `RotateX` 和属性 `RotateY` `Label` 发生更改。

但是，属性的绑定 `Scale` 为 `TwoWay` 。 这是因为 `Scale` 属性的默认值为1，而使用 `TwoWay` 绑定会导致 `Slider` 初始值设置为1而不是0。 如果该绑定为 `OneWayToSource` ，则 `Scale` 从默认值开始，属性最初设置为 0 `Slider` 。 将 `Label` 不可见，这可能会对用户造成一些混淆。

 [![向后绑定](data-binding-basics-images/slidertransforms.png)](data-binding-basics-images/slidertransforms-large.png#lightbox)

 > [!NOTE]
 > [`VisualElement`](xref:Xamarin.Forms.VisualElement)类还具有 [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX) 和 [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY) 属性，它们 `VisualElement` 分别在 x 轴和 y 轴上缩放。

## <a name="bindings-and-collections"></a>绑定和集合

XAML 和数据绑定的强大功能比模板更好 `ListView` 。

`ListView` 定义 `ItemsSource` 类型的属性 `IEnumerable` ，并显示该集合中的项。 这些项可以是任何类型的对象。 默认情况下， `ListView` 使用 `ToString` 每个项的方法来显示该项。 有时这只是所需的内容，但在许多情况下， `ToString` 只返回对象的完全限定类名。

但是， `ListView` 可以通过使用 *模板*以任何所需的方式显示集合中的项，这涉及派生自的类 `Cell` 。 将为中的每个项克隆模板 `ListView` ，对模板设置的数据绑定会传输到各个克隆。

通常，您需要使用类为这些项创建自定义单元格 `ViewCell` 。 此过程在代码中有些混乱，但在 XAML 中很简单。

XamlSamples 项目包含一个名为的类 `NamedColor` 。 每个 `NamedColor` 对象都具有 `Name` `FriendlyName` 类型的和属性以及 `string` `Color` 类型的属性 `Color` 。 此外， `NamedColor` 具有141类型的静态只读字段，它们 `Color` 对应于类中定义的颜色 Xamarin.Forms `Color` 。 静态构造函数创建一个 `IEnumerable<NamedColor>` 集合，该集合包含 `NamedColor` 与这些静态字段对应的对象，并将其分配给其公共静态 `All` 属性。

`NamedColor.All`使用标记扩展将静态属性设置为 `ItemsSource` 的 `ListView` 非常简单 `x:Static` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.ListViewDemoPage"
             Title="ListView Demo Page">

    <ListView ItemsSource="{x:Static local:NamedColor.All}" />

</ContentPage>
```

生成的显示将确定项确实属于类型 `XamlSamples.NamedColor` ：

[![绑定到集合](data-binding-basics-images/listview1.png)](data-binding-basics-images/listview1-large.png#lightbox)

这不是很多信息，但 `ListView` 可滚动和可选择。

若要为项定义模板，需要将 `ItemTemplate` 属性作为属性元素进行分解，并将其设置为 `DataTemplate` ，然后引用 `ViewCell` 。 对于 `View` 的属性， `ViewCell` 可以定义一个或多个视图的布局，以显示每个项。 下面是一个简单示例：

```xaml
<ListView ItemsSource="{x:Static local:NamedColor.All}">
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
                <ViewCell.View>
                    <Label Text="{Binding FriendlyName}" />
                </ViewCell.View>
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

> [!NOTE]
> 单元格的绑定源和单元格的子元素是 `ListView.ItemsSource` 集合。

`Label`元素设置为 `View` 的属性 `ViewCell` 。  (`ViewCell.View` 不需要标记，因为 `View` 属性是的内容属性 `ViewCell` 。 ) 此标记显示 `FriendlyName` 每个对象的属性 `NamedColor` ：

[![绑定到具有 System.windows.datatemplate> 的集合](data-binding-basics-images/listview2.png)](data-binding-basics-images/listview2-large.png#lightbox)

好多了。 现在只需修饰项模板，其中包含详细信息和实际颜色。 为了支持此模板，已在页面的资源字典中定义了一些值和对象：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             x:Class="XamlSamples.ListViewDemoPage"
             Title="ListView Demo Page">

    <ContentPage.Resources>
        <ResourceDictionary>
            <OnPlatform x:Key="boxSize"
                        x:TypeArguments="x:Double">
                <On Platform="iOS, Android, UWP" Value="50" />
            </OnPlatform>

            <OnPlatform x:Key="rowHeight"
                        x:TypeArguments="x:Int32">
                <On Platform="iOS, Android, UWP" Value="60" />
            </OnPlatform>

            <local:DoubleToIntConverter x:Key="intConverter" />

        </ResourceDictionary>
    </ContentPage.Resources>

    <ListView ItemsSource="{x:Static local:NamedColor.All}"
              RowHeight="{StaticResource rowHeight}">
        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <StackLayout Padding="5, 5, 0, 5"
                                 Orientation="Horizontal"
                                 Spacing="15">

                        <BoxView WidthRequest="{StaticResource boxSize}"
                                 HeightRequest="{StaticResource boxSize}"
                                 Color="{Binding Color}" />

                        <StackLayout Padding="5, 0, 0, 0"
                                     VerticalOptions="Center">

                            <Label Text="{Binding FriendlyName}"
                                   FontAttributes="Bold"
                                   FontSize="Medium" />

                            <StackLayout Orientation="Horizontal"
                                         Spacing="0">
                                <Label Text="{Binding Color.R,
                                       Converter={StaticResource intConverter},
                                       ConverterParameter=255,
                                       StringFormat='R={0:X2}'}" />

                                <Label Text="{Binding Color.G,
                                       Converter={StaticResource intConverter},
                                       ConverterParameter=255,
                                       StringFormat=', G={0:X2}'}" />

                                <Label Text="{Binding Color.B,
                                       Converter={StaticResource intConverter},
                                       ConverterParameter=255,
                                       StringFormat=', B={0:X2}'}" />
                            </StackLayout>
                        </StackLayout>
                    </StackLayout>
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</ContentPage>
```

请注意，使用 `OnPlatform` 定义的大小 `BoxView` 和行的高度 `ListView` 。 尽管所有平台的值都是相同的，但可以轻松地改编标记以使其他值微调显示。

## <a name="binding-value-converters"></a>绑定值转换器

前面的 **ListView 演示** XAML 文件显示结构的单个 `R` 、 `G` 和 `B` 属性 Xamarin.Forms `Color` 。 这些属性的类型为 `double` ，范围为0到1。 如果希望显示十六进制值，则不能只使用 `StringFormat` 带有 "X2" 格式规范的。 这仅适用于整数，并且 `double` 值需要乘以255。

此小问题已通过 *值转换器*解决，也称为 *绑定转换器*。 这是实现接口的类 `IValueConverter` ，这意味着它有两个名为和的方法 `Convert` `ConvertBack` 。 `Convert`当值从源传输到目标时，将调用方法; `ConvertBack` 从目标到源的传输或绑定中将调用方法 `OneWayToSource` `TwoWay` ：

```csharp
using System;
using System.Globalization;
using Xamarin.Forms;

namespace XamlSamples
{
    class DoubleToIntConverter : IValueConverter
    {
        public object Convert(object value, Type targetType,
                              object parameter, CultureInfo culture)
        {
            double multiplier;

            if (!Double.TryParse(parameter as string, out multiplier))
                multiplier = 1;

            return (int)Math.Round(multiplier * (double)value);
        }

        public object ConvertBack(object value, Type targetType,
                                  object parameter, CultureInfo culture)
        {
            double divider;

            if (!Double.TryParse(parameter as string, out divider))
                divider = 1;

            return ((double)(int)value) / divider;
        }
    }
}
```

`ConvertBack`此方法不会在此程序中起作用，因为绑定只是源到目标的一种方法。

绑定使用属性引用绑定转换器 `Converter` 。 绑定转换器也可以接受使用属性指定的参数 `ConverterParameter` 。 对于某些通用性，这是乘数的指定方式。 绑定转换器检查转换器参数的值是否有效 `double` 。

转换器在资源字典中实例化，因此它可以在多个绑定之间共享：

```xaml
<local:DoubleToIntConverter x:Key="intConverter" />
```

三个数据绑定引用此单实例。 请注意， `Binding` 标记扩展包含嵌入的 `StaticResource` 标记扩展：

```xaml
<Label Text="{Binding Color.R,
                      Converter={StaticResource intConverter},
                      ConverterParameter=255,
                      StringFormat='R={0:X2}'}" />
```

结果如下：

[![绑定到具有 System.windows.datatemplate> 和转换器的集合](data-binding-basics-images/listview3.png)](data-binding-basics-images/listview3-large.png#lightbox)

`ListView`在处理可能会动态出现在基础数据中的更改时，它非常复杂，只是在执行某些步骤时。 如果在运行时中分配给的属性的项的集合 `ItemsSource` `ListView` （即，如果可以在集合中添加或移除项），请将 `ObservableCollection` 类用于这些项。 `ObservableCollection` 实现 `INotifyCollectionChanged` 接口，并 `ListView` 将安装该事件的处理程序 `CollectionChanged` 。

如果项的属性在运行时本身发生了更改，则集合中的项应实现 `INotifyPropertyChanged` 接口，并使用事件对属性值进行信号更改 `PropertyChanged` 。 本系列的下一部分中对此进行了说明，第 [5 部分。从数据绑定到 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)。

## <a name="summary"></a>总结

数据绑定提供一种功能强大的机制，用于在页面内的两个对象之间或在视觉对象与基础数据之间链接属性。 但当应用程序开始使用数据源时，常用的应用程序体系结构模式就会成为一种非常有用的范例。 第5部分对此进行了介绍 [。从数据绑定到 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)。

## <a name="related-links"></a>相关链接

- [XamlSamples](/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [第1部分。入门 XAML (示例) ](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [第2部分。基本 XAML 语法 (示例) ](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [第3部分。XAML 标记扩展 (示例) ](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [第5部分。从数据绑定到 MVVM (示例) ](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)