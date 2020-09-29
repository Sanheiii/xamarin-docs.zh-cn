---
title: 在 Xamarin 中使用用户默认值
description: 本文介绍如何使用使用 nsuserdefaults 在 Xamarin iOS 应用或扩展中保存默认设置。 它从较高层面介绍了使用 nsuserdefaults，并讨论了如何读取和写入值。
ms.prod: xamarin
ms.assetid: DAE7FFC4-B8C9-4D9E-886A-9B2388452EEB
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/07/2016
ms.openlocfilehash: 587ce48f3d40bb4f16634409920b01d150d628ef
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436312"
---
# <a name="working-with-user-defaults-in-xamarinios"></a>在 Xamarin 中使用用户默认值

_本文介绍如何使用 NSUserDefault 在 Xamarin iOS 应用或扩展中保存默认设置。_

`NSUserDefaults`类为 IOS 应用和扩展提供了一种以编程方式与系统范围的默认系统交互的方式。 通过使用默认系统，用户可以根据应用) 的设计，配置应用的行为或样式以满足其首选项 (。 例如，若要以指标与英制度量值显示数据，或选择给定的 UI 主题。

与应用组一起使用时， `NSUserDefaults` 还提供了一种方法，用于在给定组内的应用 (或扩展) 之间进行通信。

<a name="About-User-Defaults"></a>

## <a name="about-user-defaults"></a>关于用户默认值

如上所述，可以将 () 的用户默认值 `NSUserDefaults` 添加到应用 (或扩展) 并用于提供可配置选项，最终用户可以修改这些选项以在运行时调整应用的外观或操作。

当你的应用程序第一次执行时，将 `NSUserDefaults` 从应用的用户默认数据库中读取密钥和值，并将它们缓存到内存中，以避免每次需要值时打开和读取数据库。 

> [!IMPORTANT]
> Apple 不再建议开发人员调用 `Synchronize` 方法，将内存中缓存直接与数据库同步。 相反，它会定期自动调用，以使内存中缓存与用户的默认数据库保持同步。

`NSUserDefaults`类包含用于读取和写入常见数据类型（例如： string、integer、float、boolean 和 url）的首选项值的便捷方法。 其他类型的数据可以使用进行存档 `NSData` ，然后读取或写入用户的默认数据库。 有关详细信息，请参阅 Apple 的 [首选项和设置编程指南](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/UserDefaults/Introduction/Introduction.html#//apple_ref/doc/uid/10000059i)。

<a name="Accessing-the-Shared-NSUserDefaults-Instance"></a>

## <a name="accessing-the-shared-nsuserdefaults-instance"></a>访问共享的使用 nsuserdefaults 实例 

共享用户默认实例提供对设备当前用户的用户默认设置的访问权限。 如果共享的默认对象不存在，则会在第一次访问该对象并将其初始化时，为其创建以下信息：

- 由 `NSArgumentDomain` 从当前应用分析的默认值组成的。
- 应用的捆绑标识符域。
- `NSGlobalDomain`由所有应用共享的默认值组成的。
- 为每个用户首选语言单独的域。
- `NSRegistrationDomain`具有一组临时默认值的，应用程序可以对其进行修改，以确保搜索始终成功。

若要访问共享用户默认实例，请使用以下代码：

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
```

<a name="Accessing-an-App-Group-NSUserDefaults-Instance"></a>

## <a name="accessing-an-app-group-nsuserdefaults-instance"></a>访问应用组使用 nsuserdefaults 实例

如上所述，通过使用应用组， `NSUserDefaults` 可用于在特定组内的应用 (或扩展) 之间进行通信。 首先，需要确保已在[IOS 开发人员中心](https://developer.apple.com/devcenter/ios/)的**证书、标识符 & 配置文件**部分中正确配置了应用程序组和所需的应用 id，并且已在开发环境中安装。

接下来，你的应用和/或扩展项目需要具有上面创建的有效应用 Id 之一，并且该 `Entitlements.plist` 文件必须包含在启用并指定了应用组的应用捆绑包中。

通过这一切，可以使用以下代码访问共享应用组用户默认值：

```csharp
// Get App Group User Defaults
var plist = new NSUserDefaults ("group.com.xamarin.todaysharing", NSUserDefaultsType.SuiteName);
```

其中， `group.com.xamarin.todaysharing` 是在 **"证书"** 中创建的应用组，标识符 & 要访问的配置文件。 有关详细信息，请参阅 [应用组功能](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) 文档。

<a name="Reading-Default-Values"></a>

## <a name="reading-default-values"></a>读取默认值

在访问了所需的用户默认数据库后，可以使用键/值对从默认值中读取值，并根据所读取的数据类型使用几种便捷方法：

- `ArrayForKey` -返回给定键值的的数组 `NSObjects` 。
- `BoolForKey` -返回给定键的布尔值。
- `DataForKey` -返回 `NSData` 给定键的对象。
- `DictionaryForKey` -返回 `NSDictionary` 给定键的。
- `DoubleForKey` -返回给定键的双精度值。
- `FloatForKey` -返回给定键的 float 值。
- `IntForKey` -返回给定键的整数值。
- `StringArrayForKey` - `String` 从给定键值返回对象的数组。
- `StringForKey` -返回给定键的字符串值。
- `URLForKey` -返回 `NSUrl` 给定键的值。

例如，以下代码将从用户默认值读取一个布尔值：

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
...

// Get value
var useHeader = plist.BoolForKey("UseHeader");

```

<a name="Writing-Default-Values"></a>

## <a name="writing-default-values"></a>写入默认值

例如，在访问了所需的用户默认数据库之后，可以使用键/值对将值写入默认值，并根据要写入的数据类型，使用几种便捷方法，将值写入默认值：

- `SetBool` -将给定的布尔值写入给定的键。
- `SetDouble` -将给定的双精度值写入给定的键。
- `SetFloat` -将给定的 float 值写入给定的键。
- `SetString` -将给定的字符串值写入给定的键。
- `SetURL` -将给定的 URL (`NSUrl`) 值写入给定的键。

例如，下面的代码会将一个布尔值写入用户默认值：

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
...

// Save value
plist.SetBool(useHeader, "UseHeader");
...

```

> [!IMPORTANT]
> 当你的应用程序第一次执行时，将 `NSUserDefaults` 从应用的用户默认数据库中读取密钥和值，并将它们缓存到内存中，以避免每次需要值时打开和读取数据库。

<a name="Summary"></a>

## <a name="summary"></a>总结

本文介绍了 `NSUserDefaults` 类，以及如何使用它提供一组可供最终用户用来配置 Xamarin IOS 应用的选项。 此外，它还介绍了如何使用应用组在扩展及其父应用之间或组中的应用之间进行通信。

## <a name="related-links"></a>相关链接

- [tvOS 示例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [首选项和设置编程指南](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/UserDefaults/Introduction/Introduction.html#//apple_ref/doc/uid/10000059i)
- [使用 nsuserdefaults](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/Foundation/Classes/NSUserDefaults_Class/#//apple_ref/doc/constant_group/NSUserDefaults_Domains)