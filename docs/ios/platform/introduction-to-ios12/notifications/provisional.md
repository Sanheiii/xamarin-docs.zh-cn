---
title: Xamarin 中的临时通知
description: 本文档介绍如何使用 Xamarin 处理临时通知。 IOS 12 中引入的临时通知允许应用程序在没有显式用户权限的情况下发送静默通知。
ms.prod: xamarin
ms.assetid: 5DCB36B9-2637-48AE-8FC0-F6124F08AC48
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/04/2018
ms.openlocfilehash: 745bfbc56dec12b7d46003a1d488e5638dc6c110
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435177"
---
# <a name="provisional-notifications-in-xamarinios"></a>Xamarin 中的临时通知

临时通知允许应用在无需用户的明确提前同意的情况下提供通知。 这些通知会以静默方式到达并仅显示在 "通知中心" 中，这使用户可以在退出或退出其持续交付之前预览它们。

在通知中心，用户可以指定应用应停止提供临时通知、继续向其传递暂时或开始更突出地交付。

## <a name="sample-app-redgreennotifications"></a>示例应用： RedGreenNotifications

查看发送临时通知的 [RedGreenNotifications](/samples/xamarin/ios-samples/ios12-redgreennotifications) 示例应用。

## <a name="sending-provisional-notifications"></a>发送临时通知

若要发送临时通知，请提供 `UNAuthorizationOptions.Provisional` 作为选项 [`RequestAuthorization`](xref:UserNotifications.UNUserNotificationCenter.RequestAuthorization*)
方法 `UNUserNotificationCenter` ：

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    var options = UNAuthorizationOptions.Alert | UNAuthorizationOptions.Sound | UNAuthorizationOptions.Provisional;
    center.RequestAuthorization(options, (bool success, NSError error) => {
        // ...
    );
    return true;
}
```

如果用户将临时通知升级为显著交付，则 `UNAuthorizationOptions` 传递给的值 `RequestAuthorization` 将确定在上述代码中 (的新通知传递设置， `UNAuthorizationOptions.Alert` 并 `UNAuthorizationOptions.Sound`) 。

## <a name="related-links"></a>相关链接

- [示例应用– RedGreenNotifications](/samples/xamarin/ios-samples/ios12-redgreennotifications)
- [Xamarin 中的用户通知框架](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple) ](https://developer.apple.com/documentation/usernotifications?language=objc)
- [用户通知中的新增功能 (WWDC 2018) ](https://developer.apple.com/videos/play/wwdc2018/710/)
- [ (WWDC 2017) 的最佳实践和用户通知中的新增功能 ](https://developer.apple.com/videos/play/wwdc2017/708/)
- [ (Apple) 生成远程通知 ](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)