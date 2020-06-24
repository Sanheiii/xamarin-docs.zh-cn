---
title: Xamarin.Essentials：设备信息
description: 本文档介绍 Xamarin.Essentials 中的 DeviceInfo 类，此类提供有关运行应用程序的设备的信息。
ms.assetid: A1AC5373-926A-4FB6-8D7D-4B87EB8EB522
author: jamesmontemagno
ms.custom: video
ms.author: jamont
ms.date: 11/04/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 70619097baa2c5f10321835b087f693c4fbac0c4
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2020
ms.locfileid: "84802386"
---
# <a name="xamarinessentials-device-information"></a>Xamarin.Essentials：设备信息

DeviceInfo 类提供有关运行应用程序的设备的信息。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-deviceinfo"></a>使用 DeviceInfo

在类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

通过 API 公开了以下信息：

```csharp
// Device Model (SMG-950U, iPhone10,6)
var device = DeviceInfo.Model;

// Manufacturer (Samsung)
var manufacturer = DeviceInfo.Manufacturer;

// Device Name (Motz's iPhone)
var deviceName = DeviceInfo.Name;

// Operating System Version Number (7.0)
var version = DeviceInfo.VersionString;

// Platform (Android)
var platform = DeviceInfo.Platform;

// Idiom (Phone)
var idiom = DeviceInfo.Idiom;

// Device Type (Physical)
var deviceType = DeviceInfo.DeviceType;
```

## <a name="platforms"></a>平台

[`DeviceInfo.Platform`](xref:Xamarin.Essentials.DeviceInfo.Platform) 与映射到操作系统的一个常量字符串相关联。 可以使用 `DevicePlatform` 结构检查以下值：

- DevicePlatform.iOS – iOS
- DevicePlatform.Android – Android
- DevicePlatform.UWP – UWP
- DevicePlatform.Unknown – 未知

## <a name="idioms"></a>习惯用语

[`DeviceInfo.Idiom`](xref:Xamarin.Essentials.DeviceInfo.Idiom) 与映射到运行应用程序的设备类型的一个常量字符串相关联。 可以使用 `DeviceIdiom` 结构检查以下值：

- DeviceIdiom.Phone – 电话
- DeviceIdiom.Tablet – 平板电脑
- DeviceIdiom.Desktop – 台式计算机
- DeviceIdiom.TV – TV
- DeviceIdiom.Watch – 手表
- DeviceIdiom.Unknown – 未知

## <a name="device-type"></a>设备类型

`DeviceInfo.DeviceType` 关联一个枚举以确定应用程序是在物理设备还是虚拟设备上运行。 虚拟设备是指模拟器或仿真程序。

## <a name="platform-implementation-specifics"></a>平台实现细节

# <a name="ios"></a>[iOS](#tab/ios)

iOS 不会向开发人员公开一个 API 来获取特定 iOS 设备的型号。 而是会返回一个硬件标识符，例如 iPhone10、6，这是指 iPhone X。这些标识符的映射不是由 Apple 提供，但可以在 [The iPhone Wiki](https://www.theiphonewiki.com/wiki/Models) 和 [Get iOS Model](https://github.com/dannycabrera/Get-iOS-Model) 这两个非官方来源上找到。

--------------

## <a name="api"></a>API

- [DeviceInfo 源代码](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/DeviceInfo)
- [DeviceInfo API 文档](xref:Xamarin.Essentials.DeviceInfo)

## <a name="related-video"></a>相关视频

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Device-Information-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
