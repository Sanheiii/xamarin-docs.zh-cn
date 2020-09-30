---
title: 中的动态样式 Xamarin.Forms
description: 本文说明了 Xamarin.Forms 应用程序如何在运行时使用动态资源动态响应样式更改。
ms.prod: xamarin
ms.assetid: 13D4FA4B-DF10-42BF-B001-2C49367FC216
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/28/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.custom: video
ms.openlocfilehash: d6944da583679d58885c20d327b3e43e176eb6fa
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563349"
---
# <a name="dynamic-styles-in-no-locxamarinforms"></a>中的动态样式 Xamarin.Forms

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-dynamicstyles)

_样式不会对属性更改做出响应，并在应用程序持续时间内保持不变。例如，在将样式分配给视觉对象后，如果修改、删除或添加了新的 Setter 实例，则不会将更改应用于可视元素。但是，应用程序可以使用动态资源在运行时动态响应样式更改。_

`DynamicResource`标记扩展类似于 `StaticResource` 中的标记扩展，这两个方法都使用字典键从中提取值 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 。 但是，虽然 `StaticResource` 执行单个字典查找，但仍会 `DynamicResource` 保留字典键的链接。 因此，如果替换与该键关联的字典条目，则更改将应用于该视觉对象。 这样就可以在应用程序中进行运行时样式更改。

下面的代码示例演示了 XAML 页中的 *动态* 样式：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.DynamicStylesPage" Title="Dynamic" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="baseStyle" TargetType="View">
              ...
            </Style>
            <Style x:Key="blueSearchBarStyle"
                   TargetType="SearchBar"
                   BasedOn="{StaticResource baseStyle}">
              ...
            </Style>
            <Style x:Key="greenSearchBarStyle"
                   TargetType="SearchBar">
              ...
            </Style>
            ...
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <SearchBar Placeholder="These SearchBar controls"
                       Style="{DynamicResource searchBarStyle}" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

[`SearchBar`](xref:Xamarin.Forms.SearchBar)实例使用 `DynamicResource` 标记扩展来引用 [`Style`](xref:Xamarin.Forms.Style) `searchBarStyle` 未在 XAML 中定义的命名。 但是，由于 [`Style`](xref:Xamarin.Forms.NavigableElement.Style) 使用将实例的属性 `SearchBar` 设置为 `DynamicResource` ，缺少字典键不会导致引发异常。

相反，在代码隐藏文件中，构造函数创建一个 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 包含键的项 `searchBarStyle` ，如以下代码示例所示：

```csharp
public partial class DynamicStylesPage : ContentPage
{
    bool originalStyle = true;

    public DynamicStylesPage ()
    {
        InitializeComponent ();
        Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];
    }

    void OnButtonClicked (object sender, EventArgs e)
    {
        if (originalStyle) {
            Resources ["searchBarStyle"] = Resources ["greenSearchBarStyle"];
            originalStyle = false;
        } else {
            Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];
            originalStyle = true;
        }
    }
}
```

当 `OnButtonClicked` 执行事件处理程序时， `searchBarStyle` 将在和之间切换 `blueSearchBarStyle` `greenSearchBarStyle` 。 这会导致如以下屏幕截图中所示的外观：

