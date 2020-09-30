---
title: Xamarin.Forms 扩展器
description: Xamarin.Forms扩展器控件提供了一个可扩展容器来托管任何内容。 通过点击扩展器标头来显示或隐藏内容。
ms.prod: xamarin
ms.assetid: 381DCB55-522D-4414-B45B-E8DD70AA9985
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/15/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4be3744e0cce465aab8fc4af39495d2f7d8e9004
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556602"
---
# <a name="no-locxamarinforms-expander"></a>Xamarin.Forms 扩展器

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-expanderdemos/)

Xamarin.Forms `Expander` 控件提供可扩展容器以承载任何内容。 控件具有标题和内容，并通过点击标题显示或隐藏内容 `Expander` 。 仅 `Expander` 显示标头时， `Expander` 会 *折叠*。 如果 `Expander` 内容可见，则 `Expander` *展开*。

下面的屏幕截图显示 `Expander` 其折叠和展开状态，其中显示了指示标题和内容的红框：

![在 iOS 和 Android 上折叠和展开状态的扩展器的屏幕截图](expander-images/expander.png "IOS 和 Android 上的扩展器")

> [!IMPORTANT]
> `Expander` 当前为试验性，只能通过设置标志来使用 `Expander_Experimental` 。 有关详细信息，请参阅[实验性标志](~/xamarin-forms/internals/experimental-flags.md)。
>
> 此外， `Expander` 控件完全在 `Xamarin.Forms` 命名空间中实现。 因此，它在支持的所有平台上可用 Xamarin.Forms 。

`Expander`控件定义以下属性：

- `CollapseAnimationEasing`，类型为 [`Easing`](xref:Xamarin.Forms.Easing) ，它表示折叠时要应用于内容的缓动函数 `Expander` 。
- `CollapseAnimationLength`，类型为 `uint` ，用于定义折叠时动画的持续时间 `Expander` 。 此属性的默认值为250毫秒。
- `Command`，类型为 `ICommand` ，在 `Expander` 点击标头时执行。
- `CommandParameter`，属于 `object` 类型，是传递给 `Command` 的参数。
- `Content`，类型为 [`View`](xref:Xamarin.Forms.View) ，用于定义在展开时要显示的内容 `Expander` 。
- `ContentTemplate`，类型为 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ，它是用于动态地膨胀内容的模板 `Expander` 。
- `ExpandAnimationEasing`，类型为 [`Easing`](xref:Xamarin.Forms.Easing) ，表示要在扩展期间应用于内容的缓动函数 `Expander` 。
- `ExpandAnimationLength`，类型为 `uint` ，用于定义在展开时的动画持续时间 `Expander` 。 此属性的默认值为250毫秒。
- `ForceUpdateSizeCommand`，类型为 `ICommand` ，它定义强制更新的大小时执行的命令 `Expander` 。 此属性使用 `OneWayToSource` 绑定模式。
- `Header`，类型为 [`View`](xref:Xamarin.Forms.View) ，用于定义标头内容。
- `IsExpanded`，类型为 `bool` ，确定 `Expander` 是否已展开。 此属性使用 `TwoWay` 绑定模式，其默认值为 `false` 。
- `State`，类型为 `ExpanderState` ，表示的状态 `Expander` 。 此属性使用 `OneWayToSource` 绑定模式。

这些属性由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象提供支持；也就是说，它们可以作为数据绑定的目标，并能进行样式设置。

> [!NOTE]
> 由于 `Content` 属性是 `Expander` 类的内容属性，因此不需要通过 XAML 显式设置。

`ExpanderState` 枚举定义下列成员：

- `Expanding` 指示 `Expander` 正在进行扩展。
- `Expanded` 指示 `Expander` 已展开。
- `Collapsing` 指示 `Expander` 折叠。
- `Collapsed` 指示 `Expander` 已折叠。

该 `Expander` 控件还定义 `Tapped` 在点击标头时触发的事件 `Expander` 。 此外，还 `Expander` 包括一个 `ForceUpdateSize` 方法，可以调用该方法以编程方式 `Expander` 在运行时调整大小。

## <a name="create-an-expander"></a>创建扩展器

下面的示例演示如何 `Expander` 在 XAML 中实例化：

```xaml
<Expander>
    <Expander.Header>
        <Label Text="Baboon"
               FontAttributes="Bold"
               FontSize="Medium" />
    </Expander.Header>
    <Grid Padding="10">
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="Auto" />
        </Grid.ColumnDefinitions>
        <Image Source="http://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
               Aspect="AspectFill"
               HeightRequest="120"
               WidthRequest="120" />
        <Label Grid.Column="1"
               Text="Baboons are African and Arabian Old World monkeys belonging to the genus Papio, part of the subfamily Cercopithecinae."
               FontAttributes="Italic" />
    </Grid>
</Expander>
```

在此示例中， `Expander` 默认情况下折叠，并将 [`Label`](xref:Xamarin.Forms.Label) 显示为其标头。 点击标头会导致 `Expander` 展开以显示其内容，这是一个 [`Grid`](xref:Xamarin.Forms.Grid) 包含子控件的。 展开时 `Expander` ，点击其标头会折叠 `Expander` 。

