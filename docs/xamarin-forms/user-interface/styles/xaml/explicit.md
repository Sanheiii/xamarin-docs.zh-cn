---
title: 显式样式 Xamarin.Forms
description: 显式样式是通过设置控件的样式属性有选择地应用于控件的样式。 本文介绍如何在应用程序中使用显式样式 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: C0DF9F8F-B431-4374-A574-325BC3C41A3B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b462535815e1c8ca6262d82d2527d7667b645db8
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563921"
---
# <a name="explicit-styles-in-no-locxamarinforms"></a>显式样式 Xamarin.Forms

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_显式样式是通过设置控件的样式属性有选择地应用于控件的样式。_

## <a name="create-an-explicit-style-in-xaml"></a>在 XAML 中创建显式样式

若要 [`Style`](xref:Xamarin.Forms.Style) 在页级别声明， [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 则必须将添加到页，然后一个或多个 `Style` 声明可以包含在中 `ResourceDictionary` 。 `Style`通过为其声明提供一个特性来使成为*显式*的 `x:Key` ，这将在中为其提供一个描述性键 `ResourceDictionary` 。 然后，必须通过设置*显式*样式的属性，将其应用于特定的视觉对象 [`Style`](xref:Xamarin.Forms.NavigableElement.Style) 。

下面的代码示例演示*explicit*在页的中 `ResourceDictionary` ，并将其应用于页面实例的显式样式 [`Label`](xref:Xamarin.Forms.Label) ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ExplicitStylesPage" Title="Explicit" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="labelRedStyle" TargetType="Label">
                <Setter Property="HorizontalOptions"
                        Value="Center" />
                <Setter Property="VerticalOptions"
                        Value="CenterAndExpand" />
                <Setter Property="FontSize" Value="Large" />
                <Setter Property="TextColor" Value="Red" />
            </Style>
            <Style x:Key="labelGreenStyle" TargetType="Label">
                ...
                <Setter Property="TextColor" Value="Green" />
            </Style>
            <Style x:Key="labelBlueStyle" TargetType="Label">
                ...
                <Setter Property="TextColor" Value="Blue" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Label Text="These labels"
                   Style="{StaticResource labelRedStyle}" />
            <Label Text="are demonstrating"
                   Style="{StaticResource labelGreenStyle}" />
            <Label Text="explicit styles,"
                   Style="{StaticResource labelBlueStyle}" />
            <Label Text="and an explicit style override"
                   Style="{StaticResource labelBlueStyle}"
                   TextColor="Teal" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)定义了三种应用于页面实例的*显式*样式 [`Label`](xref:Xamarin.Forms.Label) 。 每个 `Style` 用于以不同的颜色显示文本，同时还可设置字号和水平和垂直布局选项。 每个 `Style` 都 `Label` 通过 [`Style`](xref:Xamarin.Forms.NavigableElement.Style) 使用标记扩展设置其属性来应用于不同的 `StaticResource` 。 这会导致如以下屏幕截图中所示的外观：

[![显式样式示例](explicit-images/explicit-styles.png)](explicit-images/explicit-styles-large.png#lightbox)

此外，最后 [`Label`](xref:Xamarin.Forms.Label) 一个 [`Style`](xref:Xamarin.Forms.Style) 应用于它，但也将该属性重写 [`TextColor`](xref:Xamarin.Forms.Label.TextColor) 为其他 `Color` 值。

### <a name="create-an-explicit-style-at-the-control-level"></a>在控件级别创建显式样式

除了在页面级别创建 *显式* 样式以外，还可以在控件级别创建它们，如下面的代码示例所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ExplicitStylesPage" Title="Explicit" IconImageSource="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <StackLayout.Resources>
                <ResourceDictionary>
                    <Style x:Key="labelRedStyle" TargetType="Label">
                      ...
                    </Style>
                    ...
                </ResourceDictionary>
            </StackLayout.Resources>
            <Label Text="These labels" Style="{StaticResource labelRedStyle}" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

在此示例中，将 *显式* [`Style`](xref:Xamarin.Forms.Style) 实例分配给 [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) 控件的集合 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。 然后，可以将样式应用于控件及其子控件。

有关在应用程序中创建样式的信息 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) ，请参阅 [全局样式](~/xamarin-forms/user-interface/styles/application.md)。

