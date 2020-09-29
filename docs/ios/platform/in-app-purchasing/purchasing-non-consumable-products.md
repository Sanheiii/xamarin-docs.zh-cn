---
title: 在 Xamarin 中购买不可耗用的产品
description: 本文档介绍了 Xamarin 中的非可耗用产品，这些产品是由用户购买的功能，无论是否在设备上都是无限期的。
ms.prod: xamarin
ms.assetid: 635D9CA2-6BCA-53E1-7B10-968029AA3493
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 78a4f3ae90b1b20e9fe3cd4164726fee34f38eb7
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435497"
---
# <a name="purchasing-non-consumable-products-in-xamarinios"></a>在 Xamarin 中购买不可耗用的产品

非可耗用产品由客户 "拥有"。 预期是，即使设备丢失或被盗，或购买新设备，他们也始终可以访问它们。 它们对于书籍、杂志问题、游戏级别、照片筛选器、"专业功能" 等都很有用。用户购买非使用产品后，就不必再为其付费。 如果你的代码意外允许他们尝试，StoreKit 将显示一条已购买的消息。

## <a name="non-consumable-products-sample"></a>不可耗用产品示例

[InAppPurchaseSample 代码](/samples/xamarin/ios-samples/storekit)包含一个名为*NonConsumables*的项目。 此代码示例演示如何使用照片筛选器作为示例来实现不可耗用的产品。 购买筛选器后，可以将其重新应用到照片中。 永远不需要重新购买。   

此系列屏幕截图显示了购买过程– " **购买** " 按钮将成为功能激活按钮：   

 [![此系列屏幕截图显示了购买过程](purchasing-non-consumable-products-images/image34.png)](purchasing-non-consumable-products-images/image34.png#lightbox)   

购买过程与耗材产品相同;主要区别在于如何在应用程序代码中跟踪购买情况。 在此示例中，仅当尚未购买产品时，"购买" 按钮才可用，否则该按钮将激活功能本身。   

下图显示了类和 App Store 服务器之间的交互，以执行不可耗用的产品购买：   

 [![类和 App Store 服务器之间的交互，以执行不可耗用的产品购买](purchasing-non-consumable-products-images/image35.png)](purchasing-non-consumable-products-images/image35.png#lightbox)   

此可耗用示例的主要区别在于，一旦购买完成，用户界面就会更新，以防重新购买。 在此示例中，成功的事务通知将更新用户界面，以便将 " **购买** " 按钮转换为激活该功能本身的按钮。

## <a name="re-purchasing-non-consumable-products"></a>重新购买不可耗用的产品

成功购买产品后，你的代码通常会隐藏或重新使用 "购买" 按钮，以防用户尝试再次购买产品。 示例应用程序通过将 " **购买** " 按钮更改为使示例照片筛选器工作的按钮来实现此操作。   

在某些情况下，应用程序无法判断是否已购买过不需要的产品：

- 如果删除了某个应用程序并将其重新安装到设备上，则将不会 (所有购买记录，除非用户执行备份-还原) 。 
- 如果用户将应用程序安装在两个 (或更多) 设备上，并在其中一台设备上进行购买。 其他设备将继续显示可以购买的产品。 
- 如果客户尝试在这些情况下重新购买非使用的产品，应用商店将再次免费完成产品。 用户界面最初会显示执行采购 (例如，将显示确认警报并需要 Apple ID) 不过，用户将看到一条消息，告知他们已购买产品。  

此方案中的代码路径与常规购买完全相同，唯一的区别在于：

- 用户不会再为该产品付费。
- `SKPaymentTransaction`传递给应用程序的对象将具有一个 `OriginalTransaction` 属性，该属性引用最初购买产品时生成的事务。 
- 销售非耗用产品的应用程序还必须实现 StoreKit 的  **还原** 功能，以帮助用户检索现有购买内容。