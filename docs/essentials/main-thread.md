---
title: Xamarin.Essentials：MainThread
description: MainThread 类允许应用程序在主执行线程上运行代码。
ms.assetid: CD6D51E7-D933-4FE7-A7F7-392EF27812E1
author: jamesmontemagno
ms.custom: video
ms.author: jamont
ms.date: 08/20/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0dc8981024105e24ef4dcd725284723060e7313c
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91432318"
---
# <a name="no-locxamarinessentials-mainthread"></a>Xamarin.Essentials：MainThread

MainThread 类允许应用程序在主执行线程上运行代码，并确定当前是否在主线程上运行特定代码块。

## <a name="background"></a>背景

大多数操作系统（包括 iOS、Android 和通用 Windows 平台）对涉及用户界面的代码使用单线程模型。 正确序列化用户界面事件（包括击键和触控输入）需要此模型。 此线程通常称为“主线程”、“用户界面线程”或“UI 线程”。 此模型的缺点是用于访问用户界面元素的所有代码必须在应用程序的主线程上运行。

应用程序有时需要使用在辅助执行线程上调用事件处理程序的事件。 （Xamarin.Essentials 类 [`Accelerometer`](accelerometer.md)、[`Compass`](compass.md)、[`Gyroscope`](gyroscope.md)、[`Magnetometer`](magnetometer.md) 和 [`OrientationSensor`](orientation-sensor.md) 以更快的速度使用时，可能会返回有关辅助线程的信息。）如果事件处理程序需要访问用户界面元素，则必须在主线程上运行该代码。 MainThread 类允许应用程序在主线程上运行此代码。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

## <a name="running-code-on-the-main-thread"></a>在主线程上运行代码

在类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

若要在主线程上运行代码，请调用静态 `MainThread.BeginInvokeOnMainThread` 方法。 参数是 [`Action`](xref:System.Action) 对象，它只是没有参数且没有返回值的方法：

```csharp
MainThread.BeginInvokeOnMainThread(() =>
{
    // Code to run on the main thread
});
```

也可以为必须在主线程上运行的代码定义单独的方法：

```csharp
void MyMainThreadCode()
{
    // Code to run on the main thread
}
```

然后，可以通过在 `BeginInvokeOnMainThread` 方法中引用主线程，以便在主线程上运行此方法：

```csharp
MainThread.BeginInvokeOnMainThread(MyMainThreadCode);
```

> [!NOTE]
> Xamarin.Forms 具有一个名为 [`Device.BeginInvokeOnMainThread(Action)`](/dotnet/api/xamarin.forms.device.begininvokeonmainthread) 的方法
> 该方法与 `MainThread.BeginInvokeOnMainThread(Action)` 执行相同操作。
> 虽然可以在 Xamarin.Forms 应用中使用任何一种方法，但请考虑调用的代码是否需要在 Xamarin.Forms 上有任何其他依赖项。 如果不需要，则 `MainThread.BeginInvokeOnMainThread(Action)` 可能是更好的选择。

## <a name="determining-if-code-is-running-on-the-main-thread"></a>确定是否在主线程上运行代码

`MainThread` 类还允许应用程序确定是否在主线程上运行特定代码块。 如果在主线程上运行调用 `IsMainThread` 属性的代码，则该属性会返回 `true`。 程序可以使用此属性运行主线程或辅助线程的不同代码：

```csharp
if (MainThread.IsMainThread)
{
    // Code to run if this is the main thread
}
else
{
    // Code to run if this is a secondary thread
}
```

在调用 `BeginInvokeOnMainThread` 之前，你可能想知道是否应检查代码是否在辅助线程上运行，如下所示：

```csharp
if (MainThread.IsMainThread)
{
    MyMainThreadCode();
}
else
{
    MainThread.BeginInvokeOnMainThread(MyMainThreadCode);
}
```

如果已在主线程上运行代码块，你可能会猜想此检查可能会提高性能。

_但是，不需要执行此检查。_ `BeginInvokeOnMainThread` 的平台实现本身会检查是否在主线程上调用代码。 如果在并不需要时调用 `BeginInvokeOnMainThread`，则很少会有性能损失。

## <a name="additional-methods"></a>其他方法

`MainThread` 类包括以下附加 `static` 方法，可用于与背景线程中的用户界面元素进行交互：

| 方法 | 自变量 | 返回 | 目标 |
|---|---|---|---|
| `InvokeOnMainThreadAsync<T>` | `Func<T>` | `Task<T>` | 在主线程上调用 `Func<T>`，并等待其完成。 |
| `InvokeOnMainThreadAsync` | `Action` | `Task` | 在主线程上调用 `Action`，并等待其完成。 |
| `InvokeOnMainThreadAsync<T>`| `Func<Task<T>>` | `Task<T>` | 在主线程上调用 `Func<Task<T>>`，并等待其完成。 |
| `InvokeOnMainThreadAsync` | `Func<Task>` | `Task` | 在主线程上调用 `Func<Task>`，并等待其完成。 |
| `GetMainThreadSynchronizationContextAsync` | | `Task<SynchronizationContext>` | 返回主线程的 `SynchronizationContext`。 |

## <a name="api"></a>API

- [MainThread 源代码](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/MainThread)
- [MainThread API 文档](xref:Xamarin.Essentials.MainThread)

## <a name="related-video"></a>相关视频

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Main-Thread-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]