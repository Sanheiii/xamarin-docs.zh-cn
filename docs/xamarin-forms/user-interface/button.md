---
title: Xamarin.Forms 鼠标
description: 该按钮将响应一个点击或单击，指示应用程序执行特定任务。
ms.prod: xamarin
ms.assetid: 62CAEB63-0800-44F4-9B8C-EE632138C2F5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/21/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: fba8dcb344f79c21815a58cff0d8132967381cca
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556732"
---
# <a name="no-locxamarinforms-button"></a>Xamarin.Forms 鼠标

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos)

_该按钮将响应一个点击或单击，指示应用程序执行特定任务。_

[`Button`](xref:Xamarin.Forms.Button)是中的最基本的交互式控件 Xamarin.Forms 。 `Button`通常会显示一个表示命令的短文本字符串，但它还可以显示位图图像或文本和图像的组合。 用户按下 `Button` 手指或用鼠标单击来启动该命令。

下面讨论的大多数主题对应于 [**system.windows.forms.datagridview.buttondemos**](/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos) 示例中的页面。

## <a name="handling-button-clicks"></a>处理按钮单击

`Button` 定义在 [`Clicked`](xref:Xamarin.Forms.Button.Clicked) 用户 `Button` 使用手指或鼠标指针点击时触发的事件。 当从的图面释放手指或鼠标按钮时，会触发事件 `Button` 。 `Button`必须将其 [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) 属性设置为，以 `true` 使其响应点击。

[**System.windows.forms.datagridview.buttondemos**](/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos)示例中的 "**基本" 按钮单击**页演示了如何 `Button` 在 XAML 中实例化并处理其 `Clicked` 事件。 **BasicButtonClickPage**文件包含同时包含和的 `StackLayout` `Label` `Button` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ButtonDemos.BasicButtonClickPage"
             Title="Basic Button Click">
    <StackLayout>

        <Label x:Name="label"
               Text="Click the Button below"
               FontSize="Large"
               VerticalOptions="CenterAndExpand"
               HorizontalOptions="Center" />

        <Button Text="Click to Rotate Text!"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                Clicked="OnButtonClicked" />

    </StackLayout>
</ContentPage>
```

`Button`这往往会占用允许的所有空间。 例如，如果未将的属性设置 `HorizontalOptions` `Button` 为 `Fill` ，则 `Button` 将占用其父项的完整宽度。

默认情况下， `Button` 是矩形，但你可以通过使用属性为其指定圆角 [`CornerRadius`](xref:Xamarin.Forms.Button.CornerRadius) ，如下面的 "部分 [**按钮外观**](#button-appearance)" 中所述。

[`Text`](xref:Xamarin.Forms.Button.Text)属性指定在中显示的文本 `Button` 。 [`Clicked`](xref:Xamarin.Forms.Button.Clicked)事件设置为名为的事件处理程序 `OnButtonClicked` 。 此处理程序位于代码隐藏文件 **BasicButtonClickPage.xaml.cs**中：

```csharp
public partial class BasicButtonClickPage : ContentPage
{
    public BasicButtonClickPage ()
    {
        InitializeComponent ();
    }

