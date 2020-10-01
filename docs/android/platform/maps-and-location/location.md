---
title: Android 上的位置服务
description: 本指南介绍 Android 应用程序中的位置感知，并说明如何使用 Android 位置服务 API 以及 Google 位置服务 API 提供的融合位置提供程序来获取用户的位置。
ms.prod: xamarin
ms.assetid: 0008682B-6CEF-0C1D-3200-56ECF58F5D3C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/22/2018
ms.openlocfilehash: 37bfed9a2b49be2d04f58dab70d3185418f0f67d
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457466"
---
# <a name="location-services-on-android"></a>Android 上的位置服务

本指南介绍 Android 应用程序中的位置感知，并说明如何使用 Android 位置服务 API 以及 Google 位置服务 API 提供的融合位置提供程序来获取用户的位置。

Android 提供了多种定位技术，例如蜂窝塔定位、Wi-Fi 和 GPS。 每种定位技术的详细信息都通过位置提供程序进行抽象化，以便无论使用何种提供程序，应用程序都能够以相同的方式获取位置。 本指南介绍 Google Play Services 中的融合位置提供程序，它可根据可用的提供程序以及设备的使用方式，智能地确定获取设备位置的最佳方法。 Android 位置服务 API，并演示如何使用 `LocationManager` 与系统位置服务进行通信。 本指南的第二部分使用 `LocationManager` 介绍 Android 位置服务 API。

按照惯例，应用程序应优先使用融合位置提供程序，仅在必要时才会回退到较旧的 Android 位置服务 API。

## <a name="location-fundamentals"></a>位置基础知识

在 Android 中，无论选择使用何种 API 处理位置数据，有几个概念都是相同的。 本部分介绍位置提供程序和与位置相关的权限。

### <a name="location-providers"></a>位置提供程序

在内部使用多种技术来确定用户的位置。 所使用的硬件取决于为收集数据而选择的位置提供程序的类型。 Android 使用三种位置提供程序：

