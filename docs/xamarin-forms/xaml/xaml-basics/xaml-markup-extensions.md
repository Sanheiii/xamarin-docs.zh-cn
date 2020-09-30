---
title: 第 3 部分。 XAML 标记扩展
description: XAML 标记扩展在 XAML 中构成了一项重要功能，该功能允许将属性设置为间接从其他源引用的对象或值。
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F4A37564-B18B-42FF-B841-9A1949895AB6
author: davidbritch
ms.author: dabritch
ms.date: 03/27/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9923a7f0605a7ca5d9d81269c3d855e92fbccaf4
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91564077"
---
# <a name="part-3-xaml-markup-extensions"></a>第 3 部分。 XAML 标记扩展

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_XAML 标记扩展在 XAML 中构成了一项重要功能，该功能允许将属性设置为间接从其他源引用的对象或值。XAML 标记扩展对于共享对象和引用在整个应用程序中使用的常量尤其重要，但它们在数据绑定中找到了最大的实用程序。_

## <a name="xaml-markup-extensions"></a>XAML 标记扩展

通常，使用 XAML 将对象的属性设置为显式值（如字符串、数字、枚举成员）或转换为后台值的字符串。

但是，有时属性必须引用在其他位置定义的值，或者在运行时代码可能需要少量处理。 为此，可以使用 XAML *标记扩展* 。

这些 XAML 标记扩展不是 XML 的扩展。 XAML 是完全合法的 XML。 它们被称为 "扩展"，因为它们由实现的类中的代码支持 `IMarkupExtension` 。 您可以编写自己的自定义标记扩展。

在许多情况下，xaml 标记扩展会立即在 XAML 文件中识别，因为它们显示为用大括号（{和}）分隔的特性设置，但有时标记扩展作为传统元素出现在标记中。

## <a name="shared-resources"></a>共享资源

某些 XAML 页包含多个属性设置为相同值的视图。 例如，这些对象的许多属性设置 `Button` 都是相同的：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <StackLayout>
        <Button Text="Do this!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                BorderWidth="3"
                Rotation="-15"
                TextColor="Red"
                FontSize="24" />

        <Button Text="Do that!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                BorderWidth="3"
                Rotation="-15"
                TextColor="Red"
                FontSize="24" />

        <Button Text="Do the other thing!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                BorderWidth="3"
                Rotation="-15"
                TextColor="Red"
                FontSize="24" />

    </StackLayout>
</ContentPage>
```

如果需要更改这些属性中的一个，则最好只进行一次更改而不是三次更改。 如果这是代码，你可能会使用常量和静态只读对象来帮助保持此类值的一致性和修改。

在 XAML 中，最常见的解决方案是将此类值或对象存储在 *资源字典*中。 `VisualElement`类定义一个 `Resources` 类型为的属性 `ResourceDictionary` ，该属性是一个包含类型的键 `string` 和类型的值的字典 `object` 。 你可以将对象放入此字典，然后在 XAML 中从标记中引用它们。

若要在页面上使用资源字典，请包含一对 `Resources` 属性元素标记。 最方便的方法是将其放在页面顶部：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <ContentPage.Resources>

    </ContentPage.Resources>
    ...
</ContentPage>
```

还需要显式包含 `ResourceDictionary` 标记：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <ContentPage.Resources>
        <ResourceDictionary>

        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

现在可以将各种类型的对象和值添加到资源字典。 这些类型必须是可实例化的。 例如，它们不能是抽象类。 这些类型还必须具有公共的无参数构造函数。 每一项都需要一个使用特性指定的字典键 `x:Key` 。 例如：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <ContentPage.Resources>
        <ResourceDictionary>
            <LayoutOptions x:Key="horzOptions"
                           Alignment="Center" />

            <LayoutOptions x:Key="vertOptions"
                           Alignment="Center"
                           Expands="True" />
        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

这两项是结构类型的值 `LayoutOptions` ，每个项都有一个唯一键和一个或两个属性集。 在代码和标记中，使用的静态字段更为常见 `LayoutOptions` ，但在这里可以更方便地设置属性。

