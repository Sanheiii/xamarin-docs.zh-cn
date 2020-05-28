---
title: Xamarin.FormsRefreshView
description: Xamarin.FormsRefreshView 是一个容器控件，为可滚动的内容提供拉取到刷新功能。
ms.prod: ''
ms.assetId: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d84e6bb6ed41f2fbc213cd15051d071521f588cd
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2020
ms.locfileid: "84127590"
---
# <a name="xamarinforms-refreshview"></a>Xamarin.FormsRefreshView

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-refreshviewdemo/)

`RefreshView`是一个容器控件，它为可滚动的内容提供了拉取到刷新功能。 因此，的子级 `RefreshView` 必须是可滚动的控件，如 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 、 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 或 [`ListView`](xref:Xamarin.Forms.ListView) 。

`RefreshView` 定义以下属性:

- `Command`，类型为 `ICommand` ，在触发刷新时执行。
- `CommandParameter`，属于 `object` 类型，是传递给 `Command` 的参数。
- `IsRefreshing`，类型为 `bool` ，指示的当前状态 `RefreshView` 。
- `RefreshColor`，类型为 `Color` ，在刷新过程中显示的进度圆圈的颜色。

这些属性是由对象支持的 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，这意味着它们可以是数据绑定的目标和样式。

> [!NOTE]
> 在通用 Windows 平台上，的拉取方向 `RefreshView` 可以设置为特定于平台的。 有关详细信息，请参阅[RefreshView 拉取方向](~/xamarin-forms/platform/windows/refreshview-pulldirection.md)。

## <a name="create-a-refreshview"></a>创建 RefreshView

下面的示例演示如何 `RefreshView` 在 XAML 中实例化：

```xaml
<RefreshView IsRefreshing="{Binding IsRefreshing}"
             Command="{Binding RefreshCommand}">
    <ScrollView>
        <FlexLayout Direction="Row"
                    Wrap="Wrap"
                    AlignItems="Center"
                    AlignContent="Center"
                    BindableLayout.ItemsSource="{Binding Items}"
                    BindableLayout.ItemTemplate="{StaticResource ColorItemTemplate}" />
    </ScrollView>
</RefreshView>
```

`RefreshView`也可以在代码中创建：

```csharp
RefreshView refreshView = new RefreshView();
ICommand refreshCommand = new Command(() =>
{
    // IsRefreshing is true
    // Refresh data here
    refreshView.IsRefreshing = false;
});
refreshView.Command = refreshCommand;

ScrollView scrollView = new ScrollView();
FlexLayout flexLayout = new FlexLayout { ... };
scrollView.Content = flexLayout;
refreshView.Content = scrollView;
```

在此示例中，将 `RefreshView` 向刷新功能提供对 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 子级为的的请求 [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) 。 `FlexLayout`通过绑定到项的集合，使用可绑定布局生成其内容，并使用设置每个项的外观 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 。 有关可绑定布局的详细信息，请参阅[中 Xamarin.Forms 的可绑定布局](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)。

属性的值 `RefreshView.IsRefreshing` 指示的当前状态 `RefreshView` 。 当用户触发刷新时，此属性将自动转换为 `true` 。 刷新完成后，应将属性重置为 `false` 。

当用户启动刷新时，将 `ICommand` 执行由属性定义的 `Command` ，这会刷新正在显示的项。 刷新发生时，会显示刷新可视化效果，其中包含动画进度圆：

[![IOS 和 Android 上 RefreshView 刷新数据的屏幕截图](refreshview-images/default-progress-circle.png "RefreshView 刷新数据")](refreshview-images/default-progress-circle-large.png#lightbox "RefreshView 刷新数据")

> [!NOTE]
> 手动将 `IsRefreshing` 属性设置为 `true` 将触发刷新可视化，并将执行 `ICommand` 属性定义的 `Command` 。

## <a name="refreshview-appearance"></a>RefreshView 外观

除了 `RefreshView` 从类继承的属性之外 [`VisualElement`](xref:Xamarin.Forms.VisualElement) ， `RefreshView` 还定义 `RefreshColor` 属性。 此属性可设置为定义在刷新过程中出现的进度圆圈的颜色：

```xaml
<RefreshView RefreshColor="Teal"
             ... />
```

以下屏幕截图显示了 `RefreshView` 具有 `RefreshColor` 属性集的：

[![IOS 和 Android 上带有蓝绿色进度圆圈的 RefreshView 屏幕截图](refreshview-images/teal-progress-circle.png "带有蓝绿色进度圆的 RefreshView")](refreshview-images/teal-progress-circle-large.png#lightbox "带有蓝绿色进度圆的 RefreshView")

此外，还 `BackgroundColor` 可以将属性设置为 [`Color`](xref:Xamarin.Forms.Color) 表示进度圆背景色的。

> [!NOTE]
> 在 iOS 上， `BackgroundColor` 属性设置包含进度圆圈的的背景色 `UIView` 。

## <a name="disable-a-refreshview"></a>禁用 RefreshView

应用程序可以输入状态，请求刷新的状态不是有效的操作。 在这种情况下， `RefreshView` 可以通过将其 `IsEnabled` 属性设置为来禁用 `false` 。 这会阻止用户触发请求刷新。

或者，在定义 `Command` 属性时， `CanExecute` 可以指定的委托 `ICommand` 来启用或禁用该命令。

## <a name="related-links"></a>相关链接

- [RefreshView （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-refreshviewdemo/)
- [可绑定布局Xamarin.Forms](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)
- [RefreshView 拉取方向平台特定](~/xamarin-forms/platform/windows/refreshview-pulldirection.md)
