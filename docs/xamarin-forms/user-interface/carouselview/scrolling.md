---
title: Xamarin.Forms CarouselView 滚动
description: 当用户 swipes 启动滚动时，可以控制滚动的结束位置，以便完全显示项。 此外，CarouselView 还定义了两个 ScrollTo 方法，这些方法以编程方式将项滚动到视图中。
ms.prod: xamarin
ms.assetid: 92D7B618-07FA-4343-9D0F-212525E92C39
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/28/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5e48b962130acf98aa99c83773318d077dde2666
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556641"
---
# <a name="no-locxamarinforms-carouselview-scrolling"></a>Xamarin.Forms CarouselView 滚动

![预发行版 API](~/media/shared/preview.png)

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView) 定义以下与滚动相关的属性：

- `HorizontalScrollBarVisibility`，类型为 `ScrollBarVisibility` ，用于指定水平滚动条可见的时间。
- `IsDragging`，类型为 `bool` ，指示是否 `CarouselView` 正在滚动。 这是只读属性，其默认值为 `false` 。
- `IsScrollAnimated`，类型为 `bool` ，用于指定滚动时是否会发生动画 `CarouselView` 。 默认值为 `true`。
- `ItemsUpdatingScrollMode`，类型为 `ItemsUpdatingScrollMode` ，表示在 `CarouselView` 添加新项时的滚动行为。
- `VerticalScrollBarVisibility`，类型为 `ScrollBarVisibility` ，它指定垂直滚动条可见的时间。

所有这些属性都是由对象支持的 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，这意味着它们可以是数据绑定的目标。

[`CarouselView`](xref:Xamarin.Forms.CarouselView) 还定义了两种 [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) 方法，用于将项滚动到视图中。 其中一个重载将指定索引处的项滚动到视图中，而另一个重载将指定项滚动到视图中。 这两个重载都有其他参数，可以指定该参数以指示滚动完成后项的准确位置，以及是否对滚动进行动画处理。

[`CarouselView`](xref:Xamarin.Forms.CarouselView) 定义在 [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) 调用其中一个方法时激发的事件 [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) 。 [`ScrollToRequestedEventArgs`](xref:Xamarin.Forms.ScrollToRequestedEventArgs)事件附带的对象 `ScrollToRequested` 具有多个属性，包括 `IsAnimated` 、、 `Index` `Item` 和 `ScrollToPosition` 。 这些属性是从方法调用中指定的参数设置的 `ScrollTo` 。