    async void OnButtonClicked(object sender, EventArgs args)
    {
        await label.RelRotateTo(360, 1000);
    }
}
```

点击 `Button` 时，将执行 `OnButtonClicked` 方法。 `sender`参数是 `Button` 负责此事件的对象。 您可以使用它来访问 `Button` 对象，或区分 `Button` 共享同一事件的多个对象 `Clicked` 。

此特定 `Clicked` 处理程序将调用一个动画函数，该函数将 `Label` 360 度旋转1000毫秒。 下面是在 iOS 和 Android 设备上运行的程序，以及在 Windows 10 桌面上 (UWP) 应用程序的通用 Windows 平台：

[![基本按钮单击](button-images/BasicButtonClick.png "基本按钮单击")](button-images/BasicButtonClick-Large.png#lightbox "基本按钮单击")

请注意， `OnButtonClicked` 方法包含 `async` 修饰符，因为在 `await` 事件处理程序中使用。 `Clicked` `async` 仅当处理程序的主体使用时，事件处理程序才需要修饰符 `await` 。

每个平台 `Button` 以其自己的特定方式呈现。 在 " [**按钮外观**](#button-appearance) " 部分中，你将了解如何设置颜色并使 `Button` 边框对于更自定义的外观可见。 `Button` 实现 [`IFontElement`](xref:Xamarin.Forms.Internals.IFontElement) 接口，因此它包含 [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily) 、 [`FontSize`](xref:Xamarin.Forms.Button.FontSize) 和 [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes) 属性。

## <a name="creating-a-button-in-code"></a>在代码中创建按钮

通常 `Button` 在 XAML 中实例化，但也可以 `Button` 在代码中创建。 当应用程序需要基于使用循环枚举的数据创建多个按钮时，这可能很方便 `foreach` 。

" **代码按钮" 单击** "页面" 演示了如何创建一个在功能上等同于 **基本按钮单击** 页的页面，而是完全使用 c #：

```csharp
public class CodeButtonClickPage : ContentPage
{
    public CodeButtonClickPage ()
    {
        Title = "Code Button Click";

        Label label = new Label
        {
            Text = "Click the Button below",
            FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)),
            VerticalOptions = LayoutOptions.CenterAndExpand,
            HorizontalOptions = LayoutOptions.Center
        };

        Button button = new Button
        {
            Text = "Click to Rotate Text!",
            VerticalOptions = LayoutOptions.CenterAndExpand,
            HorizontalOptions = LayoutOptions.Center
        };
        button.Clicked += async (sender, args) => await label.RelRotateTo(360, 1000);

        Content = new StackLayout
        {
            Children =
            {
                label,
                button
            }
        };
    }
}
```

所有内容都在类的构造函数中完成。 由于 `Clicked` 处理程序的长度仅为一条语句，因此可以将它附加到事件中：

```csharp
button.Clicked += async (sender, args) => await label.RelRotateTo(360, 1000);
```

当然，您还可以将事件处理程序定义为单独的方法 (如同 " `OnButtonClick` **基本" 按钮** 中的方法一样单击) 并将该方法附加到事件：

```csharp
button.Clicked += OnButtonClicked;
```

## <a name="disabling-the-button"></a>禁用按钮

有时，应用程序处于特定的状态，即特定的 `Button` 单击不是有效的操作。 在这些情况下， `Button` 应通过将其属性设置 `IsEnabled` 为来禁用 `false` 。 典型示例是 `Entry` 带有文件打开的文件名的控件 `Button` ： `Button` 仅当已将某些文本键入到中时，才应启用 `Entry` 。
可以将 `DataTrigger` 用于此任务，如 [**数据触发器**](~/xamarin-forms/app-fundamentals/triggers.md#data-triggers) 一文中所示。

## <a name="using-the-command-interface"></a>使用命令界面

应用程序可以响应 `Button` 点击，而不会处理 `Clicked` 事件。 `Button`实现一个替代的通知机制，称为_命令_或_commanding_命令性接口。 这包括两个属性：

- [`Command`](xref:Xamarin.Forms.Button.Command) 类型为的 [`ICommand`](xref:System.Windows.Input.ICommand) ，它是在命名空间中定义的接口 [`System.Windows.Input`](xref:System.Windows.Input) 。
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) 类型的属性 [`Object`](xref:System.Object) 。

此方法特别适用于与数据绑定的连接，尤其是在实现模型-视图-ViewModel (MVVM) 体系结构时。 这些主题将在 " [数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md)"、" [从数据绑定到 mvvm](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)" 和 " [mvvm](~/xamarin-forms/enterprise-application-patterns/mvvm.md)" 中进行讨论。

在 MVVM 应用程序中，viewmodel 定义类型的属性 `ICommand` ，这些属性随后 `Button` 使用数据绑定连接到 XAML 元素。 Xamarin.Forms 还定义了 [`Command`](xref:Xamarin.Forms.Command) 和 [`Command<T>`](xref:Xamarin.Forms.Command`1) 类来实现 `ICommand` 接口，并协助 viewmodel 定义类型的属性 `ICommand` 。

[**命令界面**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md)一文中更详细地介绍了命令，但[**system.windows.forms.datagridview.buttondemos**](/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos)示例中的 "**基本" 按钮命令**页显示了基本方法。

`CommandDemoViewModel`类是一个非常简单的 viewmodel，它定义名为的类型的属性 `double` `Number` ，以及 `ICommand` 名为和的类型为的两个属性 `MultiplyBy2Command` `DivideBy2Command` ：

```csharp
class CommandDemoViewModel : INotifyPropertyChanged
{
    double number = 1;

    public event PropertyChangedEventHandler PropertyChanged;

    public CommandDemoViewModel()
    {
        MultiplyBy2Command = new Command(() => Number *= 2);

        DivideBy2Command = new Command(() => Number /= 2);
    }

    public double Number
    {
        set
        {
            if (number != value)
            {
                number = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Number"));
            }
        }
        get
        {
            return number;
        }
    }

    public ICommand MultiplyBy2Command { private set; get; }

    public ICommand DivideBy2Command { private set; get; }
}
```

这两个 `ICommand` 属性在类的构造函数中用两个类型的对象进行初始化 `Command` 。 `Command`构造函数包含一个小函数 (称为 `execute` 构造函数参数，) 为属性加倍或半部分 `Number` 。

