---
title: Xamarin.Essentials 地图
description: Xamarin.Essentials 中的 Map 类使应用程序可以将已安装的地图应用程序打开到特定位置或地标。
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 05/26/2020
ms.custom: video
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b566b6705d1cd8e229b6a2636fffd2ebc2ed5cde
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2020
ms.locfileid: "84802261"
---
# <a name="xamarinessentials-map"></a>Xamarin.Essentials：地图

Map 类使应用程序可以将已安装的地图应用程序打开到特定位置或地标。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-map"></a>使用 Map

在类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

Map 功能通过调用具有 `Location` 或 `Placemark` 的 `OpenAsync` 方法来使用可选的 `MapLaunchOptions` 打开。

```csharp
public class MapTest
{
    public async Task NavigateToBuilding25()
    {
        var location = new Location(47.645160, -122.1306032);
        var options =  new MapLaunchOptions { Name = "Microsoft Building 25" };

        try
        {
            await Map.OpenAsync(location, options);
        }
        catch (Exception ex)
        {
            // No map application available to open
        }
    }
}
```

使用 `Placemark` 打开时，需要以下信息：

- `CountryName`
- `AdminArea`
- `Thoroughfare`
- `Locality`

```csharp
public class MapTest
{
    public async Task NavigateToBuilding25()
    {
        var placemark = new Placemark
            {
                CountryName = "United States",
                AdminArea = "WA",
                Thoroughfare = "Microsoft Building 25",
                Locality = "Redmond"
            };
        var options =  new MapLaunchOptions { Name = "Microsoft Building 25" };

        try
        {
            await Map.OpenAsync(placemark, options);
        }
        catch (Exception ex)
        {
            // No map application available to open or placemark can not be located
        }
    }
}
```

## <a name="extension-methods"></a>扩展方法

如果已有对 `Location` 或 `Placemark` 的引用，则可以使用具有可选的 `MapLaunchOptions` 的内置扩展方法 `OpenMapAsync`：

```csharp
public class MapTest
{
    public async Task OpenPlacemarkOnMap(Placemark placemark)
    {
        try
        {
            await placemark.OpenMapAsync();
        }
        catch (Exception ex)
        {
            // No map application available to open
        }
    }
}
```

## <a name="directions-mode"></a>方向模式

如果调用不带任何 `MapLaunchOptions` 的 `OpenMapAsync`，则地图将启动到指定位置。 （可选）可以有从设备的当前位置开始计算的导航路线。 这是通过设置 `MapLaunchOptions` 上的 `NavigationMode` 来完成的：

```csharp
public class MapTest
{
    public async Task NavigateToBuilding25()
    {
        var location = new Location(47.645160, -122.1306032);
        var options =  new MapLaunchOptions { NavigationMode = NavigationMode.Driving };

        await Map.OpenAsync(location, options);
    }
}
```

## <a name="platform-differences"></a>平台差异

# <a name="android"></a>[Android](#tab/android)

- `NavigationMode` 支持骑行、驾车和步行。

# <a name="ios"></a>[iOS](#tab/ios)

- `NavigationMode` 支持驾车、公交和步行。

# <a name="uwp"></a>[UWP](#tab/uwp)

- `NavigationMode` 支持驾车、公交和步行。

--------------

## <a name="platform-implementation-specifics"></a>平台实现细节

# <a name="android"></a>[Android](#tab/android)

Android 使用 `geo:` URI 方案启动设备上的地图应用程序。 这可能会提示用户从支持此 URI 方案的现有应用程序中进行选择。  Xamarin.Essentials 使用支持此方案的 Google Maps 进行测试。

# <a name="ios"></a>[iOS](#tab/ios)

无特定于平台的实现细节。

# <a name="uwp"></a>[UWP](#tab/uwp)

无特定于平台的实现细节。

--------------

## <a name="api"></a>API

- [Map 源代码](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Map)
- [Map API 文档](xref:Xamarin.Essentials.Map)

## <a name="related-video"></a>相关视频

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Maps-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
