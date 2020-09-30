---
title: Xamarin.Forms 滑动
description: Xamarin.Forms滚动条是一个水平条，用户可以对其进行操作，以从连续范围中选择双精度值。 本文介绍如何使用 Slider 类从连续值的范围中选择值。
ms.prod: xamarin
ms.assetid: 36B1C645-26E0-4874-B6B6-BDBF77662878
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/27/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4d53f999e27aa5aea852f09e8650ea66aac1d9b9
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91558825"
---
# <a name="no-locxamarinforms-slider"></a>Xamarin.Forms 滑动

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos)

_使用滑块从连续值的范围中进行选择。_

Xamarin.Forms [`Slider`](xref:Xamarin.Forms.Slider) 是一个水平条，用户可以操作它以 `double` 从连续范围中选择值。

`Slider`定义类型的三个属性 `double` ：

- [`Minimum`](xref:Xamarin.Forms.Slider.Minimum) 范围的最小值，默认值为0。
- [`Maximum`](xref:Xamarin.Forms.Slider.Maximum) 范围的最大值，默认值为1。
- [`Value`](xref:Xamarin.Forms.Slider.Value) 滑块的值，它的范围可以介于和之间， `Minimum` `Maximum` 并且默认值为0。

所有三个属性都由 `BindableProperty` 对象支持。 `Value`属性的默认绑定模式为 `BindingMode.TwoWay` ，这意味着它适用于使用[模型-视图-ViewModel (MVVM) ](~/xamarin-forms/enterprise-application-patterns/mvvm.md)体系结构的应用程序中的绑定源。

> [!WARNING]
> 在内部， `Slider` 确保 `Minimum` 小于 `Maximum` 。 如果 `Minimum` `Maximum` 已将或设置 `Minimum` 为不小于 `Maximum` ，则会引发异常。 有关设置和属性的详细信息，请参阅下面的 " [**注意事项**](#precautions) " 部分 `Minimum` `Maximum` 。

将 `Slider` 强制 `Value` 属性，使其在 `Minimum` 和 `Maximum` （含）之间。 如果将 `Minimum` 属性设置为大于属性的值 `Value` ，则将 `Slider` `Value` 属性设置为 `Minimum` 。 同样，如果 `Maximum` 将设置为小于的值 `Value` ，则 `Slider` 将属性设置 `Value` 为 `Maximum` 。

`Slider` 定义一个 [`ValueChanged`](xref:Xamarin.Forms.Slider.ValueChanged) 事件，该事件在 `Value` 通过用户操作 `Slider` 或在程序设置属性时被触发 `Value` 。 `ValueChanged`如果 `Value` 按上一段中所述强制属性，则还会触发事件。

[`ValueChangedEventArgs`](xref:Xamarin.Forms.ValueChangedEventArgs)事件附带的对象 `ValueChanged` 具有两个属性 `double` ： [`OldValue`](xref:Xamarin.Forms.ValueChangedEventArgs.OldValue) 和 [`NewValue`](xref:Xamarin.Forms.ValueChangedEventArgs.NewValue) 。 触发事件时，的值 `NewValue` 将与 `Value` 对象的属性相同 `Slider` 。

`Slider` 还定义 `DragStarted` 和 `DragCompleted` 事件，这些事件在拖动操作的开始和结束时激发。 与 [`ValueChanged`](xref:Xamarin.Forms.Slider.ValueChanged) 事件不同， `DragStarted` 和 `DragCompleted` 事件仅通过用户操作触发 `Slider` 。 当 `DragStarted` 事件触发时，将 `DragStartedCommand` 执行类型为的 `ICommand` 。 同样，当引发 `DragCompleted` 事件时，将 `DragCompletedCommand` 执行类型为的 `ICommand` 。

> [!WARNING]
> 不要 `Center` 对、或使用不受约束的水平布局选项 `Start` `End` `Slider` 。 在 Android 和 UWP 上，都 `Slider` 折叠为长度为零的一条，在 iOS 上，条形非常短。 保留的默认 `HorizontalOptions` 设置 `Fill` ， `Auto` `Slider` 在布局中时不要使用的宽度 `Grid` 。

