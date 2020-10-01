---
title: 创建 Xamarin.Forms DataTemplate
description: 可通过内联方式创建数据模板，也可在 ResourceDictionary 中或根据自定义类型或适当的 Xamarin.Forms 单元类型进行创建。 本文对每种技术进行了探讨。
ms.prod: xamarin
ms.assetid: CFF4AB5E-9069-461C-84D8-F9F6C38510AB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f4ca18a34dc66a81fd30ce4ae2b60e31df8cffb5
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562608"
---
# <a name="creating-a-no-locxamarinforms-datatemplate"></a>创建 Xamarin.Forms DataTemplate

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)

_可通过内联方式创建数据模板，也可在 ResourceDictionary 中或根据自定义类型或适当的 Xamarin.Forms 单元类型进行创建。本文对每种技术进行了探讨。_

[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 的常见使用场景是在 [`ListView`](xref:Xamarin.Forms.ListView) 中显示来自对象集合的数据。 可通过将 [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) 属性设置为 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)，来管理 [`ListView`](xref:Xamarin.Forms.ListView) 中每个单元的数据外观。 可使用多种技术实现此目的：

- [创建内联 DataTemplate](#creating-an-inline-datatemplate)。
- [使用类型创建 DataTemplate](#creating-a-datatemplate-with-a-type)。
- [将 DataTemplate 创建为资源](#creating-a-datatemplate-as-a-resource)。

无论使用何种技术，结果都是 [`ListView`](xref:Xamarin.Forms.ListView) 中每个单元的外观由 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 定义，如以下屏幕截图所示：

![使用 DataTemplate 的 ListView](creating-images/data-template-appearance.png)

## <a name="creating-an-inline-datatemplate"></a>创建内联 DataTemplate

可以将 [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) 属性设置为内联 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)。 如果不需要在其他地方重复使用数据模板，则应使用内联模板，该模板作为相应控件属性的直接子级进行放置。 `DataTemplate` 中指定的元素定义每个单元的外观，如以下 XAML 代码示例所示：

```xaml
<ListView Margin="0,20,0,0">
    <ListView.ItemsSource>
        <x:Array Type="{x:Type local:Person}">
            <local:Person Name="Steve" Age="21" Location="USA" />
            <local:Person Name="John" Age="37" Location="USA" />
            <local:Person Name="Tom" Age="42" Location="UK" />
            <local:Person Name="Lucas" Age="29" Location="Germany" />
            <local:Person Name="Tariq" Age="39" Location="UK" />
            <local:Person Name="Jane" Age="30" Location="USA" />
        </x:Array>
    </ListView.ItemsSource>
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
                <Grid>
                    ...
                    <Label Text="{Binding Name}" FontAttributes="Bold" />
                    <Label Grid.Column="1" Text="{Binding Age}" />
                    <Label Grid.Column="2" Text="{Binding Location}" HorizontalTextAlignment="End" />
                </Grid>
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

内联 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 的子级必须属于或派生自 [`Cell`](xref:Xamarin.Forms.Cell) 类型。 本示例使用派生自 `Cell` 的 [`ViewCell`](xref:Xamarin.Forms.ViewCell)。 `ViewCell` 内的布局在此处由 [`Grid`](xref:Xamarin.Forms.Grid) 进行管理。 `Grid` 包含三个 [`Label`](xref:Xamarin.Forms.Label) 实例，这些实例将其 [`Text`](xref:Xamarin.Forms.Label.Text) 属性绑定到集合中每个 `Person` 对象的相应属性。

以下代码示例显示相应的 C# 代码：

```csharp
public class WithDataTemplatePageCS : ContentPage
{
    public WithDataTemplatePageCS()
    {
        ...
        var people = new List<Person>
        {
            new Person { Name = "Steve", Age = 21, Location = "USA" },
            ...
        };

        var personDataTemplate = new DataTemplate(() =>
        {
            var grid = new Grid();
            ...
            var nameLabel = new Label { FontAttributes = FontAttributes.Bold };
            var ageLabel = new Label();
            var locationLabel = new Label { HorizontalTextAlignment = TextAlignment.End };

            nameLabel.SetBinding(Label.TextProperty, "Name");
            ageLabel.SetBinding(Label.TextProperty, "Age");
            locationLabel.SetBinding(Label.TextProperty, "Location");

            grid.Children.Add(nameLabel);
            grid.Children.Add(ageLabel, 1, 0);
            grid.Children.Add(locationLabel, 2, 0);

            return new ViewCell { View = grid };
        });

        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children = {
                ...
                new ListView { ItemsSource = people, ItemTemplate = personDataTemplate, Margin = new Thickness(0, 20, 0, 0) }
            }
        };
    }
}
```

在 C# 中，内联 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 是使用指定 `Func` 参数的构造函数重载创建的。

## <a name="creating-a-datatemplate-with-a-type"></a>使用类型创建 DataTemplate

也可以将 [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) 属性设置为根据某个单元类型创建的 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)。 这种方法的优点在于，由该单元类型定义的外观可由整个应用程序中的多个数据模板重复使用。 以下 XAML 代码展示了此方法的一个示例：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataTemplates"
             ...>
    <StackLayout Margin="20">
        ...
        <ListView Margin="0,20,0,0">
           <ListView.ItemsSource>
                <x:Array Type="{x:Type local:Person}">
                    <local:Person Name="Steve" Age="21" Location="USA" />
                    ...
                </x:Array>
            </ListView.ItemsSource>
            <ListView.ItemTemplate>
                <DataTemplate>
                    <local:PersonCell />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

在这里，[`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) 属性设置为根据定义单元外观的自定义类型创建的 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)。 该自定义类型必须派生自 [`ViewCell`](xref:Xamarin.Forms.ViewCell) 类型，如以下代码示例所示：