现在，必须将 `HorizontalOptions` 这些按钮的和 `VerticalOptions` 属性设置为这些资源，并且这是通过 `StaticResource` XAML 标记扩展完成的：

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="3"
        Rotation="-15"
        TextColor="Red"
        FontSize="24" />
```

`StaticResource`标记扩展始终用大括号分隔，并包含字典键。

该名称将 `StaticResource` 它与 `DynamicResource` （也支持）区分开来 Xamarin.Forms 。 `DynamicResource` 用于与在运行时可能更改的值相关联的字典键，而在 `StaticResource` 构造页上的元素时仅访问字典中的元素。

对于 `BorderWidth` 属性，必须在字典中存储 double。 XAML 方便地定义常见数据类型（如和）的标记 `x:Double` `x:Int32` ：

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <LayoutOptions x:Key="horzOptions"
                       Alignment="Center" />

        <LayoutOptions x:Key="vertOptions"
                       Alignment="Center"
                       Expands="True" />

        <x:Double x:Key="borderWidth">
            3
        </x:Double>
    </ResourceDictionary>
</ContentPage.Resources>
```

不需要将其放入三行。 此旋转角度的此字典条目只占用一行：

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <LayoutOptions x:Key="horzOptions"
                       Alignment="Center" />

        <LayoutOptions x:Key="vertOptions"
                       Alignment="Center"
                       Expands="True" />

         <x:Double x:Key="borderWidth">
            3
         </x:Double>

        <x:Double x:Key="rotationAngle">-15</x:Double>
    </ResourceDictionary>
</ContentPage.Resources>
```

可以通过与值相同的方式来引用这两个资源 `LayoutOptions` ：

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="{StaticResource borderWidth}"
        Rotation="{StaticResource rotationAngle}"
        TextColor="Red"
        FontSize="24" />
```

对于类型为的资源 `Color` ，可以使用直接分配这些类型的属性时使用的相同字符串表示形式。 创建资源时，将调用类型转换器。 下面是类型的资源 `Color` ：

```xaml
<Color x:Key="textColor">Red</Color>
```

通常，程序将 `FontSize` 属性设置为枚举的成员 `NamedSize` （例如） `Large` 。 `FontSizeConverter`类在幕后工作，使用方法将其转换为与平台相关的值 `Device.GetNamedSized` 。 但是，在定义字体大小资源时，使用数字值会更有意义，这里显示为 `x:Double` 类型：

```xaml
<x:Double x:Key="fontSize">24</x:Double>
```

现在，除之外的所有属性 `Text` 都是由资源设置定义的：

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="{StaticResource borderWidth}"
        Rotation="{StaticResource rotationAngle}"
        TextColor="{StaticResource textColor}"
        FontSize="{StaticResource fontSize}" />
```

还可以使用 `OnPlatform` 资源字典中的来定义不同的平台值。 下面介绍了对象如何作为 `OnPlatform` 不同文本颜色的资源字典的一部分：

```xaml
<OnPlatform x:Key="textColor"
            x:TypeArguments="Color">
    <On Platform="iOS" Value="Red" />
    <On Platform="Android" Value="Aqua" />
    <On Platform="UWP" Value="#80FF80" />
