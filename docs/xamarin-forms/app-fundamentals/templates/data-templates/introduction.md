---
title: Xamarin.Forms 数据模板简介
description: Xamarin.Forms 数据模板让你可以在支持的控件上定义数据表示形式。 本文介绍了数据模板，并且分析了它们必不可少的原因。
ms.prod: xamarin
ms.assetid: 4ED4ACF4-BE4A-44ED-8EAF-C03947B8663B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 28dbbe6411983e1fd7986413ceac69a5543c4bb4
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562569"
---
# <a name="introduction-to-no-locxamarinforms-data-templates"></a>Xamarin.Forms 数据模板简介

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)

_Xamarin.Forms 数据模板让你可以在支持的控件上定义数据表示形式。本文介绍了数据模板，并且分析了它们必不可少的原因。_

以显示 `Person` 对象集合的 [`ListView`](xref:Xamarin.Forms.ListView) 为例。 以下代码示例展示了 `Person` 类的定义：

```csharp
public class Person
{
    public string Name { get; set; }
    public int Age { get; set; }
    public string Location { get; set; }
}
```

`Person` 类定义 `Name`、`Age` 和 `Location` 属性，这些属性可在创建 `Person` 对象时设置。 [`ListView`](xref:Xamarin.Forms.ListView) 用于显示 `Person` 对象的集合，如以下 XAML 代码示例所示：

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
                    <local:Person Name="John" Age="37" Location="USA" />
                    <local:Person Name="Tom" Age="42" Location="UK" />
                    <local:Person Name="Lucas" Age="29" Location="Germany" />
                    <local:Person Name="Tariq" Age="39" Location="UK" />
                    <local:Person Name="Jane" Age="30" Location="USA" />
                </x:Array>
            </ListView.ItemsSource>
        </ListView>
    </StackLayout>
</ContentPage>
```

通过从 `Person` 实例数组初始化 [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) 属性，在 XAML 中向 [`ListView`](xref:Xamarin.Forms.ListView) 添加项目。

> [!NOTE]
> 请注意，`x:Array` 元素需要用于指示数组中项目类型的 `Type` 属性。

以下代码示例展示了等效的 C# 页面，该示例将 [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) 属性初始化为 `Person` 实例的 `List`：

```csharp
public WithoutDataTemplatePageCS()
{
    ...
    var people = new List<Person>
    {
        new Person { Name = "Steve", Age = 21, Location = "USA" },
        new Person { Name = "John", Age = 37, Location = "USA" },
        new Person { Name = "Tom", Age = 42, Location = "UK" },
        new Person { Name = "Lucas", Age = 29, Location = "Germany" },
        new Person { Name = "Tariq", Age = 39, Location = "UK" },
        new Person { Name = "Jane", Age = 30, Location = "USA" }
    };

    Content = new StackLayout
    {
        Margin = new Thickness(20),
        Children = {
            ...
            new ListView { ItemsSource = people, Margin = new Thickness(0, 20, 0, 0) }
        }
    };
}
```

当显示集合中的对象时，[`ListView`](xref:Xamarin.Forms.ListView) 调用 `ToString`。 由于没有 `Person.ToString` 重写，`ToString` 会返回每个对象的类型名称，如以下屏幕截图所示：

![不使用数据模板的 ListView](introduction-images/no-data-template.png)

`Person` 对象可以重写 `ToString` 方法以显示有意义的数据，如以下代码示例所示：

```csharp
public class Person
{
  ...
  public override string ToString ()
  {
    return Name;
  }
}
```

这导致 [`ListView`](xref:Xamarin.Forms.ListView) 显示集合中每个对象的 `Person.Name` 属性值，如以下屏幕截图所示：

![使用数据模板的 ListView](introduction-images/override-tostring.png)

`Person.ToString` 重写可能会返回由 `Name`、`Age` 和 `Location` 属性组成的格式化字符串。 但是，此方法仅对每个数据项的外观提供有限的控制。 为了提高灵活性，可以创建 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 来定义数据的外观。

## <a name="creating-a-datatemplate"></a>创建 DataTemplate

[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 用于指定数据的外观，并且通常使用数据绑定来显示数据。 它的常见使用场景是在 [`ListView`](xref:Xamarin.Forms.ListView) 中显示来自对象集合的数据。 例如，当 `ListView` 绑定到 `Person` 对象集合时，`ListView.ItemTemplate` 属性将设置为定义 `ListView` 中每个 `Person` 对象的外观的 `DataTemplate`。 `DataTemplate` 将包含绑定到每个 `Person` 对象的属性值的元素。 若要深入了解数据绑定，请参阅[数据绑定基本知识](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)。

[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 可用作以下属性的值：

- [`ListView.HeaderTemplate`](xref:Xamarin.Forms.ListView.HeaderTemplate)
- [`ListView.FooterTemplate`](xref:Xamarin.Forms.ListView.FooterTemplate)
- [`ListView.GroupHeaderTemplate`](xref:Xamarin.Forms.ListView.GroupHeaderTemplate)
- [`ItemsView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1)，由 [`ListView`](xref:Xamarin.Forms.ListView) 继承。
- [`MultiPage.ItemTemplate`](xref:Xamarin.Forms.MultiPage`1)，由 [`CarouselPage`](xref:Xamarin.Forms.CarouselPage)、[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 和 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 继承。

> [!NOTE]
> 请注意，虽然 [`TableView`](xref:Xamarin.Forms.TableView) 使用 [`Cell`](xref:Xamarin.Forms.Cell) 对象，但它不使用 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)。 这是因为数据绑定始终直接在 `Cell` 对象上设置。

作为上面所列属性的直接子级放置的 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 称为*内联模板*。 或者，可以将 `DataTemplate` 定义为控件级别、页面级别或应用程序级别资源。 选择在何处定义 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 会影响其应用范围：

- 在控件级别定义的 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 只能应用于控件。
- 在页面级别定义的 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 可以应用于页面上的多个有效控件。
- 在应用程序级别定义的 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 可以应用于整个应用程序中的有效控件。

当视图层次结构中的数据模板共享 `x:Key` 属性时，层次较低的数据模板优先于在较高层次定义的数据模板。 例如，应用程序级别的数据模板将被页面级别的数据模板替代，而页面级别的数据模板将被控件级别的数据模板或内联数据模板替代。

## <a name="related-links"></a>相关链接

- [单元格外观](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)
- [数据模板（示例）](/samples/xamarin/xamarin-forms-samples/templates-datatemplates)
- [DataTemplate](xref:Xamarin.Forms.DataTemplate)