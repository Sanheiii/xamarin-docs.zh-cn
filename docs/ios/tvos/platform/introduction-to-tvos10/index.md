---
title: tvOS 10 简介
description: 本文介绍了 tvOS 10 for Xamarin tvOS 开发人员提供的所有新的和修改的 Api 和功能。
ms.prod: xamarin
ms.assetid: CB9C1EC8-6008-43AD-977E-976AE7C73DD8
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 5d59646f111e5ac3f50de7319f8d5065492c9942
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434908"
---
# <a name="introduction-to-tvos-10"></a>tvOS 10 简介

_本文介绍了 tvOS 10 for Xamarin tvOS 开发人员提供的所有新的和修改的 Api 和功能。_

新的 tvOS 10 SDK Apple 提供了新的 Api 和服务，使开发人员能够创建新类别的应用和功能。 

有关 tvOS 10 的详细信息，请参阅 Apple 的 [tvOS + Apps](https://developer.apple.com/tvos/) 文档。

## <a name="whats-new-in-tvos-10"></a>TvOS 10 中的新增功能

Apple 在 tvOS 10 中添加了几个新的 Api 和服务，并提供了对现有功能的许多增强功能，其中包括：

## <a name="new-user-interface-styles"></a>新用户界面样式

tvOS 10 现在支持一个深色和浅色用户界面主题，所有内置 UIKit 控件都将根据用户的偏好自动调整。

创建和实现新的自定义 UI 控件时，开发人员应使用 [UITraitCollection](https://developer.apple.com/reference/uikit/uitraitcollection) 类来适应用户选定的主题。

有关详细信息，请参阅 [新的用户界面样式](~/ios/tvos/platform/user-interface-styles.md) 文档。

## <a name="security-and-privacy-enhancements"></a>安全和隐私增强

Apple 在 tvOS 10 中对安全和隐私进行了多项改进，有助于开发人员提高应用程序的安全性并确保最终用户的隐私。

因此，在 watchOS 3 (或) 更高版本上运行的应用程序必须通过在其文件中输入一个或多个特定于隐私的密钥来向用户提供一个或多个特定于隐私的密钥 `Info.plist` ，向用户说明应用希望获取访问权限的原因。

由于 tvOS 10 与 iOS 10 共享这些更改，请参阅我们的 iOS 10 [安全和隐私增强](~/ios/app-fundamentals/security-privacy.md) 指南以获取详细信息。

## <a name="video-subscriber-account"></a>视频订户帐户

对于 tvOS 10 的新功能，视频订户帐户框架允许支持经过身份验证的流式处理或视频点播的应用使用其有线或卫星电视提供商，为最终用户提供单一登录体验。

<!--To find out more, please see our [Video Subscriber Account](~/ios/platform-features/introduction-to-ios10/video-subscriber-account/) guide.-->

## <a name="wide-color"></a>宽域颜色

tvOS 10 在整个系统（包括核心图形、核心图像、金属和 AVFoundation 等框架）中扩展了对扩展范围像素格式和宽色域颜色空间的支持。 通过在整个图形堆栈中提供此行为，可进一步减轻对具有宽颜色显示的设备的支持。

此外，已将 `UIKit` 修改为在新的扩展 **sRGB** colorspace 中工作，从而可以更轻松地混合使用宽颜色 gamuts 的颜色，而不会显著降低性能。

当使用宽色时，Apple 提供以下最佳做法：

- `UIColor` 现在使用 sRGB 颜色空间，并且不再将值夹具到 `0.0` to `1.0` 范围。 如果应用依赖于以前的夹具行为，则需要对 tvOS 10 进行修改。
- 如果应用执行的自定义呈现 `UIImages` ，则使用新的 [UIGraphicsImageRender](https://developer.apple.com/reference/uikit/uigraphicsimagerenderer) 类指定扩展范围或标准范围格式的使用。
- 使用低级别 API （如核心图形或金属图）来提供图像处理时，应用应使用支持16位浮点值的扩展范围颜色空间和像素格式。 如果需要，应用必须手动固定颜色分量值。
- 核心图形、核心图像和金属绩效着色器都提供了在两个颜色空间之间进行转换的新方法。

若要了解详细信息，请参阅 [宽颜色指南简介](~/ios/platform/wide-color.md) 。

## <a name="newly-available-existing-frameworks"></a>新可用的现有框架

IOS (上提供的多个框架（而非 tvOS) ）可用于 tvOS 10，如：

- ExternalAccessory
- HomeKit
- MultipeerConnectivity
- 照片
- ReplayKit
- UserNotification

## <a name="additional-framework-changes"></a>其他框架更改

除了上面列出的主要框架更改和添加以外，Apple 还在 tvOS 10 中进行了许多其他的次要框架更改。

若要了解详细信息，请参阅我们的 [其他框架更改](~/ios/tvos/platform/introduction-to-tvos10/additional-framework-changes.md) 指南。

## <a name="deprecated-apis"></a>弃用的 API

TvOS 10 不推荐使用 Api 或框架。 有关 API 修改的完整列表，请参阅 Apple 的 [tvOS 10 Api 差异](https://developer.apple.com/library/prerelease/content/releasenotes/General/tvOS10APIDiffs/index.html) 文档。

## <a name="related-links"></a>相关链接

- [tvOS 示例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [TvOS 10 中的新增功能](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)