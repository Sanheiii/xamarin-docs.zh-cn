---
title: Xamarin.Forms 地图位置和距离
description: Xamarin.Forms。Maps 命名空间包含一个位置结构，该结构通常在定位地图及其引脚时使用，还包含定位地图时可选择使用的距离结构。
ms.prod: xamarin
ms.assetid: 2F4EA3D2-1351-40AD-A71D-CF7F1F18F1E8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 896b7f878e1ec9c3ed3c8324330c288220963e62
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91559709"
---
# <a name="no-locxamarinforms-map-position-and-distance"></a>Xamarin.Forms 地图位置和距离

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

[`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps)命名空间包含一个 [`Position`](xref:Xamarin.Forms.Maps.Position) 结构，该结构通常在定位地图及其引脚时使用，并包含在 [`Distance`](xref:Xamarin.Forms.Maps.Distance) 定位地图时可以选择使用的结构。

## <a name="position"></a>位置

该 [`Position`](xref:Xamarin.Forms.Maps.Position) 结构封装存储为纬度值和经度值的位置。 此结构定义了两个只读属性：

- [`Latitude`](xref:Xamarin.Forms.Maps.Position.Latitude)，类型为 `double` ，表示以十进制为单位的位置的纬度。
- [`Longitude`](xref:Xamarin.Forms.Maps.Position.Longitude)，类型为 `double` ，它表示以小数表示的位置的经度。

[`Position`](xref:Xamarin.Forms.Maps.Position) 对象是用 `Position` 构造函数创建的，该构造函数要求将纬度和经度参数指定为 `double` 值：

```csharp
Position position = new Position(36.9628066, -122.0194722);
```

当创建 `Position` 对象时，纬度值将为-90.0 和90.0 之间的限制，而经度值将为-180.0 和180.0 之间的限制。

> [!NOTE]
> `GeographyUtils`类具有一个 `ToRadians` 扩展方法，该方法将 `double` 值从度转换为弧度，并使用 `ToDegrees` 扩展方法将 `double` 值从弧度转换为度。

## <a name="distance"></a>距离

该 [`Distance`](xref:Xamarin.Forms.Maps.Distance) 结构封装作为值存储的距离 `double` ，表示以米为单位的距离。 此结构定义了三个只读属性：

- [`Kilometers`](xref:Xamarin.Forms.Maps.Distance.Kilometers)，类型为 `double` ，它表示跨越的距离（公里） `Distance` 。
- [`Meters`](xref:Xamarin.Forms.Maps.Distance.Meters)，类型为 `double` ，它表示跨越的距离（以米为单位） `Distance` 。
- [`Miles`](xref:Xamarin.Forms.Maps.Distance.Miles)，类型为 `double` ，表示跨越的距离（以英里为单位） `Distance` 。

[`Distance`](xref:Xamarin.Forms.Maps.Distance) 可以使用构造函数创建对象 `Distance` ，这需要将计量参数指定为 `double` ：

```csharp
Distance distance = new Distance(1450.5);
```

另外，还 [`Distance`](xref:Xamarin.Forms.Maps.Distance) 可以通过 [`FromKilometers`](xref:Xamarin.Forms.Maps.Distance.FromKilometers*) 、 [`FromMeters`](xref:Xamarin.Forms.Maps.Distance.FromMeters*) 、 [`FromMiles`](xref:Xamarin.Forms.Maps.Distance.FromMiles*) 和 `BetweenPositions` 工厂方法来创建对象：

```csharp
Distance distance1 = Distance.FromKilometers(1.45); // argument represents the number of kilometers
Distance distance2 = Distance.FromMeters(1450.5);   // argument represents the number of meters
Distance distance3 = Distance.FromMiles(0.969);     // argument represents the number of miles
Distance distance4 = Distance.BetweenPositions(position1, position2);
```

## <a name="related-links"></a>相关链接

- [地图示例](/samples/xamarin/xamarin-forms-samples/workingwithmaps)