---
title: Xamarin.Forms RelativeLayout
description: Xamarin.FormsRelativeLayout 使用相对于布局或同级元素属性的位置和大小子级。
ms.prod: xamarin
ms.assetid: 2530BCB8-01B8-4C4F-BF14-CA53659F1B5A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/13/2020
ms.custom: contperfq1
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d350ceee778c9f9ba9f25555a89a925558c6d38b
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556836"
---
# <a name="no-locxamarinforms-relativelayout"></a>Xamarin.Forms RelativeLayout

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-relativelayoutdemos)

[![：： no (Xamarin。 Forms) ：：： RelativeLayout](relativelayout-images/layouts.png)](relativelayout-images/layouts-large.png#lightbox)

[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)用于相对于布局或同级元素的属性定位和调整子级的大小。 这允许创建在不同设备大小之间按比例缩放的 Ui。 此外，与其他一些布局类不同， `RelativeLayout` 可以定位子级，使其重叠。

[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)类定义以下属性：

- [`XConstraint`](xref:Xamarin.Forms.RelativeLayout.XConstraintProperty)，类型 [`Constraint`](xref:Xamarin.Forms.Constraint) 为，它是一个附加属性，表示子级的 X 位置上的约束。
- [`YConstraint`](xref:Xamarin.Forms.RelativeLayout.YConstraintProperty)类型为的， [`Constraint`](xref:Xamarin.Forms.Constraint) 它是一个附加属性，表示对子级的 Y 位置的约束。
- [`WidthConstraint`](xref:Xamarin.Forms.RelativeLayout.WidthConstraintProperty)，类型 [`Constraint`](xref:Xamarin.Forms.Constraint) 为，它是表示子级宽度约束的附加属性。
- [`HeightConstraint`](xref:Xamarin.Forms.RelativeLayout.HeightConstraintProperty)类型为的， [`Constraint`](xref:Xamarin.Forms.Constraint) 它是一个附加属性，表示子级高度的约束。
- [`BoundsConstraint`](xref:Xamarin.Forms.RelativeLayout.BoundsConstraintProperty)，类型 [`BoundsConstraint`](xref:Xamarin.Forms.BoundsConstraint) 为，它是一个附加属性，它表示对子级的位置和大小的约束。 此属性无法从 XAML 轻松使用。

这些属性是由对象支持的 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，这意味着属性可以是数据绑定的目标和样式化的。 有关附加属性的详细信息，请参阅[ Xamarin.Forms 附加属性](~/xamarin-forms/xaml/attached-properties.md)。

> [!NOTE]
> [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)还可以通过子 `WidthRequest` 和 `HeightRequest` 属性（而不是 [`WidthConstraint`](xref:Xamarin.Forms.RelativeLayout.WidthConstraintProperty) 和 [`HeightConstraint`](xref:Xamarin.Forms.RelativeLayout.HeightConstraintProperty) 附加属性）指定中子级的宽度和高度。

[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)类派生自 `Layout<T>` 类，该类定义 `Children` 类型的属性 `IList<T>` 。 `Children`属性是类的 `ContentProperty` `Layout<T>` ，因此无需从 XAML 显式设置。

> [!TIP]
> 尽可能避免使用 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)。 它会导致 CPU 不得不执行显著更多的工作。

## <a name="constraints"></a>约束

在中 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) ，将子级的位置和大小指定为使用绝对值或相对值的约束。 未指定约束时，子元素将定位到布局的左上角。

下表显示了如何在 XAML 和 c # 中指定约束：

|     | XAML | C# |
| --- | ---- | -- |
| **绝对值** | 绝对约束通过将 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) 附加属性设置为值来指定 `double` 。 | 绝对约束由 [`Constraint.Constant`](xref:Xamarin.Forms.Constraint.Constant*) 方法指定，或通过使用 `Children.Add` 需要自变量的重载来指定 `Func<Rectangle>` 。 |
| **相对值** | 相对约束通过将 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) 附加属性设置为 [`Constraint`](xref:Xamarin.Forms.Constraint) 标记扩展返回的对象来指定 `ConstraintExpression` 。 | 相对约束由类的 [`Constraint`](xref:Xamarin.Forms.Constraint) 方法返回的对象指定 `Constraint` 。 |

