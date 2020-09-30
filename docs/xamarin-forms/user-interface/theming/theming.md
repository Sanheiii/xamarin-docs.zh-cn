---
title: 主题 Xamarin.Forms 应用程序
description: 在 Xamarin.Forms 应用程序中，可以通过为每个主题创建 ResourceDictionary 并使用 DynamicResource 标记扩展来加载资源，来实现主题。
ms.prod: xamarin
ms.assetId: B7B17F66-4E37-4B50-9A57-351B62BE4FED
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7eef9cdf353c527be4e1de4721a5658a7763dabe
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91557538"
---
# <a name="theme-a-no-locxamarinforms-application"></a>主题 Xamarin.Forms 应用程序

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-theming/)

Xamarin.Forms 应用程序可以使用标记扩展在运行时动态响应样式更改 `DynamicResource` 。 此标记扩展类似于 `StaticResource` 标记扩展，因为这两种方法都使用字典键从中提取值 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 。 但是，当 `StaticResource` 标记扩展执行单个字典查找时， `DynamicResource` 标记扩展将保留指向字典键的链接。 因此，如果替换与该键关联的值，则更改将应用到 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。 这样就可以在应用程序中实现运行时主题 Xamarin.Forms 。

在应用程序中实现运行时主题的过程如下所示 Xamarin.Forms ：

1. 定义中每个主题的资源 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 。
1. 使用标记扩展在应用程序中使用主题资源 `DynamicResource` 。
1. 在应用程序的 **app.config** 文件中设置默认主题。
1. 添加代码以在运行时加载主题。

> [!IMPORTANT]
> `StaticResource`如果不需要在运行时更改应用主题，请使用标记扩展。

以下屏幕截图显示主题页面，其中包含使用浅色主题的 iOS 应用程序和使用深色主题的 Android 应用程序：

