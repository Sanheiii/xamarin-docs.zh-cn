---
title: 第 2 部分。 基本 XAML 语法
description: 本文介绍属性元素和附加属性的基本 XAML 语法功能。
ms.prod: xamarin
ms.assetid: 4022F1DC-3802-4635-A553-688ABD3F0D5A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e8afb0aa2a36a73af6c63e48a6c1048d8b69cf90
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563973"
---
# <a name="part-2-essential-xaml-syntax"></a>第 2 部分。 基本 XAML 语法

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_XAML 主要用于实例化和初始化对象。但通常情况下，必须将属性设置为复杂对象，这些对象不能轻松地表示为 XML 字符串，有时必须在子类上设置一个类定义的属性。这两个需要需要属性元素和附加属性的基本 XAML 语法功能。_

## <a name="property-elements"></a>属性元素

在 XAML 中，类的属性通常设置为 XML 特性：

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large"
       TextColor="Aqua" />
```

但是，还可以通过其他方式在 XAML 中设置属性。 若要在中尝试这种替代方法 `TextColor` ，请先删除现有 `TextColor` 设置：

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large" />
```

通过将空元素 `Label` 标记分成开始和结束标记来打开空元素标记：

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">

</Label>
```

在这些标记中，添加包含类名的开始标记和结束标记，以及用句点分隔的属性名称：

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">
    <Label.TextColor>

    </Label.TextColor>
</Label>
```

将属性值设置为这些新标记的内容，如下所示：

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">
    <Label.TextColor>
        Aqua
    </Label.TextColor>
</Label>
```

这两种指定属性的方法 `TextColor` 在功能上等效，但不要为同一属性使用两种方式，因为这会有效地设置属性两次，并且可能不明确。

利用这一新语法，可以引入一些便利术语：

- `Label` 是一个  *对象元素*。 它是 Xamarin.Forms 表示为 XML 元素的对象。
- `Text`、  `VerticalOptions` `FontAttributes` 和  `FontSize` 是  *属性特性*。 它们是 Xamarin.Forms 表示为 XML 特性的属性。
- 在该最终代码段中， `TextColor` 已成为  *属性元素*。 它是一个 Xamarin.Forms 属性，但它现在是一个 XML 元素。

属性元素的定义可能最初似乎违反了 XML 语法，但不是这样。 该句点在 XML 中没有任何特殊含义。 对于 XML 解码器， `Label.TextColor` 只是一个普通的子元素。

但在 XAML 中，此语法非常特殊。 属性元素的规则之一是，标记中不会出现其他任何内容 `Label.TextColor` 。 属性的值始终定义为属性元素开始标记和结束标记之间的内容。

可对多个属性使用属性元素语法：

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center">
    <Label.FontAttributes>
        Bold
    </Label.FontAttributes>
    <Label.FontSize>
        Large
    </Label.FontSize>
    <Label.TextColor>
        Aqua
    </Label.TextColor>
</Label>
```

或者，可以对所有属性使用属性元素语法：

```xaml
<Label>
    <Label.Text>
        Hello, XAML!
    </Label.Text>
    <Label.FontAttributes>
        Bold
    </Label.FontAttributes>
    <Label.FontSize>
        Large
    </Label.FontSize>
    <Label.TextColor>
        Aqua
    </Label.TextColor>
    <Label.VerticalOptions>
        Center
    </Label.VerticalOptions>
</Label>
```

第一种方式是，属性元素语法可能与不必要的喋喋不休替换相对非常简单，并且在这种情况下，确实如此。

但是，当属性的值过于复杂以致无法表示为简单字符串时，属性元素语法就变得至关重要。 在属性元素标记中，可以实例化其他对象并设置其属性。 例如，可以使用属性设置将属性（例如）显式设置 `VerticalOptions` 为 `LayoutOptions` 值：

```xaml
<Label>
    ...
    <Label.VerticalOptions>
        <LayoutOptions Alignment="Center" />
    </Label.VerticalOptions>
</Label>
```

另一个示例： `Grid` 有两个名为和的属性 `RowDefinitions` `ColumnDefinitions` 。 这两个属性的类型为 `RowDefinitionCollection` 和 `ColumnDefinitionCollection` ，它们是 `RowDefinition` 和对象的集合 `ColumnDefinition` 。 需要使用属性元素语法来设置这些集合。

下面是类的 XAML 文件的开头 `GridDemoPage` ，其中显示了和集合的属性元素标记 `RowDefinitions` `ColumnDefinitions` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.GridDemoPage"
             Title="Grid Demo Page">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
            <RowDefinition Height="100" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="100" />
        </Grid.ColumnDefinitions>
        ...
    </Grid>
