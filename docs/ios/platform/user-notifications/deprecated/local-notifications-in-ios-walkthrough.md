---
title: 演练-在 Xamarin 中使用本地通知
description: 本部分介绍如何在 Xamarin iOS 应用程序中使用本地通知。 它将演示创建和发布通知的基础知识，该通知将在应用接收到警报时弹出。
ms.prod: xamarin
ms.assetid: 32B9C6F0-2BB3-4295-99CB-A75418969A62
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 915b5cb11aed96598e0460125734b15757f45466
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436138"
---
# <a name="walkthrough---using-local-notifications-in-xamarinios"></a>演练-在 Xamarin 中使用本地通知

_本部分介绍如何在 Xamarin iOS 应用程序中使用本地通知。它将演示创建和发布通知的基础知识，该通知将在应用接收到警报时弹出。_

> [!IMPORTANT]
> 本部分中的信息适用于 iOS 9 及更早版本。 对于 iOS 10 及更高版本，请参阅 [用户通知框架指南](~/ios/platform/user-notifications/index.md) ，以支持 ios 设备上的本地和远程通知。

## <a name="walkthrough"></a>演练

让我们创建一个简单的应用程序，该应用程序将在操作中显示本地通知。 此应用程序将在其上有一个按钮。 单击该按钮时，它将创建一个本地通知。 经过指定的时间段后，会看到通知出现。

1. 在 Visual Studio for Mac 中，创建一个新的单一视图 iOS 解决方案并调用它 `Notifications` 。
1. 打开该 `Main.storyboard` 文件，并将一个按钮拖到视图上。 命名按钮 **按钮**，并为其指定标题 " **添加通知**"。 此时，您可能还需要对按钮设置一些 [约束](~/ios/user-interface/designer/designer-auto-layout.md) ： 

    ![设置按钮的某些约束](local-notifications-in-ios-walkthrough-images/image3.png)
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

1. 接下来，编辑文件 `AppDelegate.cs` ，首先将以下代码添加到 `FinishedLaunching` 方法。 我们已经检查了设备是否正在运行 iOS 8，如果是这样，我们 **需要** 请求用户是否有权接收通知：

    ```csharp
    if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
        var notificationSettings = UIUserNotificationSettings.GetSettingsForTypes (
            UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound, null
        );

        application.RegisterUserNotificationSettings (notificationSettings);
    }
    ```

1. 仍在中 `AppDelegate.cs` ，添加以下方法，在收到通知时将调用该方法：

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

1. 我们需要处理因本地通知而导致通知启动的情况。 编辑中的方法 `FinishedLaunching` `AppDelegate` 以包含以下代码片段：

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

1. 最后，运行该应用程序。 在 iOS 8 上，系统将提示你允许通知。 单击 **"确定"** ，然后单击 " **添加通知** " 按钮。 短暂停顿后，会看到警报对话框，如以下屏幕截图所示：

    ![确认能否发送通知 ](local-notifications-in-ios-walkthrough-images/image0.png) ![ "添加通知" 按钮 " ](local-notifications-in-ios-walkthrough-images/image1.png) ![ 通知警报" 对话框](local-notifications-in-ios-walkthrough-images/image2.png)

## <a name="summary"></a>总结

本演练演示了如何使用各种 API 在 iOS 中创建和发布通知。 它还演示了如何使用徽章更新应用程序图标，以向用户提供一些特定于应用程序的反馈。

## <a name="related-links"></a>相关链接

- [ (示例的本地通知) ](/samples/xamarin/ios-samples/localnotifications)
- [本地和推送通知编程指南](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)