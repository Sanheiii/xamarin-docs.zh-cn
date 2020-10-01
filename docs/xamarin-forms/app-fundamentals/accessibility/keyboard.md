---
title: 键盘辅助功能
description: 与使用默认的选项卡序列不同，有时需要通过结合 TabIndex 和 IsTabStop 属性指定选项卡序列来优化 UI 的辅助功能。
ms.prod: xamarin
ms.assetid: 8be8f498-558a-4894-a01f-91a0d3ef927e
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/09/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: db0591f89447a2ae083f5ac73f6e002b4d0ee6f8
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91561347"
---
# <a name="keyboard-accessibility-in-no-locxamarinforms"></a>Xamarin.Forms 中的键盘辅助功能

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-accessibility)

如果用户使用屏幕阅读器或有行动障碍，他们在使用不提供相应键盘访问的应用程序时可能会遇到困难。 Xamarin.Forms 应用程序可以根据需要指定 Tab 键顺序，以改进可用性和辅助功能。 为控件指定 Tab 键顺序可启用键盘导航，准备应用程序页以特定顺序接收输入，并允许屏幕阅读器为用户读取可聚焦的元素。

默认情况下，控件的 Tab 键顺序与在 XAML 中列出或以编程方式添加到子集合中的顺序相同。 此顺序是使用键盘导航控件或使用屏幕阅读器读取控件的顺序，通常这种默认顺序是最佳顺序。 但是，默认顺序并不总是与预期顺序相同，如以下 XAML 代码示例所示：

```xaml
<Grid>
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="0.5*" />
        <ColumnDefinition Width="0.5*" />
    </Grid.ColumnDefinitions>
    <Grid.RowDefinitions>
        <RowDefinition Height="Auto" />
        <RowDefinition Height="Auto" />
        <RowDefinition Height="Auto" />
    </Grid.RowDefinitions>
    <Label Text="You"
           HorizontalOptions="Center" />
    <Label Grid.Column="1"
           Text="Manager"
           HorizontalOptions="Center" />
    <Entry Grid.Row="1"
           Placeholder="Enter forename" />
    <Entry Grid.Column="1"
           Grid.Row="1"
           Placeholder="Enter forename" />
    <Entry Grid.Row="2"
           Placeholder="Enter surname" />
    <Entry Grid.Column="1"
           Grid.Row="2"
           Placeholder="Enter surname" />
</Grid>
```

以下屏幕截图显示了此代码示例的默认 Tab 键顺序：

![默认基于行的 Tab 键顺序](keyboard-images/default-tab-order.png)

此处的 Tab 键顺序是基于行的，是在 XAML 中列出控件的顺序。 因此，按 Tab 键可以导航到名 [`Entry`](xref:Xamarin.Forms.Entry) 实例，然后是姓氏 `Entry` 实例。 但是，更直观的体验是使用列优先的 Tab 导航，这样按下 Tab 键就可以通过名字对导航。 这可以通过指定输入控件的 Tab 键顺序来实现。

> [!NOTE]
> 在通用 Windows 平台上，可以定义键盘快捷方式，为用户提供一种直观的方式，以便通过键盘快速导航并与应用程序的可见 UI 交互，而不是通过触摸或鼠标。 有关更多信息，请参阅[设置 VisualElement 访问键](~/xamarin-forms/platform/windows/visualelement-access-keys.md)。

## <a name="setting-the-tab-order"></a>设置 Tab 键顺序

当用户通过按 Tab 键导航控件时，`VisualElement.TabIndex` 属性用于指示 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 实例接收焦点的顺序。 属性的默认值是 0，可以将其设置为任何 `int` 值。

使用默认 Tab 键顺序或设置 `TabIndex` 属性时，以下规则适用：

- [`VisualElement`](xref:Xamarin.Forms.VisualElement) 实例（`TabIndex` 等于 0）将根据 XAML 或子集合中的声明顺序添加到 Tab 键顺序中。
- [`VisualElement`](xref:Xamarin.Forms.VisualElement) 实例（`TabIndex` 大于 0）将根据其 `TabIndex` 值添加到 Tab 键顺序中。
- [`VisualElement`](xref:Xamarin.Forms.VisualElement) 实例（`TabIndex` 小于 0）将添加到 Tab 键顺序中，并显示在任何零值之前。
- `TabIndex` 中的冲突可通过声明顺序解决。