- GPS 提供程序 &ndash; GPS 可提供最准确的位置，能耗最高，并且在户外使用效果最佳。 该提供程序结合使用 GPS 和辅助 GPS ([aGPS](https://en.wikipedia.org/wiki/Assisted_GPS))，返回通过蜂窝塔收集的 GPS 数据。

- 网络提供程序 &ndash; 同时提供 WiFi 和蜂窝数据，包括蜂窝塔收集的 aGPS 数据。 与 GPS 提供程序相比，它的能耗更低，但是返回的位置数据的准确度不定。

- 无源提供程序 &ndash; 一种“piggyback”选项，使用其他应用程序或服务请求的提供程序在应用程序中生成位置数据。 此选项可靠性较差但能耗低，非常适用于无需持续位置更新的应用程序。

位置提供程序并非始终可用。 例如，我们可能希望对应用程序使用 GPS，但是“设置”中可能关闭了 GPS，或者设备可能根本没有 GPS。 如果特定的提供程序不可用，则选择该提供程序可能会返回 `null`。

### <a name="location-permissions"></a>位置权限

位置感知应用程序需要访问设备的硬件传感器以接收 GPS、Wi-Fi 和蜂窝数据。 通过应用程序的 Android 清单中的相应权限来控制访问权限。
有两个可用的权限 &ndash; 根据应用程序的要求和选择的 API，将需要允许其中一种权限：

- `ACCESS_FINE_LOCATION` &ndash; 允许应用程序访问 GPS。
    针对 GPS 提供程序和无源提供程序选项，此权限为必选权限（无源提供程序需有权访问其他应用程序或服务收集的 GPS 数据）  。 对于网络提供程序，此权限为可选权限。

- `ACCESS_COARSE_LOCATION` &ndash; 允许应用程序访问蜂窝和 Wi-Fi 位置。 如果未设置 `ACCESS_FINE_LOCATION`，则对于网络提供程序，此权限为必选权限。

对于面向 API 版本 21 (Android 5.0 Lollipop) 或更高版本的应用，可以启用 `ACCESS_FINE_LOCATION` 并继续在没有 GPS 硬件的设备上运行。 如果应用需要 GPS 硬件，则应在 Android 清单中显式添加 `android.hardware.location.gps` `uses-feature` 元素。 有关详细信息，请参阅 Android [uses-feature](https://developer.android.com/guide/topics/manifest/uses-feature-element.html) 元素引用。

要设置权限，请展开“Solution Pad”的“属性”文件夹，然后双击“AndroidManifest.xml”  。 这些权限将在“所需权限”下列出：

[![Android 清单“所需权限”设置的屏幕截图](location-images/location-01-xs.png)](location-images/location-01-xs.png#lightbox)

设置这些权限中的任一权限都会告知 Android，应用程序需要用户的许可才能访问位置提供程序。 每次安装应用时，运行 API 级别 22 (Android 5.1) 或更低版本的设备都会要求用户授予这些权限。 在运行 API 级别 23 (Android 6.0) 或更高版本的设备上，应用应在向位置提供程序发出请求之前执行运行时权限检查。 

> [!NOTE]
>注意：设置 `ACCESS_FINE_LOCATION` 表示可同时访问粗略和精细的位置数据。 无需设置两个权限，仅设置应用运行所需的最低权限即可。

此代码片段示例演示了如何检查应用是否有权拥有 `ACCESS_FINE_LOCATION` 权限：

```csharp
 if (ContextCompat.CheckSelfPermission(this, Manifest.Permission.AccessFineLocation) == Permission.Granted)
{
    StartRequestingLocationUpdates();
    isRequestingLocationUpdates = true;
}
else
{
    // The app does not have permission ACCESS_FINE_LOCATION 
}
```

应用必须能够容忍用户不授予任何权限（或已撤消权限）的情况，并有办法妥善处理这种情况。 有关如何在 Xamarin.Android 中实现运行时权限检查的更多详细信息，请参阅[权限指南](~/android/app-fundamentals/permissions.md)。

## <a name="using-the-fused-location-provider"></a>使用融合位置提供程序

融合位置提供程序是 Android 应用程序从设备接收位置更新的首选方式，因为它会在运行时有效地选择位置提供程序，从而以省电的方式提供最佳位置信息。 例如，在户外行走的用户可以通过 GPS 获得最佳位置信息。 如果该用户随后走进室内，而 GPS 效果不佳（或者完全无效），则融合位置提供程序可能会自动切换到在室内效果更好的 WiFi。

融合位置提供程序 API 提供了多种其他工具来支持位置感知应用程序，包括地理围栏和活动监视。 在本部分中，我们将重点介绍设置 `LocationClient`、建立提供程序以及获取用户位置的基础知识。

融合位置提供程序是 [Google Play Services](https://developer.android.com/google/play-services/index.html) 的一部分。
必须在应用程序中正确安装和配置 Google Play Services 包才能使融合位置提供程序 API 正常工作，并且设备必须安装 Google Play Services APK。

在 Xamarin.Android 应用程序可以使用融合位置提供程序之前，必须向项目添加 Xamarin.GooglePlayServices.Location 包。 此外，还应在引用下述类的任何源文件中添加以下 `using` 语句：

```csharp
using Android.Gms.Common;
using Android.Gms.Location;
```

### <a name="checking-if-google-play-services-is-installed"></a>检查是否已安装 Google Play Services

在未安装 Google Play Services（或已过时）时，如果 Xamarin.Android 尝试使用融合位置提供程序，它将会崩溃，然后会发生运行时异常。  如果未安装 Google Play Services，则应用程序应回退到上述的 Android 位置服务。 如果 Google Play Services 已过时，则应用可能会向用户显示一条消息，要求他们更新已安装的 Google Play Services 版本。

此代码片段示例演示了 Android 活动如何以编程方式检查是否已安装 Google Play Services：

```csharp
bool IsGooglePlayServicesInstalled()
{
    var queryResult = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable(this);
    if (queryResult == ConnectionResult.Success)
    {
        Log.Info("MainActivity", "Google Play Services is installed on this device.");
        return true;
    }

    if (GoogleApiAvailability.Instance.IsUserResolvableError(queryResult))
    {
        // Check if there is a way the user can resolve the issue
        var errorString = GoogleApiAvailability.Instance.GetErrorString(queryResult);
        Log.Error("MainActivity", "There is a problem with Google Play Services on this device: {0} - {1}",
                  queryResult, errorString);

        // Alternately, display the error to the user.
    }

    return false;
}
```

### <a name="fusedlocationproviderclient"></a>FusedLocationProviderClient

要与融合位置提供程序进行交互，Xamarin.Android 应用程序必须具有 `FusedLocationProviderClient` 的实例。 此类公开了订阅位置更新以及检索设备的最新已知位置的必要方法。

活动的 `OnCreate` 方法是获取对 `FusedLocationProviderClient` 的引用的合适位置，如以下代码片段所示：

```csharp
public class MainActivity: AppCompatActivity
{
    FusedLocationProviderClient fusedLocationProviderClient;

    protected override void OnCreate(Bundle bundle) 
    {
        fusedLocationProviderClient = LocationServices.GetFusedLocationProviderClient(this);
    }
}
```

### <a name="getting-the-last-known-location"></a>获取最新已知位置

`FusedLocationProviderClient.GetLastLocationAsync()` 方法为 Xamarin.Android 应用程序提供了一种简单、顺畅的方式，可使用最少的编码开销快速获取设备的最新已知位置。

此代码片段演示如何使用 `GetLastLocationAsync` 方法检索设备的位置：

```csharp
async Task GetLastLocationFromDevice()
{
    // This method assumes that the necessary run-time permission checks have succeeded.
    getLastLocationButton.SetText(Resource.String.getting_last_location);
    Android.Locations.Location location = await fusedLocationProviderClient.GetLastLocationAsync();

    if (location == null)
    {
        // Seldom happens, but should code that handles this scenario
    }
    else
    {
        // Do something with the location 
        Log.Debug("Sample", "The latitude is " + location.Latitude);
    }
}
```

### <a name="subscribing-to-location-updates"></a>订阅位置更新

Xamarin.Android 应用程序还可以使用 `FusedLocationProviderClient.RequestLocationUpdatesAsync` 方法从融合位置提供程序订阅位置更新，如以下代码片段所示：

```csharp
await fusedLocationProviderClient.RequestLocationUpdatesAsync(locationRequest, locationCallback);
```

此方法采用以下两种参数：

- `Android.Gms.Location.LocationRequest` &ndash; `LocationRequest` 对象是指 Xamarin.Android 应用程序如何传递有关融合位置提供程序的工作方式的参数。 `LocationRequest` 会保存一些信息，例如应发出请求的频率或准确的位置更新的重要性。 例如，重要的位置请求将导致设备在确定位置时使用 GPS，从而消耗更多电量。 此代码片段演示了如何针对某个位置创建高准确度的 `LocationRequest` - 大约每五分钟检查一次位置更新（但两次请求之间的间隔不得少于两分钟）。 融合位置提供程序将使用 `LocationRequest` 作为指导，用于在尝试确定设备位置时选择要使用的位置提供程序：

    ```csharp
    LocationRequest locationRequest = new LocationRequest()
                                      .SetPriority(LocationRequest.PriorityHighAccuracy)
                                      .SetInterval(60 * 1000 * 5)
                                      .SetFastestInterval(60 * 1000 * 2);
    ```

- `Android.Gms.Location.LocationCallback` &ndash; 为了接收位置更新，Xamarin.Android 应用程序必须将 `LocationProvider` 抽象类作为子类。 此类公开了两个方法，融合位置提供程序可能会调用这些方法，以更新应用的位置信息。 下面将更详细地讨论此内容。

为通知 Xamarin.Android 应用程序位置更新信息，融合位置提供程序将调用 `LocationCallBack.OnLocationResult(LocationResult result)`。 `Android.Gms.Location.LocationResult` 参数将包含位置更新信息。

当融合位置提供程序检测到位置数据的可用性发生变化时，它将调用 `LocationProvider.OnLocationAvailability(LocationAvailability
locationAvailability)` 方法。 如果 `LocationAvailability.IsLocationAvailable` 属性返回 `true`，则可以假定 `OnLocationResult` 报告的设备位置结果符合 `LocationRequest` 所要求的准确性和时效性。 如果 `IsLocationAvailable` 为 false，则 `OnLocationResult` 将不会返回任何位置结果。

此代码片段示例演示如何实现 `LocationCallback` 对象：

```csharp
public class FusedLocationProviderCallback : LocationCallback
{
    readonly MainActivity activity;

    public FusedLocationProviderCallback(MainActivity activity)
    {
        this.activity = activity;
    }

    public override void OnLocationAvailability(LocationAvailability locationAvailability)
    {
        Log.Debug("FusedLocationProviderSample", "IsLocationAvailable: {0}",locationAvailability.IsLocationAvailable);
    }

    public override void OnLocationResult(LocationResult result)
    {
        if (result.Locations.Any())
        {
            var location = result.Locations.First();
            Log.Debug("Sample", "The latitude is :" + location.Latitude);
        }
        else
        {
            // No locations to work with.
        }
    }
}
```

## <a name="using-the-android-location-service-api"></a>使用 Android 位置服务 API

Android 位置服务是旧版 API，用于在 Android 上使用位置信息。 位置数据由硬件传感器和系统服务收集，可在应用程序中通过 `LocationManager` 类和 `ILocationListener` 进行访问。

定位服务最适用于必须在未安装 Google Play Services 的设备上运行的应用程序。

位置服务是系统管理的一种特殊类型的[服务](https://developer.android.com/guide/components/services.html)。 系统服务与设备硬件进行交互，并且始终运行。 为利用应用程序中的位置更新，我们将使用 `LocationManager` 和 `RequestLocationUpdates` 调用从系统位置服务订阅位置更新。

使用 Android 位置服务获取用户的位置涉及以下几个步骤：

1. 获取对 `LocationManager` 服务的引用。
2. 实现 `ILocationListener` 接口，并在位置更改时处理事件。
3. 使用 `LocationManager` 来请求指定提供程序的位置更新。 上一步骤中的 `ILocationListener` 将用于接收来自 `LocationManager` 的回调。
4. 当应用程序不再适合接收更新时，停止位置更新。

### <a name="location-manager"></a>位置管理器

我们可以使用 `LocationManager` 类的实例访问系统位置服务。 `LocationManager` 是一个特殊的类，支持与系统位置服务进行交互并在其上调用方法。 应用程序可以通过调用 `GetSystemService` 并传入“服务”类型来获取对 `LocationManager` 的引用，如下所示：

```csharp
LocationManager locationManager = (LocationManager) GetSystemService(Context.LocationService);
```

`OnCreate` 是获取对 `LocationManager` 的引用的合适位置。
最好将 `LocationManager` 保留为类变量，以便我们可以在活动生命周期的不同时间点调用它。

### <a name="request-location-updates-from-the-locationmanager"></a>从 LocationManager 请求位置更新

应用程序引用 `LocationManager` 后，它需要告知 `LocationManager` 所需的位置信息类型以及该信息的更新频率。 为此，请调用 `LocationManager` 对象上的 `RequestLocationUpdates`，并传递一些更新条件和将接收位置更新的回调。 此回调是必须实现 `ILocationListener` 接口的类型（本指南稍后将对此进行详细介绍）。

`RequestLocationUpdates` 方法会告知系统位置服务应用程序希望开始接收位置更新。 借助此方法，可指定提供程序以及时间和距离阈值以控制更新频率。 例如，以下方法每隔 2000 毫秒并且仅当位置更改超过 1 米时会向 GPS 位置提供程序请求位置更新：

```csharp
// For this example, this method is part of a class that implements ILocationListener, described below
locationManager.RequestLocationUpdates(LocationManager.GpsProvider, 2000, 1, this);
```

应用程序应仅按其正常运行所需的频率请求位置更新。 这样可以延长电池寿命，并为用户创造更好的体验。

### <a name="responding-to-updates-from-the-locationmanager"></a>从 LocationManager 响应更新

应用程序从 `LocationManager` 请求更新后，便可以通过实现 [`ILocationListener`](xref:Android.Locations.ILocationListener) 接口从服务接收信息。 此接口提供了四种方法来侦听位置服务和位置提供程序 `OnLocationChanged`。 当用户的位置更改足够大，满足请求位置更新时设置的位置更改条件时，系统将调用 `OnLocationChanged`。 

以下代码演示了 `ILocationListener` 接口中的方法：

```csharp
public class MainActivity : AppCompatActivity, ILocationListener
{
    TextView latitude;
    TextView longitude;
    
    public void OnLocationChanged (Location location)
    {
        // called when the location has been updated.
    }
    
    public OnProviderDisabled(string locationProvider)
    {
        // called when the user disables the provider
    }
    
    public OnProviderEnabled(string locationProvider)
    {
        // called when the user enables the provider
    }
    
    public OnStatusChanged(string locationProvider, Availability status, Bundle extras)
    {
        // called when the status of the provider changes (there are a variety of reasons for this)
    }
}
```

### <a name="unsubscribing-to-locationmanager-updates"></a>取消订阅 LocationManager 更新

为了节省系统资源，应用程序应尽快取消订阅位置更新。 `RemoveUpdates` 方法会告知 `LocationManager` 停止向应用程序发送更新。  例如，活动可能会在 `OnPause` 方法中调用 `RemoveUpdates`，以便应用程序无需位置更新并且其活动不在屏幕上时，便可以节省电量：

```csharp
protected override void OnPause ()
{
    base.OnPause ();
    locationManager.RemoveUpdates (this);
}
```

如果应用程序在后台运行时需要获取位置更新，则需要创建一个自定义服务来订阅系统位置服务。 有关详细信息，请参阅[使用 Android 服务进行后台操作](~/android/app-fundamentals/services/index.md)指南。

### <a name="determining-the-best-location-provider-for-the-locationmanager"></a>确定 LocationManager 的最佳位置提供程序

上面的应用程序将 GPS 设置为位置提供程序。 但是，GPS 可能并非在所有情况下都可用，例如设备在室内或没有 GPS 接收器。 如果是这种情况，则提供程序返回的结果为 `null`。

要使应用在 GPS 不可用时正常工作，请使用 `GetBestProvider` 方法在应用程序启动时请求可用的（设备支持和用户已启用）最佳位置提供程序。 无需传递特定的提供程序，只需使用 [`Criteria` 对象](xref:Android.Locations.Criteria) `GetBestProvider` 告知提供程序的要求，例如准确度和耗电情况。 `GetBestProvider` 可针对给定条件返回最佳提供程序。

以下代码演示了如何获取可用的最佳提供程序，并在请求位置更新时使用它：

```csharp
Criteria locationCriteria = new Criteria();   
locationCriteria.Accuracy = Accuracy.Coarse;
locationCriteria.PowerRequirement = Power.Medium;

locationProvider = locationManager.GetBestProvider(locationCriteria, true);

if(locationProvider != null)
{
    locationManager.RequestLocationUpdates (locationProvider, 2000, 1, this);
}
else
{
    Log.Info(tag, "No location providers available");
}
```

> [!NOTE]
> 如果用户已禁用所有位置提供程序，则 `GetBestProvider` 将返回 `null`。 要查看此代码在实际设备上的工作方式，请确保在“Google 设置”>“位置”>“模式”下启用了 GPS、Wi-Fi 和蜂窝网络，如以下屏幕截图所示：
>
> [![Android 手机上的“位置模式”设置屏幕](location-images/location-02.png)](location-images/location-02.png#lightbox)
>
> 下面的屏幕截图演示了使用 `GetBestProvider` 运行的位置应用程序：
>
> [![GetBestProvider 应用显示纬度、经度和提供程序](location-images/location-03.png)](location-images/location-03.png#lightbox)
>
> 请记住，`GetBestProvider` 不会动态更改提供程序。 而是在活动生命周期内确定可用的最佳提供程序（一次）。 如果在设置提供程序后其状态发生更改，则该应用程序将需要在 `ILocationListener` 方法中包含其他代码 &ndash; `OnProviderEnabled`、`OnProviderDisabled` 和 `OnStatusChanged` &ndash;，用于处理与提供程序切换相关的所有可能发生的情况。

## <a name="summary"></a>总结

本指南介绍了如何使用 Android 位置服务和 Google 位置服务 API 中的融合位置提供程序获取用户的位置。

## <a name="related-links"></a>相关链接

- [位置（示例）](/samples/xamarin/monodroid-samples/location)
- [FusedLocationProvider（示例）](/samples/xamarin/monodroid-samples/fusedlocationprovider)
- [Google Play Services](https://developer.android.com/google/play-services/index.html)
- [Criteria 类](xref:Android.Locations.Criteria)
- [LocationManager 类](xref:Android.Locations.LocationManager)
- [LocationListener 类](xref:Android.Locations.ILocationListener)
- [LocationClient API](https://developer.android.com/reference/com/google/android/gms/location/LocationClient.html)
- [LocationListener API](https://developer.android.com/reference/com/google/android/gms/location/LocationListener.html)
- [LocationRequest API](https://developer.android.com/reference/com/google/android/gms/location/LocationRequest.html)