---
title: Xamarin.Forms 中的依赖项解析
description: 本文介绍如何将依赖项解析方法注入到， Xamarin.Forms 以便应用程序的依赖关系注入容器能够控制自定义呈现器、效果和 DependencyService 实现的创建和生存期。
ms.prod: xamarin
ms.assetid: 491B87DC-14CB-4ADC-AC6C-40A7627B2524
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/27/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6d4c9be2166881824e798e9cb801a5720ab55178
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91557851"
---
# <a name="dependency-resolution-in-no-locxamarinforms"></a>Xamarin.Forms 中的依赖项解析

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/advanced-dependencyresolution-dicontainerdemo)

_本文介绍如何将依赖项解析方法注入到， Xamarin.Forms 以便应用程序的依赖关系注入容器能够控制自定义呈现器、效果和 DependencyService 实现的创建和生存期。本文中的代码示例取自 [使用容器的依赖项解析](/samples/xamarin/xamarin-forms-samples/advanced-dependencyresolution-dicontainerdemo) 示例。_

在 Xamarin.Forms 使用模型-视图-ViewModel (MVVM) 模式的应用程序的上下文中，依赖关系注入容器可用于注册和解析视图模型，以及注册服务并将其注入查看模型中。 创建视图模型期间，容器将注入所需的任何依赖项。 如果尚未创建这些依赖项，则容器将首先创建和解析依赖项。 有关依赖关系注入的详细信息，包括在视图模型中注入依赖项的示例，请参阅 [依赖关系注入](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md)。

通常，在平台项目中控制类型的创建和生存期是由执行的 Xamarin.Forms ，后者使用 `Activator.CreateInstance` 方法来创建自定义呈现器、效果和实现的实例 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 。 遗憾的是，这使开发人员能够控制这些类型的创建和生存期，以及向其中注入依赖项的能力。 此行为可以通过将依赖项解析方法注入到中来进行更改 Xamarin.Forms ，这些方法控制如何创建类型（通过应用程序的依赖关系注入容器）或 Xamarin.Forms 。 但请注意，不需要将依赖项解析方法注入到中 Xamarin.Forms 。 Xamarin.Forms 如果未注入依赖项解析方法，将继续创建和管理平台项目中的类型生存期。

> [!NOTE]
> 尽管本文重点介绍如何将依赖项解析方法注入到 Xamarin.Forms 使用依赖关系注入容器解析注册的类型，但也可以注入使用工厂方法解析已注册类型的依赖项解析方法。 有关详细信息，请参阅 [使用工厂方法的依赖关系解析](/samples/xamarin/xamarin-forms-samples/advanced-dependencyresolution-factoriesdemo) 示例。

## <a name="injecting-a-dependency-resolution-method"></a>注入依赖项解析方法

[`DependencyResolver`](xref:Xamarin.Forms.Internals.DependencyResolver)类 Xamarin.Forms 通过使用方法，提供将依赖项解析方法注入到中的功能 [`ResolveUsing`](xref:Xamarin.Forms.Internals.DependencyResolver.ResolveUsing*) 。 然后，当 Xamarin.Forms 需要特定类型的实例时，将向依赖项解析方法提供提供该实例的机会。 如果依赖项解析方法 `null` 为请求的类型返回，则 Xamarin.Forms 回退到尝试使用方法创建类型实例本身 `Activator.CreateInstance` 。

下面的示例演示如何通过方法设置依赖关系解析方法 [`ResolveUsing`](xref:Xamarin.Forms.Internals.DependencyResolver.ResolveUsing*) ：

```csharp
using Autofac;
using Xamarin.Forms.Internals;
...

public partial class App : Application
{
    // IContainer and ContainerBuilder are provided by Autofac
    static IContainer container;
    static readonly ContainerBuilder builder = new ContainerBuilder();

    public App()
    {
        ...
        DependencyResolver.ResolveUsing(type => container.IsRegistered(type) ? container.Resolve(type) : null);
        ...
    }
    ...
}
```

