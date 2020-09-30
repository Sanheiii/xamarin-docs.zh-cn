---
title: Xamarin.Forms 步进
description: Xamarin.Forms分档器允许用户从一系列值中选择一个数值。 它包含以减号和加号标记的两个按钮。 操作这两个按钮会以增量方式更改所选值。
ms.prod: xamarin
ms.assetid: 62571B3E-D84B-4F52-9FC7-C105D6733B16
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/17/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 390bca8cb74fd2da725724769956b164e0264173
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91558751"
---
# <a name="no-locxamarinforms-stepper"></a>Xamarin.Forms 步进

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos)

_使用分档器从一系列值中选择一个数值。_

Xamarin.Forms [`Stepper`](xref:Xamarin.Forms.Stepper) 包含标记有减号和加号的两个按钮。 用户可以操作这些按钮以增量方式 `double` 从一系列值中选择一个值。

[`Stepper`](xref:Xamarin.Forms.Stepper)定义类型的四个属性 `double` ：

- [`Increment`](xref:Xamarin.Forms.Stepper.Increment) 选定值的更改量，默认值为1。
- [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) 范围的最小值，默认值为0。
- [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) 范围的最大值，默认值为100。
- [`Value`](xref:Xamarin.Forms.Stepper.Value) 是分档器的值，它的范围可以介于和之间，其 `Minimum` `Maximum` 默认值为0。

所有这些属性都是由对象支持的 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 。 [`Value`](xref:Xamarin.Forms.Stepper.Value)属性的默认绑定模式为 [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) ，这意味着它适用于使用[模型-视图-ViewModel (MVVM) ](~/xamarin-forms/enterprise-application-patterns/mvvm.md)体系结构的应用程序中的绑定源。

