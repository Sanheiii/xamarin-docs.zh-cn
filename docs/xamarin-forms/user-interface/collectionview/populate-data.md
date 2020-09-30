---
title: Xamarin.Forms CollectionView 数据
description: 使用数据填充 CollectionView，方法是将其 System.windows.controls.itemscontrol.itemssource 属性设置为任何实现 IEnumerable 的集合。
ms.prod: xamarin
ms.assetid: E1783E34-1C0F-401A-80D5-B2BE5508F5F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/29/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 089fb69dfc12b23bb594d5f88a50b37f9694c778
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563375"
---
# <a name="no-locxamarinforms-collectionview-data"></a>Xamarin.Forms CollectionView 数据

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 包括以下属性，这些属性定义要显示的数据及其外观：

- [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource)类型为的 `IEnumerable` 指定要显示的项的集合，其默认值为 `null` 。
- [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate)类型为的 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 指定要应用于要显示的项集合中的每一项的模板。

这些属性是由对象支持的 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，这意味着属性可以是数据绑定的目标。

> [!NOTE]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView) 定义一个 `ItemsUpdatingScrollMode` 属性，该属性表示在 `CollectionView` 添加新项时的滚动行为。 有关此属性的详细信息，请参阅 [在添加新项时控制滚动位置](scrolling.md#control-scroll-position-when-new-items-are-added)。

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 支持在用户滚动时增加数据虚拟化。 有关详细信息，请参阅 [以增量方式加载数据](#load-data-incrementally)。

## <a name="populate-a-collectionview-with-data"></a>使用数据填充 CollectionView

[`CollectionView`](xref:Xamarin.Forms.CollectionView)使用数据填充数据，方法是将其 [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) 属性设置为任何实现的集合 `IEnumerable` 。 可以通过从字符串数组中初始化属性，在 XAML 中添加项 `ItemsSource` ：

```xaml
<CollectionView>
  <CollectionView.ItemsSource>
    <x:Array Type="{x:Type x:String}">
      <x:String>Baboon</x:String>
      <x:String>Capuchin Monkey</x:String>
      <x:String>Blue Monkey</x:String>
      <x:String>Squirrel Monkey</x:String>
      <x:String>Golden Lion Tamarin</x:String>
      <x:String>Howler Monkey</x:String>
      <x:String>Japanese Macaque</x:String>
    </x:Array>
  </CollectionView.ItemsSource>
</CollectionView>
```

> [!NOTE]
> 请注意，`x:Array` 元素需要用于指示数组中项目类型的 `Type` 属性。

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView();
collectionView.ItemsSource = new string[]
{
    "Baboon",
    "Capuchin Monkey",
    "Blue Monkey",
    "Squirrel Monkey",
    "Golden Lion Tamarin",
    "Howler Monkey",
    "Japanese Macaque"
};
```

> [!WARNING]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView) 如果其 [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) 从 UI 线程中更新，则会引发异常。

默认情况下， [`CollectionView`](xref:Xamarin.Forms.CollectionView) 显示垂直列表中的项，如以下屏幕截图所示：

[![在 iOS 和 Android 上包含文本项的 CollectionView 的屏幕截图](populate-data-images/text.png "CollectionView 中的文本项")](populate-data-images/text-large.png#lightbox "CollectionView 中的文本项")

> [!IMPORTANT]
> 如果 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 需要在基础集合中添加、删除或更改项时进行刷新，则基础集合应为 `IEnumerable` 发送属性更改通知的集合，如 `ObservableCollection` 。

有关如何更改布局的信息 [`CollectionView`](xref:Xamarin.Forms.CollectionView) ，请参阅[ Xamarin.Forms CollectionView 布局](layout.md)。 有关如何定义中每个项的外观的信息 `CollectionView` ，请参阅 [定义项外观](#define-item-appearance)。

### <a name="data-binding"></a>数据绑定

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 使用数据绑定将其属性绑定到集合，可以填充数据 [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) `IEnumerable` 。 在 XAML 中，这是通过 `Binding` 标记扩展实现的：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}" />
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView();
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

在此示例中， [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) 属性数据绑定到 `Monkeys` 连接的 viewmodel 的属性。

> [!NOTE]
> 可以启用编译的绑定以提高应用程序中的数据绑定性能 Xamarin.Forms 。 有关详细信息，请参阅[已编译的绑定](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md)。

若要深入了解数据绑定，请参阅 [Xamarin.Forms 数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md)。

## <a name="define-item-appearance"></a>定义项外观

[`CollectionView`](xref:Xamarin.Forms.CollectionView)可以通过将属性设置为来定义中每个项的外观 [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ：

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
    ...
</CollectionView>
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView();
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");

collectionView.ItemTemplate = new DataTemplate(() =>
{
    Grid grid = new Grid { Padding = 10 };
    grid.RowDefinitions.Add(new RowDefinition { Height = GridLength.Auto });
    grid.RowDefinitions.Add(new RowDefinition { Height = GridLength.Auto });
    grid.ColumnDefinitions.Add(new ColumnDefinition { Width = GridLength.Auto });
    grid.ColumnDefinitions.Add(new ColumnDefinition { Width = GridLength.Auto });

    Image image = new Image { Aspect = Aspect.AspectFill, HeightRequest = 60, WidthRequest = 60 };
    image.SetBinding(Image.SourceProperty, "ImageUrl");

    Label nameLabel = new Label { FontAttributes = FontAttributes.Bold };
    nameLabel.SetBinding(Label.TextProperty, "Name");

    Label locationLabel = new Label { FontAttributes = FontAttributes.Italic, VerticalOptions = LayoutOptions.End };
    locationLabel.SetBinding(Label.TextProperty, "Location");

    Grid.SetRowSpan(image, 2);

    grid.Children.Add(image);
    grid.Children.Add(nameLabel, 1, 0);
    grid.Children.Add(locationLabel, 1, 1);

    return grid;
});
```

在中指定的元素 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 定义列表中每一项的外观。 在此示例中，中的布局由 `DataTemplate` 管理 [`Grid`](xref:Xamarin.Forms.Grid) 。 `Grid`包含一个 [`Image`](xref:Xamarin.Forms.Image) 对象和两个 [`Label`](xref:Xamarin.Forms.Label) 对象，这些对象都绑定到类的属性 `Monkey` ：

```csharp
public class Monkey
{
    public string Name { get; set; }
    public string Location { get; set; }
    public string Details { get; set; }
    public string ImageUrl { get; set; }
}
```

以下屏幕截图显示列表中每一项的模板化结果：

[![IOS 和 Android 上每个项的模板 CollectionView 的屏幕截图](populate-data-images/datatemplate.png "CollectionView 中的模板化项")](populate-data-images/datatemplate-large.png#lightbox "CollectionView 中的模板化项")

有关数据模板的详细信息，请参阅 [Xamarin.Forms 数据模板](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。

## <a name="choose-item-appearance-at-runtime"></a>在运行时选择项外观

[`CollectionView`](xref:Xamarin.Forms.CollectionView)通过将属性设置为对象，可在运行时根据项值选择每个项的外观 [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) ：

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:CollectionViewDemos.Controls">
    <ContentPage.Resources>
        <DataTemplate x:Key="AmericanMonkeyTemplate">
            ...
        </DataTemplate>

        <DataTemplate x:Key="OtherMonkeyTemplate">
            ...
        </DataTemplate>

        <controls:MonkeyDataTemplateSelector x:Key="MonkeySelector"
                                             AmericanMonkey="{StaticResource AmericanMonkeyTemplate}"
                                             OtherMonkey="{StaticResource OtherMonkeyTemplate}" />
    </ContentPage.Resources>

    <CollectionView ItemsSource="{Binding Monkeys}"
                    ItemTemplate="{StaticResource MonkeySelector}" />
</ContentPage>
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    ItemTemplate = new MonkeyDataTemplateSelector { ... }
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

[`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate)属性设置为 `MonkeyDataTemplateSelector` 对象。 下面的示例演示了 `MonkeyDataTemplateSelector` 类：

```csharp
public class MonkeyDataTemplateSelector : DataTemplateSelector
{
    public DataTemplate AmericanMonkey { get; set; }
    public DataTemplate OtherMonkey { get; set; }

    protected override DataTemplate OnSelectTemplate(object item, BindableObject container)
    {
        return ((Monkey)item).Location.Contains("America") ? AmericanMonkey : OtherMonkey;
    }
}
```

`MonkeyDataTemplateSelector`类定义了 `AmericanMonkey` 并 `OtherMonkey` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 将属性设置为不同的数据模板。 `OnSelectTemplate`重写将返回 `AmericanMonkey` 模板，该模板将在猴子名称包含 "北美洲" 时显示猴子名称和位置。 当猴子名称中不包含 "美洲" 时，该 `OnSelectTemplate` 重写将返回 `OtherMonkey` 模板，该模板将在银中显示猴子名称和位置：

[![IOS 和 Android 上的 CollectionView 运行时项模板选择屏幕截图](populate-data-images/datatemplateselector.png "CollectionView 中的运行时项模板选择")](populate-data-images/datatemplateselector-large.png#lightbox "CollectionView 中的运行时项模板选择")

有关数据模板选择器的详细信息，请参阅 [创建 Xamarin.Forms 并重](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)。

> [!IMPORTANT]
> 使用时 [`CollectionView`](xref:Xamarin.Forms.CollectionView) ，不要将对象的根元素设置 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 为 `ViewCell` 。 这将导致引发异常，因为 `CollectionView` 没有单元的概念。

## <a name="context-menus"></a>上下文菜单

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 支持通过的项的上下文菜单，该菜单 `SwipeView` 显示带有滑动手势的上下文菜单。 `SwipeView`是一个容器控件，该控件环绕内容项，并为该项内容提供上下文菜单项。 因此， `CollectionView` 通过创建一个来实现上下文菜单， `SwipeView` 该对象用于定义环绕的内容 `SwipeView` ，以及由滑动手势显示的上下文菜单项。 这是通过将设置 `SwipeView` 为中的根视图来实现的，该视图 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 定义中每个数据项的外观 `CollectionView` ：

```xaml
<CollectionView x:Name="collectionView"
                ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <SwipeView>
                <SwipeView.LeftItems>
                    <SwipeItems>
                        <SwipeItem Text="Favorite"
                                   IconImageSource="favorite.png"
                                   BackgroundColor="LightGreen"
                                   Command="{Binding Source={x:Reference collectionView}, Path=BindingContext.FavoriteCommand}"
                                   CommandParameter="{Binding}" />
                        <SwipeItem Text="Delete"
                                   IconImageSource="delete.png"
                                   BackgroundColor="LightPink"
                                   Command="{Binding Source={x:Reference collectionView}, Path=BindingContext.DeleteCommand}"
                                   CommandParameter="{Binding}" />
                    </SwipeItems>
                </SwipeView.LeftItems>
                <Grid BackgroundColor="White"
                      Padding="10">
                    <!-- Define item appearance -->
                </Grid>
            </SwipeView>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView();
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");

collectionView.ItemTemplate = new DataTemplate(() =>
{
    // Define item appearance
    Grid grid = new Grid { Padding = 10, BackgroundColor = Color.White };
    // ...

    SwipeView swipeView = new SwipeView();
    SwipeItem favoriteSwipeItem = new SwipeItem
    {
        Text = "Favorite",
        IconImageSource = "favorite.png",
        BackgroundColor = Color.LightGreen
    };
    favoriteSwipeItem.SetBinding(MenuItem.CommandProperty, new Binding("BindingContext.FavoriteCommand", source: collectionView));
    favoriteSwipeItem.SetBinding(MenuItem.CommandParameterProperty, ".");

    SwipeItem deleteSwipeItem = new SwipeItem
    {
        Text = "Delete",
        IconImageSource = "delete.png",
        BackgroundColor = Color.LightPink
    };
    deleteSwipeItem.SetBinding(MenuItem.CommandProperty, new Binding("BindingContext.DeleteCommand", source: collectionView));
    deleteSwipeItem.SetBinding(MenuItem.CommandParameterProperty, ".");

    swipeView.LeftItems = new SwipeItems { favoriteSwipeItem, deleteSwipeItem };
    swipeView.Content = grid;    
    return swipeView;
});
```

在此示例中， `SwipeView` 内容为 [`Grid`](xref:Xamarin.Forms.Grid) ，用于定义中每个项的外观 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 。 滑动项用于对内容执行操作 `SwipeView` ，并在控件从左侧重击时显示：

[![IOS 和 Android 上的 CollectionView 上下文菜单项的屏幕截图](populate-data-images/swipeview.png "带有 SwipeView 上下文菜单项的 CollectionView")](populate-data-images/swipeview-large.png#lightbox "带有 SwipeView 上下文菜单项的 CollectionView")

`SwipeView` 支持四种不同的轻扫方向，并通过将 `SwipeItems` 对象添加到的方向集合来定义滑动方向 `SwipeItems` 。 默认情况下，当用户点击一项时，将执行一项刷卡器项。 此外，在执行了一项轻扫项目后，将会隐藏该滑动项，并 `SwipeView` 重新显示内容。 不过，这些行为可以更改。

有关控件的详细信息 `SwipeView` ，请参阅[ Xamarin.Forms SwipeView](~/xamarin-forms/user-interface/swipeview.md)。

## <a name="pull-to-refresh"></a>下拉刷新

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 支持通过拉取到刷新功能 `RefreshView` ，这可通过下拉项列表来刷新要显示的数据。 `RefreshView`是一个容器控件，它提供向其子级提供刷新功能的拉取，前提是子级支持可滚动的内容。 因此，通过将拉取设置为的子级来实现对的请求刷新 `CollectionView` `RefreshView` ：

```xaml
<RefreshView IsRefreshing="{Binding IsRefreshing}"
             Command="{Binding RefreshCommand}">
    <CollectionView ItemsSource="{Binding Animals}">
        ...
    </CollectionView>
</RefreshView>
```

等效 C# 代码如下：

```csharp
RefreshView refreshView = new RefreshView();
ICommand refreshCommand = new Command(() =>
{
    // IsRefreshing is true
    // Refresh data here
    refreshView.IsRefreshing = false;
});
refreshView.Command = refreshCommand;

CollectionView collectionView = new CollectionView();
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Animals");
refreshView.Content = collectionView;
// ...
```

当用户启动刷新时，将 `ICommand` 执行由属性定义的 `Command` ，这会刷新正在显示的项。 刷新发生时，会显示刷新可视化效果，其中包含动画进度圆：

[![IOS 和 Android 上的 CollectionView 请求刷新的屏幕截图](populate-data-images/pull-to-refresh.png "CollectionView 请求刷新")](populate-data-images/pull-to-refresh-large.png#lightbox "CollectionView 请求刷新")

属性的值 `RefreshView.IsRefreshing` 指示的当前状态 `RefreshView` 。 当用户触发刷新时，此属性将自动转换为 `true` 。 刷新完成后，应将属性重置为 `false` 。

有关的详细信息 `RefreshView` ，请参阅[ Xamarin.Forms RefreshView](~/xamarin-forms/user-interface/refreshview.md)。

## <a name="load-data-incrementally"></a>以增量方式加载数据

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 支持在用户滚动时增加数据虚拟化。 这可以实现各种方案，例如，在用户滚动时，从 web 服务异步加载数据页。 此外，还可以配置加载更多数据的点，以便用户不会看到空白空间，也不会停止滚动。

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 定义以下属性以控制数据的增量加载：

- `RemainingItemsThreshold`，类型为 `int` ，在事件激发时，列表中尚未显示的项的阈值 `RemainingItemsThresholdReached` 。
- `RemainingItemsThresholdReachedCommand`，类型为 `ICommand` ，在达到时执行 `RemainingItemsThreshold` 。
- `RemainingItemsThresholdReachedCommandParameter`，属于 `object` 类型，是传递给 `RemainingItemsThresholdReachedCommand` 的参数。

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 还定义了一个 `RemainingItemsThresholdReached` 事件，该事件在 `CollectionView` 滚动到足够多的 `RemainingItemsThreshold` 项尚未显示时激发。 可以处理此事件以加载更多项。 此外，当 `RemainingItemsThresholdReached` 引发事件时， `RemainingItemsThresholdReachedCommand` 将执行，以便在 viewmodel 中进行增量数据加载。

此属性的默认值 `RemainingItemsThreshold` 为-1，表示 `RemainingItemsThresholdReached` 将永远不会触发该事件。 当属性值为0时， `RemainingItemsThresholdReached` 将在显示中的最后一项时触发事件 [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) 。 对于大于0的值， `RemainingItemsThresholdReached` 当 `ItemsSource` 包含该数目的项尚未滚动到时，将触发事件。

> [!NOTE]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView) 验证 `RemainingItemsThreshold` 属性，使其值始终大于或等于-1。

下面的 XAML 示例显示了一个 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 以增量方式加载数据的：

```xaml
<CollectionView ItemsSource="{Binding Animals}"
                RemainingItemsThreshold="5"
                RemainingItemsThresholdReached="OnCollectionViewRemainingItemsThresholdReached">
    ...
</CollectionView>
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    RemainingItemsThreshold = 5
};
collectionView.RemainingItemsThresholdReached += OnCollectionViewRemainingItemsThresholdReached;
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Animals");
```

在此代码示例中， `RemainingItemsThresholdReached` 当有5个项尚未滚动到并且在响应中执行事件处理程序时，将触发事件 `OnCollectionViewRemainingItemsThresholdReached` ：

```csharp
void OnCollectionViewRemainingItemsThresholdReached(object sender, EventArgs e)
{
    // Retrieve more data here and add it to the CollectionView's ItemsSource collection.
}
```

> [!NOTE]
> 还可以通过将绑定 `RemainingItemsThresholdReachedCommand` 到 viewmodel 中的实现来增量方式加载数据 `ICommand` 。

## <a name="related-links"></a>相关链接

- [CollectionView (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Xamarin.Forms RefreshView](~/xamarin-forms/user-interface/refreshview.md)
- [Xamarin.Forms SwipeView](~/xamarin-forms/user-interface/swipeview.md)
- [Xamarin.Forms数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Xamarin.Forms 数据模板](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [创建 Xamarin.Forms 并重](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)