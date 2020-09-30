---
title: Xamarin.Forms 格
description: Xamarin.Forms网格是一种布局，可将其子级组织到单元格的行和列中。
ms.prod: xamarin
ms.assetid: 762B1802-D185-494C-B643-74EED55882FE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/15/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 59a4d5c0730719d6cc332728c6cab641d02239b4
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563557"
---
# <a name="no-locxamarinforms-grid"></a>Xamarin.Forms 格

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-griddemos)

[![：： no (Xamarin) ：：： Grid](grid-images/layouts.png "：： no (Xamarin) ：：： Grid")](grid-images/layouts-large.png#lightbox "：： no (Xamarin) ：：： Grid")

[`Grid`](xref:Xamarin.Forms.Grid)是一个布局，它将其子级组织为行和列，这些行和列的大小可以是比例或绝对值。 默认情况下， `Grid` 包含一行和一列。 此外，还 `Grid` 可用作包含其他子布局的父布局。

[`Grid`](xref:Xamarin.Forms.Grid)不应将该布局与表混淆，不应显示表格数据。 与 HTML 表不同，用于 `Grid` 布局内容。 若要显示表格数据，请考虑使用 [ListView](~/xamarin-forms/user-interface/listview/index.md)、 [CollectionView](~/xamarin-forms/user-interface/collectionview/index.md)或 [TableView](~/xamarin-forms/user-interface/tableview.md)。

[`Grid`](xref:Xamarin.Forms.Grid)类定义以下属性：

- [`Column`](xref:Xamarin.Forms.Grid.ColumnProperty)类型为的， `int` 它是一个附加属性，用于指示父视图中的列对齐方式 `Grid` 。 此属性的默认值为 0。 验证回调确保当设置属性时，其值大于或等于0。
- [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions)类型为的 [`ColumnDefinitionCollection`](xref:Xamarin.Forms.ColumnDefinitionCollection) 对象的列表， [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) 这些对象定义网格列的宽度。
- [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing)类型为的， `double` 指示网格列之间的距离。 此属性的默认值为6个与设备无关的单位。
- [`ColumnSpan`](xref:Xamarin.Forms.Grid.ColumnSpanProperty)，类型 `int` 为，它是一个附加属性，用于指示视图在父级中跨越的总列数 `Grid` 。 此属性的默认值为 1。 验证回调确保当设置属性时，其值大于或等于1。
- [`Row`](xref:Xamarin.Forms.Grid.RowProperty)类型为的， `int` 它是一个附加属性，用于指示父视图中的行对齐方式 `Grid` 。 此属性的默认值为 0。 验证回调确保当设置属性时，其值大于或等于0。
- [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions)类型为的 [`RowDefinitionCollection`](xref:Xamarin.Forms.RowDefinitionCollection) 对象的列表， [`RowDefintion`](xref:Xamarin.Forms.RowDefinition) 这些对象定义网格行的高度。
- [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing)类型为的， `double` 指示网格行之间的距离。 此属性的默认值为6个与设备无关的单位。
- [`RowSpan`](xref:Xamarin.Forms.Grid.RowSpanProperty)类型为的， `int` 它是一个附加属性，指示视图在父级中跨越的总行数 `Grid` 。 此属性的默认值为 1。 验证回调确保当设置属性时，其值大于或等于1。

这些属性是由对象支持的 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，这意味着属性可以是数据绑定的目标和样式化的。

[`Grid`](xref:Xamarin.Forms.Grid)类派生自 `Layout<T>` 类，该类定义 `Children` 类型的属性 `IList<T>` 。 `Children`属性是类的 `ContentProperty` `Layout<T>` ，因此无需从 XAML 显式设置。

> [!TIP]
> 若要获得最佳布局性能，请遵循 [优化布局性能](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance)中的准则。

## <a name="rows-and-columns"></a>行和列

默认情况下， [`Grid`](xref:Xamarin.Forms.Grid) 包含一行和一列：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="GridTutorial.MainPage">
    <Grid Margin="20,35,20,20">
        <Label Text="By default, a Grid contains one row and one column." />
    </Grid>
</ContentPage>
```

在此示例中， [`Grid`](xref:Xamarin.Forms.Grid) 包含 [`Label`](xref:Xamarin.Forms.Label) 自动定位于单个位置的单个子级：

[![默认网格布局的屏幕截图](grid-images/default.png "默认网格布局")](grid-images/default-large.png#lightbox "默认网格布局")

的布局行为 [`Grid`](xref:Xamarin.Forms.Grid) 可以使用 [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions) 和属性进行定义 [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) ，这些属性 [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) 分别是和对象的集合 [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) 。 这些集合定义的行和列特征 `Grid` ，应 [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) 为中的每个行包含一个对象 `Grid` ，并 [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) 为中的每个列包含一个对象 `Grid` 。

[`RowDefinition`](xref:Xamarin.Forms.RowDefinition)类定义类型为 [`Height`](xref:Xamarin.Forms.RowDefinition.Height) 的属性， [`GridLength`](xref:Xamarin.Forms.GridLength) [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) 类定义 [`Width`](xref:Xamarin.Forms.ColumnDefinition.Width) 类型的属性 [`GridLength`](xref:Xamarin.Forms.GridLength) 。 [`GridLength`](xref:Xamarin.Forms.GridLength)结构在枚举中指定行高或列宽 [`GridUnitType`](xref:Xamarin.Forms.GridUnitType) ，其中包含三个成员：

- `Absolute` –行高或列宽是以与设备无关的单位表示的值， (XAML) 中的数字。
- `Auto` –行高或列宽基于 (`Auto` 在 XAML) 中的单元格内容进行自动调整。
- `Star` –剩余的行高度或列宽度按比例分配 (一个数字后跟 `*` XAML) 。

[`Grid`](xref:Xamarin.Forms.Grid)具有的 `Height` 属性的行以与 `Auto` 垂直相同的方式约束该行中视图的高度 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。 同样，具有 `Width` 属性的列 `Auto` 非常类似于水平 `StackLayout` 。

> [!CAUTION]
> 尝试确保尽可能少的行和列设置为 [`Auto`](xref:Xamarin.Forms.GridLength.Auto) 大小。 每个自动调整大小的行或列都会导致布局引擎执行额外布局计算。 而是应在可能时使用固定大小的行和列。 另外，还可以设置行和列，使其与枚举值占用比例的空间 [`GridUnitType.Star`](xref:Xamarin.Forms.GridUnitType.Star) 。

下面的 XAML 演示如何创建 [`Grid`](xref:Xamarin.Forms.Grid) 包含三行和两列的：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="GridDemos.Views.BasicGridPage"
             Title="Basic Grid demo">
   <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="2*" />
            <RowDefinition Height="*" />
            <RowDefinition Height="100" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>
        ...
    </Grid>
</ContentPage>
```

在此示例中，的 [`Grid`](xref:Xamarin.Forms.Grid) 总体高度是页面的高度。 `Grid`了解第三行的高度为100与设备无关的单位。 它从它自己的高度中减去该高度，并基于星形前面的数字在第一个行和第二行之间按比例分配剩余高度。 在此示例中，第一行的高度是第二行的两倍。

这两个 [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) 对象都将设置 [`Width`](xref:Xamarin.Forms.ColumnDefinition.Width) 为，这与 `*` 相同 `1*` ，这意味着屏幕的宽度在两列的下方等距。

> [!IMPORTANT]
> 属性的默认值 [`RowDefinition.Height`](xref:Xamarin.Forms.RowDefinition.Height) 为 `*` 。 同样，属性的默认值 [`ColumnDefinition.Width`](xref:Xamarin.Forms.ColumnDefinition.Width) 为 `*` 。 因此，在这些默认值都是可接受的情况下，不需要设置这些属性。

子视图可以在 [`Grid`](xref:Xamarin.Forms.Grid) 具有 [`Grid.Column`](xref:Xamarin.Forms.Grid.ColumnProperty) 和附加属性的特定单元中定位 [`Grid.Row`](xref:Xamarin.Forms.Grid.RowProperty) 。 此外，若要使子视图跨多个行和列，请使用 [`Grid.RowSpan`](xref:Xamarin.Forms.Grid.RowSpanProperty) 和 [`Grid.ColumnSpan`](xref:Xamarin.Forms.Grid.ColumnSpanProperty) 附加属性。

下面的 XAML 演示了相同的 [`Grid`](xref:Xamarin.Forms.Grid) 定义，并且还在特定单元中定位子视图 `Grid` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="GridDemos.Views.BasicGridPage"
             Title="Basic Grid demo">
   <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="2*" />
            <RowDefinition />
            <RowDefinition Height="100" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <BoxView Color="Green" />
        <Label Text="Row 0, Column 0"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
        <BoxView Grid.Column="1"
                 Color="Blue" />
        <Label Grid.Column="1"
               Text="Row 0, Column 1"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
        <BoxView Grid.Row="1"
                 Color="Teal" />
        <Label Grid.Row="1"
               Text="Row 1, Column 0"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
        <BoxView Grid.Row="1"
                 Grid.Column="1"
                 Color="Purple" />
        <Label Grid.Row="1"
               Grid.Column="1"
               Text="Row1, Column 1"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
        <BoxView Grid.Row="2"
                 Grid.ColumnSpan="2"
                 Color="Red" />
        <Label Grid.Row="2"
               Grid.ColumnSpan="2"
               Text="Row 2, Columns 0 and 1"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
    </Grid>
</ContentPage>
```

> [!NOTE]
> `Grid.Row`和 `Grid.Column` 属性都是从0开始编制索引的，因此引用第 `Grid.Row="2"` 三行，而 `Grid.Column="1"` 引用第二列。 此外，这两个属性的默认值均为0，因此不需要在占据第一行或第一列的子视图上设置 [`Grid`](xref:Xamarin.Forms.Grid) 。

在此示例中，所有三 [`Grid`](xref:Xamarin.Forms.Grid) 行都由 [`BoxView`](xref:Xamarin.Forms.BoxView) 和 [`Label`](xref:Xamarin.Forms.Label) 视图占用。 第三行是100与设备无关的单位，前两行占用了剩余空间， (第一行与第二行) 高两倍。 这两个列的宽度相等，并将除以 `Grid` 一半。 `BoxView`第三行中的两个列。

[![基本网格布局的屏幕截图](grid-images/basic.png "基本网格布局")](grid-images/basic-large.png#lightbox "基本网格布局")

此外，中的子视图 [`Grid`](xref:Xamarin.Forms.Grid) 可以共享单元。 子元素在 XAML 中的显示顺序是将子级置于中的顺序 `Grid` 。 在上面的示例中， [`Label`](xref:Xamarin.Forms.Label) 对象仅在对象的顶部呈现时可见 [`BoxView`](xref:Xamarin.Forms.BoxView) 。 如果对象是在对象上呈现的，则这些 `Label` 对象将不可见 `BoxView` 。

等效 C# 代码如下：

```csharp
public class BasicGridPageCS : ContentPage
{
    public BasicGridPageCS()
    {
        Grid grid = new Grid
        {
            RowDefinitions =
            {
                new RowDefinition { Height = new GridLength(2, GridUnitType.Star) },
                new RowDefinition(),
                new RowDefinition { Height = new GridLength(100) }
            },
            ColumnDefinitions =
            {
                new ColumnDefinition(),
                new ColumnDefinition()
            }
        };

        // Row 0
        // The BoxView and Label are in row 0 and column 0, and so only needs to be added to the
        // Grid.Children collection to get default row and column settings.
        grid.Children.Add(new BoxView
        {
            Color = Color.Green
        });
        grid.Children.Add(new Label
        {
            Text = "Row 0, Column 0",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.Center
        });

        // This BoxView and Label are in row 0 and column 1, which are specified as arguments
        // to the Add method.
        grid.Children.Add(new BoxView
        {
            Color = Color.Blue
        }, 1, 0);
        grid.Children.Add(new Label
        {
            Text = "Row 0, Column 1",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.Center
        }, 1, 0);

        // Row 1
        // This BoxView and Label are in row 1 and column 0, which are specified as arguments
        // to the Add method overload.
        grid.Children.Add(new BoxView
        {
            Color = Color.Teal
        }, 0, 1, 1, 2);
        grid.Children.Add(new Label
        {
            Text = "Row 1, Column 0",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.Center
        }, 0, 1, 1, 2); // These arguments indicate that that the child element goes in the column starting at 0 but ending before 1.
                        // They also indicate that the child element goes in the row starting at 1 but ending before 2.

        grid.Children.Add(new BoxView
        {
            Color = Color.Purple
        }, 1, 2, 1, 2);
        grid.Children.Add(new Label
        {
            Text = "Row1, Column 1",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.Center
        }, 1, 2, 1, 2);

        // Row 2
        // Alternatively, the BoxView and Label can be positioned in cells with the Grid.SetRow
        // and Grid.SetColumn methods.
        BoxView boxView = new BoxView { Color = Color.Red };
        Grid.SetRow(boxView, 2);
        Grid.SetColumnSpan(boxView, 2);
        Label label = new Label
        {
            Text = "Row 2, Column 0 and 1",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.Center
        };
        Grid.SetRow(label, 2);
        Grid.SetColumnSpan(label, 2);

        grid.Children.Add(boxView);
        grid.Children.Add(label);

        Title = "Basic Grid demo";
        Content = grid;
    }
}
```

在代码中，若要指定对象的高度 [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) 以及对象的宽度，请 [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) 使用结构的值 [`GridLength`](xref:Xamarin.Forms.GridLength) ，通常与枚举结合使用 [`GridUnitType`](xref:Xamarin.Forms.GridUnitType) 。

上面的示例代码还显示了几种不同的方法，可将子级添加到 [`Grid`](xref:Xamarin.Forms.Grid) 中，并指定它们所在的单元格。 当使用 `Add` 指定*left*、 *right*、 *top*和 top 参数的重载*bottom*时，尽管*left*和*top*参数将始终引用中的单元 `Grid` ，但*右侧*和*底部*参数看起来是指引用外部的单元格 `Grid` 。 这是因为 *右* 参数必须始终大于 *左侧* 参数，而 *底部* 参数必须始终大于 *top* 参数。 下面的示例假定 2x2 `Grid` ，同时使用这两个重载显示等效的代码 `Add` ：

```csharp
// left, top
grid.Children.Add(topLeft, 0, 0);           // first column, first row
grid.Children.Add(topRight, 1, 0);          // second column, first tow
grid.Children.Add(bottomLeft, 0, 1);        // first column, second row
grid.Children.Add(bottomRight, 1, 1);       // second column, second row

// left, right, top, bottom
grid.Children.Add(topLeft, 0, 1, 0, 1);     // first column, first row
grid.Children.Add(topRight, 1, 2, 0, 1);    // second column, first tow
grid.Children.Add(bottomLeft, 0, 1, 1, 2);  // first column, second row
grid.Children.Add(bottomRight, 1, 2, 1, 2); // second column, second row
```

> [!NOTE]
> 此外，可以使用和方法将子视图添加到， [`Grid`](xref:Xamarin.Forms.Grid) [`AddHorizontal`](xref:Xamarin.Forms.Grid.IGridList`1.AddHorizontal*) 这会 [`AddVertical`](xref:Xamarin.Forms.Grid.IGridList`1.AddVertical*) 将子级添加到单个行或单个列 `Grid` 。 `Grid`然后，在进行这些调用时，将在行或列中展开，并自动定位正确单元中的子级。

### <a name="simplify-row-and-column-definitions"></a>简化行和列定义

在 XAML 中，可以使用简化的语法来指定的行和列特征，以 [`Grid`](xref:Xamarin.Forms.Grid) 避免为 [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) 每个行和列定义和对象。 相反， [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions) 和 [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) 属性可以设置为包含逗号分隔值的字符串 [`GridUnitType`](xref:Xamarin.Forms.GridUnitType) ，从该值开始 Xamarin.Forms 创建 `RowDefinition` 和对象中生成的类型转换器 `ColumnDefinition` ：

```xaml
<Grid RowDefinitions="1*, Auto, 25, 14, 20"
      ColumnDefinitions="*, 2*, Auto, 300">
    ...
</Grid>
```

在此示例中， [`Grid`](xref:Xamarin.Forms.Grid) 有五行和四列。 第三个、第三行和第五行设置为绝对高度，第二行自动调整其内容的大小。 然后，将剩余的高度分配给第一行。

将第三列设置为绝对宽度，第三列自动调整其内容的大小。 在第一列和第二列之间按星号前面的数字分配剩余宽度。 在此示例中，第二列的宽度是 (第一列的两倍，因为与 `*` `1*`) 相同。

## <a name="space-between-rows-and-columns"></a>行和列之间的间距

默认情况下， [`Grid`](xref:Xamarin.Forms.Grid) 行将由6个与设备无关的空间单元隔开。 同样， `Grid` 由6个与设备无关的空间单元分隔列。 可以通过设置 [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing) 和属性分别更改这些默认值 [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing) ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="GridDemos.Views.GridSpacingPage"
             Title="Grid spacing demo">
    <Grid RowSpacing="0"
          ColumnSpacing="0">
        ..
    </Grid>
</ContentPage>
```

此示例创建一个 [`Grid`](xref:Xamarin.Forms.Grid) ，它的行和列之间没有间距：

[![单元格之间无间距的网格屏幕截图](grid-images/spacing.png "单元格之间没有间距的网格")](grid-images/spacing-large.png#lightbox "单元格之间没有间距的网格")

> [!TIP]
> [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing)和 [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing) 属性可以设置为负值，以使单元格内容重叠。

等效 C# 代码如下：

```csharp
public GridSpacingPageCS()
{
    Grid grid = new Grid
    {
        RowSpacing = 0,
        ColumnSpacing = 0,
        // ...
    };
    // ...

    Content = grid;
}
```

## <a name="alignment"></a>对齐方式

中的子视图 [`Grid`](xref:Xamarin.Forms.Grid) 可以通过和属性定位在其单元 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 格中 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 。 这些属性可以设置为结构中的以下字段 [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) ：

- [`Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)

> [!IMPORTANT]
> `AndExpands`结构中的字段 [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) 仅适用于 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 对象。

下面的 XAML 创建一个 [`Grid`](xref:Xamarin.Forms.Grid) 具有九个大小相等的单元格，并 [`Label`](xref:Xamarin.Forms.Label) 在每个单元格中以不同的对齐方式放置：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="GridDemos.Views.GridAlignmentPage"
             Title="Grid alignment demo">
    <Grid RowSpacing="0"
          ColumnSpacing="0">
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>

        <BoxView Color="AliceBlue" />
        <Label Text="Upper left"
               HorizontalOptions="Start"
               VerticalOptions="Start" />
        <BoxView Grid.Column="1"
                 Color="LightSkyBlue" />
        <Label Grid.Column="1"
               Text="Upper center"
               HorizontalOptions="Center"
               VerticalOptions="Start"/>
        <BoxView Grid.Column="2"
                 Color="CadetBlue" />
        <Label Grid.Column="2"
               Text="Upper right"
               HorizontalOptions="End"
               VerticalOptions="Start" />
        <BoxView Grid.Row="1"
                 Color="CornflowerBlue" />
        <Label Grid.Row="1"
               Text="Center left"
               HorizontalOptions="Start"
               VerticalOptions="Center" />
        <BoxView Grid.Row="1"
                 Grid.Column="1"
                 Color="DodgerBlue" />
        <Label Grid.Row="1"
               Grid.Column="1"
               Text="Center center"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
        <BoxView Grid.Row="1"
                 Grid.Column="2"
                 Color="DarkSlateBlue" />
        <Label Grid.Row="1"
               Grid.Column="2"
               Text="Center right"
               HorizontalOptions="End"
               VerticalOptions="Center" />
        <BoxView Grid.Row="2"
                 Color="SteelBlue" />
        <Label Grid.Row="2"
               Text="Lower left"
               HorizontalOptions="Start"
               VerticalOptions="End" />
        <BoxView Grid.Row="2"
                 Grid.Column="1"
                 Color="LightBlue" />
        <Label Grid.Row="2"
               Grid.Column="1"
               Text="Lower center"
               HorizontalOptions="Center"
               VerticalOptions="End" />
        <BoxView Grid.Row="2"
                 Grid.Column="2"
                 Color="BlueViolet" />
        <Label Grid.Row="2"
               Grid.Column="2"
               Text="Lower right"
               HorizontalOptions="End"
               VerticalOptions="End" />
    </Grid>
</ContentPage>
```

在此示例中， [`Label`](xref:Xamarin.Forms.Label) 每行中的对象都垂直对齐，但使用不同的水平对齐方式。 另外，可以将这视为 `Label` 每个列中的对象在水平方向上是相同的，但使用的是不同的垂直对齐方式：

[![网格内的单元格对齐的屏幕截图](grid-images/alignment.png "网格中的单元格对齐方式")](grid-images/alignment-large.png#lightbox "网格中的单元格对齐方式")

等效 C# 代码如下：

```csharp
public class GridAlignmentPageCS : ContentPage
{
    public GridAlignmentPageCS()
    {
        Grid grid = new Grid
        {
            RowSpacing = 0,
            ColumnSpacing = 0,
            RowDefinitions =
            {
                new RowDefinition(),
                new RowDefinition(),
                new RowDefinition()
            },
            ColumnDefinitions =
            {
                new ColumnDefinition(),
                new ColumnDefinition(),
                new ColumnDefinition()
            }
        };

        // Row 0
        grid.Children.Add(new BoxView
        {
            Color = Color.AliceBlue
        });
        grid.Children.Add(new Label
        {
            Text = "Upper left",
            HorizontalOptions = LayoutOptions.Start,
            VerticalOptions = LayoutOptions.Start
        });

        grid.Children.Add(new BoxView
        {
            Color = Color.LightSkyBlue
        }, 1, 0);
        grid.Children.Add(new Label
        {
            Text = "Upper center",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.Start
        }, 1, 0);

        grid.Children.Add(new BoxView
        {
            Color = Color.CadetBlue
        }, 2, 0);
        grid.Children.Add(new Label
        {
            Text = "Upper right",
            HorizontalOptions = LayoutOptions.End,
            VerticalOptions = LayoutOptions.Start
        }, 2, 0);

        // Row 1
        grid.Children.Add(new BoxView
        {
            Color = Color.CornflowerBlue
        }, 0, 1);
        grid.Children.Add(new Label
        {
            Text = "Center left",
            HorizontalOptions = LayoutOptions.Start,
            VerticalOptions = LayoutOptions.Center
        }, 0, 1);

        grid.Children.Add(new BoxView
        {
            Color = Color.DodgerBlue
        }, 1, 1);
        grid.Children.Add(new Label
        {
            Text = "Center center",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.Center
        }, 1, 1);

        grid.Children.Add(new BoxView
        {
            Color = Color.DarkSlateBlue
        }, 2, 1);
        grid.Children.Add(new Label
        {
            Text = "Center right",
            HorizontalOptions = LayoutOptions.End,
            VerticalOptions = LayoutOptions.Center
        }, 2, 1);

        // Row 2
        grid.Children.Add(new BoxView
        {
            Color = Color.SteelBlue
        }, 0, 2);
        grid.Children.Add(new Label
        {
            Text = "Lower left",
            HorizontalOptions = LayoutOptions.Start,
            VerticalOptions = LayoutOptions.End
        }, 0, 2);

        grid.Children.Add(new BoxView
        {
            Color = Color.LightBlue
        }, 1, 2);
        grid.Children.Add(new Label
        {
            Text = "Lower center",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.End
        }, 1, 2);

        grid.Children.Add(new BoxView
        {
            Color = Color.BlueViolet
        }, 2, 2);
        grid.Children.Add(new Label
        {
            Text = "Lower right",
            HorizontalOptions = LayoutOptions.End,
            VerticalOptions = LayoutOptions.End
        }, 2, 2);

        Title = "Grid alignment demo";
        Content = grid;
    }
}
```

## <a name="nested-grid-objects"></a>嵌套网格对象

[`Grid`](xref:Xamarin.Forms.Grid)可用作包含嵌套子 `Grid` 对象或其他子布局的父布局。 嵌套对象时， `Grid` `Grid.Row` 、、 `Grid.Column` `Grid.RowSpan` 和 `Grid.ColumnSpan` 附加属性始终指的是视图在其父级中的位置 `Grid` 。

以下 XAML 显示了嵌套对象的示例 [`Grid`](xref:Xamarin.Forms.Grid) ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:converters="clr-namespace:GridDemos.Converters"
             x:Class="GridDemos.Views.ColorSlidersGridPage"
             Title="Nested Grids demo">

    <ContentPage.Resources>
        <converters:DoubleToIntConverter x:Key="doubleToInt" />

        <Style TargetType="Label">
            <Setter Property="HorizontalTextAlignment"
                    Value="Center" />
        </Style>
    </ContentPage.Resources>

    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <BoxView x:Name="boxView"
                 Color="Black" />
        <Grid Grid.Row="1"
              Margin="20">
            <Grid.RowDefinitions>
                <RowDefinition />
                <RowDefinition />
                <RowDefinition />
                <RowDefinition />
                <RowDefinition />
                <RowDefinition />
            </Grid.RowDefinitions>
            <Slider x:Name="redSlider"
                    ValueChanged="OnSliderValueChanged" />
            <Label Grid.Row="1"
                   Text="{Binding Source={x:Reference redSlider},
                                  Path=Value,
                                  Converter={StaticResource doubleToInt},
                                  ConverterParameter=255,
                                  StringFormat='Red = {0}'}" />
            <Slider x:Name="greenSlider"
                    Grid.Row="2"
                    ValueChanged="OnSliderValueChanged" />
            <Label Grid.Row="3"
                   Text="{Binding Source={x:Reference greenSlider},
                                  Path=Value,
                                  Converter={StaticResource doubleToInt},
                                  ConverterParameter=255,
                                  StringFormat='Green = {0}'}" />
            <Slider x:Name="blueSlider"
                    Grid.Row="4"
                    ValueChanged="OnSliderValueChanged" />
            <Label Grid.Row="5"
                   Text="{Binding Source={x:Reference blueSlider},
                                  Path=Value,
                                  Converter={StaticResource doubleToInt},
                                  ConverterParameter=255,
                                  StringFormat='Blue = {0}'}" />
        </Grid>
    </Grid>
</ContentPage>
```

在此示例中，根 [`Grid`](xref:Xamarin.Forms.Grid) 布局 [`BoxView`](xref:Xamarin.Forms.BoxView) 在其第一行包含，在 `Grid` 其第二行中包含一个子级。 子级 `Grid` 包含 [`Slider`](xref:Xamarin.Forms.Slider) 操作显示的颜色的对象 `BoxView` ，以及 [`Label`](xref:Xamarin.Forms.Label) 显示每个对象的值的对象 `Slider` ：

[![嵌套网格的屏幕截图](grid-images/nesting.png "嵌套网格对象")](grid-images/nesting-large.png#lightbox "嵌套网格对象")

> [!IMPORTANT]
> 嵌套 [`Grid`](xref:Xamarin.Forms.Grid) 对象和其他布局的深度越多，嵌套布局就会影响性能。 有关详细信息，请参阅 [选择正确的布局](~/xamarin-forms/deploy-test/performance.md#choose-the-correct-layout)。

等效 C# 代码如下：

```csharp
public class ColorSlidersGridPageCS : ContentPage
{
    BoxView boxView;
    Slider redSlider;
    Slider greenSlider;
    Slider blueSlider;

    public ColorSlidersGridPageCS()
    {
        // Create an implicit style for the Labels
        Style labelStyle = new Style(typeof(Label))
        {
            Setters =
            {
                new Setter { Property = Label.HorizontalTextAlignmentProperty, Value = TextAlignment.Center }
            }
        };
        Resources.Add(labelStyle);

        // Root page layout
        Grid rootGrid = new Grid
        {
            RowDefinitions =
            {
                new RowDefinition(),
                new RowDefinition()
            }
        };

        boxView = new BoxView { Color = Color.Black };
        rootGrid.Children.Add(boxView);

        // Child page layout
        Grid childGrid = new Grid
        {
            Margin = new Thickness(20),
            RowDefinitions =
            {
                new RowDefinition(),
                new RowDefinition(),
                new RowDefinition(),
                new RowDefinition(),
                new RowDefinition(),
                new RowDefinition()
            }
        };

        DoubleToIntConverter doubleToInt = new DoubleToIntConverter();

        redSlider = new Slider();
        redSlider.ValueChanged += OnSliderValueChanged;
        childGrid.Children.Add(redSlider);

        Label redLabel = new Label();
        redLabel.SetBinding(Label.TextProperty, new Binding("Value", converter: doubleToInt, converterParameter: "255", stringFormat: "Red = {0}", source: redSlider));
        Grid.SetRow(redLabel, 1);
        childGrid.Children.Add(redLabel);

        greenSlider = new Slider();
        greenSlider.ValueChanged += OnSliderValueChanged;
        Grid.SetRow(greenSlider, 2);
        childGrid.Children.Add(greenSlider);

        Label greenLabel = new Label();
        greenLabel.SetBinding(Label.TextProperty, new Binding("Value", converter: doubleToInt, converterParameter: "255", stringFormat: "Green = {0}", source: greenSlider));
        Grid.SetRow(greenLabel, 3);
        childGrid.Children.Add(greenLabel);

        blueSlider = new Slider();
        blueSlider.ValueChanged += OnSliderValueChanged;
        Grid.SetRow(blueSlider, 4);
        childGrid.Children.Add(blueSlider);

        Label blueLabel = new Label();
        blueLabel.SetBinding(Label.TextProperty, new Binding("Value", converter: doubleToInt, converterParameter: "255", stringFormat: "Blue = {0}", source: blueSlider));
        Grid.SetRow(blueLabel, 5);
        childGrid.Children.Add(blueLabel);

        // Place the child Grid in the root Grid
        rootGrid.Children.Add(childGrid, 0, 1);

        Title = "Nested Grids demo";
        Content = rootGrid;
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs e)
    {
        boxView.Color = new Color(redSlider.Value, greenSlider.Value, blueSlider.Value);
    }
}
```

## <a name="related-links"></a>相关链接

- [网格演示 (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-griddemos)
- [布局选项 Xamarin.Forms](layout-options.md)
- [选择 Xamarin.Forms 布局](choose-layout.md)
- [提高 Xamarin.Forms 应用程序性能](~/xamarin-forms/deploy-test/performance.md)