此外，还 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 定义了一个 `Scrolled` 事件，该事件将激发以指示发生滚动。 `ItemsViewScrolledEventArgs`事件附带的对象 `Scrolled` 具有多个属性。 有关详细信息，请参阅 [检测滚动](#detect-scrolling)。

当用户 swipes 启动滚动时，可以控制滚动的结束位置，以便完全显示项。 此功能称为 "对齐"，因为当滚动停止时，项会对齐到位置。 有关详细信息，请参阅 [对齐点](#snap-points)。

[`CarouselView`](xref:Xamarin.Forms.CarouselView) 还可以在用户滚动时以增量方式加载数据。 有关详细信息，请参阅 [以增量方式加载数据](populate-data.md#load-data-incrementally)。

## <a name="detect-scrolling"></a>检测滚动

`IsDragging`可以检查属性以确定 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 当前是否正在滚动项目。

此外，还 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 定义了一个 `Scrolled` 事件，该事件将激发以指示发生滚动。 当需要有关滚动的数据时，应使用此事件。

下面的 XAML 示例显示了一个 `CarouselView` ，它设置事件的事件处理程序 `Scrolled` ：

```xaml
<CarouselView Scrolled="OnCollectionViewScrolled">
    ...
</CarouselView>
```

等效 C# 代码如下：

```csharp
CarouselView carouselView = new CarouselView();
carouselView.Scrolled += OnCarouselViewScrolled;
```

在此代码示例中，事件 `OnCarouselViewScrolled` 处理程序在 `Scrolled` 事件激发时执行：

```csharp
void OnCarouselViewScrolled(object sender, ItemsViewScrolledEventArgs e)
{
    Debug.WriteLine("HorizontalDelta: " + e.HorizontalDelta);
    Debug.WriteLine("VerticalDelta: " + e.VerticalDelta);
    Debug.WriteLine("HorizontalOffset: " + e.HorizontalOffset);
    Debug.WriteLine("VerticalOffset: " + e.VerticalOffset);
    Debug.WriteLine("FirstVisibleItemIndex: " + e.FirstVisibleItemIndex);
    Debug.WriteLine("CenterItemIndex: " + e.CenterItemIndex);
    Debug.WriteLine("LastVisibleItemIndex: " + e.LastVisibleItemIndex);
}
```

在此示例中， `OnCarouselViewScrolled` 事件处理程序输出 `ItemsViewScrolledEventArgs` 事件随附的对象的值。

> [!IMPORTANT]
> `Scrolled`触发用户启动滚动的事件和以编程方式滚动事件。

## <a name="scroll-an-item-at-an-index-into-view"></a>将索引中的项滚动到视图中

第一 [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) 种方法重载将指定索引处的项滚动到视图中。 给定一个 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 名为的对象 `carouselView` ，下面的示例演示如何将索引6处的项滚动到视图中：

```csharp
carouselView.ScrollTo(6);
```

> [!NOTE]
> [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested)调用方法时，将触发事件 [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) 。

## <a name="scroll-an-item-into-view"></a>将项滚动到视图

第二个 [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) 方法重载将指定项滚动到视图中。 给定一个 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 名为的对象 `carouselView` ，下面的示例演示如何将 Proboscis 的猴子项滚动到视图中：

```csharp
MonkeysViewModel viewModel = BindingContext as MonkeysViewModel;
Monkey monkey = viewModel.Monkeys.FirstOrDefault(m => m.Name == "Proboscis Monkey");
carouselView.ScrollTo(monkey);
```

> [!NOTE]
> [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested)调用方法时，将触发事件 [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) 。

## <a name="disable-scroll-animation"></a>禁用滚动动画

当在中的项之间移动时，将显示滚动动画 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 。 此动画同时用于用户启动滚动和以编程方式滚动。 将 `IsScrollAnimated` 属性设置为 `false` 将为两个滚动类别禁用动画。

此外，还 `animate` `ScrollTo` 可以将方法的参数设置为， `false` 以在编程滚动时禁用滚动动画：

```csharp
carouselView.ScrollTo(monkey, animate: false);
```

## <a name="control-scroll-position"></a>控制滚动位置

在将项滚动到视图中时，可以使用方法的参数指定项在滚动完成后的确切位置 `position` [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) 。 此参数接受 [`ScrollToPosition`](xref:Xamarin.Forms.ScrollToPosition) 枚举成员。

### <a name="makevisible"></a>MakeVisible

[`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition)成员指示应滚动项，直到它在视图中可见：

```csharp
carouselView.ScrollTo(monkey, position: ScrollToPosition.MakeVisible);
```

此示例代码将导致滚动项进入视图所需的最少滚动。

> [!NOTE]
> [`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition)默认情况下，如果在 `position` 调用方法时未指定参数，则使用成员 `ScrollTo` 。

### <a name="start"></a>开始

[`ScrollToPosition.Start`](xref:Xamarin.Forms.ScrollToPosition)成员指示该项应滚动到视图的开头：

```csharp
carouselView.ScrollTo(monkey, position: ScrollToPosition.Start);
```

此示例代码会将项滚动到视图的开头。

### <a name="center"></a>Center

[`ScrollToPosition.Center`](xref:Xamarin.Forms.ScrollToPosition)成员指示该项应滚动到视图的中心：

```csharp
carouselViewView.ScrollTo(monkey, position: ScrollToPosition.Center);
```

此示例代码会将项滚动到视图的中心。

### <a name="end"></a>结束

[`ScrollToPosition.End`](xref:Xamarin.Forms.ScrollToPosition)成员指示该项应滚动到视图的末尾：

```csharp
carouselViewView.ScrollTo(monkey, position: ScrollToPosition.End);
```

此示例代码会将项滚动到视图末尾。

## <a name="control-scroll-position-when-new-items-are-added"></a>在添加新项时控制滚动位置

[`CarouselView`](xref:Xamarin.Forms.CarouselView) 定义一个 `ItemsUpdatingScrollMode` 属性，该属性由可绑定的属性支持。 此属性获取或设置一个 `ItemsUpdatingScrollMode` 枚举值，该值表示在 `CarouselView` 添加新项时的滚动行为。 `ItemsUpdatingScrollMode` 枚举定义下列成员：

- `KeepItemsInView` 调整滚动偏移量，以便在添加新项时保持显示第一个可见项。
- `KeepScrollOffset` 在添加新项时，使滚动偏移量相对于列表的开头保持。
- `KeepLastItemInView` 调整滚动偏移量，以便在添加新项时使最后一项可见。

属性的默认值 `ItemsUpdatingScrollMode` 为 `KeepItemsInView` 。 因此，将新项添加到 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 列表中的第一个可见项时，将保持显示。 若要确保新添加的项始终显示在列表的底部， `ItemsUpdatingScrollMode` 应将属性设置为 `KeepLastItemInView` ：

```xaml
<CarouselView ItemsUpdatingScrollMode="KeepLastItemInView">
    ...
</CarouselView>
```

等效 C# 代码如下：

```csharp
CarouselView carouselView = new CarouselView
{
    ItemsUpdatingScrollMode = ItemsUpdatingScrollMode.KeepLastItemInView
};
```

## <a name="scroll-bar-visibility"></a>滚动条可见性

[`CarouselView`](xref:Xamarin.Forms.CarouselView) 定义 `HorizontalScrollBarVisibility` `VerticalScrollBarVisibility` 由可绑定属性支持的和属性。 这些属性可获取或设置一个 [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility) 枚举值，该值表示水平或垂直滚动条可见的时间。 `ScrollBarVisibility` 枚举定义下列成员：

- [`Default`](xref:Xamarin.Forms.ScrollBarVisibility) 指示平台的默认滚动条行为，是和属性的默认值 `HorizontalScrollBarVisibility` `VerticalScrollBarVisibility` 。
- [`Always`](xref:Xamarin.Forms.ScrollBarVisibility) 指示滚动条是可见的，即使在视图中显示内容时也是如此。
- [`Never`](xref:Xamarin.Forms.ScrollBarVisibility) 指示即使内容无法在视图中显示，也不会显示滚动条。

## <a name="snap-points"></a>贴靠点

当用户 swipes 启动滚动时，可以控制滚动的结束位置，以便完全显示项。 此功能称为 "对齐"，因为当滚动停止时项将对齐到位置，并由类中的以下属性控制 [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) ：

- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType)类型为的 [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType) 指定滚动时对齐点的行为。
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment)类型为的 [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment) 指定对齐点如何与项对齐。

这些属性是由对象支持的 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，这意味着属性可以是数据绑定的目标。

> [!NOTE]
> 发生对齐时，它将以生成最小动作量的方向发生。

### <a name="snap-points-type"></a>对齐点类型

[`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType)枚举定义以下成员：

- `None` 指示滚动不会对齐到项。
- `Mandatory` 指示内容始终对齐到最接近的对齐点，滚动点沿惯性方向自然停止。
- `MandatorySingle` 指示与相同的行为 `Mandatory` ，但一次只滚动一项。

默认情况下，在上 [`CarouselView`](xref:Xamarin.Forms.CarouselView) ， [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType) 属性设置为 `SnapPointsType.MandatorySingle` ，这可确保滚动一次只滚动一项。

以下屏幕截图显示了一个 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 关闭对齐的：

[![IOS 和 Android 上不带对齐点的 CarouselView 的屏幕截图](scrolling-images/snappoints-none.png "不带对齐点的 CarouselView")](scrolling-images/snappoints-none-large.png#lightbox "不带对齐点的 CarouselView")

### <a name="snap-points-alignment"></a>对齐点对齐

[`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment)枚举定义 `Start` 、 `Center` 和 `End` 成员。

> [!IMPORTANT]
> [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment)仅当 [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType) 属性设置为或时，才考虑属性的值 `Mandatory` `MandatorySingle` 。

#### <a name="start"></a>开始

`SnapPointsAlignment.Start`成员指示对齐点与项的开头边缘对齐。 下面的 XAML 示例演示如何设置此枚举成员：

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              PeekAreaInsets="100">
    <CarouselView.ItemsLayout>
        <LinearItemsLayout Orientation="Horizontal"
                           SnapPointsType="MandatorySingle"
                           SnapPointsAlignment="Start" />
    </CarouselView.ItemsLayout>
    ...
</CarouselView>
```

等效 C# 代码如下：

```csharp
CarouselView carouselView = new CarouselView
{
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Horizontal)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.Start
    },
    // ...
};
```

当用户 swipes 在水平滚动中启动滚动时 [`CarouselView`](xref:Xamarin.Forms.CarouselView) ，左侧项目将与视图的左侧对齐：

[![IOS 和 Android 上的 CarouselView 与开始对齐点的屏幕截图](scrolling-images/snappoints-start.png "具有开始对齐点的 CarouselView")](scrolling-images/snappoints-start-large.png#lightbox "具有开始对齐点的 CarouselView")

#### <a name="center"></a>Center

`SnapPointsAlignment.Center`成员指示对齐点与项的中心对齐。

默认情况下 [`CarouselView`](xref:Xamarin.Forms.CarouselView) ，将 [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment) 属性设置为 `Center` 。 但是，为了完整起见，以下 XAML 示例显示了如何设置此枚举成员：

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              PeekAreaInsets="100">
    <CarouselView.ItemsLayout>
        <LinearItemsLayout Orientation="Horizontal"
                           SnapPointsType="MandatorySingle"
                           SnapPointsAlignment="Center" />
    </CarouselView.ItemsLayout>
    ...
</CarouselView>
```

