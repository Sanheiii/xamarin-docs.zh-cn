---
title: 演练-在 Xamarin 中使用本地通知
description: 本演练演示如何在 Xamarin Android 应用程序中使用本地通知。 它演示了创建和发布本地通知的基础知识。 用户单击通知区域中的通知时，会启动第二个活动。
ms.prod: xamarin
ms.assetid: D8C6C9E2-3282-49D1-A2F6-78A4F3306E29
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/16/2018
ms.openlocfilehash: 09603fa86bce18c7cfdc141938267ee54710842c
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91455695"
---
# <a name="walkthrough---using-local-notifications-in-xamarinandroid"></a>演练-在 Xamarin 中使用本地通知

_本演练演示如何在 Xamarin Android 应用程序中使用本地通知。它演示了创建和发布本地通知的基础知识。用户单击通知区域中的通知时，会启动第二个活动。_

## <a name="overview"></a>概述

在本演练中，我们将创建一个 Android 应用程序，该应用程序会在用户单击活动中的按钮时引发通知。 当用户单击通知时，它将启动另一个活动，该活动显示用户单击第一个活动中的按钮的次数。

以下屏幕截图演示了此应用程序的一些示例：

[![带有通知的示例屏幕快照](local-notifications-walkthrough-images/1-overview-sml.png)](local-notifications-walkthrough-images/1-overview.png#lightbox)

> [!NOTE]
> 本指南重点介绍[Android 支持库](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)中的[notificationcompat.builder api](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.html) 。 这些 Api 将确保 (API 级别 14) 的最大向后兼容4.0 性。

## <a name="creating-the-project"></a>创建项目

首先，让我们使用 **Android 应用程序** 模板创建一个新的 android 项目。 让我们调用此项目 **LocalNotifications**。  (如果您不熟悉如何创建 Xamarin Android 项目，请参阅 [Hello、Android](~/android/get-started/hello-android/hello-android-quickstart.md). ) 

编辑资源文件 **值/Strings.xml** ，使其包含创建通知通道时将使用的两个额外字符串资源：

```xml
<?xml version="1.0" encoding="utf-8"?>

<resources>
  <string name="Hello">Hello World, Click Me!</string>
  <string name="ApplicationName">Notifications</string>

  <string name="channel_name">Local Notifications</string>
  <string name="channel_description">The count from MainActivity.</string>
</resources>
```

### <a name="add-the-androidsupportv4-nuget-package"></a>添加 Android. Support NuGet 包

在本演练中，我们将使用 `NotificationCompat.Builder` 来构建我们的本地通知。 如 [本地通知](~/android/app-fundamentals/notifications/local-notifications.md)中所述，我们必须在项目中包含 [Android 支持库 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) NuGet 才能使用 `NotificationCompat.Builder` 。

接下来，让我们编辑 **MainActivity.cs** ，并添加以下 `using` 语句，以便代码中的类型 `Android.Support.V4.App` 可以使用：

```csharp
using Android.Support.V4.App;
```

此外，还必须让编译器清楚地说，我们使用的 `Android.Support.V4.App` 是版本， `TaskStackBuilder` 而不是 `Android.App` 版本。 添加以下 `using` 语句以解决任何歧义：

```csharp
using TaskStackBuilder = Android.Support.V4.App.TaskStackBuilder;
```

### <a name="create-the-notification-channel"></a>创建通知通道

接下来，添加一个方法， `MainActivity` 该方法将创建通知通道 (如有必要) ：

```csharp
void CreateNotificationChannel()
{
    if (Build.VERSION.SdkInt < BuildVersionCodes.O)
    {
        // Notification channels are new in API 26 (and not a part of the
        // support library). There is no need to create a notification
        // channel on older versions of Android.
        return;
    }

    var name = Resources.GetString(Resource.String.channel_name);
    var description = GetString(Resource.String.channel_description);
    var channel = new NotificationChannel(CHANNEL_ID, name, NotificationImportance.Default)
                  {
                      Description = description
                  };

    var notificationManager = (NotificationManager) GetSystemService(NotificationService);
    notificationManager.CreateNotificationChannel(channel);
}
```

更新 `OnCreate` 方法以调用此新方法：

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);

    CreateNotificationChannel();
}
```

### <a name="define-the-notification-id"></a>定义通知 ID

我们需要提供通知和通知通道的唯一 ID。 让我们编辑 **MainActivity.cs** ，并将以下静态实例变量添加到 `MainActivity` 类：

```csharp
static readonly int NOTIFICATION_ID = 1000;
static readonly string CHANNEL_ID = "location_notification";
internal static readonly string COUNT_KEY = "count";
```

### <a name="add-code-to-generate-the-notification"></a>添加代码以生成通知

接下来，需要为按钮事件创建新的事件处理程序 `Click` 。 将以下方法添加到 `MainActivity`：

```csharp
void ButtonOnClick(object sender, EventArgs eventArgs)
{
    // Pass the current button press count value to the next activity:
    var valuesForActivity = new Bundle();
    valuesForActivity.PutInt(COUNT_KEY, count);

    // When the user clicks the notification, SecondActivity will start up.
    var resultIntent = new Intent(this, typeof(SecondActivity));

    // Pass some values to SecondActivity:
    resultIntent.PutExtras(valuesForActivity);

    // Construct a back stack for cross-task navigation:
    var stackBuilder = TaskStackBuilder.Create(this);
    stackBuilder.AddParentStack(Class.FromType(typeof(SecondActivity)));
    stackBuilder.AddNextIntent(resultIntent);

    // Create the PendingIntent with the back stack:
    var resultPendingIntent = stackBuilder.GetPendingIntent(0, (int) PendingIntentFlags.UpdateCurrent);

    // Build the notification:
    var builder = new NotificationCompat.Builder(this, CHANNEL_ID)
                  .SetAutoCancel(true) // Dismiss the notification from the notification area when the user clicks on it
                  .SetContentIntent(resultPendingIntent) // Start up this activity when the user clicks the intent.
                  .SetContentTitle("Button Clicked") // Set the title
                  .SetNumber(count) // Display the count in the Content Info
                  .SetSmallIcon(Resource.Drawable.ic_stat_button_click) // This is the icon to display
                  .SetContentText($"The button has been clicked {count} times."); // the message to display.

    // Finally, publish the notification:
    var notificationManager = NotificationManagerCompat.From(this);
    notificationManager.Notify(NOTIFICATION_ID, builder.Build());

    // Increment the button press count:
    count++;
}
```

`OnCreate`MainActivity 的方法必须进行调用以创建通知通道，并将 `ButtonOnClick` 方法分配给按钮的事件， `Click` (替换模板提供的委托事件处理程序) ：

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);

    CreateNotificationChannel();

    // Display the "Hello World, Click Me!" button and register its event handler:
    var button = FindViewById<Button>(Resource.Id.MyButton);
    button.Click += ButtonOnClick;
}
```

