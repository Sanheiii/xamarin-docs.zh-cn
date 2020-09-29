---
title: 在 Xamarin 中购买可耗用产品
description: 本文档介绍了 Xamarin 中的可耗用产品。 可使用的产品是一项功能，例如游戏中的货币。
ms.prod: xamarin
ms.assetid: E0CB4A0F-C3FA-3933-58A7-13246971D677
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: d6cc17d4a3a77487689357641999525a539b442f
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435532"
---
# <a name="purchasing-consumable-products-in-xamarinios"></a>在 Xamarin 中购买可耗用产品

可采用的产品是最容易实现的，因为没有 "还原" 要求。 它们对于游戏中的货币或一种单一用途的功能很有用。 用户可以反复重新购买可耗用的产品。

## <a name="built-in-product-delivery"></a>内置产品交付

本文档附带的示例代码演示了内置产品-将产品 Id 硬编码到应用程序中，因为它们与在付款后 "解除锁定" 功能的代码紧密耦合。 采购过程的可视化方式如下：   
   
[![采购流程可视化](purchasing-consumable-products-images/image26.png)](purchasing-consumable-products-images/image26.png#lightbox)     
   
 基本工作流是：   
   
 1. 应用将添加 `SKPayment` 到队列中。 如果需要，将提示用户输入其 Apple ID，并要求确认付款。   
   
 2. StoreKit 将请求发送到服务器进行处理。   
   
 3. 事务完成后，服务器将使用事务回执进行响应。   
   
 4. `SKPaymentTransactionObserver`子类接收并处理该接收。   
   
 5. 应用程序通过更新 `NSUserDefaults` 或其他某些机制)  (，然后调用 StoreKit 的 `FinishTransaction` 。

还有另一种类型的工作流– *服务器交付的产品* -稍后将在文档 (参阅《 *收据验证和服务器交付的产品*) 部分。

## <a name="consumable-products-example"></a>耗材产品示例

[InAppPurchaseSample 代码](/samples/xamarin/ios-samples/storekit)包含一个名为 " *" 的项目，该*项目可实现 "游戏中的基本货币 (" ) 。 此示例演示如何实现两个应用内购买产品，使用户能够根据需要购买任意数量的 "猴子信用额度" –在实际应用程序中，还可以通过某种方式来支出它们！   

