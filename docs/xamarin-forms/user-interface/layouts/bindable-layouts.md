---
title: 可绑定布局 Xamarin.Forms
description: 可绑定布局通过绑定到项的集合，使布局类能够生成其内容，并且可以选择使用 System.windows.datatemplate> 设置每个项的外观。
ms.prod: xamarin
ms.assetid: 824C3319-20A0-42D0-8632-CDECD98349C3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/09/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a5b1e43586810521e87360d8b6537d9bafdabad7
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91555432"
---
# <a name="bindable-layouts-in-no-locxamarinforms"></a>可绑定布局 Xamarin.Forms

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)

绑定布局支持从类派生的任何布局类 [`Layout<T>`](xref:Xamarin.Forms.Layout`1) ，通过绑定到项的集合来生成其内容，并且可以选择使用设置每个项的外观 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 。 可绑定布局由类提供 `BindableLayout` ，该类公开了以下附加属性：

- `ItemsSource` –指定 `IEnumerable` 布局要显示的项的集合。
- `ItemTemplate` –指定 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 要应用于布局所显示的项集合中的每个项的。
- `ItemTemplateSelector` –指定 [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) 将用于 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 在运行时为项选择的。

> [!NOTE]
> `ItemTemplate`当设置了和属性时，属性将优先 `ItemTemplate` `ItemTemplateSelector` 。

此外， `BindableLayout` 类公开以下可绑定属性：

- `EmptyView` –指定 `string` 当 `ItemsSource` 属性为时 `null` 或属性指定的集合 `ItemsSource` 为 `null` 或空时将显示的或视图。 默认值为 `null`。
- `EmptyViewTemplate` –指定在 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) `ItemsSource` 属性为时 `null` 或属性指定的集合 `ItemsSource` 为 `null` 或空时要显示的。 默认值为 `null`。

> [!NOTE]
> `EmptyViewTemplate`当设置了和属性时，属性将优先 `EmptyView` `EmptyViewTemplate` 。

所有这些属性都可以连接到、、 [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) 、 [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) [`Grid`](xref:Xamarin.Forms.Grid) [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) 和 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 类，它们都派生自 [`Layout<T>`](xref:Xamarin.Forms.Layout`1) 类。

`Layout<T>`类公开一个 [`Children`](xref:Xamarin.Forms.Layout`1.Children) 集合，其中向其中添加了布局的子元素。 当 `BinableLayout.ItemsSource` 属性设置为项的集合并附加到 [`Layout<T>`](xref:Xamarin.Forms.Layout`1) 派生类时，集合中的每一项都将添加到集合中， `Layout<T>.Children` 以供布局显示。 `Layout<T>`如果基础集合发生更改，则派生的类将更新其子视图。 有关布局周期的详细信息 Xamarin.Forms ，请参阅 [创建自定义布局](~/xamarin-forms/user-interface/layouts/custom.md)。

仅当要显示的项的集合较小时，并且不需要滚动和选择时，才应使用可绑定的布局。 虽然滚动可以通过在中包装可绑定布局来提供 [`ScrollView`](xref:Xamarin.Forms.ScrollView) ，但不建议将其作为可绑定布局缺少 UI 虚拟化。 需要滚动时，应该使用包括 UI 虚拟化的可滚动视图，例如 [`ListView`](xref:Xamarin.Forms.ListView) 或 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 。 如果未遵守此建议，可能会导致性能问题。

> [!IMPORTANT]
> 尽管从技术上讲，可以将可绑定布局附加到从类派生的任何布局类，但执行此操作 [`Layout<T>`](xref:Xamarin.Forms.Layout`1) 并非总是可行的，尤其是对于 [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) 、 [`Grid`](xref:Xamarin.Forms.Grid) 和 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) 类。 例如，假设想要使用可绑定布局在中显示数据集合 [`Grid`](xref:Xamarin.Forms.Grid) ，其中的每个项都是一个包含多个属性的对象。 中的每一行 `Grid` 都应显示集合中的一个对象，其中的每一列都 `Grid` 显示一个对象的属性。 由于 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 可绑定布局的只能包含单个对象，因此该对象必须为包含多个视图的布局类，每个视图在特定列中显示对象的一个属性 `Grid` 。 尽管此方案可以使用可绑定的布局 realised，但它会生成一个父 `Grid` `Grid` 项，其中包含绑定集合中每个项的子元素，这是布局的高效率和有问题的用法 `Grid` 。

## <a name="populate-a-bindable-layout-with-data"></a>使用数据填充可绑定布局

可以通过将数据的 `ItemsSource` 属性设置为实现的任何集合 `IEnumerable` 并将其附加到派生类，来填充可绑定的布局 [`Layout<T>`](xref:Xamarin.Forms.Layout`1) ：

```xaml
<Grid BindableLayout.ItemsSource="{Binding Items}" />
```

等效 C# 代码如下：

```csharp
IEnumerable<string> items = ...;
var grid = new Grid();
BindableLayout.SetItemsSource(grid, items);
```

