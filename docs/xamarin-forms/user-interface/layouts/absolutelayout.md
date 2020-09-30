---
title: Xamarin.Forms AbsoluteLayout
description: Xamarin.FormsAbsoluteLayout 用于使用显式值或与布局大小成正比的值来对元素进行定位和调整大小。
ms.prod: xamarin
ms.assetid: 01A5CCE0-AD45-4806-84FD-72C007005B38
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2020
ms.custom: contperfq1
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0468d132ee6e75ab75c0150f1f5c9e0af6dfde40
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91557772"
---
# <a name="no-locxamarinforms-absolutelayout"></a>Xamarin.Forms AbsoluteLayout

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-absolutelayoutdemos)

[![：： no (Xamarin。 Forms) ：：： AbsoluteLayout](absolutelayout-images/layouts.png)](absolutelayout-images/layouts-large.png#lightbox)

[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)用于使用显式值定位子级并调整其大小。 位置由子级的左上角指定，相对于的左上角 `AbsoluteLayout` ，以与设备无关的单位。 `AbsoluteLayout` 还实现了按比例定位和调整大小的功能。 此外，与其他一些布局类不同， `AbsoluteLayout` 可以定位子级，使其重叠。

[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)应视为只在您可以对子级施加大小或元素的大小不会影响其他子级的位置时使用的特殊用途的布局。

[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)类定义以下属性：

- [`LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty)类型为的， [`Rectangle`](xref:Xamarin.Forms.Rectangle) 它是一个附加属性，表示子级的位置和大小。 此属性的默认值为 (0，0，自动调整大小) 。
- [`LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty)，类型 [`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags) 为，它是一个附加属性，指示是否按比例解释用于定位和调整子级大小的布局边界的属性。 此属性的默认值为 `AbsoluteLayoutFlags.None`。

这些属性是由对象支持的 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，这意味着属性可以是数据绑定的目标和样式化的。 有关附加属性的详细信息，请参阅[ Xamarin.Forms 附加属性](~/xamarin-forms/xaml/attached-properties.md)。

[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)类派生自 `Layout<T>` 类，该类定义 `Children` 类型的属性 `IList<T>` 。 `Children`属性是类的 `ContentProperty` `Layout<T>` ，因此无需从 XAML 显式设置。

> [!TIP]
> 若要获得最佳布局性能，请遵循 [优化布局性能](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance)中的准则。

## <a name="position-and-size-children"></a>位置和大小子项

[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)通过 [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) 使用绝对值或成比例值设置每个子元素的附加属性，来定义中子元素的位置和大小。 当位置应为 "缩放" 时，"绝对" 和 "比例" 值可以混合，但大小应保持固定，反之亦然。 有关绝对值的信息，请参阅 [绝对定位和调整大小](#absolute-positioning-and-sizing)。 有关比例值的信息，请参阅按 [比例定位和调整大小](#proportional-positioning-and-sizing)。

[`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty)无论使用的是绝对值还是按比例值，都可以使用两种格式设置附加的属性：

- `x, y`. 采用此格式时， `x` 和 `y` 值指示子控件左上角相对于其父级的位置。 子元素不受约束，并调整其大小。
- `x, y, width, height`. 采用此格式时， `x` 和 `y` 值指示子元素的左上角相对于其父级的位置，而 `width` 和值指示子对象的 `height` 大小。

若要将子大小自身指定为水平或垂直，或同时指定这两者，请将 `width` 和/或 `height` 值设置为 [`AbsoluteLayout.AutoSize`](xref:Xamarin.Forms.AbsoluteLayout.AutoSize) 属性。 但是，过度利用此属性可能会损害应用程序性能，因为这会导致布局引擎执行其他布局计算。

> [!IMPORTANT]
> [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions)和 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 属性对的子级不起作用 [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) 。

## <a name="absolute-positioning-and-sizing"></a>绝对定位和调整大小

默认情况下， [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) 使用绝对值的位置并调整子级的大小，这些值在与设备无关的单位中指定，显式定义子元素在布局中的放置位置。 为实现此目的，可将子级添加到的 `Children` 集合 `AbsoluteLayout` ，并将 [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) 每个子元素的附加属性设置为绝对位置和/或大小值。

> [!WARNING]
> 使用绝对值定位和调整子级大小可能会产生问题，因为不同的设备具有不同的屏幕大小和分辨率。 因此，在一个设备上，屏幕中心的坐标可能在其他设备上偏移。

下面的 XAML 演示了一个 [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) 使用绝对值定位的子元素：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="AbsoluteLayoutDemos.Views.StylishHeaderDemoPage"
             Title="Stylish header demo">
    <AbsoluteLayout Margin="20">
        <BoxView Color="Silver"
                 AbsoluteLayout.LayoutBounds="0, 10, 200, 5" />
        <BoxView Color="Silver"
                 AbsoluteLayout.LayoutBounds="0, 20, 200, 5" />
        <BoxView Color="Silver"
                 AbsoluteLayout.LayoutBounds="10, 0, 5, 65" />
        <BoxView Color="Silver"
                 AbsoluteLayout.LayoutBounds="20, 0, 5, 65" />
        <Label Text="Stylish Header"
               FontSize="24"
               AbsoluteLayout.LayoutBounds="30, 25" />
    </AbsoluteLayout>
</ContentPage>
```

在此示例中， [`BoxView`](xref:Xamarin.Forms.BoxView) 使用在附加属性中指定的前两个绝对值来定义每个对象的位置 [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) 。 `BoxView`使用第三个和第四个值定义每个的大小。 [`Label`](xref:Xamarin.Forms.Label)使用在附加属性中指定的两个绝对值来定义对象的位置 `AbsoluteLayout.LayoutBounds` 。 未指定的大小值 `Label` ，因此它不受约束且大小本身。 在所有情况下，绝对值代表与设备无关的单位。

以下屏幕截图显示了生成的布局：

![使用绝对值放置在 AbsoluteLayout 中的子项](absolutelayout-images/absolute-values.png)

下面显示了等效的 c # 代码：

```csharp
public class StylishHeaderDemoPageCS : ContentPage
{
    public StylishHeaderDemoPageCS()
    {
        AbsoluteLayout absoluteLayout = new AbsoluteLayout
        {
            Margin = new Thickness(20)
        };

        absoluteLayout.Children.Add(new BoxView
        {
            Color = Color.Silver,
        }, new Rectangle(0, 10, 200, 5));
        absoluteLayout.Children.Add(new BoxView
        {
            Color = Color.Silver
        }, new Rectangle(0, 20, 200, 5));
        absoluteLayout.Children.Add(new BoxView
        {
            Color = Color.Silver
        }, new Rectangle(10, 0, 5, 65));
        absoluteLayout.Children.Add(new BoxView
        {
            Color = Color.Silver
        }, new Rectangle(20, 0, 5, 65));

        absoluteLayout.Children.Add(new Label
        {
            Text = "Stylish Header",
            FontSize = 24
        }, new Point(30,25));                    

        Title = "Stylish header demo";
        Content = absoluteLayout;
    }
}
```

在此示例中，每个的位置和大小 [`BoxView`](xref:Xamarin.Forms.BoxView) 都是使用 [`Rectangle`](xref:Xamarin.Forms.Rectangle) 对象定义的。 [`Label`](xref:Xamarin.Forms.Label)使用对象定义的位置 [`Point`](xref:Xamarin.Forms.Point) 。

在 c # 中，还可以 [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) 使用方法在将其添加到集合后，设置的子级的位置和大小 `Children` [`AbsoluteLayout.SetLayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.SetLayoutBounds*) 。 此方法的第一个参数是子级，第二个参数是 [`Rectangle`](xref:Xamarin.Forms.Rectangle) 对象。

