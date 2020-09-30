---
title: Xamarin.Forms 映射初始化和配置
description: Xamarin.Forms。在应用程序中使用地图功能需要 maps NuGet 包。 此外，访问用户的位置时，需要向应用程序授予 location 权限。
ms.prod: xamarin
ms.assetid: 59CD1344-8248-406C-9144-0C8A67141E5B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/07/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3da0223bf72e4de60cc50be2562a0fdbd279f52e
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91559735"
---
# <a name="no-locxamarinforms-map-initialization-and-configuration"></a>Xamarin.Forms 映射初始化和配置

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

[`Map`](xref:Xamarin.Forms.Maps.Map)控件在每个平台上使用本机映射控件。 这为用户提供了快速、熟悉的地图体验，但这意味着需要执行一些配置步骤来符合每个平台的 API 要求。

## <a name="map-initialization"></a>映射初始化

[`Map`](xref:Xamarin.Forms.Maps.Map)控件由提供[ Xamarin.Forms 。映射](https://www.nuget.org/packages/Xamarin.Forms.Maps/)应添加到解决方案中每个项目的 NuGet 包。

安装后[ Xamarin.Forms 。映射](https://www.nuget.org/packages/Xamarin.Forms.Maps/)NuGet 包，必须在每个平台项目中对其进行初始化。

在 iOS 上，此方法应在 **AppDelegate.cs** 中出现，方法是在 `Xamarin.FormsMaps.Init` 方法 *之后* 调用方法 `Xamarin.Forms.Forms.Init` ：

```csharp
Xamarin.FormsMaps.Init();
```

在 Android 上，此方法应在 **MainActivity.cs** 中出现，方法是在 `Xamarin.FormsMaps.Init` 方法 *之后* 调用方法 `Xamarin.Forms.Forms.Init` ：

```csharp
Xamarin.FormsMaps.Init(this, savedInstanceState);
```

在通用 Windows 平台 (UWP) 上，此操作应在 **MainPage.xaml.cs** 中通过调用 `Xamarin.FormsMaps.Init` 构造函数中的方法来执行 `MainPage` ：

```csharp
Xamarin.FormsMaps.Init("INSERT_AUTHENTICATION_TOKEN_HERE");
```

有关 UWP 所需的身份验证令牌的信息，请参阅 [通用 Windows 平台](#universal-windows-platform)。

添加 NuGet 包并在每个应用程序内调用初始化方法后， `Xamarin.Forms.Maps` 可以在共享代码项目中使用 api。

## <a name="platform-configuration"></a>平台配置

在显示地图之前，Android 和通用 Windows 平台 (UWP) 需要其他配置。 此外，在 iOS、Android 和 UWP 上，访问用户的位置时，需要授予对应用程序的位置权限。

### <a name="ios"></a>iOS

在 iOS 上显示和与地图进行交互不需要任何其他配置。 但是，若要访问位置服务，必须在 **info.plist**中设置以下项：

- iOS 11 及更高版本
  - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) –用于在应用程序使用时使用定位服务
  - [`NSLocationAlwaysAndWhenInUseUsageDescription`](https://developer.apple.com/documentation/bundleresources/information_property_list/nslocationalwaysandwheninuseusagedescription) –用于始终使用定位服务
- iOS 10 及更早版本
  - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) –用于在应用程序使用时使用定位服务
  - [`NSLocationAlwaysUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18) –用于始终使用定位服务    

若要支持 iOS 11 和更早版本，你可以包含所有三个键： `NSLocationWhenInUseUsageDescription` 、 `NSLocationAlwaysAndWhenInUseUsageDescription` 和 `NSLocationAlwaysUsageDescription` 。

**Info.plist**中的这些项的 XML 表示形式如下所示。 你应更新这些 `string` 值，以反映你的应用程序使用位置信息的方式：

```xml
<key>NSLocationAlwaysUsageDescription</key>
<string>Can we use your location at all times?</string>
<key>NSLocationWhenInUseUsageDescription</key>
<string>Can we use your location when your application is being used?</string>
<key>NSLocationAlwaysAndWhenInUseUsageDescription</key>
<string>Can we use your location at all times?</string>
```

编辑**info.plist**文件时，也可以在**源**视图中添加**info.plist**项：

![适用于 iOS 8 的 info.plist](setup-images/ios8-map-permissions.png "iOS 8 必需的信息。 info.plist 条目")

当应用程序尝试访问用户的位置时，将显示一条提示，请求访问：

[![IOS 上的位置权限请求的屏幕截图](setup-images/permission-ios.png "iOS 权限请求")](setup-images/permission-ios-large.png#lightbox "iOS 权限请求")

### <a name="android"></a>Android

用于在 Android 上显示和与地图进行交互的配置过程如下：

1. 获取 Google Maps API 密钥，并将其添加到清单中。
1. 在清单中指定 Google Play 服务版本号。
1. 在清单中指定 Apache HTTP 旧版本库的要求。
1. 可有可无在清单中指定 WRITE_EXTERNAL_STORAGE 权限。
1. 可有可无指定清单中的位置权限。
1. 可有可无类中的请求运行时位置权限 `MainActivity` 。

有关正确配置的清单文件的示例，请参阅示例应用程序中的 [AndroidManifest.xml](https://github.com/xamarin/xamarin-forms-samples/blob/master/WorkingWithMaps/WorkingWithMaps/WorkingWithMaps.Android/Properties/AndroidManifest.xml) 。

#### <a name="get-a-google-maps-api-key"></a>获取 Google Maps API 密钥

若要在 Android 上使用 [Google MAPS api](https://developers.google.com/maps/documentation/android/) ，必须生成 API 密钥。 为此，请按照 [获取 Google MAPS API 密钥](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)中的说明进行操作。

获取 API 密钥后，必须将其添加到 `<application>` **Properties/AndroidManifest.xml** 文件的元素中：

```xml
<application ...>
    <meta-data android:name="com.google.android.geo.API_KEY" android:value="PASTE-YOUR-API-KEY-HERE" />
</application>
```

这会将 API 密钥嵌入清单中。 如果没有有效的 API 密钥， [`Map`](xref:Xamarin.Forms.Maps.Map) 控件将显示空白网格。

> [!NOTE]
> `com.google.android.geo.API_KEY` 是 API 密钥的建议的元数据名称。 为了向后兼容， `com.google.android.maps.v2.API_KEY` 可以使用元数据名称，但仅允许对 Android MAPS API v2 进行身份验证。

要使 APK 能够访问 Google Maps，必须包含每个密钥存储的 SHA-1 指纹和包名称， (调试和发布) 用于对 APK 进行签名。 例如，如果你使用一台计算机进行调试，将另一台计算机用于生成发布 APK，则应在第一台计算机的调试密钥存储中包含 SHA-1 证书指纹，并在第二台计算机的版本存储库中包含 SHA-1 证书指纹。 另外，请记住，如果应用的 **包名称** 发生更改，则编辑密钥凭据。 请参阅 [获取 Google MAPS API 密钥](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)。

#### <a name="specify-the-google-play-services-version-number"></a>指定 Google Play 服务版本号

在AndroidManifest.xml的元素中添加以下声明 `<application>` ： ** **

```xml
<meta-data android:name="com.google.android.gms.version" android:value="@integer/google_play_services_version" />
```

这会将编译应用程序的 Google Play 服务的版本嵌入到清单中。

#### <a name="specify-the-requirement-for-the-apache-http-legacy-library"></a>指定 Apache HTTP 旧版本库的要求

如果 Xamarin.Forms 应用程序面向 API 28 或更高版本，则必须在AndroidManifest.xml的元素中添加以下声明 `<application>` ： ** **

```xml
<uses-library android:name="org.apache.http.legacy" android:required="false" />    
```

这会告知应用程序使用 Apache Http 客户端库（已从 `bootclasspath` Android 9 中的中删除）。

#### <a name="specify-the-write_external_storage-permission"></a>指定 WRITE_EXTERNAL_STORAGE 权限

如果应用程序面向 API 22 或更低，则可能需要将权限添加 `WRITE_EXTERNAL_STORAGE` 到清单，作为元素的子 `<manifest>` 元素：

```xml
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

如果应用程序面向 API 23 或更高版本，则不需要这样做。

#### <a name="specify-location-permissions"></a>指定位置权限

如果你的应用程序需要访问用户的位置，则必须通过将 `ACCESS_COARSE_LOCATION` 或 `ACCESS_FINE_LOCATION` 权限添加到清单 (或同时作为元素的子元素) 来请求权限 `<manifest>` ：

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionCode="1" android:versionName="1.0" package="com.companyname.myapp">
  ...
  <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
  <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
</manifest>
```

此 `ACCESS_COARSE_LOCATION` 权限允许 API 使用 WiFi 或移动数据，或者同时使用这两种方法来确定设备的位置。 这些 `ACCESS_FINE_LOCATION` 权限允许 API 使用 (GPS) 、WiFi 或移动数据全球定位系统来确定尽可能精确的位置。

或者，可以通过使用清单编辑器添加以下权限来启用这些权限：

- `AccessCoarseLocation`
- `AccessFineLocation`

下面的屏幕截图中显示了这些内容：

![适用于 Android 的必需权限](setup-images/android-map-permissions.png "适用于 Android 的必需权限")

#### <a name="request-runtime-location-permissions"></a>请求运行时位置权限

如果你的应用程序面向 API 23 或更高版本并需要访问用户的位置，则必须检查它在运行时是否具有所需的权限，如果没有，则请求它。 这可以通过以下操作实现：

1. 在 `MainActivity` 类中，添加以下字段：

    ```csharp
    const int RequestLocationId = 0;

    readonly string[] LocationPermissions =
    {
        Manifest.Permission.AccessCoarseLocation,
        Manifest.Permission.AccessFineLocation
    };
    ```

1. 在 `MainActivity` 类中，添加以下 `OnStart` 重写：

    ```csharp
    protected override void OnStart()
    {
        base.OnStart();

        if ((int)Build.VERSION.SdkInt >= 23)
        {
            if (CheckSelfPermission(Manifest.Permission.AccessFineLocation) != Permission.Granted)
            {
                RequestPermissions(LocationPermissions, RequestLocationId);
            }
            else
            {
                // Permissions already granted - display a message.
            }
        }
    }
    ```

    如果应用程序面向 API 23 或更高版本，则此代码将为权限执行运行时权限检查 `AccessFineLocation` 。 如果未授予权限，则通过调用方法发出权限请求 `RequestPermissions` 。

1. 在 `MainActivity` 类中，添加以下 `OnRequestPermissionsResult` 重写：

    ```csharp
    public override void OnRequestPermissionsResult(int requestCode, string[] permissions, [GeneratedEnum] Permission[] grantResults)
    {
        if (requestCode == RequestLocationId)
        {
            if ((grantResults.Length == 1) && (grantResults[0] == (int)Permission.Granted))
                // Permissions granted - display a message.
            else
                // Permissions denied - display a message.
        }
        else
        {
            base.OnRequestPermissionsResult(requestCode, permissions, grantResults);
        }
    }
    ```

    此重写处理权限请求的结果。

此代码的总体效果是，当应用程序请求用户的位置时，将显示以下对话框，请求权限：

[![Android 上的位置权限请求的屏幕截图](setup-images/permission-android.png "Android 权限请求")](setup-images/permission-android-large.png#lightbox "Android 权限请求")

### <a name="universal-windows-platform"></a>通用 Windows 平台

在 UWP 上，必须先对应用程序进行身份验证，然后才能显示地图并使用地图服务。 若要对应用程序进行身份验证，必须指定映射身份验证密钥。 有关详细信息，请参阅 [请求映射身份验证密钥](/windows/uwp/maps-and-location/authentication-key)。 然后，应在方法调用中指定身份验证令牌 `FormsMaps.Init("AUTHORIZATION_TOKEN")` ，以便用 Bing 地图对应用程序进行身份验证。

> [!NOTE]
> 在 UWP 上，若要使用地图服务（如地理编码），还必须将 `MapService.ServiceToken` 属性设置为 "身份验证密钥" 值。 这可以通过以下代码行完成： `Windows.Services.Maps.MapService.ServiceToken = "INSERT_AUTH_TOKEN_HERE";` 。

此外，如果应用程序需要访问用户的位置，则必须在包清单中启用位置功能。 这可以通过以下操作实现：

1. 在**解决方案资源管理器**中，双击 **package.appxmanifest** 并选择**功能**选项卡。
1. 在**功能**列表中，选中**位置**框。 这会将 `location` 设备功能添加到包清单文件中。

    ```xml
    <Capabilities>
      <!-- DeviceCapability elements must follow Capability elements (if present) -->
      <DeviceCapability Name="location"/>
    </Capabilities>
    ```

#### <a name="release-builds"></a>发布版本

UWP 发布版本使用 .NET 本机编译将应用程序直接编译为本机代码。 但是，这种情况的结果是，UWP 上的控件的呈现器 [`Map`](xref:Xamarin.Forms.Maps.Map) 可能与可执行文件链接。 这可以通过 `Forms.Init` 在 **App.xaml.cs**中使用方法的 UWP 特定重载来修复：

```csharp
var assembliesToInclude = new [] { typeof(Xamarin.Forms.Maps.UWP.MapRenderer).GetTypeInfo().Assembly };
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
```

此代码将类所驻留的程序集传递 `Xamarin.Forms.Maps.UWP.MapRenderer` 给 `Forms.Init` 方法。 这可确保 .NET 本机编译过程不会将程序集链接到可执行文件。

> [!IMPORTANT]
> 如果不这样做，将导致 [`Map`](xref:Xamarin.Forms.Maps.Map) 控件在运行发布版本时不显示。

## <a name="related-links"></a>相关链接

- [地图示例](/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Xamarin.Forms.映射 Pin](~/xamarin-forms/user-interface/map/pins.md)。
- [地图 API](xref:Xamarin.Forms.Maps)
- [映射自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/map-pin.md)