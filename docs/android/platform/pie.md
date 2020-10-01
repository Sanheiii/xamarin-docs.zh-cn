---
title: Android 9 Pie
description: 如何开始使用 Xamarin.Android 开发适用于 Android 9 Pie 的应用。
ms.prod: xamarin
ms.assetid: 6575DD32-9DC8-44E6-85EF-1F8BD07D3780
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/21/2018
ms.openlocfilehash: 6f192e9171a9da2e2ef7e0478b5bb6838676faee
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453436"
---
# <a name="android-pie-features"></a>Android Pie 功能

如何开始使用 Xamarin.Android 开发适用于 Android 9 Pie 的应用。 

Google 现已提供 [Android 9 Pie](https://developer.android.com/about/versions/pie/)。 此版本提供了许多新功能和 API，在最新 Android 设备中利用新硬件功能时，必须使用这些功能和 API。

![Android Pie 主图](pie-images/01-android-p-logo.png)

本文旨在帮助你开始开发适用于 Android Pie 的 Xamarin.Android 应用。 它介绍如何安装必要的更新、如何配置 SDK，以及如何准备用于测试的仿真器或设备。 它还概要介绍了 Android Pie 中的新增功能，并提供了演示如何使用 Android Pie 的一些关键功能的示例源代码。

Xamarin.Android 9.0 为 Android Pie 提供支持。 若要详细了解对 Android Pie 的 Xamarin.Android 支持，请参阅 [Android P 开发人员预览版 3](/xamarin/android/release-notes/9/9.0/#android-p-dp1) 发行说明。

## <a name="requirements"></a>要求

若要在基于 Xamarin 的应用中使用 Android Pie 功能，需具备以下组件：

- **Visual Studio** &ndash; 建议使用 Visual Studio 2019。
    如果你使用的是 Visual Studio 2017，请在 Windows 上更新为 Visual Studio 2017 版本 15.8 或更高版本。 在 macOS 上，更新为 Visual Studio 2017 for Mac 版本 7.6 或更高版本。

- **Xamarin.Android** &ndash; 必须随 Visual Studio 一起安装 Xamarin.Android 9.0.0.17 或更高版本（Xamarin.Android 将作为使用 .NET 的移动开发  工作负载的一部分自动安装）。

- **Java 开发人员工具包** &ndash; Xamarin Android 9.0 开发需要 [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)（或者可以尝试使用 Microsoft [OpenJDK](~/android/get-started/installation/openjdk.md) 发行版的预览版本）。 JDK8 将作为使用 .NET 的移动开发  工作负载的一部分自动安装。

- **Android SDK** &ndash; 必须通过 Android SDK 管理器安装 Android SDK API 28 或更高版本。

## <a name="getting-started"></a>入门

若要开始使用 Xamarin.Android 开发 Android Pie 应用，必须先下载并安装最新的工具和 SDK 包，然后才能开始创建 Android Pie 项目：

1. 建议使用 Visual Studio 2019。 如果你使用的是 Visual Studio 2017，请更新为 [Visual Studio 2017 版本 15.8](/visualstudio/releasenotes/vs2017-relnotes) 或更高版本。 如果你使用的是 Visual Studio for Mac，请更新为 [Visual Studio 2017 for Mac 版本 7.6](/visualstudio/releasenotes/vs2017-relnotes) 或更高版本。

2. 通过 SDK 管理器安装 Android Pie (API 28)  包和工具。

3. 创建面向 Android 9.0  的新 Xamarin.Android 项目。

4. 配置仿真器或设备以测试 Android Pie 应用。

以下部分介绍其中的每个步骤：

### <a name="update-visual-studio"></a>更新 Visual Studio

要使用 Xamarin 生成 Android Pie 应用，建议使用 Visual Studio 2019。

如果你使用的是 Visual Studio 2017，请更新为 Visual Studio 2017 版本 15.8 或更高版本（有关说明，请参阅[将 Visual Studio 2017 更新为最新版本](/visualstudio/install/update-visual-studio)）。 在 macOS 上，更新为 Visual Studio 2017 for Mac 7.6 或更高版本（有关说明，请参阅[设置和安装 Visual Studio for Mac](/visualstudio/mac/installation)）。

### <a name="install-the-android-sdk"></a>安装 Android SDK

若要使用 Xamarin.Android 9.0 创建项目，必须首先使用 Android SDK 管理器安装适用于 Android Pie（API 级别 28）或更高版本的 SDK 平台  。

1. 启动 SDK 管理器。 在 Visual Studio 中，单击“工具”>“Android”>“Android SDK 管理器”  。 在 Visual Studio for Mac 中，单击“工具”>“SDK 管理器”  。

2. 在右下角单击齿轮图标，然后选择“存储库”>“Google (不受支持)”  ：

    [![将存储库设置为 Google](pie-images/vs/set-repo-sml.png)](pie-images/vs/set-repo.png#lightbox)

3. 安装 Android Pie SDK 包，这些包在“平台”选项卡中列为 Android SDK 平台 28（有关使用 SDK 管理器的详细信息，请参阅 [Android SDK 设置](~/android/get-started/installation/android-sdk.md)）    ：

    [![安装 Android Pie 包](pie-images/vs/sdk-manager-sml.png)](pie-images/vs/sdk-manager.png#lightbox)

4. 如果使用的是仿真器，请创建一台支持 API 级别 28  的虚拟设备。 有关创建虚拟设备的详细信息，请参阅[使用 Android 设备管理器管理虚拟设备](~/android/get-started/installation/android-emulator/device-manager.md)。

### <a name="start-a-xamarinandroid-project"></a>启动 Xamarin.Android 项目

创建新的 Xamarin.Android 项目。 如果你不熟悉如何使用 Xamarin 开发 Android，请参阅 [Hello, Android](~/android/get-started/hello-android/index.md) 了解如何创建 Xamarin.Android 项目。

在创建 Android 项目时，必须将版本设置配置为面向 Android 9.0 或更高版本。 例如，若要使项目面向 Android Pie，必须将项目的目标 Android API 级别配置为“Android 9.0 (API 28)”  。 建议同时将目标框架级别设置为 API 28 或更高级别。 有关配置 Android API 级别的详细信息，请参阅[了解 Android API 级别](~/android/app-fundamentals/android-api-levels.md)。

### <a name="configure-a-device-or-emulator"></a>配置设备或仿真器

如果使用的是 Nexus 或 Pixel 等物理设备，可按照[用于 Nexus 和 Pixel 设备的出厂映像](https://developers.google.com/android/images)中的说明将设备更新为 Android Pie。

如果使用的是仿真器，请为 API 级别 28 创建虚拟设备，并选择基于 x86 的映像。 有关使用 Android 设备管理器创建和管理虚拟设备的信息，请参阅[使用 Android 设备管理器管理虚拟设备](~/android/get-started/installation/android-emulator/device-manager.md)。
有关使用 Android 仿真器进行测试和调试的信息，请参阅 [Android 仿真器调试](~/android/deploy-test/debugging/debug-on-emulator.md)。

## <a name="new-features"></a>新增功能

Android Pie 引入了多种新功能。 其中一些新功能旨在利用最新 Android 设备提供的新硬件功能，而其他一些功能旨在进一步改善 Android 用户体验：

- **支持刘海屏** &ndash; 提供的 API 可以在较新 Android 设备上找到屏幕顶部刘海区域  的位置和形状。

- **通知增强功能** &ndash; 通知消息现在可以显示图像，并使用新的 `Person` 类简化了会话参与者。

- **室内定位** &ndash; 对 WiFi 往返时间协议提供平台支持，这使得应用可以使用 WiFi 设备在室内设置中进行导航。

- **支持多摄像头** &ndash; 提供从多个物理摄像头（如双前置摄像头和双后置摄像头）同时访问流的功能。

以下各节将重点介绍这些功能，并提供简短代码示例，帮助你开始在应用中使用这些功能。

### <a name="display-cutout-support"></a>支持刘海屏

许多具有全屏幕的较新 Android 设备都有一个“刘海屏”（或“凹槽”）  ，在显示屏顶部为摄像头和扬声器留出空间。
下面的屏幕截图提供了刘海的一个仿真器示例：

[![用于模拟刘海的 Android 仿真器](pie-images/02-example-cutout-sml.png)](pie-images/02-example-cutout.png#lightbox)

为了管理应用窗口在带有刘海屏的设备上显示其内容的方式，Android Pie 已添加了一个新的 [LayoutInDisplayCutoutMode](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#layoutInDisplayCutoutMode) 窗口布局属性。 可以将此属性设置为以下值之一：

- [LayoutInDisplayCutoutModeNever](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_NEVER) &ndash; 永远不允许窗口与刘海区域重叠。

- [LayoutInDisplayCutoutModeShortEdges](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_SHORT_EDGES) &ndash; 允许窗口延伸到刘海区域，但只能在屏幕的短边缘上。 

- [LayoutInDisplayCutoutModeDefault](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_DEFAULT) &ndash; 如果系统栏内包含刘海，则允许窗口延伸到刘海区域。

例如，若要防止应用窗口与刘海区域重叠，请将布局刘海模式设置为“从不”  ： 

```csharp
Window.Attributes.LayoutInDisplayCutoutMode =
    Android.Views.LayoutInDisplayCutoutMode.Never;
```

下面提供了这些刘海模式的示例。 左侧的第一个屏幕截图是处于非全屏模式的应用。 在中间位置的屏幕截图中，通过将 `LayoutInDisplayCutoutMode` 设置为 `LayoutInDisplayCutoutModeShortEdges`，使应用进入全屏模式。 请注意，应用的白色背景延伸到了刘海屏区域中：

[![仿真器中的刘海屏模式示例](pie-images/03-cutout-modes-sml.png)](pie-images/03-cutout-modes.png#lightbox)

在最后一个屏幕截图（上面的右侧屏幕截图）中，将 `LayoutInDisplayCutoutMode` 设置为 `LayoutInDisplayCutoutModeShortNever` 后，进入全屏模式。
请注意，不允许应用的白色背景延伸到刘海屏区域中。

如需详细了解设备上的刘海区域，可以使用新的 [DisplayCutout](https://developer.android.com/reference/android/view/DisplayCutout.html) 类。 `DisplayCutout` 表示不能用于显示内容的显示屏区域。 可以使用此信息来检索刘海的位置和形状，从而使应用不会尝试在此非功能区域中显示内容。

有关 Android P 中的新刘海功能的详细信息，请参阅[支持刘海屏](https://developer.android.com/about/versions/pie/android-9.0#cutout)。

### <a name="notifications-enhancements"></a>通知增强功能

Android Pie 引入了以下增强功能来改善消息传递体验：

- 通知通道（在 [Android Oreo](~/android/platform/oreo.md) 中引入）现在支持阻止频道组。

- 通知系统包含三个新的“请勿打扰”类别（优先设置警报、系统声音和媒体源）。 此外，还有七种新的“请勿打扰”模式可用于阻止视觉中断（例如徽章、通知灯、状态栏外观和启动全屏活动）。

- 添加了一个新的 [Person](https://developer.android.com/reference/android/app/Person.html) 类，用来表示消息的发送方。 使用此类后，可以通过标识会话中涉及的人员（包括其头像和 URI）来优化每个通知的呈现。

- 通知现在可以显示图像。 

下面的示例演示如何使用新 API 生成可包含图像的通知。 在下面的屏幕截图中，先发布文本通知，然后发布包含嵌入图像的通知。 展开通知内容时（如右图所示），将显示第一个通知的文本，同时放大第二个通知中嵌入的图像：

[![带图像的通知示例](pie-images/04-example-notifications-sml.png)](pie-images/04-example-notifications.png#lightbox)

下面的示例说明如何在 Android Pie 通知中包含图像，并演示如何使用新的 `Person` 类：

1. 创建一个代表发送方的 `Person` 对象。 例如，发送方的名称和图标包含在 `fromPerson` 中：

    ```csharp
    Icon senderIcon = Icon.CreateWithResource(this, Resource.Drawable.sender_icon);
    Person fromPerson = new Person.Builder()
        .SetIcon(senderIcon)
        .SetName("Mark Sender")
        .Build();
    ```

2. 创建一个包含要发送的图像的 `Notification.MessagingStyle.Message`，用于将该图像传递到新的 [Notification.MessagingStyle.Message.SetData](https://developer.android.com/reference/android/app/Notification.MessagingStyle.Message.html#setData%28java.lang.String,%20android.net.Uri) 方法。
   例如：

    ```csharp
    Uri imageUri = Uri.Parse("android.resource://com.xamarin.pminidemo/drawable/example_image");
    Notification.MessagingStyle.Message message = new Notification.MessagingStyle
            .Message("Here's a picture of where I'm currently standing", 0, fromPerson)
            .SetData("image/", imageUri);
    ```

3. 将消息添加到 `Notification.MessagingStyle` 对象。 例如：

    ```csharp
    Notification.MessagingStyle style = new Notification.MessagingStyle(fromPerson)
            .AddMessage(message);
    ```

4. 将此样式插入通知生成器。 例如：

    ```csharp
    builder = new Notification.Builder(this, MY_CHANNEL)
        .SetContentTitle("Tour of the Colosseum")
        .SetContentText("I'm standing right here!")
        .SetSmallIcon(Resource.Mipmap.ic_notification)
        .SetStyle(style)
        .SetChannelId(MY_CHANNEL);
    ```

5. 发布通知。 例如：

    ```csharp
    const int notificationId = 1000;
    notificationManager.Notify(notificationId, builder.Build());
    ```

有关创建通知的详细信息，请参阅[本地通知](~/android/app-fundamentals/notifications/local-notifications.md)。

### <a name="indoor-positioning"></a>室内定位

Android Pie 支持 IEEE 802.11mc（也称为 WiFi 往返时间或 WiFi RTT   ），这使应用可以检测到与一个或多个 Wi-Fi 接入点的距离。 使用此信息，应用可以利用室内定位功能将距离精确到 1-2 米  。 在为 IEEE 801.11mc 提供硬件支持的 Android 设备上，你的应用可以提供导航功能，如智能设备基于位置的控制，或者穿过商店时获得的逐向导航指令：

[![使用 WiFi RTT 的室内导航示例](pie-images/05-wifi-rtt-sml.png)](pie-images/05-wifi-rtt.png#lightbox)

新 [WifiRttManager](https://developer.android.com/reference/android/net/wifi/rtt/WifiRttManager) 类和几个帮助程序类提供了测量到 Wi-Fi 设备的距离的方法。 有关 Android P 中引入的室内定位 API 的详细信息，请参阅 [Android.Net.Wifi.Rtt](https://developer.android.com/reference/android/net/wifi/rtt/package-summary)。

### <a name="multi-camera-support"></a>支持多摄像头

许多较新的 Android 设备都具有双前置和/或双后置摄像头，可用于诸如立体视觉、增强的视觉效果和改进的缩放功能等功能。 Android P 引入了新的[多摄像头](https://developer.android.com/about/versions/pie/android-9.0#camera) API，使应用可以使用由两个或更多个物理摄像头支持的逻辑摄像头  （或*逻辑多摄像头*）。
要确定设备是否支持逻辑多摄像头，可以查看设备上每个摄像头的功能，看看是否支持 [RequestAvailableCapabilitiesLogicalMultiCamera](https://developer.android.com/reference/android/hardware/camera2/CameraMetadata#REQUEST_AVAILABLE_CAPABILITIES_LOGICAL_MULTI_CAMERA)。

Android Pie 还包括一个新的 [SessionConfiguration](https://developer.android.com/reference/android/hardware/camera2/params/SessionConfiguration.html) 类，有助于减少初始捕获过程中的延迟，无需启动摄像头流。

有关 Android P 中的多摄像头支持的详细信息，请参阅[多摄像头支持和摄像头更新](https://developer.android.com/about/versions/pie/android-9.0#camera)。

### <a name="other-features"></a>其他功能

除了上述新增功能之外，Android Pie 还支持其他几项新功能：

- 新 [AnimatedImageDrawable](https://developer.android.com/reference/android/graphics/drawable/AnimatedImageDrawable.html) 类，可用于绘制和显示动画图像。

- 新的 [ImageDecoder](https://developer.android.com/reference/android/graphics/ImageDecoder.html) 类，将取代 `BitmapFactory`。 `ImageDecoder` 可用于解码 `AnimatedImageDrawable`。

- 支持 HDR（高动态范围）视频和 HEIF（高效率图像文件格式）图像。

- [JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html) 已得到增强，可以更智能地处理与网络相关的作业。 [JobParameters](https://developer.android.com/reference/android/app/job/JobParameters) 类的新 [GetNetwork](https://developer.android.com/reference/android/app/job/JobParameters#getNetwork%28%29) 方法返回最佳网络，可以执行给定作业的任何网络请求。

有关最新 Android Pie 功能的详细信息，请参阅 [Android 9 功能和 API](https://developer.android.com/about/versions/pie/android-9.0)。

## <a name="behavior-changes"></a>行为变更

当目标 Android 版本设置为 API 级别 28时，即使未实现上述新功能，也会有一些平台更改会影响应用的行为。 下面的列表概要介绍了这些更改：

- 应用现在必须先请求前台权限，然后才能使用前台服务。

- 如果应用有多个进程，则不能跨进程共享单个 [WebView](xref:Android.Webkit.WebView) 数据目录。

- 不再允许按路径直接访问另一个应用的数据目录。

若要详细了解面向 Android P 的应用的行为更改，请参阅[行为更改](https://developer.android.com/about/versions/pie/android-9.0-changes-all#p-apps)。

## <a name="sample-code"></a>示例代码

[AndroidPMiniDemo](https://github.com/xamarin/monodroid-samples/tree/master/android-p/AndroidPMiniDemo) 是适用于 Android Pie 的 Xamarin.Android 示例应用，它演示了如何设置刘海屏模式，如何使用新的 `Person` 类，以及如何发送包含图像的通知。

## <a name="summary"></a>总结

本文介绍了 Android Pie，并说明了如何安装和配置最新的工具和包，以便进行 Android Pie 的 Xamarin.Android 开发。 它概述了 Android Pie 提供的关键功能，并提供了其中一些功能的示例源代码。
它提供了指向 API 文档和 Android 开发人员主题的链接，帮助你开始为 Android Pie 创建应用。 它还重点介绍了可能影响现有应用的最重要的 Android Pie 行为更改。

## <a name="related-links"></a>相关链接

- [Android 9 Pie](https://developer.android.com/about/versions/pie/)