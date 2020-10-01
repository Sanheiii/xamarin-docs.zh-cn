---
title: 创建 Xamarin.Forms DataTemplateSelector
description: 本文演示如何创建和使用 DataTemplateSelector，它可以用于在运行时根据数据绑定属性的值选择 DataTemplate。
ms.prod: xamarin
ms.assetid: A4629E8F-2BAF-45CE-A76E-DF225FE8D26C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a0e84bd0bb6b2eb62ea5a25eefe6216ccdc08289
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562556"
---
# <a name="creating-a-no-locxamarinforms-datatemplateselector"></a>创建 Xamarin.Forms DataTemplateSelector

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplateselector)

DataTemplateSelector 可用于在运行时根据数据绑定属性的值来选择 DataTemplate。如此可将多个 DataTemplate 应用于同一类型的对象，以自定义特定对象的外观。本文演示如何创建和使用 DataTemplateSelector。

数据模板选择器支持多种方案，例如：将 [`ListView`](xref:Xamarin.Forms.ListView) 绑定到对象集合，其中返回特定 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 的数据模板选择器可以在运行时选择 `ListView` 中各对象的外观。

## <a name="creating-a-datatemplateselector"></a>创建 DataTemplateSelector

数据模板选择器是通过创建从 [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) 继承的类来实现的。 然后替代 `OnSelectTemplate` 方法以返回特定的 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)，如以下代码示例所示：

```csharp
public class PersonDataTemplateSelector : DataTemplateSelector
{
  public DataTemplate ValidTemplate { get; set; }
  public DataTemplate InvalidTemplate { get; set; }

  protected override DataTemplate OnSelectTemplate (object item, BindableObject container)
  {
    return ((Person)item).DateOfBirth.Year >= 1980 ? ValidTemplate : InvalidTemplate;
  }
}
```

`OnSelectTemplate` 方法根据 `DateOfBirth` 属性的值返回适当的模板。 要返回的模板是 `ValidTemplate` 属性或 `InvalidTemplate` 属性的值，这些属性是使用 `PersonDataTemplateSelector` 时设置的。

然后可以将数据模板选择器类的实例分配给 Xamarin.Forms 控件属性，如 [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1)。 有关有效属性的列表，请参阅[创建 DataTemplate](~/xamarin-forms/app-fundamentals/templates/data-templates/creating.md)。

### <a name="limitations"></a>限制

[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) 实例存在以下限制：

- 如果查询多次，`DataTemplateSelector` 子类必须始终为相同的数据返回相同的模板。
- `DataTemplateSelector` 子类不能返回另一个 `DataTemplateSelector` 子类。
- `DataTemplateSelector` 子类不能在每次调用时返回 `DataTemplate` 的新实例。 必须返回相同的实例。 如果做不到这一点，就会造成内存泄漏并禁用虚拟化。
- 在 Android 上，每个 `ListView` 最多只能有 20 个不同的数据模板。

## <a name="consuming-a-datatemplateselector-in-xaml"></a>在 XAML 中使用 DataTemplateSelector

在 XAML 中，可以通过将 `PersonDataTemplateSelector` 声明为资源来对它进行实例化，如以下代码示例所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" xmlns:local="clr-namespace:Selector;assembly=Selector" x:Class="Selector.HomePage">
    <ContentPage.Resources>
        <ResourceDictionary>
            <DataTemplate x:Key="validPersonTemplate">
                <ViewCell>
                   ...
                </ViewCell>
            </DataTemplate>
            <DataTemplate x:Key="invalidPersonTemplate">
                <ViewCell>
                   ...
                </ViewCell>
            </DataTemplate>
            <local:PersonDataTemplateSelector x:Key="personDataTemplateSelector"
                ValidTemplate="{StaticResource validPersonTemplate}"
                InvalidTemplate="{StaticResource invalidPersonTemplate}" />
        </ResourceDictionary>
    </ContentPage.Resources>
  ...
</ContentPage>
```

此页面级别 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 定义两个 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 实例和一个 `PersonDataTemplateSelector` 实例。 通过使用 `StaticResource` 标记扩展，`PersonDataTemplateSelector` 实例将其 `ValidTemplate` 和 `InvalidTemplate` 属性设置为相应的 `DataTemplate` 实例。 请注意，虽然资源是在页面的 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 中定义的，但也可以在控件级别或应用程序级别定义。

通过将 `PersonDataTemplateSelector` 实例分配给 [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) 属性来使用它，如下面的代码示例所示：

```xaml
<ListView x:Name="listView" ItemTemplate="{StaticResource personDataTemplateSelector}" />
```

在运行时，[`ListView`](xref:Xamarin.Forms.ListView) 为基础集合中的每个项调用 `PersonDataTemplateSelector.OnSelectTemplate` 方法，调用将数据对象作为 `item` 参数传递。 然后将方法返回的 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 应用于该对象。

以下屏幕截图显示了 [`ListView`](xref:Xamarin.Forms.ListView) 对基础集合中的每个对象应用 `PersonDataTemplateSelector` 的结果：

![带有数据模板选择器的 ListView](selector-images/data-template-selector.png)

任何 `DateOfBirth` 属性值大于或等于 1980 的 `Person` 对象都以绿色显示，其余对象以红色显示。

## <a name="consuming-a-datatemplateselector-in-cnum"></a>在 C&num; 中使用 DataTemplateSelector

在 C# 中，可以实例化 `PersonDataTemplateSelector` 并将其分配给 [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) 属性，如下面的代码示例所示：

```csharp
public class HomePageCS : ContentPage
{
  DataTemplate validTemplate;
  DataTemplate invalidTemplate;

  public HomePageCS ()
  {
    ...
    SetupDataTemplates ();
    var listView = new ListView {
      ItemsSource = people,
      ItemTemplate = new PersonDataTemplateSelector {
        ValidTemplate = validTemplate,
        InvalidTemplate = invalidTemplate }
    };

    Content = new StackLayout {
      Margin = new Thickness (20),
      Children = {
        ...
        listView
      }
    };
  }
  ...  
}
```

`PersonDataTemplateSelector` 实例将其 `ValidTemplate` 和 `InvalidTemplate` 属性设置为由 `SetupDataTemplates` 方法创建的相应的 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 实例。 在运行时，[`ListView`](xref:Xamarin.Forms.ListView) 为基础集合中的每个项调用 `PersonDataTemplateSelector.OnSelectTemplate` 方法，调用将数据对象作为 `item` 参数传递。 然后将方法返回的 `DataTemplate` 应用于该对象。

## <a name="summary"></a>总结

本文演示如何创建和使用 [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)。 `DataTemplateSelector` 可用于在运行时根据数据绑定属性的值来选择 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)。 如此可将多个 `DataTemplate` 实例应用于同一类型的对象，以自定义特定对象的外观。

## <a name="related-links"></a>相关链接

- [数据模板选择器（示例）](/samples/xamarin/xamarin-forms-samples/templates-datatemplateselector)
- [DataTemplateSelector](xref:Xamarin.Forms.DataTemplateSelector)