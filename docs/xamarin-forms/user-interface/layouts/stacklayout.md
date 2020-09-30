---
title: Xamarin.Forms StackLayout
description: StackLayout 在一维堆栈中的水平或垂直方向上组织子视图。
ms.prod: xamarin
ms.assetid: 6A91EA70-268C-462C-AAAF-F8DA011403F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/11/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0c8e7c0e3edb581e13944ce48e2241f035791574
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556849"
---
# <a name="no-locxamarinforms-stacklayout"></a>Xamarin.Forms StackLayout

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stacklayoutdemos)

[![：： no (Xamarin。 Forms) ：：： StackLayout](stacklayout-images/layouts.png "：： no (Xamarin。 Forms) ：：： StackLayout")](stacklayout-images/layouts-large.png#lightbox "：： no (Xamarin。 Forms) ：：： StackLayout")

在 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 一维堆栈中，在水平或垂直方向上组织子视图。 默认情况下， `StackLayout` 垂直方向。 此外，还 `StackLayout` 可用作包含其他子布局的父布局。

[`StackLayout`](xref:Xamarin.Forms.StackLayout)类定义以下属性：

- [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation)类型为的， [`StackOrientation`](xref:Xamarin.Forms.StackOrientation) 表示子视图的定位方向。 此属性的默认值为 `Vertical`。
- [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing)类型为的， `double` 表示每个子视图之间的空间量。 此属性的默认值为六个与设备无关的单位。

这些属性是由对象支持的 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，这意味着属性可以是数据绑定的目标和样式化的。

[`StackLayout`](xref:Xamarin.Forms.StackLayout)类派生自 `Layout<T>` 类，该类定义 `Children` 类型的属性 `IList<T>` 。 `Children`属性是类的 `ContentProperty` `Layout<T>` ，因此无需从 XAML 显式设置。

> [!TIP]
> 若要获得最佳布局性能，请遵循 [优化布局性能](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance)中的准则。

## <a name="vertical-orientation"></a>垂直方向

下面的 XAML 演示如何创建 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 包含不同子视图的垂直方向：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StackLayoutDemos.Views.VerticalStackLayoutPage"
             Title="Vertical StackLayout demo">
    <StackLayout Margin="20">
        <Label Text="Primary colors" />
        <BoxView Color="Red" />
        <BoxView Color="Yellow" />
        <BoxView Color="Blue" />
        <Label Text="Secondary colors" />
        <BoxView Color="Green" />
        <BoxView Color="Orange" />
        <BoxView Color="Purple" />
    </StackLayout>
</ContentPage>
```

此示例将创建一个 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 包含 [`Label`](xref:Xamarin.Forms.Label) 和 [`BoxView`](xref:Xamarin.Forms.BoxView) 对象的垂直。 默认情况下，子视图之间有六个与设备无关的空间单元：

[![垂直方向 StackLayout 的屏幕截图](stacklayout-images/vertical.png "垂直方向的 StackLayout")](stacklayout-images/vertical-large.png#lightbox "垂直方向的 StackLayout")

等效 C# 代码如下：

```csharp
public class VerticalStackLayoutPageCS : ContentPage
{
    public VerticalStackLayoutPageCS()
    {
        Title = "Vertical StackLayout demo";
        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children =
            {
                new Label { Text = "Primary colors" },
                new BoxView { Color = Color.Red },
                new BoxView { Color = Color.Yellow },
                new BoxView { Color = Color.Blue },
                new Label { Text = "Secondary colors" },
                new BoxView { Color = Color.Green },
                new BoxView { Color = Color.Orange },
                new BoxView { Color = Color.Purple }
            }
        };
    }
}
```

> [!NOTE]
> 属性的值 [`Margin`](xref:Xamarin.Forms.View.Margin) 表示元素与其相邻元素之间的距离。 有关详细信息，请参阅[边距和填充](margin-and-padding.md)。

## <a name="horizontal-orientation"></a>水平方向

下面的 XAML 演示如何 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 通过将其属性设置为，来创建水平方向 [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) `Horizontal` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StackLayoutDemos.Views.HorizontalStackLayoutPage"
             Title="Horizontal StackLayout demo">
    <StackLayout Margin="20"
                 Orientation="Horizontal"
                 HorizontalOptions="Center">
        <BoxView Color="Red" />
        <BoxView Color="Yellow" />
        <BoxView Color="Blue" />
        <BoxView Color="Green" />
        <BoxView Color="Orange" />
        <BoxView Color="Purple" />
    </StackLayout>
</ContentPage>
```

此示例将创建一个水平 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 包含 [`BoxView`](xref:Xamarin.Forms.BoxView) 对象，其中包含六个与设备无关的子视图间距单元：