`Slider`还定义了多个影响其外观的属性：

- [`MinimumTrackColor`](xref:Xamarin.Forms.Slider.MinimumTrackColorProperty) 是滚动块左侧的条形颜色。
- [`MaximumTrackColor`](xref:Xamarin.Forms.Slider.MaximumTrackColorProperty) 是滚动块右侧的条形颜色。
- [`ThumbColor`](xref:Xamarin.Forms.Slider.ThumbColorProperty) 为拇指颜色。
- [`ThumbImageSource`](xref:Xamarin.Forms.Slider.ThumbImageSourceProperty) 用于类型的缩略图的图像 [`ImageSource`](xref:Xamarin.Forms.ImageSource) 。

> [!NOTE]
> `ThumbColor`和 `ThumbImageSource` 属性互相排斥。 如果同时设置了这两个属性，则 `ThumbImageSource` 属性将优先。

## <a name="basic-slider-code-and-markup"></a>基本滑块代码和标记

[**SliderDemos**](/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos)示例从功能相同的三个页面开始，但以不同方式实现。 第一页仅使用 c # 代码，第二页使用 XAML 和代码中的事件处理程序，第三页可以通过在 XAML 文件中使用数据绑定来避免事件处理程序。

### <a name="creating-a-slider-in-code"></a>在代码中创建滑块

[**SliderDemos**](/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos)示例中的**基本滑块代码**页显示了如何 `Slider` 在代码中创建和两个 `Label` 对象：

```csharp
public class BasicSliderCodePage : ContentPage
{
    public BasicSliderCodePage()
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

        Slider slider = new Slider
        {
            Maximum = 360
        };
        slider.ValueChanged += (sender, args) =>
        {
            rotationLabel.Rotation = slider.Value;
            displayLabel.Text = String.Format("The Slider value is {0}", args.NewValue);
        };

        Title = "Basic Slider Code";
        Padding = new Thickness(10, 0);
        Content = new StackLayout
        {
            Children =
            {
                rotationLabel,
                slider,
                displayLabel
            }
        };
    }
}
```

`Slider`初始化为具有 `Maximum` 属性360。 的 `ValueChanged` 处理程序 `Slider` 使用对象的 `Value` 属性 `slider` 来设置 `Rotation` 第一个的属性 `Label` ，并将 `String.Format` 方法与 `NewValue` 事件参数的属性一起使用来设置 `Text` 第二个属性 `Label` 。 获取的当前值的两种方法是可 `Slider` 互换的。

下面是在 iOS 和 Android 设备上运行的程序：

[![基本滑块代码](slider-images/BasicSliderCode.png "基本滑块代码")](slider-images/BasicSliderCode-Large.png#lightbox)

第二个在 `Label` 操作之前显示文本 " (未初始化) " `Slider` ，这将导致激发第一个 `ValueChanged` 事件。 请注意，每个平台显示的小数位数各不相同。 这些差异与的平台实现相关 `Slider` ，并在本文后面的 [平台实现差异](#platform-implementation-differences)部分中进行了介绍。

### <a name="creating-a-slider-in-xaml"></a>在 XAML 中创建滑块

基本的 " **滑块" XAML** 页在功能上与 **基本滑块代码** 相同，但主要在 xaml 中实现：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SliderDemos.BasicSliderXamlPage"
             Title="Basic Slider XAML"
             Padding="10, 0">
    <StackLayout>
        <Label x:Name="rotatingLabel"
               Text="ROTATING TEXT"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider Maximum="360"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="displayLabel"
               Text="(uninitialized)"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

代码隐藏文件包含事件的处理程序 `ValueChanged` ：

```csharp
public partial class BasicSliderXamlPage : ContentPage
{
    public BasicSliderXamlPage()
    {
        InitializeComponent();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        double value = args.NewValue;
        rotatingLabel.Rotation = value;
        displayLabel.Text = String.Format("The Slider value is {0}", value);
    }
}
```

事件处理程序也可以 `Slider` 通过参数获取触发事件的 `sender` 。 `Value`属性包含当前值：

```csharp
double value = ((Slider)sender).Value;
```

如果在 `Slider` XAML 文件中为该对象指定了具有属性的名称 `x:Name` (例如，"slider" ) ，则事件处理程序可以直接引用该对象：

```csharp
double value = slider.Value;
```

### <a name="data-binding-the-slider"></a>数据绑定滑块

**基本滑块绑定**页面显示了如何编写一个几乎等效的程序，该程序 `Value` 通过使用[数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md)来消除事件处理程序：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SliderDemos.BasicSliderBindingsPage"
             Title="Basic Slider Bindings"
             Padding="10, 0">
    <StackLayout>
        <Label Text="ROTATING TEXT"
               Rotation="{Binding Source={x:Reference slider},
                                  Path=Value}"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                Maximum="360" />

        <Label x:Name="displayLabel"
               Text="{Binding Source={x:Reference slider},
                              Path=Value,
                              StringFormat='The Slider value is {0:F0}'}"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

