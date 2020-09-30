---
title: Xamarin.Forms CollectionView 布局
description: 默认情况下，CollectionView 会在垂直列表中显示其项。 但是，可以指定垂直和水平列表以及网格。
ms.prod: xamarin
ms.assetid: 5FE78207-1BD6-4706-91EF-B13932321FC9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/20/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e399ae590dbd1260f3b2a403c39ef7e7d98d3592
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91557356"
---
# <a name="no-locxamarinforms-collectionview-layout"></a>Xamarin.Forms CollectionView 布局

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 定义用于控制布局的下列属性：

- [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout)类型为的 [`IItemsLayout`](xref:Xamarin.Forms.IItemsLayout) 指定要使用的布局。
- [`ItemSizingStrategy`](xref:Xamarin.Forms.StructuredItemsView.ItemSizingStrategy)类型为的 [`ItemSizingStrategy`](xref:Xamarin.Forms.ItemSizingStrategy) 指定要使用的项度量策略。

这些属性是由对象支持的 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，这意味着属性可以是数据绑定的目标。

默认情况下， [`CollectionView`](xref:Xamarin.Forms.CollectionView) 会在垂直列表中显示其项。 但是，可以使用以下任何布局：

- 垂直列表–在添加新项时垂直增长的单个列列表。
- 水平列表–在添加新项时水平增长的单个行列表。
- 垂直网格–在添加新项时垂直增长的多列网格。
- 水平网格–在添加新项时水平增长的多行网格。

可以通过将 [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) 属性设置为从类派生的类来指定这些布局 [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) 。 此类定义了以下属性：

- [`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation)类型为的 [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) 指定 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 添加添加项的方向。
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment)类型为的 [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment) 指定对齐点如何与项对齐。
- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType)类型为的 [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType) 指定滚动时对齐点的行为。

这些属性是由对象支持的 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，这意味着属性可以是数据绑定的目标。 有关对齐点的详细信息，请参阅[ Xamarin.Forms CollectionView 滚动](scrolling.md)指南中的 "[对齐点](scrolling.md#snap-points)"。

[`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation)枚举定义以下成员：

- `Vertical` 指示 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 添加项时将垂直扩展。
- `Horizontal` 指示 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 添加项时将水平扩展。

`LinearItemsLayout`类从 [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) 类继承，并定义一个类型为的属性， `ItemSpacing` `double` 该属性表示每个项周围的空白区域。 此属性的默认值为0，其值必须始终大于或等于0。 `LinearItemsLayout`类还定义静态 `Vertical` 和 `Horizontal` 成员。 这些成员可以分别用来创建垂直或水平列表。 或者， `LinearItemsLayout` 可以创建一个对象， [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) 并将枚举成员指定为参数。

[`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout)类从 [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) 类继承，并定义以下属性：

- `VerticalItemSpacing`，类型为 `double` ，表示每个项周围的垂直空白间距。 此属性的默认值为0，其值必须始终大于或等于0。
- `HorizontalItemSpacing`，类型为 `double` ，表示每个项周围的水平空白空间。 此属性的默认值为0，其值必须始终大于或等于0。
- `Span`，类型为 `int` ，表示要在网格中显示的列数或行数。 此属性的默认值为1，并且它的值必须始终大于或等于1。

这些属性是由对象支持的 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，这意味着属性可以是数据绑定的目标。

> [!NOTE]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView) 使用本机布局引擎执行布局。

## <a name="vertical-list"></a>垂直列表

默认情况下， [`CollectionView`](xref:Xamarin.Forms.CollectionView) 将以垂直列表布局显示其项。 因此，不需要将 [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) 属性设置为使用此布局：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="Auto" />
                    <ColumnDefinition Width="Auto" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

但是，在 XAML 中， [`CollectionView`](xref:Xamarin.Forms.CollectionView) 可以通过将其属性设置为，将其设置为在垂直列表中显示其项 [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) `VerticalList` ：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="VerticalList">
    ...
</CollectionView>
```

此外，也可以通过将 [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) 属性设置为对象来完成此 `LinearItemsLayout` 操作，并将 `Vertical` [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) 枚举成员指定为 `Orientation` 属性值：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <LinearItemsLayout Orientation="Vertical" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = LinearItemsLayout.Vertical
};
```

这会生成单个列列表，该列表将在添加新项时垂直增长：

[![IOS 和 Android 上的 CollectionView 垂直列表布局屏幕截图](layout-images/vertical-list.png "CollectionView 垂直列表布局")](layout-images/vertical-list-large.png#lightbox "CollectionView 垂直列表布局")

## <a name="horizontal-list"></a>水平列表

在 XAML 中， [`CollectionView`](xref:Xamarin.Forms.CollectionView) 可以通过将其 [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) 属性设置为 `HorizontalList` ：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="HorizontalList">
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="35" />
                    <RowDefinition Height="35" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="70" />
                    <ColumnDefinition Width="140" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold"
                       LineBreakMode="TailTruncation" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       LineBreakMode="TailTruncation"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

此外，也可以通过将属性设置为对象来实现此布局，并将 [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) `LinearItemsLayout` `Horizontal` [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) 枚举成员指定为 `Orientation` 属性值：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <LinearItemsLayout Orientation="Horizontal" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = LinearItemsLayout.Horizontal
};
```

