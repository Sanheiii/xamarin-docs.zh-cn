---
title: Xamarin.Essentials：权限
description: 本文档介绍 Xamarin.Essentials 中的 Permissions 类，它提供了检查和请求运行时权限的功能。
ms.assetid: 34062D84-3E55-4AF7-A688-8551068B1E57
author: jamesmontemagno
ms.author: jamont
ms.custom: video
ms.date: 09/22/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 12631abacc56edf88d375d4be89e71a9a4588d03
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436377"
---
# <a name="no-locxamarinessentials-permissions"></a>Xamarin.Essentials：权限

Permissions 类提供了检查和请求运行时权限的功能。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

[!include[](~/essentials/includes/android-permissions.md)]

## <a name="using-permissions"></a>使用 Permissions

在类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

## <a name="checking-permissions"></a>检查权限

若要检查权限的当前状态，请使用 `CheckStatusAsync` 方法以及要获取其状态的特定权限。

```csharp
var status = await Permissions.CheckStatusAsync<Permissions.LocationWhenInUse>();
```

如果相应权限未声明，`PermissionException` 则会抛出。

在请求权限之前，最好先检查权限的状态。 如果系统从不提示用户，则每个操作系统会返回不同的默认状态。 iOS 会返回 `Unknown`，而其他系统返回 `Denied`。

## <a name="requesting-permissions"></a>请求权限

若要向用户请求权限，请使用 `RequestAsync` 方法以及要请求的特定权限。 如果用户先前已经授予了权限，并且没有撤销它，此方法会立即返回 `Granted`，而不会显示对话框。

```csharp
var status = await Permissions.RequestAsync<Permissions.LocationWhenInUse>();
```

如果相应权限未声明，`PermissionException` 则会抛出。

请注意，在某些平台上，权限请求只能激活一次。 开发人员必须处理其他提示，以检查权限是否处于 `Denied` 状态，并要求用户手动启用。

## <a name="permission-status"></a>权限状态

如果使用 `CheckStatusAsync` 或 `RequestAsync`，将返回 `PermissionStatus` 用于确定后续步骤：

* Unknown - 权限处于未知状态
* Denied - 用户拒绝了权限请求
* Disabled - 此功能在设备上是禁用的
* Granted - 用户已授予权限或权限已自动授予
* Restricted - 处于受限制状态


## <a name="explain-why-permission-is-needed"></a>说明为何需要权限

![预发行版 API](~/media/shared/preview.png)

最佳做法是说明应用程序需要特定权限的原因。 在 iOS 上，必须指定向用户显示的字符串。 Android 没有此功能，并且还将权限状态默认为“已禁用”。 这样则无法了解用户是否拒绝了权限，也无法了解是否是首次提示用户。 可使用 `ShouldShowRationale` 方法确定是否应显示说明性 UI。 该方法返回 `true` 时，则是因为过去用户已拒绝或禁用该权限。 调用此方法时，其他平台始终会返回 `false`。

## <a name="available-permissions"></a>可用权限

Xamarin.Essentials 会尝试将尽可能多的权限抽象化。 但是，每个操作系统都有一组不同的运行时权限。 此外，在能否为某些权限提供单个 API 方面也存在差异。 下面是当前可用权限的指南：

图标指南：

* ![完全支持](~/media/shared/yes.png "完全支持") - 支持
* ![不支持](~/media/shared/no.png "不支持或不需要") - 不支持/不需要

