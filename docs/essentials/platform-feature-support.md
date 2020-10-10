---
title: Xamarin.Essentials 平台与功能支持
description: Xamarin.Essentials 提供了适用于任何 iOS、Android 或 UWP 应用程序的单一跨平台 API，不管如何创建用户界面，都可以通过共享代码进行访问。
ms.assetid: 63FA28A5-6F52-4CB7-AF39-8DF7B436B5A4
author: jamesmontemagno
ms.author: jamont
ms.date: 08/20/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 96584aaa9bd92e838beffe3cbeaa2f86b975920f
ms.sourcegitcommit: 744f977b0595f489c592e29c8a3ba548fde02b6f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2020
ms.locfileid: "91410657"
---
# <a name="platform-support"></a>平台支持

Xamarin.Essentials 支持以下平台和操作系统：

| Platform | Version |
| --- | --- |
| Android | 4.4 (API 19) 或更高版本 |
| iOS |10.0 或更高版本 |
| Tizen | 4.0 或更高版本 |
| tvOS | 10.0 或更高版本 |
| watchOS | 4.0 或更高版本 |
| UWP | 10.0.16299.0 或更高版本 |
| macOS | 10.12.6 (Sierra) 或更高版本 |

> [!NOTE]
>
> * Tizen 受到 Samsung 开发团队的官方支持。
> * tvOS 和 watchOS 具有有限的 API 覆盖范围，请参阅功能指南获取详细信息。
> * macOS 支持处于预览阶段。

## <a name="feature-support"></a>功能支持

Xamarin.Essentials 总是试图为每个平台提供功能，但有时也会受到设备本身的限制。 下面是介绍每个平台支持哪些功能的指南。

图标指南：

* ![完全支持](~/media/shared/yes.png "完全支持") - 完全支持
* ![有限支持](~/media/shared/warn.png "有限支持") - 有限支持
* ![不支持](~/media/shared/no.png "不支持") - 不支持

