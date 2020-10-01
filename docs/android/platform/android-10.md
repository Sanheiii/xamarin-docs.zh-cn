---
title: Android 10 功能
description: 如何开始使用 Xamarin.Android 开发适用于 Android 10 的应用。
ms.assetid: B3342772-FB88-4B7F-BC15-8BC78EED749E
author: JonDouglas
ms.author: jodou
ms.date: 09/17/2019
ms.openlocfilehash: 1ce0233a45211ee91b9f1e30247ac18cdd5c5a66
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91454294"
---
# <a name="android-10-with-xamarin"></a>结合使用 Android 10 和 Xamarin

_如何开始使用 Xamarin.Android 开发适用于 Android 10 的应用。_

Google 现已提供 Android 10。 此版本提供了许多新功能和 API，在最新 Android 设备中利用新硬件功能时，必须使用这些功能和 API。

![Android 10 徽标](~/android/platform/android-10-images/android10_black.png)

本文旨在帮助你开始开发适用于 Android 10 的 Xamarin.Android 应用。 它介绍如何安装必要的更新、如何配置 SDK，以及如何准备用于测试的仿真器或设备。 它还概要介绍了 Android 10 中的新增功能，并提供了演示如何使用 Android 10 的一些关键功能的示例源代码。

Xamarin.Android 10.0 为 Android 10 提供支持。 若要详细了解对 Android 10 的 Xamarin.Android 支持，请参阅 [Xamarin.Android 10.0 发行说明](/xamarin/android/release-notes/10/10.0)。

## <a name="requirements"></a>要求

若要在基于 Xamarin 的应用中使用 Android 10 功能，需具备以下组件：

- **Visual Studio** - 建议使用 Visual Studio 2019。 在 Windows 上，请更新到 Visual Studio 2019 版本 16.3 或更高版本。 在 macOS 上，更新到 Visual Studio 2019 for Mac 版本 8.3 或更高版本。
- **Xamarin.Android** - 必须随 Visual Studio 一起安装 Xamarin.Android 10.0 或更高版本（在 Windows 上，Xamarin.Android 将作为“使用 .NET 的移动开发”  工作负载的一部分自动安装，并作为 Visual Studio for Mac 安装程序的一部分安装  ）
- **Java 开发人员工具包** - Xamarin.Android 10.0 开发需要 JDK 8。 Microsoft 的 OpenJDK 分发版是作为 Visual Studio 的一部分自动安装的。
- **Android SDK** 必须通过 Android SDK 管理器安装 Android SDK API 29。

## <a name="get-started"></a>入门

若要开始使用 Xamarin.Android 开发 Android 10 应用，必须先下载并安装最新的工具和 SDK 包，然后才能开始创建 Android 10 项目：

1. **建议使用 Visual Studio 2019**。 更新到 Visual Studio 2019 版本 16.3 或更高版本。 如果你使用的是 Visual Studio for Mac 2019，请更新为 Visual Studio 2019 for Mac 版本 8.3 或更高版本。
2. 通过 SDK 管理器安装 Android 10 (API 29)  包和工具。
    - Android 10 (API 29) SDK 平台
    - Android 10 (API 29) 系统映像
    - Android SDK 生成工具 29.0.0 以上版本
    - Android SDK 平台工具 29.0.0 以上版本
    - Android Emulator 29.0.0 以上版本
3. 创建面向 Android 10.0 的新 Xamarin.Android 项目。
4. 配置仿真器或设备以测试 Android 10 应用。

下面将介绍其中的每个步骤：

### <a name="update-visual-studio"></a>更新 Visual Studio

要使用 Xamarin 生成 Android 10 应用，建议使用 Visual Studio 2019。

如果你使用的是 Visual Studio 2019，请更新为 Visual Studio 2019 版本 16.3 或更高版本（有关说明，请参阅[将 Visual Studio 2019 更新为最新版本](/visualstudio/install/update-visual-studio)）。 在 macOS 上，请更新为 Visual Studio 2019 for Mac 8.3 或更高版本（有关说明，请参阅[将 Visual Studio 2019 for Mac 更新为最新版本](/visualstudio/mac/update)）。

### <a name="install-the-android-sdk"></a>安装 Android SDK

若要使用 Xamarin.Android 10.0 创建项目，必须首先使用 Android SDK 管理器安装适用于 Android 10（API 级别 29）的 SDK 平台  。

1. 启动 SDK 管理器。 在 Visual Studio 中，单击“工具”>“Android”>“Android SDK 管理器”。  在 Visual Studio for Mac 中，单击“工具”>“SDK 管理器”。 
2. 在右下角单击齿轮图标，然后选择“存储库”>“Google (不受支持)”： 

    ![Android SDK 管理器的“存储库”选择](~/android/platform/android-10-images/sdkrepository.png)

3. 安装 Android 10 SDK 平台包，这些包在“平台”选项卡中列为 Android SDK 平台 29（有关使用 SDK 管理器的详细信息，请参阅 [Android SDK 设置](../get-started/installation/android-sdk.md)）    ：

    ![Android SDK 管理器的“平台”选项卡](~/android/platform/android-10-images/sdkplatforms.png)

### <a name="create-a-xamarinandroid-project"></a>创建 Xamarin.Android 项目

创建新的 Xamarin.Android 项目。 如果你不熟悉如何使用 Xamarin 开发 Android，请参阅 [Hello, Android](../get-started/hello-android/index.md) 了解如何创建 Xamarin.Android 项目。

在创建 Android 项目时，必须将版本设置配置为面向 Android 10.0 或更高版本。 例如，若要使项目面向 Android 10，必须将项目的目标 Android API 级别配置为“Android 10.0 (API 29)”  。 这需要同时将“目标框架版本”  和“目标 Android SDK 版本”  设置为 API 29 或更高级别。 有关配置 Android API 级别的详细信息，请参阅[了解 Android API 级别](../app-fundamentals/android-api-levels.md)。