### <a name="create-a-second-activity"></a>创建第二个活动

现在，我们需要创建一个在用户单击通知时将显示的其他活动。 向项目中添加另一个名为 **SecondActivity**的 Android 活动。 打开 **SecondActivity.cs** ，并将其内容替换为以下代码：

```csharp
using System;
using Android.App;
using Android.OS;
using Android.Widget;

namespace LocalNotifications
{
    [Activity(Label = "Second Activity")]
    public class SecondActivity : Activity
    {
        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);

            // Get the count value passed to us from MainActivity:
            var count = Intent.Extras.GetInt(MainActivity.COUNT_KEY, -1);

            // No count was passed? Then just return.
            if (count <= 0)
            {
                return;
            }

            // Display the count sent from the first activity:
            SetContentView(Resource.Layout.Second);
            var txtView = FindViewById<TextView>(Resource.Id.textView1);
            txtView.Text = $"You clicked the button {count} times in the previous activity.";
        }
    }
}
```

还必须为 **SecondActivity**创建资源布局。 向项目添加一个名为**main.axml**的新**Android 布局**文件。 编辑 **main.axml** 并粘贴以下布局代码：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:minWidth="25px"
    android:minHeight="25px">
    <TextView
        android:text=""
        android:textAppearance="?android:attr/textAppearanceLarge"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:id="@+id/textView1" />
</LinearLayout>
```

### <a name="add-a-notification-icon"></a>添加通知图标

最后，添加一个小图标，该图标将在启动通知时出现在通知区域中。 您可以将 [此图标](local-notifications-walkthrough-images/ic-stat-button-click.png) 复制到您的项目或创建您自己的自定义图标。 将该图标文件命名为 **ic \_ stat \_ 按钮 \_click.png** 并将其复制到 **资源/可绘制** 文件夹。 请记住，使用 " **添加 > 现有项 ...** " 将此图标文件包含在项目中。

### <a name="run-the-application"></a>运行此应用程序

生成并运行应用程序。 你应该会看到第一个活动，类似于以下屏幕截图：

[![第一个活动屏幕截图](local-notifications-walkthrough-images/2-start-screen-sml.png)](local-notifications-walkthrough-images/2-start-screen.png#lightbox)

单击该按钮时，应会看到通知区域显示了通知的小图标：

[![显示通知图标](local-notifications-walkthrough-images/3-notification-icon-sml.png)](local-notifications-walkthrough-images/3-notification-icon.png#lightbox)

如果向下轻扫并公开通知抽屉，应会看到通知：

[![通知消息](local-notifications-walkthrough-images/4-notifications-sml.png)](local-notifications-walkthrough-images/4-notifications.png#lightbox)

当你单击该通知时，它应会消失，而我们的其他活动的外观应与 &ndash; 以下屏幕截图类似：

[![第二个活动屏幕快照](local-notifications-walkthrough-images/5-second-activity-sml.png)](local-notifications-walkthrough-images/5-second-activity.png#lightbox)

恭喜！ 此时，你已完成 Android 本地通知演练，你有一个可以引用的有效示例。 通知比此处显示的更多，因此，如果需要详细信息，请参阅 [Google 的文档通知](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)。

## <a name="summary"></a>总结

此演练用于 `NotificationCompat.Builder` 创建和显示通知。 其中显示了一个基本示例，说明如何启动第二个活动，作为响应用户与通知的交互的方式，并演示了从第一个活动到第二个活动的数据传输。

## <a name="related-links"></a>相关链接

- [LocalNotifications (示例) ](/samples/xamarin/monodroid-samples/localnotifications)
- [Android Oreo 通知通道](https://blog.xamarin.com/android-oreo-notification-channels/)
- [通知](xref:Android.App.Notification)
- [NotificationManager](xref:Android.App.NotificationManager)
- [Notificationcompat.builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html)
- [PendingIntent](xref:Android.App.PendingIntent)