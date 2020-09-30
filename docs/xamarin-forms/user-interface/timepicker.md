---
title: Xamarin.Forms TimePicker
description: TimePicker 是 Xamarin.Forms 允许用户选择时间的视图。 本文介绍如何在应用程序中使用 TimePicker Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: 2E99FB23-B82D-4EB4-AFB3-5002E736E7B2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/16/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9b913d9d310b4b2f1b560d06256f18e7532e1834
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556524"
---
# <a name="no-locxamarinforms-timepicker"></a>Xamarin.Forms TimePicker

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker)

_Xamarin.Forms允许用户选择时间的视图。_

Xamarin.Forms [`TimePicker`](xref:Xamarin.Forms.TimePicker) 调用平台的时间选取器控件，并允许用户选择时间。 `TimePicker` 定义以下属性:

- [`Time`](xref:Xamarin.Forms.TimePicker.Time) 对于类型 `TimeSpan` ，则为所选时间，默认值为 `TimeSpan` 0。 此 `TimeSpan` 类型指示自午夜后的持续时间。
- [`Format`](xref:Xamarin.Forms.TimePicker.Format) 类型为的 `string` [标准](/dotnet/standard/base-types/standard-date-and-time-format-strings/) 或 [自定义](/dotnet/standard/base-types/custom-date-and-time-format-strings/) .net 格式设置字符串，默认为 "t"，短时间模式。
- [`TextColor`](xref:Xamarin.Forms.TimePicker.TextColor) 类型的 [`Color`](xref:Xamarin.Forms.Color) ，用于显示选定时间的颜色，默认值为 [`Color.Default`](xref:Xamarin.Forms.Color.Default) 。
- [`FontAttributes`](xref:Xamarin.Forms.TimePicker.FontAttributes) 类型 [`FontAttributes`](xref:Xamarin.Forms.FontAttributes) 为的，默认为 [`FontAtributes.None`](xref:Xamarin.Forms.FontAttributes.None) 。
- [`FontFamily`](xref:Xamarin.Forms.TimePicker.FontFamily) 类型 `string` 为的，默认为 `null` 。
- [`FontSize`](xref:Xamarin.Forms.TimePicker.FontSize) 类型 `double` 为的，默认值为-1.0。
- `CharacterSpacing`，属于 `double` 类型，是 `TimePicker` 文本字符之间的间距。

所有这些属性都是由对象支持的 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，这意味着它们可以采用样式，属性可以是数据绑定的目标。 [`Time`](xref:Xamarin.Forms.TimePicker.Time)属性的默认绑定模式为 [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) ，这意味着它可以是使用[模型-视图-ViewModel (MVVM) ](~/xamarin-forms/enterprise-application-patterns/mvvm.md)体系结构的应用程序中数据绑定的目标。

[`TimePicker`](xref:Xamarin.Forms.TimePicker)不包括指示新选定值的事件 [`Time`](xref:Xamarin.Forms.TimePicker.Time) 。 如果需要得到通知，可以为事件添加处理程序 [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) 。

## <a name="initializing-the-time-property"></a>正在初始化时间属性

在代码中，可以将 [`Time`](xref:Xamarin.Forms.TimePicker.Time) 属性初始化为类型为的值 `TimeSpan` ：

```csharp
TimePicker timePicker = new TimePicker
{
  Time = new TimeSpan(4, 15, 26) // Time set to "04:15:26"
};
```

当在 [`Time`](xref:Xamarin.Forms.TimePicker.Time) XAML 中指定属性时，该值将转换为并进行 `TimeSpan` 验证，以确保毫秒数大于或等于0，并且小时数小于24。 应该用冒号分隔时间组件：

```xaml
<TimePicker Time="4:15:26" />
```

如果将的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 属性 [`TimePicker`](xref:Xamarin.Forms.TimePicker) 设置为 ViewModel 的实例，该实例包含名为的类型 `TimeSpan` 为 `SelectedTime` (例如) 的属性，则可以实例化， `TimePicker` 如下所示：

```xaml
<TimePicker Time="{Binding SelectedTime}" />
```

在此示例中，将 [`Time`](xref:Xamarin.Forms.TimePicker.Time) 属性初始化为 `SelectedTime` ViewModel 中的属性。 因为 `Time` 属性具有绑定模式 [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) ，所以用户选择的任何新时间都会自动传播到 ViewModel。

如果不 [`TimePicker`](xref:Xamarin.Forms.TimePicker) 包含其属性的绑定 [`Time`](xref:Xamarin.Forms.TimePicker.Time) ，应用程序应将处理程序附加到 [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) 事件，以便在用户选择新时间时收到通知。

有关设置字体属性的信息，请参阅 [字体](~/xamarin-forms/user-interface/text/fonts.md)。

## <a name="timepicker-and-layout"></a>TimePicker 和布局

可以通过以下方式使用不受约束的水平布局选项 `Center` `Start` ：、或 `End` [`TimePicker`](xref:Xamarin.Forms.TimePicker) ：

```xaml
<TimePicker ···
            HorizontalOptions="Center"
            ··· />
```

但是，不建议这样做。 根据属性的设置 [`Format`](xref:Xamarin.Forms.TimePicker.Format) ，所选时间可能需要不同的显示宽度。 例如，"T" 格式字符串会使 [`TimePicker`](xref:Xamarin.Forms.TimePicker) 视图以长格式显示时间，而 "4:15:26 am" 要求的显示宽度比短时间格式大 ( "T" ) "4:15 AM"。 根据具体的平台，这种差异可能会导致 `TimePicker` 视图更改布局中的宽度或使显示被截断。

