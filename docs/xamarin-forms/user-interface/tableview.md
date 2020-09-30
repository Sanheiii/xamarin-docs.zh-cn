---
title: Xamarin.Forms TableView
description: 本文介绍如何使用 Xamarin.Forms TableView 类在应用程序中呈现滚动菜单、设置和输入窗体。
ms.prod: xamarin
ms.assetid: D1619D19-A74F-40DF-8E53-B1B7DFF7A3FB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/25/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2a54b5e2523f4d1993cf0c86ba141135b1e9d74c
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91561739"
---
# <a name="no-locxamarinforms-tableview"></a>Xamarin.Forms TableView

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-tableview)

[`TableView`](xref:Xamarin.Forms.TableView) 是一个视图，用于显示数据的可滚动列表或可选择的不共享同一模板的行。 与 [ListView](~/xamarin-forms/user-interface/listview/index.md)不同，没有的 `TableView` 概念 `ItemsSource` ，因此必须手动将项添加为子级。

![TableView 示例](tableview-images/tableview-all-sml.png)

## <a name="use-cases"></a>用例

[`TableView`](xref:Xamarin.Forms.TableView) 在以下情况中非常有用：

- 显示设置的列表，
- 收集窗体中的数据，或
- 显示不同于行 (的数据，例如数字、百分比和图像) 。

[`TableView`](xref:Xamarin.Forms.TableView) 处理引人注目的部分中的行滚动和布局，这是上述方案的常见需求。 `TableView`控件使用每个平台的底层等效视图（如果有），从而为每个平台创建本机查找。

## <a name="structure"></a>结构

中的元素 [`TableView`](xref:Xamarin.Forms.TableView) 组织为多个节。 的根 `TableView` 为 [`TableRoot`](xref:Xamarin.Forms.TableRoot) ，它是一个或多个实例的父级 [`TableSection`](xref:Xamarin.Forms.TableSection) 。 每个都 [`TableSection`](xref:Xamarin.Forms.TableSection) 包含一个标题和一个或多个 [`ViewCell`](xref:Xamarin.Forms.ViewCell) 实例：

```xaml
<TableView Intent="Settings">
    <TableRoot>
        <TableSection Title="Ring">
            <SwitchCell Text="New Voice Mail" />
            <SwitchCell Text="New Mail" On="true" />
        </TableSection>
    </TableRoot>
</TableView>
```

等效 C# 代码如下：

```csharp
Content = new TableView
{
    Root = new TableRoot
    {
        new TableSection("Ring")
        {
          // TableSection constructor takes title as an optional parameter
          new SwitchCell { Text = "New Voice Mail" },
          new SwitchCell { Text = "New Mail", On = true }
        }
    },
    Intent = TableIntent.Settings
};
```

## <a name="appearance"></a>外观

[`TableView`](xref:Xamarin.Forms.TableView) 公开 [`Intent`](xref:Xamarin.Forms.TableView.Intent) 属性，该属性可以设置为任何 [`TableIntent`](xref:Xamarin.Forms.TableIntent) 枚举成员：

- `Data` –在显示数据项时使用。 请注意， [ListView](~/xamarin-forms/user-interface/listview/index.md) 对于滚动数据列表可能是更好的选择。
- `Form` –当 TableView 充当窗体时使用。
- `Menu` –用于在显示选择菜单时使用。
- `Settings` –在显示配置设置列表时使用。

[`TableIntent`](xref:Xamarin.Forms.TableIntent)你选择的值可能会影响 [`TableView`](xref:Xamarin.Forms.TableView) 每个平台上显示的方式。 即使没有明显的差异，最佳做法也是选择 `TableIntent` 最符合您要使用表的方式的。

此外， [`TableSection`](xref:Xamarin.Forms.TableSection) 通过将属性设置为，可以更改为每个显示的文本的颜色 `TextColor` [`Color`](xref:Xamarin.Forms.Color) 。

## <a name="built-in-cells"></a>内置单元

