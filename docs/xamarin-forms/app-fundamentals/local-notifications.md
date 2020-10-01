---
title: Xamarin.Forms 本地通知
description: 本文介绍如何在 Xamarin.Forms 中发送和接收本地通知。
ms.prod: xamarin
ms.assetid: 60460F57-63C6-4916-BBB5-A870F1DF53D7
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 10/10/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8c62d03e785e02160d9c0f83c3375383d890d62b
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91558266"
---
# <a name="local-notifications-in-no-locxamarinforms"></a>Xamarin.Forms 中的本地通知

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/local-notifications)

本地通知是安装在移动设备上的应用程序发送的警报。 本地通知通常用于如下功能：

- 日历事件
- 提醒
- 基于位置的触发器

每个平台以不同的方式处理本地通知的创建、显示和使用。 本文介绍如何创建跨平台抽象以使用 Xamarin.Forms 发送和接收本地通知。

[![iOS 和 Android 上的本地通知应用程序](local-notifications-images/local-notifications-msg-cropped.png)](local-notifications-images/local-notifications-msg.png#lightbox)

## <a name="create-a-cross-platform-interface"></a>创建跨平台接口

Xamarin.Forms 应用程序应创建和使用通知，而无需考虑基础平台实现。 以下 `INotificationManager` 接口在共享代码库中实现，并定义了应用程序可用于与通知交互的跨平台 API：

```csharp
public interface INotificationManager
{
    event EventHandler NotificationReceived;

    void Initialize();

    int ScheduleNotification(string title, string message);

    void ReceiveNotification(string title, string message);
}
```

此接口将在每个平台项目中实现。 `NotificationReceived` 事件允许应用程序处理传入通知。 `Initialize` 方法应执行准备通知系统所需的任何本机平台逻辑。 `ScheduleNotification` 方法应发送通知。 当收到消息时，`ReceiveNotification` 方法应由基础平台调用。

## <a name="consume-the-interface-in-no-locxamarinforms"></a>在 Xamarin.Forms 使用接口

创建接口后，即使尚未创建平台实现，也可以在共享 Xamarin.Forms 项目中使用该接口。 示例应用程序包含名为 MainPage.xaml 的 **，其中包含以内容`ContentPage`** ：

```xaml
<StackLayout Margin="0,35,0,0"
             x:Name="stackLayout">
    <Label Text="Click the button to create a local notification."
           TextColor="Red"
           HorizontalOptions="Center"
           VerticalOptions="Start" />
    <Button Text="Create Notification"
            HorizontalOptions="Center"
            VerticalOptions="Start"
            Clicked="OnScheduleClick"/>
</StackLayout>
```

布局包含 `Label` 元素（其中包含面向用户的说明）以及应在点击时计划通知的 `Button`。

`MainPage` 类代码隐藏处理通知的发送和接收：

```csharp
public partial class MainPage : ContentPage
{
    INotificationManager notificationManager;
    int notificationNumber = 0;

    public MainPage()
    {
        InitializeComponent();

        notificationManager = DependencyService.Get<INotificationManager>();
        notificationManager.NotificationReceived += (sender, eventArgs) =>
        {
            var evtData = (NotificationEventArgs)eventArgs;
            ShowNotification(evtData.Title, evtData.Message);
        };
    }

    void OnScheduleClick(object sender, EventArgs e)
    {
        notificationNumber++;
        string title = $"Local Notification #{notificationNumber}";
        string message = $"You have now received {notificationNumber} notifications!";
        notificationManager.ScheduleNotification(title, message);
    }

    void ShowNotification(string title, string message)
    {
        Device.BeginInvokeOnMainThread(() =>
        {
            var msg = new Label()
            {
                Text = $"Notification Received:\nTitle: {title}\nMessage: {message}"
            };
            stackLayout.Children.Add(msg);
        });
    }
}
```

`MainPage` 类构造函数使用 Xamarin.Forms `DependencyService` 检索 `INotificationManager` 的特定于平台的实例。 `OnScheduleClicked` 方法使用 `INotificationManager` 实例计划新通知。 `ShowNotification` 方法是从附加到 `NotificationReceived` 事件的事件处理程序中调用的，并在调用该事件时将新的 `Label` 插入到页面中。

`NotificationReceived` 事件处理程序将其事件参数强制转换为 `NotificationEventArgs`。 此类型在共享 Xamarin.Forms 项目中定义：

```csharp
public class NotificationEventArgs : EventArgs
{
    public string Title { get; set; }
    public string Message { get; set; }
}
```

有关 Xamarin.Forms `DependencyService` 的详细信息，请参阅 [Xamarin.Forms DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/introduction.md)。

## <a name="create-the-android-interface-implementation"></a>创建 Android 接口实现

要使 Xamarin.Forms 应用程序在 Android 上发送和接收通知，应用程序必须提供 `INotificationManager` 接口的实现。

### <a name="create-the-androidnotificationmanager-class"></a>创建 AndroidNotificationManager 类

`AndroidNotificationManager` 类实现 `INotificationManager` 接口：

```csharp
using Android.Support.V4.App;
using Xamarin.Forms;
using AndroidApp = Android.App.Application;

[assembly: Dependency(typeof(LocalNotifications.Droid.AndroidNotificationManager))]
namespace LocalNotifications.Droid
{
    public class AndroidNotificationManager : INotificationManager
    {
        const string channelId = "default";
        const string channelName = "Default";
        const string channelDescription = "The default channel for notifications.";
        const int pendingIntentId = 0;

        public const string TitleKey = "title";
        public const string MessageKey = "message";

        bool channelInitialized = false;
        int messageId = -1;
        NotificationManager manager;

        public event EventHandler NotificationReceived;

        public void Initialize()
        {
            CreateNotificationChannel();
        }

        public int ScheduleNotification(string title, string message)
        {
            if (!channelInitialized)
            {
                CreateNotificationChannel();
            }

            messageId++;

            Intent intent = new Intent(AndroidApp.Context, typeof(MainActivity));
            intent.PutExtra(TitleKey, title);
            intent.PutExtra(MessageKey, message);

            PendingIntent pendingIntent = PendingIntent.GetActivity(AndroidApp.Context, pendingIntentId, intent, PendingIntentFlags.OneShot);

            NotificationCompat.Builder builder = new NotificationCompat.Builder(AndroidApp.Context, channelId)
                .SetContentIntent(pendingIntent)
                .SetContentTitle(title)
                .SetContentText(message)
                .SetLargeIcon(BitmapFactory.DecodeResource(AndroidApp.Context.Resources, Resource.Drawable.xamagonBlue))
                .SetSmallIcon(Resource.Drawable.xamagonBlue)
                .SetDefaults((int)NotificationDefaults.Sound | (int)NotificationDefaults.Vibrate);

            var notification = builder.Build();
            manager.Notify(messageId, notification);

            return messageId;
        }

        public void ReceiveNotification(string title, string message)
        {
            var args = new NotificationEventArgs()
            {
                Title = title,
                Message = message,
            };
            NotificationReceived?.Invoke(null, args);
        }

        void CreateNotificationChannel()
        {
            manager = (NotificationManager)AndroidApp.Context.GetSystemService(AndroidApp.NotificationService);

            if (Build.VERSION.SdkInt >= BuildVersionCodes.O)
            {
                var channelNameJava = new Java.Lang.String(channelName);
                var channel = new NotificationChannel(channelId, channelNameJava, NotificationImportance.Default)
                {
                    Description = channelDescription
                };
                manager.CreateNotificationChannel(channel);
            }

            channelInitialized = true;
        }
    }
}
```

命名空间上方的 `assembly` 属性使用 `DependencyService` 注册 `INotificationManager` 接口实现。

Android 允许应用程序定义多个通知通道。 `Initialize` 方法创建示例应用程序用于发送通知的基本通道。 `ScheduleNotification` 方法定义创建和发送通知所需的特定于平台的逻辑。 最后，在收到消息时，`ReceiveNotification` 方法将由 Android OS 调用，并调用事件处理程序。

> [!NOTE]
> `Application` 类在 `Xamarin.Forms` 和 `Android.App` 命名空间中定义，因此 `AndroidApp` 别名在 `using` 语句中定义以区分这两者。

### <a name="handle-incoming-notifications-on-android"></a>处理 Android 中的传入通知

`MainActivity` 类必须检测传入通知并通知 `AndroidNotificationManager` 实例。 `MainActivity` 类上的 `Activity` 属性应指定 `LaunchMode.SingleTop` 的 `LaunchMode` 值：

```csharp
[Activity(
        //...
        LaunchMode = LaunchMode.SingleTop]
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
    {
        // ...
    }
```

当应用程序在前台时，`SingleTop` 模式会阻止启动 `Activity` 的多个实例。 此 `LaunchMode` 可能不适合在更复杂的通知方案中启动多个活动的应用程序。 有关 `LaunchMode` 枚举值的详细信息，请参阅 [Android Activity LaunchMode ](https://developer.android.com/guide/topics/manifest/activity-element#lmode)。

在 `MainActivity` 中，将修改以接收传入通知：

```csharp
protected override void OnCreate(Bundle savedInstanceState)
{
    // ...

    global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
    LoadApplication(new App());
    CreateNotificationFromIntent(Intent);
}

protected override void OnNewIntent(Intent intent)
{
    CreateNotificationFromIntent(intent);
}

void CreateNotificationFromIntent(Intent intent)
{
    if (intent?.Extras != null)
    {
        string title = intent.Extras.GetString(AndroidNotificationManager.TitleKey);
        string message = intent.Extras.GetString(AndroidNotificationManager.MessageKey);
        DependencyService.Get<INotificationManager>().ReceiveNotification(title, message);
    }
}
```

`CreateNotificationFromIntent` 方法从 `intent` 参数中提取通知数据，并使用 `ReceiveNotification` 方法将其提供给 `AndroidNotificationManager`。 `CreateNotificationFromIntent` 方法是从 `OnCreate` 方法和 `OnNewIntent` 方法中调用的：

- 当通过通知数据启动应用程序时，`Intent` 数据将传递到 `OnCreate` 方法。
- 如果应用程序已在前台，则 `Intent` 数据将传递到 `OnNewIntent` 方法。

Android 为通知提供多个高级选项。 有关详细信息，请参阅 [Xamarin.Android 中的通知](~/android/app-fundamentals/notifications/index.md)。

## <a name="create-the-ios-interface-implementation"></a>创建 iOS 接口实现

要使 Xamarin.Forms 应用程序在 iOS 上发送和接收通知，应用程序必须提供 `INotificationManager` 的实现。

### <a name="create-the-iosnotificationmanager-class"></a>创建 iOSNotificationManager 类

`iOSNotificationManager` 类实现 `INotificationManager` 接口：

```csharp
[assembly: Dependency(typeof(LocalNotifications.iOS.iOSNotificationManager))]
namespace LocalNotifications.iOS
{
    public class iOSNotificationManager : INotificationManager
    {
        int messageId = -1;

        bool hasNotificationsPermission;

        public event EventHandler NotificationReceived;

        public void Initialize()
        {
            // request the permission to use local notifications
            UNUserNotificationCenter.Current.RequestAuthorization(UNAuthorizationOptions.Alert, (approved, err) =>
            {
                hasNotificationsPermission = approved;
            });
        }

        public int ScheduleNotification(string title, string message)
        {
            // EARLY OUT: app doesn't have permissions
            if(!hasNotificationsPermission)
            {
                return -1;
            }

            messageId++;

            var content = new UNMutableNotificationContent()
            {
                Title = title,
                Subtitle = "",
                Body = message,
                Badge = 1
            };

            // Local notifications can be time or location based
            // Create a time-based trigger, interval is in seconds and must be greater than 0
            var trigger = UNTimeIntervalNotificationTrigger.CreateTrigger(0.25, false);

            var request = UNNotificationRequest.FromIdentifier(messageId.ToString(), content, trigger);
            UNUserNotificationCenter.Current.AddNotificationRequest(request, (err) =>
            {
                if (err != null)
                {
                    throw new Exception($"Failed to schedule notification: {err}");
                }
            });

            return messageId;
        }

        public void ReceiveNotification(string title, string message)
        {
            var args = new NotificationEventArgs()
            {
                Title = title,
                Message = message
            };
            NotificationReceived?.Invoke(null, args);
        }
    }
}
```

命名空间上方的 `assembly` 属性使用 `DependencyService` 注册 `INotificationManager` 接口实现。

在 iOS 上，必须先请求使用通知的权限，然后再尝试计划通知。 `Initialize` 方法请求使用本地通知的授权。 `ScheduleNotification` 方法定义创建和发送通知所需的逻辑。 最后，在收到消息时，`ReceiveNotification` 方法将由 iOS 调用，并调用事件处理程序。

### <a name="handle-incoming-notifications-on-ios"></a>处理 iOS 中的传入通知

在 iOS 上，必须创建属于 `UNUserNotificationCenterDelegate` 的子类的委托来处理传入消息。 示例应用程序定义 `iOSNotificationReceiver` 类：

```csharp
public class iOSNotificationReceiver : UNUserNotificationCenterDelegate
{
    public override void WillPresentNotification(UNUserNotificationCenter center, UNNotification notification, Action<UNNotificationPresentationOptions> completionHandler)
    {
        DependencyService.Get<INotificationManager>().ReceiveNotification(notification.Request.Content.Title, notification.Request.Content.Body);

        // alerts are always shown for demonstration but this can be set to "None"
        // to avoid showing alerts if the app is in the foreground
        completionHandler(UNNotificationPresentationOptions.Alert);
    }
}
```

此类使用 `DependencyService` 获取 `iOSNotificationManager` 类的实例，并向 `ReceiveNotification` 方法提供传入通知数据。

在应用程序启动过程中，`AppDelegate` 类必须指定自定义委托。 在应用程序启动过程中，`AppDelegate` 类必须将 `iOSNotificationReceiver` 对象指定为 `UNUserNotificationCenter` 委托。 `FinishedLaunching` 方法中会出现这种情况：

```csharp
public override bool FinishedLaunching(UIApplication app, NSDictionary options)
{
    global::Xamarin.Forms.Forms.Init();

    UNUserNotificationCenter.Current.Delegate = new iOSNotificationReceiver();

    LoadApplication(new App());
    return base.FinishedLaunching(app, options);
}
```

iOS 为通知提供多个高级选项。 有关详细信息，请参阅 [Xamarin.iOS 中的通知](~/ios/platform/user-notifications/index.md)。

## <a name="test-the-application"></a>测试应用程序

平台项目包含 `INotificationManager` 接口的已注册实现后，可在两个平台上测试应用程序。 运行应用程序并单击“计划通知”按钮以创建通知。

在 Android 上，通知区域中会出现通知。 当点击通知时，应用程序会收到通知，并在“计划通知”按钮下方显示消息：

![Android 中的本地通知](local-notifications-images/local-notifications-android.png)

在 iOS 上，传入通知自动由应用程序接收，而无需用户输入。 应用程序会收到通知，并在“计划通知”按钮下方显示消息：

![iOS 中的本地通知](local-notifications-images/local-notifications-ios.png)

## <a name="related-links"></a>相关链接

- [示例项目](/samples/xamarin/xamarin-forms-samples/local-notifications)
- [Xamarin.Android 中的通知](~/android/app-fundamentals/notifications/index.md)
- [Xamarin.iOS 中的通知](~/ios/platform/user-notifications/index.md)
- [Xamarin.Forms Dependency.Service](~/xamarin-forms/app-fundamentals/dependency-service/introduction.md)