有关使用绝对值指定约束的详细信息，请参阅 [绝对定位和调整大小](#absolute-positioning-and-sizing)。 有关使用相对值指定约束的详细信息，请参阅 [相对定位和调整大小](#relative-positioning-and-sizing)。

在 c # 中，可以 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) 通过三个重载将子级添加到 `Add` 。 第一个重载需要 `Expression<Func<Rectangle>>` 指定子级的位置和大小。 第二个重载需要 `Expression<Func<double>>` `x` 、 `y` 、 `width` 和参数的可选对象 `height` 。 第三个重载需要 `Constraint` `x` 、 `y` 、 `width` 和参数的可选对象 `height` 。

可以 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) 使用 [`SetXConstraint`](xref:Xamarin.Forms.RelativeLayout.SetXConstraint*) 、 [`SetYConstraint`](xref:Xamarin.Forms.RelativeLayout.SetYConstraint*) 、 [`SetWidthConstraint`](xref:Xamarin.Forms.RelativeLayout.SetWidthConstraint*) 和 [`SetHeightConstraint`](xref:Xamarin.Forms.RelativeLayout.SetHeightConstraint*) 方法更改中子级的位置和大小。 其中每个方法的第一个参数是子级，第二个参数是 [`Constraint`](xref:Xamarin.Forms.Constraint) 对象。 此外， [`SetBoundsConstraint`](xref:Xamarin.Forms.RelativeLayout.SetBoundsConstraint*) 还可以使用方法来更改子级的位置和大小。 此方法的第一个参数是子级，第二个参数是 [`BoundsConstraint`](xref:Xamarin.Forms.BoundsConstraint) 对象。

## <a name="absolute-positioning-and-sizing"></a>绝对定位和调整大小

[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)使用绝对值（在与设备无关的单位中指定）来定位和调整子级的大小，这些单元显式定义子应在布局中放置的位置。 为实现此目的，可将子级添加到的 `Children` 集合 `RelativeLayout` ，并 [`XConstraint`](xref:Xamarin.Forms.RelativeLayout.XConstraintProperty) 将 [`YConstraint`](xref:Xamarin.Forms.RelativeLayout.YConstraintProperty) [`WidthConstraint`](xref:Xamarin.Forms.RelativeLayout.WidthConstraintProperty) 每个子级的、、和 [`HeightConstraint`](xref:Xamarin.Forms.RelativeLayout.HeightConstraintProperty) 附加属性设置为绝对位置和/或大小值。

> [!WARNING]
> 使用绝对值定位和调整子级大小可能会产生问题，因为不同的设备具有不同的屏幕大小和分辨率。 因此，在一个设备上，屏幕中心的坐标可能在其他设备上偏移。

下面的 XAML 演示了一个 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) 使用绝对值定位的子元素：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="RelativeLayoutDemos.Views.StylishHeaderDemoPage"
             Title="Stylish header demo">
    <RelativeLayout Margin="20">
        <BoxView Color="Silver"
                 RelativeLayout.XConstraint="0"
                 RelativeLayout.YConstraint="10"
                 RelativeLayout.WidthConstraint="200"
                 RelativeLayout.HeightConstraint="5" />
        <BoxView Color="Silver"
                 RelativeLayout.XConstraint="0"
                 RelativeLayout.YConstraint="20"
                 RelativeLayout.WidthConstraint="200"
                 RelativeLayout.HeightConstraint="5" />
        <BoxView Color="Silver"
                 RelativeLayout.XConstraint="10"
                 RelativeLayout.YConstraint="0"
                 RelativeLayout.WidthConstraint="5"
                 RelativeLayout.HeightConstraint="65" />
        <BoxView Color="Silver"
                 RelativeLayout.XConstraint="20"
                 RelativeLayout.YConstraint="0"
                 RelativeLayout.WidthConstraint="5"
                 RelativeLayout.HeightConstraint="65" />
        <Label Text="Stylish header"
               FontSize="24"
               RelativeLayout.XConstraint="30"
               RelativeLayout.YConstraint="25" />
    </RelativeLayout>
