---
title: 其他 watchOS 3 框架更改
description: 本文档介绍了 watchOS 3 引入的各种框架更改，以及如何在 Xamarin 中使用它们。 讨论了核心数据、核心动作、Foundation、HealthKit、HomeKit、PassKit 和 UIKit。
ms.prod: xamarin
ms.assetid: FE93796E-F699-4B14-B37D-D39F9D48E81E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 50f671febdc698454dd5bc7e00d040672c7a79af
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436525"
---
# <a name="additional-watchos-3-frameworks-changes"></a>其他 watchOS 3 框架更改

_本文介绍了 watchOS 3 的现有框架的附加、小更改或增强功能。_

除了对 iOS 的重大更改之外，Apple 还在 watchOS 3 中对多个现有框架进行了修改和改进。

## <a name="core-data"></a>核心数据

以下对用于监视操作系统3的核心数据框架进行了以下改进：

- 根 [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) 对象支持并发错误和不序列化的提取。
- [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator)类维护 SQLite 数据存储的池。
- 在 WAL 日志模式下，具有 SQLite 数据存储的 [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) 对象支持新的查询生成功能，在该功能中，托管对象上下文 (MOC) 可以固定到特定数据库版本，以供将来获取和出错事务。
- 使用高级 `NSPersistenceContainer` 引用 `NSPersistentStoreCoordinator` 、 [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel) 和其他核心数据配置资源。
- 添加了几个新的便利方法，以便 `NSManagedObject` 更轻松地执行提取和创建子类。

有关详细信息，请参阅 Apple 的 [核心数据框架参考](https://developer.apple.com/reference/coredata)。

## <a name="core-motion"></a>核心动作

针对观看 OS 3 的核心运动框架进行了以下改进：

- 新的设备运动事件使用加速感应和陀螺仪来提供运动和方向更新。 该应用可以注册此更新 (最多 100Hz) 。
- 当用户暂停和继续运行时，新的 Pedometer 事件可实现快速实时通知。 使用 [CMPedometer](https://developer.apple.com/reference/coremotion/cmpedometer) 注册前台或后台 pedometer 事件。

## <a name="foundation"></a>Foundation

已对监视 OS 3 的基础框架进行了以下改进：

- 使用新的 [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) 类可以进行日期和时间间隔计算，如持续时间，用于比较间隔和测试间隔交点。
- 向 [NSLocal](https://developer.apple.com/reference/foundation/nslocale) 类添加了几个新属性，以获取本地信息和可用的显示格式。
- 使用新的 [NSMeasurement](https://developer.apple.com/reference/foundation/nsmeasurement) 类在不同的度量单位 (UOM) 之间进行转换，或对不同 UOMs 中的值执行计算。
- 使用新的 [NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter) 类来设置要向最终用户显示的本地化度量值的格式。
- 使用新的 [NSUnit](https://developer.apple.com/reference/foundation/nsunit) 和 [NSDimension](https://developer.apple.com/reference/foundation/nsdimension) 类来表示特定的 UOMs。

## <a name="healthkit"></a>HealthKit

HealthKit 框架对监视操作系统3进行了以下改进：

- 使用新的 [HKWorkoutConfiguration](https://developer.apple.com/reference/healthkit/hkworkoutconfiguration) 类指定健身的 `ActivityType` 和 `LocationType` 。
- 添加了新的 [HKWheelchairUseObject](https://developer.apple.com/reference/healthkit/hkwheelchairuseobject) 和 `WheelchairUse` [HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore) 类的方法，以便使用轮椅相关的运行状况数据。
- 添加了新的元数据密钥，以用于天气类型 (例如 `HKWeatherConditionClear` 和 `HKWeatherConditionCloudy`) 和健身类型 (如 `HKWorkoutActivityTypeFlexibility` 和 `HKWorkoutActivityTypeWheelchairRunPace`) 。

## <a name="homekit"></a>HomeKit

HomeKit 框架对监视操作系统3进行了以下改进：

- 添加了查看和与 HomeKit 连接的 IP 照相机的交互功能。
- 添加了几个新的服务和特性。
- 添加了更多上下文和配置主要服务和链接服务的附件。

## <a name="passkit"></a>PassKit

PassKit 框架对监视操作系统3进行了以下改进：

- 扩展框架，以支持对物理商品和服务的 Apple Watch 进行安全的应用内付款。
- 现在提供以下类： [PKPayment](https://developer.apple.com/reference/passkit/pkpayment)、 [PKPaymentMethod](https://developer.apple.com/reference/passkit/pkpaymentmethod)、 [PKPaymentRequest](https://developer.apple.com/reference/passkit/pkpaymentrequest) 和 [PKPaymentToken](https://developer.apple.com/reference/passkit/pkpaymenttoken)

## <a name="uikit"></a>UIKit

UIKit 框架对监视操作系统3进行了以下改进：

- 为了支持标签中的动态类型，文本字段和文本框使用类的新 `PreferredFontForTextStyle` 方法 `UIFont` 。
- `ColorWithDisplayP3`添加了方法以支持宽色。

## <a name="related-links"></a>相关链接

- [watchOS 示例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2bwatchos)
- [WatchOS 3 中的新增功能](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)