---
title: Xamarin macOS Sierra 疑难解答
description: 本文档提供了几个用于在 Xamarin 应用程序中处理 macOS Sierra 的故障排除提示。 与 Mac 应用商店、Apple Pay、二进制兼容性、CFNetwork、CloudKit 等相关的提示。
ms.prod: xamarin
ms.assetid: 323DD5EE-87CE-48E4-B234-1CF61B45A019
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 09/22/2016
ms.openlocfilehash: d3ec300cbda5d22a285c3f63d3281bde29b94024
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430062"
---
# <a name="xamarinmac---macos-sierra-troubleshooting"></a>Xamarin macOS Sierra 疑难解答

_本文提供了有关在 Xamarin for Mac 应用中使用 macOS Sierra 的几个疑难解答技巧。_

以下部分列出了在将 macOS Sierra 与 Xamarin 一起使用时可能会发生的一些已知问题以及这些问题的解决方案：

- [App Store](#App-Store)
- [Apple Pay](#Apple-Pay)
- [二进制兼容性](#Binary-Compatibility)
- [CFNetwork HTTP 协议](#CFNetwork-HTTP-Protocol)
- [CloudKit](#CloudKit)
- [核心映像](#CoreImage)
- [通知](#Notifications)
- [NSUserActivity](#NSUserActivity)
- [免费](#Safari)

<a name="App-Store"></a>

## <a name="app-store"></a>App Store

已知问题：

- 在沙盒环境中测试应用内购买时，身份验证对话框可能出现两次。
- 在沙盒环境中测试应用内购买的托管内容时，每次将应用程序置于前台时都会显示密码对话框，直到内容下载完成。

<a name="Apple-Pay"></a>

## <a name="apple-pay"></a>Apple Pay

如果在将新的信用卡添加到 Apple Pay 时输入了不正确的过期日期或安全代码 (CW) ，则将终止卡预配过程。

<a name="Binary-Compatibility"></a>

## <a name="binary-compatibility"></a>二进制兼容性

已知问题：

- 调用 `NSObject.ValueForKey` 将会 `null` 导致异常。
- 在 `NSURLSession` `NSURLConnection` TLS 握手期间，和都不能再用于 `http://` url。
- 如果应用在或方法中修改 superview 的几何，则它们可能会挂起 `ViewWillLayoutSubviews` `LayoutSubviews` 。
- 对于所有 SSL/TLS 连接，默认情况下，RC4 对称密码处于禁用状态。 此外，安全传输 API 不再支持 SSLv3，建议应用程序尽快停止使用 SHA-1 和3DES 加密。

<a name="CFNetwork-HTTP-Protocol"></a>

## <a name="cfnetwork-http-protocol"></a>CFNetwork HTTP 协议

`HTTPBodyStream`类的属性 `NSMutableURLRequest` 必须设置为未打开的流，因为 `NSURLConnection` `NSURLSession` 现在完全强制实施此要求。

<a name="CloudKit"></a>

## <a name="cloudkit"></a>CloudKit

长时间运行的操作将返回 _"您没有保存文件的权限"。_ ”错误。

<a name="CoreImage"></a>

## <a name="core-image"></a>核心映像

`CIImageProcessor`API 现在支持任意输入图像计数。 `CIImageProcessor` 将删除 macOS Sierra beta 1 中包含的 API。

<a name="Notifications"></a>

## <a name="notifications"></a>通知

使用通知内容扩展时，视图控制器未正确释放，并且可能导致在达到扩展内存限制时出现故障。

<a name="NSUserActivity"></a>

## <a name="nsuseractivity"></a>NSUserActivity

在提交操作之后， `UserInfo` 对象的属性 `NSUserActivity` 可能为空。 `BecomeCurrent` `NSUserActivity` 将对象显式调用为当前解决方法。

<a name="Safari"></a>

## <a name="safari"></a>Safari

WebGeolocation 要求 `https://` 在 iOS 10 和 macOS Sierra 上使用安全 () URL，以防止恶意使用位置数据。

## <a name="related-links"></a>相关链接

- [Mac 示例](/samples/browse/?products=xamarin&term=Xamarin.Mac)
- [MacOS 10.12 中的新增功能](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)