</ContentPage>
```

在此示例中， [`BoxView`](xref:Xamarin.Forms.BoxView) 使用 [`XConstraint`](xref:Xamarin.Forms.RelativeLayout.XConstraintProperty) 和附加属性中指定的值定义每个对象的位置 [`YConstraint`](xref:Xamarin.Forms.RelativeLayout.YConstraintProperty) 。 每个的大小 `BoxView` 都是使用 [`WidthConstraint`](xref:Xamarin.Forms.RelativeLayout.WidthConstraintProperty) 和附加属性中指定的值来定义的 [`HeightConstraint`](xref:Xamarin.Forms.RelativeLayout.HeightConstraintProperty) 。 [`Label`](xref:Xamarin.Forms.Label)还使用在 `XConstraint` 和附加属性中指定的值定义对象的位置 `YConstraint` 。 但是，不会为指定大小值， `Label` 因此它不受约束且大小本身。 在所有情况下，绝对值代表与设备无关的单位。

以下屏幕截图显示布局结果：

![使用绝对值放置在 RelativeLayout 中的子项](relativelayout-images/absolute-values.png)

下面显示了等效的 c # 代码：

```csharp
public class StylishHeaderDemoPageCS : ContentPage
{
    public StylishHeaderDemoPageCS()
    {
        RelativeLayout relativeLayout = new RelativeLayout
        {
            Margin = new Thickness(20)
        };

        relativeLayout.Children.Add(new BoxView
        {
            Color = Color.Silver
        }, () => new Rectangle(0, 10, 200, 5));

        relativeLayout.Children.Add(new BoxView
        {
            Color = Color.Silver
        }, () => new Rectangle(0, 20, 200, 5));

        relativeLayout.Children.Add(new BoxView
        {
            Color = Color.Silver
        }, () => new Rectangle(10, 0, 5, 65));

        relativeLayout.Children.Add(new BoxView
        {
            Color = Color.Silver
        }, () => new Rectangle(20, 0, 5, 65));

        relativeLayout.Children.Add(new Label
        {
            Text = "Stylish Header",
            FontSize = 24
        }, Constraint.Constant(30), Constraint.Constant(25));

        Title = "Stylish header demo";
        Content = relativeLayout;
    }
}
```

在此示例中， [`BoxView`](xref:Xamarin.Forms.BoxView) 使用需要的重载将对象添加到中，以便 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) `Add` `Expression<Func<Rectangle>>` 指定每个子对象的位置和大小。 的位置 [`Label`](xref:Xamarin.Forms.Label) 使用 `Add` 需要可选对象的重载 [`Constraint`](xref:Xamarin.Forms.Constraint) （在此示例中由方法创建）定义 [`Constraint.Constant`](xref:Xamarin.Forms.Constraint.Constant*) 。

> [!NOTE]
> [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)使用绝对值的可以定位和调整子级的大小，使其不适合布局边界。

## <a name="relative-positioning-and-sizing"></a>相对定位和调整大小

[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)使用相对于布局的属性的值或同级元素，可以定位子级并调整其大小。 为实现此目的，可将子级添加到的 `Children` 集合， `RelativeLayout` 并 [`XConstraint`](xref:Xamarin.Forms.RelativeLayout.XConstraintProperty) [`YConstraint`](xref:Xamarin.Forms.RelativeLayout.YConstraintProperty) [`WidthConstraint`](xref:Xamarin.Forms.RelativeLayout.WidthConstraintProperty) 使用对象将每个子级的、、和 [`HeightConstraint`](xref:Xamarin.Forms.RelativeLayout.HeightConstraintProperty) 附加属性设置为相对值 [`Constraint`](xref:Xamarin.Forms.Constraint) 。

约束可以是常量，也可以是相对于父对象的常数。 约束的类型由枚举表示，该 [`ConstraintType`](xref:Xamarin.Forms.ConstraintType) 枚举定义以下成员：

- `RelativeToParent`，指示相对于父级的约束。
- `RelativeToView`，它指示与视图 (或同级) 相关的约束。
- `Constant`，它指示常量约束。

### <a name="constraint-markup-extension"></a>约束标记扩展

在 XAML 中， [`Constraint`](xref:Xamarin.Forms.Constraint) 可以通过标记扩展来创建对象 [`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression) 。 此标记扩展通常用于将中的子元素的位置和大小与父对象 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) 或同级对象相关联。

