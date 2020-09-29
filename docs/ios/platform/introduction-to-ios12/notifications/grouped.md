---
title: Xamarin 中的分组通知
description: 使用 iOS 12，可以在通知中心或锁定屏幕上按应用程序或线程对通知进行分组。 本文档介绍如何通过 Xamarin 发送线程和 unthreaded 通知。
ms.prod: xamarin
ms.assetid: C6FA7C25-061B-4FD7-8E55-88597D512F3C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/04/2018
ms.openlocfilehash: 8c440d61a41fc26aa7537dc169be08c2bf413c68
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435215"
---
# <a name="grouped-notifications-in-xamarinios"></a>Xamarin 中的分组通知

默认情况下，iOS 12 将所有应用的通知放置在一个组中。 锁定屏幕和通知中心将此组显示为最顶部有最新通知的堆栈。 用户可以展开组以查看它包含的所有通知，并将整个组关闭。

应用还可以按线程对通知进行分组，使用户能够更轻松地找到所需的特定信息并与其进行交互。

## <a name="sample-app-groupednotifications"></a>示例应用： GroupedNotifications

若要了解如何对 Xamarin 使用分组通知，请查看 [GroupedNotifications](/samples/xamarin/ios-samples/ios12-groupednotifications) 示例应用。

此示例应用模拟与各个朋友的对话，发送每条消息的通知并按线程对消息进行分组。 它还演示了如何在应用程序级别的组中 unthreaded 通知。

本指南中的代码片段来自此示例应用。

## <a name="request-authorization-and-allow-foreground-notifications"></a>请求授权并允许前台通知

在应用可以发送本地通知之前，它必须请求权限才能执行此操作。 在示例应用程序中 [`AppDelegate`](xref:UIKit.UIApplicationDelegate) ， [`FinishedLaunching`](xref:UIKit.UIApplicationDelegate.FinishedLaunching(UIKit.UIApplication,Foundation.NSDictionary)) 方法请求此权限：

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    center.RequestAuthorization(UNAuthorizationOptions.Alert, (bool success, NSError error) =>
    {
        // Set the Delegate regardless of success; users can modify their notification
        // preferences at any time in the Settings app.
        center.Delegate = this;
    });
    return true;
}
```

[`Delegate`](xref:UserNotifications.UNUserNotificationCenter.Delegate)上述 (设置) [`UNUserNotificationCenter`](xref:UserNotifications.UNUserNotificationCenter) 确定前景应用是否应通过调用传递到的完成处理程序来显示传入通知 [`WillPresentNotification`](xref:UserNotifications.UNUserNotificationCenterDelegate_Extensions.WillPresentNotification(UserNotifications.IUNUserNotificationCenterDelegate,UserNotifications.UNUserNotificationCenter,UserNotifications.UNNotification,System.Action{UserNotifications.UNNotificationPresentationOptions})) ：

```csharp
[Export("userNotificationCenter:willPresentNotification:withCompletionHandler:")]
public void WillPresentNotification(UNUserNotificationCenter center, UNNotification notification, System.Action<UNNotificationPresentationOptions> completionHandler)
{
    completionHandler(UNNotificationPresentationOptions.Alert);
}
```

[`UNNotificationPresentationOptions.Alert`](xref:UserNotifications.UNNotificationPresentationOptions)参数指示应用应显示警报，但不播放声音或更新徽章。

## <a name="threaded-notifications"></a>线程通知

重复 **使用 Alice** 按钮点击示例应用的消息，让其使用名为 Alice 的朋友发送会话通知。
由于此对话的通知是同一线程的一部分，因此锁屏界面和通知中心将它们组合在一起。

若要开始与其他朋友的对话，请点击 " **选择新朋友** " 按钮。 此对话的通知显示在单独的组中。

### <a name="threadidentifier"></a>ThreadIdentifier

每当示例应用启动新线程时，它都会创建一个唯一的线程标识符：

```csharp
void StartNewThread()
{
    threadId = $"message-{friend}";
    // ...
}
```

若要发送线程通知，示例应用：

- 检查应用是否有权发送通知。
- 创建一个 [`UNMutableNotificationContent`](xref:UserNotifications.UNMutableNotificationContent)
通知内容的对象，并将其 [`ThreadIdentifier`](xref:UserNotifications.UNMutableNotificationContent.ThreadIdentifier)
到上面创建的线程标识符。
- 创建请求并计划通知：

```csharp
async partial void ScheduleThreadedNotification(UIButton sender)
{
    var center = UNUserNotificationCenter.Current;

    UNNotificationSettings settings = await center.GetNotificationSettingsAsync();
    if (settings.AuthorizationStatus != UNAuthorizationStatus.Authorized)
    {
        return;
    }

    string author =  // ...
    string message = // ...

    var content = new UNMutableNotificationContent()
    {
        ThreadIdentifier = threadId,
        Title = author,
        Body = message,
        SummaryArgument = author
    };

    var request = UNNotificationRequest.FromIdentifier(
        Guid.NewGuid().ToString(),
        content,
        UNTimeIntervalNotificationTrigger.CreateTrigger(1, false)
    );

    center.AddNotificationRequest(request, null);

    // ...
}
```

同一应用中具有相同线程标识符的所有通知都将出现在同一通知组中。

> [!NOTE]
> 若要在远程通知上设置线程标识符，请将该 `thread-id` 密钥添加到通知的 JSON 有效负载中。 有关更多详细信息，请参阅 Apple [生成远程通知](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification) 文档。

### <a name="summaryargument"></a>SummaryArgument

`SummaryArgument` 指定通知将如何影响出现在通知所属的通知组左下角的摘要文本。 iOS 将汇总文本从同一组中的通知聚合起来，以创建整体摘要说明。

示例应用使用消息的作者作为 summary 参数。 使用此方法时，使用 Alice 的六个通知组的摘要文本可能会有 **5 个来自 Alice 和我的通知**。

## <a name="unthreaded-notifications"></a>Unthreaded 通知

每点击一次示例应用的 **约会提醒** 按钮，都将发送各种约会提醒通知。 由于这些提醒不是串接的，它们显示在锁屏界面和通知中心的应用程序级通知组中。

若要发送 unthreaded 通知，示例应用的 `ScheduleUnthreadedNotification` 方法使用类似于上面的代码。
但是，它不会 `ThreadIdentifier` 在对象上设置 `UNMutableNotificationContent` 。

## <a name="related-links"></a>相关链接

- [示例应用– GroupedNotifications](/samples/xamarin/ios-samples/ios12-groupednotifications)
- [Xamarin 中的用户通知框架](~/ios/platform/user-notifications/index.md)
- [用户通知中的新增功能 (WWDC 2018) ](https://developer.apple.com/videos/play/wwdc2018/710/)
- [ (WWDC 2018) 使用分组通知 ](https://developer.apple.com/videos/play/wwdc2018/711/)
- [ (WWDC 2017) 的最佳实践和用户通知中的新增功能 ](https://developer.apple.com/videos/play/wwdc2017/708/)
- [ (Apple) 生成远程通知 ](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)