定义 Tab 键顺序后，按 Tab 键焦点将按升序 `TabIndex` 顺序循环通过控件，并在到达最后一个控件后绕到开头。

下面的 XAML 示例显示了输入控件上设置的 `TabIndex` 属性，可用于启用列优先 Tab 导航：

```xaml
<Grid>
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="0.5*" />
        <ColumnDefinition Width="0.5*" />
    </Grid.ColumnDefinitions>
    <Grid.RowDefinitions>
        <RowDefinition Height="Auto" />
        <RowDefinition Height="Auto" />
        <RowDefinition Height="Auto" />
    </Grid.RowDefinitions>
    <Label Text="You"
           HorizontalOptions="Center" />
    <Label Grid.Column="1"
           Text="Manager"
           HorizontalOptions="Center" />
    <Entry Grid.Row="1"
           Placeholder="Enter forename"
           TabIndex="1" />
    <Entry Grid.Column="1"
           Grid.Row="1"
           Placeholder="Enter forename"
           TabIndex="3" />
    <Entry Grid.Row="2"
           Placeholder="Enter surname"
           TabIndex="2" />
    <Entry Grid.Column="1"
           Grid.Row="2"
           Placeholder="Enter surname"
           TabIndex="4" />
</Grid>
```

以下屏幕截图显示了此代码示例的 Tab 键顺序：

![于列的 Tab 键顺序](keyboard-images/correct-tab-order.png)

此处的 Tab 键顺序是基于列的。 因此，按 Tab 键可以通过姓-名 [`Entry`](xref:Xamarin.Forms.Entry) 对导航。

> [!IMPORTANT]
> 在读取屏幕上的可访问元素时，iOS 和 Android 上的屏幕阅读器将遵从 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 的 `TabIndex`。

## <a name="excluding-controls-from-the-tab-order"></a>从 Tab 键顺序中排除控件

除了设置控件的 Tab 键顺序之外，可能还需要从 Tab 键顺序中排除控件。 实现此目的的一种方法是将控件的 [`IsEnabled`](xref:Xamarin.Forms.VisualElement) 属性设置为 `false`，因为禁用的控件将从 Tab 键顺序中排除。

但是，即便在未禁用这些控件时，也可能需要从 Tab 键顺序中排除它们。 这可以通过 `VisualElement.IsTabStop` 属性实现，该属性指示 Tab 导航中是否包含 [`VisualElement`](xref:Xamarin.Forms.VisualElement)。 其默认值是 `true`，当它的值是 `false` 时，Tab 导航基础设施将忽略该控件，而不管是否设置了 `TabIndex`。

## <a name="supported-controls"></a>支持的控件

以下控件支持 `TabIndex` 和 `IsTabStop` 属性，这些控件在一个或多个平台上接受键盘输入：

- [`Button`](xref:Xamarin.Forms.Button)
- [`DatePicker`](xref:Xamarin.Forms.DatePicker)
- [`Editor`](xref:Xamarin.Forms.Editor)
- [`Entry`](xref:Xamarin.Forms.Entry)
- [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)
- [`Picker`](xref:Xamarin.Forms.Picker)
- [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)
- [`SearchBar`](xref:Xamarin.Forms.SearchBar)
- [`Slider`](xref:Xamarin.Forms.Slider)
- [`Stepper`](xref:Xamarin.Forms.Stepper)
- [`Switch`](xref:Xamarin.Forms.Switch)
- [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)
- [`TimePicker`](xref:Xamarin.Forms.TimePicker)

> [!NOTE]
> 这些控件并不是在每个平台上都可通过 Tab 键聚焦。

## <a name="related-links"></a>相关链接

- [可访问性（示例）](/samples/xamarin/xamarin-forms-samples/userinterface-accessibility)