---
title: Xamarin.Forms CarouselView 交互
description: 可以通过 CurrentItem 和位置属性访问 CarouselView 中当前显示的项。
ms.prod: xamarin
ms.assetid: 854D97E5-D119-4BE2-AE7C-BD428792C992
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/11/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 43f2e32a240b74fde50659033096c8a7246ea60c
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91564051"
---
# <a name="no-locxamarinforms-carouselview-interaction"></a>Xamarin.Forms CarouselView 交互

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView) 定义以下属性，这些属性控制用户交互：

- `CurrentItem`，类型为 `object` ，当前正在显示的项。 此属性的默认绑定模式为 `TwoWay` ， `null` 如果没有要显示的数据，则具有值。
- `CurrentItemChangedCommand`，类型 `ICommand` 为，在当前项发生更改时执行。
- `CurrentItemChangedCommandParameter`，属于 `object` 类型，是传递给 `CurrentItemChangedCommand` 的参数。
- `IsBounceEnabled`，类型为 `bool` ，指定是否 `CarouselView` 将在内容边界处弹跳。 默认值为 `true`。
- `IsSwipeEnabled`，类型为 `bool` ，确定滑动手势是否将更改显示的项。 默认值为 `true`。
- `Position`，类型为 `int` 基础集合中当前项的索引。 此属性的默认绑定模式为 `TwoWay` ，如果没有要显示的数据，则其值为0。
- `PositionChangedCommand`，类型 `ICommand` 为，在位置更改时执行。
- `PositionChangedCommandParameter`，属于 `object` 类型，是传递给 `PositionChangedCommand` 的参数。
- `VisibleViews`，类型为 `ObservableCollection<View>` ，它是一个只读属性，其中包含当前可见项的对象。

所有这些属性都由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象提供支持，这意味着这些属性可以作为数据绑定的目标。

[`CarouselView`](xref:Xamarin.Forms.CarouselView) 定义一个 `CurrentItemChanged` 事件，该事件在属性更改时触发， `CurrentItem` 无论是用户滚动还是应用程序设置属性。 `CurrentItemChangedEventArgs`事件附带的对象 `CurrentItemChanged` 有两个属性，两者均为类型 `object` ：

- `PreviousItem` –上一项在属性更改后发生。
- `CurrentItem` –属性更改后的当前项。

[`CarouselView`](xref:Xamarin.Forms.CarouselView) 还定义一个 `PositionChanged` 事件，该事件在属性更改时触发， `Position` 无论是用户滚动还是应用程序设置属性。 `PositionChangedEventArgs`事件附带的对象 `PositionChanged` 有两个属性，两者均为类型 `int` ：

- `PreviousPosition` –在属性更改后的上一个位置。
- `CurrentPosition` –属性更改后的当前位置。

## <a name="respond-to-the-current-item-changing"></a>响应当前项更改

当当前显示的项发生更改时， `CurrentItem` 属性将设置为该项的值。 此属性发生更改时，将 `CurrentItemChangedCommand` 用 `CurrentItemChangedCommandParameter` 传递到的值来执行 `ICommand` 。 `Position`然后更新属性，并 `CurrentItemChanged` 激发该事件。

> [!IMPORTANT]
> `Position`当属性更改时，属性将更改 `CurrentItem` 。 这将导致 `PositionChangedCommand` 执行，并 `PositionChanged` 触发事件。

### <a name="event"></a>事件

下面的 XAML 示例显示了一个 [`CarouselView`](xref:Xamarin.Forms.CarouselView) ，它使用事件处理程序来响应当前项更改：

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              CurrentItemChanged="OnCurrentItemChanged">
    ...
</CarouselView>
```

等效 C# 代码如下：

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
carouselView.CurrentItemChanged += OnCurrentItemChanged;
```

在此示例中，事件 `OnCurrentItemChanged` 处理程序在 `CurrentItemChanged` 事件激发时执行：

