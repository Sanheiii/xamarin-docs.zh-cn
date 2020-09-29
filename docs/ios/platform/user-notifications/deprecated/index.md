---
title: Xamarin 中不推荐使用的通知技术
description: 本文档介绍了已弃用的 iOS 通知技术，以支持 iOS 10 中引入的用户通知框架。
ms.prod: xamarin
ms.assetid: 20C4F6E5-56DF-4A85-BBF0-E38C88586307
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/07/2016
ms.openlocfilehash: 0822f630d74563f5e6e0dcefa456cf5f5c07a8f5
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436944"
---
# <a name="deprecated-notification-technologies-in-xamarinios"></a>Xamarin 中不推荐使用的通知技术

本部分说明如何在 Xamarin 中实现本地和推送通知。 它将解释 iOS 通知的各种 UI 元素，并讨论创建和显示通知所涉及的 API。

> [!IMPORTANT]
> 本部分中的信息适用于 iOS 9 及更早版本。 对于 iOS 10 及更高版本，请参阅 [用户通知框架指南](~/ios/platform/user-notifications/index.md) ，以支持 ios 设备上的本地和远程通知。

## <a name="sections"></a>部分

<a name="Local Notifications In iOS"></a>

## <a name="local-notifications-in-ios"></a>[IOS 中的本地通知](local-notifications-in-ios.md)

本部分将讨论如何在 Xamarin 中实现本地通知。 它将解释 iOS 通知的各种 UI 元素，并讨论创建和显示通知所涉及的 API。

<a name="Local Notifications Walkthrough"></a>

## <a name="walkthrough---using-local-notifications-in-xamarinios"></a>[演练-在 Xamarin 中使用本地通知](local-notifications-in-ios-walkthrough.md)

本部分介绍如何在 Xamarin iOS 应用程序中使用本地通知。 它将演示创建和发布通知的基础知识，该通知将在应用接收到警报时弹出。

<a name="Remote Notifications In iOS"></a>

## <a name="remote-notifications-in-ios"></a>[IOS 中的远程通知](remote-notifications-in-ios.md)

本部分将介绍 iOS 中的推送通知。 它介绍了 Apple 推送通知网关服务 (APNS) ，以及在向 iOS 应用程序发布通知时所扮演的角色。 它将说明如何创建启用推送通知和讨论所需的安全证书。 最后，本部分将讨论一些应用程序服务器必须执行的日常任务，以跟踪客户端移动设备。

## <a name="related-links"></a>相关链接

- [通知 (示例) ](/samples/xamarin/ios-samples/notifications)