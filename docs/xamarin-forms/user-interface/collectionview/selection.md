---
title: Xamarin.Forms CollectionView 选择
description: 默认情况下，CollectionView 选择处于禁用状态。 但是，可以启用单个和多个选择。
ms.prod: xamarin
ms.assetid: 423D91C7-1E58-4735-9E80-58F11CDFD953
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c432c1981ba057f61bba780b7997c8b78f8cef3f
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91557343"
---
# <a name="no-locxamarinforms-collectionview-selection"></a>Xamarin.Forms CollectionView 选择

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 定义控制项选择的下列属性：

- [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode)类型为 [`SelectionMode`](xref:Xamarin.Forms.SelectionMode) 的选择模式。
- [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem)，类型为 `object` ，列表中的选定项。 此属性的默认绑定模式为 `TwoWay` ， `null` 在未选择任何项时具有值。
- [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems)，类型为 `IList<object>` ，列表中选定的项。 此属性的默认绑定模式为 `OneWay` ， `null` 在未选择任何项时具有值。
- [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand)，类型 `ICommand` 为，在选定项发生更改时执行。
- [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter)，类型为 `object` ，它是传递到的参数 `SelectionChangedCommand` 。

所有这些属性都由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象提供支持，这意味着这些属性可以作为数据绑定的目标。

默认情况下， [`CollectionView`](xref:Xamarin.Forms.CollectionView) 选定内容处于禁用状态。 但是，可以通过将 [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) 属性值设置为枚举成员之一来更改此行为 [`SelectionMode`](xref:Xamarin.Forms.SelectionMode) ：

- `None` –表示无法选择项。 这是默认值。
- `Single` –指示可以选择单个项，突出显示选定项。
- `Multiple` –指示可以选择多个项，突出显示选定项。

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 定义一个 [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) 事件，该事件在 [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) 属性发生更改时触发，无论用户是从列表中选择项，还是应用程序设置属性。 此外，当属性发生更改时，也会触发此事件 [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) 。 [`SelectionChangedEventArgs`](xref:Xamarin.Forms.SelectionChangedEventArgs)事件附带的对象 `SelectionChanged` 有两个属性，两者均为类型 `IReadOnlyList<object>` ：

- `PreviousSelection` –选定内容更改之前选定的项的列表。
- `CurrentSelection` –选定内容更改后所选择的项的列表。

此外， [`CollectionView`](xref:Xamarin.Forms.CollectionView) 还提供了一个 `UpdateSelectedItems` 方法，该方法 [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) 用选定项的列表来更新属性，同时仅引发单个更改通知。

## <a name="single-selection"></a>单选

当 [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) 属性设置为时 `Single` ，可以选择中的单个项 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 。 选择项时， [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) 属性将设置为选定项的值。 此属性发生更改时，将执行 (，并将 [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand) 值 [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter) 传递到 `ICommand`) ，并 [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) 引发事件。

下面的 XAML 示例显示了一个 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 可响应单项选择的：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                SelectionMode="Single"
                SelectionChanged="OnCollectionViewSelectionChanged">
    ...
</CollectionView>
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Single
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SelectionChanged += OnCollectionViewSelectionChanged;
```

在此代码示例中，事件 `OnCollectionViewSelectionChanged` 处理程序在事件激发时执行 [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) ，事件处理程序检索之前选择的项和当前选定项：

```csharp
void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    string previous = (e.PreviousSelection.FirstOrDefault() as Monkey)?.Name;
    string current = (e.CurrentSelection.FirstOrDefault() as Monkey)?.Name;
    ...
}
```

> [!IMPORTANT]
> [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged)事件可由更改属性后发生的更改触发 [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) 。

以下屏幕截图显示了中的单项选择 [`CollectionView`](xref:Xamarin.Forms.CollectionView) ：

[![在 iOS 和 Android 上进行单项选择的 CollectionView 垂直列表屏幕截图](selection-images/single-selection.png "带有单项选择的 CollectionView 垂直列表")](selection-images/single-selection-large.png#lightbox "带有单项选择的 CollectionView 垂直列表")

## <a name="multiple-selection"></a>多重选择

当 [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) 属性设置为时 `Multiple` ，可以选择中的多个项 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 。 选择项时， [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) 属性将设置为所选的项。 此属性发生更改时，将执行 (，并将 [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand) 值 [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter) 传递到 `ICommand`) ，并 [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) 引发事件。

下面的 XAML 示例显示了一个 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 可响应多项选择的：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                SelectionMode="Multiple"
                SelectionChanged="OnCollectionViewSelectionChanged">
    ...
</CollectionView>
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Multiple
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SelectionChanged += OnCollectionViewSelectionChanged;
```