> [!TIP]
> 最好使用带有的默认 `HorizontalOptions` 设置 `Fill` [`TimePicker`](xref:Xamarin.Forms.TimePicker) ，而不是在将其 `Auto` 置于单元中时使用的宽度 `TimePicker` `Grid` 。

## <a name="timepicker-in-an-application"></a>应用程序中的 TimePicker

[**SetTimer**](/samples/xamarin/xamarin-forms-samples/userinterface-timepicker)示例 [`TimePicker`](xref:Xamarin.Forms.TimePicker) [`Entry`](xref:Xamarin.Forms.Entry) [`Switch`](xref:Xamarin.Forms.Switch) 在其页面上包含、和视图。 `TimePicker`可以使用来选择一个时间，在该时间发生时，将显示一个警报对话框，该对话框会提醒用户 `Entry` （前提是在上切换了）中的文本 `Switch` 。 下面是 XAML 文件：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:SetTimer"
             x:Class="SetTimer.MainPage">
    <StackLayout>
        ...
        <Entry x:Name="_entry"
               Placeholder="Enter event to be reminded of" />
        <Label Text="Select the time below to be reminded at." />
        <TimePicker x:Name="_timePicker"
                    Time="11:00:00"
                    Format="T"
                    PropertyChanged="OnTimePickerPropertyChanged" />
        <StackLayout Orientation="Horizontal">
            <Label Text="Enable timer:" />
            <Switch x:Name="_switch"
                    HorizontalOptions="EndAndExpand"
                    Toggled="OnSwitchToggled" />
        </StackLayout>
    </StackLayout>
</ContentPage>
```

[`Entry`](xref:Xamarin.Forms.Entry)允许您输入将在所选时间出现时显示的提醒文本。 为 [`TimePicker`](xref:Xamarin.Forms.TimePicker) 指定了 [`Format`](xref:Xamarin.Forms.TimePicker.Format) 长时间格式的 "T" 的属性。 它有附加到事件的事件处理程序 [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) ，并且 [`Switch`](xref:Xamarin.Forms.Switch) 具有附加到其事件的处理程序 [`Toggled`](xref:Xamarin.Forms.Switch.Toggled) 。 这些事件处理程序在代码隐藏文件中，并调用 `SetTriggerTime` 方法：

```csharp
public partial class MainPage : ContentPage
{
    DateTime _triggerTime;

    public MainPage()
    {
        InitializeComponent();

        Device.StartTimer(TimeSpan.FromSeconds(1), OnTimerTick);
    }

    bool OnTimerTick()
    {
        if (_switch.IsToggled && DateTime.Now >= _triggerTime)
        {
            _switch.IsToggled = false;
            DisplayAlert("Timer Alert", "The '" + _entry.Text + "' timer has elapsed", "OK");
        }
        return true;
    }

    void OnTimePickerPropertyChanged(object sender, PropertyChangedEventArgs args)
    {
        if (args.PropertyName == "Time")
        {
            SetTriggerTime();
        }
    }

    void OnSwitchToggled(object sender, ToggledEventArgs args)
    {
        SetTriggerTime();
    }

    void SetTriggerTime()
    {
        if (_switch.IsToggled)
        {
            _triggerTime = DateTime.Today + _timePicker.Time;
            if (_triggerTime < DateTime.Now)
            {
                _triggerTime += TimeSpan.FromDays(1);
            }
        }
    }
}
```

`SetTriggerTime`方法基于 `DateTime.Today` 属性值和从返回的值计算计时器时间 `TimeSpan` [`TimePicker`](xref:Xamarin.Forms.TimePicker) 。 这是必需的，因为 `DateTime.Today` 属性返回一个 `DateTime` ，它指示当前日期，但时间为午夜。 如果计时器时间现在已经过去，则假定它是明天的时间。

计时器每秒计时，执行 `OnTimerTick` 检查是否 [`Switch`](xref:Xamarin.Forms.Switch) 打开以及当前时间是否大于或等于计时器时间的方法。 出现计时器时间时，该 [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) 方法会向用户显示警报对话框作为提醒。

首次运行该示例时，该 [`TimePicker`](xref:Xamarin.Forms.TimePicker) 视图将初始化为11am。 点击 " `TimePicker` 调用平台时间选择器"。 平台以不同的方式实现时间选取器，但对于该平台的用户而言，每种方法都很熟悉：

[![选择时间](timepicker-images/timepicker-open.png "选择时间")](timepicker-images/timepicker-open-large.png#lightbox "选择时间")

> [!TIP]
> 在 Android 上， [`TimePicker`](xref:Xamarin.Forms.TimePicker) 可以通过重写 `CreateTimePickerDialog` 自定义呈现器中的方法来自定义对话框。 例如，这允许向对话框添加其他按钮。

选择一段时间后，所选时间会显示在 [`TimePicker`](xref:Xamarin.Forms.TimePicker) ：

[![所选时间](timepicker-images/timepicker-selected.png "所选时间")](timepicker-images/timepicker-selected-large.png#lightbox "所选时间")

如果 [`Switch`](xref:Xamarin.Forms.Switch) 切换到 "开" 位置，应用程序将在 [`Entry`](xref:Xamarin.Forms.Entry) 发生所选时间时显示警报对话框，提醒用户的文本：

[![计时器弹出窗口](timepicker-images/timer-test.png "计时器弹出窗口")](timepicker-images/timer-test-large.png#lightbox "计时器弹出窗口")

一旦显示了警报对话框， [`Switch`](xref:Xamarin.Forms.Switch) 就会切换到关闭位置。

## <a name="related-links"></a>相关链接

- [SetTimer 示例](/samples/xamarin/xamarin-forms-samples/userinterface-timepicker)
- [TimePicker API](xref:Xamarin.Forms.TimePicker)