[![水平方向 StackLayout 的屏幕截图](stacklayout-images/horizontal.png "横向定向 StackLayout")](stacklayout-images/horizontal-large.png#lightbox "横向定向 StackLayout")

等效 C# 代码如下：

```csharp
public HorizontalStackLayoutPageCS()
{
    Title = "Horizontal StackLayout demo";
    Content = new StackLayout
    {
        Margin = new Thickness(20),
        Orientation = StackOrientation.Horizontal,
        HorizontalOptions = LayoutOptions.Center,
        Children =
        {
            new BoxView { Color = Color.Red },
            new BoxView { Color = Color.Yellow },
            new BoxView { Color = Color.Blue },
            new BoxView { Color = Color.Green },
            new BoxView { Color = Color.Orange },
            new BoxView { Color = Color.Purple }
        }
    };
}
```

## <a name="space-between-child-views"></a>子视图之间的间距

[`StackLayout`](xref:Xamarin.Forms.StackLayout)可以通过将属性设置为值，更改中的子视图之间的间距 [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) `double` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StackLayoutDemos.Views.StackLayoutSpacingPage"
             Title="StackLayout Spacing demo">
    <StackLayout Margin="20"
                 Spacing="0">
        <Label Text="Primary colors" />
        <BoxView Color="Red" />
        <BoxView Color="Yellow" />
        <BoxView Color="Blue" />
        <Label Text="Secondary colors" />
        <BoxView Color="Green" />
        <BoxView Color="Orange" />
        <BoxView Color="Purple" />
    </StackLayout>
</ContentPage>
```

此示例将创建一个垂直 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 包含 [`Label`](xref:Xamarin.Forms.Label) 和 [`BoxView`](xref:Xamarin.Forms.BoxView) 对象之间没有间距的对象：

[![无间距的 StackLayout 的屏幕截图](stacklayout-images/spacing.png "无间距的 StackLayout")](stacklayout-images/spacing-large.png#lightbox "无间距的 StackLayout")

> [!TIP]
> [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing)属性可以设置为负值以使子视图重叠。

等效 C# 代码如下：

```csharp
public class StackLayoutSpacingPageCS : ContentPage
{
    public StackLayoutSpacingPageCS()
    {
        Title = "StackLayout Spacing demo";
        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Spacing = 0,
            Children =
            {
                new Label { Text = "Primary colors" },
                new BoxView { Color = Color.Red },
                new BoxView { Color = Color.Yellow },
                new BoxView { Color = Color.Blue },
                new Label { Text = "Secondary colors" },
                new BoxView { Color = Color.Green },
                new BoxView { Color = Color.Orange },
                new BoxView { Color = Color.Purple }
            }
        };
    }
}
```

## <a name="position-and-size-of-child-views"></a>子视图的位置和大小

中子视图的大小和位置 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 取决于子视图的值和 [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) 属性以及 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 其 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 和属性的值 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 。 在垂直方向 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 上，子视图会在其大小未显式设置时展开以填充可用宽度。 同样，在水平方向 `StackLayout` 上，子视图会在其大小未显式设置时展开以填充可用高度。

及其 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 子视图的和属性 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 可以设置为结构中的字段， [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) 该结构封装两个布局首选项：

- *对齐方式* 确定子视图在其父布局中的位置和大小。
- *展开* 指示子视图是否应使用额外的空间（如果有）。

> [!TIP]
> 除非需要，否则不要设置的 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 和 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 属性 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。 `LayoutOptions.Fill` 和 `LayoutOptions.FillAndExpand` 的默认值可以实现最佳布局优化。 更改这些属性将会产生费用，甚至会消耗内存，即使在将它们设置回默认值时也是如此。

### <a name="alignment"></a>对齐方式

下面的 XAML 示例在中的每个子视图上设置了对齐首选项 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StackLayoutDemos.Views.AlignmentPage"
             Title="Alignment demo">
    <StackLayout Margin="20">
        <Label Text="Start"
               BackgroundColor="Gray"
               HorizontalOptions="Start" />
        <Label Text="Center"
               BackgroundColor="Gray"
               HorizontalOptions="Center" />
        <Label Text="End"
               BackgroundColor="Gray"
               HorizontalOptions="End" />
        <Label Text="Fill"
               BackgroundColor="Gray"
               HorizontalOptions="Fill" />
    </StackLayout>
</ContentPage>
```

