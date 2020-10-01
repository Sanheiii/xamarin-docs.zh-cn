---
title: Xamarin.Forms DependencyService 注册和解析
description: 本文介绍如何使用 Xamarin.Forms DependencyService 类调用本机平台功能。
ms.prod: xamarin
ms.assetid: 5d019604-4f6f-4932-9b26-1fce3b4d88f8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/05/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: da3c6c2f55f1cdbed1b3c4e1bb16c4f78e32cf23
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556043"
---
# <a name="no-locxamarinforms-dependencyservice-registration-and-resolution"></a>Xamarin.Forms DependencyService 注册和解析

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/dependencyservice/)

使用 Xamarin.Forms [`DependencyService`](xref:Xamarin.Forms.DependencyService) 调用本机平台功能时，必须通过 `DependencyService` 注册平台实现，然后从共享代码进行解析，才能调用它们。

## <a name="register-platform-implementations"></a>注册平台实现

必须通过 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 注册平台实现，Xamarin.Forms 在运行时才可以找到它们。

可以使用 [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute)、[`Register`](xref:Xamarin.Forms.DependencyService.Register*) 和 `RegisterSingleton` 方法执行注册。

> [!IMPORTANT]
> 使用 NET 本机编译的 UWP 项目的版本生成应使用 [`Register`](xref:Xamarin.Forms.DependencyService.Register*) 方法注册平台实现。

### <a name="registration-by-attribute"></a>使用属性注册

可以使用 [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) 通过 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 注册平台实现。 此属性指示指定的类型可提供接口的具体实现。

下面的示例演示如何使用 [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) 注册 `IDeviceOrientationService` 接口的 iOS 实现：

```csharp
using Xamarin.Forms;

[assembly: Dependency(typeof(DeviceOrientationService))]
namespace DependencyServiceDemos.iOS
{
    public class DeviceOrientationService : IDeviceOrientationService
    {
        public DeviceOrientation GetOrientation()
        {
            ...
        }
    }
}
```

在此示例中，[`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) 通过 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 注册 `DeviceOrientationService`。 这样就会在具体类型所实现的接口上注册它。

同样，其他平台上的 `IDeviceOrientationService` 接口的实现也应通过 [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) 注册。

> [!NOTE]
> 在命名空间级别完成使用 [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) 注册。

### <a name="registration-by-method"></a>使用方法注册

可以使用 [`DependencyService.Register`](xref:Xamarin.Forms.DependencyService.Register*) 方法和 `RegisterSingleton` 方法向 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 注册平台实现。

下面的示例演示如何使用 [`Register`](xref:Xamarin.Forms.DependencyService.Register*) 方法注册 `IDeviceOrientationService` 接口的 iOS 实现：

```csharp
[Register("AppDelegate")]
public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate
{
    public override bool FinishedLaunching(UIApplication app, NSDictionary options)
    {
        global::Xamarin.Forms.Forms.Init();
        LoadApplication(new App());
        DependencyService.Register<IDeviceOrientationService, DeviceOrientationService>();
        return base.FinishedLaunching(app, options);
    }
}
```

在此示例中，[`Register`](xref:Xamarin.Forms.DependencyService.Register*) 在 `IDeviceOrientationService` 接口上注册具体类型 `DeviceOrientationService`。 也可以使用 [`Register`](xref:Xamarin.Forms.DependencyService.Register*) 方法的重载通过 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 注册平台实现：

```csharp
DependencyService.Register<DeviceOrientationService>();
```

在此示例中，[`Register`](xref:Xamarin.Forms.DependencyService.Register*) 方法使用 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 注册 `DeviceOrientationService`。 这样就会在具体类型所实现的接口上注册它。

或者，可以使用 `RegisterSingleton` 方法将现有对象实例注册为单一实例：

```csharp
var service = new DeviceOrientationService();
DependencyService.RegisterSingleton<IDeviceOrientationService>(service);
```

在此示例中，`RegisterSingleton` 方法针对 `IDeviceOrientationService` 接口将 `DeviceOrientationService` 对象实例注册为单一实例。

同样，其他平台上的 `IDeviceOrientationService` 接口的实现也可以使用 [`Register`](xref:Xamarin.Forms.DependencyService.Register*) 方法或 `RegisterSingleton` 方法注册。

> [!IMPORTANT]
> 必须在平台项目中使用 [`Register`](xref:Xamarin.Forms.DependencyService.Register*) 和 `RegisterSingleton` 方法执行注册，才可以从共享代码调用平台实现提供的功能。

## <a name="resolve-the-platform-implementations"></a>解析平台实现

必须先解析平台实现，然后才能调用它们。 通常使用 [`DependencyService.Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) 方法在共享代码中完成此操作。 不过也可以使用 [`DependencyService.Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) 方法完成它。

默认情况下，[`DependencyService`](xref:Xamarin.Forms.DependencyService) 仅解析具有无参数构造函数的平台实现。 但是，依赖项解析方法可以注入到使用依赖项注入容器或工厂方法的 Xamarin.Forms 中，以解析平台实现。 这种方法可用于解析具有带有参数的构造函数的平台实现。 有关详细信息，请参阅 [Xamarin.Forms 中的依赖项解析](~/xamarin-forms/internals/dependency-resolution.md)。

> [!IMPORTANT]
> 如果调用的平台实现尚未通过 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 注册，将引发 `NullReferenceException`。

### <a name="resolve-using-the-getlttgt-method"></a>使用 Get&lt;T&gt; 方法解析

[`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) 方法在运行时检索接口 `T` 的平台实现，或者：

