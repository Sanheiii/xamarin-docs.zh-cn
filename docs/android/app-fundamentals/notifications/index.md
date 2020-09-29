---
title: Xamarin.Android 中的通知
ms.prod: xamarin
ms.assetid: 2E54F1D0-45F4-43A7-B3A3-4F483B7150CB
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: ce19a286a288846a67b73e77d31a5da4c77a2462
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91455581"
---
# <a name="notifications-in-xamarinandroid"></a>Xamarin.Android 中的通知

本部分介绍如何在 Xamarin 中实施通知。 它介绍了 Android 通知的各种 UI 元素，并讨论了创建和显示通知所涉及的 API。

## <a name="local-notifications-in-android"></a>[Android 中的本地通知](local-notifications.md)

本部分介绍如何在 Xamarin 中实现本地通知。 它介绍了 Android 通知的各种 UI 元素，并讨论了创建和显示通知所涉及的 Api。

## <a name="walkthrough---using-local-notifications-in-xamarinandroid"></a>[演练-在 Xamarin 中使用本地通知](local-notifications-walkthrough.md)  

本演练介绍如何在 Xamarin Android 应用程序中使用本地通知。 它演示了创建和发布通知的基础知识。 当用户单击通知银箱中的通知时，它会启动第二个活动。 

## <a name="further-reading"></a>其他阅读材料

[Firebase 云消息传送](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) &ndash; Firebase Cloud 消息 (FCM) 是一种有助于在移动应用和服务器应用程序之间进行消息传递的服务。 Firebase 云消息传送可用于实现远程通知 (也称为 Xamarin 应用程序) 的推送通知。

[通知](https://developer.android.com/guide/topics/ui/notifiers/notifications.html) &ndash; 此 Android 开发人员主题是适用于 Android 通知的最终指南。 它包含一个设计注意事项部分，该部分可帮助你设计通知，以使其符合 Android 用户界面的准则。 它提供有关启动活动时保留导航的更多背景信息，并说明如何在通知中显示进度并在锁屏界面上控制媒体播放。

[NotificationListenerService](xref:Android.Service.Notification.NotificationListenerService) &ndash; 此 Android 服务使你的应用程序可以侦听 (并与在 Android 设备上发布的所有通知) 交互，而不只是应用注册接收的通知。
请注意，用户必须显式授予你的应用程序的权限，以便能够侦听设备上的通知。

## <a name="related-links"></a>相关链接

- [ (示例的本地通知) ](/samples/xamarin/monodroid-samples/localnotifications)