[ ![ 蓝色动态样式示例](dynamic-images/dynamic-style-blue.png)](dynamic-images/dynamic-style-blue-large.png#lightbox) 
 [ ![ 绿色动态样式示例](dynamic-images/dynamic-style-green.png)](dynamic-images/dynamic-style-green-large.png#lightbox)

下面的代码示例演示了 c # 中的等效页：

```csharp
public class DynamicStylesPageCS : ContentPage
{
    bool originalStyle = true;

    public DynamicStylesPageCS ()
    {
        ...
        var baseStyle = new Style (typeof(View)) {
            ...
        };
        var blueSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        var greenSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        ...
        var searchBar1 = new SearchBar { Placeholder = "These SearchBar controls" };
        searchBar1.SetDynamicResource (VisualElement.StyleProperty, "searchBarStyle");
        ...
        Resources = new ResourceDictionary ();
        Resources.Add ("blueSearchBarStyle", blueSearchBarStyle);
        Resources.Add ("greenSearchBarStyle", greenSearchBarStyle);
        Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];

        Content = new StackLayout {
            Children = { searchBar1, searchBar2, searchBar3, searchBar4,    button    }
        };
    }
    ...
}
```

在 c # 中， [`SearchBar`](xref:Xamarin.Forms.SearchBar) 实例使用 [`SetDynamicResource`](xref:Xamarin.Forms.Element.SetDynamicResource*) 方法来引用 `searchBarStyle` 。 `OnButtonClicked`事件处理程序代码与 XAML 示例相同，执行时， `searchBarStyle` 将在和之间切换 `blueSearchBarStyle` `greenSearchBarStyle` 。

## <a name="dynamic-style-inheritance"></a>动态样式继承

无法使用属性从动态样式中派生样式 [`Style.BasedOn`](xref:Xamarin.Forms.Style.BasedOn) 。 相反， [`Style`](xref:Xamarin.Forms.Style) 该类包含 [`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey) 属性，该属性可以设置为字典键，其值可能会动态更改。

下面的代码示例演示了 XAML 页中的 *动态* 样式继承：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.DynamicStylesInheritancePage" Title="Dynamic Inheritance" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="baseStyle" TargetType="View">
              ...
            </Style>
            <Style x:Key="blueSearchBarStyle" TargetType="SearchBar" BasedOn="{StaticResource baseStyle}">
              ...
            </Style>
            <Style x:Key="greenSearchBarStyle" TargetType="SearchBar">
              ...
            </Style>
            <Style x:Key="tealSearchBarStyle" TargetType="SearchBar" BaseResourceKey="searchBarStyle">
              ...
            </Style>
            ...
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <SearchBar Text="These SearchBar controls" Style="{StaticResource tealSearchBarStyle}" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

[`SearchBar`](xref:Xamarin.Forms.SearchBar)实例使用 `StaticResource` 标记扩展来引用 [`Style`](xref:Xamarin.Forms.Style) 已命名的 `tealSearchBarStyle` 。 这会 `Style` 设置一些附加属性，并使用 [`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey) 属性来引用 `searchBarStyle` 。 `DynamicResource`标记扩展不是必需的 `tealSearchBarStyle` ，因为将不会更改，但 `Style` 它派生自。 因此， `tealSearchBarStyle` `searchBarStyle` 会在基本样式发生变化时，维持指向和的链接。

在代码隐藏文件中，构造函数创建一个 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 包含键的项 `searchBarStyle` ，这与上面演示动态样式的示例有关。 当 `OnButtonClicked` 执行事件处理程序时， `searchBarStyle` 将在和之间切换 `blueSearchBarStyle` `greenSearchBarStyle` 。 这会导致如以下屏幕截图中所示的外观：

[ ![ 蓝色动态样式继承示例](dynamic-images/dynamic-style-inheritance-blue.png)](dynamic-images/dynamic-style-inheritance-blue-large.png#lightbox) 
 [ ![ 绿色动态样式继承示例](dynamic-images/dynamic-style-inheritance-green.png)](dynamic-images/dynamic-style-inheritance-green-large.png#lightbox)

下面的代码示例演示了 c # 中的等效页：

```csharp
public class DynamicStylesInheritancePageCS : ContentPage
{
    bool originalStyle = true;

    public DynamicStylesInheritancePageCS ()
    {
        ...
        var baseStyle = new Style (typeof(View)) {
            ...
        };
        var blueSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        var greenSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        var tealSearchBarStyle = new Style (typeof(SearchBar)) {
            BaseResourceKey = "searchBarStyle",
            ...
        };
        ...
        Resources = new ResourceDictionary ();
        Resources.Add ("blueSearchBarStyle", blueSearchBarStyle);
        Resources.Add ("greenSearchBarStyle", greenSearchBarStyle);
        Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];

        Content = new StackLayout {
            Children = {
                new SearchBar { Text = "These SearchBar controls", Style = tealSearchBarStyle },
                ...
            }
        };
    }
    ...
}
```

`tealSearchBarStyle`直接分配给 [`Style`](xref:Xamarin.Forms.NavigableElement.Style) 实例的属性 [`SearchBar`](xref:Xamarin.Forms.SearchBar) 。 这会 `Style` 设置一些附加属性，并使用 [`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey) 属性来引用 `searchBarStyle` 。 [`SetDynamicResource`](xref:Xamarin.Forms.Element.SetDynamicResource*)此处不需要此方法 `tealSearchBarStyle` ，因为它不会更改，但 `Style` 它派生自。 因此， `tealSearchBarStyle` `searchBarStyle` 会在基本样式发生变化时，维持指向和的链接。

## <a name="related-links"></a>相关链接

- [XAML 标记扩展](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [动态样式 (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-styles-dynamicstyles)
- [使用样式 (示例) ](/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [样式](xref:Xamarin.Forms.Style)
- [](xref:Xamarin.Forms.Setter)Setter

## <a name="related-video"></a>相关视频

> [!Video https://channel9.msdn.com/Shows/XamarinShow/XamarinForms-101-Dynamic-Resources/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]