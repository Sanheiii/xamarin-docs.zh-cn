---
title: Xamarin 中的应用内购买
description: 本文档介绍了如何使用 StoreKit Api 销售数字产品和服务。 它链接到讨论配置、可耗用产品、非可耗用产品、事务、订阅等的指南。
ms.prod: xamarin
ms.assetid: B41929D8-47E4-466D-1F09-6CC3C09C83B2
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 7e061f12e45e6d60c1772fc8699ffdb1f1b1d730
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430397"
---
# <a name="in-app-purchasing-in-xamarinios"></a>Xamarin 中的应用内购买

iOS 应用程序可以使用 StoreKit 销售数字产品或服务– iOS 提供的一组 Api，可与 Apple 的服务器通信，通过其 Apple ID 向用户执行财务交易。 StoreKit Api 主要涉及检索产品信息和执行事务–没有用户界面组件。 实现应用内购买的应用程序必须构建自己的用户界面，并通过自定义代码跟踪购买的项目，为用户提供所需的产品或服务。

提供应用内购买功能需要执行许多步骤：

- **配置应用** –必须正确设置应用程序的预配配置文件。
- **创建产品** –必须在 iTunes Connect 门户中创建产品描述和价格。
- **实现 StoreKit** –必须根据要销售的产品类型来实现 StoreKit API。
- **构建用户界面和产品本身** –必须实现产品，其中包括跟踪每个购买和备份/还原它们的机制（如果适用）。
- **监视销售和接收资金** –使用 iTunes Connect 提供的信息来监视销售趋势和跟踪收入。

本文档介绍了如何完成所有这些步骤，以使用 Xamarin 提供应用内购买。

## <a name="requirements"></a>要求

若要支持应用内购买，必须使用 Xcode 7 及更高版本的 Xamarin 5.0 或更高版本。

## <a name="contents"></a>目录

- [应用内购买基本知识和配置](~/ios/platform/in-app-purchasing/in-app-purchase-basics-and-configuration.md)

- [StoreKit 概述和检索产品信息](~/ios/platform/in-app-purchasing/store-kit-overview-and-retreiving-product-information.md)

- [购买易耗型产品](~/ios/platform/in-app-purchasing/purchasing-consumable-products.md)

- [购买非易耗型产品](~/ios/platform/in-app-purchasing/purchasing-non-consumable-products.md)

- [事务和验证](~/ios/platform/in-app-purchasing/transactions-and-verification.md)

- [订阅和报告](~/ios/platform/in-app-purchasing/subscriptions-and-reporting.md)

## <a name="summary"></a>总结

本文介绍了应用内购买的概念，其中概述了如何配置应用程序以利用它并使用 Xamarin 提供的示例。 其中包括：

- **IOS 预配门户** –启用应用内购买功能的指导原则。
- **ITunes Connect** –配置要在应用中销售的产品。
- **应用商店工具包** –对用于构建应用内购买功能的类的说明。
- **编写应用程序的采购代码** -如何将应用内购买构建到 Xamarin iOS 应用中的示例。
- **报告** –有关通过 iTunes Connect 提供的统计信息的概述。

## <a name="related-links"></a>相关链接

- [InAppPurchaseSample](/samples/xamarin/ios-samples/storekit/)
- [应用购买编程指南](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/StoreKitGuide/Introduction.html)
- [iTunes Connect 开发人员指南](https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/iTunesConnect_Guide.pdf)
- [应用商店工具包框架参考](https://developer.apple.com/library/ios/documentation/StoreKit/Reference/StoreKit_Collection/StoreKit_Collection.pdf)
- [应用内购买产品标识符 Q&](https://developer.apple.com/library/ios/#qa/qa1329/_index.html)
- [应用内购买技术说明](https://developer.apple.com/library/ios/#technotes/tn2259/_index.html)
- [首次应用商店提交](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)
- [App Store 提交提示](https://developer.apple.com/appstore/resources/submission/tips.html)
- [App Store 审核指南](https://developer.apple.com/appstore/resources/approval/guidelines.html)
- [管理你的应用](https://developer.apple.com/appstore/resources/managing/index.html)