在此代码示例中，事件 `OnCollectionViewSelectionChanged` 处理程序在事件激发时执行 [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) ，事件处理程序检索之前选择的项和当前选定项：

```csharp
void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    var previous = e.PreviousSelection;
    var current = e.CurrentSelection;
    ...
}
```

> [!IMPORTANT]
> [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged)事件可由更改属性后发生的更改触发 [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) 。

以下屏幕截图显示了中的多项选择 [`CollectionView`](xref:Xamarin.Forms.CollectionView) ：

[![在 iOS 和 Android 上具有多个选定内容的 CollectionView 垂直列表的屏幕截图](selection-images/multiple-selection.png "具有多个选定内容的 CollectionView 垂直列表")](selection-images/multiple-selection-large.png#lightbox "具有多个选定内容的 CollectionView 垂直列表")

## <a name="single-pre-selection"></a>单项选择

当 [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) 属性设置为时 `Single` ， [`CollectionView`](xref:Xamarin.Forms.CollectionView) 可以通过将 [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) 属性设置为项来预先选择中的单个项。 下面的 XAML 示例显示了一个 `CollectionView` 预先选择单个项的：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                SelectionMode="Single"
                SelectedItem="{Binding SelectedMonkey}">
    ...
</CollectionView>
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Single
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SetBinding(SelectableItemsView.SelectedItemProperty, "SelectedMonkey");
```

> [!NOTE]
> [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem)属性的默认绑定模式为 `TwoWay` 。

[`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem)属性数据绑定到类型为的 `SelectedMonkey` 已连接视图模型的属性 `Monkey` 。 默认情况下， `TwoWay` 使用绑定，以便在用户更改所选的项时，属性的值 `SelectedMonkey` 将设置为选定的 `Monkey` 对象。 在 `SelectedMonkey` 类中定义属性 `MonkeysViewModel` ，并将其设置为集合的第四项 `Monkeys` ：

```csharp
public class MonkeysViewModel : INotifyPropertyChanged
{
    ...
    public ObservableCollection<Monkey> Monkeys { get; private set; }

    Monkey selectedMonkey;
    public Monkey SelectedMonkey
    {
        get
        {
            return selectedMonkey;
        }
        set
        {
            if (selectedMonkey != value)
            {
                selectedMonkey = value;
            }
        }
    }

    public MonkeysViewModel()
    {
        ...
        selectedMonkey = Monkeys.Skip(3).FirstOrDefault();
    }
    ...
}
```

因此，当 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 显示时，将预先选择列表中的第四项：