这些屏幕截图中显示了应用程序–每个购买都会向用户的余额添加更多 "猴子学分"：   

 [![每个购买向用户余额添加了更多的猴子信用额度](purchasing-consumable-products-images/image27.png)](purchasing-consumable-products-images/image27.png#lightbox)   

自定义类 StoreKit 和 App Store 之间的交互如下所示：   

 [![自定义类、StoreKit 和 App Store 之间的交互](purchasing-consumable-products-images/image28.png)](purchasing-consumable-products-images/image28.png#lightbox)

### <a name="viewcontroller-methods"></a>ViewController 方法

除了检索产品信息所需的属性和方法之外，视图控制器还需要其他通知观察程序来侦听与购买相关的通知。 它们只是 `NSObjects` 将在和中分别注册和 `ViewWillAppear` 删除 `ViewWillDisappear` 。

```csharp
NSObject succeededObserver, failedObserver;
```

构造函数还将创建 `SKProductsRequestDelegate` 子类 ( `InAppPurchaseManager`) ，进而创建并注册 `SKPaymentTransactionObserver` ( `CustomPaymentObserver`) 。   

处理应用内购买事务的第一部分是在用户希望购买某些内容时处理按钮按下，如示例应用程序中所示的以下代码所示：

```csharp
buy5Button.TouchUpInside += (sender, e) => {
   iap.PurchaseProduct (Buy5ProductId);
};
buy10Button.TouchUpInside += (sender, e) => {
   iap.PurchaseProduct (Buy10ProductId);
};
```

用户界面的第二部分是处理事务成功的通知，在这种情况下，会更新显示的余额：

```csharp
succeededObserver = NSNotificationCenter.DefaultCenter.AddObserver (InAppPurchaseManager.InAppPurchaseManagerTransactionSucceededNotification,
(notification) => {
   balanceLabel.Text = CreditManager.Balance() + " monkey credits";
});
```

如果出于某种原因取消了某个事务，则用户界面的最后部分会显示一条消息。 在示例代码中，消息只是写入到输出窗口：

```csharp
failedObserver = NSNotificationCenter.DefaultCenter.AddObserver (InAppPurchaseManager.InAppPurchaseManagerTransactionFailedNotification,
(notification) => {
   Console.WriteLine ("Transaction Failed");
});
```

除了视图控制器上的这些方法，可耗用的产品购买事务还需要和上的代码 `SKProductsRequestDelegate` `SKPaymentTransactionObserver` 。

### <a name="inapppurchasemanager-methods"></a>InAppPurchaseManager 方法

示例代码在 InAppPurchaseManager 类上实现了许多采购相关方法，包括 `PurchaseProduct` 创建 `SKPayment` 实例并将其添加到队列进行处理的方法：

```csharp
public void PurchaseProduct(string appStoreProductId)
{
   SKPayment payment = SKPayment.PaymentWithProduct (appStoreProductId);
   SKPaymentQueue.DefaultQueue.AddPayment (payment);
}
```

将付款添加到队列是一种异步操作。 当 StoreKit 处理事务并将其发送到 Apple 的服务器时，应用程序重新获得控制权。 此时，iOS 将验证用户是否已登录到应用商店，并在需要时提示她输入 Apple ID 和密码。   

假设用户成功地通过应用商店进行身份验证并同意事务，则 `SKPaymentTransactionObserver` 将接收 StoreKit 的响应，并调用以下方法来完成该事务并完成该事务。

```csharp
public void CompleteTransaction (SKPaymentTransaction transaction)
{
   var productId = transaction.Payment.ProductIdentifier;
   // Register the purchase, so it is remembered for next time
   PhotoFilterManager.Purchase(productId);
   FinishTransaction(transaction, true);
}
```

最后一步是通过调用以下方法确保通知 StoreKit 已成功完成事务 `FinishTransaction` ：

```csharp
public void FinishTransaction(SKPaymentTransaction transaction, bool wasSuccessful)
{
   // remove the transaction from the payment queue.
   SKPaymentQueue.DefaultQueue.FinishTransaction(transaction);  // THIS IS IMPORTANT - LET'S APPLE KNOW WE'RE DONE !!!!
   using (var pool = new NSAutoreleasePool()) {
       NSDictionary userInfo = NSDictionary.FromObjectsAndKeys(new NSObject[] {transaction},new NSObject[] {new NSString("transaction")});
       if (wasSuccessful) {
           // send out a notification that we've finished the transaction
           NSNotificationCenter.DefaultCenter.PostNotificationName (InAppPurchaseManagerTransactionSucceededNotification, this, userInfo);
       } else {
           // send out a notification for the failed transaction
           NSNotificationCenter.DefaultCenter.PostNotificationName (InAppPurchaseManagerTransactionFailedNotification, this, userInfo);
       }
   }
}
```

交付产品后， `SKPaymentQueue.DefaultQueue.FinishTransaction` 必须调用才能从付款队列中删除该事务。

### <a name="skpaymenttransactionobserver-custompaymentobserver-methods"></a>SKPaymentTransactionObserver (CustomPaymentObserver) 方法

StoreKit `UpdatedTransactions` 在收到 Apple 服务器的响应时调用方法，并传递一个 `SKPaymentTransaction` 对象数组供你的代码检查。 方法遍历每个事务，并根据事务状态执行不同的函数 (如下所示) ：

```csharp
public override void UpdatedTransactions (SKPaymentQueue queue, SKPaymentTransaction[] transactions)
{
   foreach (SKPaymentTransaction transaction in transactions)
   {
       switch (transaction.TransactionState)
       {
           case SKPaymentTransactionState.Purchased:
              theManager.CompleteTransaction(transaction);
               break;
           case SKPaymentTransactionState.Failed:
              theManager.FailedTransaction(transaction);
               break;
           default:
               break;
       }
   }
}
```

`CompleteTransaction`本部分前面介绍了方法-它将购买详细信息保存到 `NSUserDefaults` ，通过 StoreKit 完成事务，最后通知 UI 进行更新。

### <a name="purchasing-multiple-products"></a>购买多个产品

如果在应用程序中购买多个产品是有意义的，请使用 `SKMutablePayment` 类并设置 "数量" 字段：

```csharp
public void PurchaseProduct(string appStoreProductId)
{
   SKMutablePayment payment = SKMutablePayment.PaymentWithProduct (appStoreProductId);
   payment.Quantity = 4; // hardcoded as an example
   SKPaymentQueue.DefaultQueue.AddPayment (payment);
}
```

处理已完成的事务的代码还必须查询 "数量" 属性以正确完成购买：

```csharp
public void CompleteTransaction (SKPaymentTransaction transaction)
{
   var productId = transaction.Payment.ProductIdentifier;
   var qty = transaction.Payment.Quantity;
   if (productId == ConsumableViewController.Buy5ProductId)
       CreditManager.Add(5 * qty);
   else if (productId == ConsumableViewController.Buy10ProductId)
       CreditManager.Add(10 * qty);
   else
       Console.WriteLine ("Shouldn't happen, there are only two products");
   FinishTransaction(transaction, true);
}
```

如果用户购买了多个数量，则 StoreKit 确认警报将反映数量、单位价格以及支付的总价格，如以下屏幕截图所示：

[![确认购买](purchasing-consumable-products-images/image30.png)](purchasing-consumable-products-images/image30.png#lightbox)

## <a name="handling-network-outages"></a>处理网络中断

应用内购买需要 StoreKit 的有效网络连接，才能与 Apple 的服务器进行通信。 如果网络连接不可用，则应用内购买将不可用。

### <a name="product-requests"></a>产品请求

如果在进行此操作时网络不可用， `SKProductRequest` `RequestFailed` `SKProductsRequestDelegate` 将调用子类 () 的方法 `InAppPurchaseManager` ，如下所示：

```csharp
public override void RequestFailed (SKRequest request, NSError error)
{
   using (var pool = new NSAutoreleasePool()) {
       NSDictionary userInfo = NSDictionary.FromObjectsAndKeys(new NSObject[] {error},new NSObject[] {new NSString("error")});
       // send out a notification for the failed transaction
       NSNotificationCenter.DefaultCenter.PostNotificationName (InAppPurchaseManagerRequestFailedNotification, this, userInfo);
   }
}
```

然后，ViewController 侦听通知并在 "购买" 按钮中显示一条消息：

```csharp
requestObserver = NSNotificationCenter.DefaultCenter.AddObserver (InAppPurchaseManager.InAppPurchaseManagerRequestFailedNotification,
(notification) => {
   Console.WriteLine ("Request Failed");
   buy5Button.SetTitle ("Network down?", UIControlState.Disabled);
   buy10Button.SetTitle ("Network down?", UIControlState.Disabled);
});
```

由于在移动设备上网络连接可能是暂时性的，因此应用程序可能希望使用 SystemConfiguration 框架来监视网络状态，并在网络连接可用时重新尝试。 请参阅 Apple 的或使用它的。

### <a name="purchase-transactions"></a>采购交易

StoreKit 付款队列将存储并转发购买请求（如果可能），因此，网络故障的影响取决于在购买过程中网络出现故障的时间。   

如果在事务中发生错误，则) 的 `SKPaymentTransactionObserver` 子类 ( `CustomPaymentObserver` 将 `UpdatedTransactions` 调用方法并且 `SKPaymentTransaction` 类将处于失败状态。

```csharp
public override void UpdatedTransactions (SKPaymentQueue queue, SKPaymentTransaction[] transactions)
{
   foreach (SKPaymentTransaction transaction in transactions)
   {
       switch (transaction.TransactionState)
       {
           case SKPaymentTransactionState.Purchased:
               theManager.CompleteTransaction(transaction);
               break;
           case SKPaymentTransactionState.Failed:
               theManager.FailedTransaction(transaction);
               break;
           default:
               break;
       }
   }
}
```

`FailedTransaction`方法检测错误是否由用户取消导致，如下所示：

```csharp
public void FailedTransaction (SKPaymentTransaction transaction)
{
   //SKErrorPaymentCancelled == 2
   if (transaction.Error.Code == 2) // user cancelled
       Console.WriteLine("User CANCELLED FailedTransaction Code=" + transaction.Error.Code + " " + transaction.Error.LocalizedDescription);
   else // error!
       Console.WriteLine("FailedTransaction Code=" + transaction.Error.Code + " " + transaction.Error.LocalizedDescription);
   FinishTransaction(transaction,false);
}
```

即使事务失败，也 `FinishTransaction` 必须调用方法以从付款队列中删除事务：

```csharp
SKPaymentQueue.DefaultQueue.FinishTransaction(transaction);
```

然后，示例代码将发送通知，以便 ViewController 可以显示消息。 如果用户取消了事务，应用程序不应显示其他消息。 可能出现的其他错误代码包括：

```csharp
FailedTransaction Code=0 Cannot connect to iTunes Store
FailedTransaction Code=5002 An unknown error has occurred
FailedTransaction Code=5020 Forget Your Password?
Applications may detect and respond to specific error codes, or handle them in the same way.
```

## <a name="handling-restrictions"></a>处理限制

"设置" > "iOS" 的 " **常规 > 限制** " 功能，用户可锁定其设备的某些功能。   

可以查询用户是否允许通过方法进行应用内购买 `SKPaymentQueue.CanMakePayments` 。 如果此返回 false，则用户无法访问应用内购买。 如果尝试购买，StoreKit 将自动向用户显示一条错误消息。 通过选中此值，应用程序可以隐藏购买按钮，或执行其他操作来帮助用户。   

在 `InAppPurchaseManager.cs` 文件中， `CanMakePayments` 方法包装 StoreKit 函数，如下所示：

```csharp
public bool CanMakePayments()
{
   return SKPaymentQueue.CanMakePayments;
}
```

若要测试此方法，请使用 iOS 的 **限制** 功能禁用 **应用内购买**：   

 [![使用 iOS 的限制功能禁用应用内购买](purchasing-consumable-products-images/image31.png)](purchasing-consumable-products-images/image31.png#lightbox)   

此代码示例 `ConsumableViewController` `CanMakePayments` 通过在禁用的按钮上显示 **AppStore 的禁用** 文本来响应返回 false。

```csharp
// only if we can make payments, request the prices
if (iap.CanMakePayments()) {
   // now go get prices, if we don't have them already
   if (!pricesLoaded)
       iap.RequestProductData(products); // async request via StoreKit -> App Store
} else {
   // can't make payments (purchases turned off in Settings?)
   // the buttons are disabled by default, and only enabled when prices are retrieved
   buy5Button.SetTitle ("AppStore disabled", UIControlState.Disabled);
   buy10Button.SetTitle ("AppStore disabled", UIControlState.Disabled);
}
```

应用 **内购买** 功能受到限制时，应用程序如下所示–禁用了 "购买" 按钮。   

 [![当应用内购买功能受到限制时，应用程序如下所示](purchasing-consumable-products-images/image32.png)](purchasing-consumable-products-images/image32.png#lightbox)   

当为 false 时，仍可以请求产品信息 `CanMakePayments` ，因此应用仍可检索并显示价格。 这意味着，如果我们从代码中删除了 " `CanMakePayments` 采购" 按钮仍处于活动状态的检查，则当用户尝试进行购买时，用户将看到一条消息，指出在) 访问付款队列时， **不允许 StoreKit 生成应用内采购** (：   

 [![不允许应用内购买](purchasing-consumable-products-images/image33.png)](purchasing-consumable-products-images/image33.png#lightbox)   

实际的应用程序可能采用不同的方法来处理限制，如完全隐藏按钮并提供比 StoreKit 自动显示的警报更详细的消息。