`Rotation`第一个的属性 `Label` 将绑定到 `Value` 的属性，这与 `Slider` `Text` 使用规范的第二个属性相同 `Label` `StringFormat` 。 **基本滑块绑定**页面的工作方式与前两个页面的工作方式略有不同：首次显示页面时，第二个页面 `Label` 显示带有值的文本字符串。 这是使用数据绑定的一项好处。 若要在不进行数据绑定的情况下显示文本，则需要 `Text` `Label` `ValueChanged` 通过从类构造函数中调用事件处理程序来专门初始化或模拟事件的激发。

## <a name="precautions"></a>注意事项

属性的值 `Minimum` 必须始终小于属性的值 `Maximum` 。 下面的代码段将导致 `Slider` 引发异常：

```csharp
// Throws an exception!
Slider slider = new Slider
{
    Minimum = 10,
    Maximum = 20
};
```

C # 编译器生成的代码按顺序设置这两个属性，当 `Minimum` 属性设置为10时，它将大于默认 `Maximum` 值1。 在这种情况下，你可以通过先设置属性来避免异常 `Maximum` ：

```csharp
Slider slider = new Slider
{
    Maximum = 20,
    Minimum = 10
};
```

设置 `Maximum` 为20不是问题，因为它大于默认 `Minimum` 值0。 `Minimum`设置后，值小于 `Maximum` 20 的值。

XAML 中存在相同的问题。 按顺序设置属性，以确保 `Maximum` 始终大于 `Minimum` ：

```xaml
<Slider Maximum="20"
        Minimum="10" ... />
```

您可以将 `Minimum` 和 `Maximum` 值设置为负数，但只能按始终小于的顺序 `Minimum` `Maximum` ：

```xaml
<Slider Minimum="-20"
        Maximum="-10" ... />
```

`Value`属性始终大于或等于 `Minimum` 值且小于或等于 `Maximum` 。 如果将 `Value` 设置为超出该范围的值，则该值将被强制转换为范围内的，但不会引发异常。 例如，此代码 *不* 会引发异常：

```csharp
Slider slider = new Slider
{
    Value = 10
};
```

相反，该 `Value` 属性将强制为 `Maximum` 值1。

下面是上面所示的代码段：

```csharp
Slider slider = new Slider
{
    Maximum = 20,
    Minimum = 10
};
```

如果 `Minimum` 设置为10，则 `Value` 还将设置为10。

如果 `ValueChanged` 在将 `Value` 属性强制转换为默认值0以外的值时附加了事件处理程序，则 `ValueChanged` 会触发事件。 下面是 XAML 的代码段：

```xaml
<Slider ValueChanged="OnSliderValueChanged"
        Maximum="20"
        Minimum="10" />
```

当 `Minimum` 设置为10时， `Value` 还将设置为10，并 `ValueChanged` 触发事件。 这可能会在构造页的其余部分之前发生，并且处理程序可能会尝试引用尚未创建的页上的其他元素。 您可能想要向 `ValueChanged` 处理程序中添加一些代码，以检查 `null` 页上其他元素的值。 或者，您可以在 `ValueChanged` 初始化值后设置事件处理程序 `Slider` 。