在此示例中，依赖关系解析方法设置为 lambda 表达式，该表达式使用 Autofac 依赖关系注入容器来解析已注册到容器中的所有类型。 否则， `null` 将返回，这将导致 Xamarin.Forms 尝试解析该类型。

> [!NOTE]
> 依赖关系注入容器使用的 API 特定于容器。 本文中的代码示例使用 Autofac 作为依赖关系注入容器，该容器提供 `IContainer` 和 `ContainerBuilder` 类型。 备用依赖关系注入容器可以平等使用，但使用的 Api 不同于此处提供的 Api。

请注意，不需要在应用程序启动过程中设置依赖项解析方法。 可随时对其进行设置。 唯一的约束是， Xamarin.Forms 需要了解依赖关系解析方法，因为应用程序尝试使用存储在依赖关系注入容器中的类型。 因此，如果应用程序在启动过程中将需要依赖项注入容器中的服务，则必须在应用程序生命周期的早期设置依赖项解析方法。 同样，如果依赖关系注入容器管理特定的创建和生存期，则 [`Effect`](xref:Xamarin.Forms.Effect) Xamarin.Forms 需要了解依赖关系解析方法，然后再尝试创建使用它的视图 `Effect` 。

> [!WARNING]
> 注册和解析具有依赖关系注入容器的类型具有性能开销，因为容器使用反射来创建每种类型，尤其是在为应用程序中的每个页面导航重构依赖项时。 如果存在许多或深度依赖关系，则创建成本会显著增加。

## <a name="registering-types"></a>注册类型

必须先向依赖关系注入容器注册类型，然后才能通过依赖项解析方法解析这些类型。 下面的代码示例演示示例应用程序在 `App` 类中为 Autofac 容器公开的注册方法：

```csharp
using Autofac;
using Autofac.Core;
...

public partial class App : Application
{
    static IContainer container;
    static readonly ContainerBuilder builder = new ContainerBuilder();
    ...

    public static void RegisterType<T>() where T : class
    {
        builder.RegisterType<T>();
    }

    public static void RegisterType<TInterface, T>() where TInterface : class where T : class, TInterface
    {
        builder.RegisterType<T>().As<TInterface>();
    }

    public static void RegisterTypeWithParameters<T>(Type param1Type, object param1Value, Type param2Type, string param2Name) where T : class
    {
        builder.RegisterType<T>()
               .WithParameters(new List<Parameter>()
        {
            new TypedParameter(param1Type, param1Value),
            new ResolvedParameter(
                (pi, ctx) => pi.ParameterType == param2Type && pi.Name == param2Name,
                (pi, ctx) => ctx.Resolve(param2Type))
        });
    }

    public static void RegisterTypeWithParameters<TInterface, T>(Type param1Type, object param1Value, Type param2Type, string param2Name) where TInterface : class where T : class, TInterface
    {
        builder.RegisterType<T>()
               .WithParameters(new List<Parameter>()
        {
            new TypedParameter(param1Type, param1Value),
            new ResolvedParameter(
                (pi, ctx) => pi.ParameterType == param2Type && pi.Name == param2Name,
                (pi, ctx) => ctx.Resolve(param2Type))
        }).As<TInterface>();
    }

    public static void BuildContainer()
    {
        container = builder.Build();
    }
    ...
}
```

当应用程序使用依赖项解析方法解析容器中的类型时，类型注册通常从平台项目中执行。 这使平台项目可以注册自定义呈现器、效果和实现的类型 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 。

以下类型从平台项目注册后， `IContainer` 必须生成对象，这是通过调用方法来完成的 `BuildContainer` 。 此方法对实例调用 Autofac 的 `Build` 方法 `ContainerBuilder` ，这将生成一个包含已进行的注册的新的依赖项注入容器。

在接下来的部分中，将 `Logger` 实现接口的类 `ILogger` 注入类构造函数。 `Logger`类使用方法实现简单日志记录功能 `Debug.WriteLine` ，并用于演示如何将服务注入到自定义呈现器、效果和 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 实现中。