> [!IMPORTANT]
> 当 `Expander.Content` 隐式或显式设置属性时，将在 `Expander` 包含它的页被导航到时创建内容，即使 `Expander` 已折叠。 但是， `Expander.ContentTemplate` 可以将属性设置为仅在 `Expander` 第一次展开时才会放大的内容。 有关详细信息，请参阅 [按需创建扩展器内容](#create-expander-content-on-demand)。

或者， `Expander` 可以在代码中创建：

```csharp
Expander expander = new Expander
{
    Header = new Label
    {
        Text = "Baboon",
        FontAttributes = FontAttributes.Bold,
        FontSize = Device.GetNamedSize(NamedSize.Medium, typeof(Label))
    }
};

Grid grid = new Grid
{
    Padding = new Thickness(10),
    ColumnDefinitions =
    {
        new ColumnDefinition { Width = GridLength.Auto },
        new ColumnDefinition { Width = GridLength.Auto }
    }
};

grid.Children.Add(new Image
{
    Source = "http://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg",
    Aspect = Aspect.AspectFill,
    HeightRequest = 120,
    WidthRequest = 120
});

grid.Children.Add(new Label
{
    Text = "Baboons are African and Arabian Old World monkeys belonging to the genus Papio, part of the subfamily Cercopithecinae.",
    FontAttributes = FontAttributes.Italic
}, 1, 0);

expander.Content = grid;
```

## <a name="create-expander-content-on-demand"></a>按需创建扩展器内容

`Expander` 可以按需创建内容，以响应 `Expander` 展开。 这可以通过将 `Expander.ContentTemplate` 属性设置为包含内容的来实现 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ：

```xaml
<Expander>
    <Expander.Header>
        <Label Text="{Binding Name}"
               FontAttributes="Bold"
               FontSize="Medium" />
    </Expander.Header>
    <Expander.ContentTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="Auto" />
                    <ColumnDefinition Width="Auto" />
                </Grid.ColumnDefinitions>
                <Image Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="120"
                       WidthRequest="120" />
                <Label Grid.Column="1"
                       Text="{Binding Details}"
                       FontAttributes="Italic" />
            </Grid>
        </DataTemplate>
    </Expander.ContentTemplate>
</Expander>
```

在此示例中， `Expander` 仅当首次展开时才会放大内容 `Expander` 。

此方法的优点是，当页面包含多个 `Expander` 对象时， `Expander` 仅当用户首次展开时才会创建的内容。

## <a name="add-an-expansion-indicator"></a>添加扩展指示器

[`Image`](xref:Xamarin.Forms.Image)可以向 `Expander` 标头中添加，以提供展开状态的可视指示。 [`DataTrigger`](xref:Xamarin.Forms.DataTrigger)可以附加到 `Image` ，这会 `Source` 基于属性的值更改属性 `Expander.IsExpanded` ：

```xaml
<Expander>
    <Expander.Header>
        <Grid>
            <Label Text="{Binding Name}"
                   FontAttributes="Bold"
                   FontSize="Medium" />
            <Image Source="expand.png"
                   HorizontalOptions="End"
                   VerticalOptions="Start">
                <Image.Triggers>
                    <DataTrigger TargetType="Image"
                                 Binding="{Binding Source={RelativeSource AncestorType={x:Type Expander}}, Path=IsExpanded}"
                                 Value="True">
                        <Setter Property="Source"
                                Value="collapse.png" />
                    </DataTrigger>
                </Image.Triggers>
            </Image>
        </Grid>
    </Expander.Header>
    <Expander.ContentTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="Auto" />
                    <ColumnDefinition Width="Auto" />
                </Grid.ColumnDefinitions>
                <Image Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="120"
                       WidthRequest="120" />
                <Label Grid.Column="1"
                       Text="{Binding Details}"
                       FontAttributes="Italic" />
            </Grid>
        </DataTemplate>
    </Expander.ContentTemplate>
</Expander>
```

在此示例中， [`Image`](xref:Xamarin.Forms.Image) `expand` 默认情况下显示图标：

![处于折叠状态、在 iOS 和 Android 上的扩展器图标的屏幕截图](expander-images/icon-expand.png "IOS 和 Android 上的 Expandd 图标")

`IsExpanded` `true` 当 `Expander` 点击标头时，属性将变为，这将导致 `collapse` 显示图标：

![处于展开状态、在 iOS 和 Android 上的扩展器图标的屏幕截图](expander-images/icon-collapse.png "IOS 和 Android 上的 Expandd 图标")

有关触发器的详细信息，请参阅[ Xamarin.Forms 触发器](~/xamarin-forms/app-fundamentals/triggers.md)。

## <a name="embed-an-expander-in-an-expander"></a>在扩展器中嵌入扩展器

的内容 `Expander` 可以设置为另一个 `Expander` 控件，以启用多个级别的扩展。 以下 XAML 显示了 `Expander` 其内容为另一个 `Expander` 对象的：

```xaml
<Expander>
    <Expander.Header>
        <Label Text="{Binding Name}"
               FontAttributes="Bold"
               FontSize="Medium" />
    </Expander.Header>
    <Expander Padding="10">
        <Expander.Header>
            <Label Text="{Binding Location}"
                   FontSize="Medium" />
        </Expander.Header>
            <Expander.ContentTemplate>
                <DataTemplate>
                    <Grid>
                        <Grid.ColumnDefinitions>
                            <ColumnDefinition Width="Auto" />
                            <ColumnDefinition Width="Auto" />
                        </Grid.ColumnDefinitions>
                        <Image Source="{Binding ImageUrl}"
                               Aspect="AspectFill"
                               HeightRequest="120"
                               WidthRequest="120" />
                        <Label Grid.Column="1"
                               Text="{Binding Details}"
                               FontAttributes="Italic" />
                    </Grid>
                </DataTemplate>
            </Expander.ContentTemplate>
    </Expander>
</Expander>
```

在此示例中，点击根 `Expander` 标头将显示子项的标头 `Expander` ：

![IOS 和 Android 上嵌入的扩展器的屏幕截图](expander-images/embedded-expander1.png "IOS 和 Android 上的嵌入式扩展器")

点击子 `Expander` 标头会导致其内容被夸大并显示：

![IOS 和 Android 上嵌入的扩展器的屏幕截图](expander-images/embedded-expander2.png "IOS 和 Android 上的嵌入式扩展器")

## <a name="define-the-expand-and-collapse-animation"></a>定义展开和折叠动画

`Expander`可以通过将 `ExpandAnimationEasing` 和 `CollapseAnimationEasing` 属性设置为中包含的任何缓动函数 Xamarin.Forms 或自定义缓动函数来定义展开或折叠时发生的动画。 默认情况下，展开和折叠动画发生在250毫秒上。 但是，可以通过将 `ExpandAnimationLength` 和 `CollapseAnimationLength` 属性设置为值来更改这些持续时间 `uint` 。

下面的 XAML 演示了一个示例，该示例定义了当 `Expander` 用户展开或折叠时发生的动画：

```xaml
<Expander ExpandAnimationEasing="{x:Static Easing.CubicIn}"
          ExpandAnimationLength="500"
          CollapseAnimationEasing="{x:Static Easing.CubicOut}"
          CollapseAnimationLength="500">
    <Expander.Header>
        <Label Text="{Binding Name}"
               FontAttributes="Bold"
               FontSize="Medium" />
    </Expander.Header>
    <Expander.ContentTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="Auto" />
                    <ColumnDefinition Width="Auto" />
                </Grid.ColumnDefinitions>
                <Image Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="120"
                       WidthRequest="120" />
                <Label Grid.Column="1"
                       Text="{Binding Details}"
                       FontAttributes="Italic" />
            </Grid>
        </DataTemplate>
    </Expander.ContentTemplate>
</Expander>
```

在此示例中， `CubicIn` 缓动函数慢慢加快了500毫秒上的展开动画， `CubicOut` 缓动函数通过500毫秒快速减速折叠动画。

有关缓动函数的详细信息，请参阅[ Xamarin.Forms 缓动函数](~/xamarin-forms/user-interface/animation/easing.md)。

## <a name="resize-an-expander-at-runtime"></a>在运行时调整扩展器的大小

`Expander`使用方法可在运行时以编程方式调整大小 `ForceUpdateSize` 。

在给定 `Expander` 的 `expander` 中，如果其内容包含 [`Label`](xref:Xamarin.Forms.Label) 附加到它的，则 `TapGestureRecognizer` 下面的代码示例演示如何调用 `ForceUpdateSize` 方法：

```csharp
void OnLabelTapped(object sender, EventArgs e)
{
    Label label = sender as Label;
    Expander expander = label.Parent.Parent.Parent as Expander;

    if (label.FontSize == Device.GetNamedSize(NamedSize.Default, label))
    {
        label.FontSize = Device.GetNamedSize(NamedSize.Large, label);
    }
    else
    {
        label.FontSize = Device.GetNamedSize(NamedSize.Default, label);
    }
    expander.ForceUpdateSize();
}
```

在此示例中， `FontSize` [`Label`](xref:Xamarin.Forms.Label) 当点击时，会发生更改 `Label` 。 由于字体的大小变化，因此必须 `Expander` 通过调用其方法来更新的大小 `ForceUpdateSize` 。

## <a name="disable-an-expander"></a>禁用扩展器

应用程序可以输入一个状态，在该状态下，展开不 `Expander` 是有效的操作。 在这种情况下， `Expander` 可以通过将其 `IsEnabled` 属性设置为 false 来禁用。 这会阻止用户展开或折叠 `Expander` 。

## <a name="related-links"></a>相关链接

- [扩展演示 (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-expanderdemos/)
- [Xamarin.Forms 缓动函数](~/xamarin-forms/user-interface/animation/easing.md)
- [Xamarin.Forms 触发器](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin.Forms 可绑定布局](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)