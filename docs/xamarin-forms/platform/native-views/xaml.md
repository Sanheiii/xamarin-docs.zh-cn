---
title: 采用 XAML 的本机视图
description: 可以直接从 XAML 文件引用 iOS、Android 和通用 Windows 平台中的本机视图 Xamarin.Forms 。 可以在本机视图上设置属性和事件处理程序，并且可以与视图进行交互 Xamarin.Forms 。 本文演示如何从 XAML 文件使用本机视图 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: 7A856D31-B300-409E-9AEB-F8A4DB99B37E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/23/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 78bd93e2f6556480ae7d2903771d7d6303dda148
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91558371"
---
# <a name="native-views-in-xaml"></a>采用 XAML 的本机视图

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeswitch)

_可以直接从 XAML 文件引用 iOS、Android 和通用 Windows 平台中的本机视图 Xamarin.Forms 。可以在本机视图上设置属性和事件处理程序，并且可以与视图进行交互 Xamarin.Forms 。本文演示如何从 XAML 文件使用本机视图 Xamarin.Forms 。_

若要将本机视图嵌入到 Xamarin.Forms XAML 文件中：

1. `xmlns`为包含本机视图的命名空间在 XAML 文件中添加命名空间声明。
1. 在 XAML 文件中创建本机视图的实例。

> [!IMPORTANT]
> 对于任何使用本机视图的 XAML 页，必须禁用已编译的 XAML。 这可以通过使用特性修饰 XAML 页的代码隐藏类来实现 `[XamlCompilation(XamlCompilationOptions.Skip)]` 。 有关 XAML 编译的详细信息，请参阅[中的 Xamarin.Forms xaml 编译](~/xamarin-forms/xaml/xamlc.md)。

