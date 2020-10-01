---
title: Xamarin.Forms 相对绑定
description: 本文介绍如何通过使用 RelativeSource 标记扩展设置相对于绑定目标位的绑定源来创建相对绑定。
ms.prod: xamarin
ms.assetid: CC64BB1D-8303-46B1-94B6-4EF2F20317A8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/04/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 24fa816519b05b46c38015fa602a4e70fbc07d16
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91560385"
---
# <a name="no-locxamarinforms-relative-bindings"></a>Xamarin.Forms 相对绑定

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

利用相对绑定，可以设置相对于绑定目标位置的绑定源。 它们使用 `RelativeSource` 标记扩展创建，并设置为绑定表达式的 `Source` 属性。

`RelativeSourceExtension` 类支持 `RelativeSource` 标记扩展，并定义以下属性：

- `Mode`，属于 `RelativeBindingSourceMode` 类型，描述相对于绑定目标位置的绑定源位置。
- `AncestorLevel`，属于 `int` 类型，这是要查找的可选上级级别（如果 `Mode` 属性为 `FindAncestor`）。 `n` 的 `AncestorLevel` 跳过 `AncestorType` 的 `n-1` 个实例。
- `AncestorType`，属于 `Type` 类型，这是要查找的上级类型（如果 `Mode` 属性为 `FindAncestor`）。

> [!NOTE]
> XAML 分析程序允许将 `RelativeSourceExtension` 类缩写为 `RelativeSource`。

`Mode` 属性应设置为 `RelativeBindingSourceMode` 枚举成员之一：

