---
title: Marshmallow 功能
description: 本文介绍如何开始使用 Xamarin.Android 开发 Android 6.0 Marshmallow 应用。
ms.prod: xamarin
ms.assetid: E4D6F183-98D2-460A-9D65-937639A899E0
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 52c44efa335d81004ce2e3dbf0d9160640118bea
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453540"
---
# <a name="marshmallow-features"></a>Marshmallow 功能

_本文介绍如何开始使用 Xamarin.Android 开发 Android 6.0 Marshmallow 应用。_

本文概述了 Android 6.0 Marshmallow 中引入的新功能，介绍如何准备用于 Android Marshmallow 开发的 Xamarin.Android，并提供指向示例应用程序的链接，这些示例应用演示了如何在 Xamarin.Android 应用中使用新的 Android Marshmallow 功能。 

## <a name="overview"></a>概述

[Android 6.0 Marshmallow](https://developer.android.com/about/versions/marshmallow/index.html) 是继 Android Lollipop 的下一个主要 Android 版本。
Xamarin.Android 支持 Android Marshmallow，其中包括：

- **API 23/Android 6.0 绑定** &ndash; Android 6.0 为如下所述的新功能添加了许多新 API；面向 API 级别 23 时，这些 API 可供 Xamarin.Android 应用使用。 有关 Android 6.0 API 的详细信息，请参阅 [Android 6.0 API](https://developer.android.com/preview/api-overview.html)。 

[![运行 Marshmallow 的平板电脑和手机的 Hero 映像](marshmallow-images/android-m-hero-sml.png)](marshmallow-images/android-m-hero.png#lightbox)

尽管 Marshmallow 版本主要侧重于“美观和质量”，但它还提供了许多 Xamarin.Android 开发人员感兴趣的新功能。 这些功能包括： 

- **运行时权限** &ndash; 此增强功能使用户能够在运行时根据具体情况审批安全权限。 

- **身份验证改进** &ndash; 从 Android Marshmallow 开始，应用程序现在可以使用指纹传感器来对用户进行身份验证，而新的“确认凭据”  功能最大限度地减少了输入密码的需要。 

- **应用链接** &ndash; 此功能帮助自动将应用与 Web 域关联，而无需再弹出“应用选择器”  。 

- **直接共享** &ndash; 可以定义“直接共享目标”  ，使用户能够快速直观地进行共享；此功能允许用户与其他应用共享内容。 

- **语音交互** &ndash; 此新 API 可让你在应用程序中生成会话语音功能。 

- **4K 显示模式** &ndash; 在 Android Marshmallow 中，应用可以在支持 4K 显示分辨率的硬件上请求它。 

- **新的音频功能** &ndash; 从 Marshmallow 开始，Android 现支持 MIDI 协议。 它还提供了用于创建数字音频捕获和播放对象的新类，并为关联音频和输入设备提供了新的 API 挂钩。 

- **新的视频功能** &ndash; Marshmallow 提供了一个新类，可帮助应用以同步的顺序呈现音频和视频流；此类还为动态回放速率提供支持。 

- **Android for Work** &ndash; Marshmallow 包括对企业拥有的单用户设备的增强控制。 它支持设备所有者无提示安装和卸载应用、自动接受系统更新、改进的证书管理、数据使用跟踪、权限管理和工作状态通知。 

- **材料设计支持库** &ndash; 新的“设计支持库”  提供设计组件和模式，使你可以更轻松地在应用程序中生成材料设计外观。 

此外，许多核心 Android 库更新随 Android M 一同发布，这些更新为 Android M 和早期版本的 Android 提供了新功能。

此外，许多核心 Android 库更新随 Android Marshmallow 发布，这些更新为 Android Marshmallow 和早期版本的 Android 提供了新功能。 本文介绍如何开始使用 Android Marshmallow 生成应用，并概述了 Android 6.0 中新增功能的要点。 

## <a name="requirements"></a>要求

若要在基于 Xamarin 的应用中使用新的 Android Marshmallow 功能，需满足以下要求： 

- **Xamarin.Android** &ndash; 必须安装 Xamarin.Android 5.1.7.12 或更高版本并配置 Visual Studio 或 Xamarin Studio。

- **Visual Studio for Mac** 或 **Visual Studio** &ndash; 如果使用的是 Visual Studio for Mac，则需要版本 5.9.7.22 或更高版本。 如果使用的是 Visual Studio，则需要 Visual Studio Xamarin 工具的版本 3.11.1537 或更高版本。 

- **Android SDK** &ndash; 必须通过 Android SDK 管理器安装 Android SDK 6.0 (API 23) 或更高版本。

- **Java 开发人员工具包** &ndash; 如果开发 API 级别 24 或更高级别，则 Xamarin Android 需要 [JDK 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)（JDK 1.8 还支持低于 24 的 API 级别，包括 Marshmallow）。 如果使用的是自定义控件或窗体预览器，则需要使用 64 位版本的 JDK 1.8。

如果专门为 API 级别 23 或更低级别进行开发，可以继续使用 [JDK 1.7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)。 

## <a name="getting-started"></a>入门

若要开始结合使用 Android Marshmallow 和 Xamarin.Android，必须先下载并安装最新的工具和 SDK 包，然后才能创建 Android Marshmallow 项目： 

1. 从稳定  通道安装最新的 Xamarin 更新。 

2. 安装 Android 6.0 Marshmallow SDK 包和工具。

3. 创建面向 Android 6.0 Marshmallow（API 级别 23）的新 Xamarin.Android 项目。 

4. 为 Android Marshmallow 配置仿真器或设备。

以下部分介绍其中的每个步骤：

### <a name="install-xamarin-updates"></a>安装 Xamarin 更新

若要更新 Xamarin 以使其包含对 Android 6.0 Marshmallow 的支持，请将更新通道改为“稳定”  并安装所有更新。 有关从更新通道安装更新的详细信息，请参阅[更改更新通道](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/change_updates_channel)。 

### <a name="install-the-android-60-sdk"></a>安装 Android 6.0 SDK

若要创建适用于 Android Marshmallow 的 Xamarin.Android 项目，必须首先使用 Android SDK 管理器安装 Android 6.0 SDK：

- 启动 Android SDK 管理器（在 Visual Studio for Mac 中，使用“工具”>“SDK 管理器”  ；在 Visual Studio 中，使用“工具”>Android”>“Android SDK 管理器”  ）并安装最新的 Android SDK Tools：

    [![在 Android SDK 管理器中选择 Android SDK 工具](marshmallow-images/mnc-preview-tools.png)](marshmallow-images/mnc-preview-tools.png#lightbox)

- 同时，安装最新的 **Android 6.0** SDK 包：

    [![在 Android SDK 管理器中选择 Android 6.0 SDK 包](marshmallow-images/mnc-preview-packages.png)](marshmallow-images/mnc-preview-packages.png#lightbox)

必须安装 Android SDK Tools 修订版 24.3.4 或更高版本。
有关使用 Android SDK 管理器安装 Android 6.0 SDK 的详细信息，请参阅 [SDK 管理器](https://developer.android.com/tools/help/sdk-manager.html)。

### <a name="start-a-xamarinandroid-project"></a>启动 Xamarin.Android 项目

创建新的 Xamarin.Android 项目。 如果不熟悉通过 Xamarin 开发 Android，请参阅 [Hello, Android](~/android/get-started/hello-android/index.md)，了解如何创建 Android 项目。 

创建 Android 项目时，必须将版本设置配置为面向 Android 6.0 MarshMallow。 若要将项目面向 Marshmallow，必须将项目配置为“API 级别 23 (Xamarin.Android v6.0 支持)”  。 有关配置 Android API 级别的详细信息，请参阅[了解 Android API 级别](~/android/app-fundamentals/android-api-levels.md)。

### <a name="configure-an-emulator-or-device"></a>配置仿真器或设备

如果使用的是仿真器，请启动 Android AVD 管理器，并使用以下设置创建新设备：

- 设备:Nexus 5、6 或 9。
- 目标：Android 6.0 - API 级别 23
- ABI：x86

例如，此虚拟设备配置为模拟 Nexus 5：

[![使用 Nexus 5 设备、Android 6.0 目标和 Intel Atom (x86) 配置 AVD](marshmallow-images/android-m-avd.png)](marshmallow-images/android-m-avd.png#lightbox)

如果使用的是物理设备（如 Nexus 5、6 或 9），则可以安装 Android Marshmallow 的预览图。 有关将设备更新到 Android Marshmallow 的详细信息，请参阅[硬件系统映像](https://developer.android.com/preview/download.html#images)。

## <a name="new-features"></a>新增功能

Android Marshmallow 中引入的许多更改都侧重于改善 Android 用户体验、提高性能并修复 bug。 不过，Marshmallow 还对 Android 平台的基础功能引入了一些广泛的更改。 以下各节重点介绍这些增强功能，并提供可帮助你在应用中开始使用新 Android Marshmallow 功能的链接。 

### <a name="runtime-permissions"></a>运行时权限

自 Android Lollipop 以来，Android 权限系统经过了大幅优化和简化。 在 Android Marshmallow 中，用户在运行时（而不是在安装时）根据具体情况授予权限。 若要在 Android Marshmallow 和更高版本上支持此功能，请将应用程序设计为在运行时提示用户权限（在需要权限的上下文中）。 此更改使用户能够更轻松地立即开始使用应用程序，因为它简化了安装和升级应用的过程。 

若要详细了解如何在 Xamarin.Android 应用程序中实现运行时权限（包括示例代码），请参阅[在 Android Marshmallow 中请求运行时权限](https://blog.xamarin.com/requesting-runtime-permissions-in-android-marshmallow/)。
Xamarin 还提供了一个示例应用，演示运行时权限在 Android Marshmallow（及更高版本）中的工作方式：[RuntimePermissions](/samples/xamarin/monodroid-samples/android-m-runtimepermissions)。

此示例应用对下列内容进行了说明：

- 如何在运行时检查并请求权限。
- 如何声明 Android M 设备的权限。

使用此示例应用：

1. 点击“相机”  或“联系人”  按钮显示权限请求对话框。
2. 授予查看“相机”或“联系人”片段的权限。

有关 Android Marshmallow 中的新运行时权限功能的详细信息，请参阅[使用系统权限](https://developer.android.com/preview/features/runtime-permissions.html)。

### <a name="authentication-enhancements"></a>身份验证增强功能

Android Marshmallow 包括两个身份验证增强功能，无需提供密码：

- **指纹身份验证** &ndash; 使用指纹扫描对用户进行身份验证。

- **确认凭据** &ndash; 根据设备解锁时间对用户进行身份验证。

接下来介绍的链接和示例应用可帮助你熟悉这些新功能。

#### <a name="fingerprint-authentication"></a>指纹身份验证

在支持指纹扫描硬件的设备上，可以使用新的 `FingerPrintManager` 类来对用户进行身份验证。
有关 Android Marshmallow 中的指纹身份验证功能的详细信息，请参阅[指纹身份验证](https://developer.android.com/preview/api-overview.html#fingerprint-authentication)。

Xamarin 提供了一个示例应用，演示如何使用注册的指纹在应用中对用户进行身份验证：[FingerprintDialog](/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)。

使用此示例应用：

1. 触摸“购买”  按钮打开指纹身份验证对话框。
2. 扫描已注册的指纹以进行身份验证。

请注意，此示例应用要求设备带有指纹读取器。
此应用不存储指纹（或密码）。

#### <a name="voice-interactions"></a>语音交互

Android Marshmallow 中引入了新的语音交互功能，使应用程序用户可以使用其语音确认操作，并从选项列表中进行选择。 有关语音交互的详细信息，请参阅[语音交互 API 概述](https://developers.google.com/voice-actions/interaction/)。 

有关在 Xamarin.Android 应用中实现语音交互的详细信息（包括示例代码），请参阅[使用语音交互向 Android 应用添加会话](https://blog.xamarin.com/add-a-conversation-to-your-android-app-with-voice-interactions/)。
提供了一个示例应用，演示如何在 Xamarin.Android 应用中使用语音交互 API：[语音交互](https://github.com/jamesmontemagno/MarshmallowSamples/tree/master/VoiceInteractions)。

#### <a name="confirm-credential"></a>确认凭据

通过使用 Android Marshmallow 新的“确认凭据”  功能，可以根据设备的解锁时间对用户进行身份验证，使用户无需记住和输入特定于应用的密码。
为此，请使用 `KeyGenerator` 的新 `SetUserAuthenticationValidityDurationSeconds` 方法。 使用 `KeyGuardManager` 的 `CreateConfirmDeviceCredentialIntent` 方法在应用中重新对用户进行身份验证。 有关 Android Marshmallow 中的这一新功能的详细信息，请参阅[确认凭据](https://developer.android.com/preview/api-overview.html#confirm-credential)。

Xamarin 提供了一个示例应用，演示如何在应用中使用设备凭据（如 PIN、模式或密码）：[ConfirmCredential](/samples/xamarin/monodroid-samples/android-m-confirmcredential)

使用此示例应用：

1. 在设备上设置安全锁屏（“安全”>“安全性”>“锁屏”  ）。
2. 点击“购买”  按钮，并确认安全锁屏凭据。

### <a name="chrome-custom-tabs"></a>Chrome 自定义选项卡

当用户点击某个 URL 时，应用开发人员可以选择：应用程序要么启动浏览器，要么使用基于 `WebView` 的应用内浏览器。 这两个选项都面临着很大的难题 &ndash; 启动浏览器是无法自定义的复杂上下文开关，而 `WebView` 不会与浏览器共享状态。 此外，使用 `WebView` 会增加额外的维护开销。 

Chrome 自定义选项卡  使你无需让用户离开应用即可轻松完美地显示具有 Chrome 功能的网站。 此功能可让应用更好地控制用户的 Web 体验；使本地内容和 Web 内容之间的转换更贴近无缝，而无需使用 `WebView`。 应用程序还可以通过自定义以下内容来影响 Chrome 的外观： 

- 工具栏颜色

- 进入和退出动画

- Chrome 工具栏和溢出菜单中的自定义操作

- Chrome 预启动和内容预提取（用于更快加载）

若要在 Xamarin.Android 应用中利用此功能，请下载并安装 [Android 支持自定义选项卡库](https://www.nuget.org/packages/Xamarin.Android.Support.CustomTabs/)。
有关此功能的详细信息，请参阅 [Chrome 自定义选项卡](https://developer.chrome.com/multidevice/android/customtabs)。

### <a name="material-design-support-library"></a>材料设计支持库

Android Lollipop 引入了[材料设计](https://www.google.com/design/spec/material-design/introduction.html)作为新设计语言来刷新 Android 体验（有关在 Xamarin.Android 应用中使用材料设计的信息，请参阅[材料主题](~/android/user-interface/material-theme.md)）。 通过 Android Marshmallow，Google 引入了 Android 设计支持库  ，使应用程序开发人员可以更轻松地采用材料设计的外观。 此库包含以下组件：

- **CoordinatorLayout** &ndash; 新的 `CoordinatorLayout` 小组件类似于 `FrameLayout`，但功能更强大。 可以使用 `CoordinatorLayout` 作为子视图的容器或作为顶级布局，并且它提供可用于相对于其他视图瞄定视图的 `layout_anchor` 属性。

- **折叠工具栏** &ndash; 新的 `CollapsingToolbarLayout` 是一个折叠应用程序栏，它是 `Toolbar` 的包装器。 （请注意，应用栏  之前称为操作栏  。）

- **浮动操作按钮** &ndash; 一个表示应用界面上的主要操作的圆形按钮。

- **用于编辑文本的浮动标签** &ndash; 使用新的 `TextInputLayout` 小组件（包装 `EditText`）在用户输入文本的情况下隐藏提示时显示浮动标签。

- **导航视图** &ndash; 新的 `NavigationView` 小组件可帮助你以更便于用户导航的方式使用导航抽屉。

- **Snackbar** &ndash; 新的 `SnackBar` 小组件是一种轻型反馈机制（类似于 toast），它在屏幕底部显示一条简短消息，显示在屏幕的所有其他元素的上方。

- **材料选项卡** &ndash; 新的 `TabLayout` 小组件提供水平布局，以便在应用中以实现顶级导航的方式显示选项卡。

若要利用 Xamarin.Android 应用中的[设计支持库](https://developer.android.com/tools/support-library/features.html#design)，请下载并安装 Xamarin [Xamarin 支持库设计](https://www.nuget.org/packages/Xamarin.Android.Support.Design/) NuGet 包。

有关在 Xamarin.Android 应用中使用材料设计支持库的更多详细信息（包括代码示例），请参阅[带有 Android 支持设计库的精美材料设计](https://blog.xamarin.com/add-beautiful-material-design-with-the-android-support-design-library/)。
Xamarin 提供了一个示例应用，演示了 Xamarin.Android 上的新 Android 设计库 &ndash; [Cheesesquare](/samples/xamarin/monodroid-samples/android50-cheesesquare)。
此示例演示设计库的以下功能：

- 折叠工具栏
- 浮动操作按钮
- 视图锚定
- NavigationView
- Snackbar

有关设计库的详细信息，请参阅 Android 开发人员博客中的 [Android 设计支持库](https://android-developers.googleblog.com/2015/05/android-design-support-library.html)。

### <a name="additional-library-updates"></a>其他库更新

除了 Android Marshmallow，Google 还发布了几个核心 Android 库的相关更新。 Xamarin 通过多个预览版 NuGet 包为这些更新提供 Xamarin.Android 支持： 

- [Google Play Services](https://www.nuget.org/packages?q=Xamarin+Google+Play+Services) &ndash; 最新版本的 Google Play Services 包括新的“应用邀请”  功能，使用户可以与朋友共享应用。 有关此功能的详细信息，请参阅[通过 Google 的应用邀请扩展应用的范围](https://blog.xamarin.com/expand-your-apps-reach-with-googles-app-invites/)。 

- [Android 支持库](https://www.nuget.org/packages?q=xamarin+support+library) &ndash; 这些 NuGet 提供的功能仅适用于库 API，同时提供了向后兼容的 Android 框架 API 版本。 

- [Android 可穿戴库](https://www.nuget.org/packages/Xamarin.Android.Wear) &ndash; 此 NuGet 包含 Google Play Services 绑定。 最新版本的可穿戴库为 Android Wear 平台提供了新功能（包括更简单的自定义应用程序导航）。 

## <a name="summary"></a>总结

本文介绍了 Android Marshmallow，并说明如何安装和配置最新的工具和包，以便在 Marshmallow 上进行 Xamarin.Android 开发。 它还概述了 Xamarin.Android 开发中最激动人心的新 Android Marshmallow 功能。

## <a name="related-links"></a>相关链接

- [Android 6.0 Marshmallow](https://developer.android.com/about/versions/marshmallow/index.html)
- [获取 Android SDK](https://developer.android.com/sdk/index.html#Other)
- [功能概述](https://developer.android.com/preview/api-overview.html)
- [发行说明](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1.99/index.md)
- [RuntimePermissions（示例）](/samples/xamarin/monodroid-samples/android-m-runtimepermissions)
- [ConfirmCredential（示例）](/samples/xamarin/monodroid-samples/android-m-confirmcredential)
- [FingerprintDialog（示例）](/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)