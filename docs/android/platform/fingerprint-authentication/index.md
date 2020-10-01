---
title: 指纹身份验证
description: 本指南讨论如何将 Android 6.0 中引入的指纹身份验证添加到 Xamarin.Android 应用程序。
ms.prod: xamarin
ms.assetid: 6742D874-4988-4516-A946-D5C714B20A10
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: a3fd03ece3618172e68539e2a92e8924e85e2ce5
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453878"
---
# <a name="fingerprint-authentication"></a>指纹身份验证

本指南讨论如何将 Android 6.0 中引入的指纹身份验证添加到 Xamarin.Android 应用程序。

## <a name="fingerprint-authentication-overview"></a>指纹身份验证概述

在 Android 设备上引入指纹扫描仪为应用程序提供了传统的用户名/密码用户身份验证的替代方法。 相较于用户名和密码，采用指纹对用户进行身份验证使应用程序安全性的实现更具隐私性。

FingerprintManager API 适用于带指纹扫描仪的设备，FingerprintManager API 运行的是 API 级别 23 (Android 6.0) 或更高版本。 API 位于 `Android.Hardware.Fingerprints` 命名空间中。 Android Support Library v4 提供适用于旧版 Android 的指纹 API 版本。 兼容性 API 位于 `Android.Support.v4.Hardware.Fingerprint` 名称空间，通过 [Xamarin.Android.Support.v4 NuGet 软件包](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)进行分发。

[FingerprintManager](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)（及其 Support Library 所对应的 [FingerprintManagerCompat](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)）是使用指纹扫描硬件的主要类。 此类是针对系统级服务（管理与硬件本身的交互）的 Android SDK 包装器。 它负责启动指纹扫描仪并响应扫描程序的反馈。 此类的接口较简单，只包含三个成员：

- `Authenticate` &ndash; 此方法将初始化硬件扫描仪并在后台启动服务，等待用户扫描其指纹。
- `EnrolledFingerprints` &ndash; 如果用户在设备中注册了一个或多个指纹，此属性将返回 `true`。
- `HardwareDetected` &ndash; 此属性用于确定设备是否支持指纹扫描。

Android 应用程序使用 `FingerprintManager.Authenticate` 方法启动指纹扫描仪。 以下代码片段举例说明如何使用 Support Library 兼容性 API 调用它：

```csharp
// context is any Android.Content.Context instance, typically the Activity 
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
fingerprintManager.Authenticate(FingerprintManager.CryptoObject crypto,
                                int flags,
                                CancellationSignal cancel,
                                FingerprintManagerCompat.AuthenticationCallback callback,
                                Handler handler
                               );
```

本指南将讨论如何使用 `FingerprintManager` API 为 Android 应用程序增加指纹身份验证功能。 它将介绍如何实例化和创建 `CryptoObject` 以帮助保护指纹扫描仪的结果。 我们将讨论应用程序应如何子类化 `FingerprintManager.AuthenticationCallback` 并响应指纹扫描仪的反馈。 最后，我们将了解如何在 Android 设备或模拟器上注册指纹，以及如何使用 adb 模拟指纹扫描。

## <a name="requirements"></a>要求

指纹身份验证需要 Android 6.0（API 级别 23）或更高版本，以及带有指纹扫描器的设备。 

已在设备上注册了指纹的用户才能进行身份验证。 这涉及设置使用密码、PIN、轻扫模式或面部识别的屏幕锁。 可以在 Android 仿真器中模拟某些指纹身份验证功能。  有关这两个主题的更多信息，请参阅[注册指纹](enrolling-fingerprint.md)部分。 

## <a name="related-links"></a>相关链接

- [指纹指南示例应用](/samples/xamarin/monodroid-samples/fingerprintguide)
- [指纹对话示例](/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)
- [在运行时请求权限](https://developer.android.com/training/permissions/requesting.html)
- [android.hardware.fingerprint](https://developer.android.com/reference/android/hardware/fingerprint/package-summary.html)
- [android.support.v4.hardware.fingerprint](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/package-summary.html)
- [Android.Content.Context](xref:Android.Content.Context)
- [指纹和支付 API（视频）](https://youtu.be/VOn7VrTRlA4)