---
title: 设置选取器的 ItemsSource 属性
description: "\"选取器\" 视图是用于从数据列表中选择文本项的控件。 本文介绍如何通过设置 System.windows.controls.itemscontrol.itemssource 属性来使用数据填充选取器，以及如何响应用户选择的项。"
ms.prod: xamarin
ms.assetid: 8ECF390C-9DB2-4441-B9A3-101AE7E5AEC5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 395536752fb0d581408d0b8f91ef623d926aeeb4
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91559540"
---
# <a name="setting-a-pickers-itemssource-property"></a>设置选取器的 ItemsSource 属性

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-monkeyapppicker)

_"选取器" 视图是用于从数据列表中选择文本项的控件。本文介绍如何通过设置 System.windows.controls.itemscontrol.itemssource 属性来使用数据填充选取器，以及如何响应用户选择的项。_

Xamarin.Forms 2.3.4 增强了 [`Picker`](xref:Xamarin.Forms.Picker) 视图的功能，方法是通过设置其 [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) 属性并从属性中检索所选的项来填充数据 [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) 。 此外，可以通过将属性设置为来更改选定项的文本颜色 [`TextColor`](xref:Xamarin.Forms.Picker.TextColor) [`Color`](xref:Xamarin.Forms.Color) 。

## <a name="populating-a-picker-with-data"></a>使用数据填充选取器

[`Picker`](xref:Xamarin.Forms.Picker)可以通过将数据的 [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) 属性设置为集合来填充 `IList` 。 集合中的每一项都必须为类型，或者是从派生的 `object` 。 通过从项的数组初始化属性，可在 XAML 中添加项 `ItemsSource` ：

```xaml
<Picker x:Name="picker"
        Title="Select a monkey"
        TitleColor="Red">
  <Picker.ItemsSource>
    <x:Array Type="{x:Type x:String}">
      <x:String>Baboon</x:String>
      <x:String>Capuchin Monkey</x:String>
      <x:String>Blue Monkey</x:String>
      <x:String>Squirrel Monkey</x:String>
      <x:String>Golden Lion Tamarin</x:String>
      <x:String>Howler Monkey</x:String>
      <x:String>Japanese Macaque</x:String>
    </x:Array>
  </Picker.ItemsSource>
</Picker>
```

> [!NOTE]
> 请注意，`x:Array` 元素需要用于指示数组中项目类型的 `Type` 属性。

下面显示了等效的 c # 代码：

```csharp
var monkeyList = new List<string>();
monkeyList.Add("Baboon");
monkeyList.Add("Capuchin Monkey");
monkeyList.Add("Blue Monkey");
monkeyList.Add("Squirrel Monkey");
monkeyList.Add("Golden Lion Tamarin");
monkeyList.Add("Howler Monkey");
monkeyList.Add("Japanese Macaque");

var picker = new Picker { Title = "Select a monkey", TitleColor = Color.Red };
picker.ItemsSource = monkeyList;
```

## <a name="responding-to-item-selection"></a>响应项目选择

[`Picker`](xref:Xamarin.Forms.Picker)支持一次选择一个项。 当用户选择某一项时，将 [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) 激发该事件， [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) 并将属性更新为一个整数，该整数表示列表中选定项的索引，并且该 [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) 属性将更新为 `object` 表示选定项的。 [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex)属性是一个从零开始的数字，指示用户选择的项。 如果未选择任何项（如果 [`Picker`](xref:Xamarin.Forms.Picker) 是首次创建和初始化项，则为 `SelectedIndex` -1）。

> [!NOTE]
> 可在 iOS 上使用特定于平台的对中的项选择行为 [`Picker`](xref:Xamarin.Forms.Picker) 进行自定义。 有关详细信息，请参阅 [控制选取器项选择](~/xamarin-forms/platform/ios/picker-selection.md)。

下面的代码示例演示如何 [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) [`Picker`](xref:Xamarin.Forms.Picker) 在 XAML 中从中检索属性值：

```xaml
<Label Text="{Binding Source={x:Reference picker}, Path=SelectedItem}" />
```

下面显示了等效的 c # 代码：

```csharp
var monkeyNameLabel = new Label();
monkeyNameLabel.SetBinding(Label.TextProperty, new Binding("SelectedItem", source: picker));
```

此外，事件处理程序还可以在 [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) 事件触发时执行：

```csharp
void OnPickerSelectedIndexChanged(object sender, EventArgs e)
{
  var picker = (Picker)sender;
  int selectedIndex = picker.SelectedIndex;

  if (selectedIndex != -1)
  {
    monkeyNameLabel.Text = (string)picker.ItemsSource[selectedIndex];
  }
}
```

