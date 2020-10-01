---
title: Xamarin.Essentials：地理位置
description: 本文档介绍 Xamarin.Essentials 中的 Geolocation 类，此类提供 API 以检索设备的当前地理位置坐标。
ms.assetid: 8F66092C-13F0-4FEE-8AA5-901D5F79B357
author: jamesmontemagno
ms.custom: video
ms.author: jamont
ms.date: 03/13/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4a671be5f65e0e35c89f4acec17f406a214b9fa9
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434621"
---
# <a name="no-locxamarinessentials-geolocation"></a>Xamarin.Essentials：地理位置

Geolocation 提供 API 以检索设备的当前地理位置坐标。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

若要访问 Geolocation 功能，需要以下特定于平台的设置：

# <a name="android"></a>[Android](#tab/android)

需要具有 Coarse 和 Fine Location 权限，并且必须在 Android 项目中进行配置。 此外，如果应用面向 Android 5.0（API 级别 21）或更高版本，则必须声明应用使用清单文件中的硬件功能。 可以通过以下方法添加此声明：

打开 Properties 文件夹下的 AssemblyInfo.cs 文件并添加 ：

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.AccessCoarseLocation)]
[assembly: UsesPermission(Android.Manifest.Permission.AccessFineLocation)]
[assembly: UsesFeature("android.hardware.location", Required = false)]
[assembly: UsesFeature("android.hardware.location.gps", Required = false)]
[assembly: UsesFeature("android.hardware.location.network", Required = false)]
```

或更新 Android 清单：

打开 Properties 文件夹下的 AndroidManifest.xml 文件，并在“manifest”节点内添加以下代码  ：

```xml
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-feature android:name="android.hardware.location" android:required="false" />
<uses-feature android:name="android.hardware.location.gps" android:required="false" />
<uses-feature android:name="android.hardware.location.network" android:required="false" />
```

或右键单击 Android 项目并打开项目的属性。 在“Android 清单”下找到“所需权限:”区域，然后选中“ACCESS_COARSE_LOCATION”和“ACCESS_FINE_LOCATION”权限   。 这样会自动更新 AndroidManifest.xml 文件。

[!include[](~/essentials/includes/android-permissions.md)]

# <a name="ios"></a>[iOS](#tab/ios)

应用的 Info.plist 必须包含 `NSLocationWhenInUseUsageDescription` 密钥才能访问设备的位置。

打开 plist 编辑器并添加“隐私 - 使用时的位置使用说明”属性并填写一个值以显示给用户。

或手动编辑文件并添加以下内容，然后更新基本原理：

```xml
<key>NSLocationWhenInUseUsageDescription</key>
<string>Fill in a reason why your app needs access to location.</string>
```

# <a name="uwp"></a>[UWP](#tab/uwp)

必须设置应用程序的 `Location` 权限。 可以通过打开 Package.appxmanifest 并选择“功能”选项卡，然后选中“位置”来完成此设置  。

-----

## <a name="using-geolocation"></a>使用 Geolocation

在类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

Geolocation API 还将在必要时提醒用户所需的权限。

通过调用 `GetLastKnownLocationAsync` 方法获取设备上次的已知[位置](xref:Xamarin.Essentials.Location)。 这通常比执行完整的查询更快，但可能不太准确，如果不存在缓存位置，还可能会返回 `null`。

```csharp
try
{
    var location = await Geolocation.GetLastKnownLocationAsync();

    if (location != null)
    {
        Console.WriteLine($"Latitude: {location.Latitude}, Longitude: {location.Longitude}, Altitude: {location.Altitude}");
    }
}
catch (FeatureNotSupportedException fnsEx)
{
    // Handle not supported on device exception
}
catch (FeatureNotEnabledException fneEx)
{
    // Handle not enabled on device exception
}
catch (PermissionException pEx)
{
    // Handle permission exception
}
catch (Exception ex)
{
    // Unable to get location
}
```

高度并非总是可用的。 如果不可用，`Altitude` 属性可能为 `null` 或值可能为零。 如果高度可用，此值为海拔高度（以米为单位）。

若要查询当前设备的[位置](xref:Xamarin.Essentials.Location)坐标，可以使用 `GetLocationAsync`。 最好传入一个完整的 `GeolocationRequest` 和 `CancellationToken`，因为获取设备的位置可能需要一些时间。

```csharp
try
{
    var request = new GeolocationRequest(GeolocationAccuracy.Medium);
    var location = await Geolocation.GetLocationAsync(request);

    if (location != null)
    {
        Console.WriteLine($"Latitude: {location.Latitude}, Longitude: {location.Longitude}, Altitude: {location.Altitude}");
    }
}
catch (FeatureNotSupportedException fnsEx)
{
    // Handle not supported on device exception
}
catch (FeatureNotEnabledException fneEx)
{
    // Handle not enabled on device exception
}
catch (PermissionException pEx)
{
    // Handle permission exception
}
catch (Exception ex)
{
    // Unable to get location
}
```

## <a name="geolocation-accuracy"></a>地理位置的准确性

下表概述了每个平台的准确性：

### <a name="lowest"></a>最低

| Platform | 距离（以米为单位） |
| --- | --- |
| Android | 500 |
| iOS | 3000 |
| UWP | 1000 - 5000 |

### <a name="low"></a>低

| Platform | 距离（以米为单位） |
| --- | --- |
| Android | 500 |
| iOS | 1000 |
| UWP | 300 - 3000 |

### <a name="medium-default"></a>中等（默认值）

| 平台 | 距离（以米为单位） |
| --- | --- |
| Android | 100 - 500 |
| iOS | 100 |
| UWP | 30-500 |

### <a name="high"></a>高

| Platform | 距离（以米为单位） |
| --- | --- |
| Android | 0 - 100 |
| iOS | 10 |
| UWP | <= 10 |

### <a name="best"></a>最佳

| Platform | 距离（以米为单位） |
| --- | --- |
| Android | 0 - 100 |
| iOS | ~0 |
| UWP | <= 10 |

<a name="calculate-distance"></a>

## <a name="detecting-mock-locations"></a>检测模拟位置
一些设备可能会从提供程序或通过提供模拟位置的应用程序返回模拟位置。 可以通过使用位于任何 [`Location`](xref:Xamarin.Essentials.Location) 的 `IsFromMockProvider` 进行检测。

```csharp
var request = new GeolocationRequest(GeolocationAccuracy.Medium);
var location = await Geolocation.GetLocationAsync(request);

