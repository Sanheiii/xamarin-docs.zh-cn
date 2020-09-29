---
title: Xamarin 中的通知管理
description: 本文档介绍了如何使用 Xamarin 来利用 iOS 12 中引入的新的通知管理功能。
ms.prod: xamarin
ms.assetid: F1D90729-F85A-425B-B633-E2FA38FB4A0C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/04/2018
ms.openlocfilehash: 9189cfaee9c6cdebc8e269537993bc797509d9a4
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435187"
---
# <a name="notification-management-in-xamarinios"></a>Xamarin 中的通知管理

在 iOS 12 中，操作系统可从通知中心和 "设置" 应用深层链接到应用的通知管理屏幕。 此屏幕应该允许用户选择加入和退出应用发送的各种类型的通知。

## <a name="sample-app-redgreennotifications"></a>示例应用： RedGreenNotifications

若要查看通知管理工作原理的示例，请查看 [RedGreenNotifications](/samples/xamarin/ios-samples/ios12-redgreennotifications) 示例应用。

此示例应用将发送两种类型的通知（红色和绿色），并提供一个屏幕，使用户能够选择加入或退出任意一种类型。

本指南中的代码片段来自此示例应用。

## <a name="notification-management-screen"></a>通知管理屏幕

在示例应用中， `ManageNotificationsViewController` 定义了一个用户界面，该用户界面允许用户单独启用和禁用红色通知和绿色通知。 这是一个标准的 [`UIViewController`](xref:UIKit.UIViewController)
[`UISwitch`](xref:UIKit.UISwitch)为每个通知类型包含一个。 对于任一类型的通知，请切换开关，在用户默认情况下，针对该类型通知的用户首选项：

```csharp
partial void HandleRedNotificationsSwitchValueChange(UISwitch sender)
{
    NSUserDefaults.StandardUserDefaults.SetBool(sender.On, RedNotificationsEnabledKey);
}
```

> [!NOTE]
> 通知管理屏幕还会检查用户是否已完全禁用该应用的通知。 如果是这样，则隐藏单个通知类型的切换。 为此，通知管理屏幕：
>
> - 调用 [`UNUserNotificationCenter.Current.GetNotificationSettingsAsync`](xref:UserNotifications.UNUserNotificationCenter.GetNotificationSettingsAsync) 并检查 [`AuthorizationStatus`](xref:UserNotifications.UNNotificationSettings.AuthorizationStatus) 属性。
> - 如果已完全禁用应用的通知，则隐藏单个通知类型的切换。
> - 在应用程序每次移动到前台时，重新检查是否已禁用通知，因为用户可以随时在 iOS 设置中启用/禁用通知。

示例应用的 `ViewController` 类（用于发送通知）将在发送本地通知之前检查用户的首选项，以确保通知的类型为用户实际要接收的类型：

```csharp
partial void HandleTapRedNotificationButton(UIButton sender)
{
    bool redEnabled = NSUserDefaults.StandardUserDefaults.BoolForKey(ManageNotificationsViewController.RedNotificationsEnabledKey);
    if (redEnabled)
    {
        // ...
```

## <a name="deep-link"></a>深层链接

iOS 深层链接到应用的通知管理屏幕，从通知中心到应用的通知设置（位于 "设置" 应用中）。 若要简化此操作，应用必须：

- 通过传递 `UNAuthorizationOptions.ProvidesAppNotificationSettings` 到应用的通知授权请求，指示通知管理屏幕可用。
- 实现 `OpenSettings` 中的方法 [`IUNUserNotificationCenterDelegate`](xref:UserNotifications.IUNUserNotificationCenterDelegate) 。

### <a name="authorization-request"></a>授权请求

若要向操作系统指示通知管理屏幕可用，应用应将 `UNAuthorizationOptions.ProvidesAppNotificationSettings` 选项 (连同任何其他通知传递选项一起传递给中的 `RequestAuthorization` 方法) `UNUserNotificationCenter` 。

例如，在示例应用程序中 `AppDelegate` ：

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    // Request authorization to send notifications
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    var options = UNAuthorizationOptions.ProvidesAppNotificationSettings | UNAuthorizationOptions.Alert | UNAuthorizationOptions.Sound | UNAuthorizationOptions.Provisional;
    center.RequestAuthorization(options, (bool success, NSError error) =>
    {
        // ...
```

### <a name="opensettings-method"></a>OpenSettings 方法

`OpenSettings`系统调用以深层链接到应用的通知管理屏幕的方法应直接在该屏幕上导航用户。

在示例应用中，如果需要，此方法会执行的 segue `ManageNotificationsViewController` ：

```csharp
[Export("userNotificationCenter:openSettingsForNotification:")]
public void OpenSettings(UNUserNotificationCenter center, UNNotification notification)
{
    var navigationController = Window.RootViewController as UINavigationController;
    if (navigationController != null)
    {
        var currentViewController = navigationController.VisibleViewController;
        if (currentViewController is ViewController)
        {
            currentViewController.PerformSegue(ManageNotificationsViewController.ShowManageNotificationsSegue, this);
        }

    }
}
```

## <a name="related-links"></a>相关链接

- [示例应用– RedGreenNotifications](/samples/xamarin/ios-samples/ios12-redgreennotifications)
- [Xamarin 中的用户通知框架](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple) ](https://developer.apple.com/documentation/usernotifications?language=objc)
- [用户通知中的新增功能 (WWDC 2018) ](https://developer.apple.com/videos/play/wwdc2018/710/)
- [ (WWDC 2017) 的最佳实践和用户通知中的新增功能 ](https://developer.apple.com/videos/play/wwdc2017/708/)
- [ (Apple) 生成远程通知 ](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)