| 权限 | Android | iOS | UWP | watchOS | tvOS | Tizen |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---:
| CalendarRead   | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![UWP 不支持](~/media/shared/no.png "UWP 不支持") | ![支持 watchOS](~/media/shared/yes.png "支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![不支持 Tizen](~/media/shared/no.png "不支持 Tizen") |
| CalendarWrite | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![UWP 不支持](~/media/shared/no.png "UWP 不支持") | ![支持 watchOS](~/media/shared/yes.png "支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![不支持 Tizen](~/media/shared/no.png "不支持 Tizen") |
| 照相机 | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![UWP 不支持](~/media/shared/no.png "UWP 不支持") | ![不支持 watchOS](~/media/shared/no.png "不支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![Tizen 支持](~/media/shared/yes.png "支持 Tizen") |
| ContactsRead | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![不支持 watchOS](~/media/shared/no.png "不支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![不支持 Tizen](~/media/shared/no.png "不支持 Tizen") |
| ContactsWrite | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![不支持 watchOS](~/media/shared/no.png "不支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![不支持 Tizen](~/media/shared/no.png "不支持 Tizen") |
| 手电筒 | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![iOS 不支持](~/media/shared/no.png "iOS 不支持") | ![UWP 不支持](~/media/shared/no.png "UWP 不支持") | ![不支持 watchOS](~/media/shared/no.png "不支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![Tizen 支持](~/media/shared/yes.png "支持 Tizen") |
| LocationWhenInUse | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![支持 watchOS](~/media/shared/yes.png "支持 watchOS") | ![支持 tvOS](~/media/shared/yes.png "支持 tvOS")  | ![Tizen 支持](~/media/shared/yes.png "支持 Tizen") |
| LocationAlways | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![支持 watchOS](~/media/shared/yes.png "支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![Tizen 支持](~/media/shared/yes.png "支持 Tizen") |
| 媒体 | ![Android 不支持](~/media/shared/no.png "Android 不支持") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![UWP 不支持](~/media/shared/no.png "UWP 不支持") | ![不支持 watchOS](~/media/shared/no.png "不支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![不支持 Tizen](~/media/shared/no.png "不支持 Tizen") |
| 麦克风 | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![不支持 watchOS](~/media/shared/no.png "不支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![Tizen 支持](~/media/shared/yes.png "支持 Tizen") |
| 电话 | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![UWP 不支持](~/media/shared/no.png "UWP 不支持") | ![不支持 watchOS](~/media/shared/no.png "不支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![不支持 Tizen](~/media/shared/no.png "不支持 Tizen") |
| 照片 | ![Android 不支持](~/media/shared/no.png "Android 不支持") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![UWP 不支持](~/media/shared/no.png "UWP 不支持") | ![不支持 watchOS](~/media/shared/no.png "不支持 watchOS") | ![支持 tvOS](~/media/shared/yes.png "支持 tvOS") | ![不支持 Tizen](~/media/shared/no.png "不支持 Tizen") |
| 提醒 | ![Android 不支持](~/media/shared/no.png "Android 不支持") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![UWP 不支持](~/media/shared/no.png "UWP 不支持") | ![支持 watchOS](~/media/shared/yes.png "支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![不支持 Tizen](~/media/shared/no.png "不支持 Tizen") |
| Sensors | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![支持 watchOS](~/media/shared/yes.png "支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![不支持 Tizen](~/media/shared/no.png "不支持 Tizen") |
| Sms | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![UWP 不支持](~/media/shared/no.png "UWP 不支持") | ![不支持 watchOS](~/media/shared/no.png "不支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![不支持 Tizen](~/media/shared/no.png "不支持 Tizen") |
| 语音 | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![UWP 不支持](~/media/shared/no.png "UWP 不支持") | ![不支持 watchOS](~/media/shared/no.png "不支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![不支持 Tizen](~/media/shared/no.png "不支持 Tizen") |
| StorageRead | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![iOS 不支持](~/media/shared/no.png "iOS 不支持") | ![UWP 不支持](~/media/shared/no.png "UWP 不支持") | ![不支持 watchOS](~/media/shared/no.png "不支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![不支持 Tizen](~/media/shared/no.png "不支持 Tizen") |
| StorageWrite | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![iOS 不支持](~/media/shared/no.png "iOS 不支持") | ![UWP 不支持](~/media/shared/no.png "UWP 不支持") | ![不支持 watchOS](~/media/shared/no.png "不支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![不支持 Tizen](~/media/shared/no.png "不支持 Tizen") |

如果权限标有 ![not supported](~/media/shared/no.png "不受支持")，则在检查或请求权限时始终返回 `Granted`。

## <a name="general-usage"></a>常规使用情况
下面介绍了权限的一般处理模式。

```csharp
public async Task<PermissionStatus> CheckAndRequestLocationPermission()
{
    var status = await Permissions.CheckStatusAsync<Permissions.LocationWhenInUse>();
    if (status != PermissionStatus.Granted)
    {
        status = await Permissions.RequestAsync<Permissions.LocationWhenInUse>();
    }

    // Additionally could prompt the user to turn on in settings

    return status;
}
```

每个权限类型都能创建实例，可以对其直接调用方法。

```csharp
public async Task GetLocationAsync()
{
    var status = await CheckAndRequestPermissionAsync(new Permissions.LocationWhenInUse());
    if (status != PermissionStatus.Granted)
    {
        // Notify user permission was denied
        return;
    }

    var location = await Geolocation.GetLocationAsync();
}

public async Task<PermissionStatus> CheckAndRequestPermissionAsync<T>(T permission)
            where T : BasePermission
{
    var status = await permission.CheckStatusAsync();
    if (status != PermissionStatus.Granted)
    {
        status = await permission.RequestAsync();
    }

    return status;
}
```

## <a name="extending-permissions"></a>扩展 Permissions

Permissions API 灵活且可扩展，可用于需要额外验证或权限的应用程序，而这些验证或权限未包含在 Xamarin.Essentials 中。 新建继承自 `BasePermission` 的类，并实现相应抽象方法。

```csharp
public class MyPermission : BasePermission
{
    // This method checks if current status of the permission
    public override Task<PermissionStatus> CheckStatusAsync()
    {
        throw new System.NotImplementedException();
    }

    // This method is optional and a PermissionException is often thrown if a permission is not declared
    public override void EnsureDeclared()
    {
        throw new System.NotImplementedException();
    }

    // Requests the user to accept or deny a permission
    public override Task<PermissionStatus> RequestAsync()
    {
        throw new System.NotImplementedException();
    }
}
```

在特定平台中实现权限时，可以继承自 `BasePlatformPermission` 类。 这提供了额外的平台帮助程序方法，用于自动检查声明。 这在创建自定义权限来进行分组时很有用。 例如，可以使用以下自定义权限请求对 Android 上存储的读写访问权限。

```csharp
public class ReadWriteStoragePermission : Xamarin.Essentials.Permissions.BasePlatformPermission
{
    public override (string androidPermission, bool isRuntime)[] RequiredPermissions => new List<(string androidPermission, bool isRuntime)>
    {
        (Android.Manifest.Permission.ReadExternalStorage, true),
        (Android.Manifest.Permission.WriteExternalStorage, true)
    }.ToArray();
}
```

然后，你可从 Android 项目请求新权限。

```csharp
await Permissions.RequestAsync<ReadWriteStoragePermission>();
```

如果要从共享代码调用此 API，可创建一个接口，并使用[依赖项服务](../xamarin-forms/app-fundamentals/dependency-service/index.md)来注册和获取该实现。

```csharp
public interface IReadWritePermission
{        
    Task<PermissionStatus> CheckStatusAsync();
    Task<PermissionStatus> RequestAsync();
}
```

然后在平台项目中实现该接口：

```csharp
public class ReadWriteStoragePermission : Xamarin.Essentials.Permissions.BasePlatformPermission, IReadWritePermission
{
    public override (string androidPermission, bool isRuntime)[] RequiredPermissions => new List<(string androidPermission, bool isRuntime)>
    {
        (Android.Manifest.Permission.ReadExternalStorage, true),
        (Android.Manifest.Permission.WriteExternalStorage, true)
    }.ToArray();
}
```

接着可注册特定实现：

```csharp
DependencyService.Register<IReadWritePermission, ReadWriteStoragePermission>();
```
再从共享项目中解析并使用它：

```csharp
var readWritePermission = DependencyService.Get<IReadWritePermission>();
var status = await readWritePermission.CheckStatusAsync();
if (status != PermissionStatus.Granted)
{
    status = await readWritePermission.RequestAsync();
}
```

## <a name="platform-implementation-specifics"></a>平台实现细节

# <a name="android"></a>[Android](#tab/android)

Permissions 必须在 Android 清单文件中设置匹配的属性。 权限状态默认为“已拒绝”。

有关详细信息，请参阅 [Xamarin.Android 中的 Permissions](../android/app-fundamentals/permissions.md) 文档。

# <a name="ios"></a>[iOS](#tab/ios)

Permissions 必须在 `Info.plist` 文件中有匹配字符串。 请求某个权限并遭拒后，如果再次请求该权限，将不再显示弹出窗口。 必须提示用户在 iOS 的“应用设置”屏幕中手动调整设置。 权限状态默认为“未知”。

有关详细信息，请参阅 [iOS 安全和隐私功能](../ios/app-fundamentals/security-privacy.md)文档。

# <a name="uwp"></a>[UWP](#tab/uwp)

Permissions 必须有在包清单中声明的匹配功能。 多数情况下，权限状态都默认为“未知”。

有关详细信息，请参阅[应用功能声明](/windows/uwp/packaging/app-capability-declarations)文档。

--------------

## <a name="api"></a>API

- [Permissions 源代码](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Permissions)
- [Permissions API 文档](xref:Xamarin.Essentials.Permissions)


## <a name="related-video"></a>相关视频

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Permissions-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
