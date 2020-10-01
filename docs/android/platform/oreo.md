---
title: Oreo 功能
description: 如何开始使用 Xamarin.Android 开发适用于最新版 Android 的应用。
ms.prod: xamarin
ms.assetid: EAEF7341-7A00-4439-9FAF-43882637BEF8
ms.technology: xamarin-android
ms.custom: video
author: davidortinau
ms.author: daortin
ms.date: 07/06/2018
ms.openlocfilehash: 4c017f7b27d646155bb5c8e556955f495bde6be7
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457557"
---
# <a name="oreo-features"></a>Oreo 功能

如何开始使用 Xamarin.Android 开发适用于最新版 Android 的应用。  。

[Android 8.0 Oreo](https://developer.android.com/index.html) 是 Google 发布的最新版 Android。 Android Oreo 提供了 Xamarin.Android 开发人员感兴趣的许多新功能。 这些功能包括通知通道、通知锁屏提醒、XML 自定义字体、可下载字体、自动填充和画中画 (PIP)。 Android Oreo 包含用于这些新功能的新 API，如果使用 Xamarin.Android 8.0 及更高版本，可在 Xamarin.Android 应用中使用这些 API。

[![Android Oreo 主图图像](oreo-images/01-android-o-logo-sml.png)](oreo-images/01-android-o-logo.png#lightbox)

本文旨在帮助你开始开发适用于 Android 8.0 Oreo 的 Xamarin.Android 应用。 它介绍如何安装必要的更新、如何配置 SDK 以及如何创建用于测试的仿真器（或设备）。 还概述了 Android 8.0 Oreo 中的新功能，并提供了示例应用的链接，这些示例应用说明了如何在 Xamarin.Android 应用中使用 Android Oreo 功能。

## <a name="requirements"></a>要求

若要在基于 Xamarin 的应用中使用 Android Oreo 功能，需满足以下要求：

- **Visual Studio** &ndash; 如果使用的是 Windows，则需要 Visual Studio 版本 15.5 或更高版本。  如果使用的是 Mac，则需要 Visual Studio for Mac 版本 7.2.0。

- **Xamarin.Android** &ndash; 必须安装 Xamarin.Android 8.0 或更高版本并配置 Visual Studio。

- **Android SDK** &ndash; 必须通过 Android SDK 管理器安装 Android SDK 8.0 (API 26) 或更高版本。

## <a name="getting-started"></a>入门

若要开始结合使用 Android Oreo 和 Xamarin.Android，必须先下载并安装最新的工具和 SDK 包，然后才能创建 Android Oreo 项目：

1. 更新到 Visual Studio 的最新版本。

2. 通过 SDK 管理器安装 Android 8.0.0 (API 26) 或更高版本的包和工具  。

3. 创建面向 Android Oreo (API 26) 的新 Xamarin.Android 项目。

4. 配置仿真器或设备以测试 Android Oreo 应用。

以下各节具体介绍了每个步骤：

### <a name="update-visual-studio-and-xamarinandroid"></a>更新 Visual Studio 和 Xamarin.Android

若要向 Visual Studio 添加 Android Oreo 支持，请执行以下操作：

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

- 对于 Visual Studio 2019，请使用 [SDK 管理器](~/android/get-started/installation/android-sdk.md) 安装 API 级别 26.0 或更高版本。

- 如果你使用的是 Visual Studio 2017：

    1. 请更新到 Visual Studio 2017 版本 15.7 或更高版本（请参阅[更新 Visual Studio 2017](/visualstudio/install/update-visual-studio)）。

    2. 使用 [SDK 管理器](~/android/get-started/installation/android-sdk.md) 安装 API 级别 26.0 或更高版本。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

- 更新到 Visual Studio for Mac 的最新稳定版本，如[更新 Visual Studio for Mac](/visualstudio/mac/update) 中所述。

-----

有关适用于 Android Oreo 的 Xamarin 支持的详细信息，请参阅 [Xamarin.Android 8.0 发行说明](/xamarin/android/release-notes/8/8.0/)。

### <a name="install-the-android-sdk"></a>安装 Android SDK

若要使用 Xamarin.Android 8.0 创建项目，必须首先使用 Xamarin Android SDK 管理器安装适用于Android 8.0 (Oreo) 或更高版本的 SDK 平台  。 还必须安装 Android SDK Tools 26.0 或更高版本。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 启动 SDK 管理器（在 Visual Studio 中单击“工具”>“Android”>“Android SDK 管理器”  ）。

2. 安装 Android 8.0 (Oreo) 包  。 如果使用 Android SDK 仿真器，请确保包含所需的 x86 系统映像  ：

    [![在 Android SDK 管理器中选择 Android 8.0 包](oreo-images/win/01-android-o-packages.png)](oreo-images/win/01-android-o-packages.png#lightbox)

3. 安装 Android SDK Tools 26.0.2 或更高版本、Android SDK 平台工具 26.0.0 或更高版本以及 Android SDK 生成工具 26.0.0（或更高版本）    ：

    [![在 Android SDK 管理器中选择 Android SDK Tools 26](oreo-images/win/02-sdk-tools.png)](oreo-images/win/02-sdk-tools.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 启动 SDK 管理器（在 Visual Studio for Mac 中单击“工具”>“SDK 管理器”）  。

2. 安装 Android 8.0 (Oreo) SDK 包  。 如果使用 Android SDK 仿真器，请确保包含所需的 x86 系统映像  ：

    [![在 SDK 管理器中选择 Android 8.0 包](oreo-images/mac/01-android-o-packages.png)](oreo-images/mac/01-android-o-packages.png#lightbox)

3. 安装 Android SDK Tools 26.0.2 或更高版本、Android SDK 平台工具 26.0.0 或更高版本以及 Android SDK 生成工具 26.0.0（或更高版本）    ：

    [![在 SDK 管理器中选择 Android SDK Tools 26](oreo-images/mac/02-sdk-tools.png)](oreo-images/mac/02-sdk-tools.png#lightbox)

-----

### <a name="start-a-xamarinandroid-project"></a>启动 Xamarin.Android 项目

创建新的 Xamarin.Android 项目。 如果不熟悉通过 Xamarin 开发 Android，请参阅 [Hello, Android](~/android/get-started/hello-android/index.md) 了解如何创建 Xamarin.Android 项目。

创建 Android 项目时，必须将版本设置配置为面向 Android 8.0 或更高版本。 例如，若要将项目目标指向 Android 8.0，必须将项目的 Android API 级别目标配置为“Android 8.0 (API 26)”  。 建议同时将目标框架级别设置为 API 26 或更高级别。 有关配置 Android API 级别的详细信息，请参阅[了解 Android API 级别](~/android/app-fundamentals/android-api-levels.md)。

### <a name="configure-an-emulator-or-device"></a>配置仿真器或设备

如果在安装 Android SDK Tools 26.0 或更高版本后，尝试启动默认的基于Google GUI 的 AVD 管理器，可能会出现以下错误对话框，指示你改用命令行 AVD 管理器工具 avdmanager  ：

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![Android Emulator 管理器警告对话框](oreo-images/win/03-avd-warning.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

![Android Emulator 管理器警告对话框](oreo-images/mac/03-avd-warning.png)

-----

显示此消息是因为 Google 不再提供支持 API 26.0 及更高版本的独立 GUI AVD 管理器。 对于 Android 8.0 Oreo，必须使用 Xamarin Android Emulator 管理器或命令行 `avdmanager` 工具创建适用于 Android Oreo 的虚拟设备。

要使用 Android 设备管理器创建和管理虚拟设备，请参阅[使用 Android 设备管理器管理虚拟设备](~/android/get-started/installation/android-emulator/device-manager.md)。
若要创建不包含 Android 设备管理器的虚拟设备，请按照下一部分中的步骤进行操作。

#### <a name="creating-virtual-devices-using-avdmanager"></a>使用 avdmanager 创建虚拟设备

若要使用 avdmanager 创建新的虚拟设备，请遵循以下步骤  ：

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 打开“命令提示符”窗口，将 `JAVA_HOME` 设置为计算机上 Java SDK 的位置。 对于典型的 Xamarin 安装，可以使用以下命令：

    ```cmd
    setx JAVA_HOME "C:\Program Files\Java\jdk1.8.0_131"
    ```

2. 将 Android SDK `bin` 文件夹的位置添加到 `PATH`。
    对于典型的 Xamarin 安装，可以使用以下命令：

    ```cmd
    setx PATH "%PATH%;C:\Program Files (x86)\Android\android-sdk\tools\bin"
    ```

3. 关闭该“命令提示符”窗口，打开一个新的“命令提示符”窗口。 使用 [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html) 命令创建新的虚拟设备。 例如，若要使用适用于 API 级别 26 的 x86 系统映像创建名为 AVD-Oreo-8.0 的 AVD，请使用以下命令  ：

    ```cmd
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

4. 当系统提示你“是否要创建自定义硬件配置文件 [no]”时，你可以输入“否”并接受默认硬件配置文件   。 如果你选择“是”，avdmanager 会提示你提供自定义硬件配置文件的问题列表   。

使用 avdmanager 创建虚拟设备后，该设备将包含在设备下拉菜单中  ：

[![新 AVD 已添加到设备下拉菜单](oreo-images/win/04-android-o-avd-sml.png)](oreo-images/win/04-android-o-avd.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 打开“终端”窗口，并在 Mac 上切换到 Android SDK 工具目录的位置  。 对于典型的 Xamarin 安装，可以使用以下命令：

    ```bash
    cd ~/Library/Developer/Xamarin/android-sdk-macosx/tools/bin
    ```

2. 使用 [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html) 命令创建新的虚拟设备。 例如，若要使用适用于 API 级别 26 的 x86 系统映像创建名为 AVD-Oreo-8.0 的 AVD，请使用以下命令  ：

    ```bash
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

3. 当系统提示你“是否要创建自定义硬件配置文件 [no]”时，你可以输入“否”并接受默认硬件配置文件   。 如果你选择“是”，avdmanager 会提示你提供自定义硬件配置文件的问题列表   。

使用 avdmanager 创建虚拟设备后，该设备将包含在设备下拉菜单中  ：

[![新 AVD 已添加到设备下拉菜单](oreo-images/mac/04-android-o-avd-sml.png)](oreo-images/mac/04-android-o-avd.png#lightbox)

-----

有关配置 Android Emulator 进行测试和调试的详细信息，请参阅[在 Android Emulator 上调试](~/android/deploy-test/debugging/debug-on-emulator.md)。

如果使用的是物理设备（例如，Nexus 或 Pixel），则可以通过自动无线 (OTA) 更新来更新设备，或下载系统映像并直接刷新设备。 有关手动将设备更新到 Android Oreo 的详细信息，请参阅 [Nexus 和 Pixel 设备的出厂映像](https://developers.google.com/android/images)。

## <a name="new-features"></a>新增功能

Android Oreo 引入了各种新功能，如通知通道、通知锁屏提醒、XML 自定义字体、可下载字体、自动填充和画中画。 以下各节重点介绍这些功能，并提供可帮助你在应用中开始使用这些功能的链接。

### <a name="notification-channels"></a>通知通道

通知通道是应用定义的通知类别  。
你可以为需要发送的每种通知类型创建通知通道，还可以创建通知通道以反映应用用户所做的选择。 利用新的通知通道功能，你可以为用户提供对各种通知的细化控制。 例如，如果要实现消息传递应用，可以为用户创建的每个会话组创建单独的通知通道。

如需了解如何创建通知通道并将其用于发布本地通知，请参阅[通知通道](~/android/app-fundamentals/notifications/local-notifications.md#notif-chan)。 有关实际的代码示例，请参阅 [NotificationChannels](/samples/xamarin/monodroid-samples/android-o-notificationchannels) 示例；此示例应用管理两个通道并设置其他通知选项。

### <a name="notification-badges"></a>通知锁屏提醒

通知锁屏提醒是显示在应用图标上方的小点，如以下屏幕截图所示：

[![应用图标上的示例通知锁屏提醒](oreo-images/02-badges-sml.png)](oreo-images/02-badges.png#lightbox)

这些点表示与该应用图标相关联的应用中有一个或多个通知通道的新通知 &ndash; 这些通知是用户尚未解除或未执行的通知。 用户可以长按图标，浏览与通知锁屏提醒相关的通知，从出现的长按菜单取消或执行通知。

有关通知锁屏提醒的详细信息，请参阅 Android 开发人员[通知锁屏提醒](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#Badges)主题。

### <a name="custom-fonts-in-xml"></a>XML 自定义字体

Android Oreo 引入了 XML 字体，这使你能够将自定义字体合并为资源  。 支持 OpenType (.otf)和 TrueType (.ttf) 字体格式   。 若要将字体添加为资源，请执行以下操作：

1. 创建“Resources/font”文件夹  。

2. 将字体文件（例如，.ttf 和 .otf 文件）复制到“Resources/font”文件夹    。 

3. 如有必要，请重命名每个字体文件，使其遵循 Android 文件命名约定（即，在文件名中仅使用小写 a - z、0 - 9 和下划线）   。 例如，可以将字体文件 `Pacifico-Regular.ttf` 重命名为类似 `pacifico.ttf` 的文件名。

4. 使用布局 XML 中的新 `android:fontFamily` 属性应用自定义字体。 例如，下面的 `TextView` 声明使用添加的 pacifico.ttf 字体资源  ：

   ```xml
   <TextView
     android:text="Example Text in Pacifico Regular"
     android:layout_width="wrap_content"
     android:layout_height="wrap_content"
     android:fontFamily="@font/pacifico" />
   ```

还可以创建一个字体系列 XML 文件，该文件描述多种字体以及样式和粗细等详细信息。 有关详细信息，请参阅 Android 开发人员 [XML 字体](https://developer.android.com/guide/topics/ui/look-and-feel/fonts-in-xml.html)主题。

### <a name="downloadable-fonts"></a>可下载字体

从 Android Oreo 开始，应用可以向提供程序请求字体，而不是将其绑定到 APK。 仅在需要时才从网络下载字体。 此功能可减少 APK 大小、节省手机内存和蜂窝数据的使用量。 还可以通过安装 Android 支持库 26 包在 Android API 版本 14 及更高版本上使用此功能。

如果应用需要字体，则可创建 `FontsRequest` 对象（指定要下载的字体），然后将其传递给 `FontsContract` 方法以下载字体。 以下步骤更详细介绍了字体下载过程：

1. 安装 [FontRequest](https://developer.android.com/reference/android/provider/FontRequest.html) 对象。 

2. 子类化并实例化 [FontsContract.FontRequestCallback](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html)。

3. 实现 [FontRequestCallback.OnTypeFaceRetrieved](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRetrieved%28android.graphics.Typeface%29) 方法，该方法用于处理字体请求的完成。

4. 实现 [FontRequestCallback.OnTypeFaceRequestFailed](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRequestFailed%28int%29) 方法，该方法用于在字体请求过程中向应用通知发生的任何错误。

5. 调用 [FontsContract.RequestFonts](https://developer.android.com/reference/android/provider/FontsContract.html#requestFonts(android.content.Context,%20android.provider.FontRequest,%20android.os.Handler,%20android.os.CancellationSignal,%20android.provider.FontsContract.FontRequestCallback)) 方法，从字体提供程序检索字体。 

调用 `RequestFonts` 方法时，它首先从先前对 `RequestFont` 的调用中检查该字体是否在本地进行缓存。 如果未缓存此字体，它将调用字体提供程序，异步检索字体，然后通过调用 `OnTypeFaceRetrieved` 方法将结果传递回应用。

[可下载字体](/samples/xamarin/monodroid-samples/android-o-downloadablefonts)示例演示了如何使用 Android Oreo 中引入的可下载字体功能。 

有关可下载字体的详细信息，请参阅 Android 开发人员[可下载字体](https://developer.android.com/guide/topics/ui/look-and-feel/downloadable-fonts.html)主题。

### <a name="autofill"></a>自动填充

Android Oreo 中的新自动填充框架使用户能够更轻松地处理重复性任务，如登录、帐户创建和信用卡交易  。 用户花费在重新键入信息的时间会更短（这可能会导致输入错误）。 必须在系统设置中启用自动填充服务（用户可以启用或禁用自动填充）之后，你的应用才能使用自动填充框架。

[AutofillFramework](/samples/xamarin/monodroid-samples/android-o-autofillframework) 示例演示了如何使用自动填充框架。 它包括客户端活动的实现和应自动填充的视图，以及可向客户端活动提供自动填充数据的服务。

有关新的自动填充功能以及如何优化应用以进行自动填充的详细信息，请参阅 Android 开发人员[自动填充框架](https://developer.android.com/guide/topics/text/autofill.html)主题。

### <a name="picture-in-picture-pip"></a>画中画 (PIP)

Android Oreo 使活动可以在画中画 (PIP) 模式下启动，从而覆盖另一个活动的屏幕。 此功能适用于视频播放。

要指定应用的活动可使用 PIP 模式，请在 Android 清单中将以下标志设置为 true：

```xml
android:supportsPictureInPicture
```

要指定活动处于 PIP 模式时的行为方式，请使用新的 [PictureInPictureParams](https://developer.android.com/reference/android/app/PictureInPictureParams.html) 对象。 `PictureInPictureParams` 表示一组参数，用于在 PIP 模式下初始化和更新活动（例如活动的首选纵横比）。 以下新的 PIP 方法已添加到 Android Oreo 中的 `Activity`：

- [EnterPictureInPictureMode](https://developer.android.com/reference/android/app/Activity.html#enterPictureInPictureMode%28android.app.PictureInPictureParams%29) &ndash; 使活动处于 PIP 模式下。 活动位于屏幕的角落，而屏幕的其余部分则由屏幕上的前一个活动填充。

- [SetPictureInPictureParams](https://developer.android.com/reference/android/app/Activity.html#setPictureInPictureParams%28android.app.PictureInPictureParams%29) &ndash; 更新活动的 PIP 配置设置（例如，纵横比的变化）。

[PictureInPicture](/samples/xamarin/monodroid-samples/android-o-pictureinpicture) 示例演示了 Oreo 中引入的手持式设备的画中画 (PiP) 模式的基本用法。 此示例播放一个视频，该视频在显示模式或其他活动之间来回切换时不会中断。

### <a name="other-features"></a>其他功能

Android Oreo 包含许多其他新功能，例如表情符号支持库、Location API、背景限制、应用的宽范围颜色、新音频编解码器、Web 视图增强、改进的键盘导航支持和用于高性能低延迟音频的新 AAudio（pro 音频）API，有关这些功能的详细信息，请参阅 Android 开发人员 [Android Oreo 功能与 API](https://developer.android.com/about/versions/oreo/android-8.0.html) 主题。

## <a name="behavior-changes"></a>行为更改

Android Oreo 包括各种系统和 API 行为更改，这些更改可能会影响现有应用的功能。 这些更改如下所述。

### <a name="background-execution-limits"></a>后台执行限制

为了改进用户体验，Android Oreo 对应用在后台运行时可以执行的操作设置了限制。 例如，如果用户正在观看视频或玩游戏，在后台运行的应用可能会影响在前台运行的视频密集型应用的性能。 因此，Android Oreo 对未与用户直接交互的应用设置了以下限制：

1. **后台服务限制** &ndash; 当应用在后台运行时，会产生一个只有几分钟的时间窗，在此时间内仍可以创建和使用服务。 该时间窗结束后，Android 会停止应用的后台服务，并将其视为空闲  。

2. **广播限制** &ndash; Android 7.0 (API 25) 对应用注册接收的广播设置了限制。 在 Android Oreo 中，这些限制更加严格。 例如，Android Oreo 应用无法再为其清单中的隐式广播注册广播接收器。

有关新的后台执行限制的详细信息，请参阅 Android 开发人员[后台执行限制](https://developer.android.com/about/versions/oreo/background.html)主题。

### <a name="breaking-changes"></a>重大更改

面向 Android Oreo 或更高版本的应用必须修改其应用才能支持以下更改（如果适用）：

- Android Oreo 弃用了设置单个通知的优先级的功能。 取而代之，可在创建通知通道时设置建议的重要性级别。 你分配给通知通道的重要性级别适用于你向其发送的所有通知消息。

- 对于面向 Android Oreo 的应用，由于对在后台启动的服务设置了新的限制，`PendingIntent.GetService()` 不起作用。 如果面向的是 Android Oreo，则应改用 [PendingIntent.GetBroadcast](xref:Android.App.PendingIntent.GetBroadcast*)。  

## <a name="sample-code"></a>代码示例

提供了多个 Xamarin.Android 示例，用于演示如何利用 Android Oreo 功能：

- [NotificationsChannels](/samples/xamarin/monodroid-samples/android-o-notificationchannels) 演示如何使用 Android Oreo 中引入的新通知通道系统。 此示例管理两个通知通道：一个的重要性级别为默认，另一个的重要性级别为非常重要。

- [PictureInPicture](/samples/xamarin/monodroid-samples/android-o-pictureinpicture) 演示了 Oreo 中引入的手持式设备的画中画 (PiP) 模式的基本用法。 此示例播放一个视频，该视频在显示模式或其他活动之间来回切换时不会中断。

- [AutofillFramework](/samples/xamarin/monodroid-samples/android-o-autofillframework) 演示了如何使用自动填充框架。 它包括客户端活动的实现和应自动填充的视图，以及可向客户端活动提供自动填充数据的服务。

- [可下载字体](/samples/xamarin/monodroid-samples/android-o-downloadablefonts) 提供了有关如何使用前面所述的可下载字体功能的示例。

- [EmojiCompat](/samples/xamarin/monodroid-samples/android-o-emojicompat) 演示了 EmojiCompat 支持库的用法。 可以使用此库来防止应用将缺少的表情符号字符显示为“tofu”字符。

- [位置更新挂起意向](/samples/xamarin/monodroid-samples/android-o-androidplaylocation-locupdpendintent)说明了 Location API 的用法，以使用 `PendingIntent` 获取有关设备位置的更新。

- [位置更新前景服务](/samples/xamarin/monodroid-samples/android-o-androidplaylocation-locupdfgservice)演示了如何使用 Location API 通过绑定和启动的前台服务获取有关设备位置的更新。

## <a name="video"></a>视频

> [!VIDEO https://youtube.com/embed/OuvEcaMO-Ho]

**使用 C# 开发 Android 8.0 Oreo**

## <a name="summary"></a>总结

本文介绍了 Android Oreo，并说明如何为 Android Oreo 上的 Xamarin.Android 开发安装和配置最新的工具和包。 它概述了 Android Oreo 提供的关键功能，并提供了多个新功能的示例源代码链接。 它提供了指向 API 文档和 Android 开发人员主题的链接，帮助你开始为 Android Oreo 创建应用。 它还重点介绍了可能影响现有应用的最重要的 Android Oreo 行为更改。

## <a name="related-links"></a>相关链接

- [Android 8.0 Oreo](https://developer.android.com/index.html)