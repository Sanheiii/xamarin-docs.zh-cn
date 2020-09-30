---
title: 第 5 部分。 从数据绑定到 MVVM
description: MVVM 模式强制分隔三个软件层（XAML 用户界面，称作视图）：基本数据，称为模型;在视图和模型之间使用中间名为 ViewModel。
ms.prod: xamarin
ms.custom: video
ms.assetid: 48B37D44-4FB1-41B2-9A5E-6D383B041F81
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8cb59738519af933e509ebf63a923e573667941e
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562907"
---
# <a name="part-5-from-data-bindings-to-mvvm"></a>第 5 部分。 从数据绑定到 MVVM

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_模型-视图-ViewModel (MVVM) 体系结构模式是通过 XAML 而构建的。此模式强制分离三个软件层（XAML 用户界面，称作视图）：基本数据，称为模型;在视图和模型之间使用中间名为 ViewModel。视图和 ViewModel 通常通过 XAML 文件中定义的数据绑定连接。视图的 BindingContext 通常是 ViewModel 的实例。_

## <a name="a-simple-viewmodel"></a>简单的 ViewModel

作为 Viewmodel 的简介，让我们先看一看没有一个程序的程序。
之前，你已了解如何定义新的 XML 命名空间声明，以允许 XAML 文件引用其他程序集中的类。 下面是一个程序，它定义命名空间的 XML 命名空间声明 `System` ：

```csharp
xmlns:sys="clr-namespace:System;assembly=netstandard"
```

程序可以使用 `x:Static` 从静态属性获取当前日期和时间 `DateTime.Now` ，并将 `DateTime` 该值设置为 `BindingContext` 上的 `StackLayout` ：

```xaml
<StackLayout BindingContext="{x:Static sys:DateTime.Now}" …>
```

`BindingContext` 是一个特殊属性：当在元素上设置时，该元素的所有子级都将继承该属性 `BindingContext` 。 这意味着的所有子级 `StackLayout` 都具有相同的 `BindingContext` ，并且它们可以包含与该对象的属性的简单绑定。

在 **一键式 DateTime** 程序中，有两个子项包含对该值的属性的绑定 `DateTime` ，但两个其他子级包含似乎缺少绑定路径的绑定。 这意味着 `DateTime` 值本身用于 `StringFormat` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:sys="clr-namespace:System;assembly=netstandard"
             x:Class="XamlSamples.OneShotDateTimePage"
             Title="One-Shot DateTime Page">

    <StackLayout BindingContext="{x:Static sys:DateTime.Now}"
                 HorizontalOptions="Center"
                 VerticalOptions="Center">

        <Label Text="{Binding Year, StringFormat='The year is {0}'}" />
        <Label Text="{Binding StringFormat='The month is {0:MMMM}'}" />
        <Label Text="{Binding Day, StringFormat='The day is {0}'}" />
        <Label Text="{Binding StringFormat='The time is {0:T}'}" />

    </StackLayout>
</ContentPage>
```

问题在于，在第一次生成页面时，日期和时间设置一次，并且永远不会发生更改：

[![显示日期和时间的视图](data-bindings-to-mvvm-images/oneshotdatetime.png)](data-bindings-to-mvvm-images/oneshotdatetime-large.png#lightbox "显示日期和时间的视图")

XAML 文件可以显示始终显示当前时间的时钟，但它需要一些代码来帮助你解决问题。当考虑 MVVM 时，模型和 ViewModel 是完全用代码编写的类。 视图通常是一个 XAML 文件，它引用 ViewModel 中通过数据绑定定义的属性。

适当的模型是未知的 ViewModel，并有适当的 ViewModel 未知的。 但是，通常情况下，程序员会将 ViewModel 公开的数据类型定制到与特定用户界面相关联的数据类型。 例如，如果某个模型访问的数据库包含8位字符 ASCII 字符串，则 ViewModel 需要在这两个字符串之间进行转换，以便在用户界面中独占使用 Unicode。

在 MVVM (的简单示例中，如此处所示) ，通常根本没有模型，该模式只涉及与数据绑定关联的视图和 ViewModel。

下面是一个 ViewModel，其中只包含一个名为的属性 `DateTime` ，该属性 `DateTime` 每秒更新一次该属性：

```csharp
using System;
using System.ComponentModel;
using Xamarin.Forms;

