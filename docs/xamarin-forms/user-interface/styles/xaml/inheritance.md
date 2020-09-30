---
title: 样式继承 Xamarin.Forms
description: 样式可以从其他样式继承，以减少重复并实现重复使用。 本文介绍如何在应用程序中执行样式继承 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: 67A3A39C-8CC0-446D-8162-FFA73582D3B8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a8aea2617d31289266e792afba773973eddb2d93
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562231"
---
# <a name="style-inheritance-in-no-locxamarinforms"></a>样式继承 Xamarin.Forms

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_样式可以从其他样式继承，以减少重复并实现重复使用。_

## <a name="style-inheritance-in-xaml"></a>XAML 中的样式继承

样式继承是通过将属性设置 [`Style.BasedOn`](xref:Xamarin.Forms.Style.BasedOn) 为现有来执行的 [`Style`](xref:Xamarin.Forms.Style) 。 在 XAML 中，这是通过将 `BasedOn` 属性设置为 `StaticResource` 引用前面创建的的标记扩展来 `Style` 实现的。 在 c # 中，这是通过将 `BasedOn` 属性设置为 `Style` 实例实现的。

继承自基样式的样式可以包含 [`Setter`](xref:Xamarin.Forms.Setter) 新属性的实例，也可以使用这些实例从基本样式中重写样式。 此外，从基本样式继承的样式必须以相同的类型或派生自基样式的目标类型的类型为目标。 例如，如果基样式面向 [`View`](xref:Xamarin.Forms.View) 实例，则基于基样式的样式可以将 `View` 派生自类的实例或类型（ `View` 如和实例）作为目标 [`Label`](xref:Xamarin.Forms.Label) [`Button`](xref:Xamarin.Forms.Button) 。

下面的代码演示了 XAML 页中的 *显式* 样式继承：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.StyleInheritancePage" Title="Inheritance" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="baseStyle" TargetType="View">
                <Setter Property="HorizontalOptions"
                        Value="Center" />
                <Setter Property="VerticalOptions"
                        Value="CenterAndExpand" />
            </Style>
            <Style x:Key="labelStyle" TargetType="Label"
                   BasedOn="{StaticResource baseStyle}">
                ...
                <Setter Property="TextColor" Value="Teal" />
            </Style>
            <Style x:Key="buttonStyle" TargetType="Button"
                   BasedOn="{StaticResource baseStyle}">
                <Setter Property="BorderColor" Value="Lime" />
                ...
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Label Text="These labels"
                   Style="{StaticResource labelStyle}" />
            ...
            <Button Text="So is the button"
                    Style="{StaticResource buttonStyle}" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

`baseStyle`目标 [`View`](xref:Xamarin.Forms.View) 实例，并设置 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 和 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 属性。 `baseStyle`未在任何控件上直接设置。 相反， `labelStyle` 并 `buttonStyle` 从其继承，设置其他可绑定的属性值。 `labelStyle` `buttonStyle` 然后 [`Label`](xref:Xamarin.Forms.Label) ，通过设置这些实例和实例的属性来将和应用到实例和 [`Button`](xref:Xamarin.Forms.Button) 实例 [`Style`](xref:Xamarin.Forms.NavigableElement.Style) 。 这会导致如以下屏幕截图中所示的外观：

[![样式继承屏幕截图](inheritance-images/style-inheritance.png)](inheritance-images/style-inheritance-large.png#lightbox)

> [!NOTE]
> 隐式样式可以从显式样式派生，但无法从隐式样式派生显式样式。

### <a name="respecting-the-inheritance-chain"></a>遵循继承链

在视图层次结构中，style 只能从同一级别或更高级别的样式继承。 这表示：

- 应用程序级别资源只能从其他应用程序级别资源继承。
- 页面级别资源可从应用程序级别资源和其他页面级别资源继承。
- 控件级别资源可从应用程序级别资源、页级别资源和其他控件级别资源继承。

下面的代码示例演示了此继承链：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.StyleInheritancePage" Title="Inheritance" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="baseStyle" TargetType="View">
              ...
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <StackLayout.Resources>
                <ResourceDictionary>
                    <Style x:Key="labelStyle" TargetType="Label" BasedOn="{StaticResource baseStyle}">
                      ...
                    </Style>
                    <Style x:Key="buttonStyle" TargetType="Button" BasedOn="{StaticResource baseStyle}">
                      ...
                    </Style>
                </ResourceDictionary>
            </StackLayout.Resources>
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

在此示例中， `labelStyle` 和 `buttonStyle` 是控件级别的资源，而 `baseStyle` 是页面级别资源。 但是，当 `labelStyle` 和 `buttonStyle` 从继承时 `baseStyle` ，不能 `baseStyle` 从或继承， `labelStyle` 原因是 `buttonStyle` 它们在视图层次结构中的相应位置。

## <a name="style-inheritance-in-c35"></a>C&#35; 中的样式继承

在 [`Style`](xref:Xamarin.Forms.Style) 下面的代码示例中显示了将实例直接分配给 [`Style`](xref:Xamarin.Forms.NavigableElement.Style) 所需控件的属性的等效 c # 页：

```csharp
public class StyleInheritancePageCS : ContentPage
{
    public StyleInheritancePageCS ()
    {
        var baseStyle = new Style (typeof(View)) {
            Setters = {
                new Setter {
                    Property = View.HorizontalOptionsProperty, Value = LayoutOptions.Center    },
                ...
            }
        };

        var labelStyle = new Style (typeof(Label)) {
            BasedOn = baseStyle,
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Teal    }
            }
        };

        var buttonStyle = new Style (typeof(Button)) {
            BasedOn = baseStyle,
            Setters = {
                new Setter { Property = Button.BorderColorProperty, Value =    Color.Lime },
                ...
            }
        };
        ...

        Content = new StackLayout {
            Children = {
                new Label { Text = "These labels", Style = labelStyle },
                ...
                new Button { Text = "So is the button", Style = buttonStyle }
            }
        };
    }
}
```

`baseStyle`目标 [`View`](xref:Xamarin.Forms.View) 实例，并设置 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 和 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 属性。 `baseStyle`未在任何控件上直接设置。 相反， `labelStyle` 并 `buttonStyle` 从其继承，设置其他可绑定的属性值。 `labelStyle` `buttonStyle` 然后 [`Label`](xref:Xamarin.Forms.Label) ，通过设置这些实例和实例的属性来将和应用到实例和 [`Button`](xref:Xamarin.Forms.Button) 实例 [`Style`](xref:Xamarin.Forms.NavigableElement.Style) 。

## <a name="related-links"></a>相关链接

- [XAML 标记扩展](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [基本样式 (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)
- [使用样式 (示例) ](/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [样式](xref:Xamarin.Forms.Style)
- [](xref:Xamarin.Forms.Setter)Setter