**BasicButtonCommand**文件将其设置为的 `BindingContext` 实例 `CommandDemoViewModel` 。 `Label`元素和两个 `Button` 元素包含对中的三个属性的绑定 `CommandDemoViewModel` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ButtonDemos"
             x:Class="ButtonDemos.BasicButtonCommandPage"
             Title="Basic Button Command">

    <ContentPage.BindingContext>
        <local:CommandDemoViewModel />
    </ContentPage.BindingContext>

    <StackLayout>
        <Label Text="{Binding Number, StringFormat='Value is now {0}'}"
               FontSize="Large"
               VerticalOptions="CenterAndExpand"
               HorizontalOptions="Center" />

        <Button Text="Multiply by 2"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                Command="{Binding MultiplyBy2Command}" />

        <Button Text="Divide by 2"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                Command="{Binding DivideBy2Command}" />
    </StackLayout>
</ContentPage>
```

当点击两个 `Button` 元素时，将执行命令，数值将更改：

[![基本按钮命令](button-images/BasicButtonCommand.png "基本按钮命令")](button-images/BasicButtonCommand-Large.png#lightbox)

此方法对处理程序的优势在于， `Clicked` 涉及此页功能的所有逻辑都位于 viewmodel 中，而不是位于代码隐藏文件中，可更好地将用户界面与业务逻辑分离。

对象也可以 `Command` 控制元素的启用和禁用 `Button` 。 例如，假设您想要限制 2<sup>10</sup>到 2<sup> &ndash; 10</sup>之间的数字值的范围。 您可以向构造函数添加另一个函数 (称为 `canExecute` 参数) `true` 如果应启用此参数，则返回 `Button` 。 下面是对 `CommandDemoViewModel` 构造函数的修改：

```csharp
class CommandDemoViewModel : INotifyPropertyChanged
{
    ···
    public CommandDemoViewModel()
    {
        MultiplyBy2Command = new Command(
            execute: () =>
            {
                Number *= 2;
                ((Command)MultiplyBy2Command).ChangeCanExecute();
                ((Command)DivideBy2Command).ChangeCanExecute();
            },
            canExecute: () => Number < Math.Pow(2, 10));

        DivideBy2Command = new Command(
            execute: () =>
            {
                Number /= 2;
                ((Command)MultiplyBy2Command).ChangeCanExecute();
                ((Command)DivideBy2Command).ChangeCanExecute();
            },
            canExecute: () => Number > Math.Pow(2, -10));
    }
    ···
}
```

对的方法的调用 `ChangeCanExecute` `Command` 是必需的，以便此 `Command` 方法可以调用 `canExecute` 方法并确定是否 `Button` 应禁用。 在此代码更改的情况下，如果数字达到限制，将 `Button` 禁用：

[![基本按钮命令-已修改](button-images/BasicButtonCommandModified.png "基本按钮命令-已修改")](button-images/BasicButtonCommandModified-Large.png#lightbox)

两个或多个元素可以 `Button` 绑定到同一个 `ICommand` 属性。 `Button`可以使用的属性来区分元素 [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) `Button` 。 在这种情况下，你将需要使用泛型 [`Command<T>`](xref:Xamarin.Forms.Command`1) 类。 然后，将 `CommandParameter` 对象作为参数传递给 `execute` 和 `canExecute` 方法。 [**命令界面**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding)一文的[**基本**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding)命令部分中详细显示了此方法。

[**System.windows.forms.datagridview.buttondemos**](/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos)示例还在其类中使用了此方法 `MainPage` 。 **MainPage**文件包含 `Button` 示例的每一页的：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ButtonDemos"
             x:Class="ButtonDemos.MainPage"
             Title="Button Demos">
    <ScrollView>
        <FlexLayout Direction="Column"
                    JustifyContent="SpaceEvenly"
                    AlignItems="Center">

            <Button Text="Basic Button Click"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:BasicButtonClickPage}" />

            <Button Text="Code Button Click"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:CodeButtonClickPage}" />

            <Button Text="Basic Button Command"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:BasicButtonCommandPage}" />

            <Button Text="Press and Release Button"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:PressAndReleaseButtonPage}" />

            <Button Text="Button Appearance"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:ButtonAppearancePage}" />

            <Button Text="Toggle Button Demo"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:ToggleButtonDemoPage}" />

            <Button Text="Image Button Demo"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:ImageButtonDemoPage}" />

        </FlexLayout>
    </ScrollView>
