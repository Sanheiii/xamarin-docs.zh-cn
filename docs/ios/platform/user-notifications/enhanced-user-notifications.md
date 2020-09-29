---
title: Xamarin 中的增强用户通知
description: 本文介绍了 iOS 10 中引入的用户通知框架。 它讨论了本地通知、远程通知、通知管理、通知操作等。
ms.prod: xamarin
ms.assetid: 4E1FF652-28F0-4566-B383-9D12664401A4
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/02/2017
ms.openlocfilehash: 207aac33101615a0a103176cd2bf5dd061e0d264
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430416"
---
# <a name="enhanced-user-notifications-in-xamarinios"></a>Xamarin 中的增强用户通知

用户通知框架是 iOS 10 的新功能，它允许传递和处理本地和远程通知。 使用此框架，应用或应用扩展可以通过指定一组条件（如位置或一天的时间）来计划本地通知的传送。

## <a name="about-user-notifications"></a>关于用户通知

如上所述，新的用户通知框架允许传递和处理本地和远程通知。 使用此框架，应用或应用扩展可以通过指定一组条件（如位置或一天的时间）来计划本地通知的传送。

此外，在将本地和远程通知传递给用户的 iOS 设备时，应用或扩展可以接收 (并可能修改) 本地和远程通知。

新用户通知 UI 框架允许应用或应用扩展在向用户显示本地和远程通知时，对其外观进行自定义。

此框架提供了以下方法，应用程序可以向用户提供通知：

- **视觉对象警报** -通知从屏幕顶部滚动为横幅的位置。
- **Sound 和 Vibrations** -可与通知相关联。
- **应用程序图标添加徽章** -其中，应用程序的图标会显示一个标记，表明新内容可用，如未读电子邮件的数目。

此外，根据用户的当前上下文，会有不同的方法显示通知：

- 如果设备未锁定，则通知将从屏幕顶部向下滚动为横幅。
- 如果设备被锁定，通知将显示在用户的锁定屏幕上。
- 如果用户错过了通知，他们可以打开通知中心，并在其中查看任何可用的等待通知。

Xamarin iOS 应用有两种类型的用户通知可以发送：

- **本地通知** -这些应用由本地安装在用户设备上的应用发送。
- **远程通知** -从远程服务器发送，并显示给用户或触发应用内容的后台更新。

### <a name="about-local-notifications"></a>关于本地通知

IOS 应用可以发送的本地通知具有以下功能和属性：

- 它们由用户设备上的本地应用程序发送。 
- 可以将它们配置为使用时间或基于位置的触发器。 
- 应用程序会在用户的设备上计划通知，并且在满足触发器条件时显示该通知。
- 当用户与通知交互时，应用程序将收到回调。

本地通知的一些示例包括：

- 日历警报
- 提醒警报
- 识别位置触发器

