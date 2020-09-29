---
title: 使用 Firebase Cloud Messaging 发送远程通知
description: 本演练逐步说明如何使用 Firebase Cloud 消息传递在 Xamarin Android 应用程序中 (也称为推送通知) 的远程通知。 它说明了如何实现与 Firebase Cloud 消息传送 (FCM) 所需的各种类，提供了有关如何配置 Android 清单以访问 FCM 的示例，并演示了使用 Firebase 控制台的下游消息。
ms.prod: xamarin
ms.assetid: 4D7C5F46-C997-49F6-AFDA-6763E68CDC90
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/31/2018
ms.openlocfilehash: 702ca70e220d8e4d28a1a2ddc6be40daae052d58
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91455817"
---
# <a name="remote-notifications-with-firebase-cloud-messaging"></a>使用 Firebase Cloud Messaging 发送远程通知

_本演练逐步说明如何使用 Firebase Cloud 消息传递在 Xamarin Android 应用程序中 (也称为推送通知) 的远程通知。它说明了如何实现与 Firebase Cloud 消息传送 (FCM) 所需的各种类，提供了有关如何配置 Android 清单以访问 FCM 的示例，并演示了使用 Firebase 控制台的下游消息。_

## <a name="fcm-notifications-overview"></a>FCM 通知概述

在本演练中，将创建一个名为 **FCMClient** 的基本应用，以说明 FCM 消息传递的基本信息。 **FCMClient** 检查是否存在 Google Play Services、从 FCM 接收注册令牌、显示从 Firebase 控制台发送的远程通知，并订阅主题消息：