> [!NOTE]
> [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)使用绝对值的可以定位和调整子级的大小，使其不适合布局边界。

## <a name="proportional-positioning-and-sizing"></a>按比例定位和调整大小

[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)使用比例值可以定位和调整子级的位置。 这是通过以下方式实现的：将子级添加到 `Children` 的集合 `AbsoluteLayout` ，并将 [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) 每个子元素的附加属性设置为范围0-1 中的比例位置和/或大小值。 位置和大小值是通过设置 [`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) 每个子项的附加属性来形成比例的。

使用 [`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) 类型的附加属性 [`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags) ，可以设置一个标志，该标志指示子级的布局边界位置和大小值与的大小成正比 `AbsoluteLayout` 。 在布局子级时，会将 `AbsoluteLayout` 位置和大小值适当地缩放到任何设备大小。

[`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags)枚举定义以下成员：

- `None`指示将值解释为绝对值。 这是附加属性的默认值 [`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) 。
- `XProportional`指示 `x` 将值视为比例，同时将所有其他值视为绝对值。
- `YProportional`指示 `y` 将值视为比例，同时将所有其他值视为绝对值。
- `WidthProportional`指示 `width` 将值视为比例，同时将所有其他值视为绝对值。
- `HeightProportional`指示 `height` 将值视为比例，同时将所有其他值视为绝对值。
- `PositionProportional`指示 `x` 和 `y` 值将被解释为比例值，而大小值解释为绝对值。
- `SizeProportional`指示 `width` 和 `height` 值将被解释为比例，而位置值被解释为绝对。
- `All`指示将所有值视为比例值。

> [!TIP]
> [`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags)枚举是 `Flags` 枚举，这意味着枚举成员可以组合。 此操作在 XAML 中以逗号分隔列表的形式完成，并在 c # 中使用按位 "或" 运算符。

