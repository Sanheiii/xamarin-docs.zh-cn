---
title: macOS Sierra 简介
description: 本文介绍了适用于 Xamarin 开发人员 macOS Sierra 的所有新的和修改的 Api 和功能。
ms.prod: xamarin
ms.assetid: 71A8A737-F310-4320-BD23-743AA1E9033C
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 8715559db3f551773f41659a0c7c36adb29f0d1e
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91437228"
---
# <a name="introduction-to-macos-sierra"></a>macOS Sierra 简介

使用新的 macOS Sierra，开发人员可以利用新的 Api，使最终用户能够在以前不可用的情况下与其应用和网站交互。 例如，Apple 现在允许网站为客户提供安全的选项：通过 Apple Pay 和对金属框架的增强功能，提升应用的图形和计算潜力。 

有关 macOS Sierra 的详细信息，请参阅 Apple 的 [macOS + Apps](https://developer.apple.com/macos/) 文档。

<a name="Whats-New-in-macOS-Sierra"></a>

## <a name="whats-new-in-macos-sierra"></a>macOS Sierra 中的新增功能

Apple 已在 macOS Sierra 中添加了几个新的 Api 和服务，并提供了对现有功能的许多增强功能，其中包括：

<a name="Apple-File-System"></a>

### <a name="apple-file-system"></a>Apple 文件系统

使用 macOS Sierra，Apple 已发布新的 Apple 文件系统作为 iOS、macOS、tvOS 和 watchOS 的新式文件系统。 Apple 文件系统经过优化，可用于闪存和 SSD 存储，并提供以下功能：强加密、写入时复制元数据、空间共享、文件和目录的克隆、快照、快速目录大小调整和原子安全保存基元。

有关详细信息，请参阅 Apple 的 [Apple 文件系统指南](https://developer.apple.com/library/prerelease/content/documentation/FileManagement/Conceptual/APFS_Guide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40016999)。

<a name="Apple-Pay-Enhancements"></a>

### <a name="apple-pay-enhancements"></a>Apple Pay 增强功能

Apple 已对 macOS Sierra 中的 Apple Pay 进行了多项改进，使用户能够从网站中进行安全的付款。

使用 macOS Sierra，添加了几个新的 Api，它们使用 macOS Sierra、iOS 和 watchOS 来支持动态支付网络和新的沙箱测试环境。

macOS Sierra 包括新的 ApplePay Javascript 框架，使开发人员能够将 Apple Pay 直接合并到 iOS 和 macOS 基于 Safari 的网站中。 对于支持 Apple Pay 的网站，用户可以使用其 iPhone 或 Apple Watch 来授权支付。

有关详细信息，请参阅 Apple 的 [APPLEPAY JS Framework](https://developer.apple.com/reference/applepayjs) 参考。

<a name="Building-Modern-macOS-Apps"></a>

### <a name="building-modern-macos-apps"></a>生成新式 macOS 应用

新式 macOS 应用（如 Apple 的 Safari web 浏览器、页面字处理程序和数字分散工作表）使用许多新技术来提供一个统一的上下文相关用户界面，该用户界面脱离了传统 UI 元素，如浮动面板和多个打开的窗口。

[![选项卡式 Mac 窗口的示例](images/content08.png)](images/content08.png#lightbox)

我们的 " [构建新式 MacOS 应用](~/mac/platform/introduction-to-macos-sierra/modern-cocoa-apps.md) " 指南介绍了开发人员在 Xamarin 中构建现代 macOS 应用时可以使用的几个提示、功能和技术。

<a name="CloudKit-Data-Sharing"></a>

### <a name="cloudkit-data-sharing"></a>CloudKit 数据共享

CloudKit 框架已在 macOS Sierra 中展开，使用户能够快速轻松地从其专用 iCloud 数据库共享记录或记录集。

CloudKit 提供了一个用于发送和接受共享记录邀请的完整 UI，并且用户具有对记录访问权限的人员的完全读/写控制。

有关详细信息，请参阅 Apple 的 [CloudKit Framework reference](https://developer.apple.com/reference/clockkit) 和 [CloudKit JS framework 参考](https://developer.apple.com/reference/cloudkitjs)。

> [!IMPORTANT]
> Apple [提供工具](https://developer.apple.com/support/allowing-users-to-manage-data/)，用于帮助开发人员正确处理欧盟一般数据保护条例 (GDPR)。

<a name="Safari-App-Extensions-Support"></a>

### <a name="safari-app-extensions-support"></a>Safari 应用扩展支持

Safari 应用扩展允许应用扩展 Safari web 浏览器的行为，同时与 macOS Sierra 紧密集成。 由于 macOS Safari 应用扩展的工作方式类似于 iOS Safari 应用扩展，因此可以轻松地从一个系统移植到另一个系统。

有关详细信息，请参阅 Apple 的 [Safari 应用扩展编程指南](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternetWeb/Conceptual/SafariAppExtension_PG/index.html#//apple_ref/doc/uid/TP40017319)。

<a name="Security-and-Privacy-Enhancements"></a>

### <a name="security-and-privacy-enhancements"></a>安全和隐私增强

Apple 在 macOS Sierra 中对安全和隐私进行了多项改进，有助于应用提高应用程序的安全性，并确保最终用户的隐私，其中包括以下内容：

- 新 `NSAllowsArbitraryLoadsInWebContent` 密钥可以添加到应用的 `Info.plist` 文件中，并允许在 Apple 传输安全 (ATS 的情况下正确加载网页，) 保护仍为应用的其余部分启用。
- 常见的数据安全体系结构 (CDSA) API 已弃用，应该替换为 SecKey API 以生成非对称密钥。
- 对于所有 SSL/TLS 连接，默认情况下，RC4 对称密码处于禁用状态。 此外，安全传输 API 不再支持 SSLv3，建议应用程序尽快停止使用 SHA-1 和3DES 加密。
- 由于 iOS 10 和 macOS Sierra 中的新剪贴板允许用户在设备之间进行复制和粘贴，因此已将 API 扩展为允许将剪贴板限制为特定设备，并在给定的时间戳自动清除。 此外，已命名的 pasteboards 不再保留，并且应替换为共享粘贴板容器。
- 如果应用访问受保护的数据 (例如用户的日历) ，则它 _必须_ 在 `Info.plist` 日历) 的情况下，将该意图声明为其文件中的正确用途字符串值键 (`NSCalendarUsageDescription` 。
- 尚未通过 Mac App Store 提供的开发人员签名应用现在可以利用 CloudKit、iCloud 密钥链、iCloud 驱动器、远程推送通知、MapKit 和 VPN 权利。
- macOS Sierra 不再支持在其 zip 存档或无符号磁盘映像中同时传递外部代码或数据，因为运行时路径在运行时之前是未知的。

此外，在 macOS Sierra (或) 更高版本的应用程序上运行的应用程序必须通过在其文件中输入一个或多个特定于隐私的密钥 `Info.plist` ，向用户说明应用希望获取访问权限的原因，以静态方式声明其意图以访问特定功能或用户信息。

由于 macOS Sierra 与 iOS 10 共享这些更改，请参阅我们的 iOS 10 [安全和隐私增强](~/ios/app-fundamentals/security-privacy.md) 指南以获取详细信息。

<a name="Smart-Card-Driver-Extension-Support"></a>

### <a name="smart-card-driver-extension-support"></a>智能卡驱动程序扩展支持

使用 macOS Sierra，应用程序可以创建 `NSExtension` 基于智能卡驱动程序，以允许从特定类型的智能卡对内容进行只读访问。 此信息随后会显示在系统密钥链中， (替换不推荐使用的通用数据安全体系结构方法) 。

有关详细信息，请参阅 Apple 的 [CryptoTokenKit 框架引用](https://developer.apple.com/reference/cryptotokenkit)Pleas。

<a name="Unified-Logging"></a>

### <a name="unified-logging"></a>统一日志记录

统一日志记录为应用程序提供了一个 API，用于跨所有系统级别有效地进行消息传送。 利用统一日志记录，应用程序可以精细地控制多个级别的日志记录，其中包括隐私控制和活动跟踪，以便更轻松地进行调试。 

当活动跟踪和日志记录一起使用时，日志记录会提供自动消息相关性。

macOS Sierra 包含一个新的控制台应用程序 (在应用程序/实用程序) 中，该应用程序能够显示多个源（包括连接的设备）中的日志数据。 它还支持标记化和保存的搜索，并显示跨多个进程的相关消息之间的连接。

此外，还可以使用命令行工具来查看和维护日志消息。

有关详细信息，请参阅 Apple 的 [日志记录引用](https://developer.apple.com/documentation/os/logging)。

<a name="Wide-Color"></a>

### <a name="wide-color"></a>宽域颜色

macOS Sierra 在整个系统中扩展了对扩展范围像素格式和宽色域颜色空间的支持，包括核心图形、核心图像、金属和 AVFoundation 等框架。 通过在整个图形堆栈中提供此行为，可进一步减轻对具有宽颜色显示的设备的支持。

此外，已将 `AppKit` 修改为在新的扩展 **sRGB** colorspace 中工作，从而可以更轻松地混合使用宽颜色 gamuts 的颜色，而不会显著降低性能。

当使用宽色时，Apple 提供以下最佳做法：

- `NSColor` 现在使用 sRGB 颜色空间，并且不再将值夹具到 `0.0` to `1.0` 范围。 如果应用依赖于以前的夹具行为，则需要对其进行修改以 macOS Sierra。
- 使用低级别 API （如核心图形或金属图）来提供图像处理时，应用应使用支持16位浮点值的扩展范围颜色空间和像素格式。 如果需要，应用必须手动固定颜色分量值。
- 核心图形、核心图像和金属绩效着色器都提供了在两个颜色空间之间进行转换的新方法。

若要了解详细信息，请参阅 [宽颜色指南简介](~/ios/platform/wide-color.md) 。

<a name="Additional-Framework-Changes"></a>

## <a name="additional-framework-changes"></a>其他框架更改

除了上面列出的主要框架更改和添加以外，Apple 还在 macOS Sierra 中进行了许多其他的次要框架更改。

若要了解详细信息，请参阅我们的 [其他框架更改](~/mac/platform/introduction-to-macos-sierra/additional-framework-changes.md) 指南。

<a name="Deprecated-APIs"></a>

## <a name="deprecated-apis"></a>弃用的 API

MacOS Sierra 中已弃用以下 Api：

- 不再支持 HFS 标准文件系统。

有关弃用功能和更改的完整列表，请参阅 Apple 的 [macOS v 10.12 API 差异](https://developer.apple.com/library/archive/releasenotes/General/APIDiffsMacOS10_12/index.html) 文档。

## <a name="related-links"></a>相关链接

- [Mac 示例](/samples/browse/?products=xamarin&term=Xamarin.Mac)
- [MacOS 10.12 中的新增功能](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)