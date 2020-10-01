---
title: Xamarin.Essentials：安全存储
description: 本文档介绍了 Xamarin.Essentials 中的 SecureStorage 类，该类有助于安全地存储简单的键/值对。 讨论了如何使用类、平台实现细节和限制。
ms.assetid: 78856C0D-76BB-406E-A880-D5A3987B7D64
author: jamesmontemagno
ms.author: jamont
ms.date: 04/02/2019
ms.custom: video
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f4bb252448abe3c2987def143634d15b5cae194c
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433506"
---
# <a name="no-locxamarinessentials-secure-storage"></a>Xamarin.Essentials：安全存储

SecureStorage 类有助于安全地存储简单的键/值对。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

若要访问 SecureStorage 功能，需要以下特定于平台的设置：

# <a name="android"></a>[Android](#tab/android)

> [!TIP]
> [应用的自动备份](https://developer.android.com/guide/topics/data/autobackup)是 Android 6.0（API 级别 23）及更高版本的功能，可备份用户的应用数据（共享首选项、应用的内部存储中的文件和其他特定文件）。 在新设备上重新安装或安装应用时，会还原数据。 这可能会影响使用共享首选项（已备份但在还原时无法解密）的 `SecureStorage`。 Xamarin.Essentials 可通过删除键（以便可以进行重置）自动处理这种情况，但你可以通过禁用自动备份来采取其他步骤。

### <a name="enable-or-disable-backup"></a>启用或禁用备份
可以选择通过在 `AndroidManifest.xml` 文件中将 `android:allowBackup` 设置设为 false，为整个应用程序禁用自动备份。 仅当计划按另一种方式还原数据时才建议使用此方法。

```xml
<manifest ... >
    ...
    <application android:allowBackup="false" ... >
        ...
    </application>
</manifest>
```

### <a name="selective-backup"></a>选择性备份
可以将自动备份配置为禁止备份特定内容。 可以创建自定义规则集以禁止备份 `SecureStore` 项。

1. 在你的 AndroidManifest.xml 中设置 `android:fullBackupContent` 属性：

    ```xml
    <application ...
        android:fullBackupContent="@xml/auto_backup_rules">
    </application>
    ```

2. 使用 AndroidResource 的生成操作在 Resources/xml 目录中创建名为 auto_backup_rules.xml 的新 XML 文件。 然后设置以下内容，包括除 `SecureStorage` 以外的所有共享首选项：

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <full-backup-content>
        <include domain="sharedpref" path="."/>
        <exclude domain="sharedpref" path="${applicationId}.xamarinessentials.xml"/>
    </full-backup-content>
    ```

# <a name="ios"></a>[iOS](#tab/ios)

在 iOS 模拟器上开发时，启用密钥链权利，并为应用程序的捆绑包标识符添加密钥链访问组。

打开 iOS 项目中的 Entitlements.plist，查找密钥链权利并启用它。 这样会自动将应用程序的标识符添加为一个组。

在“iOS 捆绑包签名”下的“项目属性”中，将“自定义权利”设置为“Entitlements.plist”  。

> [!TIP]
> 部署到 iOS 设备时，此权利不是必需的，应将其删除。

# <a name="uwp"></a>[UWP](#tab/uwp)

无需其他设置。

-----

## <a name="using-secure-storage"></a>使用 Secure Storage

在类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

将给定密钥的值保存在安全存储中：

```csharp
try
{
  await SecureStorage.SetAsync("oauth_token", "secret-oauth-token-value");
}
catch (Exception ex)
{
  // Possible that device doesn't support secure storage on device.
}
```

从安全存储中检索值：

```csharp
try
{
  var oauthToken = await SecureStorage.GetAsync("oauth_token");
}
catch (Exception ex)
{
  // Possible that device doesn't support secure storage on device.
}
```

> [!NOTE]
> 如果没有与所请求的密钥关联的值，则 `GetAsync` 将返回 `null`。

若要删除特定密钥，请调用：

```csharp
SecureStorage.Remove("oauth_token");
```

若要删除所有密钥，请调用：

```csharp
SecureStorage.RemoveAll();
```

## <a name="platform-implementation-specifics"></a>平台实现细节

# <a name="android"></a>[Android](#tab/android)

[Android 密钥存储](https://developer.android.com/training/articles/keystore.html)用来存储用于在将值保存到文件名为 **[你的应用包 ID].xamarinessentials** 的[共享首选项](https://developer.android.com/training/data-storage/shared-preferences.html)中之前加密值的加密密钥。  共享首选项文件中所使用的键（非加密密钥，指值的密钥）是传递到 `SecureStorage` API 的密钥的 MD5 哈希。  

**API 级别 23 及更高版本**

在较新的 API 级别上，AES 密钥是从 Android 密钥存储中获得的，并与 AES/GCM/NoPadding 密码结合使用以在将值存储在共享首选项文件中之前加密值。

**API 级别 22 及更高版本**

在较旧的 API 级别上，Android 密钥存储仅支持存储 RSA 密钥，这些密钥与 RSA/ECB/PKCS1Padding 密码结合使用以加密 AES 密钥（运行时随机生成），并且存储在密钥 SecureStorageKey 下的共享首选项文件中（如果尚未生成一个）。

SecureStorage 使用[首选项](preferences.md) API，并遵循[首选项](preferences.md#persistence)文档中所述的相同数据持久性。 如果设备从 API 级别 22 或更低级别升级到 API 级别 23 及更高版本，则将继续使用此类型的加密，除非卸载该应用或调用 RemoveAll。

# <a name="ios"></a>[iOS](#tab/ios)

[密钥链](xref:Security.SecKeyChain)用于在 iOS 设备上安全地存储值。  用于存储值的 `SecRecord` 具有设置为 [你的应用捆绑包 ID].xamarinessentials 的 `Service` 值。

在某些情况下，密钥链数据与 iCloud 同步，卸载应用程序可能不会从用户的 iCloud 和其他设备中删除安全值。

# <a name="uwp"></a>[UWP](#tab/uwp)

[DataProtectionProvider](/uwp/api/windows.security.cryptography.dataprotection.dataprotectionprovider) 用于在 UWP 设备上对值进行安全地加密。

加密值存储在名为 [你的应用 ID].xamarinessentials 的容器内的 `ApplicationData.Current.LocalSettings` 中。

SecureStorage 使用[首选项](preferences.md) API，并遵循[首选项](preferences.md#persistence)文档中所述的相同数据持久性。 它还使用 `LocalSettings`，存在以下限制：每个设置的名称长度最多可为 255 个字符。 每个设置的大小最多可为 8K 字节，每个复合设置的大小最多可为 64K 字节。

-----

## <a name="limitations"></a>限制

此 API 用于存储少量文本。  如果尝试将其用于存储大量文本，则可能会降低性能。

## <a name="api"></a>API

- [SecureStorage 源代码](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/SecureStorage)
- [SecureStorage API 文档](xref:Xamarin.Essentials.SecureStorage)

## <a name="related-video"></a>相关视频

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Secure-Storage-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]