</OnPlatform>
```

请注意，由于它是 `OnPlatform` `x:Key` 字典中的对象，而是获取属性， `x:TypeArguments` 因为它是泛型类。 `iOS` `Android` `UWP` 当初始化对象时，、和属性将转换为 `Color` 值。

下面是最后一个完整的 XAML 文件，其中包含三个用于访问六个共享值的按钮：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <ContentPage.Resources>
        <ResourceDictionary>
            <LayoutOptions x:Key="horzOptions"
                           Alignment="Center" />

            <LayoutOptions x:Key="vertOptions"
                           Alignment="Center"
                           Expands="True" />

            <x:Double x:Key="borderWidth">3</x:Double>

            <x:Double x:Key="rotationAngle">-15</x:Double>

            <OnPlatform x:Key="textColor"
                        x:TypeArguments="Color">
                <On Platform="iOS" Value="Red" />
                <On Platform="Android" Value="Aqua" />
                <On Platform="UWP" Value="#80FF80" />
            </OnPlatform>

            <x:Double x:Key="fontSize">24</x:Double>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout>
        <Button Text="Do this!"
                HorizontalOptions="{StaticResource horzOptions}"
                VerticalOptions="{StaticResource vertOptions}"
                BorderWidth="{StaticResource borderWidth}"
                Rotation="{StaticResource rotationAngle}"
                TextColor="{StaticResource textColor}"
                FontSize="{StaticResource fontSize}" />

        <Button Text="Do that!"
                HorizontalOptions="{StaticResource horzOptions}"
                VerticalOptions="{StaticResource vertOptions}"
                BorderWidth="{StaticResource borderWidth}"
                Rotation="{StaticResource rotationAngle}"
                TextColor="{StaticResource textColor}"
                FontSize="{StaticResource fontSize}" />

        <Button Text="Do the other thing!"
                HorizontalOptions="{StaticResource horzOptions}"
                VerticalOptions="{StaticResource vertOptions}"
                BorderWidth="{StaticResource borderWidth}"
                Rotation="{StaticResource rotationAngle}"
                TextColor="{StaticResource textColor}"
                FontSize="{StaticResource fontSize}" />

    </StackLayout>
</ContentPage>
```

屏幕截图验证一致性样式和平台相关样式：

