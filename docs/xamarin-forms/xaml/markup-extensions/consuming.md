---
title: 使用 XAML 标记扩展
description: 本文介绍如何 Xamarin.Forms 通过允许从各种源设置元素特性来使用 xaml 标记扩展来增强 xaml 的强大功能和灵活性。
ms.prod: xamarin
ms.assetid: CE686893-609C-4EC3-9225-6C68D2A9F79C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/17/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ee834bf7bae945536facb576ae26b9d13f5b7bcd
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91558942"
---
# <a name="consuming-xaml-markup-extensions"></a>使用 XAML 标记扩展

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)

XAML 标记扩展允许从多个源设置元素特性，从而帮助提高 XAML 的功能和灵活性。 Xaml 2009 规范包含若干 XAML 标记扩展。 它们显示在 XAML 文件中 `x` ，并采用常用的命名空间前缀，并且通常引用有此前缀。 本文介绍以下标记扩展：

- [`x:Static`](#xstatic-markup-extension) –引用静态属性、字段或枚举成员。
- [`x:Reference`](#xreference-markup-extension) –引用页上的命名元素。
- [`x:Type`](#xtype-markup-extension) –将属性设置为 `System.Type` 对象。
- [`x:Array`](#xarray-markup-extension) –构造特定类型的对象的数组。
- [`x:Null`](#xnull-markup-extension) –将属性设置为 `null` 值。
- [`OnPlatform`](#onplatform-markup-extension) –根据每个平台自定义 UI 外观。
- [`OnIdiom`](#onidiom-markup-extension) -基于运行应用程序的设备的用法自定义 UI 外观。
- [`DataTemplate`](#datatemplate-markup-extension) –将类型转换为 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 。
- [`FontImage`](#fontimage-markup-extension) –在可以显示的任何视图中显示字体图标 `ImageSource` 。
- [`AppThemeBinding`](#appthemebinding-markup-extension) –根据当前系统主题使用资源。

其他 xaml 实现一直以来已支持其他 XAML 标记扩展，并且也支持 Xamarin.Forms 。 其他文章中更全面地描述了这些内容：

- `StaticResource` -从资源字典引用对象，如  [**资源**](~/xamarin-forms/xaml/resource-dictionaries.md)字典一文中所述。
- `DynamicResource` -响应资源字典中对象的更改，如 [**动态样式**](~/xamarin-forms/user-interface/styles/dynamic.md)一文中所述。
- `Binding` -在两个对象的属性之间建立链接，如 [**数据绑定**](~/xamarin-forms/app-fundamentals/data-binding/index.md)一文中所述。
- `TemplateBinding`-从控件模板执行数据绑定，如文章[** Xamarin.Forms 控件模板**](~/xamarin-forms/app-fundamentals/templates/control-template.md)中所述。
- `RelativeSource` -设置相对于绑定目标位置的绑定源，如 [相关绑定](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md)一文中所述。

[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)布局使用自定义标记扩展 [`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression) 。 [**RelativeLayout**](~/xamarin-forms/user-interface/layouts/relativelayout.md)一文中介绍了此标记扩展。

## <a name="xstatic-markup-extension"></a>x:Static 标记扩展

`x:Static`类支持标记扩展 [`StaticExtension`](xref:Xamarin.Forms.Xaml.StaticExtension) 。 类具有一个名为 [`Member`](xref:Xamarin.Forms.Xaml.StaticExtension.Member) 的类型的属性 `string` ，该属性设置为公共常量、静态属性、静态字段或枚举成员的名称。

一种常用的方法 `x:Static` 是先定义具有一些常量或静态变量的类，如 `AppConstants` [**MarkupExtensions**](/samples/xamarin/xamarin-forms-samples/xaml-markupextensions) 程序中的此类小类：

```csharp
static class AppConstants
{
    public static double NormalFontSize = 18;
}
```

**X:Static 演示**页演示了使用标记扩展的几种方法 `x:Static` 。 最详细的方法实例化 `StaticExtension` `Label.FontSize` 属性元素标记之间的类：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:sys="clr-namespace:System;assembly=netstandard"
             xmlns:local="clr-namespace:MarkupExtensions"
             x:Class="MarkupExtensions.StaticDemoPage"
             Title="x:Static Demo">
    <StackLayout Margin="10, 0">
        <Label Text="Label No. 1">
            <Label.FontSize>
                <x:StaticExtension Member="local:AppConstants.NormalFontSize" />
            </Label.FontSize>
        </Label>

        ···

    </StackLayout>
</ContentPage>
```

XAML 分析器还允许 `StaticExtension` 将类缩写为 `x:Static` ：

```xaml
<Label Text="Label No. 2">
    <Label.FontSize>
        <x:Static Member="local:AppConstants.NormalFontSize" />
    </Label.FontSize>
</Label>
```

这还可以进一步简化，但更改会引入一些新语法：它包含将 `StaticExtension` 类和成员设置置于大括号中。 生成的表达式直接设置为 `FontSize` 属性：

```xaml
<Label Text="Label No. 3"
       FontSize="{x:StaticExtension Member=local:AppConstants.NormalFontSize}" />
```

请注意，大括号内*没有引号。* 的 `Member` 属性 `StaticExtension` 不再是 XML 特性。 它是标记扩展的表达式的一部分。

正如在 `x:StaticExtension` `x:Static` 将其用作对象元素时可以缩写为，还可以在大括号内的表达式中对其进行缩写：

```xaml
<Label Text="Label No. 4"
       FontSize="{x:Static Member=local:AppConstants.NormalFontSize}" />
```

`StaticExtension`类具有 `ContentProperty` 引用属性的特性，该属性 `Member` 将此属性标记为类的默认内容属性。 对于用大括号表示的 XAML 标记扩展，可以消除 `Member=` 表达式的一部分：

```xaml
<Label Text="Label No. 5"
       FontSize="{x:Static local:AppConstants.NormalFontSize}" />
```

这是最常见的 `x:Static` 标记扩展形式。

" **静态演示** " 页包含两个其他示例。 XAML 文件的根标记包含 .NET 命名空间的 XML 命名空间声明 `System` ：

```xaml
xmlns:sys="clr-namespace:System;assembly=netstandard"
```

这允许将 `Label` 字体大小设置为静态字段 `Math.PI` 。 这会导致小文本，因此 `Scale` 属性设置为 `Math.E` ：

```xaml
<Label Text="&#x03C0; &#x00D7; E sized text"
       FontSize="{x:Static sys:Math.PI}"
       Scale="{x:Static sys:Math.E}"
       HorizontalOptions="Center" />
```

最后一个示例显示 `Device.RuntimePlatform` 值。 `Environment.NewLine`静态属性用于在两个对象之间插入一个换行符 `Span` ：

```xaml
<Label HorizontalTextAlignment="Center"
       FontSize="{x:Static local:AppConstants.NormalFontSize}">
    <Label.FormattedText>
        <FormattedString>
            <Span Text="Runtime Platform: " />
            <Span Text="{x:Static sys:Environment.NewLine}" />
            <Span Text="{x:Static Device.RuntimePlatform}" />
        </FormattedString>
    </Label.FormattedText>
</Label>
```

下面是运行的示例：

[![x:Static 演示](consuming-images/staticdemo-small.png "x:Static 演示")](consuming-images/staticdemo-large.png#lightbox "x:Static 演示")

## <a name="xreference-markup-extension"></a>x:Reference 标记扩展

`x:Reference`类支持标记扩展 [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension) 。 类具有一个名为 [`Name`](xref:Xamarin.Forms.Xaml.ReferenceExtension.Name) 的类型的属性 `string` ，该属性将设置为页面上已为其指定名称的元素的名称 `x:Name` 。 此 `Name` 属性是的 content 属性 `ReferenceExtension` ，因此在 `Name=` `x:Reference` 大括号中显示时不需要此属性。

`x:Reference`标记扩展将专门用于数据绑定，这将在[**数据绑定**](~/xamarin-forms/app-fundamentals/data-binding/index.md)一文中更详细地介绍。

" **X:Reference 演示** " 页显示了两种用于 `x:Reference` 数据绑定的，第一种用于设置对象的 `Source` 属性，第 `Binding` 二种用于设置 `BindingContext` 两个数据绑定的属性：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.ReferenceDemoPage"
             x:Name="page"
             Title="x:Reference Demo">

    <StackLayout Margin="10, 0">

        <Label Text="{Binding Source={x:Reference page},
                              StringFormat='The type of this page is {0}'}"
               FontSize="18"
               VerticalOptions="CenterAndExpand"
               HorizontalTextAlignment="Center" />

        <Slider x:Name="slider"
                Maximum="360"
                VerticalOptions="Center" />

        <Label BindingContext="{x:Reference slider}"
               Text="{Binding Value, StringFormat='{0:F0}&#x00B0; rotation'}"
               Rotation="{Binding Value}"
               FontSize="24"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

    </StackLayout>
</ContentPage>
```

这两个 `x:Reference` 表达式都使用类名的缩写形式 `ReferenceExtension` 并消除 `Name=` 表达式的部分。 在第一个示例中， `x:Reference` 标记扩展嵌入在 `Binding` 标记扩展中。 请注意， `Source` 和 `StringFormat` 设置由逗号分隔。 下面是正在运行的程序：

[![x:Reference 演示](consuming-images/referencedemo-small.png "x:Reference 演示")](consuming-images/referencedemo-large.png#lightbox "x:Reference 演示")

## <a name="xtype-markup-extension"></a>x:Type 标记扩展

`x:Type`标记扩展是等效于 c # 关键字的 XAML [`typeof`](/dotnet/csharp/language-reference/keywords/typeof/) 。 类支持它 [`TypeExtension`](xref:Xamarin.Forms.Xaml.TypeExtension) ，该类定义一个名为的类型的属性， [`TypeName`](xref:Xamarin.Forms.Xaml.TypeExtension.TypeName) 该属性 `string` 被设置为类或结构名称。 `x:Type`标记扩展返回 [`System.Type`](xref:System.Type) 该类或结构的对象。 `TypeName` 是的 content 属性 `TypeExtension` ，因此 `TypeName=` 当 `x:Type` 带有大括号时不需要。

在中 Xamarin.Forms ，有多个具有类型的参数的属性 `Type` 。 示例包括的 [`TargetType`](xref:Xamarin.Forms.Style.TargetType) 属性 `Style` ，以及用于指定泛型类中的参数的 [x:TypeArguments](~/xamarin-forms/xaml/passing-arguments.md#specifying-a-generic-type-argument) 特性。 但 XAML 分析器 `typeof` 会自动执行操作，并且 `x:Type` 不会在这些情况下使用标记扩展。

需要一个位置 `x:Type` *，其中* 包含 `x:Array` 标记扩展， [下一节](#xarray-markup-extension)将对此进行介绍。

`x:Type`当构造菜单，其中每个菜单项对应于特定类型的对象时，标记扩展也很有用。 可以将 `Type` 对象与每个菜单项相关联，然后在选定菜单项时实例化对象。

这就是 `MainPage` **标记扩展** 程序中的导航菜单的工作方式。 **MainPage**文件包含，其中 `TableView` 每个 `TextCell` 对应于程序中的特定页面：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:MarkupExtensions"
             x:Class="MarkupExtensions.MainPage"
             Title="Markup Extensions"
             Padding="10">
    <TableView Intent="Menu">
        <TableRoot>
            <TableSection>
                <TextCell Text="x:Static Demo"
                          Detail="Access constants or statics"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:StaticDemoPage}" />

                <TextCell Text="x:Reference Demo"
                          Detail="Reference named elements on the page"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:ReferenceDemoPage}" />

                <TextCell Text="x:Type Demo"
                          Detail="Associate a Button with a Type"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:TypeDemoPage}" />

                <TextCell Text="x:Array Demo"
                          Detail="Use an array to fill a ListView"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:ArrayDemoPage}" />

                ···                          

        </TableRoot>
    </TableView>
</ContentPage>
```

下面是 **标记扩展**中的打开主页：

[![主页](consuming-images/mainpage-small.png "主页")](consuming-images/mainpage-large.png#lightbox "主页")

每个 `CommandParameter` 属性都设置为一个 `x:Type` 引用其他页面的标记扩展。 `Command`属性绑定到名为的属性 `NavigateCommand` 。 此属性在 `MainPage` 代码隐藏文件中定义：

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();

        NavigateCommand = new Command<Type>(async (Type pageType) =>
        {
            Page page = (Page)Activator.CreateInstance(pageType);
            await Navigation.PushAsync(page);
        });

        BindingContext = this;
    }

    public ICommand NavigateCommand { private set; get; }
}
```

`NavigateCommand`属性是一个 `Command` 对象，该对象实现具有类型为的参数的执行命令 `Type` &mdash; `CommandParameter` 。 方法使用 `Activator.CreateInstance` 来实例化页面，然后导航到该页面。 构造函数通过将页的设置 `BindingContext` 为其自身来结束，这使 `Binding` 上的能够 `Command` 正常工作。 有关此类代码的更多详细信息，请参阅 [**数据绑定**](~/xamarin-forms/app-fundamentals/data-binding/index.md) 一文和 [**命令**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md) 项目。

**X:Type 演示**页使用类似的技术来实例化 Xamarin.Forms 元素，并将其添加到 `StackLayout` 中。 XAML 文件最初由三个元素组成， `Button` 其 `Command` 属性设置为 `Binding` ， `CommandParameter` 属性设置为三个 Xamarin.Forms 视图类型：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.TypeDemoPage"
             Title="x:Type Demo">

    <StackLayout x:Name="stackLayout"
                 Padding="10, 0">

        <Button Text="Create a Slider"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                Command="{Binding CreateCommand}"
                CommandParameter="{x:Type Slider}" />

        <Button Text="Create a Stepper"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                Command="{Binding CreateCommand}"
                CommandParameter="{x:Type Stepper}" />

        <Button Text="Create a Switch"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                Command="{Binding CreateCommand}"
                CommandParameter="{x:Type Switch}" />
    </StackLayout>
</ContentPage>
```

代码隐藏文件定义并初始化 `CreateCommand` 属性：

```csharp
public partial class TypeDemoPage : ContentPage
{
    public TypeDemoPage()
    {
        InitializeComponent();

        CreateCommand = new Command<Type>((Type viewType) =>
        {
            View view = (View)Activator.CreateInstance(viewType);
            view.VerticalOptions = LayoutOptions.CenterAndExpand;
            stackLayout.Children.Add(view);
        });

        BindingContext = this;
    }

    public ICommand CreateCommand { private set; get; }
}
```

按下时所执行的方法会 `Button` 创建自变量的新实例，并设置其 `VerticalOptions` 属性，并将其添加到中 `StackLayout` 。 然后，这三个 `Button` 元素通过动态创建的视图来共享页面：

[![x:Type 演示](consuming-images/typedemo-small.png "x:Type 演示")](consuming-images/typedemo-large.png#lightbox "x:Type 演示")

## <a name="xarray-markup-extension"></a>x:Array 标记扩展

`x:Array`标记扩展使你能够在标记中定义数组。 [`ArrayExtension`](xref:Xamarin.Forms.Xaml.ArrayExtension)该类支持定义两个属性的类：

- `Type` 类型的 `Type` ，指示数组中元素的类型。
- `Items` 类型 `IList` 为的，它是项本身的集合。 这是的内容属性 `ArrayExtension` 。

`x:Array`标记扩展本身从不出现在大括号中。 相反， `x:Array` 开始标记和结束标记用于分隔项列表。 将 `Type` 属性设置为 `x:Type` 标记扩展。

**X:Array 演示**页演示了如何通过将 `x:Array` `ListView` 属性设置为数组来使用向添加项 `ItemsSource` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.ArrayDemoPage"
             Title="x:Array Demo Page">
    <ListView Margin="10">
        <ListView.ItemsSource>
            <x:Array Type="{x:Type Color}">
                <Color>Aqua</Color>
                <Color>Black</Color>
                <Color>Blue</Color>
                <Color>Fuchsia</Color>
                <Color>Gray</Color>
                <Color>Green</Color>
                <Color>Lime</Color>
                <Color>Maroon</Color>
                <Color>Navy</Color>
                <Color>Olive</Color>
                <Color>Pink</Color>
                <Color>Purple</Color>
                <Color>Red</Color>
                <Color>Silver</Color>
                <Color>Teal</Color>
                <Color>White</Color>
                <Color>Yellow</Color>
            </x:Array>
        </ListView.ItemsSource>

        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <BoxView Color="{Binding}"
                             Margin="3" />    
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</ContentPage>        
```

`ViewCell` `BoxView` 为每个颜色项创建一个简单的：

[![x:Array 演示](consuming-images/arraydemo-small.png "x:Array 演示")](consuming-images/arraydemo-large.png#lightbox "x:Array 演示")

可以通过多种方式指定 `Color` 此数组中的各个项。 您可以使用 `x:Static` 标记扩展：

```xaml
<x:Static Member="Color.Blue" />
```

或者，您可以使用 `StaticResource` 从资源字典中检索颜色：

```xaml
<StaticResource Key="myColor" />
```

在本文末尾，你将看到一个自定义 XAML 标记扩展，它还会创建一个新的颜色值：

```xaml
<local:HslColor H="0.5" S="1.0" L="0.5" />
```

定义常见类型（如字符串或数字）的数组时，请使用 [**传递构造函数参数**](~/xamarin-forms/xaml/passing-arguments.md#passing-constructor-arguments) 一文中列出的标记来分隔值。

## <a name="xnull-markup-extension"></a>x:Null 标记扩展

`x:Null`类支持标记扩展 [`NullExtension`](xref:Xamarin.Forms.Xaml.NullExtension) 。 它没有属性，只是 XAML 等效的 c # [`null`](/dotnet/csharp/language-reference/keywords/null/) 关键字。

`x:Null`标记扩展很少需要并且很少使用，但如果确实需要它，则很高兴您已经存在。

**X:Null 演示**页说明了一种 `x:Null` 很方便的方案。 假设您为指定了一个隐式，其中 `Style` `Label` 包括将 `Setter` `FontFamily` 属性设置为与平台相关的系列名称的：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.NullDemoPage"
             Title="x:Null Demo">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="FontSize" Value="48" />
                <Setter Property="FontFamily">
                    <Setter.Value>
                        <OnPlatform x:TypeArguments="x:String">
                            <On Platform="iOS" Value="Times New Roman" />
                            <On Platform="Android" Value="serif" />
                            <On Platform="UWP" Value="Times New Roman" />
                        </OnPlatform>
                    </Setter.Value>
                </Setter>
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <ContentPage.Content>
        <StackLayout Padding="10, 0">
            <Label Text="Text 1" />
            <Label Text="Text 2" />

            <Label Text="Text 3"
                   FontFamily="{x:Null}" />

            <Label Text="Text 4" />
            <Label Text="Text 5" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>   
```

然后，你会发现，对于其中一个 `Label` 元素，你希望除之外的隐式中的所有属性设置都 `Style` `FontFamily` 是默认值。 您可以 `Style` 为此目的定义另一个方法，但更简单的方法是将特定的的 `FontFamily` 属性设置 `Label` 为 `x:Null` ，如中心中所示 `Label` 。

下面是正在运行的程序：

[![x:Null 演示](consuming-images/nulldemo-small.png "x:Null 演示")](consuming-images/nulldemo-large.png#lightbox "x:Null 演示")

请注意，四个 `Label` 元素具有衬线字体，但中心 `Label` 具有默认的 sans-serif 字体。

## <a name="onplatform-markup-extension"></a>OnPlatform 标记扩展

通过 `OnPlatform` 标记扩展可基于每个平台自定义 UI 外观。 它提供与和类相同的 [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) 功能 [`On`](xref:Xamarin.Forms.On) ，但具有更简洁的表示形式。

`OnPlatform`类支持标记扩展 [`OnPlatformExtension`](xref:Xamarin.Forms.Xaml.OnPlatformExtension) ，该类定义以下属性：

- `Default` 类型为的 `object` ，它设置为应用于表示平台的属性的默认值。
- `Android` 类型为的 `object` ，它设置为要在 Android 上应用的值。
- `GTK` 类型为的 `object` ，它设置为要在 GTK 平台上应用的值。
- `iOS` 类型为的 `object` ，它设置为要在 iOS 上应用的值。
- `macOS` 类型为的 `object` ，它设置为要在 macOS 上应用的值。
- `Tizen` 类型为的 `object` ，它设置为要在 Tizen 平台上应用的值。
- `UWP` 类型为的 `object` ，它设置为要在通用 Windows 平台上应用的值。
- `WPF` 类型为的 `object` ，它设置为要在 Windows Presentation Foundation 平台上应用的值。
- `Converter` 类型的 `IValueConverter` ，可以设置为 `IValueConverter` 实现。
- `ConverterParameter` 类型的 `object` ，可设置为要传递到实现的值 `IValueConverter` 。

> [!NOTE]
> XAML 分析器允许 [`OnPlatformExtension`](xref:Xamarin.Forms.Xaml.OnPlatformExtension) 将类缩写为 `OnPlatform` 。

`Default`属性是的内容属性 `OnPlatformExtension` 。 因此，对于用大括号表示的 XAML 标记表达式，可以消除 `Default=` 表达式的部分，前提是它是第一个参数。 如果 `Default` 未设置该属性，则它将默认为 [`BindableProperty.DefaultValue`](xref:Xamarin.Forms.BindableProperty.DefaultValue) 属性值，前提是标记扩展的目标为 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 。

> [!IMPORTANT]
> XAML 分析器要求向使用标记扩展的属性提供正确类型的值 `OnPlatform` 。 如果类型转换是必需的，则 `OnPlatform` 标记扩展将尝试使用所提供的默认转换器来执行该转换 Xamarin.Forms 。 但是，某些类型转换无法由默认转换器执行，在这些情况下， `Converter` 应将属性设置为 `IValueConverter` 实现。

**OnPlatform 演示**页显示了如何使用 `OnPlatform` 标记扩展：

```xaml
<BoxView Color="{OnPlatform Yellow, iOS=Red, Android=Green, UWP=Blue}"
         WidthRequest="{OnPlatform 250, iOS=200, Android=300, UWP=400}"  
         HeightRequest="{OnPlatform 250, iOS=200, Android=300, UWP=400}"
         HorizontalOptions="Center" />
```

在此示例中，所有三个 `OnPlatform` 表达式都使用类名称的缩写形式 `OnPlatformExtension` 。 这三个 `OnPlatform` 标记扩展将的 [`Color`](xref:Xamarin.Forms.BoxView.Color) 、 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 和 [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) 属性设置 [`BoxView`](xref:Xamarin.Forms.BoxView) 为 iOS、Android 和 UWP 上不同的值。 标记扩展还为未指定的平台上的这些属性提供默认值，同时消除表达式的 `Default=` 部分。 请注意，设置的标记扩展属性用逗号分隔。

下面是正在运行的程序：

[![OnPlatform 演示](consuming-images/onplatformdemo-small.png "OnPlatform 演示")](consuming-images/onplatformdemo-large.png#lightbox "OnPlatform 演示")

## <a name="onidiom-markup-extension"></a>OnIdiom 标记扩展

`OnIdiom`标记扩展使你可以基于运行应用程序的设备的用法来自定义 UI 外观。 [`OnIdiomExtension`](xref:Xamarin.Forms.Xaml.OnIdiomExtension)该类由类支持，该类定义以下属性：

- `Default` 类型为的 `object` ，它设置为应用于表示设备惯例的属性的默认值。
- `Phone` 类型为的 `object` ，它设置为要在手机上应用的值。
- `Tablet` 类型为的 `object` ，它设置为要在平板电脑上应用的值。
- `Desktop` 类型为的 `object` ，它设置为要在桌面平台上应用的值。
- `TV` 类型为的 `object` ，它设置为要在电视平台上应用的值。
- `Watch` 类型为的 `object` ，它设置为要在监视平台上应用的值。
- `Converter` 类型的 `IValueConverter` ，可以设置为 `IValueConverter` 实现。
- `ConverterParameter` 类型的 `object` ，可设置为要传递到实现的值 `IValueConverter` 。

> [!NOTE]
> XAML 分析器允许 [`OnIdiomExtension`](xref:Xamarin.Forms.Xaml.OnIdiomExtension) 将类缩写为 `OnIdiom` 。

`Default`属性是的内容属性 `OnIdiomExtension` 。 因此，对于用大括号表示的 XAML 标记表达式，可以消除 `Default=` 表达式的部分，前提是它是第一个参数。

> [!IMPORTANT]
> XAML 分析器要求向使用标记扩展的属性提供正确类型的值 `OnIdiom` 。 如果类型转换是必需的，则 `OnIdiom` 标记扩展将尝试使用所提供的默认转换器来执行该转换 Xamarin.Forms 。 但是，某些类型转换无法由默认转换器执行，在这些情况下， `Converter` 应将属性设置为 `IValueConverter` 实现。

**OnIdiom 演示**页显示了如何使用 `OnIdiom` 标记扩展：

```xaml
<BoxView Color="{OnIdiom Yellow, Phone=Red, Tablet=Green, Desktop=Blue}"
         WidthRequest="{OnIdiom 100, Phone=200, Tablet=300, Desktop=400}"
         HeightRequest="{OnIdiom 100, Phone=200, Tablet=300, Desktop=400}"
         HorizontalOptions="Center" />
```

在此示例中，所有三个 `OnIdiom` 表达式都使用类名称的缩写形式 `OnIdiomExtension` 。 这三个 `OnIdiom` 标记扩展将的 [`Color`](xref:Xamarin.Forms.BoxView.Color) 、 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 和 [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) 属性设置 [`BoxView`](xref:Xamarin.Forms.BoxView) 为手机、平板电脑和桌面惯例上不同的值。 标记扩展还为未指定的惯例中的这些属性提供默认值，同时消除表达式的 `Default=` 部分。 请注意，设置的标记扩展属性用逗号分隔。

下面是正在运行的程序：

[![OnIdiom 演示](consuming-images/onidiomdemo-small.png "OnIdiom 演示")](consuming-images/onidiomdemo-large.png#lightbox "OnIdiom 演示")

## <a name="datatemplate-markup-extension"></a>System.windows.datatemplate> 标记扩展

`DataTemplate`标记扩展使你能够将类型转换为 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 。 `DataTemplateExtension`类支持该类，该类定义类型为的 `TypeName` 属性，该属性 `string` 被设置为要转换为的类型的名称 `DataTemplate` 。 `TypeName`属性是的内容属性 `DataTemplateExtension` 。 因此，对于用大括号表示的 XAML 标记表达式，可以删除这类表达式的 `TypeName=` 部分。

> [!NOTE]
> XAML 分析程序允许将 `DataTemplateExtension` 类缩写为 `DataTemplate`。

此标记扩展的典型用法是在 Shell 应用程序中，如下面的示例中所示：

```xaml
<ShellContent Title="Monkeys"
              Icon="monkey.png"
              ContentTemplate="{DataTemplate views:MonkeysPage}" />
```

在此示例中， `MonkeysPage` 从转换为 [`ContentPage`](xref:Xamarin.Forms.ContentPage) [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ，它设置为属性的值 `ShellContent.ContentTemplate` 。 这可确保 `MonkeysPage` 仅在导航到页面时创建，而不是在应用程序启动时创建。

有关 Shell 应用程序的详细信息，请参阅[ Xamarin.Forms shell](~/xamarin-forms/app-fundamentals/shell/index.md)。

## <a name="fontimage-markup-extension"></a>FontImage 标记扩展

`FontImage`标记扩展使你能够在可以显示的任何视图中显示字体图标 `ImageSource` 。 它提供与类相同的功能 `FontImageSource` ，但具有更简洁的表示形式。

`FontImageExtension` 类支持 `FontImage` 标记扩展，并定义以下属性：

- `FontFamily` 类型的 `string` ，字体图标所属的字体系列。
- `Glyph` 类型的 `string` ，字体图标的 unicode 字符值。
- `Color` 类型 [`Color`](xref:Xamarin.Forms.Color) 为，显示字体图标时使用的颜色。
- `Size` 在呈现的字体图标的类型中， `double` 以与设备无关的单位表示的大小。 默认值为 30。 此外，此属性可以设置为命名字体大小。

> [!NOTE]
> XAML 分析程序允许将 `FontImageExtension` 类缩写为 `FontImage`。

`Glyph`属性是的内容属性 `FontImageExtension` 。 因此，对于用大括号表示的 XAML 标记表达式，可以消除 `Glyph=` 表达式的部分，前提是它是第一个参数。

**FontImage 演示**页显示了如何使用 `FontImage` 标记扩展：

```xaml
<Image BackgroundColor="#D1D1D1"
       Source="{FontImage &#xf30c;, FontFamily={OnPlatform iOS=Ionicons, Android=ionicons.ttf#}, Size=44}" />
```

在此示例中，类名称的缩写形式 `FontImageExtension` 用于显示 Ionicons 字体系列中的 XBox 图标 [`Image`](xref:Xamarin.Forms.Image) 。 表达式还使用 `OnPlatform` 标记扩展 `FontFamily` 在 IOS 和 Android 上指定不同的属性值。 此外，还 `Glyph=` 消除了表达式的部分，而设置的标记扩展属性则用逗号分隔。 请注意，当图标的 unicode 字符为时 `\uf30c` ，它必须在 XAML 中进行转义，因此将变为 `&#xf30c;` 。

下面是正在运行的程序：

[![FontImage 标记扩展的屏幕截图](consuming-images/fontimagedemo.png "FontImage 演示")](consuming-images/fontimagedemo-large.png#lightbox "FontImage 演示")

有关通过在对象中指定字体图标数据来显示字体图标的信息 `FontImageSource` ，请参阅 [显示字体图标](~/xamarin-forms/user-interface/text/fonts.md#display-font-icons)。

## <a name="appthemebinding-markup-extension"></a>AppThemeBinding 标记扩展

`AppThemeBinding`标记扩展使你能够根据当前系统主题指定要使用的资源（如图像或颜色）。

> [!IMPORTANT]
> `AppThemeBinding`标记扩展具有最低操作系统要求。 有关详细信息，请参阅 [响应 Xamarin.Forms 应用程序中的系统主题更改](~/xamarin-forms/user-interface/theming/system-theme-changes.md)。

`AppThemeBindingExtension` 类支持 `AppThemeBinding` 标记扩展，并定义以下属性：

- `Default`，类型为 `object` ，默认情况下，你设置为要使用的资源。
- `Light`，类型为 `object` ，在设备使用光源主题时，将设置为要使用的资源。
- `Dark`，类型为 `object` ，在设备使用其深色主题时，将设置为要使用的资源。
- `Value`，类型为 `object` ，返回当前正在由标记扩展使用的资源。

> [!NOTE]
> XAML 分析程序允许将 `AppThemeBindingExtension` 类缩写为 `AppBindingTheme`。

`Default`属性是的内容属性 `AppThemeBindingExtension` 。 因此，对于用大括号表示的 XAML 标记表达式，可以消除 `Default=` 表达式的部分，前提是它是第一个参数。

**AppThemeBinding 演示**页显示了如何使用 `AppThemeBinding` 标记扩展：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.AppThemeBindingDemoPage"
             Title="AppThemeBinding Demo">
    <ContentPage.Resources>

        <Style x:Key="labelStyle"
               TargetType="Label">
            <Setter Property="TextColor"
                    Value="{AppThemeBinding Black, Light=Blue, Dark=Teal}" />
        </Style>

    </ContentPage.Resources>
    <StackLayout Margin="20">
        <Label Text="This text is green in light mode, and red in dark mode."
               TextColor="{AppThemeBinding Light=Green, Dark=Red}" />
        <Label Text="This text is black by default, blue in light mode, and teal in dark mode."
               Style="{StaticResource labelStyle}" />
    </StackLayout>
</ContentPage>
```

在此示例中， [`Label`](xref:Xamarin.Forms.Label) 当设备使用光源主题时，第一个的文本颜色设置为绿色; 当设备使用其深主题时，将设置为红色。 第二个 `Label` [`TextColor`](xref:Xamarin.Forms.Label.TextColor) 属性通过设置 [`Style`](xref:Xamarin.Forms.Style) 。 默认情况下，这会将 `Style` 的文本颜色设置为 `Label` 黑色; 当设备使用光源主题时，将设置为蓝色; 当设备使用其深主题时，将设置为蓝绿色。

下面是正在运行的程序：

![AppThemeBinding 演示](consuming-images/appthemebindingdemo.png "AppThemeBinding 演示")

## <a name="define-markup-extensions"></a>定义标记扩展

如果你遇到了在中不可用的 XAML 标记扩展的需要 Xamarin.Forms ，则可以 [创建自己](creating.md)的扩展。

## <a name="related-links"></a>相关链接

- [标记扩展 (示例) ](/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)
- [书籍中的 XAML 标记扩展章节 Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
- [资源字典](~/xamarin-forms/xaml/resource-dictionaries.md)
- [动态样式](~/xamarin-forms/user-interface/styles/dynamic.md)
- [数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Xamarin.Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md)
- [响应应用程序中的系统主题更改 Xamarin.Forms](~/xamarin-forms/user-interface/theming/system-theme-changes.md)