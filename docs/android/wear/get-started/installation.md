---
title: '在 Xamarin 上安装和设置磨损操作系统 '
description: 本文逐步讲解为你的计算机和设备准备 Android 磨损开发所需的安装步骤和配置详细信息。 本文结束时，你将拥有一个可集成到 Visual Studio for Mac 和/或 Microsoft Visual Studio 中的 "应用的 Xamarin" 的应用程序，并准备好开始构建你的第一个 Xamarin 应用程序。
ms.prod: xamarin
ms.assetid: 3BB395FA-0545-4024-A18F-98CF5E9CA55F
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/25/2018
ms.openlocfilehash: 7c63f4c245d6fc5cb6e9da6320e159229b07de6e
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453670"
---
# <a name="install-and-setup-wear-os-on-xamarinandroid"></a>在 Xamarin 上安装和设置磨损操作系统

_本文逐步讲解为你的计算机和设备准备 Android 磨损开发所需的安装步骤和配置详细信息。本文结束时，你将拥有一个可集成到 Visual Studio for Mac 和/或 Microsoft Visual Studio 中的 "应用的 Xamarin" 的应用程序，并准备好开始构建你的第一个 Xamarin 应用程序。_

## <a name="requirements"></a>要求

若要创建基于 Xamarin 的 Android 应用，需要满足以下要求：

- **Visual Studio 或 Visual Studio for Mac** &ndash; 需要 Visual Studio 2017 社区或更高版本。

- **Xamarin.Android** &ndash; 必须安装和配置 Xamarin xamarin 4.17 或更高版本，并且必须安装 Visual Studio 或 Visual Studio for Mac。

- **Android SDK** Android SDK 5.0.1 (API 21) 或更高版本必须通过 Android SDK 管理器进行安装。

- **Java 开发人员工具包** &ndash; Xamarin Android 开发需要   [JDK 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 如果要针对 api 级别24或更高版本进行开发 (jdk 1.8 还支持早于 24) 的 API 级别。

如果要专门针对 API 级别23或更早版本进行开发，可以继续使用 [JDK 1.7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) 。

> [!IMPORTANT]
> Xamarin.Android 不支持 JDK 9。

## <a name="installation"></a>安装

安装 Xamarin 后，请执行以下步骤，以便能够生成和测试 Android 应用程序：

1. 安装所需的 Android SDK 和工具。
2. 配置测试设备。
3. 创建第一个 Android 应用。

将在下面各节中介绍这些步骤。

### <a name="install-android-sdk-and-tools"></a>安装 Android SDK 和工具

启动 **Android SDK 管理器**：

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![如何在 Visual Studio 中启动 Android SDK 管理器](installation-images/vs/sdk-menu.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

![如何在 Visual Studio for Mac 中启动 Android SDK 管理器](installation-images/xs/sdk-menu.png)

-----

确保安装了以下 Android SDK 和工具：

- Android SDK Tools v 24.0.0 或更高版本，以及
- Android 4.4 W (API20) 或
- Android 5.0.1 (API21) 或更高版本。

如果你没有安装最新的 SDK 和工具，请下载所需的 SDK 工具 *和* api 位 (你可能需要滚动一点来查找 &ndash;) 下面所示的 api 选择：

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![启用 Android 5.0.1 组件的示例 SDK 管理器屏幕截图](installation-images/vs/sdk-select.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

![启用 Android 4.4 和5.0.1 组件的示例 SDK 管理器的屏幕截图](installation-images/xs/sdk-select.png)

-----

## <a name="configuration"></a>配置

在可以使用测试应用之前，必须先配置 Android 磨损模拟器或实际的 Android 磨损设备。

### <a name="android-wear-emulator"></a>Android 损耗模拟器

使用 Android 损耗模拟器之前，必须使用 **Google 仿真器管理器** (AVD) 配置 android "飞机" 的 "android"。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![如何从 Visual Studio 启动 Android Emulator 管理器](installation-images/vs/emulator-menu.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

![如何从 Visual Studio for Mac 启动 Android Emulator 管理器](installation-images/xs/emulator-menu.png)

-----

有关设置 Android 损耗模拟器的详细信息，请参阅 [在模拟器上调试 Android 损耗](~/android/wear/deploy-test/debug-on-emulator.md)。

### <a name="android-wear-device"></a>Android 磨损设备

如果有 Android 磨损设备，如 Android 磨损 Smartwatch，则可以在此设备上调试应用，而不是使用模拟器。 有关使用磨损设备进行开发的信息，请参阅 [在磨损设备上调试](~/android/wear/deploy-test/debug-on-device.md)。

## <a name="create-your-first-android-wear-app"></a>创建第一个 Android 应用

请按照 [Hello、磨损](~/android/wear/get-started/hello-wear.md) 说明来构建你的第一个 watch 应用。

## <a name="packaging-your-app"></a>打包应用程序

Android 损耗应用程序始终与随附的 Android 手机应用一起分发。

当你添加 Android 应用程序作为对主 Android 应用程序的引用时，它将自动被假定为 Android 应用程序，并将为你生成所有必要的 XML 和元数据。 此外，它还将验证包和版本号是否匹配，以便您可以轻松地将应用程序交付到 Google Play。

若要详细了解打包应用程序，请参阅 [使用打包](~/android/wear/deploy-test/packaging.md)。

## <a name="related-links"></a>相关链接

- [SkeletonWear (示例) ](/samples/xamarin/monodroid-samples/wear-skeletonwear)