[![IOS 和 Android](theming-images/main-page-both-themes.png "主题应用的主页")](theming-images/main-page-both-themes-large.png#lightbox "主题应用的主页") 
 上主题应用的主页的屏幕截图[ ![IOS 和 Android 上的主题应用的详细信息页的屏幕截图](theming-images/detail-page-both-themes.png "主题应用的详细信息页")](theming-images/detail-page-both-themes-large.png#lightbox "主题应用的详细信息页")

> [!NOTE]
> 在运行时更改主题需要使用 XAML 样式，当前无法使用 CSS。

## <a name="define-themes"></a>定义主题

主题定义为中存储的资源对象的集合 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 。

下面的示例演示了 `LightTheme` 示例应用程序的：

```xaml
<ResourceDictionary xmlns="http://xamarin.com/schemas/2014/forms"
                    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                    x:Class="ThemingDemo.LightTheme">
    <Color x:Key="PageBackgroundColor">White</Color>
    <Color x:Key="NavigationBarColor">WhiteSmoke</Color>
    <Color x:Key="PrimaryColor">WhiteSmoke</Color>
    <Color x:Key="SecondaryColor">Black</Color>
    <Color x:Key="PrimaryTextColor">Black</Color>
    <Color x:Key="SecondaryTextColor">White</Color>
    <Color x:Key="TertiaryTextColor">Gray</Color>
    <Color x:Key="TransparentColor">Transparent</Color>
</ResourceDictionary>
```

下面的示例演示了 `DarkTheme` 示例应用程序的：

```xaml
<ResourceDictionary xmlns="http://xamarin.com/schemas/2014/forms"
                    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                    x:Class="ThemingDemo.DarkTheme">
    <Color x:Key="PageBackgroundColor">Black</Color>
    <Color x:Key="NavigationBarColor">Teal</Color>
    <Color x:Key="PrimaryColor">Teal</Color>
    <Color x:Key="SecondaryColor">White</Color>
    <Color x:Key="PrimaryTextColor">White</Color>
    <Color x:Key="SecondaryTextColor">White</Color>
    <Color x:Key="TertiaryTextColor">WhiteSmoke</Color>
    <Color x:Key="TransparentColor">Transparent</Color>
</ResourceDictionary>
```

每个都 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 包含 [`Color`](xref:Xamarin.Forms.Color) 定义各自主题的资源，每个资源 `ResourceDictionary` 使用相同的键值。 有关资源字典的详细信息，请参阅 [资源字典](~/xamarin-forms/xaml/resource-dictionaries.md)。

> [!IMPORTANT]
> 对于调用方法的每个，都需要代码隐藏文件 `ResourceDictionary` `InitializeComponent` 。 这是必需的，以便可以在运行时创建表示所选主题的 CLR 对象。

## <a name="set-a-default-theme"></a>设置默认主题

应用程序需要默认主题，以便控件具有其使用的资源的值。 可以通过将主题合并 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 到 `ResourceDictionary` 在 **应用**程序中定义的应用程序级来设置默认主题：

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ThemingDemo.App">
    <Application.Resources>
        <ResourceDictionary Source="Themes/LightTheme.xaml" />
    </Application.Resources>
</Application>
```

有关合并资源字典的详细信息，请参阅 [合并资源字典](~/xamarin-forms/xaml/resource-dictionaries.md#merged-resource-dictionaries)。

## <a name="consume-theme-resources"></a>使用主题资源

当应用程序想要使用存储在中的资源 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 表示主题时，它应使用 `DynamicResource` 标记扩展来实现。 这可确保在运行时选择其他主题时，将应用新主题中的值。

下面的示例演示了可应用于对象的示例应用程序的三种样式 [`Label`](xref:Xamarin.Forms.Label) ：

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ThemingDemo.App">
    <Application.Resources>

        <Style x:Key="LargeLabelStyle"
               TargetType="Label">
            <Setter Property="TextColor"
                    Value="{DynamicResource SecondaryTextColor}" />
            <Setter Property="FontSize"
                    Value="30" />
        </Style>

        <Style x:Key="MediumLabelStyle"
               TargetType="Label">
            <Setter Property="TextColor"
                    Value="{DynamicResource PrimaryTextColor}" />
            <Setter Property="FontSize"
                    Value="25" />
        </Style>

        <Style x:Key="SmallLabelStyle"
               TargetType="Label">
            <Setter Property="TextColor"
                    Value="{DynamicResource TertiaryTextColor}" />
            <Setter Property="FontSize"
                    Value="15" />
        </Style>

    </Application.Resources>
</Application>
```

在应用程序级资源字典中定义这些样式，使其可供多个页面使用。 每种样式均使用带有标记扩展的主题资源 `DynamicResource` 。

然后，页面将使用这些样式：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ThemingDemo"
             x:Class="ThemingDemo.UserSummaryPage"
             Title="User Summary"
             BackgroundColor="{DynamicResource PageBackgroundColor}">
    ...
    <ScrollView>
        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition Height="200" />
                <RowDefinition Height="120" />
                <RowDefinition Height="70" />
            </Grid.RowDefinitions>
            <Grid BackgroundColor="{DynamicResource PrimaryColor}">
                <Label Text="Face-Palm Monkey"
                       VerticalOptions="Center"
                       Margin="15"
                       Style="{StaticResource MediumLabelStyle}" />
                ...
            </Grid>
            <StackLayout Grid.Row="1"
                         Margin="10">
                <Label Text="This monkey reacts appropriately to ridiculous assertions and actions."
                       Style="{StaticResource SmallLabelStyle}" />
                <Label Text="  &#x2022; Cynical but not unfriendly."
                       Style="{StaticResource SmallLabelStyle}" />
                <Label Text="  &#x2022; Seven varieties of grimaces."
                       Style="{StaticResource SmallLabelStyle}" />
                <Label Text="  &#x2022; Doesn't laugh at your jokes."
                       Style="{StaticResource SmallLabelStyle}" />
            </StackLayout>
            ...
        </Grid>
    </ScrollView>
</ContentPage>
```

当直接使用主题资源时，应使用标记扩展来使用该资源 `DynamicResource` 。 但是，如果使用标记扩展的样式使用 `DynamicResource` ，则应使用标记扩展来使用该样式 `StaticResource` 。

有关样式的详细信息，请 [参阅 Xamarin.Forms 使用 XAML 样式设置应用样式](~/xamarin-forms/user-interface/styles/xaml/index.md)。 有关标记扩展的详细信息 `DynamicResource` ，请参阅[ Xamarin.Forms 中的动态样式](~/xamarin-forms/user-interface/styles/xaml/dynamic.md)。

## <a name="load-a-theme-at-runtime"></a>在运行时加载主题

当在运行时选择主题时，应用程序应执行以下操作：

1. 从应用程序中删除当前主题。 这是通过清除 [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) 应用程序级别的属性来实现的 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 。
2. 加载选定的主题。 这是通过将所选主题的实例添加到 `MergedDictionaries` 应用程序级的属性来实现的 `ResourceDictionary` 。

[`VisualElement`](xref:Xamarin.Forms.VisualElement)使用标记扩展设置属性的任何对象 `DynamicResource` 将应用新的主题值。 之所以发生这种情况，是因为 `DynamicResource` 标记扩展会保留一个到字典键的链接。 因此，当替换与键关联的值时，所做的更改将应用于 `VisualElement` 对象。

在示例应用程序中，通过包含的模式页选择主题 [`Picker`](xref:Xamarin.Forms.Picker) 。 下面的代码演示 `OnPickerSelectionChanged` 方法，当选择的主题发生更改时执行该方法：

```csharp
void OnPickerSelectionChanged(object sender, EventArgs e)
{
    Picker picker = sender as Picker;
    Theme theme = (Theme)picker.SelectedItem;

    ICollection<ResourceDictionary> mergedDictionaries = Application.Current.Resources.MergedDictionaries;
    if (mergedDictionaries != null)
    {
        mergedDictionaries.Clear();

        switch (theme)
        {
            case Theme.Dark:
                mergedDictionaries.Add(new DarkTheme());
                break;
            case Theme.Light:
            default:
                mergedDictionaries.Add(new LightTheme());
                break;
        }
    }
}
```

## <a name="related-links"></a>相关链接

- [主题 (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-theming/)
- [响应系统主题更改](system-theme-changes.md)
- [资源字典](~/xamarin-forms/xaml/resource-dictionaries.md)
- [中的动态样式 Xamarin.Forms](~/xamarin-forms/user-interface/styles/xaml/dynamic.md)
- [使用 XAML 样式设置 Xamarin.Forms 应用的样式](~/xamarin-forms/user-interface/styles/xaml/index.md)