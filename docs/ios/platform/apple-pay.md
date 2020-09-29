---
title: Xamarin 中的 Apple Pay
description: 本指南探讨了如何设置用于 Apple Pay 的 Xamarin iOS 环境，以便通过应用支付实物、娱乐和成员资格。 它包括有关所需标识符、证书和权利的信息。
ms.prod: xamarin
ms.assetid: A25AE660-B145-465F-9CCE-8D82BFD614C6
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/05/2017
ms.openlocfilehash: 51a18042bea820221f450d8d7ceedf3f69359a6d
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431629"
---
# <a name="apple-pay-in-xamarinios"></a>Xamarin 中的 Apple Pay

_本指南探讨了如何设置用于 Apple Pay 的 Xamarin iOS 环境，以便通过应用支付实物、娱乐和成员资格。它包括有关所需标识符、证书和权利的信息。_

Apple Pay 随 iOS 8 一起引入，使用户能够通过其 iOS 设备为物品（如食物、娱乐和成员资格）付费。 它在 iPhone 6 和 iPhone 6 Plus 上可用，还可以与用于商店内购买的 Apple Watch 配对。 当在 iPhone 上使用时，它将使用触控 ID 作为向用户的信用卡或借记卡确认和授权交易的一种方法。

## <a name="requirements"></a>要求

Apple Pay 仅在 iOS 8 及更高版本中可用，因此至少需要 Xcode 6。

将 Apple Pay 集成到你的应用程序中时也需要以下各项：

- 支付处理器平台
- 商家标识符
- Apple Pay 证书
- Apple Pay 权利

本文档将更详细地介绍这些项。

## <a name="differences-between-apple-pay-and-iap"></a>Apple Pay 与 IAP 之间的差异

Apple Pay 和 *应用内购买* (IAP) 的主要区别在于他们销售的产品。 *实物* 货物是通过 Apple Pay 销售的;食物、设施和身体娱乐 (例如电影入场券) ，都是这样的例子。 与此相反，IAP 销售 *虚拟* 商品（如高级或其他内容和订阅），还应考虑流式处理服务的更多月份或游戏中的额外生活。

所使用的框架也是主要差异; [PassKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/) 用于 Apple Pay， [StoreKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/) 为 IAP 提供框架 API。