</ContentPage>
```

每个 `Button` 属性都将其 `Command` 属性绑定到名为的属性 `NavigateCommand` ，并将 `CommandParameter` 设置为与 [`Type`](xref:System.Type) 项目中的某个页面类对应的对象。

该 `NavigateCommand` 属性的类型为 `ICommand` ，并且在代码隐藏文件中定义：

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();

        NavigateCommand = new Command<Type>(async (Type pageType) =>
        {
            Page page = (Page)Activator.CreateInstance(pageType);
            await Navigation.PushAsync(page);
        });

        BindingContext = this;
    }

    public ICommand NavigateCommand { private set; get; }
}
```

构造函数将 `NavigateCommand` 属性初始化为 `Command<Type>` 对象，因为 `Type` 是 `CommandParameter` XAML 文件中的对象集的类型。 这意味着该 `execute` 方法具有 `Type` 与此对象对应的类型的自变量 `CommandParameter` 。 函数实例化页面，然后导航到该页面。

请注意，构造函数通过将其设置 `BindingContext` 为自身来结束。 这对于 XAML 文件中的属性绑定到属性是必需的 `NavigateCommand` 。

## <a name="pressing-and-releasing-the-button"></a>按下并释放按钮

除 `Clicked` 事件外， `Button` 还定义 [`Pressed`](xref:Xamarin.Forms.Button.Pressed) 和 [`Released`](xref:Xamarin.Forms.Button.Released) 事件。 `Pressed`当手指按下时 `Button` ，或在鼠标指针位于上并按下鼠标按钮时发生该事件 `Button` 。 `Released`当鼠标悬停或鼠标按钮时，发生此事件。 通常情况下，事件 `Clicked` 的同时还会触发事件 `Released` ，但如果手指或鼠标指针在被释放之前从表面上滑离 `Button` ，则 `Clicked` 事件可能不会发生。

`Pressed`和 `Released` 事件通常不会使用，但它们可用于特殊目的，如 "**按下发布" 按钮**页中所示。 XAML 文件包含一个和一个，并为 `Label` `Button` 和事件附加了一个处理程序 `Pressed` `Released` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ButtonDemos.PressAndReleaseButtonPage"
             Title="Press and Release Button">
    <StackLayout>

        <Label x:Name="label"
               Text="Press and hold the Button below"
               FontSize="Large"
               VerticalOptions="CenterAndExpand"
               HorizontalOptions="Center" />

        <Button Text="Press to Rotate Text!"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                Pressed="OnButtonPressed"
                Released="OnButtonReleased" />

    </StackLayout>
</ContentPage>
```

当事件发生时，代码隐藏文件会将动画处理到 `Label` `Pressed` ，但在发生事件时挂起旋转 `Released` ：

```csharp
public partial class PressAndReleaseButtonPage : ContentPage
{
    bool animationInProgress = false;
    Stopwatch stopwatch = new Stopwatch();

    public PressAndReleaseButtonPage ()
    {
        InitializeComponent ();
    }

    void OnButtonPressed(object sender, EventArgs args)
    {
        stopwatch.Start();
        animationInProgress = true;

        Device.StartTimer(TimeSpan.FromMilliseconds(16), () =>
        {
            label.Rotation = 360 * (stopwatch.Elapsed.TotalSeconds % 1);

            return animationInProgress;
        });
    }