此方法获取 [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) 属性值，并使用值从集合中检索所选的项 [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) 。 这在功能上等效于从属性中检索所选的项 [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) 。 请注意，集合中的每个项 `ItemsSource` 都为类型 `object` ，因此必须将转换为以 `string` 显示。

> [!NOTE]
> [`Picker`](xref:Xamarin.Forms.Picker)可以通过设置或属性初始化来显示特定项 [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) 。 但是，必须在初始化集合后设置这些属性 [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) 。

## <a name="populating-a-picker-with-data-using-data-binding"></a>使用数据绑定填充包含数据的选取器

[`Picker`](xref:Xamarin.Forms.Picker)通过使用数据绑定将其属性绑定到集合，还可以使用数据填充 [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) `IList` 。 在 XAML 中，这是通过 [`Binding`](xref:Xamarin.Forms.Xaml.BindingExtension) 标记扩展实现的：

```xaml
<Picker Title="Select a monkey"
        TitleColor="Red"
        ItemsSource="{Binding Monkeys}"
        ItemDisplayBinding="{Binding Name}" />
```

下面显示了等效的 c # 代码：

```csharp
var picker = new Picker { Title = "Select a monkey", TitleColor = Color.Red };
picker.SetBinding(Picker.ItemsSourceProperty, "Monkeys");
picker.ItemDisplayBinding = new Binding("Name");
```

[`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource)属性数据绑定到 `Monkeys` 连接的视图模型的属性，该属性将返回一个 `IList<Monkey>` 集合。 下面的代码示例演示 `Monkey` 类，该类包含四个属性：

```csharp
public class Monkey
{
  public string Name { get; set; }
  public string Location { get; set; }
  public string Details { get; set; }
  public string ImageUrl { get; set; }
}
```

绑定到对象列表时， [`Picker`](xref:Xamarin.Forms.Picker) 必须告知要从每个对象显示哪个属性。 这是通过将属性设置 [`ItemDisplayBinding`](xref:Xamarin.Forms.Picker.ItemDisplayBinding) 为每个对象中的必需属性来实现的。 在上面的代码示例中， `Picker` 设置为显示每个 `Monkey.Name` 属性值。

### <a name="responding-to-item-selection"></a>响应项目选择

数据绑定可用于在对象发生更改时将对象设置为 [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) 属性值：

```xaml
<Picker Title="Select a monkey"
        TitleColor="Red"
        ItemsSource="{Binding Monkeys}"
        ItemDisplayBinding="{Binding Name}"
        SelectedItem="{Binding SelectedMonkey}" />
<Label Text="{Binding SelectedMonkey.Name}" ... />
<Label Text="{Binding SelectedMonkey.Location}" ... />
<Image Source="{Binding SelectedMonkey.ImageUrl}" ... />
<Label Text="{Binding SelectedMonkey.Details}" ... />
```

下面显示了等效的 c # 代码：

```csharp
var picker = new Picker { Title = "Select a monkey", TitleColor = Color.Red };
picker.SetBinding(Picker.ItemsSourceProperty, "Monkeys");
picker.SetBinding(Picker.SelectedItemProperty, "SelectedMonkey");
picker.ItemDisplayBinding = new Binding("Name");

var nameLabel = new Label { ... };
nameLabel.SetBinding(Label.TextProperty, "SelectedMonkey.Name");

var locationLabel = new Label { ... };
locationLabel.SetBinding(Label.TextProperty, "SelectedMonkey.Location");

var image = new Image { ... };
image.SetBinding(Image.SourceProperty, "SelectedMonkey.ImageUrl");

var detailsLabel = new Label();
detailsLabel.SetBinding(Label.TextProperty, "SelectedMonkey.Details");
```

[`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem)属性数据绑定到类型为的 `SelectedMonkey` 已连接视图模型的属性 `Monkey` 。 因此，当用户选择中的项时 [`Picker`](xref:Xamarin.Forms.Picker) ， `SelectedMonkey` 属性将设置为所选 `Monkey` 对象。 `SelectedMonkey`对象数据按和视图显示在用户界面中 [`Label`](xref:Xamarin.Forms.Label) [`Image`](xref:Xamarin.Forms.Image) ：

![选取器项选择](populating-itemssource-images/monkeys.png)

> [!NOTE]
> 请注意， [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) 和 [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) 属性默认支持双向绑定。

## <a name="related-links"></a>相关链接

- [选取器演示 (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-pickerdemo)
- [猴子 App (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-monkeyapppicker)
- [可绑定选取器 (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-bindablepicker)
- [选取器 API](xref:Xamarin.Forms.Picker)