| 功能 | Android | iOS | UWP | watchOS | tvOS | Tizen | macOS |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| [加速计](accelerometer.md?context=xamarin/xamarin-forms) | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![支持 watchOS](~/media/shared/yes.png "支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![支持 Tizen](~/media/shared/yes.png "支持 Tizen") | ![不支持 macOS](~/media/shared/no.png "不支持 macOS") |
| [应用操作](app-actions.md?context=xamarin/xamarin-forms) | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![不支持 watchOS](~/media/shared/no.png "不支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![不支持 Tizen](~/media/shared/no.png "不支持 Tizen") | ![不支持 macOS](~/media/shared/no.png "不支持 macOS") |
| [应用信息](app-information.md?context=xamarin/xamarin-forms) | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![支持 watchOS](~/media/shared/no.png "不支持 watchOS") | ![支持 tvOS](~/media/shared/yes.png "支持 tvOS") | ![支持 Tizen](~/media/shared/yes.png "支持 Tizen") | ![支持 macOS](~/media/shared/yes.png "支持 macOS") |
| [应用主题](app-theme.md?context=xamarin/xamarin-forms) | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![支持 watchOS](~/media/shared/yes.png "支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![支持 Tizen](~/media/shared/yes.png "支持 Tizen") | ![支持 macOS](~/media/shared/yes.png "支持 macOS") |
| [气压计](barometer.md?context=xamarin/xamarin-forms) | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![支持 watchOS](~/media/shared/yes.png "支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![支持 Tizen](~/media/shared/yes.png "支持 Tizen") | ![不支持 macOS](~/media/shared/no.png "不支持 macOS") |
| [电池](battery.md?context=xamarin/xamarin-forms) | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![有限支持 watchOS](~/media/shared/warn.png "有限支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![有限支持 Tizen](~/media/shared/warn.png "有限支持 Tizen") | ![支持 macOS](~/media/shared/yes.png "支持 macOS") |
| [剪贴板](clipboard.md?context=xamarin/xamarin-forms) | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![不支持 watchOS](~/media/shared/no.png "不支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![不支持 Tizen](~/media/shared/no.png "不支持 Tizen") | ![支持 macOS](~/media/shared/yes.png "支持 macOS") |
| [颜色转换器](color-converters.md?context=xamarin/xamarin-forms) | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![支持 watchOS](~/media/shared/yes.png "支持 watchOS") | ![支持 tvOS](~/media/shared/yes.png "支持 tvOS") | ![支持 Tizen](~/media/shared/yes.png "支持 Tizen") | ![支持 macOS](~/media/shared/yes.png "支持 macOS") |
| [指南针](compass.md?context=xamarin/xamarin-forms) | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![不支持 watchOS](~/media/shared/no.png "不支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![支持 Tizen](~/media/shared/yes.png "支持 Tizen") | ![不支持 macOS](~/media/shared/no.png "不支持 macOS") |
| [连接性](connectivity.md?context=xamarin/xamarin-forms) | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![不支持 watchOS](~/media/shared/no.png "不支持 watchOS") | ![支持 tvOS](~/media/shared/yes.png "支持 tvOS") | ![支持 Tizen](~/media/shared/yes.png "支持 Tizen") | ![支持 macOS](~/media/shared/yes.png "支持 macOS") |
| [联系人](contacts.md?context=xamarin/xamarin-forms) | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![不支持 watchOS](~/media/shared/no.png "不支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![支持 Tizen](~/media/shared/yes.png "支持 Tizen") | ![不支持 macOS](~/media/shared/no.png "不支持 macOS") |
| [检测抖动](detect-shake.md?context=xamarin/xamarin-forms) | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![支持 watchOS](~/media/shared/yes.png "支持 watchOS") | ![支持 tvOS](~/media/shared/yes.png "支持 tvOS") | ![支持 Tizen](~/media/shared/yes.png "支持 Tizen") | ![不支持 macOS](~/media/shared/no.png "不支持 macOS") |
| [设备显示信息](device-display.md?context=xamarin/xamarin-forms) | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![不支持 watchOS](~/media/shared/no.png "不支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![不支持 Tizen](~/media/shared/no.png "不支持 Tizen") | ![支持 macOS](~/media/shared/yes.png "支持 macOS") |
| [设备信息](device-information.md?context=xamarin/xamarin-forms) | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![支持 watchOS](~/media/shared/yes.png "支持 watchOS") | ![支持 tvOS](~/media/shared/yes.png "支持 tvOS") | ![支持 Tizen](~/media/shared/yes.png "支持 Tizen") | ![支持 macOS](~/media/shared/yes.png "支持 macOS") |
| [电子邮件](email.md?context=xamarin/xamarin-forms) | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![不支持 watchOS](~/media/shared/no.png "不支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![支持 Tizen](~/media/shared/yes.png "支持 Tizen") | ![支持 macOS](~/media/shared/yes.png "支持 macOS") |
| [文件选取器](file-picker.md?context=xamarin/xamarin-forms) | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![不支持 watchOS](~/media/shared/no.png "不支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![支持 Tizen](~/media/shared/yes.png "支持 Tizen") | ![支持 macOS](~/media/shared/yes.png "支持 macOS") |
| [文件系统帮助程序](file-system-helpers.md?context=xamarin/xamarin-forms) | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![支持 watchOS](~/media/shared/yes.png "支持 watchOS") | ![支持 tvOS](~/media/shared/yes.png "支持 tvOS") | ![支持 Tizen](~/media/shared/yes.png "支持 Tizen") | ![支持 macOS](~/media/shared/yes.png "支持 macOS") |
| [手电筒](flashlight.md?context=xamarin/xamarin-forms) | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![不支持 watchOS](~/media/shared/no.png "不支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![支持 Tizen](~/media/shared/yes.png "支持 Tizen") | ![不支持 macOS](~/media/shared/no.png "不支持 macOS") |
| [地理编码](geocoding.md?context=xamarin/xamarin-forms) | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![支持 watchOS](~/media/shared/yes.png "支持 watchOS") | ![支持 tvOS](~/media/shared/yes.png "支持 tvOS") | ![支持 Tizen](~/media/shared/yes.png "支持 Tizen") | ![支持 macOS](~/media/shared/yes.png "支持 macOS") |
| [地理位置](geolocation.md?context=xamarin/xamarin-forms) | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![不支持 watchOS](~/media/shared/no.png "不支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![支持 Tizen](~/media/shared/yes.png "支持 Tizen") | ![支持 macOS](~/media/shared/yes.png "支持 macOS") |
| [陀螺仪](gyroscope.md?context=xamarin/xamarin-forms) | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![支持 watchOS](~/media/shared/yes.png "支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![支持 Tizen](~/media/shared/yes.png "支持 Tizen") | ![不支持 macOS](~/media/shared/no.png "不支持 macOS") |
| [Haptic 反馈](haptic-feedback.md?context=xamarin/xamarin-forms) | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![不支持 watchOS](~/media/shared/no.png "不支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![支持 Tizen](~/media/shared/yes.png "支持 Tizen") | ![支持 macOS](~/media/shared/yes.png "支持 macOS") |
| [启动器](launcher.md?context=xamarin/xamarin-forms) | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![不支持 watchOS](~/media/shared/no.png "不支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![支持 Tizen](~/media/shared/yes.png "支持 Tizen") | ![支持 macOS](~/media/shared/yes.png "支持 macOS") |
| [磁力计](magnetometer.md?context=xamarin/xamarin-forms) | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![支持 watchOS](~/media/shared/yes.png "支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![支持 Tizen](~/media/shared/yes.png "支持 Tizen") | ![不支持 macOS](~/media/shared/no.png "不支持 macOS") |
| [MainThread](main-thread.md?content=xamarin/xamarin-forms) | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![支持 watchOS](~/media/shared/yes.png "支持 watchOS") | ![支持 tvOS](~/media/shared/yes.png "支持 tvOS") | ![支持 Tizen](~/media/shared/yes.png "支持 Tizen") | ![支持 macOS](~/media/shared/yes.png "支持 macOS") |
| [地图](maps.md?content=xamarin/xamarin-forms) | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![支持 watchOS](~/media/shared/yes.png "支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![支持 Tizen](~/media/shared/yes.png "支持 Tizen") | ![支持 macOS](~/media/shared/yes.png "支持 macOS") |
| [媒体选取器](media-picker.md?context=xamarin/xamarin-forms) | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![不支持 watchOS](~/media/shared/no.png "不支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![Tizen 支持](~/media/shared/yes.png "支持 Tizen") | ![支持 macOS](~/media/shared/yes.png "支持 macOS") |
| [打开浏览器](open-browser.md?context=xamarin/xamarin-forms) | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![不支持 watchOS](~/media/shared/no.png "不支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![支持 Tizen](~/media/shared/yes.png "支持 Tizen") | ![支持 macOS](~/media/shared/yes.png "支持 macOS") |
| [方向传感器](orientation-sensor.md?context=xamarin/xamarin-forms) | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![支持 watchOS](~/media/shared/yes.png "支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![支持 Tizen](~/media/shared/yes.png "支持 Tizen") | ![不支持 macOS](~/media/shared/no.png "不支持 macOS") |
| [权限](permissions.md?context=xamarin/xamarin-forms) | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![支持 watchOS](~/media/shared/yes.png "支持 watchOS") | ![支持 tvOS](~/media/shared/yes.png "支持 tvOS") | ![支持 Tizen](~/media/shared/yes.png "支持 Tizen") | ![支持 macOS](~/media/shared/yes.png "支持 macOS") |
| [电话拨号程序](phone-dialer.md?context=xamarin/xamarin-forms) | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![不支持 watchOS](~/media/shared/no.png "不支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![支持 Tizen](~/media/shared/yes.png "支持 Tizen") | ![支持 macOS](~/media/shared/yes.png "支持 macOS") |
| [平台扩展](platform-extensions.md?context=xamarin/xamarin-forms) | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![支持 watchOS](~/media/shared/yes.png "支持 watchOS") | ![支持 tvOS](~/media/shared/yes.png "支持 tvOS") | ![支持 Tizen](~/media/shared/yes.png "支持 Tizen") | ![支持 macOS](~/media/shared/yes.png "支持 macOS") |
| [首选项](preferences.md?context=xamarin/xamarin-forms) | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![支持 watchOS](~/media/shared/yes.png "支持 watchOS") | ![支持 tvOS](~/media/shared/yes.png "支持 tvOS") | ![支持 Tizen](~/media/shared/yes.png "支持 Tizen") | ![支持 macOS](~/media/shared/yes.png "支持 macOS") |
| [屏幕快照](screenshot.md?context=xamarin/xamarin-forms) | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![不支持 watchOS](~/media/shared/no.png "不支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![不支持 Tizen](~/media/shared/no.png "不支持 Tizen") | ![不支持 macOS](~/media/shared/no.png "不支持 macOS") |
| [安全存储](secure-storage.md?context=xamarin/xamarin-forms) | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![支持 watchOS](~/media/shared/yes.png "支持 watchOS") | ![支持 tvOS](~/media/shared/yes.png "支持 tvOS") | ![支持 Tizen](~/media/shared/yes.png "支持 Tizen") | ![支持 macOS](~/media/shared/yes.png "支持 macOS") |
| [共享](share.md?context=xamarin/xamarin-forms) | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![不支持 watchOS](~/media/shared/no.png "不支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![支持 Tizen](~/media/shared/yes.png "支持 Tizen") | ![支持 macOS](~/media/shared/yes.png "支持 macOS") |
| [SMS](sms.md?context=xamarin/xamarin-forms) | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![不支持 watchOS](~/media/shared/no.png "不支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![支持 Tizen](~/media/shared/yes.png "支持 Tizen") | ![支持 macOS](~/media/shared/yes.png "支持 macOS") |
| [文本到语音转换](text-to-speech.md?context=xamarin/xamarin-forms) | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![支持 watchOS](~/media/shared/yes.png "支持 watchOS") | ![支持 tvOS](~/media/shared/yes.png "支持 tvOS") | ![支持 Tizen](~/media/shared/yes.png "支持 Tizen") | ![支持 macOS](~/media/shared/yes.png "支持 macOS") |
| [单位转换器](unit-converters.md?context=xamarin/xamarin-forms) | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![支持 watchOS](~/media/shared/yes.png "支持 watchOS") | ![支持 tvOS](~/media/shared/yes.png "支持 tvOS") | ![支持 Tizen](~/media/shared/yes.png "支持 Tizen") | ![支持 macOS](~/media/shared/yes.png "支持 macOS") |
| [版本跟踪](version-tracking.md?context=xamarin/xamarin-forms) | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![支持 watchOS](~/media/shared/yes.png "支持 watchOS") | ![支持 tvOS](~/media/shared/yes.png "支持 tvOS") | ![支持 Tizen](~/media/shared/yes.png "支持 Tizen") | ![支持 macOS](~/media/shared/yes.png "支持 macOS") |
| [振动](vibrate.md?context=xamarin/xamarin-forms) | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![不支持 watchOS](~/media/shared/no.png "不支持 watchOS") | ![不支持 tvOS](~/media/shared/no.png "不支持 tvOS") | ![支持 Tizen](~/media/shared/yes.png "支持 Tizen") | ![不支持 macOS](~/media/shared/no.png "不支持 macOS") |
| [Web 验证器](web-authenticator.md?context=xamarin/xamarin-forms) | ![支持 Android](~/media/shared/yes.png "支持 Android") | ![支持 iOS](~/media/shared/yes.png "支持 iOS") | ![支持 UWP](~/media/shared/yes.png "支持 UWP") | ![不支持 watchOS](~/media/shared/no.png "不支持 watchOS") | ![支持 tvOS](~/media/shared/yes.png "支持 tvOS") | ![不支持 Tizen](~/media/shared/no.png "不支持 Tizen") | ![支持 macOS](~/media/shared/yes.png "支持 macOS") |