    void OnButtonReleased(object sender, EventArgs args)
    {
        animationInProgress = false;
        stopwatch.Stop();
    }
}
```

结果是， `Label` 仅当手指与联系时才旋转 `Button` ，并在该手指释放时停止：

[![按下并松开按钮](button-images/PressAndReleaseButton.png "按下并松开按钮")](button-images/PressAndReleaseButton-Large.png)

这种行为具有适用于游戏的应用程序：保存在上的手指 `Button` 可能使屏幕上的对象按特定方向移动。

## <a name="button-appearance"></a>按钮外观

`Button`继承或定义多个影响其外观的属性：

- [`TextColor`](xref:Xamarin.Forms.Button.TextColor)文本的颜色 `Button`
- [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) 文本的背景色。
- [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor) 围绕的区域的颜色 `Button`
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily) 用于文本的字体系列
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize) 文本的大小
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes) 指示文本是斜体还是粗体
- [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth) 边框的宽度
- [`CornerRadius`](xref:Xamarin.Forms.Button.CornerRadius) 是的圆角半径 `Button`
- [`CharacterSpacing`](xref:Xamarin.Forms.Button.CharacterSpacing) 文本的字符间距 `Button` 。
- `TextTransform` 确定文本的大小写 `Button` 。

> [!NOTE]
> `Button`类还具有 [`Margin`](xref:Xamarin.Forms.View.Margin) 控制的 [`Padding`](xref:Xamarin.Forms.Button.Padding) 布局行为的和属性 `Button` 。 有关详细信息，请参阅[边距和填充](~/xamarin-forms/user-interface/layouts/margin-and-padding.md)。

其中六个属性的效果 (排除 `FontFamily` 和 `FontAttributes`) 在 **按钮外观** 页中进行了演示。 另一个属性， [`Image`](xref:Xamarin.Forms.Button.ImageSource) 将在 " [**使用位图" 按钮**](#using-bitmaps-with-buttons)部分中进行讨论。

" **按钮外观** " 页中的所有视图和数据绑定都在 XAML 文件中定义：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ButtonDemos"
             x:Class="ButtonDemos.ButtonAppearancePage"
             Title="Button Appearance">
    <StackLayout>
        <Button x:Name="button"
                Text="Button"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                TextColor="{Binding Source={x:Reference textColorPicker},
                                    Path=SelectedItem.Color}"
                BackgroundColor="{Binding Source={x:Reference backgroundColorPicker},
                                          Path=SelectedItem.Color}"
                BorderColor="{Binding Source={x:Reference borderColorPicker},
                                      Path=SelectedItem.Color}" />

        <StackLayout BindingContext="{x:Reference button}"
                     Padding="10">

            <Slider x:Name="fontSizeSlider"
                    Maximum="48"
                    Minimum="1"
                    Value="{Binding FontSize}" />

            <Label Text="{Binding Source={x:Reference fontSizeSlider},
                                  Path=Value,
                                  StringFormat='FontSize = {0:F0}'}"
                   HorizontalTextAlignment="Center" />

            <Slider x:Name="borderWidthSlider"
                    Minimum="-1"
                    Maximum="12"
                    Value="{Binding BorderWidth}" />

            <Label Text="{Binding Source={x:Reference borderWidthSlider},
                                  Path=Value,
                                  StringFormat='BorderWidth = {0:F0}'}"
                   HorizontalTextAlignment="Center" />

            <Slider x:Name="cornerRadiusSlider"
                    Minimum="-1"
                    Maximum="24"
                    Value="{Binding CornerRadius}" />

            <Label Text="{Binding Source={x:Reference cornerRadiusSlider},
                                  Path=Value,
                                  StringFormat='CornerRadius = {0:F0}'}"
                   HorizontalTextAlignment="Center" />

            <Grid>
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                </Grid.RowDefinitions>

                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>

                <Grid.Resources>
                    <Style TargetType="Label">
                        <Setter Property="VerticalOptions" Value="Center" />
                    </Style>
                </Grid.Resources>

                <Label Text="Text Color:"
                       Grid.Row="0" Grid.Column="0" />

                <Picker x:Name="textColorPicker"
                        ItemsSource="{Binding Source={x:Static local:NamedColor.All}}"
                        ItemDisplayBinding="{Binding FriendlyName}"
                        SelectedIndex="0"
                        Grid.Row="0" Grid.Column="1" />

                <Label Text="Background Color:"
                       Grid.Row="1" Grid.Column="0" />

                <Picker x:Name="backgroundColorPicker"
                        ItemsSource="{Binding Source={x:Static local:NamedColor.All}}"
                        ItemDisplayBinding="{Binding FriendlyName}"
                        SelectedIndex="0"
                        Grid.Row="1" Grid.Column="1" />

                <Label Text="Border Color:"
                       Grid.Row="2" Grid.Column="0" />

                <Picker x:Name="borderColorPicker"
                        ItemsSource="{Binding Source={x:Static local:NamedColor.All}}"
                        ItemDisplayBinding="{Binding FriendlyName}"
                        SelectedIndex="0"
                        Grid.Row="2" Grid.Column="1" />
            </Grid>
        </StackLayout>
    </StackLayout>
</ContentPage>
```

`Button`页面顶部的三个 `Color` 属性绑定到 `Picker` 页面底部的元素。 元素中的项 `Picker` 是 `NamedColor` 包含在项目中的类的颜色。 三个 `Slider` 元素包含对的 `FontSize` 、 `BorderWidth` 和属性的双向绑定 `CornerRadius` `Button` 。

此程序允许您试验所有这些属性的组合：

[![按钮外观](button-images/ButtonAppearance.png "按钮外观")](button-images/ButtonAppearance-Large.png)

若要查看 `Button` 边框，需要将设置为，而将设置为 `BorderColor` `Default` `BorderWidth` 正值。

在 iOS 上，你会注意到，在内部强行进入大的边框宽度 `Button` ，并干扰文本显示。 如果选择使用包含 iOS 的边框 `Button` ，则可能需要开始和结束 `Text` 具有空格的属性以保持其可见性。

在 UWP 上，如果选择 `CornerRadius` 超过一半的，则会 `Button` 引发异常。

## <a name="button-visual-states"></a>按钮视觉状态

