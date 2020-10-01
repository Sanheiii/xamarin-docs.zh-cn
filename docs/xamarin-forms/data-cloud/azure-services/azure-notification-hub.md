---
title: 使用 Azure 通知中心和 Xamarin.Forms 收发推送通知
description: 本文介绍如何使用 Azure 通知中心向 Xamarin.Forms 应用程序发送跨平台推送通知。
ms.prod: xamarin
ms.assetid: 07D13195-3A0D-4C95-ACF0-143A9084973C
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 11/27/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
- Firebase
ms.openlocfilehash: 721785fe2eeb35f0ef04d1a7854afe4039a66849
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91561828"
---
# <a name="send-and-receive-push-notifications-with-azure-notification-hubs-and-no-locxamarinforms"></a>使用 Azure 通知中心和 Xamarin.Forms 收发推送通知

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azurenotificationhub/)

推送通知将来自后端系统的信息传送到移动应用程序。 Apple、Google 和其他平台都有自己的推送通知服务 (PNS)。 利用 Azure 通知中心，你可以跨平台将通知集中在一处，使后端应用程序能够与单个中心通信，将通知分发到每个平台特定的 PNS。

请按照以下步骤将 Azure 通知中心集成到移动应用：

1. [设置推送通知服务和 Azure 通知中心](#set-up-push-notification-services-and-azure-notification-hub)。
1. [了解如何使用模板和标记](#register-templates-and-tags-with-the-azure-notification-hub)。
1. [创建跨平台 Xamarin.Forms 应用程序](#xamarinforms-application-functionality)。
1. [为原生 Android 项目配置推送通知](#configure-the-android-application-for-notifications)。
1. [为原生 iOS 项目配置推送通知](#configure-ios-for-notifications)。
1. [使用 Azure 通知中心测试通知](#test-notifications-in-the-azure-portal)。
1. [创建用于发送通知的后端应用程序](#create-a-notification-dispatcher)。

> [!NOTE]
> 如果还没有 [Azure 订阅](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)，可以在开始前创建一个[免费帐户](https://aka.ms/azfree-docs-mobileapps)。

## <a name="set-up-push-notification-services-and-azure-notification-hub"></a>设置推送通知服务和 Azure 通知中心

将 Azure 通知中心与 Xamarin.Forms 移动应用集成类似于将 Azure 通知中心与 Xamarin 原生应用程序集成。 按照[使用 Azure 通知中心向 Xamarin.Android 推送通知](/azure/notification-hubs/xamarin-notification-hubs-push-notifications-android-gcm#create-a-firebase-project-and-enable-firebase-cloud-messaging)中的 Firebase 控制台步骤，设置 Firebase Cloud Messaging (FCM) 应用程序。 按照 Xamarin.Android 教程完成以下步骤：

1. 定义 Android 包名称，如示例中使用的 `com.xamarin.notifysample`。
1. 从 Firebase 控制台中下载 `google-services.json`。 在后续步骤中，需要将此文件添加到 Android 应用程序。
1. 创建 Azure 通知中心实例并命名。 本文和示例使用 `xdocsnotificationhub` 中心名称。
1. 复制 FCM“服务器密钥”，并保存到 Azure 通知中心内 Google (GCM/FCM) 下的“API 密钥”**** **** ****。

以下屏幕截图显示了 Azure 通知中心内的 Google 平台配置：

![Azure 通知中心 Google 配置的屏幕截图](azure-notification-hub-images/fcm-notification-hub-config.png "Azure 通知中心 Google 配置")

需要使用 macOS 计算机来完成 iOS 设备的设置。 请按照[使用 Azure 通知中心向 Xamarin.iOS 推送通知](/azure/notification-hubs/xamarin-notification-hubs-ios-push-notification-apns-get-started#generate-the-certificate-signing-request-file)中的初始步骤设置 Apple Push Notification 服务 (APNS)。 按照 Xamarin.iOS 教程完成以下步骤：

1. 定义 iOS 捆绑标识符。 本文和示例使用 `com.xamarin.notifysample` 捆绑标识符。
1. 创建证书签名请求 (CSR) 文件，并使用它来生成推送通知证书。
1. 在 Azure 通知中心内的 Apple (APNS) 下上传推送通知证书****。

以下屏幕截图显示了 Azure 通知中心中内的 Apple 平台配置：

![Azure 通知中心 Apple 配置的屏幕截图](azure-notification-hub-images/apns-notification-hub-config.png "Azure 通知中心 Apple 配置")

## <a name="register-templates-and-tags-with-the-azure-notification-hub"></a>向 Azure 通知中心注册模板和标记

Azure 通知中心要求移动应用程序向中心注册、定义模板并订阅标记。 注册会将平台特定的 PNS 句柄链接到 Azure 通知中心内的标识符。 要了解有关注册的详细信息，请参阅[注册管理](/azure/notification-hubs/notification-hubs-push-notification-registration-management)。

设备通过模板来指定参数化消息模板。 可以按每个设备、每个标记自定义传入消息。 要了解有关模板的详细信息，请参阅[模板](/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages)。

标记可用于订阅消息类别，如新闻、体育和天气。 为简单起见，示例应用程序定义了一个默认模板，其中包含一个名为 `messageParam` 的参数和一个名为 `default` 的标记。 在更复杂的系统中，可使用用户特定的标记来跨设备向用户发送个性化通知。 有关标记的详细信息，请参阅[路由和标记表达式](/azure/notification-hubs/notification-hubs-tags-segment-push-message)。

要成功收到消息，每个原生应用程序必须执行以下步骤：

1. 从平台 PNS 获取 PNS 句柄或令牌。
1. 向 Azure 通知中心注册 PNS 句柄。
1. 指定所含参数与传出消息相同的模板。
1. 订阅传出消息所针对的标记。

[为 Android 应用程序配置通知](#configure-the-android-application-for-notifications)和[为 iOS 配置通知](#configure-ios-for-notifications)部分进一步详细说明了每个平台的操作步骤。

## <a name="no-locxamarinforms-application-functionality"></a>Xamarin.Forms 应用程序功能

示例 Xamarin.Forms 应用程序显示了一系列推送通知消息。 这通过 `AddMessage` 方法来实现，此方法将指定的推送通知消息添加到 UI。 此方法还可防止将重复的消息添加到 UI，并且它在主线程上运行，确保可从任何线程调用。 下面的代码演示了 `AddMessage` 方法：

```csharp
public void AddMessage(string message)
{
    Device.BeginInvokeOnMainThread(() =>
    {
        if (messageDisplay.Children.OfType<Label>().Where(c => c.Text == message).Any())
        {
            // Do nothing, an identical message already exists
        }
        else
        {
            Label label = new Label()
            {
                Text = message,
                HorizontalOptions = LayoutOptions.CenterAndExpand,
                VerticalOptions = LayoutOptions.Start
            };
            messageDisplay.Children.Add(label);
        }
    });
}
```

示例应用程序包含一个 `AppConstants.cs` 文件，此文件定义了平台项目使用的属性。 需要使用 Azure 通知中心内的值来自定义此文件。 以下代码显示了 `AppConstants.cs` 文件：

```csharp
public static class AppConstants
{
    public static string NotificationChannelName { get; set; } = "XamarinNotifyChannel";
    public static string NotificationHubName { get; set; } = "< Insert your Azure Notification Hub name >";
    public static string ListenConnectionString { get; set; } = "< Insert your DefaultListenSharedAccessSignature >";
    public static string DebugTag { get; set; } = "XamarinNotify";
    public static string[] SubscriptionTags { get; set; } = { "default" };
    public static string FCMTemplateBody { get; set; } = "{\"data\":{\"message\":\"$(messageParam)\"}}";
    public static string APNTemplateBody { get; set; } = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";
}
```

自定义 `AppConstants` 中的以下值，将示例应用程序连接到 Azure 通知中心：

* `NotificationHubName`：使用在 Azure 门户中创建的 Azure 通知中心的名称。
* `ListenConnectionString`：此值位于 Azure 通知中心内的“访问策略”下****。

以下屏幕截图显示了这些值在 Azure 门户中的位置：

![Azure 通知中心访问策略的屏幕截图](azure-notification-hub-images/notification-hub-access-policy.png "Azure 通知中心访问策略")

## <a name="configure-the-android-application-for-notifications"></a>为 Android 应用程序配置通知

完成以下步骤将 Android 应用程序配置为接收和处理通知：

1. 将 Android 包名称配置为与 Firebase 控制台中的包名称一致****。
1. 安装以下 NuGet 包以与 Google Play、Firebase 和 Azure 通知中心交互：
    1. `Xamarin.GooglePlayServices.Base`
    1. `Xamarin.Firebase.Messaging`
    1. `Xamarin.Azure.NotificationHubs.Android`
1. 将你在安装 FCM 的过程中下载的 `google-services.json` 文件复制到项目，并将“生成操作”设置为 `GoogleServicesJson`。
1. 将 `AndroidManifest.xml` [配置](#configure-android-manifest)为与 Firebase 通信。
1. [重写](#override-firebasemessagingservice-to-handle-messages) `FirebaseMessagingService` 以处理消息。
1. 将传入通知[添加](#add-incoming-notifications-to-the-xamarinforms-ui)到 Xamarin.Forms UI。

> [!NOTE]
> `GoogleServicesJson` 生成操作是 `Xamarin.GooglePlayServices.Base` NuGet 包的一部分。 Visual Studio 2019 在启动过程中设置可用的生成操作。 如果在“生成操作”部分未看到 `GoogleServicesJson`，请在安装 NuGet 包后重启 Visual Studio 2019。

### <a name="configure-android-manifest"></a>配置 Android 清单

`application` 元素中的 `receiver` 元素使应用能够与 Firebase 通信。 `uses-permission` 元素允许应用处理消息，并向 Azure 通知中心注册。 完整的 `AndroidManifest.xml` 应类似于以下示例：

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionCode="1" android:versionName="1.0" package="YOUR_PACKAGE_NAME" android:installLocation="auto">
  <uses-sdk android:minSdkVersion="21" />
  <uses-permission android:name="android.permission.INTERNET" />
  <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
  <uses-permission android:name="android.permission.WAKE_LOCK" />
  <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
  <application android:label="Notification Hub Sample">
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
      <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
        <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name="${applicationId}" />
      </intent-filter>
    </receiver>
  </application>
</manifest>
```

### <a name="override-no-locfirebasemessagingservice-to-handle-messages"></a>重写 `FirebaseMessagingService` 以处理消息

要向 Firebase 注册并处理消息，请将 `FirebaseMessagingService` 类声明为子类。 示例应用程序定义了一个 `FirebaseService` 类，其子类为 `FirebaseMessagingService`。 此类用 `IntentFilter` 属性标记，其中包括 `com.google.firebase.MESSAGING_EVENT` 筛选器。 此筛选器允许 Android 将传入消息传递给此类进行处理：

```csharp
[Service]
[IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
public class FirebaseService : FirebaseMessagingService
{
    // ...
}

```

启动应用程序后，Firebase SDK 将自动从 Firebase 服务器请求唯一的令牌标识符。 成功请求后，会对 `FirebaseService` 类调用 `OnNewToken` 方法。 示例项目重写了此方法，并向 Azure 通知中心注册令牌：

```csharp
public override void OnNewToken(string token)
{
    // NOTE: save token instance locally, or log if desired

    SendRegistrationToServer(token);
}

void SendRegistrationToServer(string token)
{
    try
    {
        NotificationHub hub = new NotificationHub(AppConstants.NotificationHubName, AppConstants.ListenConnectionString, this);

        // register device with Azure Notification Hub using the token from FCM
        Registration registration = hub.Register(token, AppConstants.SubscriptionTags);

        // subscribe to the SubscriptionTags list with a simple template.
        string pnsHandle = registration.PNSHandle;
        TemplateRegistration templateReg = hub.RegisterTemplate(pnsHandle, "defaultTemplate", AppConstants.FCMTemplateBody, AppConstants.SubscriptionTags);
    }
    catch (Exception e)
    {
        Log.Error(AppConstants.DebugTag, $"Error registering device: {e.Message}");
    }
}
```

`SendRegistrationToServer` 方法会向 Azure 通知中心注册设备，并使用模板订阅标记。 示例应用程序在 `AppConstants.cs` 文件中定义了一个名为 `default` 的标记和一个具有 `messageParam` 参数的模板。 有关注册、标记和模板的详细信息，请参阅[向 Azure 通知中心注册模板和标记](#register-templates-and-tags-with-the-azure-notification-hub)。

收到消息时，将对 `FirebaseService` 类调用 `OnMessageReceived` 方法：

```csharp
public override void OnMessageReceived(RemoteMessage message)
{
    base.OnMessageReceived(message);
    string messageBody = string.Empty;

    if (message.GetNotification() != null)
    {
        messageBody = message.GetNotification().Body;
    }

    // NOTE: test messages sent via the Azure portal will be received here
    else
    {
        messageBody = message.Data.Values.First();
    }

    // convert the incoming message to a local notification
    SendLocalNotification(messageBody);

    // send the incoming message directly to the MainPage
    SendMessageToMainPage(messageBody);
}

void SendLocalNotification(string body)
{
    var intent = new Intent(this, typeof(MainActivity));
    intent.AddFlags(ActivityFlags.ClearTop);
    intent.PutExtra("message", body);

    //Unique request code to avoid PendingIntent collision.
    var requestCode = new Random().Next();
    var pendingIntent = PendingIntent.GetActivity(this, requestCode, intent, PendingIntentFlags.OneShot);

    var notificationBuilder = new NotificationCompat.Builder(this, AppConstants.NotificationChannelName)
        .SetContentTitle("XamarinNotify Message")
        .SetSmallIcon(Resource.Drawable.ic_launcher)
        .SetContentText(body)
        .SetAutoCancel(true)
        .SetShowWhen(false)
        .SetContentIntent(pendingIntent);

    if (Build.VERSION.SdkInt >= BuildVersionCodes.O)
    {
        notificationBuilder.SetChannelId(AppConstants.NotificationChannelName);
    }

    var notificationManager = NotificationManager.FromContext(this);
    notificationManager.Notify(0, notificationBuilder.Build());
}

void SendMessageToMainPage(string body)
{
    (App.Current.MainPage as MainPage)?.AddMessage(body);
}
```

使用 `SendLocalNotification` 方法将传入消息转换为本地通知。 此方法将创建一个新 `Intent`，并将消息内容以 `string` `Extra` 形式放置到 `Intent` 中。 当用户点击本地通知时，无论应用在前台还是后台运行，都将启动 `MainActivity`，并通过 `Intent` 对象访问消息内容。

本地通知和 `Intent` 示例要求用户执行点击通知的操作。 用户最好在应用程序状态更改之前执行此操作。 但是，在某些情况下，你可能需要直接访问消息数据而不要求用户操作。 前面的示例还使用 `SendMessageToMainPage` 方法将消息直接发送到当前 `MainPage` 实例。 在生产环境中，如果对一种消息类型同时实现这两种方法，则当用户点击通知时，`MainPage` 对象会收到重复的消息。

> [!NOTE]
> Android 应用程序只有在后台或前台运行时才会收到推送通知。 要在主 `Activity` 未运行时收到推送通知，必须实现服务，这不在本示例的范围之内。 有关详细信息，请参阅[创建 Android 服务](../../../android/app-fundamentals/services/index.md)

### <a name="add-incoming-notifications-to-the-no-locxamarinforms-ui"></a>将传入通知添加到 Xamarin.Forms UI

`MainActivity` 类需要获取处理通知和管理传入消息数据的权限。 以下代码演示了完整的 `MainActivity` 实现：

```csharp
[Activity(Label = "NotificationHubSample", Icon = "@mipmap/icon", Theme = "@style/MainTheme", MainLauncher = true, ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation, LaunchMode = LaunchMode.SingleTop)]
public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        TabLayoutResource = Resource.Layout.Tabbar;
        ToolbarResource = Resource.Layout.Toolbar;

        base.OnCreate(savedInstanceState);

        global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
        LoadApplication(new App());

        if (!IsPlayServiceAvailable())
        {
            throw new Exception("This device does not have Google Play Services and cannot receive push notifications.");
        }

        CreateNotificationChannel();
    }

    protected override void OnNewIntent(Intent intent)
    {
        if (intent.Extras != null)
        {
            var message = intent.GetStringExtra("message");
            (App.Current.MainPage as MainPage)?.AddMessage(message);
        }

        base.OnNewIntent(intent);
    }

    bool IsPlayServiceAvailable()
    {
        int resultCode = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable(this);
        if (resultCode != ConnectionResult.Success)
        {
            if (GoogleApiAvailability.Instance.IsUserResolvableError(resultCode))
                Log.Debug(AppConstants.DebugTag, GoogleApiAvailability.Instance.GetErrorString(resultCode));
            else
            {
                Log.Debug(AppConstants.DebugTag, "This device is not supported");
            }
            return false;
        }
        return true;
    }

    void CreateNotificationChannel()
    {
        // Notification channels are new as of "Oreo".
        // There is no need to create a notification channel on older versions of Android.
        if (Build.VERSION.SdkInt >= BuildVersionCodes.O)
        {
            var channelName = AppConstants.NotificationChannelName;
            var channelDescription = String.Empty;
            var channel = new NotificationChannel(channelName, channelName, NotificationImportance.Default)
            {
                Description = channelDescription
            };

            var notificationManager = (NotificationManager)GetSystemService(NotificationService);
            notificationManager.CreateNotificationChannel(channel);
        }
    }
}
```

`Activity` 属性将应用程序 `LaunchMode` 设置为 `SingleTop`。 此启动模式告知 Android OS 仅允许此活动的单个实例。 使用此启动模式，传入的 `Intent` 数据将路由到 `OnNewIntent` 方法，此方法提取消息数据并通过 `AddMessage` 方法将这些数据发送到 `MainPage` 实例。 如果你的应用程序使用其他启动模式，则必须以不同的方式处理 `Intent` 数据。

`OnCreate` 方法使用名为 `IsPlayServiceAvailable` 的帮助程序方法来确保设备支持 Google Play 服务。 不支持 Google Play 服务的仿真器或设备无法从 Firebase 收到推送通知。

## <a name="configure-ios-for-notifications"></a>为 iOS 配置通知

配置 iOS 应用程序以收到通知的过程如下：

1. 将 `Info.plist` 文件中的“捆绑标识符”配置为与预配配置文件中使用的值一致****。
1. 将“启用推送通知”选项添加到 `Entitlements.plist` 文件****。
1. 将 `Xamarin.Azure.NotificationHubs.iOS` NuGet 包添加到项目。
1. 向 APNS [注册](#register-for-notifications-with-apns)通知。
1. 向 Azure 通知中心[注册](#register-with-azure-notification-hub-and-subscribe-to-tags)应用程序并订阅标记。
1. 将 APNS 通知[添加](#add-apns-notifications-to-xamarinforms-ui)到 Xamarin.Forms UI。

以下屏幕截图显示了在 Visual Studio 的 `Entitlements.plist` 文件中选择了“启用推送通知”选项****：

![推送通知权限的屏幕截图](azure-notification-hub-images/push-notification-entitlement.png "推送通知权限")

### <a name="register-for-notifications-with-apns"></a>向 APNS 注册通知

必须重写 `AppDelegate.cs` 文件中的 `FinishedLaunching` 方法，以注册远程通知。 注册因设备上使用的 iOS 版本而异。 示例应用程序中的 iOS 项目将重写 `FinishedLaunching` 方法以调用 `RegisterForRemoteNotifications`，如下面的示例中所示：

```csharp
public override bool FinishedLaunching(UIApplication app, NSDictionary options)
{
    global::Xamarin.Forms.Forms.Init();
    LoadApplication(new App());

    base.FinishedLaunching(app, options);

    RegisterForRemoteNotifications();

    return true;
}

void RegisterForRemoteNotifications()
{
    // register for remote notifications based on system version
    if (UIDevice.CurrentDevice.CheckSystemVersion(10, 0))
    {
        UNUserNotificationCenter.Current.RequestAuthorization(UNAuthorizationOptions.Alert |
            UNAuthorizationOptions.Sound |
            UNAuthorizationOptions.Sound,
            (granted, error) =>
            {
                if (granted)
                    InvokeOnMainThread(UIApplication.SharedApplication.RegisterForRemoteNotifications);
            });
    }
    else if (UIDevice.CurrentDevice.CheckSystemVersion(8, 0))
    {
        var pushSettings = UIUserNotificationSettings.GetSettingsForTypes(
        UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
        new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(pushSettings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();
    }
    else
    {
        UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
        UIApplication.SharedApplication.RegisterForRemoteNotificationTypes(notificationTypes);
    }
}
```

### <a name="register-with-azure-notification-hub-and-subscribe-to-tags"></a>向 Azure 通知中心注册应用程序并订阅标记

如果设备在运行 `FinishedLaunching` 方法的过程中成功注册远程通知，iOS 将调用 `RegisteredForRemoteNotifications` 方法。 应重写此方法，以执行以下操作：

1. 实例化 `SBNotificationHub`。
1. 取消注册任何现有注册。
1. 向通知中心注册设备。
1. 使用模板订阅特定标记。

有关设备、模板和标记注册的详细信息，请参阅[向 Azure 通知中心注册模板和标记](#register-templates-and-tags-with-the-azure-notification-hub)。 以下代码演示了设备和模板的注册：

```csharp
public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
{
    Hub = new SBNotificationHub(AppConstants.ListenConnectionString, AppConstants.NotificationHubName);

    // update registration with Azure Notification Hub
    Hub.UnregisterAll(deviceToken, (error) =>
    {
        if (error != null)
        {
            Debug.WriteLine($"Unable to call unregister {error}");
            return;
        }

        var tags = new NSSet(AppConstants.SubscriptionTags.ToArray());
        Hub.RegisterNative(deviceToken, tags, (errorCallback) =>
        {
            if (errorCallback != null)
            {
                Debug.WriteLine($"RegisterNativeAsync error: {errorCallback}");
            }
        });

        var templateExpiration = DateTime.Now.AddDays(120).ToString(System.Globalization.CultureInfo.CreateSpecificCulture("en-US"));
        Hub.RegisterTemplate(deviceToken, "defaultTemplate", AppConstants.APNTemplateBody, templateExpiration, tags, (errorCallback) =>
        {
            if (errorCallback != null)
            {
                if (errorCallback != null)
                {
                    Debug.WriteLine($"RegisterTemplateAsync error: {errorCallback}");
                }
            }
        });
    });
}
```

> [!NOTE]
> 在某些情况下（例如没有网络连接），注册远程通知可能会失败。 你可以选择重写 `FailedToRegisterForRemoteNotifications` 方法来处理注册失败。

### <a name="add-apns-notifications-to-no-locxamarinforms-ui"></a>将 APNS 通知添加到 Xamarin.Forms UI

当设备收到远程通知时，iOS 会调用 `ReceivedRemoteNotification` 方法。 传入消息 JSON 会转换为 `NSDictionary` 对象，而 `ProcessNotification` 方法将从字典中提取值并将这些值发送到 Xamarin.Forms `MainPage` 实例。 重写 `ReceivedRemoteNotifications` 方法以调用 `ProcessNotification`，如以下代码所示：

```csharp
public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
{
    ProcessNotification(userInfo, false);
}

void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
{
    // make sure we have a payload
    if (options != null && options.ContainsKey(new NSString("aps")))
    {
        // get the APS dictionary and extract message payload. Message JSON will be converted
        // into a NSDictionary so more complex payloads may require more processing
        NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;
        string payload = string.Empty;
        NSString payloadKey = new NSString("alert");
        if (aps.ContainsKey(payloadKey))
        {
            payload = aps[payloadKey].ToString();
        }

        if (!string.IsNullOrWhiteSpace(payload))
        {
            (App.Current.MainPage as MainPage)?.AddMessage(payload);
        }

    }
    else
    {
        Debug.WriteLine($"Received request to process notification but there was no payload.");
    }
}
```

## <a name="test-notifications-in-the-azure-portal"></a>在 Azure 门户中测试通知

利用 Azure 通知中心，你可以检查应用程序能否收到测试消息。 你可以在通知中心内的“发送测试”部分选择目标平台并发送消息****。 将“发送到标记表达式”设置为 `default` 会将消息发送到已为 `default` 标记注册了模板的应用程序****。 单击“发送”按钮将生成一个报表，其中包含该消息可以到达的设备数****。 以下屏幕截图显示了 Azure 门户中的 Android 配置测试：

![Azure 通知中心测试消息的屏幕截图](azure-notification-hub-images/azure-notification-hub-test-send.png "Azure 通知中心测试消息")

### <a name="testing-tips"></a>测试技巧

1. 在测试应用程序能否收到推送通知时，必须使用物理设备。 Android 和 iOS 虚拟设备可能未正确配置为接收推送通知。
1. 发布 Firebase 令牌后，示例 Android 应用程序便会注册其令牌和模板。 在测试过程中，你可能需要请求一个新令牌并向 Azure 通知中心重新注册。 强制执行此操作的最佳方法是清理项目，删除 `bin` 和 `obj` 文件夹，并从设备上卸载应用程序，然后重新生成和部署。
1. 推送通知流程的许多部分都是异步执行的。 这可能会导致断点未命中或命中顺序不正确。 可使用设备或调试日志记录来跟踪执行，确保不中断应用程序流。 可使用 `Constants` 中指定的 `DebugTag` 来筛选 Android 设备日志。
1. 当在 Visual Studio 中停止调试时，应用将强制关闭。 在调试过程中启动的任何消息接收器或其他服务都将关闭，并且不会响应消息事件。

## <a name="create-a-notification-dispatcher"></a>创建通知调度程序

使用 Azure 通知中心，后端应用程序可以跨平台将通知发送到设备。 示例演示了如何使用控制台应用程序发送通知。 该应用程序包含 `DispatcherConstants.cs` 文件，用于定义以下属性：

```csharp
public static class DispatcherConstants
{
    public static string[] SubscriptionTags { get; set; } = { "default" };
    public static string NotificationHubName { get; set; } = "< Insert your Azure Notification Hub name >";
    public static string FullAccessConnectionString { get; set; } = "< Insert your DefaultFullSharedAccessSignature >";
}
```

必须将 `DispatcherConstants.cs` 配置为与 Azure 通知中心配置一致。 `SubscriptionTags` 属性的值应与客户端应用中使用的值一致。 `NotificationHubName` 属性是 Azure 通知中心实例的名称。 `FullAccessConnectionString` 属性是在通知中心“访问策略”下找到的访问密钥****。 以下屏幕截图显示了 `NotificationHubName` 和 `FullAccessConnectionString` 属性在 Azure 门户中的位置：

![Azure 通知中心名称和 FullAccessConnectionString 的屏幕截图](azure-notification-hub-images/notification-hub-full-access-policy.png "Azure 通知中心名称和 FullAccessConnectionString")

控制台应用程序循环遍历每个 `SubscriptionTags` 值，并使用 `NotificationHubClient` 类的实例将通知发送到订阅者。 以下代码显示了控制台应用程序 `Program` 类：

``` csharp
class Program
{
    static int messageCount = 0;

    static void Main(string[] args)
    {
        Console.WriteLine($"Press the spacebar to send a message to each tag in {string.Join(", ", DispatcherConstants.SubscriptionTags)}");
        WriteSeparator();
        while (Console.ReadKey().Key == ConsoleKey.Spacebar)
        {
            SendTemplateNotificationsAsync().GetAwaiter().GetResult();
        }
    }

    private static async Task SendTemplateNotificationsAsync()
    {
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(DispatcherConstants.FullAccessConnectionString, DispatcherConstants.NotificationHubName);
        Dictionary<string, string> templateParameters = new Dictionary<string, string>();

        messageCount++;

        // Send a template notification to each tag. This will go to any devices that
        // have subscribed to this tag with a template that includes "messageParam"
        // as a parameter
        foreach (var tag in DispatcherConstants.SubscriptionTags)
        {
            templateParameters["messageParam"] = $"Notification #{messageCount} to {tag} category subscribers!";
            try
            {
                await hub.SendTemplateNotificationAsync(templateParameters, tag);
                Console.WriteLine($"Sent message to {tag} subscribers.");
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Failed to send template notification: {ex.Message}");
            }
        }

        Console.WriteLine($"Sent messages to {DispatcherConstants.SubscriptionTags.Length} tags.");
        WriteSeparator();
    }

    private static void WriteSeparator()
    {
        Console.WriteLine("==========================================================================");
    }
}
```

运行示例控制台应用程序时，可以按空格键来发送消息。 只要配置正确，运行客户端应用程序的设备都会收到编号的通知。

## <a name="related-links"></a>相关链接

* [推送通知模板](/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages)。
* [设备注册管理](/azure/notification-hubs/notification-hubs-push-notification-registration-management)。
* [路由和标记表达式](/azure/notification-hubs/notification-hubs-tags-segment-push-message)。
* [Xamarin.Android Azure 通知中心教程](/azure/notification-hubs/xamarin-notification-hubs-push-notifications-android-gcm)。
* [Xamarin.iOS Azure 通知中心教程](/azure/notification-hubs/xamarin-notification-hubs-ios-push-notification-apns-get-started)。