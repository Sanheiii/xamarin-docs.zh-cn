---
title: Xamarin 中的动态通知操作按钮
description: 使用 iOS 12，通知内容扩展插件可以添加、删除和更新与通知一起显示的操作按钮。 本文档介绍如何对 Xamarin 使用动态通知操作按钮。
ms.prod: xamarin
ms.assetid: 6B34AD78-5117-42D0-B6E7-C8B4B453EAFF
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/04/2018
ms.openlocfilehash: 88eb3dec0c72acd6984b226eae5acee4feb3eaac
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433820"
---
# <a name="dynamic-notification-action-buttons-in-xamarinios"></a>Xamarin 中的动态通知操作按钮

在 iOS 12 中，通知可以动态地添加、删除和更新其关联的操作按钮。 此类自定义使用户能够向用户提供直接与通知内容相关的操作以及用户与之交互的操作。

## <a name="sample-app-redgreennotifications"></a>示例应用： RedGreenNotifications

本指南中的代码片段来自 [RedGreenNotifications](/samples/xamarin/ios-samples/ios12-redgreennotifications) 示例应用程序，该应用程序演示如何在 ios 12 中使用 Xamarin 来处理通知操作按钮。

此示例应用将发送两种类型的本地通知：红色和绿色。
在应用程序发送通知后，使用3D 触控查看其自定义用户界面。 然后，使用通知的操作按钮来旋转显示的图像。 当图像旋转时，" **重置旋转** " 按钮将显示并在必要时消失。

本指南中的代码片段来自此示例应用。

## <a name="default-action-buttons"></a>默认操作按钮

通知的类别决定了其默认操作按钮。

在应用程序启动时创建和注册通知类别。
例如，在 [示例应用程序](#sample-app-redgreennotifications)中，的 `FinishedLaunching` 方法将 `AppDelegate` 执行以下操作：

- 为红色通知定义一个类别，并为绿色通知定义另一个类别
- 通过调用 [`SetNotificationCategories`](xref:UserNotifications.UNUserNotificationCenter.SetNotificationCategories*)
方法 `UNUserNotificationCenter`
- 附加单个 [`UNNotificationAction`](xref:UserNotifications.UNNotificationAction)
到每个类别

下面的示例代码演示如何工作：

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    // Request authorization to send notifications
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    var options = UNAuthorizationOptions.Alert | UNAuthorizationOptions.Sound | UNAuthorizationOptions.Provisional | UNAuthorizationOptions.ProvidesAppNotificationSettings;
    center.RequestAuthorization(options, (bool success, NSError error) =>
    {
        // ...
        var rotateTwentyDegreesAction = UNNotificationAction.FromIdentifier("rotate-twenty-degrees-action", "Rotate 20°", UNNotificationActionOptions.None);

        var redCategory = UNNotificationCategory.FromIdentifier(
            "red-category",
            new UNNotificationAction[] { rotateTwentyDegreesAction },
            new string[] { },
            UNNotificationCategoryOptions.CustomDismissAction
        );

        var greenCategory = UNNotificationCategory.FromIdentifier(
            "green-category",
            new UNNotificationAction[] { rotateTwentyDegreesAction },
            new string[] { },
            UNNotificationCategoryOptions.CustomDismissAction
        );

        var set = new NSSet<UNNotificationCategory>(redCategory, greenCategory);
        center.SetNotificationCategories(set);
    });
    // ...
}
```

根据此代码，任何 [`Content.CategoryIdentifier`](xref:UserNotifications.UNNotificationContent.CategoryIdentifier)
默认情况下，为 "红色类别" 或 "绿色类别"，将显示 " **旋转20°** " 操作按钮。

## <a name="in-app-handling-of-notification-action-buttons"></a>通知操作按钮的应用内处理

`UNUserNotificationCenter` 具有 `Delegate` 类型为的属性 [`IUNUserNotificationCenterDelegate`](xref:UserNotifications.IUNUserNotificationCenterDelegate) 。

在示例应用中， `AppDelegate` 将自身设置为中的用户通知中心的委托 `FinishedLaunching` ：

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    // Request authorization to send notifications
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    var options = // ...
    center.RequestAuthorization(options, (bool success, NSError error) =>
    {
        center.Delegate = this;
        // ...
```

然后， `AppDelegate` 实现 [`DidReceiveNotificationResponse`](xref:UserNotifications.UNUserNotificationCenterDelegate_Extensions.DidReceiveNotificationResponse*)
处理操作按钮点击：

```csharp
[Export("userNotificationCenter:didReceiveNotificationResponse:withCompletionHandler:")]
public void DidReceiveNotificationResponse(UNUserNotificationCenter center, UNNotificationResponse response, System.Action completionHandler)
{
    if (response.IsDefaultAction)
    {
        Console.WriteLine("ACTION: Default");
    }
    if (response.IsDismissAction)
    {
        Console.WriteLine("ACTION: Dismiss");
    }
    else
    {
        Console.WriteLine($"ACTION: {response.ActionIdentifier}");
    }

    completionHandler();
        }
```