## <a name="platform-implementation-differences"></a>平台实现差异

前面所示的屏幕截图显示的值的 `Slider` 小数位数不同。 这与在 `Slider` Android 和 UWP 平台上实现的方式相关。

### <a name="the-android-implementation"></a>Android 实现

的 Android 实现 `Slider` 基于 Android [`SeekBar`](xref:Android.Widget.SeekBar) 并始终将 [`Max`](xref:Android.Widget.ProgressBar.Max) 属性设置为1000。 这意味着 `Slider` Android 上只有1001个离散值。 如果将设置为0，将设置 `Slider` 为 `Minimum` `Maximum` 5000，则 `Slider` 操作时， `Value` 属性的值为0、5、10、15等。

### <a name="the-uwp-implementation"></a>UWP 实现

的 UWP 实现 `Slider` 基于 uwp [`Slider`](/uwp/api/windows.ui.xaml.controls.slider) 控件。 `StepFrequency`UWP 的属性 `Slider` 设置为的差 `Maximum` 和 `Minimum` 属性除以10，但不大于1。

例如，对于默认范围0到1，则将 `StepFrequency` 属性设置为0.1。 操作时 `Slider` ， `Value` 属性限制为0、0.1、0.2、0.3、0.4、0.5、0.6、0.7、0.8、0.9 和1.0。  (在 [**SliderDemos**](/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos) 示例的最后一页中可以看到这种情况。 ) 当和属性之间的差异 `Maximum` `Minimum` 为10或更大时，则 `StepFrequency` 将设置为1，并且 `Value` 属性具有整数值。

### <a name="the-stepslider-solution"></a>StepSlider 解决方案