Xamarin.Forms 附带了用于收集和显示信息的内置单元。 虽然 [`ListView`](xref:Xamarin.Forms.ListView) 和 [`TableView`](xref:Xamarin.Forms.TableView) 可以使用所有相同的单元，但 [`SwitchCell`](xref:Xamarin.Forms.SwitchCell) 与 [`EntryCell`](xref:Xamarin.Forms.EntryCell) 方案最为相关 `TableView` 。

有关[TextCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#textcell)和[ImageCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#imagecell)的详细说明，请参阅[ListView 单元格外观](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)。

### <a name="switchcell"></a>SwitchCell

[`SwitchCell`](xref:Xamarin.Forms.SwitchCell)用于显示和捕获开启/关闭或状态的控件 `true` / `false` 。 它定义以下属性：

- `Text` –要在开关旁显示的文本。
- `On` –开关是显示为打开还是关闭。
- `OnColor` – [`Color`](xref:Xamarin.Forms.Color) 交换机处于开启位置时的。

所有这些属性都是可绑定属性。

[`SwitchCell`](xref:Xamarin.Forms.SwitchCell) 还公开 `OnChanged` 事件，使您可以对单元格状态的更改做出响应。

![SwitchCell 示例](tableview-images/switch-cell.png)

### <a name="entrycell"></a>EntryCell

[`EntryCell`](xref:Xamarin.Forms.EntryCell) 需要显示用户可编辑的文本数据时，会很有用。 它定义以下属性：

- `Keyboard` –编辑时要显示的键盘。 其中包括数值、电子邮件、电话号码等选项， [请参阅 API 文档](xref:Xamarin.Forms.Keyboard)。
- `Label` –要显示在文本输入字段左侧的标签文本。
- `LabelColor` –标签文本的颜色。
- `Placeholder` –当输入字段为 null 或为空时显示的文本。 此文本在文本输入开始时消失。
- `Text` – "输入" 字段中的文本。
- `HorizontalTextAlignment` –文本的水平对齐方式。 值为居中、左对齐或右对齐。 [请参阅 API 文档](xref:Xamarin.Forms.TextAlignment)。
- `VerticalTextAlignment` –文本的垂直对齐方式。 值为 `Start` 、 `Center` 或 `End` 。

[`EntryCell`](xref:Xamarin.Forms.EntryCell) 还公开 `Completed` 事件，当用户在编辑文本时在键盘上的 "完成" 按钮上命中时，将触发该事件。

![EntryCell 示例](tableview-images/entry-cell.png)

## <a name="custom-cells"></a>自定义单元格

当内置单元格不够时，可使用自定义单元以对应用有意义的方式呈现和捕获数据。 例如，你可能想要显示一个滑块，以允许用户选择图像的不透明度。

所有自定义单元必须派生自 [`ViewCell`](xref:Xamarin.Forms.ViewCell) 所有内置单元类型使用的相同基类。

下面是自定义单元格的示例：

![自定义单元格示例](tableview-images/custom-cell.png)

下面的示例演示了 [`TableView`](xref:Xamarin.Forms.TableView) 在上面的屏幕截图中创建的 XAML：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DemoTableView.TablePage"
             Title="TableView">
      <TableView Intent="Settings">
          <TableRoot>
              <TableSection Title="Getting Started">
                  <ViewCell>
                      <StackLayout Orientation="Horizontal">
                          <Image Source="bulb.png" />
                          <Label Text="left"
                                 TextColor="#f35e20" />
                          <Label Text="right"
                                 HorizontalOptions="EndAndExpand"
                                 TextColor="#503026" />
                      </StackLayout>
                  </ViewCell>
              </TableSection>
          </TableRoot>
      </TableView>
</ContentPage>
```

等效 C# 代码如下：

```csharp
var table = new TableView();
table.Intent = TableIntent.Settings;
var layout = new StackLayout() { Orientation = StackOrientation.Horizontal };
layout.Children.Add (new Image() { Source = "bulb.png"});
layout.Children.Add (new Label()
{
    Text = "left",
    TextColor = Color.FromHex("#f35e20"),
    VerticalOptions = LayoutOptions.Center
});
layout.Children.Add (new Label ()
{
    Text = "right",
    TextColor = Color.FromHex ("#503026"),
    VerticalOptions = LayoutOptions.Center,
    HorizontalOptions = LayoutOptions.EndAndExpand
});
table.Root = new TableRoot ()
{
    new TableSection("Getting Started")
    {
        new ViewCell() {View = layout}
    }
};
Content = table;
```

下的根元素 [`TableView`](xref:Xamarin.Forms.TableView) 是 [`TableRoot`](xref:Xamarin.Forms.TableRoot) ，并且 [`TableSection`](xref:Xamarin.Forms.TableSection) 紧靠在下 `TableRoot` 。 [`ViewCell`](xref:Xamarin.Forms.ViewCell)直接在下定义 `TableSection` ，而 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 用于管理自定义单元格的布局，尽管可以在此处使用任何布局。

> [!NOTE]
> 与不同 [`ListView`](xref:Xamarin.Forms.ListView) ，不 [`TableView`](xref:Xamarin.Forms.TableView) 要求在中定义自定义 (或任何) 的单元格 `ItemTemplate` 。

## <a name="row-height"></a>行高

[`TableView`](xref:Xamarin.Forms.TableView)类具有两个属性，可用于更改单元格的行高：

- [`RowHeight`](xref:Xamarin.Forms.TableView.RowHeight) –将每个行的高度设置为 `int` 。
- [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows) –如果设置为，则行具有不同的高度 `true` 。 请注意，在将此属性设置为时 `true` ，将自动计算和应用行高度 Xamarin.Forms 。

更改中单元格的内容高度时，将 [`TableView`](xref:Xamarin.Forms.TableView) 在 Android 上隐式更新行高，并通用 Windows 平台 (UWP) 。 但是，在 iOS 上，必须通过将 [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows) 属性设置为 `true` ，并通过调用方法来强制更新 [`Cell.ForceUpdateSize`](xref:Xamarin.Forms.Cell.ForceUpdateSize) 。

下面的 XAML 示例显示了一个 [`TableView`](xref:Xamarin.Forms.TableView) 包含的 [`ViewCell`](xref:Xamarin.Forms.ViewCell) ：

```xaml
<ContentPage ...>
    <TableView ...
               HasUnevenRows="true">
        <TableRoot>
            ...
            <TableSection ...>
                ...
                <ViewCell x:Name="_viewCell"
                          Tapped="OnViewCellTapped">
                    <Grid Margin="15,0">
                        <Grid.RowDefinitions>
                            <RowDefinition Height="Auto" />
                            <RowDefinition Height="Auto" />
                        </Grid.RowDefinitions>
                        <Label Text="Tap this cell." />
                        <Label x:Name="_target"
                               Grid.Row="1"
                               Text="The cell has changed size."
                               IsVisible="false" />
                    </Grid>
                </ViewCell>
            </TableSection>
        </TableRoot>
    </TableView>
</ContentPage>
```

点击时 [`ViewCell`](xref:Xamarin.Forms.ViewCell) ，将 `OnViewCellTapped` 执行事件处理程序：

```csharp
void OnViewCellTapped(object sender, EventArgs e)
{
    _target.IsVisible = !_target.IsVisible;
    _viewCell.ForceUpdateSize();
}
```

`OnViewCellTapped`事件处理程序在中显示或隐藏的第二个 [`Label`](xref:Xamarin.Forms.Label) [`ViewCell`](xref:Xamarin.Forms.ViewCell) ，并通过调用方法显式更新单元格的大小 [`Cell.ForceUpdateSize`](xref:Xamarin.Forms.Cell.ForceUpdateSize) 。

下面的屏幕截图显示了在点击之前的单元格：

![调整大小之前的 ViewCell](tableview-images/cell-beforeresize.png)

下面的屏幕截图显示了点击后的单元格：

![调整大小后的 ViewCell](tableview-images/cell-afterresize.png)

> [!IMPORTANT]
> 如果此功能过度使用，则可能会导致性能下降。

## <a name="related-links"></a>相关链接

- [TableView (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-tableview)