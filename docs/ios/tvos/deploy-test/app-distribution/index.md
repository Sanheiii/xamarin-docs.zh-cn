---
title: tvOS 应用分发概述
description: 本文档概述适用于 tvOS 应用的分发技术，并将其用作指向主题中更详细文档的指针。
ms.prod: xamarin
ms.assetid: D5E0F446-C083-4E21-9788-FC84D32D00C4
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: a9e555c6e4713c4d34f4e14ea29164a769bc3db9
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435636"
---
# <a name="tvos-app-distribution-overview"></a>tvOS 应用分发概述

_本文档概述适用于 tvOS 应用的分发技术，并将其用作指向主题中更详细文档的指针。_

开发完 tvOS 应用后，软件开发生命周期中的下一步是将应用分发给用户，如下面的关系图中突出显示的部分所示：

[![软件开发生命周期概述](images/publishingdiagram.png)](images/publishingdiagram.png#lightbox)

Apple 提供以下方法来分发 tvOS 应用，这些应用由 tvOS 支持：

1. [**App Store**](#Apple-TV-App-Store-Distribution)
2. [**内部 (Enterprise) **](#In-House-Distribution) 
3. [**即席**](#Ad_Hoc_Distribution) 

所有这些情况都要求使用相应的配置文件** 预配应用程序。 配置文件是一种文件，其中包含了代码签名信息，以及应用程序标识和预期分发机制。 对于非 App Store 分发，该文件还包含可部署应用的设备的相关信息。

<a name="Apple-TV-App-Store-Distribution"></a>

## <a name="apple-tv-app-store-distribution"></a>Apple TV 应用商店分发

这是将 tvOS 应用分发到 Apple TV 设备上的使用者的主要方式。 提交到 Apple TV 应用商店的所有应用都需要 Apple 批准。

应用通过名为 iTunes Connect** 的门户提交到 App Store。 有关以下主题的信息，请参阅 [ITunes Connect guide 中的 "配置应用程序](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md) "：

- 管理协议、税款和银行。
- 创建 iTunes Connect 记录。
- 管理应用视频和屏幕截图。
- 管理应用名称、说明、新增功能、关键字和 Url。
- 维护常规应用信息。
- 维护 Game Center 信息。
- 维护应用检查信息。
- 维护定价信息。
- 维护应用内购买信息。
- 查看应用程序评论。

本文提供了有关如何设置和使用此门户来准备要在 Apple TV 应用商店中发布的应用程序的信息。 无论你是在设置 iOS 还是 tvOS 应用程序，都需要执行许多相同的步骤。

完成上面列出的所有步骤后，请参阅 [在 ITunes Connect 中配置 TvOS 应用](~/ios/tvos/deploy-test/app-distribution/itunes-connect.md) ，以添加所需的 tvOS 应用特定信息。

务必注意，只有属于 **Apple 开发人员计划**的开发人员有权访问 iTunes Connect。 **Apple 开发人员企业计划**成员没有访问权限。

如果在将 tvOS 应用提交到 Apple TV 应用商店时遇到问题，请参阅 [故障排除](~/ios/tvos/troubleshooting.md) 指南。 它包含一些你可能会遇到的已知问题，以及如何在 tvOS 中解决这些问题。

有关详细信息，请访问 [APPLE TV 应用商店](~/ios/tvos/deploy-test/app-distribution/app-store-publishing.md) 指南中的发布。

<a name="In-House-Distribution"></a>

## <a name="in-house-distribution"></a>In-House 分发

In-House 分发（有时称为企业分发**）允许 **Apple 开发人员企业计划**成员在内部向同一组织内的其他成员分发应用。 In-House 分发的优势在于无需 App Store 审核，且没有可安装应用程序的设备数量限制。 但是，值得注意的是，**Apple 开发人员企业计划**成员**没有**权限访问 iTunes Connect，因此许可证持有人负责分发应用。

若要详细了解如何获取设置以及如何在内部分发应用程序，请参阅 [内部分发指南](~/ios/deploy-test/app-distribution/in-house-distribution.md)。 本文档特定于 iOS，但使用相同的技术来进行 tvOS 应用。

<a name="Ad_Hoc_Distribution"></a>

## <a name="ad-hoc-distribution"></a>Ad Hoc 分发

TvOS 应用可通过 ad hoc 分发进行用户测试，可在 **Apple 开发人员计划**和 **Apple developer Enterprise 计划**中获得，最多可测试 100 Apple TV 设备。 当 iTunes Connect 不是一种选择时，适用于 ad hoc 分发的最佳用例是在公司内分发的。

若要详细了解如何获取设置以及如何在内部分发应用程序，请参阅 [即席分发指南](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)。 同样，此文档特定于 iOS，但使用相同的技术来 tvOS 应用。

<a name="Summary"></a>

## <a name="summary"></a>总结

本文简要概述了适用于 tvOS 应用的分发机制。 它引入了 Apple TV 应用商店、即席部署和内部部署，并提供了更多详细信息的链接。

## <a name="related-links"></a>相关链接

- [tvOS 示例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人体学接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [TvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)