[`Button`](xref:Xamarin.Forms.Button) 具有 `Pressed` [`VisualState`](xref:Xamarin.Forms.VisualState) ，可用于在用户按下时启动对的视觉对象更改 `Button` （如果已启用）。

下面的 XAML 示例演示如何为状态定义可视状态 `Pressed` ：

```xaml
<Button Text="Click me!"
        ...>
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>
                    <Setter Property="Scale"
                            Value="1" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Pressed">
                <VisualState.Setters>
                    <Setter Property="Scale"
                            Value="0.8" />
                </VisualState.Setters>
            </VisualState>

        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Button>
```

`Pressed` [`VisualState`](xref:Xamarin.Forms.VisualState) 指定在 [`Button`](xref:Xamarin.Forms.Button) 按下时，其 [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) 属性将从其默认值1更改为0.8。 `Normal` `VisualState` 指定在 `Button` 处于正常状态时，其 `Scale` 属性将设置为1。 因此，整体效果是在 `Button` 按下时，它会略微缩小，重新缩放 `Button` 释放后，它将被重新缩放为其默认大小。

有关可视状态的详细信息，请 [参阅 Xamarin.Forms 可视状态管理器](~/xamarin-forms/user-interface/visual-state-manager.md)。

## <a name="creating-a-toggle-button"></a>创建切换按钮

可以使用子类， `Button` 使其工作方式类似于 "关显" 开关：点击按钮一次，切换按钮，然后再次点击以将其关闭。

以下 `ToggleButton` 类派生自 `Button` ，并定义名为的新事件 `Toggled` 和名为的布尔值属性 `IsToggled` 。 这些属性与定义的两个属性相同 Xamarin.Forms [`Switch`](xref:Xamarin.Forms.Switch) ：

```csharp
class ToggleButton : Button
{
    public event EventHandler<ToggledEventArgs> Toggled;

    public static BindableProperty IsToggledProperty =
        BindableProperty.Create("IsToggled", typeof(bool), typeof(ToggleButton), false,
                                propertyChanged: OnIsToggledChanged);

    public ToggleButton()
    {
        Clicked += (sender, args) => IsToggled ^= true;
    }

    public bool IsToggled
    {
        set { SetValue(IsToggledProperty, value); }
        get { return (bool)GetValue(IsToggledProperty); }
    }

    protected override void OnParentSet()
    {
        base.OnParentSet();
        VisualStateManager.GoToState(this, "ToggledOff");
    }

    static void OnIsToggledChanged(BindableObject bindable, object oldValue, object newValue)
    {
        ToggleButton toggleButton = (ToggleButton)bindable;
        bool isToggled = (bool)newValue;

        // Fire event
        toggleButton.Toggled?.Invoke(toggleButton, new ToggledEventArgs(isToggled));

        // Set the visual state
        VisualStateManager.GoToState(toggleButton, isToggled ? "ToggledOn" : "ToggledOff");
    }
}
```

`ToggleButton`构造函数将处理程序附加到 `Clicked` 事件，以便它可以更改属性的值 `IsToggled` 。 `OnIsToggledChanged`方法引发 `Toggled` 事件。

方法的最后一行 `OnIsToggledChanged` 调用静态 `VisualStateManager.GoToState` 方法，其中包含两个文本字符串 "ToggledOn" 和 "ToggledOff"。 你可以阅读有关此方法的信息，以及你的应用程序可以如何响应 [** Xamarin.Forms 可视状态管理器**](~/xamarin-forms/user-interface/visual-state-manager.md)一文中的可视状态的信息。

因为对 `ToggleButton` 进行调用 `VisualStateManager.GoToState` ，所以类本身不需要包含任何其他工具来基于其状态更改按钮的外观 `IsToggled` 。 这是承载的 XAML 的责任 `ToggleButton` 。

