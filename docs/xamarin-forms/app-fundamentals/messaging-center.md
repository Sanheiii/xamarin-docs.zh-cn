---
title: Xamarin.Forms MessagingCenter
description: Xamarin.Forms MessagingCenter 类可实现发布-订阅模式，允许不便按对象和类型引用进行链接的组件之间进行基于消息的通信。
ms.prod: xamarin
ms.assetid: EDFE7B19-C5FD-40D5-816C-FAE56532E885
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/08/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d24ff6831bb8b52d840ff2db7c13c3eb674b7bd8
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91555783"
---
# <a name="no-locxamarinforms-messagingcenter"></a>Xamarin.Forms MessagingCenter

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingmessagingcenter)

发布-订阅模式是一种消息传递模式，在此模式下，发布者可在无需知道任何接收方（称为订阅方）的情况下发送消息。 同样，订阅方可在不了解任何发布方的情况下侦听特定消息。

.NET 中的事件可实现发布-订阅模式，如果不需要松散耦合（例如控件和包含它的页面），则这些事件是通信层在组件之间最简单直接的方法。 但是，发布服务器和订阅服务器的生存期通过对象引用彼此耦合，而订阅服务器类型必须引用发布服务器类型。 这可能会造成内存管理问题，尤其是在订阅静态或长期对象事件的对象生存期较短时。 如果不删除事件处理程序，则订阅服务器通过在发布服务器中引用它来保持活动状态，这将阻止或延迟订阅服务器的垃圾回收。

Xamarin.Forms [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) 类可实现发布-订阅模式，允许不便按对象和类型引用进行链接的组件之间进行基于消息的通信。 这种机制允许发布方和订阅方在没有彼此引用的情况下进行通信，这有助于减少它们之间的依赖关系。

[`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) 类提供多播发布-订阅功能。 这意味着可以有多个发布方发布单个消息，并且可能有多个订阅方正在侦听同一消息：

![多播发布-订阅功能](messaging-center-images/messaging-center.png)

发布方使用 [`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*) 方法发送消息，而订阅方使用 [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) 方法侦听消息。 此外，订阅方还可以使用 [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) 方法取消消息订阅（如果需要）。

> [!IMPORTANT]
> 在内部，[`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) 类使用弱引用。 这意味着它不会使对象保持活动状态，而是允许对它们进行垃圾回收。 因此，只有当类不再希望接收消息时才需要取消订阅消息。

## <a name="publish-a-message"></a>发布消息

[`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) 消息是字符串。 发布方通过一种 [`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*) 重载通知订阅方查看消息。 以下代码示例可发布 `Hi` 消息：

```csharp
MessagingCenter.Send<MainPage>(this, "Hi");
```

在此示例中，[`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) 方法指定表示发送方的泛型参数。 若要接收消息，订阅方还必须指定相同的泛型参数，表明他们正在侦听来自该发送方的消息。 此外，此示例指定两个方法参数：

- 第一个参数指定发送方实例。
- 第二个参数指定消息。

还可以通过消息发送有效负载数据：

```csharp
MessagingCenter.Send<MainPage, string>(this, "Hi", "John");
```

在此示例中，[`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) 方法指定了两个泛型参数。 第一个参数是发送消息的类型，第二个参数是发送的有效负载数据的类型。 若要接收消息，订阅方还必须指定相同的泛型参数。 这允许多条消息共享一个消息标识，但发送不同的有效负载数据类型以供不同的订阅方接收。 此外，此示例指定第三个方法参数，该参数表示要发送到订阅方的有效负载数据。 在这种情况下，有效负载数据是 `string`。

[`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) 方法将使用“即发即弃”方法发布消息和任何有效负载数据。 因此，即使没有注册的订阅方接收消息，消息也仍会发送。 在这种情况下，将忽略已发送的消息。

## <a name="subscribe-to-a-message"></a>订阅消息

订阅方可以使用一种 [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) 重载进行注册以接收消息。 下面的代码示例演示了此示例：

```csharp
MessagingCenter.Subscribe<MainPage> (this, "Hi", (sender) =>
{
    // Do something whenever the "Hi" message is received
});
```

在此示例中，[`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) 方法使 `this` 对象订阅 `MainPage` 类型发送的 `Hi` 消息，并执行回调委托以响应接收消息。 指定为 lambda 表达式的回调委托可以是更新 UI、保存某些数据或触发其他操作的代码。

> [!NOTE]
> 订阅方可能不需要处理已发布消息的每个实例，这可以通过 [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) 方法中指定的泛型类型参数来控制。

以下示例显示如何订阅包含有效负载数据的消息：

```csharp
MessagingCenter.Subscribe<MainPage, string>(this, "Hi", async (sender, arg) =>
{
    await DisplayAlert("Message received", "arg=" + arg, "OK");
});
```

在此示例中，[`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) 方法订阅 `MainPage` 类型发送的 `Hi` 消息，其有效负载数据为 `string`。 回调委托在接收到此类消息时执行，该消息将在警报中显示有效负载数据。

> [!IMPORTANT]
> 将在使用 `Send` 方法发布消息的同一个线程上执行 `Subscribe` 方法执行的委托。

## <a name="unsubscribe-from-a-message"></a>取消订阅消息

订阅者可以取消订阅他们不想再收到的消息。 这可以通过一种 [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) 重载实现：

```csharp
MessagingCenter.Unsubscribe<MainPage>(this, "Hi");
```

在此示例中，[`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) 方法使 `this` 对象取消订阅 `MainPage` 类型发送的 `Hi` 消息。

应使用指定两个泛型参数的 [`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) 重载来取消订阅包含有效负载数据的消息：

```csharp
MessagingCenter.Unsubscribe<MainPage, string>(this, "Hi");
```

在此示例中，[`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) 方法使 `this` 对象取消订阅 `MainPage` 类型发送的 `Hi` 消息，其有效负载数据为 `string`。

## <a name="related-links"></a>相关链接

- [MessagingCenterSample](/samples/xamarin/xamarin-forms-samples/usingmessagingcenter)