在此示例中，在对象上设置了对齐首选项， [`Label`](xref:Xamarin.Forms.Label) 以控制它们在中的位置 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。 [`Start`](xref:Xamarin.Forms.LayoutOptions.Start)、、 [`Center`](xref:Xamarin.Forms.LayoutOptions.Center) [`End`](xref:Xamarin.Forms.LayoutOptions.End) 和 [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill) 字段用于定义父对象内的对象的对齐方式 [`Label`](xref:Xamarin.Forms.Label) `StackLayout` ：

[![已设置对齐选项的 StackLayout 的屏幕截图](stacklayout-images/alignment.png "带对齐选项的 StackLayout")](stacklayout-images/alignment-large.png#lightbox "带对齐选项的 StackLayout")

[`StackLayout`](xref:Xamarin.Forms.StackLayout) 仅遵循与 `StackLayout` 方向相反的子视图的对齐方式首选项。 因此，垂直方向的 `StackLayout` 中的 [`Label`](xref:Xamarin.Forms.Label) 子视图将其 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 属性设置为对齐方式字段中的其中一种：

- [`Start`](xref:Xamarin.Forms.LayoutOptions.Start)，它将定位在的 [`Label`](xref:Xamarin.Forms.Label) 左侧 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。
- [`Center`](xref:Xamarin.Forms.LayoutOptions.Center)，它将 [`Label`](xref:Xamarin.Forms.Label) 置于 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 中心。
- [`End`](xref:Xamarin.Forms.LayoutOptions.End)，它将定位在 [`Label`](xref:Xamarin.Forms.Label) 右侧 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。
- [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)，确保 [`Label`](xref:Xamarin.Forms.Label) 填充到 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 的宽度。

等效 C# 代码如下：

```csharp
public class AlignmentPageCS : ContentPage
{
    public AlignmentPageCS()
    {
        Title = "Alignment demo";
        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children =
            {
                new Label { Text = "Start", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Start },
                new Label { Text = "Center", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Center },
                new Label { Text = "End", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.End },
                new Label { Text = "Fill", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Fill }
            }
        };
    }
}
```

### <a name="expansion"></a>扩展

下面的 XAML 示例对中的每个设置了扩展首选项 [`Label`](xref:Xamarin.Forms.Label) [`StackLayout`](xref:Xamarin.Forms.StackLayout) ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StackLayoutDemos.Views.ExpansionPage"
             Title="Expansion demo">
    <StackLayout Margin="20">
        <BoxView BackgroundColor="Red"
                 HeightRequest="1" />
        <Label Text="Start"
               BackgroundColor="Gray"
               VerticalOptions="StartAndExpand" />
        <BoxView BackgroundColor="Red"
                 HeightRequest="1" />
        <Label Text="Center"
               BackgroundColor="Gray"
               VerticalOptions="CenterAndExpand" />
        <BoxView BackgroundColor="Red"
                 HeightRequest="1" />
        <Label Text="End"
               BackgroundColor="Gray"
               VerticalOptions="EndAndExpand" />
        <BoxView BackgroundColor="Red"
                 HeightRequest="1" />
        <Label Text="Fill"
               BackgroundColor="Gray"
               VerticalOptions="FillAndExpand" />
        <BoxView BackgroundColor="Red"
                 HeightRequest="1" />
    </StackLayout>
</ContentPage>
```

在此示例中，将在对象上设置扩展首选项 [`Label`](xref:Xamarin.Forms.Label) 以控制其在中的大小 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。 [`StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)、、 [`CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand) [`EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand) 和 [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) 字段用于定义对齐首选项，以及在 `Label` 父项中是否有更多空间（如果可用） `StackLayout` ：