有关详细信息，请参阅 Apple 的 [本地和远程通知编程指南](https://developer.apple.com/documentation/usernotifications) 文档。

### <a name="about-remote-notifications"></a>关于远程通知

IOS 应用可以发送的远程通知具有以下功能和属性：

- 应用程序具有与之通信的服务器端组件。
- Apple Push Notification 服务 (APNs) 用于从开发人员的基于云的服务器向用户的设备传送最大程度的远程通知。
- 当应用接收到远程通知时，将向用户显示该通知。
- 当用户与通知交互时，应用程序将收到回调。

远程通知的一些示例包括：

- 新闻警报
- 体育更新
- 即时消息消息

IOS 应用提供了两种类型的远程通知：

- **面向用户** -在设备上向用户显示这些用户。
- **无提示更新** -这些更新提供了一种机制，用于在后台更新 iOS 应用程序的内容。 当收到无提示更新时，应用程序可以与远程服务器联系以拉取最新的内容。

有关详细信息，请参阅 Apple 的 [本地和远程通知编程指南](https://developer.apple.com/documentation/usernotifications) 文档。

### <a name="about-the-existing-notifications-api"></a>关于现有通知 API

在 iOS 10 之前，iOS 应用将使用 `UIApplication` 向系统注册通知，并按时间或位置) 计划 (触发通知的方式。

开发人员在使用现有通知 API 时可能会遇到几个问题：

- 本地或远程通知需要不同的回调，这可能会导致代码重复。
- 应用计划给系统后，应用对通知的控制受到限制。
- 所有 Apple 现有平台都有不同的支持级别。

### <a name="about-the-new-user-notification-framework"></a>关于新用户通知框架

对于 iOS 10，Apple 引入了新的用户通知框架，这会替换 `UIApplication` 上面提到的现有方法。

用户通知框架提供以下内容：

- 一个熟悉的 API，其中包含与先前方法的功能奇偶校验，使你可以轻松地从现有框架移植代码。
- 包括一组扩展的内容选项，这些选项允许将更丰富的通知发送给用户。
- 可以通过相同的代码和回调来处理本地和远程通知。
- 简化在用户与通知进行交互时，处理发送到应用程序的回调的过程。
- 增强了挂起和传递的通知的管理功能，包括删除或更新通知的功能。
- 添加了在应用程序中演示通知的功能。
- 添加了从应用扩展中计划和处理通知的功能。
- 为通知本身添加新的扩展点。 

新的用户通知框架在 Apple 支持的多个平台中提供统一的通知 API，其中包括： 

- **iOS** -完全支持管理和计划通知。
- **tvOS** -为本地和远程通知添加标记应用图标的功能。
- **watchOS** -添加了将通知从用户的已配对 iOS 设备转发到其 Apple Watch 的功能，并向手表应用提供直接在手表上执行本地通知的功能。

有关详细信息，请参阅 Apple 的 [UserNotifications Framework 参考](https://developer.apple.com/reference/usernotifications) 和 [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui) 文档。

## <a name="preparing-for-notification-delivery"></a>准备通知送达

在 iOS 应用程序可以向用户发送通知之前，应用必须向系统注册，因为通知是用户中断，因此应用程序必须显式请求权限，然后才能发送。

用户可以为应用批准三个不同级别的通知请求：

- 显示标题。
- 声音警报。
- 添加徽章 "应用" 图标。

此外，必须为本地和远程通知请求和设置这些批准级别。

在应用启动后，应立即请求通知权限，方法是将以下代码添加到 `FinishedLaunching` 的方法 `AppDelegate` ，并将所需的通知类型设置 (`UNAuthorizationOptions`) ：

```csharp
using UserNotifications;
...

public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
    // Request notification permissions from the user
    UNUserNotificationCenter.Current.RequestAuthorization (UNAuthorizationOptions.Alert, (approved, err) => {
        // Handle approval
    });

    return true;
}
```

此外，用户随时可以使用设备上的 " **设置** " 应用随时更改应用的通知特权。 应用应先检查用户请求的通知权限，然后再使用以下代码提出通知：

```csharp
// Get current notification settings
UNUserNotificationCenter.Current.GetNotificationSettings ((settings) => {
    var alertsAllowed = (settings.AlertSetting == UNNotificationSetting.Enabled);
});    
``` 

### <a name="configuring-the-remote-notifications-environment"></a>配置远程通知环境

IOS 10 的新手，开发人员必须在开发或生产环境中通知操作系统环境推送通知的运行情况。 如果未提供此信息，则可能会导致应用在提交到 iTune App Store 时被拒绝，其中包含类似于以下内容的通知：

> 缺少推送通知权限-你的应用包括适用于 Apple 推送通知服务的 API，但该 `aps-environment` 应用的签名中缺少该权限。

若要提供所需的权限，请执行以下操作：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 双击 `Entitlements.plist` " **Solution Pad** 中的文件以将其打开以进行编辑。
2. 切换到 **源** 视图： 

    [![源视图](enhanced-user-notifications-images/setup01.png)](enhanced-user-notifications-images/setup01.png#lightbox)
3. 单击 " **+** 添加新密钥" 按钮。
4. `aps-environment`为**属性**输入，将**类型**保留为， `String` 并 `development` `production` 为**值**输入或： 

    [![Ap-环境属性](enhanced-user-notifications-images/setup02.png)](enhanced-user-notifications-images/setup02.png#lightbox)
5. 保存对文件所做的更改。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 双击 `Entitlements.plist` " **解决方案资源管理器** 中的文件以将其打开以进行编辑。
2. 单击 " **+** 添加新密钥" 按钮。
3. `aps-environment`为**属性**输入，将**类型**保留为， `String` 并 `development` `production` 为**值**输入或： 

    [![Ap-环境属性](enhanced-user-notifications-images/setup02w.png)](enhanced-user-notifications-images/setup02.png#lightbox)
4. 保存对文件所做的更改。

-----

### <a name="registering-for-remote-notifications"></a>注册远程通知

如果应用将发送和接收远程通知，则仍需使用现有 API 进行 _令牌注册_ `UIApplication` 。 此注册要求设备具有实时网络连接访问 Apn，这将生成将发送到应用的必要令牌。 应用程序需要将此令牌转发给开发人员的服务器端应用程序，以注册远程通知：

[![令牌注册概述](enhanced-user-notifications-images/token01.png)](enhanced-user-notifications-images/token01.png#lightbox)

使用以下代码初始化所需注册：

```csharp
UIApplication.SharedApplication.RegisterForRemoteNotifications ();
```

发送远程通知时，需要将发送到开发人员服务器端应用的令牌作为通知有效负载的一部分包括在从服务器发送到 APNs 的通知有效负载中：

[![作为通知负载的一部分包含的令牌](enhanced-user-notifications-images/token02.png)](enhanced-user-notifications-images/token02.png#lightbox)

令牌充当密钥，该密钥将通知和用于打开或响应通知的应用程序联系在一起。

有关详细信息，请参阅 Apple 的 [本地和远程通知编程指南](https://developer.apple.com/documentation/usernotifications) 文档。

## <a name="notification-delivery"></a>通知传递

如果应用已完全注册，并且用户请求了所需的权限，则该应用现在可以发送和接收通知。 

### <a name="providing-notification-content"></a>提供通知内容

新的 iOS 10，所有通知都同时包含 **标题** 和 **副标题** ，这将始终显示在通知内容的 **正文** 中。 另外，还可以将 **媒体附件** 添加到通知内容。

若要创建本地通知的内容，请使用以下代码：

```csharp
var content = new UNMutableNotificationContent();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;
```

对于远程通知，此过程类似：

```csharp
{
    "aps":{
        "alert":{
            "title":"Notification Title",
            "subtitle":"Notification Subtitle",
            "body":"This is the message body of the notification."
        },
        "badge":1
    }
}
```

### <a name="scheduling-when-a-notification-is-sent"></a>调度通知发送时间

如果已创建通知的内容，则应用需要通过设置 *触发器*来计划向用户显示通知的时间。 iOS 10 提供了四种不同的触发器类型：

- **推送通知** -以独占方式用于远程通知，并在 APNs 向设备上运行的应用发送通知包时触发。
- **时间间隔** -允许从时间间隔开始计划本地通知，并从现在开始，到以后结束。 例如： `var trigger =  UNTimeIntervalNotificationTrigger.CreateTrigger (5, false);`
- **日历日期** -允许将本地通知计划在特定日期和时间。
- **基于位置** -允许在 iOS 设备进入或离开特定地理位置或处于对任何蓝牙信号的邻近性时计划本地通知。

当本地通知准备就绪时，应用程序需要调用对象的 `Add` 方法以将 `UNUserNotificationCenter` 其显示计划给用户。 对于远程通知，服务器端应用程序将通知负载发送到 Apn，然后将数据包发送到用户的设备。

将所有部分组合在一起，示例本地通知可能如下所示：

```csharp
using UserNotifications;
...

var content = new UNMutableNotificationContent ();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;

var trigger =  UNTimeIntervalNotificationTrigger.CreateTrigger (5, false);

var requestID = "sampleRequest";
var request = UNNotificationRequest.FromIdentifier (requestID, content, trigger);

UNUserNotificationCenter.Current.AddNotificationRequest (request, (err) => {
    if (err != null) {
        // Do something with error...
    }
});
```

## <a name="handling-foreground-app-notifications"></a>处理前台应用通知

IOS 10 新增，应用程序可以在前台处理通知，并触发通知。 通过提供 `UNUserNotificationCenterDelegate` 并实现 `WillPresentNotification` 方法，应用程序可以接管显示通知的责任。 例如：

```csharp
using System;
using UserNotifications;

namespace MonkeyNotification
{
    public class UserNotificationCenterDelegate : UNUserNotificationCenterDelegate
    {
        #region Constructors
        public UserNotificationCenterDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void WillPresentNotification (UNUserNotificationCenter center, UNNotification notification, Action<UNNotificationPresentationOptions> completionHandler)
        {
            // Do something with the notification
            Console.WriteLine ("Active Notification: {0}", notification);

            // Tell system to display the notification anyway or use
            // `None` to say we have handled the display locally.
            completionHandler (UNNotificationPresentationOptions.Alert);
        }
        #endregion
    }
}
```

此代码只是将的内容写入 `UNNotification` 到应用程序输出，并请求系统显示通知的标准警报。 

如果应用程序希望在前台显示通知，但不使用系统默认值，请将其传递 `None` 到完成处理程序。 示例：

```csharp
completionHandler (UNNotificationPresentationOptions.None);
```

将此代码放入后，打开 `AppDelegate.cs` 文件进行编辑，并将 `FinishedLaunching` 方法更改为如下所示：

```csharp
public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
    // Request notification permissions from the user
    UNUserNotificationCenter.Current.RequestAuthorization (UNAuthorizationOptions.Alert, (approved, err) => {
        // Handle approval
    });

    // Watch for notifications while the app is active
    UNUserNotificationCenter.Current.Delegate = new UserNotificationCenterDelegate ();

    return true;
}
```

此代码会将上面的自定义附加 `UNUserNotificationCenterDelegate` 到当前的， `UNUserNotificationCenter` 以便应用程序可以在活动时和前台处理通知。

## <a name="notification-management"></a>通知管理

使用 iOS 10 的新功能，通知管理提供对挂起和传递的通知的访问，并添加删除、更新或升级这些通知的功能。

通知管理的一个重要部分是在系统创建和计划通知时分配给通知的 _请求标识符_ 。 对于远程通知，通过 `apps-collapse-id` HTTP 请求标头中的新字段分配。

请求标识符用于选择应用想要对其执行通知管理的通知。

### <a name="removing-notifications"></a>删除通知

若要从系统中删除挂起的通知，请使用以下代码：

```csharp
var requests = new string [] { "sampleRequest" };
UNUserNotificationCenter.Current.RemovePendingNotificationRequests (requests);
```

若要删除已传递的通知，请使用以下代码：

```csharp
var requests = new string [] { "sampleRequest" };
UNUserNotificationCenter.Current.RemoveDeliveredNotifications (requests);
```

### <a name="updating-an-existing-notification"></a>更新现有通知

若要更新现有通知，只需创建一个新的通知，其中包含修改后的所需参数 (如新的触发器时间) ，并将其添加到与需要修改的通知具有相同请求标识符的系统。 示例：

```csharp
using UserNotifications;
...

// Rebuild notification
var content = new UNMutableNotificationContent ();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;

// New trigger time
var trigger = UNTimeIntervalNotificationTrigger.CreateTrigger (10, false);

// ID of Notification to be updated
var requestID = "sampleRequest";
var request = UNNotificationRequest.FromIdentifier (requestID, content, trigger);

// Add to system to modify existing Notification
UNUserNotificationCenter.Current.AddNotificationRequest (request, (err) => {
    if (err != null) {
        // Do something with error...
    }
});
```

对于已经送达的通知，现有通知将更新并升级到主屏幕和锁定屏幕上列表的顶部，并在通知中心（如果已由用户读取）中升级。

## <a name="working-with-notification-actions"></a>使用通知操作

在 iOS 10 中，传递给用户的通知不是静态的，而是提供多种方式，用户可以通过这些方式与)  (从内置到自定义操作的交互。

IOS 应用可以响应三种类型的操作：

- **默认操作** -当用户点击某个通知打开应用并显示给定通知的详细信息时，则为。
- **自定义操作** -在 iOS 8 中添加了这些操作，并提供了一种快速方法，使用户可以直接从通知执行自定义任务，而无需启动应用。 它们可以显示为具有可自定义标题的按钮列表或可在后台运行的文本输入字段， (在该应用程序提供了少量时间来完成请求) 或前台 (，在前台启动应用以满足请求) 。 自定义操作适用于 iOS 和 watchOS。
- **取消操作** -当用户关闭给定通知时，此操作将发送到应用程序。

### <a name="creating-custom-actions"></a>创建自定义操作

若要创建并向系统注册自定义操作，请使用以下代码：

```csharp
// Create action
var actionID = "reply";
var title = "Reply";
var action = UNNotificationAction.FromIdentifier (actionID, title, UNNotificationActionOptions.None);

// Create category
var categoryID = "message";
var actions = new UNNotificationAction [] { action };
var intentIDs = new string [] { };
var categoryOptions = new UNNotificationCategoryOptions [] { };
var category = UNNotificationCategory.FromIdentifier (categoryID, actions, intentIDs, UNNotificationCategoryOptions.None);
    
// Register category
var categories = new UNNotificationCategory [] { category };
UNUserNotificationCenter.Current.SetNotificationCategories (new NSSet<UNNotificationCategory>(categories)); 
```

创建新的时 `UNNotificationAction` ，将为其分配唯一的 ID 和将出现在按钮上的标题。 默认情况下，该操作将创建为后台操作，但可以提供选项来调整操作的行为 (例如，将其设置为) 的前景操作。

创建的每个操作都需要与一个类别关联。 创建新的时 `UNNotificationCategory` ，将为其分配唯一 ID、它可以执行的操作的列表、意向 id 列表，以提供有关该类别中的操作意向的详细信息，以及一些用于控制类别行为的选项。

最后，使用方法向系统注册所有类别 `SetNotificationCategories` 。

### <a name="presenting-custom-actions"></a>显示自定义操作

在系统中创建并注册一组自定义操作和类别后，可以通过本地或远程通知显示它们。

对于远程通知，请 `category` 在与上面创建的类别之一匹配的远程通知负载中设置。 例如：

```csharp
{
    aps:{
        alert:"Hello world!",
        category:"message"
    }
}
```

对于 "本地通知"，请设置 `CategoryIdentifier` 对象的属性 `UNMutableNotificationContent` 。 例如：

```csharp
var content = new UNMutableNotificationContent ();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;
content.CategoryIdentifier = "message";
...
```

同样，此 ID 需要与上面创建的某个类别匹配。

### <a name="handling-dismiss-actions"></a>处理取消操作

如上所述，当用户消除通知时，可以将解除操作发送到应用。 由于这不是标准操作，因此在创建类别时需要设置一个选项。 例如：

```csharp
var categoryID = "message";
var actions = new UNNotificationAction [] { action };
var intentIDs = new string [] { };
var categoryOptions = new UNNotificationCategoryOptions [] { };
var category = UNNotificationCategory.FromIdentifier (categoryID, actions, intentIDs, UNNotificationCategoryOptions.CustomDismissAction);

```

### <a name="handling-action-responses"></a>处理操作响应

当用户与上面创建的自定义操作和类别交互时，应用需要满足所请求的任务。 这是通过提供 `UNUserNotificationCenterDelegate` 并实现方法来完成的 `UserNotificationCenter` 。 例如：

```csharp
using System;
using UserNotifications;

namespace MonkeyNotification
{
    public class UserNotificationCenterDelegate : UNUserNotificationCenterDelegate
    {
        ...

        #region Override Methods
        public override void DidReceiveNotificationResponse (UNUserNotificationCenter center, UNNotificationResponse response, Action completionHandler)
        {
            // Take action based on Action ID
            switch (response.ActionIdentifier) {
            case "reply":
                // Do something
                break;
            default:
                // Take action based on identifier
                if (response.IsDefaultAction) {
                    // Handle default action...
                } else if (response.IsDismissAction) {
                    // Handle dismiss action
                }
                break;
            }

            // Inform caller it has been handled
            completionHandler();
        }
        #endregion
    }
}
```

传入的 `UNNotificationResponse` 类具有一个 `ActionIdentifier` 属性，该属性可以是默认操作或取消操作。 使用 `response.Notification.Request.Identifier` 测试任何自定义操作。

`UserText`属性保存任何用户文本输入值。 `Notification`属性保存发起通知，其中包含包含触发器和通知内容的请求。 应用程序可以根据触发器的类型决定是本地还是远程通知。

> [!NOTE]
> iOS 12 使自定义通知 UI 可以在运行时修改其操作按钮。 有关详细信息，请参阅 [动态通知操作按钮](~/ios/platform/introduction-to-ios12/notifications/dynamic-actions.md) 文档。

## <a name="working-with-service-extensions"></a>使用服务扩展

使用远程通知时， _服务扩展_ 提供了一种在通知有效负载内启用端对端加密的方式。 服务扩展是一种非用户界面扩展 (在后台运行) 中提供，主要目的是在向用户显示通知的可见内容之前对其进行扩充或替换。 

[![服务扩展概述](enhanced-user-notifications-images/extension01.png)](enhanced-user-notifications-images/extension01.png#lightbox)

服务扩展旨在快速运行，并且仅给系统执行一小段时间。 如果服务扩展未能在分配的时间内完成其任务，则将调用回退方法。 如果回退失败，将向用户显示原始通知内容。

服务扩展的一些潜在用途包括：

- 提供远程通知内容的端对端加密。
- 将附件添加到远程通知以丰富这些附件。

### <a name="implementing-a-service-extension"></a>实现服务扩展

若要实现 Xamarin iOS 应用中的服务扩展，请执行以下操作：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 Visual Studio for Mac 中打开应用的解决方案。
2. 在**Solution Pad**中右键单击解决方案名称，然后选择 "**添加**" "  >  **添加新项目**"。
3. 选择 " **iOS**  >  **扩展**  >  **通知服务扩展**"，然后单击 "**下一步**" 按钮： 

    [![选择通知服务扩展](enhanced-user-notifications-images/extension02.png)](enhanced-user-notifications-images/extension02.png#lightbox)
4. 输入扩展的 **名称** ，然后单击 " **下一步** " 按钮： 

    [![输入扩展的名称](enhanced-user-notifications-images/extension03.png)](enhanced-user-notifications-images/extension03.png#lightbox)
5. 如果需要，请调整 **项目名称** 和/或 **解决方案名称** ，并单击 " **创建** " 按钮： 

    [![调整项目名称和/或解决方案名称](enhanced-user-notifications-images/extension04.png)](enhanced-user-notifications-images/extension04.png#lightbox) 

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 在 Visual Studio 中打开应用的解决方案。
2. 在 **解决方案资源管理器** 中右键单击解决方案名称，然后选择 " **添加 >" 新建项目 ...**"。
3. 选择 " **Visual c #" > IOS 扩展 > 通知服务扩展**：

    [![选择通知服务扩展](enhanced-user-notifications-images/extension01.w157-sml.png)](enhanced-user-notifications-images/extension01.w157.png#lightbox)
4. 输入扩展的 **名称** ，然后单击 **"确定"** 按钮。

-----

> [!IMPORTANT]
> 服务扩展的捆绑标识符应与主应用的捆绑标识符匹配，并将其 `.appnameserviceextension` 附加到末尾。 例如，如果主应用有捆绑标识符  `com.xamarin.monkeynotify` ，则服务扩展应具有的捆绑标识符 `com.xamarin.monkeynotify.monkeynotifyserviceextension` 。 向解决方案添加扩展时，应自动设置此设置。 

通知服务扩展中有一个主类，需要对其进行修改以提供所需的功能。 例如：

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;

namespace MonkeyChatServiceExtension
{
    [Register ("NotificationService")]
    public class NotificationService : UNNotificationServiceExtension
    {
        #region Computed Properties
        public Action<UNNotificationContent> ContentHandler { get; set; }
        public UNMutableNotificationContent BestAttemptContent { get; set; }
        #endregion

        #region Constructors
        protected NotificationService (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void DidReceiveNotificationRequest (UNNotificationRequest request, Action<UNNotificationContent> contentHandler)
        {
            ContentHandler = contentHandler;
            BestAttemptContent = (UNMutableNotificationContent)request.Content.MutableCopy ();

            // Modify the notification content here...
            BestAttemptContent.Title = $"{BestAttemptContent.Title}[modified]";

            ContentHandler (BestAttemptContent);
        }

        public override void TimeWillExpire ()
        {
            // Called just before the extension will be terminated by the system.
            // Use this as an opportunity to deliver your "best attempt" at modified content, otherwise the original push payload will be used.

            ContentHandler (BestAttemptContent);
        }
        #endregion
    }
}
```

第一种方法 `DidReceiveNotificationRequest` 将通过对象传递通知标识符以及通知内容 `request` 。 需要调用传入的以向 `contentHandler` 用户显示通知。

第二种方法 `TimeWillExpire` 将在即将为服务扩展处理请求的时间即将用完之前调用。 如果服务扩展未能 `contentHandler` 在分配的时间内调用，则会向用户显示原始内容。

### <a name="triggering-a-service-extension"></a>触发服务扩展

通过使用应用程序创建和传递的服务扩展，可以通过修改发送到设备的远程通知负载来触发。 例如：

```csharp
{
    aps : {
        alert : "New Message Available",
        mutable-content: 1
    },
    encrypted-content : "#theencryptedcontent"
}
```

新 `mutable-content` 密钥指定需要启动服务扩展以更新远程通知内容。 在 `encrypted-content` 向用户提供服务扩展之前，密钥包含服务扩展可以解密的加密数据。

请查看以下示例服务扩展：

```csharp
using UserNotification;

namespace myApp {
    public class NotificationService : UNNotificationServiceExtension {
    
        public override void DidReceiveNotificationRequest(UNNotificationRequest request, contentHandler) {
            // Decrypt payload
            var decryptedBody = Decrypt(Request.Content.UserInfo["encrypted-content"]);
            
            // Modify Notification body
            var newContent = new UNMutableNotificationContent();
            newContent.Body = decryptedBody;
            
            // Present to user
            contentHandler(newContent);
        }
        
        public override void TimeWillExpire() {
            // Handle out-of-time fallback event
            ...
        }
        
    }
}
```

此代码从密钥解密加密内容 `encrypted-content` ，创建新的 `UNMutableNotificationContent` ，将 `Body` 属性设置为已解密的内容，并使用 `contentHandler` 向用户显示通知。

## <a name="summary"></a>总结

本文涵盖了用户通知通过 iOS 10 增强的所有方式。 它提供新的用户通知框架，以及如何在 Xamarin iOS 应用程序或应用程序扩展中使用它。

## <a name="related-links"></a>相关链接

- [iOS 10 示例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS10)
- [UserNotifications Framework 参考](https://developer.apple.com/reference/usernotifications)
- [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui)
- [本地和远程通知编程指南](https://developer.apple.com/documentation/usernotifications)