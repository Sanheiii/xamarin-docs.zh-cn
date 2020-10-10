---
title: Xamarin.Forms DependencyService 简介
description: 本文介绍如何使用 Xamarin.Forms DependencyService 类调用本机平台功能。
ms.prod: xamarin
ms.assetid: 5d019604-4f6f-4932-9b26-1fce3b4d88f8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/12/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2d63ac91031fc4a2cc127272a075f15a8ea81f4e
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91555277"
---
# <a name="no-locxamarinforms-dependencyservice-introduction"></a>Xamarin.Forms DependencyService 简介

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/dependencyservice/)

[`DependencyService`](xref:Xamarin.Forms.DependencyService) 类是允许 Xamarin.Forms 应用程序从共享代码调用本机平台功能的服务定位器。

使用 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 调用本机平台功能的过程用于：

1. 在共享代码中创建本机平台功能的接口。 有关详细信息，请参阅[创建接口](#create-an-interface)。
1. 在所需的平台项目中实现此接口。 有关详细信息，请参阅[在各个平台上实现接口](#implement-the-interface-on-each-platform)。
1. 通过 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 注册平台实现。 这使得 Xamarin.Forms 可以在运行时找到平台实现。 有关详细信息，请参阅[注册平台实现](#register-the-platform-implementations)。
1. 从共享代码解析平台实现，并调用它们。 有关详细信息，请参阅[解析平台实现](#resolve-the-platform-implementations)。

下图说明了如何在 Xamarin.Forms 应用程序中调用本机平台功能：

![使用 Xamarin.FormsDependencyService 类执行服务定位概述](introduction-images/dependency-service.png "DependencyService 服务定位")

## <a name="create-an-interface"></a>创建接口

若要能够从共享代码调用本机平台功能，第一个步骤是创建接口来定义与本机平台功能交互的 API。 应将此接口置于共享代码项目中。

下面的示例说明了可用于检索设备方向的 API 接口：

```csharp
public interface IDeviceOrientationService
{
    DeviceOrientation GetOrientation();
}
```

## <a name="implement-the-interface-on-each-platform"></a>在各个平台上实现此接口

创建定义与本机平台功能交互的 API 的接口后，必须在各个平台项目中实现此接口。

### <a name="ios"></a>iOS

下面的代码示例演示 iOS 上的 `IDeviceOrientationService` 接口实现：

```csharp
namespace DependencyServiceDemos.iOS
{
    public class DeviceOrientationService : IDeviceOrientationService
    {
        public DeviceOrientation GetOrientation()
        {
            UIInterfaceOrientation orientation = UIApplication.SharedApplication.StatusBarOrientation;

            bool isPortrait = orientation == UIInterfaceOrientation.Portrait ||
                orientation == UIInterfaceOrientation.PortraitUpsideDown;
            return isPortrait ? DeviceOrientation.Portrait : DeviceOrientation.Landscape;
        }
    }
}
```

### <a name="android"></a>Android

下面的代码示例演示 Android 上的 `IDeviceOrientationService` 接口实现：

```csharp
namespace DependencyServiceDemos.Droid
{
    public class DeviceOrientationService : IDeviceOrientationService
    {
        public DeviceOrientation GetOrientation()
        {
            IWindowManager windowManager = Android.App.Application.Context.GetSystemService(Context.WindowService).JavaCast<IWindowManager>();

            SurfaceOrientation orientation = windowManager.DefaultDisplay.Rotation;
            bool isLandscape = orientation == SurfaceOrientation.Rotation90 ||
                orientation == SurfaceOrientation.Rotation270;
            return isLandscape ? DeviceOrientation.Landscape : DeviceOrientation.Portrait;
        }
    }
}
```

### <a name="universal-windows-platform"></a>通用 Windows 平台

以下代码示例展示了通用 Windows 平台 (UWP) 上的 `IDeviceOrientationService` 接口实现：

```csharp
namespace DependencyServiceDemos.UWP
{
    public class DeviceOrientationService : IDeviceOrientationService
    {
        public DeviceOrientation GetOrientation()
        {
            ApplicationViewOrientation orientation = ApplicationView.GetForCurrentView().Orientation;
            return orientation == ApplicationViewOrientation.Landscape ? DeviceOrientation.Landscape : DeviceOrientation.Portrait;
        }
    }
}
```

## <a name="register-the-platform-implementations"></a>注册平台实现

在各个平台项目中实现此接口后，必须通过 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 注册平台实现，Xamarin.Forms 才可以在运行时找到它们。 通常使用 [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) 完成此操作，它指示指定的类型提供接口实现。

下面的示例演示如何使用 [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) 注册 `IDeviceOrientationService` 接口的 iOS 实现：

```csharp
using Xamarin.Forms;

[assembly: Dependency(typeof(DependencyServiceDemos.iOS.DeviceOrientationService))]
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

在此示例中，[`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) 通过 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 注册 `DeviceOrientationService`。 同样，其他平台上的 `IDeviceOrientationService` 接口的实现也应通过 [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) 注册。

有关通过 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 注册平台实现的详细信息，请参阅 [Xamarin.Forms DependencyService 注册和解析](registration-and-resolution.md)。

## <a name="resolve-the-platform-implementations"></a>解析平台实现

通过 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 注册平台实现后，必须先解析实现，才能调用它们。 通常使用 [`DependencyService.Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) 方法在共享代码中完成此操作。

下面的代码示例说明了如何调用 [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) 方法解析 `IDeviceOrientationService` 接口并调用 `GetOrientation` 方法：

```csharp
IDeviceOrientationService service = DependencyService.Get<IDeviceOrientationService>();
DeviceOrientation orientation = service.GetOrientation();
```

也可以将此代码压缩到一行：

```csharp
DeviceOrientation orientation = DependencyService.Get<IDeviceOrientationService>().GetOrientation();
```

有关通过 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 解析平台实现的详细信息，请参阅 [Xamarin.Forms DependencyService 注册和解析](registration-and-resolution.md)。

## <a name="related-links"></a>相关链接

- [DependencyService 演示（示例）](/samples/xamarin/xamarin-forms-samples/dependencyservice/)
- [Xamarin.Forms DependencyService 注册和解析](registration-and-resolution.md)