- `TemplatedParent` 指示应用了存在绑定元素的模板的元素。 有关详细信息，请参阅[绑定到模板化父级](#bind-to-a-templated-parent)。
- `Self` 指示正在其上设置绑定的元素，并允许你将该元素的一个属性绑定到同一元素的其他属性上。 有关详细信息，请参阅[绑定到自身](#bind-to-self)。
- `FindAncestor` 指示绑定元素的可视化树中的上级。 此模式应该用于绑定到由 `AncestorType` 属性表示的上级控件。 有关详细信息，请参阅[绑定到上级](#bind-to-an-ancestor)。
- `FindAncestorBindingContext` 指示绑定元素可视化树中上级的 `BindingContext`。 此模式应该用于绑定到由 `AncestorType` 属性表示的上级的 `BindingContext`。 有关详细信息，请参阅[绑定到上级](#bind-to-an-ancestor)。

`Mode` 属性是 `RelativeSourceExtension` 的内容属性。 因此，对于用大括号表示的 XAML 标记表达式，可以删除这类表达式的 `Mode=` 部分。

有关 Xamarin.Forms 标记扩展的详细信息，请参阅 [XAML 标记扩展](~/xamarin-forms/xaml/markup-extensions/index.md)。

## <a name="bind-to-self"></a>绑定到自身

`Self` 相对绑定模式用于将某一元素的属性绑定到该元素的其他属性：

```xaml
<BoxView Color="Red"
         WidthRequest="200"
         HeightRequest="{Binding Source={RelativeSource Self}, Path=WidthRequest}"
         HorizontalOptions="Center" />
```

在此示例中，[`BoxView`](xref:Xamarin.Forms.BoxView) 将其 `WidthRequest` 属性设置为固定大小，并将 `HeightRequest` 属性绑定到 `WidthRequest` 属性。 因此，这两个属性都相等，并绘制正方形：

[![iOS 和 Android 上的 Self 模式相对绑定的屏幕截图](relative-bindings-images/self-relative-binding.png "Self 相对绑定模式")](relative-bindings-images/self-relative-binding-large.png#lightbox "Self 相对绑定模式")

> [!IMPORTANT]
> 将某一元素的属性绑定到该元素的其他属性时，这些属性必须是相同的类型。 此外，也可以在绑定上指定转换器以转换值。

此绑定模式的常见用法是将对象的 `BindingContext` 设置为其自身的属性。 下面的代码显示了此用法的示例：

```xaml
<ContentPage ...
             BindingContext="{Binding Source={RelativeSource Self}, Path=DefaultViewModel}">
    <StackLayout>
        <ListView ItemsSource="{Binding Employees}">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

在此示例中，页面的 `BindingContext` 设置为其自身的 `DefaultViewModel` 属性。 此属性在页面的代码隐藏文件中进行定义，并提供 viewmodel 实例。 [`ListView`](xref:Xamarin.Forms.ListView) 绑定到 viewmodel 的 `Employees` 属性。

## <a name="bind-to-an-ancestor"></a>绑定到上级

`FindAncestor` 和 `FindAncestorBindingContext` 相对绑定模式用于绑定到可视化树中特定类型的父元素。 `FindAncestor` 模式用于绑定到父元素，父元素派生自 [`Element`](xref:Xamarin.Forms.Element) 类型。 `FindAncestorBindingContext` 模式用于绑定到父元素的 `BindingContext`。

> [!WARNING]
> 使用 `FindAncestor` 和 `FindAncestorBindingContext` 相对绑定模式时，必须将 `AncestorType` 属性设置为 `Type`，否则将引发 `XamlParseException`。

如果未显式设置 `Mode` 属性，在将 `AncestorType` 属性设置为派生自 [`Element`](xref:Xamarin.Forms.Element) 的类型时，会将 `Mode` 属性隐式设置为 `FindAncestor`。 同样，如果将 `AncestorType` 属性设置为不是派生自 `Element` 的类型，则会将 `Mode` 属性隐式设置为 `FindAncestorBindingContext`。

> [!NOTE]
> 当所有上级的 `BindingContext` 发生变化时，将重新应用使用 `FindAncestorBindingContext` 模式的相对绑定。

下面的 XAML 演示了一个示例，其中 `Mode` 属性将被隐式设置为 `FindAncestorBindingContext`：

```xaml
<ContentPage ...
             BindingContext="{Binding Source={RelativeSource Self}, Path=DefaultViewModel}">
    <StackLayout>
        <ListView ItemsSource="{Binding Employees}">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell>
                        <StackLayout Orientation="Horizontal">
                            <Label Text="{Binding Fullname}"
                                   VerticalOptions="Center" />
                            <Button Text="Delete"
                                    Command="{Binding Source={RelativeSource AncestorType={x:Type local:PeopleViewModel}}, Path=DeleteEmployeeCommand}"
                                    CommandParameter="{Binding}"
                                    HorizontalOptions="EndAndExpand" />
                        </StackLayout>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

在此示例中，页面的 `BindingContext` 设置为其自身的 `DefaultViewModel` 属性。 此属性在页面的代码隐藏文件中进行定义，并提供 viewmodel 实例。 [`ListView`](xref:Xamarin.Forms.ListView) 绑定到 viewmodel 的 `Employees` 属性。 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 定义 `ListView` 中各个项的外观，它包含 [`Button`](xref:Xamarin.Forms.Button)。 按钮的 `Command` 属性绑定到其父级 viewmodel 中的 `DeleteEmployeeCommand`。 点击 `Button` 会删除员工：

[![iOS 和 Android 上的 FindAncestor 模式相对绑定的屏幕截图](relative-bindings-images/findancestor-relative-binding.png "FindAncestor 相对绑定模式")](relative-bindings-images/findancestor-relative-binding-large.png#lightbox "FindAncestor 相对绑定模式")

此外，当可视化树中可能存在多个该类型的上级时，可以使用可选属性 `AncestorLevel` 帮助消除上级查找的歧义：

```xaml
<Label Text="{Binding Source={RelativeSource AncestorType={x:Type Entry}, AncestorLevel=2}, Path=Text}" />
```

在本例中，`Label.Text` 属性绑定到上行路径上从绑定的目标元素开始，遇到的第二个 [`Entry`](xref:Xamarin.Forms.Entry) 的 `Text` 属性，。

> [!NOTE]
> `AncestorLevel` 属性应设置为 1，以查找最靠近绑定目标元素的上级。

## <a name="bind-to-a-templated-parent"></a>绑定到模板化父级

`TemplatedParent` 相对绑定模式用于从控件模板内绑定到应用该模板的运行时对象实例（称为模板化父级）。 仅当相对绑定在控件模板中时，此模式才适用，并且与设置 `TemplateBinding` 类似。

以下 XAML 显示 `TemplatedParent` 相对绑定模式的一个示例：

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <ControlTemplate x:Key="CardViewControlTemplate">
            <Frame BindingContext="{Binding Source={RelativeSource TemplatedParent}}"
                   BackgroundColor="{Binding CardColor}"
                   BorderColor="{Binding BorderColor}"
                   ...>
                <Grid>
                    ...
                    <Label Text="{Binding CardTitle}"
                           ... />
                    <BoxView BackgroundColor="{Binding BorderColor}"
                             ... />
                    <Label Text="{Binding CardDescription}"
                           ... />
                </Grid>
            </Frame>
        </ControlTemplate>
    </ContentPage.Resources>
    <StackLayout>        
        <controls:CardView BorderColor="DarkGray"
                           CardTitle="John Doe"
                           CardDescription="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla elit dolor, convallis non interdum."
                           IconBackgroundColor="SlateGray"
                           IconImageSource="user.png"
                           ControlTemplate="{StaticResource CardViewControlTemplate}" />
        <controls:CardView BorderColor="DarkGray"
                           CardTitle="Jane Doe"
                           CardDescription="Phasellus eu convallis mi. In tempus augue eu dignissim fermentum. Morbi ut lacus vitae eros lacinia."
                           IconBackgroundColor="SlateGray"
                           IconImageSource="user.png"
                           ControlTemplate="{StaticResource CardViewControlTemplate}" />
        <controls:CardView BorderColor="DarkGray"
                           CardTitle="Xamarin Monkey"
                           CardDescription="Aliquam sagittis, odio lacinia fermentum dictum, mi erat scelerisque erat, quis aliquet arcu."
                           IconBackgroundColor="SlateGray"
                           IconImageSource="user.png"
                           ControlTemplate="{StaticResource CardViewControlTemplate}" />
    </StackLayout>
</ContentPage>
```

在此示例中，[`Frame`](xref:Xamarin.Forms.Frame)（`ControlTemplate` 的根元素）将其 `BindingContext` 设置为应用了该模板的运行时对象实例。 因此，`Frame` 及其子级针对每个 `CardView` 对象的属性解析其绑定表达式：

[![iOS 和 Android 上的 TemplatedParent 模式相对绑定的屏幕截图](relative-bindings-images/templatedparent-relative-binding.png "TemplatedParent 相对绑定模式")](relative-bindings-images/templatedparent-relative-binding-large.png#lightbox "TemplatedParent 相对绑定模式")

有关控件模板的详细信息，请参阅 [Xamarin.Forms 控件模板](~/xamarin-forms/app-fundamentals/templates/control-template.md)。

## <a name="related-links"></a>相关链接

- [数据绑定演示（示例）](/samples/xamarin/xamarin-forms-samples/databindingdemos)
- [XAML 标记扩展](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Xamarin.Forms 控件模板](~/xamarin-forms/app-fundamentals/templates/control-template.md)