namespace XamlSamples
{
    class ClockViewModel : INotifyPropertyChanged
    {
        DateTime dateTime;

        public event PropertyChangedEventHandler PropertyChanged;

        public ClockViewModel()
        {
            this.DateTime = DateTime.Now;

            Device.StartTimer(TimeSpan.FromSeconds(1), () =>
                {
                    this.DateTime = DateTime.Now;
                    return true;
                });
        }

        public DateTime DateTime
        {
            set
            {
                if (dateTime != value)
                {
                    dateTime = value;

                    if (PropertyChanged != null)
                    {
                        PropertyChanged(this, new PropertyChangedEventArgs("DateTime"));
                    }
                }
            }
            get
            {
                return dateTime;
            }
        }
    }
}
```

Viewmodel 通常实现 `INotifyPropertyChanged` 接口，这意味着每当类的属性发生更改时，类就会触发 `PropertyChanged` 事件。 中的数据绑定机制将 Xamarin.Forms 处理程序附加到此 `PropertyChanged` 事件，以便在属性发生更改时通知该事件，并使用新值更新目标。

基于此 ViewModel 的时钟非常简单，如下所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.ClockPage"
             Title="Clock Page">

    <Label Text="{Binding DateTime, StringFormat='{0:T}'}"
           FontSize="Large"
           HorizontalOptions="Center"
           VerticalOptions="Center">
        <Label.BindingContext>
            <local:ClockViewModel />
        </Label.BindingContext>
    </Label>
</ContentPage>
```

请注意如何 `ClockViewModel` `BindingContext` `Label` 使用属性元素标记将设置为的。 或者，可以 `ClockViewModel` 在集合中实例化， `Resources` 并 `BindingContext` 通过标记扩展将其设置为 `StaticResource` 。 或者，代码隐藏文件可以实例化 ViewModel。

`Binding`的属性的标记扩展 `Text` `Label` 设置属性的格式 `DateTime` 。 显示内容如下：

[![查看通过 ViewModel 显示日期和时间](data-bindings-to-mvvm-images/clock.png)](data-bindings-to-mvvm-images/clock-large.png#lightbox "查看通过 ViewModel 显示日期和时间")

还可以 `DateTime` 通过用句点分隔属性，来访问 ViewModel 属性的各个属性：

```xaml
<Label Text="{Binding DateTime.Second, StringFormat='{0}'}" … >
```

## <a name="interactive-mvvm"></a>交互式 MVVM

对于基于基础数据模型的交互式视图，MVVM 通常与双向数据绑定一起使用。

下面是一个名为 `HslViewModel` 的类，它将 `Color` 值转换为 `Hue` 、 `Saturation` 和 `Luminosity` 值，反之亦然：

```csharp
using System;
using System.ComponentModel;
using Xamarin.Forms;

namespace XamlSamples
{
    public class HslViewModel : INotifyPropertyChanged
    {
        double hue, saturation, luminosity;
        Color color;

        public event PropertyChangedEventHandler PropertyChanged;

        public double Hue
        {
            set
            {
                if (hue != value)
                {
                    hue = value;
                    OnPropertyChanged("Hue");
                    SetNewColor();
                }
            }
            get
            {
                return hue;
            }
        }

        public double Saturation
        {
            set
            {
                if (saturation != value)
                {
                    saturation = value;
                    OnPropertyChanged("Saturation");
                    SetNewColor();
                }
            }
            get
            {
                return saturation;
            }
        }

        public double Luminosity
        {
            set
            {
                if (luminosity != value)
                {
                    luminosity = value;
                    OnPropertyChanged("Luminosity");
                    SetNewColor();
                }
            }
            get
            {
                return luminosity;
            }
        }

        public Color Color
        {
            set
            {
                if (color != value)
                {
                    color = value;
                    OnPropertyChanged("Color");

                    Hue = value.Hue;
                    Saturation = value.Saturation;
                    Luminosity = value.Luminosity;
                }
            }
            get
            {
                return color;
            }
        }

        void SetNewColor()
        {
            Color = Color.FromHsla(Hue, Saturation, Luminosity);
        }

        protected virtual void OnPropertyChanged(string propertyName)
        {
            PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
        }
    }
}
```

对 `Hue` 、和属性的更改将 `Saturation` `Luminosity` 导致 `Color` 属性发生更改，并将更改为 `Color` 导致其他三个属性更改。 这似乎是一个无限循环，只不过类不调用 `PropertyChanged` 事件，除非属性已更改。 这会将一个端置于另一个不可控的反馈循环。

下面的 XAML 文件包含， `BoxView` 其 `Color` 属性绑定到 ViewModel 的 `Color` 属性，以及三个 `Slider` 和三个 `Label` 绑定到 `Hue` 、 `Saturation` 和属性的视图 `Luminosity` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.HslColorScrollPage"
             Title="HSL Color Scroll Page">
    <ContentPage.BindingContext>
        <local:HslViewModel Color="Aqua" />
    </ContentPage.BindingContext>

    <StackLayout Padding="10, 0">
        <BoxView Color="{Binding Color}"
                 VerticalOptions="FillAndExpand" />

        <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}"
               HorizontalOptions="Center" />

        <Slider Value="{Binding Hue, Mode=TwoWay}" />

        <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}"
               HorizontalOptions="Center" />

        <Slider Value="{Binding Saturation, Mode=TwoWay}" />

        <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}"
               HorizontalOptions="Center" />

        <Slider Value="{Binding Luminosity, Mode=TwoWay}" />
    </StackLayout>