### <a name="registering-custom-renderers"></a>注册自定义呈现器

该示例应用程序包括一个播放 web 视频的页面，该页面的 XAML 源如下例所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             ...>
    <video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4" />
</ContentPage>
```

`VideoPlayer`视图在每个平台上由类实现 `VideoPlayerRenderer` ，该类提供播放视频的功能。 有关这些自定义呈现器类的详细信息，请参阅 [实现视频播放器](~/xamarin-forms/app-fundamentals/custom-renderer/video-player/index.md)。

在 iOS 和通用 Windows 平台 (UWP) ， `VideoPlayerRenderer` 类具有以下构造函数，该构造函数需要 `ILogger` 参数：

```csharp
public VideoPlayerRenderer(ILogger logger)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

在所有平台上，使用依赖关系注入容器的类型注册由方法执行 `RegisterTypes` ，该方法在平台使用方法加载应用程序之前被调用 `LoadApplication(new App())` 。 下面的示例演示了 `RegisterTypes` iOS 平台上的方法：

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterType<FormsVideoLibrary.iOS.VideoPlayerRenderer>();
    App.BuildContainer();
}
```

在此示例中，将 `Logger` 通过映射对其接口类型注册具体类型，并且 `VideoPlayerRenderer` 直接注册类型而不使用接口映射。 当用户导航到包含该视图的页面时 `VideoPlayer` ，将调用该依赖关系解析方法来解析 `VideoPlayerRenderer` 依赖关系注入容器中的类型，该容器还会解析该类型并将其注入 `Logger` `VideoPlayerRenderer` 构造函数。

`VideoPlayerRenderer`Android 平台上的构造函数稍微复杂一些，因为 `Context` 除了参数外，还需要参数 `ILogger` ：

```csharp
public VideoPlayerRenderer(Context context, ILogger logger) : base(context)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

下面的示例演示了 `RegisterTypes` Android 平台上的方法：

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterTypeWithParameters<FormsVideoLibrary.Droid.VideoPlayerRenderer>(typeof(Android.Content.Context), this, typeof(ILogger), "logger");
    App.BuildContainer();
}
```

在此示例中， `App.RegisterTypeWithParameters` 方法将注册到 `VideoPlayerRenderer` 依赖关系注入容器。 注册方法确保将 `MainActivity` 实例插入为 `Context` 参数，并将该 `Logger` 类型注入为 `ILogger` 参数。

### <a name="registering-effects"></a>注册效果

该示例应用程序包括一个使用触摸跟踪效果在页面上拖动 [`BoxView`](xref:Xamarin.Forms.BoxView) 实例的页面。 [`Effect`](xref:Xamarin.Forms.Effect)使用以下代码将添加到中 `BoxView` ：

```csharp
var boxView = new BoxView { ... };
var touchEffect = new TouchEffect();
boxView.Effects.Add(touchEffect);
```

`TouchEffect`类是 [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect) 在每个平台上通过一个类实现的 `TouchEffect` `PlatformEffect` 。 Platform `TouchEffect` 类提供用于拖动页面的功能 `BoxView` 。 有关这些效果类的详细信息，请参阅 [从效果调用事件](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)。

在所有平台上， `TouchEffect` 该类具有以下需要参数的构造函数 `ILogger` ：

```csharp
public TouchEffect(ILogger logger)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