[![IOS 和 Android 上包含单项选择的 CollectionView 垂直列表屏幕截图](selection-images/single-pre-selection.png "带有单项选择的 CollectionView 垂直列表")](selection-images/single-pre-selection-large.png#lightbox "带有单项选择的 CollectionView 垂直列表")

## <a name="multiple-pre-selection"></a>多个预选择

当 [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) 属性设置为时 `Multiple` ，可以预先选择中的多个项 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 。 下面的 XAML 示例显示了一个 `CollectionView` ，它将启用多个项的预选择：

```xaml
<CollectionView x:Name="collectionView"
                ItemsSource="{Binding Monkeys}"
                SelectionMode="Multiple"
                SelectedItems="{Binding SelectedMonkeys}">
    ...
</CollectionView>
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Multiple
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SetBinding(SelectableItemsView.SelectedItemsProperty, "SelectedMonkeys");
```

> [!NOTE]
> [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems)属性的默认绑定模式为 `OneWay` 。

[`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems)属性数据绑定到类型为的 `SelectedMonkeys` 已连接视图模型的属性 `ObservableCollection<object>` 。 在 `SelectedMonkeys` 类中定义属性 `MonkeysViewModel` ，并将其设置为集合中的第二、第四和第五项 `Monkeys` ：

```csharp
namespace CollectionViewDemos.ViewModels
{
    public class MonkeysViewModel : INotifyPropertyChanged
    {
        ...
        ObservableCollection<object> selectedMonkeys;
        public ObservableCollection<object> SelectedMonkeys
        {
            get
            {
                return selectedMonkeys;
            }
            set
            {
                if (selectedMonkeys != value)
                {
                    selectedMonkeys = value;
                }
            }
        }

        public MonkeysViewModel()
        {
            ...
            SelectedMonkeys = new ObservableCollection<object>()
            {
                Monkeys[1], Monkeys[3], Monkeys[4]
            };
        }
        ...
    }
}
```

因此，当显示时，将 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 预先选择列表中的第二个、第四个和第五个项：

[![在 iOS 和 Android 上具有多个预选择的 CollectionView 垂直列表屏幕截图](selection-images/multiple-pre-selection.png "具有多个预选择的 CollectionView 垂直列表")](selection-images/multiple-pre-selection-large.png#lightbox "具有多个预选择的 CollectionView 垂直列表")

## <a name="clear-selections"></a>清除选择

[`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) 通过将和属性设置为，或将其绑定到的对象，可以清除和属性 `null` 。

## <a name="change-selected-item-color"></a>更改选定项的颜色

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 具有 `Selected` [`VisualState`](xref:Xamarin.Forms.VisualState) ，可用于启动对中的选定项的视觉更改 `CollectionView` 。 这种情况的一个常见用例 `VisualState` 是更改选定项的背景色，如下面的 XAML 示例中所示：

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <Style TargetType="Grid">
            <Setter Property="VisualStateManager.VisualStateGroups">
                <VisualStateGroupList>
                    <VisualStateGroup x:Name="CommonStates">
                        <VisualState x:Name="Normal" />
                        <VisualState x:Name="Selected">
                            <VisualState.Setters>
                                <Setter Property="BackgroundColor"
                                        Value="LightSkyBlue" />
                            </VisualState.Setters>
                        </VisualState>
                    </VisualStateGroup>
                </VisualStateGroupList>
            </Setter>
        </Style>
    </ContentPage.Resources>
    <StackLayout Margin="20">
        <CollectionView ItemsSource="{Binding Monkeys}"
                        SelectionMode="Single">
            <CollectionView.ItemTemplate>
                <DataTemplate>
                    <Grid Padding="10">
                        ...
                    </Grid>
                </DataTemplate>
            </CollectionView.ItemTemplate>
        </CollectionView>
    </StackLayout>
</ContentPage>
```

> [!IMPORTANT]
> [`Style`](xref:Xamarin.Forms.Style)包含的 `Selected` `VisualState` [`TargetType`](xref:Xamarin.Forms.Style.TargetType) 属性值必须为的根元素的类型，该类型 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 设置为 `ItemTemplate` 属性值。

在此示例中， [`Style.TargetType`](xref:Xamarin.Forms.Style.TargetType) 属性值设置为， `Grid` 因为的根元素 [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) 是 [`Grid`](xref:Xamarin.Forms.Grid) 。 `Selected` [`VisualState`](xref:Xamarin.Forms.VisualState) 指定在选择中的项时 [`CollectionView`](xref:Xamarin.Forms.CollectionView) ，项的将 [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) 设置为 `LightSkyBlue` ：

[![在 iOS 和 Android 上使用自定义单项选择颜色的 CollectionView 垂直列表屏幕截图](selection-images/single-selection-color.png "使用自定义单项选择颜色的 CollectionView 垂直列表")](selection-images/single-selection-color-large.png#lightbox "使用自定义单项选择颜色的 CollectionView 垂直列表")

若要详细了解可视状态，请参阅 [Xamarin.Forms 可视状态管理器](~/xamarin-forms/user-interface/visual-state-manager.md)。

## <a name="disable-selection"></a>禁用选定内容

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 默认情况下禁用选择。 但是，如果 `CollectionView` 启用了选择，则可以通过将 [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) 属性设置为来禁用它 `None` ：

```xaml
<CollectionView ...
                SelectionMode="None" />
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    SelectionMode = SelectionMode.None
};
```

当 [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) 属性设置为时 `None` ，不能选择中的项， [`CollectionView`](xref:Xamarin.Forms.CollectionView) [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) 属性将保留 `null` ，并且 [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) 不会触发事件。

> [!NOTE]
> 如果已选择某个项并且 [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) 属性从更改 `Single` 为 `None` ，则该 [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) 属性将设置为， `null` 并且 [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) 将使用空属性激发该事件 `CurrentSelection` 。

## <a name="related-links"></a>相关链接

- [CollectionView (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Xamarin.Forms 可视状态管理器](~/xamarin-forms/user-interface/visual-state-manager.md)