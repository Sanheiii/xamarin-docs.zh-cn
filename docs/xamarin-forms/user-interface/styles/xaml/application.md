---
title: 中的全局样式 Xamarin.Forms
description: 通过将样式添加到应用程序的资源字典中，可以全局使用样式。 这有助于避免跨页面或控件的样式的重复。
ms.prod: xamarin
ms.assetid: BDC65F82-65E0-4C8E-BB91-8E340EB2D15A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4b32c4b3ac4dea9dcdf52e3b9316f5f82aaad6ef
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91558331"
---
# <a name="global-styles-in-no-locxamarinforms"></a>中的全局样式 Xamarin.Forms

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_通过将样式添加到应用程序的资源字典中，可以全局使用样式。这有助于避免跨页面或控件的样式的重复。_

## <a name="create-a-global-style-in-xaml"></a>在 XAML 中创建全局样式

默认情况下，通过 Xamarin.Forms 模板创建的所有应用程序都使用 **App** 类来实现 [`Application`](xref:Xamarin.Forms.Application) 子类。 若要在 [`Style`](xref:Xamarin.Forms.Style) 应用程序级别声明，请在应用程序的 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) using xaml 中，将默认 **应用** 程序类替换为 xaml **应用** 类和关联的代码隐藏。 有关详细信息，请参阅使用 [App 类](~/xamarin-forms/app-fundamentals/application-class.md)。

下面的代码示例演示如何 [`Style`](xref:Xamarin.Forms.Style) 在应用程序级别声明：

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.App">
    <Application.Resources>
        <ResourceDictionary>
            <Style x:Key="buttonStyle" TargetType="Button">
                <Setter Property="HorizontalOptions" Value="Center" />
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
                <Setter Property="BorderColor" Value="Lime" />
                <Setter Property="BorderRadius" Value="5" />
                <Setter Property="BorderWidth" Value="5" />
                <Setter Property="WidthRequest" Value="200" />
                <Setter Property="TextColor" Value="Teal" />
            </Style>
        </ResourceDictionary>
    </Application.Resources>
</Application>
```

这 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 将定义一个 *显式* 样式，该样式 `buttonStyle` 将用于设置实例的外观 [`Button`](xref:Xamarin.Forms.Button) 。 但全局样式可以是 *显式* 或 *隐式*的。

下面的代码示例演示了将应用 `buttonStyle` 于页面实例的 XAML 页 [`Button`](xref:Xamarin.Forms.Button) ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ApplicationStylesPage" Title="Application" IconImageSource="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Button Text="These buttons" Style="{StaticResource buttonStyle}" />
            <Button Text="are demonstrating" Style="{StaticResource buttonStyle}" />
            <Button Text="application style overrides" Style="{StaticResource buttonStyle}" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

这会导致如以下屏幕截图中所示的外观：

[![全局样式示例](application-images/application-styles-1.png)](application-images/application-styles-1-large.png#lightbox "全局样式示例")

有关在页面的中创建样式的信息 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) ，请参阅 [显式样式](~/xamarin-forms/user-interface/styles/explicit.md) 和 [隐式样式](~/xamarin-forms/user-interface/styles/implicit.md)。

### <a name="override-styles"></a>重写样式

视图层次结构中较低的样式优先于定义较高的样式。 例如，在 [`Style`](xref:Xamarin.Forms.Style) [`Button.TextColor`](xref:Xamarin.Forms.Button.TextColor) 应用程序级别设置为的设置将被设置为的 `Red` 页级别样式重写 `Button.TextColor` `Green` 。 同样，控件级样式将重写页面级别样式。 此外，如果 `Button.TextColor` 直接在控件属性上设置，则这将优先于任何样式。 下面的代码示例演示了此优先级：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ApplicationStylesPage" Title="Application" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="buttonStyle" TargetType="Button">
                ...
                <Setter Property="TextColor" Value="Red" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <StackLayout.Resources>
                <ResourceDictionary>
                    <Style x:Key="buttonStyle" TargetType="Button">
                        ...
                        <Setter Property="TextColor" Value="Blue" />
                    </Style>
                </ResourceDictionary>
            </StackLayout.Resources>
            <Button Text="These buttons" Style="{StaticResource buttonStyle}" />
            <Button Text="are demonstrating" Style="{StaticResource buttonStyle}" />
            <Button Text="application style overrides" Style="{StaticResource buttonStyle}" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

在 `buttonStyle` 应用程序级别定义的原始在 `buttonStyle` 页级别定义的实例重写。 此外，页面级别样式被控件级别重写 `buttonStyle` 。 因此， [`Button`](xref:Xamarin.Forms.Button) 实例显示为蓝色文本，如以下屏幕截图所示：

[![重写样式示例](application-images/application-styles-2.png)](application-images/application-styles-2-large.png#lightbox "重写样式示例")

## <a name="create-a-global-style-in-c35"></a>在 C 中创建全局样式&#35;

[`Style`](xref:Xamarin.Forms.Style) 可以 [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) 通过创建新的，然后将实例添加到来将实例添加到应用程序的集合中 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) `Style` `ResourceDictionary` ，如下面的代码示例所示：

```csharp
public class App : Application
{
    public App ()
    {
        var buttonStyle = new Style (typeof(Button)) {
            Setters = {
                ...
                new Setter { Property = Button.TextColorProperty,    Value = Color.Teal }
            }
        };

        Resources = new ResourceDictionary ();
        Resources.Add ("buttonStyle", buttonStyle);
        ...
    }
    ...
}
```

构造函数定义单个 *显式* 样式，适用于 [`Button`](xref:Xamarin.Forms.Button) 整个应用程序中的实例。 *显式* [`Style`](xref:Xamarin.Forms.Style) 使用方法将实例添加到中 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) [`Add`](xref:Xamarin.Forms.ResourceDictionary.Add(System.String,System.Object)) ，并指定 `key` 要引用实例的字符串 `Style` 。 `Style`然后，可以将该实例应用于应用程序中正确类型的任何控件。 但全局样式可以是 *显式* 或 *隐式*的。

下面的代码示例演示了将应用于页面实例的 c # 页 `buttonStyle` [`Button`](xref:Xamarin.Forms.Button) ：

```csharp
public class ApplicationStylesPageCS : ContentPage
{
    public ApplicationStylesPageCS ()
    {
        ...
        Content = new StackLayout {
            Children = {
                new Button { Text = "These buttons", Style = (Style)Application.Current.Resources ["buttonStyle"] },
                new Button { Text = "are demonstrating", Style = (Style)Application.Current.Resources ["buttonStyle"] },
                new Button { Text = "application styles", Style = (Style)Application.Current.Resources ["buttonStyle"]
                }
            }
        };
    }
}
```

`buttonStyle` [`Button`](xref:Xamarin.Forms.Button) 通过设置实例的属性应用于实例 [`Style`](xref:Xamarin.Forms.NavigableElement.Style) ，并控制实例的外观 `Button` 。

## <a name="related-links"></a>相关链接

- [XAML 标记扩展](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [基本样式 (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)
- [使用样式 (示例) ](/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [样式](xref:Xamarin.Forms.Style)
- [](xref:Xamarin.Forms.Setter)Setter