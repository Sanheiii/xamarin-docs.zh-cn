---
title: 隐式样式 Xamarin.Forms
description: 隐式样式是同一 TargetType 的所有控件使用的样式，无需每个控件都引用该样式。
ms.prod: xamarin
ms.assetid: 02A75F3B-4389-49D4-A2F4-AFD473A4A161
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/30/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ad297b735449796925b92fbc9d1887db9e8a1c99
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563323"
---
# <a name="implicit-styles-in-no-locxamarinforms"></a>隐式样式 Xamarin.Forms

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_隐式样式是同一 TargetType 的所有控件使用的样式，无需每个控件都引用该样式。_

## <a name="create-an-implicit-style-in-xaml"></a>在 XAML 中创建隐式样式

若要 [`Style`](xref:Xamarin.Forms.Style) 在页级别声明， [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 则必须将添加到页，然后一个或多个 `Style` 声明可以包含在中 `ResourceDictionary` 。 `Style`通过不指定属性使成为*隐式* `x:Key` 。 然后，样式将应用到完全匹配的视觉元素 `TargetType` ，而不是从值派生的元素 `TargetType` 。

下面的代码示例演示了在页的 XAML 中声明的 *隐式* 样式 `ResourceDictionary` ，并将其应用于页面的 [`Entry`](xref:Xamarin.Forms.Entry) 实例：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" xmlns:local="clr-namespace:Styles;assembly=Styles" x:Class="Styles.ImplicitStylesPage" Title="Implicit" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Entry">
                <Setter Property="HorizontalOptions" Value="Fill" />
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
                <Setter Property="BackgroundColor" Value="Yellow" />
                <Setter Property="FontAttributes" Value="Italic" />
                <Setter Property="TextColor" Value="Blue" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Entry Text="These entries" />
            <Entry Text="are demonstrating" />
            <Entry Text="implicit styles," />
            <Entry Text="and an implicit style override" BackgroundColor="Lime" TextColor="Red" />
            <local:CustomEntry Text="Subclassed Entry is not receiving the style" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)定义应用于页面实例的单个*隐式*样式 [`Entry`](xref:Xamarin.Forms.Entry) 。 `Style`用于在黄色背景上显示蓝色文本，同时还设置其他外观选项。 `Style`添加到了页中， [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 但未指定 `x:Key` 属性。 因此，将 `Style` 隐式应用于所有 `Entry` 实例，因为它们与 [`TargetType`](xref:Xamarin.Forms.Style.TargetType) 的属性完全匹配 `Style` 。 但是， `Style` 不会应用于 `CustomEntry` 实例，这是一个子类 `Entry` 。 这会导致如以下屏幕截图中所示的外观：

[![隐式样式示例](implicit-images/implicit-styles.png)](implicit-images/implicit-styles-large.png#lightbox)

此外，第四个将 [`Entry`](xref:Xamarin.Forms.Entry) [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) [`TextColor`](xref:Xamarin.Forms.InputView.TextColor) 隐式样式的和属性重写为不同的 `Color` 值。

### <a name="create-an-implicit-style-at-the-control-level"></a>在控件级别创建隐式样式

除了在页面级别创建 *隐式* 样式以外，还可以在控件级别创建它们，如下面的代码示例所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" xmlns:local="clr-namespace:Styles;assembly=Styles" x:Class="Styles.ImplicitStylesPage" Title="Implicit" IconImageSource="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <StackLayout.Resources>
                <ResourceDictionary>
                    <Style TargetType="Entry">
                        <Setter Property="HorizontalOptions" Value="Fill" />
                        ...
                    </Style>
                </ResourceDictionary>
            </StackLayout.Resources>
            <Entry Text="These entries" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

在此示例中，将 *隐式* [`Style`](xref:Xamarin.Forms.Style) 分配给 [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) 控件的集合 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。 然后，可以将 *隐式* 样式应用于控件及其子级。

有关在应用程序中创建样式的信息 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) ，请参阅 [全局样式](~/xamarin-forms/user-interface/styles/application.md)。

## <a name="create-an-implicit-style-in-c35"></a>在 C 中创建隐式样式&#35;

[`Style`](xref:Xamarin.Forms.Style) 可以 [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) 通过以下代码示例中所示，通过创建新的，然后将实例添加到来将实例添加到页的集合中 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) `Style` `ResourceDictionary` ，如下面的代码示例所示：

```csharp
public class ImplicitStylesPageCS : ContentPage
{
    public ImplicitStylesPageCS ()
    {
        var entryStyle = new Style (typeof(Entry)) {
            Setters = {
                ...
                new Setter { Property = Entry.TextColorProperty, Value = Color.Blue }
            }
        };

        ...
        Resources = new ResourceDictionary ();
        Resources.Add (entryStyle);

        Content = new StackLayout {
            Children = {
                new Entry { Text = "These entries" },
                new Entry { Text = "are demonstrating" },
                new Entry { Text = "implicit styles," },
                new Entry { Text = "and an implicit style override", BackgroundColor = Color.Lime, TextColor = Color.Red },
                new CustomEntry  { Text = "Subclassed Entry is not receiving the style" }
            }
        };
    }
}
```

构造函数定义应用于页面实例的单个 *隐式* 样式 [`Entry`](xref:Xamarin.Forms.Entry) 。 `Style`用于在黄色背景上显示蓝色文本，同时还设置其他外观选项。 `Style`添加到了页中， [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 但未指定 `key` 字符串。 因此，将 `Style` 隐式应用于所有 `Entry` 实例，因为它们与 [`TargetType`](xref:Xamarin.Forms.Style.TargetType) 的属性完全匹配 `Style` 。 但是， `Style` 不会应用于 `CustomEntry` 实例，这是一个子类 `Entry` 。

## <a name="apply-a-style-to-derived-types"></a>将样式应用于派生类型

[`Style.ApplyToDerivedTypes`](xref:Xamarin.Forms.Style.ApplyToDerivedTypes)属性允许将样式应用于从属性引用的基类型派生的控件 [`TargetType`](xref:Xamarin.Forms.Style.TargetType) 。 因此，将此属性设置为可 `true` 启用单个样式以面向多个类型，前提是这些类型派生自在属性中指定的基类型 `TargetType` 。

下面的示例演示了一个隐式样式，该样式将实例的背景色设置 [`Button`](xref:Xamarin.Forms.Button) 为红色：

```xaml
<Style TargetType="Button"
       ApplyToDerivedTypes="True">
    <Setter Property="BackgroundColor"
            Value="Red" />
</Style>
```

将此样式置于页面级别 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 将导致其应用于 [`Button`](xref:Xamarin.Forms.Button) 页面上的所有实例，还会应用于从派生的任何控件 `Button` 。 但是，如果 [`ApplyToDerivedTypes`](xref:Xamarin.Forms.Style.ApplyToDerivedTypes) 属性仍未设置，则样式仅应用于 `Button` 实例。

等效 C# 代码如下：

```csharp
var buttonStyle = new Style(typeof(Button))
{
    ApplyToDerivedTypes = true,
    Setters =
    {
        new Setter
        {
            Property = VisualElement.BackgroundColorProperty,
            Value = Color.Red
        }
    }
};

Resources = new ResourceDictionary { buttonStyle };
```

## <a name="related-links"></a>相关链接

- [XAML 标记扩展](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [基本样式 (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)
- [使用样式 (示例) ](/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [样式](xref:Xamarin.Forms.Style)
- [](xref:Xamarin.Forms.Setter)Setter