```csharp
void OnCurrentItemChanged(object sender, CurrentItemChangedEventArgs e)
{
    Monkey previousItem = e.PreviousItem as Monkey;
    Monkey currentItem = e.CurrentItem as Monkey;
}
```

在此示例中， `OnCurrentItemChanged` 事件处理程序将公开以前的和当前的项：

[![IOS 和 Android 上的 CarouselView 与以前的和当前项的屏幕截图](interaction-images/current-item-events.png "包含当前和上一项的 CarouselView")](interaction-images/current-item-events-large.png#lightbox "包含当前和上一项的 CarouselView")

### <a name="command"></a>Command

下面的 XAML 示例显示了一个 [`CarouselView`](xref:Xamarin.Forms.CarouselView) ，它使用命令对当前项的更改进行响应：

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              CurrentItemChangedCommand="{Binding ItemChangedCommand}"
              CurrentItemChangedCommandParameter="{Binding Source={RelativeSource Self}, Path=CurrentItem}">
    ...
</CarouselView>
```

等效 C# 代码如下：

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
carouselView.SetBinding(CarouselView.CurrentItemChangedCommandProperty, "ItemChangedCommand");
carouselView.SetBinding(CarouselView.CurrentItemChangedCommandParameterProperty, new Binding("CurrentItem", source: RelativeBindingSource.Self));
```

在此示例中， `CurrentItemChangedCommand` 属性绑定到 `ItemChangedCommand` 属性，并将 `CurrentItem` 属性值作为参数传递给它。 `ItemChangedCommand`然后，可以根据需要对当前项进行响应更改：

```csharp
public ICommand ItemChangedCommand => new Command<Monkey>((item) =>
{
    PreviousMonkey = CurrentMonkey;
    CurrentMonkey = item;
});
```

在此示例中，将 `ItemChangedCommand` 更新存储以前和当前项的对象。

## <a name="respond-to-the-position-changing"></a>响应位置变化

当当前显示的项发生更改时， `Position` 属性将设置为基础集合中当前项的索引。 此属性发生更改时，将 `PositionChangedCommand` 用 `PositionChangedCommandParameter` 传递到的值来执行 `ICommand` 。 然后，将 `PositionChanged` 触发该事件。 如果 `Position` 已以编程方式更改属性，则 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 会滚动到对应于值的项 `Position` 。

> [!NOTE]
> `Position`将属性设置为0将导致显示基础集合中的第一项。

### <a name="event"></a>事件

下面的 XAML 示例显示了一个 [`CarouselView`](xref:Xamarin.Forms.CarouselView) ，它使用事件处理程序来响应 `Position` 属性更改：

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"              
              PositionChanged="OnPositionChanged">
    ...
</CarouselView>
```

等效 C# 代码如下：

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
carouselView.PositionChanged += OnPositionChanged;
```

在此示例中，事件 `OnPositionChanged` 处理程序在 `PositionChanged` 事件激发时执行：

```csharp
void OnPositionChanged(object sender, PositionChangedEventArgs e)
{
    int previousItemPosition = e.PreviousPosition;
    int currentItemPosition = e.CurrentPosition;
}
```

在此示例中， `OnCurrentItemChanged` 事件处理程序将公开以前和当前的位置：

[![IOS 和 Android 上的 CarouselView，其中包含上一个和当前位置](interaction-images/current-position-events.png "具有当前和先前位置的 CarouselView")](interaction-images/current-position-events-large.png#lightbox "具有当前和先前位置的 CarouselView")

### <a name="command"></a>Command

下面的 XAML 示例显示了一个 [`CarouselView`](xref:Xamarin.Forms.CarouselView) ，它使用命令来响应 `Position` 属性更改：

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              PositionChangedCommand="{Binding PositionChangedCommand}"
              PositionChangedCommandParameter="{Binding Source={RelativeSource Self}, Path=Position}">
    ...
</CarouselView>
```

等效 C# 代码如下：

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
carouselView.SetBinding(CarouselView.PositionChangedCommandProperty, "PositionChangedCommand");
carouselView.SetBinding(CarouselView.PositionChangedCommandParameterProperty, new Binding("Position", source: RelativeBindingSource.Self));
```

在此示例中， `PositionChangedCommand` 属性绑定到 `PositionChangedCommand` 属性，并将 `Position` 属性值作为参数传递给它。 `PositionChangedCommand`然后，可以根据需要响应位置变化：

```csharp
public ICommand PositionChangedCommand => new Command<int>((position) =>
{
    PreviousPosition = CurrentPosition;
    CurrentPosition = position;
});
```

在此示例中，将 `PositionChangedCommand` 更新存储以前和当前位置的对象。

## <a name="preset-the-current-item"></a>预设当前项

[`CarouselView`](xref:Xamarin.Forms.CarouselView)通过将属性设置为项，可以通过编程方式设置中的当前项 `CurrentItem` 。 下面的 XAML 示例显示了一个 `CarouselView` 预先选择当前项的：

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              CurrentItem="{Binding CurrentItem}">
    ...
</CarouselView>
```

等效 C# 代码如下：

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
carouselView.SetBinding(CarouselView.CurrentItemProperty, "CurrentItem");
```

> [!NOTE]
> `CurrentItem`属性的默认绑定模式为 `TwoWay` 。

`CarouselView.CurrentItem`属性数据绑定到类型为的 `CurrentItem` 已连接视图模型的属性 `Monkey` 。 默认情况下， `TwoWay` 使用绑定，以便在用户更改当前项时，属性的值 `CurrentItem` 将设置为当前的 `Monkey` 对象。 `CurrentItem`属性在类中定义 `MonkeysViewModel` ：

```csharp
public class MonkeysViewModel : INotifyPropertyChanged
{
    // ...
    public ObservableCollection<Monkey> Monkeys { get; private set; }

    public Monkey CurrentItem { get; set; }

    public MonkeysViewModel()
    {
        // ...
        CurrentItem = Monkeys.Skip(3).FirstOrDefault();
        OnPropertyChanged("CurrentItem");
    }
}
```

在此示例中， `CurrentItem` 属性设置为集合中的第四项 `Monkeys` ：

[![IOS 和 Android 上带有预设项的 CarouselView 的屏幕截图](interaction-images/preset-item.png "带有预设项的 CarouselView")](interaction-images/preset-item-large.png#lightbox "带有预设项的 CarouselView")

## <a name="preset-the-position"></a>预设位置

[`CarouselView`](xref:Xamarin.Forms.CarouselView)可以通过将 `Position` 属性设置为基础集合中项的索引，以编程方式设置显示的项。 下面的 XAML 示例显示了一个 `CarouselView` 用于设置显示项的：

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              Position="{Binding Position}">
    ...
</CarouselView>
```

等效 C# 代码如下：

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
carouselView.SetBinding(CarouselView.PositionProperty, "Position");
```

> [!NOTE]
> `Position`属性的默认绑定模式为 `TwoWay` 。

`CarouselView.Position`属性数据绑定到类型为的 `Position` 已连接视图模型的属性 `int` 。 默认情况下， `TwoWay` 使用绑定，以便在用户滚动时 [`CarouselView`](xref:Xamarin.Forms.CarouselView) ，属性的值 `Position` 将设置为所显示的项的索引。 `Position`属性在类中定义 `MonkeysViewModel` ：

```csharp
public class MonkeysViewModel : INotifyPropertyChanged
{
    // ...
    public int Position { get; set; }

    public MonkeysViewModel()
    {
        // ...
        Position = 3;
        OnPropertyChanged("Position");
    }
}
```

在此示例中， `Position` 属性设置为集合中的第四项 `Monkeys` ：

[![在 iOS 和 Android 上具有预设位置的 CarouselView 的屏幕截图](interaction-images/preset-position.png "带有预设位置的 CarouselView")](interaction-images/preset-position-large.png#lightbox "带有预设位置的 CarouselView")

## <a name="define-visual-states"></a>定义可视状态

[`CarouselView`](xref:Xamarin.Forms.CarouselView) 定义四种可视状态：

- `CurrentItem` 表示当前显示项的可视状态。
- `PreviousItem` 表示以前显示的项的可视状态。
- `NextItem` 表示下一项的可视状态。
- `DefaultItem` 表示项的其余部分的可视状态。

这些可视状态可用于对所显示的项启动视觉对象更改 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 。

下面的 XAML 示例演示如何定义 `CurrentItem` 、 `PreviousItem` 、 `NextItem` 和 `DefaultItem` 可视状态：

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              PeekAreaInsets="100">
    <CarouselView.ItemTemplate>
        <DataTemplate>
            <StackLayout>
                <VisualStateManager.VisualStateGroups>
                    <VisualStateGroup x:Name="CommonStates">
                        <VisualState x:Name="CurrentItem">
                            <VisualState.Setters>
                                <Setter Property="Scale"
                                        Value="1.1" />
                            </VisualState.Setters>
                        </VisualState>
                        <VisualState x:Name="PreviousItem">
                            <VisualState.Setters>
                                <Setter Property="Opacity"
                                        Value="0.5" />
                            </VisualState.Setters>
                        </VisualState>
                        <VisualState x:Name="NextItem">
                            <VisualState.Setters>
                                <Setter Property="Opacity"
                                        Value="0.5" />
                            </VisualState.Setters>
                        </VisualState>
                        <VisualState x:Name="DefaultItem">
                            <VisualState.Setters>
                                <Setter Property="Opacity"
                                        Value="0.25" />
                            </VisualState.Setters>
                        </VisualState>
                    </VisualStateGroup>
                </VisualStateManager.VisualStateGroups>

                <!-- Item template content -->
                <Frame HasShadow="true">
                    ...
                </Frame>
            </StackLayout>
        </DataTemplate>
    </CarouselView.ItemTemplate>
</CarouselView>
```

在此示例中， `CurrentItem` 可视状态指定显示的当前项的 [`CarouselView`](xref:Xamarin.Forms.CarouselView) [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) 属性将从其默认值1更改为1.1。 `PreviousItem`和 `NextItem` 可视状态指定将显示当前项周围的项，其 [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) 值为0.5。 `DefaultItem`可视状态指定显示的项的剩余部分 `CarouselView` 将显示 `Opacity` 值为0.25。

> [!NOTE]
> 此外，还可以在 [`Style`](xref:Xamarin.Forms.Style) 具有 [`TargetType`](xref:Xamarin.Forms.Style.TargetType) 属性值（其为的根元素的类型 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ，它设置为 `ItemTemplate` 属性值）的中定义可视状态。

以下屏幕截图显示 `CurrentItem` 、 `PreviousItem` 和 `NextItem` 可视状态：

[![在 iOS 和 Android 上使用可视状态显示 CarouselView 的屏幕截图](interaction-images/visual-states.png "CarouselView 视觉状态")](interaction-images/visual-states-large.png#lightbox "CarouselView 视觉状态")

若要详细了解可视状态，请参阅 [Xamarin.Forms 可视状态管理器](~/xamarin-forms/user-interface/visual-state-manager.md)。

## <a name="clear-the-current-item"></a>清除当前项

`CurrentItem`可以通过将属性或它绑定到的对象设置为来清除该属性 `null` 。

## <a name="disable-bounce"></a>禁用弹跳

默认情况下， [`CarouselView`](xref:Xamarin.Forms.CarouselView) 弹跳内容边界处的项。 可以通过将属性设置为来禁用此设置 `IsBounceEnabled` `false` 。

## <a name="disable-swipe-interaction"></a>禁用滑动交互

默认情况下， [`CarouselView`](xref:Xamarin.Forms.CarouselView) 允许用户使用滑动手势在项目间移动。 可以通过将属性设置为来禁用此滑动交互 `IsSwipeEnabled` `false` 。

## <a name="related-links"></a>相关链接

- [CarouselView (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
- [Xamarin.Forms 可视状态管理器](~/xamarin-forms/user-interface/visual-state-manager.md)