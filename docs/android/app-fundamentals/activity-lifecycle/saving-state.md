---
title: 演练 - 保存活动状态
description: 在活动生命周期指南中，我们已介绍了保存状态的理论，现在，让我们看一个示例。
ms.prod: xamarin
ms.assetid: A6090101-67C6-4BDD-9416-F2FB74805A87
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: f6197c2d8ffc028f28291134efa0d3dd87375cc8
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91456358"
---
# <a name="walkthrough---saving-the-activity-state"></a>演练 - 保存活动状态

_在活动生命周期指南中，我们已介绍了保存状态的理论，现在，让我们看一个示例。_

## <a name="activity-state-walkthrough"></a>活动状态演练

让我们在[ActivityLifecycle](/samples/xamarin/monodroid-samples/activitylifecycle)) 示例中打开**ActivityLifecycle_Start**项目 (，将其生成并运行。 这是一个非常简单的项目，其中包含两个活动来演示活动生命周期和如何调用各种生命周期方法。 当你启动应用程序时， `MainActivity` 会显示的屏幕：

[![活动 A 屏幕](saving-state-images/01-activity-a-sml.png)](saving-state-images/01-activity-a.png#lightbox)

### <a name="viewing-state-transitions"></a>查看状态转换

此示例中的每个方法都将写入 IDE 应用程序的输出窗口以指示活动状态。  (若要在 Visual Studio 中打开 "输出" 窗口，请键入 **CTRL-ALT-O**;若要在 Visual Studio for Mac 中打开 "输出" 窗口，请单击 " **查看 > 填充 > 应用程序输出**" ) 。

当应用程序首次启动时，"输出" 窗口将显示 *活动 A*的状态更改： 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
```

单击 " **启动活动 b** " 按钮时，会看到 *活动 a* 暂停并停止，同时 *活动 B* 经历其状态更改： 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.SecondActivity] Activity B - OnCreate
[ActivityLifecycle.SecondActivity] Activity B - OnStart
[ActivityLifecycle.SecondActivity] Activity B - OnResume
[ActivityLifecycle.MainActivity] Activity A - OnStop
```

因此， *活动 B* 会启动并显示，代替 *活动 a*： 

[![活动 B 屏幕](saving-state-images/02-activity-b-sml.png)](saving-state-images/02-activity-b.png#lightbox)

当我们单击 " **后退** " 按钮时， *活动 B* 被销毁， *活动 A* 恢复： 

```shell
[ActivityLifecycle.SecondActivity] Activity B - OnPause
[ActivityLifecycle.MainActivity] Activity A - OnRestart
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
[ActivityLifecycle.SecondActivity] Activity B - OnStop
[ActivityLifecycle.SecondActivity] Activity B - OnDestroy
```

### <a name="adding-a-click-counter"></a>添加 Click 计数器

接下来，我们将更改应用程序，以便我们有一个用于统计并显示其单击次数的按钮。 首先，让我们将一个 `_counter` 实例变量添加到 `MainActivity` ：

```csharp
int _counter = 0;
```

接下来，让我们编辑 **Resource/layout/main.axml** 布局文件，并添加一个新 `clickButton` 的，其中显示用户单击了该按钮的次数。 生成的 **main.axml** 应如下所示： 

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <Button
        android:id="@+id/myButton"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/mybutton_text" />
    <Button
        android:id="@+id/clickButton"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/counterbutton_text" />
</LinearLayout>
```

在此代码中，将以下代码添加到 [OnCreate](xref:Android.App.Activity.OnCreate*) 方法的末尾 `MainActivity` &ndash; `clickButton` ：

```csharp
var clickbutton = FindViewById<Button> (Resource.Id.clickButton);
clickbutton.Text = Resources.GetString (
    Resource.String.counterbutton_text, _counter);
clickbutton.Click += (object sender, System.EventArgs e) =>
{
    _counter++;
    clickbutton.Text = Resources.GetString (
        Resource.String.counterbutton_text, _counter);
} ;
```

当我们再次生成并运行应用程序时，将显示一个新按钮，该按钮将 `_counter` 在每次单击时递增并显示的值：

[![添加触控计数](saving-state-images/03-touched-sml.png)](saving-state-images/03-touched.png#lightbox)

但当我们将设备旋转到横向模式时，此计数会丢失：

[![旋转到横向会将计数重新设置为零](saving-state-images/05-rotate-nosave-sml.png)](saving-state-images/05-rotate-nosave.png#lightbox)

检查应用程序输出，我们看到 *活动 A* 暂停、停止、销毁、重新创建、重新启动，然后在从纵向模式旋转到横向模式时恢复： 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.MainActivity] Activity A - OnStop
[ActivityLifecycle.MainActivity] Activity A - On Destroy

[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
```

由于在旋转设备时 *活动 A* 会销毁并再次重新创建，因此其实例状态将丢失。 接下来，我们将添加代码来保存和还原实例状态。

### <a name="adding-code-to-preserve-instance-state"></a>添加代码以保留实例状态

让我们添加一个方法来 `MainActivity` 保存实例状态。 销毁 *活动 A* 之前，Android 会自动调用 [OnSaveInstanceState](xref:Android.App.Activity.OnSaveInstanceState*) ，并传入可用于存储实例状态的 [捆绑包](xref:Android.OS.Bundle) 。 让我们使用它将我们的单击计数保存为整数值：

```csharp
protected override void OnSaveInstanceState (Bundle outState)
{
    outState.PutInt ("click_count", _counter);
    Log.Debug(GetType().FullName, "Activity A - Saving instance state");

    // always call the base implementation!
    base.OnSaveInstanceState (outState);    
}
```

重新创建并恢复 *活动 A* 时，Android 会将其传递 `Bundle` 回 `OnCreate` 方法。 将代码添加到 `OnCreate` 以 `_counter` 从传入的还原值 `Bundle` 。 紧靠定义的行之前添加以下代码 `clickbutton` ： 

```csharp
if (bundle != null)
{
    _counter = bundle.GetInt ("click_count", 0);
    Log.Debug(GetType().FullName, "Activity A - Recovered instance state");
}
```

再次生成并运行应用程序，然后单击第二个按钮几次。 当我们将设备旋转到横向模式时，将保留计数！

[![旋转屏幕显示四个保留的计数](saving-state-images/06-rotate-save-sml.png)](saving-state-images/06-rotate-save.png#lightbox)

让我们看看 "输出" 窗口以了解发生了什么情况：

```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.MainActivity] Activity A - Saving instance state
[ActivityLifecycle.MainActivity] Activity A - OnStop
[ActivityLifecycle.MainActivity] Activity A - On Destroy

[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - Recovered instance state
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
```

在调用 [OnStop](xref:Android.App.Activity.OnStop) 方法之前， `OnSaveInstanceState` 调用新方法以将 `_counter` 值保存在中 `Bundle` 。 `Bundle`在调用我们的方法时，Android 会将此传回给我们 `OnCreate` ，我们可以使用它将值还原 `_counter` 到我们离开的位置。

## <a name="summary"></a>总结

在此演练中，我们已使用活动生命周期的知识来保留状态数据。

## <a name="related-links"></a>相关链接

- [ActivityLifecycle (示例) ](/samples/xamarin/monodroid-samples/activitylifecycle)
- [活动生命周期](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Android 活动](xref:Android.App.Activity)