---
title: 排查用 Xamarin 构建的 tvOS 10 应用的问题
description: 本文提供了有关在 Xamarin 应用中使用 tvOS 10 的几个疑难解答技巧。 它介绍了与应用商店、二进制兼容性、CFNetwork HttpProtocol、CloudKit、Core Image、NSUserActivity 和 UIKit 相关的问题。
ms.prod: xamarin
ms.assetid: EA5564BB-C415-49A2-B70C-3DBF5E0F3FAB
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 65a76c0196b79a17f935f59902c8e6d2f9f25933
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434923"
---
# <a name="troubleshooting-tvos-10-apps-built-with-xamarin"></a>排查用 Xamarin 构建的 tvOS 10 应用的问题

以下部分列出了在 Xamarin 中使用 tvOS 10 时可能出现的一些已知问题，以及针对这些问题的解决方案：

- [App Store](#App-Store)
- [二进制兼容性](#Binary-Compatibility)
- [CFNetwork HTTP 协议](#CFNetwork-HTTP-Protocol)
- [CloudKit](#CloudKit)
- [核心映像](#CoreImage)
- [NSUserActivity](#NSUserActivity)
- [UIKit](#UIKit)

<a name="App-Store"></a>

## <a name="app-store"></a>App Store

已知问题：

- 在沙盒环境中测试应用内购买时，身份验证对话框可能出现两次。
- 在沙盒环境中测试应用内购买的托管内容时，每次将应用程序置于前台时都会显示密码对话框，直到内容下载完成。

<a name="Binary-Compatibility"></a>

## <a name="binary-compatibility"></a>二进制兼容性

已知问题：

- 调用 `NSObject.ValueForKey` 将会 `null` 导致异常。
- 当调用时，按名称引用字体 `UIFont.WithName` 会导致崩溃。
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

`CIImageProcessor`API 现在支持任意输入图像计数。 `CIImageProcessor` 将删除包含在 tvOS 10 beta 1 中的 API。

<a name="NSUserActivity"></a>

## <a name="nsuseractivity"></a>NSUserActivity

在提交操作之后， `UserInfo` 对象的属性 `NSUserActivity` 可能为空。 显式调用 `BecomeCurrent` NSUserActivity 的对象作为当前的解决方法。

<a name="UIKit"></a>

## <a name="uikit"></a>UIKit

已知问题：

- 更改对的背景外观的更改 `UINavigationBar` ， `UITabBar` 或 `UIToolBar` 可能导致布局传递来解析新的外观。 如果尝试在、或事件内修改这些外观 `LayoutSubviews` ， `UpdateConstraints` `WillLayoutSubviews` `DidUpdateSubviews` 会导致无限布局循环。
- 在 tvOS 10 中，调用 `RemoveGestureRecognizer` 对象的方法会 `UIView` 显式取消任何正在进行的笔势识别器。
- 呈现的视图控制器现在会影响状态栏的外观。
- tvOS 10 要求开发人员在进行 `base.AwakeFromNib` 子类化 `UIViewController` 和重写 `AwakeFromNib` 方法时调用。
- 使用自定义 `UIView` 子类在 `LayoutSubviews` 调用之前重写和更新布局的应用 `base.LayoutSubviews` 可能会在 tvOS 10 中触发无限布局循环。
- 指定给对象时，方向特定或 flippable 图像资产不进行翻转 `UIButton` 。

## <a name="related-links"></a>相关链接

- [tvOS 示例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [TvOS 10 中的新增功能](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)