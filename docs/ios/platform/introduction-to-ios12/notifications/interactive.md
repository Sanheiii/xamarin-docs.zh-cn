---
title: Xamarin 中的交互式通知用户界面
description: 对于 iOS 12，可以创建用于本地和远程通知的交互式用户界面。 本指南介绍如何将这些功能与 Xamarin 配合使用。
ms.prod: xamarin
ms.assetid: E3562E1B-E0EF-4C99-9F51-59DE22AFDE46
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/04/2018
ms.openlocfilehash: af06aa4dc7ea836887edcc0416cfd15ce3aa1446
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433776"
---
# <a name="interactive-notification-user-interfaces-in-xamarinios"></a>Xamarin 中的交互式通知用户界面

在 iOS 10 中引入的[通知内容扩展](~/ios/platform/user-notifications/advanced-user-notifications.md)可以为通知创建自定义用户界面。 从 iOS 12 开始，通知用户界面可包含交互式元素，如按钮和滑块。

## <a name="sample-app-redgreennotifications"></a>示例应用： RedGreenNotifications

[RedGreenNotifications](/samples/xamarin/ios-samples/ios12-redgreennotifications)示例应用包含一个包含交互式用户界面的通知内容扩展。

本指南中的代码片段来自于此示例。

## <a name="notification-content-extension-infoplist-file"></a>通知内容扩展 info.plist 文件

在示例应用程序中， **RedGreenNotificationsContentExtension**项目中的**info.plist**文件包含以下配置：

```xml
<!-- ... -->
<key>NSExtension</key>
<dict>
    <key>NSExtensionAttributes</key>
    <dict>
        <key>UNNotificationExtensionCategory</key>
        <array>
            <string>red-category</string>
            <string>green-category</string>
        </array>
        <key>UNNotificationExtensionUserInteractionEnabled</key>
        <true/>
        <key>UNNotificationExtensionDefaultContentHidden</key>
        <true/>
        <key>UNNotificationExtensionInitialContentSizeRatio</key>
        <real>0.6</real>
    </dict>
    <key>NSExtensionMainStoryboard</key>
    <string>MainInterface</string>
    <key>NSExtensionPointIdentifier</key>
    <string>com.apple.usernotifications.content-extension</string>
    <key></key>
    <true/>
</dict>
<!-- ... -->
```

请注意以下功能：

- `UNNotificationExtensionCategory`数组指定内容扩展处理的通知类别的类型。
- 为了支持交互式内容，通知内容扩展会将 `UNNotificationExtensionUserInteractionEnabled` 密钥设置为 `true` 。
- `UNNotificationExtensionInitialContentSizeRatio`键为内容扩展接口指定初始高度/宽度比。

## <a name="interactive-interface"></a>交互式接口

为通知内容扩展定义接口的 MainInterface 是一个包含单个视图控制器的标准情节提要 **。** 在示例应用中，视图控制器的类型为 `NotificationViewController` ，它包含一个图像视图、三个按钮和一个滑块。 情节提要将这些控件与在 **NotificationViewController.cs**中定义的处理程序相关联：

- " **启动应用** 程序" 按钮处理程序对 `PerformNotificationDefaultAction` 启动应用的操作方法 `ExtensionContext` ：

    ```csharp
    partial void HandleLaunchAppButtonTap(UIButton sender)
    {
        ExtensionContext.PerformNotificationDefaultAction();
    }
    ```

    在应用程序中，用户通知中心的 `Delegate` (在示例应用程序中， `AppDelegate`) 可以响应方法中的交互   `DidReceiveNotificationResponse` ：

    ```csharp
    [Export("userNotificationCenter:didReceiveNotificationResponse:withCompletionHandler:")]
    public void DidReceiveNotificationResponse(UNUserNotificationCenter center, UNNotificationResponse response, System.Action completionHandler)
    {
        if (response.IsDefaultAction)
        {
            Console.WriteLine("ACTION: Default");
            // ...
    ```

- **消除通知**按钮处理程序 `DismissNotificationContentExtension` 在上调用 `ExtensionContext` ，这将关闭通知：

    ```csharp
    partial void HandleDismissNotificationButtonTap(UIButton sender)
    {
        ExtensionContext.DismissNotificationContentExtension();
    }
    ```

- **删除通知**按钮处理程序将关闭通知并将其从通知中心删除：

    ```csharp
    partial void HandleRemoveNotificationButtonTap(UIButton sender)
    {
        ExtensionContext.DismissNotificationContentExtension();
        UNUserNotificationCenter.Current.RemoveDeliveredNotifications(new string[] { notification.Request.Identifier });
    }
    ```

- 处理滑块上值更改的方法更新在通知的接口中显示的图像的 alpha：

    ```csharp
    partial void HandleSliderValueChanged(UISlider sender)
    {
        Xamagon.Alpha = sender.Value;
    }
    ```

## <a name="related-links"></a>相关链接

- [示例应用– RedGreenNotifications](/samples/xamarin/ios-samples/ios12-redgreennotifications)
- [Xamarin 中的用户通知框架](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple) ](https://developer.apple.com/documentation/usernotifications?language=objc)
- [用户通知中的新增功能 (WWDC 2018) ](https://developer.apple.com/videos/play/wwdc2018/710/)
- [ (WWDC 2017) 的最佳实践和用户通知中的新增功能 ](https://developer.apple.com/videos/play/wwdc2017/708/)
- [ (WWDC 2017) 的丰富通知 ](https://developer.apple.com/videos/play/wwdc2017/817/)
- [ (Apple) 生成远程通知 ](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)