---
title: 演练-在 Xamarin 中使用本地通知
description: 本部分介绍如何在 Xamarin iOS 应用程序中使用本地通知。 它将演示创建和发布通知的基础知识，该通知将在应用接收到警报时弹出。
ms.prod: xamarin
ms.assetid: 32B9C6F0-2BB3-4295-99CB-A75418969A62
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 764be6319e95b16dc043bebd2abfb27ba0696457
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031409"
---
# <a name="walkthrough---using-local-notifications-in-xamarinios"></a>演练-在 Xamarin 中使用本地通知

_本部分介绍如何在 Xamarin iOS 应用程序中使用本地通知。它将演示创建和发布通知的基础知识，该通知将在应用接收到警报时弹出。_

> [!IMPORTANT]
> 本部分中的信息适用于 iOS 9 及更早版本。 对于 iOS 10 及更高版本，请参阅[用户通知框架指南](~/ios/platform/user-notifications/index.md)，以支持 ios 设备上的本地和远程通知。

## <a name="walkthrough"></a>演练

让我们创建一个简单的应用程序，该应用程序将在操作中显示本地通知。 此应用程序将在其上有一个按钮。 单击该按钮时，它将创建一个本地通知。 经过指定的时间段后，会看到通知出现。

1. 在 Visual Studio for Mac 中，创建一个新的单一查看 iOS 解决方案，并 `Notifications`调用它。
1. 打开 `Main.storyboard` 文件，然后将一个按钮拖到视图上。 命名按钮**按钮**，并为其指定标题 "**添加通知**"。 此时，您可能还需要对按钮设置一些[约束](~/ios/user-interface/designer/designer-auto-layout.md)： 

    ![](local-notifications-in-ios-walkthrough-images/image3.png "Setting some constraints on the button")
1. 编辑 `ViewController` 类，并向 ViewDidLoad 方法添加以下事件处理程序：

    ```csharp
    button.TouchUpInside += (sender, e) =>
    {
        // create the notification
        var notification = new UILocalNotification();

        // set the fire date (the date time in which it will fire)
        notification.FireDate = NSDate.FromTimeIntervalSinceNow(60);

        // configure the alert
        notification.AlertAction = "View Alert";
        notification.AlertBody = "Your one minute alert has fired!";

        // modify the badge
        notification.ApplicationIconBadgeNumber = 1;

        // set the sound to be the default sound
        notification.SoundName = UILocalNotification.DefaultSoundName;

        // schedule it
        UIApplication.SharedApplication.ScheduleLocalNotification(notification);
    };
    ```

    此代码将创建一个使用声音的通知，将图标徽章的值设置为1，并向用户显示一条警报。

1. 接下来，编辑 `AppDelegate.cs`的文件，首先将以下代码添加到 `FinishedLaunching` 方法。 我们已经检查了设备是否正在运行 iOS 8，如果是这样，我们**需要**请求用户是否有权接收通知：

    ```csharp
    if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
        var notificationSettings = UIUserNotificationSettings.GetSettingsForTypes (
            UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound, null
        );

        application.RegisterUserNotificationSettings (notificationSettings);
    }
    ```

1. 在 `AppDelegate.cs`中，添加将在收到通知时调用的以下方法：

    ```csharp
    public override void ReceivedLocalNotification(UIApplication application, UILocalNotification notification)
    {
        // show an alert
        UIAlertController okayAlertController = UIAlertController.Create(notification.AlertAction, notification.AlertBody, UIAlertControllerStyle.Alert);
        okayAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));

        UIApplication.SharedApplication.KeyWindow.RootViewController.PresentViewController(okayAlertController, true, null);

        // reset our badge
        UIApplication.SharedApplication.ApplicationIconBadgeNumber = 0;
    }
    ```

1. 我们需要处理因本地通知而导致通知启动的情况。 编辑 `AppDelegate` 中 `FinishedLaunching` 的方法，包括以下代码片段：

    ```csharp
    // check for a notification

    if (launchOptions != null)
    {
        // check for a local notification
        if (launchOptions.ContainsKey(UIApplication.LaunchOptionsLocalNotificationKey))
        {
            var localNotification = launchOptions[UIApplication.LaunchOptionsLocalNotificationKey] as UILocalNotification;
            if (localNotification != null)
            {
                UIAlertController okayAlertController = UIAlertController.Create(localNotification.AlertAction, localNotification.AlertBody, UIAlertControllerStyle.Alert);
                okayAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));

                Window.RootViewController.PresentViewController(okayAlertController, true, null);

                // reset our badge
                UIApplication.SharedApplication.ApplicationIconBadgeNumber = 0;
            }
        }
    }
    ```

1. 最后，运行该应用程序。 在 iOS 8 上，系统将提示你允许通知。 单击 **"确定"** ，然后单击 "**添加通知**" 按钮。 短暂停顿后，会看到警报对话框，如以下屏幕截图所示：

    ![](local-notifications-in-ios-walkthrough-images/image0.png "确认发送通知的能力") ![](local-notifications-in-ios-walkthrough-images/image1.png ""添加通知" 按钮")
    ![](local-notifications-in-ios-walkthrough-images/image2.png "The notification alert dialog")

## <a name="summary"></a>总结

本演练演示了如何使用各种 API 在 iOS 中创建和发布通知。 它还演示了如何使用徽章更新应用程序图标，以向用户提供一些特定于应用程序的反馈。

## <a name="related-links"></a>相关链接

- [本地通知（示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/localnotifications)
- [本地和推送通知编程指南](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)