这会生成单个行列表，该列表在添加新项时水平增长：

[![IOS 和 Android 上的 CollectionView 水平列表布局屏幕截图](layout-images/horizontal-list.png "CollectionView 水平列表布局")](layout-images/horizontal-list-large.png#lightbox "CollectionView 水平列表布局")

## <a name="vertical-grid"></a>垂直网格

在 XAML 中， [`CollectionView`](xref:Xamarin.Forms.CollectionView) 可以通过将其属性设置为来在垂直网格中显示其项 [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) `VerticalGrid` ：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="VerticalGrid, 2">
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="35" />
                    <RowDefinition Height="35" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="70" />
                    <ColumnDefinition Width="80" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold"
                       LineBreakMode="TailTruncation" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       LineBreakMode="TailTruncation"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

此外，也可以通过将属性设置为对象的 [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) [`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation) 属性设置为来完成此布局 `Vertical` ：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
       <GridItemsLayout Orientation="Vertical"
                        Span="2" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new GridItemsLayout(2, ItemsLayoutOrientation.Vertical)
};
```

默认情况下，垂直 [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) 将显示单个列中的项。 但是，此示例将 `GridItemsLayout.Span` 属性设置为2。 这会生成一个两列网格，这会在添加新项时垂直增长：

[![IOS 和 Android 上的 CollectionView 垂直网格布局的屏幕截图](layout-images/vertical-grid.png "CollectionView 垂直网格布局")](layout-images/vertical-grid-large.png#lightbox "CollectionView 垂直网格布局")

## <a name="horizontal-grid"></a>水平网格

在 XAML 中， [`CollectionView`](xref:Xamarin.Forms.CollectionView) 可以通过将其属性设置为，在水平网格中显示其项 [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) `HorizontalGrid` ：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="HorizontalGrid, 4">
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="35" />
                    <RowDefinition Height="35" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="70" />
                    <ColumnDefinition Width="140" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold"
                       LineBreakMode="TailTruncation" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       LineBreakMode="TailTruncation"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

此外，也可以通过将属性设置为对象的 [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) [`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation) 属性设置为来完成此布局 `Horizontal` ：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
       <GridItemsLayout Orientation="Horizontal"
                        Span="4" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new GridItemsLayout(4, ItemsLayoutOrientation.Horizontal)
};
```

默认情况下，水平 [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) 将在单个行中显示项。 但是，此示例将 `GridItemsLayout.Span` 属性设置为4。 这会导致四行网格，这会在添加新项时水平增长：

[![IOS 和 Android 上的 CollectionView 水平网格布局屏幕截图](layout-images/horizontal-grid.png "CollectionView 水平网格布局")](layout-images/horizontal-grid-large.png#lightbox "CollectionView 水平网格布局")

## <a name="headers-and-footers"></a>页眉和页脚

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 可以显示通过列表中的项滚动的页眉和页脚。 页眉和页脚可以是字符串、视图或 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 对象。

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 为指定页眉和页脚定义以下属性：

- `Header`类型为的 `object` 指定要在列表开头显示的字符串、绑定或视图。
- `HeaderTemplate`类型为的 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 指定用于 `DataTemplate` 格式化的 `Header` 。
- `Footer`，类型为 `object` ，指定将显示在列表末尾的字符串、绑定或视图。
- `FooterTemplate`类型为的 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 指定用于 `DataTemplate` 格式化的 `Footer` 。

这些属性是由对象支持的 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，这意味着属性可以是数据绑定的目标。

将标题添加到水平增长的布局时，将在列表的左侧显示页眉。 同样，在将页脚添加到水平增长的布局时，将在列表右侧显示页脚。

### <a name="display-strings-in-the-header-and-footer"></a>在页眉和页脚中显示字符串

`Header`和 `Footer` 属性可以设置为 `string` 值，如下面的示例中所示：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                Header="Monkeys"
                Footer="2019">
    ...
</CollectionView>
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    Header = "Monkeys",
    Footer = "2019"
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

此代码将产生以下屏幕截图，其中的标头显示在 iOS 屏幕截图中，在 Android 屏幕截图中显示了页脚：

[![IOS 和 Android 上的 CollectionView 字符串标头和脚注的屏幕截图](layout-images/header-footer-string.png "CollectionView 字符串标头和表尾")](layout-images/header-footer-string-large.png#lightbox "CollectionView 字符串标头和表尾")

### <a name="display-views-in-the-header-and-footer"></a>在页眉和页脚中显示视图

`Header`和 `Footer` 属性可以设置为视图。 这可以是单个视图，也可以是包含多个子视图的视图。 下面的示例显示了 `Header` `Footer` 每个设置到 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 包含对象的对象的和属性 [`Label`](xref:Xamarin.Forms.Label) ：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.Header>
        <StackLayout BackgroundColor="LightGray">
            <Label Margin="10,0,0,0"
                   Text="Monkeys"
                   FontSize="Small"
                   FontAttributes="Bold" />
        </StackLayout>
    </CollectionView.Header>
    <CollectionView.Footer>
        <StackLayout BackgroundColor="LightGray">
            <Label Margin="10,0,0,0"
                   Text="Friends of Xamarin Monkey"
                   FontSize="Small"
                   FontAttributes="Bold" />
        </StackLayout>
    </CollectionView.Footer>
    ...
</CollectionView>
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    Header = new StackLayout
    {
        Children =
        {
            new Label { Text = "Monkeys", ... }
        }
    },
    Footer = new StackLayout
    {
        Children =
        {
            new Label { Text = "Friends of Xamarin Monkey", ... }
        }
    }
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

此代码将产生以下屏幕截图，其中的标头显示在 iOS 屏幕截图中，在 Android 屏幕截图中显示了页脚：

[![在 iOS 和 Android 上使用视图的 CollectionView 标头和脚注的屏幕截图](layout-images/header-footer-view.png "CollectionView 视图页眉和页脚")](layout-images/header-footer-view-large.png#lightbox "CollectionView 视图页眉和页脚")

### <a name="display-a-templated-header-and-footer"></a>显示模板化页眉和页脚

`HeaderTemplate`和 `FooterTemplate` 属性可以设置为用于设置 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 页眉和页脚格式的对象。 在此方案中， `Header` 和 `Footer` 属性必须绑定到要应用的模板的当前源，如以下示例中所示：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                Header="{Binding .}"
                Footer="{Binding .}">
    <CollectionView.HeaderTemplate>
        <DataTemplate>
            <StackLayout BackgroundColor="LightGray">
                <Label Margin="10,0,0,0"
                       Text="Monkeys"
                       FontSize="Small"
                       FontAttributes="Bold" />
            </StackLayout>
        </DataTemplate>
    </CollectionView.HeaderTemplate>
    <CollectionView.FooterTemplate>
        <DataTemplate>
            <StackLayout BackgroundColor="LightGray">
                <Label Margin="10,0,0,0"
                       Text="Friends of Xamarin Monkey"
                       FontSize="Small"
                       FontAttributes="Bold" />
            </StackLayout>
        </DataTemplate>
    </CollectionView.FooterTemplate>
    ...
</CollectionView>
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    HeaderTemplate = new DataTemplate(() =>
    {
        return new StackLayout { };
    }),
    FooterTemplate = new DataTemplate(() =>
    {
        return new StackLayout { };
    })
};
collectionView.SetBinding(ItemsView.HeaderProperty, ".");
collectionView.SetBinding(ItemsView.FooterProperty, ".");
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

此代码将产生以下屏幕截图，其中的标头显示在 iOS 屏幕截图中，在 Android 屏幕截图中显示了页脚：

[![在 iOS 和 Android 上使用模板的 CollectionView 标头和脚注的屏幕截图](layout-images/header-footer-template.png "CollectionView 模板页眉和页脚")](layout-images/header-footer-template-large.png#lightbox "CollectionView 模板页眉和页脚")

## <a name="item-spacing"></a>项间距

默认情况下，中的每个项之间没有空格 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 。 可以通过在使用的项布局上设置属性来更改此行为 `CollectionView` 。

如果将 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 其 [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) 属性设置为 `LinearItemsLayout` 对象，则可以将 `LinearItemsLayout.ItemSpacing` 属性设置为 `double` 表示项之间的间距的值：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <LinearItemsLayout Orientation="Vertical"
                           ItemSpacing="20" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

> [!NOTE]
> `LinearItemsLayout.ItemSpacing`属性具有一个验证回叫集，它可确保属性的值始终大于或等于0。

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        ItemSpacing = 20
    }
};
```

此代码将生成垂直的单列列表，其中项之间的间距为20：

[![在 iOS 和 Android 上具有项间距的 CollectionView 的屏幕截图](layout-images/vertical-list-spacing.png "CollectionView 项间距")](layout-images/vertical-list-spacing-large.png#lightbox "CollectionView 项间距")

如果将 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 其 [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) 属性设置为 [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) 对象，则 `GridItemsLayout.VerticalItemSpacing` 和 `GridItemsLayout.HorizontalItemSpacing` 属性可以设置为在 `double` 各项之间垂直和水平表示空白空间的值。

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
       <GridItemsLayout Orientation="Vertical"
                        Span="2"
                        VerticalItemSpacing="20"
                        HorizontalItemSpacing="30" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

> [!NOTE]
> `GridItemsLayout.VerticalItemSpacing`和 `GridItemsLayout.HorizontalItemSpacing` 属性设置了验证回调，这可确保属性的值始终大于或等于0。

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new GridItemsLayout(2, ItemsLayoutOrientation.Vertical)
    {
        VerticalItemSpacing = 20,
        HorizontalItemSpacing = 30
    }
};
```

此代码将生成垂直的两列网格，其中项之间的垂直间距为20，两项之间的水平间距为30：

[![Android 上具有项间距的 CollectionView 的屏幕截图](layout-images/vertical-grid-spacing.png "CollectionView 项间距")](layout-images/vertical-grid-spacing-large.png#lightbox "CollectionView 项间距")

## <a name="item-sizing"></a>项大小调整

默认情况下，中的每一项 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 都是单独测量和调整大小的，前提是中的 UI 元素 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 不指定固定的大小。 此行为可以更改，由 [`CollectionView.ItemSizingStrategy`](xref:Xamarin.Forms.StructuredItemsView.ItemSizingStrategy) 属性值指定。 此属性值可以设置为 [`ItemSizingStrategy`](xref:Xamarin.Forms.ItemSizingStrategy) 枚举成员之一：

- `MeasureAllItems` –每个项都单独进行度量。 这是默认值。
- `MeasureFirstItem` –仅度量第一项，并为所有后续项提供与第一个项相同的大小。

> [!IMPORTANT]
> `MeasureFirstItem`当在项大小旨在跨所有项的情况下使用时，大小调整策略会提高性能。

下面的代码示例演示如何设置 [`ItemSizingStrategy`](xref:Xamarin.Forms.StructuredItemsView.ItemSizingStrategy) 属性：

```xaml
<CollectionView ...
                ItemSizingStrategy="MeasureFirstItem">
    ...
</CollectionView>
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemSizingStrategy = ItemSizingStrategy.MeasureFirstItem
};
```

## <a name="dynamic-resizing-of-items"></a>动态调整项的大小

中的项 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 可以在运行时通过更改中的元素的与布局相关的属性，在运行时动态调整大小 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 。 例如，下面的代码示例更改对象的 [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) 和 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 属性 [`Image`](xref:Xamarin.Forms.Image) ：

```csharp
void OnImageTapped(object sender, EventArgs e)
{
    Image image = sender as Image;
    image.HeightRequest = image.WidthRequest = image.HeightRequest.Equals(60) ? 100 : 60;
}
```

此 `OnImageTapped` 事件处理程序是为响应 [`Image`](xref:Xamarin.Forms.Image) 正在点击的对象而执行的，它更改图像的尺寸以便于查看：

[![在 iOS 和 Android 上动态调整项大小的 CollectionView 屏幕截图](layout-images/runtime-resizing.png "CollectionView 动态项大小调整")](layout-images/runtime-resizing-large.png#lightbox "CollectionView 动态项大小调整")

## <a name="right-to-left-layout"></a>从右到左布局

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 可以通过将其属性设置为，从右到左的流方向布局其内容 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft) 。 但是，在 `FlowDirection` 理想情况下，应在页面或根布局上设置属性，这会使页面或根布局中的所有元素响应流方向：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="CollectionViewDemos.Views.VerticalListFlowDirectionPage"
             Title="Vertical list (RTL FlowDirection)"
             FlowDirection="RightToLeft">
    <StackLayout Margin="20">
        <CollectionView ItemsSource="{Binding Monkeys}">
            ...
        </CollectionView>
    </StackLayout>
</ContentPage>
```

具有父级的元素的默认值 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 为 [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent) 。 因此， [`CollectionView`](xref:Xamarin.Forms.CollectionView) `FlowDirection` 从继承属性值 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ，后者又 `FlowDirection` 从继承属性值 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 。 这会导致从右到左的布局，如以下屏幕截图所示：

[![IOS 和 Android 上的 CollectionView 从右到左垂直列表布局的屏幕截图](layout-images/vertical-list-rtl.png "CollectionView 从右到左垂直列表布局")](layout-images/vertical-list-rtl-large.png#lightbox "CollectionView 从右到左垂直列表布局")

有关流方向的详细信息，请参阅 [从右到左的本地化](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)。

## <a name="related-links"></a>相关链接

- [CollectionView (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [从右到左的本地化](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)
- [Xamarin.Forms CollectionView 滚动](scrolling.md)