如果在 `BindableLayout.ItemsSource` 布局上设置附加属性，但 `BindableLayout.ItemTemplate` 未设置附加属性，则集合中的每个项 `IEnumerable` 都将由 [`Label`](xref:Xamarin.Forms.Label) 类创建的显示 `BindableLayout` 。

## <a name="define-item-appearance"></a>定义项外观

可以通过将附加属性设置为来定义可绑定布局中每个项的外观 `BindableLayout.ItemTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ：

```xaml
<StackLayout BindableLayout.ItemsSource="{Binding User.TopFollowers}"
             Orientation="Horizontal"
             ...>
    <BindableLayout.ItemTemplate>
        <DataTemplate>
            <controls:CircleImage Source="{Binding}"
                                  Aspect="AspectFill"
                                  WidthRequest="44"
                                  HeightRequest="44"
                                  ... />
        </DataTemplate>
    </BindableLayout.ItemTemplate>
</StackLayout>
```

等效 C# 代码如下：

```csharp
DataTemplate circleImageTemplate = ...;
var stackLayout = new StackLayout();
BindableLayout.SetItemsSource(stackLayout, viewModel.User.TopFollowers);
BindableLayout.SetItemTemplate(stackLayout, circleImageTemplate);
```

在此示例中，集合中的每个项 `TopFollowers` 都将由 `CircleImage` 中定义的视图显示 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ：

![具有 System.windows.datatemplate> 的可绑定布局](bindable-layouts-images/top-followers.png "使用数据模板的可绑定布局")

有关数据模板的详细信息，请参阅 [Xamarin.Forms 数据模板](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。

## <a name="choose-item-appearance-at-runtime"></a>在运行时选择项外观

可以在运行时根据项值在运行时选择每个项的外观，方法是将 `BindableLayout.ItemTemplateSelector` 附加属性设置为 [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) ：

```xaml
<FlexLayout BindableLayout.ItemsSource="{Binding User.FavoriteTech}"
            BindableLayout.ItemTemplateSelector="{StaticResource TechItemTemplateSelector}"
            ... />
```

等效 C# 代码如下：

```csharp
DataTemplateSelector dataTemplateSelector = new TechItemTemplateSelector { ... };
var flexLayout = new FlexLayout();
BindableLayout.SetItemsSource(flexLayout, viewModel.User.FavoriteTech);
BindableLayout.SetItemTemplateSelector(flexLayout, dataTemplateSelector);
```

[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)示例应用程序中使用的如下例所示：

```csharp
public class TechItemTemplateSelector : DataTemplateSelector
{
    public DataTemplate DefaultTemplate { get; set; }
    public DataTemplate XamarinFormsTemplate { get; set; }

    protected override DataTemplate OnSelectTemplate(object item, BindableObject container)
    {
        return (string)item == "Xamarin.Forms" ? XamarinFormsTemplate : DefaultTemplate;
    }
}
```

`TechItemTemplateSelector`类定义了 `DefaultTemplate` 并 `XamarinFormsTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 将属性设置为不同的数据模板。 `OnSelectTemplate`方法返回 `XamarinFormsTemplate` ，当项等于 "" 时，它会将项显示为深红色，并显示一项 Xamarin.Forms 。 当项不等于 "" 时 Xamarin.Forms ， `OnSelectTemplate` 方法返回 `DefaultTemplate` ，它使用的默认颜色来显示项 [`Label`](xref:Xamarin.Forms.Label) ：

![具有并重的可绑定布局](bindable-layouts-images/favorite-tech.png "使用数据模板选择器的可绑定布局")

有关数据模板选择器的详细信息，请参阅 [创建 Xamarin.Forms 并重](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)。

## <a name="display-a-string-when-data-is-unavailable"></a>数据不可用时显示字符串

`EmptyView`属性可以设置为字符串， [`Label`](xref:Xamarin.Forms.Label) 当 `ItemsSource` 属性为时 `null` ，或属性指定的集合 `ItemsSource` 为 `null` 或为空时，该字符串将由显示。 以下 XAML 显示了此方案的示例：

```xaml
<StackLayout BindableLayout.ItemsSource="{Binding UserWithoutAchievements.Achievements}"
             BindableLayout.EmptyView="No achievements">
    ...
</StackLayout>
```

结果是，当数据绑定集合为时 `null` ，将显示设置为 `EmptyView` 属性值的字符串：