**切换按钮**的 "演示" 页包含两个实例 `ToggleButton` ，其中包括 `Text` `BackgroundColor` 基于可视状态设置按钮、和的可视状态管理器标记 `TextColor` ：

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ButtonDemos"
             x:Class="ButtonDemos.ToggleButtonDemoPage"
             Title="Toggle Button Demo">

    <ContentPage.Resources>
        <Style TargetType="local:ToggleButton">
            <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            <Setter Property="HorizontalOptions" Value="Center" />
        </Style>
    </ContentPage.Resources>

    <StackLayout Padding="10, 0">
        <local:ToggleButton Toggled="OnItalicButtonToggled">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="ToggleStates">
                    <VisualState Name="ToggledOff">
                        <VisualState.Setters>
                            <Setter Property="Text" Value="Italic Off" />
                            <Setter Property="BackgroundColor" Value="#C0C0C0" />
                            <Setter Property="TextColor" Value="Black" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState Name="ToggledOn">
                        <VisualState.Setters>
                            <Setter Property="Text" Value=" Italic On " />
                            <Setter Property="BackgroundColor" Value="#404040" />
                            <Setter Property="TextColor" Value="White" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        </local:ToggleButton>

        <local:ToggleButton Toggled="OnBoldButtonToggled">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="ToggleStates">
                    <VisualState Name="ToggledOff">
                        <VisualState.Setters>
                            <Setter Property="Text" Value="Bold Off" />
                            <Setter Property="BackgroundColor" Value="#C0C0C0" />
                            <Setter Property="TextColor" Value="Black" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState Name="ToggledOn">
                        <VisualState.Setters>
                            <Setter Property="Text" Value=" Bold On " />
                            <Setter Property="BackgroundColor" Value="#404040" />
                            <Setter Property="TextColor" Value="White" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        </local:ToggleButton>

        <Label x:Name="label"
               Text="Just a little passage of some sample text that can be formatted in italic or boldface by toggling the two buttons."
               FontSize="Large"
               HorizontalTextAlignment="Center"
               VerticalOptions="CenterAndExpand" />

    </StackLayout>
</ContentPage>
```

`Toggled`事件处理程序在代码隐藏文件中。 它们负责根据 `FontAttributes` 按钮的状态设置的属性 `Label` ：

```csharp
public partial class ToggleButtonDemoPage : ContentPage
{
    public ToggleButtonDemoPage ()
    {
        InitializeComponent ();
    }

    void OnItalicButtonToggled(object sender, ToggledEventArgs args)
    {
        if (args.Value)
        {
            label.FontAttributes |= FontAttributes.Italic;
        }
        else
        {
            label.FontAttributes &= ~FontAttributes.Italic;
        }
    }

    void OnBoldButtonToggled(object sender, ToggledEventArgs args)
    {
        if (args.Value)
        {
            label.FontAttributes |= FontAttributes.Bold;
        }
        else
        {
            label.FontAttributes &= ~FontAttributes.Bold;
        }
    }
}
```

下面是在 iOS、Android 和 UWP 上运行的程序：

[![切换按钮演示](button-images/ToggleButtonDemo.png "切换按钮演示")](button-images/ToggleButtonDemo-Large.png#lightbox)

## <a name="using-bitmaps-with-buttons"></a>使用带有按钮的位图

`Button`类定义一个 [`ImageSource`](xref:Xamarin.Forms.Button.Image) 属性，该属性允许你在上显示位图图像 `Button` （单独显示或与文本组合显示）。 还可以指定文本和图像的排列方式。

`ImageSource`属性的类型为 [`ImageSource`](xref:Xamarin.Forms.ImageSource) ，这意味着可以从文件、嵌入的资源、URI 或流加载位图。

> [!NOTE]
> 虽然 `Button` 可以加载动态 gif，但它只显示 gif 的第一帧。

支持的每个平台都 Xamarin.Forms 允许将图像存储为多个大小，以使应用程序可能运行的不同设备的像素分辨率不同。 这些位图以这样一种方式命名或存储，操作系统可为设备的视频显示分辨率选取最佳匹配项。

对于上的位图 `Button` ，最佳大小通常介于32和64之间与设备无关的单位，具体取决于所需的大小。 本示例中使用的映像基于与48设备无关的单位大小。

在 iOS 项目中， **Resources** 文件夹包含三种大小的图像：

- 存储为/Resources/的48像素正方形位图 **MonkeyFace.png**
- 一个存储为的96像素的正方形位图 **/Resource/MonkeyFace@2x.png**
- 一个存储为的144像素的正方形位图 **/Resource/MonkeyFace@3x.png**

为所有三个位图提供了**BundleResource**的**生成操作**。

对于 Android 项目，位图都具有相同的名称，但它们存储在 **Resources** 文件夹的不同子文件夹中：

- 存储为/Resources/drawable-hdpi/的72像素正方形位图 **MonkeyFace.png**
- 存储为/Resources/drawable-xhdpi/的96像素正方形位图 **MonkeyFace.png**
- 存储为/Resources/drawable-xxhdpi/的144像素正方形位图 **MonkeyFace.png**
- 存储为/Resources/drawable-xxxhdpi/的192像素正方形位图 **MonkeyFace.png**

提供了**AndroidResource**的**生成操作**。

在 UWP 项目中，可以将位图存储在项目中的任何位置，但它们通常存储在自定义文件夹或 **资产** 现有文件夹中。 UWP 项目包含以下位图：

- 存储为/Assets/的48像素正方形位图 **MonkeyFace.scale-100.png**
- 存储为/Assets/的96像素正方形位图 **MonkeyFace.scale-200.png**
- 存储为/Assets/的192像素正方形位图 **MonkeyFace.scale-400.png**

它们都是**内容**的**生成操作**。

您可以使用的属性来指定 `Text` 和 `ImageSource` 属性的排列方式 `Button` [`ContentLayout`](xref:Xamarin.Forms.Button.ContentLayout) `Button` 。 此属性的类型为 [`ButtonContentLayout`](xref:Xamarin.Forms.Button.ButtonContentLayout) ，它是中的嵌入类 `Button` 。 [构造函数] (x： Xamarin.Forms 。ButtonContentLayout .% 23ctor (Xamarin.Forms 。ButtonContentLayout. ImagePosition，) # A3 具有两个参数：

- 枚举的成员 [`ImagePosition`](xref:Xamarin.Forms.Button.ButtonContentLayout.ImagePosition) ： `Left` 、 `Top` 、 `Right` 或 `Bottom` ，指示位图相对于文本的显示方式。
- `double`位图和文本之间的间距的值。

默认值为 `Left` 和10个单位。 命名的两个只读属性 `ButtonContentLayout` [`Position`](xref:Xamarin.Forms.Button.ButtonContentLayout.Position) ，并 [`Spacing`](xref:Xamarin.Forms.Button.ButtonContentLayout.Spacing) 提供这些属性的值。

在代码中，可以创建 `Button` 并设置属性， `ContentLayout` 如下所示：

```csharp
Button button = new Button
{
    Text = "button text",
    ImageSource = new FileImageSource
    {
        File = "image filename"
    },
    ContentLayout = new Button.ButtonContentLayout(Button.ButtonContentLayout.ImagePosition.Right, 20)
};
```

在 XAML 中，只需以逗号分隔的任何顺序仅指定枚举成员或间距，或同时指定这两者：

```xaml
<Button Text="button text"
        ImageSource="image filename"
        ContentLayout="Right, 20" />