![Xamarin.Android 目标框架](~/android/platform/android-10-images/targetframework.png)

### <a name="configure-a-device-or-emulator"></a>配置设备或仿真器

如果你使用的是物理设备（如 Pixel），可在手机的设置中转到“系统”>“系统更新”>“检查更新”，来下载 Android 10 更新。 如果倾向于刷机，请参阅有关将[出厂映像](https://developers.google.com/android/images)或 [OTA 映像](https://developers.google.com/android/ota)更新到设备的说明。

如果使用的是仿真器，请为 API 级别 29 创建虚拟设备，并选择基于 x86 的映像。 有关使用 Android 设备管理器创建和管理虚拟设备的信息，请参阅[使用 Android 设备管理器管理虚拟设备](../get-started/installation/android-emulator/device-manager.md)。 有关使用 Android 仿真器进行测试和调试的信息，请参阅 [Android 仿真器调试](../deploy-test/debugging/debug-on-emulator.md)。

## <a name="new-features"></a>新增功能

Android 10 引入了多种新功能。 其中一些新功能旨在利用最新 Android 设备提供的新硬件功能，而其他一些功能旨在进一步改善 Android 用户体验：

## <a name="enhance-your-app-with-android-10-features-and-apis"></a>利用 Android 10 功能和 API 增强应用功能

接下来，当你准备就绪时，深入了解 Android 10 并了解可以使用的 [新功能和 API](https://developer.android.com/preview/api-overview.html) 。 下面是一些主要入门功能。

建议对每个应用采用以下功能：

- **深色主题：**  通过添加 [深色主题](https://developer.android.com/preview/features/darktheme) 或启用 [强制深色](https://developer.android.com/preview/features/darktheme#force_dark)，确保为启用系统级深色主题的用户提供一致的体验。

![深色主题](~/android/platform/android-10-images/darktheme.png)

- **支持应用内 [手势导航](https://developer.android.com/preview/features/gesturalnav)**  ：进入全屏幕模式并确保自定义手势与系统导航手势配合使用。

![手势导航](~/android/platform/android-10-images/gesturenavigation.png)

- **优化可折叠内容：**  通过 [优化可折叠内容](https://developer.android.com/preview/features/foldables)在现有创新设备上提供无缝的全屏体验。

![可折叠](~/android/platform/android-10-images/foldable.png)

建议在具有适用的应用时使用以下功能：

- **更多交互式通知：**  如果你的通知包括消息，请启用 [通知中建议的答复和操作](https://developer.android.com/preview/features#smart-suggestions) 以与用户联系并让他们立即采取措施。
- **更强大的生物识别技术：**  如果你使用生物识别身份验证，请转到 [BiometricPrompt](https://developer.android.com/reference/androidx/biometric/BiometricPrompt)，这是支持新式设备上的指纹身份验证的首选方式。
- **丰富的记录：**  若要支持字幕或游戏录制，请启用 [音频播放捕获](https://developer.android.com/preview/features/playback-capture)。 这是一个很好的方法，可以吸引更多用户并使你的应用更易于访问。
- **更适用的编解码器：**  对于媒体应用，请尝试将 [AV1](https://en.wikipedia.org/wiki/AV1) 用于视频流式处理，将 [HDR10+](https://en.wikipedia.org/wiki/High-dynamic-range_video#HDR10+) 用于高动态范围视频。 对于语音和音乐流式处理，可以使用 [Opus](http://opus-codec.org/) 编码，对于音乐家，可以使用 [本机 MIDI API](https://developer.android.com/preview/features/midi) 。
- **更好的网络 API：**  如果你的应用程序通过 Wi-Fi 管理 IoT 设备，请尝试使用新的 [网络连接 API](https://developer.android.com/preview/features#peer2peer) ，以实现配置、下载或打印之类的功能。

以上只是 Android 10 中的一小部分新功能和 API。 若要查看全部新功能和 API，请访问 [面向开发人员的 Android 10 网站](https://developer.android.com/about/versions/10/highlights)。

## <a name="behavior-changes"></a>行为变更

当目标 Android 版本设置为 API 级别 29时，即使未实现上述新功能，也会有一些平台更改会影响应用的行为。 下面的列表概要介绍了这些更改：

- [为了确保应用稳定性和兼容性，Android 平台现在会限制应用可在 Android 10 中使用的非 SDK 接口](https://developer.android.com/about/versions/10/behavior-changes-10#non-sdk-restrictions)。
- [共享内存已更改](https://developer.android.com/about/versions/10/behavior-changes-10#shared-memory)。
- [Android 运行时和 AOT 的正确性](https://developer.android.com/about/versions/10/behavior-changes-10#system-only-oat)。
- [全屏意向权限必须请求 `USE_FULL_SCREEN_INTENT`](https://developer.android.com/about/versions/10/behavior-changes-10#full-screen-intents)。
- [支持可折叠内容](https://developer.android.com/about/versions/10/behavior-changes-10#foldables)。

## <a name="summary"></a>总结

本文介绍了 Android 10，并说明了如何安装和配置最新的工具和包，以便进行 Android 10 的 Xamarin.Android 开发。 其中概述了 Android 10 中提供的关键功能。 它提供了指向 API 文档和 Android 开发人员主题的链接，帮助你开始为 Android 10 创建应用。 它还重点介绍了可能影响现有应用的最重要的 Android 10 行为更改。

## <a name="related-links"></a>相关链接

- [Android 10](https://developer.android.com/about/versions/10)