[`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression)类定义以下属性：

- [`Constant`](xref:Xamarin.Forms.ConstraintExpression.Constant)，类型为 `double` ，表示约束常量值。
- [`ElementName`](xref:Xamarin.Forms.ConstraintExpression.ElementName)，类型为 `string` ，表示要对其计算约束的源元素的名称。
- [`Factor`](xref:Xamarin.Forms.ConstraintExpression.Factor)类型为的， `double` 它表示相对于源元素缩放约束维度所依据的因子。 此属性的默认值为1。
- [`Property`](xref:Xamarin.Forms.ConstraintExpression.Property)，类型为 `string` ，表示源元素上要用于约束计算的属性的名称。
- [`Type`](xref:Xamarin.Forms.ConstraintExpression.Type)，类型为 [`ConstraintType`](xref:Xamarin.Forms.ConstraintType) ，表示约束的类型。

有关 Xamarin.Forms 标记扩展的详细信息，请参阅 [XAML 标记扩展](~/xamarin-forms/xaml/markup-extensions/index.md)。

下面的 XAML 演示了一个 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) 其子级受 [`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression) 标记扩展约束的：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="RelativeLayoutDemos.Views.RelativePositioningAndSizingDemoPage"
             Title="RelativeLayout demo">
    <RelativeLayout>
        <BoxView Color="Red"
                 RelativeLayout.XConstraint="{ConstraintExpression Type=Constant, Constant=0}"
                 RelativeLayout.YConstraint="{ConstraintExpression Type=Constant, Constant=0}" />
        <BoxView Color="Green"
                 RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Constant=-40}"
                 RelativeLayout.YConstraint="{ConstraintExpression Type=Constant, Constant=0}" />
        <BoxView Color="Blue"
                 RelativeLayout.XConstraint="{ConstraintExpression Type=Constant, Constant=0}"
                 RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Constant=-40}" />
        <BoxView Color="Yellow"
                 RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Constant=-40}"
                 RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Constant=-40}" />

        <!-- Centered and 1/3 width and height of parent -->
        <BoxView x:Name="oneThird"
                 Color="Silver"
                 RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0.33}"
                 RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0.33}"
                 RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0.33}"
                 RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0.33}" />

        <!-- 1/3 width and height of previous -->
        <BoxView Color="Black"
                 RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToView, ElementName=oneThird, Property=X}"
                 RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToView, ElementName=oneThird, Property=Y}"
                 RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToView, ElementName=oneThird, Property=Width, Factor=0.33}"
                 RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToView, ElementName=oneThird, Property=Height, Factor=0.33}" />
    </RelativeLayout>
</ContentPage>
```

在此示例中， [`BoxView`](xref:Xamarin.Forms.BoxView) 通过设置 [`XConstraint`](xref:Xamarin.Forms.RelativeLayout.XConstraintProperty) 和附加属性来定义每个对象的位置 [`YConstraint`](xref:Xamarin.Forms.RelativeLayout.YConstraintProperty) 。 第一个 `BoxView` 属性的 `XConstraint` 和 `YConstraint` 附加属性设置为常量，它们是绝对值。 其余 `BoxView` 对象均使用至少一个相对值设置其位置。 例如，黄色 `BoxView` 对象将 `XConstraint` 附加属性设置为其父级 ([`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)) 减40的宽度。 同样，这会 `BoxView` 将 `YConstraint` 附加属性设置为其父级减40的高度。 这确保了黄色 `BoxView` 出现在屏幕的右下角。

> [!NOTE]
> [`BoxView`](xref:Xamarin.Forms.BoxView) 未指定大小的对象将自动调整大小为 40x40 Xamarin.Forms 。

名为的白银 [`BoxView`](xref:Xamarin.Forms.BoxView) `oneThird` 相对于其父级进行集中定位。 它也相对于其父级进行了调整，它的宽度和高度均为三分之一。 这是通过将 [`XConstraint`](xref:Xamarin.Forms.RelativeLayout.XConstraintProperty) 和 [`WidthConstraint`](xref:Xamarin.Forms.RelativeLayout.WidthConstraintProperty) 附加属性设置为) 的父 (的宽度 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) （乘以0.33）来实现的。 同样， [`YConstraint`](xref:Xamarin.Forms.RelativeLayout.YConstraintProperty) 和 [`HeightConstraint`](xref:Xamarin.Forms.RelativeLayout.HeightConstraintProperty) 附加属性设置为父级的高度，乘以0.33。

黑色的 [`BoxView`](xref:Xamarin.Forms.BoxView) 定位和相对于的大小 `oneThird` `BoxView` 。 这是通过将 [`XConstraint`](xref:Xamarin.Forms.RelativeLayout.XConstraintProperty) 和 [`YConstraint`](xref:Xamarin.Forms.RelativeLayout.YConstraintProperty) 附加属性分别设置为 `X` `Y` 同级元素的和值来实现的。 同样，其大小设置为其同级元素的宽度和高度的三分之一。 为此，可将其 [`WidthConstraint`](xref:Xamarin.Forms.RelativeLayout.WidthConstraintProperty) 和 [`HeightConstraint`](xref:Xamarin.Forms.RelativeLayout.HeightConstraintProperty) 附加属性分别设置 `Width` 为 `Height` 同级元素的和值，然后再乘以0.33。

以下屏幕截图显示了生成的布局：

![使用相对值放置在 RelativeLayout 中的子级](relativelayout-images/relative-values.png)