</ContentPage>
```

每个上的绑定 `Label` 都是默认值 `OneWay` 。 它只需要显示值。 但每个上的绑定 `Slider` 是 `TwoWay` 。 这允许 `Slider` 从 ViewModel 进行初始化。 请注意，在 `Color` `Aqua` 对 ViewModel 进行实例化时，属性设置为。 但中的更改 `Slider` 还需要为 ViewModel 中的属性设置新值，然后计算新的颜色。

[![使用双向数据绑定的 MVVM](data-bindings-to-mvvm-images/hslcolorscroll.png)](data-bindings-to-mvvm-images/hslcolorscroll-large.png#lightbox "使用双向数据绑定的 MVVM")

## <a name="commanding-with-viewmodels"></a>用 Viewmodel 进行命令

在许多情况下，MVVM 模式限制为数据项目的操作： ViewModel 中的视图并行数据对象中的用户界面对象。

但有时，视图需要包含在 ViewModel 中触发各种操作的按钮。 但 ViewModel 不能包含 `Clicked` 按钮的处理程序，因为这会将 ViewModel 关联到特定的用户界面模式。

若要允许 Viewmodel 更独立于特定用户界面对象，但仍允许在 ViewModel 中调用方法，则可以使用 *命令* 界面。 以下元素支持此命令界面 Xamarin.Forms ：

- `Button`
- `MenuItem`
- `ToolbarItem`
- `SearchBar`
- `TextCell` (，因此还 `ImageCell`) 
- `ListView`
- `TapGestureRecognizer`

除了 `SearchBar` 和 `ListView` 元素，这些元素定义了两个属性：

- `Command` 类型为  `System.Windows.Input.ICommand`
- `CommandParameter` 类型为  `Object`

`SearchBar`定义 `SearchCommand` 和 `SearchCommandParameter` 属性，而 `ListView` 定义 `RefreshCommand` 类型的属性 `ICommand` 。

`ICommand`接口定义了两个方法和一个事件：

- `void Execute(object arg)`
- `bool CanExecute(object arg)`
- `event EventHandler CanExecuteChanged`

ViewModel 可以定义类型的属性 `ICommand` 。 然后，可以将这些属性绑定到 `Command` 每个 `Button` 或其他元素的属性，或者可能绑定到实现此接口的自定义视图。 您可以选择将 `CommandParameter` 属性设置为标识 `Button` (或绑定到此 ViewModel 属性的其他元素) 的单个对象。 在内部， `Button` `Execute` 每当用户点击 `Button` ，并将其传递给方法时，都会调用方法 `Execute` `CommandParameter` 。

`CanExecute`方法和 `CanExecuteChanged` 事件用于在 `Button` 点击可能当前无效的情况下，在这种情况下， `Button` 应禁用自身。 `Button` `CanExecute` `Command` 第一次设置属性和 `CanExecuteChanged` 触发事件时调用。 如果 `CanExecute` 返回 `false` ，则将 `Button` 禁用自身，而不会生成 `Execute` 调用。

有关将命令添加到 Viewmodel 的帮助，请 Xamarin.Forms 定义实现的两个类 `ICommand` ： `Command` ， `Command<T>` 其中 `T` 是和的参数的 `Execute` 类型 `CanExecute` 。 这两个类定义了多个构造函数，以及一个 `ChangeCanExecute` 方法，ViewModel 可以调用该方法来强制 `Command` 对象触发 `CanExecuteChanged` 事件。

下面是用于输入电话号码的简单键盘的 ViewModel。 请注意， `Execute` 和 `CanExecute` 方法在构造函数中定义为 lambda 函数：

```csharp
using System;
using System.ComponentModel;
using System.Windows.Input;
using Xamarin.Forms;

