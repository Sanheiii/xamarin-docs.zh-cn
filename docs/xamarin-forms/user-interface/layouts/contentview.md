---
title: Xamarin.Forms ContentView
description: 本文介绍如何使用 ContentView 类来创建自定义控件，例如 CardView 示例。
ms.prod: xamarin
ms.assetid: 638402E7-CA44-456B-863B-791F6B6B561D
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 08/14/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: eb94b2943cfe4659bb77764f810d45dc8b189005
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91555367"
---
# <a name="no-locxamarinforms-contentview"></a>Xamarin.Forms ContentView

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-contentviewdemos/)

Xamarin.Forms [`ContentView`](xref:Xamarin.Forms.ContentView) 类是一个类型的 `Layout` ，它包含一个子元素，通常用于创建自定义的可重用控件。 `ContentView`类继承自 [`TemplatedView`](xref:Xamarin.Forms.TemplatedView) 。 本文和相关示例说明了如何创建基于类的自定义 `CardView` 控件 `ContentView` 。

以下屏幕截图显示了一个 `CardView` 派生自类的 `ContentView` 控件：

[![CardView 示例应用程序屏幕快照](contentview-images/cardview-list-cropped.png)](contentview-images/cardview-list.png#lightbox)

`ContentView`类定义单个属性：

* [`Content`](xref:Xamarin.Forms.ContentView.Content) 是一个 `View` 对象。 此属性由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象支持，因此它可以是数据绑定的目标。

`ContentView`还继承类的属性 `TemplatedView` ：

* [`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate)`ControlTemplate`可定义或重写控件外观的。

有关属性的详细信息 `ControlTemplate` ，请参阅 [使用 System.windows.controls.controltemplate> 自定义外观](#customize-appearance-with-a-controltemplate)。

## <a name="create-a-custom-control"></a>创建自定义控件

`ContentView`类本身提供很少的功能，但可用于创建自定义控件。 该示例项目定义了一个 `CardView` 控件-一个 UI 元素，该元素在类似卡片的布局中显示图像、标题和说明。

创建自定义控件的过程如下：

1. 使用 `ContentView` Visual Studio 2019 中的模板创建新类。
1. 为新的自定义控件定义代码隐藏文件中的任何唯一属性或事件。
1. 为自定义控件创建 UI。

> [!NOTE]
> 可以创建自定义控件，其布局是在代码中定义的，而不是在 XAML 中定义的。 为简单起见，示例应用程序仅定义了一个 `CardView` 具有 XAML 布局的类。 但是，示例应用程序包含一个 **CardViewCodePage** 类，该类显示在代码中使用自定义控件的过程。

### <a name="create-code-behind-properties"></a>创建代码隐藏属性

`CardView`自定义控件定义以下属性：

* `CardTitle`：一个 `string` 对象，表示卡片上显示的标题。
* `CardDescription`：一个 `string` 对象，该对象表示卡上显示的说明。
* `IconImageSource`：一个 `ImageSource` 对象，该对象表示卡上显示的图像。
* `IconBackgroundColor`：一个 `Color` 对象，该对象表示卡上显示的图像的背景色。
* `BorderColor`：一个 `Color` 对象，表示卡片边框、图像边框和分隔线的颜色。
* `CardColor`： `Color` 表示卡片背景色的对象。

> [!NOTE]
> `BorderColor`出于演示的目的，属性会影响多个项。 如果需要，可以将此属性分解为三个属性。

每个属性都由 `BindableProperty` 实例支持。 支持 `BindableProperty` 使用 MVVM 模式使每个属性都具有样式和界限。

下面的示例演示如何创建后备 `BindableProperty` ：

```csharp
public static readonly BindableProperty CardTitleProperty = BindableProperty.Create(
    "CardTitle",        // the name of the bindable property
    typeof(string),     // the bindable property type
    typeof(CardView),   // the parent object type
    string.Empty);      // the default value for the property
```

自定义属性使用 `GetValue` 和 `SetValue` 方法来获取和设置 `BindableProperty` 对象值：

```csharp
public string CardTitle
{
    get => (string)GetValue(CardView.CardTitleProperty);
    set => SetValue(CardView.CardTitleProperty, value);
}
```

有关对象的详细信息 `BindableProperty` ，请参阅可 [绑定属性](~/xamarin-forms/xaml/bindable-properties.md)。

### <a name="define-ui"></a>定义 UI

自定义控件 UI 使用 `ContentView` 作为控件的根元素 `CardView` 。 下面的示例演示了 `CardView` XAML：

```XAML
<ContentView ...
             x:Name="this"
             x:Class="CardViewDemo.Controls.CardView">
    <Frame BindingContext="{x:Reference this}"
            BackgroundColor="{Binding CardColor}"
            BorderColor="{Binding BorderColor}"
            ...>
        <Grid>
            ...
            <Frame BorderColor="{Binding BorderColor, FallbackValue='Black'}"
                   BackgroundColor="{Binding IconBackgroundColor, FallbackValue='Grey'}"
                   ...>
                <Image Source="{Binding IconImageSource}"
                       .. />
            </Frame>
            <Label Text="{Binding CardTitle, FallbackValue='Card Title'}"
                   ... />
            <BoxView BackgroundColor="{Binding BorderColor, FallbackValue='Black'}"
                     ... />
            <Label Text="{Binding CardDescription, FallbackValue='Card description text.'}"
                   ... />
        </Grid>
    </Frame>
</ContentView>
```

`ContentView`元素将属性设置 `x:Name` 为**this，这**可用于访问绑定到实例的对象 `CardView` 。 布局中的元素会将其属性的绑定设置为绑定对象上定义的值。

若要深入了解数据绑定，请参阅 [Xamarin.Forms 数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md)。

> [!NOTE]
> `FallbackValue`属性提供了一个默认值（如果绑定为） `null` 。 这也允许 Visual Studio 中的 [XAML 预览](~/xamarin-forms/xaml/xaml-previewer/index.md) 器呈现 `CardView` 控件。

## <a name="instantiate-a-custom-control"></a>实例化自定义控件

必须将对自定义控件命名空间的引用添加到实例化自定义控件的页中。 下面的示例演示了一个名为 **控件** 的命名空间引用，这些控件添加到了 `ContentPage` XAML 的实例中：

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:CardViewDemo.Controls" >
```

添加引用后， `CardView` 可以在 XAML 中实例化并定义其属性：

```xaml
<controls:CardView BorderColor="DarkGray"
                   CardTitle="Slavko Vlasic"
                   CardDescription="Lorem ipsum dolor sit..."
                   IconBackgroundColor="SlateGray"
                   IconImageSource="user.png"/>
```

`CardView`也可以在代码中实例化：

```csharp
CardView card = new CardView
{
    BorderColor = Color.DarkGray,
    CardTitle = "Slavko Vlasic",
    CardDescription = "Lorem ipsum dolor sit...",
    IconBackgroundColor = Color.SlateGray,
    IconImageSource = ImageSource.FromFile("user.png")
};
```

## <a name="customize-appearance-with-a-controltemplate"></a>使用 System.windows.controls.controltemplate> 自定义外观

派生自类的自定义控件 `ContentView` 可以使用 XAML、代码或根本不定义外观来定义外观。 不管如何定义外观， `ControlTemplate` 对象都可以使用自定义布局替代外观。

`CardView`对于某些用例，布局可能占用过多的空间。 `ControlTemplate`可以重写 `CardView` 布局以提供更紧凑的视图，适用于简洁列表：

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <ControlTemplate x:Key="CardViewCompressed">
            <Grid>
                <Grid.RowDefinitions>
                    <RowDefinition Height="100" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="100" />
                    <ColumnDefinition Width="100*" />
                </Grid.ColumnDefinitions>
                <Image Grid.Row="0"
                       Grid.Column="0"
                       Source="{TemplateBinding IconImageSource}"
                       BackgroundColor="{TemplateBinding IconBackgroundColor}"
                       WidthRequest="100"
                       HeightRequest="100"
                       Aspect="AspectFill"
                       HorizontalOptions="Center"
                       VerticalOptions="Center"/>
                <StackLayout Grid.Row="0"
                             Grid.Column="1">
                    <Label Text="{TemplateBinding CardTitle}"
                           FontAttributes="Bold" />
                    <Label Text="{TemplateBinding CardDescription}" />
                </StackLayout>
            </Grid>
        </ControlTemplate>
    </ResourceDictionary>
</ContentPage.Resources>
```

中的数据绑定 `ControlTemplate` 使用 `TemplateBinding` 标记扩展来指定绑定。 `ControlTemplate`然后，可以使用该属性的值将属性设置为定义的 system.windows.controls.controltemplate> 对象 `x:Key` 。 下面的示例演示如何 `ControlTemplate` 在实例上设置属性 `CardView` ：

```xaml
<controls:CardView ControlTemplate="{StaticResource CardViewCompressed}"/>
```

以下屏幕截图显示标准 `CardView` 实例，并已将 `CardView` 其 `ControlTemplate` 重写：

[![CardView System.windows.controls.controltemplate> 屏幕快照](contentview-images/cardview-controltemplates-cropped.png)](contentview-images/cardview-controltemplates.png#lightbox)

有关控件模板的详细信息，请参阅 [Xamarin.Forms 控件模板](~/xamarin-forms/app-fundamentals/templates/control-template.md)。

## <a name="related-links"></a>相关链接

* [ContentView 示例应用程序](/samples/xamarin/xamarin-forms-samples/userinterface-contentviewdemos/)
* [Xamarin.Forms数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md)
* 可[绑定属性](~/xamarin-forms/xaml/bindable-properties.md)。
* [Xamarin.Forms 控件模板](~/xamarin-forms/app-fundamentals/templates/control-template.md)