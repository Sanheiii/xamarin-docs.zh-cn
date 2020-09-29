---
title: tvOS 9 简介
description: 本文介绍了 tvOS 9 for Xamarin tvOS 开发人员提供的所有新的和修改的 Api 和功能。
ms.prod: xamarin
ms.assetid: A7E738E1-9F94-489B-918F-7DF8F0810987
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/07/2016
ms.openlocfilehash: 552a163636c85b13be64a0613b1523263deb5e92
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91437034"
---
# <a name="introduction-to-tvos-9"></a>tvOS 9 简介

_本文介绍了 tvOS 9 for Xamarin tvOS 开发人员提供的所有新的和修改的 Api 和功能。_

Apple 已发布第四代 Apple TV 硬件，其中包含经过重新设计、触摸启用的远程远程，运行基于 iOS 9) 的新 tvOS 操作系统 (。

第一次，tvOS 向开发人员开放 Apple TV 平台，使用户能够创建丰富的沉浸式应用程序，并在类似于使用 iTunes 应用商店编写和发布适用于 iOS 的应用的过程中，通过 Apple TV 的内置应用商店发布这些应用。

如果你熟悉 Xamarin iOS 开发，你应发现过渡到 tvOS 的方式非常简单。 大多数 Api 和功能都是相同的，但是，许多常见 Api 不可用 (例如 WebKit) 。 此外，使用 Siri 远程处理会引发一些设计挑战，这些挑战在基于触摸屏的 iOS 设备中不存在。

本指南将介绍 tvOS 9 中适用于 tvOS 开发人员的所有新的和修改的 Api 和功能。 有关 tvOS 的详细信息，请参阅 Apple [针对新的 APPLE TV 文档开发](https://developer.apple.com/tvos/) 。

<a name="Supported-and-Unsupported-Capabilities"></a>

## <a name="supported-and-unsupported-capabilities"></a>支持和不支持的功能

Apple TV 上运行的 tvOS 应用具有以下支持的功能和功能：

- 应用组
- 后台模式
- 数据保护
- Game Center
- 游戏控制器
- iCloud
- 应用内购买
- Keychain 共享

不支持以下特性和功能：

- Apple Pay
- 应用沙盒
- 关联的域
- HealthKit
- HomeKit
- 应用间音频
- Maps
- 个人 VPN
- 推送通知
- Wallet
- 无线附件配置

有关详细信息，请参阅 [支持的程序集](~/ios/tvos/internals/assemblies.md) 和 [支持的框架](~/ios/tvos/internals/frameworks.md) 文档。

<a name="Apple-TV-Hardware"></a>

## <a name="apple-tv-hardware"></a>Apple TV 硬件

新的 Apple TV 具有以下硬件规范：

- 64位 A8 处理器
- 存储的32GB 或64GB
- 2GB RAM
- 10/100Mbps 以太网
- WiFi 802.11 a/b/g/n/ac
- 1080p 解析
- HDMI
- 用于开发人员和诊断的 USB C 端口 (仅使用) 
- 基于区域) 的新 Siri 远程或 Apple TV 远程 (

### <a name="siri-remote"></a>Siri 远程

根据区域，提供的 Apple TV 遥控器将进入两种配置： Siri 远程或 Apple TV 远程。

当前在以下国家/地区提供 Siri 远程：

- 澳大利亚
- 加拿大
- 法国
- 德国
- 日本
- 西班牙
- 英国
- 美国

所有其他国家/地区将收到使用 "搜索" 按钮替换 "Siri" 按钮的 Apple TV 遥控器，该搜索按钮显示带有文本输入的默认搜索屏幕：

[![Siri 远程](tvos9-images/remote02.png)](tvos9-images/remote02.png#lightbox)

有关详细信息，请参阅我们的 [Siri 远程和蓝牙控制器](~/ios/tvos/platform/remote-bluetooth.md) 文档。

<a name="Apple-TV-Provisioning"></a>

## <a name="apple-tv-provisioning"></a>Apple TV 预配

就像为 iOS 进行开发一样，新 tvOS 将需要正确的预配配置文件，用于开发和分发，这两项功能都基于你已使用 Apple 建立的团队成员身份和签名标识。

若要访问 tvOS 功能（如 iCloud KVS 或 CloudKit 数据存储），也必须进行适当的设置。 请参阅我们的 [资源和数据存储](~/ios/tvos/app-fundamentals/resources-data-storage.md) ，了解有关在 tvOS 应用中支持 iCloud 的信息。

预配配置文件的创建和安装方式与使用 Xamarin iOS 应用的方式相同。 同样，请参阅 iOS [设备预配](~/ios/get-started/installation/device-provisioning/index.md) 文档以了解更多详细信息。

<a name="Apple-TV-Apps"></a>

## <a name="apple-tv-apps"></a>Apple TV 应用

新的 Apple TV 硬件和 tvOS 9 支持两种类型的应用：传统应用和客户端-服务器应用。

<a name="Traditional-Apps"></a>

### <a name="traditional-apps"></a>传统应用

传统应用从 Apple TV 应用商店购买，并直接安装在设备上。 这些应用可以是使用与 Xamarin iOS 应用相同的框架和技术开发的游戏、实用程序或媒体应用程序。

Apple TV 应用的最大大小为200MB，可以使用点播资源下载其他2GB 的内容。 有关详细信息，请参阅我们的 [资源和数据存储](~/ios/tvos/app-fundamentals/resources-data-storage.md) 。

请参阅 [tvOS 快速入门指南，](~/ios/tvos/get-started/hello-tvos.md) 熟悉使用 tvOS 开发 tvOS 应用程序所需的工具和概念。

<a name="Summary"></a>

### <a name="client-server-apps"></a>客户端-服务器应用

除了已安装的传统应用外，通过 Apple TV，还可以轻松地使用 web 技术 (HTTPS、XML 和 JavaScript) 创建基于 web 的客户端-服务器媒体流式处理应用。 你将使用 Apple 的 TVML 标记语言设计用户界面，并使用 JavaScript 通过 TVMLKit 定义应用的行为。

有关详细信息，请参阅 apple 的 [APPLE Tv 标记语言参考](https://developer.apple.com/library/prerelease/tvos/documentation/LanguagesUtilities/Conceptual/ATV_Template_Guide/index.html#//apple_ref/doc/uid/TP40015064)， [TVJS framework 参考](https://developer.apple.com/library/prerelease/tvos/documentation/TVMLJS/Reference/TVJSFrameworkReference/index.html#//apple_ref/doc/uid/TP40016076)， [TVMLKit FRAMEWORK 参考](https://developer.apple.com/library/prerelease/tvos/documentation/TVMLKit/Reference/TVMLKit_Collection/index.html#//apple_ref/doc/uid/TP40016429)， [关于](https://developer.apple.com/library/prerelease/tvos/referencelibrary/GettingStarted/AboutHTTPLiveStreaming/about/about.html#//apple_ref/doc/uid/TP40013978) [apple TV 文档的 HTTP Live Streaming 和 HLS 创作规范](https://developer.apple.com/services-account/download?path=/Documentation/HLS_Authoring_Specification_for_Apple_TV/HLS_Authoring_Specification_for_Apple_TV.pdf) 。

<a name="User-Interface-Challenges"></a>

## <a name="user-interface-challenges"></a>用户界面问题

与 iOS 或 OS X 不同，Apple TV 没有允许用户直接选择应用或其内容的触摸屏或鼠标。 相反，他们将新的 Siri 远程或蓝牙游戏控制器用于导航应用程序的用户界面。 有关详细信息，请参阅我们的 [Siri 远程和蓝牙控制器](~/ios/tvos/platform/remote-bluetooth.md) 文档。

此外，总体用户体验明显不同于通常是单用户体验的 iOS 或 Mac 应用。 使用 Apple TV，用户体验趋向于性质更高的社交领域，其中有多个人在沙发上与单个应用程序交互。 若要在新应用 (或移植现有应用程序时，设计成功的 Apple TV 应用体验) ，必须考虑这些更改。 

<a name="Summary"></a>

### <a name="working-with-focus-and-parallax-images"></a>使用焦点和视差图像

如上所述，你的 tvOS 应用程序的用户将不会与 iOS 直接交互，因为这种情况下，它在设备的屏幕上点击图像，但使用 Siri 远程从房间上间接地进入。 为了提供和处理此用户交互，Apple TV 使用基于焦点的模型。 

当重点变化时，将使用较小的动画和效果 (如图像上的视差效果) 用于清楚地识别当前具有焦点的用户界面项。

如果用户在 Siri 遥控器上发出缓慢的循环手势，则该聚焦项会实时地响应此移动。 在 sway 发生时，会将发光的 sheen 应用于其图像，使表面看起来像是发光。 在给定的非活动状态之后，任何焦点不变的内容会变暗，并且重点项会增长得更大。

有关详细信息，请参阅使用 [导航和焦点](~/ios/tvos/app-fundamentals/navigation-focus.md) 和使用 [图标和图像](~/ios/tvos/app-fundamentals/icons-images.md) 文档。

<a name="The-Home-Screen"></a>

### <a name="the-home-screen"></a>主屏幕

Apple TV 主屏幕显示所有已安装的应用，并提供一种方法来访问用户首选项：

[![主屏幕](tvos9-images/home01.png)](tvos9-images/home01.png#lightbox)

用户使用 Siri 远程浏览应用图标的网格，并使用焦点来选择应用并启动应用。 在应用程序图标上，你第一次机会让你的潜在用户印象非常好，并应一目了然地传达你的应用程序的用途。

每个应用都必须同时提供小版本的应用图标。 安装应用程序时，将在 Apple TV 主屏幕上使用小图标。 应用商店使用大版本。 大型应用图标应模拟小图标版本的外观。

有关详细信息，请参阅 [使用图标和图像](~/ios/tvos/app-fundamentals/icons-images.md) 文档。

<a name="The-Top-Shelf"></a>

### <a name="the-top-shelf"></a>顶部货位

如果用户将 tvOS 应用放置在 Apple TV 主屏幕上的最上面一行，则当用户选择应用程序时，将显示一个大型的顶部图像。 此图像应突出显示应用的功能，或提供指向其内容的直接链接。

[![顶部货位](tvos9-images/topshelf01.png)](tvos9-images/topshelf01.png#lightbox)

顶部货位图像可以作为单个静态 `.png` 或文件提供，也可以 `.lsr` 在运行时动态创建为可设定焦点的单个行。

它不会显示静态的顶层图像，而是包含动态行、可设定焦点的项或动态的滚动条带集。 这两种动态样式都允许您突出显示应用程序提供的内容或跳转到其最常用的功能。

有关详细信息，请参阅 [使用图标和图像](~/ios/tvos/app-fundamentals/icons-images.md) 文档和 Apple 的 [TVServices Framework 参考](https://developer.apple.com/library/prerelease/tvos/documentation/TVServices/Reference/TVServices_Ref/index.html#//apple_ref/doc/uid/TP40016412) ，了解有关将顶层扩展添加到应用的详细信息，以提供动态的顶层内容。

## <a name="related-links"></a>相关链接

- [tvOS 示例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人体学接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [TvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
- [用 Xamarin (视频构建适用于 tvOS 的应用) ](https://university.xamarin.com/lightninglectures/tvos-with-xamarin)