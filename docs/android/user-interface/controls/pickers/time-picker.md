---
title: 时间选取器
description: 使用 TimePickerDialog 和 DialogFragment 选择时间
ms.prod: xamarin
ms.assetid: EB4E8206-E8AD-9F04-AC1C-82AC9364A9DD
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: 0f6082476a7fe4ebd8a6a3f52c23951d58ac1383
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453732"
---
# <a name="android-time-picker"></a>Android 时间选取器

若要为用户提供一种选择时间的方法，可以使用 [TimePicker](xref:Android.Widget.TimePicker)。 Android 应用通常使用 `TimePicker` With [TimePickerDialog](xref:Android.App.TimePickerDialog) 来选择时间值， &ndash; 这有助于确保跨设备和应用程序的一致接口。 `TimePicker` 允许用户选择24小时或12小时上午/下午模式下的时间。
`TimePickerDialog` 是一个帮助器类，用于 `TimePicker` 在对话框中封装。

[![操作中时间选取器对话框的示例屏幕截图](time-picker-images/01-example-screen-sml.png)](time-picker-images/01-example-screen.png#lightbox)

## <a name="overview"></a>概述

新式 Android 应用程序 `TimePickerDialog` 在 [DialogFragment](xref:Android.App.DialogFragment)中显示。 这样，应用程序便可以将显示 `TimePicker` 为弹出对话框或将其嵌入活动中。 此外，还 `DialogFragment` 管理了对话框的生命周期和显示，从而减少了必须实现的代码量。

本指南演示如何使用 `TimePickerDialog` 包装在中的 `DialogFragment` 。 `TimePickerDialog`当用户单击活动上的按钮时，示例应用程序将显示为模式对话框。 如果用户设置了时间，对话框将退出，并且处理程序将 `TextView` 使用所选时间更新活动屏幕上的。

## <a name="requirements"></a>要求

本指南的示例应用程序面向 Android 4.1 (API 级别
16) 或更高版本，但可用于 Android 3.0 (API 级别11或更高版本) 。 可以支持早期版本的 Android，并向项目中添加 Android 支持库 v4 和一些代码更改。

## <a name="using-the-timepicker"></a>使用 TimePicker

此示例扩展了 `DialogFragment` ; `DialogFragment` 下面 (调用的子类实现 `TimePickerFragment`) 宿主并显示 `TimePickerDialog` 。 首次启动示例应用时，它会在将用于显示选定时间的上方显示 " **选取时间** " 按钮 `TextView` ：

[![初始示例应用屏幕](time-picker-images/02-initial-app-screen-sml.png)](time-picker-images/02-initial-app-screen.png#lightbox)

单击 " **选取时间** " 按钮时，示例应用会启动， `TimePickerDialog` 如以下屏幕截图所示：

[![应用显示的默认时间选取器对话框的屏幕截图](time-picker-images/03-am-pm-time-dialog-sml.png)](time-picker-images/03-am-pm-time-dialog.png#lightbox)

在中 `TimePickerDialog` ，选择时间并单击 **"确定"** 按钮将导致 `TimePickerDialog` 调用 [IOnTimeSetListener 方法 OnTimeSet](xref:Android.App.TimePickerDialog.IOnTimeSetListener.OnTimeSet*)。
此接口由宿主 `DialogFragment` (实现 `TimePickerFragment` ，如下) 所述。 单击 " **取消** " 按钮会使片段和对话框解除。

`DialogFragment` 通过以下三种方式之一返回托管活动的所选时间：

1. **调用方法或设置属性** &ndash; 活动可以提供专门用于设置此值的属性或方法。

2. **引发事件** &ndash;`DialogFragment`可定义在调用时将引发的事件 `OnTimeSet` 。

3. **使用 `Action` **&ndash; `DialogFragment` 可以调用 `Action<DateTime>` 来显示活动中的时间。 在实例化时，该活动将提供 `Action<DateTime` `DialogFragment` 。

此示例将使用第三种方法，该方法要求活动提供 `Action<DateTime>` 对的处理程序 `DialogFragment` 。

## <a name="start-an-app-project"></a>启动应用项目

启动名为 **TimePickerDemo** (的新 Android 项目。如果不熟悉如何创建 Xamarin Android 项目，请参阅 [Hello，Android](~/android/get-started/hello-android/hello-android-quickstart.md) 了解如何创建新项目) 。

编辑 **Resources/layout/main.axml** ，并将其内容替换为以下 XML：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:layout_gravity="center_horizontal"
    android:padding="16dp">
    <Button
        android:id="@+id/select_button"
        android:paddingLeft="24dp"
        android:paddingRight="24dp"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="PICK TIME"
        android:textSize="20dp" />
    <TextView
        android:id="@+id/time_display"
        android:layout_height="wrap_content"
        android:layout_width="match_parent"
        android:paddingTop="22dp"
        android:text="Picked time will be displayed here"
        android:textSize="24dp" />
</LinearLayout>
```

这是一个基本 [LinearLayout](xref:Android.Widget.LinearLayout) ，其中包含 [TextView](xref:Android.Widget.TextView) ，用于显示时间和打开的 [按钮](xref:Android.Widget.Button) `TimePickerDialog` 。 请注意，此布局使用硬编码的字符串和维度，使应用程序更简单、更易于理解 &ndash; 生产应用程序通常使用这些值的资源 (如 [DatePicker](https://github.com/xamarin/recipes/blob/master/Recipes/android/controls/datepicker/select_a_date/Resources/layout/Main.axml) 代码示例) 中所示。

编辑 **MainActivity.cs** 并将其内容替换为以下代码：

```csharp
using Android.App;
using Android.Widget;
using Android.OS;
using System;
using Android.Util;
using Android.Text.Format;

namespace TimePickerDemo
{
    [Activity(Label = "TimePickerDemo", MainLauncher = true, Icon = "@drawable/icon")]
    public class MainActivity : Activity
    {
        TextView timeDisplay;
        Button timeSelectButton;

        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);
            SetContentView(Resource.Layout.Main);
            timeDisplay = FindViewById<TextView>(Resource.Id.time_display);
            timeSelectButton = FindViewById<Button>(Resource.Id.select_button);
        }
    }
}
```

当你生成并运行此示例时，应会看到如下屏幕截图所示的初始屏幕：

[![初始应用屏幕](time-picker-images/02-initial-app-screen-sml.png)](time-picker-images/02-initial-app-screen.png#lightbox)

单击 " **选取时间** " 按钮不会执行任何操作，因为尚未 `DialogFragment` 实现来显示 `TimePicker` 。
下一步是创建此 `DialogFragment` 。

## <a name="extending-dialogfragment"></a>扩展 DialogFragment

若要扩展 `DialogFragment` 以便与一起使用 `TimePicker` ，必须创建派生自 `DialogFragment` 并实现的子类 `TimePickerDialog.IOnTimeSetListener` 。 将以下类添加到 **MainActivity.cs**：

```csharp
public class TimePickerFragment : DialogFragment, TimePickerDialog.IOnTimeSetListener
{
    public static readonly string TAG = "MyTimePickerFragment";
    Action<DateTime> timeSelectedHandler = delegate { };

    public static TimePickerFragment NewInstance(Action<DateTime> onTimeSelected)
    {
        TimePickerFragment frag = new TimePickerFragment();
        frag.timeSelectedHandler = onTimeSelected;
        return frag;
    }

    public override Dialog OnCreateDialog (Bundle savedInstanceState)
    {
        DateTime currentTime = DateTime.Now;
        bool is24HourFormat = DateFormat.Is24HourFormat(Activity);
        TimePickerDialog dialog = new TimePickerDialog
            (Activity, this, currentTime.Hour, currentTime.Minute, is24HourFormat);
        return dialog;
    }

    public void OnTimeSet(TimePicker view, int hourOfDay, int minute)
    {
        DateTime currentTime = DateTime.Now;
        DateTime selectedTime = new DateTime(currentTime.Year, currentTime.Month, currentTime.Day, hourOfDay, minute, 0);
        Log.Debug(TAG, selectedTime.ToLongTimeString());
        timeSelectedHandler (selectedTime);
    }
}
```

此 `TimePickerFragment` 类分为更小部分，并在下一部分中进行说明。

### <a name="dialogfragment-implementation"></a>DialogFragment 实现

`TimePickerFragment` 实现多种方法：工厂方法、对话框实例化方法和 `OnTimeSet` 所需的处理程序方法 `TimePickerDialog.IOnTimeSetListener` 。

- `TimePickerFragment` 是的子类 `DialogFragment` 。 它还实现 `TimePickerDialog.IOnTimeSetListener` 接口 (也就是说，它提供所需的 `OnTimeSet` 方法) ：

    ```csharp
    public class TimePickerFragment : DialogFragment, TimePickerDialog.IOnTimeSetListener
    ```

- `TAG` 出于日志记录目的进行初始化 (可以将 *MyTimePickerFragment* 更改为要使用) 任何字符串。 `timeSelectedHandler`操作初始化为空委托，以防止出现空引用异常：

    ```csharp
    public static readonly string TAG = "MyTimePickerFragment";
    Action<DateTime> timeSelectedHandler = delegate { };
    ```

- `NewInstance`调用工厂方法来实例化新的 `TimePickerFragment` 。 此方法使用 `Action<DateTime>` 用户在中单击 **"确定"** 按钮时调用的处理程序 `TimePickerDialog` ：

    ```csharp
    public static TimePickerFragment NewInstance(Action<DateTime> onTimeSelected)
    {
        TimePickerFragment frag = new TimePickerFragment();
        frag.timeSelectedHandler = onTimeSelected;
        return frag;
    }
    ```

- 显示片段时，Android 会调用 `DialogFragment` 方法 [OnCreateDialog](xref:Android.App.DialogFragment.OnCreateDialog*)。
    此方法创建一个新的 `TimePickerDialog` 对象并将其初始化为活动、回调对象 (该对象是) 的当前实例 `TimePickerFragment` 以及当前时间：

    ```csharp
    public override Dialog OnCreateDialog (Bundle savedInstanceState)
    {
        DateTime currentTime = DateTime.Now;
        bool is24HourFormat = DateFormat.Is24HourFormat(Activity);
        TimePickerDialog dialog = new TimePickerDialog
            (Activity, this, currentTime.Hour, currentTime.Minute, is24HourFormat);
        return dialog;
    }
    ```

- 当用户在对话框中更改时间设置时 `TimePicker` ，将 `OnTimeSet` 调用方法。 `OnTimeSet``DateTime`使用当前日期创建一个对象，并在用户选择)  (小时和分钟合并时合并：

    ```csharp
    public void OnTimeSet(TimePicker view, int hourOfDay, int minute)
    {
        DateTime currentTime = DateTime.Now;
        DateTime selectedTime = new DateTime(currentTime.Year, currentTime.Month, currentTime.Day, hourOfDay, minute, 0);
    ```

- 此 `DateTime` 对象将传递到在 `timeSelectedHandler` `TimePickerFragment` 创建时向对象注册的。 `OnTimeSet` 调用此处理程序，将活动的时间显示更新到所选时间 (此处理程序在下一节中实现) ：

    ```csharp
    timeSelectedHandler (selectedTime);
    ```

## <a name="displaying-the-timepickerfragment"></a>显示 TimePickerFragment

现在 `DialogFragment` 已经实现了，现在可以使用工厂方法来实例化， `DialogFragment` `NewInstance` 并通过调用 [DialogFragment](xref:Android.App.DialogFragment.Show*)来显示它：

将以下方法添加到 `MainActivity`：

```csharp
void TimeSelectOnClick (object sender, EventArgs eventArgs)
{
    TimePickerFragment frag = TimePickerFragment.NewInstance (
        delegate (DateTime time)
        {
            timeDisplay.Text = time.ToShortTimeString();
        });

    frag.Show(FragmentManager, TimePickerFragment.TAG);
}
```

在 `TimeSelectOnClick` 实例化后 `TimePickerFragment` ，它会在委托中创建并传入匿名方法，该方法使用传入的时间值更新活动的时间显示。 最后，它 `TimePicker` 通过 `DialogFragment.Show`) 向用户显示 (来启动对话框片段 `TimePicker` 。

在方法的末尾 `OnCreate` ，添加以下行，以将事件处理程序附加到启动对话框的 " **选择时间** " 按钮：

```csharp
timeSelectButton.Click += TimeSelectOnClick;
```

单击 " **选取时间** " 按钮时， `TimeSelectOnClick` 将调用以向用户显示 `TimePicker` 对话片段。

## <a name="try-it"></a>尝试一下！

构建并运行应用。 单击 " **选取时间** " 按钮时，将 `TimePickerDialog` 显示为活动 (的默认时间格式，在本例中为12小时 AM/PM 模式) ：

[![时间对话框在上午/下午模式下显示](time-picker-images/03-am-pm-time-dialog-sml.png)](time-picker-images/03-am-pm-time-dialog.png#lightbox)
   
单击对话框中的 **"确定"** 后 `TimePicker` ，处理程序将 `TextView` 用选定的时间更新活动的，然后退出：

[![活动 TextView 中显示了/M 时间](time-picker-images/04-after-time-dialog-sml.png)](time-picker-images/04-after-time-dialog.png#lightbox)

接下来，将以下代码行添加到 `OnCreateDialog` 在 `is24HourFormat` 声明和初始化后立即添加：

```csharp
is24HourFormat = true;
```

此更改强制将传递给构造函数的标志设置为， `TimePickerDialog` `true` 以便使用24小时模式，而不是承载活动的时间格式。 再次生成并运行应用时，请单击 " **选取时间** " 按钮， `TimePicker` 此时对话框将显示为24小时格式：

[![24小时格式的 TimePicker 对话框](time-picker-images/05-24hr-time-dialog-sml.png)](time-picker-images/05-24hr-time-dialog.png#lightbox)

由于处理程序调用 [ToShortTimeString](xref:System.DateTime.ToShortDateString*) 将时间打印到活动的时间 `TextView` ，因此仍按默认的12小时 AM/PM 格式打印该时间。

## <a name="summary"></a>总结

本文介绍了如何将 `TimePicker` 小组件显示为 Android 活动中的弹出窗口模式对话框。 它提供了一个示例 `DialogFragment` 实现并讨论了 `IOnTimeSetListener` 接口。 此示例还演示了如何与 `DialogFragment` 主机活动交互以显示选定的时间。

## <a name="related-links"></a>相关链接

- [DialogFragment](xref:Android.App.DialogFragment)
- [TimePicker](xref:Android.Widget.TimePicker)
- [TimePickerDialog](xref:Android.App.TimePickerDialog)
- [TimePickerDialog.IOnTimeSetListener](xref:Android.App.TimePickerDialog.IOnTimeSetListener)
- [TimePickerDemo (示例) ](/samples/xamarin/monodroid-samples/userinterface-timepickerdemo)