使用 Apple Pay，Apple [指出](https://developer.apple.com/apple-pay/Getting-Started-with-Apple-Pay.pdf) 它 "[不] 对用户、商家或开发人员使用 Apple Pay 付款" 进行收费。 相比之下，每个事务的 IAP 的费用为30%。 而且，使用 Apple Pay，事务根本不会通过 Apple，而是通过付款平台。

## <a name="using-a-payment-processor-platform"></a>使用付款处理器平台

Apple Pay 的基本部分之一是处理付款。 尽管可以自行执行此操作，但它需要了解有关加密的重要知识，如 Apple [支付处理指南](https://developer.apple.com/library/ios/ApplePay_Guide/ProcessPayment.html)中所述。
另一方面，支付处理平台会为你处理这些操作，使你能够集中精力构建你的应用程序。

两个选项包括：

- **Stripe** -在 [Stripe.com](https://stripe.com/) 上注册以访问其 api。

- **JudoPay** -查看 [Github 上的 Xamarin 示例代码](https://github.com/Judopay/Xamarin-Sample-App)，并在 [JudoPay.com](https://www.judopay.com/)上注册。

## <a name="provisioning-for-apple-pay"></a>Apple Pay 的设置

若要将应用配置为使用 Apple Pay 需要在 Apple 开发人员门户和应用内安装。 为 Apple 支付成功预配你的应用时，应遵循以下几个步骤：

1. 创建商家 ID：
    - 请按照[此处](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#merchantid)的步骤操作
2. 使用应用付费功能创建应用 ID，并将其添加到其中：
    - 请按照[此处](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#appid)的步骤操作
3. 为商家 ID 生成证书：
    - 请按照[此处](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#certificate)的步骤操作
4. 使用新创建的应用 ID 生成预配配置文件：
    - 请按照[此处](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioning)的步骤操作
5. 添加 Apple Pay 权利：
    - 选择[此处](~/ios/deploy-test/provisioning/entitlements.md)详细介绍的 Apple 支付权限，或从[此处](~/ios/deploy-test/provisioning/entitlements.md)将密钥/值对手动添加到文件

## <a name="working-with-apple-pay"></a>使用 Apple Pay

Apple 已在 iOS 10 中对 Apple Pay 进行了多项改进，使用户能够从网站中进行安全付款，并通过 Siri 和地图进行交互。

IOS 10 中添加了几个新的 Api，它们适用于 iOS 和 watchOS 以支持动态支付网络和新的沙箱测试环境。

### <a name="apple-pay-website-integration"></a>Apple Pay 网站集成

开发人员可以在 iOS 10 中使用 **APPLEPAY JS**将 Apple Pay 直接合并到他们的网站中。 如果用户在 iOS 或 macOS 中使用 Safari 浏览网站，则可以通过在其 iPhone 或 Apple Watch 上验证交易来支付 Apple Pay 支付。 有关详细信息，请参阅 Apple 的 [APPLEPAY JP Framework 参考](https://developer.apple.com/reference/applepayjs)。

### <a name="passkit-framework-enhancements"></a>PassKit 框架增强功能

在 iOS 10 中，已将 PassKit 框架扩展为支持外的 Apple Pay， `UIKit` 以允许卡颁发商在其应用中提供自己的卡。

#### <a name="supporting-apple-pay-outside-of-uikit"></a>UIKit 之外的支持 Apple Pay

通过使用 [PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller) 和 [PKPaymentAuthorixationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate)，应用可以支持 [PKPaymentAuthorizationViewController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationviewcontroller) 提供的同一功能，而无需使用 UIKit。 虽然这种新的 API 是支持 Apple Watch (上的 Apple Pay 的必需的，但) 在其他情况下，它是可选的，但在其他情况下，它是可选的， (的现有应用程序) 。 但是，Apple 建议尽快迁移到新的 API，以便在所有开发人员的应用程序中提供广泛的 Apple Pay 支持，只需要一个基本代码。 有关意向和 Siri 集成的详细信息，请参阅 [SiriKit 文档简介](~/ios/platform/sirikit/index.md) 。

#### <a name="presenting-issuer-cards-from-within-apps"></a>在应用内展示颁发者卡

使用 iOS 10，PassKit 框架中已添加了新功能，使卡颁发商可以在自己的应用中提供其卡。 开发人员可以将 `PKPaymentButtonTypeInStore` UIButton 添加到应用程序的用户界面，该界面将显示卡片 Apple Pay 按钮。

`PresentPaymentPass`还可以使用[PKPassLibrary](https://developer.apple.com/reference/passkit/pkpasslibrary)类的方法以编程方式显示卡。

### <a name="new-payment-network-support"></a>新的付款网络支持

IOS 10 的新功能，应用程序可在无需修改、重新编译应用程序并将其重新提交到应用商店时，自动支持新的付款网络。

类的新的 [AvailableNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1833288-availablenetworks) 方法 `PKPaymentNetwork` 允许应用程序在运行时发现用户设备上的可用网络。 此外， [SupportedNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1619329-supportednetworks) 属性已展开，以将付款提供程序的名称作为参数。 使用这些方法，应用程序可以自动支持支付提供商支持的任何网络。

有关详细信息，请参阅我们的 [Apple Pay 配置](~/ios/platform/apple-pay.md) 和 Apple [Apple Pay 指南](https://developer.apple.com/apple-pay/)。

### <a name="new-testing-environment"></a>新测试环境

使用 iOS 10，Apple 引入了新的测试环境，使开发人员能够直接在 iOS 设备上预配测试付款卡。 然后，这个新的测试环境会将加密的测试付款数据返回到应用程序。

若要启用新的测试环境，请执行以下操作：

1. 在 iTunes Connect 中创建新的测试 iCloud 帐户。
2. 用新的测试帐户登录到 iOS 设备。
3. 设置要在其中测试应用程序的所需区域。
4. 使用 " [Apple Pay 指南](https://developer.apple.com/apple-pay/) " 中的测试支付卡之一来支付。

> [!IMPORTANT]
> 通过切换 iCloud 帐户，该设备将自动切换到新的测试环境。 但是，在提交给 iTunes 应用商店之前，Apple 仍 **要求** 在生产环境中使用真实卡测试该应用。

## <a name="summary"></a>总结

本文探讨了在应用中使用 Apple Pay 所需的不同项。 我们介绍了如何创建商家 ID 以及如何在 **info.plist**中使用它，需要手动修改。

## <a name="related-links"></a>相关链接

- [应用内购买](~/ios/platform/in-app-purchasing/index.md)
- [PassKit 简介](~/ios/platform/passkit.md)
- [PassKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/)
- [Apple Pay](https://developer.apple.com/apple-pay/)
- [Emporium (示例) ](/samples/xamarin/ios-samples/ios9-emporium)