</ContentPage>
```

请注意用于定义自动调整的单元格、像素宽度和高度的单元格以及星形设置的简化语法。

## <a name="attached-properties"></a>附加属性

您刚才看到， `Grid` 需要和集合的属性元素 `RowDefinitions` `ColumnDefinitions` 来定义行和列。 不过，程序员还必须有一种方法来指示的每个子级所在的行和列 `Grid` 。

在的每个子级的标记中， `Grid` 使用以下属性指定该子元素的行和列：

- `Grid.Row`
- `Grid.Column`

这些属性的默认值为0。 您还可以指示子元素是否跨越多个具有以下属性的行或列：

- `Grid.RowSpan`
- `Grid.ColumnSpan`

这两个属性的默认值为1。

下面是完整的 GridDemoPage 文件：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.GridDemoPage"
             Title="Grid Demo Page">

    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
            <RowDefinition Height="100" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="100" />
        </Grid.ColumnDefinitions>

        <Label Text="Autosized cell"
               Grid.Row="0" Grid.Column="0"
               TextColor="White"
               BackgroundColor="Blue" />

        <BoxView Color="Silver"
                 HeightRequest="0"
                 Grid.Row="0" Grid.Column="1" />

        <BoxView Color="Teal"
                 Grid.Row="1" Grid.Column="0" />

        <Label Text="Leftover space"
               Grid.Row="1" Grid.Column="1"
               TextColor="Purple"
               BackgroundColor="Aqua"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />

        <Label Text="Span two rows (or more if you want)"
               Grid.Row="0" Grid.Column="2" Grid.RowSpan="2"
               TextColor="Yellow"
               BackgroundColor="Blue"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />

        <Label Text="Span two columns"
               Grid.Row="2" Grid.Column="0" Grid.ColumnSpan="2"
               TextColor="Blue"
               BackgroundColor="Yellow"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />

        <Label Text="Fixed 100x100"
               Grid.Row="2" Grid.Column="2"
               TextColor="Aqua"
               BackgroundColor="Red"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />

    </Grid>
</ContentPage>
```

`Grid.Row`和 `Grid.Column` 设置0不是必需的，但通常包括在内，目的是为了清晰起见。

如下所示：