if (location != null)
{
    if(location.IsFromMockProvider)
    {
        // location is from a mock provider
    }
}
```

## <a name="distance-between-two-locations"></a>两个位置之间的距离

[`Location`](xref:Xamarin.Essentials.Location) 和 [`LocationExtensions`](xref:Xamarin.Essentials.LocationExtensions) 类定义了 `CalculateDistance` 方法，可用于计算两个地理位置之间的距离。 此计算得出的距离不考虑道路或其他路径，仅仅是沿着地球表面的两点之间的最短距离，也称为大圆距离或通俗地称为“直线距离”。

以下是一个示例：

```csharp
Location boston = new Location(42.358056, -71.063611);
Location sanFrancisco = new Location(37.783333, -122.416667);
double miles = Location.CalculateDistance(boston, sanFrancisco, DistanceUnits.Miles);
```

`Location` 构造函数具有按该顺序排列的纬度和经度参数。 正纬度值表示位于赤道以北，正经度值表示位于本初子午线以东。 使用 `CalculateDistance` 的最后一个参数指定单位为英里还是公里。 `UnitConverters` 类还定义了用于在两个单位之间进行转换的 `KilometersToMiles` 和 `MilesToKilometers` 方法。

## <a name="platform-differences"></a>平台差异

每个平台上计算海拔高度的方式不同。

# <a name="android"></a>[Android](#tab/android)

在 Android 上，[海拔高度](https://developer.android.com/reference/android/location/Location#getAltitude())（如果可用）返回高于 WGS 84 参考椭球的米数。 如果此位置没有海拔高度，则返回 0.0。

# <a name="ios"></a>[iOS](#tab/ios)

在 iOS 上，[海拔高度](https://developer.apple.com/documentation/corelocation/cllocation/1423820-altitude)以米为单位。 正值指示海拔高于海平面，而负值表明海拔低于海平面。

# <a name="uwp"></a>[UWP](#tab/uwp)

UWP 上以米为单位返回海拔高度。 有关详细信息，请参阅 [AltitudeReferenceSystem](/uwp/api/windows.devices.geolocation.geopoint.altitudereferencesystem#Windows_Devices_Geolocation_Geopoint_AltitudeReferenceSystem) 文档。

-----

## <a name="api"></a>API

- [Geolocation 源代码](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Geolocation)
- [Geolocation API 文档](xref:Xamarin.Essentials.Geolocation)

## <a name="related-video"></a>相关视频

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Geolocation-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]