```

" **图像" 按钮演示** 页用于为 `OnPlatform` iOS、Android 和 UWP 位图文件指定不同的文件名。 如果要对每个平台使用相同的文件名，并避免使用 `OnPlatform` ，则需要在项目的根目录中存储 UWP 位图。

" `Button` **图像按钮演示** " 页上的第一个设置 `Image` 属性，但不设置 `Text` 属性：

```xaml
<Button>
    <Button.ImageSource>
        <OnPlatform x:TypeArguments="ImageSource">
            <On Platform="iOS, Android" Value="MonkeyFace.png" />
            <On Platform="UWP" Value="Assets/MonkeyFace.png" />
        </OnPlatform>
    </Button.ImageSource>
</Button>
```

如果 UWP 位图存储在项目的根目录中，则可大大简化此标记：

```xaml
<Button ImageSource="MonkeyFace.png" />
```

为了避免 **ImageButtonDemo** 文件中有大量重复的标记，还定义了一个隐式， `Style` 以设置 `ImageSource` 属性。 这会 `Style` 自动应用到五个其他 `Button` 元素。 下面是完整的 XAML 文件：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ButtonDemos.ImageButtonDemoPage">

    <FlexLayout Direction="Column"
                JustifyContent="SpaceEvenly"
                AlignItems="Center">

        <FlexLayout.Resources>
            <Style TargetType="Button">
                <Setter Property="ImageSource">
                    <OnPlatform x:TypeArguments="ImageSource">
                        <On Platform="iOS, Android" Value="MonkeyFace.png" />
                        <On Platform="UWP" Value="Assets/MonkeyFace.png" />
                    </OnPlatform>
                </Setter>
            </Style>
        </FlexLayout.Resources>

        <Button>
            <Button.ImageSource>
                <OnPlatform x:TypeArguments="ImageSource">
                    <On Platform="iOS, Android" Value="MonkeyFace.png" />
                    <On Platform="UWP" Value="Assets/MonkeyFace.png" />
                </OnPlatform>
            </Button.ImageSource>
        </Button>

        <Button Text="Default" />

        <Button Text="Left - 10"
                ContentLayout="Left, 10" />

        <Button Text="Top - 10"
                ContentLayout="Top, 10" />

        <Button Text="Right - 20"
                ContentLayout="Right, 20" />

        <Button Text="Bottom - 20"
                ContentLayout="Bottom, 20" />
    </FlexLayout>
</ContentPage>
```

最后四个 `Button` 元素使用 `ContentLayout` 属性指定文本和位图的位置和间距：

[![图像按钮演示](button-images/ImageButtonDemo.png "图像按钮演示")](button-images/ImageButtonDemo-Large.png#lightbox)

现在，你已了解了可用于处理 `Button` 事件和更改外观的各种方式 `Button` 。

## <a name="related-links"></a>相关链接

- [System.windows.forms.datagridview.buttondemos 示例](/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos)
- [按钮 API](xref:Xamarin.Forms.Button)