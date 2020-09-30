---
title: Xamarin.Forms 地图地理编码
description: 本文说明如何使用地理编码和反向地理编码映射数据 Xamarin.Forms 。Maps Geocoder 类。
ms.prod: xamarin
ms.assetid: DE7DB31A-8921-4614-8B49-DAEF1E7B03B3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/22/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d6a63e952d7466dc4b7f2d3e8cfc39d6c63e4daf
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91559969"
---
# <a name="no-locxamarinforms-map-geocoding"></a>Xamarin.Forms 地图地理编码

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

[`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps)命名空间提供了 [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) 一个类，该类在存储在对象中的字符串地址、纬度和经度坐标之间进行转换 [`Position`](xref:Xamarin.Forms.Maps.Position) 。 有关结构的详细信息 [`Position`](xref:Xamarin.Forms.Maps.Position) ，请参阅 [地图位置和距离](position-distance.md)。

> [!NOTE]
> 替代的地理编码 API 是提供的 Xamarin.Essentials 。 Xamarin.Essentials `Geocoding` 地理编码地址时，api 提供结构化地址数据，而不是此 api 返回的字符串。 有关详细信息，请参阅[ Xamarin.Essentials ：地理编码](~/essentials/geocoding.md)。

## <a name="geocode-an-address"></a>地理编码地址

街道地址可以通过创建 [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) 实例并对实例调用方法来地理编码到纬度和经度坐标 [`GetPositionsForAddressAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync*) `Geocoder` ：

```csharp
using Xamarin.Forms.Maps;
// ...
Geocoder geoCoder = new Geocoder();

IEnumerable<Position> approximateLocations = await geoCoder.GetPositionsForAddressAsync("Pacific Ave, San Francisco, California");
Position position = approximateLocations.FirstOrDefault();
string coordinates = $"{position.Latitude}, {position.Longitude}";
```

[`GetPositionsForAddressAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync*)方法采用一个 `string` 表示该地址的参数，并以异步方式返回 [`Position`](xref:Xamarin.Forms.Maps.Position) 可能表示该地址的对象的集合。

## <a name="reverse-geocode-an-address"></a>反向地理编码地址

可以通过创建 [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) 实例并对实例调用方法，将纬度和经度坐标反向地理编码为街道地址 [`GetAddressesForPositionAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetAddressesForPositionAsync*) `Geocoder` ：

```csharp
using Xamarin.Forms.Maps;
// ...
Geocoder geoCoder = new Geocoder();

Position position = new Position(37.8044866, -122.4324132);
IEnumerable<string> possibleAddresses = await geoCoder.GetAddressesForPositionAsync(position);
string address = possibleAddresses.FirstOrDefault();
```

[`GetAddressesForPositionAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetAddressesForPositionAsync*)方法采用 [`Position`](xref:Xamarin.Forms.Maps.Position) 由纬度和经度坐标组成的自变量，并且以异步方式返回字符串的集合，这些字符串表示位置附近的地址。

## <a name="related-links"></a>相关链接

- [地图示例](/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Xamarin.Forms 地图位置和距离](position-distance.md)
- [Geocoder API](xref:Xamarin.Forms.Maps.Geocoder)