[![IOS 和 Android 上的可绑定布局字符串空视图的屏幕截图](bindable-layouts-images/emptyview-string.png "可绑定布局字符串空视图")](bindable-layouts-images/emptyview-string-large.png#lightbox "可绑定布局字符串空视图")

## <a name="display-views-when-data-is-unavailable"></a>数据不可用时显示视图

`EmptyView`属性可以设置为视图，当 `ItemsSource` 属性为时 `null` ，或属性指定的集合 `ItemsSource` 为 `null` 或空时，该视图将显示。 这可以是单个视图，也可以是包含多个子视图的视图。 下面的 XAML 示例演示 `EmptyView` 如何将属性设置为包含多个子视图的视图：

```xaml
<StackLayout BindableLayout.ItemsSource="{Binding UserWithoutAchievements.Achievements}">
    <BindableLayout.EmptyView>
        <StackLayout>
            <Label Text="None."
                   FontAttributes="Italic"
                   FontSize="{StaticResource smallTextSize}" />
            <Label Text="Try harder and return later?"
                   FontAttributes="Italic"
                   FontSize="{StaticResource smallTextSize}" />
        </StackLayout>
    </BindableLayout.EmptyView>
    ...
</StackLayout>
```

结果是，当数据绑定集合为时 `null` ，将 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 显示及其子视图。

[![在 iOS 和 Android 上具有多个视图的可绑定布局空视图的屏幕截图](bindable-layouts-images/emptyview-views.png "可绑定布局空视图")](bindable-layouts-images/emptyview-views-large.png#lightbox "可绑定布局空视图")

同样， `EmptyViewTemplate` 可以将设置为 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ，当 `ItemsSource` 属性为时， `null` 或属性指定的集合 `ItemsSource` 为 `null` 或空时，将显示该。 `DataTemplate`可以包含单个视图或包含多个子视图的视图。 此外，的 `BindingContext` `EmptyViewTemplate` 将从的继承 `BindingContext` `BindableLayout` 。 下面的 XAML 示例演示 `EmptyViewTemplate` 如何将属性设置为 `DataTemplate` ，其中包含一个视图：

```xaml
<StackLayout BindableLayout.ItemsSource="{Binding UserWithoutAchievements.Achievements}">
    <BindableLayout.EmptyViewTemplate>
        <DataTemplate>
            <Label Text="{Binding Source={x:Reference usernameLabel}, Path=Text, StringFormat='{0} has no achievements.'}" />
        </DataTemplate>
    </BindableLayout.EmptyViewTemplate>
    ...
</StackLayout>
```

结果是，当数据绑定集合为时 `null` ，将 [`Label`](xref:Xamarin.Forms.Label) 显示中的 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ：

[![在 iOS 和 Android 上的可绑定布局的屏幕截图空视图模板](bindable-layouts-images/emptyviewtemplate.png "可绑定布局空视图模板")](bindable-layouts-images/emptyviewtemplate-large.png#lightbox "可绑定布局空视图模板")

> [!NOTE]
> `EmptyViewTemplate`不能通过设置属性 [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) 。

## <a name="choose-an-emptyview-at-runtime"></a>在运行时选择 EmptyView

数据不可用时将显示为的视图 `EmptyView` 可以定义为 [`ContentView`](xref:Xamarin.Forms.ContentView) 中的对象 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 。 `EmptyView`然后，可以在运行时将该属性设置为 `ContentView` 基于一些业务逻辑的特定。 以下 XAML 显示了此方案的示例：

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        ...    
        <ContentView x:Key="BasicEmptyView">
            <StackLayout>
                <Label Text="No achievements."
                       FontSize="14" />
            </StackLayout>
        </ContentView>
        <ContentView x:Key="AdvancedEmptyView">
            <StackLayout>
                <Label Text="None."
                       FontAttributes="Italic"
                       FontSize="14" />
                <Label Text="Try harder and return later?"
                       FontAttributes="Italic"
                       FontSize="14" />
            </StackLayout>
        </ContentView>
    </ContentPage.Resources>

    <StackLayout>
        ...
        <Switch Toggled="OnEmptyViewSwitchToggled" />

        <StackLayout x:Name="stackLayout"
                     BindableLayout.ItemsSource="{Binding UserWithoutAchievements.Achievements}">
            ...
        </StackLayout>
    </StackLayout>
</ContentPage>
```

XAML 定义了 [`ContentView`](xref:Xamarin.Forms.ContentView) 页级中的两个对象 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) ，并且 [`Switch`](xref:Xamarin.Forms.Switch) 对象控制将哪个 `ContentView` 对象设置为 `EmptyView` 属性值。 切换后 `Switch` ， `OnEmptyViewSwitchToggled` 事件处理程序将执行 `ToggleEmptyView` 方法：

```csharp
void ToggleEmptyView(bool isToggled)
{
    object view = isToggled ? Resources["BasicEmptyView"] : Resources["AdvancedEmptyView"];
    BindableLayout.SetEmptyView(stackLayout, view);
}
```

`ToggleEmptyView`方法 `EmptyView` 根据属性的值将对象的属性设置 `stackLayout` 为 [`ContentView`](xref:Xamarin.Forms.ContentView) 存储在中的两个对象之一 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) [`Switch.IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) 。 然后，当数据绑定集合为时 `null` ，将 `ContentView` 显示设置为属性的对象 `EmptyView` ：

[![在运行时，在 iOS 和 Android 上显示空视图选项的屏幕截图](bindable-layouts-images/emptyview-runtime.png "可绑定布局空视图运行时选择")](bindable-layouts-images/emptyview-runtime-large.png#lightbox "可绑定布局空视图运行时选择")

## <a name="related-links"></a>相关链接

- [可绑定布局演示 (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)
- [创建自定义布局](~/xamarin-forms/user-interface/layouts/custom.md)
- [Xamarin.Forms 数据模板](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [创建 Xamarin.Forms 并重](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)