等效 C# 代码如下：

```csharp
CarouselView carouselView = new CarouselView
{
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Horizontal)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.Center
    },
    // ...
};
```

当用户 swipes 在水平滚动中启动滚动时 [`CarouselView`](xref:Xamarin.Forms.CarouselView) ，中心项将与视图中心对齐：

[![IOS 和 Android 上包含中心对齐点的 CarouselView 的屏幕截图](scrolling-images/snappoints-center.png "具有中心对齐点的 CarouselView")](scrolling-images/snappoints-center-large.png#lightbox "具有中心对齐点的 CarouselView")

#### <a name="end"></a>结束

`SnapPointsAlignment.End`成员指示对齐点与项的尾随边缘对齐。 下面的 XAML 示例演示如何设置此枚举成员：

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              PeekAreaInsets="100">
    <CarouselView.ItemsLayout>
        <LinearItemsLayout Orientation="Horizontal"
                           SnapPointsType="MandatorySingle"
                           SnapPointsAlignment="End" />
    </CarouselView.ItemsLayout>
    ...
</CarouselView>
```

等效 C# 代码如下：

```csharp
CarouselView carouselView = new CarouselView
{
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Horizontal)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.End
    },
    // ...
};
```

当用户 swipes 在水平滚动中启动滚动时 [`CarouselView`](xref:Xamarin.Forms.CarouselView) ，右侧的项目将与视图的右侧对齐。

[![IOS 和 Android 上带有结束对齐点的 CarouselView 的屏幕截图](scrolling-images/snappoints-end.png "结束对齐点的 CarouselView")](scrolling-images/snappoints-end-large.png#lightbox "结束对齐点的 CarouselView")

## <a name="related-links"></a>相关链接

- [CarouselView (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)