## <a name="create-an-explicit-style-in-c35"></a>在 C 中创建显式样式&#35;

[`Style`](xref:Xamarin.Forms.Style) 可以 [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) 通过以下代码示例中所示，通过创建新的，然后将实例添加到来将实例添加到页的集合中 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) `Style` `ResourceDictionary` ，如下面的代码示例所示：

```csharp
public class ExplicitStylesPageCS : ContentPage
{
    public ExplicitStylesPageCS ()
    {
        var labelRedStyle = new Style (typeof(Label)) {
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Red    }
            }
        };
        var labelGreenStyle = new Style (typeof(Label)) {
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Green }
            }
        };
        var labelBlueStyle = new Style (typeof(Label)) {
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Blue }
            }
        };

        Resources = new ResourceDictionary ();
        Resources.Add ("labelRedStyle", labelRedStyle);
        Resources.Add ("labelGreenStyle", labelGreenStyle);
        Resources.Add ("labelBlueStyle", labelBlueStyle);
        ...

        Content = new StackLayout {
            Children = {
                new Label { Text = "These labels",
                            Style = (Style)Resources ["labelRedStyle"] },
                new Label { Text = "are demonstrating",
                            Style = (Style)Resources ["labelGreenStyle"] },
                new Label { Text = "explicit styles,",
                            Style = (Style)Resources ["labelBlueStyle"] },
                new Label {    Text = "and an explicit style override",
                            Style = (Style)Resources ["labelBlueStyle"], TextColor = Color.Teal }
            }
        };
    }
}
```

构造函数定义了三种应用于页面实例的 *显式* 样式 [`Label`](xref:Xamarin.Forms.Label) 。 使用方法将每个 *显式* [`Style`](xref:Xamarin.Forms.Style) 添加到，并 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) [`Add`](xref:Xamarin.Forms.ResourceDictionary.Add(System.String,System.Object)) 指定 `key` 要引用实例的字符串 `Style` 。 每个 `Style` 都 `Label` 通过设置其属性应用于不同的 [`Style`](xref:Xamarin.Forms.NavigableElement.Style) 。

不过，使用这里没有任何优势 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 。 相反， [`Style`](xref:Xamarin.Forms.Style) 可以直接将实例分配给 [`Style`](xref:Xamarin.Forms.NavigableElement.Style) 所需的视觉对象的属性，并且 `ResourceDictionary` 可以将其移除，如下面的代码示例所示：

```csharp
public class ExplicitStylesPageCS : ContentPage
{
    public ExplicitStylesPageCS ()
    {
        var labelRedStyle = new Style (typeof(Label)) {
            ...
        };
        var labelGreenStyle = new Style (typeof(Label)) {
            ...
        };
        var labelBlueStyle = new Style (typeof(Label)) {
            ...
        };
        ...
        Content = new StackLayout {
            Children = {
                new Label { Text = "These labels", Style = labelRedStyle },
                new Label { Text = "are demonstrating", Style = labelGreenStyle },
                new Label { Text = "explicit styles,", Style = labelBlueStyle },
                new Label { Text = "and an explicit style override", Style = labelBlueStyle,
                            TextColor = Color.Teal }
            }
        };
    }
}
```

构造函数定义了三种应用于页面实例的 *显式* 样式 [`Label`](xref:Xamarin.Forms.Label) 。 每个 `Style` 用于以不同的颜色显示文本，同时还可设置字号和水平和垂直布局选项。 每个 `Style` 都 `Label` 通过设置其属性来应用于不同的 [`Style`](xref:Xamarin.Forms.NavigableElement.Style) 。 此外，最后 `Label` 一个 `Style` 应用于它，但也将该属性重写 `TextColor` 为其他 `Color` 值。

## <a name="related-links"></a>相关链接

- [XAML 标记扩展](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [基本样式 (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)
- [使用样式 (示例) ](/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [样式](xref:Xamarin.Forms.Style)
- [](xref:Xamarin.Forms.Setter)Setter