---
title: 将数据添加到选取器的项集合
description: "\"选取器\" 视图是用于从数据列表中选择文本项的控件。 本文介绍如何通过将数据添加到 Items 集合来使用数据填充选取器，以及如何响应用户选择的项目。"
ms.prod: xamarin
ms.assetid: 3C840F64-A430-457D-A4B2-3D7AF46F9DBE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d65352022057ce32bd969950c2165ad530c05bbb
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91559618"
---
# <a name="adding-data-to-a-pickers-items-collection"></a>将数据添加到选取器的项集合

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-pickerdemo)

_"选取器" 视图是用于从数据列表中选择文本项的控件。本文介绍如何通过将数据添加到 Items 集合来使用数据填充选取器，以及如何响应用户选择的项目。_

## <a name="populating-a-picker-with-data"></a>使用数据填充选取器

Xamarin.Forms在2.3.4 之前，使用数据填充的过程 [`Picker`](xref:Xamarin.Forms.Picker) 是添加要显示在只读 [`Items`](xref:Xamarin.Forms.Picker.Items) 集合中的数据，它的类型为 `IList<string>` 。 集合中的每一项都必须为类型 `string` 。 通过使用项列表初始化属性，可在 XAML 中添加项 `Items` `x:String` ：

```xaml
<Picker Title="Select a monkey"
        TitleColor="Red">
  <Picker.Items>
    <x:String>Baboon</x:String>
    <x:String>Capuchin Monkey</x:String>
    <x:String>Blue Monkey</x:String>
    <x:String>Squirrel Monkey</x:String>
    <x:String>Golden Lion Tamarin</x:String>
    <x:String>Howler Monkey</x:String>
    <x:String>Japanese Macaque</x:String>
  </Picker.Items>
</Picker>
```

下面显示了等效的 c # 代码：

```csharp
var picker = new Picker { Title = "Select a monkey", TitleColor = Color.Red };
picker.Items.Add("Baboon");
picker.Items.Add("Capuchin Monkey");
picker.Items.Add("Blue Monkey");
picker.Items.Add("Squirrel Monkey");
picker.Items.Add("Golden Lion Tamarin");
picker.Items.Add("Howler Monkey");
picker.Items.Add("Japanese Macaque");
```

除了使用方法添加数据外 `Items.Add` ，还可以使用方法将数据插入集合中 `Items.Insert` 。

## <a name="responding-to-item-selection"></a>响应项目选择

[`Picker`](xref:Xamarin.Forms.Picker)支持一次选择一个项。 当用户选择某项时，将 [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) 激发该事件，并将 [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) 属性更新为一个整数，该整数表示列表中选定项的索引。 `SelectedIndex`属性是一个从零开始的数字，指示用户选择的项。 如果未选择任何项（如果 `Picker` 是首次创建和初始化项，则为 `SelectedIndex` -1）。

> [!NOTE]
> 可在 iOS 上使用特定于平台的对中的项选择行为 [`Picker`](xref:Xamarin.Forms.Picker) 进行自定义。 有关详细信息，请参阅 [控制选取器项选择](~/xamarin-forms/platform/ios/picker-selection.md)。

下面的代码示例演示 `OnPickerSelectedIndexChanged` 事件处理程序方法，该方法在 [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) 事件激发时执行：

```csharp
void OnPickerSelectedIndexChanged(object sender, EventArgs e)
{
  var picker = (Picker)sender;
  int selectedIndex = picker.SelectedIndex;

  if (selectedIndex != -1)
  {
    monkeyNameLabel.Text = picker.Items[selectedIndex];
  }
}
```

此方法获取 [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) 属性值，并使用值从集合中检索所选的项 [`Items`](xref:Xamarin.Forms.Picker.Items) 。 由于集合中的每一项 `Items` 都是 `string` ，因此可以通过显示， [`Label`](xref:Xamarin.Forms.Label) 而无需强制转换。

> [!NOTE]
> [`Picker`](xref:Xamarin.Forms.Picker)可以通过设置属性来初始化以显示特定项 [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) 。 但是， `SelectedIndex` 必须在初始化集合后设置属性 [`Items`](xref:Xamarin.Forms.Picker.Items) 。

## <a name="related-links"></a>相关链接

- [选取器演示 (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-pickerdemo)
- [选取器](xref:Xamarin.Forms.Picker)