namespace XamlSamples
{
    class KeypadViewModel : INotifyPropertyChanged
    {
        string inputString = "";
        string displayText = "";
        char[] specialChars = { '*', '#' };

        public event PropertyChangedEventHandler PropertyChanged;

        // Constructor
        public KeypadViewModel()
        {
            AddCharCommand = new Command<string>((key) =>
                {
                    // Add the key to the input string.
                    InputString += key;
                });

            DeleteCharCommand = new Command(() =>
                {
                    // Strip a character from the input string.
                    InputString = InputString.Substring(0, InputString.Length - 1);
                },
                () =>
                {
                    // Return true if there's something to delete.
                    return InputString.Length > 0;
                });
        }

        // Public properties
        public string InputString
        {
            protected set
            {
                if (inputString != value)
                {
                    inputString = value;
                    OnPropertyChanged("InputString");
                    DisplayText = FormatText(inputString);

                    // Perhaps the delete button must be enabled/disabled.
                    ((Command)DeleteCharCommand).ChangeCanExecute();
                }
            }

            get { return inputString; }
        }

        public string DisplayText
        {
            protected set
            {
                if (displayText != value)
                {
                    displayText = value;
                    OnPropertyChanged("DisplayText");
                }
            }
            get { return displayText; }
        }

        // ICommand implementations
        public ICommand AddCharCommand { protected set; get; }

        public ICommand DeleteCharCommand { protected set; get; }

        string FormatText(string str)
        {
            bool hasNonNumbers = str.IndexOfAny(specialChars) != -1;
            string formatted = str;

            if (hasNonNumbers || str.Length < 4 || str.Length > 10)
            {
            }
            else if (str.Length < 8)
            {
                formatted = String.Format("{0}-{1}",
                                          str.Substring(0, 3),
                                          str.Substring(3));
            }
            else
            {
                formatted = String.Format("({0}) {1}-{2}",
                                          str.Substring(0, 3),
                                          str.Substring(3, 3),
                                          str.Substring(6));
            }
            return formatted;
        }