```xaml
<ViewCell xmlns="http://xamarin.com/schemas/2014/forms"
          xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
          x:Class="DataTemplates.PersonCell">
     <Grid>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="0.5*" />
            <ColumnDefinition Width="0.2*" />
            <ColumnDefinition Width="0.3*" />
        </Grid.ColumnDefinitions>
        <Label Text="{Binding Name}" FontAttributes="Bold" />
        <Label Grid.Column="1" Text="{Binding Age}" />
        <Label Grid.Column="2" Text="{Binding Location}" HorizontalTextAlignment="End" />
    </Grid>
</ViewCell>
```

[`ViewCell`](xref:Xamarin.Forms.ViewCell) 内的布局在此处由 [`Grid`](xref:Xamarin.Forms.Grid) 进行管理。 `Grid` 包含三个 [`Label`](xref:Xamarin.Forms.Label) 实例，这些实例将其 [`Text`](xref:Xamarin.Forms.Label.Text) 属性绑定到集合中每个 `Person` 对象的相应属性。

以下示例展示了等效的 C# 代码：

```csharp
public class WithDataTemplatePageFromTypeCS : ContentPage
{
    public WithDataTemplatePageFromTypeCS()
    {
        ...
        var people = new List<Person>
        {
            new Person { Name = "Steve", Age = 21, Location = "USA" },
            ...
        };

        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children = {
                ...
                new ListView { ItemTemplate = new DataTemplate(typeof(PersonCellCS)), ItemsSource = people, Margin = new Thickness(0, 20, 0, 0) }
            }
        };
    }
}
```

在 C# 中，[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 是使用构造函数重载创建的，该重载将单元类型指定为参数。 单元类型必须派生自 [`ViewCell`](xref:Xamarin.Forms.ViewCell) 类型，如以下代码示例所示：

```csharp
public class PersonCellCS : ViewCell
{
    public PersonCellCS()
    {
        var grid = new Grid();
        ...
        var nameLabel = new Label { FontAttributes = FontAttributes.Bold };
        var ageLabel = new Label();
        var locationLabel = new Label { HorizontalTextAlignment = TextAlignment.End };

        nameLabel.SetBinding(Label.TextProperty, "Name");
        ageLabel.SetBinding(Label.TextProperty, "Age");
        locationLabel.SetBinding(Label.TextProperty, "Location");

        grid.Children.Add(nameLabel);
        grid.Children.Add(ageLabel, 1, 0);
        grid.Children.Add(locationLabel, 2, 0);

        View = grid;
    }
}
```

> [!NOTE]
> 请注意，Xamarin.Forms 还包括可用于在 [`ListView`](xref:Xamarin.Forms.ListView) 单元中显示简单数据的单元类型。 有关详细信息，请参阅[单元外观](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)。

## <a name="creating-a-datatemplate-as-a-resource"></a>将 DataTemplate 创建为资源

也可以将数据模板创建为 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 中的可重用对象。 这是通过为每个声明赋予一个唯一的 `x:Key` 属性来实现的，此属性为声明提供 `ResourceDictionary` 中的描述性键，如以下 XAML 代码示例所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             ...>
    <ContentPage.Resources>
        <ResourceDictionary>
            <DataTemplate x:Key="personTemplate">
                 <ViewCell>
                    <Grid>
                        ...
                    </Grid>
                </ViewCell>
            </DataTemplate>
        </ResourceDictionary>
    </ContentPage.Resources>
    <StackLayout Margin="20">
        ...
        <ListView ItemTemplate="{StaticResource personTemplate}" Margin="0,20,0,0">
            <ListView.ItemsSource>
                <x:Array Type="{x:Type local:Person}">
                    <local:Person Name="Steve" Age="21" Location="USA" />
                    ...
                </x:Array>
            </ListView.ItemsSource>
        </ListView>
    </StackLayout>
</ContentPage>
```

使用 `StaticResource` 标记扩展将 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 分配给 [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) 属性。 请注意，虽然 `DataTemplate` 是在页面的 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 中定义的，但它也可以在控件级别或应用程序级别定义。

以下代码示例展示了 C# 中的等效页面：

```csharp
public class WithDataTemplatePageCS : ContentPage
{
  public WithDataTemplatePageCS ()
  {
    ...
    var personDataTemplate = new DataTemplate (() => {
      var grid = new Grid ();
      ...
      return new ViewCell { View = grid };
    });

    Resources = new ResourceDictionary ();
    Resources.Add ("personTemplate", personDataTemplate);

    Content = new StackLayout {
      Margin = new Thickness(20),
      Children = {
        ...
        new ListView { ItemTemplate = (DataTemplate)Resources ["personTemplate"], ItemsSource = people };
      }
    };
  }
}
```

使用 [`Add`](xref:Xamarin.Forms.ResourceDictionary.Add(System.String,System.Object)) 方法将 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 添加到 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)，该方法指定了用于在检索到 `DataTemplate` 时对其进行引用的 `Key` 字符串。

## <a name="summary"></a>总结

本文说明了如何以内联方式、根据自定义类型或在 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 中创建数据模板。 如果不需要在其他地方重复使用数据模板，则应使用内联模板。 或者，可将数据模板定义为自定义类型或控件级别、页面级别或应用程序级别资源，从而重复使用数据模板。

## <a name="related-links"></a>相关链接

- [单元格外观](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)
- [数据模板（示例）](/samples/xamarin/xamarin-forms-samples/templates-datatemplates)
- [DataTemplate](xref:Xamarin.Forms.DataTemplate)