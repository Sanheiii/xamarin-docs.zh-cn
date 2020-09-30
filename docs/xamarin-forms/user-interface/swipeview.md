---
title: Xamarin.Forms SwipeView
description: Xamarin.FormsSwipeView 是一个容器控件，该控件环绕一项内容，并提供通过轻扫手势显示的上下文菜单项。
ms.prod: xamarin
ms.assetId: 602456B5-701B-4948-B454-B1F31283F1CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/26/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 178d36eabfdbe0452bec456979fcca89acdf8926
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563336"
---
# <a name="no-locxamarinforms-swipeview"></a>Xamarin.Forms SwipeView

![预发行版 API](~/media/shared/preview.png)

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-swipeviewdemos/)

`SwipeView`是一个容器控件，该控件环绕内容项，并提供通过轻扫手势显示的上下文菜单项：

[![IOS 和 Android 上的 CollectionView 中 SwipeView 的屏幕截图](swipeview-images/swipeview-collectionview.png "SwipeView 刷物品")](swipeview-images/swipeview-collectionview-large.png#lightbox "SwipeView 刷物品")

`SwipeView` 在4.4 中提供 Xamarin.Forms 。 但是，它当前是实验性的，只能通过将以下代码行添加到 iOS 上的类中，将其添加到 Android 上的类，或者添加到你在 UWP 上的类， `AppDelegate` `MainActivity` `App` 然后再调用 `Forms.Init` ：

```csharp
Forms.SetFlags("SwipeView_Experimental");
```

`SwipeView` 定义以下属性:

- `LeftItems`，类型为 `SwipeItems` ，它表示在控件从左侧重击时可以调用的滑动项。
- `RightItems`，类型为 `SwipeItems` ，它表示在控件从右侧重击时可以调用的滑动项。
- `TopItems`，类型为 `SwipeItems` ，它表示可在从上到下重击控件时调用的滑动项。
- `BottomItems`，类型为 `SwipeItems` ，它表示在控件从下到下重击时可以调用的滑动项。

这些属性由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象提供支持；也就是说，它们可以作为数据绑定的目标，并能进行样式设置。

此外，还 `SwipeView` 继承了类中的 [`Content`](xref:Xamarin.Forms.ContentView.Content) 属性 [`ContentView`](xref:Xamarin.Forms.ContentView) 。 `Content`属性是类的 content 属性 `SwipeView` ，因此不需要显式设置。

`SwipeView`类还定义四个事件：

- `SwipeStarted` 当轻扫开始时激发。 `SwipeStartedEventArgs`此事件附带的对象具有 `SwipeDirection` 类型为的属性 `SwipeDirection` 。
- `SwipeChanging` 在移动时激发。 `SwipeChangingEventArgs`此事件附带的对象具有类型为 `SwipeDirection` 的属性 `SwipeDirection` 和 `Offset` 类型为的属性 `double` 。
- `SwipeEnded` 在轻扫结束时激发。 `SwipeEndedEventArgs`此事件附带的对象具有 `SwipeDirection` 类型为的属性 `SwipeDirection` 。
- `CloseRequested` 当滑动项关闭时激发。

此外，还 `SwipeView` 包括 `Open` 和 `Close` 方法，分别以编程方式打开和关闭轻扫项目。

> [!NOTE]
> `SwipeView` 在 iOS 和 Android 上有特定于平台的，用于控制打开时使用的转换 `SwipeView` 。 有关详细信息，请参阅 SwipeView 上的 ["在 iOS 上滑动过渡模式](~/xamarin-forms/platform/ios/swipeview-swipetransitionmode.md) " 和 ["SwipeView"](~/xamarin-forms/platform/android/swipeview-swipetransitionmode.md)。

## <a name="create-a-swipeview"></a>创建 SwipeView

`SwipeView`必须定义环绕的内容 `SwipeView` ，并使用轻扫手势显示的滑动项。 "轻扫" 项是 `SwipeItem` 放置在四个方向集合之一中的一个或多个对象 `SwipeView` ： `LeftItems` 、 `RightItems` 、 `TopItems` 或 `BottomItems` 。

下面的示例演示如何 `SwipeView` 在 XAML 中实例化：

```xaml
<SwipeView>
    <SwipeView.LeftItems>
        <SwipeItems>
            <SwipeItem Text="Favorite"
                       IconImageSource="favorite.png"
                       BackgroundColor="LightGreen"
                       Invoked="OnFavoriteSwipeItemInvoked" />
            <SwipeItem Text="Delete"
                       IconImageSource="delete.png"
                       BackgroundColor="LightPink"
                       Invoked="OnDeleteSwipeItemInvoked" />
        </SwipeItems>
    </SwipeView.LeftItems>
    <!-- Content -->
    <Grid HeightRequest="60"
          WidthRequest="300"
          BackgroundColor="LightGray">
        <Label Text="Swipe right"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
    </Grid>
</SwipeView>
```

等效 C# 代码如下：

```csharp
// SwipeItems
SwipeItem favoriteSwipeItem = new SwipeItem
{
    Text = "Favorite",
    IconImageSource = "favorite.png",
    BackgroundColor = Color.LightGreen
};
favoriteSwipeItem.Invoked += OnFavoriteSwipeItemInvoked;

SwipeItem deleteSwipeItem = new SwipeItem
{
    Text = "Delete",
    IconImageSource = "delete.png",
    BackgroundColor = Color.LightPink
};
deleteSwipeItem.Invoked += OnDeleteSwipeItemInvoked;

List<SwipeItem> swipeItems = new List<SwipeItem>() { favoriteSwipeItem, deleteSwipeItem };

// SwipeView content
Grid grid = new Grid
{
    HeightRequest = 60,
    WidthRequest = 300,
    BackgroundColor = Color.LightGray
};
grid.Children.Add(new Label
{
    Text = "Swipe right",
    HorizontalOptions = LayoutOptions.Center,
    VerticalOptions = LayoutOptions.Center
});

SwipeView swipeView = new SwipeView
{
    LeftItems = new SwipeItems(swipeItems),
    Content = grid
};
```

在此示例中， `SwipeView` 内容是 [`Grid`](xref:Xamarin.Forms.Grid) 包含的 [`Label`](xref:Xamarin.Forms.Label) ：

[![IOS 和 Android 上的 SwipeView 内容的屏幕截图](swipeview-images/swipeview-content.png "SwipeView 内容")](swipeview-images/swipeview-content-large.png#lightbox "SwipeView 内容")

滑动项用于对内容执行操作 `SwipeView` ，并在控件从左侧重击时显示：

[![IOS 和 Android 上的 SwipeView 刷卡器项的屏幕截图](swipeview-images/swipeview-swipeitems.png "SwipeView 刷物品")](swipeview-images/swipeview-swipeitems-large.png#lightbox "SwipeView 刷物品")

默认情况下，当用户点击一项时，将执行一项刷卡器项。 但是，可以更改这种行为。 有关详细信息，请参阅 [扫 mode](#swipe-mode)。

执行某一轻扫项目后，将隐藏该滑动项，并 `SwipeView` 重新显示内容。 但是，可以更改这种行为。 有关详细信息，请参阅 [轻扫行为](#swipe-behavior)。

> [!NOTE]
> 滑动内容和轻扫项目可以以内联方式放置或定义为资源。

## <a name="swipe-items"></a>刷物品

`LeftItems`、 `RightItems` 、 `TopItems` 和 `BottomItems` 集合都属于类型 `SwipeItems` 。 `SwipeItems` 类定义了以下属性：

- `Mode`，类型为 `SwipeMode` ，指示滑动交互的效果。 有关轻扫模式的详细信息，请参阅 [滑动模式](#swipe-mode)。
- `SwipeBehaviorOnInvoked`，类型为 `SwipeBehaviorOnInvoked` ，指示在 `SwipeView` 调用一项刷卡器后的行为方式。 有关轻扫行为的详细信息，请参阅 [轻扫行为](#swipe-behavior)。

这些属性由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象提供支持；也就是说，它们可以作为数据绑定的目标，并能进行样式设置。

每个刷卡器项定义为一个 `SwipeItem` 对象，该对象放入四个 `SwipeItems` 方向集合之一。 `SwipeItem`类派生自 [`MenuItem`](xref:Xamarin.Forms.MenuItem) 类，并添加以下成员：

- 一个 `BackgroundColor` 类型为的属性， `Color` 用于定义刷卡器项的背景色。 此属性由可绑定的属性支持。
- `Invoked`事件，该事件在执行刷卡器项时触发。

> [!IMPORTANT]
> [`MenuItem`](xref:Xamarin.Forms.MenuItem)类定义多个属性，包括 `Command` 、 `CommandParameter` 、 `IconImageSource` 和 `Text` 。 可以在对象上设置这些属性 `SwipeItem` 以定义其外观，并定义在 `ICommand` 调用轻扫项目时执行的。 有关详细信息，请参阅[ Xamarin.Forms MenuItem](~/xamarin-forms/user-interface/menuitem.md)。

下面的示例显示集合中的两个 `SwipeItem` 对象 `LeftItems` `SwipeView` ：

```xaml
<SwipeView>
    <SwipeView.LeftItems>
        <SwipeItems>
            <SwipeItem Text="Favorite"
                       IconImageSource="favorite.png"
                       BackgroundColor="LightGreen"
                       Invoked="OnFavoriteSwipeItemInvoked" />
            <SwipeItem Text="Delete"
                       IconImageSource="delete.png"
                       BackgroundColor="LightPink"
                       Invoked="OnDeleteSwipeItemInvoked" />
        </SwipeItems>
    </SwipeView.LeftItems>
    <!-- Content -->
</SwipeView>
```

每个的外观 `SwipeItem` 均由 `Text` 、 `IconImageSource` 和属性的组合定义 `BackgroundColor` ：

[![IOS 和 Android 上的 SwipeView 刷卡器项的屏幕截图](swipeview-images/swipeview-swipeitems.png "SwipeView 刷物品")](swipeview-images/swipeview-swipeitems-large.png#lightbox "SwipeView 刷物品")

点击时 `SwipeItem` ，它的 `Invoked` 事件将激发并由其注册的事件处理程序进行处理。 或者， `Command` 可以将属性设置为 `ICommand` 在调用时将执行的实现 `SwipeItem` 。

> [!NOTE]
> 如果 `SwipeItem` 仅使用或属性定义了的外观 `Text` ，则 `IconImageSource` 内容始终居中。

除了将 "轻扫项目" 定义为 `SwipeItem` 对象之外，还可以定义自定义轻扫项目视图。 有关详细信息，请参阅 [自定义滑动项](#custom-swipe-items)。

## <a name="swipe-direction"></a>滑动方向

`SwipeView` 支持四种不同的轻扫方向，并通过将 `SwipeItems` 对象添加到的方向集合来定义滑动方向 `SwipeItem` 。 每个轻扫方向都可以容纳自己的滑动项。 例如，下面的示例演示了一个 `SwipeView` 其轻扫的项取决于轻扫方向的：

```xaml
<SwipeView>
    <SwipeView.LeftItems>
        <SwipeItems>
            <SwipeItem Text="Delete"
                       IconImageSource="delete.png"
                       BackgroundColor="LightPink"
                       Command="{Binding DeleteCommand}" />
        </SwipeItems>
    </SwipeView.LeftItems>
    <SwipeView.RightItems>
        <SwipeItems>
            <SwipeItem Text="Favorite"
                       IconImageSource="favorite.png"
                       BackgroundColor="LightGreen"
                       Command="{Binding FavoriteCommand}" />
            <SwipeItem Text="Share"
                       IconImageSource="share.png"
                       BackgroundColor="LightYellow"
                       Command="{Binding ShareCommand}" />
        </SwipeItems>
    </SwipeView.RightItems>
    <!-- Content -->
</SwipeView>
```

在此示例中， `SwipeView` 内容可以是重击的。 向右轻扫将显示 " **删除** " "删除" 项，同时向左轻扫会显示 " **收藏** " 和 " **共享** " 轻扫项目。

> [!WARNING]
> 一次只能设置一个方向 `SwipeItems` 集合实例 `SwipeView` 。 因此，在上不能有两个 `LeftItems` 定义 `SwipeView` 。

`SwipeStarted`、 `SwipeChanging` 和 `SwipeEnded` 事件通过 `SwipeDirection` 事件自变量中的属性来报告滑动方向。 此属性的类型为 `SwipeDirection` ，它是由四个成员组成的枚举：

- `Right` 指示发生了向右轻扫。
- `Left` 指示发生了左轻扫。
- `Up` 指示出现向上的扫。
- `Down` 指示发生向下轻扫。

## <a name="swipe-mode"></a>扫 mode 模式

`SwipeItems`类具有一个 `Mode` 属性，该属性指示滑动交互的效果。 应将此属性设置为 `SwipeMode` 枚举成员之一：

- `Reveal` 指示刷显示滑动的项目。 这是 `SwipeItems.Mode` 属性的默认值。
- `Execute` 指示轻扫执行轻扫项目。

在显示模式下，用户 swipes `SwipeView` 打开一个菜单，其中包含一个或多个 "轻扫" 项 执行了 "轻扫" 项后，将关闭滑动项，并 `SwipeView` 重新显示内容。 在执行模式下，用户 swipes `SwipeView` 打开一个菜单，其中包含一个更轻扫的项，然后自动执行。 执行后，将关闭滑动项，并 `SwipeView` 重新显示内容。

下面的示例显示了 `SwipeView` 配置为使用执行模式的：

```xaml
<SwipeView>
    <SwipeView.LeftItems>
        <SwipeItems Mode="Execute">
            <SwipeItem Text="Delete"
                       IconImageSource="delete.png"
                       BackgroundColor="LightPink"
                       Command="{Binding DeleteCommand}" />
        </SwipeItems>
    </SwipeView.LeftItems>
    <!-- Content -->
</SwipeView>
```

在此示例中， `SwipeView` 可以重击内容来显示立即执行的轻扫项目。 执行后，将 `SwipeView` 重新显示内容。

## <a name="swipe-behavior"></a>轻扫行为

`SwipeItems`类具有一个 `SwipeBehaviorOnInvoked` 属性，该属性指示如何在 `SwipeView` 调用刷卡器项后的行为。 应将此属性设置为 `SwipeBehaviorOnInvoked` 枚举成员之一：

- `Auto` 指示在显示模式下，在 `SwipeView` 调用 "轻扫" 项后关闭，在 "执行模式" 中， `SwipeView` 调用 "轻扫" 项后，将保持打开状态。 这是 `SwipeItems.SwipeBehaviorOnInvoked` 属性的默认值。
- `Close` 指示在 `SwipeView` 调用扫一项后关闭。
- `RemainOpen` 指示在 `SwipeView` 调用一项刷卡器后保持打开状态。

下面的示例演示 `SwipeView` 如何将配置为在调用刷卡器项后保持打开状态：

```xaml
<SwipeView>
    <SwipeView.LeftItems>
        <SwipeItems SwipeBehaviorOnInvoked="RemainOpen">
            <SwipeItem Text="Favorite"
                       IconImageSource="favorite.png"
                       BackgroundColor="LightGreen"
                       Invoked="OnFavoriteSwipeItemInvoked" />
            <SwipeItem Text="Delete"
                       IconImageSource="delete.png"
                       BackgroundColor="LightPink"
                       Invoked="OnDeleteSwipeItemInvoked" />
        </SwipeItems>
    </SwipeView.LeftItems>
    <!-- Content -->
</SwipeView>
```

## <a name="custom-swipe-items"></a>自定义滑动项

可以用类型定义自定义滑动项 `SwipeItemView` 。 `SwipeItemView`类派生自 [`ContentView`](xref:Xamarin.Forms.ContentView) 类，并添加以下属性：

- `Command`，类型为 `ICommand` ，在点击轻扫项时执行。
- `CommandParameter`，属于 `object` 类型，是传递给 `Command` 的参数。

这些属性由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象提供支持；也就是说，它们可以作为数据绑定的目标，并能进行样式设置。

在 `SwipeItemView` 执行后，类还定义在 `Invoked` 点击项时触发的事件 `Command` 。

下面的示例显示 `SwipeItemView` 了的集合中的 `LeftItems` 一个对象 `SwipeView` ：

```xaml
<SwipeView>
    <SwipeView.LeftItems>
        <SwipeItems>
            <SwipeItemView Command="{Binding CheckAnswerCommand}"
                           CommandParameter="{Binding Source={x:Reference resultEntry}, Path=Text}">
                <StackLayout Margin="10"
                             WidthRequest="300">
                    <Entry x:Name="resultEntry"
                           Placeholder="Enter answer"
                           HorizontalOptions="CenterAndExpand" />
                    <Label Text="Check"
                           FontAttributes="Bold"
                           HorizontalOptions="Center" />
                </StackLayout>
            </SwipeItemView>
        </SwipeItems>
    </SwipeView.LeftItems>
    <!-- Content -->
</SwipeView>
```

在此示例中， `SwipeItemView` 包含一个 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ，它包含一个 [`Entry`](xref:Xamarin.Forms.Entry) 和一个 [`Label`](xref:Xamarin.Forms.Label) 。 用户将输入输入到之后，中的 `Entry` 其余部分 `SwipeViewItem` 可用于执行 `ICommand` 属性定义的 `SwipeItemView.Command` 。

## <a name="open-and-close-a-swipeview-programmatically"></a>以编程方式打开和关闭 SwipeView

`SwipeView` 包括 `Open` 和 `Close` 方法，分别以编程方式打开和关闭轻扫项目。

`Open`方法需要 `OpenSwipeItem` 自变量，以指定 `SwipeView` 将从打开的方向。 `OpenSwipeItem`枚举具有四个成员：

- `LeftItems`，指示 `SwipeView` 将从左侧打开，以显示集合中的滑动项 `LeftItems` 。
- `TopItems`，它指示 `SwipeView` 将从顶部打开，以显示集合中的滑动项 `TopItems` 。
- `RightItems`，指示 `SwipeView` 将从右侧打开，以显示集合中的滑动项 `RightItems` 。
- `BottomItems`，它指示 `SwipeView` 将从底部打开，以显示集合中的滑动项 `BottomItems` 。

给定一个 `SwipeView` 命名的 `swipeView` ，下面的示例演示如何打开， `SwipeView` 以显示集合中的滑动项 `LeftItems` ：

```csharp
swipeView.Open(OpenSwipeItem.LeftItems);
```

`swipeView`然后，可以通过 `Close` 方法关闭：

```csharp
swipeView.Close();
```

> [!NOTE]
> `Close`调用方法时，将 `CloseRequested` 触发事件。

## <a name="disable-a-swipeview"></a>禁用 SwipeView

应用程序可能进入一种状态，在该状态下轻扫内容项不是有效操作。 在这种情况下， `SwipeView` 可以通过将其 `IsEnabled` 属性设置为来禁用 `false` 。 这会阻止用户刷内容以显示滑动项目。

此外，在定义或的 `Command` 属性时 `SwipeItem` `SwipeItemView` ， `CanExecute` `ICommand` 可以指定的委托来启用或禁用轻扫项。

## <a name="related-links"></a>相关链接

- [SwipeView (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-swipeviewdemos/)
- [Xamarin.Forms 项](~/xamarin-forms/user-interface/menuitem.md)