[![应用的示例屏幕截图](remote-notifications-with-fcm-images/00-app-example-sml.png)](remote-notifications-with-fcm-images/00-app-example.png#lightbox)

将探讨以下主题领域：

1. 背景通知

2. 主题消息

3. 前台通知

在本演练中，你将以增量方式向 **FCMClient** 添加功能并在设备或仿真器上运行它，以了解它如何与 FCM 交互。 你将使用日志记录来通过 FCM 服务器对实时应用事务进行见证，你将看到如何从你输入到 Firebase 控制台通知 GUI 的 FCM 消息生成通知。

## <a name="requirements"></a>要求

它有助于熟悉可通过 Firebase 云消息传送发送的 [不同类型的消息](https://firebase.google.com/docs/cloud-messaging/concept-options#notifications_and_data_messages) 。 消息的负载将确定客户端应用程序将如何接收和处理消息。

在继续本演练之前，必须获取所需的凭据才能使用 Google 的 FCM 服务器。 [Firebase Cloud 消息传递](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#setup_fcm)中介绍了此过程。
特别是，您必须下载 **google-services.js** 文件以与本演练中提供的示例代码一起使用。 如果你尚未在 Firebase 控制台中创建项目 (或者尚未将google-services.js下载到文件) ** 上** ，请参阅 [Firebase Cloud 消息传送](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)。

若要运行示例应用，需要一个与 Firebase 兼容的 Android 测试设备或模拟器。 Firebase 云消息传送支持在 Android 4.0 或更高版本上运行的客户端，并且这些设备还必须安装 Google Play 商店应用 (Google Play Services 9.2.1 或更高版本) 。 如果你尚未在设备上安装 Google Play 商店应用，请访问 [Google Play](https://support.google.com/googleplay) 网站下载并安装该应用。 或者，您也可以将 Android SDK 模拟器与 Google Play Services 安装而不是测试设备一起使用 (如果使用 Android SDK 仿真器) ，则无需安装该 Google Play 商店。

## <a name="start-an-app-project"></a>启动应用项目

若要开始，请创建名为 **FCMClient**的新空 Xamarin。 如果你不熟悉如何创建 Xamarin Android 项目，请参阅 [Hello，android](~/android/get-started/hello-android/hello-android-quickstart.md)。
创建新的应用后，下一步是设置包名称并安装将用于与 FCM 通信的多个 NuGet 包。

### <a name="set-the-package-name"></a>设置包名称

在 [Firebase Cloud 消息传递](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)中，你为启用了 FCM 的应用指定了包名称。 此包名称还用作与[API 密钥](firebase-cloud-messaging.md#fcm-in-action-api-key)关联的[*应用程序 ID*](./firebase-cloud-messaging.md#fcm-in-action-app-id) 。 将应用配置为使用此包名称：

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 打开 **FCMClient** 项目的 "属性"。

2. 在 " **Android 清单** " 页上，设置包名称。

在下面的示例中，包名称设置为 `com.xamarin.fcmexample` ：

[![设置包名称](remote-notifications-with-fcm-images/01-package-name-vs-sml.png)](remote-notifications-with-fcm-images/01-package-name-vs.png#lightbox)

更新 **Android 清单**时，还要检查以确保 `Internet` 已启用此权限。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 打开 **FCMClient** 项目的 "属性"。

2. 在 " **Android 应用程序** " 页中，设置包名称。

在下面的示例中，包名称设置为 `com.xamarin.fcmexample` ：

[![设置包名称](remote-notifications-with-fcm-images/01-package-name-xs-sml.png)](remote-notifications-with-fcm-images/01-package-name-xs.png#lightbox)

更新 **Android 清单**时，还要检查以确保 `INTERNET` 已启用权限， (在 " **必需的权限** ") 下。

-----

> [!IMPORTANT]
> 如果此包名称与在 Firebase 控制台中输入的包名称不 *完全* 匹配，则客户端应用将无法接收来自 FCM 的注册令牌。

### <a name="add-the-xamarin-google-play-services-base-package"></a>添加 Xamarin Google Play Services 基包

由于 Firebase 云消息传送依赖于 Google Play Services，因此必须将 [xamarin Google Play Services 基本](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Base/) NuGet 包添加到 xamarin 项目。 需要29.0.0.2 或更高版本。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 在 Visual Studio 中，右键单击 " **引用" > "管理 NuGet 包 ...**"。

2. 单击 " **浏览** " 选项卡，然后搜索 " **GooglePlayServices**"。

3. 将此包安装到 **FCMClient** 项目中：

    [![安装 Google Play Services 基](remote-notifications-with-fcm-images/02-google-play-services-vs-sml.png)](remote-notifications-with-fcm-images/02-google-play-services-vs.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 Visual Studio for Mac 中，右键单击 " **包" > "添加包 ...**"。

2. 搜索 **Xamarin.GooglePlayServices.Base**。

3. 将此包安装到 **FCMClient** 项目中：

    [![安装 Google Play Services 基](remote-notifications-with-fcm-images/02-google-play-services-xs-sml.png)](remote-notifications-with-fcm-images/02-google-play-services-xs.png#lightbox)

-----

如果在安装 NuGet 时出现错误，请关闭 " **FCMClient** " 项目，再次将其打开，然后重试 nuget 安装。

安装 **GooglePlayServices**时，还会安装所有必需的依赖项。 编辑 **MainActivity.cs** 并添加以下 `using` 语句：

```csharp
using Android.Gms.Common;
```

此语句使 `GoogleApiAvailability` **GooglePlayServices** 中的类可用于 **FCMClient** 代码。
`GoogleApiAvailability` 用于检查是否存在 Google Play Services。

### <a name="add-the-xamarin-firebase-messaging-package"></a>添加 Xamarin Firebase 消息包

若要从 FCM 接收消息，必须将 [Xamarin Firebase 消息](https://www.nuget.org/packages/Xamarin.Firebase.Messaging/) NuGet 包添加到应用项目中。 如果不使用此包，Android 应用程序将无法从 FCM 服务器接收消息。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 在 Visual Studio 中，右键单击 " **引用" > "管理 NuGet 包 ...**"。

2. 搜索 " **Firebase**"。

3. 将此包安装到 **FCMClient** 项目中：

    [![安装 Xamarin Firebase 消息传送](remote-notifications-with-fcm-images/03-firebase-messaging-vs-sml.png)](remote-notifications-with-fcm-images/03-firebase-messaging-vs.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 Visual Studio for Mac 中，右键单击 " **包" > "添加包 ...**"。

2. 搜索 " **Firebase**"。

3. 将此包安装到 **FCMClient** 项目中：

    [![安装 Xamarin Firebase 消息传送](remote-notifications-with-fcm-images/03-firebase-messaging-xs-sml.png)](remote-notifications-with-fcm-images/03-firebase-messaging-xs.png#lightbox)

-----

当你安装 **Firebase**时，还会安装所有必需的依赖项。

接下来，编辑 **MainActivity.cs** 并添加以下 `using` 语句：

```csharp
using Firebase.Messaging;
using Firebase.Iid;
using Android.Util;
```

前两个语句使 **Firebase** NuGet 包中的类型可用于 **FCMClient** 代码。 **Util** 添加了日志记录功能，该功能将用于观察带有 FMS 的事务。

### <a name="add-the-google-services-json-file"></a><a name="add-googleplayservices-json"></a>添加 Google Services JSON 文件

下一步是将文件 ** 中的google-services.js** 添加到项目的根目录中：

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 将 **google-services.js** 复制到项目文件夹。

2. 将**google-services.js**添加到应用项目 (单击**解决方案资源管理器**中的 "**显示所有文件**"，右键单击 " **google-services.js"**，然后选择 "**包括在项目中**) "。

3. 在“解决方案资源管理器”窗口中选择“google-services.json”。********

4. 在 " **属性** " 窗格中，将 " **生成操作** " 设置为  **GoogleServicesJson**：

    [![将生成操作设置为 GoogleServicesJson](remote-notifications-with-fcm-images/04-google-services-json-vs-sml.png)](remote-notifications-with-fcm-images/04-google-services-json-vs.png#lightbox)

    > [!NOTE] 
    > 如果未显示 **GoogleServicesJson** 生成操作，请保存并关闭解决方案，然后重新打开它。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 将 **google-services.js** 复制到项目文件夹。

2. 将 **google-services.js** 添加到应用项目。

3. 右键单击 **google-services.js"**。

4. 将 **生成操作** 设置为 **GoogleServicesJson**：

    [![将生成操作设置为 GoogleServicesJson](remote-notifications-with-fcm-images/04-google-services-json-xs-sml.png)](remote-notifications-with-fcm-images/04-google-services-json-xs.png#lightbox)

-----

当**google-services.js**添加到项目 (并且**GoogleServicesJson**生成操作设置) 时，生成过程将提取客户端 ID 和[API 密钥](./firebase-cloud-messaging.md#fcm-in-action-api-key)，然后将这些凭据添加到驻留在**obj/Debug/android/AndroidManifest.xml**的合并/生成的**AndroidManifest.xml**中。 此合并过程自动添加连接到 FCM 服务器所需的任何权限和其他 FCM 元素。

## <a name="check-for-google-play-services-and-create-a-notification-channel"></a>检查 Google Play Services 并创建通知通道

Google 建议在访问 Google Play Services 功能之前，Android 应用检查是否存在 Google Play Services APK (有关详细信息，请参阅 [检查 Google Play Services](https://firebase.google.com/docs/cloud-messaging/android/client#sample-play)) 。

将首先创建应用 UI 的初始布局。 编辑 **Resources/layout/main.axml** ，并将其内容替换为以下 XML：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="10dp">
    <TextView
        android:text=" "
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:id="@+id/msgText"
        android:textAppearance="?android:attr/textAppearanceMedium"
        android:padding="10dp" />
</LinearLayout>
```

这 `TextView` 将用于显示消息，指示是否已安装 Google Play Services。 保存对 **main.axml**所做的更改。

编辑 **MainActivity.cs** 并将以下实例变量添加到 `MainActivity` 类：

```csharp
public class MainActivity : AppCompatActivity
{
    static readonly string TAG = "MainActivity";

    internal static readonly string CHANNEL_ID = "my_notification_channel";
    internal static readonly int NOTIFICATION_ID = 100;

    TextView msgText;
```

将 `CHANNEL_ID` `NOTIFICATION_ID` 在 [`CreateNotificationChannel`](#create-notification-channel-code) `MainActivity` 本演练的稍后部分将添加到的方法中使用变量和。

在下面的示例中， `OnCreate` 方法将验证 Google Play Services 在应用尝试使用 FCM Services 之前是否可用。
将以下方法添加到 `MainActivity` 类：

```csharp
public bool IsPlayServicesAvailable ()
{
    int resultCode = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable (this);
    if (resultCode != ConnectionResult.Success)
    {
        if (GoogleApiAvailability.Instance.IsUserResolvableError (resultCode))
            msgText.Text = GoogleApiAvailability.Instance.GetErrorString (resultCode);
        else
        {
            msgText.Text = "This device is not supported";
            Finish ();
        }
        return false;
    }
    else
    {
        msgText.Text = "Google Play Services is available.";
        return true;
    }
}
```

此代码检查设备以查看是否安装了 Google Play Services 的 APK。 如果未安装，则会在中显示一条消息， `TextBox` 指示用户从 Google Play 商店 (下载 APK，或在设备的 "系统设置") 中启用它。

<a name="create-notification-channel-code"></a>) 或更高版本的 Android 8.0 (上运行的应用必须创建 [_通知通道_](~/android/app-fundamentals/notifications/local-notifications.md) 以便发布其通知。  将以下方法添加到类，以便在 `MainActivity` 必要时 (创建通知通道) ：

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

    var channel = new NotificationChannel(CHANNEL_ID,
                                          "FCM Notifications",
                                          NotificationImportance.Default)
                  {

                      Description = "Firebase Cloud Messages appear in this channel"
                  };

    var notificationManager = (NotificationManager)GetSystemService(Android.Content.Context.NotificationService);
    notificationManager.CreateNotificationChannel(channel);
}
```

将 `OnCreate` 方法替换为以下代码：

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);
    SetContentView (Resource.Layout.Main);
    msgText = FindViewById<TextView> (Resource.Id.msgText);

    IsPlayServicesAvailable ();

    CreateNotificationChannel();
}
```

`IsPlayServicesAvailable` 在结束时调用， `OnCreate` 以便在每次应用程序启动时运行 Google Play Services 检查。 调用方法 `CreateNotificationChannel` 以确保在运行 Android 8 或更高版本的设备上存在通知通道。 如果你的应用程序具有 `OnResume` 方法，它 `IsPlayServicesAvailable` 也应该从调用 `OnResume` 。 完全重新生成并运行应用。 如果正确配置了所有配置，则会看到如下屏幕截图所示的屏幕：

[![应用指示 Google Play Services 可用](remote-notifications-with-fcm-images/05-gps-available-sml.png)](remote-notifications-with-fcm-images/05-gps-available.png#lightbox)

如果没有得到此结果，请验证设备上是否安装了 Google Play Services APK (有关详细信息，请参阅 [设置 Google Play Services](https://developers.google.com/android/guides/setup)) 。
此外，请验证是否已将 **Xamarin** 包添加到 **FCMClient** 项目，如前文所述。

## <a name="add-the-instance-id-receiver"></a>添加实例 ID 接收器

下一步是添加一个服务，该服务扩展 `FirebaseInstanceIdService` 以处理 [Firebase 注册令牌](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#fcm-in-action-registration-token)的创建、旋转和更新。 要使 FCM 能够将 `FirebaseInstanceIdService` 消息发送到设备，该服务是必需的。 将 `FirebaseInstanceIdService` 服务添加到客户端应用时，应用将自动接收 FCM 消息，并在 backgrounded 应用时将其显示为通知。

### <a name="declare-the-receiver-in-the-android-manifest"></a>在 Android 清单中声明接收方

编辑 **AndroidManifest.xml** ，并将以下 `<receiver>` 元素插入到 `<application>` 部分：

```xml
<receiver
    android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver"
    android:exported="false" />
<receiver
    android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver"
    android:exported="true"
    android:permission="com.google.android.c2dm.permission.SEND">
    <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
        <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name="${applicationId}" />
    </intent-filter>
</receiver>
```

此 XML 执行以下操作：

- 声明一个 `FirebaseInstanceIdReceiver` 实现，该实现为每个应用实例提供 [唯一标识符](https://developers.google.com/instance-id/) 。 此接收方还会对操作进行身份验证和授权。

- 声明 `FirebaseInstanceIdInternalReceiver` 用于安全启动服务的内部实现。

- [应用 ID](./firebase-cloud-messaging.md#fcm-in-action-app-id)存储在[添加到项目](#add-googleplayservices-json)中的**google-services.js**文件中。 Xamarin Firebase 绑定会将令牌替换为 `${applicationId}` 应用 id; 客户端应用不需要其他代码来提供应用 id。

`FirebaseInstanceIdReceiver`是 `WakefulBroadcastReceiver` 接收 `FirebaseInstanceId` 和 `FirebaseMessaging` 事件并将其传递给从派生的类的 `FirebaseInstanceIdService` 。

### <a name="implement-the-firebase-instance-id-service"></a>实现 Firebase 实例 ID 服务

向 FCM 注册应用程序的工作由您提供的自定义 `FirebaseInstanceIdService` 服务处理。
`FirebaseInstanceIdService` 执行以下步骤：

1. 使用 [实例 ID API](https://developers.google.com/android/reference/com/google/android/gms/iid/InstanceID) 生成授权客户端应用访问 FCM 和应用服务器的安全令牌。 在返回时，应用程序将从 FCM 获取 [注册令牌](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#fcm-in-action-registration-token) 。

2. 如果应用服务器需要，则将注册令牌转发到应用服务器。

添加一个名为 **MyFirebaseIIDService.cs** 的新文件，并将其模板代码替换为以下代码：

```csharp
using System;
using Android.App;
using Firebase.Iid;
using Android.Util;

namespace FCMClient
{
    [Service]
    [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
    public class MyFirebaseIIDService : FirebaseInstanceIdService
    {
        const string TAG = "MyFirebaseIIDService";
        public override void OnTokenRefresh()
        {
            var refreshedToken = FirebaseInstanceId.Instance.Token;
            Log.Debug(TAG, "Refreshed token: " + refreshedToken);
            SendRegistrationToServer(refreshedToken);
        }
        void SendRegistrationToServer(string token)
        {
            // Add custom implementation, as needed.
        }
    }
}
```

此服务实现一个 `OnTokenRefresh` 方法，该方法是在最初创建或更改注册令牌时调用的。 当 `OnTokenRefresh` 运行时，它将从属性 (检索最新的令牌， `FirebaseInstanceId.Instance.Token` 该令牌由 FCM) 异步更新。 在此示例中，将记录刷新的令牌，以便可以在 "输出" 窗口中查看该令牌：

```csharp
var refreshedToken = FirebaseInstanceId.Instance.Token;
Log.Debug(TAG, "Refreshed token: " + refreshedToken);
```

`OnTokenRefresh` 不常发生地调用：在以下情况下，它用于更新令牌：

- 安装或卸载应用程序。

- 用户删除应用数据时。

- 应用清除实例 ID。

- 当令牌的安全性受到威胁时。

根据 Google 的 [实例 id](https://developers.google.com/instance-id/guides/android-implementation) 文档，FCM 实例 id 服务将请求始终定期 (每6个月) 应用程序定期刷新其令牌。

`OnTokenRefresh``SendRegistrationToAppServer`如果应用程序维护的任何) ，还会调用将用户的注册令牌与服务器端帐户关联 (：

```csharp
void SendRegistrationToAppServer (string token)
{
    // Add custom implementation here as needed.
}
```

由于此实现依赖于应用服务器的设计，因此在此示例中提供了一个空的方法体。 如果应用服务器需要 FCM 注册信息，请修改 `SendRegistrationToAppServer` 以将用户的 FCM 实例 ID 令牌与应用维护的任何服务器端帐户关联。  (请注意，该令牌对于客户端应用而言是不透明的。 ) 

将令牌发送到应用服务器时， `SendRegistrationToAppServer` 应维护一个布尔值以指示是否已将令牌发送到服务器。 如果此布尔值为 false，则会 `SendRegistrationToAppServer` 将令牌发送到应用服务器 &ndash; ，否则会将令牌发送到以前调用中的应用服务器。 在某些情况下 (例如 `FCMClient`) 此示例时，应用服务器不需要令牌; 因此，此示例不需要此方法。

## <a name="implement-client-app-code"></a>实现客户端应用代码

现在，接收方服务已准备就绪，可以编写客户端应用程序代码来利用这些服务。 在以下部分中，将向 UI 添加一个按钮，以记录注册令牌 (也称为 *实例 ID 令牌*) ，然后将更多代码添加到， `MainActivity` 以便在 `Intent` 从通知启动应用时查看信息：

[![添加到应用屏幕的 "日志令牌" 按钮](remote-notifications-with-fcm-images/06-log-token-sml.png)](remote-notifications-with-fcm-images/06-log-token.png#lightbox)

### <a name="log-tokens"></a>日志令牌

此步骤中添加的代码仅用于演示目的， &ndash; 生产客户端应用程序不需要记录注册令牌。 编辑 **Resources/layout/main.axml** ，并在 `Button` 元素后面添加以下声明 `TextView` ：

```xml
<Button
  android:id="@+id/logTokenButton"
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  android:layout_gravity="center_horizontal"
  android:text="Log Token" />
```

在 `MainActivity.OnCreate` 方法的末尾添加以下代码：

```csharp
var logTokenButton = FindViewById<Button>(Resource.Id.logTokenButton);
logTokenButton.Click += delegate {
    Log.Debug(TAG, "InstanceID token: " + FirebaseInstanceId.Instance.Token);
};
```

点击 " **日志令牌** " 按钮时，此代码会将当前令牌记录到 "输出" 窗口。

### <a name="handle-notification-intents"></a>处理通知方法

当用户点击 **FCMClient**发出的通知时，将在其他内容中提供通知消息附带的任何数据 `Intent` 。 编辑 **MainActivity.cs** ，并将以下代码添加到在 `OnCreate` 调用) 之前 (方法的顶部 `IsPlayServicesAvailable` ：

```csharp
if (Intent.Extras != null)
{
    foreach (var key in Intent.Extras.KeySet())
    {
        var value = Intent.Extras.GetString(key);
        Log.Debug(TAG, "Key: {0} Value: {1}", key, value);
    }
}
```

`Intent`当用户点击其通知消息时，将触发应用程序的启动程序，因此，此代码会将中所有伴随的数据记录 `Intent` 到 "输出" 窗口中。 如果 `Intent` 必须激发另一个，则 `click_action` 通知消息的字段必须设置为 `Intent` (在 `Intent`) 未指定时使用启动器 `click_action` 。

## <a name="background-notifications"></a>背景通知

生成并运行 **FCMClient** 应用。 显示 " **日志令牌** " 按钮：

[![显示 "日志令牌" 按钮](remote-notifications-with-fcm-images/06-log-token-sml.png)](remote-notifications-with-fcm-images/06-log-token.png#lightbox)

点击 " **日志令牌** " 按钮。 IDE "输出" 窗口中应显示类似如下的消息：

[![在输出窗口中显示的实例 ID 令牌](remote-notifications-with-fcm-images/07-token-received-sml.png)](remote-notifications-with-fcm-images/07-token-received.png#lightbox)

标记有 **标记** 的长字符串是将粘贴到 Firebase 控制台中的实例 ID 标记， &ndash; 请选择此字符串并将其复制到剪贴板。 如果看不到实例 ID 标记，请将以下行添加到方法的顶部， `OnCreate` 以验证是否正确分析了 **google-services.js** ：

```csharp
Log.Debug(TAG, "google app id: " + GetString(Resource.String.google_app_id));
```

`google_app_id`记录到 "输出" 窗口中的值应与 `mobilesdk_app_id` **google-services.js上**的中记录的值匹配。

### <a name="send-a-message"></a>发送消息

登录到 [Firebase 控制台](https://console.firebase.google.com)，选择你的项目，单击 " **通知**"，然后单击 " **发送第一条消息**"：

[!["发送第一条消息" 按钮](remote-notifications-with-fcm-images/08-first-notification-sml.png)](remote-notifications-with-fcm-images/08-first-notification.png#lightbox)

在 " **撰写消息** " 页上，输入消息正文，然后选择 " **单一设备**"。 从 IDE "输出" 窗口中复制 "实例 ID" 令牌，并将其粘贴到 "Firebase" 控制台的 " **FCM 注册令牌** " 字段中：

[!["撰写消息" 对话框](remote-notifications-with-fcm-images/09-compose-message-sml.png)](remote-notifications-with-fcm-images/09-compose-message.png#lightbox)

在 Android 设备 (或模拟器) 上，通过点击 "Android **概述** " 按钮并触摸主屏幕来后台应用。 设备准备就绪后，在 Firebase 控制台中单击 " **发送消息** "：

[![发送消息按钮](remote-notifications-with-fcm-images/10-send-message-sml.png)](remote-notifications-with-fcm-images/10-send-message.png#lightbox)

显示 " **检查消息** " 对话框时，单击 " **发送**"。
通知图标应显示在设备 (或模拟器) 的通知区域中：

[![显示通知图标](remote-notifications-with-fcm-images/11-notification-icon-sml.png)](remote-notifications-with-fcm-images/11-notification-icon.png#lightbox)

打开通知图标以查看消息。 通知消息应与在 Firebase 控制台的 " **消息文本** " 字段中键入的内容完全一致：

[![通知消息显示在设备上](remote-notifications-with-fcm-images/12-notification-sml.png)](remote-notifications-with-fcm-images/12-notification.png#lightbox)

点击通知图标以启动 **FCMClient** 应用。 `Intent`发送到**FCMClient**的额外内容将在 "IDE 输出" 窗口中列出：

[![通过键、消息 ID 和折叠键进行的意向附加列表](remote-notifications-with-fcm-images/13-intent-extras-sml.png)](remote-notifications-with-fcm-images/13-intent-extras.png#lightbox)

在此示例中，在此示例中，" **from** " 键设置为应用 (的 Firebase 项目编号， `41590732`) ， **collapse_key** 设置为其包名称 (**fcmexample**) 。
如果未收到消息，请尝试删除设备上的 **FCMClient** 应用 (或模拟器) 并重复上述步骤。

> [!NOTE]
> 如果强制关闭应用，FCM 将停止传递通知。 Android 阻止后台服务广播意外或不必要地启动已停止应用程序的组件。  (有关此行为的详细信息，请参阅 [在已停止的应用程序上启动控件](https://developer.android.com/about/versions/android-3.1.html#launchcontrols)。 ) 出于此原因，需要在每次运行应用程序时手动卸载该应用程序并将其从调试会话中停止， &ndash; 这会强制 FCM 生成新的令牌，以便继续接收消息。

### <a name="add-a-custom-default-notification-icon"></a>添加自定义默认通知图标

在上面的示例中，通知图标设置为 "应用程序" 图标。 以下 XML 配置通知的自定义默认图标。 Android 为所有通知消息显示此自定义默认图标，其中未显式设置通知图标。

若要添加自定义默认通知图标，请将图标添加到 **资源/可绘制** 目录，编辑 **AndroidManifest.xml**，并将以下 `<meta-data>` 元素插入到 `<application>` 部分：

```xml
<meta-data
    android:name="com.google.firebase.messaging.default_notification_icon"
    android:resource="@drawable/ic_stat_ic_notification" />
```

在此示例中，位于 **资源/可绘制/ic 状态的 \_ \_ ic \_notification.png** 的通知图标将用作自定义默认通知图标。 如果未在 **AndroidManifest.xml** 中配置自定义默认图标，并且在通知负载中未设置任何图标，则 Android 使用应用程序图标作为通知图标 (如) 上面的通知图标屏幕截图中所示。

## <a name="handle-topic-messages"></a>处理主题消息

目前为止，编写的代码处理注册令牌，并向应用程序添加远程通知功能。 下一个示例将添加侦听 *主题消息* 的代码，并将这些消息作为远程通知转发给用户。 主题消息是发送到一台或多台订阅某个特定主题的设备的 FCM 消息。 有关主题消息的详细信息，请参阅 [主题消息](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)。

### <a name="subscribe-to-a-topic"></a>订阅主题

编辑 **Resources/layout/main.axml** ，并 `Button` 紧接在上一个元素后面添加以下声明 `Button` ：

```xml
<Button
  android:id="@+id/subscribeButton"
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  android:layout_gravity="center_horizontal"
  android:layout_marginTop="20dp"
  android:text="Subscribe to Notifications" />
```

此 XML 将 " **订阅" 通知** 按钮添加到布局。
编辑 **MainActivity.cs** 并将以下代码添加到方法的末尾 `OnCreate` ：

```csharp
var subscribeButton = FindViewById<Button>(Resource.Id.subscribeButton);
subscribeButton.Click += delegate {
    FirebaseMessaging.Instance.SubscribeToTopic("news");
    Log.Debug(TAG, "Subscribed to remote notifications");
};
```

此代码将查找布局中的 " **订阅通知** " 按钮，并将其 click 处理程序分配给调用 `FirebaseMessaging.Instance.SubscribeToTopic` 的代码，并传入订阅的主题 _新闻_。 当用户点击 " **订阅** " 按钮时，应用程序会订阅 _新闻_ 主题。 在下一部分中，将从 Firebase 控制台通知 GUI 发送 _新闻_ 主题消息。

### <a name="send-a-topic-message"></a>发送主题消息

卸载应用程序，重新生成它，然后再次运行它。 单击 " **订阅通知** " 按钮：

[!["订阅通知" 按钮](remote-notifications-with-fcm-images/14-subscribe-sml.png)](remote-notifications-with-fcm-images/14-subscribe.png#lightbox)

如果应用已成功订阅，你应在 IDE 的 "输出" 窗口中看到 "已 **成功同步" 主题** ：

[!["输出" 窗口显示主题同步成功消息](remote-notifications-with-fcm-images/15-topic-sync-sml.png)](remote-notifications-with-fcm-images/15-topic-sync.png#lightbox)

使用以下步骤发送主题消息：

1. 在 Firebase 控制台中，单击 " **新建消息**"。

2. 在 " **撰写消息** " 页上，输入消息文本，然后选择 "  **主题**"。

3. 在 " **主题** " 下拉菜单中，选择内置主题 "  **新闻**：

    [![选择新闻主题](remote-notifications-with-fcm-images/16-topic-message-sml.png)](remote-notifications-with-fcm-images/16-topic-message.png#lightbox)

4. 在 Android 设备 (或模拟器) 上，通过点击 "Android **概述** " 按钮并触摸主屏幕来后台应用。

5. 设备准备就绪后，在 Firebase 控制台中单击 " **发送消息** "。

6. 检查 IDE "输出" 窗口以查看日志输出中的 **/topics/news** ：

    [![显示来自/topic/news 的消息](remote-notifications-with-fcm-images/17-message-arrived-sml.png)](remote-notifications-with-fcm-images/17-message-arrived.png#lightbox)

当在 "输出" 窗口中看到此消息时，"通知" 图标还应显示在 Android 设备上的通知区域中。 打开通知图标以查看主题消息：

[![主题消息作为通知出现](remote-notifications-with-fcm-images/18-other-news-sml.png)](remote-notifications-with-fcm-images/18-other-news.png#lightbox)

如果未收到消息，请尝试删除设备上的 **FCMClient** 应用 (或模拟器) 并重复上述步骤。

## <a name="foreground-notifications"></a>前台通知

若要在前置应用中接收通知，必须实现 `FirebaseMessagingService` 。 此服务也是接收数据负载和发送上游消息所必需的。 下面的示例演示如何实现一个服务，该服务扩展 `FirebaseMessagingService` &ndash; 生成的应用程序在前台运行时将能够处理远程通知。

### <a name="implement-firebasemessagingservice"></a>实现 FirebaseMessagingService

该 `FirebaseMessagingService` 服务负责接收和处理来自 Firebase 的消息。 每个应用都必须将此类型划分为子类并重写 `OnMessageReceived` 来处理传入消息。 当应用程序位于前台时， `OnMessageReceived` 回调将始终处理该消息。

> [!NOTE]
> 应用只需10秒钟即可处理传入的 Firebase 云消息。 任何耗时超过此时间的工作都应该使用库（如 [Android 作业计划程序](~/android/platform/android-job-scheduler.md) 或 [Firebase 作业调度](~/android/platform/firebase-job-dispatcher.md)程序）计划后台执行。

添加一个名为 **MyFirebaseMessagingService.cs** 的新文件，并将其模板代码替换为以下代码：

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Media;
using Android.Util;
using Firebase.Messaging;

namespace FCMClient
{
    [Service]
    [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
    public class MyFirebaseMessagingService : FirebaseMessagingService
    {
        const string TAG = "MyFirebaseMsgService";
        public override void OnMessageReceived(RemoteMessage message)
        {
            Log.Debug(TAG, "From: " + message.From);
            Log.Debug(TAG, "Notification Message Body: " + message.GetNotification().Body);
        }
    }
}
```

请注意， `MESSAGING_EVENT` 必须声明意向筛选器，以便将新的 FCM 消息定向到 `MyFirebaseMessagingService` ：

```csharp
[IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
```

当客户端应用从 FCM 收到消息时， `OnMessageReceived` `RemoteMessage` 通过调用其方法从传入的对象中提取消息内容 `GetNotification` 。 接下来，它记录消息内容，以便可以在 IDE 的 "输出" 窗口中查看这些内容：

```csharp
var body = message.GetNotification().Body;
Log.Debug(TAG, "Notification Message Body: " + body);
```

> [!NOTE]
> 如果在中设置断点 `FirebaseMessagingService` ，则调试会话可能会或不会命中这些断点，原因是 FCM 如何传递消息。

### <a name="send-another-message"></a>发送其他消息

卸载应用程序，重新生成它，再次运行它，然后按照以下步骤发送另一条消息：

1. 在 Firebase 控制台中，单击 " **新建消息**"。

2. 在 " **撰写消息** " 页上，输入消息正文，然后选择 "  **单一设备**"。

3. 从 IDE "输出" 窗口中复制标记字符串，并将其粘贴到 "Firebase" 控制台的 " **FCM 注册令牌** " 字段中。

4. 确保应用在前台运行，然后在 Firebase 控制台中单击 " **发送消息** "：

    [![从控制台发送另一条消息](remote-notifications-with-fcm-images/19-hello-again-sml.png)](remote-notifications-with-fcm-images/19-hello-again.png#lightbox)

5. 显示 " **检查消息** " 对话框时，单击 " **发送**"。

6. 传入消息将记录到 IDE "输出" 窗口中：

    [![将消息正文打印到输出窗口](remote-notifications-with-fcm-images/20-logged-message.png)](remote-notifications-with-fcm-images/20-logged-message.png#lightbox)

### <a name="add-a-local-notification-sender"></a>添加本地通知发件人

在此剩余示例中，传入的 FCM 消息会转换为本地通知，该通知会在应用程序在前台运行的情况下启动。 编辑 **MyFirebaseMessageService.cs** 并添加以下 `using` 语句：

```csharp
using FCMClient;
using System.Collections.Generic;
```

将以下方法添加到 `MyFirebaseMessagingService`：

<a name="sendnotification-method"></a>

```csharp
void SendNotification(string messageBody, IDictionary<string, string> data)
{
    var intent = new Intent(this, typeof(MainActivity));
    intent.AddFlags(ActivityFlags.ClearTop);
    foreach (var key in data.Keys)
    {
        intent.PutExtra(key, data[key]);
    }

    var pendingIntent = PendingIntent.GetActivity(this,
                                                  MainActivity.NOTIFICATION_ID,
                                                  intent,
                                                  PendingIntentFlags.OneShot);

    var notificationBuilder = new  NotificationCompat.Builder(this, MainActivity.CHANNEL_ID)
                              .SetSmallIcon(Resource.Drawable.ic_stat_ic_notification)
                              .SetContentTitle("FCM Message")
                              .SetContentText(messageBody)
                              .SetAutoCancel(true)
                              .SetContentIntent(pendingIntent);

    var notificationManager = NotificationManagerCompat.From(this);
    notificationManager.Notify(MainActivity.NOTIFICATION_ID, notificationBuilder.Build());
}
```

若要将此通知与后台通知区分开来，此代码使用与应用程序图标不同的图标来标记通知。 将notification.png的 [ic \_ 状态 \_ Ic \_ ](remote-notifications-with-fcm-images/ic-stat-ic-notification.png) 添加到 **资源/可绘制** 的文件，并将其包含在 **FCMClient** 项目中。

`SendNotification`方法使用 `NotificationCompat.Builder` 创建通知，并 `NotificationManagerCompat` 用于启动通知。 通知 `PendingIntent` 将保留，使用户可以打开应用并查看传入的字符串的内容 `messageBody` 。 有关的详细信息 `NotificationCompat.Builder` ，请参阅 [本地通知](~/android/app-fundamentals/notifications/local-notifications.md)。

`SendNotification`在方法的末尾调用方法 `OnMessageReceived` ：

```csharp
public override void OnMessageReceived(RemoteMessage message)
{
    Log.Debug(TAG, "From: " + message.From);

    var body = message.GetNotification().Body;
    Log.Debug(TAG, "Notification Message Body: " + body);
    SendNotification(body, message.Data);
}
```

由于这些更改， `SendNotification` 将在应用程序处于前台时收到通知时运行，并且通知将显示在通知区域中。

当应用处于后台时， [消息的负载](https://firebase.google.com/docs/cloud-messaging/concept-options#notifications_and_data_messages) 将确定消息的处理方式：

- **通知** &ndash; 消息将发送到 **系统托盘**。 本地通知将显示在此处。 当用户点击通知时，应用程序将启动。
- **数据** &ndash; 消息将由处理 `OnMessageReceived` 。
- **两者** &ndash; 同时提供通知和数据负载的消息将发送到系统托盘。 当应用程序启动时，数据负载将出现在 `Extras` `Intent` 用于启动应用程序的的中。

在此示例中，如果应用程序为 backgrounded，则在 `SendNotification` 消息具有数据负载的情况下将运行。 否则，将启动本演练中前面所述的 (后台通知) 。

### <a name="send-the-last-message"></a>发送最后一条消息

卸载应用程序，重新生成它，再次运行它，然后使用以下步骤发送最后一条消息：

1. 在 Firebase 控制台中，单击 " **新建消息**"。

2. 在 " **撰写消息** " 页上，输入消息正文，然后选择 " **单一设备**"。

3. 从 IDE "输出" 窗口中复制标记字符串，并将其粘贴到 "Firebase" 控制台的 " **FCM 注册令牌** " 字段中。

4. 确保应用在前台运行，然后在 Firebase 控制台中单击 " **发送消息** "：

    [![发送前台消息](remote-notifications-with-fcm-images/21-console-fg-msg-sml.png)](remote-notifications-with-fcm-images/21-console-fg-msg.png#lightbox)

这一次，在 "输出" 窗口中记录的消息也将打包在新的通知中， &ndash; 当应用程序在前台运行时，通知图标将出现在通知栏中：

[![前景消息的通知图标](remote-notifications-with-fcm-images/22-foreground-icon-sml.png)](remote-notifications-with-fcm-images/22-foreground-icon.png#lightbox)

打开通知时，应会看到 Firebase 控制台通知 GUI 发送的最后一条消息：

[![带有前景图标的前台通知](remote-notifications-with-fcm-images/23-foreground-msg-sml.png)](remote-notifications-with-fcm-images/23-foreground-msg.png#lightbox)

## <a name="disconnecting-from-fcm"></a>从 FCM 断开连接

若要取消订阅主题，请调用[FirebaseMessaging](https://firebase.google.com/docs/reference/android/com/google/firebase/messaging/FirebaseMessaging)类的[UnsubscribeFromTopic](https://firebase.google.com/docs/reference/android/com/google/firebase/messaging/FirebaseMessaging.html#unsubscribeFromTopic%28java.lang.String%29)方法。 例如，若要取消订阅先前订阅的 _新闻_ 主题，可以使用以下处理程序代码将 " **取消订阅** " 按钮添加到布局：

```csharp
var unSubscribeButton = FindViewById<Button>(Resource.Id.unsubscribeButton);
unSubscribeButton.Click += delegate {
    FirebaseMessaging.Instance.UnsubscribeFromTopic("news");
    Log.Debug(TAG, "Unsubscribed from remote notifications");
};
```

若要完全从 FCM 注销设备，请通过对[FirebaseInstanceId](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId)类调用[DeleteInstanceId](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId.html#deleteInstanceId%28%29)方法来删除实例 ID。 例如：

```csharp
FirebaseInstanceId.Instance.DeleteInstanceId();
```

此方法调用将删除与之关联的实例 ID 和数据。 因此，将 FCM 数据定期发送到设备暂停。

## <a name="troubleshooting"></a>疑难解答

以下描述了在将 Firebase Cloud 消息传递到 Xamarin 时可能会出现的问题和解决方法。

### <a name="firebaseapp-is-not-initialized"></a>FirebaseApp 未初始化

在某些情况下，你可能会看到以下错误消息：

```shell
Java.Lang.IllegalStateException: Default FirebaseApp is not initialized in this process
Make sure to call FirebaseApp.initializeApp(Context) first.
```

这是一个已知问题，你可以通过以下方式解决此问题：清除解决方案并重新生成项目 (**生成 > 全新解决方案**，并) **构建 > 重新生成解决方案** 。

## <a name="summary"></a>总结

本演练详细介绍了在 Xamarin Android 应用程序中实现 Firebase Cloud 消息远程通知的步骤。 本文介绍了如何安装 FCM 通信所需的包，并介绍了如何配置 Android 清单以便访问 FCM 服务器。 其中提供了示例代码，演示如何检查是否存在 Google Play Services。 本指南演示了如何实现一个实例 ID 侦听器服务，该服务为注册令牌与 FCM 协商，并说明了此代码如何在应用 backgrounded 时创建背景通知。 本文介绍了如何订阅主题消息，并提供了消息侦听器服务的示例实现，该服务用于在应用程序在前台运行时接收和显示远程通知。

## <a name="related-links"></a>相关链接

- [FCMNotifications (示例) ](/samples/xamarin/monodroid-samples/firebase-fcmnotifications)
- [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)
- [关于 FCM 消息](https://firebase.google.com/docs/cloud-messaging/concept-options)