在所有平台上，使用依赖关系注入容器的类型注册由方法执行 `RegisterTypes` ，该方法在平台使用方法加载应用程序之前被调用 `LoadApplication(new App())` 。 下面的示例演示了 `RegisterTypes` Android 平台上的方法：

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterType<TouchTracking.Droid.TouchEffect>();
    App.BuildContainer();
}
```

在此示例中，将 `Logger` 通过映射对其接口类型注册具体类型，并且 `TouchEffect` 直接注册类型而不使用接口映射。 当用户导航到包含附加到该实例的实例的页面时 [`BoxView`](xref:Xamarin.Forms.BoxView) `TouchEffect` ，将调用该依赖关系解析方法来解析 `TouchEffect` 依赖关系注入容器中的平台类型，这也会解析该类型并将其注入 `Logger` `TouchEffect` 构造函数。

### <a name="registering-dependencyservice-implementations"></a>注册 DependencyService 实现

该示例应用程序包含一个页面，该页面使用 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 每个平台上的实现来允许用户从设备的图片库中选取照片。 `IPhotoPicker`接口定义由实现实现的功能 `DependencyService` ，如以下示例中所示：

```csharp
public interface IPhotoPicker
{
    Task<Stream> GetImageStreamAsync();
}
```

在每个平台项目中， `PhotoPicker` 类 `IPhotoPicker` 使用平台 api 来实现接口。 有关这些依赖关系服务的详细信息，请参阅 [从图片库中选择照片](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)。

在 iOS 和 UWP 上， `PhotoPicker` 类具有以下需要参数的构造函数 `ILogger` ：

```csharp
public PhotoPicker(ILogger logger)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

在所有平台上，使用依赖关系注入容器的类型注册由方法执行 `RegisterTypes` ，该方法在平台使用方法加载应用程序之前被调用 `LoadApplication(new App())` 。 下面的示例演示了 `RegisterTypes` UWP 上的方法：

```csharp
void RegisterTypes()
{
    DIContainerDemo.App.RegisterType<ILogger, Logger>();
    DIContainerDemo.App.RegisterType<IPhotoPicker, Services.UWP.PhotoPicker>();
    DIContainerDemo.App.BuildContainer();
}
```

在此示例中，将 `Logger` 通过映射对其接口类型注册具体类型，并且 `PhotoPicker` 还通过接口映射注册该类型。

`PhotoPicker`Android 平台上的构造函数稍微复杂一些，因为 `Context` 除了参数外，还需要参数 `ILogger` ：

```csharp
public PhotoPicker(Context context, ILogger logger)
{
    _context = context ?? throw new ArgumentNullException(nameof(context));
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

下面的示例演示了 `RegisterTypes` Android 平台上的方法：

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterTypeWithParameters<IPhotoPicker, Services.Droid.PhotoPicker>(typeof(Android.Content.Context), this, typeof(ILogger), "logger");
    App.BuildContainer();
}
```

在此示例中， `App.RegisterTypeWithParameters` 方法将注册到 `PhotoPicker` 依赖关系注入容器。 注册方法确保将 `MainActivity` 实例插入为 `Context` 参数，并将该 `Logger` 类型注入为 `ILogger` 参数。

当用户导航到照片选取页面并选择照片时，将 `OnSelectPhotoButtonClicked` 执行处理程序：

```csharp
async void OnSelectPhotoButtonClicked(object sender, EventArgs e)
{
    ...
    var photoPickerService = DependencyService.Resolve<IPhotoPicker>();
    var stream = await photoPickerService.GetImageStreamAsync();
    if (stream != null)
    {
        image.Source = ImageSource.FromStream(() => stream);
    }
    ...
}
```

[`DependencyService.Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*)调用方法时，将调用依赖项解析方法来解析 `PhotoPicker` 依赖关系注入容器中的类型，该容器还会解析该类型并将其注入 `Logger` `PhotoPicker` 构造函数。

> [!NOTE]
> [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*)当通过从应用程序的依赖关系注入容器解析类型时，必须使用方法 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 。

## <a name="related-links"></a>相关链接

- [使用容器 (示例) 的依赖项解析 ](/samples/xamarin/xamarin-forms-samples/advanced-dependencyresolution-dicontainerdemo)
- [依赖关系注入](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md)
- [实现视频播放器](~/xamarin-forms/app-fundamentals/custom-renderer/video-player/index.md)
- [从效果调用事件](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
- [从图片库中选取照片](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)