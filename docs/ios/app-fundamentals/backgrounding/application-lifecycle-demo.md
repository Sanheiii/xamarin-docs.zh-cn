---
title: 适用于 Xamarin 的应用程序生命周期演示
description: 本文档介绍 iOS 应用程序中由应用程序委托处理的各种生命周期事件，并演示如何处理这些事件。
ms.prod: xamarin
ms.assetid: 5C8AACA6-49F8-4C6D-99C3-5F443C01B230
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/17/2018
ms.openlocfilehash: 08ef81f55ac1705224f2953197bfe582718b9cf7
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91432508"
---
# <a name="application-lifecycle-demo-for-xamarinios"></a>适用于 Xamarin 的应用程序生命周期演示

本文和 [代码示例](/samples/xamarin/ios-samples/lifecycledemo) 演示了 iOS 中的四种应用程序状态，以及 `AppDelegate` 在状态发生更改时通知应用程序的方法的角色。 应用程序会在应用更改状态时将更新打印到控制台：

[![示例应用](application-lifecycle-demo-images/image3-sml.png)](application-lifecycle-demo-images/image3.png#lightbox)

[![应用会在应用更改状态时将更新打印到控制台](application-lifecycle-demo-images/image4.png)](application-lifecycle-demo-images/image4.png#lightbox)

## <a name="walkthrough"></a>演练

1. 在**LifecycleDemo**解决方案中打开**生命周期**项目。
1. 打开 `AppDelegate` 类。 已将日志记录添加到生命周期方法，以指示应用程序何时发生更改状态：

    ```csharp
    public override void OnActivated(UIApplication application)
    {
        Console.WriteLine("OnActivated called, App is active.");
    }
    public override void WillEnterForeground(UIApplication application)
    {
        Console.WriteLine("App will enter foreground");
    }
    public override void OnResignActivation(UIApplication application)
    {
        Console.WriteLine("OnResignActivation called, App moving to inactive state.");
    }
    public override void DidEnterBackground(UIApplication application)
    {
        Console.WriteLine("App entering background state.");
    }
    // not guaranteed that this will run
    public override void WillTerminate(UIApplication application)
    {
        Console.WriteLine("App is terminating.");
    }
    ```

1. 在模拟器或设备上启动应用程序。 `OnActivated` 应用启动时将调用。 应用程序现在处于 " _活动_ " 状态。
1. 点击模拟器或设备上的 "主页" 按钮，使应用程序进入背景。 `OnResignActivation``DidEnterBackground`当应用从转换到状态时，将调用和 `Active` `Inactive` `Backgrounded` 。 由于没有设置为在后台执行的应用程序代码，因此该应用程序在内存中被视为 _挂起_ 。
1. 向后导航到该应用，使其返回到前台。 `WillEnterForeground` 和 `OnActivated` 都将调用：

    ![将状态更改打印到控制台](application-lifecycle-demo-images/image4.png)

    当应用程序从后台进入前台并更改屏幕上显示的文本时，将执行视图控制器中的以下代码行：

    ```csharp
    UIApplication.Notifications.ObserveWillEnterForeground ((sender, args) => {
        label.Text = "Welcome back!";
    });
    ```

1. 按 " **主页** " 按钮，将应用程序放入后台。 然后，双击 " **主页** " 按钮，打开应用程序切换器。 在 iPhone X 上，从屏幕的底部向上轻扫：

    [![应用程序切换器](application-lifecycle-demo-images/app-switcher-sml.png "应用程序切换器")](application-lifecycle-demo-images/app-switcher.png#lightbox)
  
1. 在应用程序切换器中找到应用程序，然后向上轻扫以将其 (在 iOS 11 上，长按，直到红色图标出现在角) ：

    [![向上轻扫以删除正在运行的应用](application-lifecycle-demo-images/app-switcher-swipe-sml.png "向上轻扫以删除正在运行的应用")](application-lifecycle-demo-images/app-switcher-swipe.png#lightbox)

iOS 将终止应用程序。 请注意， `WillTerminate` 由于应用程序已在后台 _挂起_ ，因此不会调用。

## <a name="related-links"></a>相关链接

- [LifecycleDemo (示例) ](/samples/xamarin/ios-samples/lifecycledemo)