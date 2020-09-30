---
title: ListView 数据源
description: 本文介绍如何 Xamarin.Forms 使用数据填充 listview，以及如何将数据绑定与 ListView 一起使用。
ms.prod: xamarin
ms.assetid: B5571660-1E82-4379-95C3-0725288CF5D9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/23/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c77a219ab1b729aa279708d04610911fbbfe4e81
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91560125"
---
# <a name="listview-data-sources"></a>ListView 数据源

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-switchentrytwobinding)

Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) 用于显示数据列表。 本文介绍如何使用数据填充， `ListView` 以及如何将数据绑定到所选项目。

## <a name="itemssource"></a>ItemsSource

[`ListView`](xref:Xamarin.Forms.ListView)使用属性填充数据 [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) ，此属性可接受任何实现 `IEnumerable` 的集合。 填充的最简单方法是 `ListView` 使用字符串数组：

```xaml
<ListView>
      <ListView.ItemsSource>
          <x:Array Type="{x:Type x:String}">
            <x:String>mono</x:String>
            <x:String>monodroid</x:String>
            <x:String>monotouch</x:String>
            <x:String>monorail</x:String>
            <x:String>monodevelop</x:String>
            <x:String>monotone</x:String>
            <x:String>monopoly</x:String>
            <x:String>monomodal</x:String>
            <x:String>mononucleosis</x:String>
          </x:Array>
      </ListView.ItemsSource>
</ListView>
```

等效 C# 代码如下：

```csharp
var listView = new ListView();
listView.ItemsSource = new string[]
{
  "mono",
  "monodroid",
  "monotouch",
  "monorail",
  "monodevelop",
  "monotone",
  "monopoly",
  "monomodal",
  "mononucleosis"
};
```

![ListView 显示字符串列表](data-and-databinding-images/itemssource-simple.png)

此方法将 `ListView` 使用字符串列表填充。 默认情况下， `ListView` 将 `ToString` 为每行调用并显示中的结果 `TextCell` 。 若要自定义数据的显示方式，请参阅 [单元格外观](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)。

由于已 `ItemsSource` 发送到数组，因此，当基础列表或数组发生变化时，内容将不会更新。 如果要在基础列表中添加、删除和更改项时，ListView 自动更新，则需要使用 `ObservableCollection` 。 [`ObservableCollection`](xref:System.Collections.ObjectModel.ObservableCollection`1) 在和中定义的与 `System.Collections.ObjectModel` 类似 `List` ，只不过它可以通知 `ListView` 所有更改：

```csharp
ObservableCollection<Employee> employees = new ObservableCollection<Employee>();
listView.ItemsSource = employees;

//Mr. Mono will be added to the ListView because it uses an ObservableCollection
employees.Add(new Employee(){ DisplayName="Mr. Mono"});
```

## <a name="data-binding"></a>数据绑定

数据绑定是将用户界面对象的属性绑定到某个 CLR 对象（如 viewmodel 中的类）的属性的 "粘附"。 数据绑定非常有用，因为它通过替换大量乏味的样板代码来简化用户界面的开发。

数据绑定的工作原理是将对象保持同步，因为它们的绑定值会发生更改。 无需在每次更改控件值时都编写事件处理程序，只需在 viewmodel 中建立绑定并启用绑定。

有关数据绑定的详细信息，请参阅[数据绑定基础知识](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)，它是[ Xamarin.Forms XAML 基础文章系列](~/xamarin-forms/xaml/xaml-basics/index.md)中的第四部分。

### <a name="binding-cells"></a>绑定单元

单元格) 的单元格的属性 (可以绑定到中的对象的属性 `ItemsSource` 。 例如， `ListView` 可以使用来显示雇员列表。

Employee 类：

```csharp
public class Employee
{
    public string DisplayName {get; set;}
}
```

`ObservableCollection<Employee>`创建，将设置为 `ListView` `ItemsSource` ，并使用数据填充列表：

```csharp
ObservableCollection<Employee> employees = new ObservableCollection<Employee>();
public ObservableCollection<Employee> Employees { get { return employees; }}

public EmployeeListPage()
{
    EmployeeView.ItemsSource = employees;

    // ObservableCollection allows items to be added after ItemsSource
    // is set and the UI will react to changes
    employees.Add(new Employee{ DisplayName="Rob Finnerty"});
    employees.Add(new Employee{ DisplayName="Bill Wrestler"});
    employees.Add(new Employee{ DisplayName="Dr. Geri-Beth Hooper"});
    employees.Add(new Employee{ DisplayName="Dr. Keith Joyce-Purdy"});
    employees.Add(new Employee{ DisplayName="Sheri Spruce"});
    employees.Add(new Employee{ DisplayName="Burt Indybrick"});
}
```

> [!WARNING]
> 虽然 `ListView` 将更新以响应其基础的更改，但 `ObservableCollection` 如果将 `ListView` 不同 `ObservableCollection` 的实例分配给原始 `ObservableCollection` 引用 (例如) ，则将不会更新 `employees = otherObservableCollection;` 。

下面的代码片段演示了一个 `ListView` 绑定到员工列表的：

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:constants="clr-namespace:XamarinFormsSample;assembly=XamarinFormsXamlSample"
             x:Class="XamarinFormsXamlSample.Views.EmployeeListPage"
             Title="Employee List">
  <ListView x:Name="EmployeeView"
            ItemsSource="{Binding Employees}">
    <ListView.ItemTemplate>
      <DataTemplate>
        <TextCell Text="{Binding DisplayName}" />
      </DataTemplate>
    </ListView.ItemTemplate>
  </ListView>
</ContentPage>
```

此 XAML 示例定义一个 `ContentPage` 包含的 `ListView` 。 可通过 `ItemsSource` 属性设置 `ListView` 的数据源。 在 `ListView.ItemTemplate` 元素内定义 `ItemsSource` 中每一行的布局。 这会生成以下屏幕截图：

![使用数据绑定的 ListView](data-and-databinding-images/bound-data.png)

> [!WARNING]
> `ObservableCollection` 不是线程安全的。 修改 `ObservableCollection` 会导致 UI 更新在执行修改的同一线程上发生。 如果该线程不是主 UI 线程，则会引发异常。

### <a name="binding-selecteditem"></a>绑定 SelectedItem

通常，您将需要绑定到的选定项 `ListView` ，而不是使用事件处理程序来响应更改。 若要在 XAML 中执行此操作，请绑定 `SelectedItem` 属性：

```xaml
<ListView x:Name="listView"
          SelectedItem="{Binding Source={x:Reference SomeLabel},
          Path=Text}">
 …
</ListView>
```

假设 `listView` `ItemsSource` 是字符串列表， `SomeLabel` 则会将其 `Text` 属性绑定到 `SelectedItem` 。

## <a name="related-links"></a>相关链接

- [双向绑定 (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-listview-switchentrytwobinding)