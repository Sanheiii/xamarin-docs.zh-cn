---
title: 在 Windows 上调试 Android 需要哪些 USB 驱动程序？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 36EC7341-A2A4-409C-BD4F-330BAC505123
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/22/2018
ms.openlocfilehash: c07f9099f3b76ed86a235883ce335ce19a426b99
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457882"
---
# <a name="what-usb-drivers-do-i-need-to-debug-android-on-windows"></a>在 Windows 上调试 Android 需要哪些 USB 驱动程序？

## <a name="finding-usb-drivers"></a>查找 USB 驱动程序

在 Windows 中进行开发时，若要在 Android 设备上进行调试，需要安装兼容的 USB 驱动程序。 默认情况下，Android SDK 管理器包含“Google USB 驱动程序”，这样就可以支持 Nexus 设备，如下所述：[https://developer.android.com/sdk/win-usb.html](https://developer.android.com/sdk/win-usb.html)

其他设备需要使用设备制造商专门发布的 USB 驱动程序。 本指南中包含一些最常见制造商的链接：[https://developer.android.com/tools/extras/oem-usb.html](https://developer.android.com/tools/extras/oem-usb.html)

## <a name="alternatives"></a>备选项

根据制造商的不同，可能很难找到所需的确切 USB 驱动程序。 测试在 Windows 中开发的 Android 应用的一些替代方法，包括使用 Android 仿真器或使用外部测试服务。 其中包括：

- [App Center 测试](/appcenter/test-cloud/) - 云测试服务可在数百种实际 Android 设备上运行。

- [适用于 Android 的 Visual Studio 模拟器](https://visualstudio.microsoft.com/vs/msft-android-emulator/)

- [在 Android Emulator 上调试](~/android/deploy-test/debugging/debug-on-emulator.md)