若要从代码隐藏文件引用本机视图，必须使用共享资产项目 (SAP) 并使用条件编译指令来包装平台特定代码。 有关详细信息，请参阅 [从代码引用本机视图](#refer-to-native-views-from-code)。

## <a name="consume-native-views"></a>使用本机视图

下面的代码示例演示如何将每个平台的本机视图用于 Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:win="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255,
            Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        x:Class="NativeViews.NativeViewDemo">
    <StackLayout Margin="20">
        <ios:UILabel Text="Hello World" TextColor="{x:Static ios:UIColor.Red}" View.HorizontalOptions="Start" />
        <androidWidget:TextView Text="Hello World" x:Arguments="{x:Static androidLocal:MainActivity.Instance}" />
        <win:TextBlock Text="Hello World" />
    </StackLayout>
</ContentPage>
```

`clr-namespace` `assembly` 还必须指定和，以便为本机视图命名空间指定和 `targetPlatform` 。 应将此项设置为 `iOS` 、 `Android` 、 (， `UWP` `Windows` 它等效于 `UWP`) 、 `macOS` 、 `GTK` 、 `Tizen` 或 `WPF` 。 在运行时，XAML 分析器将忽略任何与 `targetPlatform` 运行应用程序的平台不匹配的 XML 命名空间前缀。

每个命名空间声明都可用于引用指定命名空间中的任何类或结构。 例如， `ios` 命名空间声明可用于引用 iOS 命名空间中的任何类或结构 `UIKit` 。 本机视图的属性可以通过 XAML 进行设置，但属性和对象类型必须匹配。 例如， `UILabel.TextColor` 属性设置为 `UIColor.Red` 使用 `x:Static` 标记扩展和 `ios` 命名空间。

通过使用语法，还可以在本机视图上设置可绑定属性和附加的可绑定属性 `Class.BindableProperty="value"` 。 每个本机视图都包装在特定于平台的 `NativeViewWrapper` 实例中，后者派生自 [`Xamarin.Forms.View`](xref:Xamarin.Forms.View) 类。 在本机视图上设置可绑定属性或附加的可绑定属性会将属性值传输到包装。 例如，可以通过 `View.HorizontalOptions="Center"` 在本机视图上设置来指定居中的水平布局。

> [!NOTE]
> 请注意，样式不能用于本机视图，因为样式只能以对象支持的属性为目标 `BindableProperty` 。

Android 小组件构造函数通常要求将 Android `Context` 对象作为参数，这可以通过类中的静态属性来使用 `MainActivity` 。 因此，在 XAML 中创建 Android 小组件时， `Context` 通常必须使用 `x:Arguments` 具有标记扩展的属性将对象传递给小组件的构造函数 `x:Static` 。 有关详细信息，请参阅 [将参数传递给本机视图](#pass-arguments-to-native-views)。

> [!NOTE]
> 请注意， `x:Name` 不能在 .NET Standard 库项目或 (SAP) 的共享资产项目中将本机视图命名为。 这样做将生成本机类型的变量，这将导致编译错误。 但是，本机视图可以包装在实例中， `ContentView` 并在代码隐藏文件中检索，前提是使用了 SAP。 有关详细信息，请参阅 [从代码引用本机视图](#refer-to-native-views-from-code)。

## <a name="native-bindings"></a>本机绑定

数据绑定用于将 UI 与其数据源同步，并简化 Xamarin.Forms 应用程序显示数据和与其数据交互的方式。 如果源对象实现 `INotifyPropertyChanged` 接口，则 *源* 对象中的更改会由绑定框架自动推送到 *目标* 对象，并且可以选择将 *目标* 对象中的更改推送到 *源* 对象。

本机视图的属性也可以使用数据绑定。 下面的代码示例演示如何使用本机视图的属性进行数据绑定：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:win="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255,
            Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:local="clr-namespace:NativeSwitch"
        x:Class="NativeSwitch.NativeSwitchPage">
    <StackLayout Margin="20">
        <Label Text="Native Views Demo" FontAttributes="Bold" HorizontalOptions="Center" />
        <Entry Placeholder="This Entry is bound to the native switch" IsEnabled="{Binding IsSwitchOn}" />
        <ios:UISwitch On="{Binding Path=IsSwitchOn, Mode=TwoWay, UpdateSourceEventName=ValueChanged}"
            OnTintColor="{x:Static ios:UIColor.Red}"
            ThumbTintColor="{x:Static ios:UIColor.Blue}" />
        <androidWidget:Switch x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
            Checked="{Binding Path=IsSwitchOn, Mode=TwoWay, UpdateSourceEventName=CheckedChange}"
            Text="Enable Entry?" />
        <win:ToggleSwitch Header="Enable Entry?"
            OffContent="No"
            OnContent="Yes"
            IsOn="{Binding IsSwitchOn, Mode=TwoWay, UpdateSourceEventName=Toggled}" />
    </StackLayout>
</ContentPage>

```

页包含 [`Entry`](xref:Xamarin.Forms.Entry) 其 [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) 属性绑定到 `NativeSwitchPageViewModel.IsSwitchOn` 属性的。 页的将 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 设置为 `NativeSwitchPageViewModel` 代码隐藏文件中类的新实例，并使用 ViewModel 类实现 `INotifyPropertyChanged` 接口。

该页还包含每个平台的本机开关。 每个本机开关使用 [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) 绑定来更新属性的值 `NativeSwitchPageViewModel.IsSwitchOn` 。 因此，当开关处于关闭状态时，将被 `Entry` 禁用，当开关处于开启状态时，将 `Entry` 启用。 以下屏幕截图显示了每个平台上的此功能：

![已启用本机交换机禁用的 ](xaml-images/native-switch-disabled.png)
 ![ 本机切换](xaml-images/native-switch-enabled.png)

如果本机属性实现 `INotifyPropertyChanged` 或支持 (KVO) 在 iOS 上，或在 UWP 上为，则自动支持双向绑定 `DependencyProperty` 。 但是，许多本机视图不支持属性更改通知。 对于这些视图，您可以 [`UpdateSourceEventName`](xref:Xamarin.Forms.Binding.UpdateSourceEventName) 将属性值指定为绑定表达式的一部分。 应将此属性设置为本机视图中的事件名称，该事件指示目标属性已更改。 然后，当本机开关的值发生更改时， `Binding` 会通知类用户已更改开关值，并 `NativeSwitchPageViewModel.IsSwitchOn` 更新属性值。

## <a name="pass-arguments-to-native-views"></a>将自变量传递给本机视图

构造函数参数可以使用 `x:Arguments` 具有标记扩展的属性传递给本机视图 `x:Static` 。 此外，本机视图工厂方法 (`public static` 方法，这些方法返回与定义方法的类或结构相同的类型的对象或值) 可以通过使用特性指定方法的名称 `x:FactoryMethod` ，并使用属性来调用 `x:Arguments` 。

下面的代码示例演示了这两种方法：

```xaml
<ContentPage ...
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidGraphics="clr-namespace:Android.Graphics;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:winControls="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255, Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:winMedia="clr-namespace:Windows.UI.Xaml.Media;assembly=Windows, Version=255.255.255.255, Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:winText="clr-namespace:Windows.UI.Text;assembly=Windows, Version=255.255.255.255, Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:winui="clr-namespace:Windows.UI;assembly=Windows, Version=255.255.255.255, Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows">
        ...
        <ios:UILabel Text="Simple Native Color Picker" View.HorizontalOptions="Center">
            <ios:UILabel.Font>
                <ios:UIFont x:FactoryMethod="FromName">
                    <x:Arguments>
                        <x:String>Papyrus</x:String>
                        <x:Single>24</x:Single>
                    </x:Arguments>
                </ios:UIFont>
            </ios:UILabel.Font>
        </ios:UILabel>
        <androidWidget:TextView x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
                    Text="Simple Native Color Picker"
                    TextSize="24"
                    View.HorizontalOptions="Center">
            <androidWidget:TextView.Typeface>
                <androidGraphics:Typeface x:FactoryMethod="Create">
                    <x:Arguments>
                        <x:String>cursive</x:String>
                        <androidGraphics:TypefaceStyle>Normal</androidGraphics:TypefaceStyle>
                    </x:Arguments>
                </androidGraphics:Typeface>
            </androidWidget:TextView.Typeface>
        </androidWidget:TextView>
        <winControls:TextBlock Text="Simple Native Color Picker"
                    FontSize="20"
                    FontStyle="{x:Static winText:FontStyle.Italic}"
                    View.HorizontalOptions="Center">
            <winControls:TextBlock.FontFamily>
                <winMedia:FontFamily>
                    <x:Arguments>
                        <x:String>Georgia</x:String>
                    </x:Arguments>
                </winMedia:FontFamily>
            </winControls:TextBlock.FontFamily>
        </winControls:TextBlock>
        ...
</ContentPage>
```

[`UIFont.FromName`](xref:UIKit.UIFont.FromName*)工厂方法用于 [`UILabel.Font`](xref:UIKit.UILabel.Font) 在 iOS 上将属性设置为新的 [`UIFont`](xref:UIKit.UIFont) 。 `UIFont`名称和大小由作为属性的子元素的方法参数指定 `x:Arguments` 。

[`Typeface.Create`](xref:Android.Graphics.Typeface.Create*)工厂方法用于 [`TextView.Typeface`](xref:Android.Widget.TextView.Typeface) 在 Android 上将属性设置为新的 [`Typeface`](xref:Android.Graphics.Typeface) 。 `Typeface`系列名称和样式由作为属性的子元素的方法参数指定 `x:Arguments` 。

[`FontFamily`](/uwp/api/Windows.UI.Xaml.Media.FontFamily)构造函数用于将 [`TextBlock.FontFamily`](/uwp/api/Windows.UI.Xaml.Controls.TextBlock) `FontFamily` 通用 Windows 平台 (UWP) 上的新属性设置为新的。 该 `FontFamily` 名称由作为属性的子的方法参数指定 `x:Arguments` 。

> [!NOTE]
> 参数必须与构造函数或工厂方法所需的类型相匹配。

下面的屏幕截图显示了指定工厂方法和构造函数参数以在不同的本机视图上设置字体的结果：

![在本机视图上设置字体](xaml-images/passing-arguments.png)

有关在 XAML 中传递参数的详细信息，请参阅 [在 xaml 中传递参数](~/xamarin-forms/xaml/passing-arguments.md)。

## <a name="refer-to-native-views-from-code"></a>从代码中引用本机视图

尽管不能将本机视图命名为属性，但可以 `x:Name` 从共享访问项目中的代码隐藏文件中检索在 XAML 文件中声明的本机视图实例，前提是本机视图是 [`ContentView`](xref:Xamarin.Forms.ContentView) 指定属性值的的子 `x:Name` 。 然后，在代码隐藏文件中的条件编译指令的内部，应执行以下操作：

1. 检索 [`ContentView.Content`](xref:Xamarin.Forms.ContentView.Content) 属性值并将其转换为特定于平台的 `NativeViewWrapper` 类型。
1. 检索 `NativeViewWrapper.NativeElement` 属性，并将其转换为本机视图类型。

然后，可以在本机视图上调用本机 API 来执行所需的操作。 此方法还提供了一个优点，即不同平台的多个 XAML 本机视图可以是同一的子视图 [`ContentView`](xref:Xamarin.Forms.ContentView) 。 下面的代码示例演示了此方法：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:winControls="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255,
            Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:local="clr-namespace:NativeViewInsideContentView"
        x:Class="NativeViewInsideContentView.NativeViewInsideContentViewPage">
    <StackLayout Margin="20">
        <ContentView x:Name="contentViewTextParent" HorizontalOptions="Center" VerticalOptions="CenterAndExpand">
            <ios:UILabel Text="Text in a UILabel" TextColor="{x:Static ios:UIColor.Red}" />
            <androidWidget:TextView x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
                Text="Text in a TextView" />
              <winControls:TextBlock Text="Text in a TextBlock" />
        </ContentView>
        <ContentView x:Name="contentViewButtonParent" HorizontalOptions="Center" VerticalOptions="EndAndExpand">
            <ios:UIButton TouchUpInside="OnButtonTap" View.HorizontalOptions="Center" View.VerticalOptions="Center" />
            <androidWidget:Button x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
                Text="Scale and Rotate Text"
                Click="OnButtonTap" />
            <winControls:Button Content="Scale and Rotate Text" />
        </ContentView>
    </StackLayout>
</ContentPage>
```

在上面的示例中，每个平台的本机视图都是 [`ContentView`](xref:Xamarin.Forms.ContentView) 控件的子控件， `x:Name` 属性值用于 `ContentView` 在代码隐藏中检索：

```csharp
public partial class NativeViewInsideContentViewPage : ContentPage
{
    public NativeViewInsideContentViewPage()
    {
        InitializeComponent();

#if __IOS__
        var wrapper = (Xamarin.Forms.Platform.iOS.NativeViewWrapper)contentViewButtonParent.Content;
        var button = (UIKit.UIButton)wrapper.NativeView;
        button.SetTitle("Scale and Rotate Text", UIKit.UIControlState.Normal);
        button.SetTitleColor(UIKit.UIColor.Black, UIKit.UIControlState.Normal);
#endif
#if __ANDROID__
        var wrapper = (Xamarin.Forms.Platform.Android.NativeViewWrapper)contentViewTextParent.Content;
        var textView = (Android.Widget.TextView)wrapper.NativeView;
        textView.SetTextColor(Android.Graphics.Color.Red);
#endif
#if WINDOWS_UWP
        var textWrapper = (Xamarin.Forms.Platform.UWP.NativeViewWrapper)contentViewTextParent.Content;
        var textBlock = (Windows.UI.Xaml.Controls.TextBlock)textWrapper.NativeElement;
        textBlock.Foreground = new Windows.UI.Xaml.Media.SolidColorBrush(Windows.UI.Colors.Red);
        var buttonWrapper = (Xamarin.Forms.Platform.UWP.NativeViewWrapper)contentViewButtonParent.Content;
        var button = (Windows.UI.Xaml.Controls.Button)buttonWrapper.NativeElement;
        button.Click += (sender, args) => OnButtonTap(sender, EventArgs.Empty);
#endif
    }

    async void OnButtonTap(object sender, EventArgs e)
    {
        contentViewButtonParent.Content.IsEnabled = false;
        contentViewTextParent.Content.ScaleTo(2, 2000);
        await contentViewTextParent.Content.RotateTo(360, 2000);
        contentViewTextParent.Content.ScaleTo(1, 2000);
        await contentViewTextParent.Content.RelRotateTo(360, 2000);
        contentViewButtonParent.Content.IsEnabled = true;
    }
}
```

[`ContentView.Content`](xref:Xamarin.Forms.ContentView.Content)访问属性以将包装的本机视图作为特定于平台的实例检索 `NativeViewWrapper` 。 然后，将 `NativeViewWrapper.NativeElement` 访问该属性以检索本机视图的本机类型。 然后调用本机视图的 API 来执行所需的操作。

IOS 和 Android 本机按钮共享同一 `OnButtonTap` 事件处理程序，因为每个本机按钮都将使用一个 `EventHandler` 委托来响应触控事件。 但是，通用 Windows 平台 (UWP) 使用单独的 `RoutedEventHandler` ，后者又使用 `OnButtonTap` 此示例中的事件处理程序。 因此，当单击 "本机" 按钮时， `OnButtonTap` 事件处理程序将执行，该事件处理程序会缩放和旋转包含在指定的中的本机控件 [`ContentView`](xref:Xamarin.Forms.ContentView) `contentViewTextParent` 。 下面的屏幕截图演示了每个平台上出现的情况：

![包含本机控件的 ContentView](xaml-images/contentview.png)

## <a name="subclass-native-views"></a>子类本机视图

许多 iOS 和 Android 本机视图不适合在 XAML 中进行实例化，因为它们使用方法而不是属性来设置控件。 此问题的解决方法是包装中的本机视图的子类，这些包装器定义更易于 XAML 的 API，该 API 使用属性来设置控件，并使用与平台无关的事件。 然后，可以将换行的本机视图置于共享资产项目 (SAP) 并使用条件编译指令进行放置，或者放置在平台特定的项目中，并在 .NET Standard 库项目中从 XAML 进行引用。

下面的代码示例演示了一个 Xamarin.Forms 使用子类本机视图的页面：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:iosLocal="clr-namespace:SubclassedNativeControls.iOS;assembly=SubclassedNativeControls.iOS;targetPlatform=iOS"
        xmlns:android="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SubclassedNativeControls.Droid;assembly=SubclassedNativeControls.Droid;targetPlatform=Android"
        xmlns:winControls="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255,
            Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:local="clr-namespace:SubclassedNativeControls"
        x:Class="SubclassedNativeControls.SubclassedNativeControlsPage">
    <StackLayout Margin="20">
        <Label Text="Subclassed Native Views Demo" FontAttributes="Bold" HorizontalOptions="Center" />
        <StackLayout Orientation="Horizontal">
          <Label Text="You have chosen:" />
          <Label Text="{Binding SelectedFruit}" />      
        </StackLayout>
        <iosLocal:MyUIPickerView ItemsSource="{Binding Fruits}"
            SelectedItem="{Binding SelectedFruit, Mode=TwoWay, UpdateSourceEventName=SelectedItemChanged}" />
        <androidLocal:MySpinner x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
            ItemsSource="{Binding Fruits}"
            SelectedObject="{Binding SelectedFruit, Mode=TwoWay, UpdateSourceEventName=ItemSelected}" />
        <winControls:ComboBox ItemsSource="{Binding Fruits}"
            SelectedItem="{Binding SelectedFruit, Mode=TwoWay, UpdateSourceEventName=SelectionChanged}" />
    </StackLayout>
</ContentPage>
```

页面包含一个 [`Label`](xref:Xamarin.Forms.Label) ，它显示用户从本机控件选择的水果。 `Label`绑定到 `SubclassedNativeControlsPageViewModel.SelectedFruit` 属性。 页的将 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 设置为 `SubclassedNativeControlsPageViewModel` 代码隐藏文件中类的新实例，并使用 ViewModel 类实现 `INotifyPropertyChanged` 接口。

该页还包含每个平台的本机选取器视图。 每个本机视图都通过将其属性绑定到集合来显示水果的集合 `ItemSource` `SubclassedNativeControlsPageViewModel.Fruits` 。 这允许用户选取水果，如以下屏幕截图所示：

![子类本机视图](xaml-images/sub-classed.png)

在 iOS 和 Android 上，本机选取器使用方法设置控件。 因此，这些选取器必须是子类才能公开属性，以使其能够识别 XAML。 在通用 Windows 平台 (UWP) 上， `ComboBox` 已经是 XAML 友好的，因此不需要进行子类化。

### <a name="ios"></a>iOS

IOS 实现为视图提供子类 [`UIPickerView`](xref:UIKit.UIPickerView) ，并公开可从 XAML 轻松使用的属性和事件：

```csharp
public class MyUIPickerView : UIPickerView
{
    public event EventHandler<EventArgs> SelectedItemChanged;

    public MyUIPickerView()
    {
        var model = new PickerModel();
        model.ItemChanged += (sender, e) =>
        {
            if (SelectedItemChanged != null)
            {
                SelectedItemChanged.Invoke(this, e);
            }
        };
        Model = model;
    }

    public IList<string> ItemsSource
    {
        get
        {
            var pickerModel = Model as PickerModel;
            return (pickerModel != null) ? pickerModel.Items : null;
        }
        set
        {
            var model = Model as PickerModel;
            if (model != null)
            {
                model.Items = value;
            }
        }
    }

    public string SelectedItem
    {
        get { return (Model as PickerModel).SelectedItem; }
        set { }
    }
}
```

`MyUIPickerView`类公开 `ItemsSource` 和属性以及 `SelectedItem` `SelectedItemChanged` 事件。 [`UIPickerView`](xref:UIKit.UIPickerView)需要一个基础 [`UIPickerViewModel`](xref:UIKit.UIPickerViewModel) 数据模型，由 `MyUIPickerView` 属性和事件访问。 `UIPickerViewModel`数据模型由 `PickerModel` 类提供：

```csharp
class PickerModel : UIPickerViewModel
{
    int selectedIndex = 0;
    public event EventHandler<EventArgs> ItemChanged;
    public IList<string> Items { get; set; }

    public string SelectedItem
    {
        get
        {
            return Items != null && selectedIndex >= 0 && selectedIndex < Items.Count ? Items[selectedIndex] : null;
        }
    }

    public override nint GetRowsInComponent(UIPickerView pickerView, nint component)
    {
        return Items != null ? Items.Count : 0;
    }

    public override string GetTitle(UIPickerView pickerView, nint row, nint component)
    {
        return Items != null && Items.Count > row ? Items[(int)row] : null;
    }

    public override nint GetComponentCount(UIPickerView pickerView)
    {
        return 1;
    }

    public override void Selected(UIPickerView pickerView, nint row, nint component)
    {
        selectedIndex = (int)row;
        if (ItemChanged != null)
        {
            ItemChanged.Invoke(this, new EventArgs());
        }
    }
}
```

`PickerModel`类通过属性为类提供基础存储 `MyUIPickerView` `Items` 。 每当中的选定项发生更改时，都会 `MyUIPickerView` [`Selected`](xref:UIKit.UIPickerViewModel.Selected*) 执行方法，该方法将更新所选的索引并引发 `ItemChanged` 事件。 这可确保 `SelectedItem` 属性将始终返回用户选取的最后一项。 此外， `PickerModel` 类会重写用于设置实例的方法 `MyUIPickerView` 。

### <a name="android"></a>Android

Android 实现为视图提供子类 [`Spinner`](xref:Android.Widget.Spinner) ，并公开可从 XAML 轻松使用的属性和事件：

```csharp
class MySpinner : Spinner
{
    ArrayAdapter adapter;
    IList<string> items;

    public IList<string> ItemsSource
    {
        get { return items; }
        set
        {
            if (items != value)
            {
                items = value;
                adapter.Clear();

                foreach (string str in items)
                {
                    adapter.Add(str);
                }
            }
        }
    }

    public string SelectedObject
    {
        get { return (string)GetItemAtPosition(SelectedItemPosition); }
        set
        {
            if (items != null)
            {
                int index = items.IndexOf(value);
                if (index != -1)
                {
                    SetSelection(index);
                }
            }
        }
    }

    public MySpinner(Context context) : base(context)
    {
        ItemSelected += OnBindableSpinnerItemSelected;

        adapter = new ArrayAdapter(context, Android.Resource.Layout.SimpleSpinnerItem);
        adapter.SetDropDownViewResource(Android.Resource.Layout.SimpleSpinnerDropDownItem);
        Adapter = adapter;
    }

    void OnBindableSpinnerItemSelected(object sender, ItemSelectedEventArgs args)
    {
        SelectedObject = (string)GetItemAtPosition(args.Position);
    }
}
```

`MySpinner`类公开 `ItemsSource` 和属性以及 `SelectedObject` `ItemSelected` 事件。 类显示的项 `MySpinner` 由 [`Adapter`](xref:Android.Widget.Adapter) 与视图关联的提供，在 `Adapter` `ItemsSource` 首次设置属性时，项将填充到中。 每当类中的选定项 `MySpinner` 发生更改时， `OnBindableSpinnerItemSelected` 事件处理程序都将更新 `SelectedObject` 属性。

## <a name="related-links"></a>相关链接

- [NativeSwitch (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeswitch)
- [Forms2Native (示例) ](/samples/xamarin/xamarin-forms-samples/forms2native)
- [NativeViewInsideContentView (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeviewinsidecontentview)
- [SubclassedNativeControls (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-subclassednativecontrols)
- [本机窗体](~/xamarin-forms/platform/native-forms.md)
- [在 XAML 中传递参数](~/xamarin-forms/xaml/passing-arguments.md)