- 将它的实例创建为单一实例。
- 返回一个作为单一实例的现有实例，该实例已使用 `RegisterSingleton` 方法向 `DependencyService` 注册。

在这两种情况下，在应用程序的生存期期间，此实例将持续存在，并且为解析同一平台实现而执行的所有后续调用将检索同一实例。

下面的代码示例说明了如何调用 [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) 方法解析 `IDeviceOrientationService` 接口并调用 `GetOrientation` 方法：

```csharp
IDeviceOrientationService service = DependencyService.Get<IDeviceOrientationService>();
DeviceOrientation orientation = service.GetOrientation();
```

也可以将此代码压缩到一行：

```csharp
DeviceOrientation orientation = DependencyService.Get<IDeviceOrientationService>().GetOrientation();
```

> [!NOTE]
> 默认情况下，[`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) 方法返回接口 `T` 的平台实现实例并将其作为单一实例。 但是，可以更改这种行为。 有关详细信息，请参阅[管理解析对象的生存期](#manage-the-lifetime-of-resolved-objects)。

### <a name="resolve-using-the-resolvelttgt-method"></a>使用 Resolve&lt;T&gt; 方法解析

[`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) 方法在运行时使用 [`DependencyResolver`](xref:Xamarin.Forms.Internals.DependencyResolver) 类注入到 Xamarin.Forms 的依赖项解析方法检索接口 `T` 的平台实现。 如果未将依赖项解析方法注入到 Xamarin.Forms，`Resolve<T>` 方法将回退到调用 [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) 方法，以检索平台实现。 有关将依赖项解析方法注入到 Xamarin.Forms 的详细信息，请参阅 [Xamarin.Forms 中的依赖项解析](~/xamarin-forms/internals/dependency-resolution.md)。

下面的代码示例说明了如何调用 [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) 方法解析 `IDeviceOrientationService` 接口并调用 `GetOrientation` 方法：

```csharp
IDeviceOrientationService service = DependencyService.Resolve<IDeviceOrientationService>();
DeviceOrientation orientation = service.GetOrientation();
```

也可以将此代码压缩到一行：

```csharp
DeviceOrientation orientation = DependencyService.Resolve<IDeviceOrientationService>().GetOrientation();
```

> [!NOTE]
> 默认情况下，当 [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) 方法回退到调用 [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) 方法时，它将返回接口 `T` 的平台实现实例并将其作为单一实例。 但是，可以更改这种行为。 有关详细信息，请参阅[管理解析对象的生存期](#manage-the-lifetime-of-resolved-objects)。

## <a name="manage-the-lifetime-of-resolved-objects"></a>管理解析对象的生存期

[`DependencyService`](xref:Xamarin.Forms.DependencyService) 类的默认行为是将平台实现解析为单一实例。 因此，平台实现将在应用程序的生存期期间持续存在。

此行为由 [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) 和 [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) 方法上的可选参数 [`DependencyFetchTarget`](xref:Xamarin.Forms.DependencyFetchTarget) 指定。 [`DependencyFetchTarget`](xref:Xamarin.Forms.DependencyFetchTarget) 枚举定义以下两个成员：

- 将平台实现作为单一实例返回的 `GlobalInstance`。
- 返回新的平台实现实例的 `NewInstance`。 然后由应用程序负责管理平台实现实例的生存期。

[`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) 和 [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) 方法均将其可选参数设置为 [`DependencyFetchTarget.GlobalInstance`](xref:Xamarin.Forms.DependencyFetchTarget)，因此始终会将平台实现解析为单一实例。 通过将 [`DependencyFetchTarget.NewInstance`](xref:Xamarin.Forms.DependencyFetchTarget) 指定为 `Get<T>` 和 `Resolve<T>` 方法的参数，可以更改此行为，来创建新的平台实现实例：

```csharp
ITextToSpeechService service = DependencyService.Get<ITextToSpeechService>(DependencyFetchTarget.NewInstance);
```

在此示例中，[`DependencyService`](xref:Xamarin.Forms.DependencyService) 创建 `ITextToSpeechService` 接口的平台实现的新实例。 为解析 `ITextToSpeechService` 而执行的所有后续调用也将创建新实例。

始终创建新的平台实现实例的结果是由应用程序负责管理实例的生存期。 这意味着，如果订阅平台实现定义的事件，不再需要此平台实现时，应取消订阅此事件。 此外，这意味着，平台实现可能需要实现 `IDisposable` 并在 `Dispose` 方法中清除这些资源。 示例应用程序在其 `TextToSpeechService` 平台实现中演示了此方案。

如果应用程序不再使用实现 `IDisposable` 的平台实现，它应调用对象的 `Dispose` 实现。 完成此操作的方式之一是使用 `using` 语句：

```csharp
ITextToSpeechService service = DependencyService.Get<ITextToSpeechService>(DependencyFetchTarget.NewInstance);
using (service as IDisposable)
{
    await service.SpeakAsync("Hello world");
}
```

在此示例中，调用 `SpeakAsync` 方法后，`using` 语句自动释放平台实现对象。 这样就会调用对象的 `Dispose` 方法，来执行必需的清理。

有关调用对象的 `Dispose` 方法的详细信息，请参阅[使用实现 IDisposable 的对象](/dotnet/standard/garbage-collection/using-objects)。

## <a name="related-links"></a>相关链接

- [DependencyService 演示（示例）](/samples/xamarin/xamarin-forms-samples/dependencyservice/)
- [Xamarin.Forms 中的依赖项解析](~/xamarin-forms/internals/dependency-resolution.md)