> [!WARNING]
> 在内部， [`Stepper`](xref:Xamarin.Forms.Stepper) 确保 [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) 小于 [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) 。 如果 `Minimum` `Maximum` 已将或设置 `Minimum` 为不小于 `Maximum` ，则会引发异常。 有关设置和属性的详细 `Minimum` 信息 `Maximum` ，请参阅 [预防措施](#precautions) 部分。

将 [`Stepper`](xref:Xamarin.Forms.Stepper) 强制 [`Value`](xref:Xamarin.Forms.Stepper.Value) 属性，使其在 [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) 和 [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) （含）之间。 如果将 `Minimum` 属性设置为大于属性的值 `Value` ，则将 `Stepper` `Value` 属性设置为 `Minimum` 。 同样，如果 `Maximum` 将设置为小于的值 `Value` ，则 `Stepper` 将属性设置 `Value` 为 `Maximum` 。

[`Stepper`](xref:Xamarin.Forms.Stepper) 定义一个 [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) 事件，该事件 [`Value`](xref:Xamarin.Forms.Stepper.Value) 通过用户操作 `Stepper` 或在应用程序直接设置属性时被触发 `Value` 。 `ValueChanged`如果 `Value` 按上一段中所述强制属性，则还会触发事件。

[`ValueChangedEventArgs`](xref:Xamarin.Forms.ValueChangedEventArgs)事件附带的对象 [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) 具有两个属性 `double` ： [`OldValue`](xref:Xamarin.Forms.ValueChangedEventArgs.OldValue) 和 [`NewValue`](xref:Xamarin.Forms.ValueChangedEventArgs.NewValue) 。 触发事件时，的值 `NewValue` 将与 [`Value`](xref:Xamarin.Forms.Stepper.Value) 对象的属性相同 [`Stepper`](xref:Xamarin.Forms.Stepper) 。

## <a name="basic-stepper-code-and-markup"></a>基本分档器代码和标记

[**StepperDemos**](/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos)示例包含三个功能相同、但以不同方式实现的页面。 第一页仅使用 c # 代码，第二页使用 XAML 并在代码中使用事件处理程序，第三页可以通过在 XAML 文件中使用数据绑定来避免事件处理程序。

### <a name="creating-a-stepper-in-code"></a>在代码中创建分档器

[**StepperDemos**](/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos)示例中的**基本分档器代码**页演示了如何 [`Stepper`](xref:Xamarin.Forms.Stepper) 在代码中创建和两个 [`Label`](xref:Xamarin.Forms.Label) 对象：

```csharp
public class BasicStepperCodePage : ContentPage
{
    public BasicStepperCodePage()
    {
        Label rotationLabel = new Label
        {
            Text = "ROTATING TEXT",
            FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)),
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };

        Label displayLabel = new Label
        {
            Text = "(uninitialized)",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };

        Stepper stepper = new Stepper
        {
            Maximum = 360,
            Increment = 30,
            HorizontalOptions = LayoutOptions.Center
        };
        stepper.ValueChanged += (sender, e) =>
        {
            rotationLabel.Rotation = stepper.Value;
            displayLabel.Text = string.Format("The Stepper value is {0}", e.NewValue);
        };

        Title = "Basic Stepper Code";
        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children = { rotationLabel, stepper, displayLabel }
        };
    }
}
```

[`Stepper`](xref:Xamarin.Forms.Stepper)初始化为具有 [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) 360 的属性，并且属性为 [`Increment`](xref:Xamarin.Forms.Stepper.Increment) 30。 操作将 `Stepper` 基于属性的值将所选值更改为在之间递增 [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) `Maximum` `Increment` 。 的 [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) 处理程序 `Stepper` 使用对象的 [`Value`](xref:Xamarin.Forms.Stepper.Value) 属性 `stepper` 来设置 [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) 第一个的属性 [`Label`](xref:Xamarin.Forms.Label)  ，并将 `string.Format` 方法与 `NewValue` 事件参数的属性一起使用来设置 [`Text`](xref:Xamarin.Forms.Label.Text) 第二个属性 `Label` 。 获取的当前值的两种方法是可 `Stepper` 互换的。

以下屏幕截图显示了 **基本分档器代码** 页：

[![基本分档器代码](stepper-images/basic-stepper-code.png "基本分档器代码")](stepper-images/basic-stepper-code-large.png#lightbox)

第二个在 [`Label`](xref:Xamarin.Forms.Label)  操作之前显示文本 " (未初始化) " [`Stepper`](xref:Xamarin.Forms.Stepper) ，这将导致激发第一个 [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) 事件。

### <a name="creating-a-stepper-in-xaml"></a>在 XAML 中创建分档器

**基本分档器 XAML**页的功能与**基本分档器代码**相同，但主要在 XAML 中实现：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StepperDemo.BasicStepperXAMLPage"
             Title="Basic Stepper XAML">
    <StackLayout Margin="20">
        <Label x:Name="_rotatingLabel"
               Text="ROTATING TEXT"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
        <Stepper Maximum="360"
                 Increment="30"
                 HorizontalOptions="Center"
                 ValueChanged="OnStepperValueChanged" />
        <Label x:Name="_displayLabel"
               Text="(uninitialized)"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />        
    </StackLayout>
</ContentPage>
```

代码隐藏文件包含事件的处理程序 [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) ：

```csharp
public partial class BasicStepperXAMLPage : ContentPage
{
    public BasicStepperXAMLPage()
    {
        InitializeComponent();
    }

    void OnStepperValueChanged(object sender, ValueChangedEventArgs e)
    {
        double value = e.NewValue;
        _rotatingLabel.Rotation = value;
        _displayLabel.Text = string.Format("The Stepper value is {0}", value);
    }
}
```

事件处理程序也可以 [`Stepper`](xref:Xamarin.Forms.Stepper) 通过参数获取触发事件的 `sender` 。 [`Value`](xref:Xamarin.Forms.Stepper.Value)属性包含当前值：

```csharp
double value = ((Stepper)sender).Value;
```

如果在 [`Stepper`](xref:Xamarin.Forms.Stepper) XAML 文件中为该对象指定了一个具有属性的名称 `x:Name` (例如，"分档器" ) ，则事件处理程序可以直接引用该对象：

```csharp
double value = stepper.Value;
```

### <a name="data-binding-the-stepper"></a>数据绑定分档器

" **基本分档器绑定** " 页显示了如何 [`Value`](xref:Xamarin.Forms.Stepper.Value) 使用 [数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md)编写消除事件处理程序的几乎等效的应用程序：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StepperDemo.BasicStepperBindingsPage"
             Title="Basic Stepper Bindings">
    <StackLayout Margin="20">
        <Label Text="ROTATING TEXT"
               Rotation="{Binding Source={x:Reference _stepper}, Path=Value}"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
        <Stepper x:Name="_stepper"
                 Maximum="360"
                 Increment="30"
                 HorizontalOptions="Center" />
        <Label Text="{Binding Source={x:Reference _stepper}, Path=Value, StringFormat='The Stepper value is {0:F0}'}"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

[`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)第一个的属性 [`Label`](xref:Xamarin.Forms.Label) 将绑定到 [`Value`](xref:Xamarin.Forms.Stepper.Value) 的属性，这与 [`Stepper`](xref:Xamarin.Forms.Stepper) [`Text`](xref:Xamarin.Forms.Label.Text) 使用规范的第二个属性相同 `Label` `StringFormat` 。 **基本分档器绑定**页的工作方式与前两个页面的工作方式略有不同：首次显示页面时，第二个页面 `Label` 显示带有值的文本字符串。 这是使用数据绑定的一项好处。 若要在不进行数据绑定的情况下显示文本，则需要 `Text` `Label` [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) 通过从类构造函数中调用事件处理程序来专门初始化或模拟事件的激发。

## <a name="precautions"></a>注意事项

属性的值 [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) 必须始终小于属性的值 [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) 。 下面的代码段将导致 [`Stepper`](xref:Xamarin.Forms.Stepper) 引发异常：

```csharp
// Throws an exception!
Stepper stepper = new Stepper
{
    Minimum = 180,
    Maximum = 360
};
```

C # 编译器生成的代码按顺序设置这两个属性，当 [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) 属性设置为180时，它将大于默认 [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) 值100。 在这种情况下，你可以通过先设置属性来避免异常 `Maximum` ：

```csharp
Stepper stepper = new Stepper
{
    Maximum = 360,
    Minimum = 180
};
```

设置 [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) 为360不是问题，因为它大于默认 [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) 值0。 `Minimum`设置后，值小于 `Maximum` 360 的值。

XAML 中存在相同的问题。 按顺序设置属性，以确保 [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) 始终大于 `Minimum` ：

```xaml
<Stepper Maximum="360"
         Minimum="180" ... />
```

您可以将 [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) 和 [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) 值设置为负数，但只能按始终小于的顺序 `Minimum` `Maximum` ：

```xaml
<Stepper Minimum="-360"
         Maximum="-180" ... />
```

[`Value`](xref:Xamarin.Forms.Stepper.Value)属性始终大于或等于 [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) 值且小于或等于 [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) 。 如果将 `Value` 设置为超出该范围的值，则该值将被强制转换为范围内的，但不会引发异常。 例如，此代码 *不* 会引发异常：

```csharp
Stepper stepper = new Stepper
{
    Value = 180
};
```

相反，该 [`Value`](xref:Xamarin.Forms.Stepper.Value) 属性被强制为 [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) 100 的值。

下面是上面所示的代码段：

```csharp
Stepper stepper = new Stepper
{
    Maximum = 360,
    Minimum = 180
};
```

如果 [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) 设置为180，则 [`Value`](xref:Xamarin.Forms.Stepper.Value) 也将设置为180。

如果 [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) 在将 [`Value`](xref:Xamarin.Forms.Stepper.Value) 属性强制转换为默认值0以外的值时附加了事件处理程序，则 `ValueChanged` 会触发事件。 下面是 XAML 的代码段：

```xaml
<Stepper ValueChanged="OnStepperValueChanged"
         Maximum="360"
         Minimum="180" />
```

当 [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) 设置为180时， [`Value`](xref:Xamarin.Forms.Stepper.Value) 还将设置为180，并 [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) 触发事件。 这可能会在构造页的其余部分之前发生，并且处理程序可能会尝试引用尚未创建的页上的其他元素。 您可能想要向 `ValueChanged` 处理程序中添加一些代码，以检查 `null` 页上其他元素的值。 或者，您可以在 `ValueChanged` 初始化值后设置事件处理程序 [`Stepper`](xref:Xamarin.Forms.Stepper) 。

## <a name="related-links"></a>相关链接

- [分档器演示示例](/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos)
- [分档器 API](xref:Xamarin.Forms.Stepper)