        protected void OnPropertyChanged(string propertyName)
        {
            PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
        }
    }
}
```

此 ViewModel 假设 `AddCharCommand` 属性绑定到 `Command` 多个按钮的属性 (或) 具有命令界面的任何其他属性，其中每个按钮都由标识 `CommandParameter` 。 这些按钮将字符添加到 `InputString` 属性，然后将其格式化为属性的电话号码 `DisplayText` 。

还有另一个名为的类型的 `ICommand` 属性 `DeleteCharCommand` 。 此项已绑定到后退间距按钮，但如果没有要删除的字符，则应禁用该按钮。

以下小键盘并不像它那样非常复杂。 相反，此标记已缩小为最小值，以演示更清晰地使用命令界面：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.KeypadPage"
             Title="Keypad Page">

    <Grid HorizontalOptions="Center"
          VerticalOptions="Center">
        <Grid.BindingContext>
            <local:KeypadViewModel />
        </Grid.BindingContext>

        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="80" />
            <ColumnDefinition Width="80" />
            <ColumnDefinition Width="80" />
        </Grid.ColumnDefinitions>

        <!-- Internal Grid for top row of items -->
        <Grid Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="3">
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="Auto" />
            </Grid.ColumnDefinitions>

            <Frame Grid.Column="0"
                   OutlineColor="Accent">
                <Label Text="{Binding DisplayText}" />
            </Frame>

            <Button Text="&#x21E6;"
                    Command="{Binding DeleteCharCommand}"
                    Grid.Column="1"
                    BorderWidth="0" />
        </Grid>

        <Button Text="1"
                Command="{Binding AddCharCommand}"
                CommandParameter="1"
                Grid.Row="1" Grid.Column="0" />

        <Button Text="2"
                Command="{Binding AddCharCommand}"
                CommandParameter="2"
                Grid.Row="1" Grid.Column="1" />

        <Button Text="3"
                Command="{Binding AddCharCommand}"
                CommandParameter="3"
                Grid.Row="1" Grid.Column="2" />

        <Button Text="4"
                Command="{Binding AddCharCommand}"
                CommandParameter="4"
                Grid.Row="2" Grid.Column="0" />

        <Button Text="5"
                Command="{Binding AddCharCommand}"
                CommandParameter="5"
                Grid.Row="2" Grid.Column="1" />

        <Button Text="6"
                Command="{Binding AddCharCommand}"
                CommandParameter="6"
                Grid.Row="2" Grid.Column="2" />

        <Button Text="7"
                Command="{Binding AddCharCommand}"
                CommandParameter="7"
                Grid.Row="3" Grid.Column="0" />

        <Button Text="8"
                Command="{Binding AddCharCommand}"
                CommandParameter="8"
                Grid.Row="3" Grid.Column="1" />

        <Button Text="9"
                Command="{Binding AddCharCommand}"
                CommandParameter="9"
                Grid.Row="3" Grid.Column="2" />

        <Button Text="*"
                Command="{Binding AddCharCommand}"
                CommandParameter="*"
                Grid.Row="4" Grid.Column="0" />

        <Button Text="0"
                Command="{Binding AddCharCommand}"
                CommandParameter="0"
                Grid.Row="4" Grid.Column="1" />

        <Button Text="#"
                Command="{Binding AddCharCommand}"
                CommandParameter="#"
                Grid.Row="4" Grid.Column="2" />
    </Grid>
</ContentPage>
```

`Command` `Button` 此标记中显示的第一个的属性将绑定到 `DeleteCharCommand` ; 其余的将绑定到，其 `AddCharCommand` 与在 `CommandParameter` 表面上出现的字符相同 `Button` 。 下面是操作中的程序：