### <a name="constraint-objects"></a>约束对象

[`Constraint`](xref:Xamarin.Forms.Constraint)类定义以下公共静态方法，这些方法返回 `Constraint` 对象：

- [`Constant`](xref:Xamarin.Forms.Constraint.Constant*)，它将子级约束为使用指定的大小 `double` 。
- [`FromExpression`](xref:Xamarin.Forms.Constraint.FromExpression*)，它使用 lambda 表达式约束子。
- [`RelativeToParent`](xref:Xamarin.Forms.Constraint.RelativeToParent*)，它相对于其父级的大小约束子级。
- [`RelativeToView`](xref:Xamarin.Forms.Constraint.RelativeToView*)，它约束子级相对于视图的大小。

此外，类还 [`BoundsConstraint`](xref:Xamarin.Forms.BoundsConstraint) 定义了一个方法，该方法 [`FromExpression`](xref:Xamarin.Forms.BoundsConstraint.FromExpression*) 返回一个， `BoundsConstraint` 它使用约束子的位置和大小 `Expression<Func<Rectangle>>` 。 此方法可用于设置 [`BoundsConstraint`](xref:Xamarin.Forms.RelativeLayout.BoundsConstraintProperty) 附加属性。

下面的 c # 代码显示了 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) 其子对象受 [`Constraint`](xref:Xamarin.Forms.Constraint) 对象约束的：

```csharp
public class RelativePositioningAndSizingDemoPageCS : ContentPage
{
    public RelativePositioningAndSizingDemoPageCS()
    {
        RelativeLayout relativeLayout = new RelativeLayout();

        // Four BoxView's
        relativeLayout.Children.Add(
            new BoxView { Color = Color.Red },
            Constraint.Constant(0),
            Constraint.Constant(0));

        relativeLayout.Children.Add(
            new BoxView { Color = Color.Green },
            Constraint.RelativeToParent((parent) =>
            {
                return parent.Width - 40;
            }), Constraint.Constant(0));

        relativeLayout.Children.Add(
            new BoxView { Color = Color.Blue },
            Constraint.Constant(0),
            Constraint.RelativeToParent((parent) =>
            {
                return parent.Height - 40;
            }));

        relativeLayout.Children.Add(
            new BoxView { Color = Color.Yellow },
            Constraint.RelativeToParent((parent) =>
            {
                return parent.Width - 40;
            }),
            Constraint.RelativeToParent((parent) =>
            {
                return parent.Height - 40;
            }));

        // Centered and 1/3 width and height of parent
        BoxView silverBoxView = new BoxView { Color = Color.Silver };
        relativeLayout.Children.Add(
            silverBoxView,
            Constraint.RelativeToParent((parent) =>
            {
                return parent.Width * 0.33;
            }),
            Constraint.RelativeToParent((parent) =>
            {
                return parent.Height * 0.33;
            }),
            Constraint.RelativeToParent((parent) =>
            {
                return parent.Width * 0.33;
            }),
            Constraint.RelativeToParent((parent) =>
            {
                return parent.Height * 0.33;
            }));

        // 1/3 width and height of previous
        relativeLayout.Children.Add(
            new BoxView { Color = Color.Black },
            Constraint.RelativeToView(silverBoxView, (parent, sibling) =>
            {
                return sibling.X;
            }),
            Constraint.RelativeToView(silverBoxView, (parent, sibling) =>
            {
                return sibling.Y;
            }),
            Constraint.RelativeToView(silverBoxView, (parent, sibling) =>
            {
                return sibling.Width * 0.33;
            }),
            Constraint.RelativeToView(silverBoxView, (parent, sibling) =>
            {
                return sibling.Height * 0.33;
            }));

        Title = "RelativeLayout demo";
        Content = relativeLayout;
    }
}
```

在此示例中， [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) 使用 `Add` 需要 `Constraint` `x` 、 `y` 、 `width` 和 `height` 参数的可选对象的重载将子级添加到。

> [!NOTE]
> [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)使用相对值的可以定位子级并调整其大小，使其不适合布局边界内。

## <a name="related-links"></a>相关链接

- [RelativeLayout 演示 (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-relativelayoutdemos)
- [Xamarin.Forms 附加属性](~/xamarin-forms/xaml/attached-properties.md)
- [XAML 标记扩展](~/xamarin-forms/xaml/markup-extensions/index.md)
- [选择 Xamarin.Forms 布局](choose-layout.md)
- [提高 Xamarin.Forms 应用程序性能](~/xamarin-forms/deploy-test/performance.md)