例如，如果使用 `SizeProportional` 标志并将子的宽度设置为0.25，并将高度设置为0.1，则子级将是的宽度的四分之一，并是高度的四分之一 [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) 。 `PositionProportional`标志类似。  (0，0) 的位置会将子级置于左上角，而 (1、1) 的位置会将子级置于右下角，而 (0.5，0.5) 的位置将在内居中 `AbsoluteLayout` 。

下面的 XAML 演示了一个 [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) 使用比例值定位的子元素：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="AbsoluteLayoutDemos.Views.ProportionalDemoPage"
             Title="Proportional demo">
    <AbsoluteLayout>
        <BoxView Color="Blue"
                 AbsoluteLayout.LayoutBounds="0.5,0,100,25"
                 AbsoluteLayout.LayoutFlags="PositionProportional" />
        <BoxView Color="Green"
                 AbsoluteLayout.LayoutBounds="0,0.5,25,100"
                 AbsoluteLayout.LayoutFlags="PositionProportional" />
        <BoxView Color="Red"
                 AbsoluteLayout.LayoutBounds="1,0.5,25,100"
                 AbsoluteLayout.LayoutFlags="PositionProportional" />
        <BoxView Color="Black"
                 AbsoluteLayout.LayoutBounds="0.5,1,100,25"
                 AbsoluteLayout.LayoutFlags="PositionProportional" />
        <Label Text="Centered text"
               AbsoluteLayout.LayoutBounds="0.5,0.5,110,25"
               AbsoluteLayout.LayoutFlags="PositionProportional" />
    </AbsoluteLayout>
</ContentPage>
```

在此示例中，每个子项使用比例值进行定位，但使用绝对值进行调整。 这是通过将 [`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) 每个子级的附加属性设置为来完成的 `PositionProportional` 。 对于每个子元素，在附加属性中指定的前两个值 [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) 使用比例值定义位置。 使用与设备无关的单位，使用第三个和下一个绝对值来定义每个子元素的大小。

以下屏幕截图显示了生成的布局：

![使用比例位置值放置在 AbsoluteLayout 中的子级](absolutelayout-images/proportional-position.png)

下面显示了等效的 c # 代码：

```csharp
public class ProportionalDemoPageCS : ContentPage
{
    public ProportionalDemoPageCS()
    {
        BoxView blue = new BoxView { Color = Color.Blue };
        AbsoluteLayout.SetLayoutBounds(blue, new Rectangle(0.5, 0, 100, 25));
        AbsoluteLayout.SetLayoutFlags(blue, AbsoluteLayoutFlags.PositionProportional);

        BoxView green = new BoxView { Color = Color.Green };
        AbsoluteLayout.SetLayoutBounds(green, new Rectangle(0, 0.5, 25, 100));
        AbsoluteLayout.SetLayoutFlags(green, AbsoluteLayoutFlags.PositionProportional);

        BoxView red = new BoxView { Color = Color.Red };
        AbsoluteLayout.SetLayoutBounds(red, new Rectangle(1, 0.5, 25, 100));
        AbsoluteLayout.SetLayoutFlags(red, AbsoluteLayoutFlags.PositionProportional);

        BoxView black = new BoxView { Color = Color.Black };
        AbsoluteLayout.SetLayoutBounds(black, new Rectangle(0.5, 1, 100, 25));
        AbsoluteLayout.SetLayoutFlags(black, AbsoluteLayoutFlags.PositionProportional);

        Label label = new Label { Text = "Centered text" };
        AbsoluteLayout.SetLayoutBounds(label, new Rectangle(0.5, 0.5, 110, 25));
        AbsoluteLayout.SetLayoutFlags(label, AbsoluteLayoutFlags.PositionProportional);

        Title = "Proportional demo";
        Content = new AbsoluteLayout
        {
            Children = { blue, green, red, black, label }
        };
    }
}
```

在此示例中，每个子级的位置和大小都是通过 [`AbsoluteLayout.SetLayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.SetLayoutBounds*) 方法设置的。 方法的第一个参数是子级，第二个参数是 [`Rectangle`](xref:Xamarin.Forms.Rectangle) 对象。 每个子项的位置都是使用比例值设置的，而每个子级的大小则使用与设备无关的单位设置的绝对值。

> [!NOTE]
> [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)使用比例值的可以定位和调整子级，以便通过使用0-1 范围之外的值，使其不适合布局边界内。

## <a name="related-links"></a>相关链接

- [AbsoluteLayout 演示 (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-absolutelayoutdemos)
- [Xamarin.Forms 附加属性](~/xamarin-forms/xaml/attached-properties.md)
- [选择 Xamarin.Forms 布局](choose-layout.md)
- [提高 Xamarin.Forms 应用程序性能](~/xamarin-forms/deploy-test/performance.md)