第 `StepSlider` 27 章讨论了更为通用的[。](https://xamarin.azureedge.net/developer/xamarin-forms-book/XamarinFormsBook-Ch27-Apr2016.pdf)*创建移动应用 Xamarin.Forms *的书籍的自定义呈现器。 与 `StepSlider` 类似， `Slider` 但添加了 `Steps` 属性以指定与之间的值的数目 `Minimum` `Maximum` 。

## <a name="sliders-for-color-selection"></a>用于选择颜色的滑块

[**SliderDemos**](/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos)示例中的最后两个页面都使用三个 `Slider` 实例来选择颜色。 第一页处理代码隐藏文件中的所有交互，而第二页显示如何使用 ViewModel 的数据绑定。

### <a name="handling-sliders-in-the-code-behind-file"></a>代码隐藏文件中的处理滑块

" **RGB 颜色滑杆** " 页将实例化 `BoxView` 以显示颜色、三个 `Slider` 实例，以选择颜色的红色、绿色和蓝色分量，并使用三个 `Label` 元素来显示这些颜色值：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SliderDemos.RgbColorSlidersPage"
             Title="RGB Color Sliders">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Slider">
                <Setter Property="Maximum" Value="255" />
            </Style>

            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Margin="10">
        <BoxView x:Name="boxView"
                 Color="Black"
                 VerticalOptions="FillAndExpand" />

        <Slider x:Name="redSlider"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="redLabel" />

        <Slider x:Name="greenSlider"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="greenLabel" />

        <Slider x:Name="blueSlider"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="blueLabel" />
    </StackLayout>
</ContentPage>
```

A `Style` 提供 `Slider` 0 到255范围内的所有三个元素。 `Slider`元素共享同一 `ValueChanged` 处理程序，该处理程序在代码隐藏文件中实现：

```csharp
public partial class RgbColorSlidersPage : ContentPage
{
    public RgbColorSlidersPage()
    {
        InitializeComponent();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        if (sender == redSlider)
        {
            redLabel.Text = String.Format("Red = {0:X2}", (int)args.NewValue);
        }
        else if (sender == greenSlider)
        {
            greenLabel.Text = String.Format("Green = {0:X2}", (int)args.NewValue);
        }
        else if (sender == blueSlider)
        {
            blueLabel.Text = String.Format("Blue = {0:X2}", (int)args.NewValue);
        }

        boxView.Color = Color.FromRgb((int)redSlider.Value,
                                      (int)greenSlider.Value,
                                      (int)blueSlider.Value);
    }
}
```

第一部分将 `Text` 某个实例的属性设置 `Label` 为短文本字符串，指示的值（ `Slider` 十六进制）。 然后，将访问这三个 `Slider` 实例，以便 `Color` 从 RGB 组件创建值：

[![RGB 颜色滑块](slider-images/RgbColorSliders.png "RGB 颜色滑块")](slider-images/RgbColorSliders-Large.png#lightbox)

### <a name="binding-the-slider-to-a-viewmodel"></a>将滑块绑定到 ViewModel

" **HSL 颜色滑杆** " 页显示了如何使用 ViewModel 来执行用于 `Color` 从色相、饱和度和发光度值创建值的计算。 与所有 Viewmodel 一样， `HSLColorViewModel` 类实现 `INotifyPropertyChanged` 接口，并 `PropertyChanged` 在其中一个属性发生更改时触发事件：

```csharp
public class HslColorViewModel : INotifyPropertyChanged
{
    Color color;

    public event PropertyChangedEventHandler PropertyChanged;

    public double Hue
    {
        set
        {
            if (color.Hue != value)
            {
                Color = Color.FromHsla(value, color.Saturation, color.Luminosity);
            }
        }
        get
        {
            return color.Hue;
        }
    }

    public double Saturation
    {
        set
        {
            if (color.Saturation != value)
            {
                Color = Color.FromHsla(color.Hue, value, color.Luminosity);
            }
        }
        get
        {
            return color.Saturation;
        }
    }

    public double Luminosity
    {
        set
        {
            if (color.Luminosity != value)
            {
                Color = Color.FromHsla(color.Hue, color.Saturation, value);
            }
        }
        get
        {
            return color.Luminosity;
        }
    }

    public Color Color
    {
        set
        {
            if (color != value)
            {
                color = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Hue"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Saturation"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Luminosity"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Color"));
            }
        }
        get
        {
            return color;
        }
    }
}
```

`INotifyPropertyChanged`[数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md)一文中讨论了 viewmodel 和接口。

**HslColorSlidersPage**文件实例化 `HslColorViewModel` 并将其设置为页的 `BindingContext` 属性。 这允许 XAML 文件中的所有元素绑定到 ViewModel 中的属性：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:SliderDemos"
             x:Class="SliderDemos.HslColorSlidersPage"
             Title="HSL Color Sliders">

    <ContentPage.BindingContext>
        <local:HslColorViewModel Color="Chocolate" />
    </ContentPage.BindingContext>

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Margin="10">
        <BoxView Color="{Binding Color}"
                 VerticalOptions="FillAndExpand" />

        <Slider Value="{Binding Hue}" />
        <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}" />

        <Slider Value="{Binding Saturation}" />
        <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}" />

        <Slider Value="{Binding Luminosity}" />
        <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}" />
    </StackLayout>
</ContentPage>
```

在 `Slider` 操作元素时，将 `BoxView` `Label` 从 ViewModel 中更新和元素：

[![HSL 颜色滑块](slider-images/HslColorSliders.png "HSL 颜色滑块")](slider-images/HslColorSliders-Large.png#lightbox)

`StringFormat`标记扩展的组件将 `Binding` 设置为 "F2" 格式，以显示两个小数位。 文章 [字符串格式设置](~/xamarin-forms/app-fundamentals/data-binding/string-formatting.md)中讨论了数据绑定 (字符串格式设置。 ) 不过，该程序的 UWP 版本限制为值0、0.1、0.2、.。。0.9 和1.0。 这是 UWP 实现的直接结果， `Slider` 如上述 [平台实现差异](#platform-implementation-differences)部分所述。

## <a name="related-links"></a>相关链接

- [滑块演示示例](/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos)
- [滑块 API](xref:Xamarin.Forms.Slider)