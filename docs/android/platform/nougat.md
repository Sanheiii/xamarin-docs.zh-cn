---
title: Nougat 功能
description: 如何开始使用 Xamarin.Android 开发 Android Nougat 应用。
ms.prod: xamarin
ms.assetid: 5C74ABE2-C862-4ED0-8EA5-C7FEE5251D4B
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/02/2018
ms.openlocfilehash: 135303743cce1f23bb1d70731f6e760188df11e2
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457206"
---
# <a name="nougat-features"></a>Nougat 功能

_如何开始使用 Xamarin.Android 开发 Android Nougat 应用。_

本文概述了 Android Nougat 中引入的功能，介绍如何准备用于 Android Nougat 开发的 Xamarin.Android，并提供指向示例应用程序的链接，这些示例应用演示了如何在 Xamarin.Android 应用中使用 Android Nougat 功能。

## <a name="overview"></a>概述

[Android Nougat](https://developer.android.com/about/versions/nougat/android-7.0.html) 是 Google 对 Android 6.0 Marshmallow 推出的后续产品。 Xamarin.Android 在 Xamarin Android 7.0 及更高版本中支持 Android 7.x 绑定  。 Android Nougat 为如下所述的 Nougat 功能添加了许多新 API；使用 Xamarin.Android 7.0 时，这些 API 可供 Xamarin.Android 应用使用。

[![运行 Android Nougat 的 Android 平板电脑和手机的 Hero 映像](nougat-images/android-n-hero-sml.png)](nougat-images/android-n-hero.png#lightbox)

有关 Android 7.x API 的详细信息，请参阅[适用于开发人员的 Android 7.1](https://developer.android.com/preview/api-overview.html)。
有关已知的 Xamarin.Android 7.0 问题的列表，请参阅[发行说明](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_7/xamarin.android_7.0/index.md)。

Android Nougat 为 Xamarin.Android 开发人员提供了许多新功能。 这些功能包括：

- 多窗口支持  &ndash; 此增强功能使用户能够在屏幕上同时打开两个应用。

- 通知增强功能  &ndash; Android Nougat 中重新设计的通知系统包括“直接答复”  功能，该功能允许用户直接从通知 UI 中快速回复短信。 此外，如果应用程序为收到的消息创建通知，则新的“绑定通知”  功能可以在收到多个消息时，将通知作为单个组捆绑在一起。

- 数据保护程序  &ndash; 此功能是一种新的系统服务，可帮助减少应用程序使用的手机网络数据；它使用户能够控制应用使用手机网络数据的方式。

此外，Android Nougat 还为应用开发人员提供了许多其他有趣的增强功能，如新的网络安全配置功能、Doze on the Go、密钥证明、新快速设置 API、多区域设置支持、ICU4J API、Web 视图增强功能、对 Java 8 语言功能的访问、作用域内的目录访问、自定义指针 API、平台 VR 支持，虚拟文件以及后台处理优化。

本文介绍了如何开始使用 Android Nougat 生成应用程序以试用新功能，并计划迁移或功能工作以面向新的 Android Nougat 平台。

## <a name="requirements"></a>要求

若要在基于 Xamarin 的应用中使用新的 Android Nougat 功能，需满足以下要求：

- Visual Studio 或 Visual Studio for Mac  &ndash; 如果你使用的是 Visual Studio，则需要适用于 Xamarin 的 Visual Studio Tools 的版本 4.2.0.628 或更高版本。 如果使用的是 Visual Studio for Mac，则需要 Visual Studio for Mac 版本 6.1.0 或更高版本。

- Xamarin.Android  &ndash; 必须安装 Xamarin.Android 7.0 或更高版本并配置 Visual Studio 或 Visual Studio for Mac。

- Android SDK  - 必须通过 Android SDK 管理器安装 Android SDK 7.0 (API 24) 或更高版本。

- **Java 开发人员工具包** &ndash; 如果开发 API 级别 24 或更高级别的 Xamarin Android 7.0，则需要 [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)（JDK 8 还支持低于 24 的 API 级别）。 如果使用的是自定义控件或窗体预览器，则需要使用 64 位版本的 JDK 8。

> [!IMPORTANT]
> Xamarin.Android 不支持 JDK 9。

请注意，必须使用 Xamarin C6SR4 或更高版本重新生成应用，才能通过 Android Nougat 可靠地工作。 由于 Android Nougat 只能链接到 [NDK 提供的本机库](https://developer.android.com/about/versions/nougat/android-7.0-changes.html)，因此使用库的现有应用（如 Mono.Data.Sqlite.dll  ）在 Android Nougat 上运行时，如果未正确重新生成，则可能会崩溃。

## <a name="getting-started"></a>入门

若要开始结合使用 Android Nougat 和 Xamarin.Android，必须先下载并安装最新的工具和 SDK 包，然后才能创建 Android Nougat 项目：

1. 从 Xamarin 安装最新的 Xamarin.Android 更新。

2. 安装 Android 7.0 (API 24)  包和工具或更高版本。

3. 创建面向 Android Nougat 的新 Xamarin.Android 项目。

4. 为 Android Nougat 配置仿真器或设备。

以下部分介绍其中的每个步骤：

### <a name="install-xamarin-updates"></a>安装 Xamarin 更新

若要为 Android Nougat 添加 Xamarin 支持，请将 Visual Studio 或 Visual Studio for Mac 中的更新通道更改为稳定通道，并应用最新更新。 如果还需要目前仅在 Alpha 或 Beta 通道中提供的功能，可以切换到 Alpha 或 Beta 通道（Alpha 和 Beta 通道还支持 Android 7.x）。 有关如何更改更新（发行）通道的信息，请参阅[更改更新通道](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/change_updates_channel)。

### <a name="install-the-android-sdk"></a>安装 Android SDK

若要使用 Xamarin Android 7.0 创建项目，必须首先使用 Android SDK 管理器安装 SDK 平台 Android N (API 24)  或更高版本。 还必须安装最新的 Android SDK Tools  ：

1. 启动 Android SDK 管理器（在 Visual Studio for Mac 中，使用“工具”>“打开 Android SDK 管理器” **&hellip;** ；在 Visual Studio 中，使用“工具”>Android”>“Android SDK 管理器”  ）。

2. 安装 Android 7.0 (API 24)  或更高版本：

    [![在 Android SDK 管理器中选择 Android 7.0 包](nougat-images/preview-packages.png)](nougat-images/preview-packages.png#lightbox)

3. 安装最新的 Android SDK 工具：

    [![在 Android SDK 管理器中选择最新的 Android SDK 工具](nougat-images/preview-tools.png)](nougat-images/preview-tools.png#lightbox)

    必须安装 Android SDK Tools 25.2.2 或更高版本、Android SDK 平台工具 24.0.3 或更高版本，以及 Android SDK 生成工具 24.0.2 或更高版本。

4. 验证是否为 JDK 1.8 配置了“Java 开发工具包位置”  ：

    [![在“工具”选项下配置 JDK 8 路径](nougat-images/use-jdk-1.8.png)](nougat-images/use-jdk-1.8.png#lightbox)

    若要在 Visual Studio 中查看此设置，请单击“工具”>“选项”>“Xamarin”>“Android 设置”  。 在 Visual Studio for Mac 中，单击“首选项”>“项目”>“SDK 位置”>“Android”  。

### <a name="start-a-xamarinandroid-project"></a>启动 Xamarin.Android 项目

创建新的 Xamarin.Android 项目。 如果你不熟悉如何使用 Xamarin 开发 Android，请参阅 [Hello, Android](~/android/get-started/hello-android/index.md) 了解如何创建 Xamarin.Android 项目。

创建 Android 项目时，必须将版本设置配置为面向 Android 7.0 或更高版本。 例如，若要将项目目标指向 Android 7.0，必须将项目的 Android API 级别目标配置为“Android 7.0 (API 24 - Nougat)”  。 建议将目标框架级别设置为 API 24 或更高级别。 有关配置 Android API 级别的详细信息，请参阅[了解 Android API 级别](~/android/app-fundamentals/android-api-levels.md)。

> [!NOTE]
> 目前，必须将“最低 Android 版本”  设置为“Android 7.0 (API 24 - Nougat)  ，以将应用部署到 Android Nougat 设备或仿真器。

### <a name="configure-an-emulator-or-device"></a>配置仿真器或设备

如果使用的是仿真器，请启动 Android AVD 管理器，并使用以下设置创建新设备：

- 设备:Nexus 5X、Nexus 6、Nexus 6P、Nexus Player、Nexus 9 或 Pixel C。
- 目标：Android 7.0 - API 级别 24
- ABI：x86 或 x86\_64

例如，此虚拟设备配置为模拟 Nexus 6：

[![使用 Nexus 6 设备、Android 7.0 目标和 Intel Atom x86 CPU/ABI 配置 AVD](nougat-images/android-n-avd.png)](nougat-images/android-n-avd.png#lightbox)

如果使用的是物理设备（例如，Nexus 5X、6 或 9），则可以通过自动无线 (OTA) 更新来更新设备，或下载系统映像并直接刷新设备。 有关手动将设备更新到 Android Nougat 的详细信息，请参阅 [Nexus 设备的 OTA 映像](https://developers.google.com/android/nexus/ota)。

请注意，Android Nougat 不支持 Nexus 5 设备。

## <a name="new-features"></a>新增功能

Android Nougat 引入了多种新特性和功能，如多窗口支持、通知增强功能和数据保护程序。 以下各节重点介绍这些功能，并提供可帮助你在应用中开始使用这些功能的链接。

### <a name="multi-window-mode"></a>多窗口模式

多窗口模式使用户能够一次打开两个应用，并提供完整的多任务支持。 这些应用可在拆分屏幕模式下并行运行（横向）或一上一下运行（纵向）。
用户可以在应用之间拖动分隔线以调整其大小，并且可以在应用之间剪切和粘贴内容。 当在多窗口模式下显示两个应用时，所选活动将继续运行，同时未选定的活动将暂停，但仍可见。 多窗口模式不会修改 Android 活动的生命周期。

[![以纵向和横向形式在多窗口模式下运行的示例应用](nougat-images/multi-window-mode.png)](nougat-images/multi-window-mode.png#lightbox)

可以配置 Xamarin.Android 应用程序的活动如何支持多窗口模式。 例如，可以配置属性，以便在多窗口模式下设置应用的最小大小以及默认高度和宽度。 可以使用新的 `Activity.IsInMultiWindowMode` 属性来确定活动是否处于多窗口模式下。 例如：

```csharp
if (!IsInMultiWindowMode) {
    multiDisabledMessage.Visibility = ViewStates.Visible;
} else {
    multiDisabledMessage.Visibility = ViewStates.Gone;
}
```

[MultiWindowPlayground](/samples/xamarin/monodroid-samples/android-n-multiwindowplayground) 示例应用包含 C# 代码，演示如何在应用中利用多窗口用户界面。

有关多窗口模式的详细信息，请参阅[多窗口支持](https://developer.android.com/guide/topics/ui/multi-window.html)。

### <a name="enhanced-notifications"></a>增强型通知

Android Nougat 引入了重新设计的通知系统。 它具有新的“直接答复”功能，使用户能够在通知 UI 中直接回复传入的短信通知。 从 Android 7.0 开始，接收多个消息时，通知消息可以作为单个组捆绑在一起。 此外，开发人员还可以自定义通知视图、利用通知中的系统修饰，并在生成通知时利用新的通知模板。

#### <a name="direct-reply"></a>直接答复

当用户收到传入消息通知时，Android Nougat 可以在通知中回复消息（而不是打开消息应用发送回复）。
利用此内联答复功能，用户可以直接在通知界面中快速回复短信或文本消息：

[![带有内联“直接答复”字段的通知屏幕截图](nougat-images/notifications-inline-reply-sml.png)](nougat-images/notifications-inline-reply.png#lightbox)

若要在应用中支持此功能，必须通过 [RemoteInput](xref:Android.App.RemoteInput) 对象将“内联答复操作”  添加到应用，以便用户可以直接从通知 UI 中通过文本进行回复。
例如，下面的代码生成用于接收文本输入的 `RemoteInput`，为答复操作生成挂起意向，并创建启用了远程输入的操作：

```csharp
// Build a RemoteInput for receiving text input:
var remoteInput = new Android.Support.V4.App.RemoteInput.Builder (EXTRA_REMOTE_REPLY)
    .SetLabel (GetString (Resource.String.reply))
    .Build ();

// Build a Pending Intent for the reply action to trigger:
PendingIntent replyIntent = PendingIntent.GetBroadcast (ApplicationContext,
                                conversation.ConversationId,
                                GetMessageReplyIntent (conversation.ConversationId),
                                PendingIntentFlags.UpdateCurrent);

// Build an Android 7.0 compatible Remote Input enabled action:
NotificationCompat.Action actionReplyByRemoteInput = new NotificationCompat.Action.Builder (
    Resource.Drawable.notification_icon,
    GetString (Resource.String.reply),
    replyIntent).AddRemoteInput (remoteInput).Build ();
```

此操作已添加到通知：

```csharp
// Create the notification:
NotificationCompat.Builder builder = new NotificationCompat.Builder (ApplicationContext)
   .SetSmallIcon (Resource.Drawable.notification_icon)
   ...
   .AddAction (actionReplyByRemoteInput);
```

[消息服务](/samples/xamarin/monodroid-samples/android-n-messagingservice)示例应用包含 C# 代码，演示如何使用 `RemoteInput` 对象扩展通知。 有关向适用于 Android 7.0 或更高版本的应用添加内联答复操作的详细信息，请参阅 Android [回复通知](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#direct)主题。

#### <a name="bundled-notifications"></a>绑定通知

Android Nougat 可以将通知消息组合在一起（例如，按消息主题），并显示组而不是每个单独的消息。
此“绑定通知”  功能使用户可以在一个操作中消除或存档一组通知。 用户可以向下滑动来展开绑定通知，以详细查看每个通知：

[![绑定通知示例屏幕截图](nougat-images/bundled-notifications-sml.png)](nougat-images/bundled-notifications.png#lightbox)

为了支持绑定通知，应用程序可以使用 [Builder.SetGroup](xref:Android.App.Notification.Builder.SetGroup*) 方法来捆绑类似通知。 有关 Android N 中的绑定通知组的详细信息，请参阅 Android [绑定通知](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#bundle)主题。

#### <a name="custom-views"></a>自定义视图

Android Nougat 使你可以通过系统通知标头、操作和可展开的布局创建自定义通知视图。 有关 Android Nougat 中的自定义通知视图的详细信息，请参阅 Android [通知增强功能](https://developer.android.com/about/versions/nougat/android-7.0.html#notification_enhancements)主题。

### <a name="data-saver"></a>数据保护程序

从 Android Nougat 开始，用户可以启用新“数据保护程序”  设置来阻止后台数据使用。 此设置还会让应用尽可能少地使用前台数据。 [ConnectivityManager](xref:Android.Net.ConnectivityManager) 已在 Android Nougat 中扩展，以便应用可以检查用户是否已启用“数据保护程序”，这样在启用“数据保护程序”时应用可以限制其数据使用量。

有关 Android Nougat 中的新“数据保护程序”功能的详细信息，请参阅 Android [优化网络数据使用](https://developer.android.com/training/basics/network-ops/data-saver.html)主题。

### <a name="app-shortcuts"></a>应用快捷方式

Android 7.1 引入了“应用快捷方式”  功能，使用户可以使用应用快速启动常见或推荐的任务。
若要激活快捷菜单，用户可长按应用图标持续一秒或更长时间 &ndash; 菜单将快速出现。
松开按钮后，菜单仍保留：

[![用于消息应用的应用快捷菜单的示例屏幕](nougat-images/app-shortcuts-sml.png)](nougat-images/app-shortcuts.png#lightbox)

此功能仅适用于 API 级别 25 或更高级别。
有关 Android 7.1 中新应用快捷方式功能的详细信息，请参阅 Android [应用快捷方式](https://developer.android.com/guide/topics/ui/shortcuts.html)主题。

### <a name="sample-code"></a>代码示例

提供了多个 Xamarin.Android 示例，用于演示如何利用 Android Nougat 功能：

- [MultiWindowPlayground](/samples/xamarin/monodroid-samples/android-n-multiwindowplayground) 演示如何使用 Android Nougat 中提供的多窗口 API。 可以将示例应用切换为多窗口模式，看看它如何影响应用的生命周期和行为。

- [消息服务](/samples/xamarin/monodroid-samples/android-n-messagingservice)是使用 `NotificationCompatManager` 发送通知的简单服务。 它还使用 `RemoteInput` 对象扩展通知，以便 Android Nougat 设备无需打开应用即可直接从通知中通过文本进行答复。

- [活动通知](/samples/xamarin/monodroid-samples/android-n-activenotifications)演示如何使用 `NotificationManager` API 来告诉你应用程序当前显示的通知数量。

- [作用域内的目录访问](/samples/xamarin/monodroid-samples/android-n-scopeddirectoryaccess)演示如何使用作用域内的目录访问 API 来轻松访问特定目录。 这是必须在清单中定义 `READ_EXTERNAL_STORAGE` 或 `WRITE_EXTERNAL_STORAGE` 权限的替代方法。

- [直接启动](/samples/xamarin/monodroid-samples/android-n-directboot)说明如何将数据存储在设备加密的存储中，该存储始终可用，同时在输入任何用户凭据（PIN/模式/密码）前后启动设备。

## <a name="summary"></a>总结

本文介绍了 Android Nougat，并说明如何安装和配置最新的工具和包，以便在 Android Nougat 上进行 Xamarin.Android 开发。 它还概述了 Android Nougat 中提供的主要功能，并提供了指向示例源代码的链接，可帮助你开始创建 Android Nougat 应用。

## <a name="related-links"></a>相关链接

- [适用于开发人员的 Android 7.1](https://developer.android.com/about/versions/nougat/android-7.1.html)
- [Xamarin Android 7.0 发行说明](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_7/xamarin.android_7.0/index.md)