[![已设置扩展选项的 StackLayout 的屏幕截图](stacklayout-images/expansion.png "具有扩展选项的 StackLayout")](stacklayout-images/expansion-large.png#lightbox "具有扩展选项的 StackLayout")

[`StackLayout`](xref:Xamarin.Forms.StackLayout) 只能按照其方向展开子视图。 因此，垂直方向的 `StackLayout` 可以扩展 [`Label`](xref:Xamarin.Forms.Label) 子视图，该视图将 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 属性设置为其中一个扩展字段。 这意味着，对于垂直对齐方式，每个 `Label` 在 `StackLayout` 内占据相同的空间量。 但是，只有最后一个 `Label`（可将 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 属性设置为 [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)）具有不同的大小。

> [!TIP]
> 使用时 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ，请确保只将一个子视图设置为 [`LayoutOptions.Expands`](xref:Xamarin.Forms.LayoutOptions.Expands) 。 此属性可确保指定子级会占用 `StackLayout` 可以向它提供的最大空间，而多次执行这些计算比较浪费。

等效 C# 代码如下：

```csharp
public ExpansionPageCS()
{
    Title = "Expansion demo";
    Content = new StackLayout
    {
        Margin = new Thickness(20),
        Children =
        {
            new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
            new Label { Text = "StartAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.StartAndExpand },
            new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
            new Label { Text = "CenterAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.CenterAndExpand },
            new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
            new Label { Text = "EndAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.EndAndExpand },
            new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
            new Label { Text = "FillAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.FillAndExpand },
            new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 }
        }
    };
}
```

> [!IMPORTANT]
> 使用 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 中的所有空间时，扩展首选项不起作用。

有关对齐方式和扩展的详细信息，请参阅[中 Xamarin.Forms 的布局选项](layout-options.md)。

## <a name="nested-stacklayout-objects"></a>嵌套的 StackLayout 对象

[`StackLayout`](xref:Xamarin.Forms.StackLayout)可用作包含嵌套子 `StackLayout` 对象或其他子布局的父布局。

以下 XAML 显示了嵌套对象的示例 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StackLayoutDemos.Views.CombinedStackLayoutPage"
             Title="Combined StackLayouts demo">
    <StackLayout Margin="20">
        ...
        <Frame BorderColor="Black"
               Padding="5">
            <StackLayout Orientation="Horizontal"
                         Spacing="15">
                <BoxView Color="Red" />
                <Label Text="Red"
                       FontSize="Large"
                       VerticalOptions="Center" />
            </StackLayout>
        </Frame>
        <Frame BorderColor="Black"
               Padding="5">
            <StackLayout Orientation="Horizontal"
                         Spacing="15">
                <BoxView Color="Yellow" />
                <Label Text="Yellow"
                       FontSize="Large"
                       VerticalOptions="Center" />
            </StackLayout>
        </Frame>
        <Frame BorderColor="Black"
               Padding="5">
            <StackLayout Orientation="Horizontal"
                         Spacing="15">
                <BoxView Color="Blue" />
                <Label Text="Blue"
                       FontSize="Large"
                       VerticalOptions="Center" />
            </StackLayout>
        </Frame>
        ...
    </StackLayout>
</ContentPage>
```

在此示例中，父对象 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 包含 `StackLayout` 对象内的嵌套对象 [`Frame`](xref:Xamarin.Forms.Frame) 。 父对象是垂直方向的 `StackLayout` ，而子 `StackLayout` 对象是水平方向的：

[![嵌套 StackLayout 对象的屏幕截图](stacklayout-images/combined.png "嵌套 StackLayouts")](stacklayout-images/combined-large.png#lightbox "嵌套 StackLayouts")

> [!IMPORTANT]
> 嵌套 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 对象和其他布局的深度越多，嵌套布局就会影响性能。 有关详细信息，请参阅 [选择正确的布局](~/xamarin-forms/deploy-test/performance.md#choose-the-correct-layout)。

等效 C# 代码如下：

```csharp
public class CombinedStackLayoutPageCS : ContentPage
{
    public CombinedStackLayoutPageCS()
    {
        Title = "Combined StackLayouts demo";
        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children =
            {
                new Label { Text = "Primary colors" },
                new Frame
                {
                    BorderColor = Color.Black,
                    Padding = new Thickness(5),
                    Content = new StackLayout
                    {
                        Orientation = StackOrientation.Horizontal,
                        Spacing = 15,
                        Children =
                        {
                            new BoxView { Color = Color.Red },
                            new Label { Text = "Red", FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)), VerticalOptions = LayoutOptions.Center }
                        }
                    }
                },
                new Frame
                {
                    BorderColor = Color.Black,
                    Padding = new Thickness(5),
                    Content = new StackLayout
                    {
                        Orientation = StackOrientation.Horizontal,
                        Spacing = 15,
                        Children =
                        {
                            new BoxView { Color = Color.Yellow },
                            new Label { Text = "Yellow", FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)), VerticalOptions = LayoutOptions.Center }
                        }
                    }
                },
                new Frame
                {
                    BorderColor = Color.Black,
                    Padding = new Thickness(5),
                    Content = new StackLayout
                    {
                        Orientation = StackOrientation.Horizontal,
                        Spacing = 15,
                        Children =
                        {
                            new BoxView { Color = Color.Blue },
                            new Label { Text = "Blue", FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)), VerticalOptions = LayoutOptions.Center }
                        }
                    }
                },
                // ...
            }
        };
    }
}
```

## <a name="related-links"></a>相关链接

- [StackLayout 演示 (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-stacklayoutdemos)
- [布局选项 Xamarin.Forms](layout-options.md)
- [选择 Xamarin.Forms 布局](choose-layout.md)
- [提高 Xamarin.Forms 应用程序性能](~/xamarin-forms/deploy-test/performance.md)