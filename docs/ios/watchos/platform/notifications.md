---
title: Xamarin 中的 watchOS 通知
description: 本文档介绍如何在 Xamarin 中使用 watchOS 通知。 它讨论了如何创建通知控制器、生成通知和测试通知。
ms.prod: xamarin
ms.assetid: 0BC1306E-0713-4592-996E-7530CCF281E7
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: c8275811cb61aaa1a033ca414e9abd4d20ecc873
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435430"
---
# <a name="watchos-notifications-in-xamarin"></a>Xamarin 中的 watchOS 通知

如果包含的 iOS 应用支持通知，则该监视应用可以接收通知。 存在内置的通知处理，因此你无 *需* 添加下面所述的其他通知支持，但是，如果你想要自定义通知行为和外观，请继续阅读。

有关将通知支持添加到解决方案中的 iOS 应用的详细信息，请参阅 [IOS 通知](~/ios/platform/user-notifications/deprecated/index.md) 文档。

## <a name="creating-notification-controllers"></a>创建通知控制器

在情节提要通知控制器上，有一种特殊类型的 segue 触发它们。 将新的 **通知界面控制器** 拖动到情节提要时，它会自动连接到 segue：

![附加了 segue 的新通知接口控制器](notifications-images/notification-storyboard1.png)

选择通知 segue 时，可以编辑其属性：

![Segue 选择的通知](notifications-images/notification-storyboard2.png)

自定义控制器后，它可能类似于以下示例： WatchKitCatalog：

![通知属性](notifications-images/notifications-segue.png)

有两种类型的通知：

- 系统定义的**简短**无滚动静态视图。

- 你定义的**长时间**可滚动、可自定义的视图！ 可以指定更简单的静态版本和更复杂的动态版本。

### <a name="short-look-notification-controller"></a>简短通知控制器

简短的 UI 只包含应用图标、应用名称和通知标题字符串。

如果用户不忽略通知，系统会自动切换到提供详细信息的长期通知。

### <a name="long-look-notification-controller"></a>长时间通知控制器

操作系统决定是基于多种因素显示静态视图还是动态视图。 必须提供静态接口，还可以选择包括用于通知的动态接口。

#### <a name="static"></a>Static

静态视图应简单快捷并快速显示。

![静态视图](notifications-images/notification-static.png)

#### <a name="dynamic"></a>动态

动态视图可以显示更多的数据，并提供更多的交互性。

![动态视图](notifications-images/notification-dynamic.png)

## <a name="generating-notifications"></a>生成通知

通知可以来自远程服务器，也可以在 iOS 应用中本地生成。

有关如何生成本地通知的示例，请参阅 [IOS 通知演练](~/ios/platform/user-notifications/deprecated/local-notifications-in-ios-walkthrough.md) 。

本地通知必须 `AlertTitle` 设置为在 Apple Watch 上显示，该 `AlertTitle` 字符串显示在 "简略" 界面中。 `AlertTitle`和 `AlertBody` 都显示在 "通知" 列表中，并且 `AlertBody` 显示在 "长外观" 界面中。

此屏幕截图显示在 " `AlertTitle` 通知" 列表中显示的，并显示在 " `AlertBody` 长期" 界面中：

![此屏幕截图显示在 "通知" 列表中显示的 AlertTitle](notifications-images/watch-notificationslist-sml.png) ![显示在 "长外观" 界面中的 AlertBody](notifications-images/watch-notificationcontroller-sml.png)

## <a name="testing-notifications"></a>测试通知

 (只能在设备上正确测试本地和远程) 的通知，但是可以在 iOS 模拟器中使用 **json** 文件对其进行模拟。

### <a name="testing-on-apple-watch"></a>Apple Watch 测试

在 Apple Watch 上测试通知时，请记住 [Apple 的文档](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/BasicSupport.html) 会指出以下内容：

> 当某个应用的本地或远程通知到达用户的 iPhone 时，iOS 将决定是在 iPhone 上还是在 Apple Watch 上显示该通知。

这 alluding 了 iOS 决定是在 iPhone 上还是在手表上显示通知。 如果在收到通知时，配对的 iPhone 处于活动状态，则可能会在 iPhone 上显示通知，而 *不* 会将其路由到手表。

若要确保通知显示在手表上，请关闭 iPhone 屏幕 (按下电源按钮一次) 或让其进入睡眠状态。 如果配对监视处于范围内，具有电源且在手腕上磨损，通知将在那里路由，并显示在 "监视" )  (上。

### <a name="testing-on-the-ios-simulator"></a>在 iOS 模拟器上进行测试

在 iOS 模拟器中测试通知模式时， *必须* 提供测试 JSON 有效负载。 在 Visual Studio for Mac 的 " **自定义执行参数** " 窗口中设置路径。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

当监视扩展设置为 **启动项目**时，Visual Studio for Mac 将显示其他选项。
右键单击 "监视扩展" 项目，然后选择 " **运行 > 自定义参数 ...**"：

[![用自定义属性运行](notifications-images/runwith-customparams-sml.png)](notifications-images/runwith-customparams.png#lightbox)

这会打开 " **执行参数** " 窗口，其中包含 " **WatchKit** " 选项卡。选择 " **通知** " 并提供 JSON 负载，然后按 " **执行** " 在模拟器中启动 watch 应用：

[![选择通知有效负载默认值](notifications-images/runwith-execargs-sml.png)](notifications-images/runwith-execargs.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

若要在 Visual Studio 中设置测试通知负载，请右键单击监视扩展以编辑 **项目属性**。 中转到 " **调试** " 部分并从列表中选择一个通知 JSON 文件 (它会自动列出项目) 中包含的所有 JSON 文件。

[![选择通知 JSON 文件](notifications-images/runwith-execargs-sml-vs.png)](notifications-images/runwith-execargs-vs.png#lightbox)

当监视扩展为 **启动项目**时，Visual Studio 将显示其他选项，如下所示。 选择其中一个 **通知** 选项，使用 "属性" 窗口中选择的 JSON 文件 (在 **通知** 模式下启动监视应用) ：

!["设备" 菜单](notifications-images/runwith-vs.png)

-----

在模拟器上用默认负载 JSON 文件进行测试时，默认通知控制器如下所示：

![示例通知](notifications-images/notification-debug-sml.png)

还可以使用 [命令行](~/ios/watchos/troubleshooting.md#command_line) 启动 iOS 模拟器。

### <a name="example-notification-payload"></a>示例通知负载

在 " [监视工具包目录](/samples/xamarin/ios-samples/watchos-watchkitcatalog) " 示例中，有一个示例负载 JSON 文件 **NotificationPayload.js** (下面列出) 。

```json
{
    "aps": {
        "alert": "Test message content",
        "title": "Optional title",
        "category": "myCategory"
        },

        "WatchKit Simulator Actions": [
        {
            "title": "First Button",
            "identifier": "firstButtonAction"
        }
        ],

        "customKey": "Use this file to define a testing payload for your notifications. The aps dictionary specifies the category, alert text and title. The WatchKit Simulator Actions array can provide info for one or more action buttons in addition to the standard Dismiss button. Any other top level keys are custom payload. If you have multiple such JSON files in your project, you'll be able to choose between them in when selecting to debug the notification interface of your Watch App."
    }
```

## <a name="related-links"></a>相关链接

- [WatchKitCatalog (示例) ](/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Apple 的手表套件通知文档](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/BasicSupport.html)