[![样式控件](xaml-markup-extensions-images/sharedresources.png)](xaml-markup-extensions-images/sharedresources-large.png#lightbox)

尽管最常见的是 `Resources` 在页面顶部定义集合，但请记住 `Resources` ，属性是由定义的 `VisualElement` ，并且您可以在 `Resources` 页面上的其他元素上具有集合。 例如，尝试 `StackLayout` 在本示例的中添加一个：

```xaml
<StackLayout>
    <StackLayout.Resources>
        <ResourceDictionary>
            <Color x:Key="textColor">Blue</Color>
        </ResourceDictionary>
    </StackLayout.Resources>
    ...
</StackLayout>
```

你将发现按钮的文本颜色现在为蓝色。 基本上，每当 XAML 分析器遇到 `StaticResource` 标记扩展时，它都会在可视化树中向上搜索，并使用包含该键的第一个元素 `ResourceDictionary` 。

资源字典中存储的最常见的对象类型之一是 Xamarin.Forms `Style` ，它定义了属性设置的集合。 文章 [样式](~/xamarin-forms/user-interface/styles/index.md)中讨论了样式。

有时，开发人员在 XAML 中的新用户可以在中放置一个视觉元素（如 `Label` 或） `Button` `ResourceDictionary` 。 尽管当然是可行的，但这并不是很好的做法。 的用途 `ResourceDictionary` 是共享对象。 无法共享可视元素。 同一实例不能在单个页面上出现两次。

## <a name="the-xstatic-markup-extension"></a>X:Static 标记扩展

尽管其名称相似， `x:Static` 但并 `StaticResource` 不相同。 `StaticResource` 从资源字典中返回 `x:Static` 一个对象，同时访问以下内容之一：

- 公共静态字段
- 公共静态属性
- 公共常量字段
- 枚举成员。

`StaticResource`Xaml 实现支持标记扩展，定义资源字典时，它 `x:Static` 是 xaml 的内部组成部分，因为 `x` 前缀会显示。

下面的几个示例演示了如何 `x:Static` 显式引用静态字段和枚举成员：

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="{x:Static LayoutOptions.Start}"
       HorizontalTextAlignment="{x:Static TextAlignment.Center}"
       TextColor="{x:Static Color.Aqua}" />
```

到目前为止，这并不是很太深刻。 但 `x:Static` 标记扩展还可以从自己的代码中引用静态字段或属性。 例如，下面是一个 `AppConstants` 类，其中包含一些静态字段，你可能希望在整个应用程序中的多个页上使用这些静态字段：

```csharp
using System;
using Xamarin.Forms;

namespace XamlSamples
{
    static class AppConstants
    {
        public static readonly Thickness PagePadding;

        public static readonly Font TitleFont;

        public static readonly Color BackgroundColor = Color.Aqua;

        public static readonly Color ForegroundColor = Color.Brown;

        static AppConstants()
        {
            switch (Device.RuntimePlatform)
            {
                case Device.iOS:
                    PagePadding = new Thickness(5, 20, 5, 0);
                    TitleFont = Font.SystemFontOfSize(35, FontAttributes.Bold);
                    break;

                case Device.Android:
                    PagePadding = new Thickness(5, 0, 5, 0);
                    TitleFont = Font.SystemFontOfSize(40, FontAttributes.Bold);
                    break;

                case Device.UWP:
                    PagePadding = new Thickness(5, 0, 5, 0);
                    TitleFont = Font.SystemFontOfSize(50, FontAttributes.Bold);
                    break;
            }
        }
    }
}
```

若要在 XAML 文件中引用此类的静态字段，您需要在此文件所在的 XAML 文件中指示某种方法。 您可以使用 XML 命名空间声明来执行此操作。

请记住，作为标准 XAML 模板的一部分创建的 XAML 文件 Xamarin.Forms 包含两个 XML 命名空间声明：一个用于访问 Xamarin.Forms 类，另一个用于引用 XAML 的内部引用标记和特性：

```csharp
xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
```

你将需要其他 XML 命名空间声明来访问其他类。 其他每个 XML 命名空间声明都定义一个新前缀。 若要访问共享应用程序的本地类 .NET Standard 库，如 `AppConstants` XAML 程序员通常使用前缀 `local` 。 命名空间声明必须指示 CLR (公共语言运行时) 命名空间名称，也称为 .NET 命名空间名称，它是在 c # `namespace` 定义或指令中显示的名称 `using` ：

```csharp
xmlns:local="clr-namespace:XamlSamples"
```

你还可以在 .NET Standard 库引用的任何程序集中定义 .NET 命名空间的 XML 命名空间声明。 例如，下面是 `sys` 标准 .net `System` 命名空间的前缀，位于 **netstandard** 程序集中。 由于这是另一个程序集，因此还必须指定程序集名称，在本例中为 **netstandard**：

```csharp
xmlns:sys="clr-namespace:System;assembly=netstandard"
```

请注意，关键字 `clr-namespace` 后跟一个冒号和 .net 命名空间名称，后跟一个分号、关键字 `assembly` 、一个等号和程序集名称。

是的，其后有一个冒号， `clr-namespace` 但后面有一个等号 `assembly` 。 语法是特意定义的：大多数 XML 命名空间声明都引用了一个 URI，该 URI 开始使用 URI 方案名称（如 `http` ），该名称后面始终跟有一个冒号。 `clr-namespace`此字符串的组成部分旨在模拟该约定。

这两个命名空间声明都包含在 **StaticConstantsPage** 示例中。 请注意， `BoxView` 维度设置为 `Math.PI` and `Math.E` ，但缩放比例为100：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             xmlns:sys="clr-namespace:System;assembly=netstandard"
             x:Class="XamlSamples.StaticConstantsPage"
             Title="Static Constants Page"
             Padding="{x:Static local:AppConstants.PagePadding}">

    <StackLayout>
       <Label Text="Hello, XAML!"
              TextColor="{x:Static local:AppConstants.BackgroundColor}"
              BackgroundColor="{x:Static local:AppConstants.ForegroundColor}"
              Font="{x:Static local:AppConstants.TitleFont}"
              HorizontalOptions="Center" />

      <BoxView WidthRequest="{x:Static sys:Math.PI}"
               HeightRequest="{x:Static sys:Math.E}"
               Color="{x:Static local:AppConstants.ForegroundColor}"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand"
               Scale="100" />
    </StackLayout>
</ContentPage>
```

相对于屏幕的结果大小与 `BoxView` 平台相关：

[![使用 x:Static 标记扩展的控件](xaml-markup-extensions-images/staticconstants.png)](xaml-markup-extensions-images/staticconstants-large.png#lightbox)

## <a name="other-standard-markup-extensions"></a>其他标准标记扩展

多个标记扩展在 XAML 中是内部的，在 xaml 文件中受支持 Xamarin.Forms 。 其中一些方法不经常使用，但在需要时很重要：

- 如果默认情况下某个属性具有非 `null` 值，但你想要将其设置为  `null` ，请将其设置为 `{x:Null}` 标记扩展。
- 如果属性的类型为 `Type` ，则可以使用标记扩展将其分配给  `Type` 对象 `{x:Type someClass}` 。
- 您可以使用标记扩展在 XAML 中定义数组 `x:Array` 。 此标记扩展具有一个名为的必需特性  `Type` ，该特性指示数组中的元素的类型。
- 第 `Binding` 4 部分讨论了标记扩展 [。数据绑定基础知识](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)。
- `RelativeSource`[相关绑定](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md)中讨论了标记扩展。

## <a name="the-constraintexpression-markup-extension"></a>ConstraintExpression 标记扩展

标记扩展可以具有属性，但不像 XML 特性那样设置它们。 在标记扩展中，属性设置由逗号分隔，且不在大括号中出现引号。

这可以使用 Xamarin.Forms 名为的标记扩展进行说明，该扩展 `ConstraintExpression` 与类一起使用 `RelativeLayout` 。 您可以将子视图的位置或大小指定为常量，或相对于父视图或其他命名视图。 的语法 `ConstraintExpression` 允许您使用另一个视图的属性和来设置视图的位置或大小 `Factor` `Constant` 。 比要求代码更复杂的内容。

下面是一个示例：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.RelativeLayoutPage"
             Title="RelativeLayout Page">

    <RelativeLayout>

        <!-- Upper left -->
        <BoxView Color="Red"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=Constant,
                                            Constant=0}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=Constant,
                                            Constant=0}" />
        <!-- Upper right -->
        <BoxView Color="Green"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Width,
                                            Factor=1,
                                            Constant=-40}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=Constant,
                                            Constant=0}" />
        <!-- Lower left -->
        <BoxView Color="Blue"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=Constant,
                                            Constant=0}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Height,
                                            Factor=1,
                                            Constant=-40}" />
        <!-- Lower right -->
        <BoxView Color="Yellow"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Width,
                                            Factor=1,
                                            Constant=-40}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Height,
                                            Factor=1,
                                            Constant=-40}" />

        <!-- Centered and 1/3 width and height of parent -->
        <BoxView x:Name="oneThird"
                 Color="Red"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Width,
                                            Factor=0.33}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Height,
                                            Factor=0.33}"
                 RelativeLayout.WidthConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Width,
                                            Factor=0.33}"
                 RelativeLayout.HeightConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Height,
                                            Factor=0.33}"  />

        <!-- 1/3 width and height of previous -->
        <BoxView Color="Blue"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=RelativeToView,
                                            ElementName=oneThird,
                                            Property=X}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=RelativeToView,
                                            ElementName=oneThird,
                                            Property=Y}"
                 RelativeLayout.WidthConstraint=
                     "{ConstraintExpression Type=RelativeToView,
                                            ElementName=oneThird,
                                            Property=Width,
                                            Factor=0.33}"
                 RelativeLayout.HeightConstraint=
                     "{ConstraintExpression Type=RelativeToView,
                                            ElementName=oneThird,
                                            Property=Height,
                                            Factor=0.33}"  />
    </RelativeLayout>
</ContentPage>
```

也许，本示例中应执行的最重要的课程可能是标记扩展的语法：标记扩展的大括号内必须出现引号。 在 XAML 文件中键入标记扩展时，需要将属性的值括在引号中。 不能做到这一点！

下面是运行的程序：

[![使用约束的相对布局](xaml-markup-extensions-images/relativelayout.png)](xaml-markup-extensions-images/relativelayout-large.png#lightbox)

## <a name="summary"></a>总结

此处显示的 XAML 标记扩展为 XAML 文件提供重要支持。 但最有价值的 XAML 标记扩展可能是 `Binding` ，这将在本系列的下一部分中涵盖， [第4部分。数据绑定基础知识](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)。

## <a name="related-links"></a>相关链接

- [XamlSamples](/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [第1部分。与 XAML 入门](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [第2部分。基本 XAML 语法](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [第4部分。数据绑定基础知识](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [第5部分。从数据绑定到 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)