[![使用 MVVM 和命令的计算器](data-bindings-to-mvvm-images/keypad.png)](data-bindings-to-mvvm-images/keypad-large.png#lightbox "使用 MVVM 和命令的计算器")

### <a name="invoking-asynchronous-methods"></a>调用异步方法

命令还可以调用异步方法。 `async` `await` 在指定方法时，可以使用和关键字实现此目的 `Execute` ：

```csharp
DownloadCommand = new Command (async () => await DownloadAsync ());
```

这表示该 `DownloadAsync` 方法为 `Task` ，应等待：

```csharp
async Task DownloadAsync ()
{
    await Task.Run (() => Download ());
}

void Download ()
{
    ...
}
```

## <a name="implementing-a-navigation-menu"></a>实现导航菜单

包含此系列文章中所有源代码的 [XamlSamples](/samples/xamarin/xamarin-forms-samples/xamlsamples) 程序在其主页中使用 ViewModel。 此 ViewModel 是一个 short 类的定义，其中包含三个名为、和的属性， `Type` `Title` `Description` 其中包含每个示例页的类型、标题和简短说明。 此外，ViewModel 定义了一个名为的静态属性 `All` ，它是程序中所有页面的集合：

```csharp
public class PageDataViewModel
{
    public PageDataViewModel(Type type, string title, string description)
    {
        Type = type;
        Title = title;
        Description = description;
    }

    public Type Type { private set; get; }

    public string Title { private set; get; }

    public string Description { private set; get; }

    static PageDataViewModel()
    {
        All = new List<PageDataViewModel>
        {
            // Part 1. Getting Started with XAML
            new PageDataViewModel(typeof(HelloXamlPage), "Hello, XAML",
                                  "Display a Label with many properties set"),

            new PageDataViewModel(typeof(XamlPlusCodePage), "XAML + Code",
                                  "Interact with a Slider and Button"),

            // Part 2. Essential XAML Syntax
            new PageDataViewModel(typeof(GridDemoPage), "Grid Demo",
                                  "Explore XAML syntax with the Grid"),

            new PageDataViewModel(typeof(AbsoluteDemoPage), "Absolute Demo",
                                  "Explore XAML syntax with AbsoluteLayout"),

            // Part 3. XAML Markup Extensions
            new PageDataViewModel(typeof(SharedResourcesPage), "Shared Resources",
                                  "Using resource dictionaries to share resources"),

            new PageDataViewModel(typeof(StaticConstantsPage), "Static Constants",
                                  "Using the x:Static markup extensions"),

            new PageDataViewModel(typeof(RelativeLayoutPage), "Relative Layout",
                                  "Explore XAML markup extensions"),

            // Part 4. Data Binding Basics
            new PageDataViewModel(typeof(SliderBindingsPage), "Slider Bindings",
                                  "Bind properties of two views on the page"),

            new PageDataViewModel(typeof(SliderTransformsPage), "Slider Transforms",
                                  "Use Sliders with reverse bindings"),

            new PageDataViewModel(typeof(ListViewDemoPage), "ListView Demo",
                                  "Use a ListView with data bindings"),

            // Part 5. From Data Bindings to MVVM
            new PageDataViewModel(typeof(OneShotDateTimePage), "One-Shot DateTime",
                                  "Obtain the current DateTime and display it"),

            new PageDataViewModel(typeof(ClockPage), "Clock",
                                  "Dynamically display the current time"),

            new PageDataViewModel(typeof(HslColorScrollPage), "HSL Color Scroll",
                                  "Use a view model to select HSL colors"),

            new PageDataViewModel(typeof(KeypadPage), "Keypad",
                                  "Use a view model for numeric keypad logic")
        };
    }

    public static IList<PageDataViewModel> All { private set; get; }
}
```

的 XAML 文件，用于 `MainPage` 定义 `ListBox` 其 `ItemsSource` 属性设置为该 `All` 属性的，其中包含 `TextCell` 用于显示 `Title` `Description` 每个页面的和属性的：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             x:Class="XamlSamples.MainPage"
             Padding="5, 0"
             Title="XAML Samples">

    <ListView ItemsSource="{x:Static local:PageDataViewModel.All}"
              ItemSelected="OnListViewItemSelected">
        <ListView.ItemTemplate>
            <DataTemplate>
                <TextCell Text="{Binding Title}"
                          Detail="{Binding Description}" />
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</ContentPage>
```

页面显示在可滚动列表中：

[![可滚动的页面列表](data-bindings-to-mvvm-images/mainpage.png)](data-bindings-to-mvvm-images/mainpage-large.png#lightbox "可滚动的页面列表")

当用户选择某一项时，将触发代码隐藏文件中的处理程序。 处理程序将 `SelectedItem` 返回的属性设置 `ListBox` 为 `null` ，然后实例化所选页面，并导航到该页面：

```csharp
private async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs args)
{
    (sender as ListView).SelectedItem = null;

    if (args.SelectedItem != null)
    {
        PageDataViewModel pageData = args.SelectedItem as PageDataViewModel;
        Page page = (Page)Activator.CreateInstance(pageData.Type);
        await Navigation.PushAsync(page);
    }
}
```

## <a name="video"></a>视频

> [!VIDEO https://youtube.com/embed/DYRLcqG2BAY]

**Xamarin 演化2016： MVVM 使和 Prism 变得简单 Xamarin.Forms**

## <a name="summary"></a>总结

XAML 是一种功能强大的工具，用于在应用程序中定义用户界面 Xamarin.Forms ，在使用数据绑定和 MVVM 时尤其如此。 结果是用户界面的简洁、优雅且可能的 toolable 表示形式，其中包含代码中的所有背景支持。

## <a name="related-links"></a>相关链接

- [XamlSamples](/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [第1部分。与 XAML 入门](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [第2部分。基本 XAML 语法](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [第3部分。XAML 标记扩展](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [第4部分。数据绑定基础知识](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)

## <a name="related-videos"></a>相关视频

> [!Video https://channel9.msdn.com/Series/Xamarin-101/XamarinForms-MVVM-with-XAML-6-of-11/player]

> [!Video https://channel9.msdn.com/Series/Xamarin-101/XamarinForms-Navigation-with-XAML-7-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]