[![网格布局](essential-xaml-syntax-images/griddemo.png)](essential-xaml-syntax-images/griddemo-large.png#lightbox)

仅从语法中判断，这些 `Grid.Row` 、 `Grid.Column` 、 `Grid.RowSpan` 和 `Grid.ColumnSpan` 特性似乎是的静态字段或属性 `Grid` ，但更有趣的是， `Grid` 不定义任何名为 `Row` 、、或的内容 `Column` `RowSpan` `ColumnSpan` 。

相反， `Grid` 定义了四个名为、和的可绑定属性 `RowProperty` `ColumnProperty` `RowSpanProperty` `ColumnSpanProperty` 。 这些是特殊类型的可绑定属性，称为 *附加属性*。 它们由类定义， `Grid` 但在的子级上设置 `Grid` 。

若要在代码中使用这些附加属性， `Grid` 该类提供了名为的静态方法，等等 `SetRow` `GetColumn` 。 但在 XAML 中，这些附加属性 `Grid` 使用简单属性名称设置为的子级中的特性。

在 XAML 文件中，附加属性始终可识别为包含以句点分隔的类和属性名称的属性。 它们被称为 *附加属性* ，因为在这种情况下，这些属性由一个类 (定义， `Grid`) 但附加到其他对象 (在此示例中，这是) 的子项 `Grid` 。 在布局过程中， `Grid` 可以询问这些附加属性的值，以了解在何处放置每个子元素。

`AbsoluteLayout`类定义了两个名为 `LayoutBounds` 和 `LayoutFlags` 的附加属性。 下面是使用的比例定位和大小调整功能实现的棋盘模式 `AbsoluteLayout` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.AbsoluteDemoPage"
             Title="Absolute Demo Page">

    <AbsoluteLayout BackgroundColor="#FF8080">
        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0.33, 0, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="1, 0, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0, 0.33, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0.67, 0.33, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0.33, 0.67, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="1, 0.67, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0, 1, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0.67, 1, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

  </AbsoluteLayout>
</ContentPage>
```

这里是：

[![绝对布局](essential-xaml-syntax-images/absolutedemo-large.png)](essential-xaml-syntax-images/absolutedemo-large.png#lightbox)

对于这种情况，您可能会问到使用 XAML 的智慧。 当然，矩形的重复和规律性表示 `LayoutBounds` 它在代码中可能更好地实现。

这无疑是一个合理的问题，在定义用户界面时，平衡使用代码和标记没有任何问题。 可以轻松地在 XAML 中定义一些视觉对象，然后使用代码隐藏文件的构造函数来添加一些更好的视觉对象，这些视觉对象在循环中可能更好。

## <a name="content-properties"></a>内容属性

在前面的示例中， `StackLayout` 、 `Grid` 和 `AbsoluteLayout` 对象被设置为 `Content` 的属性 `ContentPage` ，并且这些布局的子级实际上是集合中的项 `Children` 。 但 `Content` `Children` XAML 文件中没有这些和属性。

当然，您可以将 `Content` 和 `Children` 属性包含为属性元素，例如在 **XamlPlusCode** 示例中：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.XamlPlusCodePage"
             Title="XAML + Code Page">
    <ContentPage.Content>
        <StackLayout>
            <StackLayout.Children>
                <Slider VerticalOptions="CenterAndExpand"
                        ValueChanged="OnSliderValueChanged" />

                <Label x:Name="valueLabel"
                       Text="A simple Label"
                       FontSize="Large"
                       HorizontalOptions="Center"
                       VerticalOptions="CenterAndExpand" />

                <Button Text="Click Me!"
                      HorizontalOptions="Center"
                      VerticalOptions="CenterAndExpand"
                      Clicked="OnButtonClicked" />
            </StackLayout.Children>
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

真正的问题是：为什么 XAML 文件中 *不* 需要这些属性元素？

在中 Xamarin.Forms 为使用而定义的元素允许在类的特性中有一个属性标记 `ContentProperty` 。 如果在 `ContentPage` 联机文档中查找类 Xamarin.Forms ，则会看到以下属性：

```csharp
[Xamarin.Forms.ContentProperty("Content")]
public class ContentPage : TemplatedPage
```

这意味着 `Content` 不需要属性元素标记。 在开始标记和结束标记之间出现的任何 XML 内容 `ContentPage` 都假设分配给 `Content` 属性。

 `StackLayout`、 `Grid` 、 `AbsoluteLayout` 和 `RelativeLayout` 都派生自 `Layout<View>` ，如果你 `Layout<T>` 在文档中查找，  Xamarin.Forms 你将看到另一个 `ContentProperty` 属性：

```csharp
[Xamarin.Forms.ContentProperty("Children")]
public abstract class Layout<T> : Layout ...
```

这允许将布局内容自动添加到 `Children` 集合，而无需显式 `Children` 属性元素标记。

其他类还具有 `ContentProperty` 特性定义。 例如，的 content 属性 `Label` 为 `Text` 。 查看其他人的 API 文档。

## <a name="platform-differences-with-onplatform"></a>平台与 OnPlatform 的差异

在单页面应用程序中，通常会在 `Padding` 页面上设置属性，以避免覆盖 iOS 状态栏。 在代码中，可以使用 `Device.RuntimePlatform` 属性来实现此目的：

```csharp
if (Device.RuntimePlatform == Device.iOS)
{
    Padding = new Thickness(0, 20, 0, 0);
}
```

你还可以使用和类在 XAML 中执行类似操作 [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) [`On`](xref:Xamarin.Forms.On) 。 首先将属性的属性元素包含在 `Padding` 页面顶部附近：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>

    </ContentPage.Padding>
    ...
</ContentPage>
```

在这些标记中，包含 `OnPlatform` 标记。 `OnPlatform` 是一个泛型类。 需要指定泛型类型参数，在本例中为 `Thickness` `Padding` 属性类型。 幸运的是，有专门用于定义名为的泛型参数的 XAML 属性 `x:TypeArguments` 。 这应与要设置的属性的类型匹配：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">

        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

`OnPlatform` 具有一个名为 `Platforms` 的属性，它是 `IList` `On` 对象的。 使用该属性的属性元素标记：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <OnPlatform.Platforms>

            </OnPlatform.Platforms>
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

现在添加 `On` 元素。 对于每个属性，将属性和属性设置 `Platform` `Value` 为属性的标记 `Thickness` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <OnPlatform.Platforms>
                <On Platform="iOS" Value="0, 20, 0, 0" />
                <On Platform="Android" Value="0, 0, 0, 0" />
                <On Platform="UWP" Value="0, 0, 0, 0" />
            </OnPlatform.Platforms>
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

可以简化此标记。 的内容属性 `OnPlatform` 为 `Platforms` ，因此可以删除这些属性元素标记：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
            <On Platform="Android" Value="0, 0, 0, 0" />
            <On Platform="UWP" Value="0, 0, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

的 `Platform` 属性 `On` 的类型为 `IList<string>` ，因此，如果值相同，则可以包含多个平台：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
            <On Platform="Android, UWP" Value="0, 0, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

由于 Android 和 UWP 设置为的默认值，因此 `Padding` 可以删除此标记：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

这是在 XAML 中设置与平台相关的属性的标准方法 `Padding` 。 如果 `Value` 设置无法由单个字符串表示，则可以为其定义属性元素：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS">
                <On.Value>
                    0, 20, 0, 0
                </On.Value>
            </On>
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

> [!NOTE]
> `OnPlatform`标记扩展还可以在 XAML 中用于根据每个平台自定义 UI 外观。 它提供与和类相同的 `OnPlatform` 功能 `On` ，但具有更简洁的表示形式。 有关详细信息，请参阅 [OnPlatform 标记扩展](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension)。

## <a name="summary"></a>总结

对于属性元素和附加属性，已经建立了许多基本 XAML 语法。 但是，有时需要以间接方式（例如，从资源字典）将属性设置为对象。 下一部分第3部分介绍了此方法 [。XAML 标记扩展](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)。

## <a name="related-links"></a>相关链接

- [XamlSamples](/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [第1部分。与 XAML 入门](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [第3部分。XAML 标记扩展](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [第4部分。数据绑定基础知识](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [第5部分。从数据绑定到 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)