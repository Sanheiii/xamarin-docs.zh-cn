---
title: ListView 交互
description: 本文介绍如何 Xamarin.Forms 通过实现选择、上下文操作和请求刷新来向 ListView 添加交互性。
ms.prod: xamarin
ms.assetid: CD14EB90-B08C-4E8F-A314-DA0EEC76E647
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/25/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d665a80d6f1e0319fc9dc8696db379cf781ee833
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91560112"
---
# <a name="listview-interactivity"></a>ListView 交互性

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)

Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) 类支持用户与它所呈现的数据交互。

## <a name="selection-and-taps"></a>选择和点击

[`ListView`](xref:Xamarin.Forms.ListView)通过将属性设置为枚举的值控制选择模式 [`ListView.SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) [`ListViewSelectionMode`](xref:Xamarin.Forms.ListViewSelectionMode) ：

- [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single) 指示可以选择单个项，突出显示选定项。 这是默认值。
- [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None) 指示不能选择项。

当用户点击某项时，将触发两个事件：

- [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 选择新项时激发。
- [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) 点击项时激发。

点击同一项两次将引发两个 [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) 事件，但只引发单个 [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 事件。

> [!NOTE]
> [`ItemTappedEventArgs`](xref:Xamarin.Forms.ItemTappedEventArgs)类，它包含事件的事件参数 [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) ，具有 [`Group`](xref:Xamarin.Forms.ItemTappedEventArgs.Group) 和 [`Item`](xref:Xamarin.Forms.ItemTappedEventArgs.Item) 属性，并且 `ItemIndex` 其值表示点击项的中的索引 [`ListView`](xref:Xamarin.Forms.ListView) 。 同样， [`SelectedItemChangedEventArgs`](xref:Xamarin.Forms.SelectedItemChangedEventArgs) 包含事件的事件参数的类 [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 具有 [`SelectedItem`](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem) 属性和 `SelectedItemIndex` 属性，其值表示所选项的中的索引 `ListView` 。

当 [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) 属性设置为时 [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single) ，可以选择中的项 [`ListView`](xref:Xamarin.Forms.ListView) ， [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) 将触发和事件，并将 [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) 属性设置为选定项的值。

当 [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) 属性设置为时 [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None) ，不能选择中的项， [`ListView`](xref:Xamarin.Forms.ListView) 不会 [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 触发事件，并且 [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) 属性将保持不变 `null` 。 不过， [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) 事件仍会被触发，然后在点击过程中会短暂突出显示攻项。

如果已选择项并且 [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) 属性从更改 [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single) 为 [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None) ，则 [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) 属性将设置为， `null` 并且 [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 将使用项激发事件 `null` 。

以下屏幕截图显示了一个 [`ListView`](xref:Xamarin.Forms.ListView) 具有默认选择模式的：

![启用了选择的 ListView](interactivity-images/selection-default.png)

### <a name="disable-selection"></a>禁用选定内容

若要禁用选择，请 [`ListView`](xref:Xamarin.Forms.ListView) 将 [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) 属性设置为 [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None) ：

```xaml
<ListView ... SelectionMode="None" />
```

```csharp
var listView = new ListView { ... SelectionMode = ListViewSelectionMode.None };
```

## <a name="context-actions"></a>上下文操作

通常，用户需要对中的项执行操作 `ListView` 。 例如，请考虑邮件应用中的电子邮件列表。 在 iOS 上，可以通过轻扫来删除消息：

![包含上下文操作的 ListView](interactivity-images/context-default.png)

上下文操作可以在 c # 和 XAML 中实现。 下面你会找到这两种方案的具体指南，但首先我们来了解一下这两种情况的一些关键实现细节。

上下文操作是使用元素创建的 `MenuItem` 。 点击对象的事件 `MenuItems` 由 `MenuItem` 自身引发，而不是 `ListView` 。 这不同于对单元格的点击事件处理方式，其中 `ListView` 引发事件而不是单元。 由于 `ListView` 引发事件，因此会为其事件处理程序提供键信息，如选择或点击的项。

默认情况下， `MenuItem` 无法了解它属于哪个单元格。 `CommandParameter`属性在上可 `MenuItem` 用于存储对象，如位于的后面的对象 `MenuItem` `ViewCell` 。 `CommandParameter`可以在 XAML 和 c # 中设置属性。

### <a name="xaml"></a>XAML

`MenuItem` 可以在 XAML 集合中创建元素。 下面的 XAML 演示了一个自定义单元格，其中实现了两个上下文操作：

```xaml
<ListView x:Name="ContextDemoList">
  <ListView.ItemTemplate>
    <DataTemplate>
      <ViewCell>
         <ViewCell.ContextActions>
            <MenuItem Clicked="OnMore"
                      CommandParameter="{Binding .}"
                      Text="More" />
            <MenuItem Clicked="OnDelete"
                      CommandParameter="{Binding .}"
                      Text="Delete" IsDestructive="True" />
         </ViewCell.ContextActions>
         <StackLayout Padding="15,0">
              <Label Text="{Binding title}" />
         </StackLayout>
      </ViewCell>
    </DataTemplate>
  </ListView.ItemTemplate>
</ListView>
```

在代码隐藏文件中，确保 `Clicked` 实现方法：

```csharp
public void OnMore (object sender, EventArgs e)
{
    var mi = ((MenuItem)sender);
    DisplayAlert("More Context Action", mi.CommandParameter + " more context action", "OK");
}

public void OnDelete (object sender, EventArgs e)
{
    var mi = ((MenuItem)sender);
    DisplayAlert("Delete Context Action", mi.CommandParameter + " delete context action", "OK");
}
```

> [!NOTE]
> `NavigationPageRenderer`Android 具有可重写的 `UpdateMenuItemIcon` 方法，该方法可用于从自定义中加载图标 `Drawable` 。 此替代使你可以在 Android 上的实例上使用 SVG 图像作为图标 `MenuItem` 。

### <a name="code"></a>代码

上下文操作可以在任何子类 (中实现， `Cell` 只要它不用作组头) 通过创建 `MenuItem` 实例并将其添加到该 `ContextActions` 单元的集合中。 您可以为上下文操作配置以下属性：

- **文本** &ndash; 显示在菜单项中的字符串。
- **单击** &ndash; 单击项时的事件。
- **IsDestructive** &ndash; (可选) 如果为 true，则在 iOS 上以不同的方式呈现项。

可以将多个上下文操作添加到一个单元，但只有一个可以 `IsDestructive` 设置为 `true` 。 下面的代码演示如何将上下文操作添加到 `ViewCell` ：

```csharp
var moreAction = new MenuItem { Text = "More" };
moreAction.SetBinding (MenuItem.CommandParameterProperty, new Binding ("."));
moreAction.Clicked += async (sender, e) =>
{
    var mi = ((MenuItem)sender);
    Debug.WriteLine("More Context Action clicked: " + mi.CommandParameter);
};

var deleteAction = new MenuItem { Text = "Delete", IsDestructive = true }; // red background
deleteAction.SetBinding (MenuItem.CommandParameterProperty, new Binding ("."));
deleteAction.Clicked += async (sender, e) =>
{
    var mi = ((MenuItem)sender);
    Debug.WriteLine("Delete Context Action clicked: " + mi.CommandParameter);
};
// add to the ViewCell's ContextActions property
ContextActions.Add (moreAction);
ContextActions.Add (deleteAction);
```

## <a name="pull-to-refresh"></a>下拉刷新

用户希望向下下拉数据列表将刷新该列表。 [`ListView`](xref:Xamarin.Forms.ListView)控件支持这种开箱即用。 若要启用请求刷新功能，请将设置 [`IsPullToRefreshEnabled`](xref:Xamarin.Forms.ListView.IsPullToRefreshEnabled) 为 `true` ：

```xaml
<ListView ...
          IsPullToRefreshEnabled="true" />
```

等效 C# 代码如下：

```csharp
listView.IsPullToRefreshEnabled = true;
```

在刷新过程中会出现一个微调框，默认情况下为黑色。 但是，可以通过将属性设置为，在 iOS 和 Android 上更改微调按钮颜色 `RefreshControlColor` [`Color`](xref:Xamarin.Forms.Color) ：

```xaml
<ListView ...
          IsPullToRefreshEnabled="true"
          RefreshControlColor="Red" />
```

等效 C# 代码如下：

```csharp
listView.RefreshControlColor = Color.Red;
```

以下屏幕截图显示了在用户请求时的请求刷新：

![ListView 正在进行刷新](interactivity-images/refresh-start.png)

下面的屏幕截图显示了在用户释放请求之后的请求刷新，并在更新时显示微调框 [`ListView`](xref:Xamarin.Forms.ListView) ：

![ListView 请求刷新完成](interactivity-images/refresh-in-progress.png)

[`ListView`](xref:Xamarin.Forms.ListView) 触发 [`Refreshing`](xref:Xamarin.Forms.ListView.Refreshing) 事件以启动刷新，并将 [`IsRefreshing`](xref:Xamarin.Forms.ListView.IsRefreshing) 属性设置为 `true` 。 若要刷新的内容所需的任何代码 `ListView` ，则应由事件的事件处理程序 `Refreshing` 或执行的方法执行 [`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand) 。 刷新后 `ListView` ， `IsRefreshing` 应将属性设置为 `false` ，或 [`EndRefresh`](xref:Xamarin.Forms.ListView.EndRefresh) 调用方法以指示刷新已完成。

> [!NOTE]
> 定义时 [`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand) ， `CanExecute` 可以指定命令的方法以启用或禁用该命令。

## <a name="detect-scrolling"></a>检测滚动

[`ListView`](xref:Xamarin.Forms.ListView) 定义一个 `Scrolled` 事件，该事件将激发以指示发生滚动。 下面的 XAML 示例显示了一个 `ListView` ，它设置事件的事件处理程序 `Scrolled` ：

```xaml
<ListView Scrolled="OnListViewScrolled">
    ...
</ListView>
```

等效 C# 代码如下：

```csharp
ListView listView = new ListView();
listView.Scrolled += OnListViewScrolled;
```

在此代码示例中，事件 `OnListViewScrolled` 处理程序在 `Scrolled` 事件激发时执行：

```csharp
void OnListViewScrolled(object sender, ScrolledEventArgs e)
{
    Debug.WriteLine("ScrollX: " + e.ScrollX);
    Debug.WriteLine("ScrollY: " + e.ScrollY);  
}
```

`OnListViewScrolled`事件处理程序输出 `ScrolledEventArgs` 事件随附的对象的值。

## <a name="related-links"></a>相关链接

- [ListView 交互 (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)