此实现 `DidReceiveNotificationResponse` 不会处理通知的 " **旋转20°** " 操作按钮。 相反，通知的内容扩展会处理此按钮。 下一节将进一步讨论通知操作按钮处理。

## <a name="action-buttons-in-the-notification-content-extension"></a>通知内容扩展中的操作按钮

通知内容扩展插件包含定义通知自定义接口的视图控制器。

此视图控制器可以 `GetNotificationActions` `SetNotificationActions` 在其上使用和方法 [`ExtensionContext`](xref:UIKit.UIViewController.ExtensionContext)
用于访问和修改通知的操作按钮的属性。

在示例应用中，通知内容扩展的视图控制器仅在响应 "已存在的操作" 按钮时才会修改操作按钮。

> [!NOTE]
> 通知内容扩展可以在其视图控制器的方法中进行响应，并将其 [`DidReceiveNotificationResponse`](xref:UserNotificationsUI.UNNotificationContentExtension_Extensions.DidReceiveNotificationResponse*) 声明为 [IUNNotificationContentExtension](xref:UserNotificationsUI.IUNNotificationContentExtension)的一部分。
>
> 尽管它与上述方法共享一个 `DidReceiveNotificationResponse` 名称[described above](#in-app-handling-of-notification-action-buttons)，但这是一种不同的方法。
>
> 在通知内容扩展完成对按钮点击的处理后，可以选择是否告知主应用程序处理相同的按钮点击。 为此，它必须将适当的 [UNNotificationContentExtensionResponseOption](xref:UserNotificationsUI.UNNotificationContentExtensionResponseOption) 值传递到其完成处理程序：
>
> - `Dismiss` 指示应消除通知界面，并且主应用不需要处理按钮点击。
> - `DismissAndForwardAction` 指示应消除通知界面，并且主应用还应处理按钮点击。
> - `DoNotDismiss` 指示不应消除通知界面，并且主应用不需要处理按钮点击。

内容扩展的 `DidReceiveNotificationResponse` 方法确定点击了哪个操作按钮，在通知的接口中旋转图像，并显示或隐藏 " **重置** 操作" 按钮：

```csharp
[Export("didReceiveNotificationResponse:completionHandler:")]
public void DidReceiveNotificationResponse(UNNotificationResponse response, Action<UNNotificationContentExtensionResponseOption> completionHandler)
{
    var rotationAction = ExtensionContext.GetNotificationActions()[0];

    if (response.ActionIdentifier == "rotate-twenty-degrees-action")
    {
        rotationButtonTaps += 1;

        double radians = (20 * rotationButtonTaps) * (2 * Math.PI / 360.0);
        Xamagon.Transform = CGAffineTransform.MakeRotation((float)radians);

        // 9 rotations * 20 degrees = 180 degrees. No reason to
        // show the reset rotation button when the image is half
        // or fully rotated.
        if (rotationButtonTaps % 9 == 0)
        {
            ExtensionContext.SetNotificationActions(new UNNotificationAction[] { rotationAction });
        }
        else if (rotationButtonTaps % 9 == 1)
        {
            var resetRotationAction = UNNotificationAction.FromIdentifier("reset-rotation-action", "Reset rotation", UNNotificationActionOptions.None);
            ExtensionContext.SetNotificationActions(new UNNotificationAction[] { rotationAction, resetRotationAction });
        }
    }

    if (response.ActionIdentifier == "reset-rotation-action")
    {
        rotationButtonTaps = 0;

        double radians = (20 * rotationButtonTaps) * (2 * Math.PI / 360.0);
        Xamagon.Transform = CGAffineTransform.MakeRotation((float)radians);

        ExtensionContext.SetNotificationActions(new UNNotificationAction[] { rotationAction });
    }

    completionHandler(UNNotificationContentExtensionResponseOption.DoNotDismiss);
}
```

在这种情况下，方法会将传递 `UNNotificationContentExtensionResponseOption.DoNotDismiss` 给它的完成处理程序。 这意味着通知的接口将保持打开状态。

## <a name="related-links"></a>相关链接

- [示例应用– RedGreenNotifications](/samples/xamarin/ios-samples/ios12-redgreennotifications)
- [Xamarin 中的用户通知框架](~/ios/platform/user-notifications/index.md)
- [声明可操作的通知类型](https://developer.apple.com/documentation/usernotifications/declaring_your_actionable_notification_types?language=objc)
- [UserNotifications (Apple) ](https://developer.apple.com/documentation/usernotifications?language=objc)
- [用户通知中的新增功能 (WWDC 2018) ](https://developer.apple.com/videos/play/wwdc2018/710/)
- [ (WWDC 2017) 的最佳实践和用户通知中的新增功能 ](https://developer.apple.com/videos/play/wwdc2017/708/)
- [ (Apple) 生成远程通知 ](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)