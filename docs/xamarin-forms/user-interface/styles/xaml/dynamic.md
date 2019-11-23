---
title: 在 Xamarin.Forms 中的动态样式
description: 本文介绍如何 Xamarin.Forms 应用程序可以响应在运行时动态样式更改通过使用动态资源。
ms.prod: xamarin
ms.assetid: 13D4FA4B-DF10-42BF-B001-2C49367FC216
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/28/2019
ms.custom: video
ms.openlocfilehash: 9a26532d13b843b812da94739be071c7accac212
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2019
ms.locfileid: "70228190"
---
# <a name="dynamic-styles-in-xamarinforms"></a>在 Xamarin.Forms 中的动态样式

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-dynamicstyles)

_样式不会对属性更改做出响应，并在应用程序持续时间内保持不变。例如，在将样式分配给视觉对象后，如果修改、删除或添加了新的 Setter 实例，则不会将更改应用于可视元素。但是，应用程序可以使用动态资源在运行时动态响应样式更改。_

`DynamicResource`标记扩展是类似于`StaticResource`都使用字典键提取从值中的标记扩展[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)。 然而，尽管`StaticResource`执行单个字典查找，`DynamicResource`维护字典键的链接。 因此，如果替换与键关联的字典条目，更改将应用到可视元素。 这样，在应用程序中进行的运行时样式更改。

下面的代码示例演示*动态*XAML 页面中的样式：

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

[ `SearchBar` ](xref:Xamarin.Forms.SearchBar)实例使用`DynamicResource`标记扩展引用[ `Style` ](xref:Xamarin.Forms.Style)名为`searchBarStyle`，未在 XAML 中定义。 但是，由于[ `Style` ](xref:Xamarin.Forms.NavigableElement.Style)的属性`SearchBar`集使用的实例`DynamicResource`，缺失的字典键不会产生引发了异常。

相反，在代码隐藏文件中，该构造函数创建[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)具有键的项`searchBarStyle`，下面的代码示例中所示：

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

当`OnButtonClicked`执行事件处理程序时，`searchBarStyle`会之间切换`blueSearchBarStyle`和`greenSearchBarStyle`。 这会导致如以下屏幕截图中所示的外观：

[![蓝色动态样式示例](dynamic-images/dynamic-style-blue.png)](dynamic-images/dynamic-style-blue-large.png#lightbox)
[![绿色动态样式示例](dynamic-images/dynamic-style-green.png)](dynamic-images/dynamic-style-green-large.png#lightbox)

下面的代码示例演示如何在 C# 中的等效页：

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

在 C# 中， [ `SearchBar` ](xref:Xamarin.Forms.SearchBar)实例使用[ `SetDynamicResource` ](xref:Xamarin.Forms.Element.SetDynamicResource*)要引用方法`searchBarStyle`。 `OnButtonClicked`事件处理程序代码等同于 XAML 的示例，并执行时，`searchBarStyle`会之间切换`blueSearchBarStyle`和`greenSearchBarStyle`。

## <a name="dynamic-style-inheritance"></a>动态样式继承

派生自动态样式的样式不能使用也能得到[ `Style.BasedOn` ](xref:Xamarin.Forms.Style.BasedOn)属性。 相反， [ `Style` ](xref:Xamarin.Forms.Style)类包括[ `BaseResourceKey` ](xref:Xamarin.Forms.Style.BaseResourceKey)可能会动态更改属性，可以将其值设置为字典键。

下面的代码示例演示*动态*设置在 XAML 页面中的样式继承：

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

[ `SearchBar` ](xref:Xamarin.Forms.SearchBar)实例使用`StaticResource`标记扩展引用[ `Style` ](xref:Xamarin.Forms.Style)名为`tealSearchBarStyle`。 这`Style`设置其他一些属性，并使用[ `BaseResourceKey` ](xref:Xamarin.Forms.Style.BaseResourceKey)属性来引用`searchBarStyle`。 `DynamicResource`标记扩展不需要，因为`tealSearchBarStyle`不会更改，除`Style`它派生。 因此，`tealSearchBarStyle`维护一个指向`searchBarStyle`和基准的样式更改时更改。

在代码隐藏文件中，该构造函数创建[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)具有键的项`searchBarStyle`、 每个前面的示例演示动态样式。 当`OnButtonClicked`执行事件处理程序时，`searchBarStyle`会之间切换`blueSearchBarStyle`和`greenSearchBarStyle`。 这会导致如以下屏幕截图中所示的外观：

[![蓝色动态样式继承示例](dynamic-images/dynamic-style-inheritance-blue.png)](dynamic-images/dynamic-style-inheritance-blue-large.png#lightbox)
[![绿色动态样式继承示例](dynamic-images/dynamic-style-inheritance-green.png)](dynamic-images/dynamic-style-inheritance-green-large.png#lightbox)

下面的代码示例演示如何在 C# 中的等效页：

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

`tealSearchBarStyle`直接分配给[ `Style` ](xref:Xamarin.Forms.NavigableElement.Style)属性[ `SearchBar` ](xref:Xamarin.Forms.SearchBar)实例。 这`Style`设置其他一些属性，并使用[ `BaseResourceKey` ](xref:Xamarin.Forms.Style.BaseResourceKey)属性来引用`searchBarStyle`。 [ `SetDynamicResource` ](xref:Xamarin.Forms.Element.SetDynamicResource*)方法不需要，此处因为`tealSearchBarStyle`不会更改，除`Style`它派生。 因此，`tealSearchBarStyle`维护一个指向`searchBarStyle`和基准的样式更改时更改。

## <a name="related-links"></a>相关链接

- [XAML 标记扩展](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [动态样式 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-dynamicstyles)
- [使用样式 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [样式](xref:Xamarin.Forms.Style)
- [资源库](xref:Xamarin.Forms.Setter)

## <a name="related-video"></a>相关视频

> [!Video https://channel9.msdn.com/Shows/XamarinShow/XamarinForms-101-Dynamic-Resources/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
