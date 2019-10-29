---
title: 在 iTunes Connect 中配置 tvOS 应用
description: 本文提供 iOS 补充指南，适用于 iOS 在 iTunes Connect 中配置应用以 tvOS 特定配置。
ms.prod: xamarin
ms.assetid: 86C7C5BD-C97D-4F1D-B611-A7694557BFDF
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 01ab48f68656dcabdf2a6cfc286dfcd8850454f8
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030764"
---
# <a name="configure-your-tvos-app-in-itunes-connect"></a>在 iTunes Connect 中配置 tvOS 应用

_本文提供 iOS 补充指南，适用于 iOS 在 iTunes Connect 中配置应用以 tvOS 特定配置。_

除了遵循 iOS 在[ITunes Connect guide 中配置应用](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)所需的配置和设置外，本文档还介绍了在 Apple TV 应用中发布 tvOS 应用程序所需的特定配置。店.

<a name="Adding-a-tvOS-Release-Version" />

## <a name="adding-a-tvos-release-version"></a>添加 tvOS 发行版本

无论是要创建要在 Apple TV 应用商店上发布的新应用，还是要向现有 iOS 应用添加 Apple TV 支持，都需要创建 iTunes Connect 记录并使用以下 iOS 特定指南进行配置：

- [创建 iTunes Connect 记录](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#creating)
- [管理应用视频和屏幕快照](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#managing)
- [管理名称、说明、新增功能、关键字和 URL](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#metadata)
- [维护一般信息](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#general)

或者，您可能还需要：

- [维护 Game Center 信息](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#game-center)
- [维护应用内购买信息](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#iap)

完成上述所有步骤后，打开应用的 iTunes Connect 记录，并选择使用左侧栏添加 tvOS 支持：

[![](itunes-connect-images/connect01.png "Add tvOS support using the left hand sidebar")](itunes-connect-images/connect01.png#lightbox)

然后，tvOS 特定的信息屏幕将可用于给定 iTunes Connect 记录：

[![](itunes-connect-images/connect02.png "The tvOS specific information screen")](itunes-connect-images/connect02.png#lightbox)

<a name="tvOS-Version-Information" />

## <a name="tvos-version-information"></a>tvOS 版本信息

在左侧边栏中，选择 " **1.0 TVOS 应用准备提交**" 部分：

[![](itunes-connect-images/connect03.png "tvOS Version Information")](itunes-connect-images/connect03.png#lightbox)

在此屏幕上提供以下信息：

- 所需的屏幕截图、说明、关键字和 Url。
- 常规应用信息，例如版本号、版权和年龄分级。
- 可选的应用内购买。
- 可选 Game Center 支持排行榜和成就。
- 必需的应用检查信息，如联系人、演示帐户和说明。

输入所需信息后，请单击屏幕右上角的 "**保存**" 按钮以保存更改：

[![](itunes-connect-images/connect04.png "tvOS Version Information ready for submission")](itunes-connect-images/connect04.png#lightbox)

<a name="Submitting-for-Review" />

## <a name="preparing-to-submit-for-review"></a>正在准备提交以供审阅

最终准备好将 tvOS 应用提交到 Apple TV App Store 进行查看时，请返回到应用的 iTunes Connect 记录，并单击屏幕右上角的 "**提交以审阅**" 按钮：

[![](itunes-connect-images/connect05.png "Submit for Review")](itunes-connect-images/connect05.png#lightbox)

<a name="Summary" />

## <a name="summary"></a>总结

本文提供 iTunes Connect 中的 tvOS 特定设置的概述，以便将 tvOS 应用程序发布到 Apple TV 应用商